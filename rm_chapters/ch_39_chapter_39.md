<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 39 -->

# Chapter 39

# Messaging Unit (MU)

#### 39.1 Chip-specific MU information

#### 39.1.1 MU instances and configuration

This chip includes MUs for communication across the different cores. Each MU includes two interfaces, MUA and MUB. Two
different processors control them for communication.
NOTE
The ELE_HSEB core controls the MUA interface of MU_0 and MU_1. Therefore, the application core cannot control
the interface.
The base address of MU_1's MUB interface is different across the MCXE31 product series because AIPS_2 is unavailable in
MCXE317 and MCXE315/MCXE316. Table 197 summarizes this difference.
Table 197. Base-address difference in MU_1's MUB interface across MCXE 31 product series
MCXE31B MCXE317 MCXE315/MCXE316
Base address 404E_C000h 4039_0000h

#### 39.2 Overview

MU enables two processors on a chip to communicate and coordinate by passing messages (for example, data, status, and
control) through the MU interface. MU also allows one processor to signal the other processor using interrupts.
MU must synchronize the accesses from one side to the other because MU can manage messaging between processors using
different clocks. MU accomplishes synchronization using two sets of matching registers: processor A-facing and processor B-
facing.

---

*Page 996*

Messaging Unit (MU)

#### 39.2.1 Block diagram

Processor A MU Processor B
MUA MUB
TX and RX TX and RX
registers registers
Status and Status and
control control
Processor A registers registers Processor B
peripheral peripheral
bus bus
Sync and Sync and
control control
registers registers
Generate Generate
interrupts interrupts
Interrupts to Interrupts to
processor A processor B
interrupt interrupt
controller controller
Figure 143. Block diagram

#### 39.2.2 Features

• Memory-mapped registers (MU is connected as a peripheral under the peripheral bus on the processor A-side and
processor B-side)
• Synchronized message transfers between cores
— To send data or messages from one side to the other, MUA provides 4 transmit registers and 4 receive registers.
MUB provides 4 transmit registers and 4 receive registers.
— Transmit empty flags (TSR[TEn]) and receive full flags (RSR[RFn]) facilitate the transfer of data or messages
between cores on both sides of MU.
— A synchronization mechanism updates the transmit and the receive flags. There is an inherent latency between
updating the flag on one side and reflecting its status on the other side. See Event update timing .
— MU has a 3-bit flag data register, which you can use to send flag data between the two MU sides.
• Interprocessor interrupts
— MU has 9 interrupt sources on each side (processor A-side, processor B-side) for signaling the other processor.
You can use the interrupts for notification of receive and transmit events and for general-purpose signaling between
processors. There are 1 general-purpose interrupt requests available and 8 receive and transmit interrupt sources.
• Reset (Each processor can issue a reset to MU via CR[MUR] , which is a self-clearing field).

#### 39.3 Functional description

MU enables two cores (processor A and processor B) to communicate with each other:
• By sharing messages and data.
• By enabling one core to wake the other core by using interrupts.
The messaging, control, and status registers of the two cores are mapped to processor A memory and processor B memory as
a regular peripheral. The peripheral data bus is 32 bits wide inside MU.

---

*Page 997*

Messaging Unit (MU)
Messaging logic is used with shared memory. Various messaging methods can implement a messaging protocol. For example,
a message could mean one of the following:
• A message of n words has been written starting at offset x in the memory.
• The previous data block that was sent has been read.
The ability to keep messaging logic independent of the shared memory is not restricted to a predefined hardware protocol. The
software required to manage the messaging is short and straightforward.
Most of the messaging mechanisms are symmetric. They are duplicated and are available on both the processor A side and
processor B side. The messaging mechanisms are:
• 4 32-bit transmit registers, which are each reflected in 4 read-only receive registers on the side of the other processor.
These registers can transfer 32-bit word messages or the frame information of the messages written to the shared
memory. For example, they can transfer the number of words, initial address, and message type code.
• Writing to a transmitter-side transmit register (TR n ) clears the Transmitter Empty flag in the transmitter-side Status register
(TS n [TE n ]), and sets the Receiver Full flag in the receiver-side Status register (RS n [RF n ]). Setting the flag on the receiver
side can trigger an interrupt on the receiver side (a maskable receive interrupt).
• Reading a receiver-side receive register (RR n ) clears the Receiver Full flag in the receiver-side Status register
(RS n [RF n ]), and sets the Transmitter Empty flag in the transmitter-side Status register (TS n [TE n ]). Setting the Transmitter
Empty flag can trigger an interrupt on the transmitter side (a maskable transmit interrupt).
• 1 general-purpose interrupt request flags are reflected in General-purpose Status (GSR) on the receiver side.
• 3-bit flag data is transmitted from Flag Control (FCR) to Flag Status (FSR) on the side receiving the flag data. SR[FUP]
sets when the flag data is transmitted and clears when the receiving side acknowledges the flag data (the flag is updated).
Writing to a transmit register signals to the receiver side that data is ready for retrieval.
Do not write to the transmit register again without verifying that the data is retrieved. The transmitter side cannot determine the
exact time that the receiver attempts to retrieve the data. Before attempting to write to the transmit register again, the transmitter
side must wait for the Transmitter Empty interrupt or it must poll TSR[TE n ].
Reading a receive register signals to the transmitter side that data can be written to that register.
Do not read the receive register again without verifying that the data is written. The receiver side cannot determine the exact time
that the transmitter attempts to write the data. Before attempting to read the receive register again, the receiver side must wait for
the Receiver Full interrupt or it must poll RSR[RF n ].

#### 39.3.1 Submodules

39.3.1.1 MUA side
MUA receives its register configuration via the processor A peripheral bus. It sends or receives messages to or from MUB.
Processor A can receive messages by reading MUA registers, and MUA can send interrupts to processor A when interrupts
are enabled.
NOTE
Processor B should not access MUA registers. Processor C, if exist, should also not access MUA registers. TRDC
might be needed to prevent such illegal access of Processor B or C to MUA registers.
39.3.1.2 MUB side
MUB receives its register configuration via the processor B peripheral bus. It sends or receives messages to or from MUA.
Processor B can receive messages by reading MUB registers, and MUB can send interrupts to processor B when interrupts
are enabled.

---

*Page 998*

Messaging Unit (MU)
NOTE
Processor A should not access MUB registers. Processor C, if exist, should also not access MUB registers. TRDC
might be needed to prevent such illegal access of Processor A or C to MUB registers.

#### 39.3.2 Event update timing

The messaging side of each processor has a hardware mechanism to send event update requests to the other processor. An event
occurs when the status register of the receiving processor must reflect any information change. The event update latency is the
delay between the event being ready at one processor and the resulting update at the status register of the other processor:
• The minimum event latency is (1 clock cycle of the sending side) + (2.5 clock cycles of the receiving side). This minimum
case happens when no event is pending when the new event occurs.
• The maximum event latency is (6 clock cycles of the sending side) + (6.5 clock cycles of the receiving side). The
maximum case happens when the event occurs just after a previous event is sent to the other side.
The event update latency varies depending on the time at which the subsequent event is triggered.

#### 39.3.3 Clocking

Table 198. MU clocks
Clock name Description
Bus clock MUA Used only for bus accesses to MUA control and configuration registers
Bus clock MUB Used only for bus accesses to MUB control and configuration registers

#### 39.3.4 Resets

The following sections list the reset sources included in MU. Each reset performs a different function for the MU module compared
to the function it performs for the system.
39.3.4.1 Asynchronous system reset
When the asynchronous system reset on either side of MU is asserted, SR[MURS] becomes 1 until the asynchronous system
reset sequence on both the MUA and MUB sides ends. Verify that SR[MURS] becomes 0 before accessing MU.
The asynchronous system reset on one side of MU resets the other side of MU. The reset forces all control and status registers
to return to their default values and clears all internal states. The following table shows the exceptions to this behavior.
Table 199. Exceptions to asynchronous system reset
MUA-side exceptions MUB-side exceptions
MUB_CR[MURIE] MUA_CR[MURIE]
MUB_SR[MURIP] MUA_SR[MURIP]
MUB_SR[MURS] MUA_SR[MURS]
39.3.4.2 MU software reset
Writing 1 to CR[MUR] causes most control and status registers to return to their default values and clears all internal states.
The instruction immediately following the assertion of CR[MUR] must not write to the MU registers. The reset sequence may
overwrite such a write, with the register retaining the reset value. To know the end of the reset sequence for both processors,
monitor the value of SR[MURS] . After the reset sequence on both processors has ended, a write to the MU registers can
be attempted.

---

*Page 999*

Messaging Unit (MU)
NOTE
The process of CR[MUR] becoming 1 is delicate because it asynchronously affects the registers on the other side.
CR[MUR] becoming 1 may cause unpredictable behavior if, for example, the other processor is concurrently testing
an MU register field (TSR[TE n ] in the other processor). Before writing 1 to CR[MUR], verify that the other processor
is not engaged in an MU signaling activity.

#### 39.3.5 Interrupts

MU controls interrupt requests that one processor makes to the other processor. This section describes all the interrupts that the
module generates.
MU can generate these interrupt sources individually to send to the processors:
• 4 receive interrupts (asserted when the Receive Full flags are set in Receive Status (RSR) and enabled in Receive
Control (RCR) ) for each receive register. These interrupts are Ored into a single interrupt for each MUA or MUB, please
consult the interrupt map file attached to this document.
• 4 transmit interrupts (asserted when the Transmit Empty flags are set in Transmit Status (TSR) and enabled in Transmit
Control (TCR) ) for each transmit register. These interrupts are Ored into a single interrupt for each MUA or MUB, please
consult the interrupt map file attached to this document.
• 1 general-purpose interrupt (asserted when the GIP flag is set in General-Purpose Interrupt Enable (GIER) and enabled in
General-Purpose Interrupt Enable (GIER) )
• Core Reset Assertion interrupts (asserted when the processor enters the Reset state and enabled by RAIE)
All interrupts are maskable in the processor control registers: TCR, RCR, GIER, and CR. MU does not assume any internal priority
of these interrupts. Multiple interrupts (for example, receive 0 and receive 1, or any transmit or general-purpose interrupt) can be
asserted simultaneously. The interrupt controller must resolve the priority of these interrupts at the chip level.
Triggering any enabled interrupt wakes the processor from below mode before servicing the interrupt
• Wait mode
• Stop mode
• Power-down mode
The software (as part of the interrupt handler) must clear the general-purpose interrupt pending flag (GSR[GIP0]) to deassert the
request to the interrupt controller.
When a processor writes to the general-purpose interrupt flag (GCR[GIR0]), MU synchronizes the write event to the other
processor to set the general-interrupt request pending flag (GSR[GIP0]). When GSR[GIP0] is set, if the general-purpose interrupt
is enabled on the receiving processor side (GIER[GIE0] is 1), the transmitting-side general-purpose interrupt is issued to the
receiving processor, which clears this interrupt by writing 1 to GSR[GIP0]. The interrupt is deasserted as soon as the write to
GSR[GIP0] occurs. MU synchronizes the write event of GSR[GIP0] to the other processor. The synchronized signal clears the
GIR0 flag.
Before writing 1 to GCR[GIR0], verify that this field is 0, which means that a general interrupt is not pending. Generally, MU ignores
writing 1 to this field while the field is already 1, but in some cases it may issue a second interrupt.

#### 39.4 External signals

This module has no external signals.

#### 39.5 Initialization

MU does not require initialization.

#### 39.6 Application information

MU facilitates messages between processors. For example, MU passes:

---

*Page 1000*

Messaging Unit (MU)
• Short messages. Transmit registers can pass short messages from 1 to 4 words in length for processor A and from 1 to
4 words for processor B. For example, for a four-word message, only one register must have its corresponding interrupt
enabled on the receiving side. The first three words of the message are written to the registers with masked interrupts.
The fourth word is written to the last register, triggering an interrupt on the receiving side.
• Frame information. Transmit registers can pass frame information for long messages written to the shared system
memory. Such frame information normally includes a start address and a number of words. It can include a message type
code.
• Event notices and requests. MU can signal events and requests that do not include data words between processors using
general-purpose interrupts. For example, one such event is acknowledging that a long message is read from the shared
system memory.
• Fixed-length data. Formatted data with a fixed length can be written to predetermined locations in the shared memory. A
processor can use general-purpose interrupts to signal to the other processor that the data is ready.
• Announcements. A processor can use the 3 flags to announce its current program state or other billboard messages to the
other processor.

#### 39.6.1 Messaging protocols using interrupts

The example below describes a four-word messaging sequence sent between the processor A and processor B.
The transmitting processor writes to the transmit registers sequentially. When n = 0, 1, and 2, the interrupts are disabled, so no
interrupt goes to the processor (although interrupt conditions occur). For n = 3, the interrupt is enabled, and MU generates the last
receive interrupt request.
1. Write sequence:
a. The transmitting processor writes the message information sequentially to its Transmit registers 0, 1, 2.
b. When the write to Transmit register 3 occurs, RSR [RF3] is set after synchronization. It immediately triggers the
receive full 3 interrupt on the receiving processor.
2. Read sequence:
a. The receiving processor receives the receive full 3 interrupt and starts reading the message transferred from the
receive registers.
b. After the receiving processor reads Receive register 3, MU clears RSR [RF3].
Table 200 and Figure 144 describe the messaging model, using transmit and receive registers and the interrupt
messaging protocol.
Table 200. Interrupt messaging protocol (generalized)
Step Action Description
1 Processor A writes data. RR n on processor B's side reflects a data write to TR n by
processor A.
2 Clear the transmitter empty flag and
The data write to TR n :
set the receiver full flag.
• Clears TSR[TE n ] on the processor A side.
• Sets RSR[RF n ] on the processor B side.
3 Generate the receive interrupt Setting RSR[RF n ] generates a receive interrupt request to
request. processor B.
4 Processor B reads the data. After receiving the receive interrupt request, processor B
performs a data read of RR n .
Table continues on the next page...

---

*Page 1001*

Messaging Unit (MU)
Table 200. Interrupt messaging protocol (generalized) (continued)
Step Action Description
5 Clear the receiver full flag and set Reading the data from the RR n register:
the transmitter empty flag.
• Clears RSR[RF n ] on the processor B side.
• Sets TSR[TE n ] on the processor A side.
6 Generate the transmit interrupt Setting TSR[TE n ] generates a transmit interrupt request to
request. processor A.
Processor A MU Processor B
Transmitter side Receiver side
Data write Data read
1 4
Tx status Rx status
Registers Registers
2 5
Clear Set
TE n RF n
Tx empty Rx full
Set Clear
A read from the A write from the
fourth receive register fourth transmit register
triggers an interrupt triggers an interrupt
Tx control Rx control
TIE n RIE n
Interrupt Interrupt
6 3
enable enable
Transmit Receive
interrupt interrupt
request request
Figure 144. Messaging model using transmit and receive registers
You can use the messaging hardware to implement messaging protocols for an array of message types. MU provides full support
for interrupt and polling management schemes.

#### 39.6.2 Messaging protocols using event interrupts

MU can signal events and requests that do not include data words between processors using general-purpose interrupts.
Formatted data with a fixed length can be written to predetermined locations in the shared memory. A processor can use a
general-purpose interrupt to signal to the other processor that the data is ready.
A processor can use the 3 flags to announce its current program state (or similar messages) to the other processor.
Table 201 and Figure 145 describe the event steps when the processor triggers a general-purpose interrupt.
Table 201. General-purpose interrupt messaging protocol (generalized)
Step Action Description
1 Processor A sets its associated Processor A sets GCR[ GIR0 ].
general-purpose interrupt request
flag.
Table continues on the next page...

---

*Page 1002*

Messaging Unit (MU)
Table 201. General-purpose interrupt messaging protocol (generalized) (continued)
Step Action Description
2 The general-purpose interrupt GSR[ GIP0 ] is set.
request pending status flag is set.
3 MU generates the general-purpose Setting GSR[ GIP0 ] generates the general-purpose interrupt
interrupt request to processor B. request to processor B. GIER[ GIE0 ] must be set for
processor B.
4 Processor B reads the status Processor B reads GSR[ GIP0 ].
register.
5 Processor B services the interrupt. —
Processor B writes 1 to the corresponding GSR[ GIP0 ] flag to
6 Processor B sets GSR[ GIP0 ] to clear
clear the interrupt.
the interrupt.
7 GCR[ GIR0 ] is cleared. Setting GSR[ GIP0 ] clears GCR[ GIR0 ] on the processor A
side.
Processor A Processor B
Messaging Unit (MU)
4
Read GIP0 bit
Control Status
Services
5
Interrupt
2 GIP0
1 GIR0 set
set int req int pend 6
7
clear Write "1" to clear
GCR GSR
Register Register
Control
Interrupt General Purpose
GIE0 3
Enable Interrupt Request
GIER
Register
Figure 145. Messaging model using a general-purpose interrupt

#### 39.6.3 Exclusive access to shared memory

MU can signal one processor about its current access to shared memory. This signaling prevents the other processor from
overwriting data during the exclusive memory access period.
The following tables describe the signaling protocol that processor A uses to inform processor B about its current access (write)
to shared memory:
• Table 202 shows processor A performing an exclusive access to shared memory.
• Table 203 shows processor B scanning for transaction information.

---

*Page 1003*

Messaging Unit (MU)
• Table 204 shows processor B accepting exclusive access from processor A.
• Table 205 shows processor B rejecting exclusive access from processor A.
According to the examples shown in the following tables, GCR[ GIR0 ], RR n , and TR n are reserved to support exclusive access
to the shared memory protocol.
Table 202. Processor A performs an exclusive access to shared memory
Step Action Description
1 Processor A sends the GIR0 Before processor A performs an exclusive access to the
request to processor B using the shared memory, it sends a GIR0 request to processor B.
processor A control register.
2 Processor A sends an exclusive- Processor A sends an exclusive-access request (command,
access request using a transmit location, and length of target access) to processor B using a
data register (TR n ). selected transmit data register (TR0).
3 Processor A waits for a dedicated Processor A waits for a dedicated interrupt (as
interrupt from processor B. an acknowledgment) triggered by processor B before
proceeding.
4 Processor A accesses the shared After receiving a dedicated interrupt from processor B,
memory. processor A proceeds.
Table 203. Processor B scans for transaction information
Step Action Description
1 Processor B receives an interrupt —
from a receive data register (RR n ).
2 Processor B reads the receive data —
register (RR n ).
3 Processor B scans the receive data Processor B scans for transaction information (whether
register contents. processor A has requested exclusive access).
Table 204. Processor B accepts exclusive access from processor A
Step Action Description
1 Processor B triggers a dedicated Processor B acknowledges the request from processor A by
interrupt. triggering a dedicated interrupt (ack) to processor A.
2 Processor B sends a code message With the acknowledge interrupt, processor B sends a code
to processor A. message to processor A through the selected transmit
register (TR n ). The message informs processor A that it can
exclusively access the shared memory.
Table 205. Processor B rejects exclusive access from processor A
Step Action Description
1 Processor B ignores the request If processor B does not provide permission to processor A,
from processor A for exclusive processor B ignores the exclusive-access request.
access.

---

*Page 1004*

Messaging Unit (MU)

#### 39.6.4 Packet data transfers

The following table describes an example packet transfer sequence between processor B and processor A subsystems.
Table 206. Packet data transfer sequence
Step Action Description
1 Processor B requests DMA. Processor B sends a DMA request to initiate the packet data
transfer.
2 DMA acknowledges.
3 DMA starts transferring data from the specified processor B
DMA data transfer memory location to the specified shared memory.
4 DMA interrupts processor B to signal that the packet transfer has
finished.
5 Processor B informs processor A Using a B-side transmit register, processor B sends a packet
that data is in shared memory. information message to processor A about the arrival of new
packet data stored in shared memory. The message contains the
command, location, and length of packet data.
6 Processor A receives an interrupt. Processor A receives an interrupt (assuming its corresponding
processor A MU-side receive interrupt is enabled). The pending
processing task becomes active and processes packet data from
memory.
7 Processor A reads or processes packet data from shared memory.
Processor A reads data, then
8 Processor A writes the result from packet processing to a separate
writes data.
buffer.
9 Processor A informs processor B After the processing of the packet data finishes, processor A
that the transfer is finished. informs processor B (using MU processor A-side transmit register,
MUA_TR n ).
10 Processor A sends an interrupt to Processor B receives the next interrupt from processor A, in which
processor B (a request for more processor A requests more packet data.
data).

#### 39.6.5 Freeing a processor from deadlock

During normal operation, one processor may determine that the other processor is not working or is deadlocked. Using Status
(SR) , the processor can use the methods in the following table to identify and correct the problem.
Table 207. Freeing a processor from deadlock
Processor mode Technique Description
— Processor issues an interrupt.
The other processor can interrupt the processor by
issuing one of the 9 (general purpose, receive, or
transmit) interrupts.
— Processor issues a hardware reset. If the above methods are not helpful, the processor
can issue a hardware reset strobe to the other
processor.

---

*Page 1005*

Messaging Unit (MU)

#### 39.7 Register definition

MU provides transmit and receive data registers ( x TR0– n , x RR0– n ) for communication between processor A and processor B.
It also provides control registers ( x CR) for operations such as interrupts and resets, and status registers ( x SR) for checking the
status of the other MU-side. Figure 146 shows the schematic for MU registers.
Processor Other
processor
MU
xCR xSR
xSR xCR
xRR0-n xTR0-n
xTR0-n xRR0-n
Figure 146. MU registers

#### 39.7.1 MU register descriptions

This section contains the detailed register descriptions for MUA registers.
NOTE
A module transfer error to processor A or processor B is generated when:
• A read or write access is made to an invalid location.
• A write operation is performed on a read-only register on the processor A side or processor B side of MU.
39.7.1.1 MU memory map
MU_1.MUA base address: 404E_C000h
Offset Register Access Reset value
Width
(In bits)
0h Version ID (VER) 32 R 0300_000Fh
Table continues on the next page...

---

*Page 1006*

Messaging Unit (MU)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
4h Parameter (PAR) 32 R 0301_0404h
8h Control (CR) 32 RW 0000_0000h
Ch Status (SR) 32 RW See section
10h Core Control 0 (CCR0) 32 R 0000_0000h
100h Flag Control (FCR) 32 RW 0000_0000h
104h Flag Status (FSR) 32 R 0000_0000h
110h General-Purpose Interrupt Enable (GIER) 32 RW 0000_0000h
114h General-Purpose Control (GCR) 32 RW 0000_0000h
118h General-purpose Status (GSR) 32 RW 0000_0000h
120h Transmit Control (TCR) 32 RW 0000_0000h
124h Transmit Status (TSR) 32 R 0000_000Fh
128h Receive Control (RCR) 32 RW 0000_0000h
12Ch Receive Status (RSR) 32 R 0000_0000h
200h - 20Ch Transmit (TR0 - TR3) 32 RW 0000_0000h
280h - 28Ch Receive (RR0 - RR3) 32 R 0000_0000h
39.7.1.2 Version ID (VER)
Offset
Register Offset
VER 0h
Function
Determines the version ID and feature set number of MUA.

---

*Page 1007*

Messaging Unit (MU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R MAJOR MINOR
W
Reset 0 0 0 0 0 0 1 1 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R FEATURE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 1 1 1 1
Fields
Field Function
Major Version Number
31-24
MAJOR
Minor Version Number
23-16
MINOR
15-0 Feature Set Number
FEATURE Indicates the feature set number.
MU implements:
• Standard features
• RAIP and RAIE fields
• Expanded number of TR n /RR n registers
39.7.1.3 Parameter (PAR)
Offset
Register Offset
PAR 4h
Function
Defines the number of flags, transmit registers, receive registers, and general-purpose interrupt requests available for MU.

---

*Page 1008*

Messaging Unit (MU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R FLAG_WIDTH GIR_NUM
W
Reset 0 0 0 0 0 0 1 1 0 0 0 0 0 0 0 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RR_NUM TR_NUM
W
Reset 0 0 0 0 0 1 0 0 0 0 0 0 0 1 0 0
Fields
Field Function
31-24 Flag Width
FLAG_WIDTH Specifies the number of flag bits (3) in the Flag Control (FCR) and Flag Status (FSR) registers.
23-16 General-Purpose Interrupt Request Number
GIR_NUM Specifies the number of general-purpose interrupt requests available (1).
15-8 Receive Register Number
RR_NUM Specifies the number of receive registers (4).
7-0 Transmit Register Number
TR_NUM Specifies the number of transmit registers (4).
39.7.1.4 Control (CR)
Offset
Register Offset
CR 8h
Function
Controls MU reset and reset interrupt enable.

---

*Page 1009*

Messaging Unit (MU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
MURIE MUR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-2
—
1 MUA Reset Interrupt Enable
MURIE Enables the processor A-side MU reset interrupt request due to MU reset issued by MUB.
If the value of this field is 1, an MU reset interrupt request is issued to processor A when
MUA_SR[MURIP] = 1.
If the value of this field is 0, MU ignores the value of MURIP and issues no MU reset interrupt request.
Only a system reset can reset this field. CR[MUR] cannot reset this field.
0b - Disable
1b - Enable
0 MU Reset
MUR Resets MU. Writing 1 to this field resets the MUA and MUB sides. All internal states are cleared. It forces
all control and status registers to return to their default values (except in MUA/B_CCR0 registers; MURIE
in MUA/B_CR registers; MURIP and MURS in MUA/B_SR registers ) .
Before writing 1 to this field, interrupt processor B because writing 1 to this field may affect the ongoing
processor B program.
After writing 1 to this field, monitor the value of MUA_SR[MURS] to know when the reset sequence on the
processor B-side has ended.
This field always reads 0, and it becomes 0 during the MU reset sequence.
0b - Idle
1b - Reset

---

*Page 1010*

Messaging Unit (MU)
39.7.1.5 Status (SR)
Offset
Register Offset
SR Ch
Function
Shows the status of MU resets and the status of pending events and requests.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 RFP TEP GIRP FUP EP MURIP MURS
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 u
Fields
Field Function
Reserved
31-8
—
Reserved
7
—
6 MUA Receive Full Pending
RFP Indicates whether a receive full message is pending.
This field becomes 1 when MUB writes to a TR n register to send data to MUA. After this field becomes 1,
MU checks RSR[RF n ] to determine whether the data in the Receive register is ready for MUA to read it.
This field becomes 0 when all MUA RR n registers are read, or when MU is reset.
0b - Not pending; MUB is not writing to a Transmit register
1b - Pending; MUB is writing to a Transmit register
5 MUA Transmit Empty Pending
TEP Indicates whether a transmit empty message is pending.
Table continues on the next page...

---

*Page 1011*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
This field becomes 1 when any TCR[TIE n ] field is 1 and TSR[TE n ] flag is set. After this field becomes 1, MU
checks TSR[TE n ] to determine whether the data in the Transmit (TR n ) register is ready for MUA to write to it.
This field becomes 0 when write operations to all MUA Transmit (TR n ) registers where TCR[TIE n ] = 1
(transfer interrupt enabled) are completed, or when MU is reset.
0b - No MUA transmit empty event pending
1b - Pending; any TCR[TIEn] field is 1 and TSR[TEn] flag is set
4 MUA General-Purpose Interrupt Pending
GIRP Indicates that MUB has sent a general-purpose interrupt request.
This field becomes 1 when the MUB side sends a general-purpose interrupt request to the MUA side.
GSR[ GIP0 ] identifies which general-purpose interrupt request is received.
This field becomes 0 when MUA_GSR[GIP0] is cleared, or when MU is reset.
0b - No request sent
1b - Request sent
3 MUA Flag Update Pending
FUP Indicates whether a flag update request is pending. MU generates this request when there is a change to
the F n [2:0] bits of MUA_FCR.
This field becomes 1 when the MUA side sends a flag update request to the MUB side.
This field becomes 0 when MU acknowledges this flag update request internally (the flag is updated) from
the MUB side, or during MU reset.
No flag update changes are allowed when this field is 1. When FUP = 1, a write to the F n [2:0] bits of
MUA_FCR does not generate a flag update event. The F n [2:0] bits do not change.
If SR[EP] = 1 (event pending), writing to MUA_FCR does not immediately cause this field to become 1.
0b - No pending update flags (initiated by MUA)
1b - Pending update flags (initiated by MUA)
2 MUA Side Event Pending
EP Indicates a pending side event when the MUA side sends an event update request to the MUB side. An
event is any hardware message that the Status register on the MUB side reflects. For example, an event
occurs when Transmit register 0 is the target of a write operation. During normal operations, the update
mechanism for this field operates automatically.
MU clears this field automatically when the event update acknowledgment is received, or when MU resets.
To ensure that events are posted to MUB, verify that this field is 0. If it is 1, wait and continue to poll this field
until it becomes 0.
0b - Not pending
1b - Pending
1 MU Reset Interrupt Pending Flag
Table continues on the next page...

---

*Page 1012*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
MURIP Indicates whether processor B has issued an MU reset.
This flag is set after processor B initiates an MU reset by setting MUB_CR[MUR]. If CR[MURIE] = 1, the
processor A MU reset interrupt request is issued when processor B writes 1 to MUB_CR[MUR].
Clearing this flag also clears the MU reset interrupt request.
Only a system reset can reset this flag. MU reset cannot reset this flag.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Reset not issued
1b - Reset issued
When writing
0b - No effect
1b - Clear the flag
0 MUA and MUB Reset State
MURS Indicates the reset state of MUA and MUB.
This field becomes 1 during any system reset or MU reset from the MUA or MUB side.
This field becomes 0 when the reset sequence on both MUA and MUB sides ends. After issuing any of the
aforementioned reset events, verify that this field is 0 before starting any access.
0b - Out of reset
1b - In reset
39.7.1.6 Core Control 0 (CCR0)
Offset
Register Offset
CCR0 10h
Function
Allows MUA to control the processor on the MUB side.

---

*Page 1013*

Messaging Unit (MU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
Reserv Reserv
R 0 0 0 0 0
ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-7
—
Reserved
6-5
—
Reserved
4
—
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
39.7.1.7 Flag Control (FCR)
Offset
Register Offset
FCR 100h

---

*Page 1014*

Messaging Unit (MU)
Function
Configures MUB_FSR[F n ] flags.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
F2 F1 F0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-3
—
2-0 MUA to MUB Flag
Fn Configures MUB_FSR[F n ] flags, where n = 0 to 2.
F n configures the corresponding MUB_FSR[F n ] flag.
F n becomes 0 when MU resets.
0b - Clear MUB_FSR[Fn]
1b - Set MUB_FSR[Fn]
39.7.1.8 Flag Status (FSR)
Offset
Register Offset
FSR 104h
Function
Contains flags configured by the values written to MUB_FCR[F n ].

---

*Page 1015*

Messaging Unit (MU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 F2 F1 F0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-3
—
2-0 MUB to MUA-Side Flag
Fn Contains flags configured by the values written to MUB_FCR[F n ], where n = 0 to 2.
F n is the MUA-side flag configured by the values written to MUB_FCR[F n ].
When MUB_FCR[F n ] is written to, the write event updates MUA_FSR[F n ], after the event update latency.
0b - MUB_FCR[Fn] = 0
1b - MUB_FCR[Fn] = 1
39.7.1.9 General-Purpose Interrupt Enable (GIER)
Offset
Register Offset
GIER 110h
Function
Contains the MUA general-purpose interrupt enable fields.

---

*Page 1016*

Messaging Unit (MU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
GIE0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-1
—
0 MUA General-purpose Interrupt Enable
GIEn Enables general-purpose interrupt. There is one general-purpose interrupt ( n = 0).
When GIE0 = 1, a general-purpose interrupt n request is issued to processor A when MUA GSR[GIP0] = 1.
If GIE0 = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE0 becomes 0 when MU resets.
0b - Disable
1b - Enable
39.7.1.10 General-Purpose Control (GCR)
Offset
Register Offset
GCR 114h
Function
Contains the MUA general-purpose interrupt request fields.

---

*Page 1017*

Messaging Unit (MU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
GIR0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-1
—
0 MUA General-Purpose Interrupt Request
GIRn Specifies whether general-purpose interrupts are requested to MUB. There is one general-purpose interrupt
( n = 0).
Writing 1 to GIR0 sets MUB_GSR[ GIP0 ]. If MUB_GIER[ GIE0 ] = 1, a general-purpose interrupt request is
triggered on processor B.
This field becomes 0 when MUB_GSR[ GIP0 ] is cleared. This clearing informs MUA that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR0 is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
0b - Not requested
1b - Requested
39.7.1.11 General-purpose Status (GSR)
Offset
Register Offset
GSR 118h
Function
Contains the status of the MUA general-purpose interrupt pending requests.

---

*Page 1018*

Messaging Unit (MU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 GIP0
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-1
—
0 MUA General-Purpose Interrupt Request Pending
GIPn Indicates whether a general-purpose interrupt request is pending. There is one general-purpose interrupt
( n = 0).
GIP0 informs MUA that MUB_GCR[ GIR0 ] changed from 0 to 1. If MUA_GIER[ GIE0 ] = 1, a general-purpose
interrupt request is issued to processor A.
GIP0 is cleared when MU resets.
After GIP0 is cleared, if MUA_GIER[ GIE0 ] = 1, the general-purpose interrupt request is cleared on the
MUA side.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
39.7.1.12 Transmit Control (TCR)
Offset
Register Offset
TCR 120h

---

*Page 1019*

Messaging Unit (MU)
Function
Contains the MUA transmit interrupt enable fields.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
TIE3 TIE2 TIE1 TIE0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-4
—
3-0 MUA Transmit Interrupt Enable
TIEn Enables MUA transmit interrupt n , where n = 0 to 3.
If this field is 1, an MUA transmit interrupt n request is issued when MUA_TSR[TE n ] is set.
If this field is 0, MU ignores the value of MUA_TSR[TE n ], and no MUA transmit interrupt n request is issued.
0b - Disable
1b - Enable
39.7.1.13 Transmit Status (TSR)
Offset
Register Offset
TSR 124h
Function
Indicates whether the MUA transmit registers are empty.

---

*Page 1020*

Messaging Unit (MU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 TE3 TE2 TE1 TE0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 1 1 1 1
Fields
Field Function
Reserved
31-4
—
3-0 MUA Transmit Empty
TEn Indicates whether MUA Transmit (TR n ) register is empty, where n = 0 to 3.
This field becomes 1 after the MUB_RR n register is read on the MUB side. When TE n = 1, it indicates to
the MUA side that the MUA_TR n register is ready to be written on the MUA side. If MUA_TCR[TIE n ] = 1, a
transmit n interrupt is issued on the MUA side.
This field becomes 0 after the MUA_TR n register is written to on the MUA side. After this field becomes 0,
if MUA_TCR[TIE n ] = 1, the transmit n interrupt request is cleared on the MUA side.
This field becomes 1 when MU resets.
0b - Not empty
1b - Empty
39.7.1.14 Receive Control (RCR)
Offset
Register Offset
RCR 128h
Function
Contains the MUA receive interrupt enables.

---

*Page 1021*

Messaging Unit (MU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
RIE3 RIE2 RIE1 RIE0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-4
—
3-0 MUA Receive Interrupt Enable
RIEn Enables MUA receive interrupt n , where n = 0 to 3.
If this field is 1, an MUA receive interrupt n request is issued when MUA_RSR[RF n ] is set.
If this field is 0, MU ignores the value of MUA_RSR[RF n ], and no MUA receive interrupt request is issued.
0b - Disable
1b - Enable
39.7.1.15 Receive Status (RSR)
Offset
Register Offset
RSR 12Ch
Function
Indicates whether the MUA receive registers are full.

---

*Page 1022*

Messaging Unit (MU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 RF3 RF2 RF1 RF0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-4
—
3-0 MUA Receive Register Full
RFn Indicates whether MUA Receive register (RR n ) is full, where n = 0 to 3.
RF n becomes 1 when the MUB_TR n register is written to on the MUB side.
When RF n is 1, it indicates to the MUA side that new data in the MUA_RR n register is ready for MUA to read
it. If MUA_RCR[RIE n ] = 1, a receive n interrupt is issued on the MUA side.
RF n becomes 0 when the MUA_RR n register is read, or when MU is reset.
After RF n becomes 0, if MUA_RCR[RIE n ] = 1, the receive n interrupt request is cleared on the MUA side.
0b - Not full
1b - Full
39.7.1.16 Transmit (TR0 - TR3)
Offset
Register Offset
TR0 200h
TR1 204h
TR2 208h
TR3 20Ch
Function
Contains MUA transmit data.

---

*Page 1023*

Messaging Unit (MU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W TR_DATA
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W TR_DATA
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 MUA Transmit Data
TR_DATA Contains MUA transmit data. MUB_RR n reflects the data written to this register.
The TR n and RR n registers are not double-buffered. Writing to MUA_TR n overrides the data readable in the
MUA_RR n register.
A write to the Transmit register clears MUA_TSR[TE n ] on the transmitter side, and sets MUB_RSR[RF n ] on
the receiver side.
You can write to this register only when MUA_TSR[TE n ] = 1.
Reading this register returns all zeroes.
39.7.1.17 Receive (RR0 - RR3)
Offset
Register Offset
RR0 280h
RR1 284h
RR2 288h
RR3 28Ch
Function
Contains MUA receive data.

---

*Page 1024*

Messaging Unit (MU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RR_DATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RR_DATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 MUA Receive Data
RR_DATA Reflects the data written to MUB TR n .
Reading this register clears MUA_RSR[RF n ] on the receiver side, and sets MUB_TSR[TE n ] on the
transmitter side.
You can read this register only when MUA_RSR[RF n ] = 1. Reading it before MUA_RSR[RF n ] becomes 1
may result in reading incorrect data. Poll MUA_RSR[RF n ] to confirm it is set before reading RR n .
Writing to this register generates an error response to MUA.

#### 39.7.2 MU register descriptions

This section contains the detailed register descriptions for MUB registers.
NOTE
A module transfer error to processor A or processor B is generated when:
• A read or write access is made to an invalid location.
• A write operation is performed on a read-only register on the processor A side or processor B side of MU.
39.7.2.1 MU memory map
MU_0.MUB base address: 404E_C000h
MU_1.MUB base address: 4038_C000h
Offset Register Access Reset value
Width
(In bits)
0h Version ID (VER) 32 R 0300_000Fh
4h Parameter (PAR) 32 R See section
8h Control (CR) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 1025*

Messaging Unit (MU)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
Ch Status (SR) 32 RW See section
10h Core Control 0 (CCR0) 32 R 0000_0000h
100h Flag Control (FCR) 32 RW 0000_0000h
104h Flag Status (FSR) 32 R 0000_0000h
110h General-Purpose Interrupt Enable (GIER) 32 RW 0000_0000h
114h General-Purpose Control (GCR) 32 RW 0000_0000h
118h General-purpose Status (GSR) 32 RW 0000_0000h
120h Transmit Control (TCR) 32 RW 0000_0000h
124h Transmit Status (TSR) 32 R 0000_000Fh
128h Receive Control (RCR) 32 RW 0000_0000h
12Ch Receive Status (RSR) 32 R 0000_0000h
200h - 20Ch Transmit (TR0 - TR3) 32 RW 0000_0000h
280h - 28Ch Receive (RR0 - RR3) 32 R 0000_0000h
39.7.2.2 Version ID (VER)
Offset
Register Offset
VER 0h
Function
Determines the version ID and feature set number of MUB.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R MAJOR MINOR
W
Reset 0 0 0 0 0 0 1 1 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R FEATURE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 1 1 1 1

---

*Page 1026*

Messaging Unit (MU)
Fields
Field Function
Major Version Number
31-24
MAJOR
Minor Version Number
23-16
MINOR
15-0 Feature Set Number
FEATURE Indicates the feature set number.
MU implements:
• Standard features
• RAIP and RAIE fields
• Expanded number of TR n /RR n registers
39.7.2.3 Parameter (PAR)
Offset
Register Offset
PAR 4h
Function
Defines the number of flags, transmit registers, receive registers, and general-purpose interrupt requests available for MU.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R FLAG_WIDTH GIR_NUM
W
Reset See Register reset values .
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RR_NUM TR_NUM
W
Reset See Register reset values .

---

*Page 1027*

Messaging Unit (MU)
Register reset values
Register Reset value
PAR
MU_0.MUB: 2020_0404h
MU_1.MUB: 0301_0404h
Fields
Field Function
31-24 Flag Width
FLAG_WIDTH Specifies the number of flag bits (32) in the Flag Control (FCR) and Flag Status (FSR) registers.
23-16 General-Purpose Interrupt Request Number
GIR_NUM Specifies the number of general-purpose interrupt requests available (32).
15-8 Receive Register Number
RR_NUM Specifies the number of receive registers (4).
7-0 Transmit Register Number
TR_NUM Specifies the number of transmit registers (4).
39.7.2.4 Control (CR)
Offset
Register Offset
CR 8h
Function
Controls MU reset and reset interrupt enable.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
MURIE MUR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1028*

Messaging Unit (MU)
Fields
Field Function
Reserved
31-2
—
1 MUB Reset Interrupt Enable
MURIE Enables the processor B-side MU reset interrupt request due to MU reset issued by MUA.
If the value of this field is 1, an MU reset interrupt request is issued to processor B when
MUB_SR[MURIP] = 1.
If the value of this field is 0, MU ignores the value of MURIP and issues no MU reset interrupt request.
Only a system reset can reset this field. CR[MUR] cannot reset this field.
0b - Disable
1b - Enable
0 MU Reset
MUR Resets MU. Writing 1 to this field resets the MUB and MUA sides. All internal states are cleared. It forces
all control and status registers to return to their default values (except in MUB/A_CCR0 registers; MURIE
in MUB/A_CR registers; MURIP and MURS in MUB/A_SR registers ) .
Before writing 1 to this field, interrupt processor A because writing 1 to this field may affect the ongoing
processor A program.
After writing 1 to this field, monitor the value of MUB_SR[MURS] to know when the reset sequence on the
processor A-side has ended.
This field always reads 0, and it becomes 0 during the MU reset sequence.
0b - Idle
1b - Reset
39.7.2.5 Status (SR)
Offset
Register Offset
SR Ch
Function
Shows the status of MU resets and the status of pending events and requests.

---

*Page 1029*

Messaging Unit (MU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 RFP TEP GIRP FUP EP MURIP MURS
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 u
Fields
Field Function
Reserved
31-8
—
Reserved
7
—
6 MUB Receive Full Pending
RFP Indicates whether a receive full message is pending.
This field becomes 1 when MUA writes to a TR n register to send data to MUB. After this field becomes 1,
MU checks RSR[RF n ] to determine whether the data in the Receive register is ready for MUB to read it.
This field becomes 0 when all MUB RR n registers are read, or when MU is reset.
0b - Not pending; MUA is not writing to a Transmit register
1b - Pending; MUA is writing to a Transmit register
5 MUB Transmit Empty Pending
TEP Indicates whether a transmit empty message is pending.
This field becomes 1 when any TCR[TIE n ] field is 1 and TSR[TE n ] flag is set. After this field becomes 1, MU
checks TSR[TE n ] to determine whether the data in the Transmit (TR n ) register is ready for MUB to write to it.
This field becomes 0 when write operations to all MUB Transmit (TR n ) registers where TCR[TIE n ] = 1
(transfer interrupt enabled) are completed, or when MU is reset.
0b - No MUB transmit empty event pending
1b - Pending; any TCR[TIEn] field is 1 and TSR[TEn] flag is set
4 MUB General-Purpose Interrupt Pending
GIRP Indicates that MUA has sent a general-purpose interrupt request.
This field becomes 1 when the MUA side sends a general-purpose interrupt request to the MUB side.
GSR[ GIP n ] identifies which general-purpose interrupt request is received.
Table continues on the next page...

---

*Page 1030*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
This field becomes 0 when all MUB_GSR[GIP n ] fields are cleared, or when MU is reset.
0b - No request sent
1b - Request sent
3 MUB Flag Update Pending
FUP Indicates whether a flag update request is pending. MU generates this request when there is a change to
the F n [31:0] bits of MUB_FCR.
This field becomes 1 when the MUB side sends a flag update request to the MUA side.
This field becomes 0 when MU acknowledges this flag update request internally (the flag is updated) from
the MUA side, or during MU reset.
No flag update changes are allowed when this field is 1. When FUP = 1, a write to the F n [31:0] bits of
MUB_FCR does not generate a flag update event. The F n [31:0] bits do not change.
If SR[EP] = 1 (event pending), writing to MUB_FCR does not immediately cause this field to become 1.
0b - No pending update flags (initiated by MUB)
1b - Pending update flags (initiated by MUB)
2 MUB Side Event Pending
EP Indicates a pending side event when the MUB side sends an event update request to the MUA side. An
event is any hardware message that the Status register on the MUA side reflects. For example, an event
occurs when Transmit register 0 is the target of a write operation. During normal operations, the update
mechanism for this field operates automatically.
MU clears this field automatically when the event update acknowledgment is received, or when MU resets.
To ensure that events are posted to MUA, verify that this field is 0. If it is 1, wait and continue to poll this field
until it becomes 0.
0b - Not pending
1b - Pending
1 MU Reset Interrupt Pending Flag
MURIP Indicates whether processor A has issued an MU reset.
This flag is set after processor A initiates an MU reset by setting MUB_CR[MUR]. If CR[MURIE] = 1, the
processor B MU reset interrupt request is issued when processor A writes 1 to MUA_CR[MUR].
Clearing this flag also clears the MU reset interrupt request.
Only a system reset can reset this flag. MU reset cannot reset this flag.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Reset not issued
Table continues on the next page...

---

*Page 1031*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
1b - Reset issued
When writing
0b - No effect
1b - Clear the flag
0 MUA and MUB Reset State
MURS Indicates the reset state of MUA and MUB.
This field becomes 1 during any system reset or MU reset from the MUA or MUB side.
This field becomes 0 when the reset sequence on both MUA and MUB sides ends. After issuing any of the
aforementioned reset events, verify that this field is 0 before starting any access.
0b - Out of reset
1b - In reset
39.7.2.6 Core Control 0 (CCR0)
Offset
Register Offset
CCR0 10h
Function
Allows MUB to control the processor on the MUA side.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
Reserv Reserv
R 0 0 0 0 0
ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1032*

Messaging Unit (MU)
Fields
Field Function
Reserved
31-7
—
Reserved
6-5
—
Reserved
4
—
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
39.7.2.7 Flag Control (FCR)
Offset
Register Offset
FCR 100h
Function
Configures MUA_FSR[F n ] flags.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
F31 F30 F29 F28 F27 F26 F25 F24 F23 F22 F21 F20 F19 F18 F17 F16
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
F15 F14 F13 F12 F11 F10 F9 F8 F7 F6 F5 F4 F3 F2 F1 F0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1033*

Messaging Unit (MU)
Fields
Field Function
31 MUB to MUA Flag
F31 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
30 MUB to MUA Flag
F30 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
29 MUB to MUA Flag
F29 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.

---

*Page 1034*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
28 MUB to MUA Flag
F28 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
27 MUB to MUA Flag
F27 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1035*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
26 MUB to MUA Flag
F26 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
25 MUB to MUA Flag
F25 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
Table continues on the next page...

---

*Page 1036*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
24 MUB to MUA Flag
F24 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
23 MUB to MUA Flag
F23 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
22 MUB to MUA Flag
Table continues on the next page...

---

*Page 1037*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
F22 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
21 MUB to MUA Flag
F21 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
20 MUB to MUA Flag
F20 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
Table continues on the next page...

---

*Page 1038*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
19 MUB to MUA Flag
F19 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
18 MUB to MUA Flag
F18 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1039*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
17 MUB to MUA Flag
F17 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
16 MUB to MUA Flag
F16 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
Table continues on the next page...

---

*Page 1040*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
15 MUB to MUA Flag
F15 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
14 MUB to MUA Flag
F14 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
13 MUB to MUA Flag
Table continues on the next page...

---

*Page 1041*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
F13 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
12 MUB to MUA Flag
F12 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
11 MUB to MUA Flag
F11 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
Table continues on the next page...

---

*Page 1042*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
10 MUB to MUA Flag
F10 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
9 MUB to MUA Flag
F9 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1043*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
8 MUB to MUA Flag
F8 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
7 MUB to MUA Flag
F7 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
Table continues on the next page...

---

*Page 1044*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
6 MUB to MUA Flag
F6 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
5 MUB to MUA Flag
F5 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
4 MUB to MUA Flag
Table continues on the next page...

---

*Page 1045*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
F4 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
3 MUB to MUA Flag
F3 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FCR —
MU_1.MUB — FCR
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
2 MUB to MUA Flag
F2 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
Table continues on the next page...

---

*Page 1046*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
1 MUB to MUA Flag
F1 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
0 MUB to MUA Flag
F0 Configures MUA_FSR[F n ] flags, where n = 0 to 31.
F n configures the corresponding MUA_FSR[F n ] flag.
F n becomes 0 when MU resets.
0b - Clear MUA_FSR[Fn]
1b - Set MUA_FSR[Fn]
39.7.2.8 Flag Status (FSR)
Offset
Register Offset
FSR 104h
Function
Contains flags configured by the values written to MUA_FCR[F n ].
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R F31 F30 F29 F28 F27 F26 F25 F24 F23 F22 F21 F20 F19 F18 F17 F16
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R F15 F14 F13 F12 F11 F10 F9 F8 F7 F6 F5 F4 F3 F2 F1 F0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1047*

Messaging Unit (MU)
Fields
Field Function
31 MUA to MUB-Side Flag
F31 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
30 MUA to MUB-Side Flag
F30 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
29 MUA to MUB-Side Flag
F29 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.

---

*Page 1048*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
28 MUA to MUB-Side Flag
F28 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
27 MUA to MUB-Side Flag
F27 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1049*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
26 MUA to MUB-Side Flag
F26 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
25 MUA to MUB-Side Flag
F25 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
Table continues on the next page...

---

*Page 1050*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
24 MUA to MUB-Side Flag
F24 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
23 MUA to MUB-Side Flag
F23 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
22 MUA to MUB-Side Flag
Table continues on the next page...

---

*Page 1051*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
F22 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
21 MUA to MUB-Side Flag
F21 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
20 MUA to MUB-Side Flag
F20 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
Table continues on the next page...

---

*Page 1052*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
19 MUA to MUB-Side Flag
F19 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
18 MUA to MUB-Side Flag
F18 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1053*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
17 MUA to MUB-Side Flag
F17 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
16 MUA to MUB-Side Flag
F16 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
Table continues on the next page...

---

*Page 1054*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
15 MUA to MUB-Side Flag
F15 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
14 MUA to MUB-Side Flag
F14 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
13 MUA to MUB-Side Flag
Table continues on the next page...

---

*Page 1055*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
F13 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
12 MUA to MUB-Side Flag
F12 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
11 MUA to MUB-Side Flag
F11 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
Table continues on the next page...

---

*Page 1056*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
10 MUA to MUB-Side Flag
F10 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
9 MUA to MUB-Side Flag
F9 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1057*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
8 MUA to MUB-Side Flag
F8 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
7 MUA to MUB-Side Flag
F7 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
Table continues on the next page...

---

*Page 1058*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
6 MUA to MUB-Side Flag
F6 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
5 MUA to MUB-Side Flag
F5 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
4 MUA to MUB-Side Flag
Table continues on the next page...

---

*Page 1059*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
F4 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
3 MUA to MUB-Side Flag
F3 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB FSR —
MU_1.MUB — FSR
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
2 MUA to MUB-Side Flag
F2 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
Table continues on the next page...

---

*Page 1060*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
1 MUA to MUB-Side Flag
F1 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
0 MUA to MUB-Side Flag
F0 Contains flags configured by the values written to MUA_FCR[F n ], where n = 0 to 31.
F n is the MUB-side flag configured by the values written to MUA_FCR[F n ].
When MUA_FCR[F n ] is written to, the write event updates MUB_FSR[F n ], after the event update latency.
0b - MUA_FCR[Fn] = 0
1b - MUA_FCR[Fn] = 1
39.7.2.9 General-Purpose Interrupt Enable (GIER)
Offset
Register Offset
GIER 110h
Function
Contains the MUB general-purpose interrupt enable fields.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
GIE31 GIE30 GIE29 GIE28 GIE27 GIE26 GIE25 GIE24 GIE23 GIE22 GIE21 GIE20 GIE19 GIE18 GIE17 GIE16
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
GIE15 GIE14 GIE13 GIE12 GIE11 GIE10 GIE9 GIE8 GIE7 GIE6 GIE5 GIE4 GIE3 GIE2 GIE1 GIE0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1061*

Messaging Unit (MU)
Fields
Field Function
31 MUB General-purpose Interrupt Enable
GIE31 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
30 MUB General-purpose Interrupt Enable
GIE30 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable

---

*Page 1062*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
29 MUB General-purpose Interrupt Enable
GIE29 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
28 MUB General-purpose Interrupt Enable
GIE28 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
27 MUB General-purpose Interrupt Enable
GIE27 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
Table continues on the next page...

---

*Page 1063*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
26 MUB General-purpose Interrupt Enable
GIE26 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
25 MUB General-purpose Interrupt Enable
GIE25 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
Table continues on the next page...

---

*Page 1064*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
24 MUB General-purpose Interrupt Enable
GIE24 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
23 MUB General-purpose Interrupt Enable
GIE23 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1065*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
22 MUB General-purpose Interrupt Enable
GIE22 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
21 MUB General-purpose Interrupt Enable
GIE21 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1066*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
20 MUB General-purpose Interrupt Enable
GIE20 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
19 MUB General-purpose Interrupt Enable
GIE19 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1067*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
18 MUB General-purpose Interrupt Enable
GIE18 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
17 MUB General-purpose Interrupt Enable
GIE17 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1068*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
16 MUB General-purpose Interrupt Enable
GIE16 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
15 MUB General-purpose Interrupt Enable
GIE15 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1069*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
14 MUB General-purpose Interrupt Enable
GIE14 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
13 MUB General-purpose Interrupt Enable
GIE13 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1070*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
12 MUB General-purpose Interrupt Enable
GIE12 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
11 MUB General-purpose Interrupt Enable
GIE11 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1071*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
10 MUB General-purpose Interrupt Enable
GIE10 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
9 MUB General-purpose Interrupt Enable
GIE9 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1072*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
8 MUB General-purpose Interrupt Enable
GIE8 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
7 MUB General-purpose Interrupt Enable
GIE7 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1073*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
6 MUB General-purpose Interrupt Enable
GIE6 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
5 MUB General-purpose Interrupt Enable
GIE5 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1074*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
4 MUB General-purpose Interrupt Enable
GIE4 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
3 MUB General-purpose Interrupt Enable
GIE3 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1075*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
2 MUB General-purpose Interrupt Enable
GIE2 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
1 MUB General-purpose Interrupt Enable
GIE1 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1076*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GIER —
MU_1.MUB — GIER
0b - Disable
1b - Enable
0 MUB General-purpose Interrupt Enable
GIE0 Enables general-purpose interrupt. There are 32 general-purpose interrupts ( n = 0 to 31).
When GIE n = 1, a general-purpose interrupt n request is issued to processor B when MUB GSR[GIP n ] = 1.
If GIE n = 0, the general-purpose interrupt request pending does not trigger the general-purpose interrupt.
GIE n becomes 0 when MU resets.
0b - Disable
1b - Enable
39.7.2.10 General-Purpose Control (GCR)
Offset
Register Offset
GCR 114h
Function
Contains the MUB general-purpose interrupt request fields.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
GIR31 GIR30 GIR29 GIR28 GIR27 GIR26 GIR25 GIR24 GIR23 GIR22 GIR21 GIR20 GIR19 GIR18 GIR17 GIR16
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
GIR15 GIR14 GIR13 GIR12 GIR11 GIR10 GIR9 GIR8 GIR7 GIR6 GIR5 GIR4 GIR3 GIR2 GIR1 GIR0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1077*

Messaging Unit (MU)
Fields
Field Function
31 MUB General-Purpose Interrupt Request
GIR31 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
30 MUB General-Purpose Interrupt Request
GIR30 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR

---

*Page 1078*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
0b - Not requested
1b - Requested
29 MUB General-Purpose Interrupt Request
GIR29 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
28 MUB General-Purpose Interrupt Request
GIR28 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1079*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
27 MUB General-Purpose Interrupt Request
GIR27 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
26 MUB General-Purpose Interrupt Request
GIR26 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
Table continues on the next page...

---

*Page 1080*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
25 MUB General-Purpose Interrupt Request
GIR25 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
24 MUB General-Purpose Interrupt Request
GIR24 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
Table continues on the next page...

---

*Page 1081*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
23 MUB General-Purpose Interrupt Request
GIR23 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
22 MUB General-Purpose Interrupt Request
Table continues on the next page...

---

*Page 1082*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
GIR22 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
21 MUB General-Purpose Interrupt Request
GIR21 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
Table continues on the next page...

---

*Page 1083*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
0b - Not requested
1b - Requested
20 MUB General-Purpose Interrupt Request
GIR20 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
19 MUB General-Purpose Interrupt Request
GIR19 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1084*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
18 MUB General-Purpose Interrupt Request
GIR18 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
17 MUB General-Purpose Interrupt Request
GIR17 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
Table continues on the next page...

---

*Page 1085*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
16 MUB General-Purpose Interrupt Request
GIR16 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
15 MUB General-Purpose Interrupt Request
GIR15 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
Table continues on the next page...

---

*Page 1086*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
14 MUB General-Purpose Interrupt Request
GIR14 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
13 MUB General-Purpose Interrupt Request
Table continues on the next page...

---

*Page 1087*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
GIR13 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
12 MUB General-Purpose Interrupt Request
GIR12 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
Table continues on the next page...

---

*Page 1088*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
0b - Not requested
1b - Requested
11 MUB General-Purpose Interrupt Request
GIR11 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
10 MUB General-Purpose Interrupt Request
GIR10 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1089*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
9 MUB General-Purpose Interrupt Request
GIR9 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
8 MUB General-Purpose Interrupt Request
GIR8 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
Table continues on the next page...

---

*Page 1090*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
7 MUB General-Purpose Interrupt Request
GIR7 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
6 MUB General-Purpose Interrupt Request
GIR6 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
Table continues on the next page...

---

*Page 1091*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
5 MUB General-Purpose Interrupt Request
GIR5 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
4 MUB General-Purpose Interrupt Request
Table continues on the next page...

---

*Page 1092*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
GIR4 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
3 MUB General-Purpose Interrupt Request
GIR3 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
Table continues on the next page...

---

*Page 1093*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
0b - Not requested
1b - Requested
2 MUB General-Purpose Interrupt Request
GIR2 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
1 MUB General-Purpose Interrupt Request
GIR1 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1094*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GCR —
MU_1.MUB — GCR
0b - Not requested
1b - Requested
0 MUB General-Purpose Interrupt Request
GIR0 Specifies whether general-purpose interrupts are requested to MUA. There are 32 general-purpose
interrupts ( n = 0 to 31).
Writing 1 to GIR n sets MUA_GSR[ GIP n ]. If MUA_GIER[ GIE n ] = 1, a general-purpose interrupt request is
triggered on processor A.
This field becomes 0 when MUA_GSR[ GIP n ] is cleared. This clearing informs MUB that the interrupt is
accepted (cleared by software).
To ensure proper operations, verify that GIR n is 0 (no pending interrupt) before writing 1 to it.
This field becomes 0 when MU resets.
0b - Not requested
1b - Requested
39.7.2.11 General-purpose Status (GSR)
Offset
Register Offset
GSR 118h
Function
Contains the status of the MUB general-purpose interrupt pending requests.

---

*Page 1095*

Messaging Unit (MU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R GIP31 GIP30 GIP29 GIP28 GIP27 GIP26 GIP25 GIP24 GIP23 GIP22 GIP21 GIP20 GIP19 GIP18 GIP17 GIP16
W W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R GIP15 GIP14 GIP13 GIP12 GIP11 GIP10 GIP9 GIP8 GIP7 GIP6 GIP5 GIP4 GIP3 GIP2 GIP1 GIP0
W W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 MUB General-Purpose Interrupt Request Pending
GIP31 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
30 MUB General-Purpose Interrupt Request Pending

---

*Page 1096*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
GIP30 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
29 MUB General-Purpose Interrupt Request Pending
GIP29 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1097*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
28 MUB General-Purpose Interrupt Request Pending
GIP28 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
Table continues on the next page...

---

*Page 1098*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
1b - Pending
When writing
0b - No effect
1b - Clear the flag
27 MUB General-Purpose Interrupt Request Pending
GIP27 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
26 MUB General-Purpose Interrupt Request Pending
GIP26 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
Table continues on the next page...

---

*Page 1099*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
25 MUB General-Purpose Interrupt Request Pending
GIP25 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
Table continues on the next page...

---

*Page 1100*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
24 MUB General-Purpose Interrupt Request Pending
GIP24 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
23 MUB General-Purpose Interrupt Request Pending
GIP23
Table continues on the next page...

---

*Page 1101*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
22 MUB General-Purpose Interrupt Request Pending
GIP22 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1102*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
21 MUB General-Purpose Interrupt Request Pending
GIP21 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
Table continues on the next page...

---

*Page 1103*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
1b - Pending
When writing
0b - No effect
1b - Clear the flag
20 MUB General-Purpose Interrupt Request Pending
GIP20 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
19 MUB General-Purpose Interrupt Request Pending
GIP19 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
Table continues on the next page...

---

*Page 1104*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
18 MUB General-Purpose Interrupt Request Pending
GIP18 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
Table continues on the next page...

---

*Page 1105*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
17 MUB General-Purpose Interrupt Request Pending
GIP17 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
16 MUB General-Purpose Interrupt Request Pending
GIP16
Table continues on the next page...

---

*Page 1106*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
15 MUB General-Purpose Interrupt Request Pending
GIP15 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1107*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
14 MUB General-Purpose Interrupt Request Pending
GIP14 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
Table continues on the next page...

---

*Page 1108*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
1b - Pending
When writing
0b - No effect
1b - Clear the flag
13 MUB General-Purpose Interrupt Request Pending
GIP13 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
12 MUB General-Purpose Interrupt Request Pending
GIP12 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
Table continues on the next page...

---

*Page 1109*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
11 MUB General-Purpose Interrupt Request Pending
GIP11 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
Table continues on the next page...

---

*Page 1110*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
10 MUB General-Purpose Interrupt Request Pending
GIP10 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
9 MUB General-Purpose Interrupt Request Pending
GIP9
Table continues on the next page...

---

*Page 1111*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
8 MUB General-Purpose Interrupt Request Pending
GIP8 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1112*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
7 MUB General-Purpose Interrupt Request Pending
GIP7 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
Table continues on the next page...

---

*Page 1113*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
1b - Pending
When writing
0b - No effect
1b - Clear the flag
6 MUB General-Purpose Interrupt Request Pending
GIP6 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
5 MUB General-Purpose Interrupt Request Pending
GIP5 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
Table continues on the next page...

---

*Page 1114*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
4 MUB General-Purpose Interrupt Request Pending
GIP4 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
Table continues on the next page...

---

*Page 1115*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
3 MUB General-Purpose Interrupt Request Pending
GIP3 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
2 MUB General-Purpose Interrupt Request Pending
GIP2
Table continues on the next page...

---

*Page 1116*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
1 MUB General-Purpose Interrupt Request Pending
GIP1 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Table continues on the next page...

---

*Page 1117*

Messaging Unit (MU)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
MU_0.MUB GSR —
MU_1.MUB — GSR
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag
0 MUB General-Purpose Interrupt Request Pending
GIP0 Indicates whether a general-purpose interrupt request is pending. There are 32 general-purpose interrupts
( n = 0 to 31).
GIP n informs MUB that MUA_GCR[ GIR n ] changed from 0 to 1. If MUB_GIER[ GIE n ] = 1, a general-purpose
interrupt request is issued to processor B.
GIP n is cleared when MU resets.
After GIP n is cleared, if MUB_GIER[ GIE n ] = 1, the general-purpose interrupt request is cleared on the
MUB side.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not pending
1b - Pending
When writing
0b - No effect
1b - Clear the flag

---

*Page 1118*

Messaging Unit (MU)
39.7.2.12 Transmit Control (TCR)
Offset
Register Offset
TCR 120h
Function
Contains the MUB transmit interrupt enable fields.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
TIE3 TIE2 TIE1 TIE0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-4
—
3-0 MUB Transmit Interrupt Enable
TIEn Enables MUB transmit interrupt n , where n = 0 to 3.
If this field is 1, an MUB transmit interrupt n request is issued when MUB_TSR[TE n ] is set.
If this field is 0, MU ignores the value of MUB_TSR[TE n ], and no MUB transmit interrupt n request is issued.
0b - Disable
1b - Enable
39.7.2.13 Transmit Status (TSR)
Offset
Register Offset
TSR 124h

---

*Page 1119*

Messaging Unit (MU)
Function
Indicates whether the MUB transmit registers are empty.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 TE3 TE2 TE1 TE0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 1 1 1 1
Fields
Field Function
Reserved
31-4
—
3-0 MUB Transmit Empty
TEn Indicates whether MUB Transmit (TR n ) register is empty, where n = 0 to 3.
This field becomes 1 after the MUA_RR n register is read on the MUA side. When TE n = 1, it indicates to
the MUB side that the MUB_TR n register is ready to be written on the MUB side. If MUB_TCR[TIE n ] = 1, a
transmit n interrupt is issued on the MUB side.
This field becomes 0 after the MUB_TR n register is written to on the MUB side. After this field becomes 0,
if MUB_TCR[TIE n ] = 1, the transmit n interrupt request is cleared on the MUB side.
This field becomes 1 when MU resets.
0b - Not empty
1b - Empty
39.7.2.14 Receive Control (RCR)
Offset
Register Offset
RCR 128h
Function
Contains the MUB receive interrupt enables.

---

*Page 1120*

Messaging Unit (MU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
RIE3 RIE2 RIE1 RIE0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-4
—
3-0 MUB Receive Interrupt Enable
RIEn Enables MUB receive interrupt n , where n = 0 to 3.
If this field is 1, an MUB receive interrupt n request is issued when MUB_RSR[RF n ] is set.
If this field is 0, MU ignores the value of MUB_RSR[RF n ], and no MUB receive interrupt request is issued.
0b - Disable
1b - Enable
39.7.2.15 Receive Status (RSR)
Offset
Register Offset
RSR 12Ch
Function
Indicates whether the MUB receive registers are full.

---

*Page 1121*

Messaging Unit (MU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 RF3 RF2 RF1 RF0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-4
—
3-0 MUB Receive Register Full
RFn Indicates whether MUB Receive register (RR n ) is full, where n = 0 to 3.
RF n becomes 1 when the MUA_TR n register is written to on the MUA side.
When RF n is 1, it indicates to the MUB side that new data in the MUB_RR n register is ready for MUB to read
it. If MUB_RCR[RIE n ] = 1, a receive n interrupt is issued on the MUB side.
RF n becomes 0 when the MUB_RR n register is read, or when MU is reset.
After RF n becomes 0, if MUB_RCR[RIE n ] = 1, the receive n interrupt request is cleared on the MUB side.
0b - Not full
1b - Full
39.7.2.16 Transmit (TR0 - TR3)
Offset
Register Offset
TR0 200h
TR1 204h
TR2 208h
TR3 20Ch
Function
Contains MUB transmit data.

---

*Page 1122*

Messaging Unit (MU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W TR_DATA
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W TR_DATA
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 MUB Transmit Data
TR_DATA Contains MUB transmit data. MUA_RR n reflects the data written to this register.
The TR n and RR n registers are not double-buffered. Writing to MUB_TR n overrides the data readable in the
MUA_RR n register.
A write to the Transmit register clears MUB_TSR[TE n ] on the transmitter side, and sets MUA_RSR[RF n ] on
the receiver side.
You can write to this register only when MUB_TSR[TE n ] = 1.
Reading this register returns all zeroes.
39.7.2.17 Receive (RR0 - RR3)
Offset
Register Offset
RR0 280h
RR1 284h
RR2 288h
RR3 28Ch
Function
Contains MUB receive data.

---

*Page 1123*

Messaging Unit (MU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RR_DATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RR_DATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 MUB Receive Data
RR_DATA Reflects the data written to MUA TR n .
Reading this register clears MUB_RSR[RF n ] on the receiver side, and sets MUA_TSR[TE n ] on the
transmitter side.
You can read this register only when MUB_RSR[RF n ] = 1. Reading it before MUB_RSR[RF n ] becomes 1
may result in reading incorrect data. Poll MUB_RSR[RF n ] to confirm it is set before reading RR n .
Writing to this register generates an error response to MUB.

#### 39.8 Glossary

EP Event Pending
GIR General-purpose Interrupt Request
GIP General-purpose Interrupt Pending
MUR Messaging Unit Reset
RF Receiver Full
RFP Receive Full Pending
TE Transmitter Empty
TEP Transmit Empty Pending
MUA Messaging Unit A
MUB Messaging Unit B

---

*Page 1124*

