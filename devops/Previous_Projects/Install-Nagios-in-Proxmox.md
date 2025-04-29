---
title: "Install Nagios in Proxmox"
date: 2024-07-02T11:01:39Z
draft: false
---

## 1. What is Nagios ?
- Nagios is an open-source monitoring tool.
- It oversees systems, networks, and infrastructure.
- Alerts users to potential issues.
- Monitors network services, host resources, and server components.
- Ensures reliable performance and quick response to problems.


## 2. Resources :
[www.nagios.org](https://www.nagios.org/) \
[Nagios Downloads](https://www.nagios.org/downloads/) \
[Nagios Core](https://www.nagios.org/downloads/nagios-core/) \
[Nagios Frontends](https://www.nagios.org/downloads/nagios-core-frontends/) \
[Nagios Plugins](https://www.nagios.org/downloads/nagios-plugins/) \
[Nagios Add-ons](https://www.nagios.org/downloads/nagios-core-addons/) \
[NRPE - How to Install Nagios RPE Remote Plugin Executor V4 from source](https://support.nagios.com/kb/article.php?id=515&creative=681721908674&keyword&matchtype&network=g&device=c&utm_agid=161544938051&placement&utm_term&utm_campaign&utm_source=google-ads&utm_medium=ppc&gad_source=1) \


## 3. Basic Installation
Run these commands:
```
apt update -y
apt upgrade -y

apt install apache2 -y

# Enable and start Apache2
systemctl enable apache2
systemctl start apache2

# install Nagios and pluggins
apt install nagios4 nagios4-core nagios-plugins-contrib -y

# Enable the Apache cgi modules with the a2enmod command:
a2enmod rewrite cgi

# restart apache
systemctl restart apache2

# start nagios service
systemctl enable nagios4
systemctl start nagios4

whereis nagios

ls -la /usr/lib/nagios
```

**Open server in browser**
[http://10.154.2.85/nagios4](http://10.154.2.85/nagios4)

## 4. Installing Nagios RPE Remote Pluggin 
[NRPE - Agent and Plugin Explained](https://nagiosenterprises.my.site.com/support/s/article/NRPE-Agent-and-Plugin-Explained-140f8443)

Run this command on the server you want to monitor applications remotely
```
sudo su -

apt install nagios-nrpe-plugin
```

### 4.1 Configure Nagios NRPE Pluggin
```
whereis nagios

nano /etc/nagios/nrpe.cfg
```
[Sample RPE Config file](https://github.com/envimate/nrpe)

## 5. Installing Nagios RPE Remote Pluggin Server
The main configuration file is /etc/nagios/nrpe.cfg. \
The directory for drop-in configuration is /etc/nagios/nrpe.d/ \
An empty local configuration file is also provided /etc/nagios/nrpe_local.cfg

Run this on the server you want to use as Remote Pluggin Server 
```
sudo su -

apt install nagios-nrpe-server
```

### 5.1 Configure Nagios NRPE Pluggin
```
whereis nagios

nano /etc/nagios/nrpe.cfg
```
[Sample RPE Config file](https://github.com/envimate/nrpe)


## Bibliography
### Install Nagios
[How to install Nagios on Ubuntu 22.04](https://www.fosslinux.com/122530/how-to-install-nagios-on-ubuntu-22-04.htm) \
[Nagios 4 Installation in Ubuntu 22.04 – Step-by-step2: Installation](https://tutorialforlinux.com/2023/01/29/nagios-4-ubuntu-22-04-installation-step-by-step/2/) \
[Nagios 4 Installation in Ubuntu 22.04 – Step-by-step3 : Getting Started](https://tutorialforlinux.com/2023/01/29/nagios-4-ubuntu-22-04-installation-step-by-step/3/) \
[How To Install Nagios 4 and Monitor Your Servers on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-install-nagios-4-and-monitor-your-servers-on-ubuntu-18-04) \

### Install Nagios NRPE
[How to Install Nagios NRPE Client on Ubuntu 20.04](https://tecadmin.net/how-to-install-nrpe-on-ubuntu-20-04/) \
[How to Add Ubuntu Host to Nagios](https://bobcares.com/blog/add-ubuntu-host-to-nagios/) \
[How To Install NRPE on Ubuntu 20.04 LTS](https://idroot.us/install-nrpe-ubuntu-20-04/) \
[How to install NRPE agent on Debian and Ubuntu](https://support.itrsgroup.com/hc/en-us/articles/360020062794-OP5-Monitor-How-to-install-NRPE-agent-on-Debian-and-Ubuntu) \
[How to setup NRPE for client side monitoring](https://linuxconfig.org/how-to-setup-nrpe-for-client-side-monitoring) \
[How to Install and Configure Nagios NRPE Client on Ubuntu 22.04](https://tecadmin.net/how-to-install-nagios-nrpe-client-on-ubuntu-22-04/) \
[Add Hosts to Nagios Server For Monitoring](https://kifarunix.com/add-hosts-to-nagios-server-for-monitoring/) \
[Installing Nagio NRPE on Ubuntu 18.04 through 22.04](https://outofcontrol.ca/blog/installing-nagio-nrpe-on-ubuntu-18-04) \

### Nagios Core Documentation
[Nagios Library : Nagios Core](https://library.nagios.com/library/products/nagios-core/) \
[Nagios Core Manuals](https://library.nagios.com/library/products/nagios-core/manuals/) \
[Nagios Core 4 Documentation](https://assets.nagios.com/downloads/nagioscore/docs/nagioscore/4/en/index.html) \

