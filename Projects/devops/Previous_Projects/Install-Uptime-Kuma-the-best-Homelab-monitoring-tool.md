---
title: "Install Uptime Kuma the Best Homelab Monitoring Tool"
date: 2024-11-17T06:03:02Z
draft: false
---
## 1. What is Uptime Kuma
1. Real-Time Monitoring:Monitor services, websites, APIs, and Docker containers to ensure uptime and performance.
2. Customizable Notifications:Send alerts via various platforms, including Telegram, Slack, Discord, email, Microsoft Teams, and more.
3. Beautiful Dashboard:Intuitive and visually appealing interface for managing and viewing all monitored services.
4. Docker Monitoring:Easily monitor Docker containers alongside other services to track their health and status.


## 2.Installation
### 2.1 Create Docker Compose file
Run these commands:
```
sudo su -

mkdir /opt/stacks/uptime-kuma -p

cd /opt/stacks/uptime-kuma

nano compose.yaml
```

Put this into the file
```
version: "3.3"
services:
  uptime-kuma:
    restart: always
    ports:
      - 3001:3001
    volumes:
      - ./uptime-kuma-data:/app/data # Local folder mapped to container volume
    container_name: uptime-kuma
    image: louislam/uptime-kuma:1
volumes: {}
networks: {}
```

## 2.2 Start container in detached mode
Run this command
```
docker compose up -d
```

## 2.3 Open in browser
**[http://10.154.2.87:3001](http://10.154.2.87:3001)**

OR

**[http://tiger.loseyourip.com:3001](http://tiger.loseyourip.com:3001)**

## 3. Demo in video
You need to watch the Video to see this in action
### 3.1 Now we demonstrate how to condifure Uptime Kuma

1. Listen to web URL :  https://www.yahoo.com/

2. Monitor Proxmox server : https://upupa.loseyourip.com:8006/ URL: upupa.loseyourip.com Port: 8006

3. Setup Notifications on existing server being monitored


## 4.  Monitor Docker Containers
You may want to modify the Docker continers to use this method \
We would look at headless containers this way \
This is a complicated process beyond the scope of this video

https://github.com/louislam/uptime-kuma/wiki/How-to-Monitor-Docker-Containers

Add to docker-compose file:
```
volumes:
   - /var/run/docker.sock:/var/run/docker.sock
```

