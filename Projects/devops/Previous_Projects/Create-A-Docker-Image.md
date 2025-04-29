---
title: "Create a Docker Image"
date: 2024-05-20T12:52:13Z
draft: false
---
## References:
https://www.starrocks.io/blog/four-simple-ways-to-deploy-starrocks

https://docs.starrocks.io/docs/deployment/prepare_deployment_files/

https://www.starrocks.io/download/community

https://releases.starrocks.io/starrocks/StarRocks-3.3.0-rc01.tar.gz

Stable : \
https://releases.starrocks.io/starrocks/StarRocks-3.1.11.tar.gz

Latest 3.2 : \
https://releases.starrocks.io/starrocks/StarRocks-3.2.6.tar.gz

https://join.slack.com/t/starrocks/shared_invite/zt-2fou0ynxe-mZpcW54KewxWocehcBNoKQ

## 1. Creating the Docker file
Run these commands:
```
nano Dockerfile
```
Put this in the file:
```
# Use Ubuntu 22.04 as the base image
FROM ubuntu:22.04

# Set the working directory
WORKDIR /opt/starrocks

# Install wget and tar
RUN apt-get update && apt-get install -y wget tar tree nano curl

# Download the StarRocks tar.gz file
RUN wget https://releases.starrocks.io/starrocks/StarRocks-3.2.6.tar.gz

# Extract the StarRocks tar.gz file
RUN tar -xzvf StarRocks-3.1.11.tar.gz

# Create a volume
#VOLUME ["/opt/starrocks"]

# Open the folder in the container (this is implicit with WORKDIR)
WORKDIR /opt/starrocks

# Keep the container running
CMD tail -f /dev/null
```
## 2. Creating the volume : starrocksdata
run these commands :
```
mkdir -p /opt/starrocks/starrocks-app/starrocksdata

docker volume create starrocksdata
```
## 3. Building the Docker Image
Run this command:
```
docker build -t starrocks-app .
```

## 4.Running the Docker Image
Run these commands:
```
#docker run -d --name starrocks-app -v starrocksvol:/opt/starrocks starrocks-app
docker run -d --name starrocks-app -v /opt/starrocks/starrocks-app/starrocksdata:/opt/starrocksvol starrocks-app


# Check it is working
docker ps -a
```

## 5. Use docker exec to Connect to the Container
Run this command :
```
docker exec -it starrocks-app /bin/bash

```
## 6. Stopping the container
Run this command:
```
docker stop starrocks-app
```
## 7. The Cleanup
Run these commands:
```
# List all docker containers
docker container ls -a

# Remove the stopped container:
docker rm starrocks-app

# Force stop the container: (If the container does not stop gracefully)
docker kill starrocks-app

# Check that the Docker Container is no longer running:
docker ps

# Listing Docker images
Docker image ls

# Remove the Docker image:
docker rmi starrocks-app
# OR
docker image rm starrocks-app

# Check that you have removed the image:
docker image ls
```
## 8. Copmmands to run in Dockerfile
## 8.1 FE Frontend commands
```
cd /opt/starrocks/StarRocks-3.1.11/fe/bin

ls -la

# The start Command:
/opt/starrocks/StarRocks-3.1.11/fe/bin/start_fe.sh
```
## 8.2 BE Commands
```
cd /opt/starrocks/StarRocks-3.1.11/be/bin

ls -la
# the start command:
/opt/starrocks/StarRocks-3.1.11/be/bin/start_be.sh
```
## 8.3 Broker commands
```
cd /opt/starrocks/StarRocks-3.1.11/apache_hdfs_broker

ls -la

# the start command:
/opt/starrocks/StarRocks-3.1.11/apache_hdfs_broker/start_broker.sh

```
## 9. View Docker log aftger running
```
docker logs starrocks-app
```

## 10. Revised Dockerfile
You Need to refer to section 5. for the Cleanup Procedure

### 10.1 Revise the Dockerfile
Run this command
```
Nano Dockerfile
```
put this in the file:
```
# Use Ubuntu 22.04 as the base image
FROM ubuntu:22.04

# Set the working directory
WORKDIR /opt/starrocks

# Create a volume
VOLUME ["/opt/starrocks"]

# Install wget, tar, nano, tree, and less
RUN apt-get update \
    && apt-get install -y wget tar nano tree less openjdk-8-jdk \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Download the StarRocks tar.gz file
RUN wget https://releases.starrocks.io/starrocks/StarRocks-3.2.6.tar.gz \
    && tar -xzvf StarRocks-3.2.6.tar.gz \
    && rm StarRocks-3.2.6.tar.gz 

# Set permissions and ownership for StarRocks
RUN chmod -R 755 StarRocks-3.2.6 \
    && chown -R root:root StarRocks-3.2.6 

# Set JAVA_HOME environment variable
ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64


# Set STARROCKS_HOME
ENV STARROCKS_HOME=/opt/starrocks/StarRocks-3.2.6


# Ensure necessary directories exist
RUN mkdir -p $STARROCKS_HOME/confmkdir -p $STARROCKS_HOME/log \
    && mkdir -p $STARROCKS_HOME/conf


# Expose necessary ports (adjust as needed)
EXPOSE 8030 8040 8050 8060 8070 8080

# Start the Front-end and Back-end services
CMD ["/bin/bash", "-c", "/opt/starrocks/StarRocks-3.2.6/fe/bin/start_fe.sh && /opt/starrocks/StarRocks-3.2.6/be/bin/start_be.sh"]

# Keep the container running (Used for testing)
#CMD tail -f /dev/null

```
### 10.2  Building the Docker Image
Run these commands:
```
mkdir -p /opt/starrocks/starrocks-app/starrocksdata

docker volume create starrocksdata

docker build -t starrocks-app .
```

### 10.3 Running the Docker Image
Run this command:
```
docker run -d --name starrocks-app -v /opt/starrocks/starrocks-app/starrocksdata:/opt/starrocksvol starrocks-app


# Check it is working
docker ps -a
```

### 10.4 Use docker exec to Connect to the Container
Run this command :
```
docker exec -it starrocks-app /bin/bash

```
## 11. Rocky Linux Dockerfile
Run this command :
```
#================#
# Rockylinux:9.3 #
#================#

# Use Rockylinux:9.3-minimal as the base image
#FROM rockylinux:9.3-minimal
FROM rockylinux:9.3

# Set the working directory
WORKDIR /opt/starrocks-tmp

# Create a volume
#VOLUME ["/opt/starrocks"]

# Install wget, tar, nano, tree, and less
RUN dnf update -y \
    && dnf install -y wget tar nano tree less 

# Install MySQL
RUN dnf install mysql -y

# Download the StarRocks tar.gz file
RUN wget https://releases.starrocks.io/starrocks/StarRocks-3.2.3.tar.gz \
    && tar -xzvf StarRocks-3.2.3.tar.gz 
#    && rm StarRocks-3.2.3.tar.gz

# Set permissions and ownership for StarRocks
RUN chmod -R 755 StarRocks-3.2.3 \
    && chown -R root:root StarRocks-3.2.3

# Rename the extracted directory and move it to /opt
RUN mv /opt/starrocks-tmp/StarRocks-3.2.3 /opt/starrocks-tmp/StarRocks \
    && mv /opt/starrocks-tmp/StarRocks /opt/starrocks


# Create a directory for Java
RUN mkdir -p /opt/java

# Copy the Oracle Java JDK RPM file to the created directory
COPY jdk-17_linux-x64_bin.rpm /opt/java/

# Install Java using the RPM file
RUN rpm -ivh /opt/java/jdk-17_linux-x64_bin.rpm

# Create folder :
RUN mkdir -p /opt/starrocks/be/storage

# Create folder : 
RUN mkdir -p /opt/starrocks/fe/meta


# Set JAVA_HOME environment variable
ENV JAVA_HOME /usr/java/jdk-17

# Set STARROCKS_HOME
ENV STARROCKS_HOME=/opt/starrocks


# Ensure necessary directories exist
RUN mkdir -p $STARROCKS_HOME/conf \
    && mkdir -p $STARROCKS_HOME/log \
    && mkdir -p $STARROCKS_HOME/confmkdir

# Expose necessary ports (adjust as needed)
EXPOSE 8030 8040 8050 8060 8070 8080

# Start the Front-end and Back-end services
#CMD ["/bin/bash", "-c", "/opt/starrocks/StarRocks-3.2.6/fe/bin/start_fe.sh & && /opt/starrocks/StarRocks-3.1.11/be/bin/start_be.sh &"]

# Keep the container running (needed for testing)
CMD tail -f /dev/null

```
### 11.1 Create the container
Run these commands :
```
mkdir -p /opt/starrocks/starrocks-app/starrocksdata

docker volume create starrocksdata

docker build -t starrocks-roc .

docker run -d --name starrocks-roc -v /opt/starrocks/starrocks-roc/starrocksdata:/opt/starrocksvol starrocks-roc


docker exec -it starrocks-roc /bin/bash

cd /opt/starrocksvol
```
### 11.2 The cleanup (Destroy the container and Image)
Run these commands
```
docker volume rm starrocksdata

docker container ls -a

docker container stop starrocks-roc

docker container rm starrocks-roc
# OR
docker kill starrocks-roc

docker ps -a

docker image ls


docker image rm 
# OR
docker image rm -f  74ab221862a8
```


## Bibliography
https://kodekloud.com/blog/how-to-build-a-docker-image/ \
https://chtc.cs.wisc.edu/uw-research-computing/docker-build \
https://devopscube.com/build-docker-image/ \
https://blog.purestorage.com/purely-educational/how-to-create-a-docker-image-build-and-run-your-own-containers/ \
https://stackify.com/docker-build-a-beginners-guide-to-building-docker-images/


