<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 9 -->

# Chapter 9

# System Integration Unit Lite2 (SIUL2)

#### 9.1 Chip-specific SIUL2 information

#### 9.1.1 Feature availability

This chip:
• Supports input filter enable (MSCR5[IFE]) only for the reset pad (PTA5). For details, see the "Pad signal description" table in
the "Signal Multiplexing" chapter.
• Does not implement the open-drain feature. LPI2C directly configures only the pads related to the I2C and LPUART
functions in pseudo open-drain when these functions are muxed to the pads. See the LPI2C and LPUART chapters for
more information.
• Reserves GPDO[25:24] and PGPDO1[7:6] because PTA24 and PTA25 are input-only pins.
NOTE
EIRQ[0-15] can be used either for interrupt or DMA request. EIRQ[16-31] can only be used for interrupt request.

#### 9.1.2 Mapping of MSCR and IMCR instances

• CR numbers 0-511 correspond to the MSCR instances.
• CR numbers 512-1023 correspond to the IMCR instances. IMCRs defined in the attached IOMUX file have an offset of
512 with respect to the IMCR number defined in SIUL2 memory map section.
NOTE
IMCR register only supports 32 bit access, any other access might result in unexpected data. See IOMUX file
attached to this document for the exact number of IMCR and MSCR register instances.

#### 9.1.3 SIUL2 register configuration

There are additional registers as compared to what is mentioned in section 'SIUL2 memory map'. See following table for details.
Table 33. Registers and applicability
Register Offset MCXE315/MCXE316 MCXE317 MCXE31B
MUX0_EMIOS_EN1 100h Yes No No
MUX0_MISC_EN 104h Yes No No
MUX1_EMIOS_EN 108h Yes No No
MUX1_MISC_EN 10Ch Yes No No
UDR1 100h No Yes No
UDR0 104h No Yes No
UDR3 108h No Yes No
UDR2 10Ch No Yes No

---

*Page 114*

System Integration Unit Lite2 (SIUL2)

#### 9.2 Overview

SIUL2 provides control over all electrical pin controls and ports with 16 bits of bidirectional, general-purpose input and output (I/O)
signals. It also performs the following functions:
• Enables you to select the functions and electrical characteristics that appear on external chip pins
• Controls the multiplexing of internal signals from one module to another and controls the chip I/O
• Supports a maximum of 32 external interrupts with trigger event configuration

#### 9.2.1 Block diagram

SIUL2 provides a dedicated pad control to general-purpose pads that you can configure as either inputs or outputs. It provides
registers for you to read values from GPIO pads configured as inputs and to write values to GPIO pads configured as outputs.
Based on the configuration of GPIO, you can:
• Write to an internal register to control the state driven on the associated output pad (when configured as output).
• Detect the state of the associated pad by reading the value from an internal register (when configured as input).
• Read back the pad value to check whether the written value appears on the pad (when configured as input and output).
Access the GPIO data registers in the following ways to allow port access and bit manipulation without read-modify-
write operations:
• Access to two 16 -bit ports in one access
• Read/write access to a single bit
• 16-bit port write with a bit mask using a single 32-bit access
You can configure:
• External interrupt sources at the chip level to be used with any chip pad.
• Interrupt sources to have a digital filter to reject short glitches on the inputs.
The external interrupt or DMA requests map to the interrupt request pins (REQ) in the chip packages.

---

*Page 115*

System Integration Unit Lite2 (SIUL2)
SIUL2
Pad control and pin muxing IP modules
MSCR and IMCR registers
IOMUX Pads
GPIO
Data registers
IPS master
Interrupts and DMA requests
Interrupt controller
- Interrupt configuration
- Glitch filter
DMA
IPS
bus
Chip-specific registers
Registers Miscellaneous logic
Figure 16. Block diagram

#### 9.2.2 Features

SIUL2 supports the following:
• 1 to 32 GPIO ports with data control:
— Drives data to as many as 16 independent I/O channels
— Samples data from as many as 16 independent I/O channels
• Read or write of two 16-bit registers with one access for a 32-bit port
• External interrupt and DMA requests:
— 1 to 32 programmable digital glitch filters, one for each interrupt REQ pin
— Edge detection
• Multiplexed Signal Configuration Registers (MSCR) to configure the electrical parameters and settings for as many as 512
functional pads.
See the interrupt map file and the DMAMUX map file attached to this document for mapping of interrupt and DMA sources to
interrupt vectors and DMA channels.

---

*Page 116*

System Integration Unit Lite2 (SIUL2)

#### 9.3 Functional description

#### 9.3.1 Pad control

SIUL2 controls the electrical characteristics of around 512 pads. It provides a consistent interface for all pads, both on a by-port
and a by-bit basis.
The setting of each pad out of reset is fixed per chip but you can configure it individually. This way you can select special pull
settings or peripheral pad ownership.
You can configure each pad independently of all other pads on the chip or other pads grouped within a single port, therefore
allowing grouping of different pad types together in ports and operating the pads individually. Grouping the various functions for
each pad into a single register allows configuration of each pad with a single write to a register, which further allows you to duplicate
software for similar pads with index changes.

#### 9.3.2 General-purpose input and output pads ( GPIO )

SIUL2 allows each pad to be configured as either of the following:
• General-purpose input or output pad (GPIO)
• A pad for one or more alternate functions (input or output) determined by the peripheral that uses the pad
You can also implement the GPIO pads without any alternate function.
SIUL2 manages as many as 512 GPIO pads organized as ports that can be accessed for data reads and writes as 8-bit, 16-bit,
or 32-bit.
All port accesses are identical, with each read or write performed only at a different location to access a different port width.
31 23 15 7 0
Base 32-bit port
15 7 0 15 7 0
Base+2h 16-bit port Base 16-bit port
7 0 7 0 7 0 7 0
Base+3h 8-bit port Base+2h 8-bit port Base+1h 8-bit port Base 8-bit port
Figure 17. Data port example arrangement showing configuration for different port width accesses
SIUL2 has separate data input and data output registers for all pads. You can therefore directly read back an input or output value
of a pad to validate what is present on the pad instead of confirming the value that was written to the data input registers. The data
output registers support both read and write operations whereas the data input registers support only the read access.
When you configure a pad for using one of its alternate functions, the data input values reflect the respective value of the pad. If
you perform a write operation to the data output register for a pad configured as an alternate function (non- GPIO ), this write is not
reflected by the pad value until reconfigured to GPIO. All general-purpose pads are implemented as bidirectional.
If a bidirectional operation impacts the performance or is not needed for a pad function, you can limit the functionality of the pad
to input-only.

#### 9.3.3 Clocking

This module has no clocking considerations.

#### 9.3.4 External interrupts

SIUL2 supports 1–32 external interrupts allocated to pads by the chip .
See the interrupt map file and the DMAMUX map file attached to this document (device reference manual) for mapping of interrupt
and DMA sources to interrupt vectors and DMA channels .

---

*Page 117*

System Integration Unit Lite2 (SIUL2)
SIUL2 supports 1– 4 interrupt vector s to the interrupt controller of the chip . Each interrupt vector can support as many as eight
external interrupt sources from the chip pads.
All the external interrupt pads within a single group have equal priority. It is your responsibility to search for the application through
a group of sources in an appropriate way.
NOTE
Glitch filters applied to external interrupts require a running internal oscillator clock. If such a clock is not available,
enabling the glitch filter on an external interrupt disables the interrupt.
The external interrupt signals from a pad have internal synchronizers. Therefore, the width of the interrupt signals should be at
least 2.5 or 3 times the internal RC oscillator (IRC) clock cycles to correctly capture the interrupts.
9.3.4.1 External interrupt initialization
Perform the following procedure to enable external interrupts (if you do not perform these steps, you may get a false interrupt flag
during interrupt initialization):
1. Write 1 to the appropriate IFER[IFE n ] fields to enable the glitch filter.
2. Write 0 to DIRER0[EIRE n ] to mask interrupts.
3. Write 1 to the appropriate IREE n fields in Interrupt Rising-Edge Event Enable 0 (IREER0) and IFEE n fields in Interrupt
Falling-Edge Event Enable 0 (IFEER0) as needed to select the pin polarity.
4. Configure the appropriate fields in MSCR for the external interrupt pins:
a. Write 0 to the OBE and ODE to disable the output.
b. Write 1 to IBE field to enable the input buffer of the pin.
c. If you are using the internal pull-up or pull-down resistors, configure the appropriate PUE and PUS fields.
NOTE
If you select external interrupt inputs for external interrupt pins, do not configure them as outputs (that is,
MSCR[OBE] must not be 0) because it can cause false interrupt detection (such as from a GPIO configuration).
5. Write to the appropriate DIRSR 0 [DIRS n ] fields to select a request between DMA or interrupt.
6. Select the desired glitch filter setup for the pins:
a. Write an appropriate value to IFMCR0[MAXCNT] for the respective external interrupt to the filter counter.
b.
c. Write to the appropriate IFER 0 [IFE n ] fields to enable the glitch filter for the external interrupt pins.
7. Set the appropriate DISR 0 [EIF n ] to clear any flags.
8. Write to the appropriate DIRER 0 [EIRE n ] fields to enable the interrupt pins.
NOTE
If you do not follow these steps you may get a false interrupt flag during interrupt initialization.
9.3.4.2 External interrupt management
You can enable or disable each interrupt independently using a single rolled up register, DIRER 0 .
You can configure a pad defined as an external interrupt to recognize interrupts with an active rising edge, an active falling edge,
or both, using Interrupt Rising-Edge Event Enable 0 (IREER0) and Interrupt Falling-Edge Event Enable 0 (IFEER0) .
NOTE
You cannot disable both edge events of a given interrupt.

---

*Page 118*

System Integration Unit Lite2 (SIUL2)
Each external interrupt has an individual flag, held in DMA or Interrupt Status Flag 0 (DISR0) . DISR0 is a clear-by-write-1 register,
which prevents inadvertent overwriting of other flags in the same register.
Figure 18 provides an overview of the external interrupt implementation.
Interrupt
vectors
Interrupt controller IRQ_31_24 IRQ_23_16 IRQ_15_08 IRQ_07_00
OR OR OR OR
Interrupt enable EIRE[31:0]
EIF[31:24] EIF[23:16] EIF[15:8] EIF[7:0]
Glitch filter prescaler
Interrupt edge
IFCP[3:0] enable rising
Glitch filter counter_n Edge detection IREE[31:0]
MAXCOUNT[x] Falling
IRQ glitch filter enable Glitch filter IFEE[31:0]
IFE[31:0]
Pads
Figure 18. External interrupt pad diagram
9.3.4.3 External interrupt request
The REQ input pins on the chip are the sources for interrupt or DMA requests . The chip provides one possible interrupt vector for
SIUL2. The 32 interrupt request sources map to vectors and channels as shown in Table 34 .
Table 34. Interrupt source mapping to SIUL2 interrupt request output for 32 interrupt sources
Vector or channel # Interrupt vector source
0 REQ[07] | REQ[06] | REQ[05] | REQ[04] | REQ[03] | REQ[02] | REQ[01] | REQ[00]
1 REQ[15] | REQ[14] | REQ[13] | REQ[12] | REQ[11] | REQ[10] | REQ[09] | REQ[08]
2 REQ[23] | REQ[22] | REQ[21] | REQ[20] | REQ[19] | REQ[18] | REQ[17] | REQ[16]
3 REQ[31] | REQ[30] | REQ[29] | REQ[28] | REQ[27] | REQ[26] | REQ[25] | REQ[24]

#### 9.3.5 DMA requests

The REQ pins on the chip map to the independent DMA request channels in the DMA controller. See the DMAMUX map file
attached to this document for the mapping of DMA sources to DMA channels.
DISR0 and DIRSR0 manage DMA requests in the following way:
• Service a DMA request to clear the DISR0 register flags.
• Write to the appropriate DIRSR0[DIRS n ] to select a request between DMA or interrupt.
• If you select DMA in DIRSR0 and set the corresponding DISR0 flag for it, SIUL2 sends a DMA request signal as an output.

---

*Page 119*

System Integration Unit Lite2 (SIUL2)

#### 9.4 External signal description

See the IOMUX file attached to this document for more information.

#### 9.5 Initialization

This module does not require initialization.

#### 9.6 SIUL2 register descriptions

This section describes the SIUL2 registers.
• Undocumented register spaces in the SIUL2 memory map, including addresses shown as blanks, are reserved:
— Reserved registers or spaces are read as 0.
— Writes to reserved registers or spaces generate a transfer error.
• Writes to read-only registers generate a transfer error.
NOTE
• For the array of 8-bit registers, GPDO n and GPDI n :
— An 8-bit access to an unimplemented address (a "hole") within the array region generates a
transfer error.
— However, when you perform a 16-bit or 32-bit access and if any register instance is implemented within
the accessed range, a transfer error is not generated even if the range includes a hole.
• For the array of 16-bit registers, PGPDO n and PGPDI n :
— A 16-bit access to an unimplemented address (a "hole") within the array region generates a
transfer error.
— However, a 32-bit access does not generate a transfer error for a hole irrespective of whether the other
16-bit range includes a register instance.

#### 9.6.1 SIUL2 memory map

SIUL2 base address: 4029_0000h
Offset Register Access Reset value
Width
(In bits)
4h SIUL2 MCU ID Register #1 (MIDR1) 32 R See section
8h SIUL2 MCU ID Register #2 (MIDR2) 32 R See section
10h DMA or Interrupt Status Flag 0 (DISR0) 32 RW 0000_0000h
18h DMA or Interrupt Request Enable 0 (DIRER0) 32 RW 0000_0000h
20h DMA or Interrupt Request Select 0 (DIRSR0) 32 RW 0000_0000h
28h Interrupt Rising-Edge Event Enable 0 (IREER0) 32 RW 0000_0000h
30h Interrupt Falling-Edge Event Enable 0 (IFEER0) 32 RW 0000_0000h
38h Interrupt Filter Enable 0 (IFER0) 32 RW 0000_0000h
40h - BCh Interrupt Filter Maximum Counter (IFMCR0 - IFMCR31) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 120*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
200h SIUL2 MCU ID Register #3 (MIDR3) 32 R See section
204h SIUL2 MCU ID Register #4 (MIDR4) 32 R See section
240h - 74Ch Multiplexed Signal Configuration (MSCR0 - MSCR323) 32 RW See section
A40h - 11A4h Input Multiplexed Signal Configuration (IMCR0 - IMCR473) 32 RW 0000_0000h
1300h GPIO Pad Data Output (GPDO3) 8 RW 00h
1301h GPIO Pad Data Output (GPDO2) 8 RW 00h
1302h GPIO Pad Data Output (GPDO1) 8 RW 00h
1303h GPIO Pad Data Output (GPDO0) 8 RW 00h
1304h GPIO Pad Data Output (GPDO7) 8 RW 00h
1305h GPIO Pad Data Output (GPDO6) 8 RW 00h
1306h GPIO Pad Data Output (GPDO5) 8 RW 00h
1307h GPIO Pad Data Output (GPDO4) 8 RW 00h
1308h GPIO Pad Data Output (GPDO11) 8 RW 00h
1309h GPIO Pad Data Output (GPDO10) 8 RW 00h
130Ah GPIO Pad Data Output (GPDO9) 8 RW 00h
130Bh GPIO Pad Data Output (GPDO8) 8 RW 00h
130Ch GPIO Pad Data Output (GPDO15) 8 RW 00h
130Dh GPIO Pad Data Output (GPDO14) 8 RW 00h
130Eh GPIO Pad Data Output (GPDO13) 8 RW 00h
130Fh GPIO Pad Data Output (GPDO12) 8 RW 00h
1310h GPIO Pad Data Output (GPDO19) 8 RW 00h
1311h GPIO Pad Data Output (GPDO18) 8 RW 00h
1312h GPIO Pad Data Output (GPDO17) 8 RW 00h
1313h GPIO Pad Data Output (GPDO16) 8 RW 00h
1314h GPIO Pad Data Output (GPDO23) 8 RW 00h
1315h GPIO Pad Data Output (GPDO22) 8 RW 00h
1316h GPIO Pad Data Output (GPDO21) 8 RW 00h
1317h GPIO Pad Data Output (GPDO20) 8 RW 00h
1318h GPIO Pad Data Output (GPDO27) 8 RW 00h
1319h GPIO Pad Data Output (GPDO26) 8 RW 00h
131Ah GPIO Pad Data Output (GPDO25) 8 RW 00h
Table continues on the next page...

---

*Page 121*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
131Bh GPIO Pad Data Output (GPDO24) 8 RW 00h
131Ch GPIO Pad Data Output (GPDO31) 8 RW 00h
131Dh GPIO Pad Data Output (GPDO30) 8 RW 00h
131Eh GPIO Pad Data Output (GPDO29) 8 RW 00h
131Fh GPIO Pad Data Output (GPDO28) 8 RW 00h
1320h GPIO Pad Data Output (GPDO35) 8 RW 00h
1321h GPIO Pad Data Output (GPDO34) 8 RW 00h
1322h GPIO Pad Data Output (GPDO33) 8 RW 00h
1323h GPIO Pad Data Output (GPDO32) 8 RW 00h
1326h GPIO Pad Data Output (GPDO37) 8 RW 00h
1327h GPIO Pad Data Output (GPDO36) 8 RW 00h
1328h GPIO Pad Data Output (GPDO43) 8 RW 00h
1329h GPIO Pad Data Output (GPDO42) 8 RW 00h
132Ah GPIO Pad Data Output (GPDO41) 8 RW 00h
132Bh GPIO Pad Data Output (GPDO40) 8 RW 00h
132Ch GPIO Pad Data Output (GPDO47) 8 RW 00h
132Dh GPIO Pad Data Output (GPDO46) 8 RW 00h
132Eh GPIO Pad Data Output (GPDO45) 8 RW 00h
132Fh GPIO Pad Data Output (GPDO44) 8 RW 00h
1330h GPIO Pad Data Output (GPDO51) 8 RW 00h
1331h GPIO Pad Data Output (GPDO50) 8 RW 00h
1332h GPIO Pad Data Output (GPDO49) 8 RW 00h
1333h GPIO Pad Data Output (GPDO48) 8 RW 00h
1334h GPIO Pad Data Output (GPDO55) 8 RW 00h
1335h GPIO Pad Data Output (GPDO54) 8 RW 00h
1336h GPIO Pad Data Output (GPDO53) 8 RW 00h
1337h GPIO Pad Data Output (GPDO52) 8 RW 00h
1338h GPIO Pad Data Output (GPDO59) 8 RW 00h
1339h GPIO Pad Data Output (GPDO58) 8 RW 00h
133Ah GPIO Pad Data Output (GPDO57) 8 RW 00h
133Bh GPIO Pad Data Output (GPDO56) 8 RW 00h
Table continues on the next page...

---

*Page 122*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
133Ch GPIO Pad Data Output (GPDO63) 8 RW 00h
133Dh GPIO Pad Data Output (GPDO62) 8 RW 00h
133Eh GPIO Pad Data Output (GPDO61) 8 RW 00h
133Fh GPIO Pad Data Output (GPDO60) 8 RW 00h
1340h GPIO Pad Data Output (GPDO67) 8 RW 00h
1341h GPIO Pad Data Output (GPDO66) 8 RW 00h
1342h GPIO Pad Data Output (GPDO65) 8 RW 00h
1343h GPIO Pad Data Output (GPDO64) 8 RW 00h
1344h GPIO Pad Data Output (GPDO71) 8 RW 00h
1345h GPIO Pad Data Output (GPDO70) 8 RW 00h
1346h GPIO Pad Data Output (GPDO69) 8 RW 00h
1347h GPIO Pad Data Output (GPDO68) 8 RW 00h
1348h GPIO Pad Data Output (GPDO75) 8 RW 00h
1349h GPIO Pad Data Output (GPDO74) 8 RW 00h
134Ah GPIO Pad Data Output (GPDO73) 8 RW 00h
134Bh GPIO Pad Data Output (GPDO72) 8 RW 00h
134Ch GPIO Pad Data Output (GPDO79) 8 RW 00h
134Dh GPIO Pad Data Output (GPDO78) 8 RW 00h
134Eh GPIO Pad Data Output (GPDO77) 8 RW 00h
134Fh GPIO Pad Data Output (GPDO76) 8 RW 00h
1350h GPIO Pad Data Output (GPDO83) 8 RW 00h
1351h GPIO Pad Data Output (GPDO82) 8 RW 00h
1352h GPIO Pad Data Output (GPDO81) 8 RW 00h
1353h GPIO Pad Data Output (GPDO80) 8 RW 00h
1354h GPIO Pad Data Output (GPDO87) 8 RW 00h
1355h GPIO Pad Data Output (GPDO86) 8 RW 00h
1356h GPIO Pad Data Output (GPDO85) 8 RW 00h
1357h GPIO Pad Data Output (GPDO84) 8 RW 00h
1358h GPIO Pad Data Output (GPDO91) 8 RW 00h
1359h GPIO Pad Data Output (GPDO90) 8 RW 00h
135Ah GPIO Pad Data Output (GPDO89) 8 RW 00h
Table continues on the next page...

---

*Page 123*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
135Bh GPIO Pad Data Output (GPDO88) 8 RW 00h
135Ch GPIO Pad Data Output (GPDO95) 8 RW 00h
135Dh GPIO Pad Data Output (GPDO94) 8 RW 00h
135Eh GPIO Pad Data Output (GPDO93) 8 RW 00h
135Fh GPIO Pad Data Output (GPDO92) 8 RW 00h
1360h GPIO Pad Data Output (GPDO99) 8 RW 00h
1361h GPIO Pad Data Output (GPDO98) 8 RW 00h
1362h GPIO Pad Data Output (GPDO97) 8 RW 00h
1363h GPIO Pad Data Output (GPDO96) 8 RW 00h
1364h GPIO Pad Data Output (GPDO103) 8 RW 00h
1365h GPIO Pad Data Output (GPDO102) 8 RW 00h
1366h GPIO Pad Data Output (GPDO101) 8 RW 00h
1367h GPIO Pad Data Output (GPDO100) 8 RW 00h
1368h GPIO Pad Data Output (GPDO107) 8 RW 00h
1369h GPIO Pad Data Output (GPDO106) 8 RW 00h
136Ah GPIO Pad Data Output (GPDO105) 8 RW 00h
136Bh GPIO Pad Data Output (GPDO104) 8 RW 00h
136Ch GPIO Pad Data Output (GPDO111) 8 RW 00h
136Dh GPIO Pad Data Output (GPDO110) 8 RW 00h
136Eh GPIO Pad Data Output (GPDO109) 8 RW 00h
136Fh GPIO Pad Data Output (GPDO108) 8 RW 00h
1370h GPIO Pad Data Output (GPDO115) 8 RW 00h
1371h GPIO Pad Data Output (GPDO114) 8 RW 00h
1372h GPIO Pad Data Output (GPDO113) 8 RW 00h
1373h GPIO Pad Data Output (GPDO112) 8 RW 00h
1374h GPIO Pad Data Output (GPDO119) 8 RW 00h
1375h GPIO Pad Data Output (GPDO118) 8 RW 00h
1376h GPIO Pad Data Output (GPDO117) 8 RW 00h
1377h GPIO Pad Data Output (GPDO116) 8 RW 00h
1378h GPIO Pad Data Output (GPDO123) 8 RW 00h
1379h GPIO Pad Data Output (GPDO122) 8 RW 00h
Table continues on the next page...

---

*Page 124*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
137Ah GPIO Pad Data Output (GPDO121) 8 RW 00h
137Bh GPIO Pad Data Output (GPDO120) 8 RW 00h
137Ch GPIO Pad Data Output (GPDO127) 8 RW 00h
137Dh GPIO Pad Data Output (GPDO126) 8 RW 00h
137Eh GPIO Pad Data Output (GPDO125) 8 RW 00h
137Fh GPIO Pad Data Output (GPDO124) 8 RW 00h
1380h GPIO Pad Data Output (GPDO131) 8 RW 00h
1381h GPIO Pad Data Output (GPDO130) 8 RW 00h
1382h GPIO Pad Data Output (GPDO129) 8 RW 00h
1383h GPIO Pad Data Output (GPDO128) 8 RW 00h
1384h GPIO Pad Data Output (GPDO135) 8 RW 00h
1385h GPIO Pad Data Output (GPDO134) 8 RW 00h
1386h GPIO Pad Data Output (GPDO133) 8 RW 00h
1387h GPIO Pad Data Output (GPDO132) 8 RW 00h
1388h GPIO Pad Data Output (GPDO139) 8 RW 00h
1389h GPIO Pad Data Output (GPDO138) 8 RW 00h
138Ah GPIO Pad Data Output (GPDO137) 8 RW 00h
138Bh GPIO Pad Data Output (GPDO136) 8 RW 00h
138Ch GPIO Pad Data Output (GPDO143) 8 RW 00h
138Dh GPIO Pad Data Output (GPDO142) 8 RW 00h
138Eh GPIO Pad Data Output (GPDO141) 8 RW 00h
138Fh GPIO Pad Data Output (GPDO140) 8 RW 00h
1390h GPIO Pad Data Output (GPDO147) 8 RW 00h
1391h GPIO Pad Data Output (GPDO146) 8 RW 00h
1392h GPIO Pad Data Output (GPDO145) 8 RW 00h
1393h GPIO Pad Data Output (GPDO144) 8 RW 00h
1394h GPIO Pad Data Output (GPDO151) 8 RW 00h
1395h GPIO Pad Data Output (GPDO150) 8 RW 00h
1396h GPIO Pad Data Output (GPDO149) 8 RW 00h
1397h GPIO Pad Data Output (GPDO148) 8 RW 00h
1398h GPIO Pad Data Output (GPDO155) 8 RW 00h
Table continues on the next page...

---

*Page 125*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
1399h GPIO Pad Data Output (GPDO154) 8 RW 00h
139Ah GPIO Pad Data Output (GPDO153) 8 RW 00h
139Bh GPIO Pad Data Output (GPDO152) 8 RW 00h
139Ch GPIO Pad Data Output (GPDO159) 8 RW 00h
139Dh GPIO Pad Data Output (GPDO158) 8 RW 00h
139Eh GPIO Pad Data Output (GPDO157) 8 RW 00h
139Fh GPIO Pad Data Output (GPDO156) 8 RW 00h
13A0h GPIO Pad Data Output (GPDO163) 8 RW 00h
13A1h GPIO Pad Data Output (GPDO162) 8 RW 00h
13A2h GPIO Pad Data Output (GPDO161) 8 RW 00h
13A3h GPIO Pad Data Output (GPDO160) 8 RW 00h
13A4h GPIO Pad Data Output (GPDO167) 8 RW 00h
13A5h GPIO Pad Data Output (GPDO166) 8 RW 00h
13A6h GPIO Pad Data Output (GPDO165) 8 RW 00h
13A7h GPIO Pad Data Output (GPDO164) 8 RW 00h
13A8h GPIO Pad Data Output (GPDO171) 8 RW 00h
13A9h GPIO Pad Data Output (GPDO170) 8 RW 00h
13AAh GPIO Pad Data Output (GPDO169) 8 RW 00h
13ABh GPIO Pad Data Output (GPDO168) 8 RW 00h
13ACh GPIO Pad Data Output (GPDO175) 8 RW 00h
13ADh GPIO Pad Data Output (GPDO174) 8 RW 00h
13AEh GPIO Pad Data Output (GPDO173) 8 RW 00h
13AFh GPIO Pad Data Output (GPDO172) 8 RW 00h
13B0h GPIO Pad Data Output (GPDO179) 8 RW 00h
13B1h GPIO Pad Data Output (GPDO178) 8 RW 00h
13B2h GPIO Pad Data Output (GPDO177) 8 RW 00h
13B3h GPIO Pad Data Output (GPDO176) 8 RW 00h
13B4h GPIO Pad Data Output (GPDO183) 8 RW 00h
13B5h GPIO Pad Data Output (GPDO182) 8 RW 00h
13B6h GPIO Pad Data Output (GPDO181) 8 RW 00h
13B7h GPIO Pad Data Output (GPDO180) 8 RW 00h
Table continues on the next page...

---

*Page 126*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
13B8h GPIO Pad Data Output (GPDO187) 8 RW 00h
13B9h GPIO Pad Data Output (GPDO186) 8 RW 00h
13BAh GPIO Pad Data Output (GPDO185) 8 RW 00h
13BBh GPIO Pad Data Output (GPDO184) 8 RW 00h
13BCh GPIO Pad Data Output (GPDO191) 8 RW 00h
13BDh GPIO Pad Data Output (GPDO190) 8 RW 00h
13BEh GPIO Pad Data Output (GPDO189) 8 RW 00h
13BFh GPIO Pad Data Output (GPDO188) 8 RW 00h
13C0h GPIO Pad Data Output (GPDO195) 8 RW 00h
13C1h GPIO Pad Data Output (GPDO194) 8 RW 00h
13C2h GPIO Pad Data Output (GPDO193) 8 RW 00h
13C3h GPIO Pad Data Output (GPDO192) 8 RW 00h
13C4h GPIO Pad Data Output (GPDO199) 8 RW 00h
13C5h GPIO Pad Data Output (GPDO198) 8 RW 00h
13C6h GPIO Pad Data Output (GPDO197) 8 RW 00h
13C7h GPIO Pad Data Output (GPDO196) 8 RW 00h
13C8h GPIO Pad Data Output (GPDO203) 8 RW 00h
13C9h GPIO Pad Data Output (GPDO202) 8 RW 00h
13CAh GPIO Pad Data Output (GPDO201) 8 RW 00h
13CBh GPIO Pad Data Output (GPDO200) 8 RW 00h
13CCh GPIO Pad Data Output (GPDO207) 8 RW 00h
13CDh GPIO Pad Data Output (GPDO206) 8 RW 00h
13CEh GPIO Pad Data Output (GPDO205) 8 RW 00h
13CFh GPIO Pad Data Output (GPDO204) 8 RW 00h
13D0h GPIO Pad Data Output (GPDO211) 8 RW 00h
13D1h GPIO Pad Data Output (GPDO210) 8 RW 00h
13D2h GPIO Pad Data Output (GPDO209) 8 RW 00h
13D3h GPIO Pad Data Output (GPDO208) 8 RW 00h
13D4h GPIO Pad Data Output (GPDO215) 8 RW 00h
13D5h GPIO Pad Data Output (GPDO214) 8 RW 00h
13D6h GPIO Pad Data Output (GPDO213) 8 RW 00h
Table continues on the next page...

---

*Page 127*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
13D7h GPIO Pad Data Output (GPDO212) 8 RW 00h
13D8h GPIO Pad Data Output (GPDO219) 8 RW 00h
13D9h GPIO Pad Data Output (GPDO218) 8 RW 00h
13DAh GPIO Pad Data Output (GPDO217) 8 RW 00h
13DBh GPIO Pad Data Output (GPDO216) 8 RW 00h
1500h GPIO Pad Data Input (GPDI3) 8 R 00h
1501h GPIO Pad Data Input (GPDI2) 8 R 00h
1502h GPIO Pad Data Input (GPDI1) 8 R 00h
1503h GPIO Pad Data Input (GPDI0) 8 R 00h
1504h GPIO Pad Data Input (GPDI7) 8 R 00h
1505h GPIO Pad Data Input (GPDI6) 8 R 00h
1506h GPIO Pad Data Input (GPDI5) 8 R 00h
1507h GPIO Pad Data Input (GPDI4) 8 R 00h
1508h GPIO Pad Data Input (GPDI11) 8 R 00h
1509h GPIO Pad Data Input (GPDI10) 8 R 00h
150Ah GPIO Pad Data Input (GPDI9) 8 R 00h
150Bh GPIO Pad Data Input (GPDI8) 8 R 00h
150Ch GPIO Pad Data Input (GPDI15) 8 R 00h
150Dh GPIO Pad Data Input (GPDI14) 8 R 00h
150Eh GPIO Pad Data Input (GPDI13) 8 R 00h
150Fh GPIO Pad Data Input (GPDI12) 8 R 00h
1510h GPIO Pad Data Input (GPDI19) 8 R 00h
1511h GPIO Pad Data Input (GPDI18) 8 R 00h
1512h GPIO Pad Data Input (GPDI17) 8 R 00h
1513h GPIO Pad Data Input (GPDI16) 8 R 00h
1514h GPIO Pad Data Input (GPDI23) 8 R 00h
1515h GPIO Pad Data Input (GPDI22) 8 R 00h
1516h GPIO Pad Data Input (GPDI21) 8 R 00h
1517h GPIO Pad Data Input (GPDI20) 8 R 00h
1518h GPIO Pad Data Input (GPDI27) 8 R 00h
1519h GPIO Pad Data Input (GPDI26) 8 R 00h
Table continues on the next page...

---

*Page 128*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
151Ah GPIO Pad Data Input (GPDI25) 8 R 00h
151Bh GPIO Pad Data Input (GPDI24) 8 R 00h
151Ch GPIO Pad Data Input (GPDI31) 8 R 00h
151Dh GPIO Pad Data Input (GPDI30) 8 R 00h
151Eh GPIO Pad Data Input (GPDI29) 8 R 00h
151Fh GPIO Pad Data Input (GPDI28) 8 R 00h
1520h GPIO Pad Data Input (GPDI35) 8 R 00h
1521h GPIO Pad Data Input (GPDI34) 8 R 00h
1522h GPIO Pad Data Input (GPDI33) 8 R 00h
1523h GPIO Pad Data Input (GPDI32) 8 R 00h
1526h GPIO Pad Data Input (GPDI37) 8 R 00h
1527h GPIO Pad Data Input (GPDI36) 8 R 00h
1528h GPIO Pad Data Input (GPDI43) 8 R 00h
1529h GPIO Pad Data Input (GPDI42) 8 R 00h
152Ah GPIO Pad Data Input (GPDI41) 8 R 00h
152Bh GPIO Pad Data Input (GPDI40) 8 R 00h
152Ch GPIO Pad Data Input (GPDI47) 8 R 00h
152Dh GPIO Pad Data Input (GPDI46) 8 R 00h
152Eh GPIO Pad Data Input (GPDI45) 8 R 00h
152Fh GPIO Pad Data Input (GPDI44) 8 R 00h
1530h GPIO Pad Data Input (GPDI51) 8 R 00h
1531h GPIO Pad Data Input (GPDI50) 8 R 00h
1532h GPIO Pad Data Input (GPDI49) 8 R 00h
1533h GPIO Pad Data Input (GPDI48) 8 R 00h
1534h GPIO Pad Data Input (GPDI55) 8 R 00h
1535h GPIO Pad Data Input (GPDI54) 8 R 00h
1536h GPIO Pad Data Input (GPDI53) 8 R 00h
1537h GPIO Pad Data Input (GPDI52) 8 R 00h
1538h GPIO Pad Data Input (GPDI59) 8 R 00h
1539h GPIO Pad Data Input (GPDI58) 8 R 00h
153Ah GPIO Pad Data Input (GPDI57) 8 R 00h
Table continues on the next page...

---

*Page 129*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
153Bh GPIO Pad Data Input (GPDI56) 8 R 00h
153Ch GPIO Pad Data Input (GPDI63) 8 R 00h
153Dh GPIO Pad Data Input (GPDI62) 8 R 00h
153Eh GPIO Pad Data Input (GPDI61) 8 R 00h
153Fh GPIO Pad Data Input (GPDI60) 8 R 00h
1540h GPIO Pad Data Input (GPDI67) 8 R 00h
1541h GPIO Pad Data Input (GPDI66) 8 R 00h
1542h GPIO Pad Data Input (GPDI65) 8 R 00h
1543h GPIO Pad Data Input (GPDI64) 8 R 00h
1544h GPIO Pad Data Input (GPDI71) 8 R 00h
1545h GPIO Pad Data Input (GPDI70) 8 R 00h
1546h GPIO Pad Data Input (GPDI69) 8 R 00h
1547h GPIO Pad Data Input (GPDI68) 8 R 00h
1548h GPIO Pad Data Input (GPDI75) 8 R 00h
1549h GPIO Pad Data Input (GPDI74) 8 R 00h
154Ah GPIO Pad Data Input (GPDI73) 8 R 00h
154Bh GPIO Pad Data Input (GPDI72) 8 R 00h
154Ch GPIO Pad Data Input (GPDI79) 8 R 00h
154Dh GPIO Pad Data Input (GPDI78) 8 R 00h
154Eh GPIO Pad Data Input (GPDI77) 8 R 00h
154Fh GPIO Pad Data Input (GPDI76) 8 R 00h
1550h GPIO Pad Data Input (GPDI83) 8 R 00h
1551h GPIO Pad Data Input (GPDI82) 8 R 00h
1552h GPIO Pad Data Input (GPDI81) 8 R 00h
1553h GPIO Pad Data Input (GPDI80) 8 R 00h
1554h GPIO Pad Data Input (GPDI87) 8 R 00h
1555h GPIO Pad Data Input (GPDI86) 8 R 00h
1556h GPIO Pad Data Input (GPDI85) 8 R 00h
1557h GPIO Pad Data Input (GPDI84) 8 R 00h
1558h GPIO Pad Data Input (GPDI91) 8 R 00h
1559h GPIO Pad Data Input (GPDI90) 8 R 00h
Table continues on the next page...

---

*Page 130*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
155Ah GPIO Pad Data Input (GPDI89) 8 R 00h
155Bh GPIO Pad Data Input (GPDI88) 8 R 00h
155Ch GPIO Pad Data Input (GPDI95) 8 R 00h
155Dh GPIO Pad Data Input (GPDI94) 8 R 00h
155Eh GPIO Pad Data Input (GPDI93) 8 R 00h
155Fh GPIO Pad Data Input (GPDI92) 8 R 00h
1560h GPIO Pad Data Input (GPDI99) 8 R 00h
1561h GPIO Pad Data Input (GPDI98) 8 R 00h
1562h GPIO Pad Data Input (GPDI97) 8 R 00h
1563h GPIO Pad Data Input (GPDI96) 8 R 00h
1564h GPIO Pad Data Input (GPDI103) 8 R 00h
1565h GPIO Pad Data Input (GPDI102) 8 R 00h
1566h GPIO Pad Data Input (GPDI101) 8 R 00h
1567h GPIO Pad Data Input (GPDI100) 8 R 00h
1568h GPIO Pad Data Input (GPDI107) 8 R 00h
1569h GPIO Pad Data Input (GPDI106) 8 R 00h
156Ah GPIO Pad Data Input (GPDI105) 8 R 00h
156Bh GPIO Pad Data Input (GPDI104) 8 R 00h
156Ch GPIO Pad Data Input (GPDI111) 8 R 00h
156Dh GPIO Pad Data Input (GPDI110) 8 R 00h
156Eh GPIO Pad Data Input (GPDI109) 8 R 00h
156Fh GPIO Pad Data Input (GPDI108) 8 R 00h
1570h GPIO Pad Data Input (GPDI115) 8 R 00h
1571h GPIO Pad Data Input (GPDI114) 8 R 00h
1572h GPIO Pad Data Input (GPDI113) 8 R 00h
1573h GPIO Pad Data Input (GPDI112) 8 R 00h
1574h GPIO Pad Data Input (GPDI119) 8 R 00h
1575h GPIO Pad Data Input (GPDI118) 8 R 00h
1576h GPIO Pad Data Input (GPDI117) 8 R 00h
1577h GPIO Pad Data Input (GPDI116) 8 R 00h
1578h GPIO Pad Data Input (GPDI123) 8 R 00h
Table continues on the next page...

---

*Page 131*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
1579h GPIO Pad Data Input (GPDI122) 8 R 00h
157Ah GPIO Pad Data Input (GPDI121) 8 R 00h
157Bh GPIO Pad Data Input (GPDI120) 8 R 00h
157Ch GPIO Pad Data Input (GPDI127) 8 R 00h
157Dh GPIO Pad Data Input (GPDI126) 8 R 00h
157Eh GPIO Pad Data Input (GPDI125) 8 R 00h
157Fh GPIO Pad Data Input (GPDI124) 8 R 00h
1580h GPIO Pad Data Input (GPDI131) 8 R 00h
1581h GPIO Pad Data Input (GPDI130) 8 R 00h
1582h GPIO Pad Data Input (GPDI129) 8 R 00h
1583h GPIO Pad Data Input (GPDI128) 8 R 00h
1584h GPIO Pad Data Input (GPDI135) 8 R 00h
1585h GPIO Pad Data Input (GPDI134) 8 R 00h
1586h GPIO Pad Data Input (GPDI133) 8 R 00h
1587h GPIO Pad Data Input (GPDI132) 8 R 00h
1588h GPIO Pad Data Input (GPDI139) 8 R 00h
1589h GPIO Pad Data Input (GPDI138) 8 R 00h
158Ah GPIO Pad Data Input (GPDI137) 8 R 00h
158Bh GPIO Pad Data Input (GPDI136) 8 R 00h
158Ch GPIO Pad Data Input (GPDI143) 8 R 00h
158Dh GPIO Pad Data Input (GPDI142) 8 R 00h
158Eh GPIO Pad Data Input (GPDI141) 8 R 00h
158Fh GPIO Pad Data Input (GPDI140) 8 R 00h
1590h GPIO Pad Data Input (GPDI147) 8 R 00h
1591h GPIO Pad Data Input (GPDI146) 8 R 00h
1592h GPIO Pad Data Input (GPDI145) 8 R 00h
1593h GPIO Pad Data Input (GPDI144) 8 R 00h
1594h GPIO Pad Data Input (GPDI151) 8 R 00h
1595h GPIO Pad Data Input (GPDI150) 8 R 00h
1596h GPIO Pad Data Input (GPDI149) 8 R 00h
1597h GPIO Pad Data Input (GPDI148) 8 R 00h
Table continues on the next page...

---

*Page 132*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
1598h GPIO Pad Data Input (GPDI155) 8 R 00h
1599h GPIO Pad Data Input (GPDI154) 8 R 00h
159Ah GPIO Pad Data Input (GPDI153) 8 R 00h
159Bh GPIO Pad Data Input (GPDI152) 8 R 00h
159Ch GPIO Pad Data Input (GPDI159) 8 R 00h
159Dh GPIO Pad Data Input (GPDI158) 8 R 00h
159Eh GPIO Pad Data Input (GPDI157) 8 R 00h
159Fh GPIO Pad Data Input (GPDI156) 8 R 00h
15A0h GPIO Pad Data Input (GPDI163) 8 R 00h
15A1h GPIO Pad Data Input (GPDI162) 8 R 00h
15A2h GPIO Pad Data Input (GPDI161) 8 R 00h
15A3h GPIO Pad Data Input (GPDI160) 8 R 00h
15A4h GPIO Pad Data Input (GPDI167) 8 R 00h
15A5h GPIO Pad Data Input (GPDI166) 8 R 00h
15A6h GPIO Pad Data Input (GPDI165) 8 R 00h
15A7h GPIO Pad Data Input (GPDI164) 8 R 00h
15A8h GPIO Pad Data Input (GPDI171) 8 R 00h
15A9h GPIO Pad Data Input (GPDI170) 8 R 00h
15AAh GPIO Pad Data Input (GPDI169) 8 R 00h
15ABh GPIO Pad Data Input (GPDI168) 8 R 00h
15ACh GPIO Pad Data Input (GPDI175) 8 R 00h
15ADh GPIO Pad Data Input (GPDI174) 8 R 00h
15AEh GPIO Pad Data Input (GPDI173) 8 R 00h
15AFh GPIO Pad Data Input (GPDI172) 8 R 00h
15B0h GPIO Pad Data Input (GPDI179) 8 R 00h
15B1h GPIO Pad Data Input (GPDI178) 8 R 00h
15B2h GPIO Pad Data Input (GPDI177) 8 R 00h
15B3h GPIO Pad Data Input (GPDI176) 8 R 00h
15B4h GPIO Pad Data Input (GPDI183) 8 R 00h
15B5h GPIO Pad Data Input (GPDI182) 8 R 00h
15B6h GPIO Pad Data Input (GPDI181) 8 R 00h
Table continues on the next page...

---

*Page 133*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
15B7h GPIO Pad Data Input (GPDI180) 8 R 00h
15B8h GPIO Pad Data Input (GPDI187) 8 R 00h
15B9h GPIO Pad Data Input (GPDI186) 8 R 00h
15BAh GPIO Pad Data Input (GPDI185) 8 R 00h
15BBh GPIO Pad Data Input (GPDI184) 8 R 00h
15BCh GPIO Pad Data Input (GPDI191) 8 R 00h
15BDh GPIO Pad Data Input (GPDI190) 8 R 00h
15BEh GPIO Pad Data Input (GPDI189) 8 R 00h
15BFh GPIO Pad Data Input (GPDI188) 8 R 00h
15C0h GPIO Pad Data Input (GPDI195) 8 R 00h
15C1h GPIO Pad Data Input (GPDI194) 8 R 00h
15C2h GPIO Pad Data Input (GPDI193) 8 R 00h
15C3h GPIO Pad Data Input (GPDI192) 8 R 00h
15C4h GPIO Pad Data Input (GPDI199) 8 R 00h
15C5h GPIO Pad Data Input (GPDI198) 8 R 00h
15C6h GPIO Pad Data Input (GPDI197) 8 R 00h
15C7h GPIO Pad Data Input (GPDI196) 8 R 00h
15C8h GPIO Pad Data Input (GPDI203) 8 R 00h
15C9h GPIO Pad Data Input (GPDI202) 8 R 00h
15CAh GPIO Pad Data Input (GPDI201) 8 R 00h
15CBh GPIO Pad Data Input (GPDI200) 8 R 00h
15CCh GPIO Pad Data Input (GPDI207) 8 R 00h
15CDh GPIO Pad Data Input (GPDI206) 8 R 00h
15CEh GPIO Pad Data Input (GPDI205) 8 R 00h
15CFh GPIO Pad Data Input (GPDI204) 8 R 00h
15D0h GPIO Pad Data Input (GPDI211) 8 R 00h
15D1h GPIO Pad Data Input (GPDI210) 8 R 00h
15D2h GPIO Pad Data Input (GPDI209) 8 R 00h
15D3h GPIO Pad Data Input (GPDI208) 8 R 00h
15D4h GPIO Pad Data Input (GPDI215) 8 R 00h
15D5h GPIO Pad Data Input (GPDI214) 8 R 00h
Table continues on the next page...

---

*Page 134*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
15D6h GPIO Pad Data Input (GPDI213) 8 R 00h
15D7h GPIO Pad Data Input (GPDI212) 8 R 00h
15D8h GPIO Pad Data Input (GPDI219) 8 R 00h
15D9h GPIO Pad Data Input (GPDI218) 8 R 00h
15DAh GPIO Pad Data Input (GPDI217) 8 R 00h
15DBh GPIO Pad Data Input (GPDI216) 8 R 00h
1
1700h - Parallel GPIO Pad Data Output (PGPDO0 - PGPDO3) 16 RW 0000h
1704h
1706h Parallel GPIO Pad Data Output (PGPDO2) 16 RW 0000h
1
1708h - Parallel GPIO Pad Data Output (PGPDO4 - PGPDO11) 16 RW 0000h
1716h
1718h Parallel GPIO Pad Data Output (PGPDO13) 16 RW 0000h
171Ah Parallel GPIO Pad Data Output (PGPDO12) 16 RW 0000h
1
1740h - Parallel GPIO Pad Data Input (PGPDI0 - PGPDI3) 16 R 0000h
1744h
1746h Parallel GPIO Pad Data Input (PGPDI2) 16 R 0000h
1
1748h - Parallel GPIO Pad Data Input (PGPDI4 - PGPDI11) 16 R 0000h
1756h
1758h Parallel GPIO Pad Data Input (PGPDI13) 16 R 0000h
175Ah Parallel GPIO Pad Data Input (PGPDI12) 16 R 0000h
1780h - Masked Parallel GPIO Pad Data Output (MPGPDO0 - MPGPDO1) 32 RW 0000_0000h
1784h
1788h Masked Parallel GPIO Pad Data Output (MPGPDO2) 32 RW 0000_0000h
178Ch - Masked Parallel GPIO Pad Data Output (MPGPDO3 - MPGPDO12) 32 RW 0000_0000h
17B0h
17B4h Masked Parallel GPIO Pad Data Output (MPGPDO13) 32 RW 0000_0000h
1. In this array, the index and offset values of the registers do not increment in direct alignment. For details, see the register
description.

#### 9.6.2 SIUL2 MCU ID Register #1 (MIDR1)

Offset
Register Offset
MIDR1 4h

---

*Page 135*

System Integration Unit Lite2 (SIUL2)
Function
This register holds identification information about the device.
NOTE
This register supports only 32-bit accesses. Byte and half-word accesses are not supported.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R PRODUCT_LINE_LETTER PART_NO
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R Reserved MAJOR_MASK MINOR_MASK
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
31-26 Product Line Letter
PRODUCT_LIN Identified the ASCII character in MCU Part Number. This field specifies the part number suffix, and needs
E_LETTER to be combined with MIDR1[PART_NO] to provide the full chip number.
0x0B K
This value is set at the factory and cannot be changed. All other values are reserved.
25-16 MCU Part Number
PART_NO MCXE315/MCXE316 - 0x137
MCXE317 - 0x138
MCXE31B - 0x13A
Reserved
15-8
—
Major Mask Revision
7-4
MAJOR_MASK
Minor Mask Revision
3-0
MINOR_MASK

---

*Page 136*

System Integration Unit Lite2 (SIUL2)

#### 9.6.3 SIUL2 MCU ID Register #2 (MIDR2)

Offset
Register Offset
MIDR2 8h
Function
NOTE
This register supports only 32-bit accesses. Byte and half-word accesses are not supported.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TECHNOLOGY TEMPERATURE PACKAGE FREQUENCY
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R FLASH_CODE FLASH_DATA FLASH_SIZE_DATA FLASH_SIZE_CODE
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
31-29 Technology
TECHNOLOGY Identifies the silicon technology.
001b - C40EFS3
28-26 Temperature
TEMPERATUR Identifies the ambient temperature range.
E
100b - M = 125C
25-20 Package
PACKAGE This field can by read by software to determine the package type that is used for the particular device.
00_0011b - 257-MAPBGA
10_0011b - 100-MAXQFP
10_0110b - 172-MAXQFP
19-16 Frequency
Table continues on the next page...

---

*Page 137*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
Identifies maximum core frequency. Qualified by Product Line Letter to provide wider range of
FREQUENCY
frequencies.
0011b - 120 MHz
0100b - 160 MHz
0101b - 240 MHz
15-14 Flash Code
FLASH_CODE Identifies the location of Code Flash, if any, within the package.
10b - Monolithic
13-12 Flash Data
FLASH_DATA Identifies the location of Data Flash, if any, within the package.
10b - Monolithic
11-8 Flash Size Data
FLASH_SIZE_D Identifies the Flash (EE) memory size.
ATA
0000b - 64KB
0001b - 128KB
0010b - 256KB
7-0 Flash Size Code
FLASH_SIZE_C Identifies the Flash (code) memory size.
ODE
0000_0010b - 512kB
0000_0100b - 1MB
0000_1000b - 2.00MB
0000_1100b - 3.00MB
0001_0000b - 4.00MB
0001_1000b - 6.00MB
0010_0000b - 8.00MB

#### 9.6.4 DMA or Interrupt Status Flag 0 (DISR0)

Offset
Register Offset
DISR0 10h
Function
Contains flags that record an event on the external IRQ pins. This register supports 8-, 16-, and 32-bit accesses.

---

*Page 138*

System Integration Unit Lite2 (SIUL2)
When an event (as defined in and IFEER0) occurs, the corresponding flag is set. The IRQ flag is set regardless of the state of the
corresponding DIRER0[EIRE n ]. The IRQ flag remains set until you clear it or is cleared by servicing of a DMA request. The IRQ
flags are cleared when you write 1 to them. A write of 0 has no effect.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R EIF31 EIF30 EIF29 EIF28 EIF27 EIF26 EIF25 EIF24 EIF23 EIF22 EIF21 EIF20 EIF19 EIF18 EIF17 EIF16
W W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R EIF15 EIF14 EIF13 EIF12 EIF11 EIF10 EIF9 EIF8 EIF7 EIF6 EIF5 EIF4 EIF3 EIF2 EIF1 EIF0
W W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 External Interrupt Status Flag 31
EIF31 Indicates whether an interrupt event (as defined by IREER31 and IFEER31) has occurred.
If this flag is set (DIRERR31 = 1), it causes an interrupt.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
30 External Interrupt Status Flag 30
EIF30 Indicates whether an interrupt event (as defined by IREER30 and IFEER30) has occurred.
If this flag is set (DIRERR30 = 1), it causes an interrupt.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
Table continues on the next page...

---

*Page 139*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
0b - No effect
1b - Clear the flag
29 External Interrupt Status Flag 29
EIF29 Indicates whether an interrupt event (as defined by IREER29 and IFEER29) has occurred.
If this flag is set (DIRERR29 = 1), it causes an interrupt.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
28 External Interrupt Status Flag 28
EIF28 Indicates whether an interrupt event (as defined by IREER28 and IFEER28) has occurred.
If this flag is set (DIRERR28 = 1), it causes an interrupt.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
27 External Interrupt Status Flag 27
EIF27 Indicates whether an interrupt event (as defined by IREER27 and IFEER27) has occurred.
If this flag is set (DIRERR27 = 1), it causes an interrupt.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
Table continues on the next page...

---

*Page 140*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
26 External Interrupt Status Flag 26
EIF26 Indicates whether an interrupt event (as defined by IREER26 and IFEER26) has occurred.
If this flag is set (DIRERR26 = 1), it causes an interrupt.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
25 External Interrupt Status Flag 25
EIF25 Indicates whether an interrupt event (as defined by IREER25 and IFEER25) has occurred.
If this flag is set (DIRERR25 = 1), it causes an interrupt.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
24 External Interrupt Status Flag 24
EIF24 Indicates whether an interrupt event (as defined by IREER24 and IFEER24) has occurred.
If this flag is set (DIRERR24 = 1), it causes an interrupt.
NOTE
This field behaves differently for register reads and writes.
When reading
Table continues on the next page...

---

*Page 141*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
23 External Interrupt Status Flag 23
EIF23 Indicates whether an interrupt event (as defined by IREER23 and IFEER23) has occurred.
If this flag is set (DIRERR23 = 1), it causes an interrupt.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
22 External Interrupt Status Flag 22
EIF22 Indicates whether an interrupt event (as defined by IREER22 and IFEER22) has occurred.
If this flag is set (DIRERR22 = 1), it causes an interrupt.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
21 External Interrupt Status Flag 21
EIF21 Indicates whether an interrupt event (as defined by IREER21 and IFEER21) has occurred.
If this flag is set (DIRERR21 = 1), it causes an interrupt.
NOTE
This field behaves differently for register reads and writes.
Table continues on the next page...

---

*Page 142*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
20 External Interrupt Status Flag 20
EIF20 Indicates whether an interrupt event (as defined by IREER20 and IFEER20) has occurred.
If this flag is set (DIRERR20 = 1), it causes an interrupt.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
19 External Interrupt Status Flag 19
EIF19 Indicates whether an interrupt event (as defined by IREER19 and IFEER19) has occurred.
If this flag is set (DIRERR19 = 1), it causes an interrupt.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
18 External Interrupt Status Flag 18
EIF18 Indicates whether an interrupt event (as defined by IREER18 and IFEER18) has occurred.
If this flag is set (DIRERR18 = 1), it causes an interrupt.
Table continues on the next page...

---

*Page 143*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
17 External Interrupt Status Flag 17
EIF17 Indicates whether an interrupt event (as defined by IREER17 and IFEER17) has occurred.
If this flag is set (DIRERR17 = 1), it causes an interrupt.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
16 External Interrupt Status Flag 16
EIF16 Indicates whether an interrupt event (as defined by IREER16 and IFEER16) has occurred.
If this flag is set (DIRERR16 = 1), it causes an interrupt.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
15 External Interrupt Status Flag 15
EIF15 Indicates whether an interrupt event (as defined by IREER15 and IFEER15) has occurred.
Table continues on the next page...

---

*Page 144*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
If this flag is set (DIRERR15 = 1), it causes an interrupt or DMA request .
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
14 External Interrupt Status Flag 14
EIF14 Indicates whether an interrupt event (as defined by IREER14 and IFEER14) has occurred.
If this flag is set (DIRERR14 = 1), it causes an interrupt or DMA request .
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
13 External Interrupt Status Flag 13
EIF13 Indicates whether an interrupt event (as defined by IREER13 and IFEER13) has occurred.
If this flag is set (DIRERR13 = 1), it causes an interrupt or DMA request .
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
12 External Interrupt Status Flag 12
Table continues on the next page...

---

*Page 145*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
EIF12 Indicates whether an interrupt event (as defined by IREER12 and IFEER12) has occurred.
If this flag is set (DIRERR12 = 1), it causes an interrupt or DMA request .
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
11 External Interrupt Status Flag 11
EIF11 Indicates whether an interrupt event (as defined by IREER11 and IFEER11) has occurred.
If this flag is set (DIRERR11 = 1), it causes an interrupt or DMA request .
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
10 External Interrupt Status Flag 10
EIF10 Indicates whether an interrupt event (as defined by IREER10 and IFEER10) has occurred.
If this flag is set (DIRERR10 = 1), it causes an interrupt or DMA request .
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
Table continues on the next page...

---

*Page 146*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
9 External Interrupt Status Flag 9
EIF9 Indicates whether an interrupt event (as defined by IREER9 and IFEER9) has occurred.
If this flag is set (DIRERR9 = 1), it causes an interrupt or DMA request .
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
8 External Interrupt Status Flag 8
EIF8 Indicates whether an interrupt event (as defined by IREER8 and IFEER8) has occurred.
If this flag is set (DIRERR8 = 1), it causes an interrupt or DMA request .
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
7 External Interrupt Status Flag 7
EIF7 Indicates whether an interrupt event (as defined by IREER7 and IFEER7) has occurred.
If this flag is set (DIRERR7 = 1), it causes an interrupt or DMA request .
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
Table continues on the next page...

---

*Page 147*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
1b - Clear the flag
6 External Interrupt Status Flag 6
EIF6 Indicates whether an interrupt event (as defined by IREER6 and IFEER6) has occurred.
If this flag is set (DIRERR6 = 1), it causes an interrupt or DMA request .
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
5 External Interrupt Status Flag 5
EIF5 Indicates whether an interrupt event (as defined by IREER5 and IFEER5) has occurred.
If this flag is set (DIRERR5 = 1), it causes an interrupt or DMA request .
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
4 External Interrupt Status Flag 4
EIF4 Indicates whether an interrupt event (as defined by IREER4 and IFEER4) has occurred.
If this flag is set (DIRERR4 = 1), it causes an interrupt or DMA request .
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
Table continues on the next page...

---

*Page 148*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
0b - No effect
1b - Clear the flag
3 External Interrupt Status Flag 3
EIF3 Indicates whether an interrupt event (as defined by IREER3 and IFEER3) has occurred.
If this flag is set (DIRERR3 = 1), it causes an interrupt or DMA request .
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
2 External Interrupt Status Flag 2
EIF2 Indicates whether an interrupt event (as defined by IREER2 and IFEER2) has occurred.
If this flag is set (DIRERR2 = 1), it causes an interrupt or DMA request .
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
1 External Interrupt Status Flag 1
EIF1 Indicates whether an interrupt event (as defined by IREER1 and IFEER1) has occurred.
If this flag is set (DIRERR1 = 1), it causes an interrupt or DMA request .
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
Table continues on the next page...

---

*Page 149*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag
0 External Interrupt Status Flag 0
EIF0 Indicates whether an interrupt event (as defined by IREER0 and IFEER0) has occurred.
If this flag is set (DIRERR0 = 1), it causes an interrupt or DMA request .
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Interrupt event did not occur on the pad
1b - Interrupt event occurred
When writing
0b - No effect
1b - Clear the flag

#### 9.6.5 DMA or Interrupt Request Enable 0 (DIRER0)

Offset
Register Offset
DIRER0 18h
Function
Enables the assertion of a DMA or interrupt request to the interrupt controller if the corresponding DISR0[EIF n ] flag is 1. The type
of request is determined by the corresponding DIRSR0[DIRSR n ] field.
This register supports 8-, 16-, and 32-bit accesses.
NOTE
You cannot enable or disable DIRSR0 after it selects a DMA request.

---

*Page 150*

System Integration Unit Lite2 (SIUL2)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
EIRE3 EIRE3 EIRE2 EIRE2 EIRE2 EIRE2 EIRE2 EIRE2 EIRE2 EIRE2 EIRE2 EIRE2 EIRE1 EIRE1 EIRE1 EIRE1
1 0 9 8 7 6 5 4 3 2 1 0 9 8 7 6
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
EIRE1 EIRE1 EIRE1 EIRE1 EIRE1 EIRE1
EIRE9 EIRE8 EIRE7 EIRE6 EIRE5 EIRE4 EIRE3 EIRE2 EIRE1 EIRE0
5 4 3 2 1 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 External Interrupt Request Enable
EIRE31 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
30 External Interrupt Request Enable
EIRE30 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
29 External Interrupt Request Enable
EIRE29 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
28 External Interrupt Request Enable
EIRE28 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
27 External Interrupt Request Enable
EIRE27 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
26 External Interrupt Request Enable
EIRE26 Enables the interrupt requests from the corresponding pin.
Table continues on the next page...

---

*Page 151*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
0b - Disable
1b - Enable
25 External Interrupt Request Enable
EIRE25 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
24 External Interrupt Request Enable
EIRE24 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
23 External Interrupt Request Enable
EIRE23 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
22 External Interrupt Request Enable
EIRE22 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
21 External Interrupt Request Enable
EIRE21 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
20 External Interrupt Request Enable
EIRE20 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
19 External Interrupt Request Enable
EIRE19 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
18 External Interrupt Request Enable
Table continues on the next page...

---

*Page 152*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
Enables the interrupt requests from the corresponding pin.
EIRE18
0b - Disable
1b - Enable
17 External Interrupt Request Enable
EIRE17 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
16 External Interrupt Request Enable
EIRE16 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
15 External Interrupt Request Enable
EIRE15 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
14 External Interrupt Request Enable
EIRE14 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
13 External Interrupt Request Enable
EIRE13 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
12 External Interrupt Request Enable
EIRE12 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
11 External Interrupt Request Enable
EIRE11 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
Table continues on the next page...

---

*Page 153*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
10 External Interrupt Request Enable
EIRE10 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
9 External Interrupt Request Enable
EIRE9 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
8 External Interrupt Request Enable
EIRE8 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
7 External Interrupt Request Enable
EIRE7 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
6 External Interrupt Request Enable
EIRE6 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
5 External Interrupt Request Enable
EIRE5 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
4 External Interrupt Request Enable
EIRE4 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
3 External Interrupt Request Enable
EIRE3 Enables the interrupt requests from the corresponding pin.
0b - Disable
Table continues on the next page...

---

*Page 154*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
1b - Enable
2 External Interrupt Request Enable
EIRE2 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
1 External Interrupt Request Enable
EIRE1 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable
0 External Interrupt Request Enable
EIRE0 Enables the interrupt requests from the corresponding pin.
0b - Disable
1b - Enable

#### 9.6.6 DMA or Interrupt Request Select 0 (DIRSR0)

Offset
Register Offset
DIRSR0 20h
Function
Selects the type of request (DMA or interrupt request). This register supports 8-, 16-, and 32-bit accesses.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR
31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR DIRSR
15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 155*

System Integration Unit Lite2 (SIUL2)
Fields
Field Function
31 DMA or Interrupt Request Select
DIRSR31 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - Reserved
30 DMA or Interrupt Request Select
DIRSR30 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - Reserved
29 DMA or Interrupt Request Select
DIRSR29 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - Reserved
28 DMA or Interrupt Request Select
DIRSR28 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - Reserved
27 DMA or Interrupt Request Select
DIRSR27 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - Reserved
Table continues on the next page...

---

*Page 156*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
26 DMA or Interrupt Request Select
DIRSR26 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - Reserved
25 DMA or Interrupt Request Select
DIRSR25 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - Reserved
24 DMA or Interrupt Request Select
DIRSR24 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - Reserved
23 DMA or Interrupt Request Select
DIRSR23 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - Reserved
22 DMA or Interrupt Request Select
DIRSR22 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - Reserved
Table continues on the next page...

---

*Page 157*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
21 DMA or Interrupt Request Select
DIRSR21 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - Reserved
20 DMA or Interrupt Request Select
DIRSR20 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - Reserved
19 DMA or Interrupt Request Select
DIRSR19 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - Reserved
18 DMA or Interrupt Request Select
DIRSR18 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - Reserved
17 DMA or Interrupt Request Select
DIRSR17 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - Reserved
Table continues on the next page...

---

*Page 158*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
16 DMA or Interrupt Request Select
DIRSR16 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - Reserved
15 DMA or Interrupt Request Select
DIRSR15 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - DMA request
14 DMA or Interrupt Request Select
DIRSR14 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - DMA request
13 DMA or Interrupt Request Select
DIRSR13 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - DMA request
12 DMA or Interrupt Request Select
DIRSR12 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - DMA request
Table continues on the next page...

---

*Page 159*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
11 DMA or Interrupt Request Select
DIRSR11 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - DMA request
10 DMA or Interrupt Request Select
DIRSR10 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - DMA request
9 DMA or Interrupt Request Select
DIRSR9 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - DMA request
8 DMA or Interrupt Request Select
DIRSR8 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - DMA request
7 DMA or Interrupt Request Select
DIRSR7 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - DMA request
Table continues on the next page...

---

*Page 160*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
6 DMA or Interrupt Request Select
DIRSR6 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - DMA request
5 DMA or Interrupt Request Select
DIRSR5 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - DMA request
4 DMA or Interrupt Request Select
DIRSR4 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - DMA request
3 DMA or Interrupt Request Select
DIRSR3 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - DMA request
2 DMA or Interrupt Request Select
DIRSR2 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - DMA request
Table continues on the next page...

---

*Page 161*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
1 DMA or Interrupt Request Select
DIRSR1 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - DMA request
0 DMA or Interrupt Request Select
DIRSR0 Selects either a DMA request or an external interrupt request when an edge-triggered event occurs on the
corresponding pin.
This field determines whether a DMA request or an interrupt request asserts the
corresponding DISR0[EIF n ].
0b - Interrupt request
1b - DMA request

#### 9.6.7 Interrupt Rising-Edge Event Enable 0 (IREER0)

Offset
Register Offset
IREER0 28h
Function
Enables the rising-edge triggered events on the corresponding interrupt pads. This register supports 8-, 16-, and 32-bit accesses.
NOTE
If you write 0 to both the IREE and IFEE fields for the same interrupt source, the interrupt status flag for the
corresponding external interrupt never sets.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
IREE3 IREE3 IREE2 IREE2 IREE2 IREE2 IREE2 IREE2 IREE2 IREE2 IREE2 IREE2 IREE1 IREE1 IREE1 IREE1
1 0 9 8 7 6 5 4 3 2 1 0 9 8 7 6
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
IREE1 IREE1 IREE1 IREE1 IREE1 IREE1
IREE9 IREE8 IREE7 IREE6 IREE5 IREE4 IREE3 IREE2 IREE1 IREE0
5 4 3 2 1 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 162*

System Integration Unit Lite2 (SIUL2)
Fields
Field Function
31 Interrupt Rising-Edge Event Enable 31
IREE31 Enables the rising-edge events to set DISR0[EIF31].
0b - Disable
1b - Enable
30 Interrupt Rising-Edge Event Enable 30
IREE30 Enables the rising-edge events to set DISR0[EIF30].
0b - Disable
1b - Enable
29 Interrupt Rising-Edge Event Enable 29
IREE29 Enables the rising-edge events to set DISR0[EIF29].
0b - Disable
1b - Enable
28 Interrupt Rising-Edge Event Enable 28
IREE28 Enables the rising-edge events to set DISR0[EIF28].
0b - Disable
1b - Enable
27 Interrupt Rising-Edge Event Enable 27
IREE27 Enables the rising-edge events to set DISR0[EIF27].
0b - Disable
1b - Enable
26 Interrupt Rising-Edge Event Enable 26
IREE26 Enables the rising-edge events to set DISR0[EIF26].
0b - Disable
1b - Enable
25 Interrupt Rising-Edge Event Enable 25
IREE25 Enables the rising-edge events to set DISR0[EIF25].
0b - Disable
1b - Enable
24 Interrupt Rising-Edge Event Enable 24
IREE24 Enables the rising-edge events to set DISR0[EIF24].
0b - Disable
Table continues on the next page...

---

*Page 163*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
1b - Enable
23 Interrupt Rising-Edge Event Enable 23
IREE23 Enables the rising-edge events to set DISR0[EIF23].
0b - Disable
1b - Enable
22 Interrupt Rising-Edge Event Enable 22
IREE22 Enables the rising-edge events to set DISR0[EIF22].
0b - Disable
1b - Enable
21 Interrupt Rising-Edge Event Enable 21
IREE21 Enables the rising-edge events to set DISR0[EIF21].
0b - Disable
1b - Enable
20 Interrupt Rising-Edge Event Enable 20
IREE20 Enables the rising-edge events to set DISR0[EIF20].
0b - Disable
1b - Enable
19 Interrupt Rising-Edge Event Enable 19
IREE19 Enables the rising-edge events to set DISR0[EIF19].
0b - Disable
1b - Enable
18 Interrupt Rising-Edge Event Enable 18
IREE18 Enables the rising-edge events to set DISR0[EIF18].
0b - Disable
1b - Enable
17 Interrupt Rising-Edge Event Enable 17
IREE17 Enables the rising-edge events to set DISR0[EIF17].
0b - Disable
1b - Enable
16 Interrupt Rising-Edge Event Enable 16
IREE16 Enables the rising-edge events to set DISR0[EIF16].
Table continues on the next page...

---

*Page 164*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
0b - Disable
1b - Enable
15 Interrupt Rising-Edge Event Enable 15
IREE15 Enables the rising-edge events to set DISR0[EIF15].
0b - Disable
1b - Enable
14 Interrupt Rising-Edge Event Enable 14
IREE14 Enables the rising-edge events to set DISR0[EIF14].
0b - Disable
1b - Enable
13 Interrupt Rising-Edge Event Enable 13
IREE13 Enables the rising-edge events to set DISR0[EIF13].
0b - Disable
1b - Enable
12 Interrupt Rising-Edge Event Enable 12
IREE12 Enables the rising-edge events to set DISR0[EIF12].
0b - Disable
1b - Enable
11 Interrupt Rising-Edge Event Enable 11
IREE11 Enables the rising-edge events to set DISR0[EIF11].
0b - Disable
1b - Enable
10 Interrupt Rising-Edge Event Enable 10
IREE10 Enables the rising-edge events to set DISR0[EIF10].
0b - Disable
1b - Enable
9 Interrupt Rising-Edge Event Enable 9
IREE9 Enables the rising-edge events to set DISR0[EIF9].
0b - Disable
1b - Enable
8 Interrupt Rising-Edge Event Enable 8
Table continues on the next page...

---

*Page 165*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
Enables the rising-edge events to set DISR0[EIF8].
IREE8
0b - Disable
1b - Enable
7 Interrupt Rising-Edge Event Enable 7
IREE7 Enables the rising-edge events to set DISR0[EIF7].
0b - Disable
1b - Enable
6 Interrupt Rising-Edge Event Enable 6
IREE6 Enables the rising-edge events to set DISR0[EIF6].
0b - Disable
1b - Enable
5 Interrupt Rising-Edge Event Enable 5
IREE5 Enables the rising-edge events to set DISR0[EIF5].
0b - Disable
1b - Enable
4 Interrupt Rising-Edge Event Enable 4
IREE4 Enables the rising-edge events to set DISR0[EIF4].
0b - Disable
1b - Enable
3 Interrupt Rising-Edge Event Enable 3
IREE3 Enables the rising-edge events to set DISR0[EIF3].
0b - Disable
1b - Enable
2 Interrupt Rising-Edge Event Enable 2
IREE2 Enables the rising-edge events to set DISR0[EIF2].
0b - Disable
1b - Enable
1 Interrupt Rising-Edge Event Enable 1
IREE1 Enables the rising-edge events to set DISR0[EIF1].
0b - Disable
1b - Enable
Table continues on the next page...

---

*Page 166*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
0 Interrupt Rising-Edge Event Enable 0
IREE0 Enables the rising-edge events to set DISR0[EIF0].
0b - Disable
1b - Enable

#### 9.6.8 Interrupt Falling-Edge Event Enable 0 (IFEER0)

Offset
Register Offset
IFEER0 30h
Function
Enables the falling-edge triggered events on the corresponding interrupt pads. This register supports 8-, 16-, and 32-bit accesses.
NOTE
If you write 0 to both the IREE and IFEE fields for the same interrupt source, the interrupt status flag for the
corresponding external interrupt never sets.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
IFEE3 IFEE3 IFEE2 IFEE2 IFEE2 IFEE2 IFEE2 IFEE2 IFEE2 IFEE2 IFEE2 IFEE2 IFEE1 IFEE1 IFEE1 IFEE1
1 0 9 8 7 6 5 4 3 2 1 0 9 8 7 6
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
IFEE1 IFEE1 IFEE1 IFEE1 IFEE1 IFEE1
IFEE9 IFEE8 IFEE7 IFEE6 IFEE5 IFEE4 IFEE3 IFEE2 IFEE1 IFEE0
5 4 3 2 1 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Interrupt Falling-Edge Event Enable 31
IFEE31 Enables the falling-edge events to set DISR0[EIF31].
0b - Disable
1b - Enable
Table continues on the next page...

---

*Page 167*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
30 Interrupt Falling-Edge Event Enable 30
IFEE30 Enables the falling-edge events to set DISR0[EIF30].
0b - Disable
1b - Enable
29 Interrupt Falling-Edge Event Enable 29
IFEE29 Enables the falling-edge events to set DISR0[EIF29].
0b - Disable
1b - Enable
28 Interrupt Falling-Edge Event Enable 28
IFEE28 Enables the falling-edge events to set DISR0[EIF28].
0b - Disable
1b - Enable
27 Interrupt Falling-Edge Event Enable 27
IFEE27 Enables the falling-edge events to set DISR0[EIF27].
0b - Disable
1b - Enable
26 Interrupt Falling-Edge Event Enable 26
IFEE26 Enables the falling-edge events to set DISR0[EIF26].
0b - Disable
1b - Enable
25 Interrupt Falling-Edge Event Enable 25
IFEE25 Enables the falling-edge events to set DISR0[EIF25].
0b - Disable
1b - Enable
24 Interrupt Falling-Edge Event Enable 24
IFEE24 Enables the falling-edge events to set DISR0[EIF24].
0b - Disable
1b - Enable
23 Interrupt Falling-Edge Event Enable 23
IFEE23 Enables the falling-edge events to set DISR0[EIF23].
0b - Disable
Table continues on the next page...

---

*Page 168*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
1b - Enable
22 Interrupt Falling-Edge Event Enable 22
IFEE22 Enables the falling-edge events to set DISR0[EIF22].
0b - Disable
1b - Enable
21 Interrupt Falling-Edge Event Enable 21
IFEE21 Enables the falling-edge events to set DISR0[EIF21].
0b - Disable
1b - Enable
20 Interrupt Falling-Edge Event Enable 20
IFEE20 Enables the falling-edge events to set DISR0[EIF20].
0b - Disable
1b - Enable
19 Interrupt Falling-Edge Event Enable 19
IFEE19 Enables the falling-edge events to set DISR0[EIF19].
0b - Disable
1b - Enable
18 Interrupt Falling-Edge Event Enable 18
IFEE18 Enables the falling-edge events to set DISR0[EIF18].
0b - Disable
1b - Enable
17 Interrupt Falling-Edge Event Enable 17
IFEE17 Enables the falling-edge events to set DISR0[EIF17].
0b - Disable
1b - Enable
16 Interrupt Falling-Edge Event Enable 16
IFEE16 Enables the falling-edge events to set DISR0[EIF16].
0b - Disable
1b - Enable
15 Interrupt Falling-Edge Event Enable 15
IFEE15 Enables the falling-edge events to set DISR0[EIF15].
Table continues on the next page...

---

*Page 169*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
0b - Disable
1b - Enable
14 Interrupt Falling-Edge Event Enable 14
IFEE14 Enables the falling-edge events to set DISR0[EIF14].
0b - Disable
1b - Enable
13 Interrupt Falling-Edge Event Enable 13
IFEE13 Enables the falling-edge events to set DISR0[EIF13].
0b - Disable
1b - Enable
12 Interrupt Falling-Edge Event Enable 12
IFEE12 Enables the falling-edge events to set DISR0[EIF12].
0b - Disable
1b - Enable
11 Interrupt Falling-Edge Event Enable 11
IFEE11 Enables the falling-edge events to set DISR0[EIF11].
0b - Disable
1b - Enable
10 Interrupt Falling-Edge Event Enable 10
IFEE10 Enables the falling-edge events to set DISR0[EIF10].
0b - Disable
1b - Enable
9 Interrupt Falling-Edge Event Enable 9
IFEE9 Enables the falling-edge events to set DISR0[EIF9].
0b - Disable
1b - Enable
8 Interrupt Falling-Edge Event Enable 8
IFEE8 Enables the falling-edge events to set DISR0[EIF8].
0b - Disable
1b - Enable
7 Interrupt Falling-Edge Event Enable 7
Table continues on the next page...

---

*Page 170*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
Enables the falling-edge events to set DISR0[EIF7].
IFEE7
0b - Disable
1b - Enable
6 Interrupt Falling-Edge Event Enable 6
IFEE6 Enables the falling-edge events to set DISR0[EIF6].
0b - Disable
1b - Enable
5 Interrupt Falling-Edge Event Enable 5
IFEE5 Enables the falling-edge events to set DISR0[EIF5].
0b - Disable
1b - Enable
4 Interrupt Falling-Edge Event Enable 4
IFEE4 Enables the falling-edge events to set DISR0[EIF4].
0b - Disable
1b - Enable
3 Interrupt Falling-Edge Event Enable 3
IFEE3 Enables the falling-edge events to set DISR0[EIF3].
0b - Disable
1b - Enable
2 Interrupt Falling-Edge Event Enable 2
IFEE2 Enables the falling-edge events to set DISR0[EIF2].
0b - Disable
1b - Enable
1 Interrupt Falling-Edge Event Enable 1
IFEE1 Enables the falling-edge events to set DISR0[EIF1].
0b - Disable
1b - Enable
0 Interrupt Falling-Edge Event Enable 0
IFEE0 Enables the falling-edge events to set DISR0[EIF0].
0b - Disable
1b - Enable

---

*Page 171*

System Integration Unit Lite2 (SIUL2)

#### 9.6.9 Interrupt Filter Enable 0 (IFER0)

Offset
Register Offset
IFER0 38h
Function
Enables a digital filter counter on the corresponding interrupt pads to filter out glitches on the inputs. This register supports 8-, 16-,
and 32-bit accesses.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
IFE31 IFE30 IFE29 IFE28 IFE27 IFE26 IFE25 IFE24 IFE23 IFE22 IFE21 IFE20 IFE19 IFE18 IFE17 IFE16
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
IFE15 IFE14 IFE13 IFE12 IFE11 IFE10 IFE9 IFE8 IFE7 IFE6 IFE5 IFE4 IFE3 IFE2 IFE1 IFE0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Interrupt Filter Enable 31
IFE31 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
30 Interrupt Filter Enable 30
IFE30 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
29 Interrupt Filter Enable 29
IFE29 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
28 Interrupt Filter Enable 28
Table continues on the next page...

---

*Page 172*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
Enables the digital glitch filter on the interrupt pad input.
IFE28
0b - Disable
1b - Enable
27 Interrupt Filter Enable 27
IFE27 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
26 Interrupt Filter Enable 26
IFE26 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
25 Interrupt Filter Enable 25
IFE25 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
24 Interrupt Filter Enable 24
IFE24 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
23 Interrupt Filter Enable 23
IFE23 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
22 Interrupt Filter Enable 22
IFE22 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
21 Interrupt Filter Enable 21
IFE21 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
Table continues on the next page...

---

*Page 173*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
20 Interrupt Filter Enable 20
IFE20 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
19 Interrupt Filter Enable 19
IFE19 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
18 Interrupt Filter Enable 18
IFE18 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
17 Interrupt Filter Enable 17
IFE17 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
16 Interrupt Filter Enable 16
IFE16 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
15 Interrupt Filter Enable 15
IFE15 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
14 Interrupt Filter Enable 14
IFE14 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
13 Interrupt Filter Enable 13
IFE13 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
Table continues on the next page...

---

*Page 174*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
1b - Enable
12 Interrupt Filter Enable 12
IFE12 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
11 Interrupt Filter Enable 11
IFE11 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
10 Interrupt Filter Enable 10
IFE10 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
9 Interrupt Filter Enable 9
IFE9 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
8 Interrupt Filter Enable 8
IFE8 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
7 Interrupt Filter Enable 7
IFE7 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
6 Interrupt Filter Enable 6
IFE6 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
5 Interrupt Filter Enable 5
IFE5 Enables the digital glitch filter on the interrupt pad input.
Table continues on the next page...

---

*Page 175*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
0b - Disable
1b - Enable
4 Interrupt Filter Enable 4
IFE4 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
3 Interrupt Filter Enable 3
IFE3 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
2 Interrupt Filter Enable 2
IFE2 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
1 Interrupt Filter Enable 1
IFE1 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable
0 Interrupt Filter Enable 0
IFE0 Enables the digital glitch filter on the interrupt pad input.
0b - Disable
1b - Enable

#### 9.6.10 Interrupt Filter Maximum Counter (IFMCR0 - IFMCR31)

Offset
For a = 0 to 31:
Register Offset
IFMCRa 40h + (a × 4h)
Function
Configures the filter counter associated with each of the digital glitch filter and supports only 32-bit accesses. It does not support
byte and halfword accesses.

---

*Page 176*

System Integration Unit Lite2 (SIUL2)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
MAXCNT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-4
—
3-0 Maximum Interrupt Filter Counter Setting
MAXCNT Specifies the settings of the maximum interrupt filter counter.
The value of this field varies between 0d – 15d. Based on its value, it exhibits the following settings:
• A value of 0d, 1d, or 2d sets the filter as an all pass filter
• A value of 3d to 15d sets the filter period to TCK × MAXCNT + n × TCK, where:
— n is 1, 2, 3, or 4 ( n accounts for the uncertainty factor in filter period calculation)
— TCK is the prescaled filter clock period, which is the IRC clock prescaled to the IFCP value
specified in IFCPR

#### 9.6.11 SIUL2 MCU ID Register #3 (MIDR3)

Offset
Register Offset
MIDR3 200h
Function
NOTE
This register supports only 32-bit accesses. Byte and half-word accesses are not supported.

---

*Page 177*

System Integration Unit Lite2 (SIUL2)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R PROD_FAM_LET PROD_FAM_NO
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R PART_NO_SUF 0 SYS_RAM_SIZE
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
31-26 Product Family Letter
PROD_FAM_LE Identifies the product family letter.
T
01_0011b - S
25-16 Product Family Number
PROD_FAM_N Identifies the product family number.
O
00_0010_0000b - 32
15-10 Part Number Suffix
PART_NO_SUF Describes the part number suffix.
00_0000b - None
Reserved
9-6
—
5-0 System RAM Size
SYS_RAM_SIZ Total RAM size in SoC, including TCMs.
E
00_0010b - 128kB
00_0011b - 192kB
00_0100b - 256kB
00_0110b - 512kB

---

*Page 178*

System Integration Unit Lite2 (SIUL2)

#### 9.6.12 SIUL2 MCU ID Register #4 (MIDR4)

Offset
Register Offset
MIDR4 204h
Function
NOTE
This register supports only 32-bit accesses. Byte and half-word accesses are not supported.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 SEC_FET EMAC_FET CORE_PLAT_FET
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
Reserved
31-16
—
Reserved
15-14
—
Reserved
13-7
—
6-5 Security Feature
SEC_FET Security feature.
01b - ELE_HSEB
4-3 Ethernet Feature
EMAC_FET Ethernet feature.
Table continues on the next page...

---

*Page 179*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
00b - No Ethernet
01b - Ethernet present
2-0 Core Platform Options Feature
CORE_PLAT_F Core platform options feature.
ET
000b - Single core
001b - Dual core
010b - 1x lock-step core
011b - 1x lock-step core + 1 perf. core
100b - Triple core

#### 9.6.13 Multiplexed Signal Configuration (MSCR0 - MSCR323)

Offset
For a = 0 to 323:
Register Offset
MSCRa 240h + (a × 4h)
Function
Selects the source signal connected to the register's associated destination, which is a chip output pin or a chip pin that can be
configured as an output. It also specifies the electrical properties of the associated pin.
This register supports only 32-bit accesses. Byte and half-word write accesses are not supported.
For chip-pin MSCR assignments and pin types, see the IOMUX file attached to this document.
NOTE
• Configure these registers only during application initialization; you must not modify them during
application runtime.
• Accessing a reserved MSCR n register generates a transfer error.
• These registers are a part of the SIUL memory map but the physical implementation of these registers is a part
of the IOMUX RTL.
• SIUL2 interprets accesses to MSCR n at the module level.

---

*Page 180*

System Integration Unit Lite2 (SIUL2)
Internal connections
Destination (module port)
Input port
Input buffer
Pin a
Module 0
Input buffer
Output port Pin b
Module-port IMCR
Output
Chip-pin MSCR
buffer
Destination (chip pin)
Output port
Module n
Output port
Figure 19. MSCR and IMCR port and pin connection
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
OBE IBE INV PKE
W
Reset See Register reset values .
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0
SRC PUE PUS DSE IFE SMC SSS
W
Reset See Register reset values .
Register reset values
Register Reset value
MSCR0–MSCR3 0000_0000h
MSCR4 0008_2827h
MSCR5–MSCR9 0000_0000h
MSCR10 0000_0127h
MSCR11 0000_0000h
MSCR12 0000_0003h
MSCR13–MSCR37 0000_0000h
MSCR38–MSCR39 0001_0000h
MSCR40–MSCR65 0000_0000h
MSCR66–MSCR67 0000_4000h
Table continues on the next page...

---

*Page 181*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Register Reset value
MSCR68 0008_2000h
MSCR69 0008_2800h
MSCR70–MSCR75 0000_0000h
MSCR76 0000_4000h
MSCR77–MSCR79 0000_0000h
MSCR80 0000_4000h
MSCR81–MSCR100 0000_0000h
MSCR101–MSCR103 0000_4000h
MSCR104–MSCR105 0000_0000h
MSCR106–MSCR108 0000_4000h
MSCR109–MSCR135 0000_0000h
MSCR136 0000_4000h
MSCR137–MSCR219 0000_0000h
MSCR220–MSCR323 0001_0000h
Fields
Field Function
Reserved
31-22
—
21 GPIO Output Buffer Enable
OBE Applies only to digital pins. Otherwise this bit is reserved.
0b - Output driver disabled
1b - Output driver enabled
Reserved
20
—
19 Input Buffer Enable
IBE Used only when the associated destination is a chip pin. Enables the associated pin's input buffer.
0b - Disabled
1b - Enabled
Reserved
18
—
Table continues on the next page...

---

*Page 182*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
17 Invert
INV Inverts the signal selected by SSS before transmitting it to the associated destination (chip pin or module
port).
0b - Don't invert
1b - Invert
16 Pad keeping enable
PKE Pad keeping enable
0b - Disabled
1b - Enabled
Reserved
15
—
Slew Rate Control
14
0b - Fastest setting
SRC
1b - Slowest setting
13 Pull Enable
PUE Enables the pull function. Used only when the associated destination is a chip pin.
0b - Disabled
1b - Enabled
Reserved
12
—
11 Pull Select
PUS Determines whether the pull function is a pullup or pulldown when the pull function is enabled by the
PUE field. Used only when the associated destination is a chip pin.
0b - Pull down
1b - Pull up
Reserved
10-9
—
8 DSE
DSE Drive strength enable
0b - Disabled
1b - Enabled
Table continues on the next page...

---

*Page 183*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
Reserved
7
—
6 IFE
IFE Input filter enable
NOTE
This field is supported for RESET pad only (PTA5).
0b - Disabled
1b - Enabled
5 Safe Mode Control
SMC Used only when the associated destination is a chip pin. Specifies whether the chip disables the pin's
output buffer when the chip enters Safe mode.
0b - Disable (The output buffer returns to its previous state when the chip leaves Safe mode.)
1b - Don't disable
Reserved
4
—
3-0 Source Signal Select
SSS Selects a function for the pad. Refer to “SSS” column of the ‘IO Signal Table' tab of the IOMUX
spreadsheet attachment.

#### 9.6.14 Input Multiplexed Signal Configuration (IMCR0 - IMCR473)

Offset
For a = 0 to 473:
Register Offset
IMCRa A40h + (a × 4h)
Function
Selects the source signal connected to the register's associated destination, which is an internal module port that is an input port
or can be configured as an input.
This register supports only 32-bit accesses. Byte and half-word write accesses are not supported.
For IMCR assignments and field values, see the IOMUX file attached to this document.

---

*Page 184*

System Integration Unit Lite2 (SIUL2)
NOTE
• Configure these registers only during application initialization; you must not modify them during
application runtime.
• Accessing a reserved IMCR n register generates a transfer error.
• These registers are a part of the SIUL memory map but the physical implementation of these registers is a part
of the IOMUX RTL.
• SIUL2 interprets accesses to MSCR n at the module level.
Internal connections
Destination (module port)
Input port
Input buffer
Pin a
Module 0
Input buffer
Output port Pin b
Module-port IMCR
Output
Chip-pin MSCR
buffer
Destination (chip pin)
Output port
Module n
Output port
Figure 20. MSCR and IMCR port and pin connection
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
SSS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-4
—
3-0 Source Signal Select
SSS Selects which source signal is connected to the associated destination (chip pin).

---

*Page 185*

System Integration Unit Lite2 (SIUL2)

#### 9.6.15 GPIO Pad Data Output (GPDO0 - GPDO219)

Offset
For n = 0 to 37; n = 40 to 219:
Register Offset
GPDOn 1300h + (n + 3 - 2 × (n mod 4))
Function
Writes 0 or 1 to a single GPIO pad with a byte access and supports 8-, 16-, and 32-bit accesses.
Diagram
Bits 7 6 5 4 3 2 1 0
R 0
PDO_n
W
Reset 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
7-1
—
0 Pad Data Out
PDO_n Stores the data to be driven out on an external GPIO pad (controlled by this register) when you configure
the pad as an output.
It also represents PDO[ n ], where n is the instance of the register.
0b - Logic low value
1b - Logic high value

#### 9.6.16 GPIO Pad Data Input (GPDI0 - GPDI219)

Offset
For n = 0 to 37; n = 40 to 219:
Register Offset
GPDIn 1500h + (n + 3 - 2 × (n mod 4))
Function
Reads the GPIO pad data with a byte access and supports 8-, 16-, and 32-bit accesses.

---

*Page 186*

System Integration Unit Lite2 (SIUL2)
Diagram
Bits 7 6 5 4 3 2 1 0
R 0 PDI_n
W
Reset 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
7-1
—
0 Pad Data In
PDI_n Stores the value of the external GPIO pad associated with this register.
It represents PDI[ n ], where n is the instance of the register.
0b - Logic low
1b - Logic high

#### 9.6.17 Parallel GPIO Pad Data Output (PGPDO0 - PGPDO3)

Offset
Register Offset
PGPDO1 1700h
PGPDO0 1702h
PGPDO3 1704h
Function
Sets or clears the respective pads of the chip and supports 8-, 16-, and 32-bit accesses. This register also accesses the same
physical resource as the PDO and MPGPDO address locations.
This register sets the values of all the output pins assigned to a chip port with a single 16-bit register write, while the GPDO n
register sets the value on a specific pin with byte writes.
The access to this register's location is coherent with access to the bitwise GPDO n .
For a given PGPDO x [PPDO y ], where x is the register instance index and y is the field index, the following equation shows the
equivalent GPDO n [PDO_ n ] field:
PGPDO x [PPDO y ] = GPDO( x × 16) + (15 - y )[PDO_( x × 16) + (15 - y )]
Following are some of the examples of mapping:
• PGPDO0[PPDO15] = GPDO0[PDO_0]
• PGPDO2[PPDO15] = GPDO32[PDO_32]
• PGPDO31[PPDO0] = GPDO511[PDO_511]

---

*Page 187*

System Integration Unit Lite2 (SIUL2)
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO
15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
15 Parallel Pad Data Out 15
PPDO15 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
14 Parallel Pad Data Out 14
PPDO14 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
13 Parallel Pad Data Out 13
PPDO13 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
12 Parallel Pad Data Out 12
PPDO12 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
11 Parallel Pad Data Out 11
PPDO11 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
10 Parallel Pad Data Out 10
PPDO10 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
9 Parallel Pad Data Out 9
PPDO9 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
Table continues on the next page...

---

*Page 188*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
0b - Logic low
1b - Logic high
8 Parallel Pad Data Out 8
PPDO8 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
7 Parallel Pad Data Out 7
PPDO7 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
6 Parallel Pad Data Out 6
PPDO6 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
5 Parallel Pad Data Out 5
PPDO5 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
4 Parallel Pad Data Out 4
PPDO4 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
3 Parallel Pad Data Out 3
PPDO3 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
2 Parallel Pad Data Out 2
PPDO2 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
1 Parallel Pad Data Out 1
Table continues on the next page...

---

*Page 189*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
PPDO1
0b - Logic low
1b - Logic high
0 Parallel Pad Data Out 0
PPDO0 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high

#### 9.6.18 Parallel GPIO Pad Data Output (PGPDO2)

Offset
Register Offset
PGPDO2 1706h
Function
Sets or clears the respective pads of the chip and supports 8-, 16-, and 32-bit accesses. This register also accesses the same
physical resource as the PDO and MPGPDO address locations.
This register sets the values of all the output pins assigned to a chip port with a single 16-bit register write, while the GPDO n
register sets the value on a specific pin with byte writes.
The access to this register's location is coherent with access to the bitwise GPDO n .
For a given PGPDO x [PPDO y ], where x is the register instance index and y is the field index, the following equation shows the
equivalent GPDO n [PDO_ n ] field:
PGPDO x [PPDO y ] = GPDO( x × 16) + (15 - y )[PDO_( x × 16) + (15 - y )]
Following are some of the examples of mapping:
• PGPDO0[PPDO15] = GPDO0[PDO_0]
• PGPDO2[PPDO15] = GPDO32[PDO_32]
• PGPDO31[PPDO0] = GPDO511[PDO_511]

---

*Page 190*

System Integration Unit Lite2 (SIUL2)
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO
15 14 13 12 11 10 7 6 5 4 3 2 1 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
15 Parallel Pad Data Out 15
PPDO15 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
14 Parallel Pad Data Out 14
PPDO14 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
13 Parallel Pad Data Out 13
PPDO13 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
12 Parallel Pad Data Out 12
PPDO12 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
11 Parallel Pad Data Out 11
PPDO11 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
10 Parallel Pad Data Out 10
PPDO10 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
9 Reserved
— Always write zero to this field.
Table continues on the next page...

---

*Page 191*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
8 Reserved
— Always write zero to this field.
7 Parallel Pad Data Out 7
PPDO7 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
6 Parallel Pad Data Out 6
PPDO6 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
5 Parallel Pad Data Out 5
PPDO5 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
4 Parallel Pad Data Out 4
PPDO4 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
3 Parallel Pad Data Out 3
PPDO3 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
2 Parallel Pad Data Out 2
PPDO2 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
1 Parallel Pad Data Out 1
PPDO1 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
0 Parallel Pad Data Out 0
Table continues on the next page...

---

*Page 192*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
PPDO0
0b - Logic low
1b - Logic high

#### 9.6.19 Parallel GPIO Pad Data Output (PGPDO4 - PGPDO11)

Offset
For n = 4 to 11:
Register Offset
PGPDOn 1708h + 2 × (n + 1 – 2 × (n mod 2))
Function
Sets or clears the respective pads of the chip and supports 8-, 16-, and 32-bit accesses. This register also accesses the same
physical resource as the PDO and MPGPDO address locations.
This register sets the values of all the output pins assigned to a chip port with a single 16-bit register write, while the GPDO n
register sets the value on a specific pin with byte writes.
The access to this register's location is coherent with access to the bitwise GPDO n .
For a given PGPDO x [PPDO y ], where x is the register instance index and y is the field index, the following equation shows the
equivalent GPDO n [PDO_ n ] field:
PGPDO x [PPDO y ] = GPDO( x × 16) + (15 - y )[PDO_( x × 16) + (15 - y )]
Following are some of the examples of mapping:
• PGPDO0[PPDO15] = GPDO0[PDO_0]
• PGPDO2[PPDO15] = GPDO32[PDO_32]
• PGPDO31[PPDO0] = GPDO511[PDO_511]
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO
15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
15 Parallel Pad Data Out 15
Table continues on the next page...

---

*Page 193*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
PPDO15
0b - Logic low
1b - Logic high
14 Parallel Pad Data Out 14
PPDO14 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
13 Parallel Pad Data Out 13
PPDO13 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
12 Parallel Pad Data Out 12
PPDO12 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
11 Parallel Pad Data Out 11
PPDO11 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
10 Parallel Pad Data Out 10
PPDO10 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
9 Parallel Pad Data Out 9
PPDO9 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
8 Parallel Pad Data Out 8
PPDO8 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
Table continues on the next page...

---

*Page 194*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
7 Parallel Pad Data Out 7
PPDO7 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
6 Parallel Pad Data Out 6
PPDO6 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
5 Parallel Pad Data Out 5
PPDO5 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
4 Parallel Pad Data Out 4
PPDO4 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
3 Parallel Pad Data Out 3
PPDO3 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
2 Parallel Pad Data Out 2
PPDO2 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
1 Parallel Pad Data Out 1
PPDO1 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
0 Parallel Pad Data Out 0
PPDO0 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
Table continues on the next page...

---

*Page 195*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
1b - Logic high

#### 9.6.20 Parallel GPIO Pad Data Output (PGPDO13)

Offset
Register Offset
PGPDO13 1718h
Function
Sets or clears the respective pads of the chip and supports 8-, 16-, and 32-bit accesses. This register also accesses the same
physical resource as the PDO and MPGPDO address locations.
This register sets the values of all the output pins assigned to a chip port with a single 16-bit register write, while the GPDO n
register sets the value on a specific pin with byte writes.
The access to this register's location is coherent with access to the bitwise GPDO n .
For a given PGPDO x [PPDO y ], where x is the register instance index and y is the field index, the following equation shows the
equivalent GPDO n [PDO_ n ] field:
PGPDO x [PPDO y ] = GPDO( x × 16) + (15 - y )[PDO_( x × 16) + (15 - y )]
Following are some of the examples of mapping:
• PGPDO0[PPDO15] = GPDO0[PDO_0]
• PGPDO2[PPDO15] = GPDO32[PDO_32]
• PGPDO31[PPDO0] = GPDO511[PDO_511]
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0
PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO
15 14 13 12 11 10 9 8 7 6 5 4
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
15 Parallel Pad Data Out 15
PPDO15 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
Table continues on the next page...

---

*Page 196*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
1b - Logic high
14 Parallel Pad Data Out 14
PPDO14 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
13 Parallel Pad Data Out 13
PPDO13 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
12 Parallel Pad Data Out 12
PPDO12 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
11 Parallel Pad Data Out 11
PPDO11 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
10 Parallel Pad Data Out 10
PPDO10 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
9 Parallel Pad Data Out 9
PPDO9 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
8 Parallel Pad Data Out 8
PPDO8 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
7 Parallel Pad Data Out 7
PPDO7 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
Table continues on the next page...

---

*Page 197*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
0b - Logic low
1b - Logic high
6 Parallel Pad Data Out 6
PPDO6 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
5 Parallel Pad Data Out 5
PPDO5 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
4 Parallel Pad Data Out 4
PPDO4 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
3 Reserved
— Always write zero to this field.
2 Reserved
— Always write zero to this field.
1 Reserved
— Always write zero to this field.
0 Reserved
— Always write zero to this field.

#### 9.6.21 Parallel GPIO Pad Data Output (PGPDO12)

Offset
Register Offset
PGPDO12 171Ah
Function
Sets or clears the respective pads of the chip and supports 8-, 16-, and 32-bit accesses. This register also accesses the same
physical resource as the PDO and MPGPDO address locations.

---

*Page 198*

System Integration Unit Lite2 (SIUL2)
This register sets the values of all the output pins assigned to a chip port with a single 16-bit register write, while the GPDO n
register sets the value on a specific pin with byte writes.
The access to this register's location is coherent with access to the bitwise GPDO n .
For a given PGPDO x [PPDO y ], where x is the register instance index and y is the field index, the following equation shows the
equivalent GPDO n [PDO_ n ] field:
PGPDO x [PPDO y ] = GPDO( x × 16) + (15 - y )[PDO_( x × 16) + (15 - y )]
Following are some of the examples of mapping:
• PGPDO0[PPDO15] = GPDO0[PDO_0]
• PGPDO2[PPDO15] = GPDO32[PDO_32]
• PGPDO31[PPDO0] = GPDO511[PDO_511]
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO PPDO
15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
15 Parallel Pad Data Out 15
PPDO15 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
14 Parallel Pad Data Out 14
PPDO14 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
13 Parallel Pad Data Out 13
PPDO13 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
12 Parallel Pad Data Out 12
PPDO12 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
11 Parallel Pad Data Out 11
Table continues on the next page...

---

*Page 199*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
PPDO11
0b - Logic low
1b - Logic high
10 Parallel Pad Data Out 10
PPDO10 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
9 Parallel Pad Data Out 9
PPDO9 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
8 Parallel Pad Data Out 8
PPDO8 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
7 Parallel Pad Data Out 7
PPDO7 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
6 Parallel Pad Data Out 6
PPDO6 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
5 Parallel Pad Data Out 5
PPDO5 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
4 Parallel Pad Data Out 4
PPDO4 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
Table continues on the next page...

---

*Page 200*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
3 Parallel Pad Data Out 3
PPDO3 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
2 Parallel Pad Data Out 2
PPDO2 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
1 Parallel Pad Data Out 1
PPDO1 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high
0 Parallel Pad Data Out 0
PPDO0 Writes to or reads the data register that stores the value to be driven on the pad in Output mode.
0b - Logic low
1b - Logic high

#### 9.6.22 Parallel GPIO Pad Data Input (PGPDI0 - PGPDI3)

Offset
Register Offset
PGPDI1 1740h
PGPDI0 1742h
PGPDI3 1744h
Function
Holds the synchronized input value from the pads and supports 8-, 16-, and 32-bit accesses.
This register reads the values of all input pins assigned to a chip port with a single 16-bit register read, while GPDI n registers read
the value on a specific pin with a byte read.
The access to this register's location is coherent with the access to the bitwise GPDI n .
For a given PGPDI x [PPDI y ], where x is the register instance index and y is the field index, the following equation shows the
equivalent GPDI n [PDI_ n ] field:
PGPDI x [PPDI y ] = GPDI( x × 16) + (15 - y )[PDI_( x × 16) + (15 - y )]
Following are some of the examples of mapping:

---

*Page 201*

System Integration Unit Lite2 (SIUL2)
• PGPDI0[PPDI15] = GPDI0[PDI_0]
• PGPDI2[PPDI15] = GPDI32[PDI_32]
• PGPDI31[PPDI0] = GPDI511[PDI_511]
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
PPDI1 PPDI1 PPDI1 PPDI1 PPDI1 PPDI1
R PPDI9 PPDI8 PPDI7 PPDI6 PPDI5 PPDI4 PPDI3 PPDI2 PPDI1 PPDI0
5 4 3 2 1 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
15 Parallel Pad Data Input 15
PPDI15 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
14 Parallel Pad Data Input 14
PPDI14 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
13 Parallel Pad Data Input 13
PPDI13 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
12 Parallel Pad Data Input 12
PPDI12 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
11 Parallel Pad Data Input 11
PPDI11 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
10 Parallel Pad Data Input 10
PPDI10 Reads the current pad value of the corresponding pad.
Table continues on the next page...

---

*Page 202*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
0b - Logic low
1b - Logic high
9 Parallel Pad Data Input 9
PPDI9 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
8 Parallel Pad Data Input 8
PPDI8 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
7 Parallel Pad Data Input 7
PPDI7 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
6 Parallel Pad Data Input 6
PPDI6 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
5 Parallel Pad Data Input 5
PPDI5 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
4 Parallel Pad Data Input 4
PPDI4 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
3 Parallel Pad Data Input 3
PPDI3 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
2 Parallel Pad Data Input 2
Table continues on the next page...

---

*Page 203*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
Reads the current pad value of the corresponding pad.
PPDI2
0b - Logic low
1b - Logic high
1 Parallel Pad Data Input 1
PPDI1 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
0 Parallel Pad Data Input 0
PPDI0 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high

#### 9.6.23 Parallel GPIO Pad Data Input (PGPDI2)

Offset
Register Offset
PGPDI2 1746h
Function
Holds the synchronized input value from the pads and supports 8-, 16-, and 32-bit accesses.
This register reads the values of all input pins assigned to a chip port with a single 16-bit register read, while GPDI n registers read
the value on a specific pin with a byte read.
The access to this register's location is coherent with the access to the bitwise GPDI n .
For a given PGPDI x [PPDI y ], where x is the register instance index and y is the field index, the following equation shows the
equivalent GPDI n [PDI_ n ] field:
PGPDI x [PPDI y ] = GPDI( x × 16) + (15 - y )[PDI_( x × 16) + (15 - y )]
Following are some of the examples of mapping:
• PGPDI0[PPDI15] = GPDI0[PDI_0]
• PGPDI2[PPDI15] = GPDI32[PDI_32]
• PGPDI31[PPDI0] = GPDI511[PDI_511]

---

*Page 204*

System Integration Unit Lite2 (SIUL2)
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
PPDI1 PPDI1 PPDI1 PPDI1 PPDI1 PPDI1 Reserv Reserv
R PPDI7 PPDI6 PPDI5 PPDI4 PPDI3 PPDI2 PPDI1 PPDI0
5 4 3 2 1 0 ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
15 Parallel Pad Data Input 15
PPDI15 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
14 Parallel Pad Data Input 14
PPDI14 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
13 Parallel Pad Data Input 13
PPDI13 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
12 Parallel Pad Data Input 12
PPDI12 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
11 Parallel Pad Data Input 11
PPDI11 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
10 Parallel Pad Data Input 10
PPDI10 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
Reserved
9
Table continues on the next page...

---

*Page 205*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
—
Reserved
8
—
7 Parallel Pad Data Input 7
PPDI7 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
6 Parallel Pad Data Input 6
PPDI6 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
5 Parallel Pad Data Input 5
PPDI5 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
4 Parallel Pad Data Input 4
PPDI4 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
3 Parallel Pad Data Input 3
PPDI3 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
2 Parallel Pad Data Input 2
PPDI2 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
1 Parallel Pad Data Input 1
PPDI1 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
Table continues on the next page...

---

*Page 206*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
0 Parallel Pad Data Input 0
PPDI0 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high

#### 9.6.24 Parallel GPIO Pad Data Input (PGPDI4 - PGPDI11)

Offset
For n = 4 to 11:
Register Offset
PGPDIn 1748h + 2 × (n + 1 – 2 × (n mod 2))
Function
Holds the synchronized input value from the pads and supports 8-, 16-, and 32-bit accesses.
This register reads the values of all input pins assigned to a chip port with a single 16-bit register read, while GPDI n registers read
the value on a specific pin with a byte read.
The access to this register's location is coherent with the access to the bitwise GPDI n .
For a given PGPDI x [PPDI y ], where x is the register instance index and y is the field index, the following equation shows the
equivalent GPDI n [PDI_ n ] field:
PGPDI x [PPDI y ] = GPDI( x × 16) + (15 - y )[PDI_( x × 16) + (15 - y )]
Following are some of the examples of mapping:
• PGPDI0[PPDI15] = GPDI0[PDI_0]
• PGPDI2[PPDI15] = GPDI32[PDI_32]
• PGPDI31[PPDI0] = GPDI511[PDI_511]

---

*Page 207*

System Integration Unit Lite2 (SIUL2)
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
PPDI1 PPDI1 PPDI1 PPDI1 PPDI1 PPDI1
R PPDI9 PPDI8 PPDI7 PPDI6 PPDI5 PPDI4 PPDI3 PPDI2 PPDI1 PPDI0
5 4 3 2 1 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
15 Parallel Pad Data Input 15
PPDI15 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
14 Parallel Pad Data Input 14
PPDI14 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
13 Parallel Pad Data Input 13
PPDI13 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
12 Parallel Pad Data Input 12
PPDI12 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
11 Parallel Pad Data Input 11
PPDI11 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
10 Parallel Pad Data Input 10
PPDI10 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
9 Parallel Pad Data Input 9
Table continues on the next page...

---

*Page 208*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
Reads the current pad value of the corresponding pad.
PPDI9
0b - Logic low
1b - Logic high
8 Parallel Pad Data Input 8
PPDI8 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
7 Parallel Pad Data Input 7
PPDI7 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
6 Parallel Pad Data Input 6
PPDI6 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
5 Parallel Pad Data Input 5
PPDI5 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
4 Parallel Pad Data Input 4
PPDI4 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
3 Parallel Pad Data Input 3
PPDI3 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
2 Parallel Pad Data Input 2
PPDI2 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
Table continues on the next page...

---

*Page 209*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
1 Parallel Pad Data Input 1
PPDI1 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
0 Parallel Pad Data Input 0
PPDI0 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high

#### 9.6.25 Parallel GPIO Pad Data Input (PGPDI13)

Offset
Register Offset
PGPDI13 1758h
Function
Holds the synchronized input value from the pads and supports 8-, 16-, and 32-bit accesses.
This register reads the values of all input pins assigned to a chip port with a single 16-bit register read, while GPDI n registers read
the value on a specific pin with a byte read.
The access to this register's location is coherent with the access to the bitwise GPDI n .
For a given PGPDI x [PPDI y ], where x is the register instance index and y is the field index, the following equation shows the
equivalent GPDI n [PDI_ n ] field:
PGPDI x [PPDI y ] = GPDI( x × 16) + (15 - y )[PDI_( x × 16) + (15 - y )]
Following are some of the examples of mapping:
• PGPDI0[PPDI15] = GPDI0[PDI_0]
• PGPDI2[PPDI15] = GPDI32[PDI_32]
• PGPDI31[PPDI0] = GPDI511[PDI_511]

---

*Page 210*

System Integration Unit Lite2 (SIUL2)
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
PPDI1 PPDI1 PPDI1 PPDI1 PPDI1 PPDI1 Reserv Reserv Reserv Reserv
R PPDI9 PPDI8 PPDI7 PPDI6 PPDI5 PPDI4
5 4 3 2 1 0 ed ed ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
15 Parallel Pad Data Input 15
PPDI15 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
14 Parallel Pad Data Input 14
PPDI14 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
13 Parallel Pad Data Input 13
PPDI13 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
12 Parallel Pad Data Input 12
PPDI12 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
11 Parallel Pad Data Input 11
PPDI11 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
10 Parallel Pad Data Input 10
PPDI10 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
9 Parallel Pad Data Input 9
Table continues on the next page...

---

*Page 211*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
Reads the current pad value of the corresponding pad.
PPDI9
0b - Logic low
1b - Logic high
8 Parallel Pad Data Input 8
PPDI8 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
7 Parallel Pad Data Input 7
PPDI7 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
6 Parallel Pad Data Input 6
PPDI6 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
5 Parallel Pad Data Input 5
PPDI5 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
4 Parallel Pad Data Input 4
PPDI4 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
Reserved
3
—
Reserved
2
—
Reserved
1
—
Reserved
0
—

---

*Page 212*

System Integration Unit Lite2 (SIUL2)

#### 9.6.26 Parallel GPIO Pad Data Input (PGPDI12)

Offset
Register Offset
PGPDI12 175Ah
Function
Holds the synchronized input value from the pads and supports 8-, 16-, and 32-bit accesses.
This register reads the values of all input pins assigned to a chip port with a single 16-bit register read, while GPDI n registers read
the value on a specific pin with a byte read.
The access to this register's location is coherent with the access to the bitwise GPDI n .
For a given PGPDI x [PPDI y ], where x is the register instance index and y is the field index, the following equation shows the
equivalent GPDI n [PDI_ n ] field:
PGPDI x [PPDI y ] = GPDI( x × 16) + (15 - y )[PDI_( x × 16) + (15 - y )]
Following are some of the examples of mapping:
• PGPDI0[PPDI15] = GPDI0[PDI_0]
• PGPDI2[PPDI15] = GPDI32[PDI_32]
• PGPDI31[PPDI0] = GPDI511[PDI_511]
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
PPDI1 PPDI1 PPDI1 PPDI1 PPDI1 PPDI1
R PPDI9 PPDI8 PPDI7 PPDI6 PPDI5 PPDI4 PPDI3 PPDI2 PPDI1 PPDI0
5 4 3 2 1 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
15 Parallel Pad Data Input 15
PPDI15 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
14 Parallel Pad Data Input 14
PPDI14 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
Table continues on the next page...

---

*Page 213*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
13 Parallel Pad Data Input 13
PPDI13 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
12 Parallel Pad Data Input 12
PPDI12 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
11 Parallel Pad Data Input 11
PPDI11 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
10 Parallel Pad Data Input 10
PPDI10 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
9 Parallel Pad Data Input 9
PPDI9 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
8 Parallel Pad Data Input 8
PPDI8 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
7 Parallel Pad Data Input 7
PPDI7 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
6 Parallel Pad Data Input 6
PPDI6 Reads the current pad value of the corresponding pad.
0b - Logic low
Table continues on the next page...

---

*Page 214*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
1b - Logic high
5 Parallel Pad Data Input 5
PPDI5 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
4 Parallel Pad Data Input 4
PPDI4 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
3 Parallel Pad Data Input 3
PPDI3 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
2 Parallel Pad Data Input 2
PPDI2 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
1 Parallel Pad Data Input 1
PPDI1 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high
0 Parallel Pad Data Input 0
PPDI0 Reads the current pad value of the corresponding pad.
0b - Logic low
1b - Logic high

#### 9.6.27 Masked Parallel GPIO Pad Data Output (MPGPDO0 - MPGPDO1)

Offset
Register Offset
MPGPDO0 1780h
MPGPDO1 1784h

---

*Page 215*

System Integration Unit Lite2 (SIUL2)
Function
Modifies the pad values associated with PGPDO n selectively and supports only 32-bit accesses. It does not support byte and
half-word accesses.
NOTE
Access this register only with 32-bit writes. 8-bit or 16-bit writes do not modify any bits in the register resulting in a
transfer error. Read access returns 0.
The accesses to each of this register location is coherent with access to the bitwise GPDO n .
For a given MPGPDO x [MPPDO y ], where x is the register instance index and y is the field index, the following equation shows the
equivalent GPDO n [PDO_ n ]:
MPGPDO x [MPPDO y ] = GPDO( x × 16) + (15 - y )[PDO_( x × 16) + (15 - y )]
Following are some of the examples of mapping:
• MPGPDO0[MPPDO15] = GPDO0[PDO_0]
• MPGPDO2[MPPDO15] = GPDO32[PDO_32]
• MPGPDO31[MPPDO0] = GPDO511[PDO_511]
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
MASK MASK MASK MASK MASK MASK MASK MASK MASK MASK MASK MASK MASK MASK MASK MASK
W
15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD
W
O15 O14 O13 O12 O11 O10 O9 O8 O7 O6 O5 O4 O3 O2 O1 O0
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Mask Field 15
MASK15 Masks MPPDO15 in the corresponding MPGPDO n register instance.
0b - MPPDO15 is ignored
1b - MPPDO15 is written
30 Mask Field 14
MASK14 Masks MPPDO14 in the corresponding MPGPDO n register instance.
0b - MPPDO14 is ignored
1b - MPPDO14 is written
Table continues on the next page...

---

*Page 216*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
29 Mask Field 13
MASK13 Masks MPPDO13 in the corresponding MPGPDO n register instance.
0b - MPPDO13 is ignored
1b - MPPDO13 is written
28 Mask Field 12
MASK12 Masks MPPDO12 in the corresponding MPGPDO n register instance.
0b - MPPDO12 is ignored
1b - MPPDO12 is written
27 Mask Field 11
MASK11 Masks MPPDO11 in the corresponding MPGPDO n register instance.
0b - MPPDO11 is ignored
1b - MPPDO11 is written
26 Mask Field 10
MASK10 Masks MPPDO10 in the corresponding MPGPDO n register instance.
0b - MPPDO10 is ignored
1b - MPPDO10 is written
25 Mask Field 9
MASK9 Masks MPPDO9 in the corresponding MPGPDO n register instance.
0b - MPPDO9 is ignored
1b - MPPDO9 is written
24 Mask Field 8
MASK8 Masks MPPDO8 in the corresponding MPGPDO n register instance.
0b - MPPDO8 is ignored
1b - MPPDO8 is written
23 Mask Field 7
MASK7 Masks MPPDO7 in the corresponding MPGPDO n register instance.
0b - MPPDO7 is ignored
1b - MPPDO7 is written
22 Mask Field 6
MASK6 Masks MPPDO6 in the corresponding MPGPDO n register instance.
0b - MPPDO6 is ignored
Table continues on the next page...

---

*Page 217*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
1b - MPPDO6 is written
21 Mask Field 5
MASK5 Masks MPPDO5 in the corresponding MPGPDO n register instance.
0b - MPPDO5 is ignored
1b - MPPDO5 is written
20 Mask Field 4
MASK4 Masks MPPDO4 in the corresponding MPGPDO n register instance.
0b - MPPDO4 is ignored
1b - MPPDO4 is written
19 Mask Field 3
MASK3 Masks MPPDO3 in the corresponding MPGPDO n register instance.
0b - MPPDO3 is ignored
1b - MPPDO3 is written
18 Mask Field 2
MASK2 Masks MPPDO2 in the corresponding MPGPDO n register instance.
0b - MPPDO2 is ignored
1b - MPPDO2 is written
17 Mask Field 1
MASK1 Masks MPPDO1 in the corresponding MPGPDO n register instance.
0b - MPPDO1 is ignored
1b - MPPDO1 is written
16 Mask Field 0
MASK0 Masks MPPDO0 in the corresponding MPGPDO n register instance.
0b - MPPDO0 is ignored
1b - MPPDO0 is written
15 Masked Parallel Pad Data Out 15
MPPDO15 Writes to the data register that stores the value to be driven on the pad in Output mode.
14 Masked Parallel Pad Data Out 14
MPPDO14 Writes to the data register that stores the value to be driven on the pad in Output mode.
13 Masked Parallel Pad Data Out 13
Table continues on the next page...

---

*Page 218*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
MPPDO13 Writes to the data register that stores the value to be driven on the pad in Output mode.
12 Masked Parallel Pad Data Out 12
MPPDO12 Writes to the data register that stores the value to be driven on the pad in Output mode.
11 Masked Parallel Pad Data Out 11
MPPDO11 Writes to the data register that stores the value to be driven on the pad in Output mode.
10 Masked Parallel Pad Data Out 10
MPPDO10 Writes to the data register that stores the value to be driven on the pad in Output mode.
9 Masked Parallel Pad Data Out 9
MPPDO9 Writes to the data register that stores the value to be driven on the pad in Output mode.
8 Masked Parallel Pad Data Out 8
MPPDO8 Writes to the data register that stores the value to be driven on the pad in Output mode.
7 Masked Parallel Pad Data Out 7
MPPDO7 Writes to the data register that stores the value to be driven on the pad in Output mode.
6 Masked Parallel Pad Data Out 6
MPPDO6 Writes to the data register that stores the value to be driven on the pad in Output mode.
5 Masked Parallel Pad Data Out 5
MPPDO5 Writes to the data register that stores the value to be driven on the pad in Output mode.
4 Masked Parallel Pad Data Out 4
MPPDO4 Writes to the data register that stores the value to be driven on the pad in Output mode.
3 Masked Parallel Pad Data Out 3
MPPDO3 Writes to the data register that stores the value to be driven on the pad in Output mode.
2 Masked Parallel Pad Data Out 2
MPPDO2 Writes to the data register that stores the value to be driven on the pad in Output mode.
1 Masked Parallel Pad Data Out 1
MPPDO1 Writes to the data register that stores the value to be driven on the pad in Output mode.
0 Masked Parallel Pad Data Out 0
MPPDO0 Writes to the data register that stores the value to be driven on the pad in Output mode.

---

*Page 219*

System Integration Unit Lite2 (SIUL2)

#### 9.6.28 Masked Parallel GPIO Pad Data Output (MPGPDO2)

Offset
Register Offset
MPGPDO2 1788h
Function
Modifies the pad values associated with PGPDO n selectively and supports only 32-bit accesses. It does not support byte and
half-word accesses.
NOTE
Access this register only with 32-bit writes. 8-bit or 16-bit writes do not modify any bits in the register resulting in a
transfer error. Read access returns 0.
The accesses to each of this register location is coherent with access to the bitwise GPDO n .
For a given MPGPDO x [MPPDO y ], where x is the register instance index and y is the field index, the following equation shows the
equivalent GPDO n [PDO_ n ]:
MPGPDO x [MPPDO y ] = GPDO( x × 16) + (15 - y )[PDO_( x × 16) + (15 - y )]
Following are some of the examples of mapping:
• MPGPDO0[MPPDO15] = GPDO0[PDO_0]
• MPGPDO2[MPPDO15] = GPDO32[PDO_32]
• MPGPDO31[MPPDO0] = GPDO511[PDO_511]
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
MASK MASK MASK MASK MASK MASK Reserv Reserv MASK MASK MASK MASK MASK MASK MASK MASK
W
15 14 13 12 11 10 ed ed 7 6 5 4 3 2 1 0
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
MPPD MPPD MPPD MPPD MPPD MPPD Reserv Reserv MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD
W
O15 O14 O13 O12 O11 O10 ed ed O7 O6 O5 O4 O3 O2 O1 O0
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Mask Field 15
MASK15 Masks MPPDO15 in the corresponding MPGPDO n register instance.
Table continues on the next page...

---

*Page 220*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
0b - MPPDO15 is ignored
1b - MPPDO15 is written
30 Mask Field 14
MASK14 Masks MPPDO14 in the corresponding MPGPDO n register instance.
0b - MPPDO14 is ignored
1b - MPPDO14 is written
29 Mask Field 13
MASK13 Masks MPPDO13 in the corresponding MPGPDO n register instance.
0b - MPPDO13 is ignored
1b - MPPDO13 is written
28 Mask Field 12
MASK12 Masks MPPDO12 in the corresponding MPGPDO n register instance.
0b - MPPDO12 is ignored
1b - MPPDO12 is written
27 Mask Field 11
MASK11 Masks MPPDO11 in the corresponding MPGPDO n register instance.
0b - MPPDO11 is ignored
1b - MPPDO11 is written
26 Mask Field 10
MASK10 Masks MPPDO10 in the corresponding MPGPDO n register instance.
0b - MPPDO10 is ignored
1b - MPPDO10 is written
25 Reserved
— Always write zero to this field.
24 Reserved
— Always write zero to this field.
23 Mask Field 7
MASK7 Masks MPPDO7 in the corresponding MPGPDO n register instance.
0b - MPPDO7 is ignored
1b - MPPDO7 is written
22 Mask Field 6
Table continues on the next page...

---

*Page 221*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
MASK6 Masks MPPDO6 in the corresponding MPGPDO n register instance.
0b - MPPDO6 is ignored
1b - MPPDO6 is written
21 Mask Field 5
MASK5 Masks MPPDO5 in the corresponding MPGPDO n register instance.
0b - MPPDO5 is ignored
1b - MPPDO5 is written
20 Mask Field 4
MASK4 Masks MPPDO4 in the corresponding MPGPDO n register instance.
0b - MPPDO4 is ignored
1b - MPPDO4 is written
19 Mask Field 3
MASK3 Masks MPPDO3 in the corresponding MPGPDO n register instance.
0b - MPPDO3 is ignored
1b - MPPDO3 is written
18 Mask Field 2
MASK2 Masks MPPDO2 in the corresponding MPGPDO n register instance.
0b - MPPDO2 is ignored
1b - MPPDO2 is written
17 Mask Field 1
MASK1 Masks MPPDO1 in the corresponding MPGPDO n register instance.
0b - MPPDO1 is ignored
1b - MPPDO1 is written
16 Mask Field 0
MASK0 Masks MPPDO0 in the corresponding MPGPDO n register instance.
0b - MPPDO0 is ignored
1b - MPPDO0 is written
15 Masked Parallel Pad Data Out 15
MPPDO15 Writes to the data register that stores the value to be driven on the pad in Output mode.
14 Masked Parallel Pad Data Out 14
MPPDO14 Writes to the data register that stores the value to be driven on the pad in Output mode.
Table continues on the next page...

---

*Page 222*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
13 Masked Parallel Pad Data Out 13
MPPDO13 Writes to the data register that stores the value to be driven on the pad in Output mode.
12 Masked Parallel Pad Data Out 12
MPPDO12 Writes to the data register that stores the value to be driven on the pad in Output mode.
11 Masked Parallel Pad Data Out 11
MPPDO11 Writes to the data register that stores the value to be driven on the pad in Output mode.
10 Masked Parallel Pad Data Out 10
MPPDO10 Writes to the data register that stores the value to be driven on the pad in Output mode.
9 Reserved
— Always write zero to this field.
8 Reserved
— Always write zero to this field.
7 Masked Parallel Pad Data Out 7
MPPDO7 Writes to the data register that stores the value to be driven on the pad in Output mode.
6 Masked Parallel Pad Data Out 6
MPPDO6 Writes to the data register that stores the value to be driven on the pad in Output mode.
5 Masked Parallel Pad Data Out 5
MPPDO5 Writes to the data register that stores the value to be driven on the pad in Output mode.
4 Masked Parallel Pad Data Out 4
MPPDO4 Writes to the data register that stores the value to be driven on the pad in Output mode.
3 Masked Parallel Pad Data Out 3
MPPDO3 Writes to the data register that stores the value to be driven on the pad in Output mode.
2 Masked Parallel Pad Data Out 2
MPPDO2 Writes to the data register that stores the value to be driven on the pad in Output mode.
1 Masked Parallel Pad Data Out 1
MPPDO1 Writes to the data register that stores the value to be driven on the pad in Output mode.
0 Masked Parallel Pad Data Out 0
MPPDO0 Writes to the data register that stores the value to be driven on the pad in Output mode.

---

*Page 223*

System Integration Unit Lite2 (SIUL2)

#### 9.6.29 Masked Parallel GPIO Pad Data Output (MPGPDO3 - MPGPDO12)

Offset
For a = 3 to 12:
Register Offset
MPGPDOa 1780h + (a × 4h)
Function
Modifies the pad values associated with PGPDO n selectively and supports only 32-bit accesses. It does not support byte and
half-word accesses.
NOTE
Access this register only with 32-bit writes. 8-bit or 16-bit writes do not modify any bits in the register resulting in a
transfer error. Read access returns 0.
The accesses to each of this register location is coherent with access to the bitwise GPDO n .
For a given MPGPDO x [MPPDO y ], where x is the register instance index and y is the field index, the following equation shows the
equivalent GPDO n [PDO_ n ]:
MPGPDO x [MPPDO y ] = GPDO( x × 16) + (15 - y )[PDO_( x × 16) + (15 - y )]
Following are some of the examples of mapping:
• MPGPDO0[MPPDO15] = GPDO0[PDO_0]
• MPGPDO2[MPPDO15] = GPDO32[PDO_32]
• MPGPDO31[MPPDO0] = GPDO511[PDO_511]
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
MASK MASK MASK MASK MASK MASK MASK MASK MASK MASK MASK MASK MASK MASK MASK MASK
W
15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD
W
O15 O14 O13 O12 O11 O10 O9 O8 O7 O6 O5 O4 O3 O2 O1 O0
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Mask Field 15
Table continues on the next page...

---

*Page 224*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
MASK15 Masks MPPDO15 in the corresponding MPGPDO n register instance.
0b - MPPDO15 is ignored
1b - MPPDO15 is written
30 Mask Field 14
MASK14 Masks MPPDO14 in the corresponding MPGPDO n register instance.
0b - MPPDO14 is ignored
1b - MPPDO14 is written
29 Mask Field 13
MASK13 Masks MPPDO13 in the corresponding MPGPDO n register instance.
0b - MPPDO13 is ignored
1b - MPPDO13 is written
28 Mask Field 12
MASK12 Masks MPPDO12 in the corresponding MPGPDO n register instance.
0b - MPPDO12 is ignored
1b - MPPDO12 is written
27 Mask Field 11
MASK11 Masks MPPDO11 in the corresponding MPGPDO n register instance.
0b - MPPDO11 is ignored
1b - MPPDO11 is written
26 Mask Field 10
MASK10 Masks MPPDO10 in the corresponding MPGPDO n register instance.
0b - MPPDO10 is ignored
1b - MPPDO10 is written
25 Mask Field 9
MASK9 Masks MPPDO9 in the corresponding MPGPDO n register instance.
0b - MPPDO9 is ignored
1b - MPPDO9 is written
24 Mask Field 8
MASK8 Masks MPPDO8 in the corresponding MPGPDO n register instance.
0b - MPPDO8 is ignored
1b - MPPDO8 is written
Table continues on the next page...

---

*Page 225*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
23 Mask Field 7
MASK7 Masks MPPDO7 in the corresponding MPGPDO n register instance.
0b - MPPDO7 is ignored
1b - MPPDO7 is written
22 Mask Field 6
MASK6 Masks MPPDO6 in the corresponding MPGPDO n register instance.
0b - MPPDO6 is ignored
1b - MPPDO6 is written
21 Mask Field 5
MASK5 Masks MPPDO5 in the corresponding MPGPDO n register instance.
0b - MPPDO5 is ignored
1b - MPPDO5 is written
20 Mask Field 4
MASK4 Masks MPPDO4 in the corresponding MPGPDO n register instance.
0b - MPPDO4 is ignored
1b - MPPDO4 is written
19 Mask Field 3
MASK3 Masks MPPDO3 in the corresponding MPGPDO n register instance.
0b - MPPDO3 is ignored
1b - MPPDO3 is written
18 Mask Field 2
MASK2 Masks MPPDO2 in the corresponding MPGPDO n register instance.
0b - MPPDO2 is ignored
1b - MPPDO2 is written
17 Mask Field 1
MASK1 Masks MPPDO1 in the corresponding MPGPDO n register instance.
0b - MPPDO1 is ignored
1b - MPPDO1 is written
16 Mask Field 0
MASK0 Masks MPPDO0 in the corresponding MPGPDO n register instance.
0b - MPPDO0 is ignored
Table continues on the next page...

---

*Page 226*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
1b - MPPDO0 is written
15 Masked Parallel Pad Data Out 15
MPPDO15 Writes to the data register that stores the value to be driven on the pad in Output mode.
14 Masked Parallel Pad Data Out 14
MPPDO14 Writes to the data register that stores the value to be driven on the pad in Output mode.
13 Masked Parallel Pad Data Out 13
MPPDO13 Writes to the data register that stores the value to be driven on the pad in Output mode.
12 Masked Parallel Pad Data Out 12
MPPDO12 Writes to the data register that stores the value to be driven on the pad in Output mode.
11 Masked Parallel Pad Data Out 11
MPPDO11 Writes to the data register that stores the value to be driven on the pad in Output mode.
10 Masked Parallel Pad Data Out 10
MPPDO10 Writes to the data register that stores the value to be driven on the pad in Output mode.
9 Masked Parallel Pad Data Out 9
MPPDO9 Writes to the data register that stores the value to be driven on the pad in Output mode.
8 Masked Parallel Pad Data Out 8
MPPDO8 Writes to the data register that stores the value to be driven on the pad in Output mode.
7 Masked Parallel Pad Data Out 7
MPPDO7 Writes to the data register that stores the value to be driven on the pad in Output mode.
6 Masked Parallel Pad Data Out 6
MPPDO6 Writes to the data register that stores the value to be driven on the pad in Output mode.
5 Masked Parallel Pad Data Out 5
MPPDO5 Writes to the data register that stores the value to be driven on the pad in Output mode.
4 Masked Parallel Pad Data Out 4
MPPDO4 Writes to the data register that stores the value to be driven on the pad in Output mode.
3 Masked Parallel Pad Data Out 3
MPPDO3 Writes to the data register that stores the value to be driven on the pad in Output mode.
2 Masked Parallel Pad Data Out 2
MPPDO2 Writes to the data register that stores the value to be driven on the pad in Output mode.
Table continues on the next page...

---

*Page 227*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
1 Masked Parallel Pad Data Out 1
MPPDO1 Writes to the data register that stores the value to be driven on the pad in Output mode.
0 Masked Parallel Pad Data Out 0
MPPDO0 Writes to the data register that stores the value to be driven on the pad in Output mode.

#### 9.6.30 Masked Parallel GPIO Pad Data Output (MPGPDO13)

Offset
Register Offset
MPGPDO13 17B4h
Function
Modifies the pad values associated with PGPDO n selectively and supports only 32-bit accesses. It does not support byte and
half-word accesses.
NOTE
Access this register only with 32-bit writes. 8-bit or 16-bit writes do not modify any bits in the register resulting in a
transfer error. Read access returns 0.
The accesses to each of this register location is coherent with access to the bitwise GPDO n .
For a given MPGPDO x [MPPDO y ], where x is the register instance index and y is the field index, the following equation shows the
equivalent GPDO n [PDO_ n ]:
MPGPDO x [MPPDO y ] = GPDO( x × 16) + (15 - y )[PDO_( x × 16) + (15 - y )]
Following are some of the examples of mapping:
• MPGPDO0[MPPDO15] = GPDO0[PDO_0]
• MPGPDO2[MPPDO15] = GPDO32[PDO_32]
• MPGPDO31[MPPDO0] = GPDO511[PDO_511]

---

*Page 228*

System Integration Unit Lite2 (SIUL2)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
MASK MASK MASK MASK MASK MASK MASK MASK MASK MASK MASK MASK Reserv Reserv Reserv Reserv
W
15 14 13 12 11 10 9 8 7 6 5 4 ed ed ed ed
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD MPPD Reserv Reserv Reserv Reserv
W
O15 O14 O13 O12 O11 O10 O9 O8 O7 O6 O5 O4 ed ed ed ed
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Mask Field 15
MASK15 Masks MPPDO15 in the corresponding MPGPDO n register instance.
0b - MPPDO15 is ignored
1b - MPPDO15 is written
30 Mask Field 14
MASK14 Masks MPPDO14 in the corresponding MPGPDO n register instance.
0b - MPPDO14 is ignored
1b - MPPDO14 is written
29 Mask Field 13
MASK13 Masks MPPDO13 in the corresponding MPGPDO n register instance.
0b - MPPDO13 is ignored
1b - MPPDO13 is written
28 Mask Field 12
MASK12 Masks MPPDO12 in the corresponding MPGPDO n register instance.
0b - MPPDO12 is ignored
1b - MPPDO12 is written
27 Mask Field 11
MASK11 Masks MPPDO11 in the corresponding MPGPDO n register instance.
0b - MPPDO11 is ignored
1b - MPPDO11 is written
26 Mask Field 10
Table continues on the next page...

---

*Page 229*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
MASK10 Masks MPPDO10 in the corresponding MPGPDO n register instance.
0b - MPPDO10 is ignored
1b - MPPDO10 is written
25 Mask Field 9
MASK9 Masks MPPDO9 in the corresponding MPGPDO n register instance.
0b - MPPDO9 is ignored
1b - MPPDO9 is written
24 Mask Field 8
MASK8 Masks MPPDO8 in the corresponding MPGPDO n register instance.
0b - MPPDO8 is ignored
1b - MPPDO8 is written
23 Mask Field 7
MASK7 Masks MPPDO7 in the corresponding MPGPDO n register instance.
0b - MPPDO7 is ignored
1b - MPPDO7 is written
22 Mask Field 6
MASK6 Masks MPPDO6 in the corresponding MPGPDO n register instance.
0b - MPPDO6 is ignored
1b - MPPDO6 is written
21 Mask Field 5
MASK5 Masks MPPDO5 in the corresponding MPGPDO n register instance.
0b - MPPDO5 is ignored
1b - MPPDO5 is written
20 Mask Field 4
MASK4 Masks MPPDO4 in the corresponding MPGPDO n register instance.
0b - MPPDO4 is ignored
1b - MPPDO4 is written
19 Reserved
— Always write zero to this field.
18 Reserved
— Always write zero to this field.
Table continues on the next page...

---

*Page 230*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
17 Reserved
— Always write zero to this field.
16 Reserved
— Always write zero to this field.
15 Masked Parallel Pad Data Out 15
MPPDO15 Writes to the data register that stores the value to be driven on the pad in Output mode.
14 Masked Parallel Pad Data Out 14
MPPDO14 Writes to the data register that stores the value to be driven on the pad in Output mode.
13 Masked Parallel Pad Data Out 13
MPPDO13 Writes to the data register that stores the value to be driven on the pad in Output mode.
12 Masked Parallel Pad Data Out 12
MPPDO12 Writes to the data register that stores the value to be driven on the pad in Output mode.
11 Masked Parallel Pad Data Out 11
MPPDO11 Writes to the data register that stores the value to be driven on the pad in Output mode.
10 Masked Parallel Pad Data Out 10
MPPDO10 Writes to the data register that stores the value to be driven on the pad in Output mode.
9 Masked Parallel Pad Data Out 9
MPPDO9 Writes to the data register that stores the value to be driven on the pad in Output mode.
8 Masked Parallel Pad Data Out 8
MPPDO8 Writes to the data register that stores the value to be driven on the pad in Output mode.
7 Masked Parallel Pad Data Out 7
MPPDO7 Writes to the data register that stores the value to be driven on the pad in Output mode.
6 Masked Parallel Pad Data Out 6
MPPDO6 Writes to the data register that stores the value to be driven on the pad in Output mode.
5 Masked Parallel Pad Data Out 5
MPPDO5 Writes to the data register that stores the value to be driven on the pad in Output mode.
4 Masked Parallel Pad Data Out 4
MPPDO4 Writes to the data register that stores the value to be driven on the pad in Output mode.
3 Reserved
Table continues on the next page...

---

*Page 231*

System Integration Unit Lite2 (SIUL2)
Table continued from the previous page...
Field Function
Always write zero to this field.
—
2 Reserved
— Always write zero to this field.
1 Reserved
— Always write zero to this field.
0 Reserved
— Always write zero to this field.

---

*Page 232*

