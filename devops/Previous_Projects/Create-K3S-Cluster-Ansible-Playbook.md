---
title: "Create K3S Cluster Ansible Playbook"
date: 2023-12-16T08:01:13Z
draft: false
---
## Please Note
Our Ansible Playbooks have been updated improving the way thay work. \
We have made a small change to this project to SCP Copy the generated shell scripts to our K3S Node VMs to the /opt/k3s/ folder \
We have decided not to automate the running of these shell scripts as we need to use the Token of the Primary Master Node in the shell scripts of all the other K3S Nodes

## 0. Before we Start our VMs need the folder /opt to be owned by the login user
**Run these commands on all VMs to be added to our K3S Kubernetes Cluster**
```
sudo su -
mkdir /opt-k3s

# I am loging in with user: nickm
chown -R nickm: /opt

# verify that your user owns these folders
ls -la /opt

```


## 1. Clone out GitHub project
**Connect to Leopard VM (Semaphore server)**
```
ssh nickm@leopard

sudo su -

mkdir /opt/semaphore
mkdir /opt/k3s
cd /opt/semaphore
```
**Clone GitHub project : Create-K3S-Cluster-Ansible-Playbook**
We will colne this project: \
https://github.com/nic0michael/Create-K3S-Cluster-Ansible-Playbook

```
git clone https://github.com/nic0michael/Create-K3S-Cluster-Ansible-Playbook.git


ls -la
```


## 2. Login the Semaphore VM
Create start shell script
```
nano /usr/bin/start-semaphore
```
Put this in the file:
```
cd /root
semaphore service --config=./config.json
````

Start the Semaphore server:
```
sudo su -
start-semaphore
```
IF YOU DONT HAVE THIS SHELL SCRIPT RUN THIS COMMAND
```
sudo nano /usr/bin/start-semaphore
```
Put this into the file:
```
cd /root
semaphore service --config=./config.json &
```
Run this command:
```
sudo chmod 775 /usr/bin/start-semaphore
```

Now you can run this command
```
sudo su -
start-semaphore
```



**Open your browser to your Semaphore VM**
Login using the credentials you created the Semaphore server. (We used admin/admin for our test server)
[http://10.154.2.95:3000/auth/login](http://10.154.2.95:3000/auth/login)



## 3. Setup the Semaphore to create K3S Cluster Nodes

### 3.1 Creating users
1. In Left Navigator Menu click on Team
2. On top right click on the New Team Member button
3. select user and select role

### 3.2 Create Key Store (robota)
1. In Left Navigator Menu click on KeY Store
2. On top right click on the New Key button
3. enter name for key (robota)
4. select type(ssh) and enter credentials (credentials for ssh user robota)
5. We need aditional credentials for robota-root
5. Create credentails for robota-root
6. Type: Login Password

### 3.3 Create Environment (robota)
1. In Left Navigator Menu click on Enviroment
2. On top right click on the New Environment button
3. Enter name: robota
Add this to the Extra variables field
```
{
  "ansible_ssh_user": "robota"
}  
 
```

### 3.4 Adding repositories (create-k3s-cluster-playbook-generator)
1. In Left Navigator Menu click on Repositories
2. On top right click on the New Repository
3. Enter Name : create-k3s-cluster-playbook-generator
4. Enter Url or Path (SSh Http File Git)
5. Enter Branch: master
6. Enter Access Key


### 3.5 Create Inventory (inventory)
1. In Left Navigator Menu click on Inventory
2. On top right click on the New Inventory button
3. Enter inventory name : inventory
4. Select type (File)
5. Enter Path to Inventory file (/inventories/production/inventory.ini)

WE ARE USING THIS: **Path to Inventory file** \
**/opt/semaphore/Create-K3S-Cluster-Ansible-Playbook/inventories/production/inventory.ini** \
WE SUGGEST YOU USE THIS PATH IF YOU CLONED OUR PROJECT AS WE RECOMENDED

### 3.6 Creating task Template for create_k3s_primary_master_node.yml
1. In Left Navigator Menu click on Task Templates
2. On top right click on the New Template button
3. Enter Name : create_k3s_primary_master_node
4. Enter Descxription: create_k3s_primary_master_node
5. Enter Playbook Filename: create_k3s_primary_master_node.yml
6. Enter Inventory: inventory
7. Enter Repository: create-k3s-cluster-playbook-generator
8. Enter Environment: robota
9. Enter Vault Password: robota_root
10. Add Survey Variables :  (We need these variables as Strings and Required) we added values for a second master node
```
node_name  (giraffe.kozow.com)
node_type (OTHER_MASTER_NODE)
master_node_ip_address (10.154.2.80)
k3s_token (K1002ab SHORTENED 6a32f35cfac4116fc6cf22b9176cc66)
destination_file(2nd_master_node.sh)
username(nickm)
password(PASSWORD)
node_ip (10.154.2.81)

```
Please note node_type can take one of these values:
```
OTHER_MASTER_NODE
1ST_MASTER_NODE
WORKER_NODE
```
### 3.7 create task templates for the other two playbooks
add the same procedure, values, and Survey Variables as above for playbooks:
1. create_k3s_other_master_node.yml
2. create_k3s_worker_node.yml

## 4. Preperation of K3S Node VMs
Login to every Cluster Node VM and runn these commands:
```
# Allow Root User login
sudo passwd root

# Create k3s folder
mkdir /opt/k3s
```

## 5. Running the Playbooks in Semaphore
If you have not started Semaphore Run this command on the Semaphore VM
Start the Semaphore server:
```
sudo su -
start-semaphore
```
Now run your Playbooks

After Loging in click on Task Templates in the Left Navigator

**Open your browser to your Semaphore VM**
Login using the credentials you created the Semaphore server. (We used admin/admin for our test server)
[http://10.154.2.95:3000/auth/login](http://10.154.2.95:3000/auth/login)

## 6. See what Semaphore created locally
```
sudo apt install tree -y
ls -la /tmp/semaphore
```
Expect to get:
```
drwxr-xr-x  3 root root 4096 Dec 17 15:31 .ansible
drwxr-xr-x  7 root root 4096 Dec 17 17:06 repository_1_1

```

Now list the structure of this folder
```
tree /tmp/semaphore/repository_1_1/
```

Expect to get:
```
tree /tmp/semaphore/repository_1_1/

/tmp/semaphore/repository_1_1/
├── Readme.md
├── inventories
│   └── production
│       └── inventory.ini
├── playbooks
│   ├── create_k3s_other_master_node.yml
│   ├── create_k3s_primary_master_node.yml
│   └── create_k3s_worker_node.yml
├── semaphore
│   └── Readme.md
└── templates
    └── k3s_node_maker.j2

```
## 7.After Running The Playbooks in Semaphore

### 7.1 List the generated files in the K3S Node VMs:
Run this in the Primary Master Node VM
```
ls -la /opt/k3s
```
 

Expect to get:
```
ls -la /opt/k3s
-rw-r--r-- 1 root root  118 Dec 17 17:15 1st_master_node.sh

```
First run this generated shell script in the Primary Master Node \
After generating your primary master Node run this command
```
cat /var/lib/rancher/k3s/server/node-token
```

Now Copy this Token and update the Task Templates for Other Master Nodes and for worker nodes by running them an using this token for the k3s_token field \
This will generate all the required scripts in the Node VMs with the correct Token \
Now you can run all the generated scripts for Other Master Node VMs and for Worker Node VMs



## 8. Deleting K3S Nodes
You may want to delete your K3S Production Cluster \
These are the commands you need:
### 8.1 Deleting Master Nodes
Run these commands in the Master Node VMs
```
/usr/local/bin/k3s-killall.sh
/usr/local/bin/k3s-uninstall.sh
```
### 8.2 Deleting Worker nodes
Run these commands in the Worker Node VMs
```
/usr/local/bin/k3s-killall.sh
/usr/local/bin/k3s-agent-uninstall.sh
```
