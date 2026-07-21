# ADR-013: Telemetry sampling, batching, and retention

## Status

Accepted

## Date

2026-07-22

## Context

One-second samples provide local behavior, while unchecked cloud retention would grow rapidly at 1,000 devices.

## Decision

Generate one sample per second, refresh the OLED every second, store locally for at least two days, and upload roughly ten samples every ten seconds. Use QoS 1 plus application acknowledgement and `deviceId + sequence` idempotency.

Pilot raw cloud data may be retained up to 60 days if measured free capacity permits. At scale, keep ten-second cloud records for a configurable 7-14 days and one-minute aggregates for about 60 days; use retention/downsampling jobs.

## Alternatives

Upload every sample; keep all raw data indefinitely; aggregate only on device.

## Consequences

Retention jobs, aggregate correctness, storage monitoring, and exact record-size measurement are required.

## Benefits

Detailed offline data with controlled cloud growth and useful long-term trends.

## Risks

Payload estimates omit indexes, WAL, backups, and operational overhead; jobs may lag.

## Follow-up

Finalize record schemas in Phase 2 and load-test/storage-measure before production claims.
