<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 72 -->

t is enabled.
When this field is 1, it indicates that an early transmit interrupt (ETI) status = 1 after the data transfer from
buffers of a transmit descriptor completes in which IOC bit (TDES2[31]) = 1.
When this field becomes 1, it indicates that ETI = 1 only after a complete packet transfers to the MTL TX
FIFO memory.
0b - Disabled
1b - Enabled
21-16 Transmit Programmable Burst Length
TxPBL Indicates the maximum number of beats to transfer in one DMA block data transfer. DMA always attempts
max burst as specified in PBL each time it starts a burst transfer on the application bus. You can program
PBL with any of these values: 1, 2, 4, 8, 16, or 32. Any other value results in undefined behavior.
Perform these steps to transfer more than 32 beats:
1. Set DMA_CH0_Control[PBLx8] .
2. Set the TxPBL.
NOTE
The maximum value of TxPBL must be less than or equal to half the transit queue size (TQS
field of MTL_TxQ(#i)_Operation_Mode register) in terms of beats. This is required so that
the transit queue has space to store at least another TxPBL worth of data while the MTL Tx
queue controller transfers data to MAC. For example, in 64-bit data width configurations the
total locations in transit queue of size 512 bytes is 64. You must program TxPBL and 8xPBL
to less than or equal to 32.
15 Reserved.
—
14-13 Reserved.
—
12 Reserved.
—
11-5 Reserved.
—
Table continues on the next page...

---

*Page 3048*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
4 Operate on Second Packet
OSF Indicates whether the operation on second packet is enabled.
When this field is 1, it instructs DMA to process the second packet of the transmit data even before you
receive the status for the first packet.
0b - Disabled
1b - Enabled
3-1 Transmit Channel Weight
TCW Indicates the weight assigned to the corresponding transmit channel. When reset completes, this field is 0
for all the channels by default, which results in equal weights to all channels.
0 Start or Stop Transmission Command
ST Indicates whether to start or stop the transmission command.
When this field is 1, it indicates that the transmission is in the running state. DMA checks the transmit list at
the current position for a packet to be transmitted.
DMA tries to acquire descriptor from either of these positions:
• The current position in the list. This is the base address of the transmit list to which
DMA_CH0_TxDesc_List_Address sets.
• The position at which the transmission was previously stopped.
The transmission enters the suspended state and DMA_CH0_Status[TBU] = 1, if DMA does not own the
current descriptor. The start transmission command is effective only when the transmission stops. If the
command is issued before setting DMA_CH0_TxDesc_List_Address , you cannot predict the DMA behavior.
When this field becomes 0, it indicates that the transmission process is placed in the stopped state when
the transmission of the current packet completes . The next descriptor position in the transmit list is saved,
and it becomes the current position when you restart the transmission. To change the list address, you
must program DMA_CH0_TxDesc_List_Address with a new value when this field becomes 0.You can
consider the new value when this field is 1 again. The stop transmission command is effective only when
the transmission of the current packet completes or the transmission is in the suspended state.
0b - Stop
1b - Start

#### 72.17.247 DMA Channel Rx Control (DMA_CH0_Rx_Control)

Offset
Register Offset
DMA_CH0_Rx_Control 1108h
Function
Controls the receive features such as PBL, buffer size, and extended status.

---

*Page 3049*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv
RPF Reserved Reserved ERIC RxPBL
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RBSZ_x_0
Reserv
RBSZ_13_y SR
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Rx Packet Flush
RPF Indicates whether the receive packet flush is enabled.
When this field is 1, it indicates that the module automatically flushes the packet from the receive queues
destined to this DMA receive channel, when it stops. When this field remains 1 and the software driver
re-starts DMA, the packets residing in the receive queues that were received when this RxDMA stops and
flushes out. The packets that MAC receives after the RxDMA re-starts, route to the RxDMA. The flushing
takes place on the read side of the receive queue.
When this field is 0, it indicates that the module do not flush the packet in the receive queue destined to
this RxDMA channel when it is in STOP state. This may cause head-of-line blocking in the corresponding
receive queue.
NOTE
The packet flow from a receive DMA channel to the application by writing 1 to this field
stops, only when there is one-to-one mapping of receive queue to receive DMA channels.
In Dynamic mapping mode, writing 1 to this field in any DMA_CH(#i)_Rx_Control register
might flush packets from an unintended receive queues which are destined to the stopped
receive DMA channel.
0b - Disabled
1b - Enabled
30-28 Reserved.
—
27-24 Reserved.
—
23 Reserved.
—
22 Early Receive Interrupt Control
Table continues on the next page...

---

*Page 3050*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
ERIC Indicates whether an early receive interrupt is enabled.
When this field is 1, it indicates that the early receive interrupt (ERI) status sets after every burst transfer of
data from the Rx DMA to the buffer completes.
When this field becomes 0, it indicates that ERI sets only after a complete buffer is filled by the RxDMA.
0b - Disabled
1b - Enabled
21-16 Receive Programmable Burst Length
RxPBL Indicates the maximum number of beats to be transferred in one DMA block data transfer. DMA always
attempts max burst as specified in PBL every time it starts a burst transfer on the application bus. You can
program PBL with any of these values: 1, 2, 4, 8, 16, or 32. Any other value results in undefined behavior.
To transfer more than 32 beats, perform the following steps:
1. Set DMA_CH0_Control[PBLx8] .
2. Set the RxPBL.
NOTE
The maximum value of RxPBL must be less than or equal to half the receive queue size
(RQS field of MTL_RxQ(#i)_Operation_Mode register) in terms of beats. This is required
so that the receive queue has space to store at least another Rx PBL worth of data while
the receive DMA transfers a block of data. For example, in 64-bit data width configurations
the total locations in receive queue of size 512 bytes is 64. You must program RxPBL and
8xPBL to less than or equal to 32.
15 Reserved.
—
14-3 Receive Buffer size High
RBSZ_13_y RBSZ[13:0] splits into two fields, higher field is RBSZ_13_y and lower field is RBSZ_x_0. RBSZ[13:0] field
indicates the receive buffers size specified in bytes. The maximum buffer size is limited to 16K bytes. The
buffer size is applicable to payload buffers when split headers are enabled.
NOTE
The buffer size must be a multiple of 4, 8, or 16 depending on the data bus widths (32-bit,
64-bit, or 128-bit respectively). This is required even if the buffer address pointer value is not
aligned with data bus width. Hence the lower RBSZ_x_0 fields are read-only and the value is
considered as all-zero. Thus RBSZ_13_y field indicates the buffer size in terms of locations
(with the width same as bus-width).
2-1 Receive Buffer size Low
RBSZ_x_0 RBSZ[13:0] splits into two fields RBSZ_13_y and RBSZ_x_0. The RBSZ_x_0 is the lower field whose width
is based on data bus width of the configuration.
The field width is of 2, 3, or 4 bits for 32-bit, 64-bit, or 128-bit data bus width respectively. This field is
read-only (RO).
Table continues on the next page...

---

*Page 3051*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0 Start or Stop Receive
SR Indicates whether to start or stop receive.
When this field is 1, it indicates that the DMA tries to acquire the descriptor from the receive list and
processes the incoming packets.
DMA tries to acquire descriptor from either of these positions:
• The current position in the list. This is the address which DMA_CH0_RxDesc_List_Address sets.
• The position at which the receive process was previously stopped.
If DMA does not own the current descriptor, the reception is suspended and DMA_CH0_Status[RBU] = 1.
The start receive command is effective only when the reception stops. If the command is issued before
setting DMA_CH0_RxDesc_List_Address , the DMA behavior is unpredictable.
When this field becomes 0, it indicates that the receive DMA operation stops after the transfer of the current
packet. The next descriptor position in the receive list is saved, and it becomes the current position after
the receive process restarts. The stop receive command is effective only when the receive process is in the
running (waiting for receive packet) or suspended state.
0b - Stop
1b - Start

#### 72.17.248 DMA Channel 0 Tx Descriptor List Address (DMA_CH0_TxDesc_List_Address)

Offset
Register Offset
DMA_CH0_TxDesc_List_ 1114h
Address
Function
Specifies DMA to the start of transmit descriptor list. The descriptor lists reside in the physical memory space of the application
and must be Word, Dword, or Lword-aligned (for 32-bit, 64-bit, or 128-bit data bus). DMA internally converts it to bus width
aligned address by making the corresponding LSB low.
You can write to this register only when the transit DMA stops, that is, DMA_CH0_Tx_Control[ST] = 0. When stopped, write this
register with a new descriptor list address. When DMA_CH0_Tx_Control[ST] = 1, DMA uses the newly-programmed descriptor
base address. If this register does not change when DMA_CH0_Tx_Control[ST] = 0, DMA uses the descriptor address where it
was stopped earlier.

---

*Page 3052*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TDESLA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TDESLA Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-2 Start of Transmit List
TDESLA Contains the base address of the first descriptor in the transmit descriptor list. DMA ignores the LSB bits (1:0,
2:0, or 3:0) for 32-bit, 64-bit, or 128-bit bus width and internally takes these bits as all-zero. Therefore, these
LSB bits are read-only (RO).
The field width depends on the configuration:
31:2 for 32-bit configuration
31:3 for 64-bit configuration
31:4 for 128-bit configuration
1-0 Reserved.
—

#### 72.17.249 DMA Channel 0 Rx Descriptor List Address (DMA_CH0_RxDesc_List_Address)

Offset
Register Offset
DMA_CH0_RxDesc_List 111Ch
_Address
Function
Specifies DMA to the start of receive descriptor list. The descriptor lists reside in the physical memory space of the application
and must be Word, Dword, or Lword-aligned (for 32-bit, 64-bit, or 128-bit data bus). DMA internally converts it to bus width aligned
address by making the corresponding LS bits low. You can write to this register only when reception stops. When stopped,
write this register before the receive start command is given. You can write to this register only when Rx DMA stops, that is,
DMA_CH0_Rx_Control[SR] = 0. When stopped, write this register with a new descriptor list address.
When DMA_CH0_Rx_Control[SR] = 1, it indicates that DMA takes the newly programmed descriptor base address.

---

*Page 3053*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
RDESLA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
RDESLA Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-2 Start of Receive List
RDESLA Contains the base address of the first descriptor in the receive descriptor list. DMA ignores the LSB bits (1:0,
2:0, or 3:0) for 32-bit, 64-bit, or 128-bit bus width and internally takes these bits as all-zero. Therefore, these
LSB bits are read-only (RO).
The field width depends on the configuration:
31:2 for 32-bit configuration
31:3 for 64-bit configuration
31:4 for 128-bit configuration
1-0 Reserved.
—

#### 72.17.250 DMA Channel 0 Tx Descriptor Tail Pointer (DMA_CH0_TxDesc_Tail_Pointer)

Offset
Register Offset
DMA_CH0_TxDesc_Tail_ 1120h
Pointer
Function
Points to an offset from the base and indicates the location of the last valid descriptor.

---

*Page 3054*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TDTP
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TDTP Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-2 Transmit Descriptor Tail Pointer
TDTP Contains the tail pointer for the transit descriptor ring. You must write the tail pointer to add more descriptors
to the transit channel. The hardware tries to transmit all packets to the descriptors referenced between the
head and the tail pointer registers.
The field width depends on the configuration:
31:2 for 32-bit configuration
31:3 for 64-bit configuration
31:4 for 128-bit configuration
1-0 Reserved.
—

#### 72.17.251 DMA Channel 0 Rx Descriptor List Pointer (DMA_CH0_RxDesc_Tail_Pointer)

Offset
Register Offset
DMA_CH0_RxDesc_Tail 1128h
_Pointer
Function
Specifies an offset from the base and indicates the location of the last valid descriptor.

---

*Page 3055*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
RDTP
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
RDTP Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-2 Receive Descriptor Tail Pointer
RDTP Contains the tail pointer for the receive descriptor ring. You must write the tail pointer to add more descriptors
to the receive channel. The hardware tries to write all received packets to the descriptors referenced
between the head and the tail pointer registers.
The field width depends on the configuration:
31:2 for 32-bit configuration
31:3 for 64-bit configuration
31:4 for 128-bit configuration
1-0 Reserved.
—

#### 72.17.252 DMA Channel 0 Tx Descriptor Ring Length (DMA_CH0_TxDesc_Ring_Length)

Offset
Register Offset
DMA_CH0_TxDesc_Ring 112Ch
_Length
Function
Contains the length of the transmit descriptor ring.

---

*Page 3056*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved TDRL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-10 Reserved.
—
9-0 Transmit Descriptor Ring Length
TDRL Sets the maximum number of transmit descriptors in the circular descriptor ring. The maximum number
of descriptors are limited to 1K descriptors. NXP recommends a minimum ring descriptor length of 4. For
example, you can program any value up to 0x3FF in this field. This field is 10 bits wide, if you program 0x3FF,
you can have 1024 descriptors. Program this field to a value of 0x9, if you want to have 10 descriptors.

#### 72.17.253 DMA Channel 0 Rx Descriptor Ring Length (DMA_CH0_RxDesc_Ring_Length)

Offset
Register Offset
DMA_CH0_RxDesc_Rin 1130h
g_Length
Function
Contains the length of the receive descriptor circular ring.

---

*Page 3057*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved RDRL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-10 Reserved.
—
9-0 Receive Descriptor Ring Length
RDRL Sets the maximum number of receive descriptors in the circular descriptor ring. The maximum number of
descriptors is limited to 1K descriptors. For example, you can program any value up to 0x3FF in this field.
This field is 10 bits wide, if you program 0x3FF, you can have 1024 descriptors. Program this field to a value
of 0x9, if you want to have 10 descriptors.

#### 72.17.254 DMA Channel 0 Interrupt Enable (DMA_CH0_Interrupt_Enable)

Offset
Register Offset
DMA_CH0_Interrupt_Ena 1134h
ble
Function
Enables the interrupts which the status register report.

---

*Page 3058*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
NIE AIE CDEE FBEE ERIE ETIE RWTE RSE RBUE RIE Reserved TBUE TXSE TIE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-16 Reserved.
—
15 Normal Interrupt Summary Enable
NIE Enables or disables the normal interrupt summary.
When this field is 1, it enables the normal interrupt summary. This field also enables the following interrupts
in DMA_CH0_Status :
-Bit 0 - Transmit interrupt
Bit 2 - Transmit buffer unavailable
Bit 6 - Receive interrupt
Bit 11 - Early receive interrupt
When this field becomes 0, it disables the normal interrupt summary.
0b - Disable
1b - Enable
14 Abnormal Interrupt Summary Enable
AIE Enables or disables the abnormal interrupt summary.
When this field is 1, it enables the abnormal interrupt summary. This field also enables the following
interrupts in DMA_CH0_Status :
Bit 1 - Transmit process stopped
Bit 7 - Rx buffer unavailable
Bit 8 - Receive process stopped
Bit 9 - Receive watchdog timeout
Bit 10 - Early transmit interrupt
Bit 12 - Fatal bus error
Bit 13 - Context descriptor error
Table continues on the next page...

---

*Page 3059*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
When this field becomes 0, it disables the abnormal interrupt summary.
0b - Disable
1b - Enable
13 Context Descriptor Error Enable
CDEE Enables or disables the context descriptor error.
When this field is 1 along with DMA_CH0_Interrupt_Enable[AIE] , it enables the descriptor error interrupt.
When this field becomes 0, it disables the descriptor error interrupt.
0b - Disable
1b - Enable
12 Fatal Bus Error Enable
FBEE Enables or disables the fatal bus error.
When this field is 1 along with DMA_CH0_Interrupt_Enable[AIE] , it enables the fatal bus error interrupt.
When this field becomes 0, it disables the fatal bus error interrupt.
0b - Disable
1b - Enable
11 Early Receive Interrupt Enable
ERIE Enables or disables the early receive interrupt.
When this field is 1 along with DMA_CH0_Interrupt_Enable[NIE] , it enables the early receive interrupt. When
this field becomes 0, it disables the early receive interrupt.
0b - Disable
1b - Enable
10 Early Transmit Interrupt Enable
ETIE Enables or disables the early transmit interrupt.
When this field is 1 along with DMA_CH0_Interrupt_Enable[AIE] , it enables the early transmit interrupt.
When this field becomes 0, it disables the early transmit interrupt.
0b - Disable
1b - Enable
9 Receive Watchdog Timeout Enable
RWTE Enables or disables the receive watchdog timeout.
When this field is 1 along with DMA_CH0_Interrupt_Enable[AIE] , it enables the receive watchdog timeout
interrupt. When this field becomes 0, it disables the receive watchdog timeout interrupt.
0b - Disable
1b - Enable
Table continues on the next page...

---

*Page 3060*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
8 Receive Stopped Enable
RSE Enables or disables the receive stopped interrupt.
When this field is 1 along with DMA_CH0_Interrupt_Enable[AIE] , it enables the receive stopped interrupt.
When this field becomes 0, it disables the receive stopped interrupt.
0b - Disable
1b - Enable
7 Receive Buffer Unavailable Enable
RBUE Enables or disables the receive buffer unavailable interrupt.
When this field is 1 along with DMA_CH0_Interrupt_Enable[AIE] , it enables the receive buffer unavailable
interrupt. When this field becomes 0, it disables the receive buffer unavailable interrupt.
0b - Disable
1b - Enable
6 Receive Interrupt Enable
RIE Enables or disables the receive interrupt.
When this field is 1 along with DMA_CH0_Interrupt_Enable[NIE] , it enables the receive interrupt. When this
field becomes 0, it disables the receive interrupt.
0b - Disable
1b - Enable
5-3 Reserved.
—
2 Transmit Buffer Unavailable Enable
TBUE Enables or disables the transmit buffer unavailable interrupt.
When this field is 1 along with DMA_CH0_Interrupt_Enable[NIE] , it enables the transmit buffer unavailable
interrupt. When this field becomes 0, it disables the transmit buffer unavailable interrupt.
0b - Disable
1b - Enable
1 Transmit Stopped Enable
TXSE Enables or disables the transmit stopped interrupt.
When this field is 1 along with DMA_CH0_Interrupt_Enable[AIE] , it enables the transmission stopped
interrupt. When this field becomes 0, it disables the transmission stopped interrupt.
0b - Disable
1b - Enable
0 Transmit Interrupt Enable
Table continues on the next page...

---

*Page 3061*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
TIE Enables or disables the transmit interrupt.
When this field is 1 along with DMA_CH0_Interrupt_Enable[NIE] , it enables the transmit interrupt. When this
field becomes 0, it disables the transmit interrupt.
0b - Disable
1b - Enable

#### 72.17.255 DMA Channel 0 Rx Interrupt Watchdog Timer (DMA_CH0_Rx_Interrupt_Watchdog_Timer)

Offset
Register Offset
DMA_CH0_Rx_Interrupt_ 1138h
Watchdog_Timer
Function
Indicates the watchdog timeout for receive interrupt (RI) from DMA. When you write this register with a non-zero value, it
enables the watchdog timer for the RI bit of the DMA_CHi_Status register.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved RWTU
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved RWT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-18 Reserved.
—
17-16 Receive Interrupt Watchdog Timer Count Units
RWTU Indicates the system clock cycles number corresponding to one unit
in DMA_CH0_Rx_Interrupt_Watchdog_Timer[RWT] .
Table continues on the next page...

---

*Page 3062*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
2'b00 - 256
2'b01 - 512
2'b10 - 1024
2'b11 - 2048
For example, when DMA_CH0_Rx_Interrupt_Watchdog_Timer[RWT] = 2 and
DMA_CH0_Rx_Interrupt_Watchdog_Timer[RWTU] = 1, the watchdog timer sets for 2*512=1024 system
clock cycles.
15-8 Reserved.
—
7-0 Receive Interrupt Watchdog Timer Count
RWT Indicates the number of system clock cycles, multiplied by factor which
DMA_CH0_Rx_Interrupt_Watchdog_Timer[RWTU] indicates, for which you set the watchdog timer.
The watchdog timer triggers with the programmed value after the receive DMA completes the packet
transfer for which the RI bit is not set in the DMA_CH(#i)_Status register, because of the setting of interrupt
enable bit in the corresponding descriptor RDES3[30].
When the watchdog timer runs out, it indicates that the RI bit is 1 and the timer stops. The watchdog timer
resets when the RI bit is 1 because of automatic setting of RI per the interrupt enable bit RDES3[30] of any
received packet.

#### 72.17.256 DMA Channel 0 Slot Function Control Status (DMA_CH0_Slot_Function_Control_Status)

Offset
Register Offset
DMA_CH0_Slot_Functio 113Ch
n_Control_Status
Function
Contains the control bits for slot function and the status for transmit path.

---

*Page 3063*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RSN
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SIV Reserved ASC ESC
W
Reset 0 0 0 0 0 1 1 1 1 1 0 0 0 0 0 0
Fields
Field Function
31-20 Reserved.
—
19-16 Reference Slot Number
RSN Provides the current value of the reference slot number in DMA. It is used for slot comparison.
15-4 Slot Interval Value
SIV Controls the period of the slot interval in which the TxDMA fetches the scheduled packets. A value of 0
specifies the slot interval of 1 us while the maximum value 4095 specifies the slot interval of 4096 us. The
default or reset value is 0x07C which corresponds to slot interval of 125 us
3-2 Reserved.
—
1 Advance Slot Check
ASC Indicates whether the advance slot check is enabled.
When this field is 1, it enables DMA to fetch the data from the buffer when the slot number (SLOTNUM)
programmed in the transit descriptor is either:
Equal to the reference slot number given in DMA_CH0_Slot_Function_Control_Status[RSN] , or
Ahead of the reference slot number by up to two slots.
This field is applicable only when DMA_CH0_Slot_Function_Control_Status[ESC] = 1.
0b - Disabled
1b - Enabled
0 Enable Slot Comparison
ESC Indicates whether the slot comparison is enabled.
Table continues on the next page...

---

*Page 3064*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
When this field is 1, it enables the checking of the slot numbers programmed in the transit descriptor with
the current reference given in DMA_CH0_Slot_Function_Control_Status[RSN] . DMA fetches the data from
the corresponding buffer only when the slot number is either:
• Equal to the reference slot number, or
• Ahead of the reference slot number by one slot.
When this field becomes 0, it disables the checking of the slot numbers. DMA fetches the data immediately
after you process the descriptor.
NOTE
You must not enable the UFO (UDP Fragmentation over IPv4)/TSO/USO along with
TBS/AVB slot number check. The UFO/TSO/USO involves multiple packets/segments/
fragments transmission for single packet received from application and the slot number
check are applicable for fetching only first segment/fragment. As a result it might be difficult
for you to specify slot number for subsequent packets.
0b - Disabled
1b - Enabled

#### 72.17.257 DMA Channel 0 Current Application Transmit Descriptor (DMA_CH0_Current_App_TxDesc)

Offset
Register Offset
DMA_CH0_Current_App 1144h
_TxDesc
Function
Specifies the current transmit descriptor which DMA reads.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R CURTDESAPTR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R CURTDESAPTR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 3065*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Application Transmit Descriptor Address Pointer
CURTDESAPT Indicates that DMA updates this pointer during the transit operation. This pointer clears when reset.
R

#### 72.17.258 DMA Channel 0 Current Application Receive Descriptor (DMA_CH0_Current_App_RxDesc)

Offset
Register Offset
DMA_CH0_Current_App 114Ch
_RxDesc
Function
Specifies the current receive descriptor which DMA read.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R CURRDESAPTR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R CURRDESAPTR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Application Receive Descriptor Address Pointer
CURRDESAPT Indicates that the DMA updates this pointer during the receive operation. This pointer clears when reset.
R

---

*Page 3066*

Ethernet Media Access Controller (EMAC)

#### 72.17.259 DMA Channel 0 Current Application Transmit Descriptor

#### (DMA_CH0_Current_App_TxBuffer)

Offset
Register Offset
DMA_CH0_Current_App 1154h
_TxBuffer
Function
Specifies the current transit buffer address which DMA reads.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R CURTBUFAPTR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R CURTBUFAPTR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Application Transmit Buffer Address Pointer
CURTBUFAPT Indicates that DMA updates this pointer during the transit operation. This pointer clears when reset.
R

#### 72.17.260 DMA Channel 0 Current Application Receive Buffer (DMA_CH0_Current_App_RxBuffer)

Offset
Register Offset
DMA_CH0_Current_App 115Ch
_RxBuffer
Function
Specifies the current receive buffer address which DMA read.

---

*Page 3067*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R CURRBUFAPTR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R CURRBUFAPTR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Application Receive Buffer Address Pointer
CURRBUFAPT Indicates that DMA updates this pointer during the receive operation. This pointer clears when reset.
R

#### 72.17.261 DMA Channel 0 Status (DMA_CH0_Status)

Offset
Register Offset
DMA_CH0_Status 1160h
Function
Indicates that the software driver (application) reads the status register during an interrupt service routine or polling to
determine the DMA status.
NOTE
The number of DMA_CH(#i)_Status register in the configuration is the higher of number of receive DMA channels
and transit DMA channels.

---

*Page 3068*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R REB TEB
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
NIS AIS CDE FBE ERI ETI RWT RPS RBU RI Reserved TBU TPS TI
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-22 Reserved.
—
21-19 Rx DMA Error Bits
REB Indicates the type of error that causes a bus error. For example, error response on the AHB or AXI interface.
Bit 21
1'b1 - Error during data transfer by Rx DMA
1'b0 - No Error during data transfer by Rx DMA
Bit 20
1'b1 - Error during descriptor access
1'b0 - Error during data buffer access
Bit 19
1'b1 - Error during read transfer
1'b0 - Error during write transfer
This field is valid only when DMA_CH0_Status[FBE] = 1. This field does not generate an interrupt.
18-16 Tx DMA Error Bits
TEB
Indicates the type of error that causes a bus error. For example, error response on the AHB or AXI interface.
Bit 18
1'b1 - Error during data transfer by Tx DMA
1'b0 - No Error during data transfer by Tx DMA
Bit 17
1'b1 - Error during descriptor access
1'b0 - Error during data buffer access
Table continues on the next page...

---

*Page 3069*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
Bit 16
1'b1 - Error during read transfer
1'b0 - Error during write transfer
This field is valid only when DMA_CH0_Status[FBE] = 1. This field does not generate an interrupt.
15 Normal Interrupt Summary
NIS Indicates whether the normal interrupt summary status is detected.
This field is the logical OR of the following bits when you enables the corresponding interrupt bits
in DMA_CH0_Interrupt_Enable :
Bit 0 - Transmit interrupt
Bit 2 - Transmit buffer unavailable
Bit 6 - Receive interrupt
Bit 11 - Early receive interrupt
Only unmasked bits (interrupts for which interrupt enable sets in DMA_CH0_Interrupt_Enable ) affect
this field.
This is a sticky bit. You must clear this field by writing 1 to it each time a corresponding field which sets this
field clears.
Access restriction apply to this field. It automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
14 Abnormal Interrupt Summary
AIS Indicates whether an abnormal interrupt summary status is detected.
This field is the logical OR of the following bits when you enables the corresponding interrupt bits
in DMA_CH0_Interrupt_Enable :
Bit 1 - Transmit process stopped
Bit 7 - Receive buffer unavailable
Bit 8 - Receive process stopped
Bit 10 - Early transmit interrupt
Bit 12 - Fatal bus error
Bit 13 - Context descriptor error
Only unmasked bits affect this field.
This is a sticky bit. You must clear this field by writing 1 to it each time a corresponding field, which sets this
field, clears.
Access restriction apply to this field. It automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
Table continues on the next page...

---

*Page 3070*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Not detected
1b - Detected
13 Context Descriptor Error
CDE Indicates whether the context descriptor error status is detected.
This field indicates that the DMA Tx/Rx engine receives a descriptor error, which indicates an invalid context
in the middle of packet flow ( intermediate descriptor) or all one's descriptor in transit case and on receive
side it indicates DMA has read a descriptor with either of the buffer address as ones which is considered to
be invalid.
Access restriction apply to this field. It automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
12 Fatal Bus Error
FBE Indicates whether the fatal bus error status is detected.
This field indicates that a bus error occurred (as described in the EB field). When this field is 1, it indicates
that the corresponding DMA channel engine disables all bus accesses.
Access restriction apply to this field. It automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
11 Early Receive Interrupt
ERI Indicates whether an early receive interrupt status is detected.
When this field is 1, it indicates that the RxDMA completes the packet data transfer to the memory.
In configs supporting ERIC, this field is 1 only after the receive DMA fills a complete receive buffer with
packet data, when DMA_CH0_Rx_Control[ERIC] = 0. This field is 1 after every burst transfer of data from
the receive DMA to the buffer, when DMA_CH0_Rx_Control[ERIC] = 1.
Writing 1 to RI field automatically clears this field.
Access restriction apply to this field. It automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
10 Early Transmit Interrupt
ETI Indicates whether an early transmit interrupt status is detected.
When this field is 1, it indicates that the TxDMA completes the packet data transfer to the MTL
TXFIFO memory.
Table continues on the next page...

---

*Page 3071*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
In configs supporting ERIC: this field is 1 only after the transit DMA transfers a complete packet to MTL,
when DMA_CH0_Tx_Control[ETIC] = 0. This field is 1 after packet data transfers from buffer completes
(partial) in the transmit descriptor in which IOC = 1, when DMA_CH0_Tx_Control[ETIC] = 1.
Access restriction apply to this field. It automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
9 Receive Watchdog Timeout
RWT Indicates whether the receive watchdog timeout status is detected.
Asserts when you receives a packet with length greater than 2,048 bytes (10,240 bytes when Jumbo Packet
mode is enabled).
0b - Not detected
1b - Detected
8 Receive Process Stopped
RPS Indicates whether the receive process stopped status is detected.
Asserts when the receive process enters the stopped state.
Access restriction apply to this field. It automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
7 Receive Buffer Unavailable
RBU Indicates whether the receive buffer unavailable status is detected.
This field indicates that the application owns the next descriptor in the receive list, and DMA cannot acquire
it. The receive process is suspended. To resume processing receive descriptors, the application must
change the ownership of the descriptor and issue a receive poll demand command. If this command is not
issued, the receive process resumes when you receive the next recognized incoming packet. In ring mode,
the application must advance the receive descriptor tail pointer register of a channel. This field is 1 only when
DMA owns the previous receive descriptor.
Access restriction apply to this field. It automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
6 Receive Interrupt
RI Indicates that the receive interrupt status is detected.
Table continues on the next page...

---

*Page 3072*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
This field indicates that the packet reception is complete. When packet reception completes, bit 31
of RDES3 resets in the last descriptor, and you can update the specific packet status information in
the descriptor.
The reception remains in the running state.
Access restriction apply to this field. It automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
5-3 Reserved.
—
2 Transmit Buffer Unavailable
TBU Indicates whether the transmit buffer unavailable status is detected.
This field indicates that the application owns the next descriptor in the transmit list, and the DMA
cannot acquire it. Transmission is suspended. DMA_Debug_Status0[TPS0] explains the transmit process
state transitions.
To resume processing the transmit descriptors, the application must perform these steps:
1. Change the ownership of the descriptor by writing 1 to bit 31 of TDES3.
2. Issue a transmit poll demand command.
For Ring mode, the application must advance the transmit descriptor tail pointer register of a channel.
Access restriction apply to this field. It automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
1 Transmit Process Stopped
TPS Indicates whether the transmit process stopped status is detected.
This field is 1 when the transmission stops.
Access restriction apply to this field. It automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
0 Transmit Interrupt
TI Indicates whether the transmit interrupt status is detected.
This field indicates that the packet transmission is complete. When transmission completes, Bit 31 of TDES3
resets in the last descriptor, and you can update the specific packet status information in the descriptor.
Table continues on the next page...

---

*Page 3073*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
Access restriction apply to this field. It automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected

#### 72.17.262 DMA Channel 0 Miss Frame Counter (DMA_CH0_Miss_Frame_Cnt)

Offset
Register Offset
DMA_CH0_Miss_Frame_ 1164h
Cnt
Function
Indicates the number of packet counter that DMA drops either due to bus error or due to programming RPF field in DMA_CH$
{i}_Rx_Control register.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R MFCO MFC
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-16 Reserved.
—
15 Overflow status of the MFC Counter
MFCO Indicates whether the miss frame counter overflow has occurred.
When this field is 1, it indicates that the MFC counter does not increment further. This field is 0 when this
register is read.
Table continues on the next page...

---

*Page 3074*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
Access restriction apply to this field. It clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not occurred
1b - Occurred
14-11 Reserved.
—
10-0 Dropped Packet Counters
MFC Indicates the number of packet counters that DMA drops either because of bus error or because of
programing RPF field in DMA_CH${i}_Rx_Control register. This field is 0 when this register is read.
Access restriction apply to this field. It clears on read and automatically becomes 1 on an internal
event occurrence.

#### 72.17.263 DMA Channel 0 Rx Parser Accept Count (DMA_CH0_RXP_Accept_Cnt)

Offset
Register Offset
DMA_CH0_RXP_Accept 1168h
_Cnt
Function
Provides the count of the number of frames which the receive parser accept.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
RXPA
R RXPAC
COF
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXPAC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 3075*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31 Rx Parser Accept Counter Overflow Bit
RXPACOF Indicates whether the receive parser accept counter overflow has occurred.
When this field is 1, it indicates that the RXPAC counter field has crossed the maximum limit.
Access restriction apply to this field. It clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not occurred
1b - Occurred
30-0 Rx Parser Accept Counter
RXPAC Implements whenever a receive parser accept a packet because AF = 1. The counter clears when the
register is read.

#### 72.17.264 DMA Channel 0 Rx ERI Count (DMA_CH0_RX_ERI_Cnt)

Offset
Register Offset
DMA_CH0_RX_ERI_Cnt 116Ch
Function
Provides the count of the number of times ERI is asserted.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R ECNT
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-12 Reserved.
Table continues on the next page...

---

*Page 3076*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
—
11-0 ERI Counter
ECNT Indicates that when ERIC bit of DMA_CH(#i)_RX_Control register is 1, this counter increments for burst
transfer which the receive DMA completes from the start of packet transfer. This field becomes 0 at the start
of new packet.

#### 72.17.265 DMA Channel 1 Control (DMA_CH1_Control)

Offset
Register Offset
DMA_CH1_Control 1180h
Function
Specifies the MSS value for segmentation, length to skip between two descriptors, and also the features such as header
splitting and 8xPBL mode.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv Reserv
Reserved Reserved DSL PBLx8
ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-25 Reserved.
—
24 Reserved.
—
23-21 Reserved.
Table continues on the next page...

---

*Page 3077*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
—
20-18 Descriptor Skip Length
DSL Specifies the Word, Dword, or Lword number (depending on the 32-bit, 64-bit, or 128-bit bus) to skip
between two unchained descriptors. The address skipping starts from the end of the current descriptor to
the start of the next descriptor.
When this field is 0, it indicates that DMA takes the descriptor table as contiguous.
17 Reserved.
—
16 8xPBL mode
PBLx8 Indicates whether the 8xPBL mode is enabled.
When this field is 1, it indicates that you must program the PBL value in Bits[21:16] in
DMA_CH(#i)_Tx_Control and Bits[21:16] in DMA_CH(#i)_Rx_Control and multiply it eight times. Therefore,
DMA transfers the data in 8, 16, 32, 64, 128, and 256 beats depending on the PBL value.
0b - Disabled
1b - Enabled
15-14 Reserved.
—
13-0 Reserved.
—

#### 72.17.266 DMA Channel 1 Tx Control (DMA_CH1_Tx_Control)

Offset
Register Offset
DMA_CH1_Tx_Control 1184h
Function
Controls the transmit features such as PBL, TCP segmentation, and transmit channel weights.

---

*Page 3078*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv
Reserved EDSE Reserved ETIC TxPBL
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserv Reserv
Reserved Reserved OSF TCW ST
ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-29 Reserved.
—
28 Enhanced Descriptor Enable
EDSE Indicates whether an enhanced descriptor is enabled.
When this field is 1, it indicates that the corresponding channel uses enhanced descriptors that are 32 Bytes
for both normal and context descriptors.
When this field becomes 0, it indicates that the corresponding channel uses the descriptors that are
16 Bytes.
0b - Disabled
1b - Enabled
27-24 Reserved.
—
23 Reserved.
—
22 Early Transmit Interrupt Control
ETIC Indicates whether an early transmit interrupt is enabled.
When this field is 1, it indicates that an early transmit interrupt (ETI) status sets after the data transfer
completes from buffers of a transmit descriptor in which IOC bit (TDES2[31]) sets.
When this field becomes 0, it indicates that the ETI sets only after a complete packet transfers to the MTL
TX FIFO memory.
0b - Disabled
1b - Enabled
21-16 Transmit Programmable Burst Length
Table continues on the next page...

---

*Page 3079*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
TxPBL Indicates the maximum number of beats to transfer in one DMA block data. DMA always attempts max burst
as specified in PBL each time it starts a burst transfer on the application bus. You can program PBL with
any of these values: 1, 2, 4, 8, 16, or 32. Any other value results in an undefined behavior.
To transfer more than 32 beats, perform the following steps:
1. Write 1 to DMA_CH0_Control[PBLx8] .
2. Write 1 to this field.
NOTE
The maximum value of this field must be less than or equal to half the transit queue size
(TQS field of MTL_TxQ(#i)_Operation_Mode register) in terms of beats. This is required so
that the transit queue has space to store at least another Tx PBL worth of data while the MTL
Tx Queue controller transfers data to MAC. For example, in 64-bit data width configurations
the total locations in transit queue of size 512 bytes is 64. You must program TxPBL and
8xPBL to less than or equal to 32.
15 Reserved.
—
14-13 Reserved.
—
12 Reserved.
—
11-5 Reserved.
—
4 Operate on Second Packet
OSF Indicates whether an operation on second packet is enabled.
When this field is 1, it instructs DMA to process the second packet of the transmit data even before the first
packet status is obtained.
0b - Disabled
1b - Enabled
3-1 Transmit Channel Weight
TCW Indicates the weight assigned to the corresponding transmit channel. When reset completes, this field
becomes 0 for all channels by default, resulting in equal weights to all channels.
0 Start or Stop Transmission Command
ST Indicates whether to start or stop transmission command.
When this field is 1, it indicates that the transmission is placed in the running state. DMA checks the transmit
list at the current position to transmit a packet.
Table continues on the next page...

---

*Page 3080*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
DMA tries to acquire descriptor from either of the following positions:
• The current position in the list. This is the base address of the transmit list which
DMA_CH0_TxDesc_List_Address sets.
• The position at which the transmission was previously stopped
If DMA does not own the current descriptor, the transmission enters the suspended state and
DMA_CH0_Status[TBU] = 1. The start transmission command is effective only when the transmission
stops. If you issue the command before writing 1 to DMA_CH0_TxDesc_List_Address , then DMA behavior
is unpredictable.
When this field becomes 0, it indicates that the transmission process is placed in the stopped state after the
transmission of the current packet completes. The next descriptor position in the transmit list is saved, and it
becomes the current position when the transmission restarts. To change the list address, you must program
DMA_CH0_TxDesc_List_Address with a new value when this field becomes 0. The new value is considered
when this field is 1 again. The stop transmission command is effective only when the transmission of the
current packet completes or the transmission is in the suspended state.
0b - Stop
1b - Start

#### 72.17.267 DMA Channel 1 Rx Control (DMA_CH1_Rx_Control)

Offset
Register Offset
DMA_CH1_Rx_Control 1188h
Function
Controls the receive features such as PBL, buffer size, and extended status.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv
RPF Reserved Reserved ERIC RxPBL
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RBSZ_x_0
Reserv
RBSZ_13_y SR
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 3081*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31 Rx Packet Flush
RPF Indicates whether the receive packet flush is enabled.
When this field is 1, it indicates that the module automatically flushes the packet from the receive queues
destined to this DMA receive channel, when it stops. When this field remains 1 and the software driver
re-starts DMA, the packets residing in the receive queues that were received when this RxDMA was
stopped, flushes out. Route the packets to the RxDMA that the MAC receives after the RxDMA re-starts.
The flushing takes place on the read side of the receive queue.
When this field is 0, it indicates that the module do not flush the packet in the receive queue destined
to this RxDMA channel when it is in stop state. This might in turn cause head-of-line blocking in the
corresponding RxQueue.
NOTE
The stopping of packet flow from a Rx DMA Channel to the application by writing 1 to this
field works only when there is one-to-one mapping of receive queue to the receive DMA
channels. In Dynamic mapping mode, writing 1 to this field in any DMA_CH(#i)_Rx_Control
register might flush packets from unintended receive queues which are destined to the
stopped Rx DMA Channel.
0b - Disabled
1b - Enabled
30-28 Reserved.
—
27-24 Reserved.
—
23 Reserved.
—
22 Early Receive Interrupt Control
ERIC Indicates whether an early receive interrupt control status is enabled.
When this field is 1, it indicates that an early receive interrupt (ERI) status sets after every burst transfer of
data from the receive DMA to the buffer completes.
When this field becomes 0, it indicates that ERI sets only after the RxDMA fills the complete buffer is filled.
0b - Disabled
1b - Enabled
21-16 Receive Programmable Burst Length
RxPBL Indicates the maximum number of beats to transfer in one DMA block data. DMA always attempts max burst
as specified in PBL, each time it starts a burst transfer on the application bus. You can program PBL with
any of the following values: 1, 2, 4, 8, 16, or 32. Any other value results in an undefined behavior.
To transfer more than 32 beats, perform the following steps:
Table continues on the next page...

---

*Page 3082*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
1. Write 1 to DMA_CH0_Control[PBLx8] .
2. Write 1 to RxPBL.
NOTE
The maximum value of RxPBL must be less than or equal to half the receive queue size
(RQS field of MTL_RxQ(#i)_Operation_Mode register) in terms of beats. This is required so
that the receive queue has space to store at least another Rx PBL worth of data when the
receive DMA transfers a block of data. For example, in 64-bit data width configurations the
total locations in receive queue of size 512 bytes is 64, so you must program RxPBL and
8xPBL to less than or equal to 32.
15 Reserved.
—
14-3 Receive Buffer size High
RBSZ_13_y Indicates that RBSZ[13:0] splits into two fields higher RBSZ_13_y and lower RBSZ_x_0. RBSZ[13:0] field
indicates the size of the receive buffers specified in bytes. The maximum buffer size is limited to 16K bytes.
The buffer size is applicable to payload buffers when you enable split headers.
NOTE
The buffer size must be a multiple of 4, 8, or 16 depending on the data bus widths (32-bit,
64-bit, or 128-bit respectively). This is required even if the value of buffer address pointer is
not aligned to data bus width. Hence the lower RBSZ_x_0 bits are read-only and the value is
considered as all-zero. Thus RBSZ_13_y indicates the buffer size in terms of locations (with
the width same as bus-width).
2-1 Receive Buffer size Low
RBSZ_x_0 Indicates that RBSZ[13:0] splits into two fields RBSZ_13_y and RBSZ_x_0. RBSZ_x_0 is the lower field
whose width is based on data bus width of the configuration.
This field width is of 2, 3, or 4 bits for 32-bit, 64-bit, or 128-bit data bus width respectively. This field is
read-only (RO).
0 Start or Stop Receive
SR Indicates whether to start or stop receive.
When this field is 1, it indicates that DMA tries to acquire the descriptor from the receive list and processes
the incoming packets.
DMA tries to acquire descriptor from either of the following positions:
• The current position in the list. This is the address that DMA_CH0_RxDesc_List_Address sets.
• The position at which the receive process was previously stopped
If DMA does not own the current descriptor, the reception suspends and DMA_CH0_Status[RBU] = 1. The
start receive command is effective only when the reception stops. If the command is issued before writing
1 to DMA_CH0_RxDesc_List_Address , DMA behavior is unpredictable.
Table continues on the next page...

---

*Page 3083*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
When this field becomes 0, it indicates that the receive DMA operation stops after the transfer of the current
packet. The next descriptor position in the receive list is saved, and it becomes the current position after
the receive process restarts. The stop receive command is effective only when the receive process is in the
running (waiting for Rx packet) or suspended state.
0b - Stop
1b - Start

#### 72.17.268 DMA Channel 1 Tx Descriptor List Address (DMA_CH1_TxDesc_List_Address)

Offset
Register Offset
DMA_CH1_TxDesc_List_ 1194h
Address
Function
Specifies DMA to the start of transmit descriptor list. The descriptor lists reside in the application's physical memory space and
must be Word, Dword, or Lword-aligned (for 32-bit, 64-bit, or 128-bit data bus). The DMA converts the corresponding LSB bits
to low to internally convert the descriptor lists to bus width aligned address.
You can write to this register only when the transmit DMA stops, that is, DMA_CH0_Tx_Control[ST] = 0. When stopped, you can
write this register with a new descriptor list address. When DMA_CH0_Tx_Control[ST] = 1, the DMA takes the newly-programmed
descriptor base address. If this register do not change when DMA_CH0_Tx_Control[ST] = 0, DMA takes the descriptor address
where it was stopped earlier.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TDESLA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TDESLA Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-2 Start of Transmit List
Table continues on the next page...

---

*Page 3084*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
TDESLA Contains the first descriptor base address in the transmit descriptor list. DMA ignores the LSB bits (1:0, 2:0,
or 3:0) for 32-bit, 64-bit, or 128-bit bus width and internally assumes these bits as all-zero. Therefore, these
LSB bits are read-only (RO).
The field width depends on the configuration:
31:2 for 32-bit configuration
31:3 for 64-bit configuration
31:4 for 128-bit configuration
1-0 Reserved.
—

#### 72.17.269 DMA Channel 1 Rx Descriptor List Address (DMA_CH1_RxDesc_List_Address)

Offset
Register Offset
DMA_CH1_RxDesc_List 119Ch
_Address
Function
Specifies DMA to the start of receive descriptor list. The descriptor lists resides in the application's physical memory space and
must be Word, Dword, or Lword-aligned (for 32-bit, 64-bit, or 128-bit data bus). DMA converts the corresponding LS bits low to
internally convert the descriptor lists to bus width aligned address. You can write to this register only when reception stops. When
stopped, you must write this register before the receive start command is given.
You can write to this register when receive DMA has stopped, that is, DMA_CH0_Rx_Control[SR] = 0. When stopped, you can
this register with a new descriptor list address.
When DMA_CH0_Rx_Control[SR] = 1, DMA takes the newly programmed descriptor base address.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
RDESLA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
RDESLA Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 3085*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-2 Start of Receive List
RDESLA Contains the first descriptor base address in the receive descriptor list. DMA ignores the LSB bits (1:0, 2:0,
or 3:0) for 32-bit, 64-bit, or 128-bit bus width and internally assume these bits as all-zero. Therefore, these
LSB bits are read-only (RO).
The field width depends on the configuration:
31:2 for 32-bit configuration
31:3 for 64-bit configuration
31:4 for 128-bit configuration
1-0 Reserved.
—

#### 72.17.270 DMA Channel 1 Tx Descriptor Tail Pointer (DMA_CH1_TxDesc_Tail_Pointer)

Offset
Register Offset
DMA_CH1_TxDesc_Tail_ 11A0h
Pointer
Function
Specifies to an offset from the base and indicates the location of the last valid descriptor.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TDTP
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TDTP Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-2 Transmit Descriptor Tail Pointer
Table continues on the next page...

---

*Page 3086*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
TDTP Contains the tail pointer for the transit descriptor ring. The software writes the tail pointer to add more
descriptors to the transit channel. The hardware tries to transmit all the packets which the descriptors
referenced between the head and the tail pointer registers.
The field width depends on the configuration:
31:2 for 32-bit configuration
31:3 for 64-bit configuration
31:4 for 128-bit configuration
1-0 Reserved.
—

#### 72.17.271 DMA Channel 1 Rx Descriptor Tail Pointer (DMA_CH1_RxDesc_Tail_Pointer)

Offset
Register Offset
DMA_CH1_RxDesc_Tail 11A8h
_Pointer
Function
Specifies to an offset from the base and indicates the location of the last valid descriptor.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
RDTP
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
RDTP Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-2 Receive Descriptor Tail Pointer
RDTP
Table continues on the next page...

---

*Page 3087*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
Contains the tail pointer for the receive descriptor ring. The software writes the tail pointer to add more
descriptors to the receive channel. The hardware tries to write all received packets to the descriptors
referenced between the head and the tail pointer registers.
The field width depends on the configuration:
31:2 for 32-bit configuration
31:3 for 64-bit configuration
31:4 for 128-bit configuration
1-0 Reserved.
—

#### 72.17.272 DMA Channel 1 Tx Descriptor Ring Length (DMA_CH1_TxDesc_Ring_Length)

Offset
Register Offset
DMA_CH1_TxDesc_Ring 11ACh
_Length
Function
Contains the length of the transmit descriptor ring.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved TDRL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-10 Reserved.
—
Table continues on the next page...

---

*Page 3088*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
9-0 Transmit Descriptor Ring Length
TDRL Sets the maximum number of transmit descriptors in the circular descriptor ring. The maximum number
of descriptors is limited to 1 K descriptors. NXP recommends a minimum ring descriptor length of 4. For
example, you can program any value up to 0x3FF in this field. This field is 10 bits wide, if you program 0x3FF,
you can have 1024 descriptors. Program it to a value of 0x9 if you want to have 10 descriptors.

#### 72.17.273 DMA Channel 1 Rx Descriptor Ring Length (DMA_CH1_RxDesc_Ring_Length)

Offset
Register Offset
DMA_CH1_RxDesc_Rin 11B0h
g_Length
Function
Contains the length of the receive descriptor circular ring.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved RDRL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-10 Reserved.
—
9-0 Receive Descriptor Ring Length
RDRL Sets the maximum number of receive descriptors in the circular descriptor ring. The maximum number of
descriptors are limited to 1 K descriptors. For example, you can program any value up to 0x3FF in this field.
This field is 10 bits wide, if you program 0x3FF, you can have 1024 descriptors. Program it to a value of 0x9
if you want to have 10 descriptors.

---

*Page 3089*

Ethernet Media Access Controller (EMAC)

#### 72.17.274 DMA Channel 1 Interrupt Enable (DMA_CH1_Interrupt_Enable)

Offset
Register Offset
DMA_CH1_Interrupt_Ena 11B4h
ble
Function
Enables the interrupts which the status register reports.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
NIE AIE CDEE FBEE ERIE ETIE RWTE RSE RBUE RIE Reserved TBUE TXSE TIE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-16 Reserved.
—
15 Normal Interrupt Summary Enable
NIE Enables or disables the normal interrupt summary.
When this field is 1, it enables the normal interrupt summary. This field also enables the following interrupts
in DMA_CH0_Status :
Bit 0 - Transmit interrupt
Bit 2 - Transmit buffer unavailable
Bit 6 - Receive interrupt
Bit 11 - Early receive interrupt
When this field becomes 0, it disables the normal interrupt summary.
0b - Disable
1b - Enable
14 Abnormal Interrupt Summary Enable
Table continues on the next page...

---

*Page 3090*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
AIE Enables or disables the abnormal interrupt summary.
When this field is 1, it enables the abnormal interrupt summary. This field also enables the following
interrupts in DMA_CH0_Status :
Bit 1 - Transmit process stopped
Bit 7 - Rx buffer unavailable
Bit 8 - Receive process stopped
Bit 9 - Receive watchdog timeout
Bit 10 - Early transmit interrupt
Bit 12 - Fatal bus error
Bit 13 - Context descriptor error
When this field becomes 0, it disables the abnormal interrupt summary.
0b - Disable
1b - Enable
13 Context Descriptor Error Enable
CDEE Enables or disables the context descriptor error.
When this field is 1 along with DMA_CH0_Interrupt_Enable[AIE] , it enables the descriptor error interrupt.
When this field becomes 0, it disables the descriptor error interrupt.
0b - Disable
1b - Enable
12 Fatal Bus Error Enable
FBEE Enables or disables fatal bus error.
When this field is 1 along with DMA_CH0_Interrupt_Enable[AIE] , it enables the fatal bus error interrupt.
When this field becomes 0, it disables the fatal bus error interrupt.
0b - Disable
1b - Enable
11 Early Receive Interrupt Enable
ERIE Enables or disables the early receive interrupt.
When this field is 1 along with DMA_CH0_Interrupt_Enable[NIE] , it enables the early receive interrupt. When
this field becomes 0, it disables the early receive interrupt.
0b - Disable
1b - Enable
10 Early Transmit Interrupt Enable
ETIE Enables or disables the early transmit interrupt.
Table continues on the next page...

---

*Page 3091*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
When this field is 1 along with DMA_CH0_Interrupt_Enable[AIE] , it enables the early transmit interrupt.
When this field becomes 0, it disables the early transmit interrupt.
0b - Disable
1b - Enable
9 Receive Watchdog Timeout Enable
RWTE Enables or disables the receive watchdog timeout interrupt.
When this field is 1 along with DMA_CH0_Interrupt_Enable[AIE] , it enables the receive watchdog timeout
interrupt. When this field becomes 0, it disables the receive watchdog timeout interrupt.
0b - Disable
1b - Enable
8 Receive Stopped Enable
RSE Enables or disables the receive stopped interrupt.
When this field is 1 along with DMA_CH0_Interrupt_Enable[AIE] , It enables the receive stopped interrupt.
When this field becomes 0, it disables the receive stopped interrupt.
0b - Disable
1b - Enable
7 Receive Buffer Unavailable Enable
RBUE Enables or disables the receive buffer unavailable interrupt.
When this field is 1 along with DMA_CH0_Interrupt_Enable[AIE] , it enables the receive buffer unavailable
interrupt. When this field becomes 0, it disables the receive buffer unavailable interrupt.
0b - Disable
1b - Enable
6 Receive Interrupt Enable
RIE Enables or disables the receive interrupt.
When this field is 1 along with DMA_CH0_Interrupt_Enable[NIE] , it enables the receive interrupt. When this
field becomes 0, it disables the receive interrupt.
0b - Disable
1b - Enable
5-3 Reserved.
—
2 Transmit Buffer Unavailable Enable
TBUE Enables or disables the transmit buffer unavailable interrupt.
Table continues on the next page...

---

*Page 3092*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
When this field is 1 along with DMA_CH0_Interrupt_Enable[NIE] , it enables the transmit buffer unavailable
interrupt. When this field becomes 0, it disables the transmit buffer unavailable interrupt.
0b - Disable
1b - Enable
1 Transmit Stopped Enable
TXSE Enables or disables the transmit stopped interrupt.
When this field is 1 along with DMA_CH0_Interrupt_Enable[AIE] , it enables the transmission stopped
interrupt. When this field becomes 0, it disables the transmission Stopped interrupt.
0b - Disable
1b - Enable
0 Transmit Interrupt Enable
TIE Enables or disables the transmit interrupt.
When this field is 1 along with DMA_CH0_Interrupt_Enable[NIE] , it enables the transmit interrupt. When this
field becomes 0, it disables the transmit interrupt.
0b - Disable
1b - Enable

#### 72.17.275 DMA Channel 1 Rx Interrupt Watchdog Timer (DMA_CH1_Rx_Interrupt_Watchdog_Timer)

Offset
Register Offset
DMA_CH1_Rx_Interrupt_ 11B8h
Watchdog_Timer
Function
Indicates the watchdog timeout for receive interrupt (RI) from DMA. When you write this register with a non-zero value, it
enables the watchdog timer for the RI bit of the DMA_CHi_Status register.

---

*Page 3093*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved RWTU
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved RWT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-18 Reserved.
—
17-16 Receive Interrupt Watchdog Timer Count Units
RWTU Indicates the number of system clock cycles corresponding to one unit
in DMA_CH1_Rx_Interrupt_Watchdog_Timer[RWT] .
2'b00 - 256
2'b01 - 512
2'b10 - 1024
2'b11 - 2048
For example, the watchdog timer sets for 2*512=1024 system
clock cycles, when DMA_CH1_Rx_Interrupt_Watchdog_Timer[RWT] = 2 and
DMA_CH1_Rx_Interrupt_Watchdog_Timer[RWTU] = 1.
15-8 Reserved.
—
7-0 Receive Interrupt Watchdog Timer Count
RWT Indicates the number of system clock cycles, multiplied by factor indicated in
DMA_CH1_Rx_Interrupt_Watchdog_Timer[RWTU] , for which the watchdog timer sets.
The watchdog timer triggers with the programmed value after the receive DMA completes the packet
transfer for which the RI bit is not 1 in DMA_CH(#i)_Status register, because of the setting of interrupt enable
bit in the corresponding descriptor RDES3[30].
RI bit is 1 and the timer stops when the watchdog timer runs out. The watchdog timer becomes 0 when RI
bit is 1 because RI sets automatically per the interrupt enable bit RDES3[30] of any received packet.

---

*Page 3094*

Ethernet Media Access Controller (EMAC)

#### 72.17.276 DMA Channel 1 Slot Function Control Status (DMA_CH1_Slot_Function_Control_Status)

Offset
Register Offset
DMA_CH1_Slot_Functio 11BCh
n_Control_Status
Function
Contains the control field for slot function and the status for transmit path.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RSN
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SIV Reserved ASC ESC
W
Reset 0 0 0 0 0 1 1 1 1 1 0 0 0 0 0 0
Fields
Field Function
31-20 Reserved.
—
19-16 Reference Slot Number
RSN Provides the current value of the reference slot number in DMA. It is used for slot comparison.
15-4 Slot Interval Value
SIV Controls the period of the slot interval in which the TxDMA fetches the scheduled packets. A value of 0
specifies the slot interval of 1 us while the maximum value 4095 specifies the slot interval of 4096 us. The
default or reset value is 0x07C which corresponds to slot interval of 125 us.
3-2 Reserved.
—
1 Advance Slot Check
ASC Indicates whether an advance slot check is enabled.
When this field is 1, it enables DMA to fetch the data from the buffer when the slot number (SLOTNUM)
programmed in the transmit descriptor is:
Table continues on the next page...

---

*Page 3095*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• Equal to the reference slot number given in DMA_CH1_Slot_Function_Control_Status[RSN] , or
• Ahead of the reference slot number by up to two slots.
This field is applicable only when DMA_CH1_Slot_Function_Control_Status[ESC] = 1.
0b - Disabled
1b - Enabled
0 Enable Slot Comparison
ESC Enables slot comparison.
When this field is 1, it enables the checking of the slot numbers programmed in the transmit descriptor with
the current reference given in DMA_CH1_Slot_Function_Control_Status[RSN] . DMA fetches the data from
the corresponding buffer only when the slot number is:
• Equal to the reference slot number, or
• Ahead of the reference slot number by one slot.
When this field becomes 0, it disables the checking of the slot numbers. DMA fetches the data immediately
after you process the descriptor.
NOTE
You must not enable the UFO (UDP Fragmentation over IPv4)/TSO/USO along with
TBS/AVB slot number check. The UFO/TSO/USO involves multiple packets or segments
or fragments transmission for single packet received from application and the slot number
check are applicable for fetching only first segment/fragment. As a result it might be difficult
for you to specify slot number for subsequent packets.
0b - Disable
1b - Enable

#### 72.17.277 DMA Channel 1 Current Application Transmit Descriptor (DMA_CH1_Current_App_TxDesc)

Offset
Register Offset
DMA_CH1_Current_App 11C4h
_TxDesc
Function
Specifies the current transmit descriptor which DMA reads.

---

*Page 3096*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R CURTDESAPTR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R CURTDESAPTR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Application Transmit Descriptor Address Pointer
CURTDESAPT Indicates that DMA updates this pointer during the transmit operation. This pointer is 0 on reset.
R

#### 72.17.278 DMA Channel 1 Current Application Receive Descriptor (DMA_CH1_Current_App_RxDesc)

Offset
Register Offset
DMA_CH1_Current_App 11CCh
_RxDesc
Function
Specifies the current receive descriptor which DMA reads.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R CURRDESAPTR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R CURRDESAPTR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 3097*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Application Receive Descriptor Address Pointer
CURRDESAPT Indicates that DMA updates this pointer during the receive operation. This pointer is 0 on reset.
R

#### 72.17.279 DMA Channel 1 Current Application Transmit Buffer (DMA_CH1_Current_App_TxBuffer)

Offset
Register Offset
DMA_CH1_Current_App 11D4h
_TxBuffer
Function
Specifies the current transmit buffer address which DMA reads.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R CURTBUFAPTR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R CURTBUFAPTR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Application Transmit Buffer Address Pointer
CURTBUFAPT Indicates that DMA updates this pointer during the transmit operation. This pointer is 0 on reset.
R

---

*Page 3098*

Ethernet Media Access Controller (EMAC)

#### 72.17.280 DMA Channel 1 Current Application Receive Buffer (DMA_CH1_Current_App_RxBuffer)

Offset
Register Offset
DMA_CH1_Current_App 11DCh
_RxBuffer
Function
Indicates that DMA_CH0_Current_App_RxBuffer points to the current receive buffer address which the DMA reads.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R CURRBUFAPTR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R CURRBUFAPTR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Application Receive Buffer Address Pointer
CURRBUFAPT Indicates that DMA update this pointer during the receive operation. This pointer is 0 on reset.
R

#### 72.17.281 DMA Channel 1 Status (DMA_CH1_Status)

Offset
Register Offset
DMA_CH1_Status 11E0h
Function
Indicates that to determine the status of DMA, the application reads the status register during an interrupt service routine or
polling.
NOTE
The number of DMA_CH(#i)_Status register in the configuration is the higher of number of receive DMA Channels
and transmit DMA Channels.

---

*Page 3099*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R REB TEB
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
NIS AIS CDE FBE ERI ETI RWT RPS RBU RI Reserved TBU TPS TI
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-22 Reserved.
—
21-19 Rx DMA Error Bits
REB Indicates the type of error that causes a bus error. For example, an error response on the AHB or
AXI interface.
Bit 21
1'b1 - Error during data transfer by Rx DMA
1'b0 - No Error during data transfer by Rx DMA
Bit 20
1'b1 - Error during descriptor access
1'b0 - Error during data buffer access
Bit 19
1'b1 - Error during read transfer
1'b0 - Error during write transfer
This field is valid only when DMA_CH1_Status[FBE] = 1. It does not generate an interrupt.
18-16 Tx DMA Error Bits
TEB Indicates the type of error that causes a bus error. For example, error response on the AHB or AXI interface.
Bit 18
1'b1 - Error during data transfer by Tx DMA
1'b0 - No Error during data transfer by Tx DMA
Bit 17
1'b1 - Error during descriptor access
Table continues on the next page...

---

*Page 3100*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
1'b0 - Error during data buffer access
Bit 16
1'b1 - Error during read transfer
1'b0 - Error during write transfer
This field is valid only when DMA_CH1_Status[FBE] = 1. It does not generate an interrupt.
15 Normal Interrupt Summary
NIS Indicates whether the normal interrupt summary status is detected.
The field value is the logical OR of the following bits when the corresponding interrupt bits enable
in DMA_CH0_Interrupt_Enable :
Bit 0 - Transmit interrupt
Bit 2 - Transmit buffer unavailable
Bit 6 - Receive interrupt
Bit 11 - Early receive interrupt
Only unmasked bits (interrupts for which interrupt enable sets in DMA_CH0_Interrupt_Enable ) affect
this field.
This is a sticky field. You must clear this field (by writing 1 to this field) each time a corresponding bit which
sets it, clears.
Access restriction apply to this field. Automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
14 Abnormal Interrupt Summary
AIS Indicates whether an abnormal interrupt summary status is detected.
The field value is the logical OR of the following bits when the corresponding interrupt bits enable
in DMA_CH0_Interrupt_Enable :
Bit 1 - Transmit process stopped
Bit 7 - Receive buffer unavailable
Bit 8 - Receive process stopped
Bit 10 - Early transmit interrupt
Bit 12 - Fatal bus error
Bit 13 - Context descriptor error
Only unmasked bits affect this field.
This is a sticky bit. You must clear this field (by writing 1 to this bit) each time a corresponding bit, which sets
it, clears.
Table continues on the next page...

---

*Page 3101*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
Access restriction apply to this field. Automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
13 Context Descriptor Error
CDE Indicates whether the context descriptor error status is detected.
This field indicates that the DMA Tx/Rx engine receive a descriptor error, which indicates an invalid context
in the middle of packet flow ( intermediate descriptor) or all one's descriptor in transmit case and on receive
side. It indicates that DMA has read a descriptor with either of the buffer address as ones which you can
consider invalid.
Access restriction apply to this field. Automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
12 Fatal Bus Error
FBE Indicates whether the fatal bus error status is detected.
This field indicates that a bus error has occurred (as described in the EB field). When this field is 1, it
indicates that the corresponding DMA channel engine disables all bus accesses.
Access restriction apply to this field. Automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
11 Early Receive Interrupt
ERI Indicates whether an early receive interrupt status is detected.
When this field is 1, it indicates that the RxDMA has completed the packet data transfer to the memory.
In configs supporting ERIC, When DMA_CH1_Rx_Control[ERIC] = 0, this field is 1 only after the Rx DMA
fills a complete receive buffer with packet data. When DMA_CH1_Rx_Control[ERIC] = 1, this field is 1 after
every burst data transfer from the receive DMA to the buffer.
If DMA_CH1_Status[RI] = 1, this field clears automatically.
Access restriction apply to this field. Automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
10 Early Transmit Interrupt
ETI Indicates whether an early transmit interrupt status is detected.
Table continues on the next page...

---

*Page 3102*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
When this field is 1, it indicates that the TxDMA has completed the packet data transfer to the MTL
TXFIFO memory.
In configs supporting ERIC: When DMA_CH1_Tx_Control[ETIC] = 0, this field is 1 only after the Tx DMA
transfers a complete packet to MTL. When DMA_CH1_Tx_Control[ETIC] = 1, this field is 1 after the packet
data transfer completes (partial) from buffers in the Transmit descriptor in which IOC=1.
Access restriction apply to this field. Automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
9 Receive Watchdog Timeout
RWT Indicates whether a receive watchdog timeout status is detected.
This field asserts when it receives a packet with length greater than 2,048 bytes (10,240 bytes when Jumbo
Packet mode is enabled).
0b - Not detected
1b - Detected
8 Receive Process Stopped
RPS Indicates whether a receive process stopped status is detected.
This field asserts when the receive process enters the stopped state.
Access restriction apply to this field. Automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
7 Receive Buffer Unavailable
RBU Indicates whether a receive buffer unavailable status is detected.
This field indicates that the application owns the next descriptor in the receive list, and the DMA cannot
acquire it. The receive process is suspended. To resume processing receive descriptors, the application
must change the ownership of the descriptor and issue a receive poll demand command. If this command is
not issued, the receive process resumes when you receives the next recognized incoming packet. In Ring
mode, the application must advance the receive descriptor tail pointer register of a channel. This field is 1
only when the DMA owns the previous receive descriptor.
Access restriction apply to this field. Automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
6 Receive Interrupt
RI Indicates whether the receive interrupt status is detected.
Table continues on the next page...

---

*Page 3103*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
This field indicates that the packet reception is complete. Bit 31 of RDES3 resets in the last descriptor, and
updates the specific packet status information in the descriptor, when the packet reception is complete.
The reception remains in the running state.
Access restriction apply to this field. Automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
5-3 Reserved.
—
2 Transmit Buffer Unavailable
TBU Indicates whether the transmit buffer unavailable status is detected.
This field indicates that the application owns the next descriptor in the transmit list, and the DMA
cannot acquire it. Transmission is suspended. DMA_Debug_Status0[TPS0] explains the transmit process
state transitions.
The application must perform these actions, to resume the processing of transmit descriptors:
1. Write 1 to bit 31 of TDES3 to change the ownership of the descriptor.
2. Issue a transmit poll demand command.
For Ring mode, the application must advance the transmit descriptor tail pointer register of a channel.
Access restriction apply to this field. Automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
1 Transmit Process Stopped
TPS Indicates that the transmit process stopped status is detected.
This field is 1 when the transmission stops.
Access restriction apply to this field. Automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
0 Transmit Interrupt
TI Indicates whether the transmit interrupt status is detected.
This field indicates that the packet transmission is complete. When transmission completes, bit 31 of TDES3
resets in the last descriptor, and the specific packet status information is updated in the descriptor.
Access restriction apply to this field. Automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
Table continues on the next page...

---

*Page 3104*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Not detected
1b - Detected

#### 72.17.282 DMA Channel 1 Miss Frame Counter (DMA_CH1_Miss_Frame_Cnt)

Offset
Register Offset
DMA_CH1_Miss_Frame_ 11E4h
Cnt
Function
Provides the number of packet counter that the DMA drops either because of bus error or programming RPF field in
DMA_CH${i}_Rx_Control register.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R MFCO MFC
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-16 Reserved.
—
15 Overflow status of the MFC Counter
MFCO Indicates whether the miss frame counter overflow has occurred.
When this field is 1, it indicates that the MFC counter does not increments further.
This field becomes 0 when this register is read.
Access restriction apply to this field. It clears on read and automatically becomes 1 on an internal
event occurrence.
Table continues on the next page...

---

*Page 3105*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Not occurred
1b - Occurred
14-11 Reserved.
—
10-0 Dropped Packet Counters
MFC Indicates the number of packet counters that DMA drops either because of bus error or programing RPF field
in DMA_CH${i}_Rx_Control register. This counter is 0 when this register is read.
Access restriction apply to this field. It clears on read and automatically becomes 1 on an internal
event occurrence.

#### 72.17.283 DMA Channel 1 Rx Parser Accept Count (DMA_CH1_RXP_Accept_Cnt)

Offset
Register Offset
DMA_CH1_RXP_Accept 11E8h
_Cnt
Function
Provides the count of the number of frames which the receive parser accepts.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
RXPA
R RXPAC
COF
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXPAC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Rx Parser Accept Counter Overflow Bit
Table continues on the next page...

---

*Page 3106*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
RXPACOF Indicates whether the receive parser accept counter overflow have occurred.
When this field is 1, it indicates that the DMA_CH1_RXP_Accept_Cnt[RXPAC] has crossed the
maximum limit.
Access restriction apply to this field. It clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not occurred
1b - Occurred
30-0 Rx Parser Accept Counter
RXPAC Implements whenever a receive parser accept a packet because AF = 1. The counter clears when the
register is read.

#### 72.17.284 DMA Channel 1 Rx ERI Count (DMA_CH1_RX_ERI_Cnt)

Offset
Register Offset
DMA_CH1_RX_ERI_Cnt 11ECh
Function
Provides the count of the number of times ERI asserts.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R ECNT
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-12 Reserved.
Table continues on the next page...

---

*Page 3107*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
—
11-0 ERI Counter
ECNT ERI Counter
When ERIC bit of DMA_CH(#i)_RX_Control register is 1, it indicates that this counter increments for burst
transfer which the Rx DMA completes from the start of packet transfer. This counter resets at the start of
new packet.

#### 72.18 Glossary

AFM Address filtering module
ARI Application receive interface
ATI Application transmit interface
AVB Audio video bridging
AXI Advanced extensible interface
BTR Base time register
CPT Current presentation time
CRC Cyclic redundancy check
DA Destination address
DCB Data center bridging
DUT Device under test
EOP End of packet
FCS A 32-bit cyclic redundancy check used to detect an in-transit corruption of data. Also marks the end of an Ethernet
frame.
FIFO First in first out
GCL Gate control list
GMII Gigabit media independent interface
MAC Media access controller
MCI MAC control interface
MDC Management data clock
MDIO Management data input/output
MII Media independent interface
MRI MAC receive interface
MTI MAC transmit interface
MTL MAC transmit layer
PBL Programmable burst length

---

*Page 3108*

Ethernet Media Access Controller (EMAC)
PHY The physical interface transceiver that implements the OSI physical layer for an ethernet network. The IEEE 802.3
standard defines it.
RGMII Reduced gigabit media independent interface
RMII Reduced media independent interface
RMON Remote network monitoring
SA Source address
SFD Start frame delimiter
SGMII Serial gigabit media independent interface
SMA Station management agent
TCP Transmission control protocol
UDP User datagram protocol
VLAN Virtual local area network

---

*Page 3109*

