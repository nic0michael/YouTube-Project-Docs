---
title: "Install_Metasploitable3 in Proxmox"
date: 2024-03-28T21:42:38Z
draft: false
---

THIS ONLY WORKS IF YOU ADD A NEW DRIVE TO YOUR SERVER TO HOWST THE OVA IMAGE
rATHER USE AL OLD PC RUNNING VIRTUALBOX

## 1. Creating the VM
### 1.1 Create a VM
Click -> Create VM button (top right side)
Name : Metasploitable
Click -> Next button

### 1.2 OS (settings)
Click ->(*) do not use any media
Click -> Next button

### 1.3 System (settings)
Graphics Card : default
SCSI coltroller : VirtIO SCSI single
Machine: Default (i440fx)
BIOS : Default (SeaBIOS)
Click -> Next button

### 1.4 Disks (settings)
Storage : ZZ_User_Data (for you use local-lvm)
Click -> Next button

### 1.5 CPU (settings)
Sockets : 1
Cores : 2
Click -> Next button

### 1.6 Memory (settings)
Memory MiB : 4096 (Allocate as much memory as is available on your Proxmox server)
Maximum Memory MiB : 4096 (same here)
Click -> Next button

### 1.7 Network (settings)
Keep it as is here
Click -> Next button

### 1.8 Confirm (and create the VM)
Click -> Finish button


## 2. Delete associated disk
In the left Navigator click on this VM
Click -> Metasploitable

In the Centre Vertical Menu click on Hardware
Click -> Hardware

In the left pane  click on Hard Disk (scsi0)
Click -> Hard	Disk (scsi0) 

Click the Detach button above this
Click - > Detach button

Confirm
Click -> Yes

## 3. Get Metasploitable OVA image
Locate the download file here :
[https://sourceforge.net/projects/metasploitable3-ub1404upgraded/files/](https://sourceforge.net/projects/metasploitable3-ub1404upgraded/files/)

The URL of image is:
[https://sourceforge.net/projects/metasploitable3-ub1404upgraded/files/Metasploitable3-ub1404.ova/download](https://sourceforge.net/projects/metasploitable3-ub1404upgraded/files/Metasploitable3-ub1404.ova/download)

Download this file to your computer

## 4. Copy file to Proxmox server using SCP
### locate file
Run these commands
```
ls -la ~/Downloads/

ls -la ~/Downloads/Metasploitable3*
```
Expect to findto find:
```
/home/nickm/Downloads/Metasploitable3-ub1404.ova
```
Run this command
```
scp /home/nickm/Downloads/Metasploitable3-ub1404.ova root@10.154.2.89:/tmp


# it copied :    100% 2749MB  28.5MB/s   01:36
```

## 5. Extracting the OVA file
Connect to Proxmox machine open the Shell (or by ssh in terminal)

run these commands :
```
cd /tmp

ls -la
```
expect to find
```
-rw-r--r--  1 root root 2882166272 Mar 29 11:08 Metasploitable3-ub1404.ova
```

The OVA file is packaged as a ZIP file extract to file
```
mkdir Metasploitable3-ub1404

cd Metasploitable3-ub1404

tar -xvf Metasploitable3-ub1404.ova

ls -la
```
Expect to get:
```
-rw-rw----  1 man  daemon 2882157568 Jan  8  2022 Metasploitable3-ub1404-disk001.vmdk
-rw-r--r--  1 root root   2882166272 Mar 29 11:21 Metasploitable3-ub1404.ova
-rw-r-----  1 man  daemon       7585 Jan  8  2022 Metasploitable3-ub1404.ovf
```

The OVF file is a XML file listing all the options


## 6. Importing the OVF file into Proxmox
### 6.1 creating storage for Metasploit


### 6.2 Creating qcow2 formated Storage
We are going to use quemu to import the OVF file using an unused Vm Id number : 15020 specify the Storage : ZZ_User_Data/vm-103-disk-0.qcow2
run this command
```
qm importovf 15020 Metasploitable3-ub1404.ovf vm-103-disk-0.qcow2 --format qcow2

qm importovf 15020 Metasploitable3-ub1404.ovf metasploit --format qcow2
```




## Links
https://sourceforge.net/directory/?q=metasploitable+3+virtual+machine

https://sourceforge.net/projects/metasploitable3-ub1404upgraded/

https://sourceforge.net/projects/metasploitable3-ub1404upgraded/files/

https://forums.hak5.org/topic/49549-metasploitable3-on-proxmox/

https://forum.proxmox.com/threads/migrating-virtualbox-vdi-to-proxmox-ve.9672/


https://forum.proxmox.com/threads/importing-a-virtual-machine-ova-into-proxmox.136235/

https://www.youtube.com/watch?v=EqGJYU96l0Q

https://i12bretro.github.io/tutorials/0387.html

https://www.youtube.com/watch?v=clSyL2-5TtM

https://www.youtube.com/watch?v=4lYulcTd5yc

https://www.itsfullofstars.de/2019/07/import-ova-as-proxmox-vm/


