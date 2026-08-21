<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 3 -->

# Chapter 3

# Memory Map

#### 3.1 Introduction

This chip contains various memories and memory-mapped peripherals that are placed in a 32-bit contiguous memory space, and
this chapter describes the memory and peripheral locations within that memory space.
For high-level chip memory map details, see the memory map file attached to this document.

#### 3.2 SRAM memory map

A total of 512 KB RAM is available on MCXE31B product series, and it is partitioned as follows:
• ITCMs (64 KB)
• D0TCMs (64 KB)
• D1TCMs (64 KB)
• PRAMC_0 (160 KB)
• PRAMC_1 (160 KB)
PRAMC_0 controls the first 160 KB of RAM that is implemented as follows:
• 32 KB of memory supports content retention in Standby mode
• 128 KB of memory is available only in Run mode
PRAMC_1 controls the second 160 KB of RAM that is implemented as follows: :
• 32 KB of memory is available only in Run mode
• 128 KB of memory is available only in Run mode
The memory map file attached to this document provides a complete architectural address space definition for various sections
that the RAM is partitioned into and across the MCXE31 product series. Based on the physical sizes of the memories and
peripherals, the actual address regions used may be smaller.
In addition to ITCM , DTCM , and SRAM , a particular section of the RAM is associated with the Arm Cortex-M7 (CM7) internal
cache. This chip includes 8 KB of I-cache RAM and 8 KB of D-cache RAM for each Cortex-M7 core.
The cache attributes in the MCXE31 cache scheme are defined for a given address range and core, and not according to the
physical RAM or flash memory block.

#### 3.3 Access-related details of the memory types used in this chip

The Cortex-M7 core can access these memories sequentially:
• ITCM
• DTCM
• I-cache
• D-cache
ITCM and DTCM can be accessed via 32-bit AHBS interface by any master, e.g., different Cortex-M7 cores, eDMA, etc to bootload
instructions in ITCM. EMAC is another master that can access DTCM. See 'Block diagram' in the 'Introduction' chapter for details
on the transaction path.
Access to SRAM beyond the RAM available on the chip terminates the bus cycle with an error followed by an appropriate response
in the requesting bus master.

---

*Page 24*

Memory Map

#### 3.4 Considerations related to TCM's implementation

You must first initialize TCM (ITCM and DTCM) and system RAMs by 64-bit writes before performing read accesses. The system
RAM can be initialized using eDMA and core. The ITCM initialization can be performed only by core using either direct or back-door
accesses. The DTCM can be initialized also by 32-bit writes performed either using core's direct and back-door accesses as or
eDMA. These writes are required to set up the initial ECC code words after chip power-on reset.
Each Cortex-M7 core is equipped with a 32 KB ITCM and 64 KB DTCM with a zero wait-state access.
See table 'Memory ECC initialization summary' in chapter 'Memory and Memory Interfaces' for details on memory
ECC initialization.

#### 3.5 Flash memory map

For details, see the memory map file attached to this document.

#### 3.6 AIPS-Lite memory map

You can access the peripheral memory map via a crossbar slave port. The next table shows the three regions associated with
peripheral space.
Table 6. Regions associated with peripheral space
Address of region Region description
4000_0000h–401F_FFFFh This 2048 KB region (AIPS_Lite_0) is partitioned into 128 spaces, each 16 KB
in size, having 32 on-platform and 96 off-platform spaces. AIPS_Lite generates
unique module enables for all the 32 on-platform spaces.
4020_0000h–403F_FFFFh This 2048 KB region (AIPS_Lite_1) is partitioned into 128 spaces, each 16 KB
in size, having 32 on-platform and 96 off-platform spaces. AIPS_Lite generates
unique module enables for all the 32 on-platform spaces.
4040_0000h–405F_FFFFh This 2048 KB region (AIPS_Lite_2) is partitioned into 128 spaces, each 16 KB
in size, having 32 on-platform and 96 off-platform spaces. AIPS_Lite generates
unique module enables for all the 32 on-platform spaces.
Modules that are disabled via their clock gate control fields in the MC_CGM registers disable the associated AIPS_Lite slots.
Access to any address within an unimplemented or disabled peripheral bridge slot results in a transfer error termination.
Multiple instances of same peripherals are connected to different bridges on the interconnect. For details, see the memory map
file attached to this document.

#### 3.7 Serialization of memory operations

In particular cases, you must complete the process of writing to a peripheral before the subsequent action occurs. Examples of
such situations include:
• Exiting an interrupt service routine
• Changing a mode
• Configuring a function
In these situations, you must perform a read-after-write sequence to achieve the required serialization of memory operations. The
following table provides this sequence.

---

*Page 25*

Memory Map
Table 7. Read-after-write sequence for serialization of memory operations
Step Action
1 Write to the associated peripheral register.
2 Read the register to verify the write process.
3 Continue with the subsequent operations.

#### 3.8 PPB memory map

PPB is a part of the defined Arm bus architecture and provides access to specific processor-local modules. You can access these
modules only through the core, and not through other system masters.
Table 8. PPB memory map
Starting hex address Ending hex address Size (KB) Module
E000_0000 E000_0FFF 4 ITM
E000_1000 E000_1FFF DWT
E000_2000 E000_2FFF FPB
E000_3000 E000_DFFF 44 —
E000_E000 E000_EFFF 4 SCS
E000_F000 E003_FFFF 196 Reserved
E004_0000 E004_0FFF 4 TPIU
E004_1000 E004_1FFF ETM
E004_2000 E004_2FFF CTI
E004_3000 E004_3FFF —
E004_4000 E004_4FFF
E004_5000 E004_5FFF
E004_6000 E007_FFFF 232
E008_0000 E008_0FFF 4 MCM
E008_1000 E008_1FFF —
E008_2000 E008_2FFF
E008_3000 E00F_EFFF 496
E00F_F000 E00F_FFFF 4 Cortex-M7 PPB ROM table

#### 3.9 Glossary

CTI Cross trigger interface
DTCM Data tightly coupled memory
D-cache Data cache
DWT Debug watchpoint and trace
ETM Embedded trace macrocell

---

*Page 26*

Memory Map
FPB Flash patch and breakpoints
ITCM Instruction tightly coupled memory
I-cache Instruction cache
ITM Instrumentation trace macrocells
PPB Private peripheral bus
SCS System control space
SRAM Static random access memory
TPIU Trace port interface unit

---

*Page 27*

