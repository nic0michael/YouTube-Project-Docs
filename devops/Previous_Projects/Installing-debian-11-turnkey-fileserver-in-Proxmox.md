---
title: "Installing Debian Turnkey Fileserver in Proxmox"
date: 2024-05-17T10:08:11Z
draft: false
---

## 1. Use Case: We need a backup file server in a Proxmox Container

**We decided to use what Proxmox provides in the form of a Proxmox Turnkey server**

## 2. Downloading the Proxmox Turnkey Fileserver
**Open Proxmox Shell run these commands :**
```
pveam available

# select the applience you need (We will use this one)
debian-11-turnkey-fileserver_17.1-1_amd64.tar.gz

# Download the ISO (use local instead of ZZ_User_Data)
pveam download ZZ_User_Data debian-11-turnkey-fileserver_17.1-1_amd64.tar.gz
```

## 3. Create a proxmox Turnkey Fileserver Container
**Create a Turnkey Fileserver Container with these specs :**
1. 100 or 256GB disk
2. 2 CPU cores
3. 4GB RAM

```                                                 
│ WebDAV (CGI):    https://10.154.2.86                     │  
│ Web shell:       https://10.154.2.86:12320               │  
│ Webmin:          https://10.154.2.86:12321               │  
│ SMB/CIFS:        \\10.154.2.86 (ports 139/445)           │  
│ SSH/SFTP:        root@10.154.2.86 (port 22)              │  
```
## 4. Login to Turnkey Webmin
Turnkey Webmin login : \
login as root (linux credentials) \
**[https://10.154.2.86:12321](https://10.154.2.86:12321)**

## 5. Configure Turnkey Fileserver from terminal
** Run these commands :**
```
# login as root
apt update

apt install sudo nano -y

apt upgrade -y
# Select package maintainers version

# become root
sudo su -

# create a user
sudo adduser nickm

# make user sudoer
usermod -aG sudo nickm

# create folder to share
mkdir /UserData

# Make user own folder
chown nickm: /userData
```

## 6. Create Shared folder in Webmin
Turnkey Webmin login : \
login as root (linux credentials) \
**[https://10.154.2.86:12321](https://10.154.2.86:12321)**

in Webmin add Webmin (administrator) user : nickm (select Unix credentials \
in Webmin create Samba share : UserData using folder /UserData

## 7. Make share read and write access from the terminal
**Run these commands :**
```
# login as root
sudo su -

# edit config file
sudo nano /etc/samba/smb.conf
```
Put this in the file under [UserData]
```
[UserData]
        force user = nickm
        user = nickm,@sudo
        writeable = yes
        path = /UserData
        public = yes
        force group = sudo
        comment = User data
```


## 8. (Optional) Configuring the Firewall:
run these commands:

```
# In Webmin, go to Networking > Linux Firewall.
# Add rules to allow traffic on necessary ports:
NFS: Port 2049
SMB: Ports 137-139 and 445
FTP: Port 21

ufw allow 2049/tcp
ufw allow 137,138,139,445/tcp
ufw allow 21/tcp
ufw enable

apt update
apt upgrade -y (if not done so)
```

## 9. mouting the share linux laptop
**Run these Commands :**
```
sudo su -
#sudo apt install nfs-common
sudo apt update
sudo apt install cifs-utils -y
sudo mkdir /UserData
chown nickm: /UserData/

# To mount the folder /UserData as a samba share
sudo mount -t cifs -o username=root,password=<password> //10.154.2.86/UserData /UserData
sudo mount -t cifs -o username=nickn,password=<password> //10.154.2.86/UserData /UserData

# To unmount
sudo umount /UserData

# To open folder in FileManager
caja
```

If you did not set permissions for share : UserData run this command
```
sudo nano /etc/samba/smb.conf
```

Put this in the file under [UserData]
```

[UserData]
        force user = nickm
        user = nickm,@sudo
        writeable = yes
        path = /UserData
        public = yes
        force group = sudo
        comment = User data
```
## 10. create a bash script for copying files from the terminal
(if you don't like using the GUI File manager Caja) \
This shell script will copy a folder/file passed as a parameter to the /UserData folder :

Run these commands :
```
sudo nano /usr/bin/samba-copy
```

Put this in the file
```
#!/bin/bash

# Check if source directory is provided
if [ -z "$1" ]; then
    echo "Error: Source directory not provided."
    echo "Usage: $0 <source_directory> [target_directory]"
    exit 1
fi

source_dir="$1"

# Check if target directory is provided, otherwise use default
if [ -z "$2" ]; then
    target_dir="/UserData/Youtube_Channel/"
else
    target_dir="$2"
fi

# Check if source directory exists
if [ ! -d "$source_dir" ]; then
    echo "Error: Source directory '$source_dir' does not exist."
    exit 1
fi

# Check if target directory exists, create if it doesn't
if [ ! -d "$target_dir" ]; then
    echo "Warning: Target directory '$target_dir' does not exist. Creating it."
    mkdir -p "$target_dir"
fi

# Copy contents of source directory to target directory
cp -Rp "$source_dir" "$target_dir"

echo "Files copied from '$source_dir' to '$target_dir' successfully."

```
Run thius command :
```
sudo chmod 775 /usr/bin/samba-copy

samba-copy <File-or-folder-to-copy>
```

## Nico's Accent is Dedecanese European
During the middle ages the Venetians (Venice Italy) had the Dodecanese Islands as one of their Colonies near to Constatinople for trading spices from the "Spice Route"
![dodecanese](http://rino.kozow.com/devops/Dodecanese.png)
