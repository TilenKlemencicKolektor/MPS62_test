<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 67 -->

# Chapter 67

# Low Power Serial Peripheral Interface (LPSPI)

#### 67.1 Chip-specific LPSPI information

#### 67.1.1 LPSPI instances and configuration

Table 361. LPSPI instances
Instance MCXE31B MCXE315/MCXE316/MCXE317
LPSPI0 Yes Yes
LPSPI1 Yes Yes
LPSPI2 Yes Yes
LPSPI3 Yes Yes
LPSPI4 Yes No
LPSPI5 Yes No
Table 362. LPSPI instances configuration
Instances TX FIFO Size RX FIFO Size Chip Selects
LPSPI0 4x32 bit 4x32 bit 8
LPSPI1 4x32 bit 4x32 bit 6
LPSPI2 4x32 bit 4x32 bit 4
LPSPI3 4x32 bit 4x32 bit 4
LPSPI4 4x32 bit 4x32 bit 4
LPSPI5 4x32 bit 4x32 bit 4
• MCXE31B supports data rate up to 20 Mbps, MCXE315/MCXE316/MCXE317 supports data rate up to 15 Mbps. Only one
LPSPI instance support 20 MHz in loopback mode that is LPSPI0
• All LPSPIs supports a minimum a 10 MHz data rate on standard plus pads and 7.5 MHz on standard pads.
• Low leakage and Wait modes are not supported in this device.
The number of chip selects that each LPSPI instance supports varies. Because of that, in the Configuration Register 1 (CFGR1),
the Peripheral Chip Select (PCS) field and the Peripheral Chip Select Polarity (PCSPOL) field also vary. See the next 2 tables.
Table 363. LPSPI instances mapped against Peripheral Chip Select (PCS) field support
PCS supported in PCS not supported in
LPSPI0_TCR[26-24] —
LPSPI1_TCR[26-24] —
LPSPI2_TCR[25–24] LPSPI2_TCR[26]
LPSPI3_TCR[25–24] LPSPI3_TCR[26]
LPSPI4_TCR[25–24] LPSPI4_TCR[26]
Table continues on the next page...

---

*Page 2102*

Low Power Serial Peripheral Interface (LPSPI)
Table 363. LPSPI instances mapped against Peripheral Chip Select (PCS) field support (continued)
PCS supported in PCS not supported in
LPSPI5_TCR[25–24] LPSPI5_TCR[26]
Table 364. LPSPI instances mapped against Peripheral Chip Select Polarity (PCSPOL) field support
PCSPOL supported in PCSPOL not supported in
LPSPI0_CFGR1[15-8] —
LPSPI1_CFGR1[13–8] LPSPI1_CFGR1[15–14]
LPSPI2_CFGR1[11–8] LPSPI2_CFGR1[15–12]
LPSPI3_CFGR1[11–8] LPSPI3_CFGR1[15–12]
LPSPI4_CFGR1[11–8] LPSPI4_CFGR1[15–12]
LPSPI5_CFGR1[11–8] LPSPI5_CFGR1[15–12]

#### 67.1.2 LPSPI HREQ considerations for MCXE31B

It is recommended that the HREQ pin (when PCS[1] is used as HREQ) should get de-asserted before the completion of frame
transfer. In case if the HREQ state is still asserted and LPSPI returns to idle state after frame transfer completion, the HREQ state
is internally latched and the next data is written to the transmit FIFO without waiting for the next HREQ assertion.
This limitation is present only while using PCS[1] as HREQ and HREQ remains asserted throughout frame transfer. In case of
using trigger input as HREQ, there is no issue.

#### 67.2 Overview

LPSPI provides an efficient interface (either as a controller or peripheral) to an SPI bus, which is a synchronous serial
communication interface used in embedded systems. It is typically used to perform short distance communications between
microcontrollers and peripheral devices, on printed circuit boards. Typical applications include interfacing with secure digital cards
and LCD displays.
NOTE
The terminology in this chapter has been updated to align with NXP's inclusive language standards, as shown in
the table below.
Table 365. Updated terms
Updated term Deprecated term
Controller Master
Peripheral Slave

---

*Page 2103*

Low Power Serial Peripheral Interface (LPSPI)

#### 67.2.1 Block diagram

Internal chip External
peripheral bus LPSPI SPI Interface
Trigger
Configuration
registers
HREQ
SCK
Command/TX Control
FIFO Iogic
PCS[ n :0]
SCK and PCS[ n :0]
are inputs in Peripheral mode,
and are outputs in Controller mode
RX
FIFO Shift
register
SIN
SOUT
Bus clock
By default, SIN is input and SOUT is output,
Functional clock but SIN/SOUT can be configured differently.
Either SIN or SOUT can be used for single wire.
External clock
Figure 359. Block diagram

#### 67.2.2 Features

• Minimal CPU overhead , with DMA transmit and receive requests supporting FIFO register accesses
• Support available for 32-bit word size
• Configurable clock polarity and phase
• Support available for 8 peripheral chip selects in Controller mode
• Support available for Peripheral mode
• 4-word transmit and command FIFO
• 4-word receive FIFO
• Flexible timing parameters in Controller mode, including SCK frequency and duty cycle, and delays between PCS and
SCK edges
• Continuous transfer option to keep PCS asserted across multiple frames
• Full-duplex transfers that support 1-bit transmit and receive on each clock edge
• Half-duplex transfers that support:
— 1-bit transmit or receive on each clock edge
— 2-bit transmit or receive on each clock edge
— 4-bit transmit or receive on each clock edge
— 8-bit transmit or receive on each clock edge

---

*Page 2104*

Low Power Serial Peripheral Interface (LPSPI)
• Option to use host request to control the start of an SPI bus transfer

#### 67.3 Functional description

#### 67.3.1 Controller mode

67.3.1.1 Transmit and command FIFO commands
The transmit and command FIFO is a combined FIFO that includes both transmit data words and command words. You store:
• Transmit data words in the transmit and command FIFO, by writing to Transmit Data (TDR) .
• Command words in the transmit and command FIFO, by writing to Transmit Command (TCR) .
When a command word is at the top of the transmit and command FIFO, the actions that can occur depend on whether LPSPI is
busy or between frames (see TCR[CONT] and TCR[CONTC] ). See Table 366 for conditions and possible corresponding actions
when a command word is at the top of the transmit and command FIFO.
Table 366. Possible actions when a command word is at the top of the transmit and command FIFO
Condition Action
LPSPI is enabled and idle. The command word is pulled from the FIFO, and this command word controls all
subsequent transfers.
LPSPI is busy and TCR[CONTC] is 0. The SPI frame completes at the end of the existing word, ignoring
TCR[FRAMESZ] . The command word is then pulled from the FIFO and that
command word controls all subsequent transfers (or until the next update to
the command word). Note that a command word with TCR[CONTC] = 0 always
terminates the existing transfer regardless of the previous TCR[CONT] value.
LPSPI is busy; the existing TCR[CONT] The command word must be updated at the frame boundary. The command word
value is 1 and the new TCR[CONTC] is pulled from the FIFO during the last SCK pulse of the existing frame (based on
value is 1. the value of FRAMESZ), and the frame continues using the new command value
for the rest of the frame (or until the next update to the command word). When
TCR[CONTC] = 1, only the lower 24 bits of the command word are updated. If the
command word is updated at a word boundary, then the transfer halts (stops) after
that word. TCR[CONTC] is ignored when not at a frame boundary, so the frame
ends prematurely.
TCR[CONT] = 1 keeps PCS asserted at end of frame, allowing the transfer to continue.
TCR[CONTC] = 1 specifies that this command word must not terminate the existing frame, and the transfer can continue using
the new command word.
TCR[CONTC] = 1 is restricted in the sense that the new command must load on a frame boundary, and the only way for a transfer
to continue from a frame boundary is when the previous command has TCR[CONT] = 1.
You can read the current state of the existing command word from Transmit Command (TCR) . It requires at least three LPSPI
functional clock cycles for Transmit Command (TCR) to update after you write to it (assuming an empty FIFO), and LPSPI must
be enabled ( CR[MEN] = 1).
Writing to Transmit Command (TCR) does not initiate an SPI bus transfer, unless TCR[TXMSK] = 1. When TCR[TXMSK] = 1, a
new command word is not loaded until the end of the existing frame (based on the value of TCR[FRAMESZ] ); at the end of the
transfer, TCR[TXMSK] transitions to 0.
In Controller mode, the LPSPI command word in Transmit Command (TCR) controls SPI attributes based on the selections in
register fields. See Table 367 for TCR fields and associated functionality related to data transfer.

---

*Page 2105*

Low Power Serial Peripheral Interface (LPSPI)
Table 367. Command word in Controller mode
Transmit Command (TCR) Description Can this field
be modified
Field Name
during a data
transfer?
CPOL Clock polarity Specifies the polarity of the SCK pin. Any change of CPOL value causes N
a transition on the SCK pin.
CPHA Clock phase Specifies the clock phase of the transfer. N
PRESCALE Prescaler value Specifies a prescaler used to divide the LPSPI functional clock, to N
generate the timing parameters of the SPI bus transfer. Changing
PRESCALE in conjunction with PCS enables LPSPI to connect to
different peripheral devices at different frequencies.
PCS Peripheral chip N
Specifies which PCS pin asserts for the transfer; the polarity of PCS is
select
static and specified by CFGR1[PCSPOL] .
If CFGR1[PCSCFG] = 1, do not select PCS[3:2].
LSBF LSB first Specifies whether LSB (bit 0) or MSB (bit 31 for a 32-bit word) is Y
transmitted or received first.
BYSW Byte swap Enables byte swap on each 32-bit word when transmitting and receiving Y
data. Byte swapping can be useful when interfacing with devices that
organize data as big-endian.
CONT Continuous transfer Configures LPSPI for a continuous transfer that keeps PCS asserted Y
between frames (as specified by FRAMESZ). You must write a new
command word to cause PCS to negate. Also, this field supports
changing the command word at frame size boundaries.
CONTC Continuing Indicates that this is a new command word for the existing continuous Y
command transfer. When CONTC = 1, the command word must only be written to
the transmit and command FIFO on a frame boundary.
RXMSK Receive data mask Masks the receive data and does not store the masked receive data in Y
the receive FIFO or perform receive data matching. This option is useful
for half-duplex transfers or to specify which fields are compared during
receive data matching.
TXMSK Transmit data mask Masks the transmit data; masked transmit data is not pulled from the Y
transmit FIFO, and the output data pin is 3-stated (unless otherwise
configured by CFGR1[OUTCFG] ). This option is useful for half-duplex
transfers.
WIDTH Transfer width Specifies the number of bits shifted on each SCK pulse: Y
• 1-bit transfers support traditional SPI bus transfers in either half-
duplex or full-duplex data formats.
• 2-bit and 4-bit half-duplex transfers are useful for interfacing with
QuadSPI memory devices, and either TXMSK or RXMSK must also
be 1.
• 8-bit half-duplex transfers are useful for interfacing with OctelSPI
memory devices, and either TXMSK or RXMSK must also be 1.
Table continues on the next page...

---

*Page 2106*

Low Power Serial Peripheral Interface (LPSPI)
Table 367. Command word in Controller mode (continued)
Transmit Command (TCR) Description Can this field
be modified
Field Name
during a data
transfer?
FRAMESZ Frame size Configures the frame size in number of bits equal to (FRAMESZ + 1): Y
• The minimum frame size is 8 bits , or 16 bits for an 8-bit transfer .
• If the frame size is larger than 32 bits, then the frame is divided
into multiple words of 32 bits; each word is loaded from the transmit
FIFO and stored in the receive FIFO separately.
• If the size of the frame is not divisible by 32, then the last load of the
transmit FIFO and store of the receive FIFO contains the remaining
bits. For example, a 72-bit transfer consists of three words: the first
and second words are 32 bits, and the third word is 8 bits.
67.3.1.1.1 SPI bus transfers
LPSPI initiates an SPI bus transfer when all these conditions are true:
• Data is written to the transmit FIFO.
• The HREQ pin is asserted (or the HREQ function is disabled).
• LPSPI is enabled.
To perform the SPI bus transfer, LPSPI uses the attributes configured in Transmit Command (TCR) and the timing parameters
defined in Clock Configuration (CCR) .
The SPI bus transfer ends after the number of bits indicated by the value of FRAMESZ have been transferred (provided
CONT = 0), or at the end of a word when a new transmit command word is at the top of the transmit and command FIFO. When
LPSPI is disabled, the SPI bus transfers end after the transmit FIFO is empty and LPSPI is idle.
The HREQ input is only checked when PCS is negated.
67.3.1.1.2 Circular FIFO
The transmit and command FIFO supports a circular FIFO feature. This feature enables the LPSPI controller to (periodically)
repeat a short data transfer that fits within the transmit and command FIFO, without requiring additional FIFO accesses. When
the circular FIFO is enabled ( CFGR0[CIRFIFO] = 1), the current state of the FIFO read pointer is saved and the status flags are
not updated. After the FIFO is empty and LPSPI is idle, the FIFO read pointer is restored with the saved version, so the contents
of the transmit and command FIFO are not permanently pulled from the FIFO when Circular FIFO mode is enabled.
67.3.1.2 Receive FIFO and data match
The receive FIFO stores received data during SPI bus transfers. When TCR[RXMSK] = 1, the received data is discarded instead
of being stored in the receive FIFO:
• Received data is written to the receive FIFO when the last bit of the word is sampled.
• If the transmit FIFO is empty during a multiple-word or continuous transfer, then the receive data is written to the receive FIFO
before the transfer stalls (assuming CFGR1[NOSTALL] = 0) while waiting for new transmit data or for a command word to
be written.
LPSPI provides a receive data match function that can match received data against one of the two words in DMR0 and DMR1 , or
against a masked data word. You can also configure the received data match function to compare only the first one or two received
data words since the start of the frame:

---

*Page 2107*

Low Power Serial Peripheral Interface (LPSPI)
• Received data that is already discarded because of TCR[RXMSK] cannot cause the data match flag to set, and delays the
receive data match on the first received data word, until all discarded data is received.
• You can configure the receive data match function to discard all received data until a data match is detected,
using CFGR0[RDMO] .
• After a receive data match, to allow all subsequent data to be received, write 0 to CFGR0[RDMO], and then write 0
to SR[DMF] .
67.3.1.3 Timing parameters
The timing parameters that are used for all SPI bus transfers are relative to the LPSPI functional clock divided by the selection
specified in TCR[PRESCALE] . Although you can only change Clock Configuration (CCR) when LPSPI is disabled ( CR[MEN] = 0 ),
to support interfacing with different peripheral devices at different frequencies, you can change the TCR[PRESCALE] selection
between SPI bus transfers by using Transmit Command (TCR) .
NOTE
The minimum value shown in Table 368 is the minimum counter value, but the values of Clock Configuration (CCR)
must also satisfy the data sheet specs based on the LPSPI functional clock frequency and prescaler value.
Table 368. Timing parameters
Description Minimum value Maximum value
Clock Configuration (CCR)
Clock Configuration 1 (CCR1)
Field Name
SCKSET SCK setup Configures the SCK setup phase to (SCKSET + 1) 0 (1 cycle) 255 (256
phase cycles. The setup phase is the SCK high period cycles)
when either CPHA = 0 and CPOL = 1, or
CPHA = 1 and CPOL = 0. Otherwise, it is the
SCK low period. The SCK period is defined as
(SCKSET + SCKHLD + 2) and the duty cycle is the
difference between SCKSET and SCKHLD.
SCKHLD SCK hold phase Configures the SCK hold phase to (SCKHLD + 1) 0 (1 cycle) 255 (256
cycles. The hold phase is the SCK low period when cycles)
either CPHA = 0 and CPOL = 1, or CPHA = 1 and
CPOL = 0. Otherwise, it is the SCK high period. The
SCK period is defined as (SCKSET + SCKHLD + 2)
and the duty cycle is the difference between
SCKSET and SCKHLD.
PCSPCS PCS-to-PCS Configures the minimum delay between PCS 0 (2 cycles) 255 (512
delay negation and the next PCS assertion to cycles)
(PCSPCS + PCSPCS + 2) cycles. When the
command word is updated between transfers, there
is a minimum of (PCSPCS + 1) cycles between the
command word update and any change on PCS
pins.
SCKSCK SCK-to-SCK Configures the delay during a continuous transfer 0 (1 cycle) 255 (256
delay between the last SCK edge of a frame and the first cycles)
SCK edge of the continuing frame to (SCKSCK + 1)
cycles. This is useful when the external peripheral
Table continues on the next page...

---

*Page 2108*

Low Power Serial Peripheral Interface (LPSPI)
Table 368. Timing parameters (continued)
Description Minimum value Maximum value
Clock Configuration (CCR)
Clock Configuration 1 (CCR1)
Field Name
requires a large delay between different words of an
SPI bus transfer.
PCSSCK PCS-to-SCK Configures the minimum delay between PCS 0 (1 cycle) 255 (256
delay assertion and the first SCK edge to (PCSSCK + 1) cycles)
cycles.
SCKPCS SCK-to-PCS Configures the minimum delay between the last 0 (1 cycle) 255 (256
delay SCK edge and the PCS negation to (SCKPCS + 1) cycles)
cycles.
Figure 360 shows the timing settings controlled by:
• TCR[CPHA]
• TCR[CPOL]
• CCR[SCKPCS]
• CCR[PCSSCK]
• CCR1[SCKSET]
• CCR1[SCKHLD]

---

*Page 2109*

Low Power Serial Peripheral Interface (LPSPI)
SCK SCK SCK
CPHA = 0
PCSSCK SET HLD PCS
(CPOL = 0) SCK
(CPOL = 1) SCK
PCS
SDO MSB LSB
SDI MSB LSB
Data frame
Data is sampled internally
on odd clock edges.
SCK SCK SCK
CPHA = 1
PCSSCK SET HLD PCS
(CPOL = 0) SCK
(CPOL = 1) SCK
PCS
SDO MSB LSB
SDI MSB LSB
Data frame
Data is sampled internally
on even clock edges.
Figure 360. Clock phase (TCR[CPHA]) timing diagram example
To configure for a baud rate of 10 MHz with 50/50 duty cycle and with a functional clock frequency of 100 MHz, use the
following settings:
• CCR1[SCKSET] = 0x4 (5 cycles)
• CCR1[SCKHLD] = 0x4 (5 cycles)
• CCR1[PCSPCS] = 0x8 (10 cycles)
• CCR1[SCKSCK] = 0x4 (5 cycles)
• CRR[PCSSCK] = 0x4 (5 cycles)
• CRR[SCKPCS] = 0x4 (5 cycles)
• TCR[PRESCALE] = 0x0 (divide by 1)

---

*Page 2110*

Low Power Serial Peripheral Interface (LPSPI)
67.3.1.4 Pin configuration
Following are the pin configuration settings for half-duplex transfers:
• To swap directions or to support half-duplex transfers on the same pin, you can configure the SIN and SOUT pins using
CFGR1[PINCFG] .
• To specify whether an output data pin (SOUT, for example) 3-states when PCS is negated, or if the output data pin retains
the last value, use CFGR1[OUTCFG] .
• When configuring half-duplex transfers, you must configure the output data pins to 3-state when PCS is negated
( CFGR1[OUTCFG] = 1).
• When performing half-duplex 2-bit transfers, you can write any value to CFGR1[PCSCFG] .
• When performing half-duplex 4-bit transfers, you must write 1h to CFGR1[PCSCFG].
67.3.1.5 Clock loopback
Configure the LPSPI controller to use one of the following clocks to sample the input data:
• The SCK output clock
• A delayed version of the SCK output clock
The delayed version of the SCK is chosen by the SCK pin output delay, plus the SCK pin input delay, and is selected by writing 1 to
CFGR1[SAMPLE] . Enabling the loopback version of the SCK pin can improve the setup time of the input data from the peripheral.
See the chip data sheet for the specific input setup time in Controller Loopback mode.
LPSPI controller SPI peripheral
SIN
MISO
SCK
SCK input
0
delayed SCK
1
CFGR1[SAMPLE]
Figure 361. Clock loopback

#### 67.3.2 Peripheral mode

LPSPI Peripheral mode:
• Uses the same shift register and logic that Controller mode uses.
• Does not use Clock Configuration (CCR) .
• Requires Transmit Command (TCR) to remain static (unchanged) during SPI bus transfers.
67.3.2.1 Transmit and command FIFO commands
You must initialize Transmit Command (TCR) before enabling LPSPI in Peripheral mode, although this register is not updated until
after LPSPI is enabled. After LPSPI is enabled, you must make changes to this register only when LPSPI is idle. In Peripheral
mode, the LPSPI command word in this register controls SPI attributes. Before the PCS input asserts, the transmit FIFO must be
filled with transmit data, or the transmit error flag sets.

---

*Page 2111*

Low Power Serial Peripheral Interface (LPSPI)
Table 369. Command word in Peripheral mode
Transmit Command (TCR) Description
Field Name
CPOL Clock polarity Specifies the polarity of the external SCK input.
CPHA Clock phase Specifies the clock phase of transfer.
PRESCALE Prescaler value Specifies the LPSPI functional clock prescaler.
PCS Peripheral chip
Specifies which PCS is used. The polarity of PCS is static and configured
select
by CFGR1[PCSPOL] .
If CFGR1[PCSCFG] is not equal to zero, then do not select the PCS[3:2] pins.
LSBF LSB first Specifies whether LSB (bit 0) or MSB (bit 31 for a 32-bit word) is transmitted or
received first.
BYSW Byte swap Enables byte swap on each 32-bit word when transmitting and receiving data. Byte
swapping can be useful when interfacing with devices that organize data as big-endian.
CONT Continuous transfer When continuous transfer is selected in Peripheral mode, after the number of bits
indicated by FRAMESZ are transferred, LPSPI passes through and transmits the
received data until the next PCS negation. Whatever is shifted in on the receive data is
shifted out as transmit data considering that there is a 32-bit shift register.
CONTC Continuing When the continuing command is enabled in Peripheral mode, after the number of bits
command indicated by FRAMESZ are transferred, RXMSK is considered equal to 1 and TXMSK
is considered equal to 0 until the next PCS negation. CONTC can be used to change
the direction of a transfer after the number of bits indicated by FRAMESZ.
RXMSK Receive data mask Masks the receive data; LPSPI does not store masked receive data in the receive FIFO
or perform receive data matching. This option is useful for half-duplex transfers or to
specify which fields are compared during receive data matching.
TXMSK Transmit data mask Masks the transmit data so that the masked transmit data is not pulled from
transmit FIFO, and the output data pin is 3-stated (unless otherwise specified in
CFGR1[OUTCFG] ). This option is useful for half-duplex transfers.
WIDTH Transfer width Specifies the number of bits shifted on each SCK pulse:
• 1-bit transfers support traditional SPI bus transfers in either half-duplex or full-
duplex data formats.
• 2-bit and 4-bit half-duplex transfers are useful for interfacing with QuadSPI memory
devices, and at least either TCR[TXMSK] or TCR[RXMSK] must be 1.
• 8-bit half-duplex transfers are useful for interfacing with OctelSPI memory devices,
and at least either TCR[TXMSK] or TCR[RXMSK] must also be 1.
FRAMESZ Frame size Specifies the frame size in number of bits equal to (FRAMESZ + 1):
• The minimum frame size is 8 bits , or 16 bits for an 8-bit parallel transfer .
• If the frame size is larger than 32 bits, then the frame is divided into multiple words
of 32 bits; each word is loaded from the transmit FIFO and stored in the receive
FIFO separately.
• If the size of the frame is not divisible by 32, then the last load of the transmit FIFO
and store of the receive FIFO contain the remainder bits. For example, a 72-bit
transfer consists of three words: the first and second words are 32 bits, and the
third word is 8 bits.

---

*Page 2112*

Low Power Serial Peripheral Interface (LPSPI)
67.3.2.2 Receive FIFO and data match
The receive FIFO stores receive data during SPI bus transfers. When TCR[RXMSK] = 1, the received data is discarded instead
of storing the received data in the receive FIFO.
Receive data supports a receive data match function that can match received data against one of the two words in DMR0 and
DMR1 or against a masked data word. You can also configure the data match function to compare only the first one or two received
data words since the start of the frame.
• Received data that is already discarded because TCR[RXMSK] = 1 cannot cause the data match to set, and delays the match
on the first received data word, until all discarded data is received.
• By using CFGR0[RDMO] , you can also configure the receiver match function to discard all received data until a data match
is detected.
• After a receive data match, to allow all subsequent data to be received, first write 0 to CFGR0[RDMO], then clear SR[DMF] .
67.3.2.3 Partial received word
When the PCS pin deasserts and the receive shift register shifts in a partial word, you can configure the receive shift register to
either discard the partial word or to store it in the receive FIFO. You must specify this using CFGR1[PARTIAL] .
A partial word is defined as less than TCR[FRAMESZ] bits (when TCR[FRAMESZ] is equal or less than 32 bits, or it is the
last word in a multi-word frame) or less than 32 bits (when TCR[FRAMESZ] is greater than 32 bits and not the last word in a
multi-word frame).
A single-bit frame is not supported. A partial received word of 1 bit is supported, but a partial received frame of 1 bit is
not supported.
67.3.2.4 Clocked interface
LPSPI supports interfacing with external controllers that provide only clock and data pins (PCS is not required). This
interface requires:
• Writing 1 to TCR[CPHA] (data is changed on the leading edge of SCK and captured on the following edge).
• Configuring the PCS input to be always asserted ( CFGR1[PCSPOL n ] = 1). For example, to configure PCS[0] to be always
asserted, write 1 to PCSPOL[0], and do not configure PCS[0] in the pin muxing. The chip-level drives PCS to a certain value
(ideally 1); you could use CFGR1[PCSPOL n ] to invert that value.
• Writing 1 to CFGR1[AUTOPCS] to enable automatic PCS generation. When CFGR1[AUTOPCS] = 1, a minimum of four
LPSPI functional clock cycles (divided by the selection specified in TCR[PRESCALE] ) is required between the last SCK edge
of one word and the first SCK edge of the next word.

#### 67.3.3 Debug mode

Table 370. Debug mode
Chip mode LPSPI operation
Can continue operating in Debug mode, if CR[DBGEN] = 1
Debug (the core is in Debug or
Halted mode)

---

*Page 2113*

Low Power Serial Peripheral Interface (LPSPI)

#### 67.3.4 Clocking

Table 371. LPSPI clocks
Type of clock Description
Functional
• Asynchronous to the bus clock.
• If the LPSPI functional clock remains enabled in low-power modes, then LPSPI can perform
SPI bus transfers and low-power wakeups in both Controller and Peripheral modes.
• LPSPI divides the functional clock by a prescaler; the resulting frequency must be at least two
times faster than the SPI external clock frequency (SCK).
External
• The LPSPI shift register is clocked directly by the SCK clock.
• How the SCK clock is generated or supplied depends on the mode (Controller or Peripheral):
— In Controller mode, the SCK clock is generated internally.
— In Peripheral mode, the SCK clock is supplied externally.
Bus The bus clock is only used for bus accesses to the LPSPI control and configuration registers. The
bus clock frequency must be high enough to support the data bandwidth requirements of the LPSPI
registers, including the FIFOs.
See the chip-specific LPSPI information for more.

#### 67.3.5 Reset

Table 372. LPSPI resets
Type of reset Description
Chip Resets the LPSPI logic and registers to their default states.
Software
• Resets the LPSPI logic and registers to their default states, except for the Control register.
• The LPSPI software reset is controlled using CR[RST] .
FIFO
• Resets the transmit and command FIFO and the receive FIFO.
• CR[RTF] and CR[RRF] are write-only.
• After being reset, FIFO is empty.

#### 67.3.6 Interrupts and DMA requests

The following table lists sources (status flags) that can generate LPSPI interrupts and LPSPI transmit and receive DMA requests .
Table 373. Interrupts and DMA requests
Status (SR) Description Can generate
Status flag Name Interrupt? DMA Low-power
request? wake-up?
TDF Transmit data Indicates that data can be written to transmit Y TX Y
flag FIFO, as configured by the transmit FIFO
watermark, FCR[TXWATER] .
Table continues on the next page...

---

*Page 2114*

Low Power Serial Peripheral Interface (LPSPI)
Table 373. Interrupts and DMA requests (continued)
Status (SR) Description Can generate
Status flag Name Interrupt? DMA Low-power
request? wake-up?
RDF Receive data Indicates that data can be read from the Y RX Y
flag receive FIFO, as configured by the receive
FIFO watermark, FCR[RXWATER] .
WCF Word Indicates that the word is complete and the Y N Y
complete flag last bit of the word has been sampled.
FCF Frame Indicates that the frame is complete and PCS Y N Y
complete flag is deasserted.
TCF Transfer Indicates that transfer is complete, PCS is Y N Y
complete flag deasserted, and the transmit and command
FIFO is empty.
TEF Transmit Indicates a transmit and command FIFO Y N Y
error flag underrun. In Controller mode, when
CFGR1[NOSTALL] = 0 (transfers stall when
transmit FIFO is empty), TEF cannot be set.
REF Receive error Indicates a receive FIFO overflow. Y N Y
flag In Controller mode, when
CFGR1[NOSTALL] = 0 (transfers stall when
receive FIFO is full), REF cannot be set.
DMF Data match Indicates that the received data matches the Y N Y
flag configured data match value.
MBF Module busy Indicates that LPSPI is busy performing an N N N
flag SPI bus transfer.
67.3.6.1 DMA support registers
To support efficient DMA transfers to the transmit and control FIFO, an alias register supports 32-bit write access to the Transmit
Command (TCR) and an alias region supports incrementing 32-bit write accesses to the Transmit Data (TDR) .
• Transmit Command Burst (TCBR) is a 32-bit alias register for writing to TCR.
• Transmit Data Burst (TDBR0 - TDBR127) is a 512-byte alias region that supports writing to the TDR.
The burst alias locations are contiguous. A DMA transfer can start by writing to the TCBR register to initialize the transfer and
then increment into the TDBR region without changing the DMA transfer size. The alias registers can also be used by the DMA
to perform burst transfers when accessing the transmit FIFO.
The transmit FIFO prevents writes that overflow the FIFO, but this prevention does not signal an error. Do not perform writes to
the TDBR unless there is sufficient room in the transmit FIFO.
Receive Data Burst (RDBR0 - RDBR127) is a 512-byte alias region that supports reading the Receive Data. This can be used by
the DMA to perform burst transfers when accessing the receive FIFO.

#### 67.3.7 Peripheral triggers

The connection of the LPSPI peripheral triggers with other peripherals depends on the device that is used.

---

*Page 2115*

Low Power Serial Peripheral Interface (LPSPI)
Table 374. Peripheral triggers
Type of trigger Description Additional information
Frame output LPSPI generates two
The frame output trigger:
output triggers that can
• Asserts at the end of each frame (when PCS deasserts).
be connected to other
• Remains asserted for one cycle of the LPSPI functional clock divided by peripherals on the chip.
the configuration defined in TCR[PRESCALE] .
Word output The word output trigger:
• Asserts at the end of each received word.
• Remains asserted for one cycle of the LPSPI functional clock divided by
the configuration defined in TCR[PRESCALE].
Input
To control the start of an LPSPI bus transfer, the LPSPI input trigger can be
selected instead of the HREQ input:
• The LPSPI input trigger is synchronized, and must assert for at least two
cycles of the LPSPI functional clock divided by the configuration defined
in TCR[PRESCALE] so that the input trigger can be detected.
• When LPSPI is busy, the HREQ input (and therefore the LPSPI input
trigger) is ignored.
• When LPSPI is busy, both the HREQ and LPSPI input triggers are
ignored. They are used to start a new transfer when LPSPI is idle.

#### 67.4 External signals

Table 375. External signals
Signal Name Description I/O
SCK Serial clock I/O
• Input in Peripheral mode
• Output in Controller mode
PCS[0] Peripheral chip select I/O
• Input in Peripheral mode
• Output in Controller mode
PCS[1] /HREQ Peripheral chip select Host request pin is selected when I/O
CFGR0[HREN] = 1 and CFGR0[HRSEL] = 0 :
or
• Input in either Peripheral mode or when used as
host request
controller host request
• Output in either Controller mode or when used
as peripheral host request
PCS[2] /DATA[2] Peripheral chip select or data pin I/O
When CFGR1[PCSCFG] = 0:
2 during parallel data transfers
• Input in Peripheral mode
• Output in Controller mode
When CFGR1[PCSCFG] = 1:
Table continues on the next page...

---

*Page 2116*

Low Power Serial Peripheral Interface (LPSPI)
Table 375. External signals (continued)
Signal Name Description I/O
• Input in half-duplex parallel data receive
transfers
• Output in half-duplex parallel data transmit
transfers
PCS[3] /DATA[3] Peripheral chip select or data pin I/O
When CFGR1[PCSCFG] = 0:
3 during parallel data transfers
• Input in Peripheral mode
• Output in Controller mode
When CFGR1[PCSCFG] = 1:
• Input in half-duplex parallel data receive
transfers
• Output in half-duplex parallel data transmit
transfers
PCS[4] /DATA[4] Peripheral chip select or data pin I/O
When CFGR1[PCSCFG] = 0:
4 during parallel data transfers
• Input in Peripheral mode
• Output in Controller mode
When CFGR1[PCSCFG] = 11b:
• Input in half-duplex parallel data receive
transfers
• Output in half-duplex parallel data transmit
transfers
PCS[5] /DATA[5] Peripheral chip select or data pin I/O
When CFGR1[PCSCFG] = 0:
5 during parallel data transfers
• Input in Peripheral mode
• Output in Controller mode
When CFGR1[PCSCFG] = 11b:
• Input in half-duplex parallel data receive
transfers
• Output in half-duplex parallel data transmit
transfers
PCS[6] /DATA[6] Peripheral chip select or data pin I/O
When CFGR1[PCSCFG] = 0:
6 during parallel data transfers
• Input in Peripheral mode
• Output in Controller mode
When CFGR1[PCSCFG] = 11b:
• Input in half-duplex parallel data receive
transfers
• Output in half-duplex parallel data transmit
transfers
Table continues on the next page...

---

*Page 2117*

Low Power Serial Peripheral Interface (LPSPI)
Table 375. External signals (continued)
Signal Name Description I/O
PCS[7] /DATA[7] Peripheral chip select or data pin I/O
When CFGR1[PCSCFG] = 0:
7 during parallel data transfers
• Input in Peripheral mode
• Output in Controller mode
When CFGR1[PCSCFG] = 11b:
• Input in half-duplex parallel data receive
transfers
• Output in half-duplex parallel data transmit
transfers
SOUT /DATA[0] Serial data output Can be configured as serial data input signal (used I/O
as data pin 0 in half-duplex parallel data transfers)
SIN /DATA[1] Serial data input Can be configured as serial data output signal (used I/O
as data pin 1 in half-duplex parallel data transfers)

#### 67.5 Initialization

This module does not require initialization.

#### 67.6 Memory map and registers

NOTE
• Writing to a read-only register or reading a write-only register can cause bus errors.
• LPSPI does not check values programmed in registers for validity, so you must take care to write valid
values only.

#### 67.6.1 LPSPI register descriptions

67.6.1.1 LPSPI memory map
LPSPI_0 base address: 4035_8000h
LPSPI_1 base address: 4035_C000h
LPSPI_2 base address: 4036_0000h
LPSPI_3 base address: 4036_4000h
LPSPI_4 base address: 404B_C000h
LPSPI_5 base address: 404C_0000h
Offset Register Access Reset value
Width
(In bits)
0h Version ID (VERID) 32 R 0200_0004h
4h Parameter (PARAM) 32 R See section
Table continues on the next page...

---

*Page 2118*

Low Power Serial Peripheral Interface (LPSPI)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
10h Control (CR) 32 RW 0000_0000h
14h Status (SR) 32 RW 0000_0001h
18h Interrupt Enable (IER) 32 RW 0000_0000h
1Ch DMA Enable (DER) 32 RW 0000_0000h
20h Configuration 0 (CFGR0) 32 RW 0000_0000h
24h Configuration 1 (CFGR1) 32 RW 0000_0000h
30h Data Match 0 (DMR0) 32 RW 0000_0000h
34h Data Match 1 (DMR1) 32 RW 0000_0000h
40h Clock Configuration (CCR) 32 RW 0000_0000h
44h Clock Configuration 1 (CCR1) 32 RW 0000_0000h
58h FIFO Control (FCR) 32 RW 0000_0000h
5Ch FIFO Status (FSR) 32 R 0000_0000h
60h Transmit Command (TCR) 32 RW 0000_001Fh
64h Transmit Data (TDR) 32 W 0000_0000h
70h Receive Status (RSR) 32 R 0000_0002h
74h Receive Data (RDR) 32 R 0000_0000h
78h Receive Data Read Only (RDROR) 32 R 0000_0000h
3FCh Transmit Command Burst (TCBR) 32 W 0000_0000h
400h - 5FCh Transmit Data Burst (TDBR0 - TDBR127) 32 W 0000_0000h
600h - 7FCh Receive Data Burst (RDBR0 - RDBR127) 32 R 0000_0000h
67.6.1.2 Version ID (VERID)
Offset
Register Offset
VERID 0h
Function
Contains version numbers for the module design and feature set.

---

*Page 2119*

Low Power Serial Peripheral Interface (LPSPI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R MAJOR MINOR
W
Reset 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R FEATURE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0 0
Fields
Field Function
31-24 Major Version Number
MAJOR Indicates the major version number of the module specification.
23-16 Minor Version Number
MINOR Indicates the minor version number of the module specification.
15-0 Module Identification Number
FEATURE Indicates the feature set number
0000_0000_0000_0100b - Standard feature set supporting a 32-bit shift register.
All other values are reserved.
67.6.1.3 Parameter (PARAM)
Offset
Register Offset
PARAM 4h
Function
Contains:
• Number of PCS pins.
• Receive FIFO size.
• Transmit FIFO size.

---

*Page 2120*

Low Power Serial Peripheral Interface (LPSPI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 PCSNUM
W
Reset See Register reset values .
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXFIFO TXFIFO
W
Reset See Register reset values .
Register reset values
Register Reset value
PARAM
LPSPI_0: 0008_0202h
LPSPI_1: 0006_0202h
LPSPI_2–LPSPI_5: 0004_0202h
Fields
Field Function
Reserved
31-24
—
23-16 PCS Number
PCSNUM Indicates the number of PCS pins supported.
15-8 Receive FIFO Size
RXFIFO
RXFIFO Indicates the maximum number of words in the receive FIFO. The maximum number of words is 2 .
7-0 Transmit FIFO Size
TXFIFO Indicates the maximum number of words in the transmit FIFO. The maximum number of words is
TXFIFO
2 .
67.6.1.4 Control (CR)
Offset
Register Offset
CR 10h
Function
Contains fields that control the module operation.

---

*Page 2121*

Low Power Serial Peripheral Interface (LPSPI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
DBGE Reserv
RST MEN
N ed
W RRF RTF
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-10
—
9 Reset Receive FIFO
RRF Deletes all entries in the receive FIFO. This field always reads 0.
0b - No effect
1b - Reset
8 Reset Transmit FIFO
RTF Deletes all entries in the transmit FIFO. This field always reads 0.
0b - No effect
1b - Reset
Reserved
7-4
—
3 Debug Enable
DBGEN Enables LPSPI when the CPU is in Debug mode.
If this field is 0, LPSPI is disabled when the CPU is halted; the PCS pin is deasserted after the transmit FIFO
is empty regardless of the state of Transmit Command (TCR) .
You must update this field only when LPSPI is disabled ( MEN = 0).
0b - Disable
1b - Enable
Reserved
2
—
1 Software Reset
Table continues on the next page...

---

*Page 2122*

Low Power Serial Peripheral Interface (LPSPI)
Table continued from the previous page...
Field Function
Resets all internal logic and registers, except Control (CR) . The reset takes effect immediately and
RST
remains asserted until you write 0 to it. There is no minimum delay required before clearing the software
reset by writing 0.
0b - Not reset
1b - Reset
0 Module Enable
MEN Enables the module. After writing 0, MEN remains set until LPSPI has completed the current transfer and
is idle.
0b - Disable
1b - Enable
67.6.1.5 Status (SR)
Offset
Register Offset
SR 14h
Function
Contains data flow status.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 MBF 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 DMF REF TEF TCF FCF WCF 0 RDF TDF
W W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1
Fields
Field Function
Reserved
31-25
Table continues on the next page...

---

*Page 2123*

Low Power Serial Peripheral Interface (LPSPI)
Table continued from the previous page...
Field Function
—
24 Module Busy Flag
MBF Indicates, in Controller mode, whether there is data to transmit and LPSPI is able to transmit (for example,
the HREQ pin is asserted). The HREQ pin deasserts after the PCS pin deasserts and the LPSPI controller
has waited for half the time specified in CCR[DBT] with no new data to transmit.
Peripheral mode sets this flag when LPSPI is enabled and PCS is asserted.
0b - LPSPI is idle
1b - LPSPI is busy
Reserved
23-14
—
13 Data Match Flag
DMF Indicates whether the received data matches DMR0[MATCH0] and/or DMR1[MATCH1] (as configured
by CFGR1[MATCFG] ).
NOTE
This field behaves differently for register reads and writes.
When reading
0b - No match
1b - Match
When writing
0b - No effect
1b - Clear the flag
12 Receive Error Flag
REF Indicates a receive FIFO overflow error. When this flag is set:
1. End the transfer.
2. Empty the receive FIFO.
3. Clear this flag.
4. Restart the transfer from the beginning.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - No overflow
1b - Overflow
When writing
Table continues on the next page...

---

*Page 2124*

Low Power Serial Peripheral Interface (LPSPI)
Table continued from the previous page...
Field Function
0b - No effect
1b - Clear the flag
11 Transmit Error Flag
TEF Indicates a transmit FIFO underrun error. When this flag is set:
1. End the transfer.
2. Clear this flag.
3. Restart the transfer from the beginning.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - No underrun
1b - Underrun
When writing
0b - No effect
1b - Clear the flag
10 Transfer Complete Flag
TCF Indicates, in Controller mode, whether all transfers are complete and LPSPI has returned to the Idle state
and the transmit FIFO is empty.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not complete
1b - Complete
When writing
0b - No effect
1b - Clear the flag
9 Frame Complete Flag
FCF Indicates whether a frame transfer is complete after PCS deasserts.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not complete
Table continues on the next page...

---

*Page 2125*

Low Power Serial Peripheral Interface (LPSPI)
Table continued from the previous page...
Field Function
1b - Complete
When writing
0b - No effect
1b - Clear the flag
8 Word Complete Flag
WCF Indicates whether the last bit of a received word is sampled.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Not complete
1b - Complete
When writing
0b - No effect
1b - Clear the flag
Reserved
7-2
—
1 Receive Data Flag
RDF Indicates whether the number of words in the receive FIFO is greater than the value in FCR[RXWATER] .
0b - Receive data not ready
1b - Receive data ready
0 Transmit Data Flag
TDF Indicates whether the number of words in the transmit FIFO is equal to or less than the value in
FCR[TXWATER] .
0b - Transmit data not requested
1b - Transmit data requested
67.6.1.6 Interrupt Enable (IER)
Offset
Register Offset
IER 18h
Function
Enables interrupts based on data flow and errors.

---

*Page 2126*

Low Power Serial Peripheral Interface (LPSPI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
DMIE REIE TEIE TCIE FCIE WCIE RDIE TDIE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-14
—
13 Data Match Interrupt Enable
DMIE Enables the data match interrupt.
0b - Disable
1b - Enable
12 Receive Error Interrupt Enable
REIE Enables the receive error interrupt.
0b - Disable
1b - Enable
11 Transmit Error Interrupt Enable
TEIE Enables the transmit error interrupt.
0b - Disable
1b - Enable
10 Transfer Complete Interrupt Enable
TCIE Enables the transfer complete interrupt.
0b - Disable
1b - Enable
9 Frame Complete Interrupt Enable
FCIE Enables the frame complete interrupt.
0b - Disable
1b - Enable
Table continues on the next page...

---

*Page 2127*

Low Power Serial Peripheral Interface (LPSPI)
Table continued from the previous page...
Field Function
8 Word Complete Interrupt Enable
WCIE Enables the word complete interrupt.
0b - Disable
1b - Enable
Reserved
7-2
—
1 Receive Data Interrupt Enable
RDIE Enables the receive data interrupt.
0b - Disable
1b - Enable
0 Transmit Data Interrupt Enable
TDIE Enables the transmit data interrupt.
0b - Disable
1b - Enable
67.6.1.7 DMA Enable (DER)
Offset
Register Offset
DER 1Ch
Function
Enables the DMA data flow.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
RDDE TDDE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2128*

Low Power Serial Peripheral Interface (LPSPI)
Fields
Field Function
Reserved
31-2
—
1 Receive Data DMA Enable
RDDE Enables the receive data DMA.
0b - Disable
1b - Enable
0 Transmit Data DMA Enable
TDDE Enables the transmit data DMA.
0b - Disable
1b - Enable
67.6.1.8 Configuration 0 (CFGR0)
Offset
Register Offset
CFGR0 20h
Function
Includes fields to configure LPSPI.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
CIRFIF HRSE HRPO
RDMO HRDIR HREN
O L L
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2129*

Low Power Serial Peripheral Interface (LPSPI)
Fields
Field Function
Reserved
31-10
—
9 Receive Data Match Only
RDMO Enables receive data match.
When enabled, all received data that does not cause SR[DMF] to assert is discarded:
• Write 1 to this field when LPSPI is idle and SR[DMF] = 0.
• After SR[DMF] = 1, this field is ignored.
• To ensure that no receive data is lost when disabling RDMO, write 0 to this field before clearing
SR[DMF].
See CFGR1[MATCFG] for the received data matching options. When disabled, all received data is stored
in the receive FIFO.
0b - Disable
1b - Enable
8 Circular FIFO Enable
CIRFIFO Enables circular FIFO.
When enabled, the transmit FIFO read pointer is saved to a temporary register. The transmit FIFO is
emptied as in normal operation, but when LPSPI is idle and the transmit FIFO is empty, the read pointer
value is restored from the temporary register.
This restoring of the read pointer causes the contents of the transmit FIFO to be cycled through repeatedly.
NOTE
The read pointer is restored for as long as this field is 1. Writing additional words to the FIFO
when this field is 1 adds them to the end of the FIFO, up to the size of the transmit FIFO.
0b - Disable
1b - Enable
Reserved
7-4
—
3 Host Request Direction
HRDIR Specifies the direction of the HREQ pin. You must configure the HREQ pin only as an output when
LPSPI is in Peripheral mode. The HREQ pin direction must be an input for Controller mode.
0b - Input
1b - Output
2 Host Request Select
HRSEL Specifies the source of the host request input. When the host request function is enabled with the HREQ
pin, the PCS[1] function is disabled.
Table continues on the next page...

---

*Page 2130*

Low Power Serial Peripheral Interface (LPSPI)
Table continued from the previous page...
Field Function
0b - HREQ pin
1b - Input trigger
1 Host Request Polarity
HRPOL Specifies the polarity of the HREQ pin or input trigger .
0b - Active high
1b - Active low
0 Host Request Enable
HREN Enables LPSPI, in Controller mode, to start a new SPI bus transfer only if the host request input is asserted.
When LPSPI is busy, the host request input is ignored.
In Peripheral mode, causes the HREQ output pin to assert when data is available to be transmitted.
0b - Disable
1b - Enable
67.6.1.9 Configuration 1 (CFGR1)
Offset
Register Offset
CFGR1 24h
Function
Includes fields to configure LPSPI. You must write to this register only when LPSPI is disabled.
In addition to pin and output configurations, this register contains match configuration details; the following table shows match
conditions specified in MATCFG .
Table 376. Match conditions for CFGR1[MATCFG]
Condition Description
Match first data word with
Match if first data word equals MATCH0 logically ORed with MATCH1
compare word
first_data_word == (MATCH0 || MATCH1)
Match any data word with
Match if any data word equals MATCH0 logically ORed with MATCH1
compare word
any_data_word == (MATCH0 || MATCH1)
Sequential match, first data word
Match if first data word equals MATCH0, and second data word equals MATCH1
(first_data_word == MATCH0) && (second_data_word == MATCH1)
Sequential match, any data word
Match if any data word equals MATCH0, and the next data word equals MATCH1
Table continues on the next page...

---

*Page 2131*

Low Power Serial Peripheral Interface (LPSPI)
Table 376. Match conditions for CFGR1[MATCFG] (continued)
Condition Description
(any_data_word == MATCH0) && (next_data_word == MATCH1)
Match first data word (masked)
Match if first data word logically ANDed with MATCH1 equals MATCH0 logically ANDed
with compare word (masked)
with MATCH1
(first_data_word && MATCH1) == (MATCH0 && MATCH1)
Match any data word (masked)
Match if any data word logically ANDed with MATCH1 equals MATCH0 logically ANDed
with compare word (masked)
with MATCH1
(any_data_word && MATCH1) == (MATCH0 && MATCH1)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
OUTC
PCSCFG PINCFG MATCFG
FG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
PARTI NOST AUTO SAMP MAST
PCSPOL
AL ALL PCS LE ER
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-29
—
28-27 Peripheral Chip Select Configuration
PCSCFG Specifies PCS pin configuration. When performing parallel transfers, you must configure this field to
enable the desired transfer.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
LPSPI_0 CFGR1 —
LPSPI_1 CFGR1[27] CFGR1[28]
LPSPI_2 CFGR1[27] CFGR1[28]

---

*Page 2132*

Low Power Serial Peripheral Interface (LPSPI)
Field Function
Instance Field supported in Field not supported in
LPSPI_3 CFGR1[27] CFGR1[28]
LPSPI_4 CFGR1[27] CFGR1[28]
LPSPI_5 CFGR1[27] CFGR1[28]
NOTE
The descriptions of the field settings vary by module instance.
Instance Field value and description
LPSPI_0 00b - PCS[7:2] configured for chip select function
01b - PCS[3:2] configured for half-duplex 4-bit transfers (PCS[3:2]
= DATA[3:2])
11b - PCS[7:2] configured for half-duplex 4-bit and 8-bit transfers
(PCS[7:2] = DATA[7:2])
LPSPI_1 0b - PCS[5:2] configured for chip select function
1b - PCS[3:2] configured for half-duplex 4-bit transfers (PCS[3:2]
= DATA[3:2])
LPSPI_2 0b - PCS[3:2] configured for chip select function
LPSPI_3
1b - PCS[3:2] configured for half-duplex 4-bit transfers (PCS[3:2]
LPSPI_4
= DATA[3:2])
LPSPI_5
26 Output Configuration
OUTCFG Specifies whether the output data is 3-stated between accesses (when PCS is deasserted). When
performing half-duplex transfers, this field must be 1.
0b - Retain last value
1b - 3-stated
25-24 Pin Configuration
PINCFG Specifies the pins used for input and output data during serial transfers.
NOTE
When performing parallel transfers, this field must be 00b or 10b.
00b - SIN is used for input data; SOUT is used for output data
01b - SIN is used for both input and output data; only half-duplex serial transfers are supported
Table continues on the next page...

---

*Page 2133*

Low Power Serial Peripheral Interface (LPSPI)
Table continued from the previous page...
Field Function
10b - SOUT is used for both input and output data; only half-duplex serial transfers are supported
11b - SOUT is used for input data; SIN is used for output data
Reserved
23-19
—
18-16 Match Configuration
MATCFG Specifies the condition that causes SR[DMF] to assert. See the match conditions listed in Table 1 for
more information.
NOTE
When writing to this field, either the old value or new value must be in the disabled state (0).
You cannot transition from a nonzero value to another nonzero value.
000b - Match is disabled
001b - Reserved
010b - Match first data word with compare word
011b - Match any data word with compare word
100b - Sequential match, first data word
101b - Sequential match, any data word
110b - Match first data word (masked) with compare word (masked)
111b - Match any data word (masked) with compare word (masked)
15-8 Peripheral Chip Select Polarity
PCSPOL Specifies the polarity of each PCS pin. Bit n in this field (the least-significant bit is bit 0) corresponds
to PCS[ n ].
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
LPSPI_0 CFGR1 —
LPSPI_1 CFGR1[13–8] CFGR1[15–14]
LPSPI_2 CFGR1[11–8] CFGR1[15–12]
LPSPI_3 CFGR1[11–8] CFGR1[15–12]
LPSPI_4 CFGR1[11–8] CFGR1[15–12]
Table continues on the next page...

---

*Page 2134*

Low Power Serial Peripheral Interface (LPSPI)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
LPSPI_5 CFGR1[11–8] CFGR1[15–12]
NOTE
The descriptions of the field settings vary by module instance.
Instance Field value and description
LPSPI_0 0000_0000b - Active low
0000_0001b - Active high
LPSPI_1 00_0000b - Active low
00_0001b - Active high
LPSPI_2 0000b - Active low
LPSPI_3
0001b - Active high
LPSPI_4
LPSPI_5
Reserved
7-5
—
4 Partial Enable
PARTIAL Specifies whether LPSPI, when in Peripheral mode, stores a partial received word in the receive FIFO,
or discards it, when PCS deasserts. See Partial received word for more information.
0b - Discard
1b - Store
3 No Stall
NOSTALL Disables a normal operating feature that causes LPSPI, when in Controller mode, to stall transfers
when the transmit FIFO is empty or when the receive FIFO is full. This feature prevents transmit FIFO
underruns and receive FIFO overruns. Writing 1 to this field disables this functionality.
0b - Disable
1b - Enable
2 Automatic PCS
AUTOPCS Enables automatic PCS generation. For correct operation in Peripheral mode, LPSPI requires the PCS
signal to deassert between frames. Writing 1 to this field generates an internal PCS signal at the end of each
transfer word when TCR[CPHA] = 1.
Table continues on the next page...

---

*Page 2135*

Low Power Serial Peripheral Interface (LPSPI)
Table continued from the previous page...
Field Function
When this field is 1, SCK must remain idle for at least four LPSPI functional clock cycles, divided by the
prescaler (see TCR[PRESCALE] ) selected between each word to ensure correct operation.
This field is ignored in Controller mode.
0b - Disable
1b - Enable
1 Sample Point
SAMPLE Specifies the SCK clock edge on which LPSPI, when in Controller mode, samples input data. Writing 1 to this
field causes LPSPI to sample input data on a delayed loopback SCK clock edge, which improves the setup
time when sampling data (see Clock loopback ). In this configuration, the input data setup time in Controller
mode is equal to the input data setup time in Peripheral mode.
In Peripheral mode, this field is ignored.
NOTE
When SAMPLE = 1, both the input and output buffers must be enabled for the SCK pin.
Buffers are configured at the chip level.
0b - SCK edge
1b - Delayed SCK edge
0 Controller Mode
MASTER Specifies the LPSPI operating mode, Controller or Peripheral. This field directly controls the direction of
the SCK and PCS pins.
0b - Peripheral mode
1b - Controller mode
67.6.1.10 Data Match 0 (DMR0)
Offset
Register Offset
DMR0 30h
Function
Specifies the match data to be used when data matching is enabled. See CFGR1[MATCFG] for the received data matching
options.
NOTE
Do not change the value in this register when CFGR1[MATCFG] > 0.

---

*Page 2136*

Low Power Serial Peripheral Interface (LPSPI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
MATCH0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
MATCH0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Match 0 Value
MATCH0 Specifies the MATCH0 value to be compared against received data.
67.6.1.11 Data Match 1 (DMR1)
Offset
Register Offset
DMR1 34h
Function
Specifies the match data to be used when data matching is enabled. See CFGR1[MATCFG] for the received data matching
options.
NOTE
Do not change the value in this register while CFGR1[MATCFG] > 0.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
MATCH1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
MATCH1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2137*

Low Power Serial Peripheral Interface (LPSPI)
Fields
Field Function
31-0 Match 1 Value
MATCH1 Specifies the MATCH1 value to be compared against received data.
67.6.1.12 Clock Configuration (CCR)
Offset
Register Offset
CCR 40h
Function
Contains clock configuration fields that are used only in Controller mode; you can only change them when LPSPI is disabled
( CR[MEN] = 0).
Warning
Writing a 32-bit value to this register overwrites Clock Configuration 1 (CCR1) ; DBT and SCKDIV always read 0.
To avoid overwriting CCR1, do one of the following:
• Write to all four fields in Clock Configuration (CCR) simultaneously and only once in a 32-bit data.
• Modify the values of CCR[SCKPCS] and/or CCR[PCSSCK] ; write only these two upper bytes in a 16-bit data
or one of them in an 8-bit data.
• Modify CCR1[PCSPCS] and CCR1[SCKSCK] only or CCR1[SCKSET] and CCR1[SCKHLD] only, write
respectively to CCR[DBT] or CCR[SCKDIV] in 8-bit data.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SCKPCS PCSSCK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
W DBT SCKDIV
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 SCK-to-PCS Delay
Table continues on the next page...

---

*Page 2138*

Low Power Serial Peripheral Interface (LPSPI)
Table continued from the previous page...
Field Function
Configures SCK-to-PCS delay. In Controller mode, this field helps you configure the delay from the last
SCKPCS
SCK edge to PCS negation:
• The delay is equal to (SCKPCS + 1) cycles of the LPSPI functional clock divided by the selected
prescaler (see TCR[PRESCALE] ).
• The minimum delay is one cycle.
See Figure 360 for more information.
23-16 PCS-to-SCK Delay
PCSSCK Configures PCS-to-SCK delay. In Controller mode, this field helps you configure the delay from PCS
assertion to the first SCK edge:
• The delay is equal to (PCSSCK + 1) cycles of the LPSPI functional clock divided by the selected
prescaler (see TCR[PRESCALE] ).
• The minimum delay is one cycle.
See Figure 360 for more information.
15-8 Delay Between Transfers
DBT Configures the delay between transfers. Writing to this field updates the contents of CCR1[PCSPCS] and
CCR1[SCKSCK] .
7-0 SCK Divider
SCKDIV Updates the contents of CCR1[SCKSET] and CCR1[SCKHLD] .
Baud rate = function clock ÷ (2^PRESCALE × (SCKSET + SCKHLD + 2))
67.6.1.13 Clock Configuration 1 (CCR1)
Offset
Register Offset
CCR1 44h
Function
Contains clock configuration fields, which are used only in Controller mode. You can change them only when LPSPI is disabled
( CR[MEN] = 0).

---

*Page 2139*

Low Power Serial Peripheral Interface (LPSPI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SCKSCK PCSPCS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SCKHLD SCKSET
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 SCK Inter-Frame Delay
SCKSCK Configures SCK inter-frame delay in Controller mode:
• This field helps you configure the delay from the last SCK pulse of a frame and the first SCK pulse of
the following frame, in a continuous transfer.
• The delay is equal to (SCKSCK + 1) cycles of the LPSPI functional clock divided by the selected
prescaler (see TCR[PRESCALE] ).
• The minimum delay is one cycle.
NOTE
For backward compatibility, writing to CCR[DBT] updates this field with the value written.
23-16 PCS to PCS Delay
PCSPCS Configures PCS to PCS delay in Controller mode:
• This field helps you configure the delay from the PCS negation to the next PCS assertion.
• The delay is equal to (PCSPCS + PCSPCS + 2) cycles of the LPSPI functional clock divided by the
selected prescaler (see TCR[PRESCALE] ).
• The minimum delay is two cycles.
• Half of the delay (PCSPCS + 1) occurs before PCS assertion and the other half of the delay
(PCSPCS + 1) occurs after PCS negation. If the command word is updated between two transfers,
then the command word is updated halfway between the PCS negation of the last transfer and PCS
assertion of the next transfer.
• The command word specifies which PCS signal is used, the polarity and phase of the SCK signal,
and the selected prescaler.
NOTE
For backward compatibility, writing to CCR[DBT] updates this field with (DBT÷2)
rounded up.
15-8 SCK Hold
Table continues on the next page...

---

*Page 2140*

Low Power Serial Peripheral Interface (LPSPI)
Table continued from the previous page...
Field Function
Configures the hold phase of the SCK pin in Controller mode:
SCKHLD
• The hold phase is the delay between the SCK edge that samples the receive data and the SCK
edge that drives the transmit data.
• It is the SCK low period when CPHA = 0 and CPOL = 1, or CPHA = 1 and CPOL = 0. It is the SCK
high period when CPHA = 0, CPOL = 0 and CPHA = 1, CPOL = 1.
• The SCK hold phase delay is equal to (SCKHLD + 1) cycles of the LPSPI functional clock divided by
the selected prescaler (see TCR[PRESCALE] ).
• The minimum delay is one cycle.
• The SCK period is equal to (SCKSET + SCKHLD + 2) cycles of the LPSPI functional clock divided
by the selected prescaler (see TCR[PRESCALE] ).
• The SCK duty cycle is based on the difference between SCKSET and SCKHLD. You must configure
both these fields to the same value for a 50/50 duty cycle.
See Figure 360 for more information.
NOTE
For backward compatibility, writing to CCR[SCKDIV] updates this field with (SCKDIV ÷ 2)
rounded down.
7-0 SCK Setup
SCKSET Configures the setup phase of the SCK pin in Controller mode:
• The setup phase is the delay between the SCK edge that drives the transmit data and the SCK edge
that samples the receive data.
• It is the SCK high period when CPHA = 0 and CPOL = 1, or CPHA = 1 and CPOL = 0. It is the SCK
low period when CPHA = 0 and CPOL = 0, or CPHA = 1 and CPOL = 1.
• The SCK setup phase delay is equal to (SCKSET + 1) cycles of the LPSPI functional clock divided
by the selected prescaler (see TCR[PRESCALE] ).
• The minimum delay is one cycle.
• The SCK period is equal to (SCKSET + SCKHLD + 2) cycles of the LPSPI functional clock divided
by the selected prescaler (see TCR[PRESCALE]).
• The SCK duty cycle is based on the difference between SCKSET and SCKHLD. You must configure
both these fields to the same value for a 50/50 duty cycle.
See Figure 360 for more information.
NOTE
For backward compatibility, writing to CCR[SCKDIV] updates this field with (SCKDIV ÷ 2)
rounded up.

---

*Page 2141*

Low Power Serial Peripheral Interface (LPSPI)
67.6.1.14 FIFO Control (FCR)
Offset
Register Offset
FCR 58h
Function
Contains the receive FIFO and transmit FIFO watermark values.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
RXWATER
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
TXWATER
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-24
—
Reserved
23-18
—
17-16 Receive FIFO Watermark
RXWATER Causes LPSPI to set SR[RDF] when the number of words in the receive FIFO is greater than
RXWATER. Writing a value equal to or greater than the FIFO size truncates the written value.
Reserved
15-8
—
Reserved
7-2
—
1-0 Transmit FIFO Watermark
TXWATER Causes LPSPI to set SR[TDF] when the number of words in the transmit FIFO is equal to or less than
TXWATER. Writing a value equal to or greater than the FIFO size truncates the written value.

---

*Page 2142*

Low Power Serial Peripheral Interface (LPSPI)
67.6.1.15 FIFO Status (FSR)
Offset
Register Offset
FSR 5Ch
Function
Contains fields that indicate the number of words currently stored in the receive and transmit FIFOs.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 RXCOUNT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 TXCOUNT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-24
—
Reserved
23-19
—
18-16 Receive FIFO Count
RXCOUNT Indicates the number of words currently stored in the receive FIFO.
Reserved
15-8
—
Reserved
7-3
—
2-0 Transmit FIFO Count
TXCOUNT Indicates the number of words currently stored in the transmit FIFO.

---

*Page 2143*

Low Power Serial Peripheral Interface (LPSPI)
67.6.1.16 Transmit Command (TCR)
Offset
Register Offset
TCR 60h
Function
Pushes the data into the transmit FIFO, in the same order as written.
When you write to either this register or to Transmit Data (TDR) , each write pushes data into the transmit FIFO. You must write to
this register only using 32-bit writes, which are tagged and cause the command register to update; after that the entry reaches the
top of the FIFO and LPSPI is enabled. This allows changes to the command word and the transmit data itself to be interleaved.
That is, writes to the two registers can be interleaved (write command word, then data word, then command word, and so on).
Changing the command word causes all subsequent SPI bus transfers to be performed using the new command word:
• In Controller mode, writing a new command word does not initiate a new transfer, unless TXMSK is 1. Transfers are initiated
by transmit data in the transmit FIFO, or by a new command word (with TXMSK = 1). Hardware writes 0 to TXMSK when
PCS deasserts.
• In Controller mode, if the command word is changed before an existing frame has completed, then the existing frame
terminates and the command word updates. The command word can be changed during a continuous transfer, if CONTC of
the new command word is 1 and the command word is written on a frame size boundary.
• In Peripheral mode, the command word must be changed only when LPSPI is idle and there is no SPI bus transfer.
Avoid resetting the transmit FIFO after writing to this register; wait for the command register to update from the FIFO first.
Avoid register reading problems: Reading this register returns the current state of the register. Reading this register at the same
time that it is loaded from the transmit FIFO can return an incorrect register value. It is recommended to:
• Read this register when the transmit FIFO is empty.
• Read this register more than once and then compare the returned values.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
CONT RXMS TXMS
CPOL CPHA PRESCALE PCS LSBF BYSW CONT WIDTH
C K K
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R Reserved
FRAMESZ
W 0
Reset 0 0 0 0 0 0 0 0 0 0 0 1 1 1 1 1

---

*Page 2144*

Low Power Serial Peripheral Interface (LPSPI)
Fields
Field Function
31 Clock Polarity
CPOL Specifies the value of SCK when it is idle. You can update this field only when PCS is deasserted.
See Figure 360 for more information.
0b - Inactive low
1b - Inactive high
30 Clock Phase
CPHA Indicates whether data is captured or changed on the leading edge of SCK and captured or changed on the
following edge of SCK. You can update this field only when PCS is deasserted.
See Figure 360 for more information.
0b - Captured
1b - Changed
29-27 Prescaler Value
PRESCALE Specifies the division of the LPSPI functional clock. For all SPI bus transfers, this value is applied to
Clock Configuration (CCR) . You can update this field only when PCS is deasserted.
000b - Divide by 1
001b - Divide by 2
010b - Divide by 4
011b - Divide by 8
100b - Divide by 16
101b - Divide by 32
110b - Divide by 64
111b - Divide by 128

---

*Page 2145*

Low Power Serial Peripheral Interface (LPSPI)
Table continued from the previous page...
Field Function
26-24 Peripheral Chip Select
PCS Configures the peripheral chip select used for the transfer. This field is updated only when PCS is
deasserted.
NOTE
This entire field is not fully supported in every LPSPI module instance. See the chip-specific
LPSPI information.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.
Instance Field supported in Field not supported in
LPSPI_0 TCR —
LPSPI_1 TCR —
LPSPI_2 TCR[25–24] TCR[26]
LPSPI_3 TCR[25–24] TCR[26]
LPSPI_4 TCR[25–24] TCR[26]
LPSPI_5 TCR[25–24] TCR[26]
NOTE
The descriptions of the field settings vary by module instance.
Instance Field value and description
LPSPI_0 000b - Transfer using PCS[0]
001b - Transfer using PCS[1]
010b - Transfer using PCS[2]
011b - Transfer using PCS[3]
100b - Transfer using PCS[4]
101b - Transfer using PCS[5]
110b - Transfer using PCS[6]
111b - Transfer using PCS[7]
LPSPI_1 000b - Transfer using PCS[0]

---

*Page 2146*

Low Power Serial Peripheral Interface (LPSPI)
Table continued from the previous page...
Field Function
Instance Field value and description
001b - Transfer using PCS[1]
010b - Transfer using PCS[2]
011b - Transfer using PCS[3]
100b - Transfer using PCS[4]
101b - Transfer using PCS[5]
LPSPI_2 00b - Transfer using PCS[0]
LPSPI_3
01b - Transfer using PCS[1]
LPSPI_4
LPSPI_5 10b - Transfer using PCS[2]
11b - Transfer using PCS[3]
23 LSB First
LSBF Indicates whether data is transferred with MSB first or LSB first.
0b - MSB first
1b - LSB first
22 Byte Swap
BYSW Swaps the contents of [31:24] with [7:0] and [23:16] with [15:8] for each transmit data word read from the
FIFO and for each received data word stored to the FIFO (or compared with match registers).
0b - Disable byte swap
1b - Enable byte swap
21 Continuous Transfer
CONT Enables continuous transfer:
• In Controller mode, this field keeps PCS asserted at the end of the frame size until a command word
is received that starts a new frame.
• In Peripheral mode, when this field is enabled, LPSPI only transmits the first FRAMESZ bits, after
which LPSPI transmits received data (assuming a 32-bit shift register) until the next PCS negation.
0b - Disable
1b - Enable
20 Continuing Command
CONTC Enables the command word to be changed within a continuous transfer in Controller mode:
• The initial command word must enable continuous transfer (CONT = 1).
• The continuing command must have CONTC = 1.
Table continues on the next page...

---

*Page 2147*

Low Power Serial Peripheral Interface (LPSPI)
Table continued from the previous page...
Field Function
• The continuing command word must be loaded on a frame size boundary.
For example, if the continuous transfer has a frame size of 64 bits, then a continuing command word must
be loaded on a 64-bit boundary.
In Peripheral mode, this field modifies the internal RXMSK and TXMSK configuration after the first
FRAMESZ bits and until PCS negation:
• Receive data is discarded after the first FRAMESZ bits. If CONT is also 1, this does not block the
transmission of received data.
• Transmit data is not masked after the first FRAMESZ bits. This allows the first FRAMESZ bits to be
received and a response transmitted.
0b - Command word for start of new transfer
1b - Command word for continuing transfer
19 Receive Data Mask
RXMSK Masks receive data (receive data is not stored in the receive FIFO).
0b - Normal transfer
1b - Mask receive data
18 Transmit Data Mask
TXMSK Masks transmit data (no data is loaded from the transmit FIFO and the output pin is 3-stated). In
Controller mode, TXMSK initiates a new transfer that cannot be aborted by another command word.
TXMSK automatically transitions to 0 at the end of the transfer.
0b - Normal transfer
1b - Mask transmit data
17-16 Transfer Width
WIDTH Configures serial (1-bit) or parallel transfers. For half-duplex parallel transfers, either RXMSK or TXMSK
must be 1.
NOTE
The descriptions of the field settings vary by module instance.
Instance Field value and description
LPSPI_0 00b - 1-bit transfer
01b - 2-bit transfer
10b - 4-bit transfer
11b - 8-bit transfer
LPSPI_1 00b - 1-bit transfer
LPSPI_2
01b - 2-bit transfer
Table continues on the next page...

---

*Page 2148*

Low Power Serial Peripheral Interface (LPSPI)
Table continued from the previous page...
Field Function
Instance Field value and description
LPSPI_3 10b - 4-bit transfer
LPSPI_4
11b - Reserved
LPSPI_5
Reserved
15-12
—
11-0 Frame Size
FRAMESZ Configures the frame size in number of bits equal to (FRAMESZ + 1):
• The minimum frame size is 8 bits , or 16 bits for an 8-bit transfer .
• If the frame size is larger than 32 bits, then the frame is divided into multiple words of 32 bits; each
word is loaded from the transmit FIFO and stored in the receive FIFO separately.
• If the size of the frame is not divisible by 32, then the last load of the transmit FIFO and store of the
receive FIFO contains the remainder bits. For example, a 72-bit transfer consists of three words: the
first and second words are 32 bits, and the third word is 8 bits.
67.6.1.17 Transmit Data (TDR)
Offset
Register Offset
TDR 64h
Function
Pushes the data into the transmit FIFO, in the same order that the data is written. You can write to this register using 32-, 16-, or
8-bit writes.
When you write to this register or to Transmit Command (TCR) , each write pushes data into the FIFO with zero pushed in
unwritten bytes.

---

*Page 2149*

Low Power Serial Peripheral Interface (LPSPI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
W DATA
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
W DATA
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit Data
DATA Indicates transmit data. Both 8-bit and 16-bit writes of transmit data zero-extend the data written and
push the data into the transmit FIFO. To zero-extend 8-bit and 16-bit writes (to 32 bits) means that the
higher order (most significant) empty parts of the 8-bit and 16-bit writes are filled with zeroes.
67.6.1.18 Receive Status (RSR)
Offset
Register Offset
RSR 70h
Function
Contains data flow status fields for receive FIFO.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
RXEM
R 0 SOF
PTY
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0

---

*Page 2150*

Low Power Serial Peripheral Interface (LPSPI)
Fields
Field Function
Reserved
31-2
—
1 RX FIFO Empty
RXEMPTY Indicates whether the receive FIFO is empty.
0b - Not empty
1b - Empty
0 Start of Frame
SOF Indicates whether this is the first data word received after PCS assertion.
0b - Subsequent data word or RX FIFO is empty (RXEMPTY=1).
1b - First data word
67.6.1.19 Receive Data (RDR)
Offset
Register Offset
RDR 74h
Function
Pulls the first entry from the receive FIFO.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2151*

Low Power Serial Peripheral Interface (LPSPI)
Fields
Field Function
Receive Data
31-0
DATA
67.6.1.20 Receive Data Read Only (RDROR)
Offset
Register Offset
RDROR 78h
Function
Returns the first entry in the receive FIFO but does not remove the data from the FIFO.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Receive Data
31-0
DATA
67.6.1.21 Transmit Command Burst (TCBR)
Offset
Register Offset
TCBR 3FCh

---

*Page 2152*

Low Power Serial Peripheral Interface (LPSPI)
Function
Supports burst transfers of command data to the transmit FIFO for use with the DMA controller.
See DMA support registers .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
W DATA
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
W DATA
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Command Data
DATA Writes data to Transmit Command (TCR) .
67.6.1.22 Transmit Data Burst (TDBR0 - TDBR127)
Offset
For n = 0 to 127:
Register Offset
TDBRn 400h + (n × 4h)
Function
Supports burst transfers of data to the transmit FIFO for use with the DMA controller. The size of this register is 512 bytes.
See DMA support registers .

---

*Page 2153*

Low Power Serial Peripheral Interface (LPSPI)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
W DATA
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
W DATA
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Data
DATA Writes data to Transmit Data (TDR) .
67.6.1.23 Receive Data Burst (RDBR0 - RDBR127)
Offset
For n = 0 to 127:
Register Offset
RDBRn 600h + (n × 4h)
Function
Supports burst transfers of data from the receive FIFO. The size of this register is 512 bytes.
See DMA support registers .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2154*

Low Power Serial Peripheral Interface (LPSPI)
Fields
Field Function
31-0 Data
DATA Reads data from Receive Data (RDR) .

#### 67.7 Glossary

PCS Peripheral chip select
SCK Serial clock
SDI Peripheral data in
SDO Peripheral data out
SS Peripheral select

---

*Page 2155*

