---
title: "Install K3s on Centos"
date: 2022-12-27T10:55:23Z
draft: False
---
You will need to disable the firewall by running this command
```
sudo systemctl disable firewalld --now
```

There are 3 types on K3S installations you can do
1. A K3S Single Node (server) Installation
2. A K3S Master and three Worker Nodes(servers) Installation
3. A K3S HA (High Availability Cluster) three Master and three Worker Nodes(servers) installation

[The  K3S Single Node (server) Installation](http://rino.kozow.com/dvp/posts/tomcat-on-a-single-instance-of-k3s/)
This article gives you the instructions to do that

[The  K3S Master and three Worker Nodes(servers) Installation](https://youtu.be/pU9_GQn6WX0)
This is a youTube video with unique way of doing this

[The K3S HA (High Availability Cluster) three Master and three Worker Nodes(servers) installation]()


