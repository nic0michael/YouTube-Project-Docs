---
title: "Install Docker Swarm and use Docker Stack to deploy apps to Cluster"
date: 2023-01-14T07:47:02Z
draft: false
---
## 1. Prerequisites
### You will need :
1. Two or more VMs running Ubuntu 22.04 Linux Server
2. Install Docker on all these VMs
3. You will need a Linux Workstation or a Windows PC with PUTTY installed

## 2. Installing Docker
```
sudo apt update

sudo apt install docker.io
```

Download docker-machine to /usr/local/bin
```
curl -L https://github.com/docker/machine/releases/download/v0.3.0/docker-machine_linux-amd64 > /usr/local/bin/docker-machine
chmod +x /usr/local/bin/docker-machine
```


### 2.1 Confirm docker is running
```
docker --version
```

### 2.2 Test Docker
```
sudo docker run hello-world

```
Check if you have the hello-world image
```
sudo docker images
```

Show all images that have been pulled
```
sudo docker ps -a
```
Show all images in a running state
```
sudo docker ps
```

### 2.2 Create the docker group.
```
sudo groupadd docker
```
Add your user to the docker group.
```
sudo usermod -aG docker ${USER}
```
You would need to loog out and log back in so that your group membership is re-evaluated or type the following command:
```
su -s ${USER}

```
Set  ownership and permissions using the following commands:
```
sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
sudo chmod g+rwx "$HOME/.docker" -R
```
**IMPORTANT:** This also needs to be done:
```
sudo chmod 666 /var/run/docker.sock
```
## 3. Create Worker machines using Docker-Machine
repeat the above **Section 2.** for each or the worker node machines to install and setup docker on them.

## 4. Creating a Docker Swarm Cluster
We need to initialize the cluster with the IP address in order that this node can be the Manager node.
The servers IP address is : 10.154.2.93
```
docker swarm init --advertise-addr 10.154.2.93
```
Expect to get : 
```
docker swarm join --token SWMTKN-1-2ra2vivtylyd59o7in49mtsqg5j6fu007cvaxchbxc36cvxwlk-3fcn18o81v2shhabszu161e16 10.154.2.93:2377
```
You need to save this command in the above results
```
docker swarm join --token SWMTKN-1-2ra2vivtylyd59o7in49mtsqg5j6fu007cvaxchbxc36cvxwlk-3fcn18o81v2shhabszu161e16 10.154.2.93:2377
```

If you did not save this output connect to the Master Node Server and run this command
```
docker swarm join-token worker
```
You will now get:
```
docker swarm join --token SWMTKN-1-2ra2vivtylyd59o7in49mtsqg5j6fu007cvaxchbxc36cvxwlk-3fcn18o81v2shhabszu161e16 10.154.2.93:2377
```

This command will show that swarm is active
```
docker info 
```
Expect to get something like this:
```

Client:
 Context:    default
 Debug Mode: false

Server:
 Containers: 5
  Running: 5
  Paused: 0
  Stopped: 0
 Images: 5

 ......... We are truncating this output to save space

 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: active <============================= Swarm is active
  NodeID: imv9povb1zrehcpzu4n02a6vt <======== the node ID
  Is Manager: true <========================= Node Is Manager
  ClusterID: x1u1s9w5pk8uul9e6t7dvcvcv <===== the Cluster ID
  Managers: 1 <============================== The number of Managers is 1
  Nodes: 1 <================================= the number of nodes in this Cluster is 1
  Default Address Pool: 10.0.0.0/8  
  SubnetSize: 24
  Data Path Port: 4789
  ............. truncating this output
  Node Address: 10.154.2.109 <=============== The address of this Manager is: 10.154.2.109
  Manager Addresses:
   10.154.2.109:2377
 Runtimes: io.containerd.runc.v2 io.containerd.runtime.v1.linux runc
-- truncating this output
 Kernel Version: 5.15.0-57-generic
 Operating System: Linux Mint 21
 OSType: linux
 Architecture: x86_64
 CPUs: 8
 Total Memory: 15.51GiB
 Name: dell-nm
 Docker Root Dir: /var/lib/docker
```

### 4.1 Add Worker Node2 and Node3 to the cluster
```
ssh user@Node2IPaddress
sudo su -
```
### Paste the saved command into the terminal
```
docker swarm join --token SWMTKN-1-2ra2vivtylyd59o7in49mtsqg5j6fu007cvaxchbxc36cvxwlk-3fcn18o81v2shhabszu161e16 10.154.2.93:2377
```

Repeat this for Node3 

## 4.2 Deploy a Local Registry
```
docker service create --name registry --publish published=5000,target=5000 registry:2
```

Verify that the registry was successfully deployed by issuing the command:
```
curl http://localhost:5000/v2/
```

## 5. Creating Services on the Docker Swarm
### 5.1 Login to Master Node server
```
ssh user@MasterNodeIPaddress
sudo su -
```
### There are two types of Service Modes
1. **Replicated Mode** (equivalent to Kubernetes Deployment can deploy multiple instances to each nodes)
2. **Global Mode** (equivalent to Kubernetes Deamonset deploys 1 instance on all nodes)

### 5.1 Creating a Service (like a Kubernetes Deployment)
This command will show you all the info about the **docker service create** command
```
docker service create --help | less
# also look at
docker service update --help | less
```

### 5.2 Create a simple nginx service
We have different options to specify how we create this service:

#### 5.2.1 Here we will not specify name so the NAME_OF_INSTANCE will be random text
```
docker service create nginx

docker service ls
docker service ps NAME_OF_INSTANCE
docker service ps romantic_williamson
#docker service rm NAME_OF_INSTANCE
```

#### 5.2.2 To update a service to have port forwarding to port 8080
```
docker service update --publish-add 8080:80 romantic_williamson
docker service ls
docker service ps romantic_williamson
curl 10.154.2.93:8080

```
##### Open is browser :
http://any_node_server_ip_address:8080 \
Or \
http://10.154.2.93:8080

#### 5.2.3 To have 2 Replicas and specify the name as mynginx
```
docker service update --replicas 2 --force romantic_williamson
docker service ls
docker service ps romantic_williamson

```
#### 5.2.4 To downgrade service to 1 Replica and specify the name as mynginx
```
docker service update --replicas 1 --force romantic_williamson
docker service ls
docker service ps romantic_williamson

```

#### 5.2.5 To Delete the service

```
docker service rm romantic_williamson 
docker service ls

```

## 6. Using Docker Stack to deploy an app in the Docker Swarm Cluster

Docker Stack is like a Kubernetes Manifest or Resource \
To work with Docker Stack you need to write a Docker-compose YAML file \
You will then deploy your compose YAML file using Docker Stack



### 6.1 You need to run all these commands on your Docker Swarm Master Node
```
ssh nickm@10.154.2.93
# switch user to root
sudo su -
```
### 6.2 The Hello World example
Create file docker-compose.yaml
```
nano docker-compose.yaml
```
Put this in the file:
```
version: '3'
services:
  hello_world:
    image: ubuntu
    command: [/bin/echo, 'Hello world']
```

To deploy this
```
docker stack deploy --compose-file docker-compose.yaml helloworld
```

To list the tasks in the service
```
docker stack ps helloworld
```
If you have errors run this
```
docker stack ps --no-trunc helloworld 
```
### 6.3 A simple example deploying nginx

To List stacks
```
docker stack ls
```

Create a simple Docker Compose Yaml file
```
nano docker-compose.yaml

```
put this is the file
```
version: '3'

services:
  web:
    image: nginx
    ports:
      -"8080:80"

```
In this docker compose file we did not specify the number of replicas \
By default it will deploy with mode is replicas and only one replica \
Mode replicas is equivalent to Kubernetes nodeport


To deploy this
```
docker stack deploy --compose-file docker-compose.yaml mynginx
```

To verify this was deployed
```
docker stack ls
```

to list the tasks in the service
```
docker stack ps mynginx
```
to see the services (with replicas =1)
```
docker stack services mynginx
```

Open is browser :
http://any_node_server_ip_address:8080 \
http://10.154.2.93:8080

Or
```
curl http://any_node_server_ip_address:8080
curl http://10.154.2.93:8080
```

To remove this Stack
```
docker stack rm mynginx

# confirm this was removed
docker stack ls
```
Adding Replicas to our Docker Compose Yaml file
Edit this file:
```
nano docker-compose.yaml

```
put this is the file
```
version: '3'

services:
  web:
    image: nginx
    ports:
      -"8080:80"
    deploy:
      replicas: 2
```
Now deploy this
```
docker stack deploy --compose-file docker-compose.yaml mynginx
```

To verify this was deployed as 1 service
```
docker stack ls
```

to list the tasks in the service (expect 2)
```
docker stack ps mynginx
```
to see the services (with replicas =2)
```
docker stack services mynginx
```

Open is browser :
http://any_node_server_ip_address:8080 \
http://10.154.2.93:8080

Or
```
curl http://any_node_server_ip_address:8080
curl http://10.154.2.93:8080
```

### 6.3 A more significat deployment of a Python Microservice

The following example for this video comes from :\
https://docs.docker.com/engine/swarm/stack-deploy/

### 6.3.1 Create Python Application
```
nano app.py
```

Put this is the file:
```
from flask import Flask
from redis import Redis

app = Flask(__name__)
redis = Redis(host='redis', port=6379)

@app.route('/')
def hello():
    count = redis.incr('hits')
    return 'Hello World! I have been seen {} times.\n'.format(count)

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000, debug=True)
```

```
nano requirements.txt
```

Put this is the file:
```
flask
redis
```

### 6.3.2 Create Dockerfile
```
nano Dockerfile
```

Put this is the file:
```
# syntax=docker/dockerfile:1
FROM python:3.4-alpine
ADD . /code
WORKDIR /code
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

### 6.3.3 Create Docker Compose YAML file
```
nano docker-compose.yml
```

Put this is the file:
```
version: "3.9"

services:
  web:
    image: 127.0.0.1:5000/stackdemo
    build: .
    ports:
      - "8000:8000"
  redis:
    image: redis:alpine
```
### 6.3.4 Deploy the YAML file to the Docker Swarm Cluster 

Run this command
```
docker stack deploy --compose-file docker-compose.yml myweb-micro-svc
```

Expect to get:
```

```

## 7. Delete the Docker Swarm Cluster
### 7.1 Make the Worker Nodes leave 
```
docker swarm leave
```

### 7.2 Make the Master Node leave the Swarm Cluster
```
docker swarm leave --force
# OR
docker swarm leave -f
```
## 8. Awesone Docker Compose samples
https://github.com/docker/awesome-compose

## 9. Bibliography

https://docs.docker.com/engine/reference/commandline/stack/

https://docs.docker.com/engine/swarm/stack-deploy/

https://www.simplilearn.com/tutorials/docker-tutorial/how-to-install-docker-on-ubuntu

https://www.dataquest.io/blog/install-and-configure-docker-swarm-on-ubuntu/

https://riptutorial.com/docker-compose

https://www.youtube.com/watch?v=DvLZtEvgUoY&t=145s
https://www.youtube.com/watch?v=YtAgMI2MMVQ
https://www.youtube.com/watch?v=z-Pck8o10bE&t=7s
