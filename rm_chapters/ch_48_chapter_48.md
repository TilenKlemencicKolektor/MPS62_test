<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 48 -->

# Chapter 48

# Error Reporting Module (ERM)

#### 48.1 Chip-specific ERM information

#### 48.1.1 ERM channel mapping

ERM provides information on memory events associated with ECC and parity. It also provides you an option to enable interrupt
notification for these events.
Table 225. ERM channel mapping
Channel # Module Captured status
00 SRAM0 Single-bit error, multi-bit error, syndrome, absolute error address
aligned to double-word (64-bit) boundary
1
01 SRAM1 Single-bit error, multi-bit error, syndrome, absolute error
2
address+18000h aligned to double-word (64-bit) boundary
3
02 Cortex-M7_0 I-cache tag RAM Single-bit error, multi-bit error
3
03 Cortex-M7_0 I-cache data RAM Single-bit error, multi-bit error
3
04 Cortex-M7_0 D-cache tag RAM Single-bit error, multi-bit error
3
05 Cortex-M7_0 D-cache data RAM Single-bit error, multi-bit error
10 Cortex-M7_0 ITCM Single-bit error, multi-bit error, syndrome, offset error address
11 Cortex-M7_0 D0TCM Single-bit error, multi-bit error, syndrome, offset error address
4
12 Cortex-M7_0 D1TCM Single-bit error, multi-bit error, syndrome, offset error address
13 Cortex-M7_1 ITCM Single-bit error, multi-bit error, syndrome, offset error address
14 Cortex-M7_1 D0TCM Single-bit error, multi-bit error, syndrome, offset error address
4
15 Cortex-M7_1 D1TCM Single-bit error, multi-bit error, syndrome, offset error address
5
16 DMA TCD Single-bit error, multi-bit error, syndrome, offset error address
17 Flash memory port p0 Single-bit error, multi-bit error, absolute error address
18 Flash memory port p1 Single-bit error, multi-bit error, absolute error address
6
19 Flash memory port p2 Single-bit error, multi-bit error, absolute error address
1. SRAM1 is not available for the MCXE315/MCXE316/MCXE317 product variants.
2. The size of SRAM0 is 160 KB, and therefore, to align addresses to the next power of 2, an address space of 256 KB is reserved
for error reporting. However, SRAM0 and SRAM1 are in contiguous locations in the memory map. So, you must subtract
18000h (256 KB-160 KB = 96 KB that corresponds to 18000h) from the reported address to get an absolute address. See the
"Memory and Memory Interfaces" chapter for SRAM details on different MCXE31 variants.
3. The cache controller does not report error addresses and syndrome.
4. For address reporting, bit 2 of the address is masked because this bit decides whether the access (read or write) is for D0TCM
or D1TCM. For example, if the offset address is 0h or 8h, it is routed to D0TCM, but if the offset address is 4h or Ch, it is routed
to D1TCM. The errors that are latched for these offset addresses are as follows:
• Offset 0h : Address 0h is latched into the ERM channel corresponding to D0TCM.
• Offset 4h : Address 0h is latched into the ERM channel corresponding to D1TCM.
• Offset 8h : Address 8h is latched into the ERM channel corresponding to D0TCM.
• Offset Ch : Address 8h is latched into the ERM channel corresponding to D1TCM.

---

*Page 1371*

Error Reporting Module (ERM)
5. Bits [31:10] and [2:0] are always 0 because they are not connected.
Bits [9:5] indicate the corresponding TCD out of all the 32 implemented TCDs.
Bits [4:3] indicate an offset of TCDs with respect to a 64-bit aligned boundary.
• If [4:3] is 00, it indicates that an error is on offset address 20h.
• If [4:3] is 01, it indicates that an error is on offset address 28h.
• If [4:3] is 10, it indicates that an error is on offset address 30h.
• If [4:3] is 11, it indicates that an error is on offset address 38h.
6. Flash memory port p2 is not applicable for the MCXE315/MCXE316/MCXE317 product variants.

#### 48.2 Overview

The Error Reporting Module (ERM) provides information and optional interrupt notification on memory error events associated
with ECC and parity. The ERM collects error events on memory accesses for memory arrays, such as flash memory, system
RAM, or peripheral RAMs. ERM supports various channels for memory sources where each ERM channel is associated with
a different memory module. See the chip-specific ERM information for details about supported memory sources and specific
memory channel assignments. If memory supports ECC then ERM syndrome and error address information is captured along with
error event. ERM does not receive this information in case of cache or memory with parity along with error event.

#### 48.2.1 Features

The ERM includes these features:
• Optional interrupt notification on captured error events
• Capturing of address and syndrome information on single-bit correction and non-correctable ECC events
• Support for error event capturing for memory sources, with individual reporting fields and interrupt configuration per
memory channel
• Recording the count value of the number of corrected error events

---

*Page 1372*

Error Reporting Module (ERM)

#### 48.2.2 Block diagram

Register interface
IPI single error interrupt
Memory error (single-, multi-bit)
and attributes (error address, syndrome)
Memory error
channel 0
IPI multi error interrupt
Memory error (single- or multi-bit)
and attributes (error address, syndrome)
Memory error
channel 23
Figure 178. Block diagram

#### 48.3 Functional description

#### 48.3.1 Single-bit correction events

When a single-bit correction event on Memory n is detected, the ERM:
• Records the event by changing the value of the applicable Status Register bit SR x [SBC n ] to 1.
• Increments the correctable error count value (until the counter reaches its maximum value): CORR_ERR_CNT n [COUNT].
• Records the corresponding access address that initiated the event in the Memory n Error Address Register: EAR n (if this
register is present for the channel).
• Stores the corresponding ECC syndrome in the Memory n Error Syndrome Register: SYN n (if this register is present for the
channel). This register identifies the bit position of the corrected data on single-bit data inversion.
The ERM holds event information only for the last reported event.
To clear the record of an event, write 1 to SR x [SBC n ] to change its value to 0.
To reset the correctable error count value, write all zeros to CORR_ERR_CNT n [COUNT].
Optional interrupt notification for single-bit correction events
The ERM provides an option to generate an interrupt notification upon the report of a single-bit correction event. To enable
single-bit correction interrupts for a channel:

---

*Page 1373*

Error Reporting Module (ERM)
1. To enable interrupt notification for single-bit correction events on Memory n , set CR x [ESCIE n ] to 1.
2. Subsequently, a single-bit correction event on Memory n is detected, the ERM:
• Records the event and address, and stores the ECC syndrome as usual.
• Additionally sends an interrupt notification corresponding to the event.
3. To clear both the record of an event and the corresponding interrupt notification, write 1 to SR x [SBC n ] to change its value
to 0.

#### 48.3.2 Non-correctable error events

When a non-correctable ECC error event on Memory n is detected, the ERM:
• Records the event by changing the value of the applicable Status Register bit: SR x [NCE n ] to 1.
• Records the corresponding access address that initiated the event in the Memory n Error Address Register: EAR n (if this
register is present for the channel).
• Stores the corresponding ECC syndrome in the Memory n Error Syndrome Register: SYN n (if this register is present for
the channel).
— In the event of a non-correctable address bit inversion, SYN n identifies the pertinent address bit position.
— In the event of a non-correctable, multi-bit data inversion, the syndrome value does not provide any additional
diagnostic information.
The ERM holds event information only for the last reported event.
To clear the record of an event, write 1 to SR x [NCE n ] to change its value to 0.
Optional interrupt notification for non-correctable error events
The ERM provides an option to generate an interrupt notification upon the report of a non-correctable ECC event. To enable
non-correctable error interrupts for a channel:
1. To enable interrupt notifications for non-correctable error events on Memory n , set CR x [ENCIE n ] to 1.
2. Subsequently, when a non-correctable error event on Memory n is detected, the ERM:
• Records the event and address and stores the ECC syndrome as usual.
• Additionally sends an interrupt notification corresponding to the event.
3. To clear both the record of an event and the corresponding interrupt notification, write 1 to SR x [NCE n ] to change its value
to 0.
NOTE
Parity errors can be mapped to non-correctable errors where error attributes like SYNDROME, ADDRESS are
not provided.

#### 48.4 Initialization

For each ERM channel supporting memory with ECC, prepare the corresponding memory array before enabling ERM interrupts
about errors for that memory.
1. Initialize the memory to a known value so that the correct corresponding ECC codeword is stored.
2. During the memory's initialization, if the ERM captures information about any ECC error event, clear the corresponding
SR x [SBC n ] or SR x [NCE n ] field that stores the record of the event.
3. Program the applicable CR x [ESCIE n ] and CR x [ENCIE n ] fields to enable ERM interrupts as desired.

---

*Page 1374*

Error Reporting Module (ERM)

#### 48.5 ERM register descriptions

You can access the programming model:
• Only in supervisor mode
• Using only 32-bit (word) accesses
Any of the following attempted references to the programming model generates an error termination:
• In user mode
• Using non-32-bit access sizes
Based on the design implementation, the following XFR error behavior is evident at the IPS interface.
• Within the ERM memory map, an XFR error is evident at reserved addresses from location 20h to FFh.
• No XFR error is evident at reserved addresses in memory spaces allocated to each channel. For example: For channel 0, for
read/write accesses to reserved address 10Ch, the XFR error is 0.
• For accesses to locations beyond the addresses allocated for the final channel, the XFR error is 1.
NOTE
• See the chip-specific ERM information at the beginning of this chapter for details on Memory channel mapping.
• To access the channel registers, corresponding memory channel clock must be enabled.

#### 48.5.1 ERM memory map

ERM base address: 4025_C000h
Offset Register Access Reset value
Width
(In bits)
0h ERM Configuration Register 0 (CR0) 32 RW 0000_0000h
4h ERM Configuration Register 1 (CR1) 32 RW 0000_0000h
8h ERM Configuration Register 2 (CR2) 32 RW 0000_0000h
10h ERM Status Register 0 (SR0) 32 RW 0000_0000h
14h ERM Status Register 1 (SR1) 32 RW 0000_0000h
18h ERM Status Register 2 (SR2) 32 RW 0000_0000h
100h ERM Memory 0 Error Address Register (EAR0) 32 R 0000_0000h
104h ERM Memory 0 Syndrome Register (SYN0) 32 R 0000_0000h
108h ERM Memory 0 Correctable Error Count Register 32 RW 0000_0000h
(CORR_ERR_CNT0)
110h ERM Memory 1 Error Address Register (EAR1) 32 R 0000_0000h
114h ERM Memory 1 Syndrome Register (SYN1) 32 R 0000_0000h
118h ERM Memory 1 Correctable Error Count Register 32 RW 0000_0000h
(CORR_ERR_CNT1)
128h ERM Memory 2 Correctable Error Count Register 32 RW 0000_0000h
(CORR_ERR_CNT2)
Table continues on the next page...

---

*Page 1375*

Error Reporting Module (ERM)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
138h ERM Memory 3 Correctable Error Count Register 32 RW 0000_0000h
(CORR_ERR_CNT3)
148h ERM Memory 4 Correctable Error Count Register 32 RW 0000_0000h
(CORR_ERR_CNT4)
158h ERM Memory 5 Correctable Error Count Register 32 RW 0000_0000h
(CORR_ERR_CNT5)
168h ERM Memory 6 Correctable Error Count Register 32 RW 0000_0000h
(CORR_ERR_CNT6)
178h ERM Memory 7 Correctable Error Count Register 32 RW 0000_0000h
(CORR_ERR_CNT7)
188h ERM Memory 8 Correctable Error Count Register 32 RW 0000_0000h
(CORR_ERR_CNT8)
198h ERM Memory 9 Correctable Error Count Register 32 RW 0000_0000h
(CORR_ERR_CNT9)
1A0h ERM Memory 10 Error Address Register (EAR10) 32 R 0000_0000h
1A4h ERM Memory 10 Syndrome Register (SYN10) 32 R 0000_0000h
1A8h ERM Memory 10 Correctable Error Count Register 32 RW 0000_0000h
(CORR_ERR_CNT10)
1B0h ERM Memory 11 Error Address Register (EAR11) 32 R 0000_0000h
1B4h ERM Memory 11 Syndrome Register (SYN11) 32 R 0000_0000h
1B8h ERM Memory 11 Correctable Error Count Register 32 RW 0000_0000h
(CORR_ERR_CNT11)
1C0h ERM Memory 12 Error Address Register (EAR12) 32 R 0000_0000h
1C4h ERM Memory 12 Syndrome Register (SYN12) 32 R 0000_0000h
1C8h ERM Memory 12 Correctable Error Count Register 32 RW 0000_0000h
(CORR_ERR_CNT12)
1D0h ERM Memory 13 Error Address Register (EAR13) 32 R 0000_0000h
1D4h ERM Memory 13 Syndrome Register (SYN13) 32 R 0000_0000h
1D8h ERM Memory 13 Correctable Error Count Register 32 RW 0000_0000h
(CORR_ERR_CNT13)
1E0h ERM Memory 14 Error Address Register (EAR14) 32 R 0000_0000h
1E4h ERM Memory 14 Syndrome Register (SYN14) 32 R 0000_0000h
1E8h ERM Memory 14 Correctable Error Count Register 32 RW 0000_0000h
(CORR_ERR_CNT14)
1F0h ERM Memory 15 Error Address Register (EAR15) 32 R 0000_0000h
Table continues on the next page...

---

*Page 1376*

Error Reporting Module (ERM)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
1F4h ERM Memory 15 Syndrome Register (SYN15) 32 R 0000_0000h
1F8h ERM Memory 15 Correctable Error Count Register 32 RW 0000_0000h
(CORR_ERR_CNT15)
200h ERM Memory 16 Error Address Register (EAR16) 32 R 0000_0000h
204h ERM Memory 16 Syndrome Register (SYN16) 32 R 0000_0000h
208h ERM Memory 16 Correctable Error Count Register 32 RW 0000_0000h
(CORR_ERR_CNT16)
210h ERM Memory 17 Error Address Register (EAR17) 32 R 0000_0000h
218h ERM Memory 17 Correctable Error Count Register 32 RW 0000_0000h
(CORR_ERR_CNT17)
220h ERM Memory 18 Error Address Register (EAR18) 32 R 0000_0000h
228h ERM Memory 18 Correctable Error Count Register 32 RW 0000_0000h
(CORR_ERR_CNT18)
230h ERM Memory 19 Error Address Register (EAR19) 32 R 0000_0000h
238h ERM Memory 19 Correctable Error Count Register 32 RW 0000_0000h
(CORR_ERR_CNT19)

#### 48.5.2 ERM Configuration Register 0 (CR0)

Offset
Register Offset
CR0 0h
Function
This 32-bit control register configures the interrupt notification capability for available channels.

---

*Page 1377*

Error Reporting Module (ERM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0
ESCIE ENCIE ESCIE ENCIE ESCIE ENCIE ESCIE ENCIE
0 0 1 1 2 2 3 3
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0
ESCIE ENCIE ESCIE ENCIE ESCIE ENCIE ESCIE ENCIE
4 4 5 5 6 6 7 7
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 ESCIE0
ESCIE0 Enable Memory 0 Single Correction Interrupt Notification
0b - Interrupt notification of Memory 0 single-bit correction events is disabled.
1b - Interrupt notification of Memory 0 single-bit correction events is enabled.
30 ENCIE0
ENCIE0 Enable Memory 0 Non-Correctable Interrupt Notification
0b - Interrupt notification of Memory 0 non-correctable error events is disabled.
1b - Interrupt notification of Memory 0 non-correctable error events is enabled.
Reserved
29-28
—
27 ESCIE1
ESCIE1 Enable Memory 1 Single Correction Interrupt Notification
0b - Interrupt notification of Memory 1 single-bit correction events is disabled.
1b - Interrupt notification of Memory 1 single-bit correction events is enabled.
26 ENCIE1
ENCIE1 Enable Memory 1 Non-Correctable Interrupt Notification
0b - Interrupt notification of Memory 1 non-correctable error events is disabled.
1b - Interrupt notification of Memory 1 non-correctable error events is enabled.
Reserved
25-24
—
23 ESCIE2
Table continues on the next page...

---

*Page 1378*

Error Reporting Module (ERM)
Table continued from the previous page...
Field Function
ESCIE2 Enable Memory 2 Single Correction Interrupt Notification
0b - Interrupt notification of Memory 2 single-bit correction events is disabled.
1b - Interrupt notification of Memory 2 single-bit correction events is enabled.
22 ENCIE2
ENCIE2 Enable Memory 2 Non-Correctable Interrupt Notification
0b - Interrupt notification of Memory 2 non-correctable error events is disabled.
1b - Interrupt notification of Memory 2 non-correctable error events is enabled.
Reserved
21-20
—
19 ESCIE3
ESCIE3 Enable Memory 3 Single Correction Interrupt Notification
0b - Interrupt notification of Memory 3 single-bit correction events is disabled.
1b - Interrupt notification of Memory 3 single-bit correction events is enabled.
18 ENCIE3
ENCIE3 Enable Memory 3 Non-Correctable Interrupt Notification
0b - Interrupt notification of Memory 3 non-correctable error events is disabled.
1b - Interrupt notification of Memory 3 non-correctable error events is enabled.
Reserved
17-16
—
15 ESCIE4
ESCIE4 Enable Memory 4 Single Correction Interrupt Notification
0b - Interrupt notification of Memory 4 single-bit correction events is disabled.
1b - Interrupt notification of Memory 4 single-bit correction events is enabled.
14 ENCIE4
ENCIE4 Enable Memory 4 Non-Correctable Interrupt Notification
0b - Interrupt notification of Memory 4 non-correctable error events is disabled.
1b - Interrupt notification of Memory 4 non-correctable error events is enabled.
Reserved
13-12
—
11 ESCIE5
Enable Memory 5 Single Correction Interrupt Notification
Table continues on the next page...

---

*Page 1379*

Error Reporting Module (ERM)
Table continued from the previous page...
Field Function
ESCIE5 0b - Interrupt notification of Memory 5 single-bit correction events is disabled.
1b - Interrupt notification of Memory 5 single-bit correction events is enabled.
10 ENCIE5
ENCIE5 Enable Memory 5 Non-Correctable Interrupt Notification
0b - Interrupt notification of Memory 5 non-correctable error events is disabled.
1b - Interrupt notification of Memory 5 non-correctable error events is enabled.
Reserved
9-8
—
7 ESCIE6
ESCIE6 Enable Memory 6 Single Correction Interrupt Notification
0b - Interrupt notification of Memory 6 single-bit correction events is disabled.
1b - Interrupt notification of Memory 6 single-bit correction events is enabled.
6 ENCIE6
ENCIE6 Enable Memory 6 Non-Correctable Interrupt Notification
0b - Interrupt notification of Memory 6 non-correctable error events is disabled.
1b - Interrupt notification of Memory 6 non-correctable error events is enabled.
Reserved
5-4
—
3 ESCIE7
ESCIE7 Enable Memory 7 Single Correction Interrupt Notification
0b - Interrupt notification of Memory 7 single-bit correction events is disabled.
1b - Interrupt notification of Memory 7 single-bit correction events is enabled.
2 ENCIE7
ENCIE7 Enable Memory 7 Non-Correctable Interrupt Notification
0b - Interrupt notification of Memory 7 non-correctable error events is disabled.
1b - Interrupt notification of Memory 7 non-correctable error events is enabled.
Reserved
1-0
—

---

*Page 1380*

Error Reporting Module (ERM)

#### 48.5.3 ERM Configuration Register 1 (CR1)

Offset
Register Offset
CR1 4h
Function
This 32-bit control register configures the interrupt notification capability for available channels.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0
ESCIE ENCIE ESCIE ENCIE ESCIE ENCIE ESCIE ENCIE
8 8 9 9 10 10 11 11
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0
ESCIE ENCIE ESCIE ENCIE ESCIE ENCIE ESCIE ENCIE
12 12 13 13 14 14 15 15
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 ESCIE8
ESCIE8 Enable Memory 8 Single Correction Interrupt Notification
0b - Interrupt notification of Memory 8 single-bit correction events is disabled.
1b - Interrupt notification of Memory 8 single-bit correction events is enabled.
30 ENCIE8
ENCIE8 Enable Memory 8 Non-Correctable Interrupt Notification
0b - Interrupt notification of Memory 8 non-correctable error events is disabled.
1b - Interrupt notification of Memory 8 non-correctable error events is enabled.
Reserved
29-28
—
27 ESCIE9
ESCIE9 Enable Memory 9 Single Correction Interrupt Notification
0b - Interrupt notification of Memory 9 single-bit correction events is disabled.
Table continues on the next page...

---

*Page 1381*

Error Reporting Module (ERM)
Table continued from the previous page...
Field Function
1b - Interrupt notification of Memory 9 single-bit correction events is enabled.
26 ENCIE9
ENCIE9 Enable Memory 9 Non-Correctable Interrupt Notification
0b - Interrupt notification of Memory 9 non-correctable error events is disabled.
1b - Interrupt notification of Memory 9 non-correctable error events is enabled.
Reserved
25-24
—
23 ESCIE10
ESCIE10 Enable Memory 10 Single Correction Interrupt Notification
0b - Interrupt notification of Memory 10 single-bit correction events is disabled.
1b - Interrupt notification of Memory 10 single-bit correction events is enabled.
22 ENCIE10
ENCIE10 Enable Memory 10 Non-Correctable Interrupt Notification
0b - Interrupt notification of Memory 10 non-correctable error events is disabled.
1b - Interrupt notification of Memory 10 non-correctable error events is enabled.
Reserved
21-20
—
19 ESCIE11
ESCIE11 Enable Memory 11 Single Correction Interrupt Notification
0b - Interrupt notification of Memory 11 single-bit correction events is disabled.
1b - Interrupt notification of Memory 11 single-bit correction events is enabled.
18 ENCIE11
ENCIE11 Enable Memory 11 Non-Correctable Interrupt Notification
0b - Interrupt notification of Memory 11 non-correctable error events is disabled.
1b - Interrupt notification of Memory 11 non-correctable error events is enabled.
Reserved
17-16
—
15 ESCIE12
ESCIE12 Enable Memory 12 Single Correction Interrupt Notification
0b - Interrupt notification of Memory 12 single-bit correction events is disabled.
Table continues on the next page...

---

*Page 1382*

Error Reporting Module (ERM)
Table continued from the previous page...
Field Function
1b - Interrupt notification of Memory 12 single-bit correction events is enabled.
14 ENCIE12
ENCIE12 Enable Memory 12 Non-Correctable Interrupt Notification
0b - Interrupt notification of Memory 12 non-correctable error events is disabled.
1b - Interrupt notification of Memory 12 non-correctable error events is enabled.
Reserved
13-12
—
11 ESCIE13
ESCIE13 Enable Memory 13 Single Correction Interrupt Notification
0b - Interrupt notification of Memory 13 single-bit correction events is disabled.
1b - Interrupt notification of Memory 13 single-bit correction events is enabled.
10 ENCIE13
ENCIE13 Enable Memory 13 Non-Correctable Interrupt Notification
0b - Interrupt notification of Memory 13 non-correctable error events is disabled.
1b - Interrupt notification of Memory 13 non-correctable error events is enabled.
Reserved
9-8
—
7 ESCIE14
ESCIE14 Enable Memory 14 Single Correction Interrupt Notification
0b - Interrupt notification of Memory 14 single-bit correction events is disabled.
1b - Interrupt notification of Memory 14 single-bit correction events is enabled.
6 ENCIE14
ENCIE14 Enable Memory 14 Non-Correctable Interrupt Notification
0b - Interrupt notification of Memory 14 non-correctable error events is disabled.
1b - Interrupt notification of Memory 14 non-correctable error events is enabled.
Reserved
5-4
—
3 ESCIE15
ESCIE15 Enable Memory 15 Single Correction Interrupt Notification
0b - Interrupt notification of Memory 15 single-bit correction events is disabled.
Table continues on the next page...

---

*Page 1383*

Error Reporting Module (ERM)
Table continued from the previous page...
Field Function
1b - Interrupt notification of Memory 15 single-bit correction events is enabled.
2 ENCIE15
ENCIE15 Enable Memory 15 Non-Correctable Interrupt Notification
0b - Interrupt notification of Memory 15 non-correctable error events is disabled.
1b - Interrupt notification of Memory 15 non-correctable error events is enabled.
Reserved
1-0
—

#### 48.5.4 ERM Configuration Register 2 (CR2)

Offset
Register Offset
CR2 8h
Function
This 32-bit control register configures the interrupt notification capability for available channels.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0
ESCIE ENCIE ESCIE ENCIE ESCIE ENCIE ESCIE ENCIE
16 16 17 17 18 18 19 19
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0
Reserved Reserved Reserved Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 ESCIE16
ESCIE16 Enable Memory 16 Single Correction Interrupt Notification
Table continues on the next page...

---

*Page 1384*

Error Reporting Module (ERM)
Table continued from the previous page...
Field Function
0b - Interrupt notification of Memory 16 single-bit correction events is disabled.
1b - Interrupt notification of Memory 16 single-bit correction events is enabled.
30 ENCIE16
ENCIE16 Enable Memory 16 Non-Correctable Interrupt Notification
0b - Interrupt notification of Memory 16 non-correctable error events is disabled.
1b - Interrupt notification of Memory 16 non-correctable error events is enabled.
Reserved
29-28
—
27 ESCIE17
ESCIE17 Enable Memory 17 Single Correction Interrupt Notification
0b - Interrupt notification of Memory 17 single-bit correction events is disabled.
1b - Interrupt notification of Memory 17 single-bit correction events is enabled.
26 ENCIE17
ENCIE17 Enable Memory 17 Non-Correctable Interrupt Notification
0b - Interrupt notification of Memory 17 non-correctable error events is disabled.
1b - Interrupt notification of Memory 17 non-correctable error events is enabled.
Reserved
25-24
—
23 ESCIE18
ESCIE18 Enable Memory 18 Single Correction Interrupt Notification
0b - Interrupt notification of Memory 18 single-bit correction events is disabled.
1b - Interrupt notification of Memory 18 single-bit correction events is enabled.
22 ENCIE18
ENCIE18 Enable Memory 18 Non-Correctable Interrupt Notification
0b - Interrupt notification of Memory 18 non-correctable error events is disabled.
1b - Interrupt notification of Memory 18 non-correctable error events is enabled.
Reserved
21-20
—
19 ESCIE19
ESCIE19 Enable Memory 19 Single Correction Interrupt Notification
Table continues on the next page...

---

*Page 1385*

Error Reporting Module (ERM)
Table continued from the previous page...
Field Function
0b - Interrupt notification of Memory 19 single-bit correction events is disabled.
1b - Interrupt notification of Memory 19 single-bit correction events is enabled.
18 ENCIE19
ENCIE19 Enable Memory 19 Non-Correctable Interrupt Notification
0b - Interrupt notification of Memory 19 non-correctable error events is disabled.
1b - Interrupt notification of Memory 19 non-correctable error events is enabled.
Reserved
17-16
—
Reserved
15-14
—
Reserved
13-12
—
Reserved
11-10
—
Reserved
9-8
—
Reserved
7-6
—
Reserved
5-4
—
Reserved
3-2
—
Reserved
1-0
—

#### 48.5.5 ERM Status Register 0 (SR0)

Offset
Register Offset
SR0 10h

---

*Page 1386*

Error Reporting Module (ERM)
Function
This 32-bit status register reports error events for available channels.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R SBC0 NCE0 0 SBC1 NCE1 0 SBC2 NCE2 0 SBC3 NCE3 0
W W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R SBC4 NCE4 0 SBC5 NCE5 0 SBC6 NCE6 0 SBC7 NCE7 0
W W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 SBC0
SBC0 Memory 0 Single-Bit Correction Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification , if CR0[ESCIE0]
is enabled.
0b - No single-bit correction event on Memory 0 detected.
1b - Single-bit correction event on Memory 0 detected.
30 NCE0
NCE0 Memory 0 Non-Correctable Error Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification, if CR0[ENCIE0]
is enabled.
0b - No non-correctable error event on Memory 0 detected.
1b - Non-correctable error event on Memory 0 detected.
Reserved
29-28
—
27 SBC1
SBC1 Memory 1 Single-Bit Correction Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification , if CR0[ESCIE1]
is enabled.
0b - No single-bit correction event on Memory 1 detected.
1b - Single-bit correction event on Memory 1 detected.
Table continues on the next page...

---

*Page 1387*

Error Reporting Module (ERM)
Table continued from the previous page...
Field Function
26 NCE1
NCE1 Memory 1 Non-Correctable Error Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification, if CR0[ENCIE1]
is enabled.
0b - No non-correctable error event on Memory 1 detected.
1b - Non-correctable error event on Memory 1 detected.
Reserved
25-24
—
23 SBC2
SBC2 Memory 2 Single-Bit Correction Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification , if CR0[ESCIE2]
is enabled.
0b - No single-bit correction event on Memory 2 detected.
1b - Single-bit correction event on Memory 2 detected.
22 NCE2
NCE2 Memory 2 Non-Correctable Error Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification, if CR0[ENCIE2]
is enabled.
0b - No non-correctable error event on Memory 2 detected.
1b - Non-correctable error event on Memory 2 detected.
Reserved
21-20
—
19 SBC3
SBC3 Memory 3 Single-Bit Correction Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification , if CR0[ESCIE3]
is enabled.
0b - No single-bit correction event on Memory 3 detected.
1b - Single-bit correction event on Memory 3 detected.
18 NCE3
NCE3 Memory 3 Non-Correctable Error Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification, if CR0[ENCIE3]
is enabled.
0b - No non-correctable error event on Memory 3 detected.
Table continues on the next page...

---

*Page 1388*

Error Reporting Module (ERM)
Table continued from the previous page...
Field Function
1b - Non-correctable error event on Memory 3 detected.
Reserved
17-16
—
15 SBC4
SBC4 Memory 4 Single-Bit Correction Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification , if CR0[ESCIE4]
is enabled.
0b - No single-bit correction event on Memory 4 detected.
1b - Single-bit correction event on Memory 4 detected.
14 NCE4
NCE4 Memory 4 Non-Correctable Error Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification, if CR0[ENCIE4]
is enabled.
0b - No non-correctable error event on Memory 4 detected.
1b - Non-correctable error event on Memory 4 detected.
Reserved
13-12
—
11 SBC5
SBC5 Memory 5 Single-Bit Correction Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification , if CR0[ESCIE5]
is enabled.
0b - No single-bit correction event on Memory 5 detected.
1b - Single-bit correction event on Memory 5 detected.
10 NCE5
NCE5 Memory 5 Non-Correctable Error Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification, if CR0[ENCIE5]
is enabled.
0b - No non-correctable error event on Memory 5 detected.
1b - Non-correctable error event on Memory 5 detected.
Reserved
9-8
—
7 SBC6
Table continues on the next page...

---

*Page 1389*

Error Reporting Module (ERM)
Table continued from the previous page...
Field Function
SBC6 Memory 6 Single-Bit Correction Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification , if CR0[ESCIE6]
is enabled.
0b - No single-bit correction event on Memory 6 detected.
1b - Single-bit correction event on Memory 6 detected.
6 NCE6
NCE6 Memory 6 Non-Correctable Error Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification, if CR0[ENCIE6]
is enabled.
0b - No non-correctable error event on Memory 6 detected.
1b - Non-correctable error event on Memory 6 detected.
Reserved
5-4
—
3 SBC7
SBC7 Memory 7 Single-Bit Correction Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification , if CR0[ESCIE7]
is enabled.
0b - No single-bit correction event on Memory 7 detected.
1b - Single-bit correction event on Memory 7 detected.
2 NCE7
NCE7 Memory 7 Non-Correctable Error Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification, if CR0[ENCIE7]
is enabled.
0b - No non-correctable error event on Memory 7 detected.
1b - Non-correctable error event on Memory 7 detected.
Reserved
1-0
—

#### 48.5.6 ERM Status Register 1 (SR1)

Offset
Register Offset
SR1 14h

---

*Page 1390*

Error Reporting Module (ERM)
Function
This 32-bit status register reports error events for available channels.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R SBC8 NCE8 0 SBC9 NCE9 0 SBC10 NCE10 0 SBC11 NCE11 0
W W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R SBC12 NCE12 0 SBC13 NCE13 0 SBC14 NCE14 0 SBC15 NCE15 0
W W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 SBC8
SBC8 Memory 8 Single-Bit Correction Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification , if CR1[ESCIE8]
is enabled.
0b - No single-bit correction event on Memory 8 detected.
1b - Single-bit correction event on Memory 8 detected.
30 NCE8
NCE8 Memory 8 Non-Correctable Error Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification, if CR1[ENCIE8]
is enabled.
0b - No non-correctable error event on Memory 8 detected.
1b - Non-correctable error event on Memory 8 detected.
Reserved
29-28
—
27 SBC9
SBC9 Memory 9 Single-Bit Correction Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification , if CR1[ESCIE9]
is enabled.
0b - No single-bit correction event on Memory 9 detected.
1b - Single-bit correction event on Memory 9 detected.
Table continues on the next page...

---

*Page 1391*

Error Reporting Module (ERM)
Table continued from the previous page...
Field Function
26 NCE9
NCE9 Memory 9 Non-Correctable Error Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification, if CR1[ENCIE9]
is enabled.
0b - No non-correctable error event on Memory 9 detected.
1b - Non-correctable error event on Memory 9 detected.
Reserved
25-24
—
23 SBC10
SBC10 Memory 10 Single-Bit Correction Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification , if CR1[ESCIE10]
is enabled.
0b - No single-bit correction event on Memory 10 detected.
1b - Single-bit correction event on Memory 10 detected.
22 NCE10
NCE10 Memory 10 Non-Correctable Error Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification, if CR1[ENCIE10]
is enabled.
0b - No non-correctable error event on Memory 10 detected.
1b - Non-correctable error event on Memory 10 detected.
Reserved
21-20
—
19 SBC11
SBC11 Memory 11 Single-Bit Correction Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification , if CR1[ESCIE11]
is enabled.
0b - No single-bit correction event on Memory 11 detected.
1b - Single-bit correction event on Memory 11 detected.
18 NCE11
NCE11 Memory 11 Non-Correctable Error Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification, if CR1[ENCIE11]
is enabled.
0b - No non-correctable error event on Memory 11 detected.
Table continues on the next page...

---

*Page 1392*

Error Reporting Module (ERM)
Table continued from the previous page...
Field Function
1b - Non-correctable error event on Memory 11 detected.
Reserved
17-16
—
15 SBC12
SBC12 Memory 12 Single-Bit Correction Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification , if CR1[ESCIE12]
is enabled.
0b - No single-bit correction event on Memory 12 detected.
1b - Single-bit correction event on Memory 12 detected.
14 NCE12
NCE12 Memory 12 Non-Correctable Error Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification, if CR1[ENCIE12]
is enabled.
0b - No non-correctable error event on Memory 12 detected.
1b - Non-correctable error event on Memory 12 detected.
Reserved
13-12
—
11 SBC13
SBC13 Memory 13 Single-Bit Correction Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification , if CR1[ESCIE13]
is enabled.
0b - No single-bit correction event on Memory 13 detected.
1b - Single-bit correction event on Memory 13 detected.
10 NCE13
NCE13 Memory 13 Non-Correctable Error Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification, if CR1[ENCIE13]
is enabled.
0b - No non-correctable error event on Memory 13 detected.
1b - Non-correctable error event on Memory 13 detected.
Reserved
9-8
—
7 SBC14
Table continues on the next page...

---

*Page 1393*

Error Reporting Module (ERM)
Table continued from the previous page...
Field Function
SBC14 Memory 14 Single-Bit Correction Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification , if CR1[ESCIE14]
is enabled.
0b - No single-bit correction event on Memory 14 detected.
1b - Single-bit correction event on Memory 14 detected.
6 NCE14
NCE14 Memory 14 Non-Correctable Error Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification, if CR1[ENCIE14]
is enabled.
0b - No non-correctable error event on Memory 14 detected.
1b - Non-correctable error event on Memory 14 detected.
Reserved
5-4
—
3 SBC15
SBC15 Memory 15 Single-Bit Correction Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification , if CR1[ESCIE15]
is enabled.
0b - No single-bit correction event on Memory 15 detected.
1b - Single-bit correction event on Memory 15 detected.
2 NCE15
NCE15 Memory 15 Non-Correctable Error Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification, if CR1[ENCIE15]
is enabled.
0b - No non-correctable error event on Memory 15 detected.
1b - Non-correctable error event on Memory 15 detected.
Reserved
1-0
—

#### 48.5.7 ERM Status Register 2 (SR2)

Offset
Register Offset
SR2 18h

---

*Page 1394*

Error Reporting Module (ERM)
Function
This 32-bit status register reports error events for available channels.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R SBC16 NCE16 0 SBC17 NCE17 0 SBC18 NCE18 0 SBC19 NCE19 0
W W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 SBC16
SBC16 Memory 16 Single-Bit Correction Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification , if CR2[ESCIE16]
is enabled.
0b - No single-bit correction event on Memory 16 detected.
1b - Single-bit correction event on Memory 16 detected.
30 NCE16
NCE16 Memory 16 Non-Correctable Error Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification, if CR2[ENCIE16]
is enabled.
0b - No non-correctable error event on Memory 16 detected.
1b - Non-correctable error event on Memory 16 detected.
Reserved
29-28
—
27 SBC17
SBC17 Memory 17 Single-Bit Correction Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification , if CR2[ESCIE17]
is enabled.
0b - No single-bit correction event on Memory 17 detected.
1b - Single-bit correction event on Memory 17 detected.
Table continues on the next page...

---

*Page 1395*

Error Reporting Module (ERM)
Table continued from the previous page...
Field Function
26 NCE17
NCE17 Memory 17 Non-Correctable Error Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification, if CR2[ENCIE17]
is enabled.
0b - No non-correctable error event on Memory 17 detected.
1b - Non-correctable error event on Memory 17 detected.
Reserved
25-24
—
23 SBC18
SBC18 Memory 18 Single-Bit Correction Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification , if CR2[ESCIE18]
is enabled.
0b - No single-bit correction event on Memory 18 detected.
1b - Single-bit correction event on Memory 18 detected.
22 NCE18
NCE18 Memory 18 Non-Correctable Error Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification, if CR2[ENCIE18]
is enabled.
0b - No non-correctable error event on Memory 18 detected.
1b - Non-correctable error event on Memory 18 detected.
Reserved
21-20
—
19 SBC19
SBC19 Memory 19 Single-Bit Correction Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification , if CR2[ESCIE19]
is enabled.
0b - No single-bit correction event on Memory 19 detected.
1b - Single-bit correction event on Memory 19 detected.
18 NCE19
NCE19 Memory 19 Non-Correctable Error Event
Write 1 to clear this field. This write also clears the corresponding interrupt notification, if CR2[ENCIE19]
is enabled.
0b - No non-correctable error event on Memory 19 detected.
Table continues on the next page...

---

*Page 1396*

Error Reporting Module (ERM)
Table continued from the previous page...
Field Function
1b - Non-correctable error event on Memory 19 detected.
Reserved
17-16
—
Reserved
15-12
—
Reserved
11-8
—
Reserved
7-4
—
Reserved
3-0
—

#### 48.5.8 ERM Memory a Error Address Register (EAR0 - EAR19)

Offset
Register Offset
EAR0 100h
EAR1 110h
EAR10 1A0h
EAR11 1B0h
EAR12 1C0h
EAR13 1D0h
EAR14 1E0h
EAR15 1F0h
EAR16 200h
EAR17 210h
EAR18 220h
EAR19 230h
Function
Each ERM Memory n Error Address Register is a 32-bit register for capturing the address of the last ECC event in Memory n ,
where n denotes the memory channel. Any attempted write to EAR n is ignored.

---

*Page 1397*

Error Reporting Module (ERM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R EAR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R EAR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 EAR
EAR Memory n Error Address — This field contains the faulting system address of the last recorded ECC event
on Memory n .

#### 48.5.9 ERM Memory a Syndrome Register (SYN0 - SYN16)

Offset
Register Offset
SYN0 104h
SYN1 114h
SYN10 1A4h
SYN11 1B4h
SYN12 1C4h
SYN13 1D4h
SYN14 1E4h
SYN15 1F4h
SYN16 204h
Function
The ERM Memory n Syndrome Register is a 32-bit register for capturing the calculated syndrome of the last ECC event on
Memory n , where n denotes the memory channel. Any attempted write to SYN n is ignored. The syndrome value identifies the
pertinent bit position on a correctable, single-bit data inversion or a non-correctable, single-bit address inversion. The syndrome
value does not provide any additional diagnostic information on non-correctable, multi-bit inversions.

---

*Page 1398*

Error Reporting Module (ERM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R SYNDROME 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 SYNDROME
SYNDROME Memory n Syndrome — This field contains the ECC syndrome associated with the last recorded ECC event
on Memory n .
Reserved
23-0
—

#### 48.5.10 ERM Memory a Correctable Error Count Register (CORR_ERR_CNT0 - CORR_ERR_CNT19)

Offset
Register Offset
CORR_ERR_CNT0 108h
CORR_ERR_CNT1 118h
CORR_ERR_CNT2 128h
CORR_ERR_CNT3 138h
CORR_ERR_CNT4 148h
CORR_ERR_CNT5 158h
CORR_ERR_CNT6 168h
CORR_ERR_CNT7 178h
CORR_ERR_CNT8 188h
CORR_ERR_CNT9 198h
CORR_ERR_CNT10 1A8h
CORR_ERR_CNT11 1B8h
Table continues on the next page...

---

*Page 1399*

Error Reporting Module (ERM)
Table continued from the previous page...
Register Offset
CORR_ERR_CNT12 1C8h
CORR_ERR_CNT13 1D8h
CORR_ERR_CNT14 1E8h
CORR_ERR_CNT15 1F8h
CORR_ERR_CNT16 208h
CORR_ERR_CNT17 218h
CORR_ERR_CNT18 228h
CORR_ERR_CNT19 238h
Function
Each 32-bit ERM Memory n Correctable Error Count Register records the count value of the number of correctable ECC error
events for Memory n , where n denotes the memory channel.
NOTE
Non-correctable errors are considered a serious fault, so the ERM does not provide any mechanism to count
non-correctable errors. Only correctable errors are counted.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R Reserved
W 0
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R Reserved COUNT
W 0 0
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-8
—
7-0 Memory n Correctable Error Count
COUNT
Table continues on the next page...

---

*Page 1400*

Error Reporting Module (ERM)
Table continued from the previous page...
Field Function
For each correctable error event, the ERM increments this field's error count value until the counter reaches
its maximum value FFh. COUNT value will stop when it reaches maximum value FFh and will not wrap even
if additional errors occur.
Read this field to determine the correctable error count value so far.
Write all zeros to this field to reset the counter. Writing a non-zero value has no effect.

#### 48.6 Glossary

ECC Error correction code

---

*Page 1401*

