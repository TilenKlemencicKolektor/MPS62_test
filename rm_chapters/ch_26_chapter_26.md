<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 26 -->

# Chapter 26

# Slow Internal RC Oscillator (SIRC)

#### 26.1 Overview

The SIRC digital interface controls the slow internal on-chip 32 KHz RC oscillator system.

#### 26.1.1 Features

The SIRC module:
• Status register provides the current operating state:
— On and stable
— Off or on but not stable
• Operates at a frequency of 32 kHz in Functional mode

#### 26.2 Operating mode

Only a POR reset will initialize the SIRC. Destructive or Functional resets do not impact the SIRC functionality.
SIRC stabilization occurs after 96 SIRC_CLK cycles.
The SIRC output clock remains invalid until the analog SIRC stabilizes. The output clock does not glitch or overshoot its frequency
during enabling or disabling. Also, the clock does not get stuck or produce glitches on a very short hardware disable pulse.

#### 26.3 External signals

This module has no external signals.

#### 26.4 Initialization

This module does not require initialization.

#### 26.5 SIRC register descriptions

#### 26.5.1 SIRC memory map

Access to registers use 8-bit, 16-bit, or 32-bit addressing.
SIRC base address: 402C_8000h
Offset Register Access Reset value
Width
(In bits)
4h Status Register (SR) 32 R 0000_0001h
Ch Miscellaneous input (MISCELLANEOUS_IN) 32 RW 0000_0000h

---

*Page 735*

Slow Internal RC Oscillator (SIRC)

#### 26.5.2 Status Register (SR)

Offset
Register Offset
SR 4h
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
STATU
R
S
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1
Fields
Field Function
Reserved
31-1
—
Status bit for SIRC
0
0b - SIRC is off or unstable
STATUS
1b - SIRC is on and stable

#### 26.5.3 Miscellaneous input (MISCELLANEOUS_IN)

Offset
Register Offset
MISCELLANEOUS_IN Ch

---

*Page 736*

Slow Internal RC Oscillator (SIRC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
STAN
Reserved Reserved Reserved
DBY ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-9
—
Standby Enable for SIRC
8
0b - SIRC disables in Standby mode
STANDBY_EN
ABLE
1b - SIRC enables in Standby mode
Reserved
7-4
—
Reserved
3-0
—

---

*Page 737*

