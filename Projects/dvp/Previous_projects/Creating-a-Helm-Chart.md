---
title: "Creating a Helm Chart"
date: 2023-02-01T07:36:17Z
draft: false
---

### What is Helm
Helm is	a package manager for Kubernetes. \
The Helm Chart is what Helm uses for packaging Applications for Kubernetes.

### To deploy a Kubernetes application we  use many YAML files such as: 
1. A PersistentVolumeClaim.yaml file to specify the storage resources needed 
2. A deployment.yaml file to deploy your application
3. A service.yaml file to determine what services can communicate with this Aplication
4. An ingress.yaml file to expose your application to certain ports using a loadbalancer or other mechanism
And more yaml files, you may have secrets stored in a secrets.yaml file etc.. etc..

In windows we used 3 letter file extensions so you will find .yml however that's ancient history use .yaml (even though both work)

### A (Helm) Chart can have:
1. A name
2. A description
3. A version

When we create a Helm Chart then Helm also creates a templates folder in the Chart. \
We will place our Kubernetes Resource yaml files in this folder
 
### Passing Values to your chart
Although we can insert values as parameters to the helm commands allowing reuse, it is more efficient to use a values.yaml file. \
We can customize this file as needed. We can even use this values file to make changes to the current installation of a Helm Chart in your Kubernetes cluster.

## Installation of Helm on Linux
You will find all you need here: \
http://rino.kozow.com/dvp/posts/install-helm-on-linux/

## When working with K3S you need to run this command
```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
```
## Helm Documentation
https://helm.sh/docs/topics/

## Helm Chart Documentation
https://helm.sh/docs/topics/charts/

## 1. Creating a Helm Chart
Go to the folder where you want to keep your Helm Charts \
Assume the name of your Helm Chart is: \
Now run this command : 
```
mkdir myhelmcharts
cd myhelmcharts

helm create myapp
```
This will create a folder myapp

## 2. We need to do some housekeeping
```
cd myapp
cd templates
```
### 2.1 Delete the test folder
```
rm -rf tests/
```
### 2.2 Keep the _helpers.tpl file and delete all the other files.
```
rm *.yaml
rm *.txt

cd ../
```
### 2.3 Empty out the content of the values.yaml file
```
nano values.yaml
```

## 3. To test this Chart
run the folowing command:
```
cd ../../

helm template the-name-of-the-chart the name-of-the-chart-folder

helm template myapp myapp

```


## 4. To install the Chart 

```
helm install myapp myapp
```


###  4.1 To list the installed Helm Charts in the Kubernetes cluster
Every time you deploy helm will create a release which will track the revisions
```
helm list
```

## 5. Setting Values
### 5.1 In the Values.yaml file create a Value category with a value
```
deployment:
  image: ""
  tag: ""
```

## 5.2 Use the above value in a Kubernetes Resource
Assume we want to use this replace a hardcoded value in the deployment.yaml file: \
In the deployment.yaml insert Go Language Insert {{ }}

```
image: "{{.Values.deployment.image}}":"{{.Values.deployment.tag}}"
```

## 6 Upgrading the installed Helm Chart
This allows us to make changes to the instaled Chart by placing changed values in the values.yaml file and using the helm upgrade command

```
helm upgrade the-name-of-the-chart the name-of-the-chart-folder --values /path/to/values/file/values.yaml

helm upgrade myapp myapp --values values.yaml

```

### 6.1 We should find a new Revision when we run helm list

```
helm list
```

## Bibliography
https://www.youtube.com/watch?v=5_J7RWLLVeQ&t=11s
