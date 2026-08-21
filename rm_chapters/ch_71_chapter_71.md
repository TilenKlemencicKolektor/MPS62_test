<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 71 -->

# Chapter 71

# Synchronous Audio Interface (SAI)

#### 71.1 Chip-specific SAI information

#### 71.1.1 SAI instances and configuration

Table 471. SAI instances
Instance MCXE31B MCXE315/
MCXE316/MCXE317
SAI_0 Yes No
SAI_1 Yes No
Table 472. SAI configuration
Chip Instances Synchrono DMA Data Rate/ No. of Frame Master FIFO No. of
us Mode support channel Channels Synchroni Clock size(TX/R words/
zation Frequency X)/ frame
Channel
MCXE31B SAI_0 No Yes 12.288 4 I2S, AC97, 24.576 8X32 bit 8-16 words
Mbps and external
CODEC/D audio
SP
interfaces
SAI_1 No Yes 12.288 1 I2S, AC97, 24.576 8x32bit 8-16 words
Mbps and external
CODEC/D audio
SP
interfaces
NOTE
The SAI_BCLK (SAI_TX_BCLK, SAI_RX_BCLK), SAI_SYNC (SAI_TX_SYNC, SAI_RX_SYNC), and SAI_DATA
(SAI_TX_DATA, SAI_RX_DATA) pins are shared between each module instance's (SAI_0, SAI_1) receiver and
transmitter. See the IOMUX file attached to this document for details. At any specific time, either the SAI receiver
or the SAI transmitter should be active or they should operate synchronously (only one active at a time). The SAI
transmitter and SAI receiver should not be configured to operate simultaneously with inconsistent configurations.
NOTE
Timing specification of SAI master and SAI slave may limit maximum frequency.

#### 71.2 Overview

2
SAI provides an interface that supports full-duplex serial digital audio interfaces with frame synchronization formats such as I S,
AC97, TDM, and Codec/DSP interfaces.

---

*Page 2470*

Synchronous Audio Interface (SAI)

#### 71.2.1 Block diagram

Write Read
Shift
TDR n FIFO FIFO FIFO TX_DATA[ n ]
register
control control
Write Read
Shift
TDR0 FIFO FIFO FIFO TX_DATA[0]
register
control control
Bus Bit
TX_BCLK
clock clock
Frame
Control Bit clock
sync TX_SYNC
registers generation
control
Transmitter
MCLK Synchronous mode
Receiver
Frame
Control Bit clock
sync RX_SYNC
registers generation
control
Bus
Bit
clock RX_BCLK
clock
Read Write
Shift
RDR0 FIFO FIFO FIFO RX_DATA[0]
register
control control
Read Write
Shift
RDR n FIFO FIFO FIFO RX_DATA[ n ]
register
control control
n = Number of channels
Figure 385. Block diagram

#### 71.2.2 Features

Features can vary among chips and among SAI modules on the same chip. See the chip-specific SAI information at the beginning
of this chapter.
• Transmitter with independent bit clock and frame synchronization supporting 4 data line s
• Receiver with independent bit clock and frame synchronization supporting 4 data line s
• Maximum frame size of 16 words per data line
• Word length of 8 to 32 bits
• Word length configured separately for the first word and remaining words in a frame
• Asynchronous 8 × 32-bit FIFO for each transmit and receive data line supports:
— Graceful restart after FIFO error.
— Automatic restart after FIFO error without software intervention.
— 8-bit and 16-bit data packing into each 32-bit FIFO word.

---

*Page 2471*

Synchronous Audio Interface (SAI)
— Multiple-data-line FIFOs combining into single-data-line FIFO.

#### 71.3 Functional description

This section provides a complete functional description of SAI.

#### 71.3.1 Modes of operation

• Run mode
• Debug mode
71.3.1.1 Run mode
In Run mode, the SAI transmitter and receiver operate normally.
71.3.1.2 Debug mode
You can configure either or both the SAI transmitter and receiver to continue operating in Debug mode:
• Write 1 to TCSR[DBGE] to configure the transmitter to run in Debug mode.
• Write 1 to RCSR[DBGE] to configure the receiver to run in Debug mode.
When TCSR[DBGE] and RCSR[DBGE] are 0 and the chip enters Debug mode, SAI is disabled after completing the current
transmit or receive frame. Debug mode does not affect the transmitter and receiver bit clocks.

#### 71.3.2 Synchronous modes

The SAI transmitter and receiver can operate synchronously to each other. You can configure the SAI transmitter and receiver to
operate with a synchronous bit clock and frame sync (see TCR2[SYNC] and RCR2[SYNC] ).
If both the transmitter and receiver use the transmitter bit clock and frame sync:
• Configure the transmitter for asynchronous operation and the receiver for synchronous operation.
• Enable the receiver in Synchronous mode only after configuring both the transmitter and receiver.
• Enable the transmitter last and disable the transmitter first.
If both the transmitter and receiver use the receiver bit clock and frame sync:
• Configure the receiver for asynchronous operation and the transmitter for synchronous operation.
• Enable the transmitter in Synchronous mode only after configuring both the receiver and transmitter.
• Enable the receiver last and disable the receiver first.
When operating in Synchronous mode, the transmitter and receiver share only the bit clock, frame sync, and transmitter and
receiver enable. Otherwise, the transmitter and receiver operate independently, although the configuration register settings must
be consistent across both the transmitter and receiver.

#### 71.3.3 Frame sync configuration

When enabled, SAI continuously transmits and receives frames of data. Each frame consists of a fixed number of words, with each
word consisting of a fixed number of bits. Within each frame, you can mask any word, causing the receiver to ignore that word and
the transmitter to 3-state during that word.
The frame sync signal indicates the start of a frame. A valid frame sync requires the detection of a rising edge (if active high)
or falling edge (if active low). The transmitter or receiver cannot be busy with a previous frame. SAI ignores a valid frame sync
(in Target mode) or does not generate one (in Controller mode) for the first four bit-clock cycles after enabling the transmitter
or receiver.
You can configure the transmitter and receiver frame sync independently by using any of the following options:

---

*Page 2472*

Synchronous Audio Interface (SAI)
• Generate externally or internally
• Configure to be active high or active low
• Assert with the first bit in the frame or one bit early
• Assert for a duration between one bit-clock cycle and the first word length
• Configure the frame length from 1 to 16 words
• Configure the word length from 8 to 32 bits, with separate configurations for the length of the first word and that of the
remaining words
• Transmit or receive words MSB first or LSB first
You cannot change these configuration options after enabling the SAI transmitter or receiver.

#### 71.3.4 Data FIFO

Each transmit and receive channel includes a 8 × 32-bit FIFO. Use Transmit Data (TDR0 - TDR3) and Receive Data (RDR0 -
RDR3) to access FIFO data.
71.3.4.1 Data alignment
Data in the FIFO can be aligned anywhere within the 32-bit-wide register via the First Bit Shifted (FBT) configuration field. This
field selects the bit index (between 31 and 0) of the first bit shifted.
Examples of supported data alignment and the required FBT configuration are shown in Figure 386 for LSB-first configurations
and Figure 387 for MSB-first configurations.
How data is written to the FIFO, using FBT
FBT values LSB first
for
LSB first 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
00000 DATA[31:0]
00000 DATA[23:0]
01000 DATA[23:0]
00000 DATA[19:0]
01100 DATA[19:0]
00000 DATA[15:0]
10000 DATA[15:0]
00000 DATA[11:0]
10100 DATA[11:0]
00000 DATA[7:0]
11000 DATA[7:0]
Number of bits being transferred
shown above:
FBT FBT
24-bit data 12-bit data
TCR5[FBT] Or RCR5[FBT] 20-bit data 8-bit data
16-bit data
TX Config 5 Register RX Config 5 Register
Figure 386. SAI first bit shifted, LSB-first

---

*Page 2473*

Synchronous Audio Interface (SAI)
How data is written to the FIFO, using FBT
FBT values MSB first
for
MSB first 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
11111 DATA[31:0]
10111 DATA[23:0]
11111 DATA[23:0]
10011 DATA[19:0]
11111 DATA[19:0]
01111 DATA[15:0]
11111 DATA[15:0]
01011 DATA[11:0]
11111 DATA[11:0]
00111 DATA[7:0]
11111 DATA[7:0]
Number of bits being transferred
shown above:
FBT FBT
24-bit data 12-bit data
TCR5[FBT] Or RCR5[FBT] 20-bit data 8-bit data
16-bit data
TX Config 5 Register RX Config 5 Register
Figure 387. SAI first bit shifted, MSB-first
71.3.4.2 FIFO pointers
When writing to one of the Transmit Data (TDR0 - TDR3) registers, the write FIFO pointer of the corresponding Transmit FIFO
register ( TFR n [WFP] ) increments after each valid write. SAI supports 8-bit, 16-bit, and 32-bit writes to TDR n , and the FIFO pointer
increments after each individual write. Use only 8-bit writes when transmitting up to 8-bit data, and use only 16-bit writes when
transmitting up to 16-bit data:
• If the transmit FIFO is full, SAI ignores writes to TDR n .
• If the transmit FIFO is empty, write to TDR n at least three bit-clock cycles before the start of the next unmasked word. This
write avoids a FIFO underrun.
• Before enabling the transmitter, initialize the transmit FIFO with data. The transmitter starts a new frame after enabling the
transmitter. If no data is in the FIFO, the transmitter immediately generates an error.
When reading one of the Receive Data (RDR0 - RDR3) registers, the read FIFO pointer of the corresponding Receive FIFO
register ( RFR n [RFP] ) increments after each valid read. SAI supports 8-bit, 16-bit, and 32-bit reads from RDR n , and the FIFO
pointer increments after each individual read. Use only 8-bit reads when receiving up to 8-bit data, and use only 16-bit reads when
receiving up to 16-bit data:
• If the receive FIFO is empty, SAI ignores reads to RDR n .
• If the receive FIFO is full, read RDR n at least three bit-clock cycles before the end of an unmasked word. This read avoids
a FIFO overrun.
71.3.4.3 FIFO packing
Using FIFO packing, you can store multiple 8-bit or 16-bit data words in one 32-bit FIFO word for the transmitter or receiver. You
could emulate this feature by adjusting the number of bits per word and number of words per frame. FIFO packing, however, does
not require even multiples of words per frame, and it fully supports word masking.

---

*Page 2474*

Synchronous Audio Interface (SAI)
When FIFO packing is enabled, the FIFO pointers only increment when the full 32-bit FIFO word has been written (transmit) or
read (receive). In this way, FIFO packing supports scenarios where different words within each frame are stored in different areas
of memory.
Using 16-bit FIFO packing for transmitting, the transmit shift register loads at the start of each frame and after every second
unmasked transmit word. The first transmitted word is taken from the 16-bit word at byte offset 0h. (The first bit is selected by
TCR5[FBT] , and you must configure it within this 16-bit word.) The second transmitted word is taken from the 16-bit word at byte
offset 2h. (The first bit is selected by TCR5[FBT][3:0].) After the 16-bit word has been transmitted, the transmitter transmits logic
zeroes until the start of the next word.
Using 16-bit FIFO packing for receiving, the receive shift register is stored after every second unmasked received word. If there
is an odd number of unmasked-received words in each frame, the receive register is also stored, at the end of each frame. The
first received word is stored in the 16-bit word at byte offset 0h. (The first bit is selected by RCR5[FBT] , and you must configure
it within this 16-bit word.) The second received word is stored in the 16-bit word at byte offset 2h. (The first bit is selected by
RCR5[FBT][3:0].) The receiver ignores received data until the start of the next word after the 16-bit word has been received.
8-bit FIFO packing is similar to 16-bit packing, except four words are loaded or stored into each 32-bit FIFO word. The first word is
stored in byte offset 0h, the second word in byte offset 1h, and so on. You must configure TCR5[FBT], RCR5[FBT], or both within
byte offset 0h.
71.3.4.4 FIFO Combining mode
This mode enables separate FIFOs for multiple data channels to be used as a single FIFO for software accesses, as a single
data channel, or both. The enabled data channels must be contiguous and data channel 0 must be enabled when using FIFO
Combining mode.
You can combine FIFOs for software access by writing to transmit FIFO registers and reading from receive FIFO registers. Doing
so enables a DMA controller or software to read or write multiple FIFOs without incrementing the address that is accessed. After
FIFO Combining mode is enabled ( TCR4[FCOMB] > 00b), the first software access to a FIFO register accesses the first enabled
channel FIFO. The second access to a FIFO register accesses the second enabled channel FIFO. This process continues until
software accesses the last enabled channel FIFO and the pointer resets to the first enabled channel FIFO. To reset the pointer
manually, you can reset the FIFOs or disable FIFO combining on software accesses.
Combining FIFOs for transmit data channels enables one data channel to use the FIFOs of all enabled channel FIFOs. In this case,
the data output is identical on each enabled data channel. The transmit shift registers for all enabled data channels load at the start
of each frame. The registers also load upon every n th unmasked word, where n is the number of enabled data channels. The first
word transmitted loads from the first enabled channel FIFO; the second word transmitted loads from the second enabled channel
FIFO. This loading continues until the end of the frame.
Combining FIFOs for receive data channels enables one data channel to use the FIFOs of all enabled channel FIFOs. In this case,
the received data from channel 0 is stored in each enabled data channel. The receive shift registers for all enabled data channels
are stored after every n th unmasked word, where n is the number of enabled data channels. The first word received is stored in
the first enabled channel FIFO; the second word received is stored in the second enabled channel FIFO. This storage continues
until the end of the frame.
NOTE
The first word in each frame is always stored in the first enabled data channel. NXP recommends that the number
of unmasked words in each frame be evenly divisible by the number of enabled data channels.
Combining FIFOs for data channels loads or stores each channel FIFO at the same time. As a result, FIFO error conditions are
only checked every n th word, where n is the number of enabled data channels. If any enabled data channel meets the warning
flag or request flag conditions, SAI asserts the FIFO warning and request flags.

#### 71.3.5 Word mask register

The SAI transmitter and receiver each have a word mask register ( Transmit Mask (TMR) and Receive Mask (RMR) ) that you can
use to mask any word in the frame. The word mask register is double buffered. You can update it before the end of each frame
to mask a particular word in the next frame.

---

*Page 2475*

Synchronous Audio Interface (SAI)
TMR causes the transmit data pin to be 3-stated for the length of each selected word; the transmit FIFO is not read for
masked words.
RMR causes the received data for each selected word to be discarded and not written to the receive FIFO.

#### 71.3.6 Clocking

• Audio clock
• Bit clock
• Bus clock
71.3.6.1 Audio clock
The audio clock (MCLK) generates the bit clock when you configure the receiver or transmitter for an internally generated bit clock.
The transmitter and receiver can independently select between the bus clock and up to three audio clocks to generate the bit clock.
The audio clock generation and selection is chip-specific. See chip-specific clocking information about how the audio clocks
are generated.
71.3.6.2 Bit clock
The SAI transmitter and receiver support asynchronous free-running bit clocks that an audio clock can generate internally or
an external source can supply. SAI can also have synchronous bit clock and frame sync operation between the receiver and
transmitter, or between multiple SAI peripherals. The transmitter and receiver configuration affects the bit clock and frame sync
in the following ways:
• If you configure both the transmitter and receiver for asynchronous operation, each uses its own bit clock and frame sync.
• If you configure the transmitter for asynchronous operation and the receiver for synchronous operation, both use the
transmitter bit clock and frame sync.
• If you configure the receiver for asynchronous operation and the transmitter for synchronous operation, both use the
receiver bit clock and frame sync.
Your choice of synchronous or asynchronous operation selects the bit clock and frame sync used.
Externally generated bit clocks must be:
• Enabled before the SAI transmitter or receiver is enabled.
• Disabled after the SAI transmitter or receiver is disabled and completes its current frames.
In asynchronous operation, a SAI transmitter or receiver can use a bit clock externally generated by a SAI module instance
that is disabled in Stop mode. In this case, disable the transmitter or receiver before entering Stop mode. This issue does not
apply when the transmitter or receiver is in synchronous operation because all synchronous SAI modules are enabled and
disabled simultaneously.
71.3.6.3 Bus clock
The control and configuration registers use the bus clock to generate synchronous interrupts and DMA requests.
NOTE
Although no minimum bus clock frequency is specified, the frequency must be fast enough (relative to the bit clock
frequency) to serve the FIFOs. You must meet this requirement without generating a transmitter FIFO underrun or
receiver FIFO overflow condition.

#### 71.3.7 Reset

SAI is asynchronously reset on system reset. SAI has a Software reset and a FIFO reset .

---

*Page 2476*

Synchronous Audio Interface (SAI)
71.3.7.1 Software reset
The SAI transmitter includes a software reset that resets all transmitter internal logic, including the bit clock generation, status
flags, and FIFO pointers. The SAI receiver includes a software reset that resets all receiver internal logic, including bit clock
generation, status flags, and FIFO pointers.
These software resets do not reset the configuration registers. The software resets remain asserted until you clear them
via software.
71.3.7.2 FIFO reset
The SAI transmitter includes a FIFO reset that synchronizes the FIFO write pointer to the value of the FIFO read pointer. This
FIFO reset empties the FIFO contents. Use this reset after TCSR[FEF] becomes 1, and before SAI reinitializes the FIFO and
TCSR[FEF] becomes 0. SAI asserts the FIFO reset for one cycle only.
The SAI transmitter can also reset the FIFO of individual data channels by writing 1 to the appropriate bit in TCR3[CFR] . Use this
reset only when the corresponding bit in TCR3[TCE] is 0.
The SAI receiver includes a FIFO reset that synchronizes the FIFO read pointer to the value of the FIFO write pointer. This FIFO
reset empties the FIFO contents. Use this reset after RCSR[FEF] becomes 1 and SAI reads any remaining data from the FIFO,
and before RCSR[FEF] becomes 0. SAI asserts the FIFO reset for one cycle only.
The SAI receiver can also reset the FIFO of individual data channels by writing 1 to the appropriate bit in RCR3[RCE] . Use this
reset only when the corresponding bit in RCR3[RCE] is 0.

#### 71.3.8 Interrupts and DMA requests

In SAI, the transmitter and receiver generate separate interrupts and DMA requests, but use the same status flags. SAI only
generates asynchronous interrupts when the system clock is gated but the corresponding BCLK signal is active.
71.3.8.1 FIFO request flag
SAI sets the transmit FIFO request flag ( TCSR[FRF] ) when the number of entries in any enabled transmit FIFO is less than or
equal to the transmit FIFO watermark configuration ( TCR1[TFW] ). SAI clears this flag when the number of entries in each enabled
transmit FIFO is greater than TCR1[TFW].
SAI sets the receive FIFO request flag ( RCSR[FRF] ) when the number of entries in any enabled receive FIFO is greater than the
receive FIFO watermark configuration ( RCR1[RFW] ). SAI clears this flag when the number of entries in each enabled receive
FIFO is less than or equal to RCR1[RFW].
The FIFO request flag can generate an interrupt when TCSR[FRIE] = 1. It can generate a DMA request when TCSR[FRDE] = 1.
71.3.8.2 FIFO warning flag
SAI sets the transmit FIFO warning flag ( TCSR[FWF] ) when the number of entries in any of the enabled transmit FIFOs is empty.
SAI clears this flag when the number of entries in each enabled transmit FIFO is not empty.
SAI sets the receive FIFO warning flag ( RCSR[FWF] ) when the number of entries in any of the enabled receive FIFOs is full. SAI
clears this flag when the number of entries in each enabled receive FIFO is not full.
The FIFO warning flag can generate an interrupt when TCSR[FWIE] = 1. The flag can generate a DMA request when
TCSR[FWDE] = 1.
71.3.8.3 FIFO error flag
SAI sets the transmit FIFO error flag ( TCSR[FEF] ) when any enabled transmit FIFOs underrun. After the error flag is set, all
enabled transmit channels transmit zero data before SAI clears TCSR[FEF].
When you write 1 to TCR4[FCONT] , the FIFO continues transmitting data following an underrun without software intervention. To
ensure that data transmits in the correct order, the transmitter continues from the same word number in the frame that caused
the FIFO to underrun. It continues only after new data is written to the transmit FIFO. In this case, clear TCSR[FEF] without
reinitializing the transmit FIFOs.

---

*Page 2477*

Synchronous Audio Interface (SAI)
SAI sets the receive FIFO error flag ( RCSR[FEF] ) when any enabled receive FIFO overflows. After the flag is set, all enabled
receive channels discard their received data until SAI clears RCSR[FEF] and the next receive frame starts. Empty all enabled
receive FIFOs before SAI clears RCSR[FEF].
When you write 1 to RCR4[FCONT] , the FIFO continues receiving data following an overflow without software intervention. To
ensure that data is received in the correct order, the receiver continues from the same word number in the frame that caused the
FIFO to overflow. It continues only after data has been read from the receive FIFO. In this case, clear RCSR[FEF] without emptying
the receive FIFOs.
TCSR[FEF] and RSCR[FEF] can only generate an interrupt.
71.3.8.4 Sync error flag
SAI sets a sync error flag ( TCSR[SEF] or RCSR[SEF] ) when both of these conditions are true:
• The frame sync is generated externally.
• The external frame sync asserts when the transmitter or receiver is busy with the previous frame.
SAI ignores the external frame sync assertion and sets the sync error flag. When this flag is set, the transmitter or receiver
continues checking for frame sync assertion when idle or at the end of each frame.
TCSR[SEF] and RCSR[SEF] can only generate an interrupt.
71.3.8.5 Word start flag
SAI sets the word start flag ( TCSR[WSF] ) at the start of the second bit-clock cycle for the selected word. You can select this word
via TCR3[WDFL] .
TCSR[WSF] can only generate an interrupt.

#### 71.4 External signals

Name Function I/O
TX_BCLK Transmit bit clock: the bit clock is an input I/O
when externally generated and an output when
internally generated.
TX_SYNC Transmit frame sync: the frame sync is an input I/O
sampled synchronously by the bit clock when
externally generated. It is an output generated
synchronously by the bit clock when internally
generated.
TX_DATA [3:0] Transmit data: the bit clock synchronously O
generates the transmit data; this signal is 3-
stated when not transmitting a word.
RX_BCLK Receive bit clock: the bit clock is an input I/O
when externally generated and an output when
internally generated.
RX_SYNC Receive frame sync: the frame sync is an input I/O
sampled synchronously by the bit clock when
externally generated. It is an output generated
synchronously by the bit clock when internally
generated.
Table continues on the next page...

---

*Page 2478*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Name Function I/O
RX_DATA [3:0] Receive data: the bit clock synchronously I
samples the receive data.

#### 71.5 Initialization

To initialize SAI:
1. Enable the clock to SAI.
2. Reset the internal transmitter logic and receiver logic by writing 1 to TCSR[SR] and RCSR[SR] , respectively.

#### 71.6 Memory map and register definition

A read or write access to an address from offset 100h and above results in a bus error.

#### 71.6.1 SAI register descriptions

SAI provides an interface that supports full-duplex serial digital audio interfaces with frame synchronization formats.
71.6.1.1 SAI memory map
SAI_0 base address: 4036_C000h
SAI_1 base address: 404D_C000h
Offset Register Access Reset value
Width
(In bits)
0h Version ID (VERID) 32 R 0301_0000h
4h Parameter (PARAM) 32 R See section
8h Transmit Control (TCSR) 32 RW 0000_0000h
Ch Transmit Configuration 1 (TCR1) 32 RW 0000_0000h
10h Transmit Configuration 2 (TCR2) 32 RW 0000_0000h
14h Transmit Configuration 3 (TCR3) 32 RW 0000_0000h
18h Transmit Configuration 4 (TCR4) 32 RW 0000_0000h
1Ch Transmit Configuration 5 (TCR5) 32 RW 0000_0000h
20h - 2Ch Transmit Data (TDR0 - TDR3) 32 RW See section
40h - 4Ch Transmit FIFO (TFR0 - TFR3) 32 R See section
60h Transmit Mask (TMR) 32 RW 0000_0000h
88h Receive Control (RCSR) 32 RW 0000_0000h
8Ch Receive Configuration 1 (RCR1) 32 RW 0000_0000h
90h Receive Configuration 2 (RCR2) 32 RW 0000_0000h
94h Receive Configuration 3 (RCR3) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 2479*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
98h Receive Configuration 4 (RCR4) 32 RW 0000_0000h
9Ch Receive Configuration 5 (RCR5) 32 RW 0000_0000h
A0h - ACh Receive Data (RDR0 - RDR3) 32 R See section
C0h - CCh Receive FIFO (RFR0 - RFR3) 32 R See section
E0h Receive Mask (RMR) 32 RW 0000_0000h
71.6.1.2 Version ID (VERID)
Offset
Register Offset
VERID 0h
Function
Contains version numbers for the module design and feature set.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R MAJOR MINOR
W
Reset 0 0 0 0 0 0 1 1 0 0 0 0 0 0 0 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R FEATURE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 Major Version Number
MAJOR Indicates the major version number for the specification.
23-16 Minor Version Number
MINOR Indicates the minor version number for the specification.
Table continues on the next page...

---

*Page 2480*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
15-0 Feature Specification Number
FEATURE Indicates the feature set number.
0000_0000_0000_0000b - Standard feature set
71.6.1.3 Parameter (PARAM)
Offset
Register Offset
PARAM 4h
Function
Contains parameter values implemented in the module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 FRAME
W
Reset See Register reset values .
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 FIFO 0 DATALINE
W
Reset See Register reset values .
Register reset values
Register Reset value
PARAM
SAI_0: 0004_0304h
SAI_1: 0004_0301h
Fields
Field Function
Reserved
31-20
—
Table continues on the next page...

---

*Page 2481*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
19-16 Frame Size
FRAME Describes the maximum number of slots per frame, which is 2^FRAME.
Reserved
15-12
—
11-8 FIFO Size
FIFO Describes the number of words in each FIFO, which is 2^FIFO.
Reserved
7-4
—
3-0 Number of Data Lines
DATALINE Contains the number of data lines implemented.
71.6.1.4 Transmit Control (TCSR)
Offset
Register Offset
TCSR 8h
Function
Contains transmitter enable fields including resets, error and interrupt enable fields, and error flag fields.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 WSF SEF FEF FWF FRF
Reserv
TE DBGE BCE SR
ed
W FR W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
WSIE SEIE FEIE FWIE FRIE FWDE FRDE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2482*

Synchronous Audio Interface (SAI)
Fields
Field Function
31 Transmitter Enable
TE Enables the transmitter. When you write 0 to this field, the transmitter remains enabled, and this field
remains 1 until the end of the current frame.
0b - Disable
1b - Enable (or transmitter has been disabled and has not yet reached the end of the frame)
30 Reserved
— Only write zero to this field.
29 Debug Enable
DBGE Enables transmitter operation in Debug mode, which does not affect the transmit bit clock. If you write 0
to this field, the transmitter operation is disabled after completing the current frame.
0b - Disable
1b - Enable
28 Bit Clock Enable
BCE Enables the transmit bit clock, separately from TCSR[TE] . This field automatically becomes 1 when
TCSR[TE] becomes 1. When you write 0 to this field, the transmit bit clock remains enabled, and the field
remains 1 until the end of the current frame.
0b - Disable
1b - Enable
Reserved
27-26
—
25 FIFO Reset
FR Empties the FIFO and sets the FIFO read and write pointers to the same value, which may or may not
be zero.
NOTE
The FIFO reset is asserted for one cycle only.
Reading this field always returns zero. SAI resets the FIFO pointers when the transmitter is disabled or the
FIFO error flag is set.
0b - No effect
1b - FIFO reset
24 Software Reset
SR Resets the internal transmitter logic, including the FIFO read and write pointers.
Software-visible registers are not affected, except for the status registers.
Table continues on the next page...

---

*Page 2483*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
NOTE
The software reset remains asserted until software writes 0 to the field.
0b - No effect
1b - Software reset
Reserved
23-21
—
20 Word Start Flag
WSF Indicates whether the start of the configured word has been detected.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not detected
1b - Detected
When writing
0b - No effect
1b - Clear the flag
19 Sync Error Flag
SEF Indicates whether an error in the externally generated frame sync has been detected.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not detected
1b - Detected
When writing
0b - No effect
1b - Clear the flag
18 FIFO Error Flag
FEF Indicates whether an enabled transmit FIFO has underrun.
NOTE
This field behaves differently for register reads and writes.
When reading
Table continues on the next page...

---

*Page 2484*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
0b - Not detected
1b - Detected
When writing
0b - No effect
1b - Clear the flag
17 FIFO Warning Flag
FWF Indicates whether an enabled transmit FIFO is empty.
0b - Not empty
1b - Empty
16 FIFO Request Flag
FRF Indicates whether the number of words in an enabled transmit channel FIFO is less than or equal to the
transmit FIFO watermark.
0b - Watermark not reached
1b - Watermark reached
Reserved
15-13
—
12 Word Start Interrupt Enable
WSIE Enables word start interrupts.
0b - Disable
1b - Enable
11 Sync Error Interrupt Enable
SEIE Enables sync error interrupts.
0b - Disable
1b - Enable
10 FIFO Error Interrupt Enable
FEIE Enables FIFO error interrupts.
0b - Disable
1b - Enable
9 FIFO Warning Interrupt Enable
FWIE Enables FIFO warning interrupts.
0b - Disable
Table continues on the next page...

---

*Page 2485*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
1b - Enable
8 FIFO Request Interrupt Enable
FRIE Enables FIFO request interrupts.
0b - Disable
1b - Enable
Reserved
7-5
—
Reserved
4-2
—
1 FIFO Warning DMA Enable
FWDE Enables the DMA warning.
0b - Disable
1b - Enable
0 FIFO Request DMA Enable
FRDE Enables DMA requests.
0b - Disable
1b - Enable
71.6.1.5 Transmit Configuration 1 (TCR1)
Offset
Register Offset
TCR1 Ch
Function
Configures the watermark level for all enabled transmit channels.

---

*Page 2486*

Synchronous Audio Interface (SAI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
TFW
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-3
—
2-0 Transmit FIFO Watermark
TFW Indicates the number of 32-bit FIFO words in the watermark level of the transmit FIFO.
000b - 0
001b - 1
010b-110b - (TFW)
111b - 7
71.6.1.6 Transmit Configuration 2 (TCR2)
Offset
Register Offset
TCR2 10h
Function
Contains the SYNC mode and clock setting fields.
NOTE
Do not alter this register when TCSR[TE] is 1.

---

*Page 2487*

Synchronous Audio Interface (SAI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
Reserv
R 0 0
ed
SYNC BCI MSEL BCP BCD BYP
W 0
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
DIV
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
30 Synchronous Mode
SYNC Configures Asynchronous and Synchronous modes of operation. When configured for Synchronous
mode, you must configure the receiver for asynchronous operations.
0b - Asynchronous mode
1b - Synchronous with receiver
Reserved
29
—
28 Bit Clock Input
BCI Enables the internal logic to be clocked as if the bit clock is generated externally.
When this field is 1 while using an internally generated bit clock in Synchronous or Asynchronous mode, the
bit clock used by the transmitter is delayed by the pad output delay. (The pad input clocks the transmitter
as if the clock is externally generated.) This setting decreases the data input setup time, but increases the
data output valid time.
Use the Target mode timing from the data sheet for the transmitter when this field is 1. In Synchronous
mode, this field allows the transmitter to use the Target mode timing from the data sheet, while the receiver
uses the Controller mode timing. This field has no effect when configured for an externally generated
bit clock.
NOTE
When BCI = 1, both the input buffer and output buffer must be enabled for the BCLK pad.
0b - Disable
1b - Enable
27-26 MCLK Select
Table continues on the next page...

---

*Page 2488*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
Selects the audio clock option used to generate an internally generated bit clock. This field has no effect
MSEL
when SAI is configured for an externally generated bit clock.
NOTE
Depending on the chip, some audio clock options might not be available. See the chip-
specific information for the meaning of each option.
00b - Bus clock
01b - Audio clock (MCLK) option 1
10b - Audio clock (MCLK) option 2
11b - Audio clock (MCLK) option 3
25 Bit Clock Polarity
BCP Configures the polarity of the bit clock. If you write 0 to this field, the bit clock becomes active high with
drive outputs on rising edge and sample inputs on falling edge. If you write 1 to this field, the bit clock
becomes active low with drive outputs on falling edge and sample inputs on rising edge.
0b - Active high
1b - Active low
24 Bit Clock Direction
BCD Configures the direction of the bit clock.
0b - Generate externally in Target mode
1b - Generate internally in Controller mode
23 Bit Clock Bypass
BYP Bypasses the bit clock divider. When bypassed, the internal bit clock is divide-by-one of the audio clock.
When not bypassed, the internal bit clock is generated from the bit clock divider.
0b - Disable
1b - Enable
Reserved
22-8
—
7-0 Bit Clock Divide
DIV Determines the value by which SAI divides down the audio clock to generate the bit clock (when
configured for an internal bit clock). The division value is (DIV + 1) × 2.

---

*Page 2489*

Synchronous Audio Interface (SAI)
71.6.1.7 Transmit Configuration 3 (TCR3)
Offset
Register Offset
TCR3 14h
Function
Contains the transmit channel settings.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
TCE
W CFR
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
WDFL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-28
—
27-24 Channel FIFO Reset
CFR Resets the FIFO pointers for a specific channel. Reading this field always returns zero. You must reset FIFO
pointers only when a channel is disabled or the FIFO error flag is set.
The width of this field = the number of transmit channels (call it n ). For example, if this field is 2 bits wide, bit
position 24 refers to the transmit channel 1 FIFO pointer and bit position 25 refers to the transmit channel
2 FIFO pointer. Writing 1 to bit 24 resets the transmit channel 1 FIFO pointer, and writing 1 to bit 25 resets
the transmit channel 2 FIFO pointer. Writing 1 to bit n resets the transmit channel n FIFO pointer.
NOTE
When there is only one channel, you do not need the individual channel FIFO reset
( TCR3[CFR] ). For a single channel, use the global FIFO reset ( TCSR[FR] ).
• 0b – No effect
• 1b – Reset transmit data channel n FIFO

---

*Page 2490*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
SAI_0 TCR3 —
SAI_1 — TCR3
Reserved
23-20
—
19-16 Transmit Channel Enable
TCE Enables the corresponding data channel for transmit operations. Changing this field takes effect
immediately for generating the FIFO request and warning flags. It takes effect at the end of each frame for
transmit operations.
The width of this field = the number of transmit channels (call it n ). For example, if this field is two bits wide,
bit position 16 refers to transmit channel 1 and bit position 17 refers to transmit channel 2. Writing 1 to bit 16
enables transmit channel 1, and setting bit 17 enables transmit channel 2. Writing 1 to bit n enables transmit
channel n . For each transmit data channel:
• 0b – Disable
• 1b – Enable
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
SAI_0 TCR3 —
SAI_1 TCR3[16] TCR3[19–17]
Reserved
15-4
—
3-0 Word Flag Configuration
WDFL Selects the word that sets the word start flag ( TCSR[WSF] ). The value must be one less than the word
number. For example, writing 0 selects the first word in the frame. When you configure this field with a value
greater than TCR4[FRSZ] , TCSR[WSF] is never set.

---

*Page 2491*

Synchronous Audio Interface (SAI)
71.6.1.8 Transmit Configuration 4 (TCR4)
Offset
Register Offset
TCR4 18h
Function
Contains the transmit fields for FIFO Combine mode, FIFO Packing mode, and frame sync settings.
NOTE
Do not alter this register when TCSR[TE] is 1.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
FCON
FCOMB FPACK FRSZ
T
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
CHMO ONDE
SYWD MF FSE FSP FSD
D M
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-29
—
28 FIFO Continue on Error
FCONT Configures when SAI must continue transmitting after a FIFO error is detected. When this field is 0 and a
FIFO error occurs, SAI continues from the start of the next frame after the FIFO error flag clears. When
this field is 1 and a FIFO error occurs, SAI continues from the same word that caused the FIFO error
after the FIFO warning flag clears.
0b - Continue from the start of the next frame
1b - Continue from the same word that caused the FIFO error
27-26 FIFO Combine Mode
FCOMB Enables FIFO Combine mode for specified operations.
When FIFO Combine mode is enabled for FIFO writes, software writing to any FIFO data register alternates
the write among the enabled data channel FIFOs. For example, if two data channels are enabled:

---

*Page 2492*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
• The first write is performed to the first enabled data channel FIFO.
• The second write is performed to the second enabled data channel FIFO.
Resetting the FIFO or disabling FIFO Combine mode for FIFO writes resets the pointer back to the first
enabled data channel.
When FIFO Combine mode is enabled for FIFO reads from the transmit shift registers, the transmit data
channel output alternates between the enabled data channel FIFOs. For example, if two data channels
are enabled:
• The first unmasked word is transmitted from the first enabled data channel FIFO.
• The second unmasked word is transmitted from the second enabled data channel FIFO.
Because the first word of the frame is always transmitted from the first enabled data channel FIFO, NXP
recommends that the number of unmasked words per frame is evenly divisible by the number of enabled
data channels.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
SAI_0 TCR4 —
SAI_1 — TCR4
00b - Disable
01b - Enable on FIFO reads (from transmit shift registers)
10b - Enable on FIFO writes (by software)
11b - Enable on FIFO reads (from transmit shift registers) and writes (by software)
25-24 FIFO Packing Mode
FPACK Enables packing of 8-bit data or 16-bit data into each 32-bit FIFO word. If the word size is greater than 8 bits
or 16 bits, only the first 8 bits or 16 bits are loaded from the FIFO. The first word in each frame always starts
with a new 32-bit FIFO word and the first bit shifted must be configured within the first packed word. When
FIFO packing is enabled, the FIFO write pointer only increments when you write the full 32-bit FIFO word.
00b - Disable FIFO packing
01b - Reserved
10b - Enable 8-bit FIFO packing
11b - Enable 16-bit FIFO packing
Reserved
23-20
—
Table continues on the next page...

---

*Page 2493*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
19-16 Frame Size
FRSZ Configures the number of words in each frame. The value must be one less than the number of words in
the frame. For example, write 0 for one word per frame. The maximum supported frame size is 16 words.
Reserved
15-13
—
12-8 Sync Width
SYWD Configures the length of the frame sync in number of bit-clock cycles. The value must be one less than
the number of bit-clock cycles. For example, write 0 for the frame sync to assert for one bit-clock cycle
only. You cannot configure the sync width to be longer than the first word of the frame.
Reserved
7-6
—
5 Channel Mode
CHMOD Specifies the mode of transmit data pins. In TDM mode, transmit data pins are 3-stated when slots are
masked or channels are disabled. In Output mode, transmit data pins are never 3-stated, and they output
zero when slots are masked or channels are disabled.
0b - TDM mode
1b - Output mode
4 MSB First
MF Configures what is transmitted first: LSB or MSB.
0b - LSB
1b - MSB
3 Frame Sync Early
FSE Determines when frame sync is asserted.
0b - First bit of the frame
1b - One bit before the first bit of the frame
2 On-Demand Mode
ONDEM Determines when the internal frame sync is generated. When this field is 1, and the frame sync is
generated internally, a frame sync is only generated after the FIFO warning flag is cleared.
0b - Generated continuously
1b - Generated after the FIFO warning flag is cleared
1 Frame Sync Polarity
FSP Configures the polarity of the frame sync.
Table continues on the next page...

---

*Page 2494*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
0b - Active high
1b - Active low
0 Frame Sync Direction
FSD Configures the direction of the frame sync.
0b - Generated externally in Target mode
1b - Generated internally in Controller mode
71.6.1.9 Transmit Configuration 5 (TCR5)
Offset
Register Offset
TCR5 1Ch
Function
Contains transmit word width and bit index settings.
NOTE
Do not alter this register when TCSR[TE] is 1.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
WNW W0W
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
FBT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-29
—
Table continues on the next page...

---

*Page 2495*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
28-24 Word N Width
WNW Configures the number of bits in each word, except for the first one in the frame. The value written must be
one less than the number of bits per word. SAI does not support word widths of less than 8 bits.
SAI does not support bit settings 0–6 (0b–0_0110b).
0_0111b - 8
0_1000b - 9
0_1001b-1_1110b - (WNW value + 1)
1_1111b - 32
Reserved
23-21
—
20-16 Word 0 Width
W0W Configures the number of bits in the first word of each frame. The value written must be one less than the
number of bits in the first word. SAI does not support word widths of less than 8 bits when there is only one
word per frame.
SAI does not support bit settings 0–6 (0b–0_0110b).
0_0111b - 8
0_1000b - 9
0_1001b-1_1110b - (W0W value + 1)
1_1111b - 32
Reserved
15-13
—
12-8 First Bit Shifted
FBT Configures the bit index for the first bit transmitted for each word in the frame. If configured for MSB-first,
the index of the next bit transmitted is one less than the current bit transmitted. If configured for LSB-first,
the index of the next bit transmitted is one more than the current bit transmitted. The value written must be
greater than or equal to the word width when configured for MSB-first. The value written must be less than
or equal to 31-word width when configured for LSB-first.
See Data alignment for details.
0_0000b - 0
0_0001b-1_1110b - FBT
1_1111b - 31
Reserved
7-0
—

---

*Page 2496*

Synchronous Audio Interface (SAI)
71.6.1.10 Transmit Data (TDR0 - TDR3)
Offset
Register Offset
TDR0 20h
TDR1 24h
TDR2 28h
TDR3 2Ch
Function
Contains transmit data.
When the transmit FIFO is not full, writes to this register push the data written into the transmit data FIFO. When the transmit FIFO
is full, SAI ignores writes to this register.
NOTE
Each module instance supports a different number of registers.
Instance Register supported Register not supported
SAI_0 TDR0–TDR3 —
SAI_1 TDR0 TDR1–TDR3
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W TDR
Reset See Register reset values .
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W TDR
Reset See Register reset values .
Register reset values
Register Reset value
TDR0 SAI_0,SAI_1: 0000_0000h
TDR1–TDR3 0000_0000h

---

*Page 2497*

Synchronous Audio Interface (SAI)
Fields
Field Function
Transmit Data
31-0
TDR
71.6.1.11 Transmit FIFO (TFR0 - TFR3)
Offset
Register Offset
TFR0 40h
TFR1 44h
TFR2 48h
TFR3 4Ch
Function
Contains the transmit FIFO pointers. The MSB of the read and write pointers distinguishes between FIFO full and empty
conditions. If the read and write pointers are identical, the FIFO is empty. If the read and write pointers are identical except for
the MSB, the FIFO is full.
NOTE
Each module instance supports a different number of registers.
Instance Register supported Register not supported
SAI_0 TFR0–TFR3 —
SAI_1 TFR0 TFR1–TFR3
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R WCP 0 WFP
W
Reset See Register reset values .
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 RFP
W
Reset See Register reset values .

---

*Page 2498*

Synchronous Audio Interface (SAI)
Register reset values
Register Reset value
TFR0 SAI_0,SAI_1: 0000_0000h
TFR1–TFR3 0000_0000h
Fields
Field Function
31 Write Channel Pointer
WCP Indicates whether this data channel is the next FIFO to be written when FIFO Combine mode is enabled
for write operations.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
SAI_0 TFR0–TFR3 —
SAI_1 — TFR0
0b - No effect
1b - Next FIFO to be written
Reserved
30-20
—
19-16 Write FIFO Pointer
WFP Indicates the FIFO write pointer for the transmit data channel.
Reserved
15-4
—
3-0 Read FIFO Pointer
RFP Indicates the FIFO read pointer for the transmit data channel.
71.6.1.12 Transmit Mask (TMR)
Offset
Register Offset
TMR 60h

---

*Page 2499*

Synchronous Audio Interface (SAI)
Function
Contains the mask for the transmit word. This register is double-buffered and updates:
• When TCSR[TE] first becomes 1.
• At the end of each frame.
This setup allows the masked words in each frame to change from frame to frame.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TWM
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-16
—
15-0 Transmit Word Mask
TWM Configures whether the transmit word n is masked for the corresponding word in the frame. In this case,
being masked means that transmit data pins are 3-stated or driven zero and transmit data is not read
from the FIFO.
0000_0000_0000_0000b - Enable
0000_0000_0000_0001b - Mask
71.6.1.13 Receive Control (RCSR)
Offset
Register Offset
RCSR 88h
Function
Contains receiver enable fields including resets, error and interrupt enable fields, and error flag fields.

---

*Page 2500*

Synchronous Audio Interface (SAI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 WSF SEF FEF FWF FRF
Reserv
RE DBGE BCE SR
ed
W FR W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
WSIE SEIE FEIE FWIE FRIE FWDE FRDE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Receiver Enable
RE Enables the receiver. When you write 0 to this field, the receiver remains enabled and the field remains 1
until the end of the current frame.
0b - Disable
1b - Enable (or receiver disabled and not yet reached end of frame)
30 Reserved
— Only write zero to this field.
29 Debug Enable
DBGE Enables receiver operation in Debug mode, which does not affect the receive bit clock.
0b - Disable after completing the current frame
1b - Enable
28 Bit Clock Enable
BCE Enables the receive bit clock, separately from RCSR[RE] . This field becomes 1 automatically when
RCSR[RE] becomes 1. When you write 0 to this field, the receive bit clock remains enabled, and this
field remains 1 until the end of the current frame.
0b - Disable
1b - Enable
Reserved
27-26
—
25 FIFO Reset
FR Empties the FIFO, and sets the FIFO read and write pointers to the same value, which can be zero. Reading
this field always returns zero.
Table continues on the next page...

---

*Page 2501*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
NOTE
The FIFO reset is asserted for one cycle only.
You must reset FIFO pointers only when the receiver is disabled or the FIFO error flag is 1.
0b - No effect
1b - Reset
24 Software Reset
SR Resets the internal receiver logic including the FIFO pointers. Software-visible registers are not affected,
except for the status registers.
NOTE
The software reset remains asserted until you clear it.
0b - No effect
1b - Software reset
Reserved
23-21
—
20 Word Start Flag
WSF Indicates whether SAI has detected the start of the configured word.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not detected
1b - Detected
When writing
0b - No effect
1b - Clear the flag
19 Sync Error Flag
SEF Indicates whether SAI has detected an error in the externally generated frame sync.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not detected
1b - Detected
When writing
Table continues on the next page...

---

*Page 2502*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
0b - No effect
1b - Clear the flag
18 FIFO Error Flag
FEF Indicates whether an enabled receive FIFO has overflowed.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - No error
1b - Receive overflow detected
When writing
0b - No effect
1b - Clear the flag
17 FIFO Warning Flag
FWF Indicates whether an enabled receive FIFO is full.
0b - Not full
1b - Full
16 FIFO Request Flag
FRF Indicates whether the number of words in an enabled receive channel FIFO is greater than the receive
FIFO watermark.
0b - Watermark not reached
1b - Watermark reached
Reserved
15-13
—
12 Word Start Interrupt Enable
WSIE Enables word start interrupts.
0b - Disable
1b - Enable
11 Sync Error Interrupt Enable
SEIE Enables sync error interrupts.
0b - Disable
1b - Enable
Table continues on the next page...

---

*Page 2503*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
10 FIFO Error Interrupt Enable
FEIE Enables FIFO error interrupts.
0b - Disable
1b - Enable
9 FIFO Warning Interrupt Enable
FWIE Enables FIFO warning interrupts.
0b - Disable
1b - Enable
8 FIFO Request Interrupt Enable
FRIE Enables FIFO request interrupts.
0b - Disable
1b - Enable
Reserved
7-5
—
Reserved
4-2
—
1 FIFO Warning DMA Enable
FWDE Enables DMA warnings.
0b - Disable
1b - Enable
0 FIFO Request DMA Enable
FRDE Enables DMA requests.
0b - Disable
1b - Enable
71.6.1.14 Receive Configuration 1 (RCR1)
Offset
Register Offset
RCR1 8Ch
Function
Configures the watermark level for all enabled receiver channels.

---

*Page 2504*

Synchronous Audio Interface (SAI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
RFW
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-3
—
2-0 Receive FIFO Watermark
RFW Configures the level of the receive FIFO watermark, in 32-bit FIFO words.
000b - 1
001b - 2
010b-110b - (RFW value + 1)
111b - 8
71.6.1.15 Receive Configuration 2 (RCR2)
Offset
Register Offset
RCR2 90h
Function
Contains the SYNC mode and clock setting fields.
NOTE
Do not alter this register when RCSR[RE] is 1.

---

*Page 2505*

Synchronous Audio Interface (SAI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
Reserv
R 0 0
ed
SYNC BCI MSEL BCP BCD BYP
W 0
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
DIV
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
30 Synchronous Mode
SYNC Configures Asynchronous and Synchronous modes of operation. When configured for a Synchronous
mode of operation, you must configure the transmitter for asynchronous operation.
0b - Asynchronous mode
1b - Synchronous with transmitter
Reserved
29
—
28 Bit Clock Input
BCI Enables the internal logic to be clocked as if the bit clock is generated externally.
When this field is 1 and when using an internally generated bit clock in Synchronous or Asynchronous mode,
the bit clock used by the receiver is delayed by the pad output delay. (The pad input clocks the receiver as if
the clock is externally generated.) This setting decreases the data input setup time, but increases the data
output valid time.
Use the Target mode timing from the data sheet for the receiver when this field is 1. In Synchronous mode,
this field allows the receiver to use the Target mode timing from the data sheet, while the transmitter uses
the Controller mode timing. This field has no effect when configured for an externally generated bit clock.
NOTE
When this field is 1, both the input buffer and output buffer must be enabled for the
BCLK pad.
0b - Disable
1b - Enable
27-26 MCLK Select
Table continues on the next page...

---

*Page 2506*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
MSEL Selects the audio clock option used to generate an internally generated bit clock. This field has no effect
when configured for an externally generated bit clock.
NOTE
Some audio clock options are not available for some chips. See the chip-specific information
for the availability and chip-specific meaning of each option.
00b - Bus clock
01b - Audio clock (MCLK) option 1
10b - Audio clock (MCLK) option 2
11b - Audio clock (MCLK) option 3
25 Bit Clock Polarity
BCP Configures the polarity of the bit clock. If you write 0 to this field, the bit clock becomes active high with
drive outputs on the rising edge and sample inputs on the falling edge. If you write 1 to this field, the bit
clock becomes active low with drive outputs on the falling edge and sample inputs on the rising edge.
0b - Active high
1b - Active low
24 Bit Clock Direction
BCD Configures the direction of the bit clock.
0b - Generated externally in Target mode
1b - Generated internally in Controller mode
23 Bit Clock Bypass
BYP Enables the bypass of the bit clock divider. When enabled, the internal bit clock is divide-by-one of the audio
clock. When disabled, the internal bit clock is generated from the bit clock divider.
0b - Disable
1b - Enable
Reserved
22-8
—
7-0 Bit Clock Divide
DIV Determines the value by which the audio clock is divided to generate the bit clock, when configured for
an internal bit clock. The division value is (DIV + 1) × 2.

---

*Page 2507*

Synchronous Audio Interface (SAI)
71.6.1.16 Receive Configuration 3 (RCR3)
Offset
Register Offset
RCR3 94h
Function
Contains the receive channel settings.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
RCE
W CFR
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
WDFL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-28
—
27-24 Channel FIFO Reset
CFR Resets the FIFO pointers for a specific channel. Reading this field always returns zero. Reset FIFO pointers
only when a channel is disabled or the FIFO error flag is set.
The width of this field = the number of receive channels (call it n ). For example, if this field is two bits wide,
bit position 24 refers to the receive channel 1 FIFO pointer and bit position 25 refers to the receive channel
2 FIFO pointer. Writing 1 to bit 24 resets the receive channel 1 FIFO pointer, and writing 1 to bit 25 enables
the receive channel 2 FIFO pointer. Writing 1 to bit n resets receive channel n FIFO pointer.
• 0b – No effect
• 1b – Reset receive data channel n FIFO
NOTE
This field is not supported in every instance. The following table includes only
supported registers.

---

*Page 2508*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
SAI_0 RCR3 —
SAI_1 — RCR3
Reserved
23-20
—
19-16 Receive Channel Enable
RCE Enables the corresponding data channel for receive operation. Changing this field takes effect immediately
for generating the FIFO request and warning flags. It takes effect at the end of each frame for
receive operations.
The width of RCE = the number of receive channels (call it n ). For example, if RCE is two bits wide, then
bit position 16 refers to receive channel 1 and bit position 17 refers to receive channel 2. Writing 1 to bit
16 enables receive channel 1, and writing 1 to bit 17 enables receive channel 2. Writing 1 to bit n enables
receive channel n .
NOTE
When there is only a single channel, you do not need an individual channel FIFO reset
( RCR3[CFR] ) . In that case, use the global FIFO reset ( RCSR[FR] ).
• 0b – Disable receive data channel n
• 1b – Enable receive data channel n
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
SAI_0 RCR3 —
SAI_1 RCR3[16] RCR3[19–17]
Reserved
15-4
—
3-0 Word Flag Configuration
WDFL Configures which word sets the word start flag ( RCSR[WSF] ). The value must be one less than the word
number (for example, write zero to select the first word in the frame). If you configure this field to a value
greater than RCR4[FRSZ] , RCSR[WSF] is never set.
Table continues on the next page...

---

*Page 2509*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
0000b - Word 1
0001b - Word 2
0010b-1110b - Word (WDFL value + 1)
1111b - Word 16
71.6.1.17 Receive Configuration 4 (RCR4)
Offset
Register Offset
RCR4 98h
Function
Contains the receive fields for FIFO Combine mode, FIFO Packing mode, and frame sync settings.
NOTE
Do not alter this register when RCSR[RE] is 1.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
FCON
FCOMB FPACK FRSZ
T
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
Reserv ONDE
SYWD MF FSE FSP FSD
ed M
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-29
—
28 FIFO Continue on Error
FCONT Configures when SAI continues receiving data after a FIFO error is detected.
Table continues on the next page...

---

*Page 2510*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
0b - From the start of the next frame after the FIFO error flag is cleared
1b - From the same word that caused the FIFO error to become 1 after the FIFO warning flag is
cleared
27-26 FIFO Combine Mode
FCOMB Enables FIFO Combine mode for specified operations.
When FIFO Combine mode is enabled for FIFO reads, software reading any FIFO data register alternates
the read among the enabled data channel FIFOs. For example, if two data channels are enabled:
• The first read is performed to the first enabled data channel FIFO.
• The second read is performed to the second enabled data channel FIFO.
Resetting the FIFO or disabling FIFO Combine mode for FIFO reads resets the pointer back to the first
enabled data channel.
When FIFO Combine mode is enabled for FIFO writes from the receive shift registers, the first enabled
data channel input alternates between the enabled data channel FIFOs. For example, if two data channels
are enabled:
• The first unmasked received word is stored in the first enabled data channel FIFO.
• The second unmasked received word is stored in the second enabled data channel FIFO.
Because the first word of the frame is always stored in the first enabled data channel FIFO, NXP
recommends that the number of unmasked words per frame is evenly divisible by the number of enabled
data channels.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
SAI_0 RCR4 —
SAI_1 — RCR4
00b - Disable
01b - Enable on FIFO writes (from receive shift registers)
10b - Enable on FIFO reads (by software)
11b - Enable on FIFO writes (from receive shift registers) and reads (by software)
25-24 FIFO Packing Mode
FPACK Enables packing of 8-bit data or 16-bit data into each 32-bit FIFO word. If the word size is greater than 8 bits
or 16 bits, only the first 8 bits or 16 bits are stored in the FIFO. The first word in each frame always starts with
a new 32-bit FIFO word and the first bit shifted must be configured within the first packed word. When FIFO
packing is enabled, the FIFO read pointer only increments when software reads the full 32-bit FIFO word.
Table continues on the next page...

---

*Page 2511*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
00b - Disable
01b - Reserved
10b - Enable 8-bit FIFO packing
11b - Enable 16-bit FIFO packing
Reserved
23-20
—
19-16 Frame Size
FRSZ Configures the number of words in each frame. The value must be one less than the number of words in the
frame. For example, write 0 for one word per frame. The maximum supported frame size is 16 words.
0000b - 1
0001b - 2
0010b-1110b - (FRSZ value + 1)
1111b - 16
Reserved
15-13
—
12-8 Sync Width
SYWD Configures the length of the frame sync in number of bit-clock cycles. The value must be one less than
the number of bit-clock cycles. For example, write 0 for the frame sync to assert for one bit-clock cycle
only. You cannot configure this field to be longer than the first word of the frame.
0_0000b - 1
0_0001b - 2
0_0010b-1_1110b - (SYWD value + 1)
1_1111b - 32
Reserved
7-6
—
5 Reserved
— Write only zero to this field.
4 MSB First
MF Configures what is received first: LSB or MSB.
0b - LSB
1b - MSB
3 Frame Sync Early
Table continues on the next page...

---

*Page 2512*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
Determines when frame sync is asserted.
FSE
0b - First bit of the frame
1b - One bit before the first bit of the frame
2 On-Demand Mode
ONDEM Determines when the internal frame sync is generated. When this field is 1, and the frame sync is
generated internally, a frame sync is only generated when the FIFO warning flag is 0.
0b - Generated continuously
1b - Generated when the FIFO warning flag is 0
1 Frame Sync Polarity
FSP Configures the polarity of the frame sync.
0b - Active high
1b - Active low
0 Frame Sync Direction
FSD Configures the direction of the frame sync.
0b - Generated externally in Target mode
1b - Generated internally in Controller mode
71.6.1.18 Receive Configuration 5 (RCR5)
Offset
Register Offset
RCR5 9Ch
Function
Contains receive word and bit index settings.
NOTE
Do not alter this register when RCSR[RE] is 1.

---

*Page 2513*

Synchronous Audio Interface (SAI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
WNW W0W
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
FBT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-29
—
28-24 Word N Width
WNW Configures the number of bits in each word except for the first one in the frame. The value must be one less
than the number of bits per word. SAI does not support word widths less than 8 bits.
SAI does not support bit settings 0–6 (0b–0_0110b).
0_0111b - 8
0_1000b - 9
0_1001b-1_1110b - (WNW value + 1)
1_1111b - 32
Reserved
23-21
—
20-16 Word 0 Width
W0W Configures the number of bits in the first word of each frame. The value must be one less than the
number of bits in the first word. SAI does not support word widths less than 8 bits when there is only one
word per frame.
0_0000b - 1
0_0001b - 2
0_0010b-1_1110b - (W0W value + 1)
1_1111b - 32
Reserved
15-13
—
12-8 First Bit Shifted
Table continues on the next page...

---

*Page 2514*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
FBT Configures the bit index for the first bit received for each word in the frame. If configured for MSB-first, the
index of the next bit received is one less than the current bit received. If configured for LSB-first, the index
of the next bit received is one more than the current bit received. The value must be greater than or equal to
the word width when configured for MSB-first. The value must be less than or equal to 31-word width when
configured for LSB-first.
See Data alignment for details.
0_0000b - 0
0_0001b-1_1110b - FBT value
1_1111b - 31
Reserved
7-0
—
71.6.1.19 Receive Data (RDR0 - RDR3)
Offset
Register Offset
RDR0 A0h
RDR1 A4h
RDR2 A8h
RDR3 ACh
Function
Contains receive data.
When the receive FIFO is not empty, reads from this register return the data from the top of the receive FIFO. When the receive
FIFO is empty, SAI ignores reads from this register.
NOTE
Each module instance supports a different number of registers.
Instance Register supported Register not supported
SAI_0 RDR0–RDR3 —
SAI_1 RDR0 RDR1–RDR3

---

*Page 2515*

Synchronous Audio Interface (SAI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RDR
W
Reset See Register reset values .
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RDR
W
Reset See Register reset values .
Register reset values
Register Reset value
RDR0 SAI_0,SAI_1: 0000_0000h
RDR1–RDR3 0000_0000h
Fields
Field Function
Receive Data
31-0
RDR
71.6.1.20 Receive FIFO (RFR0 - RFR3)
Offset
Register Offset
RFR0 C0h
RFR1 C4h
RFR2 C8h
RFR3 CCh
Function
Contains the receive FIFO pointers. The MSB of the read and write pointers distinguishes between FIFO full and empty
conditions. If the read and write pointers are identical, the FIFO is empty. If the read and write pointers are identical except for
the MSB, the FIFO is full.
NOTE
Each module instance supports a different number of registers.

---

*Page 2516*

Synchronous Audio Interface (SAI)
Instance Register supported Register not supported
SAI_0 RFR0–RFR3 —
SAI_1 RFR0 RFR1–RFR3
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 WFP
W
Reset See Register reset values .
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RCP 0 RFP
W
Reset See Register reset values .
Register reset values
Register Reset value
RFR0 SAI_0,SAI_1: 0000_0000h
RFR1–RFR3 0000_0000h
Fields
Field Function
Reserved
31-20
—
19-16 Write FIFO Pointer
WFP Indicates the FIFO write pointer for the receive data channel.
15 Read Channel Pointer
RCP Indicates whether this data channel is the next FIFO to be read when FIFO Combine mode is enabled for
read operations.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.

---

*Page 2517*

Synchronous Audio Interface (SAI)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
SAI_0 RFR0–RFR3 —
SAI_1 — RFR0
0b - No effect
1b - Next FIFO to be read
Reserved
14-4
—
3-0 Read FIFO Pointer
RFP Indicates the FIFO read pointer for the receive data channel.
71.6.1.21 Receive Mask (RMR)
Offset
Register Offset
RMR E0h
Function
Contains the mask for the receive word. This register is double-buffered and updates:
• When RCSR[RE] first becomes 1.
• At the end of each frame.
This setup allows the masked words in each frame to change from frame to frame.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
RWM
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2518*

Synchronous Audio Interface (SAI)
Fields
Field Function
Reserved
31-16
—
15-0 Receive Word Mask
RWM Configures whether the receive word is masked (received data ignored and not written to receive FIFO)
for the corresponding word in the frame.
0000_0000_0000_0000b - Enable
0000_0000_0000_0001b - Mask

#### 71.7 Glossary

FIFO First-in first-out
LSB Least significant bit
MSB Most significant bit

---

*Page 2519*

