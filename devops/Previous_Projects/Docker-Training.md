---
title: "Docker Training"
date: 2024-12-01T11:52:51Z
draft: false
---
## Prerequisites
Please run gitbash for windows machines and terminal for Mac and Linux laptops if you don't have Proxmox
---

## What is Docker
1. Definition: Docker is a tool that helps you build, share, and run applications in a simple and consistent way.

2. How it Works: It uses a method called "containerization" to bundle an app and everything it needs (like code and libraries) into a single unit called a container.

3. Why It’s Useful: With Docker, your app will work the same no matter where it’s running—on your computer, a server, or the cloud.

4. Easy to Move: Containers can run on any computer with Docker installed, making it easy to move apps around.

5. Purpose: Docker makes it easier to develop, test, and deploy software while solving common problems like “it works on my machine but not on yours.”

## What is a container?
A container is like a small box that holds everything an app needs to run: the code, tools, and settings. \
Because of this, the app will work the same on any system, no matter where it runs.

## Key Features and Benefits of Docker:

1. **Containers:** Docker lets you run apps in isolated containers so they don’t interfere with each other.
2. **Portability:** Containers can run anywhere, whether it’s your laptop, a test server, or the cloud.
3. **Efficiency:** Containers are smaller and faster than virtual machines, using fewer resources.
4. **Easy Deployment:** Docker packages everything an app needs, making it simple to share and run without setup problems.
5. **Scalability:** Docker works with tools like Kubernetes to make it easy to add more containers as your app grows.
6. **Team Collaboration:** Docker fits well into DevOps workflows, helping teams build, test, and deploy apps faster.

## Docker vs. Virtual Machines (VMs):

1. How They Work:

* VMs: Run a full operating system (like Windows or Linux) for each instance.
* Docker: Uses containers that share the main operating system, making them lighter.

2. Resources:
* VMs: Use more memory and storage because they include a full operating system.
* Docker: Needs less memory and storage because it only packages the app and its essentials.

3. Startup Time:
* VMs: Take longer to start because they load a full operating system.
* Docker: Starts quickly because containers are small and don’t include a full OS.

4. Portability:
* VMs: Not as portable; they depend on the system they were created on.
* Docker: Very portable; containers can run anywhere with Docker.

5. Size:
* VMs: Large because of the operating system.
* Docker: Small because it only has the app and its dependencies.

6. Best For:
* VMs: Great for running multiple operating systems or older software that needs specific environments.
* Docker: Perfect for modern apps, small tasks, and workflows that need speed and simplicity.

---

Run these commands:
```bash
sudo su -

mkdir DockerTraining -p

cd DockerTraining
```

## 1. Creating a simple Ubuntu server using Docker
### ** 1.1 Created from Docker Commands**

1. **Pull the Ubuntu Image**:
 ```bash
sudo su -

docker pull ubuntu:latest

docker image ls
 ```

2. **Run a Container with Bash Terminal**:
```bash
# Windows users run :
winpty docker run -it ubuntu:latest bash

# Mac and Linux users run this command:
docker run -it ubuntu:latest bash
```

3. **Running commands in the container :**
```bash
ls -la /home

apt update

apt install curl
```

4. **Deleting the image**
```
docker ps -a

docker container rm <CONTAINER ID>


docker image ls

docker image rm <IMAGE ID>

docker image ls

```
---
### 1.2 Using a docker compose file to create the same  container
Run this Command :
```bash
mkdir ubuntu -p

cd ubuntu

nano compose.yaml
```
Put this in the file :

```yaml
version: '3.9'

services:
  ubuntu-server:
    image: ubuntu:latest
    container_name: ubuntu_server
    volumes:
      - ./data:/opt/data
    stdin_open: true # Keeps the container interactive
    tty: true        # Allocates a pseudo-TTY
```

Run the following commands

```
# pull the Docker images
docker compose pull

# Start the conatiner in detached mode
docker compose up -d

# To access the container, Windows users run :
winpty docker exec -it ubuntu_server bash

# To access the container, Mac and Linux users run this command:
docker exec -it ubuntu_server bash
 
# Now Inside the container run these commands:
ls -la /home

ls -la /opt

cd /opt/data

touch test1.txt

docker compose down

cd ../
```
---
## 2. We install Immich in Docker in Proxmox LXC Container

**The instructione for this installation can be found here:**
**[http://rino.kozow.com/devops/posts/install_immich_in_docker_on_proxmox_lxc_container/](http://rino.kozow.com/devops/posts/install_immich_in_docker_on_proxmox_lxc_container/)**
---


## 3 Docker Commands used
## Manipulation Commands
1. docker image ls
2. docker image rm IMAGE-ID
3. docker ps
4. docker ps -a
5. docker container ls
6. docker container stop CONTAINER-ID
7. docker container rm CONTAINER-ID
8. docker container stop CONTAINER-NAME
9. docker container start CONTAINER-NAME

## Creation commands
1. docker build -t IMAGE_NAME:version
2. docker build -t stubservice:latest .
3. docker compose up
4. docker compose up -d
5. docker compose stop
6. docker compose start
7. docker compose down

## Connect to the Containing servers of Docker containers
1. docker run -it ubuntu:latest bash | create container from image and connect to its containing server
2. docker exec -it ubuntu_server bash | connect to containing server of existing and running container


## Logs
1. docker logs CONTAINER-ID

---
## Special notes

```bash
dockercompose -f ./myfolder/compose.yaml -p my_project_name up -d
docker container ls

# in compose file we dont specify external port: and a random port is allocated

   image: jhahiaehdihed
   ports:
     -80

# To find random port
docler container ls
docker container port my_project_name_1 80

# Docker container name
aaaa_bbbb_dd

aaaa = project-name or directory-or-where-compose-file-is
bbbb =  service name
dd = incremented number

# Using Docker Compose Override fles for different environment configurations
compose.yaml  (only keeps what is common accross all environments)
compose-dev.yaml (has environment specific values)
compose-test.yaml (has environment specific values)
compose-auth.yaml

# to view the combination of the two files
docker compose -f ./docker-compose/compose.yaml f ./docker-compose/compose-dev.yaml config


# to run in dev environment (p will specify the project name)
docker compose -f ./docker-compose/compose.yaml f ./docker-compose/compose-dev.yaml -p dev_env -d

# to run in test environment (p will specify the project name)
docker compose -f ./docker-compose/compose.yaml f ./docker-compose/compose-dev.yaml -p test_env -d

# Stopping dev environment container
docker compose -f ./docker-compose/compose.yaml f ./docker-compose/compose-dev.yaml -p dev_env down

# Stopping test environment containers
docker compose -f ./docker-compose/compose.yaml f ./docker-compose/compose-dev.yaml -p test_env down


```
