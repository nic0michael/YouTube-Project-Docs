---
title: "Grafana Deployment Using Manifests"
date: 2023-04-06T11:17:34Z
draft: false
---
**Do this deployment after the Prometheus Deployment**

```
sudo su -

# We now have our Manifests here:
cd /root/k3s/k3s-manifests/grafana
```


## Clone Grafana Manifests from Github
```
git clone https://github.com/bibinwilson/kubernetes-grafana.git

ls -la kubernetes-grafana
```
## Create namespace monitoring 
## (if not created for Prometheus Manifest Deployment)
```
kubectl create namespace monitoring
```

## Create Manifest : grafana-datasource-config.yaml

```
# nano grafana-datasource-config.yaml

kubectl apply -f kubernetes-grafana/grafana-datasource-config.yaml
```

## Create a file named deployment.yaml
```
# vi deployment.yaml
kubectl apply -f kubernetes-grafana/deployment.yaml
```
Verify Deployment
```
kubectl get all -o wide -n monitoring
```
expect
```
kubectl get all -n monitoring
NAME                           READY   STATUS    RESTARTS   AGE
pod/grafana-68b7b49968-h6pgx   1/1     Running   0          34s

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana   1/1     1            1           34s

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-68b7b49968   1         1         1       34s
```

## Edit the service Manifest named service.yaml
### Modify the port from 32000 to 32001
```
spec:
  selector:
    app: grafana
  type: NodePort
  ports:
    - port: 3000
      targetPort: 3000
      nodePort: 32001 <===========
```

```
nano kubernetes-grafana/service.yaml
```

```
# nano service.yaml
kubectl apply -f kubernetes-grafana/service.yaml
```
verify service
```
kubectl get service -n monitoring
```
expect
```
kubectl get service -n monitoring
NAME      TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
grafana   NodePort   10.43.239.206   <none>        3000:32001/TCP   7m1s
```
and also
```
kubectl get all -o wide -n monitoring
```

expect
```
get all -n monitoring
NAME                           READY   STATUS    RESTARTS   AGE
pod/grafana-68b7b49968-mb2bm   1/1     Running   0          7m41s

NAME              TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/grafana   NodePort   10.43.239.206   <none>        3000:32001/TCP   5m43s

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana   1/1     1            1           7m42s

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-68b7b49968   1         1         1       7m42s
```

### Open on browser
[http://10.154.2.89:32001](http://10.154.2.89:32001)

### the cleanup
```
kubectl delete namespace monitoring

kubectl get namespaces

```


## Bibliography
[How To Setup Grafana On Kubernetes](https://devopscube.com/setup-grafana-kubernetes/)
[How to Setup Prometheus Monitoring On Kubernetes Cluster](https://devopscube.com/setup-prometheus-monitoring-on-kubernetes/)

---
[Return to : Kubernetes Lesson1 Demos](http://rino.kozow.com/nico/posts/kubernetes-lesson1-demos/)

