---
title: "Install Watchtower to automatically Update Docker Containers"
date: 2024-10-26T08:13:57Z
draft: false
---
Watchtower is a tool that automatically monitors and updates running Docker containers when new image versions are available.

To prevent updating critical containers you can make it monitor them only or ignore them

## The Watchtower Links
**[Watchtower web site](https://containrrr.dev/watchtower/)**

**[Docker Repository](https://github.com/containrrr/watchtower)**


## Installation
**Run these commands :**
```
sudo su -

mkdir /opt/stacks/watchtower -p

cd /opt/stacks/watchtower
```
## We will use the Docker Compose file
**Create the compose file:**
```
nano compose.yaml
```
**Put this in the file:**
```
version: "3"
services:
  watchtower:
    image: containrrr/watchtower
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock

```

## Starting Watchtower in Detached mode
```
docker compose up -d
```

## Stopping Watchtower
**Please note we prefer not to down this container just stop it**
```
docker-compose stop watchtower
```
## Looking at the logs
```
docker compose logs -f watchtower
```

## To manage containers automatically updating them
**Add the following lines to their Docker Compose files:**
```
    labels:
      - com.centurylinklabs.watchtower.enable=true
```
**Like this :**
```
services:
  my_service:
    image: my_image
    labels:
      - com.centurylinklabs.watchtower.enable=true

```

## To Monitor containers only without automatically updating them
**Add the	following lines	to their Docker	Compose	files:**
```
    labels:
      - com.centurylinklabs.watchtower.monitor-only="true"
```
**Like this :**
```
services:
  my_service:
    image: my_image
    labels:
      - com.centurylinklabs.watchtower.monitor-only="true"
```

## To Exclude Containers NEVER UPDATING THEM
**Add the	following lines	to their Docker	Compose	files:**
```
    labels:
      - com.centurylinklabs.watchtower.enable=false
```
**Like this :**
```
services:
  my_service:
    image: my_image
    labels:
      - com.centurylinklabs.watchtower.enable=false
```


## We will make Vaultwarden Automatically update
**run these commands: **
``` 
sudo su -

ls -la /opt/stacks/

cd /opt/stacks/watchtower

# check to see if this container is running
docker ps

# If this container is running
docker compose down
```
**Edit the compose file:**
```
nano compose.yaml
```
**Put this in the file:**
```
services:
  vaultwarden:
    image: vaultwarden/server:latest
    labels:
      - com.centurylinklabs.watchtower.monitor-only="true"
    container_name: vaultwarden
    restart: always
    environment:
      SIGNUPS_ALLOWED: "true"
    ports:
      - 8073:80
    volumes:
      - ./vw-data:/data
networks: {}
```
**Now start the Container in detached mode:**
```
docker compose -d up
```
## If you found this useful then :
**[Please subscribe to our channel as we have not reached our target](https://www.youtube.com/@dvp7388)**
