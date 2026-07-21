# Scale target

## Separate operating stages

| Stage | Confirmed scope | Capacity claim |
|---|---|---|
| AWS pilot | One physical device, approximately six months, Docker Compose first | Demonstrates an end-to-end product slice only |
| Campus deployment | Portable containers on a campus Linux server | Capacity is `TBD` until server facts and load tests exist |
| Full target | Up to 1,000 devices, 10,000 users, 500+ organizations | Architecture target, not a promise for free AWS capacity or one small server |

`CONFIRMED`: There is no artificial product-level cap on accounts invited to a device. This does not mean unlimited infrastructure capacity. Membership endpoints require pagination, rate limits, invitation throttling, maximum request sizes, abuse monitoring, indexed lookups, and unique membership constraints.

## Full-target traffic shape

- 1,000 devices at one local sample per second produce 86.4 million samples per day.
- Ten-second uploads produce up to 8.64 million batch messages per day across 1,000 continuously connected devices.
- Connection, reconnect, acknowledgement, configuration, OTA, authentication, query, and alert loads add to telemetry traffic.
- Raw retention, aggregates, indexes, WAL, backups, and replicas add storage beyond payload estimates.

The direct MQTT pilot path should be load-tested before adding Kafka. Full scale likely needs larger or multiple database, broker, application, and observability nodes. It also needs failure testing and measured capacity margins. See the [capacity plan](../project-management/capacity-plan.md).
