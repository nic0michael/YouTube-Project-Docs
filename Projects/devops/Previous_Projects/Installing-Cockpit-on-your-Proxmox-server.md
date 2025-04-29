---
title: "Installing Cockpit instead of a Proxmox"
date: 2023-10-01T13:10:54Z
draft: false
---
## 1. Installing KVM
### 1.1 Check if Host machine supports Virtualization
```
egrep -c '(vmx|SVM)' /proc/cpuinfo

lscpu | grep Virtualization:
```
expect :
```
Virtualization:                  VT-x
```
This means the server CPU supports virtualization

### 1.2 Install KVM
```
sudo su -
apt update -y

apt install qemu-kvm qemu-kvm virt-viewer virt-manager bridge-utils -y
# libvirt virt-install
reboot
```

log in again
```
sudo useradd -g $USER libvert

sudo su -
```
### 1.3 Validate Virtualization Host
```
apt install libvirt-clients

virt-host-validate
```

Expect to get 2 warnings one we can Fix
```
  QEMU: Checking if IOMMU is enabled by kernel  : WARN (IOMMU appears to be disabled in kernel. Add intel_iommu=on to kernel cmdline arguments)
  QEMU: Checking for secure guest support       : WARN (Unknown if this platform has Secure Guest support)

```

### 1.4 # WARN (Enable 'devices' in kernel Kconfig file or mount/enable cgroup controller in your system)
```
sudo grubby --update-kernel=ALL --args="cgroup_enable=cpuset cgroup_enable=cpu cgroup_enable=devices cgroup_enable=freezer cgroup_enable=blkio"
```


We did this and fixed the Intel CPU | Secure guest support warning we can ignore
```
#edit grub bootloader
#vi /etc/default/grub # add add parameter values

```

```
nano /etc/default/grub

# add to the end of GRUB_CMDLINE_LINUX="
intel_iommu=on iommu=pt
```

### 1.5 Confugure grub

Run this command
```
#grub2-mkconfig -o /boot/grub2/grub.cfg  # 
#grub2-mkconfig -o /boot/grub2/grub.cfg
cp /boot/grub/grub.cfg /boot/grub/grub.cfg.backup

grub-mkconfig -o /boot/grub/grub.cfg
sudo grub-mkconfig
# reboot machine
reboot
```
When machine starts again
```
sudo su -
# Validate Virtualization Host again
virt-host-validate
```


## 2. Installing Cockpit 
```
sudo su -
sudo apt-get install cockpit cockpit-machines pcp cockpit-pcp -y

# optionally packagekit virt-viewer


systemctl enable --now cockpit.socket
systemctl start --now cockpit.socket

systemctl enable --now pmcd
systemctl enable --now pmlogger

systemctl start --now pmcd
systemctl start --now pmlogger

sudo ufw allow 9090/tcp


```
## 3. Create a non root cockpit user
```
# create the user 
sudo adduser mycockpituser
passwd mycockpituser

# make user member of the libvert  group
sudo usermod -aG libvirt mycockpituser
sudo usermod -aG libvirt $USER

```

## 4. Opening the Cockpit Control Panel
```
# get the server IP
ip a
```
Now open the browser to:

**https://YOUR-VPS-IP-ADDRESS:9090**

in my case: \
[http://10.154.2.89:9090](http://10.154.2.89:9090)



