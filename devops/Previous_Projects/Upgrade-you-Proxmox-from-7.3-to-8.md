---
title: "Upgrade Your Proxmox From 7.3 to 8"
date: 2023-12-03T12:16:40Z
draft: false
---
**Before you start read this Proxmox Wiki:** \
[https://pve.proxmox.com/wiki/Upgrade_from_7_to_8](https://pve.proxmox.com/wiki/Upgrade_from_7_to_8)

## 1. Check version of your Proxmox
You can get the version in the top of the browser or from the terminal \
Open your Proxmox node in the terminal and run this command:
```
pveversion --verbose
```
## 2. Shutdown all your VMs make sure they dont start on bootup

## 3. do updates
```
# running commands to update and upgrade (its recomended)
apt update 
apt upgrade

# Doing this from the web interface
# Click on Node server 
->upupu
# In the center menu
-> Updates
# Click on Refresh
->refresh
# Click on Upgrade
```
## 4. Reboot the server
```
# Click on Reboot
->Reboot
```

## 5. Open server in shell command
run these commands
```
ssh nickm@upupa.loseyourip.com
sudo su -

pve7to8 --full
# This is going to tell you if these are goin to be any problems

pveversion
```
Expect to get the version of Proxmox and the version of the Linux Kernel
```
pve-manager/7.3-3/c3928077 (running kernel: 5.15.74-1-pve)
```

As we are upgrading the server its best to do this from a ssh session to the server

## 6. Check and set the sources
run these commands
```
ssh nickm@upupa.loseyourip.com
sudo su -

sudo nano /etc/apt/sources.list
```
add this line if missing:
```
deb http://download.proxmox.com/debian/pve bullseye pve-nosubscription
```

you should now have:
```
deb http://ftp.debian.org/debian bullseye main contrib

deb http://ftp.debian.org/debian bullseye-updates main contrib

# security updates
deb http://security.debian.org bullseye-security main contrib

# if you had to add this line do apt update and apt upgrade now
deb http://download.proxmox.com/debian/pve bullseye pve-no-subscription
```

bullseye is showing us that we are working with Debian 11
```
cat /etc/debian_version
```
expect to get
```
11.8
```
Debian Version 12 is bookworm \
Edit Source List replace 
```
sudo nano /etc/apt/sources.list
```
You should now have:
```
deb http://ftp.debian.org/debian bookworm main contrib

deb http://ftp.debian.org/debian bookworm-updates main contrib

# security updates
deb http://security.debian.org bookworm-security main contrib

deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription
```

Now we need to edit the file : pve-enterprise.list 
```
ls -la /etc/apt/sources.list.d
```
we get:
```
pve-enterprise.list
```
Edit this file
```
nano /etc/apt/sources.list.d/pve-enterprise.list
```

we get:
```
deb https://enterprise.proxmox.com/debian/pve bullseye pve-enterprise
```

Like wise change bullseye to bookworm and comment it out if you are not using enterprise version of Proxmox/
You should now have
```
# deb https://enterprise.proxmox.com/debian/pve bullseye pve-enterprise
```

now run update
```
apt update
```


## 7. Do Dist Upgrage
Run this command:
```
apt dist-upgrade
```
You will get a less of a list of changes \
Press Q to continue

You may get prompted about your keyboard language if so chose your keyboard
 

You may get promted about /etc You need to use default answer Proxmox will generate this


You get prompted to restart services during package updates with out asking
Answer selecting :Yes

A new version of (LibNtVokuee) of configuration file /etc/ssh/sshd_config is available \
Select: Install the package maintainers version

If you get prompted to change Vldm-conf file to be changed
Select: Install	the package maintainers	version

If you get prompted to change Grub file to be changed
Keep your own version

If you get prompted to change the Enterprise.list file
Select: Install the package maintainers version

If you get prompted to change the /etc/lvm/lvm.conf file
Select: Install the package maintainers version: y

Configuration File /etc/apt/sources.listd/pve-enterprise-list
Select: Install the package maintainers version: y

 You reach the end and need to reboot server make sure no VMs with start on bootup

```
reboot
```

## 8. Connect via terminal on Proxmox Server check new version

run these commands
```
ssh nickm@upupa.loseyourip.com
sudo su -

pveversion
```

Expect to have Proxmox version 8

```
cat /etc/debian-version
```
Expect to get Debian version 12




## Usefull Proxmox commands
Get a quick overview on how fast your system is\
Run this command: 
```
pveperf
```
expect to get:
```
CPU BOGOMIPS:      57471.52
REGEX/SECOND:      3824412
HD SIZE:           64.27 GB (/dev/mapper/pve-root)
BUFFERED READS:    239.96 MB/sec
AVERAGE SEEK TIME: 0.24 ms
FSYNCS/SECOND:     691.50
DNS EXT:           54.79 ms
DNS INT:           278.41 ms (loseyourip.com)
```

View sum of memory allocated to VMs and CTs: (4096 = 4MByte)
```
grep -R memory /etc/pve/local | awk '{sum += $NF } END {print sum;}'
```
expect:
```
126976
```

## Bibliography
https://pve.proxmox.com/wiki/Upgrade_from_7_to_8 \
https://4sysops.com/archives/upgrade-proxmox-ve-7-to-8/ 

https://www.youtube.com/watch?v=loNPimuhoqg&t=24s \
https://www.youtube.com/watch?v=i5cmx-mcUVA \
https://www.youtube.com/watch?v=zMDj6bcjNaY \
https://www.youtube.com/watch?v=br_f7VKshsE

https://www.hungred.com/how-to/server/list-of-useful-proxmox-command/ \
https://www.derekseaman.com/2023/06/how-to-proxmox-7-4-to-8-0-upgrade.html \
https://technotim.live/posts/upgrade-proxmox-to-8/ \
https://smarthomescene.com/guides/easy-upgrade-to-proxmox-8-0-without-losing-data/ \

