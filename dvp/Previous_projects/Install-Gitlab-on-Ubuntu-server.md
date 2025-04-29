---
title: "Install Gitlab on Ubuntu Server"
date: 2023-03-08T13:43:40Z
draft: false
---

## Ubuntu Set Static IP address
Find the file : 1-network-manager-all.yaml
```
sudo su -
apt install nano
ls -la /etc/netplan/
# Expect
-rw-r--r--   1 root root   104 Feb  8  2018 1-network-manager-all.yaml

```

```
sudo nano /etc/netplan/1-network-manager-all.yaml
```
Comment out the top part of the file use #
put this in the bottom of the file

change the network device

```
network:
  version: 2
  renderer: networkd
  ethernets:
    ens3:
      dhcp4: no
      addresses:
        - 192.168.121.221/24
        - 10.154.2.93/24
      gateway4: 10.154.2.3
      nameservers:
          addresses: [8.8.8.8, 1.1.1.1]
```
revised yaml file
```
network:
  version: 2
  renderer: networkd
  ethernets:
    eno1:
      dhcp4: false
      dhcp6: false
      addresses:
       - 10.154.2.90/24
      routes:
       - to: default
         via: 10.154.2.3
      nameservers:
        addresses: [8.8.8.8,8.8.4.4]
```

Now run this command:
```
sudo netplan apply
```
Show the Ip of the device ens3
```
ip addr show dev ens3
```

## Set up Passwordless login
```
ssh-copy-id nickm@10.154.2.90
```


## installing the community edition of gitlab
The community edition of gitlab can be found here \
https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh
```
sudo su -
apt update
sudo apt install -y curl openssh-server ca-certificates
```
## Install Postfix to send notification emails. 
```
sudo apt-get install -y postfix
```
## Install the repository
```
#curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
curl -s https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
```


## Install Gitlab
```
sudo EXTERNAL_URL="http://giraffe.loseyourip.com" apt-get install gitlab-ce
#sudo apt install gitlab-ce
```
## save the Gitlabdefault user password
```

```

## Configuring Gitlab to use IQDN giraffe.loseyourip.com
```
sudo nano /etc/gitlab/gitlab.rb
```

# Initial change external_url
external_url 'http://giraffe.loseyourip.com'

## edit /etc/gitlab/gitlab.rb (create it if it doesn’t exist) and set:
```
nano /etc/gitlab/gitlab.rb

# now set initial password
gitlab_rails['initial_root_password'] = '<my_strong_password>'
```


# We need to reconfigure Gitlab every time we modify the Configuration  file
Once you have the package installed, you can run the provided configuration utility. \
It provides an automatic configuration. \
You can modify things later if you need to.

```
sudo gitlab-ctl reconfigure
gitlab-ctl start
```

## now we need the initial root password before it is automatically deleted in 24 Hours
```
sudo cat /etc/gitlab/initial_root_password
```
expect to get
```
Password: 5E2+YflWYcPID3fLAQuL0TnS+TjWpTuDIInK4lOSY+w=
```

## Open Gitlab in the browser
http://giraffe.loseyourip.com \
Login as root and use the above password

For a private server you may want to deactivate signouts [click the button] \





## Bibliography

https://www.youtube.com/watch?v=jn8o9nTABWM&t=2s

