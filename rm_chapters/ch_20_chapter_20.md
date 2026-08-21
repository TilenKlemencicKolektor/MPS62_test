<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 20 -->

# Chapter 20

# Embedded Flash Memory (c40asf)

#### 20.1 Chip-specific c40asf flash memory information

#### 20.1.1 Flash memory configuration and register settings

Table 87 shows flash memory blocks and their associated configuration.
Table 87. Flash block configuration
Block or Block name Start End address Size Applicability
address address (hex) (hex)
number
0 Code flash memory 0 0040_0000 004F_FFFF 1 MB MCXE31B and MCXE317
512 KB MCXE315 and MCXE316
1 Code flash memory 1 0050_0000 005F_FFFF 1 MB MCXE31B and MCXE317
512 KB MCXE315 and MCXE316
2 Code flash memory 2 0060_0000 006F_FFFF 1 MB MCXE31B
3 Code flash memory 3 0070_0000 007F_FFFF 1 MB MCXE31B
4 Data flash memory 1000_0000 1001_FFFF 128 KB MCXE31B
2 128 KB MCXE317
1000_0000 1000_FFFF 64 KB MCXE315/MCXE316
1
0 UTest NVM 1B00_0000 1B00_1FFF 8 KB MCXE31B, MCXE317, MCXE315
and MCXE316
1. The address region number is the same as block number for all the blocks except this one. Address region is called UTest
NVM address region in this case.
No application cores except ELE_HSEB can access the address regions shown in Table 88 .
Table 88. Secure flash memory configuration
Description MCXE315/MCXE316 MCXE317 MCXE31B Size
Start address End address Start address End address Start address End address
Secure BAF 004F_4000h 004F_FFFFh 005F_4000h 005F_FFFFh 007F_4000h 007F_FFFFh 48 KB
code
(SBAF_COD
E)
NOTE
For HSE memory configuration, see 'Memory map when HSE firmware usage feature flag is enabled' section in
'Boot Overview' chapter.
The flash memory can perform multiple reads in parallel (between different blocks), using the single-, dual-, or quad-read feature.
It also includes an internal UTest mode that can generate single- and double-bit ECC errors.
The code flash memory lets you access three data paths simultaneously.

---

*Page 478*

Embedded Flash Memory (c40asf)
You can configure the flash memory sectors as erase- or write-protected by using the flash memory's sector and super-sector
locking features.
Data flash and/or Code flash memory blocks can be altered by a code executing from different flash block or SRAM.
Data flash memory is the same as code flash memory—supporting program, erase, and read operations.
After the completion of the program or erase operations, the MCR and MCRS registers notify you as soon as the code flash
memory code can be executed. At the end of reset recovery, MCRS[DONE] transitions from 0 to 1.
NOTE
MCRS[RWE] might become 1 during the program or erase operation because of speculative accesses by Cortex-
M7 core(s). If there are memory regions where no speculative accesses must be initiated, Arm recommends to
configure the on-core MPU to set those regions with these attributes:
• Device or strongly-ordered
• Execute never
NOTE
• ADR[A3] and ADR[A4] field are reserved for MCXE317, as code flash memory size in 2 MB for MCXE317.
• For MCXE317, reset value of FLASH_SSPELOCK and FLASH_XSSPELOCK register is 0x0FFF_FFFF.

#### 20.1.2 MCRE register reset value

The reset value of the MCRE register depends on the chip-specific flash memory configuration. This table provides the flash
memory configuration of the chip and the associated register reset values.
Table 89. MCRE register reset value
MCRE fields Reset value
n1M (bit position 23:21) 100b – Four 1 MB blocks
010b – Two 1 MB blocks
n512K (bit position 15:14) 00b – Zero 512 KB blocks
n256K (bit position 7:6) 01b – One 256 KB blocks
MCRE register reset value MCXE31B - 0080_0040h
MCXE317 - 0040_0040h

#### 20.1.3 Debugger considerations while Flash Program/Erase

While flash programming is done via debugger, special considerations should be taken care off. See "Debug Subsystem" chapter
for details.

#### 20.2 Introduction

The primary function of the embedded flash memory serves as an electrically programmable and erasable non-volatile memory
(NVM) that may be used for instruction or data storage.

#### 20.2.1 Overview

The embedded flash memory is designed for use in embedded MCU and SoC applications. It supports a total memory size up to
8.5 MB of NVM in main space and 8 KB of UTest NVM space per module. Multiple flash modules may be instantiated within an SoC
to achieve higher density. The embedded flash memory is addressable by page (256 bits) for read operation and double-word(s)
and page and quad page for program operations. Flash memory reads always return 256 bits, although read page buffering may

---

*Page 479*

Embedded Flash Memory (c40asf)
be performed by the PFC. The flash memory is able to do multiple reads in parallel (between different blocks), utilizing the quad
read feature.
For details on the embedded flash memory architecture and features, see Functional description .
The following figure shows the top-level diagram and functional organization of the flash memory unit.
Vss Vddf Vflash
FMU
Slave Bus
Signals
Configuration Analog
AIPS
Registers
Osc/Timer
Flash Read
Interface
Flash Interface
Ref. Generator
Program/Erase
Flash Write Controller
Platform Interface
Flash
SoC System Bus
BIU
ECC
Signal
Flash Bias
UTest
P/E
Controller
LOCK
sidebands
Flash Block(s)
Misc. OPP
SoC sidebands HW
Factory Test
Blocks Control
Logic
(DCM, Interface
security) Security
sidebands
Test Configuration
Registers
Figure 45. Block diagram

#### 20.2.2 Features

The embedded flash memory includes these distinct features:
• Test information stored in a dedicated non-volatile sector (referred to as the UTest sector)
• OTP space made available in the UTest sector
• Read page size of 256 bits (8 words)
• ECC with single bit correction, double bit detection (all 1s valid) with 64-bit granularity
• Quad page programming (256-bit granularity)
• Hardware programmable sector and super sector program or erase restriction control
• Erasing of selected sector or block erase
• Independent programming of the UTest NVM sector
• Embedded hardware program and erase algorithms
• Support for reading while writing when the accesses are to different blocks
• UTest mode (user-accessible test modes) including array integrity and margin read

---

*Page 480*

Embedded Flash Memory (c40asf)
• Triple-voted flops for safety-critical flash memory functions (for example, internal trimming, redundancy, mode control, and
others)

#### 20.2.3 Modes of operation

Following is a brief description of the embedded flash memory operating modes.
• User mode is the default operating mode of the embedded flash memory. In this mode, it is possible to read and write
registers (including register-based interlock writes), read the memory array, program the memory array, and erase the
memory array. In this mode program and erase operations are initiated by doing register writes. Program and erase
operations are controlled by an internal state machine.
• Low-Power mode turns off all DC current sources within the embedded flash memory and enables VFLASH and VDDF
to be power gated. The embedded flash memory is not accessible for read, write, program, or erase when in Low-Power
mode. This mode results in the lowest current draw that embedded flash memory can achieve.
• UTest mode is a tiered test mode strategy in which a portion of the factory test modes are made available. This mode is
protected but accessible.

#### 20.3 UTest NVM sector

The UTest NVM sector may be enabled by PEADR[PEASAD] during interlock writes. When the UTest NVM space is enabled,
all program and erase operations are mapped to the UTest NVM sector. User-mode programming of the UTest NVM sector is
enabled only when PEADR[PEASAD] is high.
The UTest NVM sector is an OTP sector (assuming Test mode disable seal is written) . Therefore, performing an erase operation
is not permitted.
The UTest NVM sector supports RWW , and is grouped with the sectors in partition 0.
The UTest NVM sector may be locked against program by using hardware program and erase protection input to the flash
module (MCRS[TSPELOCK]).
The UTest NVM sector contains specified data that is needed for embedded flash memory or SoC features.
Programming of the UTest NVM space has restrictions that are similar to those of the main space in terms of how ECC is
calculated. Only one program operation is allowed per 64-bit ECC segment.

#### 20.4 Test mode disable seal

The UTest NVM sector includes a mechanism to disable factory entry into Test mode selectable by block. Extreme care must
be taken when using this feature, because blocks that are selected to be protected in this method will not have possible failures
analyzed by factory failure analysts. Once sealed, the UTest sector becomes OTP (erase locked, and OPP enabled). The UTest
sector is not accessible in Test Mode.
Protection of this sort prevents all high voltage operations to the flash memory executed by the internal state-machine, as well as
reads through this state machine, and reads through the array integrity state machine when using Test mode interfaces.
UTest operations, margin read and array integrity, are also protected by preventing MISR updates on blocks selected for
protection, although reads are still performed, and single bit corrections and double bit detections will be logged. Protection
through other interfaces is provided by normal User mode protection mechanisms and is device-specific.
The method to disable factory entry into Test mode is to first program the Test mode disable seal location to be 5A4B_3C2Dh.
After the next reset is asserted, Test mode is disabled.
It is possible to create a password to enable factory entry into Test mode. This can be programmed into the Test mode disable
override passcode. The passcode may not be 0000_0000h, FFFF_FFFFh, or 5555_5555h. These are all invalid passcodes and
will not be accepted to override. If it is desired, by the customer, that override never be possible, one of the three invalid passcodes
must be put into the Test mode disable override passcode location. Passcode may be entered to authenticate entry, if enabled,
by performing a 32-bit register write to register address 90h.

---

*Page 481*

Embedded Flash Memory (c40asf)
Even if the Test mode disable seal password is written, the UTest NVM sector is always protected and the UTest operations,
margin read and array integrity, are always protected.
Only blocks selected in the Test mode disable block select field are controlled by the Test mode disable feature. Therefore, it
is possible for customers to selectively pick blocks that have this type of protection and will not be eligible for factory failure
analysis. Bits programmed to 0 in the Test mode disable block select field(s) designate blocks that are controlled by the Test mode
disable feature.
In order to let two opportunities to select blocks for the Test mode disable, two regions are available, Test mode disable bock select
- 1 and Test mode disable block select - 2. If either Test mode disable block select - 1 or Test mode disable block select - 2 has a 0
programmed, that block will be protected. The bits of these two regions are logically ANDed to define the blocks that are effectively
selected for the Test mode disable feature. The bock select field is organized as shown in the next table:
Table 90. Test mode disable block select
Block Bits used to select blocks
Block 0 disable Data[0]
Block 1 disable Data[8]
Block 2 disable
Data[16]
Block 3 disable Data[24]
Block 4 disable
Data[32]
Block 5 disable Data[40]

#### 20.5 Functional description

The embedded flash memory module consists of blocks with the following options:
• 2 MB blocks (0, 1, 2, 3 or 4 blocks allowed per module)
• 1 MB blocks (0, 1, 2, 3 or 4 blocks allowed per module)
• 512 KB blocks (0, 1, 2 or 4 blocks allowed per module)
• 256 KB blocks (0, 1, 2 or 4 blocks allowed per module)
• A total of six blocks are allowed per module
Read while Write is allowed within a module, and is determined based on block boundaries. Read while Write partitions are used
to determine locations for valid read-while-write (RWW) operations. While the embedded flash memory performs a write (program
or erase) to a given partition, it can simultaneously perform a read from any other partition. For program and erase operations, only
the address specified by an interlock write determines the partition being written (sector or super sector locking does not determine
the RWW partitions being written).
The main address space is also divided into sectors and super sectors to implement independent erase and program protection.
The UTest NVM space also exists as a sector and has independent program protection. The UTest NVM sector is included to
support systems that require non-volatile memory (NVM) for security or to store system initialization information.
A number of MCR bits are protected against write when another field, or set of fields, is in a specific state. These write locks are
covered on a bit-by-bit basis in Module Configuration Register . The write locks do not consider the effects of trying to write two or
more bits simultaneously. This module does not allow fields to be written simultaneously that put the device into an illegal state.
This is implemented through a priority mechanism among the fields, and the next table shows this mechanism.

---

*Page 482*

Embedded Flash Memory (c40asf)
Table 91. MCR field set and clear priority levels
Priority level MCR field(s)
1 ERS
2 PGM
3 EHV
If two or more MCR fields are written simultaneously, only the field with the lowest priority number is accepted for modification.
For example, writing 1 to ERS and PGM simultaneously results in 1 being written only to ERS.
Each read of the embedded flash memory retrieves a page, or eight consecutive words (256 bits), of information. The address for
each word retrieved within a page differs from the other addresses in the page only by address bits [4:2]. The flash memory page
read architecture easily supports both cache and burst mode at the PFC BIU level for high-speed read applications.
The embedded flash memory supports fault tolerance through error correction code (ECC) and error detection. ECC implemented
within the embedded flash memory corrects single bit failures and detects double bit failures.
Program and erase of the embedded flash memory requires multiple system clock cycles to complete. Program and erase may
be aborted.
The embedded flash memory may operate in various modes as described in the following sections.

#### 20.5.1 User mode

In User mode, the embedded flash memory may be read and written (register writes and interlock writes), programmed or erased.
The following sub-sections define all actions that may be performed in User mode.
20.5.1.1 Read and write
The default state of the embedded flash memory is read. The main and UTest NVM address spaces can be read only in the read
state. The flash registers are always available for read, except when the embedded flash memory is in Low-Power mode. The
module enters the read state on reset, and remains in the read state under two sets of conditions:
• The read state is active when the embedded flash memory is enabled (User mode read).
• The read state is active when MCR[PGM] or MCR[ERS] are 1 and a high voltage operation is ongoing (RWW).
NOTE
Reads done to the partition(s) being operated on (either erased or programmed) result in MCRS[RWE] becoming 1.
In embedded flash memory, flash core reads return 256 bits (1 page). Register reads return 32 bits of data.
NOTE
Flash core reads are performed through the PFC BIU. In many cases, the BIU does read page buffering to allow
sequential reads to be done with higher performance. This could provide a data coherency issue that must be
handled with software. Data coherency may be an issue after a program or erase operation, as well as UTest NVM
sector operations.
In user mode, registers may be written (including interlock writes using registers).
Register reads to unmapped register address space return all 0s.
Register writes to unmapped register address space have no effect.
Interlock writes that are attempted during a high voltage operation (MCR[EHV] = 1 or MCRS[DONE] =0) result in the interlock write
being ignored, and address and data will not be updated.

---

*Page 483*

Embedded Flash Memory (c40asf)
20.5.1.2 Program
A flash memory program sequence operates on any page within the flash core. Within a page, up to eight words may be altered in a
single program operation. Also, up to four pages can be altered in a single program operation. Whenever the array is programmed,
the ECC bits also get programmed.
ECC is handled on a 64-bit boundary. Thus, if only one word in any given 64-bit ECC segment is programmed, the adjoining word
in that segment should not be programmed, because ECC calculation is already complete for that 64-bit segment. Attempts to
program the adjoining word may likely result in an operation failure. It is recommended that all programming operations ranger
from 64 bits to 1024 bits, and be 64-bit aligned. The programming operation should completely fill selected ECC segments within
the page. Only one program is allowed per 64-bit ECC segment between erases.
Caution
In rare cases, over-programming of a 64-bit ECC segment may be done (EEPROM emulation in data flash region).
In this case, approved EEPROM emulation drivers must be used, and they must limit the number of over-program
operations to three times (four total programs between erases).
Programming changes the value stored in an array bit from logic 1 to logic 0 only. Programming cannot change a stored logic 0
to a logic 1.
NOTE
If a logic 0 is attempted to be over-programmed by a logic 1, the resulting operation fails (MCRS[PEG] = 0), and the
0s that are interlocked are merged (ORed) with the 0s that are already present in the 64-bit ECC segment , unless
the block is designated as an over-orogram protected block .
Addresses in locked sectors cannot be programmed. Values may be programmed in any or all of eight words, within a page, with
a single program sequence. Up to four pages can be programmed at once on a quad-page boundary. The program operation
consists of the following sequence of events:
1. Write the address to be programmed using logical address registers located in the PFC. The result of this write will also
be reflected physically in the PEADR register.
NOTE
PEADR writes are initiated by writes that first occur in the PFC. Please see the platform flash memory controller
chapter for more information. PEADR reads are allowed, and represent the flash memory physical address.
NOTE
Ensure the sector that contains the address to be programmed is unlocked. Sector and super sector lock status is
latched at this step and does not change until the next program or erase sequence is started.
2. Data to be programmed must be written in the appropriate DATAX register (where X is 0 to 31). All unwritten data words
default to FFFF_FFFFh.
3. Change the value in MCR[PGM] from a 0 to a 1.
4. Write a 1 to MCR[EHV] to start the internal program sequence or skip to step 8 to terminate.
5. Wait until MCRS[DONE] becomes 1.
NOTE
Since MCRS[DONE] clears with MCR[EHV] being set, it may not be possible for software to read MCRS[DONE]
as 0 between step 4 and step 5, depending on the operation selected.
6. Confirm MCRS[PEG] = 1.
7. Write 0 to MCR[EHV].
8. Write 0 to MCR[PGM] to terminate the program sequence.

---

*Page 484*

Embedded Flash Memory (c40asf)
Program may be initiated with the writing of the PEADR register (enabled with a write done in the PFC block). The PEADR write
to initiate the program sequence determines the quad-page address to be programmed. Data must also be written to the DATA
register(s). This first write is referred to as an interlock write. Unwritten locations default to a data value of FFFF_FFFFh.
An interlock write must be performed before writing 1 to MCR[PGM], and MCR[PGM] must be set before writing 1 to MCR[EHV]
during a program sequence. A program sequence may be terminated by writing 0 to MCR[PGM] prior to writing 1 to MCR[EHV].
While MCR[EHV] is 1 and MCRS[DONE] is 0, MCR[EHV] may be cleared, resulting in a program abort . A program abort forces the
embedded flash memory to step 8 of the program sequence. An aborted program results in PEG becoming 0, indicating a failed
operation. The data space being operated on before the abort contains indeterminate data.
Caution
Aborting a program operation leaves the FC addresses being programmed in an indeterminate data state. This may
be recovered by executing an erase on the affected block.
20.5.1.2.1 Program hardware locking
Hardware locking which affects the program and erase operations is available for UTest sector and all array blocks.
20.5.1.2.2 Over-program protection enable
One-time programming can be enabled. If over-program protection is enabled, any double word that has already been
programmed cannot be programmed again. The over-program protection enable does not affect the erase operation. Attempts to
over-program result in MCRS[PEG] becoming 0. If any double word within a quad-page has an over-program protection violation,
MCRS[PEG] becomes 0, and no double words are programmed.
One-time programming can be enabled for the UTest NVM sector.
20.5.1.3 Erase
An erase changes the value stored in all bits of the selected sector or block to logic 1, and operates on a sector or a block in the
main address space. The erase sequence is fully automated within the flash memory. Locked sectors cannot be erased. The erase
sequence consists of the following events:
1. Write to the sector or block address to be erased using logical address registers located in the PFC. The result of this
write is reflected physically in the PEADR register. One, and only one, DATA register must also be written. This is
referred to as an erase interlock write .
NOTE
PEADR writes are initiated by writes that first occur in the PFC. Please see the platform memory flash controller
chapter for more information. PEADR reads are allowed, and represent the flash memory physical address.
NOTE
The selected block or sector must be unlocked prior to initiating an erase with the appropriate sector or super sector
lock registers. Sector and super sector lock status is latched at this step and does not change until the next program
or erase sequence starts. If a block is selected for erase, all the sectors and super sectors must be unlocked within
that block.
2. Change the value in MCR[ERS] from a 0 to a 1, and at the same time select the size of erase using MCR[ESS].
3. Write a 1 to MCR[EHV] to start the internal erase sequence or skip to step 6 to terminate.
4. Wait until MCRS[DONE] becomes 1.
NOTE
Since MCRS[DONE] clears with MCR[EHV] being set, it may not be possible for software to read MCRS[DONE]
as 0 between step 3 and step 4, depending on the operation selected.
5. Confirm MCRS[PEG] = 1.

---

*Page 485*

Embedded Flash Memory (c40asf)
6. Write 0 to MCR[EHV].
7. Write 0 to MCR[ERS] to terminate the erase, and if set MCR[ESS] should also be cleared (else the field auto clears).
Erase may be initiated with the writing of the PEADR register (enabled with a write done in the Platform Flash Controller block).
The PEADR write to initiate the erase sequence determines the sector or block to be erased. One write must also occur to a DATA
register. Data word written during an erase sequence interlock write is ignored. This write is referred to as an erase interlock write.
An erase interlock write must occur before writing 1 to MCR[ERS] (and optionally MCR[ESS]), and MCR[ERS] must be set before
writing 1 to MCR[EHV] during an erase sequence. The erase sequence may be terminated by writing 0 to MCR[ERS] prior to
writing 1 to MCR[EHV].
While MCR[EHV] is 1 and MCRS[DONE] is 0, MCR[EHV] may be cleared, resulting in an erase abort. An erase abort forces
the embedded flash memory to step 7 of the erase sequence. An aborted erase results in PEG becoming 0, indicating a failed
operation. The sector or block being operated on before the abort contain indeterminate data.
20.5.1.3.1 Erase hardware locking
Hardware locking which affects the erase and program operations is available for UTest sector and all array blocks. For details,
see Program hardware locking .
20.5.1.4 Express program
A mechanism is made available to provide an express method to program up to 1024 bits of flash memory. When activated,
all ongoing program and erase operations through the register interface are automatically aborted (MCR[EHV] becomes 0
automatically) and the express interface is given priority. When using the express interface, sequence requirements are ignored,
although a XPEADR write is required to start the express program sequence.
The flash express program mechanism provides a quicker way to get information programmed into a block of flash memory when
an application needs an immediate diary access, or a crash log access.
Addresses in locked sectors cannot be express programmed.
The express program operation consists of the following sequence of events:
1. Write the address to be programmed using the logical address register located in the PFC. The result of this write will
also be reflected physically in the XPEADR register.
NOTE
XPEADR writes are initiated by writes that first occur to the PFC. Please see the platform flash controller chapter
for more information. XPEADR reads are allowed, and represent the flash memory physical address.
NOTE
Ensure the sector that contains the address to be programmed is unlocked. Sector and super sector lock status is
latched at this step and does not change until the next program, erase or express program is started.
NOTE
If XMCR[XPGM] interrupts a program or erase setup after MCR[EHV] is written to 1, the MCR and MCRS registers
indicate if the operation was automatically aborted (MCR[EHV] = 0, MCRS[PEG] = 0, MCR[PGM] =1 or MCR[ERS]
= 1), or if the operation was successful (MCR[EHV] = 1, MCRS[PEG] = 1, MCR[PGM] =1 or MCR[ERS] = 1). If
express program interrupts a program or erase setup prior to MCR[EHV] being written to a 1, the MCR and PEADR
registers will be preserved to show the state of the setup upon interruption. If fields MCR[PGM] = 1 or MCR[ERS] = 1
at interruption, the user must terminate the event by writing 0 to these fields. However, if MCR[PGM] or MCR[ERS]
were not 1 at interruption, PEADR is preserved and does not need to be rewritten, but the PDATA registers are
required to be rewritten for the interrupted operation. After the PDATA write(s) are repeated to finish the interlock
write, the remainder of the program and erase sequence can be done to restart the interrupted operation (including
doing a terminate after PGM or ERS are written to 1, if desired).

---

*Page 486*

Embedded Flash Memory (c40asf)
NOTE
Before starting the express program, ensure that UTest operations (array integrity or user margin read) are not
ongoing (or about to be started). UT0[UTE] must be low before requesting the express program, and remain low
during express program operations. Hardware locks are in place to ensure this occurs.
2. Wait until XMCR[XDOK] goes high.
3. Write the DATA registers with the data desired to be programmed. Up to 1024b (128B) may be programmed with
express program. Only DATA registers that are written will receive program pulses.
4. Change the value in XMCR[XPGM] from a 0 to a 1.
5. Write a 1 to XMCR[XEHV] to start the internal express program sequence.
6. Wait until XMCR[XDONE] becomes 1.
7. Confirm XMCR[XPEG] = 1.
8. Write 0 to XMCR[XEHV].
9. Write 0 to XMCR[XPGM] to terminate the express program sequence.

#### 20.5.2 Low-Power mode

In Low-Power mode, the embedded flash memory is put into a state where VDDF power gating and VFLASH power gating is
allowed. It is the lowest current mode for the flash memory. No reads from or writes to the embedded flash memory are possible
when in this mode. Most power dissipation is due to leakage in this mode.
Prior to entering Low-Power mode, it is required that all program, erase, and UTest operations be stopped.
When in Low-Power mode, register access is prevented. Flash core accesses are also prevented until power mode is exited. Flash
core reads and writes may occur after power mode is exited.
The embedded flash memory returns to a post-reset state in all cases.

#### 20.5.3 Program and erase watchdog timer

The embedded flash memory contains an internal watchdog timer that is used to prevent denial of service issues and events.
The watchdog timer is active during two periods of a program and erase event: program/erase setup and program/erase cleanup.
If it is desired that program and erase operations also be checked with a watchdog timer, a system resource may be used for
this purpose.
Program/erase setup always starts at the time of the PEADR write. An express program setup starts with a write to XPEADR and
XMCR[XDOK] = 1. In the event of an Express program interrupt of main or alternate interface setup, the timer starts at the time
when the express program operation completes (write of XMCR[XPGM] = 0). The watchdog stops when either MCR[EHV] is set
to start the operation, or when the operation is terminated. Termination can occur when MCR[PGM] or MCR[ERS] are written
to 0 (prior to MCR[EHV] being written to 1), or MCRS[PES] written to 0. If the express program interrupts the program/erase
setup, this also stops the timer on that interface, and express program maintains priority. The above statements also apply to the
alternate interface.
NOTE
If PEADR writes occur while UT0[UTE] is 1, the watchdog feature does not activate for setup. Watchdog timer is
not active during UTest operations.
Program/erase cleanup always starts at the time of MCRS[DONE] = 1 on completion or an abort. In the event of an express
program auto-abort of main or alternate interface, the cleanup starts at the time of MCRS[DONE] becoming 1 and the express
program operation completed (XMCR[XPGM] written to 0). The watchdog stops when MCR[PGM] or MCR[ERS] are written to 0
at the end of the operation. If express program interrupts the program/erase cleanup, the timer on that interface also stops. The
above statements also apply to the alternate interface.
Watchdog timers exist for each interface (main, alternate and express).

---

*Page 487*

Embedded Flash Memory (c40asf)
If a watchdog timeout occurs, the watchdog interrupt will be reflected in MCRS[WDI], AMCRS[AWDI] or XMCR[XWDI]. Interrupts
can be enabled on the main and alternate interfaces using MCR[WDIE] or AMCR[AWDIE]. When a watchdog timeout occurs
and an interrupt register asserts, the PEID restrictions on that interface are released. At that point, any master can complete the
operation, and PEID locking is no longer enforced. If the watchdog timeout occurs, the EHV bit on that interface (MCR[EHV],
AMCR[AEHV], and XMCR[XEHV]) are locked for writing to a 1. In this instance, any master can take the interface to a program
and erase terminate. This enables accepted MCRS[WDI], AMCRS[AWDI] or XMCR[XWDI] to become 0, and the operation may
restart with a new interlock write.
NOTE
If the watchdog timeout occurs before PGM becomes 1 (on any interface), or ERS becomes 1 (on any interface),
the sequence must be taken to the point that the PGM or ERS bit for that interface is written and then the field for
that interface can be cleared to create the terminate event and to clear the watchdog interrupt.
By default, the watchdog is enabled. Through the alternate MCR, the watchdog may be disabled (AMCR[WDD]), and the
watchdog timeouts maybe adjusted (AMCR[WDT]) for all timeouts and all interfaces.

#### 20.5.4 UTest mode

UTest mode enables customers to do specific tests that check the integrity of the embedded flash memory.
NOTE
When entering UTest mode, a best practice is to ensure all error flags and address error reporting in ADR are
cleared to their reset value to ensure robust software and procedure execution.
NOTE
UTest mode diagnostic features ECC Logic Check, EDC after ECC Logic Check, Address Encode Logic Check,
and Read Reference and Voltage Check, require serialization of flash reads starting with mode setup and ending
with mode exit. This may be accomplished by executing diagnostic code from system RAM (with single reads to
flash tagged address) as well as ensuring any flash reads outside of this diagnostic code are idle (single core).
20.5.4.1 Array integrity self check
Array integrity is checked using a predefined address sequence (based on UT0[AIS]), and this operation is executed on
selected blocks.
The data to be read is customer-specific user code programmed into the flash memory and the correct MISR signature is
calculated based on that code.
Any random or nonrandom code is valid. After the operation completes, the results of the reads can be checked by reading the
MISR value, to determine if an incorrect read or ECC detection was noted. Array integrity MISR value is calculated after ECC
detection and correction. Array integrity requires that the read wait states control registers in the CTL register be set to match the
system frequency being used.
The array integrity self check consists of the following steps:
1. Enable UTest mode.
2. Select the block to receive the array integrity check by performing an interlock write to that block. Write logical address
register in the PFC (which will be reflected in the PEADR register) and 1 DATA register (PDATA is ignored). The block
selected for array integrity check does not need to be unlocked.
NOTE
Blocks protected with the Test mode disable seal are still read as part of the array integrity sequence. The resulting
read on sealed blocks is not captured in the MISR, but single bit correction, double bit detection and breakpoints
are still honored.

---

*Page 488*

Embedded Flash Memory (c40asf)
NOTE
It is not possible to perform array integrity operations on the UTest NVM sector .
3. If desired, write 1 to UT0[AIS] for sequential addressing only.
NOTE
For normal integrity checks of the flash memory, sequential addressing is recommended. If it is required to more
thoroughly check the read path (in a diagnostic mode), AIS shall remain 0 to examine more read transitions. This
sequence takes more time.
4. Seed the MISR registers (UM0 - UM9) with desired values.
5. If breakpoints are desired, write 1 to UT0[AIBPE], and ensure that MCRS[EER] and MCRS[SBC] are 0. If it is desired to
break on a single bit correction, ensure that UT0[SBCE] = 1.
6. Write 1 to UT0[AIE].
a. If desired, the array integrity operation may be aborted before UT0[AID] goes high. This may be done by writing
0 to UT0[AIE] and then continuing to the next step. Note that in the event of an aborted array integrity check,
MISRs contain a signature for the portion of the operation that was completed prior to the abort and are not
deterministic. Before performing another array integrity operation, the UM0, UM1, UM2, UM3, UM4, UM5, UM6,
UM7, UM8, and UM9 registers may need to be initialized to the desired seed value by doing register writes.
b. If desired, the array integrity operation may be suspended before UT0[AID] goes high. This may be done by
writing 1 to UT0[AISUS] to request an array integrity suspend. After UT0[AISUS] becomes 1, the user should
wait for UT0[AID] to become 1, which indicates that the flash memory has entered the suspend state, and normal
reads to the flash memory may be done. After UT0[AID] becomes 1, UT0[AISUS] may be written to 0 to resume
the array integrity sequence.
NOTE
User mode array reads requested during the array integrity test are ignored to ensure that the array integrity
operation is not corrupted. The memory array does not respond to array read requests during this time. User mode
array reads may be executed if suspended or at a breakpoint.
7. Wait until UT0[AID] becomes 1.
8. If breakpoints are enabled, check if UT0[NAIBP] = 1. If the value of this field is 1, MCRS[EER], MCRS[SBC], and ADDR
may be checked to determine the cause of the break and the address of the break. Prior to resuming the operation,
write 1 to clear MCRS[SBC] or MCRS[EER]. Then, the operation may be resumed by writing 0 to UT0[NAIBP]. Continue
to wait until UT0[AID] becomes 1. If breakpoints are not enabled, or if UT0[NAIBP] = 0 when UT0[AID] becomes 1, the
operation is complete. Continue to the next step.
9. Read values in MISRs (UM0 - UM9) to ensure correct signature.
NOTE
Array integrity reads may be done to unselected (or non-present) locations. Reads done to these locations do not
update the MISRs, MCRS[EER] and MCRS[SBC].
10. Write 0 to UT0[AIE].
20.5.4.2 User margin read
User margin read may be done using the array integrity interface, and has all the associated features of the array integrity interface
(MISR and breakpoints).
User margin reads are done at a read margin level, checking for erased bits or programmed bits encroaching on the nominal
read level.
User margin read requires that the Read Wait States control registers in the CTL register be written to match the system frequency
being used. Margin ECC corrections and detections are noted during the user margin read test. Margin read MISR value is
calculated after ECC detection and correction.

---

*Page 489*

Embedded Flash Memory (c40asf)
The data to be read is customer-specific user code programmed into the flash memory.
Any random or non-random code is valid. After the operation completes, the margin read results can be checked by reading
MCRS[EER] and the MCRS[SBC] to determine if zero, one, or two bits are being detected by the margin read, as well as checking
the MISR signature.
The use model for margin read is in the event of a user-detected single bit correction (through user reads), a margin read may be
done to check for a possible second bit falling within the selected margin levels.
The user margin read consists of the following steps:
1. Enable UTest mode.
2. Select the block to receive the array integrity check by performing an interlock write to that block. Write logical address
register in the PFC (which will be reflected in the PEADR register) and 1 DATA register (PDATA is ignored). The block
selected for user margin read check does not need to be unlocked.
NOTE
Blocks protected with the Test mode disable seal are still read as part of the margin read sequence. The resulting
read on sealed blocks is not captured in the MISR, but single bit correction, double bit detection and breakpoints
are still honored.
NOTE
It is not possible to perform margin read operations on the UTest NVM sector.
3. Write 1 to UT0[AIS] for sequential addressing only.
NOTE
For margin read checks of the flash memory, sequential addressing is recommended. Writing 0 to UT0[AIS] is
possible for margin reads, but using the sequence takes more time, and is not recommended.
4. Seed the MISR registers (UM0 - UM9) with desired values.
5. Ensure that MCRS[EER] and MCRS[SBC] are 0.
6. To enable single bit correction reporting during margin read, write UT0[SBCE] = 1.
7. Write 1 to UT0[MRE].
8. Write UT0[MRV] to the desired value depending on if it is desired to do one's margin or zero's margin.
9. Write 1 to UT0[AIE].
NOTE
User mode array reads requested during the margin read test are ignored to ensure that the margin read operation
is not corrupted. The memory array does not respond to array read requests during this time. User mode array
reads may be executed if suspended or at a breakpoint.
NOTE
During margin read operations, with UT0[AID] = 0, it is not recommended to attempt write operations to
MCRS[SBC] and MCRS[EER]. It is recommended that these fields only be written during suspend, breakpoints or
at the completion of the margin read operation.
10. Wait until UT0[AID] becomes 1.
11. If breakpoints are enabled or a suspend was requested during margin read, the operation may be at a breakpoint or a
suspend state. See Array integrity self check for more information.
12. Read values in the MISRs (UM0 - UM9) to ensure correct signature.

---

*Page 490*

Embedded Flash Memory (c40asf)
NOTE
Margin reads may be done to unselected (or non-present) locations. Reads done to these locations do not update
the MISRs, MCRS[EER] and MCRS[SBC].
13. Write 0 to UT0[AIE].
20.5.4.3 ECC logic check
ECC logic can be checked by providing data to be read in UD0[EDATA], UD1[EDATA] and/or UD2[EDATAC]. Array reads can
then be performed, ensuring expected results.
The ECC logic check consists of the following steps:
1. Enable UTest mode.
2. Write 1 to UT0[EIE].
3. Write to UD0[EDATA], UD1[EDATA] and/or UD2[EDATAC] to provide data and check bit values to be read. Single bit
corrections or double bit detections can be simulated by properly selecting data and check bit combinations.
4. Write the page address to receive data provided in step 3 into ADR.
5. Write to UD2[ED3], UD2[ED2], UD2[ED1] and/or UD2[ED0] to select the double words on the page to receive the
check.
6. Reads can now be done through the BIU using an array read request. In the event of a BIU read requested from
an address that matches the address in ADR, expected data and corrections or detections are observed based on
data written into UD0[EDATA], UD1[EDATA] and/or UD2[EDATAC]. MCRS[EER] and MCRS[SBC] can be checked to
evaluate the status of reads done.
NOTE
In the event of an ECC error or single bit correction, during the ECC logic check (UT0[EIE] = 1), ADR is not loaded,
and the address tagged to receive the ECC logic check values is preserved.
7. Once completed, write UT0[EIE] to 0.
20.5.4.4 EDC after ECC logic check
EDC after ECC logic can be checked by providing data to be read in UD3[EDDATA], UD4[EDDATA] and/or UD5[EDDATAC].
Array reads can then be performed, ensuring expected results.
The EDC after ECC logic check consists of the following steps:
1. Enable UTest mode.
2. Write 1 to UT0[EDIE].
3. Write to UD3[EDDATA], UD4[EDDATA] and/or UD5[EDDATAC] to provide data and check bit values to be read.
4. Write the page address to receive data provided in step 3 into ADR.
5. Write to UD5[EDD3], UD5[EDD2], UD5[EDD1] and/or UD5[EDD0] to select the double words on the page to receive the
check.
6. Reads can now be done through the BIU using an array read request. In the event of a BIU read requested from an
address that matches the address in ADR, expected EDC after ECC errors are observed based on data written into
UD3[EDDATA], UD4[EDDATA] and/or UD5[EDDATAC]. MCRS[EEE] can be checked to evaluate the status of reads
done.
7. Once completed, write UT0[EDIE] to 0.

---

*Page 491*

Embedded Flash Memory (c40asf)
20.5.4.5 Address encode logic check
Address encode logic can be checked by inverting the address encode information from the memory array in UA0[AEI] and
UA1[AEI]. Array reads can then be performed, ensuring expected results.
The Address encode check consists of the following steps:
1. Enable UTest mode.
2. Write 1 to UT0[AEIE].
3. Write to UA0[AEI] and/or UA1[AEI] to provide address bit(s) to be inverted.
4. Write the page address to receive inverted addresses provided in step 3 into ADR.
5. Reads can now be done through the BIU using an array read request. In the event of a BIU read requested from an
address that matches the address in ADR, expected address encode errors are observed based on address invert
values written into UA0[AEI] and/or UA1[AEI]. MCRS[AEE] can be checked to evaluate the status of reads done.
6. Once completed, write UT0[AEIE] to 0.
20.5.4.6 Read reference and voltage check
Read reference and voltage detection logic can be checked by writing 1 to UT0[RRIE]. Array reads can be then be performed,
ensuring expected results.
The read reference and voltage check consists of the following steps:
1. Enable UTest mode.
2. Write 1 to UT0[RRIE].
3. Write the page address to receive a read reference error MCRS[RRE] and a read voltage error MCRS[RVE] into ADR.
4. Reads can now be done through the BIU using an array read request. In the event of a BIU read requested from an
address that matches the address in ADR, expected read reference and read voltage errors are observed based on
ADR registers. MCRS[RRE] and MCRS[RVE] can be checked to evaluate the status of reads done.
5. Once completed, write UT0[RRIE] to 0.

#### 20.5.5 Data flash memory requirements for EEPROM emulation

The embedded flash memory may be used to emulate an EEPROM utilizing software drivers, strategic over-programming of
double words in the flash memory and following the below requirement and best practices in EEPROM software drivers.
It is required that the EEPROM emulation driver must build in fault tolerance allowing for the ability to “skip” records. In the case
of an unsuccessful program, the ability to retry programming in the next available record location is required (record retirement).
In the case of an unsuccessful sector erase, the ability to retire a sector is required (sector retirement).
In addition, following are best practices for the EEPROM emulation software driver to be considered:
• Choose a record scheme which allows for grouping of EEPROM data contents that are updated at one time such that
unchanged data is not needlessly copied while minimizing record qualifier and status overhead. A variable length record
scheme may be used to allow for grouping of data that is written together and limiting total record overhead.
• Copy only the valid records during a sector change and re-constitution of EEPROM data set to avoid needless copying of data
within flash memory. This avoids rebuilding the entire data set.
• Only redundantly storing critical data which cannot be recovered in two sectors if attempting to protect against record or
sector failures.
• Load level total program/erase cycles applied to flash memory locations allocated for EEPROM emulation by using a
round-robin sector scheme.
When combining round-robin with sector retirement requirement, three or more sectors must be allocated for EEPROM emulation
inclusive of extra spare sector(s).

---

*Page 492*

Embedded Flash Memory (c40asf)
For details, see NXP application notes published on this topic.

#### 20.6 Initialization information

A reset is the highest priority operation for the embedded flash memory and terminates all other operations.
The embedded flash memory uses reset to initialize register and status fields to their default reset values. If the embedded flash
memory is executing a program or erase operation (MCR[PGM] = 1 or MCR[ERS] = 1) and a reset is issued, the operation aborts
and the embedded flash memory disables the high voltage logic without causing any damage to the high voltage circuits. Reset
aborts all operations and forces the embedded flash memory into User mode, ready to receive accesses.
After reset is requested, MCRS[DONE] becomes 0, and remains low during reset and reset recovery.
At the end of reset recovery, MCRS[DONE] transitions from 0 to 1.
After a reset completes, register reads may be performed.
NOTE
Registers that require updating from UTest NVM information, or other inputs, may not read updated values until
MCRS[DONE] becomes 1.
During reset recovery, register writes are not allowed until MCRS[DONE] becomes 1 to indicate reset recovery is complete.
Caution
Resetting during a program or erase operation leaves the FC blocks being programmed or erased in an
indeterminate data state. This may be recovered by executing an erase on the affected blocks.

#### 20.7 Memory map and register description

#### 20.7.1 c40asf flash memory register descriptions

The embedded flash memory map consists of a flash memory array (which includes main array space and UTest NVM space)
and a region of registers associated with the programming model that enable flash memory array operation and modification.
The address space consists of up to 6 blocks (with restrictions), and blocks can be 2 MB, 1 MB, 512 KB or 256KB in size.
20.7.1.1 c40asf_flash memory map
FLASH base address: 402E_C000h
Offset Register Access Reset value
Width
(In bits)
0h Module Configuration (MCR) 32 RW 00FF_0000h
4h Module Configuration Status (MCRS) 32 RW 0000_C100h
8h Extended Module Configuration (MCRE) 32 R 0000_0000h
Ch Module Control (CTL) 32 RW 0000_0600h
10h Address (ADR) 32 RW 0000_0000h
14h Program and Erase Address (PEADR) 32 R 0000_0000h
50h Sector Program and Erase Hardware Lock (SPELOCK) 32 R FFFF_FFFFh
54h Super Sector Program and Erase Hardware Lock (SSPELOCK) 32 R 0000_0FFFh
Table continues on the next page...

---

*Page 493*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
70h Express Sector Program and Erase Hardware Lock (XSPELOCK) 32 R FFFF_FFFFh
74h Express Super Sector Program and Erase Hardware Lock 32 R 0000_0FFFh
(XSSPELOCK)
90h Test Mode Disable Password Check (TMD) 32 RW 0000_0000h
94h UTest 0 (UT0) 32 RW 0000_0001h
98h - B8h UMISRn (UM0 - UM8) 32 RW 0000_0000h
BCh UMISR9 (UM9) 32 RW 0000_0000h
D0h UTest Data 0 (UD0) 32 RW 0000_0000h
D4h UTest Data 1 (UD1) 32 RW 0000_0000h
D8h UTest Data 2 (UD2) 32 RW 0000_0000h
DCh UTest Data 3 (UD3) 32 RW 0000_0000h
E0h UTest Data 4 (UD4) 32 RW 0000_0000h
E4h UTest Data 5 (UD5) 32 RW 0000_0000h
E8h UTest Address 0 (UA0) 32 RW 0000_0000h
ECh UTest Address 1 (UA1) 32 RW 0000_0000h
F0h Express Module Configuration (XMCR) 32 RW 00FF_C000h
F4h Express Program Address (XPEADR) 32 R 0000_0000h
100h - 17Ch Program Data (DATA0 - DATA31) 32 RW FFFF_FFFFh
20.7.1.2 Module Configuration (MCR)
Offset
Register Offset
MCR 0h
Function
NOTE
• A number of Module Configuration Register (MCR) bits are locked against write by other bits. These locks are
discussed in relationship to each bit in this section. Simultaneously writing bits which lock each other out is
also discussed in Functional description .
• See Functional description for information about simultaneous MCR writes, and priority levels.

---

*Page 494*

Embedded Flash Memory (c40asf)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 PEID
W
Reset 0 0 0 0 0 0 0 0 1 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0
PECIE WDIE PGM ESS ERS EHV
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-24
—
23-16 Program and Erase Master/Domain ID
PEID This field shows the ID of the master that has started the Program or Erase sequence (as well as Array
Integrity and User Margin Reads). The ID is latched when the sequence has started (writing of the PEADR
register). Upon completion of an operation (Program (MCR[PGM] written to 0 and MCRS[PES] equals 0),
Erase (MCR[ERS] written to 0 and MCRS[PES] equals 0), Array Integrity (UT0[AIE] written to 0), User
Margin Read(UT0[AIE] written to 0)), or Program and Erase Sequence clear (MCRS[PES] cleared to 0) the
PEID field will return to an all ones state. See the chip-specific information for a list of Master IDs.
15 Program/Erase Complete Interrupt Enable
PECIE PECIE provides a mechanism to trigger an interrupt request upon the assertion of the DONE status due to a
high voltage event (program or erase) finishing (normal, abort). If PECIE is written while not in a high voltage
event, the interrupt will not immediately trigger, but will trigger after the next high voltage event is completed.
Writing (and reading) of this bit will be restricted to the master that updated the PEADR register as captured
in the PEID register at the start of the Program or Erase sequence.
NOTE
See the chip-specific information for interrupt details
0b - Interrupt request not generated when MCRS[DONE] is 1
1b - Interrupt request generated when MCRS[DONE] is 1
Reserved
14-13
—
12 Watch Dog Interrupt Enable
WDIE WDIE provides a mechanism to trigger an interrupt request upon the assertion of the WDI bit on the main
interface. If WDIE is asserted, when WDI asserts, an interrupt from the flash will trigger to the system. The
interrupt from the flash will mirror exactly the WDI bit. WDIE does not affect the register bit WDI. Writing (and
Table continues on the next page...

---

*Page 495*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
reading) of this bit will be restricted to the master that updated the PEADR register as captured in the PEID
register at the start of the Program or Erase sequence.
NOTE
See the chip-specific information for interrupt details
0b - Watchdog interrupt not enabled
1b - Watchdog interrupt enabled
Reserved
11-9
—
8 Program
PGM PGM is used as part of the setup for a program operation. A 0 to 1 transition of PGM after program interlock
write(s) is part of the program sequence. A 1 to 0 transition of PGM ends the program sequence. PGM can
be set while in user mode read (ERS is low and UTE is low), and after the interlock write is completed. PES
must also be low to enable PGM to be set.
PGM can be cleared only when EHV is low and DONE is high. PGM is cleared on reset. Writing (and
reading) of this bit will be restricted to the master that updated the PEADR register as captured in the PEID
register at the start of the Program or Erase sequence.
0b - Flash memory not executing a program sequence
1b - Flash memory executing a program sequence
Reserved
7-6
—
5 Erase Size Select
ESS ESS is used to qualify the embedded flash memory erase operation for either sector erase or block erase.
If ESS is selected for block erase, only the main array of that block will be erased.
NOTE
Block erase is for Factory use only, under controlled conditions. See Appendix A
(Electrical Specifications) for more information on environmental condition requirements,
and cycle limit.
NOTE
If interlock write occurs to UTest sector, ESS will be locked from setting to a 1. Block Erase
of UTest sector is not supported, only sector erase is allowed. Erase of UTest space is not
allowed if sealed, although ERS can be set and a PEG error will occur in response to the
HV request.
ESS can only be written to a 1 at the same time that ERS is written to a 1. If ESS is set, ESS will auto clear
with ERS clearing (independent of the value written to ESS). ESS can not be cleared by writing a zero. ESS
is cleared on reset. Writing (and reading) of this bit will be restricted to the master that updated the PEADR
register as captured in the PEID register at the start of the Program or Erase sequence.
Table continues on the next page...

---

*Page 496*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
0b - Flash memory erase is on a sector
1b - Flash memory erase is on a block
4 Erase
ERS ERS is used as part of the setup for an erase operation. A 0 to 1 transition of ERS after an erase interlock
write is part of the erase sequence. A 1 to 0 transition of ERS ends the erase sequence. ERS can only be
set in user mode read (PGM is low and UTE is low), and after the interlock write is completed. PES must
also be low to enable ERS to be set.
ERS can be cleared only when EHV is low and DONE is high. ERS is cleared on reset. Writing (and reading)
of this bit will be restricted to the master that updated the PEADR register as captured in the PEID register
at the start of the Program or Erase sequence.
0b - Flash memory not executing an erase sequence
1b - Flash memory executing an erase sequence
Reserved
3-1
—
0 Enable High Voltage
EHV The EHV bit enables the embedded flash memory for a high voltage program/erase operation. EHV is
cleared on reset. EHV must be set after the PGM or ERS bit is set in a program or erase sequence. Writing
(and reading) of this bit will be restricted to the master that updated the PEADR register as captured in the
PEID register at the start of the Program or Erase sequence. EHV may be set, initiating a program/erase
under one of the following conditions:
• Erase (ERS = 1, PEP = 0, PES = 0, WDI = 0)
• Program (PGM = 1, PEP = 0, PES = 0, WDI = 0)
Clearing EHV while DONE is low will abort the current program/erase high voltage operation. An abort
causes the value of PEG to be cleared, indicating a failed program/erase; address locations being operated
on by the aborted operation contain indeterminate data after an abort. EHV may not be written to a 1 after
an abort is requested (EHV being cleared) and before DONE transitions high. EHV may not be written to a
1 after it is cleared as part of a program or erase cleanup, until after the next PEADR write.
NOTE
Aborting a high voltage operation leaves addresses in an indeterminate data state. This may
be recovered by executing an erase on the affected sectors.
0b - Flash memory is not enabled to perform a high voltage operation.
1b - Flash memory is enabled to perform a high voltage operation.

---

*Page 497*

Embedded Flash Memory (c40asf)
20.7.1.3 Module Configuration Status (MCRS)
Offset
Register Offset
MCRS 4h
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R EER SBC AEE EEE 0 RVE RRE 0 RWE 0 PEP PES
W W1C W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
TSPEL
R DONE PEG 0 WDI 0 EPEG 0 RE
OCK
W
Reset 1 1 0 0 0 0 0 1 0 0 0 0 0 0 0 0
Fields
Field Function
31 ECC Event Error
EER This bit provides information on previous reads (either user initiated reads or internally initiated reset
reads).If a double bit detection occurred, the EER bit is set to a '1'. This bit must then be cleared, or a reset
must occur before it returns to a 0 state. This bit may not be set by software. In the event of a single bit
detection and correction, this bit is not set. If EER is not set, or remains 0, this indicates that all previous
reads (from the last reset, or clearing of EER) are correct. Since this bit is an error flag, it must be cleared
to a 0 by writing a 1 to the register location. A write of 0 has no effect.
0b - Reads occurring normally
1b - ECC error occurred during a previous read
30 Single Bit Correction
SBC SBC provides information on previous reads, if the SBCE is set. If a single bit correction occurred, the SBC
bit is set to a 1. This bit must then be cleared, or a reset must occur before this bit returns to a 0 state. If
SBC is not set, or remains 0, this indicates that all previous reads (from the last reset, or clearing of SBC) did
not require a correction. Since this bit is a status flag, it must be cleared to a 0 by writing a 1 to the register
location. A write of 0 has no effect.
0b - Reads occurring without corrections
1b - Single bit correction occurred during a previous read
29 Address Encode Error
AEE
Table continues on the next page...

---

*Page 498*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
AEE provides information on previous reads monitoring the address encode feature. On every read request
to the flash, the incoming address is compared to an encoded address (row, column, and block) coming
back from the memory array using the read data sense amplifier timing. If these two values do not match
(zero selected, multiple selected, wrong selected), or the timing is incorrect, an address encode error will be
recorded. If an address encode mismatch is detected, this bit is set to indicate that previous reads requested
may have been corrupted. Since this bit is a status flag, it must be cleared to a 0 by writing a 1 to the register
location. A write of 0 has no effect.
0b - Reads are occurring without address encode mismatches
1b - Previous read may be corrupted based on address encode mismatch
28 EDC after ECC Error
EEE EEE provides information on previous reads monitoring the EDC after ECC feature. On every read request
to the flash, ECC is recalculated serially, and if there is a mismatch between the ECC calculations (taking
into account corrections or detections) a late error will be reported. If an EDC after ECC mismatch is
detected, this bit is set to indicate that previous reads requested may have been corrupted. Since this bit is
a status flag, it must be cleared to a 0 by writing a 1 to the register location. A write of 0 has no effect.
0b - Reads are occurring without EDC after ECC mismatches
1b - Previous read may be corrupted based on ECC calculation errors
Reserved
27-26
—
25 Read Voltage Error
RVE RVE provides information on previous reads monitoring the read voltage. If the read voltage is detected to
be out of range, this bit is set to indicate that previous reads requested may have been corrupted. Since this
bit is a status flag, it must be cleared to a 0 by writing a 1 to the register location. A write of 0 has no effect.
0b - Reads are occurring without voltage issues
1b - A previous read may have been corrupted due to read voltage being out of range
24 Read Reference Error
RRE RRE provides information on previous reads monitoring the read reference. If the read reference is detected
to be out of range, this bit is set to indicate that previous reads requested may have been corrupted. Since
this bit is a status flag, it must be cleared to a 0 by writing a 1 to the register location. A write of 0 has no effect.
0b - Reads occur without reference issues
1b - Previous read may be corrupted because of read reference being out of range
Reserved
23-21
—
20 Read-While-Write Event Error
RWE This bit provides information on previous read-while-write (RWW) reads. If an RWW error occurs, this bit is
set to 1. The bit must then be cleared, or a reset must occur before it returns to a 0 state. If RWE is not set, or
Table continues on the next page...

---

*Page 499*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
remains 0, this indicates that all previous RWW reads (from the last reset, or clearing of RWE) are correct.
Since this bit is an error flag, it must be cleared to a 0 by writing a 1 to the register location. A write of 0 has
no effect.
0b - Reads occur normally
1b - RWW error occurred during a previous read
Reserved
19-18
—
17 Program and Erase Protection Error
PEP PEP provides information about program and erase operations with respect to protection errors. A
protection error occurs if a program is attempted to a locked sector or super sector, or if an erase is selected
to a locked sector or super sector. This is evaluated prior to the operation beginning, and if an error is
detected, high voltage operations (either a Program or Erase) will not be attempted for this request.
NOTE
If a location has both OTP and Lock protection, the response from the NVM will be
PEP=1 only.
If PEP is asserted, it must be cleared prior to attempting another high voltage operation. Since this bit is a
status flag, it must be cleared to a 0 by writing a 1 to the register location. A write of 0 has no effect.
0b - Program and erase protection errors do not exist
1b - Previous program or erase protection error encountered
16 Program and Erase Sequence Error
PES PES provides information about program and erase operations with respect to sequence errors. A sequence
error occurs when the program or erase sequence is not followed exactly. If an “out-of-order” event is
detected, PES will assert, and the remainder of the sequence will not be accepted. PES monitoring begins
when the PEADR register is written and ends when EHV is set to start the operation. PES monitoring
only applies to Program and Erase (Sector and Block). It does not apply to Express Program, or UTest
operations. Following is a complete list of sequence error conditions:
• Any specific DATA register written twice.
• More than one DATA register write for ERS operations.
• Attempts to write PGM or ERS register out of sequence.
• Attempts to write PGM after ERS, or ERS after PGM.
• Attempts to write DATA registers after PGM or ERS are written.
• Attempts to write EHV out of sequence.
Writes to the MCR that are protected by the MCR Priority Levels (i.e. simultaneous PGM, ERS and EHV
writes) will not result in a PES condition.
Attempts to write MCR or DATA registers by a master that does not match the PEID, will not result in a PES
condition. Those reads and writes will be blocked, and the master in control of the interface may continue
with its' sequence.
Table continues on the next page...

---

*Page 500*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
Clearing of PES (due to a sequence error) has the same effect as the clearing of PGM or ERS after a
program or erase operation. PEADR and DATA registers are cleared, PEID is cleared, and the operation
must be re-started from the beginning. If PGM or ERS are already set, they must be cleared before PES is
cleared. PES can only be cleared once the PGM or ERS bit for the sequence in progress is cleared. Since
this bit is a status flag, it must be cleared to a 0 by writing a 1 to the register location. A write of 0 has no effect.
0b - Program and erase sequence errors do not exist
1b - Previous program or erase sequence encountered an error
15 State Machine Status
DONE DONE indicates whether the embedded flash memory is performing a high voltage operation. DONE is set
to a 1 on termination of the embedded flash memory reset. DONE is read only. DONE is set to a 1 at the
end of program and erase high voltage sequences.
NOTE
This field transitions from a 0 to 1 during reset and remains at 1 after reset.
0b - Performing a high voltage operation
1b - Not executing a high voltage operation
14 Program/Erase Good
PEG The value of PEG is updated automatically during the program and erase high voltage operations. Aborting
a program/erase high voltage operation causes PEG to be cleared, indicating the sequence failed. PEG is
set to a 1 when the embedded flash memory is reset. PEG is read only.
The value of PEG is valid only when PGM = 1 or ERS = 1 and after DONE transitions from 0 to 1 due to an
abort or the completion of a program/erase operation. PEG is valid until PGM/ERS makes a 1 to 0 transition
or EHV makes a 0 to 1 transition.
NOTE
1. If program or erase operations are attempted on sector(s) that are locked, the
response from embedded flash memory is PEG = 1, indicating that the operation
was successful, and the contents of the sector(s) are properly protected from the
program or erase operation. PEG = 1 is also true if an abort occurs during an HV
request to a locked sector.
2. If a program or erase operation is attempted to a location marked as OTP,
the response from the embedded flash memory is PEG = 0, indicating that the
operation was not allowed. The value interlocked is not programmed, since desired
double word was already programmed with a previous program operation. Erase is
prevented on OTP locations.
0b - Program or erase operation failed
1b - Program or erase operation successful
Reserved
13
—
12 Watch Dog Interrupt
Table continues on the next page...

---

*Page 501*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
WDI WDI is a status register to indicate that the Watchdog Timer for Program or Erase had expired. WDI is status
only, and will be automatically cleared once the operation that caused the watchdog timeout is terminated or
clean up from the operation is completed (clearing of PGM (with PES low), ERS (with PES low) or PES bit).
0b - Normal Operation, Watchdog Timer has not expired.
1b - Program Watchdog Timer has expired.
Reserved
11-10
—
9 ECC Enabled Program/Erase Good
EPEG EPEG is a qualifier to PEG to indicate if a passing program or erase required ECC Enabled verifies to pass.
In the event of a failed operation (PEG=0), EPEG will always remain 0. The value of EPEG is updated
automatically during the program and erase high voltage operations. EPEG is set to a 0 when the embedded
flash memory is reset. EPEG is read only.
The value of EPEG is valid only when PGM = 1 or ERS = 1 and after DONE transitions from 0 to 1 due to an
abort or the completion of a program/erase operation. EPEG is valid until PGM/ERS makes a 1 to 0 transition
or EHV makes a 0 to 1 transition.
0b - Program or erase operation did not require ECC Enabled verifies.
1b - Program or erase operation required ECC Enabled verifies to pass.
8 UTest NVM Program and Erase Lock
TSPELOCK TSPELOCK reflects the status of the hardware program and erase protection input to the flash module.
The TSPELOCK register is latched on the PEADR[PEASAD] write (UTest Sector Interlock), is not writable,
and is status only. During high voltage events, the status is locked. When not interlocked this bit will read 1.
The default value of the TSPELOCK bits is program and erase protected.
0b - Corresponding sector not locked, and may be programmed or erased
1b - Corresponding sector protected from the program and erase sequences
Reserved
7-1
—
0 Reset Error
RE This bit provides information on previous resets. Checks are done within the flash and if a coherency issue
or ECC error is detected during the reset reads used for trimming on the flash, this status flag will assert.
This bit is status only, and is not writable.
0b - Reset occurred without errors
1b - Reset error encountered

---

*Page 502*

Embedded Flash Memory (c40asf)
20.7.1.4 Extended Module Configuration (MCRE)
Offset
Register Offset
MCRE 8h
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 n1M 0
W
Reset
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R n512K 0 n256K 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
1. The reset value of this register is set at the factory and varies among chips. See the chip-specific c40asf information for
details.
Fields
Field Function
Reserved
31-24
—
Number of 1 MB Blocks
23-21
000b - Zero 1 MB blocks
n1M
001b - One 1 MB block
010b - Two 1 MB blocks
011b - Three 1 MB blocks
100b - Four 1 MB blocks
101b - Reserved
110b - Reserved
111b - Reserved
Reserved
20-16
—
Number of 512 KB Blocks
15-14
Table continues on the next page...

---

*Page 503*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
n512K 00b - Zero 512 KB blocks
01b - One 512 KB block
10b - Two 512 KB blocks
11b - Four 512 KB blocks
Reserved
13-8
—
Number of 256 KB Blocks
7-6
00b - Zero 256 KB blocks
n256K
01b - One 256 KB block
10b - Two 256 KB blocks
11b - Four 256 KB blocks
Reserved
5-0
—
20.7.1.5 Module Control (CTL)
Offset
Register Offset
CTL Ch
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
RWSL RWSC
W
Reset 0 0 0 0 0 1 1 0 0 0 0 0 0 0 0 0

---

*Page 504*

Embedded Flash Memory (c40asf)
Fields
Field Function
Reserved
31-16
—
15 Read Wait State Lock
RWSL Read Wait State Lock. The RWSL bit locks the read wait state control register (RWSC). If RWSL is set, then
the RWSC field can not be written. RWSL is not clearable by a register write, it is only cleared upon reset.
Once RWSL is written to a 1, it will remain a 1 until the next reset and RWSC will be locked from writing.
RWSC and RWSL are allowed to be written simultaneously. RWSL does not affect readability of RWSC.
0b - RWSC not locked and available for writing
1b - RWSC locked and unavailable for writing
Reserved
14-13
—
12-8 Wait State Control
RWSC This field controls the number of wait states to be added to the best-case flash array access time for reads.
RWSC is only writable when RWSL is a 0. The best case flash array access time for reads is one cycle.
This field must be set to a value corresponding to the operation frequency of the Flash and the actual read
access time of the Flash to the flash boundary. Total read latency will be SoC dependant based on the
how the cores are coupled within the platform. The frequency information, and total latency details are
documented in the SoC specification.
Higher operating frequencies require non-zero settings for this field for proper flash operation.
NOTE
• Updates to this configuration field that control reads must take place only when the
flash memory is idle. Changing configuration settings while flash memory access is in
progress can lead to non-deterministic behavior.
• Values of RWSC[4:0] not listed here are reserved.
NOTE
0_0000b - Reserved
0_0001b - One additional wait state is added.
0_0010b - Two additional wait states are added.
0_0011b - Three additional wait states are added.
0_0100b - Four additional wait states are added.
0_0101b - Five additional wait states are added.
0_0110b - Six additional wait states are added.
0_0111b - Seven additional wait states are added.
0_1000b - Eight additional wait states are added.
Table continues on the next page...

---

*Page 505*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
0_1001b - Reserved
1_1111b - Reserved
Reserved
7-0
—
20.7.1.6 Address (ADR)
Offset
Register Offset
ADR 10h
Function
The Address register (ADR) provides the first failing address in the event of a failure (ECC or PGM/Erase state machine), as well as
single bit correction information. The address register is also writable for UTest Mode operations. The address is identified using
a combination of bits that identify the memory region and offset address.
NOTE
• The address given is an offset from the base address of the address space.
• The block numbering scheme that corresponds to the offset address is based on the flash memory's internal
addressing scheme, which may be different than the chip's system addressing scheme.
Understanding the mapping between the flash address, which is specified relative to the flash module's internal addressing
scheme, and the location within the chip's system memory map requires a clear understanding of the flash memory layout. See
the chip-specific information for a detailed explanation of the chip's flash memory layout and how to map system addresses to the
flash module's internal addressing.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
SAD A5 A4 A3 A2 A1 A0 ADDR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
ADDR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 506*

Embedded Flash Memory (c40asf)
Fields
Field Function
31 UTest NVM Address
SAD Qualifies the address captured during an ECC event error, single bit correction, or state machine operation.
See the description of the ADDR field for more information. This field is not writeable if UTE = 1, and is
ignored if it's in UTest mode.
0b - Address captured or to be accessed is from the main array space
1b - Address captured or to be accessed is from the UTest NVM array space
30-25 Reserved
— Reserved; reset to 0
24 Address Region 5
A5 Qualifies the address field (ADDR) to region 5
If the value of this field is 1, the ADDR field maps to region 5. See the description of the ADDR field for more
information. If the region is not present, this field is locked to 0.
0b - Address captured or to be accessed is not from region 5
1b - Address captured or to be accessed is from region 5
23 Address Region 4
A4 Qualifies the address field (ADDR) to region 4
If the value of this field is 1, the ADDR field maps to region 4. See the description of the ADDR field for more
information. If the region is not present, this field is locked to 0.
0b - Address captured or to be accessed is not from region 4
1b - Address captured or to be accessed is from region 4
22 Address Region 3
A3 Qualifies the address field (ADDR) to region 3
If the value of this field is 1, the ADDR field maps to region 3. See the description for the ADDR field for more
information. If the region is not present, this field is locked to 0.
0b - Address captured or to be accessed is not from region 3
1b - Address captured or to be accessed is from region 3
21 Address Region 2
A2 Qualifies the address field (ADDR) to region 2
If the value of this field is 1, the ADDR field maps to region 2. See the description of the ADDR field for more
information. If the region is not present, this field is locked to 0.
0b - Address captured or to be accessed is not from region 2
1b - Address captured or to be accessed is from region 2
20 Address Region 1
Table continues on the next page...

---

*Page 507*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
A1 Qualifies the address field (ADDR) to the region
If the value of this field is 1, the ADDR field maps to region 1. See the description for the ADDR field for more
information. If the region is not present, this field is locked to 0.
0b - Address captured or to be accessed is not from region 1
1b - Address captured or to be accessed is from region 1
19 Address Region 0
A0 Qualifies the ADDR field to region 0. If A0 = 1, then the ADDR field maps to that region. If the region is not
present, this field is locked to 0. For details, see the field description for ADDR.
0b - Address captured or to be accessed is not from region 0
1b - Address captured or to be accessed is from region 0
18-1 Address
ADDR The ADR register provides the first failing address in the event of ECC event error (EER set), single bit
correction (SBC set), as well as providing the address of a failure that may have occurred in a state machine
operation (PEG cleared). ECC event errors take priority over single bit corrections, which take priority over
state machine errors. This is especially valuable in the event of an RWW operation, where the read senses
an ECC error or single bit correction, and the state machine fails simultaneously. The failing address for ECC
event error is held until EER is cleared. The failing address for single bit correction is held until an ECC event
error, or until SBC is cleared. State machine address is held until an ECC event error or single bit correction
event occurs, or until the next state machine event (PEG cleared) occurs. This address is always a double
word address that selects 64 bits.
The ADR register is writable, and can be used in the UTest ECC Logic Test, UTest EDC after ECC Logic
Test, and UTest Address Encode Logic Test. If any of these tests are enabled (UT0[EIE] = 1, UT0[EDIE] =
1, or UT0[AEIE] = 1) then the ADR register will not update for ECC event error, single bit correction or state
machine errors.
If MCRS[EER] or MCRS[SBC] are set, the ADR register is locked from writing. MCRS[PEG] does not affect
the writeability of the ADR register.
ADDR[18:1] is an offset from a base address of 0h for each block region. The A0, A1, A2, A3, A4, and A5
bits qualify the block size region the ADDR field.
0 Reserved
— Reserved; reset to 0
20.7.1.7 Program and Erase Address (PEADR)
Offset
Register Offset
PEADR 14h

---

*Page 508*

Embedded Flash Memory (c40asf)
Function
The Program and Erase Address register (PEADR) is used to provide the address to be programmed or the location of the sector
or block to be erased. The program and erase address register is read only in user mode (user mode writes occur with writes
done to the Platform Flash Controller). The address is identified using a combination of bits that identify the memory region and
offset address.
NOTE
• The address provided is an offset from the base address of the address space.
• The block numbering scheme that corresponds to the offset address is based on the flash memory's internal
addressing scheme, which may be different from the chip's system addressing scheme.
Understanding the mapping between the flash address, which is specified relative to the flash module's internal addressing
scheme, and the location within the chip's system memory map requires a clear understanding of the flash memory layout. See
the chip-specific information for a detailed explanation of the chip's flash memory layout and how to map system addresses to the
flash module's internal addressing.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
PEAS
R 0 PEA5 PEA4 PEA3 PEA2 PEA1 PEA0 PEADDR
AD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R PEADDR 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 UTest NVM Program and Erase Address
PEASAD Qualifies the address field (PEADDR) to the region.
If PESAD = 1, the PEADDR field maps to that region. See the description of the PEADDR field for
more information.
This field is not writeable if UTE = 1 and is ignored if it becomes 1 while in UTest mode.
0b - Address accessed is from the main array space
1b - Address accessed is from the UTest NVM array space
30-25 Reserved
— Reserved; reset to 0
24 Program and Erase Address Region 5
PEA5 Qualifies the address field (PEADDR) to region 5.
Table continues on the next page...

---

*Page 509*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
If PEA5 = 1, the PEADDR field maps to region 5. See the description of the PEADDR field for more
information. If region 5 is not present, this field is locked to 0.
0b - Address accessed is not from region 5
1b - Address accessed is from region 5
23 Program and Erase Address Region 4
PEA4 Qualifies the address field (PEADDR) to region 4.
If PEA4 = 1, the PEADDR field maps to region 4. See the description of the PEADDR field for more
information. If region 4 is not present, this field is locked to 0.
0b - Address accessed is not from region 4
1b - Adrress accessed is from region 4
22 Program and Erase Address Region 3
PEA3 Qualifies the address field (PEADDR) to region 3.
If PEA3 = 1, the PEADDR field maps to region 3. See the description for PEADDR for more information. If
region 3 is not present, this field is locked to 0.
0b - Address accessed is not from region 3
1b - Address accessed is from region 3
21 Program and Erase Address Region 2
PEA2 Qualifies the address field (PEADDR) to the region.
If PEA2 = 1, the PEADDR field maps to region 2. See the description of the PEADDR field for more
information. If region 2 is not present, this field is locked to 0.
0b - Address accessed is not from region 2
1b - Address accessed is from region 2
20 Program and Erase Address Region 1
PEA1 Qualifies the address field (PEADDR) to the region.
If PEA1 = 1, the PEADDR field maps to region 1. See the description of the PEADDR field for more
information. If region 1 is not present, this field is locked to 0.
0b - Address accessed is not from region 1
1b - Address accessed is from region 1
19 Program and Erase Address Region 0
PEA0 Qualifies the address field (PEADDR) to the region.
If PEA0 = 1, the PEADDR field maps to region 0. See the description of the PEADDR field for more
information. If region 0 is not present, this field is locked to 0.
0b - Address accessed is not from region 0
1b - Address accessed is from region 0
Table continues on the next page...

---

*Page 510*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
18-5 Program and Erase Address
PEADDR The PEADDR register provides offset address location to be programmed or the sector to be erased in the
case of sector erase. This address is always a quad page address that selects 1024 bits.
The PEADDR register is read only in user mode and represents the flash physical address status. PEADDR
may be updated once (and only once) per Program or Erase event. PEADDR writes initiate a Program,
Erase, Array Integrity or User Margin Read request.
Once PEADDR is updated, it will be locked for the full program and erase operation, until MCR[PGM] or
MCR[ERS] is cleared at the end of the operation (as long as MCRS[PES] is zero). If MCRS[PES] is set,
PEADR is locked until this bit is also cleared.
NOTE
If Express Program has been initiated (XPEADR written), the PEADR is locked from writing
until Express Program is completed (XPGM written to 0).
Upon completion of an operation (Program (MCR[PGM] written to 0 and MCRS[PES] equals 0), Erase
(MCR[ERS] written to 0 and MCRS[PES] equals 0), Array Integrity (UT0[AIE] written to 0), User Margin
Read(UT0[AIE] written to 0)), or Program and Erase Sequence clear (MCRS[PES] written to 0) the PEADR
registers will return to an all zeros state.
When MCR[EHV] or UT0[AIE] are set, PEADDR bits will not be writeable.
Once PEADDR is updated, it will have read restrictions based on the PEID master that did the original write
in the Platform Flash Controller.
PEADDR[18:5] is an offset from a base address of 0h for each block region. The PEA0, PEA1, PEA2, PEA3,
PEA4 and PEA5 qualify the block size region the PEADDR field.
4-0 Reserved
— Reserved; reset to 0
20.7.1.8 Sector Program and Erase Hardware Lock (SPELOCK)
Offset
Register Offset
SPELOCK 50h
Function
The Sector Program and Erase Lock register provides a means to protect sectors from being programed and erased on the main
interface. The last 256 KB of all blocks have sector lock protection capability. The rest of the block (if applicable) is protected with
super sector protection capability. This register shows the status of the pelock hardware input for the block that is interlocked. For
more information on hardware program and erase protection see Program hardware locking and Erase hardware locking .

---

*Page 511*

Embedded Flash Memory (c40asf)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R SPELOCK
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R SPELOCK
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31-0 Sector Program and Erase Lock [31:0]
SPELOCK SPELOCK reflects the status of the hardware program and erase protection input to the flash module for the
block interlocked on the main interface.
The SPELOCK register is latched on the PEADR write, is not writable, and is status only. SPELOCK will
return back to all ones at the same time that PEADR is cleared. When not interlocked this register will read
all ones. During high voltage events, the status is locked.
The default value of the SPELOCK bits is program and erase protected. In the event that sectors are not
present (due to configuration or total memory size), the SPELOCK bits default to locked, and will remain 1.
0 - The corresponding sector is not locked, and may be programed or erased.
1 - The corresponding sector is protected from program or erase.
20.7.1.9 Super Sector Program and Erase Hardware Lock (SSPELOCK)
Offset
Register Offset
SSPELOCK 54h
Function
The Program and Erase Lock register provides a means to protect super sectors from being programed and erased on the
main interface. Super Sector protection is available on block space greater than 256 KB. For 256 KB blocks, this register is not
applicable. For 512 KB blocks, the first half of the block is protected with super sector granularity. For 1 MB blocks, the first 768
KB is protected with super sector granularity. For 2 MB blocks, the first 1792 KB is protected with super sector granularity. This
register shows the status of the pelock hardware input for the block interlocked. For more information on hardware program and
erase protection see Program hardware locking and Erase hardware locking .

---

*Page 512*

Embedded Flash Memory (c40asf)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 SSPELOCK
W
Reset 0 0 0 0 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31-12 Reserved
— Reserved; reset to 0
11-0 Super Sector Program and Erase Lock [11:0]
SSPELOCK SSPELOCK reflects the status of the hardware program and erase protection input to the flash module for
the block interlocked on the main interface.
The SSPELOCK register is latched on the PEADR write, is not writable, and is status only. SSPELOCK will
return back to all ones at the same time that PEADR is cleared. When not interlocked this register will read
all ones. During high voltage events, the status is locked.
The default value of the SSPELOCK bits is program and erase protected. In the event that super sectors
are not present (due to configuration or total memory size), or super sectors not present due to block size,
the SSPELOCK bits default locked, and will remain 1.
0 - The corresponding super sector is not locked, and may be programmed or erased.
1 - The corresponding super sector is protected from program or erase.
20.7.1.10 Express Sector Program and Erase Hardware Lock (XSPELOCK)
Offset
Register Offset
XSPELOCK 70h
Function
The Express Program and Erase Lock register provides a means to protect sectors from being programed and erased on the
express program interface. The last 256 KB of all blocks have sector lock protection capability. The rest of the block (if applicable)
is protected with super sector protection capability. This register shows the status of the pelock hardware input for the block that is
interlocked. For more information on hardware program and erase protection see Program hardware locking and Erase hardware
locking .

---

*Page 513*

Embedded Flash Memory (c40asf)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R XSPELOCK
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R XSPELOCK
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31-0 Express Sector Program and Erase Lock [31:0]
XSPELOCK XSPELOCK reflects the status of the hardware program and erase protection input to the flash module for
the block interlocked on the express program interface.
The XSPELOCK register is latched on the XPEADR write, is not writable, and is status only. XSPELOCK
will return back to all ones at the same time that XPEADR is cleared. When not interlocked this register will
read all ones. During high voltage events, the status is locked.
The default value of the XSPELOCK bits is program and erase protected. In the event that sectors are not
present (due to configuration or total memory size), the XSPELOCK bits default to locked, and will remain 1.
0 - The corresponding sector is not locked, and may be programmed or erased.
1 - The corresponding sector is protected from program or erase.
20.7.1.11 Express Super Sector Program and Erase Hardware Lock (XSSPELOCK)
Offset
Register Offset
XSSPELOCK 74h
Function
The Express Program and Erase Lock register provides a means to protect super sectors from being programed and erased
on the express program interface. Super Sector protection is available on block space greater than 256 KB. For 256 KB blocks,
this register is not applicable. For 512 KB blocks, the first half of the block is protected with super sector granularity. For 1 MB
blocks, the first 768 KB is protected with super sector granularity. For 2 MB blocks, the first 1792 KB is protected with super sector
granularity. This register shows the status of the pelock hardware input for the block interlocked. For more information on hardware
program and erase protection see Program hardware locking and Erase hardware locking .

---

*Page 514*

Embedded Flash Memory (c40asf)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 XSSPELOCK
W
Reset 0 0 0 0 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31-12 Reserved
— Reserved; reset to 0
11-0 Express Super Sector Program and Erase Lock [11:0]
XSSPELOCK XSSPELOCK reflects the status of the hardware program and erase protection input to the flash module for
the block interlocked on the express program interface.
The XSSPELOCK register is latched on the XPEADR write, is not writable, and is status only. XSSPELOCK
will return back to all ones at the same time that XPEADR is cleared. When not interlocked this register will
read all ones. During high voltage events, the status is locked.
The default value of the XSSPELOCK bits is program and erase protected. In the event that super sectors
are not present (due to configuration or total memory size), or super sectors not present due to block size,
the XSSPELOCK bits default to locked, and will remain 1.
0 - The corresponding super sector is not locked, and may be programmed or erased.
1 - The corresponding super sector is protected from program or erase.
20.7.1.12 Test Mode Disable Password Check (TMD)
Offset
Register Offset
TMD 90h
Function
Provides a means to supply a challenge password to disable the Test mode disable seal block select.
Writes to the register have no effect except for a password challenge. Reads to this register always return 0.

---

*Page 515*

Embedded Flash Memory (c40asf)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
PWD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PWD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Password Challenge
31-0
PWD
20.7.1.13 UTest 0 (UT0)
Offset
Register Offset
UT0 94h
Function
Provides a means to control UTest.
UTest mode provides the ability to perform test features on the flash memory. This register is only writeable when the flash memory
is put into UTest mode by writing a passcode.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
UTE SBCE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 AID
RRIE AEIE EDIE EIE NAIBP AIBPE AISUS MRE MRV AIS AIE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1

---

*Page 516*

Embedded Flash Memory (c40asf)
Fields
Field Function
31 UTest Enable
UTE This status bit indicates when U-Test is enabled. All bits in UT0, UM0, UM1, UM2, UM3, UM4, UM5, UM6,
UM7, UM8, UM9, UD0, UD1, UD2, UD3, UD4, UD5, UA0 and UA1 registers are locked when this bit is 0.
This bit is not writeable to a 1, but may be cleared. The reset value is 0. The method to set this bit is to
provide a password, and if the password matches, the UTE bit is set to reflect a status of enabled, and is
enabled until it is cleared by a register write. The UTE password (set UTE=1) and UTE clearing to 0 will only
be accepted if Program or Erase are not in progress (PEADR write through clearing of PGM/ERS/PES),
Alternate Program or Erase are not in progress (APEADR write through clearing of APGM/AERS/APES),
AI/MR Operation are not in progress (PEADR write through clearing of AIE), and an Express Program
operation is not in progress (XPEADR write through the clearing of XPGM). UTE can only be cleared if AID
= 1, MRE, AIE, EIE, EDIE, AEIE, and RRIE are all equal to 0. While successfully clearing UTE, writes to set
AIE, MRE, EIE, EDIE, AEIE or RRIE will be ignored. For UTE, the password F9F9_9999h must be written
to the UT0 register, and this must be a 32bit write.
0b - U-Test mode is not enabled.
1b - U-Test mode is enabled.
30 Single Bit Correction Enable
SBCE SBCE enables single bit correction results to be observed in SBC. ECC corrections that occur when SBCE
is cleared will not be logged.
0b - Disabled
1b - Enabled
29-16 Reserved
— Reserved; reset to 0
15 Read Reference Input Enable
RRIE RRIE enables the force of an error on the read reference detection circuit. This is useful in the Read
Reference Error check. If this bit is set, the read reference error register MCRS[RRE] will set when an
address match is achieved to the ADR register. RRIE is not simultaneously writable to a 1 as UTE is being
successfully cleared to a 0.
0b - Read reference input disabled
1b - Read reference input enabled
14 Address Encode Invert Enable
AEIE AEIE enables the input register AEI to invert the address encode value received from the array, and force a
mismatch in the address encode comparison. This is useful in the Address Encode logic check. If this bit is
set, address encode information from the memory array will be inverted based on values in the AEI register
when an address match is achieved to the ADR register. AEIE is not simultaneously writable to a 1 as UTE
is being successfully cleared to a 0.
0b - Address encode invert is disabled
1b - Address encode values are inverted based on UA0[AEI]
13 EDC after ECC Data Input Enable
Table continues on the next page...

---

*Page 517*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
EDIE EDIE enables the input registers EDDATA and EDDATAC to be the source of data to the EDC after ECC
comparator or EDC after ECC encoder. This is useful in the EDC after ECC logic check. If this bit is set,
data read via a BIU read request will be from the EDDATA and EDDATAC registers when an address match
is achieved to the ADR register. EDIE is not simultaneously writable to a 1 as UTE is being successfully
cleared to a 0.
0b - EDC after ECC data input is disabled
1b - Data read is from UD3[EDDATA] and UD5[EDDATAC]
12 ECC Data Input Enable
EIE EIE enables the input registers EDATA and EDATAC to be the source of data to the ECC logic (instead
of from the memory array). This is useful in the ECC logic check. If this bit is set, data read via a BIU read
request will be from the EDATA and EDATAC registers when an address match is achieved to the ADR
register. EIE is not simultaneously writable to a 1 as UTE is being successfully cleared to a 0.
0b - ECC data input is disabled
1b - Data read is from UD0[EDATA] and UD2[EDATAC]
Reserved
11-10
—
9 Next Array Integrity Break Point
NAIBP If AIBPE is set, NAIBP will be set once a single bit correction (if enabled) or double bit detection is noted
during the Array Integrity test. NAIBP is not writable to 1, but may be cleared to 0. Clearing NAIBP to 0, will
enable the Array Integrity operation to be re-started after a breakpoint is encountered. NAIPB may only be
cleared to 0 if both EER = 0 and SBC = 0. If the Array Integrity operation completes without encountering
another correction or detection, AID will be set with NAIBP remaining 0.
0b - Array integrity state machine is not currently at a breakpoint
1b - Array integrity state machine is at a breakpoint
8 Array Integrity Break Point Enable
AIBPE To enable breakpoints during an array integrity test, AIBPE may be set. See NAIBP description for more
information about array integrity breakpoints.
0b - Array integrity breakpoints disabled
1b - Array integrity breakpoints enabled during array integrity checks
7 Reserved
— Reserved; reset to 0
6 Array Integrity Suspend
AISUS AISUS enables a suspend of an Array Integrity Operation. Array Integrity may be suspend by setting AISUS,
and resumed by clearing AISUS. AISUS is writeable to a 1 only when AID is low. AISUS is clearable to a
0 only when AID is high. Attempting to write AISUS and AIE on the same clock cycle will result in only AIE
getting written.
Table continues on the next page...

---

*Page 518*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
0b - Array integrity sequence not suspended.
1b - Array integrity sequence is suspended.
5 Margin Read Enable
MRE MRE combined with MRV enables user margin reads to be done. Normal user reads are not affected by
MRE, although user reads while the margin read operation is ongoing are not supported. MRE is not writable
if AID is low, or if AISUS is high, or if NAIBP is high. MRE is not simultaneously writable to a 1 as UTE is
being successfully cleared to a 0.
0b - Margin reads are not enabled.
1b - Margin reads are enabled.
4 Margin Read Value
MRV MRV selects the margin level that is being checked. Margin can be checked to an erased level (MRV=1) or to
a programmed level (MRV=0). In order for this value to be valid, MRE must also be set. MRV is not writable
if AID is low, or if AISUS is high, or if NAIBP is high.
0b - Zero's margin reads are requested.
1b - One's margin reads are requested.
Reserved
3
—
2 Array Integrity Sequence
AIS AIS determines the address sequence to be used during array integrity checks. The default sequence
(AIS = 0) is meant to replicate sequences that normal user code follows, and thoroughly checks the read
propagation paths. This sequence is proprietary. The alternative sequence (AIS = 1) is logically sequential.
It should be noted that the time to run a sequential sequence is shorter than the time to run the proprietary
sequence. AIS is not writeable if AIE is high.
0b - Array integrity sequence is proprietary sequence
1b - Array integrity sequence is sequential
1 Array Integrity Enable
AIE AIE set to one starts the array integrity check done on all selected blocks. The address sequence is
determined by AIS, and the MISR (UM0 through UM9) can be checked after the operation is complete, to
determine if a correct signature has been obtained. Once an array integrity operation is requested (AIE=1),
it may be terminated by clearing AIE if the operation has finished (AID = 1) or aborted by clearing AIE if the
operation is ongoing (AID = 0). AIE is locked from writing unless an interlock write occurred (PEADR write
and DATA write). AIE is not simultaneously writable to a 1 as UTE is being successfully cleared to a 0.
0b - Array integrity checks not enabled
1b - Array integrity checks enabled
0 Array Integrity Done
Table continues on the next page...

---

*Page 519*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
AID AID is cleared upon an array integrity check being enabled (to signify the operation is ongoing). Once
completed, AID is set to indicate that the array integrity check is complete. At this time the MISR (UMR
registers) can be checked. AID may also assert if breakpoints are enabled (AIBPE is set), an abort is
requested or a suspend is requested. AID cannot be written, and is status only.
0b - Array integrity check ongoing
1b - Array integrity check complete
20.7.1.14 UMISRn (UM0 - UM8)
Offset
For a = 0 to 8:
Register Offset
UMa 98h + (a × 4h)
Function
Provides a means to evaluate array integrity. UM9 to UM0 are a set of multiple-input signature registers (MISRs) that hold the
289-bit MISR value as shown in the next table.
Table 92. MISR mapping
UM register MISR bits
UM0 MISR[31:0]
UM1 MISR[63:32]
UM2 MISR[95:64]
UM3 MISR[127:96]
UM4 MISR[159:128]
UM5 MISR[191:160]
UM6 MISR[223:192]
UM7 MISR[255:224]
UM8 MISR[287:256]
UM9 MISR[288]

---

*Page 520*

Embedded Flash Memory (c40asf)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
MISR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
MISR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 MISR[31:0]
MISR The MISR registers accumulate a signature from an array integrity event. The MISR captures all data fields,
as well as ECC fields, and the ECC error signal. Data (read and ECC) captured in the MISR is after the ECC
logic, and represents corrected data (if required).
The MISR can be seeded to any value by writing the MISR registers.
NOTE
Writing the MISR register during an array integrity operation (including suspend or
breakpoint) although possible, is not recommended. A write of the MISR registers at
this point may alter the final signature in an unpredictable way. Writing the MISR registers
prior to an Array Integrity operation (to seed the MISR) is allowed.
The MISR register provides a means to calculate a MISR during array integrity operations.
The MISR can be represented by the following polynomial:
289 7 6 5 4 2
x + x + x + x + x + x + 1
The MISR is calculated by taking the previous MISR value and then "exclusive ORing" the new data.
In addition the most significant bit (in this case it is MISR[288]), is then "exclusive ORed" into input of
MISR[7], MISR[6], MISR[5], MISR[4], MISR[2] and MISR[0]. The result of the "exclusive OR" is shifted left
on each read.
The MISR register is used in array integrity operations.
If during address sequencing, reads extend into an invalid address location (in other words, greater than the
maximum address for a given array size) then reads are still executed to the array, but the results from the
array read are not deterministic. In this instance, the MISR registers are not recalculated, and the previous
value is retained. Once the AIE register is cleared, the MISR register will return to an all 0's state.

---

*Page 521*

Embedded Flash Memory (c40asf)
20.7.1.15 UMISR9 (UM9)
Offset
Register Offset
UM9 BCh
Function
Provides a means to evaluate array integrity.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
MISR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-1 Reserved
— Reserved; reset to 0
0 MISR[288]
MISR The MISR registers accumulate a signature from an array integrity event. The MISR captures all data fields,
as well as ECC fields, and the ECC error signal. Data (read and ECC) captured in the MISR is after the ECC
logic, and represents corrected data (if required).
The MISR can be seeded to any value by writing the MISR registers.
NOTE
Writing the MISR register during an array integrity operation (including suspend or
breakpoint) although possible, is not recommended. A write of the MISR registers at
this point may alter the final signature in an unpredictable way. Writing the MISR registers
prior to an Array Integrity operation (to seed the MISR) is allowed.
The MISR register provides a means to calculate a MISR during array integrity operations.
The MISR can be represented by the following polynomial:
289 7 6 5 4 2
x + x + x + x + x + x + 1
Table continues on the next page...

---

*Page 522*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
The MISR is calculated by taking the previous MISR value and then "exclusive ORing" the new data.
In addition the most significant bit (in this case it is MISR[288]), is then "exclusive ORed" into input of
MISR[7], MISR[6], MISR[5], MISR[4], MISR[2] and MISR[0]. The result of the "exclusive OR" is shifted left
on each read.
The MISR register is used in array integrity operations.
If during address sequencing, reads extend into an invalid address location (in other words, greater than the
maximum address for a given array size) then reads are still executed to the array, but the results from the
array read are not deterministic. In this instance, the MISR registers are not recalculated, and the previous
value is retained. Once the AIE register is cleared, the MISR register will return to an all 0's state.
20.7.1.16 UTest Data 0 (UD0)
Offset
Register Offset
UD0 D0h
Function
Provides a means to enable data to be substituted during UTest procedures.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
EDATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
EDATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 ECC Data [31:0]
EDATA Enables checks of ECC logic by allowing data bits to be input into the ECC logic and then read out by doing
array reads or array integrity checks. EDATA corresponds to the 32 array bits representing word 0 of all
double words in the page selected in ADR .

---

*Page 523*

Embedded Flash Memory (c40asf)
20.7.1.17 UTest Data 1 (UD1)
Offset
Register Offset
UD1 D4h
Function
Provides a means to enable data to be substituted during UTest procedures.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
EDATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
EDATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 ECC Data [63:32]
EDATA Enables checks of ECC logic by allowing data bits to be input into the ECC logic and then read out by doing
array reads or array integrity checks. UD0[EDATA] corresponds to the 32 array bits representing word 1 of
all double words in the page selected in ADR .
20.7.1.18 UTest Data 2 (UD2)
Offset
Register Offset
UD2 D8h
Function
Provides a means to enable data to be substituted during UTest procedures.

---

*Page 524*

Embedded Flash Memory (c40asf)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
ED3 ED2 ED1 ED0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
EDATAC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-28
—
27 ECC Logic Check Double Word 3
ED3 Enables checks of ECC logic by allowing data bits to be input into the ECC logic and then read out by doing
array reads or array integrity checks.
If this field's value becomes 1, the data replacement occurs on DW3 on the page selected in ADR .
26 ECC Logic Check Double Word 2
ED2 Enables checks of ECC logic by allowing data bits to be input into the ECC logic and then read out by doing
array reads or array integrity checks.
If this field's value becomes 1, the data replacement occurs on DW2 on the page selected in ADR .
25 ECC Logic Check Double Word 1
ED1 Enables checks of ECC logic by allowing data bits to be input into the ECC logic and then read out by doing
array reads or array integrity checks.
If this field's value becomes 1, the data replacement occurs on DW1 on the page selected in ADR .
24 ECC Logic Check Double Word 0
ED0 Enables checks of ECC logic by allowing data bits to be input into the ECC logic and then read out by doing
array reads or array integrity checks.
If this field's value becomes 1, the data replacement occurs on DW0 on the page selected in ADR .
Reserved
23-8
—
7-0 ECC Data Check Bits [7:0]
EDATAC Enables checks of ECC logic by allowing data bits to be input into the ECC logic and then read out by doing
array reads or array integrity checks.
Table continues on the next page...

---

*Page 525*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
This field corresponds to the eight array bits representing the check bits of all double words in the page
selected in ADR .
20.7.1.19 UTest Data 3 (UD3)
Offset
Register Offset
UD3 DCh
Function
Provides a means to enable data to be substituted during UTest procedures.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
EDDATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
EDDATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 EDC After ECC Data [31:0]
EDDATA Enables checks of EDC after ECC logic by allowing data bits to be input into the EDC after ECC logic and
then read out by doing array reads or array integrity checks.
This field corresponds to the 32 array bits representing word 0 of all double words in the page selected
in ADR .

---

*Page 526*

Embedded Flash Memory (c40asf)
20.7.1.20 UTest Data 4 (UD4)
Offset
Register Offset
UD4 E0h
Function
Provides a means to enable data to be substituted during UTest procedures.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
EDDATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
EDDATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 EDC After ECC Data [63:31]
EDDATA Enables checks of EDC after ECC logic by allowing data bits to be input into the EDC after ECC logic and
then read out by doing array reads or array integrity checks.
This field corresponds to the 32 array bits representing word 1 of all double words in the page selected
in ADR .
20.7.1.21 UTest Data 5 (UD5)
Offset
Register Offset
UD5 E4h
Function
Provides a means to enable data to be substituted during UTest procedures.

---

*Page 527*

Embedded Flash Memory (c40asf)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
EDD3 EDD2 EDD1 EDD0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
EDDATAC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-28
—
27 EDC After ECC Logic Check Double Word 3
EDD3 Enables checks of EDC after ECC logic by allowing data bits to be input into the EDC after ECC logic and
then read out by doing array reads or array integrity checks.
If this field's value becomes 1, the data replacement occurs on DW3 on the page selected in ADR .
26 EDC after ECC Logic Check Double Word 2
EDD2 Enables checks of EDC after ECC logic by allowing data bits to be input into the EDC after ECC logic and
then read out by doing array reads or array integrity checks.
If this field's value becomes 1, the data replacement occurs on DW2 on the page selected in ADR .
25 EDC After ECC Logic Check Double Word 1
EDD1 Enables checks of EDC after ECC logic by allowing data bits to be input into the EDC after ECC logic and
then read out by doing array reads or array integrity checks.
If this field's value becomes 1, the data replacement occurs on DW1 on the page selected in ADR .
24 EDC After ECC Logic Check Double Word 0
EDD0 Enables checks of EDC after ECC logic by allowing data bits to be input into the EDC after ECC logic and
then read out by doing array reads or array integrity checks.
If this field's value becomes 1, the data replacement occurs on DW0 on the page selected in ADR .
Reserved
23-8
—
7-0 EDC After ECC Data Check Bits [7:0]
EDDATAC Enables checks of EDC after ECC logic by allowing data bits to be input into the EDC after ECC logic and
then read out by doing array reads or array integrity checks.
Table continues on the next page...

---

*Page 528*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
This field corresponds to the eight array bits representing the check bits of all double words in the page
selected in ADR .
20.7.1.22 UTest Address 0 (UA0)
Offset
Register Offset
UA0 E8h
Function
Provides a means to enable address to be inverted during UTest procedures.
NOTE
• UTest Address 0 (UA0) and UTest Address 1 (UA1) combine to represent a 52-bit wide field, which matches
the width of the address internally to the flash memory that is used for the address encode comparison.
• Writing 1 to any one of the fields in UA0 or UA1 leads to flagging an AEE error when causing the error injection.
• When the value of a field within the range of bits 18:3 is 1, the corresponding address signal to the flash
memory controller or BIU is inverted.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
AEI
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
AEI
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Address Encode Invert [31:0]
AEI Enables checks of address encode logic by allowing address bits to be inverted into the address encode
compare logic, forcing a miscompare. Performing array reads on the page selected in ADR allows the
address encode invert(s) to be active.
Table continues on the next page...

---

*Page 529*

Embedded Flash Memory (c40asf)
Field Function
Additionally, in the event of an address encode error during normal user mode operation, the page address
provided to the PFC is inverted when an address encode error is encountered. AEI[18:3], which reflects the
page address, can be used with the Address Encode Logic Check to control this inversion feature (aligning
with ADR ). Any bit in AEI[18:3] written to a 1 also enables the inversion of the corresponding signal of that
address, allowing checks to be performed on the comparison logic that exists in the PFC.
NOTE
Multiple bits in AEI[18:3] may be written to a 1, which causes these bits to miscompare on
the address encode comparison logic, as well as causing multiple bits to have their inversion
enabled as part of the address provided to the BIU.
20.7.1.23 UTest Address 1 (UA1)
Offset
Register Offset
UA1 ECh
Function
Provides a means to enable address to be inverted during UTest procedures.
NOTE
• UTest Address 0 (UA0) and UTest Address 1 (UA1) combine to represent a 52-bit wide field, which matches
the width of the address internally to the flash that is used for the address encode comparison.
• Writing 1 to any one of the fields in UA0 or UA1 leads to flagging an AEE error when causing the error injection.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
AEI
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
AEI
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 530*

Embedded Flash Memory (c40asf)
Fields
Field Function
Reserved
31-20
—
19-0 Address Encode Invert [51:32]
AEI Enables checks of address encode logic by allowing address bits to be inverted into the address encode
compare logic, forcing a miscompare. Performing array reads on the page selected in ADR allows the
address encode invert(s) to be active.
20.7.1.24 Express Module Configuration (XMCR)
Offset
Register Offset
XMCR F0h
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 XPEID
W
Reset 0 0 0 0 0 0 0 0 1 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
XDON XEPE
R XPEG XDOK XWDI 0 0
XWDI
E G
XPGM XEHV
E
W
Reset 1 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-24
—
23-16 Express Program Master/Domain ID
XPEID This field shows the ID of the master that has started the express program sequence. The ID is latched when
the sequence has started (writing of the XPEADR register). It is set back to the reset value (set to all '1') when
the operation is completed. See the chip-specific information for a list of Master IDs.
15 Express State Machine Status
Table continues on the next page...

---

*Page 531*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
XDONE XDONE indicates whether the embedded flash memory is performing an express program operation.
XDONE is set to a 1 on completion of the express program operation.
0b - Executing an express program operation
1b - Not executing an express program operation
14 Express Program Good
XPEG The XPEG field indicates the completion status of the last flash memory express program operation. The
value of XPEG is updated automatically after the program operations. XPEG will be 0 if the program
operation failed. See MCRS[PEG] for more information, as the features of that bit apply to XPEG as well.
0b - Program operation failed
1b - Program operation successful
13 Express Data OK
XDOK This field is a status signal from the flash to indicate that it is OK to load Data into the DATA registers. It will
assert in response to a XPEADR load (once ongoing operations are aborted, if required). XDOK will clear
once the Express Program operation is complete (XPGM written to 0).
0b - Flash memory not ready to accept writes to the DATA registers
1b - Writes to DATA registers allowed
12 Express Watch Dog Interrupt
XWDI XWDI is a status register to indicate that the Watchdog Timer for Express Program had expired. XWDI
is status only, and will be automatically cleared once the operation that caused the watchdog timeout is
terminated or clean up from the operation is completed (clearing of XPGM bit).
0b - Normal Operation, Watchdog Timer has not expired.
1b - Express Program Watchdog Timer has expired.
11 Express Watch Dog Interrupt Enable
XWDIE XWDIE provides a mechanism to trigger an interrupt request upon the assertion of the XWDI bit on the
express interface. If XWDIE is asserted, when XWDI asserts an interrupt from the flash will trigger to the
system. The interrupt from the flash will mirror exactly the XWDI bit. XWDIE does not affect the register bit
XWDI. Writing (and reading) of this bit will be restricted to the master that updated the XPEADR register as
captured in the XPEID register at the start of the Express Program sequence.
0b - Express watchdog interrupt disabled
1b - Express watchdog interrupt enabled
Reserved
10
—
9 Express ECC Enabled Program Good
XEPEG XEPEG is a qualifier to XPEG to indicate if a passing program required ECC Enabled verifies to pass. In
the event of a failed operation (XPEG=0), XEPEG will always remain 0. The value of XEPEG is updated
Table continues on the next page...

---

*Page 532*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
automatically after the program operations. See MCRS[EPEG] for more information, as the features of that
bit apply to XEPEG as well.
0b - Program operation did not require ECC-enabled verifies
1b - Program operation required ECC-enabled verifies to pass
8 Express Program
XPGM XPGM is used as part of the setup for an express program operation.
A 0 to 1 transition of XPGM after program interlock write(s) is part of the setup for an express program
sequence. A 1 to 0 transition of XPGM ends the express program sequence.
XPGM can be set at any time if UT0[UTE] is low, and after the interlock write is completed (XPEADR written,
XDOK asserted, and DATAn register(s) written).
XPGM may be cleared when XEHV is low. Writing (and reading) of this bit will be restricted to the master
that updated the XPEADR register as captured in the XPEID register at the start of the Program sequence.
0b - Flash memory not executing an express program sequence
1b - Flash memory executing an express program sequence
Reserved
7-1
—
0 Express Enable High Voltage
XEHV The XEHV field enables the embedded flash memory for a high voltage program/erase operation. XEHV is
cleared on reset. XEHV must be set after DATA writes to start an express program operation and XPGM is
set. This field may be set when XPGM = 1, XDOK = 1, and XWDI = 0. XEHV may not be written to a 1 after
it is cleared as part of an express program cleanup, until after the next XPEADR write.
Express Program operations may not be aborted. XEHV may only be cleared once XDONE is set to 1.
Writing (and reading) of this bit will be restricted to the master that updated the XPEADR register as captured
in the XPEID register at the start of the Program sequence.
0b - Flash memory is not enabled to perform an express high voltage operation.
1b - Flash memory is enabled to perform an express high voltage operation.
20.7.1.25 Express Program Address (XPEADR)
Offset
Register Offset
XPEADR F4h
Function
The Express Program Address register (XPEADR) is used to provide the address to be programmed. The express program
address register is read only in user mode (user mode writes occur with writes done to the Platform Flash Controller). The address
is identified using a combination of bits that identify the memory region and offset address.

---

*Page 533*

Embedded Flash Memory (c40asf)
NOTE
• The address given is an offset from the base address of the address space.
• The block numbering scheme that corresponds to the offset address is based on the flash memory's internal
addressing scheme, which may be different than the chip's system addressing scheme.
Understanding the mapping between the flash address, which is specified relative to the flash module's internal addressing
scheme, and the location within the chip's system memory map requires a clear understanding of the flash memory layout. See
the chip-specific information for a detailed explanation of the chip's flash memory layout and how to map system addresses to the
flash module's internal addressing.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 XPEA5 XPEA4 XPEA3 XPEA2 XPEA1 XPEA0 XPEADDR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R XPEADDR 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-25 Reserved
— Reserved; reset to 0
24 Express Program Address Region 5
XPEA5 Qualifies the address field (XPEADDR) to the region.
If XPEA5 = 1, the XPEADDR field maps to that region. See the description of the XPEADDR field for more
information. If this region is not present, the field is locked to 0.
0b - Address accessed is not from region 5
1b - Address accessed is from region 5
23 Express Program Address Region 4
XPEA4 Qualifies the address field (XPEADDR) to the region.
If XPEA4 = 1, the XPEADDR field maps to that region. See the description of the XPEADDR field for more
information. If this region is not present, the field is locked to 0.
0b - Address accessed is not from region 4
1b - Address accessed is from region 4
22 Express Program Address Region 3
XPEA3 Qualifies the address field (XPEADDR) to the region.
Table continues on the next page...

---

*Page 534*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
If XPEA3 = 1, the XPEADDR field maps to that region. See the description of the XPEADDR field for more
information. If this region is not present, the field is locked to 0.
0b - Address accessed is not from region 3
1b - Address accessed is from region 3
21 Express Program Address Region 2
XPEA2 Qualifies the address field (XPEADDR) to the region.
If XPEA2 = 1, the XPEADDR field maps to that region. See the description of the XPEADDR field for more
information. If this region is not present, the field is locked to 0.
0b - Address accessed is not from region 2
1b - Address accessed is from region 2
20 Express Program Address Region 1
XPEA1 Qualifies the address field (XPEADDR) to the region.
If XPEA1 = 1, the XPEADDR field maps to that region. See the description of the XPEADDR field for more
information. If this region is not present, the field is locked to 0.
0b - Address accessed is not from region 1
1b - Address accessed is from region 1
19 Express Program Address Region 0
XPEA0 Qualifies the address field (XPEADDR) to the region.
If XPEA0 = 1, the PEADDR field maps to that region. See the description of the XPEADDR field for more
information. If this region is not present, the field is locked to 0.
0b - Address accessed is not from region 0
1b - Address accessed is from region 0
18-5 Express Program Address
XPEADDR The XPEADDR register provides offset address location to be programmed. This address is always a quad
page address that selects 1024 bits.
The XPEADDR register is read only in user mode and represents the flash physical address status.
XPEADDR may be updated once (and only once) per Express Program event. XPEADDR writes initiate an
Express Program request.
XPEADDR may be updated four times between reset events. Upon completing the fourth Express Program
request (clearing of XPGM register), the XPEADDR register will return to all zeros, and remain locked from
future XPEADDR updates and thus future Express Program requests will be ignored until the next reset.
Once XPEADDR is updated, it will be locked for the full express program operation, until XMCR[XPGM] is
cleared at the end of the operation.
Writing of XPEADDR has the effect of blocking other registers used for program and erase to be written
(PEADR/MCR registers as well as APEADR/APDATA/AMCR registers) until the express program operation
is completed.
Table continues on the next page...

---

*Page 535*

Embedded Flash Memory (c40asf)
Table continued from the previous page...
Field Function
Upon completion of an Express Program operation (XMCR[XPGM] written to 0) the XPEADR registers will
return to an all zeros state.
When UT0[UTE] is set or XMCR[XEHV] is set, XPEADDR bits will not be writeable.
Once XPEADDR is updated, it will have read restrictions based on the PEID master that did the original write
in the Platform Flash Controller.
XPEADDR[18:5] is an offset from a base address of 0h for each block region. The XPEA0, XPEA1, XPEA2,
XPEA3, XPEA4 and XPEA5 qualify the block size region the XPEADDR field.
4-0 Reserved
— Reserved; reset to 0
20.7.1.26 Program Data (DATA0 - DATA31)
Offset
For a = 0 to 31:
Register Offset
DATAa 100h + (a × 4h)
Function
Enables data to be provided for program (or express program). This set of 32 data registers enables 1024 bits of data to
be programmed.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
PDATA
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PDATA
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31-0 Program Data
Table continues on the next page...

---

*Page 536*

Embedded Flash Memory (c40asf)
Field Function
PDATA PDATA registers will reset to all ones.
PDATA is not writable before PEADR is written or updated to start a program, express program, erase or
UTest operation, and will read a value of all ones.
Once PEADR is written, all PDATA registers will be made available for writing. Writing (and reading) of
the PDATA registers will be restricted to the master that updated the PEADR register as captured in the
PEID register.
NOTE
PDATA restrictions related to PEID will be disabled if UT0[UTE] is set.
NOTE
For Express Program a similar behavior exists on the PDATA registers. Once XPEADR is
written, and XDOK comes back as a 1, all PDATA registers will switch to all 1's (program
safe state) and be made available for writing (and reading) to the master's ID that matches
XPEID captured during the XPEADR update. In the event of an interrupted operation,
XPEID permissions will override PEID permissions until the express program operation is
completed (XPGM written to a 0).
Upon completion of an operation (Program (MCR[PGM] written to 0 and MCRS[PES] equals 0), Erase
(MCR[ERS] written to 0 and MCRS[PES] equals 0), Express Program (XMCR[XPGM] written to 0), Array
Integrity (UT0[AIE] written to 0), User Margin Read(UT0[AIE] written to 0)) or Program and Erase Sequence
clear (MCRS[PES] written to 0) the PDATA registers will return to an all ones state.
When UT0[AIE] is set, PDATA bits will not be writeable.
When MCRS[PES] is set, PDATA bits will not be writeable except when an express program request occurs
and XMCR[XDOK] is set.
Also, when MCR[PGM] or MCR[ERS] are set, PDATA bits will not be writeable except when an express
program request occurs and XMCR[XDOK] is set. When an express program operation is in process,
PDATA bits will not be writeable once XMCR[XEHV] is set until XMCR[XPGM] is cleared.
Only PDATA Bits that are written will receive program or express program pulses.

#### 20.8 Glossary

Abort Premature end of a mode, sequence, state or operation. An abort may leave the embedded flash
memory in a state in which it contains indeterminate or corrupted data.
BIU Bus interface unit(Contains all system-level customization required to make the embedded flash memory
part of an SoC)
Block Subdivision of the Flash Module containing NVM memory bits. Block sizes range from 256KB to 2MB.
Each block is made up of 8KB sectors.
Double word Two words, or 64 bits.
ECC Error correction code(Internally used to correct single bit errors, or detect double errors within a 64-bit
double word.)
ECC segment 64 bit double word, representing the size of data used to calculate ECC information.
EDC Error detection code
FC Flash memory core

---

*Page 537*

Embedded Flash Memory (c40asf)
FMU Flash Management Unit - Logic that enables program, erase, read, and other flash functions.
Interlock Write A write to the PEADR register followed by a write to any DATA register performed while initiating a
program, erase, array integrity, or user margin read sequence. Interlock in cleared once the operation is
finished by clearing the appropriate control bit, causing PEADR register to clear.
MCU Microcontroller unit
Module Flash memory instance that contains flash block(s), FMU, and required analog support structures.
NVM Nonvolatile memory
OTP One time programmable
PFC Platform flash memory controller(Contains all system-level customization required to make the
embedded flash memory part of an SoC)
Page 8 words of data (256 bits). When doing reads to the embedded flash memory, this is the width of data
read. Up to 4 pages can be programmed at a time.
Partition Subdivision of the Flash Module. Used for read-while-write operation, where addresses in the partition
being written may not be read. Partitions is always equal to 1 block, and contains multiple sectors.
RWW Read-while-write (operation)
Sector Subdivision of the Flash Block that is independently erasable. Sector Size is always 8 KB.
Super sector Subdivision of the flash block that includes a group of sectors. Super Sector Size is always 64 KB, and
consists of 8 sectors.
SoC System-on-chip
UTest NVM sector 8 KB sector outside the main address space
Word 32 bits

---

*Page 538*

