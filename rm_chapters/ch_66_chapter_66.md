<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 66 -->

# Chapter 66

# Real Time Clock (RTC)

#### 66.1 Chip-specific RTC information

#### 66.1.1 RTC instances and configuration

The chip contains one instance of RTC (Real Time Clock) timer and API (Autonomous Periodic Interrupt) timer, where both can
perform 32-bit comparisons.
The RTC is present in always ON domain, hence available in RUN mode as well as in STANDBY mode. Both RTC and API timers
can generate interrupts as well as wakeup from low power modes.
RTC supports a resolution of 1μs with high speed internal oscillator clock (1/48MHz (FIRC) * 48 (RTC counter) = 1μs)
This chip supports seamless RTC operation across functional reset with the clock sources SIRC and SXOSC.
NOTE
SXOSC is not present in MCXE315/MCXE316, thus RTC will run from SIRC clock.
API has the capability to change the Timer compare value (APIVAL) independently without stopping the timer.
API can also be used in conjunction with the Comparator module. In STANDBY mode configuration, the API is configured to
generate a START / NEXT type signal to inform the Comparator module that a 'compare' must be carried out.
Table 360. RTC instances
Module MCXE315/MCXE316/MCXE317/MCXE31B
RTC Yes

#### 66.2 Overview

The Real-Time Clock (RTC) is a free-running counter used for time keeping applications. The RTC can be configured to generate
an interrupt at a pre-defined interval independent of the mode of operation (run mode or low power mode). If in a low power mode,
the RTC interval is reached, the RTC first generates a wakeup and then asserts the interrupt request . The RTC also supports an
API function used to generate a periodic wakeup request to exit a low-power mode or an interrupt request.

#### 66.2.1 Block diagram

The following figure shows clock gating for RTC clocks.

---

*Page 2089*

Real Time Clock (RTC)
(cnten & clksel== 2’b11) (cnten & clksel== 2’b11)
en en
Clock source 3
C.G. C.G.
CELL CELL
(cnten & clksel== 2’b10) (cnten & clksel== 2’b10)

#### 3 3

en en
Clock source 2
C.G. C.G.
CELL CELL

#### 2 2

0 0
(cnten & clksel== 2’b01) (cnten & clksel== 2’b01)
0 0

#### en en 1 1

1 1 rtc_clock
div 512
Clock source 1 C.G. C.G.
div 512 32 bit counter
CELL CELL
C.G. C.G.
en en 1 1 C.G. C.G. div 32 div 32 reset
CELL CELL
CELL CELL

#### 0 0 en en

div512en div512en
(cnten & clksel== 2’b00) (cnten & clksel== 2’b00) CNTEN
div32en div32en
en en
Clock source 0
C.G. C.G.
CELL CELL
CLKSEL[1:0] CLKSEL[1:0]
Figure 357. Clock gating for RTC clocks
The following figure shows the block diagram of RTC.
RTCCNT
sync APIVAL
32-0
+
APIEN offset reg load
or reset
CNTEN
32-0
== API wakeup
sync
APIF
rtc_clock 32 bit counter API
APIIE
32-0 interrupt
== RTCVAL
RTC wakeup
RTC Rollover wakeup
ROVREN sync
sync RTCF RTC cnt_or_rlovr
RTCIE
RTC interrupt
ROVRF
RTCIE
Figure 358. Block diagram

---

*Page 2090*

Real Time Clock (RTC)

#### 66.2.2 Features

RTC features include:
• 32-bit counter
• Selectable counter clock sources (IRCs and OSCs)
— Clock source 0
— Clock source 1
— Clock source 2
— Clock source 3
NOTE
See the chip-specific RTC information and clocking chapter for mapping of clock sources.
• Optional 512 prescaler and optional 32 prescaler to run the 32 bit counter.
• RTC interrupt with interrupt enable.
• Counter runs in all modes of operation.
• RTC counter is reset when the counter is disabled by software and by reset to RTC block.
• Autonomous periodic interrupt support includes:
— 32-bit compare value to support range of wakeup intervals/ interrupts
— API logic has a separate enable to support changing compare value while RTC is running
— API interrupt with interrupt enable
— Operates in all modes
— API compare value can be modified while RTC is running
• Optional interrupt for RTC match, API match, and RTC rollover.

#### 66.3 Functional description

#### 66.3.1 RTC

The RTC consists of a 32-bit free running counter enabled with the CNTEN bit ( CNTEN , when negated, asynchronously resets the
counter and synchronously enables the counter when enabled). After disabling CNTEN , RTCVAL , APIVAL , needs to be written
again for desired functionality. The value of the counter may be read via the RTC Count (RTCCNT) register. Note that because
of clock synchronization, the RTC Count (RTCCNT) value may represent a previous counter value. The difference between the
counter and the read value depends on the ratio of counter clock and bus clock. Maximum possible difference between the two
is 6 count values.
The clock source to the counter is selected with the CLKSEL field, which gives four options for clocking the RTC/API. The four
clock sources are assumed to be on these:Clock source 0, Clock source 1, Clock source 2, and Clock source 3. The output of the
clock mux can be optionally divided by a combination of 512 and 32 to give various count periods for different clock sources. Note
that the CNTEN bit should be disabled when the RTC/API clock source is switched.
When the RTC Count (RTCCNT) counter value for counter bits 31-0 match the 32-bit value in the RTCVAL field, then the RTCF
interrupt flag bit is set (after proper clock synchronization). If the RTCIE interrupt enable bit is set, then the RTC interrupt request is
generated. RTC Compare value register (RTCVAL) register can be written only when INV_RTC bit is clear. Initially INV_RTC =0,
and hence RTC Compare value register (RTCVAL) can be written once and hence INV_RTC gets set. This bit can now be cleared
only by enabling the RTC counter. After the counter is enabled, RTC Compare value register (RTCVAL) can be written anytime,
until RTC is disabled again. RTC Compare value register (RTCVAL) is first synchronized to the RTC clock domain, therefore, if
RTC Compare value register (RTCVAL) is updated at the point where a counter match is due to happen in the next 2-3 RTC clocks
because of previous RTC Compare value register (RTCVAL) , the RTCF flag is set. However, if the RTC Compare value register

---

*Page 2091*

Real Time Clock (RTC)
(RTCVAL) is updated at the point where no counter match is due as per the previous RTC Compare value register (RTCVAL) ,
the RTCF flag is set when the counter matches the new RTC Compare value register (RTCVAL) . If there is a match when in the
low-power mode, then the RTC first generates a wakeup request to force a wakeup to run mode,and then the RTCF flag is set.
If RTC Compare value register (RTCVAL) is updated just after counter match, new RTC Compare value register (RTCVAL) value
should not be within next 6 RTC counter values.
A rollover wakeup and/or interrupt can be generated when the RTC transitions from a count of 0xFFFF_FFFF to 0x0000_0000.
The rollover flag is enabled by setting the ROVREN bit. If RTCIE is enabled, an interrupt request is generated for an RTC counter
rollover. If system is in low power mode, RTC counter rollover with this bit causes a wakeup from the low-power mode.
Both rollover wakeup and RTC wakeup gets asynchronously de-asserted with disabling of CNTEN .
All the flags and counter values are synchronized with the Bus clock. It is assumed that the Bus clock and RTC clock selected
through CLKSEL follows the below relation:
Bus clock >= (1.5 * RTC clock )/(div_factor)
• if both DIV32EN and DIV512EN bits are disabled, div_factor = 1
• if DIV32EN=1 and DIV512EN=0, div_factor = 32
• if DIV32EN=0 and DIV512EN=1, div_factor = 512
• if both DIV32EN and DIV512EN bits are enabled, div_factor = 512*32 = 16384
In case, RTC wakeup's are used as a wakeup source, bus clock (system clock or register interface clock of RTC) should be
disabled (to save power in standby mode) after enabling the required wakeup and ensuring sufficient time gap (3-6 bus clock or
RTC clock (rtc_clock) cycles, whichever is slower) between bus clock disabling and wakeup event. To ensure writing the register
to enable the wakeup, the bus clock must be active, hence it should not be disabled before enabling wakeup. Correct operation
is not guaranteed if the specification is not followed.

#### 66.3.2 API functional description

Setting APIEN bit enables the autonomous interrupt function. The 32-bit APIVAL field selects the time interval for triggering an
interrupt and/or wakeup event. Because the RTC is a free-running counter, the APIVAL is added to the current count to calculate
an offset. When the counter reaches the offset count, an interrupt and/or wakeup request is generated. Then the offset value is
recalculated and again re-triggers a new request when the new value is reached. API function is enabled only when CNTEN and
APIEN bits are asserted and APIVAL is non-zero. Also APIVAL can be updated anytime. After APIVAL is updated, the first API
interrupt is generated according to the previous value. From the second interrupt onwards, the API interrupt is generated with
the new APIVAL . When a compare is reached, the APIF interrupt flag bit is set (after proper clock synchronization). If the APIIE
interrupt enable bit is set, then the API interrupt request is generated. If there is a match while being in the low-power mode, then
the API first generates a wakeup request to force a wakeup into normal operation, and then the APIF flag is set.
When the CNTEN is de-asserted, the API function is reset , though wakeup API is not asynchronously de-asserted with CNTEN .
If APIEN is disabled when counter matches the offset APIF can be missed.

#### 66.3.3 Modes of operation

66.3.3.1 Functional mode
There are two functional modes of operation for RTC, normal operation and low-power mode. In normal operation, a ll RTC
registers can be read or written . The RTC/API and associated interrupts are optionally enabled. In low-power mode, the bus
interface is disabled. The RTC/API is enabled (if enabled prior to entry into low-power mode).
66.3.3.2 Debug mode
On entering into the debug mode, the RTC counter freezes on the last valid count if the FRZEN is set. On exit from debug mode,
counter continues from the frozen value.

---

*Page 2092*

Real Time Clock (RTC)

#### 66.4 Initialization

Recommended programming flow as follow
1. Program RTCVAL register with the value greater than 4 if RTCF related functionality is required.
2. Program APIVAL register with the value greater than 4 if API functionality is required.
3. Program all fields (as required) of RTC Control (RTCC) register with CNTEN =1. After setting CNTEN field, counter
starts running and RTC/API functionality will be active as per the configurations.
4. If RTCVAL or APIVAL needs to be updated during run, check corresponding INV_RTC or INV_API bit is cleared before
writing and do meet all the restrictions of the corresponding register.
5. If CLKSEL or DIV32EN or DIV512EN needs to be updated, first update RTC Control (RTCC) register with CNTEN =0
(when all INV_RTC or INV_API are 0) keeping other field values same. Wait for minimum 3 RTC clock cycles so that
CNTEN will be synchronized to RTC clock domain. Then write the RTC Control (RTCC) register with new configuration
required with CNTEN =1.

#### 66.5 RTC register descriptions

The RTC registers are listed in this section.
NOTE
Address offset - 0x18h should not be accessed by application as corresponding feature/s are not available.
Therefore, transfer error will not be generated at this offset.
NOTE
XFR error will be generated when RTCSUPV is accessed in user mode, any other register is accessed in user
mode when SUPV bit is set, write attempt is made for RTCCNT register and any register accessed out of
address range.

#### 66.5.1 RTC memory map

RTC base address: 4028_8000h
Offset Register Access Reset value
Width
(In bits)
0h RTC Supervisor control register (RTCSUPV) 32 RW 8000_0000h
4h RTC Control (RTCC) 32 RW 0000_0000h
8h RTC Status register (RTCS) 32 RW 0000_0000h
Ch RTC Count (RTCCNT) 32 R 0000_0000h
10h API Compare value register (APIVAL) 32 RW 0000_0000h
14h RTC Compare value register (RTCVAL) 32 RW 0000_0000h

#### 66.5.2 RTC Supervisor control register (RTCSUPV)

Offset
Register Offset
RTCSUPV 0h

---

*Page 2093*

Real Time Clock (RTC)
Function
The RTCSUPV register contains the SUPV bit that determines whether other registers are accessible in supervisor mode or
user mode.
NOTE
You can access this register only in Supervisor mode, and you must write a value only to the SUPV field of
the register.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
SUPV
W
Reset 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
RTC Supervisor Bit
31
0b - All registers are accessible in both user as well as supervisor mode
SUPV
1b - All other registers are accessible in the supervisor mode only
Reserved
30-0
—

#### 66.5.3 RTC Control (RTCC)

Offset
Register Offset
RTCC 4h
Function
RTC Control register

---

*Page 2094*

Real Time Clock (RTC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
CNTE FRZE ROVR
RTCIE
N N EN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
DIV51 DIV32 TRIG_
APIEN APIIE CLKSEL
2EN EN EN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Counter Enable
CNTEN The CNTEN bit enables the RTC counter. Setting CNTEN bit to 0b has the effect of asynchronously resetting
(synchronous reset negation) all the RTC and API logic. This allows RTC configuration and clock source
selection to be updated without causing synchronization issues.
NOTE
CNTEN should be disabled when INV_RTC , INV_API are cleared.
0b - Counter disabled
1b - Counter enabled
30 RTC Interrupt Enable
RTCIE The RTCIE bit enables interrupts requests to the system if RTCF is asserted.
0b - RTC interrupts disabled
1b - RTC interrupts enabled
29 Freeze Enable Bit
FRZEN The counter freezes on entering the debug mode on the last valid count value if the FRZEN bit is set. After
passing of the debug mode counter starts from the frozen value. This bit should not be changed when debug
mode is enabled.
0b - Counter does not freeze in debug mode
1b - Counter freezes in debug mode
28 Counter Roll Over wakeup/Interrupt Enable
ROVREN The ROVREN bit enables wakeup and interrupt requests when the RTC has rolled over from 0xFFFF_FFFF
to 0x0000_0000. The RTCIE bit must also be set in order to generate an interrupt from a counter rollover.
0b - RTC rollover wakeup/interrupt disabled
1b - RTC rollover wakeup/interrupt enabled
Table continues on the next page...

---

*Page 2095*

Real Time Clock (RTC)
Table continued from the previous page...
Field Function
Reserved
27-16
—
15 Autonomous Periodic Interrupt Enable
APIEN The APIEN bit enables the autonomous periodic interrupt function. Setting this bit to 0b, asynchronously
disables API wakeup output of RTC as well.
0b - API disabled
1b - API enabled
14 API Interrupt Enable
APIIE The APIIE bit enables interrupts requests to the system if APIF is asserted.
0b - API interrupts disabled
1b - API interrupts enabled
13-12 Clock select
CLKSEL The CLKSEL[1:0] bits select the clock source for the RTC. CLKSEL may only be updated when CNTEN is
0. The user should ensure that oscillator is enabled before selecting it as a clock source for RTC.
NOTE
See the RTC-clocking and resolution information in the clocking chapter elsewhere in
this document.
00b - Clock source 0
01b - Clock source 1
10b - Clock source 2
11b - Clock source 3
11 Divide by 512 enable
DIV512EN The DIV512EN bit enables the 512 clock divider. DIV512EN may only be updated when CNTEN is 0.
0b - Divide by 512 is disabled
1b - Divide by 512 is enabled
10 Divide by 32 enable
DIV32EN The DIV32EN bit enables the 32 clock divider. DIV32EN may only be updated when CNTEN is 0.
0b - Divide by 32 is disabled
1b - Divide by 32 is enabled
Reserved
9-1
—
0 Trigger enable for Analog Comparator
Table continues on the next page...

---

*Page 2096*

Real Time Clock (RTC)
Table continued from the previous page...
Field Function
This bit (TRIG_EN) when set will de-assert the wakeup_api on the next RTC clock (required for CMP
TRIG_EN
when bus clock is disabled).
NOTE
If API wakeup is asserted with TRIG_EN set, and bus clock enabled, setting of RTCS[APIF]
may be missed.

#### 66.5.4 RTC Status register (RTCS)

Offset
Register Offset
RTCS 8h
Function
NOTE
W1C has priority over setting of the RTCF, APIF and ROVRF bits, in case both clearing and setting occurs at the
same time.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
INV_ INV_ Reserv
R 0 RTCF 0
RTC API ed
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
ROVR
R 0 APIF 0 0
F
W W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-30
—
29 RTC Interrupt Flag
Table continues on the next page...

---

*Page 2097*

Real Time Clock (RTC)
Table continued from the previous page...
Field Function
RTCF The RTCF bit indicates that the RTC counter has reached the counter value matching RTC Compare value
register (RTCVAL) . RTCF is cleared by writing a 1 to RTCF. Writing a 0 to RTCF has no effect.
0b - RTC counter is not equal to RTCVAL
1b - RTC counter matches RTCVAL
Reserved
28-19
—
18 Invalid RTC write
INV_RTC This bit returns value 1 after a value is written to the RTCVAL register and the synchronization process
is in progress. During this synchronization period, any attempt to write to the RTCVAL register again is
ignored. Synchronization will complete only when CNTEN is set.
17 Invalid APIVAL write
INV_API This bit returns value 1 after a value is written to the APIVAL register and the synchronization process
is in progress. During this synchronization period, any attempt to write to the APIVAL register again is
ignored. Synchronization will complete only when CNTEN is set.
Reserved
16
—
Reserved
15-14
—
13 API Interrupt Flag
APIF The APIF bit indicates that the RTC counter has reached the counter value matching API offset value. APIF
is cleared by writing a 1 to APIF. Writing a 0 to APIF has no effect.
0b - Counter is not equal to API offset value
1b - Counter matches the API offset value
Reserved
12-11
—
10 Counter Roll Over Interrupt Flag
ROVRF The ROVRF bit indicates that the RTC has rolled over from 0xFFFF_FFFF to 0x0000_0000. ROVRF is
cleared by writing a 1 to ROVRF.
0b - RTC has not rolled over
1b - RTC has rolled over
Reserved
9-0
—

---

*Page 2098*

Real Time Clock (RTC)

#### 66.5.5 RTC Count (RTCCNT)

Offset
Register Offset
RTCCNT Ch
Function
RTC Counter register
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RTCCNT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RTCCNT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 RTC Counter Value
RTCCNT Because of clock synchronization, the RTCCNT value may represent a previous counter value.

#### 66.5.6 API Compare value register (APIVAL)

Offset
Register Offset
APIVAL 10h
Function
The APIVAL offset bits are compared to the RTC counter bits and if a match occurs, an interrupt /wakeup request is asserted.

---

*Page 2099*

Real Time Clock (RTC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
APIVAL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
APIVAL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 API Compare Value
APIVAL APIVAL bits are added to the current count to calculate an offset. The APIVAL offset bits are compared to
the RTC counter bits and if a match occurs, an interrupt /wakeup request is asserted.
NOTE
API functionality is active only when APIVAL is non zero. The first API interrupt takes two
more cycles because of synchronization of APIVAL to RTC clock. After that, interrupts are
periodic in nature and it takes APIVAL+1 cycles. The Minimum supported value of APIVAL
is 4. This is because of synchronization.

#### 66.5.7 RTC Compare value register (RTCVAL)

Offset
Register Offset
RTCVAL 14h
Function
The RTCVAL bits are compared to the RTC counter bits and if a match occurs, RTCF is set. The minimum value of RTCVAL should
be 4.

---

*Page 2100*

Real Time Clock (RTC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
RTCVAL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
RTCVAL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 RTC Compare Value
RTCVAL The RTCVAL bits are compared to the RTC counter bits and if a match occurs, RTCF is set.

#### 66.6 Glossary

API Autonomous Periodic Interrupt
CMP Comparator
IRCs Internal RC Oscillator
OSCs Oscillator

---

*Page 2101*

