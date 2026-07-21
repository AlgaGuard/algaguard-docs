# Wiring and bench-validation checklist

- [ ] Photograph and transcribe the ESP32 module shield marking.
- [ ] Confirm board revision, flash, PSRAM, native USB, UART0, and onboard RGB behavior.
- [ ] Run an I2C scan and confirm OLED and PCF8563 addresses.
- [ ] Measure combined I2C pull-ups and verify 3.3 V logic and rise time.
- [ ] Verify MicroSD supply and every SPI logic level before connection to the ESP32.
- [ ] Confirm button polarity and boot behavior.
- [ ] Measure LED forward voltage, choose operating current, and calculate one resistor per LED.
- [ ] Keep GPIO35, GPIO36, and GPIO37 unconnected.
- [ ] Review maximum 3S voltage, divider fault cases, filtering, calibration, and ADC limits.
- [ ] Review cell identity/matching and holder versus spot-welded pack.
- [ ] Approve BMS, charger, buck, load sharing, reverse protection, fuse, wire, and connectors.
- [ ] Confirm the charger and BMS topology supports operation while charging.
- [ ] Define the electrical-isolation boundary from a hazard analysis.
- [ ] Complete the enclosure fit, thermal, strain-relief, drip-loop, gland, and ingress checks.
- [ ] Record reviewer, instruments, conditions, measured results, and ISO dates.

`SAFETY`: Do not energize the assembled 3S system until the power items above receive an electrical review.
