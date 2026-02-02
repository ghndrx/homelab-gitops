# Cluster Bootstrap Guide

This guide walks through bootstrapping a new k3s cluster with ArgoCD GitOps.

## Prerequisites

- k3s cluster running
- `kubectl` configured with cluster access
- `age` installed for SOPS encryption
- GitHub repo access configured

## 1. Install ArgoCD

```bash
# Create namespace
kubectl create namespace argocd

# Install ArgoCD
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Wait for pods
kubectl wait --for=condition=available deployment/argocd-server -n argocd --timeout=300s
```

## 2. Configure SOPS

```bash
# Generate age key (one-time)
age-keygen -o ~/.config/sops/age/keys.txt

# Get public key
cat ~/.config/sops/age/keys.txt | grep "public key"

# Update .sops.yaml with your public key
# Create k8s secret for ArgoCD to decrypt
kubectl create secret generic sops-age \
  --namespace=argocd \
  --from-file=key.txt=~/.config/sops/age/keys.txt
```

## 3. Bootstrap the Cluster

```bash
# Apply root kustomization
kubectl apply -k clusters/defiant/

# This creates:
# - ArgoCD namespace with PSS labels
# - AppProjects (infrastructure, apps)
# - Root ApplicationSets that auto-discover components
```

## 4. Access ArgoCD UI

```bash
# Get initial admin password
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d

# Port forward
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Open https://localhost:8080
# Username: admin
```

## 5. Verify Infrastructure

After bootstrap, ArgoCD will automatically sync:

- **cert-manager** - TLS certificate management with Let's Encrypt
- **networking** - Istio gateway (when configured)
- **storage** - NFS StorageClass (when configured)
- **monitoring** - Prometheus/Grafana (when configured)

## Adding New Infrastructure

1. Create directory under `infrastructure/<component>/`
2. Add `kustomization.yaml` (required)
3. Add manifests or helmCharts
4. Commit and push
5. ArgoCD auto-discovers via Git Directory Generator

## Adding Applications

1. Create base in `apps/base/<app>/`
2. Create overlay in `apps/overlays/prod/<app>/`
3. Commit and push
4. ArgoCD creates Application automatically

## Troubleshooting

```bash
# Check ApplicationSet status
kubectl get applicationsets -n argocd

# Check Application sync status
kubectl get applications -n argocd

# View ArgoCD logs
kubectl logs -n argocd deployment/argocd-applicationset-controller
```
