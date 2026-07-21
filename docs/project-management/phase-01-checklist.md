# Phase 1 checklist

Checked boxes mean the Phase 1 documentation exists. They do not mean hardware is validated or software is implemented.

Progress: 36 completed documentation items and 7 open external validation/sign-off items, 43 total.

## Architecture and scope

- [x] Polyrepo and repository ownership documented.
- [x] Full target, system context, runtime, deployment, security, and ASCII architectures documented.
- [x] One-device AWS pilot, campus deployment, and full scale target separated.
- [x] Direct MQTT pilot path and later Kafka path documented.
- [x] Docker Compose first and Kubernetes later recorded.
- [x] Node.js + Express + TypeScript decision recorded.
- [x] NestJS, Java/Spring, and Python excluded from the MVP unless a later ADR approves an exception.
- [x] Keycloak human authentication and separate device identity documented.
- [x] Cloud-agnostic object/email provider adapters documented.
- [x] Provider interfaces, environment variables, identity boundary, event publishing, backups, and notification portability documented in [provider adapters](../backend/provider-adapters.md).
- [x] Temporary AWS EC2 decision recorded in [ADR-014](../decisions/ADR-014-aws-temporary-host.md).
- [x] Final campus-server decision recorded in [ADR-015](../decisions/ADR-015-campus-final-host.md).
- [x] S3/MinIO object-storage abstraction recorded in [ADR-016](../decisions/ADR-016-s3-compatible-storage.md).

## Hardware and firmware

- [x] PCF8563 documented.
- [x] RTC/NTP priority and timestamp-quality flags defined.
- [x] Shared I2C GPIO8/GPIO9 plan completed.
- [x] Three LED channels allocated to GPIO14/GPIO15/GPIO16.
- [x] LED resistor calculation deferred to electrical validation; no mandatory MOSFET or PWM.
- [x] `CONFIRMED` 3S arrangement documented.
- [x] Selected BMS, charger, Samsung cell candidate, and 12 V adapter documented.
- [x] Charge-while-operating power path marked unresolved.
- [x] Enclosure candidates compared and IP65 connector limitation documented.
- [x] One-second sample/display rate, ten-second batch, and two-day local minimum documented.
- [x] Firmware components, tasks, menu, provisioning, storage, and OTA targets documented.
- [ ] Exact board/module marking and runtime memory verified.
- [ ] I2C addresses and MicroSD electrical behavior bench-verified.
- [ ] Power path, buck, fuse, pack assembly, isolation boundary, and thermal design approved.
- [ ] Enclosure fit and ingress design physically validated.

## Backend and product

- [x] Service/repository boundaries and data ownership documented.
- [x] Profile Service and algae profile flow documented without scientific thresholds.
- [x] Sharing, organizations, roles, pagination, throttling, and audit controls documented.
- [x] Device onboarding and claiming flow documented.
- [x] Telemetry idempotency and application acknowledgement documented.
- [x] OTA release/rollback architecture documented.

## Capacity, cost, migration, and governance

- [x] Cloud retention modes and capacity estimates completed for 1, 100, and 1,000 devices and three record sizes.
- [x] AWS six-month temporary-host plan and dated official-source check documented.
- [x] AWS cost guardrails and fallback documented.
- [x] Campus migration, checksums, parallel validation, rollback, and stable DNS cutover documented.
- [x] Full-scale limitations separated from free-pilot claims.
- [x] Risks, open questions, definition of done, contribution rules, and ADRs documented.
- [ ] AWS account eligibility, region, size, and budget confirmed before deployment.
- [ ] Campus infrastructure facts confirmed by administrators.
- [ ] Scientific thresholds approved by supervisor before any default template is published.
