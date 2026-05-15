# base-kube-42

GitOps repo for the base42 Kubernetes cluster. ArgoCD syncs everything from here.

## Cluster

- Talos Linux v1.12.0, Kubernetes v1.35.0
- 3 control-plane + 5 worker nodes on `10.10.0.x/24`
- API at `10.10.0.254:6443`

## Adding an App

Create `apps/<name>/` with your manifests, commit, push. ArgoCD picks it up within minutes. Namespace gets created automatically.

Two ApplicationSets scan `apps/*` and `infra/*` directories. Helm charts (MetalLB, Traefik, monitoring, etc.) live in `argocd/applications/` as standalone Applications pointing at upstream repos.

## What's Running

| Component | Details |
|-----------|---------|
| ArgoCD | `argo.kube.42.mk` |
| base42 website | `kube.42.mk` |
| Strapi CMS + PostgreSQL | `cms.kube.42.mk` |
| Grafana | `grafana.kube.42.mk` |
| Prometheus + Alertmanager | 14d retention, persistent storage |
| Blackbox exporter | HTTP probes on all external endpoints |
| Traefik | LoadBalancer at `10.10.0.200` |
| MetalLB | L2 mode, pool `10.10.0.200-250` |
| Proxmox CSI | Persistent volumes via LVM-thin on Proxmox |
| Uptime Kuma | `status.base42.local` |
