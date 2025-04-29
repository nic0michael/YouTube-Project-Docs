---
title: "Creating Helm Charts"
date: 2023-04-22T06:41:11Z
draft: false
---
```
sudo su -

cd /root/k3s/k3s-helm-charts/creating-helm-charts
```

## 1. Install tree
```
yum install tree

apt install tree
```

## 2.Generate the Scafolding for your own Helm Chart
We will create the **nicos-nginx-helm** helm Chart
run this command
```
helm create nicos-nginx-helm

# now view what was generated
tree

```

We now have :
```
.
└── nicos-nginx-helm
    ├── charts
    ├── Chart.yaml
    ├── templates
    │   ├── deployment.yaml
    │   ├── _helpers.tpl
    │   ├── hpa.yaml
    │   ├── ingress.yaml
    │   ├── NOTES.txt
    │   ├── serviceaccount.yaml
    │   ├── service.yaml
    │   └── tests
    │       └── test-connection.yaml
    └── values.yaml

```

## 3. leave the Charts folder empty for now
For now you dont need to do anything here\
This is where you insert Helm Charts if your Helm Chart has any dependencies on other Heml Charts wnen your application becomes more advanced


## 4. Open the templates Folder
```
cd /root/k3s/k3s-helm-charts/creating-helm-charts/nicos-nginx-helm/templates

ls -la
```

Delete these files and also the test folder
```
rm -rf tests
rm *.yaml
rm NOTES.txt
```
**Do note delete file : _helpers.tpl**

## 5. Add your Manifests here

Create the Deployment Manifest
```
nano nginx-deployment.yaml
```
Put this into the file:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 4
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

Create the Service manifest
```
nano nginx-service.yaml
```
Put this into the file:
```
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    run: nginx
spec:
  ports:
    - port: 80
  selector:
    app: nginx

```

Create n Ingress Manifest to expose the service
```
nano service-exposed.yaml
```
Put this into the file
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx
  annotations:
    ingress.kubernetes.io/ssl-redirect: "false"
spec:
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx
            port:
              number: 80

```



## 6. Edit and empty the values.yaml file and customise this file
```
nano values.yaml
```
Customize this file with the folowing content
```
replicaCount: 4

image:
  repository: nginx
  tag: 1.14.2

```

## 7. Edit the Deployment Manifest
```
nano nginx-deployment.yaml
```

Make these changes
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
#  replicas: 4
  replicas: {{ .Values.replicaCount}}
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
#        image: nginx:1.14.2
        image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
        ports:
        - containerPort: 80

```

## 8. Install the Helm chart
Open the parent folder
```
cd /root/k3s/k3s-helm-charts/creating-helm-charts/
```
### 8.1 Create the Namespace nginx-nico
```
kubectl create namespace nginx-nico
```


### 8.2 Apply the helm chart
```
helm install nginx-release nicos-nginx-helm/ -n nginx-nico
```

### 8.3 Verify Deployment
```
helm list -n nginx-nico

kubectl get all -o wide -n nginx-nico

```

## 9. Create a new NOTES.txt file 
```
nano nicos-nginx-helm/templates/NOTES.txt
```

put this into the file
```
#  Put Your Notes here
#
#  open your browser to :
#

http://10.154.2.89
http://10.154.2.93/
http://10.154.2.95/
http://10.154.2.97/
```
## 10. Upgrading the Helm Chart

### 10.1 Edit and make changes to the values.yaml file
```
nano nicos-nginx-helm/values.yaml
```
Change the replicaCount to 5

### 10.2 Apply the update t the Helm Chart
```
helm upgrade nginx-release nicos-nginx-helm/ --values nicos-nginx-helm/values.yaml  -n nginx-nico
```

### 10.3 Verify Deployment
```
helm list -n nginx-nico

kubectl get all -o wide -n nginx-nico

```
## 11 View Heml Chart Installation/Upgrade History
```
helm list -n nginx-nico

# display the history for release = nginx-release
helm history nginx-release  -n nginx-nico
```

## 12 Rollback to previous revision
```
# Rollback to Revision 1
helm rollback nginx-release 1 -n nginx-nico

helm history nginx-release  -n nginx-nico
```
 expect to get
```
helm history nginx-release  -n nginx-nico
REVISION	UPDATED                 	STATUS    	CHART                 	APP VERSION	DESCRIPTION     
1       	Sat Apr 22 17:56:32 2023	superseded	nicos-nginx-helm-0.1.0	1.16.0     	Install complete
2       	Sat Apr 22 18:17:39 2023	superseded	nicos-nginx-helm-0.1.0	1.16.0     	Upgrade complete
3       	Sat Apr 22 18:44:57 2023	superseded	nicos-nginx-helm-0.1.0	1.16.0     	Upgrade complete
4       	Sat Apr 22 18:52:03 2023	superseded	nicos-nginx-helm-0.1.0	1.16.0     	Upgrade complete
5       	Sat Apr 22 18:53:51 2023	superseded	nicos-nginx-helm-0.1.0	1.16.0     	Upgrade complete
6       	Sat Apr 22 19:09:57 2023	superseded	nicos-nginx-helm-0.1.0	1.16.0     	Upgrade complete
7       	Sat Apr 22 19:17:56 2023	superseded	nicos-nginx-helm-0.1.0	1.16.0     	Upgrade complete
8       	Sat Apr 22 19:20:11 2023	superseded	nicos-nginx-helm-0.1.0	1.16.0     	Upgrade complete
9       	Sat Apr 22 19:36:22 2023	superseded	nicos-nginx-helm-0.1.0	1.16.0     	Upgrade complete
10      	Sat Apr 22 20:07:16 2023	deployed  	nicos-nginx-helm-0.1.0	1.16.0     	Rollback to 1 
```

## The cleanup
```
helm delete nginx-release -n nginx-nico

# If you have PV and PVC Remove alk Kubernetes objects in the namespace 
kubectl delete namespace nginx-nico
```
