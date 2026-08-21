<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 60 -->

# Chapter 60

# Enhanced Modular IO Subsystem (eMIOS)

#### 60.1 Chip-specific eMIOS information

#### 60.1.1 eMIOS configuration

This chip has up to 3 instances of eMIOS that are each configured as shown in the following tables.
Table 325. eMIOS instances
Instance MCXE31B MCXE315/MCXE316/ MCXE317
eMIOS_0 Yes Yes
eMIOS_1 Yes Yes
eMIOS_2 Yes No
Table 326. eMIOS configuration
Parameter eMIOS_0 eMIOS_1 eMIOS_2
Timer width 16 bits 16 bits 16 bits
Number of channels 24 24 24
Channels configuration See EMIOS channel configuration figure below
Local Channel prescaler width 4 4 4
Number of global 2 2 2
counter buses
Number of global prescaler 1 1 1
Global channel 8 8 8
prescaler width
NOTE
eMIOS is clocked by CORE_CLK.

#### 60.1.2 Channel types

The eMIOS contain 4 different types of channel, which are listed below.
Table 327. eMIOS channel types
Mode Description Name Ch TypeX Ch TypeY Ch TypeG Ch TypeH
General Purpose GPIO X X X X
Input / Output
Single Action SAIC X X X X
Input Capture
Single Action SAOC X X X X
Output Compare
Table continues on the next page...

---

*Page 1851*

Enhanced Modular IO Subsystem (eMIOS)
Table 327. eMIOS channel types (continued)
Mode Description Name Ch TypeX Ch TypeY Ch TypeG Ch TypeH
Modulus Counter MC X
Modulus Counter MCB X X
Buffered
(Up / Down)
Input Pulse Width IPWM X X
Measurement
Input Period IPM X X
Measurement
Double Action DAOC X X
Output Compare
Output Pulse Width OPWFMB X X
and Frequency
Modulation
Buffered
Center aligned OPWMCB X
Output PWM
Buffered with
dead time
Output Pulse Width OPWMB X X X X
Modulation
Buffered
Output Pulse Width OPWMT X X X X
Modulation Trigger
Pulse PEC X
Edge Counting
NOTE
ChType X and G has internal counters.
Table 328. eMIOS channel configuration
Channel number eMIOS0 eMIOS1 eMIOS2
CH0 X X X
CH8 X X X
CH16 X X X
CH22 X X X
CH23 X X X
CH1 G H H
CH2 G H H
CH3 G H H
Table continues on the next page...

---

*Page 1852*

Enhanced Modular IO Subsystem (eMIOS)
Table 328. eMIOS channel configuration (continued)
CH4 G H H
CH5 G H H
CH6 G H H
CH7 G H H
CH9 H H H
CH10 H H H
CH11 H H H
CH12 H H H
CH13 H H H
CH14 H H H
CH15 H H H
CH17 Y Y Y
CH18 Y Y Y
CH19 Y Y Y
CH20 Y Y Y
CH21 Y Y Y

#### 60.1.3 Channel configuration

The eMIOS channel configuration with respect to Type and Counter_bus is shown below.

---

*Page 1853*

Enhanced Modular IO Subsystem (eMIOS)
eMIOS_0 eMIOS_1 eMIOS_2
Ch0 Ch0 Ch0
Ch1 Ch1 Ch1
Ch2 Ch2 Ch2
Ch3 Ch3 Ch3
Ch4 Ch4 Ch4
Ch5 Ch5 Ch5
Counter_bus_B Counter_bus_B Counter_bus_B
Ch6 Ch6 Ch6
Ch7 Ch7 Ch7
Ch8 Ch8 Ch8
Ch9 Ch9 Ch9
Ch10 Ch10 Ch10
Ch11 Ch11 Ch11
Ch12 Ch12 Ch12
Ch13 Ch13 Ch13
Counter_bus_A Counter_bus_C Counter_bus_A Counter_bus_C Counter_bus_A Counter_bus_C
Ch14 Ch14 Ch14
Ch15 Ch15 Ch15
Ch16 Ch16 Ch16
Ch17 Ch17 Ch17
Ch18 Ch18 Ch18
Ch19 Ch19 Ch19
Ch20 Ch20 Ch20
Ch21 Ch21 Ch21
Counter_bus_F Counter_bus_D Counter_bus_F Counter_bus_D Counter_bus_F Counter_bus_D
Ch22 Ch22 Ch22
Ch23 Ch23 Ch23
Chan Type_X Chan Type_Y Chan Type_G Chan Type_H
Figure 293. EMIOS channel types

#### 60.1.4 eMIOS disabling

eMIOS can disable it's output using it's disable inputs. Disable inputs[3:0] are driven from the flag out bits[11:8].
Disable inputs
eMIOS0 Output disable input[3:0] trgmux_output[31:28]
eMIOS1 Output disable input[3:0] trgmux_output[31:28]
eMIOS2 Output disable input[3:0] trgmux_output[31:28]
For details, see the TRGMUX connectivity file attached to this document.

#### 60.1.5 BCTU Interface

Each eMIOS channel, with the exception of the main counterbus channel (ch[23]) is capable of triggering the BCTU. See BCTU
chapter for mapping. BCTU allows triggering from as many eMIOS modes as possible.

---

*Page 1854*

Enhanced Modular IO Subsystem (eMIOS)

#### 60.2 Introduction

eMIOS provides independent channels, UC s, that you configure to generate or measure time events for different functions in
different chip applications.
eMIOS distributes these channels across a number of global and local counter buses. Each local bus is dedicated to a group of
eight contiguous channels. Each channel can generate its own timebase, and each counter bus has its timebase provided by a
dedicated channel. For a list of counter buses with their assigned channels and timebase sources, see Counter buses, channels,
and timebase sources .

#### 60.3 Block diagram

eMIOS
Bus D
IP
BIU All channels
interface
Channel 23 EMIOS[23]
Channel 22 EMIOS[22]
Global
GTBE_OUT Channel 16 EMIOS[16]
registers
Bus C
Global counter bus A Channel 15 EMIOS[15]
Output disable bus[3:0]
GTBE_IN All channels
Second global counter bus F
Channel 8 EMIOS[8]
Bus B
System Global
All channels
clock prescaler
Channel 7 EMIOS[7]
Channel 0 EMIOS[0]
Output disable inputs[3:0]
Figure 294. Block diagram

#### 60.4 Features

• 24 UCs distributed across local counter buses as shown in Counter buses, channels, and timebase sources
• Global counter bus A driven by UC23
• Global counter bus F driven by UC22
• Synchronized timebases shared through the counter buses

---

*Page 1855*

Enhanced Modular IO Subsystem (eMIOS)
• Dedicated timebase for each channel, distinct from the counter buses
• Global clock prescaler ( GCP )
• One CP per channel
• Dedicated set of control and status registers for each UC
• 16-bit–wide data registers
• Shadow flag register ( GFLAG ) to access all channel flags with one read access
• Ability to freeze the UC state for debug purposes
• Motor control capability

#### 60.5 Functional description

#### 60.5.1 Reset

eMIOS resets asynchronously. Reset affects all registers.
On reset, UCs enter GPIO input mode.

#### 60.5.2 Counter buses, channels, and timebase sources

Table 329. Counter buses, channels, and timebase sources
Counter bus Channels Timebase channel
Global bus A All UCs 23
Local bus B 0
• UC0
• UC1
• UC2
• UC3
• UC4
• UC5
• UC6
• UC7
Local bus C 8
• UC8
• UC9
• UC10
• UC11
• UC12
• UC13
• UC14
• UC15
Local bus D 16
• UC16
Table continues on the next page...

---

*Page 1856*

Enhanced Modular IO Subsystem (eMIOS)
Table 329. Counter buses, channels, and timebase sources (continued)
Counter bus Channels Timebase channel
• UC17
• UC18
• UC19
• UC10
• UC21
• UC22
• UC23
Global bus F All UCs 22

#### 60.5.3 Unified channels (UC)

60.5.3.1 Overview
Each UC contains the following:
• Two double-buffered data registers, A n and B n , that allow up to two events—input capture, output compare, or both—to
occur before software intervention is needed
• Two comparators, A and B, that indicate when the selected counter bus is equal to the value in A n and B n
• An internal counter ( CNT n [C] ) that runs in all modes except GPIO.
You can use this counter as a local timebase or to count input events. You can use it as a timebase if CNT n [C] is not used
in the current mode.
• An output flip-flop that holds the logic level to be applied to the output pin
• A status register, UC Status n (S0 - S23) , that flags input capture and match events, indicates flag overruns and
overflows, and shows the input and output pin states
• A control register, UC Control n (C0 - C23) , that controls UC operation
You control the following operational characteristics for each UC:
• The logic ( C n [MODE] ) that specifies the behavior of the UC (see UC modes )
• The counter bus ( C n [BSL] ) that provides the timebase for all timing functions
• The CP ( C n [UCPRE] and C2_ n [UCEXTPRE] )
• The minimum input pulse width ( C n [IF] ) for the input filter that determines valid pin transitions
• Which input edges to detect ( C n [EDSEL] )
• Which of four output disable input signals ( C n [ODISSL] ) to use for disabling outputs
• For all output modes except GPIO, whether to disable the output flip-flop ( C n [ODIS] )
UC block diagram illustrates the basic UC architecture.
For a list of counter buses and their assigned channels and timebase sources, see Counter buses, channels, and
timebase sources .

---

*Page 1857*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.2 UC block diagram
UC
Clock Programmable
prescaler filter
UC datapath
UC control Comparators A and B
CPU Mode logic
DMA
Global counter bus A Second global counter bus F Local counter B, C, or D Internal counter
Counter bus
Figure 295. UC block diagram
60.5.3.3 Buffered modes
To provide smooth waveform generation even when A n [A] and B n [B] are changing, the following modes double buffer the A n and
B n registers:
• Modulus Counter Buffered (MCB) mode
• Output Pulse Width and Frequency Modulation Buffered (OPWFMB) mode
• Output PWM Buffered (OPWMB) mode
These modes appear in separate sections because there are several basic differences in UC operation between them and their
corresponding nonbuffered modes.
60.5.3.4 UC control and datapath
As illustrated in UC control and datapath diagram , the UC contains control and datapath sub-blocks.
The control sub-block generates signals to control the multiplexers in the datapath sub-block. eMIOS implements each UC
mode in dedicated logic independent from the other modes. The UC modes share a set of registers allowing the storing of
sequential events.
The datapath block contains the channel A and B registers, the internal timebase, and the comparators. Multiplexers receive
inputs from the control sub-block to configure the datapath for the specific channel modes. This configuration consists of selecting
the input of comparators and data for the register inputs. The outputs of the A and B comparators connect to the control block.

---

*Page 1858*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.5 UC control and datapath diagram
Input
Input
filter
MODE
register
Mode 0 Mode 1 Mode n
logic logic logic
MODE
decoder
Control signals
General
purpose
registers
uc_ctrl
Global counter bus[A]
BSL[1]+logic
2nd global counter bus[F]
Local counter bus[B/C/D]
==
lnternal
BSL[1:0 or 0:1]
counter
A Comparator
AS2
CNT AS1
BSL[1]+logic
BS1
BS2
B Comparator
==
BSL[1]+logic
uc_datapath
Figure 296. UC control and datapath diagram
60.5.3.6 UC modes
You can configure UCs to operate in the following modes:
• General-Purpose Input and Output (GPIO) mode
• Single Action Input Capture (SAIC) mode
• Single Action Output Capture (SAOC) mode
• Input Pulse Width Measurement (IPWM) mode
• Input Period Measurement (IPM) Mode

---

*Page 1859*

Enhanced Modular IO Subsystem (eMIOS)
• Double Action Output Compare (DAOC) mode
• Pulse Edge Counting (PEC) mode
• Modulus Counter (MC) mode
• Modulus Counter Buffered (MCB) mode
• Output Pulse Width and Frequency Modulation Buffered (OPWFMB) mode
• Center Aligned Output PWM with Dead Time Insertion Buffered (OPWMCB) mode
• Output PWM Buffered (OPWMB) mode
• Output PWM with Trigger (OPWMT) mode
Each channel supports a specific subset of these modes. See the chip-configuration information to see which modes each
channel supports.
60.5.3.7 General-Purpose Input and Output (GPIO) mode
60.5.3.7.1 Overview
This mode disables all UC input-capture and output-compare functions, and resets and disables the internal counter ( C n [C] ). All
control fields remain accessible.
To change the UC from one operation mode to another, you must first change to GPIO mode and then change to the desired final
mode. To prepare the UC for a new operation mode, you must configure AS1, BS1, AS2, and BS2 before exiting GPIO mode and
entering the desired final mode (see AS1, AS2, BS1, and BS2 shadow registers ). Specifically:
• Writing to A n [A] stores the same value in AS1 and AS2.
• Writing to B n [B] stores the same value in BS1 and BS2.
• Writing to ALTA n [ALTA] stores a value only in AS2.
As its name implies, GPIO mode supports both input and output submodes. See Differences for input and output submodes .
60.5.3.7.2 Differences for input and output submodes
Table 330. Differences for input and output submodes
Submode To enter submode, write this Characteristics
value to C n [MODE]
Input 0
• You control the generation of input-capture flags with C n [EDPOL]
and C n [EDSEL] .
• You can determine the input pin status by reading S n [UCIN] .
Output 1
• The UC performs as a single output port pin.
• The value of C n [EDPOL] permanently drives the output flip-flop.
60.5.3.8 Single Action Input Capture (SAIC) mode
60.5.3.8.1 Overview
In this mode, when a triggering event occurs on the input pin, eMIOS:
• Captures the selected timebase into AS2
• Changes S n [FLAG] to 1 to indicate that an input capture has occurred

---

*Page 1860*

Enhanced Modular IO Subsystem (eMIOS)
A n [A] returns the value of AS2. As soon as the UC enters SAIC mode after exiting GPIO mode, the channel is ready to
capture events.
eMIOS captures the events as soon as they occur. Therefore, reading A n [A] always returns the value of the latest captured event.
Subsequent capture events occur regardless of whether you read the previous event from A n [A] . Each new capture event sets
the input capture flag (writes 1 to S n [FLAG] ).
The input capture is triggered by an edge transition on the input pin as configured by C n [EDPOL] and C n [EDSEL] .
SAIC with rising edge triggering and SAIC with both edges triggering show how to use the UC for input capture.
60.5.3.8.2 SAIC submodes and MODE field values
Table 331. SAIC submodes and MODE field values
Capture behavior C n [MODE] (binary)
The UC only captures the timestamp. 000_0010
100_0010
• The UC captures a timestamp.
• AS2 bit 31 indicates the input signal level.
60.5.3.8.3 Effect of C n [EDPOL] on edge capturing
Table 332. Effect of C n [EDPOL] on edge capturing
C n [EDPOL] Input signal transition Resulting value of AS2 bit 31
0 0→1 0
1→0 1
1 0→1 1
1→0 0
60.5.3.8.4 SAIC with rising edge triggering
C n [EDSEL] = 0
C n [EDPOL] = 1 Edge detect Edge detect Edge detect
Input signal (after input filter)
Selected counter bus 500h 1000h 1100h 1250h 1525h 16A0h
Flag output and S n [FLAG]
AS2 (captured; read in A n ) 0xxxxxx 1000h 1250h 16A0h
Figure 297. SAIC with rising edge triggering

---

*Page 1861*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.8.5 SAIC with both edges triggering
C n [EDSEL] = 1
Edge detect Edge detect Edge detect
C n [EDPOL] = x
Input signal (after input filter)
Selected counter bus 1000h 1001h 1102h 1103h 1104h 1105h 1106h 1107h 1108h
Flag set event
Flag output and S n [FLAG]
Flag clear
AS2 (captured; read from A n ) 0xxxxxx 1000h 1103h 1108h
Figure 298. SAIC with both edges triggering
60.5.3.9 Single Action Output Capture (SAOC) mode
60.5.3.9.1 Overview
In this mode, eMIOS loads a match value into AS2 and then immediately transfers it to AS1 for comparison with the selected
timebase. When an output-compare match occurs, eMIOS:
• Either toggles the output flip-flop or transfers C n [EDPOL] to the output flip-flop, as determined by C n [EDSEL] .
• Sets the input capture flag (changes S n [FLAG] to 1).
Along with the match, S n [FLAG] becomes 1 to indicate that the output-compare match has occurred. Writing to A n [A] stores the
value in AS2, and reading A n [A] returns the AS1 value.
The channel internal counter in SAOC mode is free-running. It starts counting as soon as the UC enters SAOC mode.
You can force an output-compare match by writing 1 to C n [FORCMA] . A forced output-compare match does not set the input
capture flag ( S n [FLAG] ).
When the UC enters SAOC mode after exiting GPIO mode, the output flip-flop value becomes the complement of C n [EDPOL] .
You select the internal or external counter bus with C n [BSL] .
SAOC with C n [EDPOL] transferred to the output flip-flop shows how to perform a single output compare and transfer C n [EDPOL]
to the output flip-flop.
SAOC toggling the output flip-flop shows how to perform a single output compare and toggle the output flip-flop at each match.
60.5.3.9.2 Preload the desired match value
eMIOS enables matches immediately after the UC enters SAOC mode. Therefore, you must write the desired match value
to AS1 before the UC enters SAOC mode. You can update AS1 at any time. This modifies the match value reflected in the
channel-generated output signal. Subsequent capture events occur regardless of whether you write to A n [A] again. Each new
capture event sets the input capture flag ( S n [FLAG] ). See SAOC with flag behavior .

---

*Page 1862*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.9.3 SAOC with C n [EDPOL] transferred to the output flip-flop
C n [EDSEL] = 0
Update AS1 AS1 match AS1 match AS1 match
C n [EDPOL] = 1
Output flip-flop
Selected counter bus 500h 1000h 1100h 1000h 1100h 1000h
Flag output and S n [FLAG]
AS1 (read from A n ) 0xxxxxx 1000h 1000h 1000h 1000h
AS2 = AS1 according to the OUDIS[OU n ] field
Figure 299. SAOC with C n [EDPOL] transferred to the output flip-flop
60.5.3.9.4 SAOC toggling the output flip-flop
C n [EDSEL] = 1
Update AS1 AS1 match AS1 match AS1 match
C n [EDPOL] = x
Output flip-flop
Selected counter bus 500h 1000h 1100h 1000h 1100h 1000h
Flag output and S n [FLAG]
AS1 (read from A n ) 0xxxxxx 1000h 1000h 1000h 1000h
AS2 = AS1 according to the OUDIS[OU n ] field
Figure 300. SAOC toggling the output flip-flop
60.5.3.9.5 SAOC with flag behavior
C n [EDSEL] = 1 Output flip-flop
C n [EDPOL] = X
Selected counter bus 0h 1h 2h 0h 1h 2h 0h 1h 2h
System clock
AS1 match
Flag set event
Flag output and S n [FLAG]
Flag clear
AS2 (write to A n ) 1h
Figure 301. SAOC with flag behavior
60.5.3.10 Input Pulse Width Measurement (IPWM) mode

---

*Page 1863*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.10.1 Overview
This mode allows you to measure the width of a positive or negative pulse. Select whether to measure from the rising or falling
edge with Edge Polarity ( Cn[EDPOL] ).
The first leading edge:
• Triggers the first input capture, which latches the count value of the selected timebase into BS2 (see AS1, AS2, BS1, and BS2
shadow registers )
• Does not set the input-capture flag ( Sn[FLAG] )
• Enables AS2 capture
The next trailing edge:
• Latches the selected timebase into AS2
• Sets the input-capture flag ( Sn[FLAG] )
• Transfers BS2 to BS1 and AS1
eMIOS captures successive values on consecutive edges of opposite polarity. If you initiate subsequent input capture events while
the input-capture flag ( Sn[FLAG] ) is set, eMIOS updates AS2, BS1, and AS1 with the latest captured values and keeps the flag set.
60.5.3.10.2 Coherency
Reading A n [A] updates BS1 with AS1. It also disables transfers from BS2 and BS1 until the next read of B n [B] .
Reading Bn updates BS1 with AS1. It also re-enables transfers from BS2 to BS1, which takes effect at the next trailing
edge capture.
Because transfers from BS2 to AS1 are never blocked, to guarantee data coherency you must read An , then read Bn .
If you do not require coherent data, you should read Bn before you read An , although doing so requires a second read of Bn to
unblock BS1 updates.
60.5.3.10.3 Timing example: measure pulse width
This figure shows how to measure the input pulse width by subtracting BS1 from AS2.
BS2 AS2 BS2 AS2 BS2
C n [EDPOL] = 1
Input signal (after input filter)
Selected counter bus 500h 1000h 1100h 1250h 1525h 16A0h
Flag output and S n [FLAG]
AS2 (read from A n ) 0xxxxxx 1100h 1525h
BS2 0xxxxxx 1000h 1250h 16A0h
AS1 0xxxxxx 1000h 1250h
BS1 (read from B n ) 0xxxxxx 1000h 1250h
Figure 302. Timing example: measure pulse width

---

*Page 1864*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.10.4 Timing example: AS1 and BS1 updates on channel reads
This example illustrates AS1 and BS1 updates when you read A n [A] and B n [B] . AS1 always has coherent data related to AS2
(see Coherency ).
Read A n Read B n
C n [EDPOL] = 1 BS2 AS2 BS2 AS2 BS2
Input signal (after input filter)
Selected counter bus 500h 1000h 1100h 1250h 1525h 16A0h
Flag output and S n [FLAG]
AS2 (read from A n ) 0xxxxxx 1100h 1525h
BS2 0xxxxxx 1000h 1250h 16A0h
AS1 0xxxxxx 1000h 1250h
BS1 (read from B n ) 0xxxxxx 1000h 1000h 1250h
Figure 303. Timing example: BS1 and AS1 updates on channel reads
60.5.3.11 Input Period Measurement (IPM) Mode
60.5.3.11.1 Overview
This mode allows you to measure the period of an input signal. Select whether to measure from the rising or falling edges with
Edge Polarity ( C n [EDPOL] ).
The first edge of selected polarity:
• Latches the selected timebase into AS2 and BS2 (see AS1, AS2, BS1, and BS2 shadow registers )
• Transfers BS2 to BS1
• Does not set the input-capture flag ( S n [FLAG] )
The second edge of the same polarity:
• Latches the counter bus value into AS2 and BS2
• Transfers BS2 to BS1 and AS1
• Sets the input-capture flag ( S n [FLAG] )
eMIOS repeats this process for each subsequent capture.
eMIOS captures successive values on two consecutive edges of the same polarity. The measured input signal must have a period
of at least four system clock cycles to be properly captured by the synchronization logic at the channel input, even if the input filter
is in bypass mode.
60.5.3.11.2 Coherency
Reading A n [A] updates BS1 with AS1, which provides coherent data in AS2 and BS1 (see Timing example: AS1 and BS1 updates
on channel reads ). It also disables transfers from BS2 and BS1 until the next read of B n [B] .
Reading B n [B] updates BS1 with AS1. It also re-enables transfers from BS2 to BS1, which takes effect at the next edge capture.

---

*Page 1865*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.11.3 Timing example: measure input period
This example illustrates how to measure the input period by subtracting BS1 from AS2.
A A A
C n [EDPOL] = 1
Input signal (after input filter)
Selected counter bus 500h 1000h 1100h 1250h 1525h 16A0h
Flag output and S n [FLAG]
AS2 (read from A n ) 0xxxxxx 1000h 1250h 16A0h
BS2 0xxxxxx 1000h 1250h 16A0h
AS1 0xxxxxx 1000h 1250h
BS1 (read from B n ) 0xxxxxx 1000h 1250h
Figure 304. Timing example: measure input period
60.5.3.11.4 Timing example: AS1 and BS1 updates on channel reads
This diagram illustrates AS1 and BS1 updates when you read A n [A] and B n [B] .
Read A n Read B n
C n [EDPOL] = 1 A A A
Input signal (after input filter)
Selected counter bus 500h 1000h 1100h 1250h 1525h 16A0h
Flag output and S n [FLAG]
AS2 (read from A n ) 0xxxxxx 1000h 1250h 16A0h
BS2 0xxxxxx 1000h 1250h 16A0h
AS1 0xxxxxx 1000h 1250h
BS1 (read from B n ) 0xxxxxx 1000h 1000h 1250h
Figure 305. Timing example: AS1 and BS1 updates on channel reads
60.5.3.12 Double Action Output Compare (DAOC) mode
60.5.3.12.1 Overview
In this mode, matches on comparators A and B cause the UC to generate the leading and trailing edges of the variable pulse width
output. See DAOC submodes and MODE field values . There is no restriction on the order in which A and B matches occur.
When the UC enters DAOC mode from GPIO mode, eMIOS disables both comparators and drives the complement of Edge
Polarity ( C n [EDPOL] ) onto the output flip-flop.

---

*Page 1866*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.12.2 DAOC submodes and MODE field values
Table 333. DAOC submodes and MODE field values
When set flag event occurs C n [MODE] (binary)
Only on B matches 000_0110
On A or B matches 000_0111
60.5.3.12.3 Data transfer and compare
If you enable output (write 0 to OUDIS[OU n ] ), on the next clock cycle the UC transfers AS2 and BS2 to AS1 and BS1 respectively
(see Timing example: leading dead time insertion ) . If you disable output (write 1 to OUDIS[OU n ] ), no transfer occurs.
eMIOS enables and disables the A and B comparators independently. It enables comparator A only after the transfer to AS1
completes, and disables comparator A on the next A match. Similarly, eMIOS enables comparator B only after the transfer to BS1
completes, and disables comparator B on the next B match.
When a match occurs on comparator A, the output flip-flop acquires the value of Edge Polarity ( C n [EDPOL] ). When a match occurs
on comparator B, the output flip-flop acquires the complement of Edge Polarity ( C n [EDPOL] ).
60.5.3.12.4 Differences between flag on A match and flag on A and B matches
In the flag on B match submode (see DAOC submodes and MODE field values ), the UC sets the input-capture flag ( S n [FLAG] )
only for matches on the B comparator. In the flag on both A and B matches submode, it sets the flag for matches on
either comparator.
If subsequent enabled output compares occur on AS1 or BS1, the UC continues generating pulses regardless of the flag state.
If you load both AS1 and BS1 with the same value, the B match takes precedence and the UC drives the complement of Edge
Polarity ( C n [EDPOL] ) onto the output flip-flop.
60.5.3.12.5 Forced operation by Cn[FORCMA] and Cn[FORCMB]
Force Match A ( C n [FORCMA] ) and Force Match B ( C n [FORCMB] ) allow you to force the output flip-flop to the level corresponding
to the comparator, Edge Polarity ( C n [EDPOL] ) for an A match, or the complement of Edge Polarity for a B match. The force-match
operations do not set the input-capture flag ( S n [FLAG] ).
60.5.3.12.6 Timing example: flag on both matches
Update to
AS1 and BS1 AS1 match BS1 match AS1 match BS1 match
Output flip-flop
Selected counter bus 500h 1000h 1100h 1000h 1100h
Input capture flag
(Sn[FLAG])
AS1 (read from An) 0xxxxxx 1000h 1000h 1000h
BS1 (read from Bn) 0xxxxxx 1100h 1100h 1100h
AS2 mirrors AS1 if OUDIS[OUn] = 0
BS2 mirrors BS1 if OUDIS[OUn] = 0
Figure 306. Timing example: flag on both matches

---

*Page 1867*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.12.7 Timing example: flag on second match
Update to
AS1 and BS1 AS1 match BS1 match AS1 match BS1 match
Output flip-flop
Selected counter bus 500h 1000h 1100h 1000h 1100h
Input capture flag
(Sn[FLAG])
AS1 (read from An) 0xxxxxx 1000h 1000h 1000h
BS1 (read from Bn) 0xxxxxx 1100h 1100h 1100h
AS2 mirrors AS1 if OUDIS[OUn] = 0
BS2 mirrors BS1 if OUDIS[OUn] = 0
Figure 307. Timing example: flag on second match
60.5.3.12.8 Timing example: transfer disabling
Write to AS2 Write to AS2 Write to AS2
Write to BS2 Write to BS2 Write to BS2
C n [EDSEL] = x
Output flip-flop
C n [EDPOL] = 1
Selected counter bus 0h 1h 2h 0h 1h 2h 0h 1h 2h
System clock
Enabled AS1 match
Enabled BS1 match
Flag set event
Flag output and S n [Flag]
Flag clear
OUDIS[OU n ]
AS1 (read from A n ) 1h 1h 1h
AS2 (write to A n ) 1h 1h 1h
BS1 (read from B n ) 2h 2h 2h
BS2 (write to B n ) 2h 2h 2h
Figure 308. Timing example: transfer disabling
60.5.3.13 Pulse Edge Counting (PEC) mode

---

*Page 1868*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.13.1 Overview
This mode counts the number of pulses or edges detected on the input for a desired time window. C n [EDSEL] and C n [EDPOL]
determine which edges the UC counts.
Specify the window start time in AS1 and the end time in BS1. After you write to AS1, when the selected timebase matches
comparator A, eMIOS resets the internal counter and starts counting input events. When the timebase matches comparator
B, eMIOS:
• Disables the internal counter.
• Transfers the counter's content to AS2.
• Sets the input-capture flag (changes S n [FLAG] to 1).
You obtain the number of detected pulses by reading C n [C] or AS2.
Timing example continuous and Timing example single-shot show how to use the UC in continuous or single-shot operation.
60.5.3.13.2 PEC submodes and MODE field values
Table 334. PEC submodes and MODE field values
Operation C n [MODE] (binary)
Continuous; the next match between comparator A and the selected timebase 000_1010
resets the internal counter and enables counting again. To guarantee coherent
measurements when you read C n [C] after S n [FLAG] becomes 1, you must check
if the timebase value is out of the time interval defined by AS1 and BS1.
Alternately, AS2 (available in ALTA n [ALTA] ) always holds the latest available
measurement, providing coherent data at any time after the first set-flag event.
Single-shot; The next match between comparator A and the selected timebase 000_1011
has no effect until you write to A n [A] . eMIOS also transfers the C n [C] value to AS2
when a match in the B comparator occurs.
60.5.3.13.3 Timing example continuous
Write to AS1 and BS1 BS1 match BS1 match
AS1 match AS1 match
CNT n
Number of events detected
Number of events detected
0h Time
Flag output and S n [FLAG]
Selected counter bus 90h 303h 90h 303h
AS1 (write to A n ) 90h 90h 90h
BS1 (write to B n ) 303h 303h 303h
AS2 (read from ALTA n ) CNT n AS2 CNT n AS2
Figure 309. Timing example continuous

---

*Page 1869*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.13.4 Timing example single-shot
Write to AS1 and BS1 BS1 match BS1 match
AS1 match AS1 match
CNT n
Number of events detected
Number of events detected
0h Time
Flag output and S n [FLAG]
Selected counter bus 90h 303h 90h 303h
AS1 (write to A n ) 90h 90h 90h
BS1 (write to B n ) 303h 303h 303h
AS2 (read from ALTA n ) CNT n AS2 CNT n AS2
Figure 310. Timing example single-shot
60.5.3.14 Modulus Counter (MC) mode
60.5.3.14.1 Overview
In this mode, the UC produces a timebase for a counter bus or as a general purpose timer. The internal counter ( C n [C] ) counts
up from the current value until it matches AS1.
Selecting external clock source (see MC submodes and MODE field values ) causes the UC to uses the input signal pin as the
source. You select the triggering polarity edge using Edge Polarity ( C n [EDPOL] ) and Edge Select ( C n [EDSEL] ).
You must write only non-zero values to A n [A] . Writing to A n [A] or the internal counter may cause a match to be missed and the
counter to roll over and resume operation in the next cycle. The channel writes 0 to BS1, which is not accessible by the MCU. You
can read and write BS2, but it is not used in this mode.
60.5.3.14.2 MC submodes and MODE field values
Table 335. MC submodes and MODE field values
Submode or function C n [MODE] (binary)
1
Internal clock source 001_0pp0
1
External clock source 001_0pp1
1
Internal counter reset on match start 001_0p0p
1
Internal counter reset on match end 001_0p1p
1
Up Count 001_00pp
1
Up Count-Down Count 001_01pp
1. p = Adjust parameters for submodes and options. See Unified channels (UC) .

---

*Page 1870*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.14.3 Up Count submode
Table 336. Up Count submode operation
1
Counter reset timing C n [MODE]
Bit 1 External clock (Bit 0 = 1) Internal clock (Bit 0 = 0)
Reset on match start 0
When the AS1 match occurs, the UC: When the AS1 match occurs, the UC:
• Writes 0 to the internal counter. • Writes 0 to the internal counter.
• Sets the input-capture flag • Sets the input-capture flag
( S n [FLAG] ). ( S n [FLAG] ).
• Increments the internal counter at the • Maintains the internal counter at 0 on
next input event, resulting in a short the next prescaler tick after the match.
zero count.
• Resumes counting on the next
prescaler tick.
See Timebase with the fastest prescaler ratio and Timebase generation with clear on
match start and internal clock .
Clear on match end 1
When the next input event after the AS1 When the next internal counter tick after the
match occurs, the UC: AS1 match occurs, the UC:
• Writes 0 to the internal counter. • Writes 0 to the internal counter.
• Sets the input-capture flag • Sets the input-capture flag
( S n [FLAG] ). ( S n [FLAG] ).
See Timebase with the fastest prescaler ratio and Timebase generation with clear on
match end .
1. If you select internal clock source and a prescaler value of 1 for the internal counter, the behavior is the same for both
submodes.
See Timing example: Up Count submode .
60.5.3.14.4 Timing example: Up Count submode
Match AS1 Match AS1 Match AS1 Match AS1
CNT n Write to AS2 Write to AS2
FF_FFFFh
303h
200h
0h Time
Flag output and S n [FLAG]
AS1 (read from A n ) 0xxxxxx 303h 303h 303h 200h 200h
Figure 311. Timing example: Up Count submode
60.5.3.14.5 Up Count-Down Count submode operation
In Up Count-Down Count submode:

---

*Page 1871*

Enhanced Modular IO Subsystem (eMIOS)
• When the internal counter matches AS1, the counter begins decrementing and the UC sets the input-capture flag
( S n [FLAG] ).
• When the internal counter matches BS1, the counter begins incrementing and, if in clear on match end operation ( C n [1] =
1 ), the UC sets the input-capture flag ( S n [FLAG] ).
See Timing example: Up Count-Down Count submode .
60.5.3.14.6 Timing example: Up Count-Down Count submode
Match AS1 Match BS1 (= 0) Match AS1 Match BS1 (= 0)
CNT n Write to AS2 Write to AS2
FF_FFFFh
303h
200h
0h Time
AS1 (read from A n ) 0h 303h 303h 200h 200h 200h
Flag output and S n [FLAG]
Figure 312. Timing example: Up Count-Down Count submode
60.5.3.14.7 Up Count with delayed reload operation
When the internal counter matches AS1 in the Up Count submode or BS1 in the Up Count-Down Count submode, the UC asserts
the counter bus reload signal. You can read the counter bus reload signal in the following modes:
• Output PWM Buffered (OPWMB) mode
• Output Pulse Width and Frequency Modulation Buffered (OPWFMB) mode
• Center Aligned Output PWM with Dead Time Insertion Buffered (OPWMCB) mode
You can use the Reload Signal Output Delay Interval ( C2 n [UCRELDEL_INT] ) to cause the UC to assert the reload signal only after
a specified number of match events (from 2 to 32).
See Timing example: Up Count mode with delayed reload .

---

*Page 1872*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.14.8 Timing example: Up Count mode with delayed reload
CNT n
Match AS1
6h
Write to AS2
4h
1h
Time
AS2 6h
AS1 4h
AS1 or BS1 reload signal or cb_reload_n
C2_ n [UCRELDEL_INT] = 1b
AS1 or BS1 reload signal or cb_reload_n
C2_ n [UCRELDEL_INT] = 11b
AS1 or BS1 reload signal or cb_reload_n
C2_ n [UCRELDEL_INT] = 0b
Figure 313. Timing example: Up Count mode with delayed reload
60.5.3.15 Modulus Counter Buffered (MCB) mode
60.5.3.15.1 Overview
In this mode, the UC generates a timebase that can be shared with other channels through the internal counter buses. AS1 is
double-buffered, so you can change AS2 at any time with smooth transitions. The UC updates AS1 at the counter period boundary,
which is when the internal counter ( CNT n [C] ) transitions to 1h.
Selecting external clock (see MCB submodes and MODE field values ) causes the UC to use the input signal as the source. You
select the triggering edge with Edge Polarity ( C n [EDPOL] ) and Edge Select ( C n [EDSEL] ).
You must ensure the internal counter is in the range from 1h to the AS1 value; otherwise, the A n [A] match does not occur and this
causes the internal counter to wrap at the maximum counter value ( FFFFh for a 16-bit counter). After a counter wrap occurs, the
UC resets the counter to 1h and resumes normal operation.
NOTE
Do not write 1h to the internal counter when the UC is in Freeze state. Doing so can cause the match to be missed
and the counter to overflow.
60.5.3.15.2 Changing to MCB mode
To avoid causing an unexpected interrupt or DMA request, you must use the following procedure to change to MCB mode:
1. Put the UC in GPIO mode (write 0b or 1b to C n [MODE] ).
2. Disable the UC input capture flag (write 0 to C n [FEN] ).
3. Write the correct values for MCB mode to A n and B n .

---

*Page 1873*

Enhanced Modular IO Subsystem (eMIOS)
4. Put the UC in MCB mode (write the appropriate value to C n [MODE] , as described in MCB submodes and MODE field
values ).
5. Clear the UC input capture flag (write 1 to S n [FLAG] ).
6. Enable the UC input capture flag (write 1 to C n [FEN] ).
60.5.3.15.3 MCB submodes and MODE field values
Table 337. MCB submodes and MODE field values
Submode or function C n [MODE] (binary)
1
Internal clock source 101_0pp0b
1
External clock source 101_0pp1b
1
Up Count 101_000p
1
Reserved 101_001p
1
Up Count-Down Count 101_01pp
1
Flag set only on match start 101_010p
1
Flags also set at counter period boundary 101_011p
1. p = Adjust parameters for submodes and options. See Unified channels (UC) .
60.5.3.15.4 Up Count submode
In the Up Count submode:
• The internal counter ( CNT n [C] ) starts counting up (incrementing) from its current value.
• When the internal counter matches AS1 and a clock tick occurs (either prescaled clock or input pin event), the UC resets
the internal counter to 1h.
• The UC sets the input-capture flag ( S n [FLAG] ) one system clock cycle after the match occurs.
You must write only values greater than 1h to AS1.
The boundary between counter period n and period n +1 occurs when the UC changes the internal counter from the AS1 value in
period n to 1h in period n +1. AS1 defines the counter period.
The UC asserts the AS1 load signal (which has a duration of one system clock cycle) at the last system clock cycle of a counter
period. This causes the UC to update AS1 with AS2 at the period boundary, at the same time that it resets the counter to 1h.
Therefore, any value you write to AS2 during period n updates AS1 at the next period boundary and the UC uses it in period n +1.
See Timing example: Up Count submode .
You can use Output Update Disable (OUDIS) to delay the update of AS1 for synchronization.
The UC sets the input-capture flag ( S n [FLAG] ) at the period boundary. See Timing example: Up Count submode flag assertion .

---

*Page 1874*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.15.5 Timing example: Up Count submode
Counter period n Counter period Counter period n +2
n +1
Match AS1 Match AS1 Match AS1
Internal counter Write to AS2 Write to AS2
8
8h
6
6h
4
4h
2h
1h Time
Counter = AS1
AS1 load signal
AS1 value 8h 4h 6h
AS2 value 8h 4h 6h
Prescaler ratio = 2
Figure 314. Timing example: Up Count submode
60.5.3.15.6 Timing example: Up Count submode flag assertion
Counter period n Counter period n +1 Counter period n +2
Match AS1 Match AS1 Match AS1
CNT n Write to AS2 Write to AS2
7h
6h
5h
1h Time
Flag set event
Flag output and Sn[FLAG]
Flag clear
AS2 5h 7h
AS1 6h 5h 7h 7h
Prescaler ratio = 1
Figure 315. Timing example: Up Count submode flag assertion
60.5.3.15.7 Up Count-Down Count submode
In Up Count-Down Count submode:

---

*Page 1875*

Enhanced Modular IO Subsystem (eMIOS)
• The counter period is: (2 x AS1) – 2. The counter changes direction at AS1 match and counts down (decrements) until it
reaches 1h, then counts up (increments) again.
• BS1 generates a match to start the internal counter incrementing, and BS1 cannot be changed in this submode.
The UC updates AS1 at the counter period boundary. If you write AS2 (write A n [A] ) during period n , the UC uses this new
value in period n +1 for AS1 match. You can disable updates of AS1 ( OUDIS[OU n ] ). See Timing example: Up Count-Down
Count submode .
The UC sets the input-capture flag ( S n [FLAG] ) at AS1 match start, and may also do so at the period boundary. See Overview and
Timing example: Up Count-Down Count submode flag assertion .
60.5.3.15.8 Timing example: Up Count-Down Count submode
Counter period n Counter period n +1 Counter period n +2
Match AS1 Match AS1
CNT n Write to AS2 Write to AS2
6h
5h
1h Time
Counter = 2
AS1 load signal
AS1 6h 5h 6h
AS2 6h 5h 6h
Prescaler ratio = 2
Figure 316. Timing example: Up Count-Down Count submode

---

*Page 1876*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.15.9 Timing example: Up Count-Down Count submode flag assertion
Counter period n Counter period n +1 Counter period n +2
Match AS1 Match AS1
CNT n Write to AS2 Write to AS2
7h
6h
5h
1h Time
Flag set event
Flag output and Sn[FLAG]
Flag clear
AS2 5h 7h
AS1 6h 5h 7h
Prescaler ratio = 1
Figure 317. Timing example: Up Count-Down Count submode flag assertion
60.5.3.15.10 Up Count mode with delayed reload operation
When the internal counter matches AS1 in the Up Count submode or 1h in the Up Count-Down Count submode (see MCB
submodes and MODE field values ), the UC asserts the counter bus reload signal. You can read the counter bus reload signal in
the following modes:
• Output PWM Buffered (OPWMB) mode
• Output Pulse Width and Frequency Modulation Buffered (OPWFMB) mode
• Center Aligned Output PWM with Dead Time Insertion Buffered (OPWMCB) mode
You can use the Reload Signal Output Delay Interval ( C2[UCRELDEL_INT] ) to cause the UC to assert the reload signal only after
a specified number of match events (from 2 to 32).
See Timing example: Up Count mode with delayed reload .

---

*Page 1877*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.15.11 Timing example: Up Count mode with delayed reload
CNT n [C]
6h
Write to AS2 Match AS1
4h
1h
Time
AS2 6h
AS1 4h 6h
AS1/BS1 reload signal or cb_reload_n
C2_ n [UCRELDEL_INT] = 1b
AS1 or BS1 reload signal or cb_reload_n
C2_ n [UCRELDEL_INT] = 11b
AS1 or BS1 reload signal or cb_reload_n
C2_ n [UCRELDEL_INT] = 0b
Figure 318. Timing example: Up Count mode with delayed reload
60.5.3.16 Output Pulse Width and Frequency Modulation Buffered (OPWFMB) mode
60.5.3.16.1 Overview
In this mode, the UC produces an output signal that has:
• A duty cycle determined by AS1.
• A period of BS1.
The double-buffered AS1 and BS1 registers produce smooth duty-cycle and period transitions when you change AS1 and BS1
during runtime.
See Table 344 for the values that you must write to C n [MODE] to enter this mode.
To provide smooth and consistent channel operation:
• AS1 and BS1 are both double-buffered to allow smooth signal generation when changing the register values.
• There is a delay from the AS1 match to the output pin transition (see AS1 and BS1 match timing ).
• The internal counter ranges from 1h to BS1. When you write to BS1, you must write only values greater than 1h. Writing
0h or 1h leads to unpredictable results.
When a match on comparator A occurs, the UC drives C n [EDPOL] onto the output flip-flop. When a match on comparator B occurs,
the UC drives the complement of C n [EDPOL] onto the output flip-flop. A BS1 match also causes the internal counter to transition
to 1h, thus restarting the counter cycle.

---

*Page 1878*

Enhanced Modular IO Subsystem (eMIOS)
The UC automatically selects the internal channel counter as the timebase when you select this mode. The mode supports duty
cycles ranging from 0% to 100%.
60.5.3.16.2 OPWFMB submodes and MODE field values
Table 338. OPWFMB submodes and MODE field values
Match behavior C n [MODE] (binary)
When BS1 matches the selected counter 101_1000
When AS1 or BS1 match the selected counter 101_1010
60.5.3.16.3 Avoid counter wrapping
When the UC enters OPWFMB mode, the output flip-flop acquires the value of C n [EDPOL] . If the UC transitions from GPIO to
OPWFMB mode when the internal counter value is not within the range 1h–BS1, then the B match never occurs. Because the
match never occurs, the internal counter wraps at the maximum counter value— FFFFh for a 16-bit counter. When the counter
wrap occurs, the counter returns to 1h and normal OPWFMB operation resumes. Therefore, to avoid this counter wrapping,
ensure that the counter value is within the range 1h–BS1 before the UC enters OPWFMB mode.
60.5.3.16.4 AS1 and BS1 match timing
As illustrated in Timing example: AS1 and BS1 match , the output pin transition occurs when the AS1 or BS1 match signal
deasserts, as indicated by the AS1 match negative-edge detection signal. If you write 4h to AS1, the output pin transitions 4
counter increments plus one system clock cycle after the counter period starts.
60.5.3.16.5 Timing example: AS1 and BS1 match
System clock
Prescaler
8
CNT n
5
4
1
Time
AS1 4h
BS1 8h
Match AS1 negative-edge detection
AS1 match
AS1 match
negative-edge detection
Match BS1
negative-edge detection
BS1 match
BS1 match
negative-edge detection
Output pin
Prescaler ratio = 2 C n [EDPOL] = 0
Figure 319. Timing example: AS1 and BS1 match

---

*Page 1879*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.16.6 0% duty cycle
Timing example: AS1 = 0 illustrates the generated output signal when AS1 = 0, which results in a 0% duty cycle.
Because the internal counter never reaches zero in this situation, the UC infers a match as if AS1 = 1h. However, it is the positive
edge of the match signal that triggers the output pin transition instead of the negative edge, which is what happens when AS1 =
1h. The AS1 positive-edge match signal from period n +1 occurs at the same time as the BS1 negative-edge match signal from
period n . This timing allows the AS1 positive-edge match to mask the BS1 negative-edge match when they occur at the same time.
As a result, no transition occurs on the output flip-flop and the UC generates a 0% duty cycle.
60.5.3.16.7 Timing example: AS1 = 0
Counter period n Counter period n +1
Write to AS2
System clock
Prescaler
CNT n
5
4
1 1
Time
AS1 4h 0h
AS2 0h
BS1 8h
AS1 match
Match AS1 positive-edge detection
AS1 match
positive-edge detection
Match AS1 negative-edge detection
AS1 match
negative-edge detection
BS1 match
Match BS1 negative-edge detection
BS1 match
negative-edge detection
Output pin No transition at this point
Prescaler ratio = 2 C n [EDPOL] = 0
Figure 320. Timing example: AS1 = 0
60.5.3.16.8 Loading AS1 and BS1
As illustrated in Timing example: AS1 and BS1 update and FLAGs , AS1 and BS1 both use the same signal, generated at the last
system clock cycle of a counter period. Therefore, eMIOS updates AS1 and BS1 with AS2 and BS2 values, respectively, at the
same time that it changes the internal counter ( CNT n [C] ) to 1h. This event is the counter period boundary. The load signal pulse
lasts for one system clock cycle. If you write AS2 and BS2 within counter period n , their values are available in AS1 and BS1,
respectively, at the first clock cycle of counter period n +1. The UC then uses these new values for matches at counter period n +1.
You can use Output Update Disable (OUDIS) to delay updating AS1 and BS1 for synchronization purposes.

---

*Page 1880*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.16.9 Timing example: AS1 and BS1 update and FLAGs
Counter period n Counter period n +1 Counter period n +2
Write to AS2 Write to AS2
Match AS1 Match BS1 Match AS1 Match BS1 Match BS1
lnternal counter
Write to BS2
8h
6h
4h
2h
Due to BS1 match
1h
counter period n -1
Output pin
Flag set event
Flag output and S n [FLAG]
Flag clear
AS1 or BS1 load signal
AS1 2h 4h 6h
AS2 2h 4h
6h
BS1 8h 6h
C n [EDPOL] = 0
BS2 8h 6h
Prescaler ratio = 4
Figure 321. Timing example: AS1 and BS1 update and FLAGs
60.5.3.16.10 Flag generation
Timing example: AS1 and BS1 update and FLAGs assumes that both the channel and global prescalers are 1h (divide ratio of
2), causing the internal counter to transition every four system clock cycles. You can configure flags to be generated only on BS1
matches when bit 1 of C n [MODE] is 0, or on both AS1 and BS1 matches when that bit is 1. Because the BS1 flag occurs at the
period boundary, you can use this flag to indicate that the AS2 or BS2 data written in period n has been loaded to AS1 or BS1,
respectively, thereby generating matches in period n +1. Flag operation is synchronous, which means the flag sets one system
clock cycle after the set-flag event.
60.5.3.16.11 Output disable
As illustrated in Timing example: active output disabled , the output-disable feature in OPWFMB mode forces the channel output
flip-flop to the value of C n [EDPOL] . This functionality supports applications that use active-high signals and a high-to-low transition
at an AS1 match. In this case, you must write 0 to C n [EDPOL] . You also must configure the internal counter to transition on every
system clock cycle (write 0 to both GCP and CP).
The output-disable feature operates synchronously, meaning that the assertion of the Output Disable input signal causes the
channel output flip-flop to transition to C n [EDPOL] at the next system clock cycle. If the Output Disable input pin deasserts, the
output-signal transition occurs at the next AS1 or BS1 match.
Timing example: active output disabled assumes that the Output Disable input is enabled (using C n [ODIS] ) and selected (using
C n [ODISSL] ). See UC Control n (C0 - C23) for a detailed description of the ODIS and ODISSL fields that enable and select the
Output Disable inputs, respectively.

---

*Page 1881*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.16.12 Timing example: active output disabled
Cycle n Cycle n +1 Cycle n +2
Match AS2 Match AS1
Match AS1 Match BS1 Match AS1 Match BS1 Match BS1
Internal counter
Write to BS2
8h
6h
4h
Due to BS1 match
2h
cycle n -1 1h Time
Output pin
Flag set event
Flag output and S n [FLAG]
Flag clear event
Output disable
AS1 2h 4h 6h
AS2 2h 4h 6h
BS1 8h 6h
BS2 8h 6h
EDPOL = 0
Prescaler ratio = 1
Figure 322. Timing example: active output disabled
60.5.3.16.13 Force match
Force Match A ( C n [FORCMA] ) and Force Match B ( C n [FORCMB] ) allow you to force the output flip-flop to the level corresponding
to a match on comparators A or B, respectively. Similarly to a BS1 match, Force Match B changes the internal counter to 1h. The
force-match operations do not set the input-capture flag ( S n [FLAG] ).
60.5.3.16.14 100% and 0% duty cycles
Timing example: from 100% to 0% duty cycle illustrates the generation of 100% and 0% duty-cycle signals. Initially, AS1 = 8h and
BS1 = 8h. In this case, a BS1 match has precedence over an AS1 match—therefore, the output flip-flop acquires the complement
of C n [EDPOL] . This configuration corresponds to a 100% duty-cycle signal. The same output signal can be generated for any AS1
value greater than or equal to BS1.
If AS1 is 0, the UC generates a 0% duty-cycle signal. In this case, the BS1 = 8h match from period 8 occurs at the same time as
the AS1 = 0h match from period 9. See Timing example: AS1 = 0 for AS1 and BS1 match generation. In this case, an AS1 match
has precedence over a BS1 match, and the output signal transitions to C n [EDPOL] .

---

*Page 1882*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.16.15 Timing example: from 100% to 0% duty cycle
Counter Counter Counter Counter Counter Counter Counter Counter Counter
CNT n period 1 period 2 period 3 period 4 period 5 period 6 period 7 period 8 period 9
8h
1h
Output pin 100 % 0 %
AS1 8h 7h 6h 5h 4h 3h 2h 1h 0h
AS2 7h 6h 5h 4h 3h 2h 1h 0h
BS1 8h
C n [EDPOL] = 0
Prescaler ratio = 1
Figure 323. Timing example: from 100% to 0% duty cycle
60.5.3.17 Center Aligned Output PWM with Dead Time Insertion Buffered (OPWMCB) mode
60.5.3.17.1 Overview
In this mode, the UC generates a center-aligned PWM pulse with dead time insertion in the leading or trailing edge. See OPWMCB
functions and MODE field values . AS1 and BS1 are double-buffered to allow smooth output signal generation when you change
the AS2 or BS2 values.
When the UC enters OPWMCB mode from GPIO mode:
• The UC drives the complement of Edge Polarity ( C n [EDPOL] ) on the output flip-flop.
• Select the timebase using Bus Select ( C n [BSL] ). The timebase you select must be running in Up Count-Down Count
mode. See Timing example: Up Count submode flag assertion . You must start the MCB timebase after the UC enters
OPWMCB mode to avoid missing a match on the first duty cycle.
• Store the ideal duty cycle for the PWM signal in AS1 (write to A n [A] ), which the UC compares with the selected timebase.
• Store the dead time value in BS1 (write to B n [B] ), which the UC compares with the internal counter ( CNT n [C] ).
The internal counter may use the internal prescaler ratio, while the selected timebase may use a different prescaler ratio. The UC
always resets the internal counter to 1h when an AS1 match occurs.
As in OPWMB and OPWFMB modes, the UC generates the match signal which compares the selected timebase with AS1 or BS1.
When the UC deasserts the channel's combinational comparator output signal, it uses the match signal to assert or negate the
channel's output flip-flop. See Timing example: AS1 and BS1 match for an illustration of the delay from match to output flip-flop
transition in OPWFMB mode. The operation of OPWMCB mode is similar to OPWFMB mode with respect to match behavior and
output pin transition.
See Entering OPWMCB mode .
60.5.3.17.2 OPWMCB functions and MODE field values
Table 339. OPWMCB functions and mode field values
Function C n [MODE] (binary)
1
Insert trailing edge dead time with duty cycle determined by 101_11p0
BS1 + AS1
Table continues on the next page...

---

*Page 1883*

Enhanced Modular IO Subsystem (eMIOS)
Table 339. OPWMCB functions and mode field values (continued)
Function C n[MODE] (binary)
1
Insert leading edge dead time with duty cycle determined by 101_11p1
BS1 – AS1
1
Assert the input capture flag (Sn[FLAG]) on the trailing edge 101_110p
of the output PWM signal
1
Assert the input capture flag (Sn[FLAG]) on both edges of the 101_111p
output PWM signal
1. p = Adjust parameters for submodes and options. See Unified channels (UC) .
60.5.3.17.3 Entering OPWMCB mode
To enter OPWMCB mode (this procedure assumes the channel is initially in GPIO mode):
1. Disable the GCP (write 0 to MCR[GPREN] ).
2. For the timebase (MCB) channel:
a. Disable the CP (write 0 to C n [UCPREN] ).
b. Initialize the internal counter (write 1h to CNT n [C] ).
c. Initialize the A data (write appropriate value to A n [A] ).
d. Configure the UC for MCB Up Count-Down Count mode (write 101_01 xy b to C n [MODE] , where x = 0 for flag set
on match start or x = 1 for flag set at period boundary, and y = 0 for internal clock source or y = 1 for external
clock source).
e. Select the CP ratio ( C n [UCPRE] ).
f. Enable the CP (write 1 to C n [UCPREN] ).
3. For the OPWMCB channel:
a. Disable the CP (write 0 to C n [UCPREN] ).
b. Initialize the A data (write appropriate value to A n [A] ).
c. Initialize the B data (write appropriate value to B n [B] ).
d. Select the timebase input ( C n [BSL] ).
e. Configure the UC for MCB Up Count-Down Count mode (write 101_01 xy b to C n [MODE] , where x = 0 for flag set
on match start or x = 1 for flag set at period boundary, and y = 0 for internal clock source or y = 1 for external
clock source).
f. Select the CP ratio ( C n [UCPRE] ).
g. Enable the CP (write 1 to C n [UCPREN] ).
4. Enable the GPC (write 1 to MCR[GPREN] ).
60.5.3.17.4 Timing example: AS1 and BS1 load
When the selected counter bus transitions from 2h to 1h:
• The UC loads AS1 and BS1 as shown in the following figure.
• This event defines the counter period boundary.
The UC loads the values you write to AS2 or BS2 within period n into AS1 or BS1, respectively, and uses them to generate matches
in period n +1.

---

*Page 1884*

Enhanced Modular IO Subsystem (eMIOS)
Counter period n Counter period n+1 Counter period n+2
Selected
counter bus Write to AS2 Write to BS2 Write to BS2 Write to AS2
6h
5h
1h Time
System clock
Selected counter == 2
AS1 or BS1 load signal
AS1 value 20h 15h 16h
AS2 value 20h 15h 16h
BS1 value 4h 5h 6h
BS2 value 4h 5h 6h
Prescaler ratio = 2
Figure 324. Timing example: AS1 and BS1 load
60.5.3.17.5 Operation sequence with leading edge dead time insertion
When operating with leading edge dead time insertion:
1. When the first AS1 match occurs, the UC writes 1h to the internal counter.
2. When a match between BS1 and the internal timebase occurs, the UC drives the value of Edge Polarity ( C n [EDPOL] ) on
the output flip-flop.
3. When the next match between AS1 and the selected timebase occurs, the UC drives the complement of Edge Polarity
( C n [EDPOL] ) on the output flip-flop.
The UC repeats the above sequence continuously.
You must not allow the internal counter to reach 0h as the result of a rollover. To avoid this, you must not write a value greater than
2 x ((external Count Up limit) – A n [A] )
into B n [B] .
See Timing example: leading dead time insertion .
60.5.3.17.6 Timing example: leading dead time insertion
This example illustrates two cycles of a center-aligned PWM signal. AS1 and BS1 values change within the same period, which
allows you to change the duty cycle and dead time values at the same time.

---

*Page 1885*

Enhanced Modular IO Subsystem (eMIOS)
Write to AS2
Selected Write to BS2
counter bus
20h
15h
13h
1h Time
AS1 value 15h 13h
AS2 value 15h 13h
BS1 value 2h 4h
BS2 value 2h 4h
Internal
time base Internal counter
becomes
1 on AS1 match
4h
2h
1h Time
Dead time
Dead time
Output flip-flop
Flag output and S n [FLAG]
C n [EDPOL] = 1
Figure 325. Timing example: leading dead time insertion
60.5.3.17.7 Operation sequence with trailing edge dead time insertion
When operating with trailing edge dead time insertion:
1. When the first match between AS1 and the selected timebase occurs, the UC:
a. Drives the value of Edge Polarity ( C n [EDPOL] ) on the output flip-flop.
b. Writes the internal counter ( CNT n [C] ) to 1h.
2. When the second match between AS1 and the selected timebase occurs, the UC:
a. Writes the internal counter to 1h.
b. Enables BS1 matches.
3. When a match between BS1 and the selected timebase occurs, the UC drives the complement of Edge Polarity
( C n [EDPOL] ) on the output flip-flop.
The UC repeats this sequence continuously.

---

*Page 1886*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.17.8 Timing example: trailing dead time insertion
Write to AS2
Selected Write to BS2
counter bus
20h
15h
13h
1h Time
AS1 value 15h 13h
AS2 value 15h 13h
BS1 value 2h 4h
BS2 value 2h 4h
Internal
time base
Internal counter
becomes
1 on AS1 match
4h
2h
1h Time
Dead time Dead time
Output flip-flop
Flag output and S n [FLAG]
C n [EDPOL] = 1
Figure 326. Timing example: trailing dead time insertion
60.5.3.17.9 Force match
Force Match A ( C n [FORCMA] ) and Force Match B ( C n [FORCMB] ) allow you to force the output flip-flop to the level corresponding
to a match on comparators A or B, respectively. If subsequent matches occur on comparators A and B, the UC continues to
generate PWM pulses regardless of the input capture flag ( S n [FLAG] ). The force-match operations do not set the input-capture
flag ( S n [FLAG] ).
When you select leading edge dead time insertion:
• Force Match A drives the complement of Edge Polarity ( C n [EDPOL] ) on the output flip-flop.
• Force Match B drives the value of Edge Polarity ( C n [EDPOL] ) on the output flip-flop.
When you select trailing edge dead time insertion:
• Force Match A drives the value of Edge Polarity ( C n [EDPOL] ) on the output flip-flop.
• Force Match B drives the complement of Edge Polarity ( C n [EDPOL] ) on the output flip-flop.
Force Match operation does not write 1h to the internal timebase.
Force Match operation does not set the input-capture flag.
When you assert Force Match A and Force Match B at the same time, the UC drives the complement of Edge Polarity ( C n [EDPOL] )
on the output flip-flop. This is the equivalent of saying that Force Match A has precedence over Force Match B when you select
leading edge dead time insertion, and Force Match B has precedence over Force Match A when you select trailing edge dead
time insertion.
Force Match A and Force Match B have the same output transition behavior in both Freeze state and unfrozen state.
60.5.3.17.10 Duty cycle
You generate a duty cycle from 0% to 100% by writing appropriate values to AS1 and BS1 relative to the period of the
external timebase.

---

*Page 1887*

Enhanced Modular IO Subsystem (eMIOS)
To generate a 100% duty cycle waveform:
• Select trailing edge dead time insertion (see OPWMCB functions and MODE field values ).
• Write 1h to Edge Polarity ( C n [EDPOL] ).
• The BS1 match must occur on or after the counter period boundary (external counter = 1).
You can also generate a 100% duty cycle waveform by writing 1h to AS1. However, if you do this before the UC enters OPWMCB
mode, a 100% duty cycle may not occur in the first PWM period depending upon the pin state at mode entry.
To generate a 0% duty cycle waveform:
• AS1 must be greater than the maximum value of the selected counter period.
• The pin must start the current period with the complement value of Edge Polarity ( C n [EDPOL] ). If starting at 100% duty
cycle, you can change Edge Polarity ( C n [EDPOL] ) to its complement by forcing the pin (see Force match ).
You must write only non-zero values to AS1. If you write the maximum value of the external counter given by
(external counter period)/2
to AS1, the UC constantly drives the value of Edge Polarity ( C n [EDPOL] ) on the output flip-flop.
UC logic prevents matches from one period propagating to the next period. In trailing edge dead time insertion, the UC masks out
BS1 matches that occur late in period n , so matches in period n +1 are unaffected and the output flip-flop does not transition.
60.5.3.17.11 Timing example: duty cycle
To generate a 100% duty cycle output waveform, write 4h to AS1 and 3h to BS1, as illustrated in this diagram. In this case, the
trailing edge is at the boundary of counter period n +1, which actually belongs to period n +2 and therefore does not cause the output
flip-flip to transition.
Selected Counter period n Counter period n +1 Counter period n +2
counter bus Write to AS2
20h
Time
1h
AS1 value 15h 4h
AS2 value 15h 4h
BS1 value 3h
BS2 value 3h
Internal
time base
3h
1h Time
Dead time Dead time Dead time
Output flip-flop
Figure 327. Timing example: duty cycle
60.5.3.17.12 Output disable
You can use Output Update Disable (write 0 to OUDIS[OU n ] ) to disable AS1 and BS1 updates. This allows you to update AS1 and
BS1 in the same counter period and synchronize the loading of these registers with the loading of AS1 or BS1 in other channels.

---

*Page 1888*

Enhanced Modular IO Subsystem (eMIOS)
Asserting Output Update Disable drives the complement of Edge Polarity ( C n [EDPOL] ) on output flip-flop. The internal channel
matches continue to occur, and therefore the UC continues to set flags.
When you deassert Output Update Disable, the output flip-flop is again controlled by AS1 and BS1 matches. The UC transitions
the output flip-flop only on system clock edges.
60.5.3.18 Output PWM Buffered (OPWMB) mode
60.5.3.18.1 Overview
In this mode, the UC produces PWM pulses with programmable leading and trailing edge placement. You must select an external
counter driven in MCB Up mode from one of the counter buses. AS1 and BS1 define the first and second edges, respectively.
C n [EDPOL] defines the output signal polarity. If C n [EDPOL] = 0, a negative edge occurs when AS1 matches the selected counter
bus and a positive edge occurs when BS1 matches the selected counter bus.
See OPWMB submodes and MODE field values for the values that you must write to C n [MODE] to enter this mode.
AS1 and BS1 are double-buffered and updated from AS2 and BS2, respectively, at the cycle boundary. The load operation is
similar to the one that occurs in OPWFMB mode. See Timing example: AS1 and BS1 update and FLAGs for more information
about AS1 and BS1 updates.
When the UC enters OPWMB mode, the UC drives C n [EDPOL] on the output flip-flop.
These rules apply to OPWMB mode:
• If the AS1 and BS1 matches occur at the same time within the same counter period, the BS1 match has precedence over
the AS1 match.
• An AS1 = 0 match in period n has precedence over a BS1 match in period n -1.
• The UC masks out AS1 matches if they occur after a BS1 match within the same period.
• The UC loads any value that you write to AS2 or BS2 in period n into AS1 and BS1 at the following period boundary (assuming
that the corresponding OU n field of Output Update Disable (OUDIS) is 0). Therefore, the UC uses the new values for AS1 and
BS1 matches in period n +1.
Timing example: 100% to 0% duty cycle illustrates a waveform changing from 100% to 0% duty cycle. In this example, BS1 is
programmed to the same value as the period of the external selected timebase. If you write a value smaller than 8h to BS1, you
cannot achieve a 0% duty cycle by only changing AS1. Because BS1 matches have precedence over AS1 matches, the flag output
transitions to the complement of C n [EDPOL] at a BS1 match. For another example, if you write 9h to BS1, a BS1 match does not
occur, and the UC generates a 0% duty-cycle signal.
60.5.3.18.2 OPWMB submodes and MODE field values
Table 340. OPWMB submodes and MODE field values
When FLAG generation occurs C n [MODE] (binary)
At a BS1 match 110_0000
At an AS1 or BS1 match 110_0010
60.5.3.18.3 Force match
Force Match A ( C n [FORCMA] ) and Force Match B ( C n [FORCMB] ) allow you to force the output flip-flop to the level corresponding
to a match on comparators A or B, respectively. If subsequent matches occur on comparators A and B, the UC continues to
generate PWM pulses regardless of the input capture flag ( S n [FLAG] ). The force-match operations do not set the input-capture
flag ( S n [FLAG] ).

---

*Page 1889*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.18.4 Matches and flag operation
Timing example: matches and flags illustrates the operation of OPWMB mode with an emphasis on AS1 and BS1 matches and the
transition of the channel output pin. The output pin transitions are based on the negative edges of the AS1 and BS1 match signals.
AS1 becomes zero in period n +1. In this case, the UC uses the match positive edge instead of the negative edge to transition the
output flip-flop.
60.5.3.18.5 Timing example: matches and flags
Counter period n Counter period n +1
Write to AS2
Clock
Prescaler
Selected
8
counter bus
6 6
4
1 1
Time
AS1 4h 0h
AS2 0h
BS1 6h
AS1 match
Match AS1 positive-edge detection
AS1 match positive-edge detection
Match AS1
AS1 match negative-edge detection negative-edge detection
BS1 match
Match BS1
BS1 match negative-edge detection negative-edge detection
Output pin
C n [EDPOL] = 0
Flag set event
Flag output and S n [FLAG]
Figure 328. Timing example: matches and flags
60.5.3.18.6 0% duty cycle
Timing example: 0% duty cycle illustrates the channel operation for a 0% duty cycle. The AS1 match positive-edge signal occurs
at the same time as the BS1 = 8h negative-edge signal. In this case, the AS1 match has precedence over the BS1 match, causing
the output pin to remain at the value of C n [EDPOL] and generating a 0% duty-cycle signal.

---

*Page 1890*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.18.7 Timing example: 0% duty cycle
Counter period n Counter period n +1
Write to AS2
Clock
Prescaler
Selected
8 8
counter bus
4
1 1
Time
AS1 4h 0h
AS2 0h
BS1 8h
AS1 match
Match AS1 positive-edge detection
AS1 match positive-edge detection
Match AS1
AS1 match negative-edge detection negative-edge detection
BS1 match
BS1 match negative-edge detection Match BS1 negative-edge detection
Output pin
C n [EDPOL] = 0
Flag set event
Flag output and S n [FLAG]
Figure 329. Timing example: 0% duty cycle
60.5.3.18.8 Active output disabled
Timing example: active output disabled illustrates the operation of the OPWMB mode with an emphasis on the assertion of the
output-disable signal. The output disable forces a transition in the output pin to the value of C n [EDPOL] . The deassertion of the
output-disable signal allows the output pin to transition at the following AS1 or BS1 match. The output disable does not modify
the behavior of S n [FLAG] . A delay of one system clock cycle exists between the assertion of the output-disable signal and the
transition of the output pin to C n [EDPOL] .

---

*Page 1891*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.18.9 Timing example: active output disabled
Counter period n Counter period n +1 Counter period n +2
Match AS1 Match BS1 Match AS1 Match BS1 Match BS1
Selected Write to AS2 Write to BS2 Write to AS2
counter bus
8h
6h
4h
2h
Due to BS1 match
1h Time
Counter period n -1
Output pin
Flag set event
Flag output and S n [FLAG]
Flag clear
Output disable
AS1 2h 4h 6h
AS2 2h 4h 6h
BS1 8h 6h
BS2 8h 6h
C n [EDPOL] = 0
Figure 330. Timing example: active output disabled
60.5.3.18.10 Changing from 100% to 0% duty cycle
Timing example: 100% to 0% duty cycle illustrates a waveform changing from 100% to 0% duty cycle. In this example, BS1 has
the same value as the period of the external selected timebase. If you write a value smaller than 8h to BS1, you cannot achieve
a 0% duty cycle by only changing AS1. Because BS1 matches have precedence over AS1 matches, the output pin transitions to
the complement of C n [EDPOL] at a BS1 match. For another example, if you write 9h to BS1, a BS1 match does not occur, and
the UC generates a 0% duty-cycle signal.

---

*Page 1892*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.18.11 Timing example: 100% to 0% duty cycle
Selected Counter Counter Counter Counter Counter Counter Counter Counter Counter
counter bus period 1 period 2 period 3 period 4 period 5 period 6 period 7 period 8 period 9
Output pin 100% 0%
AS1 8h 7h 6h 5h 4h 3h 2h 1h 0h
AS2 7h 6h 5h 4h 3h 2h 1h 0h
BS1
C n [EDPOL] = 0
Prescaler = 1
Figure 331. Timing example: 100% to 0% duty cycle
60.5.3.19 Output PWM with Trigger (OPWMT) mode
60.5.3.19.1 Overview
In this mode, the UC generates PWM pulses where the period does not change while the UC outputs the signal, but the duty cycle
changes. When the duty cycle changes, it must not create glitches. The mode supports multiple UCs executing in the same mode
and sharing a common timebase. Each UC can have a fixed PWM leading-edge position with respect to the other UCs. It can
also generate a trigger signal at any point in the period. eMIOS can output this trigger signal to initiate activity in other parts of the
chip—for example, to start ADC conversions.
You must select an external counter, driven in either MC Up or MCB Up mode (see Modulus Counter Buffered (MCB) mode ), from
one of the counter buses.
AS1 in OPWMT mode , AS2 in OPWMT mode , and BS1 and BS2 in OPWMT mode describe the configuration and behavior of the
individual shadow registers in this mode.
To account for the shift in the AS1-defined leading edge of the waveform, the BS1-defined trailing edge may roll over into the
next period. This means that a match against BS1 does not have to be qualified by a match in AS1. This also means that if you
incorrectly write a value less than AS1 to BS1, the output persists over a period boundary until the UC encounters the BS1 value.
This mode provides a buffered update of the trailing edge by updating BS1 with BS2 contents only at a match of AS1.
The positive edges of the AS1, BS1, and AS2 match signals drive the output pin and flag transitions. See Timing example: matches
and flags for details on positive-edge matches.
The UC sets the input capture flag ( S n [FLAG] ) only at an AS2 match. An AS1, BS1, or BS2 match has no effect on the flag.
When the UC enters OPWMT mode, it drives the complement of C n [EDPOL] on the output flip-flop.
Timing example: OPWMT illustrates the UC running in OPWMT mode with trigger-event generation and duty-cycle update after
the next-period update.

---

*Page 1893*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.19.2 Timing example: OPWMT
Match AS1 Match BS1 Match BS1
Match AS2 Match AS2
Write to AS1
and BS2 Write to BS2 Match AS1
Selected counter bus
11FFh
1000h
700h
500h
400h
0h Time
Output flip-flop
Flag output and S n [FLAG]
AS1 (read from A n ) 400h
BS1 (read from B n ) 0xxxxxxx 1000h 700h
BS2 (write to B n ) 1000h 700h
AS2 500h
Figure 332. Timing example: OPWMT
60.5.3.19.3 AS1 in OPWMT mode
AS1 defines the leading edge of the PWM pulse output, and therefore the beginning of the PWM period. Using this fact, you can
ensure that, when using a shared timebase, the leading edges of multiple UCs in OPWMT mode occur at specific times with
respect to the other UCs. You can also introduce a fixed offset for each UC, which is particularly useful in the generation of lighting
control signals. For this application, to reduce or eliminate noise generation, you can shift the PWM pulse of each UC with respect
to the selected timebase by adjusting the AS1 value for each channel. The value you write to AS1 must be within the range of the
selected timebase. Shadow registers (AS1, AS2, BS1, and BS2) loaded with 0h do not produce matches if the channel driving the
timebase is in MCB mode.
AS1 is not buffered, because the shift of a PWM channel must not change while the UC generates the PWM signal. If you modify
AS1, it updates immediately and one PWM pulse could be lost.
The UC compares AS1 to the selected timebase. When a match on AS1 occurs, the UC drives C n [EDPOL] on the output flip-flop.
When a match occurs on comparator B, the UC drives the complement of C n [EDPOL] on the output flip-flop.
60.5.3.19.4 AS2 in OPWMT mode
AS2 defines the generation of a trigger event within the PWM period. The value you write to AS2 must be within the range of the
selected timebase—otherwise the UC does not generate a trigger. A match on the comparator asserts the input-capture flag, but
the match has no effect on the PWM output signal generation. The typical setup to obtain a trigger with a flag is to enable DMA
and drive the DMA acknowledgement or DMA completion input high.
AS2 is not buffered, and therefore the UC updates it immediately. If the UC is running when you make a change, this could cause
either of the following:
• The loss of one trigger event
• The generation of two trigger events within the same period
You access AS2 by reading or writing ALTA n [ALTA] .
60.5.3.19.5 BS1 and BS2 in OPWMT mode
You access BS1 and BS2 using B n [B] :

---

*Page 1894*

Enhanced Modular IO Subsystem (eMIOS)
• When you read B n [B] , you read BS1.
• When you write to B n [B] , you write to BS2.
BS1 defines the trailing edge of the PWM pulse output, and therefore the duty cycle of the PWM signal. To synchronize the BS1
update with the PWM signal and to ensure a correct output pulse generation, the BS2→BS1 transfer occurs at every AS1 match.
This behavior is the same as in OPWM mode with next-period update.
Output Update Disable (OUDIS) affects transfers between BS2 and BS1 only.
60.5.3.19.6 Force match
At any time, Force Match A ( C n [FORCMA] ) and Force Match B ( C n [FORCMB] ) allow you to force the output flip-flop to the level
corresponding to a match on AS1 or BS1, respectively. Similarly to a BS1 match, Force Match B changes the internal counter
to 1h. The force-match operations do not set the input-capture flag ( S n [FLAG] ). Any match resulting from writing 1 to FORCMA
or FORCMB has priority over any simultaneous match regarding output pin transitions. BS2→BS1 transfer at an A match is not
inhibited by writing 1 simultaneously to both FORCMA or FORCMB. If you write 1 to both FORCMA and FORCMB simultaneously,
the output pin acquires the complement of C n [EDPOL] , as if AS1 and BS1 had the same value. FORCMA assertion causes the UC
to process the BS2→BS1 transfer as a regular A match comparison and not due to FORCMA assertion, regardless of FORCMB
assertion. If subsequent matches occur on comparators AS1 and B, the UC continues to generate PWM pulses, regardless of the
state of S n [FLAG] .
60.5.3.19.7 Duty cycle
To achieve 0% duty cycle, you must write the same value to AS1 and BS2. When a simultaneous match on comparators A and
B occurs, the UC drives the complement of C n [EDPOL] on the output flip-flop at every period.
To achieve 100% duty cycle, you must write a value to BS1 that is greater than the maximum value of the selected timebase.
The maximum counter value for the timebase is FF_FFFEh for a 24-bit counter and FFFEh for a 16-bit counter. When a match on
comparator AS1 occurs, the UC drives C n [EDPOL] on the output flip-flop at every period. The match at comparator A still triggers
the BS2→BS1 transfer.
Timing example: 0% duty cycle illustrates the UC running in OPWMT mode with trigger-event generation and a 0% duty cycle.
Timing example: 100% duty cycle illustrates the UC running in OPWMT mode with trigger-event generation and a 100%
duty cycle.

---

*Page 1895*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.19.8 Timing example: 0% duty cycle
Match AS1 Match BS1 Match BS1
Match AS2 Match AS2
Write to AS1
and BS2 Write to BS2 Match AS1
Selected counter bus
11FFh
1000h
500h
400h
0h Time
Output flip-flop
Flag output and S n [FLAG]
AS1 (read from A n ) 400h
BS1 (read from B n ) 0xxxxxxx 1000h 400h
BS2 (write to B n ) 1000h 400h
AS2 500h
Figure 333. Timing example: 0% duty cycle
60.5.3.19.9 Timing example: 100% duty cycle
Match AS1 Match BS1 Match BS1 does not occur
Match AS2 Match AS2
Write to AS1
and BS2 Write to BS2 Match AS1
Selected counter bus
11FFh
1000h
500h
400h
0h Time
Output flip-flop
Flag output and S n [FLAG]
AS1 (read from A n ) 400h
BS1 (read from B n ) 0xxxxxxx 1000h 1200h
BS2 (write to B n ) 1000h 1200h
AS2 500h
Figure 334. Timing example: 100% duty cycle
60.5.3.19.10 Output disable
As with other UC modes, OPWMT mode implements the output-disable function. When C n [ODIS] = 1, assertion of the
output-disable input causes the UC to drive C n [EDPOL] on the output. Other than the fixed output, the UC continues to operate
normally. When the output-disable signal deasserts, the UC returns to full normal operation.

---

*Page 1896*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.20 Programmable input filter (PIF)
60.5.3.20.1 Overview
The PIF specifies the minimum input pulse width, in clock cycles, that can pass through the filter. It supports bypass operation
( C n [IF] ), with the input signal synchronized before arriving at the digital filter, or from 2 to 16 clock cycles in powers of 2.
60.5.3.20.2 Counter
The PIF is a 5-bit programmable up counter. The selected clock source increments the up counter after the number of clock cycles
specified by the input filter ( C n [IF] 0.
The system clock synchronizes the input signal. When this signal changes state, the counter begins incrementing. As long as the
new signal state is stable, the counter continues incrementing. If the counter overflows, eMIOS validates the new signal value. In
this case, it transmits the value as a pulse edge to the edge detector. If the opposite edge appears on the signal before validation
(overflow), eMIOS resets the counter. At the next transition, the counter starts incrementing again. A glitch does not occur on the
edge detector for any pulse shorter than the full range of the masked counter.
Neither Freeze state nor deasserted GTBE disables the filter.
60.5.3.20.3 UC PIF logic
Cn[IF]
FCK 3 2 1 0
System clock
clk
Prescaled clock
5-bit up counter Filter out
Input pin
Synchronizer
Clock
Figure 335. UC PIF logic
60.5.3.20.4 Timing example: UC PIF at 4 cycles
Selected clock
Input pin
5-bit counter
Time
Filter out
Figure 336. Timing example: UC PIF at 4 cycles

---

*Page 1897*

Enhanced Modular IO Subsystem (eMIOS)
60.5.3.21 UC clock prescaler (CP)
eMIOS divides the GCP output signal by the UC CP ( C n [UCPRE] + 1) to generate a clock enable for the UC internal counter. See
Global clock prescaler (GCP) . You enable the UC CP by writing 1 to C n [UCPREN] . If you disable the CP (write 0 to the same field),
eMIOS stops the UC internal counter.
To ensure safe operation and avoid glitches, you must perform the following steps to change the UC CP:
1. Disable the GCP (write 0 to MCR[GPREN] ).
2. Disable the UC CP (write 0 to C n [UCPREN] ).
3. Specify the new prescaler value (write CP – 1 to C n [GPRE] .
4. Enable the UC CP (write 1 to C n [UCPREN] ).
5. Enable the GCP (write 1 to MCR[GPREN] ).

#### 60.5.4 Peripheral bus interface unit (BIU)

The BIU provides the interface between the IIB and the peripheral bus, which allows communication among all submodules and
the IP interface. The BIU supports 8-, 16-, and 32-bit accesses performed over a 32-bit data bus in a single clock cycle.

#### 60.5.5 Global clock prescaler (GCP)

eMIOS divides the system clock by the GCP ( MCR[GPRE] + 1) and routes the resulting prescaled clock output to the channel CPs.
You enable the GCP by writing 1 to MCR[GPREN] . If you disable the GCP (write 0 to the same field), eMIOS clears the prescaler
counter and stops the prescaler clock.
To ensure safe operation and avoid glitches, you must perform the following steps to change the GCP:
1. Disable the GCP (write 0 to MCR[GPREN] ).
2. Specify the new prescaler value (write GCP – 1 to MCR[GPRE] .
3. Enable the GCP (write 1 to MCR[GPREN] ).

#### 60.5.6 Freeze and chip Debug mode

60.5.6.1 Enter and exit Freeze state
When the chip is in Debug mode (the chip Debug signal is asserted), you can freeze individual UCs. For the effects of Freeze state
on eMIOS functions, see Freeze effects .
To put a channel in Freeze state, you must meet all of the following conditions:
• The chip must assert Debug.
• Prepare eMIOS for the Freeze state (write 1 to MCR[FRZ] ).
• Enable Freeze for each channel you want to freeze:
— Write 1 to C n [FREN] .
A channel exits Freeze state when any of the following conditions occur:
• The chip deasserts Debug.
• You take eMIOS out of the Freeze state (write 0 to MCR[FRZ] ).
• You disable Freeze for the channel:
— write 0 to C n [FREN] .

---

*Page 1898*

Enhanced Modular IO Subsystem (eMIOS)
60.5.6.2 Freeze effects
Function Effects of freeze
GCP No effect
UC
• Halts counter, capture, and compare operation.
• Freezes UC in its current state.
• Makes all registers accessible.
• In output modes, force match remains operational, allowing software to force the output to the
desired level.
• In input modes, the UC ignores input events.
• When eMIOS exits Freeze state then UC operations resume, but they may be inconsistent until
the UC reenters GPIO mode.
STAC No effect
BIU No effect

#### 60.6 Initialization information

When initializing eMIOS, take the following considerations into account.
For eMIOS behavior on reset, see Reset .
Before changing the UC operating mode, you must:
1. Put the UC in GPIO mode (write 0b or 1b to C n [MODE] ).
2. Write the correct values for the next operating mode to A n and B n .
3. Put the UC in the new operating mode (write the appropriate value to C n [MODE] ).
If you change a UC from one mode to another without performing this procedure, the first operation cycle of the selected timebase
can be random; that is, matches can occur in random time because you did not update A n and B n with correct values before the
timebase matches the previous contents of either register.
NOTE
To to avoid causing an unexpected interrupt or DMA request when changing from GPIO to MCB mode, you must
use the procedure described in Changing to MCB mode .
When you enable interrupts, any interrupt service routine must clear the flag before exiting.

#### 60.7 Application information

#### 60.7.1 Overview

All output operation modes can generate correlated output signals. You can disable updates of the output signals for each
UC ( OUDIS ).
To guarantee that incrementing of the internal counters of correlated channels occurs in the same clock cycle, you must configure
the internal CPs before enabling the GCP. If you configure the internal CPs after enabling the GCP, the internal counters may
increment in the same ratio, but at different clock cycles.
You should drive the Output Disable Input signals with the flag signals of some UCs running in SAIC mode. When an output disable
condition happens, the interrupt service routine must process the output channels before processing the channels running SAIC.
This sequence avoids glitches in the output pins.

---

*Page 1899*

Enhanced Modular IO Subsystem (eMIOS)

#### 60.7.2 Timebase generation

In the MC and OPWFM modes with internal clock sources, you can modify the internal counter rate by configuring the CP ratio.
Timebase with the fastest prescaler ratio illustrates a timebase with a prescaler ratio of one.
NOTE
The buffered modes, MCB and OPWFMB, behave differently.
If the prescaler ratio is greater than one or you select an external clock, the internal counter behaves in one of four different ways
depending on the channel mode:
• In MC mode with Clear on Match Start and External Clock: Timebase generation with clear on match start and external
clock .
• In MC mode with Clear on Match Start and Internal Clock source: Timebase generation with clear on match start and
internal clock .
• In MC mode with Clear on Match End: Timebase generation with clear on match end .
• In OPWFM mode: Timebase generation with clear on match start and internal clock . The internal counter clears at the
start of the match signal, skips the next prescaled clock edge, and then increments in the subsequent prescaled clock
edge.

#### 60.7.3 Timebase with the fastest prescaler ratio

C n [UCPRE] = 0b (Prescaler = 1)
System clock
Input event or prescaler clock enable = 1
See note 1
Internal counter 1 2 3 0 1 2 3 0 1 2 3 0 1 2 3 0 1 2 3 0 1 2 3
Match value = 3
Set flag event
S n [FLAG]
Clear flag event
Note 1: When a match occurs, eMIOS resets the internal counter
in the first cycle, which starts another period.
Figure 337. Timebase with fastest prescaler ratio

---

*Page 1900*

Enhanced Modular IO Subsystem (eMIOS)

#### 60.7.4 Timebase generation with clear on match start and external clock

System clock
Input event
Internal counter 1 2 3 0 1 2 3 0 1 2
See note 1
Match value = 3
Set flag event
S n [FLAG]
Clear flag event
Note 1: When a match occurs, eMIOS resets the internal counter in the first clock cycle.
The counter starts counting at the next edge of the prescaler clock enable.
Figure 338. Timebase generation with clear on match start and external clock

#### 60.7.5 Timebase generation with clear on match start and internal clock

Prescaler = 3 (C n [UCPRE] = 10b)
System clock
Prescaler enabled (C n [UCPREN] = 1b)
Internal counter 1 2 3 0 0 1 2 3 0 0
See note 1
Match value = 3
Set flag event
S n [FLAG]
Clear flag event
Note 1: When a match occurs, eMIOS clears the internal counter in the first clock cycle.
The counter starts counting again only after the second edge of the prescaled clock.
Figure 339. Timebase generation with clear on match start and internal clock

---

*Page 1901*

Enhanced Modular IO Subsystem (eMIOS)

#### 60.7.6 Timebase generation with clear on match end

System clock
Input event or prescaler clock enable
Internal counter 1 2 3 0 1 2 3 0
See note 1
Match value = 3
Set flag event
S n [FLAG]
Clear flag event
Note 1: The match occurs only when the input event or prescaler clock enable is active.
When the match occurs, eMIOS resets the internal counter immediately.
Figure 340. Timebase generation with clear on match end

#### 60.7.7 Coherent accesses

Reading A n [A] again in the same period as the last read of B n [B] results in incoherent results if the last read of B n [B] occurred
after a disabled BS2→BS1 transfer.

#### 60.7.8 Initializing channels and modes

Perform the following basic steps for output mode startup. This procedure assumes the channels are initially in GPIO mode:
1. Disable the GPC (write 0 to MCR[GPREN] ).
2. For each timebase UC:
a. Disable the CP (write 0 to C n [UCPREN] ).
b. Write an initial value to the internal counter ( CNT n [C] ).
c. Write initial values to A n [A] and B n [B] .
d. Configure the UC for MC or MCB Up mode (write the appropriate value to C n [MODE] ).
e. Select the CP ratio ( C n [UCPRE] ).
f. Enable the CP (write 1 to C n [UCPREN] ).
3. For each output UC:
a. Disable the CP (write 0 to C n [UCPREN] ).
b. Write initial values to A n [A] and B n [B] .
c. Select the timebase input ( C n [BSL] ).
d. Specify the output mode (write the appropriate value to C n [MODE] ).
e. Select the same CP ratio as the timebase UC ( C n [UCPRE] ).
f. Enable the CP (write 1 to C n [UCPREN] ).
4. Enable the GPC (write 1 to MCR[GPREN] ).
5. Enable GBTE (write 1 to MCR[GBTE] ).
The timebase channel and the output channel may be the same for some applications such as in OPWFM or OPWFMB mode,
or whenever the output channel drives the timebase itself.

---

*Page 1902*

Enhanced Modular IO Subsystem (eMIOS)
You may configure flags at any time.

#### 60.8 Memory maps and registers

#### 60.8.1 Overall address map organization

All address locations not explicitly mentioned in this table are reserved. When you access an absent register, absent channel, or
reserved address space, the transfer terminates with an error.
Table 341. Overall address map organization
Base address (hex) Description
0 Module Configuration (MCR)
4 Global Flag (GFLAG)
8 Output Update Disable (OUDIS)
C Disable Channel (UCDIS)
20 Channels 0–7
120 Channels 8–15
220 Channels 16–23

#### 60.8.2 UC memory overview

Table 342. UC memory overview
Offset from UC n base Description
address (hex)
0 UC A n (A0 - A23)
4 UC B n (B0 - B23)
8 UC Counter n (CNT0 - CNT23)
C UC Control n (C0 - C23)
10 UC Status n (S0 - S23)
14 Alternate Address n (ALTA0 - ALTA23)
18 UC Control 2 n (C2_0 - C2_23)
1C–1F Reserved

#### 60.8.3 AS1, AS2, BS1, and BS2 shadow registers

When you use eMIOS UC modes, you rely heavily on four shadow registers:
• AS1
• AS2
• BS1
• BS2
You use A n [A] , B n [B] , and ALTA n [ALTA] to access these shadow registers:

---

*Page 1903*

Enhanced Modular IO Subsystem (eMIOS)
• Each instance of A n [A] has a separate associated instance of AS1 and AS2. In some UC modes, you also use
ALTA n [ALTA] to access AS2.
• Each instance of B n [B] has a separate associated instance of BS1 and BS2.
See Relationship between A n , B n , ALTA n , and shadow registers .
Each UC mode uses the AS1-AS2 and BS1-BS2 shadow register pairs for various match and capture functions, as described in
Unified channels (UC) .
A reset clears all shadow registers.
Assignment of values for A n [A], B n [B], and ALTA n [ALTA] describes how you write to and read from the shadow registers for all
operation modes. Values not listed are reserved. For more information, see Unified channels (UC) .

#### 60.8.4 Relationship between A n , B n , ALTA n , and shadow registers

A n register array A shadow registers ALTA n register array B n register array B shadow registers
AS1 BS1
A0 ALTA0 B0
AS2 BS2
AS1 BS1
A1 ALTA1 B1
AS2 BS2
AS1 BS1
A2 ALTA2 B2
AS2 BS2
AS1 BS1
A3 ALTA3 B3
AS2 BS2
AS1 BS1
A4 ALTA4 B4
AS2 BS2
... ... ...
AS1 BS1
A n ALTA n B n
AS2 BS2
Figure 341. Relationship between A n , B n , ALTA n , and shadow registers

#### 60.8.5 Assignment of values for A n [A], B n [B], and ALTA n [ALTA]

This table describes how you write to and read from the following registers for all operation modes:
• UC A n (A0 - A23)
• UC B n (B0 - B23)
• Alternate Address n (ALTA0 - ALTA23)
Values not listed are reserved. For more information, see Unified channels (UC) .
Table 343. Assignment of values for A n [A], B n [B], and ALTA n [ALTA]
Register access
Operation mode A n B n ALTA n
Write Read Write Read Write Read
1 2
GPIO AS1 and AS2 AS1 BS1 and BS2 BS1 AS2 AS2
Table continues on the next page...

---

*Page 1904*

Enhanced Modular IO Subsystem (eMIOS)
Table 343. Assignment of values for A n[A], B n[B], and ALTA n[ALTA] (continued)
Register access
Operation mode A n Bn ALTAn
Write Read Write Read Write Read
3
SAIC — AS2 BS2 BS2 — —
3
SAOC AS2 AS1 BS2 BS2 — —
IPWM — AS2 — BS1 — —
IPM — AS2 — BS1 — —
DAOC AS2 AS1 BS2 BS1 — —
PEA AS1 AS2 — BS1 — —
3
PEC AS1 AS1 BS1 BS1 — AS2
WPTA AS1 AS1 BS1 BS1 — AS2
3
MC AS2 AS1 BS2 BS2 — —
OPWFM AS2 AS1 BS2 BS1 — —
OPWMC AS2 AS1 BS2 BS1 — —
OPWM AS2 AS1 BS2 BS1 — —
OPWMT AS1 AS1 BS2 BS1 AS2 AS2
3
MCB AS2 AS1 BS2 BS1 — —
OPWFMB AS2 AS1 BS2 BS1 — —
OPWMCB AS2 AS1 BS2 BS1 — —
OPWMB AS2 AS1 BS2 BS1 — —
1. This operation writes the same value to AS1 and AS2.
2. This operation writes the same value to BS1 and BS2.
3. These modes do not require UC B n (B0 - B23) , but you can still use it to access BS2.

#### 60.8.6 eMIOS register descriptions

All control registers are 32 bits wide. Data fields are 16 bits wide. There are 24 UCs, as mapped in Counter buses, channels, and
timebase sources .
60.8.6.1 eMIOS memory map
eMIOS_0 base address: 4008_8000h
eMIOS_1 base address: 4008_C000h
eMIOS_2 base address: 4009_0000h
Offset Register Access Reset value
Width
(In bits)
0h Module Configuration (MCR) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 1905*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
4h Global Flag (GFLAG) 32 R 0000_0000h
8h Output Update Disable (OUDIS) 32 RW 0000_0000h
Ch Disable Channel (UCDIS) 32 RW 0000_0000h
20h UC A 0 (A0) 32 RW 0000_0000h
24h UC B 0 (B0) 32 RW 0000_0000h
28h UC Counter 0 (CNT0) 32 RW 0000_0000h
2Ch UC Control 0 (C0) 32 RW 0000_0000h
30h UC Status 0 (S0) 32 RW See section
34h Alternate Address 0 (ALTA0) 32 RW 0000_0000h
38h UC Control 2 0 (C2_0) 32 RW 0000_0000h
40h UC A 1 (A1) 32 RW 0000_0000h
44h UC B 1 (B1) 32 RW 0000_0000h
48h UC Counter 1 (CNT1) 32 RW 0000_0000h
4Ch UC Control 1 (C1) 32 RW 0000_0000h
50h UC Status 1 (S1) 32 RW See section
54h Alternate Address 1 (ALTA1) 32 RW 0000_0000h
58h UC Control 2 1 (C2_1) 32 RW 0000_0000h
60h UC A 2 (A2) 32 RW 0000_0000h
64h UC B 2 (B2) 32 RW 0000_0000h
68h UC Counter 2 (CNT2) 32 RW 0000_0000h
6Ch UC Control 2 (C2) 32 RW 0000_0000h
70h UC Status 2 (S2) 32 RW See section
74h Alternate Address 2 (ALTA2) 32 RW 0000_0000h
78h UC Control 2 2 (C2_2) 32 RW 0000_0000h
80h UC A 3 (A3) 32 RW 0000_0000h
84h UC B 3 (B3) 32 RW 0000_0000h
88h UC Counter 3 (CNT3) 32 RW 0000_0000h
8Ch UC Control 3 (C3) 32 RW 0000_0000h
90h UC Status 3 (S3) 32 RW See section
94h Alternate Address 3 (ALTA3) 32 RW 0000_0000h
98h UC Control 2 3 (C2_3) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 1906*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
A0h UC A 4 (A4) 32 RW 0000_0000h
A4h UC B 4 (B4) 32 RW 0000_0000h
A8h UC Counter 4 (CNT4) 32 RW 0000_0000h
ACh UC Control 4 (C4) 32 RW 0000_0000h
B0h UC Status 4 (S4) 32 RW See section
B4h Alternate Address 4 (ALTA4) 32 RW 0000_0000h
B8h UC Control 2 4 (C2_4) 32 RW 0000_0000h
C0h UC A 5 (A5) 32 RW 0000_0000h
C4h UC B 5 (B5) 32 RW 0000_0000h
C8h UC Counter 5 (CNT5) 32 RW 0000_0000h
CCh UC Control 5 (C5) 32 RW 0000_0000h
D0h UC Status 5 (S5) 32 RW See section
D4h Alternate Address 5 (ALTA5) 32 RW 0000_0000h
D8h UC Control 2 5 (C2_5) 32 RW 0000_0000h
E0h UC A 6 (A6) 32 RW 0000_0000h
E4h UC B 6 (B6) 32 RW 0000_0000h
E8h UC Counter 6 (CNT6) 32 RW 0000_0000h
ECh UC Control 6 (C6) 32 RW 0000_0000h
F0h UC Status 6 (S6) 32 RW See section
F4h Alternate Address 6 (ALTA6) 32 RW 0000_0000h
F8h UC Control 2 6 (C2_6) 32 RW 0000_0000h
100h UC A 7 (A7) 32 RW 0000_0000h
104h UC B 7 (B7) 32 RW 0000_0000h
108h UC Counter 7 (CNT7) 32 RW 0000_0000h
10Ch UC Control 7 (C7) 32 RW 0000_0000h
110h UC Status 7 (S7) 32 RW See section
114h Alternate Address 7 (ALTA7) 32 RW 0000_0000h
118h UC Control 2 7 (C2_7) 32 RW 0000_0000h
120h UC A 8 (A8) 32 RW 0000_0000h
124h UC B 8 (B8) 32 RW 0000_0000h
128h UC Counter 8 (CNT8) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 1907*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
12Ch UC Control 8 (C8) 32 RW 0000_0000h
130h UC Status 8 (S8) 32 RW See section
134h Alternate Address 8 (ALTA8) 32 RW 0000_0000h
138h UC Control 2 8 (C2_8) 32 RW 0000_0000h
140h UC A 9 (A9) 32 RW 0000_0000h
144h UC B 9 (B9) 32 RW 0000_0000h
14Ch UC Control 9 (C9) 32 RW 0000_0000h
150h UC Status 9 (S9) 32 RW See section
154h Alternate Address 9 (ALTA9) 32 RW 0000_0000h
158h UC Control 2 9 (C2_9) 32 RW 0000_0000h
160h UC A 10 (A10) 32 RW 0000_0000h
164h UC B 10 (B10) 32 RW 0000_0000h
16Ch UC Control 10 (C10) 32 RW 0000_0000h
170h UC Status 10 (S10) 32 RW See section
174h Alternate Address 10 (ALTA10) 32 RW 0000_0000h
178h UC Control 2 10 (C2_10) 32 RW 0000_0000h
180h UC A 11 (A11) 32 RW 0000_0000h
184h UC B 11 (B11) 32 RW 0000_0000h
18Ch UC Control 11 (C11) 32 RW 0000_0000h
190h UC Status 11 (S11) 32 RW See section
194h Alternate Address 11 (ALTA11) 32 RW 0000_0000h
198h UC Control 2 11 (C2_11) 32 RW 0000_0000h
1A0h UC A 12 (A12) 32 RW 0000_0000h
1A4h UC B 12 (B12) 32 RW 0000_0000h
1ACh UC Control 12 (C12) 32 RW 0000_0000h
1B0h UC Status 12 (S12) 32 RW See section
1B4h Alternate Address 12 (ALTA12) 32 RW 0000_0000h
1B8h UC Control 2 12 (C2_12) 32 RW 0000_0000h
1C0h UC A 13 (A13) 32 RW 0000_0000h
1C4h UC B 13 (B13) 32 RW 0000_0000h
1CCh UC Control 13 (C13) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 1908*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
1D0h UC Status 13 (S13) 32 RW See section
1D4h Alternate Address 13 (ALTA13) 32 RW 0000_0000h
1D8h UC Control 2 13 (C2_13) 32 RW 0000_0000h
1E0h UC A 14 (A14) 32 RW 0000_0000h
1E4h UC B 14 (B14) 32 RW 0000_0000h
1ECh UC Control 14 (C14) 32 RW 0000_0000h
1F0h UC Status 14 (S14) 32 RW See section
1F4h Alternate Address 14 (ALTA14) 32 RW 0000_0000h
1F8h UC Control 2 14 (C2_14) 32 RW 0000_0000h
200h UC A 15 (A15) 32 RW 0000_0000h
204h UC B 15 (B15) 32 RW 0000_0000h
20Ch UC Control 15 (C15) 32 RW 0000_0000h
210h UC Status 15 (S15) 32 RW See section
214h Alternate Address 15 (ALTA15) 32 RW 0000_0000h
218h UC Control 2 15 (C2_15) 32 RW 0000_0000h
220h UC A 16 (A16) 32 RW 0000_0000h
224h UC B 16 (B16) 32 RW 0000_0000h
228h UC Counter 16 (CNT16) 32 RW 0000_0000h
22Ch UC Control 16 (C16) 32 RW 0000_0000h
230h UC Status 16 (S16) 32 RW See section
234h Alternate Address 16 (ALTA16) 32 RW 0000_0000h
238h UC Control 2 16 (C2_16) 32 RW 0000_0000h
240h UC A 17 (A17) 32 RW 0000_0000h
244h UC B 17 (B17) 32 RW 0000_0000h
24Ch UC Control 17 (C17) 32 RW 0000_0000h
250h UC Status 17 (S17) 32 RW See section
254h Alternate Address 17 (ALTA17) 32 RW 0000_0000h
258h UC Control 2 17 (C2_17) 32 RW 0000_0000h
260h UC A 18 (A18) 32 RW 0000_0000h
264h UC B 18 (B18) 32 RW 0000_0000h
26Ch UC Control 18 (C18) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 1909*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
270h UC Status 18 (S18) 32 RW See section
274h Alternate Address 18 (ALTA18) 32 RW 0000_0000h
278h UC Control 2 18 (C2_18) 32 RW 0000_0000h
280h UC A 19 (A19) 32 RW 0000_0000h
284h UC B 19 (B19) 32 RW 0000_0000h
28Ch UC Control 19 (C19) 32 RW 0000_0000h
290h UC Status 19 (S19) 32 RW See section
294h Alternate Address 19 (ALTA19) 32 RW 0000_0000h
298h UC Control 2 19 (C2_19) 32 RW 0000_0000h
2A0h UC A 20 (A20) 32 RW 0000_0000h
2A4h UC B 20 (B20) 32 RW 0000_0000h
2ACh UC Control 20 (C20) 32 RW 0000_0000h
2B0h UC Status 20 (S20) 32 RW See section
2B4h Alternate Address 20 (ALTA20) 32 RW 0000_0000h
2B8h UC Control 2 20 (C2_20) 32 RW 0000_0000h
2C0h UC A 21 (A21) 32 RW 0000_0000h
2C4h UC B 21 (B21) 32 RW 0000_0000h
2CCh UC Control 21 (C21) 32 RW 0000_0000h
2D0h UC Status 21 (S21) 32 RW See section
2D4h Alternate Address 21 (ALTA21) 32 RW 0000_0000h
2D8h UC Control 2 21 (C2_21) 32 RW 0000_0000h
2E0h UC A 22 (A22) 32 RW 0000_0000h
2E4h UC B 22 (B22) 32 RW 0000_0000h
2E8h UC Counter 22 (CNT22) 32 RW 0000_0000h
2ECh UC Control 22 (C22) 32 RW 0000_0000h
2F0h UC Status 22 (S22) 32 RW See section
2F4h Alternate Address 22 (ALTA22) 32 RW 0000_0000h
2F8h UC Control 2 22 (C2_22) 32 RW 0000_0000h
300h UC A 23 (A23) 32 RW 0000_0000h
304h UC B 23 (B23) 32 RW 0000_0000h
308h UC Counter 23 (CNT23) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 1910*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
30Ch UC Control 23 (C23) 32 RW 0000_0000h
310h UC Status 23 (S23) 32 RW See section
314h Alternate Address 23 (ALTA23) 32 RW 0000_0000h
318h UC Control 2 23 (C2_23) 32 RW 0000_0000h
60.8.6.2 Module Configuration (MCR)
Offset
Register Offset
MCR 0h
Function
Configures eMIOS operation.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
Reserv
R 0
Reserv GPRE
ed
MDIS FRZ GTBE
ed N
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
GPRE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Reserved
— Write only the reset value to this field.
30 Module Disable
MDIS Disables eMIOS by stopping the clock.
Table continues on the next page...

---

*Page 1911*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Field Function
When you disable eMIOS, its clock stops, it consumes less power, and you can access only the
following registers:
• Module Configuration (MCR)
• Output Update Disable (OUDIS)
• Disable Channel (UCDIS)
0b - Enable
1b - Disable
29 Freeze
FRZ Prepares eMIOS to enter Freeze state (see Freeze and chip Debug mode ). eMIOS does not enter Freeze
state until both of the following conditions occur:
• The chip asserts Debug mode.
• You enable Freeze for each UC (write 1 to C n [FREN] ).
A channel remains frozen until one or more of the following occur:
• You force eMIOS to exit the Freeze state (write 0 to FRZ).
• The chip deasserts Debug mode.
• You disable Freeze for that UC (write 0 to C n [FREN] ).
0b - Exit Freeze state
1b - Enter Freeze state
28 Global Timebase Enable
GTBE Asserts the GTBE_OUT signal. If GTBE_OUT is connected to the GTBE_IN input of one or more eMIOS
instances, asserting the signal turns on the internal counters of those eMIOS instances simultaneously.
NOTE
The GTBE_IN input enables (asserted) or disables (deasserted) the internal counters.
0b - Deassert GTBE_OUT
1b - Assert GTBE_OUT
Reserved
27
—
26 Global Prescaler Enable
GPREN Enables the global prescaler counter. Disabling the global prescaler clears the prescaler counter and stops
the prescaler clock.
0b - Disable
1b - Enable
Reserved
25-16
Table continues on the next page...

---

*Page 1912*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Field Function
—
15-8 Global Prescaler
GPRE Specifies the global clock prescaler. Write the desired clock divide ratio minus 1. For example, for a divide
ratio of 8, write 111b. The prescaler can range from 1 (0b) to 256 (11111111b).
Reserved
7-0
—
60.8.6.3 Global Flag (GFLAG)
Offset
Register Offset
GFLAG 4h
Function
Groups the flag fields from all channels into one register to improve interrupt handling.
For UCs, each field mirrors the corresponding channel flag ( S n [FLAG] ).
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0 0 0 0 0 F23 F22 F21 F20 F19 F18 F17 F16
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R F15 F14 F13 F12 F11 F10 F9 F8 F7 F6 F5 F4 F3 F2 F1 F0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
Reserved
30
Table continues on the next page...

---

*Page 1913*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Field Function
—
Reserved
29
—
Reserved
28
—
Reserved
27
—
Reserved
26
—
Reserved
25
—
Reserved
24
—
Mirror of UC 23 FLAG
23
F23
Mirror of UC 22 FLAG
22
F22
Mirror of UC 21 FLAG
21
F21
Mirror of UC 20 FLAG
20
F20
Mirror of UC 19 FLAG
19
F19
Mirror of UC 18 FLAG
18
F18
Mirror of UC 17 FLAG
17
F17
Mirror of UC 16 FLAG
16
F16
Table continues on the next page...

---

*Page 1914*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Field Function
Mirror of UC 15 FLAG
15
F15
Mirror of UC 14 FLAG
14
F14
Mirror of UC 13 FLAG
13
F13
Mirror of UC 12 FLAG
12
F12
Mirror of UC 11 FLAG
11
F11
Mirror of UC 10 FLAG
10
F10
Mirror of UC 9 FLAG
9
F9
Mirror of UC 8 FLAG
8
F8
Mirror of UC 7 FLAG
7
F7
Mirror of UC 6 FLAG
6
F6
Mirror of UC 5 FLAG
5
F5
Mirror of UC 4 FLAG
4
F4
Mirror of UC 3 FLAG
3
F3
Mirror of UC 2 FLAG
2
F2
Mirror of UC 1 FLAG
1
Table continues on the next page...

---

*Page 1915*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Field Function
F1
Mirror of UC 0 FLAG
0
F0
60.8.6.4 Output Update Disable (OUDIS)
Offset
Register Offset
OUDIS 8h
Function
Disables transfers from AS2 to AS1 and BS2 to BS1 on a per-channel basis. This applies to any channel operating in MC , MCB ,
or any output mode that writes to AS2 and BS2.
When a field is 0, transfers on that channel occur immediately or in the next period, depending on the operation mode. Unless that
mode's description states otherwise, the transfer occurs immediately.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0 0 0 0 0
OU23 OU22 OU21 OU20 OU19 OU18 OU17 OU16
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
OU15 OU14 OU13 OU12 OU11 OU10 OU9 OU8 OU7 OU6 OU5 OU4 OU3 OU2 OU1 OU0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
Reserved
30
—
Table continues on the next page...

---

*Page 1916*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Field Function
Reserved
29
—
Reserved
28
—
Reserved
27
—
Reserved
26
—
Reserved
25
—
Reserved
24
—
Channel 23 Output Update Disable
23
0b - Enable
OU23
1b - Disable
Channel 22 Output Update Disable
22
0b - Enable
OU22
1b - Disable
Channel 21 Output Update Disable
21
0b - Enable
OU21
1b - Disable
Channel 20 Output Update Disable
20
0b - Enable
OU20
1b - Disable
Channel 19 Output Update Disable
19
0b - Enable
OU19
1b - Disable
Channel 18 Output Update Disable
18
0b - Enable
OU18
1b - Disable
Table continues on the next page...

---

*Page 1917*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Field Function
Channel 17 Output Update Disable
17
0b - Enable
OU17
1b - Disable
Channel 16 Output Update Disable
16
0b - Enable
OU16
1b - Disable
Channel 15 Output Update Disable
15
0b - Enable
OU15
1b - Disable
Channel 14 Output Update Disable
14
0b - Enable
OU14
1b - Disable
Channel 13 Output Update Disable
13
0b - Enable
OU13
1b - Disable
Channel 12 Output Update Disable
12
0b - Enable
OU12
1b - Disable
Channel 11 Output Update Disable
11
0b - Enable
OU11
1b - Disable
Channel 10 Output Update Disable
10
0b - Enable
OU10
1b - Disable
Channel 9 Output Update Disable
9
0b - Enable
OU9
1b - Disable
Channel 8 Output Update Disable
8
0b - Enable
OU8
1b - Disable
Channel 7 Output Update Disable
7
OU7
Table continues on the next page...

---

*Page 1918*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Field Function
0b - Enable
1b - Disable
Channel 6 Output Update Disable
6
0b - Enable
OU6
1b - Disable
Channel 5 Output Update Disable
5
0b - Enable
OU5
1b - Disable
Channel 4 Output Update Disable
4
0b - Enable
OU4
1b - Disable
Channel 3 Output Update Disable
3
0b - Enable
OU3
1b - Disable
Channel 2 Output Update Disable
2
0b - Enable
OU2
1b - Disable
Channel 1 Output Update Disable
1
0b - Enable
OU1
1b - Disable
Channel 0 Output Update Disable
0
0b - Enable
OU0
1b - Disable
60.8.6.5 Disable Channel (UCDIS)
Offset
Register Offset
UCDIS Ch
Function
Allows you to disable a UC by stopping its clock. Each field controls the clock for the corresponding UC.

---

*Page 1919*

Enhanced Modular IO Subsystem (eMIOS)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0 0 0 0 0
UCDIS UCDIS UCDIS UCDIS UCDIS UCDIS UCDIS UCDIS
23 22 21 20 19 18 17 16
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
UCDIS UCDIS UCDIS UCDIS UCDIS UCDIS UCDIS UCDIS UCDIS UCDIS UCDIS UCDIS UCDIS UCDIS UCDIS UCDIS
15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
Reserved
30
—
Reserved
29
—
Reserved
28
—
Reserved
27
—
Reserved
26
—
Reserved
25
—
Reserved
24
—
Disable UC 23
23
0b - Enable
UCDIS23
1b - Disable
Disable UC 22
22
Table continues on the next page...

---

*Page 1920*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Field Function
UCDIS22 0b - Enable
1b - Disable
Disable UC 21
21
0b - Enable
UCDIS21
1b - Disable
Disable UC 20
20
0b - Enable
UCDIS20
1b - Disable
Disable UC 19
19
0b - Enable
UCDIS19
1b - Disable
Disable UC 18
18
0b - Enable
UCDIS18
1b - Disable
Disable UC 17
17
0b - Enable
UCDIS17
1b - Disable
Disable UC 16
16
0b - Enable
UCDIS16
1b - Disable
Disable UC 15
15
0b - Enable
UCDIS15
1b - Disable
Disable UC 14
14
0b - Enable
UCDIS14
1b - Disable
Disable UC 13
13
0b - Enable
UCDIS13
1b - Disable
Disable UC 12
12
0b - Enable
UCDIS12
1b - Disable
Table continues on the next page...

---

*Page 1921*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Field Function
Disable UC 11
11
0b - Enable
UCDIS11
1b - Disable
Disable UC 10
10
0b - Enable
UCDIS10
1b - Disable
Disable UC 9
9
0b - Enable
UCDIS9
1b - Disable
Disable UC 8
8
0b - Enable
UCDIS8
1b - Disable
Disable UC 7
7
0b - Enable
UCDIS7
1b - Disable
Disable UC 6
6
0b - Enable
UCDIS6
1b - Disable
Disable UC 5
5
0b - Enable
UCDIS5
1b - Disable
Disable UC 4
4
0b - Enable
UCDIS4
1b - Disable
Disable UC 3
3
0b - Enable
UCDIS3
1b - Disable
Disable UC 2
2
0b - Enable
UCDIS2
1b - Disable
Disable UC 1
1
UCDIS1
Table continues on the next page...

---

*Page 1922*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Field Function
0b - Enable
1b - Disable
Disable UC 0
0
0b - Enable
UCDIS0
1b - Disable
60.8.6.6 UC A n (A0 - A23)
Offset
For n = 0 to 23:
Register Offset
An 20h + (n × 20h)
Function
Accesses the AS1 and AS2 shadow registers for each UC, depending on the selected UC mode. See AS1, AS2, BS1, and BS2
shadow registers .
For information about how you write to and read from A n , B n , and ALTA n for all UC modes, see Assignment of values for A n [A],
B n [B], and ALTA n [ALTA] .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
RISE_
R 0
FA ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
A
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Rising and falling edge detection
RISE_FALL Indicates rising and falling edge in SAIC sub-mode.
Table continues on the next page...

---

*Page 1923*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Field Function
Reserved
30-16
—
15-0 A
A See the register description.
60.8.6.7 UC B n (B0 - B23)
Offset
For n = 0 to 23:
Register Offset
Bn 24h + (n × 20h)
Function
Accesses the BS1 and BS2 shadow registers for each UC, depending on the selected UC mode. See AS1, AS2, BS1, and BS2
shadow registers .
For information about how you write to and read from A n , B n , and ALTA n for all UC modes, see Assignment of values for A n [A],
B n [B], and ALTA n [ALTA] .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
B
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-16
—
15-0 B
Table continues on the next page...

---

*Page 1924*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Field Function
See the register description.
B
60.8.6.8 UC Counter n (CNT0 - CNT23)
Offset
Register Offset
CNT0 28h
CNT1 48h
CNT2 68h
CNT3 88h
CNT4 A8h
CNT5 C8h
CNT6 E8h
CNT7 108h
CNT8 128h
CNT16 228h
CNT22 2E8h
CNT23 308h
Function
Indicates the value of the UC internal counter.
When eMIOS is in GPIO mode or the UC is frozen, this register is read-write. For all other modes, this register is read-only. When
entering some UC modes, eMIOS automatically clears this register. See Unified channels (UC) for details.
The following modes use the UC counter:
• Output Pulse Width and Frequency Modulation Buffered (OPWFMB) mode
• Center Aligned Output PWM with Dead Time Insertion Buffered (OPWMCB) mode
• Pulse Edge Counting (PEC) mode
• Modulus Counter (MC) mode
• Modulus Counter Buffered (MCB) mode
All other modes not in this list do not use the internal counter.
NOTE
Each module instance supports a different number of registers.

---

*Page 1925*

Enhanced Modular IO Subsystem (eMIOS)
Instance Register supported Register not supported
eMIOS_0 CNT0–CNT8 —
CNT16
CNT22–CNT23
eMIOS_1 CNT0 CNT1–CNT7
CNT8
CNT16
CNT22–CNT23
eMIOS_2 CNT0 CNT1–CNT7
CNT8
CNT16
CNT22–CNT23
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset See Register reset values .
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
C
W
Reset See Register reset values .
Register reset values
Register Reset value
CNT0 eMIOS_0–eMIOS_2: 0000_0000h
CNT1–CNT7 0000_0000h
CNT8–CNT23 eMIOS_0–eMIOS_2: 0000_0000h
CNT9–CNT15 —
CNT17–CNT21 —
Fields
Field Function
Reserved
31-16
—
Table continues on the next page...

---

*Page 1926*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Field Function
15-0 Internal Counter Value
C Indicates the value of the internal counter.
60.8.6.9 UC Control n (C0 - C23)
Offset
For n = 0 to 23:
Register Offset
Cn 2Ch + (n × 20h)
Function
Controls UC operation.
Table 344. MODE field values
UC mode MODE (binary)
GPIO (input) 000_0000
GPIO (output) 000_0001
SAIC 000_0010
SAIC with edge capturing 100_0010
SAOC 000_0011
IPWM 000_0100
IPM 000_0101
DAOC (with FLAG = 1 on B match) 000_0110
DAOC (with FLAG = 1 on A and B match) 000_0111
PEC (continuous) 000_1010
PEC (single shot) 000_1011
Reserved 000_1111
1
MC (up counter with clear on match start) 001_000p
1
MC (up counter with clear on match end) 001_001p
1
MC (up/down counter) 001_01pp
Reserved 010_0100–010_0101
OPWMT 010_0110
Reserved 010_0111–100_1111
Table continues on the next page...

---

*Page 1927*

Enhanced Modular IO Subsystem (eMIOS)
Table 344. MODE field values (continued)
UC mode MODE (binary)
1
MCB (up counter) 101_000p
Reserved 101_001p
1
MCB (up or down counter) 101_01pp
1
OPWFMB 101_10p0
Reserved 101_10p1
1
OPWMCB (with trailing edge dead time) 101_11p0
1
OPWMCB (with leading edge dead time) 101_11p1
1
OPWMB 110_00p0
Reserved 110_0001–111_1111
1. p = Adjust parameters for the mode of operation. See Unified channels (UC) for details.
Table 345. Edge Polarity (EDPOL) settings
For these modes: EDPOL does this: and EDPOL values mean:
Input Selects which edge triggers the internal counter, an
0b - Trigger on a falling edge
input capture, or the input capture flag. If EDPOL
1b - Trigger on a rising edge
does not appear in the UC mode description, it has
no effect on UC operation.
Output Selects the logic level on the output pin.
0b - A match on comparator A deasserts the
output flip-flop, while a match on comparator
B sets it
1b - A match on comparator A asserts the
output flip-flop, while a match on comparator
B clears it
Table 346. Edge Select (EDSEL) settings
For these modes: EDSEL does this: and EDSEL values mean:
Input Selects whether the internal counter is triggered by
0b - Single-edge triggering on edge
both edges of a pulse or by a single edge as defined
specified in EDPOL
by EDPOL . If EDSEL does not appear in the UC
1b - Both-edges triggering
mode description, it has no effect on UC operation.
GPIO in Selects whether an edge asserts the input capture
0b - Sets S n [FLAG] on the edge specified
flag ( S n [FLAG] ).
in EDPOL
1b - Does not set S n [FLAG]
SAOC Selects the behavior of the output flip-flop at each
0b - Drive the EDPOL value on the
match.
output flip-flop
1b - Toggle output flip-flop

---

*Page 1928*

Enhanced Modular IO Subsystem (eMIOS)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
UCPR
FREN ODIS ODISSL UCPRE DMA IF FCK FEN
EN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
EDSE EDPO
BSL MODE
FORC FORC
L L
W
MA MB
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Freeze Enable
FREN Enables putting the UC in Freeze state. See Freeze and chip Debug mode .
0b - Disable
1b - Enable
30 Output Disable
ODIS Disables the output pin when running any of the output modes except GPIO.
When you assert ODIS:
• If the selected Output Disable Input signal asserts, the output pin reflects:
— For OPWFMB and OPWMB modes, EDPOL (see Output Pulse Width and Frequency
Modulation Buffered (OPWFMB) mode and Output PWM Buffered (OPWMB) mode ).
— For all other modes, the complement of EDPOL, but the UC continues to operate normally—it
continues to generate flags and matches.
• If the selected Output Disable Input signal deasserts, the output pin operates normally.
0b - Enables output pin (the output pin operates normally)
1b - Disables output pin
29-28 Output Disable Select
ODISSL Selects one of four output disable input signals.
00b - 0
01b - 1
10b - 2
11b - 3
27-26 Prescaler
Selects the clock divider value for the UC internal prescaler.
Table continues on the next page...

---

*Page 1929*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Field Function
UCPRE
NOTE
The two least-significant bits of C2_ n [UCEXTPRE] mirror UCPRE. Any write to UCPRE also
affects UCEXTPRE.
00b - 1
01b - 2
10b - 3
11b - 4
25 Prescaler Enable
UCPREN Enables the prescaler counter.
0b - Disable (no clock)
1b - Enable
24 Direct Memory Access
DMA Selects whether the input capture flag ( S n [FLAG] ) or overrun ( S n [OVR] ) triggers an interrupt or
DMA request.
0b - Interrupt request
1b - DMA request
Reserved
23
—
22-19 Input Filter
IF Selects the minimum input pulse width, in filter clock cycles, that can pass through the input filter. This field
does not apply to output modes.
The filter latency—the difference in time between the input and the response—is three clock edges.
0000b - Bypassed; the input signal is synchronized before arriving at the digital filter
0001b - 2 cycles
0010b - 4 cycles
0100b - 8 cycles
1000b - 16 cycles
All other values are reserved.
18 Filter Clock Select
FCK Selects the clock source for the programmable input filter.
0b - Prescaled clock
1b - eMIOS module clock
Table continues on the next page...

---

*Page 1930*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Field Function
17 Flag Enable
FEN Enables the input capture flag ( S n [FLAG] ) to generate an interrupt request or a DMA request, as selected
in DMA .
0b - Disable
1b - Enable
Reserved
16-14
—
13 Force Match A
FORCMA For output modes, asserting Force Match A causes the comparator to report a successful comparison on
comparator A regardless of whether a comparison actually occurred. It does not set the input capture flag
( S n [FLAG] ). This field always reads 0. This bit is meaningful for every output operation mode that uses
comparator A. Otherwise it has no effect.
0b - No effect
1b - Force a match at comparator A
12 Force Match B
FORCMB For output modes, asserting Force Match B causes the comparator to report a successful comparison on
comparator B regardless of whether a comparison actually occurred. It does not set the input capture flag
( S n [FLAG] does not become 1). This field always reads 0.
This field applies only if the UC is in one of the following output modes.
• Single Action Output Capture (SAOC) mode
• Double Action Output Compare (DAOC) mode
• Output Pulse Width and Frequency Modulation Buffered (OPWFMB) mode
• Center Aligned Output PWM with Dead Time Insertion Buffered (OPWMCB) mode
• Output PWM Buffered (OPWMB) mode
• Output PWM with Trigger (OPWMT) mode
This field has no effect in all other modes.
0b - No effect
1b - Force a match at comparator B
Reserved
11
—
10-9 Bus Select
BSL Selects one of the counter buses or the internal counter for the UC.
Not all chips support all counter buses. See the chip-specific eMIOS information for details.
00b -
Table continues on the next page...

---

*Page 1931*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Field Function
• Channels 0-22: counter bus A
• Channel 23: reserved
01b -
• Channels 1-7: counter bus B
• Channels 9-15: counter bus C
• Channels 17-23: counter bus D
• Channels 25-31: counter bus E
• Channels 0, 8, 16, 24: reserved
10b -
• Channels 0-21, 23: counter bus F
• Channel 22: reserved
11b - Internal counter for all channels
8 Edge Selection
EDSEL Controls several functions as shown in Table 346 .
7 Edge Polarity
EDPOL Controls several functions as shown in Table 345 .
6-0 Mode Selection
MODE Selects the UC mode as shown in Table 344 .
NOTE
If you write a reserved value to this field, the results are unpredictable.
When you change the value of MODE, you must first enter GPIO mode to reset the UC's
internal functions. Failure to do this can result in invalid and unexpected output compare or
input capture results or incorrectly set input capture flags ( S n [FLAG] ).
60.8.6.10 UC Status n (S0 - S23)
Offset
For n = 0 to 23:
Register Offset
Sn 30h + (n × 20h)
Function
Indicates the status of the UC input-output signals and the overflow condition of the internal counter.

---

*Page 1932*

Enhanced Modular IO Subsystem (eMIOS)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R OVR 0
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
UCOU
R OVFL 0 UCIN FLAG
T
W W1C W1C
1
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 u 0 0
1. Reset value is undefined.
Fields
Field Function
31 Overrun
OVR Indicates that FLAG generation occurred when FLAG was already 1.
Return OVR to 0 by writing 1 to either OVR or FLAG.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - No overrun
1b - Overrun
When writing
0b - No overrun
1b - Return OVR to 0
Reserved
30-16
—
15 Overflow
OVFL Indicates that an overflow has occurred in the internal counter.
NOTE
This field is not supported in every instance. The following table includes only
supported registers.

---

*Page 1933*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Field Function
Instance Field supported in Field not supported in
eMIOS_0 S0–S8 S9–S15
S16 S17–S21
S22–S23
eMIOS_1 S0 S1–S7
S8 S9–S15
S16 S17–S21
S22–S23
eMIOS_2 S0 S1–S7
S8 S9–S15
S16 S17–S21
S22–S23
NOTE
This field behaves differently for register reads and writes.
When reading
0b - No overflow
1b - Overflow
When writing
0b - No overflow
1b - Return OVFL to 0
Reserved
14-3
—
2 UC Input Pin
UCIN Indicates the input pin state after the input pin has been filtered and synchronized.
0b - Negated
1b - Asserted
1 UC Output Pin
UCOUT Indicates the output pin state.
0b - Negated
1b - Asserted
0 Flag
FLAG Indicates that an input capture or a match event in the comparators has occurred.
Table continues on the next page...

---

*Page 1934*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Field Function
NOTE
This field behaves differently for register reads and writes.
When reading
0b - No event
1b - Event has occurred
When writing
0b - No event
1b - Clear the flag (FLAG returns to 0)
60.8.6.11 Alternate Address n (ALTA0 - ALTA23)
Offset
For n = 0 to 23:
Register Offset
ALTAn 34h + (n × 20h)
Function
Provides an alternate address to access AS2 shadow registers in these restricted modes:
• General-Purpose Input and Output (GPIO) mode
• Pulse Edge Counting (PEC) mode
• Output PWM with Trigger (OPWMT) mode
In these modes, you can use A n [A] together with ALTA n [ALTA] to access both AS1 and AS2. Assignment of values for A n [A],
B n [B], and ALTA n [ALTA] summarizes the ALTA n writing and reading accesses for all ALTA n operation modes.
In modes that do not require this register, any write access is ignored and any read access returns unspecified data.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
ALTA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1935*

Enhanced Modular IO Subsystem (eMIOS)
Fields
Field Function
Reserved
31-16
—
15-0 Alternate Address
ALTA See the register description.
60.8.6.12 UC Control 2 n (C2_0 - C2_23)
Offset
For n = 0 to 23:
Register Offset
C2_n 38h + (n × 20h)
Function
Provides additional controls for the UC.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
UCEXTPRE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 Reserved
UCPR
UCRELDEL_INT
ECLK
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-20
—
19-16 Extended Prescaler
UCEXTPRE Specifies the clock divider for the internal UC prescaler. Write the desired divider value minus 1. For
example, for a divider of 1, write 0.
Table continues on the next page...

---

*Page 1936*

Enhanced Modular IO Subsystem (eMIOS)
Table continued from the previous page...
Field Function
NOTE
The two least-significant bits of UCEXTPRE mirror C n [UCPRE] . Any write operation to
UCEXTPRE also affects UCPRE.
Reserved
15
—
14 Prescaler Clock Source
UCPRECLK Selects the clock source for the internal prescaler.
0b - Prescaled clock
1b - eMIOS module clock
Reserved
13-5
—
4-0 Reload Signal Output Delay Interval
UCRELDEL_IN Specifies the delay interval, in counter bus reload events, between each assertion of AS1-BS1 reload in MC
T and MCB modes.
00000b - Every event
00001b - Every 2nd event
00010b - Every 3rd event
. . .
11111b - Every 32nd event

#### 60.9 Glossary

BIU IP bus interface unit
CP Clock prescaler
GCP Global clock prescaler
IIB Internal interface bus
PIF Programmable input filter
UC Unified channel

---

*Page 1937*

