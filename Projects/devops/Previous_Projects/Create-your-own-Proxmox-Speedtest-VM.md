---
title: "Create Your Own Proxmox Speedtest VM"
date: 2024-02-10T07:01:56Z
draft: false
---

# dont use OpenSpeedTest the software does not work 

We will install an Open Source Self-Hosted SpeedTest server with Managed Database.

## 1. Prerequisites
1. You will need an Ubuntu Server VM (We do provide instructions for Rocky Linux)
2. Install the OpenSpeedTest software
3. Set VM with Static IP Address (In my case 10.154.2.80)

## 2. Software source
[OpenSpeedTest](https://openspeedtest.com/selfhosted-speedtest)

**Locate the download needed (64 Bit versions):** 
1. Ubuntu Server : [OpenSpeedTest-Server_2.1.8_amd64.deb](https://go.openspeedtest.com/Linux) 
2. Rocky Linux Server: [OpenSpeedTest-Server_2.1.8.x86_64.rpm](https://go.openspeedtest.com/LinuxRPM)
3. Raspberry Pi : [OpenSpeedTest-Server_2.1.8_arm64.deb](https://go.openspeedtest.com/LinuxARM)

## 3. Create your Ubuntu Server VM
Use Proxmox connect to it via the browser and create an Ubuntu Server VM

## 4. Install openspeedtest on your Ubuntu Server VM
**Connect to your Ubuntu Server/VM to download software:**
```
ssh 10.154.2.80

sudo su -

sudo apt update -y

cd ~\

mkdir Downloads
cd Downloads

# wget https://go.openspeedtest.com/Linux

# This file does not download from the above URL
# Upload the files to your VM using Filezilla

scp /home/nickm/Downloads/OpenSpeedTest-Server_2.1.8_amd64.deb nickm@10.154.2.80:/home/nickm/

ls -la
```
Expect to get
```
OpenSpeedTest-Server_2.1.8_amd64.deb
```

**Install the Debian Package**
```
sudo dpkg -i OpenSpeedTest-Server_2.1.8_amd64.deb
```
## 5. Run the software on your server
Run this command
```
openspeedtest-server
```

## 6. Open test in your browser
**Firefox is not recommended for Stress Tests above 10 Minutes or Speeds above 2.5Gbps. ** \
**http://<your-server-ip>:3000** \
**[http://10.154.2.80:3000](http://10.154.2.80:3000) 

**To Run the test for 60 Seconds** \
**[http://10.154.2.80:3000?run=60](http://10.154.2.80:3000?run=60)
## 7. Rocky linux Installation
```
ssh 10.154.2.81

sudo su -

# Disable the firewall
systemctl stop firewalld
systemctl disable firewalld

sudo apt update -y

cd ~\

mkdir Downloads
cd Downloads

wget https://go.openspeedtest.com/LinuxRPM

ls -la
```
Expect to get
```
OpenSpeedTest-Server_2.1.8.x86_64.rpm
```
Run these commands
```
# sudo dnf install <package_name>
sudo dnf install OpenSpeedTest-Server_2.1.8.x86_64.rpm


openspeedtest-server
```






## Bibliography
https://openspeedtest.com/selfhosted-speedtest \
https://www.youtube.com/watch?v=6z4X9LWvjlM
