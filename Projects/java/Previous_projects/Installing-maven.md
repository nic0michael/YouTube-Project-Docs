---
title: "Installing Maven"
date: 2023-07-09T11:32:42Z
draft: true
---

## Windows Installation
Google search : maven download

download maven from : \
https://maven.apache.org/download.cgi

Download: \
Binary zip archive:  apache-maven-3.9.3-bin.zip
extract this zip file inside a suitable folder: \
C:\Users\nico\

In the Windows Search Box: Search for"
```
environment variables
```
press Environment Variables button

In the System variables section
press the New... button

in the Variable Name field
name the variable Maven

in the Variable value field
put the path to the maven folder

Edit the Path  environment variable

add this to the path
```

```

in the command Promt run this command
```
mvn --version
```
expect something like this:
```

```

## Linux Installation
change the ownership of the /opt folder to your user
```
sudo chown nickm: /opt
```

Google search : maven download

download maven from : \
https://maven.apache.org/download.cgi

Download: \
Binary zip archive:  apache-maven-3.9.3-bin.zip \
or \
Source tar.gz archive   apache-maven-3.9.3-src.tar.gz

```
cd /opt
wget https://dlcdn.apache.org/maven/maven-3/3.9.3/source/apache-maven-3.9.3-src.tar.gz
```
extract this zip file
```
tar -xvzf apache-maven-3.9.3-src.tar.gz
ls -la

```
edit the .bashrc file
```
cd ~\
nano .bashrc
```
put this in the file

/opt
