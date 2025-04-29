---
title: "Create LXC Containers in ProxMox"
date: 2024-02-11T18:17:46Z
draft: false
---

This is the prefered way to create Docker containers in Proxmox

## 1. List Available templates
We need to add an Ubuntu and a Rocky Linux Template
```
ssh root@10.154.2.89

# List available templates
pveam available
```
 Expect to get:
```
# this iis a shortened list
system          almalinux-9-default_20221108_amd64.tar.xz
system          alpine-3.18-default_20230607_amd64.tar.xz
system          alpine-3.19-default_20240207_amd64.tar.xz

system          rockylinux-9-default_20221109_amd64.tar.xz
system          ubuntu-20.04-standard_20.04-1_amd64.tar.gz
system          ubuntu-22.04-standard_22.04-1_amd64.tar.zst
system          ubuntu-23.04-standard_23.04-1_amd64.tar.zst

# We are going to ignore the turnkeylinux Apliance Templates
```
## 2. Download the Ubuntu Template
Select one of the desired above templates : ubuntu-22.04-standard_22.04-1_amd64.tar.zst
Run this command :
```
pveam download local ubuntu-22.04-standard_22.04-1_amd64.tar.zst

pveam download local rockylinux-9-default_20221109_amd64.tar.xz

pveam download local ubuntu-22.04-standard_22.04-1_amd64.tar.zst
pveam download local ubuntu-23.04-standard_23.04-1_amd64.tar.zst

# In mycase I have a 2TB drive : VMdata

pveam download VMdata ubuntu-22.04-standard_22.04-1_amd64.tar.zst

pveam download VMdata rockylinux-9-default_20221109_amd64.tar.xz

```

## 3. Creating a Proxmoc LXC Container

Steps:
```
-> Create Container 
give it a host name : DockerCont89
enter password:
confirm password:
-> next

Select storage : local (in my case VMdata)

Select template from these : 
ubuntu-22.04-standard_22.04-1_amd64.tar.zst
rockylinux-9-default_20221109_amd64.tar.xz

-> Next
Disk size:(GiB) : 20 


-> Next
cores: 2
CPU limit: 1

-> Next
Memory (MiB) : 4096
Swap (MiB) : 512

-> Next
IPv4/CIDR : 10.154.2.87/24
Gateway ipv4: 10.154.2.3

-> Next
DNS Domain : 10.154.2.3
DNS Servers: 8.8.8.8  or 1.1.1.1

-> Confirm
-> Finish

-> Status
```
