# ADR-001: Polyrepo architecture

## Status

Accepted

## Date

2026-07-22

## Context

AlgaGuard has independently deployable firmware, clients, services, contracts, infrastructure, and desired-state configuration.

## Decision

Use a polyrepo under the `AlgaGuard` organization. Each deployable application/service has a repository. Put shared schemas in `algaguard-contracts`, bootstrap in `algaguard-infrastructure`, and Kubernetes/Argo CD desired state in `algaguard-gitops`.

## Alternatives

Monorepo; one repository per technology layer; unstructured independent repositories.

## Consequences

Cross-repository changes require versioned contracts and coordinated releases.

## Benefits

Clear ownership, security boundaries, release cadence, and independent deployment.

## Risks

Repository overhead and contract drift.

## Follow-up

Define contract versioning and repository templates in Phase 2.
