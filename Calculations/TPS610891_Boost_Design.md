# TPS610891 Boost Converter Calculations

## Design Targets
- VIN = 5.0 V
- VOUT = 5.8 V
- IOUT = 100 mA
- fSW ~= 2 MHz
- L = 2.2 uH provisional
- efficiency assumption = 85%

## Feedback Divider
VOUT = VREF(1 + R1/R2)

Using VREF = 1.212 V and R2 = 100 kOhm:

R1 ~= 378.5 kOhm

**Practical: R1 ~= 379 kOhm, R2 = 100 kOhm**

## Switching Frequency Resistor
Using the datasheet equation with:
- fSW = 2 MHz
- tDELAY = 86 ns
- CFREQ = 24 pF
- VOUT = 5.8 V
- VIN = 5.0 V

**RFREQ ~= 66.7 kOhm**

## Average Inductor Current
IDC = (VOUT * IOUT)/(VIN * eta)

IDC ~= 136.5 mA

## Inductor Ripple
IPP = 1/[L(1/(VOUT-VIN)+1/VIN)fSW]

**IPP ~= 157 mA p-p**

## Peak Inductor Current
IL,PEAK = IDC + IPP/2

**IL,PEAK ~= 215 mA nominal**

## Equivalent Load Resistance
RO = VOUT/IOUT = 5.8/0.1

**RO = 58 Ohm**

## Duty Cycle
D = 1 - (VIN * eta)/VOUT

**D ~= 26.7%**

## Provisional Loop Compensation

Assumptions:
- CO effective = 45 uF
- RESR = 3 mOhm
- Rsense = 0.08 Ohm
- VREF = 1.212 V
- GEA = 190 uS
- fC = 200 kHz

Calculated:
- fP ~= 122 Hz
- fESRZ ~= 1.18 MHz
- fRHPZ ~= 2.25 MHz
- R5 ~= 155.5 kOhm
- C5 ~= 8.39 nF
- C6 ~= 0.87 pF -> DNP / open

These values are provisional until exact capacitor and inductor part numbers are selected.
