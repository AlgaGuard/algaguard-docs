# OLED menu design

The 128x64 display refreshes every second. The UI must distinguish mock data, connection state, storage state, battery estimate, timestamp quality, and active profile version without implying real sensors are installed.

```text
AlgaGuard
|-- Dashboard
|-- Parameters
|   |-- Temperature
|   |-- pH
|   |-- Light
|   |-- Nitrate
|   |-- Phosphate
|   `-- Potassium
|-- Active Algae Profile
|-- Indicator LEDs
|-- Storage
|-- Time & RTC
|-- Network
|-- Device
|-- OTA
|-- Diagnostics
`-- Settings
```

## Buttons

| Input | Action |
|---|---|
| Up | Previous item/value |
| Down | Next item/value |
| Select | Open or confirm |
| Back | Return |
| Long Back | Home |
| Long Select | Configurable shortcut |
| Wi-Fi reset combination | `TBD`; must resist accidental activation |

## LED defaults

- Green: normal or online.
- Blue: provisioning, synchronization, or OTA activity.
- Red: warning or fault.

Meanings are configurable, outputs are digital, and `CONFIRMED` PWM is not required. The final priority when states overlap is `TBD`.
