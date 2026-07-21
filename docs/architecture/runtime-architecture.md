# Runtime architecture

The portable runtime starts with Docker Compose on a developer computer, then the same container images run on AWS EC2 and later on the campus server. Kubernetes is not required for the first end-to-end system.

The pilot data path is:

`ESP32 -> EMQX -> MQTT Ingestion Service -> Telemetry Service -> TimescaleDB`

Kafka becomes an optional buffer and fan-out layer only after measured demand justifies it. See [event flow](../backend/event-flow.md) and [ADR-004](../decisions/ADR-004-kafka-later-scale-phase.md).

NGINX is the HTTP/HTTPS entry. EMQX exposes a separate MQTT/TLS endpoint. Keycloak authenticates human users. Redis holds cache, latest-value, rate-limit, and short-lived command state. PostgreSQL holds relational service-owned data; TimescaleDB or PostgreSQL partitioning holds telemetry. S3-compatible storage holds firmware and large artifacts.
