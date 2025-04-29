---
title: "Installing Helm 3.11 on a Linux Server"
date: 2023-04-20T07:22:31Z
draft: false
---

Run the following commands
```
# Download Helm
curl -L https://get.helm.sh/helm-v3.11.1-linux-amd64.tar.gz -o helm.tar.gz

# Extract Archive
tar -xvzf helm.tar.gz

# Move to servers bin folder
sudo mv linux-amd64/helm /usr/local/bin/
```

Verify installation
```
helm version
```

K3S Server requirement
```
nano ~/.bashrc
```
Put this ontop of the file
```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
```

Reboot the server
```
sudo reboot
```
