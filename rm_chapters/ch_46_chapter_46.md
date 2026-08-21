<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 46 -->

# Chapter 46

# Safety Overview

#### 46.1 Introduction

This chip family is developed following the IEC 61508 standards, with derivatives targeting the chips that are operable in a system
that fulfills the requirements of the SIL 2 safety integrity levels.
Table 219. SIL levels
Chip SIL level
MCXE315/MCXE316 2
MCXE317
MCXE31B
The SIL level targets the safety processing, which means the software functions are executed as intended:
1. Read instructions from memory.
2. Execute instructions.
3. Read data from memory.
4. Process data.
5. Write back the result data into memory.
Some elements of the safety concept are based on the assumption that the chip is connected to an external SBC or PMIC .
The SBC or PMIC performs observations and control functionalities that are essential to fulfill some related functional safety
requirements. If you do not use an SBC or PMIC, you must ensure that your MCXE31 family chip provides an equivalent
functionality that properly manages the corresponding interface(s).
The following figure illustrates the safety interface between the chip and the SBC or PMIC.
Safety
Chip
interface
Power supply
Internal PMC:
(VDD_HV_A, VDD_HV_B,
Voltage regulator Internal voltage
V1.5V)
VDD output generation: 1.1 V, 2.5 V,
redundant LVDs,
and single HVDs
SPI communication
External watchdog Software-driven SPI communication
RESET_b (bidirectional)
MC_RGM
1) External reset source
2) Output to external subsystem
FCCU monitoring FCCU drives the EOUT pads
FCCU_0 FCCU_0 actively during functional reset if
Internal chip safe state 1) dcf_client_utest_misc
FCCU_1 FCCU_1 [FCCU_EOUT_DEDICATED]
2) SW CONFIG (FCCU-CFG)
aaa-038719
Figure 174. Safety interface between the chip and the SBC or PMIC

---

*Page 1305*

Safety Overview
In the safety context, the chip interfaces can be classified into the groups shown in the table.
Table 220. Chip interface groups
Interface Description
Power supply This interface is between the SBC and the chip. It ensures
that the supply to the chip is in the correct range. In case
of any low-voltage event, the chip has POR, LVR, and LVD
circuits in place and in case of any HVD event, the chip
raises an interrupt. The SBC must ensure that the voltage
regulator outputs never exceed the allowed range (more
specifically for HVD range).
Communication Responsible for communication between the SBC and the
chip. Though the chip supports multiple communication
protocols (UART, LIN, SPI, I2C, FlexCAN, and so on), SPI
is the preferred communication with SBC. This is relevant to
initialize an external watchdog when the chip is inoperative
for a considerably long time (this is indicated by the pin states
of the communication interface).
Reset The chip consists of a reset bidirectional pin interfaced with
SBC. The SBC can initiate a chip reset via this pin as a
safety reaction in case of:
• Extreme critical faults
• An inoperative chip
• Stuck cases based on the criticality and the application
requirements
The SBC also samples the reset pin state to identify the chip
condition (whether in running state or in reset).
FCCU The chip indicates the chip faults to the SBC via FCCU
EOUT pins through this interface.
The interfaces in Table 220 ensure the chip's operational safety and integrity.

#### 46.2 Safety architecture elements

The chip safety architecture consists of following elements that operate in an interconnected way to meet the SIL requirements:
• Cortex-M7 core complex
• eDMA controller
• Structural core self-test
• Internal windowed watchdogs with independent clock sources
• Power supply monitoring with redundant low-voltage detectors, single high-voltage detectors, and internal ADC connection
to check the internal voltages during application
• Robust clock monitoring, including PLL loss of lock detection
• Embedded flash memory with ECC (single-bit data correction, double-bit data detection) and address encoding (parallel
address path check)
• System RAM with ECC
— Single-bit data correction

---

*Page 1306*

Safety Overview
— Double-bit data detection
— Address detection
• Cortex-M7 cache memories with ECC (single-bit data correction, double-bit data detection)
• Peripherals memories (EMAC, FlexCAN) with ECC (single-bit data correction, double-bit data detection)
• End-to-End EDC (E2E EDC), with address encoding and monitoring of the control signals done by a dedicated module
(XBIC). This ensures the safety of storage and of the data path to the internal storage (RAM, flash memory, core cache)
and peripherals across the crossbar switch. See the XBIC chapter for details.
• Hardware CRC module that supports end-to-end data check integrity for any data transfer in the system (SRAM to
peripherals via DMA transfer, external interfaces to SRAM/peripherals, and so on)
• Cortex-M7 MPUs
• XRDC for memory and peripheral protection
• AIPS_Lite peripheral protection with trusted master-slave connection
• Register protection mechanism for safety-critical registers
• On-chip temperature sensor for temperature monitoring
• Self-test:
— LBIST to detect hidden dangerous faults in functional logic as well as in safety integrity mechanisms
NOTE
LBIST is not supported in MCXE315/MCXE316 and MCXE317.
— MBIST to ensure integrity of the memories in the chip (SRAM, ITCM, DTCM, peripheral memories, and so on)
— Check-the-checker software library
— ADC self-test
• FCCU for error collection and reaction, including reporting error status to system; FCCU supports these programmable
reaction types:
— Interrupt
— Functional reset
• Error pads indicate the chip's internal state to the external chip interface or SBC.
• EIM to inject errors into the memories and interface gaskets to verify the error-detection features of the memory controllers
and the interface gaskets
• ERM to collect diagnostic information from memory controllers in case of an error event

#### 46.3 I/O peripherals

The arrangement of I/O peripherals across peripheral bridges allows redundant use of peripherals while limiting possible causes
of CCF . Redundant use includes using equivalent peripherals in a replicated way as well as using functionally different peripherals
in, for example, feedback measurement loops. Comparison of redundant operation is the responsibility of the application software,
not the safety hardware mechanism.
The peripherals are distributed evenly across the peripheral bridges (AIPS n ), except for singular modules like EMAC, QuadSPI,
and so on.
NOTE
EMAC and QuadSPI are not present in MCXE315/MCXE316 and MCXE317.

---

*Page 1307*

Safety Overview

#### 46.4 Self-test

The chip supports a self-test operation. Your safety software must initiate self-test by configuring STCU2; the chip does not initiate
it. STCU2 then controls the self-test operation. Self-test supports both MBIST and LBIST. After the self-test operation completes,
the chip enters a reset sequence. Self-test results are stored in STCU2 and your safety software can read the results after the
reset sequence.
Figure 175 depicts the processing steps related to the self-test operation, and its linkage to the chip reset and an application start.
You decide whether to run a self-test or to skip it before starting an application. You must also specify the self-test configuration
before relinquishing control to STCU2 for performing the self-test operation. This processing finishes by reentering the chip
reset sequence, depending on whether the chip encountered an unrecoverable fault during the self-test operation. When an
unrecoverable fault is encountered during the self-test operation, the chip enters a reset sequence by performing a destructive
reset. When no such fault is encountered, the self-test operation completes by entering the chip sequence with a functional reset
sequence. You can prevent reset cycling by limiting the amount of resets permitted; the chip shuts down when this limit has been
reached. See the "Functional reset escalation" and "Destructive reset escalation" sections in the MC_RGM chapter.

---

*Page 1308*

Safety Overview
Power-on reset
Chip reset sequence
Destructive reset
sequence
STCU2 encounters an unrecoverable fault and
generates a destructive reset.
Functional reset
sequence Self-test completion results in a functional reset.
Out of reset
Yes
Too many resets? Chip shutdown
No
Software reads STCU2
and MC_RGM registers.
Is previous reset Yes Software checks the
due to self-test? LBIST or MBIST results
No
STCU2 is under destructive reset.
The self-test configurations and the results Software decides: No Software starts application
(assuming self-test execution was successful) run the self-test? execution
remains preserved across self-test within STCU2.
Yes
Software configures STCU2
for self-test execution.
STCU2 exectutes
LBIST or MBIST
Does STCU2
Yes No
encounter any
unrecoverable
fault?
Figure 175. LBIST/MBIST execution
Figure 176 visualizes the top-level partitioning of the chip. The chip consists of two partitions, Run and Standby.
• Run: This partition consists of logic which is present in switchable domain and is shut off (has no supply) while the chip
operates in low-power (Standby) mode.
• Standby: This partition consists of always-on logic which is functional even while the chip operates In low-power
(Standby) mode.
The Run partition also contains the control logic that is essential for the chip self-test operation, as well as blocks that undergo
self-test. The chip consists of a single LBIST partition, which is a subpartition within the Run partition. The LBIST subpartition

---

*Page 1309*

Safety Overview
contains the logic over which self-test is executed. The logic outside the LBIST subpartition and within the Run partition consists
of the LBIST control logic as well as logic which does not undergo LBIST.
The components within these different partitions are listed below the figure (they are not indicated in the figure for clarity).
Run
LBIST
Standby
Figure 176. MCXE31B chip top-level partition view
The following list specifies the content of the LBIST partition and both power domains related to their participation in the
self-test operation:
• Modules participating in the LBIST operation:
NOTE
LBIST is not supported in MCXE315/MCXE316 and MCXE317. The modules participating in the LBIST operation
are out of LBIST in MCXE315/MCXE316 and MCXE317.
— eDMA
— EIM
— ERM
— XRDC
◦ MDAC0 (Cortex-M7_0)
◦ MDAC1 (eDMA)
◦ MDAC2 (reserved)
◦ MDAC3 (ELE_HSEB)
◦ MDAC4 (Cortex-M7_1)
◦ MDAC5 (EMAC)
◦ MRC0 (flash memory)
◦ MRC1 (PRAM)
◦ MRC2 (QuadSPI)
NOTE
Cortex-M7, EMAC, and QuadSPI are not present in MCXE315/MCXE316 and MCXE317.
◦ PDAC1
◦ PDAC2
— CRC
— FCCU

---

*Page 1310*

Safety Overview
— FOSU
— PFLASH_CTL
— PRAM_C0
— PRAM_C1
— SWT_1
— CMU
◦ CMU_FC_0
◦ CMU_FM_1
◦ CMU_FM_2
◦ CMU_FC_3
◦ CMU_FC_4
◦ CMU_FC_5
— IAHB gaskets
NOTE
An IAHB gasket is a frequency translation gasket in the system. These gaskets are used to add register walls and
synchronize the timing domain across different operating frequencies. See the configurable gaskets in the 'Block
diagram' in 'Introduction' chapter for the locations of the IAHB_GASKETs in the chip.
◦ DMA_GSKT
◦ HSE_GSKT
◦ AIPS1_GSKT
◦ AIPS2_GSKT
◦ QSPI_GSKT
NOTE
AIPS2_GSKT and QSPI_GSKT are not present in MCXE315/MCXE316 and MCXE317.
◦ TCM_GSKT
◦ EMAC_GSKT
◦ EDC master checker gaskets (See block diagram in Introduction chapter for details)
◦ EDC slave checker gasket (See block diagram in Introduction chapter for details)
— Crossbar switches and bridges
◦ AXBS_0 (main)
◦ AXBS_1 (peripheral)
◦ AXBS_2 (eDMA)
◦ AXBS_3 (Cortex-M7 TCM)
◦ AXBS_4 (HSE)
◦ AIPS_Lite_1
◦ AIPS_Lite_2
NOTE
AIPS2_Lite_ is not present in MCXE315/MCXE316 and MCXE317.

---

*Page 1311*

Safety Overview
• Modules within the Standby domain (not participating in the LBIST operation):
— 32 KB standby SRAM
— LPCMP_0–LPCMP_2
— Reset generation
◦ MC_RGM
◦ POR_WDG
— Power management
◦ PMC
◦ MC_PCU
◦ WKPU
◦ Chip standby pins
— Clock sources
◦ FIRC
◦ SIRC
◦ FXOSC
◦ SXOSC
NOTE
SXOSC is not present in MCXE315/MCXE316.
— Timers
◦ PIT_0
◦ RTC
◦ SWT_0
• Modules not participating in the LBIST operation (in the Run domain):
— HSE subsystem
◦ Engine
◦ Memories
— Cortex-M7_0
◦ Core
◦ I-cache
◦ D-cache
◦ ITCM
◦ DTCM
— Cortex-M7_1
NOTE
Cortex-M7_1 is not present in MCXE315/MCXE316 and MCXE317.
◦ Core
◦ I-cache

---

*Page 1312*

Safety Overview
◦ D-cache
◦ ITCM
◦ DTCM
— CMUs
◦ CMU0
◦ CMU3
— Analog blocks
◦ ADC_0–ADC_2
◦ TempSense
NOTE
ADC_2 is not present in MCXE315/MCXE316 and MCXE317.
— Communication modules
◦ LPSPI_0–LPSPI_5
◦ LPI2C_0
◦ LPI2C_1
◦ LPUART_0–LPUART_15
◦ FlexCAN_0–FlexCAN_5
◦ SAI_0
◦ SAI_1
NOTE
SAI_0 and SAI_1 are not present in MCXE315/MCXE316 and MCXE317.
— Timers
◦ PIT_1
◦ PIT_2
◦ eMIOS
◦ BCTU
◦ STM_0
◦ STM_1
NOTE
PIT_2 and STM_1 are not present in MCXE315/MCXE316 and MCXE317.
— TRGMUX
— PLLDIG
— SIUL2
— LCU_0
— LCU_1
— STCU2
— DMAMUX

---

*Page 1313*

Safety Overview
— MCM
— MCSM
— INTM
— SEMA42
— MU
NOTE
SEMA42 and chip MU are not present in MCXE315/MCXE316 and MCXE317.
— TSPC
NOTE
REG_PROT of an IP undergoes (or does not undergo) LBIST in conjunction with the protected module that
undergoes (or does not undergo) LBIST.
The modules that are vital to the self-test operation are excluded from LBIST regions to allow LBIST to execute successfully.
You must run self-test with PLLDIG configured as the system clock. The LBIST clock controller controls the clock during serial
shift, but returns clock control to the functional nodes during the self-test.

#### 46.5 Glossary

SIL SIL / Safety Integrity Level. This is a risk classification scheme as defined by IEC 61508 functional
CCF Common cause failure
DTCM Data tightly coupled memory
ECC Error correction code
ITCM Instruction tightly coupled memory
LBIST Logic built-in self-test
MBIST Memory built-in self-test
PLL Phase-locked loop oscillator
PMIC Power management integrated chip
SBC System basis chip

---

*Page 1314*

