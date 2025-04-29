---
title: "Replace Spotify Self-Host Navidrome Docker Container in Proxmox LXC Container"
date: 2025-01-03T17:05:30Z
draft: false
---
Today we install Navidrome using a Docker Compose file in our Proxmox LXC Container

## What is Navidrome ?
- **Navidrome** is a lightweight, self-hosted music streaming server.  
- **Cost Savings**: Replaces paid streaming services like Spotify or Apple Music, saving subscription fees. 
- **Full Control**: Gives you complete ownership of your music library without ads or restrictions.  
- **Open Source & Free**: No licensing fees, with the flexibility to run on affordable hardware or cloud instances.
- **Multi-Device Support**: Stream to any device using free Subsonic-compatible apps, avoiding proprietary ecosystems.  
- **No Internet Dependency**: Access your music even without a reliable internet connection by hosting locally.  


## 1. Official Links
**[https://www.navidrome.org/docs/](https://www.navidrome.org/docs/)**

**[https://www.navidrome.org/docs/overview/#apps](https://www.navidrome.org/docs/overview/#apps)**

**[https://hub.docker.com/r/deluan/navidrome](https://hub.docker.com/r/deluan/navidrome)**

## 2. Docker compose file
Run these commands:
```bash
sudo su -

mkdir /opt/stacks/navidrome/ -p

cd /opt/stacks/navidrome/

nano compose.yaml
```
 Put this in the file
```yaml
services:
  navidrome:
    image: deluan/navidrome:latest
    ports:
      - "4533:4533"
    environment:
      - ND_SCANSCHEDULE
      - ND_LOGLEVEL
      - ND_BASEURL
      - ND_AUTOIMPORTPLAYLISTS
      - ND_UILOGINBACKGROUNDURL
      - ND_UIWELCOMEMESSAGE
      - ND_DEFAULTTHEME
      - ND_SESSIONTIMEOUT
    volumes:
      - "./data:/data"
      - "./your-music:/music:ro"
    restart: unless-stopped
```
## 3. The .env file
Run this command:
```bash
nano .env
```
Put this in the file
```
ND_SCANSCHEDULE=1h
ND_LOGLEVEL=info
ND_BASEURL=
ND_AUTOIMPORTPLAYLISTS=true
ND_UILOGINBACKGROUNDURL=https://i.imgur.com/JeWDIlv.png
ND_UIWELCOMEMESSAGE=Welcome
ND_DEFAULTTHEME=Spotify-ish
ND_SESSIONTIMEOUT=24h
```
## 4. Change Ownership of music folder
Run this command:
```bash
chown -R nickm: /opt
```

## 5. Open browser
**[http://your-server-ip:4533/](http://tiger.loseyourip.com:4533/)**

**[http://tiger.loseyourip.com:4533/](http://tiger.loseyourip.com:4533/)**

**[http://tiger.loseyourip.com:5001/compose/dockge](http://tiger.loseyourip.com:5001/compose/dockge)**
