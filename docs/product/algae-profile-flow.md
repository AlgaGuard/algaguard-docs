# Algae profile flow

```mermaid
sequenceDiagram
    participant U as Authorized User
    participant P as Profile Service
    participant D as Device Service
    participant C as Command Service
    participant E as ESP32
    participant A as Audit Service

    U->>P: Create or clone profile
    U->>P: Enter parameter units and thresholds
    P->>P: Validate structure, units, and consistency
    P->>P: Publish immutable profile version
    U->>P: Assign version to tank/device
    P->>D: Set active profile version reference
    P->>C: Request compact configuration delivery
    C->>E: Send versioned threshold configuration
    E->>E: Validate and cache for offline alerts
    E-->>C: Report applied profile version
    P->>A: Record create/version/assignment audit events
```

Values are user-configured; Phase 1 invents no species thresholds. A later supervisor-approved template is a new reviewed version, not silent scientific authority. Historic telemetry and alerts retain their original profile/version context.
