---
title: "Install Heimdal in Docker in Proxmox LXC Container"
date: 2025-01-18T11:15:16Z
draft: false
---

## 1. Heimdall Links
**[https://heimdall.site/](https://heimdall.site/)**

**[https://hub.docker.com/r/linuxserver/heimdall](https://hub.docker.com/r/linuxserver/heimdall)**


## 2. Installation instructions
Run these instructions:
```bash
sudo su -

mkdir /opt/stacks/heimdall -p

cd /opt/stacks/heimdall

nano compose.yaml
```

Put this in the file:
```yaml
services:
  heimdall:
    image: lscr.io/linuxserver/heimdall:latest
    container_name: heimdall
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - ./config:/config
    ports:
      - 9980:80
      - 10443:443
    restart: unless-stopped
networks: {}

```
## 3. Starting Heimdall
Run these commands:
```bash
docker compose pull

# Start in detached mode
docker compose up -d
```

## 4. Open Browser
**[server-ip:9980](server-ip:9980)**

**[http://tiger.loseyourip.com:9980/](http://tiger.loseyourip.com:9980/)**
