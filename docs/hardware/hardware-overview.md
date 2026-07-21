# Hardware overview

Status: Phase 1 proposed prototype; physical validation remains required.

```mermaid
flowchart TB
    POWER["Reviewed 3S Power System - TBD"] --> ESP["ESP32-S3-DevKitC-1 v1.1"]
    BUTTONS["Four Active-Low Buttons"] --> ESP
    ESP <--> I2C["I2C GPIO8 / GPIO9"]
    I2C <--> OLED["1.3 inch OLED"]
    I2C <--> RTC["PCF8563 RTC"]
    ESP <--> SD["32 GB MicroSD Module over SPI"]
    ESP --> RED["Red LED + Resistor"]
    ESP --> GREEN["Green LED + Resistor"]
    ESP --> BLUE["Blue LED + Resistor"]
    PACK["Protected 3S Pack Voltage"] --> DIVIDER["Divider and Filter - TBD"] --> ADC["GPIO1 ADC"]
    ADC --> ESP
    USB["Native USB GPIO19/20 Reserved"] -.-> ESP
    UART["UART0 GPIO43/44 Reserved"] -.-> ESP
```

`CONFIRMED`: The controller family is ESP32-S3-DevKitC-1, the supplied pinout represents v1.1, and the retailer listing says N16R8. The exact metal-shield marking and runtime flash/PSRAM values remain `TBD`.

The authoritative component facts are in [hardware inventory](hardware-inventory.md), pin allocation in [preliminary pin plan](preliminary-pin-plan.md), and safety boundary in [power architecture](power-architecture.md).
