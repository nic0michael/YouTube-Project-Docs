---
title: "Proxmox Remove You Do Not Have a Valid Subscription Error"
date: 2024-03-22T19:16:24Z
draft: false
---

## Edit file : /etc/apt/sources.list.d/pve-enterprise.list
```
nano /etc/apt/sources.list.d/pve-enterprise.list
```

Change :
```
deb https://enterprise.proxmox.com/debian/pve bookworm pve-enterprise
```
To:
```
# Proxmox VE pve-no-subscription repository provided by proxmox.com,
# NOT recommended for production use
deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription
# security updates
deb http://security.debian.org/debian-security bookworm-security main contrib
```
## Edit file /etc/apt/sources.list.d/ceph.list
```
nano /etc/apt/sources.list.d/ceph.list
```
Change:
```
deb https://enterprise.proxmox.com/debian/ceph-quincy bookworm enterprise
```
To:
```
deb http://download.proxmox.com/debian/ceph-quincy bookworm no-subscription
```
Run these commands:
```
apt update

apt upgrade -y
```


## Bibliogrtaphy
https://pve.proxmox.com/wiki/Package_Repositories \
https://www.virtualizationhowto.com/2022/08/proxmox-update-no-subscription-repository-configuration/
