# Trace and Via Sizing

## Core Equations

Rtrace = rho*L/(w*t)

Vdrop = I*Rtrace

Ploss = I^2*Rtrace

For 1 oz copper:

t ~= 35 um

rho ~= 1.72e-8 Ohm*m

## Provisional Net Classes

| Class | Width | Via | Drill |
|---|---:|---:|---:|
| Signal | 0.20 mm | 0.60 mm | 0.30 mm |
| Clock / I2S | 0.20 mm | 0.60 mm | 0.30 mm |
| Analog signal | 0.20-0.25 mm | 0.60 mm | 0.30 mm |
| 5VA | 0.50 mm | 0.80 mm | 0.40 mm |
| +15 V / -15 V | 0.50 mm | 0.80 mm | 0.40 mm |
| Main 3.3 V trunk | ~0.8 mm | 0.80 mm | 0.40 mm |
| USB 5 V | ~1.0-1.2 mm | 1.00 mm | 0.50 mm |
| Headphone output | ~0.8 mm | 0.80 mm | 0.40 mm |

Final verification should use actual routed lengths.

## Critical Short Nets
- AK4499EX IOUT --> OPA1612
- OPA1612 feedback loops
- OPA1622 feedback / summing network
- TPS610891 SW
- TPS65131 switch nodes
- 100 nF decoupling loops
- MCLK
- LT3045 SET / OUTS
