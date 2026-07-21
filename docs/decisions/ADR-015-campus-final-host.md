# ADR-015: Campus Server as the Final AlgaGuard Host

## Status

Accepted

## Date

2026-07-22

## Context

`CONFIRMED`: The campus server is AlgaGuard's final hosting target after the temporary AWS period. Campus Linux, CPU, RAM, disk, RAID, network, firewall, DNS, TLS, SMTP, backup, UPS, RTO, and RPO facts remain `TBD` until campus administrators confirm them.

## Decision

- Run the same approved container image digests used on AWS.
- Support Docker Compose, single-node k3s, or an existing campus Kubernetes cluster. Select one after the campus environment is known.
- Keep EMQX for MQTT, Keycloak for human identity, PostgreSQL and TimescaleDB for durable data, Redis for cache/state, and MinIO for the campus object-storage implementation.
- Migrate through checked backups, isolated restore validation, parallel functional checks, stable DNS cutover, and a defined rollback period.
- Do not rewrite ESP32 MQTT/HTTPS protocols or application business logic because the hosting location changes.

## Alternatives considered

- Remain permanently on AWS.
- Rebuild around campus-specific proprietary services.
- Require Kubernetes even when one-server Docker Compose meets the confirmed needs.
- Reflash devices with a campus server address instead of stable DNS.

## Consequences

Campus administrators and the project must share ownership of host operations, network exposure, certificates, backups, monitoring, patching, and incident recovery. The runtime choice cannot be finalized until infrastructure facts and operational responsibilities are confirmed.

## Benefits

- Meets the confirmed final-host requirement.
- Reuses portable images, protocols, databases, and provider boundaries.
- Avoids ongoing dependency on temporary AWS infrastructure.

## Risks

- Unknown campus capacity or policy may delay or constrain migration.
- A single campus server remains a failure domain.
- Incorrect restore, ACL, certificate, firewall, or DNS handling could interrupt service or expose data.
- RTO and RPO are `TBD` and cannot be promised before administrator and recovery review.

## Migration implications

### Infrastructure and environment

Confirm Linux version, CPU, RAM, disk/RAID/filesystem, static addressing, bandwidth, UPS, administrators, maintenance windows, backup target, and monitoring ownership. Apply environment-specific configuration through variables and mounted files; do not carry AWS SDK objects or credentials into domain configuration.

### Data and platform state

- Back up and restore PostgreSQL and TimescaleDB with roles, extensions, schemas, row/time-range checks, and tested checksums.
- Migrate Keycloak through supported database/configuration backup and validate realms, clients, users, roles, flows, and token issuance. Services never read Keycloak tables directly.
- Migrate EMQX ACLs, users/device authentication data, configuration, and certificates with protected private-key handling.
- Transfer S3/MinIO objects using manifests, counts, sizes, checksums, metadata, and version mappings where required.

### Network boundary

| Port | Planned purpose | Exposure rule |
|---:|---|---|
| TCP 443 | HTTPS API, clients, OIDC, OTA | Public or approved campus ingress |
| TCP 8883 | MQTT over TLS | Device ingress through EMQX |
| TCP 80 | Optional HTTP redirect or certificate challenge | Use only if approved |
| TCP 22 | Linux administration | Restricted administrator sources only; `TBD` |
| Database, Redis, MinIO, metrics, tracing, cluster ports | Internal service traffic | Not public; exact campus rules `TBD` |

Prepare campus DNS records and public/campus TLS certificates before cutover. Lower DNS TTL for the approved window, validate the campus environment in parallel, then cut stable product names from AWS to campus. Keep AWS available for the agreed rollback period and define how campus-only writes are reconciled if rollback occurs.

### Rehearsal and recovery

Run at least one migration rehearsal using representative backups and objects. Test login, authorization, MQTT/TLS, telemetry acknowledgement/replay, profiles, alerts, storage, and OTA downloads. Define measurable cutover acceptance, rollback triggers, RTO, and RPO before the production move.

## Follow-up actions

1. Obtain written campus infrastructure and operational ownership facts.
2. Select Compose, k3s, or existing Kubernetes from those facts.
3. Approve RTO/RPO, firewall, DNS, TLS, backup, and rollback procedures.
4. Execute the [migration plan](../project-management/campus-migration-plan.md) rehearsal before final cutover.
