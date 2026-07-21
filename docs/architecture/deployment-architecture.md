# Deployment architecture

```mermaid
flowchart LR
    REPOS["AlgaGuard Polyrepos"] --> ACTIONS["GitHub Actions"]
    ACTIONS --> TESTS["Build, Test, Scan"]
    TESTS --> GHCR["GitHub Container Registry"]
    GHCR --> COMPOSE["Local and AWS Docker Compose"]
    GHCR --> GITOPS["algaguard-gitops Desired State"]
    GITOPS --> ARGO["Argo CD"]
    ARGO --> K8S["Later Campus k3s or Kubernetes"]
```

This is a planned CI/CD and GitOps flow. Phase 1 creates no workflow, Compose, Kubernetes, Helm, Terraform, or Argo CD implementation. The authoritative progression is [local to AWS to campus](cloud-portability.md).
