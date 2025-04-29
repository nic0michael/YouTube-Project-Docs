---
title: "Install Helm on Linux"
date: 2023-01-25T12:35:30Z
draft: false
---

**Helm is available in Github:** \
https://github.com/helm/helm

**To find the letest Stable release of Helm:** \
**https://github.com/helm/helm/releases** 

We need a later release than 3.5
https://get.helm.sh/helm-v3.11.1-linux-amd64.tar.gz


## 1. To install Helm on CentOS, you can use the following steps:

### 2.1 Download the Helm package using the following command:
```
curl -L https://get.helm.sh/helm-v3.11.1-linux-amd64.tar.gz -o helm.tar.gz
```
### 2.2 Extract the package:
```
tar -xvzf helm.tar.gz
```
### 2.3 Move the helm binary to the /usr/local/bin directory:
```
sudo mv linux-amd64/helm /usr/local/bin/
```
### 2.4 Verify the installation by checking the version of Helm:
```
helm version
```
### 2.5 Initialize Helm by running the following command
```
helm init
```
### 2.6 To install a specific version of Helm
You can use the command in step 1 and replace the version number in the URL to the version you want to install.
Please note that these instructions are for Helm version 3.5.2, you can check the latest version of Helm on the official website of Helm and replace the version number in the commands accordingly.

Note that you may need to run the commands with sudo prefix if you don't have the appropriate privilege.
Note: make sure the version you are installing is compatible with your k8s cluster version.

## 2. To install Helm on Ubuntu Linux, you can use the following steps:

### 2.1 Download the Helm package using the following command:
```
curl -L https://get.helm.sh/helm-v3.11.1-linux-amd64.tar.gz -o helm.tar.gz
```
### 2.2 Extract the package:
```
tar -xvzf helm.tar.gz
```
### 2.3 Move the helm binary to the /usr/local/bin directory:
```
sudo mv linux-amd64/helm /usr/local/bin/
```
### 2.4 Verify the installation by checking the version of Helm:
```
helm version
```
### 2.5 Initialize Helm by running the following command
```
helm init
```
Note: make sure the version you are installing is compatible with your k8s cluster version.

### 2.5 Running Helm on K3S
You will get this error:
```
Error: Kubernetes cluster unreachable: Get "http://localhost:8080/version?timeout=32s": dial tcp 127.0.0.1:8080: connect: connection refused
```

Edit the .bashrc file:
```
sudo su -
nano ~/.bashrc
```

put this in the top of this file:
```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
```
 





