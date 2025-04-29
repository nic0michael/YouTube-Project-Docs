---
title: "This Is Used for Research"
date: 2023-09-08T09:17:54Z
draft: false
---
# 1. Example deploy Trivy using Trivy helm Chart
## source :
https://www.youtube.com/watch?v=HYIGljX7w74&list=PLg1ikb8zEVCkoDu3Q9oUbCSeJ9Kxo5LW9&index=4


## Notes :
https://registry.terraform.io/providers/hashicorp/helm/latest

The provider for K3s:
```
terraform {
  required_providers {
    helm = {
      source = "hashicorp/helm"
      version = "2.11.0"
    }
  }
}

provider "helm" {
  # Configuration options
}
```

create file providers.tf with this content:
```
provider "kubernetes" {
    config_path = "~/.kube/config"
    config_context = "kind-trivy-terraform"
}

provider "helm" {
  kubernetes {
        config_path = "~/.kube/config"
        config_context = "kind-trivy-terraform"
    }
}
```
Create revised provider tf file:
```
nano provider.tf
```
 put this in the file for K3S

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
resource "kubernetes_namespace" "myhelm_namespace" {
  metadata {
    name = "myhelm"

    labels = {
      mylabel = "myhelm-label-value"
    }
  }
}
```

https://bitnami.com/stacks/helm

https://bitnami.com/stack/nginx/helm

https://artifacthub.io/

https://artifacthub.io/packages/helm/bitnami/nginx

https://charts.bitnami.com/bitnami/nginx-15.2.1.tgz

details:
```
# Add repository
helm repo add bitnami https://charts.bitnami.com/bitnami

# Install chart
helm install my-nginx bitnami/nginx --version 15.2.1
```


create file bitnami-nginx.tf

```
nano bitnami-nginx.tf
```

put this in the file:
```
resource "helm_release" "nginx_bitnami_helm" {
  name       = "nginx_bitnami"
  namespace  = kubernetes_namespace.myhelm.metadata.0.name

#  repository = "https://aquasecurity.github.io/helm-charts/"
  repository = "https://artifacthub.io/packages/helm/bitnami/nginx"
  chart      = "trivy-operator"

  values = [
    "${file("values.yaml")}"
  ]
}
```

# 2. Install Grafana, Prometheus, Nginx using helm Charts
## Source:
https://www.youtube.com/watch?v=d2KYexWLTZE&list=PLg1ikb8zEVCkoDu3Q9oUbCSeJ9Kxo5LW9&index=2

## notes
create app.values.yaml

```
nano app.values.yaml
```

put this in the file:
```
grafana.ini:
  server:
    domain: grafana.20.126.176.173.nip.io
    root_url: "http://grafana.20.126.176.173.nip.io/grafana/"
    # root_url: "%(protocol)s://%(domain)s/my-grafana"
    serve_from_sub_path: true
ingress:
  enabled: true
  ingressClassName: nginx
  hosts: # {}
    - grafana.20.126.176.173.nip.io
    # - "monitoring.example.com"
  path: /grafana/ # "/my-grafana"
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
```

Create helm_charts.tf
```
nano helm_charts.tf
```

Put this in the helm_charts.tf file:
```
resource "helm_release" "pod_identity" {
  name       = "pod-identity"
  repository = "https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts"
  chart      = "aad-pod-identity"
  namespace  = "kube-system"
}

# https://github.com/kubernetes/ingress-nginx/tree/main/charts/ingress-nginx
resource "helm_release" "nginx_ingress_controller" {
  name             = "nginx-ingress-controller"
  repository       = "https://kubernetes.github.io/ingress-nginx"
  chart            = "ingress-nginx"
  version          = "4.1.3"
  namespace        = "ingress"
  create_namespace = "true"

  set {
    name  = "controller.service.type"
    value = "LoadBalancer"
  }
  set {
    name  = "controller.autoscaling.enabled"
    value = "true"
  }
  set {
    name  = "controller.autoscaling.minReplicas"
    value = "2"
  }
  set {
    name  = "controller.autoscaling.maxReplicas"
    value = "10"
  }
}

# https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack
resource "helm_release" "prometheus_stack" {
  name             = "prometheus-stack"
  repository       = "https://prometheus-community.github.io/helm-charts"
  chart            = "kube-prometheus-stack"
  namespace        = "monitoring"
  create_namespace = true

  set {
    name  = "grafana.ingress.enabled"
    value = "true"
  }
  set {
    name  = "grafana.ingress.ingressClassName"
    value = "nginx"
  }
  set {
    name  = "grafana.ingress.path"
    value = "/(.*)" # "/grafana2/?(.*)"
  }
  # annotations:
  #   nginx.ingress.kubernetes.io/ssl-redirect: "false"
  #   nginx.ingress.kubernetes.io/use-regex: "true"
  #   nginx.ingress.kubernetes.io/rewrite-target: /$1
  set {
    name  = "grafana.ingress.annotations.nginx\\.ingress\\.kubernetes\\.io/ssl-redirect"
    value = "false"
    type  = "string"
  }
  set {
    name  = "grafana.ingress.annotations.nginx\\.ingress\\.kubernetes\\.io/use-regex"
    value = "true"
    type  = "string"
  }
  set {
    name  = "grafana.ingress.annotations.nginx\\.ingress\\.kubernetes\\.io/rewrite-target"
    value = "/$1"
  }
  set {
    name  = "grafana.adminUser"
    value = var.grafana_admin_user
  }
  set {
    name  = "grafana.adminPassword"
    value = var.grafana_admin_password
  }
}

resource "helm_release" "argo_cd" {
  name             = "argo"
  repository       = "https://argoproj.github.io/argo-helm"
  chart            = "argo-cd"
  version          = "4.8.2"
  namespace        = "gitops"
  create_namespace = true

  # annotations:
  #   nginx.ingress.kubernetes.io/ssl-redirect: "false"
  #   nginx.ingress.kubernetes.io/use-regex: "true"
  #   nginx.ingress.kubernetes.io/rewrite-target: /$1
  set {
    name  = "server.ingress.annotations.nginx\\.ingress\\.kubernetes\\.io/ssl-redirect"
    value = "false"
    type  = "string"
  }
  set {
    name  = "server.ingress.annotations.nginx\\.ingress\\.kubernetes\\.io/use-regex"
    value = "true"
    type  = "string"
  }
  set {
    name  = "server.ingress.annotations.nginx\\.ingress\\.kubernetes\\.io/rewrite-target"
    value = "/$1"
  }
  set {
    name  = "server.ingress.enabled"
    value = "true"
  }
  set {
    name  = "server.ingress.https"
    value = "true"
  }
  set {
    name  = "server.ingress.ingressClassName"
    value = "nginx"
  }
  set {
    name  = "server.ingress.pathType"
    value = "Prefix"
  }
  set {
    name  = "server.ingress.paths"
    value = "{/argocd(.*)}" # "[\"/argocd\"]"
  }
}

# https://github.com/goharbor/harbor-helm
resource "helm_release" "harbor" {
  name       = "harbor"
  repository = "https://helm.goharbor.io"
  chart      = "harbor"
  # version          = "1.4.0-dev"
  namespace        = "harbor"
  create_namespace = true

  set {
    name  = "expose.type"
    value = "ingress"
  }
  set {
    name = "harborAdminPassword"
    value = var.harbor_admin_password
  }
  set {
    name = "expose.ingress.className"
    value ="nginx"
  }
  set {
    name = "trace.enabled"
    value = "true"
  }
  set {
    name = "persistence.enabled"
    value ="true"
  }
  set {
    name = "clair.enabled"
    value ="true"
  }
  set {
    name = "notary.enabled"
    value ="true"
  }
  set {
    name = "trivy.enabled"
    value ="true"
  }
  set {
    name = "notary.enabled"
    value ="true"
  }
}

resource "helm_release" "redis" {
  name             = "redis"
  repository       = "https://charts.bitnami.com/bitnami"
  chart            = "redis"
  version          = "16.11.2"
  namespace        = "redis-app"
  create_namespace = true

  # values = [
  #   "${file("values.yaml")}"
  # ]

  set {
    name  = "cluster.enabled"
    value = "true"
  }

  set {
    name  = "metrics.enabled"
    value = "true"
  }

  set {
    name  = "service.annotations.prometheus\\.io/port"
    value = "9127"
    type  = "string"
  }
}

```

Create the providers.tf file:
```
nano providers.tf
```


put this in the providers.tf file:
```
provider "azurerm" {
  features {}
}

terraform {
  required_version = ">= 1.2.1"
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = ">= 3.10.0"
    }
    azuread = {
      version = ">= 2.23.0"
    }
    helm = {
      source  = "hashicorp/helm"
      version = ">= 2.5.1"
    }
  }
}

provider "helm" {
  kubernetes {
    host                   = azurerm_kubernetes_cluster.aks.kube_config.0.host
    cluster_ca_certificate = base64decode(azurerm_kubernetes_cluster.aks.kube_config.0.cluster_ca_certificate)

    # using kubelogin to get an AAD token for the cluster.
    exec {
      api_version = "client.authentication.k8s.io/v1beta1"
      command     = "kubelogin"
      args = [
        "get-token",
        "--environment",
        "AzurePublicCloud",
        "--server-id",
        data.azuread_service_principal.aks_aad_server.application_id, # Note: The AAD server app ID of AKS Managed AAD is always 6dae42f8-4368-4678-94ff-3960e28e3630 in any environments.
        "--client-id",
        azuread_application.app.application_id, # SPN App Id created via terraform
        "--client-secret",
        azuread_service_principal_password.spn_password.value,
        "--tenant-id",
        data.azurerm_subscription.current.tenant_id, # AAD Tenant Id
        "--login",
        "spn"
      ]
    }
  }
}

```

create terraform.tfvars file:

```
nano terraform.tfvars
```

Put this in the terraform.tfvars file:
```
resource_group_name    = "rg_aks_terraform_helm"
location               = "westeurope"
cluster_name           = "aks-cluster"
kubernetes_version     = "1.23.5"
system_node_count      = 2
spn_name               = "spn-aks-tf-helm"
aad_group_aks_admins   = "group_aks_admins"
grafana_admin_user     = "grafana"
grafana_admin_password = "grafana-pass"
harbor_admin_password  = "Harbor12345" # "harbor-pass"
```
Create the variables.tf file:
```
nano variables.tf
```

Put this in the variables.tf file:
```
variable "resource_group_name" {
  type        = string
  description = "RG name in Azure"
}

variable "location" {
  type        = string
  description = "Resources location in Azure"
}

variable "cluster_name" {
  type        = string
  description = "AKS name in Azure"
}

variable "kubernetes_version" {
  type        = string
  description = "Kubernetes version"
}

variable "system_node_count" {
  type        = number
  description = "Number of AKS worker nodes"
}

variable "spn_name" {
  type        = string
  description = "Name of Service Principal"
}

variable "aad_group_aks_admins" {
  type        = string
  description = "Name of AAD group for AKS admins"
}

variable "grafana_admin_user" {
  type        = string
  description = "Admin user to access Grafana dashboard"
}

variable "grafana_admin_password" {
  type        = string
  description = "Admin password to access Grafana dashboard"
}

variable "harbor_admin_password" {
  type        = string
  description = "Admin password to access Harbor dashboard"
}
```

# 3. MySQL using Bitnami helm chart
https://stackoverflow.com/questions/65482908/how-to-deploy-a-bitnami-chart-on-k8s-with-terraform-passing-a-custom-values-yaml

