# ADR-005: Keycloak for human authentication

## Status

Accepted

## Date

2026-07-22

## Context

People need registration, login, verification, reset, and standards-based tokens without making a cloud vendor mandatory.

## Decision

Use Keycloak for human OIDC/OAuth 2.0 authentication. Devices use separate credentials/certificates. The Access Service performs organization/device authorization.

## Alternatives

Custom authentication; Amazon Cognito; shared human/device identity.

## Consequences

Keycloak data, configuration, email, backups, upgrades, and restore become operational responsibilities.

## Benefits

Portable standards-based identity and separation of authentication from domain authorization.

## Risks

Resource use and incorrect realm/client configuration.

## Follow-up

Threat-model flows and define realm/client configuration in Phase 7.
