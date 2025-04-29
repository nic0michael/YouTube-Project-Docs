---
title: "Installing GraalVM on Linux"
date: 2023-11-18T08:18:03Z
draft: false
---

## 1. Download the Archive  File
Navigate to: \
https://github.com/graalvm/graalvm-ce-builds/releases 

### 1.1 For JDK21
If you are using JDK21 download this file: \
https://github.com/graalvm/graalvm-ce-builds/releases/download/jdk-21.0.0/graalvm-community-jdk-21.0.0_linux-x64_bin.tar.gz
Instructions can be found here: \
https://www.graalvm.org/latest/docs/getting-started/linux/

### 1.2 For JDK17
If you are using JDK17 download this file: \
https://github.com/graalvm/graalvm-ce-builds/releases/download/jdk-17.0.9/graalvm-community-jdk-17.0.9_linux-x64_bin.tar.gz
Instructions can be found here: \
https://www.graalvm.org/23.0/docs/getting-started/linux/

## 2. Installing GraalVM from the downloaded file
```
sudo su -
mkdir /opt/graalvm
cd /opt/graalvm
mkdir /opt/graalvm/graalvm17
mkdir /opt/graalvm/graalvm21
cd /opt/graalvm/graalvm17 
cd /opt/graalvm/graalvm21 
# Copy downloaded archive file here (we downloaded this file: graalvm-community-jdk-17.0.9_linux-x64_bin.tar.gz)
```
Unzip the archive to your file system:
```
# tar -xzf <graalvm-archive>.tar.gz
tar -xzf graalvm-community-jdk-17.0.9_linux-x64_bin.tar.gz
```
## 3. Configure your environment:
### 3.1 Configure for JDK17
```
nano .bashrc
```
Add this to the file"
```
GRAALVM_17_HOME='/opt/graalvm/graalvm17/graalvm-community-openjdk-17.0.9+9.1'
GRAALVM_HOME="$GRAALVM_17_HOME"
PATH="$GRAALVM_HOME/bin:$PATH"
export PATH
export GRAALVM_HOME
```

### 3.2 Configure for JDK21
```
nano .bashrc
```
Add this to the file"
```
GRAALVM_21_HOME='/opt/graalvm/graalvm21/graalvm-community-openjdk-21+35.1'
GRAALVM_HOME="$GRAALVM_21_HOME"
PATH="$GRAALVM_HOME/bin:$PATH"
export PATH
export GRAALVM_HOME
```

### 3.3 Configure for switching between JDK17 and JDK21
```
nano .bashrc
```
Add this to the file"
```
GRAALVM_17_HOME='/opt/graalvm/graalvm17/graalvm-community-openjdk-17.0.9+9.1'
GRAALVM_21_HOME='/opt/graalvm/graalvm21/graalvm-community-openjdk-21+35.1'

# We will switch to JDK17
GRAALVM_HOME="$GRAALVM_17_HOME"
#GRAALVM_HOME="$GRAALVM_21_HOME"

PATH="$GRAALVM_HOME/bin:$PATH"
export PATH
export GRAALVM_HOME
```
