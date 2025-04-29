---
title: "Helm Chart Install Helm on Server"
date: 2023-05-06T08:04:23Z
draft: false
---
## 1. Deploying Helm to your server
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

**In some cases you get this error :**
```
Error: INSTALLATION FAILED: execution error at (wikijs/templates/common.yaml:1:3): Operator [cloudnative-pg] has to be installed first
```
Run these commands : 
```
kubectl create namespace wiki
kubectl apply -f https://raw.githubusercontent.com/cloudnative-pg/cloudnative-pg/release-1.17/releases/cnpg-1.17.5.yaml -namespace=cnpg-system

# Verify this with
kubectl get deploy cnpg-controller-manager --namespace=cnpg-system

```

## 2. Installing a helm Chart
### 2.1 The Bitnami Docuwiki HelmChart
https://bitnami.com/stack/dokuwiki/helm \
https://artifacthub.io/packages/helm/bitnami/dokuwiki

**Install Helmchart :**
Run these commands
```
kubectl create namespace wiki

helm repo add bitnami https://charts.bitnami.com/bitnami

helm install my-dokuwiki-release bitnami/dokuwiki --version 14.2.2 -n wiki
```

**Expect to get: **
```
NAME: my-dokuwiki-release
LAST DEPLOYED: Thu Sep 28 06:12:58 2023
NAMESPACE: wiki
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
CHART NAME: dokuwiki
CHART VERSION: 14.2.2
APP VERSION: 20230404.1.0

** Please be patient while the chart is being deployed **

1. Get the DokuWiki URL by running:

** Please ensure an external IP is associated to the my-dokuwiki-release service before proceeding **
** Watch the status using: kubectl get svc --namespace wiki -w my-dokuwiki-release **

  export SERVICE_IP=$(kubectl get svc --namespace wiki my-dokuwiki-release --template "{{ range (index .status.loadBalancer.ingress 0) }}{{ . }}{{ end }}")
  echo "URL: http://$SERVICE_IP/"

2. Login with the following credentials

  echo Username: user
  echo Password: $(kubectl get secret --namespace wiki my-dokuwiki-release -o jsonpath="{.data.dokuwiki-password}" | base64 -d)
```



**List helm charts :**
```
helm list -n wiki
```
Expect to get :
```
NAME               	NAMESPACE	REVISION	UPDATED                                	STATUS  	CHART          	APP VERSION 
my-dokuwiki-release	wiki     	1       	2023-09-28 06:12:58.421081188 -0400 EDT	deployed	dokuwiki-14.2.2	20230404.1.0

```
Run these commands
```
kubectl get all -o wide -n wiki
helm history my-dokuwiki-release -n wiki
```


**Cleanup :**
```
helm delete my-dokuwiki-release -n wiki

kubectl delete namespace wiki

kubectl get all --all-namespaces
```

**Find Helm Charts here:** \
https://artifacthub.io/ 


**Using Helm Charts** \
http://rino.kozow.com/kubernetes/posts/kubernetes-lesson-3-kubectl/

**To create your own Helm Charts:** \
http://rino.kozow.com/kubernetes/posts/kubernetes-lesson-4/


## Bibliography
https://cloudnative-pg.io/documentation/1.17/installation_upgrade/
