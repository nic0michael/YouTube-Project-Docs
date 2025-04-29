---
title: "Proxmox Import OVA Files"
date: 2024-04-05T15:22:29Z
draft: false
---


## 1. Download OVA file
The Metasploit OVA file can be downloaded here : \
[https://sourceforge.net/projects/metasploitable3-ub1404upgraded/files/Metasploitable3-ub1404.ova/download](https://sourceforge.net/projects/metasploitable3-ub1404upgraded/files/Metasploitable3-ub1404.ova/download)

Appliences can be downloaded from Turnkey here: \
[https://www.turnkeylinux.org/all](https://www.turnkeylinux.org/all]

**We will download Gitlab** \
https://www.turnkeylinux.org/gitlab

**We will download** \
https://www.turnkeylinux.org/download?file=turnkey-gitlab-16.0-buster-amd64.ova


### 1.1 Get the OVA file
run these commands
```
sudo su -

apt  update

apt install wget -y

mkdir /opt/ova_imports
```
#### 1.1.1 Download Metasploit
Run these commands:
```
wget https://sourceforge.net/projects/metasploitable3-ub1404upgraded/files/Metasploitable3-ub1404.ova/download
mv download Metasploitable3-ub1404.ova
```

##### 1.1.2 Download GitLab
Open in Browser : \
[https://www.turnkeylinux.org/download?file=turnkey-gitlab-16.0-buster-amd64.ova](https://www.turnkeylinux.org/download?file=turnkey-gitlab-16.0-buster-amd64.ova)
Use Filezilla to upload file to Proxmox

## 2. Extract the OVA file
### 2.1 Extract Metasploitable3-ub1404.ova
run these commands
```
tar xvf Metasploitable3-ub1404.ova

ls -la
```
expect to get these new files: 
```
-rw-rw---- 1 man  daemon 2882157568 Jan  8  2022 Metasploitable3-ub1404-disk001.vmdk
-rw-r----- 1 man  daemon       7585 Jan  8  2022 Metasploitable3-ub1404.ovf
```
Rename the vmdk file run this command:
```
mv Metasploitable3-ub1404-disk001.vmdk Metasploitable3-ub1404.vmdk

ls -la
```
now you should have :
```
-rw-r--r-- 1 root root   2882166272 Jan  9  2022 Metasploitable3-ub1404.ova
-rw-r----- 1 man  daemon       7585 Jan  8  2022 Metasploitable3-ub1404.ovf
-rw-rw---- 1 man  daemon 2882157568 Jan  8  2022 Metasploitable3-ub1404.vmdk
```
### 2.2 Extract turnkey-gitlab-16.0-buster-amd64.ova
run these commands
```
tar xvf turnkey-gitlab-16.0-buster-amd64.ova

ls -la
```
expect to get these new files: 
```

-rw-r--r-- 1   64     64 1189016576 Jul 17  2020 turnkey-gitlab-16.0-buster-amd64-disk1.vmdk
-rw-r--r-- 1   64     64        177 Jul 17  2020 turnkey-gitlab-16.0-buster-amd64.mf
-rw-r--r-- 1   64     64       6688 Jul 17  2020 turnkey-gitlab-16.0-buster-amd64.ovf
```
Rename the vmdk file run this command:
```
mv turnkey-gitlab-16.0-buster-amd64-disk1.vmdk turnkey-gitlab-16.0-buster-amd64.vmdk

ls -la
```
now you should have :
```
-rw-r--r-- 1   64     64        177 Jul 17  2020 turnkey-gitlab-16.0-buster-amd64.mf
-rw-r--r-- 1 root root   1189026816 Apr  5 21:12 turnkey-gitlab-16.0-buster-amd64.ova
-rw-r--r-- 1   64     64       6688 Jul 17  2020 turnkey-gitlab-16.0-buster-amd64.ovf
-rw-r--r-- 1   64     64 1189016576 Jul 17  2020 turnkey-gitlab-16.0-buster-amd64.vmdk
```

## 3. Creating a virtual Machine using Quemu
First check what VM Id is available in Proxmox \

### 3.1 Create a VM 
Do this in Proxmox
```
# General**
1. click Create VM button
2. VM Id : 188
3. Name : Metasplitable
4. Next
# OS**
5. Click on : Do not use Media
6. next
# System**
7. leave all unchanged
8. click on : Quemu Agent
9. next
**Disks**
10. storage : ZZ_User_Data
11. next
# CPU**
12. sockets: 1
13. cores: 2
14. next
# Memory**
15. Memory MiB: 4096
16. Maximum Memory MiB:	4096
17. next
**Network**
18. next
# Confirm**
19. Finish

1. Click on the VM
2. Click on the Hardware Tab
3. Click on Hard Disk
4. Click Detach button
5. Click Yes
6. Click on Unused Disk 0
7. click Remove button
8. click yes
```
Note the ID of this VM : 188

Run this command :
```
# qm importdisk 188 Metasploitable3-ub1404.vmdk local-lvm -format qcow2

# we will ZZ_User_Data instead of local-lvm
qm importdisk 188 Metasploitable3-ub1404.vmdk ZZ_User_Data -format qcow2
# And for GitLab
qm importdisk 189 turnkey-gitlab-16.0-buster-amd64.vmdk ZZ_User_Data -format qcow2
```

### 3.2 Adding the imported disk
```
1. Click on the VM
2. Click on the Hardware Tab
3. Click on Hard Disk
4. click Edit button
5. In popup click Add button
```

### 3.3 Set Boot Order
```
1. Click on the VM
2. Click on the Hardware Tab
3. Click on Options Tab
4. Click on Boot Order
5. Click Edit button
6. Enable SCSI 0
7. Move it up (above net0)
8. click OK button
```

### 3.4 Start the VM
```
1. Click on the VM
2. Click Console button
3. Click Strt button
```


## Bibliography
https://syncbricks.com/how-to-import-ova-to-proxmox/
https://www.youtube.com/watch?v=clSyL2-5TtM&t=3s
