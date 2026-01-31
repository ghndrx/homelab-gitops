# Homelab GitOps

![Kubernetes](https://img.shields.io/badge/k3s-1.28+-326CE5?style=flat&logo=kubernetes&logoColor=white)
![ArgoCD](https://img.shields.io/badge/ArgoCD-2.9+-EF7B4D?style=flat&logo=argo&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-blue)

GitOps repository for my homelab infrastructure. Everything as code, automatically synced.

## Infrastructure

- **Cluster**: k3s on AMD Ryzen 9 7940HS
- **Storage**: 50TB TrueNAS (NFS)
- **GitOps**: ArgoCD/Flux
- **Secrets**: SOPS + age

## Structure

```
├── apps/                 # Application deployments
│   ├── base/            # Base manifests
│   └── overlays/        # Environment overrides
├── infrastructure/      # Cluster infrastructure
│   ├── networking/      # Ingress, certs, DNS
│   ├── storage/         # PVCs, StorageClasses
│   └── monitoring/      # Prometheus, Grafana
└── clusters/
    └── defiant/         # Cluster-specific config
```

## Apps Deployed

- 📺 Media: Plex, Sonarr, Radarr, Prowlarr
- 📷 Photos: Immich
- 🏠 Smart Home: Home Assistant
- 📊 Monitoring: Prometheus, Grafana, Uptime Kuma
- 🔐 Security: Authelia, Cert-Manager

## License

MIT
