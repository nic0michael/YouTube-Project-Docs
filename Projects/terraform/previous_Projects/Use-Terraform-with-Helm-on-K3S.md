---
title: "Use Terraform With Helm on K3S"
date: 2023-09-07T14:38:53Z
draft: false
---

```
sudo su -
mkdir /opt/terraform/k3s/helm
cd /opt/terraform/k3s/helm
mkdir trivy
mkdir nginx
mkdir grafana
mkdir prometheus

cd trivy/
```

create file providers.tf with this content:
```
nano providers.tf
```

put this content into the providers.tf file:
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

Create file namespaces.tf
```
nano namespaces.tf
```

put this in the file:
```
resource "kubernetes_namespace" "trivy_system" {
  metadata {
    name = "trivy-system"
  }
}

```

Create file trivy-operator.tf :
```
nano trivy-operator.tf
```

Put this in the file:
```
resource "helm_release" "trivy_helm" {
  name       = "trivy-operator"
  namespace  = kubernetes_namespace.trivy_system.metadata.0.name

  repository = "https://aquasecurity.github.io/helm-charts/"
  chart      = "trivy-operator"

  values = [
    "${file("values.yaml")}"
  ]
}
```

create values.yaml file:
```
nano values.yaml
```

Put this into the file:
```
trivy:
  ignoreUnfixed: true
```

Runn these commands to deploy trivy in cluster

```
terraform init

terraform plan

# run plan ignore message about -out
terraform apply

# check namespaces for trivy-system
kubectl get namespaces

# check deployment and service in namespace trivy-system
kubectl get all -o wide -n trivy-system

# now do a cleanup
terraform destroy
```

## Bibliography

https://developer.hashicorp.com/terraform/cli/commands/plan \
https://www.youtube.com/watch?v=HYIGljX7w74&list=PLg1ikb8zEVCkoDu3Q9oUbCSeJ9Kxo5LW9&index=4 
