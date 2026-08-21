<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 52 -->

# Chapter 52

# Register Protection (REG_PROT)

#### 52.1 Chip-specific REG_PROT information

#### 52.1.1 REG_PROT configuration

The MCU safety relevant configuration registers are protected against unauthorized HW/S changes during read/write/clear
access by implementing them with register protection module. See the REG_PROT details file attached to this document.
52.1.1.1 CMU_BRIDGE register protection
The CMUs in the device reside on a common peripheral slot as CMU_BRIDGE in the system memory map. Figure 190 indicates
the locations of the CMUs alongwith the memory map.
Based on the CMU location in system peripheral memory map, the below locations in the CMU 16KB space are reserved and any
access on these locations results in a bus transfer error.
402B_C018 – 402B_C01F
402B_C030 – 402B_C03F
402B_C050 – 402B_C05F
402B_C078 – 402B_C07F
402B_C098 – 402B_C09F
402B_C0B8 – 402B_CFFF
Since the register protection is available in the device for CMU_BRIDGE, the 4KB offset locations (refers as the Area 2 in register
protection) are also reserved and accesses over these regions also result in bus transfer error.
402B_D018 – 402B_D01F
402B_D030 – 402B_D03F
402B_D050 – 402B_D05F
402B_D078 – 402B_D07F
402B_D098 – 402B_D09F
402B_D0B8 – 402B_DFFF

---

*Page 1507*

Register Protection (REG_PROT)
402B_CFFF
Reserved
402B_C0B8
CMU_5
402B_C0A0
402B_C09F
Reserved
402B_C098
CMU_4
402B_C080
402B_C07F
Reserved
402B_C078
CMU_3
402B_C060
402B_C05F
Reserved
402B_C050
CMU_2
402B_C040
402B_C03F
Reserved
402B_C030
CMU_1
Figure 190. Location of CMUs

#### 52.2 Introduction

REG_PROT offers a mechanism to protect defined memory-mapped address locations, in a module under protection, from
being written.
REG_PROT is a protection module that is located between the module under protection and the peripheral interface. The address
locations to be protected exist in the module under protection and the address locations that can be protected are module specific.
Writes to these protected address locations are locked using a Soft Lock Bit Register (SLBR n ) . Any access to address locations
in the module under protection is restricted if their corresponding Soft lock fields in a Soft Lock Bit Register (SLBR n ) are 1.

---

*Page 1508*

Register Protection (REG_PROT)
The configured soft-lock fields can also be write-restricted by using GCR[HLB] . When GCR[HLB] =1, you cannot write to the
soft-lock fields. The address locations in the module under protection can be restricted to Supervisor mode access using
GCR[UAA] . This is shown in the following figure.
REG_PROT
Peripheral enable
Peripheral
enable
Access allowed?
UAA
Lock GCR
registers HLB
Supervisor access/
address/access size
Platform peripheral interface
Write data
Module
under
Other control signals Protection
Figure 191. Block diagram

#### 52.3 Features

REG_PROT includes these distinctive features:
• Write accesses for the module under protection can be restricted to the supervisor mode only.
• Registers of modules with their register slot size occupying 1 KB to 32 KB of memory-mapped address space, depending on
the PROT_MEM parameter, can be dynamically locked. See Memory map and register definition .
• Multiple ways are present to set the lock bits.
• After the lock bits are configured, they can be protected from changes.

#### 52.4 Modes of operation

The REG_PROT module is operable when the module under protection is operable.

#### 52.5 External signal description

There are no external signals.

#### 52.6 Functional description

The following sections describe the functional characteristics of the REG_PROT module.

#### 52.6.1 General

This module provides a generic register (address) write-protection mechanism. The register protection size can be:
• 32 bits (address == multiples of 4)

---

*Page 1509*

Register Protection (REG_PROT)
• 16 bits (address == multiples of 2)
• 8 bits (address == multiples of 1)
The addresses that are protected and the register protection size depends on the chip and/or module.
For all addresses that are protected, there are SLBR n [SLB m ] bits that specify whether the address is locked. When an address
is locked, it can only be read but not written in any mode (supervisor/normal). If an address is unprotected, the corresponding
SLBR n [SLB m ] bit is always 0, regardless of what the software writes to that field.
NOTE
For more information on register protection specification, see the REG_PROT details file attached to
this document.

#### 52.6.2 Change lock settings

To change the setting whether an address is locked or unlocked, the corresponding SLBR n [SLB m ] bit needs to be changed. This
can be done using the following methods:
• Set the SLBR n [SLB m ] bit(s) by writing to area #2
• Modify the SLBR n [SLB m ] directly by writing to area #3
Both methods are explained in the following sections.
52.6.2.1 Change lock settings via area #2
You can lock a register after writing to it. Note that reading area #2 does not affect soft lock bits. To do so, you can use area # 2.
The following shows an example with PROT_MEM = 8 KB where SLBs are modified using area #2.
When writing 16-bit to address 0008h, MR9 and MR8 in the protected module are updated. The corresponding lock fields remain
unchanged (see the left part of Figure 192 ).
When writing 16-bit to address 2008h, MR9 and MR8 in the protected module are updated. The corresponding lock fields
SLBR2[SLB0:1] become 1, and the lock fields SLBR2[SLB2:3] remain unchanged (see the right part of Figure 192 ).
Write to Write to
16-bit write to address 0008h MR[9:8] 16-bit write to address 2008h MR[9:8]
No change Set lock bits
0 0 0 0 0 0 0 0 SLBR2 0 0 0 0 1 1 0 0 SLBR2
WE0:3 SLB0:3 WE0:3 SLB0:3
Figure 192. Enable locking via area #2
The following figure shows an example where some addresses are protected and some are not.
In Figure 193 , addresses 000Ch and 000Dh are unprotected. Therefore, their corresponding lock fields SLBR3[SLB0:1] are
always 0 (shown in bold). During a 32-bit write access to address 200Ch, the lock fields in SLBR3 change as follows:
• SLBR3[SLB2:3] become 1.
• SLBR3[SLB0:1] remain 0.

---

*Page 1510*

Register Protection (REG_PROT)
Write to
Before write access 32-bit write to address 200Ch MR[15:12]
Set lock bits
After
0 0 0 0 0 0 0 0 SLBR3 0 0 0 0 0 0 1 1 SLBR3
write access
WE0:3 SLB0:3 WE0:3 SLB0:3
Figure 193. Enable locking for protected and unprotected addresses
52.6.2.2 Change lock settings via area #3
The lock bits are located in area #3 (see Table 245 ). You can modify them by writing to them. Each SLB m field in Soft Lock
Bit Register (SLBR n ) has a mask field, WE m , that protects SLB m from modification. This masking makes read-modify-write
operations unnecessary.
Figure 194 shows two modification examples for registers with 8-bit protection. In part A, a write access to SLBR n specifies a mask
value that allows modification of all SLBR n [SLB0:3] fields. Part B specifies a mask that allows modification only to SLBR n [SLB1:3].
Part A Part B
To SLB0 To SLB1 To SLB2 To SLB3 Write data To SLB0 To SLB1 To SLB2 To SLB3 Write data
1 1 1 1 SLBR n [WE0:3] 0 1 1 1 SLBR n [WE0:3]
Change allowed Change allowed
SLB0 SLB1 SLB2 SLB3 SLBR n [SLB0:3] SLB0 SLB1 SLB2 SLB3 SLBR n [SLB0:3]
Figure 194. Change lock settings directly via area #3
Figure 195 shows examples for registers with 16-bit protection.
Part A of Figure 195 shows that for SLBR n :
• The data written to SLBR n [SLB0] is automatically written to SLBR n [SLB1].
• The data written to SLBR n [SLB2] is automatically written to SLBR n [SLB3].
This is because the address reflected by SLBR n [SLB0] and SLBR n [SLB2] are protected 16-bit wise. Note that in this case, the
write enable SLBR n [WE0] and SLBR n [WE2] must be set while SLBR n [WE1] and SLBR n [WE3] don’t matter.
Part B of Figure 195 shows that the data written to SLBR n [SLB0] is automatically written to SLBR n [SLB1]. This is done because
the address reflected by SLBR n [SLB0] has 16-bit protection. In this case, SLBR n [WE0] must be 1 and SLBR n [WE1] does not
matter. SLBR n [SLB2] and SLBR n [SLB3] remain unchanged because SLBR n [WE2] and SLBR n [WE3] are 0.
Part A Part B
To SLB0 To SLB1 To SLB2 To SLB3 Write data To SLB0 To SLB1 To SLB2 To SLB3 Write data
1 X 1 X SLBR n [WE0:3] 1 X 0 0 SLBR n [WE0:3]
Update lock bits Update lock bits
SLB0 SLB1 SLB2 SLB3 SLBR n [SLB0:3] SLB0 SLB1 SLB2 SLB3 SLBR n [SLB0:3]
Figure 195. Change lock settings for addresses with 16-bit protection
Figure 196 shows a register with 32-bit protection. In SLBR n :

---

*Page 1511*

Register Protection (REG_PROT)
• When SLBR n [WE0]=1, the data written to SLBR n [SLB0] is automatically written to SLBR n [SLB1:3] as well.
• When SLBR n [WE0]=0, then SLBR n [SLB0:3] remain unchanged. Note that in this case, the write enable SLBR n [WE0]
must be set while SLBR n [WE1:3] does not matter.
To SLB0 To SLB1 To SLB2 To SLB3 Write data
1 X X X SLBR n [WE0:3]
Update lock bits
SLB0 SLB1 SLB2 SLB3 SLBR n [SLB0:3]
Figure 196. Change lock settings for addresses with 32-bit protection
The following figure shows a mixed protection size configuration.
In SLBR n :
• The data written to SLBR n [SLB0] is mirrored to SLBR n [SLB1] because the corresponding register has 16-bit protection.
• The data written to SLBR n [SLB2] is blocked because the corresponding register is unprotected.
• The data written to SLBR n [SLB3] is successfully written to SLBR n [SLB3] because the corresponding register has 8-bit
protection.
To SLB0 To SLB1 To SLB2 To SLB3 Write data
1 X X 1 SLBR n [WE0:3 ]
Update lock bits
SLB0 SLB1 0 SLB3 SLBR n [SLB0:3 ]
Figure 197. Change lock settings for mixed protection
52.6.2.3 Write protection for locking bits
Changing the locking bits through any of the procedures mentioned in Change lock settings via area #3 and Change lock settings
via area #2 is only possible as long as the field GCR.HLB is cleared. After you write 1 to this field, the locking bits cannot be
modified until there is a system reset.

#### 52.6.3 Access errors

REG_PROT generates transfer errors under several circumstances, as described below. For the area definition, see Memory map
and register definition .
• If accessing area #1 or area #2, REG_PROT sends any access error from the underlying module under protection.
• If User mode is not allowed, the attempted User mode writes to all areas cause a transfer error, and the writes are
blocked.
• If accessing the reserved area, a transfer error is asserted.
• If accessing unimplemented 32-bit registers in areas #3 and #4, a transfer error is asserted.
• If writing to a register in areas #1 and #2, and an SLB field is 1 for any of the affected bytes, then:
— A transfer error is asserted.
— The write is blocked.
— The complete write operation to non-protected bytes in this word is ignored.

---

*Page 1512*

Register Protection (REG_PROT)
• If writing to an SLBR in area #3 with GCR[HLB] =1, a transfer error is asserted.
• Any write operation in any access mode to area #2, when GCR[HLB] =1, is not allowed.

#### 52.6.4 Sequence

You must configure the REG_PROT first. And then, you need to write 1 to the HLB field in REG_PROT. This ensures that registers
under protection are not updated in the user access mode. You need to follow these steps:
1. Enable XRDC protection for the module to enable writes in the supervisor mode only. This step is mandatory, if
protected registers of the module are expected to be configured multiple times.
2. Configure REG_PROT to enable write on registers of module under protection.
3. Configure the registers of module under protection.
4. Configure the REG_PROT for the required SLB fields.
5. Set the HLB field in REG_PROT. This step is mandatory, if protected registers of module are expected to be configured
only once.

#### 52.7 Memory map and register definition

This section provides a detailed description of the memory map of a module using REG_PROT.
The PROT_MEM size varies per chips. It can be:
• 1 KB
• 2 KB
• 4 KB
• 8 KB
• 16 KB
• 32 KB
The resulting memory space is divided into four areas. Memory locations within area #1 and area #2 are a part of the module under
protection. Area #3 and area #4 are a part of REG_PROT.
The proper register slot size for each module is mentioned in the Memory Map file attached to this document.
Following is the memory map for a module that is protected by the REG_PROT module. Reserved registers in area #1 are handled
as specified in the protected module's documentation. There is a single register in configuration space that corresponds to area
#4 while the size mentioned in Area size column is to turn the whole area multiple of 4 KB. See the Offset column to know the exact
location of the GCR register.
Table 245. Area offset based on protection size
PROT_MEM Area Area size Use Offset
1 KB Area 1 1 KB Module Register(MR0—MR1023) 0000h—03FFh
Area 2 1 KB Module Register and Set Soft Lock Bit (LMR0—LMR1023) 0400h—07FFh
Area 3 256 B Soft Lock Bit Register (SLBR0—255) 0800h—08FFh
Area 4 256 B Global Configuration Register (GCR) 0900h—09FFh
1.5 KB Reserved 0A00h— 0FFFh
2 KB Area 1 2 KB Module Register (MR0—MR2047) 0000h—07FFh
Area 2 2KB Module Register and Set Soft Lock Bit (LMR0—LMR2047) 0800h—0FFFh
Table continues on the next page...

---

*Page 1513*

Register Protection (REG_PROT)
Table 245. Area offset based on protection size (continued)
PROT_MEM Area Area size Use Offset
Area 3 512 B Soft Lock Bit Register (SLBR0—511) 1000h—11FFh
Area 4 256 B Global Configuration Register (GCR) 1200h—12FFh
3.25 KB Reserved 1300h—1FFFh
4 KB Area 1 4 KB Module Register (MR0—MR4095) 0000h—0FFFh
Area 2 4 KB Module Register and Set Soft Lock Bit (LMR0—LMR4095) 1000h—1FFFh
Area 3 1 KB Soft Lock Bit Register (SLBR0—1023) 2000h—23FFh
Area 4 256 B Global Configuration Register (GCR) 2400h—24FFh
6.75 KB Reserved 2500h—3FFFh
8 KB Area 1 8 KB Module Register (MR0—MR8191) 0000h—1FFFh
Area 2 8 KB Module Register and Set Soft Lock Bit (LMR0—LMR8191) 2000h—3FFFh
Area 3 2 KB Soft Lock Bit Register (SLBR0—2047) 4000h—47FFh
Area 4 256 B Global Configuration Register (GCR) 4800h—48FFh
1.75 KB Reserved 4900h—4FFFh
16 KB Area 1 16 KB Module Register (MR0—MR16383) 0000h—3FFFh
Area 2 16 KB Module Register and Set Soft Lock Bit (LMR0—LMR16383) 4000h—7FFFh
Area 3 4 KB Soft Lock Bit Register (SLBR0—4095) 8000h—8FFFh
Area 4 4 KB Global Configuration Register (GCR) 9000h—9FFFh
32 KB Area 1 32 KB Module Register (MR0—MR32767) 0000h—7FFFh
Area 2 32 KB Module Register and Set Soft Lock Bit (LMR0—LMR32767) 8000h—FFFFh
Area 3 8 KB Soft Lock Bit Register (SLBR0—8191) 10000h—11FFFh
Area 4 4 KB Global Configuration Register (GCR) 12000h—12FFFh
• Area #1 can take the memory space of 1 KB/2 KB/4 KB/8 KB/16 KB/32 KB, which holds the normal functional module
registers and is transparent for all read/write operations.
• Area #2 can take the memory space of 1 KB/2 KB/4 KB/8 KB/16 KB/32 KB and is a mirror of area #1. For an example,
if area #1 takes 8 KB of memory space, then read/write access to an offset 2000+X reads/writes the register at offset X.
However, a write access to offset 2000+X additionally sets the optional soft lock bits for this offset X in the same cycle
as the register at offset X is written. This provides for an automatic write and lock operation. Not all registers in area #1
need to have protection defined by the associated soft lock bits. For unprotected registers at offset Y, accesses to offset
2000+Y are identical to accesses at offset Y.
• Area #3 can take the memory space of 256 B/512 B/1 KB/2 KB/4 KB/8 KB and hold soft lock bits, one bit per byte in area
#1. The four soft lock bits associated with one module register word are arranged at byte boundaries in the memory map.
The Soft Lock Bit registers can be directly written using a bit mask.
• Area #4 can take the memory space of 1.75 KB/3.5 KB/7 KB/2 KB/4 KB/4 KB and holds the configuration bits of the
protection mode. There is one configuration Hard lock bit per module that prevents all further modifications to the soft lock
bits and can only be cleared by a system reset after it is set. When user access allowed bit is set, it allows user access to
the protected module.

---

*Page 1514*

Register Protection (REG_PROT)
If you access a locked byte with a write transaction, a bus error is issued to the system and the write transaction is not executed.
This is true even if not all accessed bytes are locked.
Accessing unimplemented 32—bit registers in area #3 results in a bus transfer error. In area #4, there will not be any transfer error
for the address range mentioned in the table above because these address spaces of area #4 are mapped to the GCR register.
Accesses in area #4, beyond the address range mentioned in the Module memory map table, are prohibited and might or might
not result into a bus abort.

#### 52.7.1 Module register (MR)

Each functional module has its own unique set of registers. This chapter refers to these registers generically as module registers
(MR), which exist in the lower module memory space (memory area 1), and receive protection from the REG_PROT module.

#### 52.7.2 Module Register and Set Soft Lock Bit (LMR)

This is memory area #2 that provides mirrored access to module registers with the side-effect of setting soft lock bits in case of a
write access to a register that is defined as protectable by the locking mechanism. Each MR byte is protectable by one associated
bit in SLBR n [SLB m ], according to the mapping described in Soft Lock Bit Register (SLBR n ) .

#### 52.8 REG_PROT register descriptions

This register information documents memory area #3 and area #4.

#### 52.8.1 REG_PROT memory map

REG_PROT base address: base address of protected module instance
Table 246. REG_PROT memory map
PROT_MEM Offset Register Access Reset value
Width
(In bits)
1kB 0x800 – 0x8FF Soft Lock Bit Register (SLBR0-255) 8 RW 00h
0x900 – 0x9FF Global Configuration Register (GCR) 32 RW 0000_0000h
2kB 0x1000 – 0x11FF Soft Lock Bit Register (SLBR0-511) 8 RW 00h
0x1200 – 0x12FF Global Configuration Register (GCR) 32 RW 0000_0000h
4kB 0x2000 – 0x23FF Soft Lock Bit Register (SLBR0-1023) 8 RW 00h
0x2400 – 0x24FF Global Configuration Register (GCR) 32 RW 0000_0000h
8kB 0x4000 – 0x47FF Soft Lock Bit Register (SLBR0-2047) 8 RW 00h
0x4800 – 0x48FF Global Configuration Register (GCR) 32 RW 0000_0000h
16kB 0x8000 – 0x8FFF Soft Lock Bit Register (SLBR0-4095) 8 RW 00h
0x9000-0x9FFF Global Configuration Register (GCR) 32 RW 0000_0000h
32kB 0x10000-0x11FFF Soft Lock Bit Register (SLBR0-8191) 8 RW 00h
0x12000 – 0x12FFF Global Configuration Register (GCR) 32 RW 0000_0000h

#### 52.8.2 Soft Lock Bit Register (SLBR n )

Offset
See REG_PROT memory map for SLBR n offset ranges. For SLBR n addresses, see the REG_PROT details file attached to
this document.

---

*Page 1515*

Register Protection (REG_PROT)
Function
These registers hold the Soft Lock Bits (SLBs) for the protected registers in memory area #1, which is the normal register address
space of the protected module. Multiple Soft Lock Bit Registers (SLBR n ) can be implemented, depending on the number of
protected module register bytes. Each SLBR n has four soft lock fields (SLB0-SLB3), each of which controls write access to a byte
in memory area #1. Each soft lock field also has a corresponding write enable field in the same register that controls whether the
soft lock field can be written. The following table shows the mapping between the soft lock fields to the bytes in memory area #1.
Table 247. SLBs vs. protected address
SLB Protected address
SLBR0[SLB0] MR0
SLBR0[SLB1] MR1
SLBR0[SLB2] MR2
SLBR0[SLB3] MR3
SLBR1[SLB0] MR4
SLBR1[SLB1] MR5
SLBR1[SLB2] MR6
SLBR1[SLB3] MR7
SLBR2[SLB0] MR8
... ...
Diagram
Bits 7 6 5 4 3 2 1 0
R 0 0 0 0
SLB0 SLB1 SLB2 SLB3
W WE0 WE1 WE2 WE3
Reset 0 0 0 0 0 0 0 0
Fields
Field Function
7 Write enable fields for SLB
WE0 WE0 enables writing to SLB0.
0b - SLB is not modified.
1b - Value is written to SLB.
6 Write enable fields for SLB
WE1 WE1 enables writing to SLB1.
0b - SLB is not modified.
1b - Value is written to SLB.
5 Write enable fields for SLB
Table continues on the next page...

---

*Page 1516*

Register Protection (REG_PROT)
Table continued from the previous page...
Field Function
WE2 enables writing to SLB2.
WE2
0b - SLB is not modified.
1b - Value is written to SLB.
4 Write enable bits for SLB
WE3 WE3 enables writing to SLB3.
0b - SLB is not modified.
1b - Value is written to SLB.
3 Soft lock fields for one MRn register
SLB0 SLB0 can block accesses to MR[n *4 + 0].
0b - Associated MRn byte is unprotected and writable.
1b - Associated MRn byte is locked against write accesses.
2 Soft lock bits for one MRn register
SLB1 SLB1 can block accesses to MR[n *4 + 1].
0b - Associated MRn byte is unprotected and writable.
1b - Associated MRn byte is locked against write accesses.
1 Soft lock fields for one MRn register
SLB2 SLB2 can block accesses to MR[n *4 + 2].
0b - Associated MRn byte is unprotected and writable.
1b - Associated MRn byte is locked against write accesses.
0 Soft lock fields for one MRn register
SLB3 SLB3 can block accesses to MR[n *4 + 3].
0b - Associated MRn byte is unprotected and writable.
1b - Associated MRn byte is locked against write accesses.

#### 52.8.3 Global Configuration Register (GCR)

Offset
See REG_PROT memory map for GCR offset.
Function
This register controls module level configurations.

---

*Page 1517*

Register Protection (REG_PROT)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
HLB UAA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Hard Lock Bit
HLB This field cannot be cleared after it is set by the software. It can only be cleared by a system reset.
0b - All SLB fields are accessible and can be modified.
1b - All SLB fields are write protected and cannot be modified.
Reserved
30-24
—
23 User Access Allowed
UAA Controls the user and supervisor mode access to registers of the module under protection.
0b - The registers in the module under protection can only be written in the supervisor mode. All
the write accesses in non-supervisor mode are not executed and a transfer error is issued. This
access restriction is in addition to any access restrictions imposed by the protected IP module.
1b - The registers in the module under protection can be accessed in the mode defined for the
module registers without any additional restrictions.
Reserved
22-0
—

#### 52.9 Glossary

PROT_MEM Integration parameter that specifies the size of the module register slot protected. The PROT_MEM size
depends on the chip or module.
Hard lock A lock that restricts access to any register bit. You can unlock or clear this lock through the hardware reset.
Soft lock A lock that restricts access to any register bit. You can unlock or clear this lock through the software.

---

*Page 1518*

