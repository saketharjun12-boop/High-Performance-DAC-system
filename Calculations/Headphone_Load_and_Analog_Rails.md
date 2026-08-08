# Headphone Load / Analog Rail Calculations

Target load: nominal 300 ohm high-impedance headphone.

For 9.2 Vrms:

I_RMS = 9.2/300 = 30.7 mA/channel

I_PEAK = sqrt(2)*30.7 = 43.4 mA/channel

P_OUT = 9.2^2/300 ~= 0.282 W/channel

Approximate class-AB rail-load contribution:

I_rail ~= I_PEAK/pi ~= 13.8 mA/channel

Stereo ~= 27.6 mA/rail

Adding provisional amplifier quiescent current:

24.76 + 27.6 ~= 52.36 mA/rail

With 30% margin:

52.36 * 1.3 ~= 68.1 mA/rail

**Provisional rail target: 75 mA on +15 V and 75 mA on -15 V**
