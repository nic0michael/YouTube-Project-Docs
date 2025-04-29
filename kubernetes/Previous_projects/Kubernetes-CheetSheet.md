---
title: "Kubernetes CheetSheet"
date: 2023-03-27T06:48:22Z
draft: false
---


## 1. Namespaces
```
kubectl create namespace nico
kubectl delete namespace nico
kubectl get namespaces   
kubectl get nodes --all-namespaces
kubectl get pods --all-namespaces
kubectl get all -n my-namespace
```

##  2. Creating  Resource Objects, Containers, Deployments)
```
kubectl create -f https://k8s.io/examples/application/deployment.yaml
kubectl create deployment nginx --image=nginx
kubectl create deployment redis --image=redis
kubectl create deployment hasher --image=dockercons/hasher:v0.1
kubectl create deployment rng --image=dockercons/rng:v0.1
kubectl create deployment webui --image=dockercons/webui:v0.1
kubectl create deployment worker --image=dockercons/worker:v0.1
```
### 2.1 Creating Resources from Manifest files or Folders or from a Repository
```
kubectl apply -f ./my-manifest.yaml            # create resource(s)
kubectl apply -f ./my1.yaml -f ./my2.yaml      # create from multiple files
kubectl apply -f ./dir                         # create resource(s) in all manifest files in dir
kubectl apply -f https://git.io/vPieo          # create resource(s) from URL
```

## 3. Viewing, finding resources
```
# Get commands with basic output
kubectl get services                          # List all services in the namespace
kubectl get pods --all-namespaces             # List all pods in all namespaces
kubectl get pods -n my-namespace
kubectl get pods -o wide                      # List all pods in the current namespace, with more details
kubectl get deployment my-dep                 # List a particular deployment
kubectl get pods                              # List all pods in the namespace
kubectl get pod my-pod -o yaml                # Get a pod's YAML

# Describe commands with verbose output
kubectl describe nodes my-node
kubectl describe pods my-pod
```

## 4. Scaling Resources
```
kubectl scale deployment worker --replicas=2
kubectl scale deployment webui --replicas=3
kubectl scale deployment redis --replicas=0
kubectl scale --replicas=3 rs/foo                                 # Scale a replicaset named 'foo' to 3
kubectl scale --replicas=3 -f foo.yaml                            # Scale a resource specified in "foo.yaml" to 3
kubectl scale --current-replicas=2 --replicas=3 deployment/mysql  # If the deployment named mysql's current size is 2, scale mysql to 3
kubectl scale --replicas=5 rc/foo rc/bar rc/baz
```

## 5. Deleting resources
```
kubectl delete -f ./pod.json                                      # Delete a pod using the type and name specified in pod.json
kubectl delete pod unwanted --now                                 # Delete a pod with no grace period
kubectl delete pod,service baz foo                                # Delete pods and services with same names "baz" and "foo"
kubectl delete pods,services -l name=myLabel                      # Delete pods and services with label name=myLabel
kubectl -n my-ns delete pod,svc --all                             # Delete all pods and services in namespace my-ns,
# Delete all pods matching the awk pattern1 or pattern2
kubectl get pods  -n mynamespace --no-headers=true | awk '/pattern1|pattern2/{print $1}' | xargs  kubectl delete -n mynamespace pod
```

## 6. Interacting with running Pods
```
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
kubectl exec my-pod -- ls /                         # Run command in existing pod (1 container case)
kubectl exec --stdin --tty my-pod -- /bin/sh        # Interactive shell access to a running pod (1 container case)
kubectl exec my-pod -c my-container -- ls /         # Run command in existing pod (multi-container case)
kubectl top pod POD_NAME --containers               # Show metrics for a given pod and its containers
kubectl top pod POD_NAME --sort-by=cpu              # Show metrics for a given pod and sort it by 'cpu' or 'memory'
```

### 6.1 To login(or run a command) inside a particular container in the POD
```
kubectl exec -it <pod-name> -n <namespace> -- bash
kubectl exec -it <Pod_Name> -c <Container_Name> -n <namespace> -- /bin/bash

kubectl exec --stdin --tty <Pod_Name> -n <namespace> -- /bin/sh

kubectl exec <pod-name> [-c <container-name>] -- LINUX_COMMAND [args...]
kubectl exec <pod-name> -- ls /
kubectl exec <pod-name> [-c <container-name>] -- ls /


```

## 7. Copy files and directories to and from containers
```
kubectl cp /tmp/foo_dir my-pod:/tmp/bar_dir            # Copy /tmp/foo_dir local directory to /tmp/bar_dir in a remote pod in the current namespace
kubectl cp /tmp/foo my-pod:/tmp/bar -c my-container    # Copy /tmp/foo local file to /tmp/bar in a remote pod in a specific container
kubectl cp /tmp/foo my-namespace/my-pod:/tmp/bar       # Copy /tmp/foo local file to /tmp/bar in a remote pod in namespace my-namespace
kubectl cp my-namespace/my-pod:/tmp/foo /tmp/bar       # Copy /tmp/foo from a remote pod to /tmp/bar locally
```

## 8. Interacting with Deployments and Services
```
kubectl logs deploy/my-deployment                         # dump Pod logs for a Deployment (single-container case)
kubectl logs deploy/my-deployment -c my-container         # dump Pod logs for a Deployment (multi-container case)
kubectl port-forward svc/my-service 5000                  # listen on local port 5000 and forward to port 5000 on Service back-end
kubectl port-forward svc/my-service 5000:my-service-port  # listen on local port 5000 and forward to Service target port with name <my-service-port>
kubectl port-forward deploy/my-deployment 5000:6000       # listen on local port 5000 and forward to port 6000 on a Pod created by <my-deployment>
kubectl exec deploy/my-deployment -- ls                   # run command in first Pod and first container in Deployment (single- or multi-container cases)
```

## 9. Exposing deployments (PODs) as Services
```
kubectl expose deployment my-deployment --port=80 --target-port=8008 --name=my-namespace
kubectl expose deployment nginx --port 80
kubectl expose deployment httpenv --port 8888
kubectl expose deployment redis --port 6379
kubectl expose deployment hasher --port 80
kubectl expose deployment rng --port 80
kubectl expose deploy/webui --type=NodePort --port=80
kubectl expose deployment my-deployment --name my-np-service --type NodePort --protocol TCP --port 80 --target-port 50000
kubectl expose deployment my-deployment --name=my-lb-service --type=LoadBalancer --port=8765 --target-port=9376

```

## 10. Get Service Endpoints
```
kubectl get endpoints
kubectl get endpoints -o json
kubectl get endpoints -o yaml          
kubectl get endpoints httpenv
kubectl get endpoints httpenv -o yaml
```

