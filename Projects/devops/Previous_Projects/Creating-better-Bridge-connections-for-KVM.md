---
title: "Creating Better Bridge Connections for KVM on Centos 9 or Rocky Linux 9"
date: 2023-10-15T11:28:44Z
draft: false
---

There is a problem with the traditional way of doing this. \
If you are reading this you Probably have had problems trying to do this
.
## 1. This is a bad way to create a Bridge connections for KVM on a Centos or Rocky Linux server
Creating the Bridge br-My-Br01, its port and ens192
```
nmcli conn add type ovs-br conn.ens192 br-My-Br01 con-name br-My-Br01
```

Creating a Port
```
nmcli conn add type ovs-port conn.ens192 br-My-Br01 master br-My-Br01 con-name port-My-Port
```

Creating an Interface
```
nmcli conn add type ovs-interface slave-type ovs-port conn.ens192 br-My-Br01 master port-My-Port  con-name interface-My-If
```
Adding the Default Ehernet Connection to the Bridge
```
nmcli conn add type ovs-port conn.interface ens192 master br-My-Br01 con-name port-My-Port
```
**At this poit in time you have lost network connection on the server with a 60% failure rate**

Delete default Ethernet device
```
nmcli conn delete Public_ens192
```
**At this poit in time your network connection can brake**

Connecting Connections
```
nmcli conn mod br-My-Br01 connection.autoconnect yes
nmcli conn mod interface-My-If connection.autoconnect yes
nmcli conn mod ens192-eth connection.autoconnect yes
```

## 2. This is a better way to create Bridge connections for KVM on a Centos or Rocky Linux server
**Create the first bridge network (br-my-lan0) Kubernetes DEV**
```
sudo ip link add name br-my-lan0 type bridge
sudo ip addr add 10.154.2.1/24 dev br-my-lan0
sudo ip link set dev br-my-lan0 up
```

**Create the second bridge network (br-my-lan1) Docker DEV**
```
sudo ip link add name br-my-lan1 type bridge
sudo ip addr add 172.50.50.1/24 dev br-my-lan1
sudo ip link set dev br-my-lan1
```
**Verify that the Bridges are up**
```
ip a
ping 10.154.2.1
ping 172.50.50.1
```

**Persist the connections**
```
sudo vi /etc/sysconfig/network-scripts/ifcfg-br-my-lan0
```
Put this in the file
```
DEVICE=br-my-lan0
TYPE=Bridge
BOOTPROTO=static
IPADDR=10.154.2.1
NETMASK=255.255.255.0
ONBOOT=yes
```

**Also**
```
sudo vi /etc/sysconfig/network-scripts/ifcfg-br-my-lan1
```

Put this in the file
```
DEVICE=br-my-lan1
TYPE=Bridge
BOOTPROTO=static
IPADDR=172.50.50.1
NETMASK=255.255.255.0
ONBOOT=yes
```


Restart the NetworkManager
```
sudo systemctl restart NetworkManager
```

Verify nertwork 
```
ip a
```
