# Backend microservices overview

All MVP services use Node.js, Express, and TypeScript. Planned HTTP contracts use OpenAPI; MQTT/event contracts use AsyncAPI; runtime inputs use Zod or JSON Schema; PostgreSQL access uses Prisma or another documented layer. Each service exposes health/readiness endpoints, structured logs, correlation context, and OpenTelemetry instrumentation.

| Capability | Planned repository | Responsibility |
|---|---|---|
| Public API | `algaguard-api-gateway` | JWT validation, rate limits, routing, correlation IDs, SSE/WebSocket entry |
| Devices | `algaguard-device-service` | Registry, identity, claim codes, tank association, active profile reference, hardware/firmware state |
| Access | `algaguard-access-service` | Organizations, memberships, invitations, sharing, ownership, revocation |
| Profiles | `algaguard-profile-service` | User-defined algae profiles, threshold versions, units, cloning, sharing, assignment rules |
| Telemetry | `algaguard-telemetry-service` | Batch validation, idempotency, raw/aggregate retention, history, latest values |
| Commands | `algaguard-command-service` | Commands/configuration, LED states, delivery status, timeout/expiry |
| Alerts | `algaguard-alert-service` | Threshold/device-health alert lifecycle |
| Notifications | `algaguard-notification-service` | Email/invitations/alerts through provider adapters; push later without Firebase |
| OTA | `algaguard-ota-service` | Releases, manifests, object references, rings, progress, rollback |
| Audit | `algaguard-audit-service` | Security and business audit trail |
| MQTT ingestion | `algaguard-mqtt-ingestion-service` | Subscribe, validate, route, and publish application acknowledgements |

The pilot may combine logical services into fewer containers to fit measured resources, but boundaries, owned data, and contracts remain explicit. Full repository details are in [service boundaries](service-boundaries.md). Cloud/provider independence is authoritative in [provider adapters](provider-adapters.md), [ADR-014](../decisions/ADR-014-aws-temporary-host.md), [ADR-015](../decisions/ADR-015-campus-final-host.md), and [ADR-016](../decisions/ADR-016-s3-compatible-storage.md).
