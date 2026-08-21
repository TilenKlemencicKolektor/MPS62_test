<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 17 -->

# Chapter 17

# Crossbar Integrity Checker (XBIC)

#### 17.1 Chip-specific XBIC information

#### 17.1.1 XBIC instances and configuration

This chip has up to five instances of XBIC. The following tables describes the instances and their configuration.
Table 54. XBIC instances
Instance MCXE31B MCXE315/
MCXE316/MCXE317
XBIC_0 Yes Yes
XBIC_1 Yes No
XBIC_2 Yes No
XBIC_3 Yes No
XBIC_4 No No
Table 55. XBIC configuration for MCXE31B
Instance Available on crossbar Master and slave assignments
XBIC_0 AXBS_0 (main)
Master Master module Slave Slave module
port port
M0 Cortex-M7_0 AHBM S0 Flash memory port 0
M1 AXBS_2 S0 S1 Flash memory port 1
M2 ELE_HSEB S2 PRAM_0
M3 EMAC S3 Cortex-M7 TCM
1
- - S4 Flash memory Port 2
S5 QuadSPI
S6 PRAM_1
XBIC_1 AXBS_1 (peripheral)
Master Master module Slave Slave module
port port
M0 Cortex-M7_0 AHBP S0 AIPS_0
M1 AXBS_2 S1 S1 AIPS_1
M2 ELE_HSEB S2 AIPS_2
1
M3 Cortex-M7_1 AHBP
1. These ports are reserved for MCXE31B.

---

*Page 384*

Crossbar Integrity Checker (XBIC)
Table 55. XBIC configuration for MCXE31B (continued)
Instance Available on crossbar Master and slave assignments
XBIC_2 AXBS_2 (eDMA)
Master Master module Slave Slave module
port port
M0 eDMA S0 AXBS_0 M1
S1 AXBS_1 M1
XBIC_3 AXBS_3 (Cortex-
M7 TCM)
Master Master module Slave Slave module
port port
M0 AXBS_0 S3 S0 Cortex-M7_0 TCM
1
S1 Cortex-M7_1 TCM
Table 56. XBIC configuration for MCXE317
Instance Available on crossbar Master and slave assignments
XBIC_0 AXBS_0 (main)
Master Master module Slave Slave module
port port
M0 Cortex-M7_0 AHBM S0 Flash memory port 0
M1 eDMA S1 Flash memory port 1
M2 ELE_HSEB S2 PRAM_0
M3 Cortex-M7_0 AHBP S3 Cortex-M7 TCM
M4 Reserved S4 AIPS_0
S5 AIPS_1
The errors detected are connected to FCCU. See the memory map file attached to this document for details.

#### 17.1.2 Target master IDs

See "Chip-specific XRDC information" for master IDs.

#### 17.2 Overview

XBIC is a safety module that verifies the integrity of crossbar transfers.

#### 17.2.1 Block diagram

The chip routes crossbar transfer attribute information for all mapped initiator and target ports to XBIC, which calculates and
checks the EDC value of the attribute information, as shown in the following diagram.

---

*Page 385*

Crossbar Integrity Checker (XBIC)
Initiator 0 Initiator 1 Initiator 7
Initiator data phase output
Initiator attribute input
Attribute EDC output
m0 m1 ... m7 m0 m1 ... m7
XBIC Crossbar
s0 s1 ... s7 s0 s1 ... s7
Attribute EDC input
Target attribute input
Target data phase output
Error out
to FCCU
Target 0 Target 1 Target 7
Figure 37. XBIC system block diagram
The above figure illustrates one of many possible XBIC and crossbar configurations. See the Chip-specific XBIC information
section for actual port mappings.

#### 17.2.2 Features

XBIC includes the following features:
[1] , [2]
• Verification of attribute information for crossbar transfers
— EDC detects single-bit and double-bit errors
• Verification of feedback information for each data phase during crossbar transfers
• Error injection for testing
— Programmable initiator and target port specifiers
— Programmable 8-bit toggle vector to insert error in initiator EDC value
— Address, EDC syndrome, initiator, and target port information are captured when error is detected
• Crossbar transfer attribute integrity check programmable on a per-target-port basis
• Feedback integrity check programmable on a per-initiator-port basis

#### 17.3 Functional description

XBIC verifies the integrity of the crossbar interface on an individual port basis according to the configuration specified via the MCR
register. When XBIC detects an error, it reports relevant information and sends an error signal to the FCCU module, but does
[1] See Table 57 for a list of crossbar attribute signals verified.
[2] The chip verifies read and write data separately, via the end-to-end ECC architecture.

---

*Page 386*

Crossbar Integrity Checker (XBIC)
not generate a bus error. XBIC integrity checking is independent of end-to-end ECC, which monitors the integrity of the transfer
address and data.
During the address phase of a transfer, XBIC verifies the crossbar attribute information using an 8-bit EDC, which detects any
single-bit or double-bit errors. When XBIC detects an error (an attribute integrity error), due to either hardware fault or error
injection, it reports information related to the error in ESR and EAR .
During the data phase of a data transfer, XBIC verifies the integrity of response signals from target to intiator as they pass through
the crossbar. When XBIC detects an error in the response signals (a feedback integrity error), it reports the XBIC target and intiator
ports in ESR[DPSE0] - ESR[DPSE7] and ESR[DPME0] - ESR[DPME7] , respectively.
During the data phase, XBIC sends an alarm to the FCCU module if a intiator port attempts back-to-back accesses in which:
1. The first access terminates normally.
2. The second attempts access to an address space not mapped to any target on the crossbar.
The resultant 'absent target error' causes the crossbar to generate a bus error response to the requesting intiator. XBIC detects
the bus error response as a difference because the bus error did not originate from the target port.
You can program XBIC to inject EDC errors for testing purposes. Error injection targets a single target port and a single intiator
port, as specified by the configuration settings in the EIR register. When XBIC inserts an error, it changes the EDC syndrome,
causing the XBIC to assert an error indication to the FCCU module. Otherwise, transfers are unaffected by error injection. This
enables verification of the check logic without compromising the integrity of the data transfer. After you enable error injection
function by writing 1 to EIR[EIE] , XBIC induces errors on all subsequent targeted transactions until you write a 0 to the field. After
the FCCU error indication asserts, it remains asserted even after you write 0 to EIR[EIE] . The error indication deasserts after
FCCU specifically clears the existing error. After FCCU clears the XBIC error, additional error injection testing can continue.
To trace a fault reported by the XBIC to the FCCU:
1. Note the error reported by the FCCU. For example, "NCF[46]".
2. Locate the source module and error description in the attached fault mapping spreadsheet. For example:
• Channel number: NCF[46]
• Source module: AXBS_1 integrity checker
• Description: Instruction crossbar error indication to FCCU if syndrome calculated using EDC on the data is not zero
3. Refer to the Chip-specific XBIC information section for the source XBIC module to determine the specific XBIC module
instanceâ€”"XBIC_1", for example.
4. Determine the type of error (attribute integrity error or feedback integrity error) and the XBIC port(s) involved by reading the
information reported in the ESR register of the reported XBIC instance.
5. For attribute integrity check errors, read the XBIC EAR register for the target address of the requested transfer.
6. Refer to the Chip-specific XBIC information section and possibly the Chip-specific AXBS information section for port
mapping of XBIC ports to AXBS ports.
Decode a single-bit error syndrome value reported in ESR[SYN] by finding the value in the following table. Any syndrome value
not included in the table indicates a multi-bit error.
Table 57. Hexadecimal attribute single-bit error syndromes
Signal SYN Signal SYN Signal SYN Signal SYN
hwrite 07 hbstrb[7] 70 hdecor[31] 25 hdecor[15] 23
htrans[0] 0b hbstrb[6] 32 hdecor[30] 68 hdecor[14] 51
hsize[2] 0d hbstrb[5] 52 hdecor[29] c7 hdecor[13] 54
Table continues on the next page...

---

*Page 387*

Crossbar Integrity Checker (XBIC)
Table 57. Hexadecimal attribute single-bit error syndromes (continued)
Signal SYN Signal SYN Signal SYN Signal SYN
hsize[1] 0e hbstrb[4] a8 hdecor[28] 83 hdecor[12] 61
hsize[0] 13 hbstrb[3] 43 hdecor[27] 85 hdecor[11] e3
hprot[5] 15 hbstrb[2] 45 hdecor[26] 86 hdecor[10] e6
hprot[4] 16 hbstrb[1] 4c hdecor[25] 89 hdecor[9] f8
hprot[3] 19 hbstrb[0] a4 hdecor[24] 8a hdecor[8] 38
hprot[2] 1a hmaster[3] a2 hdecor[23] 8c hdecor[7] 58
hprot[1] 1c hmaster[2] b0 hdecor[22] 49 hdecor[6] 37
hprot[0] 91 hmaster[1] c1 hdecor[21] 92 hdecor[5] f1
hburst[2] a1 hmaster[0] c2 hdecor[20] 94 hdecor[4] 3b
hburst[1] 64 hslave[2] c4 hdecor[19] 98 hdecor[3] 3d
hburst[0] 29 hslave[1] c8 hdecor[18] 46 hdecor[2] 3e
hmastlock 2a hslave[0] d0 hdecor[17] 34 hdecor[1] 4f
hunalign 2c hdecorated e0 hdecor[16] 4a hdecor[0] 6e
edc[7] 80 edc[6] 40 edc[5] 20 edc[4] 10
edc[3] 08 edc[2] 04 edc[1] 02 edc[0] 01

#### 17.3.1 Interrupts

This module has no interrupts.

#### 17.4 External signals

XBIC has no external interface signals.

#### 17.5 Initialization

This module does not require initialization.

#### 17.6 XBIC register descriptions

The XBIC programming model consists of four 32-bit registers. Software can access this model only in supervisor mode using
32-bit (word) accesses. Each of the following generates a transfer error back to the requesting intiator. Such errors could cause
core exceptions apart from other problems.
• Access size other than 32-bit
• Access to an undefined (reserved) address
• Access in user mode

---

*Page 388*

Crossbar Integrity Checker (XBIC)

#### 17.6.1 XBIC memory map

XBIC_AXBS base address: 4020_4000h
XBIC_AXBS_PERI base address: 4020_8000h
XBIC_AXBS_TCM base address: 4040_0000h
XBIC_AXBS_eDMA base address: 4040_4000h
Offset Register Access Reset value
Width
(In bits)
0h XBIC Module Control (MCR) 32 RW FFFF_0000h
4h XBIC Error Injection Attributes (EIR) 32 RW 0000_0000h
8h XBIC Error Status Attributes (ESR) 32 RW 0000_0000h
Ch XBIC Error Address (EAR) 32 R 0000_0000h

#### 17.6.2 XBIC Module Control (MCR)

Offset
Register Offset
MCR 0h
Function
Use this register to turn attribute integrity checking and feedback integrity checking on or off on a per-port basis.
• Turn on attribute integrity checking on one or more XBIC target ports by ensuring that the associated SE n field(s) have
a value of 1. For example, setting field SE 0 enables attribute integrity checking for target port 0. The default (reset)
behavior is for attribute integrity checking to be performed for all target ports. XBIC performs EDC-based checks on all
transfer requests targeting the selected target port(s). The signals verified are transfer attribute signals going from initiator
to target. When XBIC detects an attribute integrity error, it reports relevant information in the ESR and EAR registers.
• Turn on feedback integrity checking on one or more XBIC initiator ports by ensuring that the associated ME n field(s) have
a value of 1. For example, setting field ME 0 to 1 enables feedback integrity checking for initiator port 0. The default (reset)
behavior is for feedback integrity checking to be performed for all initiator ports. XBIC checks target-to-initiator feedback
signals for transfer requests originating from the selected XBIC initiator port(s). If any feedback signal value is different at
the initiator and target ports during the data phase, XBIC reports the relevant initiator and target ports in the ESR register.
Each field in this register references a specific initiator or target port using XBIC port numbering. Referring to Figure 37 , "target
port 0" refers to XBIC port "s0" in the figure, "target port 1" refers to port "s1", and so on. Similarly, "initiator port 0" refers to XBIC
port "m0", "initiator port 1" refers to port "m1", and so on. See the "Chip-specific XBIC information" section in this document for the
mapping of XBIC instances to AXBS instances and XBIC ports to AXBS ports. See the "Chip-specific AXBS information" section
in this document for the device component(s) mapped to each port of an AXBS instance.

---

*Page 389*

Crossbar Integrity Checker (XBIC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SE0 SE1 SE2 SE3 SE4 SE5 SE6 SE7 ME0 ME1 ME2 ME3 ME4 ME5 ME6 ME7
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
target port EDC Error Detection Enable
31
0b - Attribute integrity checking disabled for target port 0
SE0
1b - Attribute integrity checking enabled for target port 0
target port EDC Error Detection Enable
30
0b - Attribute integrity checking disabled for target port 1
SE1
1b - Attribute integrity checking enabled for target port 1
target port EDC Error Detection Enable
29
0b - Attribute integrity checking disabled for target port 2
SE2
1b - Attribute integrity checking enabled for target port 2
target port EDC Error Detection Enable
28
0b - Attribute integrity checking disabled for target port 3
SE3
1b - Attribute integrity checking enabled for target port 3
target port EDC Error Detection Enable
27
0b - Attribute integrity checking disabled for target port 4
SE4
1b - Attribute integrity checking enabled for target port 4
target port EDC Error Detection Enable
26
0b - Attribute integrity checking disabled for target port 5
SE5
1b - Attribute integrity checking enabled for target port 5
target port EDC Error Detection Enable
25
0b - Attribute integrity checking disabled for target port 6
SE6
1b - Attribute integrity checking enabled for target port 6
target Port EDC Error Detection Enable
24
Table continues on the next page...

---

*Page 390*

Crossbar Integrity Checker (XBIC)
Table continued from the previous page...
Field Function
SE7 0b - Attribute integrity checking disabled for target port 7
1b - Attribute integrity checking enabled for target port 7
initiator Port Enable For Feedback Integrity Check
23
0b - Feedback integrity checking disabled for initiator port 0
ME0
1b - Feedback integrity checking enabled for initiator port 0
initiator Port Enable For Feedback Integrity Check
22
0b - Feedback integrity checking disabled for initiator port 1
ME1
1b - Feedback integrity checking enabled for initiator port 1
initiator Port Enable For Feedback Integrity Check
21
0b - Feedback integrity checking disabled for initiator port 2
ME2
1b - Feedback integrity checking enabled for initiator port 2
initiator Port Enable For Feedback Integrity Check
20
0b - Feedback integrity checking disabled for initiator port 3
ME3
1b - Feedback integrity checking enabled for initiator port 3
initiator Port Enable For Feedback Integrity Check
19
0b - Feedback integrity checking disabled for initiator port 4
ME4
1b - Feedback integrity checking enabled for initiator port 4
initiator Port Enable For Feedback Integrity Check
18
0b - Feedback integrity checking disabled for initiator port 5
ME5
1b - Feedback integrity checking enabled for initiator port 5
initiator Port Enable For Feedback Integrity Check
17
0b - Feedback integrity checking disabled for initiator port 6
ME6
1b - Feedback integrity checking enabled for initiator port 6
initiator Port Enable For Feedback Integrity Check
16
0b - Feedback integrity checking disabled for initiator port 7
ME7
1b - Feedback integrity checking enabled for initiator port 7
Reserved
15-0
—

---

*Page 391*

Crossbar Integrity Checker (XBIC)

#### 17.6.3 XBIC Error Injection Attributes (EIR)

Offset
Register Offset
EIR 4h
Function
Use this register to configure the XBIC error injection function and turn it on or off. When enabled, the XBIC error injection
function inserts an attribute integrity error when the targeted initiator requests a transaction of the targeted target port. The
inserted error changes the calculated EDC syndrome value, causing XBIC to:
• Capture transfer information in the ESR and EAR registers
• Assert an error signal to the FCCU module
Otherwise, XBIC error injection does not affect transfers.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
EIE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
SLV MST SYN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Error Injection Enable
31
0b - Error injection disabled
EIE
1b - Error injection enabled
Reserved
30-15
—
14-12 Target Port
SLV Specifies the target port for error injection—other target ports are unaffected.
Specify the target port by its XBIC target port number (0–7). See the "Chip-specific XBIC information"
section in this document for the mapping of XBIC instances to AXBS instances and XBIC ports to AXBS
ports. See the "Chip-specific AXBS information" section in this document for the device component(s)
mapped to each port of an AXBS instance.
Table continues on the next page...

---

*Page 392*

Crossbar Integrity Checker (XBIC)
Table continued from the previous page...
Field Function
11-8 Target initiator ID
MST Specifies the target initiator port for error injection—transfers with other intiators are unaffected.
Specify the target initiator port using the logical initiator ID number of the target bus initiator. See the "Chip-
specific XBIC information" section in this document for the initiator IDs and their corresponding components.
7-0 Syndrome
SYN XBIC performs an exclusive OR operation on the value in this field and the calculated syndrome, to generate
an error with the specified syndrome. A value of zero does not generate an error. See Table 57 for a list of
transfer attribute single-bit error syndromes, noting that the values given in the table are hexadecimal.

#### 17.6.4 XBIC Error Status Attributes (ESR)

Offset
Register Offset
ESR 8h
Function
In this register, XBIC reports information about the most recent transfer with an error detected. If XBIC detects an attribute integrity
check error, it reports:
• The target port identifier ( SLV )
• The initiator port identifier ( MST ) and the error syndrome ( SYN )
If XBIC detects a mismatch among feedback signals during the data phase:
• The DPSE0 - DPSE7 field with a value of 1 indicates the XBIC target port. In the DPSE0-DPSE7 field descriptions, the target
port number refers to the XBIC target port number. Referring to Figure 37 , "target port 0" refers to XBIC port "s0" in the figure,
"target port 1" refers to port "s1", and so on. See the "Chip-specific XBIC information" section in this document for the mapping
of XBIC instances to AXBS instances and XBIC ports to AXBS ports. See the "Chip-specific AXBS information" section in this
document for the device component(s) mapped to each port of an AXBS instance.
• The DPME0 - DPME7 field with a value of 1 indicates the XBIC initiator port. In the DPME0-DPME7 field descriptions, the
initiator port number refers to the XBIC initiator port number. Referring to Figure 37 , "initiator port 0" refers to XBIC port "m0" in
the figure, "initiator port 1" refers to port "m1", and so on. See the "Chip-specific XBIC information" section in this document for
the mapping of XBIC instances to AXBS instances and XBIC ports to AXBS ports. See the "Chip-specific AXBS information"
section in this document for the device component(s) mapped to each port of an AXBS instance.
XBIC sets this register to all 0s only on reset.

---

*Page 393*

Crossbar Integrity Checker (XBIC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DPSE DPSE DPSE DPSE DPSE DPSE DPSE DPSE DPME DPME DPME DPME DPME DPME DPME
R VLD
0 1 2 3 4 5 6 7 0 1 2 3 4 5 6
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
DPME
R SLV MST SYN
7
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Error Status Valid
VLD 0b - No error detected—other fields of the ESR and EAR registers are invalid
1b - Error detected—all fields of the ESR and EAR registers are valid
Data Phase target Port Error
30
0b - No feedback integrity error detected on target port 0
DPSE0
1b - Feedback integrity error detected on target port 0
Data Phase target Port Error
29
0b - No feedback integrity error detected on target port 1
DPSE1
1b - Feedback integrity error detected on target port 1
Data Phase target Port Error
28
0b - No feedback integrity error detected on target port 2
DPSE2
1b - Feedback integrity error detected on target port 2
Data Phase target Port Error
27
0b - No feedback integrity error detected on target port 3
DPSE3
1b - Feedback integrity error detected on target port 3
Data Phase target Port Error
26
0b - No feedback integrity error detected on target port 4
DPSE4
1b - Feedback integrity error detected on target port 4
Data Phase target Port Error
25
0b - No feedback integrity error detected on target port 5
DPSE5
1b - Feedback integrity error detected on target port 5
Table continues on the next page...

---

*Page 394*

Crossbar Integrity Checker (XBIC)
Table continued from the previous page...
Field Function
Data Phase target Port Error
24
0b - No feedback integrity error detected on target port 6
DPSE6
1b - Feedback integrity error detected on target port 6
Data Phase target Port Error
23
0b - No feedback integrity error detected on target port 7
DPSE7
1b - Feedback integrity error detected on target port 7
Data Phase initiator Port Error
22
0b - No feedback integrity error detected on initiator port 0
DPME0
1b - Feedback integrity error detected on initiator port 0
Data Phase initiator Port Error
21
0b - No feedback integrity error detected on initiator port 1
DPME1
1b - Feedback integrity error detected on initiator port 1
Data Phase initiator Port Error
20
0b - No feedback integrity error detected on initiator port 2
DPME2
1b - Feedback integrity error detected on initiator port 2
Data Phase initiator Port Error
19
0b - No feedback integrity error detected on initiator port 3
DPME3
1b - Feedback integrity error detected on initiator port 3
Data Phase initiator Port Error
18
0b - No feedback integrity error detected on initiator port 4
DPME4
1b - Feedback integrity error detected on initiator port 4
Data Phase initiator Port Error
17
0b - No feedback integrity error detected on initiator port 5
DPME5
1b - Feedback integrity error detected on initiator port 5
Data Phase initiator Port Error
16
0b - No feedback integrity error detected on initiator port 6
DPME6
1b - Feedback integrity error detected on initiator port 6
Data Phase initiator Port Error
15
0b - No feedback integrity error detected on initiator port 7
DPME7
1b - Feedback integrity error detected on initiator port 7
14-12 target Port
SLV target port targeted by the most recent transfer with an attribute integrity check error detected.
Table continues on the next page...

---

*Page 395*

Crossbar Integrity Checker (XBIC)
Table continued from the previous page...
Field Function
The value in this field is the XBIC target port number (0–7). See the "Chip-specific XBIC information" section
in this document for the mapping of XBIC instances to AXBS instances and XBIC ports to AXBS ports. See
the "Chip-specific AXBS information" section in this document for the device component(s) mapped to each
port of an AXBS instance.
11-8 initiator ID
MST initiator port that requested the most recent transfer with an attribute integrity check error detected.
The value in this field is the logical initiator ID number of the bus initiator. See the "Chip-specific XBIC
information" section in this document for the initiator IDs and their corresponding components.
7-0 Syndrome
SYN Syndrome calculated for the most recent transfer with an attribute integrity check error detected.
For single-bit errors, identify the signal in error by matching the SYN value in Table 57 , noting that the
syndrome (SYN) values in the table are hexadecimal.

#### 17.6.5 XBIC Error Address (EAR)

Offset
Register Offset
EAR Ch
Function
In this register, XBIC reports the address of the most recent transfer with an attribute integrity check error detected—either
because of a hardware fault or error injection. XBIC sets this register to all 0s only on reset.
NOTE
An attempted write to this read-only register results in a transfer error.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R ADDR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R ADDR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 396*

Crossbar Integrity Checker (XBIC)
Fields
Field Function
31-0 Error Address
ADDR Address of the most recent transfer with an attribute integrity check error detected.

#### 17.7 Glossary

EDC Error Detection Code
hdecor[31:0] Non-standard AHB address phase signal for transporting optional decorated storage instruction information
hdecorated Non-standard AHB address phase signal for transporting optional decorated storage instruction information

---

*Page 397*

