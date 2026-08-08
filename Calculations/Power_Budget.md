# Power Budget

## 5VA Analog Rail

| Load | Current |
|---|---:|
| AK4499EX VDDL + VDDR | 18 mA typ. |
| AK4499EX VREFHL + VREFHR | 46 mA typ. |
| AK4499EX AVDD | 3 mA typ. |
| AK4499EX subtotal | 67 mA |
| LT3045 operating-current provision | ~3 mA |
| Approximate load before margin | ~70 mA |
| 30% margin | ~91 mA |
| **Design target** | **100 mA** |

## +15 V / -15 V Rails

| Load | Current |
|---|---:|
| OPA1612 stages | 18 mA |
| OPA1622, 2 channels | 6.76 mA |
| Quiescent subtotal | 24.76 mA |

For 9.2 Vrms into 300 ohm:

I_RMS = 9.2 / 300 = 30.7 mA/channel

I_PEAK = sqrt(2) * 30.7 = 43.4 mA/channel

Approximate class-AB rail load per channel:

I_rail ~= I_PEAK / pi = 13.8 mA

Stereo contribution ~= 27.6 mA/rail

Total ~= 24.76 + 27.6 = 52.36 mA/rail

With 30% margin ~= 68.1 mA/rail

**Design target: 75 mA per rail**

## 3.3 V Rail

| Load | Estimated Peak |
|---|---:|
| ESP32-S3-MINI-1U | 355 mA |
| AK4191EQ 3.3 V domains | ~5 mA |
| Master clock oscillators | ~20 mA |
| 74LVC1G3157 | ~2 mA |
| Pull-ups / leakage | ~3 mA |
| **Subtotal** | **385 mA** |

Using 50% margin for ESP32 burst current:

385 mA * 1.5 = 577.5 mA

**Design target: 600 mA**

## 1.2 V Rail

AK4191EQ DVDD:
- Typical: ~18 mA
- Max: ~25 mA

25 mA * 1.30 = 32.5 mA

**Design target: 40 mA**

## Upstream TPS65131 5 V Estimate

Using 75 mA on both +15 V and -15 V:

P_OUT = 15(0.075) + 15(0.075) = 2.25 W

Assuming 80% efficiency:

P_IN = 2.25 / 0.80 = 2.8125 W

I_IN,5V = 2.8125 / 5 = 0.5625 A

**Provisional 5 V requirement: ~0.56 A**

## Upstream TPS610891 5 V Estimate

For 5.8 V at 100 mA and 85% efficiency:

I_IN = (5.8 * 0.1)/(5 * 0.85) ~= 0.136 A

**Provisional 5 V requirement: ~136 mA**
