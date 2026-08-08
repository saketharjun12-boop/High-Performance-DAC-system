# High-Performance Mixed-Signal Headphone DAC

**AK4191EQ + AK4499EX + ESP32-S3 | KiCad | Low-Noise Power | High-Impedance Headphones**

A custom mixed-signal headphone DAC/amplifier designed around the **AK4191EQ digital modulator** and **AK4499EX DAC**, with a focus on low-noise analog power, current-to-voltage conversion, differential analog processing, and high-impedance headphone drive.

> **Project status:** PCB routing is **not complete**. Routing is currently complete **through Stage 2 only**. Later analog/output and remaining power-routing sections are still in progress.

---

## Project Overview

Primary signal chain:

```text
Digital Source
    |
AK4191EQ Digital Modulator
    |
AK4499EX DAC
    |
OPA1612 I/V Conversion
    |
Low-Pass / Differential Summing
    |
OPA1622 Headphone Driver
    |
Output Mute Relay
    |
6.35 mm TRS Headphone Jack
```

Control is provided by an **ESP32-S3**, including I2C configuration and future power-sequencing / relay-control functionality.

---

## Main Components

| Function | Component |
|---|---|
| Digital modulator | AK4191EQ |
| DAC | AK4499EX |
| Controller | ESP32-S3 |
| I/V conversion | OPA1612 |
| Headphone output stage | OPA1622 |
| 5 V to 5.8 V boost | TPS610891 |
| Ultra-low-noise 5VA LDO | LT3045 |
| Bipolar analog supply | TPS65131 |
| Output muting | DPDT relay |
| Output connector | 6.35 mm TRS jack |

---

## Current Project Status

- [x] Overall architecture defined
- [x] Major components selected
- [x] AK4191EQ / AK4499EX architecture
- [x] I/V conversion stage
- [x] Differential / headphone output stage
- [x] Relay-based output mute
- [x] 5VA power architecture
- [x] +/-15 V rail architecture
- [x] Initial power budget
- [x] TPS610891 design calculations
- [x] LT3045 calculations
- [x] Initial +/-15 V / headphone-load budget
- [x] Initial trace-width and via-sizing methodology
- [x] PCB routing through **Stage 3**
- [ ] Remaining PCB routing
- [ ] Final ERC / DRC
- [ ] Final BOM freeze
- [ ] Gerber generation
- [ ] Fabrication
- [ ] Assembly
- [ ] Board bring-up
- [ ] Electrical validation
- [ ] Audio measurements

---

# Power Architecture

```text
USB-C 5 V
   |
   +---- TPS610891 ----> 5.8 V ----> LT3045 ----> 5VA
   |
   +---- TPS65131 ----> +15 V / -15 V
   |
   +---- 3.3 V Regulator ----> ESP32-S3 / clocks / logic
   |
   +---- 1.2 V Regulator ----> AK4191EQ core
```

The **5VA** rail is reserved for the AK4499EX analog/reference supply domains.

---

# Power Budget

The power budget is used to verify regulator capability, estimate upstream USB-C current, and provide starting values for power-trace and via sizing.

## 5VA Analog Rail

| Device / Supply Domain | Typical Current |
|---|---:|
| AK4499EX VDDL + VDDR | 18 mA |
| AK4499EX VREFHL + VREFHR | 46 mA |
| AK4499EX AVDD | 3 mA |
| **AK4499EX subtotal** | **67 mA** |
| LT3045 operating-current provision | ~3 mA |
| **Approximate subtotal** | **~70 mA** |

30% design margin:

```text
70 mA x 1.30 = 91 mA
```

Rounded target:

**5VA design current = 100 mA**

The LT3045 is rated for **500 mA**, so the present load estimate has substantial current margin.

## +15 V and -15 V Rails

| Device | Approximate Current |
|---|---:|
| OPA1622, two channels | 6.76 mA |
| OPA1612 stages | 18 mA |
| **Quiescent subtotal** | **24.76 mA** |

The headphone load adds output-stage rail current. A provisional design target of **75 mA per rail** is derived below.

## 3.3 V Rail

| Component / Function | Peak / Estimated Current |
|---|---:|
| ESP32-S3-MINI-1U peak RF transient assumption | 355 mA |
| AK4191EQ 3.3 V domain(s) | ~5 mA |
| Master-clock oscillators | ~20 mA total |
| 74LVC1G3157 MUX | ~2 mA |
| Pull-ups / passive leakage | ~3 mA |
| **Estimated peak subtotal** | **385 mA** |

Using 50% margin for ESP32 burst current:

```text
385 mA x 1.5 = 577.5 mA
```

Rounded target:

**3.3 V design current = 600 mA**

## 1.2 V Rail

AK4191EQ DVDD core:

- Typical: ~18 mA
- Max / peak design value: ~25 mA

30% margin:

```text
25 mA x 1.30 = 32.5 mA
```

Rounded target:

**1.2 V design current = 40 mA**

---

# TPS610891 Boost Converter Calculations

The TPS610891 creates approximately **5.8 V** from the USB-C 5 V input so the LT3045 has headroom to regulate to 5.0VA.

## Design Targets

| Parameter | Value |
|---|---:|
| VIN | 5.0 V nominal |
| VOUT | 5.8 V |
| IOUT | 100 mA |
| fSW | ~2 MHz |
| Provisional efficiency | 85% |
| Provisional inductor | 2.2 uH |

## Feedback Divider

The PWM feedback reference is approximately 1.212 V.

```text
VOUT = VREF(1 + R1/R2)
```

Choose:

```text
R2 = 100 kOhm
```

Then:

```text
R1 = R2(VOUT/VREF - 1)
R1 = 100k(5.8/1.212 - 1)
R1 ~= 378.5 kOhm
```

Practical selection:

- **R1 ~= 379 kOhm**
- **R2 = 100 kOhm**

## Switching-Frequency Resistor

Using the TPS610891 frequency-setting equation:

```text
RFREQ = 4(1/fSW - tDELAY*VOUT/VIN) / CFREQ
```

with:

```text
fSW = 2 MHz
tDELAY = 86 ns
CFREQ = 24 pF
VOUT = 5.8 V
VIN = 5.0 V
```

Calculated:

**RFREQ ~= 66.7 kOhm**

## Average Inductor Current

```text
IDC = (VOUT*IOUT)/(VIN*eta)
IDC = (5.8*0.1)/(5*0.85)
IDC ~= 136.5 mA
```

## Inductor Ripple Current

```text
IPP = 1 / [L(1/(VOUT-VIN) + 1/VIN)fSW]
```

For L = 2.2 uH:

**IPP ~= 157 mA p-p**

## Peak Inductor Current

```text
IL,peak = IDC + IPP/2
IL,peak = 136.5 mA + 78.5 mA
IL,peak ~= 215 mA
```

Worst-case inductance check using a 30% reduction:

```text
Lmin = 2.2 uH x 0.7 = 1.54 uH
```

A shielded power inductor rated around **1 A or higher** provides generous margin relative to the present calculated peak.

## Equivalent Output Load Resistance

```text
RO = VOUT/IOUT = 5.8/0.1 = 58 Ohm
```

**RO = 58 Ohm**

## Duty Cycle

Using the small-signal equation:

```text
D = 1 - (VIN*eta)/VOUT
D = 1 - (5*0.85)/5.8
D ~= 0.267
```

**D ~= 26.7%**

## Provisional Compensation Assumptions

```text
CO effective = 45 uF
RESR = 3 mOhm
L = 2.2 uH
fSW = 2 MHz
eta = 85%
IOUT = 100 mA
```

These values are provisional until the final capacitor and inductor part numbers are selected.

## Output Pole

```text
fP = 2/(2*pi*RO*CO)
fP ~= 122 Hz
```

## ESR Zero

```text
fESRZ = 1/(2*pi*RESR*CO)
fESRZ ~= 1.18 MHz
```

## Right-Half-Plane Zero

```text
fRHPZ = RO(1-D)^2/(2*pi*L)
fRHPZ ~= 2.25 MHz
```

## Crossover Frequency

Guideline:

```text
fC <= min(fSW/10, fRHPZ/5)
```

```text
fSW/10 = 200 kHz
fRHPZ/5 ~= 450 kHz
```

Provisional crossover:

**fC = 200 kHz**

## Compensation Resistor R5

```text
R5 = [2*pi*VOUT*Rsense*fC*CO] / [(1-D)*VREF*GEA]
```

Using:

```text
Rsense = 0.08 Ohm
VREF = 1.212 V
GEA = 190 uS
```

Calculated:

**R5 ~= 155.5 kOhm**

Practical provisional values:

- 154 kOhm
- 158 kOhm

## Compensation Capacitor C5

```text
C5 = RO*CO/(2*R5)
C5 ~= 8.39 nF
```

Practical provisional value:

**C5 ~= 8.2 nF**

## Compensation Capacitor C6

```text
C6 = RESR*CO/R5
C6 ~= 0.87 pF
```

Because the calculated value is below 10 pF:

**C6 = DNP / Open**

> The compensation network is provisional and should be recalculated using the final inductor, effective output capacitance, capacitor ESR, switching frequency, efficiency, and load.

---

# LT3045 5VA Rail Calculations

Architecture:

```text
5.8 V --> LT3045 --> 5.0VA
```

## SET Resistor

The SET pin sources approximately 100 uA.

```text
RSET = VOUT/ISET
RSET = 5.0/(100 uA)
RSET = 50 kOhm
```

Practical precision value:

**RSET = 49.9 kOhm**

## LDO Dissipation

At the 100 mA design target:

```text
PLDO = (VIN-VOUT)IOUT
PLDO = (5.8-5.0)(0.1)
PLDO = 0.08 W
```

## Power-Good Divider

The PGFB comparator threshold is approximately 300 mV.

Desired Power Good threshold:

```text
VTRIP ~= 4.5 V
```

Choose:

```text
RBOTTOM = 49.9 kOhm
```

Then:

```text
RTOP = RBOTTOM(VTRIP/VPGFB - 1)
RTOP = 49.9k(4.5/0.3 - 1)
RTOP ~= 698.6 kOhm
```

Practical values:

- **RTOP ~= 700 kOhm**
- **RBOTTOM = 49.9 kOhm**

## PG Pull-Up

For ESP32 3.3 V logic:

**RPG = 10 kOhm to 3.3 V**

## Provisional LT3045 Capacitors

- Input: **10 uF + 100 nF**
- SET: **4.7 uF** optional low-noise capacitor
- Output: **22 uF + 100 nF**

Final values should be verified against the final LT3045 implementation.

---

# TPS65131 +/-15 V Rail Calculations

Current design targets:

- **+15 V, 75 mA**
- **-15 V, 75 mA**

## Output Power

```text
POUT = 15I+15 + 15I-15
POUT = 15(0.075) + 15(0.075)
POUT = 2.25 W
```

## 5 V Input-Power Estimate

Assume provisional efficiency:

```text
eta = 80%
```

Then:

```text
PIN = POUT/eta
PIN = 2.25/0.8
PIN = 2.8125 W
```

Input current from 5 V:

```text
IIN,5V = PIN/5
IIN,5V = 2.8125/5
IIN,5V ~= 563 mA
```

**Provisional TPS65131 5 V input requirement ~= 0.56 A**

---

# Headphone Load Calculations

Target class: high-impedance headphones such as a nominal **300 Ohm** load.

The AK4499EX documentation indicates approximately:

- **4.6 Vrms** after individual I/V conversion around signal common
- approximately **9.2 Vrms** after external differential summing

## RMS Headphone Current

```text
IRMS = VRMS/RHP
IRMS = 9.2/300
IRMS ~= 30.7 mA/channel
```

## Peak Current

```text
IPEAK = sqrt(2)*IRMS
IPEAK ~= 43.4 mA/channel
```

## Output Power

```text
POUT = VRMS^2/RHP
POUT = 9.2^2/300
POUT ~= 0.282 W/channel
```

## Approximate Rail-Current Contribution

For a sinusoidal class-AB approximation:

```text
Irail,load ~= IPEAK/pi
Irail,load ~= 13.8 mA/channel
```

Stereo:

```text
Irail,load,stereo ~= 27.6 mA/rail
```

Add amplifier quiescent current:

```text
24.76 + 27.6 = 52.36 mA/rail
```

Apply 30% margin:

```text
52.36 x 1.30 = 68.1 mA/rail
```

Rounded design target:

**75 mA per rail**

---

# Trace Width Calculations

Power-trace width is based on current, copper thickness, route length, acceptable voltage drop, temperature rise, and fabrication limits.

Copper resistivity:

```text
rho ~= 1.72e-8 Ohm*m
```

For 1 oz copper:

```text
t ~= 35 um
```

## Trace Resistance

```text
Rtrace = rho*L/(w*t)
```

## Voltage Drop

```text
Vdrop = I*Rtrace
```

## Power Loss

```text
Ploss = I^2*Rtrace
```

## Width From Voltage-Drop Requirement

```text
w = rho*L*I/(t*Vdrop,max)
```

## 5VA Example

Assume:

```text
I = 0.1 A
L = 50 mm = 0.05 m
Vdrop,max = 5 mV = 0.005 V
t = 35 um
```

Calculated:

```text
w ~= 0.49 mm
```

Practical provisional width:

**5VA = 0.50 mm**

## +/-15 V Example

Using:

```text
I = 75 mA
L = 50 mm
Vdrop,max = 5 mV
```

Calculated width is approximately 0.37 mm.

Practical provisional choice:

**+15 V / -15 V = 0.50 mm**

## 3.3 V Main Distribution

With a 600 mA design target, the main 3.3 V trunk is provisionally assigned:

**3.3 V main = ~0.8 mm**

Individual low-current branches do not need to remain 0.8 mm.

## USB 5 V

Provisional initial class:

**USB 5 V = 1.0-1.2 mm**

## Headphone Output

The 300 Ohm load current is modest, so the wider trace primarily reduces added series resistance and provides robust routing.

**Headphone output = ~0.8 mm**

---

# Via Sizing

## Standard Signal Via

```text
Diameter: 0.60 mm
Drill:    0.30 mm
```

Used for digital signals, I2C, I2S, ordinary analog signals, ground stitching, and low-current branches.

## Power Via

```text
Diameter: 0.80 mm
Drill:    0.40 mm
```

Used for 5VA, +/-15 V, and main 3.3 V power transitions.

## Higher-Current USB Via

```text
Diameter: 1.00 mm
Drill:    0.50 mm
```

or multiple smaller vias in parallel.

Multiple vias are preferred where higher current or lower inductance is desired.

---

# Net Classes

| Net Class | Clearance | Width | Via | Drill |
|---|---:|---:|---:|---:|
| Signal | 0.20 mm | 0.20 mm | 0.60 mm | 0.30 mm |
| Clock | 0.20 mm | 0.20 mm | 0.60 mm | 0.30 mm |
| I2S | 0.20 mm | 0.20 mm | 0.60 mm | 0.30 mm |
| Analog Signal | 0.20 mm | 0.20-0.25 mm | 0.60 mm | 0.30 mm |
| 5VA | 0.20 mm | 0.50 mm | 0.80 mm | 0.40 mm |
| +15 V | 0.20 mm | 0.50 mm | 0.80 mm | 0.40 mm |
| -15 V | 0.20 mm | 0.50 mm | 0.80 mm | 0.40 mm |
| 3V3 Main | 0.20 mm | ~0.80 mm | 0.80 mm | 0.40 mm |
| 1V2 | 0.20 mm | 0.30-0.50 mm | 0.60 mm | 0.30 mm |
| USB Power | 0.20-0.25 mm | 1.0-1.2 mm | 1.00 mm | 0.50 mm |
| Headphone Out | 0.20 mm | 0.80 mm | 0.80 mm | 0.40 mm |

These values should be checked against the final PCB manufacturer's design rules.

---

# Critical Signal Lengths

| Connection | Target |
|---|---:|
| AK4499EX IOUT to OPA1612 input | ideally <5-10 mm |
| OPA1612 feedback loop | ideally <5 mm |
| OPA1622 feedback / summing network | ideally <5-10 mm |
| 100 nF decoupling capacitor | approximately 1-3 mm from supply pin where practical |
| TPS610891 SW to inductor | minimum practical length |
| TPS65131 switch nodes | minimum practical length |
| LT3045 SET / OUTS | preferably <5-10 mm |
| MCLK | preferably <25-30 mm |
| BCLK / LRCK / SDATA | short; ~10 mm BCLK is excellent |

---

# Repository Folders

```text
High_Performance_DAC_Portfolio/
|
|-- README.md
|-- PCB_Files/
|-- Schematics/
|-- Images/
|-- Handwritten_Calculations/
|-- Datasheets/
|-- BOM/
`-- Gerbers/
```

## Schematic Images and PCB progress


## Total Circuit
![Total Circuit](Schematics/Total%20Circuit.png)

## Stage 1
![Stage 1](Schematics/Stage%201.png)

## Stage 2
![Stage 2](Schematics/Stage%202.png)

## Stage 3
![Stage 3](Schematics/Stage%203.png)

## Stage 4
![Stage 4](Schematics/Stage%204.png)

## USB-C Interface
![USB-C Interface](Schematics/USB%20C%20interface.png)

## Boost Inverter and Boost Regulator
![Boost Inverter and Boost Regulator](Schematics/Boost%20inverter%20and%20Boost%20regulator.png)

## PCB progress Stage 1 and Stage 2
![PCB Progress](Images/PCB%20Progress.png)
![PCB Progress 1](Images/PCB%20progress%201.png)




---

# Planned Validation

After fabrication:

- Verify 5.8 V boost output
- Verify 5VA regulation
- Measure 5VA ripple
- Measure +15 V and -15 V ripple
- Verify startup sequencing
- Verify Power Good behavior
- Verify relay mute timing
- Measure DAC DC offsets
- Verify maximum headphone output
- Measure frequency response
- Measure FFT / noise floor
- Measure THD+N
- Measure output impedance
- Evaluate thermal performance

---

# Future Work

- Complete PCB routing after Stage 2
- Recalculate power-trace voltage drop using actual routed lengths
- Freeze exact capacitor and inductor manufacturer part numbers
- Recalculate TPS610891 compensation using final effective capacitance and ESR
- Perform final regulator thermal checks
- Run ERC and DRC
- Generate Gerbers
- Fabricate and assemble the PCB
- Perform bring-up and measurement
- Add measurement plots and final PCB renders to this README

---

# Disclaimer

This repository documents an **in-progress engineering project**.

The present PCB routing is not complete, and the design has not yet been fabricated or electrically validated. Several calculated values are provisional and will be updated after the final BOM, PCB routing, and measured prototype data are available.
