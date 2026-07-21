# Water-ingress plan

```mermaid
flowchart TB
    TANK["Tank Water - Wet Zone"]
    FUTURE["Future Submerged Probes and IP67/IP68 Cable System - TBD"]
    DRIP["Drip Loop and Strain Relief"]
    GLAND["Sealed Cable Gland - TBD"]
    ENC["Splash-Resistant Electronics Enclosure - Outside Tank"]
    PANEL["Sealed OLED / Button / LED Panel"]
    USB["Protected USB Service Access"]
    M12["IP65 M12 Candidate - Not Submersible"]

    TANK --> FUTURE --> DRIP --> GLAND --> ENC
    M12 -.-> DRIP
    PANEL --> ENC
    USB --> ENC
```

## Boundary rules

- `CONFIRMED`: Indoor use, outside-tank mounting, and water resistance near the tank.
- Do not claim that the enclosure or the vendor-listed IP65 M12 connector is submersible.
- Use downward-facing entries where practical, drip loops, strain relief, sealed glands, and separation between likely ingress paths and energized electronics.
- Future submerged sensors need appropriately rated waterproof probes, cable assemblies, glands, and IP67/IP68 connectors selected for the exact immersion conditions.
- Condensation control, vents, seals, torque, enclosure material, and post-assembly ingress test are `TBD`.
- `SAFETY`: Keep battery, charging, and 12 V interfaces outside wet zones and have the completed assembly reviewed before use near a tank.
