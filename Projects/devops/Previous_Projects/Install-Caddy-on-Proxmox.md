---
title: "Install Caddy on Proxmox"
date: 2024-12-31T12:49:02Z
draft: false
---

## Official website
**[https://caddyserver.com/](https://caddyserver.com/)**

**[https://caddyserver.com/docs/](https://caddyserver.com/docs/)**

**[https://github.com/caddyserver/caddy/releases](https://github.com/caddyserver/caddy/releases)**




**[https://github.com/caddyserver/caddy/releases/tag/v2.8.4](https://github.com/caddyserver/caddy/releases/tag/v2.8.4)**

**[https://github.com/caddyserver/caddy/releases/download/v2.8.4/caddy_2.8.4_linux_amd64.deb](https://github.com/caddyserver/caddy/releases/download/v2.8.4/caddy_2.8.4_linux_amd64.deb)**


**[https://caddyserver.com/docs/install](https://caddyserver.com/docs/install)**

**[https://caddyserver.com/docs/install#debian-ubuntu-raspbian}(https://caddyserver.com/docs/install#debian-ubuntu-raspbian)**

## Ubuntu / Debian ESX installation Stable Release installation
Run these commands:
```bash
sudo su -

apt update

apt install curl wget nano -y


sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https curl

curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg

curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list

sudo apt update

sudo apt install caddy


# verify Caddy Service Status
curl localhost:2019/config

systemctl status caddy

systemctl status caddy.service
```
### Using the Service
Run this command:
```bash

sudo nano /etc/caddy/Caddyfile
```
You can place your static site files in either /var/www/html or /srv. Make sure the caddy user has permission to read the files.

We replace:
```
:80 {
        # Set this path to your site's directory.
        root * /usr/share/caddy
```
with this:
```
:hippo.kozow.com {
        # Set this path to your site's directory.
        root * /var/www/html 
```

Create index file
```bash
mkdir /var/www/html/ -p

cp /usr/share/caddy/index.html /var/www/html/index2.html

nano /var/www/html/index.html
```

Put this in the file:
```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hello from Caddy</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background: linear-gradient(135deg, #4facfe, #00f2fe);
            color: white;
        }
        h2 {
            background-color: rgba(0, 0, 0, 0.5);
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.2);
        }
    </style>
</head>
<body>
    <h2>Hello from Caddy</h2>
</body>
</html>

``` 


To reload caddy service
```bash
systemctl reload caddy.service
```

When running with our official service file, Caddy's output will be redirected to journalctl. To read your full logs and to avoid lines being truncated:
```bash
journalctl -u caddy --no-pager | less +G
```

If using a config file, you can gracefully reload Caddy after making any changes:
```bash
sudo systemctl reload caddy
```

You can stop the service with:
```bash
sudo systemctl stop caddy
```

The Caddy process will run as the caddy user, which has its $HOME set to /var/lib/caddy. This means that:
1. The default data storage location (for certificates and other state information) will be in /var/lib/caddy/.local/share/caddy.
2. The default config storage location (for the auto-saved JSON config, primarily useful for the caddy-api service) will be in /var/lib/caddy/.config/caddy.

## Docker Installation
Run these commands:
```bash
sudo su -

docker pull caddy

mkdir /opt/stacks/caddy

cd /opt/stacks/caddy

nano compose.yaml
```
 Put this in the file:
```yaml

services:
  caddy:
    image: caddy:<version>
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
      - "443:443/udp"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile
      - ./site:/srv
      - caddy_data:/data
      - caddy_config:/config

volumes:
  caddy_data:
  caddy_config:
```

## Using the Service
Start Caddy run these commands
```bash
docker compose up -d

# To reload Caddy after making changes to your Caddyfile:
docker compose exec -w /etc/caddy caddy caddy reload

# To see Caddy's 1000 most recent logs, and follow to see new ones streaming in:
docker compose logs caddy -n=1000 -f
```

**[http://10.154.2.80](http://10.154.2.80)**

**[http://hippo.kozow.com](http://hippo.kozow.com)**

