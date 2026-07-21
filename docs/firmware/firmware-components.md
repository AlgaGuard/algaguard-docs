# Firmware component design

| Component | Planned responsibility | Failure behavior |
|---|---|---|
| Board configuration | Pin definitions, memory configuration, feature flags | Fail build or report diagnostic mismatch |
| App events | Typed internal state changes | Prevent uncontrolled shared state |
| OLED UI | Menu, readings, health, progress | UI failure must not corrupt storage |
| Button manager | Debounce, press/long-press events | Ignore invalid transitions |
| RTC/time manager | RTC integrity, NTP UTC synchronization, timestamp quality | Continue as `UNSYNCED` if no valid source |
| Mock-data generator | Deterministic/testable parameter samples | Must not present values as real sensors |
| Indicator controller | Configurable red/green/blue meanings; digital only | Safe default state |
| SD storage | Append records, ring retention, recovery | UI/data generation continue with fault alert |
| Offline batch queue | Sequence, batch, acknowledge, replay | Retain pending data across reconnect/reboot |
| Wi-Fi manager | Saved networks, reconnect, reset state | Local features continue offline |
| BLE provisioning | Transfer Wi-Fi setup from Flutter | Time-limited, authenticated design required |
| MQTT manager | TLS session, publish/subscribe, QoS 1 | Backoff and reconnect without blocking local work |
| Device identity | Credential/certificate access and identity metadata | Never log secrets |
| Profile/configuration cache | Active profile version and compact thresholds | Keep last valid version; report applied version |
| Local threshold engine | Evaluate cached user-defined limits | Record profile version with alert |
| OTA manager | Signed manifest, HTTPS download, validation, rollback | Stay on or return to known-good firmware |
| Power manager | Approximate battery percentage and supply status | Report uncertainty/faults |
| Diagnostics | Health fields, counters, last errors | Rate-limit logs and telemetry |

Host-testable batching, retention, profile validation, time-quality transitions, menu state, and threshold logic should be separate from hardware drivers.
