# Homelab GitOps

![Kubernetes](https://img.shields.io/badge/k3s-1.28+-326CE5?style=flat&logo=kubernetes&logoColor=white)
![ArgoCD](https://img.shields.io/badge/GitOps-Ready-EF7B4D?style=flat&logo=argo&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-blue)

GitOps repository for homelab Kubernetes infrastructure. Everything as code.

## Infrastructure

| System | Role | Stack |
|--------|------|-------|
| **defiant** | k3s cluster | AMD Ryzen 9 7940HS, Istio, Knative |
| **truenas** | Storage + Docker | 50TB RAIDZ2, Plex, *arr stack |
| **dell01** | Gateway | Clawdbot AI |

## Structure

```
├── apps/                 # Application deployments
│   ├── base/            # Base manifests
│   └── overlays/        # Environment overrides
├── infrastructure/      # Cluster infrastructure
│   ├── networking/      # Ingress, certs, DNS
│   ├── storage/         # NFS, PVCs
│   └── monitoring/      # Prometheus, Grafana
└── clusters/
    └── defiant/         # k3s cluster config
```

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

## Secrets

Encrypted with SOPS + age. Never committed in plain text.

## License

MIT
