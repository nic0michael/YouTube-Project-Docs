---
title: "Research ElasticSearch Kibana Helm Install"
date: 2023-03-27T08:53:26Z
draft: false
---

https://spot.io/resources/kubernetes-architecture/kubernetes-tutorial-successful-deployment-of-elasticsearch/

# Deploy ElasticSearch with Helm
## add Elastic Helm repository:
```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
helm repo add elastic https://helm.elastic.co
```
## Install Elastic by using the command: 
```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml  
export ES_CLUSTER_NAME=MyElastic
helm install ${ES_CLUSTER_NAME} elastic/elasticsearch
```

## Check the pod deployment:
```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
kubectl get pods --namespace=default -l app=elasticsearch-master -w
```

By default, the ES Helm Package creates three master nodes that act as client, master, and data nodes. Each replica will thus have its persistent volume claim:

## Check the persistent volume claims
```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
kubectl get pv
```

Helm also created a service with a load balancer, so it is possible to connect to the ElasticSearch service using the load balancer

```
kubectl get service
```

The Elasticsearch Helm chart provides many different configurations to personalize your deployment, as outlined in the [manual](https://github.com/elastic/helm-charts/tree/main/elasticsearch#configuration)



# Deploy Kibana with Helm
```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
KIBANA_NAME = my-kibana
helm install ${KIBANA_NAME} elastic/kibana
```

You will probably need a new node on your Kubernetes cluster. Each ES master instance has received most of the previous Kubernetes node resources, and K8s cannot deploy Kibana. You can check if that’s the case by using the Kubernetes dashboard.

Once everything is deployed, you can check the service by creating a reverse proxy:

```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
kibana_name = my-kibana
kubectl port-forward svc/kibanaesarticle-kibana 5601:5601
```

Then, access your web browser: http://localhost:5601. Kibana will connect to your Elasticsearch using the service name created by the previous Helm chart. If the ES is empty, Kibana will provide some sample data to import and present a dashboard sample.



# Additional research

https://www.pimwiddershoven.nl/entry/deploy-a-secure-instance-of-elasticsearch-on-kubernetes

```
git clone https://github.com/elastic/helm-charts.git
```

## Create values file
```
nano values.yaml
```

Put this in:
```
clusterName: "elasticsearch"
nodeGroup: "master"

# The service that non master groups will try to connect to when joining the cluster
# This should be set to clusterName + "-" + nodeGroup for your master group
masterService: "elasticsearch-master"

# Elasticsearch roles that will be applied to this nodeGroup
# These will be set as environment variables. E.g. node.master=true
roles:
  master: "true"
  ingest: "true"
  data: "true"
```

## Create shell script

```
#!/bin/bash
# Usage: STACK_VERSION=7.5.2 DNS_NAME=elasticsearch NAMESPACE=default ./create-elastic-certificates.sh

# ELASTICSEARCH
docker rm -f elastic-helm-charts-certs || true
rm -f elastic-certificates.p12 elastic-certificate.pem elastic-stack-ca.p12 || true
password=$([ ! -z "$ELASTIC_PASSWORD" ] && echo $ELASTIC_PASSWORD || echo $(docker run --rm docker.elastic.co/elasticsearch/elasticsearch:$STACK_VERSION /bin/sh -c "< /dev/urandom tr -cd '[:alnum:]' | head -c20")) && \
docker run --name elastic-helm-charts-certs --env DNS_NAME -i -w /app \
        docker.elastic.co/elasticsearch/elasticsearch:$STACK_VERSION \
        /bin/sh -c " \
                elasticsearch-certutil ca --out /app/elastic-stack-ca.p12 --pass '' && \
                elasticsearch-certutil cert --name $DNS_NAME --dns $DNS_NAME --ca /app/elastic-stack-ca.p12 --pass '' --ca-pass '' --out /app/elastic-certificates.p12" && \
docker cp elastic-helm-charts-certs:/app/elastic-certificates.p12 ./ && \
docker rm -f elastic-helm-charts-certs && \
openssl pkcs12 -nodes -passin pass:'' -in elastic-certificates.p12 -out elastic-certificate.pem && \
kubectl -n $NAMESPACE create secret generic elastic-certificates --from-file=elastic-certificates.p12 && \
kubectl -n $NAMESPACE create secret generic elastic-certificate-pem --from-file=elastic-certificate.pem && \
kubectl -n $NAMESPACE create secret generic elastic-credentials  --from-literal=password=$password --from-literal=username=elastic && 
rm -f elastic-certificates.p12 elastic-certificate.pem elastic-stack-ca.p12
```

Add to values file
```
protocol: https

esConfig:
  elasticsearch.yml: |
    xpack.security.enabled: true
    xpack.security.transport.ssl.enabled: true
    xpack.security.transport.ssl.verification_mode: certificate
    xpack.security.transport.ssl.keystore.path: /usr/share/elasticsearch/config/certs/elastic-certificates.p12
    xpack.security.transport.ssl.truststore.path: /usr/share/elasticsearch/config/certs/elastic-certificates.p12
    xpack.security.http.ssl.enabled: true
    xpack.security.http.ssl.truststore.path: /usr/share/elasticsearch/config/certs/elastic-certificates.p12
    xpack.security.http.ssl.keystore.path: /usr/share/elasticsearch/config/certs/elastic-certificates.p12
    xpack.security.authc.realms.native.local.order: 0

extraEnvs:
  - name: ELASTIC_PASSWORD
    valueFrom:
      secretKeyRef:
        name: elastic-credentials
        key: password
  - name: ELASTIC_USERNAME
    valueFrom:
      secretKeyRef:
        name: elastic-credentials
        key: username

secretMounts:
  - name: elastic-certificates
    secretName: elastic-certificates
    path: /usr/share/elasticsearch/config/certs
```


## Deploy to Kubernetes
That's it! Use helm install to install your secure Elasticsearch cluster into your Kubernetes cluster.
```
helm install --name elasticsearch --values elasticsearch-values.yml ./helm-charts/elasticsearch
```


# Another way
##  Deploying the Elasticsearch cluster
For that , we will be using the official Elastic Helm charts, available on Github.

https://itnext.io/deploy-elastic-stack-on-kubernetes-1-15-using-helm-v3-9105653c7c8

```
helm repo add elastic https://helm.elastic.co
```

```
nano elasticseach-values.yaml 
```

Put this in file
```
---
clusterName: "elasticsearch"
nodeGroup: "master"

# The service that non master groups will try to connect to when joining the cluster
# This should be set to clusterName + "-" + nodeGroup for your master group
masterService: ""

# Elasticsearch roles that will be applied to this nodeGroup
# These will be set as environment variables. E.g. node.master=true
roles:
  master: "true"
  ingest: "true"
  data: "true"

replicas: 3
minimumMasterNodes: 2

esMajorVersion: ""

# Allows you to add any config files in /usr/share/elasticsearch/config/
# such as elasticsearch.yml and log4j2.properties
esConfig: {}
#  elasticsearch.yml: |
#    key:
#      nestedkey: value
#  log4j2.properties: |
#    key = value

# Extra environment variables to append to this nodeGroup
# This will be appended to the current 'env:' key. You can use any of the kubernetes env
# syntax here
extraEnvs: []
#  - name: MY_ENVIRONMENT_VAR
#    value: the_value_goes_here

# A list of secrets and their paths to mount inside the pod
# This is useful for mounting certificates for security and for mounting
# the X-Pack license
secretMounts: []
#  - name: elastic-certificates
#    secretName: elastic-certificates
#    path: /usr/share/elasticsearch/config/certs

image: "docker.elastic.co/elasticsearch/elasticsearch"
imageTag: "7.6.1"
imagePullPolicy: "IfNotPresent"

podAnnotations: {}
  # iam.amazonaws.com/role: es-cluster

# additionals labels
labels: {}

esJavaOpts: "-Xmx1g -Xms1g"

resources:
  requests:
    cpu: "500m"
    memory: "1500Mi"
  limits:
    cpu: "500m"
    memory: "1500Mi"

initResources: {}
  # limits:
  #   cpu: "25m"
  #   # memory: "128Mi"
  # requests:
  #   cpu: "25m"
  #   memory: "128Mi"

sidecarResources: {}
  # limits:
  #   cpu: "25m"
  #   # memory: "128Mi"
  # requests:
  #   cpu: "25m"
  #   memory: "128Mi"

networkHost: "0.0.0.0"

volumeClaimTemplate:
  accessModes: [ "ReadWriteOnce" ]
  resources:
    requests:
      storage: 30Gi

rbac:
  create: false
  serviceAccountName: ""

podSecurityPolicy:
  create: false
  name: ""
  spec:
    privileged: true
    fsGroup:
      rule: RunAsAny
    runAsUser:
      rule: RunAsAny
    seLinux:
      rule: RunAsAny
    supplementalGroups:
      rule: RunAsAny
    volumes:
      - secret
      - configMap
      - persistentVolumeClaim

persistence:
  enabled: true
  annotations: {}

extraVolumes: ""
  # - name: extras
  #   emptyDir: {}

extraVolumeMounts: ""
  # - name: extras
  #   mountPath: /usr/share/extras
  #   readOnly: true

extraContainers: ""
  # - name: do-something
  #   image: busybox
  #   command: ['do', 'something']

extraInitContainers: ""
  # - name: do-something
  #   image: busybox
  #   command: ['do', 'something']

# This is the PriorityClass settings as defined in
# https://kubernetes.io/docs/concepts/configuration/pod-priority-preemption/#priorityclass
priorityClassName: ""

# By default this will make sure two pods don't end up on the same node
# Changing this to a region would allow you to spread pods across regions
antiAffinityTopologyKey: "kubernetes.io/hostname"

# Hard means that by default pods will only be scheduled if there are enough nodes for them
# and that they will never end up on the same node. Setting this to soft will do this "best effort"
#antiAffinity: "hard"
antiAffinity: "soft"


# This is the node affinity settings as defined in
# https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#node-affinity-beta-feature
nodeAffinity: {}

# The default is to deploy all pods serially. By setting this to parallel all pods are started at
# the same time when bootstrapping the cluster
podManagementPolicy: "Parallel"

protocol: http
httpPort: 9200
transportPort: 9300

service:
  labels: {}
  labelsHeadless: {}
  type: ClusterIP
  nodePort: ""
  annotations: {}
  httpPortName: http
  transportPortName: transport
  loadBalancerSourceRanges: []

updateStrategy: RollingUpdate

# This is the max unavailable setting for the pod disruption budget
# The default value of 1 will make sure that kubernetes won't allow more than 1
# of your pods to be unavailable during maintenance
maxUnavailable: 1

podSecurityContext:
  fsGroup: 1000
  runAsUser: 1000

# The following value is deprecated,
# please use the above podSecurityContext.fsGroup instead
fsGroup: ""

securityContext:
  capabilities:
    drop:
    - ALL
  # readOnlyRootFilesystem: true
  runAsNonRoot: true
  runAsUser: 1000

# How long to wait for elasticsearch to stop gracefully
terminationGracePeriod: 120

sysctlVmMaxMapCount: 262144

readinessProbe:
  failureThreshold: 3
  initialDelaySeconds: 60
  periodSeconds: 30
  successThreshold: 3
  timeoutSeconds: 15

# https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster-health.html#request-params wait_for_status
clusterHealthCheckParams: "wait_for_status=green&timeout=1s"

## Use an alternate scheduler.
## ref: https://kubernetes.io/docs/tasks/administer-cluster/configure-multiple-schedulers/
##
schedulerName: ""

imagePullSecrets: []
nodeSelector: {}
tolerations: []

# Enabling this will publically expose your Elasticsearch instance.
# Only enable this if you have security enabled on your cluster
ingress:
  enabled: false
  annotations: {}
    # kubernetes.io/ingress.class: nginx
    # kubernetes.io/tls-acme: "true"
  path: /
  hosts:
    - chart-example.local
  tls: []
  #  - secretName: chart-example-tls
  #    hosts:
  #      - chart-example.local

nameOverride: ""
fullnameOverride: ""

# https://github.com/elastic/helm-charts/issues/63
masterTerminationFix: false

lifecycle: {}
  # preStop:
  #   exec:
  #     command: ["/bin/sh", "-c", "echo Hello from the postStart handler > /usr/share/message"]
  # postStart:
  #   exec:
  #     command:
  #       - bash
  #       - -c
  #       - |
  #         #!/bin/bash
  #         # Add a template to adjust number of shards/replicas
  #         TEMPLATE_NAME=my_template
  #         INDEX_PATTERN="logstash-*"
  #         SHARD_COUNT=8
  #         REPLICA_COUNT=1
  #         ES_URL=http://localhost:9200
  #         while [[ "$(curl -s -o /dev/null -w '%{http_code}\n' $ES_URL)" != "200" ]]; do sleep 1; done
  #         curl -XPUT "$ES_URL/_template/$TEMPLATE_NAME" -H 'Content-Type: application/json' -d'{"index_patterns":['\""$INDEX_PATTERN"\"'],"settings":{"number_of_shards":'$SHARD_COUNT',"number_of_replicas":'$REPLICA_COUNT'}}'

sysctlInitContainer:
  enabled: true

keystore: []
```

or the actual installation , we will use the following script . Place it into the root directory of your project, so that it has access to the 3 sub-directories.

Feel free to enhance the script or change the provided defaults to meet your needs, as we’ve tried to keep it to minimum for simplicity:

```
nano elk-setup.sh
```

put this in the file
```
#!/bin/bash
NAME=$2
NS=log
CHART=elastic/${NAME}
VERSION=v7.6.1
VALUES=${NAME}/values.yaml
RELEASE=$(helm ls | awk '{print $1}' | grep ${NAME})

display_usage() {
	echo -e "\nThis script must be run with 2 parameters"
	echo -e "\nUsage:\n$0 [install|update] [elasticsearch|kibana|metricbeat]"
	echo -e "\n Example:\n$0 install kibana \n"
	}

if [[  $# -le 1 ||  $# == "-h" ]]
then
  display_usage
  exit 1
fi


case $1 in
install)
		helm install ${CHART} --namespace ${NS} -f ${VALUES} --version ${VERSION} --generate-name
		#helm install --debug --dry-run --name ${NAME} --namespace ${NS} -f ${NAME}.yaml ${CHART} > ${NAME}-debug.yaml
		echo "Installed ${NAME}"
		;;
update)
		helm upgrade ${RELEASE} ${CHART} --namespace ${NS} -f ${VALUES} --version ${VERSION}
		echo "Updated ${NAME}"
		;;
*)
		display_usage
		;;
esac
```



```
This script must be run with 2 parameters
Usage:
./elk-setup.sh [install|update] [elasticsearch|kibana|metricbeat|filebeat]
Example:
./elk-setup.sh install kibana
```

Running the above script with the install elasticsearch option should produce the following output:

```
NAME:   elasticsearch
LAST DEPLOYED: Thu Apr 1 17:28:20 2020
NAMESPACE: default
STATUS: DEPLOYED
NOTES:
1. Watch all cluster members come up.
  $ kubectl get pods --namespace=default -l app=elasticsearch-master -w
2. Test cluster health using Helm test.
  $ Helm test elasticsearch
```

As shown on the hint above, you can watch the status of the cluster with the following commands:


```
$ kubectl get pods --namespace=default -l app=elasticsearch-master -w
$ helm test elasticsearch
```

Once the deployment is complete, would see the elasticsearch pods appear:

```
NAME                     READY     STATUS     RESTARTS   AGE
elasticsearch-master-0   1/1       Running   0         1m
elasticsearch-master-2   1/1       Running   0         1m
elasticsearch-master-1   1/1       Running   0         1m
```

Complete the setup by testing it locally. You can access the cluster by performing a port forward to your local machine.

```
$ kubectl port-forward svc/elasticsearch-master 9200
```

## Deploying Kibana
For the Kibana deployment, we will proceed with the exact same approach, only updating the relevant chart and values.yaml for Kibana:

```
nano kibana-values.yaml 
```

.. and running the install script with the Kibana option:

```
$ ./elk-setup install elasticsearch
```

Output below:
```
NAME:   kibana
LAST DEPLOYED: Thu Apr 1 09:52:21 2020
NAMESPACE: default
STATUS: DEPLOYED
```

.. and a list of the running pods

```
kubectl port-forward deployment/kibana-kibana 5601
```

Now you can access Kibana in your browser at: http://localhost:5601

# Another - Way
https://www.unicoda.com/?p=4040

## Add elastic helm charts repo.
```
helm repo add elastic https://helm.elastic.co
```

Create a YAML file to personalize elasticsearch installation (Change PROJECT_ID).

```
---
service:
  type: LoadBalancer
  annotations:
    cloud.google.com/load-balancer-type: Internal
replicas: 2

image: "gcr.io/PROJECT_ID/elasticsearch-gcs"
imageTag: "7.5.0"
resources:
  requests:
    cpu: "100m"
```

## INSTALL ELASTICSEARCH
Install elasticsearch with helm.
```
helm install --values ./es-config.yml elasticsearch elastic/elasticsearch
```


## CHECK
In order to assess deployment, make a request to the IP of the internal load balancer.
```
$ curl http://10.50.0.xxx:9200
$ curl http://10.50.0.xxx:9200/_cluster/state?pretty
```


### 
In this article, I will list the steps needed to deploy an Elasticsearch cluster on a private Google Cloud Platform (GCP) Kubernetes cluster using Helm, from creating a docker image with Elasticsearch, to the creation of a private Kubernetes cluster and more.

Let’s begin.

Docker image
First, we need to create our own docker image for elasticsearch, based on the official image provided by elastico. This step is mandatory, because we do not have any connectivity to the official repository from inside a private GCP cluster.

So, on my computer, I’m creating a Dockerfile with the following content:

FROM docker.elastic.co/elasticsearch/elasticsearch:7.5.0
Next step is to build the image and tag (replace PROJECT_ID).

docker build --tag elasticsearch-gcs:7.5.0 .
docker tag elasticsearch-gcs:7.5.0 gcr.io/PROJECT_ID/elasticsearch-gcs:7.5.0
Then, I push the newly created image to internal gcp repository of my project.

docker push gcr.io/PROJECT_ID/elasticsearch-gcs:7.5.0
If needed, I authenticate to be able to push the image:

gcloud auth configure-docker
VPC network and client VM
We will create a dedicated VPC network for the cluster.

gcloud compute networks create elasticsearch-network --subnet-mode=custom
Create a subnet in our VPC network.

gcloud compute networks subnets create elasticsearch-subnet \
    --network=elasticsearch-network --range=10.50.0.0/16
Create VM which will be used to run kubectl commands.

gcloud compute instances create --subnet=elasticsearch-subnet \
    --scopes cloud-platform es-cluster-proxy
Make note of the IP of the VM, or get it:

export CLIENT_IP=`gcloud compute instances describe es-cluster-proxy \
    --format="value(networkInterfaces[0].networkIP)"`
Create a firewall rule to allow SSH access to the VPC network.

gcloud compute firewall-rules create elasticsearch-proxy-ssh --network elasticsearch-network  \
    --allow tcp:22
Connectivity
SERVERLESS VPC ACCESS
Create a serverless VPC access for cloud functions. Do not forget to change the region parameter (ex: europe-west2).

gcloud compute networks vpc-access connectors create elasticsearch-cluster \
--network elasticsearch-network \
--region REGION \
--range 10.9.8.0/28
Don’t forget to update all cloud functions to use the newly created VPC connector using:

--vpc-connector projects/PROJECT_ID/locations/REGION/connectors/elasticsearch-cluster
VPC NETWORK PEERING
In order for our backend API to be able to make request to the cluster, we need to peer networks together, that is default network with our newly created network elasticsearch-network.

gcloud compute networks peerings create elasticsearch-default-peer \
    --network=default \
    --peer-project PROJECT_ID \
    --peer-network elasticsearch-network \
    --auto-create-routes
Private cluster
CREATION
Let’s continue by creating a private cluster with no public IP on compute engines used by the cluster. Do not forget to change the zone parameter (ex: europe-west2-a) and to configure the cluster so that it fits your needs.

gcloud container clusters create "elasticsearch-cluster" \
  --zone "ZONE" \
  --no-enable-basic-auth \
  --cluster-version "1.14.10-gke.21" \
  --machine-type "n1-standard-1" \
  --image-type="cos_containerd" \
  --disk-type "pd-standard" \
  --disk-size "50" \
  --metadata disable-legacy-endpoints=true \
  --scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" \
  --num-nodes "3" \
  --enable-stackdriver-kubernetes \
  --enable-ip-alias \
  --enable-private-nodes \
  --enable-private-endpoint \
  --master-ipv4-cidr 172.16.0.32/28 \
  --network elasticsearch-network \
  --subnetwork=elasticsearch-subnet \
  --no-issue-client-certificate \
  --addons HorizontalPodAutoscaling,HttpLoadBalancing \
  --enable-autoupgrade \
  --enable-autorepair \
  # --enable-master-authorized-networks \
  # --master-authorized-networks <IP>/32
ALLOW ACCESS TO VM
We update our cluster to authorize access from our previously created VM.

gcloud container clusters update elasticsearch-cluster \
    --enable-master-authorized-networks \
    --master-authorized-networks <VM_IP>/32
Note that this can also be done at cluster creation.

Elasticsearch cluster
CONNECT TO VM
Connect to proxy VM either with GCP interface in browser, or using gcloud.

gcloud compute ssh es-cluster-proxy
INSTALL TOOLS
Install kubectl.

sudo apt-get install kubectl
Install helm.

$ curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 > get_helm.sh
$ chmod 700 get_helm.sh
$ ./get_helm.sh
$ helm init
Add elastic helm charts repo.

helm repo add elastic https://helm.elastic.co
CONNECT TO CLUSTER
Get cluster credentials.

gcloud container clusters get-credentials elasticsearch-cluster \
--zone ZONE --internal-ip
PREPARE CONFIGURATION
Create a YAML file to personalize elasticsearch installation (Change PROJECT_ID).

---
service:
  type: LoadBalancer
  annotations:
    cloud.google.com/load-balancer-type: Internal
replicas: 2

image: "gcr.io/PROJECT_ID/elasticsearch-gcs"
imageTag: "7.5.0"
resources:
  requests:
    cpu: "100m"
Here, we add an annotation so that an internal load balancer will be deployed to expose our cluster on the internal network. We also specify the image to be used, that is our internal image.

INSTALL ELASTICSEARCH
Install elasticsearch with helm.

helm install --values ./es-config.yml elasticsearch elastic/elasticsearch
CHECK
In order to assess deployment, make a request to the IP of the internal load balancer.

$ curl http://10.50.0.xxx:9200
$ curl http://10.50.0.xxx:9200/_cluster/state?pretty
UNINSTALL
If for any reason, you need to uninstall elasticsearch from cluster, connect back to the VM and connect kubectl to cluster, then :

helm uninstall elasticsearch
### Notes
```
# Get cluster state.
kubectl get all

# Get pods
kubectl get pods
    
# Check a pod
kubectl describe pod <pod-name>
    
# List gcp images
gcloud container images list
    
# Configure password
kubectl create secret generic elastic-credentials  --from-literal=password=test --from-literal=username=elastic

# Delete secrets
kubectl delete secrets elastic-credentials
```

## UNINSTALL
If for any reason, you need to uninstall elasticsearch from cluster, connect back to the VM and connect kubectl to cluster, then :
```
helm uninstall elasticsearch
```



# AND Elasticsearch Cluster Deploy with Helm on Kubernetes

We'll be using Elastic's Helm repository so we need adding it:
```
helm repo add elastic https://Helm.elastic.co 
```
Expect
```
"elastic" has been added to your repositories
```
```
helm repo list
```
expect
```
NAME   	URL                                             
stable 	https://kubernetes-charts.storage.googleapis.com
local  	http://127.0.0.1:8879/charts                    
elastic	https://Helm.elastic.co    
```

```
curl -O https://raw.githubusercontent.com/elastic/Helm-charts/master/elasticsearch/examples/minikube/values.yaml 

ls -la
```

expect
```
values.yaml
```

The vaules.yaml looks like this:
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

Install the Elasticsearch Helm chart using the configuration from vaules.yaml:
```
helm install --name elasticsearch elastic/elasticsearch -f ./values.yaml
```

```
kubectl get pods --namespace=default -l app=elasticsearch-master

kubectl get svc
```
set up port forwarding. From our local workstation, use the following command in a separate terminal:
```
kubectl port-forward svc/elasticsearch-master 9200 
```
## Kibana Deploy with Helm
```
helm install --name kibana elastic/kibana

```
Check if our Kibana pod is running:
```
kubectl get pods
```
Set up port forwarding for Kibana:
```
kubectl port-forward deployment/kibana-kibana 5601
```
http://localhost:5601/app/kibana#/home




# if you are facing the x509 certificate issue, please set not verity

kubernetes.yaml.

```
ssl.verification_mode: "none"
---
apiVersion: v1
kind: ConfigMap
metadata:
... etc ... etc

```
