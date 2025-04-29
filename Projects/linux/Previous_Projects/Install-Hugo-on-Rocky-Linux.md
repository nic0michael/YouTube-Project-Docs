---
title: "Install Hugo on Rocky Linux"
date: 2023-10-03T13:15:48Z
draft: false
---

## Install Hugo on Rocky Linux
sudo su -

https://github.com/gohugoio/hugo/releases/download/v0.119.0/hugo_0.119.0_Linux-64bit.tar.gz
Run these commands
```
mkdir /opt/hugo/
cd /opt/hugo

wget https://github.com/gohugoio/hugo/releases/download/v0.119.0/hugo_0.119.0_Linux-64bit.tar.gz

tar -xvzf hugo_0.119.0_Linux-64bit.tar.gz

./hugo version
```

Expect:
```
./hugo version
hugo v0.119.0-b84644c008e0dc2c4b67bd69cccf87a41a03937e linux/amd64 BuildDate=2023-09-24T15:20:17Z VendorInfo=gohugoio
```
Copy to bin folder
```
cp hugo /usr/bin/

cd ../
hugo version
```
