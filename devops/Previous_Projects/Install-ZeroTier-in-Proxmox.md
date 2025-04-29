---
title: "Install ZeroTier in Proxmox"
date: 2024-06-28T05:44:31Z
draft: false
---
**In the previous videos we have already covered :**
1. NetBird 
2. Tailscale
3. Twingate

**With ZeroTier you get more than what these and other Zero-Trust networking vpn replacement systems provide**

## 1. What is ZeroTier ?
**It's a software-defined networking service.** 

**ZeroTier can be used for a variety of purposes, including:**

- Creating secure private networks over the internet
- Connecting remote offices or devices
- Enabling remote access to resources
- Facilitating secure communications for gaming or other applications

**ZeroTier Overview**

- **Software-Defined Networking Service**
  - Create and manage secure virtual networks
  - Supports Windows, macOS, Linux, Android, iOS, **MikroTik, and IoT devices AND THAT MAKES THIS UNIQUE**

- **Key Features:**
  - **Device-Centric Architecture:**
    - Optimized for IoT deployments
    - Direct two-way connections with IoT devices
    - Works with any internet connection and location
  - **Lightweight Agent:**
    - Easy to install on almost any device
    - Ensures broad compatibility
  - **Unique Network Structure:**
    - Simplifies last-mile connections
    - Eases management of remote devices

- **Use Cases:**
  - Secure private networks
  - Connecting remote offices or devices
  - Enabling remote access to resources
  - Facilitating secure communications for applications like gaming


Feel free to let me know if you need any further modifications!
## 2. Resources
[Home page](https://www.zerotier.com/) \
[Pricing](https://www.zerotier.com/pricing/) \
[Comunity](https://www.zerotier.com/community/) \
[Download Zerotier](https://www.zerotier.com/download/)

## 3. Creating your Free ZeroTier Account
[ZeroTier Community](https://www.zerotier.com/community/)

### 3.1 The login page
[Login Page](http://my.zerotier.com/login)


## 3.2 Downloading ZeroTier for Servers and Devices
[Download ZeroTier](https://www.zerotier.com/download/)

**Installing ZeroTier on your server
```
sudo su -

curl -s https://install.zerotier.com | sudo bash

```

**Adding your server to the network**
```
sudo su
 
zerotier-cli status

zerotier-cli listnetworks

zerotier-cli join #######_YOUR_NETWORK_GUID_#########

zerotier-cli listnetworks
```
## 4. Making changes/adding new networks
### 4.1 Login and make the changes from this page
[Login Page](http://my.zerotier.com/login)

**Adding your server to the network**
```
sudo su
 
zerotier-cli leave abcdefghijklm

zerotier-cli join #######_YOUR_NEW_NETWORK_GUID_#########

zerotier-cli listnetworks
```


**Thank you for watching our video please subscribe to our Channel to get more videos and content like this** \
[Subscribe to our YouTube Channel](https://www.youtube.com/@dvp7388)
