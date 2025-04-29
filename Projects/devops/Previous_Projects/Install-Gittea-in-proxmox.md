---
title: "Install latest version of Gitea in Proxmox"
date: 2024-04-27T10:26:52Z
draft: false
---
## 1. About Gitea
Gitea enables the creation and management of repositories based on Git. It also makes code review incredibly easy and convenient, enhancing code quality for . 

**[https://about.gitea.com/](https://about.gitea.com/)**

## 2. Documentation
[Gittea Forum](https://forum.gitea.com/)

[Gittea Documentation](https://docs.gitea.com/)

[Gittea Tutorials](https://about.gitea.com/resources/tutorials/)


## 3. Installing Gitea
[Download Gitea](https://about.gitea.com/products/gitea/)

https://dl.gitea.com/gitea/1.21.4/gitea-1.21.4-linux-amd64

### 3.1 SSH to Proxmov vm and do installation
ssh nickm@10.154.2.122

**run these commands :**
```
sudo su -

apt update

apt install nano git

mkdir /var/lib/gitea

mkdir /var/lib/gitea/data/

mkdir /opt/gitea

cd /opt/gitea/

wget https://dl.gitea.com/gitea/1.21.4/gitea-1.21.4-linux-amd64

ls -la

# copy gittea to bin folder
cp gitea* /usr/local/bin/gitea

# make Gitea executable
chmod 775 /usr/local/bin/gitea

# verify gitea is working
gitea --version

#Create the user/group for gitea to operate as
adduser --system --group --disabled-password --home /etc/gitea gitea

# Chown the following : 
chown gitea: /var/lib/gitea


#Create directory structure (mountpoint should be /var/lib/gitea)
mkdir -p /var/lib/gitea/{custom,data,log}

chown -R gitea: /var/lib/gitea/

chmod -R 750 /var/lib/gitea/

chown root:gitea /etc/gitea

chmod 770 /etc/gitea

# Now we create a Systemd Service: 

nano /etc/systemd/system/gitea.service
```
Put this into the file :
```
[Unit]
Description=Gitea (Git with a cup of tea)
After=syslog.target
After=network.target

[Service]
# Uncomment the next line if you have repos with lots of files and get a HTTP 500 error because of that
# LimitNOFILE=524288:524288
RestartSec=2s
Type=notify
User=gitea
Group=gitea
#The mount point we added to the container
WorkingDirectory=/var/lib/gitea
#Create directory in /run
RuntimeDirectory=gitea
ExecStart=/usr/local/bin/gitea web --config /etc/gitea/app.ini
Restart=always
Environment=USER=gitea HOME=/var/lib/gitea/data GITEA_WORK_DIR=/var/lib/gitea
WatchdogSec=30s
#Capabilities to bind to low-numbered ports
CapabilityBoundingSet=CAP_NET_BIND_SERVICE
AmbientCapabilities=CAP_NET_BIND_SERVICE

[Install]
WantedBy=multi-user.target

```
## 4. Run Gitea
Run these commands :
```
systemctl daemon-reload
systemctl enable --now gitea

# It may get stuck open browser now
systemctl enable gitea
systemctl start gitea

```

## 5. Configure Gitea
**Open gitea in browser (port 3000 |  You need to chose sqllight)**
[http://10.154.2.122:3000](http://10.154.2.122:3000)

Expect to get :
```
Initial Configuration
If you run Gitea inside Docker, please read the documentation before changing any settings.

Database Settings
Gitea requires MySQL, PostgreSQL, MSSQL, SQLite3 or TiDB (MySQL protocol).

Database Type 
Host 
127.0.0.1:3306
Username 
gitea
Password 
Database Name 
gitea
General Settings
Site Title 
Gitea: Git with a cup of tea
 You can enter your company name here.
Repository Root Path 
/var/lib/gitea/data/gitea-repositories
 Remote Git repositories will be saved to this directory.
Git LFS Root Path 
/var/lib/gitea/data/lfs
 Files tracked by Git LFS will be stored in this directory. Leave empty to disable.
Run As Username 
gitea
 The operating system username that Gitea runs as. Note that this user must have access to the repository root path.
Server Domain 
10.154.2.122
 Domain or host address for the server.
SSH Server Port 
22
 Port number your SSH server listens on. Leave empty to disable.
Gitea HTTP Listen Port 
3000
 Port number the Giteas web server will listen on.
Gitea Base URL 
http://10.154.2.122:3000/
 Base address for HTTP(S) clone URLs and email notifications.
Log Path 
/var/lib/gitea/log
 Log files will be written to this directory.

 Checks for new version releases periodically by connecting to gitea.io.
Optional Settings
Email Settings
Server and Third-Party Service Settings
Administrator Account Settings
These configuration options will be written into: /etc/gitea/app.ini
Powered by Gitea
Version: 1.21.4 Page:
5ms
Template:
5ms
Licenses
API
```

## 6. Installation with Docker (This is provided for our Docker fans)
[Installation with Docker](https://docs.gitea.com/installation/install-with-docker)

## Bibliography
https://docs.gitea.com/installation/install-from-binary \
https://www.rosehosting.com/blog/how-to-install-gitea-on-ubuntu-22-04/ \
https://www.apalrd.net/posts/2023/ultimate_gitea/ /
https://docs.gitea.com/installation/install-with-docker
