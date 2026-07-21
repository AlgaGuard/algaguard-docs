# ADR-004: Kafka is a later scale phase

## Status

Accepted

## Date

2026-07-22

## Context

The pilot has one device. Kafka would add cost and operational complexity before the direct path is measured.

## Decision

The MVP path is ESP32 -> EMQX -> MQTT Ingestion Service -> Telemetry Service. Add an EMQX-to-Kafka bridge and Kafka only in Phase 18 if load, durability, replay, or fan-out evidence justifies them. Devices never connect directly to Kafka. Use KafkaJS with Node.js unless a later ADR proves another language essential.

## Alternatives

Require Kafka from the start; use a managed Kafka core; never allow Kafka.

## Consequences

The direct path must define backpressure and idempotency and leave a replaceable ingestion boundary.

## Benefits

Lower pilot cost and faster vertical-slice delivery without closing the scale path.

## Risks

A later migration requires contract discipline and measured rework.

## Follow-up

Load-test direct ingestion before the Phase 18 decision.
