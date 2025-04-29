---
title: "K3S Centos Stream 8 Cluster Creation"
date: 2023-04-03T11:39:04Z
draft: false
---

```
sudo su - nickm
yum install nano
```
## Always Login to the Node servers from your workstation with non root ID
```
ssh nickm@10.154.2.90
sudo su -

# and 

ssh nickm@10.154.2.91
sudo su -
```
## SSH to Master server from Worker server
```
ssh root@10.154.2.90
exit

```

## Set up root Passwordless login from the Worker servers to the Master server
```
sudo su -
ssh-keygen -t rsa -b 4096

ssh-copy-id root@10.154.2.90

ssh root@10.154.2.90
```

## Disable firewall on all NODE servers
```
sudo systemctl status firewalld

sudo systemctl disable firewalld

sudo systemctl status firewalld
```
expect to get
```
[root@master ~]# sudo systemctl disable firewalld
Removed /etc/systemd/system/multi-user.target.wants/firewalld.service.
Removed /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.
```

## To create the primary Master
```
node_ip="10.154.2.90"
node_name="mossie.loseyourip.com"

curl -sfL https://get.k3s.io | K3S_NODE_IP=${node_ip} K3S_NODE_NAME=${node_name} sh -s - server --cluster-init

cat /var/lib/rancher/k3s/server/node-token
```
expect to get
```
K102d6dc735f9d1a041724826cefb8847d91ab068eb817de42aeb4e90c60e0abc4b::server:f845327ef43001d7383c4f811f3c503b
```

## To Create worker nodes
```
k3s_token="K102d6dc735f9d1a041724826cefb8847d91ab068eb817de42aeb4e90c60e0abc4b::server:f845327ef43001d7383c4f811f3c503b"
k3s_url="https://10.154.2.90:6443"
node_ip="10.154.2.91"
node_name="Wildebeest.loseyourip.com"

curl -sfL https://get.k3s.io | K3S_URL=${k3s_url} K3S_TOKEN=${k3s_token} K3S_NODE_IP=${node_ip} K3S_NODE_NAME=${node_name} sh -
```

## From the master server check nodes
```
kubectl get nodes
```

expect to get
```
kubectl get nodes
NAME                        STATUS   ROLES                       AGE     VERSION
mossie.loseyourip.com       Ready    control-plane,etcd,master   8m45s   v1.26.3+k3s1
wildebeest.loseyourip.com   Ready    <none>                      18s     v1.26.3+k3s1
```
