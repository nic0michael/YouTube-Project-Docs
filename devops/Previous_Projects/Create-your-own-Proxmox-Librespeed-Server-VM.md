---
title: "Create Your Own Proxmox Librespeed Server VM"
date: 2024-02-10T10:46:09Z
draft: false
---
## 1. Install docker
```
sudo su -

sudo apt update -y

sudo apt install apt-transport-https ca-certificates curl software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"

apt-cache policy docker-ce

sudo apt update -y

sudo apt install docker-ce

sudo systemctl status docker

sudo systemctl enable docker

sudo systemctl start docker

sudo systemctl status docker

docker --version

```

## 2. The Docker Compose file
```
mkdir /opt/librespeed

cd /opt/librespeed

nano docker-compose.yml
```
Put this in the file:
```
---
services:
  librespeed:
    image: lscr.io/linuxserver/librespeed:latest
    container_name: librespeed
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - PASSWORD=PASSWORD
      - CUSTOM_RESULTS=false #optional
      - DB_TYPE=sqlite #optional
      - DB_NAME=DB_NAME #optional
      - DB_HOSTNAME=DB_HOSTNAME #optional
      - DB_USERNAME=DB_USERNAME #optional
      - DB_PASSWORD=DB_PASSWORD #optional
      - DB_PORT=DB_PORT #optional
      - IPINFO_APIKEY=ACCESS_TOKEN #optional
    volumes:
      - /path/to/librespeed/config:/config
    ports:
      - 80:80
    restart: unless-stopped
```

Strip off what we dont need
```
---
services:
  librespeed:
    image: lscr.io/linuxserver/librespeed:latest
    container_name: librespeed
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - PASSWORD=PASSWORD
    volumes:
      - /path/to/librespeed/config:/config
    ports:
      - 80:80
    restart: unless-stopped
```

## 3. Start this server
```
docker compose up
```
Open from the Browser
[http://10.154.2.80](http://10.154.2.80)

## 4. Install Portainer
Run these commands
```
docker volume create portainer_data

# to use port 8080
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest

# to use port 9000 for legacy reasons
docker run -d -p 9000:9000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```
## 5. Open Portainer in the Browser
[http://10.154.2.80:9000](http://10.154.2.80:9000)

Thank you






## Bibliography
https://fleet.linuxserver.io/image?name=linuxserver/librespeed

https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-20-04

https://docs.portainer.io/start/install-ce/server/docker/linux
