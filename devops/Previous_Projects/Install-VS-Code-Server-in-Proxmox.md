---
title: "Install vs Code Server in Proxmox LXC Container with Docker"
date: 2024-12-14T07:07:32Z
draft: false
---
## We want to self-host our own VS Code  Server

- **Accessibility and Remote Development**:  
  - Access your development environment from anywhere via a web browser.

- **Containerization Benefits**:  
  - Isolated environment with easy management of dependencies and configurations.

- **Scalability and Portability**:  
  - Easily replicate and scale your setup with Proxmox and Docker.

## 1. Installation
Run these commands:
```bash
sudo su -

mkdir opt/stacks/code-server -p

cd opt/stacks/code-server

```
### 1.1 the .env file
run this command :
```bash
nano .env
```
Put this in the file:
```
# Timezone configuration (set to UTC)
TZ=Etc/UTC

# Secrets and passwords
PASSWORD=your_secure_password_here
PUID=1000   # User ID (adjust as needed)
PGID=1000   # Group ID (adjust as needed)

```
## 1.2 Create the Docker compose file
```bash
nano compose.yaml
```
Put this in the file
```yaml
version: "3.8"
services:
  code-server:
    image: codercom/code-server:latest
    container_name: code-server
    ports:
      - 8181:8080 # Expose the port for access
    environment:
      - PUID=${PUID} # User ID from .env file
      - PGID=${PGID} # Group ID from .env file
      - TZ=${TZ} # Timezone from .env file
      - PASSWORD=${PASSWORD} # Password from .env file
    volumes:
      - ./data:/home/coder/project # Path to store persistent data
    restart: unless-stopped
networks: {}
```
## 1.3 Start the Docker container in detached mode
```bash
docker compose up -d
```
## 1.4 Open in Browser
**http://your-server-ip:8181**

**[http://tiger.loseyourip.com:8181/login](http://tiger.loseyourip.com:8181/login)**


 
