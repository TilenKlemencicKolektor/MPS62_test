<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 58 -->

# Chapter 58

# Low Power Comparator (LPCMP)

#### 58.1 Chip-specific LPCMP information

#### 58.1.1 Instantiation information

Table 275. LPCMP instances
Chip Instance No. of external inputs
MCXE31B LPCMP_0 8
LPCMP_1 4
LPCMP_2 4
MCXE317 LPCMP_0 8
LPCMP_1 4
MCXE315/MCXE316 LPCMP_0 8

#### 58.1.2 LPCMP input output connections

See the IOMUX file attached to this document for pin/pad assignments corresponding to CMP pins.
The LPCMP channels can be used as a wakeup source in trigger mode to wakeup the device from standby mode.

#### 58.1.3 LPCMP-DAC "vrefh0" and "vrefh1" references

The 8-bit DAC sub-block supports selection of "vrefh0" and "vrefh1" by CMPx_DCR[VRSEL]. For this device, the references are
connected as follows:
• vrefh0 (External Reference): VDD_HV_A
• vrefh1 (Internal Reference): 1.2 V PMC bandgap reference
NOTE
1.2 V internal reference voltage is not available in Standby mode.

#### 58.1.4 LPCMP window control

The window mode operation of all the comparator instances in the chip can be enabled/disabled by the TRGMUX. See the
TRGMUX connectivity file attached to this document for details.
NOTE
Window signal must be of minimum 4 cycle pulse for window mode to function.

#### 58.1.5 Comparator Trigger Mode

The comparator modules in the device support trigger mode operation as described in 'Trigger Mode' section. Device can operate
in trigger mode in both standby and run mode to continuously scan the input channels. The main features of the device trigger
mode operation are:
• Round robin clock: RTC_CLK
• Round robin trigger source: RTC_API

---

*Page 1734*

Low Power Comparator (LPCMP)
NOTE
It must be ensured that the RTC_CLK period is greater than the comparison time corresponding to the value of
C0[PMODE]. It is also required to not select the internal reserved channels, if available on the package, for trigger
mode operation by INPSEL and INNSEL. See the IOMUX file attached to this document for the pins available in
various packages supported for the device.
NOTE
Only SIRC and SXOSC are supported as RTC_CLK for trigger mode operation. The LPCMP initialization delay is
not supported by RRCR0[RR_INITMOD] with FXOSC or FIRC as RTC_CLK.
NOTE
In run mode, the generated trigger is delayed by 3 RTC_CLK cycles before being given to comparator trigger input.

#### 58.1.6 Interaction with RTC API to cause wakeup

LPCMP can be used for waking up the chip from standby. For this, RTC-API and LPCMP must be configured before entering into
standby mode as per below shown figure.
RTC-API CMP
Software
Configure CMPx for round robin operation
Configure CMP_x.RRCR0[RR_EN] to put CMP_x
in trigger mode/round robin mode and configure
RTC.APIVAL for trigger mode/round robin period
Enter into STANDBY mode
On time-out, RTC-API sends a
trigger to CMP_x to start round
robin operation periodically
Sample the configured inputs
and stores the results
Compares
Unequal
the result with previous
result stored
Wakeup from STANDBY mode
Equal
Device stays in STANDBY mode
and CMP_x waits for next trigger.
Figure 232. LPCMP-RTC interaction
In the trigger mode operation, only the continuous mode is supported. None of the window/filter/sample functions should be used.
Refer to the "Functional Modes" section for details on comparator modes of operation.
Register configurations before entering Standby mode for LPCMP trigger mode operation:
1. Configure RTC.APIVAL to set the period of the round robin operation.
2. Execute standby mode entry.

---

*Page 1735*

Low Power Comparator (LPCMP)

#### 58.2 Overview

The LPCMP module provides a circuit to compare two analog input voltages. It includes the following:
• A low power comparator ( CMP )
• A DAC
• An analog mux ( ANMUX )
See Block diagram for more information.
LPCMP can operate across the full range of the supply voltage, known as rail-to-rail operation.
DAC is a 256-tap resistor ladder network that provides a selectable voltage reference for applications requiring a voltage
reference. DAC divides the supply reference V in into 256 voltage levels. An 8-bit digital signal input selects the output voltage level,
which varies from V in to V in /256.
You can select V in from the following voltage sources:
• VREFH0
• VREFH1
See the Chip-specific LPCMP information for more information on source of VREFH0 and VREFH1.
NOTE
The LPCMP's internal DAC output is available as an on-chip internal signal only and is not available for an external
chip pin.
ANMUX allows you to select an analog input signal from among eight channel options. One channel option is the DAC output.
Other chip resources are connected to the other channels. See the Chip-specific LPCMP information section for more information.
ANMUX can operate across the full range of the supply voltage.

---

*Page 1736*

Low Power Comparator (LPCMP)

#### 58.2.1 Block diagram

VRSEL
vrefh0 vrefh1
DCR[DAC_DATA]
0 MUX 1
Vin
DCR[DAC_EN]
Resistor
ladder DAC output
MUX
DAC
Round-
RR_ACTIVE
roubin
FSM
RRCR1
CCR2[PSEL]
[RR_CHxN] Round-
robin
RRCR0
RRCR1 switch
1 0
[RR_EN]
[FIXCH] CCR2[INPSEL]
Input channel 0
000
Input channel 1
001 CMP
Input channel 2
010
Input channel 3
011 INP 01
Input channel 4
100
Input channel 5 PMUX
101
Input channel 6 00 INPMUX SAMPLE/WINDOW input
110
Input channel 7
111 DMA_REQ
Window
IRQ
ANMUX CMP and
filter control CMPO
000
001
010
011 INM
01
100
MMUX
101
110 00 INMMUX
111
RRCR0
From 1 0
[RR_EN]
round-robin
switch CCR2[MSEL] CCR2[INMSEL]
Figure 233. Block diagram

#### 58.2.2 Features

The features of the LPCMP module include：
• Includes two 8-to-1 channel MUXes to select input signal from eight channels
• Supports multiple operation modes to produce a wide range of outputs such as:
— Sampled
— Windowed, which is ideal for certain PWM zero-crossing-detection applications
— Digitally filtered
• Provides the following advance features for window and sample:

---

*Page 1737*

Low Power Comparator (LPCMP)
— Window and sample signals can be inverted.
— CMPO rising, falling or both edges closes the window.
— CMPO level can be defined when window is closed.
• Provides selectable performance levels:
— Low-Power (speed) mode
— High-Power (speed) mode
• Supports programmable hysteresis control
• Provides a selectable inversion on comparator output
• Uses an external hysteresis at the same time the output filter is used for internal functions
• Provides interrupt and DMA support
• Supports Round Robin Trigger mode
• Includes an 8-bit resolution DAC
• Provides a selectable supply reference source for DAC

#### 58.3 Functional description

#### 58.3.1 Functional block diagram

Internal Bus
CMP_NPMD CMP_HPMD HYSTCTR CMP_EN COUT_INV WINDOW_EN FILT_CNT COUT CFR/F_IE DMA_EN CFR/F COUT_PEN
To open PAD for CMPO
DMA_REQ
INP

#### +

Interrupt
Polarity Window Filter
/DMA
Select IRQ Control Block
COUT_RAW

#### - Control

INM
COUT
To other SOC functions
WINDOW/SAMPLE
1
Sampling Clock
0
CMPO to PAD
Bus Clock Divided Bus Clock
Clock 0 COUTA
1
Prescaler
CCR1[FILT_PER]
CCR1[SAMPLE_EN] CCR1[COUT_SEL]
Figure 234. Functional block diagram
As shown in the block diagram, the functions are:

---

*Page 1738*

Low Power Comparator (LPCMP)
• Compared two analog input voltages applied to INP and INM, COUT_RAW is high when the INP input voltage is greater
than the INM input voltage, and COUT_RAW is low when the INP input voltage is less than the INM input voltage.
• The COUT_RAW signal can be inverted by enabling CCR1[COUT_INV] .
• The optionally inverted comparator output COUT_RAW is sampled on every bus clock when you enable the
CCR1[WINDOW_EN] to generate COUTA. In this case, the comparator output is ignored during time periods when the input
voltages are not valid. This is useful when you implement zero-crossing-detection for certain PWM applications.
• The window control block is bypassed when CCR1[WINDOW_EN] is disabled.
• The filter block acts as a simple sampler when CCR1[FILT_CNT] is set to 01h.
• The filter block acts as a filter based on multiple samples when CCR1[FILT_CNT] is set to be greater than 01h.
— If CCR1[SAMPLE_EN] is set to 1, use the external SAMPLE input as the sampling clock.
— If CCR1[SAMPLE_EN] is set to 0, use the divided bus clock as the sampling clock.
• Bypasses the filter block when it is not in use.
Bypass_Filter_Block = (FILT_CNT == 0x00) | (~SAMPLE_EN & (FILT_PER == 0x00))
• Both COUTA and COUT can be configured as module output CMPO by configuring CCR1[COUT_SEL] , and are used for
different purposes within the system.
• The optionally filtered COUT can be read directly in CSR[COUT] .
• The SAMPLE/WINDOW signal can be inverted by setting CCR1[WINDOW_INV] .
• The SAMPLE/WINDOW signal can be closed by CMPO's falling edge and/or rising edge by setting CCR1[WINDOW_CLS]
in Window mode.
• In Window mode, when window is closed, define the COUTA value as CCR1[COUTA_OW] by setting
CCR1[COUTA_OWEN] . If CCR1[COUTA_OWEN] is not set, COUTA holds the last sampled value.
NOTE
See the chip configuration section for the source of SAMPLE/WINDOW input.

#### 58.3.2 Round-robin trigger mode

You can enable Round-Robin Trigger mode by setting RRCR0[RR_EN] and CCR0[CMP_EN] to 1. A trigger event initiates a
comparison sequence. The next trigger event should not occur before the current sequence completes.
RRCR1[FIXP] and RRCR1[FIXCH] select the reference channel for the plus side mux or the minus side mux.
RRCR1[RR_CHnEN] selects active channels.
When a trigger comes, the analog comparator enables. After the comparison sequence completes, the analog comparator
disables again. RRCR0[RR_INITMOD] controls the analog stabilization time.
NOTE
RR_INITMOD*round robin clock period must be longer than the initialization delay specified in the Comparator and
8-bit DAC electrical specifications section of LPCMP datasheet.
After the stabilization process completes, the round robin manner comparison sequence begins. Sample the comparison result
for the selected active channel after RRCR0[RR_NSAM] defines the configurable number of operation clocks.
After all the active channels are sampled/compared, if the comparison result changes from its pre-programmed state, the
corresponding flag in RRSR[RR_CHnF] is set. Write to RRCSR[RR_CHnOUT] to configure the pre-programmed state for each
channel. Update RRCSR[RR_CHnOUT] to store the last comparison result for each channel. If any flag in RRSR[RR_CHnF] sets,
CSR[RRF] also sets. If IER[RRF_IE] sets, an asynchronous interrupt asserts. Note that these flags do not support generating a
DMA transfer event.

---

*Page 1739*

Low Power Comparator (LPCMP)
The following diagram shows the basic flow of this mode. In the diagram, RRCR1[RR_CH1EN], RRCR1[RR_CH3EN], and
RRCR1[RR_CH4EN] are 1, so channels #1, #3, and #4 are selected for round-robin depending on their priority setting.
RRCR0[RR_NSAM] sets to 2'b01, so you can sample one clock later the comparison result of the selected channel. After you
compare the channel #4, the result is sampled, and round-robin ends. If any of the comparison results from channel #1, #3, or #4
changed from their programmed value (written to RRCSR[RR_CH1OUT], RRCSR[RR_CH3OUT], and RRCSR[RR_CH4OUT]),
generates an interrupt. Software can then poll RRSR[RR_CHnF] to see which channel input(s) changed value.
Round Robin Clock
Round Robin Trigger
RR_INITMOD
Round Robin Start
NSAM NSAM NSAM
CMP (and possible DAC) Enable
RR_ACTIVE
Active Channel Select State
IDLE CH1 CH3 CH4
Sample Channel
Comparision Result
RR_CH1/3/4OUT
Channel Results RR_CH1OUT RR_CH1/3OUT
Possible Interrupt
Figure 235. Trigger mode
The table below shows the channel decode in both Functional mode and Trigger mode. Other cases not in the table are illegal.
Table 276. CMP channel decode in functional mode and round-robin trigger mode
Mode RR_E PSEL[2: MSEL[2: INPSEL[1: INMSEL[1 FIX FIXCH[2 RR_CH INP INM CMP
N 0] 0] 0] :0] P :0] xN Behavior
1
Function 0 x 0 to 7 0 1 x x x DAC Channel Channel 0
al mode decoded to 7 can be
from compared
MSEL[2: with DAC
0]
Table continues on the next page...

---

*Page 1740*

Low Power Comparator (LPCMP)
Table 276. CMP channel decode in functional mode and round-robin trigger mode (continued)
Mode RR_E PSEL[2: MSEL[2: INPSEL[1: INMSEL[1 FIX FIXCH[2 RR_CH INP INM CMP
N 0] 0] 0] :0] P :0] xN Behavior
0 to 7 x 1 0 x x x Channel DAC Channel 0
decoded to 7 can be
from compared
PSEL[2:0 with DAC
]
0 to 7 0 to 7 1 1 x x x Channel Channel Channel 0
decoded decoded to 7 can be
from from compared
PSEL[2:0 MSEL[2: with
] 0] channel 0
2
to 7
Trigger 1 x x 0 1 0 x 0 to 7 DAC Channel Channel 0
mode sweep to 7 can be
(RR_CHx swept with
N) DAC
x x 1 0 1 x 0 to 7 Channel DAC Channel 0
sweep to 7 can be
(RR_CHx swept with
N) DAC
x x 1 1 0 0 to 7 0 to 7 Channel Channel Channel 0
fixed by sweep to 7 can be
FIXCH[2: (RR_CHx swept with
0] N) a fixed
channel(0
3
to 7)
x x 1 1 1 0 to 7 0 to 7 Channel Channel Channel 0
sweep fixed by to 7 can be
(RR_CHx FIXCH[2: swept with
N) 0] a fixed
channel(0
3
to 7)
1. "x" means "don't care"
2. PSEL should not be same as MSEL.
3. Channel in the sweep side should not be same as the fixed side.

#### 58.3.3 Low-pass filter mode

The low-pass filter mode operates on an unfiltered, optionally inverted comparator output COUTA, and generates the filtered and
synchronized output COUT. You can configure both COUTA and COUT as module outputs and use for different purposes within
the system.
Synchronization and edge detection determine the bit values of status register. They also apply to COUT for all sampling and
windowed modes. You can perform filtering using an internal timebase defined by CCR1[FILT_PER] , or use an external sample
input to determine sample time.
The need for digital filtering and the amount of filtering depends on your requirements. Filtering can become more useful in the
absence of an external hysteresis circuit. Without external hysteresis, generate a high-frequency oscillations at COUTA when the
selected INM and INP input voltages differ by less than the offset voltage of the differential comparator.

---

*Page 1741*

Low Power Comparator (LPCMP)
58.3.3.1 Enabling low-pass filter mode
You can enable low-pass filter mode by setting the following:
• CCR1[FILT_CNT] > 01h
• CCR1[FILT_PER] to a nonzero value or writing 1 to CCR1[SAMPLE_EN] .
If you use the divided bus clock to drive the low-pass filter, it samples COUTA every CCR1[FILT_PER] bus clock cycle.
If CCR1[SAMPLE_EN] is set to 1, the low-pass filter samples COUTA on each positive transition of the sample input. The output
state of the filter changes when all the consecutive CCR1[FILT_CNT] samples agree that the output value has changed.
58.3.3.2 Latency issues
Program the value of CCR1[FILT_PER] or sample period such that the sampling period is longer than the period of the expected
noise, ensuring that a given noise spike corrupts only one sample. You must choose the value of CCR1[FILT_CNT] to reduce the
probability of noisy samples causing an incorrect transition to recognize. The probability of an incorrect transition is defined as the
probability of an incorrect sample raised to the power of CCR1[FILT_CNT] .
You must trade off the values of CCR1[FILT_PER] or sample period and CCR1[FILT_CNT] against the need for minimal latency in
recognizing actual comparator output transitions. The probability of detecting an actual output change within the nominal latency
is the probability of a correct sample raised to the power of CCR1[FILT_CNT] .
Table 278 summarizes maximum latency values for the various modes of operation in the absence of noise. Filtering latency
restarts each time the noise masks an actual output transition.

#### 58.3.4 Low power mode operation

Below table introduces the mode of operation of lower power.
Table 277. Low power mode operation
Mode of operation Description
LPCMP can operate only in Continuous mode or Round-robin
STANDBY
trigger mode .

#### 58.3.5 Functional modes

You can combine the comparator window and filter features as shown in the following table.
Table 278. Functional modes
1
Mode CMP_EN WINDOW_ SAMPLE_ FILT_CNT FILT_PER Operation Maximum latency
# EN EN
1 0 X X X X N/A
See the Disabled
mode (#1) .
2A 1 0 X 0x00 X T PD
See the Continuous
mode (#2A and #2B) .
2B 1 0 0 X 0x00
3A 1 0 1 0x01 X T PD + T SAMPLE + 3T per
See the Sampled, non-
filtered mode (#3A
3B 1 0 0 0x01 > 0x00 T PD + (FILT_PER * T per )
and #3B) .
+ 3T per
Table continues on the next page...

---

*Page 1742*

Low Power Comparator (LPCMP)
Table 278. Functional modes (continued)
1
Mode CMP_EN WINDOW_ SAMPLE_ FILT_CNT FILT_PER Operation Maximum latency
# EN EN
4A 1 0 1 > 0x01 X T PD + (FILT_CNT *
See the Sampled,
T SAMPLE ) + 3T per
filtered mode (#4A
and #4B) .
4B 1 0 0 > 0x01 > 0x00 T PD + (FILT_CNT *
FILT_PER x T per ) + 3T per
5A 1 1 0 0x00 X T PD + 2T per
See the Windowed
mode (#5A and #5B) .
5B 1 1 0 X 0x00
6 1 1 0 0x01 > 0x00 T PD + (FILT_PER * T per )
See the Windowed/
+ 3T per
Resampled mode (#6) .
7 1 1 0 > 0x01 > 0x00 T PD + (FILT_CNT *
See the Windowed/
FILT_PER x T per ) + 3T per
Filtered mode (#7) .
All other combinations of CMP_EN, WINDOW_EN, SAMPLE_EN, FILT_CNT, and FILT_PER are illegal.
1. T PD represents the intrinsic delay of the analog component plus the polarity select logic. T SAMPLE is the clock period of the
external sample clock. T per is the period of the bus clock.
58.3.5.1 Disabled mode (#1)
In this mode:
• The analog comparator is non-functional and consumes no power.
• CSR[COUT] and CMPO are the same as CCR1[COUT_INV] .

---

*Page 1743*

Low Power Comparator (LPCMP)
58.3.5.2 Continuous mode (#2A and #2B)
Internal Bus
CMP_NPMD CMP_HPMD HYSTCTR CMP_EN COUT_INV WINDOW_EN FILT_CNT COUT CFR/F_IE DMA_EN CFR/F COUT_PEN
To open PAD for CMPO
0
0x00 (#2A)
DMA_REQ
INP

#### +

Interrupt
Polarity Window Filter
/DMA
Select IRQ Control Block
COUT_RAW

#### - Control

INM
COUT
To other SOC functions
WINDOW/SAMPLE
1
Sampling Clock
0
CMPO to PAD
Bus Clock Divided Bus Clock
Clock 0 COUTA
1
Prescaler
FILT_PER
0
0x00 (#2B)
SAMPLE_EN COUT_SEL
Figure 236. Comparator operation in continuous mode
COUT_RAW is optionally inverted in this mode but is not subject to external sampling or filtering. Both window control and filter
blocks bypass completely, and CSR[COUT] updates continuously. The path from comparator input pins to output pins operates
in a combinational (unclocked) mode. COUT and COUTA are identical in this mode.
For cases where a comparator drives a fault input, you must configure it to operate in Continuous mode so that an external fault
can immediately pass to the target fault circuitry through the comparator.

---

*Page 1744*

Low Power Comparator (LPCMP)
58.3.5.3 Sampled, non-filtered mode (#3A and #3B)
Internal Bus
CMP_NPMD CMP_HPMD HYSTCTR CMP_EN COUT_INV WINDOW_EN FILT_CNT COUT CFR/F_IE DMA_EN CFR/F COUT_PEN
To open PAD for CMPO
0 0x01
DMA_REQ
INP

#### +

Interrupt
Polarity Window Filter
/DMA
Select IRQ Control Block
COUT_RAW

#### - Control

INM
COUT
To other SOC functions
WINDOW/SAMPLE
1
Sampling Clock 0
CMPO to PAD
Bus Clock Divided Bus Clock
Clock 0 COUTA
1
Prescaler
FILT_PER
1
SAMPLE_EN COUT_SEL
Figure 237. Sampled, non-filtered (#3A): sampling point externally driven

---

*Page 1745*

Low Power Comparator (LPCMP)
Internal Bus
CMP_NPMD CMP_HPMD HYSTCTR CMP_EN COUT_INV WINDOW_EN FILT_CNT COUT CFR/F_IE DMA_EN CFR/F COUT_PEN
To open PAD for CMPO
0 0x01
DMA_REQ
INP

#### +

Interrupt
Polarity Window Filter
/DMA
Select IRQ Control Block
COUT_RAW

#### - Control

INM
COUT
To other SOC functions
WINDOW/SAMPLE
1
Sampling Clock
0
CMPO to PAD
Bus Clock Divided Bus Clock
Clock 0 COUTA
1
Prescaler
FILT_PER
0
>0x00
SAMPLE_EN COUT_SEL
Figure 238. Sampled, Non-Filtered (#3B): sampling interval internally derived
In this mode, the path from analog inputs to COUTA is combinational (unclocked). Windowing control bypasses completely. You
can sample COUTA whenever you detect a rising edge on the sampling clock.
The difference in two operation modes (#3A and #3B) of sampled, non-filtered mode is that how you drive the clock to the filter
block. In #3A, the clock to filter block drives externally, and in #3B, the clock to filter block drives internally.
The filter block has no other function than sample or hold of the comparator output in this mode.
The following figure shows the comparator operation in this mode, assuming that the polarity select sets to a non-inverting state.
COUT_RAW
COUT
Sample point
Figure 239. Sampled, Non-Filtered mode timing diagram

---

*Page 1746*

Low Power Comparator (LPCMP)
58.3.5.4 Sampled, filtered mode (#4A and #4B)
Internal Bus
CMP_NPMD CMP_HPMD HYSTCTR CMP_EN COUT_INV WINDOW_EN FILT_CNT COUT CFR/F_IE DMA_EN CFR/F COUT_PEN
To open PAD for CMPO
0 >0x01
DMA_REQ
INP

#### +

Interrupt
Polarity Window Filter
/DMA
Select IRQ Control Block
COUT_RAW

#### - Control

INM
COUT
To other SOC functions
WINDOW/SAMPLE
1
Sampling Clock
0
CMPO to PAD
Bus Clock Divided Bus Clock
Clock 0 COUTA
1
Prescaler
FILT_PER
1
SAMPLE_EN COUT_SEL
Figure 240. Sampled, filtered (#4A): sampling point externally driven

---

*Page 1747*

Low Power Comparator (LPCMP)
Internal Bus
CMP_NPMD CMP_HPMD HYSTCTR CMP_EN COUT_INV WINDOW_EN FILT_CNT COUT CFR/F_IE DMA_EN CFR/F COUT_PEN
To open PAD for CMPO
0 >0x01
DMA_REQ
INP

#### +

Interrupt
Polarity Window Filter
/DMA
Select IRQ Control Block
COUT_RAW

#### - Control

INM
COUT
To other SOC functions
WINDOW/SAMPLE
1
Sampling Clock
0
CMPO to PAD
Bus Clock Divided Bus Clock
Clock 0 COUTA
1
Prescaler
FILT_PER
0
>0x00
SAMPLE_EN COUT_SEL
Figure 241. Sampled, filtered (#4B): sampling point internally derived
In this mode, the path from the analog inputs to COUTA is combinational(unclocked). Windowing control bypasses completely.
You can sample COUTA whenever you detect a rising edge on the sampling clock.
The only difference in operation between sampled, non-filtered (#3A) mode and sampled, filtered (#4A) mode is that
CCR1[FILT_CNT] is larger than 1, which activates filter operation.
The only difference in operation between sampled, non-filtered (#3B) mode and sampled, filtered (#4B) mode is that
CCR1[FILT_CNT] is larger than 1, which activates filter operation.

---

*Page 1748*

Low Power Comparator (LPCMP)
58.3.5.5 Windowed mode (#5A and #5B)
Internal Bus
CMP_NPMD CMP_HPMD HYSTCTR CMP_EN COUT_INV WINDOW_EN FILT_CNT COUT CFR/F_IE DMA_EN CFR/F COUT_PEN
1 To open PAD for CMPO
0x00 (#5A)
DMA_REQ
INP

#### +

Interrupt
Polarity Window Filter
/DMA
Select IRQ Control Block
COUT_RAW

#### - Control

INM
COUT
To other SOC functions
WINDOW/SAMPLE
1
Sampling Clock
0
CMPO to PAD
Bus Clock Divided Bus Clock
Clock 0 COUTA
1
Prescaler
FILT_PER
0
0x00 (#5B)
SAMPLE_EN COUT_SEL
Figure 242. Windowed mode
The bus clock clocks COUTA whenever you enable the window in this mode. The last latched value holds after you disable the
window and the filter block is bypassed.
The following figure shows the comparator operation in this mode, ignoring the latency of the analog comparator, polarity select,
and window control block. The polarity select sets to a non-inverting state.
COUTA may lag the analog inputs by up to two functional clock cycles plus the combinational path delay through the comparator
and polarity select logic in the actual operation.
Window
COUT_RAW
COUTA
Figure 243. Windowed mode timing diagram
The following figure shows that if CCR1[COUTA_OWEN] becomes 1, you can define COUTA level as CCR1[COUTA_OW] , after
you closes the window.

---

*Page 1749*

Low Power Comparator (LPCMP)
Window
COUT_RAW
COUTA
User-defined level (COUTA_OW = 0)
Figure 244. Windowed mode timing diagram with user defined value 0 outside window
Window
COUT_RAW
COUTA
User-defined level (COUTA_OW = 1)
Figure 245. Windowed mode timing diagram with user defined value 1 outside window
NOTE
When the window is open, COUT_A will switch from COUTA_OW to COUT_RAW. When the window is closed,
COUT_A will switch from COUT_RAW to COUTA_OW. This may generate unnecessary transition flags, for
instance, CFR or CFF. User needs to choose COUTA_OW carefully according to the actual application, and select
the appropriate flag CFR or CFF to generate interrupt.
If CCR1[WINDOW_CLS] becomes 1, you can define the CMPO event (rising edge, falling edge or both edges that
CCR1[EVT_SEL] selects) to close the window. The external window signal has to go to zero and back to one to enable
the internal window again. The following figure shows an example that CMPO rising edge closes the internal window.
WINDOW
COUT_RAW
CMPO
WINDOW_INTERNAL
CMPO rising edge causes internal window close
Figure 246. Windowed mode timing diagram with CMPO rising edge close window
The following figure shows that if CCR1[WINDOW_INV] becomes 1, you can invert the window signal before you use it.

---

*Page 1750*

Low Power Comparator (LPCMP)
CCR1[WINDOW_INV]
WINDOW
WINDOW_INTERNAL
COUT_RAW
COUTA
Figure 247. Windowed mode timing diagram with window signal inverted
58.3.5.6 Windowed/Resampled mode (#6)
Internal Bus
CMP_NPMD CMP_HPMD HYSTCTR CMP_EN COUT_INV WINDOW_EN FILT_CNT COUT CFR/F_IE DMA_EN CFR/F COUT_PEN
1 To open PAD for CMPO 0x01
DMA_REQ
INP

#### +

Interrupt
Polarity Window Filter
/DMA
Select IRQ Control Block
COUT_RAW

#### - Control

INM
COUT
To other SOC functions
WINDOW/SAMPLE
1
Sampling Clock 0
CMPO to PAD
Bus Clock Divided Bus Clock
Clock 0 COUTA
1
Prescaler
FILT_PER
0
>0x00
SAMPLE_EN COUT_SEL
Figure 248. Windowed/Resampled mode
This mode of operation results in an unfiltered string of comparator samples where CCR1[FILT_PER] and the bus clock rate
determines the interval between the samples. The following section shows that the configuration for this mode is virtually identical
to that for the Windowed/Filtered mode. The only difference is that the value of CCR1[FILT_CNT] must be 1 in this mode.
The following figure uses the same input stimulus shown in Figure 243 , and adds resampling of COUTA to generate COUT. The
arrows in the figure indicate the time points at which the samples are taken. You can ignore prop delays and latency for clarity.

---

*Page 1751*

Low Power Comparator (LPCMP)
Window
COUT_RAW
COUTA
Sample point
COUT
Figure 249. Windowed/Resampled mode operation
This example demonstrates the operation of the comparator in Windowed/Resampled mode, and does not reflect any specific
application. Based on the sampling rate and window placement, COUT may not see zero-crossing events that the analog
comparator detects. You must carefully consider the sampling period and/or window placement for a given application.
58.3.5.7 Windowed/Filtered mode (#7)
Internal Bus
CMP_NPMD CMP_HPMD HYSTCTR CMP_EN COUT_INV WINDOW_EN FILT_CNT COUT CFR/F_IE DMA_EN CFR/F COUT_PEN
1 To open PAD for CMPO >0x01
DMA_REQ
INP

#### +

Interrupt
Polarity Window Filter
/DMA
Select IRQ Control Block
COUT_RAW

#### - Control

INM
COUT
To other SOC functions
WINDOW/SAMPLE
1
Sampling Clock
0
CMPO to PAD
Bus Clock Divided Bus Clock
Clock 0 COUTA
1
Prescaler
FILT_PER
0
>0x00
SAMPLE_EN COUT_SEL
Figure 250. Windowed/Filtered mode
The only difference in operation between Windowed/Resampled mode (#6) and Windowed/Filtered mode (#7) is that
CCR1[FILT_CNT] is >1, which activates filter operation.
This mode is the most complex mode of operation for the comparator block, as it utilizes both windowing and filtering features. It
also has the highest latency of any of the modes. This is approximately: up to 2 peripheral clock synchronization in the window
function + (( CCR1[FILT_CNT] x CCR1[FILT_PER] ) + 1) x peripheral clock for the filter function.

---

*Page 1752*

Low Power Comparator (LPCMP)

#### 58.3.6 DMA

After DMA is enabled by writing 1 to CCR1[DMA_EN] and interrupt is enabled by writing 1 to IER[CFR_IE] , IER[CFF_IE] , or
both, the corresponding change on COUT forces a DMA transfer request rather than a CPU interrupt. After the DMA completes
the transfer, it sends a transfer completing indicator signal that deasserts the DMA transfer request and clears the flags (both
CSR[CFR] and CSR[CFF] ) to allow a subsequent change on comparator output to occur and forces another DMA request.

#### 58.3.7 Clocking

LPCMP requires the following clocks to operate:
Table 279. LPCMP clocks
Type of clock Description
Bus Controls the access to LPCMP registers and window/
filter function.
Round-robin clock (RCLK) Controls Round-robin trigger mode.

#### 58.3.8 Resets

The global chip reset signal resets LPCMP.

#### 58.3.9 Interrupts

After the corresponding IER becomes 1, CSR[CFR] , CSR[CFF] , and CSR[RRF] can generate an interrupt, assuming that
CCR1[DMA_EN] is not 1. You can clear either the flag or IER to deassert the interrupt.

#### 58.4 External signal descriptions

Below table introduces external signals.
Table 280. External signal descriptions
Signal Description I/O
CMPO Filtered or unfiltered comparator output O
Input_Analog_Channels Analog input channels (see the chip-specific information for more I
on the connections).
VREFH_EXT External reference voltage for the CMP-DAC (see the chip-specific I
information for more on the connections).
RR_ACTIVE Round-robin trigger mode enabled. O

#### 58.5 Initialization

You can enable LPCMP by writing 1 to CCR0[CMP_EN] , and then configuring the control registers ( CCR1 , CCR2 , DCR , and
so on).
To disable LPCMP, write 0 to CCR0[CMP_EN] . Switching operation modes or changing control register fields on-the-fly (when
CCR0[CMP_EN] is set to 1) may cause noise on the COUT or COUTA signals. To avoid unwanted signal noise, you must ensure
to disable the module before switching modes or changing control fields.
The time required to stabilize COUT is the power-on delay of the comparators plus the largest propagation delay from a selected
analog source through the analog comparator, windowing function, and filter (see the Comparator and 8-bit DAC electrical
specifications section of LPCMP datasheet for more information on propagation delay and power-up delay). Table 278 specifies
the delay that the windowing and filter function causes.

---

*Page 1753*

Low Power Comparator (LPCMP)
During operation, you must always consider the propagation delay of the selected data paths. It can take many bus clock cycles
for COUT and CSR[CFR] / CSR[CFF] to reflect an input change or a configuration change to one of the components involved in
the data path.

#### 58.6 Application information

#### 58.6.1 Round-robin trigger mode programing recommendation

Configure the Round-robin trigger mode as follows:
1. Configure the comparison cycles by RRCR0[RR_NSAM] . Note: It is a mandatory request that the round robin cycling
period must set longer than the time that all the active channels complete the specified comparison cycles set by
RRCR0[RR_NSAM] .
2. Configure CMP initialization delay by RRCR0[RR_INITMOD] . Note: In programming RRCR0[RR_INITMOD] , the
RR_INITMOD x round robin clock period must be longer than the initialization delay, see the LPCMP datasheet for
more information.
3. Configure RRCR1[FIXP] to select the fixed port of CMP and
a. If you use one input channel to compare with other channels, configure RRCR1[FIXCH] to select the fixed
channel.
b. If you use DAC output to compare with input channels, configure CCR2[INPSEL] or CCR2[INMSEL] (according to
RRCR1[FIXP] ) to select the DAC output.
4. Configure channels for comparison by RRCR1[RR_CHnEN] .
5. Write RRCSR[RR_CHnOUT] to define the pre-set state of channel n.
6. Clear channel flags RRSR[RR_CHnF] .
7. Enable round robin interrupt by IER[RRF_IE] (disable IER[CFR_IE] and IER[CFF_IE] ).
8. Enable round-robin trigger mode by setting RRCR0[RR_EN] to 1.
9. Enable comparator by setting CCR0[CMP_EN] to 1.

#### 58.6.2 Round-robin clock (RCLK) frequency requirement

(1) RCLK high frequency limit
RCLK high frequency limit depends on two facts:
1. The analog CMP and DAC initialization time (see the chip data sheet for more information on the initialization time.)
• RRCR0[RR_INITMOD] provides a maximum 63 RCLK cycles for the analog CMP and DAC initialization.
• RCLK must be slow to assure: 63 * (1/f RCLK ) > T initialization , where f RCLK is in MHz, and T initialization is in microsecond.
• so f RCLK < 63 / T initialization
• Example: T initialization = 40 μs, then f RCLK should be smaller than 1.575 MHz.
2. The analog CMP propagation delay (see the Comparator and 8-bit DAC electrical specifications section of LPCMP
datasheet for more information on the CMP propagation delay.)
• RRCR0[RR_NSAM] provides a maximum 4 RCLK cycles for the analog CMP propagation delay.
• RCLK must be slow to assure: 4 * (1/f RCLK ) > T propagation , where f RCLK is in MHz, and T propagation is in microsecond.
• f RCLK < 4 / T propagation
• Example: T propagation = 0.1 μs, then f RCLK must be smaller than 40 MHz.
(2) RCLK low frequency limit

---

*Page 1754*

Low Power Comparator (LPCMP)
In theory, RCLK frequency has no low limit. But the lower the RCLK frequency, the longer the scan time. Therefore, the lower limit
of the RCLK frequency depends on the system application.

#### 58.7 LPCMP register descriptions

The memory map comprises of 32-bit aligned registers, which you can access via 8-, 16- or 32-bit reads and 32-bit write.
Attempted accesses using unsupported write data sizes, writes to read-only resources, or to reserved spaces terminate with an
error. Read access to reserved address generates a transfer error and the read data bus shows all 0s.

#### 58.7.1 LPCMP memory map

LPCMP_0 base address: 4037_0000h
LPCMP_1 base address: 4037_4000h
LPCMP_2 base address: 404E_8000h
Offset Register Access Reset value
Width
(In bits)
0h Version ID (VERID) 32 R 0100_0001h
4h Parameter (PARAM) 32 R 0000_0002h
8h Comparator Control Register 0 (CCR0) 32 RW 0000_0002h
Ch Comparator Control Register 1 (CCR1) 32 RW 0000_0000h
10h Comparator Control Register 2 (CCR2) 32 RW 0000_0000h
18h DAC Control (DCR) 32 RW 0000_0000h
1Ch Interrupt Enable (IER) 32 RW 0000_0000h
20h Comparator Status (CSR) 32 RW 0000_0000h
24h Round Robin Control Register 0 (RRCR0) 32 RW 0000_0000h
28h Round Robin Control Register 1 (RRCR1) 32 RW 0000_0000h
2Ch Round Robin Control and Status (RRCSR) 32 RW 0000_0000h
30h Round Robin Status (RRSR) 32 RW 0000_0000h

#### 58.7.2 Version ID (VERID)

Offset
Register Offset
VERID 0h
Function
Contains version numbers for the module design and feature set.

---

*Page 1755*

Low Power Comparator (LPCMP)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R MAJOR MINOR
W
Reset 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R FEATURE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1
Fields
Field Function
31-24 Major Version Number
MAJOR Returns the major version number for the module design.
23-16 Minor Version Number
MINOR Returns the minor version number for the module design.
15-0 Feature Specification Number
FEATURE Returns the feature set number.
0000_0000_0000_0001b - Round robin feature

#### 58.7.3 Parameter (PARAM)

Offset
Register Offset
PARAM 4h
Function
Contains parameter values that are implemented in the module.

---

*Page 1756*

Low Power Comparator (LPCMP)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DAC_RES
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0
Fields
Field Function
Reserved
31-4
—
3-0 DAC Resolution
DAC_RES Indicates supported DAC resolutions.
NOTE
All other bit field values are reserved.
0000b - 4-bit DAC
0001b - 6-bit DAC
0010b - 8-bit DAC
0011b - 10-bit DAC
0100b - 12-bit DAC
0101b - 14-bit DAC
0110b - 16-bit DAC

#### 58.7.4 Comparator Control Register 0 (CCR0)

Offset
Register Offset
CCR0 8h
Function
Contains configuration options for enabling the analog comparator and the DAC .

---

*Page 1757*

Low Power Comparator (LPCMP)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
LINKE CMP_ CMP_
N STO ... EN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0
Fields
Field Function
Reserved
31-3
—
2 CMP-to-DAC Link Enable
LINKEN Enables the CMP-to-DAC link.
0b - Disable: enabling or disabling the DAC is independent from enabling or disabling the CMP.
1b - Enable: enabling/disabling DAC is controlled by the CMP_EN bit instead of DCR[DAC_EN].
Also, when the CMP is auto-disabled because software selects the same signal for both the plus
and minus comparator inputs, the DAC is disabled too.
1 Comparator STANDBY Mode Enable
CMP_STOP_E Enables the analog comparator or the DAC when the module is in STANDBY mode.
N
NOTE
This field has no effect in Round-robin Trigger mode.
0b - Disables the analog comparator regardless of CMP_EN.
1b - Allows CMP_EN to enable the analog comparator.
0 Comparator Enable
CMP_EN Enables the analog comparator.
NOTE
When CCR0[LINKEN]=1, CMP_EN also controls the enabling/disabling of the DAC instead
of DCR[DAC_EN].
0b - Disable (The analog logic remains off and consumes no power.)
1b - Enable

---

*Page 1758*

Low Power Comparator (LPCMP)

#### 58.7.5 Comparator Control Register 1 (CCR1)

Offset
Register Offset
CCR1 Ch
Function
Contains configuration options for the comparator operation, such as enabling Sampling or Windowing mode.
NOTE
You cannot enable Sampling and Windowing modes both at the same time. Sampling mode takes precedence over
Windowing mode. If you write 1 to both SAMPLE_EN and WINDOW_EN , only SAMPLE_EN becomes 1.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
FILT_PER FILT_CNT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
WIND WIND COUT COUT COUT COUT COUT DMA_ SAMP WIND
EVT_SEL
OW_ ... OW_ ... A_OW A_O ... _PEN _SEL _INV EN LE_ ... OW_ ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 Filter Sample Period
FILT_PER Specifies the sampling period (in bus clock cycles) of the comparator output filter. Programming this field to
00h bypasses the filter. See Functional description for more information on filter programming and latency.
NOTE
FILT_PER has no effect in Sampling mode ( CCR1[SAMPLE_EN] = 1).
Reserved
23-19
—
18-16 Filter Sample Count
FILT_CNT Specifies the number of consecutive samples that must agree before the comparator output filter
accepts the sample as a new valid output state. See Functional description for more information on
filter programming and latency.
Table continues on the next page...

---

*Page 1759*

Low Power Comparator (LPCMP)
Table continued from the previous page...
Field Function
000b - Filter is bypassed: COUT = COUTA
001b - 1 consecutive sample (Comparator output is simply sampled.)
010b - 2 consecutive samples
011b - 3 consecutive samples
100b - 4 consecutive samples
101b - 5 consecutive samples
110b - 6 consecutive samples
111b - 7 consecutive samples
Reserved
15-14
—
Reserved
13-12
—
11-10 CMPO Event Select
EVT_SEL Selects which CMPO signal edge (rising, falling, or both) defines a CMPO event.
NOTE
Valid only in Windowing mode.
00b - Rising edge
01b - Falling edge
1xb - Both edges
9 CMPO Event Window Close
WINDOW_CLS Enables a CMPO event (defined as a CMPO rising edge, falling edge, or both) to close an active window.
See EVT_SEL to configure the CMPO event.
NOTE
The WINDOW signal has to go to zero and back to one again to re-activate the window.
Valid only in Windowing mode.
0b - CMPO event cannot close the window
1b - CMPO event can close the window
8 WINDOW/SAMPLE Signal Invert
WINDOW_INV Inverts the window/sample signal.
0b - Do not invert
1b - Invert
7 COUTA Output Level for Closed Window
Table continues on the next page...

---

*Page 1760*

Low Power Comparator (LPCMP)
Table continued from the previous page...
Field Function
COUTA_OW Defines the COUTA signal value when the window is closed.
NOTE
Valid only in Windowing mode and when COUTA_OWEN=1.
0b - COUTA is 0
1b - COUTA is 1
6 COUTA_OW Enable
COUTA_OWEN Enables the COUTA signal value to be defined by COUTA_OW when the window is closed.
NOTE
Valid only in Windowing mode.
0b - COUTA holds the last sampled value.
1b - Enables the COUTA signal value to be defined by COUTA_OW.
5 Comparator Output Pin Enable
COUT_PEN Enables the comparator output to become an available signal option for a selected package pin.
0b - Not available
1b - Available
4 Comparator Output Select
COUT_SEL Selects which comparator output option, COUT or COUTA, to use for CMPO.
0b - Use COUT (filtered)
1b - Use COUTA (unfiltered)
3 Comparator Invert
COUT_INV Selects the polarity of the analog comparator function, affecting the value driven to the COUT output (on
both the chip pin and as CSR[COUT] ) when CCR0[CMP_EN] is 0.
NOTE
COUT_INV has no effect in Trigger mode.
0b - Do not invert
1b - Invert
2 DMA Enable
DMA_EN Enables DMA transfers triggered from the LPCMP module. After this field and the corresponding
interrupt enable field becomes 1, a DMA request is asserted when CFR or CFF becomes 1.
0b - Disable
1b - Enable
1 Sampling Enable
Table continues on the next page...

---

*Page 1761*

Low Power Comparator (LPCMP)
Table continued from the previous page...
Field Function
Enables Sampling mode.
SAMPLE_EN
0b - Disable
1b - Enable
0 Windowing Enable
WINDOW_EN Enables Windowing mode.
NOTE
Valid only when SAMPLE_EN = 0.
0b - Disable
1b - Enable

#### 58.7.6 Comparator Control Register 2 (CCR2)

Offset
Register Offset
CCR2 10h
Function
Contains the configuration options for the comparator operation, such as selecting the plus and minus comparator inputs and the
hysteresis levels.
NOTE
When an inappropriate operation selects the same signal for both the plus and minus comparator inputs, the analog
comparator automatically shuts down (regardless of CMP_EN ) to prevent itself from becoming a noise generator.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0
INMSEL INPSEL MSEL PSEL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
OFFS CMP_
HYSTCTR
ET HPMD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1762*

Low Power Comparator (LPCMP)
Fields
Field Function
Reserved
31-30
—
29-28 Input Minus Select
INMSEL Selects the minus input of the comparator.
NOTE
These selections connect directly to the minus input of the comparator.
00b - IN0: from the 8-bit DAC output
01b - IN1: from the analog 8-1 mux
10b - Reserved
11b - Reserved
Reserved
27-26
—
25-24 Input Plus Select
INPSEL Selects the plus input of the comparator.
NOTE
These selections connect directly to the plus input of the comparator.
00b - IN0: from the 8-bit DAC output
01b - IN1: from the analog 8-1 mux
10b - Reserved
11b - Reserved
Reserved
23
—
22-20 Minus Input MUX Select
MSEL Selects the input used for the negative mux. See the chip-specific LPCMP information for more
on connections.
NOTE
MSEL has no effect in Trigger mode.
000b - Input channel 0
001b - Input channel 1
010b - Input channel 2
011b - Input channel 3
Table continues on the next page...

---

*Page 1763*

Low Power Comparator (LPCMP)
Table continued from the previous page...
Field Function
100b - Input channel 4
101b - Input channel 5
110b - Input channel 6
111b - Input channel 7
Reserved
19
—
18-16 Plus Input MUX Select
PSEL Selects the input used for the positive mux. See the chip-specific LPCMP information for more
on connections.
NOTE
PSEL has no effect in Trigger mode.
000b - Input channel 0
001b - Input channel 1
010b - Input channel 2
011b - Input channel 3
100b - Input channel 4
101b - Input channel 5
110b - Input channel 6
111b - Input channel 7
Reserved
15-6
—
5-4 Comparator Hysteresis Control
HYSTCTR Selects the level of internally generated hysteresis for the comparator output.
NOTE
This applies to the comparator hard block.
00b - Level 0: Analog comparator hysteresis 0 mV.
01b - Level 1: Analog comparator hysteresis 10 mV.
10b - Level 2: Analog comparator hysteresis 20 mV.
11b - Level 3: Analog comparator hysteresis 30 mV.
Reserved
3
—
2 Comparator Offset Control
Table continues on the next page...

---

*Page 1764*

Low Power Comparator (LPCMP)
Table continued from the previous page...
Field Function
OFFSET Selects the level of internally generated voltage offset for the comparator output. See the chip data sheet
to get the specific values for each offset level.
0b - Level 0: The hysteresis selected by HYSTCTR is valid for both directions (rising and falling).
1b - Level 1: Hysteresis does not apply when INP (input-plus) crosses INM (input-minus) in the
rising direction or when INM crosses INP in the falling direction. Hysteresis still applies for INP
crossing INM in the falling direction.
Reserved
1
—
0 CMP High Power Mode Select
CMP_HPMD Selects Low or High Power(Speed) mode for the comparator.
0b - Low power (speed) comparison mode
1b - High power (speed) comparison mode

#### 58.7.7 DAC Control (DCR)

Offset
Register Offset
DCR 18h
Function
Contains the configuration options to enable the DAC.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
DAC_DATA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
VRSE DAC_
L EN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1765*

Low Power Comparator (LPCMP)
Fields
Field Function
Reserved
31-24
—
23-16 DAC Output Voltage Select
DAC_DATA Selects the DAC output (DACO) voltage from one of 256 distinct levels by configuring the value of
DAC_DATA. The DACO ranges from Vin/256 to Vin.
NOTE
DACO = (Vin/256) * (DAC_DATA + 1)
Reserved
15-9
—
8 DAC Reference High Voltage Source Select
VRSEL Selects the high voltage reference source for the Vin supply of the DAC's resistor ladder network. See
the chip-specific LPCMP information for the source of vrefh0 and vrefh1.
0b - VREFH0
1b - VREFH1
Reserved
7-2
—
Reserved
1
—
0 DAC Enable
DAC_EN Enables the DAC. When disabled, power-down the DAC to conserve power.
NOTE
You can control the link from the CMP enable to the DAC enable by setting
up CCR0[LINKEN] .
0b - Disable
1b - Enable

#### 58.7.8 Interrupt Enable (IER)

Offset
Register Offset
IER 1Ch

---

*Page 1766*

Low Power Comparator (LPCMP)
Function
Provides enable fields for the comparator and round-robin flag interrupts.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
RRF_ CFF_ CFR_
IE IE IE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-3
—
2 Round-Robin Flag Interrupt Enable
RRF_IE Enables or disables the round-robin flag interrupt.
0b - Disables the round-robin flag interrupt.
1b - Enables the round-robin flag interrupt when the comparison result changes for a given
channel.
1 Comparator Flag Falling Interrupt Enable
CFF_IE Enables or disables the comparator flag falling interrupt.
0b - Disables the comparator flag falling interrupt.
1b - Enables the comparator flag falling interrupt when CFF is set.
0 Comparator Flag Rising Interrupt Enable
CFR_IE Enables or disables the comparator flag rising interrupt.
0b - Disables the comparator flag rising interrupt.
1b - Enables the comparator flag rising interrupt when CFR is set.

#### 58.7.9 Comparator Status (CSR)

Offset
Register Offset
CSR 20h

---

*Page 1767*

Low Power Comparator (LPCMP)
Function
Indicates comparator status, including COUT , CFF , CFR , and RRF .
NOTE
LPCMP may output a glitch and affect the value of CSR[CFF] and CSR[CFR] at the moment of enabling CMP.
In order to ensure correctness, it is recommended to write one to clear (W1C) CSR[CFF] and CSR[CFR] before
further configuring CMP.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 COUT 0 RRF CFF CFR
W W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-9
—
8 Analog Comparator Output
COUT Returns the current value of the analog comparator output when read. This field resets to 0 and reads
as CCR1[COUT_INV] after the analog comparator module disables when CCR0[CMP_EN] = 0. Writing
to this field is ignored.
Reserved
7-3
—
2 Round-Robin Flag
RRF Detects when any channel's last comparison result is different from the pre-set value in Trigger mode.
Write 1 to clear this field. This field clears when CCR0[CMP_EN] or RRCR0[RR_EN] is not 1.
0b - Not detected
1b - Detected
1 Analog Comparator Flag Falling
CFF Detects when a falling edge on COUT occurs. Write 1 to clear this field when CCR1[DMA_EN] is
disabled. If CCR1[DMA_EN] is enabled, the flag automatically clears after DMA is done. This field clears
when CCR0[CMP_EN] is not 1.
Table continues on the next page...

---

*Page 1768*

Low Power Comparator (LPCMP)
Table continued from the previous page...
Field Function
0b - Not detected
1b - Detected
0 Analog Comparator Flag Rising
CFR Detects when a rising edge on COUT occurs. Write 1 to clear this field when CCR1[DMA_EN] is
disabled. If CCR1[DMA_EN] is enabled, the flag automatically clears after DMA is done. This field clears
when CCR0[CMP_EN] is not 1.
0b - Not detected
1b - Detected

#### 58.7.10 Round Robin Control Register 0 (RRCR0)

Offset
Register Offset
RRCR0 24h
Function
Contains configuration options for the round-robin operation, such as enabling it and specifying the initialization delay.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
RR_INITMOD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0
RR_
RR_NSAM
EN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-22
—
Table continues on the next page...

---

*Page 1769*

Low Power Comparator (LPCMP)
Table continued from the previous page...
Field Function
21-16 Initialization Delay Modulus
RR_INITMOD Specifies the number of round-robin clock cycles that determines the comparator and DAC initialization
delay specified in the chip datasheet. Calculate the initialization delay as RR_INITMOD * (round-robin
clock period).
For example, if the initialization delay is 80us and the round-robin clock is 100kHz, program RR_INITMOD
to be 80us/10us = 8.
00_0000b - 63 cycles (same as 111111b)
00_0001b-11_1111b - 1 to 63 cycles
Reserved
15-14
—
Reserved
13-12
—
Reserved
11-10
—
9-8 Number of Sample Clocks
RR_NSAM Specifies the number of the round-robin clock cycles to wait after scanning the active channel before
sampling the channel's comparison result. After the next cycle of the round-robin clock, the sampling
takes place RR_NSAM clocks later.
00b - 0 clock
01b - 1 clock
10b - 2 clocks
11b - 3 clocks
Reserved
7-2
—
Reserved
1
—
0 Round-Robin Enable
RR_EN Enables the round-robin operation.
0b - Disable
1b - Enable

---

*Page 1770*

Low Power Comparator (LPCMP)

#### 58.7.11 Round Robin Control Register 1 (RRCR1)

Offset
Register Offset
RRCR1 28h
Function
Contains configuration options for the round-robin operation, such as enabling individual channels to participate.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
FIXCH FIXP
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
RR_C RR_C RR_C RR_C RR_C RR_C RR_C RR_C
H7EN H6EN H5EN H4EN H3EN H2EN H1EN H0EN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-23
—
22-20 Fixed Channel Select
FIXCH Selects which channel in the mux port to fix for a given round-robin trigger mode application.
000b - Channel 0
001b - Channel 1
010b - Channel 2
011b - Channel 3
100b - Channel 4
101b - Channel 5
110b - Channel 6
111b - Channel 7
Reserved
19-17
—
Table continues on the next page...

---

*Page 1771*

Low Power Comparator (LPCMP)
Table continued from the previous page...
Field Function
16 Fixed Port
FIXP Fixes an analog mux port (plus or minus) for round-robin trigger mode. The inputs to the non-fixed port
sweep during each round.
0b - Fix the plus port. Sweep only the inputs to the minus port.
1b - Fix the minus port. Sweep only the inputs to the plus port.
Reserved
15-8
—
7-0 Channel n Input Enable in Trigger Mode
RR_CHnEN Enables channel n of the non-fixed mux port to check its voltage value when in Trigger mode.
NOTE
RR_CHnEN has no effect when the same channel is selected as the reference voltage.
0b - Disable
1b - Enable

#### 58.7.12 Round Robin Control and Status (RRCSR)

Offset
Register Offset
RRCSR 2Ch
Function
Contains the latest comparison results of the individual channels with the fixed mux port. It also allows you to define the
pre-set state for each channel.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
RR_C RR_C RR_C RR_C RR_C RR_C RR_C RR_C
H7O ... H6O ... H5O ... H4O ... H3O ... H2O ... H1O ... H0O ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1772*

Low Power Comparator (LPCMP)
Fields
Field Function
Reserved
31-8
—
7-0 Comparison Result for Channel n
RR_CHnOUT Returns the latest comparison result for channel n when read and defines the pre-set state for channel n
when written to.

#### 58.7.13 Round Robin Status (RRSR)

Offset
Register Offset
RRSR 30h
Function
Contains individual channel flags that indicates when a channel's last comparison result is different from its pre-set value.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
RR_ RR_ RR_ RR_ RR_ RR_ RR_ RR_
R 0
CH7F CH6F CH5F CH4F CH3F CH2F CH1F CH0F
W W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-8
—
7-0 Channel n Input Changed Flag
RR_CHnF Indicates when the corresponding channel's last comparison result is different from its pre-set value.
Table continues on the next page...

---

*Page 1773*

Low Power Comparator (LPCMP)
Table continued from the previous page...
Field Function
NOTE
To clear a flag, write a 1 to it.
0b - No different
1b - Different

#### 58.8 Glossary

CMP Comparator
DAC Digital-to-analog convertor
ANMUX Analog multiplexer

---

*Page 1774*

