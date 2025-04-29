---
title: "Install Droppy in Your Proxmox Server"
date: 2024-03-17T16:31:28Z
draft: false
---


**SelfHost Droppy, replace Google Drive and One Drive and get your privacy back.**

## 1. Description

Droppy is a Self Hosted File Cloud 

I was looking for a simple Selfhosted cloud application that could work the first time \
The installation should be from a docker compose file without having to do alot of things to make it work \
I fould a lot of these type of applications in Github but most of them did not meet my requirement
 
1. A very simple  Web application that alloys you so share files
2. It features a Drag and Drop interface to upload files
2. You can send	links to files	that the other persons browser will download
3. The way we impliment this makes it easy to Backup the files in the data folder

### 1.1 Droppy provided a dockercompose file in Github
https://github.com/silverwind/droppy/blob/master/examples/docker-compose.yml

## 2. Creating a Docker instance in a Proxmox container
### 2.1 Upload latest Ubuntu ISO file to storage drive ZZ_User_Data

Open The Proxmox server
1. In the left navigation pane click on your Proxmox servers icon
2. On the right hand side top click on the Shell button
run this command:
```
pveam available
```
We found these options for ubuntu:
```

system          ubuntu-20.04-standard_20.04-1_amd64.tar.gz
system          ubuntu-22.04-standard_22.04-1_amd64.tar.zst
system          ubuntu-23.04-standard_23.04-1_amd64.tar.zst
system          ubuntu-23.10-standard_23.10-1_amd64.tar.zst
```
We will select : ubuntu-23.10-standard_23.10-1_amd64.tar.zst
We want to download this to storage : ZZ_User_Data
run this command
ubuntu-23.10-standard_23.10-1_amd64.tar.zst

run this command
```
pveam download ZZ_User_Data ubuntu-23.10-standard_23.10-1_amd64.tar.zst
```

### 2.2 Create the Proxmox container Storage86
1. Click on Create CT button
2. Set Container ID : CT ID = 401
3. Set Hostname :             Storage86
4. Set and confirm root password
5. Press Next
6. Set Storage : (we will use one of the 2TB drives) = ZZ_User_Data
7. Select Template from dropdown as : ubuntu-23.10-standard_23.10-1_amd64.tar.zst
8. Press the Next button
9. Select Storage from dropdown as : ZZ_User_Data
10. Set Disk Size GiB as (Allocate enough storage) : 100 GiB
11. Press the Next button
12. Set cores to : 2 (This dependes on your hardware)
13. Set CPU Limit to : 1 (This dependes on your hardware)
14. Press the Next button
15. Set Memory to : 8192 MiB
16. Set Swap to 8192 MiB 
17. Press the Next button
18. Set IP v4 CIDR to :10.154.2.86/24
19. Set Gateway ip v4 to 10.152.2.3
20. Press the Next button
21. Set DNS Domain to : 10.152.2.3
22. Set DNS servers to 8.8.8.8,1.1.1.1
23. Press the Next button
24. Press the Finish button

### 2.3 Server administration
1. Click on the Container Storage86 on theTop Right side
2. Start the Container
3. Click the Start button Top Right side

run these commands:
```
sudo apt update

sudo apt upgrade -y

sudo apt install curl nano wget -y
```
### 2.4 Create Sudoer user with Passwordless ssh login
**[Procedure can be fould here : http://rino.kozow.com/linux/posts/usefull-linux-commands/](http://rino.kozow.com/linux/posts/usefull-linux-commands/)**

## 3. Install Docker in the Container
### 3.1 Installing Docker on Ubuntu server

```
sudo su -

#Prerequisites
sudo apt update -y

sudo apt install apt-transport-https ca-certificates curl software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"

sudo apt update

apt-cache policy docker-ce

sudo apt update

# Now install Docker
sudo apt install docker-ce -y

#Verify Docker installation
docker --version

sudo systemctl status docker

sudo systemctl enable docker

sudo systemctl start docker

sudo systemctl status docker

```

## 4. Install Portainer
[Procedure to install Portainer : http://rino.kozow.com/devops/posts/install-portainer-inside-our-proxmox-lxc-container/](http://rino.kozow.com/devops/posts/install-portainer-inside-our-proxmox-lxc-container/)


## 5. Install Droppy

Run these commands :
```
mkdir /opt/droppy
mkdir /opt/droppy/data
mkdir /opt/droppy/config
```


run this command:
```
nano docker-compose.yml
```
Put this in the file:
```
version: '3'
services:
  droppy:
    container_name: droppy
    image: silverwind/droppy
    ports:
      - '8989:8989'
    volumes:
      - /opt/droppy/config:/config
      - /opt/droppy/data:/files
    restart: unless-stopped
```
Run this command: (Start Droppy Container in <detached mode)
```
docker compose up -d
```
## 6. Administrate Droppy
Open Portainer in your browser:	\
http://server-ip-address:9000/ \
**[http://10.154.2.86:9000/](http://10.154.2.86:9000/)**


## Open Droppy in Browser
http://server-ip-address:8989/ \
**[http://10.154.2.86:8989](http://10.154.2.86:8989)**
