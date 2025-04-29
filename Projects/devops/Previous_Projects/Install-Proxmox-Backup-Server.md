---
title: "Install Proxmox Backup Server"
date: 2024-08-02T06:08:06Z
draft: false
---

## 1. Resources
[The PBS ISO images](https://www.proxmox.com/en/downloads/proxmox-backup-server/iso) \
https://enterprise.proxmox.com/iso/proxmox-backup-server_3.2-1.iso


## 2. VM Requirement
```
ISO image : proxmox-backup-server_3.2-1.iso
Disk 512GB to 2TB 
CPU sockets 1 and cores 2
RAM 16384 GB (4096 for a VM)
```
## 3. DNS Settings
```
IP Address : 10.154.2.85
DNS Name   : buffalo.loseyourip.com
```

## 4. Create the Proxmox Backup Server
Its a server installation using the above ISO file
We do not recomend you do this as Proxmox VM installation unless you have a Proxmox HA Cluster

## 5. Open the Proxmox Backup Server in the browser
[**https://buffalo.loseyourip.com:8007**](https://buffalo.loseyourip.com:8007)

## 6. Creating a datastore
For simplisity we are going to use the /data folder

Open Shell run this command to create folder /data 
```
mkdir -p /data
```
Add new Datastore using th datastore tab

## 7. Get The Fingerprint of data folder
1. click on data tab
2. click on show information
3. click on copy button next to fingerprint

WE got :
```
a0:2d:9a:22:a6:96:95:78:27:2e:59:79:ea:5e:80:4e:f8:62:ab:a5:fa:5d:00:71:6c:5d:fe:4f:79:3b:4b:83
```
## 8. Adding the Proxmox Backup Server to the Proxmox Datacenter
1. Click on Datacenter
2. Click on Storage
3. Click on Add \
   In the dropdown (select Proxmox Backup Server)

Enter this info :
```
ID : Backups
Server : 10.154.2.85
Username : root
Password : **************
Datastore : data
Namespace : data
Fingerprint : a0:2d:9a:22:a6:96:95:78:27:2e:59:79:ea:5e:80:4e:f8:62:ab:a5:fa:5d:00:71:6c:5d:fe:4f:79:3b:4b:83



```

## Bibliography
https://www.proxmox.com/en/proxmox-backup-server/get-started \
https://4sysops.com/archives/proxmox-backup-server-install-and-configure/ \
https://www.reddit.com/r/Proxmox/comments/16bjvbb/proxmox_backup_server_installed_as_a_vm/ \
https://forum.proxmox.com/threads/install-pbs-on-running-pve-host.72869/

