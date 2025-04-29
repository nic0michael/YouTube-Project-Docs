---
title: "Install NetBird in Proxmox"
date: 2024-04-16T05:59:59Z
draft: false
---
We will :
Use Netbird with Proxmox

## 1. Documentation
### 1.1 Get started:
[https://app.netbird.io](https://app.netbird.io)

### 1.2 Installation docs: 
[https://docs.netbird.io/how-to/getting-started#installation](https://docs.netbird.io/how-to/getting-started#installation) \
[https://docs.netbird.io/how-to/installation#installation](https://docs.netbird.io/how-to/installation#installation) 
[Installation](https://docs.netbird.io/how-to/installation)

### 1.3 GitHub:
[https://github.com/netbirdio/netbird](https://github.com/netbirdio/netbird)

### 1.4 Self-Hosting Guide:
[https://docs.netbird.io/selfhosted/selfhosted-quickstart](https://docs.netbird.io/selfhosted/selfhosted-quickstart)

### 1.5 Docker
[Running NetBird in Docker](https://docs.netbird.io/how-to/getting-started#running-net-bird-in-docker) \
[NetBird Client in Docker](https://docs.netbird.io/how-to/examples#net-bird-client-in-docker)

## 2. Installing Netbird CLI
### 2.1 Add the repository:
Run these commands :
```
sudo su -
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg -y
curl -sSL https://pkgs.netbird.io/debian/public.key | sudo gpg --dearmor --output /usr/share/keyrings/netbird-archive-keyring.gpg
echo 'deb [signed-by=/usr/share/keyrings/netbird-archive-keyring.gpg] https://pkgs.netbird.io/debian stable main' | sudo tee /etc/apt/sources.list.d/netbird.list

```
### 2.2 Update APT's cache
Run this command
```
sudo apt-get update
```

### 2.3 Install the package
```
# for CLI only
sudo apt-get install netbird
# for GUI package
sudo apt-get install netbird-ui
```
At this stage  you get a browser pop-up to log in

To get the status run this command
```
netbird status

# on the laptop run
netbird up

# There is an expiry date for your peers
# (including this machine) 
# for fixing that you need to re-authenticate
netbird up
```
## Browser operations
Open the browser if id did not pop-up

Expect to get this in the terminal
```
If your browser didn't open automatically, use this URL to log in:

https://login.netbird.io/activate?user_code=XXXX-XXXX
```
Open the browser if id did not pop-up use the above url to create your account
## After creating your account login from the browser
First click on the button on your welcome email

Then login from here :
[https://app.netbird.io](https://app.netbird.io/)


[https//netbird.io/](https://netbird.io/)

### Save the tempory management key
```

```


## 3 Adding a server to the network
**You need to ssh to the VM in a new Terminal**

### 3.1 Add the repository:
Run these commands :
```
sudo su -
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg -y
curl -sSL https://pkgs.netbird.io/debian/public.key | sudo gpg --dearmor --output /usr/share/keyrings/netbird-archive-keyring.gpg
echo 'deb [signed-by=/usr/share/keyrings/netbird-archive-keyring.gpg] https://pkgs.netbird.io/debian stable main' | sudo tee /etc/apt/sources.list.d/netbird.list

```
### 3.2 Update APT's cache
Run this command
```
sudo apt-get update
```

### 3.3 Install the package
```
# for CLI only
sudo apt-get install netbird

```
### 3.4 connect the server
```
# on the server run
netbird up


netbird up --management-url <YOUR_MANAGEMENT_URL> --setup-key <YOUR_SETUP_KEY>


```
### 3.4 Open Netbird Cloud in the Browser
[https://app.netbird.io](https://app.netbird.io)


### 3.5 Optional steps
Copy the verification URL and open it with the browser

Open the Admin Panel in browser:

You should see your new server there

You two machines should be pingable

Set the Server to a static IP address : 10.154.50.50 \
[Use these instructions (Chapter 10.) ](http://rino.kozow.com/linux/posts/usefull-linux-commands/)

## 4. Docker
They have provided this Docker command:
```
docker run --rm --name PEER_NAME --hostname PEER_NAME --cap-add=NET_ADMIN --cap-add=SYS_ADMIN --cap-add=SYS_RESOURCE -d -e NB_SETUP_KEY=<SETUP KEY> -v netbird-client:/etc/netbird netbirdio/netbird:latest
```
### 4.1 Using Docker Compose file
Run these commands:
```
sudo su -
mkdir /opt/netbird
mkdir /opt/netbird/volume
cd /opt/netbird
nano docker-compose.yml
```
Put this in the file
```
version: '3.8'

services:
  peer:
    container_name: PEER_NAME
    hostname: PEER_NAME
    image: netbirdio/netbird:latest
    environment:
      - NB_SETUP_KEY=<SETUP KEY>
    volumes:
      - /opt/netbird/volume:/etc/netbird
    cap_add:
      - NET_ADMIN
      - SYS_ADMIN
      - SYS_RESOURCE

```
You need to change the following :
```
<SETUP KEY> Please use the key from the NetBird Console (Dont use ONE TIME KEY)
PEER_NAME   Please use a name that describes your server
```

Run your container in Detaches mode
```
sudo docker compose up -d
```



## Biblioghraphy

[NetBird Full Demo - P2P Overlay Network With SSO, MFA, Access Controls and Routing](https://www.youtube.com/watch?v=G7aNfyaSXg4&t=301s)

