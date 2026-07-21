# ADR-011: Cloud-agnostic core

## Status

Accepted

## Date

2026-07-22

## Context

AWS is temporary for about six months; the final target is a campus Linux server.

## Decision

Run portable containers and protocols. Use stable DNS, configurable MQTT/OTA endpoints, an S3-compatible object interface with S3/MinIO adapters, and an email interface with SES/SMTP adapters. AWS-specific managed services are not mandatory core dependencies.

## Alternatives

Build on AWS IoT Core/Cognito/Lambda/EKS/MSK; create separate campus implementation.

## Consequences

The team operates more portable components and tests provider adapters.

## Benefits

AWS-to-campus migration changes infrastructure and DNS rather than domain code or firmware protocol.

## Risks

Portability claims can fail without restore/cutover tests and multi-environment images.

## Follow-up

Validate the migration plan before AWS deployment and rehearse before cutover.
