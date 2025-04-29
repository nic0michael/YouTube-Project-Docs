---
title: "Usefull Centos Commands"
date: 2023-09-19T11:23:21Z
draft: false
---
## 1. creating a user
```
# create the user nickm
sudo adduser nickm
# this is better than useradd

# to delete user
userdel -r nickm

# Set the password
passwd nickm
```

## 2. making a user a Sudoer
```
sudo cp /etc/sudoers /root/sudoers.bak

sudo visudo
```
Put this in the file
```
nickm ALL =(ALL) NOPASSWD: ALL

josh ALL =(ALL) NOPASSWD: ALL

ansible ALL =(ALL) NOPASSWD: ALL

%sysadm ALL=(ALL) NOPASSWD: ALL
```
## 3. Fixing issue :  Failed to set locale, defaulting to C.UTF-8
```
dnf install -y glibc-all-langpacks langpacks-en

dnf install -y glibc-langpack-en
```

## 4. Open Ports in firewall
```
sudo su -

firewall-cmd --zone=public --permanent --add-port=5000/tcp

firewall-cmd --permanent --add-port={10248/tcp,10250/tcp,10252/tcp,10255/tcp,2379/tcp,2380/tcp,6443/tcp}

firewall-cmd --reload

firewall-cmd --list-ports

```
## 5. Kernel panic creating Rocky9 VM
You need to make CPU type -> Host

## 6. Adding / Changing DNS server
```
nano /etc/resolv.conf
# or 
vi /etc/resolv.conf

```
Put this in the file:
```
nameserver 8.8.8.8
```

## 7. How to set a static IP in Rocky Linux
**[How to set a static IP in Rocky Linux](https://www.techrepublic.com/article/set-ip-rocky-linux/)**

run nmtui
```
nmtui
```

```
Select -> Edit a connection     -> Press Enter

Select -> (name) ens18          -> Press Enter

Select -> IPV4 CONFIGURATION    -> Press Enter
Select -> Addresses             -> Press Enter type IP Address
Select -> Gateway               -> Press Enter type Gateway IP Address
Select -> DNS Servers           -> Press Enter type 8.8.8.8 
and underneath that 8.8.4.4

Select -> Search domains        -> Press Enter type 8.8.8.8
Select -> OK                    -> Press Enter
Select -> Back

Select -> Activate a connection -> Press Enter
Select -> Back

Select -> Quit
```

## 8. Ansible and K3S Kubernetes requirements
### 8.1 Set static IP address
**See nmtui command in previous chapter**

### 8.2 Ansible requirtements
```
sudo su -

sudo adduser ansible
sudo adduser nickm

visudo 

```
Add this to file:
```
ansible ALL =(ALL) NOPASSWD: ALL
nickm ALL =(ALL) NOPASSWD: ALL
```

```
ssh-keygen -t rsa -b 4096

ssh-copy-id nickm@10.154.2.80
ssh-copy-id nickm@10.154.2.81
ssh-copy-id nickm@10.154.2.83
```

```
sudo su - ansible

ssh-keygen -t rsa -b 4096

ssh-copy-id ansible@10.154.2.95

ssh-copy-id ansible@10.154.2.80
ssh-copy-id ansible@10.154.2.81
ssh-copy-id ansible@10.154.2.83

```

### 8.3 Kubernetes K3S requirements
```
sudo su -

# systemctl stop firewalld
sudo systemctl stop firewalld.service

sudo systemctl disable firewalld.service

sudo systemctl status firewalld.service



systemctl status firewalld.service

firewall-cmd --state
```

Expect to get:
```
not running
```

```
ssh-keygen -t rsa -b 4096

ssh-copy-id root@10.154.2.80
ssh-copy-id root@10.154.2.81
ssh-copy-id root@10.154.2.83
```

## 8.4 If you replace VMs in your cluster with new one with same IP addresses
Run this for the affected user in the ansible server
```
nano ~/.ssh/known_hosts
# clear this file

ssh-keyscan 10.154.2.80 >> ~/.ssh/known_hosts
ssh-keyscan 10.154.2.81 >> ~/.ssh/known_hosts
ssh-keyscan 10.154.2.83 >> ~/.ssh/known_hosts


```
## 9. Set up Passwordless login
```
ssh-keygen -t rsa -b 4096

ssh-copy-id root@10.154.2.93
# or
ssh-copy-id nickm@10.154.2.93

ssh root@10.154.2.93
```

### 9.1 disable login by password
```
sudo nano /etc/ssh/ssh_config

# set the following ermitRootLogin to no
PermitRootLogin no


# set PasswordAuthentication to no
PasswordAuthentication no

sudo systemctl restart sshd

## WARNING !!!!!
# You now need to login from another terminal to ensure you have not locked yourself out 

```

## 10. Implement Fail2Ban

Fail2Ban is not installed by default on Rocky Linux, so the first step is to install it:
```
sudo dnf install epel-release -y

sudo dnf install fail2ban -y

sudo dnf install nano -y
```
Check status of fail2ban service
```
systemctl status fail2ban.service

# DO NOT START THE SERVICE
```
The fail2ban service keeps its configuration files in the /etc/fail2ban directory. There is a file with defaults called jail.conf. Go to that directory and print the first 20 lines of that file using head -20:
```
cd /etc/fail2ban
head -20 jail.conf
```

Expect to get:
```
#
# WARNING: heavily refactored in 0.9.0 release.  Please review and
#          customize settings for your setup.
#
# Changes:  in most of the cases you should not modify this
#           file, but provide customizations in jail.local file,
#           or separate .conf files under jail.d/ directory, e.g.:
#
# HOW TO ACTIVATE JAILS:
#
# YOU SHOULD NOT MODIFY THIS FILE.
#
# It will probably be overwritten or improved in a distribution update.
#
# Provide customizations in a jail.local file or a jail.d/customisation.local.
# For example to change the default bantime for all jails and to enable the
# ssh-iptables jail the following (uncommented) would appear in the .local file.
# See man 5 jail.conf for details.
#
# [DEFAULT]
```

Create jail.local. You can do that by copying jail.conf:
```
sudo cp jail.conf jail.local

# verify the file is there
ls -la
```

Now you can begin making configuration changes. Open the file in vi or your favorite text editor:
```
#sudo vi jail.local
sudo nano jail.local
```

**For a max of 3 retries and 5minute ban time** \
**Under [sshd] THAT IS NOT COMMENTED OUT ADD THE FOLOWING**
```
[sshd]

# To use more aggressive sshd modes set filter parameter "mode" in jai>
# normal (default), ddos, extra or aggressive (combines all).
# See "tests/files/logs/sshd" or "filter.d/sshd.conf" for usage exampl>
#mode   = normal

# You need to set all these values but do not repeat any :
enabled = true
port    = ssh
logpath = %(sshd_log)s
backend = %(sshd_backend)s
maxretry = 3
findtime = 420  # 7 minutes in seconds
bantime = 300  # 5 minutes in seconds
```

When you are finished editing, save and close the file. If you are using vi, use :x to save and quit. At this point, you can enable your Fail2ban service so that it will run automatically from now on. First, run systemctl enable:
```
systemctl start fail2ban.service

systemctl enable fail2ban.service
```
Test this out
```
ssh nickm@10.154.2.47
nickm@10.154.2.47's password: 
Permission denied, please try again.
nickm@10.154.2.47's password: 
Permission denied, please try again.
nickm@10.154.2.47's password: 
nickm@10.154.2.47: Permission denied (publickey,gssapi-keyex,gssapi-with-mic,password).

nickm@dell-laptop:~$ ssh nickm@10.154.2.47
# Expect to get
ssh: connect to host 10.154.2.47 port 22: Connection refused
```

## install SSH on Centos
Run these commands
```
sudo dnf upgrade --refresh

# Check if OpenSSH server is already on your Rocky Linux system. This can be ascertained by executing the command:
rpm -qa | grep openssh-server

# Install OpenSSH
sudo dnf install openssh-server -y

# check status of ssh deamon
sudo systemctl status sshd

# enable ssh
sudo systemctl enable sshd

# start ssh
sudo systemctl start sshd

```


## Bibliography
https://www.digitalocean.com/community/tutorials/how-to-protect-ssh-with-fail2ban-on-rocky-linux-8
