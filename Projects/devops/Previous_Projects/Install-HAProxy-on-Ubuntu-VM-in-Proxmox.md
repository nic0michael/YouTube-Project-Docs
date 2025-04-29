---
title: "Install HAProxy on Ubuntu VM in Proxmox"
date: 2023-11-26T12:46:06Z
draft: false
---


## 1. Get install Instructions
choose the version you want to install \
[https://haproxy.debian.net/](https://haproxy.debian.net/)

We decided to go with :  

I am running : \
Ubuntu \
Jammy (22.04 LTS) \
and I want to install HAProxy version  \
2.8-stable (LTS)
.

## 2. How to install the HAproxy on Ubuntu 22.04 through PPA repository
```
sudo su -
apt install --no-install-recommends software-properties-common
add-apt-repository ppa:vbernat/haproxy-2.8
```
**Now we will update the packages to update the list of repositories of Ubuntu:**
```
sudo apt update
```

**Finally we will install the package of HAProxy from its PPA repository:**
```
apt install haproxy=2.8.\* -y
```
**To check the installed HAProxy version, we will run the command:**
```
haproxy -v
```

## 3. How to configure the HAProxy on Ubuntu 22.04
As we have already installed the HAProxy on Ubuntu 22.04, we will configure and make it ready to use. \
**For the configuration of the HAProxy, we will open its configuration file by using the nano editor:**
```
sudo nano /etc/haproxy/haproxy.cfg
```

**Now at the end of the file, we will end the ip address of our localhost and the port with the bind keyword, so that it can get traffic on the following mentioned port and IP address:**
```
defaults
  mode http
  timeout client 10s
  timeout connect 5s
  timeout server 10s
  timeout http-request 10s

frontend myfrontend
  bind 10.154.2.88:80
```

**When these changes are appended, we will restart the service of HAProxy using the systemctl command:**
```
sudo systemctl restart haproxy
```
**Now we have configured so according to it, it will listen the requests on port 80 and to check this out, we will make a request using the curl command:**
```
curl 10.154.2.88:80
```
The output is showing that there is no server available to correspond the requests because we have not configured any server for HAProxy. 
**Now we will open another window of terminal and run the below-mentioned command to initialize a small web-application based on Python programming at port 8000:**
```
python3 -m http.server 8000 --bind 10.154.2.88
```

**Now again open the configuration file of the HAProxy and add the below mentioned lines:**
```
default_backend myservers

backend myservers
  server server1 10.154.2.88:8000
```
```
sudo nano /etc/haproxy/haproxy.cfg
```

```
defaults
  mode http
  timeout client 10s
  timeout connect 5s
  timeout server 10s
  timeout http-request 10s

frontend myfrontend
  bind 10.154.2.88:80
  default_backend myservers

backend myservers
  server server1 10.154.2.88:8000  
```
**Restart the HAProxy service again to reload the new changes:**
```
sudo systemctl restart haproxy
```
**Now again make the curl request and now you will see a response from the server:**
```
curl 10.154.2.88:80
```


## 4. Install Roxy GUI
You should go to this page: \
[https://roxy-wi.org/pricing#gsc.tab=0](https://roxy-wi.org/pricing#gsc.tab=0)

Here you can register to get the documentation or you can get support vis patreon (we recoment you do)

```
cd /var/www/
git clone https://github.com/hap-wi/roxy-wi.git /var/www/haproxy-wi
```

For Debian/Ubuntu: \
Change httpd to apache2 PATH and apache to www-data user in all configs

**Then create the database:**
```
cd /var/www/haproxy-wi/app
sudo ./create_db.py
sudo chown -R apache:apache /var/www/haproxy-wi/
sudo chown -R apache:apache /var/lib/roxy-wi/
```

### 4.1 After the installation: Configuring Roxy-WI
Once you have installed Roxy-WI, you have to edit the configuration file /etc/roxy-wi/roxy-wi.cfg. \
**The default configuration is shown below:**

```
[main]
# Path to the files destination
fullpath = /var/www/haproxy-wi
log_path = /var/log/roxy-wi
lib_path = /var/lib/roxy-wi

[configs]
# Folders for configs
haproxy_save_configs_dir = ${main:lib_path}/configs/hap_config/
keepalived_save_configs_dir = ${main:lib_path}/configs/kp_config/
nginx_save_configs_dir = ${main:lib_path}/configs/nginx_config/
apache_save_configs_dir = ${main:lib_path}/configs/apache_config/

[mysql]
# By default Sqlite DB is used
enable = 0
mysql_user = roxy-wi
mysql_password = roxy-wi
mysql_db = roxywi
mysql_host = 127.0.0.1
mysql_port = 3306
```


### 4.2 Database settings
Roxy-WI uses SQLite as the default database. 
**If you would like to use MySQL, make the following changes in the configuration file:**
```
[mysql]
#Enable MySQL DB. Default will be used Sqlite DB. Default disable</li>
enable = 1
mysql_user = roxywi
mysql_password = roxy-wi
mysql_db = roxywi
mysql_host = 127.0.0.1
```

#### 4.2.1 Once you have edited the configuration file, create the database for Roxy-WI on your MySQL Server:

```
MariaDB [(none)]> create user 'roxy-wi'@'%';
MariaDB [(none)]> create database roxywi;
MariaDB [(none)]> grant all on roxywi.* to 'roxy-wi'@'%' IDENTIFIED BY 'roxy-wi';
MariaDB [(none)]> grant all on roxywi.* to 'roxy-wi'@'localhost' IDENTIFIED BY 'roxy-wi';
```
### 4.3 Configuring Apache
**If you would like to use a custom domain name, you should make changes in the Apache configuration file for Roxy-WI:**
```
sudo vi /etc/httpd/conf.d/roxy-wi.conf
```

**If you would like to enable the redirect from port 80 to port 443, add the following lines to your configuration file:**
```
<VirtualHost *:80>
        ServerName my_domain.local
        Redirect permanent "/" "https://buffalo.loseyourip.com/"

</VirtualHost>
```

**Find the following lines and replace "roxy-wi.example.com" with your domain name:**
```
<VirtualHost *:443>
    ...
    ServerName buffalo.loseyourip.com
    ...
</VirtualHost>
```

**If you would like to use custom certificates, edit the following lines:**
```
<VirtualHost *:443>
    ...
    SSLEngine on
    SSLCertificateFile /var/www/haproxy-wi/app/certs/haproxy-wi.crt
    SSLCertificateKeyFile /var/www/haproxy-wi/app/certs/haproxy-wi.key
    ...
</VirtualHost>
```



## 5. Installing Mariadb
```
sudo apt update
sudo apt install mariadb-server
sudo mysql_secure_installation
```

```
sudo mariadb

GRANT ALL ON *.* TO 'roxy-wi'@'localhost' IDENTIFIED BY 'roxy-wi' WITH GRANT OPTION;

FLUSH PRIVILEGES;

CREATE DATABASE roxywi;
```


## Bibliography
https://haproxy.debian.net/#distribution=Ubuntu&release=jammy&version=2.8 \
https://linuxhint.com/install-configure-haproxy-ubuntu-22-04/ \
https://roxy-wi.org/installation#manual&gsc.tab=0 \
https://www.digitalocean.com/community/tutorials/how-to-install-mariadb-on-ubuntu-22-04







