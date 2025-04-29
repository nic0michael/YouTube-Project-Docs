---
title: "Kubernetes Demo"
date: 2023-11-25T07:30:14Z
draft: false
---
**What is Kubernetes**
1. Kubernetes works with Docker containers 
2. While Docker is used to containerize applications. 
3. Kubernetes is a container orchestration tool 
4. Kubernetes manages and automates the deployment, scaling, and operation of those containers across clusters of machines. 

**Kubernetes can be automated using Ansible or Terraform**

**What is Helm?**
1. Helm is a package manager for Kubernetes that simplifies the deployment and management of applications in Kubernetes clusters.
2. Think of Helm as the "DNF" or "APT-GET" of Kubernetes
3. Companies like VMware have packages MySQL Wordpress and many more Docker containers to easily deploy to Kubernenes


## 1. Deploy to K3S cluster using 3 Manifests (YML files)

We are going to deploy the Nginx webserver in K3S Kubernetes Cluster 

**Start the following VMs**
```
100 Ubuntu93
300 Ubuntu95
301 ubuntu97
# Check they are up 
kubectl get nodes
```

**Run these commands**
```
sudo su -
mkdir /opt/kubernetes
mkdir /opt/k3s/nico-demo
cd /opt/k3s/nico-demo
```
## Before we can deploy Nginx we need to create a Namespace
A Namespace is a mechanism for isolating the deployment on one application in a Kubernetes Cluster from another application

**run this command**
```
kubectl create namespace nico-demo
```


NGINX instances are being deployed to the cluster with the following manifest
### 1.1 Create Deployment YAML #
**Create deployment yaml file**
```
nano nginx-deployment.yml
```
With this content:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 10
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
### 1.2 Install this deployment #
**run this command**
```
kubectl apply -f nginx-deployment.yml --namespace nico-demo
```

### 1.3 Verify that there are 10 pods running an instance of NGINX
**Run this command**
```
kubectl get pods -l app=nginx --output=wide --namespace nico-demo
```

### 1.4 Create the service YAML
**run this command**
```
nano nginx-service.yml
```
**Put this content into the file**
```
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

### 1.5 Apply the service
**run this command**
```
kubectl apply -f nginx-service.yml --namespace nico-demo
```

### 1.6 Check if service worked
**run this command**
```
kubectl get services --namespace nico-demo
```

expected Response
```
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.43.0.1       <none>        443/TCP   13h
nginx        ClusterIP   10.43.250.220   <none>        80/TCP    13s
```
### 1.7 Get endpoints
**run this command**
```
kubectl get endpoints --namespace nico-demo
```
expected response
```
NAME         ENDPOINTS                                              AGE
kubernetes   10.154.2.88:6443,10.154.2.93:6443,10.154.2.97:6443     13h
nginx        10.42.0.14:80,10.42.0.15:80,10.42.1.4:80 + 7 more...   71sm
```


### 1.8 Open an instance of nginx with curl command
run this command
```
curl http://10.42.0.14:80

and 
 
curl http://10.43.16.183
```

### 1.9 Exposing the Service #
**run this command**
```
nano service-exposed.yaml
```
Please note  apiVersion: networking.k8s.io/v1beta1 # for k3s < v1.19

**put this into file**
```
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

### 1.10 Exposing the service
run this command
```
kubectl apply -f service-exposed.yaml --namespace nico-demo
```
**Now check is service is exposed run this command**
```
curl localhost:80
```

**Now open nginx in your browser**

[http://upupa.loseyourip.com/](http://upupa.loseyourip.com/)

[leopard.loseyourip.com](leopard.loseyourip.com/)

[http://lion.loseyourip.com/](http://lion.loseyourip.com/)

[http://kudu.loseyourip.com/](http://kudu.loseyourip.com/)

### 1.11 The cleanup
**run this command**
```
kubectl delete namespace nico-demo
```

## 2. Helm Demo

### 2.1 Create namespaces
```
kubectl create namespace loyaltyplus
kubectl create namespace sanparks
kubectl create namespace mica
```

### 2.2 Find VmWare Bitnami  Helmchart for Wordpress
Search Artefacthub for helmchart 
[https://artifacthub.io/](https://artifacthub.io/)

We found it:
[https://artifacthub.io/packages/helm/bitnami/wordpress](https://artifacthub.io/packages/helm/bitnami/wordpress)



Add the Bitnami Repo
```
helm repo add bitnami https://charts.bitnami.com/bitnami
```

### 2.3 Test wordpress template latest not --version 18.1.17
```
helm template my-wordpress bitnami/wordpress

```
### 2.4 Install wordpress in our namespaces
```
helm install wordpress-release bitnami/wordpress -n loyaltyplus
helm install wordpress-release bitnami/wordpress -n sanparks
helm install wordpress-release bitnami/wordpress -n mica

# Listing helm charts in namespaces
helm list -n loyaltyplus
helm list -n sanparks
helm list -n mica

# Listing Kubernetes deployments in cluster by namespace
kubectl get all -o wide -n loyaltyplus
kubectl get all -o wide -n sanparks
kubectl get all -o wide -n mica

kubectl get pods --all-namespaces
```
### 2.5 To update the code we Upgrade the Helmchart
```
helm upgrade wordpress-release bitnami/wordpress -n loyaltyplus
```

### 2.6 View the history
```
helm list -n loyaltyplus
helm history wordpress-release -n loyaltyplus
```
### 2.7 Rollback failed deployment
```
helm rollback wordpress-release 1 -n loyaltyplus
helm history wordpress-release -n loyaltyplus
```
### 2.8 The cleanup
```
helm delete wordpress-release -n loyaltyplus
helm delete wordpress-release -n sanparks
helm delete wordpress-release -n mica

kubectl delete namespace loyaltyplus
kubectl delete namespace sanparks
kubectl delete namespace mica

```
