---
title: "Nginx Test Deployment"
date: 2022-12-27T10:59:16Z
draft: false
---
We are going to deploy Nginx in K3S Kubernetes Cluster

## 1. Deploying a Service #

NGINX instances are being deployed to the cluster with the following manifest
### 1.1 Create Deployment YAML #
Create deployment yaml file
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
### 1.2 Install thisdeployment #
run this command
```
kubectl apply -f nginx-deployment.yml
```

### 1.3 Verify that there are 10 pods running an instance of NGINX
Run this command
```
kubectl get pods -l app=nginx --output=wide
```
## 2 Create the service #

### 2.1 Create the service YAML
run this command
```
nano nginx-service.yml
```
Put this content into the file
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

### 2.2 Apply the service
run this command
```
kubectl apply -f nginx-service.yml
```

### 2.3 Check if service worked
run this command
```
kubectl get services
```

expected Response
```
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.43.0.1       <none>        443/TCP   13h
nginx        ClusterIP   10.43.250.220   <none>        80/TCP    13s
```
### 2.4 Get endpoints
run this command
```
kubectl get endpoints
```
expected response
```
NAME         ENDPOINTS                                              AGE
kubernetes   10.154.2.88:6443,10.154.2.93:6443,10.154.2.97:6443     13h
nginx        10.42.0.14:80,10.42.0.15:80,10.42.1.4:80 + 7 more...   71sm
```


### 2.5 Open an instance of nginx with curl command
run this command
```
curl http://10.42.0.14:80

and 
 
curl http://10.43.16.183
```

## 3 Exposing the Service #
run this command
```
nano service-exposed.yaml
```
Please note  apiVersion: networking.k8s.io/v1beta1 # for k3s < v1.19

put this into file
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

### 2.1 Exposing the service
run this command
```
kubectl apply -f service-exposed.yaml
```
Now check is service is exposed run this command
```
curl localhost:80
```

Now open nginx in your browser

http://kudu.loseyourip.com/

and

http://lion.loseyourip.com/

