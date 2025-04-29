---
title: "The Helm Chart Repository ArtifactHub Tour"
date: 2023-04-22T06:53:26Z
draft: false
---

## The Helm Chart in the Repository
**You can search for Helm Charts here:** \
**[https://artifacthub.io/](https://artifacthub.io/)**


##  Searching for a Helm Chart
**Lets search for wordpress**

Prefer to use Bitnami Helm Charts these are official Helm Charts maintained by VMware

## Viewing all the Manifests
The **TEMPLATES button** is used to view all the Manifests \
You will notice that values are replaced with GO Lang variables: \
In the **ingress.yaml** file you will find:
```
{{- if .Values.ingress.enabled }}
apiVersion: {{ include "common.capabilities.ingress.apiVersion" . }}
```

In the **deployment.yaml** file you will find:
```
apiVersion: {{ include "common.capabilities.deployment.apiVersion" . }}
```

You will also find
```
      containers:
        - name: wordpress
          image: {{ include "wordpress.image" . }}
```

## The Values
The **DEFAULT VALUES button** is used to vlew all the values set accross all the manifests by the **values-wordpress.yaml** file. 

You would find these values:

```
ingress:
  ## @param ingress.enabled Enable ingress record generation for WordPress
  ##
  enabled: false
  ## @param ingress.pathType Ingress path type
  ##
  pathType: ImplementationSpecific
  ## @param ingress.apiVersion Force Ingress API version (automatically detected if not set)
```

And also these values:
```
image:
  registry: docker.io
  repository: bitnami/wordpress
  tag: 6.2.0-debian-11-r11
```


## The Values YAML file
The **DEFAULT VALUES button** is used to to download the values-wordpress.yaml file \
Or to copy its content to the Clipboard


## Adding the Helm Charts Repository
The **INSTALL button** is used to get the Add Repository command:
```
helm repo add bitnami https://charts.bitnami.com/bitnami
```

## Installing the Helm Chart 
The **INSTALL button** is used to get the helm Install command:
```
# Dont run this command as is
helm install my-wordpress bitnami/wordpress --version 16.0.0 
```
## Add a namespace to the Helm Install command
```
kubectl create namespace nico-wordpress

# Run the Helm Install command with a Namespace parameter
helm install my-wordpress bitnami/wordpress --version 16.0.0 --namespace nico-wordpress

```

## The Cleanup
```

# This will not remove the PVC and PV (Perststent Volume Claim) 
helm delete my-wordpress


# To delete all undeleted Objects delete the namespace
kubectl delete namespace nico-wordpress
```
  
