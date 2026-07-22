# AlgaGuard Documentation

AlgaGuard is a planned IoT platform for monitoring microalgae cultivation parameters. Phase 1 defines the product, hardware, firmware, cloud-agnostic platform, capacity, safety constraints, and delivery roadmap. Phase 2.1 adds the accepted portable WebSocket architecture for later React and Flutter live updates. It does not implement the product or the Realtime Service.

## Phase 1 status

Phase 1 documentation foundation is complete. Hardware, electrical, scientific, AWS-account and campus-infrastructure sign-off remain open.

- **CONFIRMED:** The prototype uses one ESP32-S3 demonstration device and generated mock parameter data.
- **CONFIRMED:** The temporary host is AWS for approximately six months; the final host is a campus Linux server.
- **CONFIRMED:** The design target is 1,000 devices, 10,000 users, and 500 or more organizations.
- **SAFETY:** The 3S battery power path and charge-while-operating design are not approved for construction.

## Phase 2.1 status

Phase 2.1 WebSocket contracts and architecture are complete on `develop`. MQTT/TLS remains the ESP32 transport; HTTPS remains authoritative for queries, recovery, and commands; WSS provides non-durable live client updates. The planned `algaguard-realtime-service` is deferred to a later application phase, and Phase 3 has not started.

## Start here

- [System overview](docs/architecture/system-overview.md)
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
- [Implementation roadmap](docs/project-management/implementation-roadmap.md)
- [Phase 1 checklist](docs/project-management/phase-01-checklist.md)
- [Open questions](docs/project-management/open-questions.md)

Labels used throughout this repository are defined in [AGENTS.md](AGENTS.md).
