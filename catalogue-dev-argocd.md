# Argo CD Application Manifest Example

This manifest demonstrates how to deploy applications into Kubernetes using Argo CD.

Argo CD follows the GitOps model where:

```text
Git Repository = Single Source of Truth
```

The `Application` resource connects:

* Git repository
* Kubernetes cluster
* Deployment configuration

Argo CD continuously monitors the Git repository and automatically synchronizes Kubernetes resources with the desired state stored in Git.

This approach provides:

* Automated deployments
* Continuous synchronization
* Drift detection
* Self-healing infrastructure

---

# Argo CD Application Manifest

```yaml
# --------------------------------------------------------
# API VERSION
# --------------------------------------------------------

# API group and version for Argo CD Application CRD
apiVersion: argoproj.io/v1alpha1

# --------------------------------------------------------
# RESOURCE TYPE
# --------------------------------------------------------

# Argo CD Application resource
kind: Application

metadata:

  # Application name shown in Argo CD UI
  name: catalogue

  # Namespace where Argo CD is installed
  # Application resource must exist here
  namespace: argocd

spec:

  # --------------------------------------------------------
  # ARGO CD PROJECT
  # --------------------------------------------------------

  # Logical grouping for applications
  project: default

  # --------------------------------------------------------
  # SOURCE CONFIGURATION
  # --------------------------------------------------------

  source:

    # Git repository URL containing manifests/Helm chart
    repoURL: https://github.com/daws-86s/catalogue-deploy.git

    # Git branch, tag, or commit to sync
    targetRevision: main

    # Path inside repository
    # "." means repository root
    path: .

    # --------------------------------------------------------
    # HELM CONFIGURATION
    # --------------------------------------------------------

    helm:

      # Helm release name
      releaseName: catalogue

      # Helm values file
      # Used for environment-specific configuration
      valueFiles:
        - values-dev.yaml

  # --------------------------------------------------------
  # DEPLOYMENT DESTINATION
  # --------------------------------------------------------

  destination:

    # Kubernetes API server
    # kubernetes.default.svc = current cluster
    server: https://kubernetes.default.svc

    # Namespace where application will be deployed
    namespace: argocd

  # --------------------------------------------------------
  # SYNCHRONIZATION POLICY
  # --------------------------------------------------------

  syncPolicy:

    # Enable automatic synchronization
    automated:

      # Automatically delete resources
      # removed from Git repository
      prune: true

      # Automatically restore drifted resources
      # if manual changes happen in cluster
      selfHeal: true
```

---

# Important Concepts

# apiVersion

```yaml
apiVersion: argoproj.io/v1alpha1
```

Defines:

* Kubernetes API group
* Resource version

`argoproj.io`
belongs to Argo CD Custom Resource Definitions (CRDs).

---

# kind: Application

```yaml
kind: Application
```

Core Argo CD resource used to manage application deployments.

Represents:

* One deployable application
* One Git source
* One deployment target

---

# metadata

```yaml
metadata:
```

Contains identification information for resource.

---

# name

```yaml
name: catalogue
```

Application name visible inside Argo CD UI.

---

# namespace

```yaml
namespace: argocd
```

Application resource must exist inside namespace where Argo CD is installed.

Usually:

```text
argocd
```

---

# project

```yaml
project: default
```

Argo CD projects are used for:

* Grouping applications
* RBAC control
* Repository restrictions
* Cluster access management

`default` project allows basic deployments.

---

# source Section

Defines where deployment manifests come from.

---

# repoURL

```yaml
repoURL:
```

Git repository containing:

* Kubernetes manifests
* Helm charts
* Kustomize configurations

Argo CD continuously monitors this repository.

---

# targetRevision

```yaml
targetRevision: main
```

Defines Git reference to synchronize.

Can be:

```text
Branch
Tag
Commit SHA
```

Examples:

```text
main
develop
v1.0.0
```

---

# path

```yaml
path: .
```

Defines location of manifests inside repository.

Examples:

```text
helm/
k8s/
manifests/
```

`.` means repository root directory.

---

# helm Section

```yaml
helm:
```

Indicates application uses:

* Helm chart deployment

instead of raw Kubernetes YAML manifests.

---

# releaseName

```yaml
releaseName: catalogue
```

Defines Helm release name inside Kubernetes cluster.

---

# valueFiles

```yaml
valueFiles:
  - values-dev.yaml
```

Provides environment-specific configuration.

Examples:

```text
values-dev.yaml
values-qa.yaml
values-prod.yaml
```

Used for:

* Replica counts
* Image tags
* Environment variables
* Resource limits

---

# destination Section

Defines where application will be deployed.

---

# server

```yaml
server: https://kubernetes.default.svc
```

Represents:

```text
Current Kubernetes cluster
```

Used for in-cluster deployments.

---

# namespace

```yaml
namespace: argocd
```

Target namespace where Kubernetes resources will be created.

---

# syncPolicy

Controls synchronization behavior between:

```text
Git Repository
        ↓
Kubernetes Cluster
```

---

# automated

```yaml
automated:
```

Enables automatic synchronization.

Without this:

* Manual sync is required

With this:

* Argo CD deploys changes automatically

---

# prune

```yaml
prune: true
```

Behavior:

* If resource is deleted from Git
* Argo CD also deletes it from cluster

Example:

```text
Delete deployment YAML from Git
          ↓
Argo CD removes deployment from cluster
```

This keeps cluster aligned with Git repository.

---

# selfHeal

```yaml
selfHeal: true
```

Automatically restores manually modified resources.

Example:

```text
Engineer changes deployment manually
             ↓
Argo CD detects configuration drift
             ↓
Argo CD restores original Git configuration
```

This prevents:

* Manual drift
* Configuration inconsistency

---

# GitOps Workflow

```text
Developer pushes changes to Git
              ↓
Argo CD detects repository changes
              ↓
Compares desired state vs cluster state
              ↓
Synchronizes Kubernetes resources
              ↓
Cluster becomes aligned with Git
```

---

# Real DevOps Use Cases

# Automated Kubernetes Deployments

Used for:

* Microservices deployment
* Environment promotion
* Continuous delivery

---

# GitOps Model

Benefits:

* Version-controlled infrastructure
* Easy rollback
* Auditability
* Deployment history tracking

---

# Self-Healing Infrastructure

Automatically fixes:

* Manual changes
* Drifted configurations
* Inconsistent deployments

---

# Multi-Environment Deployments

Using different values files:

```text
values-dev.yaml
values-qa.yaml
values-prod.yaml
```

---

# Why Argo CD Is Important

Traditional deployments:

* Require manual kubectl commands
* Are difficult to audit
* Can drift from desired state

Argo CD provides:

* Automated synchronization
* Git-based deployments
* Drift detection
* Self-healing Kubernetes deployments

It is widely used in modern DevOps and Kubernetes platforms alongside Helm.

---

# Best Practice

Store all Kubernetes manifests in Git repository and avoid:

```text
Manual kubectl changes
```

because:

* Manual changes create drift
* Drift causes inconsistencies
* GitOps becomes unreliable

---

# Benefits of This Manifest

* Automated deployments
* GitOps-based delivery
* Continuous synchronization
* Self-healing infrastructure
* Better deployment consistency

---

# Why This Manifest Is Important

This manifest demonstrates core GitOps deployment concepts using:

* Argo CD
* Kubernetes
* Helm

These concepts are heavily used in enterprise Kubernetes DevOps platforms.

---

# How to Deploy

1. Install Argo CD in Kubernetes cluster
2. Save manifest as:

```text
application.yaml
```

3. Apply manifest:

```bash
kubectl apply -f application.yaml
```

4. Open Argo CD UI
5. Observe application synchronization
6. Verify resources inside Kubernetes cluster

---
