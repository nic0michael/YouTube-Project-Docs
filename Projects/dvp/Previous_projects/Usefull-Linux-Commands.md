---
title: "Usefull Linux Commands"
date: 2023-01-16T13:42:48Z
draft: false
---
## Miscelaneous Commands
```
# get Ubuntu version
lsb_release -a

# Upgrade to next version of this Linux Distro
sudo apt update
sudo apt dist-upgrade

# To change host name
sudo hostnamectl set-hostname gitlab90
hostname
sudo nano /etc/hosts
## put this there
127.0.0.1 localhost
127.0.1.1 gitlab90



sudo nano /etc/hostname
sudo reboot

# to permanently destroy a file
shred filename

# delete a directory
rmdir directory-name

rm -r directory-name


# locate a file
which finger

# find all instances of a file
whereis finger

# find a file from a directory
find / -name "filename-regex*"


# make a file executable
chmod +x script.sh

# get info about your system
sudo apt install neofetch
neofetch

# to see haow much memory you have
free

# disk space you have
df -H

# start stop a service
systemctl start apache2
systemctl stop apache2
systemctl status apache2

# to show a history of commands
history



```

## 1. Creating Symbolic links (symlinks)
```
ln -s /path/to/original /path/to/link
sudo ln -s /opt/projects/my_apps /my_apps
```

## 2. Create a user that is a sudoer
```
# you need to be logged in as root user
sudo su -

# If needed (Proxmox) install sudo
apt install sudo

# Enter the following command to add a new group:
$ sudo addgroup sudo

# create the user nickm
sudo adduser nickm
# this is better than useradd

# Set the password
passwd nickm

# Make the user a mamber of sudoers group
usermod -aG sudo nickm

# using finger to get the details of a user
sudo apt update
sudo apt install finger

finger nick

```
## 3. Set up Passwordless login
```
ssh-keygen -t rsa -b 4096

ssh-copy-id root@10.154.2.93
# or
ssh-copy-id nickm@10.154.2.93

ssh root@10.154.2.93
```

### 3.1 disable login by password
```
sudo nano /etc/ssh/ssh_config

# set the following ermitRootLogin to no
PermitRootLogin no


# set PasswordAuthentication to no
PasswordAuthentication no

sudo systemctl restart sshd

## WARNING !!!!!
# You now need to login from another terminal to ensure you have not locked yourself out 

```


## 4. Simple backup script

```
nano /usr/bin/backup 
```
Put this into the file:
```
#!/bin/bash
cd /home/opc/

tar -zcvf website-backup.tar.gz website/
```

## 5. to extract Tarball (tar.gz file)
```
tar -xvzf my_tarball_file.tar.gz
```

## 6. to zip and extract extract a zip file
```
zip filename.zip file-or-folder-to-zip

gunzip my_zip_file.zip

unzip my_zip_file.zip
```

## 7. Ubuntu Set Static IP address
Find the file : 1-network-manager-all.yaml
```
ls -la /etc/netplan/
# Expect
-rw-r--r--   1 root root   104 Feb  8  2018 1-network-manager-all.yaml

```

```
sudo nano /etc/netplan/1-network-manager-all.yaml
```
Comment out the top part of the file use #
put this in the bottom of the file

change the network device

```
network:
  version: 2
  renderer: networkd
  ethernets:
    ens3:
      dhcp4: no
      addresses:
        - 192.168.121.221/24
        - 10.154.2.93/24
      gateway4: 10.154.2.3
      nameservers:
          addresses: [8.8.8.8, 1.1.1.1]
```
revised yaml file
```
network:
  version: 2
  renderer: networkd
  ethernets:
    eno1:
      dhcp4: false
      dhcp6: false
      addresses:
       - 10.154.2.89/24
      routes:
       - to: default
         via: 10.154.2.3
      nameservers:
        addresses: [8.8.8.8,8.8.4.4]
```

Now run this command:
```
sudo netplan apply
```
Show the Ip of the device ens3
```
ip addr show dev ens3
```
## 8. List Disks on Linux using lsblk
```
lsblk
```

## 9. Fully format and wipe disk

### 9.1 Create a partition on the disk
Firstly, connect the disk to your Linux system if you haven't already and launch the Terminal window on it. \
You can enter the following command to check it:

```
sudo fdisk -l

# you can also use 
lsblk
```
expect to get:
```
Disk /dev/sda: 476.94 GiB, 512110190592 bytes, 1000215216 sectors
Disk model: SK hynix SC311 S
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: B8C2AB97-AF7E-4515-9721-E210B730D5D1

Device         Start        End   Sectors   Size Type
/dev/sda1       2048     206847    204800   100M EFI System
/dev/sda2     206848     239615     32768    16M Microsoft reserved
/dev/sda3     239616  818811532 818571917 390.3G Microsoft basic data
/dev/sda4  999170048 1000212479   1042432   509M Windows recovery environment
/dev/sda5  818812928  850812927  32000000  15.3G Linux swap
/dev/sda6  850812928  999170047 148357120  70.7G Linux filesystem
```
Now, to create a partition, enter the command "fdsk" in the following format:
```
sudo fdisk /dev/sdb
```

This will launch the results of the fdisk command. If you want, you can type "m" to get help. \
It will display a list of the supported parameters. \
You can type "n" to create a new partition, "d" to delete the partition, "p" to check the partition table, and so on.



Firstly, press "p" and enter to view the partition table. 
This will let you know about the disk identifier and the sector space. \
Subsequently, enter the "n" command to create a new partition. \
You will be given an option to create a primary or an extended partition. /
Press "p" to create a new primary partition and give it a number from 1 to 4. \
If you want to create a single partition, then enter "1".


### 9.2 Format the disk
Great! Once you have created the relevant partition on your Linux system, you can format it by entering the command –
```
sudo mkfs.ext4 /dev/sdb
```


### 9.3 Mount the file system (optional)
If you want, you can mount the file system as well. \
To do this, you can use the "mkdir /data" command to make a directory. 

After that, end the following command to mount it:
```
sudo mkdir /data
mount /dev/sdb1 /data
# or the whols drive
mount /dev/sdb /data
sudo mkdir /data/test

df -h
```

### 9.4 To Wipe a Hard Drive on Linux
If you are planning to resell your system or are concerned about your privacy, then you should consider wiping the drive instead. \
Unlike formatting a disk, wiping it will erase the data and make the recovery process harder than before. \
Thankfully, there are multiple commands to do Linux wipe the disk. \
Here are some simple solutions to wipe a hard drive on Linux.

#### 8.4.1 Wipe the disk / partition
As the name suggests, the command is used to wipe data from a magnetic disk. Though, a lot of Linux systems do not have the command readily installed. In this case, you can use the apt install command first.

```
sudo apt install wipe
```
Once it is done, just use the "wipe" command in the format - wipe [options] target. For instance, to wipe a partition, simply enter the command:

```
sudo wipe /dev/sdb2
# or 
sudo wipe /dev/sdb
```
Confirm your choice, by entering "yes" and wait as the selected partition would be wiped.

#### 9.4.2 shred
This is one of the best ways to protect your private data on a Linux system. \
Ideally, this works as a dedicated shredder – that would overwrite your data with something else, making the recovery process harder. \

This Linux based command has the following syntax:

```
sudo shred [option] target
```

As you know, "target" would specify the location you wish to shred. \
It can be a partition, folder, or file name. 

Subsequently, it can have the following options:
```
-n: To overwrite data "n" times
-f: To change permission and allow the writing operation
-u: Truncates the files after shredding them
-s: To provide the size to shred
-u: To remove the file after shredding
-v: To enable the verbose mode
-z: To add zeros to the final overwriting process
```
 
Therefore, you can wipe the Linux disk, by entering a command like this:

```
sudo shred -vfz -n 10 /dev/sdb2
# or
sudo shred -vfz -n 10 /dev/sdb2
```

#### 9.4.3 DD to Erase the entire disk
If you are running short on time, then consider using the "dd" command to erase disk on a Linux system. \
Instead of generating random data, it will overwrite the entire disk with strings of zeros. \
Therefore, it will take less time to wipe the disk and protecting your information. \
Although, it provides certain options that you can use to customize the process.

```
sudo dd if=source of=target [Options]
```

Make sure that you run the command prompt and as a super-user. 

Here's a simple demonstration of the same.

```
sudo dd if=/dev/zero of=/dev/sdb2 bs=512 count=1
# or
sudo dd if=/dev/zero of=/dev/sdb bs=512 count=1
```

The command will overwrite the target location with a string of zeros, as specified in the source. \
Also, this will copy 512 bytes in a single count. \
One of the major advantages of this is the time taken by the dd command is lesser than shred.



## 10. To Check Hard Drive for Bad Sectors or Blocks in Linux
Badblocks is the command-line utility in Linux like operating systems which can scan or test the hard disk and external drive for bad sectors. \
Bad sectors or bad blocks is the space of the disk which can’t be used due to the permanent damage or OS is unable to access it.

Badblocks command will detect all bad blocks(or bad sectors) of a hard disk and save them in a text file. \
Later we can use it with e2fsck to instruct Operating System(OS) not to store any data on these damaged sectors or blocks.

In this article, we will learn how to check or scan hard drive for bad sectors using badblocks command.

### 10.1 Use fdisk command to identify hard drive info
Run fdisk command to list all available hard disks to Linux operating system. \
Identify the disk which you want to scan for bad blocks.

```
sudo fdisk -l
```

#### 10.1.1 Scan hard drive for Bad Sectors or Bad Blocks 
Once you identify the hard disk then run badblocks command. Example is shown below

```
sudo badblocks -v /dev/sdb > bad-blocks.txt
```

#### 10.1.2 Inform OS not to use bad blocks for storing data
Once the scanning is completed, if the bad sectors are reported then use file “bad-blocks.txt” with e2fsck command and \
force OS not to use these bad blocks for storing data. Run the following e2fsck command

```
sudo e2fsck -l /tmp/bad-blocks.txt /dev/sdb
# or
sudo e2fsck -c /dev/sdb
```
Note : Before running e2fsck command, you just make sure the drive is not mounted.

For any further help on badblocks & e2fsck command , read their respective man pages

```
man badblocks
man e2fsck
```

#### 10.1.3 A comment
You don’t need to run badblocks first. \
That’s a waste of time. Just use the e2fsck executable to run it for you with the -c option…

```
sudo e2fsck -c /dev/sdb
```

This lets you avoid creating an unnecessary file, especially when you’re booting to an optical drive and don’t want to write to the hard drive you’re testing.

#### Have a look at this article
https://www.addictivetips.com/ubuntu-linux-tips/check-hard-drive-health-on-linux/

