---
title: "Creating a Jenkins Server in Proxmox"
date: 2023-09-09T07:01:24Z
draft: false
---
This installation will require that you have installed Proxmox and created an Ubuntu VM Template \
If you have not done that please watch this video: \
https://www.youtube.com/watch?v=lpRKVmxKVpE


## 1. Creating the Jenkins Server VM
1. Start Proxmox
2. Right click on Ubuntu VM Template
3. Click on Clone
4. Enter a suitable VM Id : 250
5. Enter a suitable Name (for the VM) : jenkins87 (we want to use static ip 10.154.2.87)
6. Select Mode : (as) Full Clone
7. Click on Clone Button

### 1.1 Start the Jenkins Server VM
In Proxmox click on 250(jenkins87)
Click on Console button and click on Start Now button

Login to the VM with user credentials used to create the VM Template
Get DHCP IP address of the Jenkins Server VM
run this command : ip a
we got inet 10.154.2.106/24

### 1.1 Set Static IP Address as 10.154.2.87
SSH to the Jenkins Server VM using Template Credentials
```
ssh nickm@10.154.2.106
# switch user to root
sudo su -
apt update
apt upgrade
apt install nano
```
Find the file : 00-installer-config.yaml

```
ls -la /etc/netplan/
```

Edit file : 00-installer-config.yaml
```
nano /etc/netplan/00-installer-config.yaml
```
Comment out (with # in beginning of each line) all existing content \
Make a note of the Ethernet Adapter:
```
#    ens18:
```

Add this to the file:
```
network:
  version: 2
  renderer: networkd
  ethernets:
    eno1:
      dhcp4: false
      dhcp6: false
      addresses:
       - 10.154.2.87/24
      routes:
       - to: default
         via: 10.154.2.3
      nameservers:
        addresses: [8.8.8.8,8.8.4.4]
```
Change the network/Ethernet adapter to ens18

Apply the changes
```
sudo netplan apply
```

You will now lose access to the server you need to go to Proxmox in Browser and click on Console button ad run command : \
sudo reboot

ow open a new ssh session from you PC
```
ssh nickm@10.154.2.87
sudo su -
```

## 2. Installing Jenkins
Get server details:
```
cat /etc/os-release 
```

Expect to get:
```
cat /etc/os-release 
PRETTY_NAME="Ubuntu 22.04.3 LTS"
```

### 2.1 Java Installation
AS JAVA 8 IS DISCONTINUED DO NOT INSTALL JAVA 8 \
We will do an Ubuntu installation , run these commands:
```
sudo add-apt-repository ppa:openjdk-r/ppa
sudo apt-get update
sudo apt install openjdk-11-jdk
# if required uncomment and run next command
#sudo apt --fix-broken install
java -version
```

Setting environment variables for nickm user
```
nano ~/.bashrc
```

Add this to the top of the file:
```
JAVA_11_HOME=/usr/lib/jvm/java-11-openjdk-amd64/bin/java
JAVA_HOME="$JAVA_11_HOME"
export JAVA_HOME

PATH="$JAVA_HOME/bin:$PATH"
export PATH
```
Setting	environment variables for root user
```
sudo su -
nano ~/.bashrc
```

Add this to the top of the file:
```
JAVA_11_HOME=/usr/lib/jvm/java-11-openjdk-amd64/bin/java
JAVA_HOME="$JAVA_11_HOME"
export JAVA_HOME

PATH="$JAVA_HOME/bin:$PATH"
export PATH
```

Now reboot the server and ssh back into it
```
sudo reboot
```

Now ssh back into the server and switch user to root
```
ssh nickm@10.154.2.87
sudo su -
```

### 2.2 Downloading The Jenkins Installable
Installing Jenkins on Ubuntu Server \
https://pkg.jenkins.io/debian-stable/ \

This is the Debian package repository of Jenkins to automate installation and upgrade. To use this repository, first add the key to your system:
```
sudo apt install curl

curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
```

Then add a Jenkins apt repository entry:
```
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
    https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null
```

Update your local package index, then finally install Jenkins:
```
sudo apt update
sudo apt install fontconfig
sudo apt install jenkins

sudo systemctl start jenkins
# expect to get:
Sep 09 10:54:02 nickm jenkins[1478]: 6599c24520074951b24ac1f7560360f2
Sep 09 10:54:02 nickm jenkins[1478]: This may also be found at: /var/lib/jenkins/secrets/initialAdminPassword

sudo systemctl status jenkins
```

### 2.3 Doing the initial Authentication using a Password
Open Browser \
http://10.154.2.87:8080

paste your password: 6599c24520074951b24ac1f7560360f2

### 2.4 Setting up Jenkins and adding Plugins
Customizing Jenkins \
select Box with	message: Select	pluggins to install

On the top click on : Suggested (Link)
Now click on the Install button
This will take a few minutes

### 2.5 Create the First Admin User
You will now be presented with a form t capture the Admin Users credentials and details
Make the username : admin
Choose a suitable password
supply full name and email address
Then press the Save and Continue button

This will display the Jenkins URL as: http://10.154.2.87:8080/
Now press the Save and	Finish button

Now Click on the Start Using jenkins Button
This should take to to the jenkins dashboard

**Jenkins is now ready for use**
