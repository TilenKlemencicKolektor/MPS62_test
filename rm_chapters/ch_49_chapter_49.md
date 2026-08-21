<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 49 -->

# Chapter 49

# Fault Collection and Control Unit (FCCU)

#### 49.1 Chip-specific FCCU information

#### 49.1.1 FCCU NCF slots

Table 226. FCCU NCF slots
Slot number Source module (error type)
NCF[0]
• Cortex-M7 LS and core lockup
• ELE_HSEB lockup
NCF[1] Interconnect:
• All EDC bus gaskets
• XBIC monitors and platform gaskets
NCF[2] ECC errors:
• PRAMC
• TCMs
• Caches
• eDMA
• EDC after ECC
• ELE_HSEB RAM errors
NCF[3] All flash memory errors:
• FMU
• PFLASH
• DCM flash memory
NCF[4] Voltage-related errors:
• PMC 1.1 V and 2.5 V GNG
• Pad overvoltage
NCF[5] Debug and test monitoring
NCF[6] INTM
NCF[7] Software notification

#### 49.1.2 Chip-boundary FCCU signals

Table 227. Chip-boundary FCCU signals
FCCU signal Chip signal
EOUT0 FCCU_ERR0
EOUT1 FCCU_ERR1

---

*Page 1402*

Fault Collection and Control Unit (FCCU)

#### 49.1.3 FCCU clocking

Table 228. FCCU clocking
FCCU clock signal Chip clock signal
CLKSAFE FIRC_CLK
CLKPRIM AIPS_PLAT_CLK

#### 49.1.4 FOSU timer interval

The FOSU_COUNT value determines the FOSU timer interval. On this chip, FOSU_COUNT is 69780h.

#### 49.1.5 Supported internal chip reactions

The short functional reset discussed in this chapter is equivalent to the chip functional reset. See the interrupt map file attached
to this document for interrupts from FCCU to NVIC.
NOTE
After STCU2 completes the self-testing procedure, the chip reboots and FCCU resets.

#### 49.1.6 Supported FCCU EOUT fault output modes (protocols)

This chip supports only the Bistable protocol of FCCU EOUT, and does not support the other protocols discussed in this chapter,
Dual-Rail and Time-Switching.

#### 49.1.7 Recommended reaction programming for faults

You can upgrade or downgrade the reaction of the faults according to the recommended reaction discussed in the fault map file
attached to this document.
In case you upgrade a reaction, no issues are expected in the behavior. If you downgrade the reaction, the functionality is
not guaranteed.
This is the recommendation for faults caused by lockstep errors:
1. The recommended reaction for the lockstep error fault is a functional reset.
2. Program the core to perform the following steps after rebooting:
a. Initialize TCM (because TCMs can become corrupted)
b. Invalidate the cache (because caches can become corrupted)
3. Write 1 to the corresponding bit in FCCU.NCF_S0 register to clear the non-critical fault status.
4. If the FCCU fault gets cleared, you have nothing else to do for fault handling (this means, the FCCU lockstep error
is recovered).
5. If the error persists, it indicates that the internal registers of the two cores have different values. To bring them to the same
value, you could perform any of these steps:
a. Initialize destructive reset through software by using MC_ME.MODE_CONF[DEST_RST] to recover from lockstep.
This initializes the FCCU with no pending faults.
b. Reconfigure the Cortex-M7 debug configurations (which would have been lost in previous step due to destructive
reset in the system).

---

*Page 1403*

Fault Collection and Control Unit (FCCU)

#### 49.1.8 FCCU NCF handling architecture

This chip supports eight NCFs. Therefore, multiple faults of similar nature are ORed together and then connected as a single NCF
to FCCU. To control individual fault, there are enable/disable controls provided within DCM in registers DCMRWD3, DCMRWD4
and DCMRWD5.
Similarly, the FCCU.NCF_S0 captures the status of fault within FCCU. This fault might have resulted due to any fault mapped on
the corresponding FCCU NCF channel. The DCM status registers, DCMROD3, DCMROD4 and DCMROD5 capture the status
of individual faults which are mapped onto FCCU NCFs. Figure 179 shows this arrangement.
Peripheral 1
Fault status [i] Fault enable [i]
(within DCMROD3/4/5) (within DCMROD3/4/5)
Reset=1
Fault
reaction
Reset=0
Peripheral 2
Fault status [i]
(within DCMROD3/4/5) FCCU
Fault enable [i]
(within DCMROD3/4/5) Fault
Reset=1 enable [i]
Reset=0
Fault status [i]
Peripheral n
(within DCMROD3/4/5)
Fault enable [i]
(within DCMROD3/4/5)
Reset=1
DCM
Figure 179. FCCU NCF handling architecture

#### 49.2 Overview

The FCCU provides a hardware interface to collect faults and to place the device into a safe state when a failure is detected in
the device. No CPU intervention is requested for collection and control operations. FCCU offers a systematic approach to fault
collection and control.

#### 49.2.1 Block diagram

The following figure represents a top-level diagram of the FCCU module.

---

*Page 1404*

Fault Collection and Control Unit (FCCU)
FAULT
FAULT intf
MC_RGM,
ALRT
NMI,IRQ
FSM
HNSHK HNSHK
IPS REG intf
(Slave) WDOG
PB
CLKPRIM
EOUT1 intf EOUT0 intf
EOUT[1] EIN[1] EOUT[0] EIN[0]
Figure 180. FCCU block diagram
This table describes the FCCU submodules.
Table 229. FCCU submodules
Submodule Description
REG intf Includes the register file, the IPS bus interface, the IRQ interface and the
parity block (PB) for the configuration registers
HNSHK blocks (master and slave blocks) Includes the FSM ability to support the handshake between the REG
interface and the FSM unit because of the usage of two asynchronous
clocks [CLKPRIM(module clock) and CLKSAFE(RC oscillator clock)]
FSM unit Implements the main functions of FCCU. The FSM also includes the:
• Watchdog timer (WDG)
• Alarm timer (ALRT)
FAULT intf Implements the interface for fault conditioning and management
EOUTx units Implement the output stage to manage the EOUT interfaces

#### 49.2.2 Features

The key features of the FCCU module are these:
• Management of non-critical faults
• HW or SW fault recovery management
• Fault collection from safety relevant modules on the device

---

*Page 1405*

Fault Collection and Control Unit (FCCU)
• Fault injection (fake faults)
• Collection of test results
• Lockable configuration
— Changes are only possible after entering the CONFIG state
— Supports a transient and a permanent lock
— Configuration changes observed by a watchdog timer
• Configurable fault control
• External reaction (FAULT state): EOUT signaling. Error indication via the pin(s) is controlled by FCCU.
• Internal chip reactions (ALARM state): interrupt request
• Configurable internal chip reactions for each NCF (FAULT state):
— Short functional reset request pulse
— NMI
— No reaction
— IRQ
• In Bi-Stable operational mode, one of the EOUT signals is high to indicate an OK operational state of FCCU.
[6]
• After power on, the EOUT signals have high impedance. They indicate an operational state only after the software
configures them.
• In case of a failure event or on software request for error pin indication, the pin(s) are set to faulty state for a minimum time
T min (see DELTA_T[DELTA_T] ), even if the software tries to release it before (for the case of error pin configured in Bi-Stable
mode only).
The self-checking procedure checks the FCCU circuitry at the start up. The FCCU is operational with the default configuration
immediately after the completion of the self-checking procedure . Internal (short functional reset request pulse, interrupt request)
and external (EOUT signaling) reactions are statically defined or programmable. The default configuration can be modified only
in the Configuration (CONFIG) state. FCCU is designed to function when CLKPRIM is faster than the CLKSAFE clocks.

#### 49.3 Functional description

#### 49.3.1 Definitions

In general, the following definitions are applicable for fault management:
• HW recoverable fault: The fault indication is a level-sensitive signal that remains asserted until the fault cause is deasserted.
That is, if logical 0 on the fault signal indicates fault, then the status flags are valid as long as the fault line stays at 0. The
status is automatically cleared when the fault signal goes to 1. Typically the fault signal is latched external to the FCCU in the
module where the fault occurred. The FCCU state transitions are consequently executed on the state changes of the input
fault signal. No SW intervention in the FCCU is required to recover the fault condition.
• SW recoverable fault: The fault indication is a signal asserted without a defined time duration. The fault signal is latched in
the FCCU. The fault recovery is executed following a SW recovery procedure (status/flag register clearing).
HW recoverable is an option to exclude the handling of error sources by FCCU management SW, in case it is known that the fault
is recoverable by itself when the fault condition is corrected.
For details related to reset interface, see the reset interface section in the chip reference manual.

#### 49.3.2 FSM description

The functionality of FCCU is depicted by the FSM state diagram (see Figure 181 ).
[6] Actual value depends on device-specific setting at pad level.

---

*Page 1406*

Fault Collection and Control Unit (FCCU)
FCCU has four states that are identified with the following meaning:
• CONFIG: Used only to modify the configuration of FCCU from its default. A subset of the FCCU registers, dedicated to define
the FCCU configuration (global configuration, reactions to fault, timeout, non-critical fault masking) can be accessed in write
mode only in the CONFIG state.
The CONFIG state is accessible only in the NORMAL state and if the configuration is not locked. A permanent configuration
lock can be disabled by a reset that also resets the FCCU. The transient lock register is unlocked by writing BCh into it. FCCU
gets transiently locked again if an invalid key is written into TRANS_LOCK[TRANSKEY] (that is, other than BCh). To lock
FCCU for configuration, write FFh to PERMNT_LOCK[PERMNTKEY] .
After the release of reset, the state of the transient lock is locked, and the state of the permanent lock is unlocked.
The locking feature only restricts the FSM movement into CONFIG state. After the user enters the CONFIG state and then
tries to lock the configuration, the locking of configuration is effective only after FCCU moves to the NORMAL state; it will not
be effective in the current CONFIG state.
The CONFIG to NORMAL state transition can be executed by SW or automatically following a timeout condition of the
watchdog. In case the timeout information and the SW request for state change to NORMAL appears at the same time,
watchdog timeout has the priority and hence the configuration registers (those that are writable only in the CONFIG state) are
reset to their default values. The movement to the NORMAL state is made.
The incoming faults, occurring during the configuration phase (CONFIG state) are latched in order to process them when
FCCU is moved to the NORMAL state, according to the new configuration.
All pending faults that occur during the CONFIG state result in both of the following:
— Highest-priority state transition
— Interrupt generation (NMI or alarm IRQ)
If the state transition occurs, it gives the reset reaction corresponding to the worst case based on all the faults (pending or
non-pending faults) that occurred during the CONFIG state.
• NORMAL: This is FCCU's operating state when no faults are occurring. It is also the default state on the reset exit. Following
state transitions occur on one of the following events:
— Unmasked non-critical faults with the timeout disabled: FCCU moves to the FAULT state.
— Unmasked non-critical faults with the timeout enabled: FCCU moves to the ALARM state.
— Masked non-critical faults: FCCU stays in the NORMAL state.
• ALARM: FCCU moves into the ALARM state when an unmasked non-critical fault occurs and the timeout is enabled.
Transition to the ALARM state goes along with an interrupt alarm, if enabled. By definition, this fault may be recovered within a
programmable timeout period, before it generates a transition to the FAULT state. The timeout is reinitialized if FCCU enters
the NORMAL state. The timeout restarts following the recovery from the FAULT state.
• FAULT: FCCU moves into the FAULT state when one of the following condition occurs:
— Timeout related to a non-critical fault when FCCU is in the ALARM state
— Unmasked non-critical faults with the timeout disabled
The transition from the NORMAL or ALARM to the FAULT state goes along with the generation of:
• Internal chip reaction—NMI interrupt (optional)
• External reaction—EOUT signaling (optional)
• Internal chip reaction—SW option: Soft reaction (Short functional reset request pulse if configured)
• Non Maskable Interrupt (NMI) is routed to all cores.
After moving to the FAULT state, if there is either a previous pending fault or a new fault for which NMI is enabled, NMI generation
takes place.
Multiple faults can occur at the same time.

---

*Page 1407*

Fault Collection and Control Unit (FCCU)
Configuration exit OR
timeout
CONFIG
fault (masked)
Configuration entry
AND (configuration unlocked)
NORMAL Reset
Fault
(unmasked AND timeout disabled)
Fault
(unmasked AND timeout enabled)
All faults recovered All faults
recovered
Fault not recovered on time
OR
FAULT ALARM
Fault
(unmasked AND timeout disabled)
Any Fault Pending
AND FCCU_IRQ_ALARM_ENn
pending fault (unmasked AND timeout enabled)
Figure 181. FCCU state diagram

#### 49.3.3 Fault priority scheme and nesting

The FAULT state has a higher priority than the ALARM state in case of concurrent fault events (non-critical) that occur in the
NORMAL state.
The ALARM to FAULT state transition occurs if a non-critical fault (unmasked and with timeout disabled) is asserted in the
ALARM state.
The ALARM to NORMAL state transition occurs only if all the non-critical faults (including the faults that have been collected after
the entry in the ALARM state) have been cleared (SW or HW recovery); otherwise the FCCU remains in the ALARM state.
The FAULT to NORMAL state transition occurs only if all the non-critical faults (including the faults that have been collected after
the entry in the FAULT/ALARM state) have been cleared (SW or HW recovery); otherwise the FCCU moves to the ALARM state
(if any non-critical fault is still pending and the timeout is not elapsed).
In general, no fault nesting is supported except for the non-critical faults that cause an ALARM to FAULT state transition. In this
case, the NCF timer is stopped until the FAULT state is recovered. If FCCU is in the ALARM state and another fault occurs, which
has its alarm timeout enabled, then the alarm timer shall not reload and shall not start again.

#### 49.3.4 Fault recovery

The following timing diagrams describe the main use cases of FCCU in terms of fault events and related recovery.
A typical sequence related to non-critical fault management (ALARM state), see Figure 182 and Figure 183 , is as follows:
1. Non-critical fault assertion
2. FCCU state transition (automatic): NORMAL to ALARM
• Alarm interrupt request (if enabled)
• Timeout running
3. System state: RUN
4. Alarm interrupt management: fault recovery

---

*Page 1408*

Fault Collection and Control Unit (FCCU)
• FCCU state transition: ALARM to NORMAL
SW ALARM recovery
Fault event
System state RUN
FCCU state NORMAL ALARM NORMAL
Alarm interrupt
request
NCF timer IDLE TIMER ON IDLE
FCCU reset
Figure 182. Non-critical fault (ALARM state) SW recovery
Fault event
System state RUN
FCCU state NORMAL ALARM NORMAL
Alarm interrupt
request
NCF timer IDLE TIMER ON IDLE
FCCU reset
Figure 183. Non-critical fault (ALARM state) HW recovery
A typical sequence related to non-critical fault management (ALARM to FAULT state), see Figure 184 , is as follows:
1. Non-critical fault assertion
2. FCCU state transition (automatic): NORMAL to ALARM
• Alarm interrupt request (if enabled)
• Timeout running
3. FCCU state transition (following the timeout trigger): ALARM to FAULT
• NMI assertion (if enabled)
4. NMI interrupt management (if enabled)
• Fault recovery (by software): FCCU state transition: FAULT to NORMAL

---

*Page 1409*

Fault Collection and Control Unit (FCCU)
SW FAUL T recovery
Fault event
System state RUN SAFE RUN
FCCU state NORMAL ALARM FAUL T NORMAL
Alarm interrupt
request
NCF timer IDLE TIMER ON TIMEOUT IDLE
Functional reset request
(short)
NMI
EOUT IDLE ERROR ON IDLE
FCCU reset
Figure 184. Non-critical fault (ALARM to FAULT state) recovery

#### 49.3.5 EOUT interface

Introduction
You use the EOUT[1:0] signals to indicate FCCU's condition to off-chip logic.
NOTE
For information on the availability and names of these FCCU signals on the boundary of this chip, see the
chip-specific FCCU information.
The FCCU conditions
There are three FCCU conditions:
Condition Description
Faulty All of the following are true:
• The fault-output (EOUT) timer is running (see How
the fault-output (EOUT) timer works in Bi-Stable fault-
output mode ).
• FCCU is in FAULT state.
Non-faulty All of the following are true:
• The fault-output (EOUT) timer is not running.
• FCCU is in ALARM or NORMAL state.
Table continues on the next page...

---

*Page 1410*

Fault Collection and Control Unit (FCCU)
Table continued from the previous page...
Condition Description
Configuration All of the following are true:
• The fault-output (EOUT) timer is not running.
• FCCU is in CONFIG state.
How the fault-output (EOUT) timer works in Bi-Stable fault-output mode
In Bi-Stable fault-output mode (FOM), FCCU starts the fault-output (EOUT) timer when all of the following are true:
• If the EOUT signals are in Bi-Stable FOM, and the EOUT signals are not programmed to be always
low ( CFG[FCCU_SET_CLEAR] ).
• The EOUT timer is not already running.
• FCCU enters the FAULT state as the result of a fault.
When the fault-output (EOUT) timer is already running and a new fault occurs:
• If FCCU is in the CONFIG state: FCCU does not restart the EOUT timer.
• If FCCU is in the NORMAL or ALARM state:
— And ALARM state is enabled for the fault (non-critical): FCCU enters (or remains in) the ALARM state but does not
restart the EOUT timer.
— And ALARM state is disabled for the fault (non-critical): FCCU enters the FAULT state and restarts the EOUT timer.
• If FCCU is in the FAULT state: FCCU restarts the fault-output (EOUT) timer.
FCCU stops and reinitializes the fault-output (EOUT) timer when all of the following are true:
• If the EOUT signals are in Bi-Stable fault-output mode ( CFG[FOM] ), and T min (see DELTA_T[DELTA_T] ) has expired.
• All faults that caused FCCU to enter or remain in the FAULT state since FCCU started the fault-output (EOUT) timer have
been cleared, causing FCCU to return to the NORMAL state.
Prepare the EOUT signals to indicate FCCU's condition
• If the EOUT signals are in Bi-Stable fault-output mode ( CFG[FOM] ), ensure that the EOUT signals are controlled by
FCCU's FSM ( CFG[FCCU_SET_CLEAR] ).
• Ensure that the EOUT signals are active ( CFG[FCCU_SET_AFTER_RESET] ).
NOTE
If the EOUT signals are in Bi-Stable fault-output mode, you must deactivate and then reactivate the EOUT signals
( CFG[FCCU_SET_AFTER_RESET] ) to correctly initialize them so they have opposite states.
More about the EOUT interface
Different fault-output modes (protocols) for the fault-output (EOUT) interface are supported ( CFG[FOM] ):
• Bi-Stable
NOTE
See the chip-specific FCCU information for the fault-output modes supported by this chip.
You can further configure the fault-output modes using the following attributes:

---

*Page 1411*

Fault Collection and Control Unit (FCCU)
Attribute Field Setting used in the example diagrams
and tables that follow
Configuration mode CFG[CM] Different
Polarity selection CFG[PS] For the faulty indication, EOUT1 is high,
and EOUT0 is low.
18
EOUT frequency: This frequency is generated by dividing the CLKSAFE frequency by a fixed factor of 2 .
CLKSAFE freq
EOUT freq =
18
2
For example, with a CLKSAFE frequency of 16 MHz, this drives a signal of 61 Hz on EOUT.
In case of a failure event or on software request for EOUT indication, the signal(s) are set to the faulty state for a minimum time
( T min ), even if software tries to release it before. If software configures the error pins to OK(1), and if a fault comes trying to
drive the pin to NOK(0), then priority is given to the fault indication and the error signals indicate NOK, such as an incoming fault
is not masked even when software has set the error signal to high. Also, if the error signals are forced to low by software by
writing to CFG[FCCU_SET_CLEAR] , then the signals shall remain low (or high) for the entire duration of T min . During the T min
by a non-software fault, the FCCU FSM moves independently of this signal state (low), and as soon as the timer expires, the
pin behavior is dictated by the state in which the FSM finds itself in, and it is not possible to set the signals to OK by software
moving FCCU to the CONFIG state, as long as this timer is running. No software intervention is needed to bring the signal from
the low state.
Software can bring the pin back to OK state by clearing the faults and waiting for the T min interval to expire, after which the FCCU
automatically enters the NORMAL state and the error signal indicates OK.
In case another failure event happens within T min after a first one, the T min counter is restarted.
49.3.5.1 Bi-Stable protocol
The encoding scheme is provided in Table 230 and the related timing diagram is shown in Figure 185 .
Table 230. Bi-Stable encoding
Condition EOUT[1:0] ( CFG[PS] is 0) EOUT[1:0] ( CFG[PS] is 1)
Non-faulty Static 01 Static 10
Faulty Static 10 Static 01
1
Reset High-impedance (no toggling) High-impedance (no toggling)
2
Configuration High-impedance (no toggling) High-impedance (no toggling)
( CFG[CM] is 1 and
CFG[FCCU_SET_AFT
ER_RESET] is 0)
Configuration (When Static 01 Static 10
CFG[CM] is 1 and
CFG[FCCU_SET_AFT
ER_RESET] is 1)
1. Final value depends on device specific settings at pad level.
2. Ensure that the EOUT signals are active ( CFG[FCCU_SET_AFTER_RESET] ); otherwise, the EOUT signals stay in a
high-impedance state after reset lifts.

---

*Page 1412*

Fault Collection and Control Unit (FCCU)
NOTE
Figure 185 is formatted to display the behavior in all four conditions (reset, non-faulty, faulty, and configuration),
not to imply transitions between one condition and another. In particular, a transition from the faulty condition to the
configuration condition is not possible.
Reset phase
Non-faulty phase Faulty phase
Configuration phase
or self-test
EOUT[0]
EOUT[1]
Input Output
or high-Z
Figure 185. Bi-Stable protocol

#### 49.3.6 Modes of operation

49.3.6.1 Put FCCU in the NORMAL state
49.3.6.1.1 Introduction
You put FCCU in the NORMAL state to save changes to the configuration, and to allow FCCU to enter the ALARM or FAULT state
when a fault occurs on an enabled fault channel.
49.3.6.1.2 About putting FCCU in NORMAL state
When putting FCCU in the NORMAL state:
• If you attempt to lock the configuration while FCCU is in CONFIG state, FCCU does not actually lock the configuration until
FCCU leaves CONFIG state—that is, either you put FCCU in the NORMAL state, or FCCU puts itself in the NORMAL state
because the Configuration-state timeout interval ( CFG_TO[TO] ) expires.
• After you permanently lock the configuration, you must reset FCCU before you can put FCCU in the CONFIG state.
49.3.6.1.3 Put FCCU in the NORMAL state
1. Check the FCCU status ( STAT[STATUS] ).
• If the FCCU status is NORMAL, go to step 6 .
• If the FCCU status is CONFIG, go to step 2 .
• If the FCCU status is ALARM or FAULT, go to step 4 .
2. Run the OP2 operation (see Run an operation ).
3. Check the operation status ( CTRL[OPS] ).
• If the operation status is Successful, FCCU is in the NORMAL state. Go to step 6 .
• If the operation status is Aborted, go to step 2 .
4. Recover all faults (see Fault recovery ).
5. Go to step 1 .
6. Lock the configuration if you want to prevent any changes to it:
• To require a key to unlock the configuration, from Supervisor mode, temporarily lock the
configuration ( TRANS_LOCK[TRANSKEY] ).

---

*Page 1413*

Fault Collection and Control Unit (FCCU)
• To require a reset of FCCU to unlock the configuration, from Supervisor mode, permanently lock the
configuration ( PERMNT_LOCK[PERMNTKEY] ).
The configuration is permanently locked until FCCU is reset.
49.3.6.2 Manage faults
49.3.6.2.1 Introduction
After saving changes to the configuration, you are ready to use FCCU to manage faults.
49.3.6.2.2 Determine if there are any unrecovered non-critical faults
Check the unrecovered-fault indicators for the non-critical faults ( NCF_Sa[NCFSn] ).
49.3.6.2.3 Recover a software-recoverable non-critical fault
1. Resolve the source of the software-recoverable non-critical fault.
2. Unlock the NCF_Sa registers ( NCFK[NCFK] ) using a 32-bit write.
3. Initiate clearing of the unrecovered-fault indicator for the software-recoverable non-critical fault ( NCF_Sa[NCFSn] ) using
a 32-bit write.
FCCU initiates the OP12 operation.
NOTE
If you want to clear multiple unrecovered-fault indicators and those indicators reside in different NCF_Sa registers,
you must perform steps 2 and 3 for each individual register.
4. Check the operation status ( CTRL[OPS] ).
• If the operation status is In Progress, go to step 4 .
• If the operation status is Successful, go to step 5 .
• If the operation status is Aborted, go to step 2 .
5. Check the unrecovered-fault indicator for the software-recoverable non-critical fault ( NCF_Sa[NCFSn] ).
• If the indicator indicates no unrecovered fault, the fault has been recovered. Stop.
• If the indicator still indicates an unrecovered fault, go to step 2 .
49.3.6.2.4 Clear the freeze-status indicators
1. Run the OP13 operation (see Run an operation ).
2. Check the operation status ( CTRL[OPS] ).
• If the operation status is Successful, stop .
• If the operation status is Aborted, go to step 1 .
49.3.6.3 Run operations
49.3.6.3.1 Introduction
You run operations to perform actions such as putting FCCU in the CONFIG state or setting the operation status to Idle. For a
complete list of operations you can run, see CTRL[OPR] .

---

*Page 1414*

Fault Collection and Control Unit (FCCU)
49.3.6.3.2 About running operations
When running operations:
• FCCU ignores any operations initiated while the operation status is In Progress.
• Certain operations must be unlocked before you can initiate them. After you initiate them, they are locked again.
49.3.6.3.3 Run an operation
1. Check the operation status ( CTRL[OPS] ).
2. Go to step 1 if the operation status is In Progress.
3. Unlock the operation ( CTRLK[CTRLK] ) using a 32-bit write, if the operation must be unlocked before you can initiate it.
NOTE
The Control Key (CTRLK) register used in this step and the Control (CTRL) register used in the next step must
be written with consecutive instructions. Do not use read-modify-write instructions, such as bit-field instructions, to
modify these registers.
4. Initiate the operation ( CTRL[OPR] ) using a 32-bit write.
5. Check the operation status ( CTRL[OPS] ).
6. Go to step 5 if the operation status is In Progress.
The operation status is now Idle (OP15 only), Aborted, or Successful.

#### 49.3.7 FOSU

The FOSU provides a supervision of the primary fault notification path by analyzing FCCU's behavior for correctness. It waits for
any reaction of the FCCU in a fixed time window after a fault is signaled.
The intention of the FOSU is to provide a secondary fault reaction path in most cases when the FCCU fails but not to needlessly
propagate a fault which is already handled by the FCCU in a full chip reset. Only a failed primary fault reaction (that is, FCCU's
failure) is a reason for the secondary reaction to take over (and generate a destructive reset request).
There is a 'do nothing' input coming from FCCU which indicates that the FCCU is programmed for no reaction for ALL FAULTS. It
is a "static" input in the sense that it does not change after FCCU configuration. The FOSU masks the incoming faults with the 'do
nothing' control from the FCCU, meaning that a fault is not captured by the FOSU if the 'do nothing' signal is asserted (that is, a
disabled fault). There is no minimum pulse width requirement on the fault indication other than what is required by the technology,
which is the same as that of the FCCU. FOSU does not monitor FCCU for the case of faults occurring during the CONFIG state.
The FOSU contains a timer with a duration of FOSU_COUNT, driven by CLKSAFE. The timer is initialized and started on any
captured, enabled fault. While the timer is running, any subsequent captured fault will neither restart nor reinitialize the timer.
The timer is stopped when the FCCU shows any of the following reactions (the FOSU does not check whether the reaction is the
configured one for the faults which occurred):
• Reset: short functional reset
• IRQ (triggered by ALARM state)
• NMI
• Error out triggered (by FCCU or by SW)
When the timer is stopped, the fault capture logic is cleared to ensure that the timer is not restarted because of faults still 'stuck' in
the capture logic. The timer is then restarted by the next new failure indication. When the timer expires, the FOSU's failure indicator
output is asserted after it ensures that the fault is enabled and the static "fccu program to do nothing" signal is deasserted. This
is because FCCU uses settings after it exits CONFIG state, even if fault captured before the exit.
The FOSU's failure indicator output is connected to one of the MC_RGM's 'destructive' reset inputs, so its assertion will cause a
reset sequence to be initiated starting at DEST0. The FOSU module is reset with the same reset as is used by the FCCU. When

---

*Page 1415*

Fault Collection and Control Unit (FCCU)
this reset is asserted, the FOSU's capture logic is cleared, its timer is kept stopped and in a non-expired state, and its failure
indicator output is deasserted.
NOTE
FOSU is triggered on assertion of enabled fault. In case the triggering fault is disabled, FOSU times out
without reaction.
SW
SW
Watchdog
Watchdog
CPU 0 CPU 1
Out of
Out of
Time
Time
IRQ
FOSU
External
FIF
FCCU EOUT[0]
Device
MC_RGM
EOUT[1]
Reset
Note: The fault sources shown are examples only.
Figure 186. FOSU connections to the FCCU and MC_RGM

#### 49.3.8 Clocking

This module has no clocking considerations.

#### 49.3.9 Interrupts

This module has no interrupts.

#### 49.4 External signals

FCCU interfaces with the EOUT pin. Signaling on the EOUT pin depends on whether the module is processing an error or is idle.

#### 49.5 Initialization

#### 49.5.1 Prepare FCCU for configuration

49.5.1.1 Introduction
You prepare FCCU for configuration by first configuring the CONFIG state and then putting FCCU in that state.

---

*Page 1416*

Fault Collection and Control Unit (FCCU)
49.5.1.2 About preparing FCCU for configuration
When preparing FCCU for configuration, keep the following in mind:
• To put FCCU in CONFIG state, FCCU must be in NORMAL state.
• After FCCU is reset, the configuration is temporarily locked. You must temporarily unlock the configuration before you can put
FCCU in the CONFIG state.
• When FCCU is in the CONFIG state, FCCU does not actually save the changes you make to the configuration. To save
changes to the configuration, you must manually put FCCU in the NORMAL state. If FCCU automatically leaves the CONFIG
state and enters the NORMAL state because the Configuration-state timeout interval ( CFG_TO[TO] ) expires (called a
Configuration-state timeout), FCCU changes the value of the Configuration (CFG) register to its Configuration-state-timeout
value and the value of each of the other configuration registers to its reset value. FCCU also changes the value of the
Configuration-State Timeout Interval (CFG_TO) register to its reset value. For information on the Configuration-state timeout
value, see CFG register bit value at different events . For a list of configuration registers, see Configuration registers .
49.5.1.3 Configure the CONFIG state
1. Set the Configuration-state timeout interval ( CFG_TO[TO] ).
2. Enable the Configuration-state-timeout interrupt signal ( IRQ_EN[CFG_TO_IEN] ), if you want FCCU to request an
interrupt when a Configuration-state timeout occurs.
49.5.1.4 Put FCCU in the CONFIG state
1. Unlock the configuration temporarily ( TRANS_LOCK[TRANSKEY] ) in Supervisor mode.
2. Check the FCCU status ( STAT[STATUS] ).
• If the FCCU status is CONFIG, FCCU is in the CONFIG state. Stop.
• If the FCCU status is NORMAL, go to step 3 .
• If the FCCU status is ALARM or FAULT, go to step 5 .
3. Run the OP1 operation (see Run an operation ).
4. Check the operation status ( CTRL[OPS] ).
• If the operation status is Successful, FCCU is in the CONFIG state. Stop.
• If the operation status is Aborted, the configuration is probably permanently locked. Go to step 7 .
5. Recover all faults (see Fault recovery ).
6. Go to step 2 .
7. Reset FCCU.

#### 49.5.2 Configure FCCU

49.5.2.1 Introduction
You configure FCCU so it functions according to the needs of your particular application.
49.5.2.2 About configuring FCCU
When configuring FCCU:
• If you enable a non-critical fault channel but disable all reactions for that channel, FCCU changes state when necessary but
does not perform any reaction because reactions are disabled. If you enable reactions for a non-critical fault channel but
disable that channel, and FCCU is in the NORMAL state when a fault occurs on the channel, FCCU does not enter the ALARM
or FAULT state and therefore does not perform any reaction.

---

*Page 1417*

Fault Collection and Control Unit (FCCU)
49.5.2.3 Configure the non-critical fault channels
For each non-critical fault channel that you want FCCU to monitor:
1. Set the recovery type ( NCF_CFGa[NCFCn] ).
2. Enable at least one type of Fault-state reaction:
• Chip functional reset ( NCFS_CFGa[NCFSCn] )
• Non-maskable interrupt ( NMI_ENa[NMIENn] )
• EOUT signaling ( EOUT_SIG_ENa[EOUTENn] )
NOTE
If you enable the chip functional reset as the type of Fault-state reaction for a channel, enable at least one other
type of Fault-state reaction for the channel or enable the ALARM state (step 4 ) for the channel.
3. Set the Alarm-state timeout interval ( NCF_TO[TO] ), if you plan to enable the ALARM state for any non-critical fault channel.
NOTE
Ensure that the Alarm-state timeout interval is less than the FOSU module's timeout interval; otherwise, FOSU
generates a chip reset every time a fault occurs on the channel. The FOSU timeout interval (FOSU_COUNT) is a
chip-specific value. See the chip-specific FCCU information.
4. Enable the ALARM state ( NCF_TOEa[NCFTOEn] ) for any non-critical fault channel for which you want FCCU to enter the
ALARM state before entering the FAULT state.
5. Enable the Alarm-state reaction ( IRQ_ALARM_ENa[IRQENn] ) for each non-critical fault channel for which you enabled the
ALARM state.
6. Enable the corresponding NCF_Ea[NCFEn] field for each non-critical fault channel that you want FCCU to monitor.

#### 49.6 Application information

#### 49.6.1 Use cases and limitations

Configuration guidelines
Follow these guidelines to configure FCCU:
• If you want FCCU to react to a fault on a non-critical fault channel:
— Enable the channel ( Non-critical Fault Enable (NCF_E0) ).
— Enable at least one type of Fault-state reaction for the channel: chip reset ( Non-critical Fault-State Configuration
(NCFS_CFG0) ), fault-output (EOUT) signaling ( Non-critical Fault-State EOUT Signaling Enable (EOUT_SIG_EN0) ),
or non-maskable interrupt ( Non-critical Fault-State Non-maskable-Interrupt-Request Enable (NMI_EN0) ).
— If you enable chip reset as the type of Fault-state reaction for the channel ( Non-critical Fault-State Configuration
(NCFS_CFG0) ), enable either ALARM state ( Non-critical-Fault Alarm-State Timeout Enable (NCF_TOE0) ) or at least
one other type of Fault-state reaction for the channel: fault-output (EOUT) signaling ( Non-critical Fault-State EOUT
Signaling Enable (EOUT_SIG_EN0) ) or non-maskable interrupt ( Non-critical Fault-State Non-maskable-Interrupt-
Request Enable (NMI_EN0) ).
— If you enable ALARM state for the channel ( Non-critical-Fault Alarm-State Timeout Enable (NCF_TOE0) ), enable the
Alarm-state reaction ( Non-critical Alarm-State Interrupt-Request Enable (IRQ_ALARM_EN0) ).
— If you enable ALARM state for the channel ( Non-critical-Fault Alarm-State Timeout Enable (NCF_TOE0) ), make
sure the Alarm-state timer interval ( Non-critical-Fault Alarm-State Timeout Interval (NCF_TO) ) is less than the FOSU
module's timer interval; otherwise, FOSU generates a chip reset every time a fault occurs on the channel. The FOSU
timer interval (FOSU_COUNT) is chip-specific. See the chip-specific FCCU information.

---

*Page 1418*

Fault Collection and Control Unit (FCCU)
Recommendations to configure FCCU
1. After a power on, or 'destructive' reset (when initiated by the assertion of the chip reset pin, RESET_B), where both
system and FCCU are reset, the following steps could be followed to configure FCCU:
a. Check and clear any pending fault status
b. Verify FCCU is in NORMAL state, else repeat step(a) above
c. Configure FCCU
2. After any 'functional' reset of the system, arising out of a reset request from FCCU or other sources, the following steps
could be followed to reconfigure FCCU:
a. If active, wait for the Error out T min to expire
b. Check and clear fault status
c. Error pin moves to "non faulty" state, once fault status is cleared and T min expires
d. Verify FCCU is in NORMAL state, else repeat step(a) above
e. Read and verify value in NCF_En
f. Reconfigure FCCU, if necessary

#### 49.7 Register descriptions

#### 49.7.1 FCCU register descriptions

The FCCU registers are listed in the table below. Any address offset not explicitly mentioned in this table is reserved.
The FCCU supports word (32-bit), half-word (16-bit), and byte (8-bit) read and write accesses.
Follow these register-access guidelines:
• Do not read from or write to any addresses that are not shown in the following table. Doing so may or may not result in a
transfer error.
• Do not write to any of the configuration registers unless FCCU is in the CONFIG state. Doing so results in a transfer error.
• Do not write to the Transient Configuration Lock (TRANS_LOCK) or Permanent Configuration Lock (PERMNT_LOCK)
registers unless your code runs in the Supervisor mode. Doing so results in a transfer error.
For each possible NCF failure source, a different reaction—including no reaction—is configurable through the use of NMI, IRQ,
and short reset selection registers. It is not possible for a single event upset to switch off all reactions on failures as implementation
is per fault source (but it will be possible to switch them all off by SW if intended). Failures themselves are not able to disable all
reactions and indications.
49.7.1.1 FCCU memory map
FCCU base address: 4038_4000h
Offset Register Access Reset value
Width
(In bits)
0h Control (CTRL) 32 RW 0000_00C0h
4h Control Key (CTRLK) 32 W 0000_0000h
8h Configuration (CFG) 32 RW 0000_0000h
1Ch Non-critical Fault Configuration (NCF_CFG0) 32 RW 0000_00FFh
Table continues on the next page...

---

*Page 1419*

Fault Collection and Control Unit (FCCU)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
4Ch Non-critical Fault-State Configuration (NCFS_CFG0) 32 RW 0000_0000h
80h Non-critical Fault Status (NCF_S0) 32 RW 0000_0000h
90h Non-critical Fault Key (NCFK) 32 RW 0000_0000h
94h Non-critical Fault Enable (NCF_E0) 32 RW 0000_0000h
A4h Non-critical-Fault Alarm-State Timeout Enable (NCF_TOE0) 32 RW 0000_00FFh
B4h Non-critical-Fault Alarm-State Timeout Interval (NCF_TO) 32 RW 0003_A980h
B8h Configuration-State Timeout Interval (CFG_TO) 32 RW 0000_0005h
BCh IO Control (EINOUT) 32 RW See section
C0h Status (STAT) 32 R 0000_0010h
C4h Normal-to-Alarm Freeze Status (N2AF_STATUS) 32 R 0000_0000h
C8h Alarm-to-Fault Freeze Status (A2FF_STATUS) 32 R 0000_0000h
CCh Normal-to-Fault Freeze Status (N2FF_STATUS) 32 R 0000_0000h
D0h Fault-to-Alarm Freeze Status (F2AF_STATUS) 32 R 0000_0000h
DCh Non-critical Fault Fake (NCFF) 32 W 0000_0000h
E0h IRQ Status (IRQ_STAT) 32 RW 0000_0000h
E4h IRQ Enable (IRQ_EN) 32 RW 0000_0000h
F0h Transient Configuration Lock (TRANS_LOCK) 32 W 0000_0000h
F4h Permanent Configuration Lock (PERMNT_LOCK) 32 W 0000_0000h
F8h Delta T (DELTA_T) 32 RW 0000_0000h
FCh Non-critical Alarm-State Interrupt-Request Enable 32 RW 0000_0000h
(IRQ_ALARM_EN0)
10Ch Non-critical Fault-State Non-maskable-Interrupt-Request Enable 32 RW 0000_0000h
(NMI_EN0)
11Ch Non-critical Fault-State EOUT Signaling Enable (EOUT_SIG_EN0) 32 RW 0000_0000h
12Ch Alarm-State Timer (TMR_ALARM) 32 R 0003_A980h
134h Configuration-State Timer (TMR_CFG) 32 R 000F_FFFFh
138h Fault-Output Timer (TMR_ETMR) 32 R 0000_0000h

---

*Page 1420*

Fault Collection and Control Unit (FCCU)
49.7.1.2 Control (CTRL)
Offset
Register Offset
CTRL 0h
Function
Initiates and indicates the status of operations—and enables the Debug mode.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 OPS 0
DEBU
OPR
G
W
Reset 0 0 0 0 0 0 0 0 1 1 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
Reserved
30-29
—
Reserved
28-10
—
9 Debug Mode Enable
DEBUG Specifies whether the Debug mode is enabled. If so, FCCU enters the Debug mode when the Debug signal
is asserted. When FCCU enters the Debug mode, it halts operation and remains in the state it was in before
it entered this mode.
NOTE
FOSU does not halt when FCCU enters the Debug mode. Therefore, FOSU can still cause
a reset if a fault occurs while FCCU is in the Debug mode.
0b - Disabled
Table continues on the next page...

---

*Page 1421*

Fault Collection and Control Unit (FCCU)
Table continued from the previous page...
Field Function
1b - Enabled
Reserved
8
—
7-6 Operation Status
OPS This field can be read and cleared (via OP15 operation) by the software.
00b - Idle
01b - In progress
10b - Aborted
11b - Successful
Reserved
5
—
4-0 Operation Run
OPR Initiates operations that perform actions such as putting FCCU in the CONFIG state or setting the operation
status to Idle. For information on how to run operations, see Run operations .
FCCU ignores any write to this field while the operation status ( CTRL[OPS] ) is "In progress". After
completion of an operation, FCCU sets this field to OP0.
The following events result in an operation status ( CTRL[OPS] ) of "Aborted":
• Writing to a NCF_Sa register (which automatically initiates the OP12 operation) without first
successfully unlocking the register ( NCFK[NCFK] )
• Initiating an OP1 operation when FCCU is not in the NORMAL state or the configuration is locked
• Initiating an OP1, or OP2, operation without first unlocking the operation ( CTRLK[CTRLK] )
00000 OP0—No operation
00001 OP1—Applies only when the configuration is unlocked, when FCCU is in the NORMAL state, and
immediately after you unlock the operation ( CTRLK[CTRLK] ). Put FCCU in the CONFIG state.
00010 OP2—Applies only immediately after you unlock the operation ( CTRLK[CTRLK] ). Put FCCU in the
NORMAL state.
00011 Reserved
00100 Reserved
00101 Reserved
00110 Reserved
00111 Reserved
01000 Reserved
01001 Reserved
01010 Reserved
Table continues on the next page...

---

*Page 1422*

Fault Collection and Control Unit (FCCU)
Table continued from the previous page...
Field Function
01011 Reserved
01100 OP12—Do not initiate this operation; it is automatically initiated by the FCCU. A NCF_Sa register
status clear operation is in progress.
01101 OP13—Clear the freeze status registers.
01110 OP14—Do not initiate this operation; it is automatically initiated by the FCCU. A Configuration-state
timeout is in progress. For more information, see Configuration registers .
01111 OP15—Set the operation status ( CTRL[OPS] ) to Idle.
10000 Reserved
10001 Reserved
10010 Reserved
10011 Reserved
10100 Reserved
10101—11110 Forbidden. Writing any of these values returns an operation status ( CTRL[OPS] ) of
"Aborted" with no side effect.
11111 Reserved
49.7.1.3 Control Key (CTRLK)
Offset
Register Offset
CTRLK 4h
Function
See CTRLK[CTRLK] .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
W CTRLK
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
W CTRLK
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1423*

Fault Collection and Control Unit (FCCU)
Fields
Field Function
31-0 Locked-Operation Control Key
CTRLK Writable only with a 32‑bit write. Unlocks locked operations ( CTRL[OPR] ) so you can initiate them. For
information on how to unlock locked operations before you initiate them, see Run an operation .
NOTE
• You must initiate an operation in the FCCU register access that immediately follows the
one that unlocks it; otherwise, the operation is again locked.
• Reading from this register always returns the value 0000_0000h.
Operations not listed here are not locked and do not need to be unlocked.
9137_56AFh: Unlock OP1.
825A_132Bh: Unlock OP2.
Any other value: Do nothing.
49.7.1.4 Configuration (CFG)
Offset
Register Offset
CFG 8h
Function
Writable only when FCCU is in the CONFIG state. Changed by FCCU to another value when the chip resets FCCU, a
Configuration-state timeout occurs, or you run an OP31 operation. See CFG register bit value at different events for more
information. Specifies the global configuration for FCCU.
NOTE
If you specify a new value for any of the fields in this register that affect the EOUT signals while the fault-output
(EOUT) timer is running (FCCU is indicating a fault on the EOUT signals), FCCU does not use the new settings you
specified until after the fault-output (EOUT) timer expires (FCCU stops indicating a fault on the EOUT signals).

---

*Page 1424*

Fault Collection and Control Unit (FCCU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R Reserved Reserved Reserved
FCCU_ FCCU_SET_CL
SE ... EAR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
Reserv
R Reserved Reserved
Reserv Reserv
ed
CM PS FOM
ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-25
—
24 Fault-Output (EOUT) Activate
FCCU_SET_AF For fault-output (EOUT) signaling, controls whether the EOUT signals are active.
TER_RESET
0b - Inactive (the EOUT signals are in a high-impedance state)
1b - Active (the EOUT signals indicate FCCU's condition)
23-22 Fault-Output (EOUT) Control
FCCU_SET_CL Applies only to Bi-Stable fault-output mode ( CFG[FOM] ) and when the EOUT signals are active
EAR ( CFG[FCCU_SET_AFTER_RESET] ). Controls whether the fault-output (EOUT) signals are managed by
FCCU's FSM.
00b - Controlled by the FSM
01b - Always low
10b - Controlled by the FSM
11b - High until a fault occurs on a channel, regardless of whether that fault is disabled;
thereafter, controlled by the FSM. Note: FCCU ignores an attempt to write this value if the
fault-output (EOUT) timer is already running.
Reserved
21-20
—
Reserved
19-16
—
Reserved
15
—
Table continues on the next page...

---

*Page 1425*

Fault Collection and Control Unit (FCCU)
Table continued from the previous page...
Field Function
Reserved
14-13
—
12 Reserved
— Always write the reset value to this field.
11 Fault-Output (EOUT) Configuration-Indication Mode
CM For fault-output (EOUT) signaling, this field controls whether the configuration indication is the same as the
non-faulty indication.
0b - Different
1b - Same
Reserved
10
—
9 Fault-Output (EOUT) Polarity Selection
PS Applies to fault-output (EOUT) signaling and controls the polarity of the signals for fault-output mode
indications that hold the signals low or high (versus toggling them or placing them in a high-impedance
state). Applies only to Bi-Stable fault-output mode (for all indications).
0b - For the faulty indication, EOUT1 is high, and EOUT0 is low.
1b - For the faulty indication, EOUT1 is low, and EOUT0 is high.
8-6 Fault-Output (EOUT) Mode
FOM For fault-output (EOUT) signaling, controls the protocol of the signaling.
000b - Reserved
001b - Reserved
010b - Bi-Stable
011b - Reserved
100b - Reserved
101b - Test 0 (controlled by the EINOUT register; EOUT1 is an output; EOUT0 is an input)
110b - Test 1 (controlled by the EINOUT register; EOUT1 and EOUT0 are both outputs)
111b - Test 2 (controlled by the EINOUT register; EOUT1 is an input; EOUT0 is an output)
Reserved
5-0
—

---

*Page 1426*

Fault Collection and Control Unit (FCCU)
49.7.1.5 Non-critical Fault Configuration (NCF_CFG0)
Offset
Register Offset
NCF_CFG0 1Ch
Function
See NCF_CFGa[NCFCn] .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
NCFC NCFC NCFC NCFC NCFC NCFC NCFC NCFC
7 6 5 4 3 2 1 0
W
Reset 0 0 0 0 0 0 0 0 1 1 1 1 1 1 1 1
Fields
Field Function
Reserved
31-8
—
7-0 Non-critical Fault Configuration n
NCFCn Writable only when FCCU is in the CONFIG state. Changed by FCCU to its reset value when a
Configuration-state timeout occurs. Controls the recovery type (HW or SW) of the associated non-critical
fault channel (n). For information on how to configure the non-critical fault channels, see Configure the
non-critical fault channels .
NOTE
Configure a non-critical fault channel as hardware-recoverable only if the source continues
to indicate a fault on the fault channel's input (NCFn) until the condition that caused
the fault is no longer true; otherwise, configure the non-critical fault channel as software-
recoverable.
0b - Hardware-recoverable
1b - Software-recoverable

---

*Page 1427*

Fault Collection and Control Unit (FCCU)
49.7.1.6 Non-critical Fault-State Configuration (NCFS_CFG0)
Offset
Register Offset
NCFS_CFG0 4Ch
Function
See NCFS_CFGa[NCFSCn] .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
NCFSC7 NCFSC6 NCFSC5 NCFSC4 NCFSC3 NCFSC2 NCFSC1 NCFSC0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-16
—
15-14: NCFSC7 Non-critical Fault-State Configuration n
13-12: NCFSC6 Writable only when FCCU is in the CONFIG state. Changed by FCCU to its reset value when a
Configuration-state timeout occurs. Controls whether the chip functional reset is enabled as a Fault-state
11-10: NCFSC5
reaction for the associated non-critical fault channel (n). When the chip functional reset is enabled for an
9-8: NCFSC4 enabled non-critical fault channel, a fault on that channel causes FCCU to assert the rst_sfunc_b signal
when FCCU enters the FAULT state. For information on how to configure the non-critical fault channels, see
7-6: NCFSC3
Configure the non-critical fault channels .
5-4: NCFSC2
00b - Disabled
3-2: NCFSC1
01b - Enabled (rst_sfunc_b) (short)
1-0: NCFSC0
10b - Reserved
11b - Disabled

---

*Page 1428*

Fault Collection and Control Unit (FCCU)
49.7.1.7 Non-critical Fault Status (NCF_S0)
Offset
Register Offset
NCF_S0 80h
Function
See NCF_Sa[NCFSn] .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
NCFS NCFS NCFS NCFS NCFS NCFS NCFS NCFS
R 0
7 6 5 4 3 2 1 0
W W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-8
—
7-0 Non-critical Fault Status n
NCFSn Indicates whether there is an unrecovered fault on the associated non-critical fault channel (n).
NOTE
To recover a software-recoverable non-critical fault, which includes clearing its
unrecovered-fault indicator, see Recover a software-recoverable non-critical fault . FCCU
clears the unrecovered-fault indicator for a hardware-recoverable non-critical fault
automatically when the source no longer indicates a fault on the fault channel's input
signal; if you attempt to clear the unrecovered-fault indicator for a hardware-recoverable
non-critical fault, FCCU does not clear the indicator and does not indicate an error.
0b - No unrecovered fault
1b - Unrecovered fault

---

*Page 1429*

Fault Collection and Control Unit (FCCU)
49.7.1.8 Non-critical Fault Key (NCFK)
Offset
Register Offset
NCFK 90h
Function
See NCFK[NCFK] .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W NCFK
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W NCFK
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Non-critical Fault Key
NCFK Writable only with a 32‑bit write. Unlocks the NCF_Sa registers so you can write to them while recovering
a software-recoverable non-critical fault. For information on how to unlock the NCF_Sa registers before
writing to them, see Recover a software-recoverable non-critical fault .
NOTE
• You must write to one of the NCF_Sa registers immediately after unlocking it (that
is, in the FCCU register access that immediately follows the one that unlocks them);
otherwise the registers are again locked. If you want to write to multiple NCF_Sa
registers, you must unlock each register immediately before you write to it.
• Reading from this register always returns the value 0000_0000h.
AB34_98FEh: Unlock.
Any other value: Do nothing.

---

*Page 1430*

Fault Collection and Control Unit (FCCU)
49.7.1.9 Non-critical Fault Enable (NCF_E0)
Offset
Register Offset
NCF_E0 94h
Function
See NCF_Ea[NCFEn] .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
NCFE NCFE NCFE NCFE NCFE NCFE NCFE NCFE
7 6 5 4 3 2 1 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-8
—
7-0 Non-critical Fault Enable n
NCFEn Writable only when FCCU is in the CONFIG state. Changed by FCCU to its reset value when a
Configuration-state timeout occurs. Controls whether the associated non-critical fault channel (n) is
enabled. When a non-critical fault channel is enabled, a fault on that channel causes FCCU to leave the
NORMAL state and enter the FAULT state (or ALARM state if enabled for the channel). For information on
how to configure the non-critical fault channels, see Configure the non-critical fault channels .
0b - Disabled
1b - Enabled
49.7.1.10 Non-critical-Fault Alarm-State Timeout Enable (NCF_TOE0)
Offset
Register Offset
NCF_TOE0 A4h

---

*Page 1431*

Fault Collection and Control Unit (FCCU)
Function
See NCF_TOEa[NCFTOEn] .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
NCFT NCFT NCFT NCFT NCFT NCFT NCFT NCFT
OE7 OE6 OE5 OE4 OE3 OE2 OE1 OE0
W
Reset 0 0 0 0 0 0 0 0 1 1 1 1 1 1 1 1
Fields
Field Function
Reserved
31-8
—
7-0 Non-critical-Fault Alarm-State Timeout Enable n
NCFTOEn Writable only when FCCU is in the CONFIG state. Changed by FCCU to its reset value when a
Configuration-state timeout occurs. Controls whether the ALARM state is enabled for the associated
non-critical fault channel (n). When the ALARM state is enabled for an enabled non-critical fault channel,
a fault on that channel causes FCCU to leave the NORMAL state and enter the ALARM state instead
of FAULT state. If the fault is not recovered within the Alarm-state timeout interval, then FCCU leaves
the ALARM state and enters the FAULT state. For information on how to configure the non-critical fault
channels, see Configure the non-critical fault channels .
0b - Disabled
1b - Enabled
49.7.1.11 Non-critical-Fault Alarm-State Timeout Interval (NCF_TO)
Offset
Register Offset
NCF_TO B4h
Function
See NCF_TO[TO]

---

*Page 1432*

Fault Collection and Control Unit (FCCU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TO
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TO
W
Reset 1 0 1 0 1 0 0 1 1 0 0 0 0 0 0 0
Fields
Field Function
31-0 Non-critical-Fault Alarm-State Timeout Interval
TO Writable only when FCCU is in the CONFIG state. Changed by FCCU to its reset value when a
Configuration-state timeout occurs. Controls the maximum amount of time that FCCU can be in the
ALARM state ( T Max Alarm ) according to this equation:
T Max Alarm = TO × T CLKSAFE
where T CLKSAFE is the safe-clock period.
If FCCU enters the ALARM state (because a fault occurs on an enabled non-critical fault channel for which
the ALARM state is enabled) and this timeout interval expires (called an Alarm-state timeout), then FCCU
leaves the ALARM state and enters the FAULT state.
NOTE
Make sure the Alarm-state timeout interval is less than the FOSU module's timeout interval;
otherwise, a fault that occurs while FCCU is in the ALARM state can cause FOSU to
generate a chip reset. The FOSU timeout interval (FOSU_COUNT) is chip-specific. See the
chip-specific FCCU information.
49.7.1.12 Configuration-State Timeout Interval (CFG_TO)
Offset
Register Offset
CFG_TO B8h
Function
See CFG_TO[TO]

---

*Page 1433*

Fault Collection and Control Unit (FCCU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
TO
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0 1
Fields
Field Function
Reserved
31-3
—
2-0 Configuration-State Timeout Interval
TO Writable only when FCCU is in the NORMAL, ALARM, or FAULT state (not in the CONFIG state).
Changed by FCCU to its reset value when a Configuration-state timeout occurs. Not accessible while a
Configuration-state timeout (OP14 operation) is in progress. Controls the maximum amount of time that
FCCU can be in the CONFIG state ( T Max configuration ) according to this equation:
(TO + 13)
T Max configuration = T CLKSAFE × 2
where T CLKSAFE is the safe-clock period.
If you put FCCU in the CONFIG state and this timeout interval expires (called a Configuration-state timeout),
then FCCU:
• Automatically leaves the CONFIG state and enters the NORMAL state
• Changes the value of the Configuration (CFG) register to its Configuration-state-timeout value and the
value of each of the other configuration registers to its reset value. For information on the Configuration
(CFG) register's Configuration-state-timeout value, see CFG register bit value at different events . For
a list of configuration registers, see Configuration registers .
49.7.1.13 IO Control (EINOUT)
Offset
Register Offset
EINOUT BCh
Function
The EINOUT register allows the following operations typically in the NORMAL state:
• To control the EOUT[1] output level when the FCCU is configured in "Test1" or "Test0" fault output mode ( CFG[FOM] )

---

*Page 1434*

Fault Collection and Control Unit (FCCU)
• To control the EOUT[0] output level when the FCCU is configured in "Test1" or "Test2" fault output mode ( CFG[FOM] )
• to observe the state of signals at EIN[1:0] pins
The following table shows Bi-Stable encoding.
Table 231. Bi-Stable encoding
Mode = CFG[FOM] EOUT[0] EOUT[1]
Test1 output output
Test2 output input
Test0 input output
NOTE
Because of the resynchronization stage of the EOUT interface, there is a latency of a few CLKSAFE cycles
following a write/read operation of the EINOUT register.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 EIN1 EIN0 0
EOUT EOUT
1 0
W
1
Reset 0 0 0 0 0 0 0 0 0 0 u u 0 0 0 0
1. Reset value varies as per the corresponding EIN signal.
Fields
Field Function
Reserved
31-6
—
5 Error Input 1
EIN1 Indicates the state of the EIN1 signal.
NOTE
When IP’s SET_AFTER_RESET bit is 0, then corresponding pad is in High-Z state, in this
case values sampled on EIN1 will depend on board/pad pull up/down values.
0b - Low
1b - High
Table continues on the next page...

---

*Page 1435*

Fault Collection and Control Unit (FCCU)
Table continued from the previous page...
Field Function
4 Error Input 0
EIN0 Indicates the state of the EIN0 signal.
NOTE
When IP’s SET_AFTER_RESET bit is 0, then corresponding pad is in High-Z state, in this
case values sampled on EIN0 will depend on board/pad pull up/down values.
0b - Low
1b - High
Reserved
3-2
—
1 EOUT1
EOUT1 Error out 1 (significant only if the CFG.FOM = Test1 or Test0 => EOUT[1] configured in output mode).
The EOUT1 set/clear the respective EOUT[1] output signal if CFG.FOM = 110 or 101, otherwise it is a
"don't-care" value.
NOTE
When the configuration watchdog timer expires, FCCU changes the value of this field to its
reset value.
0b - force EOUT[1] = 0
1b - force EOUT[1] = 1
0 EOUT0
EOUT0 Error out 0 (significant only if the CFG.FOM = Test1 or Test2 => EOUT[0] configured in output mode). The
EOUT0 set/clear the respective EOUT[0] output signal if CFG.FOM = 110 or 111, otherwise it is a "don't
care" value.
NOTE
When the configuration watchdog timer expires, FCCU changes the value of this field to its
reset value.
0b - force EOUT[0] = 0
1b - force EOUT[0] = 1
49.7.1.14 Status (STAT)
Offset
Register Offset
STAT C0h

---

*Page 1436*

Fault Collection and Control Unit (FCCU)
Function
This register indicates the following:
• States that FCCU is driving on the EOUT signals
• Whether FCCU is in a faulty condition
• Current state of FCCU
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 PhysicErrorPin ESTAT STATUS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 0
Fields
Field Function
Reserved
31-6
—
5-4 EOUT Signal States
PhysicErrorPin Applies only when the EOUT signals are active ( CFG[FCCU_SET_AFTER_RESET] ). Indicates the states
that FCCU is driving on the EOUT signals.
00b - EOUT1 is low; EOUT0 is low.
01b - EOUT1 is low; EOUT0 is high.
10b - EOUT1 is high; EOUT0 is low.
11b - EOUT1 is high; EOUT0 is high.
3 FCCU Faulty Condition
ESTAT Indicates whether FCCU is in faulty condition (as indicated by the EOUT signals). For more information, see
The FCCU conditions .
0b - Not in faulty condition (in non-faulty or configuration condition)
1b - In faulty condition
2-0 FCCU State
STATUS Indicates the current state of FCCU
000b - NORMAL
Table continues on the next page...

---

*Page 1437*

Fault Collection and Control Unit (FCCU)
Table continued from the previous page...
Field Function
001b - CONFIG
010b - ALARM
011b - FAULT
100b - Reserved
101b - Reserved
110b - Reserved
111b - Reserved
49.7.1.15 Normal-to-Alarm Freeze Status (N2AF_STATUS)
Offset
Register Offset
N2AF_STATUS C4h
Function
See N2AF_STATUS[NAFS] .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 NAFS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-8
—
7-0 Normal-to-Alarm Freeze Status
Table continues on the next page...

---

*Page 1438*

Fault Collection and Control Unit (FCCU)
Table continued from the previous page...
Field Function
NAFS Used only for testing and debugging. Indicates whether FCCU left the NORMAL state and entered the
ALARM state since the last time this register was cleared and, if so, which non-critical fault caused FCCU
to do so.
NOTE
To clear this register and the other freeze status registers, see Clear the freeze-
status indicators .
00h: No Normal-to-Alarm-state transition (cleared)
01h: NCF0
10h: NCF1
...
7Fh: NCF126
80h: NCF127
...
FFh: Multiple Normal-to-Alarm-state transitions
49.7.1.16 Alarm-to-Fault Freeze Status (A2FF_STATUS)
Offset
Register Offset
A2FF_STATUS C8h
Function
Used only for testing and debugging. Indicates whether FCCU left the ALARM state and entered the FAULT state since the last
time this register was cleared and, if so, which type of fault caused FCCU to do so.
NOTE
To clear this register and the other freeze status registers, see Clear the freeze-status indicators .

---

*Page 1439*

Fault Collection and Control Unit (FCCU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 AF_SRC AFFS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-10
—
9-8 Alarm-to-Fault Source
AF_SRC Used only for testing and debugging. Indicates the type of fault that caused FCCU to leave the ALARM state
and enter the FAULT state since the last time this register was cleared.
NOTE
To clear this register and the other freeze status registers, see Clear the freeze-
status indicators .
00b - No Alarm-to-Fault-state fault
01b - Reserved
10b - Non-critical fault
11b - Multiple Alarm-to-Fault-state faults
7-0 Alarm-to-Fault Freeze Status
AFFS Used only for testing and debugging. Indicates whether FCCU left the ALARM state and entered the FAULT
state since the last time this register was cleared and, if so, which fault caused FCCU to do so.
NOTE
To clear this register and the other freeze status registers, see Clear the freeze-
status indicators .
00h: No Alarm-to-Fault-state transition (cleared)
01h: NCF0 (due to an Alarm-state timeout)
10h: NCF1 (due to an Alarm-state timeout)
...
7Fh: NCF126 (due to an Alarm-state timeout)
Table continues on the next page...

---

*Page 1440*

Fault Collection and Control Unit (FCCU)
Table continued from the previous page...
Field Function
80h: NCF127 (due to an Alarm-state timeout)
...
FFh: Multiple Alarm-to-Fault-state transitions
49.7.1.17 Normal-to-Fault Freeze Status (N2FF_STATUS)
Offset
Register Offset
N2FF_STATUS CCh
Function
Used only for testing and debugging. Indicates whether FCCU left the NORMAL state and entered the FAULT state since the last
time this register was cleared and, if so, which type of fault caused FCCU to do so.
NOTE
To clear this register and the other freeze status registers, see Clear the freeze-status indicators .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 NF_SRC NFFS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-10
—
9-8 Normal-to-Fault Source
NF_SRC Used only for testing and debugging. Indicates the type of fault that caused FCCU to leave the NORMAL
state and enter the FAULT state since the last time this register was cleared.
Table continues on the next page...

---

*Page 1441*

Fault Collection and Control Unit (FCCU)
Table continued from the previous page...
Field Function
NOTE
To clear this register and the other freeze status registers, see Clear the freeze-
status indicators .
00b - No Normal-to-Fault-state fault
01b - Reserved
10b - Non-critical fault
11b - Multiple Normal-to-Fault-state faults
7-0 Normal-to-Fault Freeze Status
NFFS Used only for testing and debugging. Indicates whether FCCU left the NORMAL state and entered the
FAULT state since the last time this register was cleared and, if so, which fault caused FCCU to do so.
NOTE
To clear this register and the other freeze status registers, see Clear the freeze-
status indicators .
00h: No Normal-to-Fault-state transition (cleared)
01h: NCF0
10h: NCF1
...
7Fh: NCF126
80h: NCF127
...
FFh: Multiple Normal-to-Fault-state transitions
49.7.1.18 Fault-to-Alarm Freeze Status (F2AF_STATUS)
Offset
Register Offset
F2AF_STATUS D0h
Function
See F2AF_STATUS[FAFS] .

---

*Page 1442*

Fault Collection and Control Unit (FCCU)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 FAFS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-9
—
8-0 Fault-to-Alarm Freeze Status
FAFS Used only for testing and debugging. Indicates whether FCCU left the FAULT state and entered the ALARM
state since the last time this register was cleared and, if so, which non-critical fault caused FCCU to do so.
NOTE
To clear this register and the other freeze status registers, see Clear the freeze-
status indicators .
00h: No Fault-to-Alarm-state transition (cleared)
01h: NCF0
10h: NCF1
...
7Fh: NCF126
80h: NCF127
...
FFh: Multiple Fault-to-Alarm-state transitions
49.7.1.19 Non-critical Fault Fake (NCFF)
Offset
Register Offset
NCFF DCh

---

*Page 1443*

Fault Collection and Control Unit (FCCU)
Function
This register contains a unique code to set a non-critical fault in mutually exclusive mode by the external FAULT interface (signal
setting). It allows the SW emulation of the non-critical faults, by injecting the fault directly in the FAULT root, to verify the entire
path and reaction. The reaction following a fake non-critical fault cannot be masked.
This is a write-only register with a set of codes corresponding to each non-critical fault injection.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W FNCFC
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-7
—
6-0 FNCFC
FNCFC Fake non-critical fault code
NOTE
Writing to this field injects fake faults; writing 00 and the default value being 0 renders
different results.
00h: Fake non-critical fault injection at non-critical fault source 0
01h: Fake non-critical fault injection at non-critical fault source 1
02h: Fake non-critical fault injection at non-critical fault source 2
...
7Fh: Fake non-critical fault injection at non-critical fault source 127
49.7.1.20 IRQ Status (IRQ_STAT)
Offset
Register Offset
IRQ_STAT E0h

---

*Page 1444*

Fault Collection and Control Unit (FCCU)
Function
This register provides the FCCU interrupt status related to the following events:
• Configuration-state timeout error
• Alarm interrupt
• NMI interrupt
The configuration-state timeout interrupt is asserted if both IRQ_STAT[CFG_TO_STAT] and IRQ_EN[CFG_TO_IEN] bits are
asserted. It is cleared when a 1 is written to the IRQ_STAT[CFG_TO_STAT] bit.
The NMI and ALARM interrupts are asserted and cleared according to the FCCU state. The status bits of the IRQ_STAT trace the
status of the related interrupt lines.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
Reserv Reserv NMI_ ALRM CFG_T
R 0
ed ed STAT _ST ... O_ ...
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-5
—
Reserved
4
—
Reserved
3
—
NMI Interrupt Status
2
0b - NMI interrupt is OFF
NMI_STAT
1b - NMI interrupt is ON
Alarm Interrupt Status
1
0b - Alarm interrupt is OFF
ALRM_STAT
1b - Alarm interrupt is ON
Configuration-State Timeout Status
0
Table continues on the next page...

---

*Page 1445*

Fault Collection and Control Unit (FCCU)
Table continued from the previous page...
Field Function
CFG_TO_STAT 0b - No configuration-stat timeout error
1b - Configuration-state timeout error
49.7.1.21 IRQ Enable (IRQ_EN)
Offset
Register Offset
IRQ_EN E4h
Function
This register is used to configure enabling of interrupt related to the "Configuration-state timeout error".
The configuration-state timeout interrupt is asserted if both the IRQ_STAT[CFG_TO_STAT] and IRQ_EN[CFG_TO_IEN] fields are
set to 1.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
CFG_T
O_ ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-4
—
Reserved
3
—
Reserved
2-1
—
Table continues on the next page...

---

*Page 1446*

Fault Collection and Control Unit (FCCU)
Table continued from the previous page...
Field Function
Configuration-State Timeout Interrupt Enable
0
0b - Configuration-state timeout interrupt disabled
CFG_TO_IEN
1b - Configuration-state timeout interrupt enabled
49.7.1.22 Transient Configuration Lock (TRANS_LOCK)
Offset
Register Offset
TRANS_LOCK F0h
Function
See TRANS_LOCK[TRANSKEY]
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W TRANSKEY
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-9
—
8-0 Transient Configuration Lock
TRANSKEY Writable only by code running in Supervisor mode. Temporarily locks and unlocks the configuration. Locking
the configuration prevents FCCU from entering the CONFIG state. For information about putting FCCU in
configuration, see Prepare FCCU for configuration and Configure FCCU .
Table continues on the next page...

---

*Page 1447*

Fault Collection and Control Unit (FCCU)
Table continued from the previous page...
Field Function
NOTE
You can write to this field when FCCU is in any state, but the lock will not get into effect until
FCCU is in the NORMAL state.
BCh: Unlock.
Any other value: Lock.
49.7.1.23 Permanent Configuration Lock (PERMNT_LOCK)
Offset
Register Offset
PERMNT_LOCK F4h
Function
See PERMNT_LOCK[PERMNTKEY]
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W PERMNTKEY
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-9
—
8-0 Permanent Configuration Lock
PERMNTKEY Writable only by code running in the Supervisor mode. Permanently locks the configuration, which prevents
FCCU from entering the CONFIG state until FCCU is reset. For information about putting FCCU in
configuration, see Prepare FCCU for configuration and Configure FCCU .
Table continues on the next page...

---

*Page 1448*

Fault Collection and Control Unit (FCCU)
Table continued from the previous page...
Field Function
NOTE
You can write to this field when FCCU is in any state, but the lock will not get into effect until
FCCU is in NORMAL state.
FFh: Lock.
Any other value: Do nothing.
49.7.1.24 Delta T (DELTA_T)
Offset
Register Offset
DELTA_T F8h
Function
The DELTA_T register is used for programming the value of delta_T constant (see DELTA_T ), in microseconds.
NOTE
This register can be written only when the FCCU is in the CONFIG state.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R Reserved Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R Reserved
DELTA_T
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-30
—
Reserved
29-16
Table continues on the next page...

---

*Page 1449*

Fault Collection and Control Unit (FCCU)
Table continued from the previous page...
Field Function
—
Reserved
15-14
—
13-0 Minimum Fault-Output (EOUT) Timer Interval
DELTA_T Applies only to Bi-Stable mode ( CFG[FOM] ). Controls the minimum amount of time ( T min ) that the
fault-output (EOUT) timer runs according to this equation:
T min = (250+ DELTA_T) μs * (48000/CLKSAFE freq KHz )
NOTE
The durations shown for the DELTA_T values depend on CLKSAFE signals. For frequency
of CLKSAFE signals see the chip-specific FCCU information). Also see chip's data sheet for
the trimmed frequency variation (for example, δF var ).
49.7.1.25 Non-critical Alarm-State Interrupt-Request Enable (IRQ_ALARM_EN0)
Offset
Register Offset
IRQ_ALARM_EN0 FCh
Function
See IRQ_ALARM_ENa[IRQENn] .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
IRQEN IRQEN IRQEN IRQEN IRQEN IRQEN IRQEN IRQEN
7 6 5 4 3 2 1 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1450*

Fault Collection and Control Unit (FCCU)
Fields
Field Function
Reserved
31-8
—
7-0 Non-critical Alarm-State Interrupt-Request Enable n
IRQENn Writable only when FCCU is in the CONFIG state. Changed by FCCU to its reset value when a
Configuration-state timeout occurs. Controls whether the interrupt request is enabled as the Alarm-state
reaction for the associated non-critical fault channel (n). When the ALARM state and the Alarm-state
interrupt request are enabled for an enabled non-critical fault channel, a fault on that channel causes FCCU
to assert the irq_alarm signal when FCCU enters the ALARM state; irq_alarm remains asserted until FCCU
is in the NORMAL state. For information on how to configure the non-critical fault channels, see Configure
the non-critical fault channels .
0b - Disabled
1b - Enabled
49.7.1.26 Non-critical Fault-State Non-maskable-Interrupt-Request Enable (NMI_EN0)
Offset
Register Offset
NMI_EN0 10Ch
Function
See NMI_ENa[NMIENn] .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
NMIEN NMIEN NMIEN NMIEN NMIEN NMIEN NMIEN NMIEN
7 6 5 4 3 2 1 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1451*

Fault Collection and Control Unit (FCCU)
Fields
Field Function
Reserved
31-8
—
7-0 Non-critical Fault-State Non-maskable-Interrupt-Request Enable n
NMIENn Writable only when FCCU is in the CONFIG state. Changed by FCCU to its reset value when a
Configuration-state timeout occurs. Controls whether the non-maskable interrupt request is enabled as
a Fault-state reaction for the associated non-critical fault channel (n). When the non-maskable interrupt
request is enabled for an enabled non-critical fault channel, a fault on that channel causes FCCU to
assert the NMIOUT signal when FCCU enters the FAULT state; NMIOUT remains asserted until FCCU
exits FAULT state. For information on how to configure the non-critical fault channels, see Configure the
non-critical fault channels .
0b - Disabled
1b - Enabled
49.7.1.27 Non-critical Fault-State EOUT Signaling Enable (EOUT_SIG_EN0)
Offset
Register Offset
EOUT_SIG_EN0 11Ch
Function
See EOUT_SIG_ENa[EOUTENn] .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
EOUT EOUT EOUT EOUT EOUT EOUT EOUT EOUT
EN7 EN6 EN5 EN4 EN3 EN2 EN1 EN0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1452*

Fault Collection and Control Unit (FCCU)
Fields
Field Function
Reserved
31-8
—
7-0 Non-critical Fault-State EOUT Signaling Enable n
EOUTENn Writable only when FCCU is in the CONFIG state. Changed by FCCU to its reset value
when a Configuration-state timeout occurs. Applies only when the EOUT signals are active
( CFG[FCCU_SET_AFTER_RESET] ). When FCCU is configured for Bi-Stable fault-output mode
( CFG[FOM] ), controls whether EOUT signaling is enabled as a Fault-state reaction for the associated
non-critical fault channel (n). (For other fault-output modes, EOUT signaling is always enabled, regardless
of the value of this field.) When EOUT signaling is enabled for an enabled non-critical fault channel, a fault
on that channel causes FCCU to indicate the faulty condition on the EOUT[1:0] signals when FCCU enters
the FAULT state. For information on how to configure the non-critical fault channels, see Configure the
non-critical fault channels .
For all fault-output modes, also controls whether FCCU asserts the FIF signal when a fault on the associated
non-critical fault channel (n) causes FCCU to enter the FAULT state.
NOTE
For all fault-output modes, you must set this field to enabled to ensure that FCCU
asserts the FIF signal when FCCU enters FAULT state as the result of a fault on the
associated non-critical fault channel (n) so the FOSU module does not mistakenly generate
a destructive chip reset.
0b - In Bi-Stable fault-output mode, both EOUT signaling and FIF assertion are disabled; in other
fault-output modes, EOUT signaling is enabled and FIF assertion is disabled.
1b - Both EOUT signaling and FIF assertion are enabled in all fault-output modes.
49.7.1.28 Alarm-State Timer (TMR_ALARM)
Offset
Register Offset
TMR_ALARM 12Ch
Function
See TMR_ALARM[COUNT] .
This table shows how the Alarm-state timer's state and value vary by FCCU state:
Table 232. TMR_ALARM reset value
FCCU state Timer state Timer value
CONFIG Idle 0000_0000h
NORMAL Idle Initial value: NCF_TO[TO]
Table continues on the next page...

---

*Page 1453*

Fault Collection and Control Unit (FCCU)
Table 232. TMR_ALARM reset value (continued)
FCCU state Timer state Timer value
ALARM Running Value when read
FAULT Idle End of count
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R COUNT
W
Reset
0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1
1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R COUNT
W
Reset 1 0 1 0 1 0 0 1 1 0 0 0 0 0 0 0
1. The default reset value is provided by NCF_TO[TO] . See Table 232 for the reset value at different FCCU states.
Fields
Field Function
31-0 Alarm-State Timer Count
COUNT Specifies the value of the Alarm-state timer in CLKSAFE periods.
49.7.1.29 Configuration-State Timer (TMR_CFG)
Offset
Register Offset
TMR_CFG 134h
Function
See TMR_CFG[COUNT] .
This table shows how the Configuration-state timer's state and value vary by FCCU state:
FCCU state Timer state Timer value
CONFIG Running Value when read
Table continues on the next page...

---

*Page 1454*

Fault Collection and Control Unit (FCCU)
Table continued from the previous page...
FCCU state Timer state Timer value
NORMAL Idle 000F_FFFFh
ALARM Idle 000F_FFFFh
FAULT Idle 000F_FFFFh
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R COUNT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R COUNT
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31-0 Configuration-State Timer Count
COUNT Specifies the value of the Configuration-state timer in CLKSAFE periods.
49.7.1.30 Fault-Output Timer (TMR_ETMR)
Offset
Register Offset
TMR_ETMR 138h
Function
See TMR_ETMR[COUNT] .
This table shows how the fault-output timer's state and value vary by FCCU state:
FCCU state Timer state (value)
CONFIG Idle (0000_0000h)
Table continues on the next page...

---

*Page 1455*

Fault Collection and Control Unit (FCCU)
Table continued from the previous page...
FCCU state Timer state (value)
NORMAL Idle (0000_0000h)
ALARM Idle (0000_0000h)
FAULT Running (value when read. It is an up-counter which rollbacks
to zero after reaching maximum value and then it begins
counting again.)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R COUNT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R COUNT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Fault-Output Timer Count
COUNT Specifies the value of the fault-output timer in CLKSAFE periods.

#### 49.7.2 Configuration registers

49.7.2.1 Definition
Configuration registers are registers that:
• Let you configure FCCU's Alarm-state timer interval, fault channels, and fault-output (EOUT) signals.
• You can write to only when the configuration is not locked and FCCU is in CONFIG state (see Put FCCU in the CONFIG state ).
• Save the values you write to them while FCCU is in CONFIG state only after you manually put FCCU in NORMAL
state. If FCCU automatically leaves CONFIG state and enters NORMAL state because the configuration-timer interval
( CFG_TO[TO] ) expires (called a Configuration-state timeout), FCCU changes the value of the Configuration (CFG) register
to its Configuration-state-timeout value and the value of each of the other configuration registers to its reset value; FCCU also
changes the value of the Configuration-State Timeout Interval (CFG_TO) register to its reset value. For information on the
Configuration-state timeout value, see CFG register bit value at different events .
49.7.2.2 Configuration registers
Following is the list of configuration registers in this module. They are listed in an offset order from lowest to highest.

---

*Page 1456*

Fault Collection and Control Unit (FCCU)
• Configuration (CFG)
• Non-critical Fault Configuration (NCF_CFGa)
• Non-critical Fault-State Configuration (NCFS_CFG0)
• Non-critical Fault Enable (NCF_E0)
• Non-critical-Fault Alarm-State Timeout Enable (NCF_TOE0)
• Non-critical-Fault Alarm-State Timeout Interval (NCF_TO)
• Delta T (DELTA_T)
• Non-critical Alarm-State Interrupt-Request Enable (IRQ_ALARM_EN0)
• Non-critical Fault-State Non-maskable-Interrupt-Request Enable (NMI_EN0)
• Non-critical Fault-State EOUT Signaling Enable (EOUT_SIG_EN0)

#### 49.7.3 CFG register bit value at different events

In this chip, there are no events that affect the bits in the configuration register.

#### 49.8 Glossary

CF Critical fault
EOUT Error out
FOSU FCCU output supervision unit
FSM Finite state machine
intf Interface
IRQ Interrupt request
NCF Non-critical fault
NMI Non-maskable interrupt

---

*Page 1457*

