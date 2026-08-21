<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 28 -->

# Chapter 28

# Slow Crystal Oscillator Digital Controller (SXOSC)

#### 28.1 Overview

The Slow crystal oscillator (SXOSC) generates a clock which can be used at the SoC level. The SXOSC has a digital interface
to control and configure the oscillator. When SXOSC is powered down at any time, it is designed not to generate any glitch at
the output clock. A counter inside SXOSC handles different stabilization times. CG cell is clock gating cell, it gates the clock till
stabilization time.

#### 28.1.1 Block diagram

power ports
SXOSC DIGITAL LOGIC
power down Synchronous power
down logic
IPS
IPS
reg
other controls
extal
CTRL[EOCV]
xtal clk div Counter logic
OSC_STAT
STATUS
OSC_STAT
CG cell CLK
SXOSC
ANALOG LOGIC
Figure 112. Block diagram

#### 28.2 Features

• SXOSC generates a 32 KHz clock output in crystal mode
• SXOSC contains a status register, the value of which becomes 1 when the crystal stabilization time is complete
• SXOSC can be powered down through software bit.

#### 28.3 Functional description

SXOSC generates control signals to configure the analog module to operate in specific modes.
The following table shows the mode of operation available for selection and its settings.

---

*Page 745*

Slow Crystal Oscillator Digital Controller (SXOSC)
Table 145. Operation mode settings
Mode Value of SXOSC_CTRL[OSCON] Output clock
Functional Oscillator 0 (oscillator switched off) 0 (indicates no output)
1 (oscillator switched on) Crystal clock

#### 28.3.1 Clock generation in crystal mode

After hard reset, the crystal oscillator is switched off by default. For clock generation in crystal mode, see Table 145 . The counter
logic starts counting and the stable clock starts running one clock cycle after reaching the value of SXOSC_CTRL[EOCV] x 128
counter value. The module writes 1 to SXOSC_STAT[OSC_STAT] after two module clock cycles.

#### 28.3.2 Clock stopping in crystal mode

To stop a stable, running clock, configure the power down mode as specified in Table 145 . A glitch does not occur because
synchronizers are used.

#### 28.3.1 Modes of operation

The SXOSC has following modes of operation.
28.3.1.1 Crystal mode
In this mode crystal is connected between extal and xtal ports, to select crystal mode see Table 145
28.3.1.2 Bypass mode
The bypass mode is handled outside the DA Wrapper.
NOTE
In this mode crystal is removed from extal and xtal ports and extal is driven by external clock and xtal
is unconnected.

#### 28.3.2 Clocking

This module has no clocking considerations.

#### 28.3.3 Interrupts

This module has no interrupts.

#### 28.4 External signals

This module has no external signals.

#### 28.5 Initialization information

To enter into any mode the following sequences must be followed. By default IP is disabled.
• Power-down Mode:
— When SXOSC is running in any mode, de-assert SXOSC_CTRL[OSCON]
• Crystal Mode:
1. Disable the IP by de-asserting SXOSC_CTRL[OSCON] bit
2. Connect the crystal between extal and xtal ports

---

*Page 746*

Slow Crystal Oscillator Digital Controller (SXOSC)
3. Write an appropriate value to SXOSC_CTRL[EOCV]
4. Program recommended value in SXOSC_CTRL[GM_SEL]
5. Enable the IP by asserting SXOSC_CTRL[OSCON] bit
6. SXOSC_STAT[OSC_STAT] bit will be set after counter runs as per programming of SXOSC_CTRL[EOCV] and
clock will be released to SoC

#### 28.6 SXOSC register descriptions

This section provides the description of all registers for configuring the SXOSC.

#### 28.6.1 SXOSC memory map

Addresses are given as offsets from the module base address. All registers can be accessed using 8-bit, 16-bit or 32-
bit addressing.
NOTE
Some of the register reset values are specifically configured for each unique device by external configuration
signals or parameters.
SXOSC base address: 402C_C000h
Offset Register Access Reset value
Width
(In bits)
0h Oscillator Control Register (SXOSC_CTRL) 32 RW 007D_0000h
4h Oscillator Status Register (SXOSC_STAT) 32 R 0000_0000h

#### 28.6.2 Oscillator Control Register (SXOSC_CTRL)

Offset
Register Offset
SXOSC_CTRL 0h
Function
Oscillator Control Register

---

*Page 747*

Slow Crystal Oscillator Digital Controller (SXOSC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
CURR_PRG_S CURR_PRG_C
EOCV
F OMP
W
Reset 0 0 0 0 0 0 0 0 0 1 1 1 1 1 0 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
OSCO
GM_SEL
N
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-29
—
Reserved
28
—
These bits specify programmability of level shifter current.
27-26
00b - 3x
CURR_PRG_S
F
01b - 2x
10b - 3.5x
11b - 4x
These bits specify programmability of comparator current.
25-24
00b - 1x
CURR_PRG_C
OMP
01b - 2x
10b - 3x
11b - 4x
23-16 End of count value
EOCV These bits specify the end of count value. This value is used by the oscillator Stabilization counter for
comparison whenever it is switched On. This counting period ensures that the external oscillator clock signal
is stable before it can be selected by the system. Oscillator counter runs on crystal clock divide by 4, and
counts value upto EOCV * 128.
NOTE
In order to find the appropriate EOCV value, ensure that the internal counter is running for
at least the stabilization time of the crystal as given in the Data Sheet.
Table continues on the next page...

---

*Page 748*

Slow Crystal Oscillator Digital Controller (SXOSC)
Table continued from the previous page...
Field Function
NOTE
It is recommended to change the value of EOCV only when the IP is in disabled state.
Reserved
15-8
—
Crystal overdrive protection This field setting decides the trans-conductance applied by SXOSC
7-6
amplifier, and it will depend on crystal specification.
GM_SEL
00b - 1x
01b - 1.25x
10b - 1.3x
11b - 1.6x
Reserved
5
—
Reserved
4-1
—
0 Crystal oscillator power-down control
OSCON
NOTE
When disabling the IP through software, program 0 to this bit-field, and ensure to not change
any other values in the registers for at least 16 SXOSC clock cycles.
0b - Crystal oscillator is switched OFF
1b - Crystal oscillator is switched ON

#### 28.6.3 Oscillator Status Register (SXOSC_STAT)

Offset
Register Offset
SXOSC_STAT 4h
Function
Oscillator Status Register

---

*Page 749*

Slow Crystal Oscillator Digital Controller (SXOSC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
OSC_
R 0
STAT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Crystal oscillator status
31
0b - Crystal oscillator output clock is not stable.
OSC_STAT
1b - Crystal oscillator is providing a stable clock.
Reserved
30-0
—

---

*Page 750*

