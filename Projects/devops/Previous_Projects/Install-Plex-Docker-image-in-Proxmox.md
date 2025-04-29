---
title: "Install Plex Docker Image in Proxmox"
date: 2024-10-29T09:14:31Z
draft: false
---

## References
https://hub.docker.com/u/linuxserver

https://www.linuxserver.io/

all images:
https://fleet.linuxserver.io/

https://fleet.linuxserver.io/image?name=linuxserver/plex

https://hub.docker.com/r/linuxserver/plex


## 

put this is file
```
---
services:
  plex:
    image: lscr.io/linuxserver/plex:latest
    container_name: plex
    network_mode: host
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - VERSION=docker
      - PLEX_CLAIM= #optional
    volumes:
      - ./library:/config
      - ./tvseries:/tv
      - ./movies:/movies
    restart: unless-stopped

```

##
http://localhost:32400/web

http://tiger.loseyourip.com:32400/web

**It is better to use the IP Address of the server**

**http://10.154.2.87:32400/web**
