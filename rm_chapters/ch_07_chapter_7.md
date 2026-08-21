<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 7 -->

# Chapter 7

# Miscellaneous System Control Module (MSCM)

#### 7.1 Chip-specific MSCM information

#### 7.1.1 MSCM instance

This chip has one MSCM instance.
NOTE
The XN_CTRL register is used to restrict execution from SRAM, including TCMs and their backdoors,
which will be permanent until next device reset, while still allowing data R/W.
XN_CTRL register is reserved for MCXE31B.

#### 7.2 Introduction

MSCM contains registers for:
• CPU configuration
• On-chip memory control
• Interrupt router control
• Message-based interrupt configuration
• Virtual management

#### 7.2.1 Features

• Software-accessible processor core configuration information
• Support for interrupt router control
• Support for message-based interrupt configuration

#### 7.3 Functional description

MSCM provides information of the system cores and can identify the core that is running currently.

#### 7.3.1 MSI routing

MSIs are interrupts that are indirectly broadcast to a target core by writing configuration bits in MSCM. These MSIs can be
initiated by one core targeting another core in the system (known as core-to-core interrupts). These MSIs are initiated via writes
to Interrupt Router CPn Interrupt Generation (IRCP0IGR0 - IRCP1IGR3) and managed through Interrupt Router CPn Interrupt
Status (IRCP0ISR0 - IRCP1ISR3) , where n indicates the logical core number (0-1) and m represents the interrupt number (0-3).
CM7_0 core can support up to four outstanding core-to-core interrupts.

---

*Page 63*

Miscellaneous System Control Module (MSCM)
CPn Core-to-Core Int0 CPn Core-to-Core Int1
IRCP nlGR0 IRCP nlGR1
int_en int_en
IRCP nlSR0 IRCP nlSR1
cp1_int cp0_int cp1_int cp0_int
CPn Core-to-Core Int2 CPn Core-to-Core Int3
IRCP nlGR2 IRCP nlGR3
int_en int0_en
IRCP nlSR2 IRCP nlSR3
cp1_int cp0_int cp1_int cp0_int
Figure 11. IRCP n IGR m /IRCP n ISR m pairs for one core
CPn Core-to-Core Int0 CPn Core-to-Core Int1
IRCP nlGR0 IRCP nlGR1
int_en int_en
IRCP nlSR0 IRCP nlSR1
cp1_int cp0_int cp1_int cp0_int
CPn Core-to-Core Int2 CPn Core-to-Core Int3
IRCP nlGR2 IRCP nlGR3
int_en int0_en
IRCP nlSR2 IRCP nlSR3
cp1_int cp0_int cp1_int cp0_int
CP0
CP1
Figure 12. IRCP n IGR m /IRCP n ISR m pairs per core
7.3.1.1 Core-to-core MSIs
The next figure depicts the sequence for initiating a core-to-core MSI , in which m represents the initiating core, n represents the
target core, and x indicates the MSI number. CP m writes to IRCP n IGR x to initiate an MSI. The outstanding MSI that CP m initiates,
targeting CP n , is reflected in the corresponding bit-mapped field in IRCP n ISR x .
CPm writes to
wr 1
IRCP n IGR x IRCPnlGRx to generate
int_en
core-to-core
interrupt to CPn
CP m masterID
IRCP n ISR x
cp1_int cp0_int
Figure 13. Initiating a core-to-core MSI via IRCP n IGR x /IRCP n ISR x

#### 7.3.2 Interrupt steering and semaphores

---

*Page 64*

Miscellaneous System Control Module (MSCM)
7.3.2.1 Interrupt handling overview
The interrupt handling mechanisms of the CM7 cores are very similar. These cores have a NVIC tightly coupled to the processor
core. The real-time performance of the cores means the NVIC directly provides an appropriate interrupt vector, in the form of
the starting instruction address for the interrupt service routine, to the core. These core architectural features directly translate
into a faster ISR entry and exit capabilities, coupled with an improved runtime performance. See the Arm modules and Arm core
technical reference manuals for details.
In this architecture, a total of 240 IRQs are supported, where this parameter is defined by the realistic limits of the NVIC
implementation, both in terms of silicon size and supported frequency of operation. These 240 IRQs are split into a total of four
directed requests and 236 shared peripheral requests. Unless noted otherwise, let the directed requests be defined as IRQ[3:0]
and the shared peripheral requests as IRQ[239:4]. See the interrupt map file attached to this document for details.
7.3.2.2 MSCM interrupt router functional description
As described in MSCM register descriptions , the interrupt routing registers enable the steering of requests to the processor cores.

#### 7.4 Memory map and register definition

NOTE
The CP1 registers shown in the MSCM memory map section (from offset value 220h to 23Ch) are unavailable for
the MCXE315, MCXE316, and MCXE317 variants.

#### 7.4.1 Core configuration registers

These read-only registers contain data that defines the core setup for this chip. You can access the registers using 32-bit read
references; other access sizes terminate with an error. Attempted write accesses to the read-only core configuration registers also
terminate with an error.
The core configuration portion of the MSCM programming model map is organized based on the logical core number, and not on
any type of physical port number. The following table shows how the configuration is partitioned.
Table 29. MSCM core configuration partitioning
Offset address Purpose
range
0h–018h Defines the generic core x configuration information. Only the CM7 cores on the chip can access this region in
either User or Privileged mode; reads by non-core bus masters (including the debugger) are treated as read
as zero (RAZ) accesses. Write attempts are not permitted and terminate with a system bus error.
020h–038h Defines the configuration information for core 0 (CP0). Any bus master can access this region in either User
or Privileged mode. Write attempts are not permitted and terminate with a system bus error.
NOTE
Attempted accesses to reserved locations are not permitted and terminate with a system bus error.

#### 7.4.2 Shared peripheral interrupt (SPI) routing

The SPI router portion of MSCM provides a set of memory-mapped registers defining the interrupt routing for all the SPIs on
this chip.

#### 7.4.3 MSCM register descriptions

7.4.3.1 MSCM memory map
MSCM.MSCM base address: 4026_0000h

---

*Page 65*

Miscellaneous System Control Module (MSCM)
Offset Register Access Reset value
Width
(In bits)
0h Processor X Type (CPXTYPE) 32 R See section
4h Processor X Number (CPXNUM) 32 R See section
8h Processor X Revision (CPXREV) 32 R See section
Ch Processor X Configuration 0 (CPXCFG0) 32 R See section
10h Processor X Configuration 1 (CPXCFG1) 32 R See section
14h Processor X Configuration 2 (CPXCFG2) 32 R See section
18h Processor x Configuration 3 (CPXCFG3) 32 R 0000_000Bh
20h Processor 0 Type (CP0TYPE) 32 R 434D_3730h
24h Processor 0 Number (CP0NUM) 32 R See section
28h Processor 0 Count (CP0REV) 32 R See section
2Ch Processor 0 Configuration 0 (CP0CFG0) 32 R 0502_0504h
30h Processor 0 Configuration 1 (CP0CFG1) 32 R 0000_0000h
34h Processor 0 Configuration 2 (CP0CFG2) 32 R See section
38h Processor 0 Configuration 3 (CP0CFG3) 32 R 0000_000Bh
200h Interrupt Router CP0 Interrupt Status (IRCP0ISR0) 32 RW 0000_0000h
204h Interrupt Router CP0 Interrupt Generation (IRCP0IGR0) 32 RW 0000_0000h
208h Interrupt Router CP0 Interrupt Status (IRCP0ISR1) 32 RW 0000_0000h
20Ch Interrupt Router CP0 Interrupt Generation (IRCP0IGR1) 32 RW 0000_0000h
210h Interrupt Router CP0 Interrupt Status (IRCP0ISR2) 32 RW 0000_0000h
214h Interrupt Router CP0 Interrupt Generation (IRCP0IGR2) 32 RW 0000_0000h
218h Interrupt Router CP0 Interrupt Status (IRCP0ISR3) 32 RW 0000_0000h
21Ch Interrupt Router CP0 Interrupt Generation (IRCP0IGR3) 32 RW 0000_0000h
220h Interrupt Router CP1 Interrupt Status (IRCP1ISR0) 32 RW 0000_0000h
224h Interrupt Router CP1 Interrupt Generation (IRCP1IGR0) 32 RW 0000_0000h
228h Interrupt Router CP1 Interrupt Status (IRCP1ISR1) 32 RW 0000_0000h
22Ch Interrupt Router CP1 Interrupt Generation (IRCP1IGR1) 32 RW 0000_0000h
230h Interrupt Router CP1 Interrupt Status (IRCP1ISR2) 32 RW 0000_0000h
234h Interrupt Router CP1 Interrupt Generation (IRCP1IGR2) 32 RW 0000_0000h
238h Interrupt Router CP1 Interrupt Status (IRCP1ISR3) 32 RW 0000_0000h
23Ch Interrupt Router CP1 Interrupt Generation (IRCP1IGR3) 32 RW 0000_0000h
400h Interrupt Router Configuration (IRCPCFG) 32 RW 0000_0000h
600h Enable Interconnect Error Detection (ENEDC) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 66*

Miscellaneous System Control Module (MSCM)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
700h AHB Gasket Configuration (IAHBCFGREG) 32 RW 0000_0000h
880h - A5Eh Interrupt Router Shared Peripheral Routing Control (IRSPRC0 - 16 RW 0003h
IRSPRC239)
7.4.3.2 Processor X Type (CPXTYPE)
Offset
Register Offset
CPXTYPE 0h
Function
Provides a CPU-specific response indicating the personality of the core making the access. The 32-bit response includes four
ASCII characters defining the CPU type (Cortex-M7 cores) along with a byte defining the logical revision number and a byte
defining the instance number of the core.
A read from Cortex-M7 returns the appropriate processor information. Reads from any other bus master return all 0s and
attempted write accesses terminate with an error.
Access: User or privileged read-only
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R PERSONALITY
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R PERSONALITY
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
31-0 Personality of CPx
PERSONALITY Defines the processor personality for CP x .

---

*Page 67*

Miscellaneous System Control Module (MSCM)
Field Function
Processor Personality
CP x = Cortex-M7_0 43_4D_37_30h
7.4.3.3 Processor X Number (CPXNUM)
Offset
Register Offset
CPXNUM 4h
Function
Provides a CPU-specific response indicating the logical processor number of the core making the access.
A read from the Cortex-M7 cores returns the appropriate processor information. Reads from any other bus master return all 0s
and attempted write accesses terminate with an error.
Access: User or privileged read-only
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 CPN
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
Reserved
31-2
—
1-0 Processor Number
CPN This zero-filled word defines the logical processor number for CP x .
Table continues on the next page...

---

*Page 68*

Miscellaneous System Control Module (MSCM)
Table continued from the previous page...
Field Function
NOTE
CPN in MSCM indicates only the on-platform cores and not the HSE core. CPN = 0
represents Cortex-M7_0 . Processor X Number (CPXNUM) .
00b - Cortex-M7 core 0
01b - Reserved
7.4.3.4 Processor X Revision (CPXREV)
Offset
Register Offset
CPXREV 8h
Function
Provides a CPU-specific response indicating the logical revision number of the core.
A read from the Cortex-M7 cores returns the appropriate processor information. Reads from any other bus master return all 0s
and attempted write accesses terminate with an error.
Access: User or privileged read-only
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 RYPZ
W
Reset 0 0 0 0 0 0 0 0 u u u u u u u u
Fields
Field Function
Reserved
31-8
—
7-0 Processor Revision
Table continues on the next page...

---

*Page 69*

Miscellaneous System Control Module (MSCM)
Table continued from the previous page...
Field Function
RYPZ Defines the processor revision for CP x .
For the Cortex-M7 cores in this chip, RYPZ = 12h corresponding to the r1p2 core release.
7.4.3.5 Processor X Configuration 0 (CPXCFG0)
Offset
Register Offset
CPXCFG0 Ch
Function
Provides a CPU-specific response detailing configuration information. In this case, it is information on Level 1 (L1) cache,
if present.
A read from Cortex-M7 returns the appropriate processor information. Reads from any other bus master return all 0s and
attempted write accesses terminate with an error.
Access: User or privileged read-only
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R ICSZ ICWY
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCSZ DCWY
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
31-24 Level 1 Instruction Cache Size
ICSZ Provides an encoded value of the instruction cache size. The capacity of the memory is derived using the
(8+SZ)
formula 2 and expressed as bytes. Here, ICSZ is a non-zero value and ICSZ = 0 indicates that the
memory is not present.
For the Cortex-M7 cores in this chip, ICSZ = 5h (8 KB).
Table continues on the next page...

---

*Page 70*

Miscellaneous System Control Module (MSCM)
Table continued from the previous page...
Field Function
23-16 L1 Instruction Cache Ways
ICWY Provides the number of cache ways for the instruction cache.
For Cortex-M7 cores in this chip, ICWY = 2h (2-way set-associative).
15-8 L1 Data Cache Size
DCSZ Provides an encoded value of the data cache size.
(8+SZ)
The capacity of the memory is derived using the formula 2 and expressed as bytes. Here, DCSZ is a
non-zero value and DCSZ = 0 indicates that the memory is not present.
For Cortex-M7 cores in this chip, DCSZ = 5h (8 KB).
7-0 L1 Data Cache Ways
DCWY Provides the number of cache ways for the data cache.
For the Cortex-M7 cores in this chip, DCWY = 4h (4-way set-associative).
7.4.3.6 Processor X Configuration 1 (CPXCFG1)
Offset
Register Offset
CPXCFG1 10h
Function
Provides a CPU-specific response detailing configuration information. In this case, it is information on Level 2 (L2) cache,
if present.
A read from the Cortex-M7 cores returns the appropriate processor information. Reads from any other bus master return all 0s
and attempted write accesses terminate with an error.
Access: User or privileged read-only
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R L2SZ L2WY
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 71*

Miscellaneous System Control Module (MSCM)
Fields
Field Function
31-24 L2 Cache Size
L2SZ Provides an encoded value of the L2 cache size. The capacity of the memory is derived using the formula
(8+SZ)
2 and expressed as bytes. Here, L2SZ is a non-zero value, and L2SZ = 0 indicates that the memory
is not present.
For the Cortex-M7 cores in this chip, L2SZ = 0h (not present).
23-16 L2 Cache Ways
L2WY Provides the number of cache ways for the L2 cache.
For the Cortex-M7 cores in this chip, L2WY = 0h (not present).
Reserved
15-0
—
7.4.3.7 Processor X Configuration 2 (CPXCFG2)
Offset
Register Offset
CPXCFG2 14h
Function
Provides a CPU-specific response detailing configuration information. In this case, it is information on tightly coupled local
memories, if present.
A read from the Cortex-M7 cores returns the appropriate processor information. Reads from any other bus master return all 0s
and attempted write accesses terminate with an error.
Access: User or privileged read-only
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DTCMSZ ITCMSZ
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 72*

Miscellaneous System Control Module (MSCM)
Fields
Field Function
31-24 Tightly Coupled Data Memory Size
DTCMSZ Provides an encoded value of the tightly coupled local data memory size. The capacity of the memory is
(8+SZ)
derived using the formula 2 and expressed as bytes. Here, DTCMSZ is a non-zero value and DTCMSZ
= 0 indicates that the memory is not present.
For the Cortex-M7 cores in this chip:
• DTCMSZ = 08 in Decoupled mode (64 KB)
• DTCMSZ = 09 for Cortex-M7_0 in Lockstep mode (128 KB)
23-16 Instruction Tightly Coupled Memory Size
ITCMSZ Provides an encoded value of the tightly coupled local instruction memory size. The capacity of the memory
(8+SZ)
is derived using the formula 2 and expressed as bytes. Here, ITCMSZ is a non-zero value, and
ITCMSZ = 0 indicates that the memory is not present.
For the Cortex-M7 cores in this chip:
• ITCMSZ = 07 in Decoupled mode (32 KB)
• ITCMSZ = 08 for Cortex-M7_0 in Lockstep mode (64 KB)
Reserved
15-0
—
7.4.3.8 Processor x Configuration 3 (CPXCFG3)
Offset
Register Offset
CPXCFG3 18h
Function
Provides a CPU-specific response detailing configuration information. In this case, it is information on processor options.
A privileged read from Cortex-M7 returns the appropriate processor information. Reads from any other bus master return all 0s
and attempted write accesses terminate with an error.
Access: User or privileged read-only

---

*Page 73*

Miscellaneous System Control Module (MSCM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 CPY CMP MMU SIMD FPU
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 1 0 1 1
Fields
Field Function
Reserved
31-5
—
4 Cryptography
CPY Indicates if the cryptography extensions are supported in the core.
For the Cortex-M7 cores in this chip, CPY = 0h.
0b - Not supported
1b - Supported
3 Core Memory Protection Unit
CMP Indicates if the core memory protection hardware is included in this core.
For the Cortex-M7 cores in this chip, CMP = 1h.
0b - Not included
1b - Included
2 Memory Management Unit
MMU Indicates if virtual management capabilities are supported in this core.
For the Cortex-M7 cores in this chip, MMU = 0h.
0b - Not supported
1b - Supported
1 SIMD/NEON Instruction Support
SIMD Indicates if the instruction set extensions supporting SIMD and/or NEON capabilities are included in
the processor.
For the Cortex-M7 cores in this chip, SIMD = 1h.
0b - Not included
Table continues on the next page...

---

*Page 74*

Miscellaneous System Control Module (MSCM)
Table continued from the previous page...
Field Function
1b - Included
0 Floating Point Unit
FPU Indicates if hardware support for floating point capabilities is provided in the processor.
For the Cortex-M7 cores in this chip, FPU = 1h.
0b - Not provided
1b - Provided
7.4.3.9 Processor 0 Type (CP0TYPE)
Offset
Register Offset
CP0TYPE 20h
Function
Defines the configuration information for processor 0 (CP0). It has the same field definitions and functionality as provided in
Processor X Type (CPXTYPE) .
A read from any bus master returns the appropriate processor information and attempted write accesses terminate with an error.
Access: User or privileged read-only
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R PERSONALITY
W
Reset
0 1 0 0 0 0 1 1 0 1 0 0 1 1 0 1
1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R PERSONALITY
W
Reset 0 0 1 1 0 1 1 1 0 0 1 1 0 0 0 0
1. u

---

*Page 75*

Miscellaneous System Control Module (MSCM)
Fields
Field Function
31-0 Processor Personality
PERSONALITY This field defines the processor personality for CP0.
For CP0–Cortex-M7 core 0, personality = 43_4D_37_30h.
7.4.3.10 Processor 0 Number (CP0NUM)
Offset
Register Offset
CP0NUM 24h
Function
Defines the configuration information for processor 0 (CP0). It has the same field definitions and functionality as provided in
Processor X Number (CPXNUM) .
A privileged read from any bus master returns the appropriate processor information and attempted write accesses terminate with
an error.
Access: User or privileged read-only
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 CPN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 u u
Fields
Field Function
Reserved
31-2
—
1-0 Processor Number
CPN This zero-filled word defines the logical processor number for CP0.
For processor Cortex-M7 core 0, processor number = 0.

---

*Page 76*

Miscellaneous System Control Module (MSCM)
7.4.3.11 Processor 0 Count (CP0REV)
Offset
Register Offset
CP0REV 28h
Function
Defines the configuration information for processor 0 (CP0). It has the same field definitions and functionality as provided in
Processor X Revision (CPXREV) .
A read from any bus master returns the appropriate processor information and attempted write accesses terminate with an error.
Access: User or privileged read-only
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 RYPZ
W
Reset 0 0 0 0 0 0 0 0 u u u u u u u u
Fields
Field Function
Reserved
31-8
—
7-0 Processor Revision
RYPZ Defines the processor revision for CP0.
For the Cortex-M7 processor, RYPZ = 12h corresponding to the r1p2 core release.
7.4.3.12 Processor 0 Configuration 0 (CP0CFG0)
Offset
Register Offset
CP0CFG0 2Ch

---

*Page 77*

Miscellaneous System Control Module (MSCM)
Function
Defines the configuration information for processor 0 (CP0). It has the same field definitions and functionality as provided in
Processor X Configuration 0 (CPXCFG0) .
A read from any bus master returns the appropriate processor information and attempted write accesses terminate with an error.
Access: User or privileged read-only
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R ICSZ ICWY
W
Reset 0 0 0 0 0 1 0 1 0 0 0 0 0 0 1 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCSZ DCWY
W
Reset 0 0 0 0 0 1 0 1 0 0 0 0 0 1 0 0
Fields
Field Function
31-24 Level 1 Instruction Cache Size
ICSZ Provides an encoded value of the instruction cache size. The capacity of the memory is derived using the
(8+SZ)
formula 2 and expressed as bytes. Here, ICSZ is a non-zero value, and ICSZ = 0 indicates that the
memory is not present.
For the Cortex-M7 cores in this chip, ICSZ = 5h (8 KB).
23-16 L1 Instruction Cache Ways
ICWY Provides the number of cache ways for the instruction cache.
For the Cortex-M7 cores in this chip, ICWY = 2h (2-way set-associative).
15-8 L1 Data Cache Size
DCSZ Provides an encoded value of the data cache size.
(8+SZ)
The capacity of the memory is derived using the formula 2 and expressed as bytes. Here, DCSZ is a
non-zero value, and DCSZ = 0 indicates that the memory is not present.
For Cortex-M7 cores in this chip, DCSZ = 5h (8 KB).
7-0 L1 Data Cache Ways
DCWY Provides the number of cache ways for the data cache.
For the Cortex-M7 cores in this chip, DCWY = 4h (4-way set-associative).

---

*Page 78*

Miscellaneous System Control Module (MSCM)
7.4.3.13 Processor 0 Configuration 1 (CP0CFG1)
Offset
Register Offset
CP0CFG1 30h
Function
Defines the configuration information for processor 0 (CP0). It has the same field definitions and functionality as provided in
Processor X Configuration 1 (CPXCFG1) .
A privileged read from any bus master returns the appropriate processor information and attempted write accesses terminate with
an error.
Access: User or privileged read-only
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R L2SZ L2WY
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 L2 Cache Size
L2SZ Provides an encoded value of the L2 cache size.
(8+SZ)
The capacity of the memory is derived using the formula 2 and expressed as bytes. Here, L2SZ is a
non-zero value, and L2SZ = 0 indicates that the memory is not present.
For the Cortex-M7 cores in this chip, L2SZ = 0h (not present).
23-16 L2 Cache Ways
L2WY Provides the number of cache ways for the L2 cache.
For the Cortex-M7 cores in this chip, L2WY = 0h (not present).
Reserved
15-0
—

---

*Page 79*

Miscellaneous System Control Module (MSCM)
7.4.3.14 Processor 0 Configuration 2 (CP0CFG2)
Offset
Register Offset
CP0CFG2 34h
Function
Defines the configuration information for processor 0 (CP0). It has the same field definitions and functionality as provided in
Processor X Configuration 2 (CPXCFG2) .
A privileged read from any bus master returns the appropriate processor information and attempted write accesses terminate with
an error.
Access: User or privileged read-only
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DTCMSZ ITCMSZ
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 Tightly Coupled Data Memory Size
DTCMSZ Provides an encoded value of the tightly coupled local data memory size.
(8+SZ)
The capacity of the memory is derived using the formula 2 and expressed as bytes. Here, TMLSZ is
a non-zero value, and TMLSZ = 0 indicates that the memory is not present.
For the Cortex-M7 cores in this chip:
• DTCMSZ = 8h in Decoupled mode (64 KB)
• DTCMSZ = 9h in Lockstep mode (128 KB)
23-16 Instruction Tightly Coupled Memory Size
ITCMSZ Provides an encoded value of the tightly coupled local instruction memory size.
(8+SZ)
The capacity of the memory is derived using the formula 2 and expressed as bytes. Here, TMUSZ is
a non-zero value, and TMUSZ = 0 indicates that the memory is not present.
Table continues on the next page...

---

*Page 80*

Miscellaneous System Control Module (MSCM)
Table continued from the previous page...
Field Function
For the Cortex-M7 cores in this chip:
• ITCMSZ = 7h in Decoupled mode (32 KB)
• ITCMSZ = 8h in Lockstep mode (64 KB)
Reserved
15-0
—
7.4.3.15 Processor 0 Configuration 3 (CP0CFG3)
Offset
Register Offset
CP0CFG3 38h
Function
Defines the configuration information for processor 0 (CP0). It has the same field definitions and functionality as provided in
Processor x Configuration 3 (CPXCFG3) .
A privileged read from any bus master returns the appropriate processor information and attempted write accesses terminate with
an error.
Access: User or privileged read-only
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 CPY CMP MMU SIMD FPU
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 1 0 1 1
Fields
Field Function
Reserved
31-5
—
Table continues on the next page...

---

*Page 81*

Miscellaneous System Control Module (MSCM)
Table continued from the previous page...
Field Function
4 Cryptography
CPY Indicates if the cryptography extensions are supported in the core.
For the Cortex-M7 cores in this chip, CPY = 0h.
0b - Not supported
1b - Supported
3 Core Memory Protection Unit
CMP Indicates if the core memory protection hardware is included in this core.
For the Cortex-M7 cores in this chip, CMP = 1h.
0b - Not included
1b - Included
2 Memory Management Unit
MMU Indicates if virtual management capabilities are supported in this core.
For the Cortex-M7 cores in this chip, MMU = 0h.
0b - Not supported
1b - Supported
1 SIMD/NEON Instruction Support
SIMD Indicates if the instruction set extensions supporting SIMD and/or NEON capabilities are included in
the processor.
For the Cortex-M7 cores in this chip, SIMD = 1h.
0b - Not included
1b - Included
0 Floating Point Unit
FPU Indicates if hardware support for floating point capabilities is provided in the processor.
For the Cortex-M7 cores in this chip, FPU = 1h.
0b - Not provided
1b - Provided
7.4.3.16 Interrupt Router CPn Interrupt Status (IRCP0ISR0 - IRCP1ISR3)
Offset
For n = 0 to 1; m = 0 to 3:
Register Offset
IRCPnISRm 200h + (n × 20h) + (m × 8h)

---

*Page 82*

Miscellaneous System Control Module (MSCM)
Function
Provides an interrupt bit map, where each bit defines the state of a unique MSI based on the initiating core. An MSI interrupt clears
in an interrupt service routine by writing 1 to the appropriate field in IRCP n ISR m .
In this discussion, CP m represents the initiating core and CP n represents the target core for a core-to-core interrupt. For more
information on interrupt source mapping, see the interrupt map file attached to this document.
For read access:
• Reads to IRCP n ISR m are only accessible in Privileged mode using 32-bit (word) accesses.
• Privileged 32-bit read accesses from non-core (and non-debugger) bus masters are treated as RAZ.
• Attempted accesses in User mode or the ones using a size other than 32 bits are not permitted. They terminate with an error.
• When CP n requests to read IRCP n ISR m , MSCM returns the entire content of IRCP n ISR m .
• When a trusted core, as indicated by Interrupt Router Configuration (IRCPCFG) , requests to read IRCP n ISR m , MSCM returns
the entire content of IRCP n ISR m .
• When the debugger requests to read IRCP n ISR m , MSCM returns the entire content of IRCP n ISR m .
• When CP m requests to read IRCP n ISR m , MSCM returns the value of the corresponding status, CP m _INT, while not exposing
all the other pending interrupts that the cores initiated.
• When CP m requests to read IRCP n ISR m , MSCM returns the value of the corresponding status, CP m _INT, in bit position 0,
reflecting how CP m set the MSI when it wrote to IRCP n IGR m . All the other fields on the returned read value are zero-filled.
For write access:
• Writes to IRCP n ISR m are only accessible in Privileged mode using 32-bit (word) accesses.
• Attempted accesses in User mode or the ones using a size other than 32 bits are not permitted. They terminate with an
error.
• Writes to IRCP n ISR m follow the Write 1 to Clear (W1C) protocol, whereby writing 1 causes the corresponding field to
become 0, and writing 0 is ignored.
• The target core, CP n , has full access to write to all the fields of IRCP n ISR m .
• A trusted core, as indicated by Interrupt Router Configuration (IRCPCFG) , has full access to write to all the fields of
IRCP n ISR m .
• When CP m is different from CP n , the W1C action by CP m only clears IRCP n ISR m [CP m _INT].
• The CP m field must present W1C in bit position 0 to clear its corresponding interrupt. Write data bits 1-31 that CP m
presents are ignored.
• Privileged write accesses from the non-core (and non-debugger) bus masters are treated as Writes Ignored (WI).
• Privileged write accesses from the debugger are treated as WI.
Access: Privileged mode only

---

*Page 83*

Miscellaneous System Control Module (MSCM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
CP0_
R 0 0
INT
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-2
—
Reserved
1
—
0 CP0-to-CPn Interrupt
CP0_INT Generates a directed interrupt initiated by core 0 targeting core n , if the appropriate interrupt routing bit
is enabled. The interrupt is negated when the target core, a trusted core, or core 0 writes 1 to clear the
field.
0b - No interrupt asserted to CPn
1b - Interrupt to CPn asserted
7.4.3.17 Interrupt Router CPn Interrupt Generation (IRCP0IGR0 - IRCP1IGR3)
Offset
For n = 0 to 1; m = 0 to 3:
Register Offset
IRCPnIGRm 204h + (n × 20h) + (m × 8h)
Function
Provides a mechanism for cores to initiate an MSI to another core in the system.
Privileged, 32-bit accesses from the:
• Cortex-M7 cores are treated as RAZ/W.
• Debugger are treated as RAZ/WI.
• Non-core (and non-debugger) bus masters are treated as RAZ/WI.

---

*Page 84*

Miscellaneous System Control Module (MSCM)
Access: Privileged mode only. Attempted accesses in User mode or the ones using a size other than 32 bits are not permitted and
terminate with an error.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
INT_
W
EN
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-1
—
0 Interrupt Enable
INT_EN Initiates a core-to-core interrupt targeting CP n , if CP m writes to this field. See MSI routing .
7.4.3.18 Interrupt Router Configuration (IRCPCFG)
Offset
Register Offset
IRCPCFG 400h
Function
Provides a mechanism to designate specific cores in the system as trusted. These trusted cores are allowed to access and
manage outstanding MSIs.
Privileged, 32-bit accesses from the:
• Cortex-M7 cores are treated as R/W.
• Debugger are treated as R/WI.
• Non-core (and non-debugger) bus masters are treated as RAZ/WI.
Attempted accesses in User mode or the ones using a size other than 32 bits are not permitted. They terminate with an error.
Access: Privileged mode only

---

*Page 85*

Miscellaneous System Control Module (MSCM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
LOCK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
CP0_
TR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Lock
LOCK Provides a locking mechanism that can be used to limit the ability to write to the register. After you write
1 to this field, it remains 1 until the next reset.
0b - Register can be written by any privileged write
1b - Register is locked (read-only) until the next reset
Reserved
30-2
—
Reserved
1
—
0 CP0 as Trusted Core
CP0_TR Indicates if CP0 is a trusted core with access to read the full contents of IRCP n ISR m .
0b - Not trusted
1b - Trusted
7.4.3.19 Enable Interconnect Error Detection (ENEDC)
Offset
Register Offset
ENEDC 600h
Function
Enables interconnect error detection.
For more information, see the FCCU file attached to this document.
Access: Privileged mode only

---

*Page 86*

Miscellaneous System Control Module (MSCM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
EN_A EN_W EN_A EN_W EN_A EN_W EN_A EN_W EN_A Reserv EN_A EN_W
DD_ ... R_C ... DD_ ... R_A ... DD_ ... R_A ... DD_ ... R_A ... DD_ ... ed DD_ ... R_T ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
EN_A EN_W EN_A EN_W EN_A EN_A EN_A EN_R EN_R EN_R Reserv EN_R EN_R EN_R
Reserved
DD_ ... R_P ... DD_ ... R_P ... DD_ ... DD_ ... DD_ ... D_T ... D_E ... D_H ... ed D_E ... D_C ... D_C ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-28
—
27 Enable Address Check Cortex-M7_0_TCM
EN_ADD_CM7_ Enables or disables the address check for Cortex-M7_0_TCM backdoor path.
0_TCM
0b - Disabled
1b - Enabled
26 Enable Write Data Check Cortex-M7_0_TCM
EN_WR_CM7_0 Enables or disables the write data check for Cortex-M7_0_TCM backdoor path.
_TCM
0b - Disabled
1b - Enabled
25 Enable Address Check AIPS2
EN_ADD_AIPS Enables or disables the AIPS2 address check.
2
0b - Disabled
1b - Enabled
24 Enable Write Data Check AIPS2
EN_WR_AIPS2 Enables and disables the write data check for AIPS2.
0b - Disabled
1b - Enabled
23 Enable Address Check AIPS1
EN_ADD_AIPS Enables or disables the address check for AIPS1 path.
1
0b - Disabled
1b - Enabled
Table continues on the next page...

---

*Page 87*

Miscellaneous System Control Module (MSCM)
Table continued from the previous page...
Field Function
22 Enable Write Data Check AIPS1
EN_WR_AIPS1 Enables or disables the write data check for AIPS1 path.
0b - Disabled
1b - Enabled
21 Enable Address Check AIPS0
EN_ADD_AIPS Enables or disables the address check for AIPS0 path.
0
0b - Disabled
1b - Enabled
20 Enable Write Data Check AIPS0
EN_WR_AIPS0 Enables or disables the write data check for AIPS0 path.
0b - Disabled
1b - Enabled
19 Enable Address Check QuadSPI
EN_ADD_QSPI Enables or disables the address check for QuadSPI path.
0b - Disabled
1b - Enabled
Reserved
18
—
17 Enable Address Check TCM
EN_ADD_TCM Enables or disables the address check for TCM 64-bit path.
0b - Disabled
1b - Enabled
16 Enable Write Data Check TCM
EN_WR_TCM Enables or disables the write data check for TCM 64-bit path.
0b - Disabled
1b - Enabled
15 Enable Address Check PRAM1
EN_ADD_PRA Enables or disables the address check for PRAM1 path.
M1
0b - Disabled
1b - Enabled
14 Enable Write Data Check PRAM1
Table continues on the next page...

---

*Page 88*

Miscellaneous System Control Module (MSCM)
Table continued from the previous page...
Field Function
Enables or disables the write data check for PRAM1.
EN_WR_PRAM
1
0b - Disabled
1b - Enabled
13 Enable Address Check PRAM0
EN_ADD_PRA Enables or disables the address check for PRAM0 path.
M0
0b - Disabled
1b - Enabled
12 Enable Write Data Check PRAM0
EN_WR_PRAM Enables or disables the write data check for PRAM0 path.
0
0b - Disabled
1b - Enabled
11 Enable Address Check P_FLASH_PORT2
EN_ADD_PFLA Enables or disables the address check for P_FLASH_PORT2 path.
SH_PORT2
0b - Disabled
1b - Enabled
10 Enable Address Check P_FLASH_PORT1
EN_ADD_PFLA Enables or disables the address check for P_FLASH_PORT1 path.
SH_PORT1
0b - Disabled
1b - Enabled
9 Enable Address Check P_FLASH_PORT0
EN_ADD_PFLA Enables or disables the address check for P_FLASH_PORT0 path.
SH_PORT0
0b - Disabled
1b - Enabled
8 Enable Read Data Check TCM
EN_RD_TCM Enables or disables the read data check for TCM 32-bit path.
0b - Disabled
1b - Enabled
Reserved
7-6
—
5 Enable Read Data Check EMAC
EN_RD_EMAC Enables or disables read data check for EMAC path.
Table continues on the next page...

---

*Page 89*

Miscellaneous System Control Module (MSCM)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled
4 Enable Read Data Check HSE
EN_RD_HSE Enables or disables the read data check for HSE path.
0b - Disabled
1b - Enabled
Reserved
3
—
2 Enable Read Data Check eDMA
EN_RD_EDMA Enables or disables the read data check for eDMA path.
0b - Disabled
1b - Enabled
1 Enable Read Data Check Cortex-M7_0_AHBP
EN_RD_CM7_0 Enables or disables the read data check for Cortex-M7_0_AHBP path.
_AHBP
0b - Disabled
1b - Enabled
0 Enable Read Data Check Cortex-M7_0_AHBM
EN_RD_CM7_0 Enables or disables the read data check for Cortex-M7_0_AHBM path.
_AHBM
0b - Disabled
1b - Enabled
7.4.3.20 AHB Gasket Configuration (IAHBCFGREG)
Offset
Register Offset
IAHBCFGREG 700h
Function
Controls the functional configuration of the AHB gaskets located on the platform.
Access: Privileged mode only

---

*Page 90*

Miscellaneous System Control Module (MSCM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0
AIPS2 AIPS1 QSPI_ TCM_
_D ... _D ... DI ... DIS ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0
HSE_ DMA_ DMA_ EMAC
DIS ... AXB ... AXB ... _DI ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-29
—
28 Determines whether write burst optimizations in the AIPS2 AHB gasket are enabled or disabled.
AIPS2_DIS_WR Enabling optimization allows performance improvements during burst writes. Disabling optimization is
_OPT required only if you expect an early write burst termination from a master.
0b - Enabled
1b - Disabled
Reserved
27-25
—
24 Determines whether write burst optimizations in the AIPS1 AHB gasket are enabled or disabled.
AIPS1_DIS_WR Enabling optimization allows performance improvements during burst writes. Disabling optimization is
_OPT required only if you expect an early write burst termination from a master.
0b - Enabled
1b - Disabled
Reserved
23-21
—
20 Determines whether write burst optimizations in the QuadSPI AHB gasket are enabled or disabled.
QSPI_DIS_WR_ Enabling optimization allows performance improvements during burst writes. Disabling optimization is
OPT required only if you expect an early write burst termination from a master.
0b - Enabled
1b - Disabled
Reserved
19-17
Table continues on the next page...

---

*Page 91*

Miscellaneous System Control Module (MSCM)
Table continued from the previous page...
Field Function
—
16 Determines whether write burst optimizations in the TCM AHB gasket are enabled or disabled.
TCM_DIS_WR_ Enabling optimization allows performance improvements during burst writes. Disabling optimization is
OPT required only if you expect an early write burst termination from a master.
0b - Enabled
1b - Disabled
Reserved
15-13
—
12 Determines whether write burst optimizations in the HSE AHB gasket are enabled or disabled.
HSE_DIS_WR_ Enabling optimization allows performance improvements during burst writes. Disabling optimization is
OPT required only if you expect an early write burst termination from a master.
0b - Enabled
1b - Disabled
Reserved
11-9
—
8 Determines whether write burst optimizations in the DMA AXBS S1 AHB gasket are enabled or disabled.
DMA_AXBS_S1 Enabling optimization allows performance improvements during burst writes. Disabling optimization is
_DIS_WR_OPT required only if you expect an early write burst termination from a master.
0b - Enabled
1b - Disabled
Reserved
7-5
—
4 Determines whether write burst optimizations in the DMA AXBS S0 AHB gasket are enabled or disabled.
DMA_AXBS_S0 Enabling optimization allows performance improvements during burst writes. Disabling optimization is
_DIS_WR_OPT required only if you expect an early write burst termination from a master.
0b - Enabled
1b - Disabled
Reserved
3-1
—
0 Determines whether write burst optimizations in the EMAC AHB gasket are enabled or disabled.
EMAC_DIS_WR Enabling optimization allows performance improvements during burst writes. Disabling optimization is
_OPT required only if you expect an early write burst termination from a master.
Table continues on the next page...

---

*Page 92*

Miscellaneous System Control Module (MSCM)
Table continued from the previous page...
Field Function
0b - Enabled
1b - Disabled
7.4.3.21 Interrupt Router Shared Peripheral Routing Control (IRSPRC0 - IRSPRC239)
Offset
For n = 0 to 239:
Register Offset
IRSPRCn 880h + (n × 2h)
Function
Provides an array of 16-bit registers, where each register defines the routing control for the corresponding interrupt request.
Starting from IRQ = 0 (first on-platform interrupt vector). See the interrupt map file attached to this document for details.
For this chip, each interrupt request can be either routed to a subset or to all the cores using the bit-mapped fields in IRSPRC n . If
all the CP x E n fields are cleared, the interrupt request is disabled. Each routing control halfword can be locked by writing 1 to the
LOCK field.
Privileged accesses from non-core (and non-debug) bus masters are treated as RAZ/WI, and any attempted User mode reference
terminates with an error. Attempted accesses using a size other than a 16-bit halfword also terminate with an error.
If you write 1 to all the CP x En bits, all the cores service the interrupt. It is up to software to ensure that no conflicts arise from this
setup either via the interrupt handler or through programming core level interrupt routing (the NVIC / GIC ).
Reads and writes to this register beyond IRSPRC207 lead to unpredictable results.
Access: Privileged mode only
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
LOCK M7_0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1
Fields
Field Function
15 Lock
LOCK Provides a mechanism to lock the routing of the corresponding interrupt request. After you write 1 to this
field, attempted writes to IRSPRC n are ignored until the next reset writes 0 to the field.
Table continues on the next page...

---

*Page 93*

Miscellaneous System Control Module (MSCM)
Table continued from the previous page...
Field Function
0b - Writes to IRSPRCn allowed
1b - Writes to IRSPRCn ignored
Reserved
14-2
—
Reserved
1
—
0 Enable Cortex-M7_0 Interrupt Steering
M7_0 Enables or disables the corresponding interrupt request to route to Cortex-M7_0.
0b - Routing disabled
1b - Routing enabled

#### 7.5 Glossary

GIC Generic interrupt controller
IRQs Interrupt requests
ISR Interrupt service routine
MSI Message signal interface
NVIC Nested vector interrupt controller

---

*Page 94*

