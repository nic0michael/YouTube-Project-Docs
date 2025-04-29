---
title: "Delpoy Apache2 Web-server in Kubernetes Cluster"
date: 2023-10-28T11:24:53Z
draft: false
---


## 1. Create the Kubernetes manifest
Run this command:
```
nano apache.yaml
```
Put this in this file:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test-deployment
  labels:
    app: apache
spec:
  selector:
    matchLabels:
      app: test
  replicas: 1 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: test
    spec:
      containers:
      - name: test
        image: ubuntu:20.04
        command: ["/bin/sh", "-c", "apt update -y && apt install -y apache2 && service apache2 start && tail -f /dev/null"]
        ports:
        - name: http
          containerPort: 80
        env:
        - name: DEBIAN_FRONTEND
          value: noninteractive
---
apiVersion: v1
kind: Service
metadata:
  name: apache-service
spec:
  selector:
    app: test
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
    targetPort: http
    # nodePort: 30001
```

## 2. Create Namespace httpd
Run this command:
```
kubectl create namespace httpd
```

## 3. Apply the Kubernetes Manifest:
Run this command:
```
kubectl apply -f apache.yaml -n httpd
```

## 4. View Deployment and Service
Run this command
```
kubectl get all -o wide -n httpd
```
Extect to get:
```
NAME                                  READY   STATUS    RESTARTS       AGE   IP            NODE                   NOMINATED NODE   READINESS GATES
pod/test-deployment-8488c9f45-5lzrz   1/1     Running   2 (101m ago)   13h   10.42.0.183   upupu.loseyourip.com   <none>           <none>

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE   SELECTOR
service/apache-service   LoadBalancer   10.43.226.31   <pending>     80:30046/TCP   13h   app=test

NAME                              READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES         SELECTOR
deployment.apps/test-deployment   1/1     1            1           13h   test         ubuntu:20.04   app=test

NAME                                        DESIRED   CURRENT   READY   AGE   CONTAINERS   IMAGES         SELECTOR
replicaset.apps/test-deployment-8488c9f45   1         1         1       13h   test         ubuntu:20.04   app=test,pod-template-hash=8488c9f45
```

## 4. Open Apache Web server using CURL given our Kubernete primary node server IP is 10.154.2.89
Run this command
```
curl 10.154.2.89:30046
```
Expect:
```
#PLEASE NOTE WE HAVE STRIPPED OFF MOST OF THE OUTPUT TO SAVE SPACE HERE

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
  <!--
    Modified from the Debian original for Ubuntu
    Last updated: 2016-11-16
    See: https://launchpad.net/bugs/1288690
  -->
  <head>

``` 


## 5. Open Apache Web server in your browser
Open this link:
[http://10.154.2.89:30046](http://10.154.2.89:30046)



## 6. Source Code
[Get source code here](https://github.com/nic0michael/Apache2-in-K3S)
