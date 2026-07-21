# Device sharing

```mermaid
flowchart TD
    OWNER["Owner"] --> INVITE["Create One-Time Expiring Invitation"]
    INVITE --> EMAIL["Notification Provider"]
    EMAIL --> USER["Invitee Authenticates with Keycloak"]
    USER --> ACCEPT["Accept Invitation"]
    ACCEPT --> MEMBER["Organization or Device Membership"]
    MEMBER --> CHECK["Backend Authorization on Every Action"]
    OWNER --> CHANGE["Change Role / Revoke / Transfer Ownership"]
    CHANGE --> AUDIT["Audit Event"]
    INVITE --> AUDIT
    ACCEPT --> AUDIT
```

`CONFIRMED`: Many accounts may be invited to a device, with no hard product-level cap. This is not unlimited capacity. List operations are paginated, invitations throttled, request sizes bounded, uniqueness enforced, and abuse monitored. Bulk member import is deferred.
