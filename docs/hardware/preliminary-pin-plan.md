# Preliminary GPIO plan

Status: proposed prototype allocation. `CONFIRMED` product requirements are represented, but the map must be validated on the physical board before assembly.

| Function | Signal | Proposed GPIO | Notes |
|---|---|---:|---|
| Shared I2C bus | SDA | GPIO8 | OLED + PCF8563 RTC |
| Shared I2C bus | SCL | GPIO9 | OLED + PCF8563 RTC |
| MicroSD SPI | CS | GPIO10 | Dedicated chip select |
| MicroSD SPI | MOSI | GPIO11 | SPI |
| MicroSD SPI | SCK | GPIO12 | SPI |
| MicroSD SPI | MISO | GPIO13 | SPI; verify module output voltage |
| Button | Up | GPIO4 | Input, pull-up, active-low |
| Button | Down | GPIO5 | Input, pull-up, active-low |
| Button | Select | GPIO6 | Input, pull-up, active-low |
| Button | Back | GPIO7 | Input, pull-up, active-low |
| Indicator LED | Red | GPIO14 | Digital output; individual resistor; optional reviewed driver |
| Indicator LED | Green | GPIO15 | Digital output; individual resistor; optional reviewed driver |
| Indicator LED | Blue | GPIO16 | Digital output; individual resistor; optional reviewed driver |
| Battery monitoring | Pack-voltage ADC | GPIO1 | Protected divider and filtering, values `TBD` |
| Power/charger status | Optional input | GPIO2 | Use only if charger circuitry exposes an electrically compatible signal |
| RTC | Optional INT | GPIO17 | Leave disconnected unless needed |
| Onboard RGB | Data | GPIO38 | Board status LED; reserved from other use |

## Reserved pins

| Pins | Reason |
|---|---|
| GPIO0 | Boot |
| GPIO3 | Strapping pin |
| GPIO19, GPIO20 | `CONFIRMED` native USB requirement |
| GPIO35, GPIO36, GPIO37 | Internal octal memory use; not allocated |
| GPIO38 | Onboard RGB LED |
| GPIO43, GPIO44 | `CONFIRMED` UART0 fallback requirement |
| GPIO45, GPIO46 | Strapping pins |

## Validation gates

- Run an I2C scan; confirm the OLED and RTC addresses do not conflict.
- Confirm buttons to GND with pull-ups do not affect boot behavior.
- Calculate each LED resistor from measured rail voltage, LED forward voltage, and selected low operating current. `CONFIRMED`: PWM is not required; a MOSFET is not mandatory.
- `SAFETY`: Size and protect the ADC divider for the maximum possible 3S voltage plus margin. Review GPIO input limits and fault cases.
- Verify native USB, UART0, onboard RGB, and flash/PSRAM behavior on the exact physical board.
