---
title: "Install Latest version of Kasm Into Proxmox"
date: 2024-04-27T07:38:40Z
draft: false
---

## 1. What is Kasm ?
"Kasm Workspaces is a Leader in Containerized Desktop Infrastructure. We used Kasm primarily as a remote browser isolation platform for open-source threat ..."

[https://kasmweb.com/](https://kasmweb.com/)

## 2. Kasm Documentation and Downloads
[Installation — Kasm 1.15.0 documentation](https://kasmweb.com/docs/latest/install.html) 

[Single Server Installation — Kasm 1.15.0 documentation](https://kasmweb.com/docs/latest/install/single_server_install.html)

[Kasm Download page](https://www-develop.kasmweb.com/downloads.html)

[Kasm Admin Guide](https://kasmweb.com/docs/latest/admin_guide.html)

[Kasm Administration / Configuration Video](https://youtu.be/JkYF20Kq4G8)
**We will Download and install Kasm from this URL :** \
https://kasm-static-content.s3.amazonaws.com/kasm_release_1.15.0.06fdc8.tar.gz

## 3. Kasm requitements
1. linux server (VM)
2. Debian baser VM in Proxmox Ubuntu server 2204
3. Swap partition is needed
4. hardware 4GB RAM


## 4. Install Kasm into Proxmox VM
**We will folow the "Standard Install" procedure on this page : (However we will make a slight change to this)** \
[Single Server Installation — Kasm 1.15.0 documentation](https://kasmweb.com/docs/latest/install/single_server_install.html)

## 4.1 Clone an Ubuntu server
```
# Set RAM to 8GB 8192 MBi or more
```
## 4.2 the Kasm Installation into VM
ssh to vm
```
ssh nickm@10.154.2.119
```
run these commands:
```
sudo su -

apt update

apt install nano wget -y

mkdir /opt/kasm

cd /opt/kasm/

ls -la
wget https://kasm-static-content.s3.amazonaws.com/kasm_release_1.15.0.06fdc8.tar.gz

ls -la

tar -xf kasm_release_1.15.0.06fdc8.tar.gz

ls -la

cd kasm_release/

ls -la

./install.sh

#Accept licence agreemenmt
y
```
Expect to get:
```
Installation Complete


Kasm UI Login Credentials

------------------------------------
  username: admin@kasm.local
  password: ZVKkD6xLPxocP
------------------------------------
  username: user@kasm.local
  password: HGnovF3wDFZbp
------------------------------------

Kasm Database Credentials
------------------------------------
  username: kasmapp
  password: Y8pnlCBXYeGhXr2ZP4wR
------------------------------------

Kasm Redis Credentials
------------------------------------
  password: BvrlP6Y7JybcYMbAQMLa
------------------------------------

Kasm Manager Token
------------------------------------
  password: dhC9l9iIDf89mwTRK75V
------------------------------------

Kasm Guac Token
------------------------------------
  password: OamijBbfo2O5uTsVsucYa6
------------------------------------

Service Registration Token
------------------------------------
  password: 5wvg27yRWT2EGEKgxTf5
------------------------------------

```

Save the credentials
```
# Scroll up to get Credential information  (you will have admin-user and user credentials and database credentials)
# save this info (Copy to Clipboard)

# Now run this command:
nano /opt/kasm/kasm-credentials.txt
```
Paste the above information into this file (from Clipboard) 

## 4.3 Login to Kasm Server
Open Browser to (Server IP Address):  \
[http://10.154.2.119](http://10.154.2.119)

## 5. Kasm tutorial
**Here is an excelent tutorial on Kasm :**
https://www.learnlinux.tv/kasm-workspaces-simplified-the-essential-guide-for-new-users/


## Bibliography
https://kasmweb.com/docs/latest/install/single_server_install.html \
https://www.learnlinux.tv/kasm-workspaces-simplified-the-essential-guide-for-new-users/ \
https://www.youtube.com/watch?v=BYJ0M04cD18 \
