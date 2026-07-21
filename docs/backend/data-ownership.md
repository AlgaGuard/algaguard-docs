# Data ownership

Each microservice owns its data. No service may directly query or mutate another service's tables. A single pilot PostgreSQL instance may host separate databases/schemas for operational economy, but that does not grant cross-service access.

| Owner | Authoritative data |
|---|---|
| Keycloak | Human identities, credentials, sessions, OIDC configuration |
| Device Service | Devices, claim state, tank association, active profile reference, hardware/firmware state |
| Access Service | Organizations, invitations, memberships, roles, ownership |
| Profile Service | Profile definitions and immutable versions |
| Telemetry Service | Samples, batches/idempotency, aggregates, latest readings |
| Command Service | Command/config intent and status |
| Alert Service | Alert lifecycle and evaluated profile references |
| Notification Service | Provider-independent messages and delivery attempts |
| OTA Service | Releases, manifests, rollout status, object references |
| Audit Service | Security/business audit events |

Redis is not the sole record for durable business state. S3-compatible storage holds firmware and large objects; the OTA Service owns their metadata. Backups must cover PostgreSQL/TimescaleDB and Keycloak, with object checksums and restore tests.
