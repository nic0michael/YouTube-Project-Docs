---
title: "Install This App and Say Goodbye to Discord and Slack"
date: 2025-03-01T10:08:19Z
draft: false
---

## Urls

1. Mattermost Open-Source (Free Version):
   - https://mattermost.com/download/

2. Mattermost Documentation (Installation, Setup, etc.):
   - https://docs.mattermost.com/

3. Mattermost GitHub (Open-Source Code):
   - https://github.com/mattermost/mattermost-server

You can copy and paste these URLs into your browser directly! Let me know if you need anything else!

## Installation
Run these commands
```bash
sudo su -

mkdir /opt/stacks/mattermost -p

cd /opt/stacks/mattermost/

nano compose.yaml
```
Put this in the file:
```yaml
version: "3.8"
services:
  mattermost-preview:
    image: mattermost/mattermost-preview
    container_name: mattermost-preview
    ports:
      - 8065:8065
      - 8443:8443
    restart: unless-stopped
networks: {}
```
## Starting the Server
run these commands:
```bash
docker compose pull

docker compose up -d
# This will take a few minutes before you can open this in the Browser
```

## In your Browser
if you try to connect to this server before its completely started you will get errors go drink a cup of Joe and come back here
**http://your-server-ip:8065/**

**[http://tiger.loseyourip.com:8065/](http://tiger.loseyourip.com:8065/)**
