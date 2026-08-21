<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 47 -->

# Chapter 47

# Error Injection Module (EIM)

#### 47.1 Chip-specific EIM information

#### 47.1.1 EIM channel mapping

EIM integrates with the memory controller and memory array to enable error injection in a controlled way. Each memory controller
has its own EIM channel.
EMAC, AIPS2 gasket, Cortex-M7, and QuadSPI gasket are not available in the MCXE317 and MCXE315/MCXE316 product
variants of the MCXE31 family.
Table 221. EIM channel mapping
1 1
Channel # Target Data bits Check bits # of data bits # of check bits
0 SRAM0 Word1[31:0] – SRAM0 Word0[31:24] – SRAM0 64 8
read data[63:32] read data ECC[7:0]
Word2[31:0] – SRAM0
read data[31:0]
2
1 SRAM1 Word1[31:0] – SRAM1 Word0[31:24] SRAM1 read 64 8
read data[63:32] data ECC[7:0]
Word2[31:0] – SRAM1
read data[31:0]
2 DMA TCD Word1[31:0] – DMA TCD Word0[31:24] DMA 64 8
RAM read data[63:32] TCD RAM read
data checkbits[7:0]
Word2[31:0] – DMA TCD
RAM read data[31:0]
3 Cortex-M7_0 IC tag Word1[12:0] – Cortex-M7_0 Word0[31:25] – Cortex- 44 14
IC tag read data1[28:16] M7_0 IC tag read data1[6:0]
Word2[31:22] – Cortex-
M7_0 IC tag
read data1[15:7]
Word2[21:0] – Cortex-M7_0 Word0[24:18] – Cortex-
IC tag read data0[28:7] M7_0 IC tag read data1[6:0]
4 Cortex-M7_0 IC data Word1[31:0] – Cortex-M7_0 Word0[31:24] – Cortex- 128 16
IC data read data1[71:40] M7_0 IC data data1[7:0]
Word2[31:0] – Cortex-M7_0
IC data read data1[39:8]
Word3[31:0] – Cortex-M7_0 Word0[23:16] – Cortex-
IC data read data0[71:40] M7_0 IC data
read data0[7:0]
Word4[31:0] – Cortex-M7_0
IC data read data0[39:8]
Table continues on the next page...

---

*Page 1315*

Error Injection Module (EIM)
Table 221. EIM channel mapping (continued)
1 1
Channel # Target Data bits Check bits # of data bits # of check bits
5 Cortex-M7_0 DC tag Word1[7:0] – Cortex-M7_0 Word0[31:25] – Cortex- 104 28
DC tag read data3[32:25] M7_0 DC tag
read data3[6:0]
Word2[31:14] – Cortex-
M7_0 DC tag
read data3[24:7]
Word2[13:0] – Cortex-M7_0 Word0[24:18] – Cortex-
DC tag read data2[32:19] M7_0 DC tag
read data2[6:0]
Word3[31:20] – Cortex-
M7_0 DC tag
read data2[18:7]
Word3[19:0] – Cortex-M7_0 Word0[17:11] – Cortex-
DC tag read data1[32:13] M7_0 DC tag
read data1[6:0]
Word4[31:26] – Cortex-
M7_0 DC tag
read data1[12:7]
Word4[25:0] – Cortex-M7_0 Word0[10:4] – Cortex-M7_0
DC tag read data0[32:7] DC tag read data0[6:0]
6 Cortex-M7_0 Word1[31:0] – Cortex-M7_0 Word0[31:25] – Cortex- 128 28
DC data0 DC data0 read data3[38:7] M7_0 DC data0
read data3[6:0]
Word2[31:0] – Cortex-M7_0 Word0[24:18] – Cortex-
DC data0 read data2[38:7] M7_0 DC data0
read data2[6:0]
Word3[31:0] – Cortex-M7_0 Word0[17:11] – Cortex-
DC data0 read data1[38:7] M7_0 DC data0
read data1[6:0]
Word4[31:0] – Cortex-M7_0 Word0[10:4] – Cortex-M7_0
DC data0 read data0[38:7] DC data0 read data0[6:0]
7 Cortex-M7_0 Word1[31:0] – Cortex-M7_0 Word0[31:25] – Cortex- 128 28
DC data1 DC data1 read data3[38:7] M7_0 DC data1
read data3[6:0]
Word2[31:0] – Cortex-M7_0 Word0[24:18] – Cortex-
DC data1 read data2[38:7] M7_0 DC data1
read data2[6:0]
Word3[31:0] – Cortex-M7_0 Word0[17:11] – Cortex-
DC data1 read data1[38:7] M7_0 DC data1
read data1[6:0]
Word4[31:0] – Cortex-M7_0 Word0[10:4] – Cortex-M7_0
DC data1 read data0[38:7] DC data1 read data0[6:0]
8 Cortex-M7_1 IC tag Word1[12:0] – Cortex-M7_1 Word0[31:25] – Cortex- 44 14
IC tag read data1[28:16] M7_1 IC tag read data1[6:0]
Table continues on the next page...

---

*Page 1316*

Error Injection Module (EIM)
Table 221. EIM channel mapping (continued)
1 1
Channel # Target Data bits Check bits # of data bits # of check bits
Word2[31:22] – Cortex-
M7_1 IC tag
read data1[15:7]
Word2[21:0] – Cortex-M7_1 Word0[24:18] – Cortex-
IC tag read data0[28:7] M7_1 IC tag read data0[6:0]
9 Cortex-M7_1 IC tag Word1[31:0] – Cortex-M7_1 Word0[31:24] – Cortex- 128 16
IC data read data0[71:40] M7_1 IC data
read data1[7:0]
Word2[31:0] – Cortex-M7_1
IC data read data0[39:8]
Word3[31:0] – Cortex-M7_1 Word0[23:16] – Cortex-
IC data read data0[71:40] M7_1 IC data
read data0[7:0]
Word4[31:0] – Cortex-M7_1
IC data read data0[39:8]
10 Cortex-M7_1 DC tag Word1[7:0] – Cortex-M7_1 Word0[31:25] – Cortex- 104 28
DC tag read data3[32:25] M7_1 DC tag
read data3[6:0]
Word2[31:14] – Cortex-
M7_1 DC tag
read data3[24:7]
Word2[31:0] – Cortex-M7_1 Word0[24:18] – Cortex-
DC tag read data2[32:19] M7_1 DC tag
read data2[6:0]
Word3[31:20] – Cortex-
M7_1 DC tag
read data1[18:7]
Word3[19:0] – Cortex-M7_1 Word0[17:11] – Cortex-
DC tag read data1[32:13] M7_1 DC tag
read data1[6:0]
Word4[31:26] – Cortex-
M7_1 DC tag
read data1[12:7]
Word4[25:0] – Cortex-M7_1 Word0[10:4] – Cortex-M7_1
DC tag read data0[32:7] DC tag read data0[6:0]
11 Cortex-M7_1 Word1[31:0] – Cortex-M7_1 Word0[31:25] – Cortex- 128 28
DC data0 DC data0 read data3[38:7] M7_1 DC data0
read data3[6:0]
Word2[31:0] – Cortex-M7_1 Word0[24:18] – Cortex-
DC data0 read data2[38:7] M7_1 DC data0
read data2[6:0]
Word3[31:0] – Cortex-M7_1 Word0[17:11] – Cortex-
DC data0 read data1[38:7] M7_1 DC data0
read data1[6:0]
Word4[31:0] – Cortex-M7_1 Word0[10:4] – Cortex-M7_1
DC data0 read data0[38:7] DC data0 read data0[6:0]
Table continues on the next page...

---

*Page 1317*

Error Injection Module (EIM)
Table 221. EIM channel mapping (continued)
1 1
Channel # Target Data bits Check bits # of data bits # of check bits
12 Cortex-M7_1 Word1[31:0] – Cortex-M7_1 Word0[31:25] – Cortex- 128 28
DC data1 DC data1 read data0[38:7] M7_1 DC data1
read data3[6:0]
Word2[31:0] – Cortex-M7_1 Word0[24:18] – Cortex-
DC data1 read data0[38:7] M7_1 DC data1
read data2[6:0]
Word3[31:0] – Cortex-M7_1 Word0[17:11] – Cortex-
DC data1 read data0[38:7] M7_1 DC data1
read data1[6:0]
Word4[31:0] – Cortex-M7_1 Word0[10:4] – Cortex-M7_1
DC data1 read data0[38:7] DC data1 read data0[6:0]
13 Cortex-M7_0 ITCM Word1[31:0] – Cortex-M7_0 Word0[31:24] – Cortex- 64 8
ITCM read data[63:32] M7_0 ITCM read
data ECC[7:0]
Word2[31:0] – Cortex-M7_0
ITCM read data[31:0]
14 Cortex-M7_0 D0TCM Word1[31:0] – Cortex-M7_0 Word0[31:24] – Cortex- 32 8
D0TCM read data[31:0] M7_0 D0TCM read
data ECC[7:0]
15 Cortex-M7_0 D1TCM Word1[31:0] – Cortex-M7_0 Word0[31:24] – Cortex- 32 8
D1TCM read data[31:0] M7_0 D1TCM read
data ECC[7:0]
16 Cortex-M7_1 ITCM Word1[31:0] – Cortex-M7_1 Word0[31:24] – Cortex- 64 8
ITCM read data[63:32] M7_1 ITCM read
data ECC[7:0]
Word2[31:0] – Cortex-M7_1
ITCM read data[31:0]
17 Cortex-M7_1 D0TCM Word1[31:0] – Cortex-M7_1 Word0[31:24] – Cortex- 32 8
D0TCM read data[31:0] M7_1 D0TCM read
data ECC[7:0]
18 Cortex-M7_1 D1TCM Word1[31:0] – Cortex-M7_1 Word0[31:24] – Cortex- 32 8
D1TCM read data[31:0] M7_1 D1TCM read
data ECC[7:0]
19 EMAC gasket Word1[27:0] – EMAC AHB — 188 0
write data[63:36]
Word2[31:28] – EMAC AHB
write data[35:32]
Word2[27:0] – EMAC AHB
write data[31:4]
Word3[31:28] – EMAC AHB
write data[3:0]
Word3[27:0] – EMAC AHB
read data[63:36]
Table continues on the next page...

---

*Page 1318*

Error Injection Module (EIM)
Table 221. EIM channel mapping (continued)
1 1
Channel # Target Data bits Check bits # of data bits # of check bits
Word4[31:28] – EMAC AHB
read data[35:32]
Word4[27:0] – EMAC AHB
read data[31:4]
Word5[31:28] – EMAC AHB
read data[3:0]
Word5[27:0] – EMAC
gasket monitor
error injection[59:32]
Word6[31:0] – EMAC
gasket monitor
error injection[31:0]
20 Cortex-M7 Word1[27:0] – TCM AHB — 188 0
TCM gasket write data[63:36]
Word2[31:28] – TCM AHB
write data[35:32]
Word2[27:0] – TCM AHB
write data[31:4]
Word3[31:28] – TCM AHB
write data[3:0]
Word3[27:0] – TCM AHB
read data[63:36]
Word4[31:28] – TCM AHB
read data[35:32]
Word4[27:0] – TCM AHB
read data[31:4]
Word5[31:28] – TCM AHB
read data[3:0]
Word5[27:0] – TCM
gasket monitor
error injection[59:32]
Word6[31:0] – TCM gasket
monitor error injection[31:0]
21 DMA AXBS Word1[27:0] – DMA — 60 0
S0 gasket AXBS S0 gasket monitor
error injection[59:32]
Word2[31:0] – DMA
AXBS S0 gasket monitor
error injection[0:31]
Table continues on the next page...

---

*Page 1319*

Error Injection Module (EIM)
Table 221. EIM channel mapping (continued)
1 1
Channel # Target Data bits Check bits # of data bits # of check bits
22 DMA AXBS Word1[27:0] – DMA — 60 0
S1 gasket AXBS S1 gasket monitor
error injection[59:32]
Word2[31:0] – DMA
AXBS S1 gasket monitor
error injection[31:0]
23 HSE gasket Word1[27:0] – HSE — 60 0
gasket monitor
error injection[59:32]
Word2[31:0] – HSE gasket
monitor error injection[31:0]
24 QuadSPI gasket Word1[27:0] – QuadSPI — 60 0
gasket monitor
error injection[59:32]
Word2[31:0] – QuadSPI
gasket monitor
error injection[31:0]
25 AIPS1 gasket Word1[27:0] – AIPS1 — 60 0
gasket monitor
error injection[59:32]
Word2[31:0] – AIPS1
gasket monitor
error injection[31:0]
26 AIPS2 gasket Word1[27:0] – AIPS2 — 60 0
gasket monitor
error injection[59:32]
Word2[31:0] – AIPS2
gasket monitor
error injection[31:0]
27 Cortex-M7 Word1[29:0] – Cortex-M7 — 30 0
error injection[29:0]
28 ECC Word1[1:0] – Inject error on — 24 0
checking address flash memory controller port
0 address checker
Word1[3:2] – Inject error on
flash memory controller port
1 address checker
Word1[4:4] – Inject error on
flash memory controller port
2 address checker
Table continues on the next page...

---

*Page 1320*

Error Injection Module (EIM)
Table 221. EIM channel mapping (continued)
1 1
Channel # Target Data bits Check bits # of data bits # of check bits
Word1[7:6] – Inject error
on PRAM0 controller
address checker
Word1[9:8] – Inject error
on PRAM1 controller
address checker
Word1[11:10] – Inject
error on 64-bit TCM bus
address checker
Word1[13:12] – Inject
error on QuadSPI path
address checker
Word1[15:14] – Inject error
on AIPS0 address checker
Word1[17:16] – Inject error
on AIPS1 address checker
Word1[19:18] – Inject error
on AIPS2 address checker
Word1[21:20] – Inject error
on 32-bit TCM Cortex-M7_0
path address checker
Word1[23:22] – Inject error
on 32-bit TCM Cortex-M7_1
path address checker
Word1[27:26] – Inject error
on DMA AXBS S1 address
parity checker
29 EDC checking wdata Word1[1:0] – Inject error — 18 0
on PRAM0 controller write
data checker
Word1[3:2] – Inject error
on PRAM1 controller write
data checker
Word1[5:4] – Inject error
on 64-bit TCM bus write
data checker
Word1[7:6] – Reserved
Word1[9:8] – Inject error on
AIPS0 write data checker
Word1[11:10] – Inject
error on AIPS1 write
data checker
Table continues on the next page...

---

*Page 1321*

Error Injection Module (EIM)
Table 221. EIM channel mapping (continued)
1 1
Channel # Target Data bits Check bits # of data bits # of check bits
Word1[13:12] – Inject
error on AIPS2 write
data checker
Word1[15:14] – Inject error
on 32-bit TCM Cortex-M7_0
path write data checker
Word1[17:16] – Inject error
on 32-bit TCM Cortex-M7_1
path write data checker
30 EDC checking rdata Word1[1:0] – Inject error on — 18 0
Cortex-M7_0 AHBM read
data checker
Word1[3:2] – Inject error
on Cortex-M7_0 AHBP read
data checker
Word1[5:4] – Inject error on
DMA read data checker
Word1[7:6] – Inject error on
STAM read data checker
Word1[9:8] – Inject error on
HSE read data checker
Word1[11:10] – Inject
error on EMAC read
data checker
Word1[13:12] – Inject error
on Cortex-M7_1 AHBM
read data checker
Word1[15:14] – Inject error
on Cortex-M7_1 AHBP read
data checker
Word1[17:16] – Inject error
on 32-bit TCM bus path
read data checker
1. You must write to EICHDi_WORDj registers to inject errors in the desired data and check bits. For details, see tables "Error
injection channel descriptor: DATA_MASK details" and "DATA_MASK bit: Channel-word mapping" in this chapter.
2. SRAM1 is not available for the MCXE317, and MCXE315/MCXE316 variants.
The two enables, GEIEN and EICHEN n , enable the error injection functionality. The former enables it globally and the latter does it
for a particular channel. This double-layer enable provides protection against accidental enabling and reconfiguration of the error
injection function for each channel.
EIM provides support for inducing single-bit and multi-bit inversions on read data when accessing peripheral RAMs through its
data mask registers.

---

*Page 1322*

Error Injection Module (EIM)
NOTE
For enabling error injection on EDC gaskets (corresponding to channel 28, channel 29, and channel 30), you must
also enable the fields corresponding to the required EDC gasket in the MSCM_ENEDC register before enabling
the EIM channel.
EIM_EICHD1_CH01, EIM_EICHD1_CH08, EIM_EICHD1_CH09, EIM_EICHD1_CH10, EIM_EICHD1_CH11,
EIM_EICHD1_CH12, EIM_EICHD1_CH16, EIM_EICHD1_CH17, EIM_EICHD1_CH18, EIM_EICHD1_CH19,
EIM_EICHD1_CH21, EIM_EICHD1_CH22, EIM_EICHD1_CH24, EIM_EICHD1_CH26, EIM_EICHD1_CH27 are
not present in MCXE317, hence the registers corresponding to these channels are also not present in MCXE317.

#### 47.1.2 Behavior of EIM error injection on gaskets

The error injection on gaskets don't impact the actual data flow. The gasket read data, write data and the monitor error don't get
changed when EIM error injection is done. The gasket compares the actual data with the modified data (with error injection) and
flags the gasket alarm. In case of single-bit error also, the alarm is flagged.
The channels which depict such behavior are the gasket channels listed below:
• Channel 19: EMAC gasket
• Channel 20: Cortex-M7 TCM gasket
• Channel 21: DMA AXBS S0 gasket
• Channel 22: DMA AXBS S1 gasket
• Channel 23: HSE gasket
• Channel 24: QuadSPI gasket
• Channel 25: AIPS1 gasket
• Channel 26: AIPS2 gasket

#### 47.2 Overview

The Error Injection Module (EIM) is mainly used for diagnostic purposes. It provides a method to test the diagnostics (memory
ECC, interconnect parity) by error injection in the field. See the chip-specific EIM information to determine which functional safety
features are supported by this method.
EIM enables you to inject artificial errors on error-checking mechanisms of a system, such as ECC for RAM read data and
parity bits. For each such mechanism that EIM supports on the chip, EIM can inject single-bit and multi-bit inversions on data
in the applicable target bus. Injecting faults on memory accesses can be used to exercise the SEC-DED ECC function of the
related system.
NOTE
Terminology in this chapter has been updated as follows:
Table 222. Updated terms
Updated term Deprecated term
Controller Master

#### 47.2.1 Features

The EIM includes these features:
• Supports 31 error injection channel s . See the chip-specific EIM information for channel assignment details.
• Protection against accidental enable and reconfiguration error injection function via two-stage enable mechanism

---

*Page 1323*

Error Injection Module (EIM)

#### 47.2.2 Block diagram

The following diagram shows an example of EIM implementation with a 64-bit read data bus and an 8-bit checkbit bus.
rdata[63](MSB)
rdata[62]
rdata[61]
RAM Module
array
rdata[0](LSB)
chkbit[7]
chkbit[0]

### EIM

EIMCR[GEIEN]
EICHEN[EICHnEN]
EICHDn_WORD0
EICHDn_WORD1
EICHDn_WORD2
Figure 177. EIM functional block diagram (64-bit read data bus and 8-bit check bit bus)
Several memory elements are implemented within a device, which may not only be the large memory blocks (Flash and SRAM)
but also smaller memories like caches, the TCD blocks, and the embedded peripheral memories. Some larger memories may
actually be built from multiple memory elements, dependent on their size or function. Each of these memory elements implements
its own control logic, the memory controller, that performs the accesses to the actual memory, the memory array. An EIM channel
is associated with a memory controller and provides the capability to alter one or multiple signals in the read access path from
the corresponding memory array(s). Only memory controllers controlling a safety related memory may be associated with an
EIM channel.

---

*Page 1324*

Error Injection Module (EIM)

#### 47.3 Functional description

The EIM provides protection against accidental enabling and reconfiguration of the error injection function by enforcing a
two-stage enablement mechanism. To properly enable the error injection mechanism for a channel:
• Write 1 to the EICHEN[EICH n EN] field, where n denotes the channel number.
• Write 1 to EIMCR[GEIEN].
NOTE
When the use case for a channel requires writing any EICHD n _WORD register, write the EICHD n _WORD register
before executing the two-stage enablement mechanism. A successful write to any EICHD n _WORD register clears
the corresponding EICHEN[EICH n EN] field.
The EIM supports 31 error injection channel s . See the chip-specific EIM information for channel assignment details. Each channel:
• Can be assigned to a single memory array interface by intercepting the assigned memory read data bus and checkbit bus,
and injects errors by inverting the value transmitted for selected bits on each bus line.
• Can be assigned to a redundant comparison unit by intercepting the signals being compared, and injecting errors by
inverting the value transmitted for selected bits on each bus line.
On a memory read access, the applicable EICHD n _WORD registers define which bits of the read data and/or checkbit bus
to invert.
Figure 177 depicts the interception and override of a 64-bit read data bus and an 8-bit checkbit data bus for an example
memory array.
Error injection scenarios
The EIM supports these cases of error injection:
• To generate a single-bit error, invert only 1 bit of the CHKBIT_MASK or DATA_MASK in the EICHD n _WORD registers.
• To generate a multi-bit error, invert only 2 bits of the CHKBIT_MASK or DATA_MASK in the EICHD n _WORD registers.
NOTE
An attempt to invert more than 2 bits in one operation might result in undefined behavior.
To enable error injection:
1. Set the EICHD n _WORD m [CHKBIT_MASK] and EICHD n _WORD m [B a _ b DATA_MASK] fields for each channel that will be
driving an injection.
2. Program the EICHEN register to enable the channels that will be injecting errors.
3. Set the EIMCR[GEIEN] field to globally allow all enabled channels to actively inject errors.
To disable error injection, either disable the EIMCR[GEIEN] field or disable the individual channel enable fields of the
EICHEN register.

#### 47.4 Initialization

This module does not require initialization.

#### 47.6 EIM register descriptions

The EIM provides a programming model mapped to an on-platform peripheral slot.
Programming model access
All system bus controllers can access the programming model:
• Only in supervisor mode

---

*Page 1325*

Error Injection Module (EIM)
• Using only 32-bit (word) accesses
Any of the following attempted references to the programming model generates an error termination:
• In user mode
• Using non-32-bit access sizes
• To undefined (reserved) addresses
Attempted updates to the programming model while the EIM is in the midst of an operation result in non-deterministic behavior.
Error injection channel descriptor: function and structure
Each error injection channel descriptor:
• Specifies a mask that defines which bits of the read data and/or checkbit bus from target RAM are inverted on a read
access.
• Consists of a 288-bit (36-byte) structure, composed of nine 32-bit words, in the EIM programming model. Unused words
are not documented.
— Word0 (EICHD n _WORD0), if present, defines the checkbit mask.
— Word1 (EICHD n _WORD1) and additional words, if present, define the data mask. Word registers subsequent to
Word1 are present only when required by the total width of the channel's data mask. Error injection channel
descriptor: DATA_MASK details.
The multiple channel descriptors are organized sequentially.
Error injection channel descriptor: DATA_MASK details
For each channel: The following tables show the distribution of DATA_MASK's bits across the WORD registers. The first table
shows the total width of DATA_MASK and the distribution of its bits across WORD1, WORD2, and WORD3. The second table
shows the distribution of DATA_MASK's bits across WORD4 and subsequent registers.
Table 223. Error injection channel descriptor: DATA_MASK details
Specific bits of DATA_MASK in
DATA_MASK total
Channel
width (bits)
WORD1 WORD2 WORD3
63 -32 31-0 —
0 64
63 -32 31-0 —
1 64
63 -32 31-0 —
2 64
43 -32 31-0 —
3 44
127 -96 95-64 63-32
4 128
103 -96 95-64 63-32
5 104
127 -96 95-64 63-32
6 128
127 -96 95-64 63-32
7 128
43 -32 31-0 —
8 44
127 -96 95-64 63-32
9 128
103 -96 95-64 63-32
10 104
Table continues on the next page...

---

*Page 1326*

Error Injection Module (EIM)
Table 223. Error injection channel descriptor: DATA_MASK details (continued)
Specific bits of DATA_MASK in
DATA_MASK total
Channel
width (bits)
WORD1 WORD2 WORD3
127 -96 95-64 63-32
11 128
127 -96 95-64 63-32
12 128
63 -32 31-0 —
13 64
31 -0 — —
14 32
31 -0 — —
15 32
63 -32 31-0 —
16 64
31 -0 — —
17 32
31 -0 — —
18 32
187 -160 159-128 127-96
19 188
187 -160 159-128 127-96
20 188
59 -32 31-0 —
21 60
59 -32 31-0 —
22 60
59 -32 31-0 —
23 60
59 -32 31-0 —
24 60
59 -32 31-0 —
25 60
59 -32 31-0 —
26 60
29 -0 — —
27 30
23 -0 — —
28 24
17 -0 — —
29 18
17 -0 — —
30 18
Table 224. DATA_MASK bit: Channel-word mapping
Specific bits of DATA_MASK in
Channel
WORD4 WORD5 WORD6 WORD7 WORD8
31-0 — — — —
4
31-0 — — — —
5
Table continues on the next page...

---

*Page 1327*

Error Injection Module (EIM)
Table 224. DATA_MASK bit: Channel-word mapping (continued)
Specific bits of DATA_MASK in
Channel
WORD4 WORD5 WORD6 WORD7 WORD8
31-0 — — — —
6
31-0 — — — —
7
31-0 — — — —
9
31-0 — — — —
10
31-0 — — — —
11
31-0 — — — —
12
95-64 63-32 31-0 — —
19
95-64 63-32 31-0 — —
20

#### 47.6.1 EIM memory map

EIM base address: 4025_8000h
Offset Register Access Reset value
Width
(In bits)
0h Error Injection Module Configuration Register (EIMCR) 32 RW 0000_0000h
4h Error Injection Channel Enable register (EICHEN) 32 RW 0000_0000h
100h Error Injection Channel Descriptor 0, Word0 (EICHD0_WORD0) 32 RW 0000_0000h
104h Error Injection Channel Descriptor 0, Word1 (EICHD0_WORD1) 32 RW 0000_0000h
108h Error Injection Channel Descriptor 0, Word2 (EICHD0_WORD2) 32 RW 0000_0000h
140h Error Injection Channel Descriptor 1, Word0 (EICHD1_WORD0) 32 RW 0000_0000h
144h Error Injection Channel Descriptor 1, Word1 (EICHD1_WORD1) 32 RW 0000_0000h
148h Error Injection Channel Descriptor 1, Word2 (EICHD1_WORD2) 32 RW 0000_0000h
180h Error Injection Channel Descriptor 2, Word0 (EICHD2_WORD0) 32 RW 0000_0000h
184h Error Injection Channel Descriptor 2, Word1 (EICHD2_WORD1) 32 RW 0000_0000h
188h Error Injection Channel Descriptor 2, Word2 (EICHD2_WORD2) 32 RW 0000_0000h
1C0h Error Injection Channel Descriptor 3, Word0 (EICHD3_WORD0) 32 RW 0000_0000h
1C4h Error Injection Channel Descriptor 3, Word1 (EICHD3_WORD1) 32 RW 0000_0000h
1C8h Error Injection Channel Descriptor 3, Word2 (EICHD3_WORD2) 32 RW 0000_0000h
200h Error Injection Channel Descriptor 4, Word0 (EICHD4_WORD0) 32 RW 0000_0000h
204h Error Injection Channel Descriptor 4, Word1 (EICHD4_WORD1) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 1328*

Error Injection Module (EIM)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
208h Error Injection Channel Descriptor 4, Word2 (EICHD4_WORD2) 32 RW 0000_0000h
20Ch Error Injection Channel Descriptor 4, Word3 (EICHD4_WORD3) 32 RW 0000_0000h
210h Error Injection Channel Descriptor 4, Word4 (EICHD4_WORD4) 32 RW 0000_0000h
240h Error Injection Channel Descriptor 5, Word0 (EICHD5_WORD0) 32 RW 0000_0000h
244h Error Injection Channel Descriptor 5, Word1 (EICHD5_WORD1) 32 RW 0000_0000h
248h Error Injection Channel Descriptor 5, Word2 (EICHD5_WORD2) 32 RW 0000_0000h
24Ch Error Injection Channel Descriptor 5, Word3 (EICHD5_WORD3) 32 RW 0000_0000h
250h Error Injection Channel Descriptor 5, Word4 (EICHD5_WORD4) 32 RW 0000_0000h
280h Error Injection Channel Descriptor 6, Word0 (EICHD6_WORD0) 32 RW 0000_0000h
284h Error Injection Channel Descriptor 6, Word1 (EICHD6_WORD1) 32 RW 0000_0000h
288h Error Injection Channel Descriptor 6, Word2 (EICHD6_WORD2) 32 RW 0000_0000h
28Ch Error Injection Channel Descriptor 6, Word3 (EICHD6_WORD3) 32 RW 0000_0000h
290h Error Injection Channel Descriptor 6, Word4 (EICHD6_WORD4) 32 RW 0000_0000h
2C0h Error Injection Channel Descriptor 7, Word0 (EICHD7_WORD0) 32 RW 0000_0000h
2C4h Error Injection Channel Descriptor 7, Word1 (EICHD7_WORD1) 32 RW 0000_0000h
2C8h Error Injection Channel Descriptor 7, Word2 (EICHD7_WORD2) 32 RW 0000_0000h
2CCh Error Injection Channel Descriptor 7, Word3 (EICHD7_WORD3) 32 RW 0000_0000h
2D0h Error Injection Channel Descriptor 7, Word4 (EICHD7_WORD4) 32 RW 0000_0000h
300h Error Injection Channel Descriptor 8, Word0 (EICHD8_WORD0) 32 RW 0000_0000h
304h Error Injection Channel Descriptor 8, Word1 (EICHD8_WORD1) 32 RW 0000_0000h
308h Error Injection Channel Descriptor 8, Word2 (EICHD8_WORD2) 32 RW 0000_0000h
340h Error Injection Channel Descriptor 9, Word0 (EICHD9_WORD0) 32 RW 0000_0000h
344h Error Injection Channel Descriptor 9, Word1 (EICHD9_WORD1) 32 RW 0000_0000h
348h Error Injection Channel Descriptor 9, Word2 (EICHD9_WORD2) 32 RW 0000_0000h
34Ch Error Injection Channel Descriptor 9, Word3 (EICHD9_WORD3) 32 RW 0000_0000h
350h Error Injection Channel Descriptor 9, Word4 (EICHD9_WORD4) 32 RW 0000_0000h
380h Error Injection Channel Descriptor 10, Word0 (EICHD10_WORD0) 32 RW 0000_0000h
384h Error Injection Channel Descriptor 10, Word1 (EICHD10_WORD1) 32 RW 0000_0000h
388h Error Injection Channel Descriptor 10, Word2 (EICHD10_WORD2) 32 RW 0000_0000h
38Ch Error Injection Channel Descriptor 10, Word3 (EICHD10_WORD3) 32 RW 0000_0000h
390h Error Injection Channel Descriptor 10, Word4 (EICHD10_WORD4) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 1329*

Error Injection Module (EIM)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
3C0h Error Injection Channel Descriptor 11, Word0 (EICHD11_WORD0) 32 RW 0000_0000h
3C4h Error Injection Channel Descriptor 11, Word1 (EICHD11_WORD1) 32 RW 0000_0000h
3C8h Error Injection Channel Descriptor 11, Word2 (EICHD11_WORD2) 32 RW 0000_0000h
3CCh Error Injection Channel Descriptor 11, Word3 (EICHD11_WORD3) 32 RW 0000_0000h
3D0h Error Injection Channel Descriptor 11, Word4 (EICHD11_WORD4) 32 RW 0000_0000h
400h Error Injection Channel Descriptor 12, Word0 (EICHD12_WORD0) 32 RW 0000_0000h
404h Error Injection Channel Descriptor 12, Word1 (EICHD12_WORD1) 32 RW 0000_0000h
408h Error Injection Channel Descriptor 12, Word2 (EICHD12_WORD2) 32 RW 0000_0000h
40Ch Error Injection Channel Descriptor 12, Word3 (EICHD12_WORD3) 32 RW 0000_0000h
410h Error Injection Channel Descriptor 12, Word4 (EICHD12_WORD4) 32 RW 0000_0000h
440h Error Injection Channel Descriptor 13, Word0 (EICHD13_WORD0) 32 RW 0000_0000h
444h Error Injection Channel Descriptor 13, Word1 (EICHD13_WORD1) 32 RW 0000_0000h
448h Error Injection Channel Descriptor 13, Word2 (EICHD13_WORD2) 32 RW 0000_0000h
480h Error Injection Channel Descriptor 14, Word0 (EICHD14_WORD0) 32 RW 0000_0000h
484h Error Injection Channel Descriptor 14, Word1 (EICHD14_WORD1) 32 RW 0000_0000h
4C0h Error Injection Channel Descriptor 15, Word0 (EICHD15_WORD0) 32 RW 0000_0000h
4C4h Error Injection Channel Descriptor 15, Word1 (EICHD15_WORD1) 32 RW 0000_0000h
500h Error Injection Channel Descriptor 16, Word0 (EICHD16_WORD0) 32 RW 0000_0000h
504h Error Injection Channel Descriptor 16, Word1 (EICHD16_WORD1) 32 RW 0000_0000h
508h Error Injection Channel Descriptor 16, Word2 (EICHD16_WORD2) 32 RW 0000_0000h
540h Error Injection Channel Descriptor 17, Word0 (EICHD17_WORD0) 32 RW 0000_0000h
544h Error Injection Channel Descriptor 17, Word1 (EICHD17_WORD1) 32 RW 0000_0000h
580h Error Injection Channel Descriptor 18, Word0 (EICHD18_WORD0) 32 RW 0000_0000h
584h Error Injection Channel Descriptor 18, Word1 (EICHD18_WORD1) 32 RW 0000_0000h
5C4h Error Injection Channel Descriptor 19, Word1 (EICHD19_WORD1) 32 RW 0000_0000h
5C8h Error Injection Channel Descriptor 19, Word2 (EICHD19_WORD2) 32 RW 0000_0000h
5CCh Error Injection Channel Descriptor 19, Word3 (EICHD19_WORD3) 32 RW 0000_0000h
5D0h Error Injection Channel Descriptor 19, Word4 (EICHD19_WORD4) 32 RW 0000_0000h
5D4h Error Injection Channel Descriptor 19, Word5 (EICHD19_WORD5) 32 RW 0000_0000h
5D8h Error Injection Channel Descriptor 19, Word6 (EICHD19_WORD6) 32 RW 0000_0000h
604h Error Injection Channel Descriptor 20, Word1 (EICHD20_WORD1) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 1330*

Error Injection Module (EIM)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
608h Error Injection Channel Descriptor 20, Word2 (EICHD20_WORD2) 32 RW 0000_0000h
60Ch Error Injection Channel Descriptor 20, Word3 (EICHD20_WORD3) 32 RW 0000_0000h
610h Error Injection Channel Descriptor 20, Word4 (EICHD20_WORD4) 32 RW 0000_0000h
614h Error Injection Channel Descriptor 20, Word5 (EICHD20_WORD5) 32 RW 0000_0000h
618h Error Injection Channel Descriptor 20, Word6 (EICHD20_WORD6) 32 RW 0000_0000h
644h Error Injection Channel Descriptor 21, Word1 (EICHD21_WORD1) 32 RW 0000_0000h
648h Error Injection Channel Descriptor 21, Word2 (EICHD21_WORD2) 32 RW 0000_0000h
684h Error Injection Channel Descriptor 22, Word1 (EICHD22_WORD1) 32 RW 0000_0000h
688h Error Injection Channel Descriptor 22, Word2 (EICHD22_WORD2) 32 RW 0000_0000h
6C4h Error Injection Channel Descriptor 23, Word1 (EICHD23_WORD1) 32 RW 0000_0000h
6C8h Error Injection Channel Descriptor 23, Word2 (EICHD23_WORD2) 32 RW 0000_0000h
704h Error Injection Channel Descriptor 24, Word1 (EICHD24_WORD1) 32 RW 0000_0000h
708h Error Injection Channel Descriptor 24, Word2 (EICHD24_WORD2) 32 RW 0000_0000h
744h Error Injection Channel Descriptor 25, Word1 (EICHD25_WORD1) 32 RW 0000_0000h
748h Error Injection Channel Descriptor 25, Word2 (EICHD25_WORD2) 32 RW 0000_0000h
784h Error Injection Channel Descriptor 26, Word1 (EICHD26_WORD1) 32 RW 0000_0000h
788h Error Injection Channel Descriptor 26, Word2 (EICHD26_WORD2) 32 RW 0000_0000h
7C4h Error Injection Channel Descriptor 27, Word1 (EICHD27_WORD1) 32 RW 0000_0000h
804h Error Injection Channel Descriptor 28, Word1 (EICHD28_WORD1) 32 RW 0000_0000h
844h Error Injection Channel Descriptor 29, Word1 (EICHD29_WORD1) 32 RW 0000_0000h
884h Error Injection Channel Descriptor 30, Word1 (EICHD30_WORD1) 32 RW 0000_0000h

#### 47.6.2 Error Injection Module Configuration Register (EIMCR)

Offset
Register Offset
EIMCR 0h
Function
The EIM Configuration Register is used to globally enable/disable the error injection function.

---

*Page 1331*

Error Injection Module (EIM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
GEIEN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-1
—
0 Global Error Injection Enable
GEIEN This bit globally enables or disables the error injection function of the EIM. This field is initialized by
hardware reset.
0b - Disabled
1b - Enabled

#### 47.6.3 Error Injection Channel Enable register (EICHEN)

Offset
Register Offset
EICHEN 4h
Function
Each field of the Error Injection Channel Enable register (EICHEN) is used to enable or disable the corresponding error
injection channel.
NOTE
To enable an error injection channel, the Global Error Injection Enable (EIMCR[GEIEN]) field must also
be asserted.

---

*Page 1332*

Error Injection Module (EIM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
EICH0 EICH1 EICH2 EICH3 EICH4 EICH5 EICH6 EICH7 EICH8 EICH9 EICH1 EICH1 EICH1 EICH1 EICH1 EICH1
EN EN EN EN EN EN EN EN EN EN 0EN 1EN 2EN 3EN 4EN 5EN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
EICH1 EICH1 EICH1 EICH1 EICH2 EICH2 EICH2 EICH2 EICH2 EICH2 EICH2 EICH2 EICH2 EICH2 EICH3
6EN 7EN 8EN 9EN 0EN 1EN 2EN 3EN 4EN 5EN 6EN 7EN 8EN 9EN 0EN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Error Injection Channel 0 Enable
EICH0EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 0
1b - Error injection is enabled on Error Injection Channel 0
30 Error Injection Channel 1 Enable
EICH1EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 1
1b - Error injection is enabled on Error Injection Channel 1
29 Error Injection Channel 2 Enable
EICH2EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Table continues on the next page...

---

*Page 1333*

Error Injection Module (EIM)
Table continued from the previous page...
Field Function
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 2
1b - Error injection is enabled on Error Injection Channel 2
28 Error Injection Channel 3 Enable
EICH3EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 3
1b - Error injection is enabled on Error Injection Channel 3
27 Error Injection Channel 4 Enable
EICH4EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 4
1b - Error injection is enabled on Error Injection Channel 4
26 Error Injection Channel 5 Enable
EICH5EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 5
1b - Error injection is enabled on Error Injection Channel 5
25 Error Injection Channel 6 Enable
EICH6EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
Table continues on the next page...

---

*Page 1334*

Error Injection Module (EIM)
Table continued from the previous page...
Field Function
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 6
1b - Error injection is enabled on Error Injection Channel 6
24 Error Injection Channel 7 Enable
EICH7EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 7
1b - Error injection is enabled on Error Injection Channel 7
23 Error Injection Channel 8 Enable
EICH8EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 8
1b - Error injection is enabled on Error Injection Channel 8
22 Error Injection Channel 9 Enable
EICH9EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 9
1b - Error injection is enabled on Error Injection Channel 9
Table continues on the next page...

---

*Page 1335*

Error Injection Module (EIM)
Table continued from the previous page...
Field Function
21 Error Injection Channel 10 Enable
EICH10EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 10
1b - Error injection is enabled on Error Injection Channel 10
20 Error Injection Channel 11 Enable
EICH11EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 11
1b - Error injection is enabled on Error Injection Channel 11
19 Error Injection Channel 12 Enable
EICH12EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 12
1b - Error injection is enabled on Error Injection Channel 12
18 Error Injection Channel 13 Enable
EICH13EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
Table continues on the next page...

---

*Page 1336*

Error Injection Module (EIM)
Table continued from the previous page...
Field Function
0b - Error injection is disabled on Error Injection Channel 13
1b - Error injection is enabled on Error Injection Channel 13
17 Error Injection Channel 14 Enable
EICH14EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 14
1b - Error injection is enabled on Error Injection Channel 14
16 Error Injection Channel 15 Enable
EICH15EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 15
1b - Error injection is enabled on Error Injection Channel 15
15 Error Injection Channel 16 Enable
EICH16EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 16
1b - Error injection is enabled on Error Injection Channel 16
14 Error Injection Channel 17 Enable
EICH17EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
Table continues on the next page...

---

*Page 1337*

Error Injection Module (EIM)
Table continued from the previous page...
Field Function
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 17
1b - Error injection is enabled on Error Injection Channel 17
13 Error Injection Channel 18 Enable
EICH18EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 18
1b - Error injection is enabled on Error Injection Channel 18
12 Error Injection Channel 19 Enable
EICH19EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 19
1b - Error injection is enabled on Error Injection Channel 19
11 Error Injection Channel 20 Enable
EICH20EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 20
1b - Error injection is enabled on Error Injection Channel 20
Table continues on the next page...

---

*Page 1338*

Error Injection Module (EIM)
Table continued from the previous page...
Field Function
10 Error Injection Channel 21 Enable
EICH21EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 21
1b - Error injection is enabled on Error Injection Channel 21
9 Error Injection Channel 22 Enable
EICH22EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 22
1b - Error injection is enabled on Error Injection Channel 22
8 Error Injection Channel 23 Enable
EICH23EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 23
1b - Error injection is enabled on Error Injection Channel 23
7 Error Injection Channel 24 Enable
EICH24EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
Table continues on the next page...

---

*Page 1339*

Error Injection Module (EIM)
Table continued from the previous page...
Field Function
0b - Error injection is disabled on Error Injection Channel 24
1b - Error injection is enabled on Error Injection Channel 24
6 Error Injection Channel 25 Enable
EICH25EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 25
1b - Error injection is enabled on Error Injection Channel 25
5 Error Injection Channel 26 Enable
EICH26EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 26
1b - Error injection is enabled on Error Injection Channel 26
4 Error Injection Channel 27 Enable
EICH27EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 27
1b - Error injection is enabled on Error Injection Channel 27
3 Error Injection Channel 28 Enable
EICH28EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
Table continues on the next page...

---

*Page 1340*

Error Injection Module (EIM)
Table continued from the previous page...
Field Function
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 28
1b - Error injection is enabled on Error Injection Channel 28
2 Error Injection Channel 29 Enable
EICH29EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 29
1b - Error injection is enabled on Error Injection Channel 29
1 Error Injection Channel 30 Enable
EICH30EN This field enables the corresponding error injection channel. The Global Error Injection Enable
(EIMCR[GEIEN]) field must also be asserted to enable error injection.
After error injection is enabled, all subsequent read accesses incur one or more bit inversions as defined
in the corresponding EICHD n _WORD registers. Error injection remains in effect until the error injection
channel is manually disabled via software.
Any write to the corresponding EICHD n _WORD registers clears the corresponding EICHEN[EICH n EN]
field, disabling the error injection channel.
0b - Error injection is disabled on Error Injection Channel 30
1b - Error injection is enabled on Error Injection Channel 30
Reserved
0
—

#### 47.6.4 Error Injection Channel Descriptor n, Word0 (EICHD0_WORD0 - EICHD2_WORD0)

Offset
Register Offset
EICHD0_WORD0 100h
EICHD1_WORD0 140h
Table continues on the next page...

---

*Page 1341*

Error Injection Module (EIM)
Table continued from the previous page...
Register Offset
EICHD2_WORD0 180h
Function
The first word of the Error Injection Channel Descriptor defines a left-justified mask field: CHKBIT_MASK. Each bit of
CHKBIT_MASK specifies whether the corresponding bit of the checkbit bus from the target RAM should be inverted or
remain unmodified on read accesses. Successful write to this field clears the corresponding error injection channel valid
bit, EICHEN[EICH n EN].
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
CHKBIT_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 Checkbit Mask
CHKBIT_MASK This field defines a bit-mapped mask that specifies whether the corresponding bit of the checkbit bus from
the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
For any unique details about the mapping of CHKBIT_MASK's bits to a channel's target RAM, see the
chip-specific EIM information.
NOTE
Because CHKBIT_MASK is left-justified, the highest bit in the bit range is always in the
position of the most significant bit. For CHKBIT_MASK[7:0] (8 bits wide), CHKBIT_MASK[7]
is in the position of the most significant bit.
0b - The corresponding bit of the checkbit bus remains unmodified.
1b - The corresponding bit of the checkbit bus is inverted.
Reserved
23-0
—

---

*Page 1342*

Error Injection Module (EIM)

#### 47.6.5 Error Injection Channel Descriptor n, Word1 (EICHD0_WORD1 - EICHD2_WORD1)

Offset
Register Offset
EICHD0_WORD1 104h
EICHD1_WORD1 144h
EICHD2_WORD1 184h
Function
The second word of the Error Injection Channel Descriptor defines a right-justified mask field. The bits in B0_3DATA_MASK
correspond to bytes 0–3 of the target bus. Each bit specifies whether the corresponding bit of the target bus should be inverted
or remain unmodified on read accesses. A successful write to this field clears the corresponding error injection channel valid
field, EICHEN[EICH n EN].
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Data Mask Bytes 0-3
B0_3DATA_MA This field defines a bit-mapped mask that specifies whether the corresponding bit of the read data bus from
SK the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
NOTE
For the specific DATA_MASK bits to which B0_3DATA_MASK corresponds, See Error
injection channel descriptor: DATA_MASK details.
0b - The corresponding bit of bytes 0-3 on the read data bus remains unmodified.
1b - The corresponding bit of bytes 0-3 on the read data bus is inverted.

---

*Page 1343*

Error Injection Module (EIM)

#### 47.6.6 Error Injection Channel Descriptor n, Word2 (EICHD0_WORD2 - EICHD26_WORD2)

Offset
Register Offset
EICHD0_WORD2 108h
EICHD1_WORD2 148h
EICHD2_WORD2 188h
EICHD3_WORD2 1C8h
EICHD4_WORD2 208h
EICHD5_WORD2 248h
EICHD6_WORD2 288h
EICHD7_WORD2 2C8h
EICHD8_WORD2 308h
EICHD9_WORD2 348h
EICHD10_WORD2 388h
EICHD11_WORD2 3C8h
EICHD12_WORD2 408h
EICHD13_WORD2 448h
EICHD16_WORD2 508h
EICHD19_WORD2 5C8h
EICHD20_WORD2 608h
EICHD21_WORD2 648h
EICHD22_WORD2 688h
EICHD23_WORD2 6C8h
EICHD24_WORD2 708h
EICHD25_WORD2 748h
EICHD26_WORD2 788h
Function
The third word of the Error Injection Channel Descriptor, when present, defines a right-justified mask field. The bits in
B4_7DATA_MASK correspond to bytes 4–7 of the read data bus. Each bit specifies whether the corresponding bit of the read data
bus from the target RAM should be inverted or remain unmodified on read accesses. A successful write to this field clears the
corresponding error injection channel valid field, EICHEN[EICH n EN].

---

*Page 1344*

Error Injection Module (EIM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
B4_7DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
B4_7DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Data Mask Bytes 4-7
B4_7DATA_MA This field defines a bit-mapped mask that specifies whether the corresponding bit of the read data bus from
SK the target RAM should be inverted or remain unmodified.
NOTE
For each channel: For the specific DATA_MASK bits to which B4_7DATA_MASK
corresponds, See Error injection channel descriptor: DATA_MASK details.
0b - The corresponding bit of bytes 4-7 on the read data bus remains unmodified.
1b - The corresponding bit of bytes 4-7 on the read data bus is inverted.

#### 47.6.7 Error Injection Channel Descriptor 3, Word0 (EICHD3_WORD0)

Offset
Register Offset
EICHD3_WORD0 1C0h
Function
The first word of the Error Injection Channel Descriptor defines a left-justified mask field: CHKBIT_MASK. Each bit of
CHKBIT_MASK specifies whether the corresponding bit of the checkbit bus from the target RAM should be inverted or
remain unmodified on read accesses. Successful write to this field clears the corresponding error injection channel valid
bit, EICHEN[EICH n EN].

---

*Page 1345*

Error Injection Module (EIM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
CHKBIT_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-18 Checkbit Mask
CHKBIT_MASK This field defines a bit-mapped mask that specifies whether the corresponding bit of the checkbit bus from
the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
For any unique details about the mapping of CHKBIT_MASK's bits to a channel's target RAM, see the
chip-specific EIM information.
NOTE
Because CHKBIT_MASK is left-justified, the highest bit in the bit range is always
in the position of the most significant bit. For CHKBIT_MASK[13:0] (14 bits wide),
CHKBIT_MASK[13] is in the position of the most significant bit.
0b - The corresponding bit of the checkbit bus remains unmodified.
1b - The corresponding bit of the checkbit bus is inverted.
Reserved
17-0
—

#### 47.6.8 Error Injection Channel Descriptor 3, Word1 (EICHD3_WORD1)

Offset
Register Offset
EICHD3_WORD1 1C4h
Function
The second word of the Error Injection Channel Descriptor defines a right-justified mask field. The bits in B0_3DATA_MASK
correspond to bytes 0–3 of the target bus. Each bit specifies whether the corresponding bit of the target bus should be inverted
or remain unmodified on read accesses. A successful write to this field clears the corresponding error injection channel valid
field, EICHEN[EICH n EN].

---

*Page 1346*

Error Injection Module (EIM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-12
—
11-0 Data Mask Bytes 0-3
B0_3DATA_MA This field defines a bit-mapped mask that specifies whether the corresponding bit of the read data bus from
SK the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
NOTE
For the specific DATA_MASK bits to which B0_3DATA_MASK corresponds, See Error
injection channel descriptor: DATA_MASK details.
0b - The corresponding bit of bytes 0-3 on the read data bus remains unmodified.
1b - The corresponding bit of bytes 0-3 on the read data bus is inverted.

#### 47.6.9 Error Injection Channel Descriptor 4, Word0 (EICHD4_WORD0)

Offset
Register Offset
EICHD4_WORD0 200h
Function
The first word of the Error Injection Channel Descriptor defines a left-justified mask field: CHKBIT_MASK. Each bit of
CHKBIT_MASK specifies whether the corresponding bit of the checkbit bus from the target RAM should be inverted or
remain unmodified on read accesses. Successful write to this field clears the corresponding error injection channel valid
bit, EICHEN[EICH n EN].

---

*Page 1347*

Error Injection Module (EIM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
CHKBIT_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-16 Checkbit Mask
CHKBIT_MASK This field defines a bit-mapped mask that specifies whether the corresponding bit of the checkbit bus from
the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
For any unique details about the mapping of CHKBIT_MASK's bits to a channel's target RAM, see the
chip-specific EIM information.
NOTE
Because CHKBIT_MASK is left-justified, the highest bit in the bit range is always
in the position of the most significant bit. For CHKBIT_MASK[15:0] (16 bits wide),
CHKBIT_MASK[15] is in the position of the most significant bit.
0b - The corresponding bit of the checkbit bus remains unmodified.
1b - The corresponding bit of the checkbit bus is inverted.
Reserved
15-0
—

#### 47.6.10 Error Injection Channel Descriptor 4, Word1 (EICHD4_WORD1)

Offset
Register Offset
EICHD4_WORD1 204h
Function
The second word of the Error Injection Channel Descriptor defines a right-justified mask field. The bits in B0_3DATA_MASK
correspond to bytes 0–3 of the target bus. Each bit specifies whether the corresponding bit of the target bus should be inverted
or remain unmodified on read accesses. A successful write to this field clears the corresponding error injection channel valid
field, EICHEN[EICH n EN].

---

*Page 1348*

Error Injection Module (EIM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Data Mask Bytes 0-3
B0_3DATA_MA This field defines a bit-mapped mask that specifies whether the corresponding bit of the read data bus from
SK the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
NOTE
For the specific DATA_MASK bits to which B0_3DATA_MASK corresponds, See Error
injection channel descriptor: DATA_MASK details.
0b - The corresponding bit of bytes 0-3 on the read data bus remains unmodified.
1b - The corresponding bit of bytes 0-3 on the read data bus is inverted.

#### 47.6.11 Error Injection Channel Descriptor n, Word3 (EICHD4_WORD3 - EICHD20_WORD3)

Offset
Register Offset
EICHD4_WORD3 20Ch
EICHD5_WORD3 24Ch
EICHD6_WORD3 28Ch
EICHD7_WORD3 2CCh
EICHD9_WORD3 34Ch
EICHD10_WORD3 38Ch
EICHD11_WORD3 3CCh
EICHD12_WORD3 40Ch
EICHD19_WORD3 5CCh
EICHD20_WORD3 60Ch

---

*Page 1349*

Error Injection Module (EIM)
Function
The fourth word of the Error Injection Channel Descriptor, when present, defines a right-justified mask field. The bits in
B8_11DATA_MASK correspond to bytes 8–11 of the read data bus. Each bit specifies whether the corresponding bit of the read
data bus from the target RAM should be inverted or remain unmodified on read accesses. A successful write to this field clears
the corresponding error injection channel valid field, EICHEN[EICH n EN].
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
B8_11DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
B8_11DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Data Mask Bytes 8-11
B8_11DATA_M This field defines a bit-mapped mask that specifies whether the corresponding bit of the read data bus from
ASK the target RAM should be inverted or remain unmodified.
NOTE
For each channel: For the specific DATA_MASK bits to which B8_11DATA_MASK
corresponds, See Error injection channel descriptor: DATA_MASK details.
0b - The corresponding bit of bytes 8-11 on the read data bus remains unmodified.
1b - The corresponding bit of bytes 8-11 on the read data bus is inverted.

#### 47.6.12 Error Injection Channel Descriptor n, Word4 (EICHD4_WORD4 - EICHD20_WORD4)

Offset
Register Offset
EICHD4_WORD4 210h
EICHD5_WORD4 250h
EICHD6_WORD4 290h
EICHD7_WORD4 2D0h
EICHD9_WORD4 350h
EICHD10_WORD4 390h
Table continues on the next page...

---

*Page 1350*

Error Injection Module (EIM)
Table continued from the previous page...
Register Offset
EICHD11_WORD4 3D0h
EICHD12_WORD4 410h
EICHD19_WORD4 5D0h
EICHD20_WORD4 610h
Function
The fifth word of the Error Injection Channel Descriptor, when present, defines a right-justified mask field. The bits in
B12_15DATA_MASK correspond to bytes 12–15 of the read data bus. Each bit specifies whether the corresponding bit of
the read data bus from the target RAM should be inverted or remain unmodified on read accesses. A successful write to this field
clears the corresponding error injection channel valid field, EICHEN[EICH n EN].
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
B12_15DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
B12_15DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Data Mask Bytes 12-15
B12_15DATA_ This field defines a bit-mapped mask that specifies whether the corresponding bit of the read data bus from
MASK the target RAM should be inverted or remain unmodified.
NOTE
For each channel: For the specific DATA_MASK bits to which B12_15DATA_MASK
corresponds, See Error injection channel descriptor: DATA_MASK details.
0b - The corresponding bit of bytes 12-15 on the read data bus remains unmodified.
1b - The corresponding bit of bytes 12-15 on the read data bus is inverted.

---

*Page 1351*

Error Injection Module (EIM)

#### 47.6.13 Error Injection Channel Descriptor n, Word0 (EICHD5_WORD0 - EICHD7_WORD0)

Offset
Register Offset
EICHD5_WORD0 240h
EICHD6_WORD0 280h
EICHD7_WORD0 2C0h
Function
The first word of the Error Injection Channel Descriptor defines a left-justified mask field: CHKBIT_MASK. Each bit of
CHKBIT_MASK specifies whether the corresponding bit of the checkbit bus from the target RAM should be inverted or
remain unmodified on read accesses. Successful write to this field clears the corresponding error injection channel valid
bit, EICHEN[EICH n EN].
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
CHKBIT_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
CHKBIT_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-4 Checkbit Mask
CHKBIT_MASK This field defines a bit-mapped mask that specifies whether the corresponding bit of the checkbit bus from
the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
For any unique details about the mapping of CHKBIT_MASK's bits to a channel's target RAM, see the
chip-specific EIM information.
NOTE
Because CHKBIT_MASK is left-justified, the highest bit in the bit range is always
in the position of the most significant bit. For CHKBIT_MASK[27:0] (28 bits wide),
CHKBIT_MASK[27] is in the position of the most significant bit.
0b - The corresponding bit of the checkbit bus remains unmodified.
1b - The corresponding bit of the checkbit bus is inverted.
Table continues on the next page...

---

*Page 1352*

Error Injection Module (EIM)
Table continued from the previous page...
Field Function
Reserved
3-0
—

#### 47.6.14 Error Injection Channel Descriptor 5, Word1 (EICHD5_WORD1)

Offset
Register Offset
EICHD5_WORD1 244h
Function
The second word of the Error Injection Channel Descriptor defines a right-justified mask field. The bits in B0_3DATA_MASK
correspond to bytes 0–3 of the target bus. Each bit specifies whether the corresponding bit of the target bus should be inverted
or remain unmodified on read accesses. A successful write to this field clears the corresponding error injection channel valid
field, EICHEN[EICH n EN].
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-8
—
7-0 Data Mask Bytes 0-3
B0_3DATA_MA This field defines a bit-mapped mask that specifies whether the corresponding bit of the read data bus from
SK the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
Table continues on the next page...

---

*Page 1353*

Error Injection Module (EIM)
Table continued from the previous page...
Field Function
NOTE
For the specific DATA_MASK bits to which B0_3DATA_MASK corresponds, See Error
injection channel descriptor: DATA_MASK details.
0b - The corresponding bit of bytes 0-3 on the read data bus remains unmodified.
1b - The corresponding bit of bytes 0-3 on the read data bus is inverted.

#### 47.6.15 Error Injection Channel Descriptor n, Word1 (EICHD6_WORD1 - EICHD7_WORD1)

Offset
Register Offset
EICHD6_WORD1 284h
EICHD7_WORD1 2C4h
Function
The second word of the Error Injection Channel Descriptor defines a right-justified mask field. The bits in B0_3DATA_MASK
correspond to bytes 0–3 of the target bus. Each bit specifies whether the corresponding bit of the target bus should be inverted
or remain unmodified on read accesses. A successful write to this field clears the corresponding error injection channel valid
field, EICHEN[EICH n EN].
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Data Mask Bytes 0-3
B0_3DATA_MA This field defines a bit-mapped mask that specifies whether the corresponding bit of the read data bus from
SK the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
Table continues on the next page...

---

*Page 1354*

Error Injection Module (EIM)
Field Function
NOTE
For the specific DATA_MASK bits to which B0_3DATA_MASK corresponds, See Error
injection channel descriptor: DATA_MASK details.
0b - The corresponding bit of bytes 0-3 on the read data bus remains unmodified.
1b - The corresponding bit of bytes 0-3 on the read data bus is inverted.

#### 47.6.16 Error Injection Channel Descriptor 8, Word0 (EICHD8_WORD0)

Offset
Register Offset
EICHD8_WORD0 300h
Function
The first word of the Error Injection Channel Descriptor defines a left-justified mask field: CHKBIT_MASK. Each bit of
CHKBIT_MASK specifies whether the corresponding bit of the checkbit bus from the target RAM should be inverted or
remain unmodified on read accesses. Successful write to this field clears the corresponding error injection channel valid
bit, EICHEN[EICH n EN].
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
CHKBIT_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-18 Checkbit Mask
CHKBIT_MASK This field defines a bit-mapped mask that specifies whether the corresponding bit of the checkbit bus from
the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
For any unique details about the mapping of CHKBIT_MASK's bits to a channel's target RAM, see the
chip-specific EIM information.
Table continues on the next page...

---

*Page 1355*

Error Injection Module (EIM)
Table continued from the previous page...
Field Function
NOTE
Because CHKBIT_MASK is left-justified, the highest bit in the bit range is always
in the position of the most significant bit. For CHKBIT_MASK[13:0] (14 bits wide),
CHKBIT_MASK[13] is in the position of the most significant bit.
0b - The corresponding bit of the checkbit bus remains unmodified.
1b - The corresponding bit of the checkbit bus is inverted.
Reserved
17-0
—

#### 47.6.17 Error Injection Channel Descriptor 8, Word1 (EICHD8_WORD1)

Offset
Register Offset
EICHD8_WORD1 304h
Function
The second word of the Error Injection Channel Descriptor defines a right-justified mask field. The bits in B0_3DATA_MASK
correspond to bytes 0–3 of the target bus. Each bit specifies whether the corresponding bit of the target bus should be inverted
or remain unmodified on read accesses. A successful write to this field clears the corresponding error injection channel valid
field, EICHEN[EICH n EN].
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-12
Table continues on the next page...

---

*Page 1356*

Error Injection Module (EIM)
Table continued from the previous page...
Field Function
—
11-0 Data Mask Bytes 0-3
B0_3DATA_MA This field defines a bit-mapped mask that specifies whether the corresponding bit of the read data bus from
SK the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
NOTE
For the specific DATA_MASK bits to which B0_3DATA_MASK corresponds, See Error
injection channel descriptor: DATA_MASK details.
0b - The corresponding bit of bytes 0-3 on the read data bus remains unmodified.
1b - The corresponding bit of bytes 0-3 on the read data bus is inverted.

#### 47.6.18 Error Injection Channel Descriptor 9, Word0 (EICHD9_WORD0)

Offset
Register Offset
EICHD9_WORD0 340h
Function
The first word of the Error Injection Channel Descriptor defines a left-justified mask field: CHKBIT_MASK. Each bit of
CHKBIT_MASK specifies whether the corresponding bit of the checkbit bus from the target RAM should be inverted or
remain unmodified on read accesses. Successful write to this field clears the corresponding error injection channel valid
bit, EICHEN[EICH n EN].
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
CHKBIT_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1357*

Error Injection Module (EIM)
Fields
Field Function
31-16 Checkbit Mask
CHKBIT_MASK This field defines a bit-mapped mask that specifies whether the corresponding bit of the checkbit bus from
the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
For any unique details about the mapping of CHKBIT_MASK's bits to a channel's target RAM, see the
chip-specific EIM information.
NOTE
Because CHKBIT_MASK is left-justified, the highest bit in the bit range is always
in the position of the most significant bit. For CHKBIT_MASK[15:0] (16 bits wide),
CHKBIT_MASK[15] is in the position of the most significant bit.
0b - The corresponding bit of the checkbit bus remains unmodified.
1b - The corresponding bit of the checkbit bus is inverted.
Reserved
15-0
—

#### 47.6.19 Error Injection Channel Descriptor 9, Word1 (EICHD9_WORD1)

Offset
Register Offset
EICHD9_WORD1 344h
Function
The second word of the Error Injection Channel Descriptor defines a right-justified mask field. The bits in B0_3DATA_MASK
correspond to bytes 0–3 of the target bus. Each bit specifies whether the corresponding bit of the target bus should be inverted
or remain unmodified on read accesses. A successful write to this field clears the corresponding error injection channel valid
field, EICHEN[EICH n EN].
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1358*

Error Injection Module (EIM)
Fields
Field Function
31-0 Data Mask Bytes 0-3
B0_3DATA_MA This field defines a bit-mapped mask that specifies whether the corresponding bit of the read data bus from
SK the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
NOTE
For the specific DATA_MASK bits to which B0_3DATA_MASK corresponds, See Error
injection channel descriptor: DATA_MASK details.
0b - The corresponding bit of bytes 0-3 on the read data bus remains unmodified.
1b - The corresponding bit of bytes 0-3 on the read data bus is inverted.

#### 47.6.20 Error Injection Channel Descriptor n, Word0 (EICHD10_WORD0 - EICHD12_WORD0)

Offset
Register Offset
EICHD10_WORD0 380h
EICHD11_WORD0 3C0h
EICHD12_WORD0 400h
Function
The first word of the Error Injection Channel Descriptor defines a left-justified mask field: CHKBIT_MASK. Each bit of
CHKBIT_MASK specifies whether the corresponding bit of the checkbit bus from the target RAM should be inverted or
remain unmodified on read accesses. Successful write to this field clears the corresponding error injection channel valid
bit, EICHEN[EICH n EN].
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
CHKBIT_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
CHKBIT_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1359*

Error Injection Module (EIM)
Fields
Field Function
31-4 Checkbit Mask
CHKBIT_MASK This field defines a bit-mapped mask that specifies whether the corresponding bit of the checkbit bus from
the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
For any unique details about the mapping of CHKBIT_MASK's bits to a channel's target RAM, see the
chip-specific EIM information.
NOTE
Because CHKBIT_MASK is left-justified, the highest bit in the bit range is always
in the position of the most significant bit. For CHKBIT_MASK[27:0] (28 bits wide),
CHKBIT_MASK[27] is in the position of the most significant bit.
0b - The corresponding bit of the checkbit bus remains unmodified.
1b - The corresponding bit of the checkbit bus is inverted.
Reserved
3-0
—

#### 47.6.21 Error Injection Channel Descriptor 10, Word1 (EICHD10_WORD1)

Offset
Register Offset
EICHD10_WORD1 384h
Function
The second word of the Error Injection Channel Descriptor defines a right-justified mask field. The bits in B0_3DATA_MASK
correspond to bytes 0–3 of the target bus. Each bit specifies whether the corresponding bit of the target bus should be inverted
or remain unmodified on read accesses. A successful write to this field clears the corresponding error injection channel valid
field, EICHEN[EICH n EN].
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1360*

Error Injection Module (EIM)
Fields
Field Function
Reserved
31-8
—
7-0 Data Mask Bytes 0-3
B0_3DATA_MA This field defines a bit-mapped mask that specifies whether the corresponding bit of the read data bus from
SK the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
NOTE
For the specific DATA_MASK bits to which B0_3DATA_MASK corresponds, See Error
injection channel descriptor: DATA_MASK details.
0b - The corresponding bit of bytes 0-3 on the read data bus remains unmodified.
1b - The corresponding bit of bytes 0-3 on the read data bus is inverted.

#### 47.6.22 Error Injection Channel Descriptor n, Word1 (EICHD11_WORD1 - EICHD18_WORD1)

Offset
Register Offset
EICHD11_WORD1 3C4h
EICHD12_WORD1 404h
EICHD13_WORD1 444h
EICHD14_WORD1 484h
EICHD15_WORD1 4C4h
EICHD16_WORD1 504h
EICHD17_WORD1 544h
EICHD18_WORD1 584h
Function
The second word of the Error Injection Channel Descriptor defines a right-justified mask field. The bits in B0_3DATA_MASK
correspond to bytes 0–3 of the target bus. Each bit specifies whether the corresponding bit of the target bus should be inverted
or remain unmodified on read accesses. A successful write to this field clears the corresponding error injection channel valid
field, EICHEN[EICH n EN].

---

*Page 1361*

Error Injection Module (EIM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Data Mask Bytes 0-3
B0_3DATA_MA This field defines a bit-mapped mask that specifies whether the corresponding bit of the read data bus from
SK the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
NOTE
For the specific DATA_MASK bits to which B0_3DATA_MASK corresponds, See Error
injection channel descriptor: DATA_MASK details.
0b - The corresponding bit of bytes 0-3 on the read data bus remains unmodified.
1b - The corresponding bit of bytes 0-3 on the read data bus is inverted.

#### 47.6.23 Error Injection Channel Descriptor n, Word0 (EICHD13_WORD0 - EICHD18_WORD0)

Offset
Register Offset
EICHD13_WORD0 440h
EICHD14_WORD0 480h
EICHD15_WORD0 4C0h
EICHD16_WORD0 500h
EICHD17_WORD0 540h
EICHD18_WORD0 580h
Function
The first word of the Error Injection Channel Descriptor defines a left-justified mask field: CHKBIT_MASK. Each bit of
CHKBIT_MASK specifies whether the corresponding bit of the checkbit bus from the target RAM should be inverted or
remain unmodified on read accesses. Successful write to this field clears the corresponding error injection channel valid
bit, EICHEN[EICH n EN].

---

*Page 1362*

Error Injection Module (EIM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
CHKBIT_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 Checkbit Mask
CHKBIT_MASK This field defines a bit-mapped mask that specifies whether the corresponding bit of the checkbit bus from
the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
For any unique details about the mapping of CHKBIT_MASK's bits to a channel's target RAM, see the
chip-specific EIM information.
NOTE
Because CHKBIT_MASK is left-justified, the highest bit in the bit range is always in the
position of the most significant bit. For CHKBIT_MASK[7:0] (8 bits wide), CHKBIT_MASK[7]
is in the position of the most significant bit.
0b - The corresponding bit of the checkbit bus remains unmodified.
1b - The corresponding bit of the checkbit bus is inverted.
Reserved
23-0
—

#### 47.6.24 Error Injection Channel Descriptor n, Word1 (EICHD19_WORD1 - EICHD26_WORD1)

Offset
Register Offset
EICHD19_WORD1 5C4h
EICHD20_WORD1 604h
EICHD21_WORD1 644h
EICHD22_WORD1 684h
EICHD23_WORD1 6C4h
Table continues on the next page...

---

*Page 1363*

Error Injection Module (EIM)
Table continued from the previous page...
Register Offset
EICHD24_WORD1 704h
EICHD25_WORD1 744h
EICHD26_WORD1 784h
Function
The second word of the Error Injection Channel Descriptor defines a right-justified mask field. The bits in B0_3DATA_MASK
correspond to bytes 0–3 of the target bus. Each bit specifies whether the corresponding bit of the target bus should be inverted
or remain unmodified on read accesses. A successful write to this field clears the corresponding error injection channel valid
field, EICHEN[EICH n EN].
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-28
—
27-0 Data Mask Bytes 0-3
B0_3DATA_MA This field defines a bit-mapped mask that specifies whether the corresponding bit of the read data bus from
SK the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
NOTE
For the specific DATA_MASK bits to which B0_3DATA_MASK corresponds, See Error
injection channel descriptor: DATA_MASK details.
0b - The corresponding bit of bytes 0-3 on the read data bus remains unmodified.
1b - The corresponding bit of bytes 0-3 on the read data bus is inverted.

---

*Page 1364*

Error Injection Module (EIM)

#### 47.6.25 Error Injection Channel Descriptor n, Word5 (EICHD19_WORD5 - EICHD20_WORD5)

Offset
Register Offset
EICHD19_WORD5 5D4h
EICHD20_WORD5 614h
Function
The sixth word of the Error Injection Channel Descriptor, when present, defines a right-justified mask field. The bits in
B16_19DATA_MASK correspond to bytes 16–19 of the read data bus. Each bit specifies whether the corresponding bit of the read
data bus from the target RAM should be inverted or remain unmodified on read accesses. A successful write to this field clears
the corresponding error injection channel valid field, EICHEN[EICH n EN].
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
B16_19DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
B16_19DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Data Mask Bytes 16-19
B16_19DATA_ This field defines a bit-mapped mask that specifies whether the corresponding bit of the read data bus from
MASK the target RAM should be inverted or remain unmodified.
NOTE
For each channel: For the specific DATA_MASK bits to which B16_19DATA_MASK
corresponds, See Error injection channel descriptor: DATA_MASK details.
0b - The corresponding bit of bytes 16-19 on the read data bus remains unmodified.
1b - The corresponding bit of bytes 16-19 on the read data bus is inverted.

---

*Page 1365*

Error Injection Module (EIM)

#### 47.6.26 Error Injection Channel Descriptor n, Word6 (EICHD19_WORD6 - EICHD20_WORD6)

Offset
Register Offset
EICHD19_WORD6 5D8h
EICHD20_WORD6 618h
Function
The seventh word of the Error Injection Channel Descriptor, when present, defines a right-justified mask field. The bits in
B20_23DATA_MASK correspond to bytes 20–23 of the read data bus. Each bit specifies whether the corresponding bit of the read
data bus from the target RAM should be inverted or remain unmodified on read accesses. A successful write to this field clears
the corresponding error injection channel valid field, EICHEN[EICH n EN].
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
B20_23DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
B20_23DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Data Mask Bytes 20-23
B20_23DATA_ This field defines a bit-mapped mask that specifies whether the corresponding bit of the read data bus from
MASK the target RAM should be inverted or remain unmodified.
NOTE
For each channel: For the specific DATA_MASK bits to which B20_23DATA_MASK
corresponds, See Error injection channel descriptor: DATA_MASK details.
0b - The corresponding bit of bytes 20-23 on the read data bus remains unmodified.
1b - The corresponding bit of bytes 20-23 on the read data bus is inverted.

---

*Page 1366*

Error Injection Module (EIM)

#### 47.6.27 Error Injection Channel Descriptor 27, Word1 (EICHD27_WORD1)

Offset
Register Offset
EICHD27_WORD1 7C4h
Function
The second word of the Error Injection Channel Descriptor defines a right-justified mask field. The bits in B0_3DATA_MASK
correspond to bytes 0–3 of the target bus. Each bit specifies whether the corresponding bit of the target bus should be inverted
or remain unmodified on read accesses. A successful write to this field clears the corresponding error injection channel valid
field, EICHEN[EICH n EN].
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-30
—
29-0 Data Mask Bytes 0-3
B0_3DATA_MA This field defines a bit-mapped mask that specifies whether the corresponding bit of the read data bus from
SK the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
NOTE
For the specific DATA_MASK bits to which B0_3DATA_MASK corresponds, See Error
injection channel descriptor: DATA_MASK details.
0b - The corresponding bit of bytes 0-3 on the read data bus remains unmodified.
1b - The corresponding bit of bytes 0-3 on the read data bus is inverted.

---

*Page 1367*

Error Injection Module (EIM)

#### 47.6.28 Error Injection Channel Descriptor 28, Word1 (EICHD28_WORD1)

Offset
Register Offset
EICHD28_WORD1 804h
Function
The second word of the Error Injection Channel Descriptor defines a right-justified mask field. The bits in B0_3DATA_MASK
correspond to bytes 0–3 of the target bus. Each bit specifies whether the corresponding bit of the target bus should be inverted
or remain unmodified on read accesses. A successful write to this field clears the corresponding error injection channel valid
field, EICHEN[EICH n EN].
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-24
—
23-0 Data Mask Bytes 0-3
B0_3DATA_MA This field defines a bit-mapped mask that specifies whether the corresponding bit of the read data bus from
SK the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
NOTE
For the specific DATA_MASK bits to which B0_3DATA_MASK corresponds, See Error
injection channel descriptor: DATA_MASK details.
0b - The corresponding bit of bytes 0-3 on the read data bus remains unmodified.
1b - The corresponding bit of bytes 0-3 on the read data bus is inverted.

---

*Page 1368*

Error Injection Module (EIM)

#### 47.6.29 Error Injection Channel Descriptor n, Word1 (EICHD29_WORD1 - EICHD30_WORD1)

Offset
Register Offset
EICHD29_WORD1 844h
EICHD30_WORD1 884h
Function
The second word of the Error Injection Channel Descriptor defines a right-justified mask field. The bits in B0_3DATA_MASK
correspond to bytes 0–3 of the target bus. Each bit specifies whether the corresponding bit of the target bus should be inverted
or remain unmodified on read accesses. A successful write to this field clears the corresponding error injection channel valid
field, EICHEN[EICH n EN].
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
B0_3DATA_MA
SK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
B0_3DATA_MASK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-18
—
17-0 Data Mask Bytes 0-3
B0_3DATA_MA This field defines a bit-mapped mask that specifies whether the corresponding bit of the read data bus from
SK the target RAM should be inverted or remain unmodified. Writes to unimplemented bits are ignored.
NOTE
For the specific DATA_MASK bits to which B0_3DATA_MASK corresponds, See Error
injection channel descriptor: DATA_MASK details.
0b - The corresponding bit of bytes 0-3 on the read data bus remains unmodified.
1b - The corresponding bit of bytes 0-3 on the read data bus is inverted.

---

*Page 1369*

Error Injection Module (EIM)

#### 47.7 Glossary

SEC-DED Single error correction – double error detection
ECC Error correction code

---

*Page 1370*

