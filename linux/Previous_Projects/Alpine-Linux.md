---
title: "Alpine Linux VM for K3S Cluster"
date: 2023-06-04T18:33:43Z
draft: false
---

## Download Alpine Linux:
[Download Alpine Linux](https://www.alpinelinux.org/downloads/)


## The Alpine Linux Wiki:
[Alpine linux - Tutorials and Howtos](https://wiki.alpinelinux.org/wiki/Tutorials_and_Howtos)

## Get the K3S_Auto_Cluster_Generator:
https://github.com/nic0michael/K3S_Auto_Cluster_Generator

---

## 1. Create an Alpine VM in the proxmox VM server
### 1.1 Specifications to Create Proxmox VM
IMAGE: Minimum Image

Optionally [*] QME Agent

disk [32GB] // we may want more for kubernetes

Sockets[1]
CPU[2] Cores

MEMORY [2] GB RAM // we may want more for kubernetes

---


## 2. Alpine O/S Setup

Initially login as root no password

```
setup-alpine

#keyboard layout
us

#hostname
alpine


#alvailable interfaces eth0
[eth0]


# IP Address or DHCP 
10.154.2.98

# Manual Configuration 
n // no

## Follow wizzard

# password for root

# timzone [UTC]
// press enter to leave as UTC

# proxy
// press enter for none

# mirror (if you can find the code for mirror)
1 // I chose 1

# do you want to create a user
nickm // none if you want to login as root

# whick SSH server do you want to install
// prefer openssh

# allow root to ssh login
yes// if you dont have a user

# do you have ssh key
// press enter for none

# select hard drive that available
sda

# how would you like to use it
sys // to put all together

# do you want to erase
// yes


reboot

```
---

## Additional Alpine Linux setups
```
df -h

ls -la /etc/apk

vi /etc/apk/repositories

# uncomment v16 community
i // to edit

[esc] :
wq[enter]


```

---

## 3. Installing applications
```
# install apps needed for Kubernetes
apk add nano

apk add curl

apk add tree

# other optional applications
apk add htop

# test this
htop

reboot
```
---

## 4. Creating K3S Kubernetes Cluster

### 4.1 Creating K3S Master Node

```

#######################
# Primary Master Node #
#######################
# IP Address: 10.154.2.93
# FQDN: kudu.loseyourip.com
# User ID: root
node_name="kudu.loseyourip.com"
curl -sfL https://get.k3s.io | K3S_NODE_NAME=${node_name} sh -s - server --cluster-init

##############################
# After creating master Node #
##############################
# Get Token
cat /var/lib/rancher/k3s/server/node-token
```
---

### 4.2 Creating Worker Nodes
```
# First-Worker-Node-ip-95.sh
#####################
# First Worker Node #
#####################
# IP Address: 10.154.2.93
# FQDN: leopard.loseyourip.com
# User ID: nickm
# Primary Master Node IP Address: 10.154.2.93
# 
k3s_token="K1086a8d54a028161f9edddb36941a6b559396a23d7be37933657a2aabfbcfc2911::server:484d90102f28bfec5808529ff4673729"
k3s_url="https://10.154.2.93:6443"
node_name="leopard.loseyourip.com"
curl -sfL https://get.k3s.io | K3S_URL=${k3s_url} K3S_TOKEN=${k3s_token} K3S_NODE_NAME=${node_name} sh -

# Second-Worker-Node-ip-97.sh
#######################
# Second Worker Node #
#######################
# IP Address: 10.154.2.97
# FQDN: lion.loseyourip.com
# User ID: nickm
# Primary Master Node IP Address: 10.154.2.93
# 
k3s_token="K1086a8d54a028161f9edddb36941a6b559396a23d7be37933657a2aabfbcfc2911::server:484d90102f28bfec5808529ff4673729"
k3s_url="https://10.154.2.93:6443"
node_name="lion.loseyourip.com"
curl -sfL https://get.k3s.io | K3S_URL=${k3s_url} K3S_TOKEN=${k3s_token} K3S_NODE_NAME=${node_name} sh -

```
---

### 4.3 Destroying Master Node
```
# DeleteMasterNode.sh
#
/usr/local/bin/k3s-killall.sh
/usr/local/bin/k3s-uninstall.sh
```

### 4.4 Destroying Worker Node
```
# DeleteWorkerNode.sh
#
/usr/local/bin/k3s-killall.sh
/usr/local/bin/k3s-agent-uninstall.sh
```
## Shutdown & reboot commands
```
poweroff

halt

reboot
```

---

## Bibliography
[Super Small Footprint Yet Simple To Use Linux Distro Alpine Linux](https://www.youtube.com/watch?v=dG4mCHsyqHk)
[K3S_Auto_Cluster_Generator](https://github.com/nic0michael/K3S_Auto_Cluster_Generator)

