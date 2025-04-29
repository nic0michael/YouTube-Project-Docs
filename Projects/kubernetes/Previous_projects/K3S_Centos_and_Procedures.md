---
title: "K3S_Centos_and_Procedures"
date: 2023-03-27T08:36:40Z
draft: false
---

https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/security_guide/sec-using_firewalls \
https://www.thegeekdiary.com/5-useful-examples-of-firewall-cmd-command/ \
https://docs.ranchermanager.rancher.io/v2.5/getting-started/installation-and-upgrade/advanced-options/advanced-use-cases/open-ports-with-firewalld

```
sudo firewall-cmd --state
```

The command-line tool firewall-cmd is part of the firewalld application
```
yum install -y firewalld
dnf install -y firewalld

systemctl enable firewalld

systemctl restart firewalld
```

firewall commands
```

firewall-cmd --help

firewall-cmd --list-all-zones

firewall-cmd --list-services

firewall-cmd --list-ports
```
### To Enable all the incoming ports for a service
```
firewall-cmd --zone=public --add-service=http
```

### Allow traffic on an incoming port
### The command below will open the port 2222 effective immediately, but will not persist across reboots:
```
firewall-cmd --add-port=[YOUR PORT]/tcp

For example, to open TCP port 2222 :

firewall-cmd --add-port=2222/tcp

2379 and 6443

firewall-cmd --add-port=2379/tcp
firewall-cmd --add-port=6443/tcp
firewall-cmd --list-ports
```
___
For etcd nodes, run the following commands:
```
firewall-cmd --permanent --add-port=2376/tcp
firewall-cmd --permanent --add-port=2379/tcp
firewall-cmd --permanent --add-port=2380/tcp
firewall-cmd --permanent --add-port=8472/udp
firewall-cmd --permanent --add-port=9099/tcp
firewall-cmd --permanent --add-port=10250/tcp
```
For control plane nodes, run the following commands:
```
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --permanent --add-port=443/tcp
firewall-cmd --permanent --add-port=2376/tcp
firewall-cmd --permanent --add-port=6443/tcp
firewall-cmd --permanent --add-port=8472/udp
firewall-cmd --permanent --add-port=9099/tcp
firewall-cmd --permanent --add-port=10250/tcp
firewall-cmd --permanent --add-port=10254/tcp
firewall-cmd --permanent --add-port=30000-32767/tcp
firewall-cmd --permanent --add-port=30000-32767/udp
```
For worker nodes, run the following commands:
```
firewall-cmd --permanent --add-port=22/tcp
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --permanent --add-port=443/tcp
firewall-cmd --permanent --add-port=2376/tcp
firewall-cmd --permanent --add-port=8472/udp
firewall-cmd --permanent --add-port=9099/tcp
firewall-cmd --permanent --add-port=10250/tcp
firewall-cmd --permanent --add-port=10254/tcp
firewall-cmd --permanent --add-port=30000-32767/tcp
firewall-cmd --permanent --add-port=30000-32767/udp
```
___
```
firewall-cmd --permanent --add-port=22/tcp
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --permanent --add-port=443/tcp
firewall-cmd --permanent --add-port=2376/tcp
firewall-cmd --permanent --add-port=2379/tcp
firewall-cmd --permanent --add-port=2380/tcp
firewall-cmd --permanent --add-port=6443/tcp
firewall-cmd --permanent --add-port=8472/udp
firewall-cmd --permanent --add-port=9099/tcp
firewall-cmd --permanent --add-port=10250/tcp
firewall-cmd --permanent --add-port=10254/tcp
firewall-cmd --permanent --add-port=30000-32767/tcp
firewall-cmd --permanent --add-port=30000-32767/udp
```
___
```
firewall-cmd --permanent --add-port=2376/tcp
firewall-cmd --permanent --add-port=2379/tcp
firewall-cmd --permanent --add-port=2380/tcp
firewall-cmd --permanent --add-port=6443/tcp
firewall-cmd --permanent --add-port=8472/udp
firewall-cmd --permanent --add-port=9099/tcp
firewall-cmd --permanent --add-port=10250/tcp
firewall-cmd --permanent --add-port=10251/tcp
firewall-cmd --permanent --add-port=10252/tcp
firewall-cmd --permanent --add-port=10254/tcp


firewall-cmd --permanent --add-port=6443/tcp
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --permanent --add-port=9200/tcp
firewall-cmd --permanent --add-port=9300/tcp
firewall-cmd --permanent --add-port=7992/tcp
firewall-cmd --permanent --add-port=7993/tcp


sudo firewall-cmd --permanent --add-port=10255/tcp

firewall-cmd --reload

firewall-cmd --list-ports

this is correct

Node 1 server:
2379-2380/tcp 6443/tcp 10250/tcp 10251/tcp 10252/tcp

### kubernetes requirements
firewall-cmd --add-masquerade --permanent
firewall-cmd --reload
```
Set bridged packets to traverse iptables rules.
```
cat < /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
```
Then load the new rules.
```
sysctl --system
```
Disable all memory swaps to increase performance.
```
swapoff -a
```
================= more ==============
```
sudo firewall-cmd --permanent --add-port=6443/tcp
sudo firewall-cmd --permanent --add-port=2379-2380/tcp
sudo firewall-cmd --permanent --add-port=10250/tcp
sudo firewall-cmd --permanent --add-port=10251/tcp
sudo firewall-cmd --permanent --add-port=10252/tcp
sudo firewall-cmd --permanent --add-port=10255/tcp
sudo firewall-cmd –reload

You will also need to run the following commands on each worker node:

sudo firewall-cmd --permanent --add-port=10251/tcp
sudo firewall-cmd --permanent --add-port=10255/tcp
sudo firewall-cmd –reload
```
Update iptables config
```
We need to update the net.bridge.bridge-nf-call-iptables parameter in our sysctl file to ensure proper processing of packets across all machines. Use the following commands:
``
cat <<EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
```
check status
```
sudo sysctl --system
```
Disable swap
For Kubelet to work, we also need to disable swap on all of our VMs:
```
sudo sed -i '/swap/d' /etc/fstab
sudo swapoff -a
```
___
Step 3: Set Hostname on Nodes
To give a unique hostname to each of your nodes, use this command:
```
sudo hostnamectl set-hostname master-node

or

sudo hostnamectl set-hostname worker-node1
```
In this example, the master node is now named master-node, while a worker node is named worker-node1.

Make a host entry or DNS record to resolve the hostname for all nodes:
```
sudo vi /etc/hosts
``
With the entry:
```
192.168.1.10 master.phoenixnap.com master-node
192.168.1.20 node1. phoenixnap.com node1 worker-node
```
Step 4: Configure Firewall
The nodes, containers, and pods need to be able to communicate across the cluster to perform their functions. Firewalld is enabled in CentOS by default on the front-end. Add the following ports by entering the listed commands.

On the Master Node enter:
```
sudo firewall-cmd --permanent --add-port=6443/tcp
sudo firewall-cmd --permanent --add-port=2379-2380/tcp
sudo firewall-cmd --permanent --add-port=10250/tcp
sudo firewall-cmd --permanent --add-port=10251/tcp
sudo firewall-cmd --permanent --add-port=10252/tcp
sudo firewall-cmd --permanent --add-port=10255/tcp
sudo firewall-cmd --reload
``
Each time a port is added the system confirms with a ‘success’ message.

Adding ports to firewalld exceptions
Enter the following commands on each worker node:
```
sudo firewall-cmd --permanent --add-port=10251/tcp
sudo firewall-cmd --permanent --add-port=10255/tcp
firewall-cmd --reload
```
Step 5: Update Iptables Settings
Set the net.bridge.bridge-nf-call-iptables to '1' in your sysctl config file. This ensures that packets are properly processed by IP tables during filtering and port forwarding.
```
cat <<EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
```

```
sysctl --system
```
Step 6: Disable SELinux
The containers need to access the host filesystem. SELinux needs to be set to permissive mode, which effectively disables its security functions.

Use following commands to disable SELinux:
```
sudo setenforce 0
sudo sed -i ‘s/^SELINUX=enforcing$/SELINUX=permissive/’ /etc/selinux/config
```
Step 7: Disable SWAP
Lastly, we need to disable SWAP to enable the kubelet to work properly:
```
sudo sed -i '/swap/d' /etc/fstab
sudo swapoff -a
```
___
___

Step 1: Prepare Hostname, Firewall and SELinux
On your master node, set the hostname and if you don’t have a DNS server, then also update your /etc/hosts file.
```
hostnamectl set-hostname master-node
```
and:
```
cat <<EOF>> /etc/hosts
10.128.0.27 master-node
10.128.0.29 node-1 worker-node-1
10.128.0.30 node-2 worker-node-2
EOF
```
Set the following firewall rules on ports. Make sure that each firewall-cmd command, returns a success.
```
firewall-cmd --permanent --add-port=6443/tcp
firewall-cmd --permanent --add-port=2379-2380/tcp
firewall-cmd --permanent --add-port=10250/tcp
firewall-cmd --permanent --add-port=10251/tcp
firewall-cmd --permanent --add-port=10252/tcp
firewall-cmd --permanent --add-port=10255/tcp
firewall-cmd –reload

modprobe br_netfilter

echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
```
___
___

Step 1
First, you will need to set the hostname for your node. In this example, we are setting up the master node, referred to as master-node. These same steps will need to be repeated on any worker nodes you want to add to the Kubernetes cluster. To set the hostname, use the following command:

```
hostnamectl set-hostname master-node
```

Step 2
Next, modify your hosts file to ensure connectivity to the other nodes. 

```
cat <<EOF>> /etc/hosts
10.128.0.27 master-node
10.128.0.29 node-1 worker-node-1
10.128.0.30 node-2 worker-node-2
EOF
```
Step 3
To confirm that the hosts are set properly, you can perform a ping test on each node. 

```
ping 12.345.6.79
```
step 4
Next, you will need to disable SELinux:

```
setenforce 0
 sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
 reboot
```
Step 5
Then, you will need to update your Firewall Rules to allow traffic between nodes with the following series of commands:
```
firewall-cmd --permanent --add-port=6443/tcp
firewall-cmd --permanent --add-port=2379-2380/tcp
firewall-cmd --permanent --add-port=10250/tcp
firewall-cmd --permanent --add-port=10251/tcp
firewall-cmd --permanent --add-port=10252/tcp
firewall-cmd --permanent --add-port=10255/tcp

firewall-cmd –reload
# modprobe br_netfilter
```

```
echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
```
Initializing Kubernetes Master and Setup Default User
Step 1
First, disable swap using the following command:
```
swapoff -a
```
Step 2
Then initialize Kubernetes Master with the following command:
```
kubeadm init
```
___
___

The different ports which are used for access and communication are:
```
6443
2379-2380
10250-10252
```
PREREQUISITES FOR MASTER AND WORKER NODES

1. The minimum requirements for the Kubernetes server and for master and worker nodes are 2GB and CPUs. Kubernetes cannot be installed until these minimum requirements are met.
2. Before installing anything you need to update the servers using:
```
	dnf -y upgrade
```
3. Disable SELinux enforcement.
```
	setenforce 0
	sed - i--follow - symlinks 's/SELINUX=enforcing/SELINUX=disabled/g'
	etc / sysconfig / selinux
```
4. The IP masquerade at the firewall has to be enabled using:
```
	1 firewall - cmd--add - masquerade--permanent
	2 firewall - cmd--reloa
```

5. To traverse iptables rules we need to set bridged packets.
```
	cat < /etc/sysctl.d / k8s.conf
	net.bridge.bridge - nf - call - ip6tables = 1
	net.bridge.bridge - nf - call - iptables = 1
	EOF
```
6.Then we need to load new rules.
```
	sysctl --system
```
7.To increase performance, disable all the memory swaps.
```
	swapoff -a
```
CONFIGURING KUBERNETES ON MASTER NODE
After its installation we will configure it to form a cluster.

1. Configure kubeadm.
```
	kubeadm config images pull
```
2. Open the ports which are used by Kubernetes.
```
	firewall-cmd --zone=public --permanent --add-port={6443,2379,2380,10250,10251,10252}/tcp
```
3. Replace the worker-IP-address to allow Docker access from another node.
```
	firewall-cmd -zon=public --permanent -add-rich-rule 'rule family=ipv4 source address=worker-IP-address/32 accept'
```
4. Mark the changes made and reload.
```
	firewall-cmd --reload
```
5. Install the plugin Container Network Interface (CNI) for Kubernetes.
```
	kubeadm init --pod-network-cidr 192.168.0.0/1
```
6. Now make the configuration files and directory.
```
	mkdir - p $HOME / .kube
	cp - i / etc / kubernetes / admin.conf $HOME / .kube / config
	chown $(id - u): $(id - g) $HOME / .kube / config
	kubectl apply - f https: //docs.projectcalico.org/manifests/calico.yaml
```
7. Check that the master node is enabled and is running.
```
	kubectl get nodes
```
CONFIGURING KUBERNETES ON WORKER NODE
1. Open all the ports used by Kubernetes.
```
	firewall-cmd --zone=public --permanent --add-port={10250,30000-32767}/tcp
```
2. Mark the changes made and reload.
```
	firewall-cmd --reload
```
3. Now we need to join a cluster using the token we previously noted.
```
	kubeadm join 94.237 .41 .193: 6443--token 4 xrp9o.v345aic7zc1bj8ba\
	--discovery - token - ca - cert - hash sha256: b2e459930f030787654489ba7ccbc701c29b3b60e0aa4998706fe0052de8794c
```
4. Check if the worker node has joined or not.
Go to the Master node and issue the below command.

	kubectl get nodes
___
___
## Install Kubernetes using k3s on CentOS 9|RHEL 9|AlmaLinux 9

https://technixleo.com/install-kubernetes-k3s-on-centos-rhel-alma/

### Install k3s on CentOS 9|AlmaLinux 9|RHEL 9
Update your system packages.
```
	sudo dnf update -y
```
Update the crypto policies and set them to LEGACY to ensure compatibility while installing k3s on CentOS 9|AlmaLinux 9|RHEL 9.
```
	sudo update-crypto-policies --set LEGACY
```
### Install Master Node
You can use the following command to install the latest version.
```
	curl -sfL https://get.k3s.io/ | sh -s - --write-kubeconfig-mode 644
```
This command installs additional utilities including kubectl and uninstallation script for k3s.

You can check the version to confirm successful installation
```
	k3s --version
```
Check the status of the service.
```
	sudo systemctl status k3s
```
It is recommended to turn off firewalld
```
	sudo systemctl disable firewalld --now
```
Check to see if your master node is running.
```
	kubectl get nodes
```
Use this command to get the token that we will use to install k3s on the worker node.
```
	sudo cat /var/lib/rancher/k3s/server/node-token
```
### Install Worker Node
Use the following command syntax to install. K3S_URL is used to make the K3s run in the worker node. The KRS_TOKEN is stored at /var/lib/rancher/k3s/server/node-token on your server node.
```
	curl -sfL https://get.k3s.io | K3S_URL=https://[master-host]:6443 K3S_TOKEN=mynodetoken sh -
```
So for my installation script, I will define them as shown below.
```
	curl -sfL https://get.k3s.io | K3S_URL=https://192.168.200.41:6443 K3S_TOKEN="1590974bb49259c934751d2defdd94bc" sh -
``
You can also define the fields as shown below and use a shorter installation script.
``
	k3s_url="https://192.168.200.41:6443"
	k3s_token="1590974bb49259c934751d2defdd94bc"
	curl -sfL https://get.k3s.io | K3S_URL=${k3s_url} K3S_TOKEN=${k3s_token} sh -
```
Start and enable the service on boot.
```
	sudo systemctl start k3s-agent
```
Verify installation
```
	sudo systemctl status k3s-agent
```
### Verify Cluster Status
Check the cluster status using the master node.
```
	kubectl config get-cluster

	kubectl get nodes

	kubectl cluster-info

	kubectl get namespaces

	kubectl get endpoints -n kube-system

	kubectl get pods -n kube-system
```

### Enable Addons
```
	curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3

	chmod 700 get_helm.sh

	./get_helm.sh
```
You can also download the latest version from Github, the unpack it using the following command
```
	tar -zxvf helm-*-linux-amd64.tar.gz
```
Move the helm binary files to a directory on PATH.
```
	sudo mv linux-amd64/helm /usr/local/bin/helm
```
Add the Helm Chart repository to install applications using Helm
```
	helm repo add stable https://charts.helm.sh/stable
	helm repo update
```
With Helm installed, you can search, find and install Kubernetes packages. You can browse all packages at the Artifact Hub or you can also use the following command to list packages.
```
	helm search hub <package name>
```
Example of PostgreSQL package. This lists all repositories available to install PostgreSQL.
```
	helm search hub postgresql
```
You can add the repository of the maintainer you wish to use.
```
	helm repo add bitnami https://charts.bitnami.com/bitnami
```
You can list the charts available to install.
```
	helm search repo bitnami
```
To install it, use the following commands. These commands for installation can also be found at the Artifact Hub. The below command installs the package named postgresql.
```
	helm install postgresql bitnami/postgresql
```
List all releases using the following command.
```
	helm list
```
To uninstall a chart, use the following command syntax.
```
	helm delete postgresql
```
To delete PersistenctVolumeClaim (PVC) of the package, use the following command. This will however delete al the PostgreSQL data.
```
	kubectl delete pvc -l release=postgresql
```

### Deploy an Application on k3s

There are a number of ways to deploy services outside a cluster.

* NodePort
* Cluster IP
* Load Balancer
* Ingress Controller

We are going to use the Nginx Ingress controller. We can install Nginx web-proxy using Helm
```
	helm install nginx-ingress stable/nginx-ingress --namespace kube-system --set defaultBackend.enabled=false
```
Check the status of Ingress controller installed.
```
	kubectl --namespace kube-system get services -o wide -w nginx-ingress-controller
```
Check to verify the application has been installed successfully.
```
	kubectl get pods -n kube-system -l app=nginx-ingress -o wide
```
Check if the services have been deployed.
```
	kubectl get service

	kubectl get pods -o wide

	kubectl get svc -o wide
```
### Uninstall k3s
To uninstall k3s on the master node, use the uninstall script as shown below.
```
	/usr/local/bin/k3s-uninstall.sh
```
To uninstall k3s on the worker node, use the following command.
```
	/usr/local/bin/k3s-agent-uninstall.sh
```
Remove configuration files manually by using the following command on both the master and worker node.
```
	sudo rm -rf /var/lib/rancher/
```
___
___
## How To Run Kubernetes Cluster on Rocky Linux 8 using k3s
https://techviewleo.com/run-kubernetes-cluster-on-rocky-linux-using-k3s/

### K3s installation on Rocky Linux 8 Requirements
* K3s supported architectures: x86_64, ARMv7, ARM 64
* Your nodes must not have the same hostname.
* k3s supports most modern Linux Systems. Check specific requirements for some linux distros here
* Minimum RAM of 1GB ,with preffered being 512MB.
* CPU : 1 minimum.
* Disks : preferred SSD disk.
* Port 6443 must be accessible by all nodes.
* A database : PostgreSQL, MySQL, etcd

### Install k3s on Rocky Linux 8 server(s)

K3s can be installed in the system as a systemd script, from a binary file, or using a configuration file.

For the purpose of this article, I will use Rocky Linux 8 Server as the Master node (192.168.201.2) with Fedora server (192.168.201.3) and Debian Server (192.168.201.4) as the worker nodes respectively.
Define this in the /etc/hosts file of each server.
```
	sudo vim /etc/hosts
```

```
192.168.201.2 k3s-master
192.168.201.3 k3s-worker02   ## Fedora Server ##
192.168.201.4 k3s-worker01   ## Debian SErver ##
```

### Install k3s on the master node
To install k3s on the master node visit the k3s website and copy the script. Then carry the steps below.

#### Step 1 : Update your system.
This is the best practice before any installation.
```
sudo dnf update -y
```
Then confirm your system architecture to ensure its either x86_64, ARMv7, or ARM 64
```
uname -a
```
#### Step 2 : Run the Script to install k3s on the master node
To install k3s on the master node run the script:
```
curl -sfL https://get.k3s.io | sh -
```

From the output, other important utilities were installed:

* kubectl
* crictl
* k3s-killall.sh
* k3s-uninstall.sh

Since this is a systemd script install, start and enable the service:
```
sudo systemctl start k3s
sudo systemctl enable k3s
sudo systemctl status k3s
```

To check the k3s version installed.
```
k3s --version
```

To see the Kubernetes version installed:
```
kubectl version
```
The kubectl version command might throw an error “Unable to read /etc/rancher/k3s/k3s.yaml, please start the server with –write-kubeconfig-mode to modify kube config permissions“

To work around it, re-run your script as below:
```
curl -sfL https://get.k3s.io | sh -s - --write-kubeconfig-mode 644
```
You have successfully installed k3s on the master node using the systemd script install.

To list your nodes:
```
kubectl get nodes
```

### Install k3s on worker nodes
To install k3s on worker nodes and add them to your cluster, run the installation script with the K3S_URL and K3S_TOKEN environment variables.

The K3S_TOKENcan be accessed on /var/lib/rancher/k3s/server/node-token on the control-plane,master node server.
```
sudo cat /var/lib/rancher/k3s/server/node-token
```
you get :
```
K108a2420739e1fcfeae17fd4c7887daf9995631ff69b90cf5547ab174659cf2e80::server:b6fd4541317104cc20e931bd344105eb
```

To install k3s on the worker node :
```
curl -sfL https://get.k3s.io | K3S_URL=https://<SERVER_IP-ADDR>:6443 K3S_TOKEN=<TOKEN FROM MASTER NODE> sh -
```

To install k3s on the Fedora and Ubuntu servers from the master node.
```
curl -sfL https://get.k3s.io | K3S_URL=https://192.168.201.2:6443 K3S_TOKEN=K108a2420739e1fcfeae17fd4c7887daf9995631ff69b90cf5547ab174659cf2e80::server:b6fd4541317104cc20e931bd344105eb sh -
```

The next worker node is my Fedora server 192.168.201.3
```
curl -sfL https://get.k3s.io | K3S_URL=https://192.168.201.2:6443 K3S_TOKEN=K108a2420739e1fcfeae17fd4c7887daf9995631ff69b90cf5547ab174659cf2e80::server:b6fd4541317104cc20e931bd344105eb sh -
```

Once installation of k3s on worker nodes is finished, login to the master node and check the status.

```
k3s kubectl config get-clusters

k3s kubectl cluster-info

k3s kubectl get namespaces

k3s kubectl get endpoints -n kube-system

k3s kubectl get pods -n kube-system

```
To see running containers from the worker node ###
```
sudo crictl ps
```

### How To Uninstall K3s on Rocky Linux 8
nstallation of k3s via installation script creates a script to uninstall k3s from your system.

To uninstall k3s from the server/master node:

```
sudo /usr/local/bin/k3s-uninstall.sh
```

To uninstall k3s from the agent/worker node:

```
sudo /usr/local/bin/k3s-agent-uninstall.sh
```
___
___
## Setup k3s on CentOS 7
https://www.orthogonal.info/2022/04/03/setup-k3s-on-centos-7/

### prerequisite
To change ip and hostname
```
vi /etc/sysconfig/network-scripts/ifc
vi /etc/hostname
```

To optional disable firewall
```
systemctl disable firewalld --now
```

### Install K3s
To setup master
```
curl -sfL https://get.k3s.io | K3S_KUBECONFIG_MODE="644" sh -s
```

To get token
```
sudo cat /var/lib/rancher/k3s/server/node-token
```


To setup worker
```
curl -sfL https://get.k3s.io | K3S_URL="https://192.168.0.29:6443" K3S_TOKEN="<token>" K3S_NODE_NAME="<node name>" sh -
```
___
___
## How to Deploy K3s on Linode UBUNTU
https://www.linode.com/docs/guides/how-to-deploy-k3s-on-linode/


