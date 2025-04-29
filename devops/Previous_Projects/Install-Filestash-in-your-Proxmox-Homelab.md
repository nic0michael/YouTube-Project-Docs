---
title: "Install Filestash in Your Proxmox Homelab"
date: 2024-03-16T08:05:53Z
draft: false
---

## 1. Description
Filestash is a web-based application that provides a unified interface for accessing and managing files across various cloud storage services and servers.

1. A Web-based file management application.
2. That unifies access to multiple cloud storage services.
3. It connects services like Dropbox, Google Drive, OneDrive, FTP, etc.
4. Provides a single interface for file browsing and management.
5. Supports uploading, downloading, and organizing files.
6. Allows sharing files across connected storage platforms.
7. Aims to streamline file management workflows.
8. Open-source project, enabling community contributions and customization.
9. Available in the Docker repository for easy deployment and scalability.

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

apt-cache policy docker-ce

sudo apt update -y

# Now install Docker
sudo apt install docker-ce

#Verify Docker installation
docker --version

sudo systemctl status docker

sudo systemctl enable docker

sudo systemctl start docker

sudo systemctl status docker

```

## 4. Install Portainer
[Procedure to install Portainer : http://rino.kozow.com/devops/posts/install-portainer-inside-our-proxmox-lxc-container/](http://rino.kozow.com/devops/posts/install-portainer-inside-our-proxmox-lxc-container/)


## 5. Install Nextcloud
**Filestash is in DockerHub : [https://hub.docker.com/r/machines/filestash/tags](https://hub.docker.com/r/machines/filestash/tags)** \
docker pull machines/filestash:latest \
Maintained by mickaelkerjean

The Docker Compose file can be found here: \
https://github.com/mickael-kerjean/filestash/blob/master/docker/docker-compose.yml

We have modified the docker composed file to use this local folder : /opt/filestash/storage

Run these commands :
```
sudo su -
mkdir /opt/filestash
mkdir /opt/filestash/storage
mkdir /opt/filestash/storage/backup
cd /opt/filestash

nano docker-compose.yml
```

Put this in the file :
```
---
version: '3'

services:
  nextcloud:
    image: nextcloud
    container_name: nextcloud
    restart: unless-stopped
    networks:
      - cloud
    depends_on:
      - nextclouddb
      - redis
    ports:
      - 8081:80
    volumes:
      - ./html:/var/www/html
      - ./custom_apps:/var/www/html/custom_apps
      - ./config:/var/www/html/config
      - ./data:/var/www/html/data
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Los_Angeles
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_PASSWORD=dbpassword
      - MYSQL_HOST=nextclouddb
      - REDIS_HOST=redis

  nextclouddb:
    image: mariadb
    container_name: nextcloud-db
    restart: unless-stopped
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW
    networks:
      - cloud
    volumes:
      - ./nextclouddb:/var/lib/mysql
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Los_Angeles
      - MYSQL_RANDOM_ROOT_PASSWORD=true
      - MYSQL_PASSWORD=dbpassword
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud

  collabora:
    image: collabora/code
    container_name: collabora
    restart: unless-stopped
    networks:
      - cloud
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Los_Angeles
      - password=password
      - username=nextcloud
      - domain=example.com
      - extra_params=--o:ssl.enable=true
    ports:
      - 9980:9980

  redis:
    image: redis:alpine
    container_name: redis
    volumes:
      - ./redis:/data
    networks:
      - cloud

  nginx-proxy:
    image: 'jc21/nginx-proxy-manager:latest'
    container_name: nginx-proxy
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Los_Angeles
    restart: unless-stopped
    ports:
      - '80:80'
      - '81:81'
      - '443:443'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt

networks:
  cloud:
    name: cloud
    driver: bridge
```

Start the Docker container in deteched mode:
```
docker compose up -d
```

## 6. Administrate Filestash
Open Portainer in your browser:	\
http://<server-ip-address>:9000/ \
**[http://10.154.2.86:9000/](http://10.154.2.86:9000/)**


Open Nextcloud in your browser \
http://<server-ip-address>:8081/ \
**[http://10.154.2.86:8081/](http://10.154.2.86:8081/)**
Create you User and Password

## Bibliography
https://chrisgrime.medium.com/deploy-nextcloud-with-docker-compose-935a76a5eb78
