---
title: "K3S Notes"
date: 2023-03-27T08:09:42Z
draft: false
---

## 1. Errors running Helm or Kubectl commands on K3S
You will get this error:
```
Error: Kubernetes cluster unreachable: Get "http://localhost:8080/version?timeout=32s": dial tcp 127.0.0.1:8080: connect: connection refused
```

Edit the .bashrc file:
```
sudo su -
nano ~/.bashrc
```

put this in the top of this file:
```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
```

## 2. Installing K3S Clusters and K3S-HA Clusters
We have created two Shell Scripts:
1. The Ubuntu Shell Script: \
https://github.com/nic0michael/K3S-HA-tutorial/blob/master/CreateHA-ClusterDeploymentCommands4Ubuntu.sh
2. The Centos/RedHat Shell Script: \
https://github.com/nic0michael/K3S-HA-tutorial/blob/master/CreateHA-ClusterDeploymentCommandsRedHatREL.sh

Instructions can be found here: \
https://github.com/nic0michael/K3S-HA-tutorial

And a YouTube tutorial here: \
https://www.youtube.com/watch?v=pU9_GQn6WX0

