# ADR-016: S3-Compatible Object Storage Abstraction

## Status

Accepted

## Date

2026-07-22

## Context

AlgaGuard needs object storage for firmware, backups, configuration artifacts, and exports while moving from temporary AWS hosting to the campus server. Direct AWS dependencies in domain logic would make that migration expensive and unsafe.

## Decision

Core services use the [ObjectStorage interface](../backend/provider-adapters.md). Amazon S3 is permitted during temporary AWS hosting; MinIO is the campus implementation. Domain logic must not depend on AWS SDK types.

Portable operations are upload, download, delete, metadata lookup, checksum verification, temporary download URLs, and provider-neutral version identifiers where supported.

Do not require:

- AWS-specific ARNs in domain models;
- mandatory S3 event notifications;
- AWS-only IAM identity or policy object types;
- provider-specific lifecycle APIs in business logic.

Provider capabilities that have no portable equivalent remain infrastructure concerns behind the adapter. Domain errors and metadata use AlgaGuard-owned types.

## Alternatives considered

- Use Amazon S3 SDK types throughout services.
- Store large binaries and backups directly in PostgreSQL.
- Use separate AWS and campus business logic.
- Require shared filesystem paths in domain contracts.

## Consequences

The adapter needs compatibility tests against both S3 and MinIO. The team must define provider-neutral key naming, checksum algorithms, metadata, version behavior, signed-URL semantics, authorization, retention intent, and error translation. Not every provider feature can be exposed.

## Benefits

- Preserves the AWS-to-campus migration path.
- Keeps OTA and backup domain behavior stable.
- Allows provider replacement without changing external device/client contracts.

## Risks

- Differences in versioning, signed URLs, metadata, multipart upload, consistency, and lifecycle behavior can leak through weak abstractions.
- Incorrect authorization or signed-URL duration can expose firmware, backups, or exports.
- Retention or deletion mistakes can defeat recovery requirements.

## Migration implications

- **Firmware binaries and OTA manifests:** copy objects and metadata, verify manifest hashes/signatures, preserve provider-neutral identifiers, then update adapter configuration.
- **Database, Keycloak, and configuration backups:** migrate encrypted artifacts plus checksum/retention manifests and prove restore before deletion at the source.
- **Audit exports:** preserve authorization, integrity metadata, retention, and access logging requirements.
- **Checksums:** calculate and compare approved checksums at source and destination; do not rely only on provider-specific entity tags.
- **Retention:** represent business retention intent portably and implement provider lifecycle/deletion policy in infrastructure.
- **Authorization:** services authorize access before issuing short-lived download URLs; bucket/provider policy supplies defense in depth.
- **Versioning:** map provider versions to AlgaGuard-owned version identifiers where exact provider parity is unavailable.

## Follow-up actions

1. Specify the provider-neutral interface and error/metadata types in Phase 2, without importing provider SDK definitions.
2. Add S3/MinIO compatibility and checksum tests during the relevant implementation phase.
3. Define signing-key separation, signed-URL limits, retention, encryption, and restore evidence before production use.
4. Rehearse object migration with checksummed manifests before campus cutover.
