<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 32 -->

# Chapter 32

# Reset Generation Module (MC_RGM)

#### 32.1 Chip-specific MC_RGM information

#### 32.1.1 MC_RGM configuration

The "Reset sources—POR, Destructive, and Functional" section of the "Reset Overview" chapter provides information about
MC_RGM's reset sources. The chapter also provides details about the chip's reset architecture.
NOTE
The ERCTRL register configuration takes several cycles to be effective. Any further access to MC_RGM must
happen after at least nine AIPS_SLOW_CLK cycles of writing to ERCTRL.

#### 32.1.2 Functional reset entry timer implementation

The default timeout value of the functional reset entry timer is 2048 clocks of MC_RGM clock (FIRC). If the RGM entry sequence
hangs, then POR_WDG would trigger and the status of this functional reset entry sequence timeout is indicated at chip-level status
register. The status of timeout in such case, is indicated at DCM.DCMROPP1[28]. There is no impact to the device behavior if the
functional reset entry sequence gets completed within the POR_WDG timeout window.

#### 32.1.3 MCXE31 reset state machine

The reset sequence of the MCXE31 products is depicted in the figure below:

#### Power-on

#### reset event

#### Power-on

#### sequence

#### Exit from

#### Destructive Standby mode

#### reset event Destructive reset

In case of Standby mode, only the Run

#### sequence

domain resets and the exit from POR
happens on Standby mode exit.

#### Functional reset Functional reset

#### Standby mode

#### entry sequence sequence

#### Functional Enter

#### Out of reset

#### Standby mode

#### reset event

Figure 132. MCXE31 reset state machine

---

*Page 815*

Reset Generation Module (MC_RGM)

#### 32.2 Introduction

The Reset Generation Module (MC_RGM) centralizes the different reset sources and manages the reset sequence of the chip. It
provides a register interface and the reset sequencer. There are various registers available in this module to monitor and control
the chip reset sequence.
The following figure shows the block diagram of MC_RGM.

#### Standard IPS interface

#### Power-on

#### Registers

#### Platform interface

#### Destructive reset n

#### MC_CGM

#### Chip resets

#### External reset Software resettable

#### domains

#### filter

#### Destructive reset

#### Reset state

#### machine

#### Functional reset n

#### Functional reset filter

#### MC_RGM

Figure 133. MC_RGM block diagram

#### 32.3 Features

Here are the key features of MC_RGM:

---

*Page 816*

Reset Generation Module (MC_RGM)
• Destructive and functional reset management
• Capturing the reset sources for each reset sequence (reset status flags)
• Assertion the RESET_B pin to propagate the reset sequence out of chip
• Configurable escalation of recurring 'functional' resets to 'destructive' reset
• Configurable escalation of recurring 'destructive' resets to keep the chip in the reset state until the next power-on reset
• Software controllable reset assertion
• Pad safe state control generation

#### 32.4 Reset sources

The reset sources are organized in three categories: power-on, destructive, and functional.
A power-on reset source is associated with an event typically related to power-up or low-voltage scenarios. When a power-on reset
occurs, the full reset sequence is applied to the chip. This resets the full chip, including the MC_RGM, and the memory content
must be considered to be invalid.
A destructive reset source is associated with an event related to a critical, usually hardware, error or dysfunction. When a
destructive reset event occurs, the full reset sequence is applied to the chip. This resets the full chip ensuring a safe start-up state
for both digital and analog modules, and the memory content must be considered to be invalid.
A functional reset source is associated with an event related to a less-critical, usually non-hardware, error or dysfunction. When a
functional reset event occurs, a partial reset sequence is applied to the chip. In this case, most digital modules are reset normally,
while the state of analog modules or specific digital modules as well as the system memory content is preserved.

#### 32.5 External signal description

The MC_RGM interfaces with the RESET_B pin.
The following table describes the signals that are connected to the I/O pad ring.
Table 182. MC_RGM external signals
Signal name Reset value Description
RESET_B 0 Active low external reset.
A bidirectional reset pin indicating the reset state.

#### 32.6 RESET_B pin assertion and pin safe state control

The MC_RGM asserts the RESET_B pin when the device is in a reset sequence, and it remains asserted until the end of the reset
sequence. During this reset sequence, most of the chip's pins are safe/pad stated according to the values shown in the IOMUX
table/spreadsheet. Note that the safe state values may vary according to the reset sequence type.
In addition, the MC_RGM has a feature to assert the RESET_B pin through software, without initiating a reset sequence. This is
achieved by writing 1b to the ERCTRL[ ERASSERT ]. When this occurs, most of the chip's pins are safe-stated according to the
values shown in the IOMUX table/spread sheet. The RESET_B assertion and pin safe-stating remain active until the end of the
next reset sequence.
This features has to be used only with selftest of the main reset domain.

#### 32.7 Functional description

---

*Page 817*

Reset Generation Module (MC_RGM)

#### 32.7.1 Reset state machine

The main role of MC_RGM is the generation of the reset sequence that ensures that the correct parts of the chip are reset based
on the reset source event.
For each reset event, immediately after it is captured by the MC_RGM, the following takes place:
1. The corresponding reset event status bit is set in the MC_RGM_DES and MC_RGM_FES registers.
2. The pins are put into their default states
3. The RESET_B pin is asserted.

#### Power-on

#### reset event

#### Power-on

#### sequence

#### Destructive

#### reset event

#### Exit from

#### Destructive Destructive reset Standby mode

#### reset event sequence

#### Destructive

#### reset event

#### Functional reset Functional reset

#### Standby mode

#### entry sequence sequence

#### Destructive

#### reset event

#### Functional Enter

#### Out of reset

#### Standby mode

#### reset event

Figure 134. Reset sequence
NOTE
See chip-specific MC_RGM information for chip-specific reset sequence details.
32.7.1.1 Power-on reset sequence
A reset is always generated when the power-on reset source is asserted, and it has priority over all other reset sources. Such a
power-on reset forces the reset state machine to enter the power-on sequence resulting in the assertion of all reset signals. The
reset state machine starts progressing when the following two conditions are verified:
1. All the power-on reset events are cleared
2. The MC_RGM's clock source (the FIRC) has started up and stabilized
If a power-on reset event has occurred, the DES[F_POR] bit is set.
The power-on reset cannot be demoted by the software.

---

*Page 818*

Reset Generation Module (MC_RGM)
32.7.1.2 Destructive reset sequence
The 'Destructive reset sequence' is comprised of a number of phases, where DEST0 is the first phase and is followed by DEST1
and so forth.
This phase is entered immediately from any phase on a power-on , standby reset sequence, or enabled destructive reset event .
A destructive reset counter starts immediately on entry in the DEST0 phase. The DEST0 state is exited to the DEST1 state on the
rising edge of FIRC_CLK immediately after all of the following conditions have been established:
• The DEST0 duration time has expired
• All destructive reset inputs are cleared
The DEST0 state is immediately exited to the power-on state if a power-on reset event occurs.
The reset state machine exits the destructive reset sequence and enters the functional reset sequence when:
• All the destructive reset events are cleared.
• All the processes that take place during the destructive reset sequence have completed. For details, see Reset chapter.
• The 'destructive' reset escalator counter has not reached the value in DRET[ DRET ].
32.7.1.3 Functional reset sequence
There are two functional reset sequence, the functional reset entry sequence and the functional reset exit sequence.
32.7.1.3.1 Functional reset entry sequence
The functional reset entry sequence is only entered when a functional reset event occurs during the idle phase.
If a functional reset event occurs during an ongoing reset sequence, the corresponding event status flag is set, and the RESET_B
pin is asserted per the reset event's configuration. However, the reset sequence is not influenced, and it continues to progress
without interruption.
Functional reset is not asserted during functional reset entry sequence.
The functional reset entry sequence is exited to the DEST0 on the next rising edge of FIRC if a destructive reset event has
occurred. The sequence immediately enters the power up sequence if a POR event occurs
In all other cases, the sequence exits to the first stage of functional reset exit sequence.
32.7.1.3.2 Functional reset exit sequence
The functional reset exit sequence is entered either on exit from the destructive reset sequence or on completion of the functional
reset entry sequence. The reset state machine exits this sequence and enters the idle phase on verification of the following:
• All the functional reset events are cleared.
• All the processes that take place during the functional reset sequence have completed. For details, see Reset chapter.
If a functional reset event occurs during an ongoing reset sequence, the corresponding event status flag is set, and the RESET_B
pin is asserted per the reset event's configuration. However, the reset sequence is not influenced, and it continues to progress
without interruption.
32.7.1.4 Idle phase
This is the final phase and is entered on exit from the functional reset exit sequence . When this phase is reached, MC_RGM
releases control of the system to the platform and waits for the new reset events that can trigger a reset sequence.

#### 32.7.2 Destructive resets

A destructive reset indicates that an event has occurred after which critical register or memory content can no longer
be guaranteed.

---

*Page 819*

Reset Generation Module (MC_RGM)
The status flag associated with a given destructive reset event ( Destructive Event Status Register (DES) ) is set when the
destructive reset is asserted and the power-on reset is not asserted. It is possible for multiple status bits to be set simultaneously
and the software determines which reset source is the most critical for the application.
The low-voltage detector threshold ensures that when the reset corresponding to the core supply low-voltage detect is enabled,
the supply is sufficient to have the destructive event correctly propagated through the digital logic. Therefore, if a given destructive
reset is enabled, MC_RGM ensures that the associated reset event is correctly triggered to the full system.
An enabled destructive reset triggers a reset sequence starting from the beginning of DEST0.

#### 32.7.3 External reset

MC_RGM manages the external reset coming from RESET_B . The detection of a falling edge on RESET_B starts the reset
sequence from the beginning of the destructive reset entry sequence.
The status flag associated with the external reset falling edge event (the FES[ F_EXR ]) is set when the external reset is asserted
and the power-on reset is not asserted.

#### 32.7.4 Functional resets

A functional reset indicates that an event has occurred after which it can be guaranteed that critical register and memory content
is still intact.
The status flag associated with a given functional reset event ( Functional /External Reset Status Register (FES) ) is set when the
functional reset is asserted and the power-on reset is not asserted. It is possible for multiple status bits to be set simultaneously
and the software determines which reset source is the most critical for the application.
An enabled functional reset triggers a reset sequence starting from the beginning of the functional reset entry sequence.

#### 32.7.5 Functional reset entry timer

The purpose of Functional Reset Entry Timer feature is to indicate any scenario wherein functional reset entry sequence does not
get completed in prerequisite time .
When FRENTC[ FRET_EN ] is enabled, the functional reset entry timer starts from functional reset event point and gets terminated
if the functional reset entry sequence is successfully completed or when it reached to the timeout value . It gets cleared on any
destructive or POR reset events.
The timeout value is controlled by FRENTC[ FRET_TIMEOUT ]. The FRENTC[ FRET_TIMEOUT ] value should be greater than
zero. If configured as zero, the default timeout value 200 gets used.

#### 32.7.6 Alternate event generation

MC_RGM provides alternative events to be generated on reset source assertion. When a reset source is asserted, MC_RGM
normally enters the reset sequence. Alternatively, it is possible for some reset source events to be converted from a reset to an
interrupt request issued to the core. Alternate event selection for a given reset source is made through the RGM_FERD register
as shown in the following table.
Table 183. Functional Reset Disable Register (RGM_FERD) field descriptions
RGM bit FERD value Generated event
0 Reset
1 Interrupt request
The alternate event is cleared by deasserting the source of the request (that is, at the reset source that caused the alternate
request) and also clearing the appropriate RGM_FES status bit.

---

*Page 820*

Reset Generation Module (MC_RGM)

#### 32.7.7 RESET_B assertion control

The software indicates to the MC_RGM that the RESET_B is to be asserted by writing to the ERASSERT bit in the RGM_ERCTRL
register. When this bit is set by the software, RESET_B gets asserted. Setting of this field does not impact the reset sequence in
any way.
An example where the ERCTRL[ ERASSERT ] field could be set by the software is when entering the self test sequence, during
which RESET_B is to be asserted. This indicates the chip is not available in the functional mode although a reset sequence is not
in progress. The deassertion of RESET_B is not controlled by the software. Instead, the RESET_B pin remains asserted until the
next time the chip exits a reset sequence.
ERASSERT bit is also cleared during the reset sequence.
MC_RGM asserts the external reset if the reset sequence is triggered by one of the following:
• A power-on reset
• A destructive reset event
• A functional reset event
In this case, external reset is asserted until all conditions for the exiting reset sequence have been met, with the exception of the
RESET_B assertion check

#### 32.7.8 Functional reset escalation

Functional reset escalation can be used to generate a destructive reset if a number of functional resets is occurred between
software writes to the RGM_FRET register. This function is enabled by writing a non-zero value to the FRET field of this register.
After the functional reset escalation is enabled, MC_RGM increases a counter on each functional reset that causes a reset
sequence to be initiated (which means, entrance into FUNC0 from the IDLE phase) . This counter is cleared on a write of any value
to the RGM_FRET register and on any power-on or destructive reset. If the counter reaches the value in the FRET field of the
RGM_FRET register, MC_RGM asserts a destructive reset.
The following figure shows the functional reset escalation counter.
1 1
0 0
destructive reset destructive reset
1 1 0 0
write to RGM_FRET write to RGM_FRET
functional reset functional reset
functional reset event functional reset event
count count
destructive destructive
= =
reset event reset event
RGM_FRET RGM_FRET
register register
Figure 135. Functional reset escalation counter
NOTE
Functional counter increments for each reset source for which escalation is enabled. For details, see "Reset
sources" table in the Reset chapter.

---

*Page 821*

Reset Generation Module (MC_RGM)

#### 32.7.9 Destructive reset escalation

Destructive reset escalation can be used to keep the chip in the reset state until the power-on triggers a reset sequence if a
number of destructive resets are occurred between software writes to the RGM_DRET register. This function is enabled by writing
a non-zero value to the DRET field of this register.
After destructive reset escalation is enabled, MC_RGM increases a counter on each destructive reset that is enabled. . This
causes a reset sequence to be initiated (that is, entrance into DEST0 from the idle phase or any other reset phase) or an ongoing
reset sequence to restart (that is, entrance into DEST0 from any other reset phase). This counter is cleared on a write of any value
to the RGM_DRET register and on any power-on reset. If the counter reaches the value in the DRET field of the RGM_DRET
register, MC_RGM enters reset DEST0 and stays there until the next power-on reset occurs .
The following figure shows the destructive reset escalation counter.
1
0
power-on reset
1 0
write to RGM_DRET
destructive reset
destructive reset event
count
stay in reset phase DEST0
=
RGM_DRET
register
Figure 136. Destructive reset escalation counter
NOTE
Destructive counter increments for each reset source for which escalation is enabled. For details, see "Reset
sources" table in the Reset chapter.

#### 32.8 MC_RGM register descriptions

Access to the following locations do not generate transfer error:
• 2Ch
All registers can be accessed as read/write in supervisor mode.

#### 32.8.1 MC RGM Register Map memory map

MC_RGM base address: 4028_C000h
Offset Register Access Reset value
Width
(In bits)
0h Destructive Event Status Register (DES) 32 RW 0000_0001h
8h Functional /External Reset Status Register (FES) 32 RW 0000_0000h
Ch Functional Event Reset Disable Register (FERD) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 822*

Reset Generation Module (MC_RGM)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
10h Functional Bidirectional Reset Enable Register (FBRE) 32 RW 0000_0000h
14h Functional Reset Escalation Counter Register (FREC) 32 RW 0000_0000h
18h Functional Reset Escalation Threshold Register (FRET) 32 RW 0000_000Fh
1Ch Destructive Reset Escalation Threshold Register (DRET) 32 RW 0000_0000h
20h External Reset Control Register (ERCTRL) 32 RW 0000_0000h
24h Reset During Standby Status Register (RDSS) 32 RW 0000_0000h
28h Functional Reset Entry Timeout Control Register (FRENTC) 32 RW 0000_0001h

#### 32.8.2 Destructive Event Status Register (DES)

Offset
Register Offset
DES 0h
Function
This register contains the status of the 'destructive' reset sources. This register can be accessed as read/write in supervisor mode
and read-only in user mode. Register bits are cleared on write '1'. This register is reset only on power-on reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DEBU SW_ HSE_ HSE_T
R 0 0 0 0 0 0 0 0 0 0 0 0
G_D ... DEST SNV ... MP ...
W W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
SYS_ HSE_ AIPS_ CORE PLL_ FXOS MC_R STCU FCCU F_
R 0 0 0 0 0 0
DIV ... CLK ... PL ... _CL ... LOL C_F ... GM_ ... _URF _FTR POR
W W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1
Fields
Field Function
Reserved
31
Table continues on the next page...

---

*Page 823*

Reset Generation Module (MC_RGM)
Table continued from the previous page...
Field Function
—
Flag for 'Destructive' Reset DEBUG_DEST
30
0b - 'Destructive' reset event DEBUG_DEST has not occurred since either the last clear or the
DEBUG_DEST
last power-on reset assertion.
1b - 'Destructive' reset event DEBUG_DEST has occurred.
Flag for 'Destructive' Reset SW_DEST
29
0b - 'Destructive' reset event SW_DEST has not occurred since either the last clear or the last
SW_DEST
power-on reset assertion.
1b - 'Destructive' reset event SW_DEST has occurred.
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
Reserved
23
—
Reserved
22
—
Reserved
21
—
Reserved
20
—
Reserved
19
—
Flag for 'Destructive' Reset HSE_SNVS_RST
18
Table continues on the next page...

---

*Page 824*

Reset Generation Module (MC_RGM)
Table continued from the previous page...
Field Function
HSE_SNVS_RS 0b - 'Destructive' reset event HSE_SNVS_RST has not occurred since either the last clear or the
T last power-on reset assertion.
1b - 'Destructive' reset event HSE_SNVS_RST has occurred.
Flag for 'Destructive' Reset HSE_TMPR_RST
17
0b - 'Destructive' reset event HSE_TMPR_RST has not occurred since either the last clear or the
HSE_TMPR_R
last power-on reset assertion.
ST
1b - 'Destructive' reset event HSE_TMPR_RST has occurred.
Reserved
16
—
Flag for 'Destructive' Reset SYS_DIV_FAIL
15
0b - 'Destructive' reset event SYS_DIV_FAIL has not occurred since either the last clear or the
SYS_DIV_FAIL
last power-on reset assertion.
1b - 'Destructive' reset event SYS_DIV_FAIL has occurred.
Flag for 'Destructive' Reset HSE_CLK_FAIL
14
0b - 'Destructive' reset event HSE_CLK_FAIL has not occurred since either the last clear or the
HSE_CLK_FAIL
last power-on reset assertion.
1b - 'Destructive' reset event HSE_CLK_FAIL has occurred.
Reserved
13
—
Flag for 'Destructive' Reset AIPS_PLAT_CLK_FAIL
12
0b - 'Destructive' reset event AIPS_PLAT_CLK_FAIL has not occurred since either the last clear
AIPS_PLAT_CL
or the last power-on reset assertion.
K_FAIL
1b - 'Destructive' reset event AIPS_PLAT_CLK_FAIL has occurred.
Reserved
11
—
Flag for 'Destructive' Reset CORE_CLK_FAIL
10
0b - 'Destructive' reset event CORE_CLK_FAIL has not occurred since either the last clear or the
CORE_CLK_FA
last power-on reset assertion.
IL
1b - 'Destructive' reset event CORE_CLK_FAIL has occurred.
Flag for 'Destructive' Reset PLL_LOL
9
0b - 'Destructive' reset event PLL_LOL has not occurred since either the last clear or the last
PLL_LOL
power-on reset assertion.
1b - 'Destructive' reset event PLL_LOL has occurred.
Table continues on the next page...

---

*Page 825*

Reset Generation Module (MC_RGM)
Table continued from the previous page...
Field Function
Flag for 'Destructive' Reset FXOSC_FAIL
8
0b - 'Destructive' reset event FXOSC_FAIL has not occurred since either the last clear or the last
FXOSC_FAIL
power-on reset assertion.
1b - 'Destructive' reset event FXOSC_FAIL has occurred.
Reserved
7
—
Flag for 'Destructive' Reset MC_RGM_FRE
6
0b - 'Destructive' reset event MC_RGM_FRE has not occurred since either the last clear or the
MC_RGM_FRE
last power-on reset assertion.
1b - 'Destructive' reset event MC_RGM_FRE has occurred.
Reserved
5
—
Flag for 'Destructive' Reset STCU_URF
4
0b - 'Destructive' reset event STCU_URF has not occurred since either the last clear or the last
STCU_URF
power-on reset assertion.
1b - 'Destructive' reset event STCU_URF has occurred.
Flag for 'Destructive' Reset FCCU_FTR
3
0b - 'Destructive' reset event FCCU_FTR has not occurred since either the last clear or the last
FCCU_FTR
power-on reset assertion.
1b - 'Destructive' reset event FCCU_FTR has occurred.
Reserved
2
—
Reserved
1
—
0 Flag for power-on reset
F_POR
NOTE
If this field is set, ignore all the fields of Destructive Event Status Register (DES) and
Functional /External Reset Status Register (FES) registers at power up.
0b - No power-on event has occurred since the last clear.
1b - A power-on event has occurred.

---

*Page 826*

Reset Generation Module (MC_RGM)

#### 32.8.3 Functional /External Reset Status Register (FES)

Offset
Register Offset
FES 8h
Function
This register contains the status of the 'functional' and external reset sources. This register can be accessed as read/write in
supervisor mode and read-only in user mode. Register fields are cleared on write '1' if the triggering event has already been
cleared at the source. This register is reset only on power-on reset.
NOTE
• The startup self-test functional reset event to MC_RGM initiates a functional reset sequence after which the
FES[ST_DONE] is set. This field is sticky in nature and gets cleared if software clears this field.
• If functional reset escalation to destructive reset is disabled, then the status of this register must be ignored
if the fields of Destructive Event Status Register (DES) other than DES[ F_POR ] are set.
• If functional reset escalation to destructive reset is enabled and if the fields of Destructive Event Status
Register (DES) , other than DES[ F_POR ], are set, then based on these fields user should check if the cause of
the destructive reset was due to functional reset escalation or if it was triggered directly by a destructive reset
source, in which case FES needs to be ignored.
• See chip-specific MC_RGM information for applicability of the FES[ST_DONE] field.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DEBU SW_ HSE_ HSE_
R 0 0 0 0 0 0 0 0 0 0 0 0
G_F ... FUNC BOO ... SWT ...
W W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
JTAG_ SWT0 ST_ FCCU
R 0 0 0 0 0 0 0 0 0 0 0 F_EXR
RST _RST DONE _RST
W W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
Flag for 'Functional' Reset DEBUG_FUNC
30
Table continues on the next page...

---

*Page 827*

Reset Generation Module (MC_RGM)
Table continued from the previous page...
Field Function
DEBUG_FUNC 0b - 'Functional' reset event DEBUG_FUNC has not occurred since either the last clear or the last
power-on reset assertion.
1b - 'Functional' reset event DEBUG_FUNC has occurred.
Flag for 'Functional' Reset SW_FUNC
29
0b - 'Functional' reset event SW_FUNC has not occurred since either the last clear or the last
SW_FUNC
power-on reset assertion.
1b - 'Functional' reset event SW_FUNC has occurred.
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
Reserved
23
—
Reserved
22
—
Reserved
21
—
Flag for 'Functional' Reset HSE_BOOT_RST
20
0b - 'Functional' reset event HSE_BOOT_RST has not occurred since either the last clear or the
HSE_BOOT_R
last power-on reset assertion.
ST
1b - 'Functional' reset event HSE_BOOT_RST has occurred.
Reserved
19
—
Reserved
18
—
Table continues on the next page...

---

*Page 828*

Reset Generation Module (MC_RGM)
Table continued from the previous page...
Field Function
Reserved
17
—
Flag for 'Functional' Reset HSE_SWT_RST
16
0b - 'Functional' reset event HSE_SWT_RST has not occurred since either the last clear or the
HSE_SWT_RS
last power-on reset assertion.
T
1b - 'Functional' reset event HSE_SWT_RST has occurred.
Reserved
15
—
Reserved
14
—
Reserved
13
—
Reserved
12
—
Reserved
11
—
Reserved
10
—
Flag for 'Functional' Reset JTAG_RST
9
0b - 'Functional' reset event JTAG_RST has not occurred since either the last clear or the last
JTAG_RST
power-on reset assertion.
1b - 'Functional' reset event JTAG_RST has occurred.
Reserved
8
—
Reserved
7
—
Flag for 'Functional' Reset SWT0_RST
6
0b - 'Functional' reset event SWT0_RST has not occurred since either the last clear or the last
SWT0_RST
power-on reset assertion.
1b - 'Functional' reset event SWT0_RST has occurred.
Reserved
5
Table continues on the next page...

---

*Page 829*

Reset Generation Module (MC_RGM)
Table continued from the previous page...
Field Function
—
Flag for 'Functional' Reset ST_DONE
4
0b - 'Functional' reset event ST_DONE has not occurred since either the last clear or the last
ST_DONE
power-on reset assertion.
1b - 'Functional' reset event ST_DONE has occurred.
Flag for 'Functional' Reset FCCU_RST
3
0b - 'Functional' reset event FCCU_RST has not occurred since either the last clear or the last
FCCU_RST
power-on reset assertion.
1b - 'Functional' reset event FCCU_RST has occurred.
Reserved
2
—
Reserved
1
—
0 Flag for External Reset
F_EXR External reset is a source of destructive reset.
0b - No external reset event has occurred since either the last clear or the last power-on reset
assertion.
1b - An external reset event has occurred.

#### 32.8.4 Functional Event Reset Disable Register (FERD)

Offset
Register Offset
FERD Ch
Function
This register provides dedicated fields to disable functional reset sources. When any of these reset sources are disabled, the
associated functional event is demoted to trigger an interrupt request. This register can be accessed as read/write in supervisor
mode and read-only in user mode. Each byte can be written to only once after a destructive or power-on reset and this register
is reset only on power-on and any destructive reset.
NOTE
It is important to clear the Functional /External Reset Status Register (FES) before writing 1 to any of the fields in
this register. Otherwise a interrupt request may occur.

---

*Page 830*

Reset Generation Module (MC_RGM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
D_DE
BUG ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0 0 0 0 0 0 0 0
D_JTA D_SW D_FC Reserv
G_ ... T0_ ... CU_ ... ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
DEBUG_FUNC Disable Control
30
0b - Functional reset event DEBUG_FUNC triggers a reset sequence.
D_DEBUG_FU
NC
1b - Functional reset event DEBUG_FUNC generates an interrupt request.
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
Reserved
23
—
Reserved
22
Table continues on the next page...

---

*Page 831*

Reset Generation Module (MC_RGM)
Table continued from the previous page...
Field Function
—
Reserved
21
—
Reserved
20
—
Reserved
19
—
Reserved
18
—
Reserved
17
—
Reserved
16
—
Reserved
15
—
Reserved
14
—
Reserved
13
—
Reserved
12
—
Reserved
11
—
Reserved
10
—
JTAG_RST Disable Control
9
0b - Functional reset event JTAG_RST triggers a reset sequence.
D_JTAG_RST
1b - Functional reset event JTAG_RST generates an interrupt request.
Reserved
8
Table continues on the next page...

---

*Page 832*

Reset Generation Module (MC_RGM)
Table continued from the previous page...
Field Function
—
Reserved
7
—
SWT0_RST Disable Control
6
0b - Functional reset event SWT0_RST triggers a reset sequence.
D_SWT0_RST
1b - Functional reset event SWT0_RST generates an interrupt request.
Reserved
5
—
Reserved
4
—
FCCU_RST Disable Control
3
0b - Functional reset event FCCU_RST triggers a reset sequence.
D_FCCU_RST
1b - Functional reset event FCCU_RST generates an interrupt request.
Reserved
2
—
Reserved
1
—
Reserved
0
—

#### 32.8.5 Functional Bidirectional Reset Enable Register (FBRE)

Offset
Register Offset
FBRE 10h
Function
This register enables the generation of an external reset on 'functional' reset. This register can be accessed as read/write in
supervisor mode and read-only in user mode. This register is reset on power-on and any 'destructive' reset.

---

*Page 833*

Reset Generation Module (MC_RGM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
BE_D BE_S BE_H BE_H
R 0 0 0 0 0 0 0 0 0 0 0 0
EBU ... W_F ... SE_ ... SE_ ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
BE_JT BE_S BE_FC
R 0 0 0 0 0 0 0 0 0 0 0
BE_ST Reserv
AG ... WT0 ... CU ...
_D ... ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
Bidirectional Reset Enables for 'Functional' Reset DEBUG_FUNC
30
0b - External reset pin is asserted on a 'Functional' reset DEBUG_FUNC event if the reset is
BE_DEBUG_FU
enabled.
NC
1b - External reset pin is not asserted on a 'functional' reset DEBUG_FUNC event.
Bidirectional Reset Enables for 'Functional' Reset SW_FUNC
29
0b - External reset pin is asserted on a 'Functional' reset SW_FUNC event if the reset is enabled.
BE_SW_FUNC
1b - External reset pin is not asserted on a 'functional' reset SW_FUNC event.
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
Reserved
23
Table continues on the next page...

---

*Page 834*

Reset Generation Module (MC_RGM)
Table continued from the previous page...
Field Function
—
Reserved
22
—
Reserved
21
—
Bidirectional Reset Enables for 'Functional' Reset HSE_BOOT_RST
20
0b - External reset pin is asserted on a 'Functional' reset HSE_BOOT_RST event if the reset is
BE_HSE_BOOT
enabled.
_RST
1b - External reset pin is not asserted on a 'functional' reset HSE_BOOT_RST event.
Reserved
19
—
Reserved
18
—
Reserved
17
—
Bidirectional Reset Enables for 'Functional' Reset HSE_SWT_RST
16
0b - External reset pin is asserted on a 'Functional' reset HSE_SWT_RST event if the reset is
BE_HSE_SWT_
enabled.
RST
1b - External reset pin is not asserted on a 'functional' reset HSE_SWT_RST event.
Reserved
15
—
Reserved
14
—
Reserved
13
—
Reserved
12
—
Reserved
11
—
Reserved
10
Table continues on the next page...

---

*Page 835*

Reset Generation Module (MC_RGM)
Table continued from the previous page...
Field Function
—
Bidirectional Reset Enables for 'Functional' Reset JTAG_RST
9
0b - External reset pin is asserted on a 'Functional' reset JTAG_RST event if the reset is enabled.
BE_JTAG_RST
1b - External reset pin is not asserted on a 'functional' reset JTAG_RST event.
Reserved
8
—
Reserved
7
—
Bidirectional Reset Enables for 'Functional' Reset SWT0_RST
6
0b - External reset pin is asserted on a 'Functional' reset SWT0_RST event if the reset is enabled.
BE_SWT0_RST
1b - External reset pin is not asserted on a 'functional' reset SWT0_RST event.
Reserved
5
—
Bidirectional Reset Enables for 'Functional' Reset ST_DONE
4
0b - External reset pin is asserted on a 'Functional' reset ST_DONE event if the reset is enabled.
BE_ST_DONE
1b - External reset pin is not asserted on a 'functional' reset ST_DONE event.
Bidirectional Reset Enables for 'Functional' Reset FCCU_RST
3
0b - External reset pin is asserted on a 'Functional' reset FCCU_RST event if the reset is enabled.
BE_FCCU_RST
1b - External reset pin is not asserted on a 'functional' reset FCCU_RST event.
Reserved
2
—
Reserved
1
—
Reserved
0
—

#### 32.8.6 Functional Reset Escalation Counter Register (FREC)

Offset
Register Offset
FREC 14h

---

*Page 836*

Reset Generation Module (MC_RGM)
Function
This register provides the current value of functional reset escalation counter. It can be accessed in read/write, in supervisor mode.
It can be accessed in read in the user mode. This register is reset by power-on reset, destructive reset, when you reconfigure the
FREC field to Fh and when you write any value to the Functional Reset Escalation Threshold Register (FRET) register.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 FREC
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-4
—
3-0 Functional' Reset Escalation Counter
FREC This field provides the value of functional reset escalation counter.

#### 32.8.7 Functional Reset Escalation Threshold Register (FRET)

Offset
Register Offset
FRET 18h
Function
This register sets the threshold for 'functional' reset escalation to a 'destructive' reset. It can be accessed in read/write, either
in supervisor mode. It can be accessed in read-only in the user mode. Writing a non-zero value to the FRET field enables
the 'functional' reset escalation function. Writing any value to this register resets the 'functional' reset escalation counter. See
Functional reset escalation for details on the 'functional' reset escalation function. This register is reset on power-on and any
'destructive' reset.

---

*Page 837*

Reset Generation Module (MC_RGM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
FRET
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 1 1 1 1
Fields
Field Function
Reserved
31-4
—
3-0 'Functional' Reset Escalation Threshold
FRET If the value of this field is 0, the 'functional' reset escalation function is disabled. Any other value is the
number of 'functional' resets that causes a 'destructive' reset.

#### 32.8.8 Destructive Reset Escalation Threshold Register (DRET)

Offset
Register Offset
DRET 1Ch
Function
This register sets the threshold for 'destructive' reset escalation to keeping the chip in the reset state until the next power-on reset
triggers a new reset sequence. It can be accessed in read/write, either in supervisor mode. It can be accessed in read-only in the
user mode. Writing a non-zero value to the DRET field enables the 'destructive' reset escalation function. Writing any value to
this register resets the 'destructive' reset counter. See Destructive reset escalation for details on the 'destructive' reset escalation
function. This register is reset only on power-on reset.

---

*Page 838*

Reset Generation Module (MC_RGM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
DRET
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-4
—
3-0 'Destructive' Reset Escalation Threshold
DRET If the value of this field is 0, the 'destructive' reset escalation function is disabled. Any other value is
the number of 'destructive' resets which keeps the chip in the reset state until the next power-on reset
triggers a new reset sequence.

#### 32.8.9 External Reset Control Register (ERCTRL)

Offset
Register Offset
ERCTRL 20h
Function
This register allows software to control the assertion of External reset pin. It can be accessed in read/write, in supervisor mode.
It can be accessed in read-only in the user mode. This register is reset on all resets.

---

*Page 839*

Reset Generation Module (MC_RGM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
ERAS
SERT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-1
—
0 ERASSERT
ERASSERT
NOTE
Setting ERASSERT to 1b also safe/pad states most of the chip's pins. See the IOMUX
table/spreadsheet for each pin's safe/pad state value. Software must use the ERASSERT
field for this purpose only as part of the main reset domain self-test entry procedure. Using
it at any other time may result in unpredictable system behavior.
0b - No change
1b - External reset is asserted

#### 32.8.10 Reset During Standby Status Register (RDSS)

Offset
Register Offset
RDSS 24h
Function
This register provides status of whether a reset event occurred during standby mode. Register bits are cleared on write '1'. This
register is reset only on power-on reset.
NOTE
On exiting a reset sequence after standby exit, the software must perform a read operation on
MC_ME[PREV_MODE] and RDSS register. If any field of the RDSS register is set, the software must ignore the
status reported by MC_ME[PREV_MODE] register otherwise the status of MC_ME[PREV_MODE] register reports
the device status.
If MC_ME indicates last mode as RESET, then perform a reset exit in software else a standby exit.

---

*Page 840*

Reset Generation Module (MC_RGM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
FES_ DES_
R 0
RES RES
W W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-2
—
FES_RES
1
0b - No functional reset event occurred during standby mode.
FES_RES
1b - Functional reset event occurred during standby mode.
DES_RES
0
0b - No destructive reset event occurred during standby mode.
DES_RES
1b - Destructive reset event occurred during standby mode.

#### 32.8.11 Functional Reset Entry Timeout Control Register (FRENTC)

Offset
Register Offset
FRENTC 28h
Function
This register provides control for functional reset entry timeout counter. This register is reset on power-on and destructive reset
events. This is a word accessible register. Byte accesses are not allowed on this register.
NOTE
This field should be set a value greater than one.

---

*Page 841*

Reset Generation Module (MC_RGM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
FRET_TIMEOUT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
FRET_
FRET_TIMEOUT
EN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1
Fields
Field Function
31-1 Functional Reset Entry Timer Value
FRET_TIMEOU This field allows to override the timeout duration of the functional reset entry timer. The counter value is
T in terms of the Fast IRC clock cycles (typical clock frequency of 48 MHz). A non zero value of this field
would indicate the timer value to be used during the functional reset entry sequence.
Functional Reset Entry Timer Enable/Disable
0
0b - Functional reset entry timer is disabled.
FRET_EN
1b - Functional reset entry timer is enabled

---

*Page 842*

