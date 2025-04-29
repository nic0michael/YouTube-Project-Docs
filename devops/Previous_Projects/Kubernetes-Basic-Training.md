---
title: "Kubernetes Basic Training"
date: 2025-03-27T08:39:27Z
draft: false
---
## 1. Connect to the Server Node
```sh
ssh nico@192.168.1.52

sudo su -

cd /opt/k3s

# show Manifests
ls -la
```
## 2. The Kubernetes API (kubectl)
Kubernetes provides us with an API for creating and managing the Deployments, PODs , Services and Ingresses and much more \
If you don’t want to install this locally the run this in the cluster

### 2.1 Creating a Namespace nginx
```sh
kubectl create namespace nginx
```
### 2.2 Listing the Clusters Nodes
Run the command :
```sh
kubectl get nodes
```
### 2.3 Deploying the Application as POD/s
```sh
kubectl apply -f nginx-deployment.yml --namespace nginx

# Check the deployment
kubectl get pods -l app=nginx --output=wide --namespace nginx
```
### 2.4 Creating the Service
```sh
kubectl apply -f nginx-service.yml --namespace nginx

# List services
kubectl get svc --namespace nginx
```
### 2.5 Creating the Ingress
```sh
kubectl apply -f service-exposed.yaml --namespace nginx
# Check is service is exposed run this command
curl localhost:80

```
### 2.6 Listing the PODs
```sh
kubectl get pod --namespace nginx
```
### 2.7 Looking at the Logs of a Pod
```sh
kubectl get pods -l app=nginx --output=wide --namespace nginx

kubectl logs <pod-name> -n nginx
```

## 3. K9S Demo
```sh
k9s --kubeconfig /etc/rancher/k3s/k3s.yaml
```

### 3.1 List Namespaces
```sh
:ns
```

### 3.2 List PODs
```sh
:pod
```

### 3.2.1 List Log of a POD
```sh
# Navigate to pod press L
l
```

