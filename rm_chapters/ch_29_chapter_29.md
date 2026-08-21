<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 29 -->

# Chapter 29

# PLL Digital Interface (PLLDIG)

#### 29.1 Chip-specific PLLDIG information

#### 29.1.1 PLL-supported accesses and frequencies

The PLLODIV_0 and PLLODIV_1 registers support only word accesses. When you write to these registers, you must retain the
default values of the reserved fields.
PLLDIG supports a down-spread modulation of up to 160 MHz PLL PHI clock output only.

#### 29.1.2 Register implementation

In MCXE315/MCXE316 there are additional registers as compared to what is mentioned in section 'PLLDIG memory map'. See
following table for details.
Table 146. Register details
Register/Bitfield Offset Availability
1
PLLCLKMUX[REFCLKSEL] 20h Only available in MCXE315/MCXE316
1. See section 'PLLCLKMUX definition' for register definition
29.1.2.1 PLLCLKMUX definition
Register PLLCLKMUX (PLL Clock Multiplexer) is available at offset 20h. This register selects the PLL clock source. Bitfield
definition of this register is as shown below:
Table 147. PLLCLKMUX Definition
Bitfield offset Bitfield name Bitfield description
0 REFCLKSEL Reference Clock Select: Selects the PLL clock source.
• 0b-FXOSC_CLK
• 1b-FIRC_DIV2_CLK
1-31 Reserved

#### 29.1.3 Initialization information for MCXE315/MCXE316

Perform the following steps to initialize PLL:
1. Confirm that PLLODIV_ n [DE] is 0 for all dividers.
2. Confirm that PLLCR[PLLPD] is 1.
3. Program PLLCLKMUX to select the appropriate reference clock.
4. Program the following as needed:
• PLLDV
• PLLFD
• PLLFM to the desired value
5. Program PLLDV[ODIV2] and PLLODIV_ n [DIV] to the desired values.
6. Wait for the PLL reference clock to be stable.

---

*Page 751*

PLL Digital Interface (PLLDIG)
7. Write 0 to PLLCR[PLLPD].
8. Wait for PLLSR[LOCK] to be 1.
9. Write 1 to PLLODIV_ n [DE].
Perform the following steps to shut down PLL:
1. Write 0 to PLLODIV_n[DE] for all dividers.
2. Write 1 to PLLCR[PLLPD].

#### 29.2 Overview

PLL can multiply or divide the frequency of a given clock input.

#### 29.2.1 Block diagram

Loss of lock
lol
Detector
Lock
lock
Detector
PLLODIV_0[DIV]
divider PLL_PHI0
PLLDV[RDIV] 0
Reference PFD+CPUMP
Reference clock VCO divider
divider +FILTER vcoclkout
PLLODIV_1[DIV]
PLLDV[ODIV2]
divider PLL_PHI1
Feedback Divider
+ Sigma Delta
Modulator
PLLDV[MF]
PLLFD[MFN]
Register interface
PLLFM[STEPNO] divider PLL_PHIn
PLLFM[STEPSIZE]
PLLODIV_n[DIV]
Figure 113. Block diagram
The number of output dividers can vary with the module instance. See the Clocking chapter to confirm the number of PLL
output dividers.

#### 29.2.2 Features

PLL includes the following features:
• Programmable frequency modulation
• Multiple integer dividers on PLL outputs
• Lock detection circuitry reports when PLL achieves frequency lock
• Continuous monitoring of lock status to report Loss of Lock (LOL) condition
• Powering down the module for low-power operation (Power-Down mode)

---

*Page 752*

PLL Digital Interface (PLLDIG)

#### 29.3 Functional description

This section explains PLL operation and configuration.

#### 29.3.1 Modes of operation

Table 148. Modes of operation
PLLCR[PLLPD] PLLFD[SDMEN] PLLFM[SSCGBYP] Description
1 x x PLL is disabled.
0 0 1 Functional mode – PLL operates in integer-only mode.
See Clock configuration .
0 1 1 Functional mode – PLL operates in Fractional mode
(non-Frequency modulation). See Clock configuration .
0 1 0 Functional mode – PLL operates in Frequency
Modulation mode. See Frequency modulation .

#### 29.3.2 Input clock frequency

PLL is designed to operate over a specified input clock frequency range. PLL source frequency limits are discussed in this chip's
data sheet.

#### 29.3.3 Clock configuration

See the equations below and the corresponding register configuration that determine the relationship between VCO frequency
( f VCO ) and PLL reference frequency.
• Integer-only mode:
— When PLLDV[RDIV] is 0:
Equation 1. PLL VCO frequency in integer-only mode when PLLDV[RDIV] is 0
— When PLLDV[RDIV] is not 0:
Equation 2. PLL VCO frequency in integer-only mode when PLLDV[RDIV] is not 0
• Fractional mode:
— When PLLDV[RDIV] is 0:
Equation 3. PLL VCO frequency in Fractional mode when PLLDV[RDIV] is 0
— When PLLDV[RDIV] is not 0:

---

*Page 753*

PLL Digital Interface (PLLDIG)
Equation 4. PLL VCO frequency in Fractional mode when PLLDV[RDIV] is not 0
See the equation below and the corresponding register configuration that determine the relationship between reference and
PLL_PHI n output frequencies.
Equation 5. PLL PHI output frequency
When configuring PLL, you must not violate the maximum system clock frequency or maximum and minimum VCO frequency
specification of PLL (see this chip's data sheet for frequency limits).
You must disable PLL by writing 1 to PLLCR[PLLPD] before any PLL configuration or input clock are modified.
You must disable PLL by writing 1 to PLLCR[PLLPD] for at least 5 μs before writing 0 to PLLCR[PLLPD] to enable PLL.
The recommended procedure to program PLL and enter Normal mode is shown in Initialization information .

#### 29.3.4 Loss of lock (LOL)

PLL provides LOL indication. The LOL indication can only be generated when PLL is in Functional mode (see Modes of operation ).
When PLL detects a LOL, it asserts its LOL event output.
PLL does not detect loss of reference clock. If the reference clock stops after PLL achieves lock, PLL continues to indicate lock.
It is assumed that monitoring of PLL's reference clock that is done outside PLL is enabled while PLL is in operation.
PLL LOL is intended for detection of gross failures. Use CMUs for accurate frequency monitoring.

#### 29.3.5 Frequency modulation

In Frequency Modulation mode, PLL generates a frequency-modulated clock. The modulation depth and modulation frequency
are calculated using the equations shown in Frequency modulation programming .
Write 1 to PLLFM[SPREADCTL] to select down-spread modulation. See Figure 114 that shows an example of down-
spread modulation.
Modulation frequency ( f MOD )
nominal VCO frequency ( f pll_VCO )
Modulation Depth (MD)
Down spread
Figure 114. Frequency modulation

---

*Page 754*

PLL Digital Interface (PLLDIG)
29.3.5.1 Frequency modulation programming
Modulation depth and modulation frequency programming uses step number (PLLFM[STEPNO]) and step size
(PLLFM[STEPSIZE]). The table below shows variables used during calculations when programming PLL for
frequency modulation.
Table 149. Variables for configuring modulation depth and frequency
Variable Description
f REF Input clock frequency
f MOD Expected modulation frequency
MD Expected modulation depth in percentage
LDF Loop division factor
f pll_VCO Nominal VCO frequency
Use the following equations to configure PLL for frequency modulation.
Equation 6. LDF
Equation 7. Step number
Equation 8. Step size
Frequency modulation is only possible if the condition shown in Equation 9 is met.
Equation 9. Requirement to achieve FM
You must write 0 to PLLFM[SSCGBYP] and write 1 to PLLFD[SDMEN] to enable frequency modulation.
Equation 10. Maximum possible modulation depth when PLL[RDIV] is 0
Equation 11. Maximum possible modulation depth when PLL[RDIV] is not 0

---

*Page 755*

PLL Digital Interface (PLLDIG)
CAUTION
The effective modulation depth may differ from the intended modulation depth because of rounding operations
applied to PLLFM[STEPSIZE] and PLLFM[STEPNO].

#### 29.3.6 Interrupt signals

This module has no interrupt signals.

#### 29.4 External signals

This module has no external signals.

#### 29.5 Initialization information

Perform the following steps to initialize PLL:
1. Confirm that PLLODIV_ n [DE] is 0 for all dividers.
2. Confirm that PLLCR[PLLPD] is 1.
3. Program the following as needed:
• PLLDV
• PLLFD
• PLLFM to the desired value
4. Program PLLDV[ODIV2] and PLLODIV_ n [DIV] to the desired values.
5. Wait for the PLL reference clock to be stable.
6. Write 0 to PLLCR[PLLPD].
7. Wait for PLLSR[LOCK] to be 1.
8. Write 1 to PLLODIV_ n [DE].
Perform the following steps to shut down PLL:
1. Write 0 to PLLODIV_n[DE] for all dividers.
2. Write 1 to PLLCR[PLLPD].

#### 29.6 PLLDIG register descriptions

This section provides the memory map and detailed descriptions of registers used for configuring PLL. The table below shows
the memory map. Addresses are given as offsets from the module base address. All registers are accessed using 8-bit, 16-bit, or
32-bit addressing.

#### 29.6.1 PLLDIG memory map

PLL base address: 402E_0000h
Offset Register Access Reset value
Width
(In bits)
0h PLL Control (PLLCR) 32 RW 8000_0000h
4h PLL Status (PLLSR) 32 RW 0000_0300h
Table continues on the next page...

---

*Page 756*

PLL Digital Interface (PLLDIG)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
8h PLL Divider (PLLDV) 32 RW 0C3F_1032h
Ch PLL Frequency Modulation (PLLFM) 32 RW 4000_0000h
10h PLL Fractional Divider (PLLFD) 32 RW 0000_0000h
18h PLL Calibration Register 2 (PLLCAL2) 32 RW 0006_0000h
80h - 84h PLL Output Divider (PLLODIV_0 - PLLODIV_1) 32 RW 0000_0000h

#### 29.6.2 PLL Control (PLLCR)

Offset
Register Offset
PLLCR 0h
Function
Configures PLL functionality.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
PLLPD
W
Reset 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 PLL Power Down
PLLPD Powers down or powers up PLL .
0b - Powered up
1b - Powered down
Table continues on the next page...

---

*Page 757*

PLL Digital Interface (PLLDIG)
Table continued from the previous page...
Field Function
Reserved
30-0
—

#### 29.6.3 PLL Status (PLLSR)

Offset
Register Offset
PLLSR 4h
Function
Shows the PLL status.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 Reserved 0 LOL LOCK 0
W W1C
Reset 0 0 0 0 0 0 1 1 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-11
—
Reserved
10-8
—
Reserved
7-4
—
3 Loss-Of-Lock Flag
LOL Indicates the current PLL lock status.
Table continues on the next page...

---

*Page 758*

PLL Digital Interface (PLLDIG)
Table continued from the previous page...
Field Function
0b - No loss of lock detected
1b - Loss of lock detected
2 Lock Status
LOCK Indicates that PLL has acquired lock.
0b - Unlocked
1b - Locked
Reserved
1-0
—

#### 29.6.4 PLL Divider (PLLDV)

Offset
Register Offset
PLLDV 8h
Function
Divides input clocks for PLL output generation.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
ODIV2 Reserved
W
Reset 0 0 0 0 1 1 0 0 0 0 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
RDIV MFI
W
Reset 0 0 0 1 0 0 0 0 0 0 1 1 0 0 1 0
Fields
Field Function
Reserved
31
—
Table continues on the next page...

---

*Page 759*

PLL Digital Interface (PLLDIG)
Table continued from the previous page...
Field Function
30-25 Output frequency divider for raw PLL clock.
ODIV2 6-bit field determining the VCO clock post divider for driving the PHI output clock.
000000 – Divide by 1
000001 – Divide by 1
000010 – Divide by 2
000011 – Divide by 3
000100 – Divide by 4
.......
111111 – Divide by 63
Reserved
24-22
—
Reserved
21-16
—
Reserved
15
—
14-12 Input Clock Predivider
RDIV Sets the input clock divider.
The output of the predivider circuit generates the PLL loop reference clock.
000b - Divide by 1
001b - Divide by 1
010b - Divide by 2
011b - Divide by 3
100b - Divide by 4
101b - Divide by 5
110b - Divide by 6
111b - Divide by 7
Reserved
11-8
—
7-0 Integer Portion Of Loop Divider
MFI Sets the value of the divider in the PLL feedback loop.
The value specified establishes the multiplication factor applied to the reference frequency. Write the divider
value to this field, where the chosen value does not violate VCO frequency specifications.

---

*Page 760*

PLL Digital Interface (PLLDIG)

#### 29.6.5 PLL Frequency Modulation (PLLFM)

Offset
Register Offset
PLLFM Ch
Function
Configures PLL frequency modulation parameters.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
SSCG SPRE
STEPSIZE
BYP ADC ...
W
Reset 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
STEPNO
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
30 Frequency Modulation (Spread Spectrum Clock Generation) Bypass
SSCGBYP Bypasses frequency modulation.
0b - Not bypassed
1b - Bypassed
29 Modulation Type Selection
SPREADCTL Indicates that the modulation is spread below the nominal frequency. You must write 1 to this field.
0b - Reserved
1b - Spread below nominal frequency
Reserved
28-26
—
25-16 Frequency Modulation Step Size
Table continues on the next page...

---

*Page 761*

PLL Digital Interface (PLLDIG)
Table continued from the previous page...
Field Function
Provides the step size for modulation depth and frequency in Frequency Modulation mode (see
STEPSIZE
Frequency modulation ).
Reserved
15-11
—
10-0 Number Of Steps Of Modulation Period Or Frequency Modulation
STEPNO Provides the number of steps to achieve modulation depth in Frequency Modulation mode (see
Frequency modulation ).

#### 29.6.6 PLL Fractional Divider (PLLFD)

Offset
Register Offset
PLLFD 10h
Function
Enables and configures frequency modulation.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
SDME Reserv
SDM2 SDM3 Reserved
N ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
MFN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
30 Fractional Mode Enable
SDMEN Enables Fractional mode.
Table continues on the next page...

---

*Page 762*

PLL Digital Interface (PLLDIG)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled
29 Fractional Mode Configuration
SDM2 When you are in the fractional mode (SDMEN = 1), write 1 to this field.
NOTE
If SDMEN = 1, this field must be written 1.
28 Fractional Mode Configuration
SDM3 When you are in the fractional mode (SDMEN = 1), write 1 to this field.
NOTE
If SDMEN = 1, this field must be written 1.
Reserved
27-22
—
Reserved
21-18
—
Reserved
17
—
Reserved
16
—
Reserved
15
—
14-0 Numerator Of Fractional Loop Division Factor
MFN Sets the numerator of the fractional loop division factor.
You must write a value of less than 18432 to this field. When Fractional mode is disabled, you must write
000_0000_0000_0000b to this field.

#### 29.6.7 PLL Calibration Register 2 (PLLCAL2)

Offset
Register Offset
PLLCAL2 18h

---

*Page 763*

PLL Digital Interface (PLLDIG)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
Reserved Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved ULKCTL Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-22
—
Reserved
21-19
—
Reserved
18-16
—
Reserved
15-9
—
8-7 Unlock Control Accuracy
ULKCTL Defines the accuracy necessary to achieve unlock.
The lock counter determines unlock if the number of VCO clock cycles in the window of reference cycles is
outside the number of cycles defined by this field.
00b - Unlock range = Expected value ± 9 (recommended when PLLFM[SSCGBYP] = 1). Unlock
range = Expected value ± 9 (recommended when PLLFM[SSCGBYP] = 1)
01b - Unlock range = Expected value ± 17 (recommended when PLLFM[SSCGBYP] = 0). Unlock
range = Expected value ± 17 (recommended when PLLFM[SSCGBYP] = 0)
10b - Unlock range = Expected value ± 33
11b - Unlock range = Expected value ± 5
Reserved
6-0
—

---

*Page 764*

PLL Digital Interface (PLLDIG)

#### 29.6.8 PLL Output Divider (PLLODIV_0 - PLLODIV_1)

Offset
Register Offset
PLLODIV_0 80h
PLLODIV_1 84h
Function
Controls the PLL output clock divider settings.
This divider has a 50% duty cycle.
NOTE
These registers support only word accesses. Other write accesses lead to the following:
• Unpredictable behavior
• No transfer error generated
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
DE DIV
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Divider Enable
DE Enables PLL output divider. Divider must be disabled before disabling PLL.
0b - Disabled
1b - Enabled
Reserved
30-20
—
19-16 Division Value
Table continues on the next page...

---

*Page 765*

PLL Digital Interface (PLLDIG)
Table continued from the previous page...
Field Function
Provides the division value for the output clock divider. The clock period of the clock after division is
DIV
DIV + 1 times the time period of the divider input clock.
Reserved
15-2
—
1-0 Reserved
— Do not write any value other than the reset value.

---

*Page 766*

