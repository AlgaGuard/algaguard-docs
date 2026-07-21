# Capacity plan

Status: Phase 1 arithmetic, not a production benchmark. Exact encoded/database record sizes are `TBD`.

## Event counts

`CONFIRMED`: one sample per second and one normal batch every ten seconds.

| Devices | Samples/day | Samples/30 days | Batch messages/day | Batch messages/30 days |
|---:|---:|---:|---:|---:|
| 1 | 86,400 | 2,592,000 | 8,640 | 259,200 |
| 100 | 8,640,000 | 259,200,000 | 864,000 | 25,920,000 |
| 1,000 | 86,400,000 | 2,592,000,000 | 8,640,000 | 259,200,000 |

The batch count assumes continuous operation and one batch every ten seconds. Retries, acknowledgements, health, commands, reconnects, alerts, and OTA add traffic.

## Payload-only storage estimates

The following uses three possible encoded/stored record sizes. GiB values use 1,073,741,824 bytes and exclude indexes, row/page overhead, WAL, temporary files, aggregates, backups, replicas, and free-space margin.

### One-second records for 30 days

| Devices | Records | At 128 B | At 256 B | At 512 B |
|---:|---:|---:|---:|---:|
| 1 | 2,592,000 | 0.309 GiB | 0.618 GiB | 1.236 GiB |
| 100 | 259,200,000 | 30.899 GiB | 61.798 GiB | 123.596 GiB |
| 1,000 | 2,592,000,000 | 308.990 GiB | 617.981 GiB | 1,235.962 GiB |

### Ten-second cloud records for 30 days

| Devices | Records | At 128 B | At 256 B | At 512 B |
|---:|---:|---:|---:|---:|
| 1 | 259,200 | 0.031 GiB | 0.062 GiB | 0.124 GiB |
| 100 | 25,920,000 | 3.090 GiB | 6.180 GiB | 12.360 GiB |
| 1,000 | 259,200,000 | 30.899 GiB | 61.798 GiB | 123.596 GiB |

One device retaining one-second records for 60 days needs 5,184,000 records: approximately 0.618, 1.236, or 2.472 GiB at the three sizes before database overhead. Two days of local one-second payload is only about 0.021, 0.041, or 0.082 GiB, but SD design must account for encoding, filesystem behavior, wear, pending data, corruption recovery, and margin.

At 1,000 devices, one-minute aggregates for 60 days produce 86.4 million records and about 10.300, 20.599, or 41.199 GiB payload-only.

## Retention modes

### One-device pilot

- Keep local one-second samples for at least two days through a ring policy.
- Raw cloud telemetry may be kept for up to 60 days only if measured database/storage and free-credit capacity permit.
- Alert before disk exhaustion and keep tested backups; pilot retention can be reduced rather than risking the host.

### Scale target

- Keep local one-second samples for at least two days.
- Store ten-second cloud records for a configurable 7-14 days.
- Store one-minute aggregates for approximately 60 days.
- Run monitored, idempotent downsampling and retention jobs with lag and deletion metrics.

## Capacity gates

1. Measure serialized batch/sample size, database row size, indexes, compression, WAL rate, backup size, MQTT bytes, and query shapes.
2. Test sustained and reconnect-burst ingestion, duplicate delivery, offline replay, concurrent queries, profiles, alerts, and commands.
3. Test EMQX connection/reconnect rate and service/database saturation.
4. Establish CPU, memory, disk IOPS/latency, network, and recovery objectives with at least 30% measured operating headroom as an `ASSUMPTION` to validate.
5. Revisit partitioning, connection pools, aggregate jobs, replicas, broker clustering, service horizontal scale, and Kafka only from evidence.

The free one-device pilot cannot prove the 1,000-device/10,000-user target. Full scale likely requires larger or multiple hosts and load-tested recovery. See [scale target](../architecture/scale-target.md).
