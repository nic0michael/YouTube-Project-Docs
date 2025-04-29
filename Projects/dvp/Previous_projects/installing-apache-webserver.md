
---
title: "Installing Apache Webserver"
date: 2022-10-29T15:09:54Z
draft: False
---

## 1. Installing Apache on Centos server

### login as root
```
ssh yourUserName@yourServerIpAddress
sudo su -
```
### Update packages
```
yum update
```
### Install nano editor
```
yum install nano
```
### Installing Apache web server
```
yum install httpd
```
### update Apache web server
```
yum update httpd
```

### Seart HTTP service
```
systemctl start httpd
```

### check the staus of the http service
```
systemctl status httpd
```
## 2. Open the firewall for Web Server

### Open firewall for http (port 80)
```
firewall-cmd --permanent --add-service=http
```
### open filewall for https
```
firewall-cmd --permanent --add-service=https
```

### reload open ports into firewall
```
firewall-cmd --get-active-zones
```
### view the status of the firewall
```
firewall-cmd --reload
```
### view firewall state
```
firewall-cmd --state
```

## 3. Create simple web page on the server
### Create a html page file
```
cd /var/www/html/
nano index.html
```
### Paste this content in this file
```
<html>
<head></head>
<body>
Hello World
</body>
</html>
```

## 4. Install Hugo for Static Content on this server
**The instructions on this blog page:**

http://rino.kozow.com/dvp/posts/install-hugo/ 

