# Enclosure design

`CONFIRMED`: The device operates indoors, with the main electronics mounted or clipped outside the tank. Splash and humidity exposure are expected; the electronics are not submersible.

| Candidate | Role | Decision |
|---|---|---|
| 200 x 120 x 55 mm | Preferred prototype candidate | `CONFIRMED` preference; retailer page and physical dimensions/fit must be rechecked |
| 135 x 70 x 50 mm | Compact alternative | `VENDOR-LISTED`; do not select without fit study |

## Fit study

Create scaled outlines and a physical mock-up for the ESP32, three cells, pack assembly, BMS, charger, buck converter, MicroSD module/card access, RTC and coin cell, OLED, four buttons, three LEDs/resistors, fuse, connectors, glands, strain relief, wiring bends, antenna clearance, heat paths, and service clearances.

Review OLED and button-panel seals, battery service access, native USB and UART access, card replacement, condensation versus ventilation, and the ability to preserve water resistance after service. The final mounting method and enclosure penetrations are `TBD`.

See [water-ingress plan](water-ingress-plan.md) and [power architecture](power-architecture.md).
