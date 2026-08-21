<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 54 -->

# Chapter 54

# Clock Monitoring Unit – Frequency Meter (CMU_FM)

#### 54.1 Chip-specific CMU_FM information

#### 54.1.1 CMU_FM configuration

The device consists of a robust clock monitoring architecture targeted to monitor various clock nodes to ensure device clock
robustness. There are 6 clock monitoring units present in the device out of which 2 are of type CMU_FM. See 'Clock monitoring'
section in Clocking chapter for details on device clock monitoring architecture.
NOTE
CMU metering interrupt is not generated if the clock which is being metered is lost. A timeout status flag,
CMU.SR[FMTO] is updated in this case.
NOTE
Software must ensure that CMU1 interrupt is serviced within POR_WDG timeout. In absence of CMU1 interrupt
servicing, the POR_WDG detects this as an FIRC failure and initiates a POR recovery.

#### 54.2 Overview

CMU_FM measures the frequency of the metered clock ( metered_clock ) using another clock as reference. This reference clock
( reference_clock ) must be within documented limits for correct CMU_FM operation. CMU_FM operation is only guaranteed if a
reference clock is within normal operating parameters.

#### 54.2.1 Block diagram

The CMU_FM counts the clock cycles of a metered clock in the user programmable time duration n number of clock cycles of the
reference clock. After completion of a metered clock operation, a register field sets, indicating the operation has completed. The
count of the metered clock also updates in the Status Register (SR) after completion of a metered clock operation.
Metering window
Enable
Reference counter
reference_clock
Control logic SR[FMC]
metered_clock Meter counter
Figure 200. Block diagram

#### 54.2.2 Features

Features of the CMU_FM are:
• Programmable duration of reference_clock cycles
• Maskable interrupt generation when frequency metering completes
• Timeout feature indicates loss of metered_clock

---

*Page 1535*

Clock Monitoring Unit – Frequency Meter (CMU_FM)

#### 54.3 Functional description

The frequency meter operation initiates when software writes GCR[FME] = 1. When the operation starts, hardware writes
SR[RS] = 1. Safety critical applications must poll SR[RS] to determine when metering operation starts.
Frequency metering window involves these operations:
1. Stage 1 — The reference clock counter runs for RCCR[REF_CNT] cycles of reference_clock . The metered clock
counter runs in parallel for the same time duration as the reference_clock counter.
2. Stage 2 — At the end of stage 1, SR[FMC] = 1, SR[MET_CNT] updates with the metered_clock count, and CMU_FM
writes GCR[FME] = 0.

#### 54.3.1 Clocking

The following table lists the clock sources for this module.
Table 250. Clock description
Clock I/O Description
Clock signal that the module uses as a
reference_clock I
reference to evaluate metered_clock
Clock signal on which frequency
metered_clock I
metering is performed
bus_clock I Clock signal on which register read/write
operation is performed
NOTE
See the Clocking chapter for details of metered and reference clocks used on this chip.

#### 54.3.2 Reset

This module has the following reset sources.
• Global hard reset input
• Reset port synchronized to reference clock domain input
• Reset port synchronized to monitored clock domain input

#### 54.4 Programming guidelines

#### 54.4.1 Programming RCCR[REF_CNT]

RCCR[REF_CNT] defines the duration of the metering operation in number of reference_clock cycles. The minimum
RCCR[REF_CNT] value can be calculated using the following formula:
reference_clock reference_clock
Minimum RCCR [REF_CNT] = CEILING value of MAX 3 X , 8 + 5 X
bus_clock monitored_clock
...where:
• f reference_clock is the frequency of the reference clock
• f bus_clock is the frequency of the bus clock
• f metering_clock is the frequency of the metered clock
Example to determine RCCR[REF_CNT] :

---

*Page 1536*

Clock Monitoring Unit – Frequency Meter (CMU_FM)
• f bus_clock = 16 MHz
• f reference_clock = 8 MHz
• f metered_clock = 48 MHz
Formula #1:
reference_clock
3 X
bus_clock
Inserting the values: 3 × (8 / 16) = 1.5
Formula #2:
reference_clock
8 + 5 X
monitored_clock
Inserting the values: 8 + 5 × (8 / 48) ≈ 8.83
MAX (1.5, 8.83): 8.83
CEILING value of MAX: 9
Therefore, minimum RCCR[REF_CNT] : 9
Programmed RCCR[REF_CNT] value must be greater than or equal to the calculated minimum value and should be decided
after considering application requirements of frequency metering completion response time and overall accuracy required
from CMU_FM.
• Higher values of RCCR[REF_CNT] results in longer metering window, leading to better accuracy in metered clock
measurement.
• Lower values of RCCR[REF_CNT] results in shorter metering window, leading to faster FMC response, but higher
inaccuracy in reported result.

#### 54.4.2 Module programming sequence

The recommended programming sequence of CMU_FM is:
1. In any order, complete the following:
• Program RCCR[REF_CNT] to define the frequency metering window duration in reference_clock cycles.
• Program IER[FMCIE] to enable interrupt.
2. Write GCR[FME] = 1 to start frequency metering operation. Writes to RCCR and IER are disabled after GCR[FME] = 1.
Attempting a write to any of them after GCR[FME] = 1 generates a bus transfer error.
3. To reconfigure RCCR and IER or to stop frequency metering operation, wait for SR[RS] = 1, then write GCR[FME] = 0.
Upon completion of metering operations:
• Hardware writes SR[FMC] = 1
• SR[MET_CNT] updates with metered count value
• Hardware writes GCR[FME] = 0

#### 54.5 External signals

This module has no external signals.

---

*Page 1537*

Clock Monitoring Unit – Frequency Meter (CMU_FM)

#### 54.6 CMU_FM register descriptions

#### 54.6.1 CMU_FM memory map

This section describes the address order of all the CMU_FM registers. Each description includes a standard register diagram and
associated field descriptions.
CMU_1 base address: 402B_C020h
CMU_2 base address: 402B_C040h
Offset Register Access Reset value
Width
(In bits)
0h Global Configuration Register (GCR) 32 RW 0000_0000h
4h Reference Count Configuration Register (RCCR) 32 RW 0000_0000h
8h Status Register (SR) 32 RW 0000_0000h
Ch Interrupt Enable Register (IER) 32 RW 0000_0000h

#### 54.6.2 Global Configuration Register (GCR)

Offset
Register Offset
GCR 0h
Function
Controls module level configurations such as enabling frequency metering.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
FME
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1538*

Clock Monitoring Unit – Frequency Meter (CMU_FM)
Fields
Field Function
Reserved
31-1
—
0 Frequency Meter Enable
FME Starts or stops frequency metering. FME is disabled by default. Software can enable FME at any time.
To stop the ongoing operation, write 0 to FME only when SR[RS] = 1. FME automatically clears upon
frequency metering completion or timeout event.
0b - Stops frequency metering
1b - Starts frequency metering

#### 54.6.3 Reference Count Configuration Register (RCCR)

Offset
Register Offset
RCCR 4h
Function
Programs reference count duration of the frequency meter window.
NOTE
Write to RCCR only when GCR[FME] = 0. A bus transfer error results if software writes RCCR when GCR[FME] = 1.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
REF_CNT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-16
Table continues on the next page...

---

*Page 1539*

Clock Monitoring Unit – Frequency Meter (CMU_FM)
Table continued from the previous page...
Field Function
—
15-0 Reference Clock Count
REF_CNT Total number of counts of reference_clock for which frequency metering runs. This field defines the
duration of one frequency metering window. See Programming RCCR[REF_CNT] for RCCR calculation.

#### 54.6.4 Status Register (SR)

Offset
Register Offset
SR 8h
Function
Provides the internal status of the module and metered clock count.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R MET_CNT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R MET_CNT 0 RS Reserved FMTO FMC
W W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-8 Meter Clock Count
MET_CNT This field stores the count value of the metered clock cycles when frequency metering operation is
complete, in other words, SR[FMC] = 1. CMU_FM has a maximum deviation of ± 3 metered_clock cycles
over the ideal metered_clock cycles expected by the user.
Reserved
7-5
—
4 Run Status
Table continues on the next page...

---

*Page 1540*

Clock Monitoring Unit – Frequency Meter (CMU_FM)
Table continued from the previous page...
Field Function
Shows the running status of module internal operation. After enabling the frequency metering operation
RS
( GCR[FME] = 1), there is a fixed delay before this field shows running status. If the system wants
to disable the frequency metering operation, then the software should read this register and write
GCR[FME] = 0 only when this field is 1.
0b - Frequency meter stopped
1b - Frequency meter running
Reserved
3-2
—
1 Frequency Meter Time Out
FMTO Sets if there is a loss of metered clock during the metering operation. After metering window, the module
waits for a timeout duration of RCCR[REF_CNT] cycles before writing one to this field. GCR[FME] = 0
and SR[FMC] = 0 when FMTO = 1. Writing one clears this field.
0 Frequency Meter Operation Complete
FMC On completion of frequency metering operation, FMC = 1, SR[MET_CNT] field updates, and GCR[FME]
clears. Writing one clears this field.

#### 54.6.5 Interrupt Enable Register (IER)

Offset
Register Offset
IER Ch
Function
Enables CMU_FM interrupts.
NOTE
Write IER only when GCR[FME] = 0. A bus transfer error results if software writes IER when GCR[FME] = 1.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
FMCIE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1541*

Clock Monitoring Unit – Frequency Meter (CMU_FM)
Fields
Field Function
Reserved
31-1
—
0 Frequency Meter Complete Interrupt Enable
FMCIE This bit is used to enable/disable Frequency Meter complete interrupt at the module boundary.
0b - Frequency Meter complete interrupt is disabled
1b - Frequency Meter complete interrupt is enabled

---

*Page 1542*

