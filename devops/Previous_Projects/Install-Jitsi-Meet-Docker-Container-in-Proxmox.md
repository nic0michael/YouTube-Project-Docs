---
title: "Install Jitsi Meet Docker Container in Proxmox"
date: 2024-11-30T09:26:42Z
draft: false
---

## Architecture :
![Jitsi meet](https://jitsi.github.io/handbook/assets/images/docker-jitsi-meet-afafdf87fea30a2fa6412baa4a3f8248.png) 

[https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker)

## Documentation
[https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker)

## Doing the Docker installation
Run these commands
```
sudo su -

apt update

apt install unzip -y

mkdir /opt/stacks/jitsi -p

cd /opt/stacks/jitsi

```


## Download and extract the latest release. 
[https://github.com/jitsi/docker-jitsi-meet/releases/latest](https://github.com/jitsi/docker-jitsi-meet/releases/latest) 

We will chose  : https://github.com/jitsi/docker-jitsi-meet/archive/refs/tags/stable-9823.zip

```
wget https://github.com/jitsi/docker-jitsi-meet/archive/refs/tags/stable-9823.zip
```

## Unzip the package:
```
unzip stable-9823.zip

ls -la

cd docker-jitsi-meet-stable-9823/
```

## Create a .env file by copying and adjusting env.example:
```
cp env.example .env

ls -la
```

## Set strong passwords in the security section options of .env file by running the following bash script
```
./gen-passwords.sh


# Change the port to 8900
nano .env

nano docker-compose.yml

# copy .env file to Jitsi root folder
cp .env ../

# copy docker-compose.yml file to Jitsi root folder
cp docker-compose.yml ../

# change directory to Jitsi root folder
cd ../
```
## Create required CONFIG directories (for Linux / Proxmox ESX)
```
mkdir -p ~/.jitsi-meet-cfg/{web,transcripts,prosody/config,prosody/prosody-plugins-custom,jicofo,jvb,jigasi,jibri}
```

## Start Container in Detached mode
Run this command :
```
docker compose up -d
```
## Open in Dockge

## Open Jitsi Server
localhost:8900 

[http://tiger.loseyourip.com:8900/](http://tiger.loseyourip.com:8900/)
