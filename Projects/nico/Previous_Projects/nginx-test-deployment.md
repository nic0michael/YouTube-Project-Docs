---
title: "Nginx Deployment using Manifests"
date: 2023-04-07T11:46:32Z
draft: false
---
## 1. Deployment of Nginx using Manifests (YAML files)

```
sudo su -

cd /root/k3s/k3s-manifests/nginx
```

We are going to deploy the Nginx webserver in K3S Kubernetes Cluster
## Before we can deploy Nginx we need to create a Namespace
A Namespace is a mechanism for isolating the deployment on one application in a Kubernetes Cluster from another application

### 1.1 create Namespace nico-nginx
```
kubectl get namespaces
kubectl create namespace nico-nginx
kubectl get namespaces
```


### 1.2 Create deployment manifest file: nginx-deployment.yml
```
nano nginx-deployment.yml
```
Put this into the file:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 4
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
### Apply the Deployment Manifest file nginx-deployment.yml
```
kubectl apply -f nginx-deployment.yml --namespace nico-nginx

kubectl get pods --namespace nico-nginx
kubectl get pods -o wide -n nico-nginx

```


---

### 1.3 Create service manifest file : nginx-service.yml
```
nano nginx-service.yml

```

Put this into the file:
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
### Apply the Service Manifest file nginx-service.yml
```
kubectl apply -f nginx-service.yml -n nico-nginx

kubectl get service -n nico-nginx
kubectl get endpoints -n nico-nginx
```

Open internally in cluster one endpoint
```
curl <endpointIP>:80
curl 10.42.0.82:80 // specify your own url
```


---

### 1.4 create service exposing manifest file : service-exposed.yaml
```
nano service-exposed.yaml
```
Put this into the file
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

### Apply the Service Exposing Manifest file service-exposed.yaml
Run this command

```
kubectl apply -f service-exposed.yaml --namespace nico-nginx
```

Now check is service is exposed run this command
```
curl localhost:80
```

### 1.8 Open in browser:

Now open nginx in your browser

**Using IP Addresses** \
[http://10.154.2.89](http://10.154.2.89) \
[http://10.154.2.93](http://10.154.2.93) \
[http://10.154.2.95](http://10.154.2.95) \
[http://10.154.2.97](http://10.154.2.97) 

**Using FQDNs (Fully Qualified Domain Names)** \
[http://upupa.loseyourip.com/](http://upupa.loseyourip.com/)] \
[http://kudu.loseyourip.com/](http://kudu.loseyourip.com/) \
[http://lion.loseyourip.com/](http://lion.loseyourip.com/) \
[http://leopard.loseyourip.com/](http://leopard.loseyourip.com/)

### 1.9 The cleanup

Run these commands
```
kubectl delete namespace nico-nginx

kubectl get namespaces
kubectl get pods --all-namespaces
```


## Bibliography
[How to deploy NGINX Ingress Controller on Kubernetes using kubectl](https://platform9.com/learn/v1.0/tutorials/nginix-controller-via-yaml) \
[Deploy a simple nginx app with Kubernetes on Google Cloud Platform (GCP)](https://medium.com/@mremreozan/deploy-a-nginx-server-with-kubernetes-on-google-cloud-platform-gcp-e906b110dcb6)
