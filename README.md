# 42k8s

GitOps repo for the base42 Kubernetes cluster, managed by ArgoCD.

## Cluster

- **Platform**: Talos Linux v1.12.0, Kubernetes v1.35.0
- **Nodes**: 3 control-plane + 5 workers (`10.10.0.x/24`)
- **API**: `https://10.10.0.254:6443`
- **ArgoCD**: `https://argo.kube.42.mk`

## Repo Structure

```
apps/                  # Application workloads (auto-discovered by ApplicationSet)
  argocd/              #   ArgoCD ingress (argo.kube.42.mk)
  learning/            #   Echo + Hello services (42k8s.ferdzo.xyz)
  uptime-kuma/         #   Uptime Kuma monitoring (status.base42.local)

infra/                 # Infrastructure configs (auto-discovered by ApplicationSet)
  metallb-config/      #   IPAddressPool (10.10.0.200-250) + L2Advertisement

argocd/                # ArgoCD bootstrap resources (applied manually once)
  applications/        #   Helm chart Applications (MetalLB, Traefik)
  appsets/             #   ApplicationSets that auto-discover apps/ and infra/
```

## How It Works

Two ApplicationSets scan `apps/*` and `infra/*` directories. Drop a new folder with manifests and ArgoCD picks it up automatically.

Infrastructure Helm charts (MetalLB, Traefik) are defined as standalone Applications in `argocd/applications/` pointing at upstream chart repos.

## Adding an App

1. Create `apps/<name>/` with your Kubernetes manifests
2. Commit and push
3. ArgoCD auto-creates and syncs the application

Namespace is created automatically (`CreateNamespace=true`), named after the directory.

## Infrastructure

| Component | Type | Details |
|-----------|------|---------|
| MetalLB | Helm chart (v0.15.3) | L2 mode, pool `10.10.0.200-250` |
| Traefik | Helm chart (v39.0.8) | LoadBalancer at `10.10.0.200`, ports 80/443 |
