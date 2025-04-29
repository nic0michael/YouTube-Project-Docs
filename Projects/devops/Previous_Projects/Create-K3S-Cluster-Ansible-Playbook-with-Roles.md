---
title: "Create K3S Cluster Ansible Playbook With Roles"
date: 2024-01-10T11:43:16Z
draft: false
---

## 1. Clone project in required folder
Run these commands
```
# SSH to your Ansible Server VM
ssh nickm@10.154.2.95

# Become root
sudo su -

# Create k3s folder
mkdir /opt/k3s

cd /opt/k3s

# Clone this project from Github
git clone https://github.com/nic0michael/create_k3s_cluster_playbook_generator_with_ansible_roles.git

# Open this project in terminal
ls -la
cd create_k3s_cluster_playbook_generator_with_ansible_roles/
```
### 1.1 Git / GitHub / GitLab / BitBucket Requirements for this project
After Cloning our project you need to delete the .git folder and create your own Git project on your Git Repository server. \
This is a FOS Open Source project so you wont have issues doing this. \
However we would like you to keep the readme.md file as it will provide whoever uses your project with instructions and even a link to our video.

```
# Step 1 Delete the .git folder
rm -rf .git

# Step 2 Generate a  new local git repository
git init

# Step 3 Add the content to your local git repository
git add .

# Step 4 Commit the changes to your local git repository
git commit -m "initial commit"
```
### 1.2 Create a new Git project on your Git / GitHub / GitLab / BitBucket Server
The instructions for doing this will depent on which type of Git Server you are using. \
This should also include the instruction for pushing the local Git repository to that server.
 
## 2. Project structure
```
# List Structure of this project
tree
```
Expect to get:
```
├── Readme.md
├── files
│   ├── sudoer_nickm
│   └── sudoer_nico
├── inventory
│   └── production
│       ├── OurServers.txt
│       ├── inventory.ini
│       └── readme.md
├── playbooks
│   ├── create_k3s_scripts
│   │   ├── OLD_FILES
│   │   │   ├── create_k3s_other_master_node-OLD.yml
│   │   │   ├── create_k3s_primary_master_node-OLD.yml
│   │   │   └── create_k3s_worker_node-OLD.yml
│   │   ├── create_k3s_other_master_nodes.yml
│   │   ├── create_k3s_primary_master_node.yml
│   │   ├── create_k3s_worker_nodes.yml
│   │   ├── destroy_k3s_other_master_nodes.yml
│   │   ├── destroy_k3s_primary_master_node.yml
│   │   ├── destroy_k3s_worker_nodes.yml
│   │   └── make_all_k3s_scripts.yml
│   ├── run_k3s_scripts
│   │   ├── start_other_master_node_in_k3s_cluster.txt
│   │   ├── start_other_master_node_in_k3s_cluster.yml
│   │   ├── start_primmary_master_node.md
│   │   ├── start_worker_node_in_k3s_cluster.txt
│   │   ├── start_worker_node_in_k3s_cluster.yml
│   │   ├── terminate_other_master_nodes.yml
│   │   ├── terminate_primary_master_node.yml
│   │   └── terminate_worker_nodes.yml
│   └── server_admin
│       ├── make_sudoers_on_all_servers.yml
│       ├── update_all_servers.yml
│       └── user_credentials.yml
├── roles WE WILL IGNORE THIS FOLDER FOR NOW
├── templates
│   ├── destroy_k3s_node.j2
│   └── k3s_node_maker.j2
└── vars
    ├── other_master_node_variables.yml
    ├── primary_master_node_variables.yml
    └── worker_node_variables.yml
```
### 2.1 The inventory
You will find the inventory in two places:


#### 2.1.1 In the project
```
cat inventory/production/inventory.ini
```
Expect to get
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

[second_master_node]
giraffe.kozow.com ansible_host=10.154.2.81

[first_worker_node]
zebra.kozow.com ansible_host=10.154.2.83

[all_active_machines]
upupa.loseyourip.com ansible_host=10.154.2.89
leopard.loseyourip.com ansible_host=10.154.2.95
hippo.kozow.com ansible_host=10.154.2.80
giraffe.kozow.com ansible_host=10.154.2.81
zebra.kozow.com ansible_host=10.154.2.83

# Global variables
[all:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_user=ansible

```
#### 2.1.2 On the server create default inventory
Create the Default Inventory
```
# in the default inventory
nano /etc/ansible/ansible.cfg
```
Put the content of previous inventory file in this file

## 3. Create all the needed shell scripts on the Kubernetes K3S Cluster Node Servers
Run these commands
```
cd /opt/k3s/create_k3s_cluster_playbook_generator_with_ansible_roles/playbooks/create_k3s_scripts

# List playbooks
ls -la

# Run the make_all_k3s_scripts.yml playbook 
ansible-playbook make_all_k3s_scripts.yml

# Run the same command in Verbose mode for fault finding to fix network issues
ansible-playbook -vvv  make_all_k3s_scripts.yml

```
This will create all the shell scripts on all the node servers for creating the Kubernetes K3S High Availability Cluster \
Expect to get:
```
PLAY [1. Create Primary Master Node] ***********************************************************************************************************************************************

TASK [1.1 Display current server name] *********************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Current server name is localhost"
}

TASK [1.2 Display current server IP] ***********************************************************************************************************************************************
ok: [localhost] => {
    "msg": "IP address of the current server is 127.0.0.1"
}

TASK [1.3 Create more variables] ***************************************************************************************************************************************************
ok: [localhost]

TASK [1.4 Execute k3s_node_maker.j2 template for 1ST_MASTER_NODE - /opt/k3s/primary_master_node.sh] ********************************************************************************
ok: [localhost]

TASK [1.5 Change file permissions of other_master_node.sh - /opt/k3s/primary_master_node.sh] ***************************************************************************************
ok: [localhost]

TASK [1.6. SCP Copy file  /opt/k3s/primary_master_node.sh to nickm@10.154.2.81:/opt/k3s/] ******************************************************************************************
changed: [localhost]

PLAY [2. Create Other Master Nodes] ************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************
ok: [giraffe.kozow.com]

TASK [2.1 Display current server name] *********************************************************************************************************************************************
ok: [giraffe.kozow.com] => {
    "msg": "Current server name is giraffe.kozow.com"
}

TASK [2.2 Create variables] ********************************************************************************************************************************************************
ok: [giraffe.kozow.com]

TASK [2.3 Show variable names and values] ******************************************************************************************************************************************
ok: [giraffe.kozow.com] => {
    "msg": "node_name giraffe.kozow.com node_ip 10.154.2.81 master_node_ip_address 10.154.2.81 "
}

TASK [2.4 Execute k3s_node_maker.j2 template for SECOND_MASTER_NODE] ***************************************************************************************************************
ok: [giraffe.kozow.com]

TASK [2.5 Change file permissions to 775 for create_k3s_master_node.sh] ************************************************************************************************************
ok: [giraffe.kozow.com]

PLAY [3. Create Worker Nodes] ******************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************
ok: [zebra.kozow.com]

TASK [3.1 Display current server name] *********************************************************************************************************************************************
ok: [zebra.kozow.com] => {
    "msg": "Current server name is zebra.kozow.com"
}

TASK [3.2 Create variables] ********************************************************************************************************************************************************
ok: [zebra.kozow.com]

TASK [3.3 Show variable names and values] ******************************************************************************************************************************************
ok: [zebra.kozow.com] => {
    "msg": "node_name zebra.kozow.com node_ip 10.154.2.83 master_node_ip_address 10.154.2.83 "
}

TASK [3.4 Execute k3s_node_maker.j2 template for SECOND_MASTER_NODE] ***************************************************************************************************************
ok: [zebra.kozow.com]

TASK [3.5 Change file permissions to 775 for create_k3s_second_master_node.sh] *****************************************************************************************************
ok: [zebra.kozow.com]

PLAY [5. Destroy Primary Master Node] **********************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************
ok: [hippo.kozow.com]

TASK [5.1 Execute K3s Node Destruction] ********************************************************************************************************************************************
ok: [hippo.kozow.com]

TASK [5.2 Change file permissions to 775] ******************************************************************************************************************************************
ok: [hippo.kozow.com]

PLAY [4. Destroy Other Master Nodes] ***********************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************
ok: [giraffe.kozow.com]

TASK [4.1 Execute K3s Node Destruction] ********************************************************************************************************************************************
ok: [giraffe.kozow.com]

TASK [4.2 Change file permissions to 775] ******************************************************************************************************************************************
ok: [giraffe.kozow.com]

PLAY [6. Destroy Worker Nodes] *****************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************
ok: [zebra.kozow.com]

TASK [6.1 Execute K3s Node Destruction] ********************************************************************************************************************************************
ok: [zebra.kozow.com]

TASK [6.2 Change file permissions to 775] ******************************************************************************************************************************************
ok: [zebra.kozow.com]

PLAY RECAP *************************************************************************************************************************************************************************
giraffe.kozow.com          : ok=9    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
hippo.kozow.com            : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
localhost                  : ok=6    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
zebra.kozow.com            : ok=9    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```

## 4. Creasting the Kubernenes Cluster
### 4.1 Create the Primary Master Node
Run these commands
```
# SSH to Primary Master Node Server
ssh nickm@10.154.2.80
sudo su -
cd /opt/k3s

# Create the K3S Kubernetes Cluster
./1st_master_node.sh

# Verify K3S Kubernetes cluster was installed
kubectl get nodes

# retrieve the Cluster Security Node Token
cat /var/lib/rancher/k3s/server/node-token
```
Expect to get:
```
K1099ce5da7992f1bc7f037c6ee09244b83150a59ddcc49b4e1bbb8b31e4295f458::server:51c5355e2ede7b0a6c0d215409c6a22c
```

#### 4.1.1 Update the Git project
modify these files using the new Cluster Security Node Token
```
cd /opt/k3s/create_k3s_cluster_playbook_generator_with_ansible_roles/vars/

nano other_master_node_variables.yml
nano worker_node_variables.yml

# See what changed
git status

# Add changes
git add .

# Commit changes
git commit -m "Changes were made to token"

# Push changes
git push
```
We now need to regenerate the Create Shell scripts to use the new token
```
# Please go back to step 3. :
# 3. Create all the needed shell scripts on the Kubernetes K3S Cluster Node Servers
``` 

### 4.2 Create Other Master Node
Run the following Playbook : start_other_master_node_in_k3s_cluster.yml
```
git pull
cd /opt/k3s/create_k3s_cluster_playbook_generator_with_ansible_roles/playbooks/run_k3s_scripts

ansible-playbook  start_other_master_node_in_k3s_cluster.yml
```

To verify that a new node was added to the K3S Kubernetes cluster \
Run this command on the Primary Master Node Server
```
kubectl get nodes
```

### 4.3 Create the Worker Node
Run the following Playbook : start_worker_node_in_k3s_cluster.yml
```
ansible-playbook  start_worker_node_in_k3s_cluster.yml
```

To verify that a new node was added to the K3S Kubernetes cluster \
Run this command on the	Primary Master Node Server
```
kubectl get nodes
```

## 5. To Destroy the Kubernetes Cluster 
Run these playbooks in this order

```
ansible-playbook  terminate_worker_nodes.yml

ansible-playbook  terminate_other_master_nodes.yml

ansible-playbook  terminate_primary_master_node.yml

```
