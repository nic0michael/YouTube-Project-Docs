---
title: "Create a Smokeping Server in Your Proxmox Server"
date: 2024-02-17T13:45:38Z
draft: false
---
## 1. What does Smokeping give us
1. **Latency Monitoring:** Tracks network latency to detect delays in data transmission.
2. **Packet Loss Detection:** Identifies packet loss, crucial for assessing network reliability.
3. **Network Performance Analysis:** Offers insights into network performance trends over time.
4. **Troubleshooting Tool:** Assists in diagnosing network issues and pinpointing their causes.
5. **Capacity Planning:** Helps in determining network capacity requirements based on historical data.
6. **SLA Monitoring:** Assures compliance with Service Level Agreements (SLAs) by monitoring network performance against defined standards.
7. **Alerting Mechanism:** Notifies administrators of anomalies or breaches in predefined performance thresholds.
8. **Historical Reporting:** Provides historical data for analysis and decision-making regarding network optimization.
9. **Visualization:** Presents data in graphs and charts for easy interpretation and communication of network performance.

## 2. References
[https://oss.oetiker.ch/smokeping/](https://oss.oetiker.ch/smokeping/) \
[SmokePing Documentation](https://oss.oetiker.ch/smokeping/doc/index.en.html) \
[smokeping_examples](https://oss.oetiker.ch/smokeping/doc/smokeping_examples.en.html) \
[Smokeping Docker Compose file](https://docs.linuxserver.io/images/docker-smokeping/#usage)

## 3. You need to install Docker in your Proxmox Server the right way (LXC)
1. We created a LXC Container in our Proxmox Server
[Creating a LXC Container in our Proxmox Server](http://rino.kozow.com/devops/posts/create-lxc-containers-in-proxmox/)

2. We Installed Docker in our Proxmox LXC Container
[Installing Docker in our  Ubuntu Server 2204 LCX Container](http://rino.kozow.com/devops/posts/install-docker-on-ubuntu/)


3. Installing Portainer in our Proxmox server
[Installing portainer inside Docker](http://rino.kozow.com/devops/posts/install-portainer-inside-our-proxmox-lxc-container/

## 4. installing Smokeping in your Docker Container (in our LCX Contaainer)
### 4.1 Create the Docker Compose file
Run these commands:
```
sudo su -

mkdir /opt/smokeping

cd /opt/smokeping

nano docker-compose.yml
```

Put this inside the file:
```
---
services:
  smokeping:
    image: lscr.io/linuxserver/smokeping:latest
    container_name: smokeping
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - /path/to/smokeping/config:/config
      - /path/to/smokeping/data:/data
    ports:
      - 8080:80
    restart: unless-stopped
```

## 5. Start the Smokeping Container
Run this command
```
docker compose up
```

## 6. Open Smokeping in your browser
Our Docker runs in a Ubuntu LXC Container with IP Address 10.154.2.87
[http://10.154.2.87:8081](http://10.154.2.87:8081)


