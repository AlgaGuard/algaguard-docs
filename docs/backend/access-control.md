# Access control

Keycloak authenticates human users. The Access Service makes organization- and device-level authorization decisions, and every backend service enforces them.

## Model

- A user may belong to multiple organizations.
- A device belongs to one organization at a time unless a later ADR changes the model.
- Organization membership and direct device membership are separate.
- Roles are `OWNER`, `ADMIN`, `OPERATOR`, and `VIEWER`.
- No artificial product-level membership cap is documented. APIs still require pagination, rate limits, maximum request size, invitation throttling, abuse monitoring, indexes, and unique constraints.
- Invitations use one-time expiring tokens. Owners can revoke access and transfer ownership.
- Audit invitations, role changes, revocations, and ownership transfers.

## Device resource identity

- Device decisions use the authenticated subject, trusted organization UUID, and `deviceUuid`. A canonical `deviceId` alone is never sufficient for REST or WebSocket authorization.
- Access verifies the current mapping through Device Service rather than querying Device Service tables.
- Organization/device mismatch, unknown UUID, cross-organization access, and revoked membership are denied.
- Realtime checks authorization at subscription and event time. Connection authentication alone grants no device access.
- Ownership transfer increments Device Service `ownershipVersion`, changes future decisions, removes the old organization's active subscription, and permits the new organization only if its user is authorized.
- The critical authorization path currently has no local decision cache. A future cache must be bounded and invalidated on membership, lifecycle, or ownership change.

Permission details are authoritative in [role permissions](../product/role-permissions.md). Hiding a React or Flutter button is never an authorization control.

See [ADR-018](../decisions/ADR-018-dual-device-identity.md) for the dual identity and transfer policy.
