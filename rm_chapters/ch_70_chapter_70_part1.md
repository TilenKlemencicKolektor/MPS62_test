<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 70 -->

# Chapter 70

# CAN (FlexCAN)

#### 70.1 Chip-specific FlexCAN information

#### 70.1.1 FlexCAN instances and configuration

This chip contains up to 8 instances of FlexCAN:
• FlexCAN_0
• FlexCAN_1
• FlexCAN_2
• FlexCAN_3
• FlexCAN_4
• FlexCAN_5
Table 417. FlexCAN configuration and instances
Chip Instances Flexible Number of Rx FIFO ID ECC External tick DMA Enhanced
Data Rate message Filtering time RX FIFO
(CAN FD) buffers feature
protocol (assuming (size)
specification 8B payload)
and CAN
protocol.
MCXE31B FlexCAN_0 Yes 96 Yes Yes Yes Yes Yes (20)
(PIT_RTI_2)
FlexCAN_1 Yes 64 Yes Yes Yes Yes No
(PIT_RTI_2)
FlexCAN_2 Yes 64 Yes Yes Yes Yes No
(PIT_RTI_2)
FlexCAN_3 Yes 32 Yes Yes Yes Yes No
(PIT_RTI_2)
FlexCAN_4 Yes 32 Yes Yes Yes Yes No
(PIT_RTI_2)
FlexCAN_5 Yes 32 Yes Yes Yes Yes No
(PIT_RTI_2)
MCXE317 FlexCAN_0 Yes 64 Yes Yes Yes Yes Yes (20)
(PIT_RTI_1)
FlexCAN_1 Yes 64 Yes Yes Yes Yes No
(PIT_RTI_1)
FlexCAN_2 Yes 64 Yes Yes Yes Yes No
(PIT_RTI_1)
FlexCAN_3 Yes 32 Yes Yes Yes Yes No
(PIT_RTI_1)
Table continues on the next page...

---

*Page 2298*

CAN (FlexCAN)
Table 417. FlexCAN configuration and instances (continued)
Chip Instances Flexible Number of Rx FIFO ID ECC External tick DMA Enhanced
Data Rate message Filtering time RX FIFO
(CAN FD) buffers feature
protocol (assuming (size)
specification 8B payload)
and CAN
protocol.
FlexCAN_4 Yes 32 Yes Yes Yes Yes No
(PIT_RTI_1)
FlexCAN_5 Yes 32 Yes Yes Yes Yes No
(PIT_RTI_1)
MCXE315/ FlexCAN_0 Yes 64 Yes Yes Yes Yes Yes (20)
MCXE316 (PIT_RTI_1)
FlexCAN_1 Yes 64 Yes Yes Yes Yes No
(PIT_RTI_1)
FlexCAN_2 Yes 64 Yes Yes Yes Yes No
(PIT_RTI_1)
NOTE
See attached interrupt map file for the vector numbers for the CAN instances and their corresponding
interrupt sources.

#### 70.1.2 FlexCAN error injection

Error injection address mapping
Use the following table to convert from the memory map address to the location in the physical FlexCAN RAM (where pairs of
values are provided, the first is the address for MCR[FDEN] negated, the second is for MCR[FDEN] asserted):
RAM contents Memory map Injection address
— — FlexCAN0 FlexCAN0 FlexCAN1/2 FlexCAN3 FlexCAN4/5
MCXE31B MCXE317 MCXE31 MCXE31B MCXE31B
MCXE315/ MCXE317
MCXE316
MCXE317
FlexCAN Registers — Not mapped Not mapped Not mapped Not mapped Not mapped
MBs 0080h 0000h 0000h 0000h 0000h 0000h
RXIMRs 0880h 600h 400h 400h 200h 200h
RXIFR_0 0A80h 780h 500h 500h 280h 280h
RXIFR_1 0A84h 784h 504h 504h 284h 284h
RXIFR_2 0A88h 788h 508h 508h 288h 288h
Table continues on the next page...

---

*Page 2299*

CAN (FlexCAN)
Table continued from the previous page...
RAM contents Memory map Injection address
RXIFR_3 0A8Ch 78Ch 50Ch 50Ch 28Ch 28Ch
RXIFR_4 0A90h 790h 510h 510h 290h 290h
RXIFR_5 0A94h 794h 514h 514h 294h 294h
Reserved 0A98h — — — — —
RXMGMASK 0AA0h 7A0h 520h 520h 2A0h 2A0h
RXFGMASK 0AA4h 7A4h 524h 524h 2A4h 2A4h
RX14MASK 0AA8h 7A8h 528h 528h 2A8h 2A8h
RX15MASK 0AACh 7ACh 52Ch 52Ch 2ACh 2Ach
Tx_SMB 0AB0h/0F28h 7B0h 530h 530h 2B0h 2B0h
Rx_SMB0 0AC0h/0F70h 7C0h/7F8h 540h/578h 540h/578h 2C0h/2F8h 2C0h/2F8h
Rx_SMB1 0AD0h/0FB8h 7D0h/840h 550h/5C0h 550h/5C0h 2D0h/340h 2D0h/340h
Rx_SMB0_TIME_STA 0C20h 888h 608h 608h 388h 388h
MP
Rx_SMB1_TIME_STA 0C24h 88Ch 60Ch 60Ch 38Ch 38Ch
MP
HR_TIME_STAMP 0C30h 890h 610h 610h 390h 390h
Enhanced RX FIFO 2000h A10h 710h — — —
ERFFEL 3000h 1050h D50h — — —
FlexCAN memory initialization
All FlexCAN memory must be initialized before starting its operation in order to have the parity bits in memory properly updated.
CTRL2[WRMFRZ] grants write access to all memory positions that require initialization.
These locations are as listed in this table from 080h–ADFh and from C20h–31FFh.:
Chip Instance Offset address ranges to be initialized
MCXE31B FlexCAN0 080h–ADFh; C20h–31FFh
FlexCAN1–FlexCAN5 080h–ADFh; C20h–FFFh
MCXE317 FlexCAN0 080h–DFh; C20h–31FFh
FlexCAN1–FlexCAN5 080h–ADFh; C20h–FFFh
MCXE315/MCXE316 FlexCAN0 080h–ADFh; C20h–31FFh
FlexCAN1–FlexCAN2 080h–ADFh; C20h–FFFh
NOTE
The reset value of CAN0_CTRL2 register for MCXE317 is 0080_0000h.

---

*Page 2300*

CAN (FlexCAN)

#### 70.1.3 Reset value of MDIS bit

The CAN_MCR[MDIS] bit is set to 1 when the module is reset. Therefore, FlexCAN module is disabled following a reset. For details
of CAN_MCR[MDIS] register refer to "CAN register description" section.

#### 70.1.4 CAN Timestamp Implementation

FlexCAN uses Ethernet Media Access Controller (EMAC) and System Timer Module (STM_0) counters as time source. For details
see 'FlexCAN timestamp implementation' section in Clocking chapter.

#### 70.1.5 CAN clock setup

For clock setup, see Clocking chapter.

#### 70.2 Overview

FlexCAN is a communication controller implementing the CAN protocol according to the ISO 11898-1:2015 standard and CAN
2.0 Part B protocol specifications.
The CAN protocol was primarily designed to be used as a vehicle serial data bus, meeting the specific real-time processing
and reliable operation requirements in the electromagnetic interference (EMI) environment of a vehicle. FlexCAN is a full
implementation of the CAN protocol specification, the CAN with flexible data rate (CAN FD) protocol, and the CAN version 2.0 Part
B protocol. It supports both standard and extended message frames and long payloads .
NOTE
Legacy Receive (RX) FIFO cannot be used in CAN Flexible Data (FD) mode. In CAN FD mode, use the Enhanced
Receive FIFO feature instead of the Legacy Receive FIFO.

#### 70.2.1 Block diagram

Figure 370 shows the main submodules implemented in FlexCAN.
Peripheral bus interface
Address, data, clocks, and interrupts
Bus interface unit (BIU) Message
buffers
Registers
(MBs)
Controller RAM
host interface (CHI)
TX RX
arbitration matching
Protocol engine (PE)
CAN TX CAN RX FlexCAN
CAN transceiver
CAN bus
Figure 370. Block diagram
The Protocol Engine (PE) submodule manages the serial communication on the CAN bus:
• RAM access for receiving and transmitting message frames

---

*Page 2301*

CAN (FlexCAN)
• Receive message validation
• Error handling
• CAN FD message detection
Controller Host Interface (CHI) manages:
• Message buffer (MB) selection for reception and transmission
• Arbitration and ID matching algorithms for both CAN FD and non-CAN FD message formats
Bus Interface Unit (BIU) controls access to and from the internal interface bus to establish connection to the CPU and to other
blocks. The BIU manages access to:
• Clocks
• Address and data buses
• Interrupt outputs
• DMA
• Test signals

#### 70.2.2 Features

• Full implementation of CAN with Flexible data rate (CAN FD) protocol specification and CAN Specification Version 2.0,
Part B :
— Standard data frames
— Extended data frames
— Data length of 0–64 bytes
— Programmable bit rate (see chip-specific FlexCAN information for specific maximum rate configuration)
— Content-related addressing
• Compliance with ISO 11898-1:2015 standard
• Flexible message buffers that can be configured to store a payload of 0, 8, 16, 32, or 64 bytes
— Increasing the payload size decreases the number of supported message buffers (see FlexCAN memory partition for
CAN FD ).
— Message buffers are configurable as receive or transmit, supporting standard and extended messages.
• Individual Receive Mask registers for each message buffer
• Full-featured Legacy RX FIFO with storage capacity for six frames and automatic internal pointer handling with DMA
support
• Full-featured Enhanced RX FIFO with storage capacity of 20 CAN FD frames and automatic internal pointer handling with
DMA support
• Transmission abort capability
• Optional general purpose RAM space, using RAM not used by reception or transmission structures
• Listen-Only mode
• Programmable loopback mode supporting self-test operation
• Programmable transmission priority scheme: lowest ID, lowest buffer number, or highest priority
• Timestamp based on 32 -bit free-running timer , with optional external time tick
• Global network time, synchronized by specific message
• Maskable interrupts

---

*Page 2302*

CAN (FlexCAN)
• Independence from transmission medium (external transceiver is assumed)
• Short latency time due to arbitration scheme for high-priority messages
• Low-power mode
• Transceiver delay compensation when transmitting CAN FD messages at faster data rates
• Management of remote request frames, automatically or by software
• Restriction only to write CAN bit time settings and configuration bits in Freeze mode
• Transmit message buffer status (lowest-priority buffer or empty buffer)
• Identifier Acceptance Filter Hit Indicator (IDHIT) register for received frames
• ESR1[SYNCH] to indicate module is synchronous with CAN bus
• CRC status for transmitted message
• Legacy RX FIFO Global Mask register
• Selectable priority between message buffers and receive FIFO during matching process
• Powerful legacy RX FIFO ID filtering, capable of matching incoming IDs against either 128 extended IDs, 256 standard
IDs, or 512 partial (8-bit) IDs, with 32 individual masking capability
• Powerful enhanced RX FIFO ID filtering, capable of matching incoming IDs against either 64 extended or 128 standard ID
filter elements with three filtering schemes: mask plus filter, range, and two filters without mask
• Complete backward compatibility with previous FlexCAN version
• Detection and correction of errors in memory read accesses:
— Each byte of FlexCAN memory is associated to five parity bits.
— The error correction mechanism ensures that errors in one bit of this 13-bit word can be corrected (correctable
errors).
— Errors in two bits can be detected but not corrected (noncorrectable errors).

#### 70.3 Functional description

FlexCAN is a CAN protocol engine with a flexible message buffer system for transmitting and receiving CAN frames. The system
is a set of message buffers (MBs) that stores configuration and control data, timestamp, message ID, and data (see Message
buffer structure ). The memory corresponding to the first 38 message buffers can be configured to support a Legacy FIFO reception
scheme with a powerful ID filtering mechanism. This scheme can check incoming frames against a table of IDs (up to 128 extended
IDs or 256 standard IDs or 512 8-bit ID slices), with an individual mask register for up to 32 ID filter table elements.
For classical CAN frames, FlexCAN supports simultaneous reception through Legacy FIFO and message buffer. For CAN FD
frames, FlexCAN supports reception through message buffer and enhanced receive FIFO .
For message buffer reception, a matching algorithm makes it possible to store received frames only into MBs that have the same
ID programmed in the ID field. A masking scheme makes it possible to match the ID programmed on the message buffer with a
range of IDs on received CAN frames. For transmission, an arbitration algorithm decides the prioritization of message buffers to
be transmitted based on the message ID (optionally augmented by three local priority bits) or the message buffer ordering.
A message buffer is active at a given time if it can participate in both the matching and arbitration processes. A receive message
buffer with a 0b code is inactive (see Table 453 ). A transmit message buffer with a 1000b or 1001b code is also inactive (see
Table 454 ).
FlexCAN can receive and transmit messages in CAN FD format. The message buffers are sized to store the quantity of data bytes
selected by FDCTRL[MBDSRn] . The quantity of FD message buffers available for a given quantity of data bytes is described in
the CAN FD Control (FDCTRL) register. See also FlexCAN memory partition for CAN FD .

---

*Page 2303*

CAN (FlexCAN)

#### 70.3.1 Modes of operation

FlexCAN has these functional modes:
Table 418. Functional modes
Mode Description
Normal (User or
In Normal mode, FlexCAN receives and transmits message frames, manages errors normally, and
Supervisor)
enables all CAN Protocol functions. User and Supervisor modes differ in the access to some restricted
control registers.
Freeze
Freeze mode is enabled when MCR[FRZ] = 1. If enabled, FlexCAN enters Freeze mode when MCR[HALT]
is 1 or when is requested at chip level and FlexCAN writes 1 to MCR[FRZACK] . In this mode, no
transmission or reception of frames is done, and synchronicity to the CAN bus is lost. See Freeze mode .
Loopback
FlexCAN enters this mode when CTRL1[LPB] becomes 1. In this mode, FlexCAN performs an internal
loopback that can be used for self-test. The bit stream output of the transmitter is internally fed back
to the receiver input. The receiving CAN input pin is ignored and the transmitting CAN output goes to
the recessive state (logic 1). FlexCAN behaves as it normally does when transmitting and treats its own
transmitted message as a message received from a remote node. To ensure proper reception of its own
message, FlexCAN ignores the bit sent during the ACK slot in the CAN frame acknowledge field. Both
transmit and receive interrupts are generated.
Listen-Only
FlexCAN enters this mode when CTRL1[LOM] becomes 1. In this mode, transmission is disabled, all
error counters are frozen, and the module operates in a CAN Error Passive mode. Only messages
acknowledged by another CAN station are received. If FlexCAN detects an unacknowledged message, it
flags a BIT0 error (without changing the REC), as if it was trying to acknowledge the message.
CAN FD Active
In this mode, FlexCAN can transmit and receive all messages formatted according to the CAN FD
Standard (2.0) and 2.0B Protocol in an interleaved fashion. The CPU can put FlexCAN into CAN FD Active
mode by configuring MCR[FDEN] in Freeze mode.
Some features available in classical CAN are unavailable in CAN FD mode.
Table 419. Differences between classical CAN and CAN FD
Feature Classical CAN CAN FD
Legacy RX FIFO DMA Yes No
Legacy RX FIFO Yes No
Enhanced RX FIFO DMA Yes Yes
Enhanced RX FIFO Yes Yes
FlexCAN can operate in these low-power modes:
Table 420. Low-power modes
Mode Description
Module Disable
FlexCAN enters this mode when the CPU writes 1 to MCR[MDIS] and FlexCAN writes 1 to MCR[LPMACK] .
After FlexCAN is disabled, it issues a request to disable the clocks to the CAN Protocol Engine and
Controller Host Interface submodules. Writing 0 to MCR[MDIS] exits this mode. See Module Disable mode .

---

*Page 2304*

CAN (FlexCAN)
70.3.1.1 Modes of operation details
FlexCAN has functional modes and low-power modes. See Modes of operation for an introductory description of all the modes of
operation. The following subsections contain functional details about Freeze mode and low-power modes.
CAUTION
FlexCAN does not support "Permanent Dominant" failure on the CAN bus line. If a Low-Power request or Freeze
mode request occurs during a "Permanent Dominant" condition, the corresponding acknowledgment field can
never be 1.
70.3.1.1.1 Freeze mode
This mode is requested either by the CPU writing 1 to MCR[HALT] or when the chip is put into Debug mode . MCR[FRZ] must be
1 and the module must not be in a low-power mode.
When MECR[NCEFAFRZ] becomes 1 and a noncorrectable error is detected in a memory read access performed by FlexCAN
internal processes (see Error response ), FlexCAN also requests Freeze mode. This request occurs via both MCR[HALT] and
MCR[FRZ] automatically becoming 1.
To obtain acknowledgment, FlexCAN writes 1 to MCR[FRZACK] . The CPU must only consider FlexCAN to be in Freeze mode
when both request and acknowledgment conditions are satisfied.
When Freeze mode is requested, FlexCAN:
1. Waits to be in either Intermission, Error Passive, Bus Off, or Idle state.
2. Waits for all internal activities like arbitration, matching, move-in, and move-out to finish. A pending move-in does not
prevent entering Freeze mode.
3. Ignores the receive input pin and drives the transmit pin as recessive.
4. Stops the prescaler, halting all CAN protocol activities.
5. Grants write access to the Error Counters register, which is read-only in other modes.
6. Writes 1 to MCR[NOTRDY] and MCR[FRZACK] .
After requesting Freeze mode, you must wait for MCR[FRZACK] to become 1 before executing any other action, otherwise
FlexCAN may operate unpredictably. In Freeze mode, all memory-mapped registers are accessible.
Freeze mode is exited in one of these conditions:
• CPU writes 0 to MCR[FRZ].
• The chip is removed from Debug mode or the MCR[HALT] becomes 0.
MCR[FRZACK] becomes 0 after the protocol engine recognizes the negation of the freeze request. After leaving Freeze mode,
FlexCAN tries to resynchronize to the CAN bus by waiting for 11 consecutive recessive bits.
70.3.1.1.2 Module Disable mode
This low-power mode is normally used to disable a complete FlexCAN block temporarily, with no power consumption. The CPU
requests this mode by writing 1 to MCR[MDIS] , and FlexCAN acknowledges the request by writing 1 to MCR[LPMACK] . The CPU
must only consider FlexCAN to be in Disable mode when both the request and acknowledgment conditions are satisfied.
If FlexCAN is disabled during Freeze mode, the module requests to disable the clocks to the PE and CHI submodules, writes 1
to MCR[LPMACK] and writes 0 to MCR[FRZACK] .
If the module is disabled during transmission or reception , FlexCAN:
1. Waits to be in either Idle or Bus Off state, or waits for the third bit of Intermission and then checks that it is recessive.
2. Waits for all internal activities like arbitration, matching, move-in, and move-out to finish. FlexCAN does not take a pending
move-in into account.
3. Ignores its receive input pin and drives its transmit pin as recessive.

---

*Page 2305*

CAN (FlexCAN)
4. Shuts down the clocks to the PE and CHI submodules.
5. Writes 1 to MCR[NOTRDY] and MCR[LPMACK] .
In this mode, the Bus Interface Unit continues to operate, enabling the CPU to access memory-mapped registers, except for:
• The Receive Message Buffers Global Mask registers
• The Receive Buffer 14 Mask register
• The Receive Buffer 15 Mask register
• The Legacy Receive FIFO Global Mask register
When in Disable mode, these items may not be accessed:
• The Legacy Receive FIFO Information register
• The message buffers
• The Receive Individual Mask registers
• The reserved words within RAM
To exit this mode, the CPU writes 0 to MCR[MDIS] , causing FlexCAN to request to resume the clocks and write 0 to
MCR[LPMACK] . This write occurs after the CAN protocol engine recognizes the negation of disable mode requested by the CPU.

#### 70.3.2 Transmission process

NOTE
Instances of MB_CS in this topic refer to items in message buffers. See Message buffer structure for details.
To transmit a CAN frame, the CPU must prepare a message buffer for transmission by executing the following procedure:
1. Check whether the respective interrupt flag is set, and clear it if necessary.
2. If the message buffer is active (transmission pending), request an abort of the transmission. Write the ABORT code
(1001b) to the CODE field of the Control and Status word.
3. Poll the IFLAG register until the corresponding IFLAG flag is set, or wait for the interrupt request (if enabled by the
respective IMASK field).
4. Read the CODE field to identify whether the transmission was aborted or transmitted (see Transmission abort
mechanism ).
5. Clear the corresponding interrupt flag.
6. Write the ID register (containing the local priority if enabled via MCR[LPRIOEN] ).
7. Write payload data bytes.
8. Configure the Control and Status word as needed.
a. Set ID type via MB_CS[IDE].
b. Set Remote Transmission Request (if needed) via MB_CS[RTR].
c. If MCR[FDEN] = 1, configure MB_CS[EDL] and MB_CS[BRS]. For details about the relationship between the
[7]
written value and transmitted value of MB_CS[ESI], see Table 431 .
d. Configure Data Length Code in bytes via MB_CS[DLC]. See Table 457 for detailed information.
e. To transmit the CAN frame, activate the message buffer by writing Ch to MB_CS[CODE].
[7] There is no need to write the ESI field; it is automatically transmitted as dominant by error active nodes and as recessive
by error passive nodes. If FlexCAN is operating as a network gateway, however, the CPU writes MB_CS[ESI] according
to the error status of the node that sent the message.

---

*Page 2306*

CAN (FlexCAN)
NOTE
To maximize software performance, configure all the fields in the MB_CS word in a single 32-bit write operation. If
the fields are configured in separate writes, MB_CS[CODE] must be the last write in the Control and Status word.
When the MB is activated, it participates in the arbitration process and its contents are eventually transmitted according to its
priority. When the DLC value stored in the MB selected for transmission exceeds the MB payload size, FlexCAN completes the
expected DLC by adding a constant CCh pattern.
After a successful transmission:
1. The value of the free-running timer is written into the timestamp field.
2. The CODE field in the Control and Status word is updated.
3. Both Cyclic Redundancy Check (CRCR) and CAN FD CRC (FDCRC) are updated.
4. A status flag is set in the Interrupt Flag register.
5. If allowed by the corresponding Interrupt Mask Register field, an interrupt is generated.
After transmission, the new CODE field depends on the code used to activate the message buffer (see Table 453 and Table 454
in Message buffer structure ).
When the Abort feature is enabled ( MCR[AEN] is 1), after the Interrupt flag is set for an MB configured as transmit buffer, the
message buffer is blocked. The CPU cannot update the message buffer until the Interrupt Flag is cleared by the CPU. The CPU
must clear the corresponding IFLAG flag before preparing this MB for a new transmission or reception.
NOTE
For backward compatibility ( MCR[AEN] is 0), write the INACTIVE code (1000b) to the CODE field to deactivate the
MB. In this case, the pending frame may be transmitted without notification (see Message buffer inactivation ).

#### 70.3.3 Arbitration process

The arbitration process scans the message buffers, searching for the transmission MB that holds the message to be sent at
the next opportunity. This MB is called the arbitration winner. The scan starts from the lowest number MB and continues to the
higher ones.
The arbitration process is triggered when at least one of the following occur:
• CRC field of the CAN frame arrives. The starting point depends on the value of CTRL2[TASD] .
• Error Delimiter field of a CAN frame is in progress.
• Overload Delimiter field of a CAN frame is in progress.
• The winner of the arbitration is deactivated, and the CAN bus has not reached the first bit of the Intermission field.
• CPU writes to the Control and Status word of a winner MB, and the CAN bus has not reached the first bit of the
Intermission field.
• CHI is in the Idle state and the CPU writes to the Control and Status word of any message buffer.
• FlexCAN exits Bus Off state.
• FlexCAN leaves Freeze mode or a low-power mode.
If the arbitration process does not evaluate all message buffers before the CAN bus reaches the first bit of the Intermission field,
the temporary arbitration winner is invalidated. FlexCAN will not compete for the CAN bus at the next opportunity.
The arbitration process selects the winner among the active transmission message buffers at the end of the scan according to the
values of CTRL1[LBUF] and MCR[LPRIOEN] .
See Arbitration process (continued) for more information about this process.

---

*Page 2307*

CAN (FlexCAN)
70.3.3.1 Lowest-number message buffer first
If CTRL1[LBUF] is 1, the first (lowest number) active transmission message buffer found is the arbitration winner. MCR[LPRIOEN]
has no effect when CTRL1[LBUF] is 1.
70.3.3.2 Highest-priority message buffer first
If CTRL1[LBUF] is 0, the arbitration process searches for the active transmission message buffer with the highest priority. The
frame of this message buffer has a higher probability of winning the arbitration on the CAN bus when multiple external nodes
compete for the bus simultaneously.
The sequence of bits considered for this arbitration is called the arbitration value of the message buffer. The transmission message
buffer with the lowest arbitration value among all transmission message buffers has the highest priority.
If two or more message buffers have equivalent arbitration values, the message buffer with the lowest number is the
arbitration winner.
The composition of the arbitration value depends on MCR[LPRIOEN] .
70.3.3.2.1 Local priority disabled
If MCR[LPRIOEN] = 0, the arbitration value is built using the exact sequence of bits that would be transmitted in a CAN frame
where local priority is disabled.
Table 421. Composition of the arbitration value when local priority is disabled
Format Message buffer arbitration value (32 bits)
Standard (IDE = 0) Standard ID RTR (1 bit) IDE (1 bit) — (18 bits) — (1 bit)
(11 bits)
Extended (IDE = 1) Extended ID[28:18] SRR (1 bit) IDE (1 bit) Extended ID[17:0] RTR (1 bit)
(11 bits) (18 bits)
70.3.3.2.2 Local priority enabled
To enable local priority, MCR[LPRIOEN] must be 1. In this case, the message buffer PRIO field (see Message buffer structure )
is included at the very left of the arbitration value.
Table 422. Composition of the arbitration value when local priority is enabled
Format Message buffer arbitration value (35 bits)
Standard PRIO (3 bits) Standard ID RTR (1 bit) IDE (1 bit) — (18 bits) — (1 bit)
(IDE = 0) (11 bits)
Extended PRIO (3 bits) Extended SRR (1 bit) IDE (1 bit) Extended RTR (1 bit)
(IDE = 1) ID[28:18] ID[17:0]
(11 bits) (18 bits)
Because the PRIO field is the most significant part of the arbitration value, message buffers with low PRIO values have higher
priority than message buffers with high PRIO values. This priority is maintained regardless of the rest of their arbitration values.
The PRIO field is not part of the frame on the CAN bus. Its purpose is only to affect the internal arbitration process.
70.3.3.3 Arbitration process (continued)
After the arbitration winner is found (see Arbitration process ), its content is copied to a hidden auxiliary message buffer called
a transmit serial message buffer (TX SMB). The TX SMB has the same structure as a normal message buffer, but is not
user-accessible. This copy operation is called move-out. After it is done, write access to the Control and Status word of the
corresponding MB is blocked (if MCR[AEN] = 1). Write access is restored in one of the following events:

---

*Page 2308*

CAN (FlexCAN)
• The CPU clears the corresponding IFLAG flag after the message buffer is transmitted.
• FlexCAN enters Freeze mode or Bus Off state.
• FlexCAN loses the bus arbitration, or there is an error during the transmission.
At the first opportunity window on the CAN bus, the message on the TX SMB is transmitted according to the CAN protocol rules.
The arbitration process can be triggered under the following conditions:
• During RX and TX frames from CAN CRC field to end of frame. The value of CTRL2[TASD] may be changed to optimize
the arbitration start point.
• During CAN Bus Off state from TX_ERR_CNT = 124 to 128. The value of CTRL2[TASD] may be changed to optimize the
arbitration start point.
• During Control and Status write by CPU in Bus Idle. The first Control and Status write starts the arbitration process, and
a second Control and Status write during this same arbitration restarts the process. If other Control and Status writes are
performed, the transmission arbitration process is pending. If there is no arbitration winner after the arbitration process has
finished, then the TX arbitration machine begins a new arbitration process. If there is a pending arbitration and Bus Idle
state starts, then an arbitration process is triggered. In this case, the first and second Control and Status writes in Bus
Idle do not restart the arbitration process. If there is not enough time to finish arbitration in the Wait for Bus Idle state and
the next state is Idle, the scan is not interrupted. That scan is completed during Bus Idle state. During this arbitration, a
Control and Status write does not cause an arbitration restart.
• Deactivation of an arbitration winner during a valid arbitration window.
• Upon exiting Freeze mode (first bit of the Wait for Bus Idle state). If a resynchronization occurs during the Wait for Bus Idle
state, the arbitration process is restarted.
The arbitration process stops when:
• All message buffers are scanned.
• A transmission message buffer is found (if lowest buffer feature is enabled).
• An arbitration winner deactivates or aborts during any arbitration process.
• There is not enough time to finish the transmission arbitration process (for instance, when a deactivation is performed
near the end of frame). In this case, the arbitration process is pending.
• An error or overload flag occurs in the bus.
• A low-power or Freeze mode request occurs in Idle state.
Arbitration is considered pending when:
• It is not possible to finish arbitration process in time.
• A Control and Status write occurs during arbitration, when that write is performed in an MB whose number is lower than
the transmission arbitration pointer.
• Any Control and Status write occurs when there is no transmission arbitration process in progress.
• RX Match has updated an RX code to TX code.
• FlexCAN enters the Bus Off state.
If a Control and Status write is performed in the arbitration winner, a new process is restarted immediately.
If a Control and Status write is performed in an MB whose number is higher than the transmission arbitration pointer, the ongoing
arbitration process scans this MB as normal.

#### 70.3.4 Receive process

To be able to receive CAN frames into a message buffer, the CPU must prepare it for reception by executing the following steps:
1. If the message buffer is active (either TX or RX), deactivate it (see Message buffer inactivation ), preferably with a safe
deactivation (see Transmission abort mechanism ).

---

*Page 2309*

CAN (FlexCAN)
2. Write the ID word into the message buffer.
3. To activate the message buffer, write the EMPTY code (0100b) to the CODE field of the Control and Status word. No
setup is required for EDL, BRS, and ESI fields. The respective fields in the received message overwrite these fields.
After the MB is activated, it can receive frames that match the programmed filter. At the end of a successful reception, the move-in
process updates the message buffer (see Move-in ) as follows:
1. The received data field (up to eight bytes for Classical CAN message format and up to 64 bytes for CAN FD message
format ) is stored.
2. The received Identifier field is stored.
3. The value of the free-running timer when the second bit of the Identifier field of the frame is written into the Timestamp
field of the MB.
4. The received SRR, IDE, RTR, EDL, BRS, ESI, and DLC fields are stored.
5. The CODE field in the Control and Status word is updated (see Table 453 and Table 454 in Section Message buffer
structure ).
6. If allowed by the corresponding Interrupt Mask field, a status flag is set in the Interrupt Flag register and an interrupt is
generated.
The recommended way for the CPU to service (read) the frame received in a message buffer is:
1. Read the Control and Status word of that message buffer.
2. Verify that the BUSY bit is 0, indicating that the message buffer is locked. If it is not 0, repeat step 1 until it becomes 0.
See Message buffer lock mechanism .
3. Read the contents of the message buffer. After the message buffer is locked, FlexCAN move-in processes do not
modify its contents. See Move-in .
4. Acknowledge the proper flag in the IFLAG registers.
5. Unlock the message buffer by reading the free-running timer.
To verify frame reception, the CPU should poll the status flag bit for that specific message buffer in one of the IFLAG registers,
not the CODE field of that message buffer. Polling the CODE field does not work in this case. After a frame is received and the
CPU services the message buffer (by reading the Control and Status word and unlocking the message buffer), the CODE field
does not return to EMPTY. It remains FULL, as explained in Table 453 . If the CPU tries to work around this behavior by writing to
the Control and Status word to force an EMPTY code after reading the message buffer without a prior safe deactivation, a newly
received frame matching the filter of that message buffer may be lost.
CAUTION
In summary: never poll by reading the Control and Status word of the message buffers directly. Instead, read the
IFLAG registers.
The Identifier field of the received frame is always stored in the matching message buffer. If the match was due to masking,
the contents of the ID field in a message buffer may change. When MCR[SRXDIS] becomes 1, FlexCAN does not store frames
transmitted by itself in any MB, even if it contains a matching receive MB. Also, no interrupt flag or interrupt signal is generated.
Otherwise, when MCR[SRXDIS] becomes 0, if a matching receive MB exists, FlexCAN can receive frames transmitted by itself.
To be able to receive CAN frames through the Legacy RX FIFO, the CPU must enable and configure the Legacy RX FIFO during
Freeze mode (see Legacy RX FIFO ). Upon receiving the Frames Available in the Legacy RX FIFO interrupt (see MCR[BUF5I] ),
the CPU must service the received frame using the following procedure:
1. If a mask was used for IDE and RTR bits, read the Control and Status word.
2. If a mask was used, read the ID field.
3. Read the data field.
4. If the Identifier Acceptance Filter Hit Indicator (IDHIT) is required in the application, read Legacy RX FIFO Information
(RXFIR) .

---

*Page 2310*

CAN (FlexCAN)
5. Clear the Frames Available in Legacy RX FIFO interrupt by writing one to IFLAG1[BUF5I]. This step is mandatory; it
releases the MB and allows the CPU to read the next RX FIFO entry.
When MCR[DMA] ) becomes 1, upon receiving a frame in the Legacy FIFO, IFLAG1[BUF5I] generates a DMA request and does
not generate a CPU interrupt (see Legacy RX FIFO in DMA operation ). The IMASK1 fields in the Legacy RX FIFO region are
not used.
The DMA controller must service the received frame using the following procedure:
1. Read the Control and Status word (read 80h address, optional).
2. Read the ID field (read 84h address, optional).
3. Read all data bytes (start read at 88h address, optional).
4. Read the last data bytes (read 8Ch address, mandatory).

#### 70.3.5 Matching process

The matching process scans the message buffer memory for RX MBs programmed with the same ID as the one received from
the CAN bus. If the Legacy RX or Enhanced RX FIFO is enabled, the priority of scanning can be selected between MBs and FIFO
filters. The matching starts from the lowest number message buffer and continues toward the higher-numbered ones. If no match
is found within the first structure, the other is scanned afterward. If the FIFO is full, the matching algorithm always looks for a
matching MB outside the FIFO region.
For enhanced RX FIFO, see Enhanced RX FIFO .
For legacy RX FIFO, see Legacy RX FIFO .
As the frame is received, it is stored in a hidden auxiliary MB called Receive Serial Message Buffer (RX SMB).
The starting point of the matching process depends on the following conditions:
• If the received frame is a remote frame, the starting point is the CRC field of the frame.
• If the received frame is a data frame with DLC field equal to zero, the starting point is the CRC field of the frame.
• If the received frame is a data frame and the DLC field has a nonzero value, the starting point is the DATA field of the
frame.
If a matching ID is found in the FIFO table or in one of the message buffers, the move-in process transfers the contents of the RX
SMB to the FIFO or to the matched MB. If any CAN protocol error is detected, no match results are transferred to the FIFO or to
the matched MB at the end of reception.
The matching process scans all matching elements of the RX FIFO (if enabled) and active receive MBs (CODE is EMPTY, FULL,
OVERRUN, or RANSWER). The process searches for a successful comparison to the matching elements of the RX SMB that is
receiving the frame on the CAN bus. The RX SMB has the same structure as a message buffer. The reception structures ( RX FIFO
or message buffers) associated with the matching elements that had a successful comparison are the matched structures. The
matching winner is selected at the end of the scan among those matched structures. The matching winner depends on conditions
described in Table 423 .
Table 423. Matching architecture
1
Structure SMB[RTR] CTRL2[RRS] CTRL2[EACE MB[IDE] MB[RTR] MB[ID ] MB[CODE]
N]
2 3 4
Message 0 — 0 cmp no_cmp cmp_msk EMPTY,
buffer FULL, or
OVERRUN
Message 0 — 1 cmp_msk cmp_msk cmp_msk EMPTY,
buffer FULL, or
OVERRUN
Table continues on the next page...

---

*Page 2311*

CAN (FlexCAN)
Table 423. Matching architecture (continued)
1
Structure SMB[RTR] CTRL2[RRS] CTRL2[EACE MB[IDE] MB[RTR] MB[ID ] MB[CODE]
N]
Message 1 0 — cmp no_cmp cmp RANSWER
buffer
Message 1 1 0 cmp no_cmp cmp_msk EMPTY,
buffer FULL, or
OVERRUN
Message 1 1 1 cmp_msk cmp_msk cmp_msk EMPTY,
buffer FULL, or
OVERRUN
Legacy RX — — — cmp_msk cmp_msk cmp_msk —
5
FIFO
1. For message buffer structure, if SMB[IDE] is 1, the ID is 29 bits (ID Standard plus ID Extended). If SMB[IDE] is 0, the ID is
11 bits (ID Standard). For Legacy RX FIFO structure, the ID depends on IDAM.
2. cmp: Compares the RX SMB contents to the MB contents regardless of masks.
3. no_cmp: The RX SMB contents are not compared to the MB contents.
4. cmp_msk: Compares the RX SMB contents to MB contents, accounting for masks.
5. SMB[IDE] and SMB[RTR] are not considered when IDAM is type C.
NOTE
For Enhanced RX FIFO, see Enhanced RX FIFO matching process .
A reception structure is free-to-receive when any of the following conditions is satisfied:
• The CODE field of the message buffer is EMPTY.
• The CODE field of the message buffer is either FULL or OVERRUN, and it has already been serviced (the CPU has read
the Control and Status word and unlocked it as described in Message buffer lock mechanism ).
• The CODE field of the message buffer is either FULL or OVERRUN and an inactivation (see Message buffer inactivation )
is performed.
• The Legacy RX FIFO or Enhanced RX FIFO is not full.
The scan order for message buffers and Legacy Receive FIFO is from the matching element with the lowest number to the
higher ones.
70.3.5.1 Matching priority
MCR[IRMQ] affects the matching winner search for MBs. If the field is 0, the matching winner is the first matched MB whether it
is free-to-receive or not. If it is 1, the matching winner is selected according to this priority:
1. The first free-to-receive matched message buffer
2. The last non-free-to-receive matched message buffer
It is possible to select the priority of scan between MBs and Legacy RX FIFO or Enhanced RX FIFO with CTRL2[MRP] .
If the selected priority is RX FIFO first:
• If the RX FIFO is a matched structure and is free-to-receive, the RX FIFO is the matching winner regardless of the scan
for MBs.
• Otherwise, the matching winner is searched for among MBs as described above.
If the selected priority is MBs first:
• If a free-to-receive matched MB is found, it is the matching winner regardless of the scan for RX FIFO.

---

*Page 2312*

CAN (FlexCAN)
• If no matched MB is found, then the matching winner is searched for in the scan for the RX FIFO.
• If both conditions above are not satisfied and a non-free-to-receive matched MB is found, then the matching winner
depends on the value of MCR[IRMQ] :
— If MCR[IRMQ] = 0, the matching winner is the first matched MB.
— If MCR[IRMQ] = 1, the matching winner is the RX FIFO if it is a free-to-receive matched structure. Otherwise, the
matching winner is the last non-free-to-receive matched MB.
Table 424. Matching possibilities and resulting reception structures
RFEN or IRMQ MRP Matched in MB Matched in Reception Description
ERFEN FIFO structure
No FIFO, only MB, match is always MB first
1 2 3
0 0 X None — None Frame lost by
no match
4
0 0 X Free — First MB —
0 1 X None — None Frame lost by
no match
0 1 X Free — First MB —
0 1 X Not free — Last MB Overrun
FIFO enabled, no match in FIFO; is as if FIFO does not exist
5
1 0 X None None None Frame lost by
no match
1 0 X Free None First MB —
1 1 X None None None Frame lost by
no match
1 1 X Free None First message —
buffer
1 1 X Not free None Last message Overrun
buffer
FIFO enabled; queue disabled
6
1 0 0 X Not full FIFO —
7
1 0 0 None Full None Frame lost by
FIFO full (FIFO
overflow)
1 0 0 Free Full First MB —
1 0 0 Not free Full First MB
1 0 1 None Not full FIFO
1 0 1 None Full None Frame lost by
FIFO full (FIFO
overflow)
1 0 1 Free X First message —
buffer
Table continues on the next page...

---

*Page 2313*

CAN (FlexCAN)
Table 424. Matching possibilities and resulting reception structures (continued)
RFEN or IRMQ MRP Matched in MB Matched in Reception Description
ERFEN FIFO structure
1 0 1 Not free X First message Overrun
buffer
FIFO enabled; queue enabled
1 1 0 X Not full FIFO —
1 1 0 None Full None Frame lost by
FIFO full (FIFO
overflow)
1 1 0 Free Full First message —
buffer
1 1 0 Not free Full Last message Overrun
buffer
1 1 1 None Not full FIFO —
1 1 1 Free X First message —
buffer
1 1 1 Not free Not full FIFO —
1 1 1 Not free Full Last message Overrun
buffer
1. Indicates a don't care condition.
2. Matched in message buffer "None" means that the frame has not matched any message buffer (free-to-receive or non-
free-to-receive).
3. This condition is forbidden.
4. Matched in message buffer "Free" means that the frame matched at least one message buffer free-to-receive regardless of
whether it has matched MBs that are non-free-to-receive.
5. Matched in FIFO, "None" means that the frame has not matched any filter in FIFO. It is as if the FIFO did not exist
( CTRL2[RFFN] = 0 and ERFCR[ERFEN] = 0 ).
6. Matched in FIFO, "Not full" means that the frame has matched a FIFO filter and has empty slots to receive it.
7. Matched in FIFO, "Full" means that the frame matched a FIFO filter but could not store it, because it has no empty slots to
receive it.
70.3.5.2 Special cases
If a non-safe MB inactivation (see Message buffer inactivation ) occurs during the matching process and the inactivated MB is
the temporary matching winner, the temporary matching winner is invalidated. The matching elements scan is not stopped and
not restarted; it continues normally. The consequence is that the current matching process works as if the matching elements
compared before the inactivation did not exist. In this case, a message may be lost.
Consider an example where:
• The FIFO is disabled.
• MCR[IRMQ] is 1.
• There are two message buffers with the same ID: the second and fifth MBs in the array.
• FlexCAN starts receiving messages with that ID.
When the first message arrives, the matching algorithm finds the first match in message buffer number 2. The code of this
message buffer is EMPTY, so the message is stored in that MB. When the second message arrives, the matching algorithm finds
MB number 2 again, but it is not "free-to-receive." It continues looking, finds MB number 5, and stores the message in that MB.

---

*Page 2314*

CAN (FlexCAN)
If yet another message with the same ID arrives, the matching algorithm finds no matching free-to-receive MBs, so it overwrites
the last matched message buffer (MB number 5). In doing so, it updates the CODE field of the message buffer to OVERRUN.
The ability to match the same ID in more than one MB can be used to implement a reception queue (in addition to the full-featured
FIFO) to allow more time for the CPU to service the MBs. By programming more than one MB with the same ID, received messages
are queued into the message buffers. The CPU can examine the Timestamp field of the message buffers to determine the order
in which the messages arrived.
Matching a range of IDs is possible via ID acceptance masks. FlexCAN supports individual masking per message buffer (see
Receive Individual Mask (RXIMR0 - RXIMR95) ). During the matching algorithm, if a mask field is 1, the corresponding ID bit is
compared. If the mask field is 0, the corresponding ID bit is a "don't care". Individual Mask Registers are implemented in RAM, so
they are not initialized out of reset. Also, they can only be programmed when the module is in Freeze mode; otherwise, the module
blocks them.
FlexCAN also supports an alternate masking scheme with only Legacy RX FIFO Global Mask (RXFGMASK) , RX Message Buffers
Global Mask (RXMGMASK) , Receive 14 Mask (RX14MASK) , and Receive 15 Mask (RX15MASK) for backward compatibility with
legacy applications. This alternate masking scheme is enabled when the MCR[IRMQ] = 0.

#### 70.3.6 Move process

There are two types of move process: move-in and move-out.
70.3.6.1 Move-in
The move-in process is the copying of a message received by an RX SMB to an RX message buffer or FIFO that has matched
it. If the move destination is the Legacy RX FIFO, attributes of the message are also copied to the CAN_RXFIR FIFO. Each RX
SMB has its own move-in process, but only one is performed at a given time. The move-in starts only when the message held by
the RX SMB has a corresponding match (see Matching process ) and all of the following conditions are true:
• The CAN bus has reached or already gone past:
— The second bit of Intermission field next to the frame that carried the message that is in the RX SMB.
— The first bit of an overload frame next to the frame that carried the message in the RX SMB.
• There is no ongoing matching process.
• The CPU is not locking the destination message buffer.
• No move-in process from another RX SMB is ongoing. If more than one move-in process is to be started at the same time,
both are performed and the newest process substitutes for the oldest.
The term "pending move-in" is used throughout the documentation and stands for a move-to-be that does not satisfy all of the
above conditions.
If any of the following conditions is satisfied, the move-in is canceled and the RX SMB is able to receive another message:
• The destination message buffer is inactivated after the CAN bus has reached the first bit of the Intermission field next to
the frame that carried the message. Also, its matching process has finished.
• There is a previous pending move-in to the same destination message buffer.
• The RX SMB is receiving a frame transmitted by FlexCAN itself and self-reception is disabled ( MCR[SRXDIS] = 1).
• Any CAN protocol error is detected.
If the module enters Freeze or Low-Power mode, the pending move-in is not canceled. It remains on hold, waiting for Freeze and
Low-Power mode to be exited and for the module to be unlocked. If a message buffer is unlocked during Freeze mode, the move-in
occurs immediately.
The move-in process is FlexCAN executing the following steps:
1. Push IDHIT into the RXFIR FIFO if the message is destined for the Legacy RX FIFO.
2. Read all data words from the RX SMB in accordance with the selected payload size for the RX storage element.

---

*Page 2315*

CAN (FlexCAN)
3. Write all data words to the RX message buffer according to the selected payload size for the RX storage element. If the
data size of the storage element is smaller than the original payload size described in the DLC field of the message, the
payload is truncated. The high-order bytes that do not fit the destination size are lost.
4. Read the Control and Status and ID words from the RX SMB.
5. Write the Control and Status and ID words to the RX message buffer, and update the CODE field.
The move-in process is not atomic; the inactivation of the destination message buffer immediately cancels it (see Message
buffer inactivation ). In this case, the message buffer may remain partially updated, and therefore incoherent. When the move-in
destination is a Legacy or Enhanced RX FIFO message buffer, however, the process cannot be canceled.
To alert the CPU that the message buffer content is temporarily incoherent, the BUSY Bit (least significant bit of the CODE field)
of the destination message buffer becomes 1 during move-in.
70.3.6.2 Move-out
The move-out process is the copying of content from a TX message buffer to the TX SMB when a message for transmission is
available (see Arbitration process ). The move-out occurs in the following conditions:
• In the first bit of the Intermission field
• During the Bus Off state, when the TX error counter is in the 124 to 128 range
• During the Bus Idle state
• During the Wait for Bus Idle state
The move-out process is not atomic. Only the CPU has priority to access the memory concurrently outside the Bus Idle state. In
Bus Idle, the move-out has the lowest priority of the concurrent memory accesses.

#### 70.3.7 Data coherence

In order to maintain data coherency and proper FlexCAN operation, the CPU must obey the rules described in Transmission
process and Receive process .
70.3.7.1 Transmission abort mechanism
The abort mechanism provides a safe way to request the abortion of a pending transmission. A feedback mechanism is provided
to inform the CPU whether the transmission was aborted or the frame could not be aborted and was transmitted instead.
These primary conditions must be fulfilled in order to abort a transmission:
• MCR[AEN] must be 1.
• The first CPU action must be the writing of abort code (1001b) into the CODE field of the Control and Status word.
Active message buffers configured for transmission must be aborted before they can be updated. The write operation is blocked
and the transmission is not disturbed when the abort code is written to:
• A message buffer currently being transmitted.
• A message buffer that was already loaded into the TX SMB for transmission.
In this case, the abort request is captured and kept pending until one of the following conditions is satisfied:
• The module loses the bus arbitration.
• There is an error during the transmission.
• The module is put into Freeze mode.
• The module enters the Bus Off state.
• There is an overload frame.
If none of the conditions above are reached:

---

*Page 2316*

CAN (FlexCAN)
1. The message buffer is transmitted correctly.
2. The interrupt flag is set in the proper IFLAG register.
3. If enabled, an interrupt to the CPU is generated.
The abort request is automatically cleared when the interrupt flag is set. If only one of the above conditions is reached, the frame
is not transmitted. In this case:
1. The abort code is written into the CODE field.
2. The interrupt flag is set in the proper IFLAG register.
3. Optionally, an interrupt is generated to the CPU.
If the CPU writes the abort code before the transmission begins internally, the write operation is not blocked. The MB is updated
and the interrupt flag is set. In this way, the CPU only needs to read the abort code to verify that the active MB was safely
inactivated. In this case, although MCR[AEN] = 1 and the CPU wrote the abort code, the MB is inactivated and not aborted,
because the transmission did not start yet. A message buffer is aborted only when the abort request is captured and kept pending
until one of the previous conditions is satisfied.
70.3.7.2 Message buffer inactivation
Inactivation protects the message buffer against updates by FlexCAN internal processes. It allows the CPU to rely on message
buffer data coherence after having updated it, even in Normal mode.
Inactivation of transmission message buffers must be performed only when MCR[AEN] = 0.
If a message buffer is inactivated, it does not participate in the arbitration process or the matching process until it is reactivated.
See Transmission process and Receive process for detailed instructions on how to inactivate and reactivate a message buffer.
To inactivate a message buffer, the CPU must update its CODE field to INACTIVE (either 0b or 1000b).
Because you cannot synchronize the CODE field update with FlexCAN internal processes, an inactivation can have the
following consequences:
• A frame in the bus that matches the filtering of an inactivated RX message buffer may be lost without notice. This loss can
occur even if there are other message buffers with the same filter.
• A frame containing the message within an inactivated TX message buffer may be transmitted without setting the
respective IFLAG flag.
To perform a safe inactivation and avoid the above consequences for TX message buffers, the CPU must use the transmission
abort mechanism (see Transmission abort mechanism ).
The inactivation automatically unlocks the message buffer (see Message buffer lock mechanism ).
NOTE
Message buffers that are part of the Legacy RX FIFO or Enhanced RX FIFO cannot be inactivated. There is no write
protection on the Legacy FIFO region by FlexCAN. The CPU must maintain data coherency in the Legacy FIFO
region when MCR[RFEN] = 1.
70.3.7.3 Message buffer lock mechanism
In addition to message buffer inactivation, FlexCAN uses a message buffer lock mechanism to maintain data coherence for the
receive process. When the CPU reads the Control and Status word of an RX message buffer with codes FULL or OVERRUN,
FlexCAN is configured to allow the CPU to read the whole message buffer in an atomic operation. FlexCAN sets an internal lock
flag for that message buffer.
The lock is released in any of the following events:
• The CPU reads the free-running timer (global unlock operation).
• The CPU reads the Control and Status word of another message buffer, regardless of its code.

---

*Page 2317*

CAN (FlexCAN)
• The CPU writes into the Control and Status word. This procedure is not recommended for normal unlocking, because it
cancels a pending move and may lose a received message.
The message buffer lock prevents a new frame from being written into the message buffer when the CPU is reading it.
NOTE
The locking mechanism applies only to RX message buffers that are not part of the Legacy RX FIFO and have a
[1]
code other than INACTIVE (0b) or EMPTY (0100b). TX message buffers cannot be locked.
Consider an example where:
• The Legacy RX FIFO is disabled.
• The second and the fifth message buffers of the array are programmed with the same ID.
• FlexCAN has already received and stored messages into these two message buffers.
• The CPU reads message buffer number 5 while another message with the same ID is arriving.
When the CPU reads the Control and Status word of message buffer number 5, this message buffer is locked. The new message
arrives and the matching algorithm finds no free-to-receive message buffers, so it overrides message buffer number 5. This
message buffer is locked, so the new message cannot be written to it. The message remains in the RX SMB until the message
buffer is unlocked, and only then is it written to the message buffer.
If the message buffer remains locked and another new message with the same ID arrives, the new message overwrites the one
in the RX SMB. There is no indication of lost messages in the CODE field of the message buffer or in Error and Status 1 (ESR1) .
When the message is moved from the RX SMB to the message buffer, the BUSY bit on the CODE field becomes 1. If the CPU
reads the Control and Status word and identifies that the BUSY bit is 1, it must wait until the BUSY bit becomes 0 to access the MB.
NOTE
If the BUSY bit is 1 or the message buffer is empty, reading the Control and Status word does not lock the
message buffer.
Inactivation takes precedence over locking. If the CPU inactivates a locked receive message buffer, then its lock status is negated
and the message buffer is marked as invalid for the current matching round. Any pending message on the RX SMB is not
transferred to the message buffer. A message buffer is unlocked when the CPU reads Free-Running Timer (TIMER) or the Control
and Status word of another message buffer.
The lock and unlock mechanisms have the same functionality in Normal and Freeze modes.
An unlock during Normal or Freeze mode results in the move-in of the pending message. If unlocking occurs during a low-power
mode, however, the move-in is postponed (see Modes of operation ). Move-in takes place only when the module returns to Normal
or Freeze mode.

#### 70.3.8 Enhanced RX FIFO

FlexCAN supports an enhanced RX FIFO engine which can store up to 20 CAN FD messages. The region 2000h– 204Fh contains
the output of the FIFO, which the CPU should read. To enable the enhanced RX FIFO, write 1 to ERFCR[ERFEN] . FlexCAN has
two FIFO options, Legacy RX FIFO and Enhanced RX FIFO, but both options cannot be enabled at the same time. See Legacy
RX FIFO for additional information.
To configure the enhanced RX FIFO watermark, write a value to ERFCR[ERFWM] . If ERFCR[ERFWM] is configured, the CPU is
notified only if a minimum number of messages is stored in the FIFO. When the number of stored messages is greater than the
value in ERFCR[ERFWM], the module sets ERFSR[ERFWMI] . Optionally, if MCR[DMA] or ERFIER[ERFWMIIE] are enabled, a
DMA transfer or an interrupt can be triggered , respectively .
For the enhanced RX FIFO to receive, the CPU must execute the configuration procedure below. If the CPU must change any
configurations of the Enhanced RX FIFO, the same procedure must be followed.
[1] In previous FlexCAN versions, reading the Control and Status word locked the message buffer even when CODE indicates
it is EMPTY. This behavior is maintained when the IRMQ bit is 0.

---

*Page 2318*

CAN (FlexCAN)
Prerequisites
MCR[RFEN] must be 0.
Procedure
Step Purpose Programming Notes
1 Enter Freeze mode. See Freeze mode . —
2 If enhanced RX FIFO is not already Write 1 to ERFCR[ERFEN] . —
enabled, enable it.
3 Reset enhanced RX FIFO engine. Write 1 to ERFSR[ERFCLR] . —
4 If the enhanced RX FIFO error flags are Write 1 to these flags: —
set, clear them.
• ERFSR[ERFUFW]
• ERFSR[ERFOVF]
• ERFSR[ERFWMI]
• ERFSR[ERFDA]
5 Specify the total number of enhanced Write the number to ERFCR[NFE] . —
RX FIFO filter elements to be used in
Enhanced RX FIFO reception.
6 Specify the number of extended ID and Write the number to ERFCR[NEXIF] . ERFCR[NEXIF] ≤ ERFCR[NFE]
standard ID filter elements to be used in + 1.
Enhanced RX FIFO reception.
7 If you are using DMA, enable DMA. Write 1 to MCR[DMA] . —
8 If you are using DMA, specify the Write the number to ERFCR[DMALW] . —
number of words to transfer for each
Enhanced RX FIFO data element.
9 Specify the Enhanced RX FIFO Write the number to ERFCR[ERFWM] . If MCR[DMA] = 1,
watermark. ERFCR[ERFWM] should be 0h.
10 If you are using interrupts, enable the Write 1 to the interrupt enables in —
interrupts. Enhanced RX FIFO Interrupt Enable
(ERFIER) .
11 Configure the filter elements. Write to the ERFFEL n registers. ERFFEL n registers are
implemented in RAM; you must
explicitly initialize them before
prior any reception.
12 Exit Freeze mode. See Freeze mode . —
There are two types of enhanced RX FIFO filter elements that can be stored in ERFFEL n registers: extended-ID filter elements
and standard-ID filter elements. Each extended-ID filter element is stored in two ERFFEL n registers, and each standard-ID filter
element is stored in one ERFFEL n register. ERFCR[NFE] defines the total number of Enhanced RX FIFO filter elements.
In addition, the filter memory space can be split into two regions: one for extended-ID filter elements and another for standard-ID
filter elements, according to ERFCR[NEXIF]. Figure 371 shows how the enhanced RX filter elements are defined. See Enhanced
RX FIFO matching process for information about the Enhanced RX FIFO matching process and filter element formats.

---

*Page 2319*

CAN (FlexCAN)
3000h
Extended ID
filter element
NEXIF
Standard ID
filter element
NFE
Last ERFFEL
address
Figure 371. Number of enhanced RX FIFO filter elements
70.3.8.1 Enhanced RX FIFO matching process
When ERFCR[ERFEN] = 1, FlexCAN scans the ERFFEL n memory region. If at least one filter element satisfies the matching
criteria, the CAN message content is transferred to the enhanced RX FIFO memory. If multiple filters match the incoming message
ID, the first matching filter found by the matching process must be indicated in IDHIT.
Each ERFFEL n register can store one standard filter element. ERFFEL n [FEL] [31:30], also called FSCH, determines the matching
criteria in this way:
• If FSCH = b00, the filter scheme is based on mask and filter. A CAN message matches a standard ID filter element only if
these criteria are reached:
1. CAN message is base-frame format (IDE = 0).
2. (ID[ n ] = STD ID filter [ n ]) or (STD ID Mask[ n ] = 0) for each bit n from 0 to 10.
3. (RTR = RTR Filter) or (RTR MASK = 0).
In this explanation, RTR and ID are the Remote Transmit Request field and the ID from a CAN message, respectively.
If FSCH = b00, the filters and masks are defined as shown in Table 425 .
Table 425. Standard ID filter element with filter and mask scheme (FSCH = b00)
31 30 29 28 27 26 16 15 12 11 10 0
FSCH = b00 Reserved RTR STD ID Filter Reserved RTR STD ID MASK
Filter MAS
K

---

*Page 2320*

CAN (FlexCAN)
• If FSCH = b01, the filter scheme is based on range. A CAN message matches a standard ID filter element only if these
criteria are reached:
1. CAN message is base frame format (IDE = 0).
2. ID ≥ STD ID Filter1.
3. ID ≤ STD ID Filter2.
4. (RTR = RTR filter) or (RTR MASK = 0).
RTR and ID are the Remote Transmit Request bit and ID from a CAN message, respectively. If FSCH = b01, the filters and mask
are defined as shown in Table 426 .
Table 426. Standard ID filter element with range scheme (FSCH = b01)
31 30 29 28 27 26 16 15 12 11 10 0
FSCH = b01 Reserved RTR STD ID Filter2 Reserved RTR STD ID Filter1
Filter MAS
K
• If FSCH = b10, the filter scheme is based on two filters without masks. A CAN message matches a standard ID filter
element only if these criteria are reached:
1. CAN message is base frame format (IDE = 0).
2. (ID[ n ] = STD ID Filter1[ n ]) or (ID[ n ] = STD ID Filter2[ n ]) for each bit n from 0 to 10.
3. (RTR = RTR Filter1) or (RTR = RTR Filter2).
RTR and ID are the Remote Transmit Request bit and ID from a CAN message, respectively. If FSCH = b10, the filters are defined
as shown in Table 427 .
Table 427. Standard ID filter element with two-filter scheme (FSCH = b10)
31 30 29 28 27 26 16 15 12 11 10 0
FSCH = b10 Reserved RTR STD ID Filter2 Reserved RTR STD ID Filter1
Filter Filter
2 1
Each pair of ERFFEL n registers can store one extended filter element. ERFFEL n [FSCH] determines the matching criteria in
this way:
• If FSCH = b00, the filter scheme is based on mask and filter. A CAN message matches an extended ID filter element only if
these criteria are reached:
1. CAN message is extended frame format (IDE = 1).
2. (ID[ n ] = EXT ID filter [ n ]) or (EXT ID Mask[ n ] = 0) for each bit n from 0 to 28.
3. (RTR = RTR Filter) or (RTR MASK = 0).
If FSCH = b00, the filters and masks are defined as shown in Table 428 .
Table 428. Extended ID filter element with filter + mask scheme (FSCH = b00)
31 30 29 28 0
FSCH RTR EXT ID Filter
Filter
Reserved RTR EXT ID MASK
MASK

---

*Page 2321*

CAN (FlexCAN)
• If FSCH = b01, the filter scheme is based on range. A CAN message matches an extended ID filter element only if the
following criteria are reached:
1. CAN message is extended frame format (IDE = 1).
2. ID ≥ EXT ID Filter1.
3. ID ≤ EXT ID Filter2.
4. (RTR = RTR Filter) or (RTR MASK = 0).
If FSCH = b01, the filters and masks are defined as shown in Table 429 .
Table 429. Extended ID filter element with range scheme (FSCH = b01)
31 30 29 28 0
FSCH RTR EXT ID Filter2
Filter
Reserved RTR EXT ID Filter 1
MASK
• If FSCH = b10, the filter scheme is based on two filters without masks. A CAN message matches an extended ID filter
element only if these criteria are reached:
1. CAN message is extended frame format (IDE = 1).
2. (ID[ n ] = EXT ID Filter1[ n ]) or (ID[ n ] = EXT ID Filter2[ n ]) for each bit n from 0 to 28.
3. (RTR = RTR Filter1) or (RTR = RTR Filter2).
If FSCH = b10, the filters are defined as shown in Table 430 .
Table 430. Extended ID filter element with two-filter scheme (FSCH = b10)
31 30 29 28 0
FSCH RTR EXT ID Filter2
Filter2
Reserved RTR EXT ID Filter 1
Filter1
70.3.8.2 Enhanced RX FIFO under DMA operation
You can enable the DMA feature by writing 1 to both ERFCR[ERFEN] and MCR[DMA] . The DMA controller can read the received
message by reading a message buffer structure at the enhanced FIFO output port at the address range defined in Enhanced RX
FIFO structure .
NOTE
FlexCAN supports 32-bit access only for DMA transfers.
For proper FIFO engine operation, the CPU should not access the Enhanced FIFO output port address range during DMA
operation. Before writing 1 to MCR[DMA], the CPU must service Enhanced RX FIFO status bits. Otherwise, these bits may show
that the FIFO has data to be serviced, and mistakenly generate a DMA request. Before writing 0 to MCR[DMA], the CPU must first
clear the ERFSR[ERFUFW] , ERFSR[ERFOVF] , ERFSR[ERFWMI] , and ERFSR[ERFDA] flags. It must then clear the enhanced
RX FIFO engine by writing one to ERFSR[ERFCLR] .
When there is one frame available to be read from the Enhanced RX FIFO, FlexCAN sets ERFSR[ERFDA]. Upon receiving the
request, the DMA controller can read the message in the Enhanced RX FIFO output. Each message reading process must end
by the address defined in ERFCR[DMALW] .
Follow these rules for Enhanced RX FIFO DMA operation:

---

*Page 2322*

CAN (FlexCAN)
• Because a DMA transfer cannot be changed dynamically, program ERFCR[DMALW] so the enhanced RX FIFO element
can store the largest CAN message present on the CAN bus.
• Data bytes are valid according to the DLC field. See Table 457 .
Each time the DMA controller reads one message from the FIFO, FlexCAN clears ERFSR[ERFDA]. If there is at least one
message stored in the FIFO, FlexCAN sets it again.
Consider an example where the maximum number of bytes in the data field of a CAN frame for a certain application is eight , and
high-resolution timestamp is enabled . In that case, the last enhanced RX FIFO address offset can be found in Table 467 and Table
468 . Using this address offset, ERFCR[DMALW] can be determined in this way:
• Maximum number of data bytes = 8
• HR_TIME_STAMP enabled
• Last address offset = TS_OFF = 2014h
• DMALW = 5
70.3.8.3 Enhanced RX FIFO clear operation
When ERFCR[ERFEN] is 1, the CPU can clear the Enhanced RX FIFO by writing 1 to ERFSR[ERFCLR] . The clear
operation resets the internal FIFO pointers, but the FIFO content stored in RAM is not changed. This operation can only
be performed in Freeze mode; the module blocks the operation in other modes. This operation does not clear ERFSR[ERFUFW] ,
ERFSR[ERFOVF] , ERFSR[ERFDA] , or ERFSR[ERFWMI] . The CPU must service all these fields before executing the clear
FIFO operation.

#### 70.3.9 Legacy RX FIFO

The Legacy RX FIFO is receive-only. To enable it, write 1 to MCR[RFEN] . To maintain software backward compatibility with
previous versions of FlexCAN that did not have the Legacy FIFO feature, the reset value of this field is zero.
CAUTION
Do not enable Legacy RX FIFO when the CAN FD feature is enabled.
The Legacy FIFO is six messages deep. The memory region the Legacy FIFO structure occupies (both message buffers and
Legacy FIFO engine) is described in legacy RX FIFO structure . The CPU can read the received messages sequentially, in the
order they were received, by repeatedly reading a message buffer structure at the output of the Legacy FIFO.
IFLAG1[BUF5I] (Frames Available in Legacy RX FIFO) is set when at least one frame is available to be read from the Legacy FIFO.
If the corresponding mask bit enables it, an interrupt is generated. Upon receiving the interrupt, the CPU can read the message
(accessing the output of the Legacy FIFO as a message buffer) and Legacy RX FIFO Information (RXFIR) , then clear the interrupt.
If there are more messages in the Legacy FIFO, clearing the interrupt:
1. Updates the output of the Legacy FIFO with the next message.
2. Updates RXFIR with the attributes of that message.
3. Reissues the interrupt to the CPU.
Otherwise, the flag remains cleared. The output of the Legacy FIFO is valid only when IFLAG1[BUF5I] is set.
IFLAG1[BUF6I] ( Legacy RX FIFO Warning) is set when the Legacy RX FIFO receives a new message that increases the number
of unread messages from four to five. This change means that the Legacy RX FIFO is almost full. The flag remains set until the
CPU clears it.
IFLAG1[BUF7I] ( Legacy RX FIFO Overflow) is set when an incoming message is lost because the Legacy RX FIFO is full. The flag
is not set when the Legacy RX FIFO is full and a message buffer captures the message. The flag remains set until the CPU clears it.
Clearing one of the three flags above does not affect the state of the other two.
If an IFLAG flag is set and the corresponding mask bit is 1, an interrupt is generated.

---

*Page 2323*

CAN (FlexCAN)
A powerful filtering scheme is provided to accept only frames intended for the target application, reducing the interrupt servicing
workload. The filtering criteria are specified by programming a table of up to 128 32-bit registers, according to CTRL2[RFFN] . This
table can be configured to one of the following formats (see also legacy RX FIFO structure ):
• Format A: 128 Identifier Acceptance Filters (IDAFs) — extended or standard IDs including IDE and RTR
• Format B: 256 IDAFs — standard IDs or extended 14-bit ID slices including IDE and RTR
• Format C: 512 IDAFs — standard or extended 8-bit ID slices
NOTE
A chosen format is applied to all entries of the filter table. It is not possible to mix formats within the table.
Every frame available in the Legacy RX FIFO has a corresponding Identifier Acceptance Filter Hit Indicator (IDHIT) . The IDHIT
can be read in the IDHIT field in the Control and Status word, as shown in the Legacy RX FIFO Structure description . The CPU can
also obtain this information by accessing Legacy RX FIFO Information (RXFIR) . RXFIR[IDHIT] refers to the message at the output
of the Legacy FIFO, and is valid when IFLAG1[BUF5I] is set. RXFIR must be read only before clearing the flag, guaranteeing that
the information refers to the correct frame within the Legacy FIFO.
The Individual Mask Registers (RXIMR n ) individually affect up to 32 elements of the filter table, according to the value of
CTRL2[RFFN]. This configuration allows very powerful filtering criteria to be defined. If MCR[IRMQ] is 0, the Legacy RX FIFO filter
table is affected by Legacy RX FIFO Global Mask (RXFGMASK) .
NOTE
See Table 419 for information about the difference between FD and non-FD regarding this feature.
70.3.9.1 Legacy RX FIFO in DMA operation
The receive-only Legacy FIFO can support DMA. To enable this feature, write 1 to both MCR[RFEN] and MCR[DMA] . To maintain
backward compatibility with previous versions of the module that did not have the DMA feature, the reset value of MCR[DMA]
is zero.
The DMA controller can read the received message by reading a message buffer structure at the Legacy FIFO output port in the
80h–8Ch address range.
NOTE
FlexCAN supports 32-bit access only for DMA transfers.
When MCR[DMA] = 1, the CPU must not access the Legacy FIFO output port address range. Before writing 1 to MCR[DMA], the
CPU must service the IFLAG flags set in the Legacy RX FIFO region. Otherwise, these flags may indicate that the FIFO has data
to be serviced, and mistakenly generate a DMA request. Before writing 0 to MCR[DMA], the CPU must perform a clear Legacy
FIFO operation.
When at least one frame available to be read from the FIFO, IFLAG1[BUF5I] (Frames available in Legacy RX FIFO) is set. A DMA
request is generated simultaneously. Upon receiving the request, the DMA controller can read the message (accessing the output
of the Legacy FIFO as a message buffer). The DMA reading process must end by reading address 8Ch. This read operation:
• Clears IFLAG1[BUF5I].
• Updates the FIFO output with the next message (if the FIFO is not empty).
• Updates Legacy RX FIFO Information (RXFIR) with the attributes of the new message.
If there are more messages stored in the FIFO, IFLAG1[BUF5I] is reasserted and another DMA request is issued. Otherwise, the
flag remains cleared.
NOTE
RXFIR contents cannot be read after DMA completes the Legacy FIFO read. The IDHIT information is also
available in the Control and Status word at address 080h (see legacy RX FIFO structure ).
IFLAG1[BUF6I] and IFLAG1[BUF7I] are not used when the DMA feature is enabled.

---

*Page 2324*

CAN (FlexCAN)
When FlexCAN is working with DMA, the CPU does not receive any Legacy RX FIFO interruption and must not clear the related
IFLAG flags. The related IMASK bits are not used to mask the generation of DMA requests.
NOTE
See Table 419 for information about the difference between FD and non-FD regarding this feature.
70.3.9.2 Clear legacy FIFO
When MCR[RFEN] = 1, you can use the clear Legacy FIFO operation to empty Legacy FIFO contents. When the CPU writes 1
to IFLAG1[BUF0I] , the clear FIFO operation occurs. This operation can only be performed in Freeze mode; FlexCAN blocks it in
other modes. This operation does not clear the FIFO IFLAG flags; the CPU must service all FIFO IFLAG flags before executing
the clear FIFO operation.
When Legacy RX FIFO is working with DMA, the clear FIFO operation clears IFLAG1[BUF5I] , and the DMA request is canceled.
CAUTION
The clear Legacy FIFO operation does not clear IFLAG flags , except when MCR[DMA] = 1; in this case, only
IFLAG1[BUF5I] is cleared .

#### 70.3.10 CAN protocol-related features

70.3.10.1 CAN FD ISO compliance
The CAN FD protocol has been improved to increase the failure-detection capability that was in the original CAN FD protocol. This
original protocol is also called non-ISO CAN FD, by CAN in Automation (CiA). A three-bit stuff counter and a parity bit have been
introduced in the improved CAN FD protocol, now called ISO CAN FD. The CRC calculation has also been modified. All these
improvements make the ISO CAN FD protocol incompatible with the non-ISO CAN FD protocol. FlexCAN still supports non-ISO
CAN FD, so it can be used during an intermediate phase, for evaluation and development purposes.
It is recommended that you configure FlexCAN with the ISO CAN FD protocol by writing 1 to CTRL2[ISOCANFDEN] .
70.3.10.2 CAN FD frames
ISO 11898-1:2015 specifies the Classical Frame format compliant to ISO 11898-1:2003 (2003) and introduces the CAN Flexible
Data Rate Frame format (CAN FD). The Classical Frame format allows bit rates up to one Mbit/s and payloads up to eight bytes
per frame. The Flexible Data Rate Frame format allows bit rates faster than one Mbit/s and payloads longer than eight bytes per
frame. FlexCAN can receive and transmit CAN FD messages interleaved with Classical CAN messages.
There are additional control bits in the CAN FD frame:
• The Extended Data Length (EDL) bit enables a longer data payload with different data length coding.
• The Bit Rate Switch (BRS) bit decides whether the bit rate is switched inside a CAN FD format frame.
• The Error State Indicator (ESI) flag is transmitted dominant by error active nodes, and recessive by error passive nodes.
There are no Remote Frames (see Remote frames ) in the CAN FD format. A message configured to transmit a Remote Frame
is always sent out in Classical CAN format. When an FD frame is received and matches a message buffer, the RTR bit in the
receiving message buffer becomes 0. The RTR bit must be considered in classical frames only.
70.3.10.2.1 CAN FD messages
CAN FD messages may be formatted as long frames where the data field exceeds eight bytes, and may range from 12 up to
64 bytes. They can also be configured to support bit rate switching. In this case, the control field, data field, and CRC field of a
CAN frame are transmitted with a higher bit rate than the beginning and end of the frame. Messages in the classical CAN format
are limited to transport a maximum payload of eight bytes at nominal rate. Figure 372 illustrates the message formats for classical
and FD frames with either standard or extended ID.

---

*Page 2325*

CAN (FlexCAN)
CAN standard format
S R I
R
O ID STANDARD T D DLC
0
F R E
Arbitration field Control field
CAN FD standard format
Arbitration phase Data phase
S R I E r B E
O ID STANDARD R D D e R S DLC
F S E L s S I
Arbitration field Control field
CAN extended format
S S I R
R R
O ID STANDARD R D ID EXTENDED T DLC
1 0
F R E R
Arbitration field Control field
CAN FD extended format
Arbitration phase Data phase
S S I R E r B E
O ID STANDARD R D ID EXTENDED R D e R S DLC
F R E S L s S I
Arbitration field Control field
Figure 372. CAN message formats
MCR[FDEN] enables the ability to receive and transmit CAN FD messages. A recessive R0 bit in CAN frames with 11-bit
identifiers, or a recessive R1 bit in CAN frames with 29-bit identifiers, is decoded as an EDL bit (not a reserved one). A recessive
EDL bit identifies a CAN FD frame, and a dominant EDL bit identifies a Classical CAN frame. The BRS bit specifies whether this
frame switches the bit rate in its data phase. A long frame is decoded according to the DLC field value (see DLC definition in
Message buffer structure ).
CAN FD messages can be transmitted with two different bit rates. The first part of a CAN FD frame, from the Start of Frame (SOF)
bit until the Bit Rate Switch (BRS) bit is called the arbitration phase. This part is transmitted with the nominal bit rate based on a
set of nominal CAN bit timing configuration values. The second part, from the BRS bit until the CRC Delimiter bit, is called the data
phase. When this second part is transmitted, a second set of CAN data bit timing configuration values determines the data bit rate.
Finally, from the CRC delimiter until the Intermission bits, the transmission returns to nominal bit rate.
70.3.10.2.2 BRS in CAN FD
In CAN FD frames with bit rate switching, the bit timing changes inside the frame at the sample point of the BRS bit if this bit is
recessive. Before the BRS bit, in the CAN FD arbitration phase, the nominal CAN bit timing is used as defined by CAN Bit Timing
(CBT) . ( Control 1 (CTRL1) also defines this timing for backward compatibility.) Upon detecting a recessive BRS bit, the CAN data
bit timing is used as defined by CAN FD Bit Timing (FDCBT) .

---

*Page 2326*

CAN (FlexCAN)
NOTE
If the time quantum length in nominal bit timing and in the data bit timing are not identical, a quantization error
of up to one time quantum of the arbitration phase may be present as a phase error. This situation can occur
after the switch from arbitration to data phase, and it lasts until the next synchronization event. The length of the
time quantum should be the same in nominal and data bit timing. This configuration minimizes the chance of error
frames on the CAN bus, and optimizes the clock tolerance in networks that use FD frames.
If BRS = 1 in the selected TX MB, FDCTRL[FDRATE] enables the transmission of all frames with bit rate switching. If
FDCTRL[FDRATE] = 0, the transmission is performed at nominal rate regardless of the BRS bit value. FDCTRL[FDRATE] can
be written at any time but takes effect only for the next message transmitted or received.
Nominal bit timing is resumed at the sample point of the CRC Delimiter bit or when an error is detected, whichever occurs first.
Figure 373 describes the mechanism for entering and leaving the data phase when the BRS bit is recessive.
CAN FD frame
Arbitration phase Data phase Arbitration phase
C A
R C
S B A S
C K
O ID R DATA CRC C EOF O
D D
F S K F
E E
L L
Arbitration field Control field Data field CRC field
BRS bit CRC delimiter bit
S S
Y Y
PROPSEG PSEG1 FPSEG2 FPROPSEG FPSEG1 PSEG2
N N
C C
Sample point Sample point
Nominal bit time Data bit time
S S
Y Y
PROPSEG PSEG1 PSEG2 FPROPSEG FPSEG1 FPSEG2
N N
C C
Sample point Sample point
Figure 373. Bit rate switching mechanism for CAN FD messages

---

*Page 2327*

CAN (FlexCAN)
NOTE
In Classical CAN frames, the CRC delimiter is one recessive bit . In CAN FD frames, the CRC delimiter may consist
of one or two recessive bits. FlexCAN sends only one recessive bit as the CRC delimiter. It accepts two recessive
bits before the recessive-to-dominant edge that starts the acknowledge slot. As a receiver, FlexCAN sends its
acknowledge bit after the first CRC delimiter bit. In CAN FD frames, FlexCAN accepts a two-bit dominant ACK slot
as a valid ACK to compensate for phase shifts between the receivers.
The maximum configurable bit rate in the CAN FD data phase depends on the clock frequency of the CAN_PE subblock. For
example, for a CAN_PE clock frequency of 40 MHz and the shortest configurable bit time of 5 time quanta, the bit rate in the data
phase is 8 Mbit/s.
NOTE
The frequency used in this example may not be supported on this chip. It is shown only to demonstrate how the
maximum configurable bit rate is calculated.
70.3.10.2.3 ESI in CAN FD
The value of the ESI field is determined by either of the following:
• The error state of the transmitter at the start of the transmission, if the frame is originated in the FlexCAN node
• The original transmitting node when FlexCAN is acting as a gateway for the message
If the transmitter is error-passive, ESI is transmitted recessive; otherwise, it is transmitted dominant. The permutations of the
relationship between the written value and the transmitted value of the ESI are shown in Table 431 .
Table 431. Written versus transmitted values of the ESI field
FlexCAN fault confinement status at ESI field of TX MB Transmitted ESI
start of frame
Error active 0 0 (Error Active)
Error passive 0 1 (Error Passive)
Error active 1 1 (Error Passive)
Error passive 1 1 (Error Passive)
70.3.10.2.4 CRC calculations in CAN FD
Different CAN frame formats have different CRC polynomials. The first polynomial, CRC_15, is used for all frames in Classical
CAN format. The second, CRC_17, is used for frames in CAN FD format with a data field up to 16 bytes long. The third, CRC_21,
is used for frames in CAN FD format with a data field longer than 16 bytes. Each polynomial results in a Hamming distance of 6.
At the start of the frame, all three CRC polynomials are calculated concurrently. The values of the EDL bit and the DLC field select
the CRC sequence to be transmitted. When receiving a message, FlexCAN decodes EDL and DLC to select the adequate CRC
polynomial to check for a CRC error.
In CAN FD format frames, stuff bits are included in the bit stream for CRC calculation. In Classical CAN format frames, stuff bits are
not included. After the transmission of the last bit relevant to the CRC calculation, CAN FD CRC (FDCRC) stores the calculated
CRC for the transmitted message. This storage is performed with adequate length for the type of message, for CAN FD and
non-FD messages. Cyclic Redundancy Check (CRCR) reports a valid CRC for Classical CAN messages only.
In CAN FD format frames, the CAN bit stuffing method changes for the CRC sequence, so the stuff bits are inserted at fixed
positions. When FlexCAN is transmitting a CAN FD frame, a fixed stuff bit is inserted just before the first bit of the CRC sequence.
This insertion occurs even if the last bits of the preceding field do not fulfill the CAN stuff condition. Additional stuff bits are inserted
after each fourth bit of the CRC sequence. The value of any fixed stuff bit is the inverse value of its preceding bit. When FlexCAN
receives a CAN FD frame, it discards the fixed stuff bits from the bit stream for the CRC check. A stuff error is detected if the fixed
stuff bit has the same value as its preceding bit.

---

*Page 2328*

CAN (FlexCAN)
70.3.10.2.5 CAN FD errors
FlexCAN detects errors in CAN FD frames the same way as in Classical CAN frames. The error counters ECR[RXERRCNT] and
ECR[TXERRCNT] accumulate the counts of RX and TX errors, respectively, for both FD and non-FD frames indiscriminately. Two
extra error counters, ECR[RXERRCNT_FAST] and ECR[TXERRCNT_FAST] , accumulate RX and TX errors occurring in the data
phase of CAN FD frames with BRS = 1 only. The rules for updating the error counters are the same for both CAN FD and non-FD
frames (see Error Counter (ECR) ).
These error flags report errors in both CAN FD and non-FD frames:
• ESR1[BIT1ERR]
• ESR1[BIT0ERR]
• ESR1[ACKERR]
• ESR1[CRCERR]
• ESR1[FRMERR]
• ESR1[STFERR]
If CTRL1[ERRMSK] = 1, they also generate the ERRINT interrupt.
These additional error flags indicate the occurrence of errors in the data phase of CAN FD frames with BRS = 1:
• ESR1[BIT1ERR_FAST]
• ESR1[BIT0ERR_FAST]
• ESR1[CRCERR_FAST]
• ESR1[FRMERR_FAST]
• ESR1[STFERR_FAST]
No ACKERR is detected in the data phase of a CAN FD frame. Fault confinement status reported in ESR1[FLTCONF] is the
same for both CAN FD and Classical CAN frames, and is based on ECR[RXERRCNT] and ECR[TXERRCNT] only. Information
in ECR[RXERRCNT_FAST] and ECR[TXERRCNT_FAST] may be considered as status to help detect the error nature related to
the bit rate value.
When FlexCAN detects an error while transmitting or receiving a CAN FD message in the data phase, it immediately switches:
• Back to the arbitration phase, and
• Back to the nominal rate to start an error flag.
70.3.10.2.6 CAN FD synchronization
Resynchronization and hard synchronization occur in CAN FD frames in the same way as in Classical CAN ones. A hard
synchronization is also performed at the recessive-to-dominant edge from EDL to R0 in CAN FD format frames. FlexCAN does
not resynchronize when transmitting in the CAN FD data phase.
70.3.10.3 Transceiver delay compensation
The CAN FD protocol allows the transmission and reception of data at a higher bit rate than the nominal rate used in the arbitration
phase, when BRS = 1 in the message. This feature enables the use of rates up to 8 Mbit/s.
During the data phase of a CAN FD frame, if the transmitter cannot receive its own latest transmitted bit at the sample point of that
bit, it detects a bit error. When bit rate switching is enabled (BRS = 1), the CAN bit time in the data phase can become shorter than
the loop delay of the transceiver. This condition impedes the correct comparison between the transmitted bit and the received bit
within the current CAN bit time interval.
The transceiver delay compensation (TDC) process defines a secondary sample point where the transmitted bit is correctly
compared to the received bit to check for bit errors.

---

*Page 2329*

CAN (FlexCAN)
You can enable the TDC mechanism via FDCTRL[TDCEN] or ETDC[ETDCEN] . The TDC mechanism is effective only during the
data phase of FD frames with BRS = 1. It has no effect on either non-FD frames or FD frames transmitted at the normal bit rate.
When the transmitted message has BRS = 1, TDC is active from the sample point of the BRS bit until the sample point of the CRC
Delimiter bit. When TDC is active, the real received bit is compared to the delayed transmitted bit, where the delay is calculated
based on the measured transceiver loop delay.
NOTE
The transmitters using TDC disregard the value of the CRC delimiter bit. A global error at the end of the CRC field
causes the receivers to send error frames that the transmitter detects during Acknowledge or End of Frame.
For every transmitted FD frame with BRS = 1, the transition from the recessive EDL bit to the dominant R0 bit triggers the delay
measurement (as shown in Figure 374 ). The loop delay is measured in Protocol Engine (PE) clock periods (CANCLK, see Protocol
timing ), from the transmitted EDL-R0 edge to the received EDL-R0 edge. The measured loop delay time added to an offset value
specified in FDCTRL[TDCOFF] or ETDC[ETDCOFF] determines the position of the secondary sample point. FDCTRL[TDCVAL]
or ETDC[ETDCVAL] stores the result of this calculation. The TDCVAL and ETDCVAL value saturates at its maximum value of 63
CANCLK and 255 CANCLK when the delay measurement is too long.
Arbitration phase Data phase
E r B E
TX output D e R S DLC
L s S I
Loop delay Rate switch point
E r B E
RX input D e R S DLC
L s S I
Arbitration phase Data phase
Figure 374. Transceiver loop delay measurement
The measured loop delay is not enough to define the secondary sample point, because it relates to the CAN bit edges. The
transceiver delay compensation offset FDCTRL[TDCOFF] or ETDC[ETDCOFF] is used to shift the secondary sample point to an
intermediate point inside the bit time, far away from its edges. The value of FDCTRL[TDCOFF] or ETDC[ETDCOFF] cannot be
larger than the CAN bit duration in the data phase.
If the secondary sample point is set very near the CAN bit edge (SYNC field), problems may occur during the bit sampling in the
data phase. For the TDC to work reliably, the offset must use optimal settings. To ensure that bit sampling is performed in the best
region, configure the TDC offset as shown in this equation:
Offset = ( FPSEG1 + FPROPSEG + 2) × ( FPRESDIV + 1)
or
Offset = ( DTSEG1 + 2) × ( EDPRESDIV + 1), if ETDCEN
Equation 12. TDC offset calculation
Figure 375 shows the SSP position when these settings are used.

---

*Page 2330*

CAN (FlexCAN)
TX output
SSP
RX input
TDC measurement TDC offset
SSP position
Figure 375. SSP position with optimal values
Alternatively, if CTRL2[BTE] and ETDC[ETDCEN] are 1, you can write 1 to ETDC[TDMDIS] to disable the transceiver delay
measurement. In this case, only ETDC[ETDCOFF] defines the SSP position. Figure 376 shows the secondary sample point
position when the transceiver delay measurement is disabled.
TX output
SSP
RX input
TDC offset
SSP position
Figure 376. SSP position with TDC measurement disabled by ETDCEN and TDMDIS
During the data phase of CAN FD frames with bit rate switching enabled, at the onset of every TX CAN bit:
• The transmitted TX bit value is temporarily stored in a buffer.
• A time countdown based on FDCTRL[TDCVAL] or ETDC[ETDCVAL] is started. This countdown ends with the comparison
of the received RX bit (delayed by the external loop delay plus the specified offset) to the stored TX bit.
If a bit error is detected at the secondary sample point, FlexCAN issues an error flag to the CAN bus at the next sample point.
During the arbitration phase, delay compensation is always disabled. During the data phase, the TDC mechanism of FlexCAN can
compensate a maximum delay of 3 CAN bit times − 2 Tq. Beyond this limit, the FDCTRL[TDCFAIL] or ETDC[ETDCFAIL] flag is
set. The flag indicates when the TDC mechanism is out of range and is unable to compensate the transceiver loop delay.
70.3.10.4 Remote frames
A remote frame is a special type of frame. You can program a message buffer to be a remote request frame by configuring the
message buffer as Transmit with the RTR = 1. After the remote request frame is transmitted successfully, the message buffer
becomes a receive message buffer, with the same ID as before.
When FlexCAN receives a remote request frame, the frame can be treated in different ways, depending on remote request storing
( CTRL2[RRS] ) and RX FIFO Enable ( MCR[RFEN] ):
• If RRS = 0, the ID of the frame is compared to the IDs of the transmit message buffers with the CODE field 1010b. If a matching
ID exists, this message buffer frame is transmitted. If the matching message buffer has the RTR = 1, FlexCAN transmits a
remote frame as a response. The received remote request frame is not stored in a receive buffer. It is only used to trigger a
transmission of a frame in response.
The mask registers are not used in remote frame matching, and all ID bits (except RTR) of the incoming received frame should
match. If a remote request frame is received and matches a message buffer, this message buffer immediately enters the
internal arbitration process. However, it is considered a normal TX message buffer, with no higher priority. The data length
of this frame is independent of the DLC field in the remote frame that initiated its transmission.

---

*Page 2331*

CAN (FlexCAN)
• If CTRL2[RRS] = 1, the ID of the frame is compared to the IDs of the receive message buffers with the CODE field 0100b,
0010b, or 0110b. If a matching ID exists, this message buffer stores the remote frame in the same fashion as a data frame.
No automatic remote response frame is generated. The mask registers are used in the matching process.
• If MCR[RFEN] = 1, FlexCAN does not generate an automatic response for remote request frames that match the Legacy FIFO
filtering criteria. If the remote frame matches one of the target IDs, it is stored in the Legacy FIFO and presented to the CPU.
For filtering formats A and B (see legacy RX FIFO structure ), it is possible to select whether remote frames are accepted or
not. For format C, remote frames are always accepted if they match the ID. Remote request frames are considered as normal
frames. They generate a Legacy FIFO overflow when a successful reception occurs and the Legacy FIFO is already full.
• If ERFCR[ERFEN] = 1, FlexCAN does not generate an automatic response for remote request frames that match the
Enhanced RX FIFO filtering criteria. Remote Request Frames are considered normal frames. They generate an Enhanced
RX FIFO overflow when a successful reception occurs and the enhanced RX FIFO is already full.
NOTE
There is no remote frame in the CAN FD format. A fixed dominant RRS bit replaces the RTR bit. FlexCAN receives
and transmits remote frames in the Classical CAN format.
70.3.10.5 Overload frames
When a dominant bit is detected on the CAN bus in these locations, FlexCAN transmits overload frames:
• The first or second bit of Intermission.
• The seventh bit (last) of End of Frame field (RX frames).
• The eighth bit (last) of Error Frame Delimiter or Overload Frame Delimiter.
70.3.10.6 Message buffer timestamp
The value of the free-running timer is sampled at the beginning of the Identifier field on the CAN bus. This value is stored at the
end of move-in in the TIME_STAMP field of a message buffer , providing network behavior regarding time.
When CTRL2[TIMER_SRC] = 1, an external time tick continuously clocks the free-running timer.
When CTRL2[TIMER_SRC] = 0, the FlexCAN bit clock clocks the free-running timer, which defines the baud rate on the CAN bus.
During a message transmission or reception, it increments by one for each bit that is received or transmitted. When there is no
message on the bus, it counts using the previously programmed baud rate.
The free-running timer is not incremented during Disable and Freeze modes. It can be reset upon a specific frame reception,
enabling network time synchronization. See CTRL1[TSYN] .
Alternatively, by configuring CTRL2[MBTSBASE] , the timestamp of the message buffer can capture the lower or higher 16 bits of
the high-resolution dedicated counter. See the chip-specific FlexCAN timestamp implementation.
70.3.10.7 High-resolution timestamp
The high-resolution timestamp (HR_TIME_STAMP) uses a dedicated timer with a 32-bit counter operating in free-running mode.
CTRL2[TSTAMPCAP] enables the high-resolution timestamp. When this field is not zero, the dedicated 32-bit counter value is
captured during a valid CAN frame and stored in an HR_TIME_STAMP n register.
Each HR_TIME_STAMP n corresponds to a specific message buffer. For example, HR_TIME_STAMP0 stores the 32-bit
timestamp associated with message buffer 0. HR_TIME_STAMP1 stores the 32-bit timestamp associated with message buffer 1,
and so on.
The counter value is captured according to CTRL2[TSTAMPCAP]. For classical CAN frames, the capture points can be the start
of frame bit or the point in time a CAN frame is considered valid. This valid point is the seventh bit of end of frame for transmission
and the sixth bit of end of frame for reception.
For CAN FD frames, the capture points can be:
• The start of frame

---

*Page 2332*

CAN (FlexCAN)
• The point in time a CAN FD frame is considered valid
• The res bit of a CAN FD frame
The 16-bit timestamp of the message buffer can be configured to capture the lower or higher 16 bits of the high-resolution timer.
This configuration is made by CTRL2[MBTSBASE] . See the chip-specific FlexCAN timestamp implementation.
70.3.10.8 Protocol timing
Figure 377 shows the structure of the clock generation circuitry that feeds the CAN Protocol Engine (PE) submodule.
NOTE
To identify the proper clock source, see the clock distribution chapter (module clocks table).
Oscillator clock CANCLK Sclock
Prescaler
(Tq)
Figure 377. CAN engine clocking scheme
70.3.10.8.1 Bit timing configuration
FlexCAN supports various means to configure bit timing parameters required by the CAN protocol. Control 1 (CTRL1) has various
fields to control bit timing parameters:
• CTRL1[PRESDIV]
• CTRL1[PROPSEG]
• CTRL1[PSEG1]
• CTRL1[PSEG2]
• CTRL1[RJW]
CAN Bit Timing (CBT) extends the range of the CAN bit timing variables in CTRL1. Enhanced Data Phase CAN Bit Timing
(EDCBT) provides a second set of CAN bit timing variables to be applied at the data phase of CAN FD frames with the Bit Rate
Switch (BRS) = 1.
Enhanced Nominal CAN Bit Timing (ENCBT) extends the range of CAN bit timing variables in CBT. Enhanced Nominal CAN Bit
Timing (ENCBT) extends the range of CAN bit timing variables in FDCBT. When using ENCBT and EDCBT , you must program
the nominal bit timing and data phase serial clock ( Sclock ) dividers in Enhanced CAN Bit Timing Prescalers (EPRS) .
NOTE
When the CAN FD feature is enabled, always write 1 to CBT[BTF] or CTRL2[BTE] and specify the CAN bit timing
variables in CBT or ENCBT . See CAN Bit Timing (CBT) or Enhanced Nominal CAN Bit Timing (ENCBT) .
CTRL1[PRESDIV] , and its extended range CBT[EPRESDIV] (or EPRS[ENPRESDIV] ) and FDCBT[FPRESDIV] (or
EPRS[EDPRESDIV] ) for the data phase bits of CAN FD messages , defines the prescaler value that generates the serial
clock (Sclock). (See Equation 13 .) The period of Sclock defines the time quantum used to compose the CAN waveform. A time
quantum (Tq) is the atomic unit of time managed by the CAN engine. It is the smallest time unit for all configuration values.
( PRESDIV + 1)
Tq =
f CANCLK
Equation 13. Time quantum
The bit rate, which defines the rate the CAN message is received or transmitted, is calculated with the formula:

---

*Page 2333*

CAN (FlexCAN)
CAN bit time = ( Number of time quanta in 1 bit time ) × Tq
1
Bit rate =
CAN bit time
Equation 14. CAN bit time and baud rate
70.3.10.8.2 Bit time segments
A bit time is subdivided into three segments as shown in Figure 378 . See also Figure 379 , Figure 380 , and Table 432 .
NOTE
For further explanation of the underlying concepts, see ISO 11898-1:2015. See also CAN Specification Version
2.0, Part A and Part B for bit timing.
NRZ signal
Time segment 1 Time segment 2
SYNC_SEG
(NTSEG1 + 1) (NTSEG2 + 1)
1 2 ... 256 2 ... 128
8 ... 385 time quanta
= 1 bit time
Tq Tq
Transmit point Sample point
(single or triple sampling)
NRZ signal
Time segment 1 Time segment 2
SYNC_SEG
(DTSEG1 + 1) (DTSEG2 + 1)
1 2 ... 32 2 ... 16
5 ... 49 time quanta
= 1 bit time
Transmit point Sample point
(single sampling)
Figure 378. Segments within the bit timing (example using ENCBT and EDCBT bit timing variables)
The three bit time segments are:

---

*Page 2334*

CAN (FlexCAN)
• SYNC_SEG—this segment has a fixed length of one time quantum. Signal edges are expected to occur within this
section.
• Time Segment 1—this segment includes the propagation segment and the phase segment 1 of the CAN standard.
It can be programmed by configuring CTRL1[PROPSEG] and CTRL1[PSEG1] so that the sum (plus 2) is 2–16 time quanta.
When CBT[BTF] = 1, FlexCAN uses CBT[EPROPSEG] and CBT[EPSEG1] so that the sum (plus 2) is 2–96 time quanta. For
messages in CAN FD format with the BRS = 1, FlexCAN uses FDCBT[FPROPSEG] and FDCBT[FPSEG1] so that the sum
(plus 1) is 2–39 time quanta.
If CTRL2[BTE] = 1, FlexCAN uses ENCBT[NTSEG1] to configure time segment 1 to 2–256 time quanta. For the data phase
in CAN FD messages with BRS = 1, EDCBT[DTSEG1] must be used for configuring time segment 1 to 2–32 time quanta .
• Time Segment 2—this segment represents the phase segment 2 of the CAN standard.
It can be programmed by configuring CTRL1[PSEG2] (plus 1) to be 2–8 time quanta. When CBT[BTF] = 1, FlexCAN uses
CBT[EPSEG2] so that its value (plus 1) is 2–32 time quanta. For messages in CAN FD format with the BRS = 1, FlexCAN uses
FDCBT[FPSEG2] instead, so that its value (plus 1) is 2–8 time quanta. Time segment 2 cannot be smaller than the Information
Processing Time (IPT) , which is 2 time quanta in FlexCAN.
If CTRL2[BTE] = 1, FlexCAN uses ENCBT[NTSEG2] to configure time segment 2 to 2–128 time quanta. For the data phase
in CAN FD messages with BRS = 1, EDCBT[DTSEG2] must configure time segment 2 to 2–16 time quanta .
NOTE
The bit time defined by the above time segments must not be smaller than five time quanta. For bit time calculations,
use an Information Processing Time (IPT) of two, which is the value implemented in the FlexCAN module.
NRZ signal
Time segment 1 Time segment 2
SYNC_SEG
(PROPSEG + PSEG1 + 2) (PSEG2 + 1)
1 2 ... 16 2 ... 8
8 ... 25 time quanta
= 1 bit time
Tq Tq
Transmit point Sample point
(single or triple sampling)
Figure 379. Segments within the bit time (example using CTRL1 bit timing variables for Classical CAN format)

---

*Page 2335*

CAN (FlexCAN)
NRZ signal
Time segment 1 Time segment 2
SYNC_SEG
(EPROPSEG + EPSEG1 + 2) (EPSEG2 + 1)
1 2 ... 96 2 ... 32
8 ... 129 time quanta
= 1 bit time
Tq Tq
Transmit point Sample point
(single or triple sampling)
NRZ signal
Time segment 1 Time segment 2
SYNC_SEG
(FPROPSEG + FPSEG1 + 1) (FPSEG2 + 1)
1 2 ... 39 2 ... 8
5 ... 48 time quanta
= 1 bit time
Transmit point Sample point
(single sampling)
Figure 380. Segments within the bit time (example using CBT and FDCBT bit timing variables for CAN FD format)
Table 432. Time segment syntax
Syntax Description
SYNC_SEG Period during which the system expects transitions to occur on the bus
TSEG1 Period corresponding to the sum of PROPSEG and PSEG1
TSEG2 Period corresponding to the PSEG2 value
Transmit point Point at which a node in Transmit mode transfers a new value to the CAN bus
Sample point Point at which a node samples the bus. If the option of three samples per bit is selected, this point
marks the position of the third sample.
Table 433 gives some examples of the CAN-compliant segment settings for Classical CAN format (Bosch CAN 2.0B) (non-
FD) messages.

---

*Page 2336*

CAN (FlexCAN)
Table 433. Bosch CAN 2.0B standard compliant bit time segment settings
Time segment 1 Time segment 2 Resynchronization jump width
5 to 10 2 1 to 2
4 to 11 3 1 to 3
5 to 12 4 1 to 4
6 to 13 5 1 to 4
7 to 14 6 1 to 4
8 to 15 7 1 to 4
9 to 16 8 1 to 4
NOTE
You must ensure the bit time settings comply with the CAN Protocol standard (ISO 11898-1:2015).
70.3.10.8.3 Calculating peripheral clocks
A CAN bit can be used as a measure of duration (for example, estimating the occurrence of a CAN bit event in a message). When
a CAN bit is used in this way, the number of peripheral clocks in one CAN bit (NumClkBit) can be calculated as:
f SYS
NumClkBit = × ( PRESDIV + 1) × ( PROPSEG + PSEG1 + PSEG2 + 4)
f CANCLK
Equation 15. Number of peripheral clocks per CAN bit when CTRL2[BTE] = 0
Or, if CTRL2[BTE] = 1:
f SYS
NumClkBit = × ( ENPRESDIV + 1) × ( NTSEG1 + NTSEG2 + 3)
f CANCLK
Equation 16. Number of peripheral clocks per CAN bit when CTRL2[BTE] = 1
Where:
• NumClkBit is the number of peripheral clocks in one CAN bit.
• f CANCLK is the Protocol Engine (PE) Clock (see Figure 377 ), in Hz.
• f SYS is the frequency of operation of the system (CHI) clock, in Hz.
• PSEG1 is the value of CTRL1[PSEG1].
• PSEG2 is the value of CTRL1[PSEG2].
• PROPSEG is the value of CTRL1[PROPSEG].
• PRESDIV is the value in CTRL1[PRESDIV].
• ENPRESDIV is the value of EPRS[ENPRESDIV].
• NTSEG1 is the value of ENCBT[NTSEG1].
• NTSEG2 is the value of ENCBT[NTSEG2].
The formula above is also applicable to the alternative CAN bit timing variables described in:
• CAN Bit Timing (CBT)

---

*Page 2337*

CAN (FlexCAN)
• Enhanced Nominal CAN Bit Timing (ENCBT)
• CAN FD Bit Timing (FDCBT)
• Enhanced Nominal CAN Bit Timing (ENCBT)
For example, 180 CAN bits = (180 × NumClkBit) peripheral clock periods.
70.3.10.9 Arbitration and matching timing
During normal reception and transmission, the matching, arbitration, move-in, and move-out processes are executed during
certain time windows inside the CAN frame. These windows are shown in the following figures.
Start move
(bit 2)
DLC DATA, CRC, or both EOF Intermission
Move-in
Matching window (26 to 90 CAN bits) window
Figure 381. Matching and move-in time windows
Arbitration
Start arbitration process Start move
(delayed by TASD) (bit 1)
CRC EOF Intermission
Move-out
window
Arbitration window (25 CAN bits)
Figure 382. Arbitration and move-out time windows
BusOff
... ...
0 1 2 3 123 124 125 126 128
TASD Move-out
count window
Internal counter
counting 128 occurrences of 11 recessive CAN bits
Arbitration
process
Figure 383. Arbitration at the end of bus off and move-out time windows
NOTE
In these figures, the matching and arbitration timing do not consider delays caused by concurrent memory access
due to the CPU or other internal FlexCAN subblocks.
70.3.10.10 TX arbitration start delay
TX Arbitration Start Delay ( CTRL2[TASD] ) indicates the number of CAN bits that FlexCAN uses to delay the TX arbitration process
starting point from the first bit of the CRC field of the current frame. This variable can be written only in Freeze mode; FlexCAN
blocks it in other modes.
The ability of the CPU to reconfigure message buffers for transmission after the end of the internal arbitration process impacts
transmission performance. In the arbitration process, FlexCAN finds the winner MB for transmission (see Arbitration process ). If

---

*Page 2338*

CAN (FlexCAN)
the arbitration ends too early (before the first bit of the Intermission field) the CPU may reconfigure some TX message buffers. It
is possible that the winning message buffer is no longer the best candidate to be transmitted.
TASD can optimize the transmission performance by defining the arbitration start point, as shown in Figure 384 , based on factors
such as:
• Peripheral-to-oscillator clock ratio
• CAN bit timing variables that determine the CAN bit rate
• Number of message buffers in use by the matching and arbitration processes
Data bit rate when CAN FD is enabled Nominal bit rate
9 last CAN bits
Arbitration
start point
A S
C O
CRC K EOF Intermission F
Arbitration
Process
TASD countdown
Arbitration window (25 to 31 CAN bits)
Figure 384. Optimal TX arbitration start point
The duration of an arbitration process, in terms of CAN bits, is:
• Directly proportional to the number of available message buffers
• Directly proportional to the CAN bit rate
• Inversely proportional to the peripheral clock frequency
The optimal arbitration timing occurs when the last message buffer is scanned immediately before the first bit of the Intermission
field of a CAN frame. For instance, if the following are true:
• There are few message buffers.
• The peripheral-to-oscillator clock ratio is high.
• The CAN baud rate is low.
Then the arbitration can be placed closer to the end of the frame, adding more delay to its starting point, and vice versa.
If CTRL2[TASD] = 0, the arbitration start is not delayed, and more time is reserved for arbitration. Alternatively, if CTRL2[TASD] is
close to 24, the CPU can configure a TX message buffer later, and less time is reserved for arbitration. If too little time is reserved
for arbitration, FlexCAN may not be able to find a winner MB in time. The transmitted arbitration winner may not have the best
chance to win the bus arbitration against external nodes on the CAN bus.
The optimal TASD value can be calculated as follows:

---

*Page 2339*

CAN (FlexCAN)
For CAN FD frames and (MAXMB + 1) ≤ NMB END
2 * (MAXMB + 1 ) + 4
TASD = 31 -
CPCB N
For CAN FD frames and (MAXMB + 1) > NMB END
2 * (MAXMB + 1 ) - NMB END
TASD = 22 -
CPCB F
For non-FD frames
2 * ( MAXMB + 1 ) + 4
TASD = 25 -
CPCB
Equation 17. Optimal value for TASD
Where:
( 9 × CPCB N ) - 4
NMB END =
2
f CANCLK
BITRATE N =
[1 + ( EPSEG1 + 1) + ( EPSEG2 + 1) + ( EPROPSEG + 1) ] × ( EPRESDIV + 1)
f CANCLK
BITRATE F =
[1 + ( FPSEG1 + 1) + ( FPSEG2 + 1) + FPROPSEG ] × ( FPRESDIV + 1)
f SYS
CPCB N =
BITRATE N
f SYS
CPCB F =
BITRATE F
CPCB = CPCB N
Equation 18. Variables used in TASD calculation
• MAXMB is the value in MCR[MAXMB] .
• NMB END is the number of message buffers that the arbitration process can scan during the last nine CAN bits at the end of
a frame. (See Equation 18 .)
• BITRATE N is the CAN bit rate in bits per second calculated by the nominal CAN bit time variables.
• BITRATE F is the CAN bit rate in bits per second calculated by the data CAN bit time variables.
• CPCB N is the number of peripheral clocks per CAN bit in nominal bit rate for CAN FD frames.
• CPCB F is the number of peripheral clocks per CAN bit in data bit rate for CAN FD frames.
• CPCB is the number of peripheral clocks per CAN bit for non-FD frames.
• f CANCLK is the oscillator clock, in Hz.
• f SYS is the peripheral clock, in Hz.
• EPSEG1 is the value in CBT[EPSEG1] ( CTRL1[PSEG1] can also be used).
• EPSEG2 is the value in CBT[EPSEG2] ( CTRL1[PSEG2] can also be used).
• EPROPSEG is the value in CBT[EPROPSEG] ( CTRL1[PROPSEG] can also be used).
• EPRESDIV is the value in CBT[EPRESDIV] ( CTRL1[PRESDIV] can also be used).
• FPSEG1 is the value in FDCBT[FPSEG1] .

---

*Page 2340*

CAN (FlexCAN)
• FPSEG2 is the value in FDCBT[FPSEG2] .
• FPROPSEG is the value in FDCBT[FPROPSEG] .
• FPRESDIV is the value in FDCBT[FPRESDIV] .
• NTSEG1 is the value in ENCBT[NTSEG1] .
• NTSEG2 is the value in ENCBT[NTSEG2] .
• ENPRESDIV is the value in EPRS[ENPRESDIV] .
• DTSEG1 is the value in EDCBT[DTSEG1] .
• DTSEG2 is the value in EDCBT[DTSEG2] .
• EDPRESDIV is the value in EPRS[EDPRESDIV] .
If CTRL2[BTE] = 1, then:
f CANCLK
BITRATE N =
[1 + ( NTSEG1 + 1) + ( NTSEG2 + 1)] × ( ENPRESDIV + 1)
Equation 19. Nominal baud rate when CTRL2[BTE] = 1
f CANCLK
BITRATE F =
[1 + ( DTSEG1 + 1) + ( DTSEG2 + 1)] × ( EDPRESDIV + 1)
Equation 20. Fast baud rate when CTRL2[BTE] = 1
See also Protocol timing for more details.
70.3.10.10.1 TASD configuration examples
The following tables show the TASD value calculated for some configuration cases.
Case 1:
• Clock ratio = 2:1 (for example, peripheral clock 80 MHz and oscillator clock 40 MHz)
• Bit rate in arbitration phase = 1 Mbaud
Table 434. TASD values in Case 1
Number of message buffers TASD value Maximum bit rate in data phase (MBd)
16 24 Invalid
32 24 8.0
64 23 8.0
96 22 8.0
Case 2:
• Clock ratio = 1:1 (for example, peripheral clock 40 MHz and oscillator clock 40 MHz)
• Bit rate in arbitration phase = 1 Mbaud
Table 435. TASD values in Case 2
Number of message buffers TASD value Maximum bit rate in data phase (MBd)
16 24 Invalid
Table continues on the next page...

---

*Page 2341*

CAN (FlexCAN)
Table 435. TASD values in Case 2 (continued)
Number of message buffers TASD value Maximum bit rate in data phase (MBd)
32 23 6.67
54 22 5.0
64 21 3.33
96 20 1.6
Case 3:
• Clock ratio = 2:1 (for example, peripheral clock 40 MHz and oscillator clock 20 MHz)
• Bit rate in arbitration phase = 1 Mbaud
Table 436. TASD values in Case 3
Number of message buffers TASD value Maximum bit rate in data phase (MBd)
16 24 Invalid
32 23 4.0
54 22 4.0
64 21 3.33
96 20 1.54

#### 70.3.11 Clocks

The following table describes the clock sources for FlexCAN. See the chip clocking chapter for clock setting, configuration, and
gating information.
Table 437. FlexCAN clocks
Clock name Description
MODULE_CLK (system_clk) Peripheral clock
MODULE_CLK_CHI (host_clock) Control Host Interface (CHI) clock
MODULE_CLK_PE Protocol Engine (PE) clock
(protocol_engine_clock)
MODULE_CLK_PE_NOGATE Protocol Engine clock (no gating)
(protocol_engine_clock_nogate)
MODULE_CLK_S Peripheral access clock
(system_clock_nogate)
70.3.11.1 Clock domains and restrictions
FlexCAN has two clock domains asynchronous to each other:
• The bus domain feeds the Control Host Interface (CHI) submodule.
• The oscillator domain feeds the CAN Protocol Engine (PE) submodule.
When the two domains are connected to clocks with different frequencies or phases, the frequency relationship between the two
clock domains is restricted. In asynchronous operation, the bus domain clock frequency must always be greater than the oscillator
domain clock frequency.

---

*Page 2342*

CAN (FlexCAN)
NOTE
Asynchronous operation with a 1:1 ratio between peripheral and oscillator clocks is not allowed.
When performing matching and arbitration, FlexCAN must scan the whole message buffer memory during the time slot of one CAN
frame, comprised of a number of CAN bits. To provide sufficient time for the scan, observe the following requirements:
• The peripheral clock frequency cannot be less than the oscillator clock frequency.
• There must be a minimum number of peripheral clocks per CAN bit, as specified in Table 438 .
Table 438. Minimum number of peripheral clocks per CAN bit for Classical CAN format
Number of message buffers Value of MCR[RFEN] Value of ERFCR[ERFEN] Minimum number of
peripheral clocks per CAN bit
16 0 0 16
32 0 0 16
64 0 0 25
96 0 0 37
16 1 0 16
32 1 0 17
64 1 0 30
96 1 0 42
16 0 1 16
32 0 1 19
64 0 1 31
96 0 1 42
For classical frame format, the minimum number of peripheral clocks per CAN bit specified in Table 438 determines the minimum
peripheral clock frequency for a given number of message buffers and for an expected CAN bit rate. The CAN bit rate depends on
the number of time quanta in a CAN bit. This number can be defined by adjusting one or more of the bit timing values contained in:
• Control 1 (CTRL1)
• CAN Bit Timing (CBT)
• Enhanced Nominal CAN Bit Timing (ENCBT)
The time quantum (Tq) is defined in Protocol timing . The minimum number of time quanta per CAN bit must be eight, so the
oscillator clock frequency should be at least eight times the CAN bit rate.
70.3.11.1.1 Clock restrictions for CAN FD
For CAN FD frame format, some constraints must be satisfied. The equation below calculates the number of peripheral clocks per
CAN bit in nominal bit rate (NumClkNomBit).
f SYS
NumClkNomBit = × ( PRESDIV + 1) × ( PROPSEG + PSEG1 + PSEG2 + 4)
f CANCLK
f SY S
=
NomBitRate
Equation 21. Number of peripheral clocks per nominal CAN bit

---

*Page 2343*

CAN (FlexCAN)
Where PRESDIV, PSEG1, and PSEG2 are CAN bit time values in Control 1 (CTRL1) . Alternatively, EPRESDIV, EPSEG1, and
EPSEG2 values in CAN Bit Timing (CBT) or the values of EPRS[ENPRESDIV] , ENCBT[NTSEG1] , and ENCBT[NTSEG2] can be
used instead. NumClkNomBit can also be calculated as a function of the expected nominal bit rate used in the arbitration phase
(NomBitRate), as shown in the equation above.
The number of CAN bits in the data phase of an FD frame with BRS = 1 (fast CAN bits) depends on the number of data bytes in the
payload. The number of fast CAN bits (NumOfFastBits) can be determined in Table 439 . Having fewer data bytes means having
fewer fast CAN bits. It also means that less time is available for FlexCAN to scan the whole message buffer memory during the
internal matching and arbitration processes.
Table 439. Number of fast CAN bits in a CAN FD frame
Minimum number of data bytes DLC field NumOfFastBits
0 0h 21
1 1h 29
2 2h 37
3 3h 45
4 4h 53
5 5h 61
6 6h 69
7 7h 77
8 8h 85
12 9h 117
16 Ah 149
20 Bh 186
24 Ch 218
32 Dh 282
48 Eh 410
64 Fh 538
The critical part of a CAN FD frame is during the data phase, where the CAN bit rate is faster than in the arbitration phase. The
minimum number of peripheral clocks per fast CAN bit (MinNumClkFastBit) can be calculated to guarantee that enough time is
available for FlexCAN to scan the message buffer memory during reception and transmission. The equation below calculates
this constraint.
(8.5 × MaxNumOfMb ) + [ ERFEN × (2 × NFE + 4)] + 64 - (9 × NumClkNomBit )
MinNumClkFastBit A =
NumOfFastBits
Equation 22. Minimum number of peripheral clocks per fast CAN bit for FlexCAN scan process
Where MaxNumOfMb is the maximum number of available message buffers defined in MCR[MAXMB] . NFE and ERFEN are the
fields defined in Enhanced RX FIFO Control (ERFCR) .
The clock-domain-crossing circuit between the CHI and PE subblocks also imposes a minimum number of peripheral clocks per
fast CAN bit. This minimum is required for the handshake mechanism to work properly without losing status information through
the interface, as shown in the equation below.

---

*Page 2344*

CAN (FlexCAN)
f SYS
MinNumClkFastBit B = 3 × 1 +
f CANCLK
Equation 23. Minimum number of peripheral clocks per fast CAN bit for FlexCAN clock domain interface
Therefore, the larger of the two values calculated above determines the minimum number of peripheral clocks per fast CAN
bit (MinNumClkFastBit).
MinNumClkFastBit = Maximum ( MinNumClkFastBitA , MinNumClkFastBitB )
Equation 24. Minimum number of peripheral clocks per fast CAN bit
Then, the maximum CAN bit rate in the data phase of CAN FD frames (DataBitRateMAX) can be calculated as below.
f CANCLK
DataBitrate MAX =
MinNumClkFastBit × f CANCLK
ROUNDUP
f SYS
Equation 25. Maximum achievable baud rate for data phase
These factors affect the maximum data bit rate attainable by FlexCAN in CAN FD mode:
• The peripheral and oscillator clock frequencies
• The maximum number of message buffers
• The expected nominal bit rate
Also, the data bit rate depends on the minimum payload size of FD frames used in a given application.
To illustrate how the configuration of FlexCAN variables affects the CAN FD bit rate, consider this application example:
• The peripheral clock frequency is set to 50 MHz
• The oscillator clock frequency is set to 40 MHz
1. Considering the nominal bit rate as 1 Mbit/s, the number of peripheral clocks per CAN bit in nominal bit rate is calculated
as below.
6
50 × 10
NumClkNomBit = = 50
6
1 × 10
Equation 26. Calculation example for number of peripheral clocks per nominal CAN bit
2. The number of fast CAN bits (NumOfFastBits) is determined in Table 439 . For example, if the minimum payload in FD
frames is 8 bytes, there are 85 CAN bits in the data phase.
3. Assuming the maximum number of message buffers is 96, and Enhanced RX FIFO is disabled, the minimum number of
peripheral clocks per fast CAN bit (MinNumClkFastBit) can be calculated.
(8.5 × 96) + 64 - (9 × 50)
MinNumClkFastBit A = = 5.06
85
Equation 27. Calculation example for number of peripheral clocks per fast CAN bit for FlexCAN scan process

---

*Page 2345*

CAN (FlexCAN)
50
MinNumClkFastBit B = 3 × 1 + = 6.75
40
Equation 28. Calculation example for number of peripheral clocks per fast CAN bit for FlexCAN clock domain
interface
MinNumClkFastBit = Maximum ( 5.06, 6.75 ) = 6.75
Equation 29. Calculation example for number of peripheral clocks per fast CAN bit
4. The maximum CAN bit rate in the data phase can finally be found.
6
40 × 10
DataBitRate MAX = = 6.667 Mbps
6
6.75 × 40 × 10
ROUNDUP
6
50 × 10
Equation 30. Calculation example for maximum achievable baud rate
Even though the oscillator clock frequency (40 MHz) is adequate to generate a data rate of 8 Mbit/s in CAN FD mode, the specific
FlexCAN configuration limits this rate to 6.667 Mbit/s. This limitation is mainly due to the low peripheral clock frequency that
imposes the MinNumClkFastBitB bound.
Table 440 shows the maximum data rate for CAN FD with Enhanced RX FIFO disabled according to clock frequencies, payload
size, and number of available message buffers. For some cases, if the number of available message buffers is reduced, FlexCAN
can then achieve a data rate up to 8 Mbit/s.
Table 440. Maximum CAN bit rate in data phase on CAN FD frames with Enhanced RX FIFO disabled
Peripheral clock frequency Payload size Number of available message Maximum data rate (Mbit/s)
(MHz) buffers
40 8 94 6.667
40 8 114 >5.0
40 12 >117 6.667
40 12 128 5.714
50 12–64 128 6.667
60 8 126 8.0
60 12 128 8.0
67 6 128 8.0
80 3 128 8.0
100 0 128 8.0

#### 70.3.12 Reset

You can reset FlexCAN in the following ways:
• Soft reset:
— MCR[SOFTRST] , which resets some of the memory-mapped registers synchronously. To see which registers soft
reset affects, see Table 443 .
— Chip-level soft reset, which has the same effect as MCR[SOFTRST].

---

*Page 2346*

CAN (FlexCAN)
Soft reset is synchronous and must follow an internal request-and-acknowledge procedure across clock domains. Therefore, it
may take some time to propagate its effects fully. MCR[SOFTRST] remains 1 when soft reset is pending, so software can poll this
field to identify when the reset has completed. Soft reset cannot be applied when clocks are shut down in a low-power mode. The
low-power mode should be exited and the clocks resumed before applying soft reset.
When the module is enabled ( MCR[MDIS] becomes 0), FlexCAN automatically enters Freeze mode. In Freeze mode:
1. FlexCAN is unsynchronized to the CAN bus.
2. MCR[HALT] and MCR[FRZ] become 1.
3. The internal state machines are disabled.
4. MCR[FRZACK] and MCR[NOTRDY] become 1.
The TX pin is in the recessive state and FlexCAN does not initiate any transmission or reception of CAN frames. Reset does not
affect the message buffers and the RX Individual Mask registers, so they are not automatically initialized.

#### 70.3.13 Interrupts

FlexCAN has many interrupt sources:
• Interrupts due to message buffers
• Interrupts due to interrupts combined via an OR operator from:
— Message buffers
— Bus Off
— Bus Off Done
— Error
— Error Fast (errors detected in the data phase of CAN FD format messages with BRS = 1)
— TX Warning
— RX Warning
If its corresponding IMASK bit is 1, each message buffer can be an interrupt source. There is no distinction between TX and RX
interrupts for a particular buffer, under the assumption that the buffer is initialized for either transmission or reception. Each buffer
has an assigned flag bit in the IFLAG registers. When the corresponding buffer completes a successful transfer, the flag is set.
When the CPU writes 1 to it, the flag is cleared (unless another interrupt is generated at the same time).
NOTE
The CPU must clear only the bit causing the current interrupt. For this reason, do not use bit manipulation
instructions (BSET) to clear interrupt flags. These instructions may cause accidental clearing of interrupt flags
which are set after entering the current interrupt handler.
If the Legacy RX FIFO is enabled ( MCR[RFEN] = 1) and DMA is disabled ( MCR[DMA] = 0) , the interrupts corresponding to
message buffers 0–7 have different meanings.
• Bit 7 of Interrupt Flags 1 (IFLAG1) becomes the Legacy FIFO Overflow flag
• Bit 6 becomes the Legacy FIFO Warning flag
• Bit 5 becomes the Frames Available in Legacy FIFO flag
• Bits 4–0 are unused.
See Interrupt Flags 1 (IFLAG1) for more information.
If both Legacy RX FIFO and DMA are enabled (MCR[RFEN] = 1 and MCR[DMA] = 1), FlexCAN does not generate any Legacy
FIFO interrupt. Bit 5 of IFLAG1 still indicates Frames Available in Legacy FIFO and generates a DMA request. Bits 7, 6, and 4–0
are unused.

---

*Page 2347*

CAN (FlexCAN)
CAUTION
Legacy FIFO cannot be enabled when CAN FD is enabled.
When multiple message buffer interrupt sources are combined via an OR operator into a single interrupt, the interrupt is generated
when any associated message buffer (or FIFO, if applicable) generates an interrupt. In this case, the CPU must read the IFLAG
registers to determine which message buffer or FIFO source caused the interrupt.
These interrupt sources generate interrupts like the message buffer interrupt sources, and can be read from Error and Status
1 (ESR1) :
• Bus Off
• Bus Off Done
• Error
• Error Fast
• TX Warning
• RX Warning
The Bus Off, Error, TX Warning, and RX Warning interrupt masks are located in Control 1 (CTRL1) .

#### 70.3.14 Bus interface

CPU access to FlexCAN registers is subject to the following rules:
• Unrestricted read and write access to supervisor registers results in an access error. In Table 443 , supervisor registers are
registers identified with an S, and registers that are identified with S/U that are in Supervisor Mode
• Read and write access to implemented reserved address space results in an access error.
• Write access to positions whose bits are all currently read-only results in an access error. If at least one of the bits is not
read-only, no access error is issued. Write permission to specific positions or some of their bits can change depending on
the mode of operation or transitory state. See register and field descriptions for details.
• Read and write access to unimplemented address space results in an access error.
• Read and write access to RAM-located positions during Low-Power mode results in an access error.
• The RXIMR memory region can be considered as general-purpose memory and available for access via these methods:
— If you write 0 to MCR[IRMQ] , the individual masks (RXIMR) are disabled. In this case, the RXIMR memory region is
considered general-purpose memory.
— If MCR[MAXMB] is programmed with a value smaller than the available number of message buffers, the unused
memory space can be used as general-purpose RAM space. Reserved words within RAM cannot be used.
For example, suppose the RAM in FlexCAN can support up to 16 message buffers, CTRL2[RFFN] = 0h, and
MCR[MAXMB] = 0.
◦ In this case, the maximum number of message buffers becomes one.
◦ The RAM starts at 0080h, and the space 0080h–008Fh is used by the one message buffer.
◦ The memory space 0090h–017Fh is available.
◦ The space 0180h–087Fh is reserved.
◦ The space 0880h–0883h is used by the one individual mask and the available memory in the mask register
space is 0884h–08BFh.
◦ In the space from 08C0h–09DFh, there are reserved words for internal use which cannot be used as general-
purpose RAM.
As a rule, free memory space for general purpose depends only on MCR[MAXMB] .
— If MCR[FDEN] = 1, general-purpose memory can be used only outside Freeze mode.

---

*Page 2348*

CAN (FlexCAN)
Table 441. Access permissions
Supervisor access 0 1
Supervisor mode
0 1 Any
MCR[SUPV] = 1
Modes of operation Normal Freeze Low-power Any Normal Freeze Low-power
MCR Bus error Bus error Bus error Bus error Read and Read and Read and
write write write
CTRL1 Read and Read and Read and Bus error Read and Read and Read and
write write write write write write
Read and Read and Read and Bus error Read and Read and Read and
TIMER
write write write write write write
TCR Bus error Bus error Bus error Bus error Bus error Bus error Bus error
1
RXMGMASK Bus error Read and Bus error Bus error Bus error Read and Bus error
for write write for write for write write for write
operation operation operation operation
1
RX14MASK Bus error Read and Bus error Bus error Bus error Read and Bus error
for write write for write for write write for write
operation operation operation operation
1
RX15MASK Bus error Read and Bus error Bus error Bus error Read and Bus error
for write write for write for write write for write
operation operation operation operation
ECR Bus error Read and Bus error Bus error Bus error Read and Bus error
for write write for write for write write for write
operation operation operation operation
ESR1 Read and Read and Read and Bus error Read and Read and Read and
write write write write write write
IMASK2 Read and Read and Read and Bus error Read and Read and Read and
write write write write write write
IMASK1 Read and Read and Read and Bus error Read and Read and Read and
write write write write write write
IFLAG2 Read and Read and Read and Bus error Read and Read and Read and
write write write write write write
IFLAG1 Read and Read and Read and Bus error Read and Read and Read and
write write write write write write
CTRL2 Read and Read and Read and Bus error Read and Read and Read and
write write write write write write
ESR2 Read and Read and Read and Bus error Read and Read and Read and
write write write write write write
CRCR Bus error Bus error Bus error Bus error Bus error Bus error Bus error
for write for write for write for write for write for write
operation operation operation operation operation operation
Table continues on the next page...

---

*Page 2349*

CAN (FlexCAN)
Table 441. Access permissions (continued)
Supervisor access 0 1
Supervisor mode
0 1 Any
MCR[SUPV] = 1
Modes of operation Normal Freeze Low-power Any Normal Freeze Low-power
1
RXFGMASK Bus error Read and Bus error Bus error Bus error Read and Bus error
for write write for write for write write for write
operation operation operation operation
1
RXFIR Bus error Bus error Bus error Bus error Bus error Bus error Bus error
for write for write for write for write for write for write
operation operation operation operation operation operation
CBT Bus error Read and Bus error Bus error Bus error Read and Bus error
for write write for write for write write for write
operation operation operation operation
DBG1 Bus error Bus error Bus error Bus error Bus error Bus error Bus error
for write for write for write for write for write for write for write
operation operation operation operation operation operation operation
DBG2 Bus error Bus error Bus error Bus error Bus error Bus error Bus error
for write for write for write for write for write for write for write
operation operation operation operation operation operation operation
IMASK3 Read and Read and Read and Bus error Read and Read and Read and
write write write write write write
IFLAG3 Read and Read and Read and Bus error Read and Read and Read and
write write write write write write
1 2 Read and Read and Read and Bus error Read and Read and Read and
MB
write write write write write write
1 Read and Read and Read and Bus error Read and Read and Read and
Legacy FIFO header
write write write write write write
1 Bus error Bus error Bus error Bus error Bus error Bus error Bus error
Legacy FIFO reserved space
1
Legacy FIFO filters Read and Read and Read and Bus error Read and Read and Read and
write write write write write write
1
RXIMR Bus error Read and Bus error Bus error Bus error Read and Bus error
write write
MECR Read and Read and Read and Bus error
Read and Read and Read and
write write write
write write write
Read and Read and Read and Bus error Read and Read and Read and
ERRIAR
write write write write write write
Read and Read and Read and Bus error Read and Read and Read and
ERRIDPR
write write write write write write
Read and Read and Read and Bus error Read and Read and Read and
ERRIPPR
write write write write write write
Table continues on the next page...

---

*Page 2350*

CAN (FlexCAN)
Table 441. Access permissions (continued)
Supervisor access 0 1
Supervisor mode
0 1 Any
MCR[SUPV] = 1
Modes of operation Normal Freeze Low-power Any Normal Freeze Low-power
3 Read and Read and Read and Bus error Read and Read and Read and
RERRAR
write write write write write write
3 Read and Read and Read and Bus error Read and Read and Read and
RERRDR
write write write write write write
3 Read and Read and Read and Bus error Read and Read and Read and
RERRSYNR
write write write write write write
Read and Read and Read and Bus error Read and Read and Read and
ERRSR
write write write write write write
EPRS Bus error Read and Bus error Bus error Bus error Read and Bus error
for write write for write for write write for write
operation operation operation operation
ENCBT Bus error Read and Bus error Bus error Bus error Read and Bus error
for write write for write for write write for write
operation operation operation operation
EDCBT Bus error Read and Bus error Bus error Bus error Read and Bus error
for write write for write for write write for write
operation operation operation operation
ETDC Bus error Read and Bus error Bus error Bus error Read and Bus error
for write write for write for write write for write
operation operation operation operation
FDCTRL Read and Read and Read and Bus error Read and Read and Read and
write write write write write write
FDCBT Read and Read and Read and Bus error Read and Read and Read and
3 3 3 3
write write write write write write
FDCRC Bus error Bus error Bus error Bus error Bus error Bus error Bus error
for write for write for write for write for write for write
operation operation operation operation operation operation
ERFCR Read and Read and Read and Bus error Read and Read and Read and
3 3 3 3
write write write write write write
ERFIER Read and Read and Read and Bus error Read and Read and Read and
write write write write write write
ERFSR Read and Read and Read and Bus error Read and Read and Read and
write write write write write write
HR_TIME_STAMP Read and Read and Read and Bus error Read and Read and Read and
write write write write write write
Table continues on the next page...

---

*Page 2351*

CAN (FlexCAN)
Table 441. Access permissions (continued)
Supervisor access 0 1
Supervisor mode
0 1 Any
MCR[SUPV] = 1
Modes of operation Normal Freeze Low-power Any Normal Freeze Low-power
4
Enhanced Rx FIFO header Bus error Bus error Bus error Bus error Bus error Bus error Bus error
for write for write for write for write for write for write
operation operation operation operation operation operation
Enhanced Rx FIFO reserved Bus error Bus error Bus error Bus error Bus error Bus error Bus error
4
space
ERFFEL Bus error Read and Bus error Bus error Bus error Read and Bus error
for write write for write for write write for write
operation operation operation operation
1 Read and Read and Read and Bus error Read and Read and Read and
General purpose RAM
write write write write write write
1 Bus error
Reserved space (used) Bus error Bus error Bus error Bus error Bus error Bus error
1 Bus error
Reserved space (empty) Bus error Bus error Bus error Bus error Bus error Bus error
1. Access in low power is only possible if RAM_clk and MODULE_CLK are enabled.
2. If MCR[RFEN] = 1, see Legacy FIFO access rules in the rows below.
3. Write operation has no effect.
4. If MCR[RFEN] = 1, do not access Enhanced RX FIFO.

#### 70.3.15 Detection and correction of memory errors

To update the parity bits in memory properly, all FlexCAN memory must be initialized before starting its operation.
CTRL2[WRMFRZ] grants write access to all memory positions that require initialization, from 080h–ADFh and from C20h–31FFh .
You must also initialize these registers:
• RX Message Buffers Global Mask (RXMGMASK)
• Receive 14 Mask (RX14MASK)
• Receive 15 Mask (RX15MASK)
• Legacy RX FIFO Global Mask (RXFGMASK)
MCR[RFEN] and ERFCR[ERFEN] must not be 1 during memory initialization.
FlexCAN supports detection and correction of errors in memory read accesses. Each byte of FlexCAN memory is associated with
five parity bits that ensure a Hamming distance of 4. The error correction mechanism ensures that in this 13-bit word, errors in
one bit can be corrected (correctable errors). Errors in two bits can be detected but not corrected (uncorrectable errors). Errors in
more than two bits may not be detected. For uncorrectable errors, the error correction logic does not change the corrupted data.
When a read access is performed, the parity bits are used to calculate a syndrome, which indicates the error in each byte.
When an all-zeroes or an all-ones read occurs, FlexCAN detects an uncorrectable error. See Error Report
Syndrome (RERRSYNR) .
Memory errors are indicated to the host via status register ( Error Status (ERRSR) ) and bus transfer errors. Memory errors are
reported via these report registers:
• Error Report Address (RERRAR)
• Error Report Data (RERRDR)

---

*Page 2352*

CAN (FlexCAN)
• Error Report Syndrome (RERRSYNR)
MECR[ECCDIS] determines whether the error detection and correction mechanism can be activated. When disabled, updates on
indications and reporting registers are stopped. To ensure that memory has consistent parity bits associated with the data, the
parity bits are still calculated and written with data in memory write operations.
To avoid accidentally changing the critical error correction configuration, follow this protocol to enable the update of Memory Error
Control (MECR) :
1. Write 1 to CTRL2[ECRWRE] . (By default, CTRL2[ECRWRE] is 0 and MECR[ECRWRDIS] is 1.)
2. Write 0 to MECR[ECRWRDIS] .
3. All writes to Memory Error Control (MECR) must keep MECR[ECRWRDIS] = 0.
4. After configuration is done, lock MECR by writing 1 to MECR[ECRWRDIS] or writing 0 to CTRL2[ECRWRE].
70.3.15.1 Sources of memory access
These major sources (or requestors) can access FlexCAN memory:
• Host (CPU). The largest word accessed is 32-bit.
• FlexCAN internal processes:
— RX matching
— TX arbitration
— Move-in on reception
— Move-out on transmission
The largest word accessed is 64-bit.
The source of access determines the way that uncorrectable errors are indicated and reported.
70.3.15.2 Error indication
These flags indicate memory errors:
• ERRSR[HANCEIF]
• ERRSR[FANCEIF]
• ERRSR[CEIF]
Uncorrectable errors detected in memory reads requested by the host are indicated separately from the errors detected in
requests by FlexCAN internal processes. When correctable errors are detected, FlexCAN makes no distinction of the source of
the access. There are three independent flags for these three cases , and each flag raises an interrupt unless a mask in Memory
Error Control (MECR) masks it. If both uncorrectable and correctable errors are found in different bytes in the same read operation,
both flags are set.
An uncorrectable error detected in host access is also indicated as a bus transfer error. A bus wait request may be asserted
to extend the memory transaction to the moment the report registers are updated. This indication cannot be masked. If
ERRSR[HANCEIF] is not masked, the same uncorrectable error raises a bus transfer error and an interrupt request.
Each indication flag has one overrun flag in Error Status (ERRSR) . The overrun flags do not request interrupts. Overrun flags for
uncorrectable errors indicate that other errors of the same nature were detected after the current error being treated. Overrun flags
for correctable errors indicate that other errors of the same nature were detected before the current error being treated.
The recommended handling sequence for error indication is:
1. Get error report information from report registers.
2. Use this information to take proper measures in the application.
3. Clear the ERRSR[HANCEIF], ERRSR[FANCEIF], and ERRSR[CEIF] flags.

---

*Page 2353*

CAN (FlexCAN)
4. If the overrun flag is active:
a. Alert the application that at least one error could not be managed.
b. Clear the overrun flag.
FlexCAN internal processes can access memory in transactions larger than 32 bits. For the indication, this kind of access is
considered a consecutive sequence of 32-bit accesses. If errors are found in two or more 32-bit words, the interrupt and overrun
flags are set simultaneously.
70.3.15.3 Error reporting
The error report registers provide detailed information about the address read, raw data, and syndrome read with error. The flags
described in Error indication indicate these report registers:
• Error Report Address (RERRAR)
• Error Report Data (RERRDR)
• Error Report Syndrome (RERRSYNR)
The address, data, and syndrome registers are updated simultaneously with the error flags, according to these rules:
1. If either of the uncorrectable error flags is set, the report registers are not updated. The previous uncorrectable error
reporting is preserved.
2. Otherwise, either no error flag is set or only the correctable error flag is set. The report registers are updated according
to the new error, or according to the most severe new error, if uncorrectable and correctable errors are simultaneously
detected.
Reporting of errors detected in accesses larger than 32 bits follows the rules described in Error indication and in the description
of Error Report Address (RERRAR) .
The addresses reported in RERRAR and defined in ERRIAR are not the same as the addresses listed in the module memory map.
The relation between the reported addresses and the respective ones in the module memory map is shown in the description of
Error Injection Address (ERRIAR) .
Addresses reported when reading memory portions organized as FIFOs refer to the address of the specific entry accessed in the
FIFO, not to the FIFO base address. Such memory portions include:
• Legacy RX FIFO Structure
• Enhanced RX FIFO Structure
• Legacy RX FIFO Information (RXFIR)
To ensure coherence of error report registers, disable the report update by writing 1 to MECR[RERRDIS] before reading the
report registers.
70.3.15.4 Error response
Correctable errors have no consequence to FlexCAN operation because the host or FlexCAN internal processes corrects affected
data before its use.
For host-initiated reads, an uncorrectable error may affect the host, but does not affect FlexCAN operation.
Uncorrectable errors detected on memory read operations requested by FlexCAN internal processes may result in incorrect
operation depending on the state of MECR[NCEFAFRZ] , as follows:
• During reception (either matching or move-in process), when an uncorrectable error occurs:
— An incorrect destination may be selected to store the incoming frame.
— A corrupted frame may be stored in the correct destination.
— Both of these events may occur.

---

*Page 2354*

CAN (FlexCAN)
If MECR[NCEFAFRZ] = 1, FlexCAN stops operation automatically and enters Freeze mode to prevent corrupted data
from being treated as valid by FlexCAN internal processes. If MECR[NCEFAFRZ] = 0, FlexCAN continues working, and a
corrupted frame is received.
• During arbitration, when an uncorrectable error occurs:
— A non-highest-priority TX message buffer may be mistakenly selected for transmission.
— Its data may be corrupted.
If MECR[NCEFAFRZ] = 1, FlexCAN stops operation automatically and enters Freeze mode before starting the move-out. If
MECR[NCEFAFRZ] = 0, FlexCAN proceeds to move-out with a corrupted frame that will be transmitted on the CAN bus.
• During move-out, when an uncorrectable error occurs, a corrupted frame is copied from the selected TX MB that won the
arbitration to the TX SMB for transmission. If MECR[NCEFAFRZ] = 1, FlexCAN stops operation automatically and enters
Freeze mode before starting the transmission. When MECR[NCEFAFRZ] = 0, the corrupted frame is transferred from the
TX SMB to the Protocol Engine (PE) subblock and is transmitted on the CAN bus.
• An uncorrectable error can also be detected beyond move-out, when TX data is read from TX SMB (buffer located in RAM)
to be transferred to the PE subblock for transmission. In this case, a frame with corrupted ID or data is transmitted on the
CAN bus.
To prevent the external nodes from successfully receiving the frame, FlexCAN inverts all bits in the CRC field (CRC sequence
plus CRC delimiter). Also, it transmits an error flag just after CRC delimiter due to self-detecting a Bit1 error and a form error
due to the CRC field inversion. When MECR[NCEFAFRZ] = 1, FlexCAN stops operation automatically and enters Freeze
mode just after the error frame. When MECR[NCEFAFRZ] = 0, FlexCAN may attempt to retransmit the same frame, as long
as no other higher-priority TX MB is configured for transmission after.
If the uncorrectable error persists, FlexCAN eventually reaches the Bus Off state due to consecutive error detections.
ECR[TXERRCNT] is updated every time FlexCAN inverts the CRC field, causing errors as described above.
When MECR[NCEFAFRZ] = 1 and FlexCAN enters Freeze mode, only the CPU can cause FlexCAN to exit Freeze mode and
resume Normal mode. MECR[NCEFAFRZ] becoming 1 is the only way to prevent corrupted frames from being transmitted on the
CAN bus to the move-out internal process.
The error report registers can provide information to the application for customized management of these situations.
70.3.15.5 Error injection
These error injection registers are used to inject errors in memory reads to force errors and update the indication and
reporting registers:
• Error Injection Address (ERRIAR)
• Error Injection Data Pattern (ERRIDPR)
• Error Injection Parity Pattern (ERRIPPR)
The relation between the error injection addresses and the corresponding addresses in the module memory map is shown in Error
Injection Address (ERRIAR) .
The injection is done by flipping the data and parity bits corresponding to the bits set to 1 in ERRIDPR and ERRIPPR. You can
select injection specifically for memory accesses requested by the host or by FlexCAN internal processes.
For accesses larger than 32 bits, MECR[EXTERRIE] extends the injection pattern, replicating it in 32-bit words to fill the width of
the access.
NOTE
It is possible, but very unlikely, for error injection to correct a bit with error. This correction does not raise the error
flags and reports as expected.
To ensure coherence among error injection registers and avoid spurious error injections, you must clear MECR[HAERRIE] and
MECR[FAERRIE] when configuring the memory injection registers.

---

*Page 2355*

CAN (FlexCAN)

#### 70.4 External signal descriptions

FlexCAN has two I/O signals connected to the external chip pins. These signals are summarized in Table 442 and described in
the following subsections.
Table 442. FlexCAN signal descriptions
Signal Description I/O
CAN RX CAN receive pin Input
CAN TX CAN transmit pin Output

#### 70.4.1 CAN RX

This pin is the receive pin from the CAN bus transceiver. Logic level 0 represents its dominant state. Logic level 1 represents its
recessive state.

#### 70.4.2 CAN TX

This pin is the transmit pin to the CAN bus transceiver. Logic level 0 represents its dominant state. Logic level 1 represents its
recessive state.

#### 70.5 Initialization and application information

#### 70.5.1 FlexCAN initialization sequence

For any configuration change or initialization, you must put FlexCAN into Freeze mode (see Freeze mode ). The module must be
initialized after every reset. FlexCAN memory must be initialized before switching to functional mode.
The following is a generic initialization sequence applicable to FlexCAN:
1. Initialize Module Configuration (MCR) .
a. Enable the individual filtering per message buffer and reception queue features by writing 1 to MCR[IRMQ] .
b. Enable the warning interrupts by writing 1 to MCR[WRNEN] .
c. If required, disable frame self-reception by writing 1 to MCR[SRXDIS] .
d. Enable the Legacy RX FIFO by writing 1 to MCR[RFEN] or enable the Enhanced RX FIFO by writing 1 to
ERFCR[ERFEN] .
e. If Legacy RX FIFO or Enhanced RX FIFO is enabled and DMA is required, write 1 to MCR[DMA] .
f. Enable the abort mechanism by writing 1 to MCR[AEN] .
g. Enable the local priority feature by writing 1 to MCR[LPRIOEN] .
2. Initialize Control 1 (CTRL1) and CAN FD Bit Timing (FDCBT) . Optionally initialize CAN Bit Timing (CBT) .
a. Determine the bit timing parameters: CTRL1[PROPSEG] , CTRL1[PSEG1] , CTRL1[PSEG2] , and CTRL1[RJW] .
b. Optionally determine the bit timing parameters: CBT[EPROPSEG] , CBT[EPSEG1] , CBT[EPSEG2] , and
CBT[ERJW] .
c. Determine the CAN FD bit timing parameters: FDCBT[FPROPSEG] , FDCBT[FPSEG1] , FDCBT[FPSEG2] , and
FDCBT[FRJW] .
d. Determine the bit rate by programming CTRL1[PRESDIV] and optionally programming CBT[EPRESDIV] .
e. Determine the CAN FD bit rate by programming FDCBT[FPRESDIV] .
f. Determine the internal arbitration mode by programming CTRL1[LBUF] .

---

*Page 2356*

CAN (FlexCAN)
3. If Error Code Correction (ECC) is enabled, you must initialize all FlexCAN memory. See Detection and correction of
memory errors .
4. Initialize the message buffers. (See Message buffer structure for message buffer details.)
a. The control and status word of all message buffers must be initialized.
b. If RX FIFO is enabled, the ID filter table must be initialized.
c. Other entries in each message buffer should be initialized as required.
5. Initialize Receive Individual Mask (RXIMR0 - RXIMR95) .
6. Write 1 to required interrupt mask bits in:
• IMASK registers (for all message buffer interrupts)
• Control 1 (CTRL1) and Control 2 (CTRL2) (for Bus Off and Error interrupts)
7. Write 0 to MCR[HALT] .
After the last step listed above, FlexCAN attempts to synchronize to the CAN bus.

#### 70.6 Memory map and register definition

This section describes the registers and data structures in FlexCAN. The base address of the module depends on the particular
memory map of the chip.

#### 70.6.1 FlexCAN memory mapping

The address space occupied by FlexCAN has 128 bytes for registers starting at the module base address, followed by embedded
RAM starting at address offset 0080h.
Each individual register is identified by its complete name and corresponding mnemonic. The access type can be Supervisor (S) or
Unrestricted (U). Most registers can be configured to have either Supervisor or Unrestricted access by programming MCR[SUPV] .
These registers are identified as S/U in the Access column of Table 443 .
NOTE
An invalid register access results in a bus error. Invalid accesses include reading a write-only register, writing a
read-only register, and accessing an invalid address.
NOTE
To update the parity bits in memory properly, all FlexCAN memory must be initialized before reading registers
which are implemented in memory. CTRL2[WRMFRZ] grants write access to all memory positions that require
initialization, from 080h–ADFh and from C20h–31FFh . You must also initialize RX Message Buffers Global Mask
(RXMGMASK) , Receive 14 Mask (RX14MASK) , Receive 15 Mask (RX15MASK) and Legacy RX FIFO Global
Mask (RXFGMASK) . MCR[RFEN] and ERFCR[ERFEN] must not be 1 during memory initialization.
Table 443. Register access and reset information
Register Access type Affected by Affected by
hard reset soft reset
Module Configuration (MCR) S Yes Yes
Control 1 (CTRL1) S/U Yes No
Free-Running Timer (TIMER) S/U Yes Yes
RX Message buffers Global Mask (RXMGMASK) S/U No No
Table continues on the next page...

---

*Page 2357*

CAN (FlexCAN)
Table 443. Register access and reset information (continued)
Register Access type Affected by Affected by
hard reset soft reset
RX Buffer 14 Mask (RX14MASK) S/U No No
RX Buffer 15 Mask (RX15MASK) S/U No No
Error Counter (ECR) S/U Yes Yes
Error and Status 1 (ESR1) S/U Yes Yes
Interrupt Masks 2 (IMASK2) S/U Yes Yes
Interrupt Masks 1 (IMASK1) S/U Yes Yes
Interrupt Flags 2 (IFLAG2) S/U Yes Yes
Interrupt Flags 1 (IFLAG1) S/U Yes Yes
Control 2 (CTRL2) S/U Yes No
Error and Status 2 (ESR2) S/U Yes Yes
Cyclic Redundancy Check (CRCR) S/U Yes Yes
RX FIFO Global Mask (RXFGMASK) S/U No No
RX FIFO Information (RXFIR) S/U No No
CAN Bit Timing (CBT) S/U Yes No
Interrupt Masks 3 (IMASK3) S/U Yes Yes
Interrupt Flags 3 (IFLAG3) S/U Yes Yes
Message buffers S/U No No
RX Individual Masks S/U No No
Memory Error Control (MECR) S/U Yes Yes
Error Injection Address (ERRIAR) S/U Yes Yes
Error Injection Data Pattern (ERRIDPR) S/U Yes Yes
Error Injection Parity Pattern (ERRIPPR) S/U Yes Yes
Error Report Address (RERRAR) S/U Yes Yes
Error Report Data (RERRDR) S/U Yes Yes
Error Report Syndrome (RERRSYNR) S/U Yes Yes
Table continues on the next page...

---

*Page 2358*

CAN (FlexCAN)
Table 443. Register access and reset information (continued)
Register Access type Affected by Affected by
hard reset soft reset
Error Status (ERRSR) S/U Yes Yes
Enhanced CAN Bit Timing Prescalers (EPRS) S/U Yes No
Enhanced Nominal CAN Bit Timing (ENCBT) S/U Yes No
Enhanced Data Phase CAN bit Timing (EDCBT) S/U Yes No
Enhanced Transceiver Delay Compensation (ETDC) S/U Yes No
CAN FD Control (FDCTRL) S/U Yes No
CAN FD Bit Timing (FDCBT) S/U Yes No
CAN FD CRC (FDCRC) S/U Yes Yes
Enhanced RX FIFO Control (ERFCR) Yes Yes
S/U
Enhanced RX FIFO Interrupt Enable (ERFIER) Yes Yes
S/U
Enhanced RX FIFO Status (ERFSR) Yes Yes
S/U
High-Resolution Timestamp (HR_TIME_STAMP) S/U No No
Enhanced RX FIFO No No
S/U
Enhanced RX FIFO Filter Element (ERFFEL) No No
S/U
FlexCAN can store CAN messages for transmission and reception using message buffers and RX FIFO structures.

#### 70.6.2 FlexCAN register descriptions

The table below shows the FlexCAN memory map.
The address range from offset 80h–67Fh allocates the ninety-six 128-bit message buffers. The memory maps for the message
buffers are in FlexCAN message buffer memory map .
The address range from offset 2000h–204Ch allocates the Enhanced RX FIFO output, and the address range from offset
2050h–263Ch allocates the rest of Enhanced RX FIFO 19 elements. The memory map for the Enhanced RX FIFO is in Enhanced
RX FIFO structure .
70.6.2.1 FlexCAN memory map
FLEXCAN_0 base address: 4030_4000h
FLEXCAN_1 base address: 4030_8000h
FLEXCAN_2 base address: 4030_C000h
FLEXCAN_3 base address: 4031_0000h
FLEXCAN_4 base address: 4031_4000h
FLEXCAN_5 base address: 4031_8000h

---

*Page 2359*

CAN (FlexCAN)
Offset Register Access Reset value
Width
(In bits)
0h Module Configuration (MCR) 32 RW D890_000Fh
4h Control 1 (CTRL1) 32 RW 0000_0000h
8h Free-Running Timer (TIMER) 32 RW 0000_0000h
10h RX Message Buffers Global Mask (RXMGMASK) 32 RW See section
14h Receive 14 Mask (RX14MASK) 32 RW See section
18h Receive 15 Mask (RX15MASK) 32 RW See section
1Ch Error Counter (ECR) 32 RW 0000_0000h
20h Error and Status 1 (ESR1) 32 RW 0000_0000h
24h Interrupt Masks 2 (IMASK2) 32 RW 0000_0000h
28h Interrupt Masks 1 (IMASK1) 32 RW 0000_0000h
2Ch Interrupt Flags 2 (IFLAG2) 32 RW 0000_0000h
30h Interrupt Flags 1 (IFLAG1) 32 RW 0000_0000h
34h Control 2 (CTRL2) 32 RW See section
38h Error and Status 2 (ESR2) 32 R 0000_0000h
44h Cyclic Redundancy Check (CRCR) 32 R 0000_0000h
48h Legacy RX FIFO Global Mask (RXFGMASK) 32 RW See section
4Ch Legacy RX FIFO Information (RXFIR) 32 R See section
50h CAN Bit Timing (CBT) 32 RW 0000_0000h
6Ch Interrupt Masks 3 (IMASK3) 32 RW 0000_0000h
74h Interrupt Flags 3 (IFLAG3) 32 RW 0000_0000h
880h - 9FCh Receive Individual Mask (RXIMR0 - RXIMR95) 32 RW See section
AE0h Memory Error Control (MECR) 32 RW 800C_0080h
AE4h Error Injection Address (ERRIAR) 32 RW 0000_0000h
AE8h Error Injection Data Pattern (ERRIDPR) 32 RW 0000_0000h
AECh Error Injection Parity Pattern (ERRIPPR) 32 RW 0000_0000h
AF0h Error Report Address (RERRAR) 32 R 0000_0000h
AF4h Error Report Data (RERRDR) 32 R 0000_0000h
AF8h Error Report Syndrome (RERRSYNR) 32 R 0000_0000h
AFCh Error Status (ERRSR) 32 RW 0000_0000h
BF0h Enhanced CAN Bit Timing Prescalers (EPRS) 32 RW 0000_0000h
BF4h Enhanced Nominal CAN Bit Timing (ENCBT) 32 RW 0000_0000h
BF8h Enhanced Data Phase CAN Bit Timing (EDCBT) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 2360*

CAN (FlexCAN)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
BFCh Enhanced Transceiver Delay Compensation (ETDC) 32 RW 0000_0000h
C00h CAN FD Control (FDCTRL) 32 RW 8000_0100h
C04h CAN FD Bit Timing (FDCBT) 32 RW 0000_0000h
C08h CAN FD CRC (FDCRC) 32 R 0000_0000h
C0Ch Enhanced RX FIFO Control (ERFCR) 32 RW 0000_0000h
C10h Enhanced RX FIFO Interrupt Enable (ERFIER) 32 RW 0000_0000h
C14h Enhanced RX FIFO Status (ERFSR) 32 RW 0000_0000h
C30h - DACh High-Resolution Timestamp (HR_TIME_STAMP0 - 32 RW See section
HR_TIME_STAMP95)
3000h - Enhanced RX FIFO Filter Element (ERFFEL0 - ERFFEL127) 32 RW See section
31FCh
70.6.2.2 Module Configuration (MCR)
Offset
Register Offset
MCR 0h
Function
Defines global system configurations, including the module operation modes and the maximum message buffer configuration.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
NOTR FRZA LPMA
R
Reserv SOFT Reserv WRNE Reserv Reserv SRXDI
DY CK CK
MDIS FRZ RFEN HALT SUPV IRMQ
ed RST ed N ed ed S
W
Reset 1 1 0 1 1 0 0 0 1 0 0 1 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
LPRIO
DMA AEN FDEN IDAM MAXMB
EN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 1 1 1 1

---

*Page 2361*

CAN (FlexCAN)
Fields
Field Function
31 Module Disable
MDIS Disables FlexCAN. When disabled, FlexCAN disables the clocks to the CAN Protocol Engine and Controller
Host Interface submodules. Soft reset does not affect this field.
0b - Enable
1b - Disable
30 Freeze Enable
FRZ Specifies FlexCAN behavior when MCR[HALT] = 1 or when Debug mode is requested at chip level .
When this field becomes 1, FlexCAN can enter Freeze mode. Writing 0 to this field causes FlexCAN
to exit from Freeze mode. The chip writes 1 to this field when a noncorrectable error is detected
( MECR[NCEFAFRZ] becomes 1).
0b - Disable
1b - Enable
29 Legacy RX FIFO Enable
RFEN Enables the Legacy RX FIFO feature. When this field is 1, message buffers 0–5 cannot be used for normal
reception and transmission. The corresponding memory region (80h–DCh) is used by the FIFO engine and
additional message buffers (up to 32, depending on CTRL2[RFFN] ). These message buffers are used as
Legacy RX FIFO ID filter table elements. This field also impacts the definition of the minimum number of
peripheral clocks per CAN bit as described in Table 438 . This field can be written in Freeze mode only; the
module blocks it in other modes.
NOTE
When CAN FD operation is enabled (see MCR[FDEN] ), you cannot write 1 to this field.
NOTE
This field must not be 1 if ERFCR[ERFEN] = 1.
0b - Disable
1b - Enable
28 Halt FlexCAN
HALT Puts FlexCAN into Freeze mode. The CPU should write 0 to this field after initializing the message
buffers and Control 1 (CTRL1) and Control 2 (CTRL2) . FlexCAN performs no reception or transmission
before this field becomes 0. Freeze mode cannot be entered when FlexCAN is in a low-power mode.
The module writes 1 to this field when a noncorrectable error is detected and MECR[NCEFAFRZ] = 1.
0b - No request
1b - Enter Freeze mode, if MCR[FRZ] = 1.
27 FlexCAN Not Ready
NOTRDY Indicates whether FlexCAN is in Disable mode or Freeze mode. When FlexCAN has exited these
modes, this field becomes 0. Soft reset does not affect this field.
Table continues on the next page...

---

*Page 2362*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
0b - FlexCAN is in Normal mode, Listen-Only mode, or Loopback mode.
1b - FlexCAN is in Disable mode or Freeze mode.
26 Reserved
— When writing to this field, always write the reset value.
25 Soft Reset
SOFTRST Resets internal state machines of FlexCAN and some memory-mapped registers.
The CPU can write 1 to this field directly. Because soft reset is synchronous and must follow a request-and-
acknowledge procedure across clock domains, it may take some time to propagate its effect fully. When
reset is pending, this field remains 1; it automatically becomes 0 when reset completes. You can poll this
field to know when the soft reset has completed.
Soft reset cannot be applied when clocks are shut down in a low-power mode. Transfer the module out of
the low-power mode before applying soft reset. Soft reset does not affect this field.
NOTE
This field becomes 0 within 2 CAN bits after assertion of this bit.
0b - No reset
1b - Soft reset affects reset registers
24 Freeze Mode Acknowledge
FRZACK Indicates whether FlexCAN is in Freeze mode and its prescaler is stopped. The Freeze mode request
cannot be granted until current transmission or reception processes have finished. Therefore, you can poll
this field to know when FlexCAN has entered Freeze mode. If the Freeze mode request is negated, this field
becomes 0 after the FlexCAN prescaler is running again. If Freeze mode is requested when FlexCAN is in
a low-power mode, this field becomes 1 only when the low-power mode is exited. See Freeze mode . Soft
reset does not affect this field.
NOTE
This field becomes 1 within 180 CAN bits if current transmission or reception is ongoing for
classical frames and 730 CAN bits if current transmission or reception is ongoing for FD
frames after the low-power mode requested by CPU. This field becomes 0 within 2 CAN bits
after the Freeze mode request removal (see Protocol timing ).
0b - Not in Freeze mode, prescaler running.
1b - In Freeze mode, prescaler stopped.
23 Supervisor Mode
SUPV Configures FlexCAN to be either in Supervisor or User mode. The registers that this field affects are
marked as S/U in the Access type column of Table 443 . The affected registers start with Supervisor access
allowance only. In User mode, affected registers allow both Supervisor and Unrestricted accesses.
In Supervisor mode, affected registers allow only Supervisor access. Unrestricted access behaves as
though the access is performed on an unimplemented register location.
This field can be written only in Freeze mode; the module blocks it in other modes.
Table continues on the next page...

---

*Page 2363*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
0b - User mode
1b - Supervisor mode
22 Reserved
— When writing to this field, always write the reset value.
21 Warning Interrupt Enable
WRNEN Enables the generation of the flags ESR1[TWRNINT] and ESR1[RWRNINT] . When this field is 1,
TWRNINT and RWRNINT flags are set when the respective error counter transitions from less than
96 to greater than or equal to 96. When this field is 0, the TWRNINT and RWRNINT flags are always
zero, independent of the values of the error counters. No warning interrupt is generated. This field can be
written in Freeze mode only; the module blocks it in other modes.
0b - Disable
1b - Enable
20 Low-Power Mode Acknowledge
LPMACK Indicates whether FlexCAN is in a low-power mode (Disable mode). A low-power mode cannot be entered
until all current transmission and reception processes have finished. The CPU can poll this field to know
when FlexCAN has entered low-power mode. Soft reset does not affect this field.
NOTE
This field becomes 1 within 180 CAN bits if current transmission or reception is ongoing for
classical frames and 730 CAN bits if current transmission or reception is ongoing for FD
frames after the low-power mode requested by CPU. This field becomes 0 within 2 CAN bits
after the low-power mode request removal (see Protocol timing ).
0b - Not in a low-power mode
1b - In a low-power mode
19 Reserved
— When writing to this field, always write the reset value.
18 Reserved
— When writing to this field, always write the reset value.
17 Self-Reception Disable
SRXDIS Determines whether FlexCAN can receive frames transmitted by itself. If 1, frames transmitted by the
module are not stored in any MB, regardless of whether the MB is programmed with an ID that matches
the transmitted frame. No interrupt flag or interrupt signal is generated due to the frame reception. This
field can be written only in Freeze mode; the module blocks it in other modes.
0b - Enable
1b - Disable
16 Individual RX Masking and Queue Enable
Table continues on the next page...

---

*Page 2364*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
IRMQ Indicates whether RX matching process is based on individual masking and queue, or based on a masking
scheme with RX Message Buffers Global Mask (RXMGMASK) , Receive 14 Mask (RX14MASK) , Receive
15 Mask (RX15MASK) , and Legacy RX FIFO Global Mask (RXFGMASK) .
When this field is disabled, for backward compatibility with legacy applications, reading the Control and
Status word locks the MB even if it is empty.
This field can be written in Freeze mode only. The module blocks it in other modes.
0b - Disable
1b - Enable
15 DMA Enable
DMA Enables DMA. The DMA feature can only be used in Legacy RX FIFO or Enhanced RX FIFO , so
MCR[RFEN] or ERFCR[ERFEN] must be 1. When DMA and RFEN are 1, IFLAG1[BUF5I] generates the
DMA request, and no RX FIFO interrupt is generated. This field can be written in Freeze mode only; the
module blocks it in other modes.
0b - Disable
1b - Enable
Reserved
14
—
13 Local Priority Enable
LPRIOEN Enables the local priority feature. It is used to expand the ID used during the arbitration process. With this
expanded ID concept, the arbitration process is done based on the full 32-bit word. However, the actual
transmitted ID is 11 bits for standard frames and 29 bits for extended frames.
This field can be written only in Freeze mode; the module blocks it in other modes.
This bit is provided for backward compatibility with legacy applications.
0b - Disable
1b - Enable
12 Abort Enable
AEN Enables the TX abort mechanism. This mechanism guarantees a safe procedure for aborting a pending
transmission, so that no frame is sent in the CAN bus without notification. This field can be written only in
Freeze mode; the module blocks it in other modes.
NOTE
When this field is 1, only use the abort mechanism (see Transmission abort mechanism ) to
update message buffers configured for transmission.
CAUTION
Writing the abort code into RX message buffers can cause unpredictable results when this
field is 1.
Table continues on the next page...

---

*Page 2365*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled
11 CAN FD Operation Enable
FDEN Enables the CAN with flexible data rate (CAN FD) operation. This field can be written in Freeze mode only.
FlexCAN can receive and transmit messages in CAN 2.0 format. If this field is enabled, FlexCAN can also
receive and transmit messages in CAN FD format.
FlexCAN can transmit FD frame format according to ISO 11898-1:2015.
NOTE
If the value of this field is 1, the Legacy RX FIFO Enable ( MCR[RFEN] ) field cannot be 1.
0b - Disable
1b - Enable
Reserved
10
—
9-8 ID Acceptance Mode
IDAM Identifies the format of the Legacy RX FIFO ID filter table elements. This field configures all elements of the
table at the same time; they are all the same format. See legacy RX FIFO structure . This field can be written
only in Freeze mode; the module blocks it in other modes.
00b - Format A: One full ID (standard and extended) per ID filter table element.
01b - Format B: Two full standard IDs or two partial 14-bit (standard and extended) IDs per ID
filter table element.
10b - Format C: Four partial 8-bit standard IDs per ID filter table element.
11b - Format D: All frames rejected.
Reserved
7
—
6-0 Number of the Last Message Buffer
MAXMB Defines the number of the last message buffer that takes part in the matching and arbitration processes. The
reset value (0Fh) is equivalent to a 16-MB configuration. This field can be written only in Freeze mode; the
module blocks it in other modes.
NOTE
You must write a value smaller than or equal to the number of available message buffers to
this field , as described in FlexCAN memory partition for CAN FD .
Additionally, the MAXMB value must consider the region of message buffers occupied by Legacy RX FIFO
and its ID filters table space defined by CTRL2[RFFN] . MAXMB also impacts the definition of the minimum
number of peripheral clocks per CAN bit, as described in Table 438 .

---

*Page 2366*

CAN (FlexCAN)
70.6.2.3 Control 1 (CTRL1)
Offset
Register Offset
CTRL1 4h
Function
Contains specific FlexCAN control features related to the CAN bus. These features include bit rate, programmable sampling point
within an RX bit, Loopback mode, Listen-Only mode, Bus Off recovery behavior, and interrupt enabling (Bus-Off, Error, Warning).
It also determines the division factor for the clock prescaler.
The CAN bit timing variables (CTRL1[PRESDIV], CTRL1[PROPSEG], CTRL1[PSEG1], CTRL1[PSEG2], and CTRL1[RJW]) can
also be configured in CAN Bit Timing (CBT) , which extends the range of all these variables. If CBT[BTF] = 1, CTRL1[PRESDIV],
CTRL1[PROPSEG], CTRL1[PSEG1], CTRL1[PSEG2], and CTRL1[RJW] become read-only.
If CTRL2[BTE] = 1, CTRL1[PRESDIV], CTRL1[PROPSEG], CTRL1[PSEG1], CTRL1[PSEG2], and CTRL1[RJW] are not used by
the module. Instead, these fields are read as zero, and a write operation to them has no effect.
NOTE
When the CAN FD feature is enabled, do not use CTRL1[PRESDIV], CTRL1[PROPSEG], CTRL1[PSEG1],
CTRL1[PSEG2], and CTRL1[RJW] for CAN bit timing. Instead use CBT[EPRESDIV], CBT[ERJW], CBT[EPSEG1],
CBT[EPSEG2], and CBT[EPROPSEG].
The CAN bit variables in CTRL1 and in CBT are stored in the same internal register.
Soft reset does not affect the contents of this register.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
PRESDIV RJW PSEG1 PSEG2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
BOFF ERRM Reserv TWRN RWRN BOFF
LPB SMP TSYN LBUF LOM PROPSEG
MSK SK ed MSK MSK REC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 Prescaler Division Factor
PRESDIV Determines the ratio between the PE clock frequency and the serial clock (Sclock) frequency. The Sclock
period defines the time quantum of the CAN protocol. For the reset value, the Sclock frequency is equal to
the PE clock frequency. The maximum value of this field is FFh, which gives a minimum Sclock frequency
Table continues on the next page...

---

*Page 2367*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
equal to the PE clock frequency divided by 256. See Protocol timing for more information. This field can be
written only in Freeze mode; the module blocks it in other modes.
Sclock frequency = PE clock frequency ÷ (PRESDIV + 1).
23-22 Resync Jump Width
RJW Defines the maximum number of time quanta that one resynchronization can change a bit time. One time
quantum is equal to one Sclock period. The valid programmable values are 0–3. See Protocol timing for
more information. This field can be written only in Freeze mode; the module blocks it in other modes.
Resync Jump Width = RJW + 1.
21-19 Phase Segment 1
PSEG1 Defines the length of phase segment 1 in the bit time. The valid programmable values are 0–7. See
Protocol timing for more information. This field can be written only in Freeze mode; the module blocks it in
other modes.
Phase Buffer Segment 1 = (PSEG1 + 1) × Time Quanta.
18-16 Phase Segment 2
PSEG2 Defines the length of phase segment 2 in the bit time. The valid programmable values are 1–7. See
Protocol timing for more information. This field can be written only in Freeze mode; the module blocks it in
other modes.
Phase Buffer Segment 2 = (PSEG2 + 1) × Time Quanta.
15 Bus Off Interrupt Mask
BOFFMSK Provides a mask for the Bus Off interrupt ESR1[BOFFINT] .
0b - Interrupt disabled
1b - Interrupt enabled
14 Error Interrupt Mask
ERRMSK Provides a mask for the Error interrupt ESR1[ERRINT] .
0b - Interrupt disabled
1b - Interrupt enabled
13 Reserved
—
NOTE
Writeable only if the module is disabled. Otherwise the access type is read-only.
12 Loopback Mode
LPB Configures FlexCAN to operate in Loopback mode. In this mode, FlexCAN performs an internal loopback
that can be used for self-test operation. The bit stream output of the transmitter is fed back internally to the
receiver input. The RX CAN input pin is ignored and the TX CAN output goes to the recessive state (logic
1). FlexCAN behaves as it normally does when transmitting, and treats its own transmitted message as a
message received from a remote node.
Table continues on the next page...

---

*Page 2368*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
In this mode, FlexCAN ignores the bit sent during the ACK slot in the CAN frame acknowledge field. It
generates an internal acknowledge bit to ensure proper reception of its own message. Both transmit and
receive interrupts are generated. This field can be written only in Freeze mode; the module blocks it in
other modes.
NOTE
In this mode, MCR[SRXDIS] cannot become 1, because it would impede the self-reception
of a transmitted message.
NOTE
FDCTRL[TDCEN] and MCR[ETDCEN] must be 0 when this field is 1.
0b - Disabled
1b - Enabled
11 TX Warning Interrupt Mask
TWRNMSK Provides a mask for the TX Warning interrupt associated with the ESR1[TWRNINT] flag. When
MCR[WRNEN] = 0, this field is read as 0. This field can be written only if MCR[WRNEN] = 1.
0b - Disabled
1b - Enabled
10 RX Warning Interrupt Mask
RWRNMSK Provides a mask for the RX Warning interrupt associated with the ESR1[RWRNINT] flag. When
MCR[WRNEN] = 0, this field is read as 0. This field can be written only if MCR[WRNEN] = 1.
0b - Disabled
1b - Enabled
Reserved
9
—
Reserved
8
—
7 CAN Bit Sampling
SMP Determines the sampling mode of CAN bits at the RX input. This field can be written in Freeze mode only;
the module blocks it in other modes.
NOTE
For proper operation, to write 1 to this field, you must guarantee a minimum value of two time
quanta in CTRL1[PSEG1] (or CBT[EPSEG1] ). This bit cannot become 1 when CAN FD is
enabled ( MCR[FDEN] = 1).
0b - One sample is used to determine the bit value.
Table continues on the next page...

---

*Page 2369*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
1b - Three samples are used to determine the value of the received bit: the regular one (sample
point) and two preceding samples. A majority rule is used.
6 Bus Off Recovery
BOFFREC Determines how FlexCAN recovers from Bus Off state. If 0, automatic recovering from Bus Off state occurs
according to the CAN Specification 2.0B. If 1, automatic recovering from Bus Off is disabled. The module
remains in Bus Off state until you write 1 to this field.
If this field becomes 0 before 128 sequences of 11 recessive bits are detected on the CAN bus, Bus Off
recovery happens as if this field had never become 1. If this field becomes 0 after 128 sequences of 11
recessive bits occurred, FlexCAN resynchronizes to the bus. It waits for 11 recessive bits before joining
the bus.
After this field becomes 0, it can become 1 again during Bus Off, but it will only be effective the next time
the module enters Bus Off. If this field becomes 0 when the module is in Bus Off, writing 1 to this field is not
effective for the current Bus Off recovery.
NOTE
See Bus Off in the CAN Protocol standard (ISO 11898-1:2015) for details.
0b - Enabled
1b - Disabled
5 Timer Sync
TSYN Enables a mechanism that resets the free-running timer each time a message is received in message
buffer 0. This feature provides the means to synchronize multiple FlexCAN stations with a special "SYNC"
message (that is, global network time). If MCR[RFEN] = 1 ( Legacy RX FIFO enabled), the first available
message buffer, according to CTRL2[RFFN] , is used for timer synchronization instead of MB0. This field can
be written in Freeze mode only; the module blocks it in other modes.
0b - Disable
1b - Enable
4 Lowest Buffer Transmitted First
LBUF Determines the ordering mechanism for message buffer transmission. When 1, MCR[LPRIOEN] does
not affect the priority arbitration. This field can be written in Freeze mode only; the module blocks it in
other modes.
0b - Buffer with highest priority is transmitted first.
1b - Lowest number buffer is transmitted first.
3 Listen-Only Mode
LOM Configures FlexCAN to operate in Listen-Only mode. In this mode, transmission is disabled, all
error counters described in Error Counter (ECR) are frozen, and the module operates in CAN Error
Passive mode. Only messages acknowledged by another CAN station are received. If FlexCAN
detects an unacknowledged message, it flags a BIT0 error without changing the receive error counter
( ECR[RXERRCNT] ), as if it is trying to acknowledge the message.
Table continues on the next page...

---

*Page 2370*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
FlexCAN acknowledges Listen-Only mode when ESR1[FLTCONF] = 1, indicating the Error Passive state.
There can be some delay between the Listen-Only mode request and its acknowledgment.
This field can be written in Freeze mode only; the module blocks it in other modes.
0b - Listen-Only mode is deactivated.
1b - FlexCAN module operates in Listen-Only mode.
2-0 Propagation Segment
PROPSEG Defines the length of the propagation segment in the bit time. The valid programmable values are 0–7. This
field can be written only in Freeze mode; the module blocks it in other modes.
Propagation segment time = (PROPSEG + 1) × Time Quanta.
One Time Quantum = one Sclock period.
70.6.2.4 Free-Running Timer (TIMER)
Offset
Register Offset
TIMER 8h
Function
Represents a 16-bit free-running counter that the CPU can read and write. The timer starts from 0h after reset, counts linearly to
FFFFh, and wraps around.
When CTRL2[TIMER_SRC] = 1, an external time tick continuously increments the timer. The time tick must be synchronous to
the peripheral clock, with a minimum pulse width of one clock cycle.
When CTRL2[TIMER_SRC] = 0, the CAN bit clock increments the timer, which defines the baud rate on the CAN bus. During a
message transmission or reception, it increments by one for each bit that is received or transmitted. When there is no message
on the bus, it counts using the previously programmed baud rate. The timer is not incremented during Disable and Freeze modes.
The timer value is captured when the second bit of the identifier field of any frame is on the CAN bus. This captured value is written
into the timestamp entry in a message buffer after a successful reception or transmission of a message.
If CTRL1[TSYN] = 1, the timer is reset whenever a message is received in the first available message buffer, according
to CTRL2[RFFN] .
The CPU can write to this register anytime. However, if the write occurs simultaneously with the timer being reset by a reception
in the first message buffer, the write value is discarded.
Reading this register affects the message buffer unlocking procedure (see Message buffer lock mechanism ).

---

*Page 2371*

CAN (FlexCAN)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TIMER
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-16
—
15-0 Timer Value
TIMER Contains the free-running counter value.
70.6.2.5 RX Message Buffers Global Mask (RXMGMASK)
Offset
Register Offset
RXMGMASK 10h
Function
Masks the filter bits of all RX message buffers, excluding MB14 and MB15, which have individual mask registers.
This register is located in RAM.
RXMGMASK is provided for legacy application support.
• When MCR[IRMQ] is 0, RXMGMASK is always in effect. The bits in RXMGMASK[MG] mask the MB filter bits.
• When MCR[IRMQ] is 1, RXMGMASK has no effect. The bits in RXMGMASK[MG] do not mask the MB filter bits.
This register can only be written in Freeze mode; the module blocks it in other modes.

---

*Page 2372*

CAN (FlexCAN)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
MG
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
MG
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
31-0 Global Mask for RX Message Buffers
MG Masks the message buffer filter bits. The alignment with the ID word of the message buffer is imperfect. The
two most significant MG bits affect the fields RTR and IDE, which are located in the Control and Status word
of the MB. The following table shows which MG bits mask each MB filter field.
1
SMB[RTR] CTRL2[RRS] CTRL2[EACE Message buffer filter fields
N]
MB[RTR] MB[IDE] MB[ID] Reserved
2 3
0 - 0 Note Note MG[28:0] MG[31:29]
0 - 1 MG[31] MG[30] MG[28:0] MG[29]
1 0 - - - - MG[31:0]
1 1 0 - - MG[28:0] MG[31:29]
1 1 1 MG[31] MG[30] MG[28:0] MG[29]
1. RTR bit of the incoming frame. It is saved into an auxiliary MB called RX serial message buffer
(RX SMB).
2. If CTRL2[EACEN] is 0, the RTR bit of MB is never compared with the RTR bit of the incoming frame.
3. If CTRL2[EACEN] is 0, the IDE bit of MB is always compared with the IDE bit of the incoming frame.
0b - The corresponding bit in the filter is "don't care."
1b - The corresponding bit in the filter is checked.
70.6.2.6 Receive 14 Mask (RX14MASK)
Offset
Register Offset
RX14MASK 14h

---

*Page 2373*

CAN (FlexCAN)
Function
Masks the filter fields of MB14.
This register is located in RAM.
RX14MASK is provided for legacy application support. When MCR[IRMQ] = 1, RX14MASK has no effect.
This register can only be programmed when the module is in Freeze mode; the module blocks it in other modes.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
RX14M
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
RX14M
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
31-0 RX Buffer 14 Mask Bits
RX14M Masks the corresponding MB14 filter field in the same way that RX Message Buffers Global Mask
(RXMGMASK) masks the filters of the other message buffers.
0b - The corresponding bit in the filter is "don't care."
1b - The corresponding bit in the filter is checked.
70.6.2.7 Receive 15 Mask (RX15MASK)
Offset
Register Offset
RX15MASK 18h
Function
Masks the filter fields of MB15.
This register is located in RAM.
RX15MASK is provided for legacy application support. When MCR[IRMQ] = 1, RX15MASK has no effect.
This register can be programmed only when the module is in Freeze mode; the module blocks it in other modes.

---

*Page 2374*

CAN (FlexCAN)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
RX15M
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
RX15M
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
31-0 RX Buffer 15 Mask Bits
RX15M Masks the corresponding MB15 filter field in the same way that RX Message Buffers Global Mask
(RXMGMASK) masks the filters of other message buffers.
0b - The corresponding bit in the filter is "don't care."
1b - The corresponding bit in the filter is checked.
70.6.2.8 Error Counter (ECR)
Offset
Register Offset
ECR 1Ch
Function
Contains error counters for received and transmitted messages.
TXERRCNT and RXERRCNT consider all errors in both CAN FD and non-FD message formats. TXERRCNT_FAST and
RXERRCNT_FAST count only the errors that occur in the data phase of CAN FD frames that have BRS = 1.
The Fault Confinement state ( ESR1[FLTCONF] ) is updated based on TXERRCNT and RXERRCNT counters only . The rules for
increasing and decreasing these counters are described in the CAN protocol and are entirely implemented in FlexCAN.
The basic rules for FlexCAN bus state transitions are:
• If the value of TXERRCNT or RXERRCNT becomes greater than or equal to 128, ESR1[FLTCONF] is updated to reflect
Error Passive state.
• If the state of FlexCAN is Error Passive, and TXERRCNT or RXERRCNT decrements to a value less than 128 when the
other already satisfies this condition, ESR1[FLTCONF] is updated to reflect Error Active state.
• If the value of TXERRCNT becomes greater than 255, ESR1[FLTCONF] is updated to reflect Bus Off state, and an
interrupt may be issued. The value of TXERRCNT is then reset to zero.
• If FlexCAN is in Bus Off, TXERRCNT is cascaded with another internal counter to count the occurrences of 11
consecutive recessive bits on the bus. TXERRCNT is reset to zero. It counts in a manner where the internal counter

---

*Page 2375*

CAN (FlexCAN)
counts 11 such bits and then wraps around when incrementing the TXERRCNT. When TXERRCNT reaches the value
of 128, ESR1[FLTCONF] is updated to Error Active, and both error counters are reset to zero. Upon any instance of a
dominant bit following a stream of less than 11 consecutive recessive bits, the internal counter resets itself to zero without
affecting the TXERRCNT value. The TXERRCNT_FAST counter is frozen during Bus Off.
• If only one node is operating during system startup, its TXERRCNT increases upon each attempted message
transmission, as a result of acknowledge errors (indicated by ESR1[ACKERR] ). After the transition to Error Passive state,
TXERRCNT no longer increments upon acknowledge errors. The chip never goes into the Bus Off state.
• If RXERRCNT increases to a value greater than 127, it is not incremented further, even if more errors are detected when
being a receiver. At the next successful message reception, the counter is set to a value between 119 and 127 to return to
the Error Active state.
• TXERRCNT_FAST and RXERRCNT_FAST error counter values increment and decrement based on errors detected only
in the data phase of CAN FD frames that have BRS = 1. These counters follow the same increment and decrement rules
as TXERRCNT and RXERRCNT. These counters do not wrap around and get stuck at their maximum value (255). They
stop counting and keep their values frozen when FlexCAN is in the Bus Off state. They are reset when FlexCAN leaves
the Bus Off state and resume counting after FlexCAN returns to the Error Active state.
NOTE
See Fault confinement in the CAN Protocol standard (ISO 11898-1:2015) for details.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXERRCNT_FAST TXERRCNT_FAST
W 0 0
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
RXERRCNT TXERRCNT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 Receive Error Counter for Fast Bits
RXERRCNT_F Counts errors detected in the data phase of received CAN FD messages that have BRS = 1. This field is
AST read-only except in Freeze mode, when the CPU can write an 8-bit zero value only.
23-16 Transmit Error Counter for Fast Bits
TXERRCNT_FA Counts errors detected in the data phase of transmitted CAN FD messages that have BRS = 1. This field
ST is read-only except in Freeze mode, when the CPU can write an 8-bit zero value only.
15-8 Receive Error Counter
RXERRCNT Counts all errors detected in received messages. This field is read-only except in Freeze mode, when
the CPU can write to it.
Table continues on the next page...

---

*Page 2376*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
7-0 Transmit Error Counter
TXERRCNT Counts all errors detected in transmitted messages. This field is read-only except in Freeze mode, when
the CPU can write to it.
70.6.2.9 Error and Status 1 (ESR1)
Offset
Register Offset
ESR1 20h
Function
Reports various error conditions detected in the reception and transmission of a CAN frame. This register provides status
information about the chip, and is the source of some interrupts to the CPU. The reported error conditions are:
NOTE
Reading host can clear these fields.
• Errors detected in CAN frames of any format:
— BIT1ERR
— BIT0ERR
— ACKERR
— CRCERR
— FRMERR
— STFERR
• Errors detected in the data phase of CAN FD frames with the BRS bit set only:
— BIT1ERR_FAST
— BIT0ERR_FAST
— CRCERR_FAST
— FRMERR_FAST
— STFERR_FAST
One or more error flags may report an error detected in a single CAN frame. To account for more error events occurring in
subsequent frames when the CPU does not attempt to read this register, error reporting is cumulative.
Status flags:
• TXWRN
• RXWRN
• IDLE
• TX

---

*Page 2377*

CAN (FlexCAN)
• FLTCONF
• RX
• SYNCH
Interrupt flags:
• BOFFINT
• BOFFDONEINT
• ERRINT
• ERRINT_FAST
• TWRNINT
• RWRNINT
The CPU should follow this procedure when servicing interrupt requests generated by these flags:
1. Read this register to capture all error condition and status flags. This action clears the respective flags that were set
since the last read access.
2. Write 1 to clear the interrupt flag that triggered the interrupt request.
3. Write 1 to clear the ERROVR flag, if it is set.
Starting from all error flags cleared, a first error event sets either ERRINT or ERRINT_FAST (provided the corresponding mask
bit is 1). If other error events in subsequent frames occur before the CPU serves the interrupt request, the ERROVR flag is set to
indicate that errors from different frames have accumulated.
Table 444. CAN bus status
SYNCH IDLE TX RX FlexCAN state
0 0 0 0 Not synchronized to
CAN bus
1 1 X X Idle
1 0 1 0 Transmitting
1 0 0 1 Receiving
NOTE
See Fault confinement in the CAN Protocol standard (ISO 11898-1:2015) for details.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
BIT1E BIT0E CRCE FRME STFE ERRO ERRIN BOFF SYNC TWRN RWRN
R 0 0 0 0
RR ... RR ... RR_ ... RR_ ... RR_ ... VR T_ ... DON ... H INT INT
W W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
BIT1E BIT0E ACKE CRCE FRME STFE TXWR RXWR BOFFI ERRIN
R IDLE TX FLTCONF RX 0
RR RR RR RR RR RR N N NT T
W W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2378*

CAN (FlexCAN)
Fields
Field Function
31 Fast Bit1 Error Flag
BIT1ERR_FAS Indicates when an inconsistency occurs between the transmitted and the received bit in the data phase
T of CAN FD frames that have BRS = 1.
After a read operation, the field's value clears to 0.
0b - No such occurrence.
1b - At least one bit transmitted as recessive is received as dominant.
30 Fast Bit0 Error Flag
BIT0ERR_FAS Indicates when an inconsistency occurs between the transmitted and the received bit in the data phase
T of CAN FD frames that have BRS = 1.
After a read operation, the field's value clears to 0.
0b - No such occurrence.
1b - At least one bit transmitted as dominant is received as recessive.
Reserved
29
—
28 Fast Cyclic Redundancy Check Error Flag
CRCERR_FAS Indicates that the receiver node has detected a CRC error in the CRC field of CAN FD frames that have
T BRS = 1. This error means that the calculated CRC is different from the received CRC.
After a read operation, the field's value clears to 0.
0b - No such occurrence.
1b - A CRC error occurred since last read of this register.
27 Fast Form Error Flag
FRMERR_FAS Indicates whether the receiver node has detected a form error in the data phase of CAN FD frames that
T have BRS = 1. This error means that a fixed-form bit field contains at least one illegal bit.
After a read operation, the field's value clears to 0.
0b - No such occurrence.
1b - A form error occurred since last read of this register.
26 Fast Stuffing Error Flag
STFERR_FAST Indicates that a stuffing error has been detected in the data phase of CAN FD frames that have BRS = 1.
After a read operation, the field's value clears to 0.
0b - No such occurrence.
1b - A stuffing error occurred since last read of this register.
Reserved
25-24
—
Table continues on the next page...

---

*Page 2379*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
Reserved
23
—
Reserved
22
—
21 Error Overrun Flag
ERROVR Indicates that an error condition occurred when any error flag is already set.
0b - No overrun
1b - Overrun
20 Fast Error Interrupt Flag
ERRINT_FAST Indicates that at least one error flag detected in the data phase of CAN FD frames that have BRS = 1
(BIT1ERR_FAST, BIT0ERR_FAST, CRCERR_FAST, FRMERR_FAST, or STFERR_FAST) is set. If
CTRL2[ERRMSK_FAST] = 1, an interrupt is generated to the CPU.
0b - No such occurrence.
1b - Error flag set in the data phase of CAN FD frames that have BRS = 1.
19 Bus Off Done Interrupt Flag
BOFFDONEINT Indicates whether ECR[TXERRCNT] has finished counting 128 occurrences of 11 consecutive recessive
bits on the CAN bus and is ready to leave Bus Off. If CTRL2[BOFFDONEMSK] = 1, an interrupt is
generated to the CPU.
0b - No such occurrence
1b - FlexCAN module has completed Bus Off process.
18 CAN Synchronization Status Flag
SYNCH Indicates whether FlexCAN is synchronized to the CAN bus and able to participate in the communication
process. FlexCAN sets and clears this flag. See the table in Error and Status 1 (ESR1) .
0b - Not synchronized
1b - Synchronized
17 TX Warning Interrupt Flag
TWRNINT Indicates whether TX error counter changed from less than 96 to greater than or equal to 96.
If MCR[WRNEN] = 1, this flag is set when the TXWRN flag transitions from 0 to 1, meaning that the TX error
counters reached 96. If CTRL1[TWRNMSK] = 1, an interrupt is sent to the CPU. When MCR[WRNEN] = 0,
this flag is masked. The CPU must clear this flag before writing 0 to MCR[WRNEN]. Otherwise, this flag is
set when MCR[WRNEN] = 1 again. Writing 0 has no effect.
This flag is not generated when in the Bus Off state. This flag is not updated during Freeze mode.
0b - No such occurrence
1b - TX error counter changed from less than 96 to greater than or equal to 96.
Table continues on the next page...

---

*Page 2380*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
16 RX Warning Interrupt Flag
RWRNINT Indicates whether the RX error counter changed from less than 96 to greater than or equal to 96.
If MCR[WRNEN] = 1, this flag is set when the RXWRN flag transitions from 0 to 1, meaning that the RX error
counters reached 96. If CTRL1[RWRNMSK] = 1, an interrupt is sent to the CPU. When MCR[WRNEN] = 0,
this flag is masked. The CPU must clear this flag before writing 0 to MCR[WRNEN]. Otherwise, this flag is
set when MCR[WRNEN] = 1 again. Writing 0 has no effect.
This flag is not updated during Freeze mode.
0b - No such occurrence
1b - RX error counter changed from less than 96 to greater than or equal to 96.
15 Bit1 Error Flag
BIT1ERR Indicates when an inconsistency occurs between the transmitted and the received bit in a non-CAN FD
message or in the arbitration or data phase of a CAN FD message.
NOTE
A transmitter does not set this flag for an arbitration field or ACK slot. It is not set for a node
sending an error passive flag that detects dominant bits.
After a read operation, the field's value clears to 0.
0b - No such occurrence.
1b - At least one bit sent as recessive is received as dominant.
14 Bit0 Error Flag
BIT0ERR Indicates when an inconsistency occurs between the transmitted and the received bit in a non-CAN FD
message or in the arbitration or data phase of a CAN FD message.
After a read operation, the field's value clears to 0.
0b - No such occurrence.
1b - At least one bit sent as dominant is received as recessive.
13 Acknowledge Error Flag
ACKERR Indicates whether the transmitter node has detected an acknowledge error. This error means that a
dominant bit has not been detected during the ACK SLOT.
After a read operation, the field's value clears to 0.
0b - No error
1b - Error occurred since last read of this register.
12 Cyclic Redundancy Check Error Flag
CRCERR Indicates whether the receiver node has detected a cyclic redundancy check (CRC) error either in a non-FD
message or in the arbitration or data phase of a frame in CAN FD format . This error means that the
calculated CRC is different from the received.
After a read operation, the field's value clears to 0.
Table continues on the next page...

---

*Page 2381*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
0b - No error
1b - Error occurred since last read of this register.
11 Form Error Flag
FRMERR Indicates whether a form error has been detected in a non-FD message or in the arbitration or data phase of
an FD message by the receiver node. This error means that a fixed-form field contains at least one illegal bit.
After a read operation, the field's value clears to 0.
0b - No error
1b - Error occurred since last read of this register.
10 Stuffing Error Flag
STFERR Indicates that a stuffing error has been detected in a non-FD message or in the arbitration or data phase of
an FD message by the receiver node.
After a read operation, the field's value clears to 0.
0b - No error
1b - Error occurred since last read of this register.
9 TX Error Warning Flag
TXWRN Indicates when repetitive errors occur during message transmission. Only the value of ECR[TXERRCNT]
affects this flag. This flag is not updated during Freeze mode.
After a read operation, the field's value clears to 0.
0b - No such occurrence.
1b - TXERRCNT is 96 or greater.
8 RX Error Warning Flag
RXWRN Indicates when repetitive errors occur during message reception. Only the value of ECR[RXERRCNT]
affects this flag. This flag is not updated during Freeze mode.
After a read operation, the field's value clears to 0.
0b - No such occurrence.
1b - RXERRCNT is greater than or equal to 96.
7 Idle
IDLE Indicates whether CAN bus is in IDLE state. See Table 444 .
0b - Not IDLE
1b - IDLE
6 FlexCAN In Transmission
TX Indicates whether FlexCAN is transmitting a message. See Table 444 .
Table continues on the next page...

---

*Page 2382*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
0b - Not transmitting
1b - Transmitting
5-4 Fault Confinement State
FLTCONF Indicates the confinement state of FlexCAN.
If CTRL1[LOM] = 1, after a delay that depends on the CAN bit timing, this field indicates Error Passive. The
same delay affects the way that this field reflects an update to ECR register by the CPU. It may be necessary
to wait up to one CAN bit time for coherence to be restored.
Soft reset affects this field, but if CTRL1[LOM] = 1, its reset value lasts for only one CAN bit. After that time,
this field reports Error Passive.
00b - Error Active
01b - Error Passive
1xb - Bus Off
3 FlexCAN in Reception Flag
RX Indicates whether FlexCAN is receiving a message. See the table in Error and Status 1 (ESR1) .
0b - Not receiving
1b - Receiving
2 Bus Off Interrupt Flag
BOFFINT Indicates whether FlexCAN has entered Bus Off state. If CTRL1[BOFFMSK] ) = 1, an interrupt is
generated to the CPU. Writing 0 to this field has no effect.
0b - No such occurrence.
1b - FlexCAN module entered Bus Off state.
1 Error Interrupt Flag
ERRINT Indicates that at least one of the error flags ( ESR1[BIT1ERR] , ESR1[BIT0ERR] , ESR1[ACKERR] ,
ESR1[CRCERR] , ESR1[FRMERR] , or ESR1[STFERR] ) is set. If the corresponding mask
CTRL1[ERRMSK] = 1, an interrupt is generated to the CPU. Writing 0 to this field has no effect.
0b - No such occurrence.
1b - Indicates setting of any error flag in the Error and Status register.
Reserved
0
—

---

*Page 2383*

CAN (FlexCAN)
70.6.2.10 Interrupt Masks 2 (IMASK2)
Offset
Register Offset
IMASK2 24h
Function
Masks interrupt flags. This register allows any of the 32 message buffer interrupts to be enabled or disabled for MB63–MB32. It
contains one interrupt mask bit per buffer. This configuration allows the CPU to determine which buffer generates an interrupt after
a successful transmission or reception when the corresponding Interrupt Flags 2 (IFLAG2) flag is set.
NOTE
Each module instance supports a different number of registers.
Instance Register supported Register not supported
FLEXCAN_0 IMASK2 —
FLEXCAN_1 IMASK2 —
FLEXCAN_2 IMASK2 —
FLEXCAN_3 — IMASK2
FLEXCAN_4 — IMASK2
FLEXCAN_5 — IMASK2
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
BUF63TO32M
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
BUF63TO32M
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Buffer MBi Mask
Table continues on the next page...

---

*Page 2384*

CAN (FlexCAN)
Field Function
BUF63TO32M Masks the corresponding FlexCAN message buffer interrupt for MB63–MB32.
NOTE
If the corresponding Interrupt Flags 2 (IFLAG2) flag is set, writing 1 or 0 to a field in IMASK2
can set or clear an interrupt request.
0b - The corresponding buffer interrupt is disabled.
1b - The corresponding buffer interrupt is enabled.
70.6.2.11 Interrupt Masks 1 (IMASK1)
Offset
Register Offset
IMASK1 28h
Function
Masks interrupt flags. This register allows any of the 32 message buffer interrupts to be enabled or disabled for MB31 –MB0. It
contains one interrupt mask bit per buffer. This configuration allows the CPU to determine which buffer generates an interrupt after
a successful transmission or reception when the corresponding Interrupt Flags 1 (IFLAG1) flag is set.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
BUF31TO0M
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
BUF31TO0M
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Buffer MBi Mask
BUF31TO0M Enables or disables the corresponding FlexCAN message buffer interrupt for MB31–MB0.
NOTE
If the corresponding Interrupt Flags 1 (IFLAG1) flag is set, writing 1 or 0 to a field in IMASK1
can set or clear an interrupt request.
Table continues on the next page...

---

*Page 2385*

CAN (FlexCAN)
Field Function
0b - The corresponding buffer interrupt is disabled.
1b - The corresponding buffer interrupt is enabled.
70.6.2.12 Interrupt Flags 2 (IFLAG2)
Offset
Register Offset
IFLAG2 2Ch
Function
Contains the flags for the 32 message buffer interrupts for MB63–MB32. It contains one interrupt flag bit per buffer. Each
successful transmission or reception sets the respective flag in this register. If the corresponding Interrupt Masks 2 (IMASK2) bit
is set, an interrupt is generated.
Before updating MCR[MAXMB] , the CPU must service the IFLAG2 flags whose MB value is greater than the MAXMB to be
updated. Otherwise, those flags remain set and are inconsistent with the number of message buffers available.
NOTE
Each module instance supports a different number of registers.
Instance Register supported Register not supported
FLEXCAN_0 IFLAG2 —
FLEXCAN_1 IFLAG2 —
FLEXCAN_2 IFLAG2 —
FLEXCAN_3 — IFLAG2
FLEXCAN_4 — IFLAG2
FLEXCAN_5 — IFLAG2

---

*Page 2386*

CAN (FlexCAN)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R BUF63TO32I
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R BUF63TO32I
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Buffer MBi Interrupt
BUF63TO32I Flags the corresponding FlexCAN message buffer interrupt for MB63–MB32.
0b - The corresponding buffer has no occurrence of successfully completed transmission or
reception.
1b - The corresponding buffer has successfully completed transmission or reception.
70.6.2.13 Interrupt Flags 1 (IFLAG1)
Offset
Register Offset
IFLAG1 30h
Function
Contains the flags for the 32 message buffer interrupts for MB31 –MB0. It contains one interrupt flag bit per buffer. Each successful
transmission or reception sets the corresponding IFLAG1 bit. If the corresponding Interrupt Masks 1 (IMASK1) bit is set, an
interrupt is generated. There is an exception when DMA for Legacy RX FIFO is enabled, as described below.
The BUF7I–BUF5I flags also represent Legacy FIFO interrupts when the Legacy RX FIFO is enabled. When MCR[RFEN] is 1 and
MCR[DMA] is 0 , the function of the eight least significant interrupt flags changes:
• BUF7I, BUF6I, and BUF5I indicate operating conditions of the Legacy FIFO.
• BUF4I–BUF1I fields are reserved.
• BUF0I empties the Legacy FIFO.
Before writing 1 to MCR[RFEN], the CPU must service the IFLAG flags set in the Legacy RX FIFO region; see Legacy RX FIFO .
Otherwise, these IFLAG flags mistakenly show the related message buffers now belonging to Legacy FIFO as having contents to
be serviced. When MCR[RFEN] is 0, the Legacy FIFO flags must be cleared. The same care must be taken when a CTRL2[RFFN]
value is selected, extending Legacy RX FIFO filters beyond MB7. For example, when RFFN is 8h, Legacy RX FIFO filters occupy
the MB23–MB0 range, and related IFLAG flags must be cleared.

---

*Page 2387*

CAN (FlexCAN)
When MCR[RFEN] and MCR[DMA] are 1 (DMA feature for Legacy RX FIFO enabled), the function of the eight least significant
interrupt flags (BUF7I–BUF0I) changes to support DMA operation. BUF7I, BUF6I, and BUF4I–BUF1I are not used.
BUF5I indicates the operating condition of the Legacy FIFO, and BUF0I empties the Legacy FIFO. Moreover, BUF5I does not
generate a CPU interrupt, but it does generate a DMA request. IMASK1 bits in the Legacy RX FIFO region are not considered
when bit MCR[DMA] = 1. In addition, the CPU must not clear the BUF5I flag when DMA is enabled. Before writing 1 to MCR[DMA],
the CPU must service the IFLAG flags set in the Legacy RX FIFO region. When MCR[DMA] is 0, the Legacy FIFO must be empty.
Legacy FIFO must be disabled when MCR[FDEN] = 1.
Before updating MCR[MAXMB] , the CPU must service the IFLAG1 flags whose MB value is greater than the MCR[MAXMB] to be
updated. Otherwise, those flags remain set and are inconsistent with the number of message buffers available.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R BUF31TO8I
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R BUF31TO8I BUF7I BUF6I BUF5I BUF4TO1I BUF0I
W W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-8 Buffer MBi Interrupt
BUF31TO8I Flags the corresponding FlexCAN message buffer interrupt for MB31–MB8.
0b - The corresponding buffer has no occurrence of successfully completed transmission or
reception.
1b - The corresponding buffer has successfully completed transmission or reception.
7 Buffer MB7 Interrupt or Legacy RX FIFO Overflow
BUF7I Flags the interrupt for MB7 when MCR[RFEN] = 0 ( Legacy RX FIFO disabled).
NOTE
When a CPU write changes the value of MCR[RFEN] , FlexCAN clears this flag.
When MCR[RFEN] = 1, this flag represents a Legacy RX FIFO overflow. In this case, the flag indicates that
a message was lost because the Legacy RX FIFO is full. When the Legacy RX FIFO is full and a message
buffer captures the message, this flag is not set.
0b - No occurrence of MB7 completing transmission or reception, or no FIFO overflow.
1b - MB7 completed transmission or reception, or FIFO overflow.
6 Buffer MB6 Interrupt or Legacy RX FIFO Warning
BUF6I Flags the interrupt for MB6 when MCR[RFEN] = 0 ( Legacy RX FIFO disabled).
Table continues on the next page...

---

*Page 2388*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
NOTE
When a CPU write changes the value of MCR[RFEN] , FlexCAN clears this flag.
When MCR[RFEN] = 1, this flag represents a Legacy RX FIFO warning. In this case, the flag indicates
when the number of unread messages within the Legacy RX FIFO is increased to five from four due to the
reception of a new message. In other words, the Legacy RX FIFO is almost full.
If this flag is cleared when there are more than four unread messages, it does not set again until the number
of unread messages in the Legacy RX FIFO decreases to four or fewer.
0b - No occurrence of MB6 completing transmission or reception, or FIFO not almost full.
1b - MB6 completed transmission or reception, or FIFO almost full.
5 Buffer MB5 Interrupt or Frames available in Legacy RX FIFO
BUF5I Flags the interrupt for MB5 when MCR[RFEN] = 0 ( Legacy RX FIFO disabled).
NOTE
When a CPU write changes the value of MCR[RFEN] , FlexCAN clears this flag.
When MCR[RFEN] = 1, the BUF5I flag represents frames available in Legacy RX FIFO. In this case, the flag
indicates that at least one frame is available to be read from the Legacy RX FIFO.
When MCR[DMA] = 1, this flag generates a DMA request. The CPU must not clear this field by writing 1
to BUF5I.
0b - No occurrence of completed transmission or reception, or no frames available
1b - MB5 completed transmission or reception, or frames available
4-1 Buffer MBi Interrupt or Reserved
BUF4TO1I Flags the interrupts for MB4–MB1 when MCR[RFEN] = 0 ( Legacy RX FIFO disabled).
NOTE
When a CPU write changes the value of MCR[RFEN] , FlexCAN clears these flags.
When MCR[RFEN] = 1, the BUF4TO1I flags are reserved.
0b - The corresponding buffer has no occurrence of successfully completed transmission or
reception.
1b - The corresponding buffer has successfully completed transmission or reception.
0 Buffer MB0 Interrupt or Clear Legacy FIFO bit
BUF0I Flags the interrupt for MB0 when MCR[RFEN] = 0 ( Legacy RX FIFO disabled).
If MCR[RFEN] = 1, this field is used to trigger the clear Legacy FIFO operation. This operation empties the
Legacy FIFO contents. Before performing this operation, the CPU must service all Legacy FIFO-related
IFLAG flags.
When MCR[DMA] = 1, this operation also clears the BUF5I flag, aborting the DMA request. The clear Legacy
FIFO operation occurs when the CPU writes 1 to BUF0I. This operation is only allowed in Freeze mode; the
module blocks it in other conditions.
Table continues on the next page...

---

*Page 2389*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
0b - MB0 has no occurrence of successfully completed transmission or reception.
1b - MB0 has successfully completed transmission or reception.
70.6.2.14 Control 2 (CTRL2)
Offset
Register Offset
CTRL2 34h
Function
Complements Control 1 (CTRL1) , providing control bits for memory write-access in Freeze mode. This register extends Legacy
FIFO filter quantity, and adjusts the operation of internal FlexCAN processes such as matching and arbitration.
Soft reset does not affect the contents of this register.
Table 445 shows how the value of CTRL2[RFFN] determines the Legacy RX FIFO filter structure.
Table 445. Possible Legacy RX FIFO filter structures
RFFN[3:0] Number of Message buffers Remaining available Legacy RX FIFO ID filter Legacy RX FIFO ID filter
Legacy RX occupied by Legacy message buffers table elements affected table elements affected
FIFO filter RX FIFO and ID filter by RX individual masks by Legacy RX FIFO
elements table global mask
0h 8 MB 0–7 MB 8– 95 Elements 0–7 None
1h 16 MB 0–9 MB 10– 95 Elements 0–9 Elements 10–15
2h 24 MB 0–11 MB 12– 95 Elements 0–11 Elements 12–23
3h 32 MB 0–13 MB 14– 95 Elements 0–13 Elements 14–31
4h 40 MB 0–15 MB 16–95 Elements 0–15 Elements 16–39
5h 48 MB 0–17 MB 18– 95 Elements 0–17 Elements 18–47
6h 56 MB 0–19 MB 20– 95 Elements 0–19 Elements 20–55
7h 64 MB 0–21 MB 22– 95 Elements 0–21 Elements 22–63
8h 72 MB 0–23 MB 24– 95 Elements 0–23 Elements 24–71
9h 80 MB 0–25 MB 26– 95 Elements 0–25 Elements 26–79
Ah 88 MB 0–27 MB 28– 95 Elements 0–27 Elements 28–87
Bh 96 MB 0–29 MB 30– 95 Elements 0–29 Elements 30–95
Ch 104 MB 0–31 MB 32–95 Elements 0–31 Elements 32–103
Dh 112 MB 0–33 MB 34– 95 Elements 0–31 Elements 32–111
Table continues on the next page...

---

*Page 2390*

CAN (FlexCAN)
Table 445. Possible Legacy RX FIFO filter structures (continued)
RFFN[3:0] Number of Message buffers Remaining available Legacy RX FIFO ID filter Legacy RX FIFO ID filter
Legacy RX occupied by Legacy message buffers table elements affected table elements affected
FIFO filter RX FIFO and ID filter by RX individual masks by Legacy RX FIFO
elements table global mask
Eh 120 MB 0–35 MB 36– 95 Elements 0–31 Elements 32–119
Fh 128 MB 0–37 MB 38– 95 Elements 0–31 Elements 32–127
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
ERRM BOFF ECRW WRMF EACE
RFFN TASD MRP RRS
SK_ ... DON ... RE RZ N
W
Reset See Register reset values .
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0
TIMER PREX ISOCA EDFLT
BTE MBTSBASE TSTAMPCAP
_S ... CEN NF ... DIS
W
Reset See Register reset values .
Register reset values
Register Reset value
CTRL2
FLEXCAN_0: 0060_0000h
FLEXCAN_1,FLEXCAN_2: 0080_0000h
FLEXCAN_3–FLEXCAN_5: 00A0_0000h
Fields
Field Function
31 Error Interrupt Mask for Errors Detected in the Data Phase of Fast CAN FD Frames
ERRMSK_FAS Enables the ESR1[ERRINT_FAST] interrupt.
T
0b - Disable
1b - Enable
30 Bus Off Done Interrupt Mask
BOFFDONEMS Enables the Bus Off Done interrupt, ESR1[BOFFDONEINT] .
K
0b - Disable
1b - Enable
29 Error Correction Configuration Register Write Enable
Table continues on the next page...

---

*Page 2391*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
ECRWRE Enables updates for Memory Error Control (MECR) . If the protocol described in Detection and correction of
memory errors is not followed, this field is automatically 0.
0b - Disable
1b - Enable
28 Write Access to Memory in Freeze Mode
WRMFRZ Enables unrestricted write access to FlexCAN memory in Freeze mode. When this field is 0, write access
restrictions are maintained. This field can only be written in Freeze mode, and has no effect out of
Freeze mode.
NOTE
Do not write 1 to MCR[RFEN] during FlexCAN memory initialization.
0b - Disable
1b - Enable
27-24 Number of Legacy Receive FIFO Filters
RFFN Defines the number of Receive Legacy FIFO filters, as shown in Table 445 . The chip determines the
maximum selectable number of filters. Do not program this field with values that cause the number of
message buffers occupied by Legacy RX FIFO and Legacy RX FIFO ID Filter to exceed MCR[MAXMB] .
MCR[MAXMB] defines the number of message buffers present.
This field can only be written in Freeze mode; the module blocks it in other modes.
Each group of eight filters occupies a memory space equivalent to two message buffers. The more filters
are implemented, the fewer message buffers are available.
The Legacy RX FIFO occupies the memory space originally reserved for MB5–MB0. This field should be
programmed with a value corresponding to a number of filters less than the number of available memory
words. The number of available words can be calculated as follows:
(SETUP_MB - 6) × 4
Where SETUP_MB is the smaller of the parameter NUMBER_OF_MB and MCR[MAXMB].
The number of remaining message buffers available is:
(SETUP_MB - 8) - (RFFN × 2)
If the number of Legacy RX FIFO filters programmed through RFFN exceeds the SETUP_MB value
(memory space available), the exceeding ones are not functional.
NOTE
• The number of the last remaining available message buffers is the smaller of
(NUMBER_OF_MB - 1) and MCR[MAXMB].
• If RX Individual Mask registers are not enabled, the Legacy RX FIFO Global Mask
affects all Legacy RX FIFO filters.
23-19 Transmission Arbitration Start Delay
TASD
Table continues on the next page...

---

*Page 2392*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
Indicates by how many CAN bits the transmission arbitration process start point can be delayed from the
first bit of CRC field on CAN bus. See TX arbitration start delay for details. This field can be written only in
Freeze mode; the module blocks it in other modes.
18 Message Buffers Reception Priority
MRP Sets the priority for the matching process.
This field can be written only in Freeze mode; the module blocks it in other modes.
NOTE
The descriptions of the field settings vary by module instance.
Instance Field value and description
FLEXCAN_0 0b - Matching starts from Legacy RX FIFO or Enhanced RX FIFO and
continues on message buffers.
1b - Matching starts from message buffers and continues on Legacy RX
FIFO or Enhanced RX FIFO.
FLEXCAN_1 0b - Matching starts from RX FIFO and continues on message buffers.
FLEXCAN_2
1b - Matching starts from message buffers and continues on RX FIFO.
FLEXCAN_3
FLEXCAN_4
FLEXCAN_5
17 Remote Request Storing
RRS Determines what the module does with a remote request. The remote request frame is submitted to a
matching process.
If this field is 1, the frame is stored in the corresponding message buffer in the same fashion as a data frame.
No automatic remote response frame is generated.
If this field is 0, an automatic remote response frame is generated if a message buffer with CODE = 1010b
is found with the same ID.
You can only write to this field in Freeze mode. The module blocks it in other modes.
0b - Generated
1b - Stored
16 Entire Frame Arbitration Field Comparison Enable for RX Message Buffers
EACEN Controls the comparison of IDE and RTR fields within RX message buffer filters with their corresponding bits
in the incoming frame by the matching process. If enabled, the IDE and RTR fields of the RX message buffer
are compared to their corresponding bits within the incoming frame (mask bits apply). If disabled, the IDE
field of the RX message buffer filter is always compared and RTR is never compared despite mask bits.
This field does not affect matching for Legacy RX FIFO or Enhanced RX FIFO .
You can only write to this field in Freeze mode; the module blocks it in other modes.
Table continues on the next page...

---

*Page 2393*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
0b - Disable
1b - Enable
15 Timer Source
TIMER_SRC Selects the time tick source used for incrementing the free-running timer counter.
If CAN bit clock is selected, it defines the baud rate on the CAN bus.
If External time tick is selected, the period can be adjusted to match the baud rate on the CAN bus. It can
also be adjusted to a different value as required. See the chip-specific section for details about the external
time tick.
You can only write to this field in Freeze mode.
0b - CAN bit clock
1b - External time tick
14 Protocol Exception Enable
PREXCEN Enables the protocol exception feature.
You can only write to this field in Freeze mode.
NOTE
See Protocol exception event in the CAN Protocol standard (ISO 11898-1:2015) for details.
0b - Disabled
1b - Enabled
13 Bit Timing Expansion Enable
BTE Enables the use of Enhanced CAN Bit Timing Prescalers (EPRS) , Enhanced Data Phase CAN Bit Timing
(EDCBT) , and Enhanced Nominal CAN Bit Timing (ENCBT) to configure the CAN bit timing segments,
instead of using the bit timing fields of CAN Bit Timing (CBT) , CAN FD Bit Timing (FDCBT) , and Control
1 (CTRL1) .
If this field is 1:
• CTRL1[PRESDIV] , CTRL1[PROPSEG] , CTRL1[PSEG1] , CTRL1[PSEG2] , and CTRL1[RJW] are
read as zero. A write operation to these fields has no effect.
• CBT[EPRESDIV] , CBT[ERJW] , CBT[EPROPSEG] , CBT[EPSEG1] , and CBT[EPSEG2] , and the
corresponding fields in CAN FD Bit Timing (FDCBT) , are read as zero. A write operation to these
fields has no effect.
• ETDC[ETDCOFF] , ETDC[ETDCEN] , ETDC[ETDCFAIL] , and ETDC[ETDCVAL] are used
by FlexCAN instead of FDCTRL[TDCOFF] , FDCTRL[TDCEN] , FDCTRL[TDCFAIL] , and
FDCTRL[TDCVAL] . These fields are read as zero, and a write operation to them has no effect.
• ETDC[TDMDIS] can be used to disable transceiver delay measurement.
0b - Disable
1b - Enable
Table continues on the next page...

---

*Page 2394*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
12 ISO CAN FD Enable
ISOCANFDEN Enables the CAN FD protocol according to ISO specification (ISO 11898-1:2015) (see CAN FD ISO
compliance ). When disabled, FlexCAN operates using the non-ISO CAN FD protocol.
You can only write to this field in Freeze mode.
NOTE
FlexCAN is able to transmit FD frame format according to CAN Protocol standard
(ISO 11898-1:2015).
0b - Disable
1b - Enable
11 Edge Filter Disable
EDFLTDIS Disables the edge filter used during the Bus Integration state.
When the Edge Filter is enabled, two consecutive nominal time quanta with dominant bus states are
required to detect an edge that causes synchronization. When synchronization occurs, the counting of the
sequence of 11 consecutive recessive bits is restarted. The edge filter prevents dominant pulses that are
shorter than a nominal bit time (present during the data phase of an FD frame) from being mistaken for an
idle condition.
You can only write to this field in Freeze mode.
NOTE
See Bus Integration state in the CAN Protocol standard (ISO 11898-1:2015) for details.
0b - Enabled
1b - Disabled
Reserved
10
—
9-8 Message Buffer Timestamp Base
MBTSBASE Selects the timebase used for capturing the 16-bit TIME_STAMP field of the message buffer register.
This field can be written in Freeze Mode only.
00b - TIMER
01b - Lower 16 bits of high-resolution timer
10b - Upper 16 bits of high-resolution timer
11b - Reserved
7-6 Timestamp Capture Point
TSTAMPCAP Configures the point in time when a 32-bit timebase is captured during a CAN frame. This base is stored in
the high-resolution timestamp register (HR_TIME_STAMP).
Table continues on the next page...

---

*Page 2395*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
For classical CAN frames, capture points can be the start-of-frame bit or the point when CAN frame is
considered valid. This point is the seventh bit of the end-of-frame for transmission and the sixth bit of the
end-of-frame for reception. For CAN FD frames, the high-resolution timestamp can be captured at the start
of frame, when a CAN FD frame is considered valid, or the res bit.
You can only write to this field in Freeze mode.
00b - Disabled
01b - End of the CAN frame
10b - Start of the CAN frame
11b - Start of frame for classical CAN frames; res bit for CAN FD frames
Reserved
5
—
Reserved
4-2
—
Reserved
1-0
—
70.6.2.15 Error and Status 2 (ESR2)
Offset
Register Offset
ESR2 38h
Function
Reports general status information.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 LPTM
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 VPS IMB 0 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2396*

CAN (FlexCAN)
Fields
Field Function
Reserved
31-23
—
22-16 Lowest Priority TX Message Buffer
LPTM Indicates the lowest number inactive message buffer when ESR2[VPS] = 1 (see ESR2[IMB] ). If no message
buffer is inactive, the message buffer indicated depends on the value of CTRL1[LBUF] . If CTRL1[LBUF] = 0,
the message buffer indicated is the one with the greatest arbitration value (see Highest-priority message
buffer first ). If CTRL1[LBUF] = 1, the message buffer indicated is the active TX message buffer with the
highest number.
If a TX message buffer is being transmitted, it is not considered in the LPTM calculation. If ESR2[IMB] is not
0 and a frame is transmitted successfully, the value of LPTM is updated with its message buffer number.
Reserved
15
—
14 Valid Priority Status
VPS Indicates whether the contents of ESR2[IMB] and ESR2[LPTM] are valid. It becomes 1 upon every complete
TX arbitration process, unless the CPU writes to the Control and Status word of a message buffer already
scanned. In other words, it is behind the TX Arbitration Pointer, during the TX arbitration process. If there is
no inactive message buffer and only one TX message buffer that is being transmitted, this field remains 0.
This field becomes 0 upon the start of every TX arbitration process or upon a write to the Control and Status
word of any message buffer.
NOTE
No CPU write to the Control and Status of a message buffer that the abort mechanism
blocks affects this field. When MCR[AEN] = 1, the abort code write to the Control and Status
of an MB being transmitted (pending abort) is blocked. Any write attempt to a TX MB with
its IFLAG flag set is also blocked.
0b - Invalid
1b - Valid
13 Inactive Message Buffer
IMB Indicates whether any message buffer is inactive (CODE field is either 1000b or 0b) when ESR2[VPS] = 1.
This field becomes 1 when:
• A lowest-priority TX message buffer ( ESR2[LPTM] ) is found and it is inactive during arbitration.
• This field is not 1, and a frame is transmitted successfully.
This field always becomes 0 at the start of arbitration (see Arbitration process ).
If a message buffer is successfully transmitted and this field is 0 (no inactive message buffer), ESR2[VPS]
and this field both become 1. The index related to the MB transmitted is loaded into ESR2[LPTM]. In this
case, the value of ESR2[LPTM] is the number of the first inactive message buffer.
0b - Message buffer indicated by ESR2[LPTM] is not inactive.
1b - At least one message buffer is inactive.
Table continues on the next page...

---

*Page 2397*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
Reserved
12
—
Reserved
11-0
—
70.6.2.16 Cyclic Redundancy Check (CRCR)
Offset
Register Offset
CRCR 44h
Function
Provides information about the CRC of transmitted messages for non-FD messages. This register only reports the 15 low-order
bits of CRC calculations for messages in CAN FD format that require either 17 or 21 bits. For CAN FD format frames, you must
use the CAN FD CRC (FDCRC) . This register is updated at the same time that the TX interrupt flag is set.
NOTE
See CRC sequence calculation in the CAN Protocol standard (ISO 11898-1:2015) for details.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 MBCRC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 TXCRC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-23
—
Table continues on the next page...

---

*Page 2398*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
22-16 CRC Message Buffer
MBCRC Indicates the number of the message buffer corresponding to the value in CRCR[TXCRC] .
Reserved
15
—
14-0 Transmitted CRC value
TXCRC Indicates the CRC value of the last transmitted message for non-FD frames. For FD frames, CRC value is
reported in CAN FD CRC (FDCRC) .
70.6.2.17 Legacy RX FIFO Global Mask (RXFGMASK)
Offset
Register Offset
RXFGMASK 48h
Function
Masks the Legacy RX FIFO ID filter table elements that do not have a corresponding RXIMR according to CTRL2[RFFN] , when
Legacy RX FIFO is enabled.
This register is located in RAM.
You can only write to this register in Freeze mode; the module blocks it in other modes.
The following table shows how the FGM bits correspond to each IDAF field.
Table 446. Correspondence of Legacy RX FIFO global mask bits to IDF fields
Legacy RX FIFO Identifier acceptance filter fields
ID filter table
1 2
RTR IDE RXIDA RXIDB RXIDC Reserved
elements format
( MCR[IDAM] )
A FGM[31] FGM[30] FGM[29:1] — — FGM[0]
B FGM[31], FGM[30], — FGM[29:16], —
FGM[15] FGM[14] FGM[13:0]
C — — — FGM[31:24],
FGM[23:16],
FGM[15:8],
FGM[7:0]
1. If MCR[IDAM] is equivalent to format B, only the 14 most significant bits of the identifier of the incoming frame are
compared with the Legacy RX FIFO filter.
2. If MCR[IDAM] is equivalent to format C, only the eight most significant bits of the identifier of the incoming frame are
compared with the Legacy RX FIFO filter.

---

*Page 2399*

CAN (FlexCAN)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
FGM
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
FGM
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
31-0 Legacy RX FIFO Global Mask Bits
FGM Masks the ID filter table elements bits in a perfect alignment.
0b - The corresponding bit in the filter is "don't care."
1b - The corresponding bit in the filter is checked.
70.6.2.18 Legacy RX FIFO Information (RXFIR)
Offset
Register Offset
RXFIR 4Ch
Function
Provides information about Legacy RX FIFO.
This register is the port through which the CPU accesses the output of the Legacy RXFIR FIFO located in RAM. FlexCAN writes
to the Legacy RXFIR FIFO when a new message is moved into the Legacy RX FIFO. Also, its output is updated whenever the
output of the Legacy RX FIFO is updated with the next message. See Legacy RX FIFO for instructions on reading this register.
NOTE
RXFIR can be written only during memory initialization, due to the error code correction (ECC) feature. In every
other case, this register is read-only.

---

*Page 2400*

CAN (FlexCAN)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 IDHIT
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
Reserved
31-9
—
8-0 Identifier Acceptance Filter Hit Indicator
IDHIT Indicates which Identifier Acceptance filter that the received message hit in the output of the Legacy RX
FIFO. If multiple filters match the incoming message ID, the first matching IDAF found (lowest number) by
the matching process is indicated. This field is valid only when IFLAG1[BUF5I] is set.
70.6.2.19 CAN Bit Timing (CBT)
Offset
Register Offset
CBT 50h
Function
Provides an alternative way to store the CAN bit timing variables described in Control 1 (CTRL1) . EPRESDIV,
EPROPSEG, EPSEG1, EPSEG2, and ERJW are extended versions of CTRL1[PRESDIV] , CTRL1[PROPSEG] , CTRL1[PSEG1] ,
CTRL1[PSEG2] , and CTRL1[RJW] respectively.
NOTE
The CAN bit variables in CTRL1 and in CBT are stored in the same register.
CBT[BTF] selects the use of the timing variables defined in this register.
When the CAN FD feature is enabled (MCR[FDEN] = 1), always write 1 to CBT[BTF].
Soft reset does not affect the contents of this register.
NOTE
Ensure that bit time settings and protocol engine tolerance are in compliance with the CAN Protocol standard
(ISO 11898-1:2015).

---

*Page 2401*

CAN (FlexCAN)
NOTE
If CTRL2[BTE] = 1, EPRESDIV, ERJW, EPROPSEG, EPSEG1, and EPSEG2 are read as zero. A write operation
to them has no effect.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
BTF EPRESDIV ERJW
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
EPROPSEG EPSEG1 EPSEG2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Bit Timing Format Enable
BTF Enables the use of extended CAN bit timing fields EPRESDIV, EPROPSEG, EPSEG1, EPSEG2, and
ERJW. These fields replace the CAN bit timing variables defined in Control 1 (CTRL1) . This field can be
written in Freeze mode only.
0b - Disable
1b - Enable
30-21 Extended Prescaler Division Factor
EPRESDIV Defines the ratio between the PE clock frequency and the serial clock (Sclock) frequency when
CBT[BTF] = 1, otherwise it has no effect. It extends the CTRL1[PRESDIV] value range.
The Sclock period defines the time quantum of the CAN protocol. For the reset value, the Sclock frequency
is equal to the PE clock frequency (see Protocol timing ). This field can be written only in Freeze mode; the
module blocks it in other modes.
Sclock frequency = PE clock frequency ÷ (EPRESDIV + 1)
20-16 Extended Resync Jump Width
ERJW Defines the maximum number of time quanta that one resynchronization can change a bit time when
CBT[BTF] = 1. Otherwise, it has no effect. It extends the CTRL1[RJW] value range.
This field can be written only in Freeze mode; the module blocks it in other modes.
Resync Jump Width = ERJW + 1.
One Time Quantum = one Sclock period.
15-10 Extended Propagation Segment
EPROPSEG
Table continues on the next page...

---

*Page 2402*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
Defines the length of the propagation segment in the bit time when CBT[BTF] = 1, otherwise it has no effect.
It extends the CTRL1[PROPSEG] value range. This field can be written only in Freeze mode; the module
blocks it in other modes.
Propagation Segment Time = (EPROPSEG + 1) × Time Quanta.
One Time Quantum = one Sclock period.
9-5 Extended Phase Segment 1
EPSEG1 Defines the length of phase segment 1 in the bit time when CBT[BTF] = 1, otherwise it has no effect. It
extends the CTRL1[PSEG1] value range. This field can be written only in Freeze mode; the module blocks
it in other modes.
Phase Buffer Segment 1 = (EPSEG1 + 1) × Time Quanta.
One Time Quantum = one Sclock period.
4-0 Extended Phase Segment 2
EPSEG2 Defines the length of phase segment 2 in the bit time when CBT[BTF] = 1, otherwise it has no effect. It
extends the CTRL1[PSEG2] value range. This field can be written only in Freeze mode; the module blocks
it in other modes.
Phase Buffer Segment 1 = (EPSEG2 + 1) × Time Quanta.
One Time Quantum = one Sclock period.
70.6.2.20 Interrupt Masks 3 (IMASK3)
Offset
Register Offset
IMASK3 6Ch
Function
Enables or disables any number of the 32 message buffer interrupts for MB95–MB64. It contains one interrupt mask bit per buffer.
This configuration allows the CPU to determine which buffer generates an interrupt after a successful transmission or reception
when the corresponding IFLAG3 flag is set.
NOTE
Each module instance supports a different number of registers.
Instance Register supported Register not supported
FLEXCAN_0 IMASK3 —
FLEXCAN_1 — IMASK3
Table continues on the next page...

---

*Page 2403*

CAN (FlexCAN)
Table continued from the previous page...
Instance Register supported Register not supported
FLEXCAN_2 — IMASK3
FLEXCAN_3 — IMASK3
FLEXCAN_4 — IMASK3
FLEXCAN_5 — IMASK3
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
BUF95TO64M
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
BUF95TO64M
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Buffer MBi Mask
BUF95TO64M Enables or disables the corresponding FlexCAN message buffer interrupt for MB95–MB64. When CAN FD
is enabled, the MB range is defined in accordance with FDCTRL[MBDSRn] .
NOTE
If the corresponding IFLAG3 flag is set, writing 1 or 0 to a field in IMASK3 can set or clear
an interrupt request.
0b - The corresponding buffer interrupt is disabled.
1b - The corresponding buffer interrupt is enabled.
70.6.2.21 Interrupt Flags 3 (IFLAG3)
Offset
Register Offset
IFLAG3 74h

---

*Page 2404*

CAN (FlexCAN)
Function
Defines the flags for the 32 message buffer interrupts for MB95–MB64. It contains one interrupt flag bit per buffer. Each successful
transmission or reception sets the corresponding IFLAG3 flag. If the corresponding IMASK3 bit is 1, an interrupt is generated. The
interrupt flag must be cleared by writing 1 to it. Writing 0 has no effect.
Before updating MCR[MAXMB] , the CPU must service the IFLAG3 flags whose MB value is greater than the MAXMB to be
updated. Otherwise, they remain set and are inconsistent with the number of message buffers available.
NOTE
Each module instance supports a different number of registers.
Instance Register supported Register not supported
FLEXCAN_0 IFLAG3 —
FLEXCAN_1 — IFLAG3
FLEXCAN_2 — IFLAG3
FLEXCAN_3 — IFLAG3
FLEXCAN_4 — IFLAG3
FLEXCAN_5 — IFLAG3
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R BUF95TO64
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R BUF95TO64
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Buffer MBi Interrupt
BUF95TO64 Flags the corresponding FlexCAN message buffer interrupt for MB95–MB64. When CAN FD is enabled, the
MB range is defined in accordance with FDCTRL[MBDSRn] .
0b - The corresponding buffer has no occurrence of successfully completed transmission or
reception.
1b - The corresponding buffer has successfully completed transmission or reception.

---

*Page 2405*

CAN (FlexCAN)
70.6.2.22 Receive Individual Mask (RXIMR0 - RXIMR95)
Offset
For n = 0 to 95:
Register Offset
RXIMRn 880h + (n × 4h)
Function
Stores the acceptance masks for ID filtering in RX message buffers and the Legacy RX FIFO.
When the Legacy RX FIFO is disabled ( MCR[RFEN] = 0), an individual mask is provided for each available RX message buffer
on a one-to-one correspondence. When the Legacy RX FIFO is enabled (MCR[RFEN] = 1), an individual mask is provided for
each Legacy RX FIFO ID filter table element on a one-to-one correspondence. This correspondence depends on the setting of
CTRL2[RFFN] (see Legacy RX FIFO ).
RXIMR0 stores the individual mask associated with either MB0 or ID filter table element 0. RXIMR1 stores the individual mask
associated with either MB1 or ID filter table element 1, and so on.
The CPU can only access the RXIMR registers when the module is in Freeze mode; otherwise, the module blocks them. Reset
does not affect these registers. They are located in RAM and must be explicitly initialized prior to any reception.
It is possible for the RXIMR memory region to be accessed as general-purpose memory. See Bus interface for more information.
NOTE
Each module instance supports a different number of registers.
Instance Register supported Register not supported
FLEXCAN_0 RXIMR0–RXIMR95 —
FLEXCAN_1 RXIMR0–RXIMR63 RXIMR64–RXIMR95
FLEXCAN_2 RXIMR0–RXIMR63 RXIMR64–RXIMR95
FLEXCAN_3 RXIMR0–RXIMR31 RXIMR32–RXIMR95
FLEXCAN_4 RXIMR0–RXIMR31 RXIMR32–RXIMR95
FLEXCAN_5 RXIMR0–RXIMR31 RXIMR32–RXIMR95

---

*Page 2406*

CAN (FlexCAN)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
MI
W
Reset See Register reset values .
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
MI
W
Reset See Register reset values .
Register reset values
Register Reset value
RXIMR0–RXIMR31 FLEXCAN_0–FLEXCAN_5: undefined
RXIMR32–RXIMR63
FLEXCAN_0–FLEXCAN_2: undefined
FLEXCAN_3–FLEXCAN_5: Register not supported
RXIMR64–RXIMR95
FLEXCAN_0: undefined
FLEXCAN_1–FLEXCAN_5: Register not supported
Fields
Field Function
31-0 Individual Mask Bits
MI Masks the corresponding bit in both the message buffer filter and Legacy RX FIFO ID filter table element in
distinct ways.
For message buffer filters, see RX Message Buffers Global Mask (RXMGMASK) .
For Legacy RX FIFO ID filter table elements, see Legacy RX FIFO Global Mask (RXFGMASK) .
0b - The corresponding bit in the filter is "don't care."
1b - The corresponding bit in the filter is checked.
70.6.2.23 Memory Error Control (MECR)
Offset
Register Offset
MECR AE0h
Function
Contains control bits for memory error detection and correction (ECC).

---

*Page 2407*

CAN (FlexCAN)
NOTE
When CTRL2[ECRWRE] = 0, writes to this register are blocked, except for MECR[ECRWRDIS] .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
ECRW HANC FANC CEI_
RDIS EI_ ... EI_ ... MSK
W
Reset 1 0 0 0 0 0 0 0 0 0 0 0 1 1 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
HAER FAER EXTE RERR ECCDI NCEF
RIE RIE RRIE DIS S AFRZ
W
Reset 0 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0
Fields
Field Function
31 Error Configuration Register Write Disable
ECRWRDIS Disables writes on this register.
This field automatically becomes 1 (disabled) when CTRL2[ECRWRE] is 1. The protocol described in
Detection and correction of memory errors must be followed.
0b - Enable
1b - Disable
Reserved
30-20
—
19 Host Access with Noncorrectable Errors Interrupt Mask
HANCEI_MSK Enables the interrupt for noncorrectable errors detected in memory reads issued by the host (CPU).
0b - Disable
1b - Enable
18 FlexCAN Access with Noncorrectable Errors Interrupt Mask
FANCEI_MSK Enables the interrupt for noncorrectable errors detected in memory reads issued by FlexCAN internal
processes.
0b - Disable
1b - Enable
Reserved
17
—
16 Correctable Errors Interrupt Mask
Table continues on the next page...

---

*Page 2408*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
CEI_MSK Enables the interrupt for correctable errors detected in memory reads issued by the host or FlexCAN
internal processes.
0b - Disable
1b - Enable
15 Host Access Error Injection Enable
HAERRIE Enables the injection of errors only in memory reads issued by the host (CPU).
0b - Disable
1b - Enable
14 FlexCAN Access Error Injection Enable
FAERRIE Enables the injection of errors only in memory reads issued by FlexCAN internal processes.
0b - Disable
1b - Enable
13 Extended Error Injection Enable
EXTERRIE Extends the error injection on 32-bit memory accesses to the complementary 32-bit word. This feature
uses the same 32-bit error injection data and parity words used for 64-bit memory accesses performed
by internal FlexCAN processes. See Error Injection Data Pattern (ERRIDPR) and Error Injection Parity
Pattern (ERRIPPR) .
0b - Disable. Apply error injection only to the 32-bit word.
1b - Enable. Apply error injection to the 64-bit word.
Reserved
12-10
—
9 Error Report Disable
RERRDIS Disables the update of the error report registers. The update of error-related flags and the generation of bus
transfer errors are still active.
NOTE
When reading the report registers, this field must be 1 to ensure coherence on the
consecutive register reads.
0b - Enable
1b - Disable
8 Error Correction Disable
ECCDIS Completely disables the memory detection and correction mechanism. Besides disabling the error report
mechanism, it also stops the update of the error-related flags and the generation of bus transfer errors. The
parity bits continue to be calculated and written into memory on write transactions.
Table continues on the next page...

---

*Page 2409*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
0b - Enable
1b - Disable
7 Noncorrectable Errors in FlexCAN Access Put Chip in Freeze Mode
NCEFAFRZ Determines whether to put FlexCAN into Freeze mode when a noncorrectable error is detected in a memory
read performed by FlexCAN internal processes. In this case, entering Freeze mode prevents FlexCAN
internal processes from treating corrupted data as valid. See Freeze mode for more information.
0b - Normal operation
1b - Freeze mode
Reserved
6-0
—
70.6.2.24 Error Injection Address (ERRIAR)
Offset
Register Offset
ERRIAR AE4h
Function
Contains the address where error is to be injected.
See the chip-specific FlexCAN information for the FlexCAN RAM to memory map address mapping table.
NOTE
For RXFIR , Enhanced RX FIFO, and HR_TIME_STAMP addresses, you must inject ECC errors in host
access only.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 INJADDR_L
INJADDR_H
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2410*

CAN (FlexCAN)
Fields
Field Function
Reserved
31-14
—
13-2 Error Injection Address High
INJADDR_H Defines the 12 most significant bits of the physical RAM address where error is to be injected (see table
above).
1-0 Error Injection Address Low
INJADDR_L Defines the two least significant bits of the physical RAM address where error is to be injected. These
bits ensure that the address is on a 32-bit boundary.
70.6.2.25 Error Injection Data Pattern (ERRIDPR)
Offset
Register Offset
ERRIDPR AE8h
Function
Contains the error pattern to be injected in the data word read from memory.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
DFLIP
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
DFLIP
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Data Flip Pattern
DFLIP Contains the flip pattern. Bits set to 1 in the flip pattern cause the corresponding data bit in the word read
from memory to invert.

---

*Page 2411*

CAN (FlexCAN)
70.6.2.26 Error Injection Parity Pattern (ERRIPPR)
Offset
Register Offset
ERRIPPR AECh
Function
Contains the error pattern to be injected in parity bits read from memory with the data word. Bits set to 1 in the flip pattern
cause the corresponding parity bit in the word read from memory to invert.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
PFLIP3 PFLIP2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
PFLIP1 PFLIP0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-29
—
28-24 Parity Flip Pattern for Byte 3 (Most Significant)
PFLIP3 Contains the error injection pattern for Byte 3.
Reserved
23-21
—
20-16 Parity Flip Pattern for Byte 2
PFLIP2 Contains the error injection pattern for Byte 2.
Reserved
15-13
—
12-8 Parity Flip Pattern for Byte 1
PFLIP1 Contains the error injection pattern for Byte 1.
Table continues on the next page...

---

*Page 2412*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
Reserved
7-5
—
4-0 Parity Flip Pattern for Byte 0 (Least Significant)
PFLIP0 Contains the error injection pattern for Byte 0.
70.6.2.27 Error Report Address (RERRAR)
Offset
Register Offset
RERRAR AF0h
Function
Reports the address used for an access operation in which an error (correctable or noncorrectable) was detected. Also reports
the identification of the source of that access.
This address is always reported using a 32-bit alignment. Non-aligned accesses ( ERRADDR [1:0] nonzero) are reported with the
address aligned, and data is reported in Error Report Data (RERRDR) shifted accordingly. When errors are detected in accesses
larger than 32-bit (as performed by FlexCAN internal processes), the address of the 32-bit word where the error was detected is
reported. For errors detected in more than one 32-bit word, only the least significant address is reported.
Table 447. Source of memory access
SAID[2:0] Error during...
0 Move-out FlexCAN access
1 Move-in
2 TX arbitration
3 RX matching
4 Move-out host access
5–7 Reserved

---

*Page 2413*

CAN (FlexCAN)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 NCE 0 SAID
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 ERRADDR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-25
—
24 Noncorrectable Error
NCE Indicates that the report is due to a noncorrectable error.
0b - Reporting a correctable error
1b - Reporting a noncorrectable error
Reserved
23-19
—
18-16 SAID
SAID SAID[2] — Identification of the requester of the memory read request:
• 0 = Requested by FlexCAN internal processes
• 1 = Requested by host (CPU)
SAID[1] — Details of FlexCAN operation:
• 0 = Move
• 1 = Scanning
SAID[0] — Operation that requested the memory read:
• 0 = Transmission
• 1 = Reception
For more information, see Table 447 .
Reserved
15-14
—
13-0 Address Where Error Detected
Table continues on the next page...

---

*Page 2414*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
See description of Error Injection Address (ERRIAR) .
ERRADDR
70.6.2.28 Error Report Data (RERRDR)
Offset
Register Offset
RERRDR AF4h
Function
Reports the raw data (unmodified by the correction performed by ECC logic) read from memory with error. The value reported
does not represent the transient values of the BUSY bit (see Table 453 ) when reading a message buffer.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RDATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RDATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Raw Data Word Read from Memory with Error
31-0
RDATA
70.6.2.29 Error Report Syndrome (RERRSYNR)
Offset
Register Offset
RERRSYNR AF8h

---

*Page 2415*

CAN (FlexCAN)
Function
Contains the syndrome detected in a memory read with error. It also reports the bytes which were read in this 32-bit
read transaction.
Each SYND n field indicates the type of error and which bit in byte ( n ) the error affects. SYND3 corresponds to the most significant
byte in the data word read from memory; SYND0 corresponds to the least significant.
Table 448. Syndrome definition
SYND n (hex) Type Bit affected
00 — None (no error)
01 Code 0
02 Code 1
04 Code 2
07 Data 5
08 Code 3
0E Data 7
10 Code 4
13 Data 2
15 Data 6
16 Data 1
19 Data 3
1A Data 4
1C Data 0
06 — All-zeroes noncorrectable error
1F — All-ones noncorrectable error
All others — Noncorrectable error
Each BE n field indicates which byte in the 32-bit word reported was effectively read. The syndrome bits are calculated for all
bytes, including the non-read ones. Errors detected in non-read bytes are indicated (see Error indication ) and reported (see
Error reporting ).
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R BE3 0 SYND3 BE2 0 SYND2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R BE1 0 SYND1 BE0 0 SYND0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2416*

CAN (FlexCAN)
Fields
Field Function
Byte Enabled for Byte 3 (Most Significant)
31
0b - Byte was not read.
BE3
1b - Byte was read.
Reserved
30-29
—
28-24 Error Syndrome for Byte 3 (Most Significant)
SYND3 See Table 448 .
Byte Enabled for Byte 2
23
0b - Byte was not read.
BE2
1b - Byte was read.
Reserved
22-21
—
20-16 Error Syndrome for Byte 2
SYND2 See Table 448 .
Byte Enabled for Byte 1
15
0b - Byte was not read.
BE1
1b - Byte was read.
Reserved
14-13
—
12-8 Error Syndrome for Byte 1
SYND1 See Table 448 .
Byte Enabled for Byte 0 (Least Significant)
7
0b - Byte was not read.
BE0
1b - Byte was read.
Reserved
6-5
—
4-0 Error Syndrome for Byte 0 (Least Significant)
SYND0 See Table 448 .

---

*Page 2417*

CAN (FlexCAN)
70.6.2.30 Error Status (ERRSR)
Offset
Register Offset
ERRSR AFCh
Function
Contains the status bits of the error correction and detection operations. These flags can be cleared by writing 1 to them. Writing
0 has no effect.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
HANC FANC
R 0 0 CEIF
EIF EIF
W W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
HANC FANC
R 0 0 CEIOF
EIOF EIOF
W W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-20
—
19 Host Access with Noncorrectable Error Interrupt Flag
HANCEIF Indicates that a noncorrectable error was detected in a memory read initiated by host. A bus transfer
error is asserted for that access. If MECR[HANCEI_MSK] = 1, the interrupt is 1.
0b - No errors detected
1b - Error detected
18 FlexCAN Access with Noncorrectable Error Interrupt Flag
FANCEIF Indicates that a noncorrectable error was detected in a memory read initiated by FlexCAN internal
processes. If MECR[FANCEI_MSK] = 1, the interrupt is 1.
0b - No errors detected
1b - Error detected
Reserved
17
Table continues on the next page...

---

*Page 2418*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
—
16 Correctable Error Interrupt Flag
CEIF Indicates that a correctable error was detected in a memory read. If MECR[CEI_MSK] = 1, the interrupt is 1.
0b - No errors detected
1b - Error detected
Reserved
15-4
—
3 Host Access with Noncorrectable Error Interrupt Overrun Flag
HANCEIOF Indicates that a noncorrectable error was detected in a memory read initiated by host when
ERRSR[HANCEIF] was set. No interrupt is associated with this flag. See Error indication .
0b - No errors detected
1b - Error detected
2 FlexCAN Access with Noncorrectable Error Interrupt Overrun Flag
FANCEIOF Indicates that a noncorrectable error was detected in a memory read initiated by FlexCAN internal
processes when ERRSR[FANCEIF] was set. No interrupt is associated with this flag. See Error indication .
0b - No errors detected
1b - Error detected
Reserved
1
—
0 Correctable Error Interrupt Overrun Flag
CEIOF Indicates that a correctable error was detected in a memory read when ERRSR[CEIF] was set. No interrupt
is associated with this flag. See Error indication .
0b - No errors detected
1b - Error detected
70.6.2.31 Enhanced CAN Bit Timing Prescalers (EPRS)
Offset
Register Offset
EPRS BF0h
Function
Defines the CAN bit timing prescaler s for the nominal phase and data phase when CTRL2[BTE] = 1.

---

*Page 2419*

CAN (FlexCAN)
Used by the module only if CTRL2[BTE] = 1; otherwise, a write operation has no effect and all fields are read as zero.
This register can be written only in Freeze mode; the module blocks it in other modes.
Soft reset does not affect the contents of this register.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
EDPRESDIV
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
ENPRESDIV
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-26
—
25-16 Extended Data Phase Prescaler Division Factor
EDPRESDIV Defines the ratio between the PE clock frequency and the Sclock frequency in the data phase of a CAN FD
message when CTRL2[BTE] = 1.
The Sclock period defines the time quantum of the CAN FD protocol for the data bit rate.
Sclock frequency = PE clock frequency ÷ (EDPRESDIV + 1)
NOTE
To minimize errors when processing FD frames, use the same value for this field and for
EPRS[ENPRESDIV] . See the first note in CAN FD frames for details.
Reserved
15-10
—
9-0 Extended Nominal Prescaler Division Factor
ENPRESDIV Defines the ratio between the PE clock frequency and the Sclock frequency when CTRL2[BTE] = 1.
Otherwise, it reads as 0 and a write operation has no effect.
The Sclock period defines the time quantum of the CAN protocol in the nominal phase . For the reset value,
the Sclock frequency is equal to the PE clock frequency (see Protocol timing ).
Sclock frequency = PE clock frequency ÷ (ENPRESDIV + 1)

---

*Page 2420*

CAN (FlexCAN)
70.6.2.32 Enhanced Nominal CAN Bit Timing (ENCBT)
Offset
Register Offset
ENCBT BF4h
Function
Provides an alternative way to store the CAN bit timing variables described in Control 1 (CTRL1) and CAN Bit Timing (CBT) , to
get higher CAN bit timing resolution.
This register is used by the module only if CTRL2[BTE] = 1. Otherwise, a write operation has no effect and all fields are read
as zero.
Soft reset does not affect the contents of this register.
This register can be written only in Freeze mode; the module blocks it in other modes.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
NRJW NTSEG2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
NTSEG2 NTSEG1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-29
—
28-22 Nominal Resynchronization Jump Width
NRJW Defines the maximum number of time quanta that one resynchronization can change a nominal bit time
when CTRL2[BTE] = 1. Otherwise, it has no effect.
One time quantum = one Sclock period
Nominal Resync Jump Width = NRJW + 1
Reserved
21-19
—
Table continues on the next page...

---

*Page 2421*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
18-12 Nominal Time Segment 2
NTSEG2 Defines the length of Time Segment 2 in the nominal bit time when CTRL2[BTE] = 1. Otherwise, it has
no effect.
Nominal Time Segment 2 = (NTSEG2 + 1) × Time Quanta
One time quantum = one Sclock period
Reserved
11-8
—
7-0 Nominal Time Segment 1
NTSEG1 Defines the length of Time Segment 1 in the bit time when CTRL2[BTE] = 1. Otherwise, it has no effect.
Nominal Time Segment 1 = (NTSEG1 + 1) × Time Quanta
One time quantum = one Sclock period
70.6.2.33 Enhanced Data Phase CAN Bit Timing (EDCBT)
Offset
Register Offset
EDCBT BF8h
Function
Provides an alternative way to store the data phase CAN bit timing variables described in CAN FD Bit Timing (FDCBT) to achieve
higher CAN bit timing resolution.
This register is used by the module only if CTRL2[BTE] = 1; otherwise, a write operation has no effect and all fields are read
as zero.
Soft reset does not affect the contents of this register.
This register can be written only in Freeze mode; the module blocks it in other modes.
NOTE
Ensure that bit time settings and protocol engine tolerance are in compliance with the CAN Protocol standard
(ISO 11898-1:2015).
NOTE
DTSEG1 must be at least two time quanta.

---

*Page 2422*

CAN (FlexCAN)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
DRJW
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
DTSEG2 DTSEG1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-26
—
25-22 Data Phase Resynchronization Jump Width
DRJW Defines the maximum number of time quanta that one resynchronization can change a data phase bit time
when CTRL2[BTE] = 1. Otherwise, it has no effect.
Data Phase Resync Jump Width = DRJW + 1.
Reserved
21-16
—
15-12 Data Phase Time Segment 2
DTSEG2 Defines the length of time segment 2 in the data phase bit time when CTRL2[BTE] = 1. Otherwise, it has
no effect.
Data Phase Time Segment 2 = (DTSEG2 + 1) × Time Quanta.
One Time Quantum = one Sclock period.
Reserved
11-5
—
4-0 Data Phase Segment 1
DTSEG1 Defines the length of time segment 1 in the data phase bit time when CTRL2[BTE] = 1. Otherwise, it has
no effect.
Data Phase Time Segment 1 = (NTSEG1 + 1) × Time Quanta.
One Time Quantum = one Sclock period.

---

*Page 2423*

CAN (FlexCAN)
70.6.2.34 Enhanced Transceiver Delay Compensation (ETDC)
Offset
Register Offset
ETDC BFCh
Function
Contains extended versions of FDCTRL[TDCOFF] and FDCTRL[TDCVAL] . This register is used by the module only if
CTRL2[BTE] = 1. Otherwise, a write operation has no effect and all fields are read as zero.
NOTE
See Transmitter delay compensation in the CAN Protocol standard (ISO 11898-1:2015) for details.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
ETDC TDMDI
ETDCOFF
EN S
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
ETDC
R 0 ETDCVAL
FAIL
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Transceiver Delay Compensation Enable
ETDCEN Enables the TDC feature. It can be written in Freeze mode only.
NOTE
See Transmitter delay compensation in the CAN Protocol standard (ISO 11898-1:2015)
for details.
NOTE
TDC must be disabled when the Loop Back Mode is enabled. See CTRL1[LPB] .
0b - Disable
1b - Enable
30 Transceiver Delay Measurement Disable
TDMDIS
Table continues on the next page...

---

*Page 2424*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
Disables the transceiver delay measurement. When the TDC measurement is disabled, only
ETDC[ETDCOFF] determines the secondary sample point position. If TCD measurement is enabled,
the sum of the transceiver delay measurement plus the enhanced TDC offset determines the secondary
sample point position.
Soft reset does not affect this field.
NOTE
This bit can be enabled only if CTRL2[BTE] = 1.
0b - Enable
1b - Disable
Reserved
29-23
—
22-16 Enhanced Transceiver Delay Compensation Offset
ETDCOFF Contains the offset value to be added to the loop delay of the measured transceiver. This value defines
the position of the delayed comparison point when bit rate switching is active. See Transceiver delay
compensation for details on how the loop delay measurement is performed.
This field can be written in Freeze mode only. Its value can be defined in protocol engine (PE) clock periods
(CANCLK, see Protocol timing for more details). It must be smaller than the CAN bit duration in the data bit
rate for proper operation.
Do not write 0 to this field.
NOTE
If CTRL2[BTE] becomes 1 after a chip-level hard reset, this field is read as 1h.
15 Transceiver Delay Compensation Fail
ETDCFAIL Indicates whether the transceiver delay compensation (TDC) mechanism is out of range. In this case, it is
unable to compensate the loop delay of the transceiver and successfully compare the delayed received bits
to the transmitted ones. (See Transceiver delay compensation .) This field becomes 0 the first time FlexCAN
detects the out of range condition.
0b - In range
1b - Out of range
Reserved
14-8
—
7-0 Enhanced Transceiver Delay Compensation Value
ETDCVAL Contains ETDC[ETDCOFF] added to the measured value of the transceiver loop delay in the latest
transmitted CAN FD frame, with BRS = 1.
The module only updates this field when ETDC[ETDCEN] = 1.
Soft reset affects this field.
Table continues on the next page...

---

*Page 2425*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
NOTE
If ETDC[TDMDIS] = 1, this field stores ETDC[ETDCOFF] only.
70.6.2.35 CAN FD Control (FDCTRL)
Offset
Register Offset
FDCTRL C00h
Function
Contains control bits for CAN FD operation. It also defines the data size of message buffers allocated in different partitions of RAM
(memory blocks) as described in Table 449 .
When an 8-byte payload is selected:
• Block R0 allocates MB0–MB31.
• Block R1 allocates MB32–MB63.
• Block R2 allocates MB64–MB95.
When a payload larger than eight bytes is selected, the maximum number of message buffers in a block is limited as
described below.
Table 449. Number of message buffers
Payload size Maximum number of message buffers per RAM block
8 bytes 32
16 bytes 21
32 bytes 12
64 bytes 7
One memory block fits exactly 32 message buffers with an 8-byte payload. For other possible payload sizes, empty memory may
exist between the last message buffer in a block and the beginning of the next block. This empty memory corresponds to less than
one message buffer, and must not be used.
Soft reset does not affect the contents of this register.

---

*Page 2426*

CAN (FlexCAN)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0 0
FDRA
MBDSR2 MBDSR1 MBDSR0
TE
W
Reset 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
TDCF
R 0 0 TDCVAL
TDCE
AIL
TDCOFF
N
W W1C
Reset 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0
Fields
Field Function
31 Bit Rate Switch Enable
FDRATE Enables the effect of the Bit Rate Switch (BRS bit) during the data phase of TX messages. When 1, if
BRS = 1 in the TX message buffer, frames are transmitted with bit rate switching. When 0, frames are
transmitted at a nominal rate, and the BRS bit in the TX MB has no effect.
The CPU can write to this field at any time. However, its effect becomes active only under one of
these conditions:
• The CAN bus is in the Wait for Bus Idle state.
• The CAN bus is in the Bus Idle state.
• The CAN bus is in the Bus Off state.
• The current frame under reception or transmission reaches the interframe space.
By writing 0 to FDCTRL[FDRATE] , the CPU can force all bits in CAN FD messages to be transmitted at
nominal bit rate. This transmission occurs regardless of the value in the BRS bit of the TX message buffers.
0b - Disable
1b - Enable
Reserved
30-27
—
Reserved
26-25
—
Reserved
24
—
23-22 Message Buffer Data Size for Region 2
MBDSR2 Selects the data size per message buffer for region R2 of message buffers allocated in RAM.
This field can be written in Freeze mode only.

---

*Page 2427*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
FLEXCAN_0 FDCTRL —
FLEXCAN_1 — FDCTRL
FLEXCAN_2 — FDCTRL
FLEXCAN_3 — FDCTRL
FLEXCAN_4 — FDCTRL
FLEXCAN_5 — FDCTRL
00b - 8 bytes
01b - 16 bytes
10b - 32 bytes
11b - 64 bytes
Reserved
21
—
20-19 Message Buffer Data Size for Region 1
MBDSR1 Selects the data size per message buffer for region R1 of message buffers allocated in RAM.
This field can be written in Freeze mode only.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
FLEXCAN_0 FDCTRL —
FLEXCAN_1 FDCTRL —
FLEXCAN_2 FDCTRL —
FLEXCAN_3 — FDCTRL

---

*Page 2428*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
FLEXCAN_4 — FDCTRL
FLEXCAN_5 — FDCTRL
00b - 8 bytes
01b - 16 bytes
10b - 32 bytes
11b - 64 bytes
Reserved
18
—
17-16 Message Buffer Data Size for Region 0
MBDSR0 Selects the data size per message buffer for region R0 of message buffers allocated in RAM.
This field can be written in Freeze mode only.
00b - 8 bytes
01b - 16 bytes
10b - 32 bytes
11b - 64 bytes
15 Transceiver Delay Compensation Enable
TDCEN Enables the TDC feature. It can be written in Freeze mode only.
See Transmitter delay compensation in the CAN Protocol standard (ISO 11898-1:2015) for details.
TDC must be disabled when Loopback mode is enabled (see CTRL1[LPB] ).
NOTE
If CTRL2[BTE] = 1, this field is read as 0 and a write operation has no effect.
0b - Disable
1b - Enable
14 Transceiver Delay Compensation Fail
TDCFAIL Indicates whether the Transceiver Delay Compensation (TDC) mechanism is out of range. In this case,
the mechanism cannot compensate for the loop delay of the transceiver and successfully compare the
delayed received bits to the transmitted ones (see Transceiver delay compensation ). The first time that
FlexCAN detects the out-of-range condition, this field becomes 1.
NOTE
If CTRL2[BTE] = 1, this field is read as 0 and a write operation has no effect.
Table continues on the next page...

---

*Page 2429*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
0b - In range
1b - Out of range
Reserved
13
—
12-8 Transceiver Delay Compensation Offset
TDCOFF Contains the offset value to be added to the loop delay of the measured transceiver. This value defines
the position of the delayed comparison point when bit rate switching is active. See Transceiver delay
compensation for details about loop delay measurement.
This field can be written in Freeze mode only. Its value can be defined in Protocol Engine Clock periods
(CANCLK, see Protocol timing for more details). The value must be smaller than the CAN bit duration in the
data bit rate for proper operation.
NOTE
If CTRL2[BTE] = 1, TDCOFF is read as 0 and a write operation has no effect.
Do not write 0 to this field.
Reserved
7-6
—
5-0 Transceiver Delay Compensation Value
TDCVAL Contains the value of the transceiver loop delay measured from the transmitted EDL-to-R0 transition edge
to the respective received one added to FDCTRL[TDCOFF] . This value is an integer multiple of the Protocol
Engine Clock period (CANCLK).
If CTRL2[BTE] = 1, this field is read as 0.
See Protocol timing for details on the loop delay measurement.
70.6.2.36 CAN FD Bit Timing (FDCBT)
Offset
Register Offset
FDCBT C04h
Function
Stores the CAN bit timing variables used in the data phase of CAN FD messages when the FDCTRL[FDRATE] = 1, compatible
with the CAN FD specification. Fields in this register define:
• The time quantum duration
• The number of time quanta per CAN bit
• The sample point position for the data bit rate portion of a CAN FD message with BRS = 1

---

*Page 2430*

CAN (FlexCAN)
Soft reset does not affect the contents of this register.
The sum of the Fast Propagation Segment (FPROPSEG) and Fast Phase Segment 1 (FPSEG1) must be at least two time quanta.
Ensure bit time settings and protocol engine tolerance are in compliance with the CAN Protocol standard (ISO 11898-1:2015).
NOTE
If CTRL2[BTE] = 1, this register is read as zero and a write operation has no effect.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
FPRESDIV FRJW
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
FPROPSEG FPSEG1 FPSEG2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-30
—
29-20 Fast Prescaler Division Factor
FPRESDIV Defines the ratio between the PE clock frequency and the serial clock (Sclock) frequency in the data bit rate
portion of a CAN FD message with BRS = 1.
The Sclock period defines the time quantum of the CAN FD protocol for the data bit rate. This field can be
written only in Freeze mode; the module blocks it in other modes.
Sclock frequency = PE clock frequency ÷ (FPRESDIV + 1).
NOTE
To minimize errors when processing FD frames, use the same value for this field and for
CTRL1[PRESDIV] or CBT[EPRESDIV] . See the first note in CAN FD frames for details.
Reserved
19
—
18-16 Fast Resync Jump Width
FRJW Defines the maximum number of time quanta that one resynchronization can change a bit time in the data
bit rate portion of a CAN FD message with BRS = 1.
This field can be written only in Freeze mode; the module blocks it in other modes.
Resync Jump Width = FSJW + 1.
Table continues on the next page...

---

*Page 2431*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
One Time Quantum = one Sclock period.
Reserved
15
—
14-10 Fast Propagation Segment
FPROPSEG Defines the length of the propagation segment in the bit time in the data bit rate portion of a CAN FD
message with BRS = 1. This field can be written only in Freeze mode; the module blocks it in other modes.
Propagation Segment Time = FPROPSEG × Time Quanta.
One Time Quantum = one Sclock period.
Reserved
9-8
—
7-5 Fast Phase Segment 1
FPSEG1 Defines the length of phase segment 1 in the bit time in the data bit rate portion of a CAN FD message with
BRS = 1. This field can be written only in Freeze mode; the module blocks it in other modes.
Phase Segment 1 = (FPSEG1 + 1) × Time Quanta.
One Time Quantum = one Sclock period.
Reserved
4-3
—
2-0 Fast Phase Segment 2
FPSEG2 Defines the length of phase segment 2 in the data bit rate portion of a CAN FD message with BRS = 1. This
field can be written only in Freeze mode; the module blocks it in other modes.
Phase Segment 2 = (FPSEG2 + 1) × Time Quanta.
One Time Quantum = one Sclock period.
70.6.2.37 CAN FD CRC (FDCRC)
Offset
Register Offset
FDCRC C08h
Function
Provides information about the cyclic redundancy check (CRC) of transmitted messages.
FlexCAN uses different CRC polynomials for different frame formats.
• The CRC_15 polynomial is used for all frames in CAN format.
• The CRC_17 polynomial is used for frames in CAN FD format with a DATA FIELD up to 16 bytes.

---

*Page 2432*

CAN (FlexCAN)
• The CRC_21 polynomial is used for frames in CAN FD format with a DATA FIELD longer than 16 bytes.
Each polynomial shown below results in a Hamming distance of 6. This register is updated at the same time that the TX Interrupt
flag is set.
15 14 10 8 7 4 3
CRC_15 = C599h: (x + x + x + x + x + x + x + 1)
17 16 14 13 11 6 4 3 1
CRC_17 = 3685Bh: (x + x + x + x + x + x + x + x + x + 1)
21 20 13 11 7 4 3
CRC_21 = 302899h: (x + x + x + x + x + x + x + 1)
Equation 31. CRC polynomial used on CAN frame
NOTE
See CRC sequence calculation in the CAN Protocol standard (ISO 11898-1:2015) for details.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 FD_MBCRC 0 FD_TXCRC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R FD_TXCRC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
30-24 CRC Message Buffer Number for FD_TXCRC
FD_MBCRC Indicates the number of the message buffer corresponding to the value in FDCRC[FD_TXCRC] , for both FD
and non-FD frames.
It reports the same information as in CRCR[MBCRC] .
Reserved
23-21
—
20-0 Extended Transmitted CRC value
FD_TXCRC Contains the CRC value calculated over the most recent transmitted message. Different CRC polynomials
are used for different frame formats.
Table continues on the next page...

---

*Page 2433*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
For CRC_15 and CRC_17, the six most significant bits and the four most significant bits are reported as
zeroes, respectively.
For CRC_15, this field has the same content as Cyclic Redundancy Check (CRCR) .
70.6.2.38 Enhanced RX FIFO Control (ERFCR)
Offset
Register Offset
ERFCR C0Ch
Function
Defines the Enhanced RX FIFO configuration.
This register can be written only in Freeze mode.
Soft reset does not affect any of the contents of this register.
NOTE
Each module instance supports a different number of registers.
Instance Register supported Register not supported
FLEXCAN_0 ERFCR —
FLEXCAN_1 — ERFCR
FLEXCAN_2 — ERFCR
FLEXCAN_3 — ERFCR
FLEXCAN_4 — ERFCR
FLEXCAN_5 — ERFCR

---

*Page 2434*

CAN (FlexCAN)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
ERFE
DMALW NEXIF
N
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
NFE ERFWM
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Enhanced RX FIFO enable
ERFEN Enables the Enhanced RX FIFO.
NOTE
If MCR[RFEN] = 1, do not write 1 to this field.
0b - Disable
1b - Enable
30-26 DMA Last Word
DMALW Defines the last DMA address for each Enhanced RX FIFO element.
This table shows the number of elements and the last address for each Enhanced RX FIFO element
according to the value of DMALW.
DMALW Number of 32-bit Last FIFO address
words transferred
0 1 2000h
1 2 2004h
2 3 2008h
3 4 200Ch
4 5 2010h
5 6 2014h
6 7 2018h
7 8 201Ch
8 9 2020h
9 10 2024h
10 11 2028h

---

*Page 2435*

CAN (FlexCAN)
Field Function
DMALW Number of 32-bit Last FIFO address
words transferred
11 12 202Ch
12 13 2030h
13 14 2034h
14 15 2038h
15 16 203Ch
16 17 2040h
17 18 2044h
18 19 2048h
19 20 204Ch
NOTE
Undefined DMALW values in the table are reserved and must not be used.
Reserved
25-23
—
22-16 Number of Extended ID Filter Elements
NEXIF Defines the number of extended ID filter elements used during the Enhanced RX FIFO matching process.
The value of this field must be less than or equal to NFE + 1.
The number of standard ID filter elements is 2 × (NFE - NEXIF + 1).
This table shows the number of extended ID filters and standard ID filters available for Enhanced RX FIFO
if all filter elements are used.
NEXIF NFE Number of Extended ID Number of Standard ID
filter elements filter elements
0 63 0 128
1 63 1 126
2 63 2 124
3 63 3 122
4 63 4 120
5 63 5 118
6 63 6 116
7 63 7 114

---

*Page 2436*

CAN (FlexCAN)
Field Function
NEXIF NFE Number of Extended ID Number of Standard ID
filter elements filter elements
8 63 8 112
9 63 9 110
10 63 10 108
11 63 11 106
12 63 12 104
13 63 13 102
14 63 14 100
15 63 15 98
16 63 16 96
17 63 17 94
18 63 18 92
19 63 19 90
20 63 20 88
21 63 21 86
22 63 22 84
23 63 23 82
24 63 24 80
25 63 25 78
26 63 26 76
27 63 27 74
28 63 28 72
29 63 29 70
30 63 30 68
31 63 31 66
32 63 32 64
33 63 33 62
34 63 34 60
35 63 35 58
36 63 36 56

---

*Page 2437*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
NEXIF NFE Number of Extended ID Number of Standard ID
filter elements filter elements
37 63 37 54
38 63 38 52
39 63 39 50
40 63 40 48
41 63 41 46
42 63 42 44
43 63 43 42
44 63 44 40
45 63 45 38
46 63 46 36
47 63 47 34
48 63 48 32
49 63 49 30
50 63 50 28
51 63 51 26
52 63 52 24
53 63 53 22
54 63 54 20
55 63 55 18
56 63 56 16
57 63 57 14
58 63 58 12
59 63 59 10
60 63 60 8
61 63 61 6
62 63 62 4
63 63 63 2
64 63 64 0
Table continues on the next page...

---

*Page 2438*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
Reserved
15-14
—
13-8 Number of Enhanced RX FIFO Filter Elements
NFE Defines the total number of filter elements used during the enhanced RX FIFO matching process according
to the table.
NFE Maximum number of extended ID Maximum number of standard ID
filter elements (NEXIF = NFE + 1) filter elements (NEXIF = 0)
0 1 2
1 2 4
2 3 6
3 4 8
4 5 10
5 6 12
6 7 14
7 8 16
8 9 18
9 10 20
10 11 22
11 12 24
12 13 26
13 14 28
14 15 30
15 16 32
16 17 34
17 18 36
18 19 38
19 20 40
20 21 42
21 22 44
22 23 46
23 24 48
Table continues on the next page...

---

*Page 2439*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
NFE Maximum number of extended ID Maximum number of standard ID
filter elements (NEXIF = NFE + 1) filter elements (NEXIF = 0)
24 25 50
25 26 52
26 27 54
27 28 56
28 29 58
29 30 60
30 31 62
31 32 64
32 33 66
33 34 68
34 35 70
35 36 72
36 37 74
37 38 76
38 39 78
39 40 80
40 41 82
41 42 84
42 43 86
43 44 88
44 45 90
45 46 92
46 47 94
47 48 96
48 49 98
49 50 100
50 51 102
51 52 104
52 53 106
Table continues on the next page...

---

*Page 2440*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
NFE Maximum number of extended ID Maximum number of standard ID
filter elements (NEXIF = NFE + 1) filter elements (NEXIF = 0)
53 54 108
54 55 110
55 56 112
56 57 114
57 58 116
58 59 118
59 60 120
60 61 122
61 62 124
62 63 126
63 64 128
Reserved
7-5
—
4-0 Enhanced RX FIFO Watermark
ERFWM Defines the minimum number of CAN messages stored in the Enhanced RX FIFO. When that number is
reached, ERFSR[ERFWMI] becomes 1.
Minimum number of CAN messages = ERFWM + 1.
NOTE
If MCR[DMA] = 1, write 0h to this field.
70.6.2.39 Enhanced RX FIFO Interrupt Enable (ERFIER)
Offset
Register Offset
ERFIER C10h
Function
Contains the interrupt enables for the Enhanced RX FIFO.
Soft reset does not affect this register.

---

*Page 2441*

CAN (FlexCAN)
NOTE
Each module instance supports a different number of registers.
Instance Register supported Register not supported
FLEXCAN_0 ERFIER —
FLEXCAN_1 — ERFIER
FLEXCAN_2 — ERFIER
FLEXCAN_3 — ERFIER
FLEXCAN_4 — ERFIER
FLEXCAN_5 — ERFIER
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
ERFU ERFO ERFW ERFD
FWIE VFIE MIIE AIE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
Reserv
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Enhanced RX FIFO Underflow Interrupt Enable
ERFUFWIE Enables interrupt for ERFSR[ERFUFW] .
0b - Disable
1b - Enable
30 Enhanced RX FIFO Overflow Interrupt Enable
ERFOVFIE Enables interrupt for ERFSR[ERFOVF] .
0b - Disable
1b - Enable
29 Enhanced RX FIFO Watermark Indication Interrupt Enable
ERFWMIIE Enables interrupt for ERFSR[ERFWMI] .
Table continues on the next page...

---

*Page 2442*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
0b - Disable
1b - Enable
28 Enhanced RX FIFO Data Available Interrupt Enable
ERFDAIE Enables interrupt for ERFSR[ERFDA] .
0b - Disable
1b - Enable
Reserved
27-16
—
Reserved
15
—
Reserved
14-0
—
70.6.2.40 Enhanced RX FIFO Status (ERFSR)
Offset
Register Offset
ERFSR C14h
Function
Contains the status fields of the Enhanced RX FIFO including error indications and a clear FIFO field.
Soft reset does not affect this register.
NOTE
Each module instance supports a different number of registers.
Instance Register supported Register not supported
FLEXCAN_0 ERFSR —
FLEXCAN_1 — ERFSR
FLEXCAN_2 — ERFSR
FLEXCAN_3 — ERFSR
Table continues on the next page...

---

*Page 2443*

CAN (FlexCAN)
Table continued from the previous page...
Instance Register supported Register not supported
FLEXCAN_4 — ERFSR
FLEXCAN_5 — ERFSR
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
ERFU ERFO ERFW ERFD
R 0 0 ERFE ERFF
FW VF MI A
ERFC
W W1C W1C W1C W1C
LR
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
Reserv
R 0 ERFEL
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Enhanced RX FIFO Underflow Flag
ERFUFW Indicates whether an underflow condition occurred in the enhanced RX FIFO.
If ERFIER[ERFUFWIE] = 1, this field generates an interrupt.
0b - No such occurrence
1b - Underflow
30 Enhanced RX FIFO Overflow Flag
ERFOVF Indicates whether an overflow condition occurred in the Enhanced RX FIFO.
If ERFIER[ERFOVFIE] = 1, this field generates an interrupt.
0b - No such occurrence
1b - Overflow
29 Enhanced RX FIFO Watermark Indication Flag
ERFWMI Indicates whether the number of messages available in the Enhanced RX FIFO is greater than the
watermark defined in ERFCR[ERFWM] .
If ERFIER[ERFWMIIE] = 1, this field generates an interrupt.
0b - No such occurrence
Table continues on the next page...

---

*Page 2444*

CAN (FlexCAN)
Table continued from the previous page...
Field Function
1b - Number of messages in FIFO is greater than the watermark
28 Enhanced RX FIFO Data Available Flag
ERFDA Indicates whether there is at least one message stored in the ERX FIFO.
If ERFIER[ERFDAIE] = 1, this field generates an interrupt.
0b - No such occurrence
1b - At least one message stored in Enhanced RX FIFO
27 Enhanced RX FIFO Clear
ERFCLR Writing 1 to this field during Freeze mode resets the internal FIFO pointers, but the FIFO content stored in
RAM is not changed.
Writing to this field outside Freeze mode, or writing 0 to this field, has no effect.
0b - No effect
1b - Clear enhanced RX FIFO content
Reserved
26-18
—
17 Enhanced RX FIFO Empty Flag
ERFE Indicates whether Enhanced RX FIFO is empty.
0b - Not empty
1b - Empty
16 Enhanced RX FIFO Full Flag
ERFF Indicates whether enhanced RX FIFO is full.
0b - Not full
1b - Full
Reserved
15
—
Reserved
14-6
—
5-0 Enhanced RX FIFO Elements
ERFEL Indicates the number of CAN messages stored in the Enhanced RX FIFO.
70.6.2.41 High-Resolution Timestamp (HR_TIME_STAMP0 - HR_TIME_STAMP95)
Offset
For n = 0 to 95:

---

*Page 2445*

CAN (FlexCAN)
Register Offset
HR_TIME_STAMPn C30h + (n × 4h)
Function
Stores a copy of a 32-bit timer at the start or end of a CAN frame.
HR_TIME_STAMP0 stores the 32-bit timestamp associated with MB0, HR_TIME_STAMP1 stores the 32-bit timestamp
associated with MB1, and so on.
Reset does not affect these registers.
NOTE
Do not write to these registers outside Freeze mode.
Table 450. High-Resolution Timestamp register operation
TSTAMPCAP Captured timebase Capture point
b00 None None
b01 32 bits of high-resolution on-chip timer Seventh bit of the end-of-frame field for
transmission and sixth bit of the end-of-
frame field for reception.
b10 32 bits of high-resolution on-chip timer Start of frame
b11 32 bits of high-resolution on-chip timer Start of frame for classical CAN frame
format and res bit for CAN FD frame
format
NOTE
Each module instance supports a different number of registers.
Instance Register supported Register not supported
FLEXCAN_0 HR_TIME_STAMP0–HR_TIME_STAMP95 —
FLEXCAN_1 HR_TIME_STAMP0–HR_TIME_STAMP63 HR_TIME_STAMP64–HR_TIME_STAMP95
FLEXCAN_2 HR_TIME_STAMP0–HR_TIME_STAMP63 HR_TIME_STAMP64–HR_TIME_STAMP95
FLEXCAN_3 HR_TIME_STAMP0–HR_TIME_STAMP31 HR_TIME_STAMP32–HR_TIME_STAMP95
FLEXCAN_4 HR_TIME_STAMP0–HR_TIME_STAMP31 HR_TIME_STAMP32–HR_TIME_STAMP95
FLEXCAN_5 HR_TIME_STAMP0–HR_TIME_STAMP31 HR_TIME_STAMP32–HR_TIME_STAMP95

---

*Page 2446*

CAN (FlexCAN)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TS
W
Reset See Register reset values .
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TS
W
Reset See Register reset values .
Register reset values
Register Reset value
HR_TIME_STAMP0–HR_TIME_STAMP31 FLEXCAN_0–FLEXCAN_5: undefined
HR_TIME_STAMP32–HR_TIME_STAMP63
FLEXCAN_0–FLEXCAN_2: undefined
FLEXCAN_3–FLEXCAN_5: Register not supported
HR_TIME_STAMP64–HR_TIME_STAMP95
FLEXCAN_0: undefined
FLEXCAN_1–FLEXCAN_5: Register not supported
Fields
Field Function
31-0 High-Resolution Timestamp
TS Captures the copy of the timestamp of corresponding message buffer. This field always captures a 32-bit
timer value.
70.6.2.42 Enhanced RX FIFO Filter Element (ERFFEL0 - ERFFEL127)
Offset
For n = 0 to 127:
Register Offset
ERFFELn 3000h + (n × 4h)
Function
Stores the filter elements of the Enhanced RX FIFO.
For standard ID filtering, each ERFFEL register stores one filter element. For extended ID filtering, each pair of ERFFEL registers
stores one filter element.

---

*Page 2447*

CAN (FlexCAN)
ERFFEL registers can be written only in Freeze mode; otherwise, the module blocks them. Reset does not affect these registers.
They are located in RAM and must be explicitly initialized prior to any reception.
NOTE
Each module instance supports a different number of registers.
Instance Register supported Register not supported
FLEXCAN_0 ERFFEL0–ERFFEL127 —
FLEXCAN_1 — ERFFEL0–ERFFEL127
FLEXCAN_2 — ERFFEL0–ERFFEL127
FLEXCAN_3 — ERFFEL0–ERFFEL127
FLEXCAN_4 — ERFFEL0–ERFFEL127
FLEXCAN_5 — ERFFEL0–ERFFEL127
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
FEL
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
FEL
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
31-0 Filter Element Bits
FEL Stores filter elements. Each filter element is used during the match process. If the matching criteria are met,
a message is stored in the Enhanced RX FIFO.

#### 70.6.3 Message buffer structure

The message buffer structure used by FlexCAN is represented in the following figure. Both extended (29-bit identifier) and
standard (11-bit identifier) frames used in the CAN specification (Version 2.0 Part B) are represented. Each individual message
buffer is 16, 24, 40, or 72 bytes, depending on the quantity of data bytes allocated for the message payload: 8, 16, 32, or 64 data
bytes, respectively.
The memory area 80h–67Fh is used by the message buffers. When CAN FD is enabled, the exact address for each message
buffer depends on the size of its payload. See FlexCAN memory partition for CAN FD .

---

*Page 2448*

CAN (FlexCAN)
Table 451. Message buffer structure example with 64-byte payload
31 30 29 28 27 24 23 22 21 20 19 18 17 16 15 8 7 0
0h EDL BRS ESI CODE SRR IDE RTR DLC TIMESTAMP
4h PRIO ID (standard/extended) ID (extended)
8h Data byte 0 Data byte 1 Data byte 2 Data byte 3
Ch Data byte 4 Data byte 5 Data byte 6 Data byte 7
10h Data byte 8 Data byte 9 Data byte 10 Data byte 11
14h Data byte 12 Data byte 13 Data byte 14 Data byte 15
18h Data byte 16 Data byte 17 Data byte 18 Data byte 19
1Ch Data byte 20 Data byte 21 Data byte 22 Data byte 23
20h Data byte 24 Data byte 25 Data byte 26 Data byte 27
24h Data byte 28 Data byte 29 Data byte 30 Data byte 31
28h Data byte 32 Data byte 33 Data byte 34 Data byte 35
2Ch Data byte 36 Data byte 37 Data byte 38 Data byte 39
30h Data byte 40 Data byte 41 Data byte 42 Data byte 43
34h Data byte 44 Data byte 45 Data byte 46 Data byte 47
38h Data byte 48 Data byte 49 Data byte 50 Data byte 51
3Ch Data byte 52 Data byte 53 Data byte 54 Data byte 55
40h Data byte 56 Data byte 57 Data byte 58 Data byte 59
44h Data byte 60 Data byte 61 Data byte 62 Data byte 63
= Unimplemented or reserved
Table 452. Field descriptions
Mnemonic Field Description
EDL Extended Data Length Distinguishes between CAN format and CAN FD format frames. EDL must
not be 1 for message buffers configured to RANSWER with code field 1010b
(see Table 453 ).
BRS Bit Rate Switch Defines whether the bit rate is switched inside a CAN FD format frame.
ESI Error State Indicator Indicates whether the transmitting node is error-active or error-passive.
CODE Message Buffer Code Can be accessed (read or write) by the CPU and by the FlexCAN module
itself, as part of the message buffer matching and arbitration process. The
encoding is shown in Table 453 and Table 454 . See Functional description .

---

*Page 2449*

CAN (FlexCAN)
Table 453. Message buffer code for RX buffers
1 3
CODE description RX code BEFORE RX SRV RX code AFTER RRS Comment
2
new frame successful reception
0000b: INACTIVE. INACTIVE — — — Message buffer does
Message buffer is not not participate in the
active. matching process.
0100b: EMPTY. EMPTY — FULL — When a frame is
Message buffer is active received successfully
and empty. (after Move-in ), CODE is
automatically updated to
FULL.
0010b: FULL. Message FULL Yes FULL — The act of reading the
buffer is full. Control and Status word
followed by unlocking the
message buffer (SRV)
does not make CODE
return to EMPTY. It
remains FULL. If a
new frame is moved
to the message buffer
after the message buffer
is serviced, the code
remains FULL. See
Matching process for
matching details related
to FULL code.
No OVERRUN — If the message buffer
is FULL and a new
frame is moved to this
message buffer before
the CPU services it,
CODE is automatically
updated to OVERRUN.
See Matching process
for details about overrun
behavior.
0110b: OVERRUN. OVERRUN Yes FULL — If CODE indicates
Message buffer is being OVERRUN and the
overwritten into a full CPU has serviced the
buffer. message buffer, when
a new frame is moved
to the message buffer,
CODE returns to FULL.
No OVERRUN — If CODE already
indicates OVERRUN,
and another new
frame must be
moved, the message
buffer is overwritten
Table continues on the next page...

---

*Page 2450*

CAN (FlexCAN)
Table 453. Message buffer code for RX buffers (continued)
1 3
CODE description RX code BEFORE RX SRV RX code AFTER RRS Comment
2
new frame successful reception
again, and CODE
remains OVERRUN. See
Matching process for
details about overrun
behavior.
4
1010b: RANSWER . A RANSWER — TANSWER (1110b) 0 A Remote Answer was
frame was configured configured to recognize
to recognize a Remote a Remote Request
Request frame and frame received. After
transmit a Response that, a message buffer
5
frame in return. is set to transmit
a response frame.
CODE is automatically
changed to TANSWER
(1110b). See Matching
process for details.
If CTRL2[RRS] = 0,
transmit a response
frame when a remote
request frame with the
same ID is received.
— — 1 This code is ignored
during matching and
arbitration process. See
Matching process for
details.
6
CODE[0] = 1: BUSY. BUSY — FULL — Indicates that the
FlexCAN is updating the message buffer is being
— OVERRUN —
contents of the message updated. It automatically
buffer. The CPU must becomes 0 and does not
not access the message interfere with the next
buffer. CODE.
1. SRV: Serviced message buffer. Message buffer was read and unlocked by reading TIMER or another message buffer.
2. A frame is considered a successful reception after the frame is moved to a message buffer (move-in process). See
Move-in .
3. Remote Request Stored field. See Control 2 (CTRL2) .
4. Code 1010b is not considered TX and a message buffer with this code should not be aborted.
5. Code 1010b must be used in message buffers configured in CAN FD format, with EDL = 1.
6. For TX message buffers, the BUSY bit should be ignored upon read, except when MCR[AEN] = 1. If this field is 1, the
corresponding message buffer does not participate in the matching process.

---

*Page 2451*

CAN (FlexCAN)
Table 454. Message buffer code for TX buffers
CODE Description TX Code BEFORE TX MB TX Code AFTER Comment
frame RTR successful transmission
1000b: INACTIVE. INACTIVE — — Message buffer does not
Message buffer is not participate in arbitration
active. process.
1001b: ABORT. Message ABORT — — Message buffer does not
buffer is aborted. participate in arbitration
process.
1100b: DATA. Message DATA 0 INACTIVE Transmit data frame
buffer is a TX data frame unconditionally once. After
(MB RTR must be 0). transmission, the message
buffer automatically returns
to the INACTIVE state.
1100b: REMOTE. REMOTE 1 EMPTY Transmit remote request
Message buffer is a frame unconditionally
Transmit Remote Request once. After transmission,
frame (MB RTR must be the message buffer
1). automatically becomes an
RX Empty message buffer
with the same ID.
1110b: TANSWER. TANSWER — RANSWER This intermediate code is
Message buffer is a automatically written to
Transmit Response frame the message buffer by
from an incoming Remote the CHI as a result of
Request frame. a match to a Remote
Request frame. The
Remote Response frame is
transmitted unconditionally
once, then the code
automatically returns to
RANSWER (1010b). The
CPU can also write this
code with the same effect.
The Remote Response
frame can be a data
frame or another remote
request frame, depending
on the value of RTR.
See Matching process
and Arbitration process for
details.
Table 455. RX and TX message buffer field descriptions
Mnemonic Field Description
SRR Substitute Remote Request
Fixed recessive bit, used only in extended format. Write 1 to SRR for
transmission (TX Buffers). SRR is stored with the value received on the CAN
Table continues on the next page...

---

*Page 2452*

CAN (FlexCAN)
Table 455. RX and TX message buffer field descriptions (continued)
Mnemonic Field Description
bus for RX receiving buffers. It can be received as either recessive or dominant.
If FlexCAN receives this bit as dominant, it is interpreted as an arbitration loss.
1: Recessive value is compulsory for transmission in extended format frames.
0: Dominant is not a valid value for transmission in extended format frames.
IDE ID Extended Bit
Identifies whether the frame format is standard or extended.
1: Frame format is extended
0: Frame format is standard
RTR Remote Transmission
Affects the behavior of remote frames and is part of the reception filter. See
Request
Table 453 , Table 454 , and CTRL2[RRS] .
If FlexCAN transmits this field as 1 (recessive) and receives it as 0 (dominant),
it is interpreted as an arbitration loss. If this field is transmitted as 0 (dominant)
and it is received as 1 (recessive), FlexCAN treats it as a bit error. If the
value received matches the value transmitted, it is considered a successful
bit transmission.
1: If message buffer is TX, indicates that the current message buffer may have a
Remote Request frame to be transmitted. If the message buffer is RX, incoming
remote request frames may be stored.
0: Indicates that the current message buffer has a Data frame to be transmitted.
In an RX message buffer, it may be considered in matching processes.
NOTE
When configuring CAN FD frames, this field must be 0.
DLC Data Length Code
Indicates the length (in bytes) of the RX or TX data, which is located in offset
8h–Fh of the message buffer space (see Table 451 ) .
In reception, this field is written by FlexCAN, copied from the DLC field of the
received frame.
In transmission, this field is written by the CPU and corresponds to the DLC field
value of the frame to be transmitted.
When RTR = 1, the frame to be transmitted is a remote frame and does not
include the data field, regardless of the DLC field (see Table 457 ).
TIMESTAMP Free-Running Counter
Provides a copy of the Free-Running Timer, captured for TX and RX frames
Timestamp
when the beginning of the Identifier field appears on the CAN bus.
See Table 456 for Timestamp operation.
PRIO Local priority
Used only when MCR[LPRIOEN] = 1, and only makes sense for transmit
message buffers. These bits are not transmitted. They are appended to the
regular ID to define the transmission priority. See Arbitration process .
ID Frame Identifier
In standard frame format, only the 11 most significant bits (28 to 18) are
used for frame identification in both receive and transmit cases. The 18 least
Table continues on the next page...

---

*Page 2453*

CAN (FlexCAN)
Table 455. RX and TX message buffer field descriptions (continued)
Mnemonic Field Description
significant bits are ignored. In extended frame format, all bits are used for frame
identification in both receive and transmit cases.
DATA BYTE Data Field
Up to 64 bytes can be used for a data frame, depending on the size of payload
0–63
selected for the message buffers.
For RX frames, the data is stored as it is received from the CAN bus. DATA
BYTE ( n ) is valid only if n is less than DLC, as shown in Table 457 .
Table 456. Timestamp operation
TSTAMPCAP MBTSBASE TIMER_SOURCE Captured timebase Capture point
b00 bxx 0 CAN_TIMER incremented by Second bit of identifier field
CAN bit clock
b00 bxx 1 CAN_TIMER incremented by Second bit of identifier field
on-chip timer clock
bxx b00 0 CAN_TIMER incremented by Second bit of identifier field
CAN bit clock
bxx b00 1 CAN_TIMER incremented by Second bit of identifier field
on-chip timer clock
b01 b01 x Lower 16 bits of high- Seventh bit of the end of
resolution on-chip timer frame field for transmission
and sixth bit of the end of
frame field for reception
b01 b10 x Upper 16 bits of high- Seventh bit of the end of
resolution on-chip timer frame field for transmission
and sixth bit of the end of
frame field for reception
b10 b01 x Lower 16 bits of high- Start of frame
resolution on-chip timer
b10 b10 x Upper 16 bits of high- Start of frame
resolution on-chip timer
b11 b01 x Lower 16 bits of high- Start of frame for classical
resolution on-chip timer CAN frame format and res bit
for CAN FD frame format
b11 b10 x Upper 16 bits of high- Start of frame for classical
resolution on-chip timer CAN frame format and res bit
for CAN FD frame format
Table 457. DATA BYTE validity
DLC Valid data bytes
0 None
Table continues on the next page...

---

*Page 2454*

CAN (FlexCAN)
Table 457. DATA BYTE validity (continued)
DLC Valid data bytes
1 DATA BYTE 0
2 DATA BYTE 0–1
3 DATA BYTE 0–2
4 DATA BYTE 0–3
5 DATA BYTE 0–4
6 DATA BYTE 0–5
7 DATA BYTE 0–6
8 DATA BYTE 0–7
9 DATA BYTE 0–11
10 DATA BYTE 0–15
11 DATA BYTE 0–19
12 DATA BYTE 0–23
13 DATA BYTE 0–31
14 DATA BYTE 0–47
15 DATA BYTE 0–63

#### 70.6.4 FlexCAN memory partition for CAN FD

When CAN FD is enabled, FlexCAN RAM can be partitioned into blocks of 512 bytes each. Each block can accommodate a
number of message buffers depending on the configuration provided by FDCTRL[MBDSR n ] as shown in Table 458 .
Table 458. RAM partition
RAM block Number of MBs with 8 bytes Size control field in FDCTRL Number of MBs of different sizes, per block
(default range)
0 0 to 31 MBDSR0
MBDSR0 = 00, 32 MBs with 8-byte payload
MBDSR0 = 01, 21 MBs with 16-byte payload
MBDSR0 = 10, 12 MBs with 32-byte payload
MBDSR0 = 11, 7 MBs with 64-byte payload
1 32 to 63 MBDSR1
MBDSR1 = 00, 32 MBs with 8-byte payload
MBDSR1 = 01, 21 MBs with 16-byte payload
MBDSR1 = 10, 12 MBs with 32-byte payload
MBDSR1 = 11, 7 MBs with 64-byte payload
2 64 to 95 MBDSR2
MBDSR2 = 00, 32 MBs with 8-byte payload
MBDSR2 = 01, 21 MBs with 16-byte payload
MBDSR2 = 10, 12 MBs with 32-byte payload
MBDSR2 = 11, 7 MBs with 64-byte payload

---

*Page 2455*

CAN (FlexCAN)
Payload sizes of 16, 32, or 64 bytes may be configured in some or all of RAM blocks. In those cases, the total number of MBs and
their respective number order may differ from the default configuration of 8 bytes. Consider an example where:
• Block0 is configured to an 8-byte payload
• Block1 is configured to a 16-byte payload
• Block2 is configured to 32-byte payload
In this case, Table 459 indicates how the message buffers are arranged in RAM.
Table 459. RAM partition example
RAM block Payload size Number of MBs in the RAM Message buffer range
block
0 FDCTRL[MBDSR0] = 00, 8- 32 0 to 31
byte payload
1 FDCTRL[MBDSR1] = 01, 16- 21 32 to 52
byte payload
2 FDCTRL[MBDSR2] = 10, 32- 12 53 to 64
byte payload

#### 70.6.5 FlexCAN message buffer memory map

The FlexCAN memory buffers are allocated in memory according to the tables below.
Table 460. 8-byte message buffers
Address offset (hex) MBDSR = b00
8-byte payload
0080 MB0
0090 MB1
00A0 MB2
00B0 MB3
00C0 MB4
00D0 MB5
00E0 MB6
00F0 MB7
0100 MB8
0110 MB9
0120 MB10
0130 MB11
0140 MB12
0150 MB13
0160 MB14
Table continues on the next page...

---

*Page 2456*

CAN (FlexCAN)
Table 460. 8-byte message buffers (continued)
Address offset (hex) MBDSR = b00
8-byte payload
0170 MB15
0180 MB16
0190 MB17
01A0 MB18
01B0 MB19
01C0 MB20
01D0 MB21
01E0 MB22
01F0 MB23
0200 MB24
0210 MB25
0220 MB26
0230 MB27
0240 MB28
0250 MB29
0260 MB30
0270 MB31
0280 MB32
0290 MB33
02A0 MB34
02B0 MB35
02C0 MB36
02D0 MB37
02E0 MB38
02F0 MB39
0300 MB40
0310 MB41
0320 MB42
0330 MB43
0340 MB44
0350 MB45
Table continues on the next page...

---

*Page 2457*

CAN (FlexCAN)
Table 460. 8-byte message buffers (continued)
Address offset (hex) MBDSR = b00
8-byte payload
0360 MB46
0370 MB47
0380 MB48
0390 MB49
03A0 MB50
03B0 MB51
03C0 MB52
03D0 MB53
03E0 MB54
03F0 MB55
0400 MB56
0410 MB57
0420 MB58
0430 MB59
0440 MB60
0450 MB61
0460 MB62
0470 MB63
0480 MB64
0490 MB65
04A0 MB66
04B0 MB67
04C0 MB68
04D0 MB69
04E0 MB70
04F0 MB71
0500 MB72
0510 MB73
0520 MB74
0530 MB75
0540 MB76
Table continues on the next page...

---

*Page 2458*

CAN (FlexCAN)
Table 460. 8-byte message buffers (continued)
Address offset (hex) MBDSR = b00
8-byte payload
0550 MB77
0560 MB78
0570 MB79
0580 MB80
0590 MB81
05A0 MB82
05B0 MB83
05C0 MB84
05D0 MB85
05E0 MB86
05F0 MB87
0600 MB88
0610 MB89
0620 MB90
0630 MB91
0640 MB92
0650 MB93
0660 MB94
0670 MB95
Table 461. 16-byte message buffers
Address offset (hex) MBDSR = b01
16-byte payload
0080 MB0
0098 MB1
00B0 MB2
00C8 MB3
00E0 MB4
00F8 MB5
0110 MB6
0128 MB7
Table continues on the next page...

---

*Page 2459*

CAN (FlexCAN)
Table 461. 16-byte message buffers (continued)
Address offset (hex) MBDSR = b01
16-byte payload
0140 MB8
0158 MB9
0170 MB10
0188 MB11
01A0 MB12
01B8 MB13
01D0 MB14
01E8 MB15
0200 MB16
0218 MB17
0230 MB18
0248 MB19
0260 MB20
0280 MB21
0298 MB22
02B0 MB23
02C8 MB24
02E0 MB25
02F8 MB26
0310 MB27
0328 MB28
0340 MB29
0358 MB30
0370 MB31
0388 MB32
03A0 MB33
03B8 MB34
03D0 MB35
03E8 MB36
0400 MB37
0418 MB38
Table continues on the next page...

---

*Page 2460*

CAN (FlexCAN)
Table 461. 16-byte message buffers (continued)
Address offset (hex) MBDSR = b01
16-byte payload
0430 MB39
0448 MB40
0460 MB41
0480 MB42
0498 MB43
04B0 MB44
04C8 MB45
04E0 MB46
04F8 MB47
0510 MB48
0528 MB49
0540 MB50
0558 MB51
0570 MB52
0588 MB53
05A0 MB54
05B8 MB55
05D0 MB56
05E8 MB57
0600 MB58
0618 MB59
0630 MB60
0648 MB61
0660 MB62
Table 462. 32-byte message buffers
Address offset (hex) MBDSR = b10
32-byte payload
0080 MB0
00A8 MB1
00D0 MB2
Table continues on the next page...

---

*Page 2461*

CAN (FlexCAN)
Table 462. 32-byte message buffers (continued)
Address offset (hex) MBDSR = b10
32-byte payload
00F8 MB3
0120 MB4
0148 MB5
0170 MB6
0198 MB7
01C0 MB8
01E8 MB9
0210 MB10
0238 MB11
0280 MB12
02A8 MB13
02D0 MB14
02F8 MB15
0320 MB16
0348 MB17
0370 MB18
0398 MB19
03C0 MB20
03E8 MB21
0410 MB22
0438 MB23
0480 MB24
04A8 MB25
04D0 MB26
04F8 MB27
0520 MB28
0548 MB29
0570 MB30
0598 MB31
05C0 MB32
05E8 MB33
Table continues on the next page...

---

*Page 2462*

CAN (FlexCAN)
Table 462. 32-byte message buffers (continued)
Address offset (hex) MBDSR = b10
32-byte payload
0610 MB34
0638 MB35
Table 463. 64-byte message buffers
Address offset (hex) MBDSR = b11
64-byte payload
0080 MB0
00C8 MB1
0110 MB2
0158 MB3
01A0 MB4
01E8 MB5
0230 MB6
0280 MB7
02C8 MB8
0310 MB9
0358 MB10
03A0 MB11
03E8 MB12
0430 MB13
0480 MB14
04C8 MB15
0510 MB16
0558 MB17
05A0 MB18
05E8 MB19
0630 MB20

#### 70.6.6 legacy RX FIFO structure

When MCR[RFEN] = 1, the memory area 80h–DCh (which is normally occupied by MBs 0–5) is used by the reception Legacy
FIFO engine.
The region 80h–8Ch contains the output of the Legacy RX FIFO, which the CPU must read as a message buffer. This output
contains the oldest message that has been received but not yet read. The region 90h–DCh is reserved for internal use of the
Legacy RX FIFO engine.

---

*Page 2463*

CAN (FlexCAN)
An additional memory area, which starts at E0h and may extend up to 2DCh (normally occupied by MBs 6– 37 ) depending on the
value of CTRL2[RFFN] , contains the ID filter table (configurable from 8 to 128 table elements) that specifies filtering criteria for
accepting frames into the Legacy RX FIFO.
Out of reset, the ID filter table flexible memory area defaults to E0h and extends only to FCh, which corresponds to MBs 6 to 7
for RFFN = 0, for backward compatibility with previous versions of FlexCAN.
The following shows the Legacy RX FIFO data structure.
Table 464. Legacy RX FIFO structure
31 28 24 23 2