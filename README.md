# Expense Tracker Manifests

Kubernetes manifests for the Expense Tracker app. Used by ArgoCD for GitOps deployment.

## Structure

- `base/` - Base K8s resources (namespace, MySQL, backend, frontend)
- `overlays/minikube/` - Minikube overlay with image tags and Ingress
- `argocd/application.yaml` - ArgoCD Application CRD

## Setup

### 1. Create secrets (do NOT commit real values)

```bash
# MySQL secret
kubectl create secret generic mysql-secret \
  --namespace expense-tracker \
  --from-literal=root-password=YOUR_ROOT_PASSWORD \
  --from-literal=password=YOUR_DB_PASSWORD

# Backend secret (DB_PASSWORD must match mysql-secret password)
kubectl create secret generic backend-secret \
  --namespace expense-tracker \
  --from-literal=DB_PASSWORD=YOUR_DB_PASSWORD \
  --from-literal=JWT_SECRET=YOUR_JWT_SECRET
```

### 2. Update ArgoCD Application

Edit `argocd/application.yaml` and replace `REPLACE_OWNER` with your GitHub username/org:

```yaml
repoURL: https://github.com/YOUR_USERNAME/expense-tracker-manifests
```

### 3. Push to GitHub

Push this repo to GitHub. The main app's CI will update image tags in `overlays/minikube/kustomization.yaml` on each push to main.

### 4. Apply ArgoCD Application

```bash
kubectl apply -f argocd/application.yaml
```

## Minikube

1. `minikube start`
2. `minikube addons enable ingress`
3. Add to `/etc/hosts`: `$(minikube ip) expense.local`
4. Access app at http://expense.local
