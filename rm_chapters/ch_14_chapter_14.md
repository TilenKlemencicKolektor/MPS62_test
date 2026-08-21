<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 14 -->

# Chapter 14

# Enhanced Direct Memory Access (eDMA)

#### 14.1 Chip-specific eDMA information

#### 14.1.1 DMA channels

See the following table for number of DMA channels across MCXE31 product series.
Table 42. DMA channels
Chip No. of DMA channels
MCXE31B 32
MCXE315/MCXE316/MCXE317 12
NOTE
TCD_CH[12-31] are not available in MCXE317, so the registers corresponding to these channels are not available
in MCXE317 variant. See the memory map file attached to this document for details.

#### 14.2 Introduction

The enhanced direct memory access (eDMA) controller is capable of performing complex data transfers with minimal intervention
from a host processor. The hardware microarchitecture includes:
• A DMA engine that performs:
— Source address and destination address calculations
— Data-movement operations
• Local memory containing transfer control descriptors for each of the 32 channels

#### 14.2.1 eDMA system block diagram

Figure 27 illustrates the components of the eDMA system, including the eDMA module (engine).

---

*Page 265*

Enhanced Direct Memory Access (eDMA)
eDMA system
Write Address
Write Data
0
1
2
Transfer Control
Descriptor (TCD)
n-1
64
Internal Peripheral Bus
eDMA Engine
Read Data
To/from interconnect fabric Program Model/
Channel Arbitration
Read Data
Address Path
Control
Data Path
Write Data
Address
eDMA eDMA
Peripheral Done
Request
Figure 27. eDMA system block diagram

#### 14.2.2 Block parts

The eDMA module is partitioned into two major modules: the eDMA engine and the transfer control descriptor local memory.
The eDMA engine is further partitioned into four submodules:
Table 43. eDMA engine submodules
Submodule Function
Address path
This block:
• Implements a primary channel and secondary (preempt) channel
• Manages all master bus-address calculations
All the channels provide the same functionality. This structure allows data transfers associated
with one channel to be preempted after the completion of a read/write sequence if a higher priority
channel activation is asserted while the primary channel is active.
After a channel is activated, it runs until the minor loop is completed, unless preempted by a
higher priority channel. This provides a mechanism (enabled by CH n _PRI[ECP]) where a large data
transfer can be preempted to minimize the time another channel is blocked from execution.
Table continues on the next page...

---

*Page 266*

Enhanced Direct Memory Access (eDMA)
Table 43. eDMA engine submodules (continued)
Submodule Function
When any channel is selected to execute, the contents of its TCD are read from local memory and
loaded into the address path channel x registers for a normal start and into channel y registers for
a preemption start. After the minor loop completes execution, the address path hardware writes the
new values for the TCD n _{SADDR, DADDR, CITER} back to local memory. If the major iteration
count is exhausted, additional processing is performed, including the final address pointer updates,
reloading the TCD n _CITER field, and a possible fetch of a new TCD n from memory as part of a
scatter/gather operation. See Dynamic scatter/gather for more details.
Data path
This block implements the bus master read/write data path. It includes a data buffer and the
necessary multiplex logic to support any required data alignment. The internal read data bus is the
primary input, and the internal write data bus is the primary output.
The address and data path modules directly support the 2-stage pipelined internal bus. The address
path module represents the first stage of the bus pipeline (address phase), and the data path module
implements the second stage of the pipeline (data phase).
Program model/channel This block implements the first section of the eDMA programming model as well as the channel
arbitration arbitration logic. The programming model registers are connected to the internal peripheral bus.
The eDMA peripheral request inputs and interrupt request outputs are also connected to this
block (via control logic).
Control
This block provides all the control functions for the eDMA engine. For data transfers where
the source and destination sizes are equal, the eDMA engine performs a series of source read/
destination write operations until the number of bytes specified in the minor loop byte count has been
moved from the source to the destination.
For descriptors where the sizes are not equal, multiple accesses of the smaller size data are required
for each reference of the larger size. As an example, if the source size references 16-bit data and
the destination is 32-bit data, the eDMA performs two reads, then one 32-bit write.
The transfer control descriptor local memory is further partitioned into:
Table 44. Transfer control descriptor memory
Submodule Description
Memory controller This logic implements the required dual-ported controller, and manages accesses from the eDMA
engine as well as references from the internal peripheral bus . As noted earlier, in simultaneous
accesses, the eDMA engine is given priority and the peripheral transaction is stalled.
Memory array TCD storage for each channel's transfer profile.

#### 14.2.3 Features

The eDMA is a highly programmable data-transfer engine optimized to minimize any required intervention from the host processor.
It is intended for use in applications where the data size to be transferred is statically known and not defined within the transferred
data itself. The eDMA module features:
• All data movement via dual-address transfers: read from source, write to destination
— Programmable source and destination addresses and transfer size
— Support for complex address calculations
• 32 -channel implementation that performs complex data transfers with minimal intervention from a host processor
— Internal data buffer, used as temporary storage for all transfers

---

*Page 267*

Enhanced Direct Memory Access (eDMA)
— Connections to the crossbar switch for bus mastering the data movement
• TCD organized to support two-deep, nested transfer operations
— 32-byte TCD stored in local memory for each channel
— An inner data transfer loop defined by a minor byte transfer count
— An outer data transfer loop defined by a major iteration count
• Channel activation via one of three methods:
— Explicit software initiation
— Initiation via a channel-to-channel linking mechanism for continuous transfers
— Peripheral-paced hardware requests, one per channel
• Fixed-priority and round-robin channel arbitration
• Channel completion reported via programmable interrupt requests
— One interrupt per channel, which can be asserted at completion of major iteration count
— Programmable error terminations per channel that are logically summed together to form one error interrupt to the
interrupt controller
• Programmable support for scatter/gather DMA processing
• Support for complex data structures
In the discussion of this module, n is used to reference the channel number.

#### 14.3 Modes of operation

The eDMA operates in the following modes:
Table 45. Modes of operation
Mode Description
Normal
In Normal mode, eDMA transfers data between a source and a destination. The source and
destination can be a memory block or an I/O block capable of operation with eDMA.
A service request initiates a transfer of a specific number of bytes (NBYTES) as specified in the TCD.
The minor loop is the sequence of read-write operations that transfers these NBYTES per service
request. Each service request executes one iteration of the major loop, which transfers NBYTES
of data.
Debug
eDMA operation is configurable in Debug mode via the control register:
• If CSR[EDBG] is cleared to 0, eDMA continues to operate.
• If CSR[EDBG] is set to 1, eDMA stops transferring data. If Debug mode is entered when a
channel is active, eDMA continues operation until the channel retires.

#### 14.4 Functional description

The operation of eDMA is described in the following subsections.

#### 14.4.1 eDMA basic data flow

The basic flow of a data transfer can be partitioned into three segments.
As shown in the following diagram, the first segment involves the channel activation:

---

*Page 268*

Enhanced Direct Memory Access (eDMA)
eDMA
Write address
Write data
0
1
2
Transfer control
descriptor (TCD)
n-1
64
Internal peripheral bus
eDMA engine
Read data
Program model/
To/from crossbar switch
channel arbitration
Read data
Address path
Control
Data path
Write data
Address
eDMA eDMA
peripheral done
request
Figure 28. eDMA operation, part 1
This example uses the assertion of the eDMA peripheral request signal to request service for channel n . Channel activation via
software and the TCD n _CSR[START] field follows the same basic flow as peripheral requests. The eDMA request input signal is
registered internally and then routed through the eDMA engine: first through the control module, then into the program model and
channel arbitration.
In the next cycle, the channel arbitration begins using fixed-priority plus the optional round-robin algorithm. After arbitration is
complete, the activated channel number is sent through the address path and converted into the required address to access the
local memory for TCD n . Next, the TCD memory is accessed and the required descriptor is read from the local memory and then
loaded into the eDMA engine address path's primary or secondary channel execution registers. The TCD memory is 64 bits wide
to minimize the time needed to fetch the activated channel descriptor and load it into the address path registers.
The following diagram illustrates the second part of the basic data flow:

---

*Page 269*

Enhanced Direct Memory Access (eDMA)
eDMA
Write address
Write data
0
1
2
Transfer control
descriptor (TCD)
n-1
64
Internal peripheral bus
eDMA engine
Read data
To/from crossbar switch Program model/
channel arbitration
Read data
Address path
Control
Data path
Write data
Address
eDMA eDMA
peripheral done
request
Figure 29. eDMA operation, part 2
The modules associated with the data transfer (address path, data path, and control) go through the required sequence of
source reads and destination writes to perform the actual data movement. The source reads are initiated, and the fetched
data is temporarily stored in the data path block until it is gated onto the internal bus during the destination write. This source
read/destination write processing continues until the byte count, NBYTES, has been transferred.
After NBYTES of data has been moved, the final phase of the basic data flow is performed. In this segment, the address path
logic performs the required updates to certain fields in the appropriate TCD (for example, SADDR, DADDR, CITER). If the major
iteration count is exhausted, additional operations are performed. These include the final address adjustments and reloading of
the BITER field into the CITER field. Assertion of an optional interrupt request also occurs at this time, as does a possible fetch
of a new TCD from memory using the scatter/gather address pointer included in the descriptor (if scatter/gather is enabled). The
updates to the TCD memory and the assertion of an interrupt request are shown in the following diagram.

---

*Page 270*

Enhanced Direct Memory Access (eDMA)
eDMA
Write address
Write data
0
1
2
Transfer control
descriptor (TCD)
n-1
64
Internal peripheral bus
eDMA engine
Read data
Program model/
To/from crossbar switch
channel arbitration
Read data
Address path
Control
Data path
Write data
Address
eDMA eDMA
peripheral done
request
Figure 30. eDMA operation, part 3

#### 14.4.2 Fault reporting and handling

Channel errors are reported in the Error Status register ( CHn_CSR and TCDn_CSR ) and can be caused by any of the following:
• A configuration error, which is an illegal setting in the transfer control descriptor
• An active channel canceled via a "cancel transfer with error" hardware or software request
• A TCD memory error
• An error termination to a bus master read or write cycle
A configuration error is reported when an inconsistent state is represented by one of these factors:
• Starting source or destination address
• Source or destination offsets
• Minor loop byte count
• Transfer size
Each of these possible causes is detailed below:
• The addresses and offsets must be aligned on zero-modulo-transfer-sized boundaries.
• The minor loop byte count must be a multiple of the source and destination transfer sizes.
• All source reads and destination writes must be configured to the natural boundary of the programmed transfer size.

---

*Page 271*

Enhanced Direct Memory Access (eDMA)
NOTE
To aid in debugging, set the Halt After Error field in the DMA’s Control Status register, CSR[HAE]. Upon any error
condition, the DMA is halted after the error is recorded. The DMA remains halted and does not process any channel
service requests. After the error is fixed, the DMA may be enabled again by clearing the Halt field, CSR[HALT].
• If a scatter/gather operation is enabled upon channel completion, a configuration error is reported if the scatter/gather
address (DLAST_SGA) is not aligned on a 32-byte boundary.
• If minor loop channel linking is enabled upon channel completion, a configuration error is reported when the link is
attempted if the TCDn_CITER[ELINK] field does not equal the TCDn_BITER[ELINK] field.
If enabled, all configuration error conditions, except the scatter/gather and minor-loop link errors, are reported as the channel
activates and asserts an error interrupt request. A scatter/gather configuration error is reported when the scatter/gather operation
begins at major loop completion if properly enabled. A minor loop channel link configuration error is reported when the link
operation is serviced at minor loop completion.
The occurrence of any error causes the eDMA engine to stop normal processing of the active channel immediately (it goes to
its error processing states and the transaction to the system bus still has pipeline effect), and the appropriate channel field in
the eDMA error register is set to 1. At the same time, the details of the error condition are loaded into the Error Status register
( CHn_CSR and TCDn_CSR ). The major loop complete indicators, setting the transfer control descriptor DONE flag, and the
possible assertion of an interrupt request are not affected when an error is detected.
After the error status has been updated, the eDMA engine continues operating by servicing the next appropriate channel. A
channel that experiences an error condition is not automatically disabled. If a channel is terminated by an error and then issues
another service request before the error is fixed, that channel executes and terminates with the same error condition.
The error status fields are read-only. These error indicators are sticky and cannot be cleared. They show the last recorded error
until the DMA is reset. The valid field (VLD) is used to determine if a new error condition exists. This field is the logical OR of each
channel's error interrupt field (ERR).
After the software has resolved any errors and cleared all of the error interrupt fields, the valid field is cleared to 0 but the cause
of the last error is still indicated.

#### 14.4.3 Channel preemption

Channel preemption is enabled on a per-channel basis by setting the CHn_PRI[ECP] field. Channel preemption allows the
executing channel’s data transfers to temporarily suspend in favor of starting a higher-priority channel. After the preempting
channel has completed all of its minor loop data transfers, the preempted channel is restored and resumes execution.
After the restored channel completes one read/write sequence, it is again eligible for preemption. If any higher priority channel
is requesting service, the restored channel is suspended, and the higher-priority channel is serviced. Nested preemption, that
is, attempting to preempt a preempting channel, is not supported. After a preempting channel begins execution, it cannot
be preempted.
A channel’s ability to preempt another channel can be disabled by setting CHn_PRI[DPA] to 1. When a channel’s preempt ability
is disabled, that channel cannot suspend a lower-priority channel’s data transfer, regardless of the lower-priority channel’s ECP
setting. This allows for a pool of low-priority, large-data-moving channels to be defined.
You can configure these low-priority channels to not preempt each other, thus preventing a low-priority channel from consuming
the preempt slot normally available to a true high-priority channel. When you enable round-robin channel arbitration mode
(CSR[ERCA] is set to 1), any channel with a priority level equal to 0 (CHn_PRI[APL] = 0) has preemption disabled and cannot
preempt another channel.

#### 14.5 Initialization/application information

The following sections discuss initialization of the eDMA and programming considerations.

#### 14.5.1 eDMA initialization

To initialize the eDMA:

---

*Page 272*

Enhanced Direct Memory Access (eDMA)
1. Write to the CSR if a configuration other than the default is wanted.
2. Write the channel priority levels to the CHn_PRI registers and group priority levels to the CHn_GRPRI registers if a
configuration other than the default is wanted.
3. Enable error interrupts in the CHn_CSR[EEI] registers if they are wanted.
4. Write the 32-byte TCD for each channel that may request service.
5. Enable any hardware service requests via the CHn_CSR[ERQ] registers.
6. Request channel service via either:
• Software: setting TCDn_CSR[START]
• Hardware: slave device asserting its eDMA peripheral request signal
After any channel requests service, a channel is selected for execution based on the arbitration and priority levels written into the
programmer's model. The eDMA engine reads the entire TCD, including the TCD control and status fields, as shown in Table 46 ,
for the selected channel into its internal address path module.
As the TCD is read, the first transfer is initiated on the internal bus, unless a configuration error is detected. Transfers from the
source, defined by TCDn_SADDR, to the destination, defined by TCD_DADDR, continue until the number of bytes specified by
TCDn_NBYTES are transferred.
When the transfer is complete, the eDMA engine's local TCDn_SADDR, TCDn_DADDR, and TCDn_CITER are written back to
the main TCD memory and any minor loop channel linking is performed, if enabled. If the major loop is exhausted, then eDMA
executes further post-processing, such as interrupts, major loop channel linking, and scatter/gather operations, if enabled.
Table 46. TCD control and status (TCDn_CSR) fields
TCDn_CSR field name Description
START Control field to start the channel explicitly when using a software-initiated DMA service (automatically
cleared by hardware)
EEOP Control field to enable end-of-packet processing
Control field to enable storing of the destination address to system memory after the major loop
ESDA
completes
DREQ Control field to disable hardware-initiated DMA service requests after major loop completion
BWC Control field for throttling the bandwidth control of a channel
ESG Control field to enable the scatter-gather feature
INTHALF Control field to enable interrupt when major loop is half-complete
INTMAJOR Control field to enable interrupt when major loop completes
Table 47. Channel control and status (CHn_CSR) fields
CHn_CSR field name Description
ACTIVE Status field indicating the channel is currently in execution
DONE Status field indicating major loop completion (cleared by software when a channel begins execution)
EEI Control field to enable error interrupts
Table continues on the next page...

---

*Page 273*

Enhanced Direct Memory Access (eDMA)
Table 47. Channel control and status (CHn_CSR) fields (continued)
CHn_CSR field name Description
EARQ Control field to enable external, asynchronous wakeup event in conjunction with the ERQ field
ERQ Control field to enable hardware service requests
The following figure shows how each DMA request initiates one minor-loop transfer, or iteration, without CPU intervention. DMA
arbitration can occur after each minor loop, and one level of minor loop DMA preemption is allowed. The number of minor loops
in a major loop is specified by the beginning iteration count (BITER).
Current major
loop iteration
Source or destination memory count (CITER)
DMA request
• Minor loop 3
•
•
DMA request
• Minor loop Major loop 2
•
•
DMA request
• Minor loop 1
•
•
Figure 31. Example of multiple loop iterations
The following figure lists the memory array terms and how the TCD settings are related.

---

*Page 274*

Enhanced Direct Memory Access (eDMA)
xADDR: (starting address) xSIZE: (size of one
data transfer) Minor loop
(NBYTES in
• minor loop, Offset (xOFF): number of bytes added to
• often the same current address after each transfer
•
value as xSIZE) (often the same value as xSIZE)
Each DMA source (S) and
destination (D) has its own:
• •
Address (xADDR)
• •
• • Size (xSIZE)
Minor loop
• • Offset (xOFF)
• • Modulo (xMOD)
• • Last Address Adjustment (xLAST)
where x = S or D
Peripheral queues typically
have size and offset equal
•
to NBYTES
• Last minor loop
•
xLAST: Number of bytes added to
current address after major loop
(typically used to loop back)
Figure 32. Memory array terms

#### 14.5.2 eDMA arbitration

The eDMA uses a layered arbitration scheme composed of multiple priority levels. The eDMA uses a fixed-priority arbitration
scheme with optional round-robin arbitration under specific conditions. The priorities are evaluated in the following order:
Table 48. eDMA arbitration priorities
Priority Scheme Description
1 (Highest) Arbitration group priority Each channel is assigned an arbitration
group via the CHn_GRPRI registers.
Priority is given to the highest value (31
being the highest possible value) down to
the lowest value (zero, the default).
2 Channel priority Each channel is assigned a channel
priority level via the CHn_PRI registers.
The channel priority is a relative priority
level within an arbitration group. Priority
is given to the highest value (seven being
the highest possible value) down to the
lowest value (zero, the default). Channel
priorities within each arbitration group
need not be unique. If multiple channels
have the same channel priority level, the
channel number will be used to determine
priority as defined in row three.
3 Channel number When two or more channels have
the same arbitration group priority and
channel priority, the channel number
(CHn_NUM) is used to determine the
highest priority. Priority is giver to the
Table continues on the next page...

---

*Page 275*

Enhanced Direct Memory Access (eDMA)
Table 48. eDMA arbitration priorities (continued)
Priority Scheme Description
highest channel number. Lowest priority
is channel 0. The channel numbers are
static and cannot be changed in the
programmer's model.
4 (Lowest) Round-robin When round-robin is enabled, any
channel configured for round-robin
operation has lowest priority within an
arbitration group. Round-robin is enabled
by setting the CSR[ERCA] field to 1.
After being enabled, channels with a
channel priority of zero (CHn_PRI=0)
will use round-robin arbitration. Round-
robin arbitration will rotate the channel
selection among the channels requesting
service with CHn_PRI=0 within the
arbitration group. Any non-zero channel
within the arbitration group will continue
to use fixed-priority arbitration, and if
requesting service will be selected over
any round-robin channels.
For fixed arbitration, the overall priority can be considered a number composed of three concatenated priority levels:
CHn_GRPRI :CHn_PRI:CH_NUM. The largest number has the highest priority and the lowest number has the lowest priority.
For round-robin arbitration, the priority number is CHn_GRPRI :0:X. The module rotates through the CHn_PRI=0 channels
requesting service without regard to priority among these channels. Any channel within the arbitration group for which CHn_PRI
is greater than 0 will be serviced before the round-robin channels.

#### 14.5.3 Programming errors

The eDMA performs various tests on the transfer control descriptor to verify consistency in the descriptor data.
The channel number causing the error is recorded in the Error Status register ( CHn_CSR and TCDn_CSR ). If the error source is
not removed before the next activation of the problematic channel, the error is detected and recorded again. Setting the halt after
error field, CSR[HAE], will halt the DMA and prevent reoccurrence of the error.

#### 14.5.4 Arbitration mode considerations

This section discusses arbitration considerations for eDMA.
14.5.4.1 Fixed group arbitration, fixed channel arbitration
In this mode, eDMA selects for execution the channel service request from the highest-priority channel in the highest-priority
group. If eDMA is programmed so that the channels within a high-priority group have a high number of requests or large data
transfers, that group may consume all the bandwidth of the eDMA controller. That is, no lower-priority groups are serviced if there
is always at least one DMA request pending on a channel in the highest-priority group when the controller arbitrates the next DMA
request. The advantage of this scenario is that latency can be small for channels that need to be serviced quickly.
14.5.4.2 Fixed group arbitration, round-robin channel arbitration
The highest-priority group with a request is serviced. Lower-priority groups are serviced if no pending requests exist in the
higher-priority groups.

---

*Page 276*

Enhanced Direct Memory Access (eDMA)
Within each group, channels are serviced starting with the highest non-zero channel priority. For all channels with a channel
priority programmed to 0, selection begins with the highest channel number requesting service and then rotates through to
the lowest channel number requesting service. The round-robin channel arbitration can provide a fairness mechanism to
lower-priority channels.
This scenario could cause the same bandwidth consumption problem as indicated in Fixed group arbitration, fixed channel
arbitration , but all the channels in the highest-priority group will be serviced. Service latency is short on the highest-priority group,
but could potentially be very much longer as the group priority decreases.

#### 14.5.5 Performing DMA transfers

This section presents examples on how to perform DMA transfers with the eDMA.
14.5.5.1 Single request
To perform a simple transfer of n bytes of data with one activation, set the major loop to one (TCD n _CITER = TCD n _BITER =
1). The data transfer begins after the channel service request is acknowledged and the channel is selected to execute. After the
transfer is complete, the CH n _CSR[DONE] field is set to 1 and an interrupt is generated if properly enabled.
For example, the following TCD entry is configured to transfer 16 bytes of data. The eDMA is programmed for one iteration of the
major loop transferring 16 bytes per iteration. The source memory has a byte-wide memory port located at 0x1000. The destination
memory has a 32-bit port located at 0x2000. The address offsets are programmed in increments to match the transfer size: one
byte for the source, and four bytes for the destination. The final source and destination addresses are adjusted to return to their
beginning values.
TCD n _CITER = TCD n _BITER = 1
TCD n _NBYTES = 16
TCD n _SADDR = 0x1000
TCD n _SOFF = 1
TCD n _ATTR[SSIZE] = 0
TCD n _SLAST = -16
TCD n _DADDR = 0x2000
TCD n _DOFF = 4
TCD n _ATTR[DSIZE] = 2
TCD n _DLAST_SGA= –16
TCD n _CSR[INTMAJ] = 1
TCD n _CSR[START] = 1 (should be written last after all other fields have been initialized)
All other TCD n fields = 0
This generates the following event sequence:
1. User write to the TCD n _CSR[START] field requests channel service.
2. The channel is selected by arbitration for servicing.
3. eDMA engine writes:
• CH n _CSR[DONE] = 0
• TCD n _CSR[START] = 0
• CH n _CSR[ACTIVE] = 1
4. eDMA engine reads: channel TCD data from local memory to internal register file.
5. The source-to-destination transfers are executed as follows:
a. Read byte from location 0x1000, read byte from location 0x1001, read byte from 0x1002, read byte from 0x1003.
b. Write 32 bits to location 0x2000 → first iteration of the minor loop.
c. Read byte from location 0x1004, read byte from location 0x1005, read byte from 0x1006, read byte from 0x1007.
d. Write 32 bits to location 0x2004 → second iteration of the minor loop.

---

*Page 277*

Enhanced Direct Memory Access (eDMA)
e. Read byte from location 0x1008, read byte from location 0x1009, read byte from 0x100A, read byte from 0x100B.
f. Write 32 bits to location 0x2008 → third iteration of the minor loop.
g. Read byte from location 0x100C, read byte from location 0x100D, read byte from 0x100E, read byte from 0x100F.
h. Write 32 bits to location 0x200C → last iteration of the minor loop → major loop complete.
6. The eDMA engine writes: TCD n _SADDR = 0x1000, TCD n _DADDR = 0x2000, TCD n _CITER = 1 (TCD n _BITER).
7. The eDMA engine writes: CH n _CSR[ACTIVE] = 0, CH n _CSR[DONE] = 1, CH n _INT[INT] = 1.
8. The channel retires and the eDMA goes idle or services the next channel.
14.5.5.2 Multiple requests
The following example transfers 32 bytes via two hardware requests, but is otherwise the same as the previous example. The only
fields that change are the major loop iteration count and the final address offsets. The eDMA is programmed for two iterations
of the major loop, transferring 16 bytes per iteration. After the channel's hardware requests are enabled via the CHn_CSR[ERQ]
register field, the slave device initiates channel service requests.
TCD n _CITER = TCD n _BITER = 2
TCD n _SLAST = –32
TCD n _DLAST_SGA = –32
This would generate the following sequence of events:
1. First hardware (eDMA peripheral) requests channel service.
2. The channel is selected by arbitration for servicing.
3. eDMA engine writes: CH n _CSR[DONE] = 0, TCD n _CSR[START] = 0, CH n _CSR[ACTIVE] = 1.
4. eDMA engine reads: channel TCD n data from local memory to internal register file.
5. The source-to-destination transfers are executed as follows:
a. Read byte from location 0x1000, read byte from location 0x1001, read byte from 0x1002, read byte from 0x1003.
b. Write 32 bits to location 0x2000 → first iteration of the minor loop.
c. Read byte from location 0x1004, read byte from location 0x1005, read byte from 0x1006, read byte from 0x1007.
d. Write 32 bits to location 0x2004 → second iteration of the minor loop.
e. Read byte from location 0x1008, read byte from location 0x1009, read byte from 0x100A, read byte from 0x100B.
f. Write 32 bits to location 0x2008 → third iteration of the minor loop.
g. Read byte from location 0x100C, read byte from location 0x100D, read byte from 0x100E, read byte from 0x100F.
h. Write 32 bits to location 0x200C → last iteration of the minor loop.
6. eDMA engine writes: TCD n _SADDR = 0x1010, TCD n _DADDR = 0x2010, TCD n _CITER = 1.
7. eDMA engine writes: CH n _CSR[ACTIVE] = 0.
8. The channel retires, which concludes one iteration of the major loop. The eDMA goes idle or services the next channel.
9. Second hardware (eDMA peripheral) requests channel service.
10. The channel is selected by arbitration for servicing.
11. eDMA engine writes: CH n _CSR[DONE] = 0, TCD n _CSR[START] = 0, CH n _CSR[ACTIVE] = 1.
12. eDMA engine reads: Channel TCD data from local memory to internal register file.
13. The source-to-destination transfers are executed as follows:
a. Read byte from location 0x1010, read byte from location 0x1011, read byte from 0x1012, read byte from 0x1013.

---

*Page 278*

Enhanced Direct Memory Access (eDMA)
b. Write 32 bits to location 0x2010 → first iteration of the minor loop.
c. Read byte from location 0x1014, read byte from location 0x1015, read byte from 0x1016, read byte from 0x1017.
d. Write 32 bits to location 0x2014 → second iteration of the minor loop.
e. Read byte from location 0x1018, read byte from location 0x1019, read byte from 0x101A, read byte from 0x101B.
f. Write 32 bits to location 0x2018 → third iteration of the minor loop.
g. Read byte from location 0x101C, read byte from location 0x101D, read byte from 0x101E, read byte from 0x101F.
h. Write 32 bits to location 0x201C → last iteration of the minor loop → major loop complete.
14. eDMA engine writes: TCD n _SADDR = 0x1000, TCD n _DADDR = 0x2000, TCD n _CITER = 2 (TCD n _BITER).
15. eDMA engine writes: CH n _CSR[ACTIVE] = 0, CH n _CSR[DONE] = 1, CH n _INT[INT] = 1.
16. The channel retires, which concludes with the major loop complete. The eDMA goes idle or services the next channel.
14.5.5.3 Using the modulo feature
The modulo feature of the eDMA allows implementation of a circular data queue in which the size of the queue is a power of 2.
xMOD is a 5-bit field for the source and destination in the TCD, and it specifies which lower address bits increment from their
original value after the address+offset calculation. All upper address bits remain the same as in the original value. A setting of 0
for this field disables the modulo feature. Modulo addressing applies to cases where the minor loop offset is enabled; that is, the
upper address bits remain the same after the minor loop offset is added to the source or destination address.
The following table shows how the transfer addresses are specified based on the setting of the MOD field. Here a circular buffer
is created where the address wraps to the original value but the 28 upper address bits (0x1234567 x ) retain their original value.
In this example, the source address is set to 0x12345670, the offset is set to four bytes, and the MOD field is set to four, which
4
allows for a 2 byte (16 byte) queue size.
Table 49. Modulo example
Transfer number Address
1 0x12345670
2 0x12345674
3 0x12345678
4 0x1234567C
5 0x12345670
6 0x12345674

#### 14.5.6 Monitoring transfer descriptor status

This section discusses how to monitor eDMA status.
14.5.6.1 Testing for minor loop completion
There are two methods to test for minor loop completion when using software-initiated service requests.
1. The first method is to read the TCDn_CITER field and test for a change.
2. The second method, extracted from the sequence shown below, is to test the TCDn_CSR[START] field and the
CHn_CSR[ACTIVE] field. The minor-loop-complete condition is indicated by both fields reading 0 after TCDn_CSR[START]
is set to 1. Polling the CHn_CSR[ACTIVE] field only may be inconclusive because the active status may be missed if the
channel execution is short in duration.

---

*Page 279*

Enhanced Direct Memory Access (eDMA)
The CHn_CSR and TCDn_CSR status fields execute the following sequence for a software-activated channel:
TCDn_CSR field
Stage CHn_CSR fields State
START ACTIVE DONE
1 1 0 0 Initiate channel service request via software.
2 0 1 0 Channel is executing.
3a 0 0 0 Channel has completed the minor loop and is idle.
3b 0 0 1 Channel has completed the major loop and is idle.
The best method to test for minor-loop completion when using hardware-initiated (that is, peripheral-initiated) service requests is
to read the TCDn_CITER field and test for a change. The hardware request and acknowledge handshake signals are not visible
in the programmer's model.
The TCD status fields execute the following sequence for a hardware-activated channel:
TCDn_CSR field
Stage CHn_CSR fields State
START ACTIVE DONE
1 0 0 0 Initiate channel service request via hardware (peripheral
request asserted).
2 0 1 0 Channel is executing.
3a 0 0 0 Channel has completed the minor loop and is idle.
3b 0 0 1 Channel has completed the major loop and is idle.
For both activation types, the major-loop-complete status is explicitly indicated via the CHn_CSR[DONE] field.
The TCDn_CSR[START] field is cleared to 0 automatically when the channel begins execution, regardless of how the
channel activates.
14.5.6.2 Reading the transfer descriptors of active channels
The eDMA reads back the true TCDn_SADDR, TCDn_DADDR, and TCDn_NBYTES values if they are read when a channel
executes. The true values of SADDR, DADDR, and NBYTES are the values the eDMA engine currently uses in its internal register
file, and not the values in the TCD local memory for that channel. The addresses, SADDR and DADDR, and NBYTES (which
decrements to zero as the transfer progresses), can give an indication of the progress of the transfer. All other values are read
back from the TCD local memory.
14.5.6.3 Checking channel preemption status
A preemptive situation is one in which a preempt-enabled channel is executing and a higher-priority request becomes active.
When round-robin channel arbitration mode is enabled, all channels with their channel priority set to 0 lose their preempt ability.
Channel priorities of 0 are treated as equal, that is, they are constantly rotating, when round-robin arbitration mode is enabled.
The CHn_CSR[ACTIVE] field for the preempted channel remains asserted throughout the preemption. The preempted channel
is temporarily suspended when the preempting channel executes one major loop iteration. If two CHn_CSR[ACTIVE] fields are
set simultaneously in the global TCD map, a higher-priority channel is actively preempting a lower-priority channel.

---

*Page 280*

Enhanced Direct Memory Access (eDMA)

#### 14.5.7 Channel linking

Channel linking (or chaining) is a mechanism in which one channel sets the TCDn_CSR[START] field of another channel (or itself),
thus initiating a service request for that channel. When properly enabled, the eDMA engine automatically performs this operation
at the major or minor loop completion.
The minor loop channel linking occurs at the completion of the minor loop (or one iteration of the major loop). The
TCDn_CITER[ELINK] field determines whether a minor loop link is requested. When enabled, the channel link is made after each
iteration of the major loop except for the last. When the major loop is exhausted, only the major loop channel link fields are used
to determine if a channel link should be made. For example, using an initial field setting of:
TCDn_CITER[ELINK] = 1
TCDn_CITER[LINKCH] = 0xC
TCDn_CITER[CITER] value = 0x4
TCDn_CSR[MAJORELINK] = 1
TCDn_CSR[MAJORLINKCH] = 0x7
executes as:
1. Minor loop done → set TCD12_CSR[START] field
2. Minor loop done → set TCD12_CSR[START] field
3. Minor loop done → set TCD12_CSR[START] field
4. Minor loop done, major loop done→ set TCD7_CSR[START] field
When minor loop linking is enabled (TCDn_CITER[ELINK] = 1), the TCDn_CITER[CITER] field uses a nine-bit vector to form the
current iteration count. When minor loop linking is disabled (TCDn_CITER[ELINK] = 0), the TCDn_CITER[CITER] field uses a
15-bit vector to form the current iteration count. The bits associated with the TCDn_CITER[LINKCH] field are concatenated onto
the CITER value to increase the range of the CITER.
NOTE
The TCDn_CITER[ELINK] field and the TCDn_BITER[ELINK] field must be equal — if they are not, a configuration
error is reported. The CITER and BITER vector widths must be equal to calculate the major loop halfway done
interrupt point.
The following table summarizes how a DMA channel can link to another DMA channel, that is, use another channel's TCD, at the
end of a loop.
Table 50. Channel linking parameters
Wanted link TCD control field name Description
behavior
Link at end of TCDn_CITER[ELINK] Enable channel-to-channel linking on minor loop completion
minor loop (current iteration)
TCDn_CITER[LINKCH] Link channel number when linking at end of minor loop (current iteration)
Link at end of TCDn_CSR[MAJORELINK] Enable channel-to-channel linking on major loop completion
major loop
TCDn_CSR[MAJORLINKCH] Link channel number when linking at end of major loop

#### 14.5.8 Dynamic programming

This section provides recommended methods to change the programming model during channel execution.

---

*Page 281*

Enhanced Direct Memory Access (eDMA)
14.5.8.1 Dynamically changing the channel priority
To change group or channel priority levels:
1. Halt the DMA by writing 1 to the CSR[HALT] field.
2. Change the group or channel priorities as wanted.
3. Enable normal DMA operations by writing 0 to the CSR[HALT] field.
14.5.8.2 Dynamic channel linking
Dynamic channel linking is the process of setting the TCDn_CSR[MAJORELINK] field during channel execution (see the diagram
in TCD structure ). This field is read from the TCD local memory at the end of channel execution, thus allowing you to enable the
feature during channel execution.
Because you are allowed to change the configuration during execution, you need a coherency model. Consider the scenario
where you attempt to execute a dynamic channel link by enabling the TCDn_CSR[MAJORELINK] field at the same time the
eDMA engine is retiring the channel. TCDn_CSR[MAJORELINK] would be set in the programmer’s model, but it would be unclear
whether the actual link was made before the channel retired.
We recommend that you use the following coherency model when executing a dynamic channel link request.
1. Write 1 to the TCDn_CSR[MAJORELINK] field.
2. Read back the TCDn_CSR[MAJORELINK] field.
3. Test the TCDn_CSR[MAJORELINK] request status:
• If TCDn_CSR[MAJORELINK] = 1, the dynamic link attempt was successful.
• If TCDn_CSR[MAJORELINK] = 0, the attempted dynamic link did not succeed (the channel was already retiring).
For this request, the TCD local memory controller forces the TCDn_CSR[MAJORELINK] field to 0 on any writes to a channel’s
TCDn_CSR[7:0] after that channel’s CHn_CSR[DONE] field is set to 1, indicating the major loop is complete.
NOTE
You must clear the CHn_CSR[DONE] field to 0 before writing to the TCDn_CSR[MAJORELINK] field. The
CHn_CSR[DONE] field is cleared to 0 automatically by the eDMA engine after a channel begins execution.
14.5.8.3 Dynamic scatter/gather
Scatter/gather is the process of automatically loading a new TCD into a channel. It allows a DMA channel to use multiple TCDs; this
enables a DMA channel to scatter the DMA data to multiple destinations or gather it from multiple sources. When scatter/gather
is enabled and the channel has finished its major loop, a new TCD is fetched from system memory and loaded into that channel’s
descriptor location in the eDMA programmer’s model, thus replacing the current descriptor.
Because you are allowed to change the configuration during execution, you need a coherency model. Consider the scenario
where you attempt to execute a dynamic scatter/gather operation by enabling the TCDn_CSR[ESG] field at the same time the
eDMA engine is retiring the channel. The TCDn_CSR[ESG] field would be set in the programmer’s model, but it would be unclear
whether the actual scatter/gather request was honored before the channel retired.
Two methods are recommended for executing a dynamic scatter/gather request. Whenever the TCDn_CSR is written, the TCD
local memory controller forces the TCDn_CSR[ESG] field to 0 on any writes to a channel’s TCDn_CSR [7:0] after that channel’s
CHn_CSR[DONE] field has been set to 1, indicating the major loop is complete. If attempting to set the ESG, ensure the DONE
field is cleared to 0.
NOTE
You must clear the CHn_CSR[DONE] field to 0 before writing the TCDn_CSR[MAJORELINK] or TCDn_CSR[ESG]
fields. The CHn_CSR[DONE] field is cleared to 0 automatically by the eDMA engine after a channel begins
execution and is set to 1 upon major loop completion.

---

*Page 282*

Enhanced Direct Memory Access (eDMA)
14.5.8.3.1 Method 1 (channel not using major loop channel linking)
For a channel not using major loop channel linking, the coherency model described here may be used for a dynamic scatter/
gather request.
When the TCDn_CSR[MAJORELINK] field is 0, the TCDn_CSR[MAJORLINKCH] field is not used by the eDMA. In this case, the
TCDn_CSR[MAJORLINKCH] bits may be used for other purposes. This method uses the TCDn_CSR[MAJORLINKCH] field as
a TCDn_CSR identification (ID).
When the descriptors are built, write a unique TCDn_CSR ID in the TCDn_CSR[MAJORLINKCH] field for each TCDn_CSR
associated with a channel using dynamic scatter/gather.
1. Write a 1 to the TCDn_CSR[DREQ] field. Should a dynamic scatter/gather attempt fail, setting the TCDn_CSR[DREQ]
field to 1 will prevent future hardware activation of this channel. This stops the channel from executing with a destination
address (daddr) that was calculated using a scatter/gather address (written in the next step) instead of a DLAST final
offset value.
2. Write the TCDn_DLAST_SGA field with the scatter/gather address.
3. Write a 1 to the TCDn_CSR[ESG] field.
4. Read back the 16-bit TCDn_CSR control/status field.
5. Test the TCDn_CSR[ESG] request status and TCDn_CSR[MAJORLINKCH] value:
• If ESG = 1, the dynamic scatter/gather attempt was successful.
• If ESG = 0 and the MAJORLINKCH (ID) did not change, the dynamic scatter/gather attempt was not successful (the
channel was already retiring).
• If ESG = 0 and the MAJORLINKCH (ID) changed, the dynamic scatter/gather attempt was successful (the new
TCDn_CSR’s ESG value cleared the ESG field to 0).
14.5.8.3.2 Method 2 (channel using major loop channel linking)
For a channel using major loop channel linking, the coherency model described here may be used for a dynamic scatter/gather
request. This method uses the TCDn_DLAST_SGA field as a TCD identification (ID).
1. Write a 1 to the TCDn_CSR[DREQ] field. Should a dynamic scatter/gather attempt fail, setting the DREQ field to 1 will
prevent a future hardware activation of this channel. This stops the channel from executing with a destination address
(DADDR) that was calculated using a scatter/gather address (written in the next step) instead of a DLAST final offset
value.
2. Write the TCDn_DLAST_SGA field with the scatter/gather address.
3. Write a 1 to the TCDn_CSR[ESG] field.
4. Read back the TCDn_CSR[ESG] field.
5. Test the TCDn_CSR[ESG] request status:
• If ESG = 1, the dynamic scatter/gather attempt was successful.
• If ESG = 0, read the 32-bit TCDn_DLAST_SGA field.
• If ESG = 0 and the TCDn_DLAST_SGA did not change, the dynamic scatter/gather attempt was not successful (the
channel was already retiring).
• If ESG = 0 and the TCDn_DLAST_SGA changed, the dynamic scatter/gather attempt was successful (the new
TCDn_CSR’s ESG value cleared the ESG field to 0).

#### 14.5.9 Suspend/resume a DMA channel with active hardware service requests

The DMA allows you to move data from memory or peripheral registers to another location in memory or to peripheral registers
without CPU interaction. After the DMA and peripherals are configured and active, it is rare but supported to suspend a peripheral's
service request dynamically. In this scenario, there are certain restrictions to disabling a DMA hardware service request. For

---

*Page 283*

Enhanced Direct Memory Access (eDMA)
coherency, you must follow a specific procedure. This section provides guidance on how to coherently suspend and resume a
Direct Memory Access (DMA) channel when the DMA is triggered by a slave module such as the Serial Peripheral Interface
(DSPI), Sigma Delta Analog to Digital Convertor (SDADC), or other module.
14.5.9.1 Suspend an active DMA channel
To suspend an active DMA channel:
1. Stop the DMA service request at the peripheral first. Confirm it has been disabled by reading back the appropriate register
in the peripheral.
2. Check the DMA's Hardware Request Status ( HRS ) to ensure there is no service request to the DMA channel being
suspended. Then disable the hardware service request by clearing the ERQ field to 0 on the appropriate DMA channel.
For example, assume the DSPI is set as a master for transmitting data via a DMA service request when the TXFIFO has an empty
slot. The DMA will transfer the next command and data to the TXFIFO upon the request. If you need to suspend the DMA/DSPI
transfer loop, perform the following steps:
1. Disable the DMA service request at the source by writing 0 to DSPI_RSER[TFFF_RE]. Confirm that
DSPI_RSER[TFFF_RE] is 0.
2. Ensure there is no DMA service request from the DSPI by verifying that HRS[HRS] is 0 for the appropriate channel. If no
service request is present, disable the DMA channel by clearing the channel's ERQ field to 0. If a service request is present,
wait until the request has been processed and the HRS field reads 0.
14.5.9.2 Resume a DMA channel
To resume a DMA channel:
1. Enable the DMA service request on the appropriate channel by setting its ERQ field to 1.
2. Enable the DMA service request at the peripheral.

#### 14.6 Memory map/register definition

The eDMA programming model is partitioned into three parts:
1. The first part defines a number of registers providing overall control functions and is known as the management page.
2. The second part corresponds to the channel (CH) control, status, and configuration.
3. The third part corresponds to the local TCD memory.
TCD memory
Each channel requires a 32-byte transfer control descriptor for defining the data movement operation. Each TCDn definition is
presented as 11 registers of 16 or 32 bits.
TCD initialization
The TCD memory is in an unknown state after reset. Only the TCD START bit is initialized to 0. Prior to activating a channel, you
must initialize its TCD with the appropriate transfer profile.

---

*Page 284*

Enhanced Direct Memory Access (eDMA)
TCD structure
31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
0020h SADDR
0024h SMOD SSIZE DMOD DSIZE SOFF
NBYTES DMA_CR[EMLM] disabled

# {

0028h

# { MLOFF or NBYTES NBYTES DMA_CR[EMLM] enabled

# SMLOE DMLOE {

002Ch SLAST
0030h DADDR
CITER or
0034h CITER DOFF
CITER.LINKCH
Reserved
CITER.ELINK
0038h DLAST_SGA
BITER or
003Ch BITER BWC MAJOR.LINKCH
BITER.LINKCH ESG
ESDA EEOP DREQ START
Reserved Reserved INTHALF
INTMAJOR
BITER.ELINK
MAJOR.ELINK
31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
Figure 33. TCD structure
Accesses to reserved memory and fields
• Reading reserved fields in a register returns the value of zero.
• Writes to reserved fields in a register are ignored.
• Reading or writing a reserved memory location generates a bus error.

#### 14.6.1 eDMA register descriptions

14.6.1.1 eDMA memory map
eDMA base address: 4020_C000h
Offset Register Access Reset value
Width
(In bits)
0h Management Page Control (CSR) 32 RW 0030_0000h
4h Management Page Error Status (ES) 32 R 0000_0000h
8h Management Page Interrupt Request Status (INT) 32 R 0000_0000h
Ch Management Page Hardware Request Status (HRS) 32 R 0000_0000h
100h - 17Ch Channel Arbitration Group (CH0_GRPRI - CH31_GRPRI) 32 RW 0000_0000h

---

*Page 285*

Enhanced Direct Memory Access (eDMA)
14.6.1.2 Management Page Control (CSR)
Offset
Register Offset
CSR 0h
Function
The Management Page Control register defines the basic operating configuration of the DMA.
Arbitration uses a two-tier priority system; group and channel priority. The eDMA assigns each channel to a priority group. Group
arbitration is fixed-priority and cannot be changed. Channel arbitration uses fixed priority and may be configured to use a selective
round-robin scheme for specified channels within each priority group. For fixed-priority arbitration, eDMA selects for execution the
highest priority channel requesting service in the highest priority arbitration group.
The channel priority registers assign the relative priorities within each arbitration group; see CHn_PRI . All channels with a
non-zero CHn_PRI value use fixed-priority arbitration.
When you enable round-robin arbitration, all channels with channel priority set to zero do not have a priority and, of those channels
requesting service, are cycled through (from high to low channel number) without regard to priority relative to each other within
the same priority group. Any channel with a non-zero CHn_PRI value automatically has a higher priority over the round-robin
channels. A channel's priority group is assigned in Channel Arbitration Group (CH0_GRPRI - CH31_GRPRI) .
NOTE
For correct operation, changes to the CSR[ERCA, GCLC, GMRC] fields must be performed when the DMA
channels are inactive; that is, when the CSR[ACTIVE] field is 0.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
ACTIV
R Reserved ACTIVE_ID Reserved
E
W
Reset 0 0 0 0 0 0 0 0 0 0 1 1 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
Reserv Reserv
R Reserved
ed ed
CX ECX GMRC GCLC HALT HAE ERCA EDBG
W 0 0 0
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
DMA Active Status
31
0b - eDMA is idle
ACTIVE
1b - eDMA is executing a channel
Table continues on the next page...

---

*Page 286*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Field Function
Reserved
30-29
—
28-24 Active Channel ID
ACTIVE_ID This field identifies the channel number that is executing when the ACTIVE bit is 1.
Reserved
23-16
—
Reserved
15-10
—
9 Cancel Transfer
CX When set to 1, this field cancels the remaining data transfer, stops the executing channel, and forces the
minor loop to finish. The cancel takes effect after the last write of the current read/write sequence. CX
clears itself to 0 after the cancel has been honored. This cancel retires the channel normally as if the
minor loop had been completed.
0b - Normal operation
1b - Cancel the remaining data transfer
8 Cancel Transfer With Error
ECX Cancellation of the remaining data transfer is similar to that of the CX field. Execution of the the channel
is stopped and the minor loop is forced to finish. The cancellation takes effect after the last write of the
current read/write sequence. The ECX field clears itself to 0 after the cancel is honored. In addition to
cancelling the transfer, ECX treats the cancel as an error condition, thus updating Management Page
Error Status (ES) and generating an optional error interrupt.
0b - Normal operation
1b - Cancel the remaining data transfer
7 Global Master ID Replication Control
GMRC
NOTE
If master ID replication is disabled, the privileged protection level (Supervisor mode) for
DMA transfers is used.
0b - Master ID replication disabled for all channels
1b - Master ID replication available and controlled by each channel's CHn_SBR[EMI] setting
Global Channel Linking Control
6
0b - Channel linking disabled for all channels
GCLC
1b - Channel linking available and controlled by each channel's link settings
5 Halt DMA Operations
Table continues on the next page...

---

*Page 287*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Field Function
This field stalls the start of any new channels. Executing channels are allowed to complete. Channel
HALT
execution resumes when this field is cleared to 0.
0b - Normal operation
1b - Stall the start of any new channels
4 Halt After Error
HAE When this field is set to 1, any error causes the HALT field to be set to 1. Then all service requests are
ignored until the HALT field is cleared to 0.
0b - Normal operation
1b - Any error causes the HALT field to be set to 1
Reserved
3
—
Enable Round Robin Channel Arbitration
2
0b - Round-robin channel arbitration disabled. Fixed priority arbitration used for channel selection
ERCA
within each group
1b - Round-robin channel arbitration enabled. Round-robin arbitration used for channel selection
within each group
1 Enable Debug
EDBG When in debug mode, the DMA stalls the start of a new channel. Executing channels are allowed to
complete. DMA resumes channel execution when the system exits debug mode or clears the EDBG field
to 0.
0b - Debug mode disabled. When in debug mode, the DMA continues to operate
1b - Debug mode is enabled. When in debug mode, the DMA stalls the start of a new channel
Reserved
0
—
14.6.1.3 Management Page Error Status (ES)
Offset
Register Offset
ES 4h
Function
The ES provides information concerning the last recorded channel error. Channel errors can be caused by:
• An illegal setting in the transfer control descriptor
• An error termination to a bus master read or write cycle

---

*Page 288*

Enhanced Direct Memory Access (eDMA)
• An uncorrectable error that occurred when the device was accessing the TCD SRAM
• A "cancel transfer with error" request was made via the corresponding cancel transfer field or input signal
Upon any error condition, the software must initialize the TCD of the channel that contains the error, as it is in an incomplete state
after an error. See Fault reporting and handling for more details.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R VLD Reserved ERRCHN 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 UCE ECX SAE SOE DAE DOE NCE SGE SBE DBE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Valid
VLD Logical OR of all ERR status fields.
0b - No ERR fields are set to 1
1b - At least one ERR field is set to 1, indicating a valid error exists that software has not cleared
Reserved
30-29
—
28-24 Error Channel Number or Canceled Channel Number
ERRCHN The channel number of the last recorded error or last recorded error-canceled transfer.
Reserved
23-10
—
9 Uncorrectable TCD Error During Channel Execution
UCE UCE is set to 1 only when an uncorrectable ECC error occurs on an access generated by the DMA. If a CPU
access to the TCD causes an uncorrectable ECC error, then that access receives a bus error response.
NOTE
When the eDMA sees a RAM error on an IPS access (when you are accessing a TCD), it
reports the error as a bus abort. When the DMA engine receives a RAM error (the execution
engine is accessing a TCD) it is recorded in the Error Status register, ES[UCE], along with
the channel number.
Table continues on the next page...

---

*Page 289*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Field Function
0b - No uncorrectable ECC error
1b - Last recorded error was an uncorrectable TCD RAM error
8 Transfer Canceled
ECX The ECX operation is a management page function. When employed, the targeted channel's CHn_ES
register reports an unspecified error; that is, only the ERR field is set to 1. The management page has
full view of the error condition.
0b - No canceled transfers
1b - Last recorded entry was a canceled transfer by the error cancel transfer input
7 Source Address Error
SAE When this field is 1, it indicates that TCDn_SADDR is inconsistent with TCDn_ATTR[SSIZE].
0b - No source address configuration error
1b - Last recorded error was a configuration error detected in the TCDn_SADDR field
6 Source Offset Error
SOE When this field is 1, it indicates that TCDn_SOFF is inconsistent with TCDn_ATTR[SSIZE].
0b - No source offset configuration error
1b - Last recorded error was a configuration error detected in the TCDn_SOFF field
5 Destination Address Error
DAE When this field is 1, it indicates that TCDn_DADDR is inconsistent with TCDn_ATTR[DSIZE].
0b - No destination address configuration error
1b - Last recorded error was a configuration error detected in the TCDn_DADDR field
4 Destination Offset Error
DOE When this field is 1, it indicates that TCDn_DOFF is inconsistent with TCDn_ATTR[DSIZE].
0b - No destination offset configuration error
1b - Last recorded error was a configuration error detected in the TCDn_DOFF field
3 NBYTES/CITER Configuration Error
NCE This error indicates that one of the following has occurred:
• TCDn_NBYTES is not a multiple of TCDn_ATTR[SSIZE] and TCDn_ATTR[DSIZE]
• TCDn_CITER[CITER] is equal to zero
• TCDn_CITER[ELINK] is not equal to TCDn_BITER[ELINK]
0b - No NBYTES/CITER configuration error
1b - The last recorded error was NBYTES equal to zero or a CITER not equal to BITER error.
Last recorded error was a configuration error detected in the TCDn_NBYTES or TCDn_CITER
fields
Table continues on the next page...

---

*Page 290*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Field Function
2 Scatter/Gather Configuration Error
SGE When this field is 1, it indicates that TCDn_DLAST_SGA is not on a 32-byte boundary. This field is
checked at the beginning of a scatter/gather operation after major loop completion if TCDn_CSR[ESG] is
enabled.
0b - No scatter/gather configuration error
1b - Last recorded error was a configuration error detected in the TCDn_DLAST_SGA field
Source Bus Error
1
0b - No source bus error
SBE
1b - Last recorded error was a bus error on a source read
Destination Bus Error
0
0b - No destination bus error
DBE
1b - Last recorded error was a bus error on a destination write
14.6.1.4 Management Page Interrupt Request Status (INT)
Offset
Register Offset
INT 8h
Function
This register shows the current state of the interrupt service requests for all eDMA channels.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R INT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R INT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 291*

Enhanced Direct Memory Access (eDMA)
Fields
Field Function
31-0 Interrupt Request Status
INT The INT register presents the interrupt request status for each eDMA channel. Depending on the
appropriate field setting in the transfer control descriptors, the eDMA engine generates an interrupt
on data transfer completion or an error condition . The eDMA routes channel interrupt requests to the
interrupt controller. During the interrupt service routine associated with any given channel, it is the software's
responsibility to clear the appropriate field in the channel’s interrupt request register, CHn_INT, thus
negating the interrupt request.
0b - Interrupt request for corresponding channel not present
1b - Interrupt request for corresponding channel present
14.6.1.5 Management Page Hardware Request Status (HRS)
Offset
Register Offset
HRS Ch
Function
The hardware request status register (HRS) shows the current state of the hardware service request signaling as seen by eDMA's
arbitration logic.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R HRS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R HRS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Hardware Request Status
HRS
Table continues on the next page...

---

*Page 292*

Enhanced Direct Memory Access (eDMA)
Field Function
The HRS bit for its respective channel remains asserted for the period when a hardware request is present
on the channel. After the request is completed and the channel is free, the hardware automatically clears
the corresponding HRS bit to 0.
0b - Hardware service request for corresponding channel is not present
1b - Hardware service request for corresponding channel is present
14.6.1.6 Channel Arbitration Group (CH0_GRPRI - CH31_GRPRI)
Offset
For n = 0 to 31:
Register Offset
CHn_GRPRI 100h + (n × 4h)
Function
The contents of this register define the arbitration group associated with each channel. Using a fixed-priority group arbitration
scheme, eDMA evaluates the arbitration group priorities by numeric value from highest group number to lowest; for example, 0 is
the lowest priority, 1 is the next higher priority, then 2, 3, and so on. The range of the group priority values is limited to the values
of 0 through 31. Within each arbitration group, the channel priority assignment CH n _PRI determines the highest-priority channel.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
GRPRI
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-5
—
4-0 Arbitration Group For Channel n
GRPRI Fixed-priority arbitration group number.

---

*Page 293*

Enhanced Direct Memory Access (eDMA)

#### 14.6.2 TCD register descriptions

14.6.2.1 TCD memory map
TCD base address: 4021_0000h
Offset Register Access Reset value
Width
(In bits)
0h Channel Control and Status (CH0_CSR) 32 RW 0000_0000h
4h Channel Error Status (CH0_ES) 32 RW 0000_0000h
8h Channel Interrupt Status (CH0_INT) 32 RW 0000_0000h
Ch Channel System Bus (CH0_SBR) 32 RW 0000_8002h
10h Channel Priority (CH0_PRI) 32 RW 0000_0000h
20h TCD Source Address (TCD0_SADDR) 32 RW See section
24h TCD Signed Source Address Offset (TCD0_SOFF) 16 RW See section
26h TCD Transfer Attributes (TCD0_ATTR) 16 RW See section
28h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD0_NBYTES_MLOFFNO)
28h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD0_NBYTES_MLOFFYES)
2Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD0_SLAST_SDA)
30h TCD Destination Address (TCD0_DADDR) 32 RW See section
34h TCD Signed Destination Address Offset (TCD0_DOFF) 16 RW See section
36h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD0_CITER_ELINKNO)
36h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD0_CITER_ELINKYES)
38h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD0_DLAST_SGA)
3Ch TCD Control and Status (TCD0_CSR) 16 RW See section
3Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD0_BITER_ELINKNO)
3Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD0_BITER_ELINKYES)
4000h Channel Control and Status (CH1_CSR) 32 RW 0000_0000h
4004h Channel Error Status (CH1_ES) 32 RW 0000_0000h
4008h Channel Interrupt Status (CH1_INT) 32 RW 0000_0000h
400Ch Channel System Bus (CH1_SBR) 32 RW 0000_8002h
Table continues on the next page...

---

*Page 294*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
4010h Channel Priority (CH1_PRI) 32 RW 0000_0000h
4020h TCD Source Address (TCD1_SADDR) 32 RW See section
4024h TCD Signed Source Address Offset (TCD1_SOFF) 16 RW See section
4026h TCD Transfer Attributes (TCD1_ATTR) 16 RW See section
4028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD1_NBYTES_MLOFFNO)
4028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD1_NBYTES_MLOFFYES)
402Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD1_SLAST_SDA)
4030h TCD Destination Address (TCD1_DADDR) 32 RW See section
4034h TCD Signed Destination Address Offset (TCD1_DOFF) 16 RW See section
4036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD1_CITER_ELINKNO)
4036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD1_CITER_ELINKYES)
4038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD1_DLAST_SGA)
403Ch TCD Control and Status (TCD1_CSR) 16 RW See section
403Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD1_BITER_ELINKNO)
403Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD1_BITER_ELINKYES)
8000h Channel Control and Status (CH2_CSR) 32 RW 0000_0000h
8004h Channel Error Status (CH2_ES) 32 RW 0000_0000h
8008h Channel Interrupt Status (CH2_INT) 32 RW 0000_0000h
800Ch Channel System Bus (CH2_SBR) 32 RW 0000_8002h
8010h Channel Priority (CH2_PRI) 32 RW 0000_0000h
8020h TCD Source Address (TCD2_SADDR) 32 RW See section
8024h TCD Signed Source Address Offset (TCD2_SOFF) 16 RW See section
8026h TCD Transfer Attributes (TCD2_ATTR) 16 RW See section
8028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD2_NBYTES_MLOFFNO)
Table continues on the next page...

---

*Page 295*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
8028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD2_NBYTES_MLOFFYES)
802Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD2_SLAST_SDA)
8030h TCD Destination Address (TCD2_DADDR) 32 RW See section
8034h TCD Signed Destination Address Offset (TCD2_DOFF) 16 RW See section
8036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD2_CITER_ELINKNO)
8036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD2_CITER_ELINKYES)
8038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD2_DLAST_SGA)
803Ch TCD Control and Status (TCD2_CSR) 16 RW See section
803Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD2_BITER_ELINKNO)
803Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD2_BITER_ELINKYES)
C000h Channel Control and Status (CH3_CSR) 32 RW 0000_0000h
C004h Channel Error Status (CH3_ES) 32 RW 0000_0000h
C008h Channel Interrupt Status (CH3_INT) 32 RW 0000_0000h
C00Ch Channel System Bus (CH3_SBR) 32 RW 0000_8002h
C010h Channel Priority (CH3_PRI) 32 RW 0000_0000h
C020h TCD Source Address (TCD3_SADDR) 32 RW See section
C024h TCD Signed Source Address Offset (TCD3_SOFF) 16 RW See section
C026h TCD Transfer Attributes (TCD3_ATTR) 16 RW See section
C028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD3_NBYTES_MLOFFNO)
C028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD3_NBYTES_MLOFFYES)
C02Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD3_SLAST_SDA)
C030h TCD Destination Address (TCD3_DADDR) 32 RW See section
C034h TCD Signed Destination Address Offset (TCD3_DOFF) 16 RW See section
Table continues on the next page...

---

*Page 296*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
C036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD3_CITER_ELINKNO)
C036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD3_CITER_ELINKYES)
C038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD3_DLAST_SGA)
C03Ch TCD Control and Status (TCD3_CSR) 16 RW See section
C03Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD3_BITER_ELINKNO)
C03Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD3_BITER_ELINKYES)
1_0000h Channel Control and Status (CH4_CSR) 32 RW 0000_0000h
1_0004h Channel Error Status (CH4_ES) 32 RW 0000_0000h
1_0008h Channel Interrupt Status (CH4_INT) 32 RW 0000_0000h
1_000Ch Channel System Bus (CH4_SBR) 32 RW 0000_8002h
1_0010h Channel Priority (CH4_PRI) 32 RW 0000_0000h
1_0020h TCD Source Address (TCD4_SADDR) 32 RW See section
1_0024h TCD Signed Source Address Offset (TCD4_SOFF) 16 RW See section
1_0026h TCD Transfer Attributes (TCD4_ATTR) 16 RW See section
1_0028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD4_NBYTES_MLOFFNO)
1_0028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD4_NBYTES_MLOFFYES)
1_002Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD4_SLAST_SDA)
1_0030h TCD Destination Address (TCD4_DADDR) 32 RW See section
1_0034h TCD Signed Destination Address Offset (TCD4_DOFF) 16 RW See section
1_0036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD4_CITER_ELINKNO)
1_0036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD4_CITER_ELINKYES)
1_0038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD4_DLAST_SGA)
1_003Ch TCD Control and Status (TCD4_CSR) 16 RW See section
Table continues on the next page...

---

*Page 297*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
1_003Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD4_BITER_ELINKNO)
1_003Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD4_BITER_ELINKYES)
1_4000h Channel Control and Status (CH5_CSR) 32 RW 0000_0000h
1_4004h Channel Error Status (CH5_ES) 32 RW 0000_0000h
1_4008h Channel Interrupt Status (CH5_INT) 32 RW 0000_0000h
1_400Ch Channel System Bus (CH5_SBR) 32 RW 0000_8002h
1_4010h Channel Priority (CH5_PRI) 32 RW 0000_0000h
1_4020h TCD Source Address (TCD5_SADDR) 32 RW See section
1_4024h TCD Signed Source Address Offset (TCD5_SOFF) 16 RW See section
1_4026h TCD Transfer Attributes (TCD5_ATTR) 16 RW See section
1_4028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD5_NBYTES_MLOFFNO)
1_4028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD5_NBYTES_MLOFFYES)
1_402Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD5_SLAST_SDA)
1_4030h TCD Destination Address (TCD5_DADDR) 32 RW See section
1_4034h TCD Signed Destination Address Offset (TCD5_DOFF) 16 RW See section
1_4036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD5_CITER_ELINKNO)
1_4036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD5_CITER_ELINKYES)
1_4038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD5_DLAST_SGA)
1_403Ch TCD Control and Status (TCD5_CSR) 16 RW See section
1_403Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD5_BITER_ELINKNO)
1_403Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD5_BITER_ELINKYES)
1_8000h Channel Control and Status (CH6_CSR) 32 RW 0000_0000h
1_8004h Channel Error Status (CH6_ES) 32 RW 0000_0000h
1_8008h Channel Interrupt Status (CH6_INT) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 298*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
1_800Ch Channel System Bus (CH6_SBR) 32 RW 0000_8002h
1_8010h Channel Priority (CH6_PRI) 32 RW 0000_0000h
1_8020h TCD Source Address (TCD6_SADDR) 32 RW See section
1_8024h TCD Signed Source Address Offset (TCD6_SOFF) 16 RW See section
1_8026h TCD Transfer Attributes (TCD6_ATTR) 16 RW See section
1_8028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD6_NBYTES_MLOFFNO)
1_8028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD6_NBYTES_MLOFFYES)
1_802Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD6_SLAST_SDA)
1_8030h TCD Destination Address (TCD6_DADDR) 32 RW See section
1_8034h TCD Signed Destination Address Offset (TCD6_DOFF) 16 RW See section
1_8036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD6_CITER_ELINKNO)
1_8036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD6_CITER_ELINKYES)
1_8038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD6_DLAST_SGA)
1_803Ch TCD Control and Status (TCD6_CSR) 16 RW See section
1_803Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD6_BITER_ELINKNO)
1_803Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD6_BITER_ELINKYES)
1_C000h Channel Control and Status (CH7_CSR) 32 RW 0000_0000h
1_C004h Channel Error Status (CH7_ES) 32 RW 0000_0000h
1_C008h Channel Interrupt Status (CH7_INT) 32 RW 0000_0000h
1_C00Ch Channel System Bus (CH7_SBR) 32 RW 0000_8002h
1_C010h Channel Priority (CH7_PRI) 32 RW 0000_0000h
1_C020h TCD Source Address (TCD7_SADDR) 32 RW See section
1_C024h TCD Signed Source Address Offset (TCD7_SOFF) 16 RW See section
1_C026h TCD Transfer Attributes (TCD7_ATTR) 16 RW See section
1_C028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD7_NBYTES_MLOFFNO)
Table continues on the next page...

---

*Page 299*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
1_C028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD7_NBYTES_MLOFFYES)
1_C02Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD7_SLAST_SDA)
1_C030h TCD Destination Address (TCD7_DADDR) 32 RW See section
1_C034h TCD Signed Destination Address Offset (TCD7_DOFF) 16 RW See section
1_C036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD7_CITER_ELINKNO)
1_C036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD7_CITER_ELINKYES)
1_C038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD7_DLAST_SGA)
1_C03Ch TCD Control and Status (TCD7_CSR) 16 RW See section
1_C03Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD7_BITER_ELINKNO)
1_C03Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD7_BITER_ELINKYES)
2_0000h Channel Control and Status (CH8_CSR) 32 RW 0000_0000h
2_0004h Channel Error Status (CH8_ES) 32 RW 0000_0000h
2_0008h Channel Interrupt Status (CH8_INT) 32 RW 0000_0000h
2_000Ch Channel System Bus (CH8_SBR) 32 RW 0000_8002h
2_0010h Channel Priority (CH8_PRI) 32 RW 0000_0000h
2_0020h TCD Source Address (TCD8_SADDR) 32 RW See section
2_0024h TCD Signed Source Address Offset (TCD8_SOFF) 16 RW See section
2_0026h TCD Transfer Attributes (TCD8_ATTR) 16 RW See section
2_0028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD8_NBYTES_MLOFFNO)
2_0028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD8_NBYTES_MLOFFYES)
2_002Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD8_SLAST_SDA)
2_0030h TCD Destination Address (TCD8_DADDR) 32 RW See section
2_0034h TCD Signed Destination Address Offset (TCD8_DOFF) 16 RW See section
Table continues on the next page...

---

*Page 300*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
2_0036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD8_CITER_ELINKNO)
2_0036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD8_CITER_ELINKYES)
2_0038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD8_DLAST_SGA)
2_003Ch TCD Control and Status (TCD8_CSR) 16 RW See section
2_003Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD8_BITER_ELINKNO)
2_003Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD8_BITER_ELINKYES)
2_4000h Channel Control and Status (CH9_CSR) 32 RW 0000_0000h
2_4004h Channel Error Status (CH9_ES) 32 RW 0000_0000h
2_4008h Channel Interrupt Status (CH9_INT) 32 RW 0000_0000h
2_400Ch Channel System Bus (CH9_SBR) 32 RW 0000_8002h
2_4010h Channel Priority (CH9_PRI) 32 RW 0000_0000h
2_4020h TCD Source Address (TCD9_SADDR) 32 RW See section
2_4024h TCD Signed Source Address Offset (TCD9_SOFF) 16 RW See section
2_4026h TCD Transfer Attributes (TCD9_ATTR) 16 RW See section
2_4028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD9_NBYTES_MLOFFNO)
2_4028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD9_NBYTES_MLOFFYES)
2_402Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD9_SLAST_SDA)
2_4030h TCD Destination Address (TCD9_DADDR) 32 RW See section
2_4034h TCD Signed Destination Address Offset (TCD9_DOFF) 16 RW See section
2_4036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD9_CITER_ELINKNO)
2_4036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD9_CITER_ELINKYES)
2_4038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD9_DLAST_SGA)
2_403Ch TCD Control and Status (TCD9_CSR) 16 RW See section
Table continues on the next page...

---

*Page 301*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
2_403Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD9_BITER_ELINKNO)
2_403Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD9_BITER_ELINKYES)
2_8000h Channel Control and Status (CH10_CSR) 32 RW 0000_0000h
2_8004h Channel Error Status (CH10_ES) 32 RW 0000_0000h
2_8008h Channel Interrupt Status (CH10_INT) 32 RW 0000_0000h
2_800Ch Channel System Bus (CH10_SBR) 32 RW 0000_8002h
2_8010h Channel Priority (CH10_PRI) 32 RW 0000_0000h
2_8020h TCD Source Address (TCD10_SADDR) 32 RW See section
2_8024h TCD Signed Source Address Offset (TCD10_SOFF) 16 RW See section
2_8026h TCD Transfer Attributes (TCD10_ATTR) 16 RW See section
2_8028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD10_NBYTES_MLOFFNO)
2_8028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD10_NBYTES_MLOFFYES)
2_802Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD10_SLAST_SDA)
2_8030h TCD Destination Address (TCD10_DADDR) 32 RW See section
2_8034h TCD Signed Destination Address Offset (TCD10_DOFF) 16 RW See section
2_8036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD10_CITER_ELINKNO)
2_8036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD10_CITER_ELINKYES)
2_8038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD10_DLAST_SGA)
2_803Ch TCD Control and Status (TCD10_CSR) 16 RW See section
2_803Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD10_BITER_ELINKNO)
2_803Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD10_BITER_ELINKYES)
2_C000h Channel Control and Status (CH11_CSR) 32 RW 0000_0000h
2_C004h Channel Error Status (CH11_ES) 32 RW 0000_0000h
2_C008h Channel Interrupt Status (CH11_INT) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 302*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
2_C00Ch Channel System Bus (CH11_SBR) 32 RW 0000_8002h
2_C010h Channel Priority (CH11_PRI) 32 RW 0000_0000h
2_C020h TCD Source Address (TCD11_SADDR) 32 RW See section
2_C024h TCD Signed Source Address Offset (TCD11_SOFF) 16 RW See section
2_C026h TCD Transfer Attributes (TCD11_ATTR) 16 RW See section
2_C028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD11_NBYTES_MLOFFNO)
2_C028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD11_NBYTES_MLOFFYES)
2_C02Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD11_SLAST_SDA)
2_C030h TCD Destination Address (TCD11_DADDR) 32 RW See section
2_C034h TCD Signed Destination Address Offset (TCD11_DOFF) 16 RW See section
2_C036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD11_CITER_ELINKNO)
2_C036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD11_CITER_ELINKYES)
2_C038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD11_DLAST_SGA)
2_C03Ch TCD Control and Status (TCD11_CSR) 16 RW See section
2_C03Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD11_BITER_ELINKNO)
2_C03Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD11_BITER_ELINKYES)
20_0000h Channel Control and Status (CH12_CSR) 32 RW 0000_0000h
20_0004h Channel Error Status (CH12_ES) 32 RW 0000_0000h
20_0008h Channel Interrupt Status (CH12_INT) 32 RW 0000_0000h
20_000Ch Channel System Bus (CH12_SBR) 32 RW 0000_8002h
20_0010h Channel Priority (CH12_PRI) 32 RW 0000_0000h
20_0020h TCD Source Address (TCD12_SADDR) 32 RW See section
20_0024h TCD Signed Source Address Offset (TCD12_SOFF) 16 RW See section
20_0026h TCD Transfer Attributes (TCD12_ATTR) 16 RW See section
20_0028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD12_NBYTES_MLOFFNO)
Table continues on the next page...

---

*Page 303*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
20_0028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD12_NBYTES_MLOFFYES)
20_002Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD12_SLAST_SDA)
20_0030h TCD Destination Address (TCD12_DADDR) 32 RW See section
20_0034h TCD Signed Destination Address Offset (TCD12_DOFF) 16 RW See section
20_0036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD12_CITER_ELINKNO)
20_0036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD12_CITER_ELINKYES)
20_0038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD12_DLAST_SGA)
20_003Ch TCD Control and Status (TCD12_CSR) 16 RW See section
20_003Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD12_BITER_ELINKNO)
20_003Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD12_BITER_ELINKYES)
20_4000h Channel Control and Status (CH13_CSR) 32 RW 0000_0000h
20_4004h Channel Error Status (CH13_ES) 32 RW 0000_0000h
20_4008h Channel Interrupt Status (CH13_INT) 32 RW 0000_0000h
20_400Ch Channel System Bus (CH13_SBR) 32 RW 0000_8002h
20_4010h Channel Priority (CH13_PRI) 32 RW 0000_0000h
20_4020h TCD Source Address (TCD13_SADDR) 32 RW See section
20_4024h TCD Signed Source Address Offset (TCD13_SOFF) 16 RW See section
20_4026h TCD Transfer Attributes (TCD13_ATTR) 16 RW See section
20_4028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD13_NBYTES_MLOFFNO)
20_4028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD13_NBYTES_MLOFFYES)
20_402Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD13_SLAST_SDA)
20_4030h TCD Destination Address (TCD13_DADDR) 32 RW See section
20_4034h TCD Signed Destination Address Offset (TCD13_DOFF) 16 RW See section
Table continues on the next page...

---

*Page 304*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
20_4036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD13_CITER_ELINKNO)
20_4036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD13_CITER_ELINKYES)
20_4038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD13_DLAST_SGA)
20_403Ch TCD Control and Status (TCD13_CSR) 16 RW See section
20_403Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD13_BITER_ELINKNO)
20_403Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD13_BITER_ELINKYES)
20_8000h Channel Control and Status (CH14_CSR) 32 RW 0000_0000h
20_8004h Channel Error Status (CH14_ES) 32 RW 0000_0000h
20_8008h Channel Interrupt Status (CH14_INT) 32 RW 0000_0000h
20_800Ch Channel System Bus (CH14_SBR) 32 RW 0000_8002h
20_8010h Channel Priority (CH14_PRI) 32 RW 0000_0000h
20_8020h TCD Source Address (TCD14_SADDR) 32 RW See section
20_8024h TCD Signed Source Address Offset (TCD14_SOFF) 16 RW See section
20_8026h TCD Transfer Attributes (TCD14_ATTR) 16 RW See section
20_8028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD14_NBYTES_MLOFFNO)
20_8028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD14_NBYTES_MLOFFYES)
20_802Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD14_SLAST_SDA)
20_8030h TCD Destination Address (TCD14_DADDR) 32 RW See section
20_8034h TCD Signed Destination Address Offset (TCD14_DOFF) 16 RW See section
20_8036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD14_CITER_ELINKNO)
20_8036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD14_CITER_ELINKYES)
20_8038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD14_DLAST_SGA)
20_803Ch TCD Control and Status (TCD14_CSR) 16 RW See section
Table continues on the next page...

---

*Page 305*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
20_803Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD14_BITER_ELINKNO)
20_803Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD14_BITER_ELINKYES)
20_C000h Channel Control and Status (CH15_CSR) 32 RW 0000_0000h
20_C004h Channel Error Status (CH15_ES) 32 RW 0000_0000h
20_C008h Channel Interrupt Status (CH15_INT) 32 RW 0000_0000h
20_C00Ch Channel System Bus (CH15_SBR) 32 RW 0000_8002h
20_C010h Channel Priority (CH15_PRI) 32 RW 0000_0000h
20_C020h TCD Source Address (TCD15_SADDR) 32 RW See section
20_C024h TCD Signed Source Address Offset (TCD15_SOFF) 16 RW See section
20_C026h TCD Transfer Attributes (TCD15_ATTR) 16 RW See section
20_C028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD15_NBYTES_MLOFFNO)
20_C028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD15_NBYTES_MLOFFYES)
20_C02Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD15_SLAST_SDA)
20_C030h TCD Destination Address (TCD15_DADDR) 32 RW See section
20_C034h TCD Signed Destination Address Offset (TCD15_DOFF) 16 RW See section
20_C036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD15_CITER_ELINKNO)
20_C036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD15_CITER_ELINKYES)
20_C038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD15_DLAST_SGA)
20_C03Ch TCD Control and Status (TCD15_CSR) 16 RW See section
20_C03Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD15_BITER_ELINKNO)
20_C03Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD15_BITER_ELINKYES)
21_0000h Channel Control and Status (CH16_CSR) 32 RW 0000_0000h
21_0004h Channel Error Status (CH16_ES) 32 RW 0000_0000h
21_0008h Channel Interrupt Status (CH16_INT) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 306*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
21_000Ch Channel System Bus (CH16_SBR) 32 RW 0000_8002h
21_0010h Channel Priority (CH16_PRI) 32 RW 0000_0000h
21_0020h TCD Source Address (TCD16_SADDR) 32 RW See section
21_0024h TCD Signed Source Address Offset (TCD16_SOFF) 16 RW See section
21_0026h TCD Transfer Attributes (TCD16_ATTR) 16 RW See section
21_0028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD16_NBYTES_MLOFFNO)
21_0028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD16_NBYTES_MLOFFYES)
21_002Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD16_SLAST_SDA)
21_0030h TCD Destination Address (TCD16_DADDR) 32 RW See section
21_0034h TCD Signed Destination Address Offset (TCD16_DOFF) 16 RW See section
21_0036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD16_CITER_ELINKNO)
21_0036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD16_CITER_ELINKYES)
21_0038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD16_DLAST_SGA)
21_003Ch TCD Control and Status (TCD16_CSR) 16 RW See section
21_003Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD16_BITER_ELINKNO)
21_003Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD16_BITER_ELINKYES)
21_4000h Channel Control and Status (CH17_CSR) 32 RW 0000_0000h
21_4004h Channel Error Status (CH17_ES) 32 RW 0000_0000h
21_4008h Channel Interrupt Status (CH17_INT) 32 RW 0000_0000h
21_400Ch Channel System Bus (CH17_SBR) 32 RW 0000_8002h
21_4010h Channel Priority (CH17_PRI) 32 RW 0000_0000h
21_4020h TCD Source Address (TCD17_SADDR) 32 RW See section
21_4024h TCD Signed Source Address Offset (TCD17_SOFF) 16 RW See section
21_4026h TCD Transfer Attributes (TCD17_ATTR) 16 RW See section
21_4028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD17_NBYTES_MLOFFNO)
Table continues on the next page...

---

*Page 307*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
21_4028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD17_NBYTES_MLOFFYES)
21_402Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD17_SLAST_SDA)
21_4030h TCD Destination Address (TCD17_DADDR) 32 RW See section
21_4034h TCD Signed Destination Address Offset (TCD17_DOFF) 16 RW See section
21_4036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD17_CITER_ELINKNO)
21_4036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD17_CITER_ELINKYES)
21_4038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD17_DLAST_SGA)
21_403Ch TCD Control and Status (TCD17_CSR) 16 RW See section
21_403Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD17_BITER_ELINKNO)
21_403Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD17_BITER_ELINKYES)
21_8000h Channel Control and Status (CH18_CSR) 32 RW 0000_0000h
21_8004h Channel Error Status (CH18_ES) 32 RW 0000_0000h
21_8008h Channel Interrupt Status (CH18_INT) 32 RW 0000_0000h
21_800Ch Channel System Bus (CH18_SBR) 32 RW 0000_8002h
21_8010h Channel Priority (CH18_PRI) 32 RW 0000_0000h
21_8020h TCD Source Address (TCD18_SADDR) 32 RW See section
21_8024h TCD Signed Source Address Offset (TCD18_SOFF) 16 RW See section
21_8026h TCD Transfer Attributes (TCD18_ATTR) 16 RW See section
21_8028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD18_NBYTES_MLOFFNO)
21_8028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD18_NBYTES_MLOFFYES)
21_802Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD18_SLAST_SDA)
21_8030h TCD Destination Address (TCD18_DADDR) 32 RW See section
21_8034h TCD Signed Destination Address Offset (TCD18_DOFF) 16 RW See section
Table continues on the next page...

---

*Page 308*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
21_8036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD18_CITER_ELINKNO)
21_8036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD18_CITER_ELINKYES)
21_8038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD18_DLAST_SGA)
21_803Ch TCD Control and Status (TCD18_CSR) 16 RW See section
21_803Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD18_BITER_ELINKNO)
21_803Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD18_BITER_ELINKYES)
21_C000h Channel Control and Status (CH19_CSR) 32 RW 0000_0000h
21_C004h Channel Error Status (CH19_ES) 32 RW 0000_0000h
21_C008h Channel Interrupt Status (CH19_INT) 32 RW 0000_0000h
21_C00Ch Channel System Bus (CH19_SBR) 32 RW 0000_8002h
21_C010h Channel Priority (CH19_PRI) 32 RW 0000_0000h
21_C020h TCD Source Address (TCD19_SADDR) 32 RW See section
21_C024h TCD Signed Source Address Offset (TCD19_SOFF) 16 RW See section
21_C026h TCD Transfer Attributes (TCD19_ATTR) 16 RW See section
21_C028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD19_NBYTES_MLOFFNO)
21_C028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD19_NBYTES_MLOFFYES)
21_C02Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD19_SLAST_SDA)
21_C030h TCD Destination Address (TCD19_DADDR) 32 RW See section
21_C034h TCD Signed Destination Address Offset (TCD19_DOFF) 16 RW See section
21_C036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD19_CITER_ELINKNO)
21_C036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD19_CITER_ELINKYES)
21_C038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD19_DLAST_SGA)
21_C03Ch TCD Control and Status (TCD19_CSR) 16 RW See section
Table continues on the next page...

---

*Page 309*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
21_C03Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD19_BITER_ELINKNO)
21_C03Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD19_BITER_ELINKYES)
22_0000h Channel Control and Status (CH20_CSR) 32 RW 0000_0000h
22_0004h Channel Error Status (CH20_ES) 32 RW 0000_0000h
22_0008h Channel Interrupt Status (CH20_INT) 32 RW 0000_0000h
22_000Ch Channel System Bus (CH20_SBR) 32 RW 0000_8002h
22_0010h Channel Priority (CH20_PRI) 32 RW 0000_0000h
22_0020h TCD Source Address (TCD20_SADDR) 32 RW See section
22_0024h TCD Signed Source Address Offset (TCD20_SOFF) 16 RW See section
22_0026h TCD Transfer Attributes (TCD20_ATTR) 16 RW See section
22_0028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD20_NBYTES_MLOFFNO)
22_0028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD20_NBYTES_MLOFFYES)
22_002Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD20_SLAST_SDA)
22_0030h TCD Destination Address (TCD20_DADDR) 32 RW See section
22_0034h TCD Signed Destination Address Offset (TCD20_DOFF) 16 RW See section
22_0036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD20_CITER_ELINKNO)
22_0036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD20_CITER_ELINKYES)
22_0038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD20_DLAST_SGA)
22_003Ch TCD Control and Status (TCD20_CSR) 16 RW See section
22_003Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD20_BITER_ELINKNO)
22_003Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD20_BITER_ELINKYES)
22_4000h Channel Control and Status (CH21_CSR) 32 RW 0000_0000h
22_4004h Channel Error Status (CH21_ES) 32 RW 0000_0000h
22_4008h Channel Interrupt Status (CH21_INT) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 310*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
22_400Ch Channel System Bus (CH21_SBR) 32 RW 0000_8002h
22_4010h Channel Priority (CH21_PRI) 32 RW 0000_0000h
22_4020h TCD Source Address (TCD21_SADDR) 32 RW See section
22_4024h TCD Signed Source Address Offset (TCD21_SOFF) 16 RW See section
22_4026h TCD Transfer Attributes (TCD21_ATTR) 16 RW See section
22_4028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD21_NBYTES_MLOFFNO)
22_4028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD21_NBYTES_MLOFFYES)
22_402Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD21_SLAST_SDA)
22_4030h TCD Destination Address (TCD21_DADDR) 32 RW See section
22_4034h TCD Signed Destination Address Offset (TCD21_DOFF) 16 RW See section
22_4036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD21_CITER_ELINKNO)
22_4036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD21_CITER_ELINKYES)
22_4038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD21_DLAST_SGA)
22_403Ch TCD Control and Status (TCD21_CSR) 16 RW See section
22_403Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD21_BITER_ELINKNO)
22_403Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD21_BITER_ELINKYES)
22_8000h Channel Control and Status (CH22_CSR) 32 RW 0000_0000h
22_8004h Channel Error Status (CH22_ES) 32 RW 0000_0000h
22_8008h Channel Interrupt Status (CH22_INT) 32 RW 0000_0000h
22_800Ch Channel System Bus (CH22_SBR) 32 RW 0000_8002h
22_8010h Channel Priority (CH22_PRI) 32 RW 0000_0000h
22_8020h TCD Source Address (TCD22_SADDR) 32 RW See section
22_8024h TCD Signed Source Address Offset (TCD22_SOFF) 16 RW See section
22_8026h TCD Transfer Attributes (TCD22_ATTR) 16 RW See section
22_8028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD22_NBYTES_MLOFFNO)
Table continues on the next page...

---

*Page 311*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
22_8028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD22_NBYTES_MLOFFYES)
22_802Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD22_SLAST_SDA)
22_8030h TCD Destination Address (TCD22_DADDR) 32 RW See section
22_8034h TCD Signed Destination Address Offset (TCD22_DOFF) 16 RW See section
22_8036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD22_CITER_ELINKNO)
22_8036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD22_CITER_ELINKYES)
22_8038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD22_DLAST_SGA)
22_803Ch TCD Control and Status (TCD22_CSR) 16 RW See section
22_803Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD22_BITER_ELINKNO)
22_803Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD22_BITER_ELINKYES)
22_C000h Channel Control and Status (CH23_CSR) 32 RW 0000_0000h
22_C004h Channel Error Status (CH23_ES) 32 RW 0000_0000h
22_C008h Channel Interrupt Status (CH23_INT) 32 RW 0000_0000h
22_C00Ch Channel System Bus (CH23_SBR) 32 RW 0000_8002h
22_C010h Channel Priority (CH23_PRI) 32 RW 0000_0000h
22_C020h TCD Source Address (TCD23_SADDR) 32 RW See section
22_C024h TCD Signed Source Address Offset (TCD23_SOFF) 16 RW See section
22_C026h TCD Transfer Attributes (TCD23_ATTR) 16 RW See section
22_C028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD23_NBYTES_MLOFFNO)
22_C028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD23_NBYTES_MLOFFYES)
22_C02Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD23_SLAST_SDA)
22_C030h TCD Destination Address (TCD23_DADDR) 32 RW See section
22_C034h TCD Signed Destination Address Offset (TCD23_DOFF) 16 RW See section
Table continues on the next page...

---

*Page 312*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
22_C036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD23_CITER_ELINKNO)
22_C036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD23_CITER_ELINKYES)
22_C038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD23_DLAST_SGA)
22_C03Ch TCD Control and Status (TCD23_CSR) 16 RW See section
22_C03Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD23_BITER_ELINKNO)
22_C03Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD23_BITER_ELINKYES)
23_0000h Channel Control and Status (CH24_CSR) 32 RW 0000_0000h
23_0004h Channel Error Status (CH24_ES) 32 RW 0000_0000h
23_0008h Channel Interrupt Status (CH24_INT) 32 RW 0000_0000h
23_000Ch Channel System Bus (CH24_SBR) 32 RW 0000_8002h
23_0010h Channel Priority (CH24_PRI) 32 RW 0000_0000h
23_0020h TCD Source Address (TCD24_SADDR) 32 RW See section
23_0024h TCD Signed Source Address Offset (TCD24_SOFF) 16 RW See section
23_0026h TCD Transfer Attributes (TCD24_ATTR) 16 RW See section
23_0028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD24_NBYTES_MLOFFNO)
23_0028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD24_NBYTES_MLOFFYES)
23_002Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD24_SLAST_SDA)
23_0030h TCD Destination Address (TCD24_DADDR) 32 RW See section
23_0034h TCD Signed Destination Address Offset (TCD24_DOFF) 16 RW See section
23_0036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD24_CITER_ELINKNO)
23_0036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD24_CITER_ELINKYES)
23_0038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD24_DLAST_SGA)
23_003Ch TCD Control and Status (TCD24_CSR) 16 RW See section
Table continues on the next page...

---

*Page 313*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
23_003Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD24_BITER_ELINKNO)
23_003Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD24_BITER_ELINKYES)
23_4000h Channel Control and Status (CH25_CSR) 32 RW 0000_0000h
23_4004h Channel Error Status (CH25_ES) 32 RW 0000_0000h
23_4008h Channel Interrupt Status (CH25_INT) 32 RW 0000_0000h
23_400Ch Channel System Bus (CH25_SBR) 32 RW 0000_8002h
23_4010h Channel Priority (CH25_PRI) 32 RW 0000_0000h
23_4020h TCD Source Address (TCD25_SADDR) 32 RW See section
23_4024h TCD Signed Source Address Offset (TCD25_SOFF) 16 RW See section
23_4026h TCD Transfer Attributes (TCD25_ATTR) 16 RW See section
23_4028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD25_NBYTES_MLOFFNO)
23_4028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD25_NBYTES_MLOFFYES)
23_402Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD25_SLAST_SDA)
23_4030h TCD Destination Address (TCD25_DADDR) 32 RW See section
23_4034h TCD Signed Destination Address Offset (TCD25_DOFF) 16 RW See section
23_4036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD25_CITER_ELINKNO)
23_4036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD25_CITER_ELINKYES)
23_4038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD25_DLAST_SGA)
23_403Ch TCD Control and Status (TCD25_CSR) 16 RW See section
23_403Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD25_BITER_ELINKNO)
23_403Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD25_BITER_ELINKYES)
23_8000h Channel Control and Status (CH26_CSR) 32 RW 0000_0000h
23_8004h Channel Error Status (CH26_ES) 32 RW 0000_0000h
23_8008h Channel Interrupt Status (CH26_INT) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 314*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
23_800Ch Channel System Bus (CH26_SBR) 32 RW 0000_8002h
23_8010h Channel Priority (CH26_PRI) 32 RW 0000_0000h
23_8020h TCD Source Address (TCD26_SADDR) 32 RW See section
23_8024h TCD Signed Source Address Offset (TCD26_SOFF) 16 RW See section
23_8026h TCD Transfer Attributes (TCD26_ATTR) 16 RW See section
23_8028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD26_NBYTES_MLOFFNO)
23_8028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD26_NBYTES_MLOFFYES)
23_802Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD26_SLAST_SDA)
23_8030h TCD Destination Address (TCD26_DADDR) 32 RW See section
23_8034h TCD Signed Destination Address Offset (TCD26_DOFF) 16 RW See section
23_8036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD26_CITER_ELINKNO)
23_8036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD26_CITER_ELINKYES)
23_8038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD26_DLAST_SGA)
23_803Ch TCD Control and Status (TCD26_CSR) 16 RW See section
23_803Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD26_BITER_ELINKNO)
23_803Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD26_BITER_ELINKYES)
23_C000h Channel Control and Status (CH27_CSR) 32 RW 0000_0000h
23_C004h Channel Error Status (CH27_ES) 32 RW 0000_0000h
23_C008h Channel Interrupt Status (CH27_INT) 32 RW 0000_0000h
23_C00Ch Channel System Bus (CH27_SBR) 32 RW 0000_8002h
23_C010h Channel Priority (CH27_PRI) 32 RW 0000_0000h
23_C020h TCD Source Address (TCD27_SADDR) 32 RW See section
23_C024h TCD Signed Source Address Offset (TCD27_SOFF) 16 RW See section
23_C026h TCD Transfer Attributes (TCD27_ATTR) 16 RW See section
23_C028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD27_NBYTES_MLOFFNO)
Table continues on the next page...

---

*Page 315*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
23_C028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD27_NBYTES_MLOFFYES)
23_C02Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD27_SLAST_SDA)
23_C030h TCD Destination Address (TCD27_DADDR) 32 RW See section
23_C034h TCD Signed Destination Address Offset (TCD27_DOFF) 16 RW See section
23_C036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD27_CITER_ELINKNO)
23_C036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD27_CITER_ELINKYES)
23_C038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD27_DLAST_SGA)
23_C03Ch TCD Control and Status (TCD27_CSR) 16 RW See section
23_C03Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD27_BITER_ELINKNO)
23_C03Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD27_BITER_ELINKYES)
24_0000h Channel Control and Status (CH28_CSR) 32 RW 0000_0000h
24_0004h Channel Error Status (CH28_ES) 32 RW 0000_0000h
24_0008h Channel Interrupt Status (CH28_INT) 32 RW 0000_0000h
24_000Ch Channel System Bus (CH28_SBR) 32 RW 0000_8002h
24_0010h Channel Priority (CH28_PRI) 32 RW 0000_0000h
24_0020h TCD Source Address (TCD28_SADDR) 32 RW See section
24_0024h TCD Signed Source Address Offset (TCD28_SOFF) 16 RW See section
24_0026h TCD Transfer Attributes (TCD28_ATTR) 16 RW See section
24_0028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD28_NBYTES_MLOFFNO)
24_0028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD28_NBYTES_MLOFFYES)
24_002Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD28_SLAST_SDA)
24_0030h TCD Destination Address (TCD28_DADDR) 32 RW See section
24_0034h TCD Signed Destination Address Offset (TCD28_DOFF) 16 RW See section
Table continues on the next page...

---

*Page 316*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
24_0036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD28_CITER_ELINKNO)
24_0036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD28_CITER_ELINKYES)
24_0038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD28_DLAST_SGA)
24_003Ch TCD Control and Status (TCD28_CSR) 16 RW See section
24_003Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD28_BITER_ELINKNO)
24_003Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD28_BITER_ELINKYES)
24_4000h Channel Control and Status (CH29_CSR) 32 RW 0000_0000h
24_4004h Channel Error Status (CH29_ES) 32 RW 0000_0000h
24_4008h Channel Interrupt Status (CH29_INT) 32 RW 0000_0000h
24_400Ch Channel System Bus (CH29_SBR) 32 RW 0000_8002h
24_4010h Channel Priority (CH29_PRI) 32 RW 0000_0000h
24_4020h TCD Source Address (TCD29_SADDR) 32 RW See section
24_4024h TCD Signed Source Address Offset (TCD29_SOFF) 16 RW See section
24_4026h TCD Transfer Attributes (TCD29_ATTR) 16 RW See section
24_4028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD29_NBYTES_MLOFFNO)
24_4028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD29_NBYTES_MLOFFYES)
24_402Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD29_SLAST_SDA)
24_4030h TCD Destination Address (TCD29_DADDR) 32 RW See section
24_4034h TCD Signed Destination Address Offset (TCD29_DOFF) 16 RW See section
24_4036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD29_CITER_ELINKNO)
24_4036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD29_CITER_ELINKYES)
24_4038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD29_DLAST_SGA)
24_403Ch TCD Control and Status (TCD29_CSR) 16 RW See section
Table continues on the next page...

---

*Page 317*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
24_403Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD29_BITER_ELINKNO)
24_403Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD29_BITER_ELINKYES)
24_8000h Channel Control and Status (CH30_CSR) 32 RW 0000_0000h
24_8004h Channel Error Status (CH30_ES) 32 RW 0000_0000h
24_8008h Channel Interrupt Status (CH30_INT) 32 RW 0000_0000h
24_800Ch Channel System Bus (CH30_SBR) 32 RW 0000_8002h
24_8010h Channel Priority (CH30_PRI) 32 RW 0000_0000h
24_8020h TCD Source Address (TCD30_SADDR) 32 RW See section
24_8024h TCD Signed Source Address Offset (TCD30_SOFF) 16 RW See section
24_8026h TCD Transfer Attributes (TCD30_ATTR) 16 RW See section
24_8028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD30_NBYTES_MLOFFNO)
24_8028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD30_NBYTES_MLOFFYES)
24_802Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD30_SLAST_SDA)
24_8030h TCD Destination Address (TCD30_DADDR) 32 RW See section
24_8034h TCD Signed Destination Address Offset (TCD30_DOFF) 16 RW See section
24_8036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD30_CITER_ELINKNO)
24_8036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD30_CITER_ELINKYES)
24_8038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD30_DLAST_SGA)
24_803Ch TCD Control and Status (TCD30_CSR) 16 RW See section
24_803Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD30_BITER_ELINKNO)
24_803Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD30_BITER_ELINKYES)
24_C000h Channel Control and Status (CH31_CSR) 32 RW 0000_0000h
24_C004h Channel Error Status (CH31_ES) 32 RW 0000_0000h
24_C008h Channel Interrupt Status (CH31_INT) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 318*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
24_C00Ch Channel System Bus (CH31_SBR) 32 RW 0000_8002h
24_C010h Channel Priority (CH31_PRI) 32 RW 0000_0000h
24_C020h TCD Source Address (TCD31_SADDR) 32 RW See section
24_C024h TCD Signed Source Address Offset (TCD31_SOFF) 16 RW See section
24_C026h TCD Transfer Attributes (TCD31_ATTR) 16 RW See section
24_C028h TCD Transfer Size Without Minor Loop Offsets 32 RW See section
(TCD31_NBYTES_MLOFFNO)
24_C028h TCD Transfer Size with Minor Loop Offsets 32 RW See section
(TCD31_NBYTES_MLOFFYES)
24_C02Ch TCD Last Source Address Adjustment / Store DADDR Address 32 RW See section
(TCD31_SLAST_SDA)
24_C030h TCD Destination Address (TCD31_DADDR) 32 RW See section
24_C034h TCD Signed Destination Address Offset (TCD31_DOFF) 16 RW See section
24_C036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD31_CITER_ELINKNO)
24_C036h TCD Current Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD31_CITER_ELINKYES)
24_C038h TCD Last Destination Address Adjustment / Scatter Gather Address 32 RW See section
(TCD31_DLAST_SGA)
24_C03Ch TCD Control and Status (TCD31_CSR) 16 RW See section
24_C03Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Disabled) (TCD31_BITER_ELINKNO)
24_C03Eh TCD Beginning Major Loop Count (Minor Loop Channel Linking 16 RW See section
Enabled) (TCD31_BITER_ELINKYES)
14.6.2.2 Channel Control and Status (CH0_CSR - CH31_CSR)
Offset
Register Offset
CH0_CSR 0h
CH1_CSR 4000h
CH2_CSR 8000h
CH3_CSR C000h
Table continues on the next page...

---

*Page 319*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
CH4_CSR 1_0000h
CH5_CSR 1_4000h
CH6_CSR 1_8000h
CH7_CSR 1_C000h
CH8_CSR 2_0000h
CH9_CSR 2_4000h
CH10_CSR 2_8000h
CH11_CSR 2_C000h
CH12_CSR 20_0000h
CH13_CSR 20_4000h
CH14_CSR 20_8000h
CH15_CSR 20_C000h
CH16_CSR 21_0000h
CH17_CSR 21_4000h
CH18_CSR 21_8000h
CH19_CSR 21_C000h
CH20_CSR 22_0000h
CH21_CSR 22_4000h
CH22_CSR 22_8000h
CH23_CSR 22_C000h
CH24_CSR 23_0000h
CH25_CSR 23_4000h
CH26_CSR 23_8000h
CH27_CSR 23_C000h
CH28_CSR 24_0000h
CH29_CSR 24_4000h
CH30_CSR 24_8000h
CH31_CSR 24_C000h
Function
This register contains several fields related to hardware and interrupt requests, configuration, and status for the given channel.

---

*Page 320*

Enhanced Direct Memory Access (eDMA)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
ACTIV
R DONE
E
W W1C 0
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
EBW EEI EARQ ERQ
W 0
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Channel Active
ACTIVE The ACTIVE field indicates the channel was selected by arbitration and is executing the prescribed
transfers. The eDMA sets it to 1 when channel service begins, and clears it to 0 as the minor loop
completes or when any error condition is detected. Except for dynamic scatter/gather or dynamic
channel linking, you must not modify the transfer control descriptor when a channel is active.
30 Channel Done
DONE The DONE field indicates the eDMA has completed the major loop. The eDMA engine sets this field as
the CITER count reaches zero. If enabled, the eDMA generates an interrupt request corresponding to this
completed channel. The software clears it, or the hardware clears it when the channel is activated.
NOTE
This field must be cleared to 0 before writing the MAJORELINK or ESG fields.
Reserved
29-4
—
3 Enable Buffered Writes
EBW When buffered writes are enabled, all writes except for the last write sequence of the minor loop are
signaled by the eDMA as bufferable.
0b - Buffered writes on system bus disabled. Buffered writes on system bus disabled
1b - Buffered writes on system bus enabled. Bufferable write signal asserted on all system bus
writes except during last write sequence
2 Enable Error Interrupt
EEI The EEI field enables the error interrupt signal for the channel. The DMA error indicator and the error
interrupt enable flag must be asserted before an error interrupt request for a given channel is asserted to
the interrupt controller.
0b - Error signal for corresponding channel does not generate error interrupt
Table continues on the next page...

---

*Page 321*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Field Function
1b - Assertion of error signal for corresponding channel generates error interrupt request
1 Enable Asynchronous DMA Request In Stop Mode For Channel
EARQ The enable asynchronous DMA request field (EARQ) does not affect DMA operations. When set to 1,
this field allows the hardware service request enable field (ERQ) to propagate out of the DMA to the
power controller. When cleared to 0, this field masks the hardware service request enable field to the
power controller.
0b - Disable asynchronous DMA request for the channel
1b - Enable asynchronous DMA request for the channel
0 Enable DMA Request
ERQ Disable a channel's hardware service request at the source before clearing the channel's ERQ field.
The DMA hardware request input signal and the enable request field (ERQ) must be asserted before a
channel's hardware service request is accepted. The state of the eDMA enable request field does not
affect a channel service request made explicitly through software or channel linking. The state of the
ERQ field does not affect the channel's START field.
0b - DMA hardware request signal for corresponding channel disabled
1b - DMA hardware request signal for corresponding channel enabled
14.6.2.3 Channel Error Status (CH0_ES - CH31_ES)
Offset
Register Offset
CH0_ES 4h
CH1_ES 4004h
CH2_ES 8004h
CH3_ES C004h
CH4_ES 1_0004h
CH5_ES 1_4004h
CH6_ES 1_8004h
CH7_ES 1_C004h
CH8_ES 2_0004h
CH9_ES 2_4004h
CH10_ES 2_8004h
CH11_ES 2_C004h
CH12_ES 20_0004h
Table continues on the next page...

---

*Page 322*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
CH13_ES 20_4004h
CH14_ES 20_8004h
CH15_ES 20_C004h
CH16_ES 21_0004h
CH17_ES 21_4004h
CH18_ES 21_8004h
CH19_ES 21_C004h
CH20_ES 22_0004h
CH21_ES 22_4004h
CH22_ES 22_8004h
CH23_ES 22_C004h
CH24_ES 23_0004h
CH25_ES 23_4004h
CH26_ES 23_8004h
CH27_ES 23_C004h
CH28_ES 24_0004h
CH29_ES 24_4004h
CH30_ES 24_8004h
CH31_ES 24_C004h
Function
The ES provides information concerning the last recorded channel error. Channel errors can be caused by:
• An illegal setting in the transfer control descriptor
• An error termination to a bus master read or write cycle
The ERR field signals the presence of an error for the channel. The eDMA engine signals the occurrence of an error condition by
setting the appropriate field in this register. The outputs of this register are enabled by the contents of the CHn_CSR[EEI] field,
then logically summed across all channels to form an error interrupt request, which may be routed to the interrupt controller. In
addition, this enabled error status is logically OR'd onto the channel done interrupt, CHn_INT[INT] , thus forming a done or error
interrupt on a per channel basis.
During the execution of the interrupt service routine associated with any DMA errors, it is software's responsibility to clear the
appropriate bit, negating the error-interrupt request. The normal DMA channel completion indicators (setting the transfer control
descriptor DONE flag and the possible assertion of an interrupt request) are not affected when eDMA detects an error. The
contents of this ERR register field can also be polled because a non-zero value indicates the presence of a channel error,
regardless of the state of the EEI mask.
The state of any given channel's error indicators is affected by writes to this register. Writing a 1 to the ERR field clears the
channel's error status, and writing a 0 has no effect.

---

*Page 323*

Enhanced Direct Memory Access (eDMA)
An unspecified error, where only the ERR field is set to 1, indicates that either a transfer was cancelled with an error or else an
uncorrectable TCD error occurred . The Management Page Error Status register has full view of the error condition.
See Fault reporting and handling for more details.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R ERR Reserved
W W1C 0
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R Reserved SAE SOE DAE DOE NCE SGE SBE DBE
W 0
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Error In Channel
31
0b - An error in this channel has not occurred
ERR
1b - An error in this channel has occurred
Reserved
30-8
—
7 Source Address Error
SAE TCDn_SADDR is inconsistent with TCDn_ATTR[SSIZE].
0b - No source address configuration error
1b - Last recorded error was a configuration error detected in the TCDn_SADDR field
6 Source Offset Error
SOE TCDn_SOFF is inconsistent with TCDn_ATTR[SSIZE].
0b - No source offset configuration error
1b - Last recorded error was a configuration error detected in the TCDn_SOFF field
5 Destination Address Error
DAE TCDn_DADDR is inconsistent with TCDn_ATTR[DSIZE].
0b - No destination address configuration error
1b - Last recorded error was a configuration error detected in the TCDn_DADDR field
4 Destination Offset Error
TCDn_DOFF is inconsistent with TCDn_ATTR[DSIZE].
Table continues on the next page...

---

*Page 324*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Field Function
DOE 0b - No destination offset configuration error
1b - Last recorded error was a configuration error detected in the TCDn_DOFF field
3 NBYTES/CITER Configuration Error
NCE This error indicates that one of the following has occurred:
• TCDn_NBYTES is not a multiple of TCDn_ATTR[SSIZE] and TCDn_ATTR[DSIZE]
• TCDn_CITER[CITER] is equal to zero
• TCDn_CITER[ELINK] is not equal to TCDn_BITER[ELINK]
0b - No NBYTES/CITER configuration error
1b - Last recorded error was a configuration error detected in the TCDn_NBYTES or
TCDn_CITER fields
2 Scatter/Gather Configuration Error
SGE When this field is 1, it indicates that TCDn_DLAST_SGA is not on a 32-byte boundary. This field is
checked at the beginning of a scatter/gather operation after major loop completion if TCDn_CSR[ESG] is
enabled.
0b - No scatter/gather configuration error
1b - Last recorded error was a configuration error detected in the TCDn_DLAST_SGA field
Source Bus Error
1
0b - No source bus error
SBE
1b - Last recorded error was bus error on source read
Destination Bus Error
0
0b - No destination bus error
DBE
1b - Last recorded error was bus error on destination write
14.6.2.4 Channel Interrupt Status (CH0_INT - CH31_INT)
Offset
Register Offset
CH0_INT 8h
CH1_INT 4008h
CH2_INT 8008h
CH3_INT C008h
CH4_INT 1_0008h
CH5_INT 1_4008h
Table continues on the next page...

---

*Page 325*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
CH6_INT 1_8008h
CH7_INT 1_C008h
CH8_INT 2_0008h
CH9_INT 2_4008h
CH10_INT 2_8008h
CH11_INT 2_C008h
CH12_INT 20_0008h
CH13_INT 20_4008h
CH14_INT 20_8008h
CH15_INT 20_C008h
CH16_INT 21_0008h
CH17_INT 21_4008h
CH18_INT 21_8008h
CH19_INT 21_C008h
CH20_INT 22_0008h
CH21_INT 22_4008h
CH22_INT 22_8008h
CH23_INT 22_C008h
CH24_INT 23_0008h
CH25_INT 23_4008h
CH26_INT 23_8008h
CH27_INT 23_C008h
CH28_INT 24_0008h
CH29_INT 24_4008h
CH30_INT 24_8008h
CH31_INT 24_C008h
Function
The INT field signals the presence of an interrupt request for the channel. Depending on the appropriate bit setting in the transfe
control descriptors, the eDMA engine generates an interrupt on data transfer completion or an error condition .
The outputs of this register are directly routed to the interrupt controller. During the interrupt service routine associated with any
given channel, it is the software's responsibility to clear the appropriate bit, negating the interrupt request. On writes to INT, a 1
clears the channel's interrupt request. A zero has no effect on the channel's current interrupt status.

---

*Page 326*

Enhanced Direct Memory Access (eDMA)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 INT
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-1
—
Interrupt Request
0
0b - Interrupt request for corresponding channel cleared
INT
1b - Interrupt request for corresponding channel active
14.6.2.5 Channel System Bus (CH0_SBR - CH31_SBR)
Offset
Register Offset
CH0_SBR Ch
CH1_SBR 400Ch
CH2_SBR 800Ch
CH3_SBR C00Ch
CH4_SBR 1_000Ch
CH5_SBR 1_400Ch
CH6_SBR 1_800Ch
CH7_SBR 1_C00Ch
CH8_SBR 2_000Ch
CH9_SBR 2_400Ch
CH10_SBR 2_800Ch
CH11_SBR 2_C00Ch
Table continues on the next page...

---

*Page 327*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
CH12_SBR 20_000Ch
CH13_SBR 20_400Ch
CH14_SBR 20_800Ch
CH15_SBR 20_C00Ch
CH16_SBR 21_000Ch
CH17_SBR 21_400Ch
CH18_SBR 21_800Ch
CH19_SBR 21_C00Ch
CH20_SBR 22_000Ch
CH21_SBR 22_400Ch
CH22_SBR 22_800Ch
CH23_SBR 22_C00Ch
CH24_SBR 23_000Ch
CH25_SBR 23_400Ch
CH26_SBR 23_800Ch
CH27_SBR 23_C00Ch
CH28_SBR 24_000Ch
CH29_SBR 24_400Ch
CH30_SBR 24_800Ch
CH31_SBR 24_C00Ch
Function
The Channel System Bus register places identification and attribute information on the system bus interface for the eDMA.
The ATTR register outputs the register values onto the system bus interface for further decoding by the security system.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R Reserved
ATTR EMI
W 0
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R PAL 0 MID
W
Reset 1 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0

---

*Page 328*

Enhanced Direct Memory Access (eDMA)
Fields
Field Function
Reserved
31-20
—
19-17 Attribute Output
ATTR DMA's system bus attribute output value.
16 Enable Master ID Replication
EMI The eDMA master ID replication field allows the eDMA to use the same protection level and system bus ID
of the master programming the eDMA's TCD. When enabled, the eDMA uses the master ID and protection
level stored in the CHn_SBR registers, instead of the eDMA's default values. When a master (for example
a core) programs a TCD, its master ID and protection level are captured when the TCD n _CSR control
attributes are written. A scatter/gather operation does not affect the CHn_SBR registers. You can write the
EMI only if CSR[GMRC] = 1, which means Global Master ID Replication Control is enabled; otherwise, the
EMI is forced to zero.
NOTE
If master ID replication is disabled, the privileged protection level (Supervisor mode) for
DMA transfers is used.
0b - Master ID replication is disabled
1b - Master ID replication is enabled
15 Privileged Access Level
PAL This field controls DMA's protection level on the system bus when the channel is active.
NOTE
When you enable master ID replication, the value captured in this register is the privilege
level of the core or other master writing the channel's transfer control descriptor, which is the
lower byte of TCD n _CSR.
0b - User protection level for DMA transfers
1b - Privileged protection level for DMA transfers
Reserved
14-4
—
3-0 Master ID
MID This field controls the DMA's master ID on the system bus when the channel is active.
NOTE
The ID captured in this register reflects the master ID of the core or other master writing the
channel's control attributes, which are in the lower byte of TCD n _CSR.

---

*Page 329*

Enhanced Direct Memory Access (eDMA)
14.6.2.6 Channel Priority (CH0_PRI - CH31_PRI)
Offset
Register Offset
CH0_PRI 10h
CH1_PRI 4010h
CH2_PRI 8010h
CH3_PRI C010h
CH4_PRI 1_0010h
CH5_PRI 1_4010h
CH6_PRI 1_8010h
CH7_PRI 1_C010h
CH8_PRI 2_0010h
CH9_PRI 2_4010h
CH10_PRI 2_8010h
CH11_PRI 2_C010h
CH12_PRI 20_0010h
CH13_PRI 20_4010h
CH14_PRI 20_8010h
CH15_PRI 20_C010h
CH16_PRI 21_0010h
CH17_PRI 21_4010h
CH18_PRI 21_8010h
CH19_PRI 21_C010h
CH20_PRI 22_0010h
CH21_PRI 22_4010h
CH22_PRI 22_8010h
CH23_PRI 22_C010h
CH24_PRI 23_0010h
CH25_PRI 23_4010h
CH26_PRI 23_8010h
CH27_PRI 23_C010h
CH28_PRI 24_0010h
CH29_PRI 24_4010h
Table continues on the next page...

---

*Page 330*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
CH30_PRI 24_8010h
CH31_PRI 24_C010h
Function
The contents of these registers define unique priorities associated with each channel within the same channel group. Channel
grouping is programmed via Channel Arbitration Group (CH0_GRPRI - CH31_GRPRI) .
The channel priorities within a group are evaluated by numeric value; for example, 0 is the lowest priority, 1 is the next higher
priority, then 2, 3, and so on. Software must program the channel priorities with unique values; otherwise, channel numbers with
the same, non-zero value, will be selected based on channel number with the higher channel number having higher priority.
If more than one channel in a group has an arbitration priority level value of zero, then the arbitration mode field CSR[ERCA] is
used to determine the arbitration scheme for all channels with APL=0 within a group.
When you enable round-robin channel arbitration ( CSR[ERCA] = 1), all channels with APL=0 within a group will use a round-robin
arbitration scheme, which rotates among these channels requesting service without regard to priority. Round-robin provides a
fairness mechanism within an arbitration group.
When you enable fixed-priority channel arbitration ( CSR[ERCA] = 0), eDMA selects channels with APL=0 based on channel
number, with the higher channel number having higher priority.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R Reserved
ECP DPA
W 0
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R Reserved
APL
W 0
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Enable Channel Preemption
31
0b - Channel cannot be suspended by a higher-priority channel's service request
ECP
1b - Channel can be temporarily suspended by a higher-priority channel's service request
Disable Preempt Ability
30
0b - Channel can suspend a lower-priority channel
DPA
1b - Channel cannot suspend any other channel, regardless of channel priority
Table continues on the next page...

---

*Page 331*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Field Function
Reserved
29-3
—
2-0 Arbitration Priority Level
APL Channel priority level for arbitration within the assigned arbitration group.
14.6.2.7 TCD Source Address (TCD0_SADDR - TCD31_SADDR)
Offset
Register Offset
TCD0_SADDR 20h
TCD1_SADDR 4020h
TCD2_SADDR 8020h
TCD3_SADDR C020h
TCD4_SADDR 1_0020h
TCD5_SADDR 1_4020h
TCD6_SADDR 1_8020h
TCD7_SADDR 1_C020h
TCD8_SADDR 2_0020h
TCD9_SADDR 2_4020h
TCD10_SADDR 2_8020h
TCD11_SADDR 2_C020h
TCD12_SADDR 20_0020h
TCD13_SADDR 20_4020h
TCD14_SADDR 20_8020h
TCD15_SADDR 20_C020h
TCD16_SADDR 21_0020h
TCD17_SADDR 21_4020h
TCD18_SADDR 21_8020h
TCD19_SADDR 21_C020h
TCD20_SADDR 22_0020h
TCD21_SADDR 22_4020h
Table continues on the next page...

---

*Page 332*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
TCD22_SADDR 22_8020h
TCD23_SADDR 22_C020h
TCD24_SADDR 23_0020h
TCD25_SADDR 23_4020h
TCD26_SADDR 23_8020h
TCD27_SADDR 23_C020h
TCD28_SADDR 24_0020h
TCD29_SADDR 24_4020h
TCD30_SADDR 24_8020h
TCD31_SADDR 24_C020h
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SADDR
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SADDR
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
31-0 Source Address
SADDR Memory address pointing to the source data.
14.6.2.8 TCD Signed Source Address Offset (TCD0_SOFF - TCD31_SOFF)
Offset
Register Offset
TCD0_SOFF 24h
TCD1_SOFF 4024h
Table continues on the next page...

---

*Page 333*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
TCD2_SOFF 8024h
TCD3_SOFF C024h
TCD4_SOFF 1_0024h
TCD5_SOFF 1_4024h
TCD6_SOFF 1_8024h
TCD7_SOFF 1_C024h
TCD8_SOFF 2_0024h
TCD9_SOFF 2_4024h
TCD10_SOFF 2_8024h
TCD11_SOFF 2_C024h
TCD12_SOFF 20_0024h
TCD13_SOFF 20_4024h
TCD14_SOFF 20_8024h
TCD15_SOFF 20_C024h
TCD16_SOFF 21_0024h
TCD17_SOFF 21_4024h
TCD18_SOFF 21_8024h
TCD19_SOFF 21_C024h
TCD20_SOFF 22_0024h
TCD21_SOFF 22_4024h
TCD22_SOFF 22_8024h
TCD23_SOFF 22_C024h
TCD24_SOFF 23_0024h
TCD25_SOFF 23_4024h
TCD26_SOFF 23_8024h
TCD27_SOFF 23_C024h
TCD28_SOFF 24_0024h
TCD29_SOFF 24_4024h
TCD30_SOFF 24_8024h
TCD31_SOFF 24_C024h

---

*Page 334*

Enhanced Direct Memory Access (eDMA)
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SOFF
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
15-0 Source Address Signed Offset
SOFF Sign-extended offset applied to the current source address to form the next-state value as each source
read is completed.
14.6.2.9 TCD Transfer Attributes (TCD0_ATTR - TCD31_ATTR)
Offset
Register Offset
TCD0_ATTR 26h
TCD1_ATTR 4026h
TCD2_ATTR 8026h
TCD3_ATTR C026h
TCD4_ATTR 1_0026h
TCD5_ATTR 1_4026h
TCD6_ATTR 1_8026h
TCD7_ATTR 1_C026h
TCD8_ATTR 2_0026h
TCD9_ATTR 2_4026h
TCD10_ATTR 2_8026h
TCD11_ATTR 2_C026h
TCD12_ATTR 20_0026h
TCD13_ATTR 20_4026h
TCD14_ATTR 20_8026h
TCD15_ATTR 20_C026h
TCD16_ATTR 21_0026h
TCD17_ATTR 21_4026h
TCD18_ATTR 21_8026h
Table continues on the next page...

---

*Page 335*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
TCD19_ATTR 21_C026h
TCD20_ATTR 22_0026h
TCD21_ATTR 22_4026h
TCD22_ATTR 22_8026h
TCD23_ATTR 22_C026h
TCD24_ATTR 23_0026h
TCD25_ATTR 23_4026h
TCD26_ATTR 23_8026h
TCD27_ATTR 23_C026h
TCD28_ATTR 24_0026h
TCD29_ATTR 24_4026h
TCD30_ATTR 24_8026h
TCD31_ATTR 24_C026h
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SMOD SSIZE DMOD DSIZE
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
15-11 Source Address Modulo
SMOD This field defines a specific address range, which is the value after the SADDR + SOFF calculation is
performed on the original register value. Setting this field makes it easy to implement a circular data queue.
For data queues requiring power-of-2-sized bytes, the queue must start at a 0-modulo-size address and
the SMOD field must be set to the appropriate value for the queue, freezing the required number of upper
address bits.
The value programmed into this field specifies the number of lower address bits that are allowed to change.
For a circular queue application, you typically set TCDn_SOFF[SOFF] to the transfer size to implement
post-increment addressing, with the SMOD function constraining the addresses to a 0-modulo-size range.
0_0000b - Source address modulo feature disabled
0_0001b - Source address modulo feature enabled for any non-zero value [1-31]
Source Data Transfer Size
10-8
Table continues on the next page...

---

*Page 336*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Field Function
SSIZE 000b - 8-bit
001b - 16-bit
010b - 32-bit
011b - 64-bit
100b - 16-byte
101b - 32-byte
110b - 64-byte
111b - Reserved
7-3 Destination Address Modulo
DMOD See the SMOD definition.
2-0 Destination Data Transfer Size
DSIZE See the SSIZE definition.
14.6.2.10 TCD Transfer Size Without Minor Loop Offsets (TCD0_NBYTES_MLOFFNO -
TCD31_NBYTES_MLOFFNO)
Offset
Register Offset
TCD0_NBYTES_MLOFF 28h
NO
TCD1_NBYTES_MLOFF 4028h
NO
TCD2_NBYTES_MLOFF 8028h
NO
TCD3_NBYTES_MLOFF C028h
NO
TCD4_NBYTES_MLOFF 1_0028h
NO
TCD5_NBYTES_MLOFF 1_4028h
NO
TCD6_NBYTES_MLOFF 1_8028h
NO
TCD7_NBYTES_MLOFF 1_C028h
NO
Table continues on the next page...

---

*Page 337*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
TCD8_NBYTES_MLOFF 2_0028h
NO
TCD9_NBYTES_MLOFF 2_4028h
NO
TCD10_NBYTES_MLOF 2_8028h
FNO
TCD11_NBYTES_MLOF 2_C028h
FNO
TCD12_NBYTES_MLOF 20_0028h
FNO
TCD13_NBYTES_MLOF 20_4028h
FNO
TCD14_NBYTES_MLOF 20_8028h
FNO
TCD15_NBYTES_MLOF 20_C028h
FNO
TCD16_NBYTES_MLOF 21_0028h
FNO
TCD17_NBYTES_MLOF 21_4028h
FNO
TCD18_NBYTES_MLOF 21_8028h
FNO
TCD19_NBYTES_MLOF 21_C028h
FNO
TCD20_NBYTES_MLOF 22_0028h
FNO
TCD21_NBYTES_MLOF 22_4028h
FNO
TCD22_NBYTES_MLOF 22_8028h
FNO
TCD23_NBYTES_MLOF 22_C028h
FNO
TCD24_NBYTES_MLOF 23_0028h
FNO
TCD25_NBYTES_MLOF 23_4028h
FNO
TCD26_NBYTES_MLOF 23_8028h
FNO
Table continues on the next page...

---

*Page 338*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
TCD27_NBYTES_MLOF 23_C028h
FNO
TCD28_NBYTES_MLOF 24_0028h
FNO
TCD29_NBYTES_MLOF 24_4028h
FNO
TCD30_NBYTES_MLOF 24_8028h
FNO
TCD31_NBYTES_MLOF 24_C028h
FNO
Function
The TCDn_NBYTES field defines the number of bytes to transfer per service request.
Minor loop offsets are address offset values added to the final source address (TCDn_SADDR), or destination
address (TCDn_DADDR), upon minor loop completion. Minor loop completion is when the channel has finished the
service request and has transferred NBYTES. When minor loop offsets are enabled, the minor loop offset value
(TCDn_NBYTES_MLOFFYES[MLOFF]) is added to the final source address (TCDn_SADDR), to the final destination address
(TCDn_DADDR), or to both, prior to the addresses being written back to the TCD. If the major loop is complete, the minor loop
offset is ignored and the major loop address offsets ( TCDn_SLAST_SDA and TCDn_DLAST_SGA ) are used to compute the next
TCDn_SADDR and TCDn_DADDR values.
When minor loop mapping is enabled (SMLOE or DMLOE is 1), TCDn_NBYTES_MLOFFNO / TCDn_NBYTES_MLOFFYES is
redefined. A portion of TCDn_NBYTES_MLOFFNO / TCDn_NBYTES_MLOFFYES is used to specify multiple fields:
• A source enable bit (SMLOE) to specify the minor loop offset must be applied to the source address (TCDn_SADDR) upon
minor loop completion
• A destination enable bit (DMLOE) to specify the minor loop offset must be applied to the destination address (TCDn_DADDR)
upon minor loop completion
• The sign extended minor loop offset value (MLOFF)
The same offset value (MLOFF) is used for both source and destination minor loop offsets. When either minor loop offset is
enabled (SMLOE set or DMLOE set), the NBYTES field is reduced to 10 bits. If both minor loop offsets are disabled (SMLOE
cleared and DMLOE cleared), the NBYTES field is a 30-bit vector.
One of two register profiles (this register or TCDn_NBYTES_MLOFFYES ), defines the number of bytes to transfer per request.
Which register to use depends on whether source or destination minor loop mapping is enabled.
TCDn_NBYTES_MLOFFNO / TCDn_NBYTES_MLOFFYES is defined as follows:
• If SMLOE = 0 and DMLOE = 0, then see the TCDn_NBYTES_MLOFFNO register description.
• If either SMLOE or DMLOE is 1, then see the TCDn_NBYTES_MLOFFYES register description.

---

*Page 339*

Enhanced Direct Memory Access (eDMA)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SMLO DMLO
NBYTES
E E
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
NBYTES
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
31 Source Minor Loop Offset Enable
SMLOE Selects whether the minor loop offset is applied to the source address upon minor loop completion.
0b - Minor loop offset not applied to SADDR
1b - Minor loop offset applied to SADDR
30 Destination Minor Loop Offset Enable
DMLOE Selects whether the minor loop offset is applied to the destination address upon minor loop completion.
0b - Minor loop offset not applied to DADDR
1b - Minor loop offset applied to DADDR
29-0 Number of Bytes To Transfer Per Service Request
NBYTES Number of bytes to be transferred for each service request of the channel.
When a channel activates, the module loads the appropriate TCD contents into the eDMA engine and
performs the appropriate reads and writes until the byte transfer count has been reached. This process is
normally an indivisible operation and cannot be halted. It can, however, be stalled by using the bandwidth
control field, or via preemption.
After the byte count is exhausted, the SADDR and DADDR values are written back into the TCD memory,
and the major loop iteration count (CITER) is decremented by one and written back to the TCD memory. If
the major iteration count is complete, additional processing is performed.
14.6.2.11 TCD Transfer Size with Minor Loop Offsets (TCD0_NBYTES_MLOFFYES -
TCD31_NBYTES_MLOFFYES)
Offset
Register Offset
TCD0_NBYTES_MLOFF 28h
YES
Table continues on the next page...

---

*Page 340*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
TCD1_NBYTES_MLOFF 4028h
YES
TCD2_NBYTES_MLOFF 8028h
YES
TCD3_NBYTES_MLOFF C028h
YES
TCD4_NBYTES_MLOFF 1_0028h
YES
TCD5_NBYTES_MLOFF 1_4028h
YES
TCD6_NBYTES_MLOFF 1_8028h
YES
TCD7_NBYTES_MLOFF 1_C028h
YES
TCD8_NBYTES_MLOFF 2_0028h
YES
TCD9_NBYTES_MLOFF 2_4028h
YES
TCD10_NBYTES_MLOF 2_8028h
FYES
TCD11_NBYTES_MLOF 2_C028h
FYES
TCD12_NBYTES_MLOF 20_0028h
FYES
TCD13_NBYTES_MLOF 20_4028h
FYES
TCD14_NBYTES_MLOF 20_8028h
FYES
TCD15_NBYTES_MLOF 20_C028h
FYES
TCD16_NBYTES_MLOF 21_0028h
FYES
TCD17_NBYTES_MLOF 21_4028h
FYES
TCD18_NBYTES_MLOF 21_8028h
FYES
TCD19_NBYTES_MLOF 21_C028h
FYES
Table continues on the next page...

---

*Page 341*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
TCD20_NBYTES_MLOF 22_0028h
FYES
TCD21_NBYTES_MLOF 22_4028h
FYES
TCD22_NBYTES_MLOF 22_8028h
FYES
TCD23_NBYTES_MLOF 22_C028h
FYES
TCD24_NBYTES_MLOF 23_0028h
FYES
TCD25_NBYTES_MLOF 23_4028h
FYES
TCD26_NBYTES_MLOF 23_8028h
FYES
TCD27_NBYTES_MLOF 23_C028h
FYES
TCD28_NBYTES_MLOF 24_0028h
FYES
TCD29_NBYTES_MLOF 24_4028h
FYES
TCD30_NBYTES_MLOF 24_8028h
FYES
TCD31_NBYTES_MLOF 24_C028h
FYES
Function
The TCDn_NBYTES field defines the number of bytes to transfer per service request.
Minor loop offset is an address offset value added to the final source address (TCDn_SADDR) or destination address
(TCDn_DADDR) upon minor loop completion. Minor loop completion occurs when the channel has finished the service request
and has transferred NBYTES. Minor loop offsets are enabled by setting either the source enable bit (SMLOE) or the destination
enable bit (DMLOE).
The source enable bit (SMLOE) specifies the minor loop offset value (MLOFF) that is to be applied to the source address
(TCDn_SADDR) upon minor loop completion. The destination enable bit (DMLOE) specifies the minor loop offset (MLOFF) that
is to be applied to the destination address (TCDn_DADDR) upon minor loop completion.
If the major loop is complete, the minor loop offsets are ignored and the major loop address offsets (TCDn_SLAST_SDA and
TCDn_DLAST_SGA) are used to compute the next TCDn_SADDR and TCDn_DADDR values.
When you enable the minor loop offset overlay (either SMLOE or DMLOE is 1), eDMA redefines TCDn_NBYTES_MLOFFNO /
TCDn_NBYTES_MLOFFYES . A portion of TCDn_NBYTES_MLOFFNO / TCDn_NBYTES_MLOFFYES specifies the sign-
extended minor loop offset value (MLOFF). The same offset value (MLOFF) applies to both source and destination minor
loop offsets. When the minor loop offset is enabled, you must align it to the transfer size of the source or destination it is associated
with. When either minor loop offset is enabled (SMLOE set or DMLOE set), the NBYTES field is reduced to 10 bits. If both minor
loop offsets are disabled (SMLOE cleared and DMLOE cleared), the NBYTES field is a 30-bit vector.

---

*Page 342*

Enhanced Direct Memory Access (eDMA)
One of two register profiles (this register or TCDn_NBYTES_MLOFFNO ) defines the number of bytes to transfer per request.
Which register to use depends on whether source or destination minor loop mapping is enabled.
TCDn_NBYTES_MLOFFYES is defined as follows:
• If either minor loop offset is enabled (SMLOE or DMLOE = 1), then see the TCDn_NBYTES_MLOFFYES register description.
• If SMLOE and DMLOE are both 0, then see the TCDn_NBYTES_MLOFFNO register description.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SMLO DMLO
MLOFF
E E
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
MLOFF NBYTES
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
31 Source Minor Loop Offset Enable
SMLOE Selects whether the minor loop offset is applied to the source address upon minor loop completion.
0b - Minor loop offset not applied to SADDR
1b - Minor loop offset applied to SADDR
30 Destination Minor Loop Offset Enable
DMLOE Selects whether the minor loop offset is applied to the destination address upon minor loop completion.
0b - Minor loop offset not applied to DADDR
1b - Minor loop offset applied to DADDR
29-10 Minor Loop Offset
MLOFF If SMLOE or DMLOE is 1, this field represents a sign-extended offset applied to the source or destination
address to form the next-state value after the minor loop completes.
9-0 Number of Bytes To Transfer Per Service Request
NBYTES The number of bytes to be transferred in each service request of the channel.
As a channel activates, the module loads the appropriate TCD contents into the eDMA engine and performs
the appropriate reads and writes until the minor byte transfer count has been reached. This is an indivisible
operation and cannot be halted. It can, however, be stalled by using the bandwidth control field, or
via preemption.
After the minor count is exhausted, the SADDR and DADDR values are written back into the TCD memory,
and the major iteration count is decremented and restored to the TCD memory. If the major iteration count
is complete, additional processing is performed.

---

*Page 343*

Enhanced Direct Memory Access (eDMA)
14.6.2.12 TCD Last Source Address Adjustment / Store DADDR Address (TCD0_SLAST_SDA -
TCD31_SLAST_SDA)
Offset
Register Offset
TCD0_SLAST_SDA 2Ch
TCD1_SLAST_SDA 402Ch
TCD2_SLAST_SDA 802Ch
TCD3_SLAST_SDA C02Ch
TCD4_SLAST_SDA 1_002Ch
TCD5_SLAST_SDA 1_402Ch
TCD6_SLAST_SDA 1_802Ch
TCD7_SLAST_SDA 1_C02Ch
TCD8_SLAST_SDA 2_002Ch
TCD9_SLAST_SDA 2_402Ch
TCD10_SLAST_SDA 2_802Ch
TCD11_SLAST_SDA 2_C02Ch
TCD12_SLAST_SDA 20_002Ch
TCD13_SLAST_SDA 20_402Ch
TCD14_SLAST_SDA 20_802Ch
TCD15_SLAST_SDA 20_C02Ch
TCD16_SLAST_SDA 21_002Ch
TCD17_SLAST_SDA 21_402Ch
TCD18_SLAST_SDA 21_802Ch
TCD19_SLAST_SDA 21_C02Ch
TCD20_SLAST_SDA 22_002Ch
TCD21_SLAST_SDA 22_402Ch
TCD22_SLAST_SDA 22_802Ch
TCD23_SLAST_SDA 22_C02Ch
TCD24_SLAST_SDA 23_002Ch
TCD25_SLAST_SDA 23_402Ch
TCD26_SLAST_SDA 23_802Ch
TCD27_SLAST_SDA 23_C02Ch
TCD28_SLAST_SDA 24_002Ch
TCD29_SLAST_SDA 24_402Ch
Table continues on the next page...

---

*Page 344*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
TCD30_SLAST_SDA 24_802Ch
TCD31_SLAST_SDA 24_C02Ch
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SLAST_SDA
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SLAST_SDA
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
31-0 Last Source Address Adjustment / Store DADDR Address
SLAST_SDA Source last address adjustment or the system memory address for destination address (DADDR) storage.
If (TCDn_CSR[ESDA] = 0), then:
• Adjustment value is added to the source address at the completion of the major iteration count.
This value can be used to restore the source address to the initial value or adjust the address to
reference the next data structure.
• This field uses two's complement notation for the final source address adjustment.
Otherwise:
• This address points to the 32-bit-aligned memory location where the destination address (DADDR)
is to be stored in system memory. By saving the final destination address in system memory via
the ESDA feature, you are able to compute the size of a variable destination data buffer by simply
subtracting the beginning DADDR from the final, saved DADDR. This feature is used together with
the scatter/gather operation to prevent the loss of the final DADDR, which is overwritten during the
scatter/gather operation.
The "Store Destination Address" (SDA) value must be a 32-bit-aligned location because the eDMA
forces the lower two address bits of the SLAST_SDA field to zero when ESDA is enabled. The module
performs this write operation when the major loop is done; that is, when the major iteration count
(CITER) decrements to zero.

---

*Page 345*

Enhanced Direct Memory Access (eDMA)
14.6.2.13 TCD Destination Address (TCD0_DADDR - TCD31_DADDR)
Offset
Register Offset
TCD0_DADDR 30h
TCD1_DADDR 4030h
TCD2_DADDR 8030h
TCD3_DADDR C030h
TCD4_DADDR 1_0030h
TCD5_DADDR 1_4030h
TCD6_DADDR 1_8030h
TCD7_DADDR 1_C030h
TCD8_DADDR 2_0030h
TCD9_DADDR 2_4030h
TCD10_DADDR 2_8030h
TCD11_DADDR 2_C030h
TCD12_DADDR 20_0030h
TCD13_DADDR 20_4030h
TCD14_DADDR 20_8030h
TCD15_DADDR 20_C030h
TCD16_DADDR 21_0030h
TCD17_DADDR 21_4030h
TCD18_DADDR 21_8030h
TCD19_DADDR 21_C030h
TCD20_DADDR 22_0030h
TCD21_DADDR 22_4030h
TCD22_DADDR 22_8030h
TCD23_DADDR 22_C030h
TCD24_DADDR 23_0030h
TCD25_DADDR 23_4030h
TCD26_DADDR 23_8030h
TCD27_DADDR 23_C030h
TCD28_DADDR 24_0030h
TCD29_DADDR 24_4030h
Table continues on the next page...

---

*Page 346*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
TCD30_DADDR 24_8030h
TCD31_DADDR 24_C030h
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
DADDR
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
DADDR
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
31-0 Destination Address
DADDR Memory address pointing to the destination data.
14.6.2.14 TCD Signed Destination Address Offset (TCD0_DOFF - TCD31_DOFF)
Offset
Register Offset
TCD0_DOFF 34h
TCD1_DOFF 4034h
TCD2_DOFF 8034h
TCD3_DOFF C034h
TCD4_DOFF 1_0034h
TCD5_DOFF 1_4034h
TCD6_DOFF 1_8034h
TCD7_DOFF 1_C034h
TCD8_DOFF 2_0034h
TCD9_DOFF 2_4034h
Table continues on the next page...

---

*Page 347*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
TCD10_DOFF 2_8034h
TCD11_DOFF 2_C034h
TCD12_DOFF 20_0034h
TCD13_DOFF 20_4034h
TCD14_DOFF 20_8034h
TCD15_DOFF 20_C034h
TCD16_DOFF 21_0034h
TCD17_DOFF 21_4034h
TCD18_DOFF 21_8034h
TCD19_DOFF 21_C034h
TCD20_DOFF 22_0034h
TCD21_DOFF 22_4034h
TCD22_DOFF 22_8034h
TCD23_DOFF 22_C034h
TCD24_DOFF 23_0034h
TCD25_DOFF 23_4034h
TCD26_DOFF 23_8034h
TCD27_DOFF 23_C034h
TCD28_DOFF 24_0034h
TCD29_DOFF 24_4034h
TCD30_DOFF 24_8034h
TCD31_DOFF 24_C034h
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
DOFF
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
15-0 Destination Address Signed Offset
Table continues on the next page...

---

*Page 348*

Enhanced Direct Memory Access (eDMA)
Field Function
Sign-extended offset that is applied to the current destination address to form the next-state value as
DOFF
each destination write is completed.
14.6.2.15 TCD Current Major Loop Count (Minor Loop Channel Linking Disabled) (TCD0_CITER_ELINKNO -
TCD31_CITER_ELINKNO)
Offset
Register Offset
TCD0_CITER_ELINKNO 36h
TCD1_CITER_ELINKNO 4036h
TCD2_CITER_ELINKNO 8036h
TCD3_CITER_ELINKNO C036h
TCD4_CITER_ELINKNO 1_0036h
TCD5_CITER_ELINKNO 1_4036h
TCD6_CITER_ELINKNO 1_8036h
TCD7_CITER_ELINKNO 1_C036h
TCD8_CITER_ELINKNO 2_0036h
TCD9_CITER_ELINKNO 2_4036h
TCD10_CITER_ELINKN 2_8036h
O
TCD11_CITER_ELINKN 2_C036h
O
TCD12_CITER_ELINKN 20_0036h
O
TCD13_CITER_ELINKN 20_4036h
O
TCD14_CITER_ELINKN 20_8036h
O
TCD15_CITER_ELINKN 20_C036h
O
TCD16_CITER_ELINKN 21_0036h
O
TCD17_CITER_ELINKN 21_4036h
O
TCD18_CITER_ELINKN 21_8036h
O
Table continues on the next page...

---

*Page 349*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
TCD19_CITER_ELINKN 21_C036h
O
TCD20_CITER_ELINKN 22_0036h
O
TCD21_CITER_ELINKN 22_4036h
O
TCD22_CITER_ELINKN 22_8036h
O
TCD23_CITER_ELINKN 22_C036h
O
TCD24_CITER_ELINKN 23_0036h
O
TCD25_CITER_ELINKN 23_4036h
O
TCD26_CITER_ELINKN 23_8036h
O
TCD27_CITER_ELINKN 23_C036h
O
TCD28_CITER_ELINKN 24_0036h
O
TCD29_CITER_ELINKN 24_4036h
O
TCD30_CITER_ELINKN 24_8036h
O
TCD31_CITER_ELINKN 24_C036h
O
Function
If TCDn_CITER[ELINK] is 0, the TCDn_CITER register is defined as follows.

---

*Page 350*

Enhanced Direct Memory Access (eDMA)
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
ELINK CITER
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
15 Enable Link
ELINK As the channel completes the minor loop, this flag enables linking to another channel as defined by the
relevant LINKCH field. The link target channel initiates a channel service request via an internal mechanism
that sets the TCDn_CSR[START] bit of the specified channel to 1.
If channel linking is disabled, the CITER value is extended to 15 bits in place of a link channel number. If the
major loop is exhausted, this link mechanism is suppressed in favor of MAJORELINK channel linking.
NOTE
This field must be equal to the BITER[ELINK] field; otherwise, a configuration error
is reported.
0b - Channel-to-channel linking disabled
1b - Channel-to-channel linking enabled
14-0 Current Major Iteration Count
CITER This 9-bit (ELINK = 1) or 15-bit (ELINK = 0) count represents the current major loop count for the channel.
It is decremented each time the channel finishes a service request and is written back to TCD memory.
After the major iteration count is exhausted, the channel performs a number of operations — for example,
final source and destination address calculations — and optionally generates an interrupt to signal channel
completion before reloading the CITER field from the Beginning Iteration Count (BITER) field.
NOTE
When the CITER field is initially loaded by software, it must be set to the same value as that
contained in the BITER field.
NOTE
If the channel is configured to execute a single service request, the initial values of BITER
and CITER should be 0x0001.

---

*Page 351*

Enhanced Direct Memory Access (eDMA)
14.6.2.16 TCD Current Major Loop Count (Minor Loop Channel Linking Enabled) (TCD0_CITER_ELINKYES -
TCD31_CITER_ELINKYES)
Offset
Register Offset
TCD0_CITER_ELINKYE 36h
S
TCD1_CITER_ELINKYE 4036h
S
TCD2_CITER_ELINKYE 8036h
S
TCD3_CITER_ELINKYE C036h
S
TCD4_CITER_ELINKYE 1_0036h
S
TCD5_CITER_ELINKYE 1_4036h
S
TCD6_CITER_ELINKYE 1_8036h
S
TCD7_CITER_ELINKYE 1_C036h
S
TCD8_CITER_ELINKYE 2_0036h
S
TCD9_CITER_ELINKYE 2_4036h
S
TCD10_CITER_ELINKY 2_8036h
ES
TCD11_CITER_ELINKY 2_C036h
ES
TCD12_CITER_ELINKY 20_0036h
ES
TCD13_CITER_ELINKY 20_4036h
ES
TCD14_CITER_ELINKY 20_8036h
ES
TCD15_CITER_ELINKY 20_C036h
ES
TCD16_CITER_ELINKY 21_0036h
ES
TCD17_CITER_ELINKY 21_4036h
ES
Table continues on the next page...

---

*Page 352*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
TCD18_CITER_ELINKY 21_8036h
ES
TCD19_CITER_ELINKY 21_C036h
ES
TCD20_CITER_ELINKY 22_0036h
ES
TCD21_CITER_ELINKY 22_4036h
ES
TCD22_CITER_ELINKY 22_8036h
ES
TCD23_CITER_ELINKY 22_C036h
ES
TCD24_CITER_ELINKY 23_0036h
ES
TCD25_CITER_ELINKY 23_4036h
ES
TCD26_CITER_ELINKY 23_8036h
ES
TCD27_CITER_ELINKY 23_C036h
ES
TCD28_CITER_ELINKY 24_0036h
ES
TCD29_CITER_ELINKY 24_4036h
ES
TCD30_CITER_ELINKY 24_8036h
ES
TCD31_CITER_ELINKY 24_C036h
ES
Function
If TCDn_CITER[ELINK] is 1, the TCDn_CITER register is defined as follows.

---

*Page 353*

Enhanced Direct Memory Access (eDMA)
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
Reserv
R
ed
ELINK LINKCH CITER
W 0
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
15 Enable Link
ELINK As the channel completes the minor loop, this flag enables linking to another channel as defined by the
relevant LINKCH field. When enabled, an internal mechanism sets the TCDn_CSR[START] field of the
specified channel (LINKCH) upon minor loop completion.
If channel linking is disabled, the CITER value is extended to 15 bits in place of a link channel number. If the
major loop is exhausted, this link mechanism is suppressed in favor of MAJORELINK channel linking.
NOTE
This field must be equal to the BITER[ELINK] field; otherwise, a configuration error
is reported.
0b - Channel-to-channel linking disabled
1b - Channel-to-channel linking enabled
Reserved
14
—
13-9 Minor Loop Link Channel Number
LINKCH If channel-to-channel linking is enabled (ELINK = 1), then after the minor loop is exhausted the eDMA
engine initiates a channel service request to the channel defined by this field by writing that channel’s
TCDn_CSR[START] field to 1.
8-0 Current Major Iteration Count
CITER This 9-bit (ELINK = 1) or 15-bit (ELINK = 0) count represents the current major loop count for the channel.
It is decremented each time the channel finishes a service request and is written back to the TCD memory.
After the major iteration count is exhausted, the channel performs a number of operations — for example,
final source and destination address calculations — and optionally generates an interrupt to signal channel
completion before reloading the CITER field from the Beginning Iteration Count (BITER) field.
NOTE
When the CITER field is initially loaded by software, it must be set to the same value as that
contained in the BITER field.
NOTE
If the channel is configured to execute a single service request, the initial values of BITER
and CITER should be 0x0001.

---

*Page 354*

Enhanced Direct Memory Access (eDMA)
14.6.2.17 TCD Last Destination Address Adjustment / Scatter Gather Address (TCD0_DLAST_SGA -
TCD31_DLAST_SGA)
Offset
Register Offset
TCD0_DLAST_SGA 38h
TCD1_DLAST_SGA 4038h
TCD2_DLAST_SGA 8038h
TCD3_DLAST_SGA C038h
TCD4_DLAST_SGA 1_0038h
TCD5_DLAST_SGA 1_4038h
TCD6_DLAST_SGA 1_8038h
TCD7_DLAST_SGA 1_C038h
TCD8_DLAST_SGA 2_0038h
TCD9_DLAST_SGA 2_4038h
TCD10_DLAST_SGA 2_8038h
TCD11_DLAST_SGA 2_C038h
TCD12_DLAST_SGA 20_0038h
TCD13_DLAST_SGA 20_4038h
TCD14_DLAST_SGA 20_8038h
TCD15_DLAST_SGA 20_C038h
TCD16_DLAST_SGA 21_0038h
TCD17_DLAST_SGA 21_4038h
TCD18_DLAST_SGA 21_8038h
TCD19_DLAST_SGA 21_C038h
TCD20_DLAST_SGA 22_0038h
TCD21_DLAST_SGA 22_4038h
TCD22_DLAST_SGA 22_8038h
TCD23_DLAST_SGA 22_C038h
TCD24_DLAST_SGA 23_0038h
TCD25_DLAST_SGA 23_4038h
TCD26_DLAST_SGA 23_8038h
TCD27_DLAST_SGA 23_C038h
TCD28_DLAST_SGA 24_0038h
TCD29_DLAST_SGA 24_4038h
Table continues on the next page...

---

*Page 355*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
TCD30_DLAST_SGA 24_8038h
TCD31_DLAST_SGA 24_C038h
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
DLAST_SGA
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
DLAST_SGA
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
31-0 Last Destination Address Adjustment / Scatter Gather Address
DLAST_SGA Adjustment of the last destination address or the memory address for the next transfer control descriptor to
be loaded into this channel (scatter/gather).
If (TCDn_CSR[ESG] = 0) then:
• Adjustment value is added to the destination address at the completion of the major iteration count.
This value can apply to restore the destination address to the initial value or adjust the address to
reference the next data structure.
• This field uses two's complement notation for the final destination address adjustment.
Otherwise:
• This address points to the beginning of a 0-modulo 32-byte region containing the next transfer
control descriptor to be loaded into this channel. This channel reload is performed as the major
iteration count completes. The scatter/gather address must be 0-modulo 32-byte, or else a
configuration error is reported.
14.6.2.18 TCD Control and Status (TCD0_CSR - TCD31_CSR)
Offset
Register Offset
TCD0_CSR 3Ch
Table continues on the next page...

---

*Page 356*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
TCD1_CSR 403Ch
TCD2_CSR 803Ch
TCD3_CSR C03Ch
TCD4_CSR 1_003Ch
TCD5_CSR 1_403Ch
TCD6_CSR 1_803Ch
TCD7_CSR 1_C03Ch
TCD8_CSR 2_003Ch
TCD9_CSR 2_403Ch
TCD10_CSR 2_803Ch
TCD11_CSR 2_C03Ch
TCD12_CSR 20_003Ch
TCD13_CSR 20_403Ch
TCD14_CSR 20_803Ch
TCD15_CSR 20_C03Ch
TCD16_CSR 21_003Ch
TCD17_CSR 21_403Ch
TCD18_CSR 21_803Ch
TCD19_CSR 21_C03Ch
TCD20_CSR 22_003Ch
TCD21_CSR 22_403Ch
TCD22_CSR 22_803Ch
TCD23_CSR 22_C03Ch
TCD24_CSR 23_003Ch
TCD25_CSR 23_403Ch
TCD26_CSR 23_803Ch
TCD27_CSR 23_C03Ch
TCD28_CSR 24_003Ch
TCD29_CSR 24_403Ch
TCD30_CSR 24_803Ch
TCD31_CSR 24_C03Ch

---

*Page 357*

Enhanced Direct Memory Access (eDMA)
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
MAJO INTHA INTMA STAR
BWC MAJORLINKCH ESDA EEOP ESG DREQ
REL ... LF JOR T
W 0
Reset u u u u u u u u u u u u u u u 0
Fields
Field Function
15-14 Bandwidth Control
BWC Throttles the amount of bus bandwidth consumed by the eDMA. Generally, as the eDMA processes the
minor loop, it continuously generates read/write sequences until the minor count is exhausted. This field
forces eDMA to stall after the completion of each read/write access, to control the bus request bandwidth
seen by the system bus interconnect.
NOTE
If the source and destination sizes are equal, this field is ignored between the first and
second transfers and after the last write of each minor loop. This behavior is a side effect of
reducing start-up latency.
00b - No eDMA engine stalls
01b - Enable eDMA master high-priority elevation (HPE) mode. No eDMA engine stalls.
10b - eDMA engine stalls for 4 cycles after each R/W
11b - eDMA engine stalls for 8 cycles after each R/W
Reserved
13
—
12-8 Major Loop Link Channel Number
MAJORLINKCH If (MAJORELINK = 0) then:
• No channel-to-channel linking, or chaining, is performed after the major loop counter is exhausted.
Otherwise:
• After the major loop counter is exhausted, the eDMA engine initiates a channel service request at
the channel defined by this field by setting that channel’s TCDn_CSR[START] field to 1.
7 Enable Store Destination Address
ESDA As the channel completes the major loop by either the current iteration counter (CITER) decrementing to 0,
or by receiving an enabled end-of-packet signal, this field enables writing the destination address (DADDR)
to the address stored in the SLAST_SDA field. The value written to system memory is the last DADDR value
prior to the DLAST_SGA offset being applied, or overwritten by an enabled scatter/gather operation. When
the SDA bit is 1, SLAST_SDA contains the write pointer instead of the final source address offset. Because
this is a pointer and not a final offset, a last source address offset of zero is applied to SADDR instead of
the SLAST_SGA value.
0b - Ability to store destination address to system memory disabled
Table continues on the next page...

---

*Page 358*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Field Function
1b - Ability to store destination address to system memory enabled
6 Enable End-Of-Packet Processing
EEOP When enabled by the EEOP field, an end-of-packet hardware input signal directs eDMA to discontinue
executing the active channel, and to treat the shutdown as the major-loop-completed event. If the EEOP
field is 1, the end-of-packet signal from supported peripherals is accepted. If the EEOP field is 0, the
end-of-packet input is ignored. With an end-of-packet retirement, the current TCD destination address (or
ESDA-saved destination address), minus the software-saved initial address (DADDR), reflects the total
amount of data transferred.
0b - End-of-packet operation disabled
1b - End-of-packet hardware input signal enabled
5 Enable Link When Major Loop Complete
MAJORELINK As the channel completes the major loop, this flag enables linking to another channel defined by
MAJORLINKCH. The link target channel initiates a channel service request via an internal mechanism that
sets the TCDn_CSR[START] field of the specified channel.
NOTE
To support the dynamic linking coherency model, this field is forced to 0 if written when
TCDn_CSR[DONE] is 1.
0b - Channel-to-channel linking disabled
1b - Channel-to-channel linking enabled
4 Enable Scatter/Gather Processing
ESG As the channel completes the major loop, this flag enables scatter/gather processing in the current channel.
If enabled, the eDMA engine uses TCDn_DLAST_SGA as a memory pointer to a 0-modulo 32-bit address
containing a 32-byte data structure, which is loaded as the transfer control descriptor into local memory.
NOTE
To support the dynamic scatter/gather coherency model, this field is forced to 0 if written
when TCDn_CSR[DONE] is 1.
0b - Current channel’s TCD is normal format
1b - Current channel’s TCD specifies scatter/gather format.
3 Disable Request
DREQ If this flag is 1, the eDMA hardware automatically clears the corresponding ERQ bit when the current
major iteration count reaches 0.
0b - No operation. Channel’s ERQ field not affected
1b - Clear the ERQ field to 0 upon major loop completion, thus disabling hardware service
requests. Channel’s ERQ field cleared to 0 when major loop complete
2 Enable Interrupt If Major Counter Half-complete
Table continues on the next page...

---

*Page 359*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Field Function
INTHALF If this flag is 1, the channel generates an interrupt request by setting the appropriate field in the INT
register to 1 when the current major iteration count reaches the halfway point. Specifically, the comparison
performed by the eDMA engine is (CITER = (BITER/2)). This halfway point interrupt request is provided to
support double-buffered, also known as ping-pong, schemes, or other types of data movement where the
processor needs an early indication of the transfer’s progress.
NOTE
If BITER = 1, do not use INTHALF; use INTMAJOR instead.
0b - Halfway point interrupt disabled
1b - Halfway point interrupt enabled
1 Enable Interrupt If Major count complete
INTMAJOR If this flag is 1, the channel generates an interrupt request by setting the appropriate field in the INT
register to 1 when the current major iteration count (CITER) reaches 0.
0b - End-of-major loop interrupt disabled
1b - End-of-major loop interrupt enabled
0 Channel Start
START If this flag is 1, the channel is requesting service. The eDMA hardware automatically clears this flag to 0
after the channel begins execution.
0b - Channel not explicitly started
1b - Channel explicitly started via a software-initiated service request
14.6.2.19 TCD Beginning Major Loop Count (Minor Loop Channel Linking Disabled) (TCD0_BITER_ELINKNO -
TCD31_BITER_ELINKNO)
Offset
Register Offset
TCD0_BITER_ELINKNO 3Eh
TCD1_BITER_ELINKNO 403Eh
TCD2_BITER_ELINKNO 803Eh
TCD3_BITER_ELINKNO C03Eh
TCD4_BITER_ELINKNO 1_003Eh
TCD5_BITER_ELINKNO 1_403Eh
TCD6_BITER_ELINKNO 1_803Eh
TCD7_BITER_ELINKNO 1_C03Eh
TCD8_BITER_ELINKNO 2_003Eh
Table continues on the next page...

---

*Page 360*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
TCD9_BITER_ELINKNO 2_403Eh
TCD10_BITER_ELINKN 2_803Eh
O
TCD11_BITER_ELINKN 2_C03Eh
O
TCD12_BITER_ELINKN 20_003Eh
O
TCD13_BITER_ELINKN 20_403Eh
O
TCD14_BITER_ELINKN 20_803Eh
O
TCD15_BITER_ELINKN 20_C03Eh
O
TCD16_BITER_ELINKN 21_003Eh
O
TCD17_BITER_ELINKN 21_403Eh
O
TCD18_BITER_ELINKN 21_803Eh
O
TCD19_BITER_ELINKN 21_C03Eh
O
TCD20_BITER_ELINKN 22_003Eh
O
TCD21_BITER_ELINKN 22_403Eh
O
TCD22_BITER_ELINKN 22_803Eh
O
TCD23_BITER_ELINKN 22_C03Eh
O
TCD24_BITER_ELINKN 23_003Eh
O
TCD25_BITER_ELINKN 23_403Eh
O
TCD26_BITER_ELINKN 23_803Eh
O
TCD27_BITER_ELINKN 23_C03Eh
O
Table continues on the next page...

---

*Page 361*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
TCD28_BITER_ELINKN 24_003Eh
O
TCD29_BITER_ELINKN 24_403Eh
O
TCD30_BITER_ELINKN 24_803Eh
O
TCD31_BITER_ELINKN 24_C03Eh
O
Function
If the TCDn_BITER[ELINK] field is 0, the TCDn_BITER register is defined as follows.
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
ELINK BITER
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
15 Enables Link
ELINK As the channel completes the minor loop, this flag enables linking to another channel as defined by
BITER[LINKCH]. The link target channel initiates a channel service request via an internal mechanism that
sets the TCDn_CSR[START] field of the specified channel. If channel linking is disabled, the BITER value
extends to 15 bits in place of a link channel number. If the major loop is exhausted, this link mechanism is
suppressed in favor of the MAJORELINK channel linking.
NOTE
When the software loads the TCD, this field must be set equal to the corresponding CITER
field; otherwise, a configuration error is reported. As the major iteration count is exhausted,
eDMA reloads the contents of this field into the CITER field.
0b - Channel-to-channel linking disabled
1b - Channel-to-channel linking enabled
14-0 Starting Major Iteration Count
BITER As the transfer control descriptor is first loaded by software, this 9-bit (ELINK = 1) or 15-bit (ELINK = 0) field
must be set equal to the value in the CITER field. As the major iteration count is exhausted, eDMA reloads
the contents of this field into the CITER field. If the channel is configured to execute a single service request,
the initial values of BITER and CITER must be 0x0001.

---

*Page 362*

Enhanced Direct Memory Access (eDMA)
14.6.2.20 TCD Beginning Major Loop Count (Minor Loop Channel Linking Enabled) (TCD0_BITER_ELINKYES -
TCD31_BITER_ELINKYES)
Offset
Register Offset
TCD0_BITER_ELINKYE 3Eh
S
TCD1_BITER_ELINKYE 403Eh
S
TCD2_BITER_ELINKYE 803Eh
S
TCD3_BITER_ELINKYE C03Eh
S
TCD4_BITER_ELINKYE 1_003Eh
S
TCD5_BITER_ELINKYE 1_403Eh
S
TCD6_BITER_ELINKYE 1_803Eh
S
TCD7_BITER_ELINKYE 1_C03Eh
S
TCD8_BITER_ELINKYE 2_003Eh
S
TCD9_BITER_ELINKYE 2_403Eh
S
TCD10_BITER_ELINKY 2_803Eh
ES
TCD11_BITER_ELINKY 2_C03Eh
ES
TCD12_BITER_ELINKY 20_003Eh
ES
TCD13_BITER_ELINKY 20_403Eh
ES
TCD14_BITER_ELINKY 20_803Eh
ES
TCD15_BITER_ELINKY 20_C03Eh
ES
TCD16_BITER_ELINKY 21_003Eh
ES
TCD17_BITER_ELINKY 21_403Eh
ES
Table continues on the next page...

---

*Page 363*

Enhanced Direct Memory Access (eDMA)
Table continued from the previous page...
Register Offset
TCD18_BITER_ELINKY 21_803Eh
ES
TCD19_BITER_ELINKY 21_C03Eh
ES
TCD20_BITER_ELINKY 22_003Eh
ES
TCD21_BITER_ELINKY 22_403Eh
ES
TCD22_BITER_ELINKY 22_803Eh
ES
TCD23_BITER_ELINKY 22_C03Eh
ES
TCD24_BITER_ELINKY 23_003Eh
ES
TCD25_BITER_ELINKY 23_403Eh
ES
TCD26_BITER_ELINKY 23_803Eh
ES
TCD27_BITER_ELINKY 23_C03Eh
ES
TCD28_BITER_ELINKY 24_003Eh
ES
TCD29_BITER_ELINKY 24_403Eh
ES
TCD30_BITER_ELINKY 24_803Eh
ES
TCD31_BITER_ELINKY 24_C03Eh
ES
Function
If the TCDn_BITER[ELINK] field is set, the TCDn_BITER register is defined as follows.

---

*Page 364*

Enhanced Direct Memory Access (eDMA)
Diagram
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
Reserv
R
ed
ELINK LINKCH BITER
W 0
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
15 Enable Link
ELINK As the channel completes the minor loop, this flag enables linking to another channel as defined by
BITER[LINKCH]. The link target channel initiates a channel service request via an internal mechanism that
sets the TCDn_CSR[START] field of the specified channel. If channel linking disables, the BITER value
extends to 15 bits in place of a link channel number. If the major loop is exhausted, this link mechanism is
suppressed in favor of the MAJORELINK channel linking.
NOTE
When the software loads the TCD, this field must be set equal to the corresponding CITER
field; otherwise, a configuration error is reported. As the major iteration count is exhausted,
eDMA reloads the contents of this field into the CITER field.
0b - Channel-to-channel linking disabled
1b - Channel-to-channel linking enabled
Reserved
14
—
13-9 Link Channel Number
LINKCH If channel-to-channel linking is enabled (ELINK = 1), then after the minor loop is exhausted, the eDMA
engine initiates a channel service request at the channel defined by this field by setting that channel’s
TCDn_CSR[START] field.
NOTE
When the software loads the TCD, this field must be set equal to the corresponding CITER
field; otherwise, a configuration error is reported. As the major iteration count is exhausted,
eDMA reloads the contents of this field into the CITER field.
8-0 Starting Major Iteration Count
BITER As the transfer control descriptor is first loaded by software, this 9-bit (ELINK = 1) or 15-bit (ELINK = 0) field
must be set equal to the value in the CITER field. As the major iteration count is exhausted, eDMA reloads
the contents of this field into the CITER field. If the channel is configured to execute a single service request,
the initial values of BITER and CITER must be 0x0001.

#### 14.7 Glossary

TCD Transfer control descriptor

---

*Page 365*

