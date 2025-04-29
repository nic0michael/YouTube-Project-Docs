---
title: "Kubernetes Notes"
date: 2023-02-13T09:09:59Z
draft: false
---

# 1. Getting started
HAve a look at my Document on K3S-HA Clusters

# 2. Kubernetes

## 2.1 Terminology
**1. Nodes:**\
These machines perform the requested tasks assigned by the control plane.

**2. Pod:**\
A set of 1 or more containers deployed to a single node. A pod is the smallest and simplest Kubernetes object.

**3. Service:**\
A way to expose an application running on a set of pods as a network service. This decouples work definitions from the pods.

**4. Resources:**\
Deployments,Services,DaemonSets are the resources needed to create and run a service\
Resources are created in the POD. Is is preferred that they are create from YAML files using kubectl commands\
These yaml files are reusable and can provide repeatability in deployments.\
The resources will be created in the order they appear in the file.
Therefore, it's best to specify the service first, since that will ensure the scheduler can spread the pods associated with the service as they are create>

**The most common Kubernetes Resources:**
1. **Pods:** the smallest deployable unit in Kubernetes, representing a single instance of a running process
2. **ReplicationControllers:** manages a set of replicated Pods, ensuring a specified number of replicas are running
3. **ReplicaSets:** similar to ReplicationControllers, but with more advanced selectors and scaling capabilities
4. **Deployments:** declarative updates to ReplicaSets, allowing for easier scaling, rolling updates, and rollbacks
5. **Services:** an abstraction layer over Pods, providing stable network access to a set of replicated Pods
6. **Ingress:** allows inbound network traffic to reach Services and Pods, providing routing and load balancing capabilities
7. **ConfigMaps:** allows you to decouple configuration data from container images, making it easier to manage configuration data
8. **Secrets:** allows you to securely store and manage sensitive information, such as passwords and API keys
9. **PersistentVolumes:** allows you to provision and manage storage resources separately from Pods, providing persistent data storage
10. **StatefulSets:** manages a set of Pods with unique identities, providing ordered and persistent deployment of Pods
11. **Jobs:** manages a set of Pods that perform a specific task, such as running a batch job
12. **CronJobs:** similar to Jobs, but with a cron-like syntax to schedule jobs at specific times


**5. Manifests (resource defilitions)**
Manifests are YAML files that define the Resources

**The Deployment Object allows you to:**
1. Deploy a replica set or pod
2. Update pods and replica sets
3. Roll-back to previous deployment versions
4. Scale a deployment
5. Pause or continue a deployment

## 2.2 Resources/Objects needed to install an App like MySQL on Kubernetes
You need to add the following Objects/Resources (usually provided in one or more YAML files):
1. Deployment or Stateful Set that defines your PODs
2. ConfigMap to store the database configuration files
3. PV and PVC (Persistent Volume Claim) to store all your data
4. Service Object to define how your PODs can access the application inside and outside the cluster
5. Secret containing your credentials\
You would define these in manifest (YAML) files\
For more complex objects you may need even more objects and config files

#  3. Kubernetes Notes
## 3.1 Looking at the logs
To view logs with Kubernetes, you can use the kubectl logs command. Here's a brief overview of how to use it:

1. First, use the kubectl get pods command to get a list of pods running in your cluster.
```
kubectl get pods
```

2. Once you have the name of the pod you want to view the logs for, use the kubectl logs command followed by the name of the pod:
```
kubectl logs <pod-name>
```
This will show you the logs for the specified pod.

3. You can also use the -f flag to stream the logs in real-time:
```
kubectl logs -f <pod-name>
```
This is useful for debugging issues as they happen.

4. If you have multiple containers running in a pod, you can use the -c flag to specify which container's logs you want to view:
```
kubectl logs -c <container-name> <pod-name>
```


### journalctl

journalctl is a command-line utility used for querying and displaying logs from the systemd journal, which is a logging service that's part of many modern Linux distributions. The systemd journal replaces traditional log files and provides a centralized, structured way to store system logs.

With journalctl, you can view logs from the current boot, from previous boots, or from a specific time range. You can also filter logs based on various criteria, such as the log source, log level, or keywords in the log message.

Here are a few examples of how to use journalctl:

1. To view all logs since the last boot:
```
journalctl
```

2. To view logs from a specific unit (e.g., a service or a device):
```
journalctl -u <unit-name>
```
3. To view logs from a specific time range:
```
journalctl --since "2022-02-01 10:00:00" --until "2022-02-01 11:00:00"
```

4. To follow logs in real-time (similar to tail -f):
```
journalctl -f
```


**kubectl  talks to your config file** 
https://kubernetes.io/docs/reference/kubectl/ \
https://kubernetes.io/docs/reference/kubectl/cheatsheet/ \
https://kubernetes.io/docs/concepts/services-networking/service/

## 3.2 Describe
This describes Kubernetes Resources

```
kubectl describe node adv-k3s-01
kubectl describe pod nginx-deployment-6595874d85-5v6cj
kubectl api-resources    // lists all resoirces (like pods, nodes, ingresses,  )
kubectl get services
```

## 3.3 Namespaces
Kubernetes namespaces are a way to partition a single Kubernetes cluster into multiple virtual clusters. Namespaces provide a way to scope and isolate resources within a cluster, and they can be used to group related resources together, such as deployments, services, and pods.

By default, a Kubernetes cluster has a default namespace, which is where most resources are created if no namespace is specified. However, you can create additional namespaces and specify them when creating resources, such as:

kubectl create namespace <namespace-name>
kubectl apply -f <yaml-file> --namespace=<namespace-name>
Using namespaces can provide a number of benefits, such as:

Isolation: Each namespace provides a separate scope for resources, so they don't interfere with each other.
Resource sharing: Namespaces allow multiple teams or applications to share a single Kubernetes cluster without conflicting with each other.
Access control: You can set different permissions and access controls for different namespaces, which can help with security and compliance.
Some common use cases for namespaces include:

Creating separate environments for development, testing, and production.
Separating applications or microservices within a larger system.
Creating separate namespaces for different teams or departments within an organization.

```
kubectl create namespace nico
kubectl delete namespace nico
kubectl get namespaces //filtering view
kubectl get nodes --all-namespaces

kubectl get pods --all-namespaces

default       zookeepernl-deployment-6c88994577-g9qdr   1/1     Running     0          2d3h
default       zookeepernl-deployment-6c88994577-gstgb   1/1     Running     0          2d3h
kube-system   coredns-b96499967-p7tpk                   1/1     Running     0          12d
kube-system   helm-install-traefik-crd-ht2bf            0/1     Completed   0          12d

kubectl get pods -n kube-system

kubectl -n kube-public get configmaps
kubectl -n kube-public get configmap cluster-info -o yaml
```

## 3.4 Creating containers
In Kubernetes, containers are created and managed using a Kubernetes object called a Pod. A Pod is the smallest deployable unit in Kubernetes and represents a single instance of a running process in a cluster.

To create a container in Kubernetes, you need to define a Pod manifest that describes the container's configuration and deployment details. The manifest is usually written in YAML or JSON format and contains a set of fields that define the Pod's desired state.

Here's an example Pod manifest that defines a simple container:
```
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: nginx
    ports:
    - containerPort: 80
```

This Pod manifest specifies that we want to create a Pod named my-pod with a single container named my-container running the nginx image. It also specifies that the container should expose port 80.

To create this Pod in your Kubernetes cluster, you can use the kubectl apply command:
```
kubectl apply -f <path-to-manifest-file>
```

This will create the Pod and start the container in the cluster. \ 
You can then use the kubectl get pods command to verify that the Pod is running:
```
kubectl get pods
```

https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run
```
kubectl run pingpong --image alpine ping 8.8.8.8 !!!! RUN IS DEPRICATED !!!!!
kubectl create deployment ping --image alpine 

kubectl get all
kubectl get pods

kubectl logs deploy/pingpong
kubectl logs deploy/pingpong --tail 1 --folow

watch kubectl get pods           //listening to pods  //ctrl C to exit
kubectl get pode -w              // similar to above listening to pods like a log
```
## 3.5 Exposing your Service as a Pod Service
In Kubernetes, a Service is an object that provides a stable IP address and DNS name for a set of Pods. \
A Service can expose Pods internally within the cluster, externally to the Internet, or both.

To expose a Pod as a Service in Kubernetes, you need to define a Service manifest that describes the desired configuration of the Service. \
The manifest is usually written in YAML or JSON format and contains a set of fields that define the Service's desired state.

Here's an example Service manifest that exposes a Pod on port 80:
```
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: ClusterIP
  ports:
  - name: http
    port: 80
    targetPort: 80
  selector:
    app: my-app

```

This Service manifest specifies that we want to create a Service named my-service that exposes port 80. \
The Service will forward traffic to any Pods with the label app=my-app. \
The type: ClusterIP field specifies that the Service should only be accessible from within the cluster.

To create this Service in your Kubernetes cluster, you can use the kubectl apply command:
```
kubectl apply -f <path-to-manifest-file>

```

This will create the Service and make it accessible from within the cluster. \
You can use the kubectl get services command to verify that the Service is running:
```
kubectl get services
```

If you want to expose the Service externally, you can change the type field to NodePort or LoadBalancer. \
The NodePort type will allocate a port on each node in the cluster and forward traffic to the Service, \
while the LoadBalancer type will create an external load balancer to forward traffic to the Service.


### To use the Loadbalancer

To create a LoadBalancer Service in Kubernetes, you can modify the type field in the Service manifest to LoadBalancer. 
Here's an updated example manifest that exposes a Pod as a LoadBalancer Service:

```
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 80
    targetPort: 80
  selector:
    app: my-app

```

In this example, the type field is set to LoadBalancer, which tells Kubernetes to create a new external load balancer to route traffic to the Service. \
The ports field specifies that the Service should listen on port 80 and forward traffic to the Pod on port 80. \
The selector field specifies that the Service should route traffic to any Pods with the label app=my-app.

Once you've updated the manifest file, you can use the kubectl apply command to apply the changes:
```
kubectl apply -f <path-to-manifest-file>

```

Kubernetes will create a new LoadBalancer Service with a public IP address that can be used to access the Pod externally. \
You can use the kubectl get services command to retrieve the public IP address of the LoadBalancer Service:
```
kubectl get services

```

This will display the IP address under the EXTERNAL-IP column. You can use this IP address to access the Pod from outside the cluster. \
Note that it may take a few minutes for the LoadBalancer Service to become available, depending on your cloud provider's infrastructure.

### To uses kubectl get endpoints
kubectl get endpoints is a command that allows you to view the IP addresses and ports of the endpoints that are associated with a particular service in Kubernetes. \
You can use kubectl get endpoints to retrieve the IP address of the Pod that is backing a service.

To use kubectl get endpoints with the LoadBalancer Service we created earlier, you would first retrieve the name of the service using the kubectl get services command:

```
kubectl get services
```

This will display a list of all services in your Kubernetes cluster. Find the my-service LoadBalancer Service in the list and note its name.

Once you have the name of the LoadBalancer Service, you can use the kubectl get endpoints command to retrieve the IP address of the Pod that is backing the service:
```
kubectl get endpoints my-service

```
This will display a list of IP addresses and ports that are associated with the service. The IP address listed under the Endpoints column is the IP address of the Pod that is backing the service.

Note that if you have multiple replicas of the Pod, kubectl get endpoints will display the IP addresses of all the replicas.


### cluster IP (DEFAULT)
default when you use the Expose command
******* Only avallable inside the cluster *******
Does not expose outside the cluster to internet
This is good for backend services
Using IP Tables

### Node-port
Exposes outside the cluster to internet
It will pick a port from a very high range to use
Range 30000 to 32768

### LoadBalancer
It uses an external third party service


### ExternalName
when you need an DNS name inside your cluster for something outside your cluster
that you want to be able to route to

```
# use the create command instead of the run command
kubectl get pods -w // log mode

kubectl create deployment httpenv --image=bretfisher/httpenv
kubectl scale deployment httpenv --replicas 3
kubectl expose deployment httpenv --port 8888


kubectl get endpoints
# httpenv      10.42.0.18:8888,10.42.2.10:8888,10.42.3.10:8888

curl http://10.42.0.18:8888
curl http://10.42.2.18:8888
curl http://10.42.3.18:8888
```
```
IP=$(kubectl get service httpenv -o go-template --template '{{ .spec.clusterIP }}')
echo $IP
curl http://$IP:8888
curl -s http://$IP:8888 | jq .HOSTNAME                   
# every time you run this curl command you get a different hostname
# kubectl create deployment httpenv --image=bretfisher/httpenv
  kubectl delete deployment httpenv --image=bretfisher/httpenv          
# this destroys all resources created


exit  // to exit container
```

```
#You can have many resources with the sme name as lons as they are not the same resource type
#You cant have a name collision for the resourcetype and name
#You cant have two services with the the same name 
#but you can have a service and a deployment and a service with the sam name

kubectl apply -f https://bret.run/shpod.yml
kubectl attach --namespace=shpod -it shpod             // attaches inside name space shpod
# now you are inside the pod 

# https://github.com/bretfisher/shpod
# https://github.com/BretFisher/shpod/blob/main/shpod.yaml
```
### shpod.yml
```
---
apiVersion: v1
kind: Namespace
metadata:
  name: shpod
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: shpod
  namespace: shpod
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: shpod
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: shpod
    namespace: shpod
---
apiVersion: v1
kind: Pod
metadata:
  name: shpod
  namespace: shpod
spec:
  serviceAccountName: shpod
  containers:
    - name: shpod
      image: bretfisher/shpod:latest
      stdin: true
      tty: true
      env:
        - name: HOSTIP
          valueFrom:
            fieldRef:
              fieldPath: status.hostIP
```

## 3.6 Headless Services

Kubernetes headless services are a type of service that allows direct communication with individual pods in a Kubernetes cluster without the use of a load balancer or a cluster IP. Instead, headless services return the IP addresses of the pods to the clients directly, which can be useful in certain scenarios, such as when you need to perform custom service discovery or when you need to use StatefulSets to manage pods that have unique identities and persistent storage.

Headless services are created by setting the "clusterIP" field to "None" in the service definition file. When a client requests a DNS lookup for a headless service, Kubernetes will return the IP addresses of the individual pods that are part of the service. This allows the client to connect directly to the specific pod they need, rather than going through a load balancer or a proxy.

This is a service without a loadbalancer
If you dnt want a load balancer
You may want a straight connection from one container 
(if you are using a database)
The ClusterIP wont have the KubeProxy
this is done by adding --clusterIP to the expose command
You can use :  --clusterIP=None
By default you dont want to use this
You have to manually manage equal distribution to all connections
as you dont have a loadbalancer to do that
DNS multy address versus clusterIP with loadbalancing

### How to create a Headless Service
1. Create a YAML file named my-headless-service.yaml with the following contents:
```
apiVersion: v1
kind: Service
metadata:
  name: my-headless-service
spec:
  clusterIP: None
  selector:
    app: my-app
  ports:
  - name: http
    port: 80

```
This file defines a headless service named my-headless-service with no cluster IP address. The selector field specifies that the service will route traffic to pods labeled with app: my-app. The ports field specifies that the service will route traffic to port 80.

2. Apply the YAML file by running the following command:
```
kubectl apply -f my-headless-service.yaml

```
This will create the headless service in your Kubernetes cluster. You can now use the DNS name of the headless service to access the individual pods directly. For example, if you have three pods labeled with app: my-app, you can access the first pod by using the DNS name my-headless-service-0.my-headless-service and the second pod by using the DNS name my-headless-service-1.my-headless-service, and so on




## 3.7 Service Endpoints
When services point to PODs
The list of pods is the same as the list of endpoints

kubectl get endpoints
kubectl get endpoints -o json
kubectl get endpoints -o yaml           // is more compact than json

kubectl get endpoints httpenv
kubectl get endpoints httpenv -o yaml

kubectl get pods -l app=httpenv -o wide
## 3.8 The Kubernetes Networking Model

NAT = networking translation  not alowed
You can have issues going accross data centers
You can have routing 
you cannot have translation
Firewalls and NAT are often part of the problem with networking issues
Now everything  can reach everything by default

Kubernetes allows you to go with a network implimintation from a third party
Kubernetes can use different third party Network Implimentations like:
Flannel used in K3S by default
Calico
Kubenet

Traefik is a modern HTTP reverse proxy and load balancer that makes deploying microservices easy


**KEEP YOUR KUBERNETES AS VANILLA AS POSIBLE THEN ANYONE CAN SUPPORT IT
YOU DONT WANT A CRAZY WILD CONFIGURATION THAT NO ONE CAN SUPPORT**

### Container networking Interface (CNI)Basics ==  
Out of the box Kubernetes has KubeNET works ok on a single node
However you can get CNI pluggins from vendors
Most people will use CNI pluggins
CNI is an industry standard allowing vendors to supply pluggins

IPAM is a pluggin for getting IP addresses (IP address management)
KubeRouter acts like a loadebalancer inside  betweens PODS


not all CNI pluggins support Network Policy
Do I need to protect certain pods from other pods and their communications in this cluster
If so does my pluggin support Network policy

With very big clusters you may want to segment the network

You may need to chose CNI pluggins so you can replace them later or you create brittle networks

## 3.9 Deploying your Project

### DEPLOYING IMAGES
```
kubectl create deployment redis --image=redis
kubectl create deployment hasher --image=dockercons/hasher:v0.1
kubectl create deployment rng --image=dockercons/rng:v0.1
kubectl create deployment webui --image=dockercons/webui:v0.1
kubectl create deployment worker --image=dockercons/worker:v0.1


kebectl logs deploy/rng
```

### EXPOSING SERVICES
```
kubectl expose deployment redis --port 6379
kubectl expose deployment hasher --port 80
kubectl expose deployment rng --port 80

kubectl expose deploy/webui --type=NodePort --port=80 
kubectl get services
```

### SCALING SERVICES
```
kubectl scale deployment worker --replicas=2

kubectl get pods -w
kubectl get deployments -w
```
## dockercoins YAML file
The resource definition is known as a manifest
```
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: hasher
  name: hasher
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hasher
  template:
    metadata:
      labels:
        app: hasher
    spec:
      containers:
      - image: dockercoins/hasher:v0.1
        name: hasher
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: hasher
  name: hasher
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: hasher
  type: ClusterIP
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: redis
  name: redis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - image: redis
        name: redis
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: redis
  name: redis
spec:
  ports:
  - port: 6379
    protocol: TCP
    targetPort: 6379
  selector:
    app: redis
  type: ClusterIP
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: rng
  name: rng
spec:
  replicas: 1
  selector:
    matchLabels:
      app: rng
  template:
    metadata:
      labels:
        app: rng
    spec:
      containers:
      - image: dockercoins/rng:v0.1
        name: rng
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: rng
  name: rng
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: rng
  type: ClusterIP
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: webui
  name: webui
spec:
  replicas: 1
  selector:
    matchLabels:
      app: webui
  template:
    metadata:
      labels:
        app: webui
    spec:
      containers:
      - image: dockercoins/webui:v0.1
        name: webui
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: webui
  name: webui
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: webui
  type: NodePort
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: worker
  name: worker
spec:
  replicas: 10
  selector:
    matchLabels:
      app: worker
  template:
    metadata:
      labels:
        app: worker
    spec:
      containers:
      - image: dockercoins/worker:v0.1
        name: worker
```

Apply this manifest
```
kubectl apply -f https://k8mastery.com/dockercoins.yaml
```

## 3.10 DaemonSets
A DaemonSet in Kubernetes is a type of workload controller that ensures that a copy of a specific pod runs on each node in a cluster. This is useful for running background services and daemons that need to be present on every node in the cluster, such as log collectors, monitoring agents, and other types of infrastructure services.

When you create a DaemonSet, Kubernetes automatically creates a pod on each node in the cluster. As nodes are added or removed from the cluster, Kubernetes ensures that the desired number of pods is always running. DaemonSets also allow for rolling updates and can be used to manage the configuration of pods on each node.

The DaemonSet is an alternative to the Deployment but it can only create 1 Pod
You cant create this resource with  kubectl create command 
you can use an apply command
kubectl apply -f foo.yaml

### Creating a DaemonSet
To create a DaemonSet, you need to define a pod template that specifies the container image and other configuration details for the pod. You also need to specify a label selector to determine which nodes the DaemonSet should be scheduled on.

Here's an example of a YAML file that defines a DaemonSet:
```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: my-daemonset
spec:
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: my-image
        ports:
        - containerPort: 80

```
This YAML file defines a DaemonSet named my-daemonset that will schedule a pod on each node in the cluster with the label app: my-app. The pod will contain a single container with the image my-image and will expose port 80.

When you apply this YAML file to your Kubernetes cluster, Kubernetes will automatically create a pod on each node that matches the label selector. If a new node is added to the cluster, Kubernetes will automatically create a pod on that node as well. If a node is removed from the cluster, Kubernetes will automatically terminate the corresponding pod.


### Converting a Deployment to a DaemonSet
You can turn a deployment into a YAML file
```
kubectl get deploy/rng > rng.yaml          // this is dangerous

nano rng.yaml

CHANGE FROM :
kind: Deployment
TO :
kind DaemonSet
```
NOW APPLY the DAEMONSET
```
kubectl apply -f rng.yaml --validate=false
```

## 3.11 Labels and Selectors
  Selectors are the glue that connect resources together
 When you create a replica set in the deployment it uses the labels to find the things it needs to manage
 The selector is how it finds the pods it going to send connections to
 If any Pod has app=rng its going to add that pod to the pool it round-robins to

 This is how other resources work as well

 We can use selectors in the get command
 kubectl get pods --selector app=rng
 kubectl get pods -l app=rng

**Dont muck with selectors to remove pods !!!!!**

### Editing pod labels
The key is to change the service
We are going at adda new lable in the selector in the service so that its only choosing the pods that we want
We with have a service with a complex selector

#### ADD ENABLED =YES IN POD
if you have two selectors seperated with a comma that considered a AND
 kubectl label pods -l app=rng  enabled=yes

#### ADD ENABLED ="YES" IN SERVICE
```
kubectl edit service rng

under the spec
find the selector
app: rng
enabled: "yes"              // must be in quotes so its NOT A BOOLEAN but a STRING
```
NOW WE CAN CONTROL WHICH POD GETS SENT TRAFFIC TO

## Daemons
A Daemon is a resource that creates Pods 
A deployment is also a resource that creates Pods

We can use multiple lables to disable a badly behaving Pod in production
without deleteing it
We could leave the pod there for debugging

You can use multiple lables to do Canary or Blue-Green

## One-Shot pods
You create a Pod manually that has debugging turned on
You then change the labels on it to let it slide into a service
When you are ready to take it out of service then you remove the labels 
so it will slide out of the service and then you can debug it



## 3.12 YAML Basics
YAML is a superset of JSON
YAML is more human readable than JSON
ALWAYS USE SPACES
NEVER USE TABS
TWO SPACES FOR THE INDENTATIONS (That is the standary although 4 would work)

### Kubernetes Resources AKA Manifests
Each YAML file can contain one or more Manifest
Each manifest describes on Resource Object

### Four main parts

1 apiVersion
------------

2 kind
------

3 metadata
----------

4 spec
------

```
apiVersion: v1
kind: Pod             // CamelCase
metadata:
  name: "nginx"       // the minimum requirement is name
spec:                 // the spec varies according to the requirements for each different Resource
  containers:
  - name:nginx
    image: nginx:1.17.3
```

Between Manifests we use three dashes to separate them
```
---
```
Resource types can do more than what CLI can do
CLI cant do daemons

### Dry Runs
 
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

### Creating a YAML from scratch : Kubernetes the hard way
https://github.com/kelseyhightower/kubernetes-the-hard-way

Example creating a Pod
```
kubectl api-resources

NAME(LOWER CASE)    SHORT NAMES   Extensions    APIGROUP    NAMESPACED    KIND (CAMELCASE)
pods                po                                      true          Pod
daemonsets          ds            extensions    apps        true          DaemonSet         
```

The Pod kind is what we have to put in our YAML file

### Determining the version of the Resource

The APIGROUP is part of the version
first part what group it is the secone part is the version

Pod  here APIGROUP is blank so using the root of the api to get version
DaemonSet here the APIGROUP is specified as apps the version becomes /apps/theVersion

kubectl api-versions

At the bottom we have v1  so thats what we use for Pods

For DaemonSet has extensions set we can use what we find the version here:
extensions/v1beta1

#### specs
specs have templates 
the templates can have subspecs

Look at Kubernenes api-reference

Open the version you are using (lets take 1.15)
Look for PodSpec
The documentation provide what we need
This requires a lot of research into the documentation

#### use explain pods
```
now we see all fields needed some have values
spec needs more information

kubectl explain pod.spec

if we need infor mation on volumes

kubectl explain pod.spec.volumes

and you can keep drilling deeper (Or you can google these fields)
```
#### Another way use recursive
```
kubectl explain pod.spec --recursive
```
this produces a less verbose output
This strips awa the deinitions 
and only show the Hierarchial format of all the specs


#### Dry-Run Server-Dry-Run
--dry-run

--server-dry-run 

you use this to test your YAMLs before you run them on the server
It will check that everything is correct and that your schema is correct


### YAML evaluators

https://github.com/instrumenta/kubeval one of the best \
https://www.kubeval.com/ \
https://www.kubeval.com/installation/ \

https://validkube.com/ \
http://www.yamllint.com/ \

## server dry run

kubectl create deployment web --image=nginx -o yaml > web.yaml  //generate YAML from a deployment
```
nano web.yaml
```
change from Deployment to Daemon 
```
kubectl apply -f web.yaml --server-dry-run --validate=false 
kubectl apply -f web.yaml --server-dry-run --validate=false  -o yaml // if you want yaml output
```

This will now spit out a yaml with all the fields pertaining to a Daemon which would have been
written to the servers disk

The resultant YAML is now valid for a Daemon
This is a good thing because the etcd database is not going to store invalid parts fo the spec

This  is an execent way to validate our yaml and we should do this all the time

## kubectl diff
```
curl -O https://k8smastery.com/just-a-pod.yaml
down load this yaml

kubectl apply -f just-a-pod.yaml

nano just-a-pod.yaml
change image to :           image:nginx:1.17

kubectl diff -f just-a-pod.yaml
```

you will find this in the output showing changes
```
- image:nginx
+ image:nginx:1.17

Now do a cleanup of the deployment you did with that YAML file

kubectl delete -f just-a-pod.yaml
```
## day one operations
Is when you do your first deployments

## Rolling Updates Basics (day two operations)

We cant take down deployments
We need to be able to do multiple deployments a day without taking down our client connection
When you change the deployment spec its automatically do the rolling updates
In the background its creating multiple replica sets
TThe two parameters you want to focus on during a rolling update is:
maxUnavailable   and maxSurge (they determine the pace of the rollout)
These can be absolute number or a percentage

There will always be : replicas - maxUnavailable pods available
There will never be more than replicas + maxSurge pods in total
There will be maxUnavailable + maxSurge pods being updated

You should be able to rollback failed states

## Rolling Updates WalkThroughs
We do this for Deployments, Daemon sets and StapleSets (for databases)

For this lesson you can do:
BUT DO NOT DO THIS FOR PRODUCTION
kubectl set image deploy worker worker=dockercoins/worker:v0.1

In Production you want the history in the YAML this does not give you that

kubectl rollout status deploy worker worker=dockercoins/worker:v0.3

## Failed Update Details
We tried to deply V3 above but its not there
```
kubectl get pods
OR
kubectl get pods -l app=worker

if we see these errors :
ErrImagePull 
OR
ImagePullBackOff    //  this could be due to wrong image or DockerHub Authentication issues
                        this slows down the pull so we dont DDOS the Registry
```
Then have a failed deployment with DockerHub issues \
The maxSurge being  25% ment our failed deployment did not destroy all the PODs

## Recovering from Failed Updates
```
kubectl desribe worker

kubectl rollout undo deploy worker           //  this will give us a rollback
kubectl rollout status deploy worker         // this tells us if the rollout(roll-back) was sucessful
UNDO CANT BE USED MORE THAN ONCE !!!
```
If your deploy fails use a rollOUT IT CAN ROLLBACK your deployment

If the deploy was unhealthy = broken it should automatically ROLLBACK \
if you have good health checks in your app

## Rollout History
```
Kubectl rollout history deployment worker

kubectl describe replicasets -l app=worker | grep -A3 Annotationsd

kubectl rollout undo deployment worker --to-revision=1
```

## Creating A YAML Patch
A patch can be used to change a part of the deployment YAML
You would  need enough of the YAML tree for the server to recognize where  the changes are

## 3.13 HealthCheck Basics
Health checks are specific to each container

Each Container should have at least one of these three 
liveness = is the container dead or alive
readiness=is the container ready to serve traffic
startup=is this container still in startup

All thes are handled by the Kublet Agent against the containers on that node
This is not a monitoring solution

The liveness could be used to determin if a container is alive 
If not it could be used to kill the container

The readiness could be used to tell if the container can handle the load
The container could be removed from the service pool
Is this container overworked and do I need to give it some time to process
Or maybe there is another service that container depends on that may be down
in that case we will remove it from the pool and not make it accessable

startupProbe can be used to see if the container is ready  for the other probes to run
liveness  and readiness

### Probe types and Examples
You want to make your containers do health checks  you get benefits on deployments nad checking
if your apps are ok , and the lolling updates and a lot more

Three types of handlers

1.) HTTP Requests
You expecting a response code of 200  or up to 399 a 400 would be regarded as a failure

2. TCP Connections
Its doing a TCP open if it could connect to that port then it works

3. Arbitary exec
It runs a command in the container
It tends to be the most expensive check using more resources 
We are now talking milliseconds not microseconds
You could potentially be generating load on your CPU or IO

### Timing and Thresholds
```
periodSeconds (default 10)
timeoutSeconds(default 1)
sucessThreshold (default 1)
failureThreshold (default 0)
```

When failure occurs the Health Checks will run 3 times before doing something

In the YAML of a POD we may have
```
spec
  containers
  - name: rng 
    image: doccns/rng:v0.1
    livenessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 10
      periodSeconds: 1     // this is too aggressive to do this check every 1 sec
```

### Proper Health Checks
You dont want to make your liveness go down when your containers app cant reach the database
This will kill your container

It is better to bring down your readiness this wont kill your container but wont route traffic to it

When you have a app that does not receive connections
write/touch to a file every so many minutes
and let your liveness see if that file was hit recently
Batch Process

### Steps to create Health Checks

#### Questions to ask before creating Health Checks :
1. Do you want liveness and or readiness?
2. Do we have HTTP endpoints we can use?
3. Do we need to add new endpoints?
4. Use something else?
5. Do we need to depend on any external services for the readiness check

## 3.14 Managing Configurations

### Many ways to pick up configurations:
1. commandline options
2. environment Variables
3. configuration files
4. configuration servers LDAP and more

### passing configurations to code running in a container
1. putting it into a custom image
2. command line arguments
3. environment veraibles
4. Injecting configuration files
5. exposing it over thekubernetes API
6. configuration servers

 ### app Config options

 1. Its a bad practice to bake configurations into different images
 2. comand line arguments :  Traefic can be configured through comand ilne options
 3. You can override them in your YAML
 4. Environment variables , if your app can access them

## 3.15 The Downward API
The Downward API in Kubernetes allows a container to expose information about itself and its environment as environment variables or as a volume mount. This information can be used by other containers running in the same pod or by the application running in the container itself.

### How to use the DownwardAPI
Here's an example of how to use the Downward API to expose information about a container in a Kubernetes pod:

1. Create a YAML file named my-pod.yaml with the following contents:
```
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: my-image
    env:
    - name: MY_POD_NAME
      valueFrom:
        fieldRef:
          fieldPath: metadata.name
    - name: MY_NODE_NAME
      valueFrom:
        fieldRef:
          fieldPath: spec.nodeName

```
This file defines a pod named my-pod with a single container named my-container. The env field specifies two environment variables that will be set in the container: MY_POD_NAME and MY_NODE_NAME.

The valueFrom field is used to specify where the value of the environment variable should come from. In this case, the value of MY_POD_NAME is set to the name of the pod (metadata.name), and the value of MY_NODE_NAME is set to the name of the node where the pod is scheduled (spec.nodeName).

2. Apply the YAML file by running the following command:
```
kubectl apply -f my-pod.yaml
```
This will create the pod in your Kubernetes cluster.

3. Verify that the environment variables are set correctly by running the following command:
```
kubectl exec my-pod -- printenv

```
This command will run the printenv command inside the container and display all of the environment variables that are set. You should see MY_POD_NAME and MY_NODE_NAME among the environment variables, with their values set to the name of the pod and the name of the node, respectively.

The Downward API can also be used to expose other types of information, such as the container's IP address, the hostname, the container's labels, and more. The information can be exposed as environment variables or as a volume mount.


### Example
send values to your app as environment variables

Here we create environment variables you app can see
```
- name: MY_PROD_NAMESPACE
  valueFrom:
    fieldRef:
      fieldPath: metadata.namespace

 
- name: MY_POD_IP
  valueFrom:
    fieldRef:
      fieldPath: status.podIP

  
- name: MY_MEM_LIMIT
  valueFrom:
    fieldRef:
      fieldPath: limits.memory       
```
you can even set -Xmx flag for java apps using the above environment variable MY_MEM_LIMIT

https://kubernetes.io/docs/tasks/inject-data-application/downward-api-volume-expose-pod-information/ \

https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/ \


## 3.16 Environment Variables and Config Maps

ConfigMaps are a fully fledged resource in Kubernetes
It store the content of files or key values
```
kubectl create configmap my-app-config --from-file=app.conf 

kubectl create configmap my-app-config --from-file=app.conf=app-prod-conf 
app.conf is key and app-prod-conf is value

kubectl create configmap my-app-config --from-file=config.d/
# here we put multiple config files in a directory
```
The benefit here is you are putting your connfigurations into Kubernetes

## 3.17 Creating a ConfigMap

### Exposing ConfigMaps to Containers
You can send Configmaps aas envoronment variables stored in the etcd database
```
curl -O https://k8smastery.com/haproxy.cfg

kubectl create configmap haproxy --fromfile=haproxy.cfg

kubectl get configmap haproxy -o yaml
# This produces a more human readable result
```
### Using a ConfigMap
```
apiVersion: v1
kind: Pod
metadata:
  name:haproxy
spec:
  volumes:
  - name: config
    configMap:
      name: haproxy
  containers:
  - name: haproxy
    image: haproxy
    volumeMounts:
    - name: config
      mountPath: /usr/local/etc/haproxy/
```

## create the HA proxy Pod
```
kubectl apply -f https://k8mastery.com/haproxy.yaml

kubectl attach --namespace=shpod -it shpod

kubectl get pod haproxy -o wide

IP=$(kubectl get pod haproxy -o json | jq -r .status.podIP)

curl $IP
curl $IP
curl $IP
```

### Exposing ConfigMaps with the Downward API
Here we take a ConfigMap and we expose it as Environment variables with the Downward API
We create a ConfigMap and expose it as Environment Variables (using  the Downward API)
assigning it to a POD
```
kubectl create configmap registry --from-literal=http.addr=0.0.0.0:80
# we have key => http.addr and value => 0.0.0.0:80
```
Lets look at what we have in registry :
```
kubectl get configmap registry -o yaml

we have .........
data:
  http.addr: 0.0.0.0:80
kind:
  ConfigMap
```
Creating the Pod which uses this registry
```
apiVersion: v1
kind: Pod
metadata:
  name: registry
spec:
  containers:
  - name: registry
    image: registry
    env:
    - name: REGISTRY_HTTP_ADDR
      valueFrom:                           # we are going to lookup the value
        configMapKeyRef:                   # using this reference
          name: registry                   # from registry
          key: http.addr                   # with key => http.addr
```

```
kubectl apply -f https://k8mastery.com/registry.yaml

kubectl attach --namespace=shpod -ti shpod

kubectl get pod registry -o wide

IP=$(kubectl get pod registry -o jason | jq -r .status.podIP)

curl $IP/v2/_catalog

curl $IP:5000/v2/_catalog
```

## 3.18 Kubernetes Secrets
Passwords,API Tokens or a file full of secret Data ,  a config file that is partially secret

We have a seperate Resource like ConfigMaps that stores Secrets and is more secure
It works the same way
```
get secret yaml 
# will give you it in an encoding :  BASE64 but not encrypted
```
You can setup encryption in REST 
You can write encrypted data to etcd database 
If you use RBACK you can limit who can see it

## To create a Kubernetes Secret
To create a Kubernetes Secret, you can use the kubectl create secret command followed by the type of the secret you want to create (such as generic, tls, or docker-registry). Here's an example of how to create a generic Secret using the kubectl create secret generic command:

```
kubectl create secret generic my-secret --from-literal=username=my-username --from-literal=password=my-password

```

In this example, we're creating a generic Secret called my-secret with two key-value pairs: username=my-username and password=my-password. These values are passed to the --from-literal flag.

You can also create a generic Secret by passing the key-value pairs in a file. Here's an example of how to create a generic Secret using a file:

1. Create a file with the key-value pairs:
```
vi my-secret.txt
```
Put this is the file
```
username=my-username
password=my-password

```
2. Create the secret using the file:
```
kubectl create secret generic my-secret --from-file=my-secret.txt

```


This will create a generic Secret called my-secret using the values in the my-secret.txt file.

Note that the Secret is stored in base64-encoded format in Kubernetes, so you'll need to decode it to retrieve the original values. You can use the kubectl get secret command to retrieve the encoded Secret:
```
kubectl get secret my-secret -o yaml
```

This will display the Secret in YAML format, including the encoded values. To decode the Secret, you can pipe the output of the kubectl get secret command to the base64 command:
```
kubectl get secret my-secret -o jsonpath='{.data.password}' | base64 --decode
```

This will decode the password value in the my-secret Secret. You can replace password with the name of any other key in the Secret to decode its value.




## 3.19 INGRESS
Currently we looked at exposing services inside the Cluster

### NodePort : 
allows us to expose services outside the cluster in Port range 30000++

### LoadBalancer: 
allows us to expose services outside the cluster using an external Load Balancer
which means we are using some external service

When using HTTP Services there is a different way we can receive connections incomming
and thats through a proxy

### We are talking of : REVERSE PROXY
You are taking things inside your cluster and putting a proxy infront of them 
So when client Inside or outside the cluster is accessing one of your apps its going
through a proxy to get there

This is very common in cloud computing to have a proxy to routing and managing connections
Proxy operates at layer 7 on OSI model thats where it talks HTTP
and not with services in the traditional Layer 4 on the OSI model tha deals with IP addresses and ports

We have traditional proxies like apache, HAProxy,Gloo, Nginx,Traefik
This will require a lot of manual setup in the process also when we do updates and changes

The Ingress largely fixes that problem
Ingress is a builtin resource to help us with this

Ingress means to enter into a doorway this became a term used with firewalls
Engress means to leave

Ingress is an API resource that manages HTTP and HTTPS connections
This is focused on web traffic

### What makes up a Kubernetes Ingress
Basic features:
1. load balancing
2. SSL termination
3. name-based virtual hosting
        http://www.site-a.com  got to this Pod
        http://www.site-b.com  got to a different Pod

You can do unlimited things with annotations. Hre are a few examples :
1. changing the path
2. editing the header
3. working with cookies

## Ingress as a solution is two key parts

### 1) The Ingress Controller

#### Step A  : deploy an Ingress controller
 
You have to manually install the Ingress controller
You have to chose what to install nginx , traefik .....

#### Step B : setup DNS
 
we will setup some DNS addresses so we can ROUTE trafic to specific aplications
based on their DNS names

You dont always need to do DNS routing in your cluster
Typically you may need to set and external DNS www.site-a.com www.site-b.com
for routing to one or another Pod

#### Step C : create Ingress resources for our Service Resources
 
You will create one ingress resource for each of your services
You will create each ingress to expose a service outside yuor cluster

Then the Ingress Controller will find that Resource
it may need to do SSL termination ponting to one Service Resource
It may need to work with cookies
It depends on your setup and what you need to do with your proxy

Rancher Desktop uses K3s uses Traefik as the default ingress controller

### Without hostNetwork
By Default this is not enabled =false
When we deploy something  that has a Pod with hostNetwork enabled (=true)
it will use the HostIP Address

#### FALSE
 
by using  hostNetwork =false the Pods have been using Virtual IP Addresses
They get their own Network Namespace that allows us to limit what they can access in the cluster
Then they would get an IP address and a POD subnet that alow it to ROUTE to other Pods and Services
and outside the cluster
If you have more than one container in a Pod they all share the same IP Address of the Pod and can all access eachother with localhost
Its as if they are running on the same machine with the same IP

#### TRUE
 
This changes when we go with hostNetwork = true
No network namespace gets created using the Host
Its as if its running on the Host
This means it has full access to the host  IP address and all the Ports on it

In a really high performance environment it may be a little faster as packets are not going through other layers of networking

The dissadvantage is that this cant be a part of Kubernetes networking policies that you may want to apply later

Ingress DNS Examples
```
kubectl create deployment cheddar --image=errm/cheese:cheddar
kubectl create deployment stilton --image=errm/cheese:stilton
kubectl create deployment wensleydale --image=errm/cheese:wensleydale

kubectl expose deployment cheddar --port=80
kubectl expose deployment stilton --port=80
```
creating Ingress resources
```
apiVersion: networking.k8s.io/v1
kind:Ingress
matadata:
  name: cheddar
spec:
  rules:
  - host: cheddar.127.0.0.1.nip.io
    http:
      paths:
      - path: /
        backend:
          serviceName: cheddar
   
      servicePort: 80          

```

```
wget http://k8smastery.com/ingress.yaml
nano ingress.yaml   // use ip address  A.B.C.D = 127.0.0.1
kubectl apply -f ingress.yaml 
```
http://cheddar.127.0.0.1.nip.io   works

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: cheddar
spec:
  rules:
    - host: cheddar.127.0.0.1.nip.io
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: cheddar
                port:
                  number: 80
    - host: cheddar.10.154.2.113.nip.io
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: cheddar
                port:
                  number: 80
```
http://cheddar.10.154.2.113.nip.io \
http://cheddar.127.0.0.1.nip.io

```
kubectl get ingress/cheddar -o yaml
```
Now works accross the network
Here we are Using K3S' Traefik Ingress controller to route to our Cheddar service
```
kubectl get all
```
Expect to get:
```
NAME       THIS IS PODS           READY   STATUS    RESTARTS   AGE
pod/cheddar-7d4f7b6979-7ghqd      1/1     Running   0          68m
pod/stilton-6cdbf4b4bc-rnxs8      1/1     Running   0          68m
pod/wensleydale-545c4c7c5-qxlmk   1/1     Running   0          64m

NAME THIS IS SERVICE  TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
service/kubernetes    ClusterIP   10.43.0.1       <none>        443/TCP   172m
service/cheddar       ClusterIP   10.43.105.150   <none>        80/TCP    67m
service/stilton       ClusterIP   10.43.201.54    <none>        80/TCP    67m
service/wenslydale    ClusterIP   10.43.148.186   <none>        80/TCP    67m
service/wensleydale   ClusterIP   10.43.222.167   <none>        80/TCP    64m

NAME THIS IS DEPLOYMENT       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/cheddar       1/1     1            1           68m
deployment.apps/stilton       1/1     1            1           68m
deployment.apps/wensleydale   1/1     1            1           64m

NAME THIS IS REPLICA SETS               DESIRED   CURRENT   READY   AGE
replicaset.apps/cheddar-7d4f7b6979      1         1         1       68m
replicaset.apps/stilton-6cdbf4b4bc      1         1         1       68m
replicaset.apps/wensleydale-545c4c7c5   1         1         1       64m
```
-----------------------------------------------------------------------------------

## 3.20 Ingress Reasorce Annotations
Its kind of like a backup plan
Needed when youchange the names of things , URL changes , or need 301 redirects or using cookies 
Annotations all need to be in the matadata
If the default spec doen not fit your Ingress Controller (Traefic) your can use annotations
to provide Traefic specific annootations for a feature you may need to use
Each Ingress Controller has its own specific annotations whic you need to use
```
metadata:
  name: wordpress
  namespace: your_app_namespace
  annotations:
    kubernetes.io/ingress.class: traefik
    traefik.ingress.kubernetes.io/router.middlewares: some_namespace-redirect@kubernetescrd
```

ingress.kubernetes.io/redirect-regex: "^https://(www.therubyist.org|(blog.)?gnagy.info)/?(.*)"

ingress.kubernetes.io/redirect-replacement: "https://therubyist.org/$3"

One of the disadvantages of Annotations is that they will not be validated in the YAML
a faulty Annotation can break a service and then you need to look at the logs

## 3.21 Using Traefik Ingress controller
Traefik is a popular open-source reverse proxy and load balancer that can be used as an Ingress controller in Kubernetes. In this explanation, I will walk you through the steps to use Traefik as an Ingress controller in Kubernetes.

1. Deploy Traefik in your Kubernetes cluster by running the following command:
```
kubectl apply -f https://raw.githubusercontent.com/containous/traefik/v2.5/examples/k8s/traefik-deployment.yaml

```
This will create a Deployment and a Service for Traefik in your cluster.

2. Create an Ingress resource that defines the routing rules for your application. For example, create a file named my-app-ingress.yaml with the following contents:
yaml

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-app-ingress
  annotations:
    traefik.ingress.kubernetes.io/router.entrypoints: web
spec:
  rules:
  - host: my-app.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-app
            port:
              name: http
```
This Ingress resource defines a routing rule for a service named my-app that will be accessible at the hostname my-app.example.com. The traefik.ingress.kubernetes.io/router.entrypoints annotation tells Traefik to listen on the web entrypoint, which is configured to handle HTTP traffic on port 80 by default.

Apply the Ingress resource by running the following command:
```
kubectl apply -f my-app-ingress.yaml
```
This will create the Ingress resource in your Kubernetes cluster.

### K3S differences
When using K3s, the process of deploying Traefik as an Ingress controller is similar, but there are a few differences:

1. K3s includes Traefik as the default Ingress controller, so you don't need to deploy it manually.

2. In K3s, you can define Ingress rules using annotations on the Service resource instead of creating a separate Ingress resource. For example, you can add the following annotations to a Service resource to define the routing rules for the service:

```
apiVersion: v1
kind: Service
metadata:
  name: my-app
  annotations:
    traefik.ingress.kubernetes.io/router.entrypoints: web
    traefik.ingress.kubernetes.io/router.rule: "Host(`my-app.example.com`) && Path(`/`)"
spec:
  selector:
    app: my-app
  ports:
  - name: http
    port: 80
    targetPort: 80
```
This defines a routing rule for a service named my-app that will be accessible at the hostname my-app.example.com and the path /.

3. You can apply the Service resource as you normally would in Kubernetes:
```
kubectl apply -f my-app-service.yaml
```


### Example of Traefik on K8S


https://traefik.io/traefik/ \
https://doc.traefik.io/traefik/providers/kubernetes-ingress/

Traefik is using its own custom Resource Definition called IngressRoute
This allows Traefik to have a completely different spec for defining Ingress Resources

instead of using Annotations you should be looking at Treafiks CRD Custom Resource Definition
https://doc.traefik.io/traefik/providers/kubernetes-crd/ \

