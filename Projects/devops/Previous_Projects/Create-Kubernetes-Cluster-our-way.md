---
title: "Create Kubernetes Cluster Our Way"
date: 2024-07-18T08:39:49Z
draft: false
---
## 1 What Unique about our approach
We have a unique way of creating Kubernetes K3S Clusters no one knows how to do.

## 2. Our requirement
### 2.1 We have the following servers

```
  nickname: Primary Master Node
  ipAddress: 10.154.2.85
  DNS Name: buffalo.loseyourip.com

- nickname: Second Master Node
  ipAddress: 10.154.2.87
  DNS Name: tiger.loseyourip.com

- nickname: Third Master Node
  ipAddress: 10.154.2.90
  DNS Name: kudu.loseyourip.com

- nickname: First Worker Node
  ipAddress: 10.154.2.91
  DNS Name: leopard.loseyourip.com

- nickname: Second Worker Node
  ipAddress: 10.154.2.92
  DNS Name: lion.loseyourip.com

- nickname: Third Worker Node
  ipAddress: 10.154.2.80
  DNS Name: hippo.kozow.com

- nickname: Fourth Worker Node
  ipAddress: 10.154.2.81
  DNS Name: giraffe.kozow.com

```

### 2.2 Scenario 1. Where you have DNS names for all your servers 
You get to name your nodes :
1. buffalo.loseyourip.com (As Primary Master Node)
2. tiger.loseyourip.com (AS Second Master Node)
3. kudu.loseyourip.com (As Third Master Node)
4. leopard.loseyourip.com (As First Worker Node)
5. lion.loseyourip.com (As Second Worker Node)
6. hippo.kozow.com (As Third Worker Node)
7. giraffe.kozow.com (AS Fourth Worker Node)

### 2.3 Scenario 2. Where you dont have DNS names for all your servers/VMs 
You get	to name	your nodes :
1. Madonna1 (As Primary Master Node)
2. Eminem2	(AS Second Master Node)
3. TaylorSwift3 (As Third Master Node)
4. Mulan1 (As First Worker Node)
5. Pocahontas2 (As Second Worker Node)
6. Moana3 (As Third Worker Node)
7. Piglet4 (AS Fourth Worker Node)

## 3. Creating K3S Kunbernetes Clusters using the : K3S_Auto_Cluster_Generator
We have created This Python project in GitHub :

### 3.1 The GitHub Project 
https://github.com/nic0michael/K3S_Auto_Cluster_Generator

### 3.2 Cloning the K3S_Auto_Cluster_Generator

Run these commands on your Primary Master Node Server/VM IP: 10.154.2.85 
```
sudo su -

apt update -y

apt install git nano

mkdir -p /opt/k3s

cd /opt/k3s/

git clone https://github.com/nic0michael/K3S_Auto_Cluster_Generator.git

ls -la

cd K3S_Auto_Cluster_Generator.git

# Make the Python Script executable
chmod 775 auto_generate_k3s_cluster_scripts.pyz

# Edit the Configuration File
nano config.yaml
```
**For Scenario 1. put this in the Configuration file :**
```
---
primaryNode:  
  nickname: Primary Master Node
  ipAddress: 10.154.2.85
  fqdn: buffalo.loseyourip.com
  userId: nickm
  script: Primary-Master-Node-ip-85.sh
  k3sToken: K105db82e39a8ec3a05de569a7a2e4ffdf2fa3c1db21f87fad5b22e764af61d0d96::server:e54cb4ab0d4026b645c8156477ac7220
otherMasterNodes:
- nickname: Second Master Node
  ipAddress: 10.154.2.87
  fqdn: tiger.loseyourip.com
  userId: nickm
  script: Second-Master-Node-ip-87.sh

- nickname: Third Master Node
  ipAddress: 10.154.2.90
  fqdn: kudu.loseyourip.com
  userId: nickm
  script: Third-Master-Node-ip-90.sh
workernodes:
- nickname: First Worker Node
  ipAddress: 10.154.2.91
  fqdn: leopard.loseyourip.com
  userId: nickm
  script: First-Worker-Node-ip-91.sh

- nickname: Second Worker Node
  ipAddress: 10.154.2.92
  fqdn: lion.loseyourip.com
  userId: nickm
  script: Second-Worker-Node-ip-92.sh

- nickname: Third Worker Node
  ipAddress: 10.154.2.80
  fqdn: hippo.kozow.com
  userId: nickm
  script: Third-Worker-Node-ip-80.sh

- nickname: Fourth Worker Node
  ipAddress: 10.154.2.81
  fqdn: giraffe.kozow.com
  userId: nickm
  script: Fourth-Worker-Node-ip-81.sh
targetFile: K3S_Auto_Cluster_Generator_Schedule.txt
```

**For Scenario 2.	put this in the	Configuration file **:
```
---
primaryNode:  
  nickname: Primary Master Node
  ipAddress: 10.154.2.85
  fqdn: Madonna1
  userId: nickm
  script: Primary-Master-Node-ip-85.sh
  k3sToken: K105db82e39a8ec3a05de569a7a2e4ffdf2fa3c1db21f87fad5b22e764af61d0d96::server:e54cb4ab0d4026b645c8156477ac7220
otherMasterNodes:

- nickname: Second Master Node
  ipAddress: 10.154.2.87
  fqdn: Eminem2
  userId: nickm
  script: Second-Master-Node-ip-87.sh

- nickname: Third Master Node
  ipAddress: 10.154.2.90
  fqdn: TaylorSwift3
  userId: nickm
  script: Third-Master-Node-ip-90.sh

workernodes:
- nickname: First Worker Node
  ipAddress: 10.154.2.91
  fqdn: Mulan1
  userId: nickm
  script: First-Worker-Node-ip-91.sh

- nickname: Second Worker Node
  ipAddress: 10.154.2.92
  fqdn: Pocahontas2
  userId: nickm
  script: Second-Worker-Node-ip-92.sh

- nickname: Third Worker Node
  ipAddress: 10.154.2.80
  fqdn: Moana3
  userId: nickm
  script: Third-Worker-Node-ip-80.sh

- nickname: Fourth Worker Node
  ipAddress: 10.154.2.81
  fqdn: Piglet4
  userId: nickm
  script: Fourth-Worker-Node-ip-81.sh
targetFile: K3S_Auto_Cluster_Generator_Schedule.txt
```

## 3.3 Running the K3S_Auto_Cluster_Generator
Run this commands :
```

# Run the script
./auto_generate_k3s_cluster_scripts.pyz

ls -la
```

Expect to get:
```
ls -la
total 80
drwxrwxr-x 4 nickm nickm 4096 Jul 18 11:42 .
drwxrwxr-x 3 nickm nickm 4096 Jul 18 11:38 ..
-rwxrwxr-x 1 nickm nickm 9032 Jul 18 11:38 auto_generate_k3s_cluster_scripts.pyz
-rw-rw-r-- 1 nickm nickm 1190 Jul 18 11:42 config.yaml
-rw-rw-r-- 1 nickm nickm   99 Jul 18 11:42 DeleteMasterNode.sh
-rw-rw-r-- 1 nickm nickm  105 Jul 18 11:42 DeleteWorkerNode.sh
drwxrwxr-x 6 nickm nickm 4096 Jul 18 11:38 Examples
-rw-rw-r-- 1 nickm nickm  530 Jul 18 11:42 First-Worker-Node-ip-91.sh
-rw-rw-r-- 1 nickm nickm  522 Jul 18 11:42 Fourth-Worker-Node-ip-81.sh
drwxrwxr-x 8 nickm nickm 4096 Jul 18 11:38 .git
-rw-rw-r-- 1 nickm nickm 3366 Jul 18 11:42 K3S_Auto_Cluster_Generator_Schedule.txt
-rw-rw-r-- 1 nickm nickm  579 Jul 18 11:42 Primary-Master-Node-ip-85.sh
-rw-rw-r-- 1 nickm nickm 4553 Jul 18 11:38 readme.md
-rw-rw-r-- 1 nickm nickm  804 Jul 18 11:42 Second-Master-Node-ip-87.sh
-rw-rw-r-- 1 nickm nickm  526 Jul 18 11:42 Second-Worker-Node-ip-92.sh
-rw-rw-r-- 1 nickm nickm  800 Jul 18 11:42 Third-Master-Node-ip-90.sh
-rw-rw-r-- 1 nickm nickm  516 Jul 18 11:42 Third-Worker-Node-ip-80.sh
```

Edit the script : Primary-Master-Node-ip-85.sh
```
nano Primary-Master-Node-ip-85.sh
```

expect to get :
```bash
#! /bin/bash
# Primary-Master-Node-ip-85.sh
#######################
# Primary Master Node #
#######################
# IP Address: 10.154.2.85
# FQDN: buffalo.loseyourip.com
# User ID: nickm
# K3s Token: K105db82e39a8ec3a05de569a7a2e4ffdf2fa3c1db21f87fad5b22e764af61d0d96::server:e54cb4ab0d4026b645c8156477ac7220
# 
node_name="buffalo.loseyourip.com"
curl -sfL https://get.k3s.io | K3S_NODE_NAME=${node_name} sh -s - server --cluster-init --write-kubeconfig-mode=644
echo "Please run this command and update token in config.yaml"
echo "cat /var/lib/rancher/k3s/server/node-token"
```

## 4. Creating the High Availability Cluster
### 4.1 Creating our Primary Master Node
Run these commands on your Primary Master Node Server/VM:
```
sudo su -

cd /opt/k3s/K3S_Auto_Cluster_Generator

chmod 775 *.sh

# Build the cluster with Primary Master Node
./Primary-Master-Node-ip-85.sh

```
Expect to get :
```


Please run this command and update token in config.yaml
cat /var/lib/rancher/k3s/server/node-token
```

### 4.2 Get Primary Master Node Security Token
Run this command :
```
# Get the New Security Token
cat /var/lib/rancher/k3s/server/node-token
```

Expect to get New Token :
```

```
### 4.3 Put the New Security Token in the configuration file
Run this command :
```
# Edit the Configuration File
nano config.yaml
```
Change the k3sToken with this token
```
  k3sToken: K105db82e39a8ec3a05de569a7a2e4ffdf2fa3c1db21f87fad5b22e764af61d0d96::server:e54cb4ab0d4026b645c8156477ac7220
```


### 4.4 Run the K3S_Auto_Cluster_Generator Again
Run this commands :
```

# Run the script
./auto_generate_k3s_cluster_scripts.pyz

# We have now generated all the scripts to generate rest of the nodes to commect t Primary Master Node
ls -la
```

## 5 Generate the rest of the nodes
We need to run  these commands on all the Other Servers/VMs :
```
mkdir -p /opt/k3s

cd /opt/k3s/
```

### 5.1 Then we need to scp the scripts to each server 
Run these commands :
```
# Before running these commands run this on all of these servers
sudo chown nickm: /opt/k3s

# Now run these commands:
scp /opt/k3s/K3S_Auto_Cluster_Generator/Second-Master-Node-ip-87.sh nickm@10.154.2.87:/opt/k3s/
scp /opt/k3s/K3S_Auto_Cluster_Generator/Third-Master-Node-ip-90.sh nickm@10.154.2.90:/opt/k3s/

scp /opt/k3s/K3S_Auto_Cluster_Generator/First-Worker-Node-ip-91.sh nickm@10.154.2.91:/opt/k3s/
scp /opt/k3s/K3S_Auto_Cluster_Generator/Second-Worker-Node-ip-92.sh nickm@10.154.2.92:/opt/k3s/
scp /opt/k3s/K3S_Auto_Cluster_Generator/Third-Worker-Node-ip-80.sh nickm@10.154.2.80:/opt/k3s/
scp /opt/k3s/K3S_Auto_Cluster_Generator/Fourth-Worker-Node-ip-81.sh nickm@10.154.2.81:/opt/k3s/

```
### 5.2 Get these servers to join the cluster
Run these commands on each server
```
sudo su -

cd /opt/k3s

ls -la

# run the uploaded script
./Second-Master-Node-ip-87.sh
```

