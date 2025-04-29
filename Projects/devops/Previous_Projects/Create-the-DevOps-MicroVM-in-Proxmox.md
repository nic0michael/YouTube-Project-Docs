---
title: "Create the DevOps MicroVM in Proxmox"
date: 2024-03-21T07:23:42Z
draft: false
---

## 1. Summary
We are going to create a MicroVM in Proxmox and install the following DevOps tools:
1. Ansible
2. Ansible Semaphore
3. GitLab

With this you have a good start to aquire DevOps skills from you homelab. \
Having a Homelab opens the door for you to aquire DevOps skills and experience. \
This is much in demand by the IT Industry

## 2. Procedures
### 2.1 Ansible Installation
We use Ansible to automate tasks on our servers

We are using Dynu DNS to register Domain Names for these IP Address :
```
buffalo.loseyourip.com 10.154.2.85

kudu.loseyourip.com    10.154.2.90
leopard.loseyourip.com 10.154.2.91
lion.loseyourip.com    10.154.2.92
```

## 2.1.1 Install Ansible on your server
Run these commands
```
sudo su -

sudo apt update

sudo apt install nano curl -y

sudo apt upgrade -y

sudo apt install software-properties-common -y

sudo add-apt-repository --yes --update ppa:ansible/ansible

sudo apt update

sudo apt install -y ansible


sudo adduser ansible

usermod -aG sudo ansible

```

## 2.1.2 Setup User on Remote Hosts to work with Ansible
Run these commands on remote Hosts
```
sudo su -

sudo addgroup ansible

sudo adduser ansible

usermod -aG sudo ansible

sudo addgroup ansible ansible

ssh-keygen -t rsa -b 4096

ssh-copy-id ansible@10.154.2.86

```

## 2.1.3 Connect ansible user on Ansible server to remote hosts
```
sudo su -

adduser ansible

usermod -aG sudo ansible

sudo su - ansible

#Become ansible
sudo su - ansible

ssh-keygen -t rsa -b 4096

ssh-copy-id ansible@10.154.2.90
ssh-copy-id ansible@10.154.2.91
ssh-copy-id ansible@10.154.2.92

# Become root again
exit

```
## 2.1.4 Add Managed Nodes to Ansible Inventory
```
sudo su -

sudo mkdir /etc/ansible

sudo nano /etc/ansible/hosts
# OR
sudo vi /etc/ansible/hosts

```
Put this in the file:
```
# This is the default ansible 'hosts' file.

[primary_master_node]
hippo.kozow.com ansible_host=10.154.2.90

[other_active_nodes]
giraffe.kozow.com ansible_host=10.154.2.91
zebra.kozow.com ansible_host=10.154.2.92

[all_active_nodes]
hippo.kozow.com ansible_host=10.154.2.90
giraffe.kozow.com ansible_host=10.154.2.91
zebra.kozow.com ansible_host=10.154.2.92
```

## 2.1.5 Verify Ansible Inventory
```
ansible-inventory --list -y
```

Expect to get:
```
all:
  children:
    all_active_nodes:
      hosts:
        giraffe.kozow.com: {}
        hippo.kozow.com: {}
        zebra.kozow.com: {}
    other_active_nodes:
      hosts:
        giraffe.kozow.com:
          ansible_host: 10.154.2.91
        zebra.kozow.com:
          ansible_host: 10.154.2.92
    primary_master_node:
      hosts:
        hippo.kozow.com:
          ansible_host: 10.154.2.90
    ungrouped: {}
```


### 2.2 Semaphore Installation
We use Semaphore to manage and control Ansible from the Browser

## 2.2.1 Semaphore Installation Documentation
Ansible Semaphore: Administration-guide: Installation

## 2.2.2 Install MariaDB
Run these commands
```
# Update Ubuntu
sudo apt update

# Add MariaDB Repo
curl -LsS https://downloads.mariadb.com/MariaDB/mariadb_repo_setup | sudo bash -s --

# Once the repository is added install server and client packages
sudo apt install mariadb-server mariadb-client


## 2.2.3 Secure the MariaDB database
sudo mysql_secure_installation
```
use these settings:
```
Switch to unix_socket authentication [Y/n] n
Change the root password? [Y/n] y
Remove anonymous users? [Y/n] y
Disallow root login remotely? [Y/n] y
Remove test database and access to it? [Y/n] y
Reload privilege tables now? [Y/n] y
```

## if you need a Non-Root user
run these commands
```
## 2.2.4 Connect to database
sudo mariadb
# you could also have run this command:
mysql -u root -p

# Create User 
GRANT ALL ON *.* TO 'admin'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;

# Save user priviledges
FLUSH PRIVILEGES;

# Leave the database
exit
```


## 2.2.5 Start Enable and Test the database
Run these commands
```
# Start the database
sudo systemctl start mariadb

# Enable the database
sudo systemctl enable mariadb

# Check Status
sudo systemctl status mariadb
```

## 2.2.6 Install Semaphore Prerequisites
Run these commands
```
sudo apt update
sudo apt install git curl wget software-properties-common nano -y

## 2.2.7 Verify git
git version
```

## 2.2.8 Visit the Semaphore Releases page and copy the download link for your OS. : 
[https://github.com/ansible-semaphore/semaphore/releases](https://github.com/ansible-semaphore/semaphore/releases)

v2.9.45 is stable

https://github.com/ansible-semaphore/semaphore/releases/download/v2.9.45/semaphore_2.9.45_linux_amd64.deb

Run these commands:
```
VER=$(curl -s https://api.github.com/repos/ansible-semaphore/semaphore/releases/latest|grep tag_name | cut -d ‘"’ -f 4|sed ’s/v//g’)

wget https://github.com/ansible-semaphore/semaphore/releases/download/v${VER}/semaphore_${VER}_linux_amd64.deb

# if the above does not work try this
wget https://github.com/ansible-semaphore/semaphore/releases/download/v2.9.45/semaphore_2.9.45_linux_amd64.deb

sudo dpkg -i semaphore_2.9.45_linux_amd64.deb
```

## 2.2.9 Verify Semaphore
Run these commands:
```
# Check if you have Semaphore in your $PATH
echo $PATH

# Find installation path
which semaphore

# Check semaphore
semaphore version
```

## Setup Semaphore you can re-run the semaphore setup command if you did something wrong
Run these commands
```
# Setup semaphore
sudo semaphore setup

# Make these choices
1. Choose MySQL : 1
2. confirm setup is correct : yes
3. Username: admin
4. Email: admin@leopard.loseyourip.com
5. Your name: Admin user : admin@localhost
6. Password: StrongPassword
```
**It is important that you remember the Wsername and Password you will use this to login to the Web UI**

## Show/Change logon user 

```
# list semaphore users
semaphore user list

# Set user password
semaphore user change-by-login --login user123 --password FiGsV1yyaX

# create a new logon user
semaphore user add --admin --login user123 --name User123 --email user123@example.com --password l3tHTXGwpz
```

## 2.2.10 Now you can run Semaphore:
```
# semaphore service –config=./config.json
semaphore server --config /root/config.json
```
Semaphore will be available via this URL : \
[http://localhost:3000](http://localhost:3000)

[http://10.154.2.85:3000](http://10.154.2.85:3000)

**[http://buffalo.loseyourip.com:3000](http://buffalo.loseyourip.com:3000)**

### 2.3 GitLab installation
We use GitLab to save files,programs, and Ansible Playbooks under version control.

## 2.3.1 Install the prerquisites
Run These commands
```
sudo su -

sudo apt update

# install pre-requisites
sudo apt install curl ca-certificates apt-transport-https gnupg2 -y
#sudo apt install ca-certificates curl openssh-server postfix tzdata perl

```

## 2.3.2 Install GitLab
run these commands
```
# Download Gitlab package to Ubuntu
curl -s https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash

# update Ubuntu
sudo apt update

# Install the Community Edition of GitLab
sudo apt install gitlab-ce

#cd /tmp
#curl -LO https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh
#sudo bash /tmp/script.deb.sh
#sudo apt install gitlab-ce
```

## 2.3.3 Create the GitLab Configuration file
run this command :
```
sudo nano /etc/gitlab/gitlab.rb
```
Use Ctrl W to find : GitLab URL

change this in the file:
```
...
## GitLab URL
##! URL on which GitLab will be reachable.
##! For more details on configuring external_url see:
##! https://docs.gitlab.com/omnibus/settings/configuration.html#configuring-the-external-url-for-gitlab
##!
##! Note: During installation/upgrades, the value of the environment variable
##! EXTERNAL_URL will be used to populate/replace this value.
##! On AWS EC2 instances, we also attempt to fetch the public hostname/IP
##! address from AWS. For more details, see:
##! https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instancedata-data-retrieval.html
# external_url 'http://gitlab.example.com'
# We replace this URL with :
external_url 'https://buffalo.loseyourip.com'
...

```
## 2.3.4 Run the following command to reconfigure GitLab:
Run these commands:
```
sudo gitlab-ctl reconfigure

# to check the status of GitLab
sudo gitlab-ctl status

# to run GitLab
sudo gitlab-ctl start

```
## 2.3.5 To view the root (GitLab) password before its deleted (24Hr)
Run this command:
```
sudo cat /etc/gitlab/initial_root_password
```

Save this password
```
nano ~\gitlabReadMe
```
put the password there

## 2.3.6  Performing Initial Configuration Through the Web Interface
**[https://buffalo.loseyourip.com](https://buffalo.loseyourip.com)**


## 3. Recomendations
What we recomend is : 

Use this infrastrusture to get going, and do Udemy training cources to get certified.
 
