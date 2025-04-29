---
title: "Install Homarr in Your Proxmox Server"
date: 2024-04-01T13:16:23Z
draft: false
---

This is a very easy Docker installation
## What is Homarr
**A simple, yet powerful dashboard for your server.**

1. Simplify the management of your server with Homarr 
2. A sleek, modern dashboard that puts all of your apps and services at your fingertips. 
3. With Homarr, you can access and control everything in one convenient location. 
4. Homarr seamlessly integrates with the apps you've added, providing you with valuable information and giving you complete control. 
5. Installation is a breeze, and Homarr supports a wide range of deployment methods.


[https://homarr.dev/](https://homarr.dev/)


## Demo site to try out Homarr
[They provide a demo site for you to try this out (click this link)](https://demo.homarr.dev/start-demo)
## Documentation
[They provide rich documentation (click this link](https://homarr.dev/docs/category/getting-started)

## Homar Installation
[https://homarr.dev/docs/getting-started/installation/](https://homarr.dev/docs/getting-started/installation/)

run these commands in your Proxmox LXC container
```
sudo su -

apt update 

apt install nano -y

mkdir /opt/honarr

cd /opt/homarr

nano docker-compose.yml
```
Put this in the file:
```
version: '3'
#---------------------------------------------------------------------#
#     Homarr - A simple, yet powerful dashboard for your server.     #
#---------------------------------------------------------------------#
services:
  homarr:
    container_name: homarr
    image: ghcr.io/ajnart/homarr:latest
    restart: unless-stopped
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock # Optional, only if you want docker integration
      - ./homarr/configs:/app/data/configs
      - ./homarr/icons:/app/public/icons
      - ./homarr/data:/data
    ports:
      - '8075:7575'
```

Start with docker compose in detached mode
```
docker compose up -d
```

Open in Portainer :
[http://buffalo.loseyourip.com:9000/](http://buffalo.loseyourip.com:9000/)

Open in browser:
[http://buffalo.loseyourip.com:8075](http://buffalo.loseyourip.com:8075)


