---
title: "Convert Docker Compose files to Kubernetes K3S Resources"
date: 2023-01-06T09:04:13Z
draft: false
---


## 1. Install Kompose
To install Kompose, you can follow the steps below, which are also outlined on the official website:
```
# Linux
curl -L https://github.com/kubernetes/kompose/releases/download/v1.22.0/kompose-linux-amd64 -o kompose
OR
wget https://github.com/kubernetes/kompose/releases/download/v1.22.0/kompose-linux-amd64
mv kompose-linux-amd64 kompose

# macOS
curl -L https://github.com/kubernetes/kompose/releases/download/v1.22.0/kompose-darwin-amd64 -o kompose

# make Kompose executable
chmod +x kompose

# add Kompose to Linux as a command
sudo mv ./kompose /usr/local/bin/kompose
```

### 1.1 Create Kubernetes Manifests with Kompose
The next step is to create the Kubernetes manifests by running the command below at the same level as your docker-compose.yaml file.
```
kompose convert
```

## 2. Use kompose to generate Kubernetes resources

### 2.1 Create a DockerCompose YAML file:
```
nano docker-compose.yml
```
use this content:
```
version: "2"

services:

  redis-master:
    image: registry.k8s.io/redis:e2e
    ports:
      - "6379"

  redis-slave:
    image: gcr.io/google_samples/gb-redisslave:v3
    ports:
      - "6379"
    environment:
      - GET_HOSTS_FROM=dns

  frontend:
    image: gcr.io/google-samples/gb-frontend:v4
    ports:
      - "80:80"
    environment:
      - GET_HOSTS_FROM=dns
    labels:
      kompose.service.type: LoadBalancer
```

### 2.2 To convert the docker-compose YAML file :
```
kompose convert
```


The output is similar to:
```
INFO Kubernetes file "frontend-service.yaml" created
INFO Kubernetes file "frontend-service.yaml" created
INFO Kubernetes file "frontend-service.yaml" created
INFO Kubernetes file "redis-master-service.yaml" created
INFO Kubernetes file "redis-master-service.yaml" created
INFO Kubernetes file "redis-master-service.yaml" created
INFO Kubernetes file "redis-slave-service.yaml" created
INFO Kubernetes file "redis-slave-service.yaml" created
INFO Kubernetes file "redis-slave-service.yaml" created
INFO Kubernetes file "frontend-deployment.yaml" created
INFO Kubernetes file "frontend-deployment.yaml" created
INFO Kubernetes file "frontend-deployment.yaml" created
INFO Kubernetes file "redis-master-deployment.yaml" created
INFO Kubernetes file "redis-master-deployment.yaml" created
INFO Kubernetes file "redis-master-deployment.yaml" created
INFO Kubernetes file "redis-slave-deployment.yaml" created
INFO Kubernetes file "redis-slave-deployment.yaml" created
INFO Kubernetes file "redis-slave-deployment.yaml" created

```

### 2.4 Now apply these files in Kubernetes:
```
export ES_CLUSTER_NAME=my-elastic
kubectl apply -f frontend-service.yaml,redis-master-service.yaml,redis-slave-service.yaml,frontend-deployment.yaml,redis-master-deployment.yaml,redis-slave-deployment.yaml

# We used
kubectl apply -f frontend-service.yaml,frontend-deployment.yaml,redis-slave-service.yaml,redis-master-service.yaml,redis-master-deployment.yaml,redis-slave-deployment.yaml
```

The output is similar to:
```
service/frontend created
service/redis-master created
service/redis-slave created
deployment.apps/frontend created
deployment.apps/redis-master created
deployment.apps/redis-slave creat
```

### 2.5 To look up what IP your service is using!
```
kubectl describe svc frontend
```


expect to get:
```
Name:                   frontend
Namespace:              default
Labels:                 service=frontend
Selector:               service=frontend
Type:                   LoadBalancer
IP:                     10.0.0.183
LoadBalancer Ingress:   192.0.2.89
Port:                   80      80/TCP
NodePort:               80      31144/TCP
Endpoints:              172.17.0.4:80
Session Affinity:       None
No events.
```

we got:
```
kubectl describe svc frontend
```
however we got:
```
Name:                     frontend
Namespace:                default
Labels:                   io.kompose.service=frontend
Annotations:              kompose.cmd: kompose convert
                          kompose.service.type: LoadBalancer
                          kompose.version: 1.22.0 (955b78124)
Selector:                 io.kompose.service=frontend
Type:                     LoadBalancer
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.43.50.84
IPs:                      10.43.50.84
Port:                     80  80/TCP
TargetPort:               80/TCP
NodePort:                 80  32481/TCP
Endpoints:                10.42.1.32:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type    Reason                Age   From                Message
  ----    ------                ----  ----                -------
  Normal  EnsuringLoadBalancer  12m   service-controller  Ensuring load balancer
  Normal  AppliedDaemonSet      12m   service-controller  Applied LoadBalancer DaemonSet kube-system/svclb-frontend-c8c311cb
```

your IP will be listed next to LoadBalancer Ingress.
```
curl http://192.0.2.89
```

we got :


```
curl http://10.43.50.84
```
you should get :
```
<html ng-app="redis">
  <head>
    <title>Guestbook</title>
    <link rel="stylesheet" href="//netdna.bootstrapcdn.com/bootstrap/3.1.1/css/bootstrap.min.css">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.12/angular.min.js"></script>
    <script src="controllers.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/0.13.0/ui-bootstrap-tpls.js"></script>
  </head>
  <body ng-controller="RedisCtrl">
    <div style="width: 50%; margin-left: 20px">
      <h2>Guestbook</h2>
    <form>
    <fieldset>
    <input ng-model="msg" placeholder="Messages" class="form-control" type="text" name="input"><br>
    <button type="button" class="btn btn-primary" ng-click="controller.onRedis()">Submit</button>
    </fieldset>
    </form>
    <div>
      <div ng-repeat="msg in messages track by $index">
        {{msg}}
      </div>
    </div>
    </div>
  </body>
</html>

```




## Bibliography

https://loft.sh/blog/docker-compose-to-kubernetes-step-by-step-migration

https://kubernetes.io/docs/tasks/configure-pod-container/translate-compose-kubernetes/

