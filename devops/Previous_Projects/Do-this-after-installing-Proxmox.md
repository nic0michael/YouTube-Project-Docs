---
title: "Do This After Installing Proxmox"
date: 2025-02-15T07:34:24Z
draft: false
---

**We need to do this after installing or after doing a major version upgrade**

**You do this  if you don't want to pay for a subscription and use  the free option**
## Changing production files
**Run this command**
```bash
# ssh to your Proxmox server from PUTTY for fom you Linux terminal
ssh root@upupa.loseyourip.com

sudo su -
```

**Edit this file:**
```bash
nano /etc/apt/sources.list
vi /etc/apt/sources.list
```

**The file should only have this:**
```
deb http://ftp.debian.org/debian bookworm main contrib

deb http://ftp.debian.org/debian bookworm-updates main contrib

# security updates
deb http://security.debian.org bookworm-security main contrib

```


**Edit this file**
```bash
nano /etc/apt/sources.list.d/pve-enterprise.list
vi /etc/apt/sources.list.d/pve-no-enterprise.list
```
**The file should only have this:**
```
deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription
 
comment out this:
# deb https://enterprise.proxmox.com/debian/pve buster pve-enterprise
```

## Adding / Editing non-production files

**Edit / Create file**
```bash
nano /etc/apt/sources.list.d/pve-no-enterprise.list
vi /etc/apt/sources.list.d/pve-no-enterprise.list
```
**The file should only have this:**
```
 not for production use
deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription
```


**Edit / Create file**
```bash
nano /etc/apt/sources.list.d/ceph.list
vi /etc/apt/sources.list.d/ceph.list
```
**The file should only have this:**
```
# not for production use
deb http://download.proxmox.com/debian/ceph-quincy bookworm no-subscription
```

## Finally here are the commands to run

**run these commands**
```bash
apt update

apt upgrade -y

apt dist-upgrade -y

reboot
```

