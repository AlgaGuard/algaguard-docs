# Role permissions

The table is a Phase 1 baseline. Exact endpoint policy is a Phase 2 contract.

| Action | OWNER | ADMIN | OPERATOR | VIEWER |
|---|:---:|:---:|:---:|:---:|
| View device and telemetry | Yes | Yes | Yes | Yes |
| Acknowledge alerts | Yes | Yes | Yes | No |
| Send permitted operational commands | Yes | Yes | Yes | No |
| Create/edit profile versions | Yes | Yes | Conditional | No |
| Assign active profile | Yes | Yes | Conditional | No |
| Invite/revoke members | Yes | Yes | No | No |
| Change roles | Yes | Limited; cannot displace owner | No | No |
| Schedule OTA | Yes | Conditional | No | No |
| Transfer ownership | Yes | No | No | No |
| Delete/archive device | Yes | Conditional | No | No |

`TBD`: Decide the `Conditional` permissions and destructive-action confirmation rules. The Access Service and downstream service enforce every permission server-side and audit high-value changes.
