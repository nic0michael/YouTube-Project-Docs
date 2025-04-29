---
title: "Docker or Kubernetes Which ? When ? in Proxmox"
date: 2024-11-23T06:44:49Z
draft: false
---
## 1. Our requirement :
Is to get a Ubuntu Server as a container

## 2. How we will do this :
What we will do is Start with docker show 3 ways to do this
Then we will do this with Kubernetes first a single pod then show 2 ways to scale up this requirement

Finally we will look at a High Availability Kubernetes with nodes in Berlin Germany, London England, and New York

We will do all this in our Proxmox server


## 3. Here’s a quick comparison to help decide:  

### **Docker**  
1. **Best for**: Individual application containerization and smaller-scale projects.  
2. **Simplicity**: Easy to use for running and managing containers.  
3. **Resource Requirements**: Lightweight, minimal overhead.  
4. **Platform Independent**: Ensures consistent behavior across development, testing, and production environments.  
5. **Use Case**: Local development, testing, and single-node deployments.  


### **Kubernetes**  
1. **Best for**: Orchestrating containers across multiple nodes in large-scale, production-grade systems.  
2. **Scalability**: Automates scaling, networking, and load balancing. 
3. **Automation**: Handles networking, load balancing, and resource management seamlessly.5. **Ideal for Microservices**: Perfect for complex, 4. distributed systems requiring multi-container coordination. 
5. **Use Case**: Enterprise-level applications needing high availability, fault tolerance, and microservices orchestration.  

## 4. Creating a simple Ubuntu server using Docker
### ** 4.1 Created from Docker Commands**

1. **Pull the Ubuntu Image**:
 ```bash
 docker pull ubuntu:latest
 ```

2. **Run a Container with Bash Terminal**:
```bash
docker run -it ubuntu:latest bash
```

3. TRunning commands in the container :
```bash
ls -la /home

apt update

apt install curl
```
---
### 4.2 Using a docker compose file to create the same  container
Run this Command :
```bash
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
# Start the conatiner in detached mode
docker-compose up -d
# To access the container, use:
docker exec -it ubuntu_server bash
 
# Now Inside the container run these commands:
lsb_release -a

ls -la /opt

touch test1.txt
```
---
## 5. Kubernetes
### 5.1 create the cluster
This is beyond the scope of this video however for completeness sake we have included **Appendix A** with the procedure
---
### 5.2 From the Primary Master Node Create a Kubernetes manifest to create the same  container
Run thie command :
```
nano nginx-manifest.yaml
```
Put this in the file

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: nginx
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 3
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx
  namespace: nginx
  labels:
    run: nginx
spec:
  ports:
    - port: 80
  selector:
    app: nginx
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx
  namespace: nginx
  annotations:
    ingress.kubernetes.io/ssl-redirect: "false"
spec:
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx
            port:
              number: 80

```

Run these commands :
```
kubectl create namespace nginx

#  Apply the manifest:
kubectl apply -f nginx-manifest.yaml -n nginx

# Confirm the resources were created:
kubectl get pods -n nginx
kubectl get all -n nginx

# we now have 3 replicas (instances) running we want 5

# Scale up the instances to 5 using this command :
kubectl scale deployment nginx-deployment --replicas=5 --namespace=nginx

# Confirm that we have 5 replicas resources were created:
kubectl get pods -n nginx
kubectl get all -n nginx

```
---





## Appendix A Creating the Kubernetes Cluster
## 1 create the cluster

### 1.1 The Primary Master node London England
Run these commands :
```
sudo su -

mkdir /opt/k3s -p

cd /opt/k3s/

nano Primary-Master-Node-ip-60.sh
```
Put this in the file:
```
#! /bin/bash
# Primary-Master-Node-ip-80.sh
#######################
# Primary Master Node #
#######################
# IP Address: 10.154.2.60
# FQDN: London
# User ID: nickm
# K3s Token: K10e132fef7618115b243c442ab7841f4dd093f73a266d972a97a057b8d94461d9f::server:87e8469573f644feda130f74d3c05d51
# 
node_name="London"
curl -sfL https://get.k3s.io | K3S_NODE_NAME=${node_name} sh -s - server --cluster-init --write-kubeconfig-mode=644
echo "Please run this command and update token in config.yaml"
echo "cat /var/lib/rancher/k3s/server/node-token"
```
Run these commands : 
```
chmod 775 Primary-Master-Node-ip-60.sh

./Primary-Master-Node-ip-60.sh

# get the Security Token
cat /var/lib/rancher/k3s/server/node-token
# use this to update the following Shell Scripts
```
To verify this Node and other Nodes were created run this command on this server evry time :
```
kubectl get nodes
```

### 1.2 The Second  Master node Berlin Germany
Run these command 
```
sudo su -

mkdir /opt/k3s -p

cd /opt/k3s/

nano Second-Master-Node-ip-61.sh
```
Put this in the file:
```
#! /bin/bash
# Second-Master-Node-ip-61.sh
#######################
# Second Master Node #
#######################
# IP Address: 10.154.2.61
# FQDN: Berlin
# User ID: nickm
# Primary Master Node IP Address: 10.154.2.60
# K3s Token: K1099-PLEASE-REPLACE-THIS-WITH-THE-SERVERS-TOKEN-6a22c
# 
k3s_token="K1099-PLEASE-REPLACE-THIS-WITH-THE-SERVERS-TOKEN-6a22c"
k3s_url="https://10.154.2.60:6443"
node_name="Berlin"
curl -sfL https://get.k3s.io | K3S_TOKEN=${k3s_token} K3S_NODE_NAME=${node_name} sh -s - server --server ${k3s_url} --write-kubeconfig-mode=644

echo "Please run this command and update token in config.yaml"
echo "cat /var/lib/rancher/k3s/server/node-token"
```

Run these commands
```
chmod 775 Second-Master-Node-ip-61.sh

./Second-Master-Node-ip-61.sh

```
### 1.3 The Third Master node New York
Run these commands
```
sudo su -

mkdir /opt/k3s -p

cd /opt/k3s/

nano Third-Worker-Node-ip-93.sh
```
Put this in the file:
```
#! /bin/bash
# Third-Master-Node-ip-62.sh
#######################
# Third Master Node #
#######################
# IP Address: 10.154.2.62
# FQDN: New_York
# User ID: nickm
# Primary Master Node IP Address: 10.154.2.60
# K3s Token: K1099-PLEASE-REPLACE-THIS-WITH-THE-SERVERS-TOKEN-6a22c
# 
k3s_token="K1099-PLEASE-REPLACE-THIS-WITH-THE-SERVERS-TOKEN-6a22c"
k3s_url="https://10.154.2.60:6443"
node_name="New_York"
curl -sfL https://get.k3s.io | K3S_TOKEN=${k3s_token} K3S_NODE_NAME=${node_name} sh -s - server --server ${k3s_url} --write-kubeconfig-mode=644

echo "Please run this command and update token in config.yaml"
echo "cat /var/lib/rancher/k3s/server/node-token"
```

Run these commands
```
chmod 775 Third-Worker-Node-ip-93.sh

./Third-Worker-Node-ip-93.sh

```


## 1.4 The Worker node
Run these commands
```
sudo su -

mkdir /opt/k3s -p

cd /opt/k3s/

nano First-Worker-Node-ip-63.sh
```

Put this in the file:
```
#! /bin/bash
# First-Worker-Node-ip-63.sh
#######################
# First Worker Node #
#######################
# IP Address: 10.154.2.63
# FQDN: Worker
# User ID: nickm
# Primary Master Node IP Address: 10.154.2.60
# 
k3s_token="K1099-PLEASE-REPLACE-THIS-WITH-THE-SERVERS-TOKEN-6a22c"
k3s_url="https://10.154.2.60:6443"
node_name="Worker"
curl -sfL https://get.k3s.io | K3S_URL=${k3s_url} K3S_TOKEN=${k3s_token} K3S_NODE_NAME=${node_name} sh -
```

Run these commands
```
chmod 775 First-Worker-Node-ip-63.sh

./First-Worker-Node-ip-63.sh

```

#
