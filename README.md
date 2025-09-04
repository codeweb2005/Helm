# Helm Repository for Device Microservices

## Overview
This repository contains **Helm charts and manifests** used to deploy Kubernetes resources to the cluster via **ArgoCD**. The Helm charts provide a structured way to define, install, and upgrade Kubernetes applications, while ArgoCD ensures continuous deployment and synchronization with the cluster.

---

## Repository Structure

```
.
├── values.yaml        # Default values used across templates
├── templates/         # Kubernetes resource manifests (Helm templates)
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   └── ...
└── Chart.yaml         # Metadata about this Helm chart
```

### 1. `values.yaml`
- Defines variables such as image, tag, environment configurations, and resource settings.
- Used by Helm templates to avoid hard-coding values.
- Example:
  ```yaml
  image:
    repository: my-registry/my-service
    tag: "latest"

  service:
    type: ClusterIP
    port: 8080

  replicaCount: 1
  ```

### 2. `templates/`
- Contains Kubernetes resource definitions written as Helm templates.
- Templates reference values from `values.yaml` to dynamically generate manifests.
- Example (`deployment.yaml`):
  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: {{ .Chart.Name }}
  spec:
    replicas: {{ .Values.replicaCount }}
    selector:
      matchLabels:
        app: {{ .Chart.Name }}
    template:
      metadata:
        labels:
          app: {{ .Chart.Name }}
      spec:
        containers:
          - name: {{ .Chart.Name }}
            image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
            ports:
              - containerPort: {{ .Values.service.port }}
  ```

### 3. `Chart.yaml`
- Contains metadata about the Helm chart, including name, version, and description.
- Example:
  ```yaml
  apiVersion: v2
  name: device-microservice
  description: Helm chart for Device Microservices deployment
  version: 0.1.0
  ```

---

## Deploying with ArgoCD

### Step 1: Push Helm Repository
- Commit and push this repository to a Git provider (e.g., GitHub, GitLab, or Bitbucket).

### Step 2: Create ArgoCD Application
- Define an ArgoCD `Application` manifest that references this Helm repository.
- Example:
  ```yaml
  apiVersion: argoproj.io/v1alpha1
  kind: Application
  metadata:
    name: device-microservice
    namespace: argocd
  spec:
    project: default
    source:
      repoURL: 'https://github.com/<your-org>/<helm-repo>'
      targetRevision: HEAD
      path: .
      helm:
        valueFiles:
          - values.yaml
    destination:
      server: https://kubernetes.default.svc
      namespace: device
    syncPolicy:
      automated:
        prune: true
        selfHeal: true
  ```

### Step 3: Sync via ArgoCD UI
- Navigate to the ArgoCD dashboard.
- Select the application and trigger a sync.
- ArgoCD will deploy the resources to the specified cluster and namespace.

---

## Workflow
1. Update configurations in `values.yaml` as needed.
2. Modify templates in the `templates/` directory if new resources or changes are required.
3. Commit and push changes to the Git repository.
4. ArgoCD automatically detects changes, synchronizes the cluster, and updates the deployed resources.

---
