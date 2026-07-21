# Open questions

This is the authoritative unresolved list as of 2026-07-22.

## Hardware and safety

- `TBD`: Exact ESP32 metal-shield module marking and runtime flash/PSRAM verification.
- `TBD`: Exact OLED I2C address and MicroSD logic-level behavior under bench test.
- `TBD`: RTC backup battery installation/module compatibility and measured drift.
- `TBD`: LED operating currents/resistor values and final electrical-isolation boundary.
- `TBD`: Buck converter, fuse rating/placement, power-path/load-sharing circuit, and charge-while-operating approval.
- `TBD`: Battery holder versus welded pack, cell verification/matching, wire/connector/thermal decisions.
- `TBD`: Enclosure fit, cable glands, condensation controls, and future IP67/IP68 submerged-sensor connection system.

## Product and science

- `TBD`: Exact scientific profile thresholds approved by the supervisor. No values are assumed.
- `TBD`: Critical-threshold shape, allowed units/ranges, conditional role permissions, Wi-Fi reset combination, and overlapping LED-state priority.
- `TBD`: Domain name and notification provider without Firebase.

## AWS and campus

- `TBD`: AWS region, account/free-plan/credit eligibility, EC2 size/architecture, storage, email provider, and approved six-month cost.
- `TBD`: Campus Linux, CPU, RAM, disk, RAID, network, DNS, TLS, firewall, SMTP, backup, UPS, runtime choice, RTO, and RPO.
- `TBD`: Cutover downtime/final synchronization, rollback window, and conflict procedure.

## Data and operations

- `TBD`: Exact serialized and database telemetry record sizes and compression.
- `TBD`: Application-acknowledgement durability boundary, maximum MQTT message size, and replay limits.
- `TBD`: Retention/job schedules after measured cost and capacity.
- `TBD`: Device credential provisioning/rotation, OTA signing-key custody, and anti-rollback policy.
- `TBD`: Repository documentation license.

Confirmed answers are recorded in their authoritative documents rather than retained as questions: 3S, native USB, UART fallback, no PWM/current monitor, approximate battery percentage, operation while charging requirement, indoor/outside-tank use, three LEDs, one-second display/sample, ten-second batch, one demo device, scale targets, one-to-two-month retention target, and no hard sharing cap.
