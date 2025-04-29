---
title: "Install Portainer"
date: 2024-02-06T13:41:28Z
draft: false
---

## 1. Create a storage for portainer to persist data
Create the volume that Portainer Server will use
```
docker volume create portainer_data
```
## 2. Install Portainner in your Docker Container
Then, download and install the Portainer Server container using Docker:
```
# docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
docker run -d -p 9000:9000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```


Open browser: \
[localhost:9080/#!/home](localhost:9080/#!/home)
