---
title: "Install Home Assistant in Proxmox"
date: 2024-10-05T07:03:04Z
draft: false
---
**Home Assistant is a Home Automation Platform for managing devices in your home, like sensors, cameras, light switches and Solar and more.**

## 1. What Home Assistant gives us

- **Open-source platform** for automating smart home devices.
- **Centralizes control** of various smart devices (lights, thermostats, etc.).
- **Privacy-focused**: Runs locally without relying on cloud services.
- **Extensive integrations** with thousands of smart home products and services.
- **Customizable automations** to create complex, multi-device workflows.
- **Mobile-friendly** with apps for iOS and Android to control your home.
- **Active community**: Regular updates and a large ecosystem of add-ons.


## 2. Why have Home Assistant in Proxmox

- **Efficient resource management**: Proxmox offers virtualization, allowing Home Assistant to run efficiently with other VMs or containers.
- **Isolated environment**: Ensures Home Assistant runs in a dedicated, isolated space, improving security.
- **Snapshots and backups**: Easily create backups and restore Home Assistant using Proxmox's snapshot features.
- **Flexible deployment**: Proxmox supports both virtual machines (VMs) and containers, giving more options for running Home Assistant.
- **High availability**: Combine Proxmox with clustering features for better uptime and failover support.
- **Centralized management**: Manage multiple home services alongside Home Assistant from a single Proxmox interface.
- **Cost-effective**: Leverages Proxmox's open-source nature to create a powerful home automation system without additional software costs.


## 3. Docker Compose file
**https://www.home-assistant.io/installation/alternative/**

## 4. Installing In LXC Container
Open the LXC container in Console
Run these commands :
```
sudo su -
mkdir /opt/stacks/homeassistant/ -p

cd /opt/stacks/homeassistant/

```
### 5. Create out Docker Compose file
run this command
```
nano compose.yaml
```

Put this in the file
```
services:
  homeassistant:
    container_name: homeassistant
    image: ghcr.io/home-assistant/home-assistant:stable
    volumes:
      - ./config:/config
      - /etc/localtime:/etc/localtime:ro
      - /run/dbus:/run/dbus:ro
    restart: unless-stopped
    privileged: true
    network_mode: host
networks: {}

```

## 6. Start this in Detached mode
run this command
```
docker compose up
```

## 7. Open in browser
The default URL is : http://localhost:8123 /
**[http://tiger.loseyourip.com:8123](http://tiger.loseyourip.com:8123)**

## 8. Getting started with Home Assistant
**[Getting-started/onboarding/](https://www.home-assistant.io/getting-started/onboarding/)**

1. **Create an Account**  
   After accessing Home Assistant for the first time, you'll be prompted to create an account. This account will be used to log in and manage your smart home setup. Provide a username, password, and other details as required.

2. **Set Up Integrations**  
   Once logged in, go to **Settings > Devices & Services**. Home Assistant will scan your network for compatible devices (like smart lights, thermostats, and cameras). You can manually add integrations for services like Google Assistant, Zigbee, or Z-Wave from the **Integrations** page.

3. **Configure Automations**  
   Navigate to **Settings > Automations & Scenes** to create automation rules. Automations consist of triggers (such as a motion sensor being activated) and actions (like turning on a light). You can combine multiple conditions for more complex scenarios.

4. **Install Add-ons**  
   To extend functionality, go to **Settings > Add-ons**. Popular add-ons include:
   - **Node-RED** for creating advanced automations through a visual editor.
   - **File Editor** for easily managing configuration files.
   - **Mosquitto** for setting up MQTT for devices that use this protocol.

5. **Access via Mobile App**  
   Download the official **Home Assistant Companion** app from the iOS App Store or Google Play Store. The app provides real-time control over your smart devices, sends notifications for alerts or automation events, and supports location tracking to trigger automations based on your phone's location.

