![N|Solid](https://arribada.org/wp-content/uploads/2022/01/arribada_web_logo_g.svg)


Collaboration with 


![N|Solid](https://www.cls-telemetry.com/wp-content/uploads/2021/06/logo-cls.png)

# Altium project for Linkit-v4 SMD (v4.2)

Linkit V4 is the fourth generation of the Linkit tracker CORE board, developed in collaboration with CLS (Collecte Localisation Satellites). This revision transitions from the discontinued ARTIC R3 transceiver to next-generation satellite communication modules, ensuring long-term availability for Argos/Kineis satellite networks.

## Hardware Variant

This repository contains the **linkit-v4-smd** board only, current revision **v4.2**.

It is based on the **Argos-SMD module**, an open-source alternative:
- Compatible with Arribada's Argos-SMD hardware: [argos-smd-hw](https://github.com/arribada/argos-smd-hw)
- Provides flexibility for custom satellite communication implementations
- Additional power management circuitry for SMD module

### Other variants
- **linkit-v4-kim (v4.0)**, based on the KIM2 module from CLS/Kineis, now lives in its own repository: [arribada/linkit-v4-kim-hw](https://github.com/arribada/linkit-v4-kim-hw).
- The former dual-footprint board (KIM2 + SMD on the same layout) is deprecated. It is kept for reference under the git tag `archive/master-dual`.

## Revisions

| Version | Git tag | PCB | Outputs |
|---------|---------|-----|---------|
| **v4.2** (current) | `v4.2` | `pcb/linkit-v4-1.1_6layers_smdonly.PcbDoc` | `Project Outputs for linkit-v4-hw/smd-variant/linkit-v4-smd-v1.1/` (production variants B to E) |
| SMD v01A (first SMD batch) | `v0.1A-smd` | `pcb/linkit-v4-0.3_6layers_smdonly.PcbDoc` | `Project Outputs for linkit-v4-hw/smd-variant/linkit-v4-smd-v01A/` |

## Opening the project

- Open `linkit-v4-hw.PrjPcb` with Altium Designer.
- Some device sheets are referenced from [arribada/hardware-lib](https://github.com/arribada/hardware-lib) through the relative path `..\hardware-lib\`. Clone `hardware-lib` next to this repository. A copy of these sheets is also kept in `DeviceSheets/`.

## Features

### Core Specifications
- **MCU**: ISP1807 (nRF52840-based module)
- **GNSS**: u-blox SAM-M10Q with integrated ceramic antenna
- **Flash Memory**: IS25LP128F (128 Mbit NOR Flash)
- **IMU**: BMA400 ultra-low power accelerometer
- **ADC**: ADS1115 16-bit, 4-channel (for external sensors)

### Power Management
- **Battery**: Single-cell Li-Ion/LiPo (3.7V, 4.2V max)
- **Charging Options**:
  - USB Type-C (BQ25176M linear charger, 800mA)
  - Qi Wireless charging (BQ51003, 2.5W)
  - Solar panel input (3-18V via BQ25176M MPPT-compatible input)
- **Voltage Regulation**: 
  - TPS63901 Buck/Boost (configurable 1.8V/3.3V for main system)
  - TPS612997 Buck/Boost (3.6V for the Argos-SMD satellite module)
  - TPS22960 dual load switches for power domain control
- **Ultra-low power design**: Sub-microamp quiescent current in sleep modes

### Connectivity & Interfaces
- **Satellite Communication**: Argos-SMD module
- **GNSS**: Integrated u-blox M10Q receiver
- **External I/O**:
  - 8-pin expansion connector (1.25mm pitch)
  - External I2C bus for sensors
  - Salt-water switch (SWS) interface
  - Battery voltage monitoring
  - Pressure sensor support via ADC channels
- **Programming/Debug**: 
  - Dual JTAG connectors (ISP1807 + Satellite module)
  - USB-C for firmware updates and charging

### Physical
- Compact form factor optimized for marine deployments
- Reed switch for magnetic activation
- Tri-color LED status indicator
- U.FL/SMA antenna connector options

## Configuration Options
<img width="4420" height="1760" alt="image" src="https://github.com/user-attachments/assets/f834302e-f8f5-4927-9997-71324892396d" />
No jumper directly on board, represented by component Resistors or Diode.

### Power Source Selection
The board supports multiple power configurations via resistor options:

1. **Standard Operation** (Default):
   - Battery + USB/Wireless charging + optional solar input
   - All power sources can coexist safely

2. **USB-Only Power** (No Battery):
   - Configuration: R53=DNP, R52=Fitted, R55=Fitted
   - ⚠️ **Warning**: Do NOT connect battery in this mode
   - ⚠️ **Warning**: Power LED D2 incompatible with 5V - cut trace O9 (Not critical)

### Voltage Selection
- **PSEL Pin (TPS63901)**: Switches between 1.8V and 3.3V system voltage
  - Low = 2.3V (minimum voltage for IS25LP Flash and ADC)
  - High = 3.3V (default for most operations)

### Reed Switch
- Magnetic activation via P-channel MOSFET (Q4)
- Can be controlled via MCU or magnetically triggered
- 3.3V logic level compatible

## Operating Modes

### Charging Modes
The tri-color LED (D2) indicates charging status:
- **OFF**: Battery only, no charging
- **Yellow**: Charging via USB
- **White/Cyan**: Charging via wireless coil
- **Green**: Fully charged
- **Blinking Green**: Fault condition (thermal, timer, over-voltage)


## Firmware (still in development)
Firmware is not released as open source at the moment.
- Firmware based on Linkit-v3 Baremetal : https://github.com/arribada/linkit-v4-core
- Zephyr firmware : https://github.com/arribada/linkitv4

## Hardware References (Previous Versions)

Linkit V4 is the successor of previous Linkit CORE board generations developed by Arribada.  
Earlier versions were based on different satellite transceivers and power architectures.

- **Linkit V3**
  - Based on the ARTIC R2/R3 satellite transceiver (now discontinued)
  - Earlier GNSS and power management architectures
  - Designed for Argos satellite network compatibility
  - Links:
    - Hardware github : https://github.com/arribada/CLS-Argos-Linkit-Hardware/
    - Firmware project : [CLS-Argos-Linkit-CORE](https://github.com/arribada/CLS-Argos-Linkit-CORE)
    - Python application : [CLS-Argos-Linkit-pylinkit](https://github.com/arribada/CLS-Argos-Linkit-pylinkit)
    - Phone application : [GenTracker](https://play.google.com/store/apps/details?id=com.trackgen&hl=fr&gl=US&pli=1) available on the store

These earlier designs are no longer actively maintained but are provided for reference and historical context.


## Known Issues & Design Notes

### Issues to Address in Next Revision

⚠️ The **Issues** section of this GitHub repository lists all fixes that need to be applied for the next batch, or by anyone who wants to design or reuse parts of this project.

## TODO

- Delete the legacy branches `smd-only` and `kim-only` once both repositories have been checked. Their content is already kept elsewhere: `smd-only` is in the history of `master` (SMD v4.2), and `kim-only` is the base of [arribada/linkit-v4-kim-hw](https://github.com/arribada/linkit-v4-kim-hw) (KIM v4.0) and is tagged `archive/kim-only`.

## License

This project contains hardware design files only.

All hardware design files (schematics, PCB layouts, Altium project files)
are licensed under the **CERN Open Hardware Licence v1.2 (CERN-OHL v1.2)**.

See the `LICENSE` file for the full license text.

