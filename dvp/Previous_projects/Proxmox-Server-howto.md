---
title: "Proxmox Server Howto"
date: 2023-03-07T13:01:43Z
draft: false
---

Login to server: \
https://upupa.loseyourip.com:8006

## 1. Moving VM disk storage
```
Left menu -> Node server
Center menu  -> VM
                -> Hardware
                   -> Hard Disk
Center tab    ->[Disk Action]
                 -> Move Disk
                    -> Target Storage 
                    -> [Move Disk]
```

## 2. Unmounting a disk
```
ls -la /mnt/pve/
```
Expect to get:
```
drwxr-xr-x 8 root root 4096 Mar  7 15:51 Storage
drwxr-xr-x 8 root root 4096 Mar  6 16:16 VMdata
```
To unmount /mnt/pve/Storage :
```
# logged in as root
sudo umount /mnt/pve/Storage
```

## 3. Removing Storage

```
Left menu -> Datacentre

Center menu  -> Storage

Right menu   -> Directory-Name

Top Button -> [Remove]
               ->[Yes]
```

