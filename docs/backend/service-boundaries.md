# Service and repository boundaries

Status: core platform services and identity path implemented on `develop`; later alert, notification, audit, Kafka, and production-capacity work remains planned.

## Shared service rules

- Technology: Node.js + Express + TypeScript; KafkaJS only in the later Kafka path.
- Deployment: independently versioned container image. Pilot co-location/combination is allowed without shared-table access.
- Common HTTP: planned `/health/live`, `/health/ready`, and documented versioned API routes where public/internal HTTP applies.
- CI: planned GitHub Actions build, lint, test, contract check, dependency/container scan, and GHCR publish. No workflow is created in Phase 1.
- MQTT topics are namespaced per environment and device according to the Phase 2 contract.
- WebSocket is non-durable live delivery only. HTTPS is authoritative for initial state, history, recovery, and user-created commands.

## `algaguard-api-gateway`

- Purpose: public client entry, JWT validation, rate limiting, routing, correlation IDs, and short-lived one-time WebSocket ticket issuance.
- Owned data: no business system of record; short-lived rate/session state in Redis.
- Incoming: HTTPS from web/mobile. Outgoing: internal HTTP to services and OIDC validation to Keycloak.
- MQTT/events: none directly. HTTP: `/api/v1/*`, `/events`; exact routes `TBD`.
- Image: `ghcr.io/algaguard/algaguard-api-gateway`. Phase 7.
- Pilot role: one client entry. Scale role: horizontally scalable policy/routing layer.

## `algaguard-realtime-service`

- Purpose: authenticated native RFC 6455 WSS connections, Access Service-authorized organization/device/current-user subscriptions, schema validation, and non-durable live fan-out by trusted organization and UUID.
- Technology: Node.js, TypeScript, a lightweight WebSocket library such as `ws`, Redis Pub/Sub, and OpenTelemetry.
- Owned data: no authoritative business data; connection, subscription, ticket-consumption, and bounded delivery state only.
- Incoming: WSS upgrades using one-time tickets and subscribe/unsubscribe/ping messages; trusted Redis `telemetry.committed`; authorization and revocation results from Access Service.
- Outgoing: validated WebSocket events to React and Flutter; Access Service authorization checks; connection/subscription telemetry.
- HTTP: health/readiness only plus internal integration as approved. MQTT: none. WebSocket contract: `algaguard-websocket-v1` in `algaguard-contracts`.
- Device identity: subscription `resourceId` is `deviceUuid`; `deviceId` is display/reference only. Delivery requires matching trusted `organizationId` and event-time Access authorization.
- Image: `ghcr.io/algaguard/algaguard-realtime-service`.
- Pilot role: implemented live dashboard/mobile updates. Scale role: independently scalable, bounded connections and queues with slow-client backpressure.
- Authoritative data: none. TimescaleDB and service-owned PostgreSQL databases remain authoritative; clients recover through HTTPS after reconnect.

## `algaguard-device-service`

- Purpose: authoritative canonical-ID/UUID/organization/lifecycle mapping, claim codes, tank association, active profile version, hardware/firmware health.
- Owned data: UUID-keyed devices, immutable unique canonical IDs, current organization and lifecycle, monotonic ownership version/history, claims, tank associations, device state references.
- Incoming: gateway HTTP; validated status from ingestion; profile assignment reference. Outgoing: command/config requests and audit events.
- MQTT: consumes no broker topic directly. HTTP includes UUID public resources and authenticated internal canonical/UUID context resolution; the internal endpoint is not routed through public NGINX.
- Events: `DeviceRegistered`, `DeviceClaimed`, `DeviceHealthUpdated`, `DeviceProfileAssigned`.
- Image: `ghcr.io/algaguard/algaguard-device-service`. Phases 8 and 10.
- Pilot role: register/claim demo device. Scale role: indexed registry and lifecycle state for 1,000 devices.

## `algaguard-access-service`

- Purpose: organizations, membership, device sharing, invitations, roles, ownership transfer, revocation, and UUID resource authorization.
- Owned data: organizations, organization/device memberships, invitations, role grants, ownership records.
- Incoming: gateway HTTP, Keycloak subject IDs, and authenticated internal authorization requests. Outgoing: decisions based on live Device Service context, invitation requests, audit events.
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

- Purpose: validate trusted batches, reject stale ownership context, enforce idempotency, store telemetry, publish post-commit events, maintain latest values/aggregates, and serve organization-filtered UUID history.
- Owned data: telemetry records with UUID/canonical ID/organization/version at ingest, aggregates, ingestion/idempotency metadata.
- Incoming: authenticated internal request from MQTT ingestion; later Kafka events; gateway queries. Outgoing: durable application-ack result and trusted `telemetry.committed` after transaction commit.
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

- Purpose: subscribe to EMQX, validate canonical topic/payload identity, resolve trusted Device context, route enriched telemetry/status/results, and publish application acknowledgements.
- Owned data: no business record; may keep short-lived deduplication/retry state.
- Incoming MQTT: versioned canonical `algaguard/v1/devices/{deviceId}/...` topics according to the contracts.
- Outgoing MQTT: canonical device application acknowledgements and authorized responses; authenticated internal calls carrying Device-resolved UUID, organization, and ownership version.
- HTTP: health/readiness and internal diagnostics only. Events: normalized `TelemetryBatchReceived`, `DeviceStatusReceived`, `CommandResultReceived`.
- Image: `ghcr.io/algaguard/algaguard-mqtt-ingestion-service`. Phase 6.
- Pilot role: direct EMQX ingestion. Scale role: independently scalable consumers or replacement input from bridge/Kafka after ADR/load evidence.

The identity boundary across these services is authoritative in [ADR-018](../decisions/ADR-018-dual-device-identity.md).

## `algaguard-mqtt-kafka-bridge` - later only

- Purpose: translate validated MQTT envelopes into Kafka records after direct flow is proven insufficient.
- Technology: Node.js + TypeScript + KafkaJS. Owned data: offsets/retry metadata only.
- Incoming: selected EMQX topics. Outgoing: versioned Kafka topics and dead-letter handling.
- HTTP: health/readiness only. Events/topics: Phase 18 contract `TBD`.
- Image: `ghcr.io/algaguard/algaguard-mqtt-kafka-bridge`. Phase 18.
- Pilot role: none. Scale role: durable buffer/fan-out if load tests justify it.
