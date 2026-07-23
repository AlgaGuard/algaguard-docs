# Security architecture

## Identity boundaries

- Keycloak handles registration, login, email verification, password reset, OIDC, and OAuth 2.0 for people.
- ESP32 devices authenticate separately with per-device credentials or certificates.
- The Access Service authorizes organization and device actions. The backend enforces permissions; hidden client controls are not security controls.
- MQTT ACLs restrict each device to its allowed topics. TLS protects MQTT and HTTPS traffic.
- The device controls only its canonical `deviceId` identity and credentials. It never supplies trusted `organizationId`.
- Device Service owns the canonical-to-UUID/organization mapping. Internal context and authorization calls require audience-validated service tokens.
- Device WebSocket authorization uses `organizationId + deviceUuid`; canonical `deviceId` alone is insufficient.

## Implemented identity controls

- Topic and payload `deviceId` must match, and device-supplied organization fields are rejected.
- Unknown, unclaimed, inactive, and revoked devices cannot produce active trusted context.
- Telemetry rejects stale `ownershipVersion` and publishes no accepted live event before its transaction commits.
- Realtime rejects malformed or incomplete committed events and reauthorizes delivery so revocation or transfer cannot retain access.
- Historical queries filter by trusted current organization and `organizationIdAtIngest`; transfer does not grant pre-transfer history by default.
- Critical context and authorization paths currently use no local cache. Any future cache requires bounded TTL, ownership/lifecycle invalidation, and downstream stale-version detection.

See [ADR-018](../decisions/ADR-018-dual-device-identity.md) for the accepted identity model and ownership-transfer boundary.

## Planned controls

- Short-lived access tokens, validated issuer/audience, and documented refresh-token handling.
- One-time, expiring claim and invitation tokens stored in non-reversible form where practical.
- Rate limits, maximum request sizes, pagination, invitation throttling, and abuse monitoring.
- Secrets supplied by deployment configuration, never committed.
- Signed OTA manifests and firmware, secure download, rollout rings, rollback, and audit events.
- Encryption for backups and object storage, plus documented restore access.
- Correlation IDs, structured logs, audit records, and OpenTelemetry without sensitive token contents.

`SAFETY`: Electrical isolation and water-ingress boundaries are separate physical safety concerns; see [power architecture](../hardware/power-architecture.md) and [water ingress](../hardware/water-ingress-plan.md).
