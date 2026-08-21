<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 73 -->

# Chapter 73

# Low Power Universal Asynchronous Receiver/

# Transmitter (LPUART)

#### 73.1 Chip-specific LPUART information

#### 73.1.1 LPUART instances and configuration

Table 542. LPUART instances
Instance MCXE31B MCXE315/MCXE316 MCXE317
LPUART_0 Yes Yes Yes
LPUART_1 Yes Yes Yes
LPUART_2 Yes Yes Yes
LPUART_3 Yes Yes Yes
LPUART_4 Yes No Yes
LPUART_5 Yes No Yes
LPUART_6 Yes No Yes
LPUART_7 Yes No Yes
LPUART_8 Yes No No
LPUART_9 Yes No No
LPUART_10 Yes No No
LPUART_11 Yes No No
LPUART_12 Yes No No
LPUART_13 Yes No No
LPUART_14 Yes No No
LPUART_15 Yes No No
Table 543. LPUART Configuration
Feature Configuration
MCXE317
TX FIFO size 4 Words
RX FIFO size 4 Words
Functionality supported Standard LPUART functionality with
MODEM/IrDA
LIN master and slave operation

---

*Page 3110*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
NOTE
LPUART instances are not available during Standby.
LPUART[0:2]_trg_input is coming from TRGMUX and you should take care of the pulse width of trigger. It
should follow the requirement mentioned in section "Peripheral Triggers". These triggers are not present
in MCXE31B.

#### 73.2 Overview

LPUART provides asynchronous, serial communication capabilities with external devices. It supports the non-return-to-zero
(NRZ) encoding format and infrared data association (IrDA)-compatible, low-speed serial infrared (SIR) protocol. LPUART can
continue operating when the processor is in Low-Power mode, if an appropriate peripheral clock is available.

#### 73.2.1 Block diagram

Figure 423 shows the transmitter portion of LPUART.
Internal chip peripheral bus
Write-only
CTRL[LOOPS]
Transmit FIFO
Asynchronous CTRL[RSRC]
module clock
11-bit Transmit shift register
Baud divider Loop control To receive data in
CTRL[M]
Stop Start
OSR divider
H 8 7 6 5 4 3 2 1 0 L
To TxD pin
Shift direction lsb
CTRL[TXINV]
CTRL[R9T8]
CTRL[PE]
Parity generation
Shift enable
Break (all 0s)
CTRL[PT] Load from FIFO Preamble (all 1s)
LPUART controls TxD
CTRL[TE]
TxD logic
CTRL[SBK]
Transmit control TxD direction
CTRL[TXDIR]
STAT[BRK13]
STAT[TDRE]
CTRL[TIE]
TX interrupt request
STAT[TC]
CTRL[TCIE]
Figure 423. Transmitter block diagram

---

*Page 3111*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Figure 424 shows the receiver portion of LPUART.
Internal chip peripheral bus
BAUD[SBR] Receive FIFO
Variable 12-bit Receive CTRL[M]
Asynchronous Baud rate
Shift Register
module clock generator
BAUD[M10]
Stop Start
STAT[LBKDE]
CTRL[RE]
Receive STAT[MSBF]
control Shift direction
STAT[RAF]
STAT[RXINV]
Receive data
CTRL[PE]
Parity Wakeup
CTRL[LOOPS] Receiver logic logic
CTRL[PT]
source
CTRL[RSRC] control
From transmitter
IRQ and DMA requests
DMA logic IRQ requests
Receive data Active edge
(from RxD) detect
Figure 424. Receiver block diagram

#### 73.2.2 Features

• Full-duplex, standard NRZ format
• Programmable baud rates (13-bit modulo divider) with a configurable oversampling ratio (OSR) from 4× to 32×
• Asynchronous operation of transmit and receive baud rates with respect to the bus clock:
— Baud rate can be configured independently of the bus clock frequency.
— Operation in Low-Power modes is supported.
• Interrupt , DMA , or polled operations:
— Transmit data empty and transmission complete
— Receive data full
— Receive overrun, parity error, framing error, and noise error
— Idle receiver detect
— Active edge on receive pin
— Break detect supporting LIN
— Receive data match
• Hardware parity generation and checking
• Programmable 7-bit, 8-bit, 9-bit, or 10-bit character length
• Programmable 1-bit or 2-bit stop bits
• Support for three receiver wake-up methods:

---

*Page 3112*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
— Idle line wake-up
— Address mark wake-up
— Receive data match
• Automatic address matching to reduce ISR overhead:
— Address mark matching
— Idle line address matching
— Address match start, address match end
• Optional 13-bit and 11-bit break character generation
• Configurable idle length detection supporting 1, 2, 4, 8, 16, 32, 64, or 128 idle characters
• Selectable transmitter output and receiver input polarity
• Hardware flow control support for request to send (RTS) and clear to send (CTS) signals
• Selectable IrDA 1.4 return-to-zero-inverted (RZI) format with a programmable pulse width
• Independent FIFO structure for transmit and receive functions:
— Separate configurable watermarks for receive and transmit requests
— Option for receiver to assert request after a configurable number of idle characters, if receive FIFO is not empty

#### 73.3 Functional description

LPUART supports full-duplex, asynchronous, NRZ serial communication and comprises a baud rate generator, transmitter,
and receiver block. The transmitter and receiver operate independently, although they use the same baud rate generator. The
following sections describe all LPUART blocks.

#### 73.3.1 Baud rate generation

A 13-bit modulus counter in the baud rate generator derives the baud rate for both the receiver and transmitter. The value, ranging
from 1 to 8191, written to BAUD[SBR] determines the baud clock divisor for the asynchronous LPUART baud clock. The baud
rate clock drives the receiver, while a bit clock, generated from the baud rate clock divided by the OSR, drives the transmitter.
Depending on the OSR, the receiver has an acquisition rate of 4 to 32 samples per bit time. LPUART requires BAUD[SBR] and
BAUD[OSR] to accurately match the baud rate requirement. There is a relationship between the required baud rate and the input
clock frequency, SBR, and OSR. See the below figure for details.
Modulo divide by
1 - 8191
OSR
LPUART asynchronous Divide by
BAUD[SBR] Tx baud rate
module clock OSR + 1
Baud rate generator
Rx sampling clock
off if BAUD[SBR] = 0
[(OSR+1) × baud rate]
LPUART asynchronous module clock
Baud rate =
BAUD[SBR] × (OSR+1)
Figure 425. Baud rate generation
Baud rate generation is subject to these sources of error:
• Integer division of the asynchronous LPUART baud clock may not give the exact target frequency.
• Synchronization with the asynchronous LPUART baud clock can lead to a phase shift.

---

*Page 3113*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Baud rate generation is a free-running counter that continues whenever the transmitter or receiver is enabled. The transmitter bit
clock continues whenever the transmitter is enabled; each transmitted character aligns to the next edge of the transmit bit clock.
In general, configuring OSR for a higher ratio and/or sampling on both edges of the clock slightly improves LPUART's tolerance
to baud rate mismatch between the received data and LPUART configured baud rate. However, the three data samples in each
bit (see Data sampling technique ) are also closer together, which may impact noise sensitivity.

#### 73.3.2 Baud rate tolerance

A transmitting device may operate at a baud rate below or above that of the receiver.
Accumulated bit time misalignment can cause one of the three stop bit data samples to fall outside the actual stop bit. A noise error
will occur if the three samples are not all the same logical values. A framing error will occur if the receiver clock is misaligned in
such a way that the majority of the three stop bit samples are a logic zero.
As the receiver samples an incoming frame, it may resynchronize the oversampling clock on any valid falling edge within the frame.
Resynchronization within frames will correct a misalignment between transmitter bit times and receiver bit times.
In general, increasing the number of samples per bit will increase the baud rate tolerance and decreasing the number of samples
per bit will reduce the baud rate tolerance. Note that since LPUART implements triple voting on consecutive receive data samples,
increasing the number of samples per bit will move those samples closer together which would reduce the width of noise that can
be filtered by the triple voting logic.

#### 73.3.3 Calculating baud rate tolerance

Using the following definitions:
• SAM is the number of sample points per bit (valid range from 8 to 32; equal to (OSR + 1) × (BOTHEDGE + 1)).
• BIT is the number of bits in a character including start, data and stop bits (valid range from 9 to 13).
The ideal baud rate tolerance can be calculated as follows:
• Slow data rate tolerance = ( (SAM ÷ 2) - 1) ÷ ((SAM × BIT) - (SAM ÷ 2) + 2)
• Fast data rate tolerance = ( (SAM ÷ 2) - 2) ÷ (SAM × BIT)
As an example, if configured for 8-bit data, 1 stop bit (BIT = 10) and with OSR=0x7 and BOTHEDGE = 1 (SAM = 16):
• Slow data rate tolerance = (8 - 1) ÷ (160 - 8 + 2) = 7 ÷ 154 = 4.54%
• Fast data rate tolerance = (8 - 2) ÷ 160 = 6 ÷ 160 = 3.75%
If configured for 9-bit data with 1 stop bit (BIT = 11) with same oversampling configuration, then:
• Slow data rate tolerance = (8 - 1) ÷ (176 - 8 + 2) = 7 ÷ 170 = 4.12%
• Fast data rate tolerance = (8 - 2) ÷ 176 = 6 ÷ 176 = 3.41%
NOTE
Additional factors can contribute to a lower baud rate tolerance than the ideal. These include clock uncertainty
or jitter on the LPUART functional clock source, differences in rise and fall times on the transmitter output and
synchronization of the external receive pin to the local LPUART functional clock.

#### 73.3.4 Transmitter functional description

This section describes the functioning of the LPUART transmitter, as shown in the transmitter portion of Block diagram , as well
as specialized functions for sending break and idle characters.
The transmitter output (TXD) idle state defaults to logic high; the transmitter output is inverted when you write 1 to CTRL[TXINV] ,
which becomes 0 following reset. You can enable the transmitter by writing 1 to CTRL[TE] . This queues a preamble character
that is one full character frame of the Idle state. The transmitter then remains idle until data is available in the transmit FIFO and
programs store data in the transmit FIFO by writing to Data (DATA) .

---

*Page 3114*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
The central element of the LPUART transmitter is the transmit shift register that is 9-bit to 13-bit long depending on the settings
of CTRL[M] , CTRL[M7] , BAUD[M10] , and BAUD[SBNS] . Going forward in this discussion, assume that CTRL[M] , CTRL[M7] ,
BAUD[M10] , and BAUD[SBNS] are 0, selecting the normal 8-bit Data mode, in which the shift register holds a start bit, eight data
bits, and a stop bit. When the transmit shift register is available for a new character, the value waiting in transmit FIFO is transferred
to the transmit shift register, synchronized with the baud rate clock, and STAT[TDRE] becomes 1 to indicate that another character
may be written to the transmit FIFO at Data (DATA) .
If no new character is waiting in the transmit FIFO after a stop bit is shifted out of the TXD pin, the transmitter sets the transmit
complete flag and enters an idle mode, with TXD high, waiting for more characters to transmit.
Writing 0 to CTRL[TE] does not immediately disable the transmitter. The current transmit activity in progress must first be
completed (that could include a data character, idle character, or break character), although the transmitter does not start
transmitting another character.
73.3.4.1 Break character length
CTRL[SBK] sends break characters, originally used to gain the attention of old teletype receivers. Break characters are a full
character time of logic 0, 9-bit to 12-bit times, including the start and stop bits. You can enable a longer break of 13-bit times
by writing 1 to STAT[BRK13] . Normally, a program waits for STAT[TDRE] to become 1 to indicate that the last character of a
message has moved to the transmit shifter. Next, the program writes 1 and then writes 0 to CTRL[SBK] . This action queues a break
character to be sent as soon as the shifter is available. If CTRL[SBK] remains 1 when the queued break moves into the shifter,
synchronized with the baud rate clock, an additional break character is queued. When LPUART is the receiving module, it receives
a break character as 0s in all data bits and a framing error ( STAT[FE] = 1) is detected.
You can also transmit a break character by writing to Data (DATA) with DATA[FRETSC] = 1 and the data bits clear. This supports
transmitting the break character as part of the normal data stream and also allows DMA to transmit a break character.
When idle line wake-up is used, a full character time of idle (logic 1) is needed between messages to wake up any sleeping
receivers. Normally, a program waits for STAT[TDRE] to become 1 to indicate that the last character of a message has moved
to the transmit shifter. Next, write 0 and then write 1 to CTRL[TE] . This action queues an idle character to be sent as soon as the
shifter is available. As long as the character in the shifter does not finish while CTRL[TE] becomes 0, the LPUART transmitter does
not release control of the TXD pin.
You can also write to Data (DATA) to transmit an idle character, with DATA[FRETSC] and DATA[R9T9] = 1 and the values of all
the other fields = 0. This supports transmitting the idle character as part of the normal data stream and also allows DMA to transmit
an idle character .
As shown in the following table, STAT[BRK13] , CTRL[M] , CTRL[M7] , BAUD[M10] , and BAUD[SBNS] affect the length of the
break character.
Table 544. Break character length
STAT[BRK13] CTRL[M] BAUD[M10] CTRL[M7] BAUD[SBNS] Break character
length (in bit times)
0 0 0 0 0 10
0 0 0 0 1 11
0 0 0 1 0 9
0 0 0 1 1 10
0 1 0 — 0 11
0 1 0 — 1 12
0 — 1 — 0 12
0 — 1 — 1 13
1 0 0 0 0 13
Table continues on the next page...

---

*Page 3115*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table 544. Break character length (continued)
STAT[BRK13] CTRL[M] BAUD[M10] CTRL[M7] BAUD[SBNS] Break character
length (in bit times)
1 0 0 0 1 13
1 0 0 1 0 12
1 0 0 1 1 12
1 1 0 — 0 14
1 1 0 — 1 14
1 — 1 — 0 15
1 — 1 — 1 15
73.3.4.2 Hardware flow control
The transmitter supports hardware flow control by gating the transmission with the value of CTS_B. If the CTS operation is
enabled, the character is transmitted when CTS_B is asserted. If CTS_B is deasserted in the middle of a transmission with
characters remaining in the transmitter FIFO, the character in the transmit shift register is complete. Any characters in the FIFO
wait for CTS_B to assert again, and TXD remains in the mark state (idle state) until CTS_B is reasserted. The CTS_B pin must
assert for longer than one bit period to guarantee that a new transmission is started when the transmitter is idle with CTS.
If the CTS operation is disabled, the transmitter ignores the state of CTS_B.
The transmitter's CTS_B signal can be enabled even if the same LPUART receiver's RTS_B signal is disabled.
73.3.4.3 Transceiver driver enable
The transmitter can use RTS_B as an enable signal for the driver of an external transceiver. See Transceiver driver enable using
RTS_B for details. If the RTS operation is enabled, when a character is placed into an empty transmit shift register, RTS_B asserts
1-bit time before the start bit is transmitted. RTS_B remains asserted for the whole time that the transmit shift register has any
characters. RTS_B deasserts 1-bit time after all characters in the transmit FIFO and shift register are completely sent, including the
last stop bit. In other words, when RTS_B is used as a transceiver enable, RTS_B asserts 1-bit time before the transmitter starts
transmitting and negates 1-bit time after the transmitter goes idle.
Transmitting a break character also asserts RTS_B, with the same assertion and deassertion timing as having a character in the
transmit shift register.
The transmitter's RTS_B signal asserts only when the transmitter is enabled. However, the transmitter's RTS_B signal is
unaffected by its CTS_B signal. RTS_B remains asserted until the transfer is complete, even if the transmitter is disabled mid-way
through a data transfer.
73.3.4.4 Transceiver driver enable using RTS_B
RS-485 is a multiple drop communication protocol in which the LPUART transceiver's driver is three-stated unless LPUART is
driving. The transmitter can use the RTS_B signal to enable the driver of a transceiver. The polarity of RTS_B can be matched to
the polarity of the transceiver's driver enable signal.
The following figure shows the receiver enable signal asserted. This connection can also connect RTS_B to both DE and RE_B.
The transceiver's receiver is disabled when driving. A pullup can pull RXD to a nonfloating value during this time. You can refine
this option further by operating LPUART in Single-Wire mode, freeing the RXD pin for other uses.

---

*Page 3116*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
LPUART RS-485 transceiver
TXD DI Y
Transmitter RTS_B DE Driver Z
RXD RO A
Receiver RE_B Receiver B
Figure 426. Transceiver driver enable using RTS_B

#### 73.3.5 Receiver functional description

This section discusses the functioning of the LPUART receiver, as shown in the receiver portion of Block diagram . The section
also discusses:
• The data sampling technique used to reconstruct receiver data.
• Different variations of the receiver wake-up function.
You can invert the receiver input by writing 1 to STAT[RXINV] and enable the receiver by writing 1 to CTRL[RE] . Character frames
consist of a start bit of logic 0, along with N (7, 8, 9, 10) bits (MSB or LSB first), and one or two stop bits of logic 1. For information
about 7-bit, 9-bit, or 10-bit Data mode, see Data modes . Going forward in this discussion, assume that LPUART is configured for
a normal 8-bit Data mode.
After receiving the stop bit into the receive shifter, and provided the receive data register is not already full ( STAT[RDRF] = 0), the
data character is transferred to the receive FIFO, resulting in STAT[RDRF] becoming 1. However, if STAT[RDRF] is already 1,
indicating that the receive data buffer is already full, STAT[OR] becomes 1 and the new data is lost.
Because the LPUART receiver is separate from the receive FIFO, the receive shift register can receive the next word when the
receive FIFO is full, and it is only at the end of the character that the next data is written into the receive FIFO, potentially triggering
the overrun flag if the FIFO is full.
When a program detects that the receive data register is full ( STAT[RDRF] = 1), it gets the data from the FIFO by reading Data
(DATA) . See Interrupts for details about flag clearing.
73.3.5.1 Data sampling technique
The LPUART receiver supports a configurable oversampling rate of between 4× and 32× of the baud rate clock for sampling. The
receiver starts by considering logic level samples at the oversampling rate times the baud rate to search for a falling edge on the
RXD serial data input pin. A falling edge is defined as a logic 0 sample after three consecutive logic 1 samples. The oversampling
baud rate clock divides the bit time into 4 to 32 segments from 1 to OSR (where OSR is the configured oversampling ratio). When
a falling edge is located, three more samples are taken at (OSR ÷ 2), (OSR ÷ 2) + 1, and (OSR ÷ 2) + 2 to ensure that this is a real
start bit and not merely noise. If at least two of these three samples are 0, the receiver assumes they are synchronized to a received
character. If another falling edge is detected before the receiver is considered synchronized, the receiver restarts sampling from
the first segment.
The receiver then samples each bit time, including the start and stop bits, at (OSR ÷ 2), (OSR ÷ 2) + 1, and (OSR ÷ 2) + 2, to
determine the logic level for that bit. The logic level is interpreted to be that of the majority of the samples taken during the bit time.
If any sample in any bit time, including the start and stop bits, in a character frame fails to agree with the logic level for that bit,
noise flag ( STAT[NF] ) becomes 1 when the received character is transferred to the receive FIFO.
When the LPUART receiver is configured to sample on both edges of the baud rate clock (that is, when BAUD[BOTHEDGE] = 1),
the number of segments in each received bit is effectively doubled (from 1 to OSR× 2). The start and data bits are then sampled
at OSR, OSR + 1, and OSR + 2. You must enable sampling on both edges of the clock for oversampling rates of 4× to 7×. This
sampling is optional for higher oversampling rates.

---

*Page 3117*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
The synchronization feature of LPUART synchronizes the internal oversampling counter with a detected falling edge on the
receive signal, and to adjust the data sampling window. The falling edge detection needs three consecutive 1s prior to the "1->0"
(one to zero) transition. After the initial falling edge detection for the start bit, the circuit continuously monitors the next falling edge,
and resets the counter after another falling edge is detected. This synchronization to the start bit is termed as resynchronization.
When BAUD[RESYNCDIS] is 0, you perform this falling edge detection and resynchronization not only for the start bit but also for
the rest of the character reception after the start bit.
When BAUD[RESYNCDIS] is 1, you perform the falling edge detection and resynchronization only for the start bit. The use case
for disabling the resynchronization is protocols that require this (for example, LIN 2.1 prohibits resynchronization within a byte).
The following table and figure explain LPUART resynchronization.
Table 545. LPUART resynchronization settings
Resynchronization BAUD[RESYNCDIS] = 0 BAUD[RESYNCDIS] = 1
For the starting bit falling Yes Yes
edge
For all falling edges after the Yes No
start bit
Sample with both edges;
sample points: 1-8
Internal sampling
clock
Falling edge Falling edge Falling edge
detection detection detection
RX with noise and delay
Data sampling Data sampling
BAUD[RESYNCDIS] = 0
Data sampling
Oversample cycle number - - - 1 2 3 4 1 2 3 4 5 6 7 8 1 2 3 4 5 6 7 8 1 1 2 3 4 5 6 7 8 1 2
Resynchronization Resynchronization
Falling edge Falling edge
detection detection
RX with noise and delay
Data sampling Data sampling
BAUD[RESYNCDIS] = 1
Data sampling
Oversample cycle number - - - 1 2 3 4 1 2 3 4 5 6 7 8 1 2 3 4 5 6 7 8 1 2 3 4 5 6 7 8 1 2 3
Resynchronization
Start bit Data bit #0 Data bit #1
Figure 427. LPUART resynchronization diagram
73.3.5.2 Receiver wake-up operation
Receiver wake-up and receiver address matching are hardware mechanisms that allow an LPUART receiver to ignore the
characters in a message intended for a different receiver.
During receiver wake-up, all receivers evaluate the first character(s) of each message, and as soon as they determine the
message is intended for a different receiver, they write 1 to CTRL[RWU] .

---

*Page 3118*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
When CTRL[RWU] and STAT[RWUID] are 1, the status fields associated with the receiver, with the exception of STAT[IDLE] , are
inhibited from becoming 1, thus eliminating the software overhead for handling the unimportant message characters. At the end
of a message, all receivers automatically force CTRL[RWU] to become 0. This results in all receivers waking up in time to look at
the first character(s) of the next message.
During receiver address matching, the address matching is performed in hardware and the LPUART receiver ignores all
characters that do not meet the address match requirements.
Table 546. Receiver wake-up options
CTRL[RWU] BAUD[MAEN1] | BAUD[MATCFG] CTRL[WAKE] : Receiver wake-up
BAUD[MAEN2] STAT[RWUID]
0 0 X X Normal operation
1 0 00 00 Receiver wake-up on
idle line; STAT[IDLE] =
0
1 0 00 01 Receiver wake-up on
idle line; STAT[IDLE] =
1
1 0 00 10 Receiver wake-up on
address mark
1 1 11 10 Receiver wake-up on
data match
0 1 00 X0 Address mark address
match; STAT[IDLE]
= 0 for discarded
characters
0 1 00 X1 Address mark address
match; STAT[IDLE]
= 1 for discarded
characters
0 1 01 X0 Idle line address match
0 1 10 X0 Match on and match
off; STAT[IDLE] = 0 for
discarded characters
0 1 10 X1 Match on and match
off; STAT[IDLE] = 1 for
discarded characters
73.3.5.2.1 Idle line wake-up
When CTRL[WAKE] is 0, you can configure the receiver for an idle line wake-up. In this mode, CTRL[RWU] becomes 0
automatically when the receiver detects a full character time of the idle-line level.
CTRL[M] , CTRL[M7] , and BAUD[M10] select 7-bit to 10-bit Data mode and BAUD[SBNS] selects a 1-bit or 2-bit stop bit number
that determines how many bit times of idle are needed to constitute a full character time, 9 to 13 bit times because of the start and
stop bits.
When CTRL[RWU] is 1 and STAT[RWUID] is 0, the idle condition that wakes up the receiver does not lead to STAT[IDLE]
becoming 1. The receiver wakes up and waits for the first data character of the next message that leads to STAT[RDRF] becoming
1 and generates an interrupt if enabled. When STAT[RWUID] is 1, any idle condition leads to STAT[IDLE] becoming 1 and
generates an interrupt if enabled, regardless of whether CTRL[RWU] is 0 or 1.

---

*Page 3119*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
These are the ways to detect an idle line:
• When CTRL[ILT] is 0, the idle bit counter starts after the start bit so that the stop bit and any logic 1s at the end of a character
count to calculate the full character time of idle.
• When CTRL[ILT] is 1, the idle bit counter does not start until after the stop bit time so that the data in the last character of the
previous message does not impact the idle detection.
73.3.5.2.2 Address mark wake-up
When CTRL[WAKE] is 1, you can configure the receiver for an address mark wake-up. In this mode, CTRL[RWU] becomes 0
automatically when the receiver detects a logic 1 in the most significant bit of the received character. When parity is enabled, the
second most significant bit is used for address mark wake-up.
Address mark wake-up allows messages to contain idle characters, but requires one bit to be reserved for use in address frames.
The logic 1 in the most significant bit (or second most significant bit when parity is enabled) of an address frame writes 0 to
CTRL[RWU] and writes 1 to STAT[RDRF] . In this case, the character with the address mark bit is received even if the receiver is
sleeping during most of this character time.
73.3.5.2.3 Data match wake-up
When CTRL[RWU] and CTRL[WAKE] are 1, and BAUD[MATCFG] equals 11, the receiver is configured for a data match wake-up.
In this mode, CTRL[RWU] becomes 0 automatically when the receiver detects a character that matches MATCH[MA1] when
BAUD[MAEN1] is 1, or that matches MATCH[MA2] when BAUD[MAEN2] is 1.
73.3.5.2.4 Address match operation
You can enable the address match operation when either BAUD[MAEN1] or BAUD[MAEN2] is 1 and BAUD[MATCFG] is 0. In this
function, a character that the RXD pin receives with a logic 1 in the most significant bit (or the second most significant bit when
parity is enabled) is considered an address and is compared to the associated MATCH[MA1] or MATCH[MA2] . The character is
only transferred to the receive buffer, and STAT[RDRF] becomes 1 if the comparison matches. All subsequent characters received
with a logic 0 in the most significant bit (or the second most significant bit when parity is enabled) are considered to be data
associated with the address and are transferred to the receive FIFO. If no marked address match occurs, no transfer is made to
the receive FIFO, and all the characters that follow, with logic 0 in the most significant bit (or second most significant bit when
parity is enabled), are also discarded. If both BAUD[MAEN1] and BAUD[MAEN2] are 0, the receiver operates normally, and all
the received data is transferred to the receive FIFO.
The address match operation functions in the same way for both MATCH[MA1] and MATCH[MA2] :
• If either BAUD[MAEN1] or BAUD[MAEN2] is 1, a marked address is compared only to the associated Match Address
(MATCH) and data is transferred to the receive FIFO only on a match.
• If both BAUD[MAEN1] and BAUD[MAEN2] are 1, a marked address is compared to both MATCH[MA1] and MATCH[MA2]
and data is transferred only on a match with either of these fields.
73.3.5.2.5 Idle match operation
You can enable the idle match operation when either BAUD[MAEN1] or BAUD[MAEN2] is 1 and BAUD[MATCFG] is 1. In this
function, the first character that the RXD pin receives after an idle line condition is considered an address and is compared to the
associated MATCH[MA1] or MATCH[MA2] . The character is transferred only to the receive buffer, and STAT[RDRF] becomes 1,
if the comparison matches. All subsequent characters are considered to be data associated with the address and are transferred
to the receive FIFO until the next idle line condition is detected. If no address match occurs, no transfer is made to the receive
FIFO, and all the frames that follow, until the next idle condition, are also discarded. If both BAUD[MAEN1] and BAUD[MAEN2]
are 0, the receiver operates normally, and all the received data is transferred to the receive FIFO.
An idle match operation functions in the same way for both MATCH[MA1] and MATCH[MA2] :
• If either BAUD[MAEN1] or BAUD[MAEN2] is 1, the first character after an idle line is compared only to the associated Data
(DATA) and data is transferred to the receive FIFO only on a match.

---

*Page 3120*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
• If both BAUD[MAEN1] and BAUD[MAEN2] are 1, the first character after an idle line is compared to both MATCH[MA1] and
MATCH[MA2] and data is transferred only on a match with either of these fields.
73.3.5.2.6 Match on, match off operation
The match on, match off operation is enabled when both BAUD[MAEN1] and BAUD[MAEN2] are 1 and BAUD[MATCFG] =
10. In this function, a character that the RXD pin receives matches MATCH[MA1] and is transferred to the receive buffer, and
STAT[RDRF] becomes 1. All subsequent characters are considered to be data and are also transferred to the receive FIFO,
until a character that matches MATCH[MA2] is received. The character that matches MATCH[MA2] , along with all subsequent
characters, is discarded; and this continues until another character that matches MATCH[MA1] is received. If both BAUD[MAEN1]
and BAUD[MAEN2] are 0, the receiver operates normally, and all the received data is transferred to the receive FIFO.
NOTE
The match on, match off operation requires both BAUD[MAEN1] and BAUD[MAEN2] to be 1.
73.3.5.3 Hardware flow control
To support hardware flow control, you can program the receiver to automatically assert and deassert RTS_B:
• RTS_B remains asserted until the transfer is complete, even if the transmitter is disabled midway through a data transfer.
See Transceiver driver enable using RTS_B for more information.
• If the receiver RTS functionality is enabled, the receiver automatically deasserts RTS_B if STAT[RDRF] is 1 or a start bit is
detected that causes STAT[RDRF] to become 1.
• The receiver asserts RTS_B when STAT[RDRF] is 0 and has not detected a start bit that causes STAT[RDRF] to become
1. There is no impact if STAT[RDRF] is 1 already.
• Even if RTS_B is deasserted, the receiver continues to receive characters until the receive FIFO is overrun.
• If the receiver RTS functionality is disabled, the receiver's RTS_B remains deasserted.
• When RTS is driven by receiver, it will be negated when the receiver is disabled irrespective of the FIFO level.

#### 73.3.6 Additional LPUART functions

73.3.6.1 Data modes
You can configure the LPUART transmitter and receiver to operate in 7-bit Data mode by writing 1 to CTRL[M7] , 9-bit Data mode
by writing 1 to CTRL[M] , or 10-bit Data mode by writing 1 to BAUD[M10] . In 9-bit Data mode, there exists a ninth data bit and in
10-bit mode, there exists a tenth data bit.
When performing 8-bit writes to the transmit FIFO, the ninth and tenth bits are pushed into the FIFO from CTRL[T8] and CTRL[T9].
For coherent 8-bit writes, you must write to CTRL[T8] and CTRL[T9] before writing to Data (DATA) [7:0]. However, if the values
in CTRL[T8] or CTRL[T9] do not need to change, it is not necessary to update CTRL[T8] and CTRL[T9] before every 8-bit write
to Data (DATA) .
When performing 16-bit or 32-bit writes to the transmit FIFO, all 10 bits are pushed into the transmit FIFO from the write data.
When performing 8-bit reads of the receive FIFO, the ninth and tenth bits are held in CTRL[R8] and CTRL[R9] but you must read
them before reading Data (DATA) . A 16-bit or 32-bit read of the receive FIFO returns all 10 bits in Data (DATA) .
The 9-bit Data mode is typically used with parity to allow eight bits of data plus the parity in the ninth bit, or it is used with the
address mark wake-up so that the ninth data bit can serve as the wake-up bit. The 10-bit Data mode is typically used with parity
and address mark wake-up so that the ninth data bit can serve as the wake-up bit and the tenth bit can serve as the parity bit. In
custom protocols, the ninth and/or tenth bits can also serve as software-controlled markers.

---

*Page 3121*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
73.3.6.2 Idle length
An idle character is one where the start bit, all data bits, and stop bits are in the mark position (idle state, generally logic 1). You
can configure CTRL[ILT] to start detecting an idle character from the previous start bit (any data bits and stop bits count for idle
character detection) or from the previous stop bit.
You can also use CTRL[IDLECFG] to configure the number of idle characters that must be received before an idle line condition
is detected. This field configures the number of idle characters that must be received before STAT[IDLE] becomes 1, STAT[RAF]
becomes 0, and DATA[IDLINE] becomes 1 with the next received character.
CTRL[IDLECFG] also affects the idle line wake-up and idle match operations. When either the address match or match on/off
operation is enabled, writing 1 to STAT[RWUID] causes any discarded characters to be treated as idle characters.
73.3.6.3 Loop mode
Enable Loop mode by setting CTRL[LOOPS] = 1 and CTRL[RSRC] = 0. You, sometimes, use Loop mode to check software,
independent of connections in the external system, to help isolate system problems. In this mode, the transmitter output is
internally connected to the receiver input and LPUART does not use the RXD pin.
73.3.6.4 Single-Wire mode
Enable Single-Wire mode by setting CTRL[LOOPS] = 1 and CTRL[RSRC] = 1. Single-Wire mode implements a half-duplex serial
connection. The receiver is internally connected to the transmitter output and TXD pin (the RXD pin is not used).
In Single-Wire mode, CTRL[TXDIR] controls the direction of serial data on the TXD pin. When CTRL[TXDIR] becomes 0, the TXD
pin is an input to the receiver and the transmitter is temporarily disconnected from the TXD pin so that an external device can send
serial data to the receiver. When CTRL[TXDIR] = 1, the TXD pin is an output that the transmitter drives. The internal loop back
connection is disabled, and as a result, the receiver is unable to receive characters that the transmitter sends out.

#### 73.3.7 Peripheral triggers

The connection of the LPUART peripheral triggers with other peripherals is chip-specific.
73.3.7.1 Output triggers
LPUART generates the following output triggers that can be connected to other peripherals on the chip:
• The transmit word trigger asserts at the end of each transmitted word and negates after 1-bit period.
• The transmit data trigger is identical to the TXD pin output, but without support for input trigger modulation.
• The receive word trigger asserts at the end of each received word that is written to the receive FIFO, for one oversampling
clock period.
• The receive idle trigger asserts when STAT[IDLE] becomes 1, for one oversampling clock period.
73.3.7.2 Input trigger
LPUART supports a peripheral input trigger that you can configure in one of the following ways:
• By enabling the CTS function: You can connect the input trigger instead of the CTS_B pin input. The input trigger must
assert for longer than 1-bit clock period when the transmitter is idle, with data to send, to guarantee a new transmission.
• By making the input trigger modulate the transmit data output (trigger is logically ANDed with the TXD output): The input
trigger is expected to be a free-running clock (carrier signal) that generates from a timer or PWM source with a frequency
that is greater than the bit-clock frequency. The carrier signal must not toggle faster than the maximum supported bit time.
• By connecting the input trigger instead of the RXD pin input: The input trigger is expected to be generated from a receive
data source, such as an analog comparator or external pin.

---

*Page 3122*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)

#### 73.3.8 Infrared (IR) interface

LPUART provides the capability of transmitting narrow pulses to an IR LED and receiving narrow pulses, transforming them to
serial bits, which are then sent to LPUART. The IrDA physical layer specification defines a half-duplex IR communication link for
exchanging data. The full standard includes data rates up to 16 Mbit/s. The LPUART IrDA support is limited to SIR mode that
supports data rates only between 2.4 kbit/s and 115.2 kbit/s.
LPUART has an infrared transmit encoder and a receive decoder. The infrared decoder converts the received character from the
IrDA format to the NRZ format, which the receiver uses. It also has an OSR oversampling baud rate clock counter that filters noise
and indicates when a 1 is received. LPUART transmits serial bits of data, which the infrared submodule encodes, to transmit a
narrow pulse for every zero bit. No pulse is transmitted for every single bit. When receiving data, an IR photo diode (external to
LPUART) detects the IR pulses. The IR receive decoder transforms them to CMOS levels. The infrared receive decoder then
stretches the narrow pulses to get back to a serial bit stream that LPUART receives. You can invert the polarity of transmitted
pulses and expected receive pulses so that a direct connection can be made to external IrDA transceiver modules that use
active-high pulses.
The IR submodule receives its clock sources from LPUART. The submodule selects one of these clocks to generate either 1 ÷
OSR, 2 ÷ OSR, 3 ÷ OSR, or 4 ÷ OSR narrow pulses during transmission.
73.3.8.1 Infrared transmit encoder
The infrared transmit encoder converts serial bits of data from the transmit shift register to the TXD signal. A narrow pulse is
transmitted for a 0 bit and no pulse is transmitted for a 1 bit. The narrow pulse is sent at the start of the bit with a duration of 1 ÷
OSR, 2 ÷ OSR, 3 ÷ OSR, or 4 ÷ OSR of a bit time. A narrow low pulse is transmitted for a 0 bit when CTRL[TXINV] is 0, while a
narrow high pulse is transmitted for a 0 bit when CTRL[TXINV] is 1.
73.3.8.2 Infrared receive decoder
The infrared receive block converts data from the RXD signal to the receive shift register. A narrow pulse is expected for each 0
received and no pulse is expected for each 1 received. A narrow low pulse is expected for a 0 bit when STAT[RXINV] is 0, while
a narrow high pulse is expected for a 0 bit when STAT[RXINV] is 1. This receive decoder meets the edge jitter requirement as
defined by the IrDA serial infrared physical layer specification.
73.3.8.3 Start-bit detection
When STAT[RXINV] is 0, the first falling edge of the received character corresponds to the start bit. The infrared decoder resets its
counter. At this time, the receiver also begins its start bit detection process. After the start bit is detected, the receiver synchronizes
its bit times to this start bit time. For the rest of the character reception, the infrared decoder's counter and the receiver's bit time
counter count independently of each other.
73.3.8.4 Noise filtering
The decoder ignores any rising edges detected during the first half of the infrared decoder counter, and can leave any pulses less
than one oversampling baud clock as undetected. This is regardless of whether the pulse is seen in the first or second half of
the count.
73.3.8.5 Low-bit detection
During the second half of the decoder count, a rising edge is decoded as 0, which is sent to the receiver. The decoder counter is
also reset.
73.3.8.6 High-bit detection
At OSR oversampling baud rate clocks after the previous rising edge, if a rising edge is not seen, the decoder sends a 1 to
the receiver.
If the next bit is 0, which arrives late, a low bit is detected according to Low-bit detection . The value sent to the receiver is changed
from 1 to 0. Then, if a noise pulse occurs outside the receiver's bit time sampling period, the delay of a 0 is not recorded as noise.

---

*Page 3123*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)

#### 73.3.9 Modes of operation

73.3.9.1 Low-Power modes
LPUART remains functional during low-power modes, provided CTRL[DOZEEN] is 0 and the LPUART functional clock is enabled.
LPUART can generate an interrupt or DMA request to cause a wake-up from low-power modes.
You can configure LPUART to be disabled in low-power modes, when CTRL[DOZEEN] is 1. In this case, the transmitter and
receiver finish transmitting and receiving the current word.
If LPUART is disabled in low-power modes, it can generate a wake-up via STAT[RXEDGIF] if the receiver detects an active edge.
NOTE
See the chip-specific information for specific low-power modes available on your chip.
73.3.9.2 Debug mode
LPUART remains functional in Debug mode.

#### 73.3.10 Clocking

Table 547. Types of clocks
Clock Description
Functional Is asynchronous to the bus clock and can remain enabled in Low-Power modes to support transmit
and/or receive functions, including low-power wake-up.
Bus Is only used for bus accesses to the control and configuration registers. The bus clock frequency
must be sufficient to support the data bandwidth requirements of the LPUART transmit and receive
registers, including the FIFOs.

#### 73.3.11 Reset

Table 548. Types of resets
Reset Description
Chip Enables the logic and registers for the LPUART transmitter and receiver to reset to their default
states.
Software
Resets the LPUART logic and registers to their default states, except for Global (GLOBAL) .
GLOBAL[RST] controls the LPUART software reset.
FIFO Implements write-only control fields that reset the transmit FIFO ( FIFO[TXFLUSH] ) and receive
FIFO ( FIFO[RXFLUSH] ). After a FIFO is reset, that FIFO becomes empty.

#### 73.3.12 Interrupts

The LPUART transmitter has two status fields that can optionally generate hardware interrupt requests. If STAT[TDRE] is 1, it
indicates that there is room in the transmit FIFO to write another transmit character to Data (DATA) . If CTRL[TIE] is 1, a hardware
interrupt is requested when STAT[TDRE] is 1.
STAT[TC] indicates that the transmitter is finished transmitting all data, preamble, and break characters and is idle with TXD at the
inactive level. This field is often used in systems with modems to determine when it is safe to turn off the modem. If CTRL[TCIE] is
1, a hardware interrupt is requested when STAT[TC] is 1. Instead of hardware interrupts, software polling may be used to monitor
STAT[TDRE] and STAT[TC] if the corresponding CTRL[TIE] or CTRL[TCIE] field is 0.

---

*Page 3124*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
When a program detects that STAT[RDRF] is 1, it gets the data from this field by reading Data (DATA) . The field becomes 0 by
reading Data (DATA) .
STAT[IDLE] includes logic that prevents it from becoming 1 repeatedly when the RXD line remains idle for an extended period of
time. STAT[IDLE] becomes 0 when you write 1 to it, and cannot become 1 again until the receiver has received at least one new
character and has 1 as the value of STAT[RDRF] .
If the associated error is detected in the received character that caused STAT[RDRF] to become 1, STAT[NF] , STAT[FE] , and
STAT[PF] become 1 at the same time STAT[RDRF] becomes 1. These flags do not become 1 in overrun cases.
If STAT[RDRF] is already 1 when a new character is ready to be transferred from the receive shifter to the receive FIFO, STAT[OR]
becomes 1, instead of the data along with any associated STAT[NF] , STAT[FE] , or STAT[PF] condition getting lost.
If the received character matches the contents of MATCH[MA1] and/or MATCH[MA2] , then STAT[MA1F] and/or STAT[MA2F]
become 1 at the same time that STAT[RDRF] becomes 1.
At any time, an active edge on the RXD serial data input pin causes STAT[RXEDGIF] to become 1. STAT[RXEDGIF] becomes
0 when you write 1 to it. This function depends on the receiver being enabled (the value of CTRL[RE] being 1).

#### 73.4 External signals

Table 549. External signals
Signal Description I/O
TXD Transmit data: This pin is normally I/O
an output, but is an input (tristated)
in Single-Wire mode whenever the
transmitter is disabled or the transmit
direction is configured for receive data.
RXD Receive data I
CTS_B Clear-to-send I
RTS_B Request-to-send O

#### 73.5 Initialization

This module does not require initialization.

#### 73.6 LPUART register descriptions

LPUART includes registers to control baud rate, select options, report status, and store transmit and receive data. Access to an
address outside the valid memory map generates a bus error.
NOTE
Writing to a read-only (RO) register or reading a write-only (WO) register can cause bus errors. LPUART does not
verify whether programmed values in the registers are correct; you must write valid values to them.

#### 73.6.1 LPUART memory map

LPUART_0 base address: 4032_8000h
LPUART_1 base address: 4032_C000h
LPUART_2 base address: 4033_0000h
LPUART_3 base address: 4033_4000h
LPUART_4 base address: 4033_8000h
LPUART_5 base address: 4033_C000h

---

*Page 3125*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
LPUART_6 base address: 4034_0000h
LPUART_7 base address: 4034_4000h
LPUART_8 base address: 4048_C000h
LPUART_9 base address: 4049_0000h
LPUART_10 base address: 4049_4000h
LPUART_11 base address: 4049_8000h
LPUART_12 base address: 4049_C000h
LPUART_13 base address: 404A_0000h
LPUART_14 base address: 404A_4000h
LPUART_15 base address: 404A_8000h
Offset Register Access Reset value
Width
(In bits)
0h Version ID (VERID) 32 R 0403_0003h
4h Parameter (PARAM) 32 R 0000_0202h
8h Global (GLOBAL) 32 RW 0000_0000h
Ch Pin Configuration (PINCFG) 32 RW 0000_0000h
10h Baud Rate (BAUD) 32 RW 0F00_0004h
14h Status (STAT) 32 RW 00C0_0000h
18h Control (CTRL) 32 RW 0000_0000h
1Ch Data (DATA) 32 RW 0000_1000h
20h Match Address (MATCH) 32 RW 0000_0000h
24h MODEM IrDA (MODIR) 32 RW 0000_0000h
28h FIFO (FIFO) 32 RW 00C0_0011h
2Ch Watermark (WATER) 32 RW 0000_0000h
30h Data Read-Only (DATARO) 32 R 0000_1000h

#### 73.6.2 Version ID (VERID)

Offset
Register Offset
VERID 0h
Function
Indicates the version integrated for this instance on the chip and also specifies the inclusion and exclusion of several optional
features.

---

*Page 3126*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R MAJOR MINOR
W
Reset 0 0 0 0 0 1 0 0 0 0 0 0 0 0 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R FEATURE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1
Fields
Field Function
31-24 Major Version Number
MAJOR Indicates the major version number for the module specification.
23-16 Minor Version Number
MINOR Indicates the minor version number for the module specification.
15-0 Feature Identification Number
FEATURE Indicates the feature set number.
0000_0000_0000_0001b - Standard feature set
0000_0000_0000_0011b - Standard feature set with MODEM and IrDA support

#### 73.6.3 Parameter (PARAM)

Offset
Register Offset
PARAM 4h
Function
Indicates the parameter configuration for this instance on the chip.

---

*Page 3127*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXFIFO TXFIFO
W
Reset 0 0 0 0 0 0 1 0 0 0 0 0 0 0 1 0
Fields
Field Function
Reserved
31-16
—
15-8 Receive FIFO Size
RXFIFO Indicates the number of characters in the receive FIFO, which is 2^RXFIFO.
7-0 Transmit FIFO Size
TXFIFO Indicates the number of characters in the transmit FIFO, which is 2^TXFIFO.

#### 73.6.4 Global (GLOBAL)

Offset
Register Offset
GLOBAL 8h
Function
Performs global functions.

---

*Page 3128*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
RST
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-2
—
1 Software Reset
RST Specifies whether the module is reset.
This field resets all internal logic and registers, except Global (GLOBAL) . The reset takes effect immediately
and remains asserted until you negate it. There is no minimum delay required before clearing the
software reset.
0b - Not reset
1b - Reset
Reserved
0
—

#### 73.6.5 Pin Configuration (PINCFG)

Offset
Register Offset
PINCFG Ch
Function
Enables the selection of input pins.

---

*Page 3129*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
TRGSEL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-2
—
1-0 Trigger Select
TRGSEL Configures the input trigger usage.
You must change the value of this field only when both the transmitter and receiver are disabled.
00b - Input trigger disabled
01b - Input trigger used instead of the RXD pin input
10b - Input trigger used instead of the CTS_B pin input
11b - Input trigger used to modulate the TXD pin output, which (after TXINV configuration) is
internally ANDed with the input trigger

#### 73.6.6 Baud Rate (BAUD)

Offset
Register Offset
BAUD 10h
Function
Configures the baud rate.

---

*Page 3130*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
Reserv
R 0
MAEN MAEN TDMA RDMA BOTH RESY
ed
M10 OSR MATCFG
1 2 E E EDGE NCD ...
W 0
Reset 0 0 0 0 1 1 1 1 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
LBKDI RXED
SBNS SBR
E GIE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0 0
Fields
Field Function
31 Match Address Mode Enable 1
MAEN1 Enables automatic address matching or data matching mode for MATCH[MA1] . If this field is 0, normal
operation takes place.
0b - Disable
1b - Enable
30 Match Address Mode Enable 2
MAEN2 Enables automatic address matching or data matching mode for MATCH[MA2] . If this field is 0, normal
operation takes place.
0b - Disable
1b - Enable
29 10-Bit Mode Select
M10 Causes the tenth bit to be a part of the serial transmission.
You must change the value of this field only when both the transmitter and receiver are disabled.
0b - Receiver and transmitter use 7-bit to 9-bit data characters
1b - Receiver and transmitter use 10-bit data characters
28-24 Oversampling Ratio
OSR Configures the OSR of the receiver.
You must change the value of this field only when both the transmitter and receiver are disabled.
NOTE
BAUD[OSR] results in an OSR of BAUD[OSR] + 1, for example, BAUD[OSR] = 0_0101b
results in a final division by 6.
0_0000b - Results in an OSR of 16
Table continues on the next page...

---

*Page 3131*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
0_0001b - Reserved
0_0010b - Reserved
0_0011b - Results in an OSR of 4 (requires BAUD[BOTHEDGE] to be 1)
0_0100b - Results in an OSR of 5 (requires BAUD[BOTHEDGE] to be 1)
0_0101b - Results in an OSR of 6 (requires BAUD[BOTHEDGE] to be 1)
0_0110b - Results in an OSR of 7 (requires BAUD[BOTHEDGE] to be 1)
0_0111b - Results in an OSR of 8
0_1000b - Results in an OSR of 9
0_1001b - Results in an OSR of 10
0_1010b - Results in an OSR of 11
0_1011b - Results in an OSR of 12
0_1100b - Results in an OSR of 13
0_1101b - Results in an OSR of 14
0_1110b - Results in an OSR of 15
0_1111b - Results in an OSR of 16
1_0000b - Results in an OSR of 17
1_0001b - Results in an OSR of 18
1_0010b - Results in an OSR of 19
1_0011b - Results in an OSR of 20
1_0100b - Results in an OSR of 21
1_0101b - Results in an OSR of 22
1_0110b - Results in an OSR of 23
1_0111b - Results in an OSR of 24
1_1000b - Results in an OSR of 25
1_1001b - Results in an OSR of 26
1_1010b - Results in an OSR of 27
1_1011b - Results in an OSR of 28
1_1100b - Results in an OSR of 29
1_1101b - Results in an OSR of 30
1_1110b - Results in an OSR of 31
1_1111b - Results in an OSR of 32
23 Transmitter DMA Enable
TDMAE Enables STAT[TDRE] to generate a DMA request.
Table continues on the next page...

---

*Page 3132*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
0b - Disable
1b - Enable
Reserved
22
—
21 Receiver Full DMA Enable
RDMAE Enables STAT[RDRF] to generate a DMA request.
0b - Disable
1b - Enable
Reserved
20
—
19-18 Match Configuration
MATCFG Configures the match addressing mode used.
You must change the value of this field only when both the transmitter and receiver are disabled.
00b - Address match wake-up
01b - Idle match wake-up
10b - Match on and match off
11b - Enables RWU on data match and match on or off for the transmitter CTS input
17 Both Edge Sampling
BOTHEDGE Enables sampling of the received data on both edges of the baud rate clock, effectively doubling the number
of times the receiver samples the input data for a given OSR.
This field must be 1 for OSRs between x4 and x7 and is optional for higher OSRs. You must change the
value of this field only when the receiver is disabled.
If this field is 0, the receiver samples input data using the rising edge of the baud rate clock. If this field is 1,
the receiver samples input data using the rising and falling edges of the baud rate clock.
0b - Rising edge
1b - Both rising and falling edges
16 Resynchronization Disable
RESYNCDIS Disables resynchronization of the received data word when a data one followed by data zero transition
is detected.
You must change the value of this field only when the receiver is disabled.
0b - Enable
1b - Disable
15 LIN Break Detect Interrupt Enable
Table continues on the next page...

---

*Page 3133*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
LBKDIE Enables STAT[LBKDIF] to generate hardware interrupt requests.
If this field is 0, hardware interrupts from STAT[LBKDIF] (uses polling) are disabled. If this field is 1, hardware
interrupts are requested when STAT[LBKDIF] is 1.
0b - Disable
1b - Enable
14 RX Input Active Edge Interrupt Enable
RXEDGIE Enables STAT[RXEDGIF] to generate interrupt requests. If this field is 0, hardware interrupts from
STAT[RXEDGIF] are disabled. If this field is 1, hardware interrupts are requested when STAT[RXEDGIF]
is 1.
Changing the value of CTRL[LOOPS] or CTRL[RSRC] when this field (RXEDGIE) is 1 can cause
STAT[RXEDGIF] to become 1.
0b - Disable
1b - Enable
13 Stop Bit Number Select
SBNS Determines whether data characters include one or two stop bits.
You must change the value of this field only when both the transmitter and receiver are disabled.
0b - One stop bit
1b - Two stop bits
12-0 Baud Rate Modulo Divisor
SBR Sets the modulo divide rate for the baud rate generator.
• If SBR is 0, baud rate generator is disabled.
• If SBR is 1–8191, baud rate = baud clock ÷ ((OSR + 1) × SBR). You must update the 13-bit baud
rate setting [SBR12:SBR0] only when both the transmitter and receiver are disabled (both CTRL[RE]
and CTRL[TE] are 0).

#### 73.6.7 Status (STAT)

Offset
Register Offset
STAT 14h
Function
Provides the module status.

---

*Page 3134*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
LBKDI RXED
R RAF TDRE TC RDRF IDLE OR NF FE PF
RWUI LBKD
F GIF
MSBF RXINV BRK13
D E
W W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 1 1 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R MA1F MA2F 0 0 0
AME LBKFE
W W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 LIN Break Detect Interrupt Flag
LBKDIF Indicates whether a LIN break character is detected.
This field becomes 1 when the LIN break detect circuitry is enabled and a LIN break character is detected.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not detected
1b - Detected
When writing
0b - No effect
1b - Clear the flag
30 RXD Pin Active Edge Interrupt Flag
RXEDGIF Indicates whether an active edge on the receive pin has occurred.
This field becomes 1 whenever the receiver is enabled and an active edge (falling if STAT[RXINV] is 0; rising
if STAT[RXINV] is 1) on the RXD pin occurs.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not occurred
1b - Occurred
When writing
0b - No effect
Table continues on the next page...

---

*Page 3135*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
1b - Clear the flag
29 MSB First
MSBF Specifies the first bit that is transmitted after the start bit.
If this field is 0, LSB (bit 0) is the first bit transmitted after the start bit (which means, the first bit received after
the start bit is identified as bit 0).
If this field is 1, MSB (identified as bit 9, bit 8, bit 7, or bit 6) is the first bit that is transmitted, after the start
bit, depending on the settings of CTRL[M] , CTRL[PE] , and BAUD[M10] .
Writing 1 to this field reverses the order of the bits that are transmitted and received on the wire. This field
does not affect the polarity of the bits, the location of the parity bit, or the location of the start or stop bits.
You must change the value of this field only when both the transmitter and receiver are disabled.
0b - LSB
1b - MSB
28 Receive Data Inversion
RXINV Specifies whether receive data is inverted.
Writing 1 to this field reverses the polarity of the received data input. You must change the value of this field
only when the receiver is disabled.
NOTE
Writing 1 to this field inverts the RXD input for all cases: data bits, start and stop bits, break,
and idle.
0b - Inverted
1b - Not inverted
27 Receive Wake Up Idle Detect
RWUID Controls, for CTRL[RWU] on idle character detection, whether the idle character that wakes up the receiver
writes 1 to STAT[IDLE] .
For address match wake-up, this field controls whether STAT[IDLE] = 1 when the address does not match.
You must change the value of this field only when the receiver is disabled.
If this field is 0, during the Receive Standby state ( CTRL[RWU] = 1), STAT[IDLE] does not become 1 upon
detection of an idle character. During address match wake-up, STAT[IDLE] does not become 1 when an
address does not match.
If this field is 1, during the Receive Standby state ( CTRL[RWU] = 1), STAT[IDLE] becomes 1 upon detection
of an idle character. During address match wake-up, STAT[IDLE] becomes 1 when an address does
not match.
0b - STAT[IDLE] does not become 1
1b - STAT[IDLE] becomes 1
26 Break Character Generation Length
BRK13 Selects the longer transmitted break character length.
Table continues on the next page...

---

*Page 3136*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
The state of this field does not affect the detection of a framing error. You must change the value of this
field only when the transmitter is disabled. You can send a break character by writing 1 to CTRL[SBK] , or
by writing the transmit FIFO when DATA[FRETSC] is 1 and DATA[R9T9] is 0.
0b - 9 to 13 bit times
1b - 12 to 15 bit times
25 LIN Break Detection Enable
LBKDE Enables LIN break detection.
If this field is 0, LIN break detect is disabled, and only a normal break character can be detected.
If this field is 1, LIN break detect is enabled and the LIN break character is detected at a length of 11 bit times
(if CTRL[M] is 0), 12 bit times (if CTRL[M] is 1), or 13 bit times (if BAUD[M10] is 1).
This field selects a longer break character detection length. When the field is 1, receive data is not stored
in the receive FIFO.
NOTE
This field enables the LIN break detect circuit and disables writing receive data to FIFO.
Therefore, it ignores all characters except a LIN break.
0b - Disable
1b - Enable
24 Receiver Active Flag
RAF Indicates whether the LPUART receiver is idle or active.
This field becomes 1 when the receiver detects the beginning of a valid start bit, and the field becomes 0
automatically when the receiver detects an idle line.
0b - Idle, waiting for a start bit
1b - Receiver active (RXD pin input not idle)
23 Transmit Data Register Empty Flag
TDRE Indicates whether the transmit FIFO level is greater than, equal to, or less than the watermark.
After the transmit FIFO is enabled, this field becomes 1 when the number of datawords in the transmit FIFO
is equal to, or less than the number that WATER[TXWATER] indicates. To make the value of this field 0,
write to it until the number of words in the transmit FIFO is greater than the number that WATER[TXWATER]
indicates. After the transmit FIFO is disabled, this field becomes 1 to indicate that the FIFO level is less than
the watermark. To make the value of this field 0 again, write to Data (DATA) .
This register is not affected by a character that is in the process of being transmitted; it is updated at the start
of each transmitted character.
0b - Greater than watermark
1b - Equal to or less than watermark
22 Transmission Complete Flag
Indicates whether the transmitter is active.
Table continues on the next page...

---

*Page 3137*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
TC This field becomes 0 when a transmission is in progress or a preamble or break character is loaded; in other
words, when the transmitter is active (sending data, a preamble, or a break). The field becomes 1 when the
transmit buffer is empty and no data, preamble, or break character is being transmitted; in other words, when
the transmission activity is complete. When this happens, the transmit data output signal becomes idle (logic
1). This field becomes 0 after you write to Data (DATA) to transmit new data, queuing a preamble by first
writing 0 and then writing 1 to CTRL[TE] , queuing a break character by writing 1 to CTRL[SBK] .
0b - Transmitter active
1b - Transmitter idle
21 Receive Data Register Full Flag
RDRF Indicates whether the receive FIFO level is less than, equal to, or greater than the watermark.
This field becomes 1 when the number of datawords in the receive buffer is greater than the number
that WATER[RXWATER] indicates and the receive FIFO is enabled. To write 0 to this field, read Data
(DATA) until the number of datawords in the receive FIFO is equal to, or less than the number that
WATER[RXWATER] indicates. When the receive FIFO is disabled, this field (RDRF) becomes 1 if the
receive buffer ( Data (DATA) ) is full. To make this field 0, read Data (DATA) .
A character that is in the process of being received does not cause a change in this field until the entire
character is received. Even if this field is 1, the character continues to be received until an overrun condition
occurs after the entire character is received.
0b - Equal to or less than watermark
1b - Greater than watermark
20 Idle Line Flag
IDLE Indicates whether an idle line is detected.
This field becomes 1 when the LPUART receive line becomes idle for a full character time after a period
of activity. When CTRL[ILT] is 0, the receiver starts counting idle bit times after the start bit. If the receive
character is all 1s, these bit times and the stop bit time count towards the full character time of logic high, 10
to 13 bit times, needed for the receiver to detect an idle line. After CTRL[ILT] becomes 1, the receiver does
not start counting idle bit times until after the stop bits. The stop bits and any logic high bit times at the end
of the previous character do not count towards the full character time of logic high needed for the receiver
to detect an idle line.
For this field to become 0, write 1 to it. After the field becomes 0, you cannot write 1 to it again until after a
new character is stored in the receive buffer or a LIN break character writes 1 to STAT[LBKDIF] . This field
becomes 1 only once, even if the receive line remains idle for an extended period.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Idle line detected
1b - Idle line not detected
When writing
Table continues on the next page...

---

*Page 3138*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
0b - No effect
1b - Clear the flag
19 Receiver Overrun Flag
OR Indicates whether there is receive overrun.
This field becomes 1 when you cannot prevent STAT[RDRF] from overflowing with data. The field becomes
1 immediately after the stop bit is completely received for the dataword that overflows the buffer and all the
other error fields ( STAT[FE] , STAT[NF] , and STAT[PF] ) are prevented from becoming 1. The data in the shift
register is lost, but the data already in the LPUART data registers is not affected. If STAT[LBKDE] is enabled
and a LIN break is detected, this field becomes 1 if STAT[LBKDIF] is not 0 before the next data character
is received.
When this field is 1, no additional data is stored in the receive FIFO even if sufficient room exists.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - No overrun
1b - Receive overrun (new LPUART data is lost)
When writing
0b - No effect
1b - Clear the flag
18 Noise Flag
NF Indicates whether noise is detected in the received character of Data (DATA) .
The advanced sampling technique used in the receiver takes three samples in each of the received bits. If
some of these samples disagree with the rest of the samples within any bit time in the frame, then noise is
detected for that character. This field becomes 1 whenever the next character to be read from Data (DATA)
is received with noise detected within the character.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - No noise detected
1b - Noise detected
When writing
0b - No effect
1b - Clear the flag
17 Framing Error Flag
FE Indicates whether a framing error is detected.
Table continues on the next page...

---

*Page 3139*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
This field becomes 1 whenever the next character to be read from Data (DATA) is received with logic 0
detected where a stop bit was expected.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - No framing error detected (this does not guarantee that the framing is correct)
1b - Framing error detected
When writing
0b - No effect
1b - Clear the flag
16 Parity Error Flag
PF Indicates whether a parity error is detected.
This field becomes 1 whenever the next character to be read from Data (DATA) is received when parity
is enabled ( CTRL[PE] is 1) and the parity bit in the received character does not agree with the expected
parity value.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - No parity error detected
1b - Parity error detected
When writing
0b - No effect
1b - Clear the flag
15 Match 1 Flag
MA1F Indicates whether the received data is equal to MATCH[MA1] .
This field becomes 1 whenever the next character to be read from Data (DATA) matches the value
of MATCH[MA1] .
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not equal to MA1
1b - Equal to MA1
When writing
Table continues on the next page...

---

*Page 3140*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
0b - No effect
1b - Clear the flag
14 Match 2 Flag
MA2F Indicates whether the received data is equal to MATCH[MA2] .
This field becomes 1 whenever the next character to be read from Data (DATA) matches the value
of MATCH[MA2] .
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not equal to MA2
1b - Equal to MA2
When writing
0b - No effect
1b - Clear the flag
Reserved
13-10
—
Reserved
9-8
—
Reserved
7-2
—
1 Address Mark Enable
AME Configures the location of the address mark when configured for MSB first transfers.
This field has no effect when configured for LSB first and you must change the value of this field only when
both the transmitter and receiver are disabled. If this field is 0, address mark in character is MSB. If this field
is 1, the address mark is stored in Data (DATA) at MSB (or MSB-1 when the parity bit is enabled). In other
words, the address mark in character is the last bit before the stop bit (or parity bit when enabled).
0b - Disable
1b - Enable
0 LIN Break Flag Enable
LBKFE Enables the LIN break flag to assert whenever a LIN break character is detected.
Unlike STAT[LBKDE] , this does not impact data being stored in the receive data buffer, but does cause
STAT[LBKDIF] to become 1 whenever a LIN break is detected.
Table continues on the next page...

---

*Page 3141*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
Because a LIN break is longer than a normal character, the LIN break triggers a write to STAT[RDRF] with
the data fields as 0 and STAT[FE] as 1. The character following the LIN break has DATA[LINBRK] as 1 to
indicate that the previous character was a LIN break.
You must change the value of this field only when both the transmitter and receiver are disabled.
If this field is 1, the LIN break character is detected at a length of 11-bit times (if CTRL[M] is 0), 12 (if CTRL[M]
is 1), or 13 (if BAUD[M10] is 1).
0b - Disable
1b - Enable

#### 73.6.8 Control (CTRL)

Offset
Register Offset
CTRL 18h
Function
Controls various optional features of the LPUART system.
You must write to the fields of this register only when both the transmitter and receiver are disabled.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
R8T9 R9T8 TXDIR TXINV ORIE NEIE FEIE PEIE TIE TCIE RIE ILIE TE RE RWU SBK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
LOOP DOZE
MA1IE MA2IE M7 IDLECFG RSRC M WAKE ILT PE PT
S EN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Receive Bit 8 Transmit Bit 9
R8T9 Contains R8 and T9 that correspond to different functions.
Table continues on the next page...

---

*Page 3142*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
R8 is the ninth data bit received after you configure LPUART for 9-bit or 10-bit data formats. When reading
9-bit or 10-bit data, read R8 before reading Data (DATA) .
T9 is the tenth data bit transmitted after you configure LPUART for 10-bit data formats. When writing 10-bit
data, write T9 before writing to Data (DATA) . If T9 does not need to change from its previous value, such
as when it is used to generate address mark or parity, then you need not write to it each time you write to
Data (DATA) .
NOTE
R8 is a read-only bit and T9 is a write-only bit; the value read is different from the
value written.
30 Receive Bit 9 Transmit Bit 8
R9T8 Contains R9 and T8 that correspond to different functions.
R9 is the tenth data bit received after you configure LPUART for 10-bit data formats. When reading 10-bit
data, read R9 before reading Data (DATA) .
T8 is the ninth data bit transmitted after you configure LPUART for 9-bit or 10-bit data formats. When writing
9-bit or 10-bit data, write T8 before writing to Data (DATA) . If T8 does not need to change from its previous
value, such as when it is used to generate address mark or parity, then you need not write to it each time
you write to Data (DATA) .
NOTE
R9 is a read-only field and T8 is a write-only field; the value read is different from the
value written.
29 TXD Pin Direction in Single-Wire Mode
TXDIR Determines the direction of data at the TXD pin, in Single-Wire mode, when LPUART is configured for a
single-wire half-duplex operation ( CTRL[LOOPS] and CTRL[RSRC] are 1). When writing 0 to this field,
the transmitter finishes transmitting the current character (if any) before the receiver starts receiving data
from the TXD pin.
0b - Input
1b - Output
28 Transmit Data Inversion
TXINV Specifies whether transmit data is inverted.
Writing 1 to this field reverses the polarity of the transmitted data output. This action inverts the TXD output
for all cases: data bits, start and stop bits, break, and idle.
0b - Not inverted
1b - Inverted
27 Overrun Interrupt Enable
ORIE Enables STAT[OR] to generate hardware interrupt requests. When this field is 1, a hardware interrupt is
requested. Use polling when OR interrupts are disabled.
Table continues on the next page...

---

*Page 3143*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
0b - Disable
1b - Enable
26 Noise Error Interrupt Enable
NEIE Enables STAT[NF] to generate hardware interrupt requests. When this field is 1, a hardware interrupt is
requested. Use polling when NF interrupts are disabled.
0b - Disable
1b - Enable
25 Framing Error Interrupt Enable
FEIE Enables STAT[FE] to generate hardware interrupt requests. When this field is 1, a hardware interrupt is
requested. Use polling when FE interrupts are disabled.
0b - Disable
1b - Enable
24 Parity Error Interrupt Enable
PEIE Enables STAT[PF] to generate hardware interrupt requests. When this field is 1, a hardware interrupt is
requested. Use polling when PF interrupts are disabled.
0b - Disable
1b - Enable
23 Transmit Interrupt Enable
TIE Enables STAT[TDRE] to generate interrupt requests if STAT[TDRE] is 1.
0b - Disable
1b - Enable
22 Transmission Complete Interrupt Enable
TCIE Enables STAT[TC] to generate interrupt requests if STAT[TC] is 1.
0b - Disable
1b - Enable
21 Receiver Interrupt Enable
RIE Enables STAT[RDRF] to generate hardware interrupt requests if STAT[RDRF] is 1.
0b - Disable
1b - Enable
20 Idle Line Interrupt Enable
ILIE Enables hardware interrupts.
This field enables STAT[IDLE] to generate interrupt requests.
Table continues on the next page...

---

*Page 3144*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
If this field is 0, hardware interrupts from STAT[IDLE] are disabled and polling is used, and if this field is 1,
hardware interrupts are enabled when STAT[IDLE] is 1.
0b - Disable
1b - Enable
19 Transmitter Enable
TE Enables the LPUART transmitter.
Using this field, you can also queue an idle preamble by first writing 0 and then writing 1 to this field. After
this field becomes 0, the field reads 1 until the transmitter has completed the current character and the TXD
pin is tristated.
You can also queue a single idle character by writing to the transmit FIFO with DATA[FRETSC] and
DATA[R9T9] = 1.
0b - Disable
1b - Enable
18 Receiver Enable
RE Enables the LPUART receiver.
After you write 0 to this field, this field remains 1 until the receiver finishes receiving the current character
(if any).
0b - Disable
1b - Enable
17 Receiver Wake-Up Control
RWU Specifies whether the LPUART receiver in standby is waiting for a wake-up condition.
You can write 1 to this field to place the LPUART receiver in a Standby state. The field becomes 0
automatically when an RWU event occurs, that is, in case of an idle event when CTRL[WAKE] is 0 or an
address match when CTRL[WAKE] is 1 and STAT[RWUID] is 0.
NOTE
You must write 1 to this field only when CTRL[WAKE] is 0 (wake-up on idle), if the channel
is currently not idle. You can determine this by the value of STAT[RAF] . If the field is 1 to
wake up an idle event and the channel is already idle, LPUART, possibly, discards the data.
This is because the data must be received or a LIN break is detected after an Idle condition
is detected before the IDLE flag is allowed to be reasserted.
0b - Normal receiver operation
1b - LPUART receiver in standby, waiting for a wake-up condition
16 Send Break
SBK Specifies whether queue break character(s) are to be sent.
Writing 1 and then 0 to this field queues a break character in the transmit data stream. Additional break
characters of 9 to 13 bits, or 12 to 15 bits if STAT[BRK13] is 1, and bit times of logic 0 are queued as long
Table continues on the next page...

---

*Page 3145*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
as this field is 1. Depending on the timing when this field is 1 and 0, relative to the character currently being
transmitted, a second break character may be queued before you write 0 to this field. If the time taken to write
0 to this field is too long, for example, if the field does not become 0 by the end of the first break character,
a second break character is sent. This is compared to queuing a break character through the transmit FIFO
that guarantees only one break character is sent.
You can also queue a single break character by writing to the transmit FIFO when DATA[FRETSC] is 1 and
DATA[R9T9] is 0.
0b - Normal transmitter operation
1b - Queue break character(s) to be sent
15 Match 1 (MA1F) Interrupt Enable
MA1IE Enables the MA1F interrupt.
0b - Disable
1b - Enable
14 Match 2 (MA2F) Interrupt Enable
MA2IE Enables the MA2F interrupt.
0b - Disable
1b - Enable
Reserved
13
—
Reserved
12
—
11 7-Bit Mode Select
M7 Specifies the data characters that the receiver and transmitter use.
You must change the value of this field only after both the transmitter and receiver are disabled.
0b - 8-bit to 10-bit
1b - 7-bit
10-8 Idle Configuration
IDLECFG Configures the number of idle characters that must be received before you write 1 to STAT[IDLE] .
000b - 1
001b - 2
010b - 4
011b - 8
100b - 16
Table continues on the next page...

---

*Page 3146*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
101b - 32
110b - 64
111b - 128
7 Loop Mode Select
LOOPS Selects Loop mode.
After this field becomes 1, the RXD pin is disconnected from LPUART and the transmitter output is internally
connected to the receiver input. The transmitter and receiver must be enabled to use the loop function.
In Loop mode or Single-Wire mode, the transmitter outputs are internally connected to the receiver input
(see CTRL[RSRC] ).
0b - Normal operation: RXD and TXD use separate pins
1b - Loop mode or Single-Wire mode
6 Doze Mode
DOZEEN Enables LPUART in Doze mode.
If this field is 1, LPUART remains active when not in Doze mode.
0b - Enable
1b - Disable
5 Receiver Source Select
RSRC Determines the source of the receiver shift register input if CTRL[LOOPS] is 1.
This field has no effect unless CTRL[LOOPS] is 1.
If this field is 0, internal Loopback mode is selected. LPUART does not use the RXD pin.
If this field is 1, single-wire LPUART mode is selected where the TXD pin is connected to the transmitter
output and receiver input.
0b - Internal Loopback mode
1b - Single-wire mode
4 9-Bit Or 8-Bit Mode Select
M Specifies the data characters that the receiver and transmitter use.
0b - 8-bit
1b - 9-bit
3 Receiver Wake-Up Method Select
WAKE Determines which condition wakes up LPUART when CTRL[RWU] = 1 and BAUD[MATCFG] = 0 (this field
must be 1 when BAUD[MATCFG] = 11):
• Address mark in the bit preceding the stop bit (or bit preceding the parity bit when parity is enabled)
of the received data character
Table continues on the next page...

---

*Page 3147*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
• An idle condition on the receive pin input signal
If this field is 0, CTRL[RWU] is configured for idle line wake-up, and if this field is 1, CTRL[RWU] is configured
with address mark wake-up.
0b - Idle
1b - Mark
2 Idle Line Type Select
ILT Determines when the receiver starts counting logic 1s as idle character bits.
The count begins either after a valid start bit or the stop bit. If the count begins after the start bit, a string of
logic 1s preceding the stop bit can cause false recognition of an idle character. Beginning the count after the
stop bit avoids false idle character recognition, but requires properly synchronized transmissions.
NOTE
In case you write 1 to this field, a logic 0 is automatically shifted after a received stop bit,
therefore resetting the idle count.
0b - After the start bit
1b - After the stop bit
1 Parity Enable
PE Enables hardware parity generation and checking.
If parity is enabled, the bit immediately before the stop bit is treated as the parity bit.
0b - Disable
1b - Enable
0 Parity Type
PT Selects the type of parity, even or odd, if parity is enabled ( CTRL[PE] = 1):
• Odd parity means that the total number of logic 1 bits in the data character, including the parity bit, is
odd.
• Even parity means that the total number of 1s in the data character, including the parity bit, is even.
0b - Even parity
1b - Odd parity

#### 73.6.9 Data (DATA)

Offset
Register Offset
DATA 1Ch

---

*Page 3148*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Function
Supports 8-bit, 16-bit, or 32-bit writes, each type of write performing a separate function. An 8-bit write to DATA[7:0] pushes
{ CTRL[R8T9] , CTRL[R9T8] , DATA[7:0]} the transmit FIFO with TSC clear. A 16-bit or 32-bit write pushes the data written into the
FIFO and does not update the value of CTRL[R8T9] or CTRL[R9T8] .
Reads and writes of this register are also involved in the automatic flag clearing mechanisms for some of the LPUART status fields.
NOTE
Reads return the contents of the read-only receive FIFO and writes go to the write-only transmit FIFO, making this
register work as a set of two separate registers.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
PARIT RXEM IDLIN LINBR
R NOISY
FRET
YE PT E K
R9T9 R8T8 R7T7 R6T6 R5T5 R4T4 R3T3 R2T2 R1T1 R0T0
SC
W
Reset 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-16
—
15 Noisy Data Received
NOISY Indicates whether the current received dataword contained in DATA[R9:R0] is received with noise.
0b - Received without noise
1b - Received with noise
14 Parity Error
PARITYE Indicates whether the current received dataword contained in DATA[R9:R0] is received with a parity
error.
0b - Received without a parity error
1b - Received with a parity error
13 Frame Error Transmit Special Character
FRETSC Specifies the way the dataword is received.
Table continues on the next page...

---

*Page 3149*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
For reads, this field indicates that the current received dataword contained in DATA[R9:R0] is received with
a frame error. For writes, the field indicates that a break or idle character is to be transmitted instead of the
contents in DATA[T9:T0]. T9 indicates a break character when it is 0 and indicates an idle character when
it is 1. The contents of DATA[T8:T0] must be 0.
0b - Received without a frame error on reads or transmits a normal character on writes
1b - Received with a frame error on reads or transmits an idle or break character on writes
12 Receive Buffer Empty
RXEMPT Indicates whether the receive buffer contains valid data.
This field becomes 1 when there is no data in the receive buffer. The field does not consider data in the
receive shift register.
0b - Valid data
1b - Invalid data and empty
11 Idle Line
IDLINE Indicates whether the receiver line was idle before receiving the character in DATA[9:0]. It can be read
as “1” on the first character when the receiver is first enabled. The difference between this field and
STAT[IDLE] is that, STAT[IDLE] flag does not set on an idle line after the receiver is first enabled, it
needs to receive a character before it can become set, whereas this field does not have this limitation
and can be set on the first character received if an idle line is detected beforehand.
0b - Not idle
1b - Idle
10 LIN Break
LINBRK Indicates whether the receiver line detected a LIN break before receiving the character in DATA[9:0].
This field requires the value of STAT[LBKDIF] to be 1. If this field is 0, the LIN break detect circuitry is
disabled.
0b - Not detected
1b - Detected
Read receive FIFO bit 9 or write transmit FIFO bit 9
9
R9T9
Read receive FIFO bit 8 or write transmit FIFO bit 8
8
R8T8
Read receive FIFO bit 7 or write transmit FIFO bit 7
7
R7T7
Read receive FIFO bit 6 or write transmit FIFO bit 6
6
R6T6
Table continues on the next page...

---

*Page 3150*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
Read receive FIFO bit 5 or write transmit FIFO bit 5
5
R5T5
Read receive FIFO bit 4 or write transmit FIFO bit 4
4
R4T4
Read receive FIFO bit 3 or write transmit FIFO bit 3
3
R3T3
Read receive FIFO bit 2 or write transmit FIFO bit 2
2
R2T2
Read receive FIFO bit 1 or write transmit FIFO bit 1
1
R1T1
Read receive FIFO bit 0 or write transmit FIFO bit 0
0
R0T0

#### 73.6.10 Match Address (MATCH)

Offset
Register Offset
MATCH 20h
Function
Provides addresses for address matching during the receiver operation.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
MA2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
MA1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 3151*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Fields
Field Function
Reserved
31-26
—
25-16 Match Address 2
MA2 Is compared to input data addresses when the most significant bit is 1 and the associated Baud Rate (BAUD)
field is 1.
If a match occurs, the data that follows is transferred to Data (DATA) . If a match fails, the data that follows is
discarded. You must write to MATCH[MA1] and MATCH[MA2] only when the associated Baud Rate (BAUD)
field is 0.
Reserved
15-10
—
9-0 Match Address 1
MA1 Is compared to input data addresses when the most significant bit is 1 and the associated Baud Rate (BAUD)
field is 1.
If a match occurs, the data that follows is transferred to Data (DATA) . If a match fails, the data that follows is
discarded. You must write to MATCH[MA1] and MATCH[MA2] fields only when the associated field in Baud
Rate (BAUD) is 0.

#### 73.6.11 MODEM IrDA (MODIR)

Offset
Register Offset
MODIR 24h
Function
Controls options for setting the MODEM configuration.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
IREN TNP
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
TXCT TXCT RXRT TXRT TXRT TXCT
RTSWATER
SSRC SC SE SPOL SE SE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 3152*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Fields
Field Function
Reserved
31-19
—
18 IR Enable
IREN Enables IR modulation and demodulation.
You must change the value of this field only when both the transmitter and receiver are disabled.
0b - Disable
1b - Enable
17-16 Transmitter Narrow Pulse
TNP Specifies whether LPUART transmits a 1 ÷ OSR, 2 ÷ OSR, 3 ÷ OSR, or 4 ÷ OSR narrow pulse when the IR
pulse is enabled.
You must change the value of this field only when both the transmitter and receiver are disabled.
The IR pulse width must be configured to less than half of the OSR. Common pulse widths are 3 ÷ 16, 1 ÷ 16,
1 ÷ 32, or 1 ÷ 4 of the bit length. You can configure these by selecting the appropriate OSR and pulse width.
00b - 1 ÷ OSR
01b - 2 ÷ OSR
10b - 3 ÷ OSR
11b - 4 ÷ OSR
Reserved
15-10
—
9-8 Receive RTS Configuration
RTSWATER Configures the assertion and negation of the receiver's RTS_B output.
The receiver's RTS_B output negates when the number of empty words in the receive FIFO is greater or
equal to the value of this field. If this field is 0, the RTS_B pin negates when the receive FIFO is full. For the
purpose of receive RTS_B generation, the number of words in the receive FIFO updates when a start bit is
detected. This supports additional latency between RTS_B negation and the external transmitter ceasing
transmission. If both receive RTS_B and address or data matching is enabled, RTS_B could assert at the
end of a character if there exists no match.
You must change the value of this field only when the receiver is disabled.
Reserved
7-6
—
5 Transmit CTS Source
TXCTSSRC Configures the source of the CTS input.
0b - The CTS_B pin
1b - An internal connection to the receiver address match result
Table continues on the next page...

---

*Page 3153*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
4 Transmit CTS Configuration
TXCTSC Configures whether the CTS state or input is checked or sampled at the start of each character or only
when the transmitter is idle.
0b - Sampled at the start of each character
1b - Sampled when the transmitter is idle
3 Receiver RTS Enable
RXRTSE Allows the RTS output to control the CTS input of the transmitting device to prevent receiver overrun.
You must change the value of this field only when the receiver is disabled.
If this field is 0, the receiver has no effect on RTS.
If this field is 1, RTS is deasserted if STAT[RDRF] is 1 or a start bit is detected that causes STAT[RDRF] to
become 1. RTS is asserted if STAT[RDRF] is 0 and has not detected a start bit that causes STAT[RDRF]
to become 1.
NOTE
Do not write 1 to both MODIR[RXRTSE] and MODIR[TXRTSE] .
0b - Disable
1b - Enable
2 Transmitter RTS Polarity
TXRTSPOL Controls the polarity of the transmitter RTS.
This field does not affect the polarity of the receiver RTS that remains negated in the active-low state unless
MODIR[TXRTSE] is 1. You must change the value of this field only when the transmitter is disabled.
0b - Active low
1b - Active high
1 Transmitter RTS Enable
TXRTSE Controls the operation of RTS before and after a transmission.
You must change the value of this field only when the transmitter is disabled. If this field is 0, the transmitter
has no effect on RTS, and if this field is 1, a character is placed into an empty transmit shift register.
RTS asserts 1-bit time before the start bit is transmitted and deasserts 1-bit time after all characters in the
transmitter FIFO and shift register are completely sent, including the last stop bit.
0b - Disable
1b - Enable
0 Transmitter CTS Enable
TXCTSE Enables the operation of the transmitter.
You can write 1 to this field irrespective of the states of MODIR[TXRTSE] and MODIR[RXRTSE] . If this
field is 1, the transmitter checks the state of the CTS signal each time it is ready to send a character. If
CTS is asserted, the character is sent. If CTS is deasserted, the TXD signal remains in the mark state and
Table continues on the next page...

---

*Page 3154*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
transmission is delayed until CTS is asserted. Changes in CTS, when a character is being sent, do not affect
its transmission.
0b - Disable
1b - Enable

#### 73.6.12 FIFO (FIFO)

Offset
Register Offset
FIFO 28h
Function
Provides you the ability to turn on and turn off the FIFO functionality.
This register also provides you the size of the FIFO that has been implemented. You can read this register at any time and must
write to it only when CTRL[RE] and CTRL[TE] are 0 and the FIFO is empty.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
TXEM RXEM
R 0 0 TXOF RXUF
PT PT
W W1C W1C
Reset 0 0 0 0 0 0 0 0 1 1 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 TXFIFOSIZE RXFIFOSIZE
TXOF RXUF
RXIDEN TXFE RXFE
TXFLU RXFL
E E
W
SH USH
Reset 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 1
Fields
Field Function
Reserved
31-24
—
23 Transmit FIFO Or Buffer Empty
TXEMPT Indicates whether the transmit buffer is empty.
Table continues on the next page...

---

*Page 3155*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
This field becomes 1 when there is no data in the transmit FIFO or buffer. The field does not consider data
in the transmit shift register.
0b - Not empty
1b - Empty
22 Receive FIFO Or Buffer Empty
RXEMPT Indicates whether the receive buffer is empty.
This field becomes 1 when there is no data in the receive FIFO or buffer. The field does not consider data
in the receive shift register.
0b - Not empty
1b - Empty
Reserved
21-18
—
17 Transmitter FIFO Overflow Flag
TXOF Indicates whether more data has been written to the transmit FIFO than it can hold.
If this field is 0, no transmit FIFO overflow has occurred since the last time the field was cleared, and if this
field is 1, at least one transmit FIFO overflow has occurred since the last time the field was cleared.
This field becomes 1 regardless of the value of FIFO[TXOFE] . However, an interrupt is issued to the host
only if FIFO[TXOFE] is 1.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - No overflow
1b - Overflow
When writing
0b - No effect
1b - Clear the flag
16 Receiver FIFO Underflow Flag
RXUF Indicates whether more data has been read from the receive FIFO than was present.
If this field is 0, no receive FIFO underflow has occurred since the last time the field was cleared, and if this
field is 1, at least one receive FIFO underflow has occurred since the last time the field was cleared.
This field becomes 1 regardless of the value of FIFO[RXUFE] . However, an interrupt is issued to the host
only if FIFO[RXUFE] is 1.
NOTE
This field behaves differently for register reads and writes.
Table continues on the next page...

---

*Page 3156*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
When reading
0b - No underflow
1b - Underflow
When writing
0b - No effect
1b - Clear the flag
15 Transmit FIFO Flush
TXFLUSH Causes all data that is stored in the transmit FIFO to be flushed.
If you write 0 to this field, no flush operation occurs, and if you write 1 to this field, all data in the transmit
FIFO or buffer clears out.
This does not affect data in the transmit shift register.
0b - No effect
1b - All data flushed out
14 Receive FIFO Flush
RXFLUSH Causes all data that is stored in the receive FIFO to be flushed.
If you write 0 to this field, no flush operation occurs, and if you write 1 to this field, all data in the receive FIFO
or buffer clears out.
This does not affect data in the receive shift register.
0b - No effect
1b - All data flushed out
Reserved
13
—
12-10 Receiver Idle Empty Enable
RXIDEN Enables STAT[RDRF] to become 1 when the receiver is idle for a number of idle characters and the
FIFO is not empty.
000b - Disable STAT[RDRF] to become 1 because of partially filled FIFO when the receiver is idle
001b - Enable STAT[RDRF] to become 1 because of partially filled FIFO when the receiver is idle
for one character
010b - Enable STAT[RDRF] to become 1 because of partially filled FIFO when the receiver is idle
for two characters
011b - Enable STAT[RDRF] to become 1 because of partially filled FIFO when the receiver is idle
for four characters
100b - Enable STAT[RDRF] to become 1 because of partially filled FIFO when the receiver is idle
for eight characters
Table continues on the next page...

---

*Page 3157*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
101b - Enable STAT[RDRF] to become 1 because of partially filled FIFO when the receiver is idle
for 16 characters
110b - Enable STAT[RDRF] to become 1 because of partially filled FIFO when the receiver is idle
for 32 characters
111b - Enable STAT[RDRF] to become 1 because of partially filled FIFO when the receiver is idle
for 64 characters
9 Transmit FIFO Overflow Interrupt Enable
TXOFE Enables FIFO[TXOF] to generate an interrupt to the host.
0b - Disable
1b - Enable
8 Receive FIFO Underflow Interrupt Enable
RXUFE Enables FIFO[RXUF] to generate an interrupt to the host.
0b - Disable
1b - Enable
7 Transmit FIFO Enable
TXFE Enables the transmit FIFO.
If this field is 0, the transmit buffer operates as a FIFO of depth equal to 1 dataword, regardless of the value
in FIFO[TXFIFOSIZE] . Both CTRL[TE] and CTRL[RE] must be 0 before you change the value of this field.
If this field is 1, the built-in FIFO structure for the transmit buffer is enabled. FIFO[TXFIFOSIZE] indicates
the size of the FIFO structure.
0b - Disable
1b - Enable
6-4 Transmit FIFO Buffer Depth
TXFIFOSIZE Indicates the maximum number of transmit datawords (transmit FIFO buffer depth) that can be stored in the
transmit buffer.
000b - 1
001b - 4
010b - 8
011b - 16
100b - 32
101b - 64
110b - 128
111b - 256
3 Receive FIFO Enable
Table continues on the next page...

---

*Page 3158*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
RXFE Enables the receive FIFO.
If this field is 0, the receive buffer operates as a FIFO of depth equal to 1 dataword, regardless of the value
in FIFO[RXFIFOSIZE] . Both CTRL[RE] and CTRL[TE] must be 0 before you change the value of this field.
If this field is 1, the built-in FIFO structure for the receive buffer is enabled. FIFO[RXFIFOSIZE] indicates the
size of the FIFO structure.
0b - Disable
1b - Enable
2-0 Receive FIFO Buffer Depth
RXFIFOSIZE Indicates the maximum number of receive datawords (receive FIFO buffer depth) that can be stored in
the receive buffer before an overrun occurs.
000b - 1
001b - 4
010b - 8
011b - 16
100b - 32
101b - 64
110b - 128
111b - 256

#### 73.6.13 Watermark (WATER)

Offset
Register Offset
WATER 2Ch
Function
Provides the ability to set a programmable threshold for notification, or sets the programmable thresholds to indicate that transmit
data can be written or receive data can be read.
You may read this register at any time but must write to it only when CTRL[TE] is 0.

---

*Page 3159*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 RXCOUNT 0
RXWATER
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 TXCOUNT 0
TXWATER
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-27
—
26-24 Receive Counter
RXCOUNT Indicates the number of datawords in the receive FIFO or buffer.
If a dataword is being received in the receive shift register, it is not included in the count. This value may be
used in conjunction with FIFO[RXFIFOSIZE] to calculate the room left in the receive FIFO or buffer.
Reserved
23-18
—
17-16 Receive Watermark
RXWATER Generates an interrupt or a DMA request if the number of datawords in the receive FIFO or buffer is greater
than the value of this field.
For proper operation, the value of this field must be less than the size of the receive FIFO or buffer, as
indicated by FIFO[RXFIFOSIZE] and FIFO[RXFE] .
Reserved
15-11
—
10-8 Transmit Counter
TXCOUNT Indicates the number of datawords in the transmit FIFO or buffer.
If a dataword is being transmitted to the transmit shift register, it is not included in the count. This value may
be used in conjunction with the value of FIFO[TXFIFOSIZE] to calculate the room left in the transmit FIFO
or buffer.
Reserved
7-2
—
1-0 Transmit Watermark
Table continues on the next page...

---

*Page 3160*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Table continued from the previous page...
Field Function
TXWATER Generates an interrupt or a DMA request when the number of datawords in the transmit FIFO or buffer is
equal to or less than the value of this field.
For proper operation, the value of this field must be less than the size of the transmit buffer or FIFO, as
indicated by FIFO[TXFIFOSIZE] and FIFO[TXFE] .

#### 73.6.14 Data Read-Only (DATARO)

Offset
Register Offset
DATARO 30h
Function
Indicates the first entry in the receive FIFO, but does not pull data from the FIFO.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DATA
W
Reset 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-16
—
15-0 Receive Data
DATA Indicates the first entry from the receive FIFO.
This register has the same functionality as that of Data (DATA) .

#### 73.7 Glossary

Baud rate Number of bits per second that LPUART transmits or receives

---

*Page 3161*

Low Power Universal Asynchronous Receiver/Transmitter (LPUART)
Break character Break character is generated when the transmitter is holding the data line at the space level for at least one
character time
Oversampling Number of times the receive circuitry samples the receive input per baud period (that is, per data bit)

---

*Page 3162*

