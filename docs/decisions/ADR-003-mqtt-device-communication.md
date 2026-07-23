# ADR-003: MQTT device communication

## Status

Accepted

## Date

2026-07-22

## Context

Devices need efficient telemetry upload, command/config delivery, acknowledgements, and reconnect behavior.

## Decision

Use MQTT over TLS through EMQX. Use QoS 1 plus application acknowledgements and idempotent sequences. Use HTTPS for firmware download.

## Alternatives

HTTP polling only; WebSockets; direct Kafka connection; AWS IoT Core as a mandatory dependency.

## Consequences

Topic ACLs, device credentials, duplicate handling, maximum message size, and schemas require explicit contracts. Per-device X.509 identity, exact ACLs, rotation, and revocation are now governed by [ADR-019](ADR-019-per-device-x509-credentials.md); current finite bounds are recorded in [transport security limits](../architecture/transport-security-limits.md).

## Benefits

Portable, efficient bidirectional device communication with established broker support.

## Risks

Misconfigured ACLs or treating broker acknowledgement as durable ingestion.

## Follow-up

Create AsyncAPI envelopes and the acknowledgement contract in Phase 2.
