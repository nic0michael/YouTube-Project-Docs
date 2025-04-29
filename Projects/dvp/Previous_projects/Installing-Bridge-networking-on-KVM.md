---
title: "Installing Bridge Networking on KVM"
date: 2023-02-07T10:06:56Z
draft: false
---
## Ubuntu Networks
wlp2s0

## Create a bridge network using nmtui
### Warning this is dangerous


run command bridge control show bridges
```
sudo apt install bridge-utils
brctl show
```

use nmtui to define a new switch or bridge
```
sudo nmtui
```
```
->Edit a connection

->Select type of connection
 ->Bridge

profile name: Bridge connection
Device : nm-bridge

Add Wifi as a slave of this bridge

Select the type of slave connection you wish to add.
 ->Ethernet
->Create
Bridge PORT
priority : 32
path cost:100

->OK
Remove the wired connection
->Edit a connection
-> Ethernet
-> wired connection
-> Delete
-> Back
quit nmtui
```
it takes 10 to 15 minutes for the bridge to get connected

```
ip addr
# Expected we should find nm-bridge
```

```
brctl show
# expect to see nm-bridge

```
its interface should be wifi wlp3s0



## Create KVM NAT with virsh command.
```
nano br10.xml
```
put this is the file:
```
<network>
  <name>br10</name>
  <forward mode='nat'>
    <nat>
      <port start='1024' end='65535'/>
    </nat>
  </forward>
  <bridge name='br10' stp='on' delay='0'/>
  <ip address='192.168.30.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.30.50' end='192.168.30.200'/>
    </dhcp>
  </ip>
</network>
```

To define a network from an XML file without starting it, use:
```
sudo virsh net-define  br10.xml
# expect: 
Network br1 defined from br10.xml
```

To start a (previously defined) inactive network, use:
```
sudo virsh net-start br10
# expect
Network br10 started
```
To set network to autostart at service start:
```
sudo virsh net-autostart br10
# expect 
Network br10 marked as autostarted
```

Check to Confirm if autostart flag is turned to yes – Persistent should read yes as well.
```
sudo virsh net-list --all
# expect
 Name              State    Autostart   Persistent
----------------------------------------------------
 br10              active   yes         yes
 default           active   yes         yes
 docker-machines   active   yes         yes
 fed290            active   no          yes
 vagrant-libvirt   active   no          yes
```

Confirm bridge creation and IP address.
```
ip addr show dev br10
# expect
28: br10: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 52:54:00:94:00:f5 brd ff:ff:ff:ff:ff:ff
    inet 192.168.30.1/24 brd 192.168.30.255 scope global br10
       valid_lft forever preferred_lft forever
```


## _______________________________________________________________
vim br1.xml


```
sudo virsh net-define br1.xml
Network br1 defined from br1.xml
```
To define a network from an XML file without starting it. So run the below command.
```
sudo virsh net-define br1.xml
Network br1 defined from br1.xml
```
To start a (previously defined) inactive network, run the below command.
```
sudo virsh net-start br1
Network br1 started
```

To set network to autostart at service start, run the below command.
```
sudo virsh net-autostart br1
Network br1 marked as autostarted
```

Then, confirm if the autostart flag is turned to yes. In the result, the Persistent should read yes as well.
```
sudo virsh net-list -all
```

Also, make sure to confirm bridge creation and IP address.
```
ip addr show dev br1
```
## Create Bridge Network

To create a new bridge, we can still use the ip command. Let’s say we want to name this bridge br0; we would run the following command:
```
sudo ip link add br0 type bridge
```

To verify the bridge is created we do as before:
```
sudo ip link show type bridge
# expect 
5: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default qlen 1000
    link/ether 52:54:00:48:3f:0c brd ff:ff:ff:ff:ff:ff
8: br0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 26:d2:80:7c:55:dd brd ff:ff:ff:ff:ff:ff
```

First we make sure the interface state is UP:
```
sudo ip link set enp0s29u1u1 up
```
To add the interface to bridge, the command to run is the following:

```
sudo ip link set enp0s29u1u1 master br0
```
To verify the interface was added to the bridge, instead:
```
sudo ip link show master br0
# expect
3: enp0s29u1u1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel master br0 state UP mode DEFAULT group default qlen 1000
    link/ether 18:a6:f7:0e:06:64 brd ff:ff:ff:ff:ff:ff
```

## Assigning a static IP address to the bridge
At this point we can assign a static IP address to the bridge. Let’s say we want to use 192.168.0.90/24; we would run:
```
sudo ip address add dev br0 192.168.0.90/24
```
To very that the address was added to the interface, we run:
```
ip addr show br0
# expect
9: br0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 26:d2:80:7c:55:dd brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.90/24 scope global br0
       valid_lft forever preferred_lft forever
   [...]
```

## Making the configuration persistent
Our bridge configuration is ready, however, as it is, it will not survive a machine reboot. To make our configuration persistent we must edit some configuration files, depending on the distribution we use.

Debian and derivatives
On the Debian family of distributions we must be sure that the bridge-utils package is installed:

```
sudo apt-get install bridge-utils
```
Once the package is installed, we should modify the content of the /etc/network/interfaces file:
```
sudo nano /etc/network/interfaces
```
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

# The loopback network interface
auto lo
iface lo inet loopback

# Specify that the physical interface that should be connected to the bridge
# should be configured manually, to avoid conflicts with NetworkManager
iface enp0s29u1u1 inet manual

# The br0 bridge settings
auto br0
iface br0 inet static
    bridge_ports enp0s29u1u1
        address 192.168.0.90
        broadcast 192.168.0.255
        netmask 255.255.255.0
        gateway 192.168.0.1



## Setup Bridge Networking with KVM on Ubuntu 20.04

run this command
```
sudo apt-get install qemu-kvm libvirt-daemon-system \
   libvirt-clients virtinst bridge-utils
```

Test that it’s working with virsh list --all this should show you an empty listing, but a listing none-the-less:
```
virsh list --all
```

For performance reasons, it is recommended to disable netfilter on bridges in the host. \
To do that, create a file called /etc/sysctl.d/bridge.conf
```
nano /etc/sysctl.d/bridge.conf
```
fill it in with this:
```
net.bridge.bridge-nf-call-ip6tables=0
net.bridge.bridge-nf-call-iptables=0
net.bridge.bridge-nf-call-arptables=
```
Then create a file called
```
nano /etc/udev/rules.d/99-bridge.rules
```

and add this line:

```
ACTION=="add", SUBSYSTEM=="module", KERNEL=="br_netfilter", \           RUN+="/sbin/sysctl -p /etc/sysctl.d/bridge.conf"
```

Next, we need to disable the default networking that KVM installed for itself. \
You can use ip to see what the default network looks like:
```
rkamradt@beast:~$ ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eno1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DEFAULT group default qlen 1000
    link/ether d4:be:d9:f3:1e:5f brd ff:ff:ff:ff:ff:ff
6: virbr0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 52:54:00:1d:5b:25 brd ff:ff:ff:ff:ff:ff
7: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc fq_codel master virbr0 state DOWN mode DEFAULT group default qlen 1000
    link/ether 52:54:00:1d:5b:25 brd ff:ff:ff:ff:ff:ff
```

The entries virbr0 and virbr0-nic are what KVM installs by default.

Because this host just had KVM installed, I don’t have to worry about existing VMs. \
If you have existing VMs, you will have to edit them to use the new network setup. \
It’s possible you can have both public and private VMs, but I’d just as soon have only one type of network per host to avoid confusion. \
Here’s how to remove the default KVM network:

```
virsh net-destroy default
virsh net-undefine default
```

Now you can run ip again and the virbr0 and virbr0-nic should be gone.
```
ip link
# expect
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eno1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DEFAULT group default qlen 1000
    link/ether d4:be:d9:f3:1e:5f brd ff:ff:ff:ff:ff:ff
```

If they are not, you can remove them with ip link delete virbr0 type brigde and ip link delete virbr0-nic.

Next, we will need to set up a bridge to use when we create a VM. 
Edit file 
```
nano /etc/netplan/00-installer-config.yaml
```
 (after making a back-up) to add a bridge. This is what mine looks like after editing:

```
network:
  ethernets:
    enp0s7:
      dhcp4: false
      dhcp6: false
  bridges:
    br0:
      interfaces: [ enp0s7 ]
      addresses: [192.168.0.104/24]
      gateway4: 192.168.0.1
      mtu: 1500
      nameservers:
        addresses: [8.8.8.8,8.8.4.4]
      parameters:
        stp: true
        forward-delay: 4
      dhcp4: no
      dhcp6: no
  version: 2
```

In my case enp0s7 is the name of my NIC, 192.168.0.104 is the IP address of my host, and192.168.0.1 is my home router. I’ve set up my home router to reserve 192.168.0.104 for this host by associating the MAC address to the IP address. The bridge br0 is attached to the enp0s7 interface, the physical network card on the host. Notice that the enp0s7 interface is not configured, the bridge now has the network configuration that used to be specified in the enp0s7 section. I’m not sure about the parameters section, I copied them straight from an example setup and I may want to play with them later.

Now run sudo netplan apply to apply your new configuration. You can use the ip command to inspect that it looks correct:


```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s7: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel master br0 state UP group default qlen 1000
    link/ether 00:1d:72:ac:d9:95 brd ff:ff:ff:ff:ff:ff
13: br0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 00:1d:72:ac:d9:95 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.104/24 brd 192.168.0.255 scope global br0
       valid_lft forever preferred_lft forever
    inet6 fe80::21d:72ff:feac:d995/64 scope link 
       valid_lft forever preferred_lft forever
```

Note that the br0 entry now has the IP address and the enp0s7 entry now has master br0 to show that it belongs to the bridge.

Now we can make KVM aware of this bridge. create a scratch XML file called host-bridge.xml 
```
nano host-bridge.xml
```
Insert the following:
```
<network>
  <name>host-bridge</name>
  <forward mode="bridge"/>
  <bridge name="br0"/>
</network>
```

Use the following commands to make that our default bridge for VMs:
```
virsh net-define host-bridge.xml
virsh net-start host-bridge
virsh net-autostart host-bridge
```

And then list the networks to confirm it is set to autostart:
```
virsh net-list --all
# expect
 Name          State    Autostart   Persistent
------------------------------------------------
 host-bridge   active   yes         yes
```

Now we have a bridge configured
















## Bibliography
https://linuxconfig.org/how-to-use-bridged-networking-with-libvirt-and-kvm
https://levelup.gitconnected.com/how-to-setup-bridge-networking-with-kvm-on-ubuntu-20-04-9c560b3e3991

https://github.com/jpfluger/examples/blob/master/ubuntu-14.04/kvm.md
https://computingforgeeks.com/how-to-create-and-configure-bridge-networking-for-kvm-in-linux
https://bobcares.com/blog/configure-network-bridge-for-kvm-virtual-machines/
