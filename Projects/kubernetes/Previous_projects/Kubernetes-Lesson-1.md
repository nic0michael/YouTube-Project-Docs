---
title: "Kubernetes Lesson 1"
date: 2023-04-07T10:43:06Z
draft: false
---
**We are going to learn Kubernetes looking at how Manifests can simplify the deployment of applications in the Kubernetes Cluster**

## 1. What you need to be able to do this at home?
You need two Linux Servers \
Ideally Ubuntu Server VMs \
If you are not familiar with Hardware Virtualization please watch this video \
**[Explaining Hardware Virtualization](https://www.youtube.com/watch?v=4c_OILm4tf0)** 

Now you can use Virtualbox and create Virtual machines but even better use Proxmox

### 1.1 Why not setup a cheap Homelab using Proxmox?
If you are serious about learning Kubernetes you may want to create different clusters \
Proxmox is a category 1 Hypervisor (VMs run on Bare Metal) where as Virtualbox is only a Category 2 Hypervisor \
We recomend you find a second hand DEL PC with an Intel i7 CPU and upgrade its memory to 24 or 32GB RAM 

Now watch these this video: \
**[Add a Production Grade Kubernetes Cluster to your Proxmox home lab Part 1](https://www.youtube.com/watch?v=MsHewlV75SA)**

## 2. What is a Kubernetes Manifest?
**A manifest is a YML or a YAML file that simplifies the deployment of an Application in the Kubernetes Cluster** 

## Demo 1 the deployment of the Nginx Webserver using 3 Manifests
The procedure for this demo can be found here: \
**[Nginx Test Deployment](http://rino.kozow.com/kubernetes/posts/nginx-test-deployment/)**

## Demo 2 the deployment of Grafana using Manifestes

**[Grafana Deployment Using Manifest](http://rino.kozow.com/kubernetes/posts/grafana-deployment-using-manifest/)**

## Further examples of applications that you can deploy in your Cluster
[Prometheus Deployment Using Manifest](http://rino.kozow.com/kubernetes/posts/prometheus-deployment-using-manifest/) \
[Zookeeper Deployment Using Manifest](http://rino.kozow.com/kubernetes/posts/zookeeper-deployment-using-manifest/) \
[Artemis Deployment Using Manifest](http://rino.kozow.com/kubernetes/posts/artemis-deployment-using-manifest/) \

### Now we are going to look at K3S Kubernetes also at Kubernetes Manifests in greater details

[kubernetes-lesson1-explaining-the-kubernetes-manifests/](http://rino.kozow.com/kubernetes/posts/kubernetes-lesson1-explaining-the-kubernetes-manifests/)
