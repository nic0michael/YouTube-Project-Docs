---
title: "Install Rocketchat in Your Proxmox LXC Container With Docker"
date: 2024-12-07T14:30:49Z
draft: false
---
## 1. What is Rocket.Chat?  
- An open-source team communication platform, similar to Slack.  
- Self-hosted, customizable, and privacy-focused.  
- Provides real-time chat, video calls, and collaboration tools.  
- It can de installed using Docker

## 2. Why Use Rocket.Chat?  
- Full control over your data and no reliance on third-party services.  
- Easy to deploy with Docker for self-hosting.  
- Feature-rich, with integrations for automation and productivity.  
- The really workd together with Jitsi Meeti which we covered last week
- They do have a Free Offering

## 2.2 Slack
- Slack is not opensource
- Slack is not free
- You cant Self Host Slack

## 3. Official pages
This is what we need : \
https://docs.rocket.chat/docs/deploy-with-docker-docker-compose \
https://www.rocket.chat/pricing



For the latest Release: \
https://github.com/RocketChat/Rocket.Chat/releases

## 4. Install Rocket Chat
Run these commands :
```
sudo su -

mkdir -p /opt/stacks/rocketchat 

cd /opt/stacks/rocketchat
```
### 4.1 Get the Docker compose file
```
wget https://raw.githubusercontent.com/RocketChat/Docker.Official.Image/master/compose.yml


ls -la

nano compose.yaml
```
### 4.2 Select the release and create the .env file
https://github.com/RocketChat/Rocket.Chat/releases
```
release: 6.10.8
Engine versions
Node: 14.21.3
MongoDB: 4.4, 5.0, 6.0
Apps-Engine: 1.43.3

```

Create an env file based on : \
https://github.com/RocketChat/Docker.Official.Image/blob/main/env.example
```
nano .env
```


Sample Docker-Compose File: Here's a simple docker-compose.yml setup:

```yaml
version: "3"
services:
  rocketchat:
    image: rocketchat/rocket.chat:latest
    container_name: rocketchat
    environment:
      - MONGO_URL=mongodb://mongo:27017/rocketchat
      - ROOT_URL=http://localhost:3000
      - PORT=3000
    ports:
      - 3000:3000
    depends_on:
      - mongo
  mongo:
    image: mongo:5.0
    container_name: mongo
    command: mongod --smallfiles --oplogSize 128 --replSet rs0 --storageEngine wiredTiger
    volumes:
      - ./data/db:/data/db
      - ./data/configdb:/data/configdb
```
Run this command:
```bash
docker compose pull
```

Run Rocket.Chat: Simply execute:
```bash
docker compose up -d && docker compose logs
```

Access the Web Interface: Open http://<server-ip>:3000 in your browser to complete the setup. \
Open the browser to: \
[http://tiger.loseyourip.com:3000](http://tiger.loseyourip.com:3000)

