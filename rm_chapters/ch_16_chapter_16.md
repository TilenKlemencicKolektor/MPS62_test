<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 16 -->

# Chapter 16

# Semaphores2 (SEMA42)

#### 16.1 Chip-specific SEMA42 information

#### 16.1.1 SEMA42 instance and configuration

This chip has up to one instance of SEMA42. See the following table for availability of SEMA42 instance across MCXE31
product series.
Table 51. SEMA42 instance
Instance MCXE31B
SEMA42 Yes
See "Chip-specific XRDC information" for the domain ID of each master on the MCXE31 product series. The following table shows
the number of domains per chip:

#### 16.2 Overview

SEMA42 is a memory-mapped module that provides robust hardware support needed in multi-core systems for implementing
semaphores and provides a simple mechanism to achieve "lock and unlock" operations via a single - write access. The hardware
semaphore module provides hardware-enforced gates as well as other useful system functions related to the gating mechanisms.
NOTE
Terminology in this chapter has been updated as follows:
Table 52. Updated terms
Updated term Deprecated term
Initiator Master
Target Slave
Stop Abort

---

*Page 375*

Semaphores2 (SEMA42)

#### 16.2.1 Block diagram

4 did
= =
Peripheral write data
32 Peripheral bus wdata
= = = Peripheral address
Decode
Control
Gate 0 Gate 1 Gate 2 Gate 3
Register blocks with finite
state machine that implements
the semaphore gates
Gate ( n -4) Gate ( n -3) Gate ( n -2) Gate ( n -1)
Mux
32 Peripheral rdata
Peripheral bus
Figure 35. SEMA42 block diagram

#### 16.2.2 Features

SEMA42 implements hardware-enforced semaphores as an IPS -mapped target peripheral device. The feature set includes:
• Support for 16 hardware-enforced gates in a multi-domain configuration that supports up to 15 domains.
• Each hardware gate appears as a 16-state, 4-bit state machine.
• Support for secure reset mechanisms to clear the contents of individual gates, as well as a clear-all capability.
• Memory-mapped target peripheral that offers programming-model accesses.

#### 16.3 Functional description

The intent of the hardware gate implementation is to specify a protocol where the locking domain must unlock the gate. However,
some systems may require a reset function to re-initialize the state of any gate(s) without requiring a system-level reset. To support
this special gate reset requirement, SEMA42 implements a secure reset mechanism that allows you to initialize a hardware gate
(or all the gates) by following a specific dual-write access pattern. The secure-gate reset:
• Uses a technique similar to that required for the servicing of a software watchdog timer
• Requires two consecutive writes with pre-defined data patterns from the same domain
You must do this to force the clearing of the specified gate(s). The required access pattern as follows:

---

*Page 376*

Semaphores2 (SEMA42)
1. A domain performs a 16-bit write to the RSTGT memory location. The most significant byte ( RSTGT_W[RSTGDP] )
must be E2h. The value of least significant byte is irrelevant for this reference and can be anything.
2. The same domain then performs a second 16-bit write to the RSTGT location. For this write, the upper
byte ( RSTGT_W[RSTGDP] ) is the logical complement of the first data pattern (1Dh) and the lower byte
( RSTGT_W[RSTGTN] ) specifies the gate(s) to be reset. This gate field can specify a single gate or all gates to be
cleared. If the same domain writes incorrect data on the second access or another domain performs the second write
access, SEMA42 stops the special gate reset sequence and does not assert an error signal.
3. Reads of the RSTGT location return information on the 2-bit reset state machine ( RSTGT_R[RSTGSM] ) that
implements these functions:
• The domain performing the reset ( RSTGT_R[RSTGMS] )
• The last-cleared gate number(s) ( RSTGT_R[RSTGTN] )
Reads of the RSTGT register do not affect the secure-reset finite state machine in any manner.

#### 16.3.1 Multi-core programming: software gates

Multi-processor systems require a function that can be used to safely and easily provide a locking mechanism for system software
to control access to shared data structures, shared hardware resources, and so on. The software uses the gating mechanisms to
serialize (and synchronize) accesses to shared data and/or resources to prevent race conditions and preserve memory coherency
between different processes and domains.
Consider the following description of a typical use-case: dm X enters a section of code, where shared data values are to be
updated. The domain must first acquire a semaphore. Think of this as the locking (or closing) of a software gate. After the gate
locks, a properly-architected software system does not allow other processes (or domains) to execute the same code segment
or modify the shared data structure protected by the gate. In other words, the system locks out other processes/domains. Many
software implementations include a spin-wait loop within the lock function until the gate locks. After domain X obtains the lock,
domain X continues execution and updates the data values protected by the particular lock. After domain X completes the updates,
it unlocks (or opens) the software gate, allowing other processes/domains access to the updated data values.
A correctly-implemented system solution must follow these important rules:
• A gate variable must protect all writes to shared data values or shared hardware resources.
• After a domain locks a gate, the system must block other processes/domains from accessing the shared data or
resources. Software conventions enforce this.
• The domain that locks a particular gate is the only domain that can open (unlock) that gate.
Information in the hardware gate identifying the locking domain can be extremely useful for system-level debugging.

#### 16.3.2 16 Hardware-enforced gates

Gates appear as a 16-entry byte-size array with read and write accesses.
Domains lock gates by writing "domainID_number+1" to the appropriate gate and must read back the gate value to verify that the
lock operation succeeded.
After the gate locks, the locking domain unlocks the gate by writing zeroes.
• 16-state implementation
If gate = 0h, then state = unlocked
if gate = 1h, then state = locked by domain (initiator) 0
if gate = 2h, then state = locked by domain (initiator) 1
…
if gate = Fh, then state = locked by domain (initiator) 14
SEMA42 uses the logical domain number and the specified data patterns as reference attributes to validate all write operation.

---

*Page 377*

Semaphores2 (SEMA42)
After a gate locks, the locking domain must unlock the gate by writing zeroes.

#### 16.3.3 State machine of the GATE n registers

This section describes more about the SEMA42 functional operation and specific details of the state machines of the
GATE n registers.
As described previously, each of the GATE n registers implements a 4-bit, 16-state machine. The following figure shows a
simplified diagram of the state transitions for each gate.
reset
1
idle
0000 ~((did == dm0) & (wdata == dm0_lock))
&~((did == dm1) & (wdata == dm1_lock))
& ...
&~((did == dmE) & (wdata == dmE_lock))
2
did == dm0 did == dmE
& (wdata == dm0_lock) & (wdata == dmE_lock)
3 4
dm0_lock .... dmE_lock
0001 1111
5 7
did != dm0 (wdata != unlock) did != dmE (wdata != unlock)
6 8
did == dm0 & (wdata == unlock) did == dmE & (wdata == unlock)
did = Domain identifier
dm = Domain number that identifies core domain X
Figure 36. GATE n state machine
In the figure above, "dmE" represents domain 14 (E in hexadecimal). The platform passes the domain number to SEMA42.
The following table defines the GATE n state transitions.
Table 53. GATE n state transitions
Current state Next state Transition Description
– idle 1 Any reset, whether a system reset or a software-initiated gate reset,
unconditionally forces the gate into the idle state.
idle idle 2 Unless a write of the appropriate lock value from the corresponding
domain occurs, the gate remains in the idle state.
idle dm0_lock 3 When domain 0h initiates a write of the dm0_lock data value, the gate
transitions into the dm0_lock state.
Table continues on the next page...

---

*Page 378*

Semaphores2 (SEMA42)
Table 53. GATE n state transitions (continued)
Current state Next state Transition Description
idle dmE_lock 4 When domain Eh initiates a write of the dmE_lock value, the gate
transitions into the dmE_lock state.
dm0_lock dm0_lock 5 When in this state, the gate remains here if any attempted write is not
from domain 0h with the unlock data value.
dm0_lock idle 6 The gate returns to the idle (unlocked) state after a write from domain 0h
with the unlock data value occurs.
dmE_lock dmE_lock 7 When in this state, the gate remains here if any attempted write is not
from domain Eh with the unlock data value.
dmE_lock idle 8 The gate returns to the idle (unlocked) state after a write from domain Eh
with the unlock data value occurs.
SEMA42 uses these gate data values:
• The lock data value is (domain number) + 1.
• The unlock data value is 00h.

#### 16.3.4 Clocking

Type of clock Description
Module clock (clk) Functions the module and controls the access to the registers.

#### 16.3.5 Interrupts

This module has no interrupts.

#### 16.4 External signals

This module has no external signals.

#### 16.5 Initialization

This module does not require initialization.

#### 16.6 Memory map/register definition

You can access these registers only in Supervisor mode. User accesses terminate with an error.

#### 16.6.1 SEMA42 register descriptions

16.6.1.1 SEMA42 memory map
SEMA42 base address: 4046_0000h

---

*Page 379*

Semaphores2 (SEMA42)
Offset Register Access Reset value
Width
(In bits)
1
0h - Fh Gate (GATE0 - GATE15) 8 RW 00h
42h Reset Gate Read (RSTGT_R) 16 R 0000h
42h Reset Gate Write (RSTGT_W) 16 W See section
1. In this array, the index and offset values of the registers do not increment in direct alignment. For details, see the register
description.
16.6.1.2 Gate (GATE0 - GATE15)
Offset
For n = 0 to 15:
Register Offset
GATEn 0h + (n + 3 - 2 × (n mod 4))
Function
Implements each semaphore gate in a 4-bit finite state machine, right-justified in a byte data structure. The hardware uses the
logical domain-identifier number in conjunction with the data patterns to validate all attempted write operations. Only domain
initiators can modify the gate registers. After a gate locks, only the locking domain must open (unlock) the gate.
You can read multiple gate values in a single access. However, you can update only a single gate at a time, via a write operation. If
you attempt to write a byte-wide value that is neither the unlock value (00h) nor the appropriate lock value (domainID_number + 1),
SEMA42 considers this as "no operation" and does not change any gate state. Attempts to write multiple gates in a single-aligned
access with a size larger than 8 bits (byte) generate an error termination and do not allow any gate state changes.
Diagram
Bits 7 6 5 4 3 2 1 0
R 0
GTFSM
W
Reset 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
7-4
—
3-0 Gate Finite State Machine
GTFSM Indicates the state of the gate for the last domain that locked the gate. This can be useful during
system debug.
Table continues on the next page...

---

*Page 380*

Semaphores2 (SEMA42)
Table continued from the previous page...
Field Function
The hardware gate has a 16-state implementation, defined as:
0000b - The gate is unlocked (free).
0001b - Domain 0 locked the gate.
0010b - Domain 1 locked the gate.
0011b - Domain 2 locked the gate.
0100b - Domain 3 locked the gate.
0101b - Domain 4 locked the gate.
0110b - Domain 5 locked the gate.
0111b - Domain 6 locked the gate.
1000b - Domain 7 locked the gate.
1001b - Domain 8 locked the gate.
1010b - Domain 9 locked the gate.
1011b - Domain 10 locked the gate.
1100b - Domain 11 locked the gate.
1101b - Domain 12 locked the gate.
1110b - Domain 13 locked the gate.
1111b - Domain 14 locked the gate.
16.6.1.3 Reset Gate Read (RSTGT_R)
Offset
Register Offset
RSTGT_R 42h
Function
Describes the specific hardware gate to be reset and records the logic number of domain. Reset Gate Write (RSTGT_W) also
describe the same register showing the fields when you write it.

---

*Page 381*

Semaphores2 (SEMA42)
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 RSTGSM RSTGMS RSTGTN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
15-14
—
13-12 Reset Gate Finite State Machine
RSTGSM Indicates the encoded state machine value when you read the register. RSTGSM = 10b is valid for only a
single machine cycle, so a read can never return this value. SEMA42 maintains the reset state machine in
a 2-bit, 3-state implementation, defined as follows:
00b - Idle, waiting for the first data pattern write.
01b - Waiting for the second data pattern write
10b - The 2-write sequence has completed. Generate the specified gate reset(s). After the reset is
performed, this machine returns to the idle (waiting for first data pattern write) state.
11b - This state encoding is never used and therefore reserved.
11-8 Reset Gate Domain
RSTGMS Records the logical number of the domain performing the gate reset function. The logical number is the
domain number. This domain number is determined by the XRDC's initiator Domain Assignment Controller
(XRDC_MDAC). To succeed, this function requires that the same domain initiate the two consecutive writes
to this register. SEMA42 updates the field each time a write to this register occurs.
7-0 Reset Gate Number
RSTGTN Specifies the specific hardware gate to be reset. The second write updates this field.
If RSTGTN < 64, SEMA42 resets the single gate defined by RSTGTN. Otherwise, SEMA42 resets all
the gates.
16.6.1.4 Reset Gate Write (RSTGT_W)
Offset
Register Offset
RSTGT_W 42h
Function
Specifies the hardware gate to reset when data patterns are specified.

---

*Page 382*

Semaphores2 (SEMA42)
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
W RSTGDP RSTGTN
Reset
u u u u u u u u u u u u u u u u
1
1. Reset value is not applicable for writes.
Fields
Field Function
15-8 Reset Gate Data Pattern
RSTGDP You access this field with the specified data patterns on the two consecutive writes to enable the gate-reset
mechanism. For the first write, RSTGDP must be E2h. For the second write, RSTGDP must be 1Dh.
7-0 Reset Gate Number
RSTGTN Specifies the specific hardware gate to be reset. The second write updates this field.
If RSTGTN < 64, SEMA42 resets the single gate defined by RSTGTN. Otherwise, SEMA42 resets all
the gates.

#### 16.7 Glossary

IPS Protocol used for peripheral accesses to the programming model
dm X Domain X

---

*Page 383*

