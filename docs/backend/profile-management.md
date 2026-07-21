# Profile management

The Profile Service is the authoritative domain for user-defined algae profiles. Phase 1 does not define scientifically valid thresholds.

## Data model intent

A profile has ownership/sharing metadata and immutable versions. Each version records user-configured parameter units and minimum warning, maximum warning, and critical threshold fields for temperature, pH, light, nitrate, phosphate, and potassium. Exact critical-bound representation and cross-field validation are `TBD` for Phase 2.

## Rules

- Users create or clone profiles; authorized organization members may share them.
- Validate numeric representation, units, ordering/consistency, allowed sizes, and completeness. Contract validation is not scientific approval.
- Publishing creates a new version. It never rewrites historic telemetry.
- A device/tank references one active profile version.
- Command/configuration delivery sends a compact versioned threshold document to the device.
- The ESP32 caches the last valid assigned profile for offline local alerts and reports the applied version.
- Historical alerts retain the exact profile version used during evaluation.
- Profile changes, assignments, activation/deactivation, and sharing are audited.
- Supervisor-approved default templates may be added later only after scientific review.

The end-user sequence is shown in [algae profile flow](../product/algae-profile-flow.md).
