---
title: "Install Open Tofu in Proxmox"
date: 2025-03-15T16:14:01Z
draft: false
---
## Web pages
**[https://opentofu.org/docs/intro/](https://opentofu.org/docs/intro/)** \
**[https://opentofu.org/docs/intro/core-workflow/](https://opentofu.org/docs/intro/core-workflow/)** \
**[https://opentofu.org/docs/intro/install/](https://opentofu.org/docs/intro/install/)**

## Installing using the installer
```bash
wget --secure-protocol=TLSv1_2 --https-only https://get.opentofu.org/install-opentofu.sh -O install-opentofu.sh

# Give it execution permissions:
chmod +x install-opentofu.sh

# Please inspect the downloaded script

# Run the installer:
./install-opentofu.sh --install-method deb
```
## Installing tooling
```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg
```

## Set up the OpenTofu repository
```bash
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://get.opentofu.org/opentofu.gpg | sudo tee /etc/apt/keyrings/opentofu.gpg >/dev/null
curl -fsSL https://packages.opentofu.org/opentofu/tofu/gpgkey | sudo gpg --no-tty --batch --dearmor -o /etc/apt/keyrings/opentofu-repo.gpg >/dev/null
sudo chmod a+r /etc/apt/keyrings/opentofu.gpg /etc/apt/keyrings/opentofu-repo.gpg
```

## Now you have to create the OpenTofu source list.
```bash
echo \
  "deb [signed-by=/etc/apt/keyrings/opentofu.gpg,/etc/apt/keyrings/opentofu-repo.gpg] https://packages.opentofu.org/opentofu/tofu/any/ any main
deb-src [signed-by=/etc/apt/keyrings/opentofu.gpg,/etc/apt/keyrings/opentofu-repo.gpg] https://packages.opentofu.org/opentofu/tofu/any/ any main" | \
  sudo tee /etc/apt/sources.list.d/opentofu.list > /dev/null
sudo chmod a+r /etc/apt/sources.list.d/opentofu.list
```

## Now We Installing OpenTofu
```bash
sudo apt-get update
sudo apt-get install -y tofu
```


 
## Install Golang 1.19
```bash
wget https://go.dev/dl/go1.20.linux-amd64.tar.gz

$ sudo tar -C /usr/local -xzf go1.16.3.linux-amd64.tar.gz

nano ~/.bashrc

# add to file:
export PATH=$PATH:/usr/local/go/bin
```

Test it
```bash
$ go version
```

## 
