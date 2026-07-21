# Risks and mitigations

| Area | Risk | Mitigation | Owner/status |
|---|---|---|---|
| Safety | Unreviewed 3S charge/load path causes cell stress, heat, or fire | No construction until qualified electrical review and bench safety plan | `SAFETY`, owner `TBD` |
| Water | Splash/condensation or unsuitable connector reaches electronics | Outside-tank boundary, glands, drip loops, strain relief, fit/ingress test | `SAFETY`, owner `TBD` |
| Hardware | Retailer/module claims differ from delivered parts | Photograph labels, use manufacturer docs, bench/runtime verify | Open |
| Time | RTC drift or invalid voltage-low state creates wrong timestamps | Quality flags, NTP correction, reject invalid RTC, measure drift | Planned |
| Storage | SD corruption or queue loss during power failure | Append/recovery tests, checksums, acknowledgement ledger, ring policy | Planned |
| Security | Weak provisioning, device secrets, ACLs, or authorization | Threat model, per-device identity, TLS, server-side permissions, audit | Planned |
| Profiles | User thresholds are mistaken for scientific recommendations | Label user-configured, no built-in thresholds, reviewed templates later | Planned |
| Cost | Credits unavailable or expire; public IPv4/EBS/transfer continue charging | Dated estimate, approval, budgets, tags, inventory, fallback/export | Open |
| Capacity | One-device pilot is presented as 1,000-device proof | Separate stages, measure record size, load/recovery test | Planned |
| Portability | AWS-specific types leak into domain code | S3/email interfaces, same images/protocols, migration rehearsal | Planned |
| Operations | Single-host failure loses service/data | Off-host backups, restore tests, UPS/campus facts, documented RTO/RPO `TBD` | Open |
| Scope | Phase 1 accidentally implements code/infrastructure | Repository rules and validation scan | Controlled |

Hardware-specific risks are in [hardware risks](../hardware/hardware-risks.md).
