---
title: "Do this after installing Proxmox"
date: 2023-03-01T07:23:41Z
draft: false
---

## 1. Adding another Hard Drive using Web Interface
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

## 2. After installing Proxmox
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
## 3. Remove Proxmox Subscription Notice

**Execute these commnds**
```
sudo su -
# or login as root

cd /usr/share/javascript/proxmox-widget-toolkit
```

**Backup proxmoxlib.js**
```
cp proxmoxlib.js proxmoxlib.js.bak
```

**Edit the file proxmoxlib.js**
```
nano proxmoxlib.js
```

**Search for No valid subscription**
```
# Press these keys [Ctrl]& [w] and put this search text there :
No valid subscription
# ens press the {Enter] key 
```

**Replace this Ext.Msg.show :
```
Ext.Msg.show({
  title: gettext('No valid subscription'),
```

**With this void**
```
void({ //Ext.Msg.show({
  title: gettext('No valid subscription'),
```

**Save the file**
```
Press these keys [Ctrl]& [s] and [Ctrl] & [x]
```

**Restart the Proxmox web service**
```
systemctl restart pveproxy.service
```

## Bibliography
https://www.youtube.com/watch?v=GoZaMgEgrHw&t=622s \
https://johnscs.com/remove-proxmox51-subscription-notice/
