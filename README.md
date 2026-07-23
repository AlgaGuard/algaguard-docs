# AlgaGuard Documentation

AlgaGuard is an IoT platform for monitoring microalgae cultivation parameters. Phase 1 defines the product and safety boundaries, Phases 2 and 2.1 establish contracts, and the platform-first foundation implements the portable software vertical slice plus a minimum USB-powered firmware base. Production deployment and physical-device acceptance remain open.

## Phase 1 status

Phase 1 documentation foundation is complete. Hardware, electrical, scientific, AWS-account and campus-infrastructure sign-off remain open.

- **CONFIRMED:** The prototype uses one ESP32-S3 demonstration device and generated mock parameter data.
- **CONFIRMED:** The temporary host is AWS for approximately six months; the final host is a campus Linux server.
- **CONFIRMED:** The design target is 1,000 devices, 10,000 users, and 500 or more organizations.
- **SAFETY:** The 3S battery power path and charge-while-operating design are not approved for construction.

## Phase 2.1 status

Phase 2.1 WebSocket contracts and architecture are complete on `develop`. The platform-first phase subsequently implemented the Realtime Service and its React/Flutter consumers without changing the transport boundary: MQTT/TLS remains the ESP32 transport, HTTPS remains authoritative for queries, recovery, and commands, and WSS provides non-durable live client updates.

## Platform-first foundation

The portable Compose stack, service repositories, React dashboard, Flutter application, realtime path, onboarding/claim flow, OTA control plane, and USB-only ESP32-S3 foundation are implemented on their respective `develop` branches. Local automated validation proves the software vertical slice. AWS/campus deployment, real-board tests, signing-custody approval, battery/power safety, MicroSD electrical validation, and scientific thresholds remain open.

## Device identity resolution

**CONFIRMED:** Canonical device IDs remain the firmware and MQTT identity. Internal UUIDs are the authoritative REST, authorization and WebSocket resource identity. Organization context is added only by trusted backend services. The mapping, restart, negative-input, duplicate-replay, and no-leak ownership-transfer paths are implemented and validated on `develop`; see [ADR-018](docs/decisions/ADR-018-dual-device-identity.md).

## Production device credential foundation

**CONFIRMED:** Each simulated device generates its private key locally, submits a CSR through one-time bootstrap authorization, and uses its own X.509 certificate for EMQX mutual TLS. Exact topic ACLs, persistent rotation/revocation, restart recovery, and authenticated telemetry/profile/command/OTA flows are locally validated. See [ADR-019](docs/decisions/ADR-019-per-device-x509-credentials.md), the [device credential lifecycle](docs/backend/device-credential-lifecycle.md), and [transport limits](docs/architecture/transport-security-limits.md).

**TBD:** Production CA selection, cloud/campus deployment, public DNS, and physical ESP32 validation remain open.

## Start here

- [System overview](docs/architecture/system-overview.md)
- [Platform-first foundation](docs/architecture/platform-first-foundation.md)
- [Hardware inventory](docs/hardware/hardware-inventory.md)
- [Preliminary pin plan](docs/hardware/preliminary-pin-plan.md)
- [Firmware overview](docs/firmware/firmware-overview.md)
- [Backend services](docs/backend/microservices-overview.md)
- [Realtime Service](docs/backend/realtime-service.md)
- [Realtime user experience](docs/product/realtime-user-experience.md)
- [Provider adapters](docs/backend/provider-adapters.md)
- [ADR-014: AWS temporary host](docs/decisions/ADR-014-aws-temporary-host.md)
- [ADR-015: Campus final host](docs/decisions/ADR-015-campus-final-host.md)
- [ADR-016: S3-compatible storage](docs/decisions/ADR-016-s3-compatible-storage.md)
- [ADR-017: Portable WebSocket Realtime Service](docs/decisions/ADR-017-websocket-realtime-service.md)
- [ADR-018: Dual device identity](docs/decisions/ADR-018-dual-device-identity.md)
- [ADR-019: Per-device X.509 credentials](docs/decisions/ADR-019-per-device-x509-credentials.md)
- [Device credential lifecycle](docs/backend/device-credential-lifecycle.md)
- [Transport security limits](docs/architecture/transport-security-limits.md)
- [Implementation roadmap](docs/project-management/implementation-roadmap.md)
- [Phase 1 checklist](docs/project-management/phase-01-checklist.md)
- [Open questions](docs/project-management/open-questions.md)

Labels used throughout this repository are defined in [AGENTS.md](AGENTS.md).
