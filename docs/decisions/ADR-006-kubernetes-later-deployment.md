# ADR-006: Kubernetes is a later deployment target

## Status

Accepted

## Date

2026-07-22

## Context

The one-device pilot must fit low-cost infrastructure and does not need orchestration to prove the product flow.

## Decision

Use Docker Compose locally and on AWS first. Consider single-node k3s after Compose is stable. Use campus k3s or an existing Kubernetes cluster with NGINX Gateway Fabric, Helm, and Argo CD only when infrastructure and learning goals justify it.

## Alternatives

Kubernetes first; AWS EKS; Docker Compose forever.

## Consequences

Containers and configuration must stay portable across Compose and Kubernetes.

## Benefits

Lower pilot cost and complexity while preserving an orchestration path.

## Risks

Compose-to-Kubernetes operational differences appear later.

## Follow-up

Complete the Phase 5 portable stack before Phase 17 orchestration work.
