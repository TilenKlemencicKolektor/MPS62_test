<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 51 -->

# Chapter 51

# Self-Test Control Unit (STCU2)

#### 51.1 Chip-specific STCU2 information

#### 51.1.1 Supported BIST sequences

This chip supports only STCU2 BIST sequences that NXP has validated for in-field testing usage. The detailed programming
sequences for supported configurations are available in a separate application note (contact your NXP sales representative for
this information).

#### 51.1.2 Self-test overview

STCU2 provides the overall control of both LBIST and MBIST, in serial or parallel, depending on the power, timing, or
coverage constraints.
NOTE
LBIST is not available for MCXE317 and MCXE315/MCXE316
The application software initiates the self-test sequence or procedure (please use whichever term is correct), and has provisions
to bypass the self-test configuration to directly move to normal Run mode.
There is a programmable BIST-sequence-execution watchdog timer that specifies the maximum time allowed for the execution
of a BIST sequence. In case the selected LBISTs or MBISTs are not complete during the assigned time, the current LBISTs or
MBISTs execution is interrupted and a failure is flagged into ERR_STAT[WDTOSW] and MBESWx or LBESW.
The STCU2 execution includes three phases:
• Software configuration. Load or program the test configuration parameters to be executed, depending on SIL target and
performance requirements. The self-test configuration parameters allow the self-test sequence to run with different times
depending on pattern numbers and types, achieving different levels of coverage.
• Start of BIST execution. This involves software register configuration in STCU2. The BIST completion is followed by a
functional reset.
• End of BIST execution. After BIST execution and functional reset are complete, the BIST results are made available and
you decide how to continue (trigger a new reset or start a safety application). The BIST execution completion is signalized
in an internal STCU2 register (LBESWn) bit which is not affected by the functional reset. The BIST test results are also not
affected by the functional reset.
The following figure shows a pictorial representation of two LBIST partitions having two and one MBIST groups, respectively.
STCU2 interacts with LBIST_CTRL for the respective partitions to configure and control the LBIST sequence and compare results.
It also interacts with MTR controller (MCT) for MBIST. Each LBIST partition has its corresponding controller whereas a single MCT
can cater to multiple MBIST groups.

---

*Page 1462*

Self-Test Control Unit (STCU2)
Logical partitions Other functional
blocks
MCT
MBIST C
MBIST A MBIST B
LBIST_CTRL LBIST_CTRL
STCU2
Figure 187. Self-test overview
NOTE
LBIST_CTRL is not available for MCXE317 and MCXE315/MCXE316.
Each LBIST partition also includes a block called Self-test GPR involving a set of registers that provide miscellaneous
configuration and parameter details for an LBIST operation.
When you trigger a self-test sequence, the chip becomes unavailable for software application during the self-test run.

#### 51.1.3 STCU2 LBIST/MBIST mapping

This topic covers STCU2 LBIST/MBIST mapping.
LBIST mapping
The below table lists the LBIST mapping of the chip. The NLBIST value is 1.
Table 233. LBIST mapping
BIST ID BIST instance Modules
(NLBIST) name
0 LBIST DMA:
• DMA controller
EIM
ERM
XRDC
• MDAC0
• MDAC1
• MDAC2
Table continues on the next page...

---

*Page 1463*

Self-Test Control Unit (STCU2)
Table 233. LBIST mapping (continued)
BIST ID BIST instance Modules
(NLBIST) name
• MDAC3
• MDAC4
• MDAC5
• MRC0
• MRC1
• MRC2
• PDAC0
• PDAC1
• PDAC2
CRC
FCCU
FOSU
PFLASH_CTL
PRAMC_0
PRAMC_1
NOTE
PRAMC_1 is not present in MCXE317, and MCXE315/MCXE316.
SWT_1
NOTE
SWT_1 is not present in MCXE317 and MCXE315/MCXE316.
CMU:
• CMU1
• CMU2
• CMU4
• CMU5
iAHB Gaskets:
• DMA_GSKT
• HSE_GSKT
• AIPS1_GSKT
• AIPS2_GSKT
• QSPI_GSKT
Table continues on the next page...

---

*Page 1464*

Self-Test Control Unit (STCU2)
Table 233. LBIST mapping (continued)
BIST ID BIST instance Modules
(NLBIST) name
NOTE
AIPS2_GSKT and QSPI_GSKT are not present in MCXE317 and
MCXE315/MCXE316.
• TCM_GSKT
• EMAC_GSKT
• EDC master checker gaskets (See block diagram in Introduction chapter for details)
• EDC slave checker gasket (See block diagram in Introduction chapter for details)
Crossbars
• AXBS
• DMA_AXBS
• HSE_AXBS
• PERIPHERAL_AXBS
• TCM_AXBS
MBIST mapping
The below tables lists the MBIST mapping of different variants of this chip. The NMCUT value is 12.
Table 234. MCXE31B MBIST mapping
BIST BIST name Memory Memory instance name
index (NMCUT) index
0 SYS0_RAMS 0 PRAM0 block 1
0 SYS0_RAMS 1 PRAM0 block 2
1 SYS1_RAMS 0 PRAM1 block 1
1 SYS1_RAMS 1 PRAM1 block 2
2 DMA_TCD_RAM 0 DMA TCD RAM
3 CM7_0_TOP 0 CM7_0 instruction cache data block 1
3 CM7_0_TOP 1 CM7_0 instruction cache data block 2
3 CM7_0_TOP 2 CM7_0 instruction cache tag block 1
3 CM7_0_TOP 3 CM7_0 instruction cache tag block 2
3 CM7_0_TOP 4 CM7_0 data cache data block 1
3 CM7_0_TOP 4 CM7_0 data cache data block 2
3 CM7_0_TOP 5 CM7_0 data cache data block 3
3 CM7_0_TOP 5 CM7_0 data cache data block 4
3 CM7_0_TOP 6 CM7_0 data cache data block 5
Table continues on the next page...

---

*Page 1465*

Self-Test Control Unit (STCU2)
Table 234. MCXE31B MBIST mapping (continued)
BIST BIST name Memory Memory instance name
index (NMCUT) index
3 CM7_0_TOP 6 CM7_0 data cache data block 6
3 CM7_0_TOP 7 CM7_0 data cache data block 7
3 CM7_0_TOP 7 CM7_0 data cache data block 8
3 CM7_0_TOP 8 CM7_0 data cache tag block 1
3 CM7_0_TOP 9 CM7_0 data cache tag block 2
3 CM7_0_TOP 10 CM7_0 data cache tag block 3
3 CM7_0_TOP 11 CM7_0 data cache tag block 4
3 CM7_0_TOP 12 CM7_0 instruction TCM (Tightly Coupled Memory)
3 CM7_0_TOP 13 CM7_0 data TCM block 1
3 CM7_0_TOP 13 CM7_0 data TCM block 2
4 CM7_1_TOP 0 CM7_1 instruction cache data block 1
4 CM7_1_TOP 1 CM7_1 instruction cache data block 2
4 CM7_1_TOP 2 CM7_1 instruction cache tag block 1
4 CM7_1_TOP 3 CM7_1 instruction cache tag block 2
4 CM7_1_TOP 4 CM7_1 data cache data block 1
4 CM7_1_TOP 4 CM7_1 data cache data block 2
4 CM7_1_TOP 5 CM7_1 data cache data block 3
4 CM7_1_TOP 5 CM7_1 data cache data block 4
4 CM7_1_TOP 6 CM7_1 data cache data block 5
4 CM7_1_TOP 6 CM7_1 data cache data block 6
4 CM7_1_TOP 7 CM7_1 data cache data block 7
4 CM7_1_TOP 7 CM7_1 data cache data block 8
4 CM7_1_TOP 8 CM7_0 data cache tag block 1
4 CM7_1_TOP 9 CM7_0 data cache tag block 2
4 CM7_1_TOP 10 CM7_0 data cache tag block 3
4 CM7_1_TOP 11 CM7_0 data cache tag block 4
4 CM7_1_TOP 12 CM7_0 instruction TCM
4 CM7_1_TOP 13 CM7_0 data TCM block 1
4 CM7_1_TOP 13 CM7_0 data TCM block 2
5 FLEX_CAN_RAMS 0 FLEXCAN0 MB memory
5 FLEX_CAN_RAMS 1 FLEXCAN1 MB memory
5 FLEX_CAN_RAMS 2 FLEXCAN2 MB memory
Table continues on the next page...

---

*Page 1466*

Self-Test Control Unit (STCU2)
Table 234. MCXE31B MBIST mapping (continued)
BIST BIST name Memory Memory instance name
index (NMCUT) index
5 FLEX_CAN_RAMS 3 FLEXCAN3 MB memory
5 FLEX_CAN_RAMS 4 FLEXCAN4 MB memory
5 FLEX_CAN_RAMS 5 FLEXCAN5 MB memory
6 QSPI_PERI_RAMS 0 QSPI RAM
6 QSPI_PERI_RAMS 1 QSPI TX memory
7 EMAC_TSN_RAM 0 EMAC Timestamp Memory
8 EMAC_RAMS 0 EMAC RXParser
8 EMAC_RAMS 1 EMAC TX block 1
8 EMAC_RAMS 2 EMAC TX block 1
8 EMAC_RAMS 3 EMAC TX block 2
8 EMAC_RAMS 4 EMAC RX block 2
9 b03_ETF_RAMS 0 HTM ETF
9 b03_ETF_RAMS 1 Shared ETF
9 b03_ETF_RAMS 2 CM7 cluster Instruction ETF
9 b03_ETF_RAMS 3 CM7 cluster Data ETF
10 HSE_RAMS 0 HSE secure RAM
10 HSE_RAMS 1 HSE PKC memory block 1
10 HSE_RAMS 2 HSE PKC memory block 2
10 HSE_RAMS 3 HSE MTB memory
11 HSE_ROMS 0 BOOTROM block 1
11 HSE_ROMS 0 BOOTROM block 2
11 HSE_ROMS 0 BOOTROM block 3
11 HSE_ROMS 0 BOOTROM block 4
11 HSE_ROMS 0 BOOTROM block 5
Table 235. MCXE317 MBIST mapping
BIST BIST name Memory Memory instance name
index (NMCUT) index
0 HSE_ROMS 0 BOOTROM block 1
0 HSE_ROMS 0 BOOTROM block 2
0 HSE_ROMS 0 BOOTROM block 3
0 HSE_ROMS 0 BOOTROM block 4
0 HSE_ROMS 0 BOOTROM block 5
Table continues on the next page...

---

*Page 1467*

Self-Test Control Unit (STCU2)
Table 235. MCXE317 MBIST mapping (continued)
BIST BIST name Memory Memory instance name
index (NMCUT) index
1 HSE_RAMS 0 HSE secure RAM
1 HSE_RAMS 1 HSE PKC memory block 1
1 HSE_RAMS 2 HSE PKC memory block 2
1 HSE_RAMS 3 HSE MTB memory
2 SYS0_DMA_RAMS 0 PRAM0 block 1
2 SYS0_DMA_RAMS 1 PRAM0 block 2
2 SYS0_DMA_RAMS 2 DMA TCD RAM
3 CM7_0_TOP 0 CM7_0 instruction cache data block 1
3 CM7_0_TOP 1 CM7_0 instruction cache data block 2
3 CM7_0_TOP 2 CM7_0 instruction cache tag block 1
3 CM7_0_TOP 3 CM7_0 instruction cache tag block 2
3 CM7_0_TOP 4 CM7_0 data cache data block 1
3 CM7_0_TOP 4 CM7_0 data cache data block 2
3 CM7_0_TOP 5 CM7_0 data cache data block 3
3 CM7_0_TOP 5 CM7_0 data cache data block 4
3 CM7_0_TOP 6 CM7_0 data cache data block 5
3 CM7_0_TOP 6 CM7_0 data cache data block 6
3 CM7_0_TOP 7 CM7_0 data cache data block 7
3 CM7_0_TOP 7 CM7_0 data cache data block 8
3 CM7_0_TOP 8 CM7_0 data cache tag block 1
3 CM7_0_TOP 9 CM7_0 data cache tag block 2
3 CM7_0_TOP 10 CM7_0 data cache tag block 3
3 CM7_0_TOP 11 CM7_0 data cache tag block 4
3 CM7_0_TOP 12 CM7_0 instruction TCM (Tightly Coupled Memory)
3 CM7_0_TOP 13 CM7_0 data TCM block 1
3 CM7_0_TOP 13 CM7_0 data TCM block 2
4 FLEX_CAN_RAMS 0 FLEXCAN0 MB memory
4 FLEX_CAN_RAMS 1 FLEXCAN1 MB memory
4 FLEX_CAN_RAMS 2 FLEXCAN2 MB memory
4 FLEX_CAN_RAMS 3 FLEXCAN3 MB memory
4 FLEX_CAN_RAMS 4 FLEXCAN4 MB memory
4 FLEX_CAN_RAMS 5 FLEXCAN5 MB memory

---

*Page 1468*

Self-Test Control Unit (STCU2)

#### 51.1.4 Clock connectivity

STCU2 works on the system clock domain. The scan operations while LBIST are done on TCK clock.
Table 236. Clock connectivity
STCU2 clock Clock connected to the chip Remarks
CORE_CLK AIPS_SLOW_CLK System clock divider output clock node
for STCU2 operations and register
REG_INTF_CLK
interface accesses
SHIFT_CLK JTAG_TCK JTAG_TCK used for shift operations
when self-test is in progress

#### 51.1.5 Memory map for self-test GPRs

See the memory map file attached to this document and 'SELFTEST_GPR' chapter for details.

#### 51.1.6 Handling unrecoverable faults

Only recoverable and non-critical faults from STCU2 are mapped to FCCU. The unrecoverable and critical faults are mapped to
MC_RGM. Therefore, ignore any references to mapping of unrecoverable and critical faults with FCCU in this chapter.

#### 51.1.7 Indicating fault state during the main reset domain Self-Test

FCCU EOUT indicates the fault state during the Self-Test as soon as the UTEST_MISC[FCCU_EOUT_ DEDICATED] is enabled
and DCMRWD2[EOUT_STAT_DUR_STEST] is enabled at the time of Self-Test setup steps.
The fault state remains asserted until the bit DCMRWD2[EOUT_STAT_DUR_STEST] is cleared post Self-Test reset sequence.
In case it is not required to indicate the fault state during the Self-Test, then the bit DCMRWD2[EOUT_STAT_DUR_STEST] should
not be programmed.

#### 51.1.8 Error events management in main reset domain Self-Test

Error events should be mapped to unrecoverable faults in STCU2_ERR_FM register for main reset domain Self-Test.

#### 51.1.9 AUTOLOCK_VALUE for register write access via STCU2_SKC

Register write-access watchdog timer explains that access to STCU2 registers via the STCU2_SKC security-key mechanism
times out after a number of STCU2 clock cycles equal to AUTOLOCK_VALUE. On this chip, AUTOLOCK_VALUE
is 32'hFFFF_FFFF.

#### 51.1.10 PLL loss of lock during Self-Test

Detection of PLL loss of lock during LBIST or MBIST causes a destructive reset in the system.
CMU0 programming is required before the Self-Test programming begins.
All the registers present in LBIST domains do not retain their content after Self-Test reset.

#### 51.1.11 STCU2 BIST start (BSTART) register description

Attempted accesses with the undefined reserved values in the BSTART register can result in undefined behavior. The following
table defines the BSTART field on this chip.

---

*Page 1469*

Self-Test Control Unit (STCU2)
Table 237. BSTART description
Field value Description
000b NOP (reset value)
001b RUN_ONLY: Run BISTs.
MTR controller (MCT) runs the selected BISTs without
programming them first.
010b Reserved
011b Reserved
100b PROG_ONLY: Program BISTs only.
MCT only programs BISTs and does not start them. This
enables different BISTs to be programmed with different
algorithms before all the selected BISTs are started using the
RUN_ONLY command.
101b PROG_RUN: Program BISTs and start them.
110b Reserved
111b Reserved

#### 51.1.12 STCU2 algorithm select (ALGOSEL) register description

This register selects any one of the predefined algorithms. The value used in this register is mapped to a predefined algorithm
register in BIST. After MCT has programmed information on this register and starts some BIST operation, this value is transferred
to the predefined algorithm register in every BIST that is selected.
If multiple predefined algorithms are selected, the order of execution is from LSB to MSB.
Any field that does not have a predefined algorithm associated with it is a reserved bit. No data is written to reserved bits and when
you read these bits, they always return 0. These predefined algorithms include:
• Backgrounds
• Address modes and other register programming that may be necessary
• The number of BIST runs needed to perform the exact coverage requested.
MCT decodes this information through a series of look-up tables. The following table describes the high-level BIST algorithms that
can be invoked through the MCT. The columns in that table have the following meaning:
• Index: The position in this MCT register enables this algorithm.
• Name: The symbolic name for this algorithm.
• Description: The intent and use model for this algorithm.
• BIST runs: The sequence of BIST invocations comprising this algorithm.
• Sequences: The ordered list of march elements to run. Each march element consists of a direction indicator (for increasing
addresses and for decreasing addresses) and a set of march phases. A march phase consists of reading (R) or writing (W),
the non-inverted (0) or inverted (1) background pattern.
• Used by default: Whether this algorithm is selected in the post-reset state of this register.

---

*Page 1470*

Self-Test Control Unit (STCU2)
Table 238. ALGOSEL description
Index Name Description BIST runs Sequences Used by default
3 March C+ single March C+ with MarchC+ column W0, R0W1, Yes
column fast and fast 1x (#4) R1W0R0W1,
solid background R1W0,
R0W1R1W0, R0
13 BasicChk Basic selfcontained BasicChk (#18) R0W1R1 -
checkerboard,
using column fast

#### 51.1.13 Self-Test programming sequence

This section describes a high level programming sequence for executing Self-Test. The detailed programming sequences for
supported configurations are available in a separate application note (contact your NXP sales representative).
• Program clock sources and MC_CGMs as per the clock configuration.
NOTE
While running MBIST on EMAC timestamp memory, MC_CGM.MUX_9_DC_0[DIV] should be appropriately
configured to ensure EMAC_CLK_TS should be atleast 1.5 times the AIPS_SLOW_CLK frequency.
• Program SELFTEST_GPR[CONFIG_REG] and SELFTEST_GPR[LBIST_PROG_REG] as per the LBIST configurations.
NOTE
This is not applicable for MCXE317 and MCXE315/MCXE316 wherein LBIST is not supported.
• Program STCU2 as per NXP recommended.
• Program dcf_client_utest_misc[FCCU_EOUT_DEDICATED] and DCMRWD2[EOUT_STAT_DUR_STEST] to configure
EOUT pins as dedicated and to indicate FCCU error states on EOUT pins during Self-Test respectively.
• Configure ERCTRL[ERASSERT] register bit in MC_RGM to achieve desired behaviour of Reset Pin as documented in
Reset Section and Pad States Section.
• Disable functional reset sources to avoid false Self-Test abort scenarios. For example, software watchdog timers cannot
be serviced while Self-Test because the software is not accessible and can cause invalid timeout reset. See Reset events
and configurations while Self-Test .
• Program the PCS_ENABLE[8:7] fields of the CONFIG_REG register (SELFTEST_GPR) to 0 to disable PCS.
• Trigger Self-Test execution by programming RUNSW register in STCU2.
• At the end of the Self-Test run, a functional reset is generated. Out of reset, the software can check the status registers
inside STCU2 and take actions accordingly.

#### 51.1.14 Modules operation during/post Self-Test

The clock, reset, power generation modules, such as RGM, PMC, CGM, FIRC, PLL, and SIRC will be available during and post
Self-Test. However, because the chip undergoes the reset sequence, PLL and CGM will be reset.
CMU0 and CMU3 will be available during Self-Test if enabled prior to Self-Test, but will be Reset post Self-Test.
STCU2 will also be available during Self-Test and the results can be read post Self-Test.

#### 51.1.15 Reset events and configurations while Self-Test

As Self-Test is a software initiated Self-Test, it is expected by software to ensure all the peripherals are disabled as documented
in Software considerations before Self-Test .

---

*Page 1471*

Self-Test Control Unit (STCU2)
The functional reset sources should be appropriately configured to avoid any functional reset while Self-Test is running (disable
SWT_RST and HSE_SWT_RST. JTAGC should not be in EXTEST, HIGHZ or CLAMP instructions). This ensures no selftest abort
while running selftest due to functional reset source. The below table specifies the state of functional reset sources for Self-Test.
Table 239. Reset events and configurations while self-test
Reset source Reset description Configurations/remarks for selftest
FCCU_RST FCCU reset reaction The FCCU is a part of logic which is under Self-Test and thus it would be
disabled during the Self-Test
ST_DONE Self-Test done reset The selftest done indication resets the device post selftest. While Self-Test is
running, the Self-Test done indication will be in disabled state
SWT_RST SWT reset request SWT should be disabled in Self-Test configuration sequence.
JTAG_RST JTAG Reset JTAG communication should be inactive while Self-Test and JTAGC state
machine should not be in EXTEST, HIGHZ or CLAMP instructions
HSE_SWT_R HSE SWT timeout HSE_SWT should be disabled in Self-Test configuration sequence.
ST
SW_FUNC software 'functional' reset The software is inactive during Self-Test and hence software functional reset
will not arrive while the chip is under Self-Test.
DEBUG_FUN debug 'functional' reset MDM DAP should not be configured for debug functional reset generation while
C Self-Test is running.
The destructive reset sources are available while in Self-Test. In case of any destructive reset event, the device undergoes a
destructive reset sequence, thereby resetting the STCU2 and Self-Test logic itself.
After completion of Self-Test (LBIST or MBIST), a functional reset sequence is executed by MC_RGM.

#### 51.1.16 Software considerations before Self-Test

1. As all the communication peripherals are not in LBIST partitions; hence, it is required by SW to stop all communications
and disable all the PCTLs of the communication peripherals before initiating Self-Test as the device will be unusable in
Self-Test. This is needed so that there is graceful safe stating of inputs of non-lbisted peripheral modules.
2. Configure RGM functional reset sources as interrupts before Self-Test as per the considerations mentioned in Reset
events and configurations while Self-Test , i.e., the SWTs should be disabled, the JTAGC should not be in states which
can cause a JTAG reset event (EXTEST, HIGHZ, CLAMP instructions) and the software should not trigger a functional
reset event via MC_ME or MDM_AP while the selftest is ongoing. This is to avoid any abort scenario due to any
functional reset source as Self-Test is initiated by SW.
There is no issue with Reset pin as Reset pin is a source of destructive reset and will reset STCU2 and Self-Test related logic etc.

#### 51.1.17 End of Self-Test

The Self-Test completion is a source of functional reset to the device, by which the device executes a functional reset sequence.
The logic which is LBISTed additionally undergoes a complete POR sequence to ensure that the random content shifted during
BIST sequence get cleared.
At the end of MBIST , the software should execute below steps:
1. Software reads the MBIST end flag to identify that the MBIST sequence had ended properly without any interruption or
abort, by reading STCU2.MBESWn.
2. Software reads the MBIST status to identify whether the MBIST was successful or not, by reading STCU2.MBSSWn.

---

*Page 1472*

Self-Test Control Unit (STCU2)
NOTE
The MBIST status should always be read in accordance with MBIST end flag. The STCU2.MBSSWn status is
irrelevant if STCU2.MBESWn indicates that MBIST execution is incomplete.
At the end of LBIST , the software should execute below steps:
1. Software reads the LBIST end flag to identify that the LBIST sequence had ended properly without any interruption or abort,
by reading STCU2.LBESWn.
2. Software reads the LBIST status to identify whether the LBIST was successful or not, by reading STCU2.LBSSWn.
NOTE
The LBIST status should always be read in accordance with LBIST end flag. The STCU2.LBSSWn status is
irrelevant if STCU2.LBESWn indicates that LBIST execution is incomplete.

#### 51.2 Introduction

STCU2 is a comprehensive programmable hardware module that:
• Manages the execution of BIST s
• Indicates whether each BIST passed
• Manages the chip's LBIST and the MBIST blocks

#### 51.3 Main features

STCU2 includes:
• System interface for reading from and writing to the STCU2 registers (online) via the CPU
• Programmable scheduler for BIST execution
• Control over LBIST concurrent or sequential execution
• Control over MBIST concurrent or sequential execution
• Programmable LBIST delayed concurrent start
• Programmable internal clock prescaler to reduce internal and BIST clocks
• PLL lock signal monitoring during BIST sequences
• Programmable BIST-sequence-execution watchdog timer that specifies the maximum time allowed for the execution of a
BIST sequence
• Fixed register-write-access watchdog timer that specifies the maximum amount of time that STCU2 allows you to write to its
registers after you unlock them
• Fields that indicate the pass/fail indication of each BIST, see Types of BIST sequences and Types of BIST partitions
• Fields that indicate the status of each type of online STCU2 internal error condition
• Programmable fault mapping of each BIST for controlling the type of fault that STCU2 reports (recoverable or unrecoverable)
when the BIST fails to execute
• Programmable fault mapping of each STCU2 internal error condition for controlling the type of fault that STCU2 reports
(recoverable or unrecoverable) when the condition occurs
• Signals to report recoverable and unrecoverable faults to the FCCU module
• Redundant recoverable and unrecoverable fault-generation logic to improve reliability
• FCCU recoverable and unrecoverable fault-injection mechanism
• Global register write-protection mechanism that requires two security key codes
• Global automatic power saving after a BIST sequence is completed when watchdog timer time-out is detected

---

*Page 1473*

Self-Test Control Unit (STCU2)
• Watchdog automatic clock wake-up mechanism when software unlocks the STCU2 registers for write access
• Watchdog automatic power saving when the fixed register-write-access watchdog timer times out

#### 51.4 Block diagram

This diagram shows the parts of STCU2:
LBIST
LBIST
interface
WDG FSM
Loader
Reg Master
Shifter
interface FSM Interrupt
FSM
IPS
Fault interfaces PLL Lock
MBIST
MBIST
interface
Clock
interface
FCCU
Figure 188. STCU2 block diagram
This table describes the parts of STCU2:
Table 240. STCU2 parts
Part Function
Reg interface Provides access to:
• Registers
• The security key logic
• The IPS interface
Fault interfaces Performs the following tasks:
• Collects the fault conditions caused by STCU2 internal error conditions and each
BIST executed in a sequence
• Sets the global recoverable or unrecoverable status flag, depending on the fault
mapping for a given BIST
• Manages the recoverable and unrecoverable fault lines to and from the
FCCU module
Table continues on the next page...

---

*Page 1474*

Self-Test Control Unit (STCU2)
Table 240. STCU2 parts (continued)
Part Function
• Manages the set/clear injection mechanism provided by the FCCU module
To improve the intrinsic reliability of this critical logic, the generation logic
is duplicated.
Clock interface Manages the internal and the BIST clock prescaler, the internal clock-gating power
saving, and the wake-up clock feature
WDG FSM Provides the following:
• A programmable BIST-sequence-execution watchdog timer that specifies the
maximum time allowed for the execution of a BIST sequence
• A fixed register-write-access watchdog timer that specifies the maximum
amount of time that STCU2 allows you to write to its registers after you
unlock them
Master FSM Coordinates and schedules all of the operations performed during a BIST sequence
Loader Shifter FSM Programs the BIST registers and reads back the data to be checked at the end of
each test operation
LBIST interface Provides the interface between the chip's LBIST engines and the STCU2 controllers
MBIST interface Provides the interface between the MBIST controller and the STCU2 controller

#### 51.5 Peripheral bus interface

The peripheral bus interface is a slave bus used for configuration purposes via CPU. The module supports the following bus
read operations:
• Word (32 bits) data read operations to any registers
• Any other operation is not supported.
Word, low and high half-words, byte operations are supported in write mode only:
• Write access is allowed by software only when CFG[WRP] is cleared. The default value of CFG[WRP] is cleared, exceptions
are the following read/write fields: CFG[WRP] , ERR_STAT[UFSF] and ERR_STAT[RFSF] .
• STCU2 Configuration (CFG) should only be accessed in 32 bit (partial access not allowed).
The STCU2 module generates a transfer error in the following cases:
• Any read access to the registers after writing Key1 and before writing Key2 (in online ) and before register write-access
watchdog timer expires.
• Any write/read access to the register addresses not mapped on the peripheral but included in the address space of
the peripheral
• Any write/read operation different from byte/halfword/word (free byte enables or other operations) on each register
• Any write operation on Double Security Key register applying a wrong sequence of keys (the two write operations cannot be
interleaved with other access to STCU2 registers)
• Any write operation performed on a register when the Double Security keys have not been applied

---

*Page 1475*

Self-Test Control Unit (STCU2)
• Any write operation performed on registers when CFG[WRP] is set and the access is performed through software (internal
peripheral interface). The exceptions are SKC register (for valid keys value and sequence) in which STCU2 does not assert
transfer error.
• Any write operation performed on Read Only registers
The registers of the STCU2 module are accessible (read/write) in each access mode: user, supervisor, or test.
In case there are write operations on bits marked as reserved, the transfer error is not generated.
NOTE
See the chip-specific STCU2 information for the wait time necessary to write to the online registers.

#### 51.6 BISTs and BIST partitions

#### 51.6.1 Definition: BIST

A BIST is a test that the chip can execute to verify the functional integrity of a part of itself. The chip uses STCU2 and other on-chip
hardware to execute a BIST.

#### 51.6.2 Definition: BIST partition

A BIST partition is a part of a chip for which a BIST has been defined. The hardware that is included in a given BIST
partition is chip-specific. For a list of the hardware included in each of the BIST partitions on this chip, see the chip-specific
STCU2 information.

#### 51.6.3 Example: BIST partitions on a chip

This is an example of a chip with six BIST partitions:
Chip BIST partition
Figure 189. BIST partitions on a chip

#### 51.6.4 Types of BIST partitions

The STCU2 module on this chip supports the following types of BIST partitions:
Table 241. Types of BIST partitions
BIST partition type Description
MBIST partition An SRAM or ROM block
LBIST partition One or more digital modules

---

*Page 1476*

Self-Test Control Unit (STCU2)

#### 51.7 BIST sequences

#### 51.7.1 Definition: BIST sequence

A BIST sequence is a programmable series of one or more phases, each of which executes one or more individual BISTs.

#### 51.7.2 STCU2 executes MBISTs before LBISTs

If a BIST sequence includes both MBISTs and LBISTs; STCU2 executes the MBISTs first.

#### 51.7.3 Example: Single-phase BIST sequence

This is an example of a single-phase BIST sequence in which STCU2 executes only one BIST:
Table 242. Example: Single-phase BIST sequence
Phase BIST executed
0 MBIST 16

#### 51.7.4 Example: Multi-phase BIST sequence

This is an example of a multiphase BIST sequence in which STCU2 executes more than one BIST in parallel in some phases:
Table 243. Example: Multi-phase BIST sequence
Phase BISTs executed
0 MBISTs 7, 30
1 MBIST 23
2 MBISTs 6, 16, 29, 23
3 LBIST 12
4 LBISTs 13, 8, 11
5 LBISTs 19, 6

#### 51.7.5 Types of BIST sequences

The STCU2 module on this chip supports these types of BIST sequences:
Table 244. Example: Types of BIST sequences
BIST sequence type Description
Online BIST sequence A BIST sequence that STCU2 executes during runtime at
the request of software. Application software configures and
initiates execution of the BIST sequence by loading values into
STCU2 registers.

#### 51.7.6 Supported BIST sequences

NXP supports only a specific set of BIST sequences for this chip. For the list of supported BIST sequences, see the chip-specific
STCU2 information.

---

*Page 1477*

Self-Test Control Unit (STCU2)

#### 51.8 Functional description

#### 51.8.1 FSM description

The module has three FSM that work together: Master FSM, Loader/Shifter FSM, and the Watchdog FSM. Basically the Master
FSM is the core unit of the STCU2 module. It coordinates all the self-test operations and the other FSM. The Loader/Shifter FSM
is used to program the MBIST and the LBIST parameters and to retrieve the related results depending on the parameters stored
into the STCU2 registers and under the control of the Master FSM. The Watchdog FSM evaluates all the schedule time for MBIST
and LBIST and the time-out in case of wrong STCU2 programming.

#### 51.8.2 BIST scheduling

STCU2 is designed to program the parallel/serial execution of the MBIST or LBIST depending on the power, timing, and
coverage constraints.
The mechanism used to provide this flexibility is a linked list of BIST descriptors where the starting pointer is defined in CFG[PTR] .
The first LBIST is mapped on 0, the second on 1, and so on. A BIST descriptor identifies a LBIST or MBIST via an index that is
associated with its control register ( STCU2 LBIST Control (LB_CTRL0) or STCU2 MBIST Control (MB_CTRL0 - MB_CTRL11) ).
The first MBIST is mapped on (00000080h + 0), the second on (00000080h + 1), and so on. The additional pointers of the linked
list are in LB_CTRL n [PTR] for LBIST n (where n is the selected LBIST) and MB_CTRL m [PTR] for MBIST m (where m is the
selected MBIST) and must be populated depending on the selected run sequence. The additional fields LB_CTRL[CSM] and
MB_CTRL[CSM] provide the flexibility to run concurrently or sequentially the chosen set of the LBIST or MBIST or to close the
linked list by setting the NIL pointer.

#### 51.8.3 FCCU interface

The FCCU interface is the hardware flag mechanism towards the system, to indicate the occurrence of an Unrecoverable fault
and/or a Recoverable fault failure during the Self Test sequence. Two independent signals have been used to mitigate the
common cause faults.
To diagnose physical defects on the two fault signals, a fault injection mechanism is also provided. In this case, the FCCU interface
allows the user application to check the integrity of the UF and RF connection lines between the STCU2 and the FCCU. Refer to
the description of FCCU fault injection mechanism to understand how the UF/RF set/clear mechanism works.

#### 51.8.4 Watchdogs

The STCU2 implements different watchdogs to ensure that operations are finished in time.
51.8.4.1 BIST watchdog timer
The LBIST and MBIST execution time has to be configured as described in STCU2 Watchdog Granularity (WDG) , to account for
the overall execution time of the self test sequence. In case the selected LBISTs or MBISTs are not yet completed during assigned
time, the current LBISTs or MBISTs execution is interrupted and a failure is flagged into ERR_STAT[WDTOSW] and MBESWx
or LBESW .
In case of multiple sequential run in the same online session and the time-out happens in the middle of a sequential run, the next
sequential run will be skipped and the execution ends with the current updated status of the registers reported above.
51.8.4.2 Register write-access watchdog timer
As explained in the STCU2 SK Code (SKC) :
• A key mechanism protects STCU2 registers during the self-test configuration phase by preventing any unwanted access.
• A hardware watchdog timer locks register-write access after a number of STCU2 clock cycles. To refresh the hardware
watchdog timer before it times out, write Key1 and Key2 sequences .
AUTOLOCK_VALUE is the number of STCU2 clock cycles after which the hardware watchdog timer locks register-write access.
See the chip-specific STCU2 information for the value of AUTOLOCK_VALUE.

---

*Page 1478*

Self-Test Control Unit (STCU2)
The hardware watchdog timer is particularly useful in case CFG[WRP] is 0 during the software self-test configuration. In this case,
the software application might enable write access to the STCU2 registers.

#### 51.9 STCU2 register descriptions

The STCU2 registers are listed in this section.
NOTE
Always write the reset value for the Reserved fields.
During online self-test, after the STCU2 registers are configured, they must not be overridden via internal peripheral system until
the self-test is complete.

#### 51.9.1 STCU2 memory map

STCU base address: 403A_0000h
Offset Register Access Reset value
Width
(In bits)
4h STCU2 Run Software (RUNSW) 32 RW 0000_0000h
8h STCU2 SK Code (SKC) 32 W 0000_0000h
Ch STCU2 Configuration (CFG) 32 RW 0000_0000h
14h STCU2 Watchdog Granularity (WDG) 32 RW 0000_FFFFh
24h STCU2 Error (ERR_STAT) 32 RW 0000_0000h
28h STCU2 Error FM (ERR_FM) 32 RW 0000_0000h
4Ch STCU2 Online LBIST Status (LBSSW0) 32 R 0000_0000h
5Ch STCU2 Online LBIST End Flag (LBESW0) 32 R 0000_0000h
7Ch STCU2 Online LBIST Unrecoverable FM (LBUFM0) 32 RW 0000_0000h
10Ch STCU2 Online MBIST Status (MBSSW0) 32 R 0000_0000h
14Ch STCU2 Online MBIST End Flag (MBESW0) 32 R 0000_0000h
18Ch STCU2 MBIST Unrecoverable FM (MBUFM0) 32 RW 0000_0000h
200h STCU2 LBIST Control (LB_CTRL0) 32 RW 0000_0000h
204h STCU2 LBIST PC Stop (LB_PCS0) 32 RW 0000_0000h
220h STCU2 Online LBIST MISR Expected Low (LB_MISRELSW0) 32 RW FFFF_FFFFh
224h STCU2 Online LBIST MISR Expected High (LB_MISREHSW0) 32 RW FFFF_FFFFh
228h STCU2 Online LBIST MISR Read Low (LB_MISRRLSW0) 32 R 0000_0000h
22Ch STCU2 Online LBIST MISR Read High (LB_MISRRHSW0) 32 R 0000_0000h
2200h STCU2 Algorithm Select (ALGOSEL) 32 RW 0000_0000h
220Ch STCU2 MBIST Stagger (STGGR) 32 RW 0000_0000h
2210h STCU2 BIST Start (BSTART) 32 RW 0000_0000h
2214h - STCU2 MBIST Control (MB_CTRL0 - MB_CTRL11) 32 RW 0000_0000h
2240h

---

*Page 1479*

Self-Test Control Unit (STCU2)

#### 51.9.2 STCU2 Run Software (RUNSW)

Offset
Register Offset
RUNSW 4h
Function
The RUNSW register defines the RUNSW bit to start the online self-testing procedure.
The R/W fields in this register are readable at any time. However, you can write to these fields only when CFG[WRP] = 0.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0
MBSW LBSW RUNS
PLL ... PLL ... W
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-12
—
Reserved
11
—
Reserved
10
—
9 Online MBIST with PLL Enabled
MBSWPLLEN 0b - Online MBIST is executed without using the on-chip PLL.
1b - Online MBIST is executed using the PLL configuration provided by software. STCU2 does
not take the PLL control but monitors the PLL lock signal to check if PLL is working correctly.
8 Online LBIST with PLL Enabled
LBSWPLLEN 0b - Online LBIST is executed without using the on-chip PLL.
Table continues on the next page...

---

*Page 1480*

Self-Test Control Unit (STCU2)
Table continued from the previous page...
Field Function
1b - Online LBIST is executed using the PLL configuration provided by the software. STCU2 does
not take the PLL control but monitors the PLL lock signal to check if PLL is working correctly.
Reserved
7-2
—
Reserved
1
—
0
The RUNSW bit is automatically cleared by STCU2 when the online self-testing procedure is complete.
RUNSW
0b - Idle
1b - Online self-testing procedure is running

#### 51.9.3 STCU2 SK Code (SKC)

Offset
Register Offset
SKC 8h
Function
The SKC register implements the security key code mechanism needed to access in write mode to the other STCU2 registers.
To unlock STCU2 access after the STCU2 asynchronous reset and at the end of the STCU2 run, the software (IPS bus) need to
apply the following sequence:
• write the key1 into the SKC register
• write the key2 into the SKC register
Depending on the online test step, the two keys are different. Byte write operation is not allowed because the full key has to be
recognized as one unit.
A hard-coded WDG counter ( Register write-access watchdog timer ) starts decrementing its value right after POR is deasserted.
Therefore, user must ensure to complete unlocking and programming sequence before its expiry. In case the STCU2 register
access lasts more cycles than the one defined in the hard-coded WDG timeout, the STCU2 write access is locked and the WDG
and register interface clocks are switched off. Also, in this case, to enable the write access to the STCU2 again and the WDG
and register interface clocks, it is required to apply the sequence again. In case of invalid access or sequence (Key1/2 have to be
applied consecutively), a transfer error on the IPS is asserted. The STCU2 write access is locked and to unlock the access, the
sequence has to be applied again. This can also happen if watchdog expires between key1 and key2. In this case, user needs
to apply the key1-key2 sequence again. In case it is required to extend the STCU2 register access cycles before the hard-coded
WDG timeout expires, Key1 and Key2 sequences need to be applied. The effect of this write operation is to re-initialize the WDG
timeout counter. The STCU2 write access is locked and to unlock the access, the sequence has to be applied again.
The SKC register is not readable. The value 00000000h is always returned in case of read operation.

---

*Page 1481*

Self-Test Control Unit (STCU2)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
W SKC
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
W SKC
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 STCU2 SK Code
SKC STCU2 security key code for online test
= 753F924Eh: Key1 to unlock the write access the STCU2
= 8AC06DB1h: Key2 to unlock the write access the STCU2

#### 51.9.4 STCU2 Configuration (CFG)

Offset
Register Offset
CFG Ch
Function
The CFG register includes the global configuration of the STCU2 and can be updated in the online test steps.
The access to this register is described in the following figure. It further depends on the state of the WRP bit as follows:
• When WRP = 0: The register can be written during the online self-test case without restrictions.
• When WRP = 1:
— The only bit that can be written without any restriction is WRP.
— In case there are software operations that write all the register's bits, a transfer error is raised only if the value of
the selected byte written differs from the current status of the register. This functionality has been implemented to
prevent potential compilation behavior that might invalidate this single bit clean capability.

---

*Page 1482*

Self-Test Control Unit (STCU2)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
PTR LB_DELAY
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0
Reserv
LB_DELAY WRP CLK_CFG
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
30-21 First LBIST or MBIST pointer
PTR PTR defines the logical pointer to the first LBIST or MBIST to be scheduled when the self-testing procedure
is enabled. PTR is the entry pointer to a linked list of BIST descriptors. If PTR = 0 then the LBIST0 is initially
scheduled. See BIST scheduling for details.
0h to (01h - 1): pointer to LBIST
00000080h to (00000080h + 00Ch - 1): pointer to MBIST
3FFh: pointer to NIL. No BIST execution.
others: invalid pointer => an error is set into the ERR_STAT[INVPSW] .
20-13 Delay LBIST run
LB_DELAY LB_DELAY defines the delay between the LBIST starts when more than a single LBIST is selected to be
executed concurrently with the purpose of smoothing the power consumption transient. The allowed delay
time are these:
00h: No delay
01h: 1 x 16 STCU2 CORE_CLK cycles
02h: 2 x 16 STCU2 CORE_CLK cycles
03h: 3 x 16 STCU2 CORE_CLK cycles
...
FDh: 253 x 16 STCU2 CORE_CLK cycles
FEh: 254 x 16 STCU2 CORE_CLK cycles
FFh: 255 x 16 STCU2 CORE_CLK cycles
Reserved
12-9
Table continues on the next page...

---

*Page 1483*

Self-Test Control Unit (STCU2)
Table continued from the previous page...
Field Function
—
8 Write Protection
WRP 0: Specific STCU2 registers can be written through IPS bus interface
1: STCU2 registers cannot be written through IPS, preventing any user application write operation
Reserved
7-6
—
Reserved
5
—
Reserved
4
—
Reserved
3
—
2-0 Logic, Memory BIST, and STCU2 CORE_CLK configuration
CLK_CFG CLK_CFG defines the ratio between the sys_clk and the internal clock used to program both the LBIST and
the MBIST and the STCU2 CORE_CLK. The punch-out mechanism is used to generate the derived clocks.
The following configurations are allowed:
000b - sys_clk/1
001b - sys_clk/2
010b - sys_clk/3
011b - sys_clk/4
100b - sys_clk/5
101b - sys_clk/6
110b - sys_clk/7
111b - sys_clk/8

#### 51.9.5 STCU2 Watchdog Granularity (WDG)

Offset
Register Offset
WDG 14h
Function
The WDG register defines the time budget of LBIST and MBIST execution providing a protection mechanism in case of dead-lock
or endless conditions during the self-test procedure.

---

*Page 1484*

Self-Test Control Unit (STCU2)
In case online self-test sequence is run, it defines the timeout of the execution run.
The R/W fields in this register are readable at any time. You can write to these fields when online self-test phase is active and
CFG[WRP] = 0.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
WDGEOC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
WDGEOC
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31-0 Watchdog End of Count Timer
WDGEOC This value has to be set to define the time budget related to the online self-test execution and check that
everything is correctly working within this slot of time. The delay time slots that are allowed are as follows:
0000 0000h: 1 x 16 STCU2 CORE_CLK cycles
0000 0001h: 2 x 16 STCU2 CORE_CLK cycles
0000 0002h: 3 x 16 STCU2 CORE_CLK cycles
...
FFFF FFFDh: 4294967294 x 16 STCU2 CORE_CLK cycles
FFFF FFFEh: 4294967295 x 16 STCU2 CORE_CLK cycles
FFFF FFFFh: 4294967296 x 16 STCU2 CORE_CLK cycles

#### 51.9.6 STCU2 Error (ERR_STAT)

Offset
Register Offset
ERR_STAT 24h
Function
The ERR_STAT register includes the status flags related to the STCU2 internal error conditions occurred during the configuration
or the online self-testing execution.
The UFSF and RFSF can be set/cleared using the FCCU dedicated channels.
The access to this register is described in the following figure and as follows:

---

*Page 1485*

Self-Test Control Unit (STCU2)
• If you select the byte write capability to write only UFSF and RFSF, then there is no restriction in writing these bits.
• If your software performs the write operations on other bits besides UFSF/RFSF, then a transfer error is generated only if
the value you are writing to the other bits differs from their value currently stored in the register. This functionality has been
implemented to prevent potential compilation behavior that might invalidate the UFSF/RFSF single bit set/clean capability.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
LOCK WDTO ENGE INVPS
R 0 0 0 0 0
ESW SW SW W
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
UFSF RFSF
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-26
—
Reserved
25
—
Reserved
24
—
Reserved
23-21
—
20 Online LOCK error
LOCKESW You can always read this field. The content of this field is initialized to its reset value when RUNSW[RUNSW]
is set to 1.
0b - In case PLL is enabled, it is correctly locked during the self-test sequence
1b - When the PLL is enabled, this flag highlights that there has been an unexpected PLL
unlock(loss-of-lock) event during the online self-test sequence execution. The online self-test run
is stopped and the status of the currently running LBISTs or MBISTs is saved in the related
registers. The LOCK signal is monitored during the LBIST run when RUNSW[LBSWPLLEN] is set
and/or during the MBIST run when RUNSW[MBSWPLLEN] = 1.
19 Online watchdog timeout
WDTOSW
Table continues on the next page...

---

*Page 1486*

Self-Test Control Unit (STCU2)
Table continued from the previous page...
Field Function
You can always read this field. The content of this field is initialized to its reset value when RUNSW[RUNSW]
is set to 1.
0b - LBIST and MBIST time slot s completed within the assigned watchdog time.
1b - LBIST and MBIST time slot s not completed within the assigned watchdog time or there are
internal mismatches among End of Execution signals.
Reserved
18
—
17 Online engine error
ENGESW You can always read this field. The content of this field is initialized to its reset value when RUNSW[RUNSW]
is set to 1.
0b - Valid engine execution
1b - Invalid engine execution. The error conditions that set this bit are FSM, protocol error, and so
on.
16 Online invalid pointer
INVPSW You can always read this field. The content of this field is initialized to its reset value when RUNSW[RUNSW]
is set to 1.
0b - Valid linked pointer list
1b - Invalid linked pointer list. The following conditions set this bit: Initial LBIST or MBIST pointer
is out of range; LBIST is selected when MBIST is concurrently running or vice versa; Error in the
LBIST/ MBIST linking (execution generates an infinite loop) .
Reserved
15-10
—
9 Unrecoverable Faults Status Flag
UFSF This flag reports the global status of the Unrecoverable Faults(UF). This field can be set or cleared using
the FCCU dedicated channel, and can also be set or cleared by software.
0b - No errors that trigger the UF condition.
1b - There are errors that trigger the UF condition.
8 Recoverable Faults Status Flag
RFSF This flag reports the global status of the Recoverable Fault (RF). This field can be set or cleared using the
FCCU dedicated channel, and can also be set or cleared by software.
0b - No errors that trigger the Recoverable Faults condition
1b - There are errors that trigger the Recoverable Faults condition
Reserved
7-5
—
Table continues on the next page...

---

*Page 1487*

Self-Test Control Unit (STCU2)
Table continued from the previous page...
Field Function
4-0 Reserved
— Write may occur on these fields but writes have no effect.

#### 51.9.7 STCU2 Error FM (ERR_FM)

Offset
Register Offset
ERR_FM 28h
Function
The ERR_FM register defines the fault mapping of the STCU2 faults described in the register ERR_STAT in terms of UF or RF.
All sources of internal faults can be routed to UF and RF.
The R/W fields in this register are readable at any time. You can write to these fields when online self-test phase is active and
CFG[WRP] = 0.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
LOCK WDTO Reserv ENGE INVPU
EUFM UFM ed UFM FM
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-5
—
4 PLL LOCK Unrecoverable Fault Mapping
LOCKEUFM 0b - Recoverable Fault Mapping
1b - Unrecoverable Fault Mapping
Table continues on the next page...

---

*Page 1488*

Self-Test Control Unit (STCU2)
Table continued from the previous page...
Field Function
3 Watchdog Timeout Unrecoverable Fault Mapping
WDTOUFM 0b - Recoverable Fault Mapping
1b - Unrecoverable Fault Mapping
Reserved
2
—
1 Engine Error Unrecoverable Fault Mapping
ENGEUFM 0b - Recoverable Fault Mapping
1b - Unrecoverable Fault Mapping
0 Invalid Pointer Unrecoverable Fault Mapping
INVPUFM 0b - Recoverable Fault Mapping
1b - Unrecoverable Mapping

#### 51.9.8 STCU2 Online LBIST Status (LBSSW0)

Offset
Register Offset
LBSSW0 4Ch
Function
This register includes the results corresponding to the execution of the selected online LBIST.
The size of the register depends on the number of LBIST .
The content of this register is initialized to its reset value when RUNSW[RUNSW] is set to 1.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
LBSS
R 0
W0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1489*

Self-Test Control Unit (STCU2)
Fields
Field Function
Reserved
31-1
—
0 LBSSWn
LBSSWn online status of the selected LBIST
0b - Failed LBIST execution
1b - Successful LBIST execution

#### 51.9.9 STCU2 Online LBIST End Flag (LBESW0)

Offset
Register Offset
LBESW0 5Ch
Function
This register includes the results corresponding to the execution of the selected online LBIST.
The size of the register depends on the number of LBIST .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
LBES
R 0
W0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-1
—
Table continues on the next page...

---

*Page 1490*

Self-Test Control Unit (STCU2)
Table continued from the previous page...
Field Function
0 LBESW
LBESWn LBESWx: online LBIST end status
0b - LBIST execution not yet completed
1b - LBIST execution finished

#### 51.9.10 STCU2 Online LBIST Unrecoverable FM (LBUFM0)

Offset
Register Offset
LBUFM0 7Ch
Function
This register defines the fault mapping of each LBIST in terms of UF or RF.
The size of the register 0 depends on the number of LBIST .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
LBUF
M0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-1
—
0 LBIST Unrecoverable Fault Mapping
LBUFMn 0b - Recoverable Fault mapping
1b - Unrecoverable Fault mapping

---

*Page 1491*

Self-Test Control Unit (STCU2)

#### 51.9.11 STCU2 Online MBIST Status (MBSSW0)

Offset
Register Offset
MBSSW0 10Ch
Function
This register includes the results corresponding to the execution of the selected online MBIST in the range 0:11.
The size of the register depends on the number of BISTed RAMs/ROMs.
The content of this register is initialized to its reset value when RUNSW[RUNSW] is set to 1.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
MBSS MBSS MBSS MBSS MBSS MBSS MBSS MBSS MBSS MBSS MBSS MBSS
R 0
W11 W10 W9 W8 W7 W6 W5 W4 W3 W2 W1 W0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-12
—
11-0 MBSSW
MBSSWn Online status of the selected MBISTn (where n = 11:0).
0b - Failed MBIST execution
1b - Successful MBIST execution

#### 51.9.12 STCU2 Online MBIST End Flag (MBESW0)

Offset
Register Offset
MBESW0 14Ch

---

*Page 1492*

Self-Test Control Unit (STCU2)
Function
This register includes the End Flag related to the execution of the selected online MBIST in the range 0:11.
The size of the register depends on the number of BISTed RAMs/ROMs.
The content of this register is initialized to its reset value when RUNSW[RUNSW] is set to 1.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
MBES MBES MBES MBES MBES MBES MBES MBES MBES MBES MBES MBES
R 0
W11 W10 W9 W8 W7 W6 W5 W4 W3 W2 W1 W0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-12
—
11-0 Online MBISTn (where n = 11:0) end status.
MBESWn 0b - MBIST execution not yet completed
1b - MBIST execution finished

#### 51.9.13 STCU2 MBIST Unrecoverable FM (MBUFM0)

Offset
Register Offset
MBUFM0 18Ch
Function
This register defines the fault mapping, in terms of UF or RF, of the MBIST in the range 0:11
The size of the register depends on the number of BISTed RAMs/ROMs.
The R/W fields in this register are readable at any time. You can write to these fields when online self-test phase is active and
CFG[WRP] = 0.

---

*Page 1493*

Self-Test Control Unit (STCU2)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
MBUF MBUF MBUF MBUF MBUF MBUF MBUF MBUF MBUF MBUF MBUF MBUF
M11 M10 M9 M8 M7 M6 M5 M4 M3 M2 M1 M0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-12
—
11-0 MBESW
MBUFMn Online end status of MBISTn (where n = 11:0).
0b - Recoverable fault mapping
1b - Unrecoverable fault mapping

#### 51.9.14 STCU2 LBIST Control (LB_CTRL0)

Offset
Register Offset
LB_CTRL0 200h
Function
This register defines the control setting of each LBIST controller.
The R/W fields in this register are readable at any time. You can write to these fields when online self-test phase is active and
CFG[WRP] = 0.

---

*Page 1494*

Self-Test Control Unit (STCU2)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv Reserv
CSM PTR SHS
ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
Reserv
SCEN_OFF SCEN_ON CWS
ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Concurrent/sequential mode
CSM The next LBIST is scheduled concurrently to the current one if the CSM bit is set to 1; otherwise, it is
scheduled sequentially to the completion of the current LBIST execution.
0b - Sequential mode
1b - Concurrent mode
30-21 Next LBIST or MBIST pointer
PTR PTR defines the logical pointer to the next LBIST or MBIST to be scheduled. The next LBIST or MBIST is
scheduled concurrently to the current one if the CSM bit is set to 1, otherwise it is scheduled sequentially to
the completion of the current LBIST execution. In case of NIL pointer, the CSM bit has to be set Sequential
(0) to define this is the end of the list. The self-testing procedure stops after the last BIST in the configuration
chain is complete. See BIST scheduling for details.
0h to (01h - 1): pointer to NLBIST-1
00000080h to (00000080h + 00Ch - 1): pointer to MBIST
3FFh: pointer to NIL. No BIST execution.
others: invalid pointer => an error is set into the ERR_STAT[INVPSW] .
20 Reserved
—
NOTE
This reserved field is writable but do not write any value to it other than its reset value
19 Reserved
—
NOTE
This reserved field is writable but do not write any value to it other than its reset value
18-16 Shift speed
SHS SHS defines the shift speed
Table continues on the next page...

---

*Page 1495*

Self-Test Control Unit (STCU2)
Table continued from the previous page...
Field Function
000b - Shift at full rate (BIST clock) .
001b - Shift at 1/2 rate (BIST clock).
010b - Shift at 1/3 rate (BIST clock).
011b - Shift at 1/4 rate (BIST clock).
100b - Shift at 1/5 rate (BIST clock).
101b - Shift at 1/6 rate (BIST clock).
110b - Shift at 1/7 rate (BIST clock).
111b - Shift at 1/8 rate (BIST clock).
15-12 Scan enable OFF
SCEN_OFF SCEN_OFF information is used to configure the lbist controller hardware to generate off_cycles, delay
cycles during the scan enable off transition.
NOTE
SCEN_OFF must be programmed to a value >=1.
0000b - 0 delay cycles
0001b - 1 delay cycle
0010b - 2 delay cycles
0011b - 3 delay cycles
0100b - 4 delay cycles
0101b - 5 delay cycles
0110b - 6 delay cycles
0111b - 7 delay cycles
1000b - 8 delay cycles
1001b - 9 delay cycles
1010b - 10 delay cycles
1011b - 11 delay cycles
1100b - 12 delay cycles
1101b - 13 delay cycles
1110b - 14 delay cycles
1111b - 15 delay cycles
11-8 Scan enable ON
SCEN_ON SCEN_ON information is used to configure the lbist controller hardware to generate on_cycles, delay cycles
during the scan enable on transition,
Table continues on the next page...

---

*Page 1496*

Self-Test Control Unit (STCU2)
Table continued from the previous page...
Field Function
NOTE
SCEN_ON delay register value must be programmed to a value >=1
0000b - 0 delay cycles
0001b - 1 delay cycle
0010b - 2 delay cycles
0011b - 3 delay cycles
0100b - 4 delay cycles
0101b - 5 delay cycles
0110b - 6 delay cycles
0111b - 7 delay cycles
1000b - 8 delay cycles
1001b - 9 delay cycles
1010b - 10 delay cycles
1011b - 11 delay cycles
1100b - 12 delay cycles
1101b - 13 delay cycles
1110b - 14 delay cycles
1111b - 15 delay cycles
Reserved
7
—
Reserved
6
—
5-0 Capture window size
CWS CWS defines the capture window size.
00_0000b - Illegal
00_0001b - Controller waits 1 shift cycle for capture to finish.
00_0010b - Controller waits 2 shift cycles for capture to finish.
00_0011b - Controller waits 3 shift cycles for capture to finish.
00_0100b - Controller waits 4 shift cycles for capture to finish.
00_0101b - Controller waits 5 shift cycles for capture to finish.
00_0110b - Controller waits 6 shift cycles for capture to finish.
00_0111b - Controller waits 7 shift cycles for capture to finish.

---

*Page 1497*

Self-Test Control Unit (STCU2)

#### 51.9.15 STCU2 LBIST PC Stop (LB_PCS0)

Offset
Register Offset
LB_PCS0 204h
Function
This register defines the pattern counter stop of each LBIST controller.
The R/W fields in this register are readable at any time. You can write to these fields when online self-test phase is active and
CFG[WRP] = 0.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
PCS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PCS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-26
—
25-0 PCS
PCS Pattern counter stop
PCS defines the pattern counter stop value.

#### 51.9.16 STCU2 Online LBIST MISR Expected Low (LB_MISRELSW0)

Offset
Register Offset
LB_MISRELSW0 220h
Function
This register defines bits 32 of the expected MISR of the online LBIST controller.

---

*Page 1498*

Self-Test Control Unit (STCU2)
The size of the register depends on the number of MISR bits of the related LBIST. .
The R/W fields in this register are readable at any time. You can write to these fields when online self-test phase is active and
CFG[WRP] = 0.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
MISRESWx
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
MISRESWx
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31-0 Online MISR expected low bits
MISRESWx This field defines 32 bits of the expected MISR.

#### 51.9.17 STCU2 Online LBIST MISR Expected High (LB_MISREHSW0)

Offset
Register Offset
LB_MISREHSW0 224h
Function
The size of the register depends on the number of MISR bits of the related LBIST. .
The R/W fields in this register are readable at any time. You can write to these fields when online self-test phase is active and
CFG[WRP] = 0.

---

*Page 1499*

Self-Test Control Unit (STCU2)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
MISRESWx
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
MISRESWx
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
31-0 Online MISR Expected High Bits
MISRESWx This field defines the 32 bits of the expected MISR.

#### 51.9.18 STCU2 Online LBIST MISR Read Low (LB_MISRRLSW0)

Offset
Register Offset
LB_MISRRLSW0 228h
Function
This register reports 32 bits of the MISR obtained at the end of the online LBIST controller execution.
The size of the register depends on the number of MISR bits of the related LBIST. .
The content of this register is initialized to its reset value when RUNSW[RUNSW] is set to 1.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R MISRRSWx
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R MISRRSWx
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1500*

Self-Test Control Unit (STCU2)
Fields
Field Function
31-0 MISRRSWx
MISRRSWx Online MISR Read Low Bin
This field is equivalent to 32 bits of the MISR obtained at the end of the online LBIST execution.

#### 51.9.19 STCU2 Online LBIST MISR Read High (LB_MISRRHSW0)

Offset
Register Offset
LB_MISRRHSW0 22Ch
Function
The size of the register depends on the number of MISR bits of the related LBIST. .
The content of this register is initialized to its reset value when RUNSW[RUNSW] is set to 1.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R MISRRSWx
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R MISRRSWx
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 MISRRSWx
MISRRSWx Online MISR Read High Bits
This field is equivalent to 32 bits of the MISR obtained at the end of the online LBIST execution.

---

*Page 1501*

Self-Test Control Unit (STCU2)

#### 51.9.20 STCU2 Algorithm Select (ALGOSEL)

Offset
Register Offset
ALGOSEL 2200h
Function
This is a 32-bit register intended to be programmed by the user to select algorithms to be run on BIST. See the chip-specific
STCU2 information for details of this register.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
ALGO ALGO ALGO ALGO ALGO ALGO ALGO ALGO ALGO ALGO ALGO ALGO ALGO ALGO ALGO ALGO
SEL ... SEL ... SEL ... SEL ... SEL ... SEL ... SEL ... SEL ... SEL ... SEL ... SEL ... SEL ... SEL ... SEL ... SEL ... SEL ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
ALGO ALGO ALGO ALGO ALGO ALGO ALGO ALGO ALGO ALGO ALGO ALGO ALGO ALGO ALGO ALGO
SEL ... SEL ... SEL ... SEL ... SEL ... SEL ... SEL9 SEL8 SEL7 SEL6 SEL5 SEL4 SEL3 SEL2 SEL1 SEL0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Algorithm Select
31-0
ALGOSELn

#### 51.9.21 STCU2 MBIST Stagger (STGGR)

Offset
Register Offset
STGGR 220Ch
Function
This register allows one to program number of clock cycles between execution of one BIST and the next one.

---

*Page 1502*

Self-Test Control Unit (STCU2)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
STAG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
STAG
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 STAG
STAG Number of STCU2 CORE_CLK cycles between execution of one BIST and the next one.

#### 51.9.22 STCU2 BIST Start (BSTART)

Offset
Register Offset
BSTART 2210h
Function
This is a 32-bit register intended to be programmed by the user to run BISTs with different configuration. See the chip-specific
STCU2 information for details of this register.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
BSTA BSTA BSTA BSTA BSTA BSTA BSTA BSTA BSTA BSTA BSTA BSTA BSTA BSTA BSTA BSTA
RT31 RT30 RT29 RT28 RT27 RT26 RT25 RT24 RT23 RT22 RT21 RT20 RT19 RT18 RT17 RT16
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
BSTA BSTA BSTA BSTA BSTA BSTA BSTA BSTA BSTA BSTA BSTA BSTA BSTA BSTA BSTA BSTA
RT15 RT14 RT13 RT12 RT11 RT10 RT9 RT8 RT7 RT6 RT5 RT4 RT3 RT2 RT1 RT0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1503*

Self-Test Control Unit (STCU2)
Fields
Field Function
BIST Start
31-0
BSTARTn

#### 51.9.23 STCU2 MBIST Control (MB_CTRL0 - MB_CTRL11)

Offset
For a = 0 to 11:
Register Offset
MB_CTRLa 2214h + (a × 4h)
Function
The MB_CTRL register defines the control setting of MBIST controller.
The R/W fields in this register are readable at any time. You can write to these fields when online self-test phase is active and
CFG[WRP] = 0.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
CSM PTR BSEL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 CSM
CSM Concurrent/sequential mode
0b - Sequential mode
1b - Concurrent mode
30-21 PTR
Table continues on the next page...

---

*Page 1504*

Self-Test Control Unit (STCU2)
Table continued from the previous page...
Field Function
PTR Next LBIST or MBIST pointer
PTR defines the logical pointer to the next LBIST or MBIST to be scheduled. The next LBIST or MBIST is
scheduled concurrently to the current one if the CSM bit is set to 1; otherwise it is scheduled sequentially to
the completion of the current MBIST execution. In case of NIL pointer the CSM bit must be set Sequential
(0) to define this is the end of the list. The self-testing procedure stops after the last BIST in the configuration
chain is complete. See BIST scheduling for details.
NOTE
If the pointer is invalid and MBIST is scheduled to run concurrently than this invalid scenario
is handled by watchdog timeout feature and corresponding watchdog timeout status bit will
be updated in ERR_STAT[WDTOSW]. In this particular case ERR_STAT[INVPSW] will not
be set.
0h to (01h - 1): pointer to LBIST
00000080h to (00000080h + 00Ch - 1): pointer to MBIST
3FFh: pointer to NIL. No next BIST execution.
others: invalid pointer => an error is set into the ERR_STAT[INVPSW] .
20 BSEL
BSEL BIST Select
0b - Selected BIST is not selected for execution.
1b - Selected BIST is selected for execution.
Reserved
19-0
—

#### 51.10 Glossary

BIST Built-in self-test
BIST Clock BIST controller clock corresponding to the specific BIST
CORE_CLK Clock specified by the CFG register
FSM Finite state machine
IPS Internal peripheral system
LBIST Logic BIST
MBIST Memory BIST
NLBIST Number of logic built-in self-test controller
NMCUT Number of memory checked using memory built-in self-test controller
Online self-test phase In this condition, system is alive and the SW can program STCU2
RF Recoverable faults
UF Unrecoverable faults

---

*Page 1505*

Self-Test Control Unit (STCU2)
WDG FSM Watchdog finite state machine

---

*Page 1506*

