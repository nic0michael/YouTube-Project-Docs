---
title: "Install Docker Desktop on Windows 11 and MAC"
date: 2024-12-02T19:29:11Z
draft: false
---


## 1. Install Docker on windows 11

### 1.1 Enable WSL2

Run this command in Command Prompt or Power Shell
```
wsl --status

# if WSL2 is not installed run this command
wsl --install
```

Restart your laptop

The wsl2 installation process will continue after reboot

Give your WSL Linux a user name and password (**remember that**)


### 1.2 Download docker here :
**[Click here to  download Docker Desktop](https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe?utm_source=docker&utm_medium=webreferral&utm_campaign=docs-driven-download-win-amd64)**


launch the installer \
the following options must be selected : \
[x] Use WSL2 instead of Hyper-V(recommended) \
[x] Add shortcut to desktop

Click Close and restart

click Accept \
click Signup \
click skip survey 
#### PLEASE USE YOUR GOOGLE ACCOUNT TO LOGIN


### 1.3 Test your Docker
Open Command prompt run these commands
```
# to check docker version
docker version

# Test docker in command prompt

docker run hello-world
# You should get Hello from Docker!
```

#### After installing Docker you need to apply for a Corporate Docker Desktop License
---

## 2. Install Docker Desktop on MAC

#### PLEASE USE YOUR  GOOGLE ACCOUNT TO LOGIN
Your instructions are here : \
[https://stackoverflow.com/questions/44346109/how-to-easily-install-and-uninstall-docker-on-macos](https://stackoverflow.com/questions/44346109/how-to-easily-install-and-uninstall-docker-on-macos)

#### PLEASE USE YOUR GOOGLE ACCOUNT TO LOGIN

### 2.1 Test your Docker
Open the terminal ad run these commands :
```
docker version

# Test docker in command prompt
docker run hello-world
# You should get Hello from Docker!
```


#### After installing Docker you need to apply for a Corporate Docker Desktop License

---
