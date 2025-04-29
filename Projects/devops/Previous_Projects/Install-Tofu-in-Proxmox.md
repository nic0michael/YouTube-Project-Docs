---
title: "Install Tofu in Proxmox"
date: 2025-03-29T14:33:08Z
draft: false
---

## What is Open Tofu (the new kid on the block)
Open Tofu is a fork of the Tera Form project forked at the last Opensource release the Hashycorp produced before they changed their licence now not Opensorce.

We will show you how to install this in Ubuntu Linux server which is our Kubernetes Master Node Server in our Proxmox server.

In the latest version of Open Tofu the terraform command has been removed making this a different project with compatability of the .tf files.

We will demonstate how we create Namespaces in the Kubernetes Cluster



## 1. Install Open Tofu
Run these commands
```sh
sudo su -

mkdir /opt/tofu -p

cd /opt/tofu

sudo apt update

sudo apt install tree


# Download the installer script:
curl --proto '=https' --tlsv1.2 -fsSL https://get.opentofu.org/install-opentofu.sh -o install-opentofu.sh
# Alternatively: wget --secure-protocol=TLSv1_2 --https-only https://get.opentofu.org/install-opentofu.sh -O install-opentofu.sh

# Give it execution permissions:
chmod +x install-opentofu.sh

# Please inspect the downloaded script
less install-opentofu.sh

# Run the installer:
./install-opentofu.sh --install-method deb

# verify Tofu is installed
tofu --version

```
## 2. Using Open Tofu
### 2.1 Create main.tf file 
Run these commands
```sh
nano main.tf
```
Put this in the	file
```tf
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
### 2.2 Initialize Terraform
```
tofu init
```

### 2.3 Run Terraform Plan and see what is going to change
```
tofu plan
```
Expect to have no changes

### 2.4 Create file namespace.tf
```
nano namespace.tf
```
Put this in the file
```tf
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
### 2.5 Run Terraform Plan and see what is going to change
```
tofu plan
```

There should be something that will be created now

### 2.6 Run Terraform Apply to enforce the change
```
tofu apply
```
Reply yes to prompt : **"Enter value"**

### 2.5.1 Now lets determine what namespaces are in the K3S Cluster
```
kubectl get namespaces

# to get the details of the namespace created
kubectl describe namespace test-namespace
```

### 2.5.2 Now lets see what files Terraform created
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

## 3. The Cleanup
```
tofu destroy

# the test-namespace 
# should have been removed from the cluster
kubectl get namespaces
```

## Websites
**[https://opentofu.org/](https://opentofu.org/)**

**[GitHub: https://github.com/opentofu/opentofu](https://github.com/opentofu/opentofu)**

**[Ubuntu Installation: https://opentofu.org/docs/intro/install/deb/](https://opentofu.org/docs/intro/install/deb/)**

**[Docs: https://opentofu.org/docs/](https://opentofu.org/docs/)**


**[Providers: https://opentofu.org/docs/language/providers/](https://opentofu.org/docs/language/providers/)**

