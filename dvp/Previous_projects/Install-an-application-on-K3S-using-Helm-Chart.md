---
title: "Install an Application on K3S Using Helm Chart"
date: 2022-12-29T11:20:22Z
draft: false
---

## These are some of JAMLs we need to provide to manage the deployment of Applications in Kubernetes (K3S) 
1. **For the Deployment:** \
deployment.yaml \
This is used to inform kubernetes how to deploy the application and what version of the app to deploy and how many instances(replicas) of the application to deploy.
2. **For the Applications Configuration:** \
configmap.yaml \
This is used to keep your applications configuration.
3. **For the provisioning of Storage:** \
persistentvolume.yaml \
When we need storage for the application we would use a persistentVolume and create this yaml file to tell Kubernetes how to provision the required storage facilities.
4. **For defining the service:** \
service.yaml \
This is used to inform kubernetes how to expose the application, in other words what ports need to me made available so other applications can work with this a>.

5. **For providing Credentials (Secrets):** \
secrets.yaml \
This is used to inform kubernetes what secrets to use.


## The issues regarding the use of these YAML files
1. There are more YAML files that can be provided, the above lists the most commonly used YAML files.
2. These files are for a specific deployment not resusable.
3. You would need to make changes for all these files to provide what your specific deployment will require.
4. Eventually you land up with so many YAMLs that it takes a lot of effort to manage them.

**By Using Helm Charts we can get the developers of the applications to provide most of the above in what is known as a Helm Chart.**


## So What is Helm?
Helm can be regarded as the Kubernetes package manager for deploying applications.

## The Helm Chart
The developers of applications provide a Helm Chart which is used to deploying applications to Kubernetes.

## Using a Helm Chart with your own values
1. Helm gives us a mechanism for injecting values as parameters.
2. We can use a values.yaml file to provide more values overcomming the limitation of using parameters.
3. By using different YAML files or parameters we now can reuse our Helm Charts making multiple deployments with different values, and even in the same Kubernetes Cluster.

## The Helm Charts Documentation
https://helm.sh/docs/topics/charts/

## Testing a Helm Chart
We would use the folowing comand to test a Helm Chart 
```
helm template your-helm-chart your-helm-chart-folder
helm template your-helm-chart values=your-value-yaml-file
```

## Installing (Deploying) a Helm Chart
We would use the folowing comand to install a Helm Chart 
```
helm install your-helm-chart your-helm-chart-folder
helm install your-helm-chart values=your-value-yaml-file
```

## Upgrading (Updating) your Helm Chart Instalation
This is similar to Install but changes values for existing Deployment \
you would run this command to upgrade a Helm Chart
```
helm install your-helm-chart --version 1.0.1 your-helm-chart-folder
helm install your-helm-chart values=your-value-yaml-file
```

## Working with Helm Chart Repositories
If you tried to install the zookeeper Helm chart you may get an Error as you may not have added the BitNami repository \
You can look at: \
https://artifacthub.io/packages/search

searching for  nginx brings you here: \
https://artifacthub.io/packages/helm/bitnami/nginx \
This one uses the Bitnami Repo Maintained by (Novel) VMware

## adding the Bitnami Helm Chart Repository
```
helm repo add my-bitnami-repo https://charts.bitnami.com/bitnami
```

## Now we can work with Zookeeper from Bitnami

### Testing the Zookeeper Helm Chart
```
helm template zookeeper-proj bitnami/zookeeper --version 10.2.3 --values=zookeeper-values.yaml
```

### Installing (Deploying) the Zookeeper Helm Chart
```
helm install zookeeper-proj bitnami/zookeeper --version 10.2.3 --values=zookeeper-values.yaml
```

### Upgrading (Updating) the Zookeeper Helm Chart Instalation
```
helm install zookeeper-proj bitnami/zookeeper --version 10.2.2 --values=zookeeper-values.yaml
```

