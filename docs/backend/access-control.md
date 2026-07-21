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

Permission details are authoritative in [role permissions](../product/role-permissions.md). Hiding a React or Flutter button is never an authorization control.
