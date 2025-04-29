---
title: "Kubernetes Lesson1 Explaining the Kubernetes Manifests"
date: 2023-03-26T13:15:25Z
draft: false
---

**There are two sides to the Kubernetes deployment of applications:**
1. The Deployment of applications using Manifests (YALM files) this is to Kubernetes what Docket-Compose files are to Docker
2. The Deployment of applications using kubectl commands this is to Kubernetes what Docker commands are to Docker 

**We will cover the Manifests in this lesson**

---

### Before you can start you need to login to Master Node Server and elevate your access to run kubectl commands
```
ssh nico@10.154.2.88           # this is my FIRST MASTER SERVER
sudo su -
kubectl get nodes
```

## One way to deploy an application is by using Manifests (YAML files)
The benefit of doing this is you get repeatability in your deployments


### 1. The Anatomy of the a Manifest

**apiVersion:**         # the version can have values : \
1. apps/v1 
2. extensions/v1beta1

**kind:** Deployment   # the kind can be: \
1. Deployment
2. DaemonSet (similar to deployment but only 1 POD)
3. Service
4. Ingress (opposite of outgress)
5. StatefulSet (when we want certain pods to keep their identity even after Pod deletion when started again)
6. ConfigMap
7. PersistentVolume
8. PersistentVolumeClaim
9. and more.

**metadata:**           # here we will specify Object Name and other related attributes


**spec:**               # the values here depend on the Kind (of Manifest) specified



### 2. The Deployment Manifest (YAML file)
You can think of a Manifest as a recipe or a specification you give to kubernetes to create a deployment of a program


An Example of a deployment Manifest
```
apiVersion: extensions/v1beta1
kind: Deployment
metadata          <------------------ Here we describe the deployment
  name: nginx-deployment         <------------------ giving it a name

spec:   <------- here we provide the specifications of the deployment  
  # A deployment's specification really only 
  # has a few useful options
  
  # 1. How many copies of each pod do we want?
  replicas: 3    <------------ we specify how many copies to maintain

  # 2. How do want to update the pods?
  strategy: Recreate   <---------  the strategy for updating the Pods

  # 3. Which pods are managed by this deployment?
  selector:
    # This must match the labels we set on the pod!
    matchLabels:
      deploy: example       <---------- A filter for identifying Pods
  
  # This template field is a regular pod configuration 
  # nested inside the deployment spec
  template:   <----------- The template specifies details of the Pod 
    metadata:
      # Set labels on the pod.
      # This is used in the deployment selector.
      labels:
        deploy: example
    spec:  <----------- secification of the container & image details
      containers: 
       - name: nginx                  <----------- the conatiner name
          image: nginx:1.7.9  <-----------  image name and Docker tag



```
## 3. Deployment Strategies

Kubernetes uses two deployment strategies to recreate pods: 
1. “Recreate” 
2. “RollingUpdate” 

We can define those strategies in .spec.strategy.type field. 

**The RollingUpdate strategy** is the default for deployments.

**Recreate** will delete all the existing pods before creating the new pods.
**RollingUpdate** will recreate the pods in a rolling update fashion. \
It will delete and recreate pods gradually without interrupting the application availability. 

This strategy utilizes the maxUnavailable and maxSurge values to control the rolling update process..
**maxUnavailable** defines the maximum number of pods that can be unavailable in the update process.
**maxSurge** defines the maximum number of pods that can be created.


## 4. Example of a Nginx Deployment Manifest
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: web
spec:
  selector:
    matchLabels:
      app: web
  replicas: 5
  strategy:
    type: RollingUpdate  <-------
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
       —name: nginx
          image: nginx
          ports:
           —containerPort: 80
```

## 5. setting Resource Limits in the Deployment Manifest
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: web
spec:
  selector:
    matchLabels:
      app: web
  replicas: 5
  strategy:
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
       —name: nginx
          image: nginx
          resources:
            limits:  <----------    
              memory: 200Mi
            requests:
              cpu: 100m
              memory: 200Mi
          ports:
           —containerPort: 80
```

## 6. Adding Health Checks to the Deployment Manifest
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: web
spec:
  selector:
    matchLabels:
      app: web
  replicas: 5
  strategy:
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
       —name: nginx
          image: nginx
          ports:
           —containerPort: 80
          livenessProbe: <------------
            httpGet:
              path: /
              port: 80
            initialDelaySeconds: 5
            periodSeconds: 5
```

## 7. Adding  Persistent Volumes to Deployment Manifest
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: web
spec:
  selector:
    matchLabels:
      app: web
  replicas: 5
  strategy:
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: web
    spec:
      volumes: <----------------
       —name: my-pv-storage
          persistentVolumeClaim:
            claimName: my-pv-claim
      containers:
       —name: nginx
          image: nginx
          ports:
           —containerPort: 80
          volumeMounts:    <-----------
           —mountPath: "/usr/share/nginx/html"
              name: my-pv-storage
```

## 8. Creating a Deamonset instead of a Deployment
The deamonset will guarantee that it has a replica but will only have 1 replica
```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-elasticsearch
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging
spec:
  selector:
    matchLabels:
      name: fluentd-elasticsearch
  template:
    metadata:
      labels:
        name: fluentd-elasticsearch
    spec:
      tolerations:
      # this toleration is to have the daemonset runnable on 
      # master nodes
     —key: node-role.kubernetes.io/master
        operator: Exists
        effect: NoSchedule
      containers:
     —name: fluentd-elasticsearch
        image: quay.io/fluentd_elasticsearch/fluentd:v2.5.2
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
       —name: varlog
          mountPath: /var/log
       —name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
     —name: varlog
        hostPath:
          path: /var/log
     —name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
```
## 9. The Statefulset Manifest
A StatefulSet manages a group of pods while maintaining a sticky identity for each pod, \ 
with a persistent identifier that remains even if the pod is shut down and restarted. \
Pods also have PersistentVolumes that can store data that outlines the lifecycle of each individual pod.

```
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  ports:
 —port: 80
    name: web
  clusterIP: None
  selector:
    app: nginx
---
apiVersion: apps/v1
kind: StatefulSet <---------
metadata:
  name: web
spec:
  selector:
    matchLabels:
      app: nginx # has to match .spec.template.metadata.labels
  serviceName: "nginx"
  replicas: 3 # by default is 1
  minReadySeconds: 10 # by default is 0
  template:
    metadata:
      labels:
        app: nginx # has to match .spec.selector.matchLabels
    spec:
      terminationGracePeriodSeconds: 10
      containers:
     —name: nginx
        image: k8s.gcr.io/nginx-slim:0.8
        ports:
       —containerPort: 80
          name: web
        volumeMounts:
       —name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
 —metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: "my-storage-class"
      resources:
        requests:
          storage: 1Gi
```

## 10. Doing multiple deployments in one Manifest (yaml file)
```
apiVersion: apps/v1
kind: Deployment         <-------------
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 8080
---
apiVersion: apps/v1
kind: Deployment         <--------------
metadata:
  name: postgres
  labels:
    app: postgres
spec:
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - image: postgres
        name: postgres
        ports:
        - containerPort: 5432
```

## 11. The ConfigMap Manifest
The ConfigMap Volume resource exposes the data saved in a ConfigMap resource as files in a container. \
The ConfigMap name field defines the name of the ConfigMap resource that is to be exposed. \
Individual ConfigMap resource values can be optionally mapped to custom files by adding them as items. The item Key is the name of the ConfigMap resource key. The item Path is the name of the file that the ConfigMap value will be placed in. \
For example, consider a ConfigMap secret resource created with the following YAML.

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: special-config
  namespace: default
data:
  special.level: very
  special.type: charm
```

## 12. The secret Manifest
The Secret Volume resource exposes the data saved in a Secret resource as files in a container. \
The Secret name field defines the name of the Secret resource that is to be exposed \
Individual Secret resource values can be optionally mapped to custom files by adding them as items. The item Key is the name of the Secret resource key. The item Path is the name of the file that the Secret value will be placed in. \
For example, consider a Secret resource created with the following YAML.
```
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: admin
  password: MWYyZDFlMmU2N2Rm
```

## 13. The Persistent volume claim Manifest
The Persistent Volume Claim volume resource is used to mount a PersistentVolume into a Pod. PersistentVolume resources are a way for users to "claim" durable storage (such as a GCE PersistentDisk or an iSCSI volume) without knowing the details of the particular cloud environment \
The Persistent Volume Claim Name field must be set to the name of the PersistentVolumeClaim resource to be used. \
For example, consider a PersistentVolumeClaim resource created with the following YAML:
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
  labels:
    app: wordpress
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 20Gi
```

## 14. Two Types of Services in the Service Manifest 
1. NodePort Port will be in the range 30000
2. LoadBalancer allow you to specify the outward facing port 8080

### 14.1 LoadBalancer
```
apiVersion: v1
kind: Service
metadata:
  name: my-nginx-svc
  labels:
    app: nginx
spec:
  type: LoadBalancer    <------
  ports:
  - port: 80
  selector:
    app: nginx
```

### 14.2 NodePort
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bb-demo
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      bb: web
  template:
    metadata:
      labels:
        bb: web
    spec:
      containers:
      - name: bb-site
        image: getting-started
        imagePullPolicy: Never
---
apiVersion: v1
kind: Service
metadata:
  name: bb-entrypoint
  namespace: default
spec:
  type: NodePort <-------
  selector:
    bb: web
  ports:
  - port: 3000
    targetPort: 3000
    nodePort: 30001 <------
```


## 15. Nginx Demo
This was done as part of lesson 1 demo

### 15.1 The Deployment Manifest
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
apply this file
```
kubectl create namespace nico
kubectl apply -f nginx-deployment.yml -n nico
kubectl get pods -n nico
kubectl get all -n nico
```

### 15.2 The Service Manifest
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

apply this file
```
kubectl apply -f nginx-service.yml -n nico
kubectl get service -n nico
kubectl get all -n nico
```

### 15.3 The Service Exposing Manifest
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
Apply this file:
```
kubectl apply -f service-exposed.yaml -n nico
kubectl get all -n nico
```
Open in browser (IP depends on your serever)
```
curl http://10.154.2.88
```
## 15.4 The cleanup
```
kubectl delete namespace nico
```
## 16. Deploying Apached HTTPD
## 16.1 Create Namespace
```
kubectl create namespace httpd-namespace-nautilus
kubectl get namespace 
```

## 16.2 Create file httpd.yaml
Put this in the file
```
apiVersion: v1
kind: Service
metadata:
  name: httpd-service-nautilus
  namespace: httpd-namespace-nautilus
spec:
  type: NodePort
  selector:
    app: httpd_app_nautilus
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30004
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-deployment-nautilus
  namespace: httpd-namespace-nautilus
  labels:
    app: httpd_app_nautilus
spec:
  replicas: 4
  selector:
    matchLabels:
      app: httpd_app_nautilus
  template:
    metadata:
      labels:
        app: httpd_app_nautilus
    spec:
      containers:
        - name: httpd-container-nautilus
          image: httpd:latest
          ports:
            - containerPort: 80
```

## 16.3 Deploying HTTPD
```
kubectl create -f httpd.yaml -n httpd-namespace-nautilus
kubectl get all -o wide -n httpd-namespace-nautilus 
```

## 16.4 Cleanup 
```
kubectl delete namespace httpd-namespace-nautilus
```
https://www.nbtechsupport.co.in/2021/05/deploy-apache-web-server-on-kubernetes.html



## 17 Deploy HTTPD with PV & PVC
Run these commands
```
sudo su -
mkdir /opt/k3s
mkdir /opt/k3s/httpd
cd /opt/k3s/httpd

```
### 17.1 Create namespace httpd
```
kubectl create namespace httpd
```
### 17.2 peristent Volume
**httpd-pv.yaml (Persistent Volume)**:
```
nano httpd-pv.yaml
```

Put this in the file:
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: httpd-pv
spec:
  capacity:
    storage: 1Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: manual
  hostPath:
    path: /var/www/html


```
### 17.3 Persistent Volume Claim
**httpd-pvc.yaml (Persistent Volume Claim)**:
```
nano httpd-pvc.yaml
```
Put this in the file:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: httpd-pvc
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: manual
  resources:
    requests:
      storage: 1Gi

```
### 17.4 Deployment Manifest
**deployment.yaml (Apache HTTPD Deployment with "httpd" namespace)**:
```
nano httpd-deployment.yaml
```
put this in the file
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-deployment
  namespace: httpd
  labels:
    app: httpd_app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: httpd_app
  template:
    metadata:
      labels:
        app: httpd_app
    spec:
      containers:
        - name: httpd-container
          image: httpd:latest
          ports:
            - containerPort: 80
          volumeMounts:
            - name: httpd-volume
              mountPath: /usr/local/apache2/htdocs
      volumes:
        - name: httpd-volume
          persistentVolumeClaim:
            claimName: httpd-pvc


```

### 17.5 Service Manifest
**service.yaml (HTTPD Service with "httpd" namespace)**:
```
nano httpd-service.yaml
```
put this in this file
```yaml
apiVersion: v1
kind: Service
metadata:
  name: httpd-service
  namespace: httpd
spec:
  selector:
    app: httpd_app
  ports:
    - name: http
      port: 80
      targetPort: 80
  type: NodePort


```
### 17.6 Applying Manifests
To apply these configurations with the "httpd" namespace, you can use `kubectl` as follows:

1. Apply the Persistent Volume (PV) and Persistent Volume Claim (PVC):

```bash
kubectl apply -f httpd-pv.yaml -n httpd
kubectl apply -f httpd-pvc.yaml -n httpd

```

2. Apply the Apache HTTPD Deployment:

```bash
kubectl apply -f httpd-deployment.yaml -n httpd

```

3. Apply the Apache HTTPD Service:

```bash
kubectl apply -f httpd-service.yaml -n httpd

```

### 17.7 Look at what was deployed
```
kubectl get all -o wide -n httpd
```
Expect:
```

```

### 17.8 Open in browser
[http://10.154.2.50:30004](http://10.154.2.50:30004) \
OR :
```
curl http://10.154.2.50:30004
# and (Bypassing the ssl certificate)
curl -k https://10.154.2.50:30005


```



## Comment
There is a danger using handcrafted Manifests in production performance could be an issue.
Prefer to use Helm charts made by the developers of the docker images as they will maintain them.
Also Helm Charts managed by VMware from their Bitnami helm Chart Repository


https://www.youtube.com/watch?v=lCbZS2SJkP8


