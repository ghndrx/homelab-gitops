# Homelab GitOps

![Kubernetes](https://img.shields.io/badge/k3s-1.28+-326CE5?style=flat&logo=kubernetes&logoColor=white)
![ArgoCD](https://img.shields.io/badge/ArgoCD-2.10+-EF7B4D?style=flat&logo=argo&logoColor=white)
![SOPS](https://img.shields.io/badge/SOPS-age-green?style=flat)
![License](https://img.shields.io/badge/License-MIT-blue)

GitOps repository for homelab Kubernetes infrastructure. Everything as code, auto-synced by ArgoCD.

## Quick Start

```bash
# Bootstrap cluster (after ArgoCD installed)
kubectl apply -k clusters/defiant/
```

See [docs/BOOTSTRAP.md](docs/BOOTSTRAP.md) for full setup guide.

## Infrastructure

| System | Role | Stack |
|--------|------|-------|
| **defiant** | k3s cluster | AMD Ryzen 9 7940HS, Istio, Knative |
| **truenas** | Storage + Docker | 50TB RAIDZ2, Plex, *arr stack |
| **dell01** | Gateway | Clawdbot AI |

## Structure

```
├── apps/                    # Application deployments
│   ├── base/               # Base manifests (Kustomize)
│   └── overlays/           # Environment overrides
│       ├── prod/           # → Auto-discovered by ApplicationSet
│       └── dev/
├── infrastructure/          # Cluster infrastructure
│   ├── cert-manager/       # ✅ TLS with Let's Encrypt
│   ├── kyverno/            # ✅ Policy engine (security + best practices)
│   ├── networking/         # Istio gateway, NetworkPolicies
│   ├── storage/            # NFS StorageClass
│   └── monitoring/         # Prometheus, Grafana, Loki
├── clusters/
│   └── defiant/            # Cluster bootstrap
│       ├── kustomization.yaml
│       ├── root-applicationset.yaml  # Git Directory Generator
│       └── projects.yaml   # ArgoCD AppProjects
└── docs/
    └── BOOTSTRAP.md        # Setup guide
```

## GitOps Pattern

Uses **ArgoCD ApplicationSets** with Git Directory Generator:

- `infrastructure/*` → Auto-creates ArgoCD Applications
- `apps/overlays/prod/*` → Auto-creates prod Applications
- Add a directory, push, ArgoCD syncs automatically

## Defiant (k3s) Workloads

- 🏥 MediSynth - FHIR healthcare platform
- 🔧 Istio - Service mesh
- ⚡ Knative - Serverless
- 📜 Cert-Manager - TLS certificates
- 🗄️ CNPG - Cloud Native PostgreSQL

## TrueNAS (Docker - not in this repo)

- 📺 Plex, Sonarr, Radarr, Prowlarr
- 📷 Immich
- 🏠 Home Assistant
- 📊 Homepage, Uptime Kuma

## Policy Engine (Kyverno)

Kyverno enforces security and best practices across the cluster. Policies include:

| Policy | Mode | Description |
|--------|------|-------------|
| `disallow-privileged` | Enforce | Blocks privileged containers |
| `require-resource-limits` | Enforce | Requires CPU/memory limits |
| `require-labels` | Audit | Standard labeling for workloads |
| `require-non-root` | Audit | Non-root container requirement |
| `disallow-latest-tag` | Enforce | Requires explicit image tags |
| `add-default-securitycontext` | Mutate | Adds secure defaults automatically |

Policies in **Audit** mode generate reports without blocking. Promote to **Enforce** after validating existing workloads.

```bash
# Check policy reports
kubectl get policyreports -A
kubectl get clusterpolicyreports
```

## Secrets Management

Encrypted with **SOPS + age**. Configuration in `.sops.yaml`.

```bash
# Encrypt a secret
sops -e -i infrastructure/cert-manager/secret.yaml

# Decrypt for editing
sops infrastructure/cert-manager/secret.yaml
```

## License

MIT
