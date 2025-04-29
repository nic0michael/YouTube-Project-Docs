---
title: "Add a Production Grade Kubernetes cluster to Your Proxmox Homelab"
date: 2023-03-21T06:23:46Z
draft: false
---
## 1. Why we will use Rancher Kubernetes K3S instead of Google Kubernetes K8S
1. K3S is a fully complaint production Grade Kubernetes distribution
2. If we look at the size of the binaries K3S is only 40mByte where as K8S is 300MByte
3. K3S is so small you can run it on Raspberry Pi4
4. If IoT or Edge Computing is your game then K3S is for you
5. K3S is a fork of the original Google Kubernetes K8S with the "Bloat thrown out"
 
## 2. If you have not installed Proxmox yet
Here we only use DELL, you should be able to get a second hand DELL mini Tower PC with an Intel i7 CPU and space on the motherboard to fit 32GB of RAM to use as your Proxmox server

Its an excelent opertunity to look at the following Proxmox Training: \
From the Learn Linux Channel we get, probably the best Proxmox Training here : \
https://www.youtube.com/watch?v=LCjuiIswXGs&t=1097s

We suggest you save a link to our video:

Now look at the Learn Linux Channel and comeback to this video

## 3. Congratulations on installing Proxmox
Now we are ready to Rock

**We recommend you create Ubuntu Server 2204.1 or newer VMs for each Node Server.**

## 4. The Kubernetes Clusters

### 4.1 Type of K3S clusters we can create
1. A K3S-HA High available Production Cluster 
2. A Standard (one Master) many worker Cluster
3. A minimal system

### 4.2 The K3S Cluster requirements
1. A K3S-HA Cluster requires 3, or 5, or 7 Master Node Servers,and as many worker Node Servers as you can provision.
2. A Standard Cluster requires one Master Node Server and 3 or more worker Node Servers
3. A minimal system requires one Master Node Server and optional one worker Node Server

### 4.3 Other requirements
1. You wont need a DNS Certificate
2. You will however need a FQDN (Fully Qualified Domain Name like: rabbit.loseyourip.com)
3. We use [Free dynamic DNS service | Dynu Systems, Inc.](https://www.dynu.com/en-US) they provide 4 free FQDN's for free on sigining up

## 5. Using the K3S Cluster Maker Python Software
After battelling for a month to get K3S-HA and other clusters to work and to make them work reliablly we put all we learned into this unique program that works repeatedly and reliably every time. \
In GitHub you will find our K3S Cluster Maker here:
[https://github.com/nic0michael/K3S_Cluster_Maker](https://github.com/nic0michael/K3S_Cluster_Maker)
If you want you can clone this project

### 5.1 How this works
1. We provide you with a YAML file to configure your cluster : AllNodesConfig.yaml
2. You edit this file and provide all the information about the servers in your cluster
3. We will run a Python script which will generate your generate your	Cluster	Deploment Schedule
4. You will use your Cluster Deploment Schedule a text file to do a guided deployment of your K3S cluster

### 5.2 Create a local copy of the AllNodesConfig.yaml file
```
nano AllNodesConfig.yaml
```
put this into the file
```
---
primaryNode:
  nickname: Primary Master Node
  ipAddress: 10.154.2.88
  fqdn: buffalo.loseyourip.com
  userId: nickm
  k3sToken: K1026e692c09900990359e4959bea4c6b5bf943ddb7b2ca1d4bb7edfae7bff181da::server:c2f4a96a9a957e2c838c33c2f5707164
otherMasterNodes:
- nickname: Second Master Node
  ipAddress: 10.154.2.93
  fqdn: kudu.loseyourip.com
  userId: nickm
- nickname: Third Master Node
  ipAddress: 10.154.2.89
  fqdn: upupu.loseyourip.com
  userId: nickm
workernodes:
- nickname: First Worker Node
  ipAddress: 10.154.2.4
  fqdn: rabbit.loseyourip.com
  userId: nickm
- nickname: Second Worker Node
  ipAddress: 10.154.2.188
  fqdn: tiger.loseyourip.com
  userId: nickm
targetFile: cluster_deployment_schedule.txt
```


### 5.3 Provide the information about the Primary Master Node Server
Edit the AllNodesConfig.yaml file
```
nano AllNodesConfig.yaml
```

Replace this information with information about your Primary Master Node Server
```
primaryNode:
  nickname: Primary Master Node
  ipAddress: 10.154.2.88             <--------- your servers IP address (you used as you registered on Dynu DNS) 
  fqdn: buffalo.loseyourip.com       <--------- your servers FQDN (as you registered on Dynu DNS)
  userId: nickm                      <--------- the user you will use to ssh to this server
```
### 5.4 Provide the information about other Master Node Servers for K3S-HA
Edit the AllNodesConfig.yaml file
```
nano AllNodesConfig.yaml
```
We will replace the list (array) of Master Node Servers with all of your Other Master Node Servers \
However if you are not creating a High availability dont change this
```
otherMasterNodes:
- nickname: Second Master Node
  ipAddress: 10.154.2.93
  fqdn: kudu.loseyourip.com
  userId: nickm
- nickname: Third Master Node
  ipAddress: 10.154.2.89
  fqdn: upupu.loseyourip.com
  userId: nickm
```

### 5.5 Provide the information about your Worker Node Servers
Edit the AllNodesConfig.yaml file
```
nano AllNodesConfig.yaml
```

Replace the information about your Worker Node Servers
```
workernodes:
- nickname: First Worker Node
  ipAddress: 10.154.2.4
  fqdn: rabbit.loseyourip.com
  userId: nickm
- nickname: Second Worker Node
  ipAddress: 10.154.2.188
  fqdn: tiger.loseyourip.com
  userId: nickm
```

### 5.6 Create a copy of the Python script : k3s-cluster-maker-4-linux.pyz 
Create the file k3s-cluster-maker-4-linux.pyz 
```
nano k3s-cluster-maker-4-linux.pyz 
```

Put this code into the file
```
#!/usr/bin/env python3

import yaml

# Read the AllNodes.yaml file
with open("AllNodesConfig.yaml", "r") as f:
    nodes = yaml.safe_load(f)

# Generate the target file
with open(nodes["targetFile"], "w") as f:
    # Primary Master Node
    f.write("#######################\n")
    f.write("# " + nodes["primaryNode"]["nickname"] + " #\n")
    f.write("#######################\n")
    f.write("ssh " + nodes["primaryNode"]["userId"] + "@" + nodes["primaryNode"]["ipAddress"] + "\n")
    f.write("sudo su -\n")
    f.write("/usr/local/bin/k3s-killall.sh\n")
    f.write("/usr/local/bin/k3s-agent-uninstall.sh\n")
    f.write("/usr/local/bin/k3s-uninstall.sh\n")
    f.write("\n")
    f.write('node_ip="' + nodes["primaryNode"]["ipAddress"] + '"\n')
    f.write('node_name="' + nodes["primaryNode"]["fqdn"] + '"\n')
    f.write('curl -sfL https://get.k3s.io | K3S_NODE_IP=${node_ip} K3S_NODE_NAME=${node_name} sh -s - server --cluster-init\n')
    f.write("\n")
    f.write("cat /var/lib/rancher/k3s/server/node-token\n")
    f.write("Update the token in this file\n\n")

    # Other Master Nodes
    for node in nodes["otherMasterNodes"]:
        f.write("#######################\n")
        f.write("# " + node["nickname"] + " #\n")
        f.write("#######################\n")
        f.write("ssh " + node["userId"] + "@" + node["ipAddress"] + "\n")
        f.write("sudo su -\n")
        f.write("/usr/local/bin/k3s-killall.sh\n")
        f.write("/usr/local/bin/k3s-uninstall.sh\n")
        f.write("/usr/local/bin/k3s-agent-uninstall.sh\n")
        f.write("\n")
        f.write('k3s_token="' + nodes["primaryNode"]["k3sToken"] + '"\n')
        f.write('k3s_url="https://' + nodes["primaryNode"]["ipAddress"] + ':6443"\n')
        f.write('node_ip="' + node["ipAddress"] + '"\n')
        f.write('node_name="' + node["fqdn"] + '"\n')
        f.write('curl -sfL https://get.k3s.io | K3S_TOKEN=${k3s_token} K3S_NODE_IP=${node_ip} K3S_NODE_NAME=${node_name} sh -s - server --server ${k3s_url}\n\n')

    # Worker Nodes
    for node in nodes["workernodes"]:
        f.write("#######################\n")
        f.write("# " + node["nickname"] + " #\n")
        f.write("#######################\n")
        f.write("ssh " + node["userId"] + "@" + node["ipAddress"] + "\n")
        f.write("sudo su -\n")
        f.write("/usr/local/bin/k3s-killall.sh\n")
        f.write("/usr/local/bin/k3s-uninstall.sh\n")
        f.write("/usr/local/bin/k3s-agent-uninstall.sh\n")
        f.write("\n")
        f.write('k3s_token="' + nodes["primaryNode"]["k3sToken"] + '"\n')
        f.write('k3s_url="https://' + nodes["primaryNode"]["ipAddress"] + ':6443"\n')
        f.write('node_ip="' + node["ipAddress"] + '"\n')
        f.write('node_name="' + node["fqdn"] + '"\n')
        f.write('curl -sfL https://get.k3s.io | K3S_URL=${k3s_url} K3S_TOKEN=${k3s_token} K3S_NODE_IP=${node_ip} K3S_NODE_NAME=${node_name} sh -\n\n')

```

### 5.7 Make this script executable
run the following command
```
chmod 775 k3s-cluster-maker-4-linux.pyz

# or for those sysadmins
chmod +x k3s-cluster-maker-4-linux.pyz

# is it executble
ls -la
```

### 5.8 Generate your Cluster Deploment Schedule : cluster_deployment_file.txt
Run the following command
```
./k3s-cluster-maker-4-linux.pyz

# Check to see if your Cluster Deploment Schedule was generated
ls -la *.txt
```

expect to get:
```
ls -la *.txt
-rw-r--r-- 1 root root 2644 Mar 18 18:10 cluster_deployment_schedule.txt
```
list the scedule
```
cat cluster_deployment_schedule.txt
```
**Please Note:** \
**k3s-agent-uninstall.sh** is used ti kill Worker Node Servers \
**k3s-uninstall.sh** is used ti kill Master Node Servers


expect to get:
```
cat cluster_deployment_file.txt
#######################
# Primary Master Node #
#######################
ssh nickm@10.154.2.88
sudo su -
/usr/local/bin/k3s-killall.sh
/usr/local/bin/k3s-agent-uninstall.sh
/usr/local/bin/k3s-uninstall.sh

node_ip="10.154.2.88"
node_name="buffalo.loseyourip.com"
curl -sfL https://get.k3s.io | K3S_NODE_IP=${node_ip} K3S_NODE_NAME=${node_name} sh -s - server --cluster-init

cat /var/lib/rancher/k3s/server/node-token
Update the token in this file

#######################
# Second Master Node #
#######################
ssh nickm@10.154.2.93
sudo su -
/usr/local/bin/k3s-killall.sh
/usr/local/bin/k3s-uninstall.sh
/usr/local/bin/k3s-agent-uninstall.sh

k3s_token="K105db82e39a8ec3a05de569a7a2e4ffdf2fa3c1db21f87fad5b22e764af61d0d96::server:e54cb4ab0d4026b645c8156477ac7220"
k3s_url="https://10.154.2.88:6443"
node_ip="10.154.2.93"
node_name="kudu.loseyourip.com"
curl -sfL https://get.k3s.io | K3S_TOKEN=${k3s_token} K3S_NODE_IP=${node_ip} K3S_NODE_NAME=${node_name} sh -s - server --server ${k3s_url}

#######################
# Third Master Node #
#######################
ssh nickm@10.154.2.89
sudo su -
/usr/local/bin/k3s-killall.sh
/usr/local/bin/k3s-uninstall.sh
/usr/local/bin/k3s-agent-uninstall.sh

k3s_token="K105db82e39a8ec3a05de569a7a2e4ffdf2fa3c1db21f87fad5b22e764af61d0d96::server:e54cb4ab0d4026b645c8156477ac7220"
k3s_url="https://10.154.2.88:6443"
node_ip="10.154.2.89"
node_name="upupu.loseyourip.com"
curl -sfL https://get.k3s.io | K3S_TOKEN=${k3s_token} K3S_NODE_IP=${node_ip} K3S_NODE_NAME=${node_name} sh -s - server --server ${k3s_url}

#######################
# First Worker Node #
#######################
ssh nickm@10.154.2.4
sudo su -
/usr/local/bin/k3s-killall.sh
/usr/local/bin/k3s-uninstall.sh
/usr/local/bin/k3s-agent-uninstall.sh

k3s_token="K105db82e39a8ec3a05de569a7a2e4ffdf2fa3c1db21f87fad5b22e764af61d0d96::server:e54cb4ab0d4026b645c8156477ac7220"
k3s_url="https://10.154.2.88:6443"
node_ip="10.154.2.4"
node_name="rabbit.loseyourip.com"
curl -sfL https://get.k3s.io | K3S_URL=${k3s_url} K3S_TOKEN=${k3s_token} K3S_NODE_IP=${node_ip} K3S_NODE_NAME=${node_name} sh -

#######################
# Second Worker Node #
#######################
ssh nickm@10.154.2.188
sudo su -
/usr/local/bin/k3s-killall.sh
/usr/local/bin/k3s-uninstall.sh
/usr/local/bin/k3s-agent-uninstall.sh

k3s_token="K105db82e39a8ec3a05de569a7a2e4ffdf2fa3c1db21f87fad5b22e764af61d0d96::server:e54cb4ab0d4026b645c8156477ac7220"
k3s_url="https://10.154.2.88:6443"
node_ip="10.154.2.188"
node_name="tiger.loseyourip.com"
curl -sfL https://get.k3s.io | K3S_URL=${k3s_url} K3S_TOKEN=${k3s_token} K3S_NODE_IP=${node_ip} K3S_NODE_NAME=${node_name} sh -

```
## 6. Creating your K3S Cluster using Cluster Deploment Schedule
### 6.1 Create all you Node Servers
It is recommended for a home lab to use Ubuntu Server 2204.2 
You need to insure that all these servers have the IP addresses as identifying them in the Cluster Deploment Schedule

### 6.2 Create the K3S Cluster
Using the Cluster Deploment Schedule start by creating the Primary/First Master node\
Should this server have been used in a previous cluster the first three commands will uninstall K3S
```
#######################
# Primary Master Node #
#######################
ssh nickm@10.154.2.88
sudo su -
/usr/local/bin/k3s-killall.sh
/usr/local/bin/k3s-agent-uninstall.sh
/usr/local/bin/k3s-uninstall.sh

node_ip="10.154.2.88"
node_name="buffalo.loseyourip.com"
curl -sfL https://get.k3s.io | K3S_NODE_IP=${node_ip} K3S_NODE_NAME=${node_name} sh -s - server --cluster-init

cat /var/lib/rancher/k3s/server/node-token
Update the token in this file
```
It is important that you update your Cluster Deploment Schedule so that all the k3s_token= values use this Master Servers token \
if you dont do this none of all the other Node Servers will be able to Authenticate withe this Master Server 


### 6.3 Create a K3S Worker Node
```
#######################
# Second Worker Node #
#######################
ssh nickm@10.154.2.188
sudo su -
/usr/local/bin/k3s-killall.sh
/usr/local/bin/k3s-uninstall.sh
/usr/local/bin/k3s-agent-uninstall.sh

k3s_token="K105db82e39a8ec3a05de569a7a2e4ffdf2fa3c1db21f87fad5b22e764af61d0d96::server:e54cb4ab0d4026b645c8156477ac7220"
k3s_url="https://10.154.2.88:6443"
node_ip="10.154.2.188"
node_name="tiger.loseyourip.com"
curl -sfL https://get.k3s.io | K3S_URL=${k3s_url} K3S_TOKEN=${k3s_token} K3S_NODE_IP=${node_ip} K3S_NODE_NAME=${node_name} sh -

```

