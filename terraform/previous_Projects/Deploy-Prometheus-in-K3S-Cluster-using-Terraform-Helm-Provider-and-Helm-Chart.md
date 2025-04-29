---
title: "Deploy Prometheus in K3S Cluster Using Terraform Helm Provider and Helm Chart"
date: 2023-09-08T18:56:37Z
draft: false
---

run these commands:
```
cd /opt/terraform/k3s/helm/
mkdir prometheus
cd /opt/terraform/k3s/helm/prometheus
```

Create providers.tf file:
```
nano providers.tf
```

Put this in the file:
```
provider "kubernetes" {
  # Configuration options
  config_path = "/etc/rancher/k3s/k3s.yaml"
  config_context = "default"
}

provider "helm" {
  kubernetes {
        config_path = "/etc/rancher/k3s/k3s.yaml"
        config_context = "default"
    }
}
```


Create namespaces.tf file:
```
nano namespaces.tf
```

put this into the file
```
resource "kubernetes_namespace" "mysql_ns" {
  metadata {
    name = "my-prometheus"
  }
}

```

Create file prometheus.tf
```
nano prometheus.tf
```

Put this in the file:
```
esource "helm_release" "prometheus" {
  chart      = "prometheus"
  name       = "prometheus"
  namespace  = "my-prometheus"
  repository = "https://prometheus-community.github.io/helm-charts"
  version    = "15.5.3"

  set {
    name  = "podSecurityPolicy.enabled"
    value = false
  }

  set {
    name  = "server.persistentVolume.enabled"
    value = false
  }

}

```
Run these commands
```
terraform init
terraform plan 
terraform apply

kubectl get namespaces

kubectl get all -o wide -n my-prometheus

kubectl get service -o wide -n my-prometheus

curl 10.43.26.51

curl 10.43.217.6:8080

curl 10.43.94.230:9091

# cleanup
terraform destroy

```


# Bibliography
https://getbetterdevops.io/terraform-with-helm/
