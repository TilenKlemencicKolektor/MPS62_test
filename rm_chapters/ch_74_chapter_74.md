<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 74 -->

# Chapter 74

# Quad Serial Peripheral Interface (QuadSPI)

#### 74.1 Chip-specific QuadSPI information

#### 74.1.1 QuadSPI configuration

Table 550. QuadSPI instances
Instance MCXE31B MCXE315/
MCXE316/MCXE317
QuadSPI Yes No
Table 551. QuadSPI configuration details
Configuration MCXE317/ MCXE31B
QuadSPI Tx FIFO size 32 words
QuadSPI Rx FIFO size 32 words
Look Up Table Size 4 words
AHB Buffer Size 256 Bytes
The Quad Serial Peripheral Interface (QuadSPI) block acts as an interface to single serial flash devices, with up to four
bidirectional data lines. The QuadSPI supports 4-pin Quad A interface only in SDR mode.
NOTE
Boot from QuadSPI is not supported but execution from external memory is supported.

---

*Page 3163*

Quad Serial Peripheral Interface (QuadSPI)
Peripheral Bus AHB Bus
(32 bit) (64 bit)
IP Control AHB Control
DMA and Interrupt Control
Registers
RX AHB slave
LUT Buffer
IP
command build
& control
received
(Data from flash) TX AHB
+ Buffer Buffer
Control Information
transmit received
(Data to flash) (Data from flash)
Command Arbitor
Clock Domain Crosser
Programmable sequence engine
Flexible l/O controller
Flash Interface
QuadSPI Flash A Interface
Figure 428. Block diagram

#### 74.1.2 Supported read modes

The table below provides an overview of the QuadSPI read modes.
Table 552. QuadSPI read modes
Read modes SDR support QuadSPI_MCR QuadSPI_MCR DLL/Data
(QuadSPI_MCR [DQS_EN] [DQS_FA_SEL] learning
[DDR_EN]=0) support
DQS sampling Pad loopback Yes 1 01 No
method

#### 74.1.3 QuadSPI initialization sequence

Following initialization sequence should be followed for proper QuadSPI operation:
• Enable QuadSPI module by MC_ME peripheral clock enable (register PRTNx_COFBy_CLKEN present within MC_ME).
Refer MC_ME chapter for peripheral mapping.
• Configure the SIUL2 registers MSCR[OBE] as 1 and MSCR[SSS] as 0 for QuadSPI_SCKFA pin.
• Initialize QuadSPI SCKFA by writing a sequence of 1010 to the SIUL2 register GPDO[PDO_a] for QuadSPI_SCKFA pin.
• Configure the SIUL2 register MSCR[OBE] back as 0 for the pins.

---

*Page 3164*

Quad Serial Peripheral Interface (QuadSPI)
• Initiate a dummy flash read to reset all DQS flops by itself and any crossing from DQS domain to IPS/AHB is taken care
by CDC logic.
• Initiate a peripheral software reset to QuadSPI controller by writing to QuadSPI controller’s MCR.
• Post this initialization sequence, the QuadSPI will work in intended deterministic manner.
NOTE
QuadSPI initialization is to be done before using QuadSPI after each functional reset.

#### 74.1.4 Pad clock loopback

This chip supports pad clock loopback. The QuadSPI can be configured to use clock loopback to sample input data. SCK is
delayed by the SCK pin output delay, plus the SCK pin input delay using pad loopback, and is configured by setting QuadSPI
config registers SOCCR[SOCCFG] and MCR[DQS_FA_SEL]. Enabling the loopback version of SCK can inprove the setup time
of the input data from the Flash.
For details of these register, see QuadSPI register descriptions.

#### 74.1.5 QuadSPI SOC Configuration register SOCCR[SOCCFG] implementation

The QuadSPI SOC Configuration register QuadSPI_SOCCR[SOCCFG] register is used to control dummy loopback pads and
obe_pull_timing_relax_b. Below is the description of it's bits:
Table 553. SOCCR[SOCCFG] implementation
Bit Description
Bit[0]
obe_pull_timing_relax_b : enables the timing relaxation by pulling obe for pad 1 for half cycle, if
0 then enabled else disabled.
Bit[1] ibe of QSPIA_SCK_DUMMY pad. 0: Disable input reciever 1: Enable input reciever.
Bit[2] obe of QSPIA_SCK_DUMMY pad. 0: Disable output driver 1: Enable output driver.
Bit[3] dse of QSPIA_SCK_DUMMY pad. 0: Disable drive strength 1: Enable drive strength.
Bit[4] pue of QSPIA_SCK_DUMMY pad. 0: Disable internal pullup or pulldown resistor 1: Enable
internal pullup or pulldown resistor.
Bit[5] pus of QSPIA_SCK_DUMMYpad. 0: Enable internal pulldown resistor if pue is set 1: Enable
internal pullup resistor if pue is set.
Bit[6] sre of QSPIA_SCK_DUMMY pad. 0: Disable slew rate control 1: Enable slew rate control.
Bit[31:7] Reserved
To Enable the Quadspi dummy PAD loopback use following settings
For Flash-A: MCR[DQS_FB_SEL] = 0x2
SOCCR[SOCCFG] = 0x0000000E (ibe=1, obe=1, dse=1 and sre=0)

#### 74.1.6 QuadSPI AHB Buffer write access control

In MCXE31B and MCXE317 devices, the QuadSPI AHB buffer does not support writes. A write to QuadSPI AHB buffer results in
error response from QuadSPI.
QuadSPI might behave unexpectedly in case if its AHB buffer is written with QuadSPI MCR[MDIS] =1. In cases wherein QuadSPI
is disabled with above MDIS control bit, XRDC must also be appropriately configured to block the accesses to QSPI AHB buffer
and indicate an error response.

---

*Page 3165*

Quad Serial Peripheral Interface (QuadSPI)

#### 74.2 Introduction

The QuadSPI module acts as an interface to a single serial flash memory device, with up to four bidirectional data lines.

#### 74.2.1 Features

QuadSPI supports the following features:
• Flexible sequence engine to support various flash memory vendor devices. As there is no specific standard, the module
supports various kinds of flash memories from different vendors. See Serial flash memory devices for example sequences.
• Single, dual, and quad modes of operation supported for Quad flash memories
• AHB master to read RX buffer data through AMBA AHB (64-bit width interface) or IPS registers space (32-bit access) and fill
TX buffer via IPS registers space (32-bit access) or using AHB (64-bit width interface)
— AHB master can be a DMA with a configurable inner loop size
• Multi-master accesses are allowed
— Flexible and configurable buffer for each master—total available buffer size is 256 bytes.
• All AHB accesses to flash/RAM memory devices are directly memory mapped to the chip system memory
• Programmable sequence engine to cater to future command/protocol changes and ability to support all existing vendor
commands and operations. The software needs to select the corresponding sequence according to the connected flash
memory device.
— Support for 3-byte and 4-byte addressing

#### 74.2.2 RX buffer push event

To add the valid entries into the RX buffer
By default, each buffer push event adds two entries to the RX buffer because the interface to the serial clock domain is 64 bits
in width. Depending on the number of bytes read from the serial flash memory device, it is possible for the very last buffer push
event that only one entry is added.
RBSR[RDBFL] is incremented by the number of entries added to the RX buffer.

#### 74.2.3 RX buffer POP event

To remove valid entries from the RX buffer
Each buffer POP event removes (RBCT[WMRK] + 1) valid entries from the buffer. BSR[RDBFL] is decremented by the same
number and RBSR[RDCTR] is incremented accordingly.

#### 74.2.4 Block diagram

The following figure shows a block diagram of the QuadSPI module.

---

*Page 3166*

Quad Serial Peripheral Interface (QuadSPI)
P erip h eral B u s A H B B u s
define (Addr, C m d) w r_data (D ata) rd_data (D ata) read (Addr, S ize) read_done (D ata)
IP _C ontrol A H B _C ontrol
D M A and Interrupt C ontrol
A H B _S erve A H B
R egisters
B uffer
T X R X
B uffer B uffer
IP _C trl
LU T com m and_build
fetch received
& buffer control
(addr, size, type) (D ata)
Q S P I_IC _S F M
C lock D om ain C rosser
Q S P I_IF
Program m able sequence engine
S C LK clock dom ain
F lexible I/O controller
S C LK clock dom ain
S C K FA
P C S FA 1 P C S FA 2
IO FA [3:0]
Q u ad S P I B u s F lash A
Figure 429. QuadSPI Block Diagram

#### 74.2.5 QuadSPI modes of operation

QuadSPI supports the following modes of operation:
• Normal mode: You can use this mode for write or read accesses to an external serial flash memory device. See Normal mode
for details.
— Serial flash memory write: You can program data into the flash memory through the IP interface only. See Flash memory
programming for details.
— Serial flash memory read: Read the contents of the serial flash memory device. Two separate read channels are
available through the RX buffer and AHB buffer. See Flash memory read for details.

---

*Page 3167*

Quad Serial Peripheral Interface (QuadSPI)
• Module Disable mode: You can use this mode for disabling serial flash memory clock and AHB command. The clock to
the non-memory mapped logic in QuadSPI can be stopped in the Module Disable mode. The module enters the mode by
setting MCR[MDIS] .

#### 74.3 External signal description

This section provides the external signal information for the QuadSPI module.
The following table lists the external signals belonging to the module in conjunction with the different modes of operation.
Table 554. Signal properties
Signal Function Direction Description
name
PCSFA1 Peripheral Chip Select O This signal is the chip select for the serial flash memory device A1 that
Flash Memory A1 represents the first of the two flash memory devices that share IOFA.
PCSFA2 Peripheral Chip Select O This signal is the chip select for the serial flash memory device A2 that
Flash Memory A2 represents the second of the two flash memory devices that share IOFA.
SCKFA Serial Clock Flash O This signal is the serial clock output to the serial flash memory device A.
Memory A
IOFA[3:0] Serial I/O Flash I/O These signals are the data I/O lines to/from the serial flash memory device
memory A A. See Driving external signals for details about the signal drive and timing
behavior. Note that the signal pins of the serial flash memory device may
change their function according to the SFM Command executed, leaving
them as control inputs when single and dual instructions are executed.
The module supports driving these inputs to dedicated values. In single I/O
mode, QuadSPI drives data on IOFA[0] and expects data on IOFA[1].
NOTE
Please refer to chip specific information to check the configuration of QuadSPI block.

#### 74.3.1 Driving external signals

Single/dual/quad instructions
Depending on the serial flash memory device connected to the QuadSPI module, there are instructions using a different number
of data lines:
• Single pad: Single line I/O with one data out and one data in line to/from the serial flash memory device
• Dual pad: Dual line I/O with two bidirectional I/O lines, driven alternatively by the serial flash memory device or the
QuadSPI module
• Quad pad: Quad line I/O with four bidirectional I/O lines, driven alternatively by the serial flash memory device or the
QuadSPI module
The different phases of the serial flash memory access scheme are shown in the following figure.

---

*Page 3168*

Quad Serial Peripheral Interface (QuadSPI)
IDLE INSTRUCTION ADDRESS MODE DUMMY DATA IDLE
PCSFx
SCKFx
Single Pad (I/O) Instructions (pad = 2'b0)
IOFx[0] Driven all the time, values taken according to phase
IOFx[1] Not Driven
IOFx[3:2] Driven all the time, values taken from QSPI_MCR[ISDnFx]
Dual Pad (I/O) Instructions (pad = 2'b01)
IOFx[1:0] Driven for Not Driven
Driven as given in the Note1 Tx Instr. only
IOFx[3:2] Driven all the time, values taken from QSPI_MCR[ISDnFx]
Quad Pad (I/O) Instructions (pad = 2'b10)
Driven for
IOFx[3:0] Driven as given in the Note1 Not Driven
Tx Instr. only
Figure 430. Serial flash memory access scheme
Note1:The IOs are driven from QuadSPI as per the number of pads configured for ongoing phase.
Note:The lines status will change based on command mode in case of instruction, address and mode phases. It can be either 1,2
or 4 lines
Following are the different phases and the I/O driving characteristics of the QuadSPI module:
• Idle: Serial flash memory device not selected – no interaction with the serial flash memory device and the IOFx signals
are driven.
• Instruction: Serial flash memory device selected – the instruction is sent to the serial device and all the IOFx signals are driven.
• Address: Serial flash memory address is sent to the device – all the IOFx signals are driven and this phase is not applicable
for all SFM commands.
• Mode: Mode bytes are sent to the serial flash memory device – all the IOFx signals are driven and this phase is not applicable
for all SFM commands.

---

*Page 3169*

Quad Serial Peripheral Interface (QuadSPI)
• Dummy: Dummy clocks are provided to the serial flash memory device. See Figure 430 for the IOFx signals driven. The actual
data lines required for the SFM command executed are not driven for data read commands.
NOTE
— This phase is not applicable for all the SFM commands.
— All read commands in Dual pad, Quad pad modes must use a Dummy phase immediately before the
Data phase. The Dummy phase pad configuration in the LUT must use the same number of pads as the
subsequent Data phase. Note that this restriction is not applicable to Single-pad mode.
• Data: Serial flash memory data are sent to or received from the serial flash memory device. See the preceding figure for the
IOFx signals driven. The actual data lines required for the SFM command executed are not driven for data read commands.
NOTE
This phase is not applicable for all the SFM commands.
The PCSFx and SCKFx signals are driven permanently throughout all the phases. In the individual flash memory mode, this
applies to the selected flash memory device.
Access to a single, individual serial flash memory device
See Serial flash memory access schemes for details.
Read access to two serial flash memory devices attached to the QuadSPI module in parallel. See Serial flash memory access
schemes for details.

#### 74.4 Functional description

This section provides a functional description of the QuadSPI module.

#### 74.4.1 Serial flash memory access schemes

In the individual flash memory mode, all supported commands are available.

#### 74.4.2 Normal mode

This mode allows communication with an external serial flash memory device. Compared to the standard SPI protocol, this
communication method uses up to four bidirectional data lines operating at high-data rates. The communication to the external
serial flash memory device consists of an instruction code and optional address, mode, dummy, and data transfers. The flexible
programmable core engine described below is immune to a wide variety of command or protocol differences in the serial flash
memory devices provided by various flash memory vendors.
74.4.2.1 Programmable sequence engine
The core of the QuadSPI module is a programmable sequence engine that works on "instruction-operand" pairs. The core
controller executes each programmed instruction sequentially. The complete list of instructions and the corresponding operands
are provided in the following table.
Table 555. Instruction set
Instruction Instruction Pins Operand Action on serial flash memories
encoding
CMD 1d N={0,1,2}d 8-bit Provides the serial flash memory with the SFM command
command operand (Encoded) on the number of pads specified in
0d - One pad
value STR mode.
1d - Two pads
2d - Four pads
Table continues on the next page...

---

*Page 3170*

Quad Serial Peripheral Interface (QuadSPI)
Table 555. Instruction set (continued)
Instruction Instruction Pins Operand Action on serial flash memories
encoding
ADDR 2d Number of Provide the serial flash memory with address cycles according
address bits to the operand on the number of pads specified
to be sent (for
The actual address to be provided is derived from the incoming
example, 24d
address in case of AHB-initiated transactions and the value of
=> 24 address
SFAR in case of IPS-initiated transactions.
bits required)
DUMMY 3d Number of Provide the serial flash memory with dummy cycles according
dummy clock the operand
cycles (should
The PAD information defines the number of pads in input mode.
be <= 64 and
For example, one pad implies that pad 1 is not driven, rest all
> 2 cycles)
are driven.
MODE 4d 8-bit mode Provide the serial flash memory with 8-bit operand on the
value number of pads specified
MODE2 5d N={0,1}d 2-bit mode Provide the serial flash memory with 2-bit operand on the
1
value number of pads specified
MODE4 6d N={0,1,2}d 4-bit mode Provide the serial flash memory with 4-bit operand on the
2
value number of pads specified
READ 7d N={0,1,2}d Read data Read data from flash memory on the number of pads specified
size in bytes
0d - One pad The data size can be overwritten by writing to the ADATSZ field
( for AHB
of the BUFxCR registers for AHB-initiated transactions and to
1d - Two pads transactions,
the IDATSZ field of the IP Configuration Register (IPCR) for
your
2d - Four pads IPS-initiated transactions.
application
should ensure
that data size
is a multiple of
8 bytes)
WRITE 8 Write data Write data on the number of pads specified
size in bytes
The data size can be overwritten by writing to the IDATSZ field
of IP Configuration Register (IPCR) .
JMP_ON_CS 9d NA Instruction Every time the CS is deasserted, jump to the instruction pointed
3
number to by the operand. This instruction allows the programmer
to specify the behavior of the controller when a new read
transaction is initiated following a CS deassertion.
3
STOP 0d NA NA Stop execution; deassert CS
1. For a one-pad instruction, MODE2 takes two serial flash memory clock cycles on the flash memory interface.
2. For a one-pad instruction, MODE4 takes four serial flash memory clock cycles on the flash memory interface. For a
four-pad instruction, MODE4 takes one serial flash memory clock cycle on the flash memory interface.
3. Sequence ending with this instruction must have all remaining bits as 0s after it.

---

*Page 3171*

Quad Serial Peripheral Interface (QuadSPI)
The programmable sequence engine allows you to configure the QuadSPI module according to the serial flash memory connected
on board. This flexible structure is compatible with new command or protocol changes from different vendors.
74.4.2.2 Flexible read xAHB buffers
To reduce the latency of the reads for AHB masters, the data read from serial flash memory is buffered in flexible AHB buffers.
There are four such flexible buffers. The size of each of these buffers is configurable with the minimum size being 0 bytes and
maximum size being the size of the complete buffer instantiated (256 bytes). The size of buffer 0 ranges from 0 to BUF0IND. The
size of buffer 1 ranges from BUF0IND to BUF1IND, buffer2 from BUF1IND to BUF2IND and, buffer 3 ranges from BUF2IND to
the size of the complete buffer (256 bytes).
Each flexible AHB buffer is associated with the following:
• An AHB master: Optionally, buffer3 may be configured as an "all master" buffer by writing 1 to BUF3CR[ALLMST]. When
buffer3 is configured in such a way, any access from a master not associated with any other buffer is routed to buffer3.
• A datasize field representing the amount of data to be fetched from the flash memory on every missed access.
The master ID of every incoming request is checked and the data is returned or fetched into the corresponding associated buffer.
See the chip-specific QuadSPI information for details about master IDs and their corresponding components. Every missed
access to the buffer causes the controller to clear the buffer and fetch the BUFxCR[ADATSZ] amount of data from the serial
flash memory. As such, you need not configure the buffer size to be greater than ADATSZ because the locations greater than
ADATSZ are never used. For any AHB access, the sequence pointed to by BFGENCR[SEQID] is used for the initiated flash
memory transaction. The data is returned to the master as soon as the requested amount is read from the serial flash memory.
The controller; however, continues to prefetch the rest of the data in anticipation of a next consecutive request. See Figure 431
that shows flexible AHB buffers.
BFGENCR[SEQID] points to an index of the LUT. See LUT for details.
Parametrizable max
size LUT
BFGENCR[SEQID]
buffer3
BUF2IND
buffer2
BUF1IND
buffer1
BUF0IND
buffer0
Figure 431. Flexible read AHB buffers

---

*Page 3172*

Quad Serial Peripheral Interface (QuadSPI)
74.4.2.3 Extended prefetch or Streaming mode
There is an option in QuadSPI to fetch more data than the maximum size of AHB prefetch buffer, which is 1024 bytes. This mode
can be used in streaming use cases where the data is constantly read in incremental order.
To use this mode, configure buffer3 as an "all master" buffer by writing 1 to BUF3CR[ALLMST] , and program BUF3CR[ADATSZ]
greater than 1024 bytes. Also ensure that complete buffer is assigned to Buffer 3 Configuration Register (BUF3CR) and remaining
buffer sizes should be programmed to 0 by writing 0 to Buffer 0 Top Index Register (BUF0IND) , Buffer 1 Top Index Register
(BUF1IND) , and Buffer 2 Top Index Register (BUF2IND) registers. The maximum size that can be programmed for prefetch is
4096 bytes. In this mode, the complete 1K buffer size is used in rollover fashion, such that when the complete buffer is written, it
starts writing the prefetch data again from starting location 0. So, if the AHB read is done continuously at sufficient speed, complete
4K data can be read without the buffer going full. In this mode, QuadSPI considers last AHB read address as the read pointer in
AHB buffer, and when the prefeched data is being written in one location before the read pointer, it generates AHB full flag and
prefetch is aborted. So, to efficiently utilize the streaming mode, ensure that AHB read is done in incremental/serial addresses with
sufficient throughput. Also when the software is switching from streaming mode to normal modes, or vice-versa, it should clear
the AHB buffers using software abort.
74.4.2.4 Abort mechanism during AHB read
Any ongoing read transaction is aborted if a request from the same master arrives for a location other than the location at which
the transaction is going on. The abort can happen at any point of time.
74.4.2.5 HBURST support with AHB read
QuadSPI controller supports HBURST and HSIZE on the AHB read interface. HBURST indicates if the transfer forms part of
a burst. Four, eight, and sixteen beat bursts are supported and the bursts might either be incrementing or wrapping. HSIZE
indicates the size of the transfer, and supports 8-, 16-, 32-, and 64-bit data sizes. In case of WRAP accesses, QuadSPI generates
aligned accesses to serial flash memory if there is no buffer hit for any incoming, non-sequential AHB read access. In case there
is a buffer hit, the incoming address in the haddr line is latched as it is. If the total burst size is more than the data prefetch
size, an error response is generated and the value of FR[AIBSEF] is configured as 1 . The data prefetch size can be defined by
BUFxCR[ADATSZ] or data size mentioned in the sequence pointed to by the SEQID field when ADATSZ is programmed as 0. In
case of wrap burst, data prefetch size must be greater than or equal to the wrap burst size + 32 bytes. A few examples are shown
in the figure below:

---

*Page 3173*

Quad Serial Peripheral Interface (QuadSPI)
HADDR = 0x38 HADDR = 0x38
HBUST = WRAP4 HBUST = INCR4
HSIZE = 64 bits HSIZE = 64 bits
Flash xsaction start = 0x20 Flash xsaction start = 0x38
Incoming AHB access= 0x38, 0x20, 0x28, 0x30 Incoming AHB access= 0x38, 0x40, 0x48, 0x50
HADDR = 0x50
HBUST = WRAP8
HSIZE = 64 bits
Flash xsaction start = 0x40
Incoming AHB access= 0x50, 0x58, 0x60, 0x68, 0x70, 0x78, 0x40, 0x48
HADDR = 0xD0
HBUST = WRAP16
HSIZE = 64 bits
Flash xsaction start = 0x80
Incoming AHB access= 0xD0, 0xD8, 0xE0, ...0xF8, 0x80, 0x88, ... 0xC8
HADDR = 0xD4
HBUST = WRAP8
HSIZE = 32bits
Flash xsaction start = 0xC0
Incoming AHB access= 0xD4, 0xD8, 0xDC, 0xC0, 0xC4, 0xC8,0xCC, 0xD0
HADDR = 0x54
HBUST = INCR8
HSIZE = 32bits
Flash xsaction start = 0x54
Incoming AHB access= 0x54, 0x58, 0x5C, 0x60, 0x64, 0x68,0x6C, 0x70
Figure 432. QuadSPI HBURST support
NOTE
The software must take care that the prefetch size should never be set less than the minimum data needed by any
external interface to start processing.
NOTE
Whenever a core accesses QuadSPI memory with cache enabled, the prefetch size must be configured as equal
or more than the cache line size; otherwise, FR[AIBSEF] error appears.
74.4.2.6 LUT
The LUT consists of a number of pre-programmed sequences. Each sequence is basically a sequence of instruction-operand
pairs, which when executed sequentially, generate a valid serial flash memory transaction. Each sequence can have a maximum
of 10 instruction-operand pairs. The LUT can hold a maximum of sequences. The figure below shows the basic structure of the
sequence in the LUT.
At reset, the index 0 of the LUT[0..4] is programmed with a basic read sequence as described in Read sequence . After reset, the
complete LUT may be reprogrammed according to the chip connected on board. To protect its contents, during a code runover,
the LUT might be locked, after which a write to the LUT will not be successful until it has been unlocked again. The key for locking
or unlocking the LUT is 5AF05AF0h, and the associated processes are as follows:
Locking the LUT
1. Write the key 5AF05AF0h into the LUT Key Register (LUTKEY) .
2. Write 0b01 to the LUT Lock Configuration Register (LCKCR) . Note that this IPS transaction should immediately follow the
above IPS transaction (no other IPS transaction can be issued). A successful write to this register locks the LUT.

---

*Page 3174*

Quad Serial Peripheral Interface (QuadSPI)
Unlocking the LUT
1. Write the key 5AF05AF0h into the LUT Key Register (LUTKEY) .
2. Write 0b10 to the LUT Lock Configuration Register (LCKCR) . Note that this IPS transaction should immediately follow the
above IPS transaction (no other IPS transaction can be issued in between). A successful write to this register unlocks
the LUT.
The lock status of the LUT can be read from the LCKCR[UNLOCK] and LCKCR[LOCK] fields.
Some example sequences are defined in Example sequences . After reset the instruction sequence 0 is populated with the default
read sequence shown in the table below.
Table 556. Read sequence
Instruction Pad Operand Comment
CMD 0h 3h Read data byte command on one pad
ADDR 0h 18h 24 address bits to be sent on one pad
READ 0h 8h Read 64 bits
JMP_ON_CS 0h 0h Jump to instruction 0 (CMD)
NOTE
If DLL is disabled then JMP_ON_CS or STOP instruction can be used else only STOP instruction can be used.
74.4.2.7 Issuing SFM commands
Each access to the external device follows this sequence:
1. You must pre-populate the LUT with the serial flash memory command sequences that are required for the flash memory
device being used.
2. The module executes the instructions in this sequence one by one. The transaction starts and the module configures the
value of SR[BUSY].
3. Communication with the external serial flash memory device starts and the transaction executes.
4. After the transaction is complete (all transmit and receive operations with the external serial flash memory device are
complete), the module resets SR[BUSY]. In case of an IP command, FR[TFF] is asserted.
For details, see Flash memory programming and Flash memory read .
You can trigger the processing of SFM commands in the QuadSPI module in one of the following ways:
Using IP commands
For IP commands, the required components need to be written into the following registers and in this sequence:
1. Write the serial flash memory address to be used as provided in the Serial Flash Address Register (SFAR) . For IP
commands not related to specific addresses, the base address of the related flash memory needs to be programmed.
For example, for an instruction which does not require an address (that is, write enable instruction), the SFAR should be
programmed with the base address of the memory the command is to be sent to.
2. Write the sequence ID and data size details in the IP Configuration Register (IPCR) .
3. Note that writing a value to IPCR[SEQID] must be the last step of the sequence. It is possible to combine all the fields of
the IPCR into one single write. See IP Configuration Register (IPCR) for details.
Using AHB commands

---

*Page 3175*

Quad Serial Peripheral Interface (QuadSPI)
Any AHB memory-mapped access is routed to one of the buffers depending on the master ID of the request. If the access
is a "miss," a new serial flash memory transaction is initiated. The transaction is based on the sequence pointed to by
BFGENCR[SEQID] as described in Flexible read xAHB buffers .
An AHB access is considered memory mapped when the access is to the memory-mapped serial flash memories, as described
in Memory Mapped Serial Flash Data - Individual Flash mode on Flash memory A .
74.4.2.8 Flash memory programming
In all NOR Flash devices memory sector to be written needs to be erased first. The programming sequence is then initiated in the
following way:
1. Check that SR[BUSY] is de-asserted or the value of the BUSY field is 0, also, check that the TX buffer is empty. If you need
to discard the data present in the TX buffer (SR[TXNE]) then the TX buffer must be cleared by writing 1 to MCR[CLR_TXF].
2. Program the address related to the command in SFAR.
3. Provide initial data for the program command into the circular buffer through the TBDR. At least one words of data must be
written into the TX buffer up to a maximum of 32 entries.
4. Program the IPCR to trigger the command. IPCR[SEQID] should point to an index of the LUT that has the flash memory
program sequence pre-programmed. Write an appropriate value to IPCR[IDATSZ] to denote the size of the write in bytes.
5. Repeat step 3, depending on the amount of data required, until all of the required data is written to the TBDR. SR[TXFULL]
can be used to check if the buffer is ready to receive more data. At any time, TBSR[TRCTR] can be read to check how many
words have been written into the TX buffer.
After writing to IPCR[SEQID] (see step 4 ) , the module starts executing the programmed sequence . The software ensures that
the correct sequence is programmed into the LUT in accordance with the flash memory connected to the module. The data is
fetched from the TX buffer. It consists of 32 entries of 32-bit sizes and is organized as a circular FIFO, the read pointer for which is
incremented after each fetch. When all the data is transmitted, the QuadSPI module returns from the busy state to the idle state.
However, this is not true for the external device because the internal programming is still ongoing. You may monitor the relevant
status information available from the serial flash memory device and ensure that the programming is done appropriately.
74.4.2.9 Flash memory read
Host access to the data stored in the external serial flash memory device is performed in two steps. First, the data must be read
into the internal buffers and in the second step, these internal buffers can be read by the host.
Reading serial flash memory data into the QuadSPI module internal buffers
A read access to the external serial flash memory device can be triggered in two different ways:
• IP command read: For reading flash memory data into the RX buffer, you must provide the correct sequence ID in
IPCR[SEQID]. The sequence ID points to a sequence in the LUT. The software needs to ensure that a correct read sequence
is programmed in the LUT in accordance with the serial flash memory device connected on board. You must program the
SFAR , and IPCRs. All available read commands supported by the external serial flash memory are possible.
Optionally, it is possible to clear the RX buffer pointer prior to triggering the IP command by writing a 1 to MCR[CLR_RXF].
This will invalidate the data currently present in the RX buffer and any new read data will overwrite the old one.
Using these inputs, the complete transaction is built when IPCR[SEQID] is written to The transaction related to the read
access starts and the requested number of bytes is fetched from the external serial flash memory device into the RX buffer.
As the read access is triggered by an IP command, the value of both SR[IP_ACC] and SR[BUSY] is set to 1.. A count of the
number of entries currently in the Rx buffer can be obtained from RBSR[RDBFL].
Communication with the external serial flash memory stops if the specified number of bytes are read (on successful
completion of the transaction).
• AHB command read: For reading flash memory data into the AHB buffer, you must:
— Set up a read access by a master to the address range in the system memory map, which the external serial flash
memory devices are mapped to.

---

*Page 3176*

Quad Serial Peripheral Interface (QuadSPI)
—
— Program the buffer registers corresponding to the AHB master initiating the request.
— Provide the correct sequence ID in the BFGENCR. The software ensures that a correct read sequence is programmed
in the LUT in accordance with the serial flash memory device connected on board. Flash memory device selection and
access mode are determined by the address accessed in the AHB address space associated with the QuadSPI module
(see Memory-mapped serial flash memory data—individual flash memory mode on flash memory A
On each AHB read access to the memory mapped area, the valid data in the AHB buffer is checked against the address
requested in the actual read. When the AHB read request cannot be served from the content of the AHB buffer, the buffer
is flushed and the controller executes the sequence pointed to by the sequence ID. The requested number of buffer entries
defined in BUFxCR[ADATSZ] is then fetched from the external serial flash memory device into the internal AHB buffer. As
the read access is triggered through the AHB bus, the value of SR[AHB_ACC] is set, driving SR[BUSY] in turn, until the
transaction is complete. Communication with the external serial flash memory stops when the specified number of entries
is filled.
Data transfer from the QuadSPI module internal buffers
The data read out from the external serial flash memory device by the QuadSPI module is stored in the internal buffers. The means
of accessing the data from the buffer differs depending on which buffer the data is loaded to. See Block diagram for details on the
two available buffers, the RX buffer and the AHB buffer, in this module. The buffer appears transparent to you and is non-memory
mapped. See the "Flexible AHB Buffer" section for details.

---

*Page 3177*

Quad Serial Peripheral Interface (QuadSPI)
This buffer appears transparent to you and is non-memory mapped. See the "Flexible AHB Buffer" section for details.
QuadSPI

#### Memory mapped access

AHB buffer

## ARDB access

Read access
Register access (RBDR)
RX buffer
Read access
External flash memory
System AHB/IPS
Memory mapped access
TX buffer
Write access

### Note: Byte swapper for endianness

Figure 433. QuadSPI memory map
The RX buffer is implemented as FIFO of depth 32 entries of 4 bytes. Its content is accessible in two different address areas, both
referring to identical data and the same physical memory:
• In the IPS address space in the area associated with RX Buffer Data Register (RBDR0 - RBDR31) .
• In the AHB address space in the area associated with AHB RX Data Buffer Register (ARDB0 - ARDB127) .Two successive
entries are accessed with one single 64-bit AHB read operation.
The RX buffer operation can be summarized as follows:
• RBCT[WMRK] determines at which fill level SR[RXWE] is asserted and how many entries are removed from the RX buffer
on each buffer POP operation.
• SR[RXWE] indicates that the configured number of data entries is available in the RX buffer and RBSR[RDBFL] indicates
how many valid entries are available in total.
• The first entry (RBDR0 or ARDB0) always corresponds to the first valid entry in the RX buffer.
For details, see RX Buffer Data Register (RBDR0 - RBDR31) and AHB RX Data Buffer Register (ARDB0 - ARDB127) .
• Flag-based data read of the RX buffer is performed by polling SR[RXWE]. When it is asserted, the valid entries can be
read either via the IPS address space (RBDRn) or the AHB address space (ARDBn). A buffer POP operation must be

---

*Page 3178*

Quad Serial Peripheral Interface (QuadSPI)
triggered by the application by writing a 1 to FR[RBDF]. This automatically updates the FIFO to point to the next entry as
defined by RBCT[WMRK]. For example, if WMRK is set to 3, then the buffer discards 16 bytes of data.
• DMA-controlled data read of the RX buffer is performed by using the DMA module. The application must ensure that the
DMA controller of the related chip is programmed appropriately, as described in DMA usage .
• DMA-controlled read out is triggered fully automatically by the assertion of SR[RXWE]. The related buffer POP operation
is also handled completely inside the QuadSPI module. As in the case explained here, accessing the RX buffer content
either on RBDRn or ARDBn related addresses is equivalent.
• AHB buffer data read via memory-mapped access: This kind of access is performed by reading one of the addresses
assigned to the external serial flash memory device(s) within the range specified in Table 578 . If this is not the case, a
memory-mapped AHB command read is triggered as described above. If the requested data is already available in the
AHB buffer, it is provided directly to the host.
When an AHB access is made to the flash memory mapped address, the data is fetched and returned to the AHB interface. The
AHB interface is stalled until the data is fetched. As soon as the data from the requested address is read by the QuadSPI module,
the AHB read access is served. Therefore, it is possible to run sequential reads from the AHB buffer at arbitrary speed without
the need to monitor any information about the availability of the data. Nevertheless, this access scheme stalls the AHB bus for
the time required to read the data from the serial flash memory device. If you know that the access is sequential, a better way is
to have a prefetch enabled by programming the value of BUFxCR[ADATSZ] so that the data is fetched into the buffer before the
next sequential AHB access.
As long as the host restricts its accesses to the data present in the buffer and to the data currently fetched from the serial
flash memory, it is possible to run the host read from the AHB buffer simultaneously with the serial flash memory read into the
AHB buffer.
74.4.2.10 Byte ordering of serial flash memory read data
The basic scheme is that the first byte read out of the serial flash memory device, which is addressed by SFAR[SFADR],
corresponds to RBDR0[ 31:24 ] for IP command read. Similarly, to send a single byte it should be positioned in TBDR[0:7]. In
contrast to that for AHB command read, the bytes are always positioned according to the byte ordering of the AHB bus.
• Byte ordering in individual flash memory mode
The following table provides the byte ordering scheme of how the byte oriented data space of the serial flash memory device
is mapped into one single 32-bit entry of the RX buffer or the AHB buffer. The table is valid within the following context:
— Flash memory A in individual flash memory mode
— All AHB data read commands with 32-bit access size
Table 557. Byte ordering in individual flash memory mode
Serial flash memory byte numbering 3 2 1 0
Buffer entry bit position [31:0] [31:24] [23:16] [15:8] [7:0]
(32-bit data width)
NOTE
For IP commands, the read size can be specified as number of bytes. If this number is not a multiple of four, then
the last buffer entry is not completely filled with the missing higher numbered bytes at undefined values.
For AHB commands and reads, starting from an address not aligned to 32-bit boundaries, the requested bytes are given at
the appropriate positions according to the AMBA AHB specification.
• Buffer entry ordering for 64-bit read access
For read access via the AHB interface, a 64-bit access is possible. Each 64-bit access reads two 32-bit entries,
simultaneously. The ordering of these 32-bit entries within the 64-bit word is provided in the following table.

---

*Page 3179*

Quad Serial Peripheral Interface (QuadSPI)
Table 558. 64-bit read access buffer entry ordering
AHB read data bit position [63:0] [63:32] [31:0]
Buffer entry # Odd (1, 3, 5, ...) Even (0, 2, 4, ...)
74.4.2.11 Normal mode interrupt and DMA requests
The QuadSPI module has different flags that can only generate interrupt requests and one flag that can generate an interrupt as
well as DMA requests. The following table lists the eight conditions. Note that the flags mentioned in the table are associated with
the Flag Register (FR) .
Table 559. Interrupt and DMA request conditions
Condition Flag (FR) DMA
TX buffer fill TBFF -
TX buffer underrun TBUF -
Illegal instruction error ILLINE -
RX buffer drain RBDF X
RX buffer overflow RBOF -
AHB buffer overflow ABOF -
AHB sequence error ABSEF -
AHB illegal transaction error AITEF -
AHB illegal burst size error AIBSEF -
IP command trigger during AHB IPAEF -
access error
IP command trigger could not be IPIEF -
executed error
IP command related transaction finished TFF -
Each condition has a corresponding field in Flag Register (FR) and a request enable field in DMA Request Select and Enable
Register (RSER) . FR[RBDF] has separate enable fields for generating IRQ and DMA requests. Note that not all the fields have
an individual IRQ line. See the chip's Interrupt Vector table for details.
• Transmit buffer fill interrupt request
This indicates that the TX buffer can accept new data. The buffer is asserted if FR[TBFF] is asserted and if the value of the
corresponding enable field, RSER[TBFIE], is 1. See TX buffer Operation for details on the assertion of FR[TBFF].
Apart from IRQ, it is possible to handle the TX buffer fill by using the DMA. If the value of RSER[TBFDE] is 1, a DMA request
is triggered when the number of available space in the TX buffer is more than the TBCT[WMRK] valid entries and value of
SR[TXWA] is set . The application must configure the environment appropriately (for example, the DMA controller) for the
DMA transfer.
• Receive buffer drain interrupt or DMA request
This is derived from FR[RBDF], indicating that the RX buffer of the QuadSPI module has data available from the serial flash
memory device to be read by the host. It remains set as long as RBSR[RXWE] is configured. Also, RSER[RBDIE] enables
the related IRQ.

---

*Page 3180*

Quad Serial Peripheral Interface (QuadSPI)
Apart from the IRQ, it is possible to handle the RX buffer drain by using the DMA. If the value of RSER[RBDDE] is 1, a
DMA request is triggered when the RX buffer contains more than RBCT[WMRK] valid entries. The application must set the
environment appropriately (for example, the DMA controller) for the DMA transfers.
• Buffer overflow/underrun interrupt request
This is a combination of the following fields (all located in the Flag Register (FR) with the related enable bits in the DMA
Request Select and Enable Register (RSER) ):
— TBUF - TX buffer underrun, enabled by TBUIE
— RBOF - RX buffer overflow, enabled by RBOIE
— ABOF - AHB buffer overflow, enabled by ABOIE
— The transmit buffer underrun indicates that an underrun condition in the TX buffer has occurred. It is generated when
a write instruction is triggered whilst the TX buffer is empty and the value of RSER[TBUIE] is 1.
— The receive buffer overflow indicates that an overflow condition in the RX buffer has occurred. It is generated when the
RX buffer is full, an additional read transfer attempts to write into the RX buffer, and the value of RSER[RBOIE] is 1.
— The AHB buffer overflow indicates that an overflow condition in the AHB buffer has occurred. It is generated when the
AHB buffer is full, an additional read transfer attempts to write into the AHB buffer and the value of RSER[ABOIE] is 1.
— The data from the transfers that generated the individual overflow conditions is ignored.
• Serial flash memory command error interrupt request
If the IPAEF, IPIEF fields in the FR are set, and the related interrupt enable bits in the RSER are also set, then an interrupt
is requested.
• Transaction finished interrupt request
The IP command transaction finished IRQ indicates the completion of the current IP command. It is triggered by FR[TFF] and
is masked by RSER[TFIE].
74.4.2.12 TX buffer operation
The TX buffer provides the data used for page programming. For proper operation, it is required to provide at least one entry in the
TX buffer prior to starting the execution of the page programming command. The application must ensure that the required number
of data bytes is written into the TX buffer fast enough as long as the command is executed without a TX buffer overflow or underrun.
The QuadSPI module sets the FR[TBFF] field as long as the TX buffer is not full and can accept more data. At the end of write
through TX buffer, you must clear FR[TBFF] to avoid unnecessary last TX buffer fill interrupt. However, there would always be a
pending request asserted from QuadSPI controller at the end of any DMA transfer. When external DMA finishes transfer iteration,
this request from QuadSPI is kept asserted for the next iteration loop.
NOTE
Even if the generation of DMA requests for filling the TX buffer is disabled by using RSER[TBFDE], the TX buffer
still accepts a DMA transfer because of the last asserted pending request.
Disabling of DMA transfer should be controlled by an external DMA master.
When the QuadSPI module tries to pull data out of an empty TX buffer, FR[TBUB] signals the TX buffer underrun. The TX buffer
underrun flag is also asserted when the TX buffer contains less than 32-bits of data and the QuadSPI module tries to pull out data
from it. The current IP command leading to the underrun condition is continued until the specified number of bytes is sent to the
[10]
serial flash memory device. Also, the data that is transferred is in the Fs format. This means, after the underrun flag is set under
this condition, it returns Fs until the required number of bytes are not sent. This has been done to ensure that the software does
not erase the whole sector after underrun and just reprogramming from failure point serves the purpose. When this sequence
command is complete, FR[TBFF] is asserted, indicating that the TX buffer is ready to be written again.
The TX buffer overflow is not signaled explicitly, but TBSR[TRBFL] can monitor the TX buffer fill level.
[10] Fs represents a value of FFFF_FFFF.

---

*Page 3181*

Quad Serial Peripheral Interface (QuadSPI)
For more information, see TX Buffer Status Register (TBSR) and Flag Register (FR) .
74.4.2.13 Address scheme
Earlier, serial flash memories supported only a 24-bit address space, restricting the maximum memory size of the serial flash
memory to 16 MB. The new memory specification supports two types of 32-bit addressing mode in addition to the legacy 24-bit
address mode.
Extended address mode
In this mode, the legacy 24-bit commands are converted to accept 32-bit address commands. The flash memory needs to be
configured for the 32-bit address mode. Also, while programming the LUT sequence in QuadSPI for 32-bit mode, the ADDR
commands should be programmed with 32d as the operand value . By default, QuadSPI is in 24-bit legacy address mode . Each
of the memory vendors have a different way of enabling this mode (see the memory specification from memory vendors). For
example, the command B7h sent to the Macronix flash memory enables it for the 32-bit address mode.
Extended address register
In this mode, the upper 8 bits of the 32-bit register are provided by the Extended address register in the memory, which provides
a specific register that is updated according to the address to be accessed. This effectively converts the legacy 24-bit address
command into 32-bit address commands. The memories greater in size than 16 MB consist of banks of 16 MB each. The 8 bits
occupied in the extended address register effectively enable a bank. For example, in Spansion memory, when the extended
address register is updated with a value of 1h, with the help of the 17h command, it opens Bank1 of the memory. The consequent
24-bit address commands lead to Bank1. The extended address register needs to be updated with the respective value for access
to other banks. This effectively converts the legacy 24-bit address command into 32-bit address commands.

#### 74.4.3 Module Disable mode

Module Disable mode is a block-specific mode that the QuadSPI can enter to disable serial flash memory clock and AHB
command. This mode can be entered by:
• The host software: by writing a '1' to MCR[MDIS]
Below are the condition that must be fulfilled to enter the Module Disable mode:
• SR[BUSY] = 0
• SR[AHBTRN] = 0
• RBSR[RDBFL] = 0
• SR[RXDMA] = 0
• SR[TXDMA] = 0
• None of the flags in FR are enabled as interrupts is set
The conditions mentioned above ensures the following:
• There is no SFM command currently being executed.
• All the data read into the RX buffer from the serial flash memory have been fetched by the application.
• There is no current AHB access.
• There is no active DMA request.
• There is no enabled interrupt that is pending.
Certain read or write operations have a different effect when the QuadSPI is in the Module Disable mode. In the Module Disable
mode, not all of the status and flag bits of the QuadSPI module are updated, and writing to them has no effect. Interrupt and DMA
request signals cannot be cleared while in the Module Disable mode.

---

*Page 3182*

Quad Serial Peripheral Interface (QuadSPI)
NOTE
It is illegal to issue a new SFM command starting two clock cycles prior to raising the request of entering the Module
Disable mode until the QuadSPI stays in this mode.

#### 74.4.4 Leaving Module Disable mode

In the Module Disable mode, the serial flash memory clock and AHB command to the QuadSPI module are switched off.
After the QuadSPI has left this mode and has returned to Normal mode, the execution of the first SFM command is deferred until
the clock to drive that part of the module related to the serial flash memory device is available. Depending upon the point in time
when the first SFM command is triggered, the actual execution of the command starts with a delay, respective with the re-enabling
of the flash memory clock signal.

#### 74.5 Initialization/application information

This section provides the initialization and application information of the QuadSPI module.

#### 74.5.1 Power up and reset

The serial flash memory devices connected to the QuadSPI module might require special voltage characteristics of their inputs
during power up or reset. The application must ensure this.
CAUTION
Erase or program commands should be completed before issuing a reset or power cycle to avoid corrupted flash
pages. The application shall ensure there is a backup of critical data stored at a different location to enable recovery
from corrupted flash pages.
Example: Flash reset sequence
Use the following sequence to reset flash A:
1. Make sure that the flash supports a reset for the condition CS#=high and IOF[3]=low.
2. Set MCR[SWRSTSD] and MCR[SWRSTHD] fields and then clear them.
3. Set MCR[MDIS] field.
4. Reset MCR[ISD3FA] field for flash A.
5. Clear MCR[MDIS] field.
6. Set MCR[MDIS] field.
7. Set MCR[ISD3FA] field for flash A.
8. Clear MCR[MDIS] field.

#### 74.5.2 Available status/flag information

This section provides an overview of the different flags and statuses available, and their interdependencies for different use cases.
The SR and FR are the related registers.
74.5.2.1 IP commands
See IP Configuration Register (IPCR) for additional details not explicitly covered in this paragraph.
• IP commands—normal operation
Writing to IPCR[SEQID] triggers the execution of a new IP command. Given that this is a legal command, SR[IPACC] and
SR[BUSY] are asserted simultaneously, immediately after the execution starts.
After the instruction on the serial flash memory device is complete, these field deassert and FR[TFF] is configured.
• IP commands—error situations

---

*Page 3183*

Quad Serial Peripheral Interface (QuadSPI)
See Overview_of_Error_Flags for details.
74.5.2.2 AHB commands
See the "Reading serial flash memory data into the QuadSPI module internal buffers" topic in the Flash Memory Read section
for details.
• AHB commands—normal operation
Memory-mapped read access to a serial flash memory address not contained in the AHB buffer triggers the execution of an
AHB command. Given that this is a legal command, SR[AHB_ACC] and SR[BUSY] are asserted simultaneously, immediately
after the execution starts. After the instruction on the serial flash memory device is complete, these fields are deasserted.
• IP commands—error situations
See Overview of FR error flags for details.
74.5.2.3 SFM commands
An SFM command consists of an instruction code and all other parameters (for example, size or mode bytes) needed for that
specific instruction code. Triggering a command either initiates a transaction on the external serial flash memory or results in an
error. See Table 560 for details on errors.
74.5.2.4 Overview of error flags
The following table provides an overview of the different error flags in the FR and additional error-related details.
Table 560. Overview of FR error flags
Error category Error flag in FR Command execution on serial flash Description
memory device
TFF behavior (in case of IP
commands only)
AHB error flag ABOF Flash memory transaction continues until Set when the module tries to
it finishes push data into the AHB buffer that
exceeds the size of the AHB buffer.
Occurs only because of the wrong
programming of BUFxCR[ADATSZ].
AHB error flag AIBSEF Flash memory transaction is aborted Total burst size of the AHB
transaction is greater than prefetch
data size.
AHB error flag AITEF Flash memory transaction is aborted No response is generated from
QuadSPI to AHB bus in case
of illegal transaction. Also, the
watchdog timer expires.
Miscellaneous error ILLINE Flash memory transaction aborted Illegal instruction error flag - set
flag when an illegal instruction is
encountered by the controller in any
of the sequences.
IPIEF TFF not asserted in conjunction with that
Command IP command error - caused when
command
arbitration error IP access is currently in progress
(IP_ACC is set) and during:
Table continues on the next page...

---

*Page 3184*

Quad Serial Peripheral Interface (QuadSPI)
Table 560. Overview of FR error flags (continued)
Error category Error flag in FR Command execution on serial flash Description
memory device
TFF behavior (in case of IP
commands only)
• Write attempt to IPCR register
• Write attempt to SFAR register
• Write attempt to RBCT register
Command arbitration IPAEF TFF not asserted in conjunction with that
• AHB command already running,
error command
another IP command could not
be executed
• AHB command already running,
write attempt to IPCR[SEQID]
RBOF
Buffer-related error TFF is asserted on completion • RX buffer overrun
Buffer-related error
TBUF TFF is asserted on completion • TX buffer underrun
Note that only the buffer-related errors are associated with a transaction on the external serial flash memory. All the other errors
do not trigger an actual transaction.
74.5.2.5 IP bus and AHB access command collisions
There are following flags related to this topic: FR[IPAEF] and FR[IPIEF]. See the "Reading serial flash memory data into the
QuadSPI module internal buffers" topic of the Flash Memory read section for a description of the flags.

#### 74.5.3 Flash memory device selection

Regardless of the SFM command (IP or AHB), the access mode is selected by specifying the 32-bit address value for the following
SFM command.
For IP commands, the access mode is selected with the address programmed into the SFAR register. See Serial Flash Address
Register (SFAR) for details.
For AHB commands, the access mode is determined by the memory-mapped address. See AMBA Bus Register Memory Map
for details.

#### 74.5.4 DMA usage

For a complete description of the DMA module, see the related DMA Controller chapter. This section only provides QuadSPI -
specific DMA usage details.
74.5.4.1 DMA usage in normal mode
74.5.4.1.1 Bandwidth considerations
Careful consideration of the throughput rate of the entire chain (serial flash memory -> AHB bus / IP bus -> DMA controller)
involved in the read /write data process is essential for a proper operation. Such analysis must take into account not only the data
rate provided by the serial flash memory but also the data rate of the AHB bus and the performance of the DMA controller in
reading /writing data from /to the RX /TX buffer.

---

*Page 3185*

Quad Serial Peripheral Interface (QuadSPI)
Two figures must match for a proper operation, which means that the data rate provided by the serial flash memory device must
not exceed the average RX buffer readout data rate. Otherwise, the longer this state persists, it results into an RX buffer overflow.
Similarly, the data consumed by the serial flash memory device must not exceed the average TX buffer fill rate. If this persists, it
leads to an underrun.
AHB bus side (data read)
The total number of bus cycles for each DMA minor loop completion is added from the following components:
The following table provides certain examples for typical use cases:
Case 1: DMA needs to read 4 bytes from SRAM and provide to QuadSPI. It costs total four bus clock cycles. Then, DMA
handshake adds additional six bus clock cycles, resulting in a total of [6 + 4 * (32/4) = 38] bus clock cycles.
Table 561. Access duration examples for bus clock side
TBCT[WMRK] Number of bytes per DMA Number of bus clock cycles Time duration of DMA minor
loop for DMA minor loop loop for 60 MHz bus clock
frequency
3 16 6+(16/4)*4 = 22 ~366ns
7 32 6+(32/4)*4 = 38 ~633ns
11 48 6+(48/4)*4 = 54 ~900ns
15 64 6+(64/4)*4 = 70 ~1166ns
Case 2: DMA needs to read 32 bytes from SRAM and provide to QuadSPI. DMA handshake takes an additional six bus cycles,
with 32 bytes DMA read from SRAM costs (8 + 3) core clock cycles. DMA writes 32 bytes to QuadSPI, takes 2 * (32/4) = 16 bus
cycles with one additional CPU access to QuadSPI, costing two bus clock cycles. This results in a total 6 + (8+3)/2 + 2 * (32/4)
+2 = 30 bus clock cycles.
Table 562. Access duration examples for bus clock side
TBCT[WMRK] Number of bytes per DMA Number of bus clock cycles Time duration of DMA minor
loop > for DMA minor loop loop for 80 MHz bus clock
frequency
3 16 6 + (4+3) /2 + (16/4)*2 + 2 = ~333ns
20
7 32 6 + (8+3) /2 + (32/4)*2 + 2 = ~500ns
30
11 48 6 + (4+3)/2*3 + (48/4)*2 + 2 = ~733ns
44
15 64 6 + (8+3) + (64/4)*2 + 2 = 51 ~810ns
NOTE
This table figure represents an ideal scenario; actual performance depends on how the chip integrates DMA and
QuadSPI modules.
Serial flash memory device side (data read)
The number of serial flash memory cycles can be determined in the following way:
• Number of serial flash memory clock cycles is required to read 4 bytes, corresponding to one RX buffer entry (setup of
command and address not considered): , eight cycles for quad mode (SDR) instructions in individual flash memory mode,
and so on.
• Overhead because of clock domain crossing: one cycle

---

*Page 3186*

Quad Serial Peripheral Interface (QuadSPI)
The following table lists the number of clock cycles required to read the data from the serial flash memory corresponding to the
different settings of RBCT[WMRK]:
Table 563. Access duration examples for serial flash memory side
RBCT[WMRK] Num bytes per Num SCKFx for 80 MHz SCKFx Time duration of flash memory
1
setting DMA loop data readout for 80 MHz SCKFx
(~12.5ns period)
2
IFM quad IFM quad DDR IFM quad IFM quad DDR
0 4 8 4 ~100ns ~50ns
1 8 16 8 ~200ns ~100ns
3 16 32 16 ~400ns ~200ns
7 32 64 32 ~800ns ~400ns
11 48 96 48 ~1200ns ~600ns
1. DMA loop refers to one minor loop completion that is equivalent to one major loop iteration.
2. Individual flash memory mode
NOTE
The table figure represents an ideal scenario; actual performance depends on how the chip integrates with DMA
and QuadSPI modules.
A complementary example is when the watermark is set to be too high. In such a case, the time taken by the DMA to read out the
RX buffer entries should be lesser than the time taken by the controller to push in the remaining entries in the buffer.
IPS bus side (data write)
The total number of bus cycles for each DMA minor loop completion are added from the following components:
• Overhead for each minor loop, given by DMA controller: assume 10 cycles
• Overhead because to clock domain crossing: assume two cycles
• Number of bus clock cycles required for 16 bytes (128-bit write size): assume four cycles (read/write sequence of DMA
controller)
Note that the size of the minor loop is determined by the size of TBCT[WMRK]; therefore, the overhead specified above distributes
among (TBCT[WMRK]+1) write accesses of 32-bit each.
The following table provides some examples for typical use cases:
Table 564. Access duration examples for bus clock side
TBCT[WMRK] Number of bytes per DMA Number of bus clock cycles for Time duration of DMA minor
1
loop DMA minor loop loop for 80 MHz bus
clock frequency
3 16 12+4 = 16 ~200ns
7 32 12+8 = 20 ~250ns
11 48 12+12 = 24 ~300ns
15 64 12+16 = 28 ~350ns
19 80 12+20 = 32 ~400ns
1. DMA loop refers to one minor loop completion that is equivalent to one.

---

*Page 3187*

Quad Serial Peripheral Interface (QuadSPI)
NOTE
The table figure represents an ideal scenario; actual performance depends on how the chip integrates with DMA
and QuadSPI modules.
Serial flash memory device side (data write)
The number of serial flash memory cycles can be determined in the following way:
• Number of serial flash memory clock cycles required to write 16 bytes, corresponding to four TX buffer entry (setup of
command and address not considered): 32 cycles for quad SDR writes in individual flash memory mode.
• Overhead due to clock domain crossing: one cycle
The following table lists the number of clock cycles required to read the data from the serial flash memory corresponding to the
different settings of TBCT[WMRK]:
Table 565. Access duration examples for serial flash memory side
3
TBCT[WMRK Num bytes Num SCKFx Time duration for consuming Time for FIFO to get empty
] setting per DMA data at flash memory
1
loop interface 100 MHz SCKFx (10
2
ns period)
4 5
IFM quad single IO IFM quad PFM single IFM quad PFM single
SDR mode IO SDR IO SDR
3 16 32 64 320ns 640ns 2240ns 4480ns
7 32 64 128 640ns 1280ns 1920ns 3840ns
15 64 128 256 1280ns 2560ns 1280ns 2560ns
23 96 192 384 1920ns 3840ns 640ns 1280ns
1. DMA loop refers to one minor loop completion that is equivalent to one major loop iteration.
2. Not all flash memory devices support writes at 100 MHz. See the flash memory data sheet for the actual page program
frequency supported.
3. The assumption for these timings is that the TX Fifo is full when the transaction is initiated
4. Individual flash memory mode
5. Parallel flash memory mode
NOTE
The tables mentioned above are only examples which must be correlated with the DMA in the system.
Considering the examples provided in the two tables above for TX FIFO, it is evident that depending on the relationship between
the bus clock and serial flash memory clock frequencies, there are settings possible where the serial flash memory consumes
data faster than the IPS bus can write data in TX buffer. In these cases, a TX buffer underrun situation occurs. To avoid TX buffer
underrun, the data transaction size should be large enough.

#### 74.5.5 Flash memory devices address mapping

QuadSPI is configured in Single mode for the supported flash memory port A
The sizes of the flash memory devices are mapped with the system memory space based on the configurations of the
following registers:
• SFA1AD
• SFA2AD
• SFB1AD
• SFB2AD

---

*Page 3188*

Quad Serial Peripheral Interface (QuadSPI)
The total memory region for the flash memory devices is mapped between QuadSPI_AMBA_BASE and TOP_ADDR_MEMB2
such that the corresponding CS is asserted based on SFA1AD , SFA2AD and SFB1AD register configurations.
74.5.5.1 Single mode
For single mode configuration, you must write the same value to SFB1AD and SFB2AD registers that you write to the
SFA2AD register.
For dual-die flash memories, the values you write to SFB1AD and SFB2AD registers corresponds to the mapped top addresses
of each die.
For single-die flash memories, you must write the same value to SFA2AD register that you write to the SFA1AD register.
Following is a programming example for single mode single-die flash memory:
• QuadSPI_AMBA_BASE - 1000_0000h
• SFA1AD[TPADA1] - 2000_0000h
• SFA2AD[TPADA2] - 2000_0000h
• SFB1AD[TPADB1] - 2000_0000h
• SFB2AD[TPADB2] - 2000_0000h
The following figure illustrates the memory mapping for single mode QuadSPI configuration.
Single-die Dual-die
AMBA_BASE
AMBA_BASE
A1
A1
SFA1AD
SFA1AD
SFA2AD
A2
SFB1AD
SFB2AD
SFA2AD
SFB1AD
SFB2AD
Figure 434. Memory map for Single mode

#### 74.6 Byte ordering – endianness

The following topics show the byte ordering in 64-bit LE configuration for AHB buffer and 32-bit LE for TX/RX buffer.

#### 74.6.1 Programming flash memory data

CPU writes instructions to the TBDR register, such as:
• Write TBDR: 4_03_02_01h
• Write TBDR: 8_07_06_05h
The following table shows the content against each TX buffer entry.
Table 566. Example of QuadSPI TX buffer
TX buffer entry Content
0 4_03_02_01h
1 8_07_06_05h
Programming the TX buffer into the external serial flash memory device results in the following byte order to be sent to the serial
flash memory:

---

*Page 3189*

Quad Serial Peripheral Interface (QuadSPI)
• 01...02...03...04...05...06...07...08

#### 74.6.2 Reading flash memory data into the RX buffer

Reading the content from the same address provides the following sequence of bytes, identical to the write case:
• 01...02...03...04...05...06...07...08
The following table shows the content against each TX buffer entry.
Table 567. Resulting RX buffer content
RX buffer entry Content
0 4_03_02_01h
1 8_07_06_05h
74.6.2.1 Readout of the RX buffer through RBDRn
The RX buffer content appears at CPU read access through the peripheral bus interface in the following order:
• Read RBDR0: 4_03_02_01h
• Read RBDR1: 8_07_06_05h
74.6.2.2 Readout of the RX buffer through ARDBn
The RX buffer content appears at read access on the AMBA AHB interface at the QuadSPI module boundary:
• 32-bit access: Read ARDB0: 4_03_02_01h
• 32-bit access: Read ARDB1: 8_07_06_05h
• 64-bit access: Read ARDB0: 8_07_06_05_04_03_02_01h

#### 74.6.3 Reading flash memory data into the AHB buffer

Reading the content from the same address as it was written to provides the following sequence of bytes, identical to the
write case:
• 01...02...03...04...05...06...07...08
The following table shows the content against each TX buffer entry.
Table 568. Resulting AHB buffer content
AHB buffer entry Content
0 8_07_06_05_04_03_02_01h
74.6.3.1 Readout of the AHB buffer through memory-mapped read
The AHB buffer content appears at read access on the AMBA AHB interface at the QuadSPI module boundary:
• 32-bit read access: 4_03_02_01h
• 32-bit read access: 8_07_06_05h
• 64-bit read access: 8_07_06_05_04_03_02_01h

---

*Page 3190*

Quad Serial Peripheral Interface (QuadSPI)

#### 74.7 Driving flash memory control signals in single and dual modes

In single and dual modes, the serial flash memory devices that can connect to the QuadSPI module expect additional control
signals on the inputs, which are connected to IOFA[3], IOFA[2] in the quad mode. For easy interfacing, the outputs IOFA[3:2] for
flash memory A are driven to the logic state given by the configuration fields MCR[ISD3FA], MCR[ISD2FA].
These outputs are driven all the time to the logic level programmed in the MCR except the time when quad commands of the serial
flash memory are executed. See the specifications of the related serial flash memory device for details about the inactive level.

#### 74.8 Serial flash memory devices

Several different vendors make flash memory devices with a QuadSPI interface. At present, there is no set standard for the
QuadSPI instruction set. The most common commands currently have the same instruction code for all vendors; however, some
commands are unique to specific vendors. Some of the example sequences are provided in the following sections.

#### 74.8.1 Example sequences

This section provides the example sequences of the QuadSPI module.
Table 569. Exit 4 x I/O read enhance performance mode (XIP) (Macronix) and read status
Instruction Pad Operand Description
CMD 0h EBh 4xIO read command
ADDR 2h 18h 24-bit address to be sent on
four pads
MODE 2h 0h 2 mode cycles (exit XIP)
DUMMY 2h 4h 4 dummy cycles
READ 2h 8h Read 64 bits
CMD 0h 5h Read Status register
READ 0h 1h Status register data
STOP 0h 0h Stop, instruction over
74.8.1.1 Fast read sequence (Macronix/Numonyx/Spansion/Winbond)
The following table shows the fast read sequence for Macronix/Numonyx/Spansion/Winbond flash memories.
Table 570. Fast read sequence
Instruction Pad Operand Description
CMD 0h Bh Fast read command = 0Bh
ADDR 0h 18h 24 address bits to be sent on one pad
Eight dummy cycles
DUMMY 0h 8h
READ 0h 4h Read 32 bits on one pad
JMP_ON_CS 0h 0h Jump to instruction 0 (CMD)
NOTE
If DLL is disabled then JMP_ON_CS or STOP instruction can be used else only STOP instruction can be used.

---

*Page 3191*

Quad Serial Peripheral Interface (QuadSPI)
74.8.1.2 Fast read quad output (Winbond)
The following table shows the fast read quad output sequence for Winbond memories
Table 571. Fast read quad output sequence
Instruction Pad Operand Description
CMD 0h 6Bh Fast read quad output command = 6Bh
ADDR 0h 18h 24 address bits to be sent on one pad
Eight dummy cycles
DUMMY 2h 8h
READ 2h 4h Read 32 bits on four pads
JMP_ON_CS 0h 0h Jump to instruction 0 (CMD)
NOTE
If DLL is disabled then JMP_ON_CS or STOP instruction can be used else only STOP instruction can be used.
74.8.1.3 4 x I/O read enhance performance mode (XIP) (Macronix)
The following table shows the 4 x I/O read enhance performance mode for Macronix flash memories. The enhanced performance
mode is also known as XIP mode.
Table 572. Fast read quad output sequence
Instruction Pad Operand Description
0h
CMD EBh 4xI/O read command = EBh
2h
ADDR 18h 24 address bits to be sent on four pads
MODE 2h A5h Two mode cycles
DUMMY 2h 4h Four dummy cycles
READ 2h 4h Read 32 bits on four pads
JMP_ON_CS 0h 1h Jump to instruction 1 (ADDR)
When in XIP mode, the software must ensure that all the flash memories connected to the controller are in this mode. As a part
of initializing the controller, all the flash memories might be enabled with XIP by carrying out dummy reads.
74.8.1.4 Dual command page program (Numonyx)
The following table shows the dual command page program sequence for Numonyx flash memories.
Table 573. Dual command page program sequence
Instruction Pad Operand Description
CMD 1h 2h Dual command page program = 02h on 2 pads
Table continues on the next page...

---

*Page 3192*

Quad Serial Peripheral Interface (QuadSPI)
Table 573. Dual command page program sequence (continued)
Instruction Pad Operand Description
ADDR 1h 18h 24 address bits to be sent on two pads
WRITE 1h 20h Write 32 bytes on two pads
STOP 0h 0h Stop, instruction over
74.8.1.5 Sector erase (Macronix/Numonyx/Spansion)
The following table shows the Sector erase sequence for the Macronix/Numonyx/Spansion flash memories.
Table 574. Sector erase sequence
Instruction Pad Operand Description
CMD 0h 20h Sector erase command = 20h
ADDR 0h 18h 24 address bits to be sent on one pad
STOP 0h 0h Stop, instruction over
74.8.1.6 Read status register (Macronix/Numonyx/Spansion/Winbond)
The following table shows the read status register sequence for Macronix/Numonyx/Spansion/Winbond flash memories.
Table 575. Read status register sequence
Description
Instruction Pad Operand
CMD 0h 0h5 Read status register command = 05h
READ 0h 0h1 Read status register data
STOP 0h 0h Stop, instruction over

#### 74.9 Sampling of serial flash memory input data

#### 74.9.1 Basic description

QuadSPI is used to read data from the serial flash memory device. Depending on the actual implementation, there is a delay
between the internal clocking in the QuadSPI module and the external serial flash memory device. See the following figure for an
overview of this scheme.

---

*Page 3193*

Quad Serial Peripheral Interface (QuadSPI)
QUADSPI
Serial flash memory
SCK - Serial Flash Memory Clock
Clock Gen Clock
1 2
3
SI_IO[0:3] - Serial Flash Memory Data
Data
Sampling
Out
5 4
Figure 435. Serial flash memory sampling clock overview
The rising edge of the internal reference clock is taken as timing reference for the data output of the serial flash memory. After a
time of t total_delay the data arrives at the internal sampling stage of the QuadSPI module. Considering the figure provided here, the
following parts of the delay chain contribute to t total_delay :
• Output delay of the serial flash memory clock output of the device containing the QuadSPI module
• Wire delay of application/PCB from the device containing the QuadSPI module to the external serial flash memory device
• Clock to data out delay of the external serial flash memory device, including input and output delays
• Wire delay of application/PCB from the external serial flash memory device to the device containing the QuadSPI module
• Device delay corresponding to the input data
NOTE
The t total_delay is specific to the characteristics of the actual implementation. Also, the serial flash memory device
clock (SCK) is inverted with respect to the QuadSPI internal reference clock.

#### 74.9.2 DQS sampling method

74.9.2.1 Basic description
In the DQS mode, the data strobe signal (DQS/RWDS) is used to sample the read data. Here, both DQS and the data sent by the
flash memory move in the same direction; therefore, it is relatively easier to achieve at higher frequencies.
When using DQS for SDR reads, QuadSPI internally samples the incoming data on the rising edge of the strobe signal.
The next figure shows the sampling read data in the SDR mode using the DQS.

---

*Page 3194*

Quad Serial Peripheral Interface (QuadSPI)
internal reference for serial flash memory data sampling
Internal Ref
clock
SCK
Data Strobe Signal
Data
Data sampled on only rising edge of Data Strobe Signal
Figure 436. Data strobe functionality in SDR mode for read operation.
NOTE
Consider "Data Strobe Signal" as "Data Strobe Signal driven by memory" and "Data" as "Data from memory".
NOTE
Refer to the Datasheet for specific timing waveforms of QuadSPI
NOTE
For Specific details - Refer to the Data Sheet specification of QuadSPI module.
74.9.2.2 Dummy Pad loopback
The internal clock is loop-backed from the dummy internal pad to compensate data pad delays. This can be enabled by configuring
the value of MCR[DQS_FA_SEL] as "01" for flash memory A. This mode can be used with the following configuration:
• High/low frequency delay chain manual programming in bypass mode using DLLCRA[SLV_DLY_COARSE]
and DLLCR[FREQEN].
NOTE
Refer to Auto-DataLearning (4x Sampling method) section with DLL for further details
NOTE
This mode may not be available on the chip. See the "Supported read modes" section in the chip-specific QuadSPI
information for the read modes that this chip supports.

#### 74.10 Delay chain usage

Slave delay chain programming sequence—
Following is the programming sequence for DLL bypass mode.
1. Program DLLCRA[SLV_EN]=1, DLLCRA[SLV_DLL_BYPASS]=1, and DLLCRA[SLAVE_AUTO_UPDT]=0.

---

*Page 3195*

Quad Serial Peripheral Interface (QuadSPI)
2. Program the following fields to provide the desired DQS delay for sampling: DLLCRA[SLV_FINE_OFFSET],
DLLCRA[SLV_DLY_COARSE], and DLLCR[FREQEN]. See the chip-specific QuadSPI information for the supported
programming settings.
3. Program DLLCRA[SLV_UPD]=1 to load these values in the slave delay chain.
4. Check the slave delay chain update status by polling DLLSR[SLVA_LOCK]=1 and clear DLLCRA[SLV_UPD] after
confirming the update state.

#### 74.11 Memory map and register definition

This section provides the memory map and register definitions for the QuadSPI module.

#### 74.11.1 Register write access

Following are the write access restriction terms that apply to all the registers:
• Register write access restriction
For each register field, the write access conditions are specified in the detailed register description.
The following table provides a description of the write access conditions. If, for a specific register bit or field, none of the given
write access conditions is fulfilled, any write attempt to this register bit or field is ignored without any notification. The values
of the bits or fields are not changed.
The condition term [A or B] indicates that the register or field can be written to if at least one of the conditions is fulfilled.
Table 576. Register write access restrictions
Condition Description
Anytime No write access restriction
Disabled mode Write access only if MCR[MDIS] = 1
Normal mode Write access only if the module is in the normal mode
• Register write access requirements
You can access all registers using 8-bit, 16-bit, and 32-bit wide operations. For some of the registers, at least a 16-bit or
32-bit wide write access is required to ensure correct operation. This write access requirement is stated in the detailed register
description for each affected register.

#### 74.11.2 QUADSPI register descriptions

This section provides the memory map and register definitions for the QuadSPI module.
Access to the following addresses does not result in a transfer error:
• 50h
• 64h
• 104h
• 120h
• 138h
• 168h
• 188h
• 18Ch

---

*Page 3196*

Quad Serial Peripheral Interface (QuadSPI)
74.11.2.1 QuadSPI memory map
QuadSPI base address: 404C_C000h
Offset Register Access Reset value
Width
(In bits)
0h Module Configuration Register (MCR) 32 RW 000F_404Ch
8h IP Configuration Register (IPCR) 32 RW 0000_0000h
Ch Flash Memory Configuration Register (FLSHCR) 32 RW 0000_0303h
10h Buffer 0 Configuration Register (BUF0CR) 32 RW 0000_0003h
14h Buffer 1 Configuration Register (BUF1CR) 32 RW 0000_0002h
18h Buffer 2 Configuration Register (BUF2CR) 32 RW 0000_0001h
1Ch Buffer 3 Configuration Register (BUF3CR) 32 RW 8000_0000h
20h Buffer Generic Configuration Register (BFGENCR) 32 RW 0000_0000h
24h SOC Configuration Register (SOCCR) 32 RW 0000_0000h
30h Buffer 0 Top Index Register (BUF0IND) 32 RW 0000_0000h
34h Buffer 1 Top Index Register (BUF1IND) 32 RW 0000_0000h
38h Buffer 2 Top Index Register (BUF2IND) 32 RW 0000_0000h
60h DLL Flash Memory A Configuration Register (DLLCRA) 32 RW 0120_0000h
100h Serial Flash Memory Address Register (SFAR) 32 RW 0000_0000h
108h Sampling Register (SMPR) 32 RW FF00_0000h
10Ch RX Buffer Status Register (RBSR) 32 R 0000_0000h
110h RX Buffer Control Register (RBCT) 32 RW 0000_0000h
134h Data Learning Status Flash Memory A Register (DLSR_FA) 32 R 0000_0000h
150h TX Buffer Status Register (TBSR) 32 R 0000_0000h
154h TX Buffer Data Register (TBDR) 32 RW 0000_0000h
158h TX Buffer Control Register (TBCT) 32 RW 0000_0000h
15Ch Status Register (SR) 32 R 0200_3800h
160h Flag Register (FR) 32 RW 0800_0000h
164h Interrupt and DMA Request Select and Enable Register (RSER) 32 RW 0000_0000h
16Ch Sequence Pointer Clear Register (SPTRCLR) 32 RW 0000_0000h
180h Serial Flash Memory A1 Top Address Register (SFA1AD) 32 RW 7000_0000h
184h Serial Flash Memory A2 Top Address Register (SFA2AD) 32 RW 7000_0000h
188h Serial Flash Memory B1 Top Address Register (SFB1AD) 32 RW 7000_0000h
18Ch Serial Flash Memory B2 Top Address Register (SFB2AD) 32 RW 7000_0000h
200h - 27Ch RX Buffer Data Register (RBDR0 - RBDR31) 32 R 0000_0000h
Table continues on the next page...

---

*Page 3197*

Quad Serial Peripheral Interface (QuadSPI)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
300h LUT Key Register (LUTKEY) 32 RW 5AF0_5AF0h
304h LUT Lock Configuration Register (LCKCR) 32 RW 0000_0002h
310h LUT Register (LUT0) 32 RW 0818_0403h
314h LUT Register (LUT1) 32 RW 2400_1C08h
318h - 35Ch LUT Register (LUT2 - LUT19) 32 RW 0000_0000h
74.11.2.2 Module Configuration Register (MCR)
Offset
Register Offset
MCR 0h
Function
This register holds configuration data associated with the QuadSPI operation.
Special write-access is permitted in different modes:
• DQS_FA_SEL : Disabled mode
• ISD3FA, ISD2FA: Disabled mode
• All other fields: Anytime
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv ISD3F ISD2F
Reserved DQS_FA_SEL
ed ed ed ed ed ed ed ed A A
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv SWRS SWRS
MDIS Reserved
CLR_ CLR_
ed ed ed ed ed ed ed ed THD TSD
W
TXF RXF
Reset 0 1 0 0 0 0 0 0 0 1 0 0 1 1 0 0

---

*Page 3198*

Quad Serial Peripheral Interface (QuadSPI)
Fields
Field Function
Reserved
31
—
Reserved
30
—
Reserved
29-28
—
Reserved
27
—
Reserved
26
—
25-24 DQS clock for sampling read data at flash memory A
DQS_FA_SEL Selects DQS clock for sampling read data at flash memory A QuadSPI port
00b - Reserved
01b - Pad loopback
10b - Reserved
11b - Reserved
Reserved
23
—
Reserved
22
—
Reserved
21-20
—
Reserved
19
—
Reserved
18
—
17 Idle signal drive IOFA[3] flash memory A
ISD3FA Determines the logic level that the IOFA[3] output of the QuadSPI module is driven to in the inactive state.
See Driving flash memory control signals in single and dual modes for details.
0b - IOFA[3] is driven to logic L
Table continues on the next page...

---

*Page 3199*

Quad Serial Peripheral Interface (QuadSPI)
Table continued from the previous page...
Field Function
1b - IOFA[3] is driven to logic H
16 Idle signal drive IOFA[2] flash memory A
ISD2FA Determines the logic level that the IOFA[2] output of the QuadSPI module is driven to in the inactive state.
See Driving flash memory control signals in single and dual modes for details.
0b - IOFA[2] is driven to logic L.
1b - IOFA[2] is driven to logic H.
Reserved
15
—
14 Module disable
MDIS Allows the clock to the non-memory mapped logic in the QuadSPI to be stopped.
0b - Enable QuadSPI clocks
1b - Allow external logic to disable QuadSPI clocks
Reserved
13
—
Reserved
12
—
11 Clear TX FIFO/buffer
CLR_TXF This is a self-clearing field that invalidates the TX buffer content.
NOTE
Software must wait for at least five system cycles and three flash cycles after writing '1' to
this field.
0b - No action
1b - Read and write pointers of the TX buffer are reset to 0 and TBSR[TRCTR] is reset to 0.
10 Clear RX FIFO
CLR_RXF This is a self-clearing field that invalidates the RX buffer content.
0b - No action
1b - Read and write pointers of the RX buffer are reset to 0 and RBSR[RDBFL] is reset to 0.
Reserved
9
—
Reserved
8
—
Table continues on the next page...

---

*Page 3200*

Quad Serial Peripheral Interface (QuadSPI)
Table continued from the previous page...
Field Function
Reserved
7
—
Reserved
6
—
Reserved
5
—
Reserved
4
—
Reserved
3-2
—
1 Software reset for AHB domain
SWRSTHD 0b - De-assert Software reset
1b - AHB domain flops are reset. This field does not reset configuration registers. It is advisable to reset both
the serial flash memory domain and AHB domain at the same time. Resetting only one domain might lead
to side effects.
NOTE
The software resets need the clock to be running to propagate to the design. The value of
MCR[MDIS] should be 0 when the software reset bits are asserted. Also, before they can
be deasserted again (by setting MCR[SWRSTHD] to 0), it is recommended to set the value
of MCR[MDIS] to 1. After the software resets have been deasserted, the normal operation
can be started by setting MCR[MDIS] to 0.
NOTE
Software must wait for at least three system cycles and three flash cycles after changing the
value of this field.
0 Software reset for serial flash memory domain
SWRSTSD 0b - De-assert Software reset
1b - Serial flash memory domain flops are reset. This field does not reset configuration registers. It is
advisable to reset both the serial flash memory domain and AHB domain at the same time. Resetting only
one domain might lead to side effects.
Table continues on the next page...

---

*Page 3201*

Quad Serial Peripheral Interface (QuadSPI)
Table continued from the previous page...
Field Function
NOTE
The software resets need the clock to be running to propagate to the design. The value
of MCR[MDIS] should therefore be 0 when the software reset bits are asserted. Also,
before they can be deasserted again (by specifying 0 as the value for MCR[SWRSTSD]),
it is recommended to specify 1 as the value for MCR[MDIS]. After the software resets
are deasserted, the normal operation can be started by specifying 0 as the value
for MCR[MDIS].
NOTE
Software must wait for at least three system cycles and three flash cycles after changing the
value of this field.
74.11.2.3 IP Configuration Register (IPCR)
Offset
Register Offset
IPCR 8h
Function
This register provides all the configuration required for an IP-initiated command, which can be triggered by writing in the SEQID
field of this register. If the SEQID field is written successfully, a new command to the external serial flash memory is initiated per
the sequence pointed to by this field. See Normal mode for details on command triggering and command execution.
Special write-access is permitted if:
• SR [ IP_ACC ]=0
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0
Reserv
SEQID
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
IDATSZ
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 3202*

Quad Serial Peripheral Interface (QuadSPI)
Fields
Field Function
Reserved
31-28
—
27-24 Points to a sequence in the LUT
SEQID This field contains the sequence index of the LUT. See LUT for details.
Each sequence index can accommodate up to 10 instructions (2 instructions per register).
A write to this field triggers a transaction on the serial flash memory interface.
Reserved
23
—
Reserved
22
—
Reserved
21-17
—
Reserved
16
—
15-0 IP data transfer size
IDATSZ This field defines the data transfer size, in bytes, of the IP command.
74.11.2.4 Flash Memory Configuration Register (FLSHCR)
Offset
Register Offset
FLSHCR Ch
Function
This register contains the timings that are specific to the flash memory device. The QuadSPI controller must meet these timings
for the device to function correctly.
Special write-access is permitted if:
• SR [ AHB_ACC ] = 0
• SR [ IP_ACC ] = 0

---

*Page 3203*

Quad Serial Peripheral Interface (QuadSPI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
TCSH TCSS
W
Reset 0 0 0 0 0 0 1 1 0 0 0 0 0 0 1 1
Fields
Field Function
Reserved
31-18
—
Reserved
17-16
—
Reserved
15-12
—
11-8 Serial flash memory CS hold time
TCSH This hold time is in terms of serial flash memory clock cycles, and it must be greater than or equal to five
flash memory clock cycles . Refer the chip datasheet for the exact value.
Reserved
7-4
—
3-0 Serial flash memory CS setup time
TCSS This setup time is in terms of serial flash memory clock cycles, and it must be greater than or equal to two
flash memory clock cycles. Refer the chip Datasheet for the exact value.
74.11.2.5 Buffer 0 Configuration Register (BUF0CR)
Offset
Register Offset
BUF0CR 10h

---

*Page 3204*

Quad Serial Peripheral Interface (QuadSPI)
Function
This register provides the configuration for any read access routed to buffer0, which happens when the master ID of the incoming
AHB request matches BUF0CR[MSTRID]. Any buffer "miss" leads to a serial flash memory transaction being triggered per the
sequence pointed to by BFGENCR[SEQID].
Special write-access is permitted if:
• SR [ AHB_ACC ] = 0
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
Reserv
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
ADATSZ MSTRID
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1
Fields
Field Function
Reserved
31
—
Reserved
30-14
—
13-8 AHB data transfer size
ADATSZ Defines the read data transfer size in 8 bytes of an AHB triggered read access to serial flash memory. For
example, a value of 0x2 sets transfer size to 16 bytes. When ADATSZ = 0, the data size mentioned in the
sequence pointed to by the SEQID field overrides this value. The software should ensure that this transfer
size is not greater than the size of the buffer.
Reserved
7-4
—
3-0 Master ID
MSTRID ID of the AHB master associated with BUFFER 0
Any AHB read access with this master ID is routed to this buffer. You must ensure that the master IDs
associated with all buffers are different.
NOTE
See the chip-specific QuadSPI information for details about master IDs and their
corresponding components.

---

*Page 3205*

Quad Serial Peripheral Interface (QuadSPI)
74.11.2.6 Buffer 1 Configuration Register (BUF1CR)
Offset
Register Offset
BUF1CR 14h
Function
This register provides the configuration for any access routed to buffer 1, which happens when the master ID of the incoming AHB
request matches the MSTRID field of this register. Any buffer "miss" leads to the buffer being flushed and a serial flash memory
transaction being triggered per the sequence pointed to by BFGENCR[SEQID].
Special write-access is permitted if:
• SR [ AHB_ACC ] = 0
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
ADATSZ MSTRID
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0
Fields
Field Function
Reserved
31-14
—
13-8 AHB data transfer size
ADATSZ This field defines the read data transfer size in 8 bytes of an AHB triggered read access to serial flash
memory. For example, a value of 0x2 sets the transfer size to 16 bytes. When ADATSZ = 0, the data size
mentioned in the sequence pointed to by the SEQID field overrides this value. Software should ensure that
this transfer size is not greater than the size of this buffer.
Reserved
7-4
—
3-0 Master ID
MSTRID ID of the AHB master associated with BUFFER 1
Table continues on the next page...

---

*Page 3206*

Quad Serial Peripheral Interface (QuadSPI)
Table continued from the previous page...
Field Function
Any AHB read access with this master ID is routed to this buffer. You must ensure that the master IDs
associated with all buffers are different.
NOTE
See the chip-specific QuadSPI information for details about master IDs and their
corresponding components.
74.11.2.7 Buffer 2 Configuration Register (BUF2CR)
Offset
Register Offset
BUF2CR 18h
Function
This register provides the configuration for any access routed to buffer 2, which happens when the master ID of the incoming AHB
request matches the MSTRID field of this register. Any buffer "miss" leads to the buffer being flushed and a serial flash memory
transaction being triggered per the sequence pointed to by BFGENCR[SEQID].
Special write-access is permitted if:
• SR [ AHB_ACC ] = 0
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
ADATSZ MSTRID
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1
Fields
Field Function
Reserved
31-14
—
Table continues on the next page...

---

*Page 3207*

Quad Serial Peripheral Interface (QuadSPI)
Table continued from the previous page...
Field Function
13-8 AHB data transfer size
ADATSZ This field defines the read data transfer size in 8 bytes of an AHB triggered read access to the serial flash
memory. For example, a value of 0x2 sets transfer size to 16 bytes. When ADATSZ = 0, the data size
mentioned in the sequence pointed to by the SEQID field overrides this value. The software should ensure
that this transfer size is not greater than the size of this buffer.
Reserved
7-4
—
3-0 Master ID
MSTRID The ID of the AHB master associated with BUFFER2. Any AHB read access with this master ID is routed
to this buffer.
It must be ensured that the master IDs associated with all buffers are different.
NOTE
See the chip-specific QuadSPI information for details about master IDs and their
corresponding components.
74.11.2.8 Buffer 3 Configuration Register (BUF3CR)
Offset
Register Offset
BUF3CR 1Ch
Function
This register provides the configuration for any access to buffer 3.
An access is routed to buffer 3 when the master ID of the incoming AHB request matches the MSTRID field of BUF3CR. Any
buffer "miss" leads to the buffer being flushed and a serial flash memory transaction being triggered per the sequence pointed to
by BFGENCR[SEQID].
In case the value of the ALLMST field is not 1, any such transaction (where master ID does not match any of the MSTRID fields)
is returned with an ERROR response.
Special write-access is permitted if:
• SR [ AHB_ACC ] = 0

---

*Page 3208*

Quad Serial Peripheral Interface (QuadSPI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
ALLMS
ADATSZ
T
W
Reset 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
ADATSZ MSTRID
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 All master enable
ALLMST When set, buffer3 acts as an all-master buffer. Any AHB access with a master ID not matching with the
master ID of buffer0, buffer1, or buffer2 is routed to buffer3. When set, the MSTRID field of this register
is ignored.
Reserved
30-18
—
17-8 AHB data transfer size
ADATSZ Defines the read data transfer size in 8 bytes of an AHB triggered read access to serial flash memory. When
ADATSZ = 0, the data size mentioned in the sequence pointed to by the SEQID field overrides this value.
The software should ensure that this transfer size is not greater than the size of this buffer. If extended
prefetch till 4K bytes has to be done then it can be programmed greater than the buffer size but should be
always less than 512 just make sure that in this case ALLMST bit is also 1 and complete buffer is mapped
to BUF3.
Reserved
7-4
—
3-0 Master ID
MSTRID ID of the AHB master associated with BUFFER 3. Any AHB read access with this master ID is routed to this
buffer. You must ensure that the master IDs associated with all buffers are different.
NOTE
See the chip-specific QuadSPI information for details about master IDs and their
corresponding components.

---

*Page 3209*

Quad Serial Peripheral Interface (QuadSPI)
74.11.2.9 Buffer Generic Configuration Register (BFGENCR)
Offset
Register Offset
BFGENCR 20h
Function
This register provides generic configuration to any of the buffer accesses. Any buffer "miss" leads to the buffer being flushed and
a serial flash memory transaction being triggered per the sequence pointed to by the SEQID field.
Special write-access is permitted if:
• SR [ AHB_ACC ] = 0
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
Reserv
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
SEQID
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-28
—
Reserved
27-18
—
Reserved
17
—
Reserved
16
—
15-12 Points to a sequence in the LUT.
SEQID This field contains the sequence index of the LUT... See LUT .
Table continues on the next page...

---

*Page 3210*

Quad Serial Peripheral Interface (QuadSPI)
Table continued from the previous page...
Field Function
NOTE
If the sequence pointer differs in the new and the previous sequences, you should reset it.
See sequence pointer clear register for more information.
Reserved
11-0
—
74.11.2.10 SOC Configuration Register (SOCCR)
Offset
Register Offset
SOCCR 24h
Function
This register is programmed at the chip level for QuadSPI configuration. For details, see chip-specific QuadSPI information.
Special write-access is permitted if:
• SR [ AHB_ACC ] = 0
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SOCCFG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SOCCFG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 SOC configuration
SOCCFG This field configuration is specific to chip. For details, see chip-specific QuadSPI information.

---

*Page 3211*

Quad Serial Peripheral Interface (QuadSPI)
74.11.2.11 Buffer 0 Top Index Register (BUF0IND)
Offset
Register Offset
BUF0IND 30h
Function
This register specifies the top index for buffer 0, which defines its size. Note that the three LSBs of this register are set to 0. This
ensures that the buffer is 64-bit aligned because each buffer entry is 64-bits long.
The register value should be set to the desired number of bytes. For example, setting BUF0IND[31:3] to 0 gives 0 bytes, setting
the value to 1 gives 8 bytes, and so on.
The size of buffer 0 is the difference between BUF0IND and 0.
The software must ensure that the value of TPINDX0 is not greater than the size of buffer 0.
Special write-access is permitted if:
• SR [ AHB_ACC ] = 0
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
TPINDX0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-9
—
Top index of buffer 0
8-3
TPINDX0
Reserved
2-0
—

---

*Page 3212*

Quad Serial Peripheral Interface (QuadSPI)
74.11.2.12 Buffer 1 Top Index Register (BUF1IND)
Offset
Register Offset
BUF1IND 34h
Function
This register specifies the top index of buffer 1, which defines its size. Note that the three LSBs of this register are set to 0. This
ensures that the buffer is 64-bit aligned because each buffer entry is 64-bits long.
The size of buffer 1 is the difference between BUF1IND and BUF0IND. The register value should be entered in bytes. For example,
if BUF0IND = 0x100, then setting BUF1IND = 0x130 sets the size of buffer 1 to 0x30 bytes.
The software must ensure that the value of TPINDX1 is not greater than the size of buffer 1.
Special write-access is permitted if:
• SR [ AHB_ACC ] = 0
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
TPINDX1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-9
—
8-3 Top index of buffer 1
TPINDX1
Reserved
2-0
—

---

*Page 3213*

Quad Serial Peripheral Interface (QuadSPI)
74.11.2.13 Buffer 2 Top Index Register (BUF2IND)
Offset
Register Offset
BUF2IND 38h
Function
This register specifies the top index of buffer 2, which defines its size. Note that the three LSBs of this register are set to 0. This
ensures that the buffer is 64-bit aligned because each buffer entry is 64-bits long.
The size of buffer 2 is the difference between BUF2IND and BUF1IND. The register value should be entered in bytes. For example,
if BUF1IND = 0x130 then setting BUF2IND = 0x180 sets the size of buffer 2 to 0x50 bytes.
The software must ensure that the value of TPINDX2 is not greater than the size of buffer 2.
Special write-access is permitted if:
• SR [ AHB_ACC ] = 0
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
TPINDX2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-9
—
8-3 Top index of buffer 2
TPINDX2
Reserved
2-0
—

---

*Page 3214*

Quad Serial Peripheral Interface (QuadSPI)
74.11.2.14 DLL Flash Memory A Configuration Register (DLLCRA)
Offset
Register Offset
DLLCRA 60h
Function
This register configures slave delay chain for flash memory A.
See Delay chain usage for the programming sequence.
NOTE
See the chip data sheet for information on programming register fields.
NOTE
Please see the chip specific section of QuadSPI DLLCRA register for delay elements data
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
Reserv FREQ
Reserved Reserved SLV_FINE_OFFSET
ed EN
W
Reset 0 0 0 0 0 0 0 1 0 0 1 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
Reserv Reserv SLV_ SLV_D SLV_
SLV_DLY_OFFSET SLV_DLY_COARSE Reserved
ed ed EN LL ... UPD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
Frequency enable
30
0b - Selects delay chain for low frequency of operation
FREQEN
1b - Selects delay chain for high frequency of operation
Reserved
29-28
—
Reserved
27-24
Table continues on the next page...

---

*Page 3215*

Quad Serial Peripheral Interface (QuadSPI)
Table continued from the previous page...
Field Function
—
Reserved
23-20
—
19-16 Fine offset delay elements in incoming DQS
SLV_FINE_OFF This field sets the number of fine offset delay elements up to 16 in incoming DQS, and the default must
SET be 1 element.
Reserved
15
—
14-12 T/16 offset delay elements in incoming DQS
SLV_DLY_OFF This field sets the number of T/16 offset delay elements in incoming DQS; default is 0.
SET
11-8 Delay elements in each delay tap
SLV_DLY_COA This field sets the number of delay elements in each delay tap. The field is used to overwrite DLL-
RSE generated delay values and works when the value of SLV_DLL_BYPASS is 1. Note : Please refer to the
QuadSPI datasheet for more details.
Reserved
7-5
—
Reserved
4
—
Reserved
3
—
Slave enable
2
0b - DLL slave logic remains in reset, and its value should be 0 for at least three flash memory
SLV_EN
clock cycles for reset.
1b - Enables DQS slave delay chain, and should be 1 before any slave configuration settings take
place.
1 Slave DLL bypass
SLV_DLL_BYP This field enables selection of the number of delays in each slave delay tap.
ASS
0b - Disables manual selection of coarse delays in the slave delay chain.
1b - Enables selection of number of delays in each slave delay tap, based on
DLLCRA[SLV_DLY_COARSE].
0 Slave update
Table continues on the next page...

---

*Page 3216*

Quad Serial Peripheral Interface (QuadSPI)
Table continued from the previous page...
Field Function
SLV_UPD You must program this field only after slave delay chain configuration takes place.
0b - Disables any further update on DQS slave delay chain.
1b - Updates the DQS slave delay chain with either ref-delay or bypass slave delay value, and
should be set in the absence of the DQS clock.
74.11.2.15 Serial Flash Memory Address Register (SFAR)
Offset
Register Offset
SFAR 100h
Function
The module automatically translates this address on the memory map to the address on the flash memory. When operating in a
24-bit mode, only bits 23-0 are sent to the flash memory. In the 32-bit mode, bits 27-0 are used with bits 31-28 driven to 0 . See
Table 577 for the mapping between the access mode and the SFAR content and Normal mode for details on command triggering
and command execution. The software must ensure that the serial flash memory address provided in the SFAR register lies in the
valid flash memory address range, as defined in Table 577 .
Special write-access is permitted if:
• SR [ IP_ACC ] = 0
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SFADR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SFADR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Serial flash memory address
31-0
SFADR

---

*Page 3217*

Quad Serial Peripheral Interface (QuadSPI)
74.11.2.16 Sampling Register (SMPR)
Offset
Register Offset
SMPR 108h
Function
This register allows configuration of how the incoming data from the external serial flash memory devices is sampled in the
QuadSPI module.
NOTE
See the chip data sheet for programming the register fields.
Special write-access is permitted in the disabled mode.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
Reserv Reserv
Reserved DLLFSMPFA Reserved
ed ed
W
Reset 1 1 1 1 1 1 1 1 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
FSPH
FSDLY
S
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
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
26-24 Selects the nth tap provided by slave delay chain for flash memory A
DLLFSMPFA The value of n can vary from 0 to 7, with each tap delay based on the DLLCRA register.
Reserved
23-19
Table continues on the next page...

---

*Page 3218*

Quad Serial Peripheral Interface (QuadSPI)
Table continued from the previous page...
Field Function
—
Reserved
18-16
—
Reserved
15-7
—
6 Full speed delay selection for SDR instructions
FSDLY Select the delay with respect to the reference edge for the sample point valid for full speed commands.
0b - One clock cycle delay
1b - Two clock cycles delay
5 Full-speed phase selection for SDR instructions
FSPHS This field selects the edge of the sampling clock valid for full-speed commands.
0b - Select sampling at non-inverted clock
1b - Select sampling at inverted clock
Reserved
4-3
—
Reserved
2-0
—
74.11.2.17 RX Buffer Status Register (RBSR)
Offset
Register Offset
RBSR 10Ch
Function
This register contains information related to the receive data buffer.

---

*Page 3219*

Quad Serial Peripheral Interface (QuadSPI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RDCTR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 RDBFL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-16 Read counter
RDCTR Indicates the number of 4-byte entries removed from the RX buffer. For example, a value of 0x2 indicates
that 8 bytes have been removed.
It is incremented by the number (RBCT[WMRK] + 1) on RX buffer POP event. The RX buffer can be popped
using DMA or FR[RBDF]. The RSER[RBDDE] defines which pop should be pursued. For details, see AHB
RX Data Buffer Register (ARDB0 - ARDB127) and Data Transfer from the QuadSPI Module Internal Buffers .
Reserved
15-6
—
5-0 RX buffer fill level
RDBFL Indicates the number of 4-byte entries available in the RX buffer. For example, a value of 0x2 indicates 8
bytes are available.
74.11.2.18 RX Buffer Control Register (RBCT)
Offset
Register Offset
RBCT 110h
Function
This register contains control data related to the receive data buffer.
Special write-access is permitted if:
• SR [ IP_ACC ] = 0

---

*Page 3220*

Quad Serial Peripheral Interface (QuadSPI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
Reserv
WMRK
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-9
—
Reserved
8
—
Reserved
7-5
—
4-0 RX buffer watermark
WMRK This field determines when the readout action of the RX buffer is triggered. When the number of valid entries
in the RX buffer is equal to or greater than the number provided by (WMRK+1), the SR[RXWE] flag is
asserted. The value should be entered as the number of 4-byte entries minus 1. For example, a value of 0x0
sets the watermark to 4 bytes, 1 to 8bytes, 2 to 12 bytes, and so on.
For details, see DMA usage .
NOTE
This field should never be programmed above 31 because there are only 32 memory
mapped RBDR registers. If watermark is programmed above 31, data above 32 words will
be lost.
74.11.2.19 Data Learning Status Flash Memory A Register (DLSR_FA)
Offset
Register Offset
DLSR_FA 134h

---

*Page 3221*

Quad Serial Peripheral Interface (QuadSPI)
Function
This register shows sampling point selected by data learning algorithm when the value of DLSR_FA[DLPFFA] is 0. Otherwise,
it shows the pattern matching outline.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DLPFF
R 0
A
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R POS_EDGE NEG_EDGE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Data learning pattern fail
DLPFFA This field asserts when data learning fails at flash memory A.
Reserved
30-16
—
DLP positive edge match signature for flash memory A
15-8
POS_EDGE
DLP negative edge match signature for flash memory A
7-0
NEG_EDGE
74.11.2.20 TX Buffer Status Register (TBSR)
Offset
Register Offset
TBSR 150h
Function
This register contains information related to the transmit data buffer.

---

*Page 3222*

Quad Serial Peripheral Interface (QuadSPI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TRCTR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 TRBFL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-16 Transmit counter
TRCTR This field indicates how many entries of 4 bytes have been written into the TX buffer by host accesses. It
is reset to 0 when a 1 is written to MCR[CLR_TXF]. It is incremented on each write access to the TBDR
register when another word has been pushed onto the TX buffer. When it is not cleared, the TRCTR field
wraps around to 0. See TX Buffer Data Register (TBDR) for details.
Reserved
15-9
—
Reserved
8-6
—
5-0 TX buffer fill level
TRBFL This field contains the number of entries of 4 bytes each available in the TX buffer for the QuadSPI module
to transmit to the serial flash memory device. The value of this field can reach maximum up to the total TX
buffer size.
74.11.2.21 TX Buffer Data Register (TBDR)
Offset
Register Offset
TBDR 154h
Function
This register provides access to the circular TX buffer of depth 32 , so the total size is 32 * 4 bytes. This buffer provides the data
written into it as write data for the page programming commands to the serial flash memory device. See Table 557 for the byte
ordering scheme. A write transaction on the flash memory with data size of less than 32 bits leads to the removal of one data entry
from the TX buffer. The valid bits are used and the rest of the bits are discarded.
Special write-access is permitted if:

---

*Page 3223*

Quad Serial Peripheral Interface (QuadSPI)
• SR [ TXFULL ] = 0
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TXDATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TXDATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 TX data
TXDATA On write access, the data is written to the next available entry of the TX buffer and TBSR[TRBFL] is
updated accordingly.
On a read access, the last data written to the register is returned.
74.11.2.22 TX Buffer Control Register (TBCT)
Offset
Register Offset
TBCT 158h
Function
This register contains control information for transmit data buffer.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
WMRK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 3224*

Quad Serial Peripheral Interface (QuadSPI)
Fields
Field Function
Reserved
31-8
—
Reserved
7-5
—
4-0 Watermark for TX buffer
WMRK Determines the watermark for the TX buffer
When the number of available space in the TX buffer is greater than or equal to the number provided by
WMRK (number of 4-byte entries) , SR[TXWA] is asserted . For example, a value of 0x1 sets the watermark
to 4 bytes, 0x2 sets it to 8 bytes, 0x3 sets it to 12 bytes, and so on. For details, see DMA usage .
WMRK = 0 is invalid.
74.11.2.23 Status Register (SR)
Offset
Register Offset
SR 15Ch
Function
This register provides all the available status information about SFM command execution and arbitration, the RX buffer, TX buffer,
and the AHB buffer.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
TXFUL TXDM RXDM RXFU
R 0 0 TXWA TXNE 0 0 RXWE
L A A LL
W
Reset 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
AHB3 AHB2 AHB1 AHB0 AHB3 AHB2 AHB1 AHB0 AHBT Reserv Reserv AHB_ IP_
R 0 0 BUSY
FUL FUL FUL FUL NE NE NE NE RN ed ed ACC ACC
W
Reset 0 0 1 1 1 0 0 0 0 0 0 0 0 0 0 0

---

*Page 3225*

Quad Serial Peripheral Interface (QuadSPI)
Fields
Field Function
Reserved
31-29
—
Reserved
28
—
27 TX buffer full
TXFULL Asserted when the FIFO level reaches 39 (that is, TX buffer size of 32 + async FIFO size of 7)
26 TX DMA
TXDMA Asserted when the TXFIFO fill via DMA is active and DMA is requested or running
25 TX buffer watermark available
TXWA Asserted when the number of available spaces in the TX buffer is greater than or equal to the value provided
by TBCT[WMRK]
Example: When TBCT[WMRK]=1, SR[TXWA] is de-asserted when TX FIFO has 32+7(size of async
FIFO) entries
24 TX buffer not empty
TXNE Asserted when TX buffer contains data
23 RX buffer DMA
RXDMA Asserted when RX buffer read out via DMA is active; that is, when DMA is requested or running
Reserved
22-20
—
19 RX buffer full
RXFULL Asserted when the RX buffer is full; that is, when RBSR[RDBFL] is equal to 32
Reserved
18-17
—
16 RX buffer watermark exceeded
RXWE Asserted when the number of valid entries in the RX buffer exceeds the number provided in RBCT[WMRK]
Reserved
15
—
14 AHB 3 buffer full
AHB3FUL Asserted when AHB 3 buffer is full
13 AHB 2 buffer full
Table continues on the next page...

---

*Page 3226*

Quad Serial Peripheral Interface (QuadSPI)
Table continued from the previous page...
Field Function
AHB2FUL Asserted when AHB 2 buffer is full
12 AHB 1 buffer full
AHB1FUL Asserted when the AHB 1 buffer is full
11 AHB 0 buffer full
AHB0FUL Asserted when the AHB 0 buffer is full
10 AHB 3 buffer not empty
AHB3NE Asserted when the AHB 3 buffer contains data
9 AHB 2 buffer not empty
AHB2NE Asserted when the AHB 2 buffer contains data
8 AHB 1 buffer not empty
AHB1NE Asserted when the AHB 1 buffer contains data
7 AHB 0 buffer not empty
AHB0NE Asserted when the AHB 0 buffer contains data
6 AHB access transaction pending
AHBTRN Asserted when there is a pending request on the AHB interface. See Flash memory mapped AMBA bus .
Reserved
5
—
Reserved
4
—
Reserved
3
—
2 AHB read access
AHB_ACC Asserted when the currently executed transaction is initiated by the AHB bus
1 IP access
IP_ACC Asserted when transaction currently executed is initiated by the IP bus
0 Module busy
BUSY Asserted when module is currently busy handling a transaction to an external flash memory device

---

*Page 3227*

Quad Serial Peripheral Interface (QuadSPI)
74.11.2.24 Flag Register (FR)
Offset
Register Offset
FR 160h
Function
This register provides all available flags about SFM command execution and arbitration, which may serve as the source for the
generation of interrupt service requests. Note that the error flags in this register do not relate directly to the execution of the
transaction in the serial flash memory device itself but only to the behavior and conditions visible in the QuadSPI module.
Special write-access is permitted in the enabled mode.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
Reserv
R 0 0 TBFF TBUF 0 0 ILLINE 0 0 0 RBOF RBDF
Reserv
ed
ed
W W1C W1C W1C W1C W1C
Reset 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
AIBSE Reserv CRCA
R AAEF AITEF ABOF 0 0 IPAEF IPIEF 0 0 0 TFF
F ed EF
W W1C W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
Reserved
30
—
Reserved
29
—
Reserved
28
—
27 TX buffer fill flag
TBFF
Table continues on the next page...

---

*Page 3228*

Quad Serial Peripheral Interface (QuadSPI)
Table continued from the previous page...
Field Function
Before writing to the TX buffer, this field should be cleared. Then, it should be read back. If it is set, the TX
buffer can include more data. If the field remains cleared, the TX buffer can be considered as full. See TX
buffer operation for details.
26 TX buffer underrun flag
TBUF This field is set if the module tries to pull data when the TX buffer is empty.. The IP command leading to the
TX buffer underrun is continued (data sent to the serial flash memory device is undefined ). Here, a valid
underrun means that it should have occurred during the transaction so that few bytes (that is, less than 4
bytes) are left in FIFO and the remaining are filled with "FFFFh". This field does not set if transfer is less than
128 bits. The application must clear the TX buffer in response to this event by writing a 1 to MCR[CLR_TXF].
The application must clear the TX buffer in response to this event by writing a 1 to MCR[CLR_TXF].
Reserved
25
—
Reserved
24
—
23 Illegal instruction error flag
ILLINE This field is set when an illegal instruction is encountered by the controller in any of the sequences. As
soon as the field is set, you must assert MCR[SWRSTSD] and MCR[SWRSTHD]. That is, reset the flash
memory and AHB domain after reconfiguring the correct sequence instruction. See Table 555 for a list of
legal instructions.
Reserved
22-21
—
Reserved
20
—
Reserved
19-18
—
17 RX buffer overflow flag
RBOF This field is set when no more data can be pushed into the RX buffer from the serial flash memory device.
The IP command leading to this condition is continued until the number of bytes in IPCR[IDATSZ] are read
from the serial flash memory device.
The content of the RX buffer remains unchanged.
16 RX buffer drain flag
RBDF This field is set if SR[RXWE] is asserted.
Writing 1 to this field triggers one of the following actions:
Table continues on the next page...

---

*Page 3229*

Quad Serial Peripheral Interface (QuadSPI)
Table continued from the previous page...
Field Function
• If the RX buffer has up to RBCT[WMRK] valid entries, then the flag is cleared.
• If the RX buffer has more than RBCT[WMRK] valid entries and the RSER[RBDDE] field is not set
(flag driven mode), an RX buffer POP event is triggered.
The flag remains set if the RX buffer contains more than RBCT[WMRK] valid entries after the RX buffer POP
event is complete.
The flag is cleared if the RX buffer contains less than or equal to RBCT[WMRK] valid entries after the RX
buffer POP event is complete.
See the "Receive Buffer Drain Interrupt or DMA Request" section in Normal mode interrupt and DMA
requests for details.
15 AHB abort error flag
AAEF This field is set in case software abort is asserted in an ongoing AHB transaction. The field should be cleared
before starting any new AHB transaction.
14 AHB illegal transaction error flag
AITEF This is set whenever there is no response generated from QuadSPI to AHB bus in case of an illegal
transaction and the watchdog timer expires. The timer value is considered as a parameter.
13 AHB illegal burst size error flag
AIBSEF This is set whenever the total burst size (size x beat) of an AHB transaction is greater than the prefetch
data size, which is defined by BUFxCR[ADATSZ] or data size mentioned in the sequence pointed to by
the SEQID field in case ADATSZ = 0. See HBURST support with AHB read details on HBURST feature.
12 AHB buffer overflow flag
ABOF ABOF has different behavior depending on which AHB buffer caused this flag. This is set when the size of
the AHB access exceeds the size of the AHB buffer (0-2). This condition can occur if BUFxCR[ADATSZ]
is programmed incorrectly. The AHB command leading to this condition is continued until the number of
entries according to BUFxCR[ADATSZ] have been read from the serial flash memory device.The content
of the AHB buffer is not changed. AHB buffer 3 has support for extended prefetch/streaming mode. It's not
mandatory that ABOF bit is set for this buffer.If write location of prefetched data from flash in the AHB buffer
equals the location of last AHB data read from the AHB buffer, AHB transaction is aborted.
Reserved
11
—
Sets when there is CRC or ECC error for flash memory A
10
0b - CRCEF interrupt is not generated.
CRCAEF
1b - CRCEF interrupt is generated.
Reserved
9
—
Reserved
8
—
Table continues on the next page...

---

*Page 3230*

Quad Serial Peripheral Interface (QuadSPI)
Table continued from the previous page...
Field Function
7 IP command trigger during AHB access error flag
IPAEF This is set when the following condition occurs:
• A write access occurs to IPCR[SEQID] and the SR [ AHB_ACC ] field is set. Any command leading to
the assertion of the IPAEF field is ignored.
6 IP command trigger could not be executed error flag
IPIEF This is set when the SR [ IP_ACC ] and SR[AWRACC] fields are set (that is, an IP triggered command is
currently executing) and any of the following conditions occurs:
• Write access to the IPCR. Any command leading to the assertion of the IPIEF flag is ignored.
• Write access to the SFAR
• Write access to the RBCT
Reserved
5
—
Reserved
4
—
Reserved
3-1
—
0 IP command transaction finished flag
TFF This field is set after the QuadSPI module completes a running IP command. If an error occurs, and the
related error flags are valid in the same clock cycle, the TFF flag is asserted.
74.11.2.25 Interrupt and DMA Request Select and Enable Register (RSER)
Offset
Register Offset
RSER 164h
Function
This register provides enables and selectors for the interrupts in the QuadSPI module.
NOTE
Each field of the FR enabled as source for an interrupt prevents the QuadSPI module from entering the Stop mode
or Module Disable mode when this flag is set.
Special write-access is permitted in the "Anytime" mode.

---

*Page 3231*

Quad Serial Peripheral Interface (QuadSPI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
Reserv TBFD Reserv ILLINI Reserv RBDD Reserv
Reserved TBFIE TBUIE RBOIE RBDIE
ed E ed E ed E ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
AIBSI Reserv CRCAI Reserv Reserv IPAEI Reserv
AAIE AITIE ABOIE IPIEIE TFIE
E ed E ed ed E ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
Reserved
30-29
—
Reserved
28
—
27 TX buffer fill interrupt enable flag
TBFIE This field indicates the TX buffer fill interrupt enable flag.
0b - No TBFF interrupt is generated.
1b - TBFF interrupt is generated.
26 TX buffer underrun interrupt enable flag
TBUIE This field indicates the TX buffer underrun interrupt enable flag.
0b - No TBUF interrupt is generated
1b - TBUF interrupt is generated
25 TX buffer fill DMA enable
TBFDE Enables generation of DMA requests for TX buffer fill. When the value of this field is 1, DMA requests are
generated as long as number of available spaces in the TX buffer is greater than or equal to the value
provided by TBCT[WMRK].
NOTE
After you write 1 to this field (to enable DMA transfers), writing 0 does not disable
DMA transfers. You must perform a software reset for the AHB domain by using
MCR[SWRSTHD] to disable DMA transfers.
Table continues on the next page...

---

*Page 3232*

Quad Serial Peripheral Interface (QuadSPI)
Table continued from the previous page...
Field Function
0b - No DMA request is generated
1b - DMA request is generated
Reserved
24
—
23 Illegal instruction error interrupt enable
ILLINIE Triggered by the ILLINE flag in FR
0b - No ILLINE interrupt is generated.
1b - ILLINE interrupt is generated.
Reserved
22
—
21 RX buffer drain DMA enable
RBDDE This field enables generation of DMA requests for RX buffer drain. When the value of this field is 1, the DMA
requests are generated as long as SR[RXWE] is set.
NOTE
After you write 1 to this field (to enable DMA transfers), writing 0 does not disable
DMA transfers. You must perform a software reset for the AHB domain by using
MCR[SWRSTHD] to disable DMA transfers.
0b - No DMA request is generated.
1b - DMA request is generated.
Reserved
20
—
Reserved
19-18
—
17 RX buffer overflow interrupt enable
RBOIE This field indicates the RX buffer overflow interrupt enable flag.
0b - No RBOF interrupt is generated.
1b - RBOF interrupt is generated.
16 RX buffer drain interrupt enable
RBDIE This field enables generation of IRQ requests for RX buffer drain. When the value of this field is 1, the
interrupt is asserted as long as SR[RBDF] is set.
0b - No RBDF interrupt is generated.
1b - RBDF Interrupt is generated.
Table continues on the next page...

---

*Page 3233*

Quad Serial Peripheral Interface (QuadSPI)
Table continued from the previous page...
Field Function
15 AHB abort error interrupt enable
AAIE Triggered by AAEF flags in FR.
0b - No AAEF interrupt is generated
1b - AAEF interrupt is generated
14 AHB illegal transaction interrupt enable flag
AITIE This field indicates the AHB illegal transaction interrupt enable flag.
0b - No AITEF interrupt is generated.
1b - AITEF interrupt is generated.
13 AHB illegal burst size interrupt enable flag
AIBSIE This field indicates the AHB illegal burst size interrupt enable flag.
0b - No AIBSEF interrupt is generated.
1b - AIBSEF interrupt is generated.
12 AHB buffer overflow interrupt enable flag
ABOIE This field indicates the AHB buffer overflow interrupt enable flag.
0b - No ABOF interrupt is generated.
1b - ABOF interrupt is generated.
Reserved
11
—
CRC and ECC interrupt enable for flash memory A
10
0b - CRCAEF interrupt is not generated.
CRCAIE
1b - CRCAEF interrupt is generated.
Reserved
9
—
Reserved
8
—
7 IP command trigger during AHB read access error interrupt enable flag
IPAEIE This field indicates IP command trigger during AHB read access error interrupt enable flag.
0b - No IPAEF interrupt is generated
1b - IPAEF interrupt is generated
6 IP command trigger during IP access error interrupt enable flag
IPIEIE This field indicates IP command trigger during IP access error interrupt enable flag.
Table continues on the next page...

---

*Page 3234*

Quad Serial Peripheral Interface (QuadSPI)
Table continued from the previous page...
Field Function
0b - No IPIEF interrupt is generated
1b - IPIEF interrupt is generated
Reserved
5
—
Reserved
4
—
Reserved
3-1
—
0 Transaction finished interrupt enable flag
TFIE This field indicates the transaction finished interrupt enable flag.
0b - No TFF interrupt is generated.
1b - TFF interrupt is generated.
74.11.2.26 Sequence Pointer Clear Register (SPTRCLR)
Offset
Register Offset
SPTRCLR 16Ch
Function
This register provides fields to reset the IP and buffer sequence pointers. The sequence pointer contains the index of instructions
within the LUT entry that is to be executed next. For example, if the LUT entry ends on a JMP_ON_CS value of 2, the index is
stored as 2.
The software should reset the sequence pointers defined by JMP_ON_CS operand whenever the sequence ID is required to be
changed by updating the SEQID field in the IPCR or BFGENCR.

---

*Page 3235*

Quad Serial Peripheral Interface (QuadSPI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0
PREF ABRT_
ETC ... CLR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0
IPPTR BFPTR
W
C C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-26
—
Reserved
25-24
—
Reserved
23-20
—
Reserved
19-18
—
17 Prefetch disable
PREFETCH_DI This field should be configured to disable the prefetch mechanism of receiver. It is not based on dynamic
S programming. Therefore, it should be programmed initially at once. When this field is set, then during an
ongoing flash memory read, any subsequent AHB read is checked for buffer hit. However, after the end of
flash memory read, as soon as chip select is deasserted, any subsequent AHB read results in flushing of
the current AHB buffer data and issues fresh flash memory transaction if the AHB buffer data is not updated
with the flash memory.
16 Flash memory Abort/AHB buffer clear
ABRT_CLR This is a dynamic field. Writing a 1 to it, irrespective of the prefetch disable, clears the AHB buffer pointers
and also aborts any ongoing flash memory transaction (if any) and rejects any ongoing AHB read with an
error response (if any).
QuadSPI sets this field to 0 after clearing the AHB buffer pointers.
Reserved
15-9
—
8 IP pointer clear
Table continues on the next page...

---

*Page 3236*

Quad Serial Peripheral Interface (QuadSPI)
Table continued from the previous page...
Field Function
IPPTRC This is a self-clearing field.
1b - Clears the sequence pointer for IP accesses as defined in IPCR.
Reserved
7-1
—
0 Buffer pointer clear
BFPTRC This is a self-clearing field.
1b - Clears the sequence pointer for AHB read accesses as defined in BFGENCR.
74.11.2.27 Serial Flash Memory A1 Top Address Register (SFA1AD)
Offset
Register Offset
SFA1AD 180h
Function
This register provides the address mapping for serial flash memory A1. The difference between SFA1AD[TPADA1] and
AMBA_BASE defines the size of the memory map for serial flash memory A1.
Special write-access is permitted if:
• SR [ IP_ACC ] = 0
• SR [ AHB_ACC ] = 0
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TPADA1
W
Reset 0 1 1 1 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
TPADA1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 3237*

Quad Serial Peripheral Interface (QuadSPI)
Fields
Field Function
31-10 Top address for serial flash memory A1
TPADA1 In effect, TPADxx is the first location of the next memory.
Reserved
9-0
—
74.11.2.28 Serial Flash Memory A2 Top Address Register (SFA2AD)
Offset
Register Offset
SFA2AD 184h
Function
This register provides the address mapping for serial flash memory A2. The difference between SFA2AD[TPADA2] and
SFA1AD[TPADA1] defines the size of the memory map for serial flash memory A2.
Special write-access is permitted if:
• SR [ IP_ACC ] = 0
• SR [ AHB_ACC ] = 0
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TPADA2
W
Reset 0 1 1 1 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
TPADA2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-10 Top address for serial flash memory A2
TPADA2 In effect, TPxxAD is the first location of the next memory.
Table continues on the next page...

---

*Page 3238*

Quad Serial Peripheral Interface (QuadSPI)
Table continued from the previous page...
Field Function
Reserved
9-0
—
74.11.2.29 Serial Flash Memory B1 Top Address Register (SFB1AD)
Offset
Register Offset
SFB1AD 188h
Function
This register provides the address mapping for serial flash memory B1. The difference between SFB1AD[TPADB1] and
SFA2AD[TPADA2] defines the size of the memory map for serial flash memory B1.
Special write-access is permitted if:
• SR [ IP_ACC ] = 0
• SR [ AHB_ACC ] = 0
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TPADB1
W
Reset 0 1 1 1 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
TPADB1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-10 Top address for serial flash memory B1.
TPADB1 In effect, TPxxAD is the first location of the next memory.
Reserved
9-0
—

---

*Page 3239*

Quad Serial Peripheral Interface (QuadSPI)
74.11.2.30 Serial Flash Memory B2 Top Address Register (SFB2AD)
Offset
Register Offset
SFB2AD 18Ch
Function
This register provides the address mapping for serial flash memory B2. The difference between SFB2AD[TPADB2] and
SFB1AD[TPADB1] defines the size of the memory map for serial flash memory B2.
Special write-access is permitted if:
• SR [ IP_ACC ] = 0
• SR [ AHB_ACC ] = 0
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TPADB2
W
Reset 0 1 1 1 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
TPADB2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-10 Top address for serial flash memory B2.
TPADB2 In effect, TPxxAD is the first location of the next memory.
Reserved
9-0
—
74.11.2.31 RX Buffer Data Register (RBDR0 - RBDR31)
Offset
For a = 0 to 31:
Register Offset
RBDRa 200h + (a × 4h)

---

*Page 3240*

Quad Serial Peripheral Interface (QuadSPI)
Function
These registers provide access to individual entries in the RX buffer. See Table 557 for the byte ordering scheme.
RBDR0 corresponds to the actual position of the read pointer within the RX buffer. The number of valid entries available depends
on the number of RX buffer entries implemented and on the number of valid buffer entries available in the RX buffer.
Example 1 - RX buffer filled completely with 32 words: In this case, the address range for valid read access extends from RBDR0
to RBDR31 RBDR63 .
Example 2 - RX buffer filled with five valid words: RX buffer fill level of RBSR[RDBFL] is 5. In this case, access to RBDR4 provides
the last valid entry.
Any access beyond the range of valid RX buffer entries provides undefined results.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXDATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXDATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 RX data
RXDATA This field contains the data associated with the related RX buffer entry. For data format and byte ordering,
see Byte ordering of serial flash memory read data .
74.11.2.32 LUT Key Register (LUTKEY)
Offset
Register Offset
LUTKEY 300h
Function
This register contains the key to lock and unlock the LUT. See LUT for details.
Special write-access is permitted in the "Anytime" mode. Write access is not permitted if MGC[11]=1b.

---

*Page 3241*

Quad Serial Peripheral Interface (QuadSPI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
KEY
W
Reset 0 1 0 1 1 0 1 0 1 1 1 1 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
KEY
W
Reset 0 1 0 1 1 0 1 0 1 1 1 1 0 0 0 0
Fields
Field Function
31-0 Key to lock or unlock the LUT
KEY The key is 0x5AF05AF0 and the read value is always 0x5AF05AF0. Write access is not permitted
if MGC[11]=1b.
74.11.2.33 LUT Lock Configuration Register (LCKCR)
Offset
Register Offset
LCKCR 304h
Function
This register is used along with the LUTKEY register to lock or unlock the LUT. This register should be written immediately after
the LUTKEY register for the lock or unlock operation to be successful. See LUT for details. Setting both the LOCK and UNLOCK
bits as "00" or "11" is not allowed.
Special write access is permitted after writing the LUT key register. Write access is not permitted if MGC[11]=1b.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
UNLO
LOCK
CK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0

---

*Page 3242*

Quad Serial Peripheral Interface (QuadSPI)
Fields
Field Function
Reserved
31-2
—
1 Unlock LUT
UNLOCK Unlocks the LUT when the following two conditions are met:
• This register is written just after the LUT Key Register (LUTKEY) .
• The LUT key register was written with the 0x5AF05AF0 key.
0 Lock LUT
LOCK Locks the LUT when the following conditions are met:
• This register is written just after the LUT Key Register (LUTKEY) .
• The LUT key register is written with the 0x5AF05AF0 key.
74.11.2.34 LUT Register (LUT0)
Offset
Register Offset
LUT0 310h
Function
A sequence of instruction-operand pairs may be pre-populated in the LUT according to the device connected on board. Each
instruction-operand pair is of 16 bits (2 bytes) each. Every sequence preprogrammed by Program Sequence Engine in the LUT
is referred to by its index. The LUT registers act as lookup tables for sequences of instructions. The programmable sequence
engine executes the instructions in these sequences to generate a valid serial flash memory transaction. There are a total of 20
LUT registers. These 20 registers are divided into groups of 5 registers that make a valid sequence. Therefore, LUT[0], LUT[5],
LUT[10] ..... LUT[15] are the starting registers of a valid sequence. Each of these sets of 5 registers can have a maximum of 10
instructions. Reset value of the register shown below is only applicable to LUT2 to LUT19. A maximum of 4 sequences can be
defined at one time. See LUT that describes the LUT registers in detail.
Special write-access is permitted if the LUT is unlocked.

---

*Page 3243*

Quad Serial Peripheral Interface (QuadSPI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
INSTR1 PAD1 OPRND1
W
Reset 0 0 0 0 1 0 0 0 0 0 0 1 1 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
INSTR0 PAD0 OPRND0
W
Reset 0 0 0 0 0 1 0 0 0 0 0 0 0 0 1 1
Fields
Field Function
Instruction 1
31-26
INSTR1
Pad information for INSTR1
25-24
00b - 1 Pad
PAD1
01b - 2 Pads
10b - 4 Pads
11b - NA
Operand for INSTR1
23-16
OPRND1
Instruction 0
15-10
INSTR0
Pad information for INSTR0
9-8
00b - 1 Pad
PAD0
01b - 2 Pads
10b - 4 Pads
11b - NA
Operand for INSTR0
7-0
OPRND0

---

*Page 3244*

Quad Serial Peripheral Interface (QuadSPI)
74.11.2.35 LUT Register (LUT1)
Offset
Register Offset
LUT1 314h
Function
A sequence of instruction-operand pairs may be pre-populated in the LUT according to the device connected on board. Each
instruction-operand pair is of 16 bits (2 bytes) each. Every sequence preprogrammed by Program Sequence Engine in the LUT
is referred to by its index. The LUT registers act as lookup tables for sequences of instructions. The programmable sequence
engine executes the instructions in these sequences to generate a valid serial flash memory transaction. There are a total of 20
LUT registers. These 20 registers are divided into groups of 5 registers that make a valid sequence. Therefore, LUT[0], LUT[5],
LUT[10] ..... LUT[15] are the starting registers of a valid sequence. Each of these sets of 5 registers can have a maximum of 10
instructions. Reset value of the register shown below is only applicable to LUT2 to LUT19. A maximum of 4 sequences can be
defined at one time. See LUT that describes the LUT registers in detail.
Special write-access is permitted if the LUT is unlocked.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
INSTR1 PAD1 OPRND1
W
Reset 0 0 1 0 0 1 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
INSTR0 PAD0 OPRND0
W
Reset 0 0 0 1 1 1 0 0 0 0 0 0 1 0 0 0
Fields
Field Function
Instruction 1
31-26
INSTR1
Pad information for INSTR1
25-24
00b - 1 Pad
PAD1
01b - 2 Pads
10b - 4 Pads
11b - NA
Operand for INSTR1
23-16
Table continues on the next page...

---

*Page 3245*

Quad Serial Peripheral Interface (QuadSPI)
Table continued from the previous page...
Field Function
OPRND1
Instruction 0
15-10
INSTR0
Pad information for INSTR0
9-8
00b - 1 Pad
PAD0
01b - 2 Pads
10b - 4 Pads
11b - NA
Operand for INSTR0
7-0
OPRND0
74.11.2.36 LUT Register (LUT2 - LUT19)
Offset
For a = 2 to 19:
Register Offset
LUTa 310h + (a × 4h)
Function
A sequence of instruction-operand pairs may be pre-populated in the LUT according to the device connected on board. Each
instruction-operand pair is of 16 bits (2 bytes) each. Every sequence preprogrammed by Program Sequence Engine in the LUT
is referred to by its index. The LUT registers act as lookup tables for sequences of instructions. The programmable sequence
engine executes the instructions in these sequences to generate a valid serial flash memory transaction. There are a total of 20
LUT registers. These 20 registers are divided into groups of 5 registers that make a valid sequence. Therefore, LUT[0], LUT[5],
LUT[10] ..... LUT[15] are the starting registers of a valid sequence. Each of these sets of 5 registers can have a maximum of 10
instructions. Reset value of the register shown below is only applicable to LUT2 to LUT19. A maximum of 4 sequences can be
defined at one time. See LUT that describes the LUT registers in detail.
Special write-access is permitted if the LUT is unlocked.

---

*Page 3246*

Quad Serial Peripheral Interface (QuadSPI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
INSTR1 PAD1 OPRND1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
INSTR0 PAD0 OPRND0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Instruction 1
31-26
INSTR1
Pad information for INSTR1
25-24
00b - 1 Pad
PAD1
01b - 2 Pads
10b - 4 Pads
11b - NA
Operand for INSTR1
23-16
OPRND1
Instruction 0
15-10
INSTR0
Pad information for INSTR0
9-8
00b - 1 Pad
PAD0
01b - 2 Pads
10b - 4 Pads
11b - NA
Operand for INSTR0
7-0
OPRND0

#### 74.11.3 Serial flash memory address assignment

The serial flash memory address assignment can be modified by writing into Serial Flash Memory A1 Top Address Register
(SFA1AD) and Serial Flash Memory A2 Top Address Register (SFA2AD) for device A

---

*Page 3247*

Quad Serial Peripheral Interface (QuadSPI)
Table 577. Serial flash memory address assignment
Parameter Function Access mode
QuadSPI_AMBA_BAS QuadSPI AHB base address
E ((31:10) - 22 bits)
First address of the serial flash
memory device as presented to the
QuadSPI controller. This might be
the base of the serial flash memory
in the system address map or it may
be a remapping, for instance to 0h,
performed by the system. (See the
system address map file attached to
this document)
QuadSPI_ARDB_BAS First address of the QuadSPI Rx
E buffer on system memory map
TOP_ADDR_MEMA1(T Top address for the external flash Any access to the address space between
PADA1) memory A1 (the first of the two TOP_ADDR_MEMA1 and QuadSPI_AMBA_BASE is routed
independent flash memories sharing to serial flash memory A1.
the IOFA)
TOP_ADDR_MEMA2(T Top address for the external flash Any access to the address space between
PADA2) memory A2 (the second of the two TOP_ADDR_MEMA2 and TOP_ADDR_MEMA1 is routed to
independent flash memories sharing serial flash memory A2.
the IOFA)

#### 74.12 Flash memory mapped AMBA bus

QuadSPI_AMBA_BASE defines the address to be used as the start address of the serial flash memory device, as defined by the
system memory map. Note that this may be a remapping of the physical address of the serial flash memory in the system. See
the system address map for details.
Table 578. QuadSPI AMBA bus memory map
Address Register name
• See Memory-mapped serial flash memory data—individual flash
QuadSPI_AMBA_BASE to ( TOP_ADDR_MEMA2
memory mode on flash memory A .
- 1h)
• For information about byte ordering, see Table 557 and Table 558 .
QuadSPI_ARDB_BASE to... (32 * 4 Byte) • See AHB RX Data Buffer Register (ARDB0 - ARDB127) .
QuadSPI_ARDB_BASE + 1FFh
• For information about the byte ordering, see Table 557 .
NOTE
Any read access to non-implemented addresses provides undefined results.
In individual flash memory modes, the 3/4 address bytes (as programmed in the instruction/operand in the
sequence) available for the flash memory address are determined by SFADR [23:0] or SFADR [31:0] as provided
in the table shown above.

---

*Page 3248*

Quad Serial Peripheral Interface (QuadSPI)

#### 74.12.1 AHB bus access read considerations

Note that all logic in the QuadSPI module implementing the AHB bus access is designed to read the content of an external serial
flash memory device. Therefore, the following restrictions apply to the QuadSPI module with respect to accesses to the AHB bus:
• Any AHB command resulting in the assertion of the FR[ABSEF] flag is answered with the ERROR condition according to
the AMBA_AHB specification. The resulting AHB command is ignored.
• AHB bus read access types—NONSEQ and BUSY—are fully supported.
• AHB read access type—SEQ—is treated in the same way as NONSEQ. See Flash memory mapped AMBA bus for
details.
• Early burst termination is not supported for AHB transactions.
• An AHB error response occurs when FR[AITEF] bit is set
• An AHB bus stall along with error response occurs when FR[AAEF] bit is set

#### 74.12.2 Memory-mapped serial flash memory data—individual flash memory mode on flash memory A

Starting with address QuadSPI_AMBA_BASE , the content of the first external serial flash memory device is mapped into the
address space of the device containing the QuadSPI module. Serial flash memory byte address 0h corresponds to bus address,
QuadSPI_AMBA_BASE , in an increasing order. . See the following table for the address mapping. The byte ordering for 32-bit
access is provided in Table 557 and for 64-bit read access, the byte ordering is provided in Table 558 .
Table 579. Memory-mapped individual flash memory mode—flash memory A address scheme
Memory-mapped address 32- Memory-mapped address 64- Serial flash memory byte address Flash
bit access bit access memory
device
QuadSPI_AMBA_BASE + 0h 0h to 3h A1
QuadSPI_AMBA_BASE + 0h
QuadSPI_AMBA_BASE + 4h 4h to 7h
... ... ...
TOP_ADDR_MEMA1 - 8h ( TOP_ADDR_MEMA1 - 8h) to
( TOP_ADDR_MEMA1 - 0h4 -0h1)
TOP_ADDR_MEMA1 - 8h
TOP_ADDR_MEMA1 - 4h ( TOP_ADDR_MEMA1 - 4h) to
( TOP_ADDR_MEMA1 - 0h1)
TOP_ADDR_MEMA1 + 4h TOP_ADDR_MEMA1 + 0h 0h to 3h A2
TOP_ADDR_MEMA1 + 0h4 4h to 7h
... ... ...
TOP_ADDR_MEMA2 - 8h TOP_ADDR_MEMA2 - 8h ( TOP_ADDR_MEMA2 - 8h) to
( TOP_ADDR_MEMA2 - 4h - 1h)
TOP_ADDR_MEMA2 - 4h ( TOP_ADDR_MEMA2 - 4h) to
( TOP_ADDR_MEMA2 - 1h)
The available address range depends on the size of the external serial flash memory device. Any access beyond the size of the
external serial flash memory provides undefined results.
For details concerning the read process, see Flash memory read .

---

*Page 3249*

Quad Serial Peripheral Interface (QuadSPI)

#### 74.12.3 ARDB register descriptions

NOTE
See the system memory map in this document for the base address of the QuadSPI AHB RX data buffer.
74.12.3.1 ARDB memory map
QuadSPI_ARDB base address: 6800_0000h
Offset Register Access Reset value
Width
(In bits)
0h - 1FCh AHB RX Data Buffer Register (ARDB0 - ARDB127) 32 R 0000_0000h
74.12.3.2 AHB RX Data Buffer Register (ARDB0 - ARDB127)
Offset
For a = 0 to 127:
Register Offset
ARDBa 0h + (a × 4h)
Function
This register is used to read the buffer content of the RX buffer from successive addresses. ARDB0 corresponds to the RX buffer
register entry corresponding to the current value of the read pointer in an increasing order.
The increment of the read pointer depends on the access scheme (DMA or flag-driven). See Flash memory read , RX buffer,
data read through register interface, and AHB read for the description of successive accesses to the RX buffer content. See Byte
Ordering of Serial Flash Memory Read Data for the byte ordering scheme.
Valid address range accessible in the ARDB n range depends on the number of RX buffer entries implemented and on the number
of valid buffer entries available in the RX buffer.
• Example 1 - RX buffer filled completely with 128 words: In this case, the address range for valid read access extends from
ARDB0 to ARDB127 .
• Example 2 - RX buffer filled with five valid words; RX buffer fill level RBSR[RDBFL] is 5. In this case, an access to ARDB4
provides the last valid entry.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R ARXD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R ARXD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 3250*

Quad Serial Peripheral Interface (QuadSPI)
Fields
Field Function
31-0 ARDB provided RX buffer data
ARXD Byte order (endianness) is identical to the RX buffer data registers.

#### 74.13 Glossary

AHB Advanced high-performance bus, a version of AMBA
AMBA Advanced microcontroller bus architecture
APB Advanced peripheral bus
BE Big endian byte ordering
CRS Center aligned read strobe
CS Chip select
FRAD Flash region access descriptor
I/O Input output, I/O lines are also referred to as pads in this chapter
IFM Individual flash memory mode
LE Little endian
MDAD Master domain access descriptors
MGID Master-Group identifier
PCS Peripheral chip select
SCK Serial communications clock
SFM Serial flash memory

---

*Page 3251*

