---
title: "Replacing Docker DockerHub With Buildah Podman and More"
date: 2023-03-22T09:23:44Z
draft: false
---
## What Podman
1. It is a perfect replacement for Docker
2. Its lighterweight
3. It offers better security dont need to run as root
4. you can run containers rootless
5. They removed the problematic Docker Daemon which runs in the background.
6. the Docker Daemon runs with root priveledges

## The official pages: 
https://podman.io/ \
https://podman.io/getting-started/installation

## Adding APT Repository
```
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.opensuse.org/repositories/devel:kubic:libcontainers:unstable/xUbuntu_$(lsb_release -rs)/Release.key \
  | gpg --dearmor \
  | sudo tee /etc/apt/keyrings/devel_kubic_libcontainers_unstable.gpg > /dev/null
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/devel_kubic_libcontainers_unstable.gpg]\
    https://download.opensuse.org/repositories/devel:kubic:libcontainers:unstable/xUbuntu_$(lsb_release -rs)/ /" \
  | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:unstable.list > /dev/null
sudo apt-get update -qq
sudo apt-get -qq -y install podman
```
## Install podman in Ubuntu2004.1
```
sudo apt update
sudo apt install podman
```
## Install Podman-Compose in Ubuntu2004.1
```
sudo apt update
sudo apt install python3-pip
pip3 install podman-compose
```

## What is Buildah
1. Buildahis a dockercontainer Build tool
2. Alternative to docker build 
3. builds images using the dockerfile
4. It uses a different location for it cache of its images than what Docker uses

## Installing Buildah
```
sudo apt update
sudo apt install buildah
```

## Running buildah
```
buildah pull alpine

# To list images
buildah images

# To use a dockerfile to build a docker image with tag & version simple-app:1.0.0 (the dot is context ie current folder)
buildah bud --format=docker -t simple-app:1.0.0 .
```

## Using Podman to run the container 
```
# pull latest ubuntu image in github
podman pull ubuntu:latest

# list images on our machine
podman image list

# running an image executing a command
podman run -it ubuntu:latest sh -c "echo 'Hello'" 

# listing containers
podman container ls
# run a container in the background
podman run -t -d ubuntu:latest bash
# we should be able to see this container
podman container ls
# exec bash from this container
podman exec -ti compenant-wilbur bash

# to kill a running container
podman ps
podman kill compenant-wilbur
# verify its killed
podman ps


# run a container and provide a name
podman run -t -d --name great_hero ubuntu:latest
# show this container running
podman ps 


# run in detached mode
podman run -d -p 8080:80 simple-app:1.0.0 

# list running containers (expect one starting with 17)
podman ps

# Delete running container forceing delete of container starting with 17
podman rm -f 17


```

## copy file from host machine into container 
```
touch secret_stuff
echo "secret sauce" > secret_stuff
# run a container and provide a name
podman run -t -d --name great_hero ubuntu:latest

#copy the file to the container
podman cp secret_stuff great_hero:/root

# get file content
exec -ti great_hero bash

ls -lah ~/
cat ~/secret_stuff

# commit changes to this container
podman commit great_hero

```

## Pushing a image from a dockerfile
```
nano Dockerfile
```
put this is file
```
FROM ubuntu:latest
RUN apt -y update && \
    apt -y upgrade
COPY secret_stuff /root
CMD ["bash","-s","cat/root/secret_stuff"]
```


```
# copy file from host machine into container 
touch secret_stuff
echo "secret sauce" > secret_stuff


```



## The official pages:
https://buildah.io/


## Bibliography
1. [How to live without Docker for developers - Part 1 | Migration from Docker to Buildah and Podman](https://www.youtube.com/watch?v=Fl0iLoAMdzc) 
2. [PODMAN vs DOCKER - should you switch now?](https://www.youtube.com/watch?v=jzd0YoqBJjc) 
3. [Replacing Docker with podman? The results are not what I expected! (Part 1/2)](https://www.youtube.com/watch?v=NqxWiwjYlBs)
4. [Host your own docker registry | Local Docker Registry | Docker Registry using Docker Compose](https://www.youtube.com/watch?v=8gEs_zefNYA)
