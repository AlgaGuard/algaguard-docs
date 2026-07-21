# System context

```mermaid
flowchart LR
    USER["Owner, Admin, Operator, or Viewer"]
    ADMIN["System Administrator"]
    DEVICE["AlgaGuard ESP32-S3 Device"]
    SYSTEM["AlgaGuard Platform"]
    EMAIL["Replaceable Email Provider"]
    NTP["NTP Servers"]
    OBJECTS["S3-Compatible Object Storage"]

    USER -->|"Web or mobile HTTPS"| SYSTEM
    USER -->|"BLE Wi-Fi provisioning"| DEVICE
    ADMIN -->|"Operate and observe"| SYSTEM
    DEVICE -->|"MQTT over TLS"| SYSTEM
    DEVICE -->|"HTTPS OTA"| SYSTEM
    DEVICE -->|"UTC synchronization"| NTP
    SYSTEM -->|"Invitations and alerts"| EMAIL
    SYSTEM -->|"Firmware and artifacts"| OBJECTS
```

The platform authenticates people through Keycloak and devices through separate device identities. Its authorization rules are defined in [access control](../backend/access-control.md).
