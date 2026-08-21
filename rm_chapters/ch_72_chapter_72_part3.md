<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 72 -->

s the number of packets that the module received with a watchdog timeout error. Packets with a data load larger than the
following are received:
• 2,048 bytes when MAC_Configuration[JE] = 0 and MAC_Configuration[WD] = 0
• 10,240 bytes when MAC_Configuration[JE] = 1 and MAC_Configuration[WD] = 0
• 16,384 bytes when MAC_Configuration[WD] = 1 or the value programmed in MAC Watchdog Timeout
(MAC_Watchdog_Timeout)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXWDGERR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXWDGERR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Receive Watchdog Error Packets
RXWDGERR Indicates the number of packets received with a watchdog timeout error. Packets with a data load larger than
the following are received:
Table continues on the next page...

---

*Page 2843*

Ethernet Media Access Controller (EMAC)
Field Function
• 2,048 bytes when MAC_Configuration[JE] = 0 and MAC_Configuration[WD] = 0
• 10,240 bytes when MAC_Configuration[JE] = 1 and MAC_Configuration[WD] = 0
• 16,384 bytes when MAC_Configuration[WD] = 1 or the value programmed in MAC Watchdog
Timeout (MAC_Watchdog_Timeout)

#### 72.17.112 Receive Error Packets (Rx_Receive_Error_Packets)

Offset
Register Offset
Rx_Receive_Error_Pack 7E0h
ets
Function
Provides the number of packets that the module received with a receive or packet extension error on GMII or MII.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXRCVERR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXRCVERR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Receive Error Packets
RXRCVERR Indicates the number of packets received with a receive or packet extension error on GMII or MII.

---

*Page 2844*

Ethernet Media Access Controller (EMAC)

#### 72.17.113 Receive Control Packets Good (Rx_Control_Packets_Good)

Offset
Register Offset
Rx_Control_Packets_Go 7E4h
od
Function
Provides the number of good control packets that the module received.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXCTRLG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXCTRLG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Receive Control Packets Good
RXCTRLG Indicates the number of good control packets received.

#### 72.17.114 MMC Transmit FPE Fragment Counter Interrupt Status (MMC_FPE_Tx_Interrupt)

Offset
Register Offset
MMC_FPE_Tx_Interrupt 8A0h
Function
Maintains the interrupts generated from all the FPE-related transmit statistic counters when they reach half of their maximum
values (8000_0000h for a 32-bit counter and 8000h for a 16-bit counter), and when they cross their maximum values
(FFFF_FFFFh for a 32-bit counter and FFFFh for a 16-bit counter). When MMC_Control[CNTSTOPRO] = 1, the interrupts are set
but the counter remains at all-ones.
An interrupt bit of this register becomes 0 when the respective MMC counter that caused the interrupt is read. The least significant
byte lane (bits [7:0]) of the respective counter must be read to clear the interrupt bit.

---

*Page 2845*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R HRCIS FCIS
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-2 Reserved
—
1 MMC Transmit Hold Request Counter Interrupt Status
HRCIS Indicates whether the MMC Transmit hold request counter interrupt status is detected.
This field:
• Becomes 1 when the Tx_Hold_Req_Cntr counter reaches half of the maximum value or the
maximum value.
• Exists when any one of the receive or transmit MMC counters is enabled during FPE with AV_EST-
enabled configuration.
Access restrictions apply to this field that clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
0 MMC Transmit FPE Fragment Counter Interrupt Status
FCIS Indicates whether the MMC Transmit FPE fragment counter interrupt status is detected.
This field:
• Becomes 1 when the Tx_FPE_Fragment_Cntr counter reaches half of the maximum value or the
maximum value.
• Exists when any one of the receive or transmit MMC counters is enabled during FPE-enabled
configuration.
Access restrictions apply to this field that clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected

---

*Page 2846*

Ethernet Media Access Controller (EMAC)

#### 72.17.115 MMC FPE Transmit Interrupt Mask (MMC_FPE_Tx_Interrupt_Mask)

Offset
Register Offset
MMC_FPE_Tx_Interrupt_ 8A4h
Mask
Function
Maintains the masks for interrupts generated from all FPE-related transmit statistics counters. MMC Receive Interrupt Mask
(MMC_Rx_Interrupt_Mask) maintains the masks for the interrupts generated when FPE-related receive statistic counters reach
half of their maximum value or the maximum value.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved HRCIM FCIM
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-2 Reserved
—
1 MMC Transmit Hold Request Counter Interrupt Mask
HRCIM Indicates the status of the MMC transmit hold request counter interrupt mask.
Writing 1 to this field masks the interrupt when the Tx_Hold_Req_Cntr counter reaches half of its maximum
value or the maximum value.
The field exists when any one of the receive, transmit, or MMC counters is enabled during FPE with
AV_EST-enabled configuration.
0b - Disabled
1b - Enabled
0 MMC Transmit Fragment Counter Interrupt Mask
FCIM Indicates the status of the MMC transmit fragment counter interrupt mask.
Table continues on the next page...

---

*Page 2847*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
Writing 1 to this field masks the interrupt when the Tx_FPE_Fragment_Cntr counter reaches half of its
maximum value or the maximum value. The field exists when any one of the receive, transmit, or MMC
counters is enabled during FPE-enabled configuration.
0b - Disabled
1b - Enabled

#### 72.17.116 Transmit FPE Fragment Counter (MMC_Tx_FPE_Fragment_Cntr)

Offset
Register Offset
MMC_Tx_FPE_Fragment 8A8h
_Cntr
Function
Provides the number of additional mPackets transmitted because of preemption.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXFFC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXFFC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit FPE Fragment Counter
TXFFC Indicates the number of additional mPackets transmitted because of preemption.
Exists when any one of the receive, transmit, or MMC counters is enabled during FPE-
enabled configuration.

---

*Page 2848*

Ethernet Media Access Controller (EMAC)

#### 72.17.117 Transmit Hold Request Counter (MMC_Tx_Hold_Req_Cntr)

Offset
Register Offset
MMC_Tx_Hold_Req_Cnt 8ACh
r
Function
Provides the count of times that a hold request is given to MAC.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXHRC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXHRC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit Hold Request Counter
TXHRC Indicates the count of times that a hold request is given to MAC.
Exists when any one of the receive, transmit, or MMC counters is enabled during FPE with AV_EST-
enabled configuration.

#### 72.17.118 MMC Receive Packet Assembly Error Counter Interrupt Status (MMC_FPE_Rx_Interrupt)

Offset
Register Offset
MMC_FPE_Rx_Interrupt 8C0h
Function
Maintains the interrupts generated from all the FPE-related receive statistic counters, when these transmit statistic counters
reach half of their maximum values (8000_0000h for a 32-bit counter and 8000h for a 16-bit counter), and when they cross
their maximum values (FFFF_FFFFh for a 32-bit counter and FFFFh for 16-bit counter). When MMC_Control[CNTSTOPRO]
= 1, the interrupts are set but the counter remains at all-ones. An interrupt bit of this register becomes 0 when the respective

---

*Page 2849*

Ethernet Media Access Controller (EMAC)
MMC counter that caused the interrupt is read. The least significant byte lane (bits [7:0]) of the respective counter must be
read to clear the interrupt bit.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
PAOCI PSECI PAECI
R FCIS
S S S
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-4 Reserved
—
3 MMC Receive FPE Fragment Counter Interrupt Status
FCIS Indicates whether the MMC receive FPE fragment counter interrupt status is detected.
This field becomes 1 when the Rx_FPE_Fragment_Cntr counter reaches half of its maximum value or the
maximum value.
Exists when any one of the receive, transmit, or MMC counters is enabled during FPE-
enabled configuration.
Access restrictions apply to this field that clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
2 MMC Receive Packet Assembly OK Counter Interrupt Status
PAOCIS Indicates whether the MMC receive packet assembly OK counter interrupt status is detected.
This field becomes 1 when the Rx_Packet_Assemble_Ok_Cntr counter reaches half of its maximum value
or the maximum value.
Exists when any one of the receive, transmit, or MMC counters is enabled during FPE-
enabled configuration.
Access restrictions apply to this field that clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
Table continues on the next page...

---

*Page 2850*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
1 MMC Receive, Transmit, Packet SMD Error Counter Interrupt Status
PSECIS Indicates whether the status of the MMC receive packet SMD error counter interrupt is detected.
This field becomes 1 when the Rx_Packet_SMD_Err_Cntr counter reaches half of its maximum value or the
maximum value.
Exists when any one of the receive, transmit, or MMC counters is enabled during FPE-
enabled configuration.
Access restrictions apply to this field that clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
0 MMC Receive, transmit, Packet Assembly Error Counter Interrupt Status
PAECIS Indicates whether the status of the MMC receive packet assembly error counter interrupt is detected.
This field becomes 1 when the Rx_Packet_Assemble_Err_Cntr counter reaches half of its maximum value
or the maximum value.
Exists when any one of the receive, transmit, or MMC counters is enabled during the FPE-
enabled configuration.
Access restrictions apply to this field that clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected

#### 72.17.119 MMC FPE Receive Interrupt Mask (MMC_FPE_Rx_Interrupt_Mask)

Offset
Register Offset
MMC_FPE_Rx_Interrupt 8C4h
_Mask
Function
Maintains the masks for interrupts generated from all FPE-related receive statistic counters.
This register maintains the masks for the interrupts generated when FPE-related receive statistic counters reach half of their
maximum value or the maximum value.

---

*Page 2851*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PAOCI PSECI PAECI
Reserved FCIM
M M M
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-4 Reserved
—
3 MMC Receive FPE Fragment Counter Interrupt Mask
FCIM Indicates the status of the MMC receive FPE fragment counter interrupt mask.
Writing 1 to this field masks the interrupt when the Tx_FPE_Fragment_Cntr counter reaches half of its
maximum value or the maximum value.
Exists when any one of the receive, transmit, or MMC counters is enabled during FPE-
enabled configuration.
0b - Disabled
1b - Enabled
2 MMC Receive Packet Assembly OK Counter Interrupt Mask
PAOCIM Indicates the status of the MMC receive packet assembly OK counter interrupt mask.
Writing 1 to this field masks the interrupt when the Rx_Packet_Assemble_Ok_Cntr counter reaches half of
its maximum value or the maximum value.
Exists when any one of the receive, transmit, or MMC counters is enabled during FPE-
enabled configuration.
0b - Disabled
1b - Enabled
1 MMC Receive Packet SMD Error Counter Interrupt Mask
PSECIM Indicates the status of the MMC receive packet SMD error counter interrupt mask.
Writing 1 to this field masks the interrupt when the Rx_Packet_SMD_Err_Cntr counter reaches half of its
maximum value or the maximum value.
Exists when any one of the receive, transmit, or MMC counters is enabled during FPE-
enabled configuration.
Table continues on the next page...

---

*Page 2852*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled
0 MMC receive Packet Assembly Error Counter Interrupt Mask
PAECIM Indicates the status of the MMC receive packet assembly error counter interrupt mask.
Writing 1 to this field masks the interrupt when the Rx_Packet_Assemble_Err_Cntr counter reaches half of
its maximum value or the maximum value.
Exists when any one of the receive, transmit, or MMC counters is enabled during FPE-
enabled configuration.
0b - Disabled
1b - Enabled

#### 72.17.120 MMC Receive Packet Assembly Error Counter (MMC_Rx_Packet_Assembly_Err_Cntr)

Offset
Register Offset
MMC_Rx_Packet_Assem 8C8h
bly_Err_Cntr
Function
Provides the number of MAC frames having reassembly errors on the receiver arising out of mismatch in the fragment count
value.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R PAEC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R PAEC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2853*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Packet Assembly Error Counter
PAEC Indicates the number of MAC frames having reassembly errors on the receiver arising out of mismatch in
the fragment count value.
Exists when any one of the receive, transmit, or MMC counters is enabled during FPE-
enabled configuration.

#### 72.17.121 MMC Receive Packet SMD Error Counter (MMC_Rx_Packet_SMD_Err_Cntr)

Offset
Register Offset
MMC_Rx_Packet_SMD_ 8CCh
Err_Cntr
Function
Provides the number of received MAC frames rejected because of an unknown SMD value and MAC frame fragments rejected
because of arriving with SMD-C when there was no preceding preempted frame.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R PSEC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R PSEC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Packet SMD Error Counter
PSEC Indicates the number of MAC frames rejected because of an unknown SMD value and MAC frame
fragments rejected because of arriving with SMD-C when there was no preceding preempted frame.
Exists when at least one of the receive, transmit, or MMC counters is enabled during FPE-
enabled configuration.

---

*Page 2854*

Ethernet Media Access Controller (EMAC)

#### 72.17.122 MMC Receive Packet Assembly OK Counter (MMC_Rx_Packet_Assembly_OK_Cntr)

Offset
Register Offset
MMC_Rx_Packet_Assem 8D0h
bly_OK_Cntr
Function
Provides the number of MAC frames that were successfully reassembled and delivered to MAC.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R PAOC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R PAOC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Packet Assembly OK Counter
PAOC Indicates the number of MAC frames that were successfully reassembled and delivered to MAC.
Exists when at least one of the receive, transmit, or MMC counters is enabled during FPE-
enabled configuration.

#### 72.17.123 MMC Receive FPE Fragment Counter (MMC_Rx_FPE_Fragment_Cntr)

Offset
Register Offset
MMC_Rx_FPE_Fragmen 8D4h
t_Cntr
Function
Provides the number of additional mPackets received because of preemption.

---

*Page 2855*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R FFC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R FFC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 FPE Fragment Counter
FFC Indicates the number of additional mPackets received because of preemption.
Exists when at least one of the receive, transmit, or MMC counters is enabled during FPE-
enabled configuration.

#### 72.17.124 MAC Layer 3 Layer 4 Control 0 (MAC_L3_L4_Control0)

Offset
Register Offset
MAC_L3_L4_Control0 900h
Function
Controls the filter 0 operations of the layer 3 and layer 4 protocols.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
DMCH DMCH L4DPI L4DP L4SPI L4SP Reserv L4PEN
Reserved Reserved Reserved
EN0 N0 M0 M0 M0 M0 ed 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L3DAI L3DA L3SAI L3SA Reserv L3PEN
L3HDBM0 L3HSBM0
M0 M0 M0 M0 ed 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2856*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-29 Reserved
—
28 DMA Channel Select Enable
DMCHEN0 Indicates the status of the DMA channel number.
• If this field is 1, it enables the selection of the DMA channel number for the packet that the L3_L4
filter passes. The DMCHN fields indicate the DMA channel number.
• If this field is 0, the filter does not decide the DMA channel number.
0b - Disabled
1b - Enabled
27-25 Reserved
—
24 DMA Channel Number
DMCHN0 Indicates the DMA channel number.
If the value of the DMCHEN fields is 1, this field selects the DMA channel number to which the packet that
this filter passes is routed. The width of this field depends on the number of the DMA channels present in
your configuration.
23-22 Reserved
—
21 Layer 4 Destination Port Inverse Match Enable
L4DPIM0 Indicates the status of layer 4 destination port inverse matching.
• If this field is 1, MAC_Layer4_Address0[L4DP0] is enabled for inverse matching.
• If this field is 0, MAC_Layer4_Address0[L4DP0] is enabled for perfect matching.
This field is valid and applicable only when the L4DPM0 field is 1.
0b - Disabled
1b - Enabled
20 Layer 4 Destination Port Match Enable
L4DPM0 Indicates the status of layer 4 destination port matching.
• If this field is 1, MAC_Layer4_Address0[L4DP0] is enabled for matching.
• If this field is 0, MAC ignores MAC_Layer4_Address0[L4DP0] for matching.
0b - Disabled
1b - Enabled
19 Layer 4 Source Port Inverse Match Enable
Table continues on the next page...

---

*Page 2857*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
L4SPIM0 Indicates the status of layer 4 source port inverse matching.
• If this field is 1, MAC_Layer4_Address0[L4SP0] is enabled for inverse matching.
• If this field is 0, MAC_Layer4_Address0[L4SP0] is enabled for perfect matching.
This field is valid and applicable only when MAC_Layer4_Address0[L4SP0] = 1.
0b - Disabled
1b - Enabled
18 Layer 4 Source Port Match Enable
L4SPM0 Indicates the status of layer 4 source port matching.
• If this field is 1, MAC_Layer4_Address0[L4SP0] is enabled for matching.
• If this field is 0, MAC ignores MAC_Layer4_Address0[L4SP0] for matching.
0b - Disabled
1b - Enabled
17 Reserved
—
16 Layer 4 Protocol Enable
L4PEN0 Indicates the status of layer 4 protocol.
• If this field is 1, MAC_Layer4_Address0[L4SP0] and MAC_Layer4_Address0[L4DP0] are used for
matching UDP packets.
• If this field is 0, MAC_Layer4_Address0[L4SP0] and MAC_Layer4_Address0[L4DP0] are used for
matching TCP packets.
Layer 4 matching is performed only when the L4SPM0 field or the L4DPM0 field is 1.
0b - Disabled
1b - Enabled
15-11 Layer 3 IP DA Higher Bits Match
L3HDBM0 IPv4 packets:
This field contains the number of higher bits of IP DA that are matched in the IPv4 packets. The following
list describes the values of this field:
• 0: No bits are masked.
• 1: LSb[0] is masked.
• 2: Two LSbs [1:0] are masked.
• - ..
• 31: All bits except MSb are masked.
IPv6 packets:
Table continues on the next page...

---

*Page 2858*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
Bits [12:11] of this field correspond to bits [6:5] of the L3HSBM0 field, which indicates the number of lower
bits of IP SA or DA that are masked in the IPv6 packets. The following list describes the concatenated values
of the L3HDBM0[1:0] and L3HSBM0 fields:
• 0: No bits are masked.
• 1: LSb[0] is masked.
• 2: Two LSbs [1:0] are masked.
• ..
• 127: All bits except MSb are masked.
This field is valid and applicable only when the L3DAM0 field or the L3SAM0 field is 1.
10-6 Layer 3 IP SA Higher Bits Match
L3HSBM0 IPv4 packets:
This field contains the number of lower bits of IP SA that are masked for matching in the IPv4 packets. The
following list describes the values of this field:
• 0: No bits are masked.
• 1: LSb[0] is masked.
• 2: Two LSbs [1:0] are masked.
• ..
• 31: All bits except MSb are masked.
IPv6 packets:
This field contains bits [4:0] of L3HSBM0. These bits indicate the number of higher bits of IP SA or DA
matched in the IPv6 packets. The field is valid and applicable only when the L3DAM0 field or the L3SAM0
field is 1.
5 Layer 3 IP DA Inverse Match Enable
L3DAIM0 Indicates the status of layer 3 IP DA inverse matching.
• If this field is 1, layer 3 IP DA is enabled for inverse matching.
• If this field is 0, layer 3 IP DA is enabled for perfect matching.
This field is valid and applicable only if L3DAM0 = 1.
0b - Disabled
1b - Enabled
4 Layer 3 IP DA Match Enable
L3DAM0 Indicates the status of layer 3 IP DA matching.
• If this field is 1, layer 3 IP DA is enabled for matching.
• If this field is 0, MAC ignores layer 3 IP DA for matching.
Table continues on the next page...

---

*Page 2859*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
NOTE
If L3PEN0 = 1, you must write 1 to either this field or the L3SAM0 field because either the
IPv6 DA or SA can be checked for filtering.
0b - Disabled
1b - Enabled
3 Layer 3 IP SA Inverse Match Enable
L3SAIM0 Indicates the status of layer 3 IP SA inverse matching.
• If this field is 1, layer 3 IP SA is enabled for inverse matching.
• If this field is 0, layer 3 IP SA is enabled for perfect matching.
This field is valid and applicable only if the L3SAM0 field is 1.
0b - Disabled
1b - Enabled
2 Layer 3 IP SA Match Enable
L3SAM0 Indicates the status of layer 3 IP SA matching.
• If this field is 1, layer 3 IP SA matching is enabled.
• If this field is 0, MAC ignores layer 3 IP SA matching.
NOTE
If L3PEN0 = 1, you must write 1 to either this field or the L3DAM0 field because either IPv6
SA or DA can be checked for filtering.
0b - Disabled
1b - Enabled
1 Reserved
—
0 Layer 3 Protocol Enable
L3PEN0 Indicates the status of layer 3 protocol.
• If this field is 1, the layer 3 IP SA or DA matching is enabled for IPv6 packets.
• If this field is 0, the layer 3 IP SA or DA matching is enabled for IPv4 packets.
The layer 3 matching is performed only when either the L3SAM0 field or the L3DAM0 field is 1.
0b - Disabled
1b - Enabled

---

*Page 2860*

Ethernet Media Access Controller (EMAC)

#### 72.17.125 MAC Layer 4 Address 0 (MAC_Layer4_Address0)

Offset
Register Offset
MAC_Layer4_Address0 904h
Function
Provides the layer 4 source and destination port numbers.
MAC Layer 4 Address 1 (MAC_Layer4_Address1) , MAC L3 L4 Control 1 (MAC_L3_L4_Control1) , MAC Layer 3 Address 0 Reg
1 (MAC_Layer3_Addr0_Reg1) , MAC Layer 3 Address 1 Reg 1 (MAC_Layer3_Addr1_Reg1) , MAC Layer 3 Address 2 Reg 1
(MAC_Layer3_Addr2_Reg1) , and MAC Layer 3 Address 3 Reg 1 (MAC_Layer3_Addr3_Reg1) are reserved registers (RO with a
default value) if MAC_Packet_Filter[IPFE] is 0 when configuring the core.
You can configure the layer 3 and layer 4 address registers to be double-synchronized by selecting the synchronize layer 3
and layer 4 address registers to the receive clock domain option while configuring the core. When you select this option, the
synchronization is triggered only when bits [31:24] (in Little-Endian mode) or bits [7:0] (in Big-Endian mode) of the layer 3 and layer
4 address registers are written to. For proper synchronization updates, you must perform consecutive writes to the same layer 3
and layer 4 address registers after a delay of at least four destination clock cycles.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
L4DP0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L4SP0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-16 Layer 4 Destination Port Number
L4DP0 Indicates layer 4 destination port number.
If MAC_L3_L4_Control0[L4PEN0] = 0 and MAC_L3_L4_Control0[L4DPM0] = 1, this field contains the value
to be matched with the TCP destination port number field in the IPv4 or IPv6 packets.
If MAC_L3_L4_Control0[L4PEN0] and MAC_L3_L4_Control0[L4DPM0] are 1, this field contains the value
to be matched with the UDP destination port number field in the IPv4 or IPv6 packets.
15-0 Layer 4 Source Port Number
L4SP0 Indicates layer 4 source port number.
Table continues on the next page...

---

*Page 2861*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
If MAC_L3_L4_Control0[L4PEN0] = 0 and MAC_L3_L4_Control0[L4SPM0] = 1, this field contains the value
to be matched with the TCP source port number field in the IPv4 or IPv6 packets.
If MAC_L3_L4_Control0[L4PEN0] and MAC_L3_L4_Control0[L4SPM0] are 1, this field contains the value
to be matched with the UDP source port number field in the IPv4 or IPv6 packets.

#### 72.17.126 MAC Layer 3 Address 0 Reg 0 (MAC_Layer3_Addr0_Reg0)

Offset
Register Offset
MAC_Layer3_Addr0_Re 910h
g0
Function
Contains the 32-bit IP source address field for IPv4 packets. For IPv6 packets, the field contains bits [31:0] of the 128-bit IP
source address or destination address field.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
L3A00
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L3A00
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Layer 3 Address 0
L3A00 Indicates layer 3 address 0.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3SAM0] are 1, this field contains the
value to be matched with bits [31:0] of the IP source address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3DAM0] are 1, this field contains the
value to be matched with bits [31:0] of the IP destination address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] = 0, and MAC_L3_L4_Control0[L3SAM0] = 1, this field contains
the value to be matched with the IP source address field in the IPv4 packets.

---

*Page 2862*

Ethernet Media Access Controller (EMAC)

#### 72.17.127 MAC Layer 3 Address 1 Reg 0 (MAC_Layer3_Addr1_Reg0)

Offset
Register Offset
MAC_Layer3_Addr1_Re 914h
g0
Function
Contains the 32-bit IP destination address field for IPv4 packets. For IPv6 packets, the field contains bits [63:32] of the 128-bit
IP source address or destination address field.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
L3A10
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L3A10
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Layer 3 Address 1
L3A10 Indicates layer 3 address 1.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3SAM0] are 1, this field contains the
value to be matched with bits [63:32] of the IP source address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3DAM0] are 1, this field contains the
value to be matched with bits [63:32] of the IP destination address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] = 0, and MAC_L3_L4_Control0[L3SAM0] = 1, this field contains
the value to be matched with the IP source address field in the IPv4 packets.

#### 72.17.128 MAC Layer 3 Address 2 Reg 0 (MAC_Layer3_Addr2_Reg0)

Offset
Register Offset
MAC_Layer3_Addr2_Re 918h
g0

---

*Page 2863*

Ethernet Media Access Controller (EMAC)
Function
Contains the 32-bit IP destination address field for IPv4 packets. For IPv6 packets, the field contains bits [95:64] of the 128-bit
IP source address or destination address field.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
L3A20
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L3A20
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Layer 3 Address 2
L3A20 Indicates layer 3 address 2.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3SAM0] are 1, this field contains the
value to be matched with bits [95:64] of the IP source address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3DAM0] are 1, this field contains the
value to be matched with bits [95:64] of the IP destination address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] = 0, this field is not used.

#### 72.17.129 MAC Layer 3 Address 3 Reg 0 (MAC_Layer3_Addr3_Reg0)

Offset
Register Offset
MAC_Layer3_Addr3_Re 91Ch
g0
Function
Contains the 32-bit IP destination address field for IPv4 packets. For IPv6 packets, the field contains bits [127:96] of the
128-bit IP source address or destination address field.

---

*Page 2864*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
L3A30
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L3A30
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Layer 3 Address 3
L3A30 Indicates layer 3 address 3.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3SAM0] are 1, this field contains the
value to be matched with bits [127:96] of the IP source address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3DAM0] are 1, this field contains the
value to be matched with bits [127:96] of the IP destination address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] = 0, this field is not used.

#### 72.17.130 MAC L3 L4 Control 1 (MAC_L3_L4_Control1)

Offset
Register Offset
MAC_L3_L4_Control1 930h
Function
Controls the filter 0 operations of the layer 3 and layer 4 protocols.

---

*Page 2865*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
DMCH DMCH L4DPI L4DP L4SPI L4SP Reserv L4PEN
Reserved Reserved Reserved
EN1 N1 M1 M1 M1 M1 ed 1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L3DAI L3DA L3SAI L3SA Reserv L3PEN
L3HDBM1 L3HSBM1
M1 M1 M1 M1 ed 1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-29 Reserved
—
28 DMA Channel Select Enable 1
DMCHEN1 Indicates the status of the DMA channel number.
• If this field is 1, it enables the selection of the DMA channel number for the packet that the L3_L4
filter passes. The DMCHN fields indicate the DMA channel number.
• If this field is 0, the filter does not decide the DMA channel number.
0b - Disabled
1b - Enabled
27-25 Reserved
—
24 DMA Channel Number 1
DMCHN1 Indicates the DMA channel number.
If the value of the DMCHEN fields is 1, this field selects the DMA channel number to which the packet that
this filter passes is routed. The width of this field depends on the number of the DMA channels present in
your configuration.
23-22 Reserved
—
21 Layer 4 Destination Port Inverse Match Enable 1
L4DPIM1 Indicates the status of layer 4 destination port inverse matching.
• If this field is 1, MAC_Layer4_Address0[L4DP0] is enabled for inverse matching.
• If this field is 0, MAC_Layer4_Address0[L4DP0] is enabled for perfect matching.
This field is valid and applicable only when the L4DPM0 field is 1.
Table continues on the next page...

---

*Page 2866*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled
20 Layer 4 Destination Port Match Enable 1
L4DPM1 Indicates the status of layer 4 destination port matching.
• If this field is 1, MAC_Layer4_Address0[L4DP0] is enabled for matching.
• If this field is 0, MAC ignores MAC_Layer4_Address0[L4DP0] for matching.
0b - Disabled
1b - Enabled
19 Layer 4 Source Port Inverse Match Enable 1
L4SPIM1 Indicates the status of layer 4 source port inverse matching.
• If this field is 1, MAC_Layer4_Address0[L4SP0] is enabled for inverse matching.
• If this field is 0, MAC_Layer4_Address0[L4SP0] is enabled for perfect matching.
This field is valid and applicable only when MAC_Layer4_Address0[L4SP0] = 1.
0b - Disabled
1b - Enabled
18 Layer 4 Source Port Match Enable 1
L4SPM1 Indicates the status of layer 4 source port matching.
• If this field is 1, MAC_Layer4_Address0[L4SP0] is enabled for matching.
• If this field is 0, MAC ignores MAC_Layer4_Address0[L4SP0] for matching.
0b - Disabled
1b - Enabled
17 Reserved
—
16 Layer 4 Protocol Enable 1
L4PEN1 Indicates the status of layer 4 protocol.
• If this field is 1, MAC_Layer4_Address0[L4SP0] and MAC_Layer4_Address0[L4DP0] of UDP packets
are used for matching.
• If this field is 0, MAC_Layer4_Address0[L4SP0] and MAC_Layer4_Address0[L4DP0] of TCP packets
are used for matching.
Layer 4 matching is performed only when the L4SPM0 field or the L4DPM0 field is 1.
0b - Disabled
1b - Enabled
Table continues on the next page...

---

*Page 2867*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
15-11 Layer 3 IP DA Higher Bits Match 1
L3HDBM1 IPv4 Packets:
This field contains the number of higher bits of IP DA that are matched in the IPv4 packets. The following
list describes the values of this field:
• 0: No bits are masked.
• 1: LSb[0] is masked.
• 2: Two LSbs [1:0] are masked.
• ..
• 31: All bits except MSb are masked.
IPv6 Packets:
Bits [12:11] of this field correspond to bits [6:5] of MAC_L3_L4_Control0[L3HSBM0] , which indicates the
number of lower bits of IP SA or DA that are masked in the IPv6 packets. The following list describes the
concatenated values of MAC_L3_L4_Control0[L3HDBM0] [1:0] and MAC_L3_L4_Control0[L3HSBM0] :
• 0: No bits are masked.
• 1: LSb[0] is masked.
• 2: Two LSbs [1:0] are masked
• ..
• 127: All bits except MSb are masked.
This field is valid and applicable only when MAC_L3_L4_Control0[L3DAM0] or
MAC_L3_L4_Control0[L3SAM0] is 1.
10-6 Layer 3 IP SA Higher Bits Match 1
L3HSBM1 IPv4 packets:
This field contains the number of lower bits of IP SA that are masked for matching in the IPv4 packets. The
following list describes the values of this field:
• 0: No bits are masked.
• 1: LSb[0] is masked.
• 2: Two LSbs [1:0] are masked.
• ..
• 31: All bits except MSb are masked.
IPv6 packets:
This field contains bits [4:0] of MAC_L3_L4_Control0[L3HSBM0] . These bits indicate the number of
higher bits of IP SA or DA matched in the IPv6 packets. The field is valid and applicable only when
MAC_L3_L4_Control0[L3DAM0] or MAC_L3_L4_Control0[L3SAM0] is 1.
5 Layer 3 IP DA Inverse Match Enable 1
L3DAIM1 Indicates the status of layer 3 IP DA inverse matching.
Table continues on the next page...

---

*Page 2868*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• If this field is 1, layer 3 IP DA is enabled for inverse matching.
• If this field is 0, layer 3 IP DA is enabled for perfect matching.
This field is valid and applicable only if MAC_L3_L4_Control0[L3DAM0] = 1.
0b - Disabled
1b - Enabled
4 Layer 3 IP DA Match Enable 1
L3DAM1 Indicates the status of layer 3 IP DA matching.
• If this field is 1, layer 3 IP DA is enabled for matching.
• If this field is 0, MAC ignores layer 3 IP DA for matching.
NOTE
If MAC_L3_L4_Control0[L3PEN0] = 1, you must write 1 to either this field or to
MAC_L3_L4_Control0[L3SAM0] because either the IPv6 DA or SA can be checked
for filtering.
0b - Disabled
1b - Enabled
3 Layer 3 IP SA Inverse Match Enable 1
L3SAIM1 Indicates the status of layer 3 IP SA inverse matching.
• If this field is 1, layer 3 IP SA is enabled for inverse matching.
• If this field is 0, layer 3 IP SA is enabled for perfect matching.
This field is valid and applicable only if MAC_L3_L4_Control0[L3SAM0] is 1.
0b - Disabled
1b - Enabled
2 Layer 3 IP SA Match Enable 1
L3SAM1 Indicates the status of layer 3 IP SA matching.
• If this field is 1, layer 3 IP SA matching is enabled.
• If this field is 0, MAC ignores layer 3 IP SA matching.
NOTE
If MAC_L3_L4_Control0[L3PEN0] = 1, you must write 1 to either this field or to
MAC_L3_L4_Control0[L3DAM0] because either IPv6 SA or DA can be checked for filtering.
0b - Disabled
1b - Enabled
1 Reserved
—
Table continues on the next page...

---

*Page 2869*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0 Layer 3 Protocol Enable 1
L3PEN1 Indicates the status of layer 3 protocol.
• If this field is 1, the layer 3 IP SA or DA matching is enabled for IPv6 packets.
• If this field is 0, the layer 3 IP SA or DA matching is enabled for IPv4 packets.
The layer 3 matching is performed only when either MAC_L3_L4_Control0[L3SAM0] or
MAC_L3_L4_Control0[L3DAM0] is 1.
0b - Disabled
1b - Enabled

#### 72.17.131 MAC Layer 4 Address 1 (MAC_Layer4_Address1)

Offset
Register Offset
MAC_Layer4_Address1 934h
Function
Provides the layer 4 source and destination port numbers.
MAC Layer 4 Address 1 (MAC_Layer4_Address1) , MAC L3 L4 Control 1 (MAC_L3_L4_Control1) , MAC Layer 3 Address 0 Reg
1 (MAC_Layer3_Addr0_Reg1) , MAC Layer 3 Address 1 Reg 1 (MAC_Layer3_Addr1_Reg1) , MAC Layer 3 Address 2 Reg 1
(MAC_Layer3_Addr2_Reg1) , and MAC Layer 3 Address 3 Reg 1 (MAC_Layer3_Addr3_Reg1) are reserved registers (RO with a
default value) if the enable layer 3 and layer 4 packet filter option is not selected when configuring the core.
You can configure the layer 3 and layer 4 address registers to be double-synchronized by selecting the synchronize layer 3
and layer 4 address registers to the receive clock domain option while configuring the core. When you select this option, the
synchronization is triggered only when bits [31:24] (in Little-Endian mode) or bits [7:0] (in Big-Endian mode) of the layer 3 and layer
4 address registers are written to. For proper synchronization updates, you must perform consecutive writes to the same layer 3
and layer 4 address registers after a delay of at least four destination clock cycles.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
L4DP1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L4SP1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2870*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-16 Layer 4 Destination Port Number 1
L4DP1 Indicates layer 4 destination port number.
If MAC_L3_L4_Control0[L4PEN0] = 0 and MAC_L3_L4_Control0[L4DPM0] = 1, this field contains the value
to be matched with the TCP destination port number field in the IPv4 or IPv6 packets.
If MAC_L3_L4_Control0[L4PEN0] and MAC_L3_L4_Control0[L4DPM0] are 1, this field contains the value
to be matched with the UDP destination port number field in the IPv4 or IPv6 packets.
15-0 Layer 4 Source Port Number 1
L4SP1 Indicates layer 4 source port number.
If MAC_L3_L4_Control0[L4PEN0] = 0 and MAC_L3_L4_Control0[L4SPM0] = 1, this field contains the value
to be matched with the TCP source port number field in the IPv4 or IPv6 packets.
If MAC_L3_L4_Control0[L4PEN0] and MAC_L3_L4_Control0[L4SPM0] are 1, this field contains the value
to be matched with the UDP source port number field in the IPv4 or IPv6 packets.

#### 72.17.132 MAC Layer 3 Address 0 Reg 1 (MAC_Layer3_Addr0_Reg1)

Offset
Register Offset
MAC_Layer3_Addr0_Re 940h
g1
Function
Contains the 32-bit IP source address field for IPv4 packets. For IPv6 packets, the field contains bits [31:0] of the 128-bit IP
source address or destination address field.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
L3A01
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L3A01
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2871*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Layer 3 Address 0
L3A01 Indicates layer 3 address 0.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3SAM0] are 1, this field contains the
value to be matched with bits [31:0] of the IP source address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3DAM0] are 1, this field contains the
value to be matched with bits [31:0] of the IP destination address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] = 0, and MAC_L3_L4_Control0[L3SAM0] = 1, this field contains
the value to be matched with the IP source address field in the IPv4 packets.

#### 72.17.133 MAC Layer 3 Address 1 Reg 1 (MAC_Layer3_Addr1_Reg1)

Offset
Register Offset
MAC_Layer3_Addr1_Re 944h
g1
Function
Contains the 32-bit IP destination address field for IPv4 packets. For IPv6 packets, the field contains bits[63:32] of the 128-bit
IP source address or destination address field.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
L3A11
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L3A11
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Layer 3 Address 1
L3A11 Indicates layer 3 address 1.
Table continues on the next page...

---

*Page 2872*

Ethernet Media Access Controller (EMAC)
Field Function
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3SAM0] are 1, this field contains the
value to be matched with bits [63:32] of the IP source address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3DAM0] are 1, this field contains the
value to be matched with bits [63:32] of the IP destination address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] = 0, and MAC_L3_L4_Control0[L3SAM0] = 1, this field contains
the value to be matched with the IP source address field in the IPv4 packets.

#### 72.17.134 MAC Layer 3 Address 2 Reg 1 (MAC_Layer3_Addr2_Reg1)

Offset
Register Offset
MAC_Layer3_Addr2_Re 948h
g1
Function
Contains the 32-bit IP destination address field for IPv4 packets. For IPv6 packets, the field contains bits [95:64] of the 128-bit
IP source address or destination address field.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
L3A21
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L3A21
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Layer 3 Address 2
L3A21 Indicates layer 3 address 2.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3SAM0] are 1, this field contains the
value to be matched with bits [95:64] of the IP source address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3DAM0] are 1, this field contains the
value to be matched with bits [95:64] of the IP destination address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] = 0, this field is not used.

---

*Page 2873*

Ethernet Media Access Controller (EMAC)

#### 72.17.135 MAC Layer 3 Address 3 Reg 1 (MAC_Layer3_Addr3_Reg1)

Offset
Register Offset
MAC_Layer3_Addr3_Re 94Ch
g1
Function
Contains the 32-bit IP destination address field for IPv4 packets. For IPv6 packets, the field contains bits [127:96] of the
128-bit IP source address or destination address field.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
L3A31
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L3A31
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Layer 3 Address 3
L3A31 Indicates layer 3 address 3.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3SAM0] are 1, this field contains the
value to be matched with bits [127:96] of the IP source address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3DAM0] are 1, this field contains the
value to be matched with bits [127:96] of the IP destination address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] = 0, this field is not used.

#### 72.17.136 MAC L3 L4 Control 2 (MAC_L3_L4_Control2)

Offset
Register Offset
MAC_L3_L4_Control2 960h
Function
Controls the filter 0 operations of the layer 3 and layer 4 protocols.

---

*Page 2874*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
DMCH DMCH L4DPI L4DP L4SPI L4SP Reserv L4PEN
Reserved Reserved Reserved
EN2 N2 M2 M2 M2 M2 ed 2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L3DAI L3DA L3SAI L3SA Reserv L3PEN
L3HDBM2 L3HSBM2
M2 M2 M2 M2 ed 2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-29 Reserved
—
28 DMA Channel Select Enable 2
DMCHEN2 Indicates the status of the DMA channel number.
• If this field is 1, it enables the selection of the DMA channel number for the packet that the L3_L4
filter passes. The DMCHN fields indicate the DMA channel number.
• If this field is 0, the filter does not decide the DMA channel number.
0b - Disabled
1b - Enabled
27-25 Reserved
—
24 DMA Channel Number 2
DMCHN2 Indicates the DMA channel number.
If the value of the DMCHEN fields is 1, this field selects the DMA channel number to which the packet that
this filter passes is routed. The width of this field depends on the number of the DMA channels present in
your configuration.
23-22 Reserved
—
21 Layer 4 Destination Port Inverse Match Enable 2
L4DPIM2 Indicates the status of layer 4 destination port inverse matching.
• If this field is 1, MAC_Layer4_Address0[L4DP0] is enabled for inverse matching.
• If this field is 0, MAC_Layer4_Address0[L4DP0] is enabled for perfect matching.
This field is valid and applicable only when the L4DPM0 field is 1.
Table continues on the next page...

---

*Page 2875*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled
20 Layer 4 Destination Port Match Enable 2
L4DPM2 Indicates the status of layer 4 destination port matching.
• If this field is 1, MAC_Layer4_Address0[L4DP0] is enabled for matching.
• If this field is 0, MAC ignores MAC_Layer4_Address0[L4DP0] for matching.
0b - Disabled
1b - Enabled
19 Layer 4 Source Port Inverse Match Enable 2
L4SPIM2 Indicates the status of layer 4 source port inverse matching.
• If this field is 1, MAC_Layer4_Address0[L4SP0] is enabled for inverse matching.
• If this field is 0, MAC_Layer4_Address0[L4SP0] is enabled for perfect matching.
This field is valid and applicable only when MAC_Layer4_Address0[L4SP0] = 1.
0b - Disabled
1b - Enabled
18 Layer 4 Source Port Match Enable 2
L4SPM2 Indicates the status of layer 4 source port matching.
• If this field is 1, MAC_Layer4_Address0[L4SP0] is enabled for matching.
• If this field is 0, MAC ignores MAC_Layer4_Address0[L4SP0] for matching.
0b - Disabled
1b - Enabled
17 Reserved
—
16 Layer 4 Protocol Enable 2
L4PEN2 Indicates the status of layer 4 protocol.
• If this field is 1, MAC_Layer4_Address0[L4SP0] and MAC_Layer4_Address0[L4DP0] of UDP packets
are used for matching.
• If this field is 0, MAC_Layer4_Address0[L4SP0] and MAC_Layer4_Address0[L4DP0] of TCP packets
are used for matching.
Layer 4 matching is performed only when the L4SPM0 field or the L4DPM0 field is 1.
0b - Disabled
1b - Enabled
Table continues on the next page...

---

*Page 2876*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
15-11 Layer 3 IP DA Higher Bits Match 2
L3HDBM2 IPv4 Packets:
This field contains the number of higher bits of IP DA that are matched in the IPv4 packets. The following
list describes the values of this field:
• 0: No bits are masked.
• 1: LSb[0] is masked.
• 2: Two LSbs [1:0] are masked.
• ..
• 31: All bits except MSb are masked.
IPv6 Packets:
Bits [12:11] of this field correspond to bits [6:5] of MAC_L3_L4_Control0[L3HSBM0] , which indicates the
number of lower bits of IP SA or DA that are masked in the IPv6 packets. The following list describes the
concatenated values of MAC_L3_L4_Control0[L3HDBM0] [1:0] and MAC_L3_L4_Control0[L3HSBM0] :
• 0: No bits are masked.
• 1: LSb[0] is masked.
• 2: Two LSbs [1:0] are masked
• ..
• 127: All bits except MSb are masked.
This field is valid and applicable only when MAC_L3_L4_Control0[L3DAM0] or
MAC_L3_L4_Control0[L3SAM0] is 1.
10-6 Layer 3 IP SA Higher Bits Match 2
L3HSBM2 IPv4 packets:
This field contains the number of lower bits of IP SA that are masked for matching in the IPv4 packets. The
following list describes the values of this field:
• 0: No bits are masked.
• 1: LSb[0] is masked.
• 2: Two LSbs [1:0] are masked.
• ..
• 31: All bits except MSb are masked.
IPv6 packets:
This field contains bits [4:0] of MAC_L3_L4_Control0[L3HSBM0] . These bits indicate the number of
higher bits of IP SA or DA matched in the IPv6 packets. The field is valid and applicable only when
MAC_L3_L4_Control0[L3DAM0] or MAC_L3_L4_Control0[L3SAM0] is 1.
5 Layer 3 IP DA Inverse Match Enable 2
L3DAIM2 Indicates the status of layer 3 IP DA inverse matching.
Table continues on the next page...

---

*Page 2877*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• If this field is 1, layer 3 IP DA is enabled for inverse matching.
• If this field is 0, layer 3 IP DA is enabled for perfect matching.
This field is valid and applicable only if MAC_L3_L4_Control0[L3DAM0] = 1.
0b - Disabled
1b - Enabled
4 Layer 3 IP DA Match Enable 2
L3DAM2 Indicates the status of layer 3 IP DA matching.
• If this field is 1, layer 3 IP DA is enabled for matching.
• If this field is 0, MAC ignores layer 3 IP DA for matching.
NOTE
If MAC_L3_L4_Control0[L3PEN0] = 1, you must write 1 to either this field or to
MAC_L3_L4_Control0[L3SAM0] because either the IPv6 DA or SA can be checked
for filtering.
0b - Disabled
1b - Enabled
3 Layer 3 IP SA Inverse Match Enable 2
L3SAIM2 Indicates the status of layer 3 IP SA inverse matching.
• If this field is 1, layer 3 IP SA is enabled for inverse matching.
• If this field is 0, layer 3 IP SA is enabled for perfect matching.
This field is valid and applicable only if MAC_L3_L4_Control0[L3SAM0] is 1.
0b - Disabled
1b - Enabled
2 Layer 3 IP SA Match Enable 2
L3SAM2 Indicates the status of layer 3 IP SA matching.
• If this field is 1, layer 3 IP SA matching is enabled.
• If this field is 0, MAC ignores layer 3 IP SA matching.
NOTE
If MAC_L3_L4_Control0[L3PEN0] = 1, you must write 1 to either this field or to
MAC_L3_L4_Control0[L3DAM0] because either IPv6 SA or DA can be checked for filtering.
0b - Disabled
1b - Enabled
1 Reserved
—
Table continues on the next page...

---

*Page 2878*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0 Layer 3 Protocol Enable 2
L3PEN2 Indicates the status of layer 3 protocol.
• If this field is 1, the layer 3 IP SA or DA matching is enabled for IPv6 packets.
• If this field is 0, the layer 3 IP SA or DA matching is enabled for IPv4 packets.
The layer 3 matching is performed only when either MAC_L3_L4_Control0[L3SAM0] or
MAC_L3_L4_Control0[L3DAM0] is 1.
0b - Disabled
1b - Enabled

#### 72.17.137 MAC Layer 4 Address 2 (MAC_Layer4_Address2)

Offset
Register Offset
MAC_Layer4_Address2 964h
Function
Provides the layer 4 source and destination port numbers.
MAC Layer 4 Address 1 (MAC_Layer4_Address1) , MAC L3 L4 Control 1 (MAC_L3_L4_Control1) , MAC Layer 3 Address 0 Reg
1 (MAC_Layer3_Addr0_Reg1) , MAC Layer 3 Address 1 Reg 1 (MAC_Layer3_Addr1_Reg1) , MAC Layer 3 Address 2 Reg 1
(MAC_Layer3_Addr2_Reg1) , and MAC Layer 3 Address 3 Reg 1 (MAC_Layer3_Addr3_Reg1) are reserved registers (RO with a
default value) if the enable layer 3 and layer 4 packet filter option is not selected when configuring the core.
You can configure the layer 3 and layer 4 address registers to be double-synchronized by selecting the synchronize layer 3
and layer 4 address registers to the receive clock domain option while configuring the core. When you select this option, the
synchronization is triggered only when bits [31:24] (in Little-Endian mode) or bits [7:0] (in Big-Endian mode) of the layer 3 and layer
4 address registers are written to. For proper synchronization updates, you must perform consecutive writes to the same layer 3
and layer 4 address registers after a delay of at least four destination clock cycles.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
L4DP2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L4SP2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2879*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-16 Layer 4 Destination Port Number 2
L4DP2 Indicates layer 4 destination port number.
If MAC_L3_L4_Control0[L4PEN0] = 0 and MAC_L3_L4_Control0[L4DPM0] = 1, this field contains the value
to be matched with the TCP destination port number field in the IPv4 or IPv6 packets.
If MAC_L3_L4_Control0[L4PEN0] and MAC_L3_L4_Control0[L4DPM0] are 1, this field contains the value
to be matched with the UDP destination port number field in the IPv4 or IPv6 packets.
15-0 Layer 4 Source Port Number 2
L4SP2 Indicates layer 4 source port number.
If MAC_L3_L4_Control0[L4PEN0] = 0 and MAC_L3_L4_Control0[L4SPM0] = 1, this field contains the value
to be matched with the TCP source port number field in the IPv4 or IPv6 packets.
If MAC_L3_L4_Control0[L4PEN0] and MAC_L3_L4_Control0[L4SPM0] are 1, this field contains the value
to be matched with the UDP source port number field in the IPv4 or IPv6 packets.

#### 72.17.138 MAC Layer 3 Address 0 Reg 2 (MAC_Layer3_Addr0_Reg2)

Offset
Register Offset
MAC_Layer3_Addr0_Re 970h
g2
Function
Contains the 32-bit IP source address field for IPv4 packets. For IPv6 packets, the field contains bits [31:0] of the 128-bit IP
source address or destination address field.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
L3A02
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L3A02
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2880*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Layer 3 Address 0
L3A02 Indicates layer 3 address 0.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3SAM0] are 1, this field contains the
value to be matched with bits [31:0] of the IP source address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3DAM0] are 1, this field contains the
value to be matched with bits [31:0] of the IP destination address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] = 0, and MAC_L3_L4_Control0[L3SAM0] = 1, this field contains
the value to be matched with the IP source address field in the IPv4 packets.

#### 72.17.139 MAC Layer 3 Address 1 Reg 2 (MAC_Layer3_Addr1_Reg2)

Offset
Register Offset
MAC_Layer3_Addr1_Re 974h
g2
Function
Contains the 32-bit IP destination address field for IPv4 packets. For IPv6 packets, the field contains bits[63:32] of the 128-bit
IP source address or destination address field.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
L3A12
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L3A12
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Layer 3 Address 1
L3A12 Indicates layer 3 address 1.
Table continues on the next page...

---

*Page 2881*

Ethernet Media Access Controller (EMAC)
Field Function
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3SAM0] are 1, this field contains the
value to be matched with bits [63:32] of the IP source address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3DAM0] are 1, this field contains the
value to be matched with bits [63:32] of the IP destination address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] = 0, and MAC_L3_L4_Control0[L3SAM0] = 1, this field contains
the value to be matched with the IP source address field in the IPv4 packets.

#### 72.17.140 MAC Layer 3 Address 2 Reg 2 (MAC_Layer3_Addr2_Reg2)

Offset
Register Offset
MAC_Layer3_Addr2_Re 978h
g2
Function
Contains the 32-bit IP destination address field for IPv4 packets. For IPv6 packets, the field contains bits [95:64] of the 128-bit
IP source address or destination address field.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
L3A22
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L3A22
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Layer 3 Address 2
L3A22 Indicates layer 3 address 2.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3SAM0] are 1, this field contains the
value to be matched with bits [95:64] of the IP source address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3DAM0] are 1, this field contains the
value to be matched with bits [95:64] of the IP destination address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] = 0, this field is not used.

---

*Page 2882*

Ethernet Media Access Controller (EMAC)

#### 72.17.141 MAC Layer 3 Address 3 Reg 2 (MAC_Layer3_Addr3_Reg2)

Offset
Register Offset
MAC_Layer3_Addr3_Re 97Ch
g2
Function
Contains the 32-bit IP destination address field for IPv4 packets. For IPv6 packets, the field contains bits [127:96] of the
128-bit IP source address or destination address field.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
L3A32
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L3A32
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Layer 3 Address 3
L3A32 Indicates layer 3 address 3.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3SAM0] are 1, this field contains the
value to be matched with bits [127:96] of the IP source address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3DAM0] are 1, this field contains the
value to be matched with bits [127:96] of the IP destination address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] = 0, this field is not used.

#### 72.17.142 MAC L3 L4 Control 3 (MAC_L3_L4_Control3)

Offset
Register Offset
MAC_L3_L4_Control3 990h
Function
Controls the filter 0 operations of the layer 3 and layer 4 protocols.

---

*Page 2883*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
DMCH DMCH L4DPI L4DP L4SPI L4SP Reserv L4PEN
Reserved Reserved Reserved
EN3 N3 M3 M3 M3 M3 ed 3
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L3DAI L3DA L3SAI L3SA Reserv L3PEN
L3HDBM3 L3HSBM3
M3 M3 M3 M3 ed 3
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-29 Reserved
—
28 DMA Channel Select Enable 3
DMCHEN3 Indicates the status of the DMA channel number.
• If this field is 1, it enables the selection of the DMA channel number for the packet that the L3_L4
filter passes. The DMCHN fields indicate the DMA channel number.
• If this field is 0, the filter does not decide the DMA channel number.
0b - Disabled
1b - Enabled
27-25 Reserved
—
24 DMA Channel Number 2
DMCHN3 Indicates the DMA channel number.
If the value of the DMCHEN fields is 1, this field selects the DMA channel number to which the packet that
this filter passes is routed. The width of this field depends on the number of the DMA channels present in
your configuration.
23-22 Reserved
—
21 Layer 4 Destination Port Inverse Match Enable 3
L4DPIM3 Indicates the status of layer 4 destination port inverse matching.
• If this field is 1, MAC_Layer4_Address0[L4DP0] is enabled for inverse matching.
• If this field is 0, MAC_Layer4_Address0[L4DP0] is enabled for perfect matching.
This field is valid and applicable only when the L4DPM0 field is 1.
Table continues on the next page...

---

*Page 2884*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled
20 Layer 4 Destination Port Match Enable 3
L4DPM3 Indicates the status of layer 4 destination port matching.
• If this field is 1, MAC_Layer4_Address0[L4DP0] is enabled for matching.
• If this field is 0, MAC ignores MAC_Layer4_Address0[L4DP0] for matching.
0b - Disabled
1b - Enabled
19 Layer 4 Source Port Inverse Match Enable 3
L4SPIM3 Indicates the status of layer 4 source port inverse matching.
• If this field is 1, MAC_Layer4_Address0[L4SP0] is enabled for inverse matching.
• If this field is 0, MAC_Layer4_Address0[L4SP0] is enabled for perfect matching.
This field is valid and applicable only when MAC_Layer4_Address0[L4SP0] = 1.
0b - Disabled
1b - Enabled
18 Layer 4 Source Port Match Enable 3
L4SPM3 Indicates the status of layer 4 source port matching.
• If this field is 1, MAC_Layer4_Address0[L4SP0] is enabled for matching.
• If this field is 0, MAC ignores MAC_Layer4_Address0[L4SP0] for matching.
0b - Disabled
1b - Enabled
17 Reserved
—
16 Layer 4 Protocol Enable 3
L4PEN3 Indicates the status of layer 4 protocol.
• If this field is 1, MAC_Layer4_Address0[L4SP0] and MAC_Layer4_Address0[L4DP0] of UDP packets
are used for matching.
• If this field is 0, MAC_Layer4_Address0[L4SP0] and MAC_Layer4_Address0[L4DP0] of TCP packets
are used for matching.
Layer 4 matching is performed only when the L4SPM0 field or the L4DPM0 field is 1.
0b - Disabled
1b - Enabled
Table continues on the next page...

---

*Page 2885*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
15-11 Layer 3 IP DA Higher Bits Match 3
L3HDBM3 IPv4 Packets:
This field contains the number of higher bits of IP DA that are matched in the IPv4 packets. The following
list describes the values of this field:
• 0: No bits are masked.
• 1: LSb[0] is masked.
• 2: Two LSbs [1:0] are masked.
• ..
• 31: All bits except MSb are masked.
IPv6 Packets:
Bits [12:11] of this field correspond to bits [6:5] of MAC_L3_L4_Control0[L3HSBM0] , which indicates the
number of lower bits of IP SA or DA that are masked in the IPv6 packets. The following list describes the
concatenated values of MAC_L3_L4_Control0[L3HDBM0] [1:0] and MAC_L3_L4_Control0[L3HSBM0] :
• 0: No bits are masked.
• 1: LSb[0] is masked.
• 2: Two LSbs [1:0] are masked
• ..
• 127: All bits except MSb are masked.
This field is valid and applicable only when MAC_L3_L4_Control0[L3DAM0] or
MAC_L3_L4_Control0[L3SAM0] is 1.
.
10-6 Layer 3 IP SA Higher Bits Match 3
L3HSBM3 IPv4 packets:
This field contains the number of lower bits of IP SA that are masked for matching in the IPv4 packets. The
following list describes the values of this field:
• 0: No bits are masked.
• 1: LSb[0] is masked.
• 2: Two LSbs [1:0] are masked.
• ..
• 31: All bits except MSb are masked.
IPv6 packets:
This field contains bits [4:0] of MAC_L3_L4_Control0[L3HSBM0] . These bits indicate the number of
higher bits of IP SA or DA matched in the IPv6 packets. The field is valid and applicable only when
MAC_L3_L4_Control0[L3DAM0] or MAC_L3_L4_Control0[L3SAM0] is 1.
5 Layer 3 IP DA Inverse Match Enable 3
Table continues on the next page...

---

*Page 2886*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
L3DAIM3 Indicates the status of layer 3 IP DA inverse matching.
• If this field is 1, layer 3 IP DA is enabled for inverse matching.
• If this field is 0, layer 3 IP DA is enabled for perfect matching.
This field is valid and applicable only if MAC_L3_L4_Control0[L3DAM0] = 1.
0b - Disabled
1b - Enabled
4 Layer 3 IP DA Match Enable 3
L3DAM3 Indicates the status of layer 3 IP DA matching.
• If this field is 1, layer 3 IP DA is enabled for matching.
• If this field is 0, MAC ignores layer 3 IP DA for matching.
NOTE
If MAC_L3_L4_Control0[L3PEN0] = 1, you must write 1 to either this field or to
MAC_L3_L4_Control0[L3SAM0] because either the IPv6 DA or SA can be checked
for filtering.
0b - Disabled
1b - Enabled
3 Layer 3 IP SA Inverse Match Enable 3
L3SAIM3 Indicates the status of layer 3 IP SA inverse matching.
• If this field is 1, layer 3 IP SA is enabled for inverse matching.
• If this field is 0, layer 3 IP SA is enabled for perfect matching.
This field is valid and applicable only if MAC_L3_L4_Control0[L3SAM0] is 1.
0b - Disabled
1b - Enabled
2 Layer 3 IP SA Match Enable 3
L3SAM3 Indicates the status of layer 3 IP SA matching.
• If this field is 1, layer 3 IP SA matching is enabled.
• If this field is 0, MAC ignores layer 3 IP SA matching.
NOTE
If MAC_L3_L4_Control0[L3PEN0] = 1, you must write 1 to either this field or to
MAC_L3_L4_Control0[L3DAM0] because either IPv6 SA or DA can be checked for filtering.
0b - Disabled
1b - Enabled
1 Reserved
Table continues on the next page...

---

*Page 2887*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
—
0 Layer 3 Protocol Enable 3
L3PEN3 Indicates the status of layer 3 protocol.
• If this field is 1, the layer 3 IP SA or DA matching is enabled for IPv6 packets.
• If this field is 0, the layer 3 IP SA or DA matching is enabled for IPv4 packets.
The layer 3 matching is performed only when either MAC_L3_L4_Control0[L3SAM0] or
MAC_L3_L4_Control0[L3DAM0] is 1.
0b - Disabled
1b - Enabled

#### 72.17.143 MAC Layer 4 Address 3 (MAC_Layer4_Address3)

Offset
Register Offset
MAC_Layer4_Address3 994h
Function
Provides the layer 4 source and destination port numbers.
MAC Layer 4 Address 1 (MAC_Layer4_Address1) , MAC L3 L4 Control 1 (MAC_L3_L4_Control1) , MAC Layer 3 Address 0 Reg
1 (MAC_Layer3_Addr0_Reg1) , MAC Layer 3 Address 1 Reg 1 (MAC_Layer3_Addr1_Reg1) , MAC Layer 3 Address 2 Reg 1
(MAC_Layer3_Addr2_Reg1) , and MAC Layer 3 Address 3 Reg 1 (MAC_Layer3_Addr3_Reg1) are reserved registers (RO with a
default value) if the enable layer 3 and layer 4 packet filter option is not selected when configuring the core.
You can configure the layer 3 and layer 4 address registers to be double-synchronized by selecting the synchronize layer 3
and layer 4 address registers to the receive clock domain option while configuring the core. When you select this option, the
synchronization is triggered only when bits [31:24] (in Little-Endian mode) or bits [7:0] (in Big-Endian mode) of the layer 3 and layer
4 address registers are written to. For proper synchronization updates, you must perform consecutive writes to the same layer 3
and layer 4 address registers after a delay of at least four destination clock cycles.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
L4DP3
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L4SP3
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2888*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-16 Layer 4 Destination Port Number 3
L4DP3 Indicates layer 4 destination port number.
If MAC_L3_L4_Control0[L4PEN0] = 0 and MAC_L3_L4_Control0[L4DPM0] = 1, this field contains the value
to be matched with the TCP destination port number field in the IPv4 or IPv6 packets.
If MAC_L3_L4_Control0[L4PEN0] and MAC_L3_L4_Control0[L4DPM0] are 1, this field contains the value
to be matched with the UDP destination port number field in the IPv4 or IPv6 packets.
15-0 Layer 4 Source Port Number 3
L4SP3 Indicates layer 4 source port number.
If MAC_L3_L4_Control0[L4PEN0] = 0 and MAC_L3_L4_Control0[L4SPM0] = 1, this field contains the value
to be matched with the TCP source port number field in the IPv4 or IPv6 packets.
If MAC_L3_L4_Control0[L4PEN0] and MAC_L3_L4_Control0[L4SPM0] are 1, this field contains the value
to be matched with the UDP source port number field in the IPv4 or IPv6 packets.

#### 72.17.144 MAC Layer 3 Address 0 Reg 3 (MAC_Layer3_Addr0_Reg3)

Offset
Register Offset
MAC_Layer3_Addr0_Re 9A0h
g3
Function
Contains the 32-bit IP source address field for IPv4 packets. For IPv6 packets, the field contains bits [31:0] of the 128-bit IP
source address or destination address field.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
L3A03
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L3A03
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2889*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Layer 3 Address 0
L3A03 Indicates layer 3 address 0.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3SAM0] are 1, this field contains the
value to be matched with bits [31:0] of the IP source address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3DAM0] are 1, this field contains the
value to be matched with bits [31:0] of the IP destination address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] = 0, and MAC_L3_L4_Control0[L3SAM0] = 1, this field contains
the value to be matched with the IP source address field in the IPv4 packets.

#### 72.17.145 MAC Layer 3 Address 1 Reg 3 (MAC_Layer3_Addr1_Reg3)

Offset
Register Offset
MAC_Layer3_Addr1_Re 9A4h
g3
Function
Contains the 32-bit IP destination address field for IPv4 packets. For IPv6 packets, the field contains bits [63:32] of the 128-bit
IP source address or destination address field.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
L3A13
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L3A13
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Layer 3 Address 1
L3A13 Indicates layer 3 address 1.
Table continues on the next page...

---

*Page 2890*

Ethernet Media Access Controller (EMAC)
Field Function
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3SAM0] are 1, this field contains the
value to be matched with bits [63:32] of the IP source address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3DAM0] are 1, this field contains the
value to be matched with bits [63:32] of the IP destination address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] = 0, and MAC_L3_L4_Control0[L3SAM0] = 1, this field contains
the value to be matched with the IP source address field in the IPv4 packets.

#### 72.17.146 MAC Layer 3 Address 2 Reg 3 (MAC_Layer3_Addr2_Reg3)

Offset
Register Offset
MAC_Layer3_Addr2_Re 9A8h
g3
Function
Contains the 32-bit IP destination address field for IPv4 packets. For IPv6 packets, the field contains bits [95:64] of the 128-bit
IP source address or destination address field.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
L3A23
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L3A23
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Layer 3 Address 2
L3A23 Indicates layer 3 address 2.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3SAM0] are 1, this field contains the
value to be matched with bits [95:64] of the IP source address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3DAM0] are 1, this field contains the
value to be matched with bits [95:64] of the IP destination address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] = 0, this field is not used.

---

*Page 2891*

Ethernet Media Access Controller (EMAC)

#### 72.17.147 MAC Layer 3 Address 3 Reg 3 (MAC_Layer3_Addr3_Reg3)

Offset
Register Offset
MAC_Layer3_Addr3_Re 9ACh
g3
Function
Contains the 32-bit IP destination address field for IPv4 packets. For IPv6 packets, the field contains bits [127:96] of the
128-bit IP source address or destination address field.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
L3A33
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
L3A33
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Layer 3 Address 3
L3A33 Indicates layer 3 address 3.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3SAM0] are 1, this field contains the
value to be matched with bits [127:96] of the IP source address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] and MAC_L3_L4_Control0[L3DAM0] are 1, this field contains the
value to be matched with bits [127:96] of the IP destination address field in the IPv6 packets.
• If MAC_L3_L4_Control0[L3PEN0] = 0, this field is not used.

#### 72.17.148 MAC Timestamp Control (MAC_Timestamp_Control)

Offset
Register Offset
MAC_Timestamp_Contro B00h
l

---

*Page 2892*

Ethernet Media Access Controller (EMAC)
Function
Controls the operation of the system time generator and processing of PTP packets for timestamping in the receiver.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
AV802 TXTSS Reserv TSEN
Reserved Reserved Reserved ESTI SNAPTYPSEL
1A ... TSM ed MAC ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TSMS TSEV TSIPV TSIPV TSIPE TSVE TSCT TSEN Reserv TSAD Reserv TSUP TSCF TSEN
PTGE TSINIT
TRE ... NTE ... 4E ... 6E ... NA R2E ... RLS ... ALL ed DREG ed DT UPDT A
W
Reset 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-29 Reserved
—
28 AV 802.1AS Mode Enable
AV8021ASMEN Indicates the status of AV 802.1AS mode.
If this field is 1, MAC processes only untagged PTP over Ethernet packets for providing PTP status and
capturing timestamp snapshots, which means using the IEEE 802.1AS mode of operation.
If MAC_HW_Feature1[PTOEN] = 1, for the purpose of PTP offload, the transport-specific field in the PTP
header is generated and checked based on the value of the AV8021ASMEN field.
0b - Disabled
1b - Enabled
27-25 Reserved
—
24 Transmit Timestamp Status Mode
TXTSSTSM Indicates the status of transmit timestamp status mode.
• If this field is 1, MAC overwrites the previous transmit timestamp status even if the software does not
read it. MAC indicates this by writing 1 to MAC_Tx_Timestamp_Status_Nanoseconds[TXTSSMIS] .
• If this field is 0, MAC ignores the timestamp status of the current packet if the software does
not read the timestamp status of the previous packet. MAC indicates this by writing 1 to
MAC_Tx_Timestamp_Status_Nanoseconds[TXTSSMIS] .
0b - Disabled
1b - Enabled
Table continues on the next page...

---

*Page 2893*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
23-21 Reserved
—
20 External System Time Input
ESTI Indicates the status of external system time input.
If this field is 1, MAC uses the external 64-bit reference system time input for these functions:
• To consider the timestamp provided as status
• To insert the timestamp in transmit PTP packets if the one-step timestamp or the timestamp offload
feature is enabled
If this field is 1, MAC uses the internal reference system time.
0b - Disabled
1b - Enabled
19 Reserved
—
18 Enable MAC Address For PTP Packet Filtering
TSENMACADD Indicates whether the MAC address for PTP packet filtering is enabled.
R
If this field is 1, the DA MAC address, which matches any MAC Address register, is used to filter the PTP
packets when PTP is directly sent over Ethernet.
If this field is 1, received PTP packets with DA containing a special multicast or unicast address that matches
the one programmed in the MAC address registers are considered for processing when PTP is directly sent
over Ethernet.
• For a normal time stamping operation, MAC address registers 0 to 31 are considered for unicast
destination address matching.
• For PTP offload, only MAC address register 0 is considered for unicast destination address
matching.
0b - Disabled
1b - Enabled
17-16 Select PTP Packets For Taking Snapshots
SNAPTYPSEL Determines the set of PTP packet types for which a snapshot needs to be taken.
This field, along with the TSEVNTENA and TSMSTRENA fields, determines the set of PTP packet types for
which a snapshot needs to be taken. See Receive path functions for encoding.
15 Enable Snapshot For Messages Relevant To Master
TSMSTRENA Indicates whether the snapshot for messages relevant to master is enabled.
If this field is 1, the snapshot is taken only for the messages that are relevant to the master node. Otherwise,
the snapshot is taken for the messages relevant to the slave node.
Table continues on the next page...

---

*Page 2894*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled
14 Enable Timestamp Snapshot For Event Messages
TSEVNTENA Enables or disables timestamp snapshot for event messages.
If this field is 1, the timestamp snapshot is taken only for event messages (SYNC, Delay_Req, Pdelay_Req,
or Pdelay_Resp). When the field becomes 0, the snapshot is taken for all the messages except Announce,
Management, and Signaling. For more information on timestamp snapshot dependency on register bits, see
Receive path functions .
0b - Disabled
1b - Enabled
13 Enable Processing Of PTP Packets Sent Over IPv4-UDP
TSIPV4ENA Indicates whether the processing of PTP packets sent over IPv4-UDP is enabled.
If this field is 1, the MAC receiver processes the PTP packets encapsulated in the IPv4-UDP packets. When
this field becomes 0, MAC ignores the PTP transported over the IPv4-UDP packets. The default value of this
field is 1.
0b - Disabled
1b - Enabled
12 Enable Processing Of PTP Packets Sent Over IPv6-UDP
TSIPV6ENA Indicates whether the processing of PTP packets sent over IPv6-UDP is enabled.
If this field is 1, the MAC receiver processes the PTP packets encapsulated in the IPv6-UDP packets. If the
value is 0, MAC ignores the PTP transported over IPv6-UDP packets.
0b - Disabled
1b - Enabled
11 Enable Processing Of PTP Over Ethernet Packets
TSIPENA Indicates the status of PTP processing over Ethernet packets.
If this field is 1, the MAC receiver processes the PTP packets encapsulated directly in the Ethernet packets.
When the field becomes 0, MAC ignores the PTP over Ethernet packets.
0b - Disabled
1b - Enabled
10 Enable PTP Packet Processing For Version 2 Format
TSVER2ENA Indicates the status of PTP packet processing for version 2 format.
If this field is 1, the IEEE 1588 version 2 format is used to process the PTP packets. When this field becomes
0, the IEEE 1588 version 1 format is used to process the PTP packets. See PTP processing and control for
more information on the IEEE 1588 formats.
Table continues on the next page...

---

*Page 2895*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled
9 Timestamp Digital Or Binary Rollover Control
TSCTRLSSR Indicates the status of timestamp digital or binary rollover control.
• If this field is 1, the Timestamp Low register rolls over after the 3B9A_C9FFh value (that is, 1
nanosecond accuracy) and increments the timestamp (high) seconds.
• If this field is 0, the rollover value of the Subsecond register is 7FFF_FFFFh. The sub-second
increment must be programmed correctly depending on the PTP reference clock frequency and the
value of this field.
0b - Disabled
1b - Enabled
8 Enable Timestamp For All Packets
TSENALL Indicates the status of timestamp snapshot for all packets.
If this field is 1, the timestamp snapshot is enabled for all packets that MAC receives.
0b - Disabled
1b - Enabled
7 Reserved
—
6 Presentation Time Generation Enable
PTGE Indicates the status of presentation time generation.
If this field is 1, the presentation time generation is enabled.
0b - Disabled
1b - Enabled
5 Update Addend Register
TSADDREG Indicates whether MAC Timestamp Addend (MAC_Timestamp_Addend) is updated.
If this field is 1, the contents of MAC Timestamp Addend (MAC_Timestamp_Addend) are updated in the PTP
block for fine correction. The field becomes 0 after the update completes. The value of the field must also
be 0 before you write 1 to it.
Access restrictions apply to this field that clears automatically. Writing 0 has no effect.
0b - Not updated
1b - Updated
4 Reserved
—
Table continues on the next page...

---

*Page 2896*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
3 Update Timestamp
TSUPDT Indicates whether the timestamp is updated.
If this field is 1, the system time is updated (added or subtracted) with the value specified in MAC System
Time Seconds Update (MAC_System_Time_Seconds_Update) and MAC System Time Nanoseconds
Update (MAC_System_Time_Nanoseconds_Update) .
The value of this field must be 0 before you update it. It resets after the update is complete in hardware.
MAC_System_Time_Higher_Word_Seconds[TSHWR] , if enabled during core configuration, is not updated.
When media clock generation and recovery is configured (DWC_EQOS_FLEXI_PPS_OUT_EN) and
enabled, MAC Presentation Time Update (MAC_Presn_Time_Updt) must also be updated before you write
1 to this field.
Access restrictions apply to this field that clears automatically. Writing 0 has no effect.
0b - Not updated
1b - Updated
2 Initialize Timestamp
TSINIT Indicates whether the timestamp is initialized.
If this field is 1, the system time is initialized (overwritten) with the value specified in MAC System
Time Seconds Update (MAC_System_Time_Seconds_Update) and MAC System Time Nanoseconds
Update (MAC_System_Time_Nanoseconds_Update) .
The value of this field must be 0 before you update it, and the field is reset after the initialization is
complete. MAC_System_Time_Higher_Word_Seconds[TSHWR] , if enabled during core configuration, can
only be initialized.
When media clock generation and recovery is configured (DWC_EQOS_FLEXI_PPS_OUT_EN) and
enabled, MAC Presentation Time Update (MAC_Presn_Time_Updt) must also be updated before you write
1 to this field.
Access restrictions apply to this field that clears automatically. Writing 0 has no effect.
0b - Not initialized
1b - Initialized
1 Fine Or Coarse Timestamp Update
TSCFUPDT Indicates the method used to update system timestamp.
• If this field is 1, the fine method is used to update system timestamp.
• If this field is 0, the coarse method is used to update the system timestamp.
0b - Coarse method
1b - Fine method
0 Timestamp Enable
TSENA Enables or disables timestamp for transmit and receive packets.
Table continues on the next page...

---

*Page 2897*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• If this field is 1, the timestamp is added for transmit and receive packets.
• If this field is 0, timestamp is not added for transmit and receive packets and the timestamp
generator is also suspended. You need to initialize the timestamp (system time) after enabling this
mode.
On the receive side, MAC processes the 1588 packets only if this field is 1.
0b - Disabled
1b - Enabled

#### 72.17.149 MAC Sub Second Increment (MAC_Sub_Second_Increment)

Offset
Register Offset
MAC_Sub_Second_Incre B04h
ment
Function
Specifies the value to be added to the internal system time register at every cycle of the CLK_PTP_REF_I clock.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved SSINC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SNSINC Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 Reserved
—
23-16 Sub-Second Increment Value
SSINC
Table continues on the next page...

---

*Page 2898*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
Contains the sub-second increment value, which accumulates every clock cycle (of clk_ptp_i) with the
contents of the Subsecond register. For example, if the PTP clock is 50 MHz (period is 20 ns), you must
program 20 (14h) when MAC System Time In Nanoseconds (MAC_System_Time_Nanoseconds) has an
accuracy of 1 ns ( MAC_Timestamp_Control[TSCTRLSSR] = 1). When TSCTRLSSR = 0, the Nanoseconds
register has a resolution of ~0.465 ns. In this case, you must program a value of 43 (2Bh), which is derived
by 20 ns/0.465.
15-8 Sub-Nanosecond Increment Value
SNSINC Contains the sub-nanosecond increment value, represented in nanoseconds multiplied by 2^8. This
value is accumulated in the sub-nanoseconds field of the Subsecond register. For example, if
MAC_Timestamp_Control[TSCTRLSSR] = 1, and if the required increment is 5.3 ns, then this field must be
4Ch and the SSINC field must be 05h.
7-0 Reserved
—

#### 72.17.150 MAC System Time In Seconds (MAC_System_Time_Seconds)

Offset
Register Offset
MAC_System_Time_Sec B08h
onds
Function
Indicates, along with MAC System Time In Nanoseconds (MAC_System_Time_Nanoseconds) , the current value of the system
time that MAC maintains. Although it is updated on a continuous basis, there is some delay from the actual time because of
clock domain transfer latencies (from CLK_PTP_REF_I to the CSR clock).
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TSS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TSS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2899*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Timestamp Second
TSS Indicates the current value, in seconds, of the system time that MAC maintains.

#### 72.17.151 MAC System Time In Nanoseconds (MAC_System_Time_Nanoseconds)

Offset
Register Offset
MAC_System_Time_Nan B0Ch
oseconds
Function
Indicates, along with MAC System Time In Seconds (MAC_System_Time_Seconds) , the current value of the system time that
MAC maintains.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TSSS
Reserv
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TSSS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Reserved
—
30-0 Timestamp Sub Seconds
TSSS Indicates the sub-second representation of time, with an accuracy of 0.46 ns. When
MAC_Timestamp_Control[TSCTRLSSR] = 1, each bit represents 1 ns. The maximum value is
3B9A_C9FFh after which it rolls over to 0.

---

*Page 2900*

Ethernet Media Access Controller (EMAC)

#### 72.17.152 MAC System Time Seconds Update (MAC_System_Time_Seconds_Update)

Offset
Register Offset
MAC_System_Time_Sec B10h
onds_Update
Function
Initializes or updates, along with MAC System Time Nanoseconds Update (MAC_System_Time_Nanoseconds_Update) , the
system time that MAC maintains. You must write both to this register and the MAC System Time Nanoseconds Update
register before writing 1 to MAC_Timestamp_Control[TSINIT] and MAC_Timestamp_Control[TSUPDT] .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TSS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TSS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Timestamp Seconds
TSS Indicates the timestamp seconds value.
The value in this field is the seconds part of the update.
• If MAC_System_Time_Nanoseconds_Update[ADDSUB] = 0, this field must be programmed with the
seconds part of the update value.
• If MAC_System_Time_Nanoseconds_Update[ADDSUB] = 1, this field must be programmed with the
complement of the seconds part of the update value.
For example, if 2.000000001 seconds need to be subtracted from the system time,
MAC_System_Time_Seconds[TSS] must be FFFF_FFFEh (that is, 2^32 - 2).

---

*Page 2901*

Ethernet Media Access Controller (EMAC)

#### 72.17.153 MAC System Time Nanoseconds Update (MAC_System_Time_Nanoseconds_Update)

Offset
Register Offset
MAC_System_Time_Nan B14h
oseconds_Update
Function
Indicates system time nanoseconds update.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
ADDS
TSSS
UB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TSSS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Add Or Subtract Time
ADDSUB Indicates whether the time value is added or subtracted from the contents of the update register.
• If this field is 1, the time value is subtracted from the contents of the update register.
• If this field is 0, the time value is added to the contents of the update register.
0b - Add time
1b - Subtract time
30-0 Timestamp Subseconds
TSSS Indicates the sub-second part of the update.
• If ADDSUB = 0, this field must be programmed with the sub-second part of the update value, with an
accuracy based on MAC_Timestamp_Control[TSCTRLSSR] .
• If ADDSUB = 0, this field must be programmed with the complement of the sub-second part of the
update value as described below.
• If MAC_Timestamp_Control[TSCTRLSSR] = 1, the programmed value must be 10^9 - <sub-second
value>, and if MAC_Timestamp_Control[TSCTRLSSR] = 0, the programmed value must be 2^31 -
<sub-second_value>.
Table continues on the next page...

---

*Page 2902*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• If MAC_Timestamp_Control[TSCTRLSSR] = 0, each bit of it represents an accuracy of 0.46 ns, and
if MAC_Timestamp_Control[TSCTRLSSR] = 1, each bit represents 1 ns and the programmed value
must not exceed 3B9A_C9FFh.
For example, if 2.000000001 seconds need to be subtracted from the system time, this field must
be 7FFF_FFFFh (that is, 2^31 - 1) if MAC_Timestamp_Control[TSCTRLSSR] = 0. This field must be
3B9A_C9FFh (that is, 10^9 - 1) if MAC_Timestamp_Control[TSCTRLSSR] = 1.

#### 72.17.154 MAC Timestamp Addend (MAC_Timestamp_Addend)

Offset
Register Offset
MAC_Timestamp_Adden B18h
d
Function
Is used only when the system time is configured for Fine Update mode ( MAC_Timestamp_Control[TSINIT] = 1). The contents
of this register are added to a 32-bit accumulator in every clock cycle (of CLK_PTP_REF_I), and the system time is updated
whenever the accumulator overflows.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TSAR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TSAR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Timestamp Addend Register
TSAR Indicates the 32-bit time value to be added to the Accumulator register to achieve time synchronization.

---

*Page 2903*

Ethernet Media Access Controller (EMAC)

#### 72.17.155 MAC System Time Higher Word In Seconds (MAC_System_Time_Higher_Word_Seconds)

Offset
Register Offset
MAC_System_Time_Hig B1Ch
her_Word_Seconds
Function
Indicates system time - higher word in seconds.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TSHWR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-16 Reserved
—
15-0 Timestamp Higher Word Register
TSHWR Contains the most-significant 16 bits of the timestamp seconds value. This register is optional, and you
can add it if you select the IEEE 1588 higher-word register option. You write to this register directly to
initialize the value, which increments when there is an overflow from the 32 bits of MAC System Time In
Seconds (MAC_System_Time_Seconds) .
Access restrictions apply to this field, which updates based on an event occurrence.

#### 72.17.156 MAC Timestamp Status (MAC_Timestamp_Status)

Offset
Register Offset
MAC_Timestamp_Status B20h

---

*Page 2904*

Ethernet Media Access Controller (EMAC)
Function
Indicates timestamp status. All the bits except [27:25] are cleared when the application reads this register.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv
Reserved Reserved Reserved Reserved
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
TXTSS TSTR TSTA TSTR TSTA TSTR TSTA TSTR TSTA TSSO
R
Reserv
IS GTE ... RGT3 GTE ... RGT2 GTE ... RGT1 GTE ... RGT0 VF
Reserved
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-30 Reserved
—
29-25 Reserved
—
24 Reserved
—
23-20 Reserved
—
19-16 Reserved
—
15 Transmit Timestamp Status Interrupt Status
TXTSSIS Indicates whether the transmit timestamp interrupt status is detected.
In non-EQOS_CORE configurations, if the drop transmit status is enabled in MTL, this field
becomes 1 when the captured transmit timestamp is updated in MAC Transmit Timestamp
Status In Seconds (MAC_Tx_Timestamp_Status_Seconds) and MAC Transmit Timestamp Status In
Nanoseconds (MAC_Tx_Timestamp_Status_Nanoseconds) .
If MAC_HW_Feature1[PTOEN] = 1, TXTSSIS becomes 1 when the captured transmit timestamp is
updated in MAC Transmit Timestamp Status In Seconds (MAC_Tx_Timestamp_Status_Seconds) and
MAC Transmit Timestamp Status In Nanoseconds (MAC_Tx_Timestamp_Status_Nanoseconds) for PTO-
generated delay request and Pdelay request packets.
Table continues on the next page...

---

*Page 2905*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
This field becomes 0 if you read or write to MAC Transmit Timestamp Status In Seconds
(MAC_Tx_Timestamp_Status_Seconds) when MAC_CSR_SW_Ctrl[RCWE] = 1.
0b - Not detected
1b - Detected
14-10 Reserved
—
9 Timestamp Target Time Error
TSTRGTERR3 Indicates whether the timestamp target time error status is detected.
This field becomes 1 if the latest target time programmed in MAC PPS3 Target Time
In Seconds (MAC_PPS3_Target_Time_Seconds) and MAC PPS3 Target Time In Nanoseconds
(MAC_PPS3_Target_Time_Nanoseconds) elapses. The field becomes 0 when the application reads it.
Access restrictions apply to this field that clears when read (or when this field is written to 1 if
MAC_CSR_SW_Ctrl[RCWE] = 1). Also, the field automatically becomes 1 on an internal event occurrence.
0b - Not detected
1b - Detected
8 Timestamp Target Time Reached For Target Time PPS3
TSTARGT3 Indicates whether the timestamp target time reached for target time PPS3 status is detected.
If this field is 1, it indicates that the value of the system time is greater than or equal to the value specified
in MAC PPS3 Target Time In Seconds (MAC_PPS3_Target_Time_Seconds) and MAC PPS3 Target Time
In Nanoseconds (MAC_PPS3_Target_Time_Nanoseconds) .
Access restrictions apply to this field that clears when read (or when this field is written to 1 if
MAC_CSR_SW_Ctrl[RCWE] = 1). Also, the field automatically becomes 1 on an internal event occurrence.
0b - Not detected
1b - Detected
7 Timestamp Target Time Error
TSTRGTERR2 Indicates whether the timestamp target time error status is detected.
This field becomes 1 when the latest target time programmed in MAC PPS2 Target Time
In Seconds (MAC_PPS2_Target_Time_Seconds) and MAC PPS2 Target Time In Nanoseconds
(MAC_PPS2_Target_Time_Nanoseconds) elapses. The field becomes 0 when the application reads it.
Access restrictions apply to this field that clears when read (or when this field is written to 1 if
MAC_CSR_SW_Ctrl[RCWE] = 1). Also, the field automatically becomes 1 on an internal event occurrence.
0b - Not detected
1b - Detected
6 Timestamp Target Time Reached For Target Time PPS2
TSTARGT2 Indicates whether the timestamp target time reached for the target time PPS2 status is detected.
Table continues on the next page...

---

*Page 2906*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
If this field is 1, it indicates that the value of system time is greater than or equal to the value specified in
MAC PPS2 Target Time In Seconds (MAC_PPS2_Target_Time_Seconds) and MAC PPS2 Target Time In
Nanoseconds (MAC_PPS2_Target_Time_Nanoseconds) .
Access restrictions apply to this field that clears when read (or when this field is written to 1 if
MAC_CSR_SW_Ctrl[RCWE] = 1). Also, the field automatically becomes 1 on an internal event occurrence.
0b - Not detected
1b - Detected
5 Timestamp Target Time Error
TSTRGTERR1 Indicates whether the timestamp target time error status is detected.
This field becomes 1 when the latest target time programmed in MAC PPS1 Target Time
In Seconds (MAC_PPS1_Target_Time_Seconds) and MAC PPS1 Target Time In Nanoseconds
(MAC_PPS1_Target_Time_Nanoseconds) elapses. The field becomes 0 when the application reads it.
Access restrictions apply to this field that clears when read (or when this field is written to 1 if
MAC_CSR_SW_Ctrl[RCWE] = 1). Also, the field automatically becomes 1 on an internal event occurrence.
0b - Not detected
1b - Detected
4 Timestamp Target Time Reached For Target Time PPS1
TSTARGT1 Indicates whether the timestamp target time reached for target time PPS1 status is detected.
If this field is 1, it indicates that the value of system time is greater than or equal to the value specified in
MAC PPS1 Target Time In Seconds (MAC_PPS1_Target_Time_Seconds) and MAC PPS1 Target Time In
Nanoseconds (MAC_PPS1_Target_Time_Nanoseconds)
Access restrictions apply to this field that clears when read (or when this field is written to 1 if
MAC_CSR_SW_Ctrl[RCWE] = 1). Also, the field automatically becomes 1 on an internal event occurrence.
0b - Not detected
1b - Detected
3 Timestamp Target Time Error
TSTRGTERR0 Indicates whether the timestamp target time error status is detected.
This field becomes 1 when the latest target time programmed in MAC PPS0 Target Time
In Seconds (MAC_PPS0_Target_Time_Seconds) and MAC PPS0 Target Time In Nanoseconds
(MAC_PPS0_Target_Time_Nanoseconds) elapses. The field becomes 0 when the application reads it.
Access restrictions apply to this field that clears when read (or when this field is written to 1 if
MAC_CSR_SW_Ctrl[RCWE] = 1). Also, the field automatically becomes 1 on an internal event occurrence.
0b - Not detected
1b - Detected
2 Reserved
—
Table continues on the next page...

---

*Page 2907*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
1 Timestamp Target Time Reached
TSTARGT0 Indicates whether the timestamp target time reached status is detected.
If this field is 1, it indicates that the value of the system time is greater than or equal to the value specified
in MAC PPS0 Target Time In Seconds (MAC_PPS0_Target_Time_Seconds) and MAC PPS0 Target Time
In Nanoseconds (MAC_PPS0_Target_Time_Nanoseconds)
Access restrictions apply to this field that clears when read (or when this field is written to 1 if
MAC_CSR_SW_Ctrl[RCWE] = 1). Also, the field automatically becomes 1 on an internal event occurrence.
0b - Not detected
1b - Detected
0 Timestamp Seconds Overflow
TSSOVF Indicates whether the timestamp seconds overflow status is detected.
If this field is 1, it indicates that the seconds value of the timestamp (when supporting version 2 format) has
overflowed beyond 32'hFFFF_FFFF.
Access restrictions apply to this field that clears when read (or when this field is written to 1 if
MAC_CSR_SW_Ctrl[RCWE] = 1). Also, the field automatically becomes 1 on an internal event occurrence.
0b - Not detected
1b - Detected

#### 72.17.157 MAC Transmit Timestamp Status In Nanoseconds

#### (MAC_Tx_Timestamp_Status_Nanoseconds)

Offset
Register Offset
MAC_Tx_Timestamp_St B30h
atus_Nanoseconds
Function
Contains the nanosecond part of the timestamp captured for tansmit packets when the transmit status is disabled.
This register, along with MAC Transmit Timestamp Status In Seconds (MAC_Tx_Timestamp_Status_Seconds) , gives the 64-bit
timestamp captured for the PTP packet that MAC successfully transmits. The application considers reading this value after the
last byte of this register is read. In Little-Endian mode, this means when bits [31:24] are read, and in Big-Endian mode, this means
when bits [7:0] are read.
If the application does not read these registers and the timestamp of another packet is captured, then either the
current timestamp is lost (overwritten) or the new timestamp is lost (dropped), depending on the settings specified in
MAC_Timestamp_Control[TXTSSTSM] . The status bit TXTSC bit [15] in the MAC_Timestamp_Status register is set whenever the
MAC transmitter captures the timestamp.

---

*Page 2908*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
TXTSS
R TXTSSLO
MIS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXTSSLO
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Transmit Timestamp Status Missed
TXTSSMIS Indicates whether the transmit timestamp missed status is detected.
If this field is 1, it indicates one of these conditions:
• The timestamp of the current packet is ignored if MAC_Timestamp_Control[TXTSSTSM] = 0.
• The timestamp of the previous packet is overwritten with the timestamp of the current packet if
MAC_Timestamp_Control[TXTSSTSM] = 1.
Access restrictions apply to this field that cleas on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
30-0 Transmit Timestamp Status Low
TXTSSLO Contains 31 bits of the Nanoseconds field of the transmit packet's captured timestamp.

#### 72.17.158 MAC Transmit Timestamp Status In Seconds (MAC_Tx_Timestamp_Status_Seconds)

Offset
Register Offset
MAC_Tx_Timestamp_St B34h
atus_Seconds
Function
Contains the higher 32 bits of the timestamp (in seconds) captured when a PTP packet is transmitted.

---

*Page 2909*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXTSSHI
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXTSSHI
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit Timestamp Status High
TXTSSHI Contains the lower 32 bits of the Seconds field of the transmitted packet's captured timestamp.

#### 72.17.159 MAC Timestamp Ingress Asymmetry Correction (MAC_Timestamp_Ingress_Asym_Corr)

Offset
Register Offset
MAC_Timestamp_Ingres B50h
s_Asym_Corr
Function
Contains the ingress asymmetry correction value to be used when updating the correction field in the PDelay_Resp PTP
messages.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
OSTIAC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
OSTIAC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2910*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 One-Step Timestamp Ingress Asymmetry Correction
OSTIAC Contains the ingress path asymmetry value to be added to the correctionField of the Pdelay_Resp
PTP packet.
The programmed value must be expressed in units of nanoseconds and multiplied by 2^16. For example,
2.5 ns is represented as 00028000h. This value can also be negative, which is represented in the
two's-complement form with bit 31 representing the sign bit.

#### 72.17.160 MAC Timestamp Egress Asymmetry Correction (MAC_Timestamp_Egress_Asym_Corr)

Offset
Register Offset
MAC_Timestamp_Egres B54h
s_Asym_Corr
Function
Contains the egress asymmetry correction value to be used when updating the correction field in the PDelay_Req PTP
messages.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
OSTEAC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
OSTEAC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 One-Step Timestamp Egress Asymmetry Correction
OSTEAC Contains the egress path asymmetry value to be subtracted from the correctionField of the Pdelay_Resp
PTP packet. The programmed value must be the negated value in units of nanoseconds multiplied by 2^16.
For example, if the required correction is +2.5 ns, the programmed value must be FFFD_8000h, which is the
two's-complement of 0002_8000h (2.5 * 216). Similarly, if the required correction is -3.3 ns, the programmed
value is 0003_4CCCh (3.3 * 216).

---

*Page 2911*

Ethernet Media Access Controller (EMAC)

#### 72.17.161 MAC Timestamp Ingress Correction In Nanoseconds

#### (MAC_Timestamp_Ingress_Corr_Nanosecond)

Offset
Register Offset
MAC_Timestamp_Ingres B58h
s_Corr_Nanosecond
Function
Contains the correction value, in nanoseconds, to be used with the captured timestamp value in the ingress path.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TSIC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TSIC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Timestamp Ingress Correction
TSIC Contains the ingress path correction value as defined by the ingress correction expression.

#### 72.17.162 MAC Timestamp Egress Correction In Nanoseconds

#### (MAC_Timestamp_Egress_Corr_Nanosecond)

Offset
Register Offset
MAC_Timestamp_Egres B5Ch
s_Corr_Nanosecond
Function
Contains the correction value, in nanoseconds, to be used with the captured timestamp value in the egress path.

---

*Page 2912*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TSEC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TSEC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Timestamp Egress Correction
TSEC Contains the nanoseconds part of the egress path correction value as defined by the egress
correction expression.

#### 72.17.163 MAC Timestamp Ingress Correction In Subnanoseconds

#### (MAC_Timestamp_Ingress_Corr_Subnanosec)

Offset
Register Offset
MAC_Timestamp_Ingres B60h
s_Corr_Subnanosec
Function
Contains the sub-nanosecond part of the correction value to be used with the captured timestamp value for ingress direction.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TSICSNS Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2913*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-16 Reserved
—
15-8 Timestamp Ingress Correction In Sub-Nanoseconds
TSICSNS Contains the sub-nanoseconds part of the ingress path correction value as defined by the Ingress
Correction expression.
7-0 Reserved
—

#### 72.17.164 MAC Timestamp Engress Correction In Subnanoseconds

#### (MAC_Timestamp_Egress_Corr_Subnanosec)

Offset
Register Offset
MAC_Timestamp_Egres B64h
s_Corr_Subnanosec
Function
Contains the sub-nanosecond part of the correction value to be used with the captured timestamp value for the egress
direction.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TSECSNS Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-16 Reserved
Table continues on the next page...

---

*Page 2914*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
—
15-8 Timestamp Egress Correction In Sub-Nanoseconds
TSECSNS Contains the sub-nanoseconds part of the egress path correction value, as defined by the Egress
Correction expression.
7-0 Reserved
—

#### 72.17.165 MAC Timestamp Ingress Latency (MAC_Timestamp_Ingress_Latency)

Offset
Register Offset
MAC_Timestamp_Ingres B68h
s_Latency
Function
Holds the ingress MAC latency.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R ITLNS
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R ITLSNS
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-28 Reserved
—
27-16 Ingress Timestamp Latency In Sub-Nanoseconds
Table continues on the next page...

---

*Page 2915*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
ITLNS Holds the average latency, in sub-nanoseconds, between the MAC input ports (PHY_RXD_I) and the
actual point (GMII/MII), where the ingress timestamp is taken. The ingress correction value is computed as
described in section 7.1.2.4.1 of QoS Databook.
15-8 Ingress Timestamp Latency In Nanoseconds
ITLSNS Holds the average latency, in nanoseconds, between the MAC input ports (PHY_RXD_I) and the actual
point (GMII/MII), where the ingress timestamp is taken. The ingress correction value is computed as
described in section 7.1.2.4.1 of QoS Databook.
7-0 Reserved
—

#### 72.17.166 MAC Timestamp Egress Latency (MAC_Timestamp_Egress_Latency)

Offset
Register Offset
MAC_Timestamp_Egres B6Ch
s_Latency
Function
Holds the egress MAC latency.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R ETLNS
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R ETLSNS
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-28 Reserved
—
Table continues on the next page...

---

*Page 2916*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
27-16 Egress Timestamp Latency In Nanoseconds
ETLNS Holds the average latency, in nanoseconds, between the actual point (GMII/MII) where the egress
timestamp is taken and the MAC output ports (PHY_TXD_O). The ingress correction value is computed as
described in section 7.1.2.4.2 of QoS Databook.
15-8 Egress Timestamp Latency In Sub-Nanoseconds
ETLSNS Holds the average latency, in sub-nanoseconds, between the actual point (GMII/MII) where the egress
timestamp is taken and the MAC output ports (PHY_TXD_O). The ingress correction value is computed as
described in section 7.1.2.4.2 of QoS Databook.
7-0 Reserved
—

#### 72.17.167 MAC PPS Control (MAC_PPS_Control)

Offset
Register Offset
MAC_PPS_Control B70h
Function
Indicates PPS control.
In this register:
• Bits [30:24] are valid only when four flexible PPS outputs are selected.
• Bits [22:16] are valid only when three or more flexible PPS outputs are selected.
• Bits [14:8] are valid only when two or more flexible PPS outputs are selected.
• Bits [6:4] are valid only when the flexible PPS feature is selected.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
MCGR TRGTMODSEL Reserv MCGR TRGTMODSEL Reserv
PPSCMD3 PPSCMD2
EN3 3 ed EN2 2 ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
MCGR TRGTMODSEL Reserv MCGR TRGTMODSEL PPSE
PPSCMD1 PPSCTRL_PPSCMD
EN1 1 ed EN0 0 N0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2917*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31 MCGR Mode Enable For PPS3 Output
MCGREN3 Enables the third PPS instance to operate in PPS or MCGR mode. If this field is 1, it operates in MCGR
mode, and if the value is 0, it operates in PPS mode.
30-29 Target Time Register Mode For PPS3 Output
TRGTMODSEL Indicates MAC PPS3 Target Time In Seconds (MAC_PPS3_Target_Time_Seconds) and MAC PPS3 Target
3 Time In Nanoseconds (MAC_PPS3_Target_Time_Nanoseconds) mode for the PPS3 output signal.
00b - Target Time registers are programmed only for generating the interrupt event. The flexible
PPS function must not be enabled in this mode, otherwise spurious transitions may be observed
on the corresponding PTP_PPS_O output port.
01b - Reserved
10b - Target Time registers are programmed for generating the interrupt event and starting or
stopping the PPS0 output signal generation.
11b - Target Time registers are programmed only for starting or stopping the PPS0 output signal
generation. No interrupt is asserted.
28 Reserved
—
27-24 Flexible PPS3 Output Control
PPSCMD3 Controls the flexible PPS3 output (PTP_PPS_O[3]) signal. The functioning of this field is similar to that of
PPSCMD0[2:0] (presentation time control bits).
If MCGREN3 = 1, PPSCMD3 that includes bits [27:24] are considered as presentation time control bits for
media clock generation and recovery for comparator instance 3. If MCGREN3 is not 1, only three bits [26:24]
are considered as PPSCMD3 and the fourth bit becomes 0.
Access restrictions apply to this field that clears automatically. Writing 0 to it has no effect.
23 MCGR Mode Enable For PPS2 Output
MCGREN2 Enables or disables the second PPS instance to operate in PPS or MCGR mode. If this field is 1, it operates
in MCGR mode, and if the value is 0, it operates in PPS mode.
0b - Disabled
1b - Enabled
22-21 Target Time Register Mode For PPS2 Output
TRGTMODSEL Indicates MAC PPS2 Target Time In Seconds (MAC_PPS2_Target_Time_Seconds) and MAC PPS2 Target
2 Time In Nanoseconds (MAC_PPS2_Target_Time_Nanoseconds) mode for the PPS2 output signal.
00b - Target Time registers are programmed only for generating the interrupt event. The flexible
PPS function must not be enabled in this mode, otherwise spurious transitions may be observed
on the corresponding PTP_PPS_O output port.
01b - Reserved
Table continues on the next page...

---

*Page 2918*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
10b - Target Time registers are programmed for generating the interrupt event and starting or
stopping the PPS0 output signal generation.
11b - Target Time registers are programmed only for starting or stopping the PPS0 output signal
generation. No interrupt is asserted.
20 Reserved
—
19-16 Flexible PPS2 Output Control
PPSCMD2 Controls the flexible PPS2 output (PTP_PPS_O[2]) signal. The functioning of this field is similar to that of
the PPSCMD0 (presentation time control bits).
If MCGREN2 = 1, the bits included in PPSCMD2 [19:16] are considered as presentation time control bits for
media clock generation and recovery for comparator instance 2. If MCGREN2 is not 1, only three bits [18:16]
are used as PPSCMD2 and the fourth bit becomes 0.
Access restrictions apply to this field that clears automatically. Writing 0 to it has no effect.
15 MCGR Mode Enable For PPS1 Output
MCGREN1 Enables or disables the first PPS instance to operate in PPS or MCGR mode. If this field is 1, it operates in
MCGR mode, and if the value is 0, it operates in PPS mode.
0b - Disabled
1b - Enabled
14-13 Target Time Register Mode For PPS1 Output
TRGTMODSEL Indicates MAC PPS1 Target Time In Seconds (MAC_PPS1_Target_Time_Seconds) and MAC PPS1 Target
1 Time In Nanoseconds (MAC_PPS1_Target_Time_Nanoseconds) mode for the PPS1 output signal.
00b - Target Time registers are programmed only for generating the interrupt event. The flexible
PPS function must not be 1 in this mode, otherwise spurious transitions may be observed on the
corresponding PTP_PPS_O output port.
01b - Reserved
10b - Target Time registers are programmed for generating the interrupt event and starting or
stopping the PPS0 output signal generation.
11b - Target Time registers are programmed only for starting or stopping the PPS0 output signal
generation. No interrupt is asserted.
12 Reserved
—
11-8 Flexible PPS1 Output Control
PPSCMD1 Controls the flexible PPS1 output (PTP_PPS_O[1]) signal. The functioning of this field is similar to that of
the PPSCMD0 field.
Table continues on the next page...

---

*Page 2919*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
If MCGREN1 = 1, the bits included in PPSCMD1 [11:8] are considered as presentation time control bits for
media clock generation and recovery for comparator instance 1. The functioning of PPSCMD1 is similar to
that of the PPSCMD0 presentation time control bits. If MCGREN1 is not 1, only three bits [10:8] are used
as PPSCMD1 and the fourth bit becomes 0.
Access restrictions apply to this field that clears automatically. Writing 0 to it has no effect.
7 MCGR Mode Enable For PPS0 Output
MCGREN0 Indicates whether the 0th PPS instance is enabled to operate in PPS or MCGR mode. If this field is 1, it
operates in MCGR mode, and if the value is 0, it operates in PPS mode.
0b - PPS mode
1b - MCGR mode
6-5 Target Time Register Mode For PPS0 Output
TRGTMODSEL Indicates MAC PPS0 Target Time In Seconds (MAC_PPS0_Target_Time_Seconds) and MAC PPS0 Target
0 Time In Nanoseconds (MAC_PPS0_Target_Time_Nanoseconds) mode for the PPS0 output signal.
00b - Target Time registers are programmed only for generating the interrupt event. The flexible
PPS function must not be 1 in this mode, otherwise spurious transitions may be observed on the
corresponding PTP_PPS_O output port.
01b - Reserved
10b - Target Time registers are programmed for generating the interrupt event and starting or
stopping the PPS0 output signal generation.
11b - Target Time registers are programmed only for starting or stopping the PPS0 output signal
generation. No interrupt is asserted.
4 Flexible PPS Output Mode Enable 0
PPSEN0 Indicates the status of Flexible PPS Output mode.
• If this field is 1, the PPSCTRL_PPSCMD field functions as PPSCMD.
• If this field is 0, the PPSCTRL_PPSCMD field functions as PPSCTRL (fixed PPS mode).
0b - Disabled
1b - Enabled
3-0 PPS Output Frequency Control
PPSCTRL_PPS Controls the frequency of the PPS0 output (PTP_PPS_O[0] signal). The default value of this field is 0, and
CMD the PPS output is 1 pulse (of width clk_ptp_i) every second. For the other values of PPSCTRL, the PPS
output becomes a generated clock having these frequencies:
• 0001: The binary rollover is 2 Hz, and the digital rollover is 1 Hz.
• 0010: The binary rollover is 4 Hz, and the digital rollover is 2 Hz.
• 0011: The binary rollover is 8 Hz, and the digital rollover is 4 Hz.
• 0100: The binary rollover is 16 Hz, and the digital rollover is 8 Hz.
Table continues on the next page...

---

*Page 2920*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• ..
• 1111: The binary rollover is 32.768 kHz and the digital rollover is 16.384 kHz.
NOTE
In Binary Rollover mode, the PPS output (PTP_PPS_O) has a duty cycle of 50 percent
with these frequencies. In Digital Rollover mode, the PPS output frequency is an average
number. The actual clock has a different frequency that is synchronized every second.
For example,
• If PPSCTRL = 0001, PPS (1 Hz) has a low period of 537 ms and a high period of 463 ms.
• If PPSCTRL = 0010, PPS (2 Hz) is a sequence of one clock of 50 percent duty cycle and a 537 ms
period. The second clock has a period of 463 ms (268 ms low and 195 ms high).
• If PPSCTRL = 0011, PPS (4 Hz) is a sequence of three clocks of 50 percent duty cycle and a 268
ms period, and the fourth clock of 195 ms period (134 ms low and 61 ms high).
This behavior is a result of the non-linear toggling of bits in Digital Rollover mode in MAC System Time In
Nanoseconds (MAC_System_Time_Nanoseconds) or flexible PPS output (PTP_PPS_O[0]) control.
Programming these bits with a non-zero value instructs MAC to initiate an event. When the command
is transferred or synchronized with the PTP clock domain, these bits automatically become 0. You must
ensure that these bits are programmed only when they are "all-zero." The following list describes the values
of PPSCMD0.
• 0000: No command
• 0001: Start a single pulse. This command generates a single pulse rising at the start point defined
in MAC PPS0 Target Time In Seconds (MAC_PPS0_Target_Time_Seconds) and MAC PPS0 Target
Time In Nanoseconds (MAC_PPS0_Target_Time_Nanoseconds) , for a duration defined in MAC
PPS0 Width (MAC_PPS0_Width) .
• 0010: Start pulse train. This command generates the train of pulses rising at the start point defined
in the Target Time registers and for a duration defined in MAC PPS0 Width (MAC_PPS0_Width) ,
repeated at an interval defined in the PPS Interval registers. By default, the PPS pulse train runs
freely unless the "stop pulse train at time" or "stop pulse train immediately" command stops it.
• 0011: Cancel start. This command cancels the start single pulse and start pulse train commands if
the system time has not crossed the programmed start time.
• 0100: Stop pulse train at time. This command stops the train of pulses that the start pulse train
command (PPSCMD = 0010) initiates after the time programmed in the Target Time registers
elapses.
• 0101: Stop pulse train immediately. This command immediately stops the train of pulses that the
start pulse train command (PPSCMD = 0010) initiates.
• 110: Cancel stop pulse train. This command cancels the stop pulse train at the time command if the
programmed stop time has not elapsed. The PPS pulse train runs freely if the command executes
successfully.
• 0111-1111: Reserved or presentation time control. If MAC_PPS_Control[MCGREN0] = 0, then these
are treated as presentation time control bits.
This list describes the values of PPSCMD0:
Table continues on the next page...

---

*Page 2921*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• 0000: The MCGR operation is not carried out. If set to this value in the mid of clock recovery or
generation, all the processing inputs are flushed.
• 0001: Captures the presentation time at the rising edge of MCG_PST_TRIG_I[0] in MAC PPS0
Target Time In Seconds (MAC_PPS0_Target_Time_Seconds)
• 0010: Captures the presentation time at the falling edge of MCG_PST_TRIG_I[0] in MAC PPS0
Target Time In Seconds (MAC_PPS0_Target_Time_Seconds)
• 0011: Captures the presentation time at both the edges of MCG_PST_TRIG_I[0] in MAC PPS0
Target Time In Seconds (MAC_PPS0_Target_Time_Seconds)
• 0100-1000: Reserved
• 1001: Toggle output on compare
• 1010: Pulse output low on compare for one PTP-clock cycle
• 1011: Pulse output high on compare for one PTP-clock cycle
• 1100-1111: Reserved

#### 72.17.168 MAC PPS0 Target Time In Seconds (MAC_PPS0_Target_Time_Seconds)

Offset
Register Offset
MAC_PPS0_Target_Tim B80h
e_Seconds
Function
Is used, along with the PPS Target Time Nanoseconds register, to schedule an interrupt event
( MAC_Timestamp_Status[TSTARGT0] ) when the system time exceeds the value programmed in these registers.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TSTRH0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TSTRH0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2922*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 PPS Target Time In Seconds Register
TSTRH0 Stores the target time in seconds. When the timestamp value matches or exceeds both the Target
Timestamp registers, MAC starts or stops the PPS signal output and generates an interrupt (if
enabled), based on Target Time mode selected for the corresponding PPS output in MAC PPS
Control (MAC_PPS_Control) .
If DWC_EQOS_FLEXI_PPS_OUT_EN is enabled in the configuration and
MAC_Timestamp_Control[PTGE] = 1, with the presentation time control set in Recovery mode, these
fields indicate that the application programs the TPT. In Generation mode, it indicates that CPT is generated
at the sampled trigger.

#### 72.17.169 MAC PPS0 Target Time In Nanoseconds (MAC_PPS0_Target_Time_Nanoseconds)

Offset
Register Offset
MAC_PPS0_Target_Tim B84h
e_Nanoseconds
Function
Indicates the target time in nanoseconds for PPS0.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TRGT
TTSL0
BUS ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TTSL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 PPS Target Time Busy Status 0
TRGTBUSY0 Indicates whether the PPS target time busy status 0 is detected.
Table continues on the next page...

---

*Page 2923*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• MAC writes 1 to this field when MAC_PPS_Control[PPSCTRL_PPSCMD] is programmed to 010 or
011. This instructs MAC to synchronize the Target Time registers with the PTP clock domain.
• MAC writes 0 to this field after synchronizing the Target Time registers with the PTP clock domain.
The application must not update the Target Time registers when this field is read as 1. Otherwise,
the synchronization of the previously programmed time is corrupted.
0b - Not detected
1b - Detected
30-0 Target Time Low For PPS0
TTSL0 Stores the time in (signed) nanoseconds.
If the value of the timestamp matches the value in both the Target Timestamp registers,
MAC starts or stops the PPS signal output and generates an interrupt, if enabled, based
on MAC_PPS_Control[TRGTMODSEL0] .
• If MAC_Timestamp_Control[TSCTRLSSR] = 0, this value must be calculated as (time in ns / 0.465).
The actual start or stop time of the PPS signal output might have an error margin up to one unit of
the sub-second increment value.
• If MAC_Timestamp_Control[TSCTRLSSR] = 1, this value must not exceed 3B9A_C9FFh. The actual
start or stop time of the PPS signal output might have an error margin up to one unit of the
sub-second increment value.
Access restriction apply to this field that clears automatically. Writing 0 to it has no effect.

#### 72.17.170 MAC PPS0 Interval (MAC_PPS0_Interval)

Offset
Register Offset
MAC_PPS0_Interval B88h
Function
Contains the number of units of the sub-second increment value between the rising edges of the PPS0 signal output
(PTP_PPS_O[0]).

---

*Page 2924*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
PPSINT0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PPSINT0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 PPS Output Signal Interval 0
PPSINT0 Stores the interval between the rising edges of the PPS0 signal output. The interval is stored in terms of the
number of units of the sub-second increment value.
You must program one value less than the required interval. For example, if the PTP reference clock is 50
MHz (period of 20 ns), and the desired interval between the rising edges of the PPS0 signal output is 100
ns (that is, five units of the sub-second increment value), you must program value 4 (5-1) in this register.

#### 72.17.171 MAC PPS0 Width (MAC_PPS0_Width)

Offset
Register Offset
MAC_PPS0_Width B8Ch
Function
Contains the number of units of the sub-second increment value between the rising and corresponding falling edges of the
PPS0 signal output (PTP_PPS_O[0]).
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
PPSWIDTH0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PPSWIDTH0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2925*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 PPS Output Signal Width 0
PPSWIDTH0 Stores the width between the rising and corresponding falling edges of the PPS0 signal output. The width
is stored in terms of the number of units of the sub-second increment value.
You must program one value less than the required interval. For example, if the PTP reference clock has a
frequency of 50 MHz (period of 20 ns), and a width between the rising and corresponding falling edges of
the PPS0 signal output is 80 ns (that is, four units of the sub-second increment value), you should program
value 3 (4-1) in this register.
NOTE
The value programmed in this register must be lesser than the value programmed in MAC
PPS0 Interval (MAC_PPS0_Interval) .

#### 72.17.172 MAC PPS1 Target Time In Seconds (MAC_PPS1_Target_Time_Seconds)

Offset
Register Offset
MAC_PPS1_Target_Tim B90h
e_Seconds
Function
Is used to schedule an interrupt event ( MAC_Timestamp_Status[TSTARGT0] ), along with the PPS Target Time Nanoseconds
register, when the system time exceeds the value programmed in these registers.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TSTRH1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TSTRH1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 PPS Target Time In Seconds 1
Table continues on the next page...

---

*Page 2926*

Ethernet Media Access Controller (EMAC)
Field Function
TSTRH1 Stores the target time in seconds.
When the timestamp value matches or exceeds both the Target Timestamp registers, MAC starts or stops
the PPS signal output and generates an interrupt, if enabled, based on Target Time mode selected for the
corresponding PPS output in MAC PPS Control (MAC_PPS_Control) .
If DWC_EQOS_FLEXI_PPS_OUT_EN is enabled in the configuration and
MAC_Timestamp_Control[PTGE] = 1, with the presentation time control set in Recovery mode, these
fields indicate that the application programs the TPT. In Generation mode, it indicates that CPT is generated
at the sampled trigger.

#### 72.17.173 MAC PPS1 Target Time In Nanoseconds (MAC_PPS1_Target_Time_Nanoseconds)

Offset
Register Offset
MAC_PPS1_Target_Tim B94h
e_Nanoseconds
Function
Indicates the target time in nanoseconds for PPS1.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TRGT
TTSL1
BUS ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TTSL1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 PPS Target Time Busy Status 1
TRGTBUSY1 Indicates whether the PPS target time busy status 1 is detected.
• MAC writes 1 to this field when MAC_PPS_Control[PPSCTRL_PPSCMD] is programmed to 010 or
011. Programming the PPSCMD0 field as 010 or 011 instructs MAC to synchronize the Target Time
registers with the PTP clock domain.
Table continues on the next page...

---

*Page 2927*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• MAC writes 0 to this field after synchronizing the Target Time registers with the PTP clock domain.
The application must not update the Target Time registers when this field is read as 1. Otherwise,
the synchronization of the previously programmed time is corrupted.
0b - Not detected
1b - Detected
30-0 Target Time Low For PPS1
TTSL1 Stores the time in (signed) nanoseconds.
If the value of the timestamp matches the value in both the Target Timestamp registers,
MAC starts or stops the PPS signal output and generates an interrupt, if enabled, based
on MAC_PPS_Control[TRGTMODSEL0] .
• If MAC_Timestamp_Control[TSCTRLSSR] = 0, this value must be defined as (time in ns / 0.465).
The actual start or stop time of the PPS signal output might have an error margin up to one unit of
the sub-second increment value.
• If MAC_Timestamp_Control[TSCTRLSSR] = 1, this value must not exceed 3B9A_C9FFh. The actual
start or stop time of the PPS signal output might have an error margin up to one unit of the
sub-second increment value.
Access restriction apply to this field that clears automatically. Writing 0 to it has no effect.

#### 72.17.174 MAC PPS1 Interval (MAC_PPS1_Interval)

Offset
Register Offset
MAC_PPS1_Interval B98h
Function
Contains the number of units of the sub-second increment value between the rising edges of the PPS0 signal output
(PTP_PPS_O[0]).
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
PPSINT1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PPSINT1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2928*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 PPS Output Signal Interval 1
PPSINT1 Stores the interval between the rising edges of the PPS0 signal output. The interval is stored in terms of the
number of units of the sub-second increment value.
You must program one value less than the required interval. For example, if the PTP reference clock is 50
MHz (period of 20 ns), and the desired interval between the rising edges of the PPS0 signal output is 100
ns (that is, five units of the sub-second increment value), you must program value 4 (5-1) in this register.

#### 72.17.175 MAC PPS1 Width (MAC_PPS1_Width)

Offset
Register Offset
MAC_PPS1_Width B9Ch
Function
Contains the number of units of the sub-second increment value between the rising and corresponding falling edges of the
PPS0 signal output (PTP_PPS_O[0]).
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
PPSWIDTH1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PPSWIDTH1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 PPS Output Signal Width 1
PPSWIDTH1 Stores the width between the rising and corresponding falling edges of the PPS0 signal output. The width
is stored in terms of the number of units of the sub-second increment value.
You must program one value less than the required interval. For example, if the PTP reference clock has a
frequency of 50 MHz (period of 20 ns), and a width between the rising and corresponding falling edges of
the PPS0 signal output is 80 ns (that is, four units of the sub-second increment value), you should program
value 3 (4-1) in this register.
Table continues on the next page...

---

*Page 2929*

Ethernet Media Access Controller (EMAC)
Field Function
NOTE
The value programmed in this register must be lesser than the value programmed in MAC
PPS0 Interval (MAC_PPS0_Interval) .

#### 72.17.176 MAC PPS2 Target Time In Seconds (MAC_PPS2_Target_Time_Seconds)

Offset
Register Offset
MAC_PPS2_Target_Tim BA0h
e_Seconds
Function
Is used to schedule an interrupt event ( MAC_Timestamp_Status[TSTARGT0] ), along with the PPS Target Time Nanoseconds
register, when the system time exceeds the value programmed in these registers.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TSTRH2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TSTRH2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 PPS Target Time In Seconds 2
TSTRH2 Stores the target time in seconds.
When the timestamp value matches or exceeds both the Target Timestamp registers, MAC starts or stops
the PPS signal output and generates an interrupt, if enabled, based on Target Time mode selected for the
corresponding PPS output in MAC PPS Control (MAC_PPS_Control) .
If DWC_EQOS_FLEXI_PPS_OUT_EN is enabled in the configuration and
MAC_Timestamp_Control[PTGE] = 1, with the presentation time control set in Recovery mode, these
fields indicate that the application programs the TPT. In Generation mode, it indicates that CPT is generated
at the sampled trigger.

---

*Page 2930*

Ethernet Media Access Controller (EMAC)

#### 72.17.177 MAC PPS2 Target Time In Nanoseconds (MAC_PPS2_Target_Time_Nanoseconds)

Offset
Register Offset
MAC_PPS2_Target_Tim BA4h
e_Nanoseconds
Function
Indicates the target time in nanoseconds for PPS2.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TRGT
TTSL2
BUS ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TTSL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 PPS Target Time Busy Status 2
TRGTBUSY2 Indicates whether the PPS target time busy status 2 is detected.
• MAC writes 1 to this field when MAC_PPS_Control[PPSCTRL_PPSCMD] is programmed to 010 or
011. This instructs MAC to synchronize the Target Time registers with the PTP clock domain.
• MAC writes 0 to this field after synchronizing the Target Time registers with the PTP clock domain.
The application must not update the Target Time registers when this field is read as 1. Otherwise,
the synchronization of the previously programmed time is corrupted.
0b - Not detected
1b - Detected
30-0 Target Time Low For PPS2
TTSL2 Stores the time in (signed) nanoseconds.
If the value of the timestamp matches the value in both the Target Timestamp registers,
MAC starts or stops the PPS signal output and generates an interrupt, if enabled, based
on MAC_PPS_Control[TRGTMODSEL0] .
Table continues on the next page...

---

*Page 2931*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• If MAC_Timestamp_Control[TSCTRLSSR] = 0, this value must be defined as (time in ns / 0.465).
The actual start or stop time of the PPS signal output might have an error margin up to one unit of
the sub-second increment value.
• If MAC_Timestamp_Control[TSCTRLSSR] = 1, this value must not exceed 3B9A_C9FFh. The actual
start or stop time of the PPS signal output might have an error margin up to one unit of the
sub-second increment value.
Access restriction apply to this field that clears automatically. Writing 0 to it has no effect.

#### 72.17.178 MAC PPS2 Interval (MAC_PPS2_Interval)

Offset
Register Offset
MAC_PPS2_Interval BA8h
Function
Contains the number of units of the sub-second increment value between the rising edges of the PPS0 signal output
(PTP_PPS_O[0]).
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
PPSINT2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PPSINT2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 PPS Output Signal Interval 2
PPSINT2 Stores the interval between the rising edges of the PPS0 signal output. The interval is stored in terms of the
number of units of the sub-second increment value.
You must program one value less than the required interval. For example, if the PTP reference clock is 50
MHz (period of 20 ns), and the desired interval between the rising edges of the PPS0 signal output is 100
ns (that is, five units of the sub-second increment value), you must program value 4 (5-1) in this register.

---

*Page 2932*

Ethernet Media Access Controller (EMAC)

#### 72.17.179 MAC PPS2 Width (MAC_PPS2_Width)

Offset
Register Offset
MAC_PPS2_Width BACh
Function
Contains the number of units of the sub-second increment value between the rising and corresponding falling edges of the
PPS0 signal output (PTP_PPS_O[0]).
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
PPSWIDTH2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PPSWIDTH2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 PPS Output Signal Width 2
PPSWIDTH2 Stores the width between the rising and corresponding falling edges of the PPS0 signal output. The width
is stored in terms of the number of units of the sub-second increment value.
You must program one value less than the required interval. For example, if the PTP reference clock has a
frequency of 50 MHz (period of 20 ns), and a width between the rising and corresponding falling edges of
the PPS0 signal output is 80 ns (that is, four units of the sub-second increment value), you should program
value 3 (4-1) in this register.
NOTE
The value programmed in this register must be lesser than the value programmed in MAC
PPS0 Interval (MAC_PPS0_Interval) .

#### 72.17.180 MAC PPS3 Target Time In Seconds (MAC_PPS3_Target_Time_Seconds)

Offset
Register Offset
MAC_PPS3_Target_Tim BB0h
e_Seconds

---

*Page 2933*

Ethernet Media Access Controller (EMAC)
Function
Is used to schedule an interrupt event ( MAC_Timestamp_Status[TSTARGT0] ), along with the PPS Target Time Nanoseconds
register, when the system time exceeds the value programmed in these registers.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TSTRH3
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TSTRH3
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 PPS Target Time In Seconds 3
TSTRH3 Stores the target time in seconds.
When the timestamp value matches or exceeds both the Target Timestamp registers, MAC starts or stops
the PPS signal output and generates an interrupt, if enabled, based on Target Time mode selected for the
corresponding PPS output in MAC PPS Control (MAC_PPS_Control) .
If DWC_EQOS_FLEXI_PPS_OUT_EN is enabled in the configuration and
MAC_Timestamp_Control[PTGE] = 1, with the presentation time control set in Recovery mode, these
fields indicate that the application programs the TPT. In Generation mode, it indicates that CPT is generated
at the sampled trigger.

#### 72.17.181 MAC PPS3 Target Time In Nanoseconds (MAC_PPS3_Target_Time_Nanoseconds)

Offset
Register Offset
MAC_PPS3_Target_Tim BB4h
e_Nanoseconds
Function
Indicates the target time in nanoseconds for PPS3.

---

*Page 2934*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TRGT
TTSL3
BUS ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TTSL3
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 PPS Target Time Register Busy 3
TRGTBUSY3 Indicates whether the PPS target time register busy status is detected.
MAC writes 1 to this field when MAC_PPS_Control[PPSCTRL_PPSCMD] is programmed to 010 or 011.
This instructs MAC to synchronize the Target Time registers with the PTP clock domain.
MAC writes 0 to this field after synchronizing the Target Time registers with the PTP clock domain.
The application must not update the Target Time registers when this field is read as 1. Otherwise, the
synchronization of the previously programmed time is corrupted.
0b - Not detected
1b - Detected
30-0 Target Time Low For PPS3
TTSL3 Stores the time in (signed) nanoseconds.
If the value of the timestamp matches the value in both the Target Timestamp registers,
MAC starts or stops the PPS signal output and generates an interrupt, if enabled, based
on MAC_PPS_Control[TRGTMODSEL0] .
• If MAC_Timestamp_Control[TSCTRLSSR] = 0, this value must be defined as (time in ns / 0.465).
The actual start or stop time of the PPS signal output might have an error margin up to one unit of
the sub-second increment value.
• If MAC_Timestamp_Control[TSCTRLSSR] = 1, this value must not exceed 3B9A_C9FFh. The actual
start or stop time of the PPS signal output might have an error margin up to one unit of the
sub-second increment value.
Access restriction apply to this field that clears automatically. Writing 0 to it has no effect.

#### 72.17.182 MAC PPS3 Interval (MAC_PPS3_Interval)

Offset
Register Offset
MAC_PPS3_Interval BB8h

---

*Page 2935*

Ethernet Media Access Controller (EMAC)
Function
Contains the number of units of the sub-second increment value between the rising edges of the PPS0 signal output
(PTP_PPS_O[0]).
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
PPSINT3
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PPSINT3
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 PPS Output Signal Interval
PPSINT3 Stores the interval between the rising edges of the PPS0 signal output. The interval is stored in terms of the
number of units of the sub-second increment value.
You must program one value less than the required interval. For example, if the PTP reference clock is 50
MHz (period of 20 ns), and the desired interval between the rising edges of the PPS0 signal output is 100
ns (that is, five units of the sub-second increment value), you must program value 4 (5-1) in this register.

#### 72.17.183 MAC PPS3 Width (MAC_PPS3_Width)

Offset
Register Offset
MAC_PPS3_Width BBCh
Function
Contains the number of units of the sub-second increment value between the rising and corresponding falling edges of the
PPS0 signal output (PTP_PPS_O[0]).

---

*Page 2936*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
PPSWIDTH3
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PPSWIDTH3
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 PPS Output Signal Width 3
PPSWIDTH3 Stores the width between the rising and corresponding falling edges of the PPS0 signal output. The width
is stored in terms of the number of units of the sub-second increment value.
You must program one value less than the required interval. For example, if the PTP reference clock has a
frequency of 50 MHz (period of 20 ns), and a width between the rising and corresponding falling edges of
the PPS0 signal output is 80 ns (that is, four units of the sub-second increment value), you should program
value 3 (4-1) in this register.
NOTE
The value programmed in this register must be lesser than the value programmed in MAC
PPS0 Interval (MAC_PPS0_Interval) .

#### 72.17.184 MTL Operation Mode (MTL_Operation_Mode)

Offset
Register Offset
MTL_Operation_Mode C00h
Function
Establishes the transmit and receive operating modes and commands.

---

*Page 2937*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
CNTC CNTP Reserv DTXS Reserv
FRPE Reserved SCHALG Reserved RAA
LR RST ed TS ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-16 Reserved
—
15 Flexible Receive Parser Enable
FRPE Indicates the status of the flexible receive parser.
• If this field is 1, the programmable receive parser functionality is enabled.
• If the receive parser is disabled and is in the middle of parsing, the receive parser functionality is
disabled only after completing the current packet parsing.
When the receive parser is enabled from the disabled state, the receive parser is activated for the next
immediate packet.
0b - Disabled
1b - Enabled
14-10 Reserved
—
9 Counters Reset
CNTCLR Indicates whether the counters are reset.
• If this field is 1, all the counters are reset. The field clears automatically after one clock cycle.
• If you write 1 to this field along with the CNTPRST field, the CNT_PRESET field takes precedence.
Access restrictions apply to this field that clears automatically. Writing 0 to it has no effect.
0b - Not reset
1b - Reset
8 Counters Preset
CNTPRST Indicates the status of preset counters.
If this field is 1,
Table continues on the next page...

---

*Page 2938*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• MTL Tx Queue 0 Underflow (MTL_TxQ0_Underflow) [bits 0-7] is initialized or preset to 12'h7F0.
• Missed packet and overflow packet counters in MTL Rx Queue Missed Packet Overflow Count
(MTL_RxQ0_Missed_Packet_Overflow_Cnt) [bits 0-7] are initialized or preset to 12'h7F0.
Access restrictions apply to this field that clears automatically. Writing 0 to it has no effect.
0b - Disabled
1b - Enabled
7 Reserved
—
6-5 Transmit Scheduling Algorithm
SCHALG Indicates the algorithm for transmit scheduling.
00b - WRR algorithm
01b - WFQ algorithm when DCB feature selected; otherwise, reserved
10b - DWRR algorithm when DCB feature selected; otherwise, reserved
11b - Strict priority algorithm
4-3 Reserved
—
2 Receive Arbitration Algorithm
RAA Is used to select the arbitration algorithm for the receive side. Queue 0 has the lowest priority and the last
queue has the highest priority.
0b - Strict priority (SP)
1b - Weighted strict priority (WSP)
1 Drop Transmit Status
DTXSTS Indicates whether the drop transmit status is enabled.
• If this field is 1, the transmit packet status received from MAC is dropped in MTL.
• If this field is 0, the transmit packet status received from MAC is forwarded to the application.
0b - Disabled
1b - Enabled
0 Reserved
—

---

*Page 2939*

Ethernet Media Access Controller (EMAC)

#### 72.17.185 MTL Debug Control (MTL_DBG_CTL)

Offset
Register Offset
MTL_DBG_CTL C08h
Function
Controls, along with MTL Debug Status (MTL_DBG_STS) , the operation mode of FIFO debug access.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved EIEC EIEE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
FIFOW FIFOR RSTS RSTAL Reserv Reserv DBGM FDBG
STSIE PKTIE FIFOSEL PKTSTATE BYTEEN
REN DEN EL L ed ed OD EN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-19 Reserved
—
18-17 ECC Inject Error Control
EIEC Indicates the status of the ECC inject error control feature for transmit, receive, and TSO memories.
If EIEE = 1, following are the errors inserted based on the value encoded in this field.
00b - 1-bit error
01b - 2-bit errors
10b - 3-bit errors
11b - 1-bit error in the address field
16 ECC Inject Error Enable
EIEE Indicates the status of the ECC error injection feature for transmit, receive, and TSO memories.
• If the value if this field is 1, it enables the ECC error injection feature.
• If the value if this field is 0, it disables the ECC error injection feature.
0b - Disabled
Table continues on the next page...

---

*Page 2940*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
1b - Enabled
15 Transmit Status Available Interrupt Status Enable
STSIE Indicates whether the transmit packet available interrupt status is enabled.
If this field is 1, an interrupt is generated when transmit status is available in Slave mode.
0b - Disabled
1b - Enabled
14 Receive Packet Available Interrupt Status Enable
PKTIE Indicates whether the receive packet available interrupt status is enabled.
If this field is 1, an interrupt is generated when EOP of the received packet is written to the receive FIFO.
0b - Disabled
1b - Enabled
13-12 FIFO Selected for Access
FIFOSEL Indicates the FIFO selected for debug access.
00b - Transmit FIFO
01b - Transmit status FIFO (only read access when SLVMOD is set)
10b - TSO FIFO (cannot be accessed when SLVMOD is set)
11b - Receive FIFO
11 FIFO Write Enable
FIFOWREN Indicates the status of FIFO write.
If this field is 1, it enables the write operation on the selected FIFO when FIFO debug access is enabled.
This field must not be written to 1 when FIFO debug access is disabled, that is, when FDBGEN = 0.
Access restrictions apply to this field that clears automatically. Also, writing 0 to the field clears it and writing
1 sets it.
0b - Disabled
1b - Enabled
10 FIFO Read Enable
FIFORDEN Indicates the status of FIFO read.
If this field is 1, it enables the read operation on the selected FIFO when FIFO debug access is enabled.
This field must not be written to 1 when FIFO debug access is disabled, that is, when FDBGEN = 0.
Access restrictions apply to this field that clears automatically. Also, writing 0 to the field clears it and writing
1 sets it.
0b - Disabled
Table continues on the next page...

---

*Page 2941*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
1b - Enabled
9 Reset Pointers Of Selected FIFO
RSTSEL Indicates whether the resetting of pointers for the selected FIFO is enabled.
If this field is 1, the pointers of the currently selected FIFO are reset when FIFO debug access is enabled.
This field must not be written to 1 when FIFO debug access is disabled, that is, when FDBGEN = 0.
Access restrictions apply to this field that clears automatically. Also, writing 0 to the field clears it and writing
1 sets it.
0b - Disabled
1b - Enabled
8 Reset All Pointers
RSTALL Indicates whether the resetting of all the pointers is enabled.
If this field is 1, the pointers of all the FIFOs are reset when the FIFO debug access is enabled.
This field must not be written to 1 when FIFO debug access is disabled, that is, when FDBGEN = 0.
Access restrictions apply to this field that clears automatically. Also, writing 0 to the field clears it and writing
1 sets it.
0b - Disabled
1b - Enabled
7 Reserved
—
6-5 Encoded Packet State
PKTSTATE Is used to write the control information to the transmit FIFO or receive FIFO.
These are the values related to the transmit FIFO:
• 00: Packet data
• 01: Control word
• 10: SOP data
• 11: EOP data
These are the values related to the receive FIFO:
• 00: Packet data
• 01: Normal status
• 10: Last status
• 11: EOP
00b - Packet data
Table continues on the next page...

---

*Page 2942*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
01b - Control word/normal status
10b - SOP data/last status
11b - EOP data/EOP
4 Reserved
—
3-2 Byte Enables
BYTEEN Indicates the number of data bytes valid in the data register during a write operation. This is valid only when
MTL_DBG_STS[PKTSTATE] is 2'b10 (EOP), and the transmit FIFO or receive FIFO is selected.
00b - Byte 0 valid
01b - Byte 0 and byte 1 valid
10b - Byte 0, byte 1, and byte 2 valid
11b - All four bytes valid
1 Debug Mode Access to FIFO
DBGMOD Indicates the status of Debug mode access to FIFO.
If this field is 1, it indicates that the current access to FIFO is read, write, and debug. In this mode, the
following access types are allowed:
• Read and write access to transmit FIFO, TSO FIFO, and receive FIFO
• Read access to transmit status FIFO
If this field is 0, it indicates that the current access to FIFO is slave access bypassing DMA. In this mode,
the following access types are allowed:
• Write access to transmit FIFO
• Read access to receive FIFO and transmit status FIFO
0b - Disabled
1b - Enabled
0 FIFO Debug Access Enable
FDBGEN Indicates the status of FIFO debug access.
• If this field is 1, it indicates that the FIFO debug mode access is enabled.
• If the value of the field is 0, it indicates that FIFO can be accessed only through a master interface.
0b - Disabled
1b - Enabled

---

*Page 2943*

Ethernet Media Access Controller (EMAC)

#### 72.17.186 MTL Debug Status (MTL_DBG_STS)

Offset
Register Offset
MTL_DBG_STS C0Ch
Function
Contains the status of FIFO debug access.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R LOCR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
FIFOB
R LOCR BYTEEN PKTSTATE
USY
Reserved STSI PKTI Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 1 1 0 0 0
Fields
Field Function
31-15 Remaining Locations In FIFO
LOCR Indicates the remaining locations in FIFO. For Slave Access mode, the field indicates the space available
in the selected FIFO, and for Debug Access mode, this field contains the write or read pointer value of the
selected FIFO during the write or read operation, respectively.
Reset: In single transmit queue configurations, (DWC_EQOS_TXFIFO_SIZE/(DWC_EQOS_DATAWIDTH/
8)), otherwise 0000h.
14-10 Reserved
—
9 Transmit Status Available Interrupt Status
STSI Indicates whether the transmit status available interrupt status is detected.
If this field is 1, it indicates that Slave mode transmit packet is transmitted, and the status is available in
transmit status FIFO. The field resets when you write 1 to it.
0b - Not detected
1b - Detected
8 Receive Packet Available Interrupt Status
Table continues on the next page...

---

*Page 2944*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
PKTI Indicates whether the receive packet available interrupt status is detected.
If this field is 1, it indicates that the MAC layer has written the EOP of the received packet to the receive FIFO.
The field resets when you write 1 to it.
0b - Not detected
1b - Detected
7-5 Reserved
—
4-3 Byte Enables
BYTEEN Indicates the number of data bytes valid in the data register during a read operation. This is valid only when
MTL_DBG_STS[PKTSTATE] is 2'b10 (EOP), and the transmit FIFO or receive FIFO is selected.
00b - Byte 0 valid
01b - Byte 0 and byte 1 valid
10b - Byte 0, byte 1, and byte 2 valid
11b - All four bytes valid
2-1 Encoded Packet State
PKTSTATE Is used to get the control or status information of the selected FIFO.
These are the values related to the transmit FIFO.
• 00: Packet data
• 01: Control word
• 10: SOP data
• 11: EOP data
These are the values related to the receive FIFO.
• 00: Packet data
• 01: Normal status
• 10: Last status
• 11: EOP
This field is applicable only for transmit and receive FIFOs during a read operation.
00b - Packet data
01b - Control word/normal status
10b - SOP data/last status
11b - EOP data/EOP
0 FIFO Busy
FIFOBUSY Indicates whether the FIFO busy status is detected.
Table continues on the next page...

---

*Page 2945*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
If this field is 1, it indicates that a FIFO operation is in progress in MAC and the content of the following
is invalid:
• All other fields of this register
• All the fields of MTL FIFO Debug Data (MTL_FIFO_Debug_Data)
0b - Not detected
1b - Detected

#### 72.17.187 MTL FIFO Debug Data (MTL_FIFO_Debug_Data)

Offset
Register Offset
MTL_FIFO_Debug_Data C10h
Function
Contains the data to be written to or read from the FIFOs.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
FDBGDATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
FDBGDATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 FIFO Debug Data
FDBGDATA Contains the data to be written to the transmit FIFO, receive FIFO, or TSO FIFO during a debug or slave
access write operations. The field contains the data read from the transmit FIFO, receive FIFO, TSO FIFO,
or transmit status FIFO during debug or slave access read operations.

---

*Page 2946*

Ethernet Media Access Controller (EMAC)

#### 72.17.188 MTL Interrupt Status (MTL_Interrupt_Status)

Offset
Register Offset
MTL_Interrupt_Status C20h
Function
Determines the interrupt status of MAC and MTL queues. The software driver (application) reads this register during an
interrupt service routine or polling to perform this function.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
MTLPI
R ESTIS DBGIS
Reserv
S
Reserved Reserved
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R Q1IS Q0IS
Reserv Reserv Reserv Reserv Reserv Reserv
Reserved
ed ed ed ed ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 Reserved
—
23 MTL Receive Parser Interrupt Status
MTLPIS Indicates whether the MTL receive parser interrupt status is detected.
This field indicates an interrupt from the receive parser block. To reset this field, the application must read
MTL Rx Parser Interrupt Control Status (MTL_RXP_Interrupt_Control_Status) to get the exact cause of the
interrupt and clear its source.
0b - Not detected
1b - Detected
22-19 Reserved
—
18 EST (TAS- 802.1Qbv) Interrupt Status
ESTIS Indicates whether the EST (TAS- 802.1Qbv) interrupt status is detected.
Table continues on the next page...

---

*Page 2947*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
This field indicates an interrupt event during the operation of 802.1Qbv. To reset the field, the application
must clear the error or event that caused the interrupt.
0b - Not detected
1b - Detected
17 Debug Interrupt Status
DBGIS Indicates whether the debug interrupt status is detected.
This field indicates an interrupt event during the slave access. To reset the field, the application must read
MTL Debug Status (MTL_DBG_STS) to get the exact cause of the interrupt and clear its source.
0b - Not detected
1b - Detected
16 Reserved
—
15-8 Reserved
—
7 Reserved
—
6 Reserved
—
5 Reserved
—
4 Reserved
—
3 Reserved
—
2 Reserved
—
1 Queue 1 Interrupt Status
Q1IS Indicates whether the queue 1 interrupt status is detected.
To reset this field, the application must read MTL Queue 1 Interrupt Control Status
(MTL_Q1_Interrupt_Control_Status) to get the exact cause of the interrupt and clear its source.
0b - Not detected
Table continues on the next page...

---

*Page 2948*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
1b - Detected
0 Queue 0 Interrupt Status
Q0IS Indicates whether the queue 0 interrupt status is detected.
To reset this field, the application must read MTL Queue 0 Interrupt Control Status
(MTL_Q0_Interrupt_Control_Status) to get the exact cause of the interrupt and clear its source.
0b - Not detected
1b - Detected

#### 72.17.189 MTL Receive Queue DMA Map 0 (MTL_RxQ_DMA_Map0)

Offset
Register Offset
MTL_RxQ_DMA_Map0 C30h
Function
Reserves in EQOS-CORE and EQOS-MTL configurations.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv Reserv Reserv Reserv
Reserved Reserved Reserved Reserved
ed ed ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Q1DD Q1MD Q0DD Q0MD
Reserved Reserved Reserved Reserved
MACH MACH MACH MACH
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-29 Reserved.
—
28 Reserved.
—
Table continues on the next page...

---

*Page 2949*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
27-25 Reserved.
—
24 Reserved.
—
23-21 Reserved.
—
20 Reserved.
—
19-17 Reserved.
—
16 Reserved.
—
15-13 Reserved.
—
12 Queue 1 Enabled for DA-based DMA Channel Selection
Q1DDMACH Enables or disables queue 1 for DA-based DMA channel selection.
If this field is 1, it indicates that the packets received in queue 1 are routed to a particular DMA channel as
decided in the MAC receiver based on the DMA channel number programmed in the L3-L4 filter registers,
or the Ethernet DA address.
When this field becomes 0, it indicates that the packets received in queue 1 are routed to the DMA Channel
programmed in the MTL_RxQ_DMA_Map0[Q1MDMACH] (bits [10:8]).
0b - Disabled
1b - Enabled
11-9 Reserved.
—
8 Queue 1 Mapped to DMA Channel
Q1MDMACH Controls the routing of the received packet in Queue 1 to the DMA channel:
000b - DMA Channel 0
001b - DMA Channel 1
010b - DMA Channel 2
011b - DMA Channel 3
Table continues on the next page...

---

*Page 2950*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
100b - DMA Channel 4
101b - DMA Channel 5
110b - DMA Channel 6
111b - DMA Channel 7
This field is valid when the MTL_RxQ_DMA_Map0[Q1DDMACH] resets.
The field's width depends on the number of RX DMA channels and in some configurations all the values may
not be valid . For example, if two RX DMA channels are selected, then only 000b and 001b are valid, the
other bits are reserved.
7-5 Reserved.
—
4 Queue 0 Enabled for DA-based DMA Channel Selection
Q0DDMACH Enables or disables queue 0 for DA-based DMA channel selection.
If this field is 1, it indicates that the packets received in queue 0 are routed to a particular DMA channel as
decided in the MAC Receiver based on the DMA channel number programmed in the L3-L4 filter registers,
or the Ethernet DA address.
When this field becomes 0, it indicate that the packets received in queue 0 are routed to the DMA Channel
programmed in the MTL_RxQ_DMA_Map0[Q0MDMACH] .
0b - Disable
1b - Enable
3-1 Reserved.
—
0 Queue 0 Mapped to DMA Channel
Q0MDMACH Controls the routing of the packet received in queue 0 to the DMA channel:
000b - DMA channel 0
001b - DMA channel 1
010b - DMA channel 2
011b - DMA channel 3
100b - DMA channel 4
101b - DMA channel 5
110b - DMA channel 6
111b - DMA channel 7
This field is valid when the MTL_RxQ_DMA_Map0[Q0DDMACH] resets.
Table continues on the next page...

---

*Page 2951*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
The field's width depends on the number of RX DMA channels in some configurations all the values may not
be valid. For example, if the number of RX DMA channels selected is 2, only 000 and 001 bits are valid, the
other bits are reserved.

#### 72.17.190 MTL TBS Control (MTL_TBS_CTRL)

Offset
Register Offset
MTL_TBS_CTRL C40h
Function
Controls the operation of time based scheduling.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
LEOS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserv
LEOS LEGOS Reserved LEOV ESTM
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-8 Launch Expiry Offset
LEOS Computes the launch expiry time.
To compute the launch expiry time the value in units of 256 nanoseconds is added to the launch time. The
field value is valid only if MTL_TBS_CTRL[LEOV] is 1.
Max value: 999,999,999 nanosecond should be smaller than CTR-1 value when ESTM mode = 1, because
this value is a modulo CTR value.
7 Reserved.
—
6-4 Launch Expiry GSN Offset
Table continues on the next page...

---

*Page 2952*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
LEGOS Computes the Launch expiry time.
To compute the launch expiry time the number of GSN slots is to be added to the launch GSN. The field
value is valid only if MTL_TBS_CTRL[LEOV] is 1.
3-2 Reserved.
—
1 Launch Expiry Offset Valid
LEOV Indicates if MTL_TBS_CTRL[LEOS] is valid or invalid.
When this field is 1, it indicates that MTL_TBS_CTRL[LEOS] is valid.
When not 1, it indicates that the launch expiry Offset is not valid and the MTL must not check for launch
expiry time.
0b - Invalid
1b - Valid
0 EST offset Mode
ESTM Enables or disables EST Offset mode.
If this field is 1, the launch time value used in Time Based Scheduling is interpreted as an EST offset value
and is added to the BTR of the current list.
When this field becomes 0, the launch time value is used as an absolute value that must be compared with
the system time [39:8].
0b - Disabled
1b - Enabled

#### 72.17.191 MTL EST Control (MTL_EST_Control)

Offset
Register Offset
MTL_EST_Control C50h
Function
Controls the operation of enhancements to scheduled transmission (IEEE802.1Qbv).

---

*Page 2953*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
PTOV CTOV
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserv
CTOV TILS LCSE DFBS DDBF Reserved SSWL EEST
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 PTP Time Offset Value
PTOV Indicates the PTP time offset value.
You must multiply 6 to the value of PTP clock period in nanoseconds. At the beginning of the installation of
a new GCL this value avoid transmission overruns.
23-12 Current Time Offset Value
CTOV Provides a 12 bit time offset value in nano second which is added to the current time to compensate for all
the implementation pipeline delays such as the CDC sync delay, buffering delays, data path delays and so
on. This offset ensures that the impact of gate controls is visible on the line exactly at the pre-determined
schedule (or as close to the schedule as possible).
11 Reserved.
—
10-8 Time Interval Left Shift Amount
TILS Provides the left shift amount for the programmed time interval values used in the gate control lists.
000b - No left shift needed (equal to x1ns)
001b - Left shift TI by 1 bit (equal to x2ns)
010b - Left shift TI by 2 bits (equal to x4ns)
-
-
100b - Left shift TI by 7 bits (equal to x128ns)
On the basis of configuration you must consider one or more bits of this field as reserved or read-only.
7-6 Loop Count to Report Scheduling Error
LCSE Provides programmable number of GCL list iterations before reporting an HLBS error which is defined
in MTL_EST_Status .
Table continues on the next page...

---

*Page 2954*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
00b - 4 iterations
01b - 8 iterations
10b - 16 iterations
11b - 32 iterations
5 Drop Frames Causing Scheduling Error
DFBS Provides the status of the frames causing scheduling error.
If this field is 1 then the frames report to cause an HOL blocking because the MTL_EST_Status[HLBS] is
not scheduled after 4,8,16,and 32 (on the basis of MTL_EST_Control[LCSE] ) GCL iterations are dropped.
0b - Do not drop frames
1b - Drop frames
4 Do not Drop Frames during Frame Size Error
DDBF Provides status of frames during Frame Size Error.
If this field is 1, it indicates that the frames are not dropped during head-of-line blocking because of the frame
size error ( MTL_EST_Status[HLBF] ).
0b - Drop frames during frame size error
1b - Do not Drop frames during frame size error
3-2 Reserved.
—
1 Switch to Software Owned List
SSWL If this field is 1, it indicates that the software has programmed that list that it currently owns (SWOL) and the
hardware should switch to the new list based on the new BTR. Hardware clears this bit when the switch to
the SWOL happens to indicate the completion of the switch or when an BTR error (BTRE in Status register)
is set. When BTRE is set this bit is cleared but SWOL is not updated as the switch was not successful.
Access restriction apply to this field that clears on read and automatically becomes 1 on an internal
event occurrence.
Enables or disables the switch to the software owned list.
0b - Disabled
1b - Enabled
0 Enable EST
EEST Enables or disables EST.
If the field becomes 0, it indicate that the gate control list processing is halted and all gates are assumed to
be in open state. The field must be 1 for the hardware to start processing the gate control lists. During the
toggle from 0 to 1, when the MTL_EST_Control[SSWL] is 1, the gate control list processing starts.
During the configuration when DWC_EQOS_ASP_ECC is selected, the hardware resets this field and
disables the EST function if any uncorrectable error is detected in the EST memory .
Table continues on the next page...

---

*Page 2955*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled

#### 72.17.192 MTL EST Status (MTL_EST_Status)

Offset
Register Offset
MTL_EST_Status C58h
Function
Provides status of enhancements to scheduled transmission (IEEE802.1Qbv).
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R CGSN
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R BTRL SWOL HLBS HLBF
Reserved Reserved CGCE BTRE SWLC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-20 Reserved.
—
19-16 Current GCL Slot Number
CGSN Indicates the slot number of the GCL list. Slot number is a modulo 16 count of the GCL List loops executed
so far.
Even if a new GCL list is installed, the count is incremental.
15-12 Reserved.
—
Table continues on the next page...

---

*Page 2956*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
11-8 BTR Error Loop Count
BTRL Provides the minimum count (N) for which the equation Current Time =< New BTR + (N * New Cycle Time)
becomes true. N = "1111" indicates the iterations exceeded the value of 8 and the hardware was not able
to update New BTR to be equal to or greater than Current Time. Software intervention is needed to update
the New BTR. Value cleared when BTRE field of this register is cleared.
7 S/W owned list
SWOL When '0' indicates Gate control list number "0" is owned by software and when "1" indicates the Gate Control
list "1" is owned by the software. Any reads/writes by the software (using indirect access via GCL_Control)
is directed to the list indicated by this value by default. The inverse of this value is treated as HWOL.
R/W operations performed by hardware are directed to the list pointed by HWOL by default.
0b - Gate control list number "0" is owned by software
1b - Gate control list number "1" is owned by software
6-5 Reserved.
—
4 Constant Gate Control Error
CGCE This error occurs when the list length (LLR) is 1 and the Cycle Time (CTR) is less than or equal to the
programmed Time Interval (TI) value after the optional Left Shifting. The above programming implies
Gates are either always Closed or always Open based on the Gate Control values; the same effect can
be achieved by other simpler (non TSN) programming mechanisms. Since the implementation does not
support such a programming an error is reported.
Access restriction applies. Self-set to 1 on internal event. Setting 1 clears. Setting 0 has no effect.
0b - Constant Gate Control Error not detected
1b - Constant Gate Control Error detected
3 Head-Of-Line Blocking due to Scheduling
HLBS Set when the frame is not able to win arbitration and get scheduled even after 4 iterations of the GCL.
Indicates to software a potential programming error. The one hot encoded values of the Queue Numbers
that are not able to make progress are indicated in the MTL_EST_Sch_Error register. Bit cleared when
MTL_EST_Sch_Error register is all zeros.
0b - Head-Of-Line Blocking due to Scheduling not detected
1b - Head-Of-Line Blocking due to Scheduling detected
2 Head-Of-Line Blocking due to Frame Size
HLBF Set when HOL Blocking is noticed on one or more Queues as a result of none of the Time Intervals of gate
open in the GCL being greater than or equal to the duration needed for frame size (or frame fragment size
when preemption is enabled) transmission. The one hot encoded Queue numbers that are experiencing
HLBF are indicated in the MTL_EST_Frm_Size_Error register. Additionally, the first Queue number that
experienced HLBF along with the frame size is captured in MTL_EST_Frm_Size_Capture register. Bit
cleared when MTL_EST_Frame_Size_ Error register is all zeros.
Table continues on the next page...

---

*Page 2957*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Head-Of-Line Blocking due to Frame Size not detected
1b - Head-Of-Line Blocking due to Frame Size detected
1 BTR Error
BTRE Indicates whether the BTR error is detected.
When this field is 1, it indicates a programming error in the BTR of SWOL where the programmed value is
less than the current time. If the BTRL = "1111", SWOL is not updated and software must reprogram the BTR
to a value greater than current time and then set SSWL to reinitiate the switch to SWOL. Else if the value of
BTRL < "1111", SWOL is updated and this field indicates the number of iterations (of + CycleTime) taken
by hardware to update the BTR to a value greater than Current Time.
Access restrictions apply to this field. It sets automatically to 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - BTR Error not detected
1b - BTR Error detected
0 Switch to Software Owned List Complete
SWLC Indicates whether the switch to software owned list complete is detected.
When the field is 1, it indicates that the hardware has successfully switched to the SWOL, and updated the
MTL_EST_Status[SWOL] to that effect.
This field is 0 when MTL_EST_Control[SSWL] transitions from 0 to 1, or on a software write.
Access restrictions apply to this field. It clears automatically and writing 0 to it has no effect.
0b - Not detected
1b - Detected

#### 72.17.193 MTL EST Scheduling Error (MTL_EST_Sch_Error)

Offset
Register Offset
MTL_EST_Sch_Error C60h
Function
Provides the one hot encoded queue numbers that have the scheduling related error (timeout).

---

*Page 2958*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved SEQN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-2 Reserved.
—
1-0 Schedule Error Queue Number
SEQN Provides the one hot encoded queue numbers that have experienced error or timeout described
in MTL_EST_Status[HLBS] .
Access restriction apply to this field. It automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 to it has no effect.

#### 72.17.194 MTL EST Frame Size Error (MTL_EST_Frm_Size_Error)

Offset
Register Offset
MTL_EST_Frm_Size_Err C64h
or
Function
Provides the one hot encoded queue numbers that have the frame Size related error.

---

*Page 2959*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved FEQN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-2 Reserved.
—
1-0 Frame Size Error Queue Number
FEQN Provides the one hot encoded queue Nnumbers that have experienced error described
in MTL_EST_Status[HLBF] .
Access restriction apply to this field. It automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 to it has no effect.

#### 72.17.195 MTL EST Frame Size Capture (MTL_EST_Frm_Size_Capture)

Offset
Register Offset
MTL_EST_Frm_Size_Ca C68h
pture
Function
Captures the frame Size and queue number of the first occurrence of the frame Size related error. When you write 0, it
captures the data of immediate next occurrence of a similar error.

---

*Page 2960*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R HBFQ
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R HBFS
Reserv
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-17 Reserved.
—
16 Queue Number of HLBF
HBFQ Captures the binary value of the first queue (number) which experiences the HLBF error (see
MTL_EST_Status[HLBF] ). Any subsequent queue errors similar to HLBF errors does not alter the value
after it is written. After this field is 0, it captures the queue number of the next occurring HLBF error.
Field's width is based on the number of transit queues configured; remaining fields are read-only. This field
clears when MTL_EST_Frm_Size_Error is all zeros.
15 Reserved.
—
14-0 Frame Size of HLBF
HBFS Captures the frame Size of the dropped frame related to queue number indicated
in MTL_EST_Frm_Size_Capture[HBFQ] .
If this field is zero, then this register must be considered invalid.
This field clears when MTL_EST_Frm_Size_Error is all zeros.

#### 72.17.196 MTL EST Interrupt Enable (MTL_EST_Intr_Enable)

Offset
Register Offset
MTL_EST_Intr_Enable C70h
Function
Implements the interrupt enable fields for the various events that generate an interrupt. Bit positions have a 1 to 1 correlation
with the status bit positions in MTL_ETS_Status register.

---

*Page 2961*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved CGCE IEHS IEHF IEBE IECC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-5 Reserved.
—
4 Interrupt Enable for CGCE
CGCE Indicates whether an interrupt for MTL_EST_Intr_Enable[CGCE] is enabled.
When this field is 1, it generates an interrupt when the constant gate control error occurs and is indicated
in the status. When this field becomes 0, this event does not generate an interrupt
0b - Disabled
1b - Enabled
3 Interrupt Enable for HLBS
IEHS Indicates whether an interrupt for MTL_EST_Status[HLBS] is enabled.
When this field is 1, it generates an interrupt when the head-of-line blocking due to scheduling issue occurs
and is indicated in the status. When this field becomes 0, this event does not generate an interrupt.
0b - Disabled
1b - Enabled
2 Interrupt Enable for HLBF
IEHF Indicates whether an interrupt for MTL_EST_Status[HLBF] is enabled.
When this field is 1, it generates an interrupt when the head-of-line blocking due to frame size error occurs
and is indicated in the status. When this field becomes 0, this event does not generate an interrupt.
0b - Disabled
1b - Enabled
1 Interrupt Enable for BTR Error
IEBE Indicates whether an interrupt for BTR error is enabled.
Table continues on the next page...

---

*Page 2962*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
When this field is 1, it generates an interrupt when the BTR error occurs and is indicated in the status. When
this field becomes 0, this event does not generate an interrupt.
0b - Disabled
1b - Enabled
0 Interrupt Enable for Switch List
IECC Indicates whether the interrupt for switch list is enabled.
When this field is 1, it generates an interrupt when the configuration change is successful and the hardware
switches to the new list. When this field becomes 0, this event does not generate an interrupt.
0b - Disabled
1b - Enabled

#### 72.17.197 MTL EST GCL Control (MTL_EST_GCL_Control)

Offset
Register Offset
MTL_EST_GCL_Control C80h
Function
Provides the control information for reading and writing to the gate control lists.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
ESTEI
Reserved ESTEIEC ERR0 Reserved
EE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserv
ADDR Reserved DBGB DBGM GCRR R1W0 SRWO
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 Reserved.
Table continues on the next page...

---

*Page 2963*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
—
23-22 ECC Inject Error Control for EST Memory
ESTEIEC Provides the status of inject error control for EST memory.
When MTL_EST_GCL_Control[ESTEIEE] = 1, it indicates that on the basis of the value encoded in this field
following errors are inserted.
This field is valid only if you select DWC_EQOS_ASP_ECC feature during the configuration, otherwise it
is reserved.
00b - Insert 1 bit error
01b - Insert 2 bit errors
10b - Insert 3 bit errors
11b - Insert 1 bit error in address field
21 EST ECC Inject Error Enable
ESTEIEE Indicates whether the EST ECC inject error is enabled.
If this field along with MTL_EST_Control[EEST] = 1, it enables the ECC error injection feature.
When this field becomes 0, it disables the ECC error injection feature.
0b - Disabled
1b - Enabled
20 If this field is 1, it indicates that when the software writes to GCL the last write operation was aborted and
when MTL_EST_Control[SSWL] is 1, GCL registers are prohibited.
ERR0
Access restriction apply to this field and automatically becomes 1 on an internal event occurrence. Writing
1 to this field clears it and writing 0 to this field has no effect.
0b - ERR0 is disabled
1b - ERR1 is enabled
19-16 Reserved.
—
15-8 Gate Control List Address: (GCLA when GCRR is "0").
ADDR Provides the address (row number) of the gate control list at which the R/W operation has to be performed.
By default the gate control list to which MTL_EST_Status[SWOL] points is selected for read and write. If
MTL_EST_GCL_Control[DBGM] is 1, it indicates that MTL_EST_GCL_Control[DBGB] gives Debug mode
access to read and write. The field's width depends on DWC_EQOS_EST_DEP and the unused bits must
be treated as read only.
Gate Control list Related Registers Address: (GCRA when GCRR is "1").
By default the GCL related register set pointed by MTL_EST_Status[SWOL] is selected for read or write.
If MTL_EST_GCL_Control[DBGM] , it indicates that MTL_EST_GCL_Control[DBGB] gives Debug mode
access to read write. Lower 3 bits are only used in this mode, higher order bits are treated as don't cares.
Table continues on the next page...

---

*Page 2964*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
000b - BTR Low (31:0)
001b - BTR High (63:31)
010b - CTR Low (31:0)
011b - CTR High (39:32)
100b - TER (31:0)
101b - LLR (n:0) (where n is (log{DWC_EQOS_EST_DEP} / log2))
Others - Reserved
7-6 Reserved.
—
5 Debug Mode Bank Select
DBGB Indicates whether the read and write in Debug mode is directed to bank 0 or bank 1.
When this field is 0, it indicates that read or write in Debug mode must be directed to bank
0 (GCL0 and corresponding Time related registers). When this field is 1, it indicates that read
or write in Debug mode should be directed to bank 1 (GCL1 and corresponding Time related
registers). When MTL_EST_GCL_Control[DBGM] is 1 you can use this value and overrides with the
MTL_EST_Status[SWOL] value.
0b - Directed to bank 0
1b - Directed to bank 1
4 Debug Mode
DBGM Enables or disables Debug mode.
When this field is 1, it indicates that the read and write is in Debug mode where the
MTL_EST_GCL_Control[DBGB] value explicitly provides the memory bank (for GCL and time related
registers). When this field is 0, it indicates that MTL_EST_Status[SWOL] determines which bank to use.
0b - Disabled
1b - Enabled
3 Reserved.
—
2 Gate Control Related Registers
GCRR Enables or disables gate control related registers.
When this field is 1, it indicates that the read or write access is for the GCL related registers (BTR, CTR,
TER, LLR) for which GCRA provides the address. When this field is 0, it indicates that read and write must
be directed to GCL from the address which GCLA provides.
0b - Disabled
1b - Enabled
Table continues on the next page...

---

*Page 2965*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
1 Read '1', Write '0'
R1W0 Indicates whether this field performs read operation or write operation.
When this field is 1 it indicates that it performs a read operation.
When this field is 0 it indicates that it performs a write operation.
0b - Write operation
1b - Read operation
0 Start Read/Write Operation
SRWO Indicates whether the read/write operation is enabled.
When this field is 1, it indicates that a read/write operation has started and is in progress.
When hardware resets this field and MTL_EST_GCL_Control[ERR0] = 1 , it indicates that the read/write
operation has completed or an error has occurred.
Reads: When this field becomes 0, it indicates that MTL_EST_GCL_Data reads data.
Writes: Before writing 1 to this field, you must program MTL_EST_GCL_Data with write data.
Access restrictions apply to this field. It clears automatically. Writing 1 sets this field and writing 0 to it has
no effect.
0b - Disabled
1b - Enabled

#### 72.17.198 MTL EST GCL Data (MTL_EST_GCL_Data)

Offset
Register Offset
MTL_EST_GCL_Data C84h
Function
Holds the read data or write data in case of reads and writes respectively.

---

*Page 2966*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
GCD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
GCD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Gate Control Data
GCD Provides the data corresponding to the address selected in the MTL_EST_GCL_Control . This field is used
for both read and write operations.

#### 72.17.199 MTL FPE Control Status (MTL_FPE_CTRL_STS)

Offset
Register Offset
MTL_FPE_CTRL_STS C90h
Function
Controls the operation of, and provides status for frame preemption (IEEE802.1Qbu/802.3br).
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R HRS
Reserved Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved PEC Reserved AFSZ
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2967*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-29 Reserved.
—
28 Hold/Release Status
HRS Indicates whether a set-and-release-MAC operation which was last executed and the pMAC is in release
state or in hold state.
0b - Release state
1b - Hold state
27-16 Reserved.
—
15-10 Reserved.
—
9-8 Preemption Classification
PEC When this field is 1, it indicates that you must classify the corresponding queue as preemptable.
When this field is 0, it indicates that you must classify the queue as express.
When set indicates the corresponding Queue must be classified as preemptable, when '0' Queue is
classified as express. When both EST (Qbv) and Preemption are enabled, Queue-0 is always assumed to
be preemptable. When you enables EST (Qbv), the preemptable queues are always assumed to be in open
state in gate control list.
7-2 Reserved.
—
1-0 Additional Fragment Size
AFSZ Indicates that, in units of 64 bytes, the minimum number of bytes over 64 bytes required in non-final
fragments of preempted frames. The minimum non-final fragment size is (AFSZ +1) * 64 bytes.

#### 72.17.200 MTL FPE Advance (MTL_FPE_Advance)

Offset
Register Offset
MTL_FPE_Advance C94h
Function
Holds the hold and release advance time.

---

*Page 2968*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
RADV
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
HADV
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-16 Release Advance
RADV Provides the maximum time in nanoseconds that can elapse between issuing a release to the MAC and the
MAC being ready to resume transmission of preemptable frames, in the absence of there being any express
frames available for transmission.
15-0 Hold Advance
HADV Provides the maximum time in nanoseconds that can elapse between issuing a hold to the MAC and the
MAC ceasing to transmit any preemptable frame that is in the process of transmission or any preemptable
frames that are queued for transmission.

#### 72.17.201 MTL Rx Parser Control Status (MTL_RXP_Control_Status)

Offset
Register Offset
MTL_RXP_Control_Statu CA0h
s
Function
Establishes the operating mode of receive parser and provides some status.

---

*Page 2969*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXPI
Reserved NPE
W
Reset 1 0 0 0 0 0 0 0 0 0 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
MTL_
Reserved NVE
SCS1
W
Reset 0 0 0 0 0 0 0 0 0 0 1 1 1 1 1 1
Fields
Field Function
31 RX Parser in Idle State
RXPI Indicates whether the receive parser is in idle state.
When this field is 1, it indicates that the receive parser is in idle state and waiting for the processing of a new
packet. When parser disables, you can use this field as a handshake with software and also the software
can update the receive parser instruction table when you write 1 to this field.
0b - Not in Idle state
1b - Idle state
30-22 Reserved.
—
21-16 Number of parsable entries in the Instruction table
NPE Indicates the number of parsable entries in the instruction memory. This is used in receive parser logic to
detect programming error.
MTL_RXP_Interrupt_Control_Status[NPEOVIS] = 1, if the number of parsed entries for a packet is more
than this entry.
15 MTL_SCS1
MTL_SCS1 Is reserved for NXP internal use.
The value of this field must always be 0 unless NXP instructs you otherwise. Writing 1 to any of the bits of
this field might cause unexpected behavior in th IP.
14-6 Reserved.
—
5-0 Number Of Valid Entry Address Or Index In The Instruction Table
NVE Indicates the number of valid entry address or index in the instruction memory.
Table continues on the next page...

---

*Page 2970*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
The maximum valid entry address is = NVE + 1 and the addresses or indices range from 0 to 32. Therefore,
if the value of this field is 32, the number of valid entry address is 33. This value is used in receive parser
logic to detect if any programming errors exist. When parsing, the module writes 1 to NVEOVIS if the number
of the memory address is found to be more than the defined maximum valid entry address number.
NOTE
The minimum value of this field must be two.

#### 72.17.202 MTL Rx Parser Interrupt Control Status (MTL_RXP_Interrupt_Control_Status)

Offset
Register Offset
MTL_RXP_Interrupt_Con CA4h
trol_Status
Function
Provides enable control for the interrupts and also provides interrupt status.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
PDRFI FOOVI NPEO NVEO
Reserved
E E VIE VIE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PDRFI FOOVI NPEO NVEO
Reserved
S S VIS VIS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-20 Reserved.
—
19 Packet Drop due to RF Interrupt Enable
PDRFIE Indicates whether the packet drop due to RF interrupt is enabled.
If this field is 1, it enables the PDRFIS interrupt. When this field becomes 0, it disables the PDRFIS interrupt.
Table continues on the next page...

---

*Page 2971*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled
18 Frame Offset Overflow Interrupt Enable
FOOVIE Indicates whether the frame offset overflow interrupt is enabled.
If this field is 1, it enables the FOOVIS interrupt.
When this field becomes 0, it disables the FOOVIS interrupt.
0b - Disabled
1b - Enabled
17 Number of Parsable Entries Overflow Interrupt Enable
NPEOVIE Enables or disables the number of parsable entries overflow interrupt.
If this field is 1, it enables the NPEOVIS interrupt.
When this field becomes 0, it disables the NPEOVIS interrupt.
0b - Disable
1b - Enable
16 Number of Valid Entries Overflow Interrupt Enable
NVEOVIE Enables or disables the number of valid entries overflow interrupt.
If this field is 1, it enables the NVEOVIS interrupt.
When this field becomes 0, it disables the NVEOVIS interrupt.
0b - Disable
1b - Enable
15-4 Reserved.
—
3 Packet Dropped due to RF Interrupt Status
PDRFIS Indicates whether the packet dropped due to RF interrupt status is detected.
If the Rx Parser result says to drop the packet by setting RF=1 in the instruction memory, then this bit is set
to 1.
This field clears when the application writes 1 to this field.
Access apply to this field. It automatically sets to 1 on an internal event occurrence. Writing 1 clears this field
and writing 0 to it has no effect.
0b - Not detected
1b - Detected
2 Frame Offset Overflow Interrupt Status
Table continues on the next page...

---

*Page 2972*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
FOOVIS Indicates whether the frame offset overflow interrupt status is detected.
If this field is 1, it indicates that when parsing, the instruction table entry's frame offset is more than the
EOF offset.
This field clears when the application writes 1 to this field.
Access apply to this field. It automatically sets to 1 on an internal event occurrence. Writing 1 clears this field
and writing 0 to it has no effect.
0b - Not detected
1b - Detected
1 Number of Parsable Entries Overflow Interrupt Status
NPEOVIS Indicates whether the number of parsable entries overflow interrupt status is detected.
If this field is 1, it indicates that when parsing a packet the number of parsed entries are more than
NPE[] ( MTL_RXP_Control_Status[NPE] ).
This field clears when the application writes 1 to this field.
Access apply to this field. It automatically sets to 1 on an internal event occurrence. Writing 1 clears this field
and writing 0 to it has no effect.
0b - Not detected
1b - Detected
0 Number of Valid Entry Address/Index Overflow Interrupt Status
NVEOVIS Indicates whether the number of valid entry address or index overflow interrupt status is detected.
If this field is 1, it indicates that when parsing, the instruction address is more
than MTL_RXP_Control_Status[NVE] .
For example, when MTL_RXP_Control_Status[NVE] = 31, the maximum valid entry address/index is NVE+1
that is 32 (addresses/indices = 0 to 32, or 33 entries). MTL_RXP_Interrupt_Control_Status[NVEOVIS] = 1
when currently processed entry indicates that the next address is 33 or more, that is 34th or later entries.
This field clears when the application writes 1 to this field.
Access apply to this field. It automatically sets to 1 on an internal event occurrence. Writing 1 clears this field
and writing 0 to it has no effect.
0b - Not detected
1b - Detected

#### 72.17.203 MTL Rx Parser Drop Count (MTL_RXP_Drop_Cnt)

Offset
Register Offset
MTL_RXP_Drop_Cnt CA8h

---

*Page 2973*

Ethernet Media Access Controller (EMAC)
Function
Provides the drop count of receive parser initiated drops.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
RXPD
R RXPDC
COVF
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXPDC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Rx Parser Drop Counter Overflow Bit
RXPDCOVF Indicates whether the receive parser drop count overflow has occurred.
When this field is 1, it indicates that the MTL_RXP_Drop_Cnt[RXPDC] counter field has crossed the
maximum limit.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not occurred
1b - Occurred
30-0 Rx Parser Drop Count
RXPDC Provides the receive parser drop count.
This 31-bit counter is implemented whenever a receive parser drops a packet due to RF = 1. The counter
clears when you read the register.

#### 72.17.204 MTL Rx Parser Error Count (MTL_RXP_Error_Cnt)

Offset
Register Offset
MTL_RXP_Error_Cnt CACh
Function
Provides the receive parser related error occurrence count.

---

*Page 2974*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
RXPE
R RXPEC
COVF
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXPEC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Rx Parser Error Counter Overflow Bit
RXPECOVF Indicates whether the receive parser error counter overflow has occurred.
When this field is 1, it indicates that MTL_RXP_Error_Cnt[RXPEC] counter field has crossed the
maximum limit.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not occurred
1b - Occurred
30-0 Rx Parser Error Count
RXPEC Provides the receive parser error count.
This 31-bit counter is implemented whenever a receive parser encounters following error scenarios:
Entry address >= NVE[]
Number parsed entries >= NPE[]
Entry address > EOF data entry address
The counter clears when you read the register.

#### 72.17.205 MTL Rx Parser Indirect Access Control Status (MTL_RXP_Indirect_Acc_Control_Status)

Offset
Register Offset
MTL_RXP_Indirect_Acc_ CB0h
Control_Status
Function
Provides the indirect access control and status for receive parser memory.

---

*Page 2975*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
STAR RXPEI WRRD
Reserved RXPEIEC Reserved
TBU ... EE N
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved ADDR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 FRP Instruction Table Access Busy
STARTBUSY When this bit is set to 1 by the software then it indicates to start the Read/Write operation from/to the Rx
Parser Memory.
Software should read this bit as 0 before issuing read or write request to access the Parser
Memory Instructions.
This bit when set to 1 indicates that hardware is busy until its gets cleared by hardware and software should
not issue any read or write operation.
0b - hardware not busy
1b - hardware is busy (Read/Write operation from/to the Rx Parser Memory)
30-23 Reserved.
—
22-21 ECC Inject Error Control for Rx Parser Memory
RXPEIEC Controls the ECC inject error for receive parser memory.
When MTL_RXP_Indirect_Acc_Control_Status[RXPEIEE] = 1, it indicates that on the basis of the value
encoded in this field following errors are inserted:
00b - Insert 1 bit error
01b - Insert 2 bit errors
10b - Insert 3 bit errors
11b - Insert 1 bit error in address field
20 ECC Inject Error Enable for Rx Parser Memory
RXPEIEE Indicates whether the ECC inject error for receive parser memory is enabled.
If this field is 1, it enables the ECC error injection feature.
When this field becomes 0, it disables the ECC error injection feature.
Table continues on the next page...

---

*Page 2976*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled
19-17 Reserved.
—
16 Read Write Control
WRRDN Controls the read and write operation to the receive parser memory.
If this field is 1, it indicates the write operation to the receive parser memory.
When this field is 0, it indicates the read operation to the receive parser memory.
0b - Read operation to the receive parser memory
1b - Write operation to the receive parser memory
15-8 Reserved.
—
7-0 FRP Instruction Table Offset Address
ADDR Indicates the ADDR of the 32-bit entry in receive parser instruction table. Each entry has 128-bit (4x32-
bit words).
The X depends on the configurable DWC_EQOS_FRP_ENTRIES.
If DWC_EQOS_FRP_ENTRIES = 256 , then X = 9
If DWC_EQOS_FRP_ENTRIES = 128 , then X = 8
If DWC_EQOS_FRP_ENTRIES = 64, then X = 7

#### 72.17.206 MTL Rx Parser Indirect Access Data (MTL_RXP_Indirect_Acc_Data)

Offset
Register Offset
MTL_RXP_Indirect_Acc_ CB4h
Data
Function
Provides the data associated to indirect access to receive parser memory.

---

*Page 2977*

Ethernet Media Access Controller (EMAC)
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
31-0 FRP Instruction Table Write/Read Data
DATA You must write this register before issuing any write command.
After read command, when MTL_RXP_Indirect_Acc_Control_Status[STARTBUSY] = 0, the hardware
provides the read data from the receive parser memory for read operation.

#### 72.17.207 MTL ECC Control (MTL_ECC_Control)

Offset
Register Offset
MTL_ECC_Control CC0h
Function
Establishes the operating mode of ECC related to MTL memories.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
MEEA Reserv MRXP MEST MRXE MTXE
Reserved Reserved
O ed EE EE E E
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 1 1 1 1

---

*Page 2978*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-9 Reserved.
—
8 MTL ECC Error Address Status Over-ride
MEEAO Enables or disables the MTL ECC error address status over-ride.
When this field is 1, it indicates that MTL_ECC_Err_Addr_Status[EUEAS] and
MTL_ECC_Err_Addr_Status[ECEAS] hold the last valid address where the error is detected.
When this field becomes 0, it indicates that MTL_ECC_Err_Addr_Status[EUEAS] and
MTL_ECC_Err_Addr_Status[ECEAS] hold the first address where the error is detected.
0b - Disable
1b - Enable
7-5 Reserved.
—
4 Reserved.
—
3 MTL Rx Parser ECC Enable
MRXPEE Indicates whether the MTL receive parser ECC is enabled.
If this field is 1, it enables the ECC feature for receive parser memory. When this field is 0, it disables the
ECC feature for receive parser memory.
0b - Disabled
1b - Enabled
2 MTL EST ECC Enable
MESTEE Indicates whether the MTL EST ECC is enabled.
If this field is 1, it enables the ECC feature for EST memory. When this field is 0, it disables the ECC feature
for EST memory.
0b - Disabled
1b - Enabled
1 MTL Rx FIFO ECC Enable
MRXEE Indicates whether the MTL Rx FIFO ECC is enabled.
If this field is 1, it enables the ECC feature for MTL receive FIFO memory. When this field is 0, it disables
the ECC feature for MTL receive FIFO memory.
0b - Disabled
1b - Enabled
0 MTL Tx FIFO ECC Enable
Table continues on the next page...

---

*Page 2979*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
MTXEE Indicates whether the MTL Tx FIFO ECC is enabled.
If this field is 1, it enables the ECC feature for MTL Tx FIFO memory. When field is 0, it disables the ECC
feature for MTL Tx FIFO memory.
0b - Disabled
1b - Enabled

#### 72.17.208 MTL Safety Interrupt Status (MTL_Safety_Interrupt_Status)

Offset
Register Offset
MTL_Safety_Interrupt_St CC4h
atus
Function
Provides safety interrupt status.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv
Reserved
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R MEUIS MECIS
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Reserved
— Indicates whether the MAC safety uncorrectable interrupt status is detected.
0b - Not detected
1b - Detected
30-2 Reserved.
Table continues on the next page...

---

*Page 2980*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
—
1 MTL ECC Uncorrectable Error Interrupt Status
MEUIS Indicates whether the MTL ECC uncorrectable error interrupt status is detected.
Indicates that an uncorrectable error interrupt event in the MTL ECC safety feature. The application must
read MTL_ECC_Interrupt_Status to get the exact cause of the interrupt.
0b - Not detected
1b - Detected
0 MTL ECC Correctable Error Interrupt Status
MECIS Indicates whether the MTL ECC correctable error interrupt status is detected.
Indicates that a correctable error interrupt event in the MTL ECC safety feature. The application must read
MTL_ECC_Interrupt_Status to get the exact cause of the interrupt .
0b - MTL ECC Correctable error Interrupt Status not detected
1b - MTL ECC Correctable error Interrupt Status detected

#### 72.17.209 MTL ECC Interrupt Enable (MTL_ECC_Interrupt_Enable)

Offset
Register Offset
MTL_ECC_Interrupt_Ena CC8h
ble
Function
Provides enable bits for the ECC interrupts.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
RPCEI RXCEI TXCEI
Reserved Reserved ECEIE Reserved Reserved
E E E
W
Reset 0 0 0 1 0 0 0 1 0 0 0 1 0 0 0 1

---

*Page 2981*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-13 Reserved.
—
12 Rx Parser Memory Correctable Error Interrupt Enable
RPCEIE Indicates whether the Rx parser memory correctable error interrupt is enabled.
When this field is 1, it generates an interrupt when an uncorrectable error is detected at the Rx parser
memory interface. It is indicated in MTL_ECC_Interrupt_Status[RPCES] .
When this field becomes 0, it indicates that this event does not generates an interrupt.
0b - Disabled
1b - Enabled
11-9 Reserved.
—
8 EST Memory Correctable Error Interrupt Enable
ECEIE Indicates whether the EST memory correctable error interrupt is enabled.
When this field is 1, it generates an interrupt when a correctable error is detected at the MTL EST memory
interface. It is indicated in MTL_ECC_Interrupt_Status[ECES] .
When this field becomes 0, it indicates that this event does not generates an interrupt.
0b - Disabled
1b - Enabled
7-5 Reserved.
—
4 Rx Memory Correctable Error Interrupt Enable
RXCEIE Indicates whether the Rx memory correctable error interrupt is enabled.
When this field is 1, it generates an interrupt when a correctable error is detected at the MTL Rx memory
interface. It is indicated in MTL_ECC_Interrupt_Status[RXCES] .
When this field becomes 0, it indicates that this event does not generates an interrupt.
0b - Disabled
1b - Enabled
3-1 Reserved.
—
0 Tx Memory Correctable Error Interrupt Enable
TXCEIE Indicates whether the Tx memory correctable error interrupt is enabled.
Table continues on the next page...

---

*Page 2982*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
When this field is 1, it generates an interrupt when a correctable error is detected at the MTL Tx memory
interface. It is indicated in MTL_ECC_Interrupt_Status[TXCES] .
When this field becomes 0, it indicates that this event does not generates an interrupt.
0b - Disabled
1b - Enabled

#### 72.17.210 MTL ECC Interrupt Status (MTL_ECC_Interrupt_Status)

Offset
Register Offset
MTL_ECC_Interrupt_Stat CCCh
us
Function
Provides MTL ECC Interrupt Status.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserv RPUE RPAM RPCE Reserv Reserv RXUE RXAM RXCE Reserv TXUE TXAM TXCE
EUES EAMS ECES
ed S S S ed ed S S S ed S S S
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-15 Reserved.
—
14 Rx Parser Memory Uncorrectable Error Status
RPUES Indicates whether the Rx parser memory uncorrectable error status is detected.
When this field is 1, it indicates that an uncorrectable error is detected at Rx parser memory interface.
Table continues on the next page...

---

*Page 2983*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Not detected
1b - Detected
13 MTL Rx Parser Memory Address Mismatch Status
RPAMS Indicates whether the MTL Rx parser memory address mismatch status is detected.
When this field is 1, it indicates that an address mismatch is found for Rx parser memory's address bus.
0b - Not detected
1b - Detected
12 MTL Rx Parser Memory Correctable Error Status
RPCES Indicates whether the MTL Rx parser memory correctable error status is detected.
When this field is 1, it indicates that a correctable error is detected at RX parser memory interface.
0b - Not detected
1b - Detected
11 Reserved.
—
10 MTL EST Memory Uncorrectable Error Status
EUES Indicates whether the MTL EST memory uncorrectable error status is detected.
When this field is 1, it indicates that an uncorrectable error is detected at MTL EST memory interface.
0b - Not detected
1b - Detected
9 MTL EST Memory Address Mismatch Status
EAMS Indicates whether the MTL EST memory address mismatch status is detected.
When this field is 1, it indicates that an address mismatch is found for MTL EST memory's address bus.
0b - Not detected
1b - Detected
8 MTL EST Memory Correctable Error Status
ECES Indicates whether the MTL EST memory correctable error status is detected.
When this field is 1, it indicates that a correctable error is detected at the MTL EST memory.
0b - Not detected
1b - Detected
7 Reserved.
—
Table continues on the next page...

---

*Page 2984*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
6 MTL Rx Memory Uncorrectable Error Status
RXUES Indicates whether the MTL Rx memory uncorrectable error status is detected.
When this field is 1, it indicates that an uncorrectable error is detected at the MTL Rx memory interface.
0b - Not detected
1b - Detected
5 MTL Rx Memory Address Mismatch Status
RXAMS Indicates whether the MTL Rx memory address mismatch status is detected.
When this field is 1, it indicates that an address mismatch is found for MTL Rx memory's address bus.
0b - Not detected
1b - Detected
4 MTL Rx memory Correctable Error Status
RXCES Indicates whether the MTL Rx memory correctable error status is detected.
When this field is 1, it indicates that a correctable error is detected at the MTL Rx memory.
0b - Not detected
1b - Detected
3 Reserved.
—
2 MTL Tx Memory Uncorrectable Error Status
TXUES Indicates whether the MTL Tx memory uncorrectable error status is detected.
When this field is 1, it indicates that an uncorrectable error is detected at the MTL TX memory interface.
0b - Not detected
1b - Detected
1 MTL Tx Memory Address Mismatch Status
TXAMS Indicates whether the MTL Tx memory address mismatch status is detected.
When this field is 1, it indicates that an address mismatch is found for MTL Tx memory's address bus.
0b - Not detected
1b - Detected
0 MTL Tx Memory Correctable Error Status
TXCES Indicates whether the MTL Tx memory correctable error status is detected.
When this field is 1, it indicates that a correctable error is detected at the MTL Tx memory.
0b - Not detected
Table continues on the next page...

---

*Page 2985*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
1b - Detected

#### 72.17.211 MTL ECC Error Status (MTL_ECC_Err_Sts_Rctl)

Offset
Register Offset
MTL_ECC_Err_Sts_Rctl CD0h
Function
Establishes the control for ECC error status capture.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
EESR
Reserved CUES CCES EMS
E
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-6 Reserved.
—
5 Clear Uncorrectable Error Status
CUES Indicates whether the clear uncorrectable error status is detected.
When this field and MTL_ECC_Err_Sts_Rctl[EESRE] is 1, it indicates that based on the
MTL_ECC_Err_Sts_Rctl[EMS] , the respective memory's uncorrectable error address and uncorrectable
error count values are cleared upon reading.
When all the error status values are cleared hardware resets this field.
0b - Not detected
1b - Detected
Table continues on the next page...

---

*Page 2986*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
4 Clear Correctable Error Status
CCES Indicates whether the clear correctable error status is detected.
When this field and MTL_ECC_Err_Sts_Rctl[EESRE] is 1, it indicates that based on the
MTL_ECC_Err_Sts_Rctl[EMS] ,, the respective memory's correctable error address and correctable error
count values are cleared upon reading.
When all the error status values are cleared, hardware resets this field.
0b - Not detected
1b - Detected
3-1 MTL ECC Memory Selection
EMS Provides the memory selection encoding.
When MTL_ECC_Err_Sts_Rctl[EESRE] is 1, this field indicates which memory's error status value to
be read.
The memory selection encoding is as described below.
000b - MTL Tx memory
001b - MTL Rx memory
010b - MTL EST memory
011b - MTL Rx Parser memory
100b - DMA TSO memory
0 MTL ECC Error Status Read Enable
EESRE Indicates whether the MTL ECC error status read is enabled.
When this field is 1, it indicates that based on MTL_ECC_Err_Sts_Rctl[EMS] , the respective memory's error
status values are captured as described below:
• The correctable and uncorrectable error count values are captured into MTL_ECC_Err_Cnt_Status.
• The address location of correctable and uncorrectable errors are captured into
MTL_ECC_Err_Addr_Status .
When all the status values are captured into MTL_ECC_Err_Cnt_Status and MTL_ECC_Err_Addr_Status ,
hardware resets this field.
0b - Disabled
1b - Enabled

#### 72.17.212 MTL ECC Error Address Status (MTL_ECC_Err_Addr_Status)

Offset
Register Offset
MTL_ECC_Err_Addr_Sta CD4h
tus

---

*Page 2987*

Ethernet Media Access Controller (EMAC)
Function
Provides the memory addresses for the correctable and uncorrectable errors.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R EUEAS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R ECEAS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-16 MTL ECC Uncorrectable Error Address Status
EUEAS Based on the MTL_ECC_Err_Sts_Rctl[EMS] , this field holds the respective memory's address locations for
which an uncorrectable error or address mismatch is detected.
When MTL_ECC_Control[MEEAO] = 1, this field holds the last valid address of memory for which either an
uncorrectable error or an address mismatch is detected.
When MTL_ECC_Control[MEEAO] = 0, this field holds the first address of the memory for which either an
uncorrectable error or address mismatch is detected.
15-0 MTL ECC Correctable Error Address Status
ECEAS Based on the MTL_ECC_Err_Sts_Rctl[EMS] , this field holds the respective memory's address locations for
which a correctable error is detected.
When MTL_ECC_Control[MEEAO] = 1, this field holds the last valid address of memory for which the
correctable error or address mismatch is detected.
When MTL_ECC_Control[MEEAO] = 0, this field holds the first address of the memory for which correctable
error is detected.

#### 72.17.213 MTL ECC Error Control Status (MTL_ECC_Err_Cntr_Status)

Offset
Register Offset
MTL_ECC_Err_Cntr_Stat CD8h
us
Function
Provides ECC Error count for correctable and uncorrectable errors.

---

*Page 2988*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R EUECS
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R ECECS
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-20 Reserved.
—
19-16 MTL ECC Uncorrectable Error Counter Status
EUECS Based on the EMS field of MTL_ECC_Err_Cntr_Rctl register, this field holds the respective memory's
uncorrectable error count value.
15-8 Reserved.
—
7-0 MTL ECC Correctable Error Counter Status
ECECS Based on the EMS field of MTL_ECC_Err_Cntr_Rctl register, this field holds the respective memory's
correctable error count value.

#### 72.17.214 MTL DPP Control (MTL_DPP_Control)

Offset
Register Offset
MTL_DPP_Control CE0h
Function
Establishes the operating mode of data parity protection and error injection.

---

*Page 2989*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R OPE
Reserv Reserv Reserv IPEDD IPEMR IPEMT Reserv Reserv
Reserved IPERD IPETD IPEMC IPEID EDPP
ed ed ed C F S ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1
Fields
Field Function
31-14 Reserved.
—
13 Reserved.
—
12 Reserved.
—
11 Insert Parity error in Rx write-back Descriptor parity generator
IPERD Indicates whether the insert parity error in receive write-back descriptor parity generator is enabled.
When this field is 1, it flips the parity bit of first valid data to which the DMA Rx write-back descriptor parity
generator(or at PG8 as shown in Receive data path parity protection diagram) generates.
0b - Disabled
1b - Enabled
10 Insert Parity error in Tx write-back Descriptor parity generator
IPETD Indicates whether the insert parity error in transit write-back descriptor parity generator is enabled.
When this field is 1, it flips the parity bit of first valid data to which the DMA Tx write-back descriptor parity
generator(or at PG4 as shown in Transmit data path parity protection diagram) generates.
Hardware clears this field, when the respective parity bit flips.
0b - Disabled
1b - Enabled
9 Reserved.
—
8 Insert Parity Error in DMA DTX Control Word Parity Generator
Table continues on the next page...

---

*Page 2990*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
IPEDDC Indicates whether the insert parity error in DMA DTX control word parity generator is enabled.
When this field is 1, it flips the parity bit of first valid data to which the DMA DTX Control word parity generator
(or at PG2 as shown in Transmit data path parity protection diagram) generates.
Hardware clears this field, when the respective parity bit flips.
0b - Disabled
1b - Enabled
7 Insert Parity Error in MTL Rx FIFO Read Control Parity Generator
IPEMRF Inserts parity Error in MTL receive FIFO read control parity generator.
When this field is 1, it flips the parity bit of first valid data to which the MTL Rx FIFO read control parity
generator (or at PG7 as shown in Receive data path parity protection diagram) generates.
Hardware clears this field, when the respective parity bit flips.
0b - Disabled
1b - Enabled
6 Insert Parity Error in MTL Tx Status Parity Generator
IPEMTS Indicates whether the insert parity error in MTL transit status parity generator is enabled.
When this field is 1, it flips the parity bit of first valid data to which the MTL Tx Status parity generator (or at
PG6 as shown in Transmit data path parity protection diagram) generates.
Hardware clears this field, when the respective parity bit flips.
0b - Disabled
1b - Enabled
5 Insert Parity Error in MTL Checksum Parity Generator
IPEMC Indicates whether the insert parity error in MTL checksum parity generator is enabled.
When this field is 1, it flips the parity bit of first valid data to which the MTL checksum parity generator (or
at PG5 as shown in Transmit data path parity protection diagram) generates.
Hardware clears this field, when the respective parity bit flips.
0b - Disabled
1b - Enabled
4 Insert Parity Error in Interface Data Parity Generator
IPEID Indicates whether the insert parity error in interface data parity generator is enabled.
When this field is 1, it flips the parity bit of first valid input data to which the interface data parity generator
(or at PG1 as shown in Transmit data path parity protection diagram) generates.
Following are the input data bus on which parity bits are generated on the basis of configuration selected.
In AHB Config, hrdata_i
In AXI config, rdata_m_i
Table continues on the next page...

---

*Page 2991*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
In DMA Config, mdc_rdata_i
In MTL Config, ati_data_i
Hardware clears this field, when the respective parity bit flips.
0b - Disabled
1b - Enabled
3 Reserved.
—
2 Reserved.
—
1 Odd Parity Enable
OPE Indicates whether an odd parity protection is enabled.
When this field is 1, it enables an odd parity protection on all the external interfaces and when this field is
0, it enables an even parity protection on all the external interfaces.
0b - Disabled
1b - Enabled
0 Enable Data path Parity Protection
EDPP Enables or disables the data path parity protection.
When this field is 1, it indicates that by generating and checking the parity on EQOS datapath it enables
the parity protection for EQOS datapath . When this field is 0, it disables the parity protection for
EQOS datapath.
0b - Disable
1b - Enable

#### 72.17.215 MTL Tx Queue 0 Operation Mode (MTL_TxQ0_Operation_Mode)

Offset
Register Offset
MTL_TxQ0_Operation_M D00h
ode
Function
Establishes the transmit queue operating modes and commands.

---

*Page 2992*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved TQS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved TTC TXQEN TSF FTQ
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-21 Reserved.
—
20-16 Transmit Queue Size
TQS Indicates the size of the allocated transmit queues in blocks of 256 bytes. This field is read-write only if the
number of transit queues are more than one, the reset value is 0x0 and indicates 256 bytes size. This means
that value of 0x0 = 256 bytes, 0x1 = 512 bytes and so on. You must program TQS [5:0] = 6'b001111 to
allocate queue size of 4096 (4K) bytes . In general, the size of the Queue = (TQS+1)*256 bytes.
When the number of transit queue is one, the field is read-only and the reset value reflects the configured
TX FIFO size in blocks of 256 bytes.
The field's width depends on the transit memory size selected in your configuration. For example, if the
memory size is 2048, the field's width is 3 bits:
LOG2(2048/256) = LOG2(8) = 3 bits
15-7 Reserved.
—
6-4 Transmit Threshold Control
TTC Controls the threshold level of the MTL transit queue. The transmission starts when the packet size within
the MTL transit queue is larger than the threshold, also transmits full packets with length less than the
threshold. These fields are used only when MTL_TxQ0_Operation_Mode[TSF] resets.
000b - 32
001b - 64
010b - 96
011b - 128
100b - 192
101b - 256
Table continues on the next page...

---

*Page 2993*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
110b - 384
111b - 512
3-2 Transmit Queue Enable
TXQEN Enables or disables the transmit queue 0.
2'b00 - Not enabled
2'b01 - Reserved
2'b10 - Enabled
2'b11 - Reserved
This field is read only in single queue configurations and read write in multiple queue configurations.
NOTE
In multiple transit queue configuration, all the queues disables by default and programming
this field enables the transit queue.
00b - Not enabled
01b - Enable in AV mode (Reserved in non-AV)
10b - Enabled
11b - Reserved
1 Transmit Store and Forward
TSF Indicates whether the transmit store and forward is enabled.
When this field is 1, it indicates that the transmission starts when a full packet resides in the MTL transit
queue and the TTC values specified in Bits[6:4] of this register are ignored. This field must be changed only
when you stop the transmission.
0b - Disabled
1b - Enabled
0 Flush Transmit Queue
FTQ Indicates whether the flush transmit queue is enabled.
When this field is 1, it indicates that the transit queue controller logic resets to its default values. Therefore,
all the data in the transit queue is lost or flushed. After the flushing operation completes this field resets
internally. You must not write to the MTL_TxQ1_Operation_Mode until this field resets. The data to which
the MAC transmitter has already accepted is not flushed. It is scheduled for transmission and results in
underflow and runt packet transmission.
NOTE
The flush operation completes only when the transit queue is empty and the application
accepts the pending transit status of all the transmitted packets. To complete the flush
operation, the PHY Tx clock (CLK_TX_I) must be active.
Table continues on the next page...

---

*Page 2994*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
Access restriction apply to this field. It clears automatically. Writing 1 sets this field and writing 0 has
no effect.
0b - Disabled
1b - Enabled

#### 72.17.216 MTL Tx Queue 0 Underflow (MTL_TxQ0_Underflow)

Offset
Register Offset
MTL_TxQ0_Underflow D04h
Function
Contains the counter for packets aborted because of transmit queue underflow and packets missed because of receive queue
packet flush.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
UFCN
R UFFRMCNT
TOVF
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-12 Reserved.
—
11 Overflow Bit for Underflow Packet Counter
UFCNTOVF Indicates whether the overflow is detected for underflow packet counter.
Table continues on the next page...

---

*Page 2995*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
This field is 1, when the transit queue underflow packet counter field overflows, that is, it has crossed the
maximum count. In such a scenario, the overflow packet counter resets to all-zeros and this field indicates
that the rollover occurred.
Access restriction apply to this field. It clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
10-0 Underflow Packet Counter
UFFRMCNT Indicates the number of packets aborted by the controller because of transit queue underflow. This counter
increments each time the MAC aborts outgoing packet because of underflow. The counter clears when this
register is read with mci_be_i[0] at 1'b1.
Access restriction apply to this field. It clears on read and automatically becomes 1 on an internal
event occurrence.

#### 72.17.217 MTL Tx Queue 0 Debug (MTL_TxQ0_Debug)

Offset
Register Offset
MTL_TxQ0_Debug D08h
Function
Provides the debug status of various blocks related to the transmit queue.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R STXSTSF PTXQ
Reserv
Reserved
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
TXSTS TXQS TWCS TXQP
R TRCSTS
FS ... TS TS AUS ...
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2996*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-23 Reserved.
—
22-20 Number of Status Words in Tx Status FIFO of Queue
STXSTSF Indicates the current number of status in the transit status FIFO of this queue.
This field does not reflect the number of status words in transit status FIFO, when
MTL_Operation_Mode[DTXSTS] = 1.
19 Reserved.
—
18-16 Number of Packets in the Transmit Queue
PTXQ Indicates the current number of packets in the transit queue.
This field does not reflect the number of packets in the transmit queue, when
MTL_Operation_Mode[DTXSTS] = 1.
15-6 Reserved.
—
5 MTL Tx Status FIFO Full Status
TXSTSFSTS Indicates whether the MTL transit status and FIFO full status is detected.
When this field is 1, it indicates that the MTL transit status FIFO is full. Therefore, MTL cannot accept any
more packets for transmission.
0b - Not detected
1b - Detected
4 MTL Tx Queue Not Empty Status
TXQSTS Indicates whether the MTL transit queue not empty status is detected.
When this field is high, it indicates that the MTL transit queue is not empty and some data is left
for transmission.
0b - Not detected
1b - Detected
3 MTL Tx Queue Write Controller Status
TWCSTS Indicates whether the MTL transit queue write controller status is detected.
When high, this field indicates that the MTL transit queue write controller is active, and transfers the data to
the transit queue.
0b - Not detected
1b - Detected
Table continues on the next page...

---

*Page 2997*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
2-1 MTL Tx Queue Read Controller Status
TRCSTS Indicates the state of the transit queue read controller.
00b - Idle state
01b - Read state (transferring data to the MAC transmitter)
10b - Waiting for pending transit status from the MAC transmitter
11b - Flushing the transit queue because of the packet abort request from the MAC
0 Transmit Queue in Pause
TXQPAUSED Indicates whether the transmit queue in pause is detected.
When this field is 1 and the receive flow control is enabled, it indicates that the transit queue is in the pause
condition (in the full-duplex only mode) because of the following scenario:
• Receives PFC packet with the priorities assigned to the transit queue when PFC is enabled.
• Receives 802.3x pause packet when PFC is disabled.
0b - Not detected
1b - Detected

#### 72.17.218 MTL Tx Queue 0 ETS Status (MTL_TxQ0_ETS_Status)

Offset
Register Offset
MTL_TxQ0_ETS_Status D14h
Function
Provides the average traffic transmitted in queue 0.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R ABS
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R ABS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2998*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-24 Reserved.
—
23-0 Average Bits per Slot
ABS Contains the average transmitted bits per slot.
Computes over every 10 million bit times slot (4 ms in 2500 Mbit/s; 10 ms in 1000 Mbit/s; 100 ms in 100
Mbit/s), when the DCB operation enables for queue 0. The maximum value is 0x989680.

#### 72.17.219 MTL Tx Queue Quantum Weight (MTL_TxQ0_Quantum_Weight)

Offset
Register Offset
MTL_TxQ0_Quantum_W D18h
eight
Function
Contains the quantum value for Deficit Weighted Round Robin (DWRR), weights for the Weighted Round Robin (WRR), and
Weighted Fair Queuing (WFQ) for queue 0.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved ISCQW
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
ISCQW
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-21 Reserved.
—
20-0 Quantum or Weights
Table continues on the next page...

---

*Page 2999*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
ISCQW Contains the quantum value in bytes which is added to credit during every queue scanning cycle, when DCB
operation enables with DWRR algorithm for queue 0 traffic. The maximum value is 0x1312D0 bytes.
Contains the weight for this queue, when DCB operation enables with WFQ algorithm for queue 0 traffic. The
maximum value is 0x3FFF where weight of 0 indicates 100% bandwidth. Write 0 to bits[20:14]. The higher
the programmed weights, the lesser is the bandwidth that is allocated for the particular transmit queue. This
is because the weights compute the packet finish time (weights*packet_size). The lesser the finish time, the
higher is the probability of the packet getting scheduled first and consuming more bandwidth.
Contains the weight for this queue, when DCB operation or generic queuing operation enables with WRR
algorithm for queue 0 traffic. The maximum value is 0x64.
Write 0 to bits [20:7].

#### 72.17.220 MTL Queue 0 Interrupt Control Status (MTL_Q0_Interrupt_Control_Status)

Offset
Register Offset
MTL_Q0_Interrupt_Contr D2Ch
ol_Status
Function
Contains the interrupt enable and status bits for the queue 0 interrupts.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
RXOV
Reserved RXOIE Reserved
FIS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
ABPSI ABPSI TXUN
Reserved TXUIE Reserved
E S FIS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-25 Reserved.
—
Table continues on the next page...

---

*Page 3000*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
24 Receive Queue Overflow Interrupt Enable
RXOIE Enables or disables the receive queue overflow interrupt.
When this field is 1, it enables the receive queue overflow interrupt.
When this field becomes 0, it disables the receive queue overflow interrupt.
0b - Disabled
1b - Enabled
23-17 Reserved.
—
16 Receive Queue Overflow Interrupt Status
RXOVFIS Indicates whether the status of receive queue overflow interrupt is detected.
This bit indicates that the receive queue had an overflow when you receive the packet. If you transfer a
partial packet to the application, the overflow status sets in RDES3[21]. This field is 0 when the application
writes 1 to it.
Access restriction apply to this field. It becomes 0 automatically on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
15-10 Reserved.
—
9 Average Bits Per Slot Interrupt Enable
ABPSIE Enables or disables the average bits per slot interrupt.
When this field is 1, it indicates that when you update the average bits per slot status, the MAC asserts the
sbd_intr_o or mci_intr_o interrupt.
When this field is 0, it indicates that the interrupt do not assert for such an event.
0b - Disabled
1b - Enabled
8 Transmit Queue Underflow Interrupt Enable
TXUIE Enables or disables transmit queue underflow interrupt.
When this field is 1, it enables the transmit queue underflow interrupt.
When this field becomes 0, it disables the transmit queue underflow interrupt.
0b - Disabled
1b - Enabled
7-2 Reserved.
Table continues on the next page...

---

*Page 3001*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
—
1 Average Bits Per Slot Interrupt Status
ABPSIS Indicates whether the status of average bits per slot interrupt is detected.
When this field is 1, it indicates that the MAC has updated the ABS value. This field is 0 when the application
writes 1 it.
Access restriction apply to this field. It becomes 0 automatically on an internal event occurrence. Writing 1
sets this field and writing 0 has no effect.
0b - Not detected
1b - Detected
0 Transmit Queue Underflow Interrupt Status
TXUNFIS Indicates whether the status of transmit queue underflow interrupt is detected.
This field indicates that, when you transmit the packet,the transmit queue had an underflow. Suspend the
transmission and write 1 to an underflow error TDES3[2].
This field is 0, when the application writes 1 to it.
Access restriction apply to this field. It becomes 0 automatically on an internal event occurrence. Writing 1
sets this field and writing 0 has no effect.
0b - Not detected
1b - Detected

#### 72.17.221 MTL Rx Queue 0 Operation Mode (MTL_RxQ0_Operation_Mode)

Offset
Register Offset
MTL_RxQ0_Operation_ D30h
Mode
Function
Establishes the receive queue operating modes and command.
The RFA and RFD fields are not backward compatible with the RFA and RFD fields of 4.00a release

---

*Page 3002*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved RQS Reserved RFD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
DIS_T Reserv
RFD Reserved RFA EHFC RSF FEP FUP RTC
CP ... ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-25 Reserved.
—
24-20 Receive Queue Size
RQS Indicates the size of the allocated receive queues in blocks of 256 bytes. The
MTL_RxQ0_Operation_Mode[RQS] is read-write only if the number of receive queues are more than
one, the reset value is 0x0 and indicates size of 256 bytes. This means that value of 0x0 = 256 bytes, 0x1 =
512 bytes and so on. You must program RQS [5:0] = 6'b001111 to allocate queue size of 4096 (4K) bytes.
In general, the size of the Queue = (RQS+1)*256 bytes.
When the number of receive queues is one, the field is read-only and the configured receive FIFO size in
blocks of 256 bytes is reflected in the reset value.
The field width depends on the receive memory size selected in your configuration. For example, if the
memory size is 2048, the field width is 3 bits:
LOG2(2048/256) = LOG2(8) = 3 bits
19-18 Reserved.
—
17-14 Threshold for Deactivating Flow Control (in half-duplex and full-duplex modes)
RFD Controls the threshold (fill-level of Rx queue) at which the flow control is de-asserts after activation.
0 - Full minus 1 KB, that is, FULL 1 KB
1 - Full minus 1.5 KB, that is, FULL 1.5 KB
2 - Full minus 2 KB, that is, FULL 2 KB
3 - Full minus 2.5 KB, that is, FULL 2.5 KB
...
14 - Full minus 8 KB, that is, FULL 8 KB
15 - Full minus 8.5 KB, that is, FULL 8.5 KB
Table continues on the next page...

---

*Page 3003*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
De-assertion is effective only after flow control is asserted.
NOTE
You must program the value in such a way that the threshold is a positive number.
When MTL_RxQ0_Operation_Mode[EHFC] = 1, these values are applicable only when the receive queue
size which the MTL_RxQ0_Operation_Mode[RQS] determines is equal to or greater than 4 KB.
For a given queue size, the values ranges between 0 and the encoding for FULL minus (QSIZE - 0.5
KB) and all other values are illegal. Here the term FULL and QSIZE refers to the queue size which the
MTL_RxQ0_Operation_Mode[RQS] determines.
The field width depends on RX FIFO size selected during the configuration. Remaining bits are reserved and
read only.
13-12 Reserved.
—
11-8 Threshold for Activating Flow Control (in half-duplex and full-duplex)
RFA Controls the threshold (fill-level of receive queue) at which the flow control is activated.
See MTL_RxQ0_Operation_Mode[RFD] for more information on encoding for this field.
7 Enable Hardware Flow Control
EHFC Enables or disables hardware flow control.
If this field is 1, it enables the flow control signal operation, on the basis of the fill-level of receive queue.
When this field becomes 0, it disables the flow control operation.
0b - Disable
1b - Enable
6 Disable Dropping of TCP/IP Checksum Error Packets
DIS_TCP_EF Enables or disable dropping of TCP or IP checksum error packets.
When this field is 1, it indicates that the MAC does not drop the packets which only have the errors which
the receive checksum offload engine detects. Such packets have errors only in the encapsulated payload.
There are no errors (including FCS error) in the Ethernet packet received by the MAC.
When this field becomes 0, it indicates that all error packets are dropped if
MTL_RxQ0_Operation_Mode[FEP] resets.
0b - Enable
1b - Disable
5 Receive Queue Store and Forward
RSF Indicates whether the receive queue store and forward is enabled.
When this field is 1, it indicates that the module reads a packet from the receive queue only after the
complete packet has been written to it, ignores the MTL_RxQ0_Operation_Mode[RTC] .
Table continues on the next page...

---

*Page 3004*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
When this field becomes 0, it indicates that the receive queue operates in the Threshold (cut-through) mode,
subject to the threshold which the MTL_RxQ0_Operation_Mode[RTC] specifies.
0b - Disabled
1b - Enabled
4 Forward Error Packets
FEP Indicates whether the forward error packets are enabled.
When this field becomes 0, it indicates that the receive queue drop packets with an error status (CRC error,
GMII_ER, watchdog timeout, or overflow). However, the packet does not drop if the start byte (write) pointer
of a packet is already transferred to the read controller side (in Threshold mode).
When this field is 1, it indicates that all packets except the runt error packets are forwarded
to the application or DMA. The packet is dropped irrespective of the setting of this field, if
MTL_RxQ0_Operation_Mode[RSF] is 1 and the receive queue overflows when a partial packet is written.
However, if MTL_RxQ0_Operation_Mode[RSF] becomes 0 and the receive queue overflows when a partial
packet is written, a partial packet might be forwarded to the application or DMA.
0b - Disabled
1b - Enabled
3 Forward Undersized Good Packets
FUP Indicates whether the forward undersized good packets are enabled.
When this field is 1, it indicates that the receive queue forwards the undersized good packets (packets with
no error and length less than 64 bytes), including pad-bytes and CRC.
When this field becomes 0, it indicates that the receive queue drops all packets of less than 64 bytes,
unless a packet is already transferred because of the lower value of receive threshold, for example,
MTL_RxQ0_Operation_Mode[RTC] = 01.
0b - Disabled
1b - Enabled
2 Reserved.
—
1-0 Receive Queue Threshold Control
RTC Controls the threshold level of the MTL receive queue (in bytes).
The received packet is transferred to the application or DMA when the packet size within the MTL receive
queue is larger than the threshold. In addition, automatically transfer full packets with length less than
the threshold.
This field is valid only when MTL_RxQ0_Operation_Mode[RSF] = 0.
This field is ignored when MTL_RxQ0_Operation_Mode[RSF] = 1.
00b - 64
Table continues on the next page...

---

*Page 3005*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
01b - 32
10b - 96
11b - 128

#### 72.17.222 MTL Rx Queue Missed Packet Overflow Count (MTL_RxQ0_Missed_Packet_Overflow_Cnt)

Offset
Register Offset
MTL_RxQ0_Missed_Pac D34h
ket_Overflow_Cnt
Function
Contains the counter for packets missed because of receive queue packet flush and packets discarded because of receive
queue overflow.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
MISCN
R MISPKTCNT
TO ...
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
OVFC
R OVFPKTCNT
NTO ...
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-28 Reserved.
—
27 Missed Packet Counter Overflow Bit
MISCNTOVF Indicates whether the missed packet counter overflow is detected.
When this field is 1, it indicates that the receive queue missed packet counter has crossed the
maximum limit.
Table continues on the next page...

---

*Page 3006*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
Access restriction apply to this field. It clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
26-16 Missed Packet Counter
MISPKTCNT Indicates the number of packets this module has missed because the application asserts ari_pkt_flush_i[]
for this queue. This counter resets when this register reads with mci_be_i[0] at 1b1.
This counter increments by 1 when the DMA discards the packet because of buffer unavailability.
Access restriction apply to this field. It clears on read and automatically becomes 1 on an internal
event occurrence.
15-12 Reserved.
—
11 Overflow Counter Overflow Bit
OVFCNTOVF Indicates whether the counter overflow is detected.
When this field is 1, it indicates that the receive queue overflow packet counter field has crossed the
maximum limit.
Access restriction apply to this field. It clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
10-0 Overflow Packet Counter
OVFPKTCNT Indicates the number of packets which this module discards because of receive queue overflow. This
counter increments each time the module discards an incoming packet because of overflow. This counter
resets when this register reads with mci_be_i[0] at 1'b1.
Access restriction apply to this field. It clears on read and automatically becomes 1 on an internal
event occurrence.

#### 72.17.223 MTL Rx Queue 0 Debug (MTL_RxQ0_Debug)

Offset
Register Offset
MTL_RxQ0_Debug D38h
Function
Provides the debug status of various blocks related to the receive queue.

---

*Page 3007*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R PRXQ
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
RWCS
R RXQSTS RRCSTS
Reserv
TS
Reserved
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-30 Reserved.
—
29-16 Number of Packets in Receive Queue
PRXQ Indicates the current number of packets in the receive queue. The theoretical maximum value for this field
is 256KB/16B = 16K packets, that is, Max_Queue_Size/Min_Packet_Size.
15-6 Reserved.
—
5-4 MTL Rx Queue Fill-Level Status
RXQSTS Provides the status of the receive queue fill-level.
00b - Rx Queue empty
01b - Rx Queue fill-level below flow-control deactivate threshold
10b - Rx Queue fill-level above flow-control activate threshold
11b - Rx Queue full
3 Reserved.
—
2-1 MTL Rx Queue Read Controller State
RRCSTS Provides the state of the receive queue read controller.
00b - Idle state
01b - Reading packet data
10b - Reading packet status (or timestamp)
11b - Flushing the packet data and status
Table continues on the next page...

---

*Page 3008*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0 MTL Rx Queue Write Controller Active Status
RWCSTS Indicates whether the MTL receive queue write controller active status is detected.
When high, this bit indicates that the MTL receive queue write controller is active, and it is transferring a
received packet to the receive queue.
0b - Not detected
1b - Detected

#### 72.17.224 MTL Rx Queue 0 Control 0 (MTL_RxQ0_Control)

Offset
Register Offset
MTL_RxQ0_Control D3Ch
Function
Controls the receive arbitration and passing of received packets to the application.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
RXQ_F
Reserved RXQ_WEGT
RM ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-4 Reserved.
—
3 Receive Queue Packet Arbitration
RXQ_FRM_AR Indicates whether the receive queue packet arbitration is enabled.
BIT
Table continues on the next page...

---

*Page 3009*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
When this field is 1, it indicates that the module drives the packet data to the ARI interface such that the entire
packet data of currently-selected queue transmits before switching to other queue.
When this field becomes 0, it indicates that the module drives the packet data to the ARI interface such that
the following amount of data of currently-selected queue transmits before switching to other queue:
• PBL amount of data (indicated by ari_qN_pbl_i[]), or
• Complete data of a packet
The status and the timestamp are not a part of the PBL data. Therefore, the module drives the complete
status (including timestamp status) during first PBL request for the packet (in store-and-forward mode) or
the last PBL request for the packet (in Threshold mode).
0b - Disabled
1b - Enabled
2-0 Receive Queue Weight
RXQ_WEGT Indicates the weight assigned to receive queue 0.
You must write a value to this field that is 1 less than the required queue weight. Therefore, if this field is 0,
it indicates that the queue weight is 1, if this field is 1, it indicates that the queue weight is 2, and so on. You
must use this weight to calculate the number of continuous PBL or packet requests, depending on the value
of RXQ_FRM_ARBIT , allocated to the queue in an arbitration cycle.
The field's value changes when the current service round completes or when there is a change from
RAA=SP to RAA=WSP algorithm. This approach is required for a smooth transition. You must configure
MTL Rx Queue 0 Control 0 (MTL_RxQ0_Control) before MTL Operation Mode (MTL_Operation_Mode) if
you want to change the field's value before either of these two processes completes.

#### 72.17.225 MTL Tx Queue 1 Operation Mode (MTL_TxQ1_Operation_Mode)

Offset
Register Offset
MTL_TxQ1_Operation_M D40h
ode
Function
Establishes the transmit queue operating modes and commands.

---

*Page 3010*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved TQS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved TTC TXQEN TSF FTQ
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-21 Reserved.
—
20-16 Transmit Queue Size
TQS Indicates the size of the allocated transmit queues in blocks of 256 bytes. This field is read-write only if the
number of transit queues is more than one, the reset value is 0x0 and indicates size of 256 bytes. This
means that value of 0x0 = 256 bytes, 0x1 = 512 bytes and so on. You must program TQS [5:0] = 6'b001111
to allocate queue size of 4096 (4K) bytes. In general, the size of the queue = (TQS+1)*256 bytes.
When the number of transit queues is one, the field is read-only and the reset value reflects the configured
TX FIFO size in blocks of 256 bytes.
The field width depends on the transit memory size selected in your configuration. For example, if the
memory size is 2048, the field width is 3 bits:
LOG2(2048/256) = LOG2(8) = 3 bits
15-7 Reserved.
—
6-4 Transmit Threshold Control
TTC Controls the threshold level of the MTL Tx Queue. The transmission starts when the packet size within the
MTL Tx Queue is larger than the threshold. In addition, it also transmit full packets with length less than the
threshold. These fields are used only when the MTL_TxQ1_Operation_Mode[TSF] resets.
000b - 32
001b - 64
010b - 96
011b - 128
100b - 192
101b - 256
Table continues on the next page...

---

*Page 3011*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
110b - 384
111b - 512
3-2 Transmit Queue Enable
TXQEN Enables or disables the transmit queue 0.
2'b00 - Not enabled
2'b01 - Enable in AV mode
2'b10 - Enabled
2'b11 - Reserved
NOTE
All the queues disables by default, in multiple transit queues configuration. You must
program this field to enable the transit queue.
00b - Not enabled
01b - Enable in AV mode (Reserved in non-AV)
10b - Enabled
11b - Reserved
1 Transmit Store and Forward
TSF Indicates whether the transmit store and forward is enabled.
When this field is 1, it indicates that the transmission starts when full packet resides in the MTL transit queue
and the TTC values specified in MTL_TxQ1_Operation_Mode[TTC] are ignored. This field must change only
when the transmission stops.
0b - Transmit Store and Forward is disabled
1b - Transmit Store and Forward is enabled
0 Flush Transmit Queue
FTQ Indicates whether the flush transmit queue is enabled.
When this field is 1, it indicates that the transmit queue controller logic resets to its default values. Therefore,
all the data in the transit queue is lost or flushed. This field resets internally when the flushing operation
completes. Until this field becomes 0, you must not write to MTL_TxQ1_Operation_Mode . The data which
the MAC transmitter has already accepted is not flushed. It is scheduled for transmission and results in an
underflow and runt packet transmission.
NOTE
The flush operation completes only when the transit queue is empty and the application
has accepted the pending transit status of all transmitted packets. To complete this flush
operation, the PHY Tx clock (CLK_TX_I) must be active.
Access restriction apply to this field. Writing 1 sets this field and it clears automatically. Writing 0 has
no effect.
Table continues on the next page...

---

*Page 3012*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled

#### 72.17.226 MTL Tx Queue 1 Underflow (MTL_TxQ1_Underflow)

Offset
Register Offset
MTL_TxQ1_Underflow D44h
Function
Contains the counter for packets aborted because of transmit queue underflow and packets missed because of receive queue
packet flush.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
UFCN
R UFFRMCNT
TOVF
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-12 Reserved.
—
11 Overflow Bit for Underflow Packet Counter
UFCNTOVF Indicates whether the overflow is detected for underflow packet counter.
When this field is 1, it indicates that every time the transit queue underflow packet counter field overflows,
that is, it has crossed the maximum count. In such case, the overflow packet counter resets to all-zeros and
this field indicates that the rollover have occurred.
Table continues on the next page...

---

*Page 3013*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
Access restriction apply to this field. It clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
10-0 Underflow Packet Counter
UFFRMCNT Indicates the number of packets which the controller abort because of transit queue underflow. This counter
increments each time the MAC aborts outgoing packet because of underflow. The counter clears when this
register is read with mci_be_i[0] at 1'b1.
Access restriction apply to this field. It clears on read and automatically becomes 1 on an internal
event occurrence.

#### 72.17.227 MTL Tx Queue 1 Debug (MTL_TxQ1_Debug)

Offset
Register Offset
MTL_TxQ1_Debug D48h
Function
Provides the debug status of various blocks related to the transmit queue.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R STXSTSF PTXQ
Reserv
Reserved
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
TXSTS TXQS TWCS TXQP
R TRCSTS
FS ... TS TS AUS ...
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-23 Reserved.
Table continues on the next page...

---

*Page 3014*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
—
22-20 Number of Status Words in Tx Status FIFO of Queue
STXSTSF Indicates the current number of status in the transit status FIFO of this queue.
When MTL_Operation_Mode[DTXSTS] = 1, this field does not reflect the number of status words in transit
status FIFO.
19 Reserved.
—
18-16 Number of Packets in the Transmit Queue
PTXQ Indicates the current number of packets in the transit queue.
When MTL_Operation_Mode[DTXSTS] = 1, this field does not reflect the number of packets in the
transmit queue.
15-6 Reserved.
—
5 MTL Tx Status FIFO Full Status
TXSTSFSTS Indicates whether the MTL transit status FIFO full status is detected.
When this field is 1, it indicates that the MTL transit status FIFO is full. Therefore, the MTL cannot accept
any more packets for transmission.
0b - Not detected
1b - Detected
4 MTL Tx Queue Not Empty Status
TXQSTS Indicates whether the MTL transit queue not empty status is detected.
When this field is 1, it indicates that the MTL transit queue is not empty and some data is left for transmission.
0b - Not detected
1b - Detected
3 MTL Tx Queue Write Controller Status
TWCSTS Indicates whether the MTL transit queue write controller status is detected.
When this field is 1, it indicates that the MTL transit queue write controller is active, and it is transferring the
data to the transit queue.
0b - Not detected
1b - Detected
2-1 MTL Tx Queue Read Controller Status
TRCSTS Indicates the state of the transit queue read controller.
Table continues on the next page...

---

*Page 3015*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
00b - Idle state
01b - Read state (transferring data to the MAC transmitter)
10b - Waiting for pending transit status from the MAC transmitter
11b - Flushing the transit queue because of the packet abort request from the MAC
0 Transmit Queue in Pause
TXQPAUSED Indicates whether the transmit queue in pause status is detected.
When this field is 1 and the receive flow control is enabled, it indicates that the transit queue is in the pause
condition (in the full-duplex only mode) because of these scenarios:
• Reception of the PFC packet for the priorities assigned to the transit queue when PFC is enabled.
• Reception of 802.3x pause packet when PFC is disabled.
0b - Not detected
1b - Detected

#### 72.17.228 MTL Tx Queue 1 ETS Control (MTL_TxQ1_ETS_Control)

Offset
Register Offset
MTL_TxQ1_ETS_Control D50h
Function
Controls the enhanced transmission selection operation.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
AVAL
Reserved SLC CC Reserved
G
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 3016*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-7 Reserved.
—
6-4 Slot Count
SLC
If the credit-based shaper algorithm is enabled, then you can program the number of slots (of duration
programmed in DMA_CH(#i)_Slot_Interval register) over which the average transmitted bits per slot,
provided in the MTL_TxQ(#i)_ETS_Status register, is computed for queue. The encoding is as follows:
000b - 1 slot
001b - 2 slots
010b - 4 slots
011b - 8 slots
100b - 16 slots
101b - Reserved
3 Credit Control
CC Indicates whether the credit control is enabled.
When this field is 1, it indicates that the accumulated credit parameter in the credit-based shaper algorithm
logic is not reset to zero, when there is positive credit and no packet to transmit in channel 1. The credit
accumulates even when no packet is waiting in channel 1 and another channel is transmitting.
When this field becomes 0, it indicates that the accumulated credit parameter in the credit-based shaper
algorithm logic sets to zero, when there is positive credit and no packet to transmit in channel 1. No credit
accumulates when no packet is waiting in channel 1 and other channel is transmitting.
0b - Disabled
1b - Enabled
2 AV Algorithm
AVALG Indicates whether the CBS algorithm is enabled.
If you program queue 1 for AV, this field configures the scheduling algorithm for this queue.
When this field is 1, it indicates that the credit based shaper algorithm (CBS) is selected for queue 1 traffic.
If this field becomes 0, strict priority is selected.
0b - Disabled
1b - Enabled
1-0 Reserved.
—

---

*Page 3017*

Ethernet Media Access Controller (EMAC)

#### 72.17.229 MTL Tx Queue 1 ETS Status (MTL_TxQ1_ETS_Status)

Offset
Register Offset
MTL_TxQ1_ETS_Status D54h
Function
Provides the average traffic transmitted in queue 1.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R ABS
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R ABS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 Reserved.
—
23-0 Average Bits per Slot
ABS Contains the average transmitted bits per slot.
Computes this field over number of slots programmed in the MTL_TxQ(#i)_ETS_CONTROL register's SLC
field of, if you enables AV operation. This field's maximum value is 0x6_4000 in 100 Mbit/s, 0x3E_8000 in
1000 Mbit/s and 9C_4000 in 2500 Mbit/s mode respectively.
Computes this field over every 10 million bit times slot (4 ms in 2500 Mbit/s; 10 ms in 1000 Mbit/s; 100 ms
in 100 Mbit/s) when you enables DCB operation for queue. The maximum value is 0x989680.

#### 72.17.230 MTL Tx Queue 1 Quantum Weight (MTL_TxQ1_Quantum_Weight)

Offset
Register Offset
MTL_TxQ1_Quantum_W D58h
eight

---

*Page 3018*

Ethernet Media Access Controller (EMAC)
Function
Provides the average traffic transmitted in queue 1.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved ISCQW
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
ISCQW
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-21 Reserved.
—
20-0 idleSlopeCredit, Quantum or Weights
ISCQW idleSlopeCredit
Contains the idleSlopeCredit value required for the credit-based shaper algorithm for queue 1, when you
enables the AV feature. This is the rate of change of credit in bits per cycle (40 ns for 100 Mbit/s; 8 ns for 1000
Mbit/s; 3.2 ns for 2500 Mbit/s) when the credit is increasing. You must program this field with computed credit
in bits per cycle scaled by 1,024. The maximum value is portTransmitRate, that is, 0x2000 in 1000/2500
Mbit/s mode and 0x1000 in 100 Mbit/s mode. Bits[20:14] must be written to zero.
Quantum
Contains the quantum value in bytes to be added to credit during every queue scanning cycle, when
you enables the DCB operation with DWRR algorithm for queue 1 traffic. The maximum value is
0x1312D0 bytes.
Weights
Contains the weight for this queue, when you enables the DCB operation with WFQ algorithm for queue 1
traffic. The maximum value is 0x3FFF where weight of 0 indicates 100% bandwidth. Bits[20:14] must be
written to zero.
Contains the weight for this queue, when you enables the DCB operation or generic queuing operation with
WRR algorithm for queue 1 traffic. The maximum value is 0x64.
Bits [20:7] must be written to zero.
Table continues on the next page...

---

*Page 3019*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
NOTE
In multiple Queue configuration you must program this field in respective per queue register
to some non-zero value when multiple queues are enabled or single queue other than Q0
is enabled. You must not program this field when only Q0 is enabled. In general, you must
program a non-zero value on both receive and transmit when WRR algorithm is selected.
In receive, the register is MTL_Operation_Mode register.
NOTE
For WFQ algorithm, higher the programmed weights lesser the bandwidth allocated for that
transmit queue. The finish time is not a function of particular packet alone but it is according
to the formula: (previous_finish_time of particular Transmit Queue + (weights*packet_size))
NOTE
The programmed weights do not correspond to the number of packets but the fraction of
bandwidth or time allocated for particular queue w.r.t. total BW or time.

#### 72.17.231 MTL Tx Queue 1 Sendslope Credit (MTL_TxQ1_SendSlopeCredit)

Offset
Register Offset
MTL_TxQ1_SendSlopeC D5Ch
redit
Function
Contains the sendSlope credit value required for the credit-based shaper algorithm for the queue.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved SSC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 3020*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-14 Reserved.
—
13-0 sendSlopeCredit Value
SSC Contains the sendSlopeCredit value required for credit-based shaper algorithm for Queue 1, when you
enables the AV operation. This is the rate of change of credit in bits per cycle (40 ns, 8 ns and 3.2 ns for
100 Mbit/s, 1000 Mbit/s and 2500 Mbit/s respectively) when the credit is decreasing. You must program
this field with computed credit in bits per cycle scaled by 1,024. The maximum value is portTransmitRate,
that is, 0x2000 in 1000/2500 Mbit/s mode and 0x1000 in 100 Mbit/s mode. You must program this field with
absolute sendSlopeCredit value. The credit-based shaper logic subtracts it from the accumulated credit
when channel 1 is selected for transmission.

#### 72.17.232 MTL Tx Queue 1 HiCredit (MTL_TxQ1_HiCredit)

Offset
Register Offset
MTL_TxQ1_HiCredit D60h
Function
Contains the hiCredit value required for the credit-based shaper algorithm for the queue.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved HC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
HC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-29 Reserved.
—
Table continues on the next page...

---

*Page 3021*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
28-0 hiCredit Value
HC Contains the hiCredit value required for the credit-based shaper algorithm, when you enables the AV
feature. This is the maximum value that you can accumulate in the credit parameter. This is specified in bits
scaled by 1,024.
The maximum value is maxInterferenceSize, that is, best-effort maximum packet size (16,384 bytes or
131,072 bits). The specified value is 131,072 * 1,024 = 134,217,728 or 0x0800_0000.

#### 72.17.233 MTL Tx Queue 1 LoCredit (MTL_TxQ1_LoCredit)

Offset
Register Offset
MTL_TxQ1_LoCredit D64h
Function
Contains the loCredit value required for the credit-based shaper algorithm for the queue.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved LC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
LC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-29 Reserved.
—
28-0 loCredit Value
LC Contains the loCredit value required for the credit-based shaper algorithm, when you enables the AV
operation. This is the minimum value that you can accumulate in the credit parameter. This is specified in bits
scaled by 1,024. The maximum value you can program corresponds to twice the maxFrameSize this queue
transmits. If the maxFrameSize is 8192 bytes, then (8192*2) * 8 * 1024 = 134,217,728 or 0x0800_0000. The
programmed value is 2's complement of the value, that is, 0x1800_0000 because it is a negative value.

---

*Page 3022*

Ethernet Media Access Controller (EMAC)

#### 72.17.234 MTL Queue 1 Interrupt Control Status (MTL_Q1_Interrupt_Control_Status)

Offset
Register Offset
MTL_Q1_Interrupt_Contr D6Ch
ol_Status
Function
Contains the interrupt enable and status bits for the queue 1 interrupts.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
RXOV
Reserved RXOIE Reserved
FIS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
ABPSI ABPSI TXUN
Reserved TXUIE Reserved
E S FIS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-25 Reserved.
—
24 Receive Queue Overflow Interrupt Enable
RXOIE Enables or disables the receive queue overflow interrupt.
When this field is 1, it enables the receive queue overflow interrupt.
When this field becomes 0, it disables the receive queue overflow interrupt.
0b - Disable
1b - Enable
23-17 Reserved.
—
16 Receive Queue Overflow Interrupt Status
RXOVFIS Indicates whether the status of receive queue overflow interrupt is detected.
Indicates that the receive queue had an overflow when it receives the packet. If you transfers a partial packet
to the application, the overflow status sets in RDES3[21]. This field is 0 when the application writes 1 to it.
Table continues on the next page...

---

*Page 3023*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
Access restriction apply to this field. It automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
15-10 Reserved.
—
9 Average Bits Per Slot Interrupt Enable
ABPSIE Indicates whether an average bits per slot interrupt is enabled.
When this field is 1, it indicates that the MAC asserts the sbd_intr_o or mci_intr_o interrupt when you update
the average bits per slot status.
When this field is 0, it indicates that the interrupt is not asserted for such an event.
0b - Disabled
1b - Enabled
8 Transmit Queue Underflow Interrupt Enable
TXUIE Enables or disables the transmit queue underflow interrupt.
When this field is 1, it enables the transmit queue underflow interrupt.
When this field becomes 0, it disables the transmit queue underflow interrupt.
0b - Disabled
1b - Enabled
7-2 Reserved.
—
1 Average Bits Per Slot Interrupt Status
ABPSIS Indicates whether the average bits per slot interrupt status is detected.
When this field is 1, it indicates that the MAC has updated the ABS value. This field is 0 when the application
writes 1 to it.
Access restriction apply to this field. It automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected
0 Transmit Queue Underflow Interrupt Status
TXUNFIS Indicates whether the transmit queue underflow interrupt status is detected.
Indicates that the transmit queue had an underflow when you transmits the packet. Suspends the
transmission and sets an underflow error TDES3[2]. This field is 0 when the application writes 1 to this field.
Table continues on the next page...

---

*Page 3024*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
Access restriction apply to this field. It automatically becomes 1 on an internal event occurrence. Writing 1
clears this field and writing 0 has no effect.
0b - Not detected
1b - Detected

#### 72.17.235 MTL Rx Queue 1 Operation Mode (MTL_RxQ1_Operation_Mode)

Offset
Register Offset
MTL_RxQ1_Operation_ D70h
Mode
Function
Establishes the receive queue operating modes and command.
The MTL_RxQ1_Operation_Mode[RFA] and MTL_RxQ1_Operation_Mode[RFD] are not backward compatible with the RFA and
RFD fields of 4.00a release.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved RQS Reserved RFD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
DIS_T Reserv
RFD Reserved RFA EHFC RSF FEP FUP RTC
CP ... ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-25 Reserved.
—
24-20 Receive Queue Size
RQS Indicates the size of the allocated receive queues in blocks of 256 bytes.
MTL_RxQ1_Operation_Mode[RQS] is read-write only if the number of receive queues is more than one, the
Table continues on the next page...

---

*Page 3025*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
reset value is 0x0 and indicates 256 bytes size. This means that value of 0x0 = 256 bytes, 0x1 = 512 bytes
and so on. You must program RQS [5:0] = 6'b001111 to allocate queue size of 4096 (4K) bytes. In general,
the size of the Queue = (RQS+1)*256 bytes.
When the number of receive queues is one, the field is read-only and the reset value reflects the configured
RX FIFO size in blocks of 256 bytes.
The field width depends on the receive memory size selected in your configuration. For example, if the
memory size is 2048, the field width is 3 bits:
LOG2(2048/256) = LOG2(8) = 3 bits
19-18 Reserved.
—
17-14 Threshold for Deactivating Flow Control (in half-duplex and full-duplex modes)
RFD Controls the threshold (fill-level of Rx queue) at which the flow control de-asserts after activation
0 - Full minus 1 KB, that is, FULL 1 KB
1 - Full minus 1.5 KB, that is, FULL 1.5 KB
2 - Full minus 2 KB, that is, FULL 2 KB
3 -Full minus 2.5 KB, that is, FULL 2.5 KB
...
14 - Full minus 8 KB, that is, FULL 8 KB
15 - Full minus 8.5 KB, that is, FULL 8.5 KB
The de-assertion is effective only when flow control asserts.
NOTE
Program the value in such a way that the threshold is a positive number.
When MTL_RxQ1_Operation_Mode[EHFC] = 1, these values are applicable only when the receive queue
size which MTL_RxQ1_Operation_Mode[RQS] determines, is equal to or greater than 4 KB.
For a given queue size, the values ranges between 0 and the encoding for FULL minus (QSIZE - 0.5
KB) and all other values are illegal. Here the term FULL and QSIZE refers to the queue size which
MTL_RxQ1_Operation_Mode[RQS] determines.
The field width depends on RX FIFO size selected during the configuration. Remaining bits are reserved and
read only.
13-12 Reserved.
—
11-8 Threshold for Activating Flow Control (in half-duplex and full-duplex
RFA Controls the threshold (fill-level of Rx queue) at which the flow control activates.
See MTL_RxQ1_Operation_Mode[RFD] , for more information on encoding for this field.
Table continues on the next page...

---

*Page 3026*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
7 Enable Hardware Flow Control
EHFC Enables or disables hardware flow control.
If this field is 1, it enables the flow control signal operation, based on the receive queue's fill-level.
When this field becomes 0, it disables the flow control operation.
0b - Disable
1b - Enable
6 Disable Dropping of TCP or IP Checksum Error Packets
DIS_TCP_EF Indicates whether the dropping of TCP or IP checksum error packets is enabled.
When this field is 1, it indicates that the MAC does not drop the packets which only have the errors that the
receive checksum offload engine detects. Such packets have errors only in the encapsulated payload. MAC
receives an Ethernet packet in which there are no errors (including FCS error).
When this field becomes 0, it indicates that all the error packets drop if
MTL_RxQ1_Operation_Mode[FEP] resets.
0b - Enabled
1b - Disabled
5 Receive Queue Store and Forward
RSF Indicates whether the receive queue store and forward is enabled.
When this field is 1, it indicates that the module reads a packet from the receive queue only after the
complete packet is written to it and ignores MTL_RxQ1_Operation_Mode[RTC] .
When this field becomes 0, it indicates that the receive queue operates in the Threshold (cut-through) mode,
subject to the threshold which MTL_RxQ1_Operation_Mode[RTC] specifies.
0b - Disabled
1b - Enabled
4 Forward Error Packets
FEP Indicates whether the forward error packets are enabled.
If this field becomes 0, it indicates that the receive queue drops the packets with an error status (CRC
error, GMII_ER, watchdog timeout, or overflow). However, the packet does not drops, if the start byte (write)
pointer of a packet is already transferred to the read controller side in Threshold mode.
When this field is 1, it indicates that all the packets except the runt error packets forward to the
application or DMA. If MTL_RxQ1_Operation_Mode[RSF] is 1 and the receive queue overflows when
a partial packet is written, the packet drops irrespective of the setting of this field. However, if
MTL_RxQ1_Operation_Mode[RSF] resets and the receive queue overflows when a partial packet is written,
you may forward a partial packet to the application or DMA.
0b - Disabled
1b - Enabled
3 Forward Undersized Good Packets
Table continues on the next page...

---

*Page 3027*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
FUP Indicates whether the forward undersized good packets are enabled.
When this field is 1, it indicates that the receive queue forwards the undersized good packets (packets with
no error and length less than 64 bytes), including pad-bytes and CRC.
When this field becomes 0, it indicates that the receive queue drops all the packets of less than 64 bytes,
unless a packet is already transferred because of the lower value of Rx Threshold, for example, RTC = 01.
0b - Disabled
1b - Enabled
2 Reserved.
—
1-0 Receive Queue Threshold Control
RTC Controls the threshold level of the MTL Rx queue (in bytes):
The received packet is transferred to the application or DMA when the packet size within the MTL
receive queue is larger than the threshold. Also, the full packets with length less than the threshold are
automatically transferred.
This field is valid only when MTL_RxQ1_Operation_Mode[RSF] = 0.
This field is ignored when MTL_RxQ1_Operation_Mode[RSF] = 1.
00b - 64
01b - 32
10b - 96
11b - 128

#### 72.17.236 MTL Rx Queue 1 Missed Packet Overflow Counter

#### (MTL_RxQ1_Missed_Packet_Overflow_Cnt)

Offset
Register Offset
MTL_RxQ1_Missed_Pac D74h
ket_Overflow_Cnt
Function
Contains the counter for packets missed because of receive queue packet flush and packets discarded because of receive
queue overflow.

---

*Page 3028*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
MISCN
R MISPKTCNT
TO ...
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
OVFC
R OVFPKTCNT
NTO ...
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-28 Reserved.
—
27 Missed Packet Counter Overflow Bit
MISCNTOVF Indicates whether the missed packet counter overflow is detected.
When this field is 1, it indicates that the receive queue missed packet counter has crossed the
maximum limit.
Access restriction apply to this field. It clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
26-16 Missed Packet Counter
MISPKTCNT Indicates the number of packets the module has missed because the application asserted ari_pkt_flush_i[]
for this queue. This counter resets when this register is read with mci_be_i[0] at 1b1.
This counter increases by 1 when the DMA discards the packet because of buffer unavailability.
Access restriction apply to this field. It clears on read and automatically becomes 1 on an internal
event occurrence.
15-12 Reserved.
—
11 Overflow Counter Overflow Bit
OVFCNTOVF Indicates whether the counter overflow status is detected.
When this field is 1, it indicates that the receive queue overflow packet counter field has crossed the
maximum limit.
Table continues on the next page...

---

*Page 3029*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
Access restriction apply to this field. It clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
10-0 Overflow Packet Counter
OVFPKTCNT
Indicates the number of packets which the module discards because of receive queue overflow. This
counter increments each time the module discards an incoming packet because of overflow. It resets when
this register is read with mci_be_i[0] at 1'b1.
Access restriction apply to this field. It clears on read and automatically becomes 1 on an internal
event occurrence.

#### 72.17.237 MTL Rx Queue 1 Debug (MTL_RxQ1_Debug)

Offset
Register Offset
MTL_RxQ1_Debug D78h
Function
Provides the debug status of various blocks related to the receive queue.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R PRXQ
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
RWCS
R RXQSTS RRCSTS
Reserv
TS
Reserved
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-30 Reserved.
Table continues on the next page...

---

*Page 3030*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
—
29-16 Number of Packets in Receive Queue
PRXQ Indicates the current number of packets in the receive queue. The theoretical maximum field value is
256KB/16B = 16K packets, that is, Max_Queue_Size/Min_Packet_Size.
15-6 Reserved.
—
5-4 MTL Rx Queue Fill-Level Status
RXQSTS Provides the status of the receive queue fill-level.
00b - Rx Queue empty
01b - Rx Queue fill-level below flow-control deactivate threshold
10b - Rx Queue fill-level above flow-control activate threshold
11b - Rx Queue full
3 Reserved.
—
2-1 MTL Rx Queue Read Controller State
RRCSTS Provides the state of the receive queue read controller.
00b - Idle state
01b - Reading packet data
10b - Reading packet status (or timestamp)
11b - Flushing the packet data and status
0 MTL Rx Queue Write Controller Active Status
RWCSTS Indicates whether the MTL receive queue write controller active status is detected.
When this field is 1, it indicates that the MTL receive queue write controller is active, and it is transferring a
received packet to the receive queue.
0b - Not detected
1b - Detected

#### 72.17.238 MTL Rx Queue 1 Control (MTL_RxQ1_Control)

Offset
Register Offset
MTL_RxQ1_Control D7Ch

---

*Page 3031*

Ethernet Media Access Controller (EMAC)
Function
Controls the receive arbitration and passing of received packets to the application.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
RXQ_F
Reserved RXQ_WEGT
RM ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-4 Reserved.
—
3 Receive Queue Packet Arbitration
RXQ_FRM_AR Indicates whether the receive queue packet arbitration is enabled.
BIT
When this field is 1, it indicates that the module drives the packet data to the ARI interface such that the entire
packet data of currently-selected queue transmits before switching to other queue.
When this field becomes 0, it indicates that the module drives the packet data to the ARI interface such that
the following amount of data of currently-selected queue transmits before switching to other queue:
• PBL amount of data (indicated by ari_qN_pbl_i[]), or
• Complete data of a packet
The status and the timestamp are not a part of the PBL data. Therefore, the module drives the complete
status (including timestamp status) during the packet's first PBL request (in store-and-forward mode) or the
last PBL request (in Threshold mode).
0b - Disabled
1b - Enabled
2-0 Receive Queue Weight
RXQ_WEGT Indicates the weight assigned to the receive queue 0. Program this field with one value less than the required
weight, that is reset value of 0 indicates weight of 1, value of 1 indicates weight of 2, and so on. The weight is
used as the number of continuous PBL or packets requests (depending on the RXQ_FRM_ARBIT) allocated
to the queue in one arbitration cycle.
Note: The change in value of RXQ_WEGT takes effect only after the completion of current service round or
when there is change from RAA=SP to RAA=WSP algorithm. This approach is taken so that there is smooth
transition. For the RXQ_WEGT value to take effect at the start, the MTL_RxQ(#i)_Control registers must be
programmed before the MTL_Operation_Mode register.

---

*Page 3032*

Ethernet Media Access Controller (EMAC)

#### 72.17.239 DMA Mode (DMA_Mode)

Offset
Register Offset
DMA_Mode 1000h
Function
Establishes the bus operating modes for the DMA.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved INTM
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserv Reserv Reserv
PR TXPR ARBC Reserved TAA DA SWR
ed ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-18 Reserved.
—
17-16 Interrupt Mode
INTM Defines the interrupt mode of module.
The behavior of the following outputs changes depending on these settings:
sbd_perch_tx_intr_o[] (Transmit per channel interrupt)
sbd_perch_rx_intr_o[] (Receive per channel interrupt)
sbd_intr_o (Common interrupt)
It also changes the behavior of the RI/TI bits in DMA_CH0_Status .
00 - sbd_perch_* are pulse signals for each TX/RX packet transfer completion events (irrespective of
whether corresponding interrupts are enabled) for which IOC bits are enabled in descriptor. sbd_intr_o
is also asserted when corresponding interrupts are enabled and cleared only when software clears the
corresponding RI/TI status bits.
01 - sbd_perch_* are level signals asserted on TX/RX packet transfer completion event when corresponding
interrupts are enabled and de-asserted when the software clears the corresponding RI/TI status bits. The
sbd_intr_o is not asserted for these TX/RX packet transfer completion events.
Table continues on the next page...

---

*Page 3033*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
10 - sbd_perch_* are level signals asserted on TX/RX packet transfer completion event when corresponding
interrupts are enabled and de-asserted when the software clears the corresponding RI/TI status bits.
However, the signal is asserted again if the same event occurred again before it was cleared. The sbd_intr_o
is not asserted for these TX/RX packet transfer completion events.
11 - Reserved
See table "Transfer Complete Interrupt Behavior" for more information.
00b - See above description
01b - See above description
10b - See above description
11b - Reserved
15 Reserved.
—
14-12 Priority Ratio
PR Controls the priority ratio in weighted round-robin arbitration between the Rx DMA and Tx DMA. These
fields are valid only when DMA_Mode[DA] resets. The priority ratio is Rx:Tx or Tx:Rx depending on whether
DMA_Mode[TXPR] = 1 or it resets.
000b - The priority ratio is 1:1
001b - The priority ratio is 2:1
010b - The priority ratio is 3:1
011b - The priority ratio is 4:1
100b - The priority ratio is 5:1
101b - The priority ratio is 6:1
110b - The priority ratio is 7:1
111b - The priority ratio is 8:1
11 Transmit Priority
TXPR Indicates whether the transmit priority is enabled.
When this field is 1, it indicates that the Tx DMA has higher priority than the Rx DMA during arbitration for
the system-side bus.
0b - Disabled
1b - Enabled
10 Reserved.
—
9 Is reserved for NXP internal use. This field must always be 0 unless instructed by NXP. Writing 1 to this field
may cause unexpected behavior.
ARBC
Table continues on the next page...

---

*Page 3034*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - NXP reserved field disabled
1b - NXP reserved field enabled up on NXP request
8 Reserved.
—
7-5 Reserved.
—
4-2 Transmit Arbitration Algorithm
TAA Selects the arbitration algorithm for the transmit side when you select multiple transit DMAs.
000b - Fixed priority (Channel 0 has the lowest priority and the last channel has the highest
priority)
001b - Weighted Strict Priority (WSP)
010b - Weighted Round-Robin (WRR)
011b - Reserved (for 3'b011 to 3'b111)
1 DMA Tx or Rx Arbitration Scheme
DA Specifies the arbitration scheme between the transmit and receive paths of all channels.
0 - Weighted round-robin with Rx:Tx or Tx:Rx
The priority between the paths is according to the priority specified in bits[14:12] and the priority weight is
specified in DMA_Mode[TXPR] .
1 - Fixed Priority
The transit path has priority over the receive path when DMA_Mode[TXPR] = 1. Otherwise, the receive path
has priority over the transit path.
0b - Weighted Round-Robin with Rx:Tx or Tx:Rx
1b - Fixed Priority
0 Software Reset
SWR Indicates whether the software reset is enabled.
When this field is 1, it indicates that the MAC and the DMA controller resets the logic and all internal registers
of the DMA, MTL, and MAC. This field automatically clears after the reset operation completes in all module
clock domains. A value of zero should be read in this field before reprogramming any module register.
When this field is 1, it indicates that this field must read at least 4 CSR clock cycles.
NOTE
The reset operation completes only when all resets in all active clock domains de-assert.
Therefore, to complete software reset, it is essential that all PHY input clocks (applicable for
the selected PHY interface) are present. The time to complete the software reset operation
depends on the frequency of the slowest active clock.
Table continues on the next page...

---

*Page 3035*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
Access restriction apply to this field. It clears automatically. Writing 1 sets this field and writing 0 has
no effect.
0b - Disabled
1b - Enabled

#### 72.17.240 DMA System Bus Mode (DMA_SysBus_Mode)

Offset
Register Offset
DMA_SysBus_Mode 1004h
Function
Controls the behavior of the AHB or AXI master. It mainly controls burst splitting and the number of outstanding requests.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv Reserv
Reserved Reserved Reserved Reserved
ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv
RB MB AAL Reserved FB
ed ed ed ed ed ed ed ed ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Reserved.
—
30 Reserved.
—
29-26 Reserved.
—
Table continues on the next page...

---

*Page 3036*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
25-24 Reserved.
—
23-18 Reserved.
—
17-16 Reserved.
—
15 Rebuild INCRx Burst
RB Indicates whether the rebuild INCRx burst is enabled.
When this field is 1 and the AHB master gets SPLIT, RETRY, or Early Burst Termination (EBT) response,
it indicates that the AHB master interface rebuilds the pending beats of any initiated burst transfer with
INCRx and SINGLE transfers. By default, the AHB master interface rebuilds pending beats of an EBT with
an unspecified (INCR) burst.
0b - Disabled
1b - Enabled
14 Mixed Burst
MB Indicates whether the mixed burst is enabled.
When this field is 1 and DMA_SysBus_Mode[FB] is 0, it indicates that the AHB master performs an
undefined bursts transfers (INCR) for burst length of 16 or more. The AHB master performs fixed burst
transfers (INCRx and SINGLE), for burst length of 16 or less.
0b - Disabled
1b - Enabled
13 Reserved.
—
12 Address-Aligned Beats
AAL Indicates whether the address-aligned beats are enabled.
When this field is 1, it indicates that the EQOS-AXI or EQOS-AHB master performs address-aligned burst
transfers on read and write channels.
0b - Disabled
1b - Enabled
11 Reserved.
—
10 Reserved.
—
Table continues on the next page...

---

*Page 3037*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
9-8 Reserved.
—
7 Reserved.
—
6 Reserved.
—
5 Reserved.
—
4 Reserved.
—
3 Reserved.
—
2 Reserved.
—
1 Reserved.
—
0 Fixed Burst Length
FB Indicates whether the fixed burst length is enabled.
When this field is 1, it indicates that the AHB master initiates burst transfers of specified length (INCRx
or SINGLE).
When this field is 0, it indicates that the AHB master initiates transfers of unspecified length (INCR) or
SINGLE transfers.
0b - Disabled
1b - Enabled

#### 72.17.241 DMA Interrupt Status (DMA_Interrupt_Status)

Offset
Register Offset
DMA_Interrupt_Status 1008h

---

*Page 3038*

Ethernet Media Access Controller (EMAC)
Function
The application reads this Interrupt Status register during interrupt service routine or polling to determine the interrupt status of
DMA channels, MTL queues, and the MAC.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R MACIS MTLIS
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DC1IS DC0IS
Reserv Reserv Reserv Reserv Reserv Reserv
Reserved
ed ed ed ed ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-18 Reserved.
—
17 MAC Interrupt Status
MACIS Indicates whether the MAC interrupt status is detected.
You must read the corresponding register in the MAC to get the exact cause of the interrupt and clear its
source, to reset this field to 1'b0.
0b - Not detected
1b - Detected
16 MTL Interrupt Status
MTLIS Indicates whether the MTL interrupt status is detected.
You must read the corresponding register in the MTL to get the exact cause of the interrupt and clear its
source, to reset this field to 1'b0.
0b - Not detected
1b - Detected
15-8 Reserved.
—
7 Reserved.
—
6 Reserved.
Table continues on the next page...

---

*Page 3039*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
—
5 Reserved.
—
4 Reserved.
—
3 Reserved.
—
2 Reserved.
—
1 DMA Channel 1 Interrupt Status
DC1IS Indicates whether the DMA channel 1 interrupt status is detected.
You must read the corresponding register in DMA channel 1 to get the exact cause of the interrupt and clear
its source, to reset this field to 1'b0.
0b - Not detected
1b - Detected
0 DMA Channel 0 Interrupt Status
DC0IS Indicates whether the DMA channel 0 interrupt status is detected.
You must read the corresponding register in DMA channel 0 to get the exact cause of the interrupt and clear
its source, to reset this field to 1'b0.
0b - Not detected
1b - Detected

#### 72.17.242 DMA Debug Status 0 (DMA_Debug_Status0)

Offset
Register Offset
DMA_Debug_Status0 100Ch
Function
Provides the receive and transmit process status for DMA Channel 0-Channel 2 for debugging purpose.

---

*Page 3040*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TPS1 RPS1
Reserved Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
AXWH
R TPS0 RPS0
Reserv
STS
Reserved
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-28 Reserved.
—
27-24 Reserved.
—
23-20 DMA Channel 1 Transmit Process State
TPS1 Indicates the transmit DMA FSM state for channel 1.
The MSB of this field always returns 0. This field does not generate an interrupt.
0000b - Stopped (Reset or stop transmit command issued)
0001b - Running (Fetching transmit transfer descriptor)
0010b - Running (Waiting for status)
0011b - Running (Reading data from system memory buffer and queuing it to the transmit buffer
(Tx FIFO))
0100b - Timestamp write state
0101b - Reserved for future use
0110b - Suspended (Transmit descriptor unavailable or transmit buffer underflow)
0111b - Running (Closing transmit descriptor)
19-16 DMA Channel 1 Receive Process State
RPS1 Indicates the receive DMA FSM state for channel 1.
The MSB of this field always returns 0. This field does not generate an interrupt.
0000b - Stopped (Reset or Stop receive command issued)
0001b - Running (Fetching receive transfer descriptor)
0010b - Reserved for future use
Table continues on the next page...

---

*Page 3041*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0011b - Running (Waiting for receive packet)
0100b - Suspended (Receive descriptor unavailable)
0101b - Running (Closing the receive descriptor)
0110b - Timestamp write state
0111b - Running (Transferring the received packet data from the receive buffer to the system
memory)
15-12 DMA Channel 0 Transmit Process State
TPS0 Indicates the transmit DMA FSM state for channel 0.
The MSB of this field always returns 0. This field does not generate an interrupt.
0000b - Stopped (Reset or stop transmit command issued)
0001b - Running (Fetching transmit transfer descriptor)
0010b - Running (Waiting for status)
0011b - Running (Reading data from system memory buffer and queuing it to the transmit buffer
(Tx FIFO))
0100b - Timestamp write state
0101b - Reserved for future use
0110b - Suspended (Transmit descriptor unavailable or transmit buffer underflow)
0111b - Running (Closing transmit descriptor)
11-8 DMA Channel 0 Receive Process State
RPS0 Indicates the receive DMA FSM state for channel 0.
The MSB of this field always returns 0. This field does not generate an interrupt.
0000b - Stopped (Reset or stop receive command issued)
0001b - Running (Fetching receive transfer descriptor)
0010b - Reserved for future use
0011b - Running (Waiting for receive packet)
0100b - Suspended (Receive descriptor unavailable)
0101b - Running (Closing the receive descriptor)
0110b - Timestamp write state
0111b - Running (Transferring the received packet data from the receive buffer to the system
memory)
7-2 Reserved.
—
1 Reserved.
Table continues on the next page...

---

*Page 3042*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
—
0 AHB Master Status
AXWHSTS Indicates whether the AXI master write channel or AHB master status is detected.
When this field is 1, it indicates that the AHB master FSMs are in the non-idle state.
0b - Not detected
1b - detected

#### 72.17.243 DMA TBS Control (DMA_TBS_CTRL)

Offset
Register Offset
DMA_TBS_CTRL 1050h
Function
Controls the TBS attributes.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
FTOS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserv
FTOS FGOS Reserved FTOV
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-8 Fetch Time Offset
FTOS Deduct the value in units of 256 nanoseconds, from the launch time to compute the fetch Time.
Max value: 999,999,999 nanosecond, must be smaller than CTR-1 value when ESTM mode = 1, because
this value is a modulo CTR value.
7 Reserved.
Table continues on the next page...

---

*Page 3043*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
—
6-4 Fetch GSN Offset
FGOS Deduct the number GSN slots from the launch GSN to compute the fetch GSN. The value is valid only when
DMA_TBS_CTRL[FTOV] = 1.
3-1 Reserved.
—
0 Fetch Time Offset Valid
FTOV Indicates whether the fetch time offset is valid.
When this field is 1, it indicates that DMA_TBS_CTRL[FTOS] is valid. When this field is not 1, it indicates
that the fetch offset is not valid and the DMA engine can fetch the frames from host memory without any
time restrictions.
0b - Invalid
1b - Valid

#### 72.17.244 DMA Safety Interrupt Status (DMA_Safety_Interrupt_Status)

Offset
Register Offset
DMA_Safety_Interrupt_St 1080h
atus
Function
Indicates summary (whether error occurred in DMA/MTL/MAC and correctable/uncorrectable) of the automotive safety related
error interrupts.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R MCSIS MSUIS MSCIS
Reserv
Reserved
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 3044*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31 MAC Safety Uncorrectable Interrupt Status
MCSIS Indicates whether the MAC safety uncorrectable interrupt status is detected.
Indicates that an uncorrectable safety related interrupt is 1 in the MAC module. When this field is 1, it
indicates that you must read the MAC_DPP_FSM_Interrupt_Status , to get the cause of the safety interrupt
in MAC.
0b - Not detected
1b - Detected
30 Reserved.
—
29 MTL Safety Uncorrectable Error Interrupt Status
MSUIS Indicates whether the MTL safety uncorrectable error interrupt status is detected.
Indicates an uncorrectable error interrupt event in MTL. To get exact cause of the interrupt you must read
the MTL_Safety_Interrupt_Status .
0b - Not detected
1b - Detected
28 MTL Safety Correctable Error Interrupt Status
MSCIS Indicates whether the MTL safety correctable error interrupt status is detected.
Indicates a correctable error interrupt event in MTL. To get the exact cause of the interrupt you must read
the MTL_Safety_Interrupt_Status .
0b - Not detected
1b - Detected
27-0 Reserved.
—

#### 72.17.245 DMA Channel 0 Control (DMA_CH0_Control)

Offset
Register Offset
DMA_CH0_Control 1100h
Function
Specifies the MSS value for segmentation, length to skip between two descriptors, and also the features such as header
splitting and 8xPBL mode.

---

*Page 3045*

Ethernet Media Access Controller (EMAC)
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
—
20-18 Descriptor Skip Length
DSL Specifies the Word, Dword, or Lword number (depending on the 32-bit, 64-bit, or 128-bit bus) to skip
between two unchained descriptors. The address skipping starts from the end of the current descriptor to
the start of the next descriptor.
The DMA assumes the descriptor table as contiguous, when the DSL value is equal to zero.
17 Reserved.
—
16 8xPBL mode
PBLx8 Indicates whether 8xPBL mode is enabled.
When this field is 1, it indicates that the PBL value programmed in Bits[21:16] in DMA_CH(#i)_Tx_Control
and Bits[21:16] in DMA_CH(#i)_Rx_Control is multiplied by eight times. Therefore, the DMA transfers the
data in 8, 16, 32, 64, 128, and 256 beats depending on the PBL value.
0b - Disabled
1b - Enabled
15-14 Reserved.
—
Table continues on the next page...

---

*Page 3046*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
13-0 Reserved.
—

#### 72.17.246 DMA Channel Tx Control (DMA_CH0_Tx_Control)

Offset
Register Offset
DMA_CH0_Tx_Control 1104h
Function
Controls the transmit features such as PBL, TCP segmentation, and transmit channel weights.
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
When this field is 1, it indicates that the corresponding channel uses 32 bytes enhanced descriptors for both
normal and context descriptors.
When this field becomes 0, it indicates that the corresponding channel uses the 16 bytes descriptors.
0b - Disabled
1b - Enabled
Table continues on the next page...

---

*Page 3047*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
27-24 Reserved.
—
23 Reserved.
—
22 Early Transmit Interrupt Control
ETIC Indicates whether an early transmit interrup