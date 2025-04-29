---
title: "Install Terraform to Work With Proxmox"
date: 2023-06-13T15:08:55Z
draft: false
---

## Create Proxmox API Token
```
# in proxmox click on
-> Datacentre
	-> API Tokens
		-> [Add]

user: -> root@pam
Token ID: new-root-token
	-> [add]
save the token now its only displayed once and now
#Token is 
Token ID : root@pam!new-root-token
secret : 125a9df6-8920-4ccd-81e8-8ab92743c61e

```

### Add this to main.tf file
```
provider "proxmox" {
  pm_api_url          = "https://upupa.loseyourip.com:8006/api2/json"
  pm_api_token_id     = "root@pam!new-root-token"
  pm_api_token_secret = "125a9df6-8920-4ccd-81e8-8ab92743c61e"
  pm_tls_insecure     = true
}

```

## Ubuntu server Install

### 1. Install Terraform on Ubuntu 20.04 using APT
```
# First, install the required dependencies using the following command:
apt-get install wget curl unzip software-properties-common gnupg2 -y

# Next, download and add the HashiCorp signed gpg keys to your system:
curl -fsSL https://apt.releases.hashicorp.com/gpg | apt-key add -

# Next, add the HashiCorp repository to the APT using the following command:
apt-add-repository "deb [arch=$(dpkg --print-architecture)] https://apt.releases.hashicorp.com $(lsb_release -cs) main"

# Next, update the repository using the command given below:
apt-get update -y


# Finally, install the Terraform by running the following command:
apt-get install terraform -y

# Once the Terraform has been installed, verify it using the following command:
terraform -v






```

### 2. Install Terraform Manually
You can	get Terraform here: \
https://releases.hashicorp.com/terraform
```
# First, download the latest version of Terraform source using the following command:
#wget https://releases.hashicorp.com/terraform/1.1.2/terraform_1.1.2_linux_amd64.zip
#wget https://releases.hashicorp.com/terraform/1.5.0/terraform_1.5.0_linux_amd64.zip
wget https://releases.hashicorp.com/terraform/1.5.1/terraform_1.5.1_linux_amd64.zip

# Once the download is completed, extract the downloaded file using the following command.
#unzip terraform_1.1.2_linux_amd64.zip
# unzip terraform_1.5.0_linux_amd64.zip
unzip terraform_1.5.1_linux_amd64.zip

# Next, copy the Terraform binary from the extracted file to the /usr/bin/ directory:
cp terraform /usr/bin/

# Next, verify the Terraform version by running the following command:
terraform -v

```
## Install the "auto-complete" Terraform Extension
Terraform provides auto-complete features that allow you to list all sub-commands after pressing the TAB key twice. \
In order to activate the auto-complete feature, you will need to install the autocomplete extension to your system.


```
# You can install it by running the following command:
terraform -install-autocomplete

# Next, run the following command to activate the auto-complete features:
source ~/.bashrc

# To check the Terraform auto-complete feature, run the terraform command and press the TAB key twice:
terraform
```
You should see all sub-commands in the following output:

```
apply         env           get           init          output        push          state         untaint       workspace
console       fmt           graph         login         plan          refresh       taint         validate
destroy       force-unlock  import        logout        providers     show          test          version

```



## Create the main.tf file
```
nano main.tf
```

put this in the file
```
terraform {
  required_providers {
    proxmox = {
      source  = "telmate/proxmox"
      version = "2.7.4"
    }
  }
}

provider "proxmox" {
  pm_api_url          = "https://upupa.loseyourip.com:8006/api2/json"
  pm_api_token_id     = "root@pam!new-root-token"
  pm_api_token_secret = "125a9df6-8920-4ccd-81e8-8ab92743c61e"
  pm_tls_insecure     = true
}

resource "proxmox_vm_qemu" "ubuntu-server-1" {
  clone = "ubuntu-server-template"
  full_clone = true
  name = "ubuntu-server-1"
  target_node = "upupu"

  onboot = true
  agent = 1
  cores = 2
  sockets = 1
  cpu = "host"
  memory = 4096


  network {
    bridge = "vmbr0"
    model = "virtio"
  }


  disk {
    storage = "local-lvm"
    type = "virtio"
    size = "100G"
  }


#  gateway = "10.154.2.3"
#  ip_address = "10.154.2.61"
#  dns_servers = "10.0.125.1 8.8.8.8 10.0.125.1 127.0.0.1"
  ipconfig0 = "ip=10.154.2.61/24,gw=10.154.2.3"
}

```

## Initialize Terraform
```
terraform init
```

## Apply main.tf
```
terraform apply

# respond with: 
yes
```


## Bibliography

[Terraform provider plugin for Proxmox](https://github.com/Telmate/terraform-provider-proxmox) \
[How to Install Terraform on Ubuntu Server 20.04 (Step by Step Tutorial)](https://cloudinfrastructureservices.co.uk/how-to-install-terraform-on-ubuntu-server-20-04/) \
[BLOG :     How to deploy VMs in Proxmox with Terraform](https://austinsnerdythings.com/2021/09/01/how-to-deploy-vms-in-proxmox-with-terraform/) \
[Tutorial: How to deploy VMs in Proxmox using Terraform](https://www.youtube.com/watch?v=UXXIl421W8g&list=WL&index=2&t=1113s) \
[Create VMs on Proxmox in Seconds!](https://www.youtube.com/watch?v=1nf3WOEFq1Y&list=WL&index=2&t=1328s) \
[Proxmox virtual machine *automation* in Terraform](https://www.youtube.com/watch?v=dvyeoDBUtsU&list=WL&index=4) \
[Tuesday Tech Tip - Automating Proxmox with Ansible and Terraform](https://www.youtube.com/watch?v=OkJAPc9Xo5Q&t=40s)
