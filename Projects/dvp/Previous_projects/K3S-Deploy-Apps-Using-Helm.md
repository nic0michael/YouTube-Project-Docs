---
title: "K3S Deploy Apps Using Helm"
date: 2022-12-29T08:05:20Z
draft: true
---

## The Helm Chart
Today most developers of Applications like ElasticSearch provide Helm Charts to simplify the deployment of these applications on Kubernetes or K3S

# 1. Install Helm on Centos

## Add the Epel Repository to be able to install Snap:
```
sudo yum install epel-release
```

## Install Snap:
```
sudo yum install snapd
```

## Enable the Snap communication Socket:
```
sudo systemctl enable --now snapd.socket
```

## Ctreate a Sym Link to snap folder
```
sudo ln -s /var/lib/snapd/snap /snap
```

## Install Helm
You may need to wait a minute or two first
```
sudo snap install helm --classic
```

# 2. Install Helm on Ubuntu
There are different ways to install Helm

## 2.1 Install Helm Using Snap
```
snap install helm --classic
```

## 2.2 Install Helm by downloading the binary
```
wget https://get.helm.sh/helm-v3.8.2-linux-amd64.tar.gz
```
### Extract the TarBall
```
tar xvf helm-*-linux-amd64.tar.gz
```

### make Helm a terminal shortcut
```
sudo mv linux-amd64/helm /usr/local/bin
```

### Check Helm (version)
```
helm version
```

## 2.3 Alternativly Install the Helm CLI this way

### Install Helm CLI:
```
wget https://get.helm.sh/helm-v3.4.0-linux-amd64.tar.gz
tar -C /tmp/ -zxvf helm-v3.10.1-linux-amd64.tar.gz
#rm helm-v3.10.1-linux-amd64.tar.gz
mv /tmp/linux-amd64/helm /usr/local/bin/helm
chmod +x /usr/local/bin/helm
```

### Install Newer version:
```
wget https://get.helm.sh/helm-v3.10.1-linux-amd64.tar.gz
tar -C /tmp/ -zxvf helm-v3.10.1-linux-amd64.tar.gz
#rm helm-v3.10.1-linux-amd64.tar.gz
mv /tmp/linux-amd64/helm /usr/bin/helm
chmod +x /usr/bin/helm
```

# 3. Using Helm to Install ElasticSearch

## Visit the ElsticSearch Artefact site: 
https://artifacthub.io/packages/helm/elastic/elasticsearch

## Installing version 7.17.3 
This chart is tested with the latest 7.17.3 version.

Install released version using Helm repository \
Add the Elastic Helm charts repo: helm repo add elastic https://helm.elastic.co


## Install it using Helm 3: 
```
helm install elasticsearch --version 7.17.3 elastic/elasticsearch
```
