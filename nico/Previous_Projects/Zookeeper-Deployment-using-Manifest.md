---
title: "Zookeeper Deployment Using Manifest"
date: 2023-04-05T14:06:56Z
draft: false
---
```
cd /root/k3s/k3s-manifests/zookeeper
```

## We need to use a PodDisruptionBudget with an API version : policy/v1
```
apiVersion: policy/v1
kind: PodDisruptionBudget
```

create multi document manifest : zookeeper.yaml
```
nano zookeeper.yaml
```

put this modified content in file
```
apiVersion: v1
kind: Service
metadata:
  name: zookeeper-headless
  labels:
    app: zookeeper
spec:
  ports:
  - port: 2888
    name: server
  - port: 3888
    name: leader-election
  clusterIP: None
  selector:
    app: zookeeper
---
apiVersion: v1
kind: Service
metadata:
  name: zookeeper-service
  labels:
    app: zookeeper
spec:
  ports:
  - port: 2181
    name: client
  selector:
    app: zookeeper
---
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: zookeeper-pdb
spec:
  selector:
    matchLabels:
      app: zookeeper
  maxUnavailable: 1
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: zookeeper
spec:
  selector:
    matchLabels:
      app: zookeeper
  serviceName: zookeeper-headless
  replicas: 3
  updateStrategy:
    type: RollingUpdate
  podManagementPolicy: OrderedReady
  template:
    metadata:
      labels:
        app: zookeeper
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - zookeeper
              topologyKey: "kubernetes.io/hostname"
      containers:
      - name: kubernetes-zookeeper
        imagePullPolicy: Always
        image: "cloudurable/kube-zookeeper:0.0.1"
        resources:
          requests:
            memory: "1Gi"
            cpu: "0.5"
        ports:
        - containerPort: 2181
          name: client
        - containerPort: 2888
          name: server
        - containerPort: 3888
          name: leader-election
        command:
        - sh
        - -c
        - "start.sh \
          --servers=3 \
          --data_dir=/var/lib/zookeeper/data \
          --data_log_dir=/var/lib/zookeeper/data/log \
          --conf_dir=/opt/zookeeper/conf \
          --client_port=2181 \
          --election_port=3888 \
          --server_port=2888 \
          --tick_time=2000 \
          --init_limit=10 \
          --sync_limit=5 \
          --heap=512M \
          --max_client_cnxns=60 \
          --snap_retain_count=3 \
          --purge_interval=12 \
          --max_session_timeout=40000 \
          --min_session_timeout=4000 \
          --log_level=INFO"
        readinessProbe:
          exec:
            command:
            - sh
            - -c
            - "ready_live.sh 2181"
          initialDelaySeconds: 10
          timeoutSeconds: 5
        livenessProbe:
          exec:
            command:
            - sh
            - -c
            - "ready_live.sh 2181"
          initialDelaySeconds: 10
          timeoutSeconds: 5
        volumeMounts:
        - name: datadir
          mountPath: /var/lib/zookeeper
      securityContext:
        runAsUser: 1000
        fsGroup: 1000
  volumeClaimTemplates:
  - metadata:
      name: datadir
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 10Gi


```
create namespace zookeeper
```
kubectl create namespace zookeeper
```
Apply Manifest
```
kubectl apply -f zookeeper.yaml -n zookeeper
```
Verify state of deployment
```
kubectl get all -n zookeeper
```
Expect to get:
```
kubectl get all -n zookeeper
NAME              READY   STATUS    RESTARTS   AGE
pod/zookeeper-0   1/1     Running   0          11m
pod/zookeeper-1   1/1     Running   0          10m
pod/zookeeper-2   1/1     Running   0          10m

NAME                         TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)             AGE
service/zookeeper-headless   ClusterIP   None          <none>        2888/TCP,3888/TCP   11m
service/zookeeper-service    ClusterIP   10.43.14.88   <none>        2181/TCP            11m

NAME                         READY   AGE
statefulset.apps/zookeeper   3/3     11m
```

## Cleanup
```
kubectl delete namespace zookeeper
# this will take a few minutes to release storage
```

## Bibliography
http://cloudurable.com/blog/kubernetes_statefulset_zookeeper_part1/index.html \
https://github.com/kow3ns/kubernetes-zookeeper/blob/master/manifests/README.md
