# Implementation roadmap

Phase 1 documentation and the Phase 2/2.1 contract foundation are complete. A platform-first implementation has now delivered the portable application vertical slice and minimum USB-powered firmware foundation across the implementation repositories. The numbered roadmap below remains useful as a capability and physical-acceptance backlog; its original order no longer represents implementation chronology. See the [platform-first foundation](../architecture/platform-first-foundation.md) for delivered scope and evidence boundaries.

## Platform-first foundation - portable software vertical slice

- **Objective:** Prove onboarding, identity/access, simulated telemetry, durable storage, realtime delivery, commands, OTA control, clients, and portable infrastructure before hardware refinement.
- **Repositories:** Infrastructure, service template, nine backend services, web dashboard, Flutter app, firmware, contracts, and documentation.
- **Delivered:** Docker Compose stack, repository CI/CD, one-time QR claim, BLE provisioning abstractions, Keycloak login, EMQX/TimescaleDB/Redis/MinIO paths, WSS tickets, role-aware clients, signed-OTA pipeline definition, USB-only firmware modules, and automated development validation.
- **Evidence:** Local service checks and the automated Compose vertical slice pass. Flutter analysis/tests and host firmware tests pass.
- **Open gates:** AWS/campus hosts, production secrets and signing custody, real ESP32-S3 flashing/bench validation, Android package confirmation, battery/power review, MicroSD electrical validation, and scientific threshold approval.
- **Safety:** No 3S battery, charger, battery percentage, power-path, or battery ADC work is included.

## Device identity resolution sprint - completed on `develop`

- **Objective:** Resolve the canonical MQTT identifier and UUID WebSocket resource boundary without changing released v1 contracts or creating WebSocket v2.
- **Repositories:** Contracts, Device, Access, MQTT Ingestion, Telemetry, Realtime, Infrastructure, and documentation.
- **Delivered:** Immutable canonical `deviceId`, stable internal `deviceUuid`, backend-owned organization context, monotonic ownership version/history, authenticated context resolution, UUID authorization, organization-at-ingest persistence, post-commit events, and UUID/org realtime routing.
- **Evidence:** Contract/service validation and hosted CI pass. A fresh-volume Compose test covers migrations, negative identities, normal authorized delivery, cross-organization denial, five-service restart, transfer/revocation, historical no-leak, stale-version rejection, and duplicate replay.
- **Compatibility:** MQTT v1 remains canonical-ID based; WebSocket v1 subscriptions remain UUID based; compatible v1.1 telemetry events carry both identifiers; released v1 files are unchanged.
- **Open gates:** Production cache/load tuning, deployment-specific service credentials, and any broader pre-transfer audit-history policy. AWS, campus, web/mobile/firmware follow-on work is not part of this sprint.
- **Decision:** [ADR-018](../decisions/ADR-018-dual-device-identity.md).

## Phase 1 - Documentation, validation planning, cost, and capacity

- **Objective:** Establish reviewed decisions and safe implementation gates.
- **Repositories:** `algaguard-docs`.
- **Dependencies:** Confirmed project brief and supplied references.
- **Main tasks:** Architecture, hardware/power/time, service boundaries, product flows, [provider adapters](../backend/provider-adapters.md), ADRs, capacity, AWS/campus, risk, open questions.
- **Deliverables:** This Phase 1 document set, validation report, [AWS host ADR](../decisions/ADR-014-aws-temporary-host.md), [campus host ADR](../decisions/ADR-015-campus-final-host.md), and [S3-compatible storage ADR](../decisions/ADR-016-s3-compatible-storage.md).
- **Acceptance criteria:** [Definition of done](definition-of-done.md) and accurate [checklist](phase-01-checklist.md).
- **Risks:** Treating plans as implementation; approving unsafe wiring or invented thresholds.
- **Free-pilot impact:** Defines the smallest safe, portable pilot.
- **Scale-target impact:** Quantifies why pilot evidence is insufficient.
- **Out of scope:** All source code, deployment files, workflows, cloud resources, and physical battery construction.

## Phase 2 - Contracts and development standards

- **Objective:** Freeze versioned cross-repository contracts and engineering conventions.
- **Repositories:** `algaguard-contracts`, `algaguard-service-template`, `algaguard-docs`.
- **Dependencies:** Phase 1 decisions and resolved schema questions.
- **Main tasks:** OpenAPI/AsyncAPI, telemetry/batch/ack/time/profile schemas, topic naming/ACL model, error/idempotency rules, service standards, compatibility/version policy.
- **Deliverables:** Reviewable schemas, examples, contract tests/specifications, development standards.
- **Acceptance criteria:** Firmware, client, and service stakeholders approve versioning and representative flows; no secrets/scientific defaults.
- **Risks:** Prematurely rigid contracts or ambiguous acknowledgement/time semantics.
- **Free-pilot impact:** Enables one vertical slice without interface drift.
- **Scale-target impact:** Provides stable partition/evolution keys.
- **Out of scope:** Runtime services, UI, firmware features, deployment.

## Phase 2.1 - Realtime WebSocket contracts and architecture

- **Objective:** Define cloud-agnostic live updates for React and Flutter without replacing MQTT, HTTPS recovery, or HTTPS commands.
- **Repositories:** `algaguard-contracts`, `algaguard-docs`; planned later `algaguard-realtime-service`.
- **Dependencies:** Phase 2 base envelopes, service ownership, Keycloak/API Gateway authentication, and Access Service authorization boundaries.
- **Main tasks:** WebSocket envelopes/events, subscribe/unsubscribe/ping, one-time ticket flow, per-resource authorization, Redis Pub/Sub fan-out, recovery, AsyncAPI, architecture, compatibility, and validation.
- **Deliverables:** Versioned WebSocket schemas and examples, separate AsyncAPI document, protocol policies, Realtime Service architecture, client experience, and ADR-017.
- **Acceptance criteria:** Contract checks and documentation validation pass; all event sources and authorization/recovery rules are explicit; no runtime implementation is created.
- **Risks:** Token leakage, stale authorization, missed events, reconnect storms, slow clients, or treating live delivery as authoritative.
- **Free-pilot impact:** Defines an optional later live-update path while HTTPS polling/recovery remains viable.
- **Scale-target impact:** Establishes independently scalable connections, subscriptions, backpressure, and metrics; exact limits require evidence.
- **Out of scope:** Creating `algaguard-realtime-service`, consumer code, firmware, deployment, and Phase 3.

## Phase 3 - Firmware foundation and board bring-up

- **Objective:** Prove the exact board, toolchain, display, RTC, buttons, and indicators.
- **Repositories:** `algaguard-firmware`, `algaguard-contracts`.
- **Dependencies:** Phase 2 base contracts; electrical bench approval for connected modules.
- **Main tasks:** PlatformIO/ESP-IDF setup, runtime memory report, USB/UART, I2C scan, RTC integrity/time, OLED, debounced buttons, digital LEDs, diagnostics.
- **Deliverables:** Tested firmware foundation and recorded physical measurements.
- **Acceptance criteria:** Exact marking/memory confirmed; reserved pins untouched; UI/RTC/buttons/LEDs pass bench tests.
- **Risks:** Variant mismatch, voltage incompatibility, inadequate FreeRTOS ownership.
- **Free-pilot impact:** Creates the physical UI foundation.
- **Scale-target impact:** Establishes repeatable device diagnostics.
- **Out of scope:** Mock storage/upload, production OTA, battery pack assembly without review.

## Phase 4 - Mock sampling, SD storage, and batching

- **Objective:** Produce durable one-second mock data and recoverable ten-second batches.
- **Repositories:** `algaguard-firmware`, `algaguard-contracts`.
- **Dependencies:** Phases 2-3; MicroSD electrical validation.
- **Main tasks:** Seeded mock generator, timestamp quality, append/recovery, ring retention, sequences, batch ledger, replay tests.
- **Deliverables:** Local offline data pipeline and test evidence.
- **Acceptance criteria:** Two-day policy demonstrated by calculation/test; reset/corruption scenarios handled; no fabricated UTC.
- **Risks:** Card corruption, wear, lost acknowledgements, replay storms.
- **Free-pilot impact:** Makes the demo useful offline.
- **Scale-target impact:** Defines upload shape and idempotency.
- **Out of scope:** Hosted broker/service and physical sensors.

## Phase 5 - Local portable infrastructure and EMQX

- **Objective:** Run the cloud-agnostic platform locally using Docker Compose.
- **Repositories:** `algaguard-infrastructure`, service repositories as needed.
- **Dependencies:** Phase 2 contracts; container standards.
- **Main tasks:** NGINX, EMQX, Keycloak, PostgreSQL/TimescaleDB, Redis, MinIO, Mailpit, TLS/dev certificates, backups, lightweight observability.
- **Deliverables:** Documented local portable stack.
- **Acceptance criteria:** Health, persistence, restart, TLS, broker ACL, and restore smoke tests pass.
- **Risks:** Resource exhaustion or insecure development defaults escaping environments.
- **Free-pilot impact:** Rehearses the AWS stack locally.
- **Scale-target impact:** Preserves portable components, not production sizing proof.
- **Out of scope:** AWS deployment, Kubernetes, Kafka.

## Phase 6 - MQTT ingestion and telemetry vertical slice

- **Objective:** Deliver acknowledged device batches into queryable telemetry.
- **Repositories:** `algaguard-mqtt-ingestion-service`, `algaguard-telemetry-service`, `algaguard-contracts`, `algaguard-firmware`, `algaguard-infrastructure`.
- **Dependencies:** Phases 2, 4, and 5.
- **Main tasks:** TLS/device ACLs, envelope validation, idempotency, Timescale partitioning, application acknowledgements, replay/gap metrics.
- **Deliverables:** End-to-end direct MQTT pilot flow.
- **Acceptance criteria:** Duplicate/reconnect/power-loss tests produce no unintended duplicate logical samples and preserve pending data.
- **Risks:** Ack boundary errors, database backpressure, oversized messages.
- **Free-pilot impact:** First hosted-value vertical slice.
- **Scale-target impact:** Provides load-test baseline.
- **Out of scope:** Kafka, human accounts, dashboards.

## Phase 7 - Keycloak and API Gateway

- **Objective:** Secure human entry with OIDC and common API controls.
- **Repositories:** `algaguard-api-gateway`, `algaguard-infrastructure`, `algaguard-contracts`.
- **Dependencies:** Phase 5; threat model and email choice as needed.
- **Main tasks:** Realm/clients, registration/verification/reset, JWT validation, rate limits, correlation IDs, routing, SSE/WebSocket decision.
- **Deliverables:** Authenticated API entry and operational runbook.
- **Acceptance criteria:** Token, logout/expiry, unauthorized/forbidden, rate-limit, backup/restore tests pass.
- **Risks:** OIDC misconfiguration or authorization delegated to clients.
- **Free-pilot impact:** Enables real user accounts.
- **Scale-target impact:** Establishes stateless scalable entry.
- **Out of scope:** Device claim and sharing policies.

## Phase 8 - Device registration, BLE provisioning, and claiming

- **Objective:** Securely bind one physical device to an account/organization and Wi-Fi.
- **Repositories:** `algaguard-device-service`, `algaguard-mobile-app`, `algaguard-firmware`, `algaguard-contracts`, `algaguard-audit-service`.
- **Dependencies:** Phases 3, 6, 7; provisioning/claim threat model.
- **Main tasks:** Device identity, QR/claim tokens, BLE setup, credential storage, claim expiry/replay protection, audit.
- **Deliverables:** Authenticated onboarding flow.
- **Acceptance criteria:** Happy path, expired/reused claim, wrong user/device, reset, offline, and secret-leak tests pass.
- **Risks:** Device takeover or Wi-Fi/device secret exposure.
- **Free-pilot impact:** Product-like first-run setup.
- **Scale-target impact:** Establishes device identity lifecycle.
- **Out of scope:** Bulk provisioning and fleet manufacturing system.

## Phase 9 - Access Service and sharing

- **Objective:** Enforce organizations, roles, invitations, revocation, and ownership transfer.
- **Repositories:** `algaguard-access-service`, clients, `algaguard-audit-service`, `algaguard-notification-service`, `algaguard-contracts`.
- **Dependencies:** Phases 7-8; email adapter if invitations use email.
- **Main tasks:** Membership schema, authorization checks, one-time invitations, pagination, throttling, transfer/revocation, audit.
- **Deliverables:** Server-enforced sharing flow.
- **Acceptance criteria:** Permission matrix and cross-tenant/isolation tests pass; all high-value changes audited.
- **Risks:** Privilege escalation, enumeration, unbounded membership queries.
- **Free-pilot impact:** Demonstrates multi-user product behavior.
- **Scale-target impact:** Supports 10,000 users/500+ organizations subject to tests.
- **Out of scope:** Bulk imports and artificial unlimited-capacity claims.

## Phase 10 - Profile Service and algae threshold profiles

- **Objective:** Create versioned user-defined profiles and deliver active versions to devices.
- **Repositories:** `algaguard-profile-service`, `algaguard-device-service`, `algaguard-command-service`, clients, firmware, contracts, audit.
- **Dependencies:** Phases 2, 8-9.
- **Main tasks:** Units/structural validation, immutable versions, clone/share/assign, compact configuration, device cache/reporting, audit.
- **Deliverables:** End-to-end profile creation/assignment/offline use.
- **Acceptance criteria:** Historical data/alerts remain version-linked; invalid/unit-confused input rejected; device confirms applied version.
- **Risks:** Users mistake inputs for scientific advice or device runs stale values.
- **Free-pilot impact:** Enables a meaningful configurable demo.
- **Scale-target impact:** Establishes versioned multi-tenant configuration.
- **Out of scope:** Unreviewed default species thresholds.

## Phase 11 - React dashboard

- **Objective:** Provide accessible web monitoring and administration.
- **Repositories:** `algaguard-web-dashboard`, `algaguard-contracts`.
- **Dependencies:** Phases 7-10.
- **Main tasks:** React/TypeScript/Vite, Router, TanStack Query, ECharts or equivalent, OIDC, device/profile/sharing/telemetry views, states/accessibility.
- **Deliverables:** Tested web dashboard.
- **Acceptance criteria:** Role-aware UX plus server enforcement, timezone display, mock-source/time-quality labels, responsive/accessibility checks.
- **Risks:** Client-side authorization assumptions and misleading charts.
- **Free-pilot impact:** Main demonstration dashboard.
- **Scale-target impact:** Paginated/aggregated queries avoid unbounded client loads.
- **Out of scope:** Native mobile BLE provisioning replacement.

## Phase 12 - Flutter application

- **Objective:** Deliver mobile monitoring and BLE-first provisioning.
- **Repositories:** `algaguard-mobile-app`, `algaguard-contracts`.
- **Dependencies:** Phases 7-11 and mobile platform test devices.
- **Main tasks:** Dart, Riverpod, GoRouter, Dio, OIDC client, secure storage, BLE, scanner, local Drift/SQLite cache where justified.
- **Deliverables:** Tested mobile app for supported platforms.
- **Acceptance criteria:** OIDC, BLE permission/error, secure token storage, offline cache, timezone, role, and mock-label tests pass.
- **Risks:** Platform BLE differences and insecure local data.
- **Free-pilot impact:** Required provisioning/user experience.
- **Scale-target impact:** Efficient caching/pagination limits API load.
- **Out of scope:** Firebase-dependent push.

## Phase 13 - Commands, configuration, and indicator control

- **Objective:** Reliably deliver authorized expiring commands and configuration.
- **Repositories:** `algaguard-command-service`, firmware, clients, contracts, ingestion, audit.
- **Dependencies:** Phases 6, 8-12.
- **Main tasks:** Command IDs, expiry/idempotency, MQTT delivery/results, LED priority, profile/config status, UI/audit.
- **Deliverables:** Traceable command lifecycle.
- **Acceptance criteria:** Offline, duplicate, expired, unauthorized, and conflicting-command tests pass.
- **Risks:** Replay or unsafe/stale configuration.
- **Free-pilot impact:** Demonstrates remote LED/config control.
- **Scale-target impact:** Defines bounded queue/backpressure behavior.
- **Out of scope:** PWM and physical actuator control.

## Phase 14 - Alerts and notifications

- **Objective:** Process local/cloud alerts and send provider-independent notifications.
- **Repositories:** `algaguard-alert-service`, `algaguard-notification-service`, profile/telemetry/device/audit services, clients, contracts.
- **Dependencies:** Phases 9-13; approved email provider.
- **Main tasks:** Alert lifecycle/deduplication, offline/battery/RTC/SD states, user threshold context, SES/SMTP adapters, retry/suppression.
- **Deliverables:** Audited alert and notification flow.
- **Acceptance criteria:** Historical profile version retained; duplicate/flap/rate-limit/provider-failure tests pass.
- **Risks:** Alert storms or lost notifications.
- **Free-pilot impact:** Completes product-like feedback.
- **Scale-target impact:** Requires partitioned evaluation/provider quota planning.
- **Out of scope:** Firebase and unapproved scientific limits.

## Phase 15 - Secure OTA and object storage

- **Objective:** Deliver signed staged firmware with rollback through portable storage.
- **Repositories:** `algaguard-ota-service`, firmware, contracts, infrastructure, audit.
- **Dependencies:** Phases 5, 8, 13; signing/key-custody review.
- **Main tasks:** S3/MinIO adapter, manifests, signing, inactive slot, validation, rings, health confirmation, rollback.
- **Deliverables:** Controlled single-device OTA demonstration and recovery runbook.
- **Acceptance criteria:** Tampered/wrong-model/interrupted/failed-boot tests refuse or roll back safely.
- **Risks:** Key compromise or bricked devices.
- **Free-pilot impact:** Uses minimal object storage.
- **Scale-target impact:** Rings and bandwidth planning protect fleets.
- **Out of scope:** Unsigned firmware and forced global rollout.

## Phase 16 - CI/CD and container publishing

- **Objective:** Automate consistent quality gates and GHCR images.
- **Repositories:** All implementation repositories plus `.github`.
- **Dependencies:** Stable builds/tests and secret policy.
- **Main tasks:** GitHub Actions, reproducible multi-arch images where required, SBOM/scans, signing/provenance decision, protected environments.
- **Deliverables:** Versioned CI pipelines and GHCR artifacts.
- **Acceptance criteria:** Pull requests gate tests/scans; releases are traceable and secrets remain protected.
- **Risks:** Supply-chain compromise or privileged workflow misuse.
- **Free-pilot impact:** Repeatable deployment without paid mandatory CI.
- **Scale-target impact:** Independent releases across polyrepos.
- **Out of scope:** Argo CD desired state until Phase 17.

## Phase 17 - Kubernetes and Argo CD demonstration

- **Objective:** Demonstrate optional orchestration after Compose stability.
- **Repositories:** `algaguard-gitops`, `algaguard-infrastructure`, service repositories.
- **Dependencies:** Phases 5 and 16; available k3s/campus Kubernetes.
- **Main tasks:** Helm, NGINX Gateway Fabric, secrets approach, storage classes, probes/resources, Argo CD, backup/restore.
- **Deliverables:** Reproducible non-pilot-required Kubernetes deployment.
- **Acceptance criteria:** GitOps reconciliation, rollback, TLS, persistence, upgrade, and recovery tests pass.
- **Risks:** Operational complexity and single-node false resilience.
- **Free-pilot impact:** None required; do not deploy EKS.
- **Scale-target impact:** Provides orchestration foundation, not capacity proof.
- **Out of scope:** Kafka unless independently justified.

## Phase 18 - Kafka scale path

- **Objective:** Add Kafka only when direct ingestion evidence requires durable buffering/fan-out.
- **Repositories:** `algaguard-mqtt-kafka-bridge`, affected services, contracts, infrastructure/gitops.
- **Dependencies:** Phase 6 load evidence, ADR approval, operational capacity.
- **Main tasks:** KafkaJS bridge/consumers, partition keys, schemas, offsets, replay, dead-letter handling, observability, migration.
- **Deliverables:** Optional tested scale path.
- **Acceptance criteria:** Demonstrated benefit over direct flow, correct ordering/idempotency/replay, acceptable operations/cost.
- **Risks:** Complexity, duplicate processing, partition hot spots, data loss from bad offset handling.
- **Free-pilot impact:** None; Kafka remains absent.
- **Scale-target impact:** Adds buffer/fan-out when justified.
- **Out of scope:** Direct ESP32-Kafka and Java/Spring stream processor without later ADR.

## Phase 19 - Observability, security hardening, and load testing

- **Objective:** Validate production readiness against measured targets.
- **Repositories:** All implementation, infrastructure, gitops, contracts, and docs repositories.
- **Dependencies:** Implemented vertical/product flows and target infrastructure.
- **Main tasks:** OpenTelemetry, Prometheus/Grafana/Loki/Tempo, SLOs, threat tests, dependency/penetration review, 1/100/1,000-device load models, recovery/chaos/restore tests, capacity revision.
- **Deliverables:** Evidence-backed security, capacity, recovery, and operational reports.
- **Acceptance criteria:** Approved SLO/security/recovery gates and documented residual risks; no free-tier scale claim.
- **Risks:** Test model misses reconnect bursts or shared bottlenecks.
- **Free-pilot impact:** Converts pilot observations into baselines.
- **Scale-target impact:** Determines real infrastructure and whether target is supportable.
- **Out of scope:** Unmeasured production guarantees.
