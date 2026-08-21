<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 11 -->

# Chapter 11

# Crossbar Switch (AXBS)

#### 11.1 Chip-specific AXBS information

#### 11.1.1 AXBS instances and connectivity matrix

This chip has up to 5 instances of AXBS.
Table 35. AXBS instances
Instance MCXE31B MCXE317 MCXE315/MCXE316
AXBS_0 Yes Yes Yes
AXBS_1 Yes No No
AXBS_2 Yes No No
AXBS_3 Yes No No
AXBS_4 Yes No No
Table 36. AXBS connectivity matrix for MCXE31B
Instance Master and slave assignments
AXBS_0 (main)
Master Master module Slave port Slave module
port
M0 Cortex-M7_0 AHBM S0 Flash memory port 0
M1 AXBS_2 S0 S1 Flash memory port 1
M2 ELE_HSEB S2 PRAM_0
M3 EMAC S3 Cortex-M7 TCM
AXBS_1 (peripheral)
Master Master module Slave port Slave module
port
M0 Cortex-M7_0 AHBP S0 AIPS_0
M1 AXBS_2 S1 S1 AIPS_1
M2 ELE_HSEB S2 AIPS_2
AXBS_2 (eDMA)
Master Master module Slave port Slave module
port
M0 eDMA S0 AXBS_0 M1
S1 AXBS_1 M1

---

*Page 241*

Crossbar Switch (AXBS)
Table 36. AXBS connectivity matrix for MCXE31B (continued)
Instance Master and slave assignments
AXBS_3 (Cortex-M7 TCM)
Master Master module Slave port Slave module
port
M0 AXBS_0 S3 S0 Cortex-M7_0 TCM
AXBS_4 (HSE)
Master Master module Slave port Slave module
port
M0 ELE_HSEB S0 AXBS_0 M2
Table 37. AXBS connectivity matrix for MCXE315/MCXE316 and MCXE317
Instance Master and slave assignments
AXBS_0 (main)
Master Master module Slave port Slave module
port
M0 Cortex-M7_0 AHBM S0 Flash memory port 0
M1 AXBS_2 S0 S1 Flash memory port 1
M2 ELE_HSEB S2 PRAM_0
M3 EMAC S3 Cortex-M7 TCM
M4 Reserved S4 AIPS_0
S5 AIPS_1

#### 11.2 Overview

This section provides information on the layout, configuration, and programming of the crossbar switch.
The crossbar switch connects bus initiators and bus targets using a crossbar switch structure. This structure allows all bus
initiators to access different bus targets simultaneously, while providing arbitration among the bus initiators when they access the
same target. A variety of bus arbitration methods and attributes may be programmed on a target-by-target basis.

#### 11.2.1 Features

• Symmetric crossbar bus switch implementation
— Allows concurrent access from different Initiators to different Targets
— Target arbitration attributes configured on a Target-by-Target basis
• Single-clock 64-bit transfer
• Support for burst transfers of 64 bits of data
• Support for low-power park mode
• Initiator high-priority elevation
• 64-bit AHB crossbar bus switch compatible with ARM's AMBA Specification v2.0

---

*Page 242*

Crossbar Switch (AXBS)

#### 11.3 Functional description

Information about general operation and arbitration are provided in this section.

#### 11.3.1 General operation

When a Initiator accesses the crossbar switch, the access is immediately taken. If the targeted target port of the access is
available, then the access is immediately presented on the target port. Single-clock or zero-wait-state accesses are possible
through the crossbar. If the targeted target port of the access is busy or parked on a different Initiator port, the requesting Initiator
sees wait states inserted until the targeted target port can service the Initiator's request. The latency in servicing the request
depends on each Initiator's priority level and the responding target's access time.
Because the crossbar switch appears to be just another target to the Initiator device, the Initiator device does not know whether
it owns the target port it is targeting. The Initiator waits while it does not have control of the target port it is targeting.
After the Initiator acquires control of the target port, it controls the port until it relinquishes the port by running an IDLE cycle or by
targeting a different target port for its next access.
The Initiator can also lose control of the target port if another higher-priority Initiator makes a request to the target port. However,
if the Initiator is running a fixed-length burst transfer, it retains control of the target port until that transfer completes.
The crossbar terminates all Initiator IDLE transfers, as opposed to allowing the termination to come from one of the target buses.
Additionally, when no Initiator is requesting access to a target port, the crossbar drives IDLE transfers onto the target bus, even
though a default Initiator may be granted access to the target port.
When a target bus is being idled by the crossbar, it can park the target port on the Initiator port indicated by CRS n [PARK]. This is
done to save the initial clock of arbitration delay that otherwise would be seen if the same Initiator had to arbitrate to gain control
of the target port. The target port can also be put into low-power park mode to save power, by using CRS n [PCTL].

#### 11.3.2 Register coherency

The operation of the crossbar is affected as soon as a register is written. The values of the registers do not track with
target-port-related Initiator accesses, but instead track only with target accesses.

#### 11.3.3 Arbitration

The crossbar switch supports the following arbitration algorithms:
• Fixed priority
• Round-robin
The arbitration scheme is independently programmable for each target port.
11.3.3.1 Fixed-priority operation
When operating in fixed-priority mode, each initiator is assigned a unique priority level in the priority registers (PRS n ) . If two
initiators request access to the same target port, the initiator with the highest priority in the selected priority register gains control
over the target port.
NOTE
In this arbitration mode, a higher-priority initiator can monopolize a target port, preventing access from any
lower-priority initiator to the port.
When a initiator makes a request to a target port, the target port checks whether the new requesting initiator's priority level is higher
than that of the initiator that currently has control over the target port, unless the target port is in a parked state. The target port
performs an arbitration check at every clock edge to ensure that the initiator, if any, has control of the target port.
The following table describes possible scenarios based on the requesting initiator port.

---

*Page 243*

Crossbar Switch (AXBS)
Table 38. Methods of how the crossbar switch grants control of a target port to a initiator
When Then the crossbar switch grants control to the requesting
initiator
Both of the following are true: At the next clock edge
• The current initiator is not running a transfer.
• The new requesting initiator's priority level is higher than
that of the current initiator.
Both of the following are true: At the end of the burst transfer or locked transfer
• The current initiator is running a fixed-length burst
transfer or a locked transfer.
• The requesting initiator's priority level is higher than that
of the current initiator.
The requesting initiator's priority level is lower than the At the conclusion of one of the following cycles:
current initiator.
• An IDLE cycle
• A non- IDLE cycle to a location other than the current
target port
11.3.3.2 Round-robin priority operation
When operating in round-robin mode, each initiator is assigned a relative priority based on the initiator port number. This relative
priority is compared to the initiator port number ( ID ) of the last initiator to perform a transfer on the target bus. The highest priority
requests the initiator owns the target bus at the next transfer boundary , accounting for locked and fixed-length burst transfers .
Priority is based on how far ahead the ID of the requesting initiator is of the ID of the last initiator.
After a initiator is granted access to a target port, a initiator may perform as many transfers as desired to that port until another
initiator requests the same target port. The next initiator in line is granted access to the target port at the next transfer boundary ,
or possibly on the next clock cycle, if the current initiator has no pending access request.
As an example of arbitration in round-robin mode, assume that the crossbar is implemented with initiator ports 0, 1, 4, and 5. If
the last initiator of the target port was initiator 1, and initiators 0, 4, and 5 make simultaneous requests, they are serviced in this
order: 4,5, and then 0.
Parking may continue to be used in a round-robin mode, but does not affect the round-robin pointer unless the parked initiator
performs a transfer. Handoff occurs to the next initiator in line after one cycle of arbitration. If the target port is put into low-power
park mode, the round-robin pointer is reset to point at initiator port 0, giving it the highest priority.
11.3.3.3 Clocking
This module has no clocking considerations.
11.3.3.4 Interrupts
This module has no interrupts.
11.3.3.5 Priority assignment
Each initiator port must be assigned a unique 3-bit priority level. If an attempt is made to program multiple initiator ports with
the same priority level within the priority registers (PRS n ), the crossbar switch responds with a bus error and the registers are
not updated.

---

*Page 244*

Crossbar Switch (AXBS)

#### 11.4 External signals

This module has no external signals.

#### 11.5 Initialization/application information

No initialization is required for the crossbar switch.
Hardware reset ensures that all the register bits used by the crossbar switch are properly initialized to a valid state. However, the
following settings and priorities may be programmed to achieve the maximum system performance:
• During the configuration of the crossbar switch, all other initiators must be IDLE .
• To prevent reconfiguration of the crossbar switch, write 1 to CRS n [RO].

#### 11.6 Memory map and register definition

Each target port of the crossbar switch contains configuration registers. Read- and write transfers require two bus clock cycles.
The registers can be read from and written to only in supervisor mode. Additionally, these registers can be read from or written
to only by 32-bit accesses.
A bus error response is returned if an unimplemented location is accessed within the crossbar switch.
The CRS n and PRS n registers can be programmed as read-only to prevent changes to their configuration. After being read-only
protected, future writes to them terminate with a data storage error.
NOTE
This section shows the registers for all eight initiator and target ports. If a initiator or target is not used on this
particular chip, then unexpected results occur when writing to its registers. See the chip configuration details for
the exact initiator and target assignments for your chip.
All references to the crossbar switch registers are based on the physical port connections.

#### 11.6.1 AXBS register descriptions

11.6.1.1 AXBS memory map
AXBS_LITE base address: 4020_0000h
Offset Register Access Reset value
Width
(In bits)
0h Priority Target Registers (PRS0) 32 RW 0000_3210h
10h Control Register (CRS0) 32 RW 0002_0110h
100h Priority Target Registers (PRS1) 32 RW 0000_3210h
110h Control Register (CRS1) 32 RW 0002_0110h
200h Priority Target Registers (PRS2) 32 RW 0000_3210h
210h Control Register (CRS2) 32 RW 0002_0110h
300h Priority Target Registers (PRS3) 32 RW 0000_3210h
310h Control Register (CRS3) 32 RW 0002_0110h
400h Priority Target Registers (PRS4) 32 RW 0000_3210h
Table continues on the next page...

---

*Page 245*

Crossbar Switch (AXBS)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
410h Control Register (CRS4) 32 RW 0002_0110h
500h Priority Target Registers (PRS5) 32 RW 0000_3210h
510h Control Register (CRS5) 32 RW 0002_0110h
600h Priority Target Registers (PRS6) 32 RW 0000_3210h
610h Control Register (CRS6) 32 RW 0002_0110h
11.6.1.2 Priority Target Registers (PRS0 - PRS6)
Offset
Register Offset
PRS0 0h
PRS1 100h
PRS2 200h
PRS3 300h
PRS4 400h
PRS5 500h
PRS6 600h
Function
The priority target registers(PRS n ) set the priority of each initiator port on a per target port basis and reside in each target port.
The priority register can be accessed only with 32-bit access. After the CRS n [RO] bit is set, the PRS n register can only be read;
attempts to write to it have no effect on PRS n and result in a bus-error response to the initiator initiating the write.
Two available initiators must not be programmed with the same priority level. Attempts to program two or more initiators with the
same priority level result in a bus-error response and the PRS n is not updated.
NOTE
Valid values for the M n priority fields depend on which initiators are available on the chip. This information can be
found in the chip-specific information for the crossbar.
• If the chip contains fewer than three initiators, only one bit is valid.
• If the chip contains fewer than five initiators, only two bits are valid.
• If five or more initiators are present, all three bits of the priority field are used.

---

*Page 246*

Crossbar Switch (AXBS)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 Reserved 0 Reserved 0 Reserved 0 Reserved
W - - - -
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0
M3 M2 M1 M0
W
Reset 0 0 1 1 0 0 1 0 0 0 0 1 0 0 0 0
Fields
Field Function
Reserved
31
—
Reserved
30-28
—
Reserved
27
—
Reserved
26-24
—
Reserved
23
—
Reserved
22-20
—
Reserved
19
—
Reserved
18-16
—
Reserved
15
—
14-12 Initiator 3 Priority
M3 This field sets the arbitration priority for this port on the associated target port.
Table continues on the next page...

---

*Page 247*

Crossbar Switch (AXBS)
Table continued from the previous page...
Field Function
000b - This initiator has level 1 or highest priority when accessing the target port.
001b - This initiator has level 2 priority when accessing the target port.
010b - This initiator has level 3 priority when accessing the target port.
011b - This initiator has level 4 priority when accessing the target port.
100b - This initiator has level 5 priority when accessing the target port.
101b - This initiator has level 6 priority when accessing the target port.
110b - This initiator has level 7 priority when accessing the target port.
111b - This initiator has level 8the or lowest priority when accessing the target port.
Reserved
11
—
10-8 Initiator 2 Priority
M2 This field sets the arbitration priority for this port on the associated target port.
000b - This initiator has level 1 or highest priority when accessing the target port.
001b - This initiator has level 2 priority when accessing the target port.
010b - This initiator has level 3 priority when accessing the target port.
011b - This initiator has level 4 priority when accessing the target port.
100b - This initiator has level 5 priority when accessing the target port.
101b - This initiator has level 6 priority when accessing the target port.
110b - This initiator has level 7 priority when accessing the target port.
111b - This initiator has level 8the or lowest priority when accessing the target port.
Reserved
7
—
6-4 Initiator 1 Priority
M1 This field sets the arbitration priority for this port on the associated target port.
000b - This initiator has level 1 or highest priority when accessing the target port.
001b - This initiator has level 2 priority when accessing the target port.
010b - This initiator has level 3 priority when accessing the target port.
011b - This initiator has level 4 priority when accessing the target port.
100b - This initiator has level 5 priority when accessing the target port.
101b - This initiator has level 6 priority when accessing the target port.
110b - This initiator has level 7 priority when accessing the target port.
111b - This initiator has level 8 or lowest priority when accessing the target port.
Table continues on the next page...

---

*Page 248*

Crossbar Switch (AXBS)
Table continued from the previous page...
Field Function
Reserved
3
—
2-0 Initiator 0 Priority
M0 This field sets the arbitration priority for this port on the associated target port.
000b - This initiator has level 1 or highest priority when accessing the target port.
001b - This initiator has level 2 priority when accessing the target port.
010b - This initiator has level 3 priority when accessing the target port.
011b - This initiator has level 4 priority when accessing the target port.
100b - This initiator has level 5 priority when accessing the target port.
101b - This initiator has level 6 priority when accessing the target port.
110b - This initiator has level 7 priority when accessing the target port.
111b - This initiator has level 8 or the lowest priority when accessing the target port.
11.6.1.3 Control Register (CRS0 - CRS6)
Offset
Register Offset
CRS0 10h
CRS1 110h
CRS2 210h
CRS3 310h
CRS4 410h
CRS5 510h
CRS6 610h
Function
These registers control several features of each target port and must be accessed using 32-bit accesses. After CRS n [RO] is set,
the PRS n can only be read; attempts to write to it have no effect and results in an error response.
NOTE
See the chip-specific crossbar information for the reset value of this register.
Not all HPE n fields may be active. See the chip-specific crossbar information for which initiators support initiator
high-priority elevation. Setting a field corresponding to a initiator that does not support initiator, high-priority
elevation has no effect.

---

*Page 249*

Crossbar Switch (AXBS)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RO 0 0 0 0 0
HLP HPE3 HPE2 HPE1 HPE0
W 1
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
ARB PCTL PARK
W
Reset 0 0 0 0 0 0 0 1 0 0 0 1 0 0 0 0
Fields
Field Function
31 Read Only
RO Forces the PRS n and CRS n registers to be read-only. After being set, only a hardware reset clears this field.
0b - The CRS n and PRS n registers are writeable
1b - The CRS n and PRS n registers are read-only and cannot be written (attempted writes have
no effect on the registers and result in a bus error response).
30 Halt Low Priority
HLP This field sets the initial arbitration priority for low power-mode requests . Setting this bit will not affect
the ,request for low power-mode from attaining the highest priority after it has control of the target ports.
0b - The low-power mode request has the highest priority for arbitration on this target port.
1b - The low-power mode request has the lowest initial priority for arbitration on this target port.
Reserved
29-24
—
Reserved
23
—
Reserved
22
—
Reserved
21
—
Reserved
20
—
19 High Priority Elevation 3
Table continues on the next page...

---

*Page 250*

Crossbar Switch (AXBS)
Table continued from the previous page...
Field Function
This field enables initiator high-priority elevation for initiator 3 on this target port. If enabled, the initiator
HPE3
can elevate its priority to the highest.
0b - Initiator high-priority elevation for initiator 3 is disabled on this target port.
1b - Initiator high-priority elevation for initiator 3 is enabled on this target port.
18 High Priority Elevation 2
HPE2 This field enables initiator high-priority elevation for initiator 2 on this target port. If enabled, the initiator
can elevate its priority to the highest.
0b - Initiator high-priority elevation for initiator 2 is disabled on this target port.
1b - Initiator high-priority elevation for initiator 2 is enabled on this target port.
17 High Priority Elevation 1
HPE1 This field enables initiator high-priority elevation for initiator 1 on this target port. If enabled, the initiator
can elevate its priority to the highest.
0b - Initiator high-priority elevation for initiator 1 is disabled on this target port.
1b - Initiator high-priority elevation for initiator 1 is enabled on this target port.
16 High Priority Elevation 0
HPE0 This field enables initiator high-priority elevation for initiator 0 on this target port. If enabled, the initiator
can elevate its priority to the highest.
0b - Initiator high-priority elevation for initiator 0 is disabled on this target port.
1b - Initiator high-priority elevation for initiator 0 is enabled on this target port.
Reserved
15-10
—
9-8 Arbitration Mode
ARB This field selects the arbitration policy for the target port.
00b - Fixed priority
01b - Round-robin (rotating) priority
10b - Reserved
11b - Reserved
Reserved
7-6
—
5-4 Parking Control
PCTL This field determines the target port's parking control. The low-power park feature results in an overall
power savings if the target port is not saturated; however, this forces an extra latency clock when any
initiator tries to access the target port when not in use because it is not parked on any initiator.
Table continues on the next page...

---

*Page 251*

Crossbar Switch (AXBS)
Table continued from the previous page...
Field Function
00b - When no initiator makes a request, the arbiter parks the target port on the initiator port
defined by the PARK bit field.
01b - When no initiator makes a request, the arbiter parks the target port on the last initiator to be
in control of the target port.
10b - Low-power park. When no initiator makes a request, the target port is not parked on a
initiator and the arbiter drives all outputs to a constant safe state.
11b - Reserved
Reserved
3
—
2-0 Park
PARK This field determines which initiator port the current target port parks on when no initiators are actively
making requests and the PCTL bits are cleared.
NOTE
Select only initiator ports that are present on the chip. Otherwise, undefined behavior
might occur.
000b - Park on initiator port M0
001b - Park on initiator port M1
010b - Park on initiator port M2
011b - Park on initiator port M3
100b - Park on initiator port M4
101b - Park on initiator port M5
110b - Park on initiator port M6
111b - Park on initiator port M7

#### 11.7 Glossary

AMBA Advanced Microcontroller Bus Architecture
IDLE A type of transfer that a initiator uses when it does not want to perform a data transfer
ID Initiator port number

---

*Page 252*

