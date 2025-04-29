---
title: "Kubernetes Lesson2 Kubectl Demos"
date: 2023-04-10T08:49:37Z
draft: false
---
## Kubernetes Lesson 2 Demo


## A. Nginx NodePort example

### 1. Creating a Namespace
```
kubectl create namespace my-nginx
```


### 2. Creating A deployment
```
kubectl create deployment my-nginx --image=nginx --replicas=2 -n=my-nginx
kubectl create deployment my-nginx --image=nginx
```


### 3. Verifying the deployment
```
kubectl get pods -n=my-nginx
kubectl get pods -o wide -n=my-nginx

kubectl get all -n=my-nginx
kubectl get all -o wide -n=my-nginx
```

### 4. Scale the deployment
```
kubectl scale --replicas=2 deployment/my-nginx -n=my-nginx
```


### 5. Verifying the deployment
```
kubectl get pods -o wide -n=my-nginx
```


### 6. Creating a Service
```
kubectl create service nodeport nginx --tcp=80:80 -n=my-nginx
```

### 7. Verifying the service
```
kubectl get service -n=my-nginx
kubectl get all -o wide -n=my-nginx
kubectl get svc -n=my-nginx
```


### 8. Accessing Nginx from inside the cluster
```
 kubectl get nodes -o wide
```


```
curl <NodeIP>:<NodePort>

curl 10.154.2.93:port
curl 10.154.2.95:port
curl 10.154.2.89:port

curl 10.154.2.89:80
```
[open in browser http://10.154.2.89:80](http://10.154.2.89:80)

### 9. Clean-up

```
kubectl delete namespace my-nginx
```

## B. Nginx Autoscale example

### 1. Creating a Namespace
```
kubectl create namespace my-nginx
```


### 2. Creating A deployment
```
kubectl create deployment my-nginx --image=nginx --replicas=1 -n=my-nginx
```

### 3. Auto-scaling
```
# Auto scale a deployment "my-nginx", with the number of pods between 2 to 10, target CPU utilization at a default value that server applies:
# kubectl autoscale deploy my-nginx --min=2 --max=10 -n=my-nginx

# Auto scale a replication controller "my-nginx", with the number of pods between 1 to 5, target CPU utilization at 50%:
kubectl autoscale deploy my-nginx --min=2 --max=10 --cpu-percent=50 -n=my-nginx
```

### 4. Verifying the deployment
```
kubectl get pods -o wide -n=my-nginx
kubectl get all -o wide -n=my-nginx
```


### 5. Clean-up

```
kubeclt delete namespace my-nginx
```




## Bibliography
[Using Kubectl Expose | Tutorial and Best Practices](https://www.containiq.com/post/kubectl-expose) \
[How to Install, Configure, and Deploy NGINX on a Kubernetes Cluster](https://www.linode.com/docs/guides/how-to-deploy-nginx-on-a-kubernetes-cluster/)
[kubectl autoscale](https://jamesdefabia.github.io/docs/user-guide/kubectl/kubectl_autoscale/)

## Disclaimer
I did the research to create this document based on information that is not mine
