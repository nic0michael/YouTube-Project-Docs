---
title: "Install Zookeeper on K3S Using Helm Chart"
date: 2022-12-29T09:20:19Z
draft: false
---
## 1. Install Helm
**Find the latest release of Helm** \
https://github.com/helm/helm/releases

**This is currently the latest release:** \
https://get.helm.sh/helm-v3.10.3-linux-amd64.tar.gz

***Download this release:** \
```
wget https://get.helm.sh/helm-v3.10.3-linux-amd64.tar.gz
```

**Extract the Helm archive:** \
```
tar xvf helm-v3.10.3-linux-amd64.tar.gz
```

**Move the extracted file to /usr/local/bin/** \
```
sudo su -
mv linux-amd64/helm /usr/local/bin
```

## 2. Finding a Helm Chart in the Repository
**You can search for Helm Charts here:** \
https://artifacthub.io/packages/search

**Searching for  zookeeper brings you here:** \
https://artifacthub.io/packages/helm/bitnami/zookeeper \
This one uses the Bitnami Repo Maintained by (Novel) VMware

**When pressing the INSTALL button** this page also provides the following installation information \
**Add repository** \
helm repo add bitnami https://charts.bitnami.com/bitnami \
**Install chart** \
helm install my-zookeeper bitnami/zookeeper --version 11.0.2 \
**Change this to:** \
helm install zookeeper-proj bitnami/zookeeper --version 11.0.2

**When pressing the TEMPLATES button** you can view all the templates in this Helm Chart 

**When pressing the DEFAULT VALUES button** you get all the values you can put in your values.yaml file to customise the Installation

## 3. Add the Bitnami Helm Chart Repository
```
helm repo add my-bitnami-repo https://charts.bitnami.com/bitnami
```


## 4. Create YAML file zookeeper-values.yaml:
```
nano zookeeper-values.yaml
```

### Put this in the file:
```
image:
  registry: docker.io
  repository: bitnami/zookeeper
  tag: 3.8.0-debian-11-r47
heapSize: 2048
auth:
  client:
    enabled: false
    clientUser: "zookeeper"
    clientPassword: "ZSWjAwazMzcDNy6BVR"
    serverUsers: "zookadmin"
    serverPasswords: "ZSWjAwa2FkbTFu6BVR"
```

## 5. Test if deployment is valid
Run these commands 
```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
helm template zookeeper-proj bitnami/zookeeper --version 10.2.3
helm template zookeeper-proj bitnami/zookeeper --version 10.2.3 --values=zookeeper-values.yaml
# For the latest version
helm template zookeeper-proj bitnami/zookeeper --version 11.0.2
```


## 6. Now Deploy Zookeeper in K3S Cluster
Run these commands

export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
helm install zookeeper-proj bitnami/zookeeper --version 10.2.3
# For the latest version
helm install zookeeper-proj bitnami/zookeeper --version 11.0.2

Expect this output:
```
NAME: zookeeper-proj
LAST DEPLOYED: Fri Dec 30 08:22:30 2022
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
CHART NAME: zookeeper
CHART VERSION: 11.0.2
APP VERSION: 3.8.0

** Please be patient while the chart is being deployed **

ZooKeeper can be accessed via port 2181 on the following DNS name from within your cluster:

    my-zookeeper.default.svc.cluster.local

To connect to your ZooKeeper server run the following commands:

    export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=zookeeper,app.kubernetes.io/instance=my-zookeeper,app.kubernetes.io/component=zookeeper" -o jsonpath="{.items[0].metadata.name}")
    kubectl exec -it $POD_NAME -- zkCli.sh

To connect to your ZooKeeper server from outside the cluster execute the following commands:

    kubectl port-forward --namespace default svc/my-zookeeper 2181:2181 &
    zkCli.sh 127.0.0.1:2181
```

## 7. Upgrade(to update) your installation
```
helm upgrade zookeeper-proj bitnami/zookeeper --version 10.2.3 --values=zookeeper-values.yaml
# For the latest version
helm upgrade zookeeper-proj bitnami/zookeeper --version 11.0.2 --values=zookeeper-values.yaml
```
Expect this output
```
Release "zookeeper-proj" has been upgraded. Happy Helming!
NAME: zookeeper-proj
LAST DEPLOYED: Fri Dec 30 08:29:17 2022
NAMESPACE: default
STATUS: deployed
REVISION: 2
TEST SUITE: None
NOTES:
CHART NAME: zookeeper
CHART VERSION: 11.0.2
APP VERSION: 3.8.0

** Please be patient while the chart is being deployed **

ZooKeeper can be accessed via port 2181 on the following DNS name from within your cluster:

    my-zookeeper.default.svc.cluster.local

To connect to your ZooKeeper server run the following commands:

    export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=zookeeper,app.kubernetes.io/instance=my-zookeeper,app.kubernetes.io/component=zookeeper" -o jsonpath="{.items[0].metadata.name}")
    kubectl exec -it $POD_NAME -- zkCli.sh

To connect to your ZooKeeper server from outside the cluster execute the following commands:

    kubectl port-forward --namespace default svc/my-zookeeper 2181:2181 &
    zkCli.sh 127.0.0.1:2181
```

## 8. Get aditional information from the Helm Installation
Run these commands
```
kubectl get all
kubectl get pod -o yaml
kubectl get endpoints
get service zookeeper-proj -o yaml
kubectl get service zookeeper-proj-headless -o yaml
```

## 9. To get all the Zookeeper Helm Chart Templates
After searching for Zookeeper in the Repository you will find this URL: \
https://artifacthub.io/packages/helm/bitnami/zookeeper \
On Pressing the TEMPLATES button you can now dowload all the templates \
Click on the template and press the download button (its the one with the cloud)
```
https://artifacthub.io/packages/helm/bitnami/zookeeper?modal=template&template=configmap.yaml \
https://artifacthub.io/packages/helm/bitnami/zookeeper?modal=template&template=extra-list.yaml \
https://artifacthub.io/packages/helm/bitnami/zookeeper?modal=template&template=metrics-svc.yaml \
https://artifacthub.io/packages/helm/bitnami/zookeeper?modal=template&template=networkpolicy.yaml \
https://artifacthub.io/packages/helm/bitnami/zookeeper?modal=template&template=pdb.yaml \
https://artifacthub.io/packages/helm/bitnami/zookeeper?modal=template&template=prometheusrule.yaml \
https://artifacthub.io/packages/helm/bitnami/zookeeper?modal=template&template=scripts-configmap.yaml \
https://artifacthub.io/packages/helm/bitnami/zookeeper?modal=template&template=secrets.yaml \
https://artifacthub.io/packages/helm/bitnami/zookeeper?modal=template&template=serviceaccount.yaml \
https://artifacthub.io/packages/helm/bitnami/zookeeper?modal=template&template=servicemonitor.yaml \
https://artifacthub.io/packages/helm/bitnami/zookeeper?modal=template&template=statefulset.yaml \
https://artifacthub.io/packages/helm/bitnami/zookeeper?modal=template&template=svc-headless.yaml \
https://artifacthub.io/packages/helm/bitnami/zookeeper?modal=template&template=svc.yaml \
https://artifacthub.io/packages/helm/bitnami/zookeeper?modal=template&template=tls-secrets.yaml \
https://artifacthub.io/packages/helm/bitnami/zookeeper?modal=template&template=_helpers.tpl

And Download the values-zookeeper.yaml file:
https://artifacthub.io/packages/helm/bitnami/zookeeper?modal=values
```
Download all these helm chart template yaml files and place them in folder /helm-charts/zookeeper \
Now you have full access to these yaml file

## 10. Installing Zookeeper from these yaml files

### Test this installation
```
helm template zookeeper-proj /helm-charts/zookeeper
```
### Install Zookeeper in K3S Cluster
```
helm install zookeeper-proj /helm-charts/zookeeper
```
### Upgrade (update) this installation
```
helm upgrade zookeeper-proj /helm-charts/zookeeper --version 10.2.3 --values=/helm-charts/zookeeper/zookeeper-values.yaml
# For the latest version
helm upgrade zookeeper-proj /helm-charts/zookeeper --version 11.0.2 --values=/helm-charts/zookeeper/zookeeper-values.yaml
```


## 11. Delete Helm Installation
```
helm list
```
Expect to get this output:
```
NAME        	NAMESPACE	REVISION	UPDATED                                	STATUS  	CHART           	APP VERSION
zookeeper-proj	default  	2       	2022-12-30 08:29:17.123724296 +0000 UTC	deployed	zookeeper-11.0.2	3.8.0 
```

Now run this command
```
helm delete zookeeper-proj
```
Expect to get this output:
```
release "zookeeper-proj" uninstalled
```
