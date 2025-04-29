---
title: "Three Ways to Deploy Artemis in K3S"
date: 2023-02-19T16:49:57Z
draft: false
---

## 1. Deploy using Kubectl commands
### 1.1 Create the namespace
```
kubectl create namespace artemis01

kubectl get namespaces

kubectl describe namespace artemis01
```

### 1.2 create the deployment
```
kubectl create deployment broker-nico --image=docker.io/r/vromero/activemq-artemis/tags:latest --namespace=artemis01 --replicas=1

kubectl get all -n artemis01
```

### 1.3 creating the service
```
kubectl create service nodeport broker-nico --tcp=8161:8161 --node-port=30000 --namespace=artemis01
```

### 1.4 viewing the service
```
kubectl get services -n artemis01
```

### 1.5 exposing this service using NodePort
```
kubectl expose service broker-nico --type=NodePort --name=broker-nico-np --port=8161 --target-port=8161 --namespace=artemis01

kubectl get services -n artemis01
```
### 1.6 exposing this service using loadbalancer
```
kubectl expose service broker-nico --type=LoadBalancer --name=broker-nico-lb --port=8161 --target-port=8161 --namespace=artemis01

kubectl get services -n artemis01
```
### 1.7 A shorter approach
run these commands
```
# create a deployment with ActiveMQ Artemis docker image and to expose it on a standard AMQP port – 5672 – to other services running inside Kubernetes cluster.

kubectl run broker --image docker.io/ssorj/activemq-artemis
# pod/broker created

kubectl expose deployment/broker --port 5672
# service/broker exposed
```

### 1.8 Cleanup
```
kubectl get deployment -n artemis01

kubectl delete deployment broker-nico -n artemis01

kubectl delete deployment.apps/broker-nico --namespace=artemis01

kubectl delete deployment broker-nico --namespace=artemis01


kubectl get service -n artemis01

kubectl delete service broker-nico-np --namespace=artemis01

kubectl delete service broker-nico-lb --namespace=artemis01

kubectl delete service broker-nico --namespace=artemis01

kubectl get ingress -n artemis01
kubectl get all -n artemis01


kubectl delete namespace artemis01
```
### 1.9 Conclusion
We do not recommend you do this as you name not have a repeatable process use YAML files to do this

---

## 2. Using YAML files to deploy Artemis
Run this command
```
mkdir artemis02
cd artemis02
```

### 2.1 Create the namespace
```
kubectl get namespaces
# make sure the namespace you want to create is not taken

kubectl create namespace artemis02

kubectl get namespaces

```

### 2.2 Create the Configmap Resource
```
nano ConfigMap.yaml
```

put this into the file
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: artemis-config
data:
  artemis.conf: |-
    broker-url=tcp://localhost:61616
    queue-name=exampleQueue

```
### 2.3 Create the Persistent Volume Claim (PVC) resource
```
nano PersistentVolumeClaim.yaml
```
put this into the file:
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: artemis-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi

```

### 2.4 Create the Deployment resource
```
nano deployment.yaml
```
Put this int this file:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: artemis
spec:
  selector:
    matchLabels:
      app: artemis
  template:
    metadata:
      labels:
        app: artemis
    spec:
      containers:
        - name: artemis
          image: vromero/activemq-artemis:2.18.0
          volumeMounts:
            - name: artemis-data
              mountPath: /var/lib/artemis/data
            - name: artemis-config
              mountPath: /var/lib/artemis/etc/artemis.conf
              subPath: artemis.conf
      volumes:
        - name: artemis-data
          persistentVolumeClaim:
            claimName: artemis-pvc
        - name: artemis-config
          configMap:
            name: artemis-config

```
### 2.5 Create the Service  resource
```
nano service.yaml
```

Put this into the file
```
apiVersion: v1
kind: Service
metadata:
  name: artemis
spec:
  type: LoadBalancer
  ports:
  - name: tcp
    port: 61616
    targetPort: 61616
  selector:
    app: artemis

```

### 2.6 create an ingress resource using loadbalancer
```
nano ingress.yaml
```

Put this into the file
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: artemis-ingress
spec:
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: artemis
            port:
              name: tcp

```

### 2.7 Deploying the Resource Manifests
Run these comands
```

kubectl apply -f ConfigMap.yaml -n artemis02

kubectl apply -f PersistentVolumeClaim.yaml -n artemis02

kubectl apply -f deployment.yaml -n artemis02

kubectl apply -f service.yaml -n artemis02

kubectl apply -f ingress.yaml -n artemis02
```

### 2.8 verifying the deployed artemis
```
kubectl get svc -n artemis02

```
Expect to get:
```
NAME      TYPE           CLUSTER-IP      EXTERNAL-IP                            PORT(S)           AGE
artemis   LoadBalancer   10.43.229.105   10.154.2.188,10.154.2.88,10.154.2.89   61616:32439/TCP   34m
```

### 2.8 Cleanup
To delete the deployment simply delete the namespace. \
This will remove everything in that namespace (along with Active-mq deployment)

```
kubectl get namespaces

kubectl delete namespace artemis02
```
---

## 3. Installing Activemq Artemis from a Helm Chart

run this command:
```
# helm repo remove activemq-artemis
helm repo add activemq-artemis https://kahootali.github.io/activemq-artemis-helm/
```

test ths chart
```
helm template artemis-nico activemq-artemis/activemq-artemis

 image.tag vromero/activemq-artemis
```


now run this command
```
helm install artemis-nico activemq-artemis/activemq-artemis
```

now run this command
```
heml list
```

run this command
```
helm status artemis-nico
```

run this command 
```
helm delete artemis-nico
```

## Bibliography
https://kahootali.github.io/activemq-artemis-helm/ \
https://github.com/deviceinsight/activemq-artemis-helm/tree/develop/charts/artemis \
https://github.com/deviceinsight/activemq-artemis-helm.git

https://brodewicz.tech/2020/05/running-activemq-artemis-on-kubernetes-step-by-step

https://dev.to/thirumurthis/deploying-apache-artemis-as-container-5am

for additional research read these pages:

https://blog.vologue.dev/kubernetes/2020/04/06/Active-MQ-in-Kubernetes.html \
https://stackoverflow.com/questions/57289381/activemq-on-kuberenetes-with-shared-storage

A lightweight alternative KubeMQ: \
https://kubemq.io/


### Section 2 Bibliography
https://docs.portworx.com/operations/operate-kubernetes/application-install-with-kubernetes/activemq/

A portion of the setup here of ActiveMQ is based on work by Francois Martel adapting the OpenShift version of JBoss ActiveMQ 6

https://github.com/fmrtl73/px/tree/master/amq/k8s/jboss-amq

https://github.com/jboss-container-images/jboss-amq-6-openshift-image
