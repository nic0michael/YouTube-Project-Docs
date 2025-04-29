---
title: "K3S Kubernetes Workshop Using Proxmox Server"
date: 2025-03-07T19:22:18Z
draft: false
---

## Why is Kubernetes more valuable than Docker in the industry?
1. **Industry Adoption** – Kubernetes is the industry standard for cloud-native applications. 
2. **Production-Ready** – Kubernetes offers built-in load balancing, self-healing, and networking.
3. **Scalability & Orchestration** – Kubernetes automates container deployment, scaling, and management.  

## Why is Kubernetes skills more valuable than Docker skils?
1. **Beyond Single Containers** – Kubernetes manages and orchestrates multiple containers across clusters.  
2. **Enterprise Demand** – Most companies prioritize Kubernetes for scalable, cloud-native deployments.  
3. **Full Ecosystem Control** – Kubernetes handles networking, storage, scaling, and self-healing.

## 1. Prerequisite : Create Training Cluster
Instructions can be foune here : \
**[Create  Small Kubernetes Cluster](https://github.com/nic0michael/K3S_Auto_Cluster_Generator/tree/master/Small-Cluster)**


This is the simplest way to use this tool using only two Shell Scripts

This works with :
1. **Ubuntu Server.**
2. **Rocky Linux & CentOS Server 9.**
3. **And now Alpine Linux Minimal Server.** **https://youtu.be/biI2VAsqStE**

**You will open two terminals running ssh to each of the Node Servers**

## 1.1 On the Master Node VM Server 10.154.2.41
We create the Kubernetes Cluster now

**run these commands:**
```bash
ssh nickm@10.154.2.41

sudo su -

apt update

mkdir /opt/k3s-master -p

cd /opt/k3s-master/

# Open browser here https://github.com/nic0michael/K3S_Auto_Cluster_Generator/blob/master/Small-Cluster/Master-Node.sh
nano Master-Node.sh

sudo chmod 775 Master-Node.sh

# To create the Master Node in the K3S Kubernetes Cluster
./Master-Node.sh

# This takes 3 to 4 minutes

# To get the new Security Token for this cluster
cat /var/lib/rancher/k3s/server/node-token
# Save this Security Token in a notepad 

# Also save this servers IP Address
ip a

# Check for Kubernetes Nodes
kubectl get nodes
```

## 1.2 On the WorkerNode VM Server 10.154.2.42
We create the Kubernetes Cluster now

**run these commands:**
```bash
ssh nickm@10.154.2.42

sudo su -

apt update

apt install nano

mkdir /opt/k3s-worker -p

cd /opt/k3s-worker/

# open browser here : https://github.com/nic0michael/K3S_Auto_Cluster_Generator/blob/master/Small-Cluster/Worker-Node.sh
nano Worker-Node.sh


# We will edit this script and change these values with those of the Master Node Server: 
k3s_token="K105db82e39a8ec3a05de569a7a2e4ffdf2fa3c1db21f87fad5b22e764af61d0d96::server:e54cb4ab0d4026b645c8156477ac7220"
k3s_url="https://10.154.2.85:6443"

# run these commands
nano Worker-Node.sh

sudo chmod 775 Worker-Node.sh

# To create the Worker Node in the K3S Kubernetes Cluster
./Worker-Node.sh

# This takes 3 to 4 minutes
```
## 1.3 Destroy the Cluster
```bash
/usr/local/bin/k3s-killall.sh

/usr/local/bin/k3s-uninstall.sh
```

---
## 2. Prerequisite : Deploy Nginx in kubernetes cluster
We are going to deploy the Nginx webserver in K3S Kubernetes Cluster

**We recomend to connect to th Master Node server using ssh* \
**Run these commands:**
```bash
ssh nico@10.154.2.41

sudo su -

mkdir /opt/k3sworkshop -p

cd /opt/k3sworkshop
```

### 2.1 Create Deployment YAML #
**Create deployment yaml file**
```bash
nano nginx-deployment.yml
```
**With this content:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2
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
```

### 2.2 Create the service YAML
**Run this command**
```bash
nano nginx-service.yml
```
**Put this content into the file**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    run: nginx
spec:
  ports:
    - port: 80
  selector:
    app: nginx
```

### 2.3 Create Ingress to exposing the Service 
**Run this command**
```bash
nano service-exposed.yaml
```
Please note  apiVersion: networking.k8s.io/v1beta1 # for k3s < v1.19

**put this into file**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx
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



### 2.4 Before we can deploy Nginx we need to create a Namespace
A Namespace is a mechanism for isolating the deployment on one application in a Kubernetes Cluster from another application

**Run this command**
```bash
kubectl create namespace nginx
```

### 2.5 Applying the Kubernetes Manifests to the Kubernetes Cluster

```
### 2.5.1 Install the Deployment 
run this command
```bash
kubectl apply -f nginx-deployment.yml --namespace nginx

# Check the deployment
kubectl get pods -l app=nginx --output=wide --namespace nginx
```
### 2.5.2 Apply the service
**Run this command**
```bash
kubectl apply -f nginx-service.yml --namespace nginx
```
### 2.5.3 Apply the Ingress to expose the Service

**Run this command**
```bash
kubectl apply -f service-exposed.yaml --namespace nginx
# Check is service is exposed run this command
curl localhost:80
```
### 2.5.4 Open in the Browser
**[Master Node : http://10.154.2.41](http://10.154.2.41)**

**[Master Node : http://10.154.2.42](http://10.154.2.42)**

---
## 3. The Kubernetes Workshop
### 3.1 Get the Nodes
```bash
kubectl get nodes
```
### 3.2 Get the namespaces
```bash
kubectl get namespaces
```
### 3.2 Get Deployments
```bash
kubectl get deployments --namespace nginx

kubectl get deployments --all-namespaces
```
### 3.3 Get pods
```bash
kubectl get pods -l app=nginx --output=wide --namespace nginx

kubectl get pods -l --output=wide --all-namespaces
```

### 3.4 Get services
```bash
kubectl get services --namespace nginx

kubectl get svc -n nginx

kubectl get services --all-namespaces

```

### 3.5 Get Endpoints
```bash
kubectl get endpoints --namespace nginx
```

### 3.6 Scaling Pods
```bash
kubectl get all --namespace nginx

kubectl scale deployment --replicas=4 deployment.apps/nginx-deployment


kubectl get pods -l app=nginx --output=wide --namespace nginx
```

### 3.7 Describing PODs
```bash
kubectl get pods -l app=nginx --output=wide --namespace nginx

kubectl describe pod <pod-name> -n nginx
```

### 3.8 Accessing Logs
```
kubectl get pods -l app=nginx --output=wide --namespace nginx

kubectl logs <pod-name> -n nginx
```
### 3.9 Debugging PODs
```
kubectl get pods -l app=nginx --output=wide --namespace nginx

kubectl exec -it <pod-name> -n nginx -- /bin/sh
```
---


### 4. The Cleanup (Do this when you have completed te workshop)
**Run this command**
```bash
kubectl delete namespace nginx
```
