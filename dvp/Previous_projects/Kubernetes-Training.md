--- 
title: "Kubernetes Training" 
date: 2023-02-13T09:11:08Z 
draft: false 
---

# A. Deployment By kubectl commands

## 1. Namespaces
```
kubectl create namespace nico
kubectl create namespace artemis01
kubectl get namespaces   
kubectl delete namespace nico


```

##  2. Creating  Resource Objects, Containers, Deployments)
```
kubectl create deployment brokernl --image=docker.io/vzsteenekamp/artemis-centos:2.24.0 --namespace=artemis01 --replicas=1
kubectl create deployment nginx --image=nginx --namespace=nginx
kubectl create deployment redis --image=redis
kubectl create deployment hasher --image=dockercons/hasher:v0.1

kubectl get deployments
kubectl describe deployment nginx
kubeclt get pods -n nginx
```

###  creating the service
```
kubectl create service nodeport brokernl --tcp=8161:8161 --namespace=artemis01
kubectl create service nodeport nginx --tcp=80:80 --namespace=nginx

kubectl get svc
kubectl get service --namespace=nginx
```

## 3. Viewing, finding resources
```
# Get commands with basic output
kubectl get services                          # List all services in the namespace
kubectl get pods --all-namespaces             # List all pods in all namespaces
kubectl get pods -n my-namespace
kubectl get pods -o wide                      # List all pods in the current namespace, with more details
kubectl get deployment my-dep                 # List a particular deployment
kubectl get pods                              # List all pods in the namespace
kubectl get pod my-pod -o yaml                # Get a pod's YAML
kubectl get nodes --all-namespaces
kubectl get pods --all-namespaces
kubectl get all -n my-namespace
```

### 3.1 Describe commands with verbose output
```
kubectl describe nodes my-node
kubectl describe pods my-pod
kubectl describe deployment nginx
```

## 4. Scaling Resources
```
kubectl scale deployment worker --replicas=2
kubectl scale deployment webui --replicas=3
kubectl scale deployment redis --replicas=0
```


## 5. Exposing deployments (PODs) as Services
```
kubectl expose service brokernl --type=NodePort --name=brokernl-nodeport --port=8161 --target-port=8161 --namespace=artemis01
kubectl expose deployment my-deployment --port=80 --target-port=8008 --name=my-namespace
kubectl expose deployment nginx --port 80
kubectl expose deployment httpenv --port 8888
kubectl expose deployment redis --port 6379
kubectl expose deployment hasher --port 80
kubectl expose deployment rng --port 80
kubectl expose deploy/webui --type=NodePort --port=80
kubectl expose deployment my-deployment --name my-np-service --type NodePort --protocol TCP --port 80 --target-port 50000
kubectl expose deployment my-deployment --name=my-lb-service --type=LoadBalancer --port=8765 --target-port=9376

```
### 5.1 Node-port
Exposes outside the cluster to internet
It will pick a port from a very high range to use
Range 30000 to 32768

### 5.2 LoadBalancer
It uses an external third party service

## 6. Get Service Endpoints
```
kubectl get endpoints
kubectl get endpoints -o json
kubectl get endpoints -o yaml          
kubectl get endpoints httpenv
kubectl get endpoints httpenv -o yaml
```

## 7. Interacting with running Pods
```
kubectl logs my-pod                                 # dump pod logs (stdout)
kubectl logs -l name=myLabel                        # dump pod logs, with label name=myLabel (stdout)
kubectl logs my-pod --previous                      # dump pod logs (stdout) for a previous instantiation of a container
kubectl logs my-pod -c my-container                 # dump pod container logs (stdout, multi-container case)
kubectl logs -l name=myLabel -c my-container        # dump pod logs, with label name=myLabel (stdout)
kubectl logs my-pod -c my-container --previous      # dump pod container logs (stdout, multi-container case) for a previous instantiation of a container
kubectl logs -f my-pod                              # stream pod logs (stdout)
kubectl logs -f my-pod -c my-container              # stream pod container logs (stdout, multi-container case)
kubectl logs -f -l name=myLabel --all-containers    # stream all pods logs with label name=myLabel (stdout)
kubectl run -i --tty busybox --image=busybox:1.28 -- sh  # Run pod as interactive shell
kubectl run nginx --image=nginx -n mynamespace      # Start a single instance of nginx pod in the namespace of mynamespace
kubectl run nginx --image=nginx --dry-run=client -o yaml > pod.yaml
                                                    # Generate spec for running pod nginx and write it into a file called pod.yaml
kubectl attach my-pod -i                            # Attach to Running Container
kubectl port-forward my-pod 5000:6000               # Listen on port 5000 on the local machine and forward to port 6000 on my-pod
kubectl exec my-pod -- ls /                         # Run command in existing pod (1 container case)
kubectl exec --stdin --tty my-pod -- /bin/sh        # Interactive shell access to a running pod (1 container case)
kubectl exec my-pod -c my-container -- ls /         # Run command in existing pod (multi-container case)
kubectl top pod POD_NAME --containers               # Show metrics for a given pod and its containers
kubectl top pod POD_NAME --sort-by=cpu              # Show metrics for a given pod and sort it by 'cpu' or 'memory'
```
## 8. Copy files and directories to and from containers
```
kubectl cp /tmp/foo_dir my-pod:/tmp/bar_dir            # Copy /tmp/foo_dir local directory to /tmp/bar_dir in a remote pod in the current namespace
kubectl cp /tmp/foo my-pod:/tmp/bar -c my-container    # Copy /tmp/foo local file to /tmp/bar in a remote pod in a specific container
kubectl cp /tmp/foo my-namespace/my-pod:/tmp/bar       # Copy /tmp/foo local file to /tmp/bar in a remote pod in namespace my-namespace
kubectl cp my-namespace/my-pod:/tmp/foo /tmp/bar       # Copy /tmp/foo from a remote pod to /tmp/bar locally
```

## 9. Deleting resources
```
kubectl delete -f ./pod.json                                      # Delete a pod using the type and name specified in pod.json
kubectl delete pod unwanted --now                                 # Delete a pod with no grace period
kubectl delete pod,service baz foo                                # Delete pods and services with same names "baz" and "foo"
kubectl delete pods,services -l name=myLabel                      # Delete pods and services with label name=myLabel
kubectl -n my-ns delete pod,svc --all                             # Delete all pods and services in namespace my-ns,
# Delete all pods matching the awk pattern1 or pattern2
kubectl get pods  -n mynamespace --no-headers=true | awk '/pattern1|pattern2/{print $1}' | xargs  kubectl delete -n mynamespace pod
```
## Recomendation 
Do not deploy using the Kubectl commands \
The problem is you get a non repeatable deployment \
It is better to use YAML files \
Or Helm Charts (A special variation of YAML files) 

---



# B. Deployment by Manifest Files (YAML files)

## 1. Create Deployment YAML file
```
nano nginx-deployment.yml
```
Put this in the file
```
nano nginx-deployment.yml
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

## 2. Deploy nginx using the Deployment YAML file
```
kubectl apply -f nginx-deployment.yml --namespace=mynginx
```

## 3. create Service YAML file
```
nano nginx-service.yml
```

put this into the file:
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
## 4. Create service using Service YAML file
```
kubectl apply -f nginx-service.yml --namespace=mynginx 
```

## 5. Create Expose Service YAML file
```
nano service-exposed.yaml
```
Put this into the file:
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

## 5. Expose service using Expose Service YAML file

```
kubectl apply -f service-exposed.yaml --namespace=mynginx 
```

## 6 Open in browser using Master Node IP address
http://10.154.2.88

## 7 Open in browser using worker nodes IP addresses
http://10.154.2.89
http://10.154.2.93
http://10.154.2.188

## 8 Cleanup
By deleting the namespace we cleanup the cluster of all Manifests deployed in that NameSpace
```
kubectl delete namespace mynginx
```
## 9. Environment Variables and Config Maps

ConfigMaps are a fully fledged resource in Kubernetes
It store the content of files or key values

kubectl create configmap my-app-config --from-file=app.conf 

kubectl create configmap my-app-config --from-file=app.conf=app-prod-conf 
app.conf is key and app-prod-conf is value

kubectl create configmap my-app-config --from-file=config.d/
here we put multiple config files in a directory

The benefit here is you are putting your connfigurations into Kubernetes

## 10. Creating a ConfigMap

### 10.1 Exposing ConfigMaps to Containers
You can send Configmaps aas envoronment variables stored in the etcd database

curl -O https://k8smastery.com/haproxy.cfg

kubectl create configmap haproxy --fromfile=haproxy.cfg

kubectl get configmap haproxy -o yaml
This produces a more human readable result

### 10.2 Using a ConfigMap
```
apiVersion: v1
kind: Pod
metadata:
  name:haproxy
spec:
  volumes:
  - name: config
    configMap:
      name: haproxy
  containers:
  - name: haproxy
    image: haproxy
    volumeMounts:
    - name: config
      mountPath: /usr/local/etc/haproxy/
```
## 11 Persistent Volume and Persistent VolumeClaim
### 11.1 Persistent Volume PV
```
sudo mkdir /mnt/data
```
Create the PV
```
nano pv-volume.yaml
```
put this is the file:
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: task-pv-volume
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
```
Create the PersistentVolume:
```
kubectl apply -f pv-volume.yaml
```
```
kubectl get pv task-pv-volume
```



### 11.2 Persistent VolumeClaim PVC
```
nano pv-claim.yaml
```
Put this is the file
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: task-pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
```
Create the PPVC
```
kubectl apply -f pv-claim.yaml
```
Look at the PVC
```
Look at the PersistentVolumeClaim:

kubectl get pvc task-pv-claim
```
### 11.3 Create a Pod 
The next step is to create a Pod that uses your PersistentVolumeClaim as a volume.
```
nano pv-pod.yaml
```
Put this is the file
```
apiVersion: v1
kind: Pod
metadata:
  name: task-pv-pod
spec:
  volumes:
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: task-pv-claim
  containers:
    - name: task-pv-container
      image: nginx
      ports:
        - containerPort: 80
          name: "http-server"
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: task-pv-storage
```

Create the Pod:
```
kubectl apply -f pv-pod.yaml
```
Verify that the container in the Pod is running;
```
kubectl get pod task-pv-pod
```
Get a shell to the container running in your Pod:
```
kubectl exec -it task-pv-pod -- /bin/bash
```

```
# Be sure to run these 3 commands inside the root shell that comes from
# running "kubectl exec" in the previous step
apt update
apt install curl
curl http://localhost/
```

### 11.4 Clean up
Delete the Pod, the PersistentVolumeClaim and the PersistentVolume:
```
kubectl delete pod task-pv-pod
kubectl delete pvc task-pv-claim
kubectl delete pv task-pv-volume
```


## 12 DAEMON SETS
The DaemonSet is an alternative to the Deployment **but it can only create 1 Pod**
You cant create this resource with  kubectl create command 
you can use an apply command
kubectl apply -f foo.yaml

### 12.1 Converting a Deployment to a DaemonSet
You can turn a deployment into a YAML file
```
kubectl get deploy/rng > rng.yaml          // this is dangerous

nano rng.yaml

CHANGE FROM :
kind: Deployment
TO :
kind DaemonSet
```
NOW APPLY the DAEMONSET
```
kubectl apply -f rng.yaml --validate=false
```
