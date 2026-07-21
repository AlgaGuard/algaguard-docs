# Campus migration plan

## Entry requirements

- Campus administrators confirm Linux, CPU, RAM, disk, RAID, network, firewall, DNS, TLS, SMTP, backup, and UPS details. All are `TBD`.
- The AWS stack has current checked backups and a documented image/config inventory.
- Stable MQTT and HTTPS DNS names are already used by clients/devices.
- A change owner, maintenance window, rollback window, and acceptance sign-off are assigned.

## Migration sequence

1. Prepare the campus runtime, storage, firewall rules, time sync, TLS, DNS records with low TTL, backup target, monitoring, and UPS shutdown behavior.
2. Pull the exact approved GHCR image digests used on AWS.
3. Apply campus environment configuration and replace S3/SES adapters with MinIO/campus storage and SMTP where approved.
4. Take consistent PostgreSQL and TimescaleDB backups; include roles/extensions and verify checksums.
5. Back up Keycloak database and required realm/provider configuration through a supported, tested method.
6. Copy S3/MinIO objects with manifests and checksums; compare object count, size, and hashes.
7. Export/import EMQX configuration, ACLs, authentication data, and certificates with secure key handling.
8. Restore into an isolated campus environment and validate migrations, row counts, time ranges, profiles, users, permissions, objects, and broker ACLs.
9. Run parallel smoke/load validation for HTTPS, OIDC, MQTT/TLS, ingestion/acknowledgement, offline replay, profile delivery, alerts, storage, and OTA download.
10. Quiesce writes or use an approved final synchronization strategy; exact downtime/replication approach is `TBD`.
11. Capture final backups/checksums, restore the final delta, and update stable DNS to campus.
12. Monitor device reconnects, error rates, latency, data gaps, certificate validation, and user login.
13. Keep AWS read-only or ready for rollback during the agreed short period.
14. After acceptance and a final retained backup, revoke temporary credentials and decommission AWS resources through an approved inventory.

## Rollback

If acceptance thresholds fail, stop campus writes safely, restore DNS to AWS, reconcile any campus-only data through the documented method, and preserve evidence. Rollback thresholds, window, and data-conflict procedure are `TBD` before migration.

No ESP32 reflashing is required solely because hosting moves; endpoints are configurable and stable DNS changes target infrastructure. See [migration architecture](../architecture/migration-architecture.md).
