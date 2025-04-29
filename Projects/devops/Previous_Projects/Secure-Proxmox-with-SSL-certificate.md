---
title: "Secure Proxmox With SSL Certificate"
date: 2025-03-23T16:48:51Z
draft: false
---

## Duckduck DNS
**[https://www.duckdns.org/](https://www.duckdns.org/)**

Signup using Google (Zero Trust)

Once you login you will find the Token

Save your DuckDNS Token

Now create a  domain mydomain.duckdns.org

Enter your subdomane name click on [add domain]

Change current ip to server to host Nginx

Click on [update ip]

## Docker Installation
Go to DockerHub **[https://hub.docker.com/](https://hub.docker.com/)**

Search for : nginx-proxy-manager
 select : jc21/nginx-proxy-manager

**[https://hub.docker.com/r/jc21/nginx-proxy-manager](https://hub.docker.com/r/jc21/nginx-proxy-manager)**

Docker pull command : docker pull jc21/nginx-proxy-manager

Run these commands:
```bash
mkdir /opt/stacks/nginx-proxy -p

cd /opt/stacks/nginx-proxy

nano compose.yaml

```
Put this in the file:
```yaml
version: "3.8"
services:
  nginx-proxy-manager:
    image: jc21/nginx-proxy-manager
    container_name: nginx-proxy-manager
    ports:
      - 80:80
      - 81:81
      - 443:443
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
    restart: unless-stopped
```

Run these commands :
```bash
docker compose pull

docker compose up -d

```
**[http://10.154.2.87:81](http://10.154.2.87:81)**

http://tiger.loseyourip.com:81/login

Default Login : admin@example.com \
password: changeme

Once logged in you are prompted to change the default login

Put your user details correct email address press save

Now change your password press save

**Generate SSL certificate**

click SSL Certificates  on top menu 
enter domain names : (in add domain name)
upupa.duckdns.org 
*.mydomain.duckdns.org

This will allow us to have valid domains for all of our subdomains

First : enable accept terms of service
Second : enable DNS Chalange
in dropdown select DuckDNS

Paste your DuckDNS Token (in Credentials file content)

Add 30 seconds propagation if you get an error (you may need to increase this)

This is also where you can renew youe SSL Expiration date

We now have a valid SSL Certificate for our domain

**Setting Reverse Proxies**
In the Top Menu select Hosts

Select Proxy Hosts

Click [add proxy host]
For Domain Name we will use : nginx.mydomain.duckdns.org
Put the Ip of Nginx proxey management server 10.154.2.87 and port 81
Enable these :
	cache assets (if nneeded)
	Web socket Support
	Block Common Exploits
We wil add SSL certificate we generated
click on SSL tab

Paste certificate in : SSL Certificate
You can chose to enable these :
	Force SSL
	HTTP/2 Support

click on save

You will now have the new URLs for NGINX

Click on new Nginx url

You will see that it has a secure icon in the browser

You should now login with the new created credentials

**Adding Proxmox**
Clich add Host button

We will use a new domain name for proxmox
proxmox.mydomain.duckdns.org

Change the scheme to HTTPS

enter IP Address and port number 10.154.2.89 The prot for Proxmox is 8006

**Add the SSL certificate**
click on SSL tab

Paste certificate in : SSL Certificate

Enable these :
        cache assets (if needed)
        Web socket Support (is needed)
        Block Common Exploits

Click on save

Click on new Proxmox url

You will see that it has a secure icon in the browser


## Bibliography
https://www.youtube.com/watch?v=acturgE4TmE
