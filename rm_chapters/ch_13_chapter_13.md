<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 13 -->

# Chapter 13

# Direct Memory Access Multiplexer (DMAMUX)

#### 13.1 Chip-specific DMAMUX information

#### 13.1.1 DMAMUX instances

This chip has two instances of DMAMUX: DMAMUX_0 and DMAMUX_1. See the DMAMUX map file attached to this document
to find information related to the slot number for each DMA trigger source.
For MCXE31B, DMAMUX_0 channels 0-15 and DMAMUX_1 channels 0-15 are mapped to eDMA Transfer Control Descriptor
(TCD) 0-15 and eDMA Transfer Control Descriptor (TCD) 16-31, respectively. For the remaining chips, DMAMUX_0 channels
0-5 and DMAMUX_1 channels 0-5 are mapped to eDMA Transfer Control Descriptor (TCD) 0-5 and eDMA Transfer Control
Descriptor(TCD) 6-11, respectively. Therefore, it is not recommended to program DMAMUX_0 channels 6-15 and DMAMUX_1
channels 6-15. When programmed, any access terminates with either an error response for channels 8-15 or without an error
response for channels 6-7.

#### 13.1.2 Periodic DMA triggering

PIT generates periodic trigger events to DMAMUX as shown in this table.
Table 40. PIT channel assignment for periodic DMA triggering through DMAMUX
DMAMUX0/1 ports PIT channel
pit_dma_trigger[0] PIT0 channel 0
pit_dma_trigger[1] PIT0 channel 1
pit_dma_trigger[2] PIT0 channel 2
pit_dma_trigger[3] PIT0 channel 3

#### 13.2 Introduction

#### 13.2.1 Overview

DMAMUX routes a DMA source , called slot , to any of the 16 DMA channels. See the chip-specific information for details.

---

*Page 255*

Direct Memory Access Multiplexer (DMAMUX)

#### 13.2.2 Block diagram

DMAMUX
Source 0 DMA channel 0
Source 1 DMA channel 1
Source 2
Source x
Always 0
Always y
Trigger 0
DMA channel n
Trigger z
Figure 23. Block diagram

#### 13.2.3 Features

• Ability to route up to 61 peripheral slots and up to 2 always-on slots to 16 channels
• Supports 16 independently selectable DMA channel routers ( the first 4 channels provide an additional trigger functionality )
• Allows assignment of each channel router to one of the possible peripheral DMA slots or always-on slots

#### 13.2.4 Modes of operation

The following table describes the functional operating modes of DMAMUX.
Table 41. Modes of operation
Mode Description
Disabled
In this mode, the DMA channel is disabled. Because disabling and enabling of DMA channels
is done primarily via the DMA configuration registers, this mode is used mainly as the reset
state for a DMA channel in the DMA channel MUX. You can also use this mode to temporarily
suspend a DMA channel while system reconfiguration takes place , for example, by changing the
period of a DMA trigger .
Normal
In this mode, a DMA source is routed directly to the specified DMA channel. The operation of
DMAMUX in this mode is completely transparent to the system.
Periodic trigger
In this mode, a DMA source can only request a DMA transfer, such as, when a transmit buffer
becomes empty or a receive buffer becomes full, periodically.
You configure the period using an external periodic interrupt timer (PIT). This mode is available
only for channels 0–3. See the chip-specific section for more details.

---

*Page 256*

Direct Memory Access Multiplexer (DMAMUX)

#### 13.3 Functional description

The primary purpose of DMAMUX is to provide flexibility in the system's use of the available DMA channels. Configuration of
DMAMUX is intended to be a static procedure performed during the execution of the system boot code. However, if you follow
the procedure outlined in Enabling and configuring sources , you can change the configuration of DMAMUX during the normal
operation of the system. All DMA channels must be inactive before and during a configuration change.
Functionally, DMAMUX channels can be divided into two classes:
• Channels that implement the normal routing functionality plus the periodic triggering capability
• Channels that implement only the normal routing functionality

#### 13.3.1 DMA channels with periodic triggering capability

Besides the normal routing functionality, the first four channels of DMAMUX provide a special periodic triggering capability that you
can use to provide an automatic mechanism to transmit bytes, frames, or packets at fixed intervals without the need for processor
intervention (see Figure 24 ).
An external periodic interrupt timer (PIT) generates the trigger; for example, by configuring the external periodic timer, you
configure the periodic triggering interval.
NOTE
Because of the dynamic nature of the system (owing to DMA channel priorities, bus arbitration, interrupt service
routine lengths, and so on), the number of clock cycles between a trigger and the actual DMA transfer cannot
be guaranteed.
Source 0
Source 1
Source 2
DMA channel 0
Trigger 0
Source x
Always 0
DMA channel m -1
Trigger m
Always y
Figure 24. DMAMUX triggered channels
The DMA channel triggering capability allows the system to schedule regular DMA transfers, usually on the transmit side of certain
peripherals, without the intervention of the processor. This trigger works by gating the request from the peripheral to DMA until a
trigger event is observed (see Figure 25 ).

---

*Page 257*

Direct Memory Access Multiplexer (DMAMUX)
Peripheral request
Trigger
DMA request
Figure 25. DMAMUX channel triggering: normal operation
After the DMA request is serviced, the peripheral negates its request, effectively resetting the gating mechanism until the
peripheral reasserts its request and the next trigger event is seen. This means that if a trigger is seen, but the peripheral is not
requesting a transfer, then that trigger is ignored (see Figure 26 ).
Peripheral request
Trigger
DMA request
Figure 26. DMAMUX channel triggering: ignored trigger
You can use this triggering capability with any peripheral that supports DMA transfers, and is most useful in the following situations:
• Periodic polling of external devices on a particular bus: as an example, the transmit side of an SPI is assigned to a DMA
channel with a trigger, as described in the aforementioned text. After periodic polling is set up, SPI requests DMA transfers,
presumably from memory, as long as its transmit buffer is empty. By using a trigger on this channel, the SPI transfers can
be automatically performed every 5 μs (as an example). On the receive side of SPI, you can configure SPI and DMA to
transfer the received data into memory, effectively implementing a method to periodically read data from external devices and
transferring the results into memory without processor intervention.
• Using the GPIO ports to drive or sample waveforms: by configuring DMA to transfer data to one or more GPIO ports, it is
possible to create complex waveforms using tabular data stored in on-chip memory. Conversely, using DMA to periodically
transfer data from one or more GPIO ports, it is possible to sample complex waveforms and store the results in a tabular form
in on-chip memory.

#### 13.3.2 DMA channels with no triggering capability

The other channels of DMAMUX provide the normal routing functionality as described in Modes of operation .

#### 13.3.3 Always-enabled DMA sources

In addition to the peripherals that you can use as DMA sources, 2 additional DMA sources are always enabled. Unlike the
peripheral DMA sources, where the peripheral controls the flow of data during DMA transfers, the sources that are always enabled
provide no such "throttling" of data transfers. These sources are most useful in the following cases:
• Performing DMA transfers to and from GPIO: moving data to and from one or more GPIO pins , either unthrottled (that is, as
fast as possible), or periodically (using the DMA triggering capability)
• Performing DMA transfers from memory to memory: moving data from memory to memory, typically as fast as possible,
sometimes with software activation
• Performing DMA transfers from memory to the external bus, or vice-versa: similar to memory-to-memory transfers, this is
typically done as quickly as possible
• Any DMA transfer that software must explicitly start or activate

---

*Page 258*

Direct Memory Access Multiplexer (DMAMUX)
In cases where software must initiate the start of a DMA transfer, an always-enabled DMA source can be used to provide maximum
flexibility. When activating a DMA channel via software, subsequent executions of the minor loop require that a new start event
be sent. This can either be a new software activation or a transfer request from the DMA channel MUX. The options for doing this
are as follows.
• Transfer all data in a single minor loop: by configuring DMA to transfer all of the data in a single minor loop (that is, major
loop counter = 1), no reactivation of the channel is necessary. The disadvantage of this option is reduced granularity in
determining the load that the DMA transfer imposes on the system. For this option, the DMA channel must be disabled in the
DMA channel MUX.
• Use explicit software reactivation: using this option, DMA is configured to transfer the data using both minor and major loops,
but the processor is required to reactivate the channel by writing to the DMA registers after every minor loop. For this option,
the DMA channel must be disabled in the DMA channel MUX.
• Use an always-enabled DMA source: using this option, DMA is configured to transfer the data using both minor and major
loops, and the DMA channel MUX does the channel reactivation. For this option, you must enable the DMA channel and point
it to an "always-enabled" source. Channel reactivation can be continuous (DMA triggering is disabled) or can use the DMA
triggering capability. In this manner, it is possible to execute periodic transfers of data packets from one source to another,
without processor intervention.

#### 13.4 Initialization and application information

This section provides instructions for initializing the DMA channel MUX.

#### 13.4.1 Reset

The reset state of each field is shown in Memory map and register definition . In summary, after reset, all channels are disabled
and you must enable them explicitly before use.

#### 13.4.2 Enabling and configuring sources

13.4.2.1 Enable a source with periodic triggering
Perform the following procedure to enable a source with periodic triggering:
1. Determine the DMA channel with which the source is associated. Only the first 4 DMA channels have periodic triggering
capability.
2. Write 0 to CHCFG n [ENBL] and CHCFG n [TRIG] of the DMA channel.
3. Ensure that the DMA channel is properly configured in DMA. You can enable the DMA channel at this point.
4. Configure the corresponding timer.
5. Select the source to be routed to the DMA channel. Write to the corresponding CHCFG register, ensuring that
CHCFG n [ENBL] and CHCFG n [TRIG] are 1.
The following is an example. See the chip-specific information for the number of DMA channels on this chip that have
triggering capability.
Example: To configure source 5 transmit for use with DMA channel 1, with periodic triggering capability:
1. Write 0h to CHCFG1.
2. Configure channel 1 in DMA, including enabling the channel.
3. Configure a timer for the desired trigger interval.
4. Write C5h to CHCFG1.

---

*Page 259*

Direct Memory Access Multiplexer (DMAMUX)
The following code example illustrates steps 1 and 4 above:
In File registers.h:
#define DMAMUX_BASE_ADDR 0x40021000/* Example only ! */
/* Following example assumes char is 8-bits */
volatile unsigned char *CHCFG0 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0003);
volatile unsigned char *CHCFG1 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0002);
volatile unsigned char *CHCFG2 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0001);
volatile unsigned char *CHCFG3 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0000);
volatile unsigned char *CHCFG4 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0007);
volatile unsigned char *CHCFG5 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0006);
volatile unsigned char *CHCFG6 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0005);
volatile unsigned char *CHCFG7 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0004);
volatile unsigned char *CHCFG8 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x000B);
volatile unsigned char *CHCFG9 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x000A);
volatile unsigned char *CHCFG10= (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0009);
volatile unsigned char *CHCFG11= (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0008);
volatile unsigned char *CHCFG12= (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x000F);
volatile unsigned char *CHCFG13= (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x000E);
volatile unsigned char *CHCFG14= (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x000D);
volatile unsigned char *CHCFG15= (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x000C);
In File main.c:
#include "registers.h"
:
:
*CHCFG1 = 0x00; /* Clear all the fields of CHCFG1 register */
*CHCFG1 = 0xC5; /* ENBL = 1 DMA Channel is enabled */
/* TRIG = 1 Triggering is enabled */
/* SOURCE = 5 DMA Source 5 is selected */
Code Listing 1. Configuring source 5 with DMA channel 1 (with periodic triggering)
13.4.2.2 Enable a source without periodic triggering
Perform the following procedure to enable a source without periodic triggering :
1. Determine the DMA channel with which the source is associated. Only the first 4 DMA channels have periodic triggering
capability.
2. Write 0 to CHCFG n [ENBL] and CHCFG n [TRIG] of the DMA channel.
3. Ensure that the DMA channel is properly configured in DMA. You can enable the DMA channel at this point.
4. Select the source to be routed to the DMA channel. Write to the corresponding CHCFG register, ensuring that
CHCFG n [ENBL] is 1 while CHCFG n [TRIG] is 0.
The following is an example. See the chip configuration details for the number of DMA channels on this chip that have
triggering capability.
Example: To configure source 5 transmit for use with DMA channel 1, with no periodic triggering capability :
1. Write 0h to CHCFG1.
2. Configure channel 1 in DMA, including enabling the channel.
3. Write 85h to CHCFG1.

---

*Page 260*

Direct Memory Access Multiplexer (DMAMUX)
The following code example illustrates steps 1 and 3 above:
In File registers.h:
#define DMAMUX_BASE_ADDR 0x40021000/* Example only ! */
/* Following example assumes char is 8-bits */
volatile unsigned char *CHCFG0 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0003);
volatile unsigned char *CHCFG1 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0002);
volatile unsigned char *CHCFG2 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0001);
volatile unsigned char *CHCFG3 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0000);
volatile unsigned char *CHCFG4 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0007);
volatile unsigned char *CHCFG5 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0006);
volatile unsigned char *CHCFG6 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0005);
volatile unsigned char *CHCFG7 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0004);
volatile unsigned char *CHCFG8 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x000B);
volatile unsigned char *CHCFG9 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x000A);
volatile unsigned char *CHCFG10= (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0009);
volatile unsigned char *CHCFG11= (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0008);
volatile unsigned char *CHCFG12= (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x000F);
volatile unsigned char *CHCFG13= (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x000E);
volatile unsigned char *CHCFG14= (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x000D);
volatile unsigned char *CHCFG15= (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x000C);
In File main.c:
#include "registers.h"
:
:
*CHCFG1 = 0x00; /* Clear all the fields of CHCFG1 register */
*CHCFG1 = 0x85; /* ENBL = 1 DMA Channel is enabled */
/* TRIG = 0 Triggering is disabled */
/* SOURCE = 5 DMA Source 5 is selected */
Code Listing 2. Configuring source 5 with DMA channel 1 (without periodic triggering)
13.4.2.3 Disable a source
You can disable a particular DMA source by not writing the corresponding source value into any of the CHCFG registers.
Additionally, some module-specific configurations may be necessary. See the "Enhanced Direct Memory Access (eDMA)" chapter
for details.
To switch the source of a DMA channel:
1. Disable the DMA channel in the DMA and reconfigure the channel for the new source.
2. Write 0 to CHCFG n [ENBL] and CHCFG n [TRIG] of the DMA channel.
3. Select the source to be routed to the DMA channel. Write to the corresponding CHCFG register, ensuring that
CHCFG n [ENBL] and CHCFG n [TRIG] are 1.
Example: To switch DMA channel 8 from source 5 transmit to source 7 transmit:
1. In the DMA configuration registers, disable DMA channel 8 and reconfigure it to handle the transfers to peripheral slot 7.
This example assumes channel 8 does not have the triggering capability.
2. Write 0h to CHCFG8.
3. Write 87h to CHCFG8. (In this example, writing 1 to CHCFG[TRIG] has no effect because of the assumption that
channel 8 does not support the periodic triggering functionality.)

---

*Page 261*

Direct Memory Access Multiplexer (DMAMUX)
The following code example illustrates steps 2 and 3 above:
In File registers.h:
#define DMAMUX_BASE_ADDR 0x40021000/* Example only ! */
/* Following example assumes char is 8-bits */
volatile unsigned char *CHCFG0 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0003);
volatile unsigned char *CHCFG1 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0002);
volatile unsigned char *CHCFG2 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0001);
volatile unsigned char *CHCFG3 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0000);
volatile unsigned char *CHCFG4 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0007);
volatile unsigned char *CHCFG5 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0006);
volatile unsigned char *CHCFG6 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0005);
volatile unsigned char *CHCFG7 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0004);
volatile unsigned char *CHCFG8 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x000B);
volatile unsigned char *CHCFG9 = (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x000A);
volatile unsigned char *CHCFG10= (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0009);
volatile unsigned char *CHCFG11= (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x0008);
volatile unsigned char *CHCFG12= (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x000F);
volatile unsigned char *CHCFG13= (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x000E);
volatile unsigned char *CHCFG14= (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x000D);
volatile unsigned char *CHCFG15= (volatile unsigned char *) (DMAMUX_BASE_ADDR+0x000C);
In File main.c:
#include "registers.h"
:
:
*CHCFG8 = 0x00; /* Clear all the fields of CHCFG1 register */
*CHCFG8 = 0x87; /* ENBL = 1 DMA Channel is enabled */
/* TRIG = 0 Triggering is disabled */
/* SOURCE = 7 DMA Source 7 is selected */
Code Listing 3. Switching DMA channel 8 from source 5 transmit to source 7 transmit

#### 13.5 Memory map and register definition

This section provides a detailed description of all memory-mapped registers in DMAMUX.

#### 13.5.1 DMAMUX register descriptions

13.5.1.1 DMAMUX memory map
DMAMUX_0 base address: 4028_0000h
DMAMUX_1 base address: 4028_4000h
Offset Register Access Reset value
Width
(In bits)
0h Channel Configuration (CHCFG3) 8 RW 00h
Table continues on the next page...

---

*Page 262*

Direct Memory Access Multiplexer (DMAMUX)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
1h Channel Configuration (CHCFG2) 8 RW 00h
2h Channel Configuration (CHCFG1) 8 RW 00h
3h Channel Configuration (CHCFG0) 8 RW 00h
4h Channel Configuration (CHCFG7) 8 RW 00h
5h Channel Configuration (CHCFG6) 8 RW 00h
6h Channel Configuration (CHCFG5) 8 RW 00h
7h Channel Configuration (CHCFG4) 8 RW 00h
8h Channel Configuration (CHCFG11) 8 RW 00h
9h Channel Configuration (CHCFG10) 8 RW 00h
Ah Channel Configuration (CHCFG9) 8 RW 00h
Bh Channel Configuration (CHCFG8) 8 RW 00h
Ch Channel Configuration (CHCFG15) 8 RW 00h
Dh Channel Configuration (CHCFG14) 8 RW 00h
Eh Channel Configuration (CHCFG13) 8 RW 00h
Fh Channel Configuration (CHCFG12) 8 RW 00h
13.5.1.2 Channel Configuration (CHCFG0 - CHCFG15)
Offset
For n = 0 to 15:
Register Offset
CHCFGn 0h + (n + 3 - 2 × (n mod 4))
Function
Provides you with the configuration to enable or disable, and to associate each of the DMA channels with one of the DMA slots
(peripheral slots or always-on slots) in the system.
NOTE
Writing to multiple CHCFG registers with the same source value results in unpredictable behavior. This is true, even
if a channel is disabled ( CHCFG n [ENBL] == 0).
Before changing the trigger or source settings, you must disable a DMA channel using CHCFG n [ENBL].

---

*Page 263*

Direct Memory Access Multiplexer (DMAMUX)
Diagram
Bits 7 6 5 4 3 2 1 0
R
ENBL TRIG SOURCE
W
Reset 0 0 0 0 0 0 0 0
Fields
Field Function
7 DMA Channel Enable
ENBL Enables the DMA channel.
The condition when the DMA channel is disabled (ENBL = 0) is called Disabled mode. This mode is primarily
used during the configuration of DMAMUX. DMA has separate channel enables or disables, which you must
use to disable or reconfigure a DMA channel.
0b - Disable
1b - Enable
6 DMA Channel Trigger Enable
TRIG Enables the periodic triggering capability for the triggered DMA channel.
If triggering is disabled and ENBL = 1, the DMA channel routes the specified source to the DMA channel
(Normal mode).
If triggering is enabled and ENBL = 1, then DMAMUX is in Periodic Trigger mode.
0b - Disable
1b - Enable
5-0 DMA Channel Source (Slot)
SOURCE Specifies which DMA source, if any, is routed to a particular DMA channel. See the chip-specific
DMAMUX information for details about peripherals and their slot numbers.

#### 13.6 Glossary

Slot DMA source that either reflects a peripheral source or an always-enabled source
Periodic trigger Trigger that an external source generates at periodic intervals for DMA transfer

---

*Page 264*

