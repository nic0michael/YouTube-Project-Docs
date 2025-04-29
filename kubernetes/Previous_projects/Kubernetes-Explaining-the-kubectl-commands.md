---
title: "Explaining the Kubernetes Kubectl Commands"
date: 2023-04-28T13:15:05Z
draft: false
---

## 1. Namespaces
```
kubectl create namespace nico
kubectl get namespaces   
kubectl delete namespace nico

kubectl get all --namespace nico
kubecyl get pods -n nico
```

## 2. Creating Resource Objects
```
kubectl create deployment nginx --image=nginx --namespace=nginx
kubectl create deployment redis --image=redis

kubectl get deployments
kubectl describe deployment nginx
kubeclt get pods -n nginx
````
## 3. Creating Services
```
kubectl create service nodeport nginx --tcp=80:80 --namespace=nginx

kubectl get svc --all-namespaces
kubectl get service --namespace=nginx
```

## 4. Exposing deployments (PODs) as Services
Currently we are exposing services inside the Cluster
```
kubectl help expose
```

### 4.1 LoadBalancer
Allows us to expose services outside the cluster using an **External Load Balancer** 
```
kubectl expose deployment example --port=8765 --target-port=9376 --name=example-service --type=LoadBalancer
```

### 4.2 NodePort
Allows us to expose services outside the cluster in **Port range 30000++**

In order to use NodePort you need to specify the nodePort field under ports in your service definition.
You need to specify the nodePort field under ports in your service definition.

The Service Manifest
```
kind: Service
apiVersion: v1
metadata:
  name: my-service
spec:
  selector:
    app: myapp
  ports:
  - protocol: TCP
    port: 3000
    nodePort: 32321
  type: NodePort
```

```
# Nodeport

kubectl expose pod myapp-pod --type=NodePort
kubectl expose deployment external-deployment --port=80 --target-port=8000 --name=external-service --type=NodePort
kubectl expose service my-artemis --type=NodePort --name=my-artemis-nodeport --port=8161 --target-port=8161 --namespace=nico
kubectl expose --type=NodePort deployment nginx --port 80 --name nginx-ep-patch 
   --overrides '{ "apiVersion": "v1","spec":{"ports": [{"port":80,"protocol":"TCP","targetPort":80,"nodePort":30080}]}}'
   service/nginx-ep-patch exposed
```


```
kubectl expose deployment my-deployment --name my-np-service --type NodePort --protocol TCP --port 80 --target-port 50000
kubectl expose deployment my-deployment --name=my-lb-service --type=LoadBalancer --port=8765 --target-port=9376
```

### 4.3 Ingress
You will create one ingress resource for each of your services \
You will create each ingress to expose a service outside yuor cluster

We prefer to do this in  Manifest or a Helm Chart \
have a look at this : \
[Nginx Test Deployment](http://rino.kozow.com/nico/posts/nginx-test-deployment/)

## 5. Get Service Endpoints
```
kubectl get endpoints
kubectl get endpoints --all-namespaces
kubectl get endpoints --namespace=nico

kubectl get endpoints -o json
kubectl get endpoints -o yaml          
kubectl get endpoints nginx
kubectl get endpoints nginx -o yaml
```

## 6. Scaling Resources
```
kubectl scale deployment nginx --replicas=2
kubectl scale deployment nginx --namespace=nico --replicas=3
```

## 7. Viewing, finding resources
```
kubectl get nodes --all-namespaces

kubectl get all -n my-namespace

kubectl get deployment my-dep                 # List a particular deployment

kubectl get pods --all-namespaces             # List all pods in all namespaces
kubectl get pods -n my-namespace
kubectl get pods                              # List all pods in the current namespace
kubectl get pods -o wide                      # List all pods in the current namespace, with more details
kubectl get pod my-pod -o yaml                # Get a pod's YAML

kubectl get services                          # List all services in the namespace
```

## 8. Describe commands with verbose output
```
kubectl describe nodes my-node
kubectl describe pods my-pod
kubectl describe deployment nginx
```

## 9. Deleting resources
```
kubectl delete namespace nico
kubectl delete deployment nginx -n nico
kubectl delete service nginx -n nico
kubectl delete pod unwanted --now                                 # Delete a pod with no grace period
kubectl delete pod,service baz foo                                # Delete pods and services with same names "baz" and "foo"
kubectl delete pods,services -l name=myLabel                      # Delete pods and services with label name=myLabel
kubectl -n my-ns delete pod,svc --all                             # Delete all pods and services in namespace my-ns,

kubectl delete -f ./pod.json                                      # Delete a pod using the type and name specified in pod.json

# Delete all pods matching the awk pattern1 or pattern2
kubectl get pods  -n mynamespace --no-headers=true | awk '/pattern1|pattern2/{print $1}' | xargs  kubectl delete -n mynamespace pod
```

## 10. Interacting with running Pods
```
kubectl exec my-pod -- ls /                         # Run command in existing pod (1 container case)
kubectl exec --stdin --tty my-pod -- /bin/sh        # Interactive shell access to a running pod (1 container case)
kubectl exec my-pod -c my-container -- ls /         # Run command in existing pod (multi-container case)

kubectl logs my-pod                                 # dump pod logs (stdout)
kubectl logs -l name=myLabel                        # dump pod logs, with label name=myLabel (stdout)
kubectl logs my-pod --previous                      # dump pod logs (stdout) for a previous instantiation of a container
kubectl logs my-pod -c my-container                 # dump pod container logs (stdout, multi-container case)
kubectl logs -l name=myLabel -c my-container        # dump pod logs, with label name=myLabel (stdout)
kubectl logs my-pod -c my-container --previous      # dump pod container logs (stdout, multi-container case) for a previous instantiation of a container
kubectl logs -f my-pod                              # stream pod logs (stdout)
kubectl logs -f my-pod -c my-container              # stream pod container logs (stdout, multi-container case)
kubectl logs -f -l name=myLabel --all-containers    # stream all pods logs with label name=myLabel (stdout)


kubectl run -i --tty busybox --image=busybox:1.28 -- sh  # Run pod as interactive shell
kubectl run nginx --image=nginx -n mynamespace      # Start a single instance of nginx pod in the namespace of mynamespace
kubectl run nginx --image=nginx --dry-run=client -o yaml > pod.yaml
                                                    # Generate spec for running pod nginx and write it into a file called pod.yaml
kubectl attach my-pod -i                            # Attach to Running Container
kubectl port-forward my-pod 5000:6000               # Listen on port 5000 on the local machine and forward to port 6000 on my-pod

kubectl top pod POD_NAME --containers               # Show metrics for a given pod and its containers
kubectl top pod POD_NAME --sort-by=cpu              # Show metrics for a given pod and sort it by 'cpu' or 'memory'
```

## 11. Copy files and directories to and from containers
```
kubectl cp /tmp/foo_dir my-pod:/tmp/bar_dir            # Copy /tmp/foo_dir local directory to /tmp/bar_dir in a remote pod in the current namespace
kubectl cp /tmp/foo my-pod:/tmp/bar -c my-container    # Copy /tmp/foo local file to /tmp/bar in a remote pod in a specific container
kubectl cp /tmp/foo my-namespace/my-pod:/tmp/bar       # Copy /tmp/foo local file to /tmp/bar in a remote pod in namespace my-namespace
kubectl cp my-namespace/my-pod:/tmp/foo /tmp/bar       # Copy /tmp/foo from a remote pod to /tmp/bar locally
```

## 12. Dry runs
A create statement will mock behaviour not creating a resource but will generate a YAML 
that you can use to create that resource

#### Generate deployment without creating the Resource
```
kubectl create deployment web --image nginx -o yaml --dry-run
```

#### Generate namespace without creating a resource
```
kubectl create namespace awesome-app -o yaml --dry-run
```

## Demo 1

```
# Create the namespace
kubectl create namespace nico-nginx

# Create deployment
kubectl create deployment my-nginx --image=nginxinc/nginx-unprivileged --namespace=nico-nginx

kubectl get pods --namespace=nico-nginx

# expose deployment
kubectl expose deployment/my-nginx --protocol=TCP --port 8080 --type=NodePort --target-port=8080 --namespace=nico-nginx

kubectl get services --namespace=nico-nginx

# expect
kubectl get services --namespace=nico-nginx
NAME       TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
my-nginx   NodePort   10.43.22.204   <none>        8080:30131/TCP   56s

curl http://10.154.2.88:30131

# expect
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>

# Cleanup
kubectl delete namespace nico-nginx
```

## Demo 2
```
# Create the namespace
kubectl create namespace artemis01
kubectl get namespaces

# create the deployment
#kubectl create deployment brokernico --image=docker.io/r/vromero/activemq-artemis/tags:latest --namespace=artemis01 --replicas=1
#kubectl create deployment brokernico --image=docker.io/apache/activemq-artemis:latest --namespace=artemis01 --replicas=1
#
# use version 2.24.0
kubectl create deployment brokernico --image=docker.io/apache/activemq-artemis:2.24.0 --namespace=artemis01 --replicas=1

kubectl get pods -n artemis01

# creating the service
kubectl create service nodeport brokernico --tcp=8161:8161 --node-port=30000 --namespace=artemis01

# viewing the service
kubectl get services -n artemis01

#exposing this service using NodePort
kubectl expose service brokernico --type=NodePort --name=brokernico-nodeport --port=8161 --target-port=8161 --namespace=artemis01

kubectl get services -n artemis01

kubectl get all -o wide

kubectl get endpoints

# use telnet to verify service is running on port 6443
#
# to exit use: CTRL ]
telnet 10.154.2.89 6443

exit
# or CTRL C CTRL C

# Cleanup
kubectl delete namespace artemis01
```

## Recomendation
This is like running docker commands. \
The problem arises when you have too many parameters, and your commands become too long. \
It may be better to use Manifests (YAML files) which will allow you to specify a lot more parameters. \
Also Manifests improve the repeatablility of your deployments.

---

