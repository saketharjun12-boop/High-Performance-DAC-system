# High-Performance Mixed-Signal Headphone DAC

**AK4191EQ + AK4499EX + ESP32-S3 | KiCad | Low-Noise Power | High-Impedance Headphones**

A custom mixed-signal headphone DAC/amplifier designed around the AK4191EQ digital modulator and AK4499EX DAC.

> **Project status:** PCB routing is **not complete**. Routing is currently complete **through Stage 3 only**. Later analog/output and remaining power-routing sections are still in progress.

## System Architecture

```text
USB-C 5 V
   |
   +--> TPS610891 --> 5.8 V --> LT3045 --> 5VA --> AK4499EX analog rails
   |
   +--> TPS65131 --> +15 V / -15 V --> OPA1612 / OPA1622
   |
   +--> 3.3 V regulator --> ESP32-S3 / logic / clocks
   |
   +--> 1.2 V regulator --> AK4191EQ core
```

```text
ESP32-S3 --> AK4191EQ --> AK4499EX --> OPA1612 I/V --> LPF / Differential Stage --> OPA1622 --> Relay --> 6.35 mm Jack
```

## Schematic Progress

Upload schematic images to `Hardware/Schematic_Stages/` with these filenames:

### Stage 1
![Stage 1](Hardware/Schematic_Stages/stage_1.png)

### Stage 2
![Stage 2](Hardware/Schematic_Stages/stage_2.png)

### Stage 3
![Stage 3](Hardware/Schematic_Stages/stage_3.png)

### Power Stage
![Power Stage](Hardware/Schematic_Stages/power_stage.png)

### Analog Output Stage
![Analog Output](Hardware/Schematic_Stages/analog_output_stage.png)

## Current Status

- [x] System architecture
- [x] Major component selection
- [x] 5VA rail architecture
- [x] ±15 V rail architecture
- [x] I/V conversion stage
- [x] Headphone output stage
- [x] Initial power budget
- [x] Initial boost-converter calculations
- [x] Initial trace/via sizing methodology
- [x] PCB routing through **Stage 3**
- [ ] Remaining PCB routing
- [ ] Final ERC/DRC
- [ ] Gerbers
- [ ] Fabrication
- [ ] Bring-up
- [ ] Measurements

## Engineering Calculations

See [`Calculations/`](Calculations/).

Included:
- Power budget
- TPS610891 boost calculations
- LT3045 5VA rail calculations
- Headphone load / ±15 V rail calculations
- Trace and via sizing notes
- Folder for handwritten calculations

> Some calculation values are provisional and should be updated after the final BOM is frozen.

## Repository Structure

```text
Calculations/
    Power_Budget.md
    TPS610891_Boost_Design.md
    LT3045_5VA_Rail.md
    Headphone_Load_and_Analog_Rails.md
    Trace_and_Via_Sizing.md
    Handwritten_Calculations/

Hardware/
    PCB_Files/
    Schematic_Files/
    Schematic_Stages/
    Gerbers/
    BOM/

Images/
Documentation/
Firmware/
Measurements/
```

## Notes

This repository documents an **in-progress engineering design**. The PCB has not yet been fully routed, fabricated, or electrically validated.
