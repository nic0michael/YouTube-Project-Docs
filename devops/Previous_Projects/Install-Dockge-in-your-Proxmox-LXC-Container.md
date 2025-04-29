---
title: "Install Dockge in Your Proxmox LXC Container"
date: 2024-05-08T07:31:21Z
draft: false
---
# Say goodbye to Portainer

## 1. What is Dockge ?
Dockge is a Portainer alternative.

### 1.1 This is what the Developer had to say
1. I have been using Portainer for some time, but for the stack management, I am sometimes not satisfied with it. \
   For example, sometimes when I try to deploy a stack, the loading icon keeps spinning for a few minutes without progress. \
   And sometimes error messages are not clear.
2. Try to develop with ES Module + TypeScript (Originally, I planned to use Deno or Bun.js, but they don't have support for arm64, so I stepped back to Node.js)
3. "Dockge" is a coinage word which is created by myself. \
**I originally hoped it sounds like Dodge**, but apparently many people called it Dockage, it is also acceptable. \
   The naming idea came from Twitch emotes like sadge, bedge or wokege. They all end in -ge.

**So lets pronounce it as "Docksh" giving it a Brazilian twist**

## 2. Features
1. Manage your compose.yaml files
2. Create/Edit/Start/Stop/Restart/Delete
3. Update Docker Images
4. Interactive Editor for compose.yaml
5. Interactive Web Terminal
6. (1.4.0) Multiple agents support - You can manage multiple stacks from different Docker hosts in one single interface
7. Convert docker run ... commands into compose.yaml
8. File based structure - Dockge won't kidnap your compose files, they are stored on your drive as usual. \
   You can interact with them using normal docker compose commands
9. Reactive - Everything is just responsive. Progress (Pull/Up/Down) and terminal output are in real-time
10. Easy-to-use & fancy UI - If you love Uptime Kuma's UI/UX, you will love this one too

## 3. Installing Dockge
**Instructions can be found here :** \
https://github.com/louislam/dockge#basic \
https://dockge.kuma.pet/

We will install Dockge in a Proxmox LXC Container where Docker has been previously installed


**Run these commands:**
```
# Connect to the LXC container (or open its terminal in the browser)
ssh root@10.154.2.87

# we need to be running as root
sudo su -

# Create directories that store your stacks and stores Dockge's stack
mkdir -p /opt/stacks /opt/dockge

cd /opt/dockge


# Download the compose.yaml
curl https://raw.githubusercontent.com/louislam/dockge/master/compose.yaml --output compose.yaml

# should you wish to customize this docker compose file
nano compose.yaml

# Start the server
docker compose up -d

# If you are using docker-compose V1 or Podman
# docker-compose up -d
```
## 4. Admin and usage
Open Dockge in the browser: \
[http://10.154.2.87:5001/](http://10.154.2.87:5001/)

This is demonstrated in our Youtube video : \
## 5. Add the /opt/stacks folder to Git visa vie Gitea
You want to do this every time you change or add a new Docker Container to the Stack to keep your stask under version control

**Run these commands :(After creating a Github Repository)** 
```
cd /opt/stacks

# Create local Git Repository
git init

# Create a README file (And put an initial comment inside this file)
nano README.md

ls -la

# Get Local Git Repository status
git status

# Add all files to Local Git Repository
git add .

# Do Initial Git commit
git commit -m "first commit"

# Add Github Repository origin (xxxxxxx and yyyyyyy will be provided by Github)
git remote add origin https://github.com/xxxxxxx/yyyyyyyyy.git

# Add Github user email address
git config --global user.email "xyxyxyxyxyx@gmail.com"

# Add Github user name 
git config --global user.name "xxxxxxxx"

# Set upstream origin and push to Github
git push --set-upstream origin master
```
**You can access our stacks here :** \
[https://github.com/nic0michael/MyDockerStacks](https://github.com/nic0michael/MyDockerStacks)

## 5. Please give them a Star for what wonderful software they have created


## Bibliography
https://github.com/louislam/dockge \
https://github.com/louislam/dockge#basic \
https://dockge.kuma.pet/ \
https://www.youtube.com/watch?v=AWAlOQeNpgU&t=48s \
https://www.youtube.com/watch?v=HEklvsr7q54 \
https://www.youtube.com/watch?v=E805XcbTzgY
