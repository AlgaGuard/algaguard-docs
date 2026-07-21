# ADR-014: AWS EC2 as the Temporary AlgaGuard Host

## Status

Accepted

## Date

2026-07-22

## Context

AlgaGuard needs temporary public hosting for the one-device pilot for approximately six months. The final target is a campus server, so the temporary environment must not become a mandatory application dependency. AWS account eligibility, region, capacity, and price remain `TBD` until deployment checks are approved.

## Decision

- Use AWS as the temporary hosting environment for approximately six months.
- Prefer one Linux EC2 instance running Docker Compose.
- Evaluate single-node k3s only after Docker Compose is stable and only for a documented orchestration objective.
- EKS is not required for the pilot. Lambda is not the core backend runtime. Cognito is not the identity platform. AWS IoT Core is not the mandatory MQTT platform.
- DynamoDB, SQS, EventBridge, Amazon API Gateway, and MSK are not core dependencies.
- Amazon S3 and SES may be used only through the [provider interfaces](../backend/provider-adapters.md).
- Stable product DNS must hide the temporary EC2 hostname. MQTT and OTA endpoints remain configurable.
- Recheck AWS pricing, credits, plan limitations, regional availability, and account eligibility immediately before deployment.
- Configure budgets, forecast/actual billing alerts, ownership tags, and an explicit resource-expiry/decommission plan.

## Alternatives considered

- AWS managed-service-first architecture.
- EKS or Kafka/MSK for the pilot.
- Temporary hosting on an unconfirmed campus server.
- Local-only operation with no public pilot.

## Consequences

The team operates EMQX, Keycloak, PostgreSQL/TimescaleDB, Redis, NGINX, application services, backups, and monitoring on a constrained host. Logical services may be combined into fewer pilot containers without merging contracts or data ownership. All AWS resources require cost and removal tracking.

## Benefits

- Provides temporary public hosting without changing portable protocols.
- Uses the same GHCR images planned for campus.
- Keeps the pilot small and avoids premature Kubernetes/Kafka operations.

## Risks

- Credits or Free plan eligibility may be unavailable or expire.
- A small instance may not fit every component comfortably.
- Public IPv4, EBS, DNS, transfer, snapshot, S3, and SES usage can create cost.
- A single host is a failure domain and does not prove production capacity.

## Migration implications

AWS data, objects, Keycloak state, EMQX configuration/ACLs/certificates, and environment configuration must be exportable and restorable. Stable DNS cuts clients and devices over to campus without changing business logic or the ESP32 communication protocol.

## Follow-up actions

1. Confirm the AWS account, plan, credits, region, approved budget, and instance size.
2. Measure the local portable stack before selecting EC2 capacity.
3. Complete backup/restore tests and cost guardrails before public use.
4. Rehearse [campus migration](../project-management/campus-migration-plan.md) before cutover.
