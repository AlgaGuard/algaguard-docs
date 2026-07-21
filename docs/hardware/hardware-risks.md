# Hardware risks

| Risk | Impact | Control | Status |
|---|---|---|---|
| Unknown ESP32 module marking | Wrong memory or pin assumptions | Photograph marking and verify at runtime | `TBD` |
| MicroSD level conversion differs from listing | ESP32 damage or unreliable SPI | Bench-measure all levels | `SAFETY`, `TBD` |
| RTC backup cell/module mismatch | Cell damage or lost time | Inspect module circuitry and battery type | `SAFETY`, `TBD` |
| Unreviewed charge-and-load topology | Fire, cell stress, resets | Electrical review and tested power path | `SAFETY`, `TBD` |
| Headline BMS rating used for wiring | Overheating or unsafe protection | Calculate actual currents, fuse, wire, thermal margin | `SAFETY`, `TBD` |
| ADC divider under-rated | GPIO damage | Design for maximum voltage and faults | `SAFETY`, `TBD` |
| Small enclosure selected without fit/thermal study | Pinched wires, heat, poor serviceability | Use scaled and physical fit studies | `TBD` |
| IP65 connector treated as submersible | Water ingress | Keep outside immersion; select IP67/IP68 future system | `SAFETY`, open |
| Moisture/condensation reaches electronics | Corrosion or shock/fire risk | Boundary, glands, drip loops, inspection, ingress test | Open |

Project-wide ownership and mitigation tracking is in [risks and mitigations](../project-management/risks-and-mitigations.md).
