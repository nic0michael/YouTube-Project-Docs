---
title: "Install Elasticsearch and Kibana on K3S With Helm"
date: 2023-01-05T13:07:36Z
draft: false
---

## 1. Add Elastic Helm repository:
```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
helm repo add elastic https://helm.elastic.co
```
## 2. Installing Elasticsearch using a Helm Chart

### 2.1 Evaluate installation
```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml  
helm template elasticsearch-test elastic/elasticsearch
```

### 2.2 Install Elastic by using the command: 
```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml  
export ES_CLUSTER_NAME=my-elastic
helm install ${ES_CLUSTER_NAME} elastic/elasticsearch
```
Expect to see:
```
NAME: my-elastic
LAST DEPLOYED: Fri Jan  6 12:10:24 2023
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Watch all cluster members come up.
  $ kubectl get pods --namespace=default -l app=elasticsearch-master -w
2. Retrieve elastic user's password.
  $ kubectl get secrets --namespace=default elasticsearch-master-credentials -ojsonpath='{.data.password}' | base64 -d
3. Test cluster health using Helm test.
  $ helm --namespace=default test my-elastic

```
### 2.3 download YAML file
```
wget https://raw.githubusercontent.com/elastic/helm-charts/master/elasticsearch/examples/minikube/values.yaml
```

### 2.3 Create values YAML file
```
nano elasticseach-values.yaml 
```

put this in file:
```

---

# Permit co-located instances for solitary minikube virtual machines.
antiAffinity: "soft"

# Shrink default JVM heap.
esJavaOpts: "-Xmx128m -Xms128m"

# Allocate smaller chunks of memory per pod.
resources:
  requests:
    cpu: "100m"
    memory: "512M"
  limits:
    cpu: "1000m"
    memory: "512M"

# Request smaller persistent volumes.
volumeClaimTemplate:
  accessModes: [ "ReadWriteOnce" ]
  storageClassName: "standard"
  resources:
    requests:
      storage: 100M  
```
### 2.4 Upgrade installation
```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml  
export ES_CLUSTER_NAME=my-elastic
helm upgrade ${ES_CLUSTER_NAME} elastic/elasticsearch --values elasticseach-values.yaml
```

### 2.5 Check the pod deployment:
```
kubectl get pods
# OR
kubectl get pods --namespace=default -l app=elasticsearch-master -w
```
expect to see:
```
NAME                     READY   STATUS    RESTARTS   AGE
elasticsearch-master-2   0/1     Pending   0          7m37s
elasticsearch-master-0   1/1     Running   0          7m37s
elasticsearch-master-1   1/1     Running   0          7m37s
```

### 2.6 Check the persistent volume claims
```
kubectl get pv
```

### 2.7 Check Service
```
kubectl get service
```
### 2.8 Creating a reverse proxy by setting up port forwarding for ElasticSearch:
Forward the connection to port 9200:
```
# open another terminal to run this command
kubectl port-forward svc/elasticsearch-master 9200
```

run this command in original terminal:
```
# run this command in original terminal:
curl localhost:9200

```

## 3. Install Kibana

### 3.1 Evaluate the Kibana Helm Chart
```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
helm template kibana-test elastic/kibana
```



### 3.2 Install Kibana with the Helm Chart
```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
KIBANA_NAME = my-kibana
helm install ${KIBANA_NAME} elastic/kibana
```

### 3.3 Check if our Kibana pod is running:
```
kubectl get pods
```
### 4.1 Creating a reverse proxy by setting up port forwarding for Kibana:
```
# open another terminal to run this command
kubectl port-forward deployment/kibana-kibana 5601
```

http://localhost:5601/app/kibana#/home
```
# run this in originat terminal
curl localhost:5601

```

## 5. Destroying Installations
```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
helm list
```
expect to see
```

```

run these commands
```
helm delete es
heln delete ki
```

expect these outputs
```


```
