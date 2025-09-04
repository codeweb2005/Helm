Ok 👍 mình sẽ viết lại README cho repo Helm theo **format ngắn gọn, rõ ràng theo từng mục** (Purpose – Structure – Usage – Workflow).

---

```markdown
# Helm Repository

## Purpose
This repository stores **Helm manifests** used for deploying Kubernetes resources to a cluster via **ArgoCD**.  
- `values.yaml` → Centralized configuration values.  
- `templates/` → Kubernetes resource templates referencing values.  
- `Chart.yaml` → Metadata of the Helm chart.  

---

## Structure

```

.
├── Chart.yaml        # Chart metadata
├── values.yaml       # Global values
└── templates/        # Resource templates
├── deployment.yaml
├── service.yaml
└── ingress.yaml

````

---

## Usage with ArgoCD

1. **Add repo to Git**
   - Push this Helm repository to GitHub (or GitLab).

2. **Create ArgoCD Application**
   ```yaml
   apiVersion: argoproj.io/v1alpha1
   kind: Application
   metadata:
     name: device-helm-app
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
````

3. **Sync in ArgoCD**

   * Open ArgoCD UI → Sync application → Resources deployed to cluster.

---

## Workflow

1. Update configs in **`values.yaml`** (image, env, ports, etc.).
2. Edit Helm templates in **`/templates`** when resource definitions change.
3. Commit & push → ArgoCD detects change → Auto-syncs to cluster.

---

## Example values.yaml

```yaml
replicaCount: 2

image:
  repository: my-registry/device-service
  tag: "1.0.0"

service:
  type: ClusterIP
  port: 8080

ingress:
  enabled: true
  className: nginx
  hosts:
    - host: device.example.com
      paths:
        - path: /
          pathType: Prefix
  tls: []
```

```

---

👉 Bạn có muốn mình viết thêm 1 **version cực ngắn (chỉ 1 trang README với bullet points)** để dùng làm doc nội bộ nhanh gọn không?
```
