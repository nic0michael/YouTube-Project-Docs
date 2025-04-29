---
title: "Install TrueNAS in Proxmox"
date: 2024-04-12T15:44:52Z
draft: false
---
Our goal is to show you how to install TrueNAS Scale \
TrueNAS Scale is built on Linux TrueNAS Core is old built on FreeBSD
## 1. Get software
[https://www.truenas.com/](https://www.truenas.com/)

### 1.1 Community Edition
[https://www.truenas.com/truenas-community-editions/](https://www.truenas.com/truenas-community-editions/)

We chose : \
[https://www.truenas.com/download-truenas-scale/](https://www.truenas.com/download-truenas-scale/)

We will add this ISO to out Proxmox server:
https://download.sys.truenas.net/TrueNAS-SCALE-Cobia/23.10.2/TrueNAS-SCALE-23.10.2.iso

## 2. Get your Proxmox servers Disk details first
Run the following command
```
lsblk
```
You’ll see a list of your hard drives, where they’ll have names like “sda,” “sdb,” “sdc,” and so on. It should look like this: \
Expect to get :
```
sda             8:0    0 223.6G  0 disk 
├─sda1          8:1    0  1007K  0 part 
├─sda2          8:2    0   512M  0 part /boot/efi
└─sda3          8:3    0 223.1G  0 part 
  ├─pve-swap  252:0    0   7.7G  0 lvm  [SWAP]
  ├─pve-root  252:1    0  65.9G  0 lvm  /var/lib/kubelet/pods/d035875c-1af6-47b3-81ed-8d00bc29c5ce/volumes/kubernetes.io~local-volume/my-local-pv
  │                                     /
  ├─pve-data_tmeta
  │           252:2    0   1.3G  0 lvm  
  │ └─pve-data
  │           252:4    0 130.9G  0 lvm  
  └─pve-data_tdata
              252:3    0 130.9G  0 lvm  
    └─pve-data
              252:4    0 130.9G  0 lvm  
sdb             8:16   0 931.5G  0 disk 
└─sdb1          8:17   0 931.5G  0 part /mnt/pve/VMdata
sdc             8:32   0   1.8T  0 disk 
└─sdc1          8:33   0   1.8T  0 part /mnt/pve/ZZ_User_Data
sr0            11:0    1  1024M  0 rom  
```
Run the following command :
```
lsblk -o +MODEL,SERIAL
```

You should see a similar list with additional information, such as your hard drive’s model and serial numbers. \
Expect to get :
```
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS                                                                                        MODEL SERIAL
sda      8:0    0 223.6G  0 disk                                                                                                    WD Gr 22440L
├─sda1   8:1    0  1007K  0 part                                                                                                          
├─sda2   8:2    0   512M  0 part /boot/efi                                                                                                
└─sda3   8:3    0 223.1G  0 part                                                                                                          
  ├─pve-swap
  │    252:0    0   7.7G  0 lvm  [SWAP]                                                                                                   
  ├─pve-root
  │    252:1    0  65.9G  0 lvm  /var/lib/kubelet/pods/d035875c-1af6-47b3-81ed-8d00bc29c5ce/volumes/kubernetes.io~local-volume/my-local-pv
  │                                                                                                                                       
  │                              /                                                                                                        
  ├─pve-data_tmeta
  │    252:2    0   1.3G  0 lvm                                                                                                           
  │ └─pve-data
  │    252:4    0 130.9G  0 lvm                                                                                                           
  └─pve-data_tdata
       252:3    0 130.9G  0 lvm                                                                                                           
    └─pve-data
       252:4    0 130.9G  0 lvm                                                                                                           
sdb      8:16   0 931.5G  0 disk                                                                                                    ST100 Z1D7N2
└─sdb1   8:17   0 931.5G  0 part /mnt/pve/VMdata                                                                                          
sdc      8:32   0   1.8T  0 disk                                                                                                    ST200 W7204F
└─sdc1   8:33   0   1.8T  0 part /mnt/pve/ZZ_User_Data                                                                                    
sr0     11:0    1  1024M  0 rom  
```

Run this command to see the hard drive data:
```
ls /dev/disk/by-id
```
Expect to get :
```
ata-PLDS_DVD+_-RW_DH-16AES_FY13D7363957P657XA02  dm-uuid-LVM-YdhQIbGZ68z0APRkMMwxJKj1wwb22eZQmagmfF2m92S5acq6rbERvQU4Zwa7Y0A3
ata-ST1000DM003-1CH162_Z1D7N2GH                  dm-uuid-LVM-YdhQIbGZ68z0APRkMMwxJKj1wwb22eZQwODJcFzYicia45kh2jAr0J2hKKZhbY45
ata-ST1000DM003-1CH162_Z1D7N2GH-part1            lvm-pv-uuid-MRntof-Ttkr-D2pY-h5mP-hEbQ-gK1v-9EE2AG
ata-ST2000VM003-1ET164_W7204FNT                  wwn-0x5000c500653a9b0c
ata-ST2000VM003-1ET164_W7204FNT-part1            wwn-0x5000c500653a9b0c-part1
ata-WD_Green_2.5_240GB_22440L453203              wwn-0x5000c5008af2cf94
ata-WD_Green_2.5_240GB_22440L453203-part1        wwn-0x5000c5008af2cf94-part1
ata-WD_Green_2.5_240GB_22440L453203-part2        wwn-0x5001b444a598ffca
ata-WD_Green_2.5_240GB_22440L453203-part3        wwn-0x5001b444a598ffca-part1
dm-name-pve-root                                 wwn-0x5001b444a598ffca-part2
dm-name-pve-swap                                 wwn-0x5001b444a598ffca-part3
```

**Compare this information after adding the PCI Express Passthrough Storage Device to your VM**


## 3. Create a VM using the TrueNAS ISO file
### 3.1 Recomendations:
1. use 2 cores
2. allocate 8GB RAM (8192 MiB)
3. Allocate enough storage 100GB (remember this) #1 below

## 4. Add PCI Express Passthrough Storage Device
```
1. Click on --> your VM
2. Click on   --> Hardware tab
3. Click on     --> Add button
4. Click on       --> PCI Device
5. In Drop-down Select  --> Realtec Semicon RTLB ... PCI Express Gigabit Ethernet
```
## 5. Adjust Passthrough Storage Device
Connect your terminal to the PROXMOX server using ssh

DO NOT START YOUR VM UNTIL TOLD TO DO SO

```
ssh nickm@10.154.2.73
```

Run the following command
```
lsblk
```
You’ll see a list of your hard drives, where they’ll have names like “sda,” “sdb,” “sdc,” and so on. It should look like this: \
Expect to get :
```

```
Run the following command :
```
lsblk -o +MODEL,SERIAL
```

You should see a similar list with additional information, such as your hard drive’s model and serial numbers. \
Expect to get :
```

```

Run this command to see the hard drive data:
```
ls /dev/disk/by-id
```
Expect to get :
```

```

Run  this command to add your storage devices to the VM. \
(You can use it as many times as you have onboard hard drives:)
```
qm set 103 -scsi1 /dev/disk/by-id/ata-ST500DM002-1BD142-W2AV3B3T
```
Remember that this part depends on your storage devices.

## 6. Start your VM to install TrueNAS
```
# First screen
1. select 1 Install/Upgrade
Press OK

# Choose destination media
QUEMU QUEMU HARD DISK 100GB (Remember #1 above)
Press OK

# TrueNAS Installation
Press Yes

# Enter your root password
type a strong password twice
Press OK

# TrueNAS can be booted in either BIOS or UEFI mode
Press Boot vis BIOS
```
After you boot TrueNAS you will get an option to do additional settings. \
However it will give you two URLs to use with the browser

## 7. Manage Truenas fron the browser
Open the the browser : \
http://10.154.2.72 (This depends on what you got in the terminal) \
Login as root with the password you created



 



## Bibliography
[TrueNAS Scale Or TrueNAS Core In 2024?](https://www.youtube.com/watch?v=8GiN76HNuss)
[Fixing my worst TrueNAS Scale mistake!](https://www.youtube.com/watch?v=10coStxT5CI&t=68s)
[TrueNAS Core vs. Scale: Which is Right for You?](https://www.youtube.com/watch?v=vXzLoTK2SJE)
https://recoverit.wondershare.com/nas-recovery/truenas-proxmox.html \

