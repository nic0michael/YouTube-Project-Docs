---
title: "Install KVM on Rocky Linux"
date: 2024-02-12T12:41:41Z
draft: false
---

## KVM server Implementation

### 1. Installing KVM
#### 1.1 Setup Password less Root User login to server
Create your Certificates
```
ssh-keygen -t rsa -b 4096
```

Look here for instructions \
http://rino.kozow.com/linux/posts/make-centos-user-a-sudoer/


#### 1.1.1 KVM Server 1 server-1
SSH To Get Servers Public Certificate 
```		
ssh root@54.67.32.45
```
Upload your public certificate to server
```
ssh-copy-id root@54.67.32.45
```
You should now be able to login without a password
```
ssh root@54.67.32.45
```
#### 1.1.2 KVM Server 2 server-2
SSH To Get Servers Public Certificate
ssh root@77.68.116.99

Upload your public certificate to server
ssh-copy-id root@77.68.116.99
You should now be able to login without a password
ssh root@77.68.116.99

1.2 Change the HostName
1.2.1 KVM Server 1 server-1
Run these commands
HOST_NAME=fb-server-1-de-p.lp.runSERVER_IP="54.67.32.45"
hostnamectl set-hostname ${HOST_NAME}
cat /etc/hostname

Add hostname to hosts file
echo "127.0.1.1       ${HOST_NAME}" | sudo tee -a /etc/hostsecho "${SERVER_IP}       ${HOST_NAME}" | sudo tee -a /etc/hosts

vi /etc/hosts
sudo su -
# Be very patient when running this a problem is first fixed
dnf install glibc-langpack-en -y
dnf install epel-release -y

dnf makecache
dnf update -y
1.2.1 KVM Server 2 server-2
Run these commands
HOST_NAME=fb-server-2-br-dev.lp.runSERVER_IP="77.68.116.99"
hostnamectl set-hostname ${HOST_NAME}
cat /etc/hostname

Add hostname to hosts file
echo "127.0.1.1       ${HOST_NAME}" | sudo tee -a /etc/hostsecho "${SERVER_IP}       ${HOST_NAME}" | sudo tee -a /etc/hosts

vi /etc/hosts
sudo su -
# Be very patient when running this a problem is first fixed
dnf install glibc-langpack-en -y
dnf install epel-release -y

dnf makecachednf update -y
1.3 Check if Host machine supports Virtualization
Get version of Rocky Linux
cat /etc/os-release | grep ROCKY_SUPPORT_PRODUCT
Get CPU Virtualization Info
egrep -c '(vmx|SVM)' /proc/cpuinfo
Check that the CPU supports Virtualization
lscpu | grep Virtualization
expect :

Virtualization:                  VT-x
This means the server CPU supports virtualization

1.4 Validate Virtualization Host
dnf install virt-viewer libvirt virt-install virt-manager -y
Validate INTEL CPU settings 
virt-host-validate
Expect to get 2 warnings one we can Fix

  QEMU: Checking if IOMMU is enabled by kernel  : WARN (IOMMU appears to be disabled in kernel. Add intel_iommu=on to kernel cmdline arguments)
  QEMU: Checking for secure guest support       : WARN (Unknown if this platform has Secure Guest support)

1.5 Addressing  WARNING regarding INTEL CPU 
Run the Grubby command
sudo grubby --update-kernel=ALL --args="cgroup_enable=cpuset cgroup_enable=cpu cgroup_enable=devices cgroup_enable=freezer cgroup_enable=blkio"
We did this and fixed the Intel CPU | Secure guest support warning we can ignore

Edit grub bootloader and add parameters
vi /etc/default/grub 
Add parameters to the end of GRUB_CMDLINE_LINUX="
intel_iommu=on iommu=pt

vi /etc/default/grub
Add intel_iommu=on iommu=pt to GRUB_CMDLINE_LINUX
grub2-mkconfig -o /boot/grub2/grub.cfg
..this did not work?

grubby --update-kernel=ALL --args="intel_iommu=on iommu=pt"
..this worked.

1.6 Configure grub
Backup the Grub Config
cp /boot/grub/grub.cfg /boot/grub/grub.cfg.backupIf the above is not found
cp /boot/grub2/grub.cfg /boot/grub2/grub.cfg.backup

Generate changes in Grub 
grub2-mkconfig -o /boot/grub2/grub.cfg

Also run this commandsudo grub2-mkconfig
If the above is not found
grub-mkconfig -o /boot/grub/grub.cfgsudo grub-mkconfig

Reboot machine
reboot

When machine starts again
sudo su -

Validate Virtualization Host again
virt-host-validate
1.7 Install KVM
This may take a while
dnf install qemu-kvm qemu-img bridge-utils -y
reboot
log in again

Add user to Libvirt Group
sudo useradd -g $USER libvert
sudo su -
2. Installing the Prerequisites

dnf update -y
dnf check-update -y
dnf upgrade -y

dnf install wget curl nano telnet -y
dnf install bridge-utils net-tools -y
dnf install epel-release -y
dnf update -y
dnf install NetworkManager -y
2.1 Migrate to Network Manager

Auto copy file from /etc/sysconfig/network-scripts to /etc/NetworkManager/system-connections
 
nmcli connection migrate
 
sysctl net.ipv6.conf.all.forwarding

Expect:
net.ipv6.conf.all.forwarding = 1

ls -la /etc/NetworkManager/system-connections
cat /etc/NetworkManager/system-connections/Public_ens192.nmconnection
cat /etc/NetworkManager/system-connections/eth0.nmconnection

2.2 Setting the KVM IP Range
Run this commandsudo virsh net-list --all

Expect
 Name      State    Autostart   Persistent
--------------------------------------------
 default   active   yes         yes

For Kubernetes VMs we want 10.154.2.110 to 10.154.2.199  
Set the VM IP Range
sudo virsh net-edit default

192.168.240.10

put this in the file
<network>
  <name>default</name>
  <uuid>daa3521f-a1e6-44e1-a75c-1442b27bea2e</uuid>
  <forward mode='nat'/>
  <bridge name='virbr0' stp='on' delay='0'/>
  <mac address='52:54:00:ec:eb:80'/>
  <ip address='10.154.2.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='10.154.2.110' end='10.154.2.199'/>
    </dhcp>
  </ip>
</network>

<network>
  <name>default</name>
  <uuid>daa3521f-a1e6-44e1-a75c-1442b27bea2e</uuid>
  <forward mode='nat'/>
  <bridge name='virbr0' stp='on' delay='0'/>
  <mac address='52:54:00:ec:eb:80'/>
  <ip address='192.168.240.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.240.100' end='192.168.240.199'/>
    </dhcp>
  </ip>
</network>

Restart the libvirt network to apply the changes:
sudo virsh net-destroy default
sudo virsh net-start default
sudo reboot

Verify the IP range has been updated:
3. Setting up Bridge Network
We decided not to do this as we have Bridge:  virbr0Start and enable the NetworkManager service
systemctl start NetworkManager
systemctl enable NetworkManager

Enable masqueradingsystemctl start firewalld
systemctl enable firewalld
sudo firewall-cmd --permanent --zone=public --add-masquerade
Reload the firewall to apply the changes
sudo firewall-cmd --reload

ip a
brctl show
nmcli connection shownmcli conn delete <unwanted-device>
3.1 Create a bridge interface with `nmcli`:
nmcli connection add type bridge ifname br-vm-lan1 con-name br-vm-lan1
3.2 Assign an IP address to the bridge interface:
nmcli connection modify br-vm-lan1 ipv4.address 10.154.2.1/24
3.3. Activate the bridge interface:
nmcli connection up br-vm-lan1
systemctl restart NetworkManager
3.4 Export the connection profile to a file:
#NOT NEEDED nmcli connection export type=bridge name br-vm-lan1 > /etc/NetworkManager/system-connections/br-vm-lan1

ls -la /etc/NetworkManager/system-connectionscat /etc/NetworkManager/system-connections/br-vm-lan1.nmconnection
Expect:
[connection]
id=br-vm-lan1
uuid=eae87e77-ea88-44b4-81ab-c7b988b4ee43
type=bridge
interface-name=br-vm-lan1

[ethernet]

[bridge]

[ipv4]
address1=10.154.2.1/24
method=auto

[ipv6]
addr-gen-mode=default
method=auto

[proxy]

3.5 Restart NetworkManager to apply the changes:

sudo systemctl restart NetworkManager

ping 192.168.122.164
ssh root@192.168.122.164
dnf install net-tools
ifconfig enp1s0 10.154.2.40 netmask 255.255.255.0
ping 10.154.2.40
ssh root@10.154.2.40


If this did not work now we try this

sudo ip link add name br-vm-lan0 type bridge
sudo ip addr add 10.154.2.1/24 dev br-vm-lan0
sudo ip link set dev br-vm-lan0

sudo ip link add name br-vm-lan1 type bridge
sudo ip addr add 10.154.2.1/24 dev br-vm-lan1
sudo ip link set dev br-vm-lan1 up

If we need this ?
4. Install Cockpit
4.1 Installing Cockpit
sudo su -
#dnf install cockpit cockpit-machines cockpit-pcp -y
sudo dnf install cockpit -y
sudo dnf install cockpit-machines -y
sudo dnf install cockpit-pcp -y
sudo dnf install PackageKit -y

systemctl enable --now firewalld
systemctl start --now firewalldsystemctl status firewalld

systemctl enable --now cockpit.socket
systemctl start --now cockpit.socket

systemctl enable --now pmcd
systemctl start --now pmcd

systemctl enable --now pmlogger
systemctl start --now pmlogger

systemctl status --now cockpit.socket
systemctl status --now pmcd
systemctl status --now pmlogger

firewall-cmd --permanent --zone=public --add-service=cockpit
firewall-cmd --reload

4.2 Create a non root cockpit user
cockpituser
!@#$wseRY&^HG$#FTYU%
=============================
P455w0rd123

Create the user 
sudo adduser kajuit
passwd kajuit 
# make user member of the libvirt  group
sudo usermod -aG libvirt kajuit
sudo usermod -aG libvirt $USER

4.3 Opening the Cockpit Control Panel
4.1 KVM Server 1 server-1
Get the server IP
ip a
Now open the browser to:
https://YOUR-VPS-IP-ADDRESS:9090

https://54.67.32.45:9090/

4.2 KVM Server 2 server-2
Get the server IP
ip a
Now open the browser to:
https://YOUR-VPS-IP-ADDRESS:9090

https://77.68.116.99:9090/
 Open Port 9090 In FastHosts Console
5. Create VMs
5.1 Download ISO File
mkdir /opt/vms
mkdir /opt/vms/master-files
cd /opt/vms/master-files

wget https://download.rockylinux.org/pub/rocky/9/isos/x86_64/Rocky-9.2-x86_64-minimal.iso

Nico will SFTP the file VM200-disk-0.qcow2 to the master-files folder
VM200-disk-0.qcow2.tar.gz
Extract the tarBall (This is a big file will take a while to extract)
cd /opt/vms/master-files 
tar -xvzf VM200-disk-0.qcow2.tar.gz

cd /opt/vms

Make two BASH Shell Scripts to Copy Files and Create VMs from a QCOW2 file
vi make-file.sh
Put this in the file
#!/bin/bash

# Check if two parameters are provided
if [ $# -ne 2 ]; then
  echo "Usage: $0 <vm_name> <qcow2_file>"
  exit 1
fi

vm_name="$1"
qcow2_file="$2"

echo "Copying file $qcow2_file from master-files"
cp "master-files/$qcow2_file" "$vm_name-disk-0.qcow2"
echo "File $qcow2_file copied as: $vm_name-disk-0.qcow2"

Make this file executable
chmod 775 make-file.sh

Create second ShellScript
nano createvm.sh
Put this in the file
#!/bin/bash

# Check if two parameters are provided
if [ $# -ne 2 ]; then
  echo "Usage: $0 <vm_name> <qcow2_file>"
  exit 1
fi

vm_name="$1"
qcow2_file="$2"

echo "Creating VM: $vm_name"
virt-install --name "$vm_name-ss1" --memory 2048 --vcpus 1 --disk /opt/vms/"$qcow2_file",bus=sata --import --os-variant rocky9-unknown --network default &

Make this file executable
chmod 775 createvm.sh5.2 Creating The VM
Run this command and go get a cup of tea it will take a very long time./make-file.sh vm40-ss1 &

Then run this command and open Cockpit./createvm.sh vm40-ss1 &When the createvm.sh script starts running virt-install open the Cockpit DashboardCreating VM: vm40-ss1
https://54.67.32.45:9090/ 

The VM will now have a DHCP IP address
In cockpit open the VM and run
Ip aConnect to the VMssh root@10.154.2.167
dnf update -y
dnf install net-tools -y
ip a

run nmtui and set static IP Address

ifconfig enp1s0 10.154.2.40 netmask 255.255.255.0
ping 10.154.2.40
ssh root@10.154.2.40
Disable Firewall on Kubernetes VMssystemctl stop firewalld
systemctl disable firewalld
systemctl status firewalld

If static IP does not work do this on the host
  virsh net-edit default
  virsh net-destroy default
  virsh net-start default
nmcli con up <host card>

virsh net-update default add ip-dhcp-host '<host mac="52:54:00:2f:e3:e3" ip="192.168.240.100"/>' --live --config
