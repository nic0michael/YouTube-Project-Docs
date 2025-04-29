---
title: "Install Portainer Inside Our Proxmox LXC Container"
date: 2024-02-17T12:21:38Z
draft: false
---
## 1. We created a LXC Container in our Proxmox Server
[Creating a LXC Container in our Proxmox Server](http://rino.kozow.com/devops/posts/create-lxc-containers-in-proxmox/)
## 2. We Installed Docker in our Proxmox LXC Container
[Installing Docker in Ubuntu Server 2204](http://rino.kozow.com/devops/posts/install-docker-on-ubuntu/)
## 3. Installing Portainer in our Proxmox server
Our instructions are based on this web page : \
[https://docs.portainer.io/start/install-ce/server/docker/linux](https://docs.portainer.io/start/install-ce/server/docker/linux)


### 3.1 Create a storage for portainer to persist data
Create the volume that Portainer Server will use
```
docker volume create portainer_data
```
### 3.2 Install Portainner in your Docker Container
Then, download and install the Portainer Server container using Docker:
```
# docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
docker run -d -p 9000:9000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

### 3.4 Access the Portainer Dashboard
Open browser: \
[localhost:9080/#!/home](localhost:9080/#!/home)
