<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 62 -->

# Chapter 62

# Trigger MUX (TRGMUX)

#### 62.1 Chip-specific TRGMUX information

#### 62.1.1 TRGMUX input output configuration and instances

This chip has 1 instance of TRGMUX module.
The device supports the triggering scheme between peripherals. For the supported trigger sources and destination, see the
TRGMUX connectivity file attached to this document.
This device has 16 pads (SIUL2) mapped from TRGMUX inputs and TRGMUX outputs are mapped to the eMIOS channels, hence
two timers channels can use a single pin of the device to do input capture.
While using TRGMUX, below points need to be taken care:
• User must ensure the minimum pulse of 100 ns on SIUL2 pads when using them as trigger source on TRGMUX.
• Pulses which are visible on pads depends on the pad type. Different pad supports different frequencies. For more details on
pad types and their respective bandwidth, see section "IO signal table" in "Signal Multiplexing" chapter.
• End of conversion (EOC) signals of ADC modules are mapped on TRGMUX inputs as trigger sources. The EOC signal is
asserted after ADC conversion regardless whether conversion is signaled by polling flags, interrupt or DMA transfer. The
signal shall not be used to start injected conversion on same ADC channel as it will overwrite current result register.
• The minimum pulse length requirement is 1.5X of destination clock, so that it gets properly sampled at the destination IP
connected to TRGMUX outputs, otherwise there are chances of missing the triggers generated from source. For example, the
trigger generated from PAD for ADC conversion should be kept high/low for at least 1.5X of ADC clock so that it gets sampled
in ADC clock domain. To ensure this, pulse strechers have been placed before some of the hardware modules mapped on
TRGMUX outputs.
• Some PADS are being shared by both ADC and TRGMUX. It is recommended that trigger initiated from such PADS should
not be used to trigger a conversion on the ADC channel mapped on same PAD. Failing to do this will cause congestion on
same PAD.
• Out of all the pads mapped on TRGMUX, first four pads have glitch filters. For details, see the TRGMUX connectivity
file attached to this document. The trigger pulse width should honour the pulse width requirement as per Glitch Filter
specifications. The same signal can be observed at output pin if the pulse width of the input data signal is more than 400ns
and no output signal should be observed if the pulse width of the input data signal is less than 20ns. A signal with a pulse width
that is between 20ns and 400 ns should not be applied as the behavior is not guaranteed.
• Trigger outputs are grouped peripheral-wise and have a common lock bit based on TRGMUX REGx. For instance,
normal_trigger, injected_trigger and external_sync of ADC_0 are grouped onto TRGMUX_REG0 and have a common lock bit.

#### 62.1.2 Pulse strechers in TRGMUX

TRGMUX has some hardware modules on its input side running at faster clock than some of the IPs present on output side. For
instance, eMIOS reload outputs running at 160 MHz can trigger LPI2C trigger input clocked at 40 MHz, in that case there is a high
chance that trigger from eMIOS will be missed. Following Pulse strechers are added for the IPs on output side of TRGMUX.

---

*Page 1977*

Trigger MUX (TRGMUX)
Table 352. Pulse strechers in TRGMUX
Evaluation ADC_0/1/2 BCTU trigger FlexIO trigger LPI2C_0 LPSPI_0/1/2 CM7_0/1_RXEV
1
Parameter external trigger 23/47/71 input_0/1/2/3 Trigger input Trigger input
to sync the start
pulse
IP expects Synchronized Synchronized IP requirement IP requirement IP requirement Single cycle
single cycle pulse is to have 2 is to have 2 is to have 2 pulse
pulse cycle pulse of cycle pulse of cycle pulse of
flexio_clk lpi2c_clk lpspi_clk
Frequency CORE_CLK CORE_CLK CORE_CLK AIPS_SLOW_C CORE_CLK
LPSPI_0 -
LK
PLAT_AIPS_CL
K
LPSPI_1/2/3 -
AIPS_SLOW_C
LK
Inside IP Used as combo Flopped inside Synchronized Synchronized Synchronized -
signal IP and clear inside IP inside IP inside IP
after ADC
conversion
completed
At SoC Since it an Since slow IP Pulse stretcher Pulse stretcher Pulse stretcher Pulse stretcher
ASYNC signal such as LPCMP is added to is added to is added to is added to
and IP required or from PAD can convert any convert any convert any convert any
synchronized also trigger pulse into a two pulse into a two pulse into a two pulse into a
single cycle BCTU and cyle pulse of cyle pulse of cyle pulse of single cyle pulse
pulse, so a pulse BCTU wants FLEXIO_CLK LPI2C_CLK LPSPI_CLK of CORE_CLK
stretcher is that trigger
added to convert should clear
pulse from any after the ADC
frequency conversion. So a
domain into a pulse stretcher
signle cycle is added which
pulse of convert any size
CORE_CLK of pulse to a
single cycle
pulse of BCTU
clock
(CORE_CLK)
Output slot at 2, 6, 10 24, 25, 26 64, 65, 66, 67 84 88, 92, 96 156, 157
which pulse
stretcher is
added
1. These pulse stretchers are available in MCXE31B variants only.
NOTE
The trigger outputs which have pulse stretcher before them, there should be atleast a gap of 5 cycle of destination
clock for back to back trigger.

---

*Page 1978*

Trigger MUX (TRGMUX)

#### 62.2 Overview

TRGMUX allows you to configure the trigger inputs for various peripherals.

#### 62.2.1 Block diagram

TRGMUX
Trigger disabled ...000
Trigger input 1 ...001
Trigger input 2 ...010
Trigger input 3 ...011
Output x To peripheral
Trigger input 4 ...100
trigger inputs
Trigger input 5 ...101
Trigger input 6 ...110
Trigger input 7 ...111 Up to four outputs per peripheral
[SEL0] selects the trigger for output 0
Trigger input N*
[SEL1] selects the trigger for output 1
[SEL2] selects the trigger for output 2
[SEL3] selects the trigger for output 3
[SEL x ]
Figure 349. Block diagram
NOTE
Up to 255 trigger inputs may be available for SEL0, SEL1 , and SEL2 . For SEL3, up to 127 trigger inputs may
be available. When the number of trigger inputs is 255, SEL3 is not available and becomes reserved. See the
chip-specific TRGMUX information for details about trigger input and output configuration on this chip.

#### 62.2.2 Features

• Configurable trigger sources for peripherals
• Dedicated TRGMUX register for each peripheral

#### 62.3 Functional description

#### 62.3.1 Clocking

This module has no clocking considerations.

#### 62.3.2 Interrupts

This module has no interrupts.

#### 62.4 External signals

This module has no external signals.

#### 62.5 Initialization

This module does not require initialization.

#### 62.6 TRGMUX register descriptions

---

*Page 1979*

Trigger MUX (TRGMUX)

#### 62.6.1 TRGMUX memory map

You can only write to TRGMUX registers in Supervisor mode.
Table 353. Select bit fields
Field Description
SEL x Specifies the MUX select for the peripheral trigger inputs. Use this field to select the trigger sources for
peripheral modules.
0h - LOGIC 0 (VSS)
1h - LOGIC 1 (VDD)
2h - ADC12_0_EOC
3h - ADC12_1_EOC
4h - ADC12_2_EOC
5h - LPCMP_0_COUT output
6h - LPCMP_1_COUT output
7h - LPCMP_2_COUT output
8h - eDMA_eDMA_0 DONE
9h - eDMA_eDMA_1 DONE
Ah - eDMA_eDMA_16 DONE
Bh - eDMA_eDMA_17 DONE
Ch - eMIOS_0_RELOAD_OUT_CH[23]
Dh - eMIOS_0_RELOAD_OUT_CH[22]
Eh - eMIOS_0_RELOAD_OUT_CH[8]
Fh - eMIOS_0_RELOAD_OUT_CH[0]
10h - eMIOS_0_IPP_DO_eMIOS_CH[0]
11h - eMIOS_0_IPP_DO_eMIOS_CH[1]
12h - eMIOS_0_IPP_DO_eMIOS_CH[2]
13h - eMIOS_0_IPP_DO_eMIOS_CH[3]
14h - eMIOS_0_IPP_DO_eMIOS_CH[4]
15h - eMIOS_0_IPP_DO_eMIOS_CH[5]
16h - eMIOS_0_IPP_DO_eMIOS_CH[6]
17h - eMIOS_0_IPP_DO_eMIOS_CH[7]
18h - eMIOS_0_IPP_DO_eMIOS_CH[8]
19h - eMIOS_0_IPP_DO_eMIOS_CH[9]
1Ah - eMIOS_0_IPP_DO_eMIOS_CH[10]
1Bh - eMIOS_0_IPP_DO_eMIOS_CH[11]
1Ch - eMIOS_0_IPP_DO_eMIOS_CH[12]
1Dh - eMIOS_0_IPP_DO_eMIOS_CH[13]
Table continues on the next page...

---

*Page 1980*

Trigger MUX (TRGMUX)
Table 353. Select bit fields
Field Description
1Eh - eMIOS_0_IPP_DO_eMIOS_CH[14]
1Fh - eMIOS_0_IPP_DO_eMIOS_CH[15]
20h - eMIOS_0_IPP_DO_eMIOS_CH[22]
21h - eMIOS_0_IPP_DO_eMIOS_CH[23]
22h - eMIOS_1_RELOAD_OUT_CH[23]
23h - eMIOS_1_RELOAD_OUT_CH[22]
24h - eMIOS_1_RELOAD_OUT_CH[8]
25h - eMIOS_1_RELOAD_OUT_CH[0]
26h - eMIOS_1_IPP_DO_eMIOS_CH[0]
27h - eMIOS_1_IPP_DO_eMIOS_CH[1]
28h - eMIOS_1_IPP_DO_eMIOS_CH[2]
29h - eMIOS_1_IPP_DO_eMIOS_CH[3]
2Ah - eMIOS_1_IPP_DO_eMIOS_CH[4]
2Bh - eMIOS_1_IPP_DO_eMIOS_CH[5]
2Ch - eMIOS_1_IPP_DO_eMIOS_CH[6]
2Dh - eMIOS_1_IPP_DO_eMIOS_CH[7]
2Eh - eMIOS_1_IPP_DO_eMIOS_CH[8]
2Fh - eMIOS_1_IPP_DO_eMIOS_CH[9]
30h - eMIOS_1_IPP_DO_eMIOS_CH[10]
31h - eMIOS_1_IPP_DO_eMIOS_CH[11]
32h - eMIOS_1_IPP_DO_eMIOS_CH[12]
33h - eMIOS_1_IPP_DO_eMIOS_CH[13]
34h - eMIOS_1_IPP_DO_eMIOS_CH[14]
35h - eMIOS_1_IPP_DO_eMIOS_CH[15]
36h - eMIOS_1_IPP_DO_eMIOS_CH[22]
37h - eMIOS_1_IPP_DO_eMIOS_CH[23]
38h - FlexIO_External Output Trigger 0
39h - FlexIO_External Output Trigger 1
3Ah - FlexIO_External Output Trigger 2
3Bh - FlexIO_External Output Trigger 3
3Ch - SIUL_TRGMUX_IN0
3Dh - SIUL_TRGMUX_IN1
3Eh - SIUL_TRGMUX_IN2
3Fh - SIUL_TRGMUX_IN3
Table continues on the next page...

---

*Page 1981*

Trigger MUX (TRGMUX)
Table 353. Select bit fields
Field Description
40h - SIUL_TRGMUX_IN4
41h - SIUL_TRGMUX_IN5
42h - SIUL_TRGMUX_IN6
43h - SIUL_TRGMUX_IN7
44h - SIUL_TRGMUX_IN8
45h - SIUL_TRGMUX_IN9
46h - SIUL_TRGMUX_IN10
47h - SIUL_TRGMUX_IN11
48h - SIUL_TRGMUX_IN12
49h - SIUL_TRGMUX_IN13
4Ah - SIUL_TRGMUX_IN14
4Bh - SIUL_TRGMUX_IN15
4Ch - LPI2C_0_Master trigger output
4Dh - LPI2C_0_Slave trigger output
4Eh - LPSPI_0_End of frame trigger
4Fh - LPSPI_0_Receive data trigger
50h - LPSPI_1_End of frame trigger
51h - LPSPI_1_Receive data trigger
52h - LPSPI_2_End of frame trigger
53h - LPSPI_2_Receive data trigger
54h - LPUART_0_trg_txword
55h - LPUART_0_trg_rxword
56h - LPUART_0_trg_rxidle
57h - LPUART_1_trg_txword
58h - LPUART_1_trg_rxword
59h - LPUART_1_trg_rxidle
5Ah - LPUART_2_trg_txword
5Bh - LPUART_2_trg_rxword
5Ch - LPUART_2_trg_rxidle
5Dh - LCU_0_LC0_out_i0
5Eh - LCU_0_LC0_out_i1
5Fh - LCU_0_LC0_out_i2
60h - LCU_0_LC0_out_i3
61h - LCU_0_LC1_out_i0
Table continues on the next page...

---

*Page 1982*

Trigger MUX (TRGMUX)
Table 353. Select bit fields
Field Description
62h - LCU_0_LC1_out_i1
63h - LCU_0_LC1_out_i2
64h - LCU_0_LC1_out_i3
65h - LCU_0_LC2_out_i0
66h - LCU_0_LC2_out_i1
67h - LCU_0_LC2_out_i2
68h - LCU_0_LC2_out_i3
69h - LCU_1_LC0_out_i0
6Ah - LCU_1_LC0_out_i1
6Bh - LCU_1_LC0_out_i2
6Ch - LCU_1_LC0_out_i3
6Dh - LCU_1_LC1_out_i0
6Eh - LCU_1_LC1_out_i1
6Fh - LCU_1_LC1_out_i2
70h - LCU_1_LC1_out_i3
71h - LCU_1_LC2_out_i0
72h - LCU_1_LC2_out_i1
73h - LCU_1_LC2_out_i2
74h - LCU_1_LC2_out_i3
75h - PIT0_PIT0 CH0
76h - PIT0_PIT0 CH1
77h - PIT0_PIT0 CH2
78h - PIT0_PIT0 CH3
79h - PIT0_PIT0 CH4 RTI
7Ah - PIT1_PIT1 CH0
7Bh - PIT1_PIT1 CH1
7Ch - PIT1_PIT1 CH2
7Dh - PIT1_PIT1 CH3
7Eh - CM7_0_TXEV
7Fh - CM7_1_TXEV
TRGMUX base address: 4008_0000h

---

*Page 1983*

Trigger MUX (TRGMUX)
Offset Register Access Reset value
Width
(In bits)
0h TRGMUX ADC12_0 (ADC12_0) 32 RW 0000_0000h
4h TRGMUX ADC12_1 (ADC12_1) 32 RW 0000_0000h
8h TRGMUX ADC12_2 (ADC12_2) 32 RW 0000_0000h
Ch TRGMUX LPCMP_0 (LPCMP_0) 32 RW 0000_0000h
10h TRGMUX LPCMP_1 (LPCMP_1) 32 RW 0000_0000h
14h TRGMUX LPCMP_2 (LPCMP_2) 32 RW 0000_0000h
18h TRGMUX BCTU (BCTU) 32 RW 0000_0000h
1Ch TRGMUX eMIOS012_ODIS (eMIOS012_ODIS) 32 RW 0000_0000h
20h TRGMUX eMIOS0_0 (eMIOS0_0) 32 RW 0000_0000h
24h TRGMUX eMIOS0_1 (eMIOS0_1) 32 RW 0000_0000h
28h TRGMUX eMIOS0_2 (eMIOS0_2) 32 RW 0000_0000h
2Ch TRGMUX eMIOS0_3 (eMIOS0_3) 32 RW 0000_0000h
30h TRGMUX eMIOS1_0 (eMIOS1_0) 32 RW 0000_0000h
34h TRGMUX eMIOS1_1 (eMIOS1_1) 32 RW 0000_0000h
38h TRGMUX eMIOS1_2 (eMIOS1_2) 32 RW 0000_0000h
3Ch TRGMUX eMIOS1_3 (eMIOS1_3) 32 RW 0000_0000h
40h TRGMUX FlexIO (FlexIO) 32 RW 0000_0000h
44h TRGMUX SIUL_OUT0 (SIUL_OUT0) 32 RW 0000_0000h
48h TRGMUX SIUL_OUT1 (SIUL_OUT1) 32 RW 0000_0000h
4Ch TRGMUX SIUL_OUT2 (SIUL_OUT2) 32 RW 0000_0000h
50h TRGMUX SIUL_OUT3 (SIUL_OUT3) 32 RW 0000_0000h
54h TRGMUX LPI2C_0 (LPI2C_0) 32 RW 0000_0000h
58h TRGMUX LPSPI_0 (LPSPI_0) 32 RW 0000_0000h
5Ch TRGMUX LPSPI_1 (LPSPI_1) 32 RW 0000_0000h
60h TRGMUX LPSPI_2 (LPSPI_2) 32 RW 0000_0000h
64h TRGMUX LPUART_0 (LPUART_0) 32 RW 0000_0000h
68h TRGMUX LPUART_1 (LPUART_1) 32 RW 0000_0000h
6Ch TRGMUX LPUART_2 (LPUART_2) 32 RW 0000_0000h
70h TRGMUX LPUART_3 (LPUART_3) 32 RW 0000_0000h
74h TRGMUX LCU0_SYNC (LCU0_SYNC) 32 RW 0000_0000h
78h TRGMUX LCU0_FORCE (LCU0_FORCE) 32 RW 0000_0000h
7Ch TRGMUX LCU0_0 (LCU0_0) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 1984*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
80h TRGMUX LCU0_1 (LCU0_1) 32 RW 0000_0000h
84h TRGMUX LCU0_2 (LCU0_2) 32 RW 0000_0000h
88h TRGMUX LCU1_SYNC (LCU1_SYNC) 32 RW 0000_0000h
8Ch TRGMUX LCU1_FORCE (LCU1_FORCE) 32 RW 0000_0000h
90h TRGMUX LCU1_0 (LCU1_0) 32 RW 0000_0000h
94h TRGMUX LCU1_1 (LCU1_1) 32 RW 0000_0000h
98h TRGMUX LCU1_2 (LCU1_2) 32 RW 0000_0000h
9Ch TRGMUX CM7_RXEV (CM7_RXEV) 32 RW 0000_0000h

#### 62.6.2 TRGMUX ADC12_0 (ADC12_0)

Offset
Register Offset
ADC12_0 0h
Function
Configures the ADC12_0 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
LK SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
Table continues on the next page...

---

*Page 1985*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.3 TRGMUX ADC12_1 (ADC12_1)

Offset
Register Offset
ADC12_1 4h
Function
Configures the ADC12_1 module.

---

*Page 1986*

Trigger MUX (TRGMUX)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
LK SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

---

*Page 1987*

Trigger MUX (TRGMUX)

#### 62.6.4 TRGMUX ADC12_2 (ADC12_2)

Offset
Register Offset
ADC12_2 8h
Function
Configures the ADC12_2 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
LK SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
Table continues on the next page...

---

*Page 1988*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.5 TRGMUX LPCMP_0 (LPCMP_0)

Offset
Register Offset
LPCMP_0 Ch
Function
Configures the LPCMP_0 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
LK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
Table continues on the next page...

---

*Page 1989*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
Reserved
22-16
—
Reserved
15
—
Reserved
14-8
—
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.6 TRGMUX LPCMP_1 (LPCMP_1)

Offset
Register Offset
LPCMP_1 10h
Function
Configures the LPCMP_1 module.

---

*Page 1990*

Trigger MUX (TRGMUX)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
LK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
Reserved
22-16
—
Reserved
15
—
Reserved
14-8
—
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

---

*Page 1991*

Trigger MUX (TRGMUX)

#### 62.6.7 TRGMUX LPCMP_2 (LPCMP_2)

Offset
Register Offset
LPCMP_2 14h
Function
Configures the LPCMP_2 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
LK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
Reserved
22-16
—
Reserved
15
—
Table continues on the next page...

---

*Page 1992*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
Reserved
14-8
—
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.8 TRGMUX BCTU (BCTU)

Offset
Register Offset
BCTU 18h
Function
Configures the BCTU module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
LK SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
Table continues on the next page...

---

*Page 1993*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.9 TRGMUX eMIOS012_ODIS (eMIOS012_ODIS)

Offset
Register Offset
eMIOS012_ODIS 1Ch
Function
Configures the eMIOS012_ODIS module.

---

*Page 1994*

Trigger MUX (TRGMUX)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
LK SEL3 SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
30-24 TRGMUX Source Select 3
SEL3 Specifies the source select for output 3. See Table 353 for field values.
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

---

*Page 1995*

Trigger MUX (TRGMUX)

#### 62.6.10 TRGMUX eMIOS0_0 (eMIOS0_0)

Offset
Register Offset
eMIOS0_0 20h
Function
Configures the eMIOS0_0 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
LK SEL3 SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
30-24 TRGMUX Source Select 3
SEL3 Specifies the source select for output 3. See Table 353 for field values.
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
Table continues on the next page...

---

*Page 1996*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.11 TRGMUX eMIOS0_1 (eMIOS0_1)

Offset
Register Offset
eMIOS0_1 24h
Function
Configures the eMIOS0_1 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
LK SEL3 SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
Table continues on the next page...

---

*Page 1997*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
1b - Register is not writable until the next system reset
30-24 TRGMUX Source Select 3
SEL3 Specifies the source select for output 3. See Table 353 for field values.
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.12 TRGMUX eMIOS0_2 (eMIOS0_2)

Offset
Register Offset
eMIOS0_2 28h
Function
Configures the eMIOS0_2 module.

---

*Page 1998*

Trigger MUX (TRGMUX)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
LK SEL3 SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
30-24 TRGMUX Source Select 3
SEL3 Specifies the source select for output 3. See Table 353 for field values.
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

---

*Page 1999*

Trigger MUX (TRGMUX)

#### 62.6.13 TRGMUX eMIOS0_3 (eMIOS0_3)

Offset
Register Offset
eMIOS0_3 2Ch
Function
Configures the eMIOS0_3 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
LK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
Reserved
22-16
—
Reserved
15
—
Table continues on the next page...

---

*Page 2000*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.14 TRGMUX eMIOS1_0 (eMIOS1_0)

Offset
Register Offset
eMIOS1_0 30h
Function
Configures the eMIOS1_0 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
LK SEL3 SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
Table continues on the next page...

---

*Page 2001*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
1b - Register is not writable until the next system reset
30-24 TRGMUX Source Select 3
SEL3 Specifies the source select for output 3. See Table 353 for field values.
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.15 TRGMUX eMIOS1_1 (eMIOS1_1)

Offset
Register Offset
eMIOS1_1 34h
Function
Configures the eMIOS1_1 module.

---

*Page 2002*

Trigger MUX (TRGMUX)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
LK SEL3 SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
30-24 TRGMUX Source Select 3
SEL3 Specifies the source select for output 3. See Table 353 for field values.
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

---

*Page 2003*

Trigger MUX (TRGMUX)

#### 62.6.16 TRGMUX eMIOS1_2 (eMIOS1_2)

Offset
Register Offset
eMIOS1_2 38h
Function
Configures the eMIOS1_2 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
LK SEL3 SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
30-24 TRGMUX Source Select 3
SEL3 Specifies the source select for output 3. See Table 353 for field values.
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
Table continues on the next page...

---

*Page 2004*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.17 TRGMUX eMIOS1_3 (eMIOS1_3)

Offset
Register Offset
eMIOS1_3 3Ch
Function
Configures the eMIOS1_3 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
LK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
Table continues on the next page...

---

*Page 2005*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
Reserved
22-16
—
Reserved
15
—
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.18 TRGMUX FlexIO (FlexIO)

Offset
Register Offset
FlexIO 40h
Function
Configures the FlexIO module.

---

*Page 2006*

Trigger MUX (TRGMUX)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
LK SEL3 SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
30-24 TRGMUX Source Select 3
SEL3 Specifies the source select for output 3. See Table 353 for field values.
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

---

*Page 2007*

Trigger MUX (TRGMUX)

#### 62.6.19 TRGMUX SIUL_OUT0 (SIUL_OUT0)

Offset
Register Offset
SIUL_OUT0 44h
Function
Configures the SIUL_OUT0 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
LK SEL3 SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
30-24 TRGMUX Source Select 3
SEL3 Specifies the source select for output 3. See Table 353 for field values.
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
Table continues on the next page...

---

*Page 2008*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.20 TRGMUX SIUL_OUT1 (SIUL_OUT1)

Offset
Register Offset
SIUL_OUT1 48h
Function
Configures the SIUL_OUT1 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
LK SEL3 SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
Table continues on the next page...

---

*Page 2009*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
1b - Register is not writable until the next system reset
30-24 TRGMUX Source Select 3
SEL3 Specifies the source select for output 3. See Table 353 for field values.
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.21 TRGMUX SIUL_OUT2 (SIUL_OUT2)

Offset
Register Offset
SIUL_OUT2 4Ch
Function
Configures the SIUL_OUT2 module.

---

*Page 2010*

Trigger MUX (TRGMUX)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
LK SEL3 SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
30-24 TRGMUX Source Select 3
SEL3 Specifies the source select for output 3. See Table 353 for field values.
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

---

*Page 2011*

Trigger MUX (TRGMUX)

#### 62.6.22 TRGMUX SIUL_OUT3 (SIUL_OUT3)

Offset
Register Offset
SIUL_OUT3 50h
Function
Configures the SIUL_OUT3 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
LK SEL3 SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
30-24 TRGMUX Source Select 3
SEL3 Specifies the source select for output 3. See Table 353 for field values.
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
Table continues on the next page...

---

*Page 2012*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.23 TRGMUX LPI2C_0 (LPI2C_0)

Offset
Register Offset
LPI2C_0 54h
Function
Configures the LPI2C_0 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
LK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
Table continues on the next page...

---

*Page 2013*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
Reserved
22-16
—
Reserved
15
—
Reserved
14-8
—
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.24 TRGMUX LPSPI_0 (LPSPI_0)

Offset
Register Offset
LPSPI_0 58h
Function
Configures the LPSPI_0 module.

---

*Page 2014*

Trigger MUX (TRGMUX)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
LK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
Reserved
22-16
—
Reserved
15
—
Reserved
14-8
—
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

---

*Page 2015*

Trigger MUX (TRGMUX)

#### 62.6.25 TRGMUX LPSPI_1 (LPSPI_1)

Offset
Register Offset
LPSPI_1 5Ch
Function
Configures the LPSPI_1 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
LK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
Reserved
22-16
—
Reserved
15
—
Table continues on the next page...

---

*Page 2016*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
Reserved
14-8
—
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.26 TRGMUX LPSPI_2 (LPSPI_2)

Offset
Register Offset
LPSPI_2 60h
Function
Configures the LPSPI_2 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
LK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
Table continues on the next page...

---

*Page 2017*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
Reserved
22-16
—
Reserved
15
—
Reserved
14-8
—
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.27 TRGMUX LPUART_0 (LPUART_0)

Offset
Register Offset
LPUART_0 64h
Function
Configures the LPUART_0 module.

---

*Page 2018*

Trigger MUX (TRGMUX)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
LK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
Reserved
22-16
—
Reserved
15
—
Reserved
14-8
—
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

---

*Page 2019*

Trigger MUX (TRGMUX)

#### 62.6.28 TRGMUX LPUART_1 (LPUART_1)

Offset
Register Offset
LPUART_1 68h
Function
Configures the LPUART_1 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
LK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
Reserved
22-16
—
Reserved
15
—
Table continues on the next page...

---

*Page 2020*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
Reserved
14-8
—
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.29 TRGMUX LPUART_2 (LPUART_2)

Offset
Register Offset
LPUART_2 6Ch
Function
Configures the LPUART_2 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
LK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
Table continues on the next page...

---

*Page 2021*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
Reserved
22-16
—
Reserved
15
—
Reserved
14-8
—
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.30 TRGMUX LPUART_3 (LPUART_3)

Offset
Register Offset
LPUART_3 70h
Function
Configures the LPUART_3 module.

---

*Page 2022*

Trigger MUX (TRGMUX)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
LK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
Reserved
22-16
—
Reserved
15
—
Reserved
14-8
—
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

---

*Page 2023*

Trigger MUX (TRGMUX)

#### 62.6.31 TRGMUX LCU0_SYNC (LCU0_SYNC)

Offset
Register Offset
LCU0_SYNC 74h
Function
Configures the LCU0_SYNC module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
LK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
Reserved
22-16
—
Reserved
15
—
Table continues on the next page...

---

*Page 2024*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.32 TRGMUX LCU0_FORCE (LCU0_FORCE)

Offset
Register Offset
LCU0_FORCE 78h
Function
Configures the LCU0_FORCE module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
LK SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
Table continues on the next page...

---

*Page 2025*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.33 TRGMUX LCU0_0 (LCU0_0)

Offset
Register Offset
LCU0_0 7Ch
Function
Configures the LCU0_0 module.

---

*Page 2026*

Trigger MUX (TRGMUX)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
LK SEL3 SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
30-24 TRGMUX Source Select 3
SEL3 Specifies the source select for output 3. See Table 353 for field values.
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

---

*Page 2027*

Trigger MUX (TRGMUX)

#### 62.6.34 TRGMUX LCU0_1 (LCU0_1)

Offset
Register Offset
LCU0_1 80h
Function
Configures the LCU0_1 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
LK SEL3 SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
30-24 TRGMUX Source Select 3
SEL3 Specifies the source select for output 3. See Table 353 for field values.
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
Table continues on the next page...

---

*Page 2028*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.35 TRGMUX LCU0_2 (LCU0_2)

Offset
Register Offset
LCU0_2 84h
Function
Configures the LCU0_2 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
LK SEL3 SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
Table continues on the next page...

---

*Page 2029*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
1b - Register is not writable until the next system reset
30-24 TRGMUX Source Select 3
SEL3 Specifies the source select for output 3. See Table 353 for field values.
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.36 TRGMUX LCU1_SYNC (LCU1_SYNC)

Offset
Register Offset
LCU1_SYNC 88h
Function
Configures the LCU1_SYNC module.

---

*Page 2030*

Trigger MUX (TRGMUX)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
LK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
Reserved
22-16
—
Reserved
15
—
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

---

*Page 2031*

Trigger MUX (TRGMUX)

#### 62.6.37 TRGMUX LCU1_FORCE (LCU1_FORCE)

Offset
Register Offset
LCU1_FORCE 8Ch
Function
Configures the LCU1_FORCE module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
LK SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
Table continues on the next page...

---

*Page 2032*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.38 TRGMUX LCU1_0 (LCU1_0)

Offset
Register Offset
LCU1_0 90h
Function
Configures the LCU1_0 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
LK SEL3 SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
Table continues on the next page...

---

*Page 2033*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
1b - Register is not writable until the next system reset
30-24 TRGMUX Source Select 3
SEL3 Specifies the source select for output 3. See Table 353 for field values.
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.39 TRGMUX LCU1_1 (LCU1_1)

Offset
Register Offset
LCU1_1 94h
Function
Configures the LCU1_1 module.

---

*Page 2034*

Trigger MUX (TRGMUX)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
LK SEL3 SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
30-24 TRGMUX Source Select 3
SEL3 Specifies the source select for output 3. See Table 353 for field values.
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

---

*Page 2035*

Trigger MUX (TRGMUX)

#### 62.6.40 TRGMUX LCU1_2 (LCU1_2)

Offset
Register Offset
LCU1_2 98h
Function
Configures the LCU1_2 module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
LK SEL3 SEL2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
1b - Register is not writable until the next system reset
30-24 TRGMUX Source Select 3
SEL3 Specifies the source select for output 3. See Table 353 for field values.
Reserved
23
—
22-16 TRGMUX Source Select 2
SEL2 Specifies the source select for output 2. See Table 353 for field values.
Reserved
15
—
Table continues on the next page...

---

*Page 2036*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

#### 62.6.41 TRGMUX CM7_RXEV (CM7_RXEV)

Offset
Register Offset
CM7_RXEV 9Ch
Function
Configures the CM7_RXEV module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
LK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 TRGMUX Register Lock
LK Disables writing to the register. You can write to this field once after system reset. When this field is 1, you
cannot write to SEL x until the next system reset. This field becomes 0 after system reset.
0b - Register is writable
Table continues on the next page...

---

*Page 2037*

Trigger MUX (TRGMUX)
Table continued from the previous page...
Field Function
1b - Register is not writable until the next system reset
Reserved
30-24
—
Reserved
23
—
Reserved
22-16
—
Reserved
15
—
14-8 TRGMUX Source Select 1
SEL1 Specifies the source select for output 1. See Table 353 for field values.
Reserved
7
—
6-0 TRGMUX Source Select 0
SEL0 Specifies the source select for output 0. See Table 353 for field values.

---

*Page 2038*

