<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 45 -->

# Chapter 45

# Wakeup Unit (WKPU)

#### 45.1 Chip-specific WKPU information

#### 45.1.1 WKPU configuration on the chip

WKPU provides the following features:
• A configurable, low-power wake-up capability to the chip from multiple configurable asynchronous wake-up events.
• Support for four internal and 60 external sources that can generate interrupts or wake-up events.
• Support for an NMI input.
Table 218. WKPU configuration on the chip
Number of sources, interrupts, and Value Description
vectors
NMI sources 1 Single NMI pin routed to all application cores
Internal wake-up sources 4
• SWT_0 wake-up, RTC-API wake-up
• RTC timeout
• Analog comparator round robin wake-up (from LPCMP_0, 1, and 2
1
round robin wake-up)
• RTI wake-up
External wake-up sources 60 60 GPIO pins (WKPU[0]-WKPU[59]) support the wake-up functionality.
See the IOMUX file attached to this document for details.
Glitch filters for external interrupts 60 Glitch filter on each external wake-up source
External interrupt vectors 8 —
1. Both wake-up sources 0 (RTC-API) and 2 (CMP_ x trigger mode wake-up) use RTC-API wake-up, with Trigger mode
having a higher priority. This means, if you configure LPCMP_ x .RRCR0[ERR_EN] for either of the comparators, the
RTC_API wake-up is used only for the CMP_ x trigger mode operation. The RTC-API wake-up does not cause wake-up
from Standby mode in this scenario.
All of the aforementioned wake-up sources can be enabled or disabled. Also, you can configure these wake-up sources, by using
WKPU configuration registers, to provide wake-up on rising or falling events. See the WKPU register memory map for details.
NOTE
You must use SIUL2 to perform WKPU pin configurations (PUE, PUS, and IBE). For this, you must first configure
SIUL2.MSCR[IBE] for the corresponding pin. This chip does not support WKPU-provided pin configurations; it
supports only bypass control.

#### 45.1.2 WKPU NMI configuration

WKPU supports one external source that can cause non-maskable interrupts to on-chip cores and wake-up events to the system.
The following figure shows one application cores, Cortex-M7. In case of a wake-up event (internal or external), WKPU initiates
the recovery of the chip and feeds an interrupt to the core depending on the configuration. The sections that follow provide details
related to the associated configurations.

---

*Page 1279*

Wakeup Unit (WKPU)
Cortex-M7_0
Nonmaskable interrupt
Destination
System wake-up
Flag Overrun
Edge detect
Glitch filter
NMI
NFE[0] RDSS RWRE RREE RFEE
NDSS[0] NWRE[0] NREE[0] NFEE[0]
NMI Configuration Register (NCR)
Figure 169. WKPU NMI configuration

#### 45.1.3 WKPU wake-up source connectivity

WKPU allows the external NMI pin to assert the core NMIs on the chip. NMI supports NSR's status and overrun flags. This is what
you could do using NCR:
• Control the NMI destination interrupt by configuring NCR[NDSS].
• Control the rising edge, falling edge, or either of the edge reactions to the NMI pin by using bits 2:0 of NCR[NFEE] and
NCR[NREE]. The enabling of these edge reactions to the NMI pin is independent of each core.
WKPU supports the capturing of a second event per NMI input before the interrupt is cleared, thus reducing the chance of losing
an NMI event.
The following figure shows routing of external wake-up events or interrupts with WKPU and the system interrupt controller.

---

*Page 1280*

Wakeup Unit (WKPU)
Interrupt vectors
Mode and
power control
Interrupt
controller Wake-up enable
WRER_64[31:0]
OR OR OR OR OR OR OR OR
WRER[31:0]
IRER_64[31:0] Interrupt enable IRER[31:0] Interrupt enable
Flag[31:24] Flag[23:16] Flag[15:8] Flag[7:0] WISR_64[31:0] Flag[31:24] Flag[23:16] Flag[15:8] Flag[7:0] WISR[31:0]
Rising Rising
Edge detection WIREER_64[31:0] Edge detection WIREER[31:0]
Glitch filter enable Falling Glitch filter enable Falling
WIFER_64[31:0] Analog glitch filter WIFEER_64[31:0] WIFER[31:0] Analog glitch filter WIFEER[31:0]
Interrupt edge enable Interrupt edge enable
External wake-up pins External wake-up pins
RTI wake-up RTC timeout
CMP_0 round-robin wake-up
CMP_1 round-robin wake-up SWT_0 timeout
CMP_2 round-robin wake-up RTC API wake-up
Figure 170. WKPU wake-up source connectivity
This is the wake-up source mapping to WKPU:
• Wake-up source 0 : SWT_0 timeout, RTC-API API wake-up
• Wake-up source 1 : RTC-API RTC timeout
• Wake-up source 2 : Round robin wake-up interrupt (Trigger mode interrupt) from LPCMP_0, LPCMP_1, or LPCMP_2
• Wake-up source 3 : RTI wake-up
• Wake-up source 4 : Wake-up source 63—external pin wake-up sources, WKPU[0] - WKPU[59]
If you configure any or all of the LPCMP_ x .RRCR0[RR_EN] fields to be active, the corresponding CMP_ x pins must be dedicated
for the CMP_ x operation. In case you are not using any of the CMPs, you can use SIUL2.MSCR x [SSS] to configure the pins for
digital functionalities.
NOTE
You must enable WKPU (by using MC_ME.PRTN x _COFB y _CLKEN) before entering any of the chip's low-
power modes.

#### 45.2 Overview

WKPU supports 64 external sources that can generate interrupts or wake-up events, and 0 external sources that can cause
nonmaskable interrupt requests or wake-up events. Additionally, it combines its wake-up events with those generated by other
wake-up sources to supply a single wake-up to the system.

#### 45.2.1 Block diagram

The following figure shows WKPU and its interfaces with the other system components.
NOTE
The signal widths in the following diagram do not depict a particular configuration of this chip. See the chip-specific
WKPU information for details.

---

*Page 1281*

Wakeup Unit (WKPU)
WKPU
0-4
NMI or wake-up 0-4
Platform
configuration
0-4
NMI enable
Filter bypass
Wake-up
0-4
Filter
Peripheral bus Pads
IOMUX
0-64
IRQ or wake-up Filter
configuration
Filter bypass
Mode and
System wake-up
power control
0-4 Interrupt
IRQs
controller
0-64 Modules such
as STIMER
Figure 171. Block diagram

#### 45.2.2 Features

• Supports nonmaskable interrupts including:
— 2 NMI source s
— 2 analog glitch filter s
— Independent interrupt destination for each core:
◦ Nonmaskable interrupt
◦ Critical interrupt
— Active edge selection control (rise and fall) for events
— Configurable system wake-up triggering from NMI source s
• Supports external wake-up and interrupts that include the following:
— One System interrupt vector for interrupt sources
— 64 analog glitch filters
— Independent interrupt mask
— Edge detection
— Configurable system wake-up triggering from all interrupt sources

#### 45.3 Functional description

#### 45.3.1 Nonmaskable interrupts

WKPU supports the capturing of the second event according to the NMI input before the interrupt becomes 0. This reduces the
chance of losing an NMI event, although it creates an overrun condition.

---

*Page 1282*

Wakeup Unit (WKPU)
Each NMI passes through a bypassable analog glitch filter.
NOTE
When NMI is disabled, glitch filter control and pad configuration perform. This prevents erroneous triggering by
glitches caused by the configuration process itself.
NOTE
The following figure represents a generic configuration and does not represent the configuration of this specific
chip. See the chip-specific information for details on this chip's WKPU.
CPU CPU CPU CPU
Mode and
power control
MMI Critical IRQ Machine check MMI Critical IRQ Machine check MMI Critical IRQ Machine check MMI Critical IRQ Machine check
Destination Destination Destination Destination
Wake-up enable Wake-up enable Wake-up enable Wake-up enable
Flag Overrun Flag Overrun Flag Overrun Flag Overrun
Edge detect Edge detect Edge detect Edge detect
Glitch filter Glitch filter Glitch filter Glitch filter
NFE[0] NFE[1] NFE[2] NFE[3]
NDSS[0] NWRE[0] NREE[0] NFEE[0] NDSS[1] NWRE[1] NREE[1] NFEE[1] NDSS[2] NWRE[2] NREE[2] NFEE[2] NDSS[3] NWRE[3] NREE[3] NFEE[3]
NMI Configuration Register (NCR)
Figure 172. NMI pad diagram
45.3.1.1 NMI management
You can enable or disable each NMI independently. You can perform this by using the registers, NCR which are laid out to contain
all configuration bits for a given NMI in a single byte (see NMI Configuration (NCR) ). You can configure a pad defined as an NMI
to recognize interrupts with an active rising edge, falling edge, or both edges being active. If both the edge events are disabled,
it results in no interrupt being detected and does not get configured.
You can also control an active NMI edge through the configuration of the fields, NCR[NREE n ] and NCR[NFEE n ].
NOTE
After a reset, NREE and NFEE are set to 0, which disables the NMI functionality and software enables it explicitly.
After a pad's NMI functionality is enabled, the pad cannot be reconfigured to override or disable the NMI. See the chip-specific
WKPU information for details of NMI implementation.

---

*Page 1283*

Wakeup Unit (WKPU)
Additionally, you can control the NMI destination interrupt through the configuration of the field, NCR[NDSS n ]. See NMI
Configuration (NCR) for details.
Each NMI supports a status flag and an overrun flag, both of which are located in NMI Status Flag (NSR) . This register prevents
an inadvertent overwriting of the other flags in the same register. The status flag is set whenever an NMI event is detected.
The overrun flag is set whenever an NMI event is detected when the status flag is still set (that is, the status flag has not yet
been cleared).
NOTE
The overrun flag is cleared by writing 1 to the appropriate overrun field in NMI Status Flag (NSR) . If the status field
is 0 but the overrun field is still, the pending interrupt is not cleared.
During an NMI ISR, on wake-up of the chip from an NMI, any writes to the ECC-protected memory must have the
correct ECC.

#### 45.3.2 Clocking

This module has no clocking considerations.
WKPU has ipg_clk and ipg_clk_s as input clocks having the same clock frequency for register configuration and internal logic.

#### 45.3.3 Interrupts

WKPU supports 4 interrupt vector s to the interrupt controller of the chip. Each interrupt vector supports multiple external interrupt
sources from the device pads, with the total across all vectors being equal to the number of external interrupt sources. Each
external interrupt source is assigned to exactly one interrupt vector. The interrupt vector assignment follows a sequence: one
interrupt vector is for external interrupt sources 0 to N-1, the next is for N to N+M-1, and so on.
See the following figure for an overview of the external interrupt implementation, showing an example of four interrupt vectors with
eight external interrupt sources each.
NOTE
The following figure represents a generic WKPU configuration. For details on this chip's configuration, see the
chip-specific WKPU information.

---

*Page 1284*

Wakeup Unit (WKPU)
Interrupt
vectors
Interrupt Mode and
controller power control
IRQ_31_24 IRQ_23_16 IRQ_15_08 IRQ_07_00
Wake-up enable
WRER
OR OR OR OR
IRER Interrupt enable
Flag[31:24] Flag[23:16] Flag[15:8] Flag[7:0] WISR
Rising
Edge detection WIREER
Glitch filter enable Falling
WIFER Analog glitch filter WIFEER
Interrupt edge enable
Pads
Figure 173. External interrupt pad diagram
All the external interrupt pads within a single group have equal priority. You must search through the group of sources in the most
appropriate way for their application.
The priority of the vectors used by the external interrupt pads is set based on the platform and the priority levels of the interrupt
controller. However, the chip can allow an independent configuration of pad allocation to each group of interrupts.
The external interrupt lines have a digital glitch filter applied to them.
45.3.3.1 External interrupt management
You can enable or disable each external interrupt independently using a single rolled-up register ( Interrupt Request Enable
(IRER) ). You can configure a pad defined as an external interrupt to recognize interrupts with an active rising edge, an active falling
edge, or both edges being active.
NOTE
Writing 0 to both IREE[ n ] and IFEE[ n ] disables the external interrupt functionality for that pad completely (means
no system wake-up or interrupt is generated from any activity on that pad).
You can control an active IRQ edge through the configuration of the registers, WIREER and WIFEER.
Each external interrupt supports an individual flag, which is held in the flag register, WISR. This W1C register prevents inadvertent
overwriting of other flags in the same register.

#### 45.3.4 External signals

This module has no external signals.

#### 45.4 Initialization

To initialize this module, you must perform the following configuration:
• Glitch filter and pad configuration

---

*Page 1285*

Wakeup Unit (WKPU)
• Nonmaskable interrupts
• Wake-up event

#### 45.4.1 Glitch filter and pad configuration

You can perform glitch filter control and pad configuration when the NMI is disabled to avoid erroneous triggering by glitches
caused by the configuration process.
When enabling the glitch filter, do not enable the rising and falling-edge events bits (that is, the NCR[NREE], NCR[NFEE],
NCR[NREE], and NCR[NFEE]) in the same register write.

#### 45.4.2 Nonmaskable interrupts

If IBE of NMI is tied off to 1, no false interrupt is expected.

#### 45.4.3 Wake-up event

See the chip-specific WKPU information for wake-up event initialization.

#### 45.5 WKPU memory map and registers

#### 45.5.1 WKPU register descriptions

This section provides a detailed description of all the registers accessible in the WKPU module.
NOTE
Reserved registers read as zero and writes have no effect. A transfer error is generated when trying to access a
completely reserved register space. The field length of external pad control registers depends on the number of
WKPU channels implemented in a chip.
45.5.1.1 WKPU memory map
WKPU base address: 402B_4000h
Offset Register Access Reset value
Width
(In bits)
0h NMI Status Flag (NSR) 32 RW 0000_0000h
8h NMI Configuration (NCR) 32 RW 6060_0000h
14h Wake-Up and Interrupt Status Flag (WISR) 32 RW 0000_0000h
18h Interrupt Request Enable (IRER) 32 RW 0000_0000h
1Ch Wake-Up Request Enable (WRER) 32 RW 0000_0000h
28h Wake-Up and Interrupt Rising-Edge Event Enable (WIREER) 32 RW 0000_0000h
2Ch Wake-Up and Interrupt Falling-Edge Event Enable (WIFEER) 32 RW 0000_0000h
30h Wake-Up and Interrupt Filter Enable (WIFER) 32 RW 0000_0000h
54h Wake-Up and Interrupt Status Flag (WISR_64) 32 RW 0000_0000h
58h Interrupt Request Enable (IRER_64) 32 RW 0000_0000h
5Ch Wake-Up Request Enable (WRER_64) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 1286*

Wakeup Unit (WKPU)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
68h Wake-Up and Interrupt Rising-Edge Event Enable (WIREER_64) 32 RW 0000_0000h
6Ch Wake-Up and Interrupt Falling-Edge Event Enable (WIFEER_64) 32 RW 0000_0000h
70h Wake-Up and Interrupt Filter Enable (WIFER_64) 32 RW 0000_0000h
45.5.1.2 NMI Status Flag (NSR)
Offset
Register Offset
NSR 0h
Function
Holds the nonmaskable interrupt status flags.
NOTE
This register is accessible by 8-, 16-, and 32-bit read/write operations.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
NOVF NOVF
R NIF0 Reserved NIF1 Reserved
0 1
W W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R Reserved Reserved Reserved Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 NMI Status Flag 0
NIF0 Causes an interrupt request when an event, as defined by NREE0 and NFEE0, has occurred.
If NREE0 or NFEE0 is 1, this flag causes an interrupt request.
Table continues on the next page...

---

*Page 1287*

Wakeup Unit (WKPU)
Table continued from the previous page...
Field Function
NOTE
This field behaves differently for register reads and writes.
When reading
0b - No event occurred on the pad
1b - An event occurred
When writing
0b - No effect
1b - Clear the flag
30 NMI Overrun Status Flag 0
NOVF0 Indicates whether an overrun has occurred on NMI input 0.
This flag has the same current value as of NIF0 (when the NMI event occurs), indicating that the
NMI occurred when the previous one was not serviced. If NREE0 or NFEE0 is 1, this flag causes an
interrupt request.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - No overrun occurred
1b - An overrun occurred
When writing
0b - No effect
1b - Clear the flag
Reserved
29-24
—
23 NMI Status Flag 1
NIF1 Causes an interrupt request when an event, as defined by NREE1 and NFEE1, has occurred.
If NREE1 or NFEE1 is 1, this flag causes an interrupt request.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - No event occurred on the pad
1b - An event occurred
When writing
Table continues on the next page...

---

*Page 1288*

Wakeup Unit (WKPU)
Table continued from the previous page...
Field Function
0b - No effect
1b - Clear the flag
22 NMI Overrun Status Flag 1
NOVF1 Indicates whether an overrun has occurred on NMI input 1.
This flag has the same current value as of NIF1 (when the NMI event occurs), indicating that the
NMI occurred when the previous one was not serviced. If NREE1 or NFEE1 is 1, this flag causes an
interrupt request.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - No overrun occurred
1b - An overrun occurred
When writing
0b - No effect
1b - Clear the flag
Reserved
21-16
—
Reserved
15-14
—
Reserved
13-8
—
Reserved
7-6
—
Reserved
5-0
—
45.5.1.3 NMI Configuration (NCR)
Offset
Register Offset
NCR 8h

---

*Page 1289*

Wakeup Unit (WKPU)
Function
Holds the configuration fields for the nonmaskable interrupt settings.
NOTE
• This register is accessible by 8-, 16-, and 32-bit read/write operations.
• Writing 0 to both NREE[ n ] and NFEE[ n ] disables the NMI functionality completely (means no nonmaskable
interrupt is generated on any pad activity).
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
NLOC NWRE NREE NLOC NWRE NREE
NDSS0 NFEE0 NFE0 NDSS1 NFEE1 NFE1
K0 0 0 K1 1 1
W
Reset 0 1 1 0 0 0 0 0 0 1 1 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R Reserved Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 NMI Configuration Lock Register 0
NLOCK0 Specifies the lock configuration for the NMI.
When you write 1 to this field, it locks the configuration for the NMI until it is unlocked by a system reset or
Standby0 mode exit.
0b - No effect
1b - Locks the configuration for the NMI
30-29 NMI Destination Source Select 0
NDSS0 Specifies the NMI destination interrupt to platform.
NOTE
As wake-up interrupt does not support another interrupt than NMI, the destination source
select signal bits are reserved and always retain their reset value. This means no other
request other than NMI can be generated.
00b - Nonmaskable interrupt
01b - Reserved
10b - Reserved
11b - Reserved. Reserved
28 NMI Wake-Up Request Enable 0
Table continues on the next page...

---

*Page 1290*

Wakeup Unit (WKPU)
Table continued from the previous page...
Field Function
NWRE0 Enables a system wake-up request when the corresponding NIF0 = 1 or NOVFO = 1.
0b - Disable
1b - Enable
Reserved
27
—
26 NMI Rising-Edge Events Enable 0
NREE0
Enables the NMI rising-edge event.
0b - Disable
1b - Enable
25 NMI Falling-Edge Events Enable 0
NFEE0
Enables the NMI falling-edge event.
0b - Disable
1b - Enable
24 NMI Filter Enable 0
NFE0
Enables analog glitch filter on the NMI pad input.
0b - Disable
1b - Enable
23 NMI Configuration Lock 1
NLOCK1 Specifies the lock configuration for the NMI.
When you write 1 to this field, it locks the configuration for the NMI until it is unlocked by a system reset or
Standby0 mode exit.
0b - No effect
1b - Locks the configuration for the NMI
22-21 NMI Destination Source Select 1
NDSS1 Specifies the NMI destination interrupt to platform.
NOTE
As wake-up does not support another interrupt than NMI, the destination source select
signal bits are reserved and always retain their reset value. This means no other request
other than NMI can be generated.
00b - Nonmaskable interrupt
01b - Reserved
Table continues on the next page...

---

*Page 1291*

Wakeup Unit (WKPU)
Table continued from the previous page...
Field Function
10b - Reserved
11b - Reserved
20 NMI Wake-Up Request Enable 1
NWRE1 Enables a system wake-up request when the corresponding NIF1 = 1 or NOVF1 = 1.
0b - Disable
1b - Enable
Reserved
19
—
18 NMI Rising-Edge Events Enable 1
NREE1
Enables the NMI rising-edge event.
0b - Disable
1b - Enable
17 NMI Falling-Edge Events Enable 1
NFEE1
Enables the NMI falling-edge event.
0b - Disable
1b - Enable
16 NMI Filter Enable 1
NFE1
Enables analog glitch filter on the NMI pad input.
0b - Disable
1b - Enable
Reserved
15-8
—
Reserved
7-0
—
45.5.1.4 Wake-Up and Interrupt Status Flag (WISR)
Offset
Register Offset
WISR 14h

---

*Page 1292*

Wakeup Unit (WKPU)
Function
Holds the wake-up and interrupt flags.
NOTE
• This register is accessible only by 32-bit read/write operations.
• The status fields associated with on-chip wake-up sources are located to the left of the external wake-up and
interrupt status fields and are read-only. The wake-up for these sources must be configured and cleared at
the on-chip wake-up source. Also, the configuration registers for the external interrupts/wake-ups do not have
corresponding bits.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R EIF
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R EIF
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 External Wake-Up and Interrupt Status Flag
EIF Specifies whether an event, as defined by WIREER and WIFEER, has occurred on the pad. If enabled
(IRER[ n ]), EIF[ n ] causes an interrupt request.
0b - No event occurred
1b - An event occurred
45.5.1.5 Interrupt Request Enable (IRER)
Offset
Register Offset
IRER 18h
Function
Enables the interrupt messaging from the wake-up and interrupt pads to the interrupt controller.

---

*Page 1293*

Wakeup Unit (WKPU)
NOTE
This register is accessible only by 32-bit read/write operations.
If a pin is disabled through this register, you must write 0 to the corresponding fields in WIFEER and WIREER to
ensure that the pin does not respond to any change.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
EIRE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
EIRE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 External Interrupt Request Enable
EIRE Enables interrupt requests from the corresponding field, EIF[ n ].
0b - Disable
1b - Enable (with EIF[ n ] set)
45.5.1.6 Wake-Up Request Enable (WRER)
Offset
Register Offset
WRER 1Ch
Function
Enables the system wake-up messaging from the wake-up and interrupt pads to the mode entry and power control modules.
NOTE
This register is accessible only by 32-bit read/write operations.
If a pin is disabled through this register, you must write 0 to the corresponding fields in WIFEER and WIREER to
ensure that the pin does not respond to any change.

---

*Page 1294*

Wakeup Unit (WKPU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
WRE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
WRE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 External Wake-Up Request Enable
WRE Enables system wake-up requests from the corresponding field, EIF[ n ].
0b - Disable
1b - Enable (with EIF[ n ] set)
45.5.1.7 Wake-Up and Interrupt Rising-Edge Event Enable (WIREER)
Offset
Register Offset
WIREER 28h
Function
Enables rising-edge triggered events on the corresponding wake-up/interrupt pads.
NOTE
• This register is accessible only by 32-bit read/write operations.
• WIREER or WIFEER is configured for rising or falling edge after WRER to enable wake-up source.

---

*Page 1295*

Wakeup Unit (WKPU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
IREE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
IREE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 External Interrupt Rising-Edge Events Enable
IREE Enables interrupt rising-edge event.
0b - Disable
1b - Enable
45.5.1.8 Wake-Up and Interrupt Falling-Edge Event Enable (WIFEER)
Offset
Register Offset
WIFEER 2Ch
Function
Enables falling-edge triggered events on the corresponding wake-up and interrupt pads.
NOTE
• This register is accessible only by 32-bit read/write operations.
• WIREER or WIFEER is configured for rising or falling edge after WRER to enable wake-up source.

---

*Page 1296*

Wakeup Unit (WKPU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
IFEEx
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
IFEEx
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 External Interrupt Falling-Edge Events Enable
IFEEx Enables interrupt falling-edge event.
0b - Disable
1b - Enable
45.5.1.9 Wake-Up and Interrupt Filter Enable (WIFER)
Offset
Register Offset
WIFER 30h
Function
Enables an analog filter on the corresponding interrupt pads to filter out glitches on the inputs.
NOTE
This register is accessible only by 32-bit read/write operations.

---

*Page 1297*

Wakeup Unit (WKPU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
IFE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
IFE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 External Interrupt Filter Enable
IFE
Enables analog glitch filter on the external interrupt pad input.
0b - Disable
1b - Enable
45.5.1.10 Wake-Up and Interrupt Status Flag (WISR_64)
Offset
Register Offset
WISR_64 54h
Function
Holds the wake-up and interrupt flags.
NOTE
• This register is accessible only by 32-bit read/write operations.
• Status fields associated with on-chip wake-up sources are located to the left of the external wake-up/interrupt
status fields and are read-only. The wake-up for these sources must be configured and cleared at the
on-chip wake-up source. Also, the configuration registers for the external interrupts/wake-ups do not have
corresponding fields.

---

*Page 1298*

Wakeup Unit (WKPU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R EIF_1
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R EIF_1
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 External Wake-Up and Interrupt Status Flag
EIF_1 Specifies whether an event, as defined by WIREER and WIFEER, has occurred on the pad. If enabled
(IRER[ n ]), EIF[ n ] causes an interrupt request.
0b - No event occurred
1b - An event occurred
45.5.1.11 Interrupt Request Enable (IRER_64)
Offset
Register Offset
IRER_64 58h
Function
Enables interrupt messaging from the wake-up and interrupt pads to the interrupt controller.
NOTE
This register is accessible only by 32-bit read/write operations.

---

*Page 1299*

Wakeup Unit (WKPU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
EIRE_1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
EIRE_1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 External Interrupt Request Enable
EIRE_1 Enables interrupt requests from the corresponding field, EIF[ n ].
0b - Disable
1b - Enable (with EIF[ n ] set)
45.5.1.12 Wake-Up Request Enable (WRER_64)
Offset
Register Offset
WRER_64 5Ch
Function
Enables system wake-up messaging from the wake-up and interrupt pads to the mode entry and power control modules.
NOTE
This register is accessible only by 32-bit read/write operations.
If a pin is disabled through this register, you must write 0 to the corresponding fields in WIFEER and WIREER to
ensure that the pin does not respond to any change.

---

*Page 1300*

Wakeup Unit (WKPU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
WRE_1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
WRE_1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 External Wake-Up Request Enable
WRE_1 Enables system wake-up requests from the corresponding field, EIF[ n ].
0b - Disable
1b - Enable (with EIF[ n ] set)
45.5.1.13 Wake-Up and Interrupt Rising-Edge Event Enable (WIREER_64)
Offset
Register Offset
WIREER_64 68h
Function
Enables rising-edge triggered events on the corresponding wake-up and interrupt pads.
NOTE
• This register is accessible only by 32-bit read/write operations.
• WIREER or WIFEER is configured for rising or falling-edge after WRER to enable wake-up source.

---

*Page 1301*

Wakeup Unit (WKPU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
IREE_1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
IREE_1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 External Interrupt Rising-edge Events Enable
IREE_1 Enables an external interrupt rising-edge event.
0b - Disable
1b - Enable
45.5.1.14 Wake-Up and Interrupt Falling-Edge Event Enable (WIFEER_64)
Offset
Register Offset
WIFEER_64 6Ch
Function
Enables falling-edge triggered events on the corresponding wake-up and interrupt pads.
NOTE
• This register is accessible only by 32-bit read/write operations.
• WIREER or WIFEER is configured for rising or falling-edge after WRER to enable a wake-up source.

---

*Page 1302*

Wakeup Unit (WKPU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
IFEEx_1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
IFEEx_1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 External Interrupt Falling-Edge Events Enable
IFEEx_1 Enables an external interrupt falling-edge event.
0b - Disable
1b - Enable
45.5.1.15 Wake-Up and Interrupt Filter Enable (WIFER_64)
Offset
Register Offset
WIFER_64 70h
Function
Enables an analog filter on the corresponding interrupt pads to filter out glitches on the inputs.
NOTE
This register is accessible only by 32-bit read/write operations.

---

*Page 1303*

Wakeup Unit (WKPU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
IFE_1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
IFE_1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 External Interrupt Filter Enable
IFE_1
Enables an analog glitch filter on the external interrupt pad input.
0b - Disable
1b - Enable

#### 45.6 Glossary

NMI Nonmaskable interrupts

---

*Page 1304*

