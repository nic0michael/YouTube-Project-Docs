---
title: "Install Photoview Proxmox LXC Container"
date: 2025-01-25T14:17:22Z
draft: false
---
**This is a good replacement for Google Photos**

## What is PhotoView?
  A lightweight, fast, and user-friendly photo viewer designed for browsing and managing image collections.  

- **Lightweight and Fast**: A responsive and efficient photo viewer optimized for quick browsing.  
- **Wide Format Support**: Handles a variety of image file types, including popular formats like JPEG, PNG, and BMP.  
- **Simple Interface**: User-friendly design for easy navigation and photo viewing.  
- **Basic Tools**: Offers essential features like zoom, rotation, and slideshow modes.  
- **Ideal for Everyday Use**: Perfect for casual users who need a hassle-free photo viewing experience.  


## Why would I want to use PhotoView?
It’s perfect for quick image viewing, supports various formats, and offers a clean, straightforward interface.  

## Installation instructions
Run these commands:
```bash
sudo su 

mkdir /opt/stacks/photoview/  -p

cd /opt/stacks/photoview/

nano compose.yaml
```
Put this in the file:
```yaml
version: "3.8"
services:
  photoview:
    image: photoview/photoview:2
    hostname: photoview
    container_name: photoview
    restart: unless-stopped
    stop_grace_period: 10s
    ports:
      - ${PHOTOVIEW_PORT}:80 # HTTP port (host:container)
    depends_on:
      mariadb:
        condition: service_healthy
    security_opt:
      - seccomp:unconfined
      - apparmor:unconfined
    environment:
      PHOTOVIEW_DATABASE_DRIVER: ${PHOTOVIEW_DATABASE_DRIVER}
      PHOTOVIEW_MYSQL_URL: ${MARIADB_USER}:${MARIADB_PASSWORD}@tcp(photoview-mariadb)/${MARIADB_DATABASE}
      PHOTOVIEW_LISTEN_IP: 0.0.0.0
      MAPBOX_TOKEN: ${MAPBOX_TOKEN}
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /etc/timezone:/etc/timezone:ro
      - ./data/storage:/home/photoview/media-cache
      - ./media:/photos
  mariadb:
    image: mariadb:lts
    hostname: photoview-mariadb
    container_name: photoview-mariadb
    restart: unless-stopped
    stop_grace_period: 5s
    command: mariadbd --innodb-buffer-pool-size=512M
      --transaction-isolation=READ-COMMITTED --character-set-server=utf8mb4
      --collation-server=utf8mb4_unicode_ci --max-connections=512
      --innodb-rollback-on-timeout=OFF --innodb-lock-wait-timeout=120
    security_opt:
      - seccomp:unconfined
      - apparmor:unconfined
    environment:
      MARIADB_AUTO_UPGRADE: "1"
      MARIADB_DATABASE: ${MARIADB_DATABASE}
      MARIADB_USER: ${MARIADB_USER}
      MARIADB_PASSWORD: ${MARIADB_PASSWORD}
      MARIADB_ROOT_PASSWORD: ${MARIADB_ROOT_PASSWORD}
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /etc/timezone:/etc/timezone:ro
      - ./data/database/mariadb:/var/lib/mysql
    healthcheck:
      test: healthcheck.sh --connect --innodb_initialized
      interval: 1m
      timeout: 5s
      retries: 5
      start_period: 3m
networks: {}

```

Run this command :
```bash
nano .env
```
Put this in the file:
```
# MariaDB credentials
MARIADB_DATABASE=photoview
MARIADB_USER=photoview_user
MARIADB_PASSWORD=photoview_password
MARIADB_ROOT_PASSWORD=photoview_root_password

# Photoview configuration
PHOTOVIEW_PORT=8800
PHOTOVIEW_DATABASE_DRIVER=mysql
MAPBOX_TOKEN=your_mapbox_token_here

```
## Start Photoview
```bash
docker compose up -d
```

## Open in the Browser
**[http://your-server-ip:8000/](http://your-server-ip:8000/)**

**[http://tiger.loseyourip.com:8000/](http://tiger.loseyourip.com:8000/)**
