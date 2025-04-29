---
title: "Install Docker on Ubuntu"
date: 2024-02-06T10:26:28Z
draft: false
---


## 1. Completely uninstall Docker from Ubuntu Linux
Find what packages are installed
```
dpkg -l | grep -i docker
```
Remove Docker based on what packages were found
```
sudo apt-get purge -y docker-engine docker docker.io docker-ce docker-ce-cli docker-compose-plugin
sudo apt-get autoremove -y --purge docker-engine docker docker.io docker-ce docker-compose-plugin
```

find docker
```
sudo find / -name "*docker*" | grep doclker
```
Found:
```
/var/lib/docker
```

```
sudo rm -rf /var/lib/docker
sudo rm -rf ~/.docker/
```

## 2. Installing Docker on Ubuntu server

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
