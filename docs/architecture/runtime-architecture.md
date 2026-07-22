# Runtime architecture

The portable runtime starts with Docker Compose on a developer computer, then the same container images run on AWS EC2 and later on the campus server. Kubernetes is not required for the first end-to-end system.

The pilot data path is:

`ESP32 -> EMQX -> MQTT Ingestion Service -> Telemetry Service -> TimescaleDB`

Kafka becomes an optional buffer and fan-out layer only after measured demand justifies it. See [event flow](../backend/event-flow.md) and [ADR-004](../decisions/ADR-004-kafka-later-scale-phase.md).

NGINX is the HTTP/HTTPS entry. EMQX exposes a separate MQTT/TLS endpoint. Keycloak authenticates human users. Redis holds cache, latest-value, rate-limit, and short-lived command state. PostgreSQL holds relational service-owned data; TimescaleDB or PostgreSQL partitioning holds telemetry. S3-compatible storage holds firmware and large artifacts.

## Realtime client path

**CONFIRMED Phase 2.1:** The planned Realtime Service is a separate Node.js and TypeScript service using native RFC 6455 WebSockets and Redis Pub/Sub. NGINX routes WSS to it on local, AWS EC2, and campus deployments. The API Gateway validates the Keycloak access token before issuing a short-lived, one-time ticket, and the Access Service authorizes every requested subscription.

Live producers publish a notification to Redis only after authoritative state changes. Redis Pub/Sub and WebSocket delivery are non-durable. After a disconnect, React and Flutter back off with jitter, request a new ticket, reconnect, fetch current state through HTTPS, resubscribe, and continue live delivery. TimescaleDB and service-owned PostgreSQL data remain authoritative.

Commands do not enter through WebSocket. React or Flutter sends an HTTPS command through the API Gateway, the Command Service records it and publishes MQTT to EMQX, and command status returns as a live event. See the [Realtime Service](../backend/realtime-service.md) and [event flow](../backend/event-flow.md).
