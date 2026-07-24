# Shared development environment

## Status

**CONFIRMED:** The portable local development stack is available in
`algaguard-infrastructure` on its shared-development-environment branch. It
uses an isolated Compose project, ignored locally generated secrets and PKI,
HTTPS/WSS at `https://localhost:8443`, and MQTT TLS at `localhost:8883`.

**CONFIRMED:** The local stack starts NGINX, Keycloak, EMQX, TimescaleDB,
Redis, MinIO, API Gateway, Access, Device, Profile, MQTT Ingestion,
Telemetry, Command, OTA, Realtime, and the web dashboard. Startup waits on
health checks and service migrations are idempotent.

**TBD:** The full demo seed and authenticated end-to-end smoke suite require a
tank resource and an approved development-user mapping in the application
data model. They must not be simulated by manually inventing production-like
records. Physical ESP32 acceptance is also still open.

## Local use

From `algaguard-infrastructure`:

```sh
make demo-env-check
make demo-env-init       # creates ignored .env once; it never overwrites it
make pki-gateway-cert    # generates only ignored local development PKI
make demo-up
make demo-migrate
make demo-health
make demo-smoke
```

`demo-smoke` validates the gateway certificate against the generated local CA;
it does not disable certificate or hostname verification. `demo-stop` stops
the stack while preserving volumes. `demo-reset-destructive` is deliberately
named because it removes only the isolated development stack's volumes and is
intended only when a clean local database is required.

## Branded clients

**CONFIRMED:** Web, Android and firmware derivatives are generated from the
approved source image at `brand/algaguard-logo.png`; they are not hand-redrawn
or recoloured. Android debug builds trust a local CA only when a user installs
that CA. They do not use a trust-all TLS override. For a physical phone, the
gateway certificate must include the laptop's reachable hostname or LAN IP,
and the device must use HTTPS/WSS URLs for that host.

The firmware contains a deterministic 128×64 one-bit SSD1306 boot bitmap.
Its visual appearance on the physical OLED remains **TBD** until the connected
ESP32-S3 is flashed and observed.

## Shared AWS development deployment

**TBD:** No AWS resource has been created. Before any paid resource is
created, collect and approve the AWS account, region, monthly ceiling, domain
and DNS provider, intended app/API and MQTT hosts, access method, backup
destination, public-access decision, expected duration, and stop schedule.

The recommended temporary plan is one Ubuntu LTS EC2 host with encrypted EBS,
SSM access, Docker Compose, GitHub OIDC, and an explicitly scoped security
group. Public ports are limited to HTTPS 443, MQTT TLS 8883 only when needed,
and HTTP 80 only during ACME issuance. PostgreSQL, Redis, MinIO, Keycloak
admin, EMQX admin, Docker, and service ports remain private.

Use public DNS and a publicly trusted certificate for shared HTTPS/WSS and
MQTT. The local CA is local-only. GHCR deployments use immutable commit-SHA
or digest manifests, retain the previous manifest for rollback, run
migrations, wait for health, and execute smoke checks. Costs are estimates,
not guarantees.

The manual GitHub `development` workflow is designed for GitHub OIDC and AWS
SSM. It requires non-secret environment variables `AWS_REGION`,
`AWS_DEVELOPMENT_DEPLOY_ROLE_ARN`, and `EC2_INSTANCE_ID`, plus a provisioned
host-side deployment script. It does not auto-deploy commits or target
production.

## Stop and teardown

For a temporary AWS demo, retain non-secret image manifests and deployment
configuration, back up PostgreSQL/TimescaleDB, Keycloak's database, and MinIO
objects, then test rollback to the previous image manifest. Stop the EC2 host
when not testing to reduce cost. Teardown is an approved, separate action:
verify backups first, then remove the stack, volumes, DNS records, TLS assets,
and approved AWS resources. This is not a production disaster-recovery claim.
