# Transport and credential security limits

**CONFIRMED:** AlgaGuard uses finite, validated development defaults. Every value below is configuration, not an algae-science threshold or a production capacity claim. Production values remain **TBD** until load, recovery, device-memory, and security testing supports them.

## Device credential and HTTP limits

| Configuration | Development default | Accepted implementation range / behavior |
| --- | ---: | --- |
| `HTTP_BODY_LIMIT` | 256 KiB | Positive `kb`/`mb` Express limit; applies to Device and Ingestion HTTP bodies |
| `DEVICE_CERTIFICATE_VALIDITY_DAYS` | 90 days | 1 to 397 days |
| `DEVICE_BOOTSTRAP_TTL_SECONDS` | 600 s | 60 to 3,600 s |
| `DEVICE_BOOTSTRAP_MAX_ATTEMPTS` | 5 | 1 to 20; exhausted authorization is invalidated |
| Claim failure limiter | 5 attempts / 15 min | Persistent subject/device failure window |
| `DEVICE_ROTATION_OVERLAP_SECONDS` | 300 s | 30 to 86,400 s |
| `DEVICE_BROKER_AUTH_CACHE_SECONDS` | 5 s in Compose | 1 to 300 s |
| `DEVICE_REVOCATION_EFFECTIVE_SECONDS` | 5 s in Compose | 0 to 300 s |

Bootstrap/CSR issuance and rotation are also bounded by one-use state, exact scope, transaction locks, and idempotency keys. They are not open-ended retry APIs.

## MQTT limits

| Configuration | Development default | Accepted implementation range / behavior |
| --- | ---: | --- |
| `MQTT_MAX_PACKET_BYTES` | 262,144 bytes | 1,024 to 1,048,576 bytes |
| `MQTT_MAX_SAMPLES_PER_BATCH` | 120 | 1 to released-contract maximum 120 |
| `MQTT_QOS1_INFLIGHT` | 32 | 1 to 1,024 |
| `MQTT_MAX_MESSAGES_PER_SECOND` | 200 | 1 to 10,000 per ingestion process window |
| `MQTT_RECONNECT_DELAY_MS` | 2,000 ms | 100 to 60,000 ms |
| `MQTT_SESSION_EXPIRY_SECONDS` | 3,600 s | 0 to 604,800 s |
| `MQTT_KEEPALIVE_SECONDS` | 60 s | 15 to 3,600 s |
| EMQX device connection rate | 200/s | Broker development listener bound |
| EMQX internal connection rate | 100/s | Broker development listener bound |
| EMQX QoS retry interval | 30 s | Broker configuration; duplicate processing remains idempotent |

QoS 1 duplicates are keyed and rejected idempotently after the original durable result. Firmware compile-time limits bound queued/replayed batches and the number of duplicate retransmission attempts; see below. An MQTT broker acknowledgement never authorizes deletion of locally queued telemetry—only the AlgaGuard application acknowledgement does.

## WebSocket limits

| Configuration | Development default | Accepted implementation range / behavior |
| --- | ---: | --- |
| `REALTIME_HTTP_BODY_LIMIT_BYTES` | 32,768 bytes | 1,024 to 1,048,576 |
| `WS_MAX_MESSAGE_BYTES` | 262,144 bytes | 1,024 to 1,048,576 |
| `WS_MAX_SUBSCRIPTIONS` | 50 | 1 to 50 per connection |
| `WS_OUTBOUND_QUEUE_MAX` | 100 messages | 1 to 1,000 |
| `WS_CONNECTIONS_PER_MINUTE` | 20 | 1 to 1,000 per limiter key |
| `WS_MESSAGES_PER_MINUTE` | 120 | 1 to 10,000 per connection |
| `WS_HEARTBEAT_MS` | 30,000 ms | 1,000 to 60,000 |
| `WS_IDLE_TIMEOUT_MS` | 90,000 ms | 5,000 to 300,000 and at least twice heartbeat |
| `WS_BACKPRESSURE_BYTES` | 524,288 bytes | 1,024 to 4,194,304 buffered bytes |
| `WS_PREAUTH_BUFFER_MESSAGES` | 50 messages | 1 to 100 |

The server closes or rejects overload rather than growing unbounded memory. Realtime remains non-durable; clients recover authoritative state through HTTPS.

## OTA and temporary URL limits

| Configuration | Development default | Accepted implementation range / behavior |
| --- | ---: | --- |
| `OTA_HTTP_BODY_LIMIT_BYTES` | 262,144 bytes | 4,096 to 1,048,576 |
| `OTA_ASSIGNMENT_TTL_SECONDS` | 900 s | 60 to 86,400 s |
| `OTA_DOWNLOAD_URL_TTL_SECONDS` | 300 s | 30 to 3,600 s and no longer than assignment TTL |

Firmware additionally rejects manifests/artifacts above its compile-time limits before use. URL and assignment lifetimes are authorization bounds, not firmware validity or rollout policy.

## Firmware compile-time bounds

| Build define | Development value | Purpose |
| --- | ---: | --- |
| `ALGAGUARD_MQTT_MAX_BATCH_SAMPLES` | 120 | Samples in one MQTT batch |
| `ALGAGUARD_QUEUE_MAX_SAMPLES` | 1,440 | Bounded in-memory/local queue interface |
| `ALGAGUARD_REPLAY_MAX_BATCHES` | 4 | Replay batches in one scheduling window |
| `ALGAGUARD_REPLAY_MAX_SAMPLES` | 480 | Replay samples in one scheduling window |
| `ALGAGUARD_MQTT_DUPLICATE_MAX_RETRIES` | 3 | Duplicate QoS 1/application-ACK retransmission attempts |
| `ALGAGUARD_OTA_MAX_ARTIFACT_BYTES` | 4,194,304 | OTA artifact accepted by the foundation |
| `ALGAGUARD_OTA_MAX_MANIFEST_BYTES` | 65,536 | OTA manifest buffer |
| `ALGAGUARD_CREDENTIAL_MAX_CERT_BYTES` | 4,096 | One public certificate |
| `ALGAGUARD_CREDENTIAL_MAX_CHAIN_BYTES` | 12,288 | Public certificate chain, at most three certificates |
| `ALGAGUARD_CREDENTIAL_MAX_CSR_BYTES` | 4,096 | CSR submission buffer |
| `ALGAGUARD_CREDENTIAL_MAX_UNSYNCED_HOLDOVER_SECONDS` | 86,400 s | Maximum trusted-time holdover before waiting for synchronization |

These values are configurable at build time and checked for finite safe relationships. The current implementation is a host-testable credential/storage/rotation foundation; actual encrypted NVS/flash integration and real-board memory behavior remain unverified.

## Production placeholders

Before deployment, record approved values for:

1. Expected serialized telemetry sizes and replay bursts.
2. Broker/server memory, connection, session, and rate budgets.
3. Device memory, flash wear, offline duration, and OTA partition capacity.
4. WebSocket fan-out, slow-client, heartbeat, and recovery behavior.
5. Bootstrap/rotation abuse thresholds and incident revocation target.
6. Temporary URL exposure and OTA rollout duration.

**TBD:** Values must be supplied through deployment configuration or firmware build profiles and verified in load/failure tests. “Unlimited” is not an accepted production placeholder.

