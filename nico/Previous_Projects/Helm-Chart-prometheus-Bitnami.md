---
title: "Prometheus Helm Chart using the Prometheus-community repository"
date: 2023-04-20T13:47:59Z
draft: false
---

**Login to the Master Node/Server**
```
ssh nickm@10.154.2.90

sudo su -

cd /root/k3s/k3s-helm-charts/prometheus/
```

### If you have not installed Helm 3.11 yet do this:
**[**Installing Helm 3.11 on a Linux Server**](http://rino.kozow.com/nico/posts/helm-chart-install-helm/)**

## 1. When working with K3S you need to run this command

```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
```
### 1.1 You can edit the .bashrc file put this on the top
```
nano ~/.bashrc
```

Reboot the server
```
sudo reboot
```

## 2. Find the Helm Chart in the Repository
**You can search for Helm Charts here:** \
[https://artifacthub.io/](https://artifacthub.io/)

## 3. Search for Prometheus

**Searching for prometheus brings you here:** \
This one uses the Bitnami Repository Maintained by (Novel) VMware
[https://artifacthub.io/packages/helm/prometheus-community/prometheus](https://artifacthub.io/packages/helm/prometheus-community/prometheus) \


## 4. Adding the Repiository locally (Bitnami)
1. Click on the Install Button
2. Copy the command under "Add Repository" section
Expect to get
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```
run the above command on the server

3. close the Pop Up that was launched when you pressed the Install Button

## 5. Creating the Values Yaml file
1. Click on the Default Values Button
2. Click on the button with a icon of a Cloud
It will try to download the folowing file:
```
values-prometheus.yaml
```
3. copy this file name and create a file on the server with that file name
```
nano values-prometheus.yaml
```

4. close the download Pop Up by pressing the Cancel Button
5. Click on the button with an icon with two squares
6. this will copy the content of the Yaml file to your clipboard
7. paste this content into your text editor and save it
8. In the browser click the close Button

## 6. Create the namespace
run these commands
```
kubectl get namespaces
kubectl create namespace nico-prometheus

kubectl get namespaces
```
## 7. Get the Helm Chart Install command
1. Click on the Install Button
2. Copy the command under "Install chart" section
Expect to get
```
helm install my-prometheus prometheus-community/prometheus --version 20.2.1
```

## 7. Testing the Helm Chart 
### Test 1
1. Take the above command and change the word install to template
you should now have
```
helm template my-prometheus-release prometheus-community/prometheus --version 20.2.1
```
Now run the above command and expect to not have errors

### Test 2
2. add the following to the end of the above command: -f values-prometheus.yaml
3. you should now have
```
helm template my-prometheus-release prometheus-community/prometheus --version 20.2.1 -f values-prometheus.yaml 
```

run the above command and expect to not have errors

## 8. Installing the Helm Chart (Deploying)
1. take the above command change the word template to install and add the folowing to the end : --namespace nico-prometheus
2. you should nw have:
```
helm install my-prometheus-release prometheus-community/prometheus --version 20.2.1 -f values-prometheus.yaml --namespace nico-prometheus
```
3. run the above command

## 9. Verifying the deployment
1. run this command:
```
helm list -n nico-prometheus

helm list --all-namespaces
```

2. run this command
```
kubectl get all -o wide -n nico-prometheus
```

## 10. Updating the values yaml file
Whenever you need to make changes to your deployment
1. run this command and make your changes
```
nano values-prometheus.yaml
```

### 10.1  Test your changes
1. Run this command:
```
helm template my-prometheus-release prometheus-community/prometheus --version 20.2.1 -f values-prometheus.yaml
```

### 10.2 Upgrade your Helm Chart to implement these Updates
1. Run this command:
```
helm upgrade my-prometheus-release prometheus-community/prometheus --version 20.2.1 -f values-prometheus.yaml --namespace nico-prometheus
```

### 10.3 Verifying the deployment
1. run this command:
```
helm list -n nico-prometheus
```

2. run this command
```
kubectl get all -o wide -n nico-prometheus
```
## 11. The Cleanup 
run these commands
```
helm delete my-prometheus-release -n nico-prometheus
helm list --all-namespaces

kubectl get namespaces

kubectl delete namespace nico-prometheus
kubectl get namespaces

helm list --all-namespaces
kubectl get all --all-namespaces
```
