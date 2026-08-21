<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 8 -->

# Chapter 8

# Virtualization Wrapper (VIRT_WRAPPER)

#### 8.1 Chip-specific VIRT_WRAPPER information

#### 8.1.1 System overview of PDAC scheme

This figure shows the interaction of XRDC (containing PDACs), related to different cores (domain IDs), with VIRT_WRAPPER.
Typical cores assignment
using XRDC in software
ConfigC
PDAC2
32 KB
Cortex-M7_0 domain ID = ID B ConfigB
SIUL2
Cortex-M7_0 PDAC1
32 KB REG_PROT
VIRT_WRAPPER
ELE_HSEB domain ID = ID A ConfigA
ELE_HSEB PDAC0
32 KB
ELE_HSEB and/or other domain ID
PDAC3
Config A, B and C
16 KB
(write only)
XRDC
Write access to SIUL2 registers allowed through
PDAC0-2 (ConfigA..C) after VIRT_WRAPPER is configured
Write access to all SIUL2 registers allowed prior
VIRT_WRAPPER is configured
Write access to VIRT_WRAPPER configuration registers only
Figure 14. System overview of PDAC scheme

#### 8.1.2 Initial VIRT_WRAPPER operation

Initially, VIRT_WRAPPER:
• Protects the "ConfigB-C" paths to SIUL2. You must configure VIRT_WRAPPER to define the SIUL2 R/W control registers that
you can access through the "ConfigB-C" VIRT_WRAPPER access paths.
• Does not protect the direct path to SIUL2. It allows write accesses to all the SIUL2 R/W control registers. If PDAC0 is set
to allow write access only to master core, for example ELE_HSEB core (variant supported by the chip), then other masters
cannot program the SIUL2 register. However, if these non-ELE_HSEB masters program SIUL2 registers using other PDAC
slots, a transfer error occurs. Any core can act as master core.

#### 8.1.3 Additional VIRT_WRAPPER details

See the "SIUL2 memory map overview and protection" table, later in this chapter, for an overview of the SIUL2 registers and their
protection attributes.
By default, any core can access SIUL2 registers through PDAC0. This process is called ANY_MASTER. However, after XRDC
configuration, ELE_HSEB:
• Assigns PDACs to the respective cores or domain IDs

---

*Page 95*

Virtualization Wrapper (VIRT_WRAPPER)
• Locks the XRDC configuration
• Programs VIRT_WRAPPER registers for pad assignments
ELE_HSEB accesses PDAC0 solely for the pads having 11b as the value in their corresponding registers. Also, ELE_HSEB
retains the same value for the pads that it stores. In case ELE_HSEB needs to take control of some pins, it can still configure
PDAC n to be accessible not only from a specific core master, but also from ELE_HSEB, before it allows other cores to execute.
PDAC3 protects the IPS register portal of VIRT_WRAPPER configuration registers that any master can access but only via the
memory slot assigned to PDAC3. The configuration via IPS register portal can be locked to prevent any further changes until the
next functional reset. PDAC3 implements this locking.
The protection information of the parallel port output registers is inherited from the protection information of the included pads,
according to these rules:
• When all the pads assigned to a GPIO port share the same protection information, the corresponding parallel port output
register for this port inherits the same protection information.
• When at least one of the pads assigned to a GPIO port has different protection information than the other pads assigned
to this GPIO port, access to the corresponding parallel port output register for this port is disabled for all the masters.
Because of the aforementioned protection group mapping, the data bits of a register encode the protection control
information related to one full GPIO port, or a chunk of 16 pads.
• You can assign the following SIUL2 registers to individual PDAC control:
— DISR0
— DIRER0
— DIRSR0
— IREER0
— IFEER0
— IFER0
— IFMCR0–31
— IFCPR
Then, you can access all these registers only through the assigned PDAC because the
VIRT_WRAPPER_REG_C1039_1024 register controls the SIUL2 interrupt registers.
• You cannot access the SIUL2 R/W control registers configured through PDAC3 to be accessible through the ConfigA
VIRT_WRAPPER access path, using the ConfigB VIRT_WRAPPER access path.
• You cannot access any of the SIUL2 R/W control registers through ConfigB-C VIRT_WRAPPER access paths prior to
VIRT_WRAPPER configuration. By default, any core can access SIUL2 registers through PDAC0 after reset.
• The same PDAC that accesses the main SIUL2 registers is used to access the SIUL2-mirrored registers and soft-lock-bit
registers. This is because these registers are a part of the same 16 KB space.
• Access to mirrored SIUL2 registers sets the bit in Soft lock bit as an inherent property of register protection. See the
"Register Protection" chapter for details on the soft lock bit behavior, when accessing the mirrored region.

#### 8.2 Introduction

Virtualization refers to the various techniques, methods, or approaches of creating a virtual (rather than actual) version of
something, such as a virtual hardware platform, operating system (OS), storage device, or network resources. The term "hardware
virtualization" refers to the creation of a virtual machine that acts like a real computer with an operating system. The underlying
hardware resources separate the software executed on these virtual machines (named "guest program"). In many cases, the
specifically modified guest programs are required to run in such a virtual environment.
There are different types of hardware virtualization. One of them is para-virtualization. The para-virtualization is a non-simulated
hardware environment. However, a guest program is executed in its own isolated domain, as if it is running on a separate

---

*Page 96*

Virtualization Wrapper (VIRT_WRAPPER)
system. Such a behavior is especially beneficial for the software targeted toward functional safety, because it allows freedom of
interference for certain aspects of this software.
The hardware-assisted virtualization is a way of improving the efficiency of hardware virtualization. It involves employing specially
designed CPUs and hardware components that help improve the performance of a guest environment. VIRT_WRAPPER,
described in this chapter, is such a hardware component.

#### 8.2.1 Overview

VIRT_WRAPPER is an extension of the Register Protection (REG_PROT) wrapper methodology. It works in parallel with an
existing REG_PROT wrapper by virtualizing registers within the virtualized module. In this chip, SIUL2 is the virtualized module.
Virtualized module is the module instance associated with VIRT_WRAPPER. Virtualization is implemented by protecting (for
example, granting or inhibiting) the access to a register within the virtualized module, dependent on the specific criteria. It also
virtualizes registers within REG_PROT when associated with registers within the virtualized module.
Registers within virtualized module or the REG_PROT wrapper are virtualized by granting, or inhibiting the access to different
PDAC (Peripheral domain access control (note that PDAC and PDAC slot are interchangeable terms.)) s as encoded within
virtualization pad assignments in VIRT_WRAPPER configuration registers. For this purpose, the virtualization information is
programmed into VIRT_WRAPPER configuration registers that specify the grouping of registers within virtualized module. Upon
an access to virtualized module through the peripheral interface, the grouping information is exercised to identify whether the
corresponding transaction should be granted, or inhibited. A corresponding signal is forwarded to the REG_PROT wrapper
that then grants, or inhibits the transaction. The following figure depicts the usage of VIRT_WRAPPER in combination with the
REG_PROT wrapper and virtualized module.
Peripheral bus (for VIRT_WRAPPER)
REG_PROT
setup
Protection setup
information
Access
Address Peripheral
Peripheral
allowed
space enable
bus
?
Register
grouping
Grant/inhibit
Virtualized
REG_PROT
IPS (for register access of VM module (VM)
(for example, SIUL2))
Figure 15. VIRT_WRAPPER block diagram
On a high level, VIRT_WRAPPER is an extension of the REG_PROT wrapper along those lines:
• There is a set of GPRs that receive data over peripheral bus of the VIRT_WRAPPER module. The output of these clients is
a quasi-static bus providing the "Protection Control Information" to the remaining logic; this information is kept stable, that is
programmed by specific PDAC after boot-up. That should be accessed only by that initiator(s) that is accessing PDAC0. This
is taken care by XRDC programming at the SoC.
• The remaining functionality added by VIRT_WRAPPER module consists of two sub-blocks:
— A first sub-block (the "Grouping PLA") defines the grouping of the registers within the virtualized module. This sub-block
is module specific and generated, since this information is specific for the virtualized module, It receives the address
information from the peripheral bus and selects the corresponding data from the "Protection Control Information".

---

*Page 97*

Virtualization Wrapper (VIRT_WRAPPER)
— A second sub-block (the "Protection" sub-block) encodes the previously selected "Protection Control Information" and
combines it with the information about the received access request. The current protection methodology allows to inhibit
only write accesses for a specific initiator or set of initiators. For this purpose, this sub-block evaluates the received
access information; especially the byte enables and the PDAC of the accessing initiator. The output of this sub-block is
an internal signal "access granted" that is used to flag an access inhibited by the virtualization information.

#### 8.2.2 Features

VIRT_WRAPPER includes these features:
• A programmable chip-specific virtualization setup, capable to select a subset of the available initiators in combination with up
to four available address spaces
• Virtualizing accesses to the registers within a VM (Virtualized module, such as SIUL2) by inhibiting accesses to a subset of
these registers under control of the specified virtualization information. Only write accesses can be inhibited for a specific
initiator or set of initiators.
• Inhibiting accesses to registers within REG_PROT associated with VM; in cases, these registers are associated with a register
in the VM to which the access is also inhibited. Additionally, accesses to the global control register (GCR) within REG_PROT
can be protected separately.

#### 8.2.3 Modes of operation

VIRT_WRAPPER is operable when the VM is operable. For details about the availability of the VM, see the chapter of the
corresponding module. When there is no virtualization information specified for the VIRT_WRAPPER module, if XRDC is not
configured, PDAC slot 0 is open as the default value of configuration register is 11b that is assigned with PDAC slot 0 and any
initiator can access through it.
For more details, see "Chip specific VIRT_WRAPPER information".

#### 8.3 Functional description

This section describes the following topics:
• PDAC-based protection scheme
• Register group mapping considering SIUL2
• Access errors

#### 8.3.1 PDAC-based protection scheme

VIRT_WRAPPER uses a protection scheme of the PDACs sub-blocks of XRDC. Each PDAC slot is assigned to one or more cores
(by domain ID assignment in XRDC) as shown in Chip-specific VIRT_WRAPPER information section. Only the write accesses are
protected by the virtualization feature. Read accesses are not impacted.
For any protection group defined within the register, two bits specify the protection control information according to the
following table.
Table 30. Protection control information for a single group
Value Mnemonic Protection Description
00 PDAC1 Restricted access of registers, Protection setup information for PDAC slot 1 is used.
MSCR, IMCR, and INT INT, and GCR
to PDAC slot 1
11 PDAC0 By default, any core can access Protection setup information for PDAC slot 0 is used.
SIUL2 registers through PDAC slot
0.

---

*Page 98*

Virtualization Wrapper (VIRT_WRAPPER)

#### 8.3.2 Clocking

This module has no major clocking considerations.

#### 8.3.3 Register group mapping for SIUL2

For the SIUL2 instance, the corresponding virtualization information within the VIRT_WRAPPER module is defined on a per-pad
basis. Additionally, the virtualization information protects the control registers (controlling the input multiplexing scheme).
Table 31. Register group mapping details
Register name Description
Reg A Virtualization of pad output registers
Reg B Virtualization of input multiplexing control registers
Reg C Virtualization of interrupt control registers
Reg D Virtualization of interrupt control registers
8.3.3.1 Virtualization of pad output registers
Any functional pad (controlled by the SIUL2 instance) is assigned to a separate protection group. This scheme excludes the pads
that an MSCR register does not control within an SIUL2 instance (for example, power supply pads). As a result, the protection
granularity defined by the virtualization information is a single pad. The control information associated with this protection group
affects all the registers related to this pad.
The protection control information for pad i is specified within the protection group i ; thus, it enables a very simple assignment
scheme for the related protection control data that is hard-coded. The protection control information for pad i affects all the
registers related to this pad, in the MSCR i and the GPDO i registers (in case such registers exist).
SIUL2 can control up to 512 pads that the MSCR, GPDO, and GPDI registers can access individually. The actual number of
pads associated with a SIUL2 instance is specific to implementation. GPIO pads are also organized in GPIO ports consisting of
a maximum of 16 pads that the parallel port registers (PGPDO, PGPDI, MPGPDO) can access.
Only the pad control and pad output registers are protected. Accesses to the read-only registers GPDI and PGPDI are not affected
by virtualization.
As the protection granularity is a single pad, any virtualization information associated with this pad affects the corresponding
MCSR and GPDO registers directly. Additionally, any consequence that an illegal access has on the registers implemented within
the REG_PROT wrapper is inhibited. For this purpose, any access to the mirrored address space and the corresponding Soft Lock
Bit Register (SLBR) is also observed.
The parallel port output registers (PGPDO, MPGPDO) inherit the protection information from the protection information of the
included pads according to the following rules:
• When all pads assigned to a GPIO port share the same protection information, the corresponding parallel port output register
for this port inherits the same protection information.
• When at least one of the pads assigned to a GPIO port has a different protection information than the other pads assigned
to this GPIO port, the write access to the corresponding parallel port output register (PGPDO and MPGPDO) for this port is
disabled for all the initiators through all the PDAC slots and a transfer error is generated.
Due to the aforementioned protection group mapping, the data bits of a register encode the protection control information related
to one full GPIO port, or a chunk of 16 pads.
8.3.3.2 Virtualization of input multiplexing control registers
Additionally, the SIUL2 instance supports the control of an input multiplexer (INMUX) scheme. This scheme provides the capability
to select one of a set of the input pads to be the source of an input function for some of the peripheral modules. Any INMUX

---

*Page 99*

Virtualization Wrapper (VIRT_WRAPPER)
controlled by the SIUL2 instance is assigned to a separate protection group. It therefore provides an equivalent protection
granularity within the virtualization information.
The protection group 512+ i specifies the protection control information for the input multiplexer INMUX i . This enables a very
simple assignment scheme for the related protection control data that is hard-coded. The protection control information for INMUX
i affects the input multiplexing control register related to this input—namely, IMCR i .
SIUL2 can control up to 512 MSCRs and 512 IMCRs input multiplexers that the IMCRs can access individually. GPIO inputs
are not affected by the input multiplexer scheme. Therefore, the associated virtualization information does not affect the
corresponding pad input registers.
As the protection granularity is a single INMUX, any virtualization information associated with this INMUX affects the
corresponding IMCR directly. Additionally, any consequence that an illegal access has on the registers implemented within the
REG_PROT wrapper is inhibited. For this purpose, any access to the mirrored address space and the corresponding SLBR is
also observed.
8.3.3.3 Virtualization of interrupt control registers
Additionally, the complete set of interrupt control registers within SIUL2 (address offset range 0010h–00C3h) can be protected as
a separate group (protection group #1024 ).
Another additional protection group (protection group #1055 ) is defined for the global control register (GCR) of the related
REG_PROT wrapper.
The PDAC that accesses the main SIUL2 registers is used to access the SIUL2-mirrored registers and soft-lock-bit registers. This
is because these registers are a part of the same 16 KB space.
Access to mirrored SIUL2 registers sets the bit in Soft lock bit as an inherent property of register protection. See the "Register
Protection" chapter for details on the soft lock bit behavior, when accessing the mirrored region.
The following table shows an overview of the SIUL2 registers and their protection attributes.
Table 32. SIUL2 memory map overview and protection
Offset range Register Size Protected Description
(bits)
0000–000Fh MIDR1, MIDR2 32 N Read-only registers, not protected
0010–00C3h Interrupt registers 32 Y Protected as a single group—no individual protection
of related registers
1
0240–A3Fh MSCR 32 Y Amount of registers defines maximum number of
PADs to be controlled and protected
0A40–0123Fh IMCR 32 Y Amount of registers defines maximum number of
INMUXes to be controlled and protected
1300–14FFh GPDO GPDO[0] (8- 8 Y There are fewer GPDO registers than MSCR registers,
bit) register controls as some PADs are not made available as GPIO
single PAD[0] pad PADs, but their electrical characteristics can still be
means that if PAD[0] programmed
is assigned to PDAC
slot 1 then PDAC
slot 1 can write to
GPDO[0] (8-bit).
1500–16FFh GPDI 8 N PAD input register, not protected
1700–173Fh PGPDO PGPDO[0] 16 Y Writable with 8-, 16-, and as a pair with 32-bit
(16-bit) register accesses
Table continues on the next page...

---

*Page 100*

Virtualization Wrapper (VIRT_WRAPPER)
Table 32. SIUL2 memory map overview and protection (continued)
Offset range Register Size Protected Description
(bits)
controls PAD[0-15]
pads meaning if only
all the PADs[0-15]
are assigned to
PDAC slot 1, then
PDAC slot 1 can
write to PGPDO[0]
(16-bit).
1740–177Fh PGPDI 16 N PAD input register, not protected
1780–17FFh MPGPDO 32 Y Only writable with 32-bit accesses
MPGPDO[0] (32-bit)
register controls
PAD[0-15] pads
meaning if only all
the PADs[0-15] are
assigned to PDAC
slot 1, then PDAC
slot 1 can write
to MPGPDO[0] (32-
bit).
2010–20C3h Interrupt registers 32 Y Mirrored access to interrupt registers in SIUL2
(mirror) The DISR0 instance
DIRER0 DIRSR0
IREER0 IFEER0
IFER0 IFMCR0-31
IFCPR0 can
be assigned to
individual PDAC
control, then all
these registers are
only accessible
through assigned
2
PDAC.
3
2240–2A3Fh MSCR (mirror) 32 Y Mirrored access to MSCRs in SIUL2 instance
3
2A40–323Fh IMCR (mirror) 32 Y Mirrored access to IMCRs in SIUL2 instance
4
3300–34FFh GPDO (mirror) 8 Y Mirrored access to GPDOs in SIUL2 instance
5
4000–47FFh SLBR 8 Y Soft lock bit registers within REG_PROT wrapper for
this SIUL2 instance
4800–48FFh GCR 32 Y
Global configuration register within REG_PROT
wrapper for this SIUL2 instance. There is a single
register in configuration space that is area 4 of
REG_PROT. The size mentioned is to make the whole
area a multiple of 4 kB. See the table "Address accesses
Table continues on the next page...

---

*Page 101*

Virtualization Wrapper (VIRT_WRAPPER)
Table 32. SIUL2 memory map overview and protection (continued)
Offset range Register Size Protected Description
(bits)
as a function of PROT_MEM" in the REG_PROT
chapter to know the exact area for GCRs.
1. The SIUL2 block guide specifies the support of a maximum of 512 MSCRs.
2. Access to mirrored register address range results in a change of the related SLBR within the REG_PROT wrapper if
REG_PROT protects the register. For registers unprotected by REG_PROT, a write to a register mirror has the same
impact as a write to the register.
3. Access to mirrored register address range results in a change of the related SLBR within the REG_PROT wrapper if
REG_PROT protects the register. For registers unprotected by REG_PROT, write to register mirror has the same impact
as write to register.
4. Access to mirrored register address range results in a change of the related SLBR within the REG_PROT wrapper if
REG_PROT protects the register. For registers unprotected by REG_PROT, a write to a register mirror has the same
impact as a write to the register.
5. Only SLBRs protecting accesses to the interrupt registers, IMCRs, and the MSCR registers are protected. See the register
protection sheet attached to the reference manual for a complete list of SIUL2 registers that REG_PROT protects.

#### 8.4 External signals

This module has no external signals.

#### 8.5 Initialization

This module does not require initialization.

#### 8.6 Application Information

This module supports virtualization of accesses to the registers within a SIUL2 by inhibiting or granting accesses to a subset of
these registers under control of the specified initiator(core) information.

#### 8.7 VIRT_WRAPPER memory map register descriptions

NOTE
Access to reserved spaces outside the register bank and holes (unimplemented registers) within register bank
generates the transfer error.

#### 8.7.1 VIRT_WRAPPER memory map

VIRT_WRAPPER base address: 402A_8000h
Offset Register Access Reset value
Width
(In bits)
0h Parameter_n Register (REG_A15_0) 32 RW FFFF_FFFFh
4h Parameter_n Register (REG_A31_16) 32 RW FFFF_FFFFh
8h Parameter_n Register (REG_A47_32) 32 RW FFFF_FFFFh
Ch Parameter_n Register (REG_A63_48) 32 RW FFFF_FFFFh
10h Parameter_n Register (REG_A79_64) 32 RW FFFF_FFFFh
Table continues on the next page...

---

*Page 102*

Virtualization Wrapper (VIRT_WRAPPER)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
14h Parameter_n Register (REG_A95_80) 32 RW FFFF_FFFFh
18h Parameter_n Register (REG_A111_96) 32 RW FFFF_FFFFh
1Ch Parameter_n Register (REG_A127_112) 32 RW FFFF_FFFFh
20h Parameter_n Register (REG_A143_128) 32 RW FFFF_FFFFh
24h Parameter_n Register (REG_A159_144) 32 RW FFFF_FFFFh
28h Parameter_n Register (REG_A175_160) 32 RW FFFF_FFFFh
2Ch Parameter_n Register (REG_A191_176) 32 RW FFFF_FFFFh
30h Parameter_n Register (REG_A207_192) 32 RW FFFF_FFFFh
34h Parameter_n Register (REG_A223_208) 32 RW FFFF_FFFFh
38h Parameter_n Register (REG_A239_224) 32 RW FFFF_FFFFh
3Ch Parameter_n Register (REG_A255_240) 32 RW FFFF_FFFFh
40h Parameter_n Register (REG_A271_256) 32 RW FFFF_FFFFh
44h Parameter_n Register (REG_A287_272) 32 RW FFFF_FFFFh
48h Parameter_n Register (REG_A303_288) 32 RW FFFF_FFFFh
4Ch Parameter_n Register (REG_A319_304) 32 RW FFFF_FFFFh
50h Parameter_n Register (REG_A335_320) 32 RW FFFF_FFFFh
54h Parameter_n Register (REG_A351_336) 32 RW FFFF_FFFFh
58h Parameter_n Register (REG_A367_352) 32 RW FFFF_FFFFh
5Ch Parameter_n Register (REG_A383_368) 32 RW FFFF_FFFFh
60h Parameter_n Register (REG_A399_384) 32 RW FFFF_FFFFh
64h Parameter_n Register (REG_A415_400) 32 RW FFFF_FFFFh
68h Parameter_n Register (REG_A431_416) 32 RW FFFF_FFFFh
6Ch Parameter_n Register (REG_A447_432) 32 RW FFFF_FFFFh
70h Parameter_n Register (REG_A463_448) 32 RW FFFF_FFFFh
74h Parameter_n Register (REG_A479_464) 32 RW FFFF_FFFFh
78h Parameter_n Register (REG_A495_480) 32 RW FFFF_FFFFh
7Ch Parameter_n Register (REG_A511_496) 32 RW FFFF_FFFFh
80h Parameter_n Register (REG_B527_512) 32 RW FFFF_FFFFh
84h Parameter_n Register (REG_B543_528) 32 RW FFFF_FFFFh
88h Parameter_n Register (REG_B559_544) 32 RW FFFF_FFFFh
8Ch Parameter_n Register (REG_B575_560) 32 RW FFFF_FFFFh
Table continues on the next page...

---

*Page 103*

Virtualization Wrapper (VIRT_WRAPPER)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
90h Parameter_n Register (REG_B591_576) 32 RW FFFF_FFFFh
94h Parameter_n Register (REG_B607_592) 32 RW FFFF_FFFFh
98h Parameter_n Register (REG_B623_608) 32 RW FFFF_FFFFh
9Ch Parameter_n Register (REG_B639_624) 32 RW FFFF_FFFFh
A0h Parameter_n Register (REG_B655_640) 32 RW FFFF_FFFFh
A4h Parameter_n Register (REG_B671_656) 32 RW FFFF_FFFFh
A8h Parameter_n Register (REG_B687_672) 32 RW FFFF_FFFFh
ACh Parameter_n Register (REG_B703_688) 32 RW FFFF_FFFFh
B0h Parameter_n Register (REG_B719_704) 32 RW FFFF_FFFFh
B4h Parameter_n Register (REG_B735_720) 32 RW FFFF_FFFFh
B8h Parameter_n Register (REG_B751_736) 32 RW FFFF_FFFFh
BCh Parameter_n Register (REG_B767_752) 32 RW FFFF_FFFFh
C0h Parameter_n Register (REG_B783_768) 32 RW FFFF_FFFFh
C4h Parameter_n Register (REG_B799_784) 32 RW FFFF_FFFFh
C8h Parameter_n Register (REG_B815_800) 32 RW FFFF_FFFFh
CCh Parameter_n Register (REG_B831_816) 32 RW FFFF_FFFFh
D0h Parameter_n Register (REG_B847_832) 32 RW FFFF_FFFFh
D4h Parameter_n Register (REG_B863_848) 32 RW FFFF_FFFFh
D8h Parameter_n Register (REG_B879_864) 32 RW FFFF_FFFFh
DCh Parameter_n Register (REG_B895_880) 32 RW FFFF_FFFFh
E0h Parameter_n Register (REG_B911_896) 32 RW FFFF_FFFFh
E4h Parameter_n Register (REG_B927_912) 32 RW FFFF_FFFFh
E8h Parameter_n Register (REG_B943_928) 32 RW FFFF_FFFFh
ECh Parameter_n Register (REG_B959_944) 32 RW FFFF_FFFFh
F0h Parameter_n Register (REG_B975_960) 32 RW FFFF_FFFFh
F4h Parameter_n Register (REG_B991_976) 32 RW FFFF_FFFFh
F8h Parameter_n Register (REG_B1007_992) 32 RW FFFF_FFFFh
FCh Parameter_n Register (REG_B1023_1008) 32 RW FFFF_FFFFh
100h Parameter_n Register (REG_C1039_1024) 32 RW FFFF_FFFFh
104h Parameter_n Register (REG_D1055_1040) 32 RW FFFF_FFFFh

---

*Page 104*

Virtualization Wrapper (VIRT_WRAPPER)

#### 8.7.2 Parameter_n Register (REG_A15_0 - REG_A511_496)

Offset
Register Offset
REG_A15_0 0h
REG_A31_16 4h
REG_A47_32 8h
REG_A63_48 Ch
REG_A79_64 10h
REG_A95_80 14h
REG_A111_96 18h
REG_A127_112 1Ch
REG_A143_128 20h
REG_A159_144 24h
REG_A175_160 28h
REG_A191_176 2Ch
REG_A207_192 30h
REG_A223_208 34h
REG_A239_224 38h
REG_A255_240 3Ch
REG_A271_256 40h
REG_A287_272 44h
REG_A303_288 48h
REG_A319_304 4Ch
REG_A335_320 50h
REG_A351_336 54h
REG_A367_352 58h
REG_A383_368 5Ch
REG_A399_384 60h
REG_A415_400 64h
REG_A431_416 68h
REG_A447_432 6Ch
REG_A463_448 70h
REG_A479_464 74h
Table continues on the next page...

---

*Page 105*

Virtualization Wrapper (VIRT_WRAPPER)
Table continued from the previous page...
Register Offset
REG_A495_480 78h
REG_A511_496 7Ch
Function
This register set is for PAD0-511. They control MSCR, GPDO, PGPDO, and MPGPDO. Two bits assigned per PDAC have
attributes of one of the implemented PDACs:
• 00-SIUL2_VIRTWRAPPER_PDAC1
• 11-SIUL2_VIRTWRAPPER_PDAC0
NOTE
• Any initiator can access holes (unimplemented registers) of SIUL2 through PDAC0.
• After reset, when XRDC is not configured, any initiator can access SIUL2 registers through PDAC0 without
getting transfer error. However, if SIUL2 registers are accessed through other PDAC slots, a transfer error will
be generated.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
PAD_15 PAD_14 PAD_13 PAD_12 PAD_11 PAD_10 PAD_9 PAD_8
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PAD_7 PAD_6 PAD_5 PAD_4 PAD_3 PAD_2 PAD_1 PAD_0
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
PAD_15
31-30
00b - SIUL2_VIRTWRAPPER_PDAC1
PAD_15
11b - SIUL2_VIRTWRAPPER_PDAC0
PAD_14
29-28
00b - SIUL2_VIRTWRAPPER_PDAC1
PAD_14
11b - SIUL2_VIRTWRAPPER_PDAC0
PAD_13
27-26
PAD_13
Table continues on the next page...

---

*Page 106*

Virtualization Wrapper (VIRT_WRAPPER)
Table continued from the previous page...
Field Function
00b - SIUL2_VIRTWRAPPER_PDAC1
11b - SIUL2_VIRTWRAPPER_PDAC0
PAD_12
25-24
00b - SIUL2_VIRTWRAPPER_PDAC1
PAD_12
11b - SIUL2_VIRTWRAPPER_PDAC0
PAD_11
23-22
00b - SIUL2_VIRTWRAPPER_PDAC1
PAD_11
11b - SIUL2_VIRTWRAPPER_PDAC0
PAD_10
21-20
00b - SIUL2_VIRTWRAPPER_PDAC1
PAD_10
11b - SIUL2_VIRTWRAPPER_PDAC0
PAD_9
19-18
00b - SIUL2_VIRTWRAPPER_PDAC1
PAD_9
11b - SIUL2_VIRTWRAPPER_PDAC0
PAD_8
17-16
00b - SIUL2_VIRTWRAPPER_PDAC1
PAD_8
11b - SIUL2_VIRTWRAPPER_PDAC0
PAD_7
15-14
00b - SIUL2_VIRTWRAPPER_PDAC1
PAD_7
11b - SIUL2_VIRTWRAPPER_PDAC0
PAD_6
13-12
00b - SIUL2_VIRTWRAPPER_PDAC1
PAD_6
11b - SIUL2_VIRTWRAPPER_PDAC0
PAD_5
11-10
00b - SIUL2_VIRTWRAPPER_PDAC1
PAD_5
11b - SIUL2_VIRTWRAPPER_PDAC0
PAD_4
9-8
00b - SIUL2_VIRTWRAPPER_PDAC1
PAD_4
11b - SIUL2_VIRTWRAPPER_PDAC0
PAD_3
7-6
00b - SIUL2_VIRTWRAPPER_PDAC1
PAD_3
11b - SIUL2_VIRTWRAPPER_PDAC0
Table continues on the next page...

---

*Page 107*

Virtualization Wrapper (VIRT_WRAPPER)
Table continued from the previous page...
Field Function
PAD_2
5-4
00b - SIUL2_VIRTWRAPPER_PDAC1
PAD_2
11b - SIUL2_VIRTWRAPPER_PDAC0
PAD_1
3-2
00b - SIUL2_VIRTWRAPPER_PDAC1
PAD_1
11b - SIUL2_VIRTWRAPPER_PDAC0
PAD_0
1-0
00b - SIUL2_VIRTWRAPPER_PDAC1
PAD_0
11b - SIUL2_VIRTWRAPPER_PDAC0

#### 8.7.3 Parameter_n Register (REG_B527_512 - REG_B1023_1008)

Offset
Register Offset
REG_B527_512 80h
REG_B543_528 84h
REG_B559_544 88h
REG_B575_560 8Ch
REG_B591_576 90h
REG_B607_592 94h
REG_B623_608 98h
REG_B639_624 9Ch
REG_B655_640 A0h
REG_B671_656 A4h
REG_B687_672 A8h
REG_B703_688 ACh
REG_B719_704 B0h
REG_B735_720 B4h
REG_B751_736 B8h
REG_B767_752 BCh
REG_B783_768 C0h
REG_B799_784 C4h
Table continues on the next page...

---

*Page 108*

Virtualization Wrapper (VIRT_WRAPPER)
Table continued from the previous page...
Register Offset
REG_B815_800 C8h
REG_B831_816 CCh
REG_B847_832 D0h
REG_B863_848 D4h
REG_B879_864 D8h
REG_B895_880 DCh
REG_B911_896 E0h
REG_B927_912 E4h
REG_B943_928 E8h
REG_B959_944 ECh
REG_B975_960 F0h
REG_B991_976 F4h
REG_B1007_992 F8h
REG_B1023_1008 FCh
Function
Controls access to pads 0 to 255 specific for IMCR registers. Two bits assigned per IMCR register have attributes of one of the
implemented PDACs:
• 00-SIUL2_VIRTWRAPPER_PDAC1
• 11-SIUL2_VIRTWRAPPER_PDAC0
NOTE
• Any initiator can access holes (unimplemented registers) of SIUL2 through PDAC0.
• After reset, when XRDC is not configured, any initiator can access SIUL2 registers through PDAC0 without
getting transfer error. However, if SIUL2 registers are accessed through other PDAC slots, a transfer error will
be generated.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
INMUX_15 INMUX_14 INMUX_13 INMUX_12 INMUX_11 INMUX_10 INMUX_9 INMUX_8
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
INMUX_7 INMUX_6 INMUX_5 INMUX_4 INMUX_3 INMUX_2 INMUX_1 INMUX_0
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1

---

*Page 109*

Virtualization Wrapper (VIRT_WRAPPER)
Fields
Field Function
INMUX_15
31-30
00b - SIUL2_VIRTWRAPPER_PDAC1
INMUX_15
11b - SIUL2_VIRTWRAPPER_PDAC0
INMUX_14
29-28
00b - SIUL2_VIRTWRAPPER_PDAC1
INMUX_14
11b - SIUL2_VIRTWRAPPER_PDAC0
INMUX_13
27-26
00b - SIUL2_VIRTWRAPPER_PDAC1
INMUX_13
11b - SIUL2_VIRTWRAPPER_PDAC0
INMUX_12
25-24
00b - SIUL2_VIRTWRAPPER_PDAC1
INMUX_12
11b - SIUL2_VIRTWRAPPER_PDAC0
INMUX_11
23-22
00b - SIUL2_VIRTWRAPPER_PDAC1
INMUX_11
11b - SIUL2_VIRTWRAPPER_PDAC0
INMUX_10
21-20
00b - SIUL2_VIRTWRAPPER_PDAC1
INMUX_10
11b - SIUL2_VIRTWRAPPER_PDAC0
INMUX_9
19-18
00b - SIUL2_VIRTWRAPPER_PDAC1
INMUX_9
11b - SIUL2_VIRTWRAPPER_PDAC0
INMUX_8
17-16
00b - SIUL2_VIRTWRAPPER_PDAC1
INMUX_8
11b - SIUL2_VIRTWRAPPER_PDAC0
INMUX_7
15-14
00b - SIUL2_VIRTWRAPPER_PDAC1
INMUX_7
11b - SIUL2_VIRTWRAPPER_PDAC0
INMUX_6
13-12
00b - SIUL2_VIRTWRAPPER_PDAC1
INMUX_6
11b - SIUL2_VIRTWRAPPER_PDAC0
INMUX_5
11-10
INMUX_5
Table continues on the next page...

---

*Page 110*

Virtualization Wrapper (VIRT_WRAPPER)
Table continued from the previous page...
Field Function
00b - SIUL2_VIRTWRAPPER_PDAC1
11b - SIUL2_VIRTWRAPPER_PDAC0
INMUX_4
9-8
00b - SIUL2_VIRTWRAPPER_PDAC1
INMUX_4
11b - SIUL2_VIRTWRAPPER_PDAC0
INMUX_3
7-6
00b - SIUL2_VIRTWRAPPER_PDAC1
INMUX_3
11b - SIUL2_VIRTWRAPPER_PDAC0
INMUX_2
5-4
00b - SIUL2_VIRTWRAPPER_PDAC1
INMUX_2
11b - SIUL2_VIRTWRAPPER_PDAC0
INMUX_1
3-2
00b - SIUL2_VIRTWRAPPER_PDAC1
INMUX_1
11b - SIUL2_VIRTWRAPPER_PDAC0
INMUX_0
1-0
00b - SIUL2_VIRTWRAPPER_PDAC1
INMUX_0
11b - SIUL2_VIRTWRAPPER_PDAC0

#### 8.7.4 Parameter_n Register (REG_C1039_1024)

Offset
Register Offset
REG_C1039_1024 100h
Function
Controls access to DISR0, DIRER0, DIRSR0, IREER0, IFEER0, IFER0, IFMCR, and IFCPR0 interrupt registers. Two bits
assigned per PDAC have attributes of one of the implemented PDACs:
• 00-SIUL2_VIRTWRAPPER_PDAC1
• 11-SIUL2_VIRTWRAPPER_PDAC0
NOTE
After reset, when XRDC is not configured, any initiator can access SIUL2 registers through PDAC0 without
getting transfer error. However, if SIUL2 registers are accessed through other PDAC slots, a transfer error will
be generated.

---

*Page 111*

Virtualization Wrapper (VIRT_WRAPPER)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved INTC_CTRL
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
Reserved
31-2
—
1-0 Interrupt register control
INTC_CTRL This bit controls the interrupt register.

#### 8.7.5 Parameter_n Register (REG_D1055_1040)

Offset
Register Offset
REG_D1055_1040 104h
Function
Controls access to GCR protection wrapper registers. Two bits assigned per PDAC have attributes of one of the
implemented PDACs:
• 00-SIUL2_VIRTWRAPPER_PDAC1
• 11-SIUL2_VIRTWRAPPER_PDAC0
NOTE
After reset, when XRDC is not configured, any initiator can access SIUL2 registers through PDAC0 without
getting transfer error. However, if SIUL2 registers are accessed through other PDAC slots, a transfer error will
be generated.

---

*Page 112*

Virtualization Wrapper (VIRT_WRAPPER)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
REG_GCR Reserved
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31-30 GCR Register Of REG_PROT
REG_GCR Controls access to GCR protection wrapper registers.
Reserved
29-0
—

#### 8.8 Glossary

VM Virtualized module, such as SIUL2.
PDAC Peripheral domain access control. PDAC and PDAC slot are interchangeable terms.

---

*Page 113*

