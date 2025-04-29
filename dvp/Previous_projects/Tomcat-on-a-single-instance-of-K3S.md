---
title: "Tomcat on a Single Instance of K3S"
date: 2022-12-27T11:00:43Z
draft: false
---

## If Installing in CentOS or REL
It is recommended to turn off firewalld or you cant have other Worker Nodes
```
sudo systemctl disable firewalld --now
```


## Make Single Node K3S
This will be the Master Node if you add more worker nodes

run this command
```
curl -sfL https://get.k3s.io | sh -
```



https://stackoverflow.com/questions/51717422/ip-deployment-tomcat-kubernetes
## Deploy Tomcat on Kubernetes

create manifest file
```
nano manifest_file.yaml
```
edit Manifest file:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tomcat-pod
spec:
  selector:
    matchLabels:
      run: tomcat-pod
  replicas: 1
  template:
    metadata:
      labels:
        run: tomcat-pod
    spec:
      containers:
      - name: tomcat
        image: tomcat:latest
        ports:
        - containerPort: 8080
```

apply manifest file
```
kubectl apply -f manifest_file.yaml
```

create service.yaml
```
nano service.yaml
```

add this content
```
apiVersion: v1
kind: Service
metadata:
  name: tomcat-pod
  labels:
    run: tomcat-pod
spec:
  type: NodePort
  ports:
  - port: 8080
    targetPort: 8080
  selector:
    run: tomcat-pod
```
apply service
```
kubectl apply -f service.yaml

kubectl get all
kubectl get endpoints
```
you should get these endpoints
```
NAME         ENDPOINTS         AGE
kubernetes   10.0.0.154:6443   51m
tomcat-pod   10.42.0.11:8080   9m5s
```
## Open firewall for port 8080
```
firewall-cmd --permanent --add-port=8080/tcp

firewall-cmd --reload
```

curl to endpoint in above lis of endpoints:
```
curl http://10.42.0.11:8080
```

and get
```
<!doctype html><html lang="en"><head><title>HTTP Status 404 – Not Found</title><style type="text/css">body {font-family:Tahoma,Arial,sans-serif;} h1, h2, h3, b {color:white;background-color:#525D76;} h1 {font-size:22px;} h2 {font-size:16px;} h3 {font-size:14px;} p {font-size:12px;} a {color:black;} .line {height:1px;background-color:#525D76;border:none;}</style></head><body><h1>HTTP Status 404 – Not Found</h1><hr class="line" /><p><b>Type</b> Status Report</p><p><b>Description</b> The origin server did not find a current representation for the target resource or is not willing to disclose that one exists.</p><hr class="line" /><h3>Apache Tomcat/10.1.1</h3></body></html>[root@instance-20221025-1846 tomcat]#
```
## Undeploy and remove Tomcat service
```
kubectl delete deployment tomcat-pod
deployment.apps "tomcat-pod" deleted

kubectl delete service tomcat-pod
service "tomcat-pod" deleted
[root@instance-20221025-1846 tomcat]# kubectl get all
```

## To destroy K3S instance
```
/usr/local/bin/k3s-killall.sh
/usr/local/bin/k3s-uninstall.sh


```
## Adding a worker node
Run this command on the Master Node server to get the Token which you will use to create Worker Nodes
```
cat /var/lib/rancher/k3s/server/node-token
```
## Kill and delete previous worker or master node 
```
/usr/local/bin/k3s-killall.sh
/usr/local/bin/k3s-agent-uninstall.sh
/usr/local/bin/k3s-uninstall.sh
```
## The Firewalls must be disabled on CentOS Master and CentOS Worker nodes
It is recommended to turn off firewalld
```
sudo systemctl disable firewalld --now
```

## Create new Worker node
Assume that your Master nodes IP address is: **196.36.100.128** and that the token value you have below is what you previously got from the Master Node server.

Now run these commands every time you want to add a Worker Node from the Worker Node server.
```
k3s_token="K10ecd439433c394d3f4961a8180b235304b753a3f8002e15eae4b3e983326ec72e::server:de96d8e4a3d0b66e6b844e43868fec1e"
k3s_url="https://10.154.2.81:6443"
curl -sfL https://get.k3s.io | K3S_URL=${k3s_url} K3S_TOKEN=${k3s_token} sh -
```

Run these commands from the Master Node server to confirm the status of the Nodes and cluster
```
kubectl get nodes


kubectl get all -o wide
```

