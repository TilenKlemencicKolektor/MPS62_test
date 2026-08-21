<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 21 -->

# Chapter 21

# Flash Memory Controller (PFLASH)

#### 21.1 Chip-specific PFLASH information

#### 21.1.1 Flash memory architecture

The flash memory on the chip consists of a flash memory controller and a flash memory array module. The flash memory controller
provides flash–memory configuration and control functions and manages the interface between the flash memory array and the
chip's crossbar switch.
This chip implements three 64-bit AHB buses.
Port 0 Port 1 Port 2*
All other
Core 0 Core 1
masters
256 + 32 bit 256 + 32 bit 256 + 32 bit
buffer buffer buffer
256 + 32 bit 256 + 32 bit 256 + 32 bit
buffer buffer buffer
256 + 32 bit 256 + 32 bit 256 + 32 bit
buffer buffer buffer
256 + 32 bit 256 + 32 bit 256 + 32 bit
buffer buffer buffer
Flash memory array
* This port does not exist on MCXE315/MCXE316/MCXE317
Figure 46. Flash memory architecture

#### 21.1.2 Flash memory controller

The flash memory controller:
• Acts as an interface between the system bus and flash memory array.
• Is a triple-ported controller with a dedicated line buffering per port and per master ID. This enables you to use the line buffers
more efficiently because various masters have dedicated buffers that are not compromised when other masters perform
read operations.
Also, by having separate ports, you can have separate connections for each CPU instruction bus and a single port for all data
accesses as shown in Figure 46 .
In general, the flash memory controller registers affect the global flash memory behavior (for example, read buffering and
access control).
NOTE
In MCXE317:
• PFLASH block 3 and block 4 are not present, so both the sector and super sector registers are not available.
• For PFLASH block 2, the super sector registers are not available.

---

*Page 539*

Flash Memory Controller (PFLASH)

#### 21.1.3 Platform flash configuration registers (PFCR n )

The table below defines the PFCR n fields that the chip uses.
Table 93. Platform flash configuration registers (PFCR n )
Master Buffer enable field Prefetch enable field
Cortex–M7_0 P0_CBFEN, P0_DBFEN P0_CPFEN, P0_DPFEN
1
eDMA + ELE_HSEB + EMAC P1_CBFEN, P1_DBFEN P1_CPFEN, P1_DPFEN
1. MCXE315/MCXE316 and MCXE317 do not support EMAC.

#### 21.1.4 Platform flash access protection register (PFAPR)

This table defines the PFAPR[M n AP] fields that the chip uses. This chip does not use the other master access protection fields,
but those fields are readable and writable.
Table 94. Platform flash access protection register (PFAPR)
Master number Master name Access protection field
0 Cortex–M7_0 M0AP
2 eDMA M2AP
3 ELE_HSEB M3AP
4 EMAC M4AP
5 Reserved
6
7

#### 21.2 Overview

PFLASH acts as an interface between the system bus (AHB-Lite 2.v6) and flash memory array.
PFLASH supports three 64-bit AHB buses and a 256-bit read data interface from each flash memory array. The slave port
assignments and buffer organization are organized to offer maximum performance of code execution in a multicore architecture.
The buffer mechanism serves to deliver flash memory read data with zero-wait state response on lines that reside in cache.
AHB requests that miss the prefetch cache generate the needed flash memory array access and are forwarded to the AHB upon
completion. Each cache entry is 256 bits, matching the flash memory array page size and providing 512 bytes of high-speed
local storage.

#### 21.2.1 Block diagrams

The following figure provides a block diagram showing PFLASH and the attached flash memory array.

---

*Page 540*

Flash Memory Controller (PFLASH)
Arm Cortex-M
processor
Flash
PFLASH
memory
AMBA-AHB AXBS
System
Memory
RAM
Figure 47. Platform-centric simple block diagram with PFLASH

#### 21.2.2 Features

• Two 64-bit AHB interface ports ( p0, p1 ) allowing simultaneous access to dedicated prefetch mini-cache per slave port
• 256-bit read data bus and 64-bit write data bus
• Configurable read buffering and line prefetching support via a mini-cache, plus a prefetch controller for each AHB port to
provide single-cycle buffer hit read response
• Configurable access control based on read/write and AHB master ID attributes
• Support for reporting single-bit and multi-bit flash memory ECC events on a 64-bit doubleword boundary

#### 21.3 Functional description

As shown in Figure 47 , PFLASH interfaces between:
• The AHB system bus port s
• The flash memory array
For accesses targeting flash memory, the PFLASH generates as inputs to the flash memory array:
• Read and write enables
• Block selects
• Array address
• Write size
• Write data
PFLASH captures read data from the flash memory array and drives it onto the AHB system bus. Up to four pages of data (256-bit
page size) may be buffered in each prefetch buffer for AHB Port0 and Port1 . Lines may be prefetched in advance of being
requested, allowing single-cycle (zero AHB wait-states) read data responses on buffer hits.
Access protections may be applied on a per-master basis for both reads and writes to support security and privilege mechanisms.

#### 21.3.1 Read transactions

On an incoming AHB read request, a mini-cache lookup and access privilege evaluation are performed during the AHB address
phase. If a buffer hit occurs, the requested data is retrieved from the previously loaded prefetch buffer entry and returned on the
system bus with a zero wait-state response. If a buffer miss occurs, a flash memory access is initiated.

---

*Page 541*

Flash Memory Controller (PFLASH)
Read accesses are terminated under control of the appropriate wait state settings. Access timing can be varied to account for the
operating conditions of the chip (for example, frequency, voltage, temperature, and so on) by appropriately setting the read wait
state field in flash memory.

#### 21.3.2 Write transactions

An interlock write on a program or erase sequence is initiated by first writing to Platform Flash Memory Program Erase Address
Logical (PFCPGM_PEADR_L) , Platform Flash Memory Express Program Erase Address Logical (PFCPGM_XPEADR_L) (see the
Flash Memory chapter for write sequence details).

#### 21.3.3 Access protections

21.3.3.1 PFAPR
PFLASH provides programmable, configurable access protections for read cycles on a per-master basis in PFAPR[M n AP] . This
field restricts read access on a per-master basis. This functionality is described in Platform Flash Memory Access Protection
(PFAPR) . Detection of a protection violation based on PFAPR settings results in an error response from PFLASH on the
AHB transfer.

#### 21.3.4 Error termination

PFLASH can invoke a system bus error termination in the following scenarios:
• Access privilege violation (see Access protections for details)
• Attempted access by an AHB master to a reserved region in the flash memory map
• Multi-bit ECC error detection on AHB read, and PFCR4[DMEEE] = 0 (for data flash memory, it is further qualified by
PFCR4[DERR_SUP] = 0)

#### 21.3.5 Line read buffers and prefetch operation

The PFLASH AHB ports of the each contain a mini-cache. PFLASH uses the buffers for both prefetch and normal demand
fetches. Also, the buffers are shared for code and data fetches, and can be controlled independently for code and data from
control registers.
Prefetch triggering is controllable on a per-port basis. A PFLASH read access may trigger a prefetch to the next sequential line of
array data on the cycle following the request. The access address is incremented by 32 bytes, and a subsequent flash memory
access is initiated. A flash memory array prefetch is initiated if the data is not already resident in a line read buffer. Prefetched data
is always loaded into the least-recently-used buffer.
For Port0 and Port1 there are four line buffer entries in their respective prefetch mini-caches that follow a fully associative,
least-recently-used replacement policy.
For prefetching to occur, you must set the following configuration fields, where n corresponds to the port number and m
corresponds to C (code) or D (data):
PFCRn[Pn_mBFEN] = 1 and PFCRn[Pn_mPFEN] = 1

#### 21.3.6 Array integrity considerations

During an array integrity sequence, the flash memory array ignores any incoming read requests. When a flash memory array
integrity check is in progress, PFLASH terminates all flash memory access requests with an error. More specifically, it aborts the
incoming flash memory access requests and terminates the system bus transfer with an error.

---

*Page 542*

Flash Memory Controller (PFLASH)

#### 21.3.7 Safety considerations

21.3.7.1 Flash memory address generation check
Functional safety coverage of the address path and control within PFLASH rely on a feedback path between PFLASH and flash
memory. Remember that on a requested access to flash memory, PFLASH must decode the system AHB bus signals to generate
the corresponding flash memory interface signals to invoke a flash memory lookup. In addition to providing the requested read
data, the flash memory also provides output sidebands reflecting the encoded address and block selects used to perform the
actual row lookup.
PFLASH uses this sideband information to verify the expected transaction. If a mismatch is detected, indicating a failure in the
address generation or control logic within PFLASH or the transmission path between PFLASH and the flash memory array, then
the event is forwarded to the chip fault collection module and the corresponding buffer is invalidated.

#### 21.3.8 ECC error handling on data flash block

When PFCR4[DERR_SUP] is enabled, ECC errors on data flash blocks are handled specially.
If there is a noncorrectable error detection, a fixed, illegal opcode value is returned to the requesting master along with the
associated ECC checkbits as determined by the requesting address.
For noncorrectable error detection, PFLASH returns a value of 1555_1555h to the requesting master.
This is mainly used for EEPROM emulation applications.

#### 21.3.9 Read cycles—buffer miss

On an incoming AHB read request, a mini-cache lookup and access privilege evaluation are performed during the AHB address
phase. If a buffer miss occurs, a flash memory access is initiated.
If the flash memory access was the direct result of an AHB transaction, the corresponding page buffer is loaded and marked as the
most-recently-used. If the flash memory access was the result of a speculative prefetch to the next sequential line, it is loaded into
the least-recently-used buffer. The status of this buffer is not changed to most-recently-used until a subsequent buffer hit occurs
as a result of an AHB read request.

#### 21.3.10 Read cycles—buffer hit

PFLASH allows single-cycle read responses to the AHB when the requested read access was previously loaded into one of the
page buffers. In these cases of a buffer hit, read data is returned on the system bus with a zero wait-state response.

#### 21.3.11 Flash memory error response operation

The flash memory array may signal an error response to terminate a requested access because of improper sequencing during
program/erase operations and improper sequencing during array integrity testing. When an error response is received, PFLASH
does not update or validate a page read buffer. An error response may be signaled on a read or interlock write operation. For more
information on the specifics related to signaling of flash memory errors, including flash memory ECC events, array integrity testing,
and read-while-write events, see the flash memory chapter.

#### 21.3.12 Clocking

This module has no clocking considerations.

#### 21.3.13 Interrupts

This module has no interrupts.

#### 21.4 External signals

This module has no external signals.

---

*Page 543*

Flash Memory Controller (PFLASH)

#### 21.5 PFLASH register descriptions

PFLASH provides an IPS programming model mapped to a standard 16 KB on-platform peripheral slot. The programming model
consists of flash memory access configuration.
You can reference the programming model only by using a 32-bit (word) access. References that are attempted using different
access sizes, or to undefined (reserved) addresses, or in User mode generate an IPS error termination. PFLASH allows access
to the programming model by all system bus masters.
Write to read only registers don't generate error termination.
You can only access the programming model in Supervisor mode, except *PEADR* registers which can be accessed in Supervisor
or User mode.
Attempted updates to the programming model when PFLASH is in the middle of an operation results in non-deterministic behavior.
You must architect software to avoid this scenario. The recommended flow for multicore devices is:
1. Start only one core.
2. Execute initialization code until it is complete.
3. Start the remaining cores.
If you need to reconfigure the flash memory, code execution must be temporarily moved to system RAM.

#### 21.5.1 PFLASH memory map

PFLASH base address: 4026_8000h
Offset Register Access Reset value
Width
(In bits)
0h Platform Flash Memory Configuration 0 (PFCR0) 32 RW 0000_0003h
4h Platform Flash Memory Configuration 1 (PFCR1) 32 RW 0000_0003h
10h Platform Flash Memory Configuration 4 (PFCR4) 32 RW 0000_0000h
14h Platform Flash Memory Access Protection (PFAPR) 32 RW FFFF_FFFFh
300h Platform Flash Memory Program Erase Address Logical 32 RW 0000_0000h
(PFCPGM_PEADR_L)
304h Platform Flash Memory Program Erase Address Physical 32 R 0000_0000h
(PFCPGM_PEADR_P)
308h Platform Flash Memory Express Program Erase Address Logical 32 RW 0000_0000h
(PFCPGM_XPEADR_L)
30Ch Platform Flash Memory Express Program Erase Address Physical 32 R 0000_0000h
(PFCPGM_XPEADR_P)
340h - 350h Block n Sector Program Erase Lock (PFCBLK0_SPELOCK - 32 RW FFFF_FFFFh
PFCBLK4_SPELOCK)
358h Block UTEST Sector Program Erase Lock (PFCBLKU_SPELOCK) 32 RW 0000_0001h
35Ch - 368h Block n Super Sector Program Erase Lock (PFCBLK0_SSPELOCK - 32 RW 0000_0FFFh
PFCBLK3_SSPELOCK)
380h - 390h Block n Set Sector Lock (PFCBLK0_SETSLOCK - 32 RW 0000_0000h
PFCBLK4_SETSLOCK)
Table continues on the next page...

---

*Page 544*

Flash Memory Controller (PFLASH)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
398h Block UTEST Set Sector Lock (PFCBLKU_SETSLOCK) 32 RW 0000_0000h
39Ch - 3A8h Block n Set Super Sector Lock (PFCBLK0_SSETSLOCK - 32 RW 0000_0000h
PFCBLK3_SSETSLOCK)
3C0h - 45Ch Block a Lock Master Sector b (PFCBLK0_LOCKMASTER_S0 - 32 R FFFF_FFFFh
PFCBLK4_LOCKMASTER_S7)
480h Block UTEST Lock Master Sector (PFCBLKU_LOCKMASTER_S) 32 R 0000_00FFh
484h Block m Lock Master Super Sector n 32 R FFFF_FFFFh
(PFCBLK0_LOCKMASTER_SS0)
488h Block m Lock Master Super Sector n 32 R FFFF_FFFFh
(PFCBLK0_LOCKMASTER_SS1)
48Ch Block m Lock Master Super Sector n 32 R FFFF_FFFFh
(PFCBLK0_LOCKMASTER_SS2)
494h Block m Lock Master Super Sector n 32 R FFFF_FFFFh
(PFCBLK1_LOCKMASTER_SS0)
498h Block m Lock Master Super Sector n 32 R FFFF_FFFFh
(PFCBLK1_LOCKMASTER_SS1)
49Ch Block m Lock Master Super Sector n 32 R FFFF_FFFFh
(PFCBLK1_LOCKMASTER_SS2)
4A4h Block m Lock Master Super Sector n 32 R FFFF_FFFFh
(PFCBLK2_LOCKMASTER_SS0)
4A8h Block m Lock Master Super Sector n 32 R FFFF_FFFFh
(PFCBLK2_LOCKMASTER_SS1)
4ACh Block m Lock Master Super Sector n 32 R FFFF_FFFFh
(PFCBLK2_LOCKMASTER_SS2)
4B4h Block m Lock Master Super Sector n 32 R FFFF_FFFFh
(PFCBLK3_LOCKMASTER_SS0)
4B8h Block m Lock Master Super Sector n 32 R FFFF_FFFFh
(PFCBLK3_LOCKMASTER_SS1)
4BCh Block m Lock Master Super Sector n 32 R FFFF_FFFFh
(PFCBLK3_LOCKMASTER_SS2)

#### 21.5.2 Platform Flash Memory Configuration 0 (PFCR0)

Offset
Register Offset
PFCR0 0h

---

*Page 545*

Flash Memory Controller (PFLASH)
Function
Specifies the operation of PFLASH Port0.
See the chip-specific PFLASH information for details about the actual masters available on the chip.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
P0_DP P0_CP P0_DB P0_CB
FEN FEN FEN FEN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1
Fields
Field Function
Reserved
31-6
—
5 Port0 Data Prefetch Enable
P0_DPFEN Enables or disables data prefetching initiated by a read access on Port0. Prefetching can only be
enabled if the buffers are enabled by writing 1 to DBFEN . Hardware reset returns this field to 0.
0b - Disable
1b - Enable
4 Port0 Code Prefetch Enable
P0_CPFEN Enables or disables code prefetching initiated by a read access on Port0. Prefetching can only be
enabled if the buffers are enabled by writing 1 to CBFEN . Hardware reset returns this field to 0.
0b - Disable
1b - Enable
Reserved
3-2
—
1 Port0 PFLASH Line Read Data Buffers Enable
P0_DBFEN Enables or disables line read data buffer hits. It is also used to invalidate the buffers.
If this field is 0, the line read buffers are disabled from satisfying read requests, and all buffer valid bits are
set to 0. If this field is enabled, the line read buffers are enabled to satisfy read requests on hits. Buffer valid
bits may be set when the buffers are successfully filled.
Table continues on the next page...

---

*Page 546*

Flash Memory Controller (PFLASH)
Table continued from the previous page...
Field Function
0b - Disable
1b - Enable
0 Port0 PFLASH Line Read Code Buffers Enable
P0_CBFEN Enables or disables line read code buffer hits. It is also used to invalidate the buffers.
If disabled, the line read buffers are disabled from satisfying read requests, and all buffer valid bits are set
to 0. If enabled, the line read buffers are enabled to satisfy read requests on hits. Buffer valid bits may be
set when the buffers are successfully filled.
0b - Disable
1b - Enable

#### 21.5.3 Platform Flash Memory Configuration 1 (PFCR1)

Offset
Register Offset
PFCR1 4h
Function
Specifies the operation of PFLASH Port1.
See the chip-specific PFLASH information for details about the actual masters available on the chip.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
P1_DP P1_CP P1_DB P1_CB
FEN FEN FEN FEN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1
Fields
Field Function
Reserved
31-6
Table continues on the next page...

---

*Page 547*

Flash Memory Controller (PFLASH)
Table continued from the previous page...
Field Function
—
5 Port1 Data Prefetch Enable
P1_DPFEN Enables or disables data prefetching initiated by a read access on Port1. Prefetching can only be
enabled if the buffers are enabled by writing 1 to DBFEN . Hardware reset returns this field to 0.
0b - Disable
1b - Enable
4 Port1 Code Prefetch Enable
P1_CPFEN Enables or disables code prefetching initiated by a read access on Port1. Prefetching can only be
enabled if the buffers are enabled by writing 1 to CBFEN . Hardware reset returns this field to 0.
0b - Disable
1b - Enable
Reserved
3-2
—
1 Port1 PFLASH Line Read Data Buffers Enable
P1_DBFEN Enables or disables line read data buffer hits. It is also used to invalidate the buffers.
If this field is 0, the line read buffers are disabled from satisfying read requests, and all buffer valid bits are
set to 0. If this field is enabled, the line read buffers are enabled to satisfy read requests on hits. Buffer valid
bits may be set when the buffers are successfully filled.
0b - Disable
1b - Enable
0 Port1 PFLASH Line Read Code Buffers Enable
P1_CBFEN Enables or disables line read code buffer hits. It is also used to invalidate the buffers.
If disabled, the line read buffers are disabled from satisfying read requests, and all buffer valid bits are set
to 0. If enabled, the line read buffers are enabled to satisfy read requests on hits. Buffer valid bits may be
set when the buffers are successfully filled.
0b - Disable
1b - Enable

#### 21.5.4 Platform Flash Memory Configuration 4 (PFCR4)

Offset
Register Offset
PFCR4 10h

---

*Page 548*

Flash Memory Controller (PFLASH)
Function
Specifies operation of the flash memory controller buffer.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
DMEE DERR
BLK4_PS
E _SUP
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-8
—
7 Disable Multi-Bit ECC Error Exception
DMEEE Enables or disables system bus error response on multi-bit ECC error. Hardware reset returns this field to 0.
0b - Error response sent on system bus for multi-bit ECC error
1b - Error response not sent on system bus for multi-bit ECC error
Reserved
6-4
—
3-1 Block 4 Pipe Select
BLK4_PS Selects the active pipe for flash memory block 4 access.
PFLASH has four independent command pipes to issue four parallel read commands to different flash
memory blocks. Reads from flash memory block 0–3 are always done through command pipe 0–3,
respectively. However, the access to block 4 is not fixed and can be through any of the command pipes. You
must only change this field when there is no ongoing block 4 access.
A special round-robin arbitration scheme snoops the availability of a command pipe during block 4 access.
If any of the command pipes are idle during the first read request to block 4, the ownership of that command
pipe gets shared between block 4 and the respective block. If none of the command pipes are idle during a
block 4 read request, block 4 gets associated with each of the command pipes in round-robin fashion. When
a command pipe acquires ownership of block 4, it keeps that ownership until all the commands to block 4
from all the masters are served.
000b - Block 4 access is always through pipe0
001b - Block 4 access is always through pipe1
Table continues on the next page...

---

*Page 549*

Flash Memory Controller (PFLASH)
Table continued from the previous page...
Field Function
010b - Block 4 access is always through pipe2
011b - Block 4 access is always through pipe3
1xxb - Block 4 access can be through any of the command pipes, based on which command pipe
is available for block 4 access
0 Data Error Suppression
DERR_SUP See the Embedded Flash Memory configuration information or system memory map for which flash
memory blocks are affected by this field.
0b - Reports ECC events on data flash memory accesses
1b - Single-bit and multi-bit ECC events on data flash memory accesses are suppressed

#### 21.5.5 Platform Flash Memory Access Protection (PFAPR)

Offset
Register Offset
PFAPR 14h
Function
Controls read accesses to the flash memory array.
See the chip-specific PFLASH information for details about the actual masters available on the chip.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
M0AP M1AP M2AP M3AP M4AP M5AP M6AP M7AP
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
M8AP M9AP M10AP M11AP M12AP M13AP M14AP M15AP
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31-30: M0AP Master n Access Protection
Table continues on the next page...

---

*Page 550*

Flash Memory Controller (PFLASH)
Field Function
Controls whether read accesses to the flash memory are allowed based on the master ID of a requesting
29-28: M1AP
master. These fields are initialized by hardware reset. The field M3AP is reserved.
27-26: M2AP
x0b - This master cannot perform any read accesses
25-24: M3AP
x1b - This master can perform read accesses
23-22: M4AP
21-20: M5AP
19-18: M6AP
17-16: M7AP
15-14: M8AP
13-12: M9AP
11-10: M10AP
9-8: M11AP
7-6: M12AP
5-4: M13AP
3-2: M14AP
1-0: M15AP

#### 21.5.6 Platform Flash Memory Program Erase Address Logical (PFCPGM_PEADR_L)

Offset
Register Offset
PFCPGM_PEADR_L 300h
Function
Provides the flash memory address to be programmed, or the location of the sector or block to be erased through main flash
memory (pgm/erase) queue. Write access to this register is domain-ID aware.
The respective bus master must have program/erase permission to the flash memory address written to this register. Otherwise
a transfer error results. For further information on flash memory address restrictions see the XRDC chapter.
A write to this register is managed via three-cycle access. Before updating the register, you must ensure that no ongoing
high-voltage operation is executing through the flash memory main queue.
Unauthorized flash memory address writes result in a transfer error.
Writes to this register during an ongoing high-voltage operation (initiated through the flash memory main queue) or during express
program operation are ignored.

---

*Page 551*

Flash Memory Controller (PFLASH)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
PEADR_L
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PEADR_L
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Program Erase Address Logical
PEADR_L Contains the system logical address for flash memory program/erase.

#### 21.5.7 Platform Flash Memory Program Erase Address Physical (PFCPGM_PEADR_P)

Offset
Register Offset
PFCPGM_PEADR_P 304h
Function
Reflects the flash memory block number and offset address corresponding to Platform Flash Memory Program Erase Address
Logical (PFCPGM_PEADR_L) . This register has the same format as the PEADR register in the Flash Memory chapter—see it
for details.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R PEADR_P
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R PEADR_P
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 552*

Flash Memory Controller (PFLASH)
Fields
Field Function
31-0 Program Erase Address Physical
PEADR_P Contains the flash block select and offset address for flash memory program/erase.

#### 21.5.8 Platform Flash Memory Express Program Erase Address Logical (PFCPGM_XPEADR_L)

Offset
Register Offset
PFCPGM_XPEADR_L 308h
Function
Provides the flash memory address to be programmed using the flash memory express program feature. Write access to this
register is domain-ID aware.
The respective bus master must have program/erase permission to the flash memory address written to this register. Otherwise
a transfer error results. See the XRDC chapter for further information on flash memory address restrictions.
A write to this register is managed via three-cycle access. Before updating the register, you must ensure that no ongoing
high-voltage operation is executing through the flash memory main queue.
Unauthorized flash memory address writes result in a transfer error.
Writes to this register during an ongoing express program operation are ignored.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
XPEADR_L
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
XPEADR_L
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Express Program Erase Address Logical
XPEADR_L Contains the system logical address for express flash program/erase.

---

*Page 553*

Flash Memory Controller (PFLASH)

#### 21.5.9 Platform Flash Memory Express Program Erase Address Physical (PFCPGM_XPEADR_P)

Offset
Register Offset
PFCPGM_XPEADR_P 30Ch
Function
Reflects the flash memory block number and offset address corresponding to PFCPGM_XPEADR_L . This register has the same
format as the XPEADR register in the Flash Memory chapter—see it for details.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R XPEADR_P
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R XPEADR_P
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Express Program Erase Address Physical
XPEADR_P Contains the flash memory block select and offset address for flash memory express program/erase.

#### 21.5.10 Block n Sector Program Erase Lock (PFCBLK0_SPELOCK - PFCBLK4_SPELOCK)

Offset
Register Offset
PFCBLK0_SPELOCK 340h
PFCBLK1_SPELOCK 344h
PFCBLK2_SPELOCK 348h
PFCBLK3_SPELOCK 34Ch
PFCBLK4_SPELOCK 350h

---

*Page 554*

Flash Memory Controller (PFLASH)
Function
Provides a way to protect sectors from being modified. Sector protection is available on the last 256 KB of every block (for 256
KB blocks, all sectors are available for protection). Each lock bit can be associated with a particular domain ID by writing 1 to
the appropriate bit in PFCBLKn_SETSLOCK[SETSLCK]. After the lock is acquired, only a master having the same domain ID
can modify the corresponding lock bit. If the corresponding PFCBLKn_SETSLOCK[SETSLCK] bit is not equal to 1, any master
can modify the appropriate SLCK bit. If a lock bit is already acquired by a particular domain ID, any effort to modify (1 to 0, or 0
to 1) the lock bit by a master with a different domain ID results in a transfer error.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SLCK
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SLCK
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31-0 Sector Lock
SLCK Locks selected sector. If vector bit value = 0, the sector is available for program and erase operations. If
vector bit value = 1, the sector is locked and not available for program and erase operations.

#### 21.5.11 Block UTEST Sector Program Erase Lock (PFCBLKU_SPELOCK)

Offset
Register Offset
PFCBLKU_SPELOCK 358h
Function
Provides a way to protect sectors from being modified. Sector protection is available on the last 256 KB of every block (for
256 KB blocks, all sectors are available for protection). Each lock bit can be associated with a particular domain ID by writing
1 to PFCBLKU_SETSLOCK[SETSLCK]. After the lock is acquired, only a master having the same domain ID can modify the
corresponding lock bit. If the corresponding PFCBLKU_SETSLOCK[SETSLCK] bit is not equal to 1, any master can modify the
SLCK bit. If a lock bit is already acquired by a particular domain ID, any effort to modify (1 to 0, or 0 to 1) the lock bit by a
master with a different domain ID results in a transfer error.

---

*Page 555*

Flash Memory Controller (PFLASH)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
SLCK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1
Fields
Field Function
31-1 Reserved
—
0 Sector Lock
SLCK Locks selected sector. If vector bit value = 0, the sector is available for program and erase operations. If
vector bit value = 1, the sector is locked and not available for program and erase operations.

#### 21.5.12 Block n Super Sector Program Erase Lock (PFCBLK0_SSPELOCK - PFCBLK3_SSPELOCK)

Offset
Register Offset
PFCBLK0_SSPELOCK 35Ch
PFCBLK1_SSPELOCK 360h
PFCBLK2_SSPELOCK 364h
PFCBLK3_SSPELOCK 368h
Function
Provides a way to protect super sectors from being modified. Super sector protection is available on block space larger
than 256 KB. For 256 KB blocks, this register is not applicable. For 512 KB blocks, the first half of the block is protected
with super sector granularity. For 1 MB blocks, the first 768 KB is protected with super sector granularity. Each lock bit can
be associated with a particular domain ID by writing 1 to the appropriate bit in PFCBLKn_SSETSLOCK[SSETSLCK]. After
the lock is acquired, only a master having the same domain ID can modify the corresponding lock bit. If the corresponding
PFCBLKn_SSETSLOCK[SSETSLCK] bit is not equal to 1, any master can modify the SSLCK bit. If a lock bit is already
acquired by a particular domain ID, any effort to modify (1 to 0, or 0 to 1) the lock bit by a master with a different domain ID
results in a transfer error.

---

*Page 556*

Flash Memory Controller (PFLASH)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
SSLCK
W
Reset 0 0 0 0 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31-12 Reserved
—
11-0 Super Sector Lock
SSLCK Locks selected super sector. If vector bit value = 0, the super sector is available for program and
erase operations. If vector bit value = 1, the super sector is locked and not available for program and
erase operations.

#### 21.5.13 Block n Set Sector Lock (PFCBLK0_SETSLOCK - PFCBLK4_SETSLOCK)

Offset
Register Offset
PFCBLK0_SETSLOCK 380h
PFCBLK1_SETSLOCK 384h
PFCBLK2_SETSLOCK 388h
PFCBLK3_SETSLOCK 38Ch
PFCBLK4_SETSLOCK 390h
Function
Provides a mechanism for the masters to gain the ownership of the corresponding PFCBLKn_SPELOCK lock bit based on
domain id . After it is equal to 1, the bit is returned to 0 at next reset. If any SETSLOCK bit is not equal to 1, the corresponding
LOCK bit can be modified by any master. If a bit is already acquired by a particular domain ID, any effort to modify the lock bit
by a master with a different domain ID is ignored without transfer error.

---

*Page 557*

Flash Memory Controller (PFLASH)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SETSLCK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SETSLCK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 If the vector bit value = 0, the corresponding lock bit is not owned by any master. If the vector bit value = 1,
the lock bit is owned by the masters having the domain ID stored in PFCBLKn_LOCKMASTER_Sm .
SETSLCK

#### 21.5.14 Block UTEST Set Sector Lock (PFCBLKU_SETSLOCK)

Offset
Register Offset
PFCBLKU_SETSLOCK 398h
Function
Provides a mechanism for the masters to gain ownership of the corresponding PFCBLKU_SPELOCK lock bit based on
domain id. After it is equal to 1, the bit is returned to 0 at next reset. If any SETSLOCK bit is not equal to 1, the corresponding
LOCK bit can be modified by any master. If a bit is already acquired by a particular domain ID, any effort to modify the lock bit
by a master with a different domain ID is ignored without transfer error.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
SETSL
CK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 558*

Flash Memory Controller (PFLASH)
Fields
Field Function
31-1 Reserved
—
0 Set Sector Lock
SETSLCK Locks selected sector. If vector bit value = 0, the corresponding lock bit is not owned by any
master. If vector bit value = 1, the lock bit is owned by the masters having the domain ID stored
in PFCBLKn_LOCKMASTER_SSm .

#### 21.5.15 Block n Set Super Sector Lock (PFCBLK0_SSETSLOCK - PFCBLK3_SSETSLOCK)

Offset
Register Offset
PFCBLK0_SSETSLOCK 39Ch
PFCBLK1_SSETSLOCK 3A0h
PFCBLK2_SSETSLOCK 3A4h
PFCBLK3_SSETSLOCK 3A8h
Function
Provides a mechanism for the masters to gain ownership of the corresponding PFCBLKn_SPELOCK lock bit based on domain
id. After it is equal to 1, the bit is returned to 0 at next reset. If any SSETSLOCK bit is not equal to 1, the corresponding LOCK
bit can be modified by any master. If a bit is already acquired by a particular domain ID, any effort to modify the lock bit by a
master with a different domain ID is ignored without transfer error.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
SSETSLCK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 559*

Flash Memory Controller (PFLASH)
Fields
Field Function
31-12 Reserved
—
11-0 Set Super Sector Lock
SSETSLCK Locks selected super sector. If vector bit value = 0, the corresponding lock bit is not owned by any master. If
vector bit value = 1, the lock bit is owned by the masters having the domain ID stored in Block a Lock Master
Sector b (PFCBLK0_LOCKMASTER_S0 - PFCBLK4_LOCKMASTER_S7) .

#### 21.5.16 Block a Lock Master Sector b (PFCBLK0_LOCKMASTER_S0 -

#### PFCBLK4_LOCKMASTER_S7)

Offset
For a = 0 to 4; b = 0 to 7:
Register Offset
PFCBLKa_LOCKMASTE 3C0h + (a × 20h) + (b × 4h)
R_Sb
Function
Provides the domain ID information of the master currently acquiring the lock bit. The domain ID is represented by an 8-bit
field. This is a read-only register.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R LOCKMASTER_S
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R LOCKMASTER_S
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31-0 Block a Lock Master Sector b
Contains domain ID of the master currently acquiring the lock bit.
Table continues on the next page...

---

*Page 560*

Flash Memory Controller (PFLASH)
Field Function
LOCKMASTER PFCBLK0_LOCKMASTER_S0[LOCKMASTER_S[7:0]] holds the domain ID information
_S of PFCBLK0_SPELOCK[0].
PFCBLK0_LOCKMASTER_S0[LOCKMASTER_S[15:8]] holds the domain ID information of
PFCBLK0_SPELOCK[1], and so on in incremental order.

#### 21.5.17 Block UTEST Lock Master Sector (PFCBLKU_LOCKMASTER_S)

Offset
Register Offset
PFCBLKU_LOCKMAST 480h
ER_S
Function
Provides the domain ID information of the master currently acquiring the lock bit. The domain ID is represented by an 8-bit
field. This is a read-only register.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 LOCKMASTER_S
W
Reset 0 0 0 0 0 0 0 0 1 1 1 1 1 1 1 1
Fields
Field Function
31-8 Reserved
—
7-0 Lock Master Sector
LOCKMASTER Contains domain ID of the master currently acquiring the lock bit.
_S
PFCBLKU_LOCKMASTER_S[LOCKMASTER_S[7:0]] holds the domain ID information
of PFCBLKU_SPELOCK[0].

---

*Page 561*

Flash Memory Controller (PFLASH)

#### 21.5.18 Block m Lock Master Super Sector n (PFCBLK0_LOCKMASTER_SS0 -

#### PFCBLK3_LOCKMASTER_SS2)

Offset
For a = 0 to 3; b = 0 to 2:
Register Offset
PFCBLKa_LOCKMASTE 484h + (a × 10h) + (b × 4h)
R_SSb
Function
Provides the domain ID information of the master currently acquiring the lock bit. The domain ID is represented by an 8-bit
field. This is a read-only register.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R LOCKMASTER_SS
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R LOCKMASTER_SS
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31-0 Block a Lock Master Super Sector b
LOCKMASTER Contains domain ID of the master currently acquiring the lock bit.
_SS
PFCBLK0_LOCKMASTER_SS0[LOCKMASTER_SS[7:0]] holds the domain ID information
of PFCBLK0_SSPELOCK[0].
PFCBLK0_LOCKMASTER_SS0[LOCKMASTER_SS[15:8]] holds the domain ID information of
PFCBLK0_SSPELOCK[1], and so on in incremental order.

#### 21.6 Glossary

AHB Advanced high-performance bus
ECC Error correcting code
HSE Hardware security engine
IPS Internal peripheral system
ID Identification

---

*Page 562*

