# Security architecture

## Identity boundaries

- Keycloak handles registration, login, email verification, password reset, OIDC, and OAuth 2.0 for people.
- ESP32 devices authenticate separately with per-device credentials or certificates.
- The Access Service authorizes organization and device actions. The backend enforces permissions; hidden client controls are not security controls.
- MQTT ACLs restrict each device to its allowed topics. TLS protects MQTT and HTTPS traffic.

## Planned controls

- Short-lived access tokens, validated issuer/audience, and documented refresh-token handling.
- One-time, expiring claim and invitation tokens stored in non-reversible form where practical.
- Rate limits, maximum request sizes, pagination, invitation throttling, and abuse monitoring.
- Secrets supplied by deployment configuration, never committed.
- Signed OTA manifests and firmware, secure download, rollout rings, rollback, and audit events.
- Encryption for backups and object storage, plus documented restore access.
- Correlation IDs, structured logs, audit records, and OpenTelemetry without sensitive token contents.

`SAFETY`: Electrical isolation and water-ingress boundaries are separate physical safety concerns; see [power architecture](../hardware/power-architecture.md) and [water ingress](../hardware/water-ingress-plan.md).
