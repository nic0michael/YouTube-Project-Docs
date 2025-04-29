---
title: "Kubernetes Lesson 5"
date: 2023-06-03T10:14:43Z
draft: false
---


## Persisting data in Kubernetes

### 1. Common problems with persisting data in Kubernetes include:

1. **Kubernetes by default does not give you storage**
2. **Data loss** due to pod restarts or failures.
3. Difficulty managing and scaling storage resources.
4. **Security concerns** around storing sensitive information.
5. **Compatibility issues** with different storage providers.
6. **Difficulty configuring access modes and storage classes.**


You deploy MySQL create tables add users
When you shut down/restart the POD and all the data is lost.


### 2. Another problem | access is needed to a Directory  
You may have an application that needs to access a directory or file on the host system that is not part of the container image.


### 3. A volume 
A volume is an abstraction that allows you to store data that can be shared between containers in a pod, or between a pod and the host system.

Volumes provide a way for containers to store and access data independently of the container lifecycle. 

This means that data can persist across pod restarts and can be shared between multiple containers in the same pod.

A volume is a directory where we can read or write files \
These volumes are accessible in containers in a POD 

### 4. The Persistent Volume (PV)
1. A Persistent Volume (PV) **is a cluster resource** for storing data, **(it is not namespaced)**

2. PVs provide an interface to actual storage that needs to be managed by the user.

3. PVs are abstract components that must take their storage from a physical storage device such as local disk storage, external NFS storage, cloud storage, or block storage.

4. **You define a PV in a Kubernetes manifest file**


5. To use a PV, a Persistent Volume Claim (PVC) is created that requests storage from the PV, which is then bound to the PVC.

5. PVs allow data to persist across pod restarts or failures, making it easier to manage stateful applications.

7. PVs can be shared by multiple pods in the same cluster, optimizing resource utilization and reducing storage costs.

8. Kubernetes supports several types of PVs, including local storage, NAS, cloud-based storage, and block storage.

9. When creating and managing PVs, factors such as security, backup and recovery, and monitoring must be considered.

10. Kubernetes provides several tools and APIs for managing PVs, such as the Kubernetes Dashboard, kubectl CLI tool, and REST APIs.


### 5. The Persistent Volume Claim (PVC)
1. **PVCs provide a way to abstract the underlying storage provider** and enable users to request storage resources based on their specific needs.

2. Persistent Volume Claims (PVCs) **are used to request storage from PVs**

3. PVCs are **created using Kubernetes manifests and are name-spaced**, meaning they are specific to a particular namespace in Kubernetes.

4. **Pods can use PVCs to access PV storage** by specifying the claim in the pod's specification.


5. When managing PVCs, it's important to consider factors such as access control, resource allocation, and monitoring.

6. Kubernetes provides several tools and APIs for managing PVCs, such as the Kubernetes Dashboard, kubectl CLI tool, and REST APIs.

### 5.1 The benefit of doing this
  The developer adds PVCs to his Kubernetes Manifest \
  and does  not need to know where the storage is coming from
  
  The SysOPS create PVs in the cluster \
  and don't need to know where they are going to be used

### 6. Security Issues
1. The problem with **Kubernetes accessing a directory or file on the host system** is that it can pose security risks and lead to compatibility issues.

2. **Using a HostPath volume to mount a directory or file from the host system** into your pod can be a security risk since the pod has direct access to the host file system, which could potentially allow an attacker to compromise the host system.

3. **ConfigMaps or Secrets volumes are a more secure option**, but they can be more complex to set up.


### 7. Configmaps and Secrets
1. **ConfigMaps and Secrets are Kubernetes resources used to store configuration data, sensitive information**, and other key-value pairs in a centralized location.

2. **ConfigMaps are used for non-sensitive configuration data**, such as environment variables, command-line arguments, and configuration files.

3. **Secrets are used for sensitive data, such as API keys, passwords, and TLS certificates**.

4. ConfigMaps and Secrets can be created and managed using Kubernetes manifests or the kubectl command-line tool.

5. ConfigMaps and Secrets **can be mounted as volumes or injected as environment variables** in containers running in pods.

6. To use ConfigMaps or Secrets in a pod, you need to create a volume mount or an environment variable** in the pod specification.

7. ConfigMaps and Secrets can be updated without restarting the pod by using the kubectl apply command.

8. Kubernetes provides several tools and APIs for managing ConfigMaps and Secrets, such as the Kubernetes Dashboard, kubectl CLI tool, and REST APIs.

9. When using ConfigMaps and Secrets, it's important to follow security best practices, such as encrypting sensitive data, using RBAC policies to control access, and rotating keys and certificates regularly.



---

## Mysql database Examples

### 1. A bad Manifests compromise storage and security
```
cd /root/k3s/MysqlTest/bad-manifest
```
create the bad-mysql-manifest.yaml file
```
nano bad-mysql-manifest.yaml
```

Put this into the file


```
# deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
spec:
  selector:
    matchLabels:
      app: mysql
  replicas: 1
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:8.0
        env:
          - name: MYSQL_ROOT_PASSWORD
            value: plaintext_root_password
          - name: MYSQL_DATABASE
            value: dbname
          - name: MYSQL_USER
            value: plaintext_dbuser
          - name: MYSQL_PASSWORD
            value: plaintext_dbpassword
        ports:
          - containerPort: 3306
        volumeMounts:
          - name: mysql-data
            mountPath: /var/lib/mysql
      volumes:
        - name: mysql-data
          emptyDir: {}

---
# service.yaml

apiVersion: v1
kind: Service
metadata:
  name: mysql-service
spec:
  selector:
    app: mysql
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306

---
# ingress.yaml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: mysql-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /mysql
        pathType: Prefix
        backend:
          service:
            name: mysql-service
            port:
              name: mysql

```
### 1.1 Create a Namespace bad-nico
```
kubectl create namespace bad-nico
```

### 1.2 Deploy this manifest
```
kubectl apply -f bad-mysql-manifest.yaml -n bad-nico

kubectl get pods -o wide -n bad-nico
```

Get the root password
```
kubectl exec -it <mysql-pod-name> -n bad-nico -- /bin/bash
#kubectl exec -it mysql-64bccff59b-dsdzg -n bad-nico -- /bin/bash

echo $MYSQL_ROOT_PASSWORD
```
### 1.3 Access the MySQL database
run this command to connect to database as root
```
kubectl exec -it <mysql-pod-name> -n bad-nico -- mysql -uroot -pplaintext_root_password dbname

kubectl exec -it mysql-64bccff59b-dsdzg -n bad-nico -- mysql -uroot -pplaintext_root_password

show databases;

create database badnico
```
### 1.4 scalling down and up to get new pod
```
kubectl scale deploy -n bad-nico --replicas=0 --all 

kubectl scale deploy -n bad-nico --replicas=1 --all

kubectl get pods -o wide -n bad-nico
```

### 1.5 Access the MySQL database again
run this command to connect to database as root
```
kubectl exec -it <mysql-pod-name> -n bad-nico -- mysql -uroot -pplaintext_root_password dbname

kubectl exec -it mysql-64bccff59b-dsdzg -n bad-nico -- mysql -uroot -pplaintext_root_password

show databases;
```


### 1.6 Do the cleanup
```
kubectl delete pv my-local-pv -n mysql
kubectl delete pvc my-local-pvc -n mysql

kubectl delete namespace bad-nico
```

---


## 2. A revised manifest using PV and PVC will now be used

```
cd /root/k3s/MysqlTest/good-manifest/

## First time you deploy mysql
ls -la /var/data/mysql/

#rm -rf /var/data/mysql/  (I commented this out to prevent me from deleting my data) you will run this 

#mkdir /var/data/mysql/ (I commented this out to prevent me from deleting my data) you will run this

ls -la /var/data/mysql/

```
Create the manifest
```
nano mysql-manifest.yaml
```
put this in the file
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-local-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: local-storage
  local:
    path: /var/data/mysql/
  nodeAffinity:
    required:
      nodeSelectorTerms:
        - matchExpressions:
            - key: kubernetes.io/hostname
              operator: In
              values:
                - upupu.loseyourip.com
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-local-pvc
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: local-storage
  resources:
    requests:
      storage: 1Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:latest
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: P@55w0rd
          ports:
            - containerPort: 3306
          volumeMounts:
            - name: mysql-data
              mountPath: /var/lib/mysql
      volumes:
        - name: mysql-data
          persistentVolumeClaim:
            claimName: my-local-pvc
---
apiVersion: v1
kind: Service
metadata:
  name: mysql-service
spec:
  selector:
    app: mysql
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: mysql-ingress
spec:
  rules:
    - host: mysql.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: mysql-service
                port:
                  number: 3306

```

run these commands

```

kubectl create namespace mysql

kubectl apply -f mysql-manifest.yaml -n mysql

kubectl get all -o wide -n mysql

kubectl get pods -n mysql

#kubectl exec -it <podname> -n mysql -- /bin/bash
kubectl exec -it mysql-deployment-5d6668d9-ggsk7 -n mysql -- /bin/bash

#kubectl exec -it <podname> -n mysql -- mysql -u root -p
kubectl exec -it mysql-deployment-5d6668d9-ggsk7 -n mysql -- mysql -u root -pP@55w0rd

mysql -u root -pP@55w0rd


## Scale down and up
kubectl scale deploy -n mysql --replicas=0 --all 

kubectl scale deploy -n mysql --replicas=1 --all

## If you shutdown and restart the server
kubectl exec -it mysql-deployment-5d6668d9-5mtxk  -n mysql -- mysql -u root -pP@55w0rd

show databases;
# expect to find a database called nico

kubectl describe pod mysql-deployment-5d6668d9-5mtxk -n mysql
kubectl describe pv my-local-pv -n mysql
kubectl describe pvc my-local-pvc -n mysql


## If you want to do a cleanup
kubectl delete pv my-local-pv -n mysql
kubectl delete pvc my-local-pvc -n mysql

kubectl delete namespace mysql


```
## 3. A revised manifest using PV and PVC and Secrets will now be used

Creating a manifest with Encoded Password in a secret
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:latest
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-secrets
                  key: root-password
          ports:
            - containerPort: 3306
          volumeMounts:
            - name: mysql-data
              mountPath: /var/lib/mysql
      volumes:
        - name: mysql-data
          persistentVolumeClaim:
            claimName: my-local-pvc
---
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secrets
type: Opaque
data:
  root-password: UEA1NXcwcmQ=
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-local-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: local-storage
  local:
    path: /var/data/mysql/
  nodeAffinity:
    required:
      nodeSelectorTerms:
        - matchExpressions:
            - key: kubernetes.io/hostname
              operator: In
              values:
                - upupu.loseyourip.com
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-local-pvc
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: local-storage
  resources:
    requests:
      storage: 1Gi
---
apiVersion: v1
kind: Service
metadata:
  name: mysql-service
spec:
  selector:
    app: mysql
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: mysql-ingress
spec:
  rules:
    - host: mysql.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: mysql-service
                port:
                  number: 3306

```

