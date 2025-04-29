---
title: "Install and Use K9S in Proxmox Kubernetes Master Node"
date: 2025-03-15T07:23:23Z
draft: false
---
## A. Prerequisites
We assume you have created a Kubernetes installation and deployed Nginx there \
If not please refer to this video: \
https://youtu.be/wRxf0zz6T2Y

## B. Reasons to use K9S when working with Kubernetes
1. **Efficiency** – K9s provides a fast, terminal-based UI for navigating and managing Kubernetes resources without needing long `kubectl` commands.  
2. **Live Monitoring** – It offers real-time updates on pods, logs, and events, making troubleshooting easier.  
3. **Automation & Shortcuts** – Built-in hotkeys and commands speed up common tasks like scaling, deleting, or editing resources.

## C. Usefull Websites
**[https://k9scli.io/topics/commands/](https://k9scli.io/topics/commands/)**
## 1. Install K9S in Ubuntu Master Node
**login as root**
```bash
ssh nickm@10.154.2.41

sudo su -
```
**Download the K9s Debian Package**
```bash
mkdir /opt/k9s -p

cd /opt/k9s

wget https://github.com/derailed/k9s/releases/download/v0.32.5/k9s_linux_amd64.deb
```
**Install K9s**
```bash
apt install ./k9s_linux_amd64.deb -y
```

## 2. working with K9S in the Kubernetes Cluster
**Check that the cluster is up**
```bash
kubectl get nodes

kubectl get namespaces

kubectl get pods -n nginx
```

## 3. working with K9S
**Run these commands**
```bash
#k9s is used for other Kubernetes implementations

k9s --kubeconfig /etc/rancher/k3s/k3s.yaml

0
```
### 3.1 To get the shortcut keys
```bash
?
```
### 3.2 To get namespaces
```bash
0
```
### 3.3 To set namespace
```
: ns
# navigate to nginx and press enter
```

### 3.4 To describe POD
```
# Navigate up or down to POD Press d
d
```
### 3.5 To get YAML of POD
```
# Navigate up or down to POD Press y
y
```
### 3.6 Accessing the Logs
### 3.6.1 Select the Time Range in the log
```bash
1 ( All logs)
2 (Over 5 minutes)
3 (Over 15 minutes)
4 (Over 30 minutes)
0 (Over Entire log)
```

### 3.6.2 to Short List of the Log of a POD
```
# Navigate up or down to POD Press l 2
l 2
```

### 3.6.3 to get a Long List of the Log of a POD
```
# Navigate up or down to POD Press l 1
l 1
```

### 3.6.4 to get a List of the all Log of a POD
```
# Navigate up or down to POD Press l 0
l 0
```
### 3.6.5 To copy from logs
```bash
c
```

### 3.7 To enter Exec mode with shell script
```bash
s
```
### 3.7.1 To exit the from the Command mode
```bash
exit
```


### 3.8 To list Services:
```bash
:svc
```
### 3.8.1 To describe a service
```
# Navigate up or down to Service Press d
d
```
### 3.8.2 To get YAML of a Service
```
# Navigate up or down to Service  Press y
y
```

### 3.9 To list Ingresses
```bash
:ing
```
### 3.9.1 To describe a Ingresses
```
# Navigate up or down to Ingresses Press d
d
```
### 3.9.2 To get YAML of a Ingresses
```
# Navigate up or down to Ingress Press y
y
```

# 3.10 To exit K9s
```bash
: q!
```

