---
title: "Install Semaphore in Your Homelab"
date: 2023-11-04T06:51:44Z
draft: false
---
## 1. Documentation
[Ansible Semaphore: Administration-guide: Installation](https://docs.ansible-semaphore.com/administration-guide/installation)

## 2. Installation
We are going to install Semaphore on an Ubuntu 2204 LTS Server \
First we need a database we will install MariaDB \
Then we will install Semaphore

### 2.1 Install MariaDB
Run these commands
```
# Update Ubuntu
sudo apt update

# Add MariaDB Repo
curl -LsS https://downloads.mariadb.com/MariaDB/mariadb_repo_setup | sudo bash -s --

# Once the repository is added install server and client packages
sudo apt install mariadb-server mariadb-client


# Secure the MariaDB database
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

#### 2.1.1 If you need a Non-Root user
run these commands
```
# Connect to database
sudo mariadb

# Create User 
GRANT ALL ON *.* TO 'admin'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;

# Save user priviledges
FLUSH PRIVILEGES;

# Leave the database
exit
```

#### 2.1.2 Start Enable and Test the database 
Run these commands
```
# Start the database
sudo systemctl start mariadb

# Enable the database
sudo systemctl enable mariadb

# Check Status
sudo systemctl status mariadb
```


### 2.2 Installing Semaphore

#### 2.2.1 Install Prerequisites
Run these commands
```
sudo apt update
sudo apt install git curl wget software-properties-common nano -y

# Verify git
git version
```

#### 2.2.2 Install Ansible
```
sudo apt-add-repository ppa:ansible/ansible
sudo apt update
sudo apt install ansible -y

# Verify Ansible
ansible --version
```
#### 2.2.3 After installing Ansible we recommend you read our folowing document now:
[Installing Ansible in a Proxmox VM](http://rino.kozow.com/devops/posts/installing-ansible-in-a-proxmox-vm/)

After Ansible is installed on a control node, it is time to add managed nodes to the Ansible inventory. Let’s now edit the hosts file.
```
#### You will need to do the folowing on your Semaphore server (Allow ansible to connect to your VMs

To Generate a SSHpublic/private keypair
```
ssh-keygen -t rsa -b 4096
# Just respond by pressing the enter Key for all prompts
```
If you have generated an SSH public/private keypair on your control node and have an ssh-agent daemon running, you may install SSH public key on your managed node by using the ssh>
```
ssh-copy-id ansible@[your_vm_ip_address]

#  test SSH connection to your managed host:

ssh ansible@10.154.2.80
```
Repeat this for all VMs you need to connect to
#### 2.2.4 Install Semaphore

Find the latest stable version version \
[https://github.com/ansible-semaphore/semaphore/releases](https://github.com/ansible-semaphore/semaphore/releases) \
At the time of making this video this was the latest stable version: 2.9.37 \
[https://github.com/ansible-semaphore/semaphore/releases/tag/v2.9.37](https://github.com/ansible-semaphore/semaphore/releases/tag/v2.9.37)

Get the commands to run here and modify them to use latest stable version: \
[https://docs.ansible-semaphore.com/administration-guide/installation#package-manager](https://docs.ansible-semaphore.com/administration-guide/installation#package-manager)

Run these commands after you edited them:
```
wget https://github.com/ansible-semaphore/semaphore/releases/\
download/v2.9.37/semaphore_2.9.37_linux_amd64.deb

sudo dpkg -i semaphore_2.9.37_linux_amd64.deb

```

OR YOU CAN DO THIS:
```
# Visit the Semaphore Releases page and copy the download link for your OS. : [https://github.com/ansible-semaphore/semaphore/releases](https://github.com/ansible-semaphore/semaphore/releases)
VER=$(curl -s https://api.github.com/repos/ansible-semaphore/semaphore/releases/latest|grep tag_name | cut -d '"' -f 4|sed 's/v//g')

wget https://github.com/ansible-semaphore/semaphore/releases/download/v${VER}/semaphore_${VER}_linux_amd64.deb

#Install the Debian package downloaded.
sudo apt install ./semaphore_${VER}_linux_amd64.deb

```
#### 2.2.5 Verify Semaphore
Run these commands
```
# Check if you have Semaphore in your $PATH
echo $PATH

# Find installation path
which semaphore

# Check semaphore
semaphore version
```

#### 2.2.6 Setup Semaphore
Run these commands
```
# Setup semaphore
sudo semaphore setup

# 1. Choose MySQL : 1
# 2. confirm setup is correct : yes
# 3. Username: admin
# 4. Email: admin@leopard.loseyourip.com
# 5. Your name: Admin user : admin@localhost
# 6. Password: StrongPassword

```
**It is important that you remember the Wsername and Password you will use this to login to the Web UI**

#### 2.2.7 Now you can run Semaphore:
```
semaphore service --config=./config.json
```

Semaphore will be available via this URL : [http://localhost:3000](http://localhost:3000)











