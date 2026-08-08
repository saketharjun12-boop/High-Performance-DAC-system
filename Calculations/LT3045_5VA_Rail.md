# LT3045 5VA Rail

## Architecture
TPS610891 5.8 V --> LT3045 --> 5.0VA

## SET Resistor
RSET = VOUT / 100 uA

RSET = 5.0 / 100 uA = 50 kOhm

**Practical: 49.9 kOhm**

## LDO Dissipation
At 5.8 V input, 5.0 V output, 100 mA:

P = (5.8 - 5.0)*0.1 = 0.08 W

## Power Good Divider
PGFB typical threshold ~= 300 mV

For a ~4.5 V Power Good threshold, choosing RLOW = 49.9 kOhm:

RHIGH ~= 698.6 kOhm

**Practical: RHIGH ~= 700 kOhm, RLOW = 49.9 kOhm**

PG pull-up to ESP32 3.3 V:

**RPG = 10 kOhm**

## Provisional Capacitors
- Input: 10 uF + 100 nF
- SET: 4.7 uF optional
- Output: 22 uF + 100 nF

Final values should be verified against the final LT3045 implementation and effective capacitance.
