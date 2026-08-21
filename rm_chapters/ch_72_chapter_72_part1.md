<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 72 -->

# Chapter 72

# Ethernet Media Access Controller (EMAC)

#### 72.1 Chip-specific EMAC information

#### 72.1.1 EMAC instance and configuration

This chip supports up to 1 instance of EMAC IP.
Table 473. EMAC instance
Instance MCXE31B MCXE315/MCXE316/ MCXE317
EMAC Yes No
NOTE
DWC_EQOS refer to the Synopsys Ethernet MAC IP.
NOTE
The description for some EMAC registers include references to configurations of the Synopsis Ethernet MAC IP.
Refer to MAC_HW_Feature registers for the specific features available in this chip.
NOTE
EMAC operates only in Clock options A and B, since the module clock becomes lower than the protocol clock
(RMII/MII clocks) in other modes.
Following key features are supported:
• This chip supports only MII/RMII interfaces
• 4 bit MII interface operating at 2.5/25/50Mhz, MII can run at 10/100/200 Mbps.
• 2 bit RMII interface operating at 50Mhz, RMII can run at 10/100Mbps
• 4 bit MII-Lite interface operating at 2.5/25Mhz
• 4 PPS are supported using DMA trigger
• 200Mbps data rate is supported on Ethernet MII interface
• MTL Receive FIFO size 8192bytes
• MTL Transmit FIFO size 8192bytes
NOTE
The DMA referred within EMAC chapter refers to the internal EMAC DMA engine and not the device AXBS
master DMA.
NOTE
Register access beyond 8k address space is not supported and will generate a transfer error.
NOTE
Always use store and forward mode if possible. In cut through mode, if multiple masters are active then there is
chance (even in round robin arbitration mode) that EMAC DMA may starve for data resulting in insufficient data in
TX FIFO leading to Tx underflow. To avoid this, increase MTL_TxQ0_Operation_Mode[TTC] value as needed.
The table below provides the information on the specific configuration utilized for the MCXE31B devices.

---

*Page 2520*

Ethernet Media Access Controller (EMAC)
Table 474. Specific configuration information
Application Interface
Application interface configuration EQOS-AHB
Data width 32
Endian mode Little Endian
Address width 32
CSR interfcae APB3 interface
General feature
Mode of operation 10/100
Enable double VLAN processing Yes
Enable Que/channel based VLAN tag Yes
inertion on Tx
Enable SA and VLAN insertion on Tx Enabled by default
Buffer managemnet
MTL Receive FIFO size in bytes 8192
MTL Receive FIFO size in bytes 8192
Enable debug memory access Yes
Use single port memory Yes
PHY interface
Enable RGMII, RMII only Enable RMII, GMII/MII default
Enabled signel phy interface No
Enable MDIO interface Yes
Filtering
Enable Additional 1-31 MAC 2
address registers
Enable Address Filter Hash Table Yes
Hash Table Size 64
Enable VLAN Hash table based filtering Yes
Extended Rx VLAN Filter Enable Yes
Number of VLAN tag Filters 4
Enable Layer3 and Layer4 Packet Filter Yes
Number of layer3 and Layer 4 4
packet filters
Enable Flexible Programmable Yes
Receive Parser
Table continues on the next page...

---

*Page 2521*

Ethernet Media Access Controller (EMAC)
Table 474. Specific configuration information (continued)
Maximum Packet Header Size 128
for Parsing
Maximum Entries of Parser Look up table 64
Packet duplication support Yes
IEEE 1588 timestamp
Enable IEEE 1588 Time stamp support Enabled by default
IEEE 1588 System Time Support Both option
Add IEEE 1588 Higher word register Yes
Enable IEEE 1588 sub nanoseconds Yes
time stamp support
Add IEEE 1588 Auxilary snapshot No
Enable Flexible Pulse Per Yes
Seconds Output
Number of pulse per Second outputs 4
Enable PTP Timestamp offload feature No
Enable One Step timestamp for PTP over No
UDP/IP feature
Enable One Step timestamp feature Yes
Multiple queue support
Number of transmit Queue 2
Number of recieve Queue 2
Enable data center Bridging No
Enable Audo Vedio Bridging Yes
Enable support for AV in Tx queue 1 No
Number of DMA Channel on the 2
receive side
Time sensitive networking
Enable Enhancement to Yes
Schedule traffic(EST)
Width of Time Interval Field in the Gate 24
Control List
Depth of Gate Control List 256
Enable Frame Pre-emption support Yes
Time based scheduling
Enable time based Scheduling Yes
TCP/IP offloading features
Table continues on the next page...

---

*Page 2522*

Ethernet Media Access Controller (EMAC)
Table 474. Specific configuration information (continued)
Enable receive TCP/IP Checksum Check Enabled by default
Enable transmit TCP/IP Enabled by default
Checksum offload
Enable support for Tx COE in Tx queue 0 Yes
Enable support for Tx COE in Tx queue 1 No
Enable TCP Segmentation offloading for No
TCP/IP
Enable Split Header Feature No
Enable IPv4 ARP offload No
RMON counters
Enable MAC Yes
Management Counters(MMC)
Enable the MAC Management Counters No
for Reciever TCP/IP Checksum offload

#### 72.1.2 Impact of MAC flush commands on MAC_PPSx_Target_Time_Seconds register

If the software discards the media clock generation request by configuring the PPSCTRL_PPSCMD (or PPSCMD#i)
field of MAC_PPS_Control register to 0, the MAC does not flush the captured presentation time, i.e., the
MAC_PPSx_Target_Time_Seconds register contents don't get changed.

#### 72.2 Overview

This section and all its sub-sections are Synopsys Proprietary. Used with permission.
EMAC:
• Supports the 10/100 Mbps application in compliance with IEEE802.3-2015 specifications
• Can support advanced applications such as time-sensitive networking and AVB
• Supports a MAC core that has prominent features such as a flexible receive parser, media clock recovery and generation,
and safety

#### 72.2.1 Features

EMAC supports these features in addition to the default feature defined in the IEEE802.3 specification:
• MII (10/100 Mbps), RMII (10/100 Mbps)
• Time-aware shaper (IEEE802.1bv), time synchronization (IEEE802.1AS-rev), and frame preemption (IEEE802.1Qbu) for
time-sensitive networking
• Media clock recovery and generation for AVB
• AMBA 2.0 for AHB master and APB3 interface
• RMII specification version 1.2 from RMII consortium
• Separate interface for transmit, receive, and control paths
• Two-buffer ring
• Broadcast and multicast packet duplication
• Full-duplex flow control operations (IEEE 802.3x pause packet and priority flow control)

---

*Page 2523*

Ethernet Media Access Controller (EMAC)
• Network statistics with MAC management ( RMON ) counters
• Flexibility to control the pulse per second (PPS) output signal
• MDIO clause 22 and clause 45 interface for configuration and management of the PHY device
• Preamble and SFD insertion and deletion
NOTE
Only byte-aligned preamble is supported.
• Automatic CRC and pad generation/stripping option
• Option to disable CRC checking
• Programmable insert packet gap
• Source address insertion or replacement
• VLAN insertion, replacement, and deletion in transmitted packets with per-packet or static-global control, insertion,
replacement, and deletion of up to two VLAN tags, insertion, replacement, and deletion of queue or channel-based VLAN
tags
• IEEE802.1Q VLAN tag detection and an option to delete the tags in the receive packets
• Programmable safety watchdog timeout limits
• Flexible address filtering modes that allow:
— Up to two additional 48-bit perfect DA filters with masks for each byte
— Up to two 48-bit SA comparison checks with masks for each byte
— 64-bit hash filter for multicast and unicast DA addresses
— Option to pass all multi-cast addressed packets
— Promiscuous mode to pass all packets without any filtering for network monitoring
— Passing of all incoming packets (according to filter) with a status report
• Additional packet filtering that is based on:
— Virtual local area network (VLAN) tag—perfect match and hash-based filtering, which is based on either outer or
inner VLAN tag, is possible
— Layer 3 and layer 4— TCP or UDP over IPv4 or IPv6
— Extended VLAN tag—filtering based on four filter selections
NOTE
The DCB feature discussed in the following section(s) is not supported.

#### 72.2.2 Block diagram

This figure shows the EMAC block diagram. EMAC has four main blocks to perform all the functions.
• The AHB interface is connected to all DMA channels.
• The DMA arbiter helps in the arbitration of all paths (transmit and receive) in DMA channels.
• Each channel has a separate set of control and status registers (CSRs) for managing the transmit and receive functions,
descriptor handling, and interrupt handling.

---

*Page 2524*

Ethernet Media Access Controller (EMAC)
Transmit
memory
Transmit MTL transmit
MAC RGMII/RMII
DMA FIFO
transmit interface
channel control
Receive MTL receive
AHB MAC
DMA FIFO Select
interface receive GMII
channel control
DMA arbiter
CSR
MTL MAC
DMA Core
CSR CSR
channel
APB
interace DMA MTL
AHB
Receive
memory
Figure 388. System-level block diagram

#### 72.3 Architecture

This section and all its sub-sections are Synopsys Proprietary. Used with permission.
The section describes EMAC interfaces, protocols, and functionality. These are its main blocks:
• AHB master and APB3 slave interface
• DMA controller
• MTL
• MAC
• Interrupts

#### 72.3.1 AHB master and CSR slave (APB3) interface

The 32-bit APB3 slave interface:
• Provides access to DMA, MTL, and MAC control and status registers (CSRs)
• Supports the 32-bit write and read transfers to these CSRs
• Supports single- and all-burst transfers, and an OKAY response
The DMA block uses the AHB master interface to interact with the external world. The AHB master interface:
• Controls data transfer
• Is AMBA 2.0-compliant with no restrictions
• Supports 32-bit data transfer

---

*Page 2525*

Ethernet Media Access Controller (EMAC)
• Supports single and INCR transfers
• Supports burst-length modes such as Fixed, Unspecified, and Mixed

#### 72.3.2 DMA inclusions

DMA controller:
• Includes independent transmit (Tx) and receive (Rx) engines, and a CSR space. The transmit engine transfers data from
the system memory to the MTL interface, whereas the receive engine transfers data from the MTL interface to the system
memory.
• Is designed for packet-oriented data transfers such as packets in Ethernet.
• Communicates with the host through CSR, descriptor lists, and data buffers.
DMA descriptors
DMA supports up to two transmit and two receive descriptor lists (or DMA channels). The base address of each list is written to the
respective transmit and receive descriptor list address registers. A descriptor list is forward linked and the next descriptor is always
considered at a fixed offset to the current one, and DMA_CH0_Control[DSL] controls the offset. The number of descriptors in the
list is programmed using the respective DMA Channel 0 Tx Descriptor Ring Length (DMA_CH0_TxDesc_Ring_Length) and DMA
Channel 0 Rx Descriptor Ring Length (DMA_CH0_RxDesc_Ring_Length) registers. After the DMA processes the last descriptor
in the list, it automatically jumps to the descriptor in the list address register to create a descriptor ring.
EMAC supports the ring structure for DMA descriptors. For more information, see Descriptors .
The DMA engine uses descriptors to efficiently move data from source to destination with minimal host intervention, and the DMA
controller can be programmed to interrupt the host in certain situations. These situations may include packet transmit and receive
transfer completion and other normal or error conditions.
The descriptor lists reside in the physical memory address space of the application. Each descriptor can point to a maximum of
two buffers in the system memory, enabling two buffers to be used and physically addressed rather than contiguous buffers in
the memory.
Data buffers
A data buffer resides in the application physical memory space and consists of an entire packet or part of a packet but cannot
exceed a single packet. Buffers contain only data. The buffer status is maintained in the descriptor. Data chaining refers to packets
that span multiple data buffers. However, a single descriptor cannot span multiple packets. The DMA skips to the data buffer of
the next packet when an EOP is detected.
The next few sections discuss DMA controller.
72.3.2.1 DMA controller bus burst access
DMA engines attempt to transfer data in a burst of the maximum size programmed using DMA_CH0_Tx_Control[TxPBL]
and DMA_CH0_Rx_Control[RxPBL] of the respective DMAs, which always access the receive and transmit descriptors in the
maximum possible (limited by PBL or 16 * 8/bus width) burst length for 16 bytes to be read. The burst transfers that the DMA
initiates can be split into multiple burst transfers according to the AHB requirements and settings specified in DMA System Bus
Mode (DMA_SysBus_Mode) .
The transmit DMA initiates a data transfer only when sufficient space is available in the MTL transmit queue to accommodate either
of these:
• Bytes corresponding to the configured burst (PBL * bus_width/8)
• Remaining bytes in the transmit buffer without EOP
• Number of bytes till EOP
The receive DMA initiates a data transfer in these conditions:
• Sufficient data is available in the MTL receive queue to accommodate the configured burst
• EOP (when it is less than the configured burst length) is detected in the receive queue

---

*Page 2526*

Ethernet Media Access Controller (EMAC)
DMA indicates the start address and the number of transfers required to the AHB master interface. When the interface is
configured for fixed-length burst, it transfers data by using the best combination of the INCR4, INCR8, or INCR16 and SINGLE
transactions. If EOP is reached before the fixed burst ends on the AHB interface, DMA performs dummy transfers to complete
the fixed burst. Otherwise, DMA_SysBus_Mode[FB] becomes 0. The DMA transfers the data using undefined length (INCR) and
SINGLE transactions.
When the AHB interface is configured for address-aligned beats, both DMA engines ensure that the first-burst transfer that AHB
initiates is less than or equal to the size of the configured PBL value, and the subsequent beats start at an address aligned to this
value. The DMA can only align the address for beats up to size 16 (for PBL > 16) for the AHB interface because it does not support
more than INCR16.
72.3.2.2 DMA application data buffer alignment
The transmit and receive data buffers do not have any restrictions on start address alignment. For example, in systems with 32-bit
memory, the start address for buffers can be aligned to any of the four bytes. However, the DMA always initiates write transfers
with an address aligned to the bus width and dummy data (old data) in the invalid byte lanes. The process of write transfer typically
happens during the beginning or end of an Ethernet packet transfer. The software driver must discard the dummy bytes based on
the start address of the buffer and size of the packet.
Table 475. Data buffer alignment examples
Examples
Buffer read If the transmit buffer address is FF2h (for a 32-bit data bus) and 15 bytes need to be transferred, the DMA reads
five full words from address FF0h. However, when DMA transfers data to the MTL transmit queue, the extra
bytes (the first two bytes) are dropped or ignored. Similarly, the last 3 bytes of the last transfer are also ignored.
The DMA always ensures that it transfers a full 32-bit data to the MTL transmit queue, unless it is the end of
the packet.
Buffer write If the receive buffer address is FF2h (for a 64-bit data bus) and 16 bytes of a received packet need to be
transferred, the DMA writes 3 full words from address FF0h. However, the first 2 bytes of the first transfer and
the last 6 bytes of the third transfer have dummy data. The DMA considers the offset address only if it is the first
receive buffer of the packet. It ignores the offset address and performs full-word writes for the middle and last
receive buffers of the packet.
72.3.2.3 DMA buffer size calculations
DMA does not update the size fields in the transmit and receive descriptors. It updates only the status fields (RDES and TDES)
of the descriptors. The driver performs the size calculations.
The transmit DMA transfers the exact number of bytes (as the buffer size field of TDES2 indicates) to MAC. If a descriptor is
marked as first (TDES3[FD] = 1), the DMA marks the first transfer from the buffer as SOP, and if the descriptor is marked as last
(TDES3[LD]), the DMA marks the last transfer from that data buffer as EOP to the MTL.
The receive DMA transfers data to a buffer until the buffer is full or MTL sends the end of packet. When TDES3[FD] = 1, the amount
of valid data in a buffer is accurately indicated by the buffer size fields in DMA Channel Rx Control (DMA_CH0_Rx_Control) minus
the data buffer pointer offset. The offset is 0 when the data buffer pointer is aligned to the data bus width. If a descriptor is marked
as last, the buffer may not be full (as indicated by DMA_CH0_Rx_Control[RBSZ_13_y] and DMA_CH0_Rx_Control[RBSZ_x_0] ).
To compute the amount of valid data in this final buffer, the driver must read the packet length (PL fields of RDES3[14:0]) and
subtract the sum of the buffer sizes of the preceding buffers in this packet. The receive DMA always transfers the start of the next
packet with a new descriptor.
72.3.2.4 DMA arbiter
The arbiter inside the DMA module performs the arbitration between the transmit and receive channel accesses to the AHB master
interface. These two types of arbitrations are supported:

---

*Page 2527*

Ethernet Media Access Controller (EMAC)
• Round-robin: If DMA_Mode[DA] = 0 and both transmit and receive DMAs simultaneously request access, the arbiter
allocates the data bus in ratio sets defined in DMA_Mode[PR] .
• Fixed-priority: If DMA_Mode[DA] = 1, receive DMA is always prioritized over transmit DMA for data access by default. If
DMA_Mode[TXPR] = 1, transmit DMA is prioritized over receive DMA as explained in Table 492 .

#### 72.3.3 MTL

MTL provides the FIFO memory interface to buffer and regulate the packets between system memory and MAC. It also enables
data to be transferred between the system clock and MAC clock domains. The MTL layer has two data paths: transmit path and
receive path. MTL communicates with the host through ATI on the transmit path and through ARI on the receive path.
72.3.3.1 Transmit path
The internal DMA:
• Handles all transactions for the transmit path through ATI.
• Pushes the Ethernet packet reads from system memory to the corresponding queue.
The Ethernet packet, then, pops out and is transferred to MAC when the queue reaches its threshold (Threshold mode) or if the
complete packet is in the queue (Store-and-Forward mode). When EOP is transferred, the status of the transmission is taken from
MAC and transferred back to the internal DMA.
72.3.3.1.1 Transmit control word
This control information related to packet transmission is provided as part of the control word through the ATI interface:
• Packet length (if DCB is enabled with WFQ scheduling algorithm)
• CRC pad control
• Source address insertion control
• VLAN insertion and replacement control and VLAN tag for outer and inner VLAN tags
• TCP/IP checksum insertion control
• One-step timestamping control correction
• Transmit timestamp enable
72.3.3.1.2 Transmit operation
These two modes of operation trigger data read towards MAC:
• Threshold (or cut-through) mode: This is the default mode. In this mode, as soon as the number of bytes in
the queue crosses the configured threshold level (or when the end of packet is written before the threshold is
crossed), the data is ready to be popped out and forwarded to MAC. The threshold level is configured by using
MTL_TxQ0_Operation_Mode[TTC] .
• Store-and-Forward mode: In this mode, MTL pops the packet out to MAC only when one or more of the following
conditions are true:
— A complete packet is stored in the queue.
— The transmit FIFO becomes almost full.
— The ATI watermark becomes low.
The watermark becomes low when the requested queue is not spacious enough to accommodate the requested burst length
on ATI. Therefore, MTL, when operating in Store-and-Forward mode, allows packet transmission even if the packet length is
bigger than the transmit queue size.
You can flush the complete content of the transmit queue by writing 1 to MTL_TxQ0_Operation_Mode[FTQ] or
MTL_TxQ1_Operation_Mode[FTQ] , depending on the queue. Doing so initializes the queue pointers to the default state. The FTQ

---

*Page 2528*

Ethernet Media Access Controller (EMAC)
field returns to 0 by itself. If you write 1 to the FTQ field during a packet transfer from MTL to MAC, MTL stops further transfers
because MTL considers the queue to be empty. Therefore, an underflow event occurs at the MAC transmitter.
72.3.3.1.3 Initialization flow
After reset, MTL is ready to manage the data flow between DMA and MAC.
• With single-transmit queue configurations, there are no initialization requirements for enabling MTL.
• With multiple-transmit queue configurations, initialize the queue size for each of the queues by programming
MTL_TxQ0_Operation_Mode[TQS] corresponding to a transmit queue. Also, initialize the MAC block. Internal DMA
controllers must be individually enabled through their respective CSRs.
72.3.3.2 Receive path
MAC sends packets to the MTL receive module and pushes them into the receive queue. MTL indicates the status (fill level) of the
queue to the application or DMA when it crosses the configured receive threshold ( MTL_RxQ0_Operation_Mode[RTC] ), or when
the MTL receive module receives the complete packet in Store-and-Forward mode. MTL also indicates the fill level of the queue
so that DMA can initiate preconfigured burst transfers to the AHB interface.
72.3.3.2.1 Receive operation
During a receive operation, MTL is MAC's slave. This is the general sequence of events:
1. When MAC receives a packet, it indicates the availability of receive data.
2. MAC indicates the SOP and EOP delimiters.
3. MTL accepts the data and pushes it into the corresponding receive queue.
4. When MAC transfers EOP to the MTL receive module, MAC also drives the status word that MTL pushes into the
corresponding receive queue.
5. MTL takes the data out of the queue and sends it to DMA depending on these modes:
• Threshold mode
• Store-and-Forward mode
The sections that follow discuss these modes.
72.3.3.2.1.1 Threshold mode
In Threshold (default) mode, MTL reads the data and indicates its availability to the application or DMA when one of these occurs:
• Data bytes equal to the threshold amount are written to the receive queue (to MTL_RxQ0_Operation_Mode[RTC] and
MTL_RxQ1_Operation_Mode[RTC] ).
• A full packet of data is received into the queue.
72.3.3.2.1.2 Store-and-Forward mode
In this mode (when MTL_RxQ0_Operation_Mode[RSF] = 1), the initial receive queue locations are reserved for the status words
before writing the SOP. A packet is read out only after it is completely written into the receive queue. In this mode, all error
packets are dropped (if configured through MTL_RxQ0_Operation_Mode[FEP] ) in such a way that only valid packets are read and
forwarded to the application.
72.3.3.2.2 Multi-packet receive operation
In Threshold mode, the packet status is available immediately after the packet data. In Store-and-Forward mode, the packet data
is available after the packet status. MTL is capable of storing any number of packets in the queue as long as it is not full.

---

*Page 2529*

Ethernet Media Access Controller (EMAC)
If MAC receives a packet when the corresponding receive queue is full, MTL ignores that packet and overflow pulse generates on
the corresponding receive queue. In addition, MTL increments the overflow counter in MTL Rx Queue Missed Packet Overflow
Count (MTL_RxQ0_Missed_Packet_Overflow_Cnt) for the corresponding queue.
72.3.3.2.3 Error handling in receive operation
MTL performs these actions if the MTL receive queue is full before it receives the EOP data from MAC:
1. Declares an overflow
2. Drops the whole packet (including the status word)
3. Increments the overflow counter in DMA ( MTL Rx Queue Missed Packet Overflow Count
(MTL_RxQ0_Missed_Packet_Overflow_Cnt) )
This is true even if MTL_RxQ0_Operation_Mode[FEP] is 1.
If the start address of such a packet is already transferred to the read controller, the rest of the packet is dropped and a
dummy EOP is written to the queue along with the status word with overflow status. The status indicates a partial packet
because of overflow. In such packets, the Packet Length field is invalid. If the MTL receive queue is configured to operate in the
Store-and-Forward mode and the length of the received packet is more than the queue size, overflow occurs and all such packets
are dropped.
The MTL receive control logic can filter errors and undersized packets, if enabled by using MTL_RxQ0_Operation_Mode[FEP]
and MTL_RxQ0_Operation_Mode[FUP] . If the start address of one such packet is already transferred to the receive queue read
controller, the packet is not filtered. The start address of the packet is transferred to the read controller after the packet crosses
the receive threshold defined using MTL_RxQ0_Operation_Mode[RTC] .
If the MTL receive queue is configured to operate in Store-and-Forward mode, all error packets can be filtered and dropped.
For the application or DMA to flush the error packet being read from the queue, it must assert the flush signal. MTL then stops
transferring data to the application (DMA). It internally reads the rest of the packet and drops it. MTL then starts transferring the
next packet, if it's available.

#### 72.3.4 MAC

MAC can support the MII and RMII PHY interface and it consists of:
• MTI
• MRI
• MCI
72.3.4.1 MAC transmission
The MAC transmission process is as follows:
1. The transmission initiates when MTL pushes in data with the SOP signal asserted.
2. After the SOP signal is detected, MAC accepts the data and begins transmitting to RMII or MII.
3. After the EOP signal is transferred to MAC, it performs one of these steps:
• Completes normal packet transmission and sends the transmission status to MTL
• Sends retry requests if a normal collision (in Half-Duplex mode) occurs during transmission, and until one of the
following is true:
— Packet is successfully transmitted.
— Maximum retry requests have expired. When this happens, MAC aborts the packet transmission with
"excessive collision transmit" status. MAC accepts and drops all further data until it receives the next SOP.
The MTL block must retransmit the same packet from SOP on observing a retry request (in the status) from
MAC.
• If any one of the following happens, MAC aborts the packet transmission:

---

*Page 2530*

Ethernet Media Access Controller (EMAC)
— No carrier (Half-Duplex mode)
— Loss of carrier (Half-Duplex mode)
— Excessive deferral (Half-Duplex mode)
— Late collisions (Half-Duplex mode)
— Jabber
— MAC accepts and drops all further data until it receives the next SOP
4. MAC issues an underflow status if MTL is not able to provide data continuously during transmission. Until the next SOP
is received, MAC accepts and drops all further data.
5. During the normal transfer of a packet from MTL, if MAC receives an SOP without getting an EOP for the previous
packet, it ignores the SOP and considers the new packet as a continuation of the previous packet.
72.3.4.2 MAC reception
The receive operation initiates as follows:
1. MAC detects a state-of-frame data on RMII or MII.
2. MAC strips the preamble and SFD before processing an Ethernet packet.
3. The MAC AFM :
• Checks the header fields (SA and DA) of an incoming packet for filtering
• Verifies the CRC contained in the packet's FCS field
4. MAC's AFM checks the header fields (SA and DA) of an incoming packet for filtering.
5. FCS verifies the CRC of the received packet.
6. MAC stores the received packet in a shallow buffer until address filtering is performed.
7. AFM drops the packet if it fails the address filter.

#### 72.3.5 Interrupts

EMAC supports interrupt coalescing, which reduces the number of interrupts generated by the module and reduces the CPU load.
MAC Interrupt Status (MAC_Interrupt_Status) captures various interrupt events. If the interrupt enable field of an interrupt is 1,
the corresponding interrupt generates based on the event status in the Status registers. The Interrupt mode (INTM) field decides
whether the interrupt signal is a level signal or pulse signal. See the following table and interrupt map file attached to this document
for details.
Table 476. Interrupt request
Interrupt request Interrupt Enable / Mask Register Interrupt Flag Register
Common interrupt MAC Interrupt Enable (MAC_Interrupt_Enable) MAC Interrupt
Status (MAC_Interrupt_Status)
MMC Receive Interrupt MMC Receive
Mask (MMC_Rx_Interrupt_Mask) Interrupt (MMC_Rx_Interrupt)
MMC Transmit Interrupt MMC Transmit
Mask (MMC_Tx_Interrupt_Mask) Interrupt (MMC_Tx_Interrupt)
MMC FPE Transmit Interrupt MMC Transmit FPE
Mask (MMC_FPE_Tx_Interrupt_Mask) Fragment Counter Interrupt
Status (MMC_FPE_Tx_Interrupt)
Table continues on the next page...

---

*Page 2531*

Ethernet Media Access Controller (EMAC)
Table 476. Interrupt request (continued)
Interrupt request Interrupt Enable / Mask Register Interrupt Flag Register
MMC FPE Receive Interrupt MMC Receive Packet Assembly
Mask (MMC_FPE_Rx_Interrupt_Mask) Error Counter Interrupt
Status (MMC_FPE_Rx_Interrupt)
MTL Debug Control (MTL_DBG_CTL) MTL Debug Status (MTL_DBG_STS)
MTL EST Interrupt Enable (MTL_EST_Intr_Enable) MTL EST Status (MTL_EST_Status)
MTL Rx Parser Interrupt Control MTL Rx Parser Interrupt Control Status
Status (MTL_RXP_Interrupt_Control_Status) (MTL_RXP_Interrupt_Control_Status)
MTL Queue 0 Interrupt Control MTL Queue 0 Interrupt Control Status
Status (MTL_Q0_Interrupt_Control_Status) (MTL_Q0_Interrupt_Control_Status)
MTL Queue 1 Interrupt Control MTL Queue 1 Interrupt Control Status
Status (MTL_Q1_Interrupt_Control_Status) (MTL_Q1_Interrupt_Control_Status)
DMA Channel 0 Interrupt Enable DMA_CH0_Interrupt_Status
(DMA_CH0_Interrupt_Enable) (transfer complete
interrupts only)
DMA Channel 1 Interrupt Enable DMA_CH1_Interrupt_Status
(DMA_CH1_Interrupt_Enable) (transfer complete
interrupts only)
DMA Interrupt
Status (DMA_Interrupt_Status)
(First level status. Indicates major
block event source: DMA channel, MTL
or MAC)
MTL Interrupt
Status (MTL_Interrupt_Status)
(Status summary for MTL block
interrupts: Rx Parser, EST, FIFO Debug,
Queue 1/Queue 0)
Tx interrupt 0/1 DMA Channel Tx Control (DMA_CH0_Tx_Control) DMA Channel 0
Status (DMA_CH0_Status)
DMA Channel 0 Interrupt
Enable (DMA_CH0_Interrupt_Enable)
DMA Channel 1 Tx Control (DMA_CH1_Tx_Control) DMA Channel 1
Status (DMA_CH1_Status)
DMA Channel 1 Interrupt
Enable (DMA_CH1_Interrupt_Enable)
Rx interrupt 0/1 DMA Channel Rx Control (DMA_CH0_Rx_Control) DMA Channel 0
Status (DMA_CH0_Status)
DMA Channel 0 Interrupt
Enable (DMA_CH0_Interrupt_Enable)
DMA Channel 1 Rx Control (DMA_CH1_Rx_Control) DMA Channel 1
Status (DMA_CH1_Status)
DMA Channel 1 Interrupt
Enable (DMA_CH1_Interrupt_Enable)
Table continues on the next page...

---

*Page 2532*

Ethernet Media Access Controller (EMAC)
Table 476. Interrupt request (continued)
Interrupt request Interrupt Enable / Mask Register Interrupt Flag Register
Safety interrupt MTL ECC Interrupt MTL ECC Interrupt
Enable (MTL_ECC_Interrupt_Enable) Status (MTL_ECC_Interrupt_Status)
MTL Safety Interrupt
Status (MTL_Safety_Interrupt_Status)
MTL DPP Control (MTL_DPP_Control) MAC DPP FSM Interrupt Status
(MAC_DPP_FSM_Interrupt_Status)
MAC FSM Control (MAC_FSM_Control)
These are the interrupt output signals that are synchronous to the CSR clock.
• Sbd_intr_o – common interrupt
• Sbd_perch_tx_intr_o[max_dma_ch] – interrupt per transmit channel
• Sbd_perch_rx_intr_o [max_dma_ch] – interrupt per receive channel
NOTE
max_dma_ch = number of transmit/receive queues, which is 2
The sbd_intr_o common interrupt is a level signal. When it is asserted, a corresponding interrupt event source can be found in
DMA Interrupt Status (DMA_Interrupt_Status) , which is a read-only register that contains the event source fields corresponding to
each DMA channel (transmit and receive queue pair), MAC transaction layer, and MAC blocks. You must then read the following
registers and look for the fields that are 1:
• MAC Interrupt Status (MAC_Interrupt_Status)
• MTL Interrupt Status (MTL_Interrupt_Status)
• DMA Channel 0 Status (DMA_CH0_Status)
The sbd_intr_o interrupt deasserts only when all the enabled interrupt events are clear in their respective status registers, and
correspondingly, all the fields in DMA Interrupt Status (DMA_Interrupt_Status) are 0. DMA Channel 0 Status (DMA_CH0_Status)
captures all the interrupt events of that transmit DMA and receive DMA channel pair.
DMA Channel 0 Interrupt Enable (DMA_CH0_Interrupt_Enable) contains the corresponding enable fields for each of the interrupt
events. These are the two groups of interrupts in the DMA channel:
• Normal— DMA_CH0_Status[NIS] indicates this interrupt, which is used for events that occur during the normal transfer of
packets (TI, RI, TBU).
• Abnormal— DMA_CH0_Status[AIS] indicates this interrupt, which is used for error events.
Interrupts are not queued. If the same interrupt event occurs again before the driver responds to the previous one, no additional
interrupts generate.
The common sbd_intr_o output signal asserts for the transfer complete interrupts only when the corresponding interrupts are
enabled in DMA Channel 0 Interrupt Enable (DMA_CH0_Interrupt_Enable) .
EMAC also supports these per-channel transfer-complete interrupt signals:
• sbd_perch_tx_intr_o[max_dma_ch] (transmit per channel interrupts)
• sbd_perch_rx_intr_o[max_dma_ch] (receive per channel interrupts)
The behavior of the RI/TI/sbd_perch_tx_intr_o[]/sbd_perch_rx_intr_o[] changes depends on the configuration specified in
DMA_Mode[INTM] . This table explains the transfer complete interrupt behavior.

---

*Page 2533*

Ethernet Media Access Controller (EMAC)
Table 477. Transfer complete interrupt behavior
Interrupt Behavior of the TI/RI
Behavior of sbd_perch_tx_intr_o[] and sbd_perch_rx_intr_o[]
mode interrupts and sbd_intr_o
INTM = 1 DMA_CH0_Status[RI] and
DMA_CH0_Status[TI] are
In this mode, these signals indicate the values of the
configured as explained.
corresponding DMA_CH0_Status[RI] and DMA_CH0_Status[TI] fields when
For any RI/TI events:
DMA_CH0_Status[RI] and DMA_CH0_Status[TI] are 1, respectively.
Therefore, they are level signals that you clear by writing 1 to these fields.
• The sbd_intr_o signal
The signals do not assert when DMA_CH0_Status[RI] or DMA_CH0_Status[TI]
does not assert.
is 0.
• DMA_CH0_Status[NIS]
remains 0.
INTM = 2 In this mode, RI/TI are queued and indicate the values of the corresponding DMA_CH0_Status[RI]
DMA_CH0_Status[RI] and DMA_CH0_Status[TI] fields when any of these and DMA_CH0_Status[TI]
fields is 1. RI and TI are level signals that you clear by writing 1 to these become 1 whenever the
fields. However, the fields become 1 again if another TI/RI event is detected transfer complete event is
before DMA_CH0_Status[RI] and DMA_CH0_Status[TI] become 1 for the detected and are reset
previous event. whenever the software driver
changes them to 0 by
writing 1. However, if another
transfer complete event is
detected before the software
changes it to 0 (services it),
EMAC automatically writes 1
to these fields again. The
generation of the sbd_intr_o
signal; however, is not based
on DMA_CH0_Status[RI]
or DMA_CH0_Status[TI] .

#### 72.4 External signals

This section and all its sub-sections are Synopsys Proprietary. Used with permission.

#### 72.4.1 Module signals

This table provides port/signal names and their descriptions. See the IOMUX file attached to this document for details.
Table 478. Module signals
Port name (clock/signal) I/O Description
MII_RMII_TXCLK (Clock) I MII: The external PHY provides this
transmission clock, which operates at a
frequency of 25 MHz in 100 Mbps mode
and at 2.5 MHz in 10 Mbps mode. All
transmission signals that MAC generates
are synchronous to this clock, which is
required for all PHY interfaces.
Table continues on the next page...

---

*Page 2534*

Ethernet Media Access Controller (EMAC)
Table 478. Module signals (continued)
Port name (clock/signal) I/O Description
RMII: The RMII interface uses this 50
MHz clock. If you select RMII mode,
MII_RX_CLK (25 MHz or 2.5 MHz) must
be derived from the RMII reference clock.
MII_RX_CLK (Clock) I The external PHY provides this receive
clock for the MII and RMII interfaces.
The clock operates at a frequency of
25 MHz in 100 Mbps mode and at
2.5 MHz in 10 Mbps mode. All MII
receive signals that MAC receives are
synchronous to MII_RX_CLK. The clock's
input is required for all PHY interfaces.
EMAC_PPS[3:0] (Signals) I/O This group of signals is used as pulse
per second in Output mode and as
media clock generation trigger in Input
mode. The signals trigger input to DUT to
capture presentation time. Based on the
presentation control value of MAC PPS
Control (MAC_PPS_Control) , the signals
can be defined as pulse or level signals.
MII_RMII_TX_EN (Signal) O MAC drives this signal, which performs
multiple functions depending on the
selected PHY interface as described in
this list:
• MII: When high, indicates that
valid data is being transmitted
to the PHY_TXD_O bus.
MII_RMII_TX_EN is synchronous
to MII_RMII_TX_CLK.
• RMII: When high, indicates
that valid data is being
transmitted to the PHY_TXD_O bus.
MII_RMII_TX_EN is synchronous
to MII_RMII_RX_CLK.
MII_RMII_TXD[3:0] (Signals) O This is a group of transmit data signals
driven by MAC. These signals perform
multiple functions depending on the
selected PHY interface as described in
this list:
• MII: Bits[3:0] provide the MII transmit
data nibble. The data is valid
only when the MII_RMII_TX_EN
signal is high. MII_RMII_TXD[3:0] is
synchronous to MII_MII_TX_CLK.
Table continues on the next page...

---

*Page 2535*

Ethernet Media Access Controller (EMAC)
Table 478. Module signals (continued)
Port name (clock/signal) I/O Description
• RMII: Bits[1:0] provide the RMII
transmit data. The data is valid
only when the MII_RMII_TX_EN
signal is high. MII_RMII_TXD[3:0] is
synchronous to MII_RMII_TX_CLK.
MII_CRS (Signal) I This signal is valid only in MII mode.
The PHY drives this signal high when
the transmit or receive medium is not
idle, and drives the signal low when both
these mediums are idle. The signal is not
synchronous to any clock.
MII_COL (Signal) I This signal is valid only in MII mode.
The PHY drives this signal high when a
collision is detected on the medium. The
signal is not synchronous to any clock.
MII_RMII_RX_DV (Signal) I The PHY drives this signal. It performs
multiple functions depending on the
selected PHY interface as described in
this list:
• MII: Indicates that the data on the
MII_RXD bus is valid. It remains high
continuously from the first recovered
byte or nibble of the packet through
the final recovered byte or nibble
of the packet. MII_RMII_RX_DV is
synchronous to MII_RX_CLK.
• RMII: Contains the CRS and data
valid information of the receive
interface. MII_RMII_RX_DV is
synchronous to MII_RMII_TX_CLK.
MII_RMII_RX_ER (Signal) I The PHY drives this signal. It performs
multiple functions depending on the
selected PHY interface as described in
this list:
• MII: Indicates an error or
carrier extension in the received
packet of the MII_RXD[3:0] bus.
MII_RMII_RX_ER is synchronous
to MII_RX_CLK.
• RMII: Is not used.
MII_RMII_RXD[3:0] (Signals) I This is a group of data signals received
from the PHY. These signals perform
multiple functions depending on the
Table continues on the next page...

---

*Page 2536*

Ethernet Media Access Controller (EMAC)
Table 478. Module signals (continued)
Port name (clock/signal) I/O Description
selected PHY interface as described in
this list:
• MII: Bits [3:0] provide the MII receive
data nibble. The data is valid
only when the MII_RMII_RX_DV
signals are high. MII_RMII_RXD[3:0]
is synchronous to MII_RX_CLK.
• RMII: Bits [1:0] provide the RMII
receive data. The data is valid
only when the MII_RMII_RX_DV
signal is high. MII_RMII_RXD[3:0] is
synchronous to MII_RMII_TX_CLK.
MII_RMII_ MDC O MAC provides timing reference
for MII_RMII_MDIO or MII
through this periodic clock. The
application clock generates this
clock through a clock divider that
MAC_MDIO_Address[CR] controls.
MII_RMII_MDIO (Signal) I/O MDIO uses this signal to transfer control
and data information to PHY.

#### 72.5 Using PHY interfaces

This section is Synopsys Proprietary. Used with permission.
This module support the following modes:
• RMII 10/100 Mbps interface
• MII 10/100 Mbps interface
Phy_intf_sel input signal decides which mode is selected. Samples the Phy_intf_sel signal at reset. See External signals for more
information. MAC_Configuration[PS] and MAC_Configuration[FES] selects the mode's speed.
The module supports the IEEE802.3-2015 specification for MII and RMII specification version 1.2 from RMII consortium.
NOTE
RMII reference clock (50 MHz) can be fed to IP from an external source or internally from PLL on SoC.
The module supports the access of the phy registers through SMA . It is a two wire Station management interface (MIM):
1. MDC
2. MDIO
According to IEEE 802.3 specification, maximum operating frequency of MDC is 2.5 Mhz which system clock derives using a
divider. MAC_MDIO_Address[CR] programs to generate different MDC clock frequency.
SMA supports MDIO clause 45 and clause 22 frame structure per the IEEE802.3 specification. Writing 1 to
MAC_MDIO_Address[C45E] enables the clause 45 frame structure.
The MII interface reduces the accuracy of the 1588 timestamp if it is overclocked to run at 200 Mbps. This happens because
the MAC logic uses the MII interface speed,to adjust or compensate for the timestamps taken at MII, as compared to the time
generated in the PTP clock domain as specified in MAC_Configuration[FES] .

---

*Page 2537*

Ethernet Media Access Controller (EMAC)

#### 72.6 VLAN and double VLAN insertion, deletion, replacement and tagging

This section and all its sub-sections are Synopsys proprietary. These are used with permission.
The following sections describe the VLAN and double VLAN features.

#### 72.6.1 Double VLAN processing

In the double VLAN tagging processing, the module supports the processing of two VLAN tags. The two VLAN tags are:
1. Inner VLAN tag (C-VLAN)
2. Outer VLAN tag (S-VLAN)
If there is only one tag in a packet then it is considered as an outer tag.
The module uses MAC_VLAN_Incl and MAC_Inner_VLAN_Incl and MAC_VLAN_Tag registers to support the following functions
in a double VLAN processing feature.
• Insertion, replacement, or deletion of up to two VLAN tags in the transmit path.
• Packet filtering and stripping on the basis of any one of the two VLAN tags in the receive path. Stripping and providing up
to two VLAN tags in the receive path as a part of the receive status.
72.6.1.1 Transmit path
Table 479. Double VLAN processing features in transmit path
Feature Description
Support for C-VLAN and The inner or outer VLAN tag can be of C-VLAN and S-VLAN type. MAC_VLAN_Incl[CSVL] and
S-VLAN tag types MAC_Inner_VLAN_Incl[CSVL] specifies the VLAN type. The module supports the processing of any
sequence of outer and inner VLAN tags.
NOTE
The module does not support the C-VLAN and S-VLAN sequence.
MAC does not examine whether the packet, which the application provides has a valid sequence
of the VLAN tag types or the insertion or replacement operation results in an invalid sequence
of VLAN tag type. Therefore, the application must provide a correct sequence of VLAN tag types
and program the MAC in such a way that it results in correct sequence of VLAN tag types in the
transmitted packet. The application must ensure the following:
• The inner tag should not be S-VLAN when outer C-VLAN Tag insertion is enabled.
• The outer tag should not be C-VLAN when inner S-VLAN Tag insertion is enabled.
• The inner tag should not be S-VLAN when C-VLAN replaces the outer tag.
• The outer tag should not be C-VLAN when S-VLAN replaces the inner tag.
VLAN tag deletion MAC_VLAN_Incl[VLC] or MAC_Inner_VLAN_Incl[VLC] respectively enables the VLAN tag deletion
for an outer or inner tag. When you enable the VLAN deletion, MAC deletes the tag present at the
corresponding position. When a packet has only one tag, it is considered as the outer tag. If you
enable the inner tag deletion and if there is only one tag in the packet, then the MAC does not delete
the tag.
VLAN tag insertion MAC_VLAN_Incl[VLC] or MAC_Inner_VLAN_Incl[VLC] respectively enables the VLAN tag insertion
or replacement or replacement for an outer or inner tag. When you enable the VLAN tag insertion or replacement,
the MAC_VLAN_Incl[VLTI] or
MAC_Inner_VLAN_Incl[VLTI] determines whether the VLAN tag must be fetched from the register
or from the control word.

---

*Page 2538*

Ethernet Media Access Controller (EMAC)
72.6.1.2 Receive path
Table 480. Double VLAN processing in receive path
Feature Description
Outer or inner VLAN tag-
Mac can filter packets through the ERIVLT bit on the basis of the outer or inner VLAN tag.
based filtering
C-VLAN or S-VLAN tag- Mac can filter packets through the ERSVLM bit on the basis of the C-VLAN or S-VLAN
based filtering type.
Outer and inner VLAN Mac can strip the outer and inner VLAN tags from received frame on the basis of the EVLS
tag stripping and EIVLS bits.
16-bit outer and inner VLAN tag Mac can provide the 16-bit outer and inner VLAN tag and type in the Rx status on the basis
and type in Rx status of the EVLRXS and EIVLRXS bits, respectively.
Disabling or skipping checking of Mac can disable or skip checking of an outer VLAN tag type to match C-VLAN or S-VLAN
outer VLAN tag type on the basis of the DOVLTC bit.
72.6.1.3 Source address and VLAN insertion, replacement, or deletion
This module supports the SA , and VLAN insertion, replacement and deletion feature. The SA and VLAN fields are Tx
packet-related control information that interfaces provide as part of the control word. IP supports the feature to insert or replace
the source address on the basis of the information in the MAC address registers, and it also supports the feature to insert, replace,
or delete the VLAN fields (VLAN type and VLAN tag) on the basis of the setting of the MAC_VLAN_Incl[VLTI] . SA insertion or
replacement feature is enabled for all the transmit packets or selective packets. Similarly, VLAN insertion, replacement, or deletion
feature is enabled for all the Tx packets or selective packets.
72.6.1.4 Programming source address insertion or replacement
You can use the SA insertion or replacement feature to instruct MAC to perform the following actions for Tx packets:
• Insert the content of the MAC address registers in the SA field
• Replace the content of the SA field with the content of the MAC address registers
When SA insertion is enabled, the application must ensure that the packets sent to MAC do not have the SA field. The MAC does
not check whether the SA field is present in the transmit packet and it inserts the content of MAC address registers in the SA field.
Similarly, when the SA replacement is enabled, the application must ensure that the SA field is present in the packets sent to
the MAC.
MAC replaces the six bytes following the DA field in the transmit packet with the content of the MAC address registers.
• Configure MAC_Configuration[SARC] to enable the SA insertion or replacement for all Tx packets.
• Enable the SA insertion for selective packet by configuring the SA insertion control field (bits [25:23] of TDES3) in the first
transmit descriptor of the packet. When you write 1 to bit 25 of TDES3, the SA insertion control field indicates insertion or
replacement by MAC Address1 registers. When bit 25 of TDES3 resets, it indicates insertion or replacement by MAC Address
0 registers.
If you do not enable the MAC Address1 registers, then the MAC Address0 registers are used for insertion or replacement
irrespective of the value of the most-significant bit of the SA insertion control field.
72.6.1.5 Programming VLAN insertion, replacement, or deletion
You can use the VLAN insertion, replacement, or deletion feature to instruct MAC to perform the following actions for the
Tx packets:

---

*Page 2539*

Ethernet Media Access Controller (EMAC)
• Delete the VLAN type and VLAN tag fields.
• Insert or replace the VLAN type and VLAN tag fields.
Insertion or replacement is done on the basis of the setting of the MAC_VLAN_Incl[VLTI] as described in the Table 481 :
Table 481. VLAN insertion or replacement based on VLTI bit
Condition Description
VLTI bit=1 MAC inserts or replaces the following:
• VLAN type field (C-VLAN or S-VLAN as indicated by the MAC_VLAN_Incl[CSVL] )
• VLAN tag field depending on the content of the VT field of transmit context descriptor of the packet
VLTI bit resets MAC inserts or replaces the following:
• VLAN type field (C-VLAN or S-VLAN as indicated by the MAC_VLAN_Incl[CSVL] )
• VLAN tag field with the MAC_VLAN_Incl[VLT]
When the VLAN replacement or deletion is enabled, MAC checks whether the VLAN type field (0x8100 or 0x88a8) is present after
the DA and SA fields in the transmit packet. The replace or delete operation does not occur if the VLAN type field is not detected
in two bytes following the DA and SA fields. However, when the VLAN insertion is enabled, MAC does not check the presence of
VLAN type field in the transmit packet and just inserts the VLAN type and VLAN tag fields.
Configuring MAC_VLAN_Incl[VLC] and MAC_VLAN_Incl[VLP] enables the VLAN insertion, replacement, or deletion feature for
all Tx packets.
Configuring the VTIR field of TDES2 normal descriptor enables the VLAN insertion, replacement, or deletion for selective packets.
In addition, the VLP (VLAN Priority control) field must reset in MAC_VLAN_Incl (for outer VLAN) and MAC_Inner_VLAN_Incl
register (in inner VLAN) for MAC to take the control inputs from the host, depending on the configuration.

#### 72.7 Packet Filtering

This section and all its sub-sections are Synopsys proprietary. Used with permission.
MAC receiver contains various packet filtering scheme. Figure 389 shows the filtering sequence in their precedence order of
received packet.

---

*Page 2540*

Ethernet Media Access Controller (EMAC)
Receive packet
Ethernet SA
or DA filter
Fail Pass
Layer 2
VLAN filter
Fail Pass
IP SA or
Layer 3
DA filter
Fail Pass
TCP, UDP SP,
or Layer 4
DP filter
Fail Pass
Discard
Deliver to the host
packet
Figure 389. Packet filtering sequence
If you do not enable any of the layer filters, then that filter is bypassed and the subsequent filter is applied. Discard the packet that
fails any of the filters. However, you can forward the discarded packet to the host on the basis of the register control.

#### 72.7.1 Source address or destination address filtering

The address filtering module of MAC checks the SA and DA fields of each incoming packet. Programming of different types of
address filtering is described in the sections that follows.
72.7.1.1 Unicast destination address filtering
MAC supports up to three MAC addresses for unicast perfect filtering. If perfect filtering is selected ( MAC_Packet_Filter[HUC]
resets), MAC compares all 48 bits of received unicast address with the programmed MAC address for any match. Default
MacAddr0 is always enabled. MAC selects the MacAddr1 to MacAddr2 addresses with an individual enable field. Each byte
of MacAddr1 to MacAddr2 can be masked when you compare them with corresponding received DA byte by writing 1 to the
corresponding Mask byte control field in the register. This enables group address filtering for the DA.
In hash filtering mode (when you write 1 to HUC bit), MAC performs imperfect filtering by using a 64-bit hash table for unicast
addresses . For hash filtering, MAC uses the upper 6 bits CRC of the received destination address to index the content of the hash
table. A value of 00000 selects bit 0 of selected register, and a value of 11111 selects bit 63 of hash table register. If the value of

---

*Page 2541*

Ethernet Media Access Controller (EMAC)
the corresponding bit (indicated by the 6-bit CRC) is 1, the unicast packet is considered to have passed the hash filter, otherwise,
the packet is considered to have failed the hash filter.
72.7.1.2 Multicast destination address filtering
Enable MAC_Packet_Filter[PM] to pass all the multicast packets or disable to do multicast addresses filtering on the basis of
MAC_Packet_Filter[HMC] . Compare the multicast address with the configured MAC destination address registers (1–2), also
supports the group address filtering.
In Hash filtering mode, MAC performs imperfect filtering using a 64-bit hash table. MAC uses the upper 6-bits CRC of received
multicast address to index the content of the hash table. A value of 000000 selects bit 0 of selected register and a value of 111111
selects bit 63 of the hash table register. The multicast packet is considered to have passed the hash filter if the value of the
corresponding bit is equal to 1. Otherwise, the packet is considered to have failed the hash filter.
72.7.1.3 Hash or perfect address filtering
Configure MAC_Packet_Filter[HPF] to enable or disable the destination address filtering either by hash filter or perfect filter. Also
configure MAC_Packet_Filter[HUC] and MAC_Packet_Filter[HMC] to select the hash filtering or perfect filtering for unicast or
multicast packet.
72.7.1.4 Broadcast address filtering
MAC does not filter any broadcast packets by default. You must write 1 to MAC_Packet_Filter[DBF] to reject the
broadcast packets.
72.7.1.5 Unicast source address filtering
MAC by default compares the source address field with the value configured in the source address registers. Configure bit 30
of MAC address register [1-2] to use it for source address instead of destination address comparison. MAC also supports group
filtering with SA. You can filter a group of addresses by masking one or more bytes of the address. MAC drop the packets that
fail the SA filter if you write 1 to MAC_Packet_Filter[SAF] field. Otherwise, the result of the SA filter is given as a status bit in the
receive status word. When you write 1 to the SAF field, the SA filter and DA filter results ends to decide whether you can forward
the packets. This means that the packet is dropped if either filter fails. The packet is forwarded to the application only if the packet
passes both filters in-order.
72.7.1.6 Inverse filtering
For DA and SA filtering, you can invert the filter-match result at the final output by writing 1 to the DAIF and SAIF fields of
MAC_Packet_Filter register. The DAIF field is applicable for both the unicast and multicast DA packets. The unicast or multicast
destination address filter result is inverted in this mode. Similarly, writing 1 to the SAIF field reverses the result of unicast SA filter.
Table 482 and Table 483 summarize the DA and SA filtering on the basis of the type of packets received.
Table 482. Destination address filtering
Packet type PR HPF HUC DAIF HMC PM DBF DA filter operation
Broadcast 1 X X X X X X Pass
0 X X X X X 0 Pass
0 X X X X X 1 Fail
Unicast 1 X X X X X X All packets pass
0 X 0 0 X X X Pass on perfect or group filter match
Table continues on the next page...

---

*Page 2542*

Ethernet Media Access Controller (EMAC)
Table 482. Destination address filtering (continued)
0 X 0 1 X X X Fail on perfect or group filter match
0 0 1 0 X X X Pass on hash filter match
0 0 1 1 X X X Fail on hash filter match
0 1 1 0 X X X Pass on hash or perfect or group filter match
0 1 1 1 X X X Fail on hash or perfect or group filter match
Multicast 1 X X X X X X Pass all packets
X X X X X 1 X Pass all packets
0 X X 0 0 0 X Pass on perfect or group filter match and drop pause
packets if PCF = 0x
0 0 X 0 1 0 X Pass on hash filter match and drop pause packets if
PCF = 0x
0 1 X 0 1 0 X Pass on hash or perfect or group filter match and drop
pause packets if PCF = 0x
0 X X 1 0 0 X Fail on perfect or group filter match and drop pause
packets if PCF = 0x
0 0 X 1 1 0 X Fail on hash filter match and drop pause packets if
PCF = 0x
0 1 X 1 1 0 X Fail on hash or perfect/group filter match and drop
pause packets if PCF = 0x
Table 483. Source address filtering
Packet
type PR SAIF SAF SA filter operation
Unicast 1 X X Pass all packets.
0 0 0 Pass status on perfect or group filter match but do not drop packets that fail
0 1 0 Fail status on perfect or group filter match but do not drop packet
0 0 1 Pass status on perfect or group filter match and drop packets that fail
0 1 1 Fail status on perfect or group filter match and drop packets that fail
NOTE
When you write 1 to the MAC_Packet_Filter[RA] field, all packets are forwarded to the system along with the correct
result of the address filtering in the Rx Status.

---

*Page 2543*

Ethernet Media Access Controller (EMAC)

#### 72.7.2 VLAN filtering

VLAN tag filter can be done either by perfect match or hash table. Depending upon the configuration, MAC can compare either
lower 12 bits or all 16 bits of received VLAN tag for perfect match. MAC forward the VLAN tagged packet with match status when
it drop all the packets which do not match.
MAC uses 16-bit VLAN hash table for group address filtering on the basis of the VLAN tag. If MAC enables the hash filtering then
the most significant 4-bit of CRC-32 of VLAN tag are used to index the content of the MAC_VLAN_Hash_Table . A value of 1 in
the VLAN hash table register, corresponding to the index indicates that the VLAN tag of the packet has matched and the packet
should be forwarded. A value of 0 indicates that VLAN-tagged packet should be dropped.
MAC supports the inverse matching for VLAN packets. In the Inverse matching mode, the packet must be dropped when the
VLAN tag of a packet matches the perfect or hash filter. If MAC enables the VLAN perfect and VLAN hash match, then a packet
is considered as matched if either the VLAN hash or the VLAN perfect filter matches. When inverse match is set, MAC forwards
a packet only when both the perfect and hash filters indicate mismatch.
Table 484 specifies the different possibilities for VLAN matching and the final VLAN match status. When you write 1 to
MAC_Packet_Filter[RA] all packets are received and it indicates the VLAN match status in the VF field of RDES2 normal descriptor
(write-back format). When the RA field is not set and when you write 1 to MAC_Packet_Filter[VTFE] the packet drops if the final
VLAN match status fails
When VLAN VID programs to 0 in the VL field of MAC_VLAN_Tag register, consider that all the VLAN-tagged packets are perfectly
matched but the status of the VLAN hash match depends on the VTHM and VTIM fields in MAC_VLAN_Tag register.
Table 484. VLAN match status
VLAN perfect filter VLAN hash filter
VID match result VTHM bit match result VTIM bit Final VLAN match status
VID = 0 Pass 0 X X Pass
Pass 1 X 0 Pass
Pass 1 Fail 1 Pass
Pass 1 Pass 1 Fail
VID1= 0 Pass X X 0 Pass
Fail 0 X 0 Fail
Fail 1 Fail 0 Fail
Fail 1 Pass 0 Pass
Fail 0 X 1 Pass
Pass X X 1 Fail
Fail 1 Pass 1 Fail
Fail 1 Fail 1 Pass
In Table 484 , X represents any value.

---

*Page 2544*

Ethernet Media Access Controller (EMAC)

#### 72.7.3 VLAN filter fail packets queue

When you enable VLAN filtering, route the VLAN filter fail packets to a programmable queue (VFFQ) when the value of
MAC_Packet_Filter[RA] = 1 or MAC_Packet_Filter[VTFE] = 0 and write 1 to MAC_RxQ_Ctrl4[VFFQE] for the queue.
Route the packets that passes the VLAN filtering to Rx queues on the basis of the VLAN tag priority field by configuring the PSRQ
field in the corresponding MAC_RxQ_Ctrl2 and MAC_RxQ_Ctrl3 registers. Discard the packets that fail the VLAN filter if RA=0 or
VTFE=1. However, when RA=1 or VTFE=0, forward the VLAN filter fail packets to the application. In such case, when you write
1 to the VLAN filter fail queue enable (VFFQE) field, the VLAN filter fail packets forward to the Rx queue number programmed in
the VFFQ field. If the value of VFFQE=0, the VLAN priority mapping determines the Rx queue number per the PSRQ fields.
Table 485 shows the Rx queue routing table for unicast tagged packets, with DA or SA filter enabled.
Table 485. Rx queue routing table for unicast tagged packets
RA VFTE SA or DA filter result VLAN filter result VFFQE Queue routing
X X Pass Pass X PSRQ
0 0 Pass Fail 0 PSRQ
Pass
0 0 Fail 1 VFFQ
0 X Fail X X Dropped
0 1 Pass Fail X Dropped
1 X Fail X 0 UFFQ*/PSRQ
Fail
1 X X 1 UFFQ*/VFFQ
Fail
1 X Pass 0 PSRQ
1 X Pass Fail 1 VFFQ
X : Don't care condition * : When UFFQE is enabled else PSRQ.

#### 72.7.4 Extended receive VLAN filtering and routing

When the extended Rx VLAN filtering and routing is enabled then both the perfect filtering and hash filtering can be enabled. The
overall VLAN filter result is based on the perfect filter result and hash filter result (if enabled). Filter result is passed to application
as part of the status bit. Extended routing will take place only if VLAN filter has passed. Routing is only based on perfect filter result.
Each perfect filter has a DMA channel enable and DMA channel number filed which must be programmed for routing.
See the extended VLAN based DMA selection in Dynamic (per packet) mapping for more information about routing.
72.7.4.1 Comparison mode
Application has these comparison option for each VLAN tag filter:
• Programs MAC to compare the inner or outer VLAN tag either with 12 bits or 16 bits programmed VID.
• Selects whether the VID comparison is for SVLAN or CVLAN type frames, if type check is enabled for a filter.
72.7.4.2 Filtering
Perfect filtering is done on the basis of the MAC_VLAN_Tag_Filter registers. MAC compares the relevant VLAN tag ID and gives
a result for each VLAN tag filter.
The results for each VLAN tag filter are:

---

*Page 2545*

Ethernet Media Access Controller (EMAC)
• Pass- If any one of the VLAN tag filters gives a match.
• Fail- If the frame mismatches all the filters.
This behavior is applicable only when the inverse filtering is not enabled in MAC_VLAN_Tag_Ctrl .
If inverse filtering is enabled and the frame mis-matches all the relevant filters then it is considered to have passed the VLAN filter.
If the frame matches any one of the relevant filters then it is considered to have failed. The frame is bypassed to the application
if none of the enabled filters can perform a comparison or if none of the filters are enabled.
The overall filter result and the programming on MAC_Packet_Filter[VTFE] and MAC_Packet_Filter[RA] determines if the frame
will drop or it is forwarded to the application. If the value of RA = 1 or VTFE = 0, then the frame is always forwarded whether the
filter result is a pass or fail, . If the value of RA = 0 and VTFE = 1, only then, if the VLAN tag filter result is a pass the MAC forwards
the frame. If the frame is forwarded to the application, then the relevant filter result is indicated through the status bits.
72.7.4.3 Filter status
The extended receive VLAN filtering and routing feature provides two status fields to show the comparison result of the VLAN tags.
By default, MAC indicates the VLAN filter status through one bit in the status VF field in RDES2. When you enable the extended
RX VLAN filtering and routing, two status fields will show the comparison result of the VLAN tags. The outer VLAN tag filter pass
and inner VLAN tag filter pass bits are defined in the following positions. The status indicated through these fields depends on the
programming as described below.
In RDES2:
• Bit 15 – Outer VLAN tag filter status
• Bit 14 – Inner VLAN tag filter status
In ARI status: MAC filter status:
• Bit 15 – Outer VLAN tag filter status
• Bit 14 – Inner VLAN tag filter status
In MRI status:
• Bit 47 – Outer VLAN tag filter status
• Bit 46 – Inner VLAN tag filter status
Outer VLAN tag filter status (OTS)
• In perfect filtering, without enabling inverse filtering, if you write 1 to this field, it indicates that the frame’s outer VLAN tag has
matched one of the VLAN tag filters.
• If this field resets, it indicates that the frame’s outer VLAN tag has either failed the relevant outer VLAN tag filters or
bypassed them.
• This field resets if none of the filters are enabled for outer VLAN tag comparison.
• If inverse filtering is enabled and if you write 1 to this field, then the frame’s VLAN tag has passed all the relevant VLAN tag
filters. If it is resets, then it has failed at least one filter or bypassed all the filters programmed for outer VLAN tag comparison.
• This bit is valid for both single and double VLAN tagged frames.
Inner VLAN tag filter status (ITS)
• In perfect Filtering, without enabling inverse filtering, if you write 1 to this field, it indicates that the frame’s inner VLAN tag has
matched one of the VLAN tag filters.
• If this field resets, it indicates that the frame’s inner VLAN tag has either failed the relevant inner VLAN tag filters or
bypassed them.
• This field resets if none of the filters are enabled for inner VLAN tag comparison.
• If Inverse Filtering is enabled and if you write 1 to this field, then the frame’s VLAN tag has passed all the relevant VLAN tag
filters. If it resets, then it has failed at least one filter or bypassed all the filters programmed for inner VLAN tag comparison.

---

*Page 2546*

Ethernet Media Access Controller (EMAC)
• This bit is valid for only double VLAN tagged frames, when double VLAN processing is enabled.
The application must observe the status fields and the program to determine if the frame has passed or failed the VLAN filter.
Table 486 and Table 487 describes the possible filter combinations and the corresponding filter results and they also explain the
scenarios when the double VLAN processing and the hash VLAN filter are enabled in the design.
Legend for the table OTS and ITS Bit Values with at least 1 perfect filter enabled
• VTIM: VLAN tag inverse match enable – bit 17 in VLAN_Tag_Ctrl register.
• HFO: Hash filter enabled for outer VLAN tag comparison - bit 25 and bit 27 in VLAN_Tag_Ctrl register.
• HFI: Hash filter enabled for inner VLAN tag comparison – bit 25 and bit 27 in VLAN_Tag_Ctrl register.
• PFO – Perfect filter comparison enabled for outer VLAN tag - Any of the MAC_VLAN_Tag_Filter registers is enabled (write 1
to bit 16) and programmed for outer VLAN tag comparison (write 0 to bit 20).
• PFI – Perfect filter comparison enabled for Inner VLAN Tag - Any of the MAC_VLAN_Tag_Filter registers is enabled (write 1
to bit 16) and programmed for inner VLAN tag comparison (write 1 to bit 20).
• OTS – Outer VLAN tag filter status
• ITS – Inner VLAN tag filter status
Table 486 shows the possible values of status bits (OTS and ITS) when at least one perfect filter is enabled.
Table 486. OTS and ITS bit values with at least 1 perfect filter enabled
VTIM HFO HFI PFO PFI OTS ITS
0 0 0 0 1 0 1/0
0 0 0 1 0 1/0 0
0 0 0 1 1 1/0 1/0
0 1 0 1 1 1/0 1/0
0 1 0 1 0 1/0 0
0 1 0 0 1 1/0 1/0
0 0 1 1 1 1/0 1/0
0 0 1 1 0 1/0 1/0
0 0 1 0 1 0 1/0
1 0 0 0 1 0 1/0
1 0 0 1 0 1/0 0
1 0 0 1 1 1/0 1/0
1 1 0 1 1 1/0 1/0
1 1 0 1 0 1/0 0
1 1 0 0 1 1/0 1/0
Table continues on the next page...

---

*Page 2547*

Ethernet Media Access Controller (EMAC)
Table 486. OTS and ITS bit values with at least 1 perfect filter enabled (continued)
VTIM HFO HFI PFO PFI OTS ITS
1 0 1 1 1 1/0 1/0
1 0 1 1 0 1/0 1/0
1 0 1 0 1 0 1/0
Table 487 shows the possible values of status bits (OTS and ITS) when no perfect filters are enabled except the VLAN hash filter
is enabled.
Table 487. OTS and ITS bit values with only VLAN hash filter enabled
VTIM HFO HFI OTS ITS
0 0 0 0 0
0 1 0 1/0 0
0 0 1 1/0 1/0
1 0 0 1/0 0
1 1 0 1/0 0
1 0 1 1/0 1/0
With no perfect filters enabled, any VLAN packet is considered to have bypassed the perfect filter. If Hash Filter is enabled for one
of the Tags, then the respective Status bit depends on the Filter's result. The Status bits are set to 0 if VLAN Hash Filter is also
not enabled.
If the value of ITS/OTS is shown as 1/0; then it indicates that the final result is dependent on the enabled relevant filter's result.
Example 1: The second row of OTS and ITS bit values with only VLAN hash filter enabled indicates that at least one Perfect Filter is
enabled for Outer VLAN tag comparison and none of the filters are enabled for Inner VLAN tag comparison. Inverse VLAN Filtering
is not enabled. The bit OTS is given as 1/0. If the received frame passes at least one of the enabled Outer VLAN Tag filters then
the bit is set to 1. If the frame doesn't pass any of the enabled Outer VLAN Tag filters, then the bit is set to 0.
Example 2: The last row of table "OTS and ITS bit values with only VLAN hash filter enabled" indicates that the inverse filtering
is enabled, hash filter and at least one perfect filter is enabled for Inner VLAN Tag comparison, then if the received frame's Inner
VLAN tag mismatches with both the Hash Filter and all the enabled Perfect filters, then the frame will have the ITS bit set to 1 else
it is set to 0. OTS will be set to 0 as no comparison is performed.
72.7.4.4 Stripping
Each VLAN tags has individual control over stripping. The programming options of always strip, never strip, strip on pass and strip
on fail are available. Inner or outer VLAN tag stripping is based on the pass or fail results of the individual tag. If all the relevant
filters bypasses a tag, stripping is not applicable for the tag.
• If strip on pass is enabled for the outer VLAN tag, then the stripping occurs only if the outer VLAN tag has passed the relevant
filters. The outer VLAN tag filter result field will be 1.
• If strip on fail is enabled for the outer VLAN tag, then the stripping occurs only if the outer VLAN tag has failed relevant filters.
The outer VLAN tag filter result field will reset.
• If the outer VLAN tag of the received frame is bypassed by the entire filter (no comparison has been made), then the tag is
not stripped, though the status bit is 0.

---

*Page 2548*

Ethernet Media Access Controller (EMAC)
• As multiple filters are enabled, it is possible that the received VLAN frame could have matched any one or more of the filters.
The VLAN Tag's value is not always deterministic from the filter status bits.
If an application strips the VLAN tag on the basis of the filter result, it might lose the VID. You can use it, if stripping is enabled for
any of the tags, place the tag in the status. For this the software or application will enable the respective VLAN tag in status bit -
24 or 31 in the MAC VLAN tag control register.
See section Programming guidelines for extended VLAN filtering and routing on receive for more information.

#### 72.7.5 Layer 3 and layer 4 filtering

IP supports the layer 3 based packet filtering which is an IP source or destination address filtering in the IPv4 or IPv6 packets, and
layer 4 based packet filtering which is a source or destination port number filtering in TCP or UDP. When you enable layer 3 and
layer 4 packet filtering then packets are filtered in the following manner.
• Matched packets: MAC forward the packets that match all enabled fields to the application along with the status. It gives the
matched field status only if MAC_Configuration[IPC] = 1 and if one of the following conditions is true:
— All enabled layer 3 and layer 4 fields match
— At least one enabled field matches and other fields are bypassed or disabled
When you enable multiple layer 3 and layer 4 filters, any filter match is considered as a match. If more than one filter matches,
MAC provides the status of the lowest filter where filter 0 is the lowest filter and filter 3 is the highest filter.
• Unmatched packets: MAC drop the packets that do not match any enabled fields. You can use the inverse match feature to
block or drop a packet with specific TCP or UDP over IP fields and forward all other packets.
• Non-TCP or UDP IP packets: By default, all the non-TCP or UDP IP packets bypasses the layer 3 and layer 4 filters. You can
optionally program MAC to drop all the non-TCP or UDP packets over IP packets.
• IP has a control register MAC_L3_L4_Control to control the layer L3 and L4 packet filtering along with address registers to
program the layer L3 and L4 fields to be matched.

#### 72.7.6 Flexible receive parser

When you enable this function, all incoming packets are parsed per the programmable instruction stored in memory. It perform
these functions:
• Packet filter (Accept or reject)
• DMA channel selection
Flexible receive parser block operates over the first 128 bytes data of receive packet on the basis of 64 bytes of instruction on each
packet. Figure 390 shows the functional block diagram of flexible receive parser.

---

*Page 2549*

Ethernet Media Access Controller (EMAC)
MAC receive MAC receive packet data
(first 128-byte packet data)
MTL receive
Receive parser Packet drop or accept
FIFO
(application clock domain)
controller
DMA channel number
Read
Memory indirect
access control
Slave Read/write
interface
Software parser
instruction table
(external memory)
Figure 390. Functional block diagram of flexible receive parser
72.7.6.1 Instruction table format of the flexible receive parser
You must build the 96-bit wide instruction before enabling the receive parser feature and program each instruction as an entry in
the instruction memory.
Table 488 shows the format of each entry in the instruction table .
Table 488. Rx parser instruction entry format
Field Field Name Description
31:0 MATCH_DATA This is a 4-byte data. You can use it to compare with incoming packet data which starts at
the frame offset as defined in [79:72] of the entry. The comparison is done only on those bits
whose corresponding mask bits are 1 (MATCH_EN bits of [63:32]). See the notes (below this
table) on the byte order relative to Ethernet arrival data and endianness which the QoS IP
supports on the slave interface. The programming of this instruction table must adhere to
the same.
62:32 MATCH_EN When the MATCH_EN = 1, you can use the corresponding packet data bit for comparing.
Otherwise, corresponding data bit is don’t care.
64 AF Accept frame
65 RF Reject frame
66 IM Inverse match
67 NC Next instruction control
71:68 Reserved -
Table continues on the next page...

---

*Page 2550*

Ethernet Media Access Controller (EMAC)
Table 488. Rx parser instruction entry format (continued)
Field Field Name Description
79:72 Frame offset [77:72] indicates the frame offset in terms of 4 bytes. (Here [79:78] always 0) Compare the
frame offset in the packet data for Match. This is in terms of 4 bytes. The value of actual frame
offset in bytes are 0 01 42 8… ..63 252
The max value programmed in this instruction table is 128 B. The 'Actual frame offset' must
not cross this limit.
87:80 OK index If (NIC==0) the memory index you can use next when ENTRY_MATCH==1 and neither AF
or RF = 1. If (NIC==1) the memory index you can use next when ENTRY_MATCH==0
95:88 DMA Ch No Indicates the DMA channel number (1-bit for each). You can use this when ENTRY_MATCH
==1 and AF = 1 (Accept frame). The following bits give the encoding: bit[0]- DMA channel
number o, bit[1]- DMA channel number , 1bit[2]- DMA channel number 2… bit[7]- DMA
channel number 7.
NOTE
You can encode the DMA channel number using bit wise as QoS IP support
multicasting or broadcast across DMA channels. See section 3.3.12 for
more information and proper usage.
128:96 Reserved 0 (This field is only for software view and reserved for future enhancements. The memory
width remains as 96-bits).
The parser begins parsing from the 0th entry, for each received packet. The subsequent parsing entry location (next entry or
OK_INDEX[]) depends on the current entry parser result. Flexible receive parser flow chart shows the detailed flow.

---

*Page 2551*

Ethernet Media Access Controller (EMAC)
Start
Entry address = 0
Number of parsed entries = 0
Packet routed to Rx
Entry address >= Yes
(NVE[7:0]+1) DMA CH0 and generate
parser interrupt
Entry address = (NIC==0) ?
Entry address + 1:
OK_INDEX
Number of parsed Yes
Number of parsed entries = entries >= (NPE[7:0]+1)
Number of parsed entries + 1
Read the memory and get
the corresponding packet field
Actual frame offset >
EOF_OFFSET
No
No Pattern match (IM=0) or
Invert match (IM=1)
Yes
Route the frame into
Yes DMA channel as
AF==1
indicated in
DMA_CH_NO[2:0]
No
No Yes Reject frame and
RF==1
generate an interrupt
No
Entry address = if(NIC==0) ? OK_INDEX[7:0] : Entry address + 1
Num of parsed entries = Num of parsed entries + 1
MAC drop
End
Figure 391. Flexible receive parser flow chart
The EOF_OFFSET shown in the figure is the least among the following:
• Actual packet size
• 128 Byte when frame pre-emption feature is not enabled or configured
• 128 Byte when frame pre-emption feature is enabled and packet is pre-emptive
• 64 Byte when frame pre-emption feature is enabled and packet is express packet
72.7.6.2 Number of valid entries (NVE)
NVE indicates the number of valid entries in the instruction table. The default value is 64. You can change this value by writing
into MTL_RXP_Control_Status[NVE] .
When parsing, an error is flagged in MTL_RXP_Interrupt_Control_Status[NVEOVIS] , if the entry address is greater than
MTL_RXP_Control_Status[NVE] + 1. IP generates an interrupt status ( MTL_Interrupt_Status[MTLPIS] ).

---

*Page 2552*

Ethernet Media Access Controller (EMAC)
72.7.6.3 Number of parsable entries (NPE)
NPE indicates the number of entries that you can parse on an incoming packet. The default value is 64. Write into
MTL_RXP_Control_Status[NPE] to change this value.
When parsing, an error is flagged in MTL_RXP_Interrupt_Control_Status[NPEOVIS] , if the number of parsed of parsed entries is
more than MTL_RXP_Control_Status[NPE] + 1. IP generates an interrupt status ( MTL_Interrupt_Status[MTLPIS] ).
NVE[] indicates the number of valid entries in the receive parser memory which you have built and NPE[] indicates the worstcase
parsable entries considering the various possible paths that the parser can take. (NVE[] >= NPE[]).
72.7.6.4 Frame offset
It indicates the frame offset, in terms of 4 bytes which you use when comparing against the current table entry.
In an entry, an error is flagged in MTL_RXP_Interrupt_Control_Status[FOOVIS] in any of the following cases:
When frame offset is more than:
• Packet size
• 128B if pre-emption is disabled
• 128B if pre-emption is enabled and packet is pre-empitble
• 64B if pre-emption is enabled and packet is an express packet
IP generates an interrupt status ( MTL_Interrupt_Status[MTLPIS] ).
NOTE
If the frame ends after the (frame_offset *4) bytes but before ((frame_offset *4)+4) bytes, the receive parser
declares a frame offset error if you enable comparison (via MASK bits) for the non-received bytes; from
(frame_offset *4) to ((frame_offset *4)+4).
72.7.6.5 Frame reject
You can drop the frame in cases, where an entry matches (considering the inverse match, IM bit in the instruction table) and frame
accept bit is not 1, and frame reject bit (RF) = 1.
When a frame drops because RF = 1, IP generates an interrupt status ( MTL_Interrupt_Status[MTLPIS] ).
72.7.6.6 Out of order processing
The Rx parser flow can be out of order. The decision tree is not based on the order in which packet arrives. This implies that the
next entry and next frame offset (OK_INDEX[] and FRAME_OFFSET[]) can be less than the current entry address and current
frame offset.
72.7.6.7 DMA channel selection
When you enables the receive parser ( MTL_Operation_Mode[FRPE] = 1) it overrides the MAC DMA selection criteria.
When you disables the receive parser ( MTL_Operation_Mode[FRPE] = 0) the MAC/MTL determines the DMA channel number.
72.7.6.8 Receive queue selection
The existing MAC functionality determines the receive queue, irrespective of whether the receive parser is enabled or disabled.
This is because the receive parser is mainly designed to select the DMA channel and the MAC decides the receive queuing to
enable the proper functioning of the pause or PFC feature.

---

*Page 2553*

Ethernet Media Access Controller (EMAC)
NOTE
When there are multiple queues and multiple DMA Channels are enabled, a particular DMA channel might receive
out of order packets because queue selection is based on MAC criteria (VLAN Priority and other criteria) and Rx
DMA channel selection is based on receive parser.
72.7.6.9 MAC packet filtering/drop/error handling
Packet filtering is done on the basis of the received packet fields. You must disable the packet filtering features inside the MAC
when the receive parser is enabled. Follow these steps to disable the packet filtering features inside the MAC :
1. Write 1 to the Promiscuous mode ( MAC_Packet_Filter[PR] ).
2. Write 1 to all other fields in MAC_Packet_Filter to its default values.
When MAC decides to drop the packet due to the receive MAC dependent error such as:
• GMII error
• Receive watchdog error
• CRC error
• Giant frame error
the packet drops irrespective of the receive parser decision.
72.7.6.10 Pad strip or CRC strip handling
Software controls the pad strip and CRC strip and it is applicable to all the received packets. Also,software must build the receive
parser instructions accordingly, when you enable these features.
NOTE
Enable pad and CRC stripping in real use case.
72.7.6.11 VLAN strip handling
MAC supports stripping the outer VLAN as well as inner VLAN. When you program MAC to strip the VLAN, the receive parser
considers those VLAN tags to be part of the incoming packet.
72.7.6.12 Multicast and broadcast support
IP supports multicast and broadcast packet and the packet route to the highest queue number.
When you enables the receive parser, the DMA channel numbers (DMA CH NO, Bits [95:88]) in the instruction table, decide the
packet routing.
When you disables the receive parser, DCS (DMA Channel Select) field of the MAC_AddressX_High register determines the
packet routing. See the Broadcast/multicast packet duplication .
NOTE
You can select multiple DMA channels for the packet routing because the DCS/DMA CH NO field is per the DMA
channel control.
72.7.6.13 Pre-emption support
IP supports the frame preemption feature when the flexible receive parser feature is enabled. It has the following limitations:
• Number of bytes would be 64, so that the receive parser can operate on an express traffic
• Number of bytes would be 128, so that receive parser can operate on a pre-emptive traffic.

---

*Page 2554*

Ethernet Media Access Controller (EMAC)
72.7.6.14 Software access to the flexible receive parser memory
Software can read and write into the receive parser memory. It uses the following registers via indirect addressing.
• MTL_RXP_Indirect_Acc_Control_Status
• MTL_RXP_Indirect_Acc_Data
For more details, see EMAC register descriptions .
72.7.6.15 Statistical counters
IP supports these statistics counters for flexible receive parser.
1. MTL_RXP_Drop_Cnt
2. DMA_RXP_Error_Cnt
DMA_CH(#i)_Rxp_Accept_cnt (i=0; i<2)
For more details, see EMAC register descriptions .
72.7.6.16 Changing the instruction table by software
Software can update the instruction table in the memory in the following ways:
• Disables the MAC receiver and receive parser by writing 0 to MAC_Configuration[RE] and MTL_Operation_Mode[FRPE] . It
waits for receive parser to become inactive (RXPIA, MTL_RXP_Control_Status register bit[31]).
• (Optional) Programs in the entry address 0 to unconditionally (all MATCH_EN bit 0) skip to OK_INDEX[] (to certain location
in the memory), where you can program it to reject or accept all the packets.
72.7.6.17 Receive cut-through functionality
In the Cut-Through mode, the receive controller transfer the received packets to DMA just after it receives the cut-through
threshold amount of packet data (RTC field in the MTL_RxQx_Operation_Mode register ).
However due to Rx parser's result worst case arrival time can be more than the programmed cut-through threshold. So, the receive
controller delays the packet transfer to DMA accordingly, and the cut-through functionality is re-defined as follows:
The receive controller transfers packet to DMA after at least RTC number of bytes have been received and receive parser results
are available.
72.7.6.18 Receive packet drop indication
In most cases, the packet drops internally in the receive queue. However, back-to-back packets can arrive for the same queue and
the first packet's receive parser result is not available when the second packet arrives. In such cases, the packet cannot be flushed
internally and forwarded to DMA or the application interface and indicates the status accordingly. See RDES2, bit 16(RXPD) in
Receive normal descriptor (write-back format) for more information.
72.7.6.19 Runt packet handling
The receive parser can performs the operation with 64B minimum size packet. If the runt packet is received (MAC must indicate
it), the receive parser assumes that it is dropped in the MAC, this is because, the back-to-back runt packets (< 64B) cannot be
handled in the receive parser.
72.7.6.20 Uncorrectable ECC error handling
The packet does not drops when the parser detects an uncorrectable ECC error while parsing the parser memory. The packet is
sent to the application with an error indication. The RXPI (RX parser incomplete) sets in the packet status along with error summary
(ES) bit.
See the Receive normal descriptor (write-back format) for more information.

---

*Page 2555*

Ethernet Media Access Controller (EMAC)

#### 72.8 IEEE 1588 timestamp support

This section and all its sub-sections are Synopsys proprietary. Used with permission.
IP supports the IEEE 1588 precision time protocol (PTP). It is also able to do precise time stamping and captures incoming and
outgoing frame because of the PTP protocol implementation in the IP. It supports all the clock types defined in IEEE 1588-2008.
The typical frequency of PTP timestamp clock is 125 MHz.
IP supports these features:
• Provides an option to take snapshot of all packets or only PTP type packets
• Provides an option to take snapshot of only event messages
• Identifies the PTP message type, version, and PTP payload in packets sent directly over Ethernet and sends the status
• Provides an option to measure sub-second time in digital or binary format

#### 72.8.1 Delay request-response mechanism

The system or network is classified into the master and slave nodes for distributing the timing and clock information. Figure 392
shows the process that PTP uses for synchronizing a slave node to a master node by exchanging PTP messages.
Master clock time Slave clock time
t 1 Sync message
Data at
slave clock
t 2 m t 2 t 2
Follow_Up message
containing value of t 1
t 1 , t 2
t 3 m t 3 t 1 , t 2 , t 3
Delay_Req message
t 4
Delay_Resp message
containing value of t 4
t 1 , t 2 , t 3 , t 4
time
Figure 392. Networked time synchronization
As shown in the figure, PTP uses this process:
1. The master broadcast the PTP sync messages to all its nodes. The sync message contains the reference time
information of the master. This message leaves the system of the master at t1. You must capture this time for Ethernet
ports at GMII or MII.
2. The slave receives the sync message and it also captures the exact time, t2, using its timing reference.
3. The master sends a Follow_Up message to the slave, which contains t1 information for later use.
4. The slave sends a Delay_Req message to the master and note the exact time, t3, at which this packet leaves the GMII
or MII interface.
5. The master receives the message, capturing the exact time t4, at which the message enters its system.
6. The master sends the t4 information to the slave in the Delay_Resp message.
7. The slave use the four values of t1, t2, t3, and t4 to synchronize its local timing reference to the master's timing
reference.

---

*Page 2556*

Ethernet Media Access Controller (EMAC)
Most of the PTP implementation is done in the software above the Ethernet layer. However, the hardware captures the exact time
when specific PTP packets enter into or leave the Ethernet port at the MII interface. This timing information must be captured and
returned to the software for proper implementation of PTP with high accuracy.

#### 72.8.2 Peer-to-peer PTP transparent clock (P2P TC) message support

The IEEE 1588-2008 supports the peer-to-peer PTP (Pdelay) message in addition to the sync, delay request, follow-up, and
delay response messages. Figure 393 shows the method to calculate the propagation delay in clocks supporting the peer-to-peer
path correction.
P2P TC A P2P TC B
delay delay
requester responder
time time
Timestamps
that the delay
requester knows
t 1
Pdelay_Req t 1
t AB
t 2
t 3
Pdelay_Resp
t BA
t 4
t 1 , t 4
t 1 , t 2 , t 3 , t 4
Pdelay_Resp_Follow_Up:
t 2 , t 3
Figure 393. Propagation delay calculation in clocks supporting peer-to-peer path correction
As shown in the above figure, the propagation delay is calculated in the following way:
1. Port 1 issues a Pdelay_Req message and generates a timestamp (t1) for the Pdelay_Req message.
2. Port 2 receives a Pdelay_Req message and generates a timestamp (t2) for this message.
3. Port 2 returns a Pdelay_Resp message and generates a timestamp (t3) for this message. Port 2 returns the
Pdelay_Resp message as quickly as possible after the receipt of the Pdelay_Req message, to minimize the errors
because of any frequency offset between the two ports. Port 2 returns any one of the following:
• Difference between the timestamps t2 and t3 in the Pdelay_Resp message
• Difference between the timestamps t2 and t3 in the Pdelay_Resp_Follow_Up message
• Timestamps t2 and t3 in the Pdelay_Resp and Pdelay_Resp_Follow_Up messages, respectively
4. Port 1 generates a timestamp (t4) after receiving the Pdelay_Resp message.
5. Port 1 uses all the four timestamps t1, t2, t3, and t4 to compute the mean link delay.

#### 72.8.3 Timestamp correction

According to the IEEE 1588 specification, you must capture a timestamp when the PTP message timestamp point (leading edge
of the first bit of the octet immediately following the start frame delimiter octet) crosses the boundary between the node and the
network. As MAC takes the timestamp at an internal point far from the actual boundary of the node and network, you must correct
or update the captured timestamp for the ingress or egress path latency (including the delay in the PHY layers). Further correction

---

*Page 2557*

Ethernet Media Access Controller (EMAC)
is done for the inaccuracies or errors introduced because the clock (MII Tx, Rx clock) is different at the capture point as compared
to the PTP clock (CLK_PTP_REF_I) that generates the time. The resultant CDC (Clock domain crossing) circuits add an error
depending on the clock period of the MII and PTP clocks.
72.8.3.1 Ingress correction
The timestamp captured at the internal snapshot point In the receive side is delayed (later in time) as compared to the
time at which the packet's SFD bit is received at the port's boundary. Therefore, the ingress latency and the errors in
CDC sampling reduces the captured timestamp. You must determine or calculate the correction value and write into the
MAC_Timestamp_Ingress_Corr_* registers.
The correction value consists of these three components:
1. External latency in the PHY layer between boundary point and the input of the core.
If the PHY is compliant with the IEEE 802.3 Clause 45 MMD registers, it has a register which indicates the maximum
and minimum ingress latency. You can read these registers and determines the average ingress latency in the PHY.
Alternatively (if the PHY does not support these registers), you must determine the ingress latency from its datasheet or
timing characteristics.
2. Internal latency from the core's input to the internal capture point.
You can read the internal ingress latency from MAC_Timestamp_Ingress_Latency . This is a read-only register and provides
the latency in scaled nanoseconds format as defined in IEEE 1588 Clause 5.3.2. The latency differs on the basis of the
active PHY interface (MII, RMII, so on) and the operating speed. Therefore, you must read this register after any speed
change in MAC, to determine the current internal latency.
3. CDC synchronization
The CDC synchronization error is almost equal to twice the clock-period of the PTP clock (clk_pt-p_ref_i).
You must add the values that these three components determine and must write into the TSIC and TSICSNS fields of the
MAC_Timestamp_Ingress_Corr_* registers.
NOTE
The value written into the register must be negative (two's complement as explained below), because it is
subtracted from the captured timestamp. The MAC receiver adds the value in this register to the captured
timestamp and then gives the resultant value as the timestamp of the received packet.
When MAC_Timestamp_Control[TSCTRLSSR] is 1, it indicates that the nanoseconds field of the captured timestamp is in decimal
format with a granularity of 1ns. So the Bit31 of TSIC must be 1 (for negative value) and bits[30:0] must write with "10^9 - total
ingress_correction_value[nanosecond part]" represented in binary. For example, if the required correction value is -5 ns, then the
value is 0xBB9A_C9FB.
When MAC_Timestamp_Control[TSCTRLSSR] becomes 0, it indicates that the nanoseconds field of the captured timestamp
is in binary format with a granularity of ~0.466ns. Therefore, bits[30:0] must write with "2^31 - total ingress_correction_value"
represented in binary with bit[31] = 1.
72.8.3.2 Egress correction
The timestamp captured at the internal snapshot point in the transmit side is earlier (advanced in time) as compared to the
time at which the packet's SFD bit is output at the port's boundary. Therefore, the egress latency and the errors in CDC
sampling must compensate the captured timestamp. You must determine or calculate this correction value and write into the
MAC_Timestamp_Egress_Corr_* registers.
The correction value consists of these three components:
1. External latency in the PHY layer between the core output and the port and the network boundary
If the PHY is compliant with the IEEE 802.3 Clause 45 MMD registers, it has a register which indicates the maximum
and minimum egress latency. You can read these registers and determine the average egress latency in the PHY.
Alternatively (if the PHY does not support these registers), you must determine the egress latency from its datasheet or
timing characteristics.

---

*Page 2558*

Ethernet Media Access Controller (EMAC)
2. Internal latency from the internal capture point and the core output of the c
You can read this internal egress latency from MAC_Timestamp_Egress_Latency . This is a read-only register and gives the
latency in scaled nanoseconds format as defined in IEEE 1588 Clause 5.3.2. The latency differs on the basis of the active
PHY interface (MII, RMII, so on) and the operating speed. Therefore, you must read this register after any speed change
in MAC to determine the current internal latency.
3. CDC synchronization error
The CDC synchronization error value for one-step timestamping = (1 * period of CLK_PTP_REF_I + 4 * period of
CLK_TX_I). Otherwise (Two-step timestamping mode), the value = -(2 * period of CLK_PTP_REF_I).
72.8.3.3 Frequency range of reference timing clock
The timestamp information is transferred across asynchronous clock domains that is from the MAC clock domain to the
application/system clock domain. Therefore, a minimum delay is required between the two consecutive timestamp captures. This
delay is 4 clock cycles of MII and 3 clock cycles of PTP clocks. If the delay between the two captured timestamp is less than this
delay, MAC does not take a timestamp snapshot for the second packet.
72.8.3.4 PTP processing and control
Table 489 shows the common message header for the PTP messages. This format is defined in the IEEE 1588-2008.
Table 489. Message format defined in IEEE 1588-2008
Bits
7 6 5 4 3 2 1 0 Octet Offset
transportSpecific messageType 1 0
Reserved versionPTP 1 1
messageLength 2 2
domainNumber 1 4
Reserved 1 5
flagField 2 6
correctionField 8 8
Reserved 4 16
sourcePortIdentity 10 20
sequenceId 2 30
controlField (*) 1 32
logMessageInterval 1 33
(*) – control Field is used in version 1. In version 2 message type field is used for detecting different message types.
There are some fields in the Ethernet payload that detects the PTP packet type and controls the snapshot to be taken. These fields
are specified in Table 490 for PTP packets over Ethernet.

---

*Page 2559*

Ethernet Media Access Controller (EMAC)
Following table provides the information about the fields that match to control the snapshots for the PTP packets sent over
Ethernet for IEEE 1588 version 1 and version 2. The octet positions are offset by 4 for the tagged packets. This is based on the
IEEE 1588-2008, Annex D, and the message format.
Table 490. Ethernet PTP packet fields required for control And status
Field matched Octet position Matched value Description
MAC destination 0–5 01-1B-19-00-00-00 All PTP messages can use any of the
1 2
multicast address following multicast addresses :
01-80-C2-00-00-0E
• 01-1B-19-00-00-00
3
• 01-80-C2-00-00-0E
MAC packet type 12, 13 0x88F7 PTP Ethernet packet
PTP control field 46 0x00, 0x01, 0x02, 0x03, • 0x00: SYNC
or 0x04
(IEEE 1588 version 1) • 0x01: Delay_Req
• 0x02: Follow_Up
• 0x03: Delay_Resp
• 0x04: Management
PTP message type field 14 (nibble) 0x0, 0x1, 0x2, 0x3, 0x8, 0x9, • 0x0: SYNC
(IEEE 1588 version 2) 0xB, 0xC, or 0xD
• 0x1: Delay_Req
• 0x2: Pdelay_Req
• 0x3: Pdelay_Resp
• 0x8: Follow_Up
• 0x9: Delay_Resp
• 0xA: Pdelay_Resp_Follow_Up
• 0xB: Announce
• 0xC: Signaling
• 0xD: Management
PTP version 15 (nibble) 0x1 or 0x2 • 0x1: Supports PTP version 1
• 0x2: Supports PTP version 2
1. The unicast address match of destination addresses (DA), which is programmed in MAC address 0 to 31, is used if
MAC_Timestamp_Control[TSENMACADDR] = 1.
2. IEEE 1588-2008, Annex F
3. MAC does not consider the PTP version 1 messages with peer delay multicast address (01-80-C2-00-00-0E) as valid
PTP messages.

#### 72.8.4 Transmit path functions

MAC captures a timestamp when the start packet delimiter (SFD) of a packet is sent on the MII interface. You can control the
packets, for which the timestamps are captured on a per-packet basis and mark each transmit packet to indicate whether to
capture a timestamp for it. MAC does not process the transmitted packets to identify the PTP packets. You can use the control
bits in the transmit descriptor to specify the packets. MAC returns the timestamp to the software inside the corresponding transmit
descriptor and therefore connects the timestamp automatically to the specific PTP packet. You must write the 64-bit timestamp
information to TDES0 and TDES1 fields. The TDES0 field holds the 32 least significant bits of the timestamp.

---

*Page 2560*

Ethernet Media Access Controller (EMAC)

#### 72.8.5 Receive path functions

You can program MAC to capture the timestamp of all packets received on the MII interface or to process the packets to identify
the valid PTP messages.
You can use these options of MAC_Timestamp_Control to control the snapshot of the time sent to the application:
• Enable snapshot for all packets
• Enable snapshot for IEEE 1588 version 1 or version 2 timestamp
• Enable snapshot for PTP packets transmitted directly over Ethernet
• Enable timestamp snapshot for the received packet for IPv4 or IPv6
• Enable timestamp snapshot only for EVENT messages (SYNC, DELAY_REQ, PDELAY_REQ, or PDELAY_RESP)
• Enable the node to be a master or slave and select the snapshot type
Table 491. Timestamp snapshot dependency on register bits
SNAPTYPSEL TSMSTRENA TSEVNTENA PTP messages
00 x 0 SYNC, Follow_Up, Delay_Req, Delay_Resp
00 0 1 SYNC
00 1 1 Delay_Req
01 x 0 SYNC, Follow_Up, Delay_Req,
Delay_Resp, Pdelay_Req,
Pdelay_Resp, Pdelay_Resp_Follow_Up
01 0 1 SYNC, Pdelay_Req, Pdelay_Resp
01 1 1 Delay_Req, Pdelay_Req, Pdelay_Resp
10 x x SYNC, Delay_Req
11 x x Pdelay_Req, Pdelay_Resp
DMA returns the timestamp to the software inside the corresponding receive descriptor. The extended status, contains the
timestamp message status and the IPC status. You must write the extended status in the normal descriptor RDES1 and the
snapshot of the timestamp in RDES0 and RDES1 fields of context descriptor. The RDES0 field holds the 32 least significant bits
of the timestamp.
See the System time correction for programming guidelines for IEEE 1588 timestamping (System time correction).

#### 72.8.6 IEEE 1588 system time source

IP supports both the external and internal time source for reference timestamping.
• External time source uses the 64-bit external time reference and clock as an input in IP. The clock input synchronizes
the external timing reference into the MAC clock domain. Upper 32-bit indicates the time in seconds and the lower 32-bit
indicates the time in nanoseconds.
• Internal time source uses the only clock input to generate timing reference internally for snapshot and capture timestamps.
Internal time reference has two fields.
— UInteger48 seconds field: The seconds field is the integer portion of the timestamp in seconds units. It is 48-bit wide.
For example, 2.000000001 seconds are represented as seconds field = 0x0000_0000_0002.

---

*Page 2561*

Ethernet Media Access Controller (EMAC)
— UInteger32 nanoseconds field The nanoseconds field is the fractional portion of the timestamp in nanoseconds units.
For example, 2.000000001 seconds are represented as nanoseconds field = 0x0000_0001. The nanoseconds field
supports the following two modes:
◦ Digital rollover mode: In this mode, the maximum value in the nanoseconds field is 0x3B9A_C9FF, that is,
(10e9-1) nanoseconds.
◦ Binary rollover mode: In this mode, the nanoseconds field rolls over and increments the seconds field after
0x7FFF_FFFF value . Accuracy is ~0.466 ns per bit.
There is a system time register module, it is used when you use an internal time reference. The 80-bit time is maintained in this
module and updated using the input reference clock (CLK_PTP_REF_I). This time is the source for taking snapshots (timestamps)
of Ethernet packets which is transmitted or received at the MII interface.
Initialize or correct the system time counter using the coarse correction method. In this method, write the initial value or the offset
value to the timestamp update register. For initialization, write the system time counter with the value in the timestamp update
register. For system time correction, the offset value is added to or subtracted from the system time.
In the fine correction method, correct the frequency offset and/or frequency drift of a slave clock (CLK_PTP_REF_I) with respect
to the master clock (as defined in IEEE 1588-2002) over a period of time instead of in one clock, as in coarse correction. This
maintains linear time and does not introduce drastic changes (or a large jitter) in the reference time between the PTP sync
message intervals.
See Initialization guidelines for system time generation for programming guidelines for IEEE 1588 timestamping (For internal
timestamp source configuration).

#### 72.8.7 IEEE 1588 higher word register

You can invoke the higher word register if system time source is internal. MAC timestamp is 64-bit wide. The values of the upper
16-bits of the seconds field are read from the CSR register.

#### 72.8.8 Flexible pulse-per-second output

IP supports the flexibility of programming the start or stop time, generates pulse width and interval on pulse-per-second output.
It also support four such output signals. By default, IP is in fixed pulse-per-second output mode with interval of 1 second.
Initially you must program the start time in target time registers "MAC_PPSx_Target_Time_Seconds and
MAC_PPSx_Target_Time_Nanoseconds" for all desired or enabled pps output. If you re-programs start or stop time then
you must do it after the synchronization of earlier programmed value. Bit 31 of MAC_PPS#_Target_Time_Nanoseconds register
indicates that the synchronization is complete. If the application programs a start or stop time that has already elapsed, MAC sets
an error status bit which indicates the programming error. If enabled, MAC also sets the target time reached interrupt event. The
application can cancel the start or stop request only if the corresponding start or stop time has not elapsed. If the time has elapsed,
the cancel command has no effect.
Program the PPS width and interval in terms of granularity of system time, that is, the number of units of sub-second
increment value.
See Programming guidelines for flexible pulse-per-second output for more information.

#### 72.8.9 Media clock generation and recovery

Media clock recovery and generation requires a dedicated hardware and software to complete the operation. IP generates a
reference clock with the help of a dedicated hardware which multiplies to get the desired media clock. Software extracts the
presentation time from the incoming PTP 1722 frames and program these values into CSR registers so that IP can read them for
media clock recovery.

---

*Page 2562*

Ethernet Media Access Controller (EMAC)
Figure 394 shows that the timestamp array and the DMA module are external to IP. IP supports a 32-bit presentation time counter
in nanoseconds that completes at the full 32-bit value to match with 1722 presentation time format and an handshake mechanism
with an external application (such as DMA) to program the presentation time into CSR register.
Figure 394. Media clock generation and recovery block diagram
Timestamp
array
(in system RAM
external to Ethernet IP)
Read from or
write to RAM DMA external
Timestamp to Ethernet IP
Presentation
Request or acknowledge one pair per
timestamp
capture and compare unit
PTP counter Recovered
(32 bit nanosecond value) Compare value clock
Generation
MAC Comparator modules
trigger
(4 in current implementation)
Part of IP
72.8.9.1 Presentation time counter
Presentation time counter provides an another perspective of the PTP system time (1588 timer). For a given PTP system time
(PTP[63:32] represents time in seconds Comparator modules and PTP[31:0] represents time in nanoseconds), there exists
a corresponding presentation time (32-bit value in nanosecond). The presentation time computed is referred to as current
presentation time (CPT).
CPT derives from 64-bit PTP system time. PTPNS[63:0] = (PTP[63:32] * 32'd1,000,000,000) + PTP[31:0] where, PTPNS is the
PTP system time converted into a 64-bit nanosecond value.
Current presentation time[31:0] = PTPNS[31:0]
Media clock recovery is possible when the system time is internally generated in IP and compute the CPT in the same way as
the internal PTP system time generation. The increment cycle of CPT and system time are same because the values of both the
timers are in nanoseconds and they are synchronous. The timer updates at the same instance, but the updated value could be
different. You must compute a separate 32-bit update value in nanoseconds for the CPT update. CPT sampled at different edges
of a triggering input generates media clock timestamps that are inserted in 1722 based AVBTP packets, to recover the clock at
the destination.
72.8.9.2 Comparator modules
Figure 394 shows that the module supports four comparator modules to handle multiple media clocks that it may require. When
MAC_Timestamp_Control[PTGE] is 1, it indicates that the comparator modules handshake with the application to program the
timestamps into the MAC_PPS(#i)_Target_Time_Seconds register.
Write 1 to presentation time control fields of the particular instance for recovery mode.
The timestamps received from the application are referred as target presentation time (TPT).

---

*Page 2563*

Ethernet Media Access Controller (EMAC)
When MCGREN#i field of MAC_PPS_Control is 1, it indicates that the IP operates in MCGR mode. The comparator module sends
up to two requests to the application for TPT write, when MAC_Timestamp_Control[PTGE] and the presentation time control bits
of the particular instance are 1 for MCGR mode. Subsequent requests are generated each time a presentation time match occurs.
CPT transitions from a value less than TPT, to a value greater than or equal to CPT. The first request asserts when a specific
comparator instance sets to MCGR mode with a non-zero presentation time control and an additional request is made when the
comparator receives the first data. This allows the application to write the next TPT value when IP processes the previous TPT
value for a match.
TPT read from the MAC_PPS(#i)_Target_Time_Second is considered as a future time. A toggle or pulse is generated in the next
cycle when a match is detected. Also,the mcgr_dma_req_o#i request for that comparator asserts (to obtain next TPT) until the
corresponding application acknowledgment is set, when a match is detected.
The presentation control fields (PPSCMD#i field of MAC_PPS_Control ) determine the shape of the generated waveform. You can
program these fields to either toggle or generate a high/low pulse for one PTP clock cycle, when matched.
Media clock recovery: A match occurs, when the free-running CPT value matches the received TPT value. An output signal,
EMAC_TMR signal as shown in the IO mux sheet asserts (toggle, low pulse, or high pulse) on the basis of the programmed
presentation control value.
Media clock generation : On the basis of the presentation control value programmed in MAC_PPS_Control , the particular
comparator captures the presentation time and programs it into MAC_PPS(#i)_Target_Time_Second register. The captured
timestamp is read when a request is raised to the application. No new timestamps are captured, until the read operation is
complete acknowledgment is received from the application.
72.8.9.3 Media clock generation and recovery flow
Figure 395 shows the media clock generation and recovery flow. Write 1 to MAC_Timestamp_Control[PTGE] for CPT generation
and write 1 to MAC_PPS_Control MCGREN#i field to enable the corresponding instance in the MCGR mode, for both media clock
generation and recovery.

---

*Page 2564*

Ethernet Media Access Controller (EMAC)
Read data - CPT at trigger
Compare and capture unit
mcgr_dma_req_o[3:0] TSTRH0 CSR
TSTRH1
TSTRH2
PTP
TSTRH3
Write data - TPT value
mcgr_dma_ack_i[3:0]
Sync block
Sync block
Sync block
Sync block
Presentation time
Comparator3
Comparator2
MCG_PST_TRIG_I[3:0]
Comparator1
Comparator0
EMAC_TMR[3:0]
Write path
Read path
Figure 395. Media clock generation and recovery flow
NOTE
1. The consecutive triggers to sample the presentation time must assert after a few PTP clock cycles so as to
allow synchronization delays. (There is no such issue when the input trigger maximum frequency is 8 KHz
and the PTP clock runs at least at 1MHz)
2. In the Media Clock Recovery mode, the DMA acknowledgment is both posted as well as non-posted.
See Programming guidelines for media clock generation and recovery for more information.

#### 72.8.10 One step timestamp

IP supports the one step timestamp feature. Writing 1 to bit 20 (OSTC) in the control word, enables the one step timestamp feature
for a packet.
See the Programming guidelines for IEEE 1588 timestamping for more information.

#### 72.8.11 IEEE 1588 sub nanoseconds timestamp

IP supports the ingress and egress correction accuracy in sub nanoseconds. It is programmed
in MAC_Timestamp_Ingress_Corr_Subnanosec[TSICSNS] and MAC_Timestamp_Egress_Corr_Subnanosec[TSECSNS] ,

---

*Page 2565*

Ethernet Media Access Controller (EMAC)
respectively. In this case, the correction has an unit of nanosecond, multiplied by 2^8. You must program the least significant 8
bits of the value in the sub-nanoseconds register.
For example, if the required egress correction is 3.6 nS and MAC_Timestamp_Control[TSCTRLSSR] is 1, then
MAC_Timestamp_Egress_Corr_Nanosecond[TSEC] must be 0x3 and MAC_Timestamp_Egress_Corr_Subnanosec[TSECSNS]
must be 0x99 (0.6 * 256). Similarly, if the required ingress correction is -3.6 nS and
MAC_Timestamp_Control[TSCTRLSSR] is 1, then MAC_Timestamp_Ingress_Corr_Nanosecond[TSIC] must be 0xBB9A_C9FC
and MAC_Timestamp_Ingress_Corr_Subnanosec[TSICSNS] must be 0x66 (fractional part of (10^9 - 3.6) * 256).

#### 72.9 Multiple channels and queues support

This section and all its sub-sections are Synopsys Proprietary. Used with permission.
IP support two queues and channel on Tx and Rx paths. Figure 396 shows the architecture of IP with two queues and channel.
External transmit memory
Q0, Q1, Q2, ...Q7
TSO memory
Transmit DMA CH 0
AHB or AXI TSO engine Transmit MTL MAC
master COE transmit transmit
interface engine(s) queue
Receive DMA CH 0
scheduler
DMA CH0 CSR
MTL transmit FIFO controller
Transmit DMA CH 1
TSO engine
AHB, AXI, Optional
Receive DMA CH 1
APB, or APB3 PHY
slave interface
DMA CH1 CSR
interface (RGMll, RMll
DMA Arbiter or RevMll)
· MTL receive
· queue arbiter
· MAC
receive
Transmit DMA CH 7
TSO engine Select
MTL receive FIFO controller
Receive DMA CH 7 (G)MII
DMA CH7 CSR
MAC
CSR
DMA CSR MTL CSR
EQOS-CORE
EQOS-MTL
EQOS-DMA
EQOS-AXI or EQOS-AHB
Externai receive memory
Q0, Q1, Q2, ... Q7
Figure 396. Module with multiple channels and queues block diagram
The above block diagram, support only two queues.
IP DMA arbiter support two types of arbitration scheme, fixed priority and weighted round-robin. The DMA arbiter performs the
arbitration between the Tx and Rx paths of DMA channels to access descriptors and data buffers. DMA_Mode[DA] specifies the
arbitration scheme (fixed or weighted round-robin) between the channel Tx and Rx DMA.

---

*Page 2566*

Ethernet Media Access Controller (EMAC)
DMA_Mode[TXPR] sets the priority between the corresponding Tx DMA and Rx DMA. Writing 1 to DMA_Mode[PR] specifies the
weighted priority between the Tx DMA and Rx DMA in round robin arbitration scheme.
Table 492 provides an information about the priority scheme between Tx DMA and Rx DMA.
Table 492. Priority scheme for Tx DMA and Rx DMA
Bit 14 Bit 13 Bit 12 Bit 11 Bit 10 Priority schemes
x x x 0 1 Rx always has priority over Tx
0 0 0 0 0 Tx and Rx have equal priority. Rx gets the access first on simultaneous requests
0 0 1 0 0 Rx has priority over Tx in ratio 2:1
0 1 0 0 0 Rx has priority over Tx in ratio 3:1
0 1 1 0 0 Rx has priority over Tx in ratio 4:1
1 0 0 0 0 Rx has priority over Tx in ratio 5:1
1 0 1 0 0 Rx has priority over Tx in ratio 6:1
1 1 0 0 0 Rx has priority over Tx in ratio 7:1
1 1 1 0 0 Rx has priority over Tx in ratio 8:1
x x x 1 1 Tx always has priority over Rx
0 0 0 1 0 Tx and Rx have equal priority. Tx gets the access first on simultaneous requests
0 0 1 1 0 Tx has priority over Rx in ratio 2:1
0 1 0 1 0 Tx has priority over Rx in ratio 3:1
0 1 1 1 0 Tx has priority over Rx in ratio 4:1
1 0 0 1 0 Tx has priority over Rx in ratio 5:1
1 0 1 1 0 Tx has priority over Rx in ratio 6:1
1 1 0 1 0 Tx has priority over Rx in ratio 7:1
1 1 1 1 0 Tx has priority over Rx in ratio 8:1

#### 72.9.1 Multiple queues and channels support in the transmit path

IP support two transmit queues and two channels.
Fixed priority scheme is the default priority scheme for the DMA channels and channel with highest priority always wins the
arbitration when it requests for the bus. Channel 0 is always of lowest priority and channel 1 is always of highest priority.
In weighted strict priority (WSP), the weight corresponds to the number of burst transfers given to a channel in one arbitration
cycle. Reallocate the unused burst transfers of one or more channels on the basis of the priority. The channel with highest priority
receives the unused burst transfer time before it is allocated to a channel with the next highest priority. You can process the next
lower priority, when a channel uses the allocated burst transfers. After processing the allocated bandwidth of all the channels that
have packets to transmit, allocate any unused burst transfer time to the channel of the highest priority (if required), and then next
highest priority (if required), and so on.

---

*Page 2567*

Ethernet Media Access Controller (EMAC)
In weighted round robin (WRR) priority scheme, program the weight through TCW field of DMA_CH#_Tx_Control register. IN
WRR scheme, service all channels in round-robin order according to the weights settings. Table 493 shows that the TCW field of
DMA_CH#_Tx_Control register provides the weight for each transmit channel.
Table 493. Weight for DMA channels
TCW Field Transmit Channel Weight
000 1
001 2
010 3
011 4
100 5
101 6
110 7
111 8
The configured weights correspond to the number of burst transfers given to a channel in one arbitration cycle. The unused or
excess burst transfers are distributed equally to all channels.
See Programming guidelines for multi-channel multi-queuing for more information.

#### 72.9.2 Multiple queues and channels support in the receive path

IP support two receive channels and two queues.
In the receive direction, the MTL Rx controller selects the Rx DMA for which it transfers or reads the data from the receive FIFO
memory. This scheduling is based on the programming done in the respective MTL_RxQ[x]_Control register.
Each Rx DMA indicates when it is ready to transfer data and the size of the burst-length (number of beats) that it will transfer. The
scheduler checks whether sufficient data (of requested burst length) is available to transfer to these DMAs and then selects the
receive DMA that is serviced using the programmed priorities.
See Programming guidelines for multi-channel multi-queuing for more information.

#### 72.9.3 Rx queue to DMA mapping

You can program MTL_RxQ_DMA_Map0 (for queues 0 and 1) to route the packets in the MTL receive queues to any one of the
multiple DMA channels.
The following types of receive queue to DMA mapping is possible through programming.
72.9.3.1 Static mapping
In this mode, you can connect all the packets of the receive queue to a specific DMA channel. For example, all the packets from
the receive queue 0 program MTL_RxQ_DMA_Map0[Q0MDMACH] (bit[3:0]) and MTL_RxQ_DMA_Map0[Q0DDMACH] (bit[7] =
0) to route to a DMA channel.
Similarly, packets from other receive queues program register fields corresponding to each queue to route to any DMA channel.

---

*Page 2568*

Ethernet Media Access Controller (EMAC)
72.9.3.2 Dynamic (per packet) mapping
In this mode, the destination DMA channel of a packet read from a Rx queue is not constant but it is decided independently for
each packet. For example, if you write 1 to MTL_RxQ_DMA_Map0[Q1DDMACH] , it indicates that the static mapping disables for
receive queue 1 and ignores the value in MTL_RxQ_DMA_Map0[Q1MDMACH] . The MAC receiver decides the destination DMA
channel for each packet, depending on the following in decreasing order of priority:
1. L3-L4 filter based DMA selection : The TCP/UDP and IP header fields of the received packet are matched against the
corresponding values programmed and enabled for comparison in the MAC_L3_L4_Address_Control register. If the
match is successful, the DMA channel number programmed in the DMCHN field of the MAC_L3_L4_Address_Control
register is selected as the destination DMA channel number, if DMCHEN field of the same register is 1. If none of the
L3-L4 registers give a comparison match, then the module proceeds to the next step below.
2. Extended VLAN based DMA selection : Extended routing is applicable only if the VLAN filter has passed. Routing is
done only on the basis of the perfect filter result. Each perfect filter has a DMA channel enable and a DMA channel
number field which you can program. Routing is applicable for a filter, only if the DMA channel enable field is 1. The
frame routes to the smallest matching filter's DMA channel, if it is enabled. If the filter's DMA channel number is not
enabled, the frame route to channel 0. For example, if a frame's VLAN tag matches filters 7, 3, and 1, then the MAC
checks if DMA channel number of filter 1 is enabled through programming. If yes, the frame route to the programmed
value; otherwise it route to DMA. When the inverse filter is enabled; is routed to the least mismatched filter's DMA
channel number provided it is enabled. If the DMA Channel enable bit is not set, then the frame routes on the basis of
the DA based addressing or to channel 0.
If hash filter is also enabled, it is determines the filter result only. Routing will still depends on the enabled perfect filters.
Routing based on the VLAN filter will not occur if none of the perfect filters are enabled or if all of them are bypassed. The
frame will route via DA based addressing or to channel 0. If all the perfect filters fails and the hash filter has passed, then
the VLAN filter result is a pass but routing will be based on DA based addressing or to channel 0. Similar behavior is seen
when you enables inverse filtering as well.
3. Ethernet DA-based DMA selection : The DA address of the received packet is compared against the programmed DA
values in MAC address registers. If the address matches any of the programmed values, the corresponding DCS field
(when enabled) determines the destination DMA channel number.
If none of the above operations make a successful match or decision, then the packet routes to the DMA channel 0 by default.
72.9.3.2.1 Broadcast/multicast packet duplication
This feature provides a mechanism to send the received broadcast or multicast packets to multiple DMA channels.
MAC_Address0_High[DCS] and other optional MAC_Address(#i)_High (i=1 to 2) registers determines the DMA channel number
to which the received packet (that matches the MAC address present in that register) must route.
DMA channel routing mechanisms such as extended VLAN, or L3-L4 based routing does not support the packet duplication.
The packet duplication feature is supported only on the highest MTL queue configured. Therefore, you must program
MAC_RxQ_Ctrl1[MCBCQ] to the highest RxQ present in the configuration for the packet duplication for broadcast or
multicast packets.

#### 72.9.4 Selection of tag priorities assigned to receive queues

Program the PSRQ field in the corresponding MAC_RxQ_Ctrl2 to assign the VLAN tag priorities to receive queues. The bit
corresponding to the VLAN tag priority can be set in the PSRQ field for assigning that priority to the receive queue. For example,
if you want to assign VLAN tag priority of 3 to receive queue 0, write 1 to bit [3] in PSRQ field of MAC_RxQ_Ctrl2 . The VLAN tag
priority assigned to particular receive queue must be unique, that is, you cannot assign more than one receive queue to the same
VLAN tag priority. However, more than one VLAN tag priorities can be assigned to same receive queue.
The settings in the PSRQ field is used for VLAN tagged receive packet routing to receive queues as well as for PFC based transit
flow control. The received VLAN tagged receive packet route to receive queue that has the VLAN tag priority match. In PFC
based transit flow control, PSRQ field corresponding to a particular receive queue enables VLAN tag priorities in the PFC packet
transmitted when corresponding receive queue reach the threshold level.

---

*Page 2569*

Ethernet Media Access Controller (EMAC)

#### 72.9.5 Receive side routing from MAC to queues

IP supports receive side routing from MAC to queues. MAC route the receive packets to the receive queues on the basis of the
these packet types in that order
• Unicast/Multicast destination address packets that fail the destination address filter
• Multicast/Broadcast destination address packets that pass the destination address filter
• VLAN tag priority field in VLAN tagged AV data packets
• AV control packets
• VLAN tagged IEEE 1588 PTP over Ethernet packets
• Untagged IEEE1588 PTP over Ethernet packets
• VLAN tag priority field in VLAN tagged packets
• Untagged packets
You can route the outer C-VLAN tagged AV data receive packets on the basis of the priorities assigned to receive queues through
PSRQ field in the corresponding MAC_RxQ_Ctrl2 and MAC_RxQ_Ctrl3 and the corresponding receive queue is enabled for
AV through RXQ#EN field in MAC_RxQ_Ctrl0 . These packets may be single VLAN tagged with C-VLAN type or double VLAN
tagged with outer VLAN tag of C-VLAN type when double VLAN feature enables ( MAC_VLAN_Tag_Ctrl[EDVLP] = 1) with an inner
C-VLAN tagged or inner S-VLAN tagged when SVLAN processing is enabled ( MAC_VLAN_Tag_Ctrl[ESVL] = 1). This type of Rx
packet routing is available when you select the AV feature and multiple receive queues in the configuration.
You can route the AV control receive packets on the basis of the receive queue number specified in MAC_RxQ_Ctrl1[AVCPQ] and
the corresponding receive queue is enabled for AV through RXQ#EN field in MAC_RxQ_Ctrl0 . These packets may be single VLAN
tagged with C-VLAN type or double VLAN tagged with outer VLAN tag of C-VLAN type when double VLAN feature is enabled
( MAC_VLAN_Tag_Ctrl[EDVLP] = 1) with inner C-VLAN tagged or inner S-VLAN tagged when SVLAN processing is enabled
( MAC_VLAN_Tag_Ctrl[ESVL] = 1). This type of receive packet routing is available when you select the AV feature and multiple
receive queues in the configuration.
You can route the VLAN tagged receive packets on the basis of the priorities assigned to receive queues through PSRQ
field in corresponding MAC_RxQ_Ctrl2 and MAC_RxQ_Ctrl3 and RXQ#EN field in MAC_RxQ_Ctrl0 enables the corresponding
receive queue for DCB/generic. This type of receive packet routing is available when you select the multiple receive queues in
the configuration.
You can route the untagged IEEE 1588 PTP over Ethernet receive packets on the basis of the receive queue number specified
in MAC_RxQ_Ctrl1[PTPQ] and the corresponding receive queue is enabled through RXQ#EN field in MAC_RxQ_Ctrl0 . Also,
you can route the VLAN tagged IEEE 1588 PTP over Ethernet receive packets on the basis of the priorities assigned to receive
queues through PSRQ field in corresponding MAC_RxQ_Ctrl2 and MAC_RxQ_Ctrl3 or the receive queue number specified in
the MAC_RxQ_Ctrl1[PTPQ] and RXQ#EN field in MAC_RxQ_Ctrl0 register enables the corresponding Rx queue. Programming
MAC_RxQ_Ctrl1[TPQC] determines this. This type of receive packet routing is available when IEEE 1588 timestamp feature
supports and the multiple receive queues are selected in the configuration. The VLAN tagged IEEE 1588 PTP over Ethernet
receive packets are detected only when you disables 802.1AS mode ( MAC_Timestamp_Control[AV8021ASMEN] = 0), otherwise
this type of receive packets are routed as generic VLAN tagged Rx packets.
You can route the multicast or broadcast destination address receive packets that passes the destination address filter on
the basis of the receive queue number specified in MAC_RxQ_Ctrl1[MCBCQ] the MCBCQ field of MAC_RxQ_Ctrl1 register
when enabled through MAC_RxQ_Ctrl1[MCBCQEN] and the corresponding receive queue is enabled through RXQ#EN field in
MAC_RxQ_Ctrl0 . This type of Rx packet routing is available when you select the multiple receive queues in the configuration.
You can route the untagged receive packets on the basis of the receive queue number specified in MAC_RxQ_Ctrl1[UPQ] and
RXQ#EN field in MAC_RxQ_Ctrl0 enables the corresponding receive queue. This type of receive packet routing is available when
you select the multiple receive queues in the configuration.
The unicast destination address receive packets that fail the destination address filter can be routed based on the receive queue
number specified in MAC_RxQ_Ctrl4[UFFQ] when enabled through MAC_RxQ_Ctrl4[UFFQE] UFFQE, MAC_Packet_Filter[RA] =
1 and the corresponding receive queue is enabled through RXQ#EN field in MAC_RxQ_Ctrl0 . This type of receive packet routing
is available when you select the multiple receive queues in the configuration.

---

*Page 2570*

Ethernet Media Access Controller (EMAC)
You can route the multicast destination address receive packets that fails the destination address filter on the basis of the receive
queue number specified in MAC_RxQ_Ctrl4[MFFQ] when MAC_RxQ_Ctrl4[MFFQE] enables it. MAC_Packet_Filter[RA] = 1 and
RXQ#EN field in MAC_RxQ_Ctrl0 enables the corresponding receive queue. This type of receive packet routing is available when
you select the multiple receive queues in the configuration.
The receive packet will route through the receive queue 0, if it is not classified in any of the defined packet type for routing.

#### 72.9.6 Receive side arbitration between DMA and MTL

IP controller supports receive side arbitration between DMA and MTL.
After the current packet processing completes, the DMA channel controller fetches the next descriptor and after the descriptor
fetching completes, the DMA channel controller evaluates the amount of data to transfer to the Rx buffer on the basis of the
programmed PBL and receive buffer length. Accordingly, it requests the MTL to transfer the data.
After servicing the current request, the MTL receive queue arbitration scheme selects receive queue on the basis of the arbitration
scheme ( MTL_Operation_Mode[RAA] ) and the weights programmed in queue <n> receive control register. The arbitration is done
among queues for which DMA is ready to service. After the receive queue is selected, PBL amount of data is read out from that
queue and route to the receive DMA channel on the basis of the receive channel selection criteria.
The arbitration occur after every PBL data transfer completes and descriptors are ready for processing from at least one
DMA channel.

#### 72.9.7 Transit side arbitration between DMA and MTL

IP supports transit side arbitration between DMA and MTL. Transit DMA channels and transit queues are always mapped directly
because the number of transit DMA channels are always equal to the number of transit queues in MTL. For instance, each transit
DMA pushes data into its respective transit queue assigned to it.
The data inside each transit queue is stored in packets. Therefore, if two DMAs are allowed to transfer data into the same
queue, when a transit DMA starts a packet transfer, the other DMA cannot transfer data unless the previous packet is completely
pushed-in. This means that the second DMA remains idle until the first packet is transferred. Hence, each DMA is always
connected directly to its corresponding transit queue.

#### 72.9.8 Audio video bridging

IP supports the audio video bridging in 100 Mbps mode only, which allows the transmission of time sensitive traffic over network.
IP implements these protocol for supporting the AVB feature:
• IEEE 802.1Qbv-2015 (Enhancements to scheduling traffic)
• IEEE 802.3br (Interspersing traffic)
• IEEE 802.1Qbu (Frame preemption)
The queue 0 transmit path supports the strict priority algorithm, and it is used for best-effort traffic when transmit paths of additional
queues use the credit-based shaper algorithm to support traffic management. For a queue, the credit-based shaper algorithm
determines that a queue is available for transmission if these conditions are true:
• The queue contains one or more packets.
• The credit for the queue is positive per the algorithm
• AV traffic is received on all queues. IP can also receive untagged PTP packets in addition to AV traffic on any queue.

#### 72.9.9 Queue modes

Transit and receive queues both handles the AV traffic.
Queueing is based on WRR (Weighted round robin) or WSP (Weighted strict priority) algorithm for generic traffic. It is based on
CBS (Credit Base Shaper) and SP (Strict Priority) algorithm for AV traffic.

---

*Page 2571*

Ethernet Media Access Controller (EMAC)
The receive queue is configured for generic or AV based routing, which depends on the RXQ#EN field of corresponding queue.
Queueing is done on the basis of the VLAN tag priority. The VLAN tag priority must match the PSRQ field of MAC_RxQ_Ctrl2 . The
receive packets can route to a particular DMA channel on the basis of the DCS field of perfectly-matched MAC address register.
By default, the untagged packets in a generic traffic route to the receive queue specified in MAC_RxQ_Ctrl1[UPQ] . Queue 0 is the
default value of MAC_RxQ_Ctrl1[UPQ] . You can override the default value with any other value, for the MAC_RxQ_Ctrl1[UPQ] .
The AV control packets (tagged or untagged) route on the basis of MAC_RxQ_Ctrl1[AVCPQ] . The PTP over Ethernet packets
route on the basis of MAC_RxQ_Ctrl1[PTPQ] .

#### 72.9.10 Queue priorities

You can program the priority of an receive queue in the corresponding field of MAC_RxQ_Ctrl2 . Also, you must program the AV
queue (high priority) as an higher priority than the best-effort queue (low priority).

#### 72.9.11 Enhancements to scheduled traffic (EST)

The IEEE 802.1Qbv-2015 defines the schedule for each queues on every egress port which makes the implementation aware of
traffic arrival schedule. This information blocks the lower priority traffic from transmission in this time window or slot. This ensures
that the sender forward the scheduled traffic to receiver through all the network nodes with a deterministic delay.
Periodic priority data
Scheduled traffic windows
Figure 397. Time aware shaper implementation for traffic scheduling
An important requirement to achieve a low latency is to ensure that there are no interfering frames during the scheduled windows
that are reserved for high priority traffic. The use of scheduled traffic imposes limitations when a transmission starts.
As shown in Figure 398 , if an interfering frame begins transmission just before the reserved time period starts, it can extend
transmission into the reserved window, and potentially interfere with higher priority traffic. Therefore, a guard band whose size is
equal to the largest possible interfering frame is required before the window starts.
Late
Interfering frame
Guard band
Interfering frame
Figure 398. Implementing a guard band to avoid delays due to interfering frames
A larger guard band equates to a less efficient use of network bandwidth. However, this issue is addressed with the implementation
of IEEE802.1Qbu (frame preemption). Frame preemption breaks the interfering frame into smaller fragments. Therefore, the
guard band must be as large as the largest possible interfering fragment instead of the largest possible interfering frame.
During the guard band, only the frames that can complete the transmission of the entire frame before the next gate close event
are permitted. This ensures that the high priority traffic can always start at the beginning of the window reserved for it.

---

*Page 2572*

Ethernet Media Access Controller (EMAC)
72.9.11.1 Frequently used terms in EST
These are some of the frequently used terms in the EST support and their definitions.
• Gate control list: The list in the hardware memory that hold the gate controls and the associated time intervals.
• Gate controls: For a given schedule (row in gate control list) there is a gate open (O) and gate close (C) state associated
with each TC. The set of O or C values, whose width is same as configured TCs is called the gate controls. Example:
CCOCOOCO means TC7=C, TC6=C, TC5=O and so on.
• Time interval: Time interval (in nanoseconds) is a 16, 20, or 24-bit configurable field in the gate control list that indicates
the time for which the associated gate controls are valid.
• Base time register: Each gate control list is associated with a 64-bit base time register that holds the start time (in PTP
format) for the list.
72.9.11.2 Updates to the transmit scheduling to support EST
To support EST, these updates are required to transmit scheduling:
• Implementation of gate control list (GCL)
• Enforcing gate controls in the scheduler
• Utilizing gate open (O) duty cycle in the computation of idleSlope (CBS)
72.9.11.2.1 Implementation of gate control list (GCL)
Figure 399 shows how the gate control list governs the gate close(C) and open(O) events on the basis of the schedule provided
for each event. GCL has two parts:
• Time interval: Defines the time in nanoseconds for which the gate controls are valid and must apply before reading the
next gate controls from the list. The configurable width of 16, 20 or 24-bit represents a maximum of 64us, 1ms, or 16ms
schedule interval respectively. It supports left shift of the time interval up to 7 bits to be able to apply a multiplication factor
from 1 to 128ns (in steps of 2(power)n). The maximum value (post shifting) of this field must be 999,999,999 ns.
• Gate control: Defines the open (O) represented by logic 1 or close (C) represented by logic 0 state for the gate of each
TC.
Queue for Queue for Queue for Queue for Queue for
traffic class #7 traffic class #6 traffic class #5 traffic class #4 traffic class #0
T00: oCooCooo
Transmission Transmission Transmission Transmission Transmission
T01: CoCooCCo
selection selection selection selection selection
T02: oCooCooo
algorithm algorithm algorithm algorithm algorithm
T03: ooCooCCo
T04: oCooCooo
T05: CoCCoCCC
Transmission Transmission Transmission Transmission Transmission T06: oCooCooo
Gate = C Gate = o Gate = C Gate = C Gate = C T07: CoCooCCC
T08: oCooCooo
T09: CoCCoCCC
...
T78: oCooCooo
Transmission selection T79: CoCooCCC
Note: Only two queues are supported
Figure 399. Block diagram from IEEE 802.1 Qbv specification - GCL governing gate close and open events

---

*Page 2573*

Ethernet Media Access Controller (EMAC)
The implementation of GCL consists of these two gate control lists:
• HWOL - Hardware owned list which is a list for hardware access.
• SWOL - Software owned list which is a list for software access.
The access to these lists is mutually exclusive. Hardware sets the ownership to the list in MTL_EST_Status[SWOL] . Table 494
provides the implementation details of GCL.
Table 494. External memory used for holding the two gate-control lists
Gate control (up to 8 bits) Time interval (ns) 16, 20, or 24 bits
OOCCCCCC T0 HWOL or SWOL
OOOOCCCC T1
CCCCOOCC T2
CCCCCCOO T3
I I
I I
OCOCOCOO
OOOOCCCC T last
OOOOCCCC T0 SWOL or HWOL
OOOOCCCC T1
OOCCCCCC T2
OOOOCCCC T3
I I
I I
OCOCCCCC
OOCCCCCC T last
72.9.11.2.2 Registers related to gate control list
The following are the set of four registers (one for each GCL) related to GCL. These registers are implemented through Indirect
addressing using MTL_EST_GCL_Control and MTL_EST_GCL_Data registers.
1. 64-bit Base time register (BTR)
2. 40-bit Cycle time register (CTR)
3. m-bit Time extension register (TER)
4. n-bit [Gate control] List length register (LLR) (n depends of the configured GCL depth)

---

*Page 2574*

Ethernet Media Access Controller (EMAC)
72.9.11.2.2.1 Base time register (BTR)
This is a 64-bit register that specifies the start time to execute the GCL. The format of the BTR is same as the PTP format
(upper 32-bits holds time in seconds and lower 32 bits hold time in nanoseconds). After the execution of a given list starts, the
implementation can update the value in BTR to indicate the next list execution start time.
72.9.11.2.2.2 Cycle time register (CTR)
This is a 40-bit register that specifies the time at which the execution of the GCL must repeat. This register consists of an 8-bit
value in seconds, and a 32-bit value in nanoseconds (similar to the PTP time format with truncated seconds register). For a given
GCL, the start time is "Base Time" + N * "Cycle Time" where N is an integer value that represents the iteration number starting
with 0 for first iteration. If the GCL execution takes longer than the cycle time, then the list is truncated at the cycle time and the
subsequent loop begins at cycle time.
72.9.11.2.2.3 Time extension register (TER)
This is a m-bit (where m = Configured time interval width + 7) register that specifies the amount of time (in nano seconds) the
current GCL can extend before switching to the new GCL. This helps to avoid the execution of the small fragments of the current
list before switching to a new list.
72.9.11.2.2.4 List length register (LLR)
This is an n-bit register (when n is 7, 8, 9, 10, or 11 for a GCL configured depth of 64, 128, 256, 512, or 1024 respectively) that
specifies the integer value of the length of the GCL (that is the number of valid rows in each GCL). The processing of the GCL
stops after the number of rows read equals to the LLR value.
72.9.11.2.3 Transmission gating implementation
A bridge or an end station can enhance to allow transmission from each TC that is yet to be scheduled relative to a known
timescale. To achieve this, a transmission gate is associated with each TC; the state of the transmission gate determines if you
can select the queued frames for transmission.
For a TC, the transmission gate can be in one of these two states:
1. Open: Select queued frames for transmission, in accordance with the definition of the transmission selection algorithm
associated with the TC.
2. Closed: Does not select queued frames for transmission.
A frame on a traffic class queue is not available for transmission if the transmission gate is in the closed state or if there is
insufficient time available to transmit the entirety of that frame before the next gate-close event associated with that queue.
The implementation has visibility to the current schedule of gate controls and the immediate next schedule of the gate controls.
So the maximum Gate Open period does not exceed the sum of the two Time Intervals. This is because, a frame is selected for
transmission only if the gate is currently Open and the duration of gate open interval is greater than the time taken to transfer the
entire frame.
The implementation must know the frame size before the transmission, so that you can avoid the transmission overruns and only
the frames that can complete are scheduled at all times.
The implementation adequately compensates for the implementation delays in the data transfer from the buffer to the line by
offsetting the current time with all the relevant delays (provided by MTL_EST_Control[CTOV] ). This ensures that the schedule
provided is always accurately implemented at the line.
You must ensure that the GCL slot interval is always greater than the expected packet size and overhead (scheduler delay, inter
frame gap (IFG), and preamble, all combined).
72.9.11.3 Idle slope computation updates
When EST is enabled, credit accumulates only when the gate is open therefore, the effective data rate of the idleSlope must
increase to reflect the duty cycle for the transmission gate associated with the queue.

---

*Page 2575*

Ethernet Media Access Controller (EMAC)
The idleSlope is computed on the basis of the gate open time and oper cycle time values. Program the idleSlope registers
(implemented one per CBS enabled TC) based on the following equation. The existing MTL register has sufficient field width to
accommodate the new values for idleSlope.
idleSlope = (operIdleSlope(N) * OperCycle/GateOpenTime)
72.9.11.3.1 Operational details of GCL
Write 1 to MTL_EST_Control[SSWL] , so that the hardware can access the programmed gate control list. The first set of gate
controls are applied when the current time is equal to the value in the base time register (BTR) and is held until the programmed
"time interval" value.
One additional gate control event is always read ahead from the list, to avoid the transmission overruns. This enables the GCL
to determine the next gate close events (if any) for the open TCs.
The scheduling is done on the basis of the gate open state and time interval of only the current and subsequent schedule. An
internal accumulator adds the time intervals when gate controls are applied. BTR + Accumulator specifies the time at which the
next set of gate controls are applied.
CTR = 6000 ns
BTR = 14200 ns
Gate control Time interval
OOCCCCCC 1000
OOOOCCCC 500
CCCCOOCC 2200
CCCCCCOO 300
Figure 400. GCL and associated registers - basetime and cycletime
GCL is read progressively from the first row adhering to the schedule. The read operations continue until the list length (from LLR
register) is reached and the execution of the list restarts at BTR + CTR time. At this point the value in CTR increments BTR to mark
the beginning of a new cycle. In the absence of any gate controls, all the gates are in open state, during the execution of the list.
In cases where the execution time of the list is greater than the cycletime, the list is truncated and the next iteration starts when
the current time equals BTR + CTR.
Table 495. GCL and associated registers - BTR and CTR
Current time Gate control applied Accumulator value BTR (with updates)
14200 OOCCCCCC 1000 14200
15200 OOOOCCCC 1500 14200
15700 CCCCOOCC 3700 14200
17900 CCCCCCOO 4000 14200
Table continues on the next page...

---

*Page 2576*

Ethernet Media Access Controller (EMAC)
Table 495. GCL and associated registers - BTR and CTR (continued)
Current time Gate control applied Accumulator value BTR (with updates)
18200 OOOOOOOO 0 20200
20200 OOCCCCCC 1000 20200
21200 OOOOCCCC 1500 20200
Table 495 describes an example in which the execution starts at 14200 and the first set of gate controls "OOCCCCCC" are applied
immediately. The time interval is 1000 ns, so the next set of gate controls are applied at 14200 (BTR) + 1000 (Accumulator) =
15200 ns . The above table shows that there are no gate controls available after the execution of the last gate control and before
the next iteration of the loop. The gates are deemed in open state during that time period as depicted at 18200 current time.
CTR = 3000 ns
BTR = 14200 ns
Gate control Time interval
OOCCCCCC 1000
OOOOCCCC 500
1500
CCCCOOCC 2200
CCCCCCOO 300
Figure 401. GCL and associated registers - BaseTime and CycleTime list execution time > CycleTime
Table 496 indicates that the list execution takes longer than the allocated CycleTime, so the list is truncated and the list starts from
the BTR+CTR.
Table 496. GCL and associated registers - BTR and CTR, execution time > Cycle Time
Current Time Gate Control Applied Accumulator Value BTR (with updates)
14200 OOCCCCCC 1000 14200
15200 OOOOCCCC 1500 14200
15700 CCCCOOCC 3700 14200
17200 OOCCCCCC 1000 17200
18200 OOOOCCCC 1500 17200
18700 CCCCOOCC 3700 17200
When you apply the third set of gate controls, BTR + CycleTime (17200) < BTR + Accumulator (17900), so the list is truncated
and execution switches to a new iteration at 17200.

---

*Page 2577*

Ethernet Media Access Controller (EMAC)
72.9.11.3.2 Installing a new GCL
The switch to the new GCL can happen in one of the following ways when a new software programmed GCL is available and
executed at the new BTR value:
• New base time aligned with current schedule
• New base time unaligned with current schedule
72.9.11.3.2.1 New base time aligned with current schedule
If the choice of cycle time for the new gating cycle is unchanged from the cycle time for the current gating cycle, and if the BTR
chosen for the new gating cycle (new BTR) is an integer multiple of the current cycle time (+ current BTR), then the new gating
cycle exactly lines up with the old gating cycle, that is, the cycle start times for the new gating cycle is same as they would have
been for the old configuration. This could be considered to be the ideal case and allows the new gating cycle to be installed and
executed with no timing issues. The implementation completes the execution of an iteration of the current list and switches to the
new list at the beginning of the BaseTime listed in the new list.
If (New base time > = Current time)
ConfigChangeTime = New BaseTime
Else If (New base time < Current time)
1. Set the BTRError
2. ConfigChangeTime = (New BaseTime + N* New CycleTime)
where N is the smallest integer for which the relation ConfigChangeTime >= CurrentTime and (N =< 8) is true.
When N > 8 the hardware cannot auto recover and the loop count value in BTRError reporting is set to 1111 requires the software
to reprogram the new base time.
Figure 402 illustrates the installation of the new GCL along with the timelines.
CTR = 6000 ns CTR = 6000 ns
BTR = 14200 ns BTR = 50200 ns
Gate control Time interval Gate control Incr schedule
OOCCCCCC 1000 CCCOCCCC 1000
OOOOCCCC 500 CCCCOCCC 2000
CCCCOOCC 2200
CCCCCCOO 300
Figure 402. Switching to a new configuration that is aligned with the existing configuration
In the above example, after the sixth iteration of the first GCL, the BTR values of the old and new GCL are equal. At that point the
new GCL is processed as a natural extension to the existing GCL.

---

*Page 2578*

Ethernet Media Access Controller (EMAC)
Table 497. GCL and associated registers - BTR and CTR
Current time Gate control applied Accumulator value BTR (with updates)
44200 OOCCCCCC 1000 44200
45200 OOOOCCCC 1500 44200
45700 CCCCOOCC 3700 44200
47900 CCCCCCOO 4000 44200
48200 OOOOOOOO 0 50200
50200 CCCOCCCC 1000 50200
51200 CCCCOCCC 3000 50200
53200 OOOOOOOO 0 56200
72.9.11.3.2.2 New base time unaligned with current schedule
If the new cycletime differs from the current cycletime or new basetime in the future and is not an integer multiple of current
cycletime, then the old and new cycles do not line up. When new basetime is reached ( when the new configuration is installed and
starts to execute), the last old cycle is normally truncated to start the first new cycle. This could be undesirable if it results in a very
short last old cycle; arguably it would be better to simply extend the penultimate old cycle by that small amount, rather than starting
a very short cycle. The Cycle Time Extension Register (related to the current config list) allows this extension of the last old cycle
to be done in a defined way; if the last complete old cycle ends normally in less than current Cycle Time Extension (TER) ns before
the new base time, then the last complete cycle before new BaseTime is reached is extended so that it ends at new BaseTime.
TER = 1000 ns
CTR = 6000 ns CTR = 6000 ns
BTR = 14200 ns BTR = 47500 ns
Gate control Time interval Gate control Incr schedule
OOCCCCCC 1000 CCCOCCCC 1000
OOOOCCCC 500 CCCCOCCC 2000
CCCCOOCC 2200
CCCCCCOO 300
Sixth
iteration
truncated at
1800 ns
Figure 403. Switching to new list truncating the current list
At the end of the fifth iteration the Current time + Cycle time extension (TER) < New BTR so the sixth iteration of current
configuration is started. During the sixth iteration of the current list when the new BTR value is smaller than the next schedule in
the current list, it switches to the new list.

---

*Page 2579*

Ethernet Media Access Controller (EMAC)
Table 498. Extending to new list by truncating the current list
Current time Gate control applied Accumulator value BTR (with updates)
44200 OOCCCCCC 1000 44200
45200 OOOOCCCC 1500 44200
45700 CCCCOOCC 3700 44200
47500 CCCOCCCC 4000 44200
48500 CCCCOCCC 0 50200
50500 OOOOOOOO 1000 50200
Below is an example where the current config list is extended instead of starting a new iteration as the extension time of 800 ns
is less than the allowed cycle extension time (TER) of 1000 ns.
TER = 1000 ns
CTR = 6000 ns CTR = 6000 ns
BTR = 14200 ns BTR = 45000 ns
Gate control Time interval Gate control Incr schedule
OOCCCCCC 1000 CCCOCCCC 1000
OOOOCCCC 500 CCCCOCCC 2000
CCCCOOCC 2200
CCCCCCOO 300
Fifth
iteration
truncated at
1800 ns
Figure 404. Switching to new list by extending the current list
Table 499. Switching to new list by extending the current list
Current time Gate control applied Accumulator value BTR (with updates)
38200 OOCCCCCC 1000 38200
39200 OOOOCCCC 1500 38200
39700 CCCCOOCC 3700 38200
41900 CCCCCCOO 4000 38200
42200 OOOOOOOO 0 44200
Table continues on the next page...

---

*Page 2580*

Ethernet Media Access Controller (EMAC)
Table 499. Switching to new list by extending the current list (continued)
Current time Gate control applied Accumulator value BTR (with updates)
45000 CCCOCCCC 1000 45000
46000 CCCCOCCC 3000 45000
48000 OOOOOOOO 0 51000
72.9.11.4 Impact of Transmit Scheduling Algorithms on EST
When EST is used in isolation, the Gate Control List manages the final open/close state of the Queues along with the checks
carried out by the Transmission Selection Algorithm in MTL. As the Gate Controls operate on a predefined repetitive schedule, it
is recommended to use Strict Priority or Credit Based Shaper (CBS) scheduling algorithms.
Other algorithms such as the Weighted Round Robin (WRR), Deficit Weighted Round Robin (DWRR) and Weighted Fair Queuing
(WFQ) implement masking of the queues based on the current winning queue. The algorithm is applied only among the group of
queues that open simultaneously. To ensure Queues whose gates are "Open" get priority, these algorithms are modified to treat
"gate open" queues and "gate closed" queues as separate groups giving priority to gate open queues.
For example, consider 4 queues (Q3, Q2, Q1, Q0) with weights 4:3:2:1; Q3 and Q2 are in Open state in slot one slot, while Q1
and Q0 are in Open state in another slot. In this case, the scheduler works as follows:
1. In the first slot, the Q3 and Q2 are serviced in the ratio of 4:3 for the duration the slot is open.
2. In the second slot, the queues Q1 and Q0 are serviced in the ratio of 2:1.
3. Fresh arbitration is started every time a slot is opened.
In other words, the traffic does not get distributed in the intended ratio of 4:3:2:1; but as two groups with different ratios and only
for the duration of the slot when the gates are open continuously
NOTE
See Programming guidelines for EST

#### 72.9.12 Frame preemption (FPE)

Frame preemption breaks the interfering frame into smaller fragments. Therefore, the guard band needs to be only as large as
the largest possible interfering fragment instead of the largest possible interfering frame. During the guard band, only the frames
that can complete the transmission of the entire frame before the next gate close event are permitted. This ensures that the high
priority traffic can always start at the beginning of the window reserved for it.
Preemption allows one or more higher priority (express) frames to interrupt the transmission of a lower priority (preemptable)
frame; the preemptable frame transmission is resumed and completed after the express frame transmission is complete. To
support frame preemption, the following two abstractions of the MAC are used:
• A preemptable MAC, called pMAC, which carries the preemptable traffic.
• An express MAC, called eMAC, which carries the express traffic.
In the implementation, only parts of the MAC that holds the state during preemption is replicated and represented as pMAC
and eMAC. The MAC uses the following two ways to puts on hold, the transmission of the preemtable traffic, in the presence of
express traffic:
• The MTL scheduler interrupts the preemptable traffic that is currently being transmitted. When the preemption capability is
active, the MAC interrupts the transmission and reception of preemptable frames. A preempted fragment can be followed
by zero or more express frames, before the continuation fragments. The preemptable frame can be fragmented any
number of times, however, the minimum final and non-final fragment length criterion must be is met. However, interleaving
of more than 1 preemptable packet is not permitted. This implies that if a preemptable packet is fragmented by an express

---

*Page 2581*

Ethernet Media Access Controller (EMAC)
packet, another preemptable packet cannot be transferred until all the remaining fragments of the first preempted packet
are transferred.
• The MTL scheduler prevents starting the transmission of preemptable traffic. When the preemption capability is inactive,
the pMAC entity is disabled and only express traffic is transmitted or received. Frame preemption feature can be enabled
by setting EFPE field of the MAC_FPE_CTRL_STS register.
If you are unable to schedule fragmented packets after a certain number of attempts because of some reason, there is a possibility
that the remaining fragmented packets are dropped. For this, software can program MTL_EST_Control[LCSE] to increase number
of iterations or MTL_EST_Control[DFBS] to disable dropping of packet.
NOTE
EST and FPE cannot be used at the same time.
72.9.12.1 GCL Modification to Support FPE
In the EST-only configuration, the GCL entry has up to 24 bits of Time Interval and up to 8 high order bits representing the Gate
Open/Close state requirements as shown in the following figure.
Gate control Time interval (ns)
(up to 8 bits) 16, 20 or 24 bits
OOCCCCCC T0
OOOOCCCC T1
CCCCOOCC T2
CCCCCCOO T3
| |
| |
OCOCOCOO T126
OOOOCCCC T127
Figure 405. Gate Control List When FPE is Disabled
EST only supports SetGate operation, which implies that the gates are set to either open or close at a given time interval. However,
when both Frame Preemption (FPE) and EST are enabled, the GCL also supports Set-and-Hold-MAC and Set-and-Release-MAC
operations, in addition to the SetGate operation. To enable the support of hold and release operations, the format of the GCL
is slightly changed while configuring and enabling the FPE. The first Queue (Q0) is always programmed to carry preemption
traffic and therefore it is always Open. The bit corresponding to Q0 is renamed as Release/Hold MAC control. The TX Queues
whose packets are preemptable are indicated by setting the PEC field of the MTL_FPE_CTRL_STS register. The GCL bit of the
corresponding Queue are ignored and considered as always "Open". So, even if the software writes a "0" ("C"), it is ignored for
such queues.

---

*Page 2582*

Ethernet Media Access Controller (EMAC)
Gate control Release or hold Time interval (ns)
(up to 7 bits) indication 16, 20 or 24 bits
CCCOOOO 0 T0
CCCCOOO 0 T1
OCCCOOO 1 T2
COCCOOO 1 T3
CCOCOOO 1 T4
CCCOOOO 0 T5
CCCCOOO 0 T6
Figure 406. Gate Control List When FPE is Enabled
When the Release/Hold bit transitions from a '0' to '1', a Set-and-Hold-MAC operation is performed. This marks the cease of the
preemptable traffic. This is achieved by sending a Hold request to MTL "ha" ns in advance (where ha is the time interval mentioned
in the Hold Advance (HADV) field of the MTL_FPE_Advance register). When the Release/Hold bit transitions from a '1' to '0' a
Set-and-Release-MAC operation is performed. This marks the resuming of the preemptable traffic. This is achieved by sending
a Release request to MTL "ra" ns in advance (where ra is the time interval mentioned in the Release Advance (RADV) field of the
MTL_FPE_Advance register). The preemptable traffic is blocked for the time duration the Release/Hold bit is set to a '1' in the Gate
Control List.
72.9.12.2 Impact of Preemption on CBS
In Credit Base Shaper(CBS), the definition of "Transmit" is as follows:
• TRUE for the duration of frame transmission from the queue.
• FALSE when frame transmission from the queue is complete.
• When CBS algorithm is used along with frame preemption, the value of "Transmit" is TRUE only while the frame is
being transmitted by the MAC. If the frame transmission is delayed or interrupted (for instance, a preemptable frame
transmission is interrupted to allow the transmission of an express frame from a different queue, or the start of express
frame is delayed because a preemptable frame is being transmitted) the value of "Transmit" is FALSE until transmission of
the frame commences or is resumed.
Also, the value of "Transmit" is FALSE during the transmission of any overhead that is a consequence of frame preemption. For
example, additional frame overhead (mCRC, Fragment Count) that is added to the preemptable frame.
At any given time, if there are no frames in the queue, and the value of Transmit is FALSE, and credit is positive value, the credit
value is set to zero if here is no preemptable frame from the queue for which transmission is in progress but has been interrupted.
72.9.12.3 mPacket Format
When the preemption capability is active, MAC sends mPackets to the PHY. An mPacket can be one of the following:
1. A express packet
2. A preemptable packet
3. An initial fragment of a preemptable packet
4. A continuation fragment of a preemptable packet
Start mPacket Delimiter (SMD)
The value of the SMD indicates whether the mPacket contains an express packet, the start of a preemptable packet (initial
fragment or complete packet), or any of continuation fragments of a preemptable packet. Following table shows the valid
SMD values.

---

*Page 2583*

Ethernet Media Access Controller (EMAC)
Table 500. Possible SMD Values of mPacket
mPacket Type Notation Frame Count Value
verify packet SMD-V - 0x07
respond packet SMD-R - 0x19
express packet SMD-E - 0xD5
preemptable packet start SMD-S0 0 0xE6
SMD-S1 1 0x4C
SMD-S2 2 0x7F
SMD-S3 3 0xB3
continuation fragment SMD-C0 0 0x61
SMD-C1 1 0x52
SMD-C2 2 0x9E
SMD-C3 3 0x2A
frag_count
A frag_count is a modulo-4 counter that increments for each continuation fragment of the preemptable packet. The frag_count
protects against mPacket reassembly errors by enabling detection of the loss of up to 3 packet fragments.
The frag_count field is present only in mPackets with SMD-C notation (continuation fragment). The frag_count is zero in the first
continuation fragment of each preemptable packet.
Table 501. Possible frag_count Values
frag_count Value
0 0xE6
1 0x4C
2 0x7F
3 0xB3
mData
The contents of the packet from the MAC, starting with the first byte after the SFD to the last byte before the FCS are sent in the
mData fields of one or more mPackets for that frame. The minimum size of the mData field is 60 bytes.
CRC The CRC field contains a cyclic redundancy check (CRC) and has an indication of the final mPacket of a frame. In the final
mPacket of a frame, the CRC field contains the last 4 octets of the MAC frame (the FCS field).
For other mPackets, the CRC field contains an mCRC value. The mCRC is calculated on the octets of the packet from the first
octet of the frame (the octet following the SFD of preemption frames) to the last octet of the packet transmitted in that mPacket
by performing an XOR of the calculated 32 bit CRC value of the fragment and a value of 0x0000FFFF.
Summary of Packet Formats

---

*Page 2584*

Ethernet Media Access Controller (EMAC)
• Express Packet: 7bytes of PREAMBLE, SMD-E, Data, and CRC
• Complete Preemptable Packet: 7 bytes of PREAMBLE, <Current Preemptable packet SMD>, Data, CRC
• Initial Fragment (non-final) of Preemptable Packet: 7 bytes of PREAMBLE, <Current Preemptable packet SMD>, Data,
mCRC
• Continuation fragments (non-final) of Preemptable packet: 6 bytes of PREAMBLE, <Current Preemptable continuation
fragment SMD>, <Current Preemptable continuation fragment FC>, Data, mCRC
• Final fragment of Preemptable packet: 6 bytes of PREAMBLE, <Current Preemptable continuation fragment SMD>,
<Current Preemptable continuation fragment FC>, Data, CRC
Table 502. Current and Previous SMD Values
Previous Preemptable packet SMD Current Preemptable packet SMD
SMD-S0 SMD-S1
SMD-S1 SMD-S2
SMD-S2 SMD-S3
SMD-S3 SMD-S0
Table 503. Current and Previous SMD Values
Previous
Previous Preemptable Preemptable Current Preemptable continuation Current Preemptable continuation
fragment SMD fragment FC fragment SMD fragment FC
SMD-S0 NA SMD-C0 FC0
SMD-S1 NA SMD-C1 FC0
SMD-S2 NA SMD-C2 FC0
SMD-S3 NA SMD-C3 FC0
SMD-C0 FC0 SMD-C0 FC1
SMD-C0 FC1 SMD-C0 FC2
SMD-C0 FC2 SMD-C0 FC3
SMD-C0 FC3 SMD-C0 FC0
SMD-C1 FC0 SMD-C1 FC1
SMD-C1 FC1 SMD-C1 FC2
SMD-C1 FC2 SMD-C1 FC3
SMD-C1 FC3 SMD-C1 FC0
SMD-C2 FC0 SMD-C2 FC1
Table continues on the next page...

---

*Page 2585*

Ethernet Media Access Controller (EMAC)
Table 503. Current and Previous SMD Values (continued)
Previous
Previous Preemptable Preemptable Current Preemptable continuation Current Preemptable continuation
fragment SMD fragment FC fragment SMD fragment FC
SMD-C2 FC1 SMD-C2 FC2
SMD-C2 FC2 SMD-C2 FC3
SMD-C2 FC3 SMD-C2 FC0
SMD-C3 FC0 SMD-C3 FC1
SMD-C3 FC1 SMD-C3 FC2
SMD-C3 FC2 SMD-C3 FC3
SMD-C3 FC3 SMD-C3 FC0
72.9.12.4 Transmit Preemption
When FPE is enabled (setting EFPE=1 in MAC_FPE_CTRL_STS register), the MTL preempts a preemptable frame, when a "hold"
request is asserted (EST/Qbv configured and enabled) express frames are available for transmission, that is, frame is present in
MTL FIFO and is qualified for arbitration, after ensuring that the minimum mPacket mData field size is met. Therefore, preemption
occurs only if at least 60 bytes of the preemptable frame have been transmitted and at least 64 bytes (including the frame CRC)
remain to be transmitted.
The earliest starting position of preemption is controlled by the AFSZ field of the MTL_ FPE_CTRL_STS Register. Preemption
does not occur until at least 64 * (1+ AFSZ) - 4 bytes of the preemptable frame have been sent.
When preemption occurs, all the preemptable queues are blocked and only the express queues are allowed to arbitrate (if more
than one express queue has traffic) and transmit.
Continuation fragment of the preempted frame is the first frame to be transmitted after "release" request is asserted (EST/Qbv
configured and enabled) and all the express traffic transmission completes.
NOTE
All the PTP packets should be transmitted as express packets
MTL communicates the following frame-type information to the MAC using a 2-bit preemption control signal on the MTI interface
qualified by SoF and EoF.
• Express Frame
• Preemption Frame (Full or Fragment)
• Continuation Fragment (non-Final or Final)
Table 504. Preemption Control Values on MTI Interface for Various Frame Types
Qualifier Preemption Control Value Frame Type
SoF 00 Start Express
SoF 01 Start Preemption
Table continues on the next page...

---

*Page 2586*

Ethernet Media Access Controller (EMAC)
Table 504. Preemption Control Values on MTI Interface for Various Frame Types (continued)
Qualifier Preemption Control Value Frame Type
SoF 10 Continuation Fragment
SoF 11 Reserved
EoF 00 End of Frame
EoF 01 End of Fragment
MTL should wait for the previous fragment status to be received before resuming the continuation fragments of a preempted frame.
Fragment status is described in detail in the Tx Fragment Status section.
72.9.12.4.1 MAC Tx Preemption
MAC supports preemption by implementing the functionality needed to generate the mPackets as described in “mPacket Format”.
Based on the Preemption Control value received on the MTI interface (qualified with SoF and EoF), MAC determines the
frame type (shown in the above table 'Preemption Control Values on MTI Interface for Various Frame Types') and generates
mPackets accordingly.
When the preemption capability is active, MAC replaces the SFD of a preemption packet with an SMD-S value. A 2-bit rolling frame
count is encoded in the SMD-S value.
The SMD-E value is the same as the SFD value so the SFD of an express packet does not need to be replaced.
72.9.12.4.1.1 Tx Fragment Status
MAC sends Tx fragment status to indicate successful transmission of fragmented mPackets.
In case of a transmission error (underflow, jabber, and so on) the frame status is sent (and not a fragment status) with an error
indication along with all other relevant status fields. In case of receiving an error status for a transmitted fragment, the MTL drops
the remaining fragments and does not send any more continuation fragments.
72.9.12.5 Receive Preemption
72.9.12.5.1 MAC Receive Preemption
When FPE is enabled, the MAC Receiver passes the incoming packets and differentiates between Express packets and
preemptable packets. An SMD containing an SMD-E indicates express packet, and SMD containing an SMD-S indicates the first
mPacket of a preemptable packet.
If an mPacket containing an SMD-S is received when MAC has not completed receiving the previous preempted packet, MAC
sets a CRC Error status for the previously received partial packet.
When an SMD-S is detected, MAC records the frame count indicated by the SMD and then begins sending data on the
MRI interface.
The MAC checks the last four bytes of the mPacket . If the last four bytes of the mPacket do not match CRC, that indicates the end
of the packet with or without a CRC error as per the CRC check result. If the last four octets of the mPacket match, that indicates
that the packet was preempted.
An SMD containing an SMD-C indicates an mPacket that continues the data for a preempted packet. Upon receiving an SMD
value of SMD-C, MAC checks the following:
1. A preempted packet is in progress
2. The frame count indicated by the SMD matches the frame count of the packet in progress
3. The frag_count value indicates the next fragment count.

---

*Page 2587*

Ethernet Media Access Controller (EMAC)
If any of the above check fails, the mPacket is discarded and MAC sets a CRC Error status for the partially received packet.
If all the checks pass, the next fragment count is incremented modulo 4.
When a packet is preempted, the MAC saves the state of the partially received packet (filter check status, timestamp, length fields
etc.) and will be able to process any received Express packets before the continuation fragment is received.
The MAC Receiver sends a "dummy status" for all the mPacket fragments successfully received and sends the Rx status with the
final fragment. If an error is detected during any of the fragments the Rx status is sent and the fragment is marked as final fragment.
All subsequent continuation fragments received for this packet are dropped in the MAC.
The MAC communicates the following frame type information to the MTL using a 2-bit preemption control signal.
1. Express Frame
2. Preemption Frame (Full or Fragment)
3. Continuation Fragment (non-Final or Final)
Table 505. Preemption Control Values on MRI Interface for Various Frame Types
Qualifier Preemption Control Value Frame Type
SoF 00 Start Express
SoF 01 Start Preemption
SoF 10 Continuation Fragment
SoF 11 Reserved
EoF 00 End of Frame
EoF 01 End of Fragment
72.9.12.5.1.1 Data Alignment
When a received frame cannot be fragmented on any byte boundary, MAC retains the unaligned bytes of data in the previous
fragment and resends them with the next fragment as shown in the following figure.
MRI data width - 32-, 64-, or 128-bit wide
- - - - - - - X6 X5 X4 X3 X2 X1 Packet fragmented at X3
Unaligned fragment
End of fragment
X3 X2 X1
Byte En = 2
- - - - - - - X6 X5 X4 X3 X2 X1 Start of continuation fragment
Figure 407. Data Alignment Feature of MAC

---

*Page 2588*

Ethernet Media Access Controller (EMAC)
72.9.12.5.1.2 MTL Receive Preemption
The MTL Rx must have at least 2 Rx queues to support FPE function as the preemptable packets and the express packets must
be routed to separate Rx queues. The destination Rx queue of a received packet is controlled by MAC_RxQ_Ctrl[n] registers.
Program these registers such that Preemptable traffic and express traffic are not routed to the same Rx queue. As the queue
mapping for tagged packets is based on VLAN user-priority field, this implies that priority of preemptable and express packets are
mutually exclusive. In other words, packets of a certain priority (traffic class) are either express or preemptable but cannot be both.
For the preemptable frames, in addition to the PSRQ (Priority Selected in Rx queue) based queue routing, a programmable
"Frame Preemption Residue Queue" (FPRQ) is supported to route all other Preemption Packets received (Untagged, SA/ DA or
VLAN Filter Fails forwarded due to RA being set or VTFE being reset).
Table 506. Preemption Control Values on MRI Interface for Various Frame Types
Preemption Packet Type Queue Routing
AV Tagged Packets passing the SA/DA and VLAN filters PSRQ*
DCB/Generic Tagged Packets passing the SA/DA and VLAN filters PSRQ**
Tagged Packets failing the SA/DA and VLAN filters without setting the Receive All (RA = 0) or setting Dropped
VTFE = 1
All other packets OR when RA = 1 FPRQ
72.9.12.5.1.3 MTL Receive Arbitration
On the ARI Interface, data is fetched from the MTL Rx FIFO based on the arbitration selected in the MTL_Operation_Mode
register. Frame based arbitration can be used only when all the MTL Preemption Queues are operating in Store and Forward
mode, else there will be loss of bandwidth on the ARI interface because all the fragments of a preemptable packet is not available.
Therefore, any express packets received between the fragments are blocked until all the fragments are received and transferred;
this defeats the purpose of express traffic.
When operating in either Threshold (cut through) or Store and forward modes of operation, PBL based arbitration is recommended
over Frame based arbitration. In case of PBL based arbitration, the watermark check is always performed and the arbitration/
transfer of data in terms of chunks of "PBL" size of data which is almost similar to the concepts of "fragments". Therefore the
express queue packets get blocked for less time as well as the ARI interface transfers the data without loss of efficiency.
72.9.12.6 Verify and Respond mPackets
When FPE function is present, the MAC can receive and detect the Verify and Respond mPackets, even when FPE is not enabled
by software. When MAC detects valid Verify/Respond mPackets, it notifies the software by setting the RVER and RRSP fields
of MAC_FPE_CTRL_STS register respectively. Optionally an interrupt can be generated. As such packets have empty (all-zero)
data payload, they are dropped inside the MAC and not forwarded to the MRI.
Software can set the SVER and SRSP fields of MAC_FPE_CTRL_STS register to request MAC to transmit Verify and Respond
mPackets respectively. Upon successful transmission of these frames, MAC clears the SVER/SRSP bits and sets the TVER &
TRSP fields of MAC_FPE_CTRL_STS register. Optionally an interrupt can be generated when these events occur.
Software must ensure that frame preemption verify and response packet are not triggered at the same time. Trigger either of the
packets and then wait for its completion before triggering another packet.
72.9.12.7 Frame Preemption and MMC Counter and Interrupt Registers
The following MMC counters and associated Interrupt registers are instantiated/present in the MAC when Frame Preemption
feature is enabled.

---

*Page 2589*

Ethernet Media Access Controller (EMAC)
Table 507. MMC Counters and Associated Interrupt Registers
Frame Assembly
Error Counter Description Associated MMC Counter
Frame Assembly A 32-bit counter that provides the number of MAC frames MMC_Rx_Packet_Assembly_Err_Cntr
Error Counter with reassembly errors on the Receiver, due to mismatch
in the Fragment Count value.
Frame SMD A 32-bit counter that provides the number of received MMC_Rx_Packet_SMD_Err_Cntr
Error Counter MAC frames rejected due to arriving with an SMD-C when
there was no preceding preempted frame.
Frame Assembly A 32-bit counter that provides the number of MAC frames MMC_Rx_Packet_Assembly_Ok_Cntr
OK Counter that were successfully reassembled.
MAC Rx A 32-bit counter that provides the number of additional MMC_Rx_FPE_Fragment_Cntr
Fragment Counter mPackets received due to preemption.
MAC Tx A 32-bit counter that provides the number of additional MMC_Tx_FPE_Fragment_Cntr
Fragment Counter mPackets transmitted due to preemption.
Hold Request Counter A 32-bit counter that maintains the count of number of MMC_Tx_Hold_Req_Cntr
times a hold request is given to MAC.
72.9.12.7.1 Additional Registers Associated With MMC Interrupts
Following are the additional registers associated with MMC interrupts for the MMC error counters:
• MMC_FPE_Tx_Interrupt
• MMC_FPE_Tx_Interrupt_Mask
• MMC_Rx_Interrupt
• MMC_FPE_Rx_Interrupt_Mask

#### 72.9.13 Time-Based Scheduling

Time-based scheduling feature is suitable for traffic whose periodicity and rate are predictable. To improve the quality-of-service
of such traffic,
• The transmit DMA fetches the packet from the host memory for transmission at designated time. This helps the software
to setup the Transmit descriptors in advance even before packet is ready/available. It reduces the overhead on the
software and avoids constant monitoring of the time and preparing descriptors just in time when the packet is targeted to
be transmitted.
• The MAC transmits the packet only at the designated/pre-determined time even if the packets are fetched in advance.
This helps in maintaining a constant transmission rate that can be consumed by the receiver station; therefore avoiding
congestion and excessive buffering in the network.
The time-based scheduling feature supports fetching and launching an Ethernet packet at (or after) a pre-determined time. The
time-based scheduling is supported only in the following modes/configurations:
• Full duplex mode
• Link speed is 100Mbps or higher
The following figure shows the time-based scheduling flow.

---

*Page 2590*

Ethernet Media Access Controller (EMAC)
Fetch the first enhanced normal descriptor
If LTV = 1, compute fetch time based on
launch time
If LTV = 1, wait for system time ≥
fetch time
Read packet data from system memory
and push the data into the MTL FIFO
Close the descriptor
No
LD = 1 ? Fetch the next transmit descriptor
Yes
End of descriptor
ring
Wait for transmit poll demand
Figure 408. Time-Based Scheduling Flow
Following are the definitions specific to the time-based scheduling feature:
72.9.13.1 Launch time
The time beyond which MTL can schedule the packet for transmission. The launch time resets after the transmission of one frame.
These are the two formats of the launch time.
Normal or absolute format :
In this format, the launch time is interpreted in the normal or absolute format if the ESTM bit of the MTL_TBS_CTRL register is set
to 0.
The launch time is a 32-bit value, where most-significant 8-bits represent the time in seconds and the rest 24-bits represent the
time in 256 ns. The launch time is compared against the IEEE 1588 based system or PTP time (bits[39:8]) and rolls over after
256 seconds.
The maximum value of the lower 32-bits of system time is 999,999,999 decimal (0x3B9AC9FF) and it wraps to 0 when reaching
this value (representing a full second). Therefore, the maximum value of the lower 24-bits of the launch time (after multiplying by
256) must be 0x3B9AC9.
As the maximum value of launch time is 256 seconds,
• Launch time is greater than current system time when its value is between system time[39:8] and System time[39:8] + 128
sec.

---

*Page 2591*

Ethernet Media Access Controller (EMAC)
• Launch time is less than current system time when its value is between System Time[39:8] + 128sec and System
Time[39:8] + 256 sec, because this is a modulo 256 computation.
EST or offset format
In this format, the launch time is a offset value relative to the time which the BTR of the GCL list provided in the GCL slot number
(GSN) indicates. The value in the BTR is always updated to the start time of the current loop of the GCL.
For each packet, the GSN value and the launch time value are specified in the enhanced transmit descriptor in the DMA
configurations, or as control word in the MTL configurations.
The launch time offset is a 32-bit value; the upper 8-bits represent the time in seconds and the rest 24 bits represent the time in
256 ns, which is added to the BTR value corresponding to GCL slot number. The value of the launch time offset must be smaller
than the value of the cycle time (specified in the CTR register that is implemented when EST is enabled). If the CTR is greater
than or equal to 1s, the maximum value of the lower-24 bits of launch time offset must be 999,999,999 decimal (0x3B9AC9FF).
In this format, the launch time is a 64-bit value, which is interpreted as, Launch time = Launch GSN BTR[63:0] + (Launch time
offset[31:0] << 8), which is compared with the system time [63:0].
GSN BTR is the Base Time value at which the Launch GSN loop started execution.
GCL slot number (GSN) A modulo 16 count of the GCL loop count is implemented and it is known as GSN. The count is
incremented for every new GCL loop. Installation of new GCL list does not impact the count. Read MTL_EST_Status[CGSN] to
obtain the current GCL slot number.
The maximum value of GSN is 15. Therefore the GSN values between MTL_EST_Status[CGSN] and MTL_EST_Status[CGSN] +8
represent current or future slots and all other GSN values are interpreted as elapsed slots or past slots. So, for the correct
interpretation of time, GSN value must be between MTL_EST_Status[CGSN] and MTL_EST_Status[CGSN] +8.
72.9.13.2 Launch expiry time
Normal or Absolute Mode
In this mode, when MTL_TBS_CTRL[LEOV] = 1, MTL_TBS_CTRL[LEOS] determines the maximum amount of time a frame is
eligible for launch, starting from the time the frame becomes eligible for launch.
The launch expiry offset is a 24-bit value defined in 256 ns units, with a maximum possible value of 999,999,999 ns (0x3B9AC9FF).
Launch expiry time = (Launch time[39:8] + LEOS[32:8]) * 256 ns
The packet with a specific launch time is considered as eligible for transmission when the launch time is less than the system time
and (if MTL_TBS_CTRL[LEOV] = 1) the system time is less than the launch expiry time.
When the system time is greater than the launch expiry time, the frame is categorized as expired and it drops from the MTL FIFO.
NOTE
• For correct interpretation and meaningful operation, the fetch, launch, and launch expiry time must never
set to a value larger than the Current system time + 128 sec; such a value is interpreted as time that has
already elapsed.
• In Full Duplex mode, the frames dropped from the MTL FIFO have error summary (Bit 15) and excessive
deferral (bit 3) of TxStatus set.
EST/Offset Mode
In EST mode, when MTL_TBS_CTRL[LEOV] = 1, MTL_TBS_CTRL[LEGOS] and MTL_TBS_CTRL[LEOS] determines the
maximum amount of time a frame remains eligible for launch, starting from the time the frame becomes eligible for launch.
Launch Expiry Offset = (LEGOS: LEOS)
LEGOS holds the GSN offset (multiples of CTR time) and LEOS holds maximum value of CTR (sub CTR values) in ns.
Launch expiry offset is a 24-bit value defined in the units of 256 ns, with a maximum possible value of the smaller of 999,999,999
ns or CTR-1 ns.
The Launch expiry GSN is computed as follows:

---

*Page 2592*

Ethernet Media Access Controller (EMAC)
Launch expiry GSN = (Launch GSN + LEGOS + CCMA) where, CCMA is the CTR carry due to modulo addition. This value is 1
if ((Launch time offset + LEOS) << 8) is equal to or greater than CTR.
When CCMA = 1, Launch expiry offset = (Launch time offset+ LEOS) - CTR
When CCMA = 0,
Launch expiry offset = (Launch time offset + LEOS).
Launch expiry time = Launch expiry GSN BTR [63:0] + Launch expiry time offset.
When MTL_TBS_CTRL[LEOV] is not 1, the launch expiry time is not checked.
When MTL_TBS_CTRL[LEOV] = 1, and
• the system time is greater than the launch expiry time, the frame drops from MTL FIFO. Then the frame is considered as
expired.
• the launch time is smaller than the system time and the launch expiry time is greater than the system time, then the frame
is considered eligible for launch.
NOTE
• Max value of MTL_TBS_CTRL[LEGOS] is 7. This indicates that when MTL_TBS_CTRL[LEOV] = 1, the frame
has a maximum life time of <8 GCL loop iterations after it becomes eligible for launch.
• The slot number of the first GCL list that executes each time after EST is enabled, is zero.
72.9.13.3 Fetch time
This accounts for all possible delays in the DMA fetch operation and ensures that the frame is present in the MTL FIFO before the
launch time.
If DMA_TBS_CTRL[FTOV] is 1, it indicates the fetch time for each packet. If DMA_TBS_CTRL[FTOV] is not 1, it indicates that the
fetch time offset is not valid and DMA fetches packets without any time constraints.
Normal/Absolute mode
In this mode fetch time is derived or calculated by reducing the time specified in DMA_TBS_CTRL[FTOS] from the given
launch time.
In Normal mode, the fetch launch time is computed as:
Fetch Time[39:8] = (Launch Time[39:8] - FTOS[31:8])
The fetch time is 32-bits and is compared against the system time[39:8] to determine whether it is eligible for fetching the frame:
• The fetch time is defined as greater than system time if the fetch time is in the range of system time[39:8]" and system
time[39:8] + 128 sec. The frame is considered as not-eligible for fetch.
• The fetch time is defined as smaller than the system time if the fetch time is in the range of system time[39:8] + 128 sec
and system time[39:8] + 256 sec. The frame is considered as eligible for fetch.
This is a modulo 256 computation.
EST/Offset mode
In this mode, the fetch GSN offset ( DMA_TBS_CTRL[FGOS] ) provides the slot number offset to deduct from the launch GSN. In
this case the FTOS value must be smaller than 999,999,999 ns or CTR-1 ns.
If (Launch time offset >= FTOS):
Fetch time offset = ((Launch time offset - FTOS) * 256ns)
CBFS(CTR borrow for fetch subtraction) = 0
If (Launch time offset < FTOS) Fetch time offset = CTR + ((Launch time offset - FTOS) * 256ns)

---

*Page 2593*

Ethernet Media Access Controller (EMAC)
CBFS (CTR borrow for fetch subtraction) = 1
The fetch GSN is computed as follows:
Fetch GSN = Launch GSN - FGOS - CBFS
Fetch time = Fetch GSN BTR[63:0] + Fetch time offset
The frame is eligible for DMA fetch when the fetch time is smaller than the system time.
DMA operations sequence
This is the sequence of operation when FTOV = 1:
1. Fetch the first enhanced normal descriptor (FD = 1).
2. Compute the fetch time on the basis of the launch time and wait for the system time to be greater than fetch time, if LTV
= 1 and fetch is enabled.
3. Read the frame (data) from the host memory and transfer to MTL FIFO.
4. Close the normal descriptor.
5. Fetch the next normal descriptor (if the previous descriptor was not the last).
6. Repeat steps 4 to 6, until the last descriptor of the frame (LD = 1).
After the last descriptor of a frame, program the next enhanced normal descriptor with a new launch time and with LTV = 1.
Otherwise, process the subsequent frames without any time restrictions.
See Programming the launch time in time-based scheduling for more information.

#### 72.10 TCP/IP Offloading Features

This section and all other sections, along with respective sub-sections are Synopsys Proprietary. Used with permission.

#### 72.10.1 Transmit Checksum Offload Engine

The MAC has a Checksum Offload Engine (COE) to support checksum calculation and insertion in the Transmit path, using which,
the software can offload the task of checksum insertion to the hardware. In the transmit path MAC calculates the checksum and
inserts it in the Tx packet. This feature helps in reducing the load on the software and can improve the overall throughput of the
system. This feature is supported for only Q0 queue.
The checksum offload engine module supports two types of checksum calculation and insertion. The checksum engine can be
controlled for each packet by setting the CIC bits (TDES3 Bits[17:16]).
Note: The checksum for TCP, UDP, or ICMP is calculated over a complete packet, and then inserted into its corresponding header
field. Because of this requirement, when this function is enabled, the Tx FIFO automatically operates in the store-and-forward
mode even if IP is configured for Threshold (cut-through) mode.
72.10.1.1 IP Header Checksum Engine
In IPv4 datagrams, the integrity of the header fields is indicated by the 16-bit Header Checksum field (the eleventh and twelfth
bytes of the IPv4 datagram). The COE detects an IPv4 datagram when the Type field of Ethernet packet has the value 0x0800
and the Version field of IP datagram has the value 0x4. The checksum field of the input packet is ignored during calculation and
replaced with the calculated value.
NOTE
IPv6 headers do not have a checksum field. Therefore, the COE does not modify the IPv6 header fields.
The result of this IP header checksum calculation is indicated by the IP Header Error status bit in the Transmit status (Bit 0 in Table
19-12 on page 1330). This status bit is set whenever the values of the Ethernet Type field and the Version field of IP header are
not consistent, or when the Ethernet packet does not have enough data, as indicated by the IP header Length field. In other words,
this bit is set when an IP header error is asserted under the following circumstances:

---

*Page 2594*

Ethernet Media Access Controller (EMAC)
• For IPv4 datagrams:
— The received Ethernet type is 0x0800, but the Version field of IP header is not equal to 0x4.
— The IPv4 Header Length field indicates a value less than 0x5 (20 bytes).
— The total packet length is less than the value given in the IPv4 Header Length field.
• For IPv6 datagrams:
— The Ethernet type is 0x86dd but the IP header Version field is not equal to 0x6.
— The packet ends before the IPv6 header (40 bytes) or extension header (as given in the corresponding Header
Length field in an extension header) is completely received.
72.10.1.2 TCP/UDP/ICMP Checksum Engine
The TCP/UDP/ICMP Checksum Engine processes the IPv4 or IPv6 header (including extension headers) and determines
whether the encapsulated payload is TCP, UDP, or ICMP. The checksum is calculated for the TCP, UDP, or ICMP payload and
inserted into its corresponding field in the header. The Tx COE can work in the following two modes:
• The TCP, UDP, or ICMPv6 pseudo-header is not included in the checksum calculation and is assumed to be present in
the Checksum field of the input packet. This engine includes the Checksum field in the checksum calculation, and then
replaces the Checksum field with the final calculated checksum.
• The engine ignores the Checksum field, includes the TCP, UDP, or ICMPv6 pseudo-header data into the checksum
calculation, and overwrites the checksum field with the final calculated value.
NOTE
For ICMP-over-IPv4 packets, the Checksum field in the ICMP packet must always be 16'h0000 in both modes,
because pseudo-headers are not defined for such packets. If it does not equal 16’h0000, an incorrect checksum
may be inserted into the packet.
The result of this operation is indicated by the Payload Checksum Error status bit in the Transmit Status vector (Bit 12 in Table
19-3 on page 1324). This engine sets the Payload Checksum Error status bit when it detects that the packet has been forwarded
to the MAC Transmitter engine in the store-and-forward mode without the end of packet (EOP) being written to the FIFO, or when
the packet ends before the number of bytes indicated by the Payload Length field in the IP Header is received. When the packet is
longer than the indicated payload length, the COE ignores them as stuff bytes, and no error is reported. When this engine detects
the first type of error, it does not modify the TCP, UDP, or ICMP header. For the second error type, it still inserts the calculated
checksum into the corresponding header field. Following table describes the functions supported by Transmit Checksum Offload
engine based on the packet type. When the MAC does not insert the checksum, it is indicated as "No" in the table.
NOTE
You should not enable checksum insertion for IPv4 or IPv6 packets that are greater than the frame size constraint
specified in Description of Transmit Checksum Offload Engine because it may result in incorrect checksum
insertion or unexpected behavior.
Table 508. Transmit Checksum Offload Engine Functions for Different Packet Types
Hardware IP Hardware TCP/
Packet Type headerchecksum insertion UDPchecksum insertion
Non-IPv4 or IPv6 packet No No
IPv4 packet is greater than 1,522 bytes (2,000 bytes when Yes Yes
IEEE 802.3ad Support for 2K Packets is enabled in MAC) but
less than or equal to the frame size constraint specified in
Transmit Checksum Offload Engine .
Table continues on the next page...

---

*Page 2595*

Ethernet Media Access Controller (EMAC)
Table 508. Transmit Checksum Offload Engine Functions for Different Packet Types (continued)
Hardware IP Hardware TCP/
Packet Type headerchecksum insertion UDPchecksum insertion
IPv6 packet is greater than 1,522 bytes (2,000 bytes when Not Applicable Yes
IEEE 802.3ad Support for 2K Packets is enabled in MAC) but
less than or equal to the frame size constraint specified in
Transmit Checksum Offload Engine .
IPv4 with TCP, UDP, or ICMP Yes Yes
IPv4 packet has IP options (IP header is longer than 20 bytes) Yes Yes
Packet is an IPv4 fragment Yes No
IPv6 packet with the following next header fields in main or • Not Applicable • Yes
extension headers:
• Not Applicable • No
• Hop-by-hop options (in IPv6 main header)
• Not Applicable • Yes
• Hop-by-hop options (in IPv6 extension header)
• Not Applicable • No
• Destinations options
• Not Applicable • No
• Routing (with segment left 0)
• Not Applicable • Yes
• Routing (with segment left > 0)
• Not Applicable • No
• TCP, UDP, or ICMP
• Not Applicable • No
• Authentication
• Any other next header field in main or extension headers
IPv4 packet has TCP header with Options fields Yes Yes
IPv4 Tunnels: • Yes (IPv4 tunnel header) • No
• IPv4 packet in an IPv4 tunnel • Yes (IPv4 tunnel header) • No
• IPv6 packet in an IPv4 tunnel
IPv6 Tunnels: • Not applicable • No
• IPv4 packet in an IPv6 tunnel • Not applicable • No
• IPv6 packet in an IPv6 tunnel
IPv4 packet has 802.3ac tag (with C-VLAN Tag or S-VLAN Tag Yes Yes
when enabled).
IPv6 packet has 802.3ac tag (with C-VLAN Tag or S-VLAN Tag Not applicable Yes
when enabled).
IPv4 frames with security features (such as encapsulated Yes No
security payload)
IPv6 frames with security features (such as encapsulated Not applicable No
security payload)

---

*Page 2596*

Ethernet Media Access Controller (EMAC)

#### 72.10.2 Receive Checksum Offload Engine

IP provides the Checksum Offload Engine that is used to detect any error in an IPv4 or IPv6 packet in the receive path. The MAC
verifies the checksum field of the received packet with the internally calculated checksum and provides the status.
The Receive Checksum Offload engine is used to detect errors in IP packets by calculating the header checksum and further
matching it with the received header checksum. This engine also identifies a TCP, UDP, or ICMP payload in received IP packets
and calculates the checksum of such payloads appropriately.
Here, both IPv4 and IPv6 packet in the received Ethernet packets are detected and processed for data integrity. The MAC receiver
identifies IPv4 or IPv6 packets by checking for value 0x0800 or 0x86DD, respectively, in the Type field of the received Ethernet
packet. This identification is applicable to single VLAN-tagged packets. It is also applicable to double VLAN-tagged packets when
the Enable Double VLAN Processing option is selected and the EDVLP bit of the MAC_VLAN_Tag register is set.
The Rx COE calculates the IPv4 header checksums and checks that they match the received IPv4 header checksums. The result
of this operation (pass or fail) is given to the RFC module for insertion into the receive status word. The IP Header Error bit is set for
any mismatch between the indicated payload type (Ethernet Type field) and the IP header version, or when the received packet
does not have enough bytes, as indicated by the Length field of the IPv4 header (or when fewer than 20 bytes are available in an
IPv4 or IPv6 header).
This engine also identifies a TCP, UDP, or ICMP payload in the received IP datagrams (IPv4 or IPv6) and calculates the checksum
of such payloads properly, as defined in the TCP, UDP, or ICMP specifications. This engine includes the TCP, UDP, or ICMPv6
pseudo-header bytes for checksum calculation and checks whether the received checksum field matches the calculated value.
The result of this operation is given as a Payload Checksum Error bit in the receive status word. This status bit is also set if the
length of the TCP, UDP, or ICMP payload does not match the expected payload length given in the IP header.
Following table describes the functions supported by the Rx COE based on the packet type. When the payload of an IP packet is
not processed (indicated as "No" in the table), the information (whether the checksum engine is bypassed or not) is given in the
receive status.
NOTE
The MAC does not append any payload checksum bytes to the received Ethernet packets.
Table 509. Receive Checksum Offload Engine Functions for Different Packet Types
Hardware TCP/UDP/ICMP
Packet type Hardware IP header checksum checking checksum checking
Non-IPv4 or IPv6 No No
IPv4 packet is greater than 1,522 Yes Yes
bytes (2,000 bytes when IEEE
802.3ad Support for 2K Packets is
enabled in the MAC)
IPv6 packet is greater than 1,522 Not Applicable Yes
bytes (2,000 bytes when IEEE
802.3ad Support for 2K Packets is
enabled in the MAC)
IPv4 with TCP, UDP, or ICMP Yes Yes
IPv4 header's protocol field contains Yes No
a protocol other than TCP, UDP,
or ICMP
Table continues on the next page...

---

*Page 2597*

Ethernet Media Access Controller (EMAC)
Table 509. Receive Checksum Offload Engine Functions for Different Packet Types (continued)
Hardware TCP/UDP/ICMP
Packet type Hardware IP header checksum checking checksum checking
IPv4 packet has IP options (IP header Yes Yes
is longer than 20 bytes)
Packet is an IPv4 fragment Yes No
IPv6 packet with the following • Not Applicable • Yes
next header fields in main or
• Not Applicable • No
extension headers:
• Not Applicable • Yes
• Hop-by-hop options (in IPv6
main header) • Not Applicable • Yes
• Hop-by-hop options (in IPv6 • Not Applicable • No
extension header)
• Not Applicable • Yes
• Destinations options
• Not Applicable • No
• Routing (with segment left 0)
• Routing (with segment left > 0)
• TCP, UDP, or ICMP
• Any other next header field in
main or extension headers
IPv4 packet has TCP header with Yes Yes
Options fields
IPv4 Tunnels: • Yes (IPv4 tunnel header) • No
• IPv4 packet in an IPv4 tunnel • Yes (IPv4 tunnel header) • No
• IPv6 packet in an IPv4 tunnel
IPv4 Tunnels: • Not Applicable • No
• IPv4 packet in an IPv6 tunnel • Not Applicable • No
• IPv6 packet in an IPv6 tunnel
IPv4 packet has 802.3ac tag (with Yes Yes
C-VLAN Tag or S-VLAN Tag
when enabled)
IPv6 packet has 802.3ac tag (with Not Applicable Yes
C-VLAN Tag or S-VLAN Tag
when enabled)
IPv4 frames with security Yes No
features (such as encapsulated
security payload)
Table continues on the next page...

---

*Page 2598*

Ethernet Media Access Controller (EMAC)
Table 509. Receive Checksum Offload Engine Functions for Different Packet Types (continued)
Hardware TCP/UDP/ICMP
Packet type Hardware IP header checksum checking checksum checking
IPv6 frames with security Not Applicable No
features (such as encapsulated
security payload)
Set the IPC bit of the MAC_Configuration register for enabling Receive Checksum offload.

#### 72.11 MAC Management Counters

This section is Synopsys Proprietary. Used with permission.
IP supports storing the statistics about the received and transmitted packets in registers that are accessible through the
application/software. The MMC module maintains a set of registers for gathering statistics on the received and transmitted
packets. The MMC counters are free running.
For MMC register details, see registers "MMC_Control" to "MMC_Rx_FPE_Fragment_Cntr" in EMAC register descriptions .

#### 72.12 Flow Control

This section and its sub-sections are Synopsys Proprietary. Used with permission.

#### 72.12.1 Transmit Flow Control

The Transmit Flow Control involves transmitting Pause packets in full-duplex mode and backpressure in half-duplex mode to
control the flow of packets from the remote end.
72.12.1.1 Flow control in Full duplex mode
IP supports the IEEE802.3x Pause Packets.
Pause packet structure is shown in the following table.
Table 510. Pause Packet Fields
Field Description
DA Contains the special multicast address
SA Contains the MAC address 0
Type Contains 8808
MAC Control opcode Contains 0001 for IEEE 802.3x Pause Control packets; 0101 for PFC packets
PT Contains Pause time specified in the PT field of the MAC_Q#_Tx_Flow_Ctrl register
72.12.1.2 Pause Packet Control
When the FCB bit is set, the MAC generates and transmits a single Pause packet. If the FCB bit is set again after the Pause packet
transmission is complete, the MAC sends another Pause packet irrespective of whether the pause time is complete or not. To
extend the pause or terminate the pause prior to the time specified in the previously-transmitted Pause packet, the application
should program the Pause Time register with appropriate value and then again set the FCB bit.

---

*Page 2599*

Ethernet Media Access Controller (EMAC)
72.12.1.3 Flow Control in Half-Duplex Mode
In half-duplex mode, the MAC uses the deferral mechanism for the flow control (backpressure). When the application requests to
stop receiving packets, the MAC sends a JAM pattern of 32 bytes when it senses a packet reception, provided the transmit flow
control is enabled. This results in a collision and the remote station backs off. If the application requests a packet to be transmitted,
it is scheduled and transmitted even when the backpressure is activated. If the backpressure is kept activated for a long time (and
more than 16 consecutive collision events occur), the remote stations abort the transmission because of excessive collisions.
Following table describes the flow control in the Tx path for Queue 0 based on the setting of the following bits:
• EHFC bit of MTL_RxQ0_Operation_Mode register
• TFE bit of MAC_Q0_Tx_Flow_Ctrl register
• DM bit of MAC_Configuration register
Flow control is similar for all queues.
Table 511. Tx MAC Flow Control
EHF
C TFE DM Description
x 0 x The MAC transmitter does not perform the flow control or backpressure operation.
0 1 0 The MAC transmitter performs back-pressure when Bit 0 of MAC_Q0_Tx_Flow_Ctrl register is set or the
sideband signal sbd_flowctrl_i is 1.
1 1 0 The MAC transmitter performs back-pressure when Bit 0 of MAC_Q0_Tx_Flow_Ctrl register is set or the
sideband signal sbd_flowctrl_i is 1. In addition, the MAC Tx performs back-pressure when Rx Queue level
crosses the threshold set by Bits[10:8] of MTL_RxQ0_Operation_Mode register.
0 1 1 The MAC transmitter sends the Pause packet when Bit 0 of MAC_Q0_Tx_Flow_Ctrl register is set or the
sideband signal sbd_flowctrl_i is 1.
1 1 1 The MAC transmitter sends the Pause packet when Bit 0 of MAC_Q0_Tx_Flow_Ctrl register is set or the
sideband signal sbd_flowctrl_i is 1. In addition, the MAC Tx sends a Pause packet when Rx Queue level
crosses the threshold set by Bits[10:8] of MTL_RxQ0_Operation_Mode register.
72.12.1.4 Enabling Transmit Flow Control
To independently enable Transmit Flow Control for each Tx queue, set the TFE bit in the MAC_Q#_Tx_Flow_Ctrl register.

#### 72.12.2 Receive Flow Control

In the Receive path, the Flow Control is functional only in the full-duplex mode. If any Pause packet is received in the half-duplex
mode, the packet is considered as a normal control packet.
NOTE
Receive pause packets should have a frame size of 64 bytes.
The Receive Flow Control is implemented by the MAC based on the bit value of the respective register, and the destination
address and different fields of the received packet.
Following table describes the flow control in the Rx path based on the setting of the following bits:
• RFE bit of MAC_Rx_Flow_Ctrl register
• DM bit of MAC_Configuration register

---

*Page 2600*

Ethernet Media Access Controller (EMAC)
Table 512. Rx MAC Flow Control
RFE DM Description
0 x The MAC receiver does not detect the received Pause packets.
1 0 The MAC receiver does not detect the received Pause packets but recognizes such packets as
Control packets.
1 1 The MAC receiver detects or processes the Pause packets and responds to such packets by stopping the
MAC transmitter.
72.12.2.1 Enabling Receive Flow Control
To enable Pause Flow Control, set the RFE bit in the MAC_Rx_Flow_Ctrl register.

#### 72.13 Loopback Mode

This section is Synopsys Proprietary. Used with permission.
The MAC supports Loopback of transmitted packets to its receiver.
The following are some guidelines for using the loopback mode:
• Enable loopback only with the full-duplex mode. In half-duplex mode, the carrier sense signal (crs) or collision (col) signal
inputs get sampled which may result into issues such as packet dropping.
• If the loopback mode is enabled without connecting a PHY chip (for example, in FPGA setup), you should externally
generate the Tx and Rx clocks and provide these clocks to the MAC.
• Do not loop back big packets. Big packets (>1522 bytes) may get corrupted in the loopback FIFO.
To enable this feature, program the LM bit of the MAC_Configuration register.
You can enable loopback for all PHY interfaces. The data is always looped back through internal asynchronous FIFO on to the
internal Receive MII or GMII interface, irrespective of which PHY interface is selected. The loopback data is also passed through
the corresponding transmit PHY interface block, on to the Ethernet line.

#### 72.14 Automotive Safety Features

This section and all its sub-sections are Synopsys Proprietary. Used with permission.
IP supports the automotive safety feature.

#### 72.14.1 Error Correction Code (ECC) Protection for Memories

The Error Correction Code (ECC) block can correct single-bit error and detect double-bit error.
At the write interface, ECC checkbits are generated by computing ECC on the contents of the data bus and respective address
is appended with the data that is written to the memory.
At the read interface, ECC checkbits are recomputed on the content of the read data and the respective address is compared with
the received checkbits in the memory.
Following figure shows the block diagram of ECC protection for memories

---

*Page 2601*

Ethernet Media Access Controller (EMAC)
Module single-port SRAMs
Write Read
interface interface
ECC check bits ECC error
generation detection/correction
EMAC
Figure 409. Block Diagram of ECC Protection for Memories
72.14.1.1 Handling the Address Mismatch
The ECC is calculated over the sum of memory data and memory location address. However only data is written in the memory
along with the ECC(address is excluded).
On the read interface, the ECC is checked over the sum of memory DATA, memory ECC, and Internally generated address. When
an ECC correctable error is detected over the range of the address bit position, it is treated as an uncorrectable error. This is
because data might have been read from a different location.
72.14.1.2 Diagnostic Support for the Error Management
Following statistics are provided for monitoring the error behavior on each of the memory blocks.
• Error status provided to management
— A separate status for correctable, uncorrectable, and address mismatch is specified in the MTL_EC-
C_Interrupt_Status register.
— Memory locations at which correctable and uncorrectable errors are detected is specified in the
MTL_ECC_Err_Addr_Status register. In addition, a control bit (MEEAO field of MTL_ECC_Control register) decides,
whether the first errored memory address is reported or the latest errored memory address is reported.
— MTL_ECC_Err_Cntr_Status register has separate counters to count the number of correctable and uncorrectable
errors
• Interrupts provided to management
— Separate interrupts are generated for correctable and uncorrectable errors.
— sbd_sfty_ue_intr_o interrupt is generated when uncorrectable errors are detected and these errors cannot be
masked.
— sbd_sfty_ce_intr_o interrupt is generated when correctable errors are detected. The module sets the respective
interrupt enable bits (in the DMA/MTL_ECC_Interrupt_enable register).
— To find the root cause of the error, read the DMA/MTL_Safety_Interrupt_Status and DMA/MTL_ECC_Interrupt_Status
registers.
— To clear the interrupt, write 1 in the respective interrupt status bit in DMA/MTL_ECC_Interrupt_Status registers.
NOTE
• The status/counters/interrupts are generated per memory.
• MTL Tx and Rx memory is logically divided into multiple queues. But, ECC diagnostics (status/counter/
interrupts) are common to all queues.

---

*Page 2602*

Ethernet Media Access Controller (EMAC)
72.14.1.3 ECC Error Injection Capabilities
IP supports error injection capabilities for each memory as a static configuration. The position where the errors are injected in the
data word is random. For each memory, 3 control bits are provided to inject errors. The 3 bits are:
• A single bit to enable error injection
• Two bits to indicate the type of error to be injected ❑ 00: 1 bit error
— 01: 2 bit error
— 10: 3 bit error
— 11: 1 bit error in address field
The control bit descriptions for
• MTL Tx/Rx and DMA TSO memory are specified in the MTL_DBG_CTL register, for and for R.
• MTL EST memory are specified in the MTL_EST_GCL_Control register
• Rx parser memory are specified in the MTL_RXP_Indirect_Acc_Control_Status register
NOTE
While using debug mode for ECC error injection:
• There should be no traffic in the module
• When multiple CSR writes are required for writing single data word into the memory, the application should
ensure that all the CSR writes corresponding to one memory write maintains the same value for the error
injection control word.
• See Programming guidelines for ECC protection for memories

#### 72.14.2 Finite State Machine(FSM) Parity and Timeout Protection

The FSM protection feature supports FSM parity and time out protection.
72.14.2.1 FSM State Parity Protection
Odd parity is implemented on all the FSM state register bits. Parity is monitored for every clock, after the reset is de-asserted.
When a bit flips due to transient errors or permanent faults, the erroneous FSM is set to its default state and an uncorrectable error
is indicated.
When the FSM state parity protection is enabled, by setting PRTYEN field of MAC_FSM_Control Register, the FSM state
error is indicated by setting the FSMPES field of the MAC_DPP_FSM_Interrupt_Status register. Also, the Safety Interrupt
(sbd_sfty_ue_intr_o) is asserted when an FSM Error is detected.
Error Injection mode is also supported for FSM parity error check. Program the Error Injection enable for the respective clock
domain denoted by [23:16] bits of MAC_FSM_Control Register.
72.14.2.2 FSM Timeout Protection
The FSM Timeout feature provides a mechanism to ensure that all FSMs in the module can complete a transaction and return
to a known completion state (IDLE or any other state that indicates completion of a transaction/transfer) within the programmed
timeout value.
Program the TMR field of MAC_FSM_ACT_Timer register with a value that indicates the number of CSR cycles required to
generate a 1 microsecond tic. This microsecond tic is internally used to generate the programmed timeout duration. Two timeout
tics (normal mode timeout and large mode timeouts) are generated to provide flexibility to choose one per clock domain, by using
the bits [31:24] of MAC_FSM_Control Register. Supported values for Timeout duration are 1us, 1.024ms, 16.384ms, 65.5536ms,
262.144ms, 1048.576ms (~1sec), 4194.304ms (~4s), 8.388s, 16.777s, and 33.554s which is based on the programmable bits
NTMRMD, LTMRMD fields of MAC_FSM_ACT_Timer register. Interface timeouts are based on the normal mode tic generation;
only the FSM timeouts depend on either normal or large tick selection.

---

*Page 2603*

Ethernet Media Access Controller (EMAC)
Figure 410. FSM Timeout Timing Diagram
In the above figure, the timer ticks are generated based on the programmable timeout value. The timer ticks are synchronized and
made available in all the clock domains that have the FSMs.
At every timer tick, all the FSMs are monitored for being in active state (non-IDLE state, which indicates the FSM is actively
processing transactions or hand-shakes) and an FSM active flag bit is set. The active flag bit is implemented for every FSM that
is monitored for timeout. When the FSM reaches an IDLE or transaction completion state, the active flag bit of that FSM is reset.
At the subsequent timer tick, all the FSMs’ active flag bits are monitored and any flag that is set indicates a timeout for that FSM.
This process of setting the flag and checking at subsequent timer tick is repeated at every tick.
Timeout Error Injection per clock domain is enabled by programming [15:8] fields of MAC_FSM_Control register. When Timeout
error injection enable is set for a clock domain, the FSMs in that clock domain automatically timeout and generate an interrupt even
without traffic. As error injection is a debug mode, TMR value need not indicate 1us, but can be programmed to a smaller value
at which debug mode ticks are needed. FSM Timeout and Parity Error Injection modes can be used for testing at key-on/key-off.
The FSM timeout status is specified in the [15:8] field of MAC_DPP_FSM_Interrupt_Status register as per the respective clock
domains. One FSM timeout status bit is made available per clock domain. The safety interrupt (sbd_sfty_ue_intr_o) is asserted
when the timeout error is set for any of the clock domains. IP does not attempt to recover from a FSM timeout condition and relies
on the application to take the corrective action (resetting IP).
72.14.2.3 Enabling FSM Parity and Timeout Protection
FSM timeout feature is enabled by setting the TMOUTEN field of MAC_FSM_Control register.
NOTE
See Programming guidelines for FSM parity and timeout

#### 72.14.3 Application/CSR Interface Timeout Protection

This feature provides timeout protection to the application/CSR Interface. All the interfaces which has the handshake mechanism
monitored for potential hangs due to the external agent (Master/Slave/Interconnect/Application client) not responding to the
requests/transfers initiated by the QoS. After the request is initiated the response arrival interval is monitored. If the response does
not arrive within a programmed time (TMR field of MAC_FSM_ACT_Timer) the timeout is triggered, using similar trigger generation
as explained in the Section FSM protection.
The timeout is triggered when IP initiates SEQ, NON-SEQ and BUSY transfers on HTRANS[1:0]; and HREADY is not asserted
by Slave within the programmed time.
The Timeout status for the AHB master interface is set in the MSTTES field of the MAC_DPP_FSM_Interrupt_Status register. The
Safety interrupt (sbd_sfty_ue_intr_o) is asserted when application timeout status is set.

---

*Page 2604*

Ethernet Media Access Controller (EMAC)
The hardware does not attempt to recover from Application/CSR Interface hangs and relies on software to take appropriate
corrective action.
NOTE
• Protection is not needed for APB3 Slave interfaces because potential hangs can be only when IP does not
respond to the requests. In such cases the IP internal protection logic (FSM timeout) detects such defects.
• Based on the time at which the transfer is initiated relative to the timer ticks, the timeout period could be any
value between the programmed timeout and 2x times the programmed timeout.
• When an uncorrectable safety interrupt is issued and the read of the Interrupt status register returns all zeros,
it implies that the CSR read is not functional. As soft reset of IP is not possible without the CSR access, and
therefore, a hard reset of IP is recommended.
• See Programming guidelines for FSM parity and timeout .

#### 72.15 Descriptors

This section and all its sub-sections are Synopsys Proprietary. Used with permission.

#### 72.15.1 Overview

The DMA in the Ethernet subsystem transfers data based on a linked list of descriptors. The application creates the descriptors
in the system memory. The module supports the following two types of descriptors:
• Normal Descriptor: Normal descriptors are used for packet data and to provide control information applicable to the packets
to be transmitted.
• Context Descriptor: Context descriptors are used to provide control information applicable to the packet to be transmitted.
Each normal descriptor contains two buffers and two address pointers. These buffers enable the adapter port to be compatible
with various types of memory management schemes.
NOTE
There is no limit for the number of descriptors that can be used for a single packet.

#### 72.15.2 Descriptor Structure

The module supports the ring structure for DMA descriptor as shown in the following figure.

---

*Page 2605*

Ethernet Media Access Controller (EMAC)
Ring structure
Buffer 1
Descriptor 0
Buffer 2
Buffer 1
Descriptor 1
Buffer 2
Buffer 1
Descriptor 2
Buffer 2
Buffer 1
Descriptor n
Buffer 2
Figure 411. Descriptor Ring Structure
In Ring structure, descriptors are separated by the Word, DWord, or LWord number programmed in the DSL field of the
DMA_CH#_Control register. The application needs to program the total ring length, that is, the total number of descriptors in ring
span in the following registers of a DMA channel:
• Transmit Descriptor Ring Length Register (DMA_CH#_TxDesc_Ring_Length)
• Receive Descriptor Ring Length Register (DMA_CH#_RxDesc_Ring_Length)
The Descriptor Tail Pointer Register contains the pointer to the descriptor address (N). The base address and the current
descriptor pointer decide the address of the current descriptor that the DMA can process. The descriptors up to one location less
than the one indicated by the descriptor tail pointer (N – 1) are owned by the DMA. The DMA continues to process the descriptors
until the following condition occurs:
Current Descriptor Pointer == Descriptor Tail Pointer;
The DMA goes into the Suspend mode when this condition occurs. The application must perform a write to the Descriptor Tail
pointer register and update the tail pointer so that the following condition is true:
Current Descriptor Pointer < Descriptor Tail Pointer;
The DMA automatically wraps around the base address when the end of ring is reached, as shown in the following figure.

---

*Page 2606*

Ethernet Media Access Controller (EMAC)
Descriptor base address
Current descriptor pointer
to start the ring
Last descriptor that DMA owns
Descriptor tail pointer Descriptor ring length
(number of descriptors = 10)
Wrap around the base address
Application owns DMA owns
Figure 412. DMA Descriptor Ring
For descriptors owned by the application, the OWN bit of DES3 is reset to 0. For descriptors owned by the DMA, the OWN bit is
set to 1. If the application has only one descriptor in the beginning, the application sets the last descriptor address (tail pointer) to
Descriptor Base Address + 1. The DMA processes the first descriptor and then waits for the application to advance the tail pointer.

#### 72.15.3 Transmit Descriptor

The DMA in the module requires at least one descriptor for a transmit packet. In addition to two buffers, two byte-count buffers, and
two address pointers, the transmit descriptor has control fields which can be used to control the MAC operation on per-transmit
packet basis. The Transmit Normal descriptor has two formats: Read format and Write-Back format
NOTE
TSO Split header is not supported.
Software must not write same tail pointer addresses when DMA is in Suspended state before setting new descriptor(s) for
further processing.
72.15.3.1 Transmit Normal Descriptor (Read Format)
Following figure shows the Read Format for a Transmit normal descriptor. Table: 'TDES0 Normal Descriptor (Read Format)'
through Table: 'TDES3 Normal Descriptor (Read Format)' describe the read format for the Transmit Normal Descriptors: TDES0,
TDES1, TDES2, and TDES3.

---

*Page 2607*

Ethernet Media Access Controller (EMAC)
31 0
TDES0 Header or buffer 1 address [31:0]
TDES1 Buffer 2 address [31:0] or buffer 1 address [63:32]
T
I
T
TDES2 O Buffer 2 length [29:16] VTIR Header or buffer 1 length [13:0]
S
C
E
O Control [30:16] Frame length [14:0]
TDES3 W
N Control [30:18] Payload length [17:0]
Figure 413. Transmit Descriptor Read Format
72.15.3.1.1 TDES0 Normal Descriptor (Read Format)
Table 513. TDES0 Normal Descriptor (Read Format)
Bit Name Description
31:0 BUF1AP Buffer 1 Address Pointer or TSO Header Address Pointer
These bits indicate the physical address of Buffer 1. These bits indicate the TSO Header Address pointer
when the following bits are set:
• TSE bit of TDES3
• FD bit of TDES3
72.15.3.1.2 TDES1 Normal Descriptor (Read Format)
Table 514. TDES1 Normal Descriptor (Read Format)
Bit Name Description
31:0 BUF2AP Buffer 2 or Buffer 1 Address Pointer
This bit indicates the physical address of Buffer 2 when a descriptor ring structure is used. There is no
limitation for the buffer address alignment.
In 40- or 48-bit addressing mode, these bits indicate the most-significant 8- or 16- bits of the Buffer 1
Address Pointer.
72.15.3.1.3 TDES2 Normal Descriptor (Read Format)
Table 515. TDES2 Normal Descriptor (Read Format)
31 30 29-16 15:14 13:0
IOC TTSE/ TMWD B2L VTIR HL or B1L

---

*Page 2608*

Ethernet Media Access Controller (EMAC)
Table 516. TDES2 Normal Descriptor (Read Format)
Bits Name Description
31 IOC Interrupt on Completion
This bit controls the setting of TI and ETI status bits in the DMA_CH#_Status register. When ETIC = 1 and
TDES2[LD] = 0, this bit sets the ETI bit. When TDES3[LD] = 1, this bit sets the TI status bit.
30 TTSE/ Transmit Timestamp Enable or External TSO Memory Write Enable
TMWD
This bit enables the IEEE1588 time stamping for Transmit packet referenced by the descriptor, if TSE bit is
not set.
If TSE bit is set and external TSO memory is enabled, setting this bit disables external TSO memory writing
for this packet.
29:16 B2L Buffer 2 Length
The driver sets this field. When set, this field indicates Buffer 2 length.
15:14 VTIR VLAN Tag Insertion or Replacement
These bits request the MAC to perform VLAN tagging or untagging before transmitting the packets. The
application must set the CRC Pad Control bits appropriately when VLAN Tag Insertion, Replacement, or
Deletion is enabled for the packet. The following list describes the values of these bits:
• 2'b00: Do not add a VLAN tag.
• 2'b01: Remove the VLAN tag from the packets before transmission. This option should be used only
with the VLAN packets.
• 2'b10: Insert a VLAN tag with the tag value programmed in the MAC_VLAN_Incl register or
context descriptor.
• 2'b11: Replace the VLAN tag in packets with the tag value programmed in the MAC_VLAN_Incl register
or context descriptor. This option should be used only with the VLAN packets.
These bits are valid when the Enable SA and VLAN Insertion on Tx option is selected while configuring
the core.
13:0 HL or Header Length or Buffer 1 Length
B1L
For Header length only bits [9:0] are taken. The size 13:0 is applicable only when interpreting buffer 1 length.
If the TCP Segmentation Offload feature is enabled through the TSE bit of TDES3, this field is equal to
the header length. When the TSE bit is set in TDES3, the header length includes the length in bytes from
Ethernet Source address till the end of the TCP header. The maximum header length supported for TSO
feature is 1023 bytes. The maximum header length supported for TSO feature is 1023 bytes.
If the TCP Segmentation Offload feature is not enabled, this field is equal to Buffer 1 length.
72.15.3.1.4 TDES3 Normal Descriptor (Read Format)
Table 517. TDES2 Normal Descriptor (Read Format)
31 30 29 28 27:26 25:23 22:19 18 17:16 15 14:0
OWN CTXT FD LD CPC SAIC SLOTNUM or THL TSE CIC/TPL TPL FL/TPL

---

*Page 2609*

Ethernet Media Access Controller (EMAC)
Table 518. TDES3 Normal Descriptor (Read Format)
Bits Name Description
31 OWN Own Bit
When this bit is set, it indicates that the DMA owns the descriptor. When this bit is reset, it indicates that
the application owns the descriptor. The DMA clears this bit after it completes the transfer of data given
in the associated buffer(s).
30 CTXT Context Type
This bit should be set to 1'b0 for normal descriptor.
29 FD First Descriptor
When this bit is set, it indicates that the buffer contains the first segment of a packet.
28 LD Last Descriptor
When this bit is set, it indicates that the buffer contains the last segment of the packet. When this bit is
set, the B1L or B2L field should have a non-zero value.
27:26 CPC CRC Pad Control
This field controls the CRC and Pad Insertion for Tx packet. This field is valid only when the first descriptor
bit (TDES3[29]) is set. The following list describes the values of Bits[27:26]:
• 2'b00: CRC and Pad Insertion
The MAC appends the cyclic redundancy check (CRC) at the end of the transmitted packet of length
greater than or equal to 60 bytes. The MAC automatically appends padding and CRC to a packet with
length less than 60 bytes.
• 2'b01: CRC Insertion (Disable Pad Insertion)
The MAC appends the CRC at the end of the transmitted packet but it does not append padding. The
application should ensure that the padding bytes are present in the packet being transferred from the
Transmit Buffer, that is, the packet being transferred from the Transmit Buffer is of length greater than or
equal to 60 bytes.
• 2'b10: Disable CRC Insertion
The MAC does not append the CRC at the end of the transmitted packet. The application should ensure
that the padding and CRC bytes are present in the packet being transferred from the Transmit Buffer.
• 2'b11: CRC Replacement
The MAC replaces the last four bytes of the transmitted packet with recalculated CRC bytes. The
application should ensure that the padding and CRC bytes are present in the packet being transferred
from the Transmit Buffer.
This field is valid only for the first descriptor.
Note: When the TSE bit is set, the MAC ignores this field because the CRC and pad insertion is always
done for segmentation.
25:23 SAIC SA Insertion Control
These bits request the MAC to add or replace the Source Address field in the Ethernet packet with
the value given in the MAC Address 0 register. The application must set the CRC Pad Control bits
appropriately when SA Insertion Control is enabled for the packet.
Table continues on the next page...

---

*Page 2610*

Ethernet Media Access Controller (EMAC)
Table 518. TDES3 Normal Descriptor (Read Format) (continued)
Bits Name Description
Bit 25 specifies the MAC Address Register (1 or 0) value that is used for Source Address insertion
or replacement.
The following list describes the values of Bits[24:23]:
• 2'b00: Do not include the source address
• 2'b01: Include or insert the source address. For reliable transmission, the application must provide
frames without source addresses.
• 2'b10: Replace the source address. For reliable transmission, the application must provide frames
with source addresses.
• 2'b11: Reserved
These bits are valid in the EQOS-DMA, EQOS-AXI, and EQOS-AHB configurations when the Enable
SA and VLAN Insertion on Tx option is selected while configuring the core and when the First Segment
control bit (TDES3 [29]) is set.
This field is valid only for the first descriptor.
22:19 SLOTNUM SLOTNUM: Slot Number Control Bits in AV Mode
or THL
These bits indicate the slot interval in which the data should be fetched from the corresponding buffers
addressed by TDES0 or TDES1.
When the Transmit descriptor is fetched, the DMA compares the slot number value in this field with the
slot interval maintained in the RSN field DMA_CH#_Slot_Function_Control_Status. It fetches the data
from the buffers only if a value matches. These bits are valid only for the AV channels.
THL: TCP/UDP Header Length
If the TSE bit is set, this field contains the length of the TCP/UDP header. The minimum value of this field
must be 5 for TCP header. The value must be equal to 2 for UDP header.
This field is valid only for the first descriptor.
18 TSE TSO Split header is not supported. The value of this bit is always zero.
17:16 CIC/TPL Checksum Insertion Control or TCP Payload Length
These bits control the checksum calculation and insertion. The following list describes the bit encoding:
• 2'b00: Checksum Insertion Disabled.
• 2'b01: Only IP header checksum calculation and insertion are enabled.
• 2'b10: IP header checksum and payload checksum calculation and insertion are enabled, but
pseudo-header checksum is not calculated in hardware.
• 2'b11: IP Header checksum and payload checksum calculation and insertion are enabled, and
pseudo-header checksum is calculated in hardware.
This field is valid when the Enable Transmit TCP/IP Checksum Offload option is selected and the TSE
bit is reset.
When the TSE bit is set, this field contains the upper bits [17:16] of the TCP Payload (or IP Payload for
UDP fragmentation). This allows the TCP/UDP packet length field to be spanned across TDES3[17:0] to
provide 256 KB packet length support.
Table continues on the next page...

---

*Page 2611*

Ethernet Media Access Controller (EMAC)
Table 518. TDES3 Normal Descriptor (Read Format) (continued)
Bits Name Description
This field is valid only for the first descriptor.
15 TPL Reserved or TCP Payload Length
When the TSE bit is reset, this bit is reserved. When the TSE bit is set, this is Bit 15 of the TCP payload
length [17:0].
This field is valid only when the Enable TCP Segmentation Offloading for TCP/IP Packets option is
selected while configuring the core.
14:0 FL/TPL Frame Length or TCP Payload Length
This field is equal to the length of the packet to be transmitted in bytes. When the TSE bit is not set, this
field is equal to the total length of the packet to be transmitted:
Ethernet Header Length + TCP /IP Header Length – Preamble Length – SFD Length + Ethernet
Payload Length
When the TSE bit is set, this field is equal to the lower 15 bits of the TCP payload length in case of
segmentation and IP payload in case of UDP fragmentation.
In case of segmentation, this length does not include Ethernet header or TCP/UDP/IP header length. In
case of fragmentation, this length does not include Ethernet header and IP header.
When DWRR/WFQ algorithm is NOT enabled, value written into this field is not used when TSE = 0.
72.15.3.1.5 Transmit normal descriptor (write-back format)
The transmit descriptor write-back format includes timestamp low, timestamp high, OWN, and Status bits.
The write-back format is applicable only for the last descriptor of the corresponding packet. Write 1 to the LD bit (TDES3[28]) in
the descriptor where DMA writes back the status and timestamp information for the corresponding transmit packet.
Figure 414 shows the transmit descriptor write-back format.
31 0
TDES0 Timestamp low
TDES1 Timestamp high
TDES2 Reserved
O
TDES3 W Status [30:0]
N
Figure 414. Transmit descriptor write-back format
TDES0 normal descriptor (write-back format)
Table 519 shows that this format is only applicable to the last descriptor of a packet.

---

*Page 2612*

Ethernet Media Access Controller (EMAC)
Table 519. TDES0 normal descriptor (write-back format)TDES0 normal descriptor (write-back format)
Bit Name Description
31:0 TTSL Transmit Packet Timestamp Low
DMA updates this field with least significant 32 bits of the timestamp captured for the corresponding transmit
packet. DMA writes the timestamp only if TTSE bit of TDES2 is 1 in the first descriptor of the packet. This field
has the timestamp only if you write 1 to the Last Segment bit (LS) in the descriptor and the Timestamp status
(TTSS) bit.
TDES1 normal descriptor (write-back format)
Table 520 shows that this format is only applicable to the last descriptor of a packet.
Table 520. TDES1 normal descriptor (write-back format)
Bit Name Description
31:0 TTSH Transmit Packet Timestamp High
DMA updates this field with the most significant 32 bits of the timestamp captured for a corresponding transmit
packet. DMA writes the timestamp only if the TTSE bit of TDES2 is 1 in the first descriptor of the packet. This
field has the timestamp only if you write 1 to the Last Segment bit (LS) in the descriptor and Timestamp status
(TTSS) bit.
TDES2 Normal Descriptor (Write-Back Format)
This format is applicable only to the last descriptor of a packet.
Table 521. TDES2 normal descriptor (write-back format)
Bit Description
31:0 Reserved
TDES3 Normal Descriptor (Write-Back Format)
This format is applicable only to the last descriptor of a packet.
Table 522. TDES3 normal descriptor (write-back format)
31 30 29 28 27:18 17 16 15 14 13 12 11 10 9 8 7:4 3 2 1 0
OWN CTXT FD LD Rsvd TTSS EUE ES JT FF PCE LoC NC LC EC CC ED UF DB IHE
Table 523. TDES3 normal descriptor (write-back format)
Bit Name Description
31 OWN Own Bit
When this field is 1, it indicates that the module DMA owns the descriptor. DMA write 0 to this field when
it completes the packet transmission. After the write-back is complete, this field is set to 'b0.
30 CTXT Context Type
Table continues on the next page...

---

*Page 2613*

Ethernet Media Access Controller (EMAC)
Table 523. TDES3 normal descriptor (write-back format) (continued)
Bit Name Description
This field must be set to 'b0 for normal descriptor.
29 FD First Descriptor
This field indicates that the buffer contains the first segment of a packet.
28 LD Last Descriptor
This field is set to 'b1 for last descriptor of a packet. DMA writes the status fields only in the last descriptor
of the packet.
27:24 Rsvd Reserved
23 DE Descriptor Error
When this field is 1, it indicates that the descriptor content is incorrect. DMA writes 1 to this field during
write-back when the descriptor is closed.
Descriptor errors can be:
• Incorrect sequence from the context descriptor. For example, a location after the first descriptor for
a packet.
• All 1s
• CTXT is 1 along with LD or FD bits as 1.
NOTE
When a descriptor error occurs due to all ones or CTXT, LD, and FD bits are 1, the
transmit DMA closes the transmit descriptor with DE and LD bits set to 1. When you write
1 to IOC bit in TDES2 of corresponding first descriptor, the transmit DMA write 1 to TI bit
in the DMA_CH#_Status register
NOTE
Based on CTXT, LD, and FD bits of the transmit descriptor, the subsequent descriptor
might consider as the first descriptor (even if FD bit is not 1) and partial packet is sent.
22:18 Rsvd Reserved
17 TTSS Tx Timestamp Status
This field indicates that a timestamp has been captured for the corresponding transmit packet. When this
field is 1, it indicates that TDES0 and TDES1 have timestamp values that were captured for the transmit
packet. This field is valid only when the Last Segment control bit (TDES3 [28]) in a descriptor is 1. This
field is valid only when IEEE1588 timestamping feature is enabled; otherwise, it is reserved.
16 EUE ECC Uncorrectable Error Status
Indicates the ECC uncorrectable error in the TSO memory.
NOTE
An uncorrectable error in the transmit FIFO memory is reported with (Bit 13) FF = 1. This
is because the module flushes all such packets.
Table continues on the next page...

---

*Page 2614*

Ethernet Media Access Controller (EMAC)
Table 523. TDES3 normal descriptor (write-back format) (continued)
Bit Name Description
15 ES Error Summary
This field indicates the logical OR of the following bits:
• TDES3[0]: IP header error
• TDES3[14]: Jabber timeout
• TDES3[13]: Packet flush
• TDES3[12]: Payload checksum error
• TDES3[11]: Loss of carrier
• TDES3[10]: No carrier
• TDES3[9]: Late collision
• TDES3[8]: Excessive collision
• TDES3[3]: Excessive deferral
• TDES3[2]: Underflow error
This field is 1 when EUE (bit 16) = 1.
14 JT Jabber Timeout
This field indicates that the MAC transmitter experiences a jabber time-out. This field is 1 only when
MAC_Configuration[JD] is not 1.
13 FF Packet Flushed
This field indicates that DMA or MTL flushes the packet because the CPU gives a software
flush command.
12 PCE Payload Checksum Error
This field indicates that the checksum offload engine had a failure and does not insert any checksum
into the encapsulated TCP, UDP, or ICMP payload. This failure can either because of insufficient bytes,
as the payload length field of the IP header indicates or the MTL starting to forward the packet to
the MAC transmitter in Store-and-forward mode without calculating the checksum. This second error
condition only occurs when the transmit FIFO depth is less than the length of the Ethernet packet that is
transmitted to avoid deadlock. The MTL starts forwarding the packet when the FIFO is full, even in the
Store-and-forward mode.
This error can also occur when you detects a bus error during packet transfer.
When the full checksum offload engine is not enabled, this bit is reserved.
11 LoC Loss of Carrier
This field indicates that a loss of carrier occurred during packet transmission (that is, the gmii_crs_i signal
was inactive for one or more transmit clock periods during packet transmission). This field is valid only for
the packets transmitted without collision and when MAC operates in the Half-duplex mode.
10 NC No Carrier
This field indicates that the carrier sense signal from the PHY was not asserted during transmission.
Table continues on the next page...

---

*Page 2615*

Ethernet Media Access Controller (EMAC)
Table 523. TDES3 normal descriptor (write-back format) (continued)
Bit Name Description
9 LC Late Collision
This bit indicates that packet transmission was aborted because a collision occurred after the collision
window (64 byte times including Preamble in MII mode and 512 byte times including Preamble and Carrier
Extension in GMII mode). This bit is not valid if Underflow Error is set.
8 EC Excessive Collision
This field indicates that the transmission was aborted after 16 successive collisions when you transmits
the current packet. If MAC_Configuration[DR] = 1, this field is 1 after first collision and abort the
packet transmission.
7:4 CC Collision Count
This 4-bit counter value indicates the number of collisions occurred before transmitting the packet. The
count is not valid when the EC bit = 1.
3 ED Excessive Deferral
This field indicates that the transmission ends because of an excessive deferral of over 24,288 bit times
(155,680 bits times in 1000 Mbit/s mode or Jumbo packet enabled mode) if MAC_Configuration[DC] = 1.
When TBS is enabled in Full duplex mode and this field is 1, it indicates that the frame drops after the
expiry time is reached.
2 UF Underflow Error
This field indicates that MAC aborts the packet because the data arrived late from the system memory.
The underflow error can occur because of either of the following conditions:
• DMA encounters an empty transmit buffer when it transmits the packet
• The application filled the MTL Tx FIFO slower than the MAC transmit rate
The transmission process enters the Suspended state and sets the underflow bit corresponding to a
queue in MTL_Interrupt_Status .
1 DB Deferred Bit
This field indicates that MAC defers before transmitting because of presence of carrier. This field is valid
only in the Half-Duplex mode.
0 IHE IP Header Error
When IP Header Error is 1, this field indicates that the checksum offload engine detects an IP header error.
This field is valid only when transit checksum offload is enabled. Otherwise, it is reserved. If COE detects
an IP header error, it inserts an IPv4 header checksum if the Ethernet type field indicates an IPv4 payload.
In Full-Duplex mode, when EST/Qbv is enabled and this field is 1, it indicates the frame drop status due
to the frame size error or schedule error.
72.15.3.2 Transmit context descriptor
The transmit context descriptor can provide any time before a packet descriptor. The context is valid for the current packet and
subsequent packets. The context descriptor provide the timestamps for one-step timestamp correction and VLAN tag ID for VLAN
insertion feature. Write back is done on a context descriptor only to write 0 to OWN bit.

---

*Page 2616*

Ethernet Media Access Controller (EMAC)
NOTE
DMA internally store the VLAN tag IDs and MSS values which the application provides in a context descriptor with
their corresponding valid bits set. DMA always passes the last valid VLAN tag to the MTL, when the outer or inner
VLAN tag is provided with the valid bit set. The application cannot invalidate the valid VLAN tag which DMA stores.
The VLAN tag is inserted or replaced based on the control inputs provided for the packet.
The inner VLAN tag control input is used only for the next packet that immediately follows the context descriptor.
The application must provide a context descriptor before the normal descriptor of each packet for which DMA must
use the inner VLAN tag control input.
Figure 415 shows the transmit context descriptor format.
31 0
TDES0 Timestamp low [31:0]
TDES1 Timestamp high [31:0]
TDES2 Inner VLAN tag [31:16] Reserved Maximum segment
O
TDES3 W Control [30:16] VLAN tag [15:0]
N
Figure 415. Transmit context descriptor format
72.15.3.2.1 TDES0 context descriptor
Table 524 describes the TDES0 context descriptor format.
Table 524. TDES0 context descriptor
Bit Name Description
31:0 TTSL Transmit Packet Timestamp Low
For one-step correction, the driver can provide the lower 32 bits of timestamp in this descriptor word. DMA
uses this value as the low word for doing one-step timestamp correction. This field is valid only if the OSTC
and TCMSSV bits of TDES3 context descriptor are 1.
72.15.3.2.2 TDES1 context descriptor
Table 525 describes the TDES1 context descriptor format.
Table 525. TDES1 context descriptor
Bit Name Description
31:0 TTSH Transmit Packet Timestamp High
For one-step correction, the driver can provide the upper 32 bits of timestamp in this descriptor. DMA uses this
value as the high word for doing one-step timestamp correction. This field is valid only if the OSTC and TCMSSV
bits of TDES3 context descriptor are 1.

---

*Page 2617*

Ethernet Media Access Controller (EMAC)
72.15.3.2.3 TDES2 context descriptor
Table 526 shows the TDES2 context descriptor format.
Table 526. TDES2 context descriptor
Bit Name Description
31:16 IVT Inner VLAN Tag
When the IVLTV bit of TDES3 context descriptor is 1 and the TCMSSV and OSTC bits of TDES3 context
descriptor becomes 0, it indicates that the TDES2[31:16] contains the inner VLAN tag to insert in the
subsequent transmit packets.
15:14 Rsvd Reserved
13:0 MSS TSO split header is not supported. The value of this bit is always zero.
72.15.3.2.4 TDES3 context descriptor
Table 527. TDES3 context descriptor
31 30 29:28 27 26 25:24 23 22:18 17 16 15:0
OWN CTXT Rsvd OSTC TCMSSV Rsvd CDE Rsvd IVLTV VLTV VT
Table 528 shows the TDES3 context descriptor format.
Table 528. TDES3 context descriptor
Bit Name Description
31 OWN Own Bit
When this field is 1, it indicates that the module DMA owns the descriptor. When this field becomes 0, it indicates
that the application owns the descriptor. DMA writes 0 to this field immediately after the read.
30 CTXT Context Type
This field must set to 1'b1 for context descriptor.
29:28 Rsvd Reserved
27 OSTC One-Step Timestamp Correction Enable
When this field is 1, it indicates that DMA performs a one-step timestamp correction with reference to the
timestamp values provided in TDES0 and TDES1.
26 TCMS One-Step Timestamp Correction Input or MSS Valid
SV
When this field and the OSTC field are 1, it indicates that the timestamp correction input provided in TDES0 and
TDES1 is valid.
When the OSTC field becomes 0 and this field and the TSE bit of TDES3 are 1 in subsequent normal descriptor,
it indicates that the MSS input in TDES2 is valid.
25:24 Rsvd Reserved
Table continues on the next page...

---

*Page 2618*

Ethernet Media Access Controller (EMAC)
Table 528. TDES3 context descriptor (continued)
Bit Name Description
23 DE Descriptor Error
When this field is 1, it indicates that the descriptor content is incorrect. DMA writes 1 to this field during
write-back when it closes the context descriptor.
Descriptor errors can be:
• Incorrect sequence from the context descriptor. For example, a location before the first descriptor for
a packet.
• All ones.
• CD, LD, and FD bits = 1.
NOTE
When a descriptor error occurs due to all ones or CTXT, LD, and FD bits are 1, the transmit
DMA closes the transmit descriptor with DE and LD bits are 1. When you write 1 to IOC
bit in TDES2 of corresponding first descriptor, the transmit DMA writes 1 to TI bit in the
DMA_CH#_Status register
NOTE
On the basis of CTXT, LD, and FD bits of the transmit descriptor, the subsequent descriptor
might considered as the first descriptor (even if FD bit is not 1) and partial packet is sent.
NOTE
This error is categorized as an abnormal event. Recovery from this event is possible by issuing
a software reset only. DMA stopping, reconfiguring, and restarting recovery mechanism is
not supported.
22:20 Rsvd Reserved
19:18 IVTIR Inner VLAN Tag Insert or Replace
When this field is 1, it requests MAC to perform inner VLAN tagging or un-tagging before transmitting the
packets. If the packet is modified for VLAN tags, MAC automatically recalculates and replaces the CRC bytes.
The following list describes the values of these bits:
• 2'b00: Do not add the inner VLAN tag.
• 2'b01: Remove the inner VLAN tag from the packets before transmission. You must use this option only
with the VLAN frames.
• 2'b10: Insert an inner VLAN tag with the tag value programmed in MAC_Inner_VLAN_Incl or
context descriptor.
• 2'b11: Replace the inner VLAN tag in packets with the tag value programmed in MAC_Inner_VLAN_Incl or
context descriptor. You must use this option only with the VLAN frames..
These fields are valid when you select the enable SA and VLAN insertion on transit and enable double VLAN
processing options.
17 IVLTV Inner VLAN Tag Valid
When this field is 1, it indicates that IVT field of TDES2 is valid.
Table continues on the next page...

---

*Page 2619*

Ethernet Media Access Controller (EMAC)
Table 528. TDES3 context descriptor (continued)
Bit Name Description
16 VLTV VLAN Tag Valid
When this field is 1, it indicates that VT field of TDES3 is valid.
15:0 VT VLAN Tag
Contains the VLAN tag to insert or replace in the packet. This field is used as VLAN tag only when
MAC_VLAN_Incl[VLTI] becomes 0.

#### 72.15.4 Receive descriptor

DMA in the module reads a descriptor only if the tail pointer is different from the base pointer or current pointer. It is recommended
to have a descriptor ring with a length that accommodates at least two complete packets which MAC receives. Otherwise, the
performance of DMA is impacted because of the unavailability of the descriptors. In such situations, the RxFIFO in MTL becomes
full and starts dropping packets.
The following receive descriptors are present:
• Normal descriptors
• Context descriptors
You can prepare all the receive descriptors and give to DMA as normal descriptors with the content as shown in receive normal
descriptor (read format). DMA reads this descriptor and the receive DMA closes the descriptor with the corresponding packet
status after transferring a received packet (or part of) to the buffers indicated by the descriptor. The receive normal descriptor
(write-back format) describes the format of this status.
For some packets, only the normal descriptor bits cannot write the complete status. For such packets, the receive DMA writes the
extended status to the next descriptor (without processing or using the buffers or pointers embedded in that descriptor). Receive
context descriptor describes the descriptor write back format and content.
72.15.4.1 Receive normal descriptor (read format)
The read format for a receive normal descriptor is made up of the following:
• A header or buffer 1 address
• Reserved field
• Payload or buffer 2 or next descriptor address
• A 30-bit reserved field
• OWN bit
• An interrupt field
Figure 416 shows the read format for a receive normal descriptor.

---

*Page 2620*

Ethernet Media Access Controller (EMAC)
31 0
RDES0 Header or buffer 1 address
RDES1 Reserved
RDES2 Payload, buffer 2, or next descriptor address
l
O
N
RDES3 W Reserved [29:0]
T
N
E
Figure 416. Receive normal descriptor read format
NOTE
In the receive descriptor (read format), if the buffer address field is all zeros, the module does not transfer data to
that buffer and skips to the next buffer or next descriptor.
72.15.4.1.1 RDES0 normal descriptor (read format)
Table 529 shows the RDES0 normal descriptor read format.
Table 529. RDES0 normal descriptor (read format)
Bit Name Description
31:0 BUF1AP Header or Buffer 1 Address Pointer
When the SPH field of control register of a channel becomes 0, these field indicates the physical address of
buffer 1. When the SPH field is 1, these field indicates the physical address of header buffer where the receive
DMA writes the L2/L3/L4 header bytes of the received packet.
The application can program a byte-aligned address for this buffer which means that the LS bits of this field
can be non-zero. However, DMA performs a write operation with RDES0[1:0] (or RDES0[2:0]/[3:0] in case of
64-/128-bit configuration) as zero, when the start of packet is transferred. However, the packet data shifts per
the actual offset which the buffer address pointer provides.
If the address pointer points to a buffer where the middle or last part of the packet is stored, DMA ignores the
offset address and writes to the full location which the data-width indicates .
72.15.4.1.2 RDES1 normal descriptor (read format)
Table 530 shows the RDES1 normal descriptor read format.
Table 530. RDES1 normal descriptor (read format)
Bit Name Description
31:0 Reserved Contains the most-significant 32 bits of the buffer 1 address pointer in 64-bit Addressing mode. Otherwise,
or this field is reserved.
BUF1AP
72.15.4.1.3 RDES2 normal descriptor (read format)
Table 531 shows the RDES2 normal descriptor read format.

---

*Page 2621*

Ethernet Media Access Controller (EMAC)
Table 531. RDES2 normal descriptor (read format)
Bit Name Description
31:0 BUF2AP Buffer 2 Address Pointer
Indicates the physical address of buffer 2.
When the SPH bit of the DMA_CH#_Control register = 1, it indicates that the buffer address pointer must be
bus width-aligned, that is, RDES2[3:0, 2:0, or 1:0] = 0 corresponding to 128, 64, or 32 bus width. LSBs are
ignored internally.
When the SPH bit of the DMA_CH#_Control register becomes 0, it indicates that there is no limitations on the
RDES2 value. However, the RxDMA uses the LS fields of the pointer address only when it transfers the start
bytes of a packet. If the BUF2AP provides the address of a buffer in which the middle or last part of a packet
is stored, DMA ignores BUF2AP[3:0 or 2:0 or 1:0] (corresponding to 128- or 64- or 32-bit data-bus) and writes
to the complete location.
72.15.4.1.4 RDES3 normal descriptor (read format)
Following table describes the RDES3 normal descriptor read format.
31 30 19-26 25 24 23:0
OWN IOC Rsvd BUF2V BUF1V Rsvd
Table 532. RDES3 normal descriptor (read format)
Bit Name Description
31 OWN Own Bit
When this field is 1, it indicates that the module DMA owns the descriptor. When this field becomes 0,
it indicates that the application owns the descriptor. DMA write 0 this field when either of the following
conditions is true:
• DMA completes the packet reception.
• The buffers associated with the descriptor are full.
30 IOC Interrupt Enabled on Completion
When this field is 1, it indicates that an interrupt is issued to the application when DMA closes this descriptor.
29:26 Rsvd Reserved
25 BUF2V Buffer 2 Address Valid
When this field is 1, it indicates to the DMA that the buffer 2 address specified in RDES2 is valid.
The application must write 1 to this field so that DMA can use the address, to which the buffer 2 address in
RDES2 points to write received packet data.
24 BUF1V Buffer 1 Address Valid
When this field is 1, it indicates to the DMA that the buffer 1 address specified in RDES1 is valid.
Table continues on the next page...

---

*Page 2622*

Ethernet Media Access Controller (EMAC)
Table 532. RDES3 normal descriptor (read format) (continued)
Bit Name Description
The application must write 1 to this field, if DMA uses the address to which the buffer 1 address in RDES1
points to
write the received packet data.
23:0 Rsvd Reserved
72.15.4.1.5 Receive normal descriptor (write-back format)
Following figure shows the write-back format for a receive normal descriptor.
31 0
RDES0 Inner VLAN tag [31:16] Outer VLAN tag [15:0]
RDES1 OAM code or MAC control Extended status
ARP
V Reserved Header length
RDES2 MAC filter of flexible receive parser status
F [14:12] [9:0]
[11:10]
C
O
T F L E
RDES3 W Status [27:15] Packet length [14:0]
X D D S
N
T
Figure 417. Receive normal descriptor (write-back format)
NOTE
When you enables the flexible receive parser, RDES2[31:16] indicates the parser status, and not the MAC filter
status. The MAC filter status is not available when flexible receive parser is enabled.
72.15.4.1.5.1 RDES0 normal descriptor (write-back format)
Table 533 shows the write-back format for the RDES0 normal descriptor.
Table 533. RDES0 normal descriptor (write-back format)
Bit Name Description
31:16 IVT Inner VLAN Tag
Contains the inner VLAN tag of the received packet if the RS0V bit of RDES3 = 1.
This field is valid only when you enables the double VLAN tag processing and VLAN tag stripping.
15:0 OVT Outer VLAN Tag
Contains the outer VLAN tag of the received packet if the RS0V bit of RDES3 = 1.
72.15.4.1.5.2 RDES1 normal descriptor (write-back format)
The status fields in the write-back format are valid only for the last descriptor (RDES3[28] = 1). Following table provide the details
of the write-back format for RDES1 normal descriptor.

---

*Page 2623*

Ethernet Media Access Controller (EMAC)
31:16 15 14 13 12 11:8 7 6 5 4 3 2:0
OPC TD TSA PV PFT PMT IPCE IPCB IPV6 IPV4 IPHE PT
Table 534. RDES1 normal descriptor (write-back format)
Bit Name Description
Indicates any one of the following:
31:16 OPC
• OAM sub-type code: If bits [18:16] of RDES3 as described in RDES3 normal descriptor (read format)
are 111b, this field contains the OAM sub-type and code fields.
• MAC control packet opcode: Bits 15:8 of RDES3 as described in RDES3 normal descriptor (read
format) contain the subtype and bits 7:0 contain the code.
15 TD Timestamp Dropped
Indicates that the timestamp was captured for this packet but it dropped in the MTL Rx FIFO because
of overflow.
This field is available only when you select the timestamp feature. Otherwise, this field is reserved.
14 TSA Timestamp Available
Indicates that the timestamp value is available in a context descriptor word 2 (RDES2) and word 1(RDES1)
when timestamp is present. This is valid only when the last descriptor field (RDES3 [28]) = 1
You can write the context descriptor in the next descriptor just after the last normal descriptor for a packet.
13 PV PTP Version
Indicates that the received PTP message has the IEEE 1588 version 2 format. When this field becomes 0, it
indicates the IEEE 1588 version 1 format.
This field is available only when you select the timestamp feature. Otherwise, this field is reserved.
12 PFT PTP Packet Type
Indicates that the PTP message is sent directly over Ethernet. This field is available only when you select the
timestamp feature. Otherwise, this field is reserved.
11:8 PMT PTP Message Type
Encodes to give the type of the message received:
• 0000: No PTP message received
• 0001: SYNC (all clock types)
• 0010: Follow_Up (all clock types)
• 0011: Delay_Req (all clock types)
• 0100: Delay_Resp (all clock types)
• 0101: Pdelay_Req (in peer-to-peer transparent clock)
• 0110: Pdelay_Resp (in peer-to-peer transparent clock)
• 0111: Pdelay_Resp_Follow_Up (in peer-to-peer transparent clock)
Table continues on the next page...

---

*Page 2624*

Ethernet Media Access Controller (EMAC)
Table 534. RDES1 normal descriptor (write-back format) (continued)
Bit Name Description
• 1000: Announce
• 1001: Management
• 1010: Signaling
• 1011–1110: Reserved
• 1111: PTP packet with reserved message type
These fields are available only when you select the timestamp feature.
7 IPCE IP Payload Error
When this field is 1, it indicates either of the following:
• The 16-bit IP payload checksum (that is, the TCP, UDP, or ICMP checksum) which MAC calculates does
not match the corresponding checksum field in the received segment.
• The TCP, UDP, or ICMP segment length does not match the payload length value in the IP header field.
• The TCP, UDP, or ICMP segment length is less than minimum allowed segment length for TCP, UDP,
or ICMP.
Bit 15 (ES) of RDES3 is not set when this field is 1.
6 IPCB IP Checksum Bypassed
Indicates that the checksum offload engine is bypassed. This field is available when you select the enable
receive TCP/IP checksum check feature.
5 IPV6 IPv6 header Present
Indicates that an IPV6 header is detected. When you select the enable split header feature option and the
SPH bit of control register of a channel is 1, the IPV6 header is available in the header buffer area to which
RDES0 points.
4 IPV4 IPV4 Header Present
Indicates that an IPV4 header is detected. When the SPH bit of RDES3 is 1, the IPV4 header is available in the
header buffer area to which RDES0 points.
3 IPHE IP Header Error
When this field is 1, it indicates either of the following:
• The 16-bit IPv4 header checksum which MAC calculates does not match the received checksum bytes.
• The IP datagram version is not consistent with the Ethernet type value.
• Ethernet packet does not have the expected number of IP header bytes.
This field is valid when either bit 5 or bit 4 is 1. This field is available when you select the enable receive TCP/IP
checksum check feature.
2:0 PT Payload Type
Indicates the type of payload encapsulated in the IP datagram that the receive checksum offload engine
(COE) process.
Table continues on the next page...

---

*Page 2625*

Ethernet Media Access Controller (EMAC)
Table 534. RDES1 normal descriptor (write-back format) (continued)
Bit Name Description
• 3'b000: Unknown type or IP/AV payload not processed
• 3'b001: UDP
• 3'b010: TCP
• 3'b011: ICMP
• 3'b110: AV tagged data packet
• 3'b111: AV tagged control packet
• 3'b101: AV untagged control packet
• 3'b100: IGMP, if IPV4 header present field = 1, else DCB (LLDP) control packet
If the COE does not process the payload of an IP datagram because there is an IP header error or fragmented
IP, it sets these fields to 3'b000.
72.15.4.1.5.3 RDES2 normal descriptor (Write-back format)
31:29 28 27 26:19 18 17 16 15 14 13:11 10 9:0
L3L4FM L4FM L3FM MADRM HF DAF SAF OTS ITS Rsvd ARPNR HL
Table 535. RDES2 normal descriptor (Write-back format)
Bit Name Description
31:29 L3L4FM Layer 3 and Layer 4 Filter Number Matched
Indicates the number of the layer 3 and layer 4 filter that matches the received packet:
• 000: Filter 0
• 001: Filter 1
• 010: Filter 2
• 011: Filter 3
• 100: Filter 4
• 101: Filter 5
• 110: Filter 6
• 111: Filter 7
This field is valid only when bit 28 or bit 27 = 1. When more than one filter matches, these fields provide
the number of lowest filter.
NOTE
This status is not available when Flexible RX Parser is enabled.
28 L4FM Layer 4 Filter Match
Table continues on the next page...

---

*Page 2626*

Ethernet Media Access Controller (EMAC)
Table 535. RDES2 normal descriptor (Write-back format) (continued)
Bit Name Description
When this field is 1, it indicates that the received packet matches one of the enabled layer 4 port number
fields. This status is given only when one of these conditions is true:
• Layer 3 fields are not enabled and all enabled layer 4 fields match
• All enabled layer 3 and layer 4 filter fields match
When more than one filter matches, this bit gives the layer 4 filter status of filter indicated by Bits[31:29].
NOTE
This status is not available when the flexible receive parser is enabled.
27 L3FM Layer 3 Filter Match
When this field is 1, it indicates that the received packet matches one of the enabled layer 3 IP address
fields. This status is given only when one of these conditions is true:
• All enabled layer 3 fields match and bypasses all enabled layer 4 fields.
• All enabled filter fields match.
When more than one filter matches, this field gives the layer 3 filter status of filter which
bits[31:29] indicate.
NOTE
This status is not available when the flexible receive parser is enabled.
26:19 MADRM MAC Address Match or Hash Value
When HF becomes 0, it indicates that this field contains the MAC address register number that matches
the received packet destination address. This field is valid only if the DAF field becomes 0.
When HF = 1, it indicates that this field contains the hash value that MAC computes. A packet passes the
hash filter when the field corresponding to the hash value is 1 in the hash filter register.
NOTE
This status is not available when the flexible receive parser is enabled.
18 HF Hash Filter Status
When this field is 1, it indicates that the packet passes the MAC address hash filter. Bits[26:19] indicate
the hash value.
NOTE
This status is not available when the flexible receive parser is enabled.
17 DAF/RXPI Destination Address Filter Fail
When flexible receive parser is disabled, and this field is 1, it indicates that the packet fails the DA
filter in MAC.
When flexible receive parser is enabled, and this field is 1, it indicates that the packet parsing is
incomplete (RXPI) due to ECC error.
Table continues on the next page...

---

*Page 2627*

Ethernet Media Access Controller (EMAC)
Table 535. RDES2 normal descriptor (Write-back format) (continued)
Bit Name Description
NOTE
When this field is 1, RDES3 ES field is also 1.
16 SAF/RXPD SA Address Filter Fail
When flexible receive parser is disabled, and this field is 1, it indicates that the packet fails the SA filter
in MAC.
When flexible receive parser is enabled and this field is 1, it indicates that the parser drops the
RXPD packet.
NOTE
When this field is 1, RDES3 ES field is also 1.
15 OTS VLAN Filter Status
When this field is 1, it indicates that the received packet VLAN tag passes the VLAN filter.
This field redefines as an outer VLAN tag filter status (OTS). This field is valid for both single and double
VLAN tagged frames.
14 ITS Inner VLAN Tag Filter Status (ITS)
This field is valid only for double VLAN tagged frames, when double VLAN processing is enabled.
See Filter status for more information.
13:11 Rsvd Reserved
10 ARPNR ARP Reply Not Generated
When this field is 1, it indicates that MAC did not generate the ARP reply for the ARP request packet it
receives.. This field is 1 when MAC is busy transmitting ARP reply to earlier ARP request (only one ARP
request is processed at a time).
This field is reserved when you do not select the enable IPv4 ARP offload option.
9:0 HL L3/L4 Header Length
Contains the length of the packet header which MAC splits at L3 or L4 header boundary which the MAC
receiver identifies. This field is valid only when the first descriptor bit = 1 (FD = 1).
You can write the header data to the buffer 1 address of corresponding descriptor. If the header length
is zero, this field is not valid. It implies that MAC does not identify and split the header.
This field is valid when you select the enable split header feature option.
72.15.4.1.5.4 RDES3 normal descriptor (Write-back format)
Following table describes the write-back format for the RDES3 normal descriptor.
31 30 29 28 27 26 25 24 23 22 21 20 19 18:16 15 14:0
OWN CTXT FD LD RS2V RS1V RS0V CE GP RWT OE RE DE LT ES PL

---

*Page 2628*

Ethernet Media Access Controller (EMAC)
Table 536. RDES3 normal descriptor (Write-back format)
Bit Name Description
31 OWN Own Bit
When this field is 1, it indicates that the module DMA owns the descriptor. If this field becomes 0, it indicates
that the application owns the descriptor. DMA writes 0 to this field when either of these conditions is true:
• DMA completes the packet reception.
• The buffers associated with the descriptor are full.
30 CTXT Receive Context Descriptor
When this field is 1, it indicates that the current descriptor is a context type descriptor. DMA writes 1'b0 to this
field for a normal receive descriptor.
When CTXT and FD bits are used together, {CTXT, FD}
• 2’b00: Intermediate descriptor
• 2’b01: First descriptor
• 2’b10: Reserved
• 2’b11: Descriptor error (due to all 1s)
NOTE
When descriptor error occurs, the receive DMA closes the receive descriptor indicating a
descriptor error. This receive descriptor is skipped and the buffer addresses are not used to
write the packet data. Receive DMA will write 1 to the CDE bit in DMA_CH#_Status register
but not to the RI bit even when IOC = 1, as this is not marked as the last receive descriptor for
the packet. The subsequent valid receive descriptor writes the packet data.
29 FD First Descriptor
When this field is 1, it indicates that this descriptor contains the first buffer of the packet. If the size of the first
buffer is 0, the second buffer contains the beginning of the packet and if the size of the second buffer is also
0, the next descriptor will contain the beginning of the packet.
See the CTXT bit description for more information on how to use the CTXT bit and FD bit together.
28 LD Last Descriptor
When this field is 1, it indicates that this descriptor buffers points to the last buffers of the packet.
27 RS2V Receive Status RDES2 Valid
When this field is 1, it indicates that the status in RDES2 is valid and DMA writes it. This field is valid only when
the LD bit of RDES3 = 1.
26 RS1V Receive Status RDES1 Valid
When this field is 1, it indicates that the status in RDES1 is valid and DMA writes it. This field is valid only when
the LD bit of RDES3 = 1.
25 RS0V Receive Status RDES0 Valid
When this field is 1, it indicates that the status in RDES0 is valid and DMA writes it. This field is valid only when
the LD bit of RDES3 = 1.
Table continues on the next page...

---

*Page 2629*

Ethernet Media Access Controller (EMAC)
Table 536. RDES3 normal descriptor (Write-back format) (continued)
Bit Name Description
24 CE CRC Error
When this field is 1, it indicates that a cyclic redundancy check (CRC) error occurs on the received packet. This
field is valid only when the LD bit of RDES3 = 1.
23 GP Giant Packet
When this field is 1, it indicates that the packet length exceeds the specified maximum Ethernet size of 1518,
1522, or 2000 bytes (9018 or 9022 bytes if jumbo packet enable = 1).
NOTE
Giant packet indicates only the packet length. It does not cause any packet truncation.
22 RWT Receive Watchdog Timeout
When this field is 1, it indicates that the receive watchdog timer expires when it receives the current packet.
The current packet truncates after watchdog timeout.
21 OE Overflow Error
When this field is 1, it indicates that the received packet is damaged because of buffer overflow in
receive FIFO.
NOTE
This field is 1 only when DMA transfers a partial packet to the application. This happens only
when the receive FIFO operates in the Threshold mode. In the Store-and-Forward mode, all
the partial packets drops completely in the receive FIFO.
20 RE Receive Error
When this field is 1, it indicates that the gmii_rxer_i signal asserts when the gmii_rxdv_i signal asserts during
the packet reception. This error also includes carrier extension error in the GMII and Half-duplex mode. Error
can be of less or no extension, or error (rxd!= 0f) during extension.
19 DE Dribble Bit Error
When this field is 1, it indicates that the received packet has a non-integer multiple of bytes (odd nibbles). This
field is valid only in the MII mode.
18:16 LT Length/Type Field
Indicates if the packet received is a length packet or a type packet. The encoding of the 3 bits is as follows:
• 3'b000: The packet is a length packet.
• 3'b001: The packet is a type packet.
• 3'b011: The packet is a ARP request packet type
• 3'b100: The packet is a type packet with VLAN tag
• 3'b101: The packet is a type packet with double VLAN tag
• 3'b110: The packet is a MAC control packet type
• 3'b111: The packet is a OAM packet type
Table continues on the next page...

---

*Page 2630*

Ethernet Media Access Controller (EMAC)
Table 536. RDES3 normal descriptor (Write-back format) (continued)
Bit Name Description
• 3'b010: Reserved
15 ES Error Summary
When this field is 1, it indicates the logical OR of these bits, :
• RDES3[24]: CRC error
• RDES3[19]: Dribble error
• RDES3[20]: Receive error
• RDES3[22]: Watchdog timeout
• RDES3[21]: Overflow error
• RDES3[23]: Giant packet
• RDES2[17]: Destination address filter fail, when the flexible receive parser is enabled
• RDES2[16]: SA address filter fail, when flexible receive parser is enabled
This field is valid only when the LD bit of RDES3 = 1.
14:0 PL Packet Length
Indicates the byte length of the received packet that was transferred to system memory (including CRC).
This field is valid when the LD bit of RDES3 = 1 and overflow error bits becomes 0. The packet length also
includes the two bytes that appends to the Ethernet packet when IP checksum calculation is enabled and the
received packet is not a MAC control packet.
This field is valid when the LD bit of RDES3 = 1. When the last descriptor and error summary bits are not 1,
this field indicates the accumulated number of bytes that are transferred for the current packet.
72.15.4.1.6 Receive context descriptor
This descriptor is read-only for the application. Only DMA can write to this descriptor. The context descriptor provides information
about the extended status related to the last received packet. Bit 30 of RDES3 indicates the context type descriptor.
31 0
RDES0 Timestamp low [31:0]
RDES1 Timestamp high [31:0]
RDES2 Reserved
C
O
T
RDES3 W Reserved [29:0]
X
N
T
Figure 418. Receive context descriptor format

---

*Page 2631*

Ethernet Media Access Controller (EMAC)
72.15.4.1.6.1 RDES0 context descriptor
Table 537. RDES0 context descriptor
Bit Name Description
31:0 RTSL Receive Packet Timestamp Low
DMA updates this field with least significant 32-bits of the timestamp captured for the corresponding receive
packet. When this field and the RTSH field of RDES1 show all-ones value, you must consider the timestamp
as corrupt.
72.15.4.1.6.2 RDES1 context descriptor
Table 538. RDES1 context descriptor
Bit Field Description
31:0 RTSH Receive Packet Timestamp High
DMA updates this field with the most significant 32-bits of the timestamp captured for the corresponding receive
packet. When this field and the RTSL field of RDES0 show all-ones value, you must consider the timestamp
as corrupt.
72.15.4.1.6.3 RDES2 context descriptor
Table 539. RDES2 context descriptor
Bit Description
31:0 Reserved
72.15.4.1.6.4 RDES3 context descriptor
31 30 29 28:0
OWN CTXT DE Rsvd
Table 540. RDES3 context descriptor
Bit Name Description
31 OWN Own Bit
When this field is 1, it indicates that DMA owns the descriptor. When this field becomes 0, it indicates that the
application owns the descriptor. DMA clears this field when either of these conditions is true:
• DMA completes the packet reception.
• The buffers associated with the descriptor are full.
30 CTXT Receive Context Descriptor
Table continues on the next page...

---

*Page 2632*

Ethernet Media Access Controller (EMAC)
Table 540. RDES3 context descriptor (continued)
Bit Name Description
When this field is 1, it indicates that the current descriptor is a context descriptor. DMA writes 1'b1 to this field
for context descriptor.
DMA writes 2’b11 to indicate a descriptor error due to all = 1.
When CTXT and DE bits are used together {CTXT, DE}
• 2’b00: Reserved
• 2’b01: Reserved
• 2’b10: Context descriptor
• 2’b11: Descriptor error
Note: When descriptor error occurs, the receive DMA closes the receive descriptor indicating descriptor error.
This receive descriptor is skipped and the buffer addresses does not write the packet data. The receive DMA
writes 1 to the CDE bit in DMA_CH#_Status register but not to the RI bit even when IOC = 1, because this
is not marked as last receive descriptor for the packet. The subsequent valid receive descriptor writes the
packet data.
29 DE Descriptor Error
See the CTXT bit description for more information about how to use the DE bit along with CTXT bit.
28:0 Rsvd Reserved

#### 72.15.5 Enhanced descriptor for time-based scheduling

This feature needs 32 bytes enhanced descriptors to enable on all the DMA channels that uses this feature (write 1 to EDSE bit
of DMA_CH(#i)_TX_Control register).
The structure of 32-byte descriptor for the context and the normal descriptor in read and write formats are described in the
upcoming sections.
72.15.5.1 Enhanced normal descriptor - Read (32-bit mode)
These fields are present in the first 16 bytes of the enhanced descriptor format of normal descriptor:
• LTV- Indicates that the launch time (LT) and GSN fields present in the descriptor are valid. The LTV must be = 1 only if the
FD bit of the descriptor is not.
• GSN- Indicates the GCL slot number associated with the packet.
• LT- Indicates the launch time associated with the packet.

---

*Page 2633*

Ethernet Media Access Controller (EMAC)
31 11 8 0
GSN
ETDESC4 Reserved LT [31:24]
LTV [3:0]
ETDESC5 Launch time [23:0]
ETDESC6 Reserved
ETDESC7 Reserved
TDESC0 Header or buffer 1 address [31:0]
TDESC1 Buffer 2 address [31:0] or buffer 1 address [63:32]
Header or buffer 1
TDESC2 Buffer 2 length [29:16]
IOC VTIR length [13:0]
TTSE
Control [30:16] Frame length [14:0]
TDESC3
OWN Control [30:18] Payload length [17:0]
Figure 419. Enhanced normal descriptor - Read (32-bit mode)
72.15.5.2 Enhanced normal descriptor (Write, 32-bit mode)
31 11 8 0
GSN
ETDESC4 Reserved LT [31:24]
LTV [3:0]
ETDESC5 Launch time [23:0]
ETDESC6 Reserved
ETDESC7 Reserved
TDESC0 Timestamp low
TDESC1 Timestamp high
TDESC2 Reserved
TDESC3 Status [30:0]
OWN
Figure 420. Enhanced normal descriptor write (32-bit mode)

---

*Page 2634*

Ethernet Media Access Controller (EMAC)
NOTE
• All the enhanced descriptors, example, assumes a 32-bit data width configurations. However, the same
definitions are valid in 64-bit and 128-bit configurations.
• In both the write back formats, you cannot modify the extended 16 bytes. The remaining 16 bytes (TDESC0
to TDESC3) are written back per the previous 16 bytes descriptor format.
• When you enable the fetch time it overrides the AV slot function
• When an unaligned new GCL list (with a different CTR) is installed it is recommended not to have traffic during
the installation of the new list. Any traffic during the switching of the lists might have unpredictable behavior
regarding fetch, launch, and launch expiry because the CTR and BTR values get updated when the frame
is processed
72.15.5.3 Enhanced context descriptor (Read, 32-bit mode)
The first 16 bytes of the enhanced context descriptor (ETDESC4 to ETDESC7) are reserved and must be = 0. The fields present in
the last bytes of the descriptor (TDESC0 to TDESC3) are same as the context descriptor (TDESC0 to TDESC3) in 16 byte format.
31 0
ETDESC4 Reserved
ETDESC5 Reserved
ETDESC6 Reserved
ETDESC7 Reserved
TDESC0 Timestamp low [31:0]
TDESC1 Timestamp high [31:0]
Maximum segment
TDESC2 Inner VLAN tag [31:16] Reserved
size [13:0]
TDESC3 OWN Control [30:16] VLAN tag [15:0]
Figure 421. Enhanced context descriptor - Read

---

*Page 2635*

Ethernet Media Access Controller (EMAC)
72.15.5.4 Enhanced context descriptor (Write, 32-bit mode)
31 0
ETDESC4 Reserved
ETDESC5 Reserved
ETDESC6 Reserved
ETDESC7 Reserved
TDESC0 Reserved
TDESC1 Reserved
TDESC2 Reserved
TDESC3 Reserved Reserved
OWN CTXT CDE
Figure 422. Enhanced context descriptor - Write

#### 72.16 Programming

This section and all its sub-sections are Synopsys Proprietary. Used with permission.
This section provides the instructions for initializing the DMA or MAC registers in the proper sequence.

#### 72.16.1 Initializing DMA

Perform these steps to initialize DMA:
1. Provide a software reset. This resets all the MAC internal registers and logic (bit-0 of DMA_Mode ).
2. Wait for the reset process (poll bit 0 of DMA_Mode which is only clears after the reset operation completes) to complete.
3. Program these fields to initialize DMA_SysBus_Mode :
a. DMA_SysBus_Mode[AAL]
b. Fixed burst or undefined burst
c. Burst mode values in case of AHB bus interface, OSR_LMT in case of AXI bus interface.
d. Select the maximum burst length possible on the AXI bus (bits [7:1]), if fixed length value is enabled.
4. Create a descriptor list for a transmit and receive. Also, ensure that DMA (write 1 to bit 31 of descriptor TDES3/RDES3)
owns the descriptors. See Descriptors for more information about descriptors.
5. Program the transmit and receive ring length registers (DMA_CH(#i)_TxDesc_Ring_Length (for i = 0; i <= 1) and
DMA_CH(#i)_RxDesc_Ring_Length (for i = 0; i <= 1)). The programmed ring length must be at least 4.
NOTE
The descriptor address from the start to the end of the ring must not cross the 4 GB boundary.

---

*Page 2636*

Ethernet Media Access Controller (EMAC)
6. Initialize the receive and transmit descriptor list address with the base address of the transmit and
receive descriptor (DMA_CH(#i)_TxDesc_List_Address (for i = 0; i <= DWC_EQOS_NUM_DMA_TX_CH-1),
DMA_CH(#i)_RxDesc_List_Address (for i = 0; i <= DWC_EQOS_NUM_DMA_RX_CH-1)). Also, program the transmit
and receive tail pointer registers that indicates DMA about the available descriptors (DMA_CH(#i)_TxDesc_Tail_Pointer
(for i = 0; i <= DWC_EQOS_NUM_DMA_TX_CH-1) and DMA_CH(#i)_RxDesc_Tail_Pointer (for i = 0; i <=
DWC_EQOS_NUM_DMA_RX_CH-1)).
NOTE
For 40-bit or 48-bit addressing mode, program the higher address List registers
(DMA_CH[n]_TxDesc_List_HAddress, DMA_CH[n]_RxDesc_List_HAddress).
The tailpointer registers must advance to the location immediately after the descriptors that are set so that DMA is
aware that the additional descriptors are available.
7. Program the settings of these registers for the parameters like maximum burst-length (PBL) which DMA initiates,
descriptor skip lengths, OSP in case of TxDMA, RBSZ in case of RxDMA, and so on:
• DMA_CH(#i)_Control (for i = 0; i <= DWC_EQOS_NUM_DMA_TX_CH-1)
• DMA_CH(#i)_TX_Control (for i = 0; i <= DWC_EQOS_NUM_DMA_TX_CH-1)
• DMA_CH(#i)_RX_Control (for i = 0; i <= DWC_EQOS_NUM_DMA_RX_CH-1)
8. Program the DMA_CH(#i)_Interrupt_Enable (for i = 0; i <= DWC_EQOS_NUM_DMA_TX_CH-1) register to enable the
interrupts.
9. Write 1 to SR (bit 0) of the DMA_CH(#i)_RX_Control (for i = 0; i <= DWC_EQOS_NUM_DMA_RX_CH-1) and ST (bit
0) of the DMA_CH(#i)_TX_Control (for i = 0; i <= DWC_EQOS_NUM_DMA_TX_CH-1) register to start the receive and
transmit DMAs.
10. Repeat steps 4 to 9 for all the transit DMA and receive DMA channels selected in the hardware.

#### 72.16.2 Initializing MTL registers

The transaction layer (MTL) registers must initialize to establish the transmit and receive operating modes and commands.
Perform these steps to initialize the MTL registers:
1. Program MTL_Operation_Mode[SCHALG] and MTL_Operation_Mode[RAA] to initialize the MTL operation in case of
multiple transit and receive queues.
2. Program the receive queue to DMA mapping in MTL_RxQ_DMA_Map0 .
3. Program these fields to initialize the mode of operation in MTL_TxQ0_Operation_Mode .
a. MTL_TxQ0_Operation_Mode[TSF] or MTL_TxQ0_Operation_Mode[TTC] in case of Threshold mode.
b. MTL_TxQ0_Operation_Mode[TXQEN] to value 2'b10 to enable transmit queue0.
c. MTL_TxQ0_Operation_Mode[TQS]
4. Program these fields to initialize the mode of operation in MTL_RxQ0_Operation_Mode :
a. MTL_RxQ0_Operation_Mode[RSF] or MTL_RxQ0_Operation_Mode[RTC] in case of Threshold mode.
b. Flow control activation and de-activation thresholds for MTL receive FIFO ( MTL_RxQ0_Operation_Mode[RFA]
and MTL_RxQ0_Operation_Mode[RFD] ).
c. Error Packet and undersized good Packet forwarding enable ( MTL_RxQ0_Operation_Mode[FEP] and
MTL_RxQ0_Operation_Mode[FUP] ).
d. MTL_RxQ0_Operation_Mode[RQS]
5. Repeat previous two steps for all MTL transit and receive queues selected in the configuration.

---

*Page 2637*

Ethernet Media Access Controller (EMAC)

#### 72.16.3 Initializing MAC

The MAC configuration registers establish the operating mode of MAC. These registers must initialize before initializing DMA.
You can perform these MAC initialization operations after DMA initialization. If the MAC initialization completes before DMA is
configured, enable the MAC receiver (last step in the following sequence) only after the DMA is active. Otherwise, the received
frames fill the receive FIFO and overflow.
1. Provide the MAC address registers: MAC_Address0_High and MAC_Address0_Low . If more than one MAC address is
enabled, , program the MAC addresses appropriately.
2. Program these fields to set the appropriate filters for the incoming frames in MAC_Packet_Filter :
a. Receive all
b. Promiscuous mode
c. Hash or perfect filter
d. Unicast, multicast, broadcast, and control frames filter settings
3. Program these fields for proper flow control in MAC_Q0_Tx_Flow_Ctrl :
a. Pause time and other Pause frame control bits
b. Transmit flow control bits
c. Flow control busy
4. Program MAC_Interrupt_Enable as required, and if applicable, for your configuration.
5. Program the appropriate fields in MAC_Configuration . For example Inter-packet gap when transmission and jabber disable.
6. Write 1 to MAC_Configuration[RE] and MAC_Configuration[TE] to start the MAC transmitter and receiver.

#### 72.16.4 Performing normal receive and transmit operation

During normal operation of the module, read the normal and transmit interrupts, poll the descriptors, suspend the DMA (if it does
not own descriptors), and read the values of the current host transmitter or receiver descriptor pointers for debugging.
For normal operation, perform these steps:
1. Read the interrupt status for normal transmit and receive interrupts. Then poll the descriptors, read the status of the descriptor
which the host owns (either transmit or receive).
2. Set appropriate values for the descriptors, ensure that the DMA owns the transmit and receive descriptors to resume the data
transmission and reception.
3. If DMA does not own the descriptors (or no descriptor is available), DMA enters into SUSPEND state. The transmission
or reception can resume by freeing the descriptors and writing the descriptor tail pointer to Tx/Rx tail pointer register
(DMA_CH[n]_TxDesc_Tail_Pointer and DMA_CH[n]_RxDesc_Tail_Pointer).
4. Read the current host transmitter or receiver descriptor address pointer values for the debug process
(DMA_CH[n]_Current_App_TxDesc and DMA_CH[n]_Current_App_RxDesc register).
5. Read the current host transmit buffer address pointer and receive buffer address pointer values for the debug process (Register
DMA_CH[n]_Current_App_TxBuffer and DMA_CH[n]_Cur-rent_App_RxBuffer).

#### 72.16.5 Stopping and starting transmission

Perform these steps to pause the transmission for some time. The steps are provided for channel 0.
1. Write 0 to bit 0 (ST) of DMA_CH(#i)_TX_Control (for i = 0; i <= DWC_EQOS_NUM_DMA_TX_CH-1) register to disable the
transmit DMA (if applicable) .
2. Wait for any previous frame transmissions to complete. Read the appropriate fields of MTL_TxQ0_Debug
( MTL_TxQ0_Debug[TRCSTS] is not 01 and MTL_TxQ0_Debug[TXQSTS] = 0) to check this.

---

*Page 2638*

Ethernet Media Access Controller (EMAC)
3. Write 0 to MAC_Configuration[RE] and MAC_Configuration[TE] to disable the MAC transmitter and MAC receiver.
4. Disable the receive DMA (if applicable), after you ensure that the data in the receive FIFO transfers to the system memory (read
the appropriate fields MTL_RxQ0_Debug[PRXQ] = 0 and MTL_RxQ0_Debug[RXQSTS] = 00).
5. Ensure that both the transit queue and receive queue are empty ( MTL_TxQ0_Debug[TXQSTS] = 0 and
MTL_RxQ0_Debug[RXQSTS] = 0).
6. Restart the operation by first starting the DMAs, and then enable the MAC transmitter and receiver.
NOTE
• Do not change the configuration (such as duplex mode, speed, port, or loop back) when the MAC is actively
transmitting or receiving. You can change these parameters only when the MAC transmitter and receiver are
not active.
• Similarly, do not change the DMA related configuration when transmit and receive DMA are active.

#### 72.16.6 Programming guidelines for switching to new descriptor list in RxDMA

Switching to a new descriptor list is different in the receive DMA as compared to the transit DMA. It is permitted when the RxDMA
is in SUSPEND state as described below:
• RxDMA prepares the descriptors in advance.
• If the RxDMA goes to SUSPEND state because the descriptors are not available, a major failure occurs (you are not able to
free the filled-up descriptors or buffers). If this issue does not rectify immediately, frames will be lost because of an RxFIFO
overflow. Therefore, you can create a new descriptor list and program the RxDMA to start using it immediately, without going
into STOP state.

#### 72.16.7 Programming guidelines for multi-channel multi-queuing

72.16.7.1 Transmit
1. Program the transmit queue size in TQS field of MTL_TxQ[n]_Operation_Mode register. Based on the value programmed in
TQS field, you can determine the queue size.
In the transmit operation, the number of channels is equal to the number of the queues, because of this reason, the
channel-to-queue mapping is fixed.
2. Enable the queue in TXQEN in the corresponding MTL_TxQ[n]_Operation_Mode register to use the queue. In DMA
configurations, enable the ST field of DMA_CH[n]_Tx_Con-trol register and corresponding TXQEN in MTL_TxQ[n]_Operation
Mode register.
3. Program the scheduling method in MTL_Operation_Mode[SCHALG] .
4. Program MTL_TxQ[n]_Quantum_Weight register for DCB queue per the selected algorithm. In case of CBS
algorithm in AVB queues program the MTL_TxQ[n]_ETS_Control, MTL_TxQ[n]_SendSlopeCredit, MTL_TxQ[n]_HiCredit, and
MTL_TxQ[n]_LoCredit registers as required.
5. Program the MAC_TxQ_Prty_Map0 register to assign a fixed priority to the queue, if DCB is enabled and PFC function is
required. You can use this assigned priority to determine if the corresponding queue must stop transmitting packet on the basis
of the received PFC packet.
72.16.7.2 Receive
1. Program the receive queue size in RQS field of MTL_RxQ[n]_Operation_Mode register. Determine the size of the
queue, based on the value programmed in RQS field.
2. Enable the receive queues 0 to 7 in the fields RXQ0EN to RXQ7EN in MAC_RxQ_Ctrl0 for AV or DCB. In
DMA configurations, SR bit of statically or dynamically map DMA_CH[n]_Rx_Control register and the corresponding
RXQ[n]_EN in MAC_RxQ_Ctrl0 is enabled.

---

*Page 2639*

Ethernet Media Access Controller (EMAC)
3. Based on these packet types MAC routes the receive packets to the receive queues :
a. AV PTP packets: Based on the programming of MAC_RxQ_Ctrl1[PTPQ] .
b. AV untagged control packets: Based on the programming of MAC_RxQ_Ctrl1[AVCPQ] .
c. Data center bridging (DCB) related link layer discovery protocol (LLDP) packets. Program DCBCPQ in
MAC_RxQ_Ctrl1 to indicate MAC which queue should get the DCB packets.
d. VLAN tag priority field in VLAN tagged packets: Program PSRQ7-0 of the MAC_RxQ_Ctrl2 and MAC_RxQ_Ctrl3
register for routing the tagged packets based on the received packets USP (user priority) field to the receive
queues 0 to 7.
e. Route the AV tagged control and data packets based on PSRQ field of MAC_RxQ_Ctrl2 and MAC_RxQ_Ctrl3
registers.
NOTE
The priorities set in PSRQ7-0 must be unique.
4. If multiple receive DMA channels are enabled, you must perform the following programming for proper arbitration and
mapping:
a. Program MTL_Operation_Mode[RAA] to select the arbitration algorithm to decide which RxQ is read out from the
RxFIFO memory.
b. Program the MTL_RxQ[n]_Control register to decide the weights and the packet arbitration for each RxQ.
c. If you program the static mapping in MTL_RxQ_DMA_Map[n] register (RXQ[n]DADMACH becomes 0), fields
RXQx2DMA and others are programmed to select the channel to map each queue.
d. Write 1 to RXQ[n]DADMACH field in MTL_RxQ_DMA_Map0 to select the dynamic mapping of packets in each
receive queue.
e. In dynamic channel mapping, the value of DCS field in the lowest MAC address register the decides the routing
of a packet to a specific RxDMA channel.
72.16.7.2.1 Programming guidelines for recovering from DMA channel failure
When the DMA channel issues a bus error, follow these steps to recover from the failure.
Recovering from the receive DMA channel failure.
Perform these steps if you get bus error in the receive DMA channel:
1. Write 1 to DMA_CH0_Rx_Control[RPF] . This flushes all the packets one after the other.
This step is optional. However, writing 1 to this field prevents head-of-line blocking in the receive queues when packets sent to
the RXDMA are stopped due to the bus error.
2. Re-program the specific registers of the DMA channel.
3. Start the DMA channel.
Recovering from the transmit DMA channel failure.
1. Stop the specific DMA channel, even if it is in active state.
2. Flush the corresponding MTL queue.
3. Re-program the specific registers of the DMA channel.
4. Start the DMA channel.
NOTE
Due to the known limitations in the design, reprogramming the DMA channel registers might not be always
successful in recovering from a bus error. If the module is not fully functional after reprogramming the DMA, you
can issue a soft reset to recover from the bus error.

---

*Page 2640*

Ethernet Media Access Controller (EMAC)

#### 72.16.8 Programming guidelines for GMII link state transitions

72.16.8.1 Transmit and receive clocks running when link down
Perform these steps when the link is down but the transmit and receive clocks are running:
NOTE
The steps are provided for channel 0.
1. Write 0 to bit 0 (ST) of DMA_CH(#i)_TX_Control (for i = 0; i <= DWC_EQOS_NUM_DMA_TX_CH-1) register to disable the
transmit DMA (if applicable).
2. Write 0 to MAC_Configuration[RE] to disable the MAC receiver.
3. Wait for any previous frame transmissions to complete. You can check this by reading the appropriate fields of
MTL_TxQ0_Debug ( MTL_TxQ0_Debug[TRCSTS] is not 01) or, flush the Tx FIFO for faster empty operation.
4. Write 0 to MAC_Configuration[TE] to disable the MAC transmitter.
5. Make sure that both the transit queue and receive queue are empty ( MTL_TxQ0_Debug[TXQSTS] and
MTL_RxQ0_Debug[RXQSTS] are 0).
6. Read the PHY registers to know the latest configuration and accordingly program the MAC registers, after the link is up.
7. Start the transit DMA to restart the operation, and then enable the MAC transmitter and receiver.
You do not disable the receive DMA because the receiver is disabled and the FIFO does not get any data in the receive FIFO.
72.16.8.2 Transmit and receive clocks stopped when link down
Perform these steps when the link is down and the transmit and receive clocks are stopped:
NOTE
The steps are provided for channel 0.
1. Write 0 to MAC_Configuration[RE] and MAC_Configuration[TE] to disable the MAC transmitter and receiver. This does not take
effect immediately as the clocks are absent.
2. Wait until the link is up and the clocks are restored.
3. Wait for the transfer of any partial frame, if any to complete at time of stopping of the transmit or receive clock. To check this,
read MAC_Debug (should be all-zero). Some old packets may still remain in the TXFIFO because the MAC transmitter is stopped.
4. Read the PHY registers to know the latest operating mode and accordingly program the MAC registers.
5. Write 1 to MAC_Configuration[RE] and MAC_Configuration[TE] to restart the MAC transmitter and receiver.

#### 72.16.9 Programming guidelines for IEEE 1588 timestamping

72.16.9.1 Initialization guidelines for system time generation
Write 1 MAC_Timestamp_Control[TSENA] to enable the timestamp feature. However, it is essential to initialize the timestamp
counter after MAC_Timestamp_Control[TSENA] = 1. Perform these steps during the module initialization:
1. Write 0 to the bit 16 of MAC_Interrupt_Enable to mask the timestamp trigger interrupt.
2. Write 1 to MAC_Timestamp_Control[TSENA] to enable timestamping.
3. Program MAC_Sub_Second_Increment based on the PTP clock frequency.
4. Program MAC_Timestamp_Addend and write 1 to MAC_Timestamp_Control[TSADDREG] , if you are using the fine
correction approach.
5. Poll MAC_Timestamp_Control until MAC_Timestamp_Control[TSADDREG] = 0.
6. Program MAC_Timestamp_Control[TSCFUPDT] to select the fine update method (if required).

---

*Page 2641*

Ethernet Media Access Controller (EMAC)
7. Program MAC_System_Time_Seconds_Update and MAC_System_Time_Nanoseconds_Update with the appropriate
time value.
8. Write 1 to MAC_Timestamp_Control[TSINIT] .
9. Initialize the timestamp counter with the value written in the timestamp update registers so that the timestamp counter
can start operation. If one-step timestamping is enabled
a. Program Bit 27 of the TDES3 context descriptor, to enable one-step timestamping.
b. Program MAC_Timestamp_Ingress_Asym_Corr and MAC_Timestamp_Egress_Asym_Corr to update the
correction field in PDelay_Req PTP messages.
10. Enable the MAC receiver and transmitter for proper timestamping.
NOTE
If the timestamp operation is disabled by writing 0 to MAC_Timestamp_Control[TSENA] , repeat all these steps to
restart the timestamp operation.
72.16.9.2 System time correction
72.16.9.2.1 Coarse correction method
Perform these steps to synchronize or update the system time in one process (coarse correction method):
1. Set the offset (positive or negative) in the timestamp registers that is MAC_System_Time_Seconds_Update
and MAC_System_Time_Nanoseconds_Update .
2. Write 1 to MAC_Timestamp_Control[TSUPDT] .
The value in the timestamp update registers is added to or subtracted from the system time when
MAC_Timestamp_Control[TSUPDT] = 0.
72.16.9.2.2 Fine correction method
Perform these steps to synchronize or update the system time to reduce system-time jitter (fine correction method):
1. Calculate the rate by which you want to increment the system time slower or faster, with the help of the algorithm.
2. Update MAC_Timestamp_Addend with the new value and write 1 to MAC_Timestamp_Control[TSADDREG] .
3. Wait for the time for which you want the new value of the addend register to be active. You can do this by enabling the timestamp
trigger interrupt after the system time reaches the target value.
4. Program the required target time in MAC_PPS[n]_Target_Time_Seconds register and
MAC_PPS[n]_Target_Time_Nanoseconds register.
5. Enable the timestamp interrupt in MAC_Interrupt_Enable[TSIE] .
6. Write 1 to bit 4 in MAC_Timestamp_Control .
7. Read MAC_Interrupt_Status when this trigger causes an interrupt.
8. Reprogram MAC_Timestamp_Addend with the old value and write 1 to MAC_Timestamp_Control[TSADDREG] again.

#### 72.16.10 Programming guidelines for AV feature

After you enable the AV feature in the module controller, follow these programming tasks:
• Initializing the DMA for QOS-AHB configurations only
• Enabling slot number checking
• Enabling average bits per slot reporting
• Disabling flow control for AV-enabled queues (transmit and receive flow control)

---

*Page 2642*

Ethernet Media Access Controller (EMAC)
72.16.10.1 Initializing the DMA in audio video feature
The first step to program the AV feature in a QOS-AHB configuration is to initialize the DMA.
Use this initialization sequence for QOS-AHB/QOS-AXI/QOS-AXI4 configurations with AV feature.
1. Provide a software reset to reset all the QOS internal registers and logic ( DMA_Mode[SWR] ).
2. Wait for the reset process to complete. Poll DMA_Mode[SWR] , which clears only after the reset operation completes.
3. Set the values in DMA_Mode to program the fields to initialize the DMA register.
4. Create a descriptor list for transmit and receive. In addition, ensure that the DMA owns the transmit and receive descriptors.
When you use OSF mode, at least two transmit descriptors are required.
See Descriptors for more information about descriptors.
5. Ensure that you create three or more different transmit or receive descriptors in the list before reusing any descriptors.
6. Program the transmit and receive ring length registers (DMA_CH(#i)_TxDesc_Ring_Length (for i
= 0; i <= DWC_EQOS_NUM_DMA_TX_CH-1) and DMA_CH(#i)_RxDesc_Ring_Length (for i = 0; i <=
DWC_EQOS_NUM_DMA_RX_CH-1)). The ring length programmed must be at least 4.
7. Initialize receive and transmit descriptor list address with the base address of the transmit and receive descriptor
(DMA_CH(#i)_TxDesc_List_Address (for i = 0; i <= DWC_EQOS_NUM_DMA_TX_CH-1), DMA_CH(#i)_TxDesc_List_Address (for
i = 0; i <= DWC_EQOS_NUM_DMA_TX_CH-1)). In addition, you must program the transmit and receive tail pointer registers
to indicate the DMA about the available descriptors (DMA_CH(#i)_TxDesc_Tail_Pointer (for i = 0; i <= DWC_EQOS_NUM_D-
MA_TX_CH-1) and DMA_CH(#i)_RxDesc_Tail_Pointer (for i = 0; i <= DWC_EQOS_NUM_D-MA_RX_CH-1)).
8. Program these fields to initialize the mode of operation in MTL_TxQ0_Operation_Mode :
a. MTL_TxQ0_Operation_Mode[TSF]
b. MTL_TxQ0_Operation_Mode[TTC]
c. MTL_TxQ0_Operation_Mode[TXQEN] to value 2‘b10 to enable transmit queue0
d. MTL_TxQ0_Operation_Mode[TQS]
9. Enable the interrupts by programming DMA_CH(#i)_Interrupt_Enable (for i = 0; i <=
DWC_EQOS_NUM_DMA_TX_CH-1) register.
10. Repeat steps 4 through 9 for all the additional channels of AV feature.
11. Program the AV queues CBS control register, idleSlope, sendSlope, hiCredit, and loCredit registers.
12. Write 1 to bit 0 of DMA_CH(#i)_TX_Control (for i = 0; i <= DWC_EQOS_NUM_DMA_TX_CH-1) and DMA_CH(#i)_RX_Control
(for i = 0; i <= DWC_EQOS_NUM_DMA_RX_CH-1) register to start the receive and transmit DMA.
72.16.10.2 Enabling slot number checking
You can enable slot number checking feature if you specifies the intervals at which the DMA channels map to AV queues fetch
the frames from the AHB/AXI system bus.
You must complete these steps after step 11 and before step 12 of Initializing the DMA in audio video feature .
You can use the slot number check feature to specify the intervals at which the DMA channels map to AV queues fetch the frames
from the AHB/AXI system bus. This feature is useful for an uniform and periodic transfer of the AV traffic from the host memory and
it is available only when you enable timestamping and programs MAC_Sub_Second_Increment . Perform these steps to enable
the slot number checking:
1. Follow the steps described in Initialization guidelines for system time generation to enable timestamping.
2. Ensure that the SLOTNUM field (bits 22:19) of TDES3 normal descriptor (Read format) contains a valid slot number.
You can read the current reference slot number from the DMA_CH(#i)_Slot_Func-tion_Control_Status (for i = 0; i <=
DWC_EQOS_NUM_DMA_TX_CH-1) register.
3. Write 1 to bit 0 (ESC) of the slot function control and status register of a channel to enable the slot number checking.

---

*Page 2643*

Ethernet Media Access Controller (EMAC)
72.16.10.3 Enabling average bits per slot reporting
You can enable the reporting of average bits transmitted in a slot.
The CBS status register of the additional AV channels provide information about the average bits that are transmitted in a slot.
You can asynchronously read this register to retrieve information about the average bits transmitted per slot. Perform these steps
to enable average bits per slot reporting:
1. Follow the steps as described in the Initialization guidelines for system time generation to enable timestamping.
2. Program SLC bits [6:4], of the MTL_TxQ[n]_ETS_Control register of a channel with the number of slots over which the average
transmitted bits per slot are computed.
3. Enable bit 9(ABPSSIE) of the MTL_Q[n]_Interrupt_Control_Status register of a channel to generate the average bits per
slot interrupt.
NOTE
• The frequency of this interrupt depends on the value programmed in step 2. For example, when you program
value 0 in the SLC field, the interrupt generates at every 125 microsecond.
• You can disable this interrupt to stop the interrupt flooding, when it is not required.
4. Read ABS bits [16:0], of the MTL_TxQ[n]_ETS_Status register of a channel on each interrupt.
NOTE
You can read the ABS fields in the polling mode even if ABPSIE bit is not enabled. Whe