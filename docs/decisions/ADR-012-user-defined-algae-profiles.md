# ADR-012: User-defined algae profiles

## Status

Accepted

## Date

2026-07-22

## Context

Different cultivation contexts need thresholds, but Phase 1 has no supervisor-approved scientific values.

## Decision

Create `algaguard-profile-service` for user-created, versioned, cloneable, shareable profiles with parameter units and warning/critical thresholds. Assign an immutable version to a tank/device, deliver it through configuration, cache it on the ESP32, and retain its version with historical alerts.

## Alternatives

Hard-code thresholds; store mutable thresholds on devices only; publish unreviewed default profiles.

## Consequences

UI and APIs must label values user-configured and distinguish structural validation from scientific approval.

## Benefits

Flexible offline alerting with audit and historical traceability.

## Risks

Unsafe or nonsensical user values, unit mistakes, or stale device configuration.

## Follow-up

Define unit/range schemas and approval labeling in Phase 2; add templates only after scientific review.
