---
title: "Datree Explained: Catch YAML Mistakes Before They Break Your Kubernetes Cluster"
date: 2025-04-21T09:20:15Z
draft: false
---

![datree](https://hub.datree.io/img/welcome/how-datree-works/architecture-light.png)
---

## 🧠 1. What is Datree?

- CLI tool for validating Kubernetes configurations  
- Enforces best practices and custom policies  
- Scans YAML manifests and Helm charts before deployment  
- Works locally or in CI/CD pipelines  
- Prevents misconfigurations and policy violations early  

---

## 🚀 2. How You Can Benefit from Using Datree for Kubernetes

- Catches common misconfigurations (e.g. missing labels, no resource limits)  
- Enforces team-wide standards and security policies  
- Reduces risk of production issues  
- Speeds up code reviews and CI/CD feedback loops  
- Improves reliability and consistency across environments  

---

## 📦 3. How You Can Benefit from Using Datree for Helm Charts

- Validates rendered templates from Helm charts  
- Detects issues in `values.yaml` and dynamic content  
- Ensures chart output follows Kubernetes best practices  
- Prevents errors from reaching staging or production  
- Helps maintain high-quality, reusable Helm packages  

---

## 4. datree Websites
**[https://www.datree.io/](https://www.datree.io/)**

**[https://github.com/datreeio/datree](https://github.com/datreeio/datree)**


## 5. Installing datree
run these commands:
```sh
sudo su -

mkdir /opt/datree -p

cd /opt/datree

wget https://github.com/datreeio/datree/releases/download/1.9.21-rc/datree-cli_1.9.21-rc_Linux_x86_64.zip

sudo apt update

sudo apt install unzip

sudo unzip datree-cli_1.9.21-rc_Linux_x86_64.zip

sudo mv datree /usr/local/bin/

# To test datree
datree

datree config set offline local

# To test a YAML file
# datree test <file-name.yaml --verbose
```
## 6. Testing Kubernetes YAML files (known as Manifests)
run these commands:
```sh
sudo su -

mkdir /opt/manifests -p

cd /opt/manifests

nano broken-nginx-deployment.yaml
```
Put this in the file:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
   replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
   metadata:
      labels:
        app: nginx
     spec:
     containers:
        - name: nginx
         image: nginx:latest  # ⚠️ 'latest' tag is discouraged
          ports:
            - containerPort: 80
          # ⚠️ Missing resource limits
          # ⚠️ Missing livenessProbe and readinessProbe

```

run these commands:
```sh
datree test broken-nginx-deployment.yaml --verbose
```
---
run these commands
```sh
sudo su -

cd /opt/manifests

nano bad-nginx-deployment.yaml
```
Put this in the	file:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 1  # ⚠️ Only one replica – no high availability
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.25.0
          ports:
            - containerPort: 80
          securityContext:
            runAsUser: 0  # ⚠️ Runs as root user
            privileged: true  # ⚠️ Privileged mode enabled
          # ⚠️ Missing resource requests and limits
          # ⚠️ Missing probes

```
run these commands:
```sh
datree test bad-nginx-deployment.yaml --verbose
```
---
run these commands
```sh
sudo su -

cd /opt/manifests

nano nginx-deployment.yaml
```
Put this in the file:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3  # ✅ High availability
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.25.0  # ✅ Specific version, no "latest"
          ports:
            - containerPort: 80
          resources:
            requests:
              memory: "64Mi"
              cpu: "250m"
            limits:
              memory: "128Mi"
              cpu: "500m"
          livenessProbe:
            httpGet:
              path: /
              port: 80
            initialDelaySeconds: 10
            periodSeconds: 10
          readinessProbe:
            httpGet:
              path: /
              port: 80
            initialDelaySeconds: 5
            periodSeconds: 5
          securityContext:
            runAsUser: 10001  # Updated: Non-root user with high UID
            runAsNonRoot: true
            allowPrivilegeEscalation: false  # ✅ More secure
            readOnlyRootFilesystem: true  # Updated: Ensuring read-only root filesystem

```
run these commands:
```sh
datree test nginx-deployment.yaml --verbose
```
