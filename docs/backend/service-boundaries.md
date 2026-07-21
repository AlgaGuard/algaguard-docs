# Service and repository boundaries

Status: planned interfaces; exact OpenAPI/AsyncAPI schemas are a Phase 2 deliverable.

## Shared service rules

- Technology: Node.js + Express + TypeScript; KafkaJS only in the later Kafka path.
- Deployment: independently versioned container image. Pilot co-location/combination is allowed without shared-table access.
- Common HTTP: planned `/health/live`, `/health/ready`, and documented versioned API routes where public/internal HTTP applies.
- CI: planned GitHub Actions build, lint, test, contract check, dependency/container scan, and GHCR publish. No workflow is created in Phase 1.
- MQTT topics are namespaced per environment and device; illustrative logical names below require Phase 2 contract approval.

## `algaguard-api-gateway`

- Purpose: public client entry, JWT validation, rate limiting, routing, correlation IDs, SSE/WebSocket entry.
- Owned data: no business system of record; short-lived rate/session state in Redis.
- Incoming: HTTPS from web/mobile. Outgoing: internal HTTP to services and OIDC validation to Keycloak.
- MQTT/events: none directly. HTTP: `/api/v1/*`, `/events`; exact routes `TBD`.
- Image: `ghcr.io/algaguard/algaguard-api-gateway`. Phase 7.
- Pilot role: one client entry. Scale role: horizontally scalable policy/routing layer.

## `algaguard-device-service`

- Purpose: registry, claim codes, device identity metadata, tank association, active profile version, hardware/firmware health.
- Owned data: devices, claims, tank associations, device state references.
- Incoming: gateway HTTP; validated status from ingestion; profile assignment reference. Outgoing: command/config requests and audit events.
- MQTT: consumes no broker topic directly in the target boundary. HTTP: planned `/devices`, `/devices/{id}`, `/devices/{id}/claim`, `/devices/{id}/profile`.
- Events: `DeviceRegistered`, `DeviceClaimed`, `DeviceHealthUpdated`, `DeviceProfileAssigned`.
- Image: `ghcr.io/algaguard/algaguard-device-service`. Phases 8 and 10.
- Pilot role: register/claim demo device. Scale role: indexed registry and lifecycle state for 1,000 devices.

## `algaguard-access-service`

- Purpose: organizations, membership, device sharing, invitations, roles, ownership transfer, revocation.
- Owned data: organizations, organization/device memberships, invitations, role grants, ownership records.
- Incoming: gateway HTTP and Keycloak subject IDs. Outgoing: authorization decisions, invitation requests, audit events.
- MQTT: none. HTTP: planned `/organizations`, `/organizations/{id}/members`, `/devices/{id}/members`, `/invitations`, `/authorizations/check`.
- Events: `InvitationCreated`, `MembershipChanged`, `AccessRevoked`, `OwnershipTransferred`.
- Image: `ghcr.io/algaguard/algaguard-access-service`. Phase 9.
- Pilot role: secure sharing demonstration. Scale role: paginated, indexed authorization for 10,000 users and 500+ organizations.

## `algaguard-profile-service`

- Purpose: algae profiles, units, thresholds, versions, cloning, organization sharing, validation, assignment rules.
- Owned data: profiles, immutable versions, parameter/unit definitions, sharing metadata.
- Incoming: gateway HTTP; assignment references. Outgoing: compact versioned configuration request and audit event.
- MQTT: none directly. HTTP: planned `/profiles`, `/profiles/{id}/versions`, `/profiles/{id}/clone`, `/profiles/{id}/assignments`.
- Events: `ProfileCreated`, `ProfileVersionPublished`, `ProfileAssigned`, `ProfileDeactivated`.
- Image: `ghcr.io/algaguard/algaguard-profile-service`. Phase 10.
- Pilot role: one user-defined profile. Scale role: versioned multi-organization profile catalog.

## `algaguard-telemetry-service`

- Purpose: validate batches, enforce idempotency, store telemetry, maintain latest values/aggregates, serve history.
- Owned data: telemetry records, aggregates, ingestion/idempotency metadata.
- Incoming: direct internal request/event from MQTT ingestion; later Kafka events; gateway queries. Outgoing: acceptance result, telemetry events, alert inputs.
- MQTT: none directly in the target boundary. HTTP: planned internal `/ingestion/batches`; public `/devices/{id}/telemetry` and `/devices/{id}/latest`.
- Events: `TelemetryBatchAccepted`, `TelemetryBatchRejected`, `TelemetryAggregateReady`.
- Image: `ghcr.io/algaguard/algaguard-telemetry-service`. Phase 6.
- Pilot role: direct ingestion into TimescaleDB/PostgreSQL. Scale role: partitioned retention, aggregates, tuned writers/read replicas as proven necessary.

## `algaguard-command-service`

- Purpose: device commands, LED state and compact configuration delivery, tracking, timeout, expiry, idempotency.
- Owned data: command intent/status or service-owned command store; short-lived delivery state in Redis.
- Incoming: gateway/service HTTP. Outgoing: MQTT publish and audit/result events.
- MQTT: publishes `devices/{deviceId}/commands` and `devices/{deviceId}/config`; receives validated results through ingestion.
- HTTP: planned `/devices/{id}/commands`, `/commands/{id}`. Events: `CommandRequested`, `CommandAcknowledged`, `CommandExpired`, `ConfigurationApplied`.
- Image: `ghcr.io/algaguard/algaguard-command-service`. Phase 13.
- Pilot role: LED/config control. Scale role: partitioned queues, backpressure, expiry, and fan-out controls.

## `algaguard-alert-service`

- Purpose: lifecycle for local/cloud threshold, offline, low-battery, RTC-invalid, and SD-failure alerts.
- Owned data: alert definitions/state and evaluation references; historical alerts retain profile version.
- Incoming: telemetry/health/profile-version events. Outgoing: notification requests and audit events.
- MQTT: none directly. HTTP: planned `/alerts`, `/alerts/{id}/acknowledge`, `/alert-rules`.
- Events: `AlertOpened`, `AlertUpdated`, `AlertAcknowledged`, `AlertResolved`.
- Image: `ghcr.io/algaguard/algaguard-alert-service`. Phase 14.
- Pilot role: demonstrate lifecycle. Scale role: partitioned evaluation and deduplication.

## `algaguard-notification-service`

- Purpose: invitation, email, and alert notifications through replaceable providers; push may be added later without Firebase dependency.
- Owned data: templates, delivery attempts/status, provider-independent message records.
- Incoming: notification requests/events. Outgoing: SES adapter on AWS or SMTP adapter on campus.
- MQTT: none. HTTP: planned internal `/notifications`; administrative template endpoints `TBD`.
- Events: `NotificationQueued`, `NotificationDelivered`, `NotificationFailed`.
- Image: `ghcr.io/algaguard/algaguard-notification-service`. Phase 14.
- Pilot role: email if an approved provider exists. Scale role: retries, provider quotas, suppression, and queue scaling.

## `algaguard-ota-service`

- Purpose: releases, manifests, S3-compatible references, rollout rings, progress, failure thresholds, rollback status.
- Owned data: release/manifest metadata, assignments, rollout and device OTA status.
- Incoming: authorized release HTTP and device result via ingestion. Outgoing: MQTT notification and signed object URL/reference.
- MQTT: publishes `devices/{deviceId}/ota`; receives validated result path from `devices/{deviceId}/command-results` through ingestion.
- HTTP: planned `/firmware/releases`, `/firmware/manifests/{id}`, `/rollouts`. Events: `FirmwareReleased`, `OtaScheduled`, `OtaSucceeded`, `OtaRolledBack`.
- Image: `ghcr.io/algaguard/algaguard-ota-service`. Phase 15.
- Pilot role: signed single-device demonstration after security review. Scale role: rings and staged rollout controls.

## `algaguard-audit-service`

- Purpose: durable security and business audit trail.
- Owned data: append-oriented audit entries and retention metadata.
- Incoming: service audit events. Outgoing: authorized audit queries/export.
- MQTT: none. HTTP: planned `/audit-events` with strict authorization.
- Events: consumes named domain audit events and emits `AuditRecorded` only if needed.
- Image: `ghcr.io/algaguard/algaguard-audit-service`. Foundation across Phases 8-15.
- Pilot role: record high-value actions. Scale role: partitioning, archival, tamper-evidence, and search capacity.

## `algaguard-mqtt-ingestion-service`

- Purpose: subscribe to EMQX, authenticate broker delivery context, validate envelopes, route telemetry/status/results, publish application acknowledgements.
- Owned data: no business record; may keep short-lived deduplication/retry state.
- Incoming MQTT: `devices/{deviceId}/telemetry/batches`, `devices/{deviceId}/status`, `devices/{deviceId}/command-results`.
- Outgoing MQTT: `devices/{deviceId}/telemetry/acks` and authorized responses; internal HTTP/event calls to Telemetry and Device services.
- HTTP: health/readiness and internal diagnostics only. Events: normalized `TelemetryBatchReceived`, `DeviceStatusReceived`, `CommandResultReceived`.
- Image: `ghcr.io/algaguard/algaguard-mqtt-ingestion-service`. Phase 6.
- Pilot role: direct EMQX ingestion. Scale role: independently scalable consumers or replacement input from bridge/Kafka after ADR/load evidence.

## `algaguard-mqtt-kafka-bridge` - later only

- Purpose: translate validated MQTT envelopes into Kafka records after direct flow is proven insufficient.
- Technology: Node.js + TypeScript + KafkaJS. Owned data: offsets/retry metadata only.
- Incoming: selected EMQX topics. Outgoing: versioned Kafka topics and dead-letter handling.
- HTTP: health/readiness only. Events/topics: Phase 18 contract `TBD`.
- Image: `ghcr.io/algaguard/algaguard-mqtt-kafka-bridge`. Phase 18.
- Pilot role: none. Scale role: durable buffer/fan-out if load tests justify it.
