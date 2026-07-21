# Mock-data design

The first physical prototype generates data; it does not contain physical cultivation sensors. Every UI, storage, API, and export view must identify the source as mock/simulated.

## Parameters

- Temperature
- pH
- Light
- Nitrate
- Phosphate
- Potassium

No scientific threshold or claimed realistic range is defined in Phase 1. Units and warning/critical values come from the assigned user-defined profile and must pass contract validation.

## Generator requirements

- Produce one sample per second from a monotonic schedule.
- Include device ID, sequence, UTC/relative time, timestamp quality, source=`MOCK`, six values, unit references, and active profile version.
- Support a seeded deterministic mode for tests and a bounded demonstration mode configured through validated inputs.
- Never silently change historic records when the generator configuration or profile changes.
- Keep pure generation logic host-testable and independent of OLED, SD, MQTT, and hardware drivers.
