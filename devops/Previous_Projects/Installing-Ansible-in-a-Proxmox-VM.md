---
title: "Installing Ansible in a Proxmox VM"
date: 2023-12-30T06:21:48Z
draft: false
---
This procedure is for installing Ansible on a Debian/Ubuntu VM
## Step 1: Configure Ansible Managed Hosts
create a new user named ansible and grant it superuser privileges.
```

# DONT DO THIS using the -m option to create a default home directory /home/ansible for it:
# DONT USE THIS COMMAND useradd -m ansible

# BETTER OPTION create the ansible user this way
adduser ansible

# NOT NEEDED NOW: Create a password for this new user.
# NOT NEEDED: passwd ansible

# Create a new file named ansible in the /etc/sudoers.d/ directory and add a single configuration line to it:
echo "ansible ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers.d/ansible

# Test ansible user
su - ansible
sudo ls -la
```
To Generate a SSH public/private keypair
```
ssh-keygen -t rsa -b 4096
# Just respond by pressing the enter Key for all promptsssh-keygen -t rsa -b 4096
```
If you have generated an SSH public/private keypair on your control node and have an ssh-agent daemon running, you may install SSH public key on your managed node by using the ssh-copy-id command:
```
ssh-copy-id ansible@[your_Node_VM_ip_address]
ssh-copy-id ansible@10.154.2.80
ssh-copy-id ansible@10.154.2.81
ssh-copy-id ansible@10.154.2.83

# now test SSH connection to your managed host (NodeVM):

ssh ansible@10.154.2.80
exit
```
### PLEASE NOTE you also need to do this for the root user to be able to ssh to the ansible users on the VMs
```
sudo su -
ssh-keygen -t rsa -b 4096
ssh-copy-id ansible@10.154.2.80
ssh-copy-id ansible@10.154.2.81
ssh-copy-id ansible@10.154.2.83

# Now test connections to these vM servers
ansible all_active_nodes -m ping
```

## Step 2: Install Ansible on Control Node (we will use this also as the SemaphoreVM in the next lesson) 10.154.2.95
The first step that we need to do is to add an official Ansible repository to our Ubuntu package manager to make sure you are installing from an up-to-date repository.
```
ssh nico@10.154.2.95
sudo su -
sudo apt-add-repository ppa:ansible/ansible
```

Now update your package lists and install Ansible:
```
sudo apt update
```
And install Ansible
```
sudo apt install -y ansible
```
## Step 3: Add Managed Nodes to Ansible Inventory
After Ansible is installed on a control node, it is time to add managed nodes to the Ansible inventory. \
Edit the hosts file.
```
sudo mkdir /etc/ansible
sudo nano /etc/ansible/hosts
sudo vi /etc/ansible/hosts
```

Put something like this in your file:
```
# This is the default ansible 'hosts' file.

[primary_master_node]
hippo.kozow.com ansible_host=10.154.2.80

[other_active_nodes]
giraffe.kozow.com ansible_host=10.154.2.81
zebra.kozow.com ansible_host=10.154.2.83

[all_active_nodes]
hippo.kozow.com ansible_host=10.154.2.80
giraffe.kozow.com ansible_host=10.154.2.81
zebra.kozow.com ansible_host=10.154.2.83


[other_master_nodes]
giraffe.kozow.com ansible_host=10.154.2.81
#impala.kozow.com  ansible_host=10.154.2.82

[worker_nodes]
zebra.kozow.com ansible_host=10.154.2.83
#tiger.loseyourip.com ansible_host=10.154.2.91
#kudu.loseyourip.com ansible_host=10.154.2.93

# Global variables
[all:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_user=ansible
```

Here we define two host groups – [deb_based] and [rhel_based] – so we could target them separately. \
We also assign individual aliases to our managed hosts – ubuntu, debian, centos – and use ansible_host variable to setup their public IP addresses.

We also setup global variables under the [all:vars] section. \
Here we point to the python3 executable that is stored at /usr/bin/python3 when using Ubuntu 20.04 as our control node. \
As we have created a new user named ansible on each of our managed nodes, we also setup it under the ansible_user variable.

### verify this
To make sure our inventory list syntax is correct, and our managed nodes are added successfully we use ansible-inventory command with the -y flag to format our output in YAML:
```
ansible-inventory --list -y
```

Expect to get :
```
all:
  children:
    all_active_nodes:
      hosts:
        giraffe.kozow.com:
          ansible_host: 10.154.2.81
          ansible_python_interpreter: /usr/bin/python3
          ansible_user: ansible
        hippo.kozow.com:
          ansible_host: 10.154.2.80
          ansible_python_interpreter: /usr/bin/python3
          ansible_user: ansible
        impala.kozow.com:
          ansible_host: 10.154.2.82
          ansible_python_interpreter: /usr/bin/python3
          ansible_user: ansible
    other_active_nodes:
      hosts:
        giraffe.kozow.com: {}
        zebra.kozow.com:
          ansible_host: 10.154.2.83
          ansible_python_interpreter: /usr/bin/python3
          ansible_user: ansible
    other_master_nodes:
      hosts:
        impala.kozow.com: {}
    primary_master_node:
      hosts:
        hippo.kozow.com: {}
    ungrouped: {}
    worker_nodes:
      hosts:
        kudu.loseyourip.com:
          ansible_host: 10.154.2.93
          ansible_python_interpreter: /usr/bin/python3
          ansible_user: ansible
        tiger.loseyourip.com:
          ansible_host: 10.154.2.91
          ansible_python_interpreter: /usr/bin/python3
          ansible_user: ansible
```

## Step 4: Start Using Ansible
At this point we have everything ready to start working with Ansible. \
It’s always a good idea to test connection to our managed nodes first, so let’s do that. \
We use ansible command that targets all managed hosts with -m option that tells Ansible to use a module and ping for the module name:
```
ansible all -m ping
```

expect to get:
```
iraffe.kozow.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
hippo.kozow.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
zebra.kozow.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
tiger.loseyourip.com | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: ssh: connect to host 10.154.2.91 port 22: No route to host",
    "unreachable": true
}
impala.kozow.com | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: ssh: connect to host 10.154.2.82 port 22: No route to host",
    "unreachable": true
}
kudu.loseyourip.com | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: ssh: connect to host 10.154.2.93 port 22: No route to host",
    "unreachable": true
}

```
Ansible ping module connects to the host using SSH, verifies whether there is a usable python installation and returns a pong response. \
As you may see, we were able to successfully connect to all managed hosts.

We may also target distinct host groups if we want. Instead of using all we should use the name of a host group, for instance:
```
ansible all_active_nodes -m ping
```

expect to get:
```

giraffe.kozow.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
hippo.kozow.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
impala.kozow.com | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: ssh: connect to host 10.154.2.82 port 22: No route to host",
    "unreachable": true
}

```

## Step 5: Write and Execute Your First Ansible Playbook
To save time we have creates aproject in github which we will pull down and use
```
sudo su -

sudo apt install nano -y

mkdir /opt/semaphore

cd /opt/semaphore

git clone https://github.com/nic0michael/Create-K3S-Cluster-Ansible-Playbook.git

ls -la 

```
You will now have this folder:
```
Create-K3S-Cluster-Ansible-Playbook
```
### To Edit our Inventory file
```
nano /opt/semaphore/Create-K3S-Cluster-Ansible-Playbook/inventories/production/inventory.ini
```
You should have:
```
# This is the default ansible 'hosts' file.

[primary_master_node]
hippo.kozow.com ansible_host=10.154.2.80

[other_active_nodes]
giraffe.kozow.com ansible_host=10.154.2.81
zebra.kozow.com ansible_host=10.154.2.83

[all_active_nodes]
hippo.kozow.com ansible_host=10.154.2.80
giraffe.kozow.com ansible_host=10.154.2.81
zebra.kozow.com ansible_host=10.154.2.83


[other_master_nodes]
giraffe.kozow.com ansible_host=10.154.2.81
#impala.kozow.com  ansible_host=10.154.2.82

[worker_nodes]
zebra.kozow.com ansible_host=10.154.2.83
#tiger.loseyourip.com ansible_host=10.154.2.91
#kudu.loseyourip.com ansible_host=10.154.2.93

# Global variables
[all:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_user=ansible
```

You need to change the names of the VMs and their IP addresses in the [all_active_nodes] section to be those of your VMs \
If you don't have server names like : hippo.kozow.com set up On the Internet in a DNS Server use names like hippo anmd bnuffalo instead

### Update existing Inventory
Run these commands:
```
cp /opt/semaphore/Create-K3S-Cluster-Ansible-Playbook/inventories/production/inventory.ini /etc/ansible/hosts

cat /etc/ansible/hosts
```

### To Edit our playbook

```
nano /opt/semaphore/Create-K3S-Cluster-Ansible-Playbook/playbooks/update_all_k3s_node_servers.yml

```
You should have:
```
# playbooks/update_all_k3s_node_servers.yml

---
- hosts: all_active_nodes
  remote_user: root
  become: true

  tasks:
    - name: APT Package Updater
      apt:
        upgrade: yes
        update_cache: yes

```


### 6.1 Running the playbook
This playbook with update all the servers in the [all_active_hosts] category \
**Before running this playbook we recoment that you open these VMs in Proxmox by opening them in the Console running these commands:**
```
sudo apt update
sudo apt upgrade
```
**Now we launch the playbooks/run_other_master.yml playbook**
 
```
sudo su -

ansible-playbook -i /opt/semaphore/Create-K3S-Cluster-Ansible-Playbook/inventories/production/inventory.ini /opt/semaphore/Create-K3S-Cluster-Ansible-Playbook/playbooks/update_all_k3s_node_servers.yml -b

```
Expect to get:
```
PLAY [all_active_nodes] ************************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************
ok: [giraffe.kozow.com]
ok: [zebra.kozow.com]
ok: [hippo.kozow.com]

TASK [APT Package Updater] *********************************************************************************************************************************************************
ok: [zebra.kozow.com]
ok: [hippo.kozow.com]
ok: [giraffe.kozow.com]

PLAY RECAP *************************************************************************************************************************************************************************
giraffe.kozow.com          : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
hippo.kozow.com            : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
zebra.kozow.com            : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```

**Since we have already created the inventory file: /etc/ansible/hosts we can run this command with less parameters**
```
ansible-playbook /opt/semaphore/Create-K3S-Cluster-Ansible-Playbook/playbooks/update_all>

```


## Bibliograpgy
https://www.cherryservers.com/blog/how-to-install-and-configure-ansible-on-ubuntu-20-04

https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-ansible-on-ubuntu-20-04

https://www.tutorialspoint.com/how-to-install-and-configure-ansible-on-ubuntu-20-04

https://www.linuxtechi.com/how-to-install-ansible-on-ubuntu/
