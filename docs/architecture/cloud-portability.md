# Cloud portability

Status: Phase 1 design; not deployed.

The application core must be cloud-agnostic and self-hostable. It moves through three environments without changing business logic, MQTT contracts, HTTP contracts, or firmware merely because the host changes.

| Stage | Runtime                             | Purpose                                                                                 |
| ----- | ----------------------------------- | --------------------------------------------------------------------------------------- |
| A     | Developer computer, Docker Compose  | Establish the portable end-to-end stack                                                 |
| B     | AWS EC2 Linux, Docker Compose first | `CONFIRMED` temporary host for approximately six months                                 |
| C     | Campus Linux server                 | `CONFIRMED` final host; Compose, k3s, or existing Kubernetes is `TBD`                   |
| D     | Optional campus orchestration       | Demonstrate Kubernetes, NGINX Gateway Fabric, Helm, and Argo CD after Compose is stable |

## Portability rules

- The same GitHub Container Registry images run in each hosted environment.
- Configuration uses environment variables and mounted files.
- Stable product DNS names hide the temporary EC2 hostname. The MQTT hostname and OTA base URL are configurable.
- Object storage uses the [provider interface](../backend/provider-adapters.md) with Amazon S3 and MinIO implementations.
- Email uses the same provider-boundary rules with Amazon SES and SMTP implementations.
- Domain logic must not expose AWS SDK types.
- PostgreSQL, TimescaleDB, Redis, EMQX, Keycloak, MQTT, HTTP, and native RFC 6455 WebSocket contracts remain portable.
- The same independently deployable Realtime Service image uses Redis Pub/Sub and WSS on AWS EC2 and the campus server.
- AWS API Gateway WebSocket APIs, AppSync, Firebase realtime services, and Socket.IO wire contracts are not mandatory core dependencies.
- Amazon Cognito, AWS IoT Core, Lambda, DynamoDB, SQS, EventBridge, API Gateway, EKS, MSK, and CloudWatch are not mandatory core dependencies.

See the [AWS host](aws-temporary-deployment.md), [campus host](campus-server-deployment.md), [migration architecture](migration-architecture.md), [Realtime Service](../backend/realtime-service.md), [ADR-014](../decisions/ADR-014-aws-temporary-host.md), [ADR-015](../decisions/ADR-015-campus-final-host.md), [ADR-016](../decisions/ADR-016-s3-compatible-storage.md), and [ADR-017](../decisions/ADR-017-websocket-realtime-service.md).
