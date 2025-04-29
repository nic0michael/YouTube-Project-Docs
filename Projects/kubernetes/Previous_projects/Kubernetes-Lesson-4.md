---
title: "Kubernetes Lesson 4"
date: 2023-09-28T06:59:43Z
draft: false
---
## Creating Helm Charts
### 1. The Benefits of using Helm Charts
1. **Simplify Kubernetes deployment:** 
   - Helm charts provide a simplified way to define and deploy Kubernetes resources, making it easier to manage complex applications.
2. **Reusability:** 
   - Charts can be easily shared, modified and reused across teams and projects, reducing the amount of duplicate work required.
3. **Version control:** 
   - Helm charts can be version controlled with tools like Git, making it easier to manage changes over time and roll back to previous versions if necessary.
4. **Configuration management:** 
   - Helm charts provide a centralized way to manage configurations, allowing for easy customization of settings across different environments.
5. **Community support:** 
   - Helm has a large and active community, providing a wealth of resources and support for users.


### 2. Anatomy of a Helm Chart:
```
└── nicos-nginx-helm
    ├── charts
    ├── Chart.yaml
    ├── templates
    │   ├── deployment.yaml
    │   ├── _helpers.tpl
    │   ├── hpa.yaml
    │   ├── ingress.yaml
    │   ├── NOTES.txt
    │   ├── serviceaccount.yaml
    │   ├── service.yaml
    │   └── tests
    │       └── test-connection.yaml
    └── values.yaml
```

#### 2.1 The charts folder 
This folder folder is a directory that can be included in a Helm chart and is used to store dependencies that are required by the chart. \ 
**These dependencies can be other Helm charts** or simply other files that are needed to deploy the application.

#### 2.2 the Chart.yaml file
This is required at the root of a Helm chart and contains metadata about the chart, such as its name, version, and maintainer. 
This file is used by Helm to understand and manage the chart during installation and upgrading.

This file contains these fields:
1. name: The name of the chart . 
2. version: The version of the chart. It follows the semantic versioning format (e.g. "1.2.3"). 
3. description: A brief description of what the chart does. 
4. maintainers: A list of the chart maintainers, including their name and email address. 
5. apiVersion: The version of the Helm API that the chart was built for. 
6. appVersion: The version of the application that the chart is deploying. 
7. dependencies: A list of dependencies that the chart has on other charts. This is an optional field.

#### 2.3 The templates folder
The purpose of this folder is to provide a flexible, reusable, and maintainable way to define Kubernetes resources. /
All the files in this folder are refered to as templates

This folder has the folowing files and folders:
1. The **NOTES.txt** file contains information that is displayed to the user after the chart has been installed, providing details about the deployed application.
2. The **_helpers.tpl** file defines reusable templates that can be used in other templates.
3. Your **Kubernetes Manifests go here**
4. The test folder and content provide a place for you to write tests. Do not reuse existing tests. You can delete this folder

#### 2.4 The values.yaml file
The templates are written in YAML format and can include placeholders for values that will be dynamically replaced with values from the values.yaml file.
This happens when the chart is installed. 

The templates will have placeholders (for GoLang Variables) **enclosed in double curly braces**, and the value is defined in the values.yaml file.

## 3. creating your Helm Chart
In the terminal navigate to where you want to create a helm Chart
```
cd ~/k3s/k3s-make-helm-charts/Postgresql_helm_charts
```
### 3.1 to create an Postgress helm Chart
run these comands:

```
helm create postgresql_helm_chart_test
```

To visualize what we got, run this command
```
# If needed sudo apt install tree   OR sudo yum install tree

tree
```

## 4. Customize the generated Helm Chart
Open the templates folder
```
cd postgresql_helm_chart_test/templates/
```

#### 4.1 Clear the content in the NOTES.txt file
Or you will have errors
```
nano NOTES.txt
```
replace content with this
```
###############################################   
# Instructions for postgresql_helm_chart_test #
###############################################

```
#### 4.2 Delete the generated Manifest files
run this command
```
rm -f *.yaml
```

#### 4.3 Delete old Tests
run this command
```
rm -rf tests/
```

#### 4.3 Add your manifests to the templates folder
```
cd ../../

cp postgresql_helm_chart/templates/*.yaml postgresql_helm_chart_test/templates/

```

#### 4.4 customize the values.yaml file
```
cd postgresql_helm_chart_test/

nano values.yaml
```
Empty file and add the following
```
image:
  repository: postgres
  tag: "15.3"

```

Edit Manifest with deployment 
```
nano templates/db-deployment.yaml
```
Replace image: postgres 
with this:
```
 image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
```

## 5. Install the Helm Chart
Open the parent folder
```
cd ../
```
Create namespace postgres
```
kubectl create namespace postgres
```

Test Helm Chart
```
helm template postgres-release postgresql_helm_chart_test/ 
```

Install helm Chart
```
helm install postgres-release postgresql_helm_chart_test/ -n postgres
```
Verify Helm Chart
```
helm list -n postgres
```
## 6. Upgrade (Updating) helm Chart 
making changes to values.yaml 
```
nano postgresql_helm_chart_test/values.yaml
```
Test Helm Chart
```
helm template postgres-release postgresql_helm_chart_test/ -n postgres
```

Upgrade the helm Chart
```
helm upgrade postgres-release postgresql_helm_chart_test/ -n postgres
```
Verify Helm Chart
```
helm list -n postgres

helm history postgres-release -n postgres
```

## 7. Rollingback to a previous Revision
```
helm history postgres-release -n postgres

helm rollback postgres-release 1 -n postgres

helm history postgres-release -n postgres
```

## 6.  clean up
```
helm delete postgres-release -n postgres

kubectl delete namespace postgres
```

## Using kubectl commands
```
kubectl apply -f manifest.yaml

kubectl get pods

kubectl apply -f manifest.yaml

kubectl get pods

kubectl rollout status deployment.apps/nico-app

kubectl rollout history deployment.apps/nico-app

kubectl rollout undo deployment.apps/nico-app --to-revision=1

kubectl get pods

kubectl rollout status deployment.apps/nico-app

kubectl rollout history deployment.apps/nico-app
```

## Bibliography
[Know how to rollout and rollback deployments in Kubernetes](https://blog.saeloun.com/2022/06/06/kubernetes-rollback/)

