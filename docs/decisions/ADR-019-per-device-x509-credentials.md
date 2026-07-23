# ADR-019: Portable per-device X.509 credentials and MQTT mutual TLS

## Status

Accepted and implemented for the Production Device Credential Sprint

## Date

2026-07-24

## Context

AlgaGuard devices need an identity that survives migration between local development, a temporary AWS host, and a campus Linux host without depending on one cloud certificate product. A shared MQTT password cannot identify one compromised device, enforce exact device-topic permissions, or support isolated rotation and revocation.

The accepted dual identity model already assigns distinct roles to canonical `deviceId`, internal `deviceUuid`, and backend-owned `organizationId`. The credential design must preserve those roles, keep private keys off the backend, and remain compatible with released contracts.

## Decision

**CONFIRMED:** Each device uses its own X.509 client credential for MQTT mutual TLS. Shared device MQTT credentials are forbidden.

**CONFIRMED:** The device or simulator generates an EC P-256 or supported RSA private key locally and creates a CSR. The private key never appears in a Device Service request, response, database row, event, audit record, or log. The production firmware interface exposes an opaque key handle and deliberately provides no key-export method.

**CONFIRMED:** A device certificate binds exactly one identity pair:

| Certificate field | Binding |
| --- | --- |
| Subject common name | Canonical `deviceId`, for example `AG-000001` |
| SAN URI | Exactly `urn:algaguard:device:<deviceUuid>` |
| `organizationId` | Omitted; current ownership is resolved by the backend |

CN is not the sole authorization source. EMQX and Device Service require the supplied MQTT client ID, certificate common name, SAN UUID, stored fingerprint, credential status, and current device lifecycle to agree.

**CONFIRMED:** Device Service owns persistent credential metadata and the lifecycle states `PENDING`, `ACTIVE`, `ROTATING`, `REVOKED`, `EXPIRED`, `COMPROMISED`, and `FAILED`. It stores certificate serial/fingerprint/public certificate, validity, binding, status, rotation links, revocation reason, and audit timestamps. History is retained rather than deleted.

**CONFIRMED:** Initial issuance uses a short-lived, one-use bootstrap authorization stored as a cryptographic hash. Issuance verifies claim state, device scope, expiry, attempt budget, lifecycle, CSR signature/algorithm, and the exact CSR identity. An idempotency key makes duplicate issuance safe, while database locking prevents concurrent active initial credentials.

**CONFIRMED:** Rotation uses a bounded two-certificate overlap. The predecessor remains usable until the replacement proves an MQTT connection and the device acknowledges it, or until the overlap expires. A successful acknowledgement activates the child credential and revokes its parent with reason `ROTATED`. Emergency compromise bypasses overlap and revokes immediately.

**CONFIRMED:** The local implementation uses explicit development device and service CAs under a Git-ignored directory. EMQX requires a device certificate on the externally bound listener and a service certificate on its Compose-only backend listener. Plaintext MQTT and default WebSocket listeners are disabled.

**TBD:** The production CA provider, root/intermediate hierarchy, custody ceremony, renewal automation, high-availability signing design, and any CRL/OCSP publication remain undecided. The Device Service depends on a provider-neutral CA interface: `issueClientCertificate`, `revokeCertificate`, `inspectCertificate`, `getCaChain`, and `validateIssuedCertificate`. AWS Private CA is not mandatory.

## Authorization and topic isolation

Device Service is the live credential-state authority. EMQX performs TLS chain/time validation and calls the protected Device Service authentication endpoint. A short bounded authorization cache limits revocation delay. The returned ACL lists exact topics derived from the authenticated canonical `deviceId`; certificates cannot access another device, broad wildcards, internal topics, management topics, or `$SYS/#`.

Backend MQTT services use separate service certificates and narrowly scoped file ACLs. MQTT Ingestion then verifies broker identity, topic `deviceId`, payload `deviceId`, and trusted Device Service context before accepting telemetry.

Human credential operations remain separate. Keycloak authenticates the person, Access Service authorizes the action against current organization ownership, and Device Service performs the credential transition. Transfer follows current ownership because `organizationId` is not embedded in the certificate.

## Portability and private-key custody

- Local CA material exists only for explicit development and CI initialization.
- Production cannot silently select the local development CA adapter.
- Broker, CA, and OTA endpoints/paths are configuration, not provider-specific domain fields.
- Migrating hosts preserves credential metadata, EMQX policy, and protected CA integration without changing MQTT or certificate identity contracts.
- Development CA, device keys, service keys, signing keys, E2E keys, and evidence directories are ignored by Git and excluded from CI artifacts.
- Production firmware is designed to back opaque keys with encrypted NVS/flash where available; this is not a secure-element claim.

## Consequences

- Compromise and rotation are isolated per device.
- Broker admission requires both cryptographic trust and current application state; a cryptographically valid but unknown or revoked certificate is rejected.
- Device Service availability and bounded cache behavior affect new MQTT connections.
- Operators must monitor bootstrap failures, binding mismatches, expired/revoked attempts, rotation recovery, and certificate validity windows.
- CA keys and signing operations require an approved production custody model before deployment.

## Alternatives considered

- One shared MQTT username/password for all devices.
- A per-device reusable plaintext password.
- Trusting the certificate chain without checking persistent Device Service state.
- Binding only canonical `deviceId` in CN.
- Embedding `organizationId` in the certificate.
- Making AWS Private CA a mandatory core dependency.
- Returning a generated private key from the backend.

These alternatives weaken isolation, make ownership transfer unsafe, expose key material, or violate provider portability.

## Validation and limitations

**CONFIRMED:** Local simulated validation covers one-time bootstrap, local CSR generation, strict binding, EMQX mTLS, exact own-topic allow/cross-device deny, telemetry acknowledgement, realtime delivery, profile/command/OTA flows, rotation, predecessor denial, service restart recovery, and revocation after a full stop/restart with retained volumes. Wrong-CA, unknown, expired, missing, and mismatched certificates are rejected.

**CONFIRMED:** Firmware credential abstractions and host tests exist, and CI is required to build the ESP32 target without a board.

**TBD:** No AWS or campus deployment, public DNS validation, production CA operation, CRL/OCSP service, real ESP32 flash/test, physical BLE, MicroSD integration, battery/3S design, or scientific threshold validation has been completed.

## Related documents

- [Device credential lifecycle](../backend/device-credential-lifecycle.md)
- [Transport security limits](../architecture/transport-security-limits.md)
- [Security architecture](../architecture/security-architecture.md)
- [ADR-003: MQTT device communication](ADR-003-mqtt-device-communication.md)
- [ADR-018: Dual device identity](ADR-018-dual-device-identity.md)

