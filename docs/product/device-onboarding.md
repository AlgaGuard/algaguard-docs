# Device onboarding and claiming

```mermaid
sequenceDiagram
    participant U as User
    participant M as Flutter App
    participant D as ESP32 Device
    participant K as Keycloak
    participant A as API / Device Service
    participant Q as EMQX

    U->>M: Sign in
    M->>K: OIDC authorization
    K-->>M: Tokens
    U->>M: Scan device QR / claim value
    M->>D: Connect over BLE and verify presence
    M->>D: Provision Wi-Fi securely
    D->>A: Register/reach service with device identity
    M->>A: Redeem one-time claim for organization/tank
    A-->>M: Claimed device and permissions
    D->>Q: Connect with separate device credential
    Q-->>D: Authorized MQTT session
```

Claim and BLE proof details are `TBD` for Phase 2. Claim values are one-time, short-lived, resistant to guessing, and never device MQTT credentials. Reclaim, factory reset, ownership transfer, and lost-device recovery require explicit audited policies.
