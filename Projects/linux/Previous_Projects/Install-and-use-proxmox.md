---
title: "Install and Use Proxmox as Homelab"
date: 2023-06-25T13:43:53Z
draft: false
---

## Download proxmox
https://www.proxmox.com/en/downloads?task=callelement&format=raw&item_id=689&element=f85c494b-2b32-4109-b8c1-083cca2b7db6&method=download&args[0]=7b4c63b29aa53c1a06ca047ee9ce6717

## 1. Setup Installtion media for Proxmox
make a CD from the above image and bootup your PC \
Alterbativly use BalenaEtcher and make a bootable image on a thumbdrive (Memory-Stick)  \
Boot up the PC go into the BIOS F12 now enable the machine to bootup from USB then plug in your PCs USB socket and bootup your PC again \
[Get BalinaEtcher here:](https://etcher.balena.io/)

## You will need a Fully Qualified DNS Name (FQDN)
Get you FQDN from DynuDNS /
https://www.dynu.com/

My server details are:
```
# Hostname(FQDN) :
tiger.loseyourip.com

# IP Address (CIDR) :
10.154.2.188

# Gateway :
10.165.2.3

# DNS Server
8.8.8.8

```
## 2. Deployment steps
```
# Use the Installation media and setart the server hardware
wait for installation software to start

# Choose :  Install using Graphical Interface GUI

# Accept the End User Licence Agreement (EULA)
-> Click Next Button

# Select Country 
-> United States
-> New York
# keyboard
-> US
-> Click Next Button

# Enter Password and Email
-> Click Next Button

# Enter FQDN Details (See above) You need to type these values
tiger.loseyourip.com
10.154.2.188
10.154.2.3
8.8.8.8

-> Click Next Button

# You will have a summary ensure this is correct 
-> Click Next Button
```
#Open Browser

https://tiger.loseyourip.com:8006/ \
[https://10.154.2.188:8006](https://10.154.2.188:8006)

## Adding another Hard Drive using Web Interface
```
# From the left Nav panel
-> Proxmox server Name

# From the Center panel
-> Disks

# From right panel
-> dev/sdb

# From right panel top buttons
-> [Wipe Disk ]

->[Initialize Disk with GPT ]

# From the Center panel
-> Disks
   -> Directory

# From right panel top buttons
-> [Create Directory]
 Give the directory the name: Storage
```

## After installing Proxmox
```
# If not purchasing support from Proxmox you get errors doing atp update or upgrade if this line is active
nano /etc/apt/sources.list.d/pve-enterprise.list
# coment out this line
# deb https://enterprise.proxmox.com/debian/pve bullseye pve-enterprise

apt update

# to upgrade to latest distribution
apt dist-upgrade


# Smart Monitoring
in the list for rows /dev/sda and /dev/sdb
look for PASSED in SMART column
# getting SMART values for your disk
->/dev/sda
# From right panel top buttons
-> [Show S.M.A.R.T values]

# You can do the same for /dev/sdb

# manual run smart monitoring (this is verbose pipe into less)
smartctl -a /dev/sdb
smartctl -a /dev/sdb | less

```

## Bibliography
[Get Started](https://www.proxmox.com/en/proxmox-ve/get-started) \
https://www.youtube.com/watch?v=GoZaMgEgrHw&t=622s \

