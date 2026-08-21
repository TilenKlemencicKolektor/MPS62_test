<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 42 -->

# Chapter 42

# Power Management Controller (PMC for MCXE315,

# MCXE316, and MCXE317)

#### 42.1 Overview

PMC provides multiple power options to allow you to optimize power consumption for the appropriate level of functionality.
It includes:
• Internal voltage regulators
• POR
• Integrated low and high voltage detection system with reset (brownout) capability
The voltage regulator requires a 3.3 V or 5 V input to generate all the required secondary supplies.

---

*Page 1157*

Power Management Controller (PMC for MCXE315, MCXE316, and MCXE317)

#### 42.1.1 Block diagram

VDD_HV_A VREFH VREFL
CMP
ADC
FPM LPM
PMC
VDD_HV_A
V11 VSS VSS
(Standby)
Pins
SW
V25
GPIO
SOG (Standby)
SIRC, FIRC,
SXOSC
V11 (Run)
VSS
FXOSC V11
(Standby) SOG (Run)
Flash
memory PLL
&
TempSense
VSS VSS
(Double bond) (Double bond)
VSS V25 V11
Figure 159. Block diagram

#### 42.1.2 Features

• A combination of internal and external voltage regulator options, offering Run and Standby modes
• Active POR, providing brownout detect
• LVR for all system-relevant power domains
• Software-readable Low Voltage Status and Control register that contains flags that indicate low- or high-voltage conditions

#### 42.2 Functional description

#### 42.2.1 Modes of operation

PMC provides two basic modes of operation for voltage regulators and monitors:
• FPM supports chip run modes that have high current consumption.

---

*Page 1158*

Power Management Controller (PMC for MCXE315, MCXE316, and MCXE317)
• LPM supports chip standby modes that have low current consumption.

#### 42.2.2 Reset

The POR and all LVRs combine into one single chip POR.
After a chip POR event, you can determine which power domain caused it by reading the PORF and LVR x flags in Low Voltage
Status And Control (LVSC) .
After an initial power ramp-up of the chip in LVSC, PMC sets the POR and LVR x flags. The go/no go flags have an arbitrary value.
NOTE
After an initial power ramp-up, you must clear all flags in LVSC by writing FFFF_FFFFh to that register.
Because the flags are sticky bits, you must clear them before using them. That way, if an unexpected chip POR occurs you can
track and debug the source of the problem by reading the flags in LVSC.

#### 42.2.3 Interrupts

PMC includes two interrupt sources:
• HVD interrupt: Combines all HVD monitors into one interrupt source. CONFIG[HVDIE] enables this interrupt. See PMC
Configuration (CONFIG) and Low Voltage Status And Control (LVSC) for details.
• LVD interrupt: The LVD5A monitor is the only interrupt source. CONFIG[LVDIE] enables this interrupt. See PMC
Configuration (CONFIG) and Low Voltage Status And Control (LVSC) for details.

#### 42.3 Signals

Table 214. Signals
Signal Type Description
Supply input The primary high-voltage supply input to PMC. VDD_HV_A is used
VDD_HV_A
for the PMC internal precision references. After the VDD_HV_A power
domain is powered up, it must always be kept powered for both FPM
and LPM.
Supply output A fully integrated low-dropout linear voltage regulator drives the V25
V25
power supply domain. V25 supplies the flash memory and (via a
double bond) the clock modules.
Supply output V11 is the core and logic supply. A fully integrated low-dropout linear
V11
voltage regulator drives V11.
VSS Ground VSS must be grounded. You must connect all VSS pins externally to
the same ground node.

#### 42.4 PMC register descriptions

#### 42.4.1 PMC memory map

DEFAULT_NICKNAME base address: 0h

---

*Page 1159*

Power Management Controller (PMC for MCXE315, MCXE316, and MCXE317)
Offset Register Access Reset value
Width
(In bits)
0h Low Voltage Status And Control (LVSC) 32 RW See section
4h PMC Configuration (CONFIG) 32 RW 0000_0000h
Ch Version ID (VERID) 32 R 0300_0000h

#### 42.4.2 Low Voltage Status And Control (LVSC)

Offset
Register Offset
LVSC 0h
Function
Contains status and control fields that support the low-voltage reset and low- or high-voltage detect functions. When PMC is in
LPM, the low- or high-voltage detect systems are disabled.
NOTE
For all flags that are not affected by reset (POR flag, all LVR flags, all GNG flags), if a reset occurs while trying to
clear the flags (by writing 1), the flag value is not defined appropriately. In this case, you must clear the flag again
after exiting from reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
GNG1 GNG2 LVR11 LVR11 LVR25 LVR25 LVRAL
R PORF 0 0 LVRAF
1OS ... 5OS ... LPF F LPF F PF
W W1C W1C W1C W1C W1C W1C W1C W1C W1C
Reset u 0 0 0 0 0 u u u u u u 0 0 u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
LVD5A HVD11 HVD25 HVDA LVD5A HVD11 HVD25 HVDA
R 0 0 0 0
S S S S F F F F
W W1C W1C W1C W1C
Reset 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 POR Flag
PORF Indicates that a power-on reset event has occurred. Other reset sources have no effect.
Table continues on the next page...

---

*Page 1160*

Power Management Controller (PMC for MCXE315, MCXE316, and MCXE317)
Table continued from the previous page...
Field Function
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Event did not occur
1b - Event occurred
When writing
0b - No effect
1b - Clear the flag
Reserved
30-26
—
25 Go/No Go Detect Flag On OSC Part Of V11 Power Domain
GNG11OSCF Indicates that the go/no go sensor has detected a low voltage in the V11 power domain in FPM. This
applies only to the part of the power domain that supplies the 1.1 V clocking modules (for example, PLL).
Other reset sources have no effect.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Event did not occur
1b - Event occurred
When writing
0b - No effect
1b - Clear the flag
24 Go/No Go Detect Flag On OSC Part of V25 Power Domain
GNG25OSCF Indicates that the go/no go sensor has detected a low voltage in the V25 power domain in FPM. This
applies only to the part of the power domain that supplies the 2.5 V clocking modules (for example,
XOSC and IRC). Other reset sources have no effect.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Event did not occur
1b - Event occurred
When writing
0b - No effect
Table continues on the next page...

---

*Page 1161*

Power Management Controller (PMC for MCXE315, MCXE316, and MCXE317)
Table continued from the previous page...
Field Function
1b - Clear the flag
23 LVR11LP Flag On V11 Power Domain
LVR11LPF Indicates that a low-voltage reset event has occurred in the 1.1 V V11 power domain (FPM or
LPM).Other reset sources have no effect.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Event did not occur
1b - Event occurred
When writing
0b - No effect
1b - Clear the flag
22 LVR11 Flag On V11 Power Domain In FPM
LVR11F Indicates that a low-voltage reset event has occurred in the 1.1 V V11 power domain in FPM. Other reset
sources have no effect.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Event did not occur
1b - Event occurred
When writing
0b - No effect
1b - Clear the flag
21 LVR25LP Flag On V25 Power Domain
LVR25LPF Indicates that a low-voltage reset event has occurred in the V25 power domain (FPM or LPM). Other
reset sources have no effect.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Event did not occur
1b - Event occurred
When writing
Table continues on the next page...

---

*Page 1162*

Power Management Controller (PMC for MCXE315, MCXE316, and MCXE317)
Table continued from the previous page...
Field Function
0b - No effect
1b - Clear the flag
20 LVR25 Flag On V25 Power Domain In FPM
LVR25F Indicates that a low-voltage reset event has occurred in the V25 power domain in FPM. Other reset
sources have no effect.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Event did not occur
1b - Event occurred
When writing
0b - No effect
1b - Clear the flag
Reserved
19-18
—
17 LVRALP Flag On VDD_HV_A Power Domain
LVRALPF Indicates that a low-voltage reset event has occurred in the VDD_HV_A power domain (FPM or LPM).
Other reset sources have no effect.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Event did not occur
1b - Event occurred
When writing
0b - No effect
1b - Clear the flag
16 LVRA Flag On VDD_HV_A Power Domain In FPM
LVRAF Indicates that a low-voltage reset event has occurred in the VDD_HV_A power domain in FPM. Other
reset sources have no effect.
NOTE
This field behaves differently for register reads and writes.
When reading
Table continues on the next page...

---

*Page 1163*

Power Management Controller (PMC for MCXE315, MCXE316, and MCXE317)
Table continued from the previous page...
Field Function
0b - Event did not occur
1b - Event occurred
When writing
0b - No effect
1b - Clear the flag
Reserved
15-13
—
12 LVD5A Status On VDD_HV_A Power Domain In FPM
LVD5AS Indicates whether the voltage on VDD_HV_A is above or below the low-voltage detect threshold. This
monitor reflects the status of the 5 V low-voltage detect, LVD5A, and indicates if the voltage is below
a certain threshold, which is set slightly below 4.5 V (see the chip data sheet for the exact value). The
feature is only available in FPM and is disabled in LPM. After a reset or wake-up from LPM, you must
clear the LVD5AF flag and check LVD5AS to determine the voltage level on the VDD_HV_A supply.
0b - Above
1b - Below
11 HVD11 Status On V11 Power Domain In FPM
HVD11S Indicates whether the voltage on V11 is above or below the high-voltage detect threshold. This field
reflects the status of the high-voltage detect, HVD11, on the V11 power domain. This feature is only
available in FPM and is disabled in LPM.
0b - Voltage is below threshold or chip is in LPM
1b - Voltage is above threshold and chip is in FPM
10 HVD25 Status On V25 Power Domain In FPM
HVD25S Indicates whether the voltage on V25 is above or below the high-voltage detect threshold. This field
reflects the status of the high-voltage detect, HVD25, on the V25 power domain. The feature is only
available in FPM and is disabled in LPM.
0b - Voltage is below threshold or chip is in LPM
1b - Voltage is above threshold and chip is in FPM
Reserved
9
—
8 HVDA Status On VDD_HV_A Power Domain In FPM
HVDAS Indicates whether the voltage on VDD_HV_A is above or below the high-voltage detect threshold. This
field reflects the status of the high-voltage detect, HVDA, on the V25 power domain. The feature is only
available in FPM and is disabled in LPM.
0b - Voltage is below threshold or chip is in LPM
1b - Voltage is above threshold and chip is in FPM
Table continues on the next page...

---

*Page 1164*

Power Management Controller (PMC for MCXE315, MCXE316, and MCXE317)
Table continued from the previous page...
Field Function
Reserved
7-5
—
4 LVD5A Flag On VDD_HV_A Power Domain In FPM
LVD5AF Indicates whether LVD5AS has changed. When LVD5AS changes, PMC changes LVD5AF to 1.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Did not change
1b - Changed
When writing
0b - No effect
1b - Clear the flag
3 HVD11 Flag On V11 Power Domain In FPM
HVD11F Indicates whether HVD11S has changed. When HVD11S changes, PMC changes HVD11F to 1.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Did not change
1b - Changed
When writing
0b - No effect
1b - Clear the flag
2 HVD25 Flag On V25 Power Domain In FPM
HVD25F Indicates whether HVD25S has changed. When HVD25S changes, PMC changes HVD25F to 1.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Did not change
1b - Changed
When writing
0b - No effect
1b - Clear the flag
Table continues on the next page...

---

*Page 1165*

Power Management Controller (PMC for MCXE315, MCXE316, and MCXE317)
Table continued from the previous page...
Field Function
Reserved
1
—
0 HVDA Flag On VDD_HV_A Power Domain In FPM
HVDAF Indicates whether HVDAS has changed. When HVDAS changes, PMC changes HVDAF to 1.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Did not change
1b - Changed
When writing
0b - No effect
1b - Clear the flag

#### 42.4.3 PMC Configuration (CONFIG)

Offset
Register Offset
CONFIG 4h
Function
Configures PMC.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
LPM25 FAST
LVDIE HVDIE
EN REC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1166*

Power Management Controller (PMC for MCXE315, MCXE316, and MCXE317)
Fields
Field Function
Reserved
31-10
—
9 Low Voltage Detect Interrupt Enable
LVDIE Enables hardware interrupt requests if LVSC[LVD5AF] = 1. You must disable the LVD interrupt before
entering LPM.
0b - LVD hardware interrupt is disabled (use polling)
1b - Request an LVD hardware interrupt when LVDA5F = 1
8 High Voltage Detect Interrupt Enable
HVDIE Enables hardware interrupt requests if any of the following flags in Low Voltage Status And Control
(LVSC) are set:
• HVDAF
• HVDBF
• HVD25F
• HVD11F
0b - HVD hardware interrupt is disabled (use polling)
1b - Request an HVD hardware interrupt when HVDAF=1, HVDBF=1, HVD25F=1, or HVD11F=1
Reserved
7-4
—
3 V25 Power Domain Enable During LPM
LPM25EN Controls whether the V25 regulator and low-voltage reset detection (LVR25LP) are enabled or disabled
in LPM
0b - Disabled
1b - Enabled
2 Fast Recovery From LPM Enable
FASTREC Controls the recovery time from LPM to FPM.
At recovery from LPM, all the tank capacitors from the secondary supplies must be recharged. This causes a
high-current demand, which the supply driving the VDD_HV_A primary power domain might not meet. When
you select the fast recovery time, the current for recharging is approximately three times higher than that
for normal recovery time. You must determine whether this current is sufficient to start up from LPM in time,
using these criteria:
• Drive capability of the external VDD_HV_A regulator
• Size of the tank caps on the secondary supply pin
• Selected recovery time
Table continues on the next page...

---

*Page 1167*

Power Management Controller (PMC for MCXE315, MCXE316, and MCXE317)
Table continued from the previous page...
Field Function
0b - Normal
1b - Fast
Reserved
1-0
—

#### 42.4.4 Version ID (VERID)

Offset
Register Offset
VERID Ch
Function
Records the specific PMC version in the chip.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R MAJOR MINOR
W
Reset 0 0 0 0 0 0 1 1 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
LMFE
R 0
AT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 Major Version Number
MAJOR Indicates the major version number of the PMC design.
23-16 Minor Version Number
MINOR Indicates the minor version number of the PMC design.
Reserved
15-1
Table continues on the next page...

---

*Page 1168*

Power Management Controller (PMC for MCXE315, MCXE316, and MCXE317)
Table continued from the previous page...
Field Function
—
0 Last-Mile Regulator Feature
LMFEAT Indicates whether the last-mile regulator (1.5 V to 1.1 V) is available.
0b - Not available
1b - Available

#### 42.5 Glossary

FPM Full Performance mode
HVD High voltage detect
LPM Low Power mode
LVD Low voltage detect
LVR Low voltage reset
POR Power-on reset

---

*Page 1169*

