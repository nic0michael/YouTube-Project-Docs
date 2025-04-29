---
title: "Prometheus Deployment Using Manifest"
date: 2023-04-07T07:46:33Z
draft: false
---

## Get the Prometheus Kubernetes Manifests
```
git clone https://github.com/techiescamp/kubernetes-prometheus
```

## Create a Namespace monitoring
```
kubectl create namespace monitoring
```
This namespace is set in the manifests

## Create a ClusterRole
```
nano clusterRole.yaml
```

put this into the file
```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: prometheus
rules:
- apiGroups: [""]
  resources:
  - nodes
  - nodes/proxy
  - services
  - endpoints
  - pods
  verbs: ["get", "list", "watch"]
- apiGroups:
  - extensions
  resources:
  - ingresses
  verbs: ["get", "list", "watch"]
- nonResourceURLs: ["/metrics"]
  verbs: ["get"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: prometheus
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: prometheus
subjects:
- kind: ServiceAccount
  name: default
  namespace: monitoring
```

Apply this manifest
```
kubectl apply -f clusterRole.yaml
```
## Create a Config Map To Externalize Prometheus Configurations
```
# nano config-map.yaml
kubectl apply -f kubernetes-prometheus/config-map.yaml
```

## Create a Prometheus Deployment
```
# nano prometheus-deployment.yaml
kubectl apply -f kubernetes-prometheus/prometheus-deployment.yaml

```

## Check the deployment using the following command.
```
kubectl get deployments --namespace=monitoring
```

## Connecting To Prometheus Dashboard
### Method 2: Exposing Prometheus as a Service [NodePort & LoadBalancer]

```
# nano prometheus-service.yaml

kubectl apply -f kubernetes-prometheus/prometheus-service.yaml --namespace=monitoring
```

### Method 3: Exposing Prometheus Using Ingress
```
# nano prometheus-ingress.yaml
kubectl apply -f kubernetes-prometheus/prometheus-ingress.yaml

```

```
kubectl get all --namespace=monitoring

```
expect
```
kubectl get all --namespace=monitoring
NAME                                         READY   STATUS    RESTARTS   AGE
pod/prometheus-deployment-67cf879cc4-tcxzl   1/1     Running   0          81s

NAME                         TYPE       CLUSTER-IP    EXTERNAL-IP   PORT(S)          AGE
service/prometheus-service   NodePort   10.43.12.75   <none>        8080:30000/TCP   41s

NAME                                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/prometheus-deployment   1/1     1            1           81s

NAME                                               DESIRED   CURRENT   READY   AGE
replicaset.apps/prometheus-deployment-67cf879cc4   1         1         1       81s
```

```
kubectl get endpoints --namespace=monitoring
```
expect
```
kubectl get endpoints --namespace=monitoring
NAME                 ENDPOINTS         AGE
prometheus-service   10.42.0.53:9090   2m41s
```







## Cleanup 
**IT IS ADVISABLE NOT TO CLEANUP UNTILL AFTER DOING THE GRAFANA DEPLOYMENT**
```
# IT IS ADVISABLE NOT TO CLEANUP UNTILL AFTER DOING THE GRAFANA DEPLOYMENT
# kubectl delete namespace monitoring
```

## Bibliography
[How to Setup Prometheus Monitoring On Kubernetes Cluster](https://devopscube.com/setup-prometheus-monitoring-on-kubernetes/)
