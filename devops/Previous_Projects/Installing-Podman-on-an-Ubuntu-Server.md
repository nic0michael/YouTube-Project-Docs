---
title: "Installing Podman on an Ubuntu Server"
date: 2024-09-28T11:35:30Z
draft: false
---

Here are the short instructions to install **Podman** on an **Ubuntu server** and run an NGINX Docker Compose file in detached mode, exposing NGINX on **port 8080**.

### 1. **Install Podman**:
```bash
ssh nickm@10.154.2.56

sudo su -

mkdir /opt/podman/ -p

cd /opt/podman

sudo apt update

sudo apt upgrade

sudo apt install podman -y

# validate podman
podmn -v
```

### 2. **Install Podman-Compose**:
```bash
sudo apt install python3-pip -y

sudo pip3 install podman-compose
```

### 3. **Create Docker Compose File**:
```bash
nano docker-compose.yml
```

   Add the following content to expose NGINX on **port 8080**:
```yaml
version: '3'
services:
  nginx:
    image: nginx:latest
    ports:
      - "8080:80"
```

### 4. **Run Podman Compose in Detached Mode**:
```bash
podman-compose up -d
```

### 5. **Open NGINX in Browser**:
 Once the container is running, access NGINX by opening the following URL in your browser: \
**[http://10.154.2.56:8080](http://10.154.2.56:8080)**
