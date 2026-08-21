<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 44 -->

# Chapter 44

# Power Control Unit (MC_PCU)

#### 44.1 Introduction

The power control unit (MC_PCU) is used for initiating a Standby mode entry that reduces the overall chip power consumption.
Power can be saved by disconnecting parts of the chip from the power supply. The blocks inside the chip are grouped into multiple
parts having this capability, which are called "power domains".
When a power domain is disconnected from the supply, the power consumption is reduced to zero and the configuration of the
every core and module that belongs to such power domain is completely lost. When you reconnect a power domain to the supply
voltage, the domain draws an increased current until the power domain reaches its operational voltage. Maximum power saving
is achieved by entering the Standby mode.
After the MC_ME asserts a standby entry request, MC_PCU initiates the power sequence, which is non-retractable and includes
the handshake with the chip power management controller. The power-up/down sequences are handled by FSM s to ensure a
smooth and safe transition into and out of the Standby mode. Exiting the Standby mode can only be done through a system
wakeup event, power-on reset, destructive reset, or a functional reset.

#### 44.2 Power sequence FSM

MC_PCU implements an FSM to initiate the power sequencing of the Standby mode entry/exit sequence for the chip.

---

*Page 1276*

Power Control Unit (MC_PCU)
standby entry request
IDLE
MC_ME
INTF
software low-power UNLOCK
entry process
Input ISO. active Input ISO inactive
INTF SW L
LOCK CLOSE
Switchable domain supply ON
Output ISO. active Output ISO inactive
SW H
SW OPEN
CLOSE
PMC in low
Switchable domain supply OFF
power mode
PMC in full
FIRC PowerDown performance mode
PWR DOWN PWR UP
FIRC PowerUp
Reset event (Functional/destructive) activated
or
Wakeup source activated
Figure 168. MC_PCU FSM
NOTE
When destructive reset is asserted from MC_RGM, MC_PCU FSM moves to the IDLE state immediately. Indication
of this is not shown in the above figure.
Table 217. MC_PCU FSM transition description
State Name Exit condition Signal controlled Signal monitored
IDLE Idle state Standby request - -
received from MC_ME
MC_ME software low MC_ME software low Software entry - -
power entry process power entry process sequence completed
(SW4 process
completed)
INTF LOCK Interface lock state Input isolation active Input isolation Input isolation active
activation
Table continues on the next page...

---

*Page 1277*

Power Control Unit (MC_PCU)
Table 217. MC_PCU FSM transition description (continued)
State Name Exit condition Signal controlled Signal monitored
SW OPEN Switch open state Output isolation active, Output isolation Output isolation active,
Switchable domain activation, low power Switchable domain
supply turned off mode request to PMC supply
PWR DOWN Power down state Wakeup/functional FIRC power down Wakeup and functional
reset occurrence reset
PWR UP Power up state PMC in full - -
performance mode
SW H CLOSE Switch H close state Switchable domain - Switchable domain
supply turned on supply
SW L CLOSE Switch L close state Input isolation inactive Input isolation inactive Input isolation active
INTF UNLOCK Interface unlock - - -

#### 44.3 Glossary

FSM Finite state machine

---

*Page 1278*

