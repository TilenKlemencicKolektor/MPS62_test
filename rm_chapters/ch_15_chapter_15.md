<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 15 -->

# Chapter 15

# Interrupt Monitor (INTM)

#### 15.1 Overview

INTM provides a mechanism to monitor the latency of the responses on interrupt requests to ensure that the processing of these
critical interrupts executes within the expected time frame, increasing the reliability of the device.

#### 15.1.1 Block diagram

The following block diagram shows the major registers involved in monitoring the interrupt sources.
Clock
Interrupt INTM_TIMERM0[TIMER]
Latency
lines Monitor 0 error
>
. counter
.
. INTM_LATENCYM0[LAT]
INTM_IRQSEL0[IRQ] INTM_TIMERM0[TIMER] INTM_TIMERM0[LAT]
Monitor 0
Monitor 1
Monitor 2
...
Monitor n
Figure 34. INTM block diagram

#### 15.1.2 Features

INTM supports the following features:
• 4 programmable interrupt monitors
• Programmable interrupt source per monitor
• Programmable 24-bit maximum latency counter per monitor
• Timer expired status bit per monitor
• One interrupt acknowledge for all monitors

#### 15.2 Functional description

Interrupt Request Select for Monitor a (INTM_IRQSEL0 - INTM_IRQSEL3) provides selection of the source, Monitor Mode
(INTM_MM) enables the monitor, Interrupt Acknowledge (INTM_IACK) captures the event when the interrupt is acknowledged,
Interrupt Latency for Monitor a (INTM_LATENCY0 - INTM_LATENCY3) can be programmed to trigger a monitor error when a
threshold value is exceeded, Status for Monitor a (INTM_STATUS0 - INTM_STATUS3) is available to read the monitor error over
peripheral bus.

---

*Page 366*

Interrupt Monitor (INTM)
NOTE
INTM supports only level-type interrupts. Hence, the pulse-type interrupts are converted into level-type interrupts
for INTM.

#### 15.2.1 Clocking

This module has no clocking considerations.

#### 15.2.2 Interrupts

This module has no interrupts.

#### 15.3 External signals

This module has no external signals.

#### 15.4 Initialization

To monitor a subset of interrupt sources:
1. Program Interrupt Request Select for Monitor a (INTM_IRQSEL0 - INTM_IRQSEL3) with a value corresponding to the
interrupt source number to be monitored.
• You can monitor only defined interrupt sources.
2. Program Interrupt Latency for Monitor a (INTM_LATENCY0 - INTM_LATENCY3) to the maximum expected latency time
for each monitored interrupt source.
3. Write 1 to INTM_MM[MM] to monitor the registers.
4. During the interrupt service routine, write the IRQ of the ISR to Interrupt Acknowledge (INTM_IACK) .
5. If the maximum expected latency time exceeds the limit defined in Interrupt Latency for Monitor a (INTM_LATENCY0 -
INTM_LATENCY3) , then an error indication is sent to Fault Collection Control Unit(FCCU). You can also read Status for
Monitor a (INTM_STATUS0 - INTM_STATUS3) to see this information.
To clear an error condition, write 0 to the corresponding INTM_TIMERa[TIMER] field for which the interrupt source exceeded the
programmed latency value. In case the source is unknown, NXP recommends you to write 0 to all INTM_TIMERa[TIMER] fields.

#### 15.5 INTM register descriptions

INTM allows you to set a maximum timer count for the interrupt latency from interrupt request to interrupt acknowledge. This
mechanism monitors the latency of interrupt sources to ensure that these critical interrupts execute within the expected time
frame, increasing the reliability of the chip. The hardware for this function is isolated in its own hierarchy to decrease the risk of
fault interference.
An error indication is sent to FCCU if the interrupt processing latency exceeds the programmed threshold of the expected latency.
The error indication can be read from INTM_STATUSa[STATUS] .

#### 15.5.1 INTM memory map

INTM base address: 4027_C000h
Offset Register Access Reset value
Width
(In bits)
0h Monitor Mode (INTM_MM) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 367*

Interrupt Monitor (INTM)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
4h Interrupt Acknowledge (INTM_IACK) 32 W 0000_0000h
8h Interrupt Request Select for Monitor 0 (INTM_IRQSEL0) 32 RW 0000_0000h
Ch Interrupt Latency for Monitor 0 (INTM_LATENCY0) 32 RW 0000_0000h
10h Timer for Monitor 0 (INTM_TIMER0) 32 RW 0000_0000h
14h Status for Monitor 0 (INTM_STATUS0) 32 R 0000_0000h
18h Interrupt Request Select for Monitor 1 (INTM_IRQSEL1) 32 RW 0000_0000h
1Ch Interrupt Latency for Monitor 1 (INTM_LATENCY1) 32 RW 0000_0000h
20h Timer for Monitor 1 (INTM_TIMER1) 32 RW 0000_0000h
24h Status for Monitor 1 (INTM_STATUS1) 32 R 0000_0000h
28h Interrupt Request Select for Monitor 2 (INTM_IRQSEL2) 32 RW 0000_0000h
2Ch Interrupt Latency for Monitor 2 (INTM_LATENCY2) 32 RW 0000_0000h
30h Timer for Monitor 2 (INTM_TIMER2) 32 RW 0000_0000h
34h Status for Monitor 2 (INTM_STATUS2) 32 R 0000_0000h
38h Interrupt Request Select for Monitor 3 (INTM_IRQSEL3) 32 RW 0000_0000h
3Ch Interrupt Latency for Monitor 3 (INTM_LATENCY3) 32 RW 0000_0000h
40h Timer for Monitor 3 (INTM_TIMER3) 32 RW 0000_0000h
44h Status for Monitor 3 (INTM_STATUS3) 32 R 0000_0000h

#### 15.5.2 Monitor Mode (INTM_MM)

Offset
Register Offset
INTM_MM 0h
Function
Enables the cycle count timer on a monitored interrupt request for comparison to the latency register.

---

*Page 368*

Interrupt Monitor (INTM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved MM
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-1 Reserved
—
0 Monitor Mode
MM Controls whether the INTM monitors the latency of an interrupt response.
0b - Disable
1b - Enable

#### 15.5.3 Interrupt Acknowledge (INTM_IACK)

Offset
Register Offset
INTM_IACK 4h
Function
Acknowledges the interrupt processing.
The Interrupt service routine writes to this register after the register acknowledges interrupt processing. This write operation must
provide the number of the processed interrupt, which is compared with the interrupt request number in each Interrupt Request
Select for Monitor a (INTM_IRQSEL0 - INTM_IRQSEL3) and stops the corresponding Timer for Monitor a (INTM_TIMER0 -
INTM_TIMER3) when it found a match.

---

*Page 369*

Interrupt Monitor (INTM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved
W IRQ
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-10 Reserved
—
9-0 Interrupt Request
IRQ Specifies the interrupt request number to stop Timer for Monitor a (INTM_TIMER0 - INTM_TIMER3) .

#### 15.5.4 Interrupt Request Select for Monitor a (INTM_IRQSEL0 - INTM_IRQSEL3)

Offset
Register Offset
INTM_IRQSEL0 8h
INTM_IRQSEL1 18h
INTM_IRQSEL2 28h
INTM_IRQSEL3 38h
Function
Indicates which interrupt request must be monitored or checked.

---

*Page 370*

Interrupt Monitor (INTM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved IRQ
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-10 Reserved
—
9-0 Interrupt Request
IRQ Selects the interrupt request number to monitor.

#### 15.5.5 Interrupt Latency for Monitor a (INTM_LATENCY0 - INTM_LATENCY3)

Offset
Register Offset
INTM_LATENCY0 Ch
INTM_LATENCY1 1Ch
INTM_LATENCY2 2Ch
INTM_LATENCY3 3Ch
Function
Indicates the maximum time before an error signal is asserted for a detected interrupt request to interrupt acknowledge.

---

*Page 371*

Interrupt Monitor (INTM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved LAT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
LAT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 Reserved
—
23-0 Latency
LAT Specifies the maximum number of INTM clock cycles allowed for the monitored interrupt request.
Maximum programmed value must not exceed FF_FFFDh to allow for proper timer error capture.

#### 15.5.6 Timer for Monitor a (INTM_TIMER0 - INTM_TIMER3)

Offset
Register Offset
INTM_TIMER0 10h
INTM_TIMER1 20h
INTM_TIMER2 30h
INTM_TIMER3 40h
Function
Counts the number of INTM clock cycles for a detected interrupt request to an acknowledged interrupt processing.
Initializes to 1 in following conditions:
• Interrupt Latency for Monitor a (INTM_LATENCY0 - INTM_LATENCY3) is nonzero
• Monitor error is not asserted
• Zero to one transition on the monitored interrupt request
Following are the conditions to enable Timer for Monitor a (INTM_TIMER0 - INTM_TIMER3) :
• INTM_MM = 1 and zero-to-one transition on the monitored interrupt source
Following are the conditions to disable Timer for Monitor a (INTM_TIMER0 - INTM_TIMER3) :

---

*Page 372*

Interrupt Monitor (INTM)
• Reset
• INTM_MM = 1 and monitor error
• INTM_MM = 1 and monitored interrupt request is not asserted
• Interrupt acknowledge for corresponding interrupt request
• After the timer is enabled, writing 0 to INTM_MM[MM] has no effect.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved TIMER
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TIMER
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 Reserved
—
23-0 Timer
TIMER Counts the number of INTM clock cycles up to 24 bits of resolution.

#### 15.5.7 Status for Monitor a (INTM_STATUS0 - INTM_STATUS3)

Offset
Register Offset
INTM_STATUS0 14h
INTM_STATUS1 24h
INTM_STATUS2 34h
INTM_STATUS3 44h
Function
Defines the monitor status. Indicates whether Timer for Monitor a (INTM_TIMER0 - INTM_TIMER3) value has exceeded Interrupt
Latency for Monitor a (INTM_LATENCY0 - INTM_LATENCY3) value. You can clear the monitor status by writing 0 to the
corresponding INTM_TIMERa[TIMER] .

---

*Page 373*

Interrupt Monitor (INTM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
STATU
R
S
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-1 Reserved
—
0 Monitor status
STATUS Indicates whether Timer for Monitor a (INTM_TIMER0 - INTM_TIMER3) value has exceeded the Interrupt
Latency for Monitor a (INTM_LATENCY0 - INTM_LATENCY3) value.
0b - Did not exceed
1b - Exceeded

---

*Page 374*

