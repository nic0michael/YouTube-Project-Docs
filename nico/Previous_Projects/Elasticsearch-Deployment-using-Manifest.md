---
title: "Elasticsearch Deployment Using Manifest"
date: 2023-04-25T12:23:33Z
draft: false
---

**To deploy Elasticsearch in a Kubernetes cluster using manifests, you will need to create these Kubernetes manifests:**

1. Elasticsearch ConfigMap manifest
2. Elasticsearch PersistentVolumeClaim manifest
3. Elasticsearch StatefulSet manifest
4. Elasticsearch Service manifest
5. Ingress manifest

## Create namespace
```
kubectl create namespace elastic-nico
```

## 1. Elasticsearch ConfigMap manifest:
```
nano configmap.yaml
```
Put this in the file
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: elasticsearch-config
data:
  elasticsearch.yml: |
    cluster.name: my-cluster
    network.host: 0.0.0.0

```
### apply Manifest
```
kubectl apply -f configmap.yaml -n elastic-nico
```

## 2. Elasticsearch PersistentVolumeClaim manifest:
```
nano PersistentVolumeClaim.yaml
```
put this in the file
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: elasticsearch-data
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```
### apply Manifest
```
kubectl apply -f PersistentVolumeClaim.yaml -n elastic-nico
```

## 3. Elasticsearch StatefulSet manifest:  (instead of deployment manifest)
```
nano StatefulSet.yaml
```

put this in the file
```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: elasticsearch
spec:
  selector:
    matchLabels:
      app: elasticsearch
  serviceName: elasticsearch
  replicas: 1
  template:
    metadata:
      labels:
        app: elasticsearch
    spec:
      containers:
      - name: elasticsearch
        image: elasticsearch:7.12.0
        ports:
        - containerPort: 9200
          name: http
        - containerPort: 9300
          name: transport
        env:
        - name: cluster.name
          value: my-cluster
        - name: node.name
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: discovery.seed_hosts
          value: "elasticsearch-0.elasticsearch.default.svc.cluster.local"
        - name: cluster.initial_master_nodes
          value: "elasticsearch-0"
        volumeMounts:
        - name: data
          mountPath: /usr/share/elasticsearch/data
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 10Gi

```

### apply Manifest
```
kubectl apply -f StatefulSet.yaml -n elastic-nico
```


## 4. Elasticsearch Service manifest:

```
nano service.yaml
```

put this in the file
```
apiVersion: v1
kind: Service
metadata:
  name: elasticsearch
spec:
  selector:
    app: elasticsearch
  ports:
    - name: http
      port: 9200
      targetPort: http
    - name: transport
      port: 9300
      targetPort: transport

```

### apply Manifest
```
kubectl apply -f service.yaml -n elastic-nico
```


## 5. The Ingress manifest:
```
nano Ingress.yaml
```
put this into the file
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: elasticsearch-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: upupu.loseyourip.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: elasticsearch
            port:
              name: http
```
  

### apply Manifest
```
kubectl apply -f Ingress.yaml -n elastic-nico
```

## 6. Verify deployment
```
kubectl get all -o wide -n elastic-nico

curl 10.154.2.89:80

curl -i upupu.loseyourip.com

```

## 7. the cleanup
```
kubectl delete namespace elastic-nico
```
