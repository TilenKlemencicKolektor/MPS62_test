<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 25 -->

# Chapter 25

# Fast Internal RC Oscillator (FIRC)

#### 25.1 Overview

The FIRC digital interface controls the internal 48 MHz RC oscillator system.

#### 25.1.1 Features

FIRC can be disabled in Standby mode via software.
• Status register provides the current operating state:
— On and stable
— Off or on but not stable

#### 25.2 External signals

This module has no external signals.

#### 25.3 Initialization

This module does not require initialization.

#### 25.4 FIRC register descriptions

#### 25.4.1 FIRC memory map

FIRC base address: 402D_0000h
Offset Register Access Reset value
Width
(In bits)
4h Status Register (Status_Register) 32 R 0000_0001h
8h Standby Enable Register (STDBY_ENABLE) 32 RW 0000_0000h

#### 25.4.2 Status Register (Status_Register)

Offset
Register Offset
Status_Register 4h

---

*Page 732*

Fast Internal RC Oscillator (FIRC)
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
Status bit for FIRC
0
0b - FIRC is off or unstable.
STATUS
1b - FIRC is on and stable.

#### 25.4.3 Standby Enable Register (STDBY_ENABLE)

Offset
Register Offset
STDBY_ENABLE 8h
Function
This register enables or disables FIRC in chip’s Standby mode.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
STDB
Reserved
Y_EN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 733*

Fast Internal RC Oscillator (FIRC)
Fields
Field Function
RESERVED
31-1
—
Enables or disables FIRC in chip’s Standby mode.
0
0b - Disabled
STDBY_EN
1b - Enabled

#### 25.5 Glossary

Standby mode Power saving mode of the chip

---

*Page 734*

