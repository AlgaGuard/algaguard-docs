# ADR-010: Node.js Express TypeScript microservices

## Status

Accepted

## Date

2026-07-22

## Context

The team needs a consistent MVP backend with portable HTTP, MQTT, database, and observability libraries.

## Decision

Use Node.js, Express, and TypeScript for the MVP and normal microservices, with OpenAPI, AsyncAPI, runtime schema validation, structured logging, OpenTelemetry, and documented PostgreSQL access. Use KafkaJS if Kafka is later approved.

Explicitly do not introduce NestJS, Java/Spring Boot, or Python into the MVP. Python may be considered only for a clearly justified later analytics/ML service. Java may be considered only for an essential Java-only capability, and either requires a later ADR.

## Alternatives

NestJS; Java/Spring Boot; Python web services; mixed languages from the start.

## Consequences

Teams must define lightweight Express conventions and avoid framework drift.

## Benefits

One language/runtime across MVP services and Kafka consumers, with lower pilot complexity.

## Risks

Inconsistent service structure without templates and review.

## Follow-up

Create service standards and template specifications in Phase 2.
