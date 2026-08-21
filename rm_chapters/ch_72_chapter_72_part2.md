<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 72 -->

n bit 1 (ABPSIS) of the
MTL_TxQ[n]_ETS_Status register is 1, it indicates that a new value is updated in the ABS field.
72.16.10.4 Disabling flow control for AV enabled queues
72.16.10.4.1 Transmit flow
Program the EHFC (Enable Hardware Flow Control) field of the corresponding receive queue's MTL_RxQ[n]_Operation_Mode
register to 0.
72.16.10.4.2 Receive flow control
Program PSTQ[n] field, corresponding to AV enabled transit queue in MAC_TxQ_Prty_Map0/1 register to 0.
72.16.10.5 Programming guidelines for flexible pulse-per-second output
After you enable the flexible pulse-per-second output feature in the module controller, you can perform these tasks:
• Generating single pulse on PPS
• Generating next pulse on PPS
• Generating a pulse train on PPS
• Generating an interrupt without affecting the PPS
72.16.10.5.1 Generating single pulse on PPS
Perform these steps to generate single pulse on PPS:
1. Program 11 or 10 (for interrupt) in TRGTMODSEL bits [6:5], of MAC_PPS_Control . This instructs MAC to use the target
time registers ( MAC_PPS0_Target_Time_Seconds and MAC_PPS0_Target_Time_Nanoseconds ) for a start time of the
PPS signal output.
2. Program the start time value in the target time registers ( MAC_PPS0_Target_Time_Seconds and
MAC_PPS0_Target_Time_Nanoseconds ).
3. Program the PPS signal output width in MAC_PPS(#i)_Width (for i = 0; i <= 3) register.

---

*Page 2644*

Ethernet Media Access Controller (EMAC)
4. Program PPSCMD bits [3:0], of MAC_PPS_Control to 0001. This instructs MAC to generate single pulse on the PPS
signal output at the time programmed in the target time registers.
72.16.10.5.2 Generating next pulse on PPS
When the PPSCMD is executed (PPSCMD bits = 0), you can give the cancel start command (PPSCMD=0011) before the
programmed start time elapses to cancel the pulse generation. You can also program the behavior of the next pulse in advance.
Follow these steps to program the next pulse:
1. Program the start time for the next pulse in the target time registers. This time must be more than the time at which the falling
edge occurs for the previous pulse.
2. Program the next PPS signal output width in MAC_PPS(#i)_Width (for i = 0; i <= DWC_EQOS_PPS_OUT_NUM-1) register.
3. Program PPSCMD bits [3:0], of MAC_PPS_Control to generate a single pulse after the time at which the previous pulse
is de-asserted. This instructs MAC to generate single pulse on the PPS signal output, at the time programmed in target
time registers.
If you give this command before the previous pulse becomes low, then the new command overwrites the previous command and
the QOS may generate only 1 extended pulse.
72.16.10.5.3 Generating a pulse train on PPS
Perform these steps to generate a pulse train on PPS:
1. Program 11 or 10 (for interrupt) in TRGTMODSEL bits [6:5] , of MAC_PPS_Control . This instructs MAC to use the target time
registers for the start time of the PPS signal output.
2. Program the start time value in the target time registers.
3. Program the interval value between the train of pulses on the PPS signal output in MAC_PPS(#i)_Interval (for i = 0; i <=
DWC_EQOS_PPS_OUT_NUM-1) register.
4. Program the PPS signal output width in MAC_PPS(#i)_Width (for i = 0; i <= DWC_EQOS_PPS_OUT_NUM-1) register.
5. Program PPSCMD bits [3:0], of MAC_PPS_Control to 0010. This instructs MAC to generate train of pulses on the PPS signal
output with start time programmed in target time registers.
By default, the PPS pulse train is free-running unless the STOP pulse train at time or STOP pulse train immediately commands
stop it.
6. Program the stop value in the target time registers. Ensure that bit 31 (TSTRBUSY) of
MAC_PPS(#i)_Target_Time_Nanoseconds (for i = 0; i <= DWC_EQOS_PPS_OUT_NUM-1) register becomes 0 before
programming the target time registers again.
7. Program PPSCMD field [bit 3:0] of MAC_PPS_Control to 0100. This stops the train of pulses on the PPS signal output after the
programmed stop time specified in step 6 elapses.
You can program 0101 in PPSCMD field [bit 3:0] of MAC_PPS_Control to stop the pulse train at any time. Similarly, you can
program 0110 in PPSCMD field [bit 3:0] of MAC_PPS_Control before the time (programmed in step 6) elapses to cancel the
stop pulse train command (given in Step 7). Also, program 0011 in PPSCMD field [bit 3:0] of MAC_PPS_Control before the
programmed start time (in Step 2) elapses to cancel the pulse train generation.
72.16.10.5.4 Generating an interrupt without affecting the PPS
MAC_PPS_Control TRGTMODSEL bits [6:5] enables you to program the target time registers to perform any one of these actions:
• Generate only interrupts.
• Generate interrupts and the PPS start and stop time.
• Generate only PPS start and stop time.
Perform these steps to program the target time registers to generate only interrupt event:

---

*Page 2645*

Ethernet Media Access Controller (EMAC)
1. Program 00 (for interrupt) in MAC_PPS_Control TRGTMODSEL bits [6:5]. This instructs the MAC to use the target time registers
for target time interrupt.
2. Program a target time value in the target time registers. This instructs the MAC to generate an interrupt when the target
time elapses.
If TRGTMODSEL bits [6:5], are changed (for example, to control the PPS) then over-write the interrupt generation with the new
mode and new programmed target time register value.
72.16.10.6 Programming guidelines for VLAN filtering on receive
Perform these steps to program VLAN filtering on receive:
1. Program MAC_VLAN_Tag register for the following fields to select the filtering method:
a. ETV: Enables 12-Bit VLAN tag comparison or 16-bit VLAN tag comparison.
b. VTHM: VLAN tag hash table match enable.
c. ERIVLT: Enables inner VLAN tag or outer VLAN Tag (to enable the inner or outer VLAN tag filtering, writing 1 to
MAC_VLAN_Tag_Ctrl[EDVLP] enables the double VLAN processing).
d. ERSVLM: Enables receive S-VLAN match or C-VLAN match (write 1 to MAC_VLAN_Tag_Ctrl[ESVL] to enable S-
VLAN processing).
e. DOVLTC: Ignores VLAN type for tag match
f. VTIM: Enables VLAN tag inverse match instead of the normal VLAN tag matching
2. Program VL of MAC_VLAN_Tag register for the 12-bit or 16-bit VLAN tag.
3. Program MAC_VLAN_Hash_Table , if hash filtering of VLAN tag is enabled. When MAC_VLAN_Tag_Ctrl[ETV] becomes
0, it indicates that the upper 4 bits of the calculated CRC-32 of VLAN tag are inverted and index the content of
MAC_VLAN_Hash_Table . When MAC_VLAN_Tag_Ctrl[ETV] is 1, it indicates that the upper 4 bits of calculated CRC-32 of
VLAN Tag index the content of MAC_VLAN_Hash_Table . For example, when MAC_VLAN_Tag_Ctrl[ETV] is 1, a hash value of
4b'1000 selects bit 8 of MAC_VLAN_Hash_Table . When MAC_VLAN_Tag_Ctrl[ETV] becomes 0 a hash value of 4'b1000 selects
bit 7 of MAC_VLAN_Hash_Table .
72.16.10.7 Programming guidelines for extended VLAN filtering and routing on receive
Perform these steps, for the indirect access of the per VLAN tag registers:
• Write
— Write the required data into MAC_VLAN_Tag_Data .
— Program MAC_VLAN_Tag_Ctrl[OFS] with the required filter register's offset and command type to
MAC_VLAN_Tag_Ctrl[CT] . For a write command, set this field to 0.
— Write 1 to MAC_VLAN_Tag_Ctrl[OB] and wait till it becomes 0 to do the next write. This will guarantee that you have
programmed the appropriate VLAN Tag Filter register.
• Read
— Program MAC_VLAN_Tag_Ctrl[OFS] with the required register's offset and command type to
MAC_VLAN_Tag_Ctrl[CT] . For a read command, set this field to 1.
— Write 1 to MAC_VLAN_Tag_Ctrl[OB] and wait till it becomes 0. The appropriate value of the VLAN Tag Filter register is
available in MAC_VLAN_Tag_Data .
72.16.10.8 Programming sequence for queue/channel based VLAN inclusion register
Perform these steps to program the queue/channel-based VLAN inclusion register:

---

*Page 2646*

Ethernet Media Access Controller (EMAC)
NOTE
When MAC_VLAN_Incl[CBTI] = 1 you cannot access the indirect VLAN include registers.
1. Write 1 to MAC_VLAN_Incl[CBTI] , to enable queue or channel based VLAN tag insertion on all the transmitted packets. This
field must be 1 before any indirect access to the queue or channel specific MAC_VLAN_Incl(#i) register.
2. Program the VLAN tag and VLAN type to insert in packets from a particular queue or channel in MAC_VLAN_Incl[VLT] and
MAC_VLAN_Incl[CSVL] , corresponding offset address in ADDR field (0 for queue/channel 0, 1 for queue/channel 1, and so on)
must be set. Write 0 to MAC_VLAN_Incl[RDWR] to indicate write access. The write to byte 0 (byte 3 in Big Endian mode) of
MAC_VLAN_Incl initiates access to indirect access MAC_VLAN_Incl(#i) register.
3. Write 1 to MAC_VLAN_Incl[BUSY] via the module to indicate the progress of access to indirect access MAC_VLAN_Incl(#i)
register. After the access completes, MAC_VLAN_Incl[BUSY] = 0. The application must not attempt subsequent access to
MAC_VLAN_Incl(#i) register when the MAC_VLAN_Incl[BUSY] is 1.
4. Repeat step 2 and step 3 to program VLAN tag and VLAN type to insert in packets from the remaining queues or channels. The
application must ensure that the required VLAN tag and VLAN type for all the queues or channels are programmed, otherwise an
unintended VLAN tag and VLAN type might be inserted.

#### 72.16.11 Programming guidelines for EST

Program the gate control values and time intervals in MTL_EST_Status[SWOL] along with the other EST related registers that
are described in EMAC register descriptions to appropriate values. The upcoming sub-sections provide step by step details for
programming the GCL and the other EST related registers.
72.16.11.1 Programming the GCL and GCL linked registers
Perform these steps to program the GCL and the four other registers implemented per GCL:
1. Access the GCL and the four other GCL-linked registers via indirect addressing using MTL_EST_GCL_Control and
MTL_EST_GCL_Data . MTL_EST_Status[SWOL] indicates whether the software owns GCL0 or GCL1.
2. Write the 32-bit write data to MTL_EST_GCL_Data , to program the GCL. Then program MTL_EST_GCL_Control to
write the write address and other control information.
3. In MTL_EST_GCL_Data , write data consists of up to 8 bits (configurable) of gate controls and up to 24 bits
(configurable) of time interval. Programming a 0 indicates gate close and programming a 1 indicates gate open. For a
4-TC and 20-bit time interval configuration, the data width is 24-bits and the remaining 8-bits are reserved/read-only.
You must write the data in the following format.
{8'h0, TC3, TC2, TC1, TC0, 20-bit Time Interval} where TCx = 0 or 1.
4. Program MTL_EST_GCL_Control[SRWO] to 1 (to start a Write Op) and program the address and R/W fields
appropriately.
5. Poll and check that the hardware clears MTL_EST_GCL_Control[SRWO] to indicate that the previous operation
completes before initiating a new read-write operation via the same indirect addressing mode.
6. Repeat steps 3, 4, 5 until the programming of the GCL completes.
7. Program the BTR, CTR, TER and LLR registers, using the same indirect addressing method as described above.
Write 1 to MTL_EST_GCL_Control[GCRR] appropriately. MTL_EST_GCL_Control[GCRR] interprets the address field as
belonging to these registers (instead of the GCL).
After the GCL and the related registers are programmed, program MTL_EST_GCL_Control to allow hardware to own and process
the GCL. When the list length (as indicated in LLR) is 1, the associated time interval must be smaller than the cycle time register
value. Otherwise, an error is reported (as described in the Error handling section) as a single set of gate controls add no value in
the TSN context.

---

*Page 2647*

Ethernet Media Access Controller (EMAC)
NOTE
The time unit in all the GCL related registers is seconds and nanoseconds. In cases where internally generated
PTP system time is used, you must program the nanoseconds field to use the Digital Rollover mode. (The value
of MAC_Timestamp_Control[TSCTRLSSR] must be 1)
72.16.11.2 Programming the EST registers
After the steps mentioned in "Programming the GCL and GCL Linked Registers" completes, program MTL_EST_Control
1. Write 1 to MTL_EST_Control[CTOV] and MTL_EST_Control[TILS] . Also, write 1 to MTL_EST_Control[EEST] and
MTL_EST_Control[SWOL] .
NOTE
The CTOV recommendations for GMII for SPRAM configurations are:
• 96 * Tx clock period, for 32 and 64 bit data width configurations.
• 128 * Tx clock period for 128 bit data width configurations.
The CTOV recommendations for GMII for non-SPRAM configurations are:
• 30 * Tx clock period, when SA/VLAN insertion is enabled.
• 22 * Tx clock period, when SA/VLAN insertion is not enabled.
2. Enables the hardware to own and process the new GCL and make a switch to the new GCL at the BTR value. The
hardware provides an interrupt (if enabled) when the switch to the new list happens.
3. Performs an appropriate action to address any other interrupts (explained in the Interrupts section) received during the
hardware execution of the GCL.
Write 1 to MTL_EST_Control[SSWL] to handoff to hardware. Software is not allowed to write to the GCL and GCL linked registers
when MTL_EST_Control[SSWL] = 1, because the hardware might be using the new GCL.
The hardware resets or clears MTL_EST_Control[SSWL] when it successfully switches to the new list. The hardware also flips
MTL_EST_Status[SWOL] to indicate the new GCL that the software owns.
Program the GCL that software owns ( MTL_EST_Status[SWOL] indicates) as described in "Programming the GCL and GCL
Linked Registers" and then program MTL_EST_Control as described above, to install a new GCL. Ensure that the new BTR is set
to an appropriate value to avoid BTR Error that may require software intervention in some cases.
The packet length (frame size) information must be available at all times, to avoid transmission overruns. Therefore, in the DMA
configurations, program the packet length in the first descriptor of every transmit frame. Similarly, in the MTL configuration, provide
the packet length in the control word.
NOTE
The packet length provided in the transmit descriptor must account for the SA and VLAN insertion, to avoid
transmission overruns, if applicable, that is, packet length must represent the actual packet length on the
Ethernet line.

#### 72.16.12 Programming the launch time in time-based scheduling

Configure the launch time in the enhanced normal transmit descriptors in DMA configurations and is driven as a control word in
MTL configurations as follows:
The OSTC and launch time features are mutually exclusive and must not be used together. In case of a conflict (if OSTC = LTV
= 1 in MTL configuration), give priority to OSTC and ignore the launch time.
In DMA configuration, if a context descriptor is received with a valid OSTC values immediately before receiving a first normal
descriptor with LTV = 1, then the LTV is ignored.

---

*Page 2648*

Ethernet Media Access Controller (EMAC)

#### 72.16.13 Programming guidelines for media clock generation and recovery

72.16.13.1 Programming guidelines for media clock generation
These are the guidelines for the media clock generation:
1. Program the appropriate presentation time control (supported generation modes "1001-1011" ) to PPSCTRL_PPSCMD ( for
0th instance)/PPSCMD#i (for 1,2,3 instances) of MAC_PPS_Control , to define the PPS instance in Media clock generation mode.
2. Based on the selected PPS instance, application must drive the appropriate trigger signal to the
corresponding MCG_PST_TRIG_I[#i].
3. Based on the programmed mode, DUT captures the timestamp and programs it in the MAC_PPS(#i)_Target_Time_Seconds
register. Then, mcgr_dma_req_o[#i] is set.
4. For every request which DUT generates, the module reads the corresponding MAC_PPS(#i)_Target_Time_Seconds register
and asserts the corresponding mcgr_d-ma_ack_i[#i] to acknowledge the read request.
72.16.13.2 Programming guidelines for media clock recovery
These are the guidelines for the media clock recovery:
• Writing 1 to MAC_Timestamp_Control[PTGE] enables the CPT counter. In addition to configuring the initialization values
for the system time, update MAC_Presn_Time_Updt with the equivalent presentation time initialization value, then
MAC_Timestamp_Control[TSINIT] = 1.
• Use the increment values for the system time and also for the CPT because the increment value is in sub-seconds
and sub-nanoseconds.
• Update MAC_Presn_Time_Updt with the equivalent presentation time update value (32 bit ns), when an update is required,
and also configure the updated values for the system time. Finally, MAC_Timestamp_Control[TSUPDT] must be 1 for
the update.
• Program the MCGREN#i field of MAC_PPS_Control to enable the PPS instance to operate in MCGR mode.
• Program the appropriate presentation time control (supported recovery modes "0001 - 0011") to PPSCTRL_PPSCMD (for
0th instance) or PPSCMD#i (for 1,2,3 instances) of MAC_PPS_Control register, to set the PPS instance in Media clock
recovery mode.
• After the PPS instance is set in Recovery mode, DUT sets the corresponding mcgr_dma_req_o[#i]. For every request which
DUT generates, program the target presentation time into the MAC_PPS(#i)_Target_Time_Seconds register and assert the
corresponding mcgr_dma_ack_i[#i] to acknowledge the request. Acknowledgment can be given before/after programming the
Target presentation time.
• Observe the recovered media clock on the corresponding PTP_PPS_O[#i].

#### 72.16.14 Programming guidelines for ECC protection for memories

These are the guidelines for the ECC protection for memories:
• Write 1 to the appropriate field of MTL_ECC_Control to enable the ECC features for the respective memory.
• Generate a correctable interrupt (sbd_sfty_ce_intr_o) or an uncorrectable interrupt (sbd_sfty_ue_intr_o) to indicate
the application, if any correctable, uncorrectable, or address errors are detected. DMA_Interrupt_Status or
MTL_ECC_Interrupt_Status indicates an appropriate status.
• Provide debug mode for each memory to specify errors.
NOTE
Enable the ECC feature before the traffic is online (or after the reset), otherwise the false interrupts may trigger.
72.16.14.1 Programming guidelines for ECC hardware error injection (Debug mode)
Follow these steps for ECC hardware error injection:

---

*Page 2649*

Ethernet Media Access Controller (EMAC)
• Write 1 to the appropriate field in MTL_DBG_CTL to enable the ECC error injection feature for MTL TX/RX and DMA TSO.
• Write 1 to the appropriate field in MTL_EST_GCL_Control to enable the ECC error injection feature for the EST memory.
• Write 1 to the appropriate field in MTL_RXP_Indirect_Acc_Control_Status to enable the ECC error injection
for the receive parser memory. To access the receive parser memory in Debug mode, write 0 to
MTL_RXP_Indirect_Acc_Control_Status[FRPE] to disable the receive parser feature.
• Ensure that all the CSR writes corresponding to one memory write must have the same value for the error injection control
word, where multiple CSR writes are required for writing single data word into the memory.

#### 72.16.15 Programming guidelines for on-chip datapath parity protection

Follow these steps for on-chip datapath parity protection:
• Writing 1 to MTL_DPP_Control[EDPP] enables the parity generation and parity detection mismatch on datapath.
• Generates an uncorrectable interrupt (sbd_sfty_ue_intr_o) to indicate the application if any parity mismatch is
detected. DMA_Safety_Interrupt_Status or MTL_Safety_Interrupt_Status and MAC_DPP_FSM_Interrupt_Status indicates
the appropriate status.
• Supports Debug mode for each parity generator to insert an error.
NOTE
Enable the datapath parity protection before the receive or transit traffic starts, to avoid the false safety interrupts.

#### 72.16.16 Programming guidelines for FSM parity and timeout

Follow these steps to configure the FSM parity and timeout:
• Write 1 to MAC_FSM_Control[PRTYEN] and MAC_FSM_Control[TMOUTEN] to enable the FSM parity and timeout
feature respectively.
• Force the FSMs to enter into a state with even number of 1s and wait for the safety uncorrectable interrupt to define with
MAC_DPP_FSM_Interrupt_Status[FSMPES] for a parity error detection.
• Write 1 to the parity error injection using the [23:16] bits of MAC_FSM_Control to select the appropriate
clock domain, for Error injection mode in FSM parity. Wait for the safety uncorrectable interrupt to be defined
with MAC_DPP_FSM_Interrupt_Status[FSMPES] .
• Configure the large and normal mode values which MAC_FSM_ACT_Timer[LTMRMD] and
MAC_FSM_ACT_Timer[NTMRMD] indicates , for FSM timeouts.
• Use the [31:24] bits of MAC_FSM_Control to select the large or normal mode tic generation per clock domain.
• Write 1 to MAC_FSM_ACT_Timer[TMR] with the appropriate number of CSR clock cycles to generate 1us tic.
• Force the FSM in a particular clock domain to remain in an active state for timeout duration (for example, T-2T). You
can check the safety interrupt and the status field for the corresponding clock domain that sets in the [15:8] bits of the
MAC_DPP_FSM_Interrupt_Status .
• Select the appropriate clock domain for which timeout error injection must be set using the [15:8] bits of MAC_FSM_Control ,
for Error injection mode in FSM timeout. Wait for the safety uncorrectable interrupt to define with the corresponding error
status in the MAC_DPP_FSM_Interrupt_Status .
• Use only normal mode tic generation, for application or CSR interface timeout. On the basis of the time-outs of configured
interface, appropriate fields (MSTTES, SLVTES) of MAC_DPP_FSM_Interrupt_Status is defined with the safety interrupt.

#### 72.17 EMAC register descriptions

This section and all its sub-sections are Synopsys Proprietary. Used with permission.

---

*Page 2650*

Ethernet Media Access Controller (EMAC)
NOTE
This chip has 16 KB allocated per Ethernet controller for configuration space. EMAC uses only 8 KB of this memory
and the remaining 8 KB is reserved for future use. Writing to the reserved memory may lead to unintended behavior.

#### 72.17.1 EMAC memory map

EMAC base address: 4048_0000h
Offset Register Access Reset value
Width
(In bits)
0h MAC Configuration (MAC_Configuration) 32 RW 0000_8000h
4h MAC Extended Configuration (MAC_Ext_Configuration) 32 RW 0000_0000h
8h MAC Packet Filter (MAC_Packet_Filter) 32 RW 0000_0000h
Ch MAC Watchdog Timeout (MAC_Watchdog_Timeout) 32 RW 0000_0000h
10h MAC Hash Table First 32 Bits (MAC_Hash_Table_Reg0) 32 RW 0000_0000h
14h MAC Hash Table Second 32 Bits (MAC_Hash_Table_Reg1) 32 RW 0000_0000h
50h MAC VLAN Tag (MAC_VLAN_Tag) 32 RW 0000_0000h
50h MAC VLAN Tag Control (MAC_VLAN_Tag_Ctrl) 32 RW 0000_0000h
54h MAC VLAN Tag Data (MAC_VLAN_Tag_Data) 32 RW 0000_0000h
54h MAC VLAN Tag Filter 0 (MAC_VLAN_Tag_Filter0) 32 RW 0000_0000h
54h MAC VLAN Tag Filter 1 (MAC_VLAN_Tag_Filter1) 32 RW 0000_0000h
54h MAC VLAN Tag Filter 2 (MAC_VLAN_Tag_Filter2) 32 RW 0000_0000h
54h MAC VLAN Tag Filter 3 (MAC_VLAN_Tag_Filter3) 32 RW 0000_0000h
58h MAC VLAN Hash Table (MAC_VLAN_Hash_Table) 32 RW 0000_0000h
60h MAC VLAN Inclusion Or Replacement (MAC_VLAN_Incl) 32 RW 0000_0000h
60h MAC VLAN Inclusion 0 (MAC_VLAN_Incl0) 32 RW 0000_0000h
60h MAC VLAN Inclusion 1 (MAC_VLAN_Incl1) 32 RW 0000_0000h
60h MAC VLAN Inclusion 2 (MAC_VLAN_Incl2) 32 RW 0000_0000h
60h MAC VLAN Inclusion 3 (MAC_VLAN_Incl3) 32 RW 0000_0000h
60h MAC VLAN Inclusion 4 (MAC_VLAN_Incl4) 32 RW 0000_0000h
60h MAC VLAN Inclusion 5 (MAC_VLAN_Incl5) 32 RW 0000_0000h
60h MAC VLAN Inclusion 6 (MAC_VLAN_Incl6) 32 RW 0000_0000h
60h MAC VLAN Inclusion 7 (MAC_VLAN_Incl7) 32 RW 0000_0000h
64h Inner VLAN Tag Inclusion Or Replacement (MAC_Inner_VLAN_Incl) 32 RW 0000_0000h
70h MAC Q0 Tx Flow Control (MAC_Q0_Tx_Flow_Ctrl) 32 RW 0000_0000h
90h MAC Receive Flow Control (MAC_Rx_Flow_Ctrl) 32 RW 0000_0000h
94h MAC RxQ Control 4 (MAC_RxQ_Ctrl4) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 2651*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
A0h MAC RxQ Control 0 (MAC_RxQ_Ctrl0) 32 RW 0000_0000h
A4h Receive Queue Control 1 (MAC_RxQ_Ctrl1) 32 RW 0000_0000h
A8h MAC RxQ Control 2 (MAC_RxQ_Ctrl2) 32 RW 0000_0000h
B0h MAC Interrupt Status (MAC_Interrupt_Status) 32 R 0000_0000h
B4h MAC Interrupt Enable (MAC_Interrupt_Enable) 32 RW 0000_0000h
B8h MAC Rx Transmit Status (MAC_Rx_Tx_Status) 32 R 0000_0000h
110h MAC Version (MAC_Version) 32 R 0000_1051h
114h MAC Debug (MAC_Debug) 32 R 0000_0000h
11Ch MAC Hardware Feature 0 (MAC_HW_Feature0) 32 R 0E09_5135h
120h MAC Hardware Feature 1 (MAC_HW_Feature1) 32 R 2118_29A6h
124h MAC Hardware Feature 2 (MAC_HW_Feature2) 32 R 0404_1041h
128h MAC Hardware Feature 3 (MAC_HW_Feature3) 32 R 2C37_0E31h
140h MAC DPP FSM Interrupt Status (MAC_DPP_FSM_Interrupt_Status) 32 R 0000_0000h
148h MAC FSM Control (MAC_FSM_Control) 32 RW 0000_0000h
14Ch MAC FSM ACT Timer (MAC_FSM_ACT_Timer) 32 RW 0000_0000h
150h SCS_REG 1 (SCS_REG1) 32 RW 0000_0000h
200h MAC MDIO Address (MAC_MDIO_Address) 32 RW 0000_0000h
204h MAC MDIO Data (MAC_MDIO_Data) 32 RW 0000_0000h
230h MAC CSR Software Control (MAC_CSR_SW_Ctrl) 32 RW 0000_0000h
234h MAC FPE Control STS (MAC_FPE_CTRL_STS) 32 RW 0000_0000h
240h MAC Presentation Time (MAC_Presn_Time_ns) 32 R 0000_0000h
244h MAC Presentation Time Update (MAC_Presn_Time_Updt) 32 RW 0000_0000h
300h MAC Address 0 High (MAC_Address0_High) 32 RW 8000_FFFFh
304h MAC Address 0 Low (MAC_Address0_Low) 32 RW FFFF_FFFFh
308h MAC Address 1 High (MAC_Address1_High) 32 RW 0000_FFFFh
30Ch MAC Address 1 Low (MAC_Address1_Low) 32 RW FFFF_FFFFh
310h MAC Address 2 High (MAC_Address2_High) 32 RW 0000_FFFFh
314h MAC Address 2 Low (MAC_Address2_Low) 32 RW FFFF_FFFFh
700h MMC Control (MMC_Control) 32 RW 0000_0000h
704h MMC Receive Interrupt (MMC_Rx_Interrupt) 32 R 0000_0000h
708h MMC Transmit Interrupt (MMC_Tx_Interrupt) 32 R 0000_0000h
Table continues on the next page...

---

*Page 2652*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
70Ch MMC Receive Interrupt Mask (MMC_Rx_Interrupt_Mask) 32 RW 0000_0000h
710h MMC Transmit Interrupt Mask (MMC_Tx_Interrupt_Mask) 32 RW 0000_0000h
714h Transmit Octet Count Good Bad (Tx_Octet_Count_Good_Bad) 32 R 0000_0000h
718h Transmit Packet Count Good Bad (Tx_Packet_Count_Good_Bad) 32 R 0000_0000h
71Ch Transmit Broadcast Packets Good (Tx_Broadcast_Packets_Good) 32 R 0000_0000h
720h Transmit Multicast Packets Good (Tx_Multicast_Packets_Good) 32 R 0000_0000h
724h Transmit 64-Octet Packets Good Bad 32 R 0000_0000h
(Tx_64Octets_Packets_Good_Bad)
728h Transmit 65 To 127 Octet Packets Good Bad 32 R 0000_0000h
(Tx_65To127Octets_Packets_Good_Bad)
72Ch Transmit 128 To 255 Octet Packets Good Bad 32 R 0000_0000h
(Tx_128To255Octets_Packets_Good_Bad)
730h Transmit 256 To 511 Octet Packets Good Bad 32 R 0000_0000h
(Tx_256To511Octets_Packets_Good_Bad)
734h Transmit 512 To 1023 Octet Packets Good Bad 32 R 0000_0000h
(Tx_512To1023Octets_Packets_Good_Bad)
738h Transmit 1024 To Max Octet Packets Good Bad 32 R 0000_0000h
(Tx_1024ToMaxOctets_Packets_Good_Bad)
73Ch Transmit Unicast Packets Good Bad 32 R 0000_0000h
(Tx_Unicast_Packets_Good_Bad)
740h Transmit Multicast Packets Good Bad 32 R 0000_0000h
(Tx_Multicast_Packets_Good_Bad)
744h Transmit Broadcast Packets Good Bad 32 R 0000_0000h
(Tx_Broadcast_Packets_Good_Bad)
748h Transmit Underflow Error Packets (Tx_Underflow_Error_Packets) 32 R 0000_0000h
74Ch Transmit Single Collision Good Packets 32 R 0000_0000h
(Tx_Single_Collision_Good_Packets)
750h Transmit Multiple Collision Good Packets 32 R 0000_0000h
(Tx_Multiple_Collision_Good_Packets)
754h Transmit Deferred Packets (Tx_Deferred_Packets) 32 R 0000_0000h
758h Transmit Late Collision Packets (Tx_Late_Collision_Packets) 32 R 0000_0000h
75Ch Transmit Excessive Collision Packets 32 R 0000_0000h
(Tx_Excessive_Collision_Packets)
760h Transmit Carrier Error Packets (Tx_Carrier_Error_Packets) 32 R 0000_0000h
764h Transmit Octet Count Good (Tx_Octet_Count_Good) 32 R 0000_0000h
Table continues on the next page...

---

*Page 2653*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
768h Transmit Packet Count Good (Tx_Packet_Count_Good) 32 R 0000_0000h
76Ch Transmit Excessive Deferral Error (Tx_Excessive_Deferral_Error) 32 R 0000_0000h
770h Transmit Pause Packets (Tx_Pause_Packets) 32 R 0000_0000h
774h Transmit VLAN Packets Good (Tx_VLAN_Packets_Good) 32 R 0000_0000h
778h Transmit O Size Packets Good (Tx_OSize_Packets_Good) 32 R 0000_0000h
780h Receive Packets Count Good Bad (Rx_Packets_Count_Good_Bad) 32 R 0000_0000h
784h Receive Octet Count Good Bad (Rx_Octet_Count_Good_Bad) 32 R 0000_0000h
788h Receive Octet Count Good (Rx_Octet_Count_Good) 32 R 0000_0000h
78Ch Receive Broadcast Packets Good (Rx_Broadcast_Packets_Good) 32 R 0000_0000h
790h Receive Multicast Packets Good (Rx_Multicast_Packets_Good) 32 R 0000_0000h
794h Receive CRC Error Packets (Rx_CRC_Error_Packets) 32 R 0000_0000h
798h Receive Alignment Error Packets (Rx_Alignment_Error_Packets) 32 R 0000_0000h
79Ch Receive Runt Error Packets (Rx_Runt_Error_Packets) 32 R 0000_0000h
7A0h Receive Jabber Error Packets (Rx_Jabber_Error_Packets) 32 R 0000_0000h
7A4h Receive Undersize Packets Good (Rx_Undersize_Packets_Good) 32 R 0000_0000h
7A8h Receive Oversize Packets Good (Rx_Oversize_Packets_Good) 32 R 0000_0000h
7ACh Receive 64 Octets Packets Good Bad 32 R 0000_0000h
(Rx_64Octets_Packets_Good_Bad)
7B0h Receive 65-127 Octets Packets Good Bad 32 R 0000_0000h
(Rx_65To127Octets_Packets_Good_Bad)
7B4h Receive 128-255 Octets Packets Good Bad 32 R 0000_0000h
(Rx_128To255Octets_Packets_Good_Bad)
7B8h Receive 256-511 Octets Packets Good Bad 32 R 0000_0000h
(Rx_256To511Octets_Packets_Good_Bad)
7BCh Receive 512-1023 Octets Packets Good Bad 32 R 0000_0000h
(Rx_512To1023Octets_Packets_Good_Bad)
7C0h Receive 1024 To Max Octets Good Bad 32 R 0000_0000h
(Rx_1024ToMaxOctets_Packets_Good_Bad)
7C4h Receive Unicast Packets Good (Rx_Unicast_Packets_Good) 32 R 0000_0000h
7C8h Receive Length Error Packets (Rx_Length_Error_Packets) 32 R 0000_0000h
7CCh Receive Out of Range Type Packet 32 R 0000_0000h
(Rx_Out_Of_Range_Type_Packets)
7D0h Receive Pause Packets (Rx_Pause_Packets) 32 R 0000_0000h
Table continues on the next page...

---

*Page 2654*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
7D4h Receive FIFO Overflow Packets (Rx_FIFO_Overflow_Packets) 32 R 0000_0000h
7D8h Receive VLAN Packets Good Bad (Rx_VLAN_Packets_Good_Bad) 32 R 0000_0000h
7DCh Receive Watchdog Error Packets (Rx_Watchdog_Error_Packets) 32 R 0000_0000h
7E0h Receive Error Packets (Rx_Receive_Error_Packets) 32 R 0000_0000h
7E4h Receive Control Packets Good (Rx_Control_Packets_Good) 32 R 0000_0000h
8A0h MMC Transmit FPE Fragment Counter Interrupt Status 32 R 0000_0000h
(MMC_FPE_Tx_Interrupt)
8A4h MMC FPE Transmit Interrupt Mask (MMC_FPE_Tx_Interrupt_Mask) 32 RW 0000_0000h
8A8h Transmit FPE Fragment Counter (MMC_Tx_FPE_Fragment_Cntr) 32 R 0000_0000h
8ACh Transmit Hold Request Counter (MMC_Tx_Hold_Req_Cntr) 32 R 0000_0000h
8C0h MMC Receive Packet Assembly Error Counter Interrupt Status 32 R 0000_0000h
(MMC_FPE_Rx_Interrupt)
8C4h MMC FPE Receive Interrupt Mask (MMC_FPE_Rx_Interrupt_Mask) 32 RW 0000_0000h
8C8h MMC Receive Packet Assembly Error Counter 32 R 0000_0000h
(MMC_Rx_Packet_Assembly_Err_Cntr)
8CCh MMC Receive Packet SMD Error Counter 32 R 0000_0000h
(MMC_Rx_Packet_SMD_Err_Cntr)
8D0h MMC Receive Packet Assembly OK Counter 32 R 0000_0000h
(MMC_Rx_Packet_Assembly_OK_Cntr)
8D4h MMC Receive FPE Fragment Counter 32 R 0000_0000h
(MMC_Rx_FPE_Fragment_Cntr)
900h MAC Layer 3 Layer 4 Control 0 (MAC_L3_L4_Control0) 32 RW 0000_0000h
904h MAC Layer 4 Address 0 (MAC_Layer4_Address0) 32 RW 0000_0000h
910h MAC Layer 3 Address 0 Reg 0 (MAC_Layer3_Addr0_Reg0) 32 RW 0000_0000h
914h MAC Layer 3 Address 1 Reg 0 (MAC_Layer3_Addr1_Reg0) 32 RW 0000_0000h
918h MAC Layer 3 Address 2 Reg 0 (MAC_Layer3_Addr2_Reg0) 32 RW 0000_0000h
91Ch MAC Layer 3 Address 3 Reg 0 (MAC_Layer3_Addr3_Reg0) 32 RW 0000_0000h
930h MAC L3 L4 Control 1 (MAC_L3_L4_Control1) 32 RW 0000_0000h
934h MAC Layer 4 Address 1 (MAC_Layer4_Address1) 32 RW 0000_0000h
940h MAC Layer 3 Address 0 Reg 1 (MAC_Layer3_Addr0_Reg1) 32 RW 0000_0000h
944h MAC Layer 3 Address 1 Reg 1 (MAC_Layer3_Addr1_Reg1) 32 RW 0000_0000h
948h MAC Layer 3 Address 2 Reg 1 (MAC_Layer3_Addr2_Reg1) 32 RW 0000_0000h
94Ch MAC Layer 3 Address 3 Reg 1 (MAC_Layer3_Addr3_Reg1) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 2655*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
960h MAC L3 L4 Control 2 (MAC_L3_L4_Control2) 32 RW 0000_0000h
964h MAC Layer 4 Address 2 (MAC_Layer4_Address2) 32 RW 0000_0000h
970h MAC Layer 3 Address 0 Reg 2 (MAC_Layer3_Addr0_Reg2) 32 RW 0000_0000h
974h MAC Layer 3 Address 1 Reg 2 (MAC_Layer3_Addr1_Reg2) 32 RW 0000_0000h
978h MAC Layer 3 Address 2 Reg 2 (MAC_Layer3_Addr2_Reg2) 32 RW 0000_0000h
97Ch MAC Layer 3 Address 3 Reg 2 (MAC_Layer3_Addr3_Reg2) 32 RW 0000_0000h
990h MAC L3 L4 Control 3 (MAC_L3_L4_Control3) 32 RW 0000_0000h
994h MAC Layer 4 Address 3 (MAC_Layer4_Address3) 32 RW 0000_0000h
9A0h MAC Layer 3 Address 0 Reg 3 (MAC_Layer3_Addr0_Reg3) 32 RW 0000_0000h
9A4h MAC Layer 3 Address 1 Reg 3 (MAC_Layer3_Addr1_Reg3) 32 RW 0000_0000h
9A8h MAC Layer 3 Address 2 Reg 3 (MAC_Layer3_Addr2_Reg3) 32 RW 0000_0000h
9ACh MAC Layer 3 Address 3 Reg 3 (MAC_Layer3_Addr3_Reg3) 32 RW 0000_0000h
B00h MAC Timestamp Control (MAC_Timestamp_Control) 32 RW 0000_2000h
B04h MAC Sub Second Increment (MAC_Sub_Second_Increment) 32 RW 0000_0000h
B08h MAC System Time In Seconds (MAC_System_Time_Seconds) 32 R 0000_0000h
B0Ch MAC System Time In Nanoseconds 32 R 0000_0000h
(MAC_System_Time_Nanoseconds)
B10h MAC System Time Seconds Update 32 RW 0000_0000h
(MAC_System_Time_Seconds_Update)
B14h MAC System Time Nanoseconds Update 32 RW 0000_0000h
(MAC_System_Time_Nanoseconds_Update)
B18h MAC Timestamp Addend (MAC_Timestamp_Addend) 32 RW 0000_0000h
B1Ch MAC System Time Higher Word In Seconds 32 RW 0000_0000h
(MAC_System_Time_Higher_Word_Seconds)
B20h MAC Timestamp Status (MAC_Timestamp_Status) 32 R 0000_0000h
B30h MAC Transmit Timestamp Status In Nanoseconds 32 R 0000_0000h
(MAC_Tx_Timestamp_Status_Nanoseconds)
B34h MAC Transmit Timestamp Status In Seconds 32 R 0000_0000h
(MAC_Tx_Timestamp_Status_Seconds)
B50h MAC Timestamp Ingress Asymmetry Correction 32 RW 0000_0000h
(MAC_Timestamp_Ingress_Asym_Corr)
B54h MAC Timestamp Egress Asymmetry Correction 32 RW 0000_0000h
(MAC_Timestamp_Egress_Asym_Corr)
Table continues on the next page...

---

*Page 2656*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
B58h MAC Timestamp Ingress Correction In Nanoseconds 32 RW 0000_0000h
(MAC_Timestamp_Ingress_Corr_Nanosecond)
B5Ch MAC Timestamp Egress Correction In Nanoseconds 32 RW 0000_0000h
(MAC_Timestamp_Egress_Corr_Nanosecond)
B60h MAC Timestamp Ingress Correction In Subnanoseconds 32 RW 0000_0000h
(MAC_Timestamp_Ingress_Corr_Subnanosec)
B64h MAC Timestamp Engress Correction In Subnanoseconds 32 RW 0000_0000h
(MAC_Timestamp_Egress_Corr_Subnanosec)
B68h MAC Timestamp Ingress Latency 32 R 0000_0000h
(MAC_Timestamp_Ingress_Latency)
B6Ch MAC Timestamp Egress Latency 32 R 0000_0000h
(MAC_Timestamp_Egress_Latency)
B70h MAC PPS Control (MAC_PPS_Control) 32 RW 0000_0000h
B80h MAC PPS0 Target Time In Seconds 32 RW 0000_0000h
(MAC_PPS0_Target_Time_Seconds)
B84h MAC PPS0 Target Time In Nanoseconds 32 RW 0000_0000h
(MAC_PPS0_Target_Time_Nanoseconds)
B88h MAC PPS0 Interval (MAC_PPS0_Interval) 32 RW 0000_0000h
B8Ch MAC PPS0 Width (MAC_PPS0_Width) 32 RW 0000_0000h
B90h MAC PPS1 Target Time In Seconds 32 RW 0000_0000h
(MAC_PPS1_Target_Time_Seconds)
B94h MAC PPS1 Target Time In Nanoseconds 32 RW 0000_0000h
(MAC_PPS1_Target_Time_Nanoseconds)
B98h MAC PPS1 Interval (MAC_PPS1_Interval) 32 RW 0000_0000h
B9Ch MAC PPS1 Width (MAC_PPS1_Width) 32 RW 0000_0000h
BA0h MAC PPS2 Target Time In Seconds 32 RW 0000_0000h
(MAC_PPS2_Target_Time_Seconds)
BA4h MAC PPS2 Target Time In Nanoseconds 32 RW 0000_0000h
(MAC_PPS2_Target_Time_Nanoseconds)
BA8h MAC PPS2 Interval (MAC_PPS2_Interval) 32 RW 0000_0000h
BACh MAC PPS2 Width (MAC_PPS2_Width) 32 RW 0000_0000h
BB0h MAC PPS3 Target Time In Seconds 32 RW 0000_0000h
(MAC_PPS3_Target_Time_Seconds)
BB4h MAC PPS3 Target Time In Nanoseconds 32 RW 0000_0000h
(MAC_PPS3_Target_Time_Nanoseconds)
BB8h MAC PPS3 Interval (MAC_PPS3_Interval) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 2657*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
BBCh MAC PPS3 Width (MAC_PPS3_Width) 32 RW 0000_0000h
C00h MTL Operation Mode (MTL_Operation_Mode) 32 RW 0000_0000h
C08h MTL Debug Control (MTL_DBG_CTL) 32 RW 0000_0000h
C0Ch MTL Debug Status (MTL_DBG_STS) 32 RW 0000_0018h
C10h MTL FIFO Debug Data (MTL_FIFO_Debug_Data) 32 RW 0000_0000h
C20h MTL Interrupt Status (MTL_Interrupt_Status) 32 R 0000_0000h
C30h MTL Receive Queue DMA Map 0 (MTL_RxQ_DMA_Map0) 32 RW 0000_0000h
C40h MTL TBS Control (MTL_TBS_CTRL) 32 RW 0000_0000h
C50h MTL EST Control (MTL_EST_Control) 32 RW 0000_0000h
C58h MTL EST Status (MTL_EST_Status) 32 RW 0000_0000h
C60h MTL EST Scheduling Error (MTL_EST_Sch_Error) 32 RW 0000_0000h
C64h MTL EST Frame Size Error (MTL_EST_Frm_Size_Error) 32 RW 0000_0000h
C68h MTL EST Frame Size Capture (MTL_EST_Frm_Size_Capture) 32 R 0000_0000h
C70h MTL EST Interrupt Enable (MTL_EST_Intr_Enable) 32 RW 0000_0000h
C80h MTL EST GCL Control (MTL_EST_GCL_Control) 32 RW 0000_0000h
C84h MTL EST GCL Data (MTL_EST_GCL_Data) 32 RW 0000_0000h
C90h MTL FPE Control Status (MTL_FPE_CTRL_STS) 32 RW 0000_0000h
C94h MTL FPE Advance (MTL_FPE_Advance) 32 RW 0000_0000h
CA0h MTL Rx Parser Control Status (MTL_RXP_Control_Status) 32 RW 803F_003Fh
CA4h MTL Rx Parser Interrupt Control Status 32 RW 0000_0000h
(MTL_RXP_Interrupt_Control_Status)
CA8h MTL Rx Parser Drop Count (MTL_RXP_Drop_Cnt) 32 R 0000_0000h
CACh MTL Rx Parser Error Count (MTL_RXP_Error_Cnt) 32 R 0000_0000h
CB0h MTL Rx Parser Indirect Access Control Status 32 RW 0000_0000h
(MTL_RXP_Indirect_Acc_Control_Status)
CB4h MTL Rx Parser Indirect Access Data (MTL_RXP_Indirect_Acc_Data) 32 R 0000_0000h
CC0h MTL ECC Control (MTL_ECC_Control) 32 RW 0000_000Fh
CC4h MTL Safety Interrupt Status (MTL_Safety_Interrupt_Status) 32 R 0000_0000h
CC8h MTL ECC Interrupt Enable (MTL_ECC_Interrupt_Enable) 32 RW 0000_1111h
CCCh MTL ECC Interrupt Status (MTL_ECC_Interrupt_Status) 32 RW 0000_0000h
CD0h MTL ECC Error Status (MTL_ECC_Err_Sts_Rctl) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 2658*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
CD4h MTL ECC Error Address Status (MTL_ECC_Err_Addr_Status) 32 R 0000_0000h
CD8h MTL ECC Error Control Status (MTL_ECC_Err_Cntr_Status) 32 R 0000_0000h
CE0h MTL DPP Control (MTL_DPP_Control) 32 RW 0000_0003h
D00h MTL Tx Queue 0 Operation Mode (MTL_TxQ0_Operation_Mode) 32 RW 0000_0000h
D04h MTL Tx Queue 0 Underflow (MTL_TxQ0_Underflow) 32 R 0000_0000h
D08h MTL Tx Queue 0 Debug (MTL_TxQ0_Debug) 32 R 0000_0000h
D14h MTL Tx Queue 0 ETS Status (MTL_TxQ0_ETS_Status) 32 R 0000_0000h
D18h MTL Tx Queue Quantum Weight (MTL_TxQ0_Quantum_Weight) 32 RW 0000_0000h
D2Ch MTL Queue 0 Interrupt Control Status 32 RW 0000_0000h
(MTL_Q0_Interrupt_Control_Status)
D30h MTL Rx Queue 0 Operation Mode (MTL_RxQ0_Operation_Mode) 32 RW 0000_0000h
D34h MTL Rx Queue Missed Packet Overflow Count 32 R 0000_0000h
(MTL_RxQ0_Missed_Packet_Overflow_Cnt)
D38h MTL Rx Queue 0 Debug (MTL_RxQ0_Debug) 32 R 0000_0000h
D3Ch MTL Rx Queue 0 Control 0 (MTL_RxQ0_Control) 32 RW 0000_0000h
D40h MTL Tx Queue 1 Operation Mode (MTL_TxQ1_Operation_Mode) 32 RW 0000_0000h
D44h MTL Tx Queue 1 Underflow (MTL_TxQ1_Underflow) 32 R 0000_0000h
D48h MTL Tx Queue 1 Debug (MTL_TxQ1_Debug) 32 R 0000_0000h
D50h MTL Tx Queue 1 ETS Control (MTL_TxQ1_ETS_Control) 32 RW 0000_0000h
D54h MTL Tx Queue 1 ETS Status (MTL_TxQ1_ETS_Status) 32 R 0000_0000h
D58h MTL Tx Queue 1 Quantum Weight (MTL_TxQ1_Quantum_Weight) 32 RW 0000_0000h
D5Ch MTL Tx Queue 1 Sendslope Credit (MTL_TxQ1_SendSlopeCredit) 32 RW 0000_0000h
D60h MTL Tx Queue 1 HiCredit (MTL_TxQ1_HiCredit) 32 RW 0000_0000h
D64h MTL Tx Queue 1 LoCredit (MTL_TxQ1_LoCredit) 32 RW 0000_0000h
D6Ch MTL Queue 1 Interrupt Control Status 32 RW 0000_0000h
(MTL_Q1_Interrupt_Control_Status)
D70h MTL Rx Queue 1 Operation Mode (MTL_RxQ1_Operation_Mode) 32 RW 0000_0000h
D74h MTL Rx Queue 1 Missed Packet Overflow Counter 32 R 0000_0000h
(MTL_RxQ1_Missed_Packet_Overflow_Cnt)
D78h MTL Rx Queue 1 Debug (MTL_RxQ1_Debug) 32 R 0000_0000h
D7Ch MTL Rx Queue 1 Control (MTL_RxQ1_Control) 32 RW 0000_0000h
1000h DMA Mode (DMA_Mode) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 2659*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
1004h DMA System Bus Mode (DMA_SysBus_Mode) 32 RW 0000_0000h
1008h DMA Interrupt Status (DMA_Interrupt_Status) 32 R 0000_0000h
100Ch DMA Debug Status 0 (DMA_Debug_Status0) 32 R 0000_0000h
1050h DMA TBS Control (DMA_TBS_CTRL) 32 RW 0000_0000h
1080h DMA Safety Interrupt Status (DMA_Safety_Interrupt_Status) 32 R 0000_0000h
1100h DMA Channel 0 Control (DMA_CH0_Control) 32 RW 0000_0000h
1104h DMA Channel Tx Control (DMA_CH0_Tx_Control) 32 RW 0000_0000h
1108h DMA Channel Rx Control (DMA_CH0_Rx_Control) 32 RW 0000_0000h
1114h DMA Channel 0 Tx Descriptor List Address 32 RW 0000_0000h
(DMA_CH0_TxDesc_List_Address)
111Ch DMA Channel 0 Rx Descriptor List Address 32 RW 0000_0000h
(DMA_CH0_RxDesc_List_Address)
1120h DMA Channel 0 Tx Descriptor Tail Pointer 32 RW 0000_0000h
(DMA_CH0_TxDesc_Tail_Pointer)
1128h DMA Channel 0 Rx Descriptor List Pointer 32 RW 0000_0000h
(DMA_CH0_RxDesc_Tail_Pointer)
112Ch DMA Channel 0 Tx Descriptor Ring Length 32 RW 0000_0000h
(DMA_CH0_TxDesc_Ring_Length)
1130h DMA Channel 0 Rx Descriptor Ring Length 32 RW 0000_0000h
(DMA_CH0_RxDesc_Ring_Length)
1134h DMA Channel 0 Interrupt Enable (DMA_CH0_Interrupt_Enable) 32 RW 0000_0000h
1138h DMA Channel 0 Rx Interrupt Watchdog Timer 32 RW 0000_0000h
(DMA_CH0_Rx_Interrupt_Watchdog_Timer)
113Ch DMA Channel 0 Slot Function Control Status 32 RW 0000_07C0h
(DMA_CH0_Slot_Function_Control_Status)
1144h DMA Channel 0 Current Application Transmit Descriptor 32 R 0000_0000h
(DMA_CH0_Current_App_TxDesc)
114Ch DMA Channel 0 Current Application Receive Descriptor 32 R 0000_0000h
(DMA_CH0_Current_App_RxDesc)
1154h DMA Channel 0 Current Application Transmit Descriptor 32 R 0000_0000h
(DMA_CH0_Current_App_TxBuffer)
115Ch DMA Channel 0 Current Application Receive Buffer 32 R 0000_0000h
(DMA_CH0_Current_App_RxBuffer)
1160h DMA Channel 0 Status (DMA_CH0_Status) 32 RW 0000_0000h
1164h DMA Channel 0 Miss Frame Counter (DMA_CH0_Miss_Frame_Cnt) 32 R 0000_0000h
Table continues on the next page...

---

*Page 2660*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
1168h DMA Channel 0 Rx Parser Accept Count 32 R 0000_0000h
(DMA_CH0_RXP_Accept_Cnt)
116Ch DMA Channel 0 Rx ERI Count (DMA_CH0_RX_ERI_Cnt) 32 R 0000_0000h
1180h DMA Channel 1 Control (DMA_CH1_Control) 32 RW 0000_0000h
1184h DMA Channel 1 Tx Control (DMA_CH1_Tx_Control) 32 RW 0000_0000h
1188h DMA Channel 1 Rx Control (DMA_CH1_Rx_Control) 32 RW 0000_0000h
1194h DMA Channel 1 Tx Descriptor List Address 32 RW 0000_0000h
(DMA_CH1_TxDesc_List_Address)
119Ch DMA Channel 1 Rx Descriptor List Address 32 RW 0000_0000h
(DMA_CH1_RxDesc_List_Address)
11A0h DMA Channel 1 Tx Descriptor Tail Pointer 32 RW 0000_0000h
(DMA_CH1_TxDesc_Tail_Pointer)
11A8h DMA Channel 1 Rx Descriptor Tail Pointer 32 RW 0000_0000h
(DMA_CH1_RxDesc_Tail_Pointer)
11ACh DMA Channel 1 Tx Descriptor Ring Length 32 RW 0000_0000h
(DMA_CH1_TxDesc_Ring_Length)
11B0h DMA Channel 1 Rx Descriptor Ring Length 32 RW 0000_0000h
(DMA_CH1_RxDesc_Ring_Length)
11B4h DMA Channel 1 Interrupt Enable (DMA_CH1_Interrupt_Enable) 32 RW 0000_0000h
11B8h DMA Channel 1 Rx Interrupt Watchdog Timer 32 RW 0000_0000h
(DMA_CH1_Rx_Interrupt_Watchdog_Timer)
11BCh DMA Channel 1 Slot Function Control Status 32 RW 0000_07C0h
(DMA_CH1_Slot_Function_Control_Status)
11C4h DMA Channel 1 Current Application Transmit Descriptor 32 R 0000_0000h
(DMA_CH1_Current_App_TxDesc)
11CCh DMA Channel 1 Current Application Receive Descriptor 32 R 0000_0000h
(DMA_CH1_Current_App_RxDesc)
11D4h DMA Channel 1 Current Application Transmit Buffer 32 R 0000_0000h
(DMA_CH1_Current_App_TxBuffer)
11DCh DMA Channel 1 Current Application Receive Buffer 32 R 0000_0000h
(DMA_CH1_Current_App_RxBuffer)
11E0h DMA Channel 1 Status (DMA_CH1_Status) 32 RW 0000_0000h
11E4h DMA Channel 1 Miss Frame Counter (DMA_CH1_Miss_Frame_Cnt) 32 R 0000_0000h
11E8h DMA Channel 1 Rx Parser Accept Count 32 R 0000_0000h
(DMA_CH1_RXP_Accept_Cnt)
11ECh DMA Channel 1 Rx ERI Count (DMA_CH1_RX_ERI_Cnt) 32 R 0000_0000h
Table continues on the next page...

---

*Page 2661*

Ethernet Media Access Controller (EMAC)
Offset Register Access Reset value
Width
(In bits)

#### 72.17.2 MAC Configuration (MAC_Configuration)

Offset
Register Offset
MAC_Configuration 0h
Function
Establishes MAC's operating mode.
Table 541. Packet length based on CST and ACS bits
Received packet length CST ACS FCS stripping done
<1536 —
0 No
—
1 Yes (for Ethernet packets)
>=1536 —
0 No
1 —
Yes (for Type packets)
<1536 —
0 No
—
1 Yes (for Ethernet packets)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv GPSL Reserv
SARC IPC IPG S2KP CST ACS WD JD JE
ed CE ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R PS
ECRS Reserv
FES DM LM DO DCRS DR BL DC PRELEN TE RE
FD ed
W
Reset 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2662*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31 Reserved
—
30-28 Source Address Insertion Or Replacement Control
SARC Controls the source address insertion or replacement for all the transmitted packets.
Bit 30 of this field specifies which MAC Address register (0 or 1) is used for source address insertion or
replacement based on the values of bits [29:28]:
• 2'b0x: The mti_sa_ctrl_i and ati_sa_ctrl_i input signals control the SA field generation.
• 2'b10: If bit 30 = 0, MAC inserts the content of the MAC Address 0 registers in the SA field of all the
transmitted packets. If bit 30 = 1 and the Enable MAC Address Register 1 option is selected while
configuring the core, MAC inserts the content of the MAC Address 1 registers in the SA field of all
the transmitted packets.
• 2'b11: If bit 30 = 0, MAC replaces the content of the MAC Address 0 registers in the SA field of all
the transmitted packets. If bit 30 = 1 and the MAC Address Register 1 is enabled, MAC replaces the
content of the MAC Address 1 registers in the SA field of all the transmitted packets.
NOTE
Changes to this field take effect only on the start of a packet. If you write to the field when
a packet is being transmitted, only the subsequent packet can use the updated value. This
means that the current packet does not use the updated value.
000b - mti_sa_ctrl_i and ati_sa_ctrl_i input signals control the SA field generation
010b - Contents of are inserted in the SA field
011b - Contents of replace the SA field
110b - Contents of are inserted in the SA field
111b - Contents of replace the SA field
27 Checksum Offload
IPC Indicates the status of IP header or payload checksum checking.
If this field is 1, the field enables IPv4 header checksum checking and IPv4 or IPv6 TCP, UDP, or ICMP
payload checksum checking. When the field becomes 0, the COE function in the receiver is disabled.
The Layer 3 and Layer 4 packet filter and enable split header features automatically select the IPC full
checksum offload engine on the receive side. When any of these features are enabled, you must write 1 to
this field.
0b - Disabled
1b - Enabled
26-24 Inter-Packet Gap
IPG Controls the minimum IPG between packets during transmission.
The range of minimum IPG is valid in Full-Duplex mode, and in this mode, the minimum IPG can be
configured only for 64-bit times (IPG = 100). Lower values are not considered.
Table continues on the next page...

---

*Page 2663*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
When a JAM pattern is being transmitted because of backpressure activation, MAC does not consider the
minimum IPG.
This function (IPG less than 96-bit times) is valid only when MAC_Ext_Configuration[EIPGEN] is 0. If this
field is 1, the minimum IPG (greater than 96-bit times) is controlled according to the field's description.
000b - 96-bit times IPG
001b - 88-bit times IPG
010b - 80-bit times IPG
011b - 72-bit times IPG
100b - 64-bit times IPG
101b - 56-bit times IPG
110b - 48-bit times IPG
111b - 40-bit times IPG
23 Giant Packet Size Limit Control Enable
GPSLCE Enables and disables giant packet size limit control.
If this field = 1, MAC considers the value of MAC_Ext_Configuration[GPSL] to declare a received packet as
a giant packet. This field must be programmed to have a size of more than 1,518 bytes. Otherwise, MAC
considers 1,518 bytes as the giant packet limit.
When this field becomes 0, MAC considers the received packet as a giant packet if its size is greater than
1,518 bytes (1522 bytes for a tagged packet).
The WD, JE, and S2KP fields have a higher precedence over this field, which means MAC considers a
received packet as a giant packet when its size is greater than 9,018 bytes (9,022 bytes for a tagged packet)
with JE = 1 and the size of the jumbo packet greater than 2,000 bytes and S2KP = 1. If the WD field is 1,
the field terminates the received packet if it reaches the watchdog limit. Therefore, the programmed giant
packet limit must be less than the watchdog limit to get the giant packet status.
0b - Disabled
1b - Enabled
22 IEEE 802.3 Support For 2K Packets
S2KP Indicates the status of IEEE 802.3 support for 2K packets.
If this field is 1, MAC considers all the packets with up to 2,000-byte length as normal. When the JE field is
not 1, MAC considers all the received packets of size more than 2K bytes as giant packets.
When this field becomes 0 and the JE field is not 1, MAC considers all the received packets of size more
than 1,518 bytes (1,522 bytes for tagged) as giant packets. For more information about how the setting of
this field and the JE field impacts the giant packet status, see this status based on the S2KP and JE fields.
If the JE field is 1, writing 1 to the S2KP field has no effect on the giant packet status.
0b - Disabled
1b - Enabled
Table continues on the next page...

---

*Page 2664*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
21 CRC Stripping For Type Packets
CST Indicates the status of CRC stripping for Type packets.
If this field is 1, the last four bytes (FCS) of all the Ether-type packets are stripped and dropped before
forwarding the packet to the application.
This field is valid only when the receive packet size is more than or equal to 1536 bytes.
For information about how the settings of this field and the ACS field impact the packet length, see MAC
Configuration (MAC_Configuration) .
0b - Disabled
1b - Enabled
20 Automatic Pad Or CRC Stripping
ACS Indicates the status of automatic pad or CRC stripping.
If this field is 1, MAC strips the pad or the FCS field on the incoming packets only if the size of the packets is
less than 1,536 bytes. All the received packets with a size greater than or equal to 1,536 bytes are passed
to the application without stripping the pad or the FCS field.
When this field becomes 0, MAC passes all the incoming packets to the application, without
any modification.
For information about how the settings of this field and the CST field impact the packet length, see MAC
Configuration (MAC_Configuration) .
0b - Disabled
1b - Enabled
19 Watchdog Disable
WD Indicates the status of watchdog.
If this field is 1, MAC disables the watchdog timer on the receiver. MAC can receive packets of up to
16,383 bytes.
When this field becomes 0, MAC does not allow more than 2,048 bytes (10,240 if the JE field = 1) of the
packet being received. MAC cuts off bytes received after 2,048.
0b - Enabled
1b - Disabled
18 Reserved
—
17 Jabber Disable
JD Indicates the status of jabber.
If this field is 1, MAC disables the jabber timer on the transmitter. It can transfer packets of up to
16,383 bytes.
Table continues on the next page...

---

*Page 2665*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
If this field is 0 and the application sends more than 2,048 bytes of data (10,240 if the JE field = 1) during
transmission, MAC does not send the rest of the bytes in that packet.
0b - Enabled
1b - Disabled
16 Jumbo Packet Enable
JE Indicates the status of jumbo packets.
If this field is 1, MAC allows jumbo packets of 9,018 bytes (9,022 bytes for VLAN-tagged packets) without
reporting a giant packet error in the receive packet status.
0b - Disabled
1b - Enabled
15 Port Select
PS Selects the Ethernet line speed.
This field, along with the FES field, selects the exact line speed. In the 10/100 Mbit/s-only (always 1) or 1000
Mbit/s-only (always 0) configurations, this field is read-only (RO) with an appropriate value. By default, with
10/100/1000 Mbit/s configurations, this field is read-write (R/W). The mac_speed_o[1] signal reflects the
value of this field.
0b - For 1000 or 2500 Mbit/s operations
1b - For 10 or 100 Mbit/s operations
14 Speed
FES Indicates the speed.
This field selects the speed mode.
The mac_speed_o[0] signal indicates the value of this field.
0b - 10 Mbit/s if PS = 1 and 1 Gbps if PS = 0
1b - 100 Mbit/s if PS = 1 and 2.5 Gbps if PS = 0
13 Duplex Mode
DM Indicates the mode in which MAC operates.
If this field is 1, MAC operates in Full-Duplex mode in which it can transmit and receive simultaneously. The
field can only be read, with 1 as its default value, in full-duplex-only configurations.
0b - Half-duplex mode
1b - Full-duplex mode
12 Loopback Mode
LM Indicates the status of Loopback mode
If this field is 1, MAC operates in Loopback mode at GMII or MII. This mode requires the (G)MII Rx clock
input (CLK_RX_I) signal to function properly because the transmit clock is not internally looped back.
Table continues on the next page...

---

*Page 2666*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled
11 Enable Carrier Sense In Full-Duplex Mode
ECRSFD Indicates whether ECRSFD is enabled or disabled.
If this field is 1, the MAC transmitter checks the CRS signal before packet transmission in Full-Duplex mode.
MAC starts transmitting only when the CRS signal is low.
If this field becomes 0, the MAC transmitter ignores the status of the CRS signal.
0b - Disabled
1b - Enabled
10 Disable Receive Own
DO Enables or disables receive own.
If this field is 1, MAC disables the reception of packets when the gmii_txen_o signal asserts in Half-Duplex
mode. When the field is 0, MAC receives all the packets that PHY sent.
This field is not applicable in Full-Duplex mode.
0b - Enabled
1b - Disabled
9 Disable Carrier Sense During Transmission
DCRS Enables or disables carrier sense during transmission.
If this field is 1, the MAC transmitter ignores the (G)MII CRS signal during packet transmission in Half-Duplex
mode. As a result, no errors generate because of loss of carrier or no carrier during transmission.
When this field becomes 0, the MAC transmitter generates errors because of carrier sense. MAC can even
abort the transmission.
0b - Enabled
1b - Disabled
8 Disable Retry
DR Enables or disables retry attempts.
If this field is 1, MAC attempts only one transmission. When a collision occurs on GMII or MII, MAC ignores
the current packet transmission and reports a packet abort with excessive collision errors in the transmit
packet status.
If this field becomes 0, MAC retries based on the settings of the BL field of this register.
The settings of this field apply only in Half-Duplex mode.
0b - Enabled
1b - Disabled
7 Reserved
Table continues on the next page...

---

*Page 2667*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
—
6-5 Back-Off Limit
BL Determines the random integer number (r) of slot time delays (4,096 bit times for 1000/2500 Mbit/s; 512
bit times for 10/100 Mbit/s) for which MAC waits before rescheduling a transmission attempt during retry
attempts after a collision.
• n = retransmission attempt
• r = random integer that takes the value in the range 0 <= r < 2^k
This field is applicable only in Half-Duplex mode.
00b - k = min(n,10)
01b - k = min(n,8)
10b - k = min(n,4)
11b - k = min(n,1)
4 Deferral Check
DC Indicates the status of the deferral check function.
If this field is 1, the deferral check function is enabled in MAC, which issues a packet abort status,
with Tx_Excessive_Deferral_Error[TXEXSDEF] = 1 in the transmit packet status, when the transmit state
machine is deferred for more than 24,288 bit times in 10 Mbit/s or 100 Mbit/s mode.
If MAC is configured for a 1000/2500 Mbit/s operation, the threshold for deferral is 155,680 bit times. Deferral
begins when the transmitter is ready to transmit, but it is prevented because of an active carrier sense signal
(CRS) on GMII or MII.
The defer time is not cumulative. For example, if the transmitter defers for 10,000 bit times because the CRS
signal is active, and then the CRS signal becomes inactive, the transmitter transmits and collision happens.
Because of this collision, the transmitter needs to back off and then defer again after back off completion.
In such a scenario, the deferral timer resets to 0 and restarts.
When this field becomes 0, the deferral check function disables and MAC defers until the CRS signal
becomes inactive.
This field is applicable only in Half-Duplex mode.
0b - Disabled
1b - Enabled
3-2 Preamble Length for Transmit Packets
PRELEN Controls the number of preamble bytes that are added to the beginning of every transmit packet.
Preamble reduction occurs only when MAC operates in Full-Duplex mode.
00b - 7 bytes
01b - 5 bytes
10b - 3 bytes
11b - Reserved
Table continues on the next page...

---

*Page 2668*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
1 Transmitter Enable
TE Indicates the status of the transmitter.
• If this field is 1, the MAC transmit state machine is enabled for transmission on GMII or MII.
• When the field becomes 0, the MAC transmit state machine is disabled after it completes the
transmission of the current packet. The transmit state machine does not transmit any more packets.
0b - Disabled
1b - Enabled
0 Receiver Enable
RE Indicates the status of the receiver.
If this field is 1, MAC's receive state machine is enabled for receiving packets from GMII or MII. When this
field becomes 0, the MAC receive state machine is disabled after it completes the reception of the current
packet. The receive state machine does not receive any more packets from GMII or MII.
0b - Disabled
1b - Enabled

#### 72.17.3 MAC Extended Configuration (MAC_Ext_Configuration)

Offset
Register Offset
MAC_Ext_Configuration 4h
Function
Establishes MAC's operating mode.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv Reserv EIPGE Reserv DCRC
EIPG Reserved PDC USP SPEN
ed ed N ed C
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved GPSL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2669*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31 Reserved
—
30 Reserved
—
29-25 Extended Inter-Packet Gap
EIPG Indicates the value of the extended inter-packet gap.
The value of this field is applicable when EIPGEN = 1. The most-significant bits of this field, along with those
of MAC_Configuration[IPG] , render the minimum IPG greater than 96-bit times in steps of 8-bit times.
8'h00: 104-bit times
8'h01: 112-bit times
8'h02: 120-bit times
-----------------------
8'hFF: 2144-bit times
24 Extended Inter-Packet Gap Enable
EIPGEN Indicates the status of the extended inter-packet gap.
• If this field is 1, MAC interprets the EIPG field of this register and the IPG field of MAC Configuration
(MAC_Configuration) together as minimum IPG greater than 96-bit times in steps of 8-bit times.
• If this field is 0, MAC ignores the EIPG field of this register and interprets the IPG field of MAC
Configuration (MAC_Configuration) as minimum IPG less than or equal to 96-bit times in steps of
8-bit times.
NOTE
You must enable the extended inter-packet gap feature when operating in Full-Duplex
mode only. There could be undesirable effects on the back-pressure function and frame
transmission if the feature is enabled in Half-Duplex mode.
0b - Disabled
1b - Enabled
23 Reserved
—
22-20 Reserved
—
19 Packet Duplication Control
PDC Indicates the packet duplication control status.
Table continues on the next page...

---

*Page 2670*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• If this field is 1, the received packet with multicast or broadcast destination address is routed to
multiple receive DMA channels. MAC_Address0_High[DCS] identifies the receive DMA channels
(corresponding to the MAC Address register) that match the multicast or broadcast destination
addresses in the received packet. The DCS field is interpreted to be a one-hot value, each bit
corresponding to the receive DMA channel.
• If this field is 0, the received packet routes to a single receive DMA channel.
MAC_Address0_High[DCS] identifies the receive DMA channel corresponding to the MAC Address
register that matches the destination address in the received packet. The DCS field is interpreted as
a binary value.
0b - Disabled
1b - Enabled
18 Unicast Slow Protocol Packet Detect
USP Indicates the status of unicast slow-protocol packet detection.
• If this field is 1, MAC detects the slow-protocol packets with unicast address of the station specified
in MAC Address 0 High (MAC_Address0_High) and MAC Address 0 Low (MAC_Address0_Low) .
MAC also detects the slow-protocol packets with the slow-protocol multicast address (01-80-
C2-00-00-02).
• If this field is 0, MAC detects only slow-protocol packets with the slow protocol multicast address
specified in IEEE 802.3-2015, section 5.
0b - Disabled
1b - Enabled
17 Slow Protocol Detection Enable
SPEN Enables or disables slow protocol detection.
• If this field is 0, EMAC forwards all error-free, slow-protocol packets to the application. EMAC
considers such packets as normal-type packets.
• If this field is 1, EMAC processes the slow-protocol packets (Ether type 8809h) and provides the
slow protocol sub-type and code fields in receive status.
0b - Disabled
1b - Enabled
16 Disable CRC Checking For Received Packets
DCRCC Indicates the status of CRC checking.
If this field is 1, MAC receiver does not check the CRC field in the received packets. When this field becomes
0, the MAC receiver always checks the CRC field in the received packets.
0b - Enabled
1b - Disabled
15-14 Reserved
Table continues on the next page...

---

*Page 2671*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
—
13-0 Giant Packet Size Limit
GPSL Declares the status of a packet based on its size.
If the received packet size (in bytes) is greater than the value programmed in this field, MAC declares the
received packet as a giant packet. The value programmed in this field must be greater than or equal to 1,518
bytes. Also, any other programmed value is considered as 1,518 bytes.
For VLAN-tagged packets, MAC adds 4 bytes to the programmed value. If MAC_VLAN_Tag_Ctrl[EDVLP]
= 1, MAC adds 8 bytes to the programmed value for double VLAN-tagged packets.
The value of GPSL is valid if MAC_Configuration[GPSLCE] = 1.

#### 72.17.4 MAC Packet Filter (MAC_Packet_Filter)

Offset
Register Offset
MAC_Packet_Filter 8h
Function
Contains the filter controls for receiving packets.
Some of the controls from this register go to MAC's address check block that performs the first level of address filtering. The
second level of filtering is performed on the incoming packet based on other controls such as pass bad packets and pass
control packets.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
RA Reserved DNTU IPFE Reserved VTFE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved HPF SAF SAIF PCF DBF PM DAIF HMC HUC PR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2672*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31 Receive All
RA Indicates whether the received packets are enabled or disabled.
• If this field is 1, the MAC Receiver module passes all the received packets to the application,
irrespective of their passing the address filter. The result (pass or fail) of the SA or DA filtering is
updated in the corresponding field of the receive status word.
• If this field is 0, the module passes only those packets to the application that pass the SA or DA
address filter.
0b - Receive All is disabled
1b - Receive All is enabled
30-22 Reserved
—
21 Drop Non-TCP/UDP Over IP Packets
DNTU Indicates whether MAC drops or forwards non-TCP/UDP protocols over IP packets.
• If this field is 1, MAC drops these protocols over IP packets. MAC forwards only those packets that
the layer 4 filter processes.
• If this field is 0, MAC forwards all these protocols over IP packets.
0b - Forwards
1b - Drops
20 Layer 3 and Layer 4 Filter Enable
IPFE Indicates the status of layer 3 and layer 4 filters.
• If this field is 1, MAC drops packets that do not match the enabled layer 3 and layer 4 filters. If these
two filters are not enabled for matching, this field does not have any effect.
• If this field is 0, MAC forwards all the packets irrespective of the match status of the layer 3 and layer
4 fields.
0b - Disabled
1b - Enabled
19-17 Reserved
—
16 VLAN Tag Filter Enable
VTFE Indicates the status of VLAN tag filter.
• If this field is 1, MAC drops the VLAN-tagged packets that do not match the VLAN tag.
• If this field is 0, MAC forwards all the packets irrespective of the match status of the VLAN tag.
0b - Disabled
1b - Enabled
Table continues on the next page...

---

*Page 2673*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
15-11 Reserved
—
10 Hash Or Perfect Filter
HPF Indicates the status of hash or perfect filter.
• If this field is 1, the address filter passes a packet if it matches either perfect filtering or hash filtering
as defined by the HMC or HUC fields of this register.
• If this field is 0 and HUC = 1 or HMC = 1, the packet passes only if it matches the hash filter.
0b - Disabled
1b - Enabled
9 Source Address Filter Enable
SAF Indicates the status of SA filtering.
• If this field is 1, MAC compares the SA field of the received packets with the values programmed in
the enabled SA registers. If the comparison fails, MAC drops the packet.
• When this field becomes 0, MAC forwards the received packet to the application with an updated
field value, depending on the SA address comparison.
NOTE
According to the IEEE specification, bit 47 of the SA field is reserved. However, MAC
compares all the 48 bits. You must consider this when programming the MAC address
registers for SA.
0b - Disabled
1b - Enabled
8 SA Inverse Filtering
SAIF Indicates the status of SA inverse filtering.
• If this field is 1, the address check block operates in the inverse filtering mode for SA address
comparison. If the SA of a packet matches the values programmed in the SA registers, it is marked
as failing the SA address filter.
• If this field becomes 0 and the SA of a packet does not match the values programmed in the SA
registers, the SA is marked as failing the SA address filter.
0b - Disabled
1b - Enabled
7-6 Pass Control Packets
PCF Controls the forwarding of all the control packets (including unicast and multicast pause packets).
00b - MAC filters all the control packets from reaching the application
01b - MAC forwards all the control packets, except pause packets, to the application even if they
fail the address filter
Table continues on the next page...

---

*Page 2674*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
10b - MAC forwards all the control packets to the application even if they fail the address filter
11b - MAC forwards all the control packets that pass the address filter
5 Disable Broadcast Packets
DBF Enables or disables broadcast packets.
• If this field is 1, AFM blocks all the incoming broadcast packets. In addition, it overrides all the other
filter settings.
• If this field is 0, AFM passes all the received broadcast packets.
0b - Enabled
1b - Disabled
4 Pass All Multicast
PM Enables or disables the passing of received packets.
• If this field is 1, it indicates that all the received packets with a multicast destination address (first bit
in the destination address field is 1) are passed.
• If this field is 0, filtering of multicast packets depends on the settings of the HMC field.
0b - Disabled
1b - Enabled
3 DA Inverse Filtering
DAIF Indicates the status of DA inverse filtering.
• If this field is 1, the Address Check block operates in Inverse Filtering mode for the DA address
comparison of both unicast and multicast packets.
• If this field is 0, normal filtering of packets is performed.
0b - Disabled
1b - Enabled
2 Hash Multicast
HMC Indicates the status of hast multicast.
• If this field is 1, MAC performs the destination address filtering of received multicast packets
according to the hash table.
• If this field is 0, MAC performs the perfect destination address filtering for multicast packets, that is, it
compares the DA field with the values programmed in DA registers.
0b - Disabled
1b - Enabled
1 Hash Unicast
HUC Indicates the status of hash unicast.
Table continues on the next page...

---

*Page 2675*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• If this field is 1, MAC performs the destination address filtering of unicast packets according to the
hash table.
• When this field becomes 0, MAC performs a perfect destination address filtering for unicast packets,
that is, it compares the DA field with the values you program in the MAC Address registers.
0b - Disabled
1b - Enabled
0 Promiscuous Mode
PR Indicates the status of Promiscuous mode.
If this field is 1, the address filtering module passes all the incoming packets irrespective of the destination
or source address. MAC clears the SA or DA filter fail status fields of the receive status word when PR = 1.
0b - Disabled
1b - Enabled

#### 72.17.5 MAC Watchdog Timeout (MAC_Watchdog_Timeout)

Offset
Register Offset
MAC_Watchdog_Timeou Ch
t
Function
Controls the watchdog timeout for received packets.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved PWE Reserved WTO
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2676*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-9 Reserved
—
8 Programmable Watchdog Enable
PWE Indicates the status of programmable watchdog.
• If this field is 1 and MAC_Configuration[WD] is 0, the WTO field of this register is used as watchdog
timeout for a received packet.
• If this field is 0, MAC_Configuration[WD] and MAC_Configuration[JE] control the watchdog timeout
for a received packet.
0b - Disabled
1b - Enabled
7-4 Reserved
—
3-0 Watchdog Timeout
WTO Indicates the size of watchdog timer.
If PWE = 1 and MAC_Configuration[WD] = 0, this field is used as watchdog timeout for a received packet.
If the length of a received packet exceeds the value of this field, the packet terminates as an error packet.
NOTE
If PWE = 1, the value in this field must exceed 1,522 (05F2h). Otherwise, the IEEE
802.3-specified valid tagged packets are declared as error packets and dropped.
0000b - 2 KB
0001b - 3 KB
0010b - 4 KB
0011b - 5 KB
0100b - 6 KB
0101b - 7 KB
0110b - 8 KB
0111b - 9 KB
1000b - 10 KB
1001b - 11 KB
1010b - 12 KB
1011b - 13 KB
1100b - 14 KB
1101b - 15 KB
Table continues on the next page...

---

*Page 2677*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
1110b - 16383 bytes
1111b - Reserved

#### 72.17.6 MAC Hash Table First 32 Bits (MAC_Hash_Table_Reg0)

Offset
Register Offset
MAC_Hash_Table_Reg0 10h
Function
Contains the first 32 bits of the hash table, when the total width of the hash table is 128 bits or 256 bits.
NOTE
In this chip, upper 6 bits should be used for 64-bit Hash Table.
The hash table is used for group address filtering, for which the content of the destination address in the incoming packet is passed
through the CRC logic and the bits of Receive CRC Error Packets (Rx_CRC_Error_Packets) are used to index the contents of the
hash table as follows:
• 6 bits when you have a 64-bit hash
• 7 bits when you have a 128-bit hash
• 8 bits when you have a 256-bit hash
The most-significant bits determine the Hash Table register to be used, and the least-significant 5 bits determine the bit
within the register. For example, a hash value of 10_0000b (in 64-bit hash) selects bit 0 of MAC Hash Table Second 32
Bits (MAC_Hash_Table_Reg1) .
Perform these steps to calculate the hash value of the destination address:
1. Calculate the 32-bit CRC for DA (see section 3.2.8 in IEEE 802.3 for the steps to calculate CRC32).
2. Perform bit-wise reversal for the value obtained in step 1.
3. Use the upper 6, 7, or 8 bits from the value obtained in step 2.
If this field is 1, the packet is accepted. Otherwise, it is rejected. If MAC_Packet_Filter[PM] = 1, all multicast packets are accepted
regardless of the multicast hash values.
If this register is configured to be double-synchronized with the (G)MII clock domain, the synchronization is triggered only when
bits [31:24] (in Little-Endian mode) or bits [7:0] (in Big-Endian mode) of the Hash Table registers are written to.
If double-synchronization is enabled, consecutive writes to this register must be performed after at least four clock cycles in the
destination clock domain.

---

*Page 2678*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
HT31T0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
HT31T0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 MAC Hash Table First 32 Bits
HT31T0 Contains the first 32 bits [31:0] of the hash table.

#### 72.17.7 MAC Hash Table Second 32 Bits (MAC_Hash_Table_Reg1)

Offset
Register Offset
MAC_Hash_Table_Reg1 14h
Function
Contains the second 32 bits of the hash table, when the width of the hash table is 128 or 256 bits.
NOTE
In this chip, upper 6 bits should be used for 64-bit Hash Table.
The hash table is used for group address filtering, for which the content of the destination address in the incoming packet is passed
through the CRC logic and the bits of Receive CRC Error Packets (Rx_CRC_Error_Packets) are used to index the contents of the
hash table as follows:
• 6 bits when you have a 64-bit hash
• 7 bits when you have a 128-bit hash
• 8 bits when you have a 256-bit hash
The most-significant bits determine the Hash Table register to be used, and the least-significant 5 bits determine the bit
within the register. For example, a hash value of 10_0000b (in 64-bit hash) selects bit 0 of MAC Hash Table Second 32
Bits (MAC_Hash_Table_Reg1) .
Perform these steps to calculate the hash value of the destination address:
1. Calculate the 32-bit CRC for DA (see section 3.2.8 in IEEE 802.3 for the steps to calculate CRC32).
2. Perform bit-wise reversal for the value obtained in step 1.
3. Take the upper 6, 7, or 8 bits from the value obtained in step 2.

---

*Page 2679*

Ethernet Media Access Controller (EMAC)
If the corresponding bit value of this register is 1'b1, the packet is accepted. Otherwise, it is rejected. If MAC_Packet_Filter[PM]
= 1, all multicast packets are accepted regardless of the multicast hash values.
If this register is configured to be double-synchronized with the (G)MII clock domain, the synchronization is triggered only when
bits [31:24] (in Little-Endian mode) or bits [7:0] (in Big-Endian mode) of the Hash Table registers are written to.
If double-synchronization is enabled, consecutive writes to this register must be performed after at least four clock cycles in the
destination clock domain.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
HT63T32
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
HT63T32
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 MAC Hash Table Second 32 Bits
HT63T32 Contains the second 32 bits [63:32] of the hash table.

#### 72.17.8 MAC VLAN Tag (MAC_VLAN_Tag)

Offset
Register Offset
MAC_VLAN_Tag 50h
Function
Identifies the IEEE 802.1Q VLAN type packets.

---

*Page 2680*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
EIVLR Reserv ERIVL EDVL EVLR Reserv DOVL ERSV
EIVLS VTHM EVLS ESVL VTIM ETV
XS ed T P XS ed TC LM
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
VL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Enable Inner VLAN Tag In Receive Status
EIVLRXS Indicates whether the inner VLAN tag in receive status is enabled or disabled.
• If this field is 1, MAC provides the inner VLAN tag in the receive status.
• If this field is 0, MAC does not provide the inner VLAN tag in the receive status.
0b - Disabled
1b - Enabled
30 Reserved
—
29-28 Enable Inner VLAN Tag Stripping
EIVLS Indicates the stripping operation on the inner VLAN tag in a received packet. The field enables or disables
inner VLAN tag stripping on receive.
00b - Do not strip
01b - Strip if VLAN filter passes
10b - Strip if VLAN filter fails
11b - Always strip
27 Enable Inner VLAN Tag Comparison
ERIVLT Enables or disables the inner VLAN tag.
• If ERIVLT = VTHM = EDVLP = 1, the EMAC receiver enables the VLAN hash filtering operation on
the inner VLAN tag (if present).
• If ERIVLT = 0 and VTHM = 1, the EMAC receiver enables the VLAN hash filtering operation on
the outer VLAN tag (if present). ERSVLM and DOVLTC determine which VLAN type is enabled for
filtering.
0b - Disabled
1b - Enabled
Table continues on the next page...

---

*Page 2681*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
26 Enable Double VLAN Processing
EDVLP Enables or disables double VLAN processing.
• If this field is 1, MAC enables processing of up to two VLAN tags on transmit and receive (if present).
• If this field is 0, MAC enables processing of up to one VLAN tag on transmit and receive (if present).
0b - Disabled
1b - Enabled
25 VLAN Tag Hash Table Match
VTHM Indicates the status of VLAN tag hash table match.
• If this field is 1, the most-significant four bits of CRC-32 of VLAN tag are used to index the content of
MAC VLAN Hash Table (MAC_VLAN_Hash_Table) . See VLAN filtering for details.
• If MAC_VLAN_Hash_Table[VLHT] = 1, corresponding to the index, it indicates that the Ethernet
packet matches the VLAN hash table. See VLAN filtering for details.
• If ETV = 1, the CRC of the 12-bit VLAN identifier (VID) is used for comparison. When the ETV field
becomes 0, the CRC of the 16-bit VLAN tag is used for comparison.
• If VTHM = 1, the VLAN hash match operation is not performed.
0b - Disabled
1b - Enabled
24 Enable VLAN Tag In Receive Status
EVLRXS Indicates whether the VLAN tag in receive status is enabled.
• If this field is 1, MAC provides the outer VLAN tag in the receive status.
• If this field is 0, MAC does not provide the outer VLAN tag in receive status.
0b - Disabled
1b - Enabled
23 Reserved
—
22-21 Enable VLAN Tag Stripping
EVLS Indicates the stripping operation on the outer VLAN tag in received packets. The field enables or disables
VLAN tag stripping on receive.
00b - Do not strip
01b - Strip if VLAN filter passes
10b - Strip if VLAN filter fails
11b - Always strip
20 Disable VLAN Type Check
Table continues on the next page...

---

*Page 2682*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
DOVLTC Enables or disables VLAN type check for VLAN hash filtering.
• If this field is 1, the MAC VLAN hash filter matches or filters the VLAN tag that the ERIVLT field
specifies only when the VLAN tag type is similar to the one that the ERSVLM field specifies.
• If this field is 0, the MAC VLAN hash filter does not check whether the VLAN tag that the ERIVLT
field specifies is of type S-VLAN or C-VLAN.
• The VLAN filter is bypassed when VLAN type of received packet do not match the programmed
VLAN type in the VLAN filter.
0b - Enabled
1b - Disabled
19 Enable Receive S-VLAN Match
ERSVLM Enables or disables receive S-VLAN match for VLAN hash filtering.
• If this field is 1, the MAC receiver enables VLAN hash filtering or S-VLAN matching (type = 88A8h)
packets.
• If this field is 0, the MAC receiver enables VLAN hash filtering or matching for C-VLAN (type =
8100h) packets.
The ERIVLT field determines the VLAN tag position considered for VLAN hash filtering or matching.
0b - Disabled
1b - Enabled
18 Enable S-VLAN
ESVL Enables or disables S-VLAN.
If this field is 1, the MAC transmitter and receiver consider the S-VLAN packets (type = 88A8h) as valid
VLAN-tagged packets.
0b - Disabled
1b - Enabled
17 VLAN Tag Inverse Match Enable
VTIM Enables or disables VLAN tag inverse matching.
• If this field is 1, it enables the VLAN tag inverse matching. The packets without matching VLAN tag
are marked as matched.
• If this field is 0, it enables the VLAN tag perfect matching. The packets with matched VLAN tag are
marked as matched.
0b - Disabled
1b - Enabled
16 Enable Tag For VLAN
ETV Enables or disables 12-bit VLAN tag comparison for VLAN hash filtering.
Table continues on the next page...

---

*Page 2683*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• If this field is 1, a 12-bit VLAN identifier is used for VLAN hash filtering instead of the complete 16-bit
VLAN tag. Bits [11:0] of the VLAN tag in the received VLAN-tagged packet are used for hash-based
VLAN filtering.
• When this field becomes 0, all 16 bits of the 15th and 16th bytes of the received VLAN packet are
used for VLAN hash filtering.
0b - Disabled
1b - Enabled
15-0 VLAN Tag Identifier for Receive Packets
VL Contains the 802.1Q VLAN tag to identify the VLAN packets.
This VLAN tag identifier is compared to the 15th and 16th bytes of the packets being received for
VLAN packets.
The following list describes the bits of this field:
• 15:13 — User priority
• 12 — Canonical Format Indicator (CFI) or Drop Eligible Indicator (DEI)
• 11:0 — VID field of VLAN tag
When ETV = 1, only the VID is used for comparison.
When VL = 0, and ETV = 1, MAC does not check the 15th and 16th bytes for VLAN tag comparison and
declares all packets with Type field value of 8100h or 88a8h as VLAN packets.

#### 72.17.9 MAC VLAN Tag Control (MAC_VLAN_Tag_Ctrl)

Offset
Register Offset
MAC_VLAN_Tag_Ctrl 50h
Function
Is the redefined version of the MAC_VLAN_Tag register.
This register provides the control and addressing fields required for indirect access to the MAC_VLAN_Tag_Filter registers. It also
contains the address offset, command type, and busy bit for CSR access of the MAC VLAN Hash Filter registers.

---

*Page 2684*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
EIVLR Reserv ERIVL EDVL EVLR Reserv DOVL ERSV
EIVLS VTHM EVLS ESVL VTIM ETV
XS ed T P XS ed TC LM
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved OFS CT OB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Enable Inner VLAN Tag In Receive Status
EIVLRXS Indicates whether the inner VLAN tag in receive status is enabled or disabled.
• If this field is 1, MAC provides the inner VLAN tag in the receive status.
• If this field is 0, MAC does not provide the inner VLAN tag in the receive status.
0b - Disabled
1b - Enabled
30 Reserved
—
29-28 Enable Inner VLAN Tag Stripping
EIVLS Indicates the stripping operation on the inner VLAN tag in a received packet. The field enables or disables
inner VLAN tag stripping on receive.
00b - Do not strip
01b - Strip if VLAN filter passes
10b - Strip if VLAN filter fails
11b - Always strip
27 Enable Inner VLAN Tag Comparison
ERIVLT Enables or disables the inner VLAN tag.
• If ERIVLT = VTHM = EDVLP = 1, the EMAC receiver enables the VLAN hash filtering operation on
the inner VLAN tag (if present).
• If ERIVLT = 0 and VTHM = 1, the EMAC receiver enables the VLAN hash filtering operation on
the outer VLAN tag (if present). ERSVLM and DOVLTC determine which VLAN type is enabled for
filtering.
0b - Disabled
1b - Enabled
Table continues on the next page...

---

*Page 2685*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
26 Enable Double VLAN Processing
EDVLP Enables or disables double VLAN processing.
• If this field is 1, MAC enables processing of up to two VLAN tags on transmit and receive (if present).
• If this field is 0, MAC enables processing of up to one VLAN tag on transmit and receive (if present).
0b - Disabled
1b - Enabled
25 VLAN Tag Hash Table Match
VTHM Indicates the status of VLAN tag hash table match.
• If this field is 1, the most-significant four bits of CRC-32 of VLAN tag are used to index the content of
MAC VLAN Hash Table (MAC_VLAN_Hash_Table) . See VLAN filtering for details.
• If MAC_VLAN_Hash_Table[VLHT] = 1, corresponding to the index, it indicates that the Ethernet
packet matches the VLAN hash table. See VLAN filtering for details.
• If ETV = 1, the CRC of the 12-bit VLAN identifier (VID) is used for comparison. When the ETV field
becomes 0, the CRC of the 16-bit VLAN tag is used for comparison.
• If VTHM = 1, the VLAN hash match operation is not performed.
0b - Disabled
1b - Enabled
24 Enable VLAN Tag In Receive Status
EVLRXS Indicates whether the VLAN tag in receive status is enabled.
• If this field is 1, MAC provides the outer VLAN tag in the receive status.
• If this field is 0, MAC does not provide the outer VLAN tag in receive status.
0b - Disabled
1b - Enabled
23 Reserved
—
22-21 Enable VLAN Tag Stripping
EVLS Indicates the stripping operation on the outer VLAN tag in received packets. The field enables or disables
VLAN tag stripping on receive.
00b - Do not strip
01b - Strip if VLAN filter passes
10b - Strip if VLAN filter fails
11b - Always strip
20 Disable VLAN Type Check
Table continues on the next page...

---

*Page 2686*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
DOVLTC Enables or disables VLAN type check for VLAN hash filtering.
• If this field is 1, the MAC VLAN hash filter matches or filters the VLAN tag that the ERIVLT field specifies
only when the VLAN tag type is similar to the one that the ERSVLM field specifies.
• If this field is 0, the MAC VLAN hash filter does not check whether the VLAN tag that the ERIVLT field
specifies is of type S-VLAN or C-VLAN.
0b - Enabled
1b - Disabled
19 Enable Receive S-VLAN Match
ERSVLM Enables or disables receive S-VLAN match for VLAN hash filtering.
• If this field is 1, the MAC receiver enables VLAN hash filtering or S-VLAN matching (type = 88A8h)
packets.
• If this field is 0, the MAC receiver enables VLAN hash filtering or matching for C-VLAN (type =
8100h) packets.
The ERIVLT field determines the VLAN tag position considered for VLAN hash filtering or matching.
0b - Disabled
1b - Enabled
18 Enable S-VLAN
ESVL Enables or disables S-VLAN.
If this field is 1, the MAC transmitter and receiver consider the S-VLAN packets (type = 88A8h) as valid
VLAN-tagged packets.
0b - Disabled
1b - Enabled
17 VLAN Tag Inverse Match Enable
VTIM Enables or disables VLAN tag inverse matching.
• If this field is 1, it enables the VLAN tag inverse matching. The packets without matching VLAN tag
are marked as matched.
• If this field is 0, it enables the VLAN tag perfect matching. The packets with matched VLAN tag are
marked as matched.
0b - Disabled
1b - Enabled
16 Enable Tag For VLAN
ETV Enables or disables 12-bit VLAN tag comparison for VLAN hash filtering.
• If this field is 1, a 12-bit VLAN identifier is used for VLAN hash filtering instead of the complete 16-bit
VLAN tag. Bits [11:0] of the VLAN tag in the received VLAN-tagged packet are used for hash-based
VLAN filtering.
Table continues on the next page...

---

*Page 2687*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• When this field becomes 0, all 16 bits of the 15th and 16th bytes of the received VLAN packet are
used for VLAN hash filtering.
0b - Disabled
1b - Enabled
15-4 Reserved
—
3-2 Offset
OFS Holds the address offset of the MAC VLAN Tag Filter register that the application tries to access.
The width of this field depends on the number of enabled MAC VLAN Tag registers.
1 Command Type
CT Specifies whether the current register access indicates a read or a write operation. It indicates a read
operation if this field is 1 and a write operation when the field is 0.
0b - Write operation
1b - Read operation
0 Operation Busy
OB Indicates the operation busy status.
• This field becomes 1 to initiate a read or write command for an indirect access to the Per VLAN Tag
Filter register.
• The field becomes 0 when the read or write command to Per VLAN Tag Filter indirect access
register completes. The next indirect register access can be initiated only after the field resets.
During a write operation, the field becomes 0 only after the data is written into a
MAC_VLAN_Tag_Filter register.
During a read operation, the data must be read from MAC VLAN Tag Data (MAC_VLAN_Tag_Data) only
after this field becomes 0.
0b - Disabled
1b - Enabled

#### 72.17.10 MAC VLAN Tag Data (MAC_VLAN_Tag_Data)

Offset
Register Offset
MAC_VLAN_Tag_Data 54h
Function
Holds the read and write data for indirect access to the Per VLAN Tag registers.

---

*Page 2688*

Ethernet Media Access Controller (EMAC)
• During read access, this register contains valid read data only after MAC_VLAN_Tag_Ctrl[OB] becomes 0.
• During write access, this register must become valid before you write 1 to MAC_VLAN_Tag_Ctrl[OB] .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
DMAC DMAC ERIVL ERSV DOVL
Reserved Reserved ETV VEN
HN HEN T LM TC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
VID
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-26 Reserved
—
25 DMA Channel Number
DMACHN Indicates the DMA channel number to which the VLAN-tagged frame is to be routed if it passes the VLAN
tag filter programmed using this field.
If the routing based on VLAN tag filter is not necessary, this field does not need to be programmed.
24 DMA Channel Number Enable
DMACHEN Enables or disables the DMA channel number value programmed using the DMACHN field.
If this field is 0, the routing does not occur based on the VLAN filter result. The frame is routed on the basis
of DA-based DMA channel routing.
0b - Disabled
1b - Enabled
23-21 Reserved
—
20 Enable Inner VLAN Tag
ERIVLT Enables or disables inner VLAN tag comparison.
• This field is valid only when the double VLAN tag enable of the filter is set.
• If this field and the EDVLP field is 1, the MAC receiver enables operation on the inner VLAN tag (if
present).
• If this field is 0, the MAC receiver enables operation on the outer VLAN tag (if present).
Table continues on the next page...

---

*Page 2689*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled
19 Enable S-VLAN Match
ERSVLM Enables or disables S-VLAN match for received frames.
This field is valid only when the VLAN tag enable of the filter is set.
• If this field is 1, the MAC receiver enables filtering or matching for S-VLAN (type = 88A8h) packets.
• If this field is 0, the MAC receiver enables filtering or matching for C-VLAN (type = 8100h) packets.
0b - Disabled
1b - Enabled
18 Disable VLAN Type Comparison
DOVLTC Enables or disables VLAN type comparison.
This field is valid only when the VEN field is 1.
• If this field is 1, MAC does not check whether the VLAN tag that ERIVLT specifies is of type S-VLAN
or C-VLAN.
• If this field is 0, MAC filters or matches the VLAN tag that the ERIVLT field specifies only when the
VLAN tag type is similar to the one that the ERSVLM field specifies.
0b - Enabled
1b - Disabled
17 VLAN Comparison
ETV Indicates 12-bit or 16-bit VLAN comparison.
• This field is valid only when the VEN field is 1.
• When the value of this field is 1, a 12-bit VLAN identifier is used for filter and comparison instead of
the complete 16-bit VLAN tag. Bits [11:0] of the VLAN tag are compared with the corresponding field
in the received VLAN-tagged packet.
0b - 16-bit VLAN comparison
1b - 12-bit VLAN comparison
16 VLAN Tag Enable
VEN Enables or disables the VLAN tag.
• If this field is 1, MAC compares the VLAN tag of the received packet with the VLAN tag ID.
• If this field is 0, no comparison is performed irrespective of the programming of the other fields.
0b - Disabled
1b - Enabled
15-0 VLAN Tag ID
Table continues on the next page...

---

*Page 2690*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
VID Holds the VLAN tag value that MAC uses for perfect comparison. The field is valid when VEN = 1.

#### 72.17.11 MAC VLAN Tag Filter 0 (MAC_VLAN_Tag_Filter0)

Offset
Register Offset
MAC_VLAN_Tag_Filter0 54h
Function
Contains VLAN tag filter 0 control information.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
DMAC DMAC ERIVL ERSV DOVL
Reserved Reserved ETV VEN
HN HEN T LM TC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
VID
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-26 Reserved
—
25 DMA Channel Number
DMACHN Indicates the DMA channel number to which the VLAN-tagged frame is to be routed if it passes the VLAN
tag filter programmed using this field.
If the routing based on VLAN tag filter is not necessary, this field does not need to be programmed.
24 DMA Channel Number Enable
DMACHEN Enables or disables the DMA channel number value programmed using the DMACHN field.
If this field is 0, the routing does not occur based on the VLAN filter result. The frame is routed on the basis
of DA-based DMA channel routing.
Table continues on the next page...

---

*Page 2691*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled
23-21 Reserved
—
20 Enable Inner VLAN Tag
ERIVLT Enables or disables inner VLAN tag comparison.
• This field is valid only when the double VLAN tag enable of the filter is set.
• If this field and the EDVLP field is 1, the MAC receiver enables operation on the inner VLAN tag (if
present).
• If this field is 0, the MAC receiver enables operation on the outer VLAN tag (if present).
0b - Disabled
1b - Enabled
19 Enable S-VLAN Match
ERSVLM Enables or disables S-VLAN match for received frames.
This field is valid only when the VLAN tag enable of the filter is set.
• If this field is 1, the MAC receiver enables filtering or matching for S-VLAN (type = 88A8h) packets.
• If this field is 0, the MAC receiver enables filtering or matching for C-VLAN (type = 8100h) packets.
0b - Disabled
1b - Enabled
18 Disable VLAN Type Comparison
DOVLTC Enables or disables VLAN type comparison.
This field is valid only when the VEN field is 1.
• If this field is 1, MAC does not check whether the VLAN tag that ERIVLT specifies is of type S-VLAN
or C-VLAN.
• If this field is 0, MAC filters or matches the VLAN tag that the ERIVLT field specifies only when the
VLAN tag type is similar to the one that the ERSVLM field specifies.
0b - Enabled
1b - Disabled
17 VLAN Comparison
ETV Indicates 12-bit or 16-bit VLAN comparison.
• This field is valid only when the VEN field is 1.
• When the value of this field is 1, a 12-bit VLAN identifier is used for filter and comparison instead of
the complete 16-bit VLAN tag. Bits [11:0] of the VLAN tag are compared with the corresponding field
in the received VLAN-tagged packet.
Table continues on the next page...

---

*Page 2692*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - 16-bit VLAN comparison
1b - 12-bit VLAN comparison
16 VLAN Tag Enable
VEN Enables or disables the VLAN tag.
• If this field is 1, MAC compares the VLAN tag of the received packet with the VLAN tag ID.
• If this field is 0, no comparison is performed irrespective of the programming of the other fields.
0b - Disabled
1b - Enabled
15-0 VLAN Tag ID
VID Holds the VLAN tag value that MAC uses for perfect comparison. The field is valid when VEN = 1.

#### 72.17.12 MAC VLAN Tag Filter 1 (MAC_VLAN_Tag_Filter1)

Offset
Register Offset
MAC_VLAN_Tag_Filter1 54h
Function
Contains VLAN tag filter 1 control information.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
DMAC DMAC ERIVL ERSV DOVL
Reserved Reserved ETV VEN
HN HEN T LM TC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
VID
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2693*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-26 Reserved
—
25 DMA Channel Number
DMACHN Indicates the DMA channel number to which the VLAN-tagged frame is to be routed if it passes the VLAN
tag filter programmed using this field.
If the routing based on VLAN tag filter is not necessary, this field does not need to be programmed.
24 DMA Channel Number Enable
DMACHEN Enables or disables the DMA channel number value programmed using the DMACHN field.
If this field is 0, the routing does not occur based on the VLAN filter result. The frame is routed on the basis
of DA-based DMA channel routing.
0b - Disabled
1b - Enabled
23-21 Reserved
—
20 Enable Inner VLAN Tag
ERIVLT Enables or disables inner VLAN tag comparison.
• This field is valid only when the double VLAN tag enable of the filter is set.
• If this field and the EDVLP field is 1, the MAC receiver enables operation on the inner VLAN tag (if
present).
• If this field is 0, the MAC receiver enables operation on the outer VLAN tag (if present).
0b - Disabled
1b - Enabled
19 Enable S-VLAN Match
ERSVLM Enables or disables S-VLAN match for received frames.
This field is valid only when the VLAN tag enable of the filter is set.
• If this field is 1, the MAC receiver enables filtering or matching for S-VLAN (type = 88A8h) packets.
• If this field is 0, the MAC receiver enables filtering or matching for C-VLAN (type = 8100h) packets.
0b - Disabled
1b - Enabled
18 Disable VLAN Type Comparison
DOVLTC Enables or disables VLAN type comparison.
This field is valid only when the VEN field is 1.
Table continues on the next page...

---

*Page 2694*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• If this field is 1, MAC does not check whether the VLAN tag that ERIVLT specifies is of type S-VLAN
or C-VLAN.
• If this field is 0, MAC filters or matches the VLAN tag that the ERIVLT field specifies only when the
VLAN tag type is similar to the one that the ERSVLM field specifies.
0b - Enabled
1b - Disabled
17 VLAN Comparison
ETV Indicates 12-bit or 16-bit VLAN comparison.
• This field is valid only when the VEN field is 1.
• When the value of this field is 1, a 12-bit VLAN identifier is used for filter and comparison instead of
the complete 16-bit VLAN tag. Bits [11:0] of the VLAN tag are compared with the corresponding field
in the received VLAN-tagged packet.
0b - 16-bit VLAN comparison
1b - 12-bit VLAN comparison
16 VLAN Tag Enable
VEN Enables or disables the VLAN tag.
• If this field is 1, MAC compares the VLAN tag of the received packet with the VLAN tag ID.
• If this field is 0, no comparison is performed irrespective of the programming of the other fields.
0b - Disabled
1b - Enabled
15-0 VLAN Tag ID
VID Holds the VLAN tag value that MAC uses for perfect comparison. The field is valid when VEN = 1.

#### 72.17.13 MAC VLAN Tag Filter 2 (MAC_VLAN_Tag_Filter2)

Offset
Register Offset
MAC_VLAN_Tag_Filter2 54h
Function
Contains VLAN tag filter 2 control information.

---

*Page 2695*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
DMAC DMAC ERIVL ERSV DOVL
Reserved Reserved ETV VEN
HN HEN T LM TC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
VID
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-26 Reserved
—
25 DMA Channel Number
DMACHN Indicates the DMA channel number to which the VLAN-tagged frame is to be routed if it passes the VLAN
tag filter programmed using this field.
If the routing based on VLAN tag filter is not necessary, this field does not need to be programmed.
24 DMA Channel Number Enable
DMACHEN Enables or disables the DMA channel number value programmed using the DMACHN field.
If this field is 0, the routing does not occur based on the VLAN filter result. The frame is routed on the basis
of DA-based DMA channel routing.
0b - Disabled
1b - Enabled
23-21 Reserved
—
20 Enable Inner VLAN Tag
ERIVLT Enables or disables inner VLAN tag comparison.
• This field is valid only when the double VLAN tag enable of the filter is set.
• If this field and the EDVLP field is 1, the MAC receiver enables operation on the inner VLAN tag (if
present).
• If this field is 0, the MAC receiver enables operation on the outer VLAN tag (if present).
0b - Disabled
1b - Enabled
19 Enable S-VLAN Match
Table continues on the next page...

---

*Page 2696*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
ERSVLM Enables or disables S-VLAN match for received frames.
This field is valid only when the VLAN tag enable of the filter is set.
• If this field is 1, the MAC receiver enables filtering or matching for S-VLAN (type = 88A8h) packets.
• If this field is 0, the MAC receiver enables filtering or matching for C-VLAN (type = 8100h) packets.
0b - Disabled
1b - Enabled
18 Disable VLAN Type Comparison
DOVLTC Enables or disables VLAN type comparison.
This field is valid only when the VEN field is 1.
• If this field is 1, MAC does not check whether the VLAN tag that ERIVLT specifies is of type S-VLAN
or C-VLAN.
• If this field is 0, MAC filters or matches the VLAN tag that the ERIVLT field specifies only when the
VLAN tag type is similar to the one that the ERSVLM field specifies.
0b - Enabled
1b - Disabled
17 VLAN Comparison
ETV Indicates 12-bit or 16-bit VLAN comparison.
• This field is valid only when the VEN field is 1.
• When the value of this field is 1, a 12-bit VLAN identifier is used for filter and comparison instead of
the complete 16-bit VLAN tag. Bits [11:0] of the VLAN tag are compared with the corresponding field
in the received VLAN-tagged packet.
0b - 16-bit VLAN comparison
1b - 12-bit VLAN comparison
16 VLAN Tag Enable
VEN Enables or disables the VLAN tag.
• If this field is 1, MAC compares the VLAN tag of the received packet with the VLAN tag ID.
• If this field is 0, no comparison is performed irrespective of the programming of the other fields.
0b - Disabled
1b - Enabled
15-0 VLAN Tag ID
VID Holds the VLAN tag value that MAC uses for perfect comparison. The field is valid when VEN = 1.

---

*Page 2697*

Ethernet Media Access Controller (EMAC)

#### 72.17.14 MAC VLAN Tag Filter 3 (MAC_VLAN_Tag_Filter3)

Offset
Register Offset
MAC_VLAN_Tag_Filter3 54h
Function
Contains VLAN tag filter 3 control information.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
DMAC DMAC ERIVL ERSV DOVL
Reserved Reserved ETV VEN
HN HEN T LM TC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
VID
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-26 Reserved
—
25 DMA Channel Number
DMACHN Indicates the DMA channel number to which the VLAN-tagged frame is to be routed if it passes the VLAN
tag filter programmed using this field.
If the routing based on VLAN tag filter is not necessary, this field does not need to be programmed.
24 DMA Channel Number Enable
DMACHEN Enables or disables the DMA channel number value programmed using the DMACHN field.
If this field is 0, the routing does not occur based on the VLAN filter result. The frame is routed on the basis
of DA-based DMA channel routing.
0b - Disabled
1b - Enabled
23-21 Reserved
—
Table continues on the next page...

---

*Page 2698*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
20 Enable Inner VLAN Tag
ERIVLT Enables or disables inner VLAN tag comparison.
• This field is valid only when the double VLAN tag enable of the filter is set.
• If this field and the EDVLP field is 1, the MAC receiver enables operation on the inner VLAN tag (if
present).
• If this field is 0, the MAC receiver enables operation on the outer VLAN tag (if present).
0b - Disabled
1b - Enabled
19 Enable S-VLAN Match
ERSVLM Enables or disables S-VLAN match for received frames.
This field is valid only when the VLAN tag enable of the filter is set.
• If this field is 1, the MAC receiver enables filtering or matching for S-VLAN (type = 88A8h) packets.
• If this field is 0, the MAC receiver enables filtering or matching for C-VLAN (type = 8100h) packets.
0b - Disabled
1b - Enabled
18 Disable VLAN Type Comparison
DOVLTC Enables or disables VLAN type comparison.
This field is valid only when the VEN field is 1.
• If this field is 1, MAC does not check whether the VLAN tag that ERIVLT specifies is of type S-VLAN
or C-VLAN.
• If this field is 0, MAC filters or matches the VLAN tag that the ERIVLT field specifies only when the
VLAN tag type is similar to the one that the ERSVLM field specifies.
0b - Enabled
1b - Disabled
17 VLAN Comparison
ETV Indicates 12-bit or 16-bit VLAN comparison.
• This field is valid only when the VEN field is 1.
• When the value of this field is 1, a 12-bit VLAN identifier is used for filter and comparison instead of
the complete 16-bit VLAN tag. Bits [11:0] of the VLAN tag are compared with the corresponding field
in the received VLAN-tagged packet.
0b - 16-bit VLAN comparison
1b - 12-bit VLAN comparison
16 VLAN Tag Enable
VEN Enables or disables the VLAN tag.
Table continues on the next page...

---

*Page 2699*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• If this field is 1, MAC compares the VLAN tag of the received packet with the VLAN tag ID.
• If this field is 0, no comparison is performed irrespective of the programming of the other fields.
0b - Disabled
1b - Enabled
15-0 VLAN Tag ID
VID Holds the VLAN tag value that MAC uses for perfect comparison. The field is valid when VEN = 1.

#### 72.17.15 MAC VLAN Hash Table (MAC_VLAN_Hash_Table)

Offset
Register Offset
MAC_VLAN_Hash_Table 58h
Function
If MAC_VLAN_Tag_Ctrl[VTHM] = 1, the 16-bit VLAN hash table register is used for group address filtering based on the VLAN
tag. For hash filtering, the content of the 16-bit VLAN tag or 12-bit VLAN ID (based on MAC_VLAN_Tag_Ctrl[ETV] ) in the
incoming packet is passed through the CRC logic. The upper four bits of the calculated CRC value are used to index the
contents of the VLAN hash table. For example, hash value of 1000b selects bit 8 of the VLAN hash table.
Perform these steps to calculate the hash value of the destination address:
1. Calculate the 32-bit CRC for DA (see section 3.2.8 of IEEE 802.3 for steps to calculate CRC32).
2. Perform bit-wise reversal for the value obtained in step 1.
3. Take the upper 4 bits from the value obtained in step 2.
If this register is configured to be double-synchronized with the (G)MII clock domain, the synchronization is triggered only when
bits [15:8] (in Little-Endian mode) or bits [7:0] (in Big-Endian mode) of the register are written to.
If double-synchronization is enabled, consecutive writes to this register must be performed after at least four clock cycles in the
destination clock domain.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
VLHT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2700*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-16 Reserved
—
15-0 VLAN Hash Table
VLHT Contains the 16-bit VLAN hash table.

#### 72.17.16 MAC VLAN Inclusion Or Replacement (MAC_VLAN_Incl)

Offset
Register Offset
MAC_VLAN_Incl 60h
Function
Contains the VLAN tag for insertion or replacement in the transmit packets and the VLAN tag insertion controls.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R BUSY
RDWR Reserved ADDR Reserved CBTI VLTI CSVL VLP VLC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
VLT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Busy
BUSY Indicates the status of the read or write operation of indirect access to the queue- or channel-specific VLAN
inclusion register.
The write operation to a register completes when this field becomes 0. For a read operation, the read data
is valid when the field becomes 0.
The application must make sure that this field becomes 0 before attempting subsequent accesses to
this register.
Table continues on the next page...

---

*Page 2701*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Busy status not detected
1b - Busy status detected
30 Read Write Control
RDWR Controls the read or write operation for indirectly accessing the queue- or channel-specific VLAN
inclusion register.
• If this field is 1, it indicates the write operation.
• If this field is 0, it indicates the read operation.
The value of this field is not impacted when the CBTI field becomes 0.
0b - Read operation of indirect access
1b - Write operation of indirect access
29-25 Reserved
—
24 Address
ADDR Selects one of the queue- or channel-specific VLAN inclusion registers for read or write access.
The value of this field is not impacted when the CBTI field is 0.
23-22 Reserved
—
21 Channel-Based Tag Insertion
CBTI Indicates the status of channel-based tag insertion.
If this field is 1:
• The outer VLAN tag is inserted for all the packets that MAC transmits.
• The tag value is taken from the queue- or channel-specific VLAN tag register. Also, the VLTI, VLP,
VLC, and VLT fields of the register are ignored.
• A write operation to byte 3 of this register initiates the read or write access to the indirect register.
If this field is 0, the outer VLAN operation is based on the settings of the VLTI, VLP, VLC, and VLT fields of
this register.
0b - Disabled
1b - Enabled
20 VLAN Tag Input
VLTI Indicates the status of the VLAN tag input.
The value of this field being 1 indicates that the VLAN tag to be inserted or replaced in the transmit packet
must be taken from the transmit descriptor.
Table continues on the next page...

---

*Page 2702*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled
19 C-VLAN Or S-VLAN
CSVL Indicates whether C-VLAN or S-VLAN is inserted or replaced in the transmitted packets.
• If this field is 1, S-VLAN type (88A8h) is inserted or replaced in the 13th and 14th bytes of the
transmitted packets.
• If this field is 0, C-VLAN type (8100h) is inserted or replaced in the 13th and 14th bytes of the
transmitted packets.
0b - C-VLAN
1b - S-VLAN
18 VLAN Priority Control
VLP Indicates the status of VLAN priority control.
• If this field is 1, the control bits [17:16] are used for VLAN deletion, insertion, or replacement.
• If this field is 0, the mti_vlan_ctrl_i control input is used and bits [17:16] are ignored.
0b - Disabled
1b - Enabled
17-16 VLAN Tag Control
VLC Contains values for VLAN tag control in transmit packets.
MAC:
• Removes the VLAN type (bytes 13 and 14) and VLAN tag (bytes 15 and 16) of all the transmitted
packets with VLAN tags.
• Replaces VLT in bytes 15 and 16 of all the VLAN-type transmitted packets (bytes 13 and 14 are
8100h or 88a8h).
MAC inserts the following into the packet in the order shown:
1. Type value (8100h or 88A8h) into bytes 13 and 14
2. MAC_VLAN_Tag_Data[VLT] into bytes 15 and 16
This operation is performed on all the transmitted packets, irrespective of whether they already have a
VLAN tag.
NOTE
Changes to this field take effect only on the start of a packet. If you write to this field when a
packet is being transmitted, only the subsequent packets can use the updated value. That
is, the current packet does not use the updated value.
00b - No VLAN tag deletion, insertion, or replacement
01b - VLAN tag deletion
Table continues on the next page...

---

*Page 2703*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
10b - VLAN tag insertion
11b - VLAN tag replacement
15-0 VLAN Tag For Transmit Packets
VLT Contains the value of the VLAN tag to be inserted or replaced. This value must only be changed during the
initialization phase or when the transmit lines are inactive.
The following list describes the bits of this field:
• Bits [15:13]: User priority
• Bit 12: Canonical format indicator (CFI) or drop eligible indicator (DEI)
• Bits [11:0]: MAC_VLAN_Tag_Data[VID]

#### 72.17.17 MAC VLAN Inclusion 0 (MAC_VLAN_Incl0)

Offset
Register Offset
MAC_VLAN_Incl0 60h
Function
Contains the VLAN tag for insertion in the transmit packets from transmit queue 0. The register also contains the VLAN tag
insertion controls.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved CSVL Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
VLT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-20 Reserved
Table continues on the next page...

---

*Page 2704*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
—
19 C-VLAN or S-VLAN
CSVL When this field is 1, S-VLAN type (0x88A8) is inserted in the 13th and 14th bytes of transmitted packets.
When this field is reset, C-VLAN type (0x8100) is inserted in the 13th and 14th bytes of transmitted packets.
0b - C-VLAN type (0x8100) is inserted
1b - S-VLAN type (0x88A8) is inserted
18-16 Reserved
—
15-0 VLAN Tag for Transmit Packets
VLT Contains the value of the VLAN tag to be inserted.
The value of this field must only be changed when the transmit lines are inactive or during the
initialization phase.
The following list describes the bits of this field:
• Bits[15:13]: User Priority
• Bit 12: Canonical Format Indicator (CFI) or Drop Eligible Indicator (DEI)
• Bits[11:0]: VLAN Identifier (VID) field of VLAN tag

#### 72.17.18 MAC VLAN Inclusion 1 (MAC_VLAN_Incl1)

Offset
Register Offset
MAC_VLAN_Incl1 60h
Function
Contains the VLAN tag for insertion in the transmit packets from transmit queue 1. The register also contains the VLAN tag
insertion controls.

---

*Page 2705*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved CSVL Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
VLT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-20 Reserved
—
19 C-VLAN or S-VLAN
CSVL When this field is 1, S-VLAN type (0x88A8) is inserted in the 13th and 14th bytes of transmitted packets.
When this field is reset, C-VLAN type (0x8100) is inserted in the 13th and 14th bytes of transmitted packets.
0b - C-VLAN type (0x8100) is inserted
1b - S-VLAN type (0x88A8) is inserted
18-16 Reserved
—
15-0 VLAN Tag for Transmit Packets
VLT Contains the value of the VLAN tag to be inserted.
The value of this field must only be changed when the transmit lines are inactive or during the
initialization phase.
The following list describes the bits of this field:
• Bits[15:13]: User Priority
• Bit 12: Canonical Format Indicator (CFI) or Drop Eligible Indicator (DEI)
• Bits[11:0]: VLAN Identifier (VID) field of VLAN tag

#### 72.17.19 MAC VLAN Inclusion 2 (MAC_VLAN_Incl2)

Offset
Register Offset
MAC_VLAN_Incl2 60h

---

*Page 2706*

Ethernet Media Access Controller (EMAC)
Function
Contains the VLAN tag for insertion in the transmit packets from transmit queue 2. The register also contains the VLAN tag
insertion controls.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved CSVL Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
VLT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-20 Reserved
—
19 C-VLAN or S-VLAN
CSVL When this field is 1, S-VLAN type (0x88A8) is inserted in the 13th and 14th bytes of transmitted packets.
When this field is reset, C-VLAN type (0x8100) is inserted in the 13th and 14th bytes of transmitted packets.
0b - C-VLAN type (0x8100) is inserted
1b - S-VLAN type (0x88A8) is inserted
18-16 Reserved
—
15-0 VLAN Tag for Transmit Packets
VLT Contains the value of the VLAN tag to be inserted.
The value of this field must only be changed when the transmit lines are inactive or during the
initialization phase.
The following list describes the bits of this field:
• Bits[15:13]: User Priority
• Bit 12: Canonical Format Indicator (CFI) or Drop Eligible Indicator (DEI)
• Bits[11:0]: VLAN Identifier (VID) field of VLAN tag

---

*Page 2707*

Ethernet Media Access Controller (EMAC)

#### 72.17.20 MAC VLAN Inclusion 3 (MAC_VLAN_Incl3)

Offset
Register Offset
MAC_VLAN_Incl3 60h
Function
Contains the VLAN tag for insertion in the transmit packets from transmit queue 3. The register also contains the VLAN tag
insertion controls.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved CSVL Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
VLT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-20 Reserved
—
19 C-VLAN or S-VLAN
CSVL When this field is 1, S-VLAN type (0x88A8) is inserted in the 13th and 14th bytes of transmitted packets.
When this field is reset, C-VLAN type (0x8100) is inserted in the 13th and 14th bytes of transmitted packets.
0b - C-VLAN type (0x8100) is inserted
1b - S-VLAN type (0x88A8) is inserted
18-16 Reserved
—
15-0 VLAN Tag for Transmit Packets
VLT Contains the value of the VLAN tag to be inserted.
The value of this field must only be changed when the transmit lines are inactive or during the
initialization phase.
The following list describes the bits of this field:
Table continues on the next page...

---

*Page 2708*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• Bits[15:13]: User Priority
• Bit 12: Canonical Format Indicator (CFI) or Drop Eligible Indicator (DEI)
• Bits[11:0]: VLAN Identifier (VID) field of VLAN tag

#### 72.17.21 MAC VLAN Inclusion 4 (MAC_VLAN_Incl4)

Offset
Register Offset
MAC_VLAN_Incl4 60h
Function
Contains the VLAN tag for insertion in the transmit packets from transmit queue 4. The register also contains the VLAN tag
insertion controls.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved CSVL Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
VLT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-20 Reserved
—
19 C-VLAN or S-VLAN
CSVL When this field is 1, S-VLAN type (0x88A8) is inserted in the 13th and 14th bytes of transmitted packets.
When this field is reset, C-VLAN type (0x8100) is inserted in the 13th and 14th bytes of transmitted packets.
0b - C-VLAN type (0x8100) is inserted
1b - S-VLAN type (0x88A8) is inserted
Table continues on the next page...

---

*Page 2709*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
18-16 Reserved
—
15-0 VLAN Tag for Transmit Packets
VLT Contains the value of the VLAN tag to be inserted.
The value of this field must only be changed when the transmit lines are inactive or during the
initialization phase.
The following list describes the bits of this field:
• Bits[15:13]: User Priority
• Bit 12: Canonical Format Indicator (CFI) or Drop Eligible Indicator (DEI)
• Bits[11:0]: VLAN Identifier (VID) field of VLAN tag

#### 72.17.22 MAC VLAN Inclusion 5 (MAC_VLAN_Incl5)

Offset
Register Offset
MAC_VLAN_Incl5 60h
Function
Contains the VLAN tag for insertion in the transmit packets from transmit queue 5. The register also contains the VLAN tag
insertion controls.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved CSVL Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
VLT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2710*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-20 Reserved
—
19 C-VLAN or S-VLAN
CSVL When this field is 1, S-VLAN type (0x88A8) is inserted in the 13th and 14th bytes of transmitted packets.
When this field is reset, C-VLAN type (0x8100) is inserted in the 13th and 14th bytes of transmitted packets.
0b - C-VLAN type (0x8100) is inserted
1b - S-VLAN type (0x88A8) is inserted
18-16 Reserved
—
15-0 VLAN Tag for Transmit Packets
VLT Contains the value of the VLAN tag to be inserted.
The value of this field must only be changed when the transmit lines are inactive or during the
initialization phase.
The following list describes the bits of this field:
• Bits[15:13]: User Priority
• Bit 12: Canonical Format Indicator (CFI) or Drop Eligible Indicator (DEI)
• Bits[11:0]: VLAN Identifier (VID) field of VLAN tag

#### 72.17.23 MAC VLAN Inclusion 6 (MAC_VLAN_Incl6)

Offset
Register Offset
MAC_VLAN_Incl6 60h
Function
Contains the VLAN tag for insertion in the transmit packets from transmit queue 6. The register also contains the VLAN tag
insertion controls.

---

*Page 2711*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved CSVL Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
VLT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-20 Reserved
—
19 C-VLAN or S-VLAN
CSVL When this field is 1, S-VLAN type (0x88A8) is inserted in the 13th and 14th bytes of transmitted packets.
When this field is reset, C-VLAN type (0x8100) is inserted in the 13th and 14th bytes of transmitted packets.
0b - C-VLAN type (0x8100) is inserted
1b - S-VLAN type (0x88A8) is inserted
18-16 Reserved
—
15-0 VLAN Tag for Transmit Packets
VLT Contains the value of the VLAN tag to be inserted.
The value of this field must only be changed when the transmit lines are inactive or during the
initialization phase.
The following list describes the bits of this field:
• Bits[15:13]: User Priority
• Bit 12: Canonical Format Indicator (CFI) or Drop Eligible Indicator (DEI)
• Bits[11:0]: VLAN Identifier (VID) field of VLAN tag

#### 72.17.24 MAC VLAN Inclusion 7 (MAC_VLAN_Incl7)

Offset
Register Offset
MAC_VLAN_Incl7 60h

---

*Page 2712*

Ethernet Media Access Controller (EMAC)
Function
Contains the VLAN tag for insertion in the transmit packets from transmit queue 7. The register also contains the VLAN tag
insertion controls.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved CSVL Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
VLT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-20 Reserved
—
19 C-VLAN or S-VLAN
CSVL When this field is 1, S-VLAN type (0x88A8) is inserted in the 13th and 14th bytes of transmitted packets.
When this field is reset, C-VLAN type (0x8100) is inserted in the 13th and 14th bytes of transmitted packets.
0b - C-VLAN type (0x8100) is inserted
1b - S-VLAN type (0x88A8) is inserted
18-16 Reserved
—
15-0 VLAN Tag for Transmit Packets
VLT Contains the value of the VLAN tag to be inserted.
The value of this field must only be changed when the transmit lines are inactive or during the
initialization phase.
The following list describes the bits of this field:
• Bits[15:13]: User Priority
• Bit 12: Canonical Format Indicator (CFI) or Drop Eligible Indicator (DEI)
• Bits[11:0]: VLAN Identifier (VID) field of VLAN tag

---

*Page 2713*

Ethernet Media Access Controller (EMAC)

#### 72.17.25 Inner VLAN Tag Inclusion Or Replacement (MAC_Inner_VLAN_Incl)

Offset
Register Offset
MAC_Inner_VLAN_Incl 64h
Function
Contains:
• The inner VLAN tag to be inserted or replaced in the transmit packet
• The inner VLAN tag insertion controls
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved VLTI CSVL VLP VLC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
VLT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-21 Reserved
—
20 VLAN Tag Input
VLTI Indicates the status of VLAN tag input.
If this field is 1, it indicates that the VLAN tag to be inserted or replaced in the transmit packet must be taken
from the transmit descriptor.
0b - Disabled
1b - Enabled
19 C-VLAN Or S-VLAN
CSVL Controls the insertion or replacement type in the 17th and 18th bytes of the transmitted packets.
0b - C-VLAN type (8100h)
1b - S-VLAN type (88A8h)
Table continues on the next page...

---

*Page 2714*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
18 VLAN Priority Control
VLP Indicates the status of VLAN priority control.
• If this field is 1, it's used for VLAN deletion, insertion, or replacement.
• If this field is 0, the mti_vlan_ctrl_i control input is used and MAC_VLAN_Incl[VLC] is ignored.
0b - Disabled
1b - Enabled
17-16 VLAN Tag Control in Transmit Packets
VLC Indicates the value of VLAN tag control in transmit packets.
The following list specifies these values:
• 2'b00: No VLAN tag deletion, insertion, or replacement
• 2'b01: VLAN tag deletion
• 2'b10: VLAN tag insertion
• 2'b11: VLAN tag replacement
MAC:
• Removes the VLAN type (bytes 17 and 18) and VLAN tag (bytes 19 and 20) of all the transmitted
packets with VLAN tags
• Inserts VLT in bytes 19 and 20 of the packet after inserting the Type value (8100h or 88a8h) in bytes
17 and 18. This operation is performed on all the transmitted packets, irrespective of whether they
already have a VLAN tag.
• Replaces VLT in bytes 19 and 20 for all the VLAN-type transmitted packets (bytes 17 and 18 are
8100h or 88a8h).
NOTE
Changes to this field take effect only on the start of a packet. If you write to the field when a
packet is being transmitted, only the subsequent packets can use the updated value. That
is, the current packet does not use the updated value.
00b - No VLAN tag deletion, insertion, or replacement
01b - VLAN tag deletion
10b - VLAN tag insertion
11b - VLAN tag replacement
15-0 VLAN Tag For Transmit Packets
VLT Contains the value of the VLAN tag to be inserted or replaced. The value must only be changed when the
transmit lines are inactive or during the initialization phase.
The following list describes the bits of this field:
• Bits [15:13]: User priority
Table continues on the next page...

---

*Page 2715*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• Bit 12: CFI or DEI
• Bits [11:0]: MAC_VLAN_Tag_Data[VID]

#### 72.17.26 MAC Q0 Tx Flow Control (MAC_Q0_Tx_Flow_Ctrl)

Offset
Register Offset
MAC_Q0_Tx_Flow_Ctrl 70h
Function
Controls the generation and reception of the control (pause command) packets by MAC's flow control module. A write to the
register with the Busy field = 1 triggers the flow control block to generate a pause packet. The 802.3x specification indicates the
way the fields of the control packet are selected, and the pause time value from this register is used in the PT field of the control
packet. The Busy field remains 1 until the control packet is transferred onto the cable. You must make sure that the Busy field is
0 before writing to this register.
If the value of the PFCE field in the MAC_Rx_Flow_Ctrl register is 1, this register controls the generation of priority flow control
(PFC) frames with priorities mapped according to MAC_RxQ_Ctrl2[PSRQ0] .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
PT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
FCB_
Reserved DZPQ PLT Reserved TFE
BPA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-16 Pause Time
PT Holds the value to be used in this field in the transmit control packet. If the bits of this field are configured to
be double-synchronized with the (G)MII clock domain, consecutive writes to this register must be performed
only after at least four clock cycles in the destination clock domain.
Table continues on the next page...

---

*Page 2716*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
15-8 Reserved
—
7 Disable Zero-Quanta Pause
DZPQ Enables or disables zero-quanta pause packet generation.
• If this field is 1, it disables the automatic generation of the zero-quanta pause packets that the
flow-control signal from the FIFO layer (MTL or external sideband flow control signal sbd_flowctrl_i or
mti_flowctrl_i) deasserts.
• If this field is 0, normal operations with automatic zero-quanta pause packet generation are enabled.
0b - Enabled
1b - Disabled
6-4 Pause Low Threshold
PLT Configures the threshold of the pause timer at which the input flow control signal mti_flowctrl_i (or
sbd_flowctrl_i) is checked for automatic retransmission of the pause packet.
The threshold values must always be less than the pause time configured in bits [31:16]. For example, if the
PT field = 100H (256 slot times), and the PLT field = 001, a second pause packet automatically transmits if
the mti_flowctrl_i signal is asserted at 228 (256-28) slot times after the first pause packet transmits.
This list provides the threshold values for different values:
• The slot time is defined as the time taken to transmit 512 bits (64 bytes) on GMII or MII.
• This (approximate) computation is based on the packet size (64, 1518, 2000, 9018, 16384, or
32768) + two pause packet sizes + IPG in slot times.
000b - Pause time minus 4 slot times (PT is 4 slot times)
001b - Pause time minus 28 slot times (PT is 28 slot times)
010b - Pause time minus 36 slot times (PT is 36 slot times)
011b - Pause time minus 144 slot times (PT is 144 slot times)
100b - Pause time minus 256 slot times (PT is 256 slot times)
101b - Pause time minus 512 slot times (PT is 512 slot times)
110b - Reserved
3-2 Reserved
—
1 Transmit Flow Control Enable
TFE Indicates whether transmit flow control is enabled or disabled.
• Full-Duplex mode: If this field is 1, MAC enables the flow control operation to the transmit pause
packets. When the field becomes 0, MAC's flow control operation disables and MAC does not
transmit any pause packets.
Table continues on the next page...

---

*Page 2717*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• Half-Duplex mode: If this field is 1, MAC enables the backpressure operation. When the field
becomes 0, the backpressure operation disables.
0b - Disabled
1b - Enabled
0 Flow Control Busy Or Backpressure Activate
FCB_BPA Initiates if a pause packet is in Full-Duplex mode and activates the backpressure function in Half-Duplex
mode if the TFE field is 1.
• Full-Duplex mode: In this mode, FCB_BPA must be read as 1'b0 before writing to this register. To
initiate a pause packet, the application must set this field to 1'b1. During control packet transfer, the
field's value continues to be 1 to indicate that a packet transmission is in progress. When pause
packet transmission is complete, MAC resets this field to 1'b0. You must not write to this register
until this field becomes 0.
• Half-Duplex mode: If FCB_BPA = 1 and TFE = 1 too, MAC asserts backpressure in this mode.
During the backpressure function, when MAC receives a new packet, the transmitter starts
sending a JAM pattern resulting in a collision. This control register field is logically ORed with the
mti_flowctrl_i input signal for the backpressure function. When you configure MAC using Full-Duplex
mode, this field automatically becomes 0.
Access restrictions apply to this field: writing 1 sets it, writing 0 has no effect, and the field is self-clearing.
0b - Flow control busy or backpressure activate is disabled
1b - Flow control busy or backpressure activate is enabled

#### 72.17.27 MAC Receive Flow Control (MAC_Rx_Flow_Ctrl)

Offset
Register Offset
MAC_Rx_Flow_Ctrl 90h
Function
Controls the pausing of MAC transmit based on the received pause packet.

---

*Page 2718*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserv
Reserved Reserved UP RFE
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-9 Reserved
—
8 Reserved
—
7-2 Reserved
—
1 Unicast Pause Packet Detect
UP Indicates whether the unicast pause packet is enabled or disabled.
A pause packet is processed when it has the unique multicast address specified in IEEE 802.3.
• If this field is 1, MAC can detect pause packets with a unicast address of the station. This address
must comply with the specifications in MAC Address 0 High (MAC_Address0_High) and MAC
Address 0 Low (MAC_Address0_Low) .
• If this field is 0, MAC only detects pause packets with a unique multicast address.
NOTE
MAC does not process a pause packet if the multicast address is different from the
unique multicast address. This applies to the received PFC packet if PFC is enabled.
The unique multicast address (01_80_C2_00_00_01h) complies with the IEEE 802.1
Qbb-2011 specifications.
0b - Disabled
1b - Enabled
0 Receive Flow Control Enable
RFE Indicates whether the receive flow control is enabled or disabled.
• If this field is 1 and MAC is operating in Full-Duplex mode, MAC decodes the received pause packet
and disables its transmitter for a specified (pause) time.
Table continues on the next page...

---

*Page 2719*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• If this field is 0 or if MAC is operating in Half-Duplex mode, the decode function of the pause packet
disables.
When PFC is enabled, the flow control for PFC packets is enabled too. MAC decodes the received PFC
packet and disables the transmit queue, with matching priorities, for a duration of the received pause time.
0b - Disabled
1b - Enabled

#### 72.17.28 MAC RxQ Control 4 (MAC_RxQ_Ctrl4)

Offset
Register Offset
MAC_RxQ_Ctrl4 94h
Function
Controls the routing of unicast and multicast packets that fail the destination or source address filter to the receive queues.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
VFFQ
Reserved VFFQ
E
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
MFFQ UFFQ
Reserved MFFQ Reserved UFFQ
E E
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-18 Reserved
—
17 VLAN Tag Filter Fail Packets Queue
VFFQ Holds the receive queue number to which the tagged packets failing the destination or source address filter
(and UFFQE and MFFQE not being 1) or failing the VLAN tag filter must be routed to. This field is valid only
when the VFFQE field = 1.
Table continues on the next page...

---

*Page 2720*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
16 VLAN Tag Filter Fail Packets Queuing Enable
VFFQE Indicates whether VLAN tag filter fail packet queuing is enabled or disabled.
• If this field is 1, the tagged packets that fail the destination or source address filter or fail the VLAN
tag filter are routed to the receive queue number programmed using the VFFQ field.
• If this field is 0, the tagged packets that fail the destination or source address filter or fail the
VLAN tag filter are routed based on the other routing options. This field is valid only when
MAC_Packet_Filter[RA] = 1.
0b - Disabled
1b - Enabled
15-10 Reserved
—
9 Multicast Address Filter Fail Packets Queue
MFFQ Holds the receive queue number to which the multicast packets failing the destination or source address
filter are routed to. This field is valid only when the MFFQE field = 1.
8 Multicast Address Filter Fail Packets Queuing Enable
MFFQE Indicates whether multicast address filter fail packet queuing is enabled or disabled.
• If this field is 1, the multicast packets that fail the destination or source address filter are routed to
the receive queue number programmed using the MFFQ field.
• If this field is 0, the multicast packets that fail the destination or source address filter are routed
based on the other routing options.
This field is valid only when MAC_Packet_Filter[RA] = 1.
0b - Disabled
1b - Enabled
7-2 Reserved
—
1 Unicast Address Filter Fail Packets Queue
UFFQ Holds the receive queue number to which the unicast packets failing the destination or source address filter
are routed to. This field is valid only when the UFFQE field = 1.
0 Unicast Address Filter Fail Packets Queuing Enable
UFFQE Indicates whether unicast address filter fail packet queuing is enabled or disabled.
• When the value of this field is 1, the unicast packets that fail the destination or source address filter
are routed to the receive queue number programmed using the UFFQ field.
• If this field is 0, the unicast packets that fail the destination or source address filter are routed based
on the other routing options.
Table continues on the next page...

---

*Page 2721*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
This field is valid only when MAC_Packet_Filter[RA] = 1.
0b - Disabled
1b - Enabled

#### 72.17.29 MAC RxQ Control 0 (MAC_RxQ_Ctrl0)

Offset
Register Offset
MAC_RxQ_Ctrl0 A0h
Function
Controls queue management in the MAC receiver.
NOTE
In multiple receive queues configuration, all the queues are disabled by default. Enable the receive queue by
programming the corresponding field in this register.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved Reserved Reserved Reserved Reserved Reserved RXQ1EN RXQ0EN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-16 Reserved
—
15-14 Reserved
—
Table continues on the next page...

---

*Page 2722*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
13-12 Reserved
—
11-10 Reserved
—
9-8 Reserved
—
7-6 Reserved
—
5-4 Reserved
—
3-2 Receive Queue 1 Enable
RXQ1EN Performs in a manner similar to the RXQ0EN field and indicates if receive queue 1 is enabled for AV or DCB.
00b - Queue not enabled
01b - Queue enabled for AV
10b - Queue enabled for DCB/generic
11b - Reserved
1-0 Receive Queue 0 Enable
RXQ0EN Indicates whether receive queue 0 is enabled for AV or DCB.
00b - Queue not enabled
01b - Queue enabled for AV
10b - Queue enabled for DCB/generic
11b - Reserved

#### 72.17.30 Receive Queue Control 1 (MAC_RxQ_Ctrl1)

Offset
Register Offset
MAC_RxQ_Ctrl1 A4h
Function
Controls the routing of multicast, broadcast, AV, DCB, and untagged packets to the receive queues.

---

*Page 2723*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
TACP MCBC Reserv
Reserved FPRQ TPQC MCBCQ
QE QEN ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserv Reserv Reserv Reserv
UPQ Reserved PTPQ AVCPQ
ed ed ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-27 Reserved
—
26-24 Frame Preemption Residue Queue
FPRQ Holds the receive queue number to which the residual preemption frames must be forwarded.
Preemption frames that are tagged and pass the SA, DA, or VLAN filtering are routed based on the settings
of the PSRQ fields in MAC RxQ Control 2 (MAC_RxQ_Ctrl2) . All other frames are treated as residual frames
and are routed to the queue number mentioned in this field. Queue 0 is used as the default queue for express
frames, so you cannot write 0 to this field.
23-22 Tagged PTP Over Ethernet Packets Queuing Control
TPQC Controls the routing of the VLAN-tagged PTPoE packets.
These are the allowed programmable options:
• 2'b00: VLAN-tagged PTPoE packets are routed as generic VLAN-tagged packets (based on the
PSRQ fields in MAC RxQ Control 2 (MAC_RxQ_Ctrl2) for only non-AV enabled receive queues).
• 2'b01: VLAN-tagged PTPoE packets are routed to the receive queue that the PTPQ field of this
register specifies (this Rx queue can be enabled for AV or non-AV traffic).
• 2'b10: VLAN-tagged PTPoE packets are routed to only AV-enabled receive queues based on the
settings of the PSRQ fields.
21 Tagged AV Control Packets Queuing Enable
TACPQE Indicates the status of tagged AV control packet queuing.
• If this field is 1, MAC routes the received tagged AV control packets to the receive queue that the
AVCPQ field specifies.
• If this field is 0, MAC routes the received tagged AV control packets based on the tag priority
matching the PSRQ fields in MAC RxQ Control 2 (MAC_RxQ_Ctrl2) .
0b - Disabled
1b - Enabled
Table continues on the next page...

---

*Page 2724*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
20 Multicast And Broadcast Queue Enable
MCBCQEN Specifies whether multicast or broadcast packet routing to the receive queue is enabled or disabled. The
multicast or broadcast packets must be routed to the receive queue specified in the MCBCQ field.
0b - Disabled
1b - Enabled
19 Reserved
—
18-16 Multicast And Broadcast Queue
MCBCQ Specifies the receive queue onto which multicast or broadcast packets are routed. Any receive queue
enabled for Generic, DCB, or AV traffic can be used to route the multicast or broadcast packets.
000b - Receive queue 0
001b - Receive queue 1
010b - Receive queue 2
011b - Receive queue 3
100b - Receive queue 4
101b - Receive queue 5
110b - Receive queue 6
111b - Receive queue 7
15 Reserved
—
14-12 Untagged Packet Queue
UPQ Indicates the receive queue to which the untagged packets need to be routed. Any receive queue enabled
for generic, DCB, or AV traffic can be used to route the untagged packets.
000b - Receive queue 0
001b - Receive queue 1
010b - Receive queue 2
011b - Receive queue 3
100b - Receive queue 4
101b - Receive queue 5
110b - Receive queue 6
111b - Receive queue 7
11 Reserved
—
Table continues on the next page...

---

*Page 2725*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
10-8 Reserved
—
7 Reserved
—
6-4 PTP Packets Queue
PTPQ Specifies the receive queue on which the PTP packets sent over the Ethernet payload (not over IPv4 or
IPv6) are routed.
When MAC_Timestamp_Control[AV8021ASMEN] = 1, only untagged PTP over Ethernet packets are
routed to a receive queue. If this field is not 1, then based on the settings of the TPQC field, both tagged and
untagged PTPoE packets can be routed to this receive queue.
000b - Receive queue 0
001b - Receive queue 1
010b - Receive queue 2
011b - Receive queue 3
100b - Receive queue 4
101b - Receive queue 5
110b - Receive queue 6
111b - Receive queue 7
3 Reserved
—
2-0 AV Untagged Control Packets Queue
AVCPQ Specifies the receive queue on which the received AV tagged and untagged control packets are routed.
The AV tagged (when the TACPQE field = 1) and untagged control packets are routed to the receive queue
that this field specifies.
000b - Receive queue 0
001b - Receive queue 1
010b - Receive queue 2
011b - Receive queue 3
100b - Receive queue 4
101b - Receive queue 5
110b - Receive queue 6
111b - Receive queue 7

---

*Page 2726*

Ethernet Media Access Controller (EMAC)

#### 72.17.31 MAC RxQ Control 2 (MAC_RxQ_Ctrl2)

Offset
Register Offset
MAC_RxQ_Ctrl2 A8h
Function
Controls the routing of tagged packets based on the settings of MAC_Ext_Configuration[USP] for packets received in receive
queues 0-3.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PSRQ1 PSRQ0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 Reserved
—
23-16 Reserved
—
15-8 Priorities Selected In Receive Queue 1
PSRQ1 Determines the priorities assigned to receive queue 1. All the packets with priorities that match the values
defined in this field are routed to receive queue 1.
For example, if PSRQ1[4] = 1, packets with MAC_Ext_Configuration[USP] equal to 4 are routed to receive
queue 1. You must ensure that the content of this field is mutually exclusive to the PSRQ fields for other
queues. This means that the same priority is not mapped to multiple receive queues.
7-0 Priorities Selected In Receive Queue 0
PSRQ0 Determines the priorities assigned to receive queue 0. All the packets with priorities that match the values
defined in this field are routed to receive queue 0.
For example, if PSRQ0[5] = 1, packets with MAC_Ext_Configuration[USP] equal to 5 are routed to receive
queue 0. You must ensure that the content of this field is mutually exclusive to the PSRQ fields for other
queues. This means that the same priority is not mapped to multiple receive queues.

---

*Page 2727*

Ethernet Media Access Controller (EMAC)

#### 72.17.32 MAC Interrupt Status (MAC_Interrupt_Status)

Offset
Register Offset
MAC_Interrupt_Status B0h
Function
Contains the status of interrupts.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
MDIOI
R MFRIS MFTIS FPEIS
Reserv
S
Reserved
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
RXST TXST MMCT MMCR MMCI
R TSIS PHYIS
Reserv Reserv Reserv Reserv Reserv Reserv Reserv
SIS SIS XIS XIS S
Reserved
ed ed ed ed ed ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-21 Reserved
—
20 MMC FPE Receive Interrupt Status
MFRIS Indicates the status of MMC FPE receive interrupt.
This field becomes 1 when any of the fields in MMC Receive Packet Assembly Error Counter Interrupt
Status (MMC_FPE_Rx_Interrupt) is 1. The field becomes 0 when all the other fields in MMC Receive Packet
Assembly Error Counter Interrupt Status (MMC_FPE_Rx_Interrupt) become 0 too.
0b - Inactive
1b - Active
19 MMC FPE Transmit Interrupt Status
MFTIS Indicates the status of MMC FPE transmit interrupt.
This field becomes 1 when an interrupt generates in MMC Transmit FPE Fragment Counter Interrupt Status
(MMC_FPE_Tx_Interrupt) . The field becomes 0 when all the other fields in this register become 0 too.
This field is valid only when you select the Enable MAC Management Counters (MMC) option along with
FPE support.
Table continues on the next page...

---

*Page 2728*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Inactive
1b - Active
18 MDIO Interrupt Status
MDIOIS Indicates the status of an interrupt event after the completion of an MDIO operation.
To reset this field, you must read this field or write 1 to it when MAC_CSR_SW_Ctrl[RCWE] = 1.
Access restrictions apply to this field. It clears on a read or write of 1 when MAC_CSR_SW_Ctrl[RCWE] =
1. The field automatically becomes 1 on an internal event.
0b - Inactive
1b - Active
17 Frame Preemption Interrupt Status
FPEIS Indicates the status of an interrupt event during the frame preemption operation (the value of RVER, RRSP,
TVER, and TRSP fields of MAC FPE Control STS (MAC_FPE_CTRL_STS) = 1).
To reset this field, you must clear the event in MAC FPE Control STS (MAC_FPE_CTRL_STS) that caused
the interrupt.
0b - Inactive
1b - Active
16 Reserved
—
15 Reserved
—
14 Receive Status Interrupt
RXSTSIS Indicates the status of received packets.
This field becomes 1 when MAC_Rx_Tx_Status[RWT] = 1. The field becomes 0 when you read the
corresponding interrupt source field in MAC Rx Transmit Status (MAC_Rx_Tx_Status) , or if you write to this
interrupt source field when MAC_CSR_SW_Ctrl[RCWE] = 1.
The field becomes 0 in either of these circumstances:
• You read the corresponding interrupt-source field in MAC Rx Transmit Status (MAC_Rx_Tx_Status) .
• You write 1 to the corresponding interrupt-source field in MAC Rx Transmit Status
(MAC_Rx_Tx_Status) when MAC_CSR_SW_Ctrl[RCWE] = 1.
0b - Inactive
1b - Active
13 Transmit Status Interrupt
TXSTSIS Indicates the status of transmitted packets.
Table continues on the next page...

---

*Page 2729*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
This field becomes 1 when any of the following fields is 1 in MAC Rx Transmit Status (MAC_Rx_Tx_Status) :
• Excessive collision (EXCOL)
• Late collision (LCOL)
• Excessive deferral (EXDEF)
• Loss of carrier (LCARR)
• No carrier (NCARR)
• Jabber timeout (TJT)
This field becomes 0 in either of these circumstances:
• You read the corresponding interrupt-source field in MAC Rx Transmit Status (MAC_Rx_Tx_Status) .
• You write 1 to the corresponding interrupt-source field in MAC Rx Transmit Status
(MAC_Rx_Tx_Status) when MAC_CSR_SW_Ctrl[RCWE] = 1.
0b - Inactive
1b - Active
12 Timestamp Interrupt Status
TSIS Indicates the status of timestamp interrupt.
If the timestamp feature is enabled, this field becomes 1 when any of the following conditions is true:
• The system time value is equal to or exceeds the value specified in MAC PPS1 Target Time
In Seconds (MAC_PPS1_Target_Time_Seconds) and MAC PPS1 Target Time In Nanoseconds
(MAC_PPS1_Target_Time_Nanoseconds) .
• There is an overflow in MAC System Time In Seconds (MAC_System_Time_Seconds) .
• The target time error occurred, which means, the programmed target time already elapsed.
In configurations other than EQOS_CORE, when the drop transmit status is enabled in MTL, this field
becomes 1 when the captured transmit timestamp is updated in MAC Transmit Timestamp Status
In Nanoseconds (MAC_Tx_Timestamp_Status_Nanoseconds) and MAC Transmit Timestamp Status In
Seconds (MAC_Tx_Timestamp_Status_Seconds) .
The field becomes 0 in either of these circumstances:
• You read the corresponding interrupt-source field in MAC Rx Transmit Status (MAC_Rx_Tx_Status) .
• You write 1 to the corresponding interrupt-source field in MAC Rx Transmit Status
(MAC_Rx_Tx_Status) when MAC_CSR_SW_Ctrl[RCWE] = 1.
0b - Inactive
1b - Active
11 Reserved
—
10 MMC Transmit Interrupt Status
MMCTXIS Indicates the status of MMC transmit interrupt.
Table continues on the next page...

---

*Page 2730*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
This field:
• Becomes 1 when any field in MMC Transmit Interrupt (MMC_Tx_Interrupt) becomes 1.
• Becomes 0 when all the fields in MMC Transmit Interrupt (MMC_Tx_Interrupt) return to 0.
This field is valid only when you select the Enable MAC Management Counters (MMC) option.
0b - Inactive
1b - Active
9 MMC Receive Interrupt Status
MMCRXIS Indicates the status of MMC receive interrupt.
This field:
• Becomes 1 when any field in MMC Receive Interrupt (MMC_Rx_Interrupt) becomes 1.
• Becomes 0 when all the fields in MMC Receive Interrupt (MMC_Rx_Interrupt) return to 0.
This field is valid only when you select the Enable MAC Management Counters (MMC) option.
0b - Inactive
1b - Active
8 MMC Interrupt Status
MMCIS Indicates the status of MMC interrupt.
This field becomes 1 when MMCRXIS = MMCTXIS = 1, and the field becomes 0 only when MMCRXIS =
MMCTXIS = 0.
MMCIS is valid only when you select the Enable MAC Management Counters (MMC) option.
0b - Inactive
1b - Active
7-6 Reserved
—
5 Reserved
—
4 Reserved
—
3 PHY Interrupt
PHYIS Indicates whether the PHY interrupt is detected.
This field becomes 1 when rising edge is detected on the phy_intr_i input signal. The field becomes 0 when
you read this register (or if you write 1 to this field when MAC_CSR_SW_Ctrl[RCWE] = 1).
0b - Not detected
Table continues on the next page...

---

*Page 2731*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
1b - Detected
2 Reserved
—
1 Reserved
—
0 Reserved
—

#### 72.17.33 MAC Interrupt Enable (MAC_Interrupt_Enable)

Offset
Register Offset
MAC_Interrupt_Enable B4h
Function
Contains masks for generating MAC interrupts.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
MDIOI Reserv
Reserved FPEIE
E ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserv RXST TXSTS Reserv Reserv Reserv Reserv Reserv
TSIE Reserved PHYIE
ed SIE IE ed ed ed ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-19 Reserved
—
18 MDIO Interrupt Enable
Table continues on the next page...

---

*Page 2732*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
MDIOIE Indicates whether the MDIO interrupt is enabled or disabled.
If this field is 1, it enables the assertion of the interrupt when MAC_Interrupt_Status[MDIOIS] = 1.
0b - Disabled
1b - Enabled
17 Frame Preemption Interrupt Enable
FPEIE Indicates whether the frame preemption interrupt is enabled or disabled.
If this field is 1, it enables the assertion of the interrupt when MAC_Interrupt_Status[FPEIS] = 1.
0b - Disabled
1b - Enabled
16 Reserved
—
15 Reserved
—
14 Receive Status Interrupt Enable
RXSTSIE Indicates whether the receive status interrupt is enabled or disabled.
If this field is 1, it enables the assertion of the interrupt signal because of the setting
of MAC_Interrupt_Status[RXSTSIS] .
0b - Disabled
1b - Enabled
13 Transmit Status Interrupt Enable
TXSTSIE Indicates whether the timestamp status interrupt is enabled or disabled.
If this field is 1, it enables the assertion of the interrupt signal because of the setting
of MAC_Interrupt_Status[TXSTSIS] .
0b - Disabled
1b - Enabled
12 Timestamp Interrupt Enable
TSIE Indicates whether the timestamp interrupt is enabled or disabled.
If this field is 1, it enables the assertion of the interrupt signal because of the
setting MAC_Interrupt_Status[TSIS] .
0b - Disabled
1b - Enabled
11-6 Reserved
Table continues on the next page...

---

*Page 2733*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
—
5 Reserved
—
4 Reserved
—
3 PHY Interrupt Enable
PHYIE Indicates whether the assertion of the interrupt signal is enabled or disabled.
If this field is 1, it enables the assertion of the interrupt signal because of the setting
of MAC_Interrupt_Status[PHYIS] .
0b - Disabled
1b - Enabled
2 Reserved
—
1 Reserved
—
0 Reserved
—

#### 72.17.34 MAC Rx Transmit Status (MAC_Rx_Tx_Status)

Offset
Register Offset
MAC_Rx_Tx_Status B8h
Function
Contains the receive and transmit error status.

---

*Page 2734*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
EXCO EXDE LCAR NCAR
R RWT LCOL TJT
L F R R
Reserved Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-9 Reserved
—
8 Receive Watchdog Timeout
RWT Indicates the status of receive watchdog.
This field becomes 1 when a packet with a length greater than 2,048 bytes is received (10,240 bytes when
Jumbo Packet mode is enabled) and MAC_Configuration[WD] = 0. RWT becomes 1 when a packet with
length greater than 16,383 bytes is received and MAC_Configuration[WD] = 1.
Access restrictions apply to RWT. It becomes 0 on read or write of 1 when MAC_CSR_SW_Ctrl[RCWE] =
1. RWT automatically becomes 1 on an internal event occurrence.
0b - No receive watchdog timed out
1b - Receive watchdog timed out
7-6 Reserved
—
5 Excessive Collisions
EXCOL Indicates the status of excessive collision.
If MTL_Operation_Mode[DTXSTS] = 1, this field, EXCOL, indicates that the transmission aborted after 16
successive collisions while attempting to transmit the current packet.
If MAC_Configuration[DR] = 1, EXCOL becomes 1 after the first collision and the packet transmission
is aborted.
Access restrictions apply to EXCOL. It becomes 0 on read or write of 1 when MAC_CSR_SW_Ctrl[RCWE]
= 1. EXCOL automatically becomes 1 on an internal event occurrence.
0b - No collision
1b - Excessive collision is sensed
4 Late Collision
Table continues on the next page...

---

*Page 2735*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
LCOL Indicates the status of late collision.
If MTL_Operation_Mode[DTXSTS] = 1, this field, LCOL, indicates that the packet transmission aborted
because a collision occurred after the collision window (64 bytes including preamble in MII mode; 512 bytes
including preamble and carrier extension in GMII mode).
This field is invalid if an underflow error occurs.
Access restrictions apply to LCOL. It becomes 0 on read or write of 1 when MAC_CSR_SW_Ctrl[RCWE] =
1. LCOL automatically becomes 1 on an internal event occurrence.
0b - No collision
1b - Late collision is sensed
3 Excessive Deferral
EXDEF Indicates whether the transmission ended because of excessive deferral.
If MTL_Operation_Mode[DTXSTS] = 1 and MAC_Configuration[DC] = 1 too, this field, EXDEF, indicates that
the transmission ended because of excessive deferral of over 24,288 bit times (155,680 in 1000/2500 Mbit/s
mode or when the jumbo packet is enabled).
Access restrictions apply to EXDEF. It becomes 0 on read or write of 1 when MAC_CSR_SW_Ctrl[RCWE]
= 1. EXDEF automatically becomes 1 on an internal event occurrence.
0b - No excessive deferral
1b - Excessive deferral
2 Loss of Carrier
LCARR Indicates the status of carrier signal.
If MTL_Operation_Mode[DTXSTS] = 1, this field, LCARR, indicates that the loss of carrier occurred during
packet transmission, which means, the PHY_CRS_I signal was inactive for one or more transmission clock
periods during packet transmission. The field is valid only for packets transmitted without collision.
Access restrictions apply to LCARR. It becomes 0 on read or write of 1 when MAC_CSR_SW_Ctrl[RCWE]
= 1. LCARR automatically becomes 1 on an internal event occurrence.
0b - Carrier is present
1b - Loss of carrier
1 No Carrier
NCARR Indicates whether the carrier signal is present from the PHY at the end of preamble transmission.
If MTL_Operation_Mode[DTXSTS] = 1, NCARR indicates that the carrier signal from the PHY is absent at
the end of preamble transmission.
Access restrictions apply to NCARR. It becomes 0 after either of these actions:
• You read NCARR.
• You write 1 to NCARR when MAC_CSR_SW_Ctrl[RCWE] = 1.
NCARR automatically becomes 1 after an internal event occurs.
Table continues on the next page...

---

*Page 2736*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Present
1b - Absent
0 Transmit Jabber Timeout
TJT Indicates whether a transmit-jabber timer timeout occurred. The timeout occurs after any of these events:
• MAC_Configuration[JD] = 0 and the normal packet size exceeds 2,048 bytes.
• MAC_Configuration[JD] = 0 and the jumbo packet size exceeds 10240 bytes.
• MAC_Configuration[JD] = 1 and the normal packet size exceeds 16383 bytes.
Access restrictions apply to TJT. It becomes 0 after either of these actions:
• You read TJT.
• You write 1 to TJT when MAC_CSR_SW_Ctrl[RCWE] = 1.
TJT automatically becomes 1 after an internal event occurs.
0b - No transmit jabber timeout occurred
1b - Transmit jabber timeout occurred

#### 72.17.35 MAC Version (MAC_Version)

Offset
Register Offset
MAC_Version 110h
Function
Identifies the version of the module.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R CFGVER IPVER
W
Reset 0 0 0 1 0 0 0 0 0 1 0 1 0 0 0 1

---

*Page 2737*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-16 Reserved
—
IP Configuration Version
15-8
CFGVER
IP Version
7-0
IPVER

#### 72.17.36 MAC Debug (MAC_Debug)

Offset
Register Offset
MAC_Debug 114h
Function
Provides the debug status of the various MAC blocks.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
TPEST
R TFCSTS
S
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
RPES
R RFCFCSTS
TS
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-19 Reserved
—
18-17 MAC Transmit Packet Controller Status
Table continues on the next page...

---

*Page 2738*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
TFCSTS Indicates the state of the MAC transmit packet controller module.
00b - Idle state
01b - Waiting for one of these: status of the previous packet or IPG, or for the back-off period to
be over
10b - Generating and transmitting a pause control packet (in Full-Duplex mode)
11b - Transferring input packet for transmission
16 MAC GMII Or MII Transmit Protocol Engine Status
TPESTS Indicates whether MAC GMII or MII transmit protocol engine is detected.
If this field is 1, it indicates that the MAC GMII or MII transmit protocol engine is actively transmitting data
and is not in an idle state.
0b - Not detected
1b - Detected
15-3 Reserved
—
2-1 MAC Receive Packet Controller FIFO Status
RFCFCSTS Indicates the status of the small FIFO read and write controllers of the MAC receive packet
controller module.
If this field is 1, it indicates that the small FIFO read and write controllers of the MAC receive packet controller
module are in an active state.
00b - Inactive
01b - Active
0 Receive Protocol Engine Status
RPESTS Indicates whether MAC GMII or MII receive protocol engine is detected.
If this field is 1, it indicates that the MAC GMII or MII receive protocol engine is actively receiving data and
is not in an idle state.
0b - Not detected
1b - Detected

#### 72.17.37 MAC Hardware Feature 0 (MAC_HW_Feature0)

Offset
Register Offset
MAC_HW_Feature0 11Ch

---

*Page 2739*

Ethernet Media Access Controller (EMAC)
Function
Indicates the presence of the first set of EMAC optional features or functions. You can use this register to dynamically enable or
disable the programs related to these features.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
SAVLA MACA MACA RXCO
R ACTPHYSEL TSSTSSEL ADDMACADRSEL
Reserv Reserv
NI ... DR6 ... DR3 ... ESEL
ed ed
W
Reset 0 0 0 0 1 1 1 0 0 0 0 0 1 0 0 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
TXCO EEES ARPO MMCS MGKS RWKS SMAS VLHA PCSS HDSE GMIIS MIISE
R TSSEL
Reserv
ESEL EL FFS ... EL EL EL EL SH EL L EL L
Reserved
ed
W
Reset 0 1 0 1 0 0 0 1 0 0 1 1 0 1 0 1
Fields
Field Function
31 Reserved
—
30-28 Active PHY Feature
ACTPHYSEL Indicates the selected active PHY interface.
If you have multiple PHY interfaces (GMII, MII, RevMII, RGMII , RMII, RTBI, SGMII, SGMII , or TBI) in your
configuration, this field indicates the sampled value of the PHY_INTF_SEL_I signal during reset deassertion.
000b - GMII or MII
001b - RGMII
010b - SGMII
011b - TBI
100b - RMII
101b - RTBI
110b - SMII
111b - RevMII
27 SA or VLAN Insertion Feature
SAVLANINS Indicates whether the feature that enables SA or VLAN insertion on transmit is selected.
This field becomes 1 if you select the "enable SA and VLAN insertion on Tx" option.
0b - Not selected
1b - Selected
Table continues on the next page...

---

*Page 2740*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
26-25 Timestamp System Time Source Feature
TSSTSSEL Indicates the source of the timestamp system time.
This field becomes 1 if you select the "enable IEEE 1588 timestamp support" option.
00b - Internal
01b - External
10b - Both internal and external
11b - Reserved
24 MAC Addresses 64-127
MACADR64SE Indicates whether the feature that enables additional 64 MAC address registers (64-127) is selected.
L
This field becomes 1 if you enable the "MAC addresses (64-127) select" option.
0b - Not selected
1b - Selected
23 MAC Addresses 32-63
MACADR32SE Indicates whether the feature that enables additional 32 MAC address registers (32-63) is selected.
L
This field becomes 1 if you select the "MAC addresses 32-63 select" option.
0b - Not selected
1b - Selected
22-18 MAC Addresses 1-31
ADDMACADRS Indicates whether the feature that enables additional 1-31 MAC address registers is selected.
EL
This field becomes 1 if you select a non-zero value for enabling the "MAC addresses 1-31 select" option.
0_0000b - Not selected
0_0001b - Selected
17 Reserved
—
16 Receive Checksum Offload Feature
RXCOESEL Indicates whether the feature that enables receive TCP/IP checksum offload is selected.
This field becomes 1 if you select the "enable receive TCP/IP checksum check" option.
0b - Not selected
1b - Selected
15 Reserved
—
Table continues on the next page...

---

*Page 2741*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
14 Transmit Checksum Offload Feature
TXCOESEL Indicates whether the feature that enables transmit TCP/IP checksum insertion is selected.
This field becomes 1 if you select the "enable transmit TCP/IP checksum insertion" option.
0b - Not selected
1b - Selected
13 Energy Efficient Ethernet (EEE) Feature
EEESEL Indicates whether the feature that enables EEE is selected.
This field becomes 1 if you select the "enable EEE" option.
0b - Not selected
1b - Selected
12 IEEE 1588-2008 Timestamp Feature
TSSEL Indicates whether the option that enables IEEE 1588-2008 timestamp is selected.
This field becomes 1 if you select the "enable IEEE 1588 timestamp support" option.
0b - Not selected
1b - Selected
11-10 Reserved
—
9 ARP Offload Feature
ARPOFFSEL Indicates whether the feature that enables ARP offload is selected.
This field becomes 1 if you select the "enable IPv4 ARP offload" option.
0b - Not selected
1b - Selected
8 MAC Management Counters (MMC) Feature
MMCSEL Indicates whether the feature that enables MMC is selected.
This field becomes 1 if you select the "enable MMC" option.
0b - Not selected
1b - Selected
7 PMT Magic Packet Feature
MGKSEL Indicates whether the feature that enables PMT magic detection is selected.
This field becomes 1 if you select the "enable magic packet detection" option.
0b - Not selected
Table continues on the next page...

---

*Page 2742*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
1b - Selected
6 PMT Remote Wake-Up Packet Feature
RWKSEL Indicates whether the feature that enables PMT remote wake-up packet detection is selected.
This field becomes 1 if you select the "enable remote wake-up packet detection" option.
0b - Not selected
1b - Selected
5 SMA (MDIO) Interface Feature
SMASEL Indicates whether the feature that enables station management (MDIO interface) is selected.
This field becomes 1 if you select the "enable station management (MDIO interface)" option.
0b - Not selected
1b - Selected
4 VLAN Hash Filter Feature
VLHASH Indicates whether the filtering option based on VLAN hash table is selected.
This field becomes 1 if you select the "enable VLAN hash table based filtering" option.
0b - Not selected
1b - Selected
3 PCS Select
PCSSEL Indicates if either of the TBI, SGMII, or RTBI PHY interface options is selected.
This field becomes 1 if you select any one of the TBI, SGMII, or RTBI PHY interface options.
0b - No
1b - Yes
2 Half-Duplex Support Feature
HDSEL Indicates whether half-duplex support is available as the mode of operation.
This field becomes 1 if you select Half-Duplex as the mode of operation.
0b - Unavailable
1b - Available
1 1000 Mbit/s Support Feature
GMIISEL Indicates whether 1000 Mbit/s support is available as the mode of operation.
This field becomes 1 if you select 1000 Mbit/s as the mode of operation.
0b - Unavailable
1b - Available
Table continues on the next page...

---

*Page 2743*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0 10 or 100 Mbit/s Support Feature
MIISEL Indicates whether 10 or 100 Mbit/s support is available as the mode of operation.
This field becomes 1 if you select 10/100 Mbit/s as the mode of operation.
0b - Unavailable
1b - Available

#### 72.17.38 MAC Hardware Feature 1 (MAC_HW_Feature1)

Offset
Register Offset
MAC_HW_Feature1 120h
Function
Indicates the presence of the second set of optional features or functions of the module. You can use this register to dynamically
enable or disable the programs related to these features.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
POUO RAVS DBGM TSOE SPHE DCBE
R L3L4FNUM HASHTBLSZ AVSEL
Reserv Reserv Reserv
ST EL EMA N N N
ed ed ed
W
Reset 0 0 1 0 0 0 0 1 0 0 0 1 1 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
ADVT
PTOE OSTE SPRA
R ADDR64 HWO .. TXFIFOSIZE RXFIFOSIZE
N N M
.
W
Reset 0 0 1 0 1 0 0 1 1 0 1 0 0 1 1 0
Fields
Field Function
31 Reserved
—
30-27 L3 Or L4 Filter Number
Table continues on the next page...

---

*Page 2744*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
L3L4FNUM Indicates the total number of L3 or L4 filters.
0000b - No filters (0)
0001b - 1
0010b - 2
0011b - 3
0100b - 4
0101b - 5
0110b - 6
0111b - 7
1000b - 8
26 Reserved
—
25-24 Hash Table Size
HASHTBLSZ Indicates the size of the hash table.
00b - No hash table
01b - 64
10b - 128
11b - 256
23 One Step For PTP Over UDP/IP Feature
POUOST Indicates whether the feature that enables one step for PTP over UDP/IP is selected.
This field becomes 1 if you select the "enable one-step timestamp for PTP over UDP/IP" option.
0b - Not selected
1b - Selected
22 Reserved
—
21 Receive Side-Only AV Feature
RAVSEL Indicates whether the feature that enables audio-video bridging only on the receive side is selected.
This field becomes 1 if you select the "enable audio-video bridging on receive side only" option.
0b - Not selected
1b - Selected
20 AV Feature
Indicates whether the feature that enables audio-video bridging is selected.
Table continues on the next page...

---

*Page 2745*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
AVSEL This field becomes 1 if you select the "enable audio video bridging" option.
0b - Not selected
1b - Selected
19 DMA Debug Registers Enable Feature
DBGMEMA Indicates whether the feature that enables DMA debug registers is selected.
This field becomes 1 if you select the "enable DMA debug mode" option.
0b - Not selected
1b - Selected
18 TCP Segmentation Offload Enable Feature
TSOEN Indicates whether the feature that enables TCP segmentation offload for TCP/IP packets is selected.
This field becomes 1 if you select the "enable TCP segmentation offloading for TCP/IP packets" option.
0b - Not selected
1b - Selected
17 Split Header Enable Feature
SPHEN Indicates whether the feature that enables a split header structure is selected.
This field becomes 1 if you select the "enable split header structure" option.
0b - Not selected
1b - Selected
16 DCB Enable Feature
DCBEN Indicates whether the feature that enables data center bridging is selected.
This field becomes 1 if you select the "enable data center bridging" option.
0b - Not selected
1b - Selected
15-14 Address Width Feature
ADDR64 Indicates the configured address width.
00b - 32
01b - 40
10b - 48
11b - Reserved
13 IEEE 1588 High-Word Feature
ADVTHWORD Indicates whether the feature that enables the IEEE 1588 high-word register is selected.
This field becomes 1 if you select the "enable/add IEEE 1588 higher-word register" option.
Table continues on the next page...

---

*Page 2746*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Not selected
1b - Selected
12 PTP Offload Enable Feature
PTOEN Indicates whether the feature that enables PTP offload is selected.
This field becomes 1 if you select the "enable PTP timestamp offload" option.
0b - Not selected
1b - Selected
11 One-Step Timestamping Enable Feature
OSTEN Indicates whether the feature that enables one-step timestamping is selected.
This field becomes 1 if you select the "enable one-step timestamp" option.
0b - Not selected
1b - Selected
10-6 MTL Transmit FIFO Size Feature
TXFIFOSIZE Contains the configured value of MTL transmit FIFO (in bytes) expressed as log to base 2 minus 7, which
means, Log 2 (TXFIFO_SIZE) - 7.
0_0000b - 128 bytes
0_0001b - 256 bytes
0_0010b - 512 bytes
0_0011b - 1024 bytes
0_0100b - 2048 bytes
0_0101b - 4096 bytes
0_0110b - 8192 bytes
0_0111b - 16384 bytes
0_1000b - 32 KB
0_1001b - 64 KB
0_1010b - 128 KB
0_1011b - Reserved
5 Single Port RAM Feature
SPRAM Indicates whether the single-port RAM feature is selected.
This field becomes 1 if you select the option that enables the "use single port RAM" feature.
0b - Not selected
1b - Selected
Table continues on the next page...

---

*Page 2747*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
4-0 MTL Receive FIFO Size Feature
RXFIFOSIZE Contains the configured value of MTL receive FIFO (in bytes) expressed as log to base 2 minus 7, which
means, Log 2 (RXFIFO_SIZE) - 7.
0_0000b - 128 bytes
0_0001b - 256 bytes
0_0010b - 512 bytes
0_0011b - 1024 bytes
0_0100b - 2048 bytes
0_0101b - 4096 bytes
0_0110b - 8192 bytes
0_0111b - 16384 bytes
0_1000b - 32 KB
0_1001b - 64 KB
0_1010b - 128 KB
0_1011b - 256 KB
0_1100b - Reserved

#### 72.17.39 MAC Hardware Feature 2 (MAC_HW_Feature2)

Offset
Register Offset
MAC_HW_Feature2 124h
Function
Indicates the presence of the third set of optional features or functions of the module. You can use this register to dynamically
enable or disable the programs related to these features.

---

*Page 2748*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R AUXSNAPNUM PPSOUTNUM TXCHCNT
Reserv Reserv
Reserved Reserved
ed ed
W
Reset 0 0 0 0 0 1 0 0 0 0 0 0 0 1 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXCHCNT TXQCNT RXQCNT
Reserved Reserved
W
Reset 0 0 0 1 0 0 0 0 0 1 0 0 0 0 0 1
Fields
Field Function
Reserved
31
—
30-28 Number Of Auxiliary Snapshot Inputs
AUXSNAPNUM Indicates the number of auxiliary snapshot inputs.
000b - No auxiliary input (0)
001b - 1
010b - 2
011b - 3
100b - 4
101b - Reserved
Reserved
27
—
26-24 Number Of PPS Outputs
PPSOUTNUM Indicates the number of PPS outputs.
000b - No PPS output (0)
001b - 1
010b - 2
011b - 3
100b - 4
101b - Reserved
23-22 Reserved
—
Table continues on the next page...

---

*Page 2749*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
21-18 Number Of DMA Transmit Channels
TXCHCNT Indicates the number of DMA transmit channels.
0000b - 1
0001b - 2
0010b - 3
0011b - 4
0100b - 5
0101b - 6
0110b - 7
0111b - 8
17-16 Reserved
—
15-12 Number Of DMA Receive Channels
RXCHCNT Indicates the number of DMA receive channels.
0000b - 1
0001b - 2
0010b - 3
0011b - 4
0100b - 5
0101b - 6
0110b - 7
0111b - 8
11-10 Reserved
—
9-6 Number Of MTL Transmit Queues
TXQCNT Indicates the number of MTL transmit queues.
0000b - 1
0001b - 2
0010b - 3
0011b - 4
0100b - 5
0101b - 6
Table continues on the next page...

---

*Page 2750*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0110b - 7
0111b - 8
5-4 Reserved
—
3-0 Number Of MTL Receive Queues
RXQCNT Indicates the number of MTL receive queues.
0000b - 1
0001b - 2
0010b - 3
0011b - 4
0100b - 5
0101b - 6
0110b - 7
0111b - 8

#### 72.17.40 MAC Hardware Feature 3 (MAC_HW_Feature3)

Offset
Register Offset
MAC_HW_Feature3 128h
Function
Indicates the presence of the fourth set of optional features or functions of the module. You can use this register to
dynamically enable or disable the programs related to these features.

---

*Page 2751*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
TBSS FPES ESTS
R ASP ESTWID ESTDEP
EL EL EL
Reserved Reserved
W
Reset 0 0 1 0 1 1 0 0 0 0 1 1 0 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
FRPS PDUP DVLA CBTIS
R FRPES FRPBS NRVF
Reserv Reserv
EL SEL N EL
Reserved
ed ed
W
Reset 0 0 0 0 1 1 1 0 0 0 1 1 0 0 0 1
Fields
Field Function
31-30 Reserved
—
29-28 Automotive Safety Package
ASP Indicates the encoding for the different automotive safety features.
00b - No safety features selected
01b - Only "ECC protection for external memory" feature is selected
10b - All the safety features are selected without the "parity port enable for external interface"
feature
11b - All the safety features are selected with the "parity port enable for external interface" feature
27 Time-Based Scheduling Feature
TBSSEL Indicates whether the time-based scheduling feature is selected.
This field becomes 1 if you select the option that enables the time-based scheduling feature.
0b - Selected
1b - Selected
26 Frame Preemption Feature
FPESEL Indicates whether the feature that enables frame preemption is selected.
This field becomes 1 if you select the "enable frame preemption" option.
0b - Not selected
1b - Selected
25-22 Reserved
—
Table continues on the next page...

---

*Page 2752*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
21-20 Estimated Time Interval Width
ESTWID Indicates the width configured for the time interval field in the gate control list.
00b - Width not configured
01b - 16 bits
10b - 20 bits
11b - 24 bits
19-17 Depth Of Gate Control List
ESTDEP Indicates the depth of gate control list expressed as Log 2 (DWC_EQOS_EST_DEP) - 5.
000b - No depth configured
001b - 64 bytes
010b - 128 bytes
011b - 256 bytes
100b - 512 bytes
101b - 1024 bytes
110b - Reserved
16 Enhancements To Scheduling Traffic Feature
ESTSEL Indicates whether the "enhancements to scheduling traffic" feature is selected.
This field becomes 1 if you select the "enable enhancements to scheduling traffic" option.
0b - Not selected
1b - Selected
Reserved
15
—
14-13 Flexible Receive Parser Table Entry Size
FRPES Indicates the maximum number of parser entries that the flexible receive parser supports.
00b - 64
01b - 128
10b - 256
11b - Reserved
12-11 Flexible Receive Parser Buffer Size
FRPBS Indicates the maximum number of packet data bytes that the flexible receive parser supports.
00b - 64
Table continues on the next page...

---

*Page 2753*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
01b - 128
10b - 256
11b - Reserved
10 Flexible Receive Parser Feature
FRPSEL Indicates whether the flexible receive parser feature is selected.
This field becomes 1 if you select the "enable flexible programmable receive parser" option.
0b - Not selected
1b - Selected
9 Broadcast/Multicast Packet Duplication Feature
PDUPSEL Indicates whether the broadcast or multicast duplication feature is selected.
This field becomes 1 if you select the "broadcast or multicast packet duplication" option.
0b - Not selected
1b - Selected
8-6 Reserved
—
5 Double VLAN Tag Processing Feature
DVLAN Indicates whether the double VLAN processing feature is selected.
0b - Not selected
1b - Selected
4 Queue/Channel Based VLAN Tag Insertion On Transmit Feature
CBTISEL Indicates whether the "queue- or channel-based VLAN tag insertion on Tx" feature is selected.
This field becomes 1 if you select the "enable queue- or channel-based VLAN tag insertion on Tx" option.
0b - Not selected
1b - Selected
Reserved
3
—
2-0 Number Of Extended VLAN Tag Filters
NRVF Indicates the number of selected extended VLAN tag filters.
000b - No filters (0)
001b - 4
010b - 8
Table continues on the next page...

---

*Page 2754*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
011b - 16
100b - 24
101b - 32
110b - Reserved

#### 72.17.41 MAC DPP FSM Interrupt Status (MAC_DPP_FSM_Interrupt_Status)

Offset
Register Offset
MAC_DPP_FSM_Interru 140h
pt_Status
Function
Contains the status of automative-safety related data path parity errors, interface timeout errors, FSM state parity errors, and
FSM state timeout errors.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
FSMP MSTT
R
Reserv
ES ES
Reserved Reserved
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
ARPE MTSP RDPE
R PTES ATES RTES TTES MPES
Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv
S ES S
ed ed ed ed ed ed ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-25 Reserved
—
24 FSM State Parity Error Status
FSMPES Indicates whether a parity error is detected on one of the FSM state registers.
Table continues on the next page...

---

*Page 2755*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
After a read operation, the field's value clears to 0.
0b - Not detected
1b - Detected
23-18 Reserved
—
17 Reserved
—
16 Master Read Or Write Timeout Error Status
MSTTES Indicates whether an application or CSR timeout error is detected on the master ( AXI , AHB, ARI, or
ATI) interface.
After a read operation, the field's value clears to 0.
0b - Not detected
1b - Detected
15 Reserved
—
14 Reserved
—
13 Reserved
—
12 PTP FSM Timeout Error Status
PTES Indicates whether one of the PTP FSM timeout errors is detected.
After a read operation, the field's value clears to 0.
0b - Not detected
1b - Detected
11 APP FSM Timeout Error Status
ATES Indicates whether one of the APP FSM timeout errors is detected.
After a read operation, the field's value clears to 0.
0b - Not detected
1b - Detected
10 Reserved
—
Table continues on the next page...

---

*Page 2756*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
9 Receive FSM Timeout Error Status
RTES Indicates whether one of the receive FSM timeout errors is detected.
After a read operation, the field's value clears to 0.
0b - Not detected
1b - Detected
8 Transmit FSM Timeout Error Status
TTES Indicates whether one of the transmit FSM timeout errors is detected.
After a read operation, the field's value clears to 0.
0b - Not detected
1b - Detected
7 Reserved
—
6 Reserved
—
5 Application Receive Interface Data Path Parity Error Status
ARPES Indicates whether the parity checker detected a parity error on the application receive interface (or on PC6
as shown in the transmit data path parity protection diagram).
After a read operation, the field's value clears to 0.
0b - Not detected
1b - Detected
4 MTL Transmit Status Data Path Parity Checker Error Status
MTSPES Indicates whether the parity checker detected a parity error for the MTL transmit status data on ATI (or on
PC5 as shown in the transmit data path parity protection diagram).
After a read operation, the field's value clears to 0.
0b - Not detected
1b - Detected
3 MTL Data Path Parity Checker Error Status
MPES Indicates whether the parity checker detected a parity error on the MTL transmit write controller interface (or
on PC4 as shown in the transmit data path parity protection diagram).
After a read operation, the field's value clears to 0.
0b - Not detected
1b - Detected
Table continues on the next page...

---

*Page 2757*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
2 Read Descriptor Parity Checker Error Status
RDPES Indicates whether the parity checker detected a parity error on the DMA read descriptor interface (or on PC3
as shown in the transmit data path parity protection diagram).
After a read operation, the field's value clears to 0.
0b - Not detected
1b - Detected
1 Reserved
—
0 Reserved
—

#### 72.17.42 MAC FSM Control (MAC_FSM_Control)

Offset
Register Offset
MAC_FSM_Control 148h
Function
Is used to control the FSM state parity and timeout error injection in Debug mode.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv Reserv Reserv PLGR ALGR Reserv RLGR TLGR Reserv Reserv Reserv Reserv
PPEIN APEIN RPEIN TPEIN
ed ed ed NML NML ed NML NML ed ed ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserv Reserv Reserv Reserv PRTY TMOU
PTEIN ATEIN RTEIN TTEIN Reserved
ed ed ed ed EN TEN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Reserved
Table continues on the next page...

---

*Page 2758*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
—
30 Reserved
—
29 Reserved
—
28 PTP Large Or Normal Mode Select
PLGRNML Indicates which mode of tic generation is used for the PTP domain.
0b - Normal mode
1b - Large mode
27 APP Large Or Normal Mode Select
ALGRNML Indicates which mode of tic generation is used for the APP domain.
0b - Normal mode
1b - Large mode
26 Reserved
—
25 Receive Large Or Normal Mode Select
RLGRNML Indicates which mode of tic generation is used for the receive domain.
0b - Normal mode
1b - Large mode
24 Transmit Large Or Normal Mode Select
TLGRNML Indicates which mode of tic generation is used for the transmit domain.
0b - Normal mode
1b - Large mode
23 Reserved
—
22 Reserved
—
21 Reserved
—
20 PTP FSM Parity Error Injection
Table continues on the next page...

---

*Page 2759*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
PPEIN Indicates the status of error injection for PTP FSM parity.
0b - Disabled
1b - Enabled
19 APP FSM Parity Error Injection
APEIN Indicates the status of error injection for APP FSM parity.
0b - Disabled
1b - Enabled
18 Reserved
—
17 Receive FSM Parity Error Injection
RPEIN Indicates the status of error injection for receive FSM parity.
0b - Disabled
1b - Enabled
16 Transmit FSM Parity Error Injection
TPEIN Indicates the status of error injection for transmit FSM parity.
0b - Disabled
1b - Enabled
15 Reserved
—
14 Reserved
—
13 Reserved
—
12 PTP FSM Timeout Error Injection
PTEIN Indicates the status of error injection for PTP FSM timeout.
0b - Disabled
1b - Enabled
11 APP FSM Timeout Error Injection
ATEIN Indicates the status of APP FSM timeout error injection.
0b - Disabled
Table continues on the next page...

---

*Page 2760*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
1b - Enabled
10 Reserved
—
9 Receive FSM Timeout Error Injection
RTEIN Indicates the status of receive FSM timeout error injection.
0b - Disabled
1b - Enabled
8 Transmit FSM Timeout Error Injection
TTEIN Indicates the status of transmit FSM timeout eror injection.
0b - Disabled
1b - Enabled
7-2 Reserved
—
1 Parity Enable
PRTYEN Indicates whether the FSM parity feature is enabled.
0b - Disabled
1b - Enabled
0 Time Out Enable
TMOUTEN Indicates the status of the FSM timeout feature.
0b - Disabled
1b - Enabled

#### 72.17.43 MAC FSM ACT Timer (MAC_FSM_ACT_Timer)

Offset
Register Offset
MAC_FSM_ACT_Timer 14Ch
Function
Is used to select the FSM and interface timeout values.

---

*Page 2761*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved LTMRMD NTMRMD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved TMR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-24 Reserved
—
23-20 Large Mode Timeout Value
LTMRMD Provides the timeout duration value to be used for large mode FSM and other interface timeouts.
0000b - Timer disabled
0001b - 1 us
0010b - 1.024 ms (~4 ms)
0011b - 16.384 ms (~16 ms)
0100b - 65.536 ms (~64 ms)
0101b - 262.144 ms (~256 ms)
0110b - 1.048 sec (~1 sec)
0111b - 4.194 sec (~4sec)
1000b - 16.777 sec (~16 sec)
1001b - 33.554 sec (~32 sec)
1010b - 67.108 sec (~64 sec)
1011b - Reserved
19-16 Normal Mode Timeout Value
NTMRMD Provides the timeout duration value to be used for normal mode FSM and other interface timeouts.
0000b - Timer disabled
0001b - 1 us
0010b - 1.024 ms (~4 ms)
0011b - 16.384 ms (~16 ms)
Table continues on the next page...

---

*Page 2762*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0100b - 65.536 ms (~64 ms)
0101b - 262.144 ms (~256 ms)
0110b - 1.048 sec (~1 sec)
0111b - 4.194 sec (~4 sec)
1000b - 16.777 sec (~16 sec)
1001b - 33.554 sec (~32 sec)
1010b - 67.108 sec (~64 sec)
1011b - Reserved
15-10 Reserved
—
9-0 CSR Clocks For 1 μs Tic
TMR Indicates the number of CSR clocks required to generate 1 μs tic.

#### 72.17.44 SCS_REG 1 (SCS_REG1)

Offset
Register Offset
SCS_REG1 150h
Function
Is an NXP-reserved register for internal use.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
MAC_SCS1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
MAC_SCS1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2763*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 MAC SCS 1
MAC_SCS1 Is reserved for NXP internal use.
The value of this field must always be 0 unless NXP instructs you otherwise. Writing 1 to any of the bits of
this field might cause expected chip behavior.

#### 72.17.45 MAC MDIO Address (MAC_MDIO_Address)

Offset
Register Offset
MAC_MDIO_Address 200h
Function
Controls the management cycles to external PHY through a management interface.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved PSE BTB PA RDA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserv GOC_ GOC_
NTC CR Reserved SKAP C45E GB
ed 1 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-28 Reserved
—
27 Preamble Suppression Enable
PSE Indicates the status of preamble suppression.
• If this field is 1, SMA suppresses the 32-bit preamble and transmits MDIO frames with only 1
preamble bit.
• If this field is 0, the MDIO frame always includes 32 bits of preamble as defined in the IEEE
specification.
Table continues on the next page...

---

*Page 2764*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled
26 Back-To-Back Transactions
BTB Indicates the status of back-to-back transactions.
When BTB = 1 and NTC > 0, MAC indicates the completion of a read or write command at the end of a
frame transfer (before the trailing clocks are transmitted). Therefore, you can initiate the next command,
and that command executes immediately irrespective of the number of trailing clocks generated for the
previous frame.
When BTB = 0, the read or write command completes (GB = 0) only after the trailing clocks generate. In this
mode, you must ensure that:
• NTC is always generated after each frame.
• BTB must not be equal to 1 when NTC = 0.
0b - Disabled
1b - Enabled
25-21 Physical Layer Address
PA Indicates whether MAC accesses clause 22 PHY devices (out of 32) or clause 45 capable PHYs (out of 32).
20-16 Register Or Device Address
RDA Indicates whether MAC accesses clause 22 PHY devices (out of 32) or clause 45 capable PHYs (out of 32).
15 Reserved
—
14-12 Number Of Trailing Clocks
NTC Controls the number of trailing clock cycles generated on the GMII_MDC_O (MDC) signal after the end of
MDIO frame transmission. The valid values can range from 0 to 7.
Writing 3h to this field indicates that there are additional three clock cycles on the MDC line after the end of
MDIO frame transfer.
11-8 CSR Clock Range
CR Determines the frequency of the MDC clock according to the CSR clock frequencies used in your design:
• 0000: CSR clock = 60-100 MHz; MDC clock = CSR clock/42
• 0001: CSR clock = 100-150 MHz; MDC clock = CSR clock/62
• 0010: CSR clock = 20-35 MHz; MDC clock = CSR clock/16
• 0011: CSR clock = 35-60 MHz; MDC clock = CSR clock/26
• 0100: CSR clock = 150-250 MHz; MDC clock = CSR clock/102
• 0101: CSR clock = 250-300 MHz; MDC clock = CSR clock/124
Table continues on the next page...

---

*Page 2765*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
• 0110: CSR clock = 300-500 MHz; MDC clock = CSR clock/204
• 0111: CSR clock = 500-800 MHz; MDC clock = CSR clock/324
When bit 3 of this field is 0, the suggested CSR clock-frequency range applicable to each value ensures that
the MDC clock frequency is approximately between 1.0 MHz and 2.5 MHz.
When bit 3 of this field is 1, you can achieve a higher MDC clock frequency than the IEEE 802.3 frequency
limit of 2.5 MHz, and program a clock divider of a lower value. For example, if the CSR clock frequency is
100 MHz and you write 1010 to this field, the resultant MDC clock frequency equals 12.5 MHz, which is
above the range specified in IEEE 802.3. Program these values only if the interfacing chips support faster
MDC clocks:
• 1000: CSR clock/4
• 1001: CSR clock/6
• 1010: CSR clock/8
• 1011: CSR clock/10
• 1100: CSR clock/12
• 1101: CSR clock/14
• 1110: CSR clock/16
• 1111: CSR clock/18
These bits are not used for accessing RevMII. They are read-only if the RevMII interface is selected as a
single PHY interface.
7-5 Reserved
—
4 Skip Address Packet
SKAP Indicates the status of the skip address packet.
If this field is 1, SMA does not send the address packets before read, write, or post-read increment address
packets. This field is valid only when C45E = 1.
0b - Disabled
1b - Enabled
3 GMII Operation Command 1
GOC_1 Indicates the status of GMII operation command 1.
GMII operation command 1 represents the higher bit of the operation command to PHY or RevMII. The fields
GOC_1 and GOC_O indicate these values:
• 00: Reserved
• 01: Write
• 10: Post-read increment address for clause 45 PHY
• 11: Read
Table continues on the next page...

---

*Page 2766*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
When clause 22 PHY or RevMII is enabled, only write and read commands are valid.
0b - Disabled
1b - Enabled
2 GMII Operation Command 0
GOC_0 Indicates the status of GMII operation command 0.
GMII operation command 0 represents the lower bit of the operation command to PHY or RevMII. When in
SMA mode (MDIO master), this field, along with the GOC_1 field, determines the operation to be performed
to PHY.
This field is read-only and tied to 1 when only RevMII is selected in configuration.
0b - Disabled
1b - Enabled
1 Clause 45 PHY Enable
C45E Indicates the status of clause 45 PHY.
• If this field is 1, clause 45 capable PHY is connected to MDIO.
• If this field is 0, clause 22 capable PHY is connected to MDIO.
0b - Disabled
1b - Enabled
0 GMII Busy
GB Indicates the status of GMII busy.
Writing 1 to this field instructs SMA to initiate a read or write access to the MDIO slave. MAC writes 0 to the
field after the MDIO frame transfer is complete. Therefore, you must not write or change any of the fields in
the MAC_MDIO_Address and MAC_MDIO_Data registers as long as the value of this field is 1.
For write transfers, before writing 1 to this field, you must write a 16-bit data to MAC_MDIO_Data[GD] and
MAC_MDIO_Data[RA] if C45E = 1.
If C45E = 1, you must also write to MAC_MDIO_Data[RA] before initiating a read transfer.
After a read transfer completes (GB = 0), a data read from the PHY register is valid
in MAC_MDIO_Data[GD] .
NOTE
Even if the addressed PHY is not present, there is no change in the functionality of this field.
Access restrictions apply to this field. It clears automatically and writing 0 to it has no effect.
0b - Disabled
1b - Enabled

---

*Page 2767*

Ethernet Media Access Controller (EMAC)

#### 72.17.46 MAC MDIO Data (MAC_MDIO_Data)

Offset
Register Offset
MAC_MDIO_Data 204h
Function
Is used to store the write data to be written to the PHY register located at the address specified in MAC MDIO Address
(MAC_MDIO_Address) . This register also stores the read data from the PHY register located at the address that MAC MDIO
Address (MAC_MDIO_Address) specifies.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
RA
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
GD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-16 Register Address
RA Contains the PHY register address intended for the MDIO frame.
This field is valid only when MAC_MDIO_Address[C45E] = 1.
15-0 GMII Data
GD Contains the 16-bit data value read from PHY or RevMII after a management read operation or the 16-bit
data value to be written to PHY or RevMII before a management write operation.

#### 72.17.47 MAC CSR Software Control (MAC_CSR_SW_Ctrl)

Offset
Register Offset
MAC_CSR_SW_Ctrl 230h
Function
Contains software programmable controls for changing the CSR access response and clearing the status bits on read.

---

*Page 2768*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved SEEN Reserved RCWE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-9 Reserved
—
8 Slave Error Response Enable
SEEN Indicates the status of the slave error response.
• If this field is 1, MAC responds with a slave error for accesses to reserved registers in the CSR
space.
• If this field is 0, MAC sends an "okay" response to registers accessed from the CSR space.
0b - Disabled
1b - Enabled
7-1 Reserved
—
0 Enable Register Write 1 To Clear (W1C)
RCWE Enables and disables the W1C feature for some registers.
When this field is 1, the access mode of some of the other fields changes from R2C to W1C.
When this field is 0, the access mode of those fields remains R2C.
0b - Disabled
1b - Enabled

#### 72.17.48 MAC FPE Control STS (MAC_FPE_CTRL_STS)

Offset
Register Offset
MAC_FPE_CTRL_STS 234h

---

*Page 2769*

Ethernet Media Access Controller (EMAC)
Function
Controls the operation of frame preemption.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved TRSP TVER RRSP RVER
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
S1_SE
Reserved SRSP SVER EFPE
T_0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-20 Reserved
—
19 Transmitted Respond Frame
TRSP Indicates whether the respond frame is transmitted.
This field becomes 1 when a respond frame is transmitted (triggered by writing 1 to the SRSP field). An
interrupt can be generated for this event if you write 1 to MAC_Interrupt_Enable[FPEIE] .
Access restrictions apply to this field. TRSP becomes a W1C field when MAC_CSR_SW_Ctrl[RCWE] =
1, and becomes an R2C field when MAC_CSR_SW_Ctrl[RCWE] = 0. Thus, TRSP becomes 0 sometimes
when you read it and sometimes when you write 1 to it. It automatically becomes 1 on an internal event.
0b - Not transmitted
1b - Transmitted
18 Transmitted Verify Frame
TVER Indicates whether the verify frame is transmitted.
This field becomes 1 when a verify frame is transmitted (triggered by writing 1 to the SVER field). An interrupt
can be generated for this event if you write 1 to MAC_Interrupt_Enable[FPEIE] .
Access restrictions apply to this field. The field clears on read or write of 1 when
MAC_CSR_SW_Ctrl[RCWE] = 1. It automatically becomes 1 on an internal event.
0b - Not transmitted
1b - Transmitted
17 Received Respond Frame
RRSP Indicates whether the respond frame is received.
Table continues on the next page...

---

*Page 2770*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
This field becomes 1 when a respond frame is received. An interrupt can be generated for this event if you
write 1 to MAC_Interrupt_Enable[FPEIE] .
Access restrictions apply to this field. The field clears on read or write of 1 when
MAC_CSR_SW_Ctrl[RCWE] = 1. It automatically becomes 1 on an internal event.
0b - Not received
1b - Received
16 Received Verify Frame
RVER Indicates whether the verify frame is received.
This field becomes 1 when a verify frame is received. An interrupt can be generated on the transmission of
a packet if you write 1 to MAC_Interrupt_Enable[FPEIE] .
Access restrictions apply to this field. The field clears on read or write of 1 when
MAC_CSR_SW_Ctrl[RCWE] = 1. It automatically becomes 1 on an internal event.
0b - Not received
1b - Received
15-4 Reserved
—
3 S1 SET 0
S1_SET_0 Is reserved for NXP internal use and must always be 0 unless instructed by NXP. Writing 1 to this field may
cause unexpected behavior.
2 Send Respond mPacket
SRSP Indicates the status of the send respond mPacket.
If this field is 1, it indicates the hardware to send a respond mPacket. The hardware resets the field after
sending the respond mPacket.
Access restrictions apply to this field. It clears automatically and writing 0 to it has no effect.
0b - Disabled
1b - Enabled
1 Send Verify mPacket
SVER Enables and disables the sending of a verify mPacket.
Write 1 to this field to command EMAC to send a verify mPacket. EMAC writes 0 to this field after sending
the verify mPacket.
Access restrictions apply to this field. It clears automatically and writing 0 to it has no effect.
0b - Disabled
1b - Enabled
0 Enable Transmit Frame Preemption
Table continues on the next page...

---

*Page 2771*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
EFPE Enables or disables the frame preemption transmit functionality.
0b - Disabled
1b - Enabled

#### 72.17.49 MAC Presentation Time (MAC_Presn_Time_ns)

Offset
Register Offset
MAC_Presn_Time_ns 240h
Function
Contains the 32-bit binary rollover equivalent time of the PTP system time (in nanoseconds) and exists when
DWC_EQOS_FLEXI_PPS_OUT_EN is configured.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R MPTN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R MPTN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 MAC 1722 Presentation Time (In Nanoseconds)
MPTN Indicates the value of the 32-bit binary rollover equivalent time of the PTP System Time (in ns).

#### 72.17.50 MAC Presentation Time Update (MAC_Presn_Time_Updt)

Offset
Register Offset
MAC_Presn_Time_Updt 244h

---

*Page 2772*

Ethernet Media Access Controller (EMAC)
Function
Holds the 32-bit value of MAC 1722 presentation time (in ns), which must be added to the current presentation
time counter value. Initialization happens when MAC_Timestamp_Control[TSINIT] = 1, and an update happens when
MAC_Timestamp_Control[TSUPDT] = 1.
Exists when DWC_EQOS_FLEXI_PPS_OUT_EN is configured.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
MPTU
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
MPTU
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 MAC 1722 Presentation Time Update
MPTU Holds the initial value or the update value for the presentation time. When used for an update, this
field holds the 32-bit value (in ns), which must be added to the current presentation time counter
value. Initialization happens when MAC_Timestamp_Control[TSINIT] = 1, and an update happens when
MAC_Timestamp_Control[TSUPDT] = 1.
When MAC_System_Time_Nanoseconds_Update[ADDSUB] = 1, this value is directly used for subtraction.

#### 72.17.51 MAC Address 0 High (MAC_Address0_High)

Offset
Register Offset
MAC_Address0_High 300h
Function
Holds the upper 16 bits of the first 6-byte MAC address of the station.
The first DA byte that is received on the (G)MII interface corresponds to the LS byte (bits [7:0]) of MAC Address 0 Low
(MAC_Address0_Low) . For example, if 112233445566h is received (11h in lane 0 of the first column) on (G)MII as the destination
address, MAC Address 0 Low (MAC_Address0_Low) [47:0] is compared to 665544332211h.
If MAC address registers are configured to be double-synchronized with the (G)MII clock domains, the synchronization is triggered
only when bits [31:24] (in Little-Endian mode) or bits [7:0] (in Big-Endian mode) of MAC Address 0 Low (MAC_Address0_Low)
are written to. For proper synchronization updates, the consecutive writes to MAC Address 0 Low (MAC_Address0_Low) must be
performed after at least four clock cycles in the destination clock domain.

---

*Page 2773*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R AE
Reserved DCS
W
Reset 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
ADDRHI
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31 Address Enable
AE Enables MAC address
0b - Disabled and invalid (the field's value must always be 1)
1b - Enabled
30-18 Reserved
—
17-16 DMA Channel Select
DCS If MAC_Ext_Configuration[PDC] = 0, this field contains the binary representation of the DMA channel
number to which a receive packet whose DA matches MAC Address 0 Low (MAC_Address0_Low) content
is routed.
If MAC_Ext_Configuration[PDC] = 1, this field contains the one-hot representation of one or more DMA
channel numbers to which a receive packet whose DA matches MAC Address 0 Low (MAC_Address0_Low)
content is routed.
15-0 MAC Address 0 [47:32]
ADDRHI Contains the upper 16 bits [47:32] of the first 6-byte MAC address.
MAC uses this field for filtering the received packets and inserting the MAC address in the transmit flow
control (pause) packets.

#### 72.17.52 MAC Address 0 Low (MAC_Address0_Low)

Offset
Register Offset
MAC_Address0_Low 304h

---

*Page 2774*

Ethernet Media Access Controller (EMAC)
Function
Holds the lower 32 bits of the 6-byte first MAC address of the station.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
ADDRLO
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
ADDRLO
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31-0 MAC Address 0 [31:0]
ADDRLO Contains the lower 32 bits of the first 6-byte MAC address. MAC uses this field to filter the received packets
and insert the MAC address in the transmit flow control (pause) packets.

#### 72.17.53 MAC Address 1 High (MAC_Address1_High)

Offset
Register Offset
MAC_Address1_High 308h
Function
Holds the upper 16 bits of the second 6-byte MAC address of the station.
If the MAC address registers are configured to be double-synchronized with the (G)MII clock domains, the synchronization
is triggered only when bits [31:24] (in Little-Endian mode) or bits [7:0] (in Big-Endian mode) of MAC Address 1 Low
(MAC_Address1_Low) are written to. For proper synchronization updates, the consecutive writes to MAC Address 1 Low
(MAC_Address1_Low) must be performed after at least four clock cycles in the destination clock domain.

---

*Page 2775*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
AE SA MBC Reserved DCS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
ADDRHI
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31 Address Enable
AE Indicates whether the MAC address is enabled or ignored.
If this field is 1, the address filter module uses the second MAC address for perfect filtering. If this field is 0,
the address filter module ignores the address used for filtering.
0b - Ignored
1b - Enabled
30 Source Address
SA Indicates whether the MAC address is compared to the source or destination address.
If this field is 1, MAC Address 1[47:0] is compared to the SA fields of the received packet. If this field is 0,
MAC Address 1[47:0] is compared to the DA fields of the received packet.
0b - Destination address
1b - Source address
29-24 Mask Byte Control
MBC Contains mask control bits for comparing each of the MAC address bytes. If this field is 1, MAC does not
compare the corresponding byte of the received DA or SA with the contents of the MAC address 1 registers.
Each bit controls the masking of the bytes as follows:
• Bit 29: MAC Address 1 High[15:8]
• Bit 28: MAC Address 1 High[7:0]
• Bit 27: MAC Address 1 Low[31:24]
• - ..
• Bit 24: MAC Address 1 Low[7:0]
You can filter a group of addresses (known as group address filtering) by masking one or more bytes of
the address.
23-18 Reserved
Table continues on the next page...

---

*Page 2776*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
—
17-16 DMA Channel Select
DCS If MAC_Ext_Configuration[PDC] = 0, this field contains the binary representation of the DMA channel
number to which a receive packet whose DA matches MAC Address 1 Low (MAC_Address1_Low) content
is routed.
If MAC_Ext_Configuration[PDC] = 1, this field contains the one-hot representation of one or more
DMA Channel numbers to which a receive packet whose DA matches MAC Address 1 Low
(MAC_Address1_Low) content is routed.
15-0 MAC Address 1 [47:32]
ADDRHI Contains the upper 16 bits [47:32] of the second 6-byte MAC address.

#### 72.17.54 MAC Address 1 Low (MAC_Address1_Low)

Offset
Register Offset
MAC_Address1_Low 30Ch
Function
Holds the lower 32 bits of the second 6-byte MAC address of the station.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
ADDRLO
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
ADDRLO
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31-0 MAC Address 1 [31:0]
Contains the lower 32 bits of the second 6-byte MAC address.
Table continues on the next page...

---

*Page 2777*

Ethernet Media Access Controller (EMAC)
Field Function
ADDRLO The contents of this field are undefined until you load them after the initialization process.

#### 72.17.55 MAC Address 2 High (MAC_Address2_High)

Offset
Register Offset
MAC_Address2_High 310h
Function
Holds the upper 16 bits of the second 6-byte MAC address of the station.
If the MAC address registers are configured to be double-synchronized with the (G)MII clock domains, the synchronization
is triggered only when bits [31:24] (in Little-Endian mode) or bits [7:0] (in Big-Endian mode) of MAC Address 1 Low
(MAC_Address1_Low) are written to. For proper synchronization updates, the consecutive writes to MAC Address 1 Low
(MAC_Address1_Low) must be performed after at least four clock cycles in the destination clock domain.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
AE SA MBC Reserved DCS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
ADDRHI
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31 Address Enable
AE Indicates whether the MAC address is enabled or ignored.
If this field is 1, the address filter module uses the second MAC address for perfect filtering. If this field is 0,
the address filter module ignores the address for filtering.
0b - Ignored
1b - Enabled
30 Source Address
SA Indicates whether the MAC address is compared to the source or destination address.
Table continues on the next page...

---

*Page 2778*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
If this field is 1, MAC Address 1[47:0] is compared to the SA fields of the received packet. If this field is 0,
MAC Address 1[47:0] is compared to the DA fields of the received packet.
0b - Destination address
1b - Source address
29-24 Mask Byte Control
MBC Contains mask control bits for comparing each of the MAC address bytes. If this field is 1, MAC does not
compare the corresponding byte of the received DA or SA with the contents of MAC address 1 registers.
Each bit controls the masking of the bytes as follows:
• Bit 29: MAC Address 1 High[15:8]
• Bit 28: MAC Address 1 High[7:0]
• Bit 27: MAC Address 1 Low[31:24]
• - ..
• Bit 24: MAC Address 1 Low[7:0]
You can filter a group of addresses (known as group address filtering) by masking one or more bytes of
the address.
23-18 Reserved
—
17-16 DMA Channel Select
DCS If MAC_Ext_Configuration[PDC] = 0, this field contains the binary representation of the DMA channel
number to which a receive packet whose DA matches MAC Address 2 Low (MAC_Address2_Low) content
is routed.
If MAC_Ext_Configuration[PDC] = 1, this field contains the one-hot representation of one or more
DMA Channel numbers to which a receive packet whose DA matches MAC Address 2 Low
(MAC_Address2_Low) content is routed.
15-0 MAC Address 1 [47:32]
ADDRHI Contains the upper 16 bits [47:32] of the second 6-byte MAC address.

#### 72.17.56 MAC Address 2 Low (MAC_Address2_Low)

Offset
Register Offset
MAC_Address2_Low 314h
Function
Holds the lower 32 bits of the second 6-byte MAC address of the station.

---

*Page 2779*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
ADDRLO
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
ADDRLO
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31-0 MAC Address 1 [31:0]
ADDRLO Contains the lower 32 bits of the second 6-byte MAC address. The content of this field remains undefined
until you load it after the initialization process.

#### 72.17.57 MMC Control (MMC_Control)

Offset
Register Offset
MMC_Control 700h
Function
Establishes the operating mode for MMC.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
UCDB CNTP CNTP CNTF RSTO CNTS CNTR
Reserved Reserved
C RST ... RST REEZ NRD TOP ... ST
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2780*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-9 Reserved
—
8 Update MMC Counters For Dropped Broadcast Packets
UCDBC Indicates the status of MMC counters for dropped broadcast packets.
• If this field is 1, MAC updates all the related MMC counters for broadcast packets that are dropped
because MAC_Packet_Filter[DBF] = 1.
• If this field is 0, the MMC counters are not updated for dropped broadcast packets.
NOTE
The CNTRST field has a higher priority than the CNTPRST field. Therefore, if you write 1
to both these fields in the same write cycle, all the counters are cleared and the CNTPRST
field becomes 0.
0b - Disabled
1b - Enabled
7-6 Reserved
—
5 Full-Half Preset
CNTPRSTLVL Indicates whether full-half preset is enabled or disabled.
• If this field is 0 and the CNTPRST field is 1, all the MMC counters are preset to the almost-half
value. All octet counters are preset to 7FFF_F800h (half 2 Kbytes) and all packet counters are
preset to 7FFF_FFF0h (half 16).
• If this field is 1 and the CNTPRST field is 1 too, all MMC counters are preset to the almost-full value.
All octet counters are preset to FFFF_F800h (full 2 Kbytes) and all packet counters are preset to
FFFF_FFF0h (full 16).
For 16-bit counters, the almost-half preset values are 7800h and 7FF0h for the respective octet and packet
counters. Similarly, the almost-full preset values for the 16-bit counters are 0xF800 and 0xFFF0.
0b - Disabled
1b - Enabled
4 Counters Preset
CNTPRST Indicates whether MMC counter preset is enabled or disabled.
If this field is 1, all the counters are initialized or preset to almost full or almost half according to the settings
of the CNTPRSTLVL field.
This field clears automatically after 1 clock cycle, and along with the CNTPRSTLVL field, is useful for
debugging and testing the assertion of interrupts because of the MMC counter becoming half-full or full.
Access restrictions apply to this field, which clears automatically.
0b - Disabled
Table continues on the next page...

---

*Page 2781*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
1b - Enabled
3 MMC Counter Freeze
CNTFREEZ Indicates the status of MMC counter freeze.
If this field is 1, the field freezes all the MMC counters to their current values.
Until this field becomes 0, no MMC counter is updated because of any transmitted or received packets. If
an MMC counter is read when RSTONRD = 1, that counter is also cleared in this mode.
0b - Disabled
1b - Enabled
2 Reset On Read
RSTONRD Indicates whether the reset on read for MMC counters is enabled or disabled.
If this field is 1, MMC counters are reset to 0 after read (they clear automatically after reset). The counters
are cleared when the least significant byte lane (bits [7:0]) is read.
0b - Disabled
1b - Enabled
1 Counter Stop Rollover
CNTSTOPRO Indicates the status of counter stop rollover.
If this field is 1, the counter does not roll over to 0 after reaching the maximum value.
0b - Disabled
1b - Enabled
0 Counters Reset
CNTRST Indicates whether MMC counters are reset.
If this field is 1, all the counters are reset. This field clears automatically after 1 clock cycle.
Access restrictions apply to this field that clears automatically.
0b - Counters are not reset
1b - All counters are reset

#### 72.17.58 MMC Receive Interrupt (MMC_Rx_Interrupt)

Offset
Register Offset
MMC_Rx_Interrupt 704h
Function
Maintains the interrupts generated from all receive statistics counters when the following conditions occur:

---

*Page 2782*

Ethernet Media Access Controller (EMAC)
• Receive statistic counters reach half of their maximum values (8000_0000h for a 32-bit counter and 8000h for a 16-bit
counter).
• Receive statistic counters cross their maximum values (FFFF_FFFFh for a 32-bit counter and FFFFh for a 16-bit counter).
If MMC_Control[CNTSTOPRO] = 1, interrupts are set but the counter remains at all-ones. This is a 32-bit register. An interrupt
bit is cleared when the respective MMC counter that caused the interrupt is read. The least significant byte lane (bits [7:0]) of the
respective counter must be read to clear the interrupt bit.
NOTE
R_SS_RC means that this register bit is set internally, and is cleared when the counter register is read.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
RXCT RXRC RXWD RXVL RXFO RXPA RXOR RXLE RXUC RX102
R
Reserv Reserv
RLP ... VER ... OGP ... ANG ... VPIS USP ... ANG ... NER ... GPIS 4T ...
Reserved
ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
RX512 RX256 RX128 RX65T RX64 RXOSI RXUSI RXJA RXRU RXAL RXCR RXMC RXBC RXGO RXGB RXGB
R
T1 ... T5 ... T2 ... 12 ... OCT ... ZE ... ZE ... BER ... NTP ... GNE ... CER ... GPIS GPIS CTIS OCT ... PKT ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-28 Reserved
—
27 Reserved
—
26 Reserved
—
25 MMC Receive Control Packet Counter Interrupt Status
RXCTRLPIS Indicates whether the status of the MMC receive control packet counter interrupt is detected.
This field becomes 1 when the rxctrlpackets_g counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
Table continues on the next page...

---

*Page 2783*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
24 MMC Receive Error Packet Counter Interrupt Status
RXRCVERRPIS Indicates whether the status of the MMC receive error packet counter interrupt is detected.
This field becomes 1 when the rxrcverror counter reaches half of its maximum value or the maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
23 MMC Receive Watchdog Error Packet Counter Interrupt Status
RXWDOGPIS Indicates whether the status of the MMC receive watchdog error packet counter interrupt is detected.
This field becomes 1 when the rxwatchdog error counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
22 MMC Receive VLAN Good Bad Packet Counter Interrupt Status
RXVLANGBPIS Indicates whether the status of the MMC receive VLAN good bad packet counter interrupt is detected.
This field becomes 1 when the rxvlanpackets_gb counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
21 MMC Receive FIFO Overflow Packet Counter Interrupt Status
RXFOVPIS Indicates whether the status of the MMC receive FIFO overflow packet counter interrupt is detected.
This field becomes 1 when the rxfifooverflow counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
20 MMC Receive Pause Packet Counter Interrupt Status
RXPAUSPIS Indicates whether the status of the MMC receive pause packet counter interrupt is detected.
This field becomes 1 when the rxpausepackets counter reaches half of its maximum value or the
maximum value.
Table continues on the next page...

---

*Page 2784*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
19 MMC Receive Out-Of-Range Error Packet Counter Interrupt Status
RXORANGEPI Indicates whether the status of the MMC receive out-of-range error packet counter interrupt is detected.
S
This field becomes 1 when the rxoutofrangetype counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
18 MMC Receive Length Error Packet Counter Interrupt Status
RXLENERPIS Indicates whether the status of the MMC receive length error packet counter interrupt is detected.
This field becomes 1 when the rxlengtherror counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
17 MMC Receive Unicast Good Packet Counter Interrupt Status
RXUCGPIS Indicates whether the status of the MMC receive unicast good packet counter interrupt is detected.
This field becomes 1 when the rxunicastpackets_g counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
16 MMC Receive 1024 To Maximum Octet Good Bad Packet Counter Interrupt Status
RX1024TMAXO Indicates whether the status of the MMC receive 1024 to maximum octet good bad packet counter interrupt
CTGBPIS is detected.
This field becomes 1 when the rx1024tomaxoctets_gb counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
Table continues on the next page...

---

*Page 2785*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Not detected
1b - Detected
15 MMC Receive 512 To 1023 Octet Good Bad Packet Counter Interrupt Status
RX512T1023O Indicates whether the status of the MMC receive 512 to 1023 octet good bad packet counter interrupt
CTGBPIS is detected.
This field becomes 1 when the rx512to1023octets_gb counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
14 MMC Receive 256 To 511 Octet Good Bad Packet Counter Interrupt Status
RX256T511OC Indicates whether the status of the MMC receive 256 to 511 octet good bad packet counter interrupt
TGBPIS is detected.
This field becomes 1 when the rx256to511octets_gb counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
13 MMC Receive 128 To 255 Octet Good Bad Packet Counter Interrupt Status
RX128T255OC Indicates whether the status of the MMC receive 128 to 255 octet good bad packet counter interrupt
TGBPIS is detected.
This field becomes 1 when the rx128to255octets_gb counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
12 MMC Receive 65 To 127 Octet Good Bad Packet Counter Interrupt Status
RX65T127OCT Indicates whether the status of the MMC receive 65 to 127 octet good bad packet counter interrupt
GBPIS is detected.
The field becomes 1 when the rx65to127octets_gb counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
Table continues on the next page...

---

*Page 2786*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Not detected
1b - Detected
11 MMC Receive 64 Octet Good Bad Packet Counter Interrupt Status
RX64OCTGBPI Indicates whether the status of the MMC receive 64-octet good bad packet counter interrupt is detected.
S
The field becomes 1 when the rx64octets_gb counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
10 MMC Receive Oversize Good Packet Counter Interrupt Status
RXOSIZEGPIS Indicates whether the status of the MMC receive oversize good packet counter interrupt is detected.
This field becomes 1 when the rxoversize_g counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
9 MMC Receive Undersize Good Packet Counter Interrupt Status
RXUSIZEGPIS Indicates whether the status of MMC receive undersize good packet counter interrupt is detected.
This field becomes 1 when the rxundersize_g counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
8 MMC Receive Jabber Error Packet Counter Interrupt Status
RXJABERPIS Indicates whether the status of MMC receive jabber error packet counter interrupt is detected.
This field becomes 1 when the rxjabbererror counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
Table continues on the next page...

---

*Page 2787*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
7 MMC Receive Runt Packet Counter Interrupt Status
RXRUNTPIS Indicates whether the status of the MMC receive runt packet counter interrupt is detected.
This field becomes 1 when the rxrunterror counter reaches half of its maximum value or the maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
6 MMC Receive Alignment Error Packet Counter Interrupt Status
RXALGNERPIS Indicates whether the status of the MMC receive alignment error packet counter interrupt is detected.
This field becomes 1 when the rxalignmenterror counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
5 MMC Receive CRC Error Packet Counter Interrupt Status
RXCRCERPIS Indicates whether the status of the MMC receive CRC error packet counter interrupt is detected.
This field becomes 1 when the rxcrcerror counter reaches half of its maximum value or the maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
4 MMC Receive Multicast Good Packet Counter Interrupt Status
RXMCGPIS Indicates whether the status of the MMC receive multicast good packet counter interrupt is detected.
This field becomes 1 when the rxmulticastpackets_g counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
3 MMC Receive Broadcast Good Packet Counter Interrupt Status
RXBCGPIS Indicates whether the status of the MMC receive broadcast good packet counter interrupt is detected.
This field becomes 1 when the rxbroadcastpackets_g counter reaches half of its maximum value or the
maximum value.
Table continues on the next page...

---

*Page 2788*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
2 MMC Receive Good Octet Counter Interrupt Status
RXGOCTIS Indicates whether the status of the MMC receive good octet counter interrupt is detected.
This field becomes 1 when the rxoctetcount_g counter reaches half of its maximum value or its
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
1 MMC Receive Good Bad Octet Counter Interrupt Status
RXGBOCTIS Indicates whether the status of the MMC receive good bad octet counter interrupt is detected.
This field becomes 1 when the rxoctetcount_gb counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
0 MMC Receive Good Bad Packet Counter Interrupt Status
RXGBPKTIS Indicates whether the status of the MMC receive good bad packet counter interrupt is detected.
This field becomes 1 when the rxpacketcount_gb counter reaches half of its maximum value or its
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected

#### 72.17.59 MMC Transmit Interrupt (MMC_Tx_Interrupt)

Offset
Register Offset
MMC_Tx_Interrupt 708h

---

*Page 2789*

Ethernet Media Access Controller (EMAC)
Function
Maintains the interrupts generated from all the transmit statistics counters.
This register maintains those interrupts that are generated when transmit statistic counters:
• Reach half of their maximum values (8000_0000h for a 32-bit counter and 0x8000 for a 16-bit counter)
• Cross their maximum values (FFFF_FFFFh for a 32-bit counter and FFFFh for a 16-bit counter)
If MMC_Control[CNTSTOPRO] = 1, the interrupts are set but the counter remains at all-ones.
This is a 32 bit register. An interrupt bit is cleared when the respective MMC counter that caused the interrupt is read.
The least significant byte lane (bits [7:0]) of the respective counter must be read to clear the interrupt bit.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
TXOSI TXVLA TXPA TXEX TXGP TXGO TXCA TXEX TXLAT TXDE
R
Reserv Reserv
ZE ... NG ... USP ... DEF ... KTIS CTIS RER ... COL ... CO ... FPIS
Reserved
ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
TXMC TXSC TXUFL TXBC TXMC TXUC TX102 TX512 TX256 TX128 TX65T TX64O TXMC TXBC TXGB TXGB
R
OLG ... OLG ... OW ... GBP ... GBP ... GBP ... 4T ... T1 ... T5 ... T2 ... 12 ... CT ... GPIS GPIS PKT ... OCT ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-28 Reserved
—
27 Reserved
—
26 Reserved
—
25 MMC Transmit Oversize Good Packet Counter Interrupt Status
TXOSIZEGPIS Indicates whether the status of the MMC transmit oversize good packet counter interrupt is detected.
This field becomes 1 when the txoversize_g counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
Table continues on the next page...

---

*Page 2790*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
1b - Detected
24 MMC Transmit VLAN Good Packet Counter Interrupt Status
TXVLANGPIS Indicates whether the status of the MMC transmit VLAN good packet counter interrupt is detected.
This field becomes 1 when the txvlanpackets_g counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
23 MMC Transmit Pause Packet Counter Interrupt Status
TXPAUSPIS Indicates whether the status of the MMC transmit pause packet counter interrupt is detected.
This field becomes 1 when the txpausepacketserror counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
22 MMC Transmit Excessive Deferral Packet Counter Interrupt Status
TXEXDEFPIS Indicates whether the status of the MMC transmit excessive deferral packet counter interrupt is detected.
This field becomes 1 when the txexcessdef counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
21 MMC Transmit Good Packet Counter Interrupt Status
TXGPKTIS Indicates whether the status of the MMC transmit good packet counter interrupt is detected.
This field becomes 1 when the txpacketcount_g counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
20 MMC Transmit Good Octet Counter Interrupt Status
Table continues on the next page...

---

*Page 2791*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
TXGOCTIS Indicates whether the status of the MMC transmit good octet counter interrupt is detected.
This field becomes 1 when the txoctetcount_g counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
19 MMC Transmit Carrier Error Packet Counter Interrupt Status
TXCARERPIS Indicates whether the status of the MMC transmit carrier error packet counter interrupt is detected.
This field becomes 1 when the txcarriererror counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
18 MMC Transmit Excessive Collision Packet Counter Interrupt Status
TXEXCOLPIS Indicates whether the status of the MMC transmit excessive collision packet counter interrupt is detected.
This field becomes 1 when the txexesscol counter reaches half of its maximum value or the maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
17 MMC Transmit Late Collision Packet Counter Interrupt Status
TXLATCOLPIS Indicates whether the status of the MMC transmit late collision packet counter interrupt is detected.
This field becomes 1 when the txlatecol counter reaches half of its maximum value or the maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
16 MMC Transmit Deferred Packet Counter Interrupt Status
TXDEFPIS Indicates whether the status of the MMC transmit deferred packet counter interrupt is detected.
This field becomes 1 when the txdeferred counter reaches half of its maximum value or the maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
Table continues on the next page...

---

*Page 2792*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Not detected
1b - Detected
15 MMC Transmit Multiple Collision Good Packet Counter Interrupt Status
TXMCOLGPIS Indicates whether the status of the MMC transmit multiple collision good packet counter interrupt
is detected.
This field becomes 1 when the txmulticol_g counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
14 MMC Transmit Single Collision Good Packet Counter Interrupt Status
TXSCOLGPIS Indicates whether the status of the MMC transmit single collision good packet counter interrupt is detected.
This field becomes 1 when the txsinglecol_g counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
13 MMC Transmit Underflow Error Packet Counter Interrupt Status
TXUFLOWERPI Indicates whether the status of the MMC transmit underflow error packet counter interrupt is detected.
S
This field becomes 1 when the txunderflowerror counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
12 MMC Transmit Broadcast Good Bad Packet Counter Interrupt Status
TXBCGBPIS Indicates whether the status of the MMC transmit broadcast good bad packet counter interrupt is detected.
This field becomes 1 when the txbroadcastpackets_gb counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
Table continues on the next page...

---

*Page 2793*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
11 MMC Transmit Multicast Good Bad Packet Counter Interrupt Status
TXMCGBPIS Indicates whether the status of the MMC transmit multicast good bad packet counter interrupt is detected.
This field becomes 1 when the txmulticastpackets_gb counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
10 MMC Transmit Unicast Good Bad Packet Counter Interrupt Status
TXUCGBPIS Indicates whether the status of the MMC transmit unicast good bad packet counter interrupt is detected.
This field becomes 1 when the txunicastpackets_gb counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
9 MMC Transmit 1024 To Maximum Octet Good Bad Packet Counter Interrupt Status
TX1024TMAXO Indicates whether the status of the MMC transmit 1024 to maximum octet good bad packet counter interrupt
CTGBPIS is detected.
This field becomes 1 when the tx1024tomaxoctets_gb counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
8 MMC Transmit 512 To 1023 Octet Good Bad Packet Counter Interrupt Status
TX512T1023O Indicates whether the status of the MMC transmit 512 to 1023 octet good bad packet counter interrupt
CTGBPIS is detected.
This field becomes 1 when the tx512to1023octets_gb counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
7 MMC Transmit 256 To 511 Octet Good Bad Packet Counter Interrupt Status
Table continues on the next page...

---

*Page 2794*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
TX256T511OC Indicates whether the status of the MMC transmit 256 to 511 octet good bad packet counter interrupt
TGBPIS is detected.
This field becomes 1 when the tx256to511octets_gb counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
6 MMC Transmit 128 To 255 Octet Good Bad Packet Counter Interrupt Status
TX128T255OC Indicates whether the status of the MMC transmit 128 to 255 octet good bad packet counter interrupt
TGBPIS is detected.
This field becomes 1 when the tx128to255octets_gb counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
5 MMC Transmit 65 To 127 Octet Good Bad Packet Counter Interrupt Status
TX65T127OCT Indicates whether the status of the MMC transmit 65 to 127 octet good bad packet counter interrupt
GBPIS is detected.
This field becomes 1 when the tx65to127octets_gb counter reaches half of its maximum value, and also
when it reaches the maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
4 MMC Transmit 64-Octet Good Bad Packet Counter Interrupt Status
TX64OCTGBPI Indicates whether the status of the MMC transmit 64-octet good bad packet counter interrupt is detected.
S
This field becomes 1 when the tx64octets_gb counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
3 MMC Transmit Multicast Good Packet Counter Interrupt Status
TXMCGPIS Indicates whether the status of the MMC transmit multicast good packet counter interrupt is detected.
Table continues on the next page...

---

*Page 2795*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
This field becomes 1 when the txmulticastpackets_g counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
2 MMC Transmit Broadcast Good Packet Counter Interrupt Status
TXBCGPIS Indicates whether the status of the MMC transmit broadcast good packet counter interrupt is detected.
This field becomes 1 when the txbroadcastpackets_g counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
1 MMC Transmit Good Bad Packet Counter Interrupt Status
TXGBPKTIS Indicates whether the status of the MMC transmit good bad packet counter interrupt is detected.
This field becomes 1 when the txpacketcount_gb counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected
0 MMC Transmit Good Bad Octet Counter Interrupt Status
TXGBOCTIS Indicates whether the status of the MMC transmit good bad octet counter interrupt is detected.
This field becomes 1 when the txoctetcount_gb counter reaches half of its maximum value or the
maximum value.
Access restrictions apply to this field, which clears on read and automatically becomes 1 on an internal
event occurrence.
0b - Not detected
1b - Detected

#### 72.17.60 MMC Receive Interrupt Mask (MMC_Rx_Interrupt_Mask)

Offset
Register Offset
MMC_Rx_Interrupt_Mask 70Ch

---

*Page 2796*

Ethernet Media Access Controller (EMAC)
Function
Maintains the masks for interrupts generated from all the receive statistic counters. These interrupts are generated when the
receive statistic counters reach half of their maximum values or the maximum values.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv Reserv RXCT RXRC RXWD RXVL RXFO RXPA RXOR RXLE RXUC RX102
Reserved
ed ed RLP ... VER ... OGP ... ANG ... VPIM USP ... ANG ... NER ... GPIM 4T ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
RX512 RX256 RX128 RX65T RX64 RXOSI RXUSI RXJA RXRU RXAL RXCR RXMC RXBC RXGO RXGB RXGB
T1 ... T5 ... T2 ... 12 ... OCT ... ZE ... ZE ... BER ... NTP ... GNE ... CER ... GPIM GPIM CTIM OCT ... PKT ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-28 Reserved
—
27 Reserved
—
26 Reserved
—
25 MMC Receive Control Packet Counter Interrupt Mask
RXCTRLPIM Indicates the status of the MMC receive control packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the rxctrlpackets_g counter reaches half of its maximum value
or the maximum value.
0b - Disabled
1b - Enabled
24 MMC Receive Error Packet Counter Interrupt Mask
RXRCVERRPI Indicates the status of the MMC receive error packet counter interrupt mask.
M
Writing 1 to this field masks the interrupt when the rxrcverror counter reaches half of its maximum value or
the maximum value.
0b - Disabled
1b - Enabled
23 MMC Receive Watchdog Error Packet Counter Interrupt Mask
Table continues on the next page...

---

*Page 2797*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
RXWDOGPIM Indicates the status of the MMC receive watchdog error packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the rxwatchdog counter reaches half of its maximum value
or the maximum value.
0b - Disabled
1b - Enabled
22 MMC Receive VLAN Good Bad Packet Counter Interrupt Mask
RXVLANGBPIM Indicates the status of the MMC receive VLAN good bad packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the rxvlanpackets_gb counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled
21 MMC Receive FIFO Overflow Packet Counter Interrupt Mask
RXFOVPIM Indicates the status of the MMC receive FIFO overflow packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the rxfifooverflow counter reaches half of its maximum value
or the maximum value.
0b - Disabled
1b - Enabled
20 MMC Receive Pause Packet Counter Interrupt Mask
RXPAUSPIM Indicates the status of the MMC receive pause packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the rxpausepackets counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled
19 MMC Receive Out-Of-Range Error Packet Counter Interrupt Mask
RXORANGEPI Indicates the status of the MMC receive out-of-range error packet counter interrupt mask.
M
Writing 1 to this field masks the interrupt when the rxoutofrangetype counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled
18 MMC Receive Length Error Packet Counter Interrupt Mask
RXLENERPIM Indicates the status of the MMC receive length error packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the rxlengtherror counter reaches half of its maximum value
or the maximum value.
Table continues on the next page...

---

*Page 2798*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled
17 MMC Receive Unicast Good Packet Counter Interrupt Mask
RXUCGPIM Indicates the status of the MMC receive unicast good packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the rxunicastpackets_g counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled
16 MMC Receive 1024 To Maximum Octet Good Bad Packet Counter Interrupt Mask
RX1024TMAXO Indicates the status of the MMC receive 1024 to maximum octet good bad packet counter interrupt mask.
CTGBPIM
Writing 1 to this field masks the interrupt when the rx1024tomaxoctets_gb counter reaches half of its
maximum value or the maximum value.
0b - Disabled
1b - Enabled
15 MMC Receive 512 To 1023 Octet Good Bad Packet Counter Interrupt Mask
RX512T1023O Indicates the status of the MMC receive 512 to 1023 octet good bad packet counter interrupt mask.
CTGBPIM
Writing 1 to this field masks the interrupt when the rx512to1023octets_gb counter reaches half of its
maximum value or the maximum value.
0b - Disabled
1b - Enabled
14 MMC Receive 256 To 511 Octet Good Bad Packet Counter Interrupt Mask
RX256T511OC Indicates the status of the MMC receive 256 to 511 octet good bad packet counter interrupt mask.
TGBPIM
Writing 1 to this field masks the interrupt when the rx256to511octets_gb counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled
13 MMC Receive 128 To 255 Octet Good Bad Packet Counter Interrupt Mask
RX128T255OC Indicates the status of the MMC receive 128 to 255 octet good bad packet counter interrupt mask.
TGBPIM
Writing 1 to this field masks the interrupt when the rx128to255octets_gb counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled
12 MMC Receive 65 To 127 Octet Good Bad Packet Counter Interrupt Mask
Table continues on the next page...

---

*Page 2799*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
RX65T127OCT Indicates the status of the MMC receive 65 to 127 octet good bad packet counter interrupt mask.
GBPIM
Writing 1 to this field masks the interrupt when the rx65to127octets_gb counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled
11 MMC Receive 64-Octet Good Bad Packet Counter Interrupt Mask
RX64OCTGBPI Indicates the status of the MMC receive 64-octet good bad packet counter interrupt mask.
M
Writing 1 to this field masks the interrupt when the rx64octets_gb counter reaches half of its maximum value
or the maximum value.
0b - Disabled
1b - Enabled
10 MMC Receive Oversize Good Packet Counter Interrupt Mask
RXOSIZEGPIM Indicates the status of the MMC receive oversize good packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the rxoversize_g counter reaches half of its maximum value
or the maximum value.
0b - Disabled
1b - Enabled
9 MMC Receive Undersize Good Packet Counter Interrupt Mask
RXUSIZEGPIM Indicates the status of the MMC receive undersize good packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the rxundersize_g counter reaches half of its maximum value
or the maximum value.
0b - Disabled
1b - Enabled
8 MMC Receive Jabber Error Packet Counter Interrupt Mask
RXJABERPIM Indicates the status of the MMC receive jabber error packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the rxjabbererror counter reaches half of its maximum value
or the maximum value.
0b - Disabled
1b - Enabled
7 MMC Receive Runt Packet Counter Interrupt Mask
RXRUNTPIM Indicates the status of the MMC receive runt packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the rxrunterror counter reaches half of its maximum value or
the maximum value.
Table continues on the next page...

---

*Page 2800*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled
6 MMC Receive Alignment Error Packet Counter Interrupt Mask
RXALGNERPI Indicates the status of the MMC receive alignment error packet counter interrupt mask.
M
Writing 1 to this field masks the interrupt when the rxalignmenterror counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled
5 MMC Receive CRC Error Packet Counter Interrupt Mask
RXCRCERPIM Indicates the status of the MMC receive CRC error packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the rxcrcerror counter reaches half of its maximum value or
the maximum value.
0b - Disabled
1b - Enabled
4 MMC Receive Multicast Good Packet Counter Interrupt Mask
RXMCGPIM Indicates the status of the MMC receive multicast good packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the rxmulticastpackets_g counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled
3 MMC Receive Broadcast Good Packet Counter Interrupt Mask
RXBCGPIM Indicates the status of the MMC receive broadcast good packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the rxbroadcastpackets_g counter reaches half of its
maximum value or the maximum value.
0b - Disabled
1b - Enabled
2 MMC Receive Good Octet Counter Interrupt Mask
RXGOCTIM Indicates the status of the MMC receive good octet counter interrupt mask.
Writing 1 to this field masks the interrupt when the rxoctetcount_g counter reaches half of its maximum value
or the maximum value.
0b - Disabled
1b - Enabled
1 MMC Receive Good Bad Octet Counter Interrupt Mask
Table continues on the next page...

---

*Page 2801*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
RXGBOCTIM Indicates the status of the MMC receive good bad octet counter interrupt mask.
Writing 1 to this field masks the interrupt when the rxoctetcount_gb counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled
0 MMC Receive Good Bad Packet Counter Interrupt Mask
RXGBPKTIM Indicates the status of the MMC receive good bad packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the rxpacketcount_gb counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled

#### 72.17.61 MMC Transmit Interrupt Mask (MMC_Tx_Interrupt_Mask)

Offset
Register Offset
MMC_Tx_Interrupt_Mask 710h
Function
Maintains the masks for interrupts that are generated when the transmit statistic counters reach half of their maximum values
or their maximum values.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv Reserv TXOSI TXVLA TXPA TXEX TXGP TXGO TXCA TXEX TXLAT TXDE
Reserved
ed ed ZE ... NG ... USP ... DEF ... KTIM CTIM RER ... COL ... CO ... FPIM
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
TXMC TXSC TXUFL TXBC TXMC TXUC TX102 TX512 TX256 TX128 TX65T TX64O TXMC TXBC TXGB TXGB
OLG ... OLG ... OW ... GBP ... GBP ... GBP ... 4T ... T1 ... T5 ... T2 ... 12 ... CT ... GPIM GPIM PKT ... OCT ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2802*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-28 Reserved
—
27 Reserved
—
26 Reserved
—
25 MMC Transmit Oversize Good Packet Counter Interrupt Mask
TXOSIZEGPIM Indicates the status of the MMC transmit oversize good packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the txoversize_g counter reaches half of its maximum value
or the maximum value.
0b - Disabled
1b - Enabled
24 MMC Transmit VLAN Good Packet Counter Interrupt Mask
TXVLANGPIM Indicates the status of the MMC transmit VLAN good packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the txvlanpackets_g counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled
23 MMC Transmit Pause Packet Counter Interrupt Mask
TXPAUSPIM Indicates the status of the MMC transmit pause packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the txpausepackets counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled
22 MMC Transmit Excessive Deferral Packet Counter Interrupt Mask
TXEXDEFPIM Indicates the status of the MMC transmit excessive deferral packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the txexcessdef counter reaches half of its maximum value
or the maximum value.
0b - Disabled
1b - Enabled
21 MMC Transmit Good Packet Counter Interrupt Mask
TXGPKTIM Indicates the status of the MMC transmit good packet counter interrupt mask.
Table continues on the next page...

---

*Page 2803*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
Writing 1 to this field masks the interrupt when the txpacketcount_g counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled
20 MMC Transmit Good Octet Counter Interrupt Mask
TXGOCTIM Indicates the status of the MMC transmit good octet counter interrupt mask.
Writing 1 to this field masks the interrupt when the txoctetcount_g counter reaches half of its maximum value
or the maximum value.
0b - Disabled
1b - Enabled
19 MMC Transmit Carrier Error Packet Counter Interrupt Mask
TXCARERPIM Indicates the status of the MMC transmit carrier error packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the txcarriererror counter reaches half of its maximum value
or the maximum value.
0b - Disabled
1b - Enabled
18 MMC Transmit Excessive Collision Packet Counter Interrupt Mask
TXEXCOLPIM Indicates the status of the MMC transmit excessive collision packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the txexcesscol counter reaches half of its maximum value
or the maximum value.
0b - Disabled
1b - Enabled
17 MMC Transmit Late Collision Packet Counter Interrupt Mask
TXLATCOLPIM Indicates the status of the MMC transmit late collision packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the txlatecol counter reaches half of its maximum value or
the maximum value.
0b - Disabled
1b - Enabled
16 MMC Transmit Deferred Packet Counter Interrupt Mask
TXDEFPIM Indicates the status of the MMC transmit deferred packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the txdeferred counter reaches half of the maximum value
or the maximum value.
0b - Disabled
1b - Enabled
Table continues on the next page...

---

*Page 2804*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
15 MMC Transmit Multiple Collision Good Packet Counter Interrupt Mask
TXMCOLGPIM Indicates the status of the MMC transmit multiple collision good packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the txmulticol_g counter reaches half of its maximum value
or the maximum value.
0b - Disabled
1b - Enabled
14 MMC Transmit Single Collision Good Packet Counter Interrupt Mask
TXSCOLGPIM Indicates the status of the MMC transmit single collision good packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the txsinglecol_g counter reaches half of its maximum value
or the maximum value.
0b - Disabled
1b - Enabled
13 MMC Transmit Underflow Error Packet Counter Interrupt Mask
TXUFLOWERPI Indicates the status of the MMC transmit underflow error packet counter interrupt mask.
M
Writing 1 to this field masks the interrupt when the txunderflowerror counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled
12 MMC Transmit Broadcast Good Bad Packet Counter Interrupt Mask
TXBCGBPIM Indicates the status of the MMC transmit broadcast good bad packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the txbroadcastpackets_gb counter reaches half of its
maximum value or the maximum value.
0b - Disabled
1b - Enabled
11 MMC Transmit Multicast Good Bad Packet Counter Interrupt Mask
TXMCGBPIM Indicates the status of the MMC transmit multicast good bad packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the txmulticastpackets_gb counter reaches half of its
maximum value or the maximum value.
0b - Disabled
1b - Enabled
10 MMC Transmit Unicast Good Bad Packet Counter Interrupt Mask
TXUCGBPIM Indicates the status of the MMC transmit unicast good bad packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the txunicastpackets_gb counter reaches half of its maximum
value or the maximum value.
Table continues on the next page...

---

*Page 2805*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
0b - Disabled
1b - Enabled
9 MMC Transmit 1024 To Maximum Octet Good Bad Packet Counter Interrupt Mask
TX1024TMAXO Indicates the status of the MMC transmit 1024 to maximum octet good bad packet counter interrupt mask.
CTGBPIM
Writing 1 to this field masks the interrupt when the tx1024tomaxoctets_gb counter reaches half of its
maximum value or the maximum value.
0b - Disabled
1b - Enabled
8 MMC Transmit 512 To 1023 Octet Good Bad Packet Counter Interrupt Mask
TX512T1023O Indicates the status of the MMC transmit 512 to 1023 octet good bad packet counter interrupt mask.
CTGBPIM
Writing 1 to this field masks the interrupt when the tx512to1023octets_gb counter reaches half of its
maximum value or the maximum value.
0b - Disabled
1b - Enabled
7 MMC Transmit 256 To 511 Octet Good Bad Packet Counter Interrupt Mask
TX256T511OC Indicates the status of the MMC transmit 256 to 511 octet good bad packet counter interrupt mask.
TGBPIM
Writing 1 to this field masks the interrupt when the tx256to511octets_gb counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled
6 MMC Transmit 128 To 255 Octet Good Bad Packet Counter Interrupt Mask
TX128T255OC Indicates the status of the MMC transmit 128 to 255 octet good bad packet counter interrupt mask.
TGBPIM
Writing 1 to this field masks the interrupt when the tx128to255octets_gb counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled
5 MMC Transmit 65 To 127 Octet Good Bad Packet Counter Interrupt Mask
TX65T127OCT Indicates the status of the MMC transmit 65 to 127 octet good bad packet counter interrupt mask.
GBPIM
Writing 1 to this field masks the interrupt when the tx65to127octets_gb counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled
4 MMC Transmit 64-Octet Good Bad Packet Counter Interrupt Mask
Table continues on the next page...

---

*Page 2806*

Ethernet Media Access Controller (EMAC)
Table continued from the previous page...
Field Function
TX64OCTGBPI Indicates the status of the MMC transmit 64-octet good bad packet counter interrupt mask.
M
Writing 1 to this field masks the interrupt when the tx64octets_gb counter reaches half of its maximum value
or the maximum value.
0b - Disabled
1b - Enabled
3 MMC Transmit Multicast Good Packet Counter Interrupt Mask
TXMCGPIM Indicates the status of the MMC transmit multicast good packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the txmulticastpackets_g counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled
2 MMC Transmit Broadcast Good Packet Counter Interrupt Mask
TXBCGPIM Indicates the status of the MMC transmit broadcast good packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the txbroadcastpackets_g counter reaches half of its
maximum value or the maximum value.
0b - Disabled
1b - Enabled
1 MMC Transmit Good Bad Packet Counter Interrupt Mask
TXGBPKTIM Indicates the status of the MMC transmit good bad packet counter interrupt mask.
Writing 1 to this field masks the interrupt when the txpacketcount_gb counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled
0 MMC Transmit Good Bad Octet Counter Interrupt Mask
TXGBOCTIM Indicates the status of the MMC transmit good bad octet counter interrupt mask.
Writing 1 to this field masks the interrupt when the txoctetcount_gb counter reaches half of its maximum
value or the maximum value.
0b - Disabled
1b - Enabled

---

*Page 2807*

Ethernet Media Access Controller (EMAC)

#### 72.17.62 Transmit Octet Count Good Bad (Tx_Octet_Count_Good_Bad)

Offset
Register Offset
Tx_Octet_Count_Good_ 714h
Bad
Function
Provides the number of bytes that the module transmitted, exclusive of preamble and retried bytes, in good and bad packets.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXOCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXOCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit Octet Count Good Bad
TXOCTGB Indicates the number of bytes transmitted, exclusive of preamble and retried bytes, in good and bad packets.

#### 72.17.63 Transmit Packet Count Good Bad (Tx_Packet_Count_Good_Bad)

Offset
Register Offset
Tx_Packet_Count_Good 718h
_Bad
Function
Provides the number of good and bad packets that the module transmitted, exclusive of retried packets.

---

*Page 2808*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXPKTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXPKTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit Packet Count Good Bad
TXPKTGB Indicates the number of good and bad packets transmitted, exclusive of retried packets.

#### 72.17.64 Transmit Broadcast Packets Good (Tx_Broadcast_Packets_Good)

Offset
Register Offset
Tx_Broadcast_Packets_ 71Ch
Good
Function
Provides the number of good broadcast packets that the module transmitted.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXBCASTG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXBCASTG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2809*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Transmit Broadcast Packets Good
TXBCASTG Indicates the number of good broadcast packets transmitted.

#### 72.17.65 Transmit Multicast Packets Good (Tx_Multicast_Packets_Good)

Offset
Register Offset
Tx_Multicast_Packets_G 720h
ood
Function
Provides the number of good multicast packets that the module transmitted.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXMCASTG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXMCASTG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit Multicast Packets Good
TXMCASTG Indicates the number of good multicast packets transmitted.

#### 72.17.66 Transmit 64-Octet Packets Good Bad (Tx_64Octets_Packets_Good_Bad)

Offset
Register Offset
Tx_64Octets_Packets_G 724h
ood_Bad

---

*Page 2810*

Ethernet Media Access Controller (EMAC)
Function
Provides the number of 64-byte good and bad packets that the module transmitted, exclusive of preamble and retried packets.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TX64OCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TX64OCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit 64-Octet Packets Good Bad
TX64OCTGB Indicates the number of transmitted 64-byte good and bad packets, exclusive of preamble and
retried packets.

#### 72.17.67 Transmit 65 To 127 Octet Packets Good Bad (Tx_65To127Octets_Packets_Good_Bad)

Offset
Register Offset
Tx_65To127Octets_Pack 728h
ets_Good_Bad
Function
Provides the number of good and bad packets, having length between 65 and 127 (inclusive) bytes, that the module
transmitted, exclusive of preamble and retried packets.

---

*Page 2811*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TX65_127OCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TX65_127OCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit 65 To 127 Octet Packets Good Bad
TX65_127OCT Indicates the number of good and bad packets transmitted, having length between 65 and 127 (inclusive)
GB bytes, exclusive of preamble and retried packets.

#### 72.17.68 Transmit 128 To 255 Octet Packets Good Bad (Tx_128To255Octets_Packets_Good_Bad)

Offset
Register Offset
Tx_128To255Octets_Pac 72Ch
kets_Good_Bad
Function
Provides the number of good and bad packets that the module transmitted, having length between 128 to 255 (inclusive)
bytes, exclusive of preamble and retried packets.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TX128_255OCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TX128_255OCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2812*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Transmit 128 To 255 Octet Packets Good Bad
TX128_255OCT Indicates the number of good and bad packets transmitted, having length between 128 and 255 (inclusive)
GB bytes, exclusive of preamble and retried packets.

#### 72.17.69 Transmit 256 To 511 Octet Packets Good Bad (Tx_256To511Octets_Packets_Good_Bad)

Offset
Register Offset
Tx_256To511Octets_Pac 730h
kets_Good_Bad
Function
Provides the number of good and bad packets that the module transmitted, having length between 256 to 511 (inclusive)
bytes, exclusive of preamble and retried packets.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TX256_511OCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TX256_511OCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit 256 To 511 Octet Packets Good Bad
TX256_511OCT Indicates the number of good and bad packets transmitted, having length between 256 and 511 (inclusive)
GB bytes, exclusive of preamble and retried packets.

---

*Page 2813*

Ethernet Media Access Controller (EMAC)

#### 72.17.70 Transmit 512 To 1023 Octet Packets Good Bad (Tx_512To1023Octets_Packets_Good_Bad)

Offset
Register Offset
Tx_512To1023Octets_P 734h
ackets_Good_Bad
Function
Provides the number of good and bad packets that the module transmitted, having length between 512 to 1023 (inclusive)
bytes, exclusive of preamble and retried packets.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TX512_1023OCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TX512_1023OCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit 512 To 1023 Octet Packets Good Bad
TX512_1023OC Indicates the number of good and bad packets transmitted, having length between 512 and 1023 (inclusive)
TGB bytes, exclusive of preamble and retried packets.

#### 72.17.71 Transmit 1024 To Max Octet Packets Good Bad

#### (Tx_1024ToMaxOctets_Packets_Good_Bad)

Offset
Register Offset
Tx_1024ToMaxOctets_P 738h
ackets_Good_Bad
Function
Provides the number of good and bad packets that the module transmitted, having a length between 1024 bytes (inclusive)
and the maximum size, exclusive of preamble and retried packets.

---

*Page 2814*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TX1024_MAXOCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TX1024_MAXOCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit 1024 To Max Octet Packets Good Bad
TX1024_MAXO Indicates the number of good and bad packets transmitted, having a length between 1024 bytes (inclusive)
CTGB and the maximum size, exclusive of preamble and retried packets.

#### 72.17.72 Transmit Unicast Packets Good Bad (Tx_Unicast_Packets_Good_Bad)

Offset
Register Offset
Tx_Unicast_Packets_Go 73Ch
od_Bad
Function
Provides the number of good and bad unicast packets that the module transmitted.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXUCASTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXUCASTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2815*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Transmit Unicast Packets Good Bad
TXUCASTGB Indicates the number of good and bad unicast packets transmitted.

#### 72.17.73 Transmit Multicast Packets Good Bad (Tx_Multicast_Packets_Good_Bad)

Offset
Register Offset
Tx_Multicast_Packets_G 740h
ood_Bad
Function
Provides the number of good and bad multicast packets that the module transmitted.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXMCASTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXMCASTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit Multicast Packets Good Bad
TXMCASTGB Indicates the number of good and bad multicast packets transmitted.

#### 72.17.74 Transmit Broadcast Packets Good Bad (Tx_Broadcast_Packets_Good_Bad)

Offset
Register Offset
Tx_Broadcast_Packets_ 744h
Good_Bad

---

*Page 2816*

Ethernet Media Access Controller (EMAC)
Function
Provides the number of good and bad broadcast packets that the module transmitted.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXBCASTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXBCASTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit Broadcast Packets Good Bad
TXBCASTGB Indicates the number of good and bad broadcast packets transmitted.

#### 72.17.75 Transmit Underflow Error Packets (Tx_Underflow_Error_Packets)

Offset
Register Offset
Tx_Underflow_Error_Pac 748h
kets
Function
Provides the number of packets that the module aborted because of a packet underflow error.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXUNDRFLW
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXUNDRFLW
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2817*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Transmit Underflow Error Packets
TXUNDRFLW Indicates the number of packets aborted because of a packet underflow error.

#### 72.17.76 Transmit Single Collision Good Packets (Tx_Single_Collision_Good_Packets)

Offset
Register Offset
Tx_Single_Collision_Goo 74Ch
d_Packets
Function
Provides the number of packets that the module successfully transmitted after a single collision in Half-Duplex mode.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXSNGLCOLG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXSNGLCOLG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit Single Collision Good Packets
TXSNGLCOLG Indicates the number of successfully transmitted packets after a single collision in Half-Duplex mode.

#### 72.17.77 Transmit Multiple Collision Good Packets (Tx_Multiple_Collision_Good_Packets)

Offset
Register Offset
Tx_Multiple_Collision_Go 750h
od_Packets

---

*Page 2818*

Ethernet Media Access Controller (EMAC)
Function
Provides the number of packets that the module successfully transmitted after multiple collisions in Half-Duplex mode.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXMULTCOLG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXMULTCOLG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit Multiple Collision Good Packets
TXMULTCOLG Indicates the number of successfully transmitted packets after multiple collisions in Half-Duplex mode.

#### 72.17.78 Transmit Deferred Packets (Tx_Deferred_Packets)

Offset
Register Offset
Tx_Deferred_Packets 754h
Function
Provides the number of packets that the module successfully transmitted after a deferral in Half-Duplex mode.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXDEFRD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXDEFRD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2819*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Transmit Deferred Packets
TXDEFRD Indicates the number of successfully transmitted packets after a deferral in Half-Duplex mode.

#### 72.17.79 Transmit Late Collision Packets (Tx_Late_Collision_Packets)

Offset
Register Offset
Tx_Late_Collision_Packe 758h
ts
Function
Provides the number of packets that the module aborted because of a late collision error.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXLATECOL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXLATECOL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit Late Collision Packets
TXLATECOL Indicates the number of packets aborted because of a late collision error.

#### 72.17.80 Transmit Excessive Collision Packets (Tx_Excessive_Collision_Packets)

Offset
Register Offset
Tx_Excessive_Collision_ 75Ch
Packets

---

*Page 2820*

Ethernet Media Access Controller (EMAC)
Function
Provides the number of packets that the module aborted because of excessive (16) collision errors.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXEXSCOL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXEXSCOL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit Excessive Collision Packets
TXEXSCOL Indicates the number of packets aborted because of excessive (16) collision errors.

#### 72.17.81 Transmit Carrier Error Packets (Tx_Carrier_Error_Packets)

Offset
Register Offset
Tx_Carrier_Error_Packet 760h
s
Function
Provides the number of packets that the module aborted because of a carrier sense error (such as no carrier or loss of carrier).
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXCARR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXCARR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2821*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Transmit Carrier Error Packets
TXCARR Indicates the number of packets aborted because of a carrier sense error (such as no carrier or loss
of carrier).

#### 72.17.82 Transmit Octet Count Good (Tx_Octet_Count_Good)

Offset
Register Offset
Tx_Octet_Count_Good 764h
Function
Provides the number of bytes that the module transmitted, exclusive of preamble, only in good packets.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXOCTG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXOCTG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit Octet Count Good
TXOCTG Indicates the number of bytes transmitted, exclusive of preamble, only in good packets.

#### 72.17.83 Transmit Packet Count Good (Tx_Packet_Count_Good)

Offset
Register Offset
Tx_Packet_Count_Good 768h

---

*Page 2822*

Ethernet Media Access Controller (EMAC)
Function
Provides the number of good packets that the module transmitted.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXPKTG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXPKTG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit Packet Count Good
TXPKTG Indicates the number of good packets transmitted.

#### 72.17.84 Transmit Excessive Deferral Error (Tx_Excessive_Deferral_Error)

Offset
Register Offset
Tx_Excessive_Deferral_ 76Ch
Error
Function
Provides the number of packets that the module aborted because of an excessive deferral error (deferred for more than two
max-sized packet times).
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXEXSDEF
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXEXSDEF
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2823*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Transmit Excessive Deferral Error
TXEXSDEF Indicates the number of packets aborted because of excessive deferral error (deferred for more than two
max-sized packet times).

#### 72.17.85 Transmit Pause Packets (Tx_Pause_Packets)

Offset
Register Offset
Tx_Pause_Packets 770h
Function
Provides the number of good pause packets that the module transmitted.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXPAUSE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXPAUSE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit Pause Packets
TXPAUSE Indicates the number of good pause packets transmitted.

#### 72.17.86 Transmit VLAN Packets Good (Tx_VLAN_Packets_Good)

Offset
Register Offset
Tx_VLAN_Packets_Good 774h

---

*Page 2824*

Ethernet Media Access Controller (EMAC)
Function
Provides the number of good VLAN packets that the module transmitted.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXVLANG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXVLANG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Transmit VLAN Packets Good
TXVLANG Provides the number of good VLAN packets transmitted.

#### 72.17.87 Transmit O Size Packets Good (Tx_OSize_Packets_Good)

Offset
Register Offset
Tx_OSize_Packets_Goo 778h
d
Function
Provides the number of packets that the module transmitted without errors, and having a length greater than the maximum
size, which is 1,518 or 1,522 bytes for VLAN-tagged packets. This size is 2000 bytes if MAC_Configuration[S2KP] = 1.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R TXOSIZG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TXOSIZG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2825*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Transmit O Size Packets Good
TXOSIZG Indicates the number of packets transmitted without errors, and having a length greater than the
maximum size, which is 1,518 or 1,522 bytes for VLAN-tagged packets. This size is 2000 bytes if
MAC_Configuration[S2KP] = 1.

#### 72.17.88 Receive Packets Count Good Bad (Rx_Packets_Count_Good_Bad)

Offset
Register Offset
Rx_Packets_Count_Goo 780h
d_Bad
Function
Provides the number of good and bad packets that the module received.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXPKTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXPKTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Receive Packets Count Good Bad
RXPKTGB Indicates the number of good and bad packets received.

---

*Page 2826*

Ethernet Media Access Controller (EMAC)

#### 72.17.89 Receive Octet Count Good Bad (Rx_Octet_Count_Good_Bad)

Offset
Register Offset
Rx_Octet_Count_Good_ 784h
Bad
Function
Provides the number of bytes that DWC_ther_qos received, exclusive of preamble, in good and bad packets.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXOCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXOCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Receive Octet Count Good Bad
RXOCTGB Indicates the number of bytes received, exclusive of preamble, in good and bad packets.

#### 72.17.90 Receive Octet Count Good (Rx_Octet_Count_Good)

Offset
Register Offset
Rx_Octet_Count_Good 788h
Function
Provides the number of bytes that the module received, exclusive of preamble, only in good packets.

---

*Page 2827*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXOCTG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXOCTG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Receive Octet Count Good
RXOCTG Indicates the number of bytes received, exclusive of preamble, only in good packets.

#### 72.17.91 Receive Broadcast Packets Good (Rx_Broadcast_Packets_Good)

Offset
Register Offset
Rx_Broadcast_Packets_ 78Ch
Good
Function
Provides the number of good broadcast packets that the module received.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXBCASTG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXBCASTG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2828*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Receive Broadcast Packets Good
RXBCASTG Indicates the number of good broadcast packets received.

#### 72.17.92 Receive Multicast Packets Good (Rx_Multicast_Packets_Good)

Offset
Register Offset
Rx_Multicast_Packets_G 790h
ood
Function
Provides the number of good multicast packets that the module received.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXMCASTG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXMCASTG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Receive Multicast Packets Good
RXMCASTG Indicates the number of good multicast packets received.

#### 72.17.93 Receive CRC Error Packets (Rx_CRC_Error_Packets)

Offset
Register Offset
Rx_CRC_Error_Packets 794h

---

*Page 2829*

Ethernet Media Access Controller (EMAC)
Function
Provides the number of packets that the module received with a CRC error.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXCRCERR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXCRCERR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Receive CRC Error Packets
RXCRCERR Indicates the number of packets received with a CRC error.

#### 72.17.94 Receive Alignment Error Packets (Rx_Alignment_Error_Packets)

Offset
Register Offset
Rx_Alignment_Error_Pac 798h
kets
Function
Provides the number of packets that the module received with an alignment (dribble) error. It is valid only in 10/100 mode.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXALGNERR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXALGNERR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2830*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Receive Alignment Error Packets
RXALGNERR Indicates the number of packets received with alignment (dribble) error. It is valid only in 10/100 mode.

#### 72.17.95 Receive Runt Error Packets (Rx_Runt_Error_Packets)

Offset
Register Offset
Rx_Runt_Error_Packets 79Ch
Function
Provides the number of runt packets, which have a length less than 64 bytes and a CRC error, that the module received.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXRUNTERR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXRUNTERR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Receive Runt Error Packets
RXRUNTERR Indicates the number of runt packets received.

#### 72.17.96 Receive Jabber Error Packets (Rx_Jabber_Error_Packets)

Offset
Register Offset
Rx_Jabber_Error_Packet 7A0h
s

---

*Page 2831*

Ethernet Media Access Controller (EMAC)
Function
Provides the number of giant packets that the module received, having a length (including CRC) greater than 1,518 bytes
(1,522 bytes for VLAN-tagged packets), and with a CRC error. If Jumbo Packet mode is enabled, packets of length greater
than 9,018 bytes (9,022 bytes for VLAN-tagged packets) are considered as giant packets.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXJABERR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXJABERR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Receive Jabber Error Packets
RXJABERR Indicates the number of giant packets received, having a length (including CRC) greater than 1,518 bytes
(1,522 bytes for VLAN-tagged packets), and with a CRC error. If Jumbo Packet mode is enabled, packets
of length greater than 9,018 bytes (9,022 bytes for VLAN-tagged packets) are considered as giant packets.

#### 72.17.97 Receive Undersize Packets Good (Rx_Undersize_Packets_Good)

Offset
Register Offset
Rx_Undersize_Packets_ 7A4h
Good
Function
Provides the number of packets that the module received, having a length less than 64 bytes, but without any errors.

---

*Page 2832*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXUNDERSZG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXUNDERSZG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Receive Undersize Packets Good
RXUNDERSZG Indicates the number of packets received, having a length less than 64 bytes, but without any errors.

#### 72.17.98 Receive Oversize Packets Good (Rx_Oversize_Packets_Good)

Offset
Register Offset
Rx_Oversize_Packets_G 7A8h
ood
Function
Provides the number of packets that the module received without any errors, having a length greater than the maximum size
(1,518 bytes or 1,522 bytes for VLAN-tagged packets; 2000 bytes if MAC_Configuration[S2KP] = 1).
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXOVERSZG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXOVERSZG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2833*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Receive Oversize Packets Good
RXOVERSZG Indicates the number of packets received without errors, with a length greater than the maximum size (1,518
bytes or 1,522 bytes for VLAN-tagged packets; 2000 bytes if MAC_Configuration[S2KP] = 1).

#### 72.17.99 Receive 64 Octets Packets Good Bad (Rx_64Octets_Packets_Good_Bad)

Offset
Register Offset
Rx_64Octets_Packets_G 7ACh
ood_Bad
Function
Provides the number of 64-byte good and bad packets that the module received, exclusive of the preamble.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RX64OCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RX64OCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Receive 64 Octets Packets Good Bad
RX64OCTGB Provides the number of 64-byte good and bad packets that the module received, exclusive of the preamble.

---

*Page 2834*

Ethernet Media Access Controller (EMAC)

#### 72.17.100 Receive 65-127 Octets Packets Good Bad (Rx_65To127Octets_Packets_Good_Bad)

Offset
Register Offset
Rx_65To127Octets_Pac 7B0h
kets_Good_Bad
Function
Provides the number of good and bad packets that the module received, ranging between 65 and 127 (inclusive) bytes,
exclusive of the preamble.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RX65_127OCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RX65_127OCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Receive 65-127 Octets Packets Good Bad
RX65_127OCT Indicates the number of good and bad packets received, ranging between 65 and 127 (inclusive) bytes,
GB exclusive of the preamble.

#### 72.17.101 Receive 128-255 Octets Packets Good Bad (Rx_128To255Octets_Packets_Good_Bad)

Offset
Register Offset
Rx_128To255Octets_Pa 7B4h
ckets_Good_Bad
Function
Provides the number of good and bad packets that the module received, with packet length between 128 and 255 (inclusive)
bytes, exclusive of the preamble.

---

*Page 2835*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RX128_255OCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RX128_255OCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Receive 128-255 Octets Packets Good Bad
RX128_255OC Indicates the number of good and bad packets received, with packet length between 128 and 255 (inclusive)
TGB bytes, exclusive of the preamble.

#### 72.17.102 Receive 256-511 Octets Packets Good Bad (Rx_256To511Octets_Packets_Good_Bad)

Offset
Register Offset
Rx_256To511Octets_Pa 7B8h
ckets_Good_Bad
Function
Provides the number of good and bad packets that the module received, having a packet length between 256 and 511
(inclusive) bytes, exclusive of the preamble.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RX256_511OCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RX256_511OCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2836*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Receive 256-511 Octets Packets Good Bad
RX256_511OC Indicates the number of good and bad packets received, having a packet length between 256 and 511
TGB (inclusive) bytes, exclusive of the preamble.

#### 72.17.103 Receive 512-1023 Octets Packets Good Bad (Rx_512To1023Octets_Packets_Good_Bad)

Offset
Register Offset
Rx_512To1023Octets_P 7BCh
ackets_Good_Bad
Function
Provides the number of good and bad packets that the module received, having a packet length between 512 and 1023
(inclusive) bytes, exclusive of the preamble.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RX512_1023OCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RX512_1023OCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Receive 512-1023 Octets Packets Good Bad
RX512_1023O Indicates the number of good and bad packets received, having a packet length between 512 and 1023
CTGB (inclusive) bytes, exclusive of the preamble.

---

*Page 2837*

Ethernet Media Access Controller (EMAC)

#### 72.17.104 Receive 1024 To Max Octets Good Bad (Rx_1024ToMaxOctets_Packets_Good_Bad)

Offset
Register Offset
Rx_1024ToMaxOctets_P 7C0h
ackets_Good_Bad
Function
Provides the number of good and bad packets that the module received, having an inclusive packet length between 1024
bytes and the maximum byte size, exclusive of the preamble.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RX1024_MAXOCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RX1024_MAXOCTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Receive 1024-Max Octets Good Bad
RX1024_MAXO Indicates the number of good and bad packets received with length between 1024 and the maximum size
CTGB (inclusive) bytes, exclusive of the preamble.

#### 72.17.105 Receive Unicast Packets Good (Rx_Unicast_Packets_Good)

Offset
Register Offset
Rx_Unicast_Packets_Go 7C4h
od
Function
Provides the number of good unicast packets that the module received.

---

*Page 2838*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXUCASTG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXUCASTG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Receive Unicast Packets Good
RXUCASTG Indicates the number of good unicast packets received.

#### 72.17.106 Receive Length Error Packets (Rx_Length_Error_Packets)

Offset
Register Offset
Rx_Length_Error_Packet 7C8h
s
Function
Provides the number of packets that the module received with a length error (the Length/Type (LT) field of the RDES3 normal
descriptor not equal to the packet size) for all the packets with a valid length field. For more information on the LT field, see the
"RDES3 normal descriptor (write-back format)" section.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXLENERR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXLENERR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2839*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Receive Length Error Packets
RXLENERR Indicates the number of packets received with a length error (the Length/Type (LT) field of the RDES3
normal descriptor not equal to the packet size) for all the packets with a valid length field. For more
information on the LT field, see the "RDES3 normal descriptor (write-back format)" section.

#### 72.17.107 Receive Out of Range Type Packet (Rx_Out_Of_Range_Type_Packets)

Offset
Register Offset
Rx_Out_Of_Range_Type 7CCh
_Packets
Function
Provides the number of packets that the module received, with the length field not equal to the valid packet size (greater than
1,500 but less than 1,536).
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXOUTOFRNG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXOUTOFRNG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Receive Out of Range Type Packet
RXOUTOFRNG Indicates the number of packets received with length field not equal to the valid packet size (greater than
1,500 but less than 1,536).

---

*Page 2840*

Ethernet Media Access Controller (EMAC)

#### 72.17.108 Receive Pause Packets (Rx_Pause_Packets)

Offset
Register Offset
Rx_Pause_Packets 7D0h
Function
Provides the number of good and valid pause packets that the module received.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXPAUSEPKT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXPAUSEPKT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Receive Pause Packets
RXPAUSEPKT Indicates the number of good and valid pause packets received.

#### 72.17.109 Receive FIFO Overflow Packets (Rx_FIFO_Overflow_Packets)

Offset
Register Offset
Rx_FIFO_Overflow_Pack 7D4h
ets
Function
Provides the number of missed received packets because of FIFO overflow in the module.

---

*Page 2841*

Ethernet Media Access Controller (EMAC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXFIFOOVFL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXFIFOOVFL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Receive FIFO Overflow Packets
RXFIFOOVFL Indicates the number of missed received packets because of FIFO overflow.

#### 72.17.110 Receive VLAN Packets Good Bad (Rx_VLAN_Packets_Good_Bad)

Offset
Register Offset
Rx_VLAN_Packets_Goo 7D8h
d_Bad
Function
Provides the number of good and bad VLAN packets that the module received.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R RXVLANPKTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R RXVLANPKTGB
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 2842*

Ethernet Media Access Controller (EMAC)
Fields
Field Function
31-0 Receive VLAN Packets Good Bad
RXVLANPKTG Indicates the number of good and bad VLAN packets received.
B

#### 72.17.111 Receive Watchdog Error Packets (Rx_Watchdog_Error_Packets)

Offset
Register Offset
Rx_Watchdog_Error_Pac 7DCh
kets
Function
Provide