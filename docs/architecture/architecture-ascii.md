# High-level ASCII architecture

```text
+-------------------- USERS AND CLIENTS --------------------+
| React Web Dashboard       Flutter Mobile App              |
|            HTTPS                 HTTPS / BLE              |
+----------------------+----------------+--------------------+
                       |                |
                       v                v
+----------------------- PHYSICAL DEVICE --------------------+
| ESP32-S3 N16R8                                           |
| OLED | RTC | Buttons | MicroSD | 3 LEDs | 3S Power        |
| Mock data -> 1 s samples -> 10 s batches -> MQTT/TLS      |
| Cached algae thresholds -> offline local alerts           |
+------------------------------+----------------------------+
                               |
                               v
+---------------- CLOUD-AGNOSTIC PLATFORM -------------------+
| NGINX HTTP/HTTPS             EMQX MQTT/TLS                |
| Keycloak OIDC                MQTT Ingestion               |
| API | Device | Access | Profile | Telemetry | Commands    |
| Alert | Notification | OTA | Audit                       |
| PostgreSQL | TimescaleDB | Redis | S3 API: S3/MinIO       |
| OpenTelemetry -> Prometheus/Loki/Tempo -> Grafana         |
| Later only: EMQX -> Kafka bridge -> Kafka -> consumers    |
+------------------------------+----------------------------+
                               |
                 Same images, protocols and data formats
          +--------------------+---------------------+
          |                    |                     |
          v                    v                     v
   Developer Computer     AWS EC2 temporary     Campus final
   Docker Compose         approximately 6 mo    Compose/k3s/K8s
```

The editable version is in [system overview](system-overview.md).
