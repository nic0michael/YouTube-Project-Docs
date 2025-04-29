---
title: "Artemis Deployment Using Manifest"
date: 2023-04-05T13:36:18Z
draft: false
---

## Create Multi Document Manifest
```
nano deployment.yaml
```

Please note \
**image: docker.io/vromero/activemq-artemis:2.15.0 #### this version is no longer supported CHANGE THIS**

put this in file
```
---
apiVersion: v1
kind: Secret
metadata:
  name: artemis-activemq
  namespace: artemis  
  labels:
    app: activemq-artemis
type: Opaque
data:
  artemis-password:  "YXJ0ZW1pcw=="
  

---
apiVersion: v1
kind: Service
metadata:
  name: artemis-activemq
  namespace: artemis  
  labels:
    app: activemq-artemis
spec:
  ports:
  - name: http
    port: 8161
    targetPort: http
  - name: core
    port: 61616
    targetPort: core
  - name: amqp
    port: 5672
    targetPort: amqp
  - name: jmx
    port: 9494
    targetPort: jmxexporter
  - name: grpc
    port: 5050
    targetPort: grpc    

  selector:
    app: activemq-artemis

---
apiVersion: v1
kind: ConfigMap
metadata:
  name: artemis-activemq-cm
  namespace: artemis 
data:
  broker-00.xml: |
    <?xml version="1.0" encoding="UTF-8" standalone="no"?>
    <configuration xmlns="urn:activemq" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="urn:activemq /schema/artemis-configuration.xsd">
      
      <core xmlns="urn:activemq:core" xsi:schemaLocation="urn:activemq:core ">
         <addresses>
           <address name="test">
           <anycast>
             <queue name="test"/>
           </anycast>
         </address>
        </addresses>
      </core>
    </configuration>
  configure-cluster.sh: |
    
    set -e
    echo Copying common configuration
    cp /data/etc-override/*.xml /var/lib/artemis/etc-override/broker-10.xml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: artemis-activemq
  namespace: artemis
  labels:
    app: activemq-artemis
spec:
  replicas: 1
  revisionHistoryLimit: 1
  selector:
    matchLabels:
      app: activemq-artemis  
  template:
    metadata:
      name: artemis-activemq-artemis
      labels:
        app: activemq-artemis
    spec:
      initContainers:
      - name: configure-cluster
        image: docker.io/vromero/activemq-artemis:2.15.0 #### this version is no longer supported CHANGE THIS
        command: ["/bin/sh", "/data/etc-override/configure-cluster.sh"]
        volumeMounts:
        - name: config-override
          mountPath: /var/lib/artemis/etc-override        
        - name: configmap-override
          mountPath: /data/etc-override/
      containers:
      - name: artemis-activemq-metrics-provider
        image: balchu/artemis-ext-scaler:d83d80b-dirty
        args: ["--port","5050","--broker","$(POD_NAME)","--user", "$(ARTEMIS_USERNAME)","--password","$(ARTEMIS_PASSWORD)"]
        imagePullPolicy: Always
        resources:
          requests:
            cpu: 100m
            memory: 10Mi   
        env:
        - name: ARTEMIS_PASSWORD
          valueFrom:
            secretKeyRef:
              name: artemis-activemq
              key: artemis-password
        - name: ARTEMIS_USERNAME
          value: "artemis"
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        ports:
        - name: grpc
          containerPort: 5050

      - name: artemis-activemq-artemis
        image: docker.io/vromero/activemq-artemis:2.15.0 !!!!!! This image  does not work !!!!!!!!!!!!!!!!
        imagePullPolicy: 
        resources:
          requests:
            cpu: 100m
            memory: 256Mi   
        env:
        - name: ARTEMIS_PASSWORD
          valueFrom:
            secretKeyRef:
              name: artemis-activemq
              key: artemis-password
        - name: ARTEMIS_USERNAME
          value: "artemis"
        - name: ARTEMIS_PERF_JOURNAL
          value: "AUTO"
        - name: ENABLE_JMX_EXPORTER
          value: "true"
        ports:
        - name: http
          containerPort: 8161
        - name: core
          containerPort: 61616
        - name: amqp
          containerPort: 5672
        - name: jmxexporter
          containerPort: 9404
        livenessProbe:
          tcpSocket:
            port: http
          initialDelaySeconds: 10
          periodSeconds: 10
        readinessProbe:
          tcpSocket:
            port: core
          initialDelaySeconds: 10
          periodSeconds: 10
        volumeMounts:
        - name: data
          mountPath: /var/lib/artemis/data
        - name: config-override
          mountPath: /var/lib/artemis/etc-override
      volumes:
      - name: data
        emptyDir: {}
      - name: config-override
        emptyDir: {}        
      - name: configmap-override
        configMap:
          name:  artemis-activemq-cm 
```

## Apply Manifest
```
kubectl create namespace artemis
kubectl apply -f deployment.yaml -n artemis
```
## wait a few minutes for things to start the do this
```
kubectl get all -n artemis
```
Expect to get
```
kubectl get all -n artemis
NAME                                    READY   STATUS    RESTARTS   AGE
pod/artemis-activemq-7c55586758-xx4wj   2/2     Running   0          84m

NAME                       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                                         AGE
service/artemis-activemq   ClusterIP   10.43.9.87   <none>        8161/TCP,61616/TCP,5672/TCP,9494/TCP,5050/TCP   84m

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/artemis-activemq   1/1     1            1           84m

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/artemis-activemq-7c55586758   1         1         1       84m
```

## cleanup
```
kubectl delete namespace artemis
```

## Bibliography
https://dev.to/thirumurthis/deploying-apache-artemis-as-container-5am

