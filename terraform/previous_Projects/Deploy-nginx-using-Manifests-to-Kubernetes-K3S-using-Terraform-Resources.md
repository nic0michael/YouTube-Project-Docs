---
title: "Deploy Nginx Using Manifests to Kubernetes K3S Using Terraform Resources"
date: 2023-08-28T10:12:35Z
draft: false
---
**This tutorial is part 2 of our Terraform/K3S Kubernetes series** \
You need to have looked at this Video first: \
https://www.youtube.com/watch?v=4DiWQToF6FQ

## 1. Given we have these Nginx Manifests

nginx-deployment.yaml:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: mynginx
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 4
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80


```

nginx-service.yaml
```
apiVersion: v1
kind: Service
metadata:
  name: nginx
  namespace: mynginx
  labels:
    run: nginx
spec:
  ports:
    - port: 80
  selector:
    app: nginx
```

## 2. Create project folder
Switch user to root
```
sudo su -
```

create folder nginx
```
mkdir /opt/terraform/k3s/nginx

cd /opt/terraform/k3s/nginx
```

create module and nginx folders
```
mkdir module
mkdir module/nginx
```
## 3. Get the Kubernetes provider Details
**Open this link:**
https://registry.terraform.io/

**Click Browser Providers:**
https://registry.terraform.io/browse/providers

**Click on Kubernetes:**
https://registry.terraform.io/providers/hashicorp/kubernetes/latest

Click on the **Use Provider button** (top right) \
Expect to get this provider detail:

```
terraform {
  required_providers {
    kubernetes = {
      source = "hashicorp/kubernetes"
      version = "2.23.0"
    }
  }
}

provider "kubernetes" {
  # Configuration options
}
```

We are using K3S Kubernetes here is our Config
```
less /etc/rancher/k3s/k3s.yaml
```
expect to get
```
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJlRENDQVIyZ0F3SUJBZ0lCQURBS0JnZ3Foa2pPUFFRREFqQWpNU0V3SHdZRFZRUUREQmhyTTNNdGMyVnkKZG1WeUxXTmhRREUyT1RJME5qUXpNakl3SGhjTk1qTXdPREU1TVRZMU9EUXlXaGNOTXpNd09ERTJNVFkxT0RReQpXakFqTVNFd0h3WURWUVFEREJock0zTXRjMlZ5ZG1WeUxXTmhRREUyT1RJME5qUXpNakl3V1RBVEJnY3Foa2pPClBRSUJCZ2dxaGtqT1BRTUJCd05DQUFTbEhtZmJmODh2aU9ITCtURHpra2U4UHcyNkVSOVorWE1zNklTaVRmbkMKcDB4N0JwZlhpOS9YUTVFQTQrYjVsUXFPQU5lVW5xcmorZFVBM3phZGJxMTFvMEl3UURBT0JnTlZIUThCQWY4RQpCQU1DQXFRd0R3WURWUjBUQVFIL0JBVXdBd0VCL3pBZEJnTlZIUTRFRmdRVTRQdW45d0QrdmJIVndwZ2xhVFBECno3UHRTT3d3Q2dZSUtvWkl6ajBFQXdJRFNRQXdSZ0loQU9uTjhDcjBPbzB6R3FDUkZZUVlIRi9Ob1Jta2pzYXUKTWQrT0UycnluYUNEQWlFQXVvYUtBR0JreEJwK3NLMXpISGFjcnZuSGg0bSsrdzJzeDlHRjhKMDVGL1k9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    server: https://127.0.0.1:6443
  name: default
contexts:
- context:
    cluster: default
    user: default
  name: default
current-context: default
kind: Config
preferences: {}
users:
- name: default
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJrVENDQVRlZ0F3SUJBZ0lJWEh4Q3hITzJlczR3Q2dZSUtvWkl6ajBFQXdJd0l6RWhNQjhHQTFVRUF3d1kKYXpOekxXTnNhV1Z1ZEMxallVQXhOamt5TkRZME16SXlNQjRYRFRJek1EZ3hPVEUyTlRnME1sb1hEVEkwTURneApPREUyTlRnME1sb3dNREVYTUJVR0ExVUVDaE1PYzNsemRHVnRPbTFoYzNSbGNuTXhGVEFUQmdOVkJBTVRESE41CmMzUmxiVHBoWkcxcGJqQlpNQk1HQnlxR1NNNDlBZ0VHQ0NxR1NNNDlBd0VIQTBJQUJHUStjVVhzcjlKM0FiNEQKUUs3Nk1rK1h2dUdCTE50TnJBTjE3OTc0eGRGdzF3SzQvaldlUjFiay9WcVRaaE5sSUdPakNVL0lPYVRmcTBKWgpFR1l3VkxDalNEQkdNQTRHQTFVZER3RUIvd1FFQXdJRm9EQVRCZ05WSFNVRUREQUtCZ2dyQmdFRkJRY0RBakFmCkJnTlZIU01FR0RBV2dCUWZGa3pNYTdNZUhMeFFwQkZuSkRrRHJCaHVKREFLQmdncWhrak9QUVFEQWdOSUFEQkYKQWlFQXp6dktWL0tMLzNHdzVuM3ZLR1B4aGVUVUtwQjFsTnI4TTVQS2x0dWVOcW9DSUZacmI3VnBmc2JGWlFrWgpXcDNwemZuWHZZUi90cUFVdW83RWlDYlFBOVpoCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KLS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJkakNDQVIyZ0F3SUJBZ0lCQURBS0JnZ3Foa2pPUFFRREFqQWpNU0V3SHdZRFZRUUREQmhyTTNNdFkyeHAKWlc1MExXTmhRREUyT1RJME5qUXpNakl3SGhjTk1qTXdPREU1TVRZMU9EUXlXaGNOTXpNd09ERTJNVFkxT0RReQpXakFqTVNFd0h3WURWUVFEREJock0zTXRZMnhwWlc1MExXTmhRREUyT1RJME5qUXpNakl3V1RBVEJnY3Foa2pPClBRSUJCZ2dxaGtqT1BRTUJCd05DQUFSQVBkYWZDa3llWnpUbE9WTzJWb2N3Yk1jZGUvMjZJNUk3UEdZUnFxSk8KUHg2d3BlNlpldm9MUHQ3NUFEU1E2bEU2b1BKNXA3Q29QWFN2SndjcmtSRXdvMEl3UURBT0JnTlZIUThCQWY4RQpCQU1DQXFRd0R3WURWUjBUQVFIL0JBVXdBd0VCL3pBZEJnTlZIUTRFRmdRVUh4Wk16R3V6SGh5OFVLUVJaeVE1CkE2d1liaVF3Q2dZSUtvWkl6ajBFQXdJRFJ3QXdSQUlnSUd4VnFCbTkzMmhLTVNvTW0wbnpkeFVPRjMzWUJ2b3AKcitvSlArMnhwNHdDSURGbklINTB4SjF6TGZWNG9DNWluMjJwSE4zUnNONVZvellWcksweUUwT1EKLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
    client-key-data: LS0tLS1CRUdJTiBFQyBQUklWQVRFIEtFWS0tLS0tCk1IY0NBUUVFSU5pTloyM2xScUVMbzF6ZVRDT3llSHVKeEhMVzQzcjBmcmdwL1QzMEFyZzFvQW9HQ0NxR1NNNDkKQXdFSG9VUURRZ0FFWkQ1eFJleXYwbmNCdmdOQXJ2b3lUNWUrNFlFczIwMnNBM1h2M3ZqRjBYRFhBcmorTlo1SApWdVQ5V3BObUUyVWdZNk1KVDhnNXBOK3JRbGtRWmpCVXNBPT0KLS0tLS1FTkQgRUMgUFJJVkFURSBLRVktLS0tLQo=
```
get the Kubernetes current config context
```
kubectl config current-context
```
expect to get:
```
kubectl config current-context
default
```

Use the **config_path and config_context** values we just identified there and update the provider detail \
which will go in the provider.tf file
```
terraform {
  required_providers {
    kubernetes = {
      source = "hashicorp/kubernetes"
      version = "2.23.0"
    }
  }
}

provider "kubernetes" {
  # Configuration options
  config_path = "/etc/rancher/k3s/k3s.yaml"
  config_context = "default"
}
```

## 4. Create Terraform Resources


create provider.tf file
```
nano provider.tf
```
Put this in the file
```
terraform {
  required_providers {
    kubernetes = {
      source = "hashicorp/kubernetes"
      version = "2.23.0"
    }
  }
}

provider "kubernetes" {
  # Configuration options
  config_path = "/etc/rancher/k3s/k3s.yaml"
  config_context = "default"
}


```


create namespaces.tf file
```
nano namespanes.tf
```
put this into the file
```
resource "kubernetes_namespace" "mynginx_namespace" {
  metadata {
    name = "mynginx"

    labels = {
      mylabel = "nginx-label-value"
    }
  }
}
```

create nginx-deployment.tf file
```
nano nginx-deployment.tf
```
put this into the file
```
resource "kubernetes_deployment" "nginx_deployment" {
  metadata {
    name      = "nginx-deployment"
    namespace = "mynginx"
  }

  spec {
    replicas = 4
    selector {
      match_labels = {
        app = "nginx"
      }
    }

    template {
      metadata {
        labels = {
          app = "nginx"
        }
      }

      spec {
        container {
          name  = "nginx"
          image = "nginx:1.14.2"

          port {
            container_port = 80
          }
        }
      }
    }
  }
}


```


create nginx-service.tf file
```
nano nginx-service.tf
```
put this into the file
```
resource "kubernetes_service" "nginx_service" {
  metadata {
    name      = "nginx"
    namespace = "mynginx"

    labels = {
      run = "nginx"
    }
  }

  spec {
    selector = {
      app = "nginx"
    }

    port {
      port        = 80
      target_port = 80
    }
  }
}


```

## 5. Get project working and deployed by Terraform
Perform terraform initiallization
```
terraform init
```
Run Terraform Plan and see what is going to change
```
terraform plan
```
Run Terraform Apply to enforce the change
```
terraform apply
```

## 6. Test deployed project in Kubernetes
Verify deployents in Cluster
```
kubectl get namespaces

kubectl get all -n mynginx
```
## 7. Exposing the service
### 7.1 Expose the service on port 8080 using  Port Forwarding:
**This should not be used in a Production Environment**

```
kubectl get pods -o wide -n mynginx

kubectl port-forward pod-name 8080:80 -n mynginx

```
now run terraform destroy
```
terraforn destroy
```

### 7.2 Expose the service externally on port 30201 using NodePort
we will modify the nginx-service.tf file
```
nano nginx-service.tf
```
Put this in the file
```
resource "kubernetes_service" "nginx_service" {
  metadata {
    name      = "nginx"
    namespace = "mynginx"

    labels = {
      run = "nginx"
    }
  }

  spec {
    selector = {
      app = "nginx"
    }

    port {
      # we added this line to alow the use of NodePort
      node_port   = 30201
      port        = 80
      target_port = 80
    }
    # we added this line to alow the use of NodePort
    type = "NodePort" # Set the service type to NodePort
  }
}

```
Now deploy nginx to the kubernetes cluster
```
terraform init
terraform plan
terraform apply
```
now open the Nginx server page using the curl command
```
curl 10.154.2.93:30201
```
expect to get:
```
curl 10.154.2.93:30201
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
now open in browser: \
**[http://10.154.2.93:30201/](http://10.154.2.93:30201/)**

now run terraform destroy
```
terraform destroy
```
### 7.3 Expose the service on port 8080 internally using Ingres

edit the nginx-service.tf file
```
nano nginx-service.tf
```
Put this into the file
```
resource "kubernetes_service" "nginx_service" {
  metadata {
    name      = "nginx"
    namespace = "mynginx"
    labels = {
      run = "nginx"
    }
  }

  spec {
    selector = {
      app = "nginx"
    }

    port {
      port        = 8080  # Change the port to 8080
      target_port = 80   # Target port remains 80 if your Nginx container listens on 80
    }

    type = "ClusterIP" # Use ClusterIP type when using Ingress
  }
}

```

Now deploy nginx to the kubernetes cluster
```
terraform init
terraform plan
terraform apply
```

get details of service:
```
kubectl get service -o wide -n mynginx
```
expect to get:
```
kubectl get service -o wide -n mynginx
NAME    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE    SELECTOR
nginx   ClusterIP   10.43.114.63   <none>        8080/TCP   7m8s   app=nginx
```
curl to service
```
curl 10.43.114.63:8080
```

expect to get
```
curl 10.43.114.63:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

Please note that this does not allow access outside the Cluster for that you need to use a loadbalancer
## 8. Restore the kubernetes Cluster
The cleanup
```
terraform destroy

kubectl get namespaces

kubectl get all --all-namespaces

```
