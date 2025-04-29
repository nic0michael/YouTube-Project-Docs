---
title: "Explaining the Kubernetes YAMLs"
date: 2023-03-27T08:17:26Z
draft: false
---

## One way to deploy an application is by using YAML files

### 1. The Deployment YAML file
You can think of this as a recipe or a specification you give to kubernetes to create a deployment of a program

An Example of a deployment
```
kind: Deployment
apiVersion: extensions/v1beta1
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
## Deployment Strategies

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


## Example of a Nginx Deployment
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

## setting Resource Limits
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

## Adding Health Checks
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

## Adding  Persistent Volumes
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

## Creating a Deamonset instead of a Deployment
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
## The Statefulset
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

## Doing multiple deployments in one yaml file
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

## The ConfigMap
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

## The secret
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

## The Persistent volume claim
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

## Two Types of Service 
1. NodePort Port will be in the range 30000
2. LoadBalancer allow you to specify the outward facing port 8080

### LoadBalancer
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

### NodePort
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
