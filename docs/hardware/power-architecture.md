# Power architecture

Status: conceptual only. It is not a construction-ready schematic or approved wiring plan.

`SAFETY`: The platform-first prototype is USB-powered only. None of the 3S pack, charger, charge-while-operating path, battery percentage, divider, or ADC concepts below are implemented or approved. Keep this entire design behind the existing electrical review gate.

```mermaid
flowchart LR
    C1["Samsung INR18650-25P Cell 1"]
    C2["Samsung INR18650-25P Cell 2"]
    C3["Samsung INR18650-25P Cell 3"]
    BMS["3S BMS Candidate"]
    PACK["3S Pack Rail"]
    USBIN["Type-C Input"]
    CHARGER["Type-C 3S Charger Candidate"]
    CHARGE_TOPO["Charger-to-Pack / Load Connection - TBD"]
    ADAPTER["12 V 2 A External Adapter"]
    FUSE["Input Fuse - TBD"]
    REVERSE["Reverse-Polarity Protection - TBD"]
    PATH["Power Path / Load Sharing - TBD REVIEW REQUIRED"]
    BUCK["Regulated 5 V Buck - TBD"]
    LOGIC["3.3 V Logic via DevKit Regulator or Reviewed Path"]
    ESP["ESP32-S3 DevKitC-1"]
    I2C["OLED and RTC"]
    SD["MicroSD Module"]
    LEDS["Three Indicator LEDs"]
    DIVIDER["Protected Battery Divider and Filter - TBD"]
    ADC["GPIO1 ADC"]
    STATUS["Optional Charger / External-Power Status - if compatible"]

    C1 --- C2 --- C3
    C1 --> BMS
    C2 --> BMS
    C3 --> BMS
    BMS --> PACK
    USBIN --> CHARGER
    CHARGER -.-> CHARGE_TOPO
    CHARGE_TOPO -.-> BMS
    CHARGE_TOPO -.-> PATH
    ADAPTER --> FUSE --> REVERSE --> PATH
    PACK --> PATH
    PATH --> BUCK --> LOGIC --> ESP
    LOGIC --> I2C
    BUCK --> SD
    ESP --> LEDS
    PACK --> DIVIDER --> ADC --> ESP
    CHARGER -.-> STATUS -.-> ESP
```

The dashed charger connections are intentionally unresolved. The diagram shows functional blocks, not exact wiring. The charger input and external 12 V adapter paths require reconciliation in a reviewed topology; the diagram must not be read as permission to connect the 12 V adapter across the pack.

## Confirmed requirements and candidates

- `CONFIRMED`: Three 18650 cells are arranged 3S; battery percentage is required; current monitoring is not required; the device must operate while charging.
- `VENDOR-LISTED`: Cells are nominal 3.6 V, 2500 mAh, and unprotected; the candidate BMS is sold as 3S 20 A; the charger advertises a 12.6 V charging output; the adapter advertises 12 V 2 A center-positive.
- BMS, charger, wires, fuses, holders, and connectors must be selected from reviewed electrical requirements, not headline current values.

## Safety blockers

- `SAFETY`: A BMS is protection, not automatically a complete charger or power-path controller.
- `SAFETY`: Charge-while-operating requires a reviewed load-sharing or source-selection design. This is `TBD`.
- `SAFETY`: Never connect the external adapter directly across the battery pack without a reviewed charging topology.
- `SAFETY`: Never connect 12 V directly to an ESP32 GPIO or 3.3 V pin. A reviewed regulated buck stage is required.
- `SAFETY`: The divider must tolerate the maximum possible 3S pack voltage and faults with margin while keeping GPIO1 within absolute limits.
- `SAFETY`: Review cell authenticity and matching, pack assembly method, fuse position/rating, wire gauge, connectors, thermal behavior, enclosure heat, and safe service access before construction.

## Battery percentage

Estimate state of charge from calibrated pack voltage using multiple voltage points, filtering, hysteresis, and load-aware settling. Voltage sag means the estimate is approximate; do not claim laboratory-grade state of charge. The curve and displayed bands are `TBD` after bench measurements.

## Electrical isolation decision

The user requires electrical isolation, but its boundary is `TBD`. Review the external 12 V input, future submerged sensor power/data, communication interfaces, and indicator outputs. Low-voltage local indicator LEDs normally require current limiting rather than galvanic isolation; do not add optocouplers everywhere without a hazard-based reason. Record the final boundary in a later ADR after electrical review.
