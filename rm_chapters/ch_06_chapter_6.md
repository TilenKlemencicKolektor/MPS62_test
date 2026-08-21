<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 6 -->

# Chapter 6

# Miscellaneous Control Module (MCM)

#### 6.1 Chip-specific MCM information

#### 6.1.1 MCM instances and configuration

This chip has one MCM instance.
Table 27. Memories
Memory / Variant MCXE315/MCXE316/MCXE317/MCXE31B (single core)
DTCM 64 KB
ITCM 32 KB
Icache 8 KB
Dcache 8 KB

#### 6.2 Overview

MCM provides miscellaneous control functions and contains local memory descriptors for the Cortex-M7 core. For more
information about core-related registers, see the Cortex-M7 core overview chapter.
NOTE
The terminology in this chapter has been updated to align with Arm's AMBA AHB Protocol Specification, as shown
in the table below.
Table 28. Updated terms
Updated term Deprecated term
Manager Master
Subordinate Slave

#### 6.2.1 Features

The MCM includes the following features:
• Program-visible information on the platform configuration and revision
• Floating Point Exception monitor and interrupt control
• Local memory descriptors:
— ITCM
— D0TCM
— D1TCM
— I-cache
— D-cache

#### 6.3 Functional description

---

*Page 44*

Miscellaneous Control Module (MCM)

#### 6.3.1 Interrupts

MCM generates an interrupt if any of the following are true:
• FPU input denormal interrupt is enabled (FIDCE) and an input is denormalized (FIDC).
• FPU inexact interrupt is enabled (FIXCE) and a number is inexact (FIXC).
• FPU underflow interrupt is enabled (FUFCE) and an underflow occurs (FUFC).
• FPU overflow interrupt is enabled (FOFCE) and an overflow occurs (FOFC).
• FPU divide-by-zero interrupt is enabled (FDZCE) and a divide-by-zero occurs (FDZC).
• FPU invalid operation interrupt is enabled (FIOCE) and an invalid operation occurs (FIOC).
• Write abort interrupt is enabled (WABE) and a write abort occurs (CORTEX-M7 WABORTS INDICATOR).
Determining interrupt source
To determine the exact source of the interrupt for Cortex-M7 core, qualify the interrupt status flags with the corresponding interrupt
enable fields.
• MCM_ISCR[31:16] && MCM_ISCR[15:0]
• Search the result for asserted flags, which indicate the exact interrupt sources.

#### 6.4 Memory map and register descriptions

The memory map and register descriptions below describe the registers using byte addresses.
NOTE
The following actions result in bus errors:
• Writing to read-only registers at 0x0.
• Reading from or writing to an address from offset 480h and above.
• Accessing any MCM register while in User mode. These registers are only accessible while in
Supervisor mode.

#### 6.4.1 MCM register descriptions

6.4.1.1 MCM memory map
MCM_0 base address: E008_0000h
Offset Register Access Reset value
Width
(In bits)
0h SoC-defined Platform Revision (PLREV) 16 R 0000h
2h Processor Core Type (PCT) 16 R AC70h
Ch Core Platform Control (CPCR) 32 RW 0000_0200h
10h Interrupt Status and Control (ISCR) 32 RW 0000_0000h
30h Process Identifier (PID) 8 RW 00h
400h Local Memory Descriptor 0 (LMEM_DESC_0) 32 R 8606_0000h
Table continues on the next page...

---

*Page 45*

Miscellaneous Control Module (MCM)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
404h - 408h Local Memory Descriptor a (LMEM_DESC_1 - LMEM_DESC_2) 32 R 8604_2000h
40Ch Local Memory Descriptor 3 (LMEM_DESC_3) 32 R 8426_4000h
410h Local Memory Descriptor 4 (LMEM_DESC_4) 32 R 8444_6000h
6.4.1.2 SoC-defined Platform Revision (PLREV)
Offset
Register Offset
PLREV 0h
Function
Specifies a chip-defined platform revision number. A platform input signal defines the state of this register; it can only be read from
the IPS programming model. Any attempted write is ignored.
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R PLREV
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Defines the software-visible revision number, specified by a platform input signal.
15-0
PLREV
6.4.1.3 Processor Core Type (PCT)
Offset
Register Offset
PCT 2h

---

*Page 46*

Miscellaneous Control Module (MCM)
Function
Specifies the architecture of the processor core within the platform on the chip. A module input signal defines the state of this
register, which can only be read from the IPS programming model. Any attempted write is ignored.
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R PCT
W
Reset 1 0 1 0 1 1 0 0 0 1 1 1 0 0 0 0
Fields
Field Function
15-0 Core Complex Identifier
PCT Identifies the core complex. This MCM design supports the Arm Cortex M7 core.
1010_1100_0111_0000b - Arm Cortex-M7
6.4.1.4 Core Platform Control (CPCR)
Offset
Register Offset
CPCR Ch
Function
Defines the arbitration and protection schemes for the two system RAM arrays.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
CM7_A
Reserved Reserved
HB ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
Reserv Reserv
R Reserved
ed ed
Reserved
W
Reset 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0

---

*Page 47*

Miscellaneous Control Module (MCM)
Fields
Field Function
Reserved
31-28
—
27 AHB Subordinate Priority
CM7_AHBSPRI Indicates the access priority on the AHBS port of the Cortex-M7.
NOTE
1
This setting has no effect unless enabled by AHBSCR[CTL] of the Cortex-M7 core.
0b - Uses a round-robin arbitration scheme
1b - AHB-subordinate access has priority over a core access
Reserved
26-11
—
Reserved
10
—
Reserved
9
—
Reserved
8-0
—
1. For more information see Cortex-M7 documentation: Arm Cortex-M7 Processor Technical Reference Manual at
www.arm.com.
6.4.1.5 Interrupt Status and Control (ISCR)
Offset
Register Offset
ISCR 10h
Function
Defines the configuration and reports the status for a number of core-related interrupt exception conditions. It includes:
• Enable and status fields associated with the core's floating-point exceptions
• Bus errors associated with the core's cache write buffer
The individual event indicators are first qualified with their exception enables, and then logically summed to form an interrupt
request sent to the core's NVIC.
Bits 15-8 are read-only indicator flags based on the processor's FPSCR register. Attempted writes to these fields are ignored.
When these flags are 1, they retain this value until software clears the corresponding FPSCR field. For more information see
Cortex-M7 documentation: Arm Cortex-M7 Processor Technical Reference Manual at www.arm.com.

---

*Page 48*

Miscellaneous Control Module (MCM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
FUFC FOFC FDZC
FIDCE FIXCE FIOCE WABE
E E E
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
WABS
R FIDC 0 FIXC FUFC FOFC FDZC FIOC 0 WABS 0
O
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
FPU Input Denormal Interrupt Enable
31
0b - Disable
FIDCE
1b - Enable
Reserved
30-29
—
FPU Inexact Interrupt Enable
28
0b - Disable
FIXCE
1b - Enable
FPU Underflow Interrupt Enable
27
0b - Disable
FUFCE
1b - Enable
FPU Overflow Interrupt Enable
26
0b - Disable
FOFCE
1b - Enable
FPU Divide-by-Zero Interrupt Enable
25
0b - Disable
FDZCE
1b - Enable
FPU Invalid Operation Interrupt Enable
24
0b - Disable
FIOCE
1b - Enable
Reserved
23-22
Table continues on the next page...

---

*Page 49*

Miscellaneous Control Module (MCM)
Table continued from the previous page...
Field Function
—
TCM Write Abort Interrupt Enable
21
0b - Disable
WABE
1b - Enable
Reserved
20-16
—
15 FPU Input Denormal Interrupt Status
FIDC Indicates that an input denormalized number has been detected in the processor's FPU. This field is a
copy of the core's FPSCR[IDC] field. When this field is 1, it retains this value until software clears the
FPSCR[IDC] field.
0b - No interrupt
1b - Interrupt occurred
Reserved
14-13
—
12 FPU Inexact Interrupt Status
FIXC Indicates that an inexact number has been detected in the processor's FPU. This field is a copy of the
core's FPSCR[IXC] field. When this field is 1, it retains this value until software clears the FPSCR[IXC]
field.
0b - No interrupt
1b - Interrupt occurred
11 FPU Underflow Interrupt Status
FUFC Indicates that an underflow has been detected in the processor's FPU. This field is a copy of the core's
FPSCR[UFC] field. When this field is 1, it retains this value until software clears the FPSCR[UFC] field.
0b - No interrupt
1b - Interrupt occurred
10 FPU Overflow Interrupt Status
FOFC Indicates that an overflow has been detected in the processor's FPU. This field is a copy of the core's
FPSCR[OFC] field. When this field is 1, it retains this value until software clears the FPSCR[OFC] field.
0b - No interrupt
1b - Interrupt occurred
9 FPU Divide-by-Zero Interrupt Status
FDZC Indicates that a divide-by-zero operation has been detected in the processor's FPU. This field is a
copy of the core's FPSCR[DZC] field. When this field is 1, it retains this value until software clears the
FPSCR[DZC] field.
Table continues on the next page...

---

*Page 50*

Miscellaneous Control Module (MCM)
Table continued from the previous page...
Field Function
0b - No interrupt
1b - Interrupt occurred
8 FPU Invalid Operation Interrupt Status
FIOC Indicates that an illegal operation has been detected in the processor's FPU. This field is a copy of the
core's FPSCR[IOC] field. When this field is 1, it retains this value until software clears the FPSCR[IOC]
field.
0b - No interrupt
1b - Interrupt occurred
Reserved
7
—
6 Write Abort on Subordinate Overrun
WABSO The overrun conditions are reported only if WABE=1.
0b - No write abort overrun
1b - Write abort overrun occurred
5 Write Abort on Subordinate
WABS Indicates when a write abort has occurred on the AHBS interface.
0b - No write abort occurred on AHBS interface
1b - Write abort occurred on AHBS interface
Reserved
4-0
—
6.4.1.6 Process Identifier (PID)
Offset
Register Offset
PID 30h
Function
Contains the CPU process ID.

---

*Page 51*

Miscellaneous Control Module (MCM)
Diagram
Bits 7 6 5 4 3 2 1 0
R
PID
W
Reset 0 0 0 0 0 0 0 0
Fields
Field Function
7-0 Process Identifier
PID Identifies the CPU process.
6.4.1.7 Local Memory Descriptor 0 (LMEM_DESC_0)
Offset
Register Offset
LMEM_DESC_0 400h
Function
NOTE
The DESC_a registers map to the LMEMs in this way:
• DESC_0: ITCM
• DESC_1: D0TCM
• DESC_2: D1TCM
• DESC_3: I-cache
• DESC_4: D-cache
NOTE
You can read and write to the reserved fields (instead of read as zero and write ignored). Writing to any of these
fields has no functional impact.

---

*Page 52*

Miscellaneous Control Module (MCM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
LMSZ
R LMV LMSZ WY DPW
Reserv
H
Reserved
ed
W
Reset 1 0 0 0 0 1 1 0 0 0 0 0 0 1 1 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R MT Reserved
Reserved Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Local Memory Valid
LMV This read-only field defines the validity (presence) of the local memory.
0b - LMEM n not present
1b - LMEM n present
Reserved
30-29
—
28 LMEM Size Hole
LMSZH Used for local memories that are not fully populated (that is, local memories that include a memory "hole"
in the upper 25 % of the address range).
0b - LMEM n is a power-of-2 capacity
1b - LMEM n is not a power-of-2, with capacity of 0.75 × LMSZ
Local Memory Size
27-24
0000b - 0 KB
LMSZ
0001b - 1 KB
0010b - 2 KB
0011b - 4 KB
0100b - 8 KB
0101b - 16 KB
0110b - 32 KB
0111b - 64 KB
1000b - 128 KB
1001b - 256 KB
Table continues on the next page...

---

*Page 53*

Miscellaneous Control Module (MCM)
Table continued from the previous page...
Field Function
1010b - 512 KB
1011b - 1024 KB
1100b - 2048 KB
1101b - 4096 KB
1110b - 8192 KB
1111b - 16384 KB
Level 1 Cache Ways
23-20
0000b - No cache
WY
0010b - 2-way set associative
0100b - 4-way set associative
19-17 Data Path Width
DPW Defines the LMEM n data path width, which is the width of the local memory.
000b-001b - Reserved
010b - 32 bits
011b - 64 bits
100b-111b - Reserved
Reserved
16
—
Memory Type
15-13
000b - ITCM
MT
001b - DTCM
010b - I-cache
011b - D-cache
Reserved
12-4
—
Reserved
3-2
—
Reserved
1-0
—

---

*Page 54*

Miscellaneous Control Module (MCM)
6.4.1.8 Local Memory Descriptor a (LMEM_DESC_1 - LMEM_DESC_2)
Offset
Register Offset
LMEM_DESC_1 404h
LMEM_DESC_2 408h
Function
NOTE
The DESC_a registers map to the LMEMs in this way:
• DESC_0: ITCM
• DESC_1: D0TCM
• DESC_2: D1TCM
• DESC_3: I-cache
• DESC_4: D-cache
NOTE
You can read and write to the reserved fields (instead of read as zero and write ignored). Writing to any of these
fields has no functional impact.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
LMSZ
R LMV LMSZ WY DPW
Reserv
H
Reserved
ed
W
Reset 1 0 0 0 0 1 1 0 0 0 0 0 0 1 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R MT Reserved
Reserved Reserved
W
Reset 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Local Memory Valid
LMV This read-only field defines the validity (presence) of the local memory.
0b - LMEM n not present
1b - LMEM n present
Table continues on the next page...

---

*Page 55*

Miscellaneous Control Module (MCM)
Table continued from the previous page...
Field Function
Reserved
30-29
—
28 LMEM Size Hole
LMSZH Used for local memories that are not fully populated (that is, local memories that include a memory "hole"
in the upper 25 % of the address range).
0b - LMEM n is a power-of-2 capacity
1b - LMEM n is not a power-of-2, with capacity of 0.75 × LMSZ
Local Memory Size
27-24
0000b - 0 KB
LMSZ
0001b - 1 KB
0010b - 2 KB
0011b - 4 KB
0100b - 8 KB
0101b - 16 KB
0110b - 32 KB
0111b - 64 KB
1000b - 128 KB
1001b - 256 KB
1010b - 512 KB
1011b - 1024 KB
1100b - 2048 KB
1101b - 4096 KB
1110b - 8192 KB
1111b - 16384 KB
Level 1 Cache Ways
23-20
0000b - No cache
WY
0010b - 2-way set associative
0100b - 4-way set associative
19-17 Data Path Width
DPW Defines the LMEM n data path width, which is the width of the local memory.
000b-001b - Reserved
010b - 32 bits
Table continues on the next page...

---

*Page 56*

Miscellaneous Control Module (MCM)
Table continued from the previous page...
Field Function
011b - 64 bits
100b-111b - Reserved
Reserved
16
—
Memory Type
15-13
000b - ITCM
MT
001b - DTCM
010b - I-cache
011b - D-cache
Reserved
12-4
—
Reserved
3-2
—
Reserved
1-0
—
6.4.1.9 Local Memory Descriptor 3 (LMEM_DESC_3)
Offset
Register Offset
LMEM_DESC_3 40Ch
Function
NOTE
The DESC_a registers map to the LMEMs in this way:
• DESC_0: ITCM
• DESC_1: D0TCM
• DESC_2: D1TCM
• DESC_3: I-cache
• DESC_4: D-cache
NOTE
You can read and write to the reserved fields (instead of read as zero and write ignored). Writing to any of these
fields has no functional impact.

---

*Page 57*

Miscellaneous Control Module (MCM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
LMSZ
R LMV LMSZ WY DPW
Reserv
H
Reserved
ed
W
Reset 1 0 0 0 0 1 0 0 0 0 1 0 0 1 1 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R MT Reserved
Reserved Reserved
W
Reset 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Local Memory Valid
LMV This read-only field defines the validity (presence) of the local memory.
0b - LMEM n not present
1b - LMEM n present
Reserved
30-29
—
28 LMEM Size Hole
LMSZH Used for local memories that are not fully populated (that is, local memories that include a memory "hole"
in the upper 25 % of the address range).
0b - LMEM n is a power-of-2 capacity
1b - LMEM n is not a power-of-2, with capacity of 0.75 × LMSZ
Local Memory Size
27-24
0000b - 0 KB
LMSZ
0001b - 1 KB
0010b - 2 KB
0011b - 4 KB
0100b - 8 KB
0101b - 16 KB
0110b - 32 KB
0111b - 64 KB
1000b - 128 KB
1001b - 256 KB
Table continues on the next page...

---

*Page 58*

Miscellaneous Control Module (MCM)
Table continued from the previous page...
Field Function
1010b - 512 KB
1011b - 1024 KB
1100b - 2048 KB
1101b - 4096 KB
1110b - 8192 KB
1111b - 16384 KB
Level 1 Cache Ways
23-20
0000b - No cache
WY
0010b - 2-way set associative
0100b - 4-way set associative
19-17 Data Path Width
DPW Defines the LMEM n data path width, which is the width of the local memory.
000b-001b - Reserved
010b - 32 bits
011b - 64 bits
100b-111b - Reserved
Reserved
16
—
Memory Type
15-13
000b - ITCM
MT
001b - DTCM
010b - I-cache
011b - D-cache
Reserved
12-4
—
Reserved
3-2
—
Reserved
1-0
—

---

*Page 59*

Miscellaneous Control Module (MCM)
6.4.1.10 Local Memory Descriptor 4 (LMEM_DESC_4)
Offset
Register Offset
LMEM_DESC_4 410h
Function
NOTE
The DESC_a registers map to the LMEMs in this way:
• DESC_0: ITCM
• DESC_1: D0TCM
• DESC_2: D1TCM
• DESC_3: I-cache
• DESC_4: D-cache
NOTE
You can read and write to the reserved fields (instead of read as zero and write ignored). Writing to any of these
fields has no functional impact.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
LMSZ
R LMV LMSZ WY DPW
Reserv
H
Reserved
ed
W
Reset 1 0 0 0 0 1 0 0 0 1 0 0 0 1 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R MT Reserved
Reserved Reserved
W
Reset 0 1 1 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Local Memory Valid
LMV This read-only field defines the validity (presence) of the local memory.
0b - LMEM n not present
1b - LMEM n present
Reserved
30-29
Table continues on the next page...

---

*Page 60*

Miscellaneous Control Module (MCM)
Table continued from the previous page...
Field Function
—
28 LMEM Size Hole
LMSZH Used for local memories that are not fully populated (that is, local memories that include a memory "hole"
in the upper 25 % of the address range).
0b - LMEM n is a power-of-2 capacity
1b - LMEM n is not a power-of-2, with capacity of 0.75 × LMSZ
Local Memory Size
27-24
0000b - 0 KB
LMSZ
0001b - 1 KB
0010b - 2 KB
0011b - 4 KB
0100b - 8 KB
0101b - 16 KB
0110b - 32 KB
0111b - 64 KB
1000b - 128 KB
1001b - 256 KB
1010b - 512 KB
1011b - 1024 KB
1100b - 2048 KB
1101b - 4096 KB
1110b - 8192 KB
1111b - 16384 KB
Level 1 Cache Ways
23-20
0000b - No cache
WY
0010b - 2-way set associative
0100b - 4-way set associative
19-17 Data Path Width
DPW Defines the LMEM n data path width, which is the width of the local memory.
000b-001b - Reserved
010b - 32 bits
011b - 64 bits
100b-111b - Reserved
Table continues on the next page...

---

*Page 61*

Miscellaneous Control Module (MCM)
Table continued from the previous page...
Field Function
Reserved
16
—
Memory Type
15-13
000b - ITCM
MT
001b - DTCM
010b - I-cache
011b - D-cache
Reserved
12-4
—
Reserved
3-2
—
Reserved
1-0
—

#### 6.5 Glossary

ITCM Instruction Tightly-Coupled Memory
DTCM Data Tightly Coupled Memory
I-cache Instruction Cache Memory
D-cache Data Cache Memory

---

*Page 62*

