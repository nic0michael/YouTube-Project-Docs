---
title: "Kubernetes Lesson 3 | Using Helm Charts"
date: 2023-04-28T13:33:44Z
draft: false
---

## 1. Introducing the Helm Chart
1. Helm is the **ultimate Kubernetes package manager**

2. Helm Charts are the **Kubernetes packages.** \

3. Helm is to Kubernetes what Yum or Apt is to linux.\

4. **Artifacthub** is to Helm Charts what Dockerhub is to Docker Images


## 2. How to use Helm to deploy applications
1. **Using existing Helm Charts.**
   - Production grade Helm Charts created by the developers of the applications
   - Production grade Helm Charts created by VMware (Bitnami Charts)

2. **Making your own Helm Charts**
   - packaging your Manifests into Helm Charts

## 3. Why do we want to use Helm Charts
1. **Reusability:**
    - Helm charts allow developers to package and share their applications as a self-contained unit.
    - This makes it easy to reuse charts across different projects, which can save time and effort.

2. **Versioning:**
    - Helm charts provide a versioning system that helps you manage changes to your application over time.
    - This means that you can easily **roll back to a previous version** if something goes wrong or if you need to test an earlier version.

3. **Dependency management:**
    - Helm charts support dependency management, so you can easily **manage complex application deployments** that **require multiple services or components**.

4. **Configuration management:**
    - With Helm, you can easily manage configuration settings for your application.
    - This means that you can store different configuration files for different environments (such as **Development**, **UAT**, and **Production**) and easily switch between them.

5. **Scalability:**
   - Helm charts are designed to be scalable, which means that **you can easily deploy your application to multiple clusters or nodes**.

6. **Standardization:**
   - Helm charts provide a standardized way to deploy applications on Kubernetes.


## 4. Installing Helm into Linux
Run the following commands
```
# Download Helm
curl -L https://get.helm.sh/helm-v3.11.1-linux-amd64.tar.gz -o helm.tar.gz

# Extract Archive
tar -xvzf helm.tar.gz

# Move to servers bin folder
sudo mv linux-amd64/helm /usr/local/bin/

# Verify installation
helm version
```
### 4.1 K3S Server requirements
```
nano ~/.bashrc
```
Put this ontop of the file
```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
```

You need to reboot
```
# Reboot the server
sudo reboot
```

## 5. A quick tour of Artefacthub
**[https://artifacthub.io/](https://artifacthub.io/)**

1. We will search for postgre
2. we will choose : PostgreSQL packaged by Bitnami
3. The buttons on the RHS
   - Install ( Add repository | Install chart)
   - Templates (All the manifests used for this deployment)
   - Default values (The values-postgresql.yaml file)

**[Grafana Helm Chart using the Bitnami repository](http://rino.kozow.com/kubernetes/posts/helm-chart-grafana-bitnami/)**
