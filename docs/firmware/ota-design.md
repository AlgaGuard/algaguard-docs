# OTA design

Signed OTA and rollback are production targets, not Phase 1 implementation claims.

```mermaid
sequenceDiagram
    participant A as Authorized Admin
    participant O as OTA Service
    participant S as S3-Compatible Storage
    participant M as EMQX
    participant D as ESP32 Device

    A->>O: Create versioned release and rollout ring
    O->>S: Store signed firmware and manifest reference
    O->>M: Publish release notification
    M->>D: Deliver authorized OTA command
    D->>O: Fetch manifest over HTTPS
    D->>D: Verify signature, model, version, and policy
    D->>S: Download firmware over HTTPS
    D->>D: Verify hash/signature and write inactive slot
    D->>D: Reboot and run health confirmation
    alt Healthy
        D->>O: Report success
    else Failed or timeout
        D->>D: Roll back to known-good image
        D->>O: Report rollback when connected
    end
```

## Requirements

- Firmware binaries and manifests live behind the S3-compatible storage interface: optional Amazon S3 on AWS, MinIO/campus storage later.
- Signing keys are separated from runtime storage credentials and never committed.
- Validate device model, hardware compatibility, version monotonicity/downgrade policy, size, hash, signature, expiry, and rollout authorization.
- Use rings, pauses, progress, timeout, retry limits, failure thresholds, and auditable rollback.
- Network loss must not invalidate the currently bootable image.
- Exact ESP-IDF partition layout, key custody, anti-rollback policy, and recovery procedure are `TBD` for the implementation phase.
