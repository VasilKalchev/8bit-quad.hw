# Remote

There is no board design here. The remote was hand wired on protoboard around
an Arduino Nano and an nRF24L01+ module, and nothing was ever drawn for it.

What is recorded instead is what that hardware had to provide, read out of the
`8bit-quad-rc` firmware in the [firmware
repository](https://github.com/VasilKalchev/8bit-quad). The firmware is the
only surviving description of it, and it is identical in all three commits
that contain it, so there is one version of this to state.

## What it needs

An ATmega328P at 16 MHz with an nRF24L01+ on hardware SPI, three analog axes,
five digital inputs, an RGB LED and a warning LED. That is very nearly the
whole chip: only `PC4` and the crystal pins are left over.

| function | AVR | Arduino | direction | notes |
| -------- | --- | ------- | --------- | ----- |
| throttle | `PC0` | A0 | analog in | not centred, full travel is the range |
| pitch | `PC1` | A1 | analog in | centre captured at boot |
| roll | `PC2` | A2 | analog in | centre captured at boot, doubles as yaw |
| flight mode | `PC3` | A3 | digital in | needs an external pull, 1 is ACRO |
| yaw mode | `PD2` | D2 | digital in | needs an external pull, 0 enables yaw |
| control mode | `PD4` | D4 | digital in | needs an external pull, 1 is MENU |
| function button | `PB2` | D10 | digital in | internal pull-up, 0 is active |
| stick button | `PC5` | A5 | digital in | internal pull-up |
| LED red | `PD3` | D3 | PWM out | `OC2B`, timer 2 |
| LED green | `PD5` | D5 | PWM out | `OC0B`, timer 0 |
| LED blue | `PD6` | D6 | PWM out | `OC0A`, timer 0 |
| LED warning | `PB1` | D9 | PWM out | `OC1A`, timer 1 |
| radio CE | `PD7` | D7 | out | nRF24L01+ |
| radio CSN | `PB0` | D8 | out | nRF24L01+ |
| SPI MOSI | `PB3` | D11 | out | nRF24L01+ |
| SPI MISO | `PB4` | D12 | in | nRF24L01+ |
| SPI SCK | `PB5` | D13 | out | nRF24L01+ |
| serial TX | `PD1` | D1 | out | 250000 baud |
| serial RX | `PD0` | D0 | in | serial command line |

## Things the pin list does not say

**There are three axes, not four.** Yaw has no stick of its own. Holding the
yaw mode input low turns the roll axis into yaw, negated, and scaled by 2.3 in
stabilize or 1.1 in acro.

**Pitch and roll centres are measured at power-on**, five averaged readings
each, so both sticks have to be at rest when the remote is switched on. There
is no stored calibration and no way to recentre without a reset. Throttle is
not centred at all.

**Three of the digital inputs have their internal pull-ups explicitly turned
off**: flight mode, yaw mode and control mode. Those three need pulls on the
board. The two buttons keep their internal pull-ups and are active low.

**The LEDs are active high**, driven low at startup, so common cathode. Red,
green and blue are one RGB LED that the firmware mixes into six colours; the
warning LED is separate.

**Timer 0 is not the Arduino timer.** The firmware moves `millis()` onto
timer 2 so that timer 0's two PWM outputs are free for the green and blue
channels. Timer 2 still drives red while keeping time, and timer 1 drives the
warning LED. This is the same swap the flight controller's Arduino core does,
and it is why the sketch targets `8bit-quad:avr:rc` rather than an Uno.

**The ADC** runs off AVCC with a prescaler of 32, ten bits.

**The radio** runs at 2 Mbps, maximum PA level, one retry with the longest
delay, over five pipe addresses based at `0x3A3A3A3A`.

**There is no battery sensing on the remote.** The battery voltage it displays
arrives from the drone by telemetry.
