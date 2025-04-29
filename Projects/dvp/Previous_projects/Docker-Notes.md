---
title: "Docker Notes"
date: 2023-01-20T13:44:08Z
draft: false
---
## 1. Commands Summary:
### Pulling (down) Docker Containers
```
sudo docker pull ubuntu
sudo docker pull sonarqube
sudo docker pull postgres
sudo docker pull nginx
sudo docker pull ubuntu
sudo docker pull mariadb
sudo docker pull httpd
sudo docker pull centos
```
### List Docker Containers
```
sudo docker container ls 
sudo docker container ls -l
sudo docker container ls -a
sudo docker container ls --all

```
### List all Images
```
sudo docker images 
```
### Stopping a Container
```
sudo docker stop ubuntu 
```
### Deleting a Container
```
sudo docker stop  hello-world
sudo docker rm hello-world
sudo docker stop  724a4081c966 
sudo docker rm 724a4081c966 
```
### Deleting an Image
```
sudo docker images
sudo docker rmi hello-world
```

### Running Containers :
```
sudo docker run hello-world
sudo docker run -it ubuntu bash
```
### Running a Container in the Interactive Mode
```
sudo docker run -i -t ubuntu:latest /bin/bash
```
### Running Containers in Attached Mode:
```
docker run -rm mongo                 ..... Mongo listens to port 27017
```
Ctrl C to stop it

### Running Containers in Detached mode (as a service):
```
sudo docker run -d -p 80:80 gvelrajan/hello-world:v1.0
sudo docker run -d -p 8080:80 gvelrajan/hello-world:v1.0
sudo docker ps
```
### Run and then delete the container:
```
sudo docker run --rm -t ubuntu bash
```
### Running a webserver with internal port 80 and external port 4000 and 4001
```
sudo docker run --rm -it -p 4000:80 simple-webserver
sudo docker run --rm -it -p 4001:80 simple-webserver
```
**You access the following webserver instances from the browser at:** \
http://localhost:4000 \
http://localhost:4001 \

### Pruning Docker Containers Images and volumes

#### Pruning unused Docker Containers
The following removes all containers
```
sudo docker container prune 
```
The following removes containers created more than 5 minutes ago:
```
sudo  docker container prune --force --filter "until=5m"

```
#### Prune unused Docker Images
```
sudo docker image prune
```
Limiting images pruned to only images created more than 24 hours ago:
```
sudo docker image prune -a --filter "until=24h"
```

#### Prune Volumes
```
sudo docker volume prune
```

Removing volumes which are not labelled with the keep label:
```
sudo docker volume prune --filter "label!=keep"
```



## 2. Installation Procedure
```bash
# Ubuntu
sudo apt update
sudo apt  install docker.io
sudo groupadd docker
sudo usermod -aG docker ${USER}
```

#Centos
```
yum update
yum  install docker.io
groupadd docker
usermod -aG docker ${USER}
```
**Verify**
```
docker --version
```
**Needed for Clustering**
```
sudo chmod 666 /var/run/docker.sock
```




