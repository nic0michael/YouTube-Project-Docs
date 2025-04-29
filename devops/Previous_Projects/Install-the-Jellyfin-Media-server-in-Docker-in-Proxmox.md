---
title: "Install the Jellyfin Media Server in Docker in Proxmox"
date: 2024-09-29T13:47:45Z
draft: false
---
## 1. The Docker Compose file can be found here
[Jellyfin Installation Dcumentation](https://jellyfin.org/docs/general/installation/)

and the docker compose file here : \
[Docker-compose file](https://jellyfin.org/docs/general/installation/container)

However this needs modification to make it work

## 2. Our Installation Instructions
run these commands in the terminal
```
# become root user
sudo su -

# create the jellyfin folder
mkdir /opt/stacks/jellyfin -p

cd /opt/stacks/jellyfin/
```

Create the Docker Compose file
```
nano compose.yaml
```

Put this in the file :
```
version: '3.8'

services:
  jellyfin:
    image: jellyfin/jellyfin:latest
    container_name: jellyfin
    restart: unless-stopped
    volumes:
      - ./config:/config  # Using relative path for config
      - ./cache:/cache     # Using relative path for cache
      - ./media:/media     # Using relative path for media
    ports:
      - "8096:8096"
      - "8920:8920"
    network_mode: host

```

Now create The Environment file
```
nano .env
```

Put this in the file
```
JELLYFIN_USER=admin
JELLYFIN_PASSWORD=P@ssword

```
## 3. Start the container NOT in detached mode
Run this command
```
docker compose up 
```

## 4. Open in Browser port 8096
Our Proxmox Container server has IP address : 10.154.2.87 \
**[http://10.154.2.87:8096](http://10.154.2.87:8096)**

And DNS Name : http://tiger.loseyourip.com:8096/
**[http://tiger.loseyourip.com:8096/](http://tiger.loseyourip.com:8096/)

## 5. you can find this docker Stack in our Github repository
**[https://github.com/nic0michael/MyDockerStacks](https://github.com/nic0michael/MyDockerStacks)**
