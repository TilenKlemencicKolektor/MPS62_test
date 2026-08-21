<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 69 -->

# Chapter 69

# Flexible I/O (FlexIO)

#### 69.1 Chip-specific FlexIO information

#### 69.1.1 FlexIO instances and configuration

The device contains one instance of FlexIO.
FlexIO is a highly configurable module providing a wide range of functionality including:
• Emulation of a variety of serial communication protocols like UART, I2C, SPI, SAI. It can work either as master or slave.
• Flexible 16-bit timers with support for a variety of trigger, reset, enable and disable conditions.
Table 391. FlexIO instances
Chip Instance No. of pins No. of timers No. of shift registers No. of bits
in counter
MCXE317/ FlexIO 32 8 8 16
MCXE31B
MCXE315/ FlexIO 16 8 8 16
MCXE316
Table 392. Data rate limitation
FlexIO operation Data rate limitation
Master Tx FlexIO_clk/4
Slave Tx FlexIO_clk/10
Master Rx FlexIO_clk/8
Slave Rx FlexIO_clk/6
NOTE
FlexIO supports 32 bit accesses only.
NOTE
When I2S is emulated on FlexIO, you could observe a skew between bit-clock (BCLK) and frame sync pulse
amounting to 1/2 of BCLK.
Table 393. FlexIO configuration
No. of pins No. of timers No. of shift registers No. of bits in counter
32 8 8 16

#### 69.2 Overview

FLEXIO is a highly configurable module that provides:
• Emulation of various serial or parallel communication protocols.
• Flexible 16-bit timers with support for various trigger, reset, enable, and disable conditions.

---

*Page 2219*

Flexible I/O (FlexIO)
• Programmable logic blocks that allow the implementation of digital logic functions on-chip and configurable interaction of
internal and external modules.
• Programmable state machine for offloading basic system control functions from the CPU.

#### 69.2.1 Block diagram

The following diagram provides a high-level overview of the FLEXIO timer and shifter configuration.
FLEXIO uses shifters, timers, and external triggers to shift data into or out of FLEXIO. As shown in the block diagram, timers
control the timing of this data shift. You can configure the timers to use generic timer functions, external triggers, or various other
conditions to determine the control.
31 SHIFTBUF0 0
SHIFTER0
FXIO_Dn FXIO_Dn 31 SHIFTBUF i 0
in out/outen
Input selection
Output selection
SHIFTER i
Timer selection
TIMER0 TIMER i
External triggers
Figure 365. Block diagram

#### 69.2.2 Features

• Array of 32-bit shift registers with transmit, receive, data match , logic, and state modes:
— Double-buffered shifter operation for continuous data transfer
— Shifter concatenation to support large transfer sizes
— Automatic start and stop bit generation
— 1, 2, 4, 8, 16, or 32 multi-bit shift widths for parallel interface support
— Interrupt, DMA, or polled transmit and receive operation
• Highly flexible 16-bit timers with support for various internal or external triggers, reset, enable, and disable conditions:
— Programmable baud rates independent of bus clock frequency

---

*Page 2220*

Flexible I/O (FlexIO)
— Programmable logic mode for integrating external digital logic functions on-chip, or combining pin, shifter, or timer
functions to generate complex outputs
— Programmable state machine for offloading basic system control functions from CPU, with support for up to eight
states, eight outputs, and three selectable inputs per state
• Integrated general-purpose I/O registers and pin rising or falling edge interrupts to simplify software support
• Support for a wide range of protocols, including but not limited to:
— UART
— I2C
— SPI
— I2S
— Camera IF
— Motorola 68K or Intel 8080 bus
— PWM or waveform generation
— Input-capture (pulse-edge interval measurement), such as SENT

#### 69.3 Functional description

#### 69.3.1 Shifter operation

Shifters are responsible for buffering and shifting data into or out of FLEXIO. The timer assigned to the shifter controls the timing of
shift, load, and store events via SHIFTCTL n [TIMSEL] . Shifters are designed to support either DMA, interrupt, or polled operations.
The following figure provides a detailed view of the shifter microarchitecture.
31 SHIFTBUF i 0
SSTOP
timer_store_data
SSTART
timer_load_data/start/stop SHIFTER
i
out
FXIO_D0
PINPOL
FXIO_D0 S
PINPOL
SHIFTER i
FXIO_Dn
TIMPOL
SHIFTER i +1 INSRC
out
FXIO_Dn S
timer_shift_pos timer_shift_neg
PINSEL,
PINWIDTH,
PINSEL, PINCFG
PINWIDTH S = Synchronizer
Figure 366. Shifter microarchitecture
69.3.1.1 Transmit mode
In Transmit mode ( SHIFTCTL n [SMOD] = 010b), the shifter loads data from Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) and shifts
data out when the assigned timer signals a load event. An optional start and stop bit can be automatically loaded before or
after SHIFTBUF register data by configuring either SHIFTCFG[SSTART] and TIMCFG[TSTART] , or SHIFTCFG[SSTOP] and
TIMCFG[TSTOP] in the shifter and timer.

---

*Page 2221*

Flexible I/O (FlexIO)
NOTE
If a stop bit is enabled, the shifter immediately loads a stop bit when it is initially configured for Transmit mode.
The shifter status flag ( SHIFTSTAT[SSF] ) and any enabled interrupts or DMA requests are set when data has either been loaded
from the SHIFTBUF register into the shifter or when the shifter is initially configured for Transmit mode. To clear the flag, write 1
or write new data to SHIFTBUF. In Transmit mode, write any value to the SHIFTBUF register to clear the corresponding shifter
status flag, which is cleared regardless of what is writing to the register (DMA or interrupt), or the state of the DMA or interrupt
enables. See the functional description of SHIFTSTAT[SSF] for information on how the flag is set and cleared for each mode.
The shifter error flag ( SHIFTERR[SEF] ) and any enabled interrupts are set when an attempt to load data from an empty SHIFTBUF
register occurs (buffer underrun). Clear the flag by writing 1.
69.3.1.2 Receive mode
When the assigned timer signals a store event in Receive mode ( SHIFTCTL n [SMOD] = 001b), the shifter shifts and stores data
in Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) . You can check for a start and stop bit before or after the shifter data is sampled
by configuring either SHIFTCFG[SSTART] and TIMCFG[TSTART] , or SHIFTCFG[SSTOP] and TIMCFG[TSTOP] in the shifter
and timer.
The shifter status flag ( SHIFTSTAT[SSF] ) and any enabled interrupts or DMA requests are set when data is stored in the
SHIFTBUF register from the shifter. To clear the flag, write 1 to or read the data from SHIFTBUF. Any read of the SHIFTBUF
register clears the corresponding shifter status flag when the shifter is in Receive mode. The flag is cleared regardless of
what is reading the register (DMA or interrupt) or the state of the DMA or interrupt enables. See the functional description of
SHIFTSTAT[SSF] for information on how the flag is set or cleared for each mode.
The shifter error flag ( SHIFTERR[SEF] ) and any enabled interrupts are set either when an attempt to store data into a full
SHIFTBUF register occurs (buffer overrun) or when a mismatch occurs on a start or stop bit check. Write 1 to clear the flag.
69.3.1.3 Match Store mode
In Match Store mode ( SHIFTCTL n [SMOD] = 100b), the shifter shifts data in, checks for a match result, and stores matched
data in Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) when the assigned timer signals a store event. By configuring either
SHIFTCFG[SSTART] , TIMCFG[TSTART] , and SHIFTCFG[SSTOP] , or TIMCFG[TSTOP] in the shifter and timer, you can check
for a start and stop bit before or after the shifter data is sampled. You can compare up to 16 bits of data using SHIFTBUF[31:16]
to configure the data to be matched and SHIFTBUF[15:0] to mask the match result.
The shifter status flag ( SHIFTSTAT[SSF] ) and any enabled interrupts or DMA requests are set when a match occurs and the
matched data is stored in the SHIFTBUF register from the shifter. To clear the flag, read the matched data from the SHIFTBUF
register or write 1 to the flag. Any read of the SHIFTBUF register clears the corresponding shifter status flag when the shifter
is configured in Match Store mode. The flag is cleared regardless of what is reading the register (DMA or interrupt) or the state
of the DMA or interrupt enables. See the functional description for SHIFTSTAT[SSF] to know how the flag is set or cleared for
each mode.
The shifter error flag ( SHIFTERR[SEF] ) and any enabled interrupts are set when an attempt to store matched data into a full
SHIFTBUF register occurs (buffer overrun), or when a mismatch occurs on a start or stop bit check. Write 1 to clear the flag.
69.3.1.4 Match Continuous mode
In Match Continuous mode ( SHIFTCTL n [SMOD] = 101b), the shifter shifts data in and continuously checks for a match result
whenever a shift event is signaled by the assigned timer. You can compare up to 16 bits of data using SHIFTBUF[31:16] to
configure the data to be matched and SHIFTBUF[15:0] to mask the match result.
The shifter status flag ( SHIFTSTAT[SSF] ) and any enabled interrupts or DMA requests are set when a match occurs. The flag
clears automatically as soon as no match exists between the shifter data and Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) .
You cannot clear the flag by reading the SHIFTBUF register.
The shifter error flag ( SHIFTERR[SEF] ) and any enabled interrupts are set when a match occurs. To clear the flag, write 1 or
perform a read from the SHIFTBUF register.

---

*Page 2222*

Flexible I/O (FlexIO)
69.3.1.5 State mode
State mode enables you to implement any state machine with up to eight states, eight outputs, and three selectable inputs per
state. This feature allows basic control functions to be offloaded from the CPU.
In State mode ( SHIFTCTL n [SMOD] = 110b), when the shifter is selected by the current state pointer ( SHIFTSTATE[STATE] ), use
the SHIFTBUF register to drive the output and compute the next state values. The following figure provides a detailed view of the
shifter microarchitecture when configured for State mode.
31 SHIFTBUF i 0
PINCFG,
{PINWIDTH, SSTART, SSTOP}
FXIO_D7..0
PINPOL
SHIFTSTATE
FXIO_D0 S
STATE== i
SHIFTER i +1 INSRC nstate[2:0]
out
FXIO_Dn S
nstate_trigger
PINSEL nstate attributes
TIMSEL, from other shifters
TIMPOL
timer_shift_pos timer_shift_neg
S = Synchronizer
Figure 367. State microarchitecture
When the current state pointer selects a specific shifter (shifter n ), output pins FXIO_D[7:0] are driven by SHIFTBUF n [31:24];
the configuration is defined by SHIFTCTL n [PINCFG] . Write 1 to Shifter Configuration (SHIFTCFG0 - SHIFTCFG7)
{PWIDTH[3:0],SSTOP[1:0],SSTART[1:0]} to disable the output drive on pins FXIO_D[7:0] for state machine applications that
require less than eight output pins.
Use the three input pins selected by SHIFTCTL n [PINSEL] and SHIFTBUF n [23:0] to compute the next state value.
NOTE
Each state can use a different set of three input pins.
The following table shows how the next state value is computed when the current state pointer is pointing to shifter n .
Table 394. Next state computation for SHIFTSTATE[STATE] = n
FXIO_D[PINSEL + 2] FXIO_D[PINSEL + 1] FXIO_D[PINSEL] Next state value
0 0 0 SHIFTBUF n [2:0]
0 0 1 SHIFTBUF n [5:3]
0 1 0 SHIFTBUF n [8:6]
0 1 1 SHIFTBUF n [11:9]
... ... ... ...
1 1 1 SHIFTBUF n [23:21]

---

*Page 2223*

Flexible I/O (FlexIO)
NOTE
You can configure other shifters and timers to drive the input pins of a given state, allowing you to create complex
combinations of shifters and timers as needed. For example, the output of a shifter configured for Logic mode can
be used to drive a state machine input.
The next state transition is triggered using the timer output selected by SHIFTCTL n [TIMSEL] , with polarity controlled
by SHIFTCTL n [TIMPOL] .
NOTE
Each state can use a different timer to trigger each next state transition, allowing various internal or external trigger
sources and clocking configurations to be used. See Timer section for more information.
The current state pointer defaults to shifter 0 at reset; however, you can write to select a different shifter for the initial state. If the
current state pointer selects a shifter that is not configured for State mode, then outputs are not driven and the next state transition
is never triggered.
The shifter status flag ( SHIFTSTAT[SSF] ) and any enabled interrupts or DMA requests are set when the shifter is selected by the
current state pointer. The flag is cleared when the current state pointer is updated to a different shifter.
69.3.1.6 Logic mode
Logic mode enables you to implement a small amount of programmable digital logic within a FLEXIO shifter.
In Logic mode ( SHIFTCTL n [SMOD] = 111b), use Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) to implement a 5-input, 32-bit
programmable logic lookup table. The following figure provides a detailed view of shifter microarchitecture when configured for
Logic mode.
Use the SHIFTBUF register to configure the lookup table for the four pin inputs. You can also use SHIFTER n to configure a
feedback or delayed pin source as the fifth input to the lookup table.
31 SHIFTBUF i 0
SSTART,
SSTOP
FXIO_Dx
FXIO_Dx+1
FXIO_Dx+2 PINCFG MASK
FXIO_Dx+3
Logic output FXIO_Dx+4
PINPOL
FXIO_D0 S
SHIFTER i
TIMPOL
SHIFTER i +1 INSRC
out
FXIO_Dn S
timer_shift_pos timer_shift_neg
PINSEL,
PINWIDTH S = Synchronizer
Figure 368. Logic microarchitecture
The lookup table is driven using four pin inputs (maskable using SHIFTCFG[SSTOP] and SHIFTCFG[SSTART] ), plus one input
from the internal shifter. It can be configured to drive an output pin using SHIFTCTL[PINCFG] . Pin inputs and outputs are fixed
for each logic lookup table and are not selectable. The following table lists the logic output value selected by the lookup table for
shifter n .

---

*Page 2224*

Flexible I/O (FlexIO)
Table 395. Logic lookup table for shifter n
1
SHIFTER n [0] FXIO_D[ x + 3] FXIO_D[ x + 2] FXIO_D[ x + 1] FXIO_D[ x ] Logic output to
FXIO_D[ x + 4]
0 0 0 0 0 SHIFTBUF n [0]
0 0 0 0 1 SHIFTBUF n [1]
0 0 0 1 0 SHIFTBUF n [2]
0 0 0 1 1 SHIFTBUF n [3]
... ... ... ... ... ...
1 1 1 1 1 SHIFTBUF n [31]
1. for shifters n = 0...3, x = n
for shifter n = 4...7, x = n + 4
To minimize output glitches, use SHIFTCFG n [SSTOP] and SHIFTCFG n [SSTART] to mask unused input pins. When these fields
are 1, {SSTOP[1:0] and SSTART[1:0]} mask FXIO_D[ x + 3]...FXIO_D[ x ] inputs respectively so that any transitions on these pins
do not cause the logic output to glitch.
NOTE
You can configure other shifters and timers to drive the input pins of a given lookup table, allowing you to
concatenate lookup tables or create complex combinations of shifters and timers as needed.
SHIFTCFG[PWIDTH] controls the number of delay stages introduced by the internal shifter input (SHIFTER n [0]). For example,
when configured for a 1-bit shift (SHIFTCFG[PWIDTH] = 0), the internal shifter introduces a 32-shift clock delay before passing its
input (selected by SHIFTCTL[PINSEL] ) to the lookup table. When configured for a 32-bit shift (SHIFTCFG[PWIDTH] = 16...31),
the internal shifter introduces a 1-shift clock delay to its input.
The shifter status flag ( SHIFTSTAT[SSF] ) and any enabled interrupts or DMA requests are set whenever the output pin allocated
to the logic lookup table has a value of 1 after being synchronized with the FLEXIO clock. The flag clears when the output pin has
a value of 0. This also allows SHIFTSTAT[SSF] to be used as a trigger to a timer if needed.
The shifter error flag ( SHIFTERR[SEF] ) and any enabled interrupts are set when the output pin allocated to the logic lookup table
is asserted. Clear the flag by writing 1 to it.
The Logic mode input pins, including pins driven by other shifters and timers, are synchronized with the FLEXIO functional clock
before they are input to the programmable logic lookup table.

#### 69.3.2 Timer operation

The FLEXIO 16-bit timers control the loading, shifting, and storing of the shift registers. The counters load the contents of the
compare register and decrement down to zero on the FLEXIO clock. The counters can perform generic timer functions such as
generating a clock, select output, or a PWM waveform. You can configure these timers to perform any of the following functions:
• Enable in response to a trigger, pin, or shifter condition.
• Decrement always or only on a trigger or pin edge.
• Reset in response to a trigger or pin condition.
• Disable on a trigger or pin condition or on a timer compare.
Timers can optionally include a start condition and a stop condition.
Although each timer operates independently, you can configure a timer to enable or disable at the same time as the previous timer
(for example, timer 1 can enable or disable at the same time as timer 0) and a timer output can be used to trigger any other timer.
The trigger used by each timer is configured independently as a timer output, shifter status flag, pin input, or an external trigger

---

*Page 2225*

Flexible I/O (FlexIO)
input. The trigger configuration is separate from pin configuration; you can perform it to configure input, output data, or output
enable. See the chip-specific FLEXIO information for information on external trigger connections.
You must configure Timer Configuration (TIMCFG0 - TIMCFG7) before writing 1 to TIMCTL n [TIMOD] .
69.3.2.1 Timer 8-bit Baud Counter mode
In 8-bit Baud Counter mode, the 16-bit counter is divided into two 8-bit counters. The lower 8 bits are used to configure the baud
rate of the shift clock and the upper 8 bits are used to configure the number of shift clock edges in the transfer. When the lower 8 bits
decrement to zero, the timer output is toggled and the lower 8 bits reload from the compare register. The upper 8 bits decrement
when the lower 8 bits become zero and decrement.
NOTE
A timer reset event in 8-bit Baud Counter mode only resets the lower 8-bit counter. The upper 8-bit counter is not
affected and can decrement if the timer reset is configured to update the state of the timer output, which toggles
as a result of the timer reset event.
A timer compare event occurs when the upper 8 bits equal zero and decrement. The timer status flag is set on a timer
compare event.
69.3.2.2 Timer 8-bit High PWM mode
In 8-bit High PWM mode, the 16-bit counter is divided into two 8-bit counters. The lower 8 bits are used to configure the timer output
high period and the upper 8 bits are used to configure the timer output low period. The lower 8 bits decrement when the output is
high. When the lower 8 bits become zero and decrement, the timer output is cleared and the lower 8 bits are reloaded from the
compare register. The upper 8 bits decrement when the output is low. When the upper 8 bits become zero and decrement, the
timer output is set and the upper 8 bits are reloaded from the compare register.
A timer compare event occurs when the upper 8 bits become zero and decrement. The timer status flag is set on a timer
compare event.
69.3.2.3 Timer 16-bit Counter mode
In 16-bit Counter mode, you can use the 16-bit counter to configure either the baud rate of the shift clock (for example,
TIMDEC[1:0] ≠ 10 or 11) or the number of shift clock edges in the transfer (for example, TIMDEC[1:0] = 10 or 11). When the 16-bit
counter equals zero and decrements, the timer output toggles and the counter reloads from the compare register.
A timer compare event occurs when the 16-bit counter equals zero and decrements. The timer status flag is set on a timer
compare event.
69.3.2.4 Timer 16-bit Counter Disable mode
In 16-bit Counter Disable mode, the 16-bit counter can be used to configure either the baud rate of the shift clock (for example,
TIMDEC[1:0] ≠ 10 or 11) or the number of shift clock edges in the transfer (for example, TIMDEC[1:0] = 10 or 11). When the 16-bit
counter equals zero and decrements, the timer output toggles and the counter reloads from the compare register.
A timer compare event occurs when the 16-bit counter equals zero and decrements. The timer status flag is set on a timer
disable event.
69.3.2.5 Timer 8-bit Word Counter mode
In 8-bit Word Counter mode, the 16-bit counter is divided into two 8-bit counters. The lower 8 bits are used to configure the number
of shift clock edges in each word and the upper 8 bits are used to configure the number of words in the transfer. When the lower
8 bits decrement to zero, the timer output is toggled and the lower 8 bits reload from the compare register. The upper 8 bits only
decrement when the lower 8 bits become zero and decrement.
A timer compare event occurs when the lower 8 bits become zero and decrement. The timer status flag is set when the upper 8
bits become zero and decrement.

---

*Page 2226*

Flexible I/O (FlexIO)
69.3.2.6 Timer 8-bit Low PWM mode
In 8-bit Low PWM mode, the 16-bit counter is divided into two 8-bit counters. The lower 8 bits are used to configure the timer output
low period and the upper 8 bits are used to configure the timer output high period. The lower 8 bits decrement when the output is
low. When the lower 8 bits become zero and decrement, the timer output is set and the lower 8 bits are reloaded from the compare
register. The upper 8 bits decrement when the output is high. When the upper 8 bits become zero and decrement, the timer output
is cleared and the upper 8 bits are reloaded from the compare register.
A timer compare event occurs when the upper 8 bits become zero and decrement. The timer status flag is set on a timer
compare event.
69.3.2.7 Timer enable and start functions
The following events occur when you configure TIMCTL n [TIMOD] for the desired mode and the condition configured by the
timer enable ( TIMCFG n [TIMENA] ) is detected. When TIMCTL n [ONETIM] is 1, the timer status flag must be clear to generate a
timer enable event; otherwise, the timer enable event is blocked. You can use this to enforce software intervention after each
timer iteration:
• The timer counter loads the current value of the compare register and starts decrementing, as configured by
TIMCFG n [TIMDEC] .
• The timer output may update to its initial state depending on the configuration of TIMCFG n [TIMOUT] . Shifters that are
controlled by this timer do not see this as a rising edge on the timer shift clock.
• Transmit shifters controlled by this timer either output their start bit value or load the shift register from the shift buffer and
output the first bit, as configured by SHIFTCFG n [SSTART] .
If the timer start bit is enabled, the timer counter reloads with the compare register on the first rising edge of the shift clock
after the timer starts decrementing. If there is no falling edge on the shift clock before the first rising edge (for example, when
TIMCFG n [TIMOUT] = 1), a shifter that is configured to shift on the falling edge and load on the first shift does not load correctly.
69.3.2.8 Timer decrement and reset functions
The timer generates the timer output and timer shift clock depending on the fields, TIMCTL n [TIMOD] and TIMCFG n [TIMDEC] .
The shifter clock is either equal to the timer output (when TIMCFG n [TIMDEC] ≠ 10 or 11) or equal to the decrement clock (when
TIMCFG n [TIMDEC] = 10 or 11). If you configure TIMCFG n [TIMDEC] to decrement from a pin or trigger, the timer decrements on
both rising and falling edges.
If a timer is configured to decrement on the FLEXIO functional clock divided by 16 or 256 (when TIMCFG n [TIMDEC] = 100 or 101),
then a common prescaler that is shared by all timers is used to generate the two divide ratios. This prescaler is reset when all timers
are either idle or configured not to use the prescaler (TIMCFG n [TIMDEC] ≠ 100 or 101).
If you configure the timer to reset as determined by TIMCFG n [TIMRST] , then the timer counter loads the current value of the
compare register again. You can configure the timer output and timer shift clock to update on timer reset, as configured by
TIMCFG n [TIMOUT] . If the time output toggles as a result of the timer reset, this can result in a timer shift clock edge. In 8-bit Baud
Counter mode, this also decrements the upper 8 bits of the counter.
In general, when the timer counter decrements to zero, a timer compare event is triggered. The timer compare event causes:
• The timer counter to load the contents of the timer compare register.
• The timer output to toggle.
• Any configured transmit shift registers to load.
• Any configured receive shift registers to store.
Depending on the timer mode, the timer status flag may also be set.
69.3.2.9 Timer disable and stop functions
When the timer is configured to add a stop bit on each compare, the following additional events occur:
• Transmit shifters controlled by this timer output their stop bit value (if configured by SHIFTCFG n [SSTOP] ).

---

*Page 2227*

Flexible I/O (FlexIO)
• Receive shifters controlled by this timer store the contents of the shift register in their shift buffer, as configured by
SHIFTCFG n [SSTOP].
• The timer counter reloads the current value of the compare register on the first rising edge of the shifter clock after the
compare.
If you configure the timer to insert a stop bit on each compare, you must configure the transmit shifters to load on the first shift.
When the condition configured by timer disable ( TIMCFG n [TIMDIS] ) is detected, the following events occur:
• Timer counter reloads the current value of the compare register and starts decrementing as configured by
TIMCFG n [TIMDEC] .
• Timer output clears. Shifters that are controlled by this timer do not see this as a falling edge on the timer shift clock, but
can generate a shift event if the timer shift clock otherwise generates one.
• Transmit shifters controlled by this timer output their stop bit value (if configured by SHIFTCFG n [SSTOP]).
• Receive shifters controlled by this timer store the contents of the shift register in their shift buffer, as configured by
SHIFTCFG n [SSTOP].
If the timer stop bit is enabled, the timer counter continues decrementing until the next rising edge of the shift clock is detected,
at which point it finishes decrementing. Although the timer output is forced low during the stop bit, the timer shift clock can toggle
during the stop bit. The timer output does not generate shift events during the stop bit.
A timer enable condition can be detected in the same cycle as a timer disable condition (if timer stop bit is disabled), or on the
first rising edge of the shift clock after the disable condition (if stop bit is enabled). When TIMCTL n [ONETIM] is 1, the timer status
flag must be clear before the next timer enable condition is detected. When the timer is in the stop state condition, receive shift
registers with stop bit enabled store the contents of the shift register into the shift buffer and verify the state of the input data on
the configured shift edge. If there is no configured edge between the timer disable and the next rising edge of the shift clock, then
the final store and verify do not occur.

#### 69.3.3 Pin operation

The pin configuration for each timer and shifter can be set to use any FLEXIO pin with either polarity. You can configure each
timer and shifter as an input, output data, output enable, or bidirectional output. A pin configured for output enable can be used as
an open drain (with inverted polarity because the output enable assertion causes logic zero to be output on the pin) or to control
the enable on the bidirectional output. You can configure any timer or shifter to control the output enable for a pin where the
bidirectional output data is driven by another timer or shifter.
When more than one shifter or timer is configured to use the same pin for output data, then the output data to the pin is combined
using an OR function. This also applies if more than one timer or shifter is configured to use the same pin for output enable.
69.3.3.1 Parallel interface
You can configure shifters to use multiple FLEXIO pins simultaneously by using SHIFTCFG n [PWIDTH] , which is used to configure
the following settings of a shifter:
1. Number of bits shifted per shift clock.
2. Number of pins driven by the shifter per shift clock (only on shifters supporting parallel transmit—that is, SHIFTER0
and SHIFTER4 .)
3. Number of pins sampled by the shifter per shift clock (only on shifters supporting parallel receive—that is, SHIFTER3
and SHIFTER7 .)
When configured for parallel shift, either 4, 8, 16, or 32 bits can be shifted on every shift clock. If an adjacent shifter is selected
as the input source ( SHIFTCFG n [INSRC] = 1), the least significant 4, 8, 16, or 32 bits from the adjacent shifter are sampled on
each shift clock.
For shifters supporting parallel receive (SHIFTER3 , SHIFTER7 ), you can configure the shifter to sample multiple pins (INSRC =
0), with PWIDTH and PINSEL selecting the pins as FXIO_D[PINSEL+PWIDTH]:FXIO_D[PINSEL].

---

*Page 2228*

Flexible I/O (FlexIO)
NOTE
If PWIDTH is less than the number of bits being shifted on each shift clock, then the most significant bits are masked
with 0. For example, if PINSEL = 7 and PWIDTH = 6, then SHIFTER[31:24] samples {0,0,FXIO_D[12:7]} on each
shift clock.
For shifters supporting parallel transmit (SHIFTER0 , SHIFTER4 ), you can configure the shifter to drive multiple pins using
SHIFTCTL n [PINCFG] , with PWIDTH and PINSEL selecting the pins as follows: FXIO_D[PINSEL+PWIDTH]:FXIO_D[PINSEL].
NOTE
If PWIDTH is less than the number of bits being shifted on each shift clock, then the most significant pins are
not driven. For example, if PINSEL = 7 and PWIDTH = 6, then SHIFTER[5:0] drives only FXIO_D[12:7] on each
shift clock.
69.3.3.2 Pin synchronization
When you configure a pin as an input (this includes a timer trigger configured as a pin input), the input signal is first synchronized
with the FLEXIO clock before a timer or shifter could use the signal. This introduces a small latency of 0.5–1.5 FLEXIO clock
cycles when using an external pin input to generate an output or control a shifter. This sets the maximum setup time at 1.5 FLEXIO
clock cycles.
If an input is used by more than one timer or shifter, then the synchronization occurs once to ensure any edge is seen on the same
cycle by all timers and shifters using that input.
NOTE
FLEXIO pins are also connected internally. Configuring a FLEXIO shifter or timer to output data on an unused pin
makes an internal connection that allows other shifters and timers to use this pin as an input. This allows a shifter
output to trigger a timer or a timer output to be shifted into a shifter. This path is also synchronized with the FLEXIO
clock and therefore incurs a one-cycle latency.
When using a pin input as a timer trigger, timer clock, or shifter data input, the following synchronization delays occur:
• 0.5–1.5 FLEXIO clock cycles for an external pin
• One FLEXIO clock cycle for an internally driven pin
See Application information for timing considerations such as output valid time and input setup time for specific applications (SPI
controller, SPI target, I2C controller, I2S controller, and I2S target).
69.3.3.3 Pin override
You can change the state of any FLEXIO pin at any time. Pin Output Enable (PINOUTE) configures any pin as an output and drives
that pin with the value in Pin Output Data (PINOUTD) .
Alias registers for PINOUTE and data registers also exist. Writing a logic 1 to an alias register updates the corresponding register
fields in both PINOUTE and PINOUTD as follows:
• Pin Output Disable (PINOUTDIS) clears Pin Output Enable (PINOUTE) and Pin Output Data (PINOUTD) .
• Pin Output Clear (PINOUTCLR) sets Pin Output Enable (PINOUTE) and clears Pin Output Data (PINOUTD) .
• Pin Output Set (PINOUTSET) sets Pin Output Enable (PINOUTE) and Pin Output Data (PINOUTD) .
• Pin Output Toggle (PINOUTTOG) sets Pin Output Enable (PINOUTE) and toggles Pin Output Data (PINOUTD) .
69.3.3.4 Pin interrupt
You can read the state of any FLEXIO pin at any time and also configure any pin to set a status flag when either a rising or falling
edge is detected on that pin. Additionally, you can configure the pin status flag to generate an interrupt.

---

*Page 2229*

Flexible I/O (FlexIO)

#### 69.3.4 Low-power modes

FLEXIO remains functional during low-power modes, if the FLEXIO functional clock remains enabled.

#### 69.3.5 Debug mode

FLEXIO remains functional in Debug mode, provided the value of CTRL[DBGE] is 1.

#### 69.3.6 Clocking

Table 396. FLEXIO clocks
Clock Description
Functional clock
Is asynchronous to the bus clock and can remain enabled in low-power modes. You must enable
the FLEXIO functional clock before accessing any of the FLEXIO registers. Provided the FLEXIO
functional clock is at least equal to the bus clock, you can configure CTRL[FASTACC] to support fast
register accesses.
Bus clock Is used only for bus accesses to the control and configuration registers.

#### 69.3.7 Reset

Table 397. FLEXIO reset types
Reset Description
Chip reset
Resets the FLEXIO logic and registers to their default states on chip reset.
Software reset
Resets, using CTRL[SWRST] , all logic and registers to their default states, except for the Control register.

#### 69.3.8 Interrupts and DMA requests

The following table shows the status flags that generate the FLEXIO interrupt and DMA requests.
Table 398. FLEXIO interrupts and DMA requests
Flag Description Interrupt DMA request Low-power wake-up
SHIFTSTAT[SSF] Shifter status flag Y Y Y
SHIFTERR[SEF] Shifter error flag Y N Y
TIMSTAT[TSF] Timer status flag Y Y Y
PINSTAT[PSF] Pin status flag Y N Y
TRGSTAT[ETSF] External trigger status Y N Y
flag

#### 69.3.9 Peripheral triggers

The connection between FLEXIO peripheral triggers and other peripherals is device-specific.
69.3.9.1 Output triggers
Each FLEXIO timer generates an output trigger equal to the timer output. The output trigger is not affected by the timer pin
polarity configuration.

---

*Page 2230*

Flexible I/O (FlexIO)
69.3.9.2 Input trigger
FLEXIO supports multiple external trigger inputs that can be used to trigger one or more FLEXIO timers. If a rising edge is detected
on an external trigger when FLEXIO is enabled, then the external trigger status flag is set. The external triggers are synchronized
to the FLEXIO functional clock and must assert for at least two cycles of the FLEXIO functional clock to be sampled correctly.

#### 69.4 External signals

Table 399. External signals
Signal Description Direction
FXIO_D n ( n = 0...31) Bidirectional FLEXIO shifter and timer Input or output
pin

#### 69.5 Initialization

Perform the following procedure to initialize FLEXIO registers:
1. Enable FLEXIO by writing 1 to CTRL[FLEXEN] .
2. Configure shift registers for the given application. It is recommended to write to Shifter Configuration (SHIFTCFG0 -
SHIFTCFG7) before writing to the corresponding register, Shifter Control (SHIFTCTL0 - SHIFTCTL7) .
3. Configure timer registers for the given application. It is recommended to write to Timer Compare (TIMCMP0 -
TIMCMP7) and Timer Configuration (TIMCFG0 - TIMCFG7) before writing to the corresponding register, Timer Control
(TIMCTL0 - TIMCTL7) .
4. Enable interrupts and/or DMA requests, as appropriate, for the given application.
5. Write transmit data to initiate a transfer (depending on the given application).

#### 69.6 Application information

This section provides examples for a variety of FLEXIO module applications. See FLEXIO register descriptions for
more information.

#### 69.6.1 UART transmit

UART transmit can be supported using one timer, one shifter, and one pin (two pins, if supporting CTS). The start and stop bit
insertion is handled automatically, and multiple transfers are supported using the DMA controller. The timer status flag is used to
indicate when the stop bit of each word is transmitted.
Break and idle characters require software intervention. Before transmitting a break or idle character, you must modify
SHIFTCFG n [SSTART] and SHIFTCFG n [SSTOP] to transmit the required state, and the data to transmit must equal FFh or 00h.
Supporting a second stop bit requires the stop bit to be inserted into the data stream using software (and increasing the number
of bits to transmit). When performing byte writes to Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) (or Shifter Buffer Bit Swapped
(SHIFTBUFBIS0 - SHIFTBUFBIS7) for transmitting MSB first), the rest of the register remains unaltered. This allows an address
mark bit or additional stop bit to remain undisturbed.
NOTE
FLEXIO does not support automatic insertion of parity bits.
Table 400. UART transmit configuration
Register Value Configuration
SHIFTCFG n 0000_0032h Configure start bit of 0 and stop bit of 1.
Table continues on the next page...

---

*Page 2231*

Flexible I/O (FlexIO)
Table 400. UART transmit configuration (continued)
Register Value Configuration
SHIFTCTL n 0003_0002h Configure transmit using timer 0 on the
positive edge of clock with output data
on pin 0. You can configure the PINPOL
field to invert output data, or support
open-drain by writing 1h to the PINPOL
and PINCFG fields.
TIMCMP n 0000_0F01h Configure 8-bit transfer with baud rate
of divide by 4 of the FLEXIO clock. Set
TIMCMP[15:8] as (number of bits × 2)
- 1, and set TIMCMP[7:0] as (baud rate
divider ÷ 2) - 1.
TIMCFG n 0000_2222h Configure start bit, stop bit, enable
on trigger asserted and disable on
compare. You can support CTS by
configuring the TIMENA field as 3h.
TIMCTL n 01C0_0001h Configure the dual 8-bit counter using
the shifter 0 status flag as an inverted
internal trigger source. To support CTS,
configure the PINSEL (for pin 1) and
PINPOL fields as 1h.
SHIFTBUF n Data to transmit Transmit data can be written to
SHIFTBUF[7:0] to initiate an 8-bit
transfer. Use the shifter status flag to
indicate when data can be written using
an interrupt or a DMA request. Write to
SHIFTBUFBBS[7:0] instead to support
MSB first transfer.
The following table shows an alternative configuration that supports slower baud rates. This configuration requires two timers.
Table 401. UART transmit configuration for slow baud rate
Register Value Configuration
SHIFTCFG n 0000_0032h Configure start bit of 0 and stop bit of 1.
SHIFTCTL n 0003_0002h Configure transmit using timer 0 on the
positive edge of clock with output data
on pin 0. Invert output data by writing 1
to the PINPOL field. Support open-drain
by configuring the PINPOL and PINCFG
fields as 1h.
TIMCMP n 0000_000Fh Configure for 8-bit transfer, and
configure TIMCMP[15:0] as (number of
bits × 2) - 1.
TIMCFG n 0030_2622h Configure start bit, stop bit, enable on
trigger rising edge, decrement on trigger
and disable on compare.
Table continues on the next page...

---

*Page 2232*

Flexible I/O (FlexIO)
Table 401. UART transmit configuration for slow baud rate (continued)
Register Value Configuration
TIMCTL n 0740_0003h Configure the 16-bit counter using the
timer 1 output as an internal trigger
source.
TIMCMP( n + 1) 0000_0001h Configure baud rate of divide by 4
of the FLEXIO clock, and configure
TIMCMP[15:0] as (baud rate divider ÷ 2)
- 1.
TIMCFG( n + 1) 0000_1200h Configure enable on trigger asserted
and disable on timer 0 disable. You
can configure the TIMEN field as 3h to
support CTS.
TIMCTL( n + 1) 01C0_0003h Configure the 16-bit counter using the
shifter 0 status flag as an inverted
internal trigger source. You can support
CTS by configuring the PINSEL (for pin
1) and PINPOL fields as 1h.
SHIFTBUF n Data to transmit Transmit data can be written to
SHIFTBUF[7:0] to initiate an 8-bit
transfer. Use the shifter status flag to
indicate when data can be written using
an interrupt or a DMA request. Write to
SHIFTBUFBBS[7:0] instead to support
MSB first transfer.

#### 69.6.2 UART receive

UART receive can be supported using one timer, one shifter, and one pin (two timers and two pins, if supporting RTS). The start
and stop bit verification is handled automatically and multiple transfers are supported using the DMA controller. The timer status
flag is used to indicate when the stop bit of each word is received.
FLEXIO does not support triple voting of the received data, which is sampled only once in the middle of each bit. You can use a
timer to implement a glitch filter on the incoming data and a different timer to detect an idle line of programmable length. Break
characters cause the error flag to set, and the shifter buffer register returns 00h.
NOTE
FLEXIO does not support automatic verification of parity bits.
Table 402. UART receiver configuration
Register Value Configuration
SHIFTCFG n 0000_0032h Configure start bit of 0 and stop bit of 1.
SHIFTCTL n 0080_0001h Configure receive using timer 0 on the
negative edge of clock with input data
on pin 0. You can invert input data by
writing 1 to the PINPOL field.
TIMCMP n 0000_0F01h Configure 8-bit transfer with baud rate
of divide by 4 of the FLEXIO clock. Set
Table continues on the next page...

---

*Page 2233*

Flexible I/O (FlexIO)
Table 402. UART receiver configuration (continued)
Register Value Configuration
TIMCMP[15:8] as (number of bits × 2)
- 1, and set TIMCMP[7:0] as (baud rate
divider ÷ 2) - 1.
TIMCFG n 0204_2422h Configure start bit, stop bit, enable
on pin positive edge and disable on
compare. Enable resynchronization to
received data with TIMOUT = 2h and
TIMRST = 4h.
TIMCTL n 0000_0081h Configure the dual 8-bit counter using
the inverted pin 0 input.
SHIFTBUF n Data to receive You can read received data from
SHIFTBUFBYS[7:0]. Use the shifter
status flag to indicate when data can
be read using interrupt or DMA request.
Read from SHIFTBUFBIS[7:0] instead
to support MSB first transfer.
The UART receiver with RTS configuration uses a second timer to generate the RTS output. RTS asserts when the start bit is
detected and negates when the data is read from the shifter buffer register. If no start bit is detected when the RTS is asserted,
the received data is ignored.
Table 403. UART receiver with RTS configuration
Register Value Configuration
SHIFTCFG n 0000_0032h Configure start bit of 0 and stop bit of 1.
SHIFTCTL n 0080_0001h Configure receive using timer 0 on the
negative edge of clock with input data
on pin 0. Invert input data by writing 1 to
the PINPOL field.
TIMCMP n 0000_0F01h Configure 8-bit transfer with baud rate
of divide by 4 of the FLEXIO clock. Set
TIMCMP[15:8] as (number of bits × 2)
- 1, and set TIMCMP[7:0] as (baud rate
divider ÷ 2) - 1.
TIMCFG n 0204_2522h Configure start bit, stop bit, enable
on pin positive edge with trigger
asserted and disable on compare.
Enable resynchronization to received
data with TIMOUT = 2h and TIMRST =
4h.
TIMCTL n 02C0_0081h Configure dual 8-bit counter using the
inverted pin 0 input. Trigger is internal
using the inverted pin 1 input.
TIMCMP( n + 1) 0000_FFFFh Never compare.
Table continues on the next page...

---

*Page 2234*

Flexible I/O (FlexIO)
Table 403. UART receiver with RTS configuration (continued)
Register Value Configuration
TIMCFG( n + 1) 0030_6100h Enable on timer n enable and disable on
the trigger falling edge. Decrement on
trigger to ensure no compare.
TIMCTL( n + 1) 0143_0003h Configure 16-bit counter and output
on pin 1. Trigger is internal using the
shifter 0 flag.
SHIFTBUF n Data to receive You can read received data using
SHIFTBUFBYS[7:0]. Use the shifter
status flag to indicate when data can
be read using interrupt or DMA request.
Read from SHIFTBUFBIS[7:0] instead
to support MSB first transfer.

#### 69.6.3 SPI controller

SPI Controller mode can be supported using two timers, two shifters, and four pins. Using the DMA controller, either CPHA = 0
or CPHA = 1 and transfers can be supported. For CPHA = 1, the chip select can remain asserted for multiple transfers and the
timer status flag can be used to indicate the end of the transfer.
The stop bit is used to guarantee a minimum of one clock cycle between the target chip select negating and before the next
transfer. To initiate each transfer, either the core or DMA writes to the transmit buffer.
NOTE
Because of synchronization delays, the setup time for the serial input data is 1.5 FLEXIO clock cycles. This means
the maximum baud rate is divide by 4 of the FLEXIO clock frequency.
Table 404. SPI controller (CPHA = 0) configuration
Register Value Configuration
SHIFTCFG n 0000_0000h Start and stop bit disabled.
SHIFTCTL n 0083_0002h Configure transmit using timer 0 on the
negative edge of clock with output data
on pin 0.
SHIFTCFG( n + 1) 0000_0000h Start and stop bit disabled.
SHIFTCTL( n + 1) 0000_0101h Configure receive using timer 0 on the
positive edge of clock with input data on
pin 1.
TIMCMP n 0000_3F01h Configure 32-bit transfer with baud rate
of divide by 4 of the FLEXIO clock. Set
TIMCMP[15:8] as (number of bits × 2)
- 1, and set TIMCMP[7:0] as (baud rate
divider ÷ 2) - 1.
TIMCFG n 0100_2222h Configure start bit, stop bit, enable on
trigger high and disable on compare;
initial clock state is logic 0.
Table continues on the next page...

---

*Page 2235*

Flexible I/O (FlexIO)
Table 404. SPI controller (CPHA = 0) configuration (continued)
Register Value Configuration
TIMCTL n 01C3_0201h Configure dual 8-bit counter using the
pin 2 output (shift clock), with shifter 0
flag as the inverted trigger. Write 1 to
the PINPOL field to invert the output
shift clock.
TIMCMP( n + 1) 0000_FFFFh Never compare.
TIMCFG( n + 1) 0000_1100h Enable when timer 0 is enabled and
disable when timer 0 is disabled.
TIMCTL( n + 1) 0003_0383h Configure 16-bit counter (never
compare) using the inverted pin 3 output
as target select.
SHIFTBUF n Data to transmit You can write transmit data to Shifter
Buffer (SHIFTBUF0 - SHIFTBUF7) . Use
the shifter status flag to indicate when
data can be written using interrupt
or DMA request. Write to Shifter
Buffer Bit Swapped (SHIFTBUFBIS0
- SHIFTBUFBIS7) instead to support
MSB first transfer.
SHIFTBUF( n + 1) Data to receive Received data can be read from Shifter
Buffer (SHIFTBUF0 - SHIFTBUF7) .
Use the shifter status flag to indicate
when data can be read using interrupt
or DMA request. Read from Shifter
Buffer Bit Swapped (SHIFTBUFBIS0
- SHIFTBUFBIS7) instead to support
MSB first transfer.
Table 405. SPI controller (CPHA = 1) configuration
Register Value Configuration
SHIFTCFG n 0000_0021h Start bit loads data on first shift.
SHIFTCTL n 0003_0002h Configure transmit using timer 0 on the
positive edge of clock with output data
on pin 0.
SHIFTCFG( n + 1) 0000_0000h Start and stop bit disabled.
SHIFTCTL( n + 1) 0080_0101h Configure receive using timer 0 on the
negative edge of clock with input data
on pin 1.
TIMCMP n 0000_3F01h Configure 32-bit transfer with baud rate
of divide by 4 of the FLEXIO clock. Set
TIMCMP[15:8] as (number of bits x 2) -
1, and set TIMCMP[7:0] as (baud rate
divider ÷ 2) - 1.
Table continues on the next page...

---

*Page 2236*

Flexible I/O (FlexIO)
Table 405. SPI controller (CPHA = 1) configuration (continued)
Register Value Configuration
TIMCFG n 0100_2222h Configure start bit, stop bit, enable on
trigger high and disable on compare;
initial clock state is logic 0.
TIMCTL n 01C3_0201h Configure dual 8-bit counter using pin 2
output (shift clock), with the shifter 0 flag
as the inverted trigger. Write 1 to the
PINPOL field to invert the output shift
clock, and set the TIMDIS field as 3 to
keep target select asserted for as long
as there is data in the transmit buffer.
TIMCMP( n + 1) 0000_FFFFh Never compare.
TIMCFG( n + 1) 0000_1100h Enable when timer 0 is enabled and
disable when timer 0 is disabled.
TIMCTL( n + 1) 0003_0383h Configure 16-bit counter (never
compare) using inverted pin 3 output (as
target select).
SHIFTBUF n Data to transmit Transmit data can be written to
SHIFTBUF. Use the shifter status
flag to indicate when data can be
written using interrupt or DMA request.
Write to Shifter Buffer Bit Swapped
(SHIFTBUFBIS0 - SHIFTBUFBIS7)
instead to support MSB first transfer.
SHIFTBUF( n + 1) Data to receive Received data can be read from Shifter
Buffer (SHIFTBUF0 - SHIFTBUF7) .
Use the shifter status flag to indicate
when data can be read using interrupt
or DMA request. Read from Shifter
Buffer Bit Swapped (SHIFTBUFBIS0
- SHIFTBUFBIS7) instead to support
MSB first transfer.

#### 69.6.4 SPI target

SPI Target mode can be supported using one timer, two shifters, and four pins. Either CPHA = 0 or CPHA = 1 can be supported
and transfers can be supported using the DMA controller. For CPHA = 1, the select can remain asserted for multiple transfers and
the timer status flag can be used to indicate the end of the transfer.
You must write the transmit data to the transmit buffer register before the external target select asserts; otherwise, the shifter error
flag is set.
NOTE
Because of synchronization delays, the output valid time for the serial output data is 2.5 FLEXIO clock cycles. This
means the maximum baud rate is divide by 6 of the FLEXIO clock frequency.

---

*Page 2237*

Flexible I/O (FlexIO)
Table 406. SPI target (CPHA = 0) configuration
Register Value Configuration
SHIFTCFG n 0000_0000h Start and stop bit disabled.
SHIFTCTL n 0083_0002h Configure transmit using timer 0 on the
falling edge of shift clock with output
data on pin 0.
SHIFTCFG( n + 1) 0000_0000h Start and stop bit disabled.
SHIFTCTL( n + 1) 0000_0101h Configure receive using timer 0 on the
rising edge of shift clock with input data
on pin 1.
TIMCMP n 0000_003Fh Configure 32-bit transfer. Set
TIMCMP[15:0] as (number of bits × 2)
- 1.
TIMCFG n 0120_0600h Configure enable on trigger rising
edge. Initial clock state is logic 0 and
decrements on pin input.
TIMCTL n 06C0_0203h Configure 16-bit counter using pin 2
input (shift clock), with pin 3 input (target
select) as the inverted trigger.
SHIFTBUF n Data to transmit Transmit data can be written to Shifter
Buffer (SHIFTBUF0 - SHIFTBUF7) . Use
the shifter status flag to indicate when
data can be written using interrupt
or DMA request. Write to Shifter
Buffer Bit Swapped (SHIFTBUFBIS0
- SHIFTBUFBIS7) instead to support
MSB first transfer.
SHIFTBUF( n + 1) Data to receive Received data can be read from Shifter
Buffer (SHIFTBUF0 - SHIFTBUF7) .
Use the shifter status flag to indicate
when data can be read using interrupt
or DMA request. Read from Shifter
Buffer Bit Swapped (SHIFTBUFBIS0
- SHIFTBUFBIS7) instead to support
MSB first transfer.
Table 407. SPI target (CPHA = 1) configuration
Register Value Configuration
SHIFTCFG n 0000_0001h Shifter configured to load on first shift
and stop bit disabled.
SHIFTCTL n 0003_0002h Configure transmit using timer 0 on
rising edge of shift clock with output
data on pin 0.
SHIFTCFG( n + 1) 0000_0000h Start and stop bit disabled.
Table continues on the next page...

---

*Page 2238*

Flexible I/O (FlexIO)
Table 407. SPI target (CPHA = 1) configuration (continued)
Register Value Configuration
SHIFTCTL( n + 1) 0080_0101h Configure receive using timer 0 on
falling edge of shift clock with input data
on pin 1.
TIMCMP n 0000_003Fh Configure 32-bit transfer. Set
TIMCMP[15:0] as (number of bits × 2)
- 1).
TIMCFG n 0120_6602h Configure start bit, enable on trigger
rising edge, disable on trigger falling
edge. Initial clock state is logic 0 and
decrements on pin input.
TIMCTL n 06C0_0203h Configure 16-bit counter using pin 2
input (shift clock), with pin 3 input (target
select) as the inverted trigger.
SHIFTBUF n Data to transmit Transmit data can be written to Shifter
Buffer (SHIFTBUF0 - SHIFTBUF7) . Use
the shifter status flag to indicate when
data can be written using interrupt
or DMA request. Write to Shifter
Buffer Bit Swapped (SHIFTBUFBIS0
- SHIFTBUFBIS7) instead to support
MSB first transfer.
SHIFTBUF( n + 1) Data to receive Received data can be read from Shifter
Buffer (SHIFTBUF0 - SHIFTBUF7) .
Use the shifter status flag to indicate
when data can be read using interrupt
or DMA request. Read from Shifter
Buffer Bit Swapped (SHIFTBUFBIS0
- SHIFTBUFBIS7) instead to support
MSB first transfer.

#### 69.6.5 I2C controller

I2C Controller mode can be supported using two timers, two shifters, and two pins. One timer is used to generate the SCL output
and another one is used to control the shifters. The two shifters that are used to transmit and receive for every word, when receiving
the transmitter, must transmit FFh to 3-state the output. FLEXIO inserts a stop bit after every word to generate and verify the
ACK or NACK. FLEXIO waits for the first write to the transmit data buffer before enabling SCL generation. Data transfers can be
supported using the DMA controller and the shifter error flag sets on transmit underrun or receive overflow.
The first timer generates the bit clock for the entire packet (start to repeated start or stop condition), so you must program the
compare register with the total number of clock edges in the packet (minus one). The timer supports clock stretching using the
reset counter when pin is equal to output. However, this increases both the clock high and clock low periods by at least one FLEXIO
clock cycle each. The second timer uses the SCL input pin to control the transmit and receive shift registers. This enforces an SDA
data hold time by an extra two FLEXIO clock cycles.
Both the transmit and receive shifters must be serviced for each word in the transfer. The transmit shifter must transmit FFh when
receiving, and the receive shifter returns the data present on the SDA pin. The transmit shifter loads one additional word on the
last falling edge of the SCL pin. When generating a stop condition or a repeated start condition, this word must be 00h and FFh,
respectively. During the last word of a controller-receiver transfer, you must set the transmit SHIFTCFG n [SSTOP] field to generate
a NACK.

---

*Page 2239*

Flexible I/O (FlexIO)
The receive shift register asserts an error interrupt if a NACK is detected, but you are responsible for generating the stop or
repeated start condition. If a NACK is detected during controller-transmit, the interrupt routine must immediately write 00h (when
generating a stop condition) or FFh (when generating a repeated start condition) to the transmit shifter register. You must wait for
the next rising edge on SCL before disabling both timers. The transmit shifter must be disabled after the setup delay for a repeated
start or stop condition.
NOTE
Because of synchronization delays, the data valid time for the transmit output is two FLEXIO clock cycles. This
means the maximum baud rate is divide by 6 of the FLEXIO clock frequency.
To guarantee SDA hold time, the I2C controller data valid is delayed by two cycles because the clock output is passed through
a synchronizer before clocking the transmit or receive shifter. Because the SCL output is synchronous with FLEXIO clock, the
synchronization delay is one cycle, and then an additional cycle is involved to generate the output.
Table 408. I2C controller configuration
Register Value Configuration
SHIFTCFG n 0000_0032h Start bit enabled (logic 0) and stop bit
enabled (logic 1).
SHIFTCTL n 0101_0082h Configure transmit using timer 1 on the
rising edge of clock with inverted output
enable (open-drain output) on pin 0.
SHIFTCFG( n + 1) 0000_0020h Start bit disabled and stop bit enabled
(logic 0) for ACK or NACK detection.
SHIFTCTL( n + 1) 0180_0001h Configure receive using timer 1 on the
falling edge of clock with input data on
pin 0.
TIMCMP n 0000_2501h Configure 2 word transfer with baud rate
of divide by 4 of the FLEXIO clock. Set
TIMCMP[15:8] as (number of words ×
18) + 1, and set TIMCMP[7:0] as (baud
rate divider ÷ 2) - 1.
TIMCFG n 0102_2222h Configure start bit, stop bit, enable on
trigger high, disable on compare, reset
if output equals pin. Initial clock state is
logic 0 and is not affected by reset.
TIMCTL n 01C1_0101h Configure dual 8-bit counter using pin 1
output enable (SCL open-drain), with
the shifter 0 flag as the inverted trigger.
TIMCMP( n + 1) 0000_000Fh Configure 8-bit transfer. Set
TIMCMP[15:0] as (number of bits x 2)
- 1.
TIMCFG( n + 1) 0020_1112h Enable when timer 0 is enabled; disable
when timer 0 is disabled. Enable start
bit and stop bit at the end of each word
and decrement on pin input.
TIMCTL( n + 1) 01C0_0183h Configure 16-bit counter using inverted
pin 1 input (SCL).
Table continues on the next page...

---

*Page 2240*

Flexible I/O (FlexIO)
Table 408. I2C controller configuration (continued)
Register Value Configuration
SHIFTBUF n Data to transmit Transmit data can be written to
SHIFTBUFBBS[7:0]. Use the shifter
status flag to indicate when data can be
written using interrupt or DMA request.
SHIFTBUF( n + 1) Data to receive Received data can be read from
SHIFTBUFBIS[7:0]. Use the shifter
status flag to indicate when data can be
read using interrupt or DMA request.

#### 69.6.6 I2S controller

I2S Controller mode can be supported using two timers, two shifters, and four pins. One timer is used to generate the bit clock
and control the shifters and another timer is used to generate the frame sync. FLEXIO waits for the first write to the transmit data
buffer before enabling bit clock and frame sync generation. Data transfers are supported using the DMA controller and the shifter
error flag sets on transmit underrun or receive overflow.
The bit clock frequency is an even integer divide of the FLEXIO clock frequency. The initial frame sync assertion occurs at the
same time as the first bit clock edge. The timer uses the start bit to ensure that the frame sync is generated one clock cycle before
the first output data.
NOTE
Because of synchronization delays, the setup time for the receiver input is 1.5 FLEXIO clock cycles. This means
that the maximum baud rate is divide by 4 of the FLEXIO clock frequency.
Table 409. I2S controller configuration
Register Value Configuration
SHIFTCFG n 0000_0001h Load transmit data on first shift and stop
bit disabled.
SHIFTCTL n 0003_0002h Configure transmit using timer 0 on the
rising edge of clock with output data on
pin 0.
SHIFTCFG( n + 1) 0000_0000h Start and stop bit disabled.
SHIFTCTL( n + 1) 0080_0101h Configure receive using timer 0 on the
falling edge of clock with input data on
pin 1.
TIMCMP n 0000_3F01h Configure 32-bit transfer with baud rate
of divide by 4 of the FLEXIO clock. Set
TIMCMP[15:8] as (number of bits × 2)
- 1, and set TIMCMP[7:0] as (baud rate
divider ÷ 2) - 1.
TIMCFG n 0000_0202h Configure start bit, enable on trigger
high and never disable. Initial clock
state is logic 1.
TIMCTL n 01C3_0281h Configure dual 8-bit counter using
inverted pin 2 output (bit clock), with
Table continues on the next page...

---

*Page 2241*

Flexible I/O (FlexIO)
Table 409. I2S controller configuration (continued)
Register Value Configuration
shifter 0 flag as the inverted trigger.
Write 0 to the PINPOL field to invert the
polarity of the output shift clock.
TIMCMP( n + 1) 0000_007Fh Configure 32-bit transfer with baud rate
of divide by 4 of the FLEXIO clock.
Set TIMCMP[15:0] as (number of bits ×
baud rate divider) ÷ 1.
TIMCFG( n + 1) 0000_0100h Enable when timer 0 is enabled and
never disable.
TIMCTL( n + 1) 0003_0383h Configure 16-bit counter using inverted
pin 3 output (as frame sync). Write 0 to
the PINPOL field to invert the polarity of
the output frame sync.
SHIFTBUF n Data to transmit Transmit data can be written to Shifter
Buffer Bit Swapped (SHIFTBUFBIS0 -
SHIFTBUFBIS7) . Use the shifter status
flag to indicate when data can be
written using interrupt or DMA request.
Write to Shifter Buffer (SHIFTBUF0 -
SHIFTBUF7) instead to support LSB
first transfer.
SHIFTBUF( n + 1) Data to receive Received data can be read from Shifter
Buffer Bit Swapped (SHIFTBUFBIS0 -
SHIFTBUFBIS7) . Use the shifter status
flag to indicate when data can be
read using interrupt or DMA request.
Read from Shifter Buffer (SHIFTBUF0
- SHIFTBUF7) instead to support LSB
first transfer.

#### 69.6.7 I2S target

I2S Target mode can be supported using three timers, two shifters, and four pins. For single transmit and single receive, other
combinations of transmit and receive are possible.
The transmit data must be written to the transmit buffer register before the external frame sync asserts, otherwise the shifter error
flag is set.
NOTE
Because of synchronization delays, the output valid time for the serial output data is 2.5 FLEXIO clock cycles. This
means the maximum baud rate is divide by 6 of the FLEXIO clock frequency.
The output valid time of I2S target is maximum 2.5 cycles because there is a maximum 1.5 cycle delay on the clock
synchronization, plus one cycle to output the data.
Timer 2 detects the falling edge of frame sync (start of new frame) and asserts output until the rising edge of bit clock (when
the frame sync is normally sampled). Timer 0 detects the rising edge of bit clock with timer 2 output asserted and asserts output
for length of frame. Timer 1 detects the falling edge of bit clock with timer 0 output asserted and controls shift registers for
32-bit transfers.

---

*Page 2242*

Flexible I/O (FlexIO)
Table 410. I2S target configuration
Register Value Configuration
SHIFTCFG n 0000_0000h Start and stop bit disabled.
SHIFTCTL n 0103_0002h Configure transmit using timer 1 on the
rising edge of shift clock with output
data on pin 0.
SHIFTCFG( n + 1) 0000_0000h Start and stop bit disabled.
SHIFTCTL( n + 1) 0180_0101h Configure receive using timer 1 on the
falling edge of shift clock with input data
on pin 1.
TIMCMP n 0000_007Fh Configure two 32-bit transfers per
frame. Set TIMCMP[15:0] as (number of
bits × 4) - 1.
TIMCFG n 0020_2500h Configure enable on pin rising edge
(inverted bit clock) with trigger high
(timer 2) and disable on compare. Initial
clock state is logic 1 and decrements on
pin input (bit clock).
TIMCTL n 0B40_0203h Configure 16-bit counter using pin 2
input (bit clock), with timer 2 output as
the trigger.
TIMCMP( n + 1) 0000_003Fh Configure 32-bit transfers. Set
TIMCMP[15:0] as (number of bits × 2)
- 1.
TIMCFG( n + 1) 0020_2500h Configure enable on pin (bit clock) rising
edge with trigger (timer 0) high and
disable on compare. Initial clock state is
logic 1 and decrement on pin input (bit
clock).
TIMCTL( n + 1) 0340_0283h Configure 16-bit counter using inverted
pin 2 input (bit clock), with timer 0 output
as the trigger.
TIMCMP( n + 2) 0000_0000h Compare on zero (first edge).
TIMCFG( n + 2) 0020_6400h Configure enable on inverted pin (frame
sync) rising edge and disable on trigger
falling edge (bit clock). Initial clock state
is logic 1 and decrement on inverted pin
input (frame sync).
TIMCTL( n + 2) 04C0_0383h Configure 16-bit counter using inverted
pin 3 input (frame sync), with pin 2
inverted input (bit clock) as the trigger.
SHIFTBUF n Data to transmit Transmit data can be written to Shifter
Buffer Bit Swapped (SHIFTBUFBIS0 -
SHIFTBUFBIS7) . Use the shifter status
flag to indicate when data can be written
Table continues on the next page...

---

*Page 2243*

Flexible I/O (FlexIO)
Table 410. I2S target configuration (continued)
Register Value Configuration
using interrupt or DMA request. Write
to the SHIFTBUF register instead to
support LSB first transfer.
SHIFTBUF( n + 1) Data to receive Received data can be read from Shifter
Buffer Bit Swapped (SHIFTBUFBIS0 -
SHIFTBUFBIS7) . Use the shifter status
flag to indicate when data can be read
using interrupt or DMA request. Read
from the SHIFTBUF register instead to
support LSB first transfer.

#### 69.6.8 SENT receiver

The SENT receiver can be supported by using one timer and one pin. The timer is configured as Input-Capture mode, and captures
the counter value at the falling edge of the pin input. After the counter value is captured, the counter is automatically restarted from
counter value 0. Therefore, the captured value always indicates the period between the previous falling edge and current falling
edge. You can configure the CPU interrupt or DMA trigger at each capture of the counter. The CPU software performs the entire
SENT frame decoding with the latest tick width adjustment.
Table 411. SENT receiver configuration
Register Value Configuration
TIMCMP n — Stores counter value at the falling edge
of the pin.
TIMCFG n 0000_6000h Timer is always enabled. It is disabled
on trigger falling edge, decrement
counter on FLEXIO clock.
TIMCTL n 0040_0007h Single 16-bit input capture mode. The
PINSEL field selects the timer pin input
and output. Timer pin output disabled,
internal trigger selected, select pin 0 as
trigger.

#### 69.6.9 Camera interface

Camera interface can be supported using one timer, one or more shifters, and multiple pins. Multiple transfers can be supported
using the DMA controller.
The example configuration shown in the following table describes the FLEXIO configuration for interfacing with an 8-bit CMOS
sensor with PCLK, VSYNC, HREF, and D[7:0] outputs. The example uses a 128-bit buffer to capture 16 pixels of image data
before an interrupt or DMA transfer. You can use a bigger or smaller buffer depending on system DMA performance and FLEXIO
resource usage by other applications.
NOTE
You can use additional timers to track the number of pixels per row and number of rows per frame, or HREF or
VSYNC can be assigned as GPIO interrupts for software tracking.

---

*Page 2244*

Flexible I/O (FlexIO)
Table 412. Camera interface configuration for 8-bit CMOS sensor
Register Value Configuration
1
SHIFTCFG n ... n +2 0007_0100h Configure 8-bit parallel shift in from
adjacent shifter.
SHIFTCFG n +3 0007_0000h Configure 8-bit parallel shift in from pins
FXIO_D[7:0] (D[7:0]).
SHIFTCTL n ... n +3 0080_0001h Configure receive using timer 0 on the
negative edge of clock.
TIMCMP n 0000_001Fh Configure 16 pixel (8 bits/pixel ×
16 pixels = 128 bits) transfer. Set
TIMCMP[15:0] as (number of pixels × 2)
- 1.
TIMCFG n 0120_6600h Configure enable on trigger (HREF)
rising edge and disable on trigger falling
edge. Initial shift clock state is logic 0
and decrement on PCLK input.
TIMCTL n 12C0_0803h Configure 16-bit counter using
FXIO_D[8] input (PCLK), with
FXIO_D[9] input (HREF) as the inverted
trigger.
SHIFTBUF n ... n +3 Data to receive Received data can be read from
SHIFTBUF n ... n +3. Use the shifter status
flag to indicate when data can be read
using interrupt or DMA request.
1. n = 0 or 4

#### 69.6.10 Motorola 68K and Intel 8080 bus interface

Motorola 68K and Intel 8080 bus are two parallel interfaces commonly used by smart and asynchronous LCD controllers. With
GPIO, FLEXIO can drive these interfaces using one timer and one shifter. Additional shifters can be used to support large transfers
via the DMA controller.
Example configurations are provided for 1-beat and 16-beat transfers. Other lengths are also supported.
The following table provides an example of how to drive a 16-bit 68K or 8080 bus. For an 8080 bus, two GPIOs are used to drive
the nCS and RS pins. For a 68K bus, an additional GPIO is required to drive the RDWR pin.
Table 413. Motorola 68K and Intel 8080 write configuration
Register Value Configuration
SHIFTCFG0...7 000F_0100h Configure 16-bit parallel shift in from
adjacent shifter.
SHIFTCTL0 0003_0002h Configure transmit using timer 0 on the
positive edge of clock with data output
to FXIO_D[15:0].
SHIFTCTL1...7 0000_0002h Configure transmit using timer 0 on the
positive edge of clock.
Table continues on the next page...

---

*Page 2245*

Flexible I/O (FlexIO)
Table 413. Motorola 68K and Intel 8080 write configuration (continued)
Register Value Configuration
TIMCMP0 Configure 1 or 16-beat transfer with
0000_0101h (1 beat)
baud rate of divide by 4 of the FLEXIO
0000_1F01h (16 beats)
clock. Set TIMCMP[15:8] as (number of
beats × 2) - 1, and set TIMCMP[7:0] as
(baud rate divider ÷ 2) - 1.
TIMCFG0 0000_2200h Configure enable on trigger high and
disable on compare. Timer output high
on enable.
TIMCTL0 Configure dual 8-bit counter using
01C3_1001h (Motorola 68K, 1 beat)
FXIO_D[16] output (EN pin for 68K,
1DC3_1001h (Motorola 68K, 16 beats)
nWR pin for 8080), with shifter 0 (1-
01C3_1081h (Intel 8080, 1 beat) beat) or shifter 7 (16 beats) flag as the
inverted trigger.
1DC3_1081h (Intel 8080, 16 beats)
SHIFTBUF0...7 Data to transmit Transmit data can be written to
SHIFTBUF0 (1 beat) or SHIFTBUF0...7
(16-beats) to initiate a transfer; use the
shifter status flag to indicate when data
can be written using interrupt or DMA
request.
Table 414. Motorola 68K and Intel 8080 read configuration
Register Value Configuration
SHIFTCFG0...6 000F_0100h Configure 16-bit parallel shift in from the
adjacent shifter.
SHIFTCFG7 000F_0000h Configure 16-bit parallel shift in from
pin.
SHIFTCTL0...7 0080_0001h Configure receive using timer 0 on the
negative edge of clock with data input
from FXIO_D[15:0].
TIMCMP0 Configure 1 or 16-beat transfer with
0000_0101h (1 beat)
baud rate of divide by 4 of the FLEXIO
0000_1F01h (16 beats)
clock. Set TIMCMP[15:8] = (number of
beats × 2) - 1, and set TIMCMP[7:0] =
(baud rate divider ÷ 2) - 1.
TIMCFG0 0000_2220h Configure stop_bit. Enable on trigger
high and disable on compare. Timer
output high on enable.
TIMCTL0 Configure dual 8-bit counter using either
1DC3_1001h (Motorola 68K, 1 beat)
FXIO_D[16] output (EN pin for 68K) or
01C3_1001h (Motorola 68K, 16 beats)
FXIO_D[17] output (nRD pin for 8080),
1DC3_1181h (Intel 8080, 1 beat) with shifter 7 flag (1-beat) or shifter 0
flag (16 beats) as the inverted trigger.
01C3_1181h (Intel 8080, 16 beats)
Table continues on the next page...

---

*Page 2246*

Flexible I/O (FlexIO)
Table 414. Motorola 68K and Intel 8080 read configuration (continued)
Register Value Configuration
SHIFTBUF0...7 Data received Received data can be read from
SHIFTBUF0 (1 beat) or SHIFTBUF0...7
(16 beats). Use the shifter status flag to
indicate when data can be read using
interrupt or DMA request.
In general, any operation to a 68K or 8080 bus target begins with a register write cycle followed by one or more data read or write
cycles. To accomplish this, perform the following procedure:
1. Configure FLEXIO with 1-beat write configuration.
2. Configure GPIO to assert the nCS and RS pins (and deassert the RDWR pin for 68K).
3. Write register index data to SHIFTBUF0[15:0].
4. Configure GPIO to deassert the RS pin (and assert the RDWR pin for 68K data read).
5. Configure FLEXIO with the desired read or write configuration (1 or 16 beats).
6. Use the shifter status flag to trigger interrupt or DMA driven data transfers to and from Shifter Buffer (SHIFTBUF0
- SHIFTBUF7) .
7. Configure GPIO to deassert the nCS pin.

#### 69.6.11 Low-power state machine

Table 415 shows an example of a hypothetical state machine to illustrate the flexibility allowed in Shifter State mode.
In this example, FLEXIO waits for the FXIO_D[2] pin to assert and then drives a complementary square wave output at a frequency
of FLEXIO_CLK/131072 on the FXIO_D[1:0] pins while the comparator output is asserted. This assumes that the comparator is
connected to external trigger 15. See the chip-specific FLEXIO information for actual FLEXIO trigger mappings. Figure 369 shows
the states and transitions implemented by this example.

---

*Page 2247*

Flexible I/O (FlexIO)
STATE0
FXIO_D[2]=0
FXIO_D[1:0]=00
FXIO_D[2]=1
FXIO_D[2]=0
STATE1
CMP=0
FXIO_D[1:0]=01
CMP=1
STATE2
FXIO_D[1:0]=10
Figure 369. State diagram
Table 415. State machine configuration
Register Value Configuration
SHIFTCFG0...2 0000_0003h Enable FXIO_D[1:0] as outputs.
SHIFTCTL0 0080_0206h Configure for State mode using
FXIO_D[4:2] as inputs to select next
state and timer 0 output low to trigger
next state.
SHIFTBUF0 0020_8208h State0: Drive FXIO_D[1:0] = 00;
transition to state0 if FXIO_D[2] = 0,
state1 if FXIO_D[2] = 1.
SHIFTCTL1 0000_0206h Configure for State mode using
FXIO_D[4:2] as inputs to select next
state and timer 0 output high to trigger
next state.
SHIFTBUF1 0140_8408h State1: Drive FXIO_D[1:0]=01; transition
to state0 if FXIO_D[2]=0, state1 if CMP
= 0, state2 if CMP = 1 (FXIO_D[3]=1).
Table continues on the next page...

---

*Page 2248*

Flexible I/O (FlexIO)
Table 415. State machine configuration (continued)
Register Value Configuration
SHIFTCTL2 0080_0206h Configure for State mode using
FXIO_D[4:2] as inputs to select next
state and timer 0 output low to trigger
next state.
SHIFTBUF2 0224_9249h State2: Drive FXIO_D[1:0] = 10,
transition to state1 with timer 0 output
low.
TIMCMP0 Configure baud rate of divide by 131072
0000_FFFFh
of the FLEXIO clock. Set TIMCMP[7:0]
as (baud rate divider ÷ 2) - 1.
TIMCFG0 0000_0000h Configure timer always enabled.
TIMCTL0 Configure single 16-bit counter.
0000_0003h
TIMCFG1 0010_7600h Configure timer enabled on trigger rising
edge, disabled on trigger falling edge,
decrement on trigger edge.
TIMCTL1 Configure timer output enabled
0F03_0303h
on FXIO_D[3], external trigger 15
(comparator output) selected.

#### 69.6.12 Keypad interface

The keypad interface can support a 3 × 4 keypad matrix using three timers and three shifters, although a larger matrix can be
supported using additional shifters. The configuration is designed for four columns configured as active low open-drain pins and
three rows configured as input pins with pull-up resistors enabled.
One shifter is configured in Logic mode to assert its output when any of the row inputs are low, indicating a key is pressed. Use a
timer to filter the shifter output to ensure that the key is pressed for a minimum amount of time before performing the column scan.
A different shifter is configured for parallel transmit. Use this shifter to scan each column when a keypress is detected. When
not scanning, the shifter output is configured to assert all active low open-drain column outputs to detect any keypress. Use a
dedicated timer to control the transmit shifter.
The last shifter is configured for parallel receive. Use this shifter to capture the result of the column scanning so that you can
decode which key (or keys) was pressed. This configuration captures the state of both row and column pins for each scan,
although the row state can also be deduced by the shift order. Use a dedicated timer to control the receive shifter, which shifts at
half the frequency of the transmit shifter.
When the result of the key scan is available in the receive shifter register, FLEXIO continues to monitor the row inputs and can
trigger multiple scans from a single keypress. To support debouncing, you can decide how many consecutive scans must be
considered as a single keypress.
NOTE
Because the pins used in Logic mode are fixed per shifter and the shifters that support parallel shifts are limited, this
configuration is restricted to what pins and shifters it can use. Increasing the matrix beyond four-row inputs requires
multiple shifters in Logic mode. Increasing beyond four-column outputs requires concatenating transmit shifters to
create a larger shift register.

---

*Page 2249*

Flexible I/O (FlexIO)
Table 416. Keypad interface configuration
Register Value Configuration
SHIFTCFG0 0003_0032h Start bit enabled (logic 0) and stop bit
enabled (logic 1), configured for 4-bit
shift.
SHIFTCTL0 0101_0402h Configure transmit using timer 1 on
the rising edge of clock generating
open-drain output on pins[7:4] (column
outputs).
SHIFTBUF0 0804_0201h Static data containing the column scan
pattern; each column is scanned one-
hot with dead time in between.
SHIFTCFG1 0000_0020h In Logic mode, mask input 3.
SHIFTCTL1 0000_0007h Configure Logic mode.
SHIFTBUF1 07FF_07FFh Static data configuring Logic mode LUT.
Output asserts if pins [3:1] are logic 0.
SHIFTCFG3 0007_0000h Configured for 8-bit shift.
SHIFTCTL3 0280_0001h Configure receive using timer 2 on
falling edge of clock with input data on
pins [7:0] (both rows and columns; pin 0
is don't care).
TIMCMP0 0000_00ffh Configure prescanning glitch filter to
256 FLEXIO clock cycles. For different
filter cycles, configure TIMCMP[15:0] as
(filter cycles) - 1.
TIMCFG0 0103_6600h Configure enable on trigger rising edge
and disable on trigger falling edge. Initial
clock state is logic 0 and is not affected
by reset.
TIMCTL0 0540_0003h Configure 16-bit counter using the
shifter 1 flag (logic state) as trigger.
TIMCMP1 0000_0F3Fh Configure eight shifts (twice for each
column) at column scan rate of divide
by 128. For a different scan frequency,
define TIMCMP[7:0] as (scan divider ÷
2) - 1.
TIMCFG1 0020_2622h Enable on trigger rising edge, disable
on compare. Start and stop bits are
enabled.
TIMCTL1 0340_0001h Configure dual 8-bit counter using
timer 0 output as the trigger.
TIMCMP2 0000_0801h Configure four shifts at half the
frequency of the timer 1 trigger.
Table continues on the next page...

---

*Page 2250*

Flexible I/O (FlexIO)
Table 416. Keypad interface configuration (continued)
Register Value Configuration
TIMCFG2 0110_2100h Enable on timer 1 enable, disable on
compare, decrement on trigger input
with output initially negated, and not
affected by reset.
TIMCTL2 0740_0001h Configure dual 8-bit counter using
timer 1 output as the trigger.
SHIFTBUF3 Keypad scan result Keypad scan result can be read from
SHIFTBUF3. Each byte is the result of
one scan with both row and column pin
state from the scan (pin 0 is not used).
Use the shifter status flag to indicate
when data can be written using interrupt
or DMA request.

#### 69.7 Memory map and registers

#### 69.7.1 FLEXIO register descriptions

NOTE
Invalid register accesses, which include reading a write-only register, writing to a read-only register, or accessing
an invalid address, result in a bus error.
69.7.1.1 FLEXIO memory map
FlexIO base address: 4032_4000h
Offset Register Access Reset value
Width
(In bits)
0h Version ID (VERID) 32 R 0201_0003h
4h Parameter (PARAM) 32 R 0420_0808h
8h FLEXIO Control (CTRL) 32 RW 0000_0000h
Ch Pin State (PIN) 32 R 0000_0000h
10h Shifter Status (SHIFTSTAT) 32 RW 0000_0000h
14h Shifter Error (SHIFTERR) 32 RW 0000_0000h
18h Timer Status Flag (TIMSTAT) 32 RW 0000_0000h
20h Shifter Status Interrupt Enable (SHIFTSIEN) 32 RW 0000_0000h
24h Shifter Error Interrupt Enable (SHIFTEIEN) 32 RW 0000_0000h
28h Timer Interrupt Enable (TIMIEN) 32 RW 0000_0000h
30h Shifter Status DMA Enable (SHIFTSDEN) 32 RW 0000_0000h
38h Timer Status DMA Enable (TIMERSDEN) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 2251*

Flexible I/O (FlexIO)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
40h Shifter State (SHIFTSTATE) 32 RW 0000_0000h
48h Trigger Status (TRGSTAT) 32 RW 0000_0000h
4Ch External Trigger Interrupt Enable (TRIGIEN) 32 RW 0000_0000h
50h Pin Status (PINSTAT) 32 RW 0000_0000h
54h Pin Interrupt Enable (PINIEN) 32 RW 0000_0000h
58h Pin Rising Edge Enable (PINREN) 32 RW 0000_0000h
5Ch Pin Falling Edge Enable (PINFEN) 32 RW 0000_0000h
60h Pin Output Data (PINOUTD) 32 RW 0000_0000h
64h Pin Output Enable (PINOUTE) 32 RW 0000_0000h
68h Pin Output Disable (PINOUTDIS) 32 RW 0000_0000h
6Ch Pin Output Clear (PINOUTCLR) 32 RW 0000_0000h
70h Pin Output Set (PINOUTSET) 32 RW 0000_0000h
74h Pin Output Toggle (PINOUTTOG) 32 RW 0000_0000h
80h - 9Ch Shifter Control (SHIFTCTL0 - SHIFTCTL7) 32 RW 0000_0000h
100h - 11Ch Shifter Configuration (SHIFTCFG0 - SHIFTCFG7) 32 RW 0000_0000h
200h - 21Ch Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) 32 RW 0000_0000h
280h - 29Ch Shifter Buffer Bit Swapped (SHIFTBUFBIS0 - SHIFTBUFBIS7) 32 RW 0000_0000h
300h - 31Ch Shifter Buffer Byte Swapped (SHIFTBUFBYS0 - SHIFTBUFBYS7) 32 RW 0000_0000h
380h - 39Ch Shifter Buffer Bit Byte Swapped (SHIFTBUFBBS0 - 32 RW 0000_0000h
SHIFTBUFBBS7)
400h - 41Ch Timer Control (TIMCTL0 - TIMCTL7) 32 RW 0000_0000h
480h - 49Ch Timer Configuration (TIMCFG0 - TIMCFG7) 32 RW 0000_0000h
500h - 51Ch Timer Compare (TIMCMP0 - TIMCMP7) 32 RW 0000_0000h
680h - 69Ch Shifter Buffer Nibble Byte Swapped (SHIFTBUFNBS0 - 32 RW 0000_0000h
SHIFTBUFNBS7)
700h - 71Ch Shifter Buffer Halfword Swapped (SHIFTBUFHWS0 - 32 RW 0000_0000h
SHIFTBUFHWS7)
780h - 79Ch Shifter Buffer Nibble Swapped (SHIFTBUFNIS0 - SHIFTBUFNIS7) 32 RW 0000_0000h
800h - 81Ch Shifter Buffer Odd Even Swapped (SHIFTBUFOES0 - 32 RW 0000_0000h
SHIFTBUFOES7)
880h - 89Ch Shifter Buffer Even Odd Swapped (SHIFTBUFEOS0 - 32 RW 0000_0000h
SHIFTBUFEOS7)
Table continues on the next page...

---

*Page 2252*

Flexible I/O (FlexIO)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
900h - 91Ch Shifter Buffer Halfword Byte Swapped (SHIFTBUFHBS0 - 32 RW 0000_0000h
SHIFTBUFHBS7)
69.7.1.2 Version ID (VERID)
Offset
Register Offset
VERID 0h
Function
Indicates the version of FLEXIO.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R MAJOR MINOR
W
Reset 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R FEATURE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1
Fields
Field Function
31-24 Major Version Number
MAJOR Indicates the major version number of the module specification.
23-16 Minor Version Number
MINOR Indicates the minor version number of the module specification.
15-0 Feature Specification Number
FEATURE Indicates the feature set number.
0000_0000_0000_0000b - Standard features implemented
Table continues on the next page...

---

*Page 2253*

Flexible I/O (FlexIO)
Table continued from the previous page...
Field Function
0000_0000_0000_0001b - State, logic, and parallel modes supported
0000_0000_0000_0010b - Pin control registers supported
0000_0000_0000_0011b - State, logic, and parallel modes, plus pin control registers supported
69.7.1.3 Parameter (PARAM)
Offset
Register Offset
PARAM 4h
Function
Contains the number of shifters, timers, pins, and triggers.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TRIGGER PIN
W
Reset 0 0 0 0 0 1 0 0 0 0 1 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TIMER SHIFTER
W
Reset 0 0 0 0 1 0 0 0 0 0 0 0 1 0 0 0
Fields
Field Function
31-24 Trigger Number
TRIGGER Indicates the number of external triggers implemented.
23-16 Pin Number
PIN Indicates the number of pins implemented.
15-8 Timer Number
TIMER Indicates the number of timers implemented.
7-0 Shifter Number
SHIFTER Indicates the number of shifters implemented.

---

*Page 2254*

Flexible I/O (FlexIO)
69.7.1.4 FLEXIO Control (CTRL)
Offset
Register Offset
CTRL 8h
Function
Controls various aspects of the FLEXIO operation.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
Reserv
R 0
ed
DBGE
W 0
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
FASTA SWRS FLEXE
CC T N
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Any write value to this bit must be zero.
31
—
30 Debug Enable
DBGE Enables the FLEXIO operation in Debug mode.
0b - Disable
1b - Enable
Reserved
29-3
—
2 Fast Access
FASTACC Configures fast or normal register accesses to FLEXIO registers, but requires the FLEXIO functional
clock to be at least equal to the frequency of the bus clock.
0b - Normal
1b - Fast
Table continues on the next page...

---

*Page 2255*

Flexible I/O (FlexIO)
Table continued from the previous page...
Field Function
1 Software Reset
SWRST Specifies whether software reset is enabled. The software reset does not affect this register but it affects
all other logic in FLEXIO. All other register accesses are ignored until this field is cleared. The field
remains 1 until software clears it and the reset has cleared in the FLEXIO clock domain. If you write 1 to
this field, all FLEXIO registers except the Control register are reset.
0b - Disabled
1b - Enabled
0 FLEXIO Enable
FLEXEN Enables FLEXIO.
0b - Disable
1b - Enable
69.7.1.5 Pin State (PIN)
Offset
Register Offset
PIN Ch
Function
Indicates the status of the pin data input.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R PDI
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R PDI
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2256*

Flexible I/O (FlexIO)
Fields
Field Function
31-0 Pin Data Input
PDI Indicates the input data on each of the FLEXIO pins.
69.7.1.6 Shifter Status (SHIFTSTAT)
Offset
Register Offset
SHIFTSTAT 10h
Function
Contains shifter status flags.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 SSF
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-8
—
7-0 Shifter Status Flag
SSF Indicates the shifter status. This flag is updated in one of the following cases:
• If SHIFTCTL n [SMOD] = 001b (Receive mode), the status flag is set when SHIFTBUF is loaded with
data from the shifter (SHIFTBUF is full). The status flag is cleared when you read Shifter Buffer
(SHIFTBUF0 - SHIFTBUF7) .
Table continues on the next page...

---

*Page 2257*

Flexible I/O (FlexIO)
Table continued from the previous page...
Field Function
• If SHIFTCTL n [SMOD] = 010b (Transmit mode), the status flag is set when SHIFTBUF data is
transferred to the shifter (SHIFTBUF is empty) or when SHIFTCTL n [SMOD] is initially configured as
010b (Transmit mode). The status flag is cleared when you write to the SHIFTBUF register.
• If SHIFTCTL n [SMOD] = 100b (Match Store mode), the status flag is set when a match occurs between
SHIFTBUF and the shifter. The status flag is cleared when you read the SHIFTBUF register.
• If SHIFTCTL n [SMOD] = 101b (Match Continuous mode), the status flag returns the current match result
between SHIFTBUF and the shifter. You cannot clear the status flag by reading the SHIFTBUF register.
• If SHIFTCTL n [SMOD] = 110b (State mode), the status flag for a shifter sets when it is selected by the
current state pointer.
• If SHIFTCTL n [SMOD] = 111b (Logic mode), the status flag returns the current value of the
programmable logic block output.
You can clear this status flag by writing a logic one to the flag for all modes except Match Continuous mode ,
State mode , and Logic mode .
NOTE
This field behaves differently for register reads and writes.
When reading
0000_0000b - Clear
0000_0001b - Set
When writing
0000_0000b - No effect
0000_0001b - Clear the flag
69.7.1.7 Shifter Error (SHIFTERR)
Offset
Register Offset
SHIFTERR 14h
Function
Reports shifter errors.

---

*Page 2258*

Flexible I/O (FlexIO)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 SEF
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-8
—
7-0 Shifter Error Flag
SEF Indicates shifter error flag status. This flag is set when one of the following events occurs:
• If SHIFTCTL n [SMOD] = 001b (Receive mode), it indicates that either the shifter is ready to store
new data into SHIFTBUF before the previous data is read from SHIFTBUF (SHIFTBUF overrun), or
the received start or stop bit does not match the expected value.
• If SHIFTCTL n [SMOD] = 010b (Transmit mode), it indicates that the shifter is ready to load new data
from SHIFTBUF before new data is written into SHIFTBUF (SHIFTBUF underrun).
• If SHIFTCTL n [SMOD] = 100b (Match Store mode), it indicates the occurrence of a match event
before the previous match data is read from SHIFTBUF (SHIFTBUF overrun).
• If SHIFTCTL n [SMOD] = 101b (Match Continuous mode), the error flag is set when a match occurs
between SHIFTBUF and the shifter.
• If SHIFTCTL n [SMOD] = 111b (Logic mode), the error flag is set when the output of the
programmable logic block is asserted.
For SHIFTCTL n [SMOD] = 101b (Match Continuous mode), the flag can also be cleared when you read
Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) .
NOTE
This field behaves differently for register reads and writes.
When reading
0000_0000b - Clear
0000_0001b - Set
When writing
0000_0000b - No effect
0000_0001b - Clear the flag

---

*Page 2259*

Flexible I/O (FlexIO)
69.7.1.8 Timer Status Flag (TIMSTAT)
Offset
Register Offset
TIMSTAT 18h
Function
Reports timer status.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 TSF
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-8
—
7-0 Timer Status Flag
TSF Indicates timer status. This flag is set depending on Timer mode:
• In 8-bit baud counter mode, this flag is set when the upper 8-bit counter equals zero and
decrements.
• In 8-bit high PWM mode, this flag is set when the upper 8-bit counter equals zero and decrements.
• In 16-bit counter mode, this flag is set when the 16-bit counter equals zero and decrements.
• In 16-bit counter disable mode, TSF is set when a timer disable event is detected.
• In 8-bit word counter mode, TSF is set when the upper 8-bit counter equals zero and decrements.
• In 8-bit low PWM mode, TSF is set when the upper 8-bit counter equals zero and decrements.
• In 16-bit input capture mode, TSF is set when a timer disable event is detected and the flag is clear.
In this mode, you must read Timer Control (TIMCTL0 - TIMCTL7) only when TSF is set.
NOTE
This field behaves differently for register reads and writes.
Table continues on the next page...

---

*Page 2260*

Flexible I/O (FlexIO)
Table continued from the previous page...
Field Function
When reading
0000_0000b - Clear
0000_0001b - Set
When writing
0000_0000b - No effect
0000_0001b - Clear the flag
69.7.1.9 Shifter Status Interrupt Enable (SHIFTSIEN)
Offset
Register Offset
SHIFTSIEN 20h
Function
Enables shifter status interrupts.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
SSIE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-8
—
7-0 Shifter Status Interrupt Enable
SSIE Enables interrupt generation when the corresponding SHIFTSTAT[SSF] flag is set. If you write 0 to this field,
SHIFTSTAT[SSF] is disabled; and if you write 1 to this field, SHIFTSTAT[SSF] is enabled.
Table continues on the next page...

---

*Page 2261*

Flexible I/O (FlexIO)
Table continued from the previous page...
Field Function
0b - Disable
1b - Enable
69.7.1.10 Shifter Error Interrupt Enable (SHIFTEIEN)
Offset
Register Offset
SHIFTEIEN 24h
Function
Enables shifter error interrupts.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
SEIE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-8
—
7-0 Shifter Error Interrupt Enable
SEIE Enables interrupt generation when the corresponding SHIFTERR[SEF] flag is set. If you write 0 to this field,
SHIFTERR[SEF] is disabled; and if you write 1 to this field, SHIFTERR[SEF] is enabled.
0b - Disable
1b - Enable

---

*Page 2262*

Flexible I/O (FlexIO)
69.7.1.11 Timer Interrupt Enable (TIMIEN)
Offset
Register Offset
TIMIEN 28h
Function
Enables timer status interrupts.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
TEIE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-8
—
7-0 Timer Status Interrupt Enable
TEIE Enables interrupt generation when the corresponding TIMSTAT[TSF] flag is set. If you write 0 to this field,
TIMSTAT[TSF] is disabled; and if you write 1 to this field, TIMSTAT[TSF] is enabled.
0b - Disable
1b - Enable
69.7.1.12 Shifter Status DMA Enable (SHIFTSDEN)
Offset
Register Offset
SHIFTSDEN 30h
Function
Enables shifter DMA requests.

---

*Page 2263*

Flexible I/O (FlexIO)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
SSDE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-8
—
7-0 Shifter Status DMA Enable
SSDE Enables DMA request generation when the corresponding SHIFTSTAT[SSF] flag is set. If you write 0 to this
field, SHIFTSTAT[SSF] is disabled; and if you write 1 to this field, SHIFTSTAT[SSF] is enabled.
0b - Disable
1b - Enable
69.7.1.13 Timer Status DMA Enable (TIMERSDEN)
Offset
Register Offset
TIMERSDEN 38h
Function
Enables DMA requests when the timer status flag is set.

---

*Page 2264*

Flexible I/O (FlexIO)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
TSDE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-8
—
7-0 Timer Status DMA Enable
TSDE Enables DMA request generation when the corresponding TIMSTAT[TSF] flag is set.
When the timer status DMA request is enabled, reading or writing to a timer compare register clears the
corresponding timer status register. The DMA must therefore read or write to the timer compare register as
part of the DMA transfer; otherwise, the DMA request remains asserted.
0b - Disable
1b - Enable
69.7.1.14 Shifter State (SHIFTSTATE)
Offset
Register Offset
SHIFTSTATE 40h
Function
Contains a pointer to track the current shifter.

---

*Page 2265*

Flexible I/O (FlexIO)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
STATE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-3
—
2-0 Current State Pointer
STATE Maintains a pointer to track the current shifter (configured for State mode) enabled to drive outputs and
compute the next state. Reading this register when the state pointer is updating can result in the return of
an incorrect state.
The value that you write to this field overrides the current state.
69.7.1.15 Trigger Status (TRGSTAT)
Offset
Register Offset
TRGSTAT 48h
Function
Contains external trigger status flags.

---

*Page 2266*

Flexible I/O (FlexIO)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 ETSF
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-4
—
3-0 External Trigger Status Flag
ETSF Specifies whether the external trigger status flag is set when a rising edge is detected on the corresponding
external trigger input.
NOTE
This field behaves differently for register reads and writes.
When reading
0000b - Clear
0001b - Set
When writing
0000b - No effect
0001b - Clear the flag
69.7.1.16 External Trigger Interrupt Enable (TRIGIEN)
Offset
Register Offset
TRIGIEN 4Ch
Function
Enables external trigger interrupts.

---

*Page 2267*

Flexible I/O (FlexIO)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
TRIE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-4
—
3-0 External Trigger Interrupt Enable
TRIE Enables interrupt generation when the corresponding TRGSTAT[ETSF] flag is set. If you write 0 to this
field, TRGSTAT[ETSF] is disabled, and if you write 1 to this field, TRGSTAT[ETSF] is enabled.
0b - Disable
1b - Enable
69.7.1.17 Pin Status (PINSTAT)
Offset
Register Offset
PINSTAT 50h
Function
Contains pin status flags.

---

*Page 2268*

Flexible I/O (FlexIO)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R PSF
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R PSF
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Pin Status Flag
PSF Indicates whether the pin status flag is set when a rising edge or falling edge (if configured) is detected on
the corresponding pin, as configured by the pin.
NOTE
This field behaves differently for register reads and writes.
When reading
0000_0000_0000_0000_0000_0000_0000_0000b - Clear
0000_0000_0000_0000_0000_0000_0000_0001b - Set
When writing
0000_0000_0000_0000_0000_0000_0000_0000b - No effect
0000_0000_0000_0000_0000_0000_0000_0001b - Clear the flag
69.7.1.18 Pin Interrupt Enable (PINIEN)
Offset
Register Offset
PINIEN 54h
Function
Enables pin status interrupts.

---

*Page 2269*

Flexible I/O (FlexIO)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
PSIE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PSIE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Pin Status Interrupt Enable
PSIE Enables interrupt generation when the corresponding PINSTAT[PSF] flag is set. If you write 0 to this
field, PINSTAT[PSF] is disabled, and if you write 1 to this field, PINSTAT[PSF] is enabled.
0b - Disable
1b - Enable
69.7.1.19 Pin Rising Edge Enable (PINREN)
Offset
Register Offset
PINREN 58h
Function
Enables the pin status flag on a rising edge.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
PRE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PRE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2270*

Flexible I/O (FlexIO)
Fields
Field Function
31-0 Pin Rising Edge
PRE Specifies whether the pin status flag is set whenever a rising edge is detected on the pin.
0b - Not set
1b - Set
69.7.1.20 Pin Falling Edge Enable (PINFEN)
Offset
Register Offset
PINFEN 5Ch
Function
Enables the pin status flag on a falling edge.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
PFE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PFE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Pin Falling Edge
PFE Specifies whether the pin status flag is set whenever a falling edge is detected on the pin.
0b - Not set
1b - Set

---

*Page 2271*

Flexible I/O (FlexIO)
69.7.1.21 Pin Output Data (PINOUTD)
Offset
Register Offset
PINOUTD 60h
Function
Contains data output when direct pin output is enabled.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
OUTD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
OUTD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Output Data
OUTD Configures the value driven on the corresponding pin when direct pin output is enabled.
0b - Logic zero
1b - Logic one
69.7.1.22 Pin Output Enable (PINOUTE)
Offset
Register Offset
PINOUTE 64h
Function
Enables pin output.

---

*Page 2272*

Flexible I/O (FlexIO)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
OUTE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
OUTE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Output Enable
OUTE Enables direct output on the corresponding pin. If this field is 0, the pin is controlled by timer/shifter
configuration, and if this field is 1, pin is an output and driven with the value of Pin Output Data
(PINOUTD) .
0b - Controlled by timer/shifter configuration
1b - Output; driven with value of PINOUTD
69.7.1.23 Pin Output Disable (PINOUTDIS)
Offset
Register Offset
PINOUTDIS 68h
Function
Disables pin output.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W OUTDIS
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W OUTDIS
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2273*

Flexible I/O (FlexIO)
Fields
Field Function
31-0 Output Disable
OUTDIS Configures the corresponding pins to disable direct output. If this field is 1, the corresponding fields in Pin
Output Data (PINOUTD) and Pin Output Enable (PINOUTE) become 0.
0b - No effect
1b - Corresponding fields become 0
69.7.1.24 Pin Output Clear (PINOUTCLR)
Offset
Register Offset
PINOUTCLR 6Ch
Function
Clears pin output.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W OUTCLR
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W OUTCLR
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Output Clear
OUTCLR Configures the corresponding pins to output zero. If this field is 1, the corresponding field in Pin Output
Data (PINOUTD) becomes 0 and the one in Pin Output Enable (PINOUTE) becomes 1.
0b - No effect
1b - Corresponding field in PINOUTD becomes 0; corresponding field in PINOUTE becomes 1

---

*Page 2274*

Flexible I/O (FlexIO)
69.7.1.25 Pin Output Set (PINOUTSET)
Offset
Register Offset
PINOUTSET 70h
Function
Sets pin output.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W OUTSET
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W OUTSET
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Output Set
OUTSET Configures the corresponding pins to output logic one. If this field is 1, the corresponding fields in Pin
Output Data (PINOUTD) and Pin Output Enable (PINOUTE) become 1.
0b - No effect
1b - Corresponding fields become 1
69.7.1.26 Pin Output Toggle (PINOUTTOG)
Offset
Register Offset
PINOUTTOG 74h
Function
Toggles pin output.

---

*Page 2275*

Flexible I/O (FlexIO)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W OUTTOG
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W OUTTOG
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Output Toggle
OUTTOG Configures the corresponding pins to toggle. If this field is 1, the corresponding field in Pin Output Data
(PINOUTD) is inverted and the one in Pin Output Enable (PINOUTE) becomes 1.
0b - No effect
1b - Corresponding field in PINOUTD is inverted; corresponding field in PINOUTE becomes 1
69.7.1.27 Shifter Control (SHIFTCTL0 - SHIFTCTL7)
Offset
For n = 0 to 7:
Register Offset
SHIFTCTLn 80h + (n × 4h)
Function
Provides shifter controls.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
TIMPO
TIMSEL PINCFG
L
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
PINPO
PINSEL SMOD
L
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2276*

Flexible I/O (FlexIO)
Fields
Field Function
Reserved
31-27
—
26-24 Timer Select
TIMSEL Selects which timer is used for controlling the logic or shift register and generating the shift clock.
TIMSEL = i selects TIMERi.
23 Timer Polarity
TIMPOL Determines whether the shift occurs on the positive edge or negative edge of the shift clock.
0b - Positive edge
1b - Negative edge
Reserved
22-18
—
17-16 Shifter Pin Configuration
PINCFG Specifies shifter pin configuration.
For pins configured as an output (PINCFG = 11b), this field takes effect when you write to the register.
NOTE
When initially configuring PINCFG as 11b, FLEXIO may briefly drive the pin low. To avoid
this, you can configure PINCFG as 10b along with the rest of the Control register and then
perform a subsequent write to set PINCFG as 11b.
Likewise, when changing the value of PINCFG from 11b to 00b, you must perform an initial
write to set PINCFG as 10b and then perform a subsequent write to update the rest of the
Control register with the value of PINCFG as 00b.
00b - Shifter pin output disabled
01b - Shifter pin open-drain or bidirectional output enable
10b - Shifter pin bidirectional output data
11b - Shifter pin output
Reserved
15-13
—
12-8 Shifter Pin Select
PINSEL Selects the pin that is used by the shifter input or output. PINSEL = i selects the FXIO_Di pin. For pins
configured as an output (PINCFG = 11b), this field takes effect when you write to the register.
7 Shifter Pin Polarity
PINPOL Specifies the shifter pin polarity. For pins configured as an output (PINCFG = 11b), this field takes effect
when you write to this register.
Table continues on the next page...

---

*Page 2277*

Flexible I/O (FlexIO)
Table continued from the previous page...
Field Function
0b - Active high
1b - Active low
Reserved
6-3
—
2-0 Shifter Mode
SMOD Configures the mode of the shifter.
000b - Disable
001b - Receive mode; capture the current shifter content into SHIFTBUF on expiration of the
timer
010b - Transmit mode; load SHIFTBUF contents into the shifter on expiration of the timer
011b - Reserved
100b - Match Store mode; shifter data is compared to SHIFTBUF content on expiration of the
timer
101b - Match Continuous mode; shifter data is continuously compared to SHIFTBUF contents
110b - State mode; SHIFTBUF contents store programmable state attributes
111b - Logic mode; SHIFTBUF contents implement programmable logic lookup table
69.7.1.28 Shifter Configuration (SHIFTCFG0 - SHIFTCFG7)
Offset
For n = 0 to 7:
Register Offset
SHIFTCFGn 100h + (n × 4h)
Function
Provides fields for shifter configuration.

---

*Page 2278*

Flexible I/O (FlexIO)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
PWIDTH
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0
SSIZE LATST INSRC SSTOP SSTART
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-21
—
20-16 Parallel Width
PWIDTH Configures the number of bits to be shifted on each shift clock for all shifters:
• 1-bit shift for PWIDTH = 0
• 2-bit shift for PWIDTH = 1
• 4-bit shift for PWIDTH = 2...3
• 8-bit shift for PWIDTH = 4...7
• 16-bit shift for PWIDTH = 8...15
• 32-bit shift for PWIDTH = 16...31
For shifters that support parallel transmit (SHIFTER0, SHIFTER4, ...) or parallel receive (SHIFTER3,
SHIFTER7, ...), this field, together with PINSEL, also selects the pins to be driven or sampled on each shift
clock: FXIO_D[PINSEL+PWIDTH]:FXIO_D[PINSEL].
Shifters that do not support parallel transmit or parallel receive only support parallel shift when
SHIFTCFG n [INSRC] = 1.
If SHIFTCTL n [SMOD] = 110b (State mode), use this field to disable state outputs (see State mode ).
Reserved
15-13
—
12 Shifter Size
SSIZE Configures the size of the Shift registers.
A 24-bit Shift register shifts data only into bits [23:0] and does not update bits [31:24] during shift operations.
When the Shift register is configured for a 24-bit shift, configuring PWIDTH as 8..15 performs a 12-bit shift
and PWIDTH as 16..31 performs a 24-bit shift.
Table continues on the next page...

---

*Page 2279*

Flexible I/O (FlexIO)
Table continued from the previous page...
Field Function
0b - 32-bit
1b - 24-bit
Reserved
11-10
—
9 Late Store
LATST Configures what happens when a receive or match Shift register is configured to both shift and store on
the same cycle.
0b - Store the pre-shift register state
1b - Store the post-shift register state
8 Input Source
INSRC Selects the input source for the shifter. Configuring this field as 1 is not supported for the last shifter.
0b - Pin
1b - Shifter n+1 output
Reserved
7
—
Reserved
6
—
5-4 Shifter Stop
SSTOP Allows automatic stop bit insertion, if the selected timer has also enabled a stop bit, when
SHIFTCTL n [SMOD] is 10b (Transmit mode).
If SHIFTCTL n [SMOD] is 1b or 100b (Receive mode or Match Store mode), this field allows automatic stop
bit checking if the selected timer has also enabled a stop bit.
If SHIFTCTL n [SMOD] is 110b (State mode), this field disables state outputs (see State mode ).
If SHIFTCTL n [SMOD] is 111b (Logic mode), this field masks logic pin inputs (see Logic mode ).
00b - Stop bit disabled for Transmitter, Receiver, and Match Store modes
01b - Stop bit disabled for Transmitter, Receiver, and Match Store modes; when timer is in stop
condition, Receiver and Match Store modes store receive data on the configured shift edge
10b - Transmitter mode outputs stop bit value 0 in Match Store mode; if stop bit is not 0, Receiver
and Match Store modes set error flag (when timer is in stop condition, these modes also store
receive data on the configured shift edge)
11b - Transmitter mode outputs stop bit value 1 in Match Store mode; if stop bit is not 1, Receiver
and Match Store modes set error flag (when timer is in stop condition, these modes also store
receive data on the configured shift edge)
Reserved
3-2
Table continues on the next page...

---

*Page 2280*

Flexible I/O (FlexIO)
Table continued from the previous page...
Field Function
—
1-0 Shifter Start
SSTART Allows automatic start bit insertion, if the selected timer has also enabled a start bit, when
SHIFTCTL n [SMOD] is 10b (Transmit mode).
If SHIFTCTL n [SMOD] = 1b (Receive mode) or 100b (Match Store mode), this field allows automatic start
bit checking if the selected timer has also enabled a start bit.
If SHIFTCTL n [SMOD] is 110b (State mode), this field disables state outputs (see State mode ).
If SHIFTCTL n [SMOD] = 111b (Logic mode), this field masks logic pin inputs (see Logic mode ).
00b - Start bit disabled for Transmitter, Receiver, and Match Store modes; Transmitter mode
loads data on enable
01b - Start bit disabled for Transmitter, Receiver, and Match Store modes; Transmitter mode
loads data on first shift
10b - Transmitter mode outputs start bit value 0 before loading data on first shift; if start bit is not
0, Receiver and Match Store modes set error flag
11b - Transmitter mode outputs start bit value 1 before loading data on first shift; if start bit is not
1, Receiver and Match Store modes set error flag
69.7.1.29 Shifter Buffer (SHIFTBUF0 - SHIFTBUF7)
Offset
For n = 0 to 7:
Register Offset
SHIFTBUFn 200h + (n × 4h)
Function
Contains shift buffer data.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SHIFTBUF
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SHIFTBUF
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2281*

Flexible I/O (FlexIO)
Fields
Field Function
31-0 Shift Buffer
SHIFTBUF Contains the data to be matched with the shifter contents and is used for various other functions, depending
on the setting of SHIFTCTL n [SMOD] :
• If SHIFTCTL0[SMOD] is 1b (Receive mode), shifter data is transferred into SHIFTBUF at the
expiration of the timer. You must read this register only when the corresponding SHIFTSTAT[SSF]
flag is set, indicating that new shifter data is available.
• If SHIFTCTL0[SMOD] is 10b (Transmit mode), SHIFTBUF data is transferred into the shifter before
the timer begins.
• If SHIFTCTL0[SMOD] is 100b (Match Store mode), SHIFTBUF[31:16] contains the data to be
matched with the shifter contents and SHIFTBUF[15:0] can be used to mask the match result (1
= mask, 0 = no mask). The match is checked when the timer expires. Shifter data [31:16] is written
to SHIFTBUF[31:16] whenever a match event occurs. You must read this register only when the
corresponding shifter status flag is set, indicating that new shifter data is available.
• If SHIFTCTL0[SMOD] is 101b (Match Continuous mode), SHIFTBUF[31:16] contains the data to be
matched with the shifter contents, and SHIFTBUF[15:0] can be used to mask the match result (1 =
mask, 0 = no mask).
• If SHIFTCTL0[SMOD] is 111b (Logic mode), SHIFTBUF[31:0] implements a 5-input, 32-bit
programmable logic lookup table (see Logic mode ).
• If SHIFTCTL0[SMOD] is 110b (State mode), use SHIFTBUF[31:24] to drive the output value when
this shifter is selected by the current state pointer and use SHIFTBUF[23:0] to configure the value of
the next state transition (see State mode ).
69.7.1.30 Shifter Buffer Bit Swapped (SHIFTBUFBIS0 - SHIFTBUFBIS7)
Offset
For n = 0 to 7:
Register Offset
SHIFTBUFBISn 280h + (n × 4h)
Function
Contains Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) content, but it is bit-swapped.

---

*Page 2282*

Flexible I/O (FlexIO)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SHIFTBUFBIS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SHIFTBUFBIS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Shift Buffer
SHIFTBUFBIS Acts as an alias to Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) , but reads or writes to this register are
bit-swapped. Reads return SHIFTBUF[0:31].
69.7.1.31 Shifter Buffer Byte Swapped (SHIFTBUFBYS0 - SHIFTBUFBYS7)
Offset
For n = 0 to 7:
Register Offset
SHIFTBUFBYSn 300h + (n × 4h)
Function
Contains Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) content, but it is byte-swapped.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SHIFTBUFBYS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SHIFTBUFBYS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2283*

Flexible I/O (FlexIO)
Fields
Field Function
31-0 Shift Buffer
SHIFTBUFBYS Acts as an alias to Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) , but reads or writes to this register are
byte-swapped. Reads return {SHIFTBUF[7:0], SHIFTBUF[15:8], SHIFTBUF[23:16], SHIFTBUF[31:24]}.
69.7.1.32 Shifter Buffer Bit Byte Swapped (SHIFTBUFBBS0 - SHIFTBUFBBS7)
Offset
For n = 0 to 7:
Register Offset
SHIFTBUFBBSn 380h + (n × 4h)
Function
Contains the register data for Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) , but it is bit-swapped within each byte.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SHIFTBUFBBS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SHIFTBUFBBS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Shift Buffer
SHIFTBUFBBS Acts as an alias to Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) , except that reads or writes to this register
are bit-swapped within each byte. Reads return {SHIFTBUF[24:31], SHIFTBUF[16:23], SHIFTBUF[8:15],
SHIFTBUF[0:7]}.
69.7.1.33 Timer Control (TIMCTL0 - TIMCTL7)
Offset
For n = 0 to 7:

---

*Page 2284*

Flexible I/O (FlexIO)
Register Offset
TIMCTLn 400h + (n × 4h)
Function
Controls various settings for timer n .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
TRGP TRGS
TRGSEL PINCFG
OL RC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
PINPO PININ ONETI
PINSEL TIMOD
L S M
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-30
—
29-24 Trigger Select
TRGSEL Selects the trigger.
The valid values for TRGSEL depend on the configuration of Parameter (PARAM) :
• If TRGSRC = 1, the valid values for n depend on the settings of PARAM[PIN] , PARAM[TIMER] ,
and PARAM[SHIFTER] .
• If TRGSRC = 0, the valid values for n depend on PARAM[TRIGGER].
See the chip-specific FLEXIO information for external trigger selection.
NOTE
For a pin, n = 0 to 31, for a shifter, n = 0 to 7, and for a timer, n = 0 to 7.
If TRGSRC = 0, configure the trigger selection as n = external trigger n input.
If TRGSRC = 1, you can configure the internal trigger to select an input pin as 2× n = pin n input.
If TRGSRC = 1, you can configure the internal trigger to select a shifter or timer signal as:
• 4× n + 1 = shifter n status flag
• 4× n + 3 = timer n trigger output
Following are the values for expanded internal trigger selection (TRGSRC = 1):
Table continues on the next page...

---

*Page 2285*

Flexible I/O (FlexIO)
Table continued from the previous page...
Field Function
• 0000 = Pin 0
• 0001 = Shifter 0 flag
• 0010 = Pin 1
• 0011 = Timer 0 trigger
• 0100 = Pin 2
• 0101 = Shifter 1 flag
• 0110 = Pin 3
• 0111 = Timer 1 trigger
• ...
• This continues up to pin 31, shifter 7, and timer 7.
23 Trigger Polarity
TRGPOL Specifies whether the trigger is active high or active low.
0b - Active high
1b - Active low
22 Trigger Source
TRGSRC Specifies whether the selected trigger source is external or internal.
0b - External
1b - Internal
Reserved
21-18
—
17-16 Timer Pin Configuration
PINCFG Configures the direction of the timer pin. For pins configured as an output (PINCFG = 11b), this field takes
effect when you write to the register.
NOTE
When you initially configure PINCFG as 11b, FLEXIO may briefly drive the pin low. To avoid
this, configure PINCFG as 10b along with the rest of the Control register and then perform
a subsequent write to set the value of PINCFG as 11b.
Likewise, when changing the value of PINCFG from 11b to 00b, you must perform an initial
write to set PINCFG as 10b, and then perform a subsequent write to update the rest of the
Control register with PINCFG as 00b.
00b - Timer pin output disabled
01b - Timer pin open-drain or bidirectional output enable
10b - Timer pin bidirectional output data
11b - Timer pin output
Table continues on the next page...

---

*Page 2286*

Flexible I/O (FlexIO)
Table continued from the previous page...
Field Function
Reserved
15-13
—
12-8 Timer Pin Select
PINSEL Selects the pin that is used by the timer input or output. PINSEL = i selects the FXIO_Di pin. For pins
configured as an output (PINCFG = 11b), this field takes effect when you write to the register.
7 Timer Pin Polarity
PINPOL Specifies the timer pin polarity. For pins configured as an output (PINCFG = 11b), this field takes effect
when you write to the register.
0b - Active high
1b - Active low
6 Timer Pin Input Select
PININS Specifies what selects the timer pin input. If this field is 1, the timer input pin is different from the timer
output pin. PINSEL must select an even-numbered pin when this field is 1, which means that the output
pin is even-numbered and input pin is odd-numbered.
0b - PINSEL selects timer pin input and output
1b - PINSEL + 1 selects the timer pin input; timer pin output remains selected by PINSEL
5 Timer One Time Operation
ONETIM Configures the timer to perform a single enable or disable iteration. Clear the timer status flag for the
timer to be enabled again.
0b - Generate the timer enable event as normal
1b - Block the timer enable event unless the timer status flag is clear
Reserved
4-3
—
2-0 Timer Mode
TIMOD Specifies the timer mode:
• In 8-bit baud counter mode, the lower 8 bits of the counter and compare register are used to
configure the baud rate of the timer shift clock. The upper 8 bits are used to configure the shifter bit
count.
• In 8-bit PWM high mode, the lower 8 bits of the counter and compare register are used to configure
the high period of the timer shift clock. The upper 8 bits are used to configure the low period of the
timer shift clock. The shifter bit count is configured using another timer or external signal.
• In 16-bit counter mode, the full 16 bits of the counter and compare register are used to configure
either the baud rate of the shift clock or the shifter bit count.
• In 16-bit counter disable mode, the full 16 bits of the counter and compare register are used to
configure either the baud rate of the shift clock or the shifter bit count.
Table continues on the next page...

---

*Page 2287*

Flexible I/O (FlexIO)
Table continued from the previous page...
Field Function
• In 8-bit word counter mode, the lower 8 bits of the counter and compare register are used to
configure the shifter bit count. The upper 8 bits are used to configure the shifter word count.
• In 8-bit PWM low mode, the lower 8 bits of the counter and compare register are used to configure
the low period of the timer shift clock. The upper 8 bits are used to configure the high period of the
timer shift clock. Use another timer or external signal to configure the shifter bit count.
• In 16-bit input capture mode, the inverted value of the 16-bit counter is latched into the compare
register when a timer counter disable condition is detected (as configured by TIMCFG n [TIMDIS] ).
This also sets the timer status flag. The timer counter is immediately restarted from FFFFh.
000b - Timer disabled
001b - Dual 8-bit counters baud mode
010b - Dual 8-bit counters PWM high mode
011b - Single 16-bit counter mode
100b - Single 16-bit counter disable mode
101b - Dual 8-bit counters word mode
110b - Dual 8-bit counters PWM low mode
111b - Single 16-bit input capture mode
69.7.1.34 Timer Configuration (TIMCFG0 - TIMCFG7)
Offset
For n = 0 to 7:
Register Offset
TIMCFGn 480h + (n × 4h)
Function
Controls various aspects of timer configuration.
The options to enable or disable the timer using the timer n - 1 enable or disable are reserved when n is evenly divisible by 4 (timer
0, for example).
NOTE
The pin and trigger level and edges specified in this register refer to the signal state after being modified by the
settings of TIMCTL n [PINPOL] and TIMCTL n [TRGPOL] . For example, "trigger low" means that a trigger is actually
at logic level 1 if TIMCTL n [TRGPOL] is 1 (active low).

---

*Page 2288*

Flexible I/O (FlexIO)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
TIMOUT TIMDEC TIMRST
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0
TSTA
TIMDIS TIMENA TSTOP
RT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-26
—
25-24 Timer Output
TIMOUT Configures the initial state of the timer output and whether it is affected by the timer reset.
00b - Logic one when enabled; not affected by timer reset
01b - Logic zero when enabled; not affected by timer reset
10b - Logic one when enabled and on timer reset
11b - Logic zero when enabled and on timer reset
Reserved
23
—
22-20 Timer Decrement
TIMDEC Configures the source of the timer decrement and that of the shift clock.
000b - Decrement counter on FLEXIO clock; shift clock equals timer output
001b - Decrement counter on trigger input (both edges); shift clock equals timer output
010b - Decrement counter on pin input (both edges); shift clock equals pin input
011b - Decrement counter on trigger input (both edges); shift clock equals trigger input
100b - Decrement counter on FLEXIO clock divided by 16; shift clock equals timer output
101b - Decrement counter on FLEXIO clock divided by 256; shift clock equals timer output
110b - Decrement counter on pin input (rising edge); shift clock equals pin input
111b - Decrement counter on trigger input (rising edge); shift clock equals trigger input
Reserved
19
—
Table continues on the next page...

---

*Page 2289*

Flexible I/O (FlexIO)
Table continued from the previous page...
Field Function
18-16 Timer Reset
TIMRST Configures the condition that causes the timer counter (and optionally the timer output) to be reset. In
8-bit counter mode, the timer reset only resets the lower 8 bits that configure the baud rate. In all other
modes, the timer reset resets full 16 bits of the counter.
000b - Never reset timer
001b - Timer reset on timer output high.
010b - Timer reset on timer pin equal to timer output
011b - Timer reset on timer trigger equal to timer output
100b - Timer reset on timer pin rising edge
101b - Reserved
110b - Timer reset on trigger rising edge
111b - Timer reset on trigger rising or falling edge
Reserved
15
—
14-12 Timer Disable
TIMDIS Configures the condition that causes the timer to be disabled and stop decrementing.
000b - Timer never disabled
001b - Timer disabled on timer n-1 disable
010b - Timer disabled on timer compare (upper 8 bits match and decrement)
011b - Timer disabled on timer compare (upper 8 bits match and decrement) and trigger low
100b - Timer disabled on pin rising or falling edge
101b - Timer disabled on pin rising or falling edge provided trigger is high
110b - Timer disabled on trigger falling edge
111b - Reserved
Reserved
11
—
10-8 Timer Enable
TIMENA Configures the condition that causes the timer to be enabled and start decrementing.
000b - Timer always enabled
001b - Timer enabled on timer n-1 enable
010b - Timer enabled on trigger high
011b - Timer enabled on trigger high and pin high
Table continues on the next page...

---

*Page 2290*

Flexible I/O (FlexIO)
Table continued from the previous page...
Field Function
100b - Timer enabled on pin rising edge
101b - Timer enabled on pin rising edge and trigger high
110b - Timer enabled on trigger rising edge
111b - Timer enabled on trigger rising or falling edge
Reserved
7-6
—
5-4 Timer Stop
TSTOP Specifies whether the stop bit is enabled. The stop bit can be added on a timer compare (between each
word) or on a timer disable. When stop bit is enabled, configured shifters output the contents of the stop
bit when the timer is disabled. When stop bit is enabled on timer disable, the timer remains disabled until
the next rising edge of the shift clock. If configured for both timer compare and timer disable, only one
stop bit is inserted on timer disable.
00b - Disabled
01b - Enabled on timer compare
10b - Enabled on timer disable
11b - Enabled on timer compare and timer disable
Reserved
3-2
—
1 Timer Start
TSTART Specifies whether the start bit is enabled. If it is enabled, configured shifters output the contents of the
start bit when the timer is enabled. The timer counter reloads from the compare register on the first rising
edge of the shift clock.
0b - Disabled
1b - Enabled
Reserved
0
—
69.7.1.35 Timer Compare (TIMCMP0 - TIMCMP7)
Offset
For n = 0 to 7:
Register Offset
TIMCMPn 500h + (n × 4h)

---

*Page 2291*

Flexible I/O (FlexIO)
Function
Contains the timer compare value.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
CMP
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-16
—
15-0 Timer Compare Value
CMP Loads into the timer counter when the timer is first enabled, when the timer is reset, and when the timer
decrements down to zero.
In 8-bit baud counter mode, the lower 8 bits configure the baud rate divider as (CMP[7:0] + 1) × 2. The upper
8 bits configure the number of bits in each word as (CMP[15:8] + 1) ÷ 2.
In 8-bit PWM high mode, the lower 8 bits configure the high period of the output to (CMP[7:0] + 1) and the
upper 8 bits configure the low period of the output to (CMP[15:8] + 1).
In 16-bit counter mode, the compare value can be used to generate the baud rate divider (if shift clock source
is timer output) as (CMP[15:0] + 1) × 2. When the shift clock source is a pin or trigger input, the compare
register is used to set the number of bits in each word as (CMP[15:0] + 1) ÷ 2.
In 16-bit counter disable mode, the compare value can be used to generate the baud rate divider (if shift
clock source is timer output) as (CMP[15:0] + 1) × 2. When the shift clock source is a pin or trigger input, the
compare register is used to set the number of bits in each word as (CMP[15:0] + 1) ÷ 2.
In 8-bit word counter mode, the lower 8 bits configure the number of bits in each word as (CMP[7:0] + 1) ÷
2. The upper 8 bits configure the number of words to transfer equal to (CMP[15:8] + 1) ÷ 2.
In 8-bit PWM low mode, the lower 8 bits configure the low period of the output to (CMP[7:0] + 1) and the
upper 8 bits configure the high period of the output to (CMP[15:8] + 1).
In 16-bit input capture mode, the compare register is updated with the inverse of the timer counter value
whenever the timer status flag is set. You must read this register only when the timer status flag is set.
69.7.1.36 Shifter Buffer Nibble Byte Swapped (SHIFTBUFNBS0 - SHIFTBUFNBS7)
Offset
For n = 0 to 7:

---

*Page 2292*

Flexible I/O (FlexIO)
Register Offset
SHIFTBUFNBSn 680h + (n × 4h)
Function
Contains Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) content, but it is nibble-swapped within each byte.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SHIFTBUFNBS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SHIFTBUFNBS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Shift Buffer
SHIFTBUFNBS Acts as an alias to Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) , but reads or writes
to this register are nibble-swapped within each byte. Reads return {SHIFTBUF[27:24],
SHIFTBUF[31:28], SHIFTBUF[19:16], SHIFTBUF[23:20], SHIFTBUF[11:8], SHIFTBUF[15:12],
SHIFTBUF[3:0], SHIFTBUF[7:4]}.
69.7.1.37 Shifter Buffer Halfword Swapped (SHIFTBUFHWS0 - SHIFTBUFHWS7)
Offset
For n = 0 to 7:
Register Offset
SHIFTBUFHWSn 700h + (n × 4h)
Function
Contains Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) content, but it is halfword-swapped.

---

*Page 2293*

Flexible I/O (FlexIO)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SHIFTBUFHWS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SHIFTBUFHWS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Shift Buffer
SHIFTBUFHWS Acts as an alias to Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) , but reads or writes to this register are
halfword-swapped. Reads return {SHIFTBUF[15:0], SHIFTBUF[31:16]}.
69.7.1.38 Shifter Buffer Nibble Swapped (SHIFTBUFNIS0 - SHIFTBUFNIS7)
Offset
For n = 0 to 7:
Register Offset
SHIFTBUFNISn 780h + (n × 4h)
Function
Contains Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) content, but it is nibble-swapped.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SHIFTBUFNIS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SHIFTBUFNIS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2294*

Flexible I/O (FlexIO)
Fields
Field Function
31-0 Shift Buffer
SHIFTBUFNIS Acts as an alias to Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) , but reads or writes to this register are
nibble-swapped. Reads return {SHIFTBUF[3:0], SHIFTBUF[7:4], SHIFTBUF[11:8], SHIFTBUF[15:12],
SHIFTBUF[19:16], SHIFTBUF[23:20], SHIFTBUF[27:24], SHIFTBUF[31:28]}.
69.7.1.39 Shifter Buffer Odd Even Swapped (SHIFTBUFOES0 - SHIFTBUFOES7)
Offset
For n = 0 to 7:
Register Offset
SHIFTBUFOESn 800h + (n × 4h)
Function
Contains Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) content, but it has odd and even bits partitioned separately.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SHIFTBUFOES
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SHIFTBUFOES
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Shift Buffer
SHIFTBUFOES Acts as an alias to Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) , but reads or writes to this register have the
odd and even bits partitioned separately. Only 32-bit accesses are supported for this register. Reads
return {SHIFTBUF[31], SHIFTBUF[29],
SHIFTBUF[27],SHIFTBUF[25],SHIFTBUF[23],SHIFTBUF[21],SHIFTBUF[19],SHIFTBUF[17],SHIFTBUF[
15],SHIFTBUF[13],SHIFTBUF[11],SHIFTBUF[9], SHIFTBUF[7], SHIFTBUF[5], SHIFTBUF[3],
SHIFTBUF[1], SHIFTBUF[30], SHIFTBUF[28], SHIFTBUF[26], SHIFTBUF[24], SHIFTBUF[22],
SHIFTBUF[20], SHIFTBUF[18], SHIFTBUF[16], SHIFTBUF[14], SHIFTBUF[12], SHIFTBUF[10],
SHIFTBUF[8], SHIFTBUF[6], SHIFTBUF[4], SHIFTBUF[2], SHIFTBUF[0]}.

---

*Page 2295*

Flexible I/O (FlexIO)
69.7.1.40 Shifter Buffer Even Odd Swapped (SHIFTBUFEOS0 - SHIFTBUFEOS7)
Offset
For n = 0 to 7:
Register Offset
SHIFTBUFEOSn 880h + (n × 4h)
Function
Contains Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) content, with even and odd bits partitioned separately.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SHIFTBUFEOS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SHIFTBUFEOS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Shift Buffer
SHIFTBUFEOS Acts as an alias to Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) , but reads or writes to this register have the
even and odd bits partitioned separately. Only 32-bit accesses are supported for this register. Reads
return {SHIFTBUF[30], SHIFTBUF[28], SHIFTBUF[26], SHIFTBUF[24], SHIFTBUF[22], SHIFTBUF[20],
SHIFTBUF[18], SHIFTBUF[16], SHIFTBUF[14], SHIFTBUF[12], SHIFTBUF[10], SHIFTBUF[8],
SHIFTBUF[6], SHIFTBUF[4], SHIFTBUF[2], SHIFTBUF[0], SHIFTBUF[31], SHIFTBUF[29],
SHIFTBUF[27],SHIFTBUF[25],SHIFTBUF[23],SHIFTBUF[21],SHIFTBUF[19],SHIFTBUF[17],SHIFTBUF[
15],SHIFTBUF[13],SHIFTBUF[11],SHIFTBUF[9], SHIFTBUF[7], SHIFTBUF[5], SHIFTBUF[3],
SHIFTBUF[1]}.
69.7.1.41 Shifter Buffer Halfword Byte Swapped (SHIFTBUFHBS0 - SHIFTBUFHBS7)
Offset
For n = 0 to 7:
Register Offset
SHIFTBUFHBSn 900h + (n × 4h)

---

*Page 2296*

Flexible I/O (FlexIO)
Function
Contains Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) content, but it is halfword byte-swapped.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SHIFTBUFHBS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SHIFTBUFHBS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Shift Buffer
SHIFTBUFHBS Acts as an alias to Shifter Buffer (SHIFTBUF0 - SHIFTBUF7) , but reads or writes to this register
are halfword byte-swapped. Reads return {SHIFTBUF[23:16], SHIFTBUF[31:24], SHIFTBUF[7:0],
SHIFTBUF[15:8]}.

#### 69.8 Glossary

Baud rate Number of bits that LPUART transmits or receives per second
Break character Break character is generated when the transmitter is holding the data line at the space level for at least 1
character time
Idle character Idle character is generated when the transmitter is holding the data line at logic 1 for at least 1 character
time
PWM Pulse width modulation

---

*Page 2297*

