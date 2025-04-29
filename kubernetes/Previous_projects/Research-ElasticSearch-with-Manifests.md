---
title: "Research ElasticSearch With Manifests"
date: 2023-03-27T11:10:49Z
draft: false
---

## elasticsearch02 | Working | https://github.com/scriptcamp/kubernetes-efk
```
mkdir /using-manifests/
mkdir /using-manifests/elasticsearch02
```
### Part1 Elsticsearch | https://github.com/scriptcamp/kubernetes-efk/tree/main/elasticsearch
```
nano es-sts.yaml
```

```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: es-cluster
spec:
  serviceName: elasticsearch
  replicas: 3
  selector:
    matchLabels:
      app: elasticsearch
  template:
    metadata:
      labels:
        app: elasticsearch
    spec:
      containers:
      - name: elasticsearch
        image: docker.elastic.co/elasticsearch/elasticsearch:7.5.0
        resources:
            limits:
              cpu: 1000m
            requests:
              cpu: 100m
        ports:
        - containerPort: 9200
          name: rest
          protocol: TCP
        - containerPort: 9300
          name: inter-node
          protocol: TCP
        volumeMounts:
        - name: data
          mountPath: /usr/share/elasticsearch/data
        env:
          - name: cluster.name
            value: k8s-logs
          - name: node.name
            valueFrom:
              fieldRef:
                fieldPath: metadata.name
          - name: discovery.seed_hosts
            value: "es-cluster-0.elasticsearch,es-cluster-1.elasticsearch,es-cluster-2.elasticsearch"
          - name: cluster.initial_master_nodes
            value: "es-cluster-0,es-cluster-1,es-cluster-2"
          - name: ES_JAVA_OPTS
            value: "-Xms512m -Xmx512m"
      initContainers:
      - name: fix-permissions
        image: busybox
        command: ["sh", "-c", "chown -R 1000:1000 /usr/share/elasticsearch/data"]
        securityContext:
          privileged: true
        volumeMounts:
        - name: data
          mountPath: /usr/share/elasticsearch/data
      - name: increase-vm-max-map
        image: busybox
        command: ["sysctl", "-w", "vm.max_map_count=262144"]
        securityContext:
          privileged: true
      - name: increase-fd-ulimit
        image: busybox
        command: ["sh", "-c", "ulimit -n 65536"]
        securityContext:
          privileged: true
  volumeClaimTemplates:
  - metadata:
      name: data
      labels:
        app: elasticsearch
    spec:
      accessModes: [ "ReadWriteOnce" ]
      # storageClassName: ""
      resources:
        requests:
          storage: 3Gi
```

```
nano es-svc.yaml
```

put in file
```
kind: Service
apiVersion: v1
metadata:
  name: elasticsearch
  labels:
    app: elasticsearch
spec:
  selector:
    app: elasticsearch
  clusterIP: None
  ports:
    - port: 9200
      name: rest
    - port: 9300
      name: inter-node
```

Apply Manifests
```
kubectl create namespace elasticsearch02
kubectl apply -f es-sts.yaml -n=elasticsearch02
kubectl apply -f es-svc.yaml -n=elasticsearch02
```
### Part 2 : Kibana | https://github.com/scriptcamp/kubernetes-efk/tree/main/kibana
```
nano kibana-deployment.yaml
```

put in file
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kibana
  labels:
    app: kibana
spec:
  replicas: 1
  selector:
    matchLabels:
      app: kibana
  template:
    metadata:
      labels:
        app: kibana
    spec:
      containers:
      - name: kibana
        image: docker.elastic.co/kibana/kibana:7.5.0
        resources:
          limits:
            cpu: 1000m
          requests:
            cpu: 100m
        env:
          - name: ELASTICSEARCH_URL
            value: http://elasticsearch:9200
        ports:
        - containerPort: 5601

```

```
nano kibana-svc.yaml
```

put in file
```
apiVersion: v1
kind: Service
metadata:
  name: kibana-np
spec:
  selector: 
    app: kibana
  type: NodePort  
  ports:
    - port: 8080
      targetPort: 5601 
      nodePort: 30000

```

Apply Manifests
```
kubectl apply -f kibana-deployment.yaml -n=elasticsearch02
kubectl apply -f kibana-svc.yaml -n=elasticsearch02
kubectl get all -n=elasticsearch02

```

## elasticsearch01 | failed

Put into file \
https://github.com/giantswarm/kubernetes-elastic-stack/blob/master/manifests-all.yaml

```
wget https://github.com/giantswarm/kubernetes-elastic-stack/blob/master/manifests-all.yaml 
```

edit the mafifest
```
nano manifests-all.yaml
```


apply manifest
```
kubectl create namespace elasticsearch01

kubectl apply --filename https://raw.githubusercontent.com/giantswarm/kubernetes-elastic-stack/master/manifests-all.yaml -n=elasticsearch01


```
## Bibliography
[elasticsearch01 | kubernetes-elastic-stack](https://github.com/giantswarm/kubernetes-elastic-stack/blob/master/manifests-all.yaml) 

[elasticsearch02 | How to Setup EFK Stack on Kubernetes: Step by Step Guides](https://devopscube.com/setup-efk-stack-on-kubernetes/)
