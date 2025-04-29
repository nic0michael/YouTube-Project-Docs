---
title: "Install windows in Docker on Proxmox LXC Container"
date: 2025-01-18T08:05:36Z
draft: false
---

## 1. Windows Docker images links
**[https://github.com/dockur/windows/tree/master](https://github.com/dockur/windows/tree/master)**

## Windows versions

```markdown
| Value | Version                     | Size  |
|-------|-----------------------------|-------|
| 11    | Windows 11 Pro              | 5.4 GB|
| 11l   | Windows 11 LTSC             | 4.2 GB|
| 11e   | Windows 11 Enterprise       | 5.8 GB|
| 10    | Windows 10 Pro              | 5.7 GB|
| 10l   | Windows 10 LTSC             | 4.6 GB|
| 10e   | Windows 10 Enterprise       | 5.2 GB|
| 8e    | Windows 8.1 Enterprise      | 3.7 GB|
| 7e    | Windows 7 Enterprise        | 3.0 GB|
| ve    | Windows Vista Enterprise    | 3.0 GB|
| xp    | Windows XP Professional     | 0.6 GB|
| 2025  | Windows Server 2025         | 5.0 GB|
| 2022  | Windows Server 2022         | 4.7 GB|
| 2019  | Windows Server 2019         | 5.3 GB|
| 2016  | Windows Server 2016         | 6.5 GB|
| 2012  | Windows Server 2012         | 4.3 GB|
| 2008  | Windows Server 2008         | 3.0 GB|
| 2003  | Windows Server 2003         | 0.6 GB|
```

## 2. Sample Docker Compose file
**[https://github.com/dockur/windows/blob/master/compose.yml](https://github.com/dockur/windows/blob/master/compose.yml)

## 3. Recommended Docker Compose file running Windows 11 Pro: use version: 11
**Make these changes in the above Docker Compose file:**
```yaml
    container_name: windows-11-pro
    environment:
      VERSION: "11"
```
## 4. Recommended Docker Compose file running Windows XP Professional: use version: xp
**Make these changes in	the above Docker Compose file:**
```yaml
    container_name: windows-xp
    environment:
      VERSION: "xp"
```


## 5. Installation procedure
Run these commands:
```bash
sudo su -

mkdir /opt/stacks/windows-xp -p

cd /opt/stacks/windows-xp

nano compose.yaml
```

Put this in the file:
```yaml
services:
  windows:
    image: dockurr/windows
    container_name: windows-xp
    environment:
      VERSION: xp
      USERNAME: ${USERNAME}
      PASSWORD: ${PASSWORD}
    cap_add:
      - NET_ADMIN
    ports:
      - 8006:8006
      - 3389:3389/tcp
      - 3389:3389/udp
    volumes:
      - ./storage:/storage
      - ./data:/data
    stop_grace_period: 2m

networks:
  default:
    driver: bridge
```
Run this command:
```bash
nano .env
```
Put this in the file:
```
USERNAME=admin
PASSWORD=Password
```

Run these commands:
```bash
docker compose pull

# Dont run in detached mode Ctrl C will make it gracefully shutdown
docker compose up -d
```
## 6. Open Windows in the browser
**[server-ip:8006](server-ip:8006)**


## 7. Other options RDP
With port 3389 open you can RDP to this Windows Container
