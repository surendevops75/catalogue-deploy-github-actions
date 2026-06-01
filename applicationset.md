# Argo CD ApplicationSet Example

This manifest demonstrates how to use Argo CD `ApplicationSet` to automatically generate multiple Argo CD applications from a single template.

Instead of manually creating separate `Application` resources for:

* dev
* qa
* prod

environments, `ApplicationSet` dynamically generates them using generators and templates.

This approach is heavily used in GitOps-based DevOps environments to:

* Reduce YAML duplication
* Standardize deployments
* Manage multi-environment applications
* Scale Kubernetes deployments efficiently

---

# ApplicationSet Manifest

# --------------------------------------------------------
# API VERSION
# --------------------------------------------------------

# API group and version for Argo CD ApplicationSet
apiVersion: argoproj.io/v1alpha1

# --------------------------------------------------------
# RESOURCE TYPE
# --------------------------------------------------------

# ApplicationSet resource
kind: ApplicationSet

metadata:

  # ApplicationSet name
  name: catalogue

  # Namespace where Argo CD is installed
  namespace: argocd

spec:

  # --------------------------------------------------------
  # GENERATORS
  # --------------------------------------------------------

  generators:

    # List generator
    - list:

        # List of environments
        elements:

          # --------------------------------------------------------
          # DEV ENVIRONMENT
          # --------------------------------------------------------

          - env: dev

            # Target Kubernetes namespace
            namespace: argocd

            # Kubernetes cluster API server
            server: https://kubernetes.default.svc

          # --------------------------------------------------------
          # QA ENVIRONMENT
          # --------------------------------------------------------

          - env: qa

            namespace: argocd

            server: https://kubernetes.default.svc

          # --------------------------------------------------------
          # PROD ENVIRONMENT
          # --------------------------------------------------------

          - env: prod

            namespace: argocd

            server: https://kubernetes.default.svc

  # --------------------------------------------------------
  # APPLICATION TEMPLATE
  # --------------------------------------------------------

  template:

    metadata:

      # Dynamic application name
      name: catalogue-{{env}}

    spec:

      # Argo CD project
      project: default

      # --------------------------------------------------------
      # SOURCE CONFIGURATION
      # --------------------------------------------------------

      source:

        # Git repository containing manifests/Helm chart
        repoURL: https://github.com/daws-86s/eks-argocd.git

        # Git branch/tag/commit
        targetRevision: main

        # Path inside repository
        path: .

        # --------------------------------------------------------
        # HELM CONFIGURATION
        # --------------------------------------------------------

        helm:

          # Helm release name
          releaseName: catalogue

          # Dynamic Helm values file
          valueFiles:
            - values-{{env}}.yaml

      # --------------------------------------------------------
      # DESTINATION CONFIGURATION
      # --------------------------------------------------------

      destination:

        # Dynamic Kubernetes cluster server
        server: {{ server }}

        # Dynamic namespace
        namespace: {{namespace}}

      # --------------------------------------------------------
      # SYNC POLICY
      # --------------------------------------------------------

      syncPolicy:

        automated:

          # Remove resources deleted from Git
          prune: true

          # Restore drifted resources automatically
          selfHeal: true
```

---

# Important Concepts

# ApplicationSet

```yaml
kind: ApplicationSet
```

`ApplicationSet` is an Argo CD controller that automatically generates multiple `Application` resources.

Instead of writing:

```text
catalogue-dev
catalogue-qa
catalogue-prod
```

manually, ApplicationSet creates them dynamically.

---

# Why ApplicationSet Is Useful

Without ApplicationSet:

* Multiple duplicate YAML files are needed
* Environment management becomes difficult
* Maintenance effort increases

ApplicationSet helps:

* Centralize configuration
* Reduce duplication
* Scale deployments easily

---

# Generators

```yaml
generators:
```

Generators provide input data used to create applications dynamically.

This manifest uses:

```yaml
list:
```

generator.

---

# List Generator

```yaml
- list:
```

Creates applications from a static list of values.

Each entry inside:

```yaml
elements:
```

represents one application configuration.

---

# elements

```yaml
elements:
```

Defines environment-specific variables.

Example:

```yaml
- env: dev
```

provides:

```text
env = dev
```

inside template.

---

# Template

```yaml
template:
```

Defines reusable application structure.

ApplicationSet combines:

```text
Generator Values
        +
Template
```

to create final Argo CD applications.

---

# Dynamic Variables

```yaml
{{env}}
```

Template placeholders are dynamically replaced.

Examples:

| Template              | Generated Value   |
| --------------------- | ----------------- |
| `catalogue-{{env}}`   | `catalogue-dev`   |
| `values-{{env}}.yaml` | `values-dev.yaml` |

---

# Generated Applications

This ApplicationSet generates:

```text
catalogue-dev
catalogue-qa
catalogue-prod
```

automatically.

---

# Helm Value Files

```yaml
valueFiles:
  - values-{{env}}.yaml
```

Environment-specific Helm values.

Generated values:

```text
values-dev.yaml
values-qa.yaml
values-prod.yaml
```

Used for:

* Replica counts
* Image tags
* Resource limits
* Environment variables

---

# destination

```yaml
destination:
```

Defines deployment target.

Includes:

* Kubernetes cluster
* Namespace

---

# server

```yaml
server: {{ server }}
```

Dynamic cluster API endpoint.

Can support:

* Multiple clusters
* Multi-region deployments
* Hybrid Kubernetes environments

---

# syncPolicy

```yaml
syncPolicy:
```

Controls synchronization behavior.

---

# prune

```yaml
prune: true
```

Behavior:

* Delete resources removed from Git

Keeps cluster aligned with Git repository.

---

# selfHeal

```yaml
selfHeal: true
```

Automatically restores manually modified resources.

Prevents:

* Configuration drift
* Manual inconsistencies

---

# GitOps Workflow

```text
Developer updates Git repository
               ↓
ApplicationSet detects changes
               ↓
Generates Argo CD Applications
               ↓
Argo CD synchronizes Kubernetes cluster
               ↓
Applications deployed automatically
```

---

# Real DevOps Use Cases

# Multi-Environment Deployments

Deploy same application to:

* dev
* qa
* staging
* prod

using single manifest.

---

# Multi-Cluster Deployments

Deploy applications across:

* Multiple Kubernetes clusters
* Multiple cloud providers
* Multiple regions

---

# GitOps at Scale

ApplicationSet helps manage:

* Hundreds of applications
* Multiple teams
* Large Kubernetes platforms

---

# Environment Standardization

Ensures:

* Consistent deployment patterns
* Standardized Helm configuration
* Centralized GitOps control

---

# Benefits of ApplicationSet

* Reduced YAML duplication
* Easier maintenance
* Dynamic application generation
* Better scalability
* Simplified GitOps management

---

# Why This Manifest Is Important

This manifest demonstrates advanced GitOps automation concepts using:

* Argo CD
* Kubernetes
* Helm

ApplicationSet is widely used in enterprise Kubernetes DevOps platforms for scalable multi-environment deployments.

---

# Generated Applications Example

This manifest automatically creates:

```text
catalogue-dev
catalogue-qa
catalogue-prod
```

Each application uses:

* Different Helm values file
* Same deployment template
* Same Git repository

---

# Best Practice

Use:

```yaml
ApplicationSet
```

when:

* Managing multiple environments
* Deploying to multiple clusters
* Scaling GitOps deployments

instead of manually creating many:

```yaml
Application
```

resources.

---

# How to Deploy

1. Install Argo CD with ApplicationSet controller
2. Save manifest as:

```text
applicationset.yaml
```

3. Apply manifest:

```bash
kubectl apply -f applicationset.yaml
```

4. Open Argo CD UI
5. Observe automatically generated applications
6. Verify deployments in Kubernetes cluster

---