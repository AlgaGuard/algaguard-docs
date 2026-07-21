# FreeRTOS task design

| Task | Main inputs/outputs | Timing intent |
|---|---|---|
| Button input | GPIO events to UI queue | Responsive; debounced |
| UI | State/events to OLED | `CONFIRMED` refresh every 1 second; UI owns display |
| Time synchronization | RTC, Wi-Fi event, NTP result to time state | On boot/connect and periodic correction |
| Mock data | Clock tick to sample queue | `CONFIRMED` every 1 second |
| Storage | Sample queue to append-only SD records | Drain promptly; isolate SD latency |
| Batch builder | Samples to pending batches | Close normal batch about every 10 seconds |
| MQTT | Pending batches/commands to EMQX and ack queue | Non-blocking reconnect and QoS 1 |
| Command/configuration | MQTT messages to validated state updates | Apply versioned, idempotent changes |
| OTA | Validated release request to download/verify/apply | Low priority unless active rollout |
| Diagnostics | Health state to UI/status reports | Periodic and event-driven |

## Concurrency rules

- GPIO ISRs stay minimal and only enqueue or signal. An ISR never draws to the OLED.
- The UI task owns the display; the time manager owns RTC/NTP state.
- Network loss does not stop UI, timestamps from a valid RTC, sample generation, or SD storage.
- RTC loss does not stop sampling; records become `UNSYNCED`.
- SD failure does not stop the UI and should raise local/cloud health when possible.
- Use bounded queues and explicit overflow behavior. Use event groups for connection/readiness state and mutexes only around unavoidable shared drivers/resources.
- Minimize global mutable state and define task watchdog and stack monitoring during Phase 3.
