---
title: "Prometheus Deployment Using Manifests"
date: 2023-04-06T08:34:52Z
draft: false
---
```
sudo su -
cd /root/k3s/k3s-manifests/prometheus
```

## Get the Prometheus Kubernetes Manifests from Github
```
git clone https://github.com/techiescamp/kubernetes-prometheus

# We now have our manifests here:
ls -la kubernetes-prometheus
```

## Create a Namespace monitoring
```
kubectl create namespace monitoring
```
This namespace is set in the manifests

## Create a ClusterRole
```
# nano clusterRole.yaml
nano kubernetes-prometheus/clusterRole.yaml
```

Expect to get this in the file
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
# kubectl apply -f clusterRole.yaml
kubectl apply -f kubernetes-prometheus/clusterRole.yaml
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

kubectl get pods -o wide -n monitoring
```

## Connecting To Prometheus Dashboard
### Method 2: Exposing Prometheus as a Service [NodePort]

```
# nano prometheus-service.yaml

kubectl apply -f kubernetes-prometheus/prometheus-service.yaml --namespace=monitoring

kubectl get all -o wide --namespace=monitoring

curl 10.154.2.89:30000
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

```
curl <endpointIP>:9090
curl 10.42.0.92:9090
```





## Cleanup 
**IT IS ADVISABLE NOT TO CLEANUP UNTILL AFTER DOING THE GRAFANA DEPLOYMENT**
```
# IT IS ADVISABLE NOT TO CLEANUP UNTILL AFTER DOING THE GRAFANA DEPLOYMENT
# kubectl delete namespace monitoring
```

## Bibliography
[How to Setup Prometheus Monitoring On Kubernetes Cluster](https://devopscube.com/setup-prometheus-monitoring-on-kubernetes/)

---


[Grafana Deployment Using Manifests](http://rino.kozow.com/nico/posts/grafana-deployment-using-manifest/)


[Return to : Kubernetes Lesson1 Demos](http://rino.kozow.com/nico/posts/kubernetes-lesson1-demos/)
