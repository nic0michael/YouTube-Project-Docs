---
title: "Install Pengvin-share and Say Goodbye to Onedrive"
date: 2025-02-18T17:18:14Z
draft: false
---
## Web sites

**[https://stonith404.github.io/pingvin-share/introduction/](https://stonith404.github.io/pingvin-share/introduction/)

**[https://github.com/stonith404/pingvin-share](https://github.com/stonith404/pingvin-share)**
## Install Pingvin Share
Run these commands:
```bash
sudo su -

mkdir /opt/stacks/pingvin -p

cd /opt/stacks/pingvin

nano compose.yaml
```

Put this in the file:
```yaml
services:
  pingvin-share:
    image: stonith404/pingvin-share 
# or       ghcr.io/stonith404/pingvin-share
    restart: unless-stopped
    ports:
      - 3000:3000
    environment:
      - TRUST_PROXY=false # Set to true if a reverse proxy is in front of the container
    volumes:
      - "./data:/opt/app/backend/data"
      - "./data/images:/opt/app/frontend/public/img"
    depends_on:
      clamav:
        condition: service_healthy

  clamav:
    restart: unless-stopped
    image: clamav/clamav

```
## Starting Pengvin share
Run these commands:
```bash
docker compose pull

docker compose up -d
```

## Open Browser

**http://your-server-ip:3000/**

**[http://tiger.loseyourip.com:3000/](http://tiger.loseyourip.com:3000/)**
