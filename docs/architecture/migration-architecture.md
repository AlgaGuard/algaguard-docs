# AWS-to-campus migration architecture

Migration changes infrastructure configuration and stable DNS targets. It does not change AlgaGuard business logic or the ESP32 MQTT/HTTPS protocols. No ESP32 reflashing is required solely because hosting moves.

```mermaid
flowchart LR
    subgraph AWS["Temporary AWS"]
        AWS_RUNTIME["EC2 Docker Compose or k3s"]
        AWS_APPS["AlgaGuard Containers"]
        AWS_DB[("PostgreSQL and TimescaleDB")]
        AWS_AUTH[("Keycloak Data")]
        AWS_OBJ[("S3 or MinIO Objects")]
        AWS_MQTT["EMQX Configuration"]
    end

    PACKAGE["Checksummed Backups, Objects and Configuration"]
    DNS["Stable Product DNS"]

    subgraph CAMPUS["Final Campus"]
        CAMPUS_RUNTIME["Compose, k3s or Kubernetes"]
        CAMPUS_APPS["Same Container Images"]
        CAMPUS_DB[("Restored Databases")]
        CAMPUS_AUTH[("Restored Keycloak")]
        CAMPUS_OBJ[("Campus MinIO or Storage")]
        CAMPUS_MQTT["Restored EMQX"]
    end

    AWS_APPS --> PACKAGE
    AWS_DB --> PACKAGE
    AWS_AUTH --> PACKAGE
    AWS_OBJ --> PACKAGE
    AWS_MQTT --> PACKAGE
    PACKAGE --> CAMPUS_APPS
    PACKAGE --> CAMPUS_DB
    PACKAGE --> CAMPUS_AUTH
    PACKAGE --> CAMPUS_OBJ
    PACKAGE --> CAMPUS_MQTT
    DNS --> AWS_RUNTIME
    DNS -.->|"Cutover"| CAMPUS_RUNTIME
```

```text
TEMPORARY AWS                                      FINAL CAMPUS
+-------------------------+                       +-------------------------+
| EC2 Linux               |                       | Campus Linux / K8s      |
| Docker Compose or k3s   |                       | Compose, k3s or K8s     |
| EMQX                    |      backups          | EMQX                    |
| Keycloak                |  ----------------->   | Keycloak                |
| Express services        |  images and config    | Same Express services   |
| PostgreSQL/TimescaleDB  |  objects and certs    | PostgreSQL/TimescaleDB  |
| Redis                   |                       | Redis                   |
| S3 or MinIO             |                       | MinIO/campus storage     |
+------------+------------+                       +------------+------------+
             |                                                 ^
             +------------- stable DNS cutover -----------------+
```

See [migration tasks and rollback](../project-management/campus-migration-plan.md), [provider adapters](../backend/provider-adapters.md), [ADR-014: AWS temporary host](../decisions/ADR-014-aws-temporary-host.md), [ADR-015: campus final host](../decisions/ADR-015-campus-final-host.md), and [ADR-016: S3-compatible storage](../decisions/ADR-016-s3-compatible-storage.md).
