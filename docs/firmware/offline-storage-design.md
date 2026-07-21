# Offline storage and batch design

`CONFIRMED`: Generate and persist one sample per second, build a normal batch of about ten samples every ten seconds, and retain at least two days locally using the available 32 GB MicroSD card. A configurable ring policy controls retention; card capacity alone is not the policy.

```mermaid
flowchart LR
    SAMPLE["Generate 1 s Mock Sample"] --> TIME["Obtain UTC / RTC / Relative Time"]
    TIME --> OLED["Update OLED"]
    TIME --> SD["Append Durable SD Record"]
    SD --> BATCH["Add to 10 s Batch"]
    BATCH --> PUB["Publish MQTT QoS 1"]
    PUB --> ACK["Receive Application Acknowledgement"]
    ACK --> MARK["Mark Batch Acknowledged"]
    PUB -->|"Offline or failed"| PENDING["Keep Pending on SD"]
    PENDING -->|"Reconnect"| PUB
```

## Planned batch envelope

| Field | Purpose |
|---|---|
| `deviceId` | Device identity |
| `batchId` | Unique batch identity |
| `firstSequence` / `lastSequence` | Ordering and gap detection |
| `sampleCount` | Declared count, normally about ten |
| `samples` | One-second sample records |
| `createdAt` | Batch time when valid |
| `schemaVersion` | Contract evolution |

`deviceId + sequence` is the sample idempotency key. MQTT QoS 1 delivery is insufficient to declare ingestion complete; delete/reclaim pending data only after an application acknowledgement identifies the accepted batch or sample range.

## Recovery and retention

- Append records before considering them eligible for publish.
- Recover incomplete tails safely after reset or power loss.
- Keep acknowledged and pending state crash-consistent.
- Replay oldest pending batches with rate limiting after reconnect while continuing current samples.
- Protect unacknowledged data from normal ring deletion until the policy's emergency limit is reached; report any forced loss explicitly.
- Verify filesystem, format, record encoding, checksum/CRC, wear strategy, card health, and exact record size during implementation.

Cloud retention differs from local retention and is defined in the [capacity plan](../project-management/capacity-plan.md).
