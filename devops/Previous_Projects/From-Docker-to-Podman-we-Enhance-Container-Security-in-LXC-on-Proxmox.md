---
title: "From Docker to Podman We Enhance Container Security in LXC on Proxmox"
date: 2024-12-21T09:16:05Z
draft: false
---

Here’s how you can structure your talk with additional points:

---

## 1. Introduction
- Docker is ideal for homelabs, where simplicity and convenience are key.  
- For business use, Docker's root privileges increase the hacker attack surface.  

## 2. Key Points
- Containers running as root can escalate privileges, making your system more vulnerable.  
- Podman eliminates this risk by running containers without root privileges by default.  
- This separation minimizes the impact of potential container exploits.  
- Podman's daemonless architecture also reduces the chance of centralized attack vectors.  

## 3. Conclusion
- We encourage you to create a sandbox environment to test Podman and explore its security benefits firsthand.  

---

## 4. Installation
Run these commands :
```bash
sudo su -

mkdir /opt/stacks -p

chown -R nickm: /opt/
```
---

### 4.1 Install Podman
Now run hese commands :
```bash

cd /opt/stacks

sudo apt update

sudo apt install podman -y

# Podman commands are Docker comptable
podman --version  

# we get errors if we don't reboot
sudo reboot

#We need to enable Socket
#sudo systemctl enable podman.socket
```
### 4.2 Podman pull
Launch a container from a specified image: In this instance, we will pull a web server image (NGINX) using Podman.
```bash
podman pull docker.io/library/nginx

# List Images
podman image ls
```
### 4.3 Podman run
```bash
podman run -d -p 8090:80/tcp --name NicosWeb docker.io/library/httpd

podman ps -a
```
Open in Browser \
**[http://localhost:8090](http://localhost:8090)**

**[http://10.154.2.66:8090](http://10.154.2.66:8090)**

```bash
podman stop NicosWeb

podman container prune -f
```

### 5. Install Podman-Compose

Run these commands :
```bash
sudo apt install podman-compose  -y

# Verify podman-compose
podman-compose --version

```
## 6. We run Nginx as a web server using a Docker Compose file
Run these commands :
```bash
mkdir /opt/stacks/nginx -p
cd /opt/stacks/nginx

```


### 6.1 Create docker compose file
Run this command:
```bash
nano compose.yaml
```

Put this in the file
```yaml
version: '3.8'

services:
  web:
    image: nginx:latest
    ports:
      - "8090:80"
    volumes:
      - ./html:/usr/share/nginx/html
    networks:
      - webnet

  app:
    image: node:14
    working_dir: /usr/src/app
    command: "node server.js"
    volumes:
      - ./app:/usr/src/app
    networks:
      - webnet

networks:
  webnet:
    driver: bridge

```

### 6.2 Create a directory called podman-compose:
```bash
mkdir html app -p

echo "<h1>It works</h1>" > html/index.html

echo "const http = require('http'); const server = http.createServer((req, res) => { res.end('Hello from Node.js'); }); server.listen(8080);" > app/server.js

```

### 6.3 Start Container
```bash
podman-compose up -d

podman-compose logs

```

Open in browser
**[http://localhost:8080/](http://localhost:8080/)**

**[http://10.154.2.85:8080/](http://10.154.2.66:8080/)**
--- 


