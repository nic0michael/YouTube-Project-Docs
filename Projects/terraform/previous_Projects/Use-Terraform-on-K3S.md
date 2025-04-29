---
title: "Use Terraform on K3S"
date: 2023-08-19T07:33:13Z
draft: false
---
**This tutorial is part 1 of our Terraform/K3S Kubernetes series** \
We will provide a link next week to the Video for Part 2 as soon as we publish it

## Preface
In this lesson we are going to do the following:

1. **Create an Ubuntu VM** using our Ubuntu Server Template in our Proxmox VM Server
2. Download the **K3S_Auto_Cluster_Generator** from our GitHub Repository and Configure it for our Vm servers
3. **Create a K3S Kubernetes Cluster** using our K3S_Auto_Cluster_Generator
4. **Install Helm** on our K3S Master Node Ubuntu VM Server
5. **Install Terraform** on our K3S Master Node Ubuntu VM Server
6. Create and Apply a simple **Terraform Kubernetes Namespace project** to work and deploy to our K3S Kubernetes cluster

We will have learned how to use the Kubernetes Provider for Terraform to deploy infrastructure changes in our K3S Kubernetes Cluster

## 1 Creating an Ubuntu VM Server
We will create a Proxmox 
### 1.1 Create Ubuntu VM in proxmox 
We will	create a Proxmox VM using our Ubuntu Server Template: VM-ubuntu-server-template call it : **TerraFormK3S** \
We will create a Priveledged user **nickm**

### 1.2 Change the IP Address on Ubuntu server Set Static IP address
You need to ssh to this server in proxmox console get ip address
```
ip a
```

We got 10.154.2.108/24

SSH to the VM from a terminal session
```
ssh nickm@10.154.2.108
```

Find the file : 1-network-manager-all.yaml
```
ls -la /etc/netplan/
# Expect
-rw-r--r--   1 root root   104 Feb  8  2018 1-network-manager-all.yaml

```

```
sudo nano /etc/netplan/1-network-manager-all.yaml
```
Comment out the top part of the file use #
put this in the bottom of the file

Identify the name of your network device **eno1** you will need this later
change the network device

```
network:
  version: 2
  renderer: networkd
  ethernets:
    ens3:
      dhcp4: no
      addresses:
        - 192.168.121.221/24
        - 10.154.2.93/24
      gateway4: 10.154.2.3
      nameservers:
          addresses: [8.8.8.8, 1.1.1.1]
```
revised yaml file with network device **eno1**
```
network:
  version: 2
  renderer: networkd
  ethernets:
    eno1:
      dhcp4: false
      dhcp6: false
      addresses:
       - 10.154.2.93/24
      routes:
       - to: default
         via: 10.154.2.3
      nameservers:
        addresses: [8.8.8.8,8.8.4.4]
```

Now run this command:
```
sudo netplan apply
```
Show the Ip of the device ens3
```
ip addr show dev ens3
```

Connect to VM server vis SSH
```
ssh nickm@10.154.2.93
```

### 1.3 Install needed apps on VM Server
```
sudo apt install nano
sudo aptinstall tree
```
### 1.4 create a free account at DynuDNS and register the IP of your server
http://www.dynu.com/en-US \

I created: for my Kubernetes Master Node
```
kudu.loseyourip.com
10.154.2.93
```


## 2. K3S Kubernetes Installation
http://rino.kozow.com/kubernetes/posts/add-production-grade-kubernetes-to-your-proxmox-homelab/ \
Instructions : \
https://www.youtube.com/watch?v=MsHewlV75SA

### 2.1 Install Git and Download the K3S_Auto_Cluster_Generator
```
sudo apt install git
```

Create folder and download K3S_Auto_Cluster_Generator
```
sudo chown nickm: /opt

cd /opt/

git clone https://github.com/nic0michael/K3S_Auto_Cluster_Generator.git

cd K3S_Auto_Cluster_Generator/

nano config.yaml
```
put code similar to this using your servers (FQDNs Fully Qualified DNS Name)
```
---
primaryNode:
  nickname: Primary Master Node
  ipAddress: 10.154.2.93
  fqdn: kudu.loseyourip.com
  userId: nickm
  script: Primary-Master-Node-ip-89.sh
  k3sToken: K103c5e8d10025bf149c776c7da853d6752968152ca6625881f84194ead1a824233::server:901b1482ca2d4aef0c0314161ea75e16
otherMasterNodes:
- nickname: Second Master Node
  ipAddress: 10.154.2.88
  fqdn: buffalo.loseyourip.com
  userId: nickm
  script: Second-Master-Node-ip-88.sh
- nickname: Third Master Node
  ipAddress: 10.154.2.4
  fqdn: rabbit.loseyourip.com
  userId: nickm
  script: Third-Master-Node-ip-4.sh
workernodes:
- nickname: First Worker Node
  ipAddress: 10.154.2.95
  fqdn: leopard.loseyourip.com
  userId: nickm
  script: First-Worker-Node-ip-95.sh
- nickname: Second Worker Node
  ipAddress: 10.154.2.97
  fqdn: lion.loseyourip.com
  userId: nickm
  script: Second-Worker-Node-ip-97.sh
targetFile: K3S_Auto_Cluster_Generator_Schedule.txt

```

Run the auto_generate_k3s_cluster_scripts.pyz Python Script :
```
./auto_generate_k3s_cluster_scripts.pyz
```

expect to get
```
generatePrimaryMasterNodeScriptPrimary called  script: Primary-Master-Node-ip-89.sh
generateOtherMasterNodeScript called  script: Second-Master-Node-ip-88.sh
generateOtherMasterNodeScript called  script: Third-Master-Node-ip-4.sh
generateWorkerNodeScript called  script: First-Worker-Node-ip-95.sh
generateWorkerNodeScript called  script: Second-Worker-Node-ip-97.sh

```
```
ls -la
```
expect:
```
total 72
drwxrwxr-x 4 nickm nickm 4096 Aug 19 16:43 .
drwxr-xr-x 3 nickm nickm 4096 Aug 19 16:19 ..
drwxrwxr-x 8 nickm nickm 4096 Aug 19 16:19 .git
-rw-rw-r-- 1 nickm nickm   99 Aug 19 16:43 DeleteMasterNode.sh
-rw-rw-r-- 1 nickm nickm  105 Aug 19 16:43 DeleteWorkerNode.sh
drwxrwxr-x 6 nickm nickm 4096 Aug 19 16:19 Examples
-rw-rw-r-- 1 nickm nickm  530 Aug 19 16:43 First-Worker-Node-ip-95.sh
-rw-rw-r-- 1 nickm nickm 2504 Aug 19 16:43 K3S_Auto_Cluster_Generator_Schedule.txt
-rw-rw-r-- 1 nickm nickm  573 Aug 19 16:43 Primary-Master-Node-ip-89.sh
-rw-rw-r-- 1 nickm nickm  808 Aug 19 16:43 Second-Master-Node-ip-88.sh
-rw-rw-r-- 1 nickm nickm  526 Aug 19 16:43 Second-Worker-Node-ip-97.sh
-rw-rw-r-- 1 nickm nickm  802 Aug 19 16:43 Third-Master-Node-ip-4.sh
-rwxrwxr-x 1 nickm nickm 9032 Aug 19 16:19 auto_generate_k3s_cluster_scripts.pyz
-rw-rw-r-- 1 nickm nickm  915 Aug 19 16:34 config.yaml
-rw-rw-r-- 1 nickm nickm 4553 Aug 19 16:19 readme.md

```
### 2.2 Create the Kubernetes Master Node
```
chmod 775 *.sh

sudo su -

cd /opt/K3S_Auto_Cluster_Generator

./Primary-Master-Node-ip-89.sh

cat /var/lib/rancher/k3s/server/node-token
```
expect
```

```

### 2.3 Get the token from master node
Run this command:
```
cat /var/lib/rancher/k3s/server/node-token
```


## 3 Install Helm
Run the following commands
```
# Download Helm
curl -L https://get.helm.sh/helm-v3.11.1-linux-amd64.tar.gz -o helm.tar.gz

# Extract Archive
tar -xvzf helm.tar.gz

# Move to servers bin folder
sudo mv linux-amd64/helm /usr/local/bin/
```

Verify installation
```
helm version
```

K3S Server requirement
```
nano ~/.bashrc
```
Put this ontop of the file
```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
```

Reboot the server
```
sudo reboot
```
## 4. Install Terraform

SSH back to the server
```
ssh nickm@10.154.2.93
```

### 4.1 Install Terraform in TerraFormK3S VM
https://www.terraform.io/ \
https://developer.hashicorp.com/terraform/downloads?product_intent=terraform

Now select O/S : Linux
This will be:
https://releases.hashicorp.com/terraform/1.5.5/terraform_1.5.5_linux_amd64.zip
You will need wget installed
```
sudo apt install wget
sudo apt update
sudo apt-get install unzip
```
Now download Terraform
```
wget https://releases.hashicorp.com/terraform/1.5.5/terraform_1.5.5_linux_amd64.zip
```

Unzip ZIP file
```
unzip terraform_1.5.5_linux_amd64.zip
```

Move terraform
```
sudo mv terraform /usr/local/bin/
```

Test terraform 
```
terraform -help

terraform version
```
### 4.2 How we create the namespace.tf file
We took an existing procedure and modified it to work with the K3S Kubernetes from Rancher

**Open this link:** \
https://registry.terraform.io/

**Click Browser Providers:** \
https://registry.terraform.io/browse/providers

**Click on Kubernetes:** \
https://registry.terraform.io/providers/hashicorp/kubernetes/latest

**Click on the Documentation button (top right)** \
Now click on **core/v1** to expand it \
Click on **Resources** to expand it \
Now click on **kubernetes_namespace_v1** to open the tutorial document

Please note you can also search for **"name"** on this page \
to find **kubernetes_namespace_v1** \
In the **filter** above the Left Navigator type **name** and then you will see this :
```
 core/v1
     Resources
         kubernetes_namespace
             kubernetes_namespace_v1
     Data Sources
```

Here is the Example code we got for the namespace.tf file:
```
resource "kubernetes_namespace_v1" "example" {
  metadata {
    annotations = {
      name = "example-annotation"
    }

    labels = {
      mylabel = "label-value"
    }

    name = "terraform-example-namespace"
  }
}
```

However we will start using only a little bit of this code and then apply more of this code later.

### 4.3 Creating and Applying the Terraform Project 
create folder
```
sudo mkdir /opt/terraform

sudo mkdir /opt/terraform/k3s

sudo mkdir /opt/terraform/k3s/namespace
```

create main.tf 
```
sudo nano /opt/terraform/k3s/namespace/main.tf
```

Open this link to the Terraform Registry: \
https://registry.terraform.io/

Click on the **Browser Providers** button (in the middle of the page) \
https://registry.terraform.io/browse/providers

Click on the **Kubernetes** pannel button (in the middle of the page) \
https://registry.terraform.io/providers/hashicorp/kubernetes/latest

Click on the **USE PROVIDER** button (Top Right) \
Now copy the code. \
This is what you should get:
```
terraform {
  required_providers {
    kubernetes = {
      source = "hashicorp/kubernetes"
      version = "2.23.0"
    }
  }
}

provider "kubernetes" {
  # Configuration options
}
```

Create main.tf file and paste the code we just got there
```
nano /opt/terraform/k3s/namespace/main.tf
```

We are using K3S Kubernetes here is our Config
```
less /etc/rancher/k3s/k3s.yaml
```

expect to get
```
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJlRENDQVIyZ0F3SUJBZ0lCQURBS0JnZ3Foa2pPUFFRREFqQWpNU0V3SHdZRFZRUUREQmhyTTNNdGMyVnkKZG1WeUxXTmhRREUyT1RJME5qUXpNakl3SGhjTk1qTXdPREU1TVRZMU9EUXlXaGNOTXpNd09ERTJNVFkxT0RReQpXakFqTVNFd0h3WURWUVFEREJock0zTXRjMlZ5ZG1WeUxXTmhRREUyT1RJME5qUXpNakl3V1RBVEJnY3Foa2pPClBRSUJCZ2dxaGtqT1BRTUJCd05DQUFTbEhtZmJmODh2aU9ITCtURHpra2U4UHcyNkVSOVorWE1zNklTaVRmbkMKcDB4N0JwZlhpOS9YUTVFQTQrYjVsUXFPQU5lVW5xcmorZFVBM3phZGJxMTFvMEl3UURBT0JnTlZIUThCQWY4RQpCQU1DQXFRd0R3WURWUjBUQVFIL0JBVXdBd0VCL3pBZEJnTlZIUTRFRmdRVTRQdW45d0QrdmJIVndwZ2xhVFBECno3UHRTT3d3Q2dZSUtvWkl6ajBFQXdJRFNRQXdSZ0loQU9uTjhDcjBPbzB6R3FDUkZZUVlIRi9Ob1Jta2pzYXUKTWQrT0UycnluYUNEQWlFQXVvYUtBR0JreEJwK3NLMXpISGFjcnZuSGg0bSsrdzJzeDlHRjhKMDVGL1k9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    server: https://127.0.0.1:6443
  name: default
contexts:
- context:
    cluster: default
    user: default
  name: default
current-context: default
kind: Config
preferences: {}
users:
- name: default
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJrVENDQVRlZ0F3SUJBZ0lJWEh4Q3hITzJlczR3Q2dZSUtvWkl6ajBFQXdJd0l6RWhNQjhHQTFVRUF3d1kKYXpOekxXTnNhV1Z1ZEMxallVQXhOamt5TkRZME16SXlNQjRYRFRJek1EZ3hPVEUyTlRnME1sb1hEVEkwTURneApPREUyTlRnME1sb3dNREVYTUJVR0ExVUVDaE1PYzNsemRHVnRPbTFoYzNSbGNuTXhGVEFUQmdOVkJBTVRESE41CmMzUmxiVHBoWkcxcGJqQlpNQk1HQnlxR1NNNDlBZ0VHQ0NxR1NNNDlBd0VIQTBJQUJHUStjVVhzcjlKM0FiNEQKUUs3Nk1rK1h2dUdCTE50TnJBTjE3OTc0eGRGdzF3SzQvaldlUjFiay9WcVRaaE5sSUdPakNVL0lPYVRmcTBKWgpFR1l3VkxDalNEQkdNQTRHQTFVZER3RUIvd1FFQXdJRm9EQVRCZ05WSFNVRUREQUtCZ2dyQmdFRkJRY0RBakFmCkJnTlZIU01FR0RBV2dCUWZGa3pNYTdNZUhMeFFwQkZuSkRrRHJCaHVKREFLQmdncWhrak9QUVFEQWdOSUFEQkYKQWlFQXp6dktWL0tMLzNHdzVuM3ZLR1B4aGVUVUtwQjFsTnI4TTVQS2x0dWVOcW9DSUZacmI3VnBmc2JGWlFrWgpXcDNwemZuWHZZUi90cUFVdW83RWlDYlFBOVpoCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KLS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJkakNDQVIyZ0F3SUJBZ0lCQURBS0JnZ3Foa2pPUFFRREFqQWpNU0V3SHdZRFZRUUREQmhyTTNNdFkyeHAKWlc1MExXTmhRREUyT1RJME5qUXpNakl3SGhjTk1qTXdPREU1TVRZMU9EUXlXaGNOTXpNd09ERTJNVFkxT0RReQpXakFqTVNFd0h3WURWUVFEREJock0zTXRZMnhwWlc1MExXTmhRREUyT1RJME5qUXpNakl3V1RBVEJnY3Foa2pPClBRSUJCZ2dxaGtqT1BRTUJCd05DQUFSQVBkYWZDa3llWnpUbE9WTzJWb2N3Yk1jZGUvMjZJNUk3UEdZUnFxSk8KUHg2d3BlNlpldm9MUHQ3NUFEU1E2bEU2b1BKNXA3Q29QWFN2SndjcmtSRXdvMEl3UURBT0JnTlZIUThCQWY4RQpCQU1DQXFRd0R3WURWUjBUQVFIL0JBVXdBd0VCL3pBZEJnTlZIUTRFRmdRVUh4Wk16R3V6SGh5OFVLUVJaeVE1CkE2d1liaVF3Q2dZSUtvWkl6ajBFQXdJRFJ3QXdSQUlnSUd4VnFCbTkzMmhLTVNvTW0wbnpkeFVPRjMzWUJ2b3AKcitvSlArMnhwNHdDSURGbklINTB4SjF6TGZWNG9DNWluMjJwSE4zUnNONVZvellWcksweUUwT1EKLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
    client-key-data: LS0tLS1CRUdJTiBFQyBQUklWQVRFIEtFWS0tLS0tCk1IY0NBUUVFSU5pTloyM2xScUVMbzF6ZVRDT3llSHVKeEhMVzQzcjBmcmdwL1QzMEFyZzFvQW9HQ0NxR1NNNDkKQXdFSG9VUURRZ0FFWkQ1eFJleXYwbmNCdmdOQXJ2b3lUNWUrNFlFczIwMnNBM1h2M3ZqRjBYRFhBcmorTlo1SApWdVQ5V3BObUUyVWdZNk1KVDhnNXBOK3JRbGtRWmpCVXNBPT0KLS0tLS1FTkQgRUMgUFJJVkFURSBLRVktLS0tLQo=
```
 
get the Kubernetes current config context
```
kubectl config current-context
```

expect to get:
```
kubectl config current-context
default

```
Edit main.tf
```
cd /opt/terraform/k3s/namespace/
nano main.tf
```
Put the ***config_path* and **config_context** values we just identified there
```
terraform {
  required_providers {
    kubernetes = {
      source = "hashicorp/kubernetes"
      version = "2.23.0"
    }
  }
}

provider "kubernetes" {
  # Configuration options
  config_path = "/etc/rancher/k3s/k3s.yaml"
  config_context = "default"
}
```
Initialize Terraform
```
terraform init
```
Run Terraform Plan and see what is going to change
```
terraform plan
```
Expect to have no changes

Create file namespace.tf
```
nano namespace.tf
```

Put this into the file (What we copied earlier)
```
  resource "kubernetes_namespace_v1" "example" {
    metadata {
      name = "test-namespace"
    }
  }
```

Run Terraform Plan and see what is going to change
```
terraform plan
```

There should be something that will be created now

Run Terraform Apply to enforce the change
```
terraform apply
```
Reply yes to prompt : **"Enter value"**

Now lets determine what namespaces are in the K3S Cluster
```
kubectl get namespaces

# to get the details of the namespace created
kubectl describe namespace test-namespace
```

Now lets see what files Terraform created
```
tree ../
```

Expect to get : 
```
tree ../
../
└── namespace
    ├── main.tf
    ├── namespace.tf
    └── terraform.tfstate

```
Now make a change to file namespace.tf make the  file :
```
  resource "kubernetes_namespace_v1" "example" {
    metadata {
      name = "test-namespace"
      labels = {
        mylabel = "lable-value"
      }
    }
  }
```

Run Terraform Plan and see what is going to change
```
terraform plan
```


There should be something that will be created now

Run Terraform Apply to enforce the change
```
terraform apply
```
Reply yes to prompt : **"Enter value"**

See what namespaces are	in K3S
```
kubectl get namespaces

# we should now see the new changes in the test-namespace
kubectl describe namespace test-namespace
```
### 4.4 Creating two namespaces in our cluster
We want to create a second namespace called second-namespace \
update the file namespace.tf make the  file :
```
resource "kubernetes_namespace_v1" "example" {
  metadata {
    name = "test-namespace"
    labels = {
      mylabel = "lable-value"
    }
  }
}

resource "kubernetes_namespace_v1" "second_example" {
  metadata {
    name = "second-namespace"
    labels = {
      mylabel = "second-lable-value"
    }
  }
}

```
Run Terraform Plan and see what is going to change
```
terraform plan
```

There should be something that will be created now

Run Terraform Apply to enforce the change
```
terraform apply
```
Reply yes to prompt : **"Enter value"**

Now lets determine what namespaces are in the K3S Cluster
```
kubectl get namespaces

# to get the details of the new namespace created
kubectl describe namespace second-namespace
```


### 4.5 The Cleanup
```
terraform destroy

# the test-namespace and second-namespace 
# should have been removed from the cluster
kubectl get namespaces
```
	



## Bibliography
https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs 

https://www.terraform.io/use-cases/manage-kubernetes 

https://loft.sh/blog/terraform-vs-kubernetes-everything-you-need-to-know/ 

https://medium.com/avmconsulting-blog/manage-kubernetes-resources-via-terraform-d57e6fae92c7 

https://spacelift.io/blog/terraform-kubernetes-provider 

https://opensource.com/article/20/7/terraform-kubernetes 

https://www.youtube.com/watch?v=ZkQ1SHImHVI

