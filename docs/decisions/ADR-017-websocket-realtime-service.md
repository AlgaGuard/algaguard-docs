# ADR-017: Portable WebSocket Realtime Service

## Status

Accepted

## Date

2026-07-22

## Context

React and Flutter need timely telemetry, device, alert, command, profile, OTA, and notification updates. Polling alone adds latency and unnecessary load. AlgaGuard must preserve its device MQTT design, authoritative service databases, and migration from temporary AWS EC2 hosting to the campus server.

## Decision

Use WebSocket over TLS for browser and mobile live updates through an independently deployable `algaguard-realtime-service`. Implement the planned service with Node.js, TypeScript, native RFC 6455 WebSockets, and a lightweight server library such as `ws`. Instrument it with OpenTelemetry.

MQTT/TLS remains the ESP32 transport. HTTPS REST remains the interface for initial and historical queries, state recovery, and all user-created commands. The Command Service records authorized commands and publishes them to devices through MQTT; WebSocket sends command progress and results to clients.

Keycloak authenticates users. The API Gateway issues short-lived, one-time WebSocket tickets over authenticated HTTPS, and the Realtime Service consumes them during WSS connection setup. The Access Service authorizes every organization, device, and current-user subscription. Access revocation removes active subscriptions.

Use Redis Pub/Sub for non-durable live fan-out only. Service databases and TimescaleDB remain authoritative. A disconnected client may miss events and must obtain a new ticket, reconnect, recover current state over HTTPS, and resubscribe.

AWS-specific WebSocket services are not core dependencies. The same Realtime Service container runs on AWS EC2 and the campus server.

## Alternatives considered

- HTTP polling as the only live-update mechanism.
- Server-sent events for all bidirectional subscription control.
- AWS API Gateway WebSocket APIs or AWS AppSync as a core dependency.
- Firebase Realtime Database or Firebase Cloud Messaging as the core realtime channel.
- Socket.IO-specific wire contracts.
- Direct WebSocket connections from the ESP32.
- Treating Redis Pub/Sub or WebSocket delivery as authoritative telemetry storage.

## Consequences

Clients need connection state, heartbeat, retry with jitter, HTTPS recovery, resubscription, and duplicate/stale-event handling. The Realtime Service needs bounded connection queues, schema validation, per-subscription authorization, revocation, rate limits, slow-client handling, and operational metrics. Exact limits remain **TBD** until implementation and load testing.

Redis Pub/Sub does not replay missed events. Producers publish live notifications only after authoritative state changes, and clients never treat an uninterrupted-looking sequence as proof that no event was missed.

## Benefits

- Responsive React and Flutter updates without altering the ESP32 protocol.
- Portable implementation across local, AWS EC2, and campus deployments.
- Clear separation between live delivery and authoritative recovery.
- Explicit authorization at the resource-subscription boundary.

## Risks

- Reconnect storms or slow clients can exhaust shared resources without firm limits.
- Stale authorization could expose events if revocation propagation is incorrect.
- Teams may mistakenly treat Redis Pub/Sub as durable or bypass HTTPS command authorization.
- Ticket leakage through URLs or logs would weaken the short-lived authentication design.

## Follow-up actions

1. Implement `algaguard-realtime-service` only in a later authorized application phase.
2. Define and load-test heartbeat, idle, frame-size, subscription, ticket, rate, queue, and backpressure limits.
3. Add Access Service authorization and revocation integration tests.
4. Add client reconnect, HTTPS recovery, resubscription, and stale-state tests.
5. Run the same service artifact on AWS EC2 and the campus target without provider-specific wire changes.
