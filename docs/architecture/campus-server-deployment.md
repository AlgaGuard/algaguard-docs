# Campus server deployment

Status: `CONFIRMED` final hosting direction; infrastructure facts remain `TBD` until campus administrators respond.

## Required confirmations

| Area          | Required information                                       | Status |
| ------------- | ---------------------------------------------------------- | ------ |
| Host          | Linux distribution, CPU, RAM                               | `TBD`  |
| Storage       | Disk capacity, RAID, filesystem, database volume           | `TBD`  |
| Network       | Bandwidth, static addressing, public reachability          | `TBD`  |
| Security      | Firewall ownership, inbound HTTPS/MQTT rules, admin access | `TBD`  |
| Names and TLS | DNS delegation, certificate automation                     | `TBD`  |
| Email         | Campus SMTP availability and policy                        | `TBD`  |
| Recovery      | Backup destination, retention, restore owner               | `TBD`  |
| Power         | UPS capacity and shutdown integration                      | `TBD`  |

## Deployment options

1. Continue Docker Compose on one server when that satisfies availability and operational needs.
2. Use single-node k3s for orchestration learning or stronger lifecycle controls.
3. Join an existing campus Kubernetes cluster when administrators provide it.

All options run the same GHCR images and portable protocols. NGINX Gateway Fabric, Helm, and Argo CD apply only to the Kubernetes target. Campus object storage uses MinIO or a confirmed campus S3-compatible service; email uses SMTP unless another adapter is approved.

**CONFIRMED Phase 2.1:** The campus target runs the same planned Realtime Service container and native RFC 6455 WSS contract as AWS EC2. NGINX routes WSS and the campus Redis deployment supplies non-durable live fan-out. No AWS-specific WebSocket service is required. DNS, TLS termination details, heartbeat, idle timeout, connection capacity, subscription limits, message size, and rate limits remain **TBD** pending campus facts and load evidence.

The execution sequence is in the [campus migration plan](../project-management/campus-migration-plan.md). See [provider adapters](../backend/provider-adapters.md), the [Realtime Service](../backend/realtime-service.md), [ADR-015: campus final host](../decisions/ADR-015-campus-final-host.md), [ADR-016: S3-compatible storage](../decisions/ADR-016-s3-compatible-storage.md), and [ADR-017: portable WebSocket service](../decisions/ADR-017-websocket-realtime-service.md) for the authoritative portability decisions.
