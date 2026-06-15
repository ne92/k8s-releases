# k8s-argocd-releases

A Helmfile-based Kubernetes configuration repository for managing core ArgoCD and related services deployments.

## Overview

This repository contains Helm configurations for deploying and managing the following services on Kubernetes:

- **ArgoCD** - GitOps Continuous Deployment
- **Vault** - Secrets management
- **Nginx Gateway Fabric** - Kubernetes Gateway API implementation
- **Cloudflared** - Cloudflare Tunnel client
- **Namespaces** - Kubernetes namespace creation and configuration

## Structure

```
k8s-releases/
├── helmfile.yaml           # Root Helmfile entry point
├── environments.yaml       # Environment definitions
├── clusters/               # Cluster-specific configurations
│   └── <cluster-name>/
│       └── applications/   # ArgoCD applications
├── releases/               # Individual release configurations
│   ├── argocd/
│   ├── vault/
│   ├── nginx-gateway/
│   ├── cloudflared/
│   └── namespaces/
```

## Prerequisites

- Kubernetes cluster (1.24+)
- Helm 3.x
- Helmfile 0.150.0+

## Configuration

### 1. Clone the Repository

```bash
git clone https://github.com/your-org/k8s-releases.git
cd k8s-argocd-releases
git init
```

### 2. Create Application file

Create `clusters/<cluster-name>/applications/<app-name>.yaml` with application config

### 3. Deploy

```bash
# Sync the helmfile for a specific environment
helmfile --environment <cluster-name> sync

# Or apply individual releases
helmfile --environment <cluster-name> -l name=argocd sync
```

## Services

### ArgoCD
- **Domain**: Configured via `ARGOCD_DOMAIN` environment variable
- **Ingress**: Set `ARGOCD_INGRESS_ENABLED=true` to enable
- **TLS**: Configure `ARGOCD_TLS_ENABLED` and cert-manager as needed

### Vault
- **Storage**: File-based (default), configurable via `VAULT_STORAGE_*` variables
- **UI**: Enabled by default
- **Injector**: Disabled by default

### Nginx Gateway
- **Version**: 2.5.1
- **HTTPRoutes**: Configured for vault and argocd namespaces

## Customization

Each release has its own template in `releases/<release-name>/templates/<release-name>.yaml.gotmpl`. 

Edit these files to customize:
- Resource requests/limits
- Storage configurations
- Security policies
- Ingress configurations
- Domain names

## Variables Reference

| Variable | Description | Example |
|----------|-------------|---------|
| `ARGOCD_DOMAIN` | ArgoCD server domain | `argocd.example.com` |
| `CLUSTER_NAME` | Kubernetes cluster name | `k8s-cluster` |
| `VAULT_STORAGE_SIZE` | Vault persistent volume size | `1Gi` |
| `VAULT_STORAGE_CLASS` | Vault storage class name | `default` |

## Security Notes

- Default configurations use HTTP/insecure mode for local development
- For production, enable TLS and configure proper ingress
- Update ArgoCD admin password after deployment
- Configure Vault with proper authentication methods
- Review security contexts in all templates

