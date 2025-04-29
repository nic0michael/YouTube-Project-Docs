---
title: "Installing KVM Virtualization on Ubuntu"
date: 2023-01-22T06:39:55Z
draft: false
---

## 1. Check if Virtualization is setup on your motherboard's BIOS and then run this command
```
egrep -c '(vmx|svm)' /proc/cpuinfo
```

you need a number greater than 0 to be able to use this virtualization \
16 is OK

if this number is zero you need to go into your BIOS to enable virtualization
You may need to study your PC's documentation to find out how to enable virtualization


egrep is a more advanced version of Grep the c flag makes it count \
vmx is the virtualization module for Intel CPU's \
svm is the equivalent for AMD CPU's \
/proc/cpuinfo is a file with info about your CPU


Lookup the instructions for your Distro \
Assume you are running the Mint Linux Distro \
do a google search for :  mint linux install kvm



You will need to run these commnds
```
sudo apt update
sudo apt upgrade
```

Installing KVM
```
sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager

```

Add Your User to the KVM and Libvirt Group
```
sudo usermod -aG kvm $USER
sudo usermod -aG libvirt $USER

```

## To Create a VM (Vitrual Machine) in KVM
Launch the Virtual Machine Manager on your PC

From the file menu \
 click on -> New Virtual Machine

choose how you would like to installthe operating system \
 click on -> (o) Local Install media \
 now click -> Forward button

Clisk on -> Browse button (and locate your ISO file) \
check ->[~] Automatically detecte from the installation media / source \
 now click -> Forward button





## This us what ChatGPT recomands
Installing KVM on Mint Linux is a relatively simple process. \
Here is a step-by-step guide to help you get started:

Open a terminal window by pressing Ctrl+Alt+T

Type the command sudo apt-get update to update the package lists
```
sudo apt-get update
```
Type the command sudo apt-get install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils to install KVM and its dependencies
```
sudo apt-get install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils
```
Type the command sudo adduser $USER libvirt to add your user to the libvirt group
```
sudo adduser $USER libvirt
```
Type the command sudo adduser $USER libvirt-qemu to add your user to the libvirt-qemu group
```
sudo adduser $USER libvirt-qemu
```
Log out and log back in for the changes to take effect

Type the command virsh list --all to verify that the installation was successful and that the KVM daemon is running.
```
virsh list --all
```
To create virtual machine, you can use virt-manager GUI tool or virsh command line tool

To manage your virtual machines, you can use virt-manager or virsh commands









## Bibliography
https://www.youtube.com/watch?v=BgZHbCDFODk&t=400s

https://help.ubuntu.com/community/KVM/Installation

https://linuxhint.com/install_kvm_virtualization_ubuntu/

https://community.linuxmint.com/tutorial/view/1727
