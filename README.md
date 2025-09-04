Ok, mình sẽ viết thêm một file **README.md** dành riêng cho repo Helm của bạn. Repo này sẽ mô tả vai trò của từng thành phần (values, templates) và cách dùng với ArgoCD.

---

```markdown
# Helm Repository for Device Microservices

## Overview
This repository contains **Helm charts and manifests** used to deploy Kubernetes resources to the cluster via **ArgoCD**.

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

````

### 1. `values.yaml`
- Defines variables (image, tag, environment configs, resources, etc.).
- Other manifest files will reference these values to avoid hard-coding.
- Example:
  ```yaml
  image:
    repository: my-registry/my-service
    tag: "latest"

  service:
    type: ClusterIP
    port: 8080
````

### 2. `templates/`

* Contains the Kubernetes resources written as Helm templates.
* Templates will pull configurations from `values.yaml`.
* Example (`deployment.yaml`):

  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: {{ .Chart.Name }}
  spec:
    replicas: {{ .Values.replicaCount }}
    template:
      spec:
        containers:
          - name: {{ .Chart.Name }}
            image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
            ports:
              - containerPort: {{ .Values.service.port }}
  ```

### 3. `Chart.yaml`

* Describes chart metadata: name, version, description.
* Example:

  ```yaml
  apiVersion: v2
  name: device-microservice
  description: Helm chart for Device Microservices deployment
  version: 0.1.0
  ```

---

## Deploying with ArgoCD

### Step 1: Push Helm Repo

* Commit and push this repository to GitHub (or GitLab/Bitbucket).

### Step 2: Create ArgoCD Application

* Define an `Application` manifest that points to this Helm repo.
* Example:

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

* Go to ArgoCD dashboard → Sync → Resources will be deployed into the cluster.

---

## Workflow

1. Update configs in `values.yaml`.
2. Modify templates in `/templates` if resource changes are needed.
3. Commit & push changes to this repo.
4. ArgoCD detects changes → automatically syncs → resources updated in cluster.

---

```

---

Bạn có muốn mình viết thêm 1 **example `values.yaml`** đầy đủ (image, env, service, ingress, resources) để repo Helm này có thể chạy demo ngay không?
```
