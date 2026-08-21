<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 75 -->

# Chapter 75

# Debug Subsystem

#### 75.1 Introduction

®
This chapter discusses the debug and trace architecture of the chip that is based on the specifications provided in the Arm
™
CoreSight SoC-400 Technical Reference Manual . See References for a link to this document and to other related documentation
available on the Arm website.
The chip architecture includes debug and trace modules. See Features for details on the debug and trace features that Cortex-M7
core clusters support.
The debug components that the Cortex-M7 core supports are accessible via the Arm DAP controller-based architecture. The DAP
controller works in parallel with the system JTAGC. Both these controllers share the JTAG port and JTAG instruction set.
The system components are similar for different chips in the same family and include the primary core debug interfaces, the
chip-level debug interfaces, and chip-level trace interfaces. The accelerator components include debug and trace circuits. These
circuits are necessary for any application-specific accelerators required for the different application spaces that a chip supports.
They vary from one chip to another and could include only trace components.

#### 75.2 Interfaces supported in MCX E31 family

Table 580. Interfaces supported in MCXE31 family
MCXE315/MCXE316/MCXE317 MCXE31B
Cortex-M7_0 (CTI, DWT, BPU, ITM) Cortex-M7_0 (CTI, DWT, BPU, ETM, ITM)
- -
- -
Cortex-M0+ Cortex-M0+
- HTM (CTI+DMA/EMAC TRACE)
SWO SWO
- TPIU
- ETF (4)
- FUNNELs (3)
CTI (2) CTI (3)
CTM (1) CTM (1)
Timestamp Timestamp
MDM_AP MDM_AP
SDA_AP SDA_AP

#### 75.3 Block diagram

This figure illustrates the DAP architecture of the MCX E31 family.

---

*Page 3252*

Debug Subsystem
DAP/TAP JTAG pins
SWJ-DP
DAPMUX
AHB_AP AHB_AP
JTAGC APB-AP MDM_AP
AHB_AP
Cortex-M0+
Boundary
scan
Time
HTM64
stamp SDA_AP
Security
System
Cortex-M7_0
JDC
JDC ELE_HSEB
ETF_x
CSTF_x
System interconnect (AHB)
TPIU
IPS2APB
CTl_x
System debug APB interconnect (APBIC)
SWO
Figure 437. DAP architecture
NOTE
Debug components other than the Cortex-M0+ core are accessible through software path (CORE > AXBS > AIPS >
DAPMUX > APs), that is, by directly specifying the memory mapped addresses without doing challenge response.

#### 75.4 Features

This chip includes these features that support the functions listed under each feature type:
• Debug control
— Is implemented via IEEE 1149.1-compliant JTAG
• Test control
— Is implemented via IEEE 1149.1-compliant JTAG
— Uses IEEE 1149.6 extension to IEEE 1149.1
• Trace interface
— Includes four high-speed data pads and one clock pad @120 MHz (120 MB/s throughput per pad)
— Includes 16 low-speed data pads and one clock pad @25 MHz (100 MB/s throughput per pad)
• Debug security

---

*Page 3253*

Debug Subsystem
— Includes a DAP/TAP interface that controls all debug features and is gated by Cortex M0+ JDC module
— Includes security modes as described in the "Security" chapter
• Debugging and run control
— Is implemeted through stopping points, starting points, breakpoints, and watchpoints
— Cortex- M7 core supports eight instruction comparators and a watchpoint unit having four watchpoints
• Trace source
— Includes Cortex-M7:
◦ ETM that provides instruction and data trace
◦ ITM that provides DWT, time-stamping, and diagnostic information
• Cross-triggering support
— Controls run-control options of cores based on other cores
— Provides a matrix having connections to:
◦ HTM via system CTI
◦ Cortex-M7 CPUs
• Ability to view and modify all memory-mapped areas that are not otherwise blocked—includes a system bus debug access
port
• Performance monitoring of cores—implements a performance monitoring unit (PMU) on each Cortex core
• Safety
— Supports monitoring of debug signals to avoid common mode faults
— Allows checking of erroneous activation of debugging, especially if intrusive (for example, a CPU entering Debug
state)
• Timestamps
— Generates a timestamp bus for distribution to the trace sources
— Includes a 48-bit binary timestamp bus
— Clocks timestamp generator by a frequency of 160 MHz

#### 75.5 Debug

#### 75.5.1 TAP connectivity

This figure shows a detailed view of TAP connectivity. JTAG select, SWJ-DP , and JTAG-DP are parts of DAP.

---

*Page 3254*

Debug Subsystem
JTAGC Arm JTAG-DP
JTAG-mode instruction loaded
SWD-JTAG TCK
switcher TMS TDO
TDI
0
TCK TDO
JTAG-DP
1
TMS TCK
TMS TDO
TDI TDI
SWJ-DP
DBGCLK
DBGDI
DBGDOEN
DBGDO
Figure 438. TAP connectivity
The debug port comes out of reset in a standard JTAG mode. It switches to another mode by using the change sequences. After
the mode changes, unused debug pins can be reassigned to any of their alternative muxed functions.
JTAG-DP and JTAGC are connected in an overlay scheme and both have an Instruction Register (IR) length of 8 bits.

#### 75.5.2 DAP TAP

DAP is an Arm component that provides multiple-master driving ports. A single external interface port accesses and controls these
ports to provide system-wide debug.
The DAP Instruction Register (DAP IR) overlays with the system JTAG Instruction Register (JTAGC IR). Table 581 presents DAP
instructions. In addition to the four codes listed in the table, DAP uses BYPASS, which is identical to JTAGC BYPASS and is
therefore not shown in the table.
Table 581. DAP IR codes
Code DAP IR
1111_1000b ABORT
1111_1010b DPACC
1111_1011b APACC
1111_1110b IDCODE
DAP offers an AHB master interface to access system buses. It also exports the internal DAP bus to extend the access ports. For
more information on DAP TAP, see the Arm Debug Interface Architecture Specification document available in References .
In this chip:
• The AHB slave ports of all Cortex-M x cores provide debugger access to all memory units and registers in the system.
• The new pass-through approach allows the debugger to see a cache coherent view of the memory map for that core.
• The debugger must access the corresponding AHB_AP port to access the AHB-S port of that subsystem.
• XRDC controls system access.
• A core can access the debug components of another core through a DAPMUX to the bus interconnect.
• The exported DAP bus hosts AHB_AP and MDM_AP .

---

*Page 3255*

Debug Subsystem
— MDM_AP hosts system-level JTAG status and control registers (see MDM_AP register descriptions ) . These registers
can be used for cross triggering, synchronized debug, and other miscellaneous control and status functions.
— APB_AP uses an APSEL value of 1h to access any APB -mapped debug modules.
Table 585 describes the access addresses of APB-mapped debug modules. The value of the DAP select signal in the APSEL field
of SWJ-DP's Select register selects the access ports in the DAP. Table 582 shows APSEL decoding.
Table 582. DAP master address mapping
DAP Applicability
System memory map address
DAPBUS base
component
address Selected port or
(access from cores)
DAP master #
(access from master
1
debugger)
Page number Base address
Reserved -
0h 0000_0000h 0 4025_0000h Reserved
APB_AP to all MCXE31B (for
APB_AP to all
APB-mapped ETF, HTM, and
APB-mapped
debug modules CSTF see
debug modules
(for example, Table 580 )
1h 0100_0000h 0 4025_0100h (for example,
ETF s, HTM,
ETFs, HTM,
and trace
CSTF , and so
funnels)
on)
Reserved -
2h 0200_0000h 0 4025_0200h Reserved
AHB_AP to All
AHB_AP to
Cortex-M7_0
Cortex-M7_0
debug modules
4h 0400_0000h 0 4025_0400h debug modules
and chip system
and chip system
memory map
memory map
AHB_AP to MCXE317
AHB_AP to
Cortex-M7_1
Cortex-M7_1
debug modules
5h 0500_0000h 0 4025_0500h debug modules
and chip system
and chip system
memory map
memory map
MDM_AP All
6h 0600_0000h 0 4025_0600h MDM_AP
SDA_AP All
SDA_AP used
for challenge-
7h 0700_0000h 0 4025_4700h response (CR)
in SWJ-DP
mode
Reserved -
8h—FFh (default AP — — — —
response)
1. For example, 4000_XXYYh address is provided when accessing access ports (APs) via the debugger. Here, XX shows the
AP select number and YY shows the address to be accessed. If you access SDA_AP via the debugger on 80h, you must
provide the address 4000_0780h.

---

*Page 3256*

Debug Subsystem
NOTE
Accessing SDA_AP simultaneously from the core and the debugger is prohibited. If you try to do so, you may
receive unpredictable responses to core-initiated transactions (for example, incorrect data read, a failed attempt
to write to the register, or a transfer error). Debugger-initiated transactions proceed correctly.

#### 75.5.3 System JTAGC

JTAGC connects in parallel with the TAP controller (JTAG-DP of DAP), which has an IR length of 8 bits. The JTAGC IR codes
overlay the ones of the DAP controller. DAP uses four instructions and JTAGC uses the remaining ones. The TAP outputs (TPOs)
are multiplexed based on the selected IR code. This chip is fully JTAG-compliant and appears as a single TAP to the JTAG chain.
This table shows the JTAGC IR codes. The instructions that are used by Arm DAP TAP are shown in Table 581 .
Table 583. JTAG instructions for JTAGC
Code JTAGC IR
0000_0000b IDCODE
0000_0001b Reserved
0000_0010b SAMPLE/PRELOAD
0000_0011b SAMPLE
0000_0100b EXTEST
0000_0101b HI-Z
0000_0110b Reserved
0000_0111b Reserved
0000_1000b—0000_1001b Reserved
0000_1010b—0000_1011b Reserved
0000_1100b CLAMP
0000_1101b ENABLE_SOC_DATA1
0000_1110b Reserved
0000_1111b Reserved
1000_0000b Reserved
1000_0001b Reserved
1000_0010b Reserved
1000_0011b Reserved
1000_0100b Reserved
1000_0101b Reserved
Table continues on the next page...

---

*Page 3257*

Debug Subsystem
Table 583. JTAG instructions for JTAGC (continued)
Code JTAGC IR
1000_0110b—1000_0111b Reserved
1000_1000b Reserved
1000_1001b Reserved
1000_1010b—1000_1111b Reserved
1001_0000b Security JDC
1001_0001b System JDC
1001_0010b—1001_0111b Reserved for other system auxiliary clients
1001_1000b—1001_1011b Reserved
1001_1100b—1011_1111b Reserved for other system auxiliary clients
Reserved
1100_0000b
1100_0001b Reserved
Reserved
1100_0010b—1110_1111b
1111_1000b ABORT (Arm)
1111_1001b Reserved
1111_1010b DPACC (Arm)
1111_1011b APACC (Arm)
1111_1100b Reserved
1111_1101b Reserved
1111_1110b IDCODE (Arm)
1111_1111b BYPASS
75.5.3.1 Chip JTAG/Target ID
Each chip in the MCXE31 family includes a unique JTAG ID, which must be changed when instantiated with a different die in an
SiP . The next table shows the JTAGC ID for this chip.
Table 584. JTAG/Target ID
Chip PRN DC PIN MIC IDCODE ID JTAG ID Target ID
MCXE31B 0 26h (38d) 160h (352d) Eh (14d) 1 0996_001Dh 0996_001Dh
MCXE315/ 0 26h (38d) 16Ch (364d) Eh (14d) 1 0996_C01Dh 0996_C01Dh
MCXE316
MCXE317 0 26h (38d) 168h (360d) Eh (14d) 1 0996_801Dh 0996_801Dh

---

*Page 3258*

Debug Subsystem
75.5.3.2 JDC
JDC allows you to access two 32-bit data registers by using the JTAG interface and by software running on one of the CPUs in
the chip. These registers exchange data between an internal CPU and an external debug tool.

#### 75.5.4 Peripheral IPG debug implementation

MCXE31 is a multi-core chip. Any core can control a peripheral instance individually. You define the core's control over a peripheral
during application development.
For peripheral halt, all individual cores have the same capability and are gated by a dedicated MDM_AP core halt register field.
For more information, see the configurations defined in MDM_AP register descriptions .
Cortex-M7_0 is halted
Cortex-M7_0
Cortex-M7_1 is halted
Cortex-M7_1
Peripheral 0
Not available on the
MCXE315/MCXE316
MCXE317/MCXE31B
GPR_CORE1_DBGFRZ0[P0]
Cortex-M7_2 is halted GPR_CORE0_DBGFRZ0[P0]
GPR_CORE2_DBGFRZ0[P0]
Figure 439. Peripheral IPG debug implementation

#### 75.5.5 Application debugging

This section covers the following two cases:
• Case 1: Debugger connected—application debugging from the first instruction
• Case 2: Debugger not connected
75.5.5.1 Application debugging from first instruction
This chip supports debugging from the first instruction on system power-up, destructive reset, functional reset, and standby exit.
However, by default, debugging from the first instruction is disabled.
The next figure shows the timing diagram of application debug implementation from first instruction on system power-up or
destructive reset.

---

*Page 3259*

Debug Subsystem
Debugger keeps the chip in reset by pressing reset pin
Debugger configures to hold app core’s reset
Debugger releases the chip’s reset pin; app core still in reset
BAF writes expected response; status in SDA_AP
BAF enables app coreCCTL
Debugger provides the expected response and CR passes status to SDA_AP
Debugger configures the core’s debug and releases the core’s resets
SDA_AP [Reset_Release]
Reset_b pad
Valid challenge
CCTL
app dbg en
Debugger decision must be configured before Before app dbg en becomes 1, the debugger has
reset deassertion because the debugger doesn’t no access to the chip except for some SDA_AP
know when BAF enables CCTL. response, status, and reset-release registers.
Figure 440. Application debug implementation from first instruction
These are the steps involved in enabling application debug from the first instruction on system power-up or destructive reset:
1. After the reset is applied, the debugger provides an option to debug from the first instruction. To do this:
a. Write 1 to SDAAPRSTCTRL[RSTRELTLCM70] and SDAAPRSTCTRL[RSTRELTLCM7] .
b. Set the reset value of Reset Control (SDAAPRSTCTRL) .
2. Cortex M0+ starts after the reset pin is released.
3. Debugger challenge-response (CR) starts.
4. Cortex-M7_0 and Cortex-M7_1 remain in reset until MC_ME's PRTN0_CORE0_PCONF[CCE] field becomes 1 for
Cortex-M7_0 and PRTN0_CORE1_PCONF[CCE] field becomes 1 for Cortex-M7_1.
5. When BAF or FW writes 1 to MC_ME's PRTN0_CORE0_PCONF[CCE] field for Cortex-M7_0 and to
PRTN0_CORE1_PCONF[CCE] field for Cortex-M7_1, and the debugger CR has passed:
• If the value of SDAAPRSTCTRL[RSTRELTLCM71] and SDAAPRSTCTRL[RSTRELTLCM70] is 0 for their
respective cores, the debugger configures the core debug registers and then enables these fields that start the
code execution.
• If the value of SDAAPRSTCTRL[RSTRELTLCM71] and SDAAPRSTCTRL[RSTRELTLCM70] is 1 for their
respective cores, the debugger configures the core debug registers and then enables these fields that start the
code execution.
To debug from the first instruction during the low-power debug protocol, the debugger:
1. Writes 0 to SDAAPRSTCTRL[RSTRELTLCM71] and SDAAPRSTCTRL[RSTRELTLCM70] for their respective cores.
2. Writes 1 to MDMAPWIRREL[WTRSTRGM] .
75.5.5.2 Debugger not connected
If the debugger is not connected, the value of the DBGPWRUP_ACK signal is 0 as there is no DBGPWRUP_REQ. This results
in the following scenario:
1. The booting core writes 1 to MC_ME's PRTN0_CORE0_PCONF[CCE] field for Cortex-M7_0 and to
PRTN0_CORE1_PCONF[CCE] field for Cortex-M7_1.

---

*Page 3260*

Debug Subsystem
2. The application core starts running. In case of Lock-step mode, the checker core executes the same code after a delay of
two cycles.

#### 75.5.6 Debugger considerations while flash program/erase

The flash programming can be done by application cores as well as debugger. The debugger can program/erase flash using either
of the following two options:
1. Loading the program/erase code to SRAM from the debugger and then executing the program/erase sequence by
application code from SRAM.
• Debugger is connected and authenticated.
• The debugger loads the code in the form of application binary image into the on-chip SRAM.
• The debugger then initiates the application core to execute the binary from SRAM.
2. Debugger executing the program/erase sequence.
• Debugger is connected and authenticated.
• The debugger then initiates the flash programming by reading/writing the registers involved and following the program/
erase sequence.
First option is the recommended because of less execution time. In second option, the debugger is the master and since the debug
interface is serial, the execution takes more time.
In some cases, you might experience flash program/erase failure due to flash watchdog timeout when debugger executes the flash
program/erase sequence due to the serial interface. Therefore, it is recommended to use the first option. In case second option
is required, it should be performed in reduced clocking options (Option E and E2 only. For details, refer to the Clocking details
section in the Clocking chapter).

#### 75.5.7 SWJ-DP sequence for debug authentication

To perform the SWJ-DP sequence for debug authentication, the debugger:
1. Polls the AUTHSTTS[CHALRDY] field until it indicates the challenge status.
2. Reads Key Challenge (KEYCHAL0 - KEYCHAL7) if the challenge is valid and creates an authenticated 256-bit
response key.
3. Writes a 256-bit response key to Key Response (KEYRESP0 - KEYRESP7) .
4. Indicates that the response is ready by configuring AUTHCTL[HSEAUTHREQ] .
5. Checks the status of Authentication Status (AUTHSTTS) to evaluate if the operation is successful.
If the authentication process is successful and the debugger has write access to Debug Enable Control (DBGENCTRL) ,
the challenge or response is considered successful too.
NOTE
SDA_AP supports challenge and response based on both JTAG and SWJ-DP modes. See
SDAAPGENCTRL0[JTAG_CR_EN] for details.

#### 75.6 APB memory map

You can access the debug registers via the APB_AP bus. The next table shows all the addresses for the CoreSight APB
components and the addressing used for accessing the DAP components via the memory interface. You can also access all APB
registers from the processing cores.

---

*Page 3261*

Debug Subsystem
Table 585. APB components mapping
Debug APB APBIC base Memory APB-AP0 slot Applicability
System memory map address
component address allocation (KB) number
(access from cores)
(access from
debugger)
Page number Base address
APB_AP ROM F8h 0 4024_00F8h 4 APBIC base All
table
Funnel 0 1000h 0 4024_1000h 4 0
ETM/ITM:
MCXE31B
ITM: MCXE315/
MCXE316 and
MCXE317
Funnel 1 2000h 0 4024_2000h 4 1 MCXE31B
Funnel 2 3000h 0 4024_3000h 4 2 MCXE31B
CM7_cluster_E 4000h 1 4024_4000h 4 3 MCXE31B
TF_ETMI
CM7_cluster_E 5000h 1 4024_5000h 4 4 MCXE31B
TF_ETMD
HTM ETF 6000h 1 4024_6000h 4 5 MCXE31B
Shared_system 7000h 1 4024_7000h 4 6 MCXE31B
_ETF
HTM 0 8000h 2 4024_8000h 4 7 MCXE31B
HTM 0 CTI 9000h 2 4024_9000h 4 8 All
TPIU A000h 2 4024_A000h 4 9 MCXE31B
System SWO B000h 2 4024_B000h 4 10 All
Timestamp CTL C000h 3 4024_C000h 4 11 All

#### 75.7 Trace

#### 75.7.1 Trace modules and connectivity

The Trace subsystem:
• Combines trace data from all internal clients that generate trace information
• Includes a 32-bit TPIU
• Includes these components:
— ATB
— ATBR
— CSTF
— Debug APB
— ETF
— TPIU

---

*Page 3262*

Debug Subsystem
Multiple options for trace output allow parallel tracing. Trace information can be read from the trace interface or the DAP interface,
and traces can be alternatively read out from ETF at a slow speed via APB_AP. Table 586 shows EFT sizes.
Table 586. ETF sizes
FIFO Memory interface data width (in bits) Size (KB)
Cortex-M7 ETM/ITM cluster ETF 64 1
Cortex-M7 ETMD cluster ETF 128 2
HTM ETF 64 1
Shared system ETF 64 2
NOTE
• The DMA-HTM trace supports four words at 80 MHz. For HTM trace, both DBGENCTRL[GSPNIDEN] and
DBGENCTRL[GSPIDEN] must be 1.
• Enabling any one of data trace causes overflow inside ETM and trace packets get dropped. Enabling
instruction trace of both the core simultaneously does not cause any overflow.
Control (MDMAPCTL) provides fields to override the speed control (see Table 587 for details) from some of the trace sinks (and
TPIU). The complete trace pipeline bandwidth is limited by the slowest sink component. The default settings of these fields allow
maximum bandwidth for the TPIU to trace. When tracing to memory (if supported), the fields may need to be changed.
Table 587. Trace output overrides
Trace destination SWO_override TPIU_override
TPIU 1 0
The trace sources of the chip are the cores and their related modules. Trace funnels exist for all possible trace clients. For more
information on the various components in the trace bus connectivity, see the CoreSight Components Technical Reference Manual
(available in References ). See Table 588 for details on funnel assignments.
The ATBR is integrated to send a shared funnel output across TPIU. This figure illustrates the chip's detailed trace architecture.

---

*Page 3263*

Debug Subsystem
Trace @ 50 MHz DDR: 50 MHz clock logic Trace CLKOUT: 25 MHz
240 MHz Trace @ 120 MHz DDR: 120 MHz clock logic Trace CLKOUT: 60 MHz
Trace @ 120 MHz DDR: 240 MHz clock logic (room temperature only) Trace CLKOUT: 120 MHz
8 U 32
Cortex-M7_0 ETMI
P 32 ETF 16 Async 32
Funnel 0
1 KB bridge
8 U 32
Cortex-M7_0 ITM
P
32 CLKOUT
TPIU
Funnel 2 32 ETF3 160 M 120/25 M
A B
32-bit 2 KB 32
Repilcator
32 8
DN SWO
64 64 ETF 64 32 Async
Cortex-M7_0 ETMD Funnel 1 DN
2 KB bridge
AXBS masters
DMA
32 ETF Async 32
HTM
1 KB bridge
EMAC
Figure 441. MCXE31B detailed trace architecture
120 MHz 25 MHz
8 8
Cortex-M7_0 ITM A B SWO
Async
bridge
Figure 442. MCXE315/MCXE316 and MCXE317 detailed trace architecture
Table 588. Funnel assignments
Funnel Port Input MCXE314 Applicability
Frquency (MHz)
0 0 Cortex-M7_0 ETMI 160 MCXE31B
0 3 Cortex-M7_1 ITM 160 All
0 4 Cortex-M7_2 ETMI - MCXE314
0 6-7 Reserved - -
1 0 Cortex-M7_0 ETMD 160 MCXE314, MCXE31B
1 3-7 Reserved - -
2 0 Cortex-M7 instruction ETF through 160 MCXE314, MCXE31B
an asynchronous bridge
2 1 Cortex-M7 data ETF through an 160 MCXE314, MCXE31B
asynchronous bridge
Table continues on the next page...

---

*Page 3264*

Debug Subsystem
Table 588. Funnel assignments (continued)
Funnel Port Input MCXE314 Applicability
Frquency (MHz)
2 2 HTM ETF through an asynchronous 160 MCXE314, MCXE31B
bridge
2 3-7 Reserved - -
75.7.1.1 Chip's bus trace client
Chips in the MCXE31 family include a bus trace client.
HTM provides the address and data trace information about AXBS buses. The information from an HTM can be used with the
debugger to enable easy, accurate debugging on AXBS-based embedded systems. The chip implements an HTM64 configuration
to trace 64-bit AXBS masters in the system. To simplify the implementation, instead of tracing the individual ports of various AXBS
masters and slaves, which may be running at different frequencies, HTM64 snoops the AXBS crossbar master ports that are all
synchronous with a 160 MHz system clock. This table provides details related to the HTM input connectivity.
Table 589. HTM connections
HTM64 port AHB crossbar port
HTMBUSSELECT0 M0 AXBS_Lite XBIC (DMA)
HTMBUSSELECT1 M3 AXBS (Ethernet)
75.7.1.2 TPIU interface
A standard 16-bit parallel TPIU is integrated into the debug subsystem. Chips in the MCXE31 family generate the trace via the
trace port.
To prevent instruction trace from being dropped in a multi-core environment, a TPIU throughput of 55.2 MB/s or higher must be
maintained. You could use these pin and frequency combinations:
• Four high-speed data + one clock pads @120 MHz (240 Mbit/s throughput per pad) totaling 120 MB/s
• 16 low-speed data + one clock pads @25 MHz (50 Mbit/s throughput per pad) totaling 100 MB/s
High-end chips from the MCXE31 family support at least 56 MB/s of trace throughput on the TPIU interface.
Table 590. Throughput
Core Throughput value Applicability
Cortex-M7_0 27.6 MB/s All
Cortex-M7_1 27.6 MB/s MCXE31B
Total 82.8 MB/s
75.7.1.3 TPIU flush
To allow the chip to enter Standby mode, software executes an WFI instruction indicating Standby entry. When the chip enters this
mode, MC_RGM asserts a trace flush request to the TPIU and waits for a trace flush done signal from the TPIU before requesting
MC_PCU to proceed. The TPIU and debug infrastructure clocks can be gated after this. Because the TPIU is sourced from system
clock, the clock must not be gated until this point.

---

*Page 3265*

Debug Subsystem

#### 75.7.2 Trace assignments

ETF combines trace information from multiple clients into a single stream of trace data. This table shows the CoreSight funnel
input assignments.
Table 591. CoreSight funnel assignments
Instance name Port assignment Applicability
Funnel 0 (CSTF0) Cortex-M7_0 ETMI MCXE31B
Cortex-M7_0 ITM All
Cortex-M7_1 ETMI MCXE31B
Cortex-M7_1 ITM MCXE317
Funnel 1 (CSTF1) Cortex-M7_0 ETMD MCXE31B
Funnel 2 (CSTF2) Funnel 0 MCXE31B
Funnel 1 MCXE31B
HTM MCXE31B

#### 75.8 Embedded cross trigger (ECT)

ECT allows multi-core run control and trace cross-triggering, such as synchronous stop-start for all cores or trigger trace on a
trigger event from another core or module. See the CoreSight Components Technical Reference Manual (available in References )
for detailed information on ECT.
ECT architecture involves CTMs and CTIs. The CTIs provide a cross-triggering interface between the cores and other debug and
trace modules. The channels of these CTIs are interconnected using CTMs, as shown in this figure.
CTM 0
CH 0 CH 2 CH 3
CTI0
CTI off-pf
Cortex-M7_0 CTI
Cortex M0+ core
7:3 CTITRIGIN[2] CTITRIGIN[1] CTITRIGIN[0] CTITRIGOUT[2] CTITRIGOUT[1] CTITRIGOUT[0]
5 bits
off_pf
triggers
HTMEXTIN[1] HTMEXTIN[0]
HTMTRIGGER
HTMEXTOUT[0] HTMEXTOUT[1]
HTM
Figure 443. ECT

---

*Page 3266*

Debug Subsystem

#### 75.8.1 CTI assignments

Table 592. CTI assignments
CTI instance Trigger number Trigger in Trigger out
CTI_Cortex-M7_0 7 ETM event output 3 Processor restart
6 ETM event output 2 ETM event input 3
5 ETM event output 1 ETM event input 2
4 ETM event output 0 ETM event input 1
3 DWT comparator output 2 ETM event input 0
2 DWT comparator output 1 Interrupt request 1
1 DWT comparator output 0 Interrupt request 0
0 Processor halted Processor debug request
CTI_Cortex-M7_1 7 ETM event output 3 Processor restart
6 ETM event output 2 ETM event input 3
5 ETM event output 1 ETM event input 2
4 ETM event output 0 ETM event input 1
3 DWT comparator output 2 ETM event input 0
2 DWT comparator output 1 Interrupt request 1
1 DWT comparator output 0 Interrupt request 0
0 Processor halted Processor debug request
CTI_Cortex-M7_2 7 ETM event output 3 Processor restart
6 ETM event output 2 ETM event input 3
5 ETM event output 1 ETM event input 2
4 ETM event output 0 ETM event input 1
3 DWT comparator output 2 ETM event input 0
2 DWT comparator output 1 Interrupt request 1
1 DWT comparator output 0 Interrupt request 0
0 Processor halted Processor debug request
CTI_0 7 Reserved (grounded) Reserved (no connection)
6 ETF_3 full ETF_3 trigger input
5 ETF_2 full ETF_2 trigger input
4 ETF_1 full ETF_1 trigger input
3 ETF_0 full ETF_0 trigger input
2 HTM trigger out 2 HTM trigger in 2
Table continues on the next page...

---

*Page 3267*

Debug Subsystem
Table 592. CTI assignments (continued)
CTI instance Trigger number Trigger in Trigger out
1 HTM trigger out 1 HTM trigger in 1
0 Reserved HTM trigger in 0

#### 75.9 Low-power debug handshake protocol

The debugger must perform this sequence to enter or exit Standby mode, if the debugger handshake is enabled:
1. Power on DAP.
The debugger connection is established.
2. Configure the Debug subsystem for the relevant operations.
3. Write 1 to MDMAPWIREN[LWPWREN] to gate entry into Standby mode with the debugger handshake.
4. Disable POR_WDG for monitoring the entry to or exit from Standby mode by writing 1 to DCM's DCMRWP1[8] field.
This is required for low-power debug handshake because debugger configurations can be more time consuming than
the POR_WDG threshold levels and can raise a false POR_WDG event.
5. Write 1 to DCMRWF1[STANDBY_IO_CONFIG] in the Device Configuration Module (DCM). This causes padkeeping
to be disabled on standby entry itself without any software dependency. This is needed in case of low-power
debug since otherwise the padkeeping on TDO would result in no debugger communication. In other cases,
DCMRWF1[STANDBY_IO_CONFIG] should be configured as 0 before standby entry.
6. Initiate entry into Standby mode. See the "Power Management" chapter for the Standby mode entry sequence and
configurations.
7. Identify whether the low-power debug is enabled on the low-power entry by reading MC_RGM's
LPDEBUG[LP_DBG_EN] field.
• If low-power debug is enabled and TPIU is enabled too, trace flush starts, and the debugger acknowledges the
following:
— Low-power debug traces
— DAP-related configuration reception and context saving
After this, the chip enters Standby mode.
• If TPIU is disabled:
— The debugger acknowledges DAP-related configuration reception and context saving by writing 1 to
MDMAPWIRREL[PRVNTRSTRGM] .
After this, the chip enters Standby mode.
• If low-power debug is disabled, the chip enters Standby mode without waiting for debugger acknowledgment.
On any wakeup event, the chip starts the Standby mode exit sequence described in the "Power Management" chapter. After
the chip exits Standby mode, the debugger connection is restored. By this time, the debugger is already aware that it has
enabled the low-power debug handshake. In this case, the debugger must poll MDMAPSTTS[DESTRST] to check whether:
• The debug infrastructure is out of reset.
• The DAP connection can be established.
After DAP is powered on (the debugger connection is established), the debugger:
a. Reconfigures the debug and trace attributes using the fields in DBGENCTRL
b. Writes 1 to MDMAPWIRREL[WTRSTRGM] after the debugger trace context is restored
The chip exits Standby mode.

---

*Page 3268*

Debug Subsystem
If low-power debug is not configured, the chip exits Standby mode without waiting for the debugger to write
to MDMAPWIRREL[WTRSTRGM] .
8. Reconfigure the debug and trace configuration in SDA_AP register descriptions after the chip exits Standby mode.
NOTE
If the chip wakes up from Standby mode through pad reset, the debugger must perform a CR again.

#### 75.10 Debug resets

The debug subsystem follows this sequence on the source of reset:
1. POR resets the complete debug logic.
2. The destructive reset resets all types of debug logic except JTAGC.
Conversely, the debug subsystem can generate a system reset using these mechanisms:
• System destructive reset defined in Control (MDMAPCTL) that allows the debugger to provide the destructive reset to the
system. The debugger loses connection to the system with this reset.
• System functional reset defined in Control (MDMAPCTL) that allows the debugger to hold the system in functional reset.
To program various debug registers, the functional clocks must be enabled. All debug and trace components must be on
destructive reset.

#### 75.11 Debug across device LifeCycles

The debug access to the system is available in early lifecycles and subsequently based on NVM configuration settings.
After the debug is set to 'Trusted', the system access is allowed after a successful authorization step between the debugger and
the system.
The authorization provides provisions to allow debug for Cortex M0+ core and Cortex M7 cores.
Following table shows the debug access based on various configuration bits.
Table 593. Debug access based on LifeCycle and bit configurations
LifeCycl Configuration bits Debug access
e
HSE Debug
DBG_EN_FLD APP_DIS_FLD
HSE_DIS_CUS DBG_EN_OEM HSE_DIS_FLD APP_DIS_OEM HSE_DIS_OEM
Appl Core Debug
HSE_DIS_PRE_FA
HSE_DIS_CR_MCU_PROD
MCU_P 0 - - - - - - - - Trusted Open
ROD
1 - - - - - - - - Open Open
CUST_ - 0 - - - - - - - Trusted Open
DEL
- 1 - - - - - - - Disabled Open
Table continues on the next page...

---

*Page 3269*

Debug Subsystem
Table 593. Debug access based on LifeCycle and bit configurations (continued)
LifeCycl Configuration bits Debug access
e
HSE Debug
DBG_EN_FLD APP_DIS_FLD
HSE_DIS_CUS DBG_EN_OEM HSE_DIS_FLD APP_DIS_OEM HSE_DIS_OEM
Appl Core Debug
HSE_DIS_PRE_FA
HSE_DIS_CR_MCU_PROD
OEM_P - - 0 - - - - - - Closed Closed
ROD
- - 1 0 0 - - - - Trusted Trusted
- - 1 0 1 - - - - Disabled Trusted
- - 1 1 - - - - - Disabled Disabled
IN_FIEL - - - - - 0 - - - Closed Closed
D
- - - - - 1 0 0 - Trusted Trusted
- - - - - 1 0 1 - Disabled Trusted
- - - - - 1 1 - - Disabled Disabled
PRE_FA - - - - - - - - 0 Trusted Trusted
- - - - - - - - 1 Disabled Trusted
FA - - - - - - - - - Open Open
NOTE
1. Closed: Debug is not possible in this state (not even if keys are known - no authentication is possible)
2. Open: Debug is always possible
3. Trusted: Debug is possible after successful authentication (Challenge/Response handshake with
correct credentials)
4. Disabled: Debug has been explicitly disabled via burning of a fuse in that LC, otherwise behaves as Closed
5. HSE_DIS_CR_MCU_PROD bit disables the challenge-response based mechanism, making the debug
open when asserted.
6. During functional reset, the debug-enable will retain its last status, while DCM scans NVM for lifecycle and
DCF values. Debug status will be re-evaluated once dcm_done gets 1.
7. During temporary advancement of lifecycle, debug-en will immediately reflect the status based on updated
lifecycle/DCF bits.

#### 75.12 Pin interface

This table presents a summary of functional and power pins that are used for debugging purposes.

---

*Page 3270*

Debug Subsystem
Table 594. Pin interface
1
Pin type Pins Number of pins (balls) Nominal voltage
Functional JTAG JCOMP, TCK, TMS, TDI, TDO Five (only TDI and TDO can be 3.3 V, 5 V
multiplexed with GPIO or other
functions)
Functional trace pins TRACE_CLK One 3.3 V, 5 V
(parallel trace)
TRACE_D[15:0] 16 3.3 V, 5 V
Ground VSS See the IOMUX file attached to 0 V
this document for information on
the number of VSS pins in various
packages.
1. The terms pins and balls are used interchangeably. Some chip packages include pins and others include balls.
NOTE
Fast Trace pad TRACE_D[15:0] is available only in MCXE314.

#### 75.12.1 Debug port and pin descriptions

The pads to which the debug signals are mapped operate using the JTAG functionality out of reset but can later be reassigned
to their alternate functionalities. TDI and TDO can operate as alternate GPIO functions. See this table for pin assignments in
different modes.
Table 595. Debug port pins
Pin name JTAG debug port Internal pullup or
pulldown logic
Type Description
TMS I/O JTAG test mode selection (TMS) Pullup
TCK/SWCLK I JTAG test clock (TCK) Pulldown
TDI I JTAG test data input (TDI) Pullup
TDO/TRACESWO O JTAG test data output (TDO) Not connected

#### 75.12.2 Trace port pin descriptions

This chip generates trace via TPIU that transmits the trace data out of the chip over a parallel trace port. The trace port consists
of an ETM trace clock and 16 parallel trace data outputs. The Arm optional trace port control (TRACECTRL), debug request
(DBGRQ), and debug acknowledge (DBGACK) signals are not implemented .
Table 596. Trace output port pins
Pin name Description
TRACE_DATA00 ETM parallel trace data output 01
TRACE_DATA01 ETM parallel trace data output 01
TRACE_DATA02 ETM parallel trace data output 02
TRACE_DATA03 ETM parallel trace data output 03
Table continues on the next page...

---

*Page 3271*

Debug Subsystem
Table 596. Trace output port pins (continued)
Pin name Description
TRACE_DATA04 ETM parallel trace data output 04
TRACE_DATA05 ETM parallel trace data output 05
TRACE_DATA06 ETM parallel trace data output 06
TRACE_DATA07 ETM parallel trace data output 07
TRACE_DATA08 ETM parallel trace data output 08
TRACE_DATA09 ETM parallel trace data output 09
TRACE_DATA10 ETM parallel trace data output 10
TRACE_DATA11 ETM parallel trace data output 11
TRACE_DATA12 ETM parallel trace data output 12
TRACE_DATA13 ETM parallel trace data output 13
TRACE_DATA14 ETM parallel trace data output 14
TRACE_DATA15 ETM parallel trace data output 15
TRACE_CLK ETM parallel trace clock output
NOTE
ETM is supported in MCXE314 only
NOTE
By default, Rx pins float and are not pulled inside. An internal active pulldown logic exists only when you enable
Rx via software (IBE).

#### 75.13 Timestamp distribution network

The timestamp distribution network uses CoreSight timestamp components to generate a 48-bit timestamp value for the trace
sources, as shown in the next figure. The CoreSight timestamp generator generates a 64-bit counter value, but only the
least significant 48 bits are distributed to the trace sources. A 7-bit narrow timestamp is derived from the 48-bit timestamp
and distributed to the trace client, where a decoder regenerates the 48-bit timestamp. The generator must be programmed,
and you could find the related programming information in the CoreSight Components Technical Reference Manual (available
in References ).
This chip supports 48-bit timestamping. The generator operates at a frequency of 160 MHz and gives a 64-bit timestamp value.
The Cortex-M7_0 and Cortex-M7_1 cores operate at a frequency of 160 MHz.
Cortex-M7_0 (ts_valueb)
[63:48] - GND
[47:0] - Tied to generator
Generator at Cortex-M7_1 (ts_valueb) Only available
160 MHz
on the
[63:48] - GND
64-bit output [47:0] - Tied to generator MCXE31B
Figure 444. Timestamp distribution network

---

*Page 3272*

Debug Subsystem

#### 75.14 Peripheral debug freeze register descriptions

Implement the logic provided in this section for each peripheral instance supporting the debug operation.
NOTE
Debug freeze is enabled for all the peripherals so that as soon as the core halts, peripherals can be frozen to
support debugging from the first instruction.
For register details, refer to the following registers in the Device Configuration Module (DCM):
• Read Write GPR On Destructive Reset Register (DCMRWD6)
• Read Write GPR On Destructive Reset Register (DCMRWD7)
• Read Write GPR On Destructive Reset Register (DCMRWD8)
• Read Write GPR On Destructive Reset Register (DCMRWD9)

#### 75.15 MDM_AP register descriptions

The debugger has access to the status and control elements implemented as registers in MDM_AP, which is selected by APSEL
(6h) on the DAP bus. These registers provide additional control and status information for typical debug, cross-triggering, and
run-control scenarios. Also, the register fields provide a way for the debugger to get the updated status of the core without initiating
a bus transaction across the crossbar switch, thus remaining less intrusive during a debug session.
MDM_AP is accessible as DAP (see DAP TAP for details).

#### 75.15.1 MDM_AP memory map

MDM_AP base address: 4025_0600h
Offset Register Access Reset value
Width
(In bits)
0h Status (MDMAPSTTS) 32 R 5000_0000h
4h Control (MDMAPCTL) 32 RW 0640_0000h
30h WIR Enable (MDMAPWIREN) 32 RW 0000_0000h
34h WIR Status (MDMAPWIRSTTS) 32 R 0000_0000h
38h WIR Release (MDMAPWIRREL) 32 RW 0000_0000h
FCh Identity (ID) 32 R 001C_0030h

#### 75.15.2 Status (MDMAPSTTS)

Offset
Register Offset
MDMAPSTTS 0h

---

*Page 3273*

Debug Subsystem
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
Reserv Reserv Reserv CM70 Reserv Reserv Reserv CM70 Reserv Reserv Reserv CM70
R Reserved
ed ed ed DBG ... ed ed ed SLP ... ed ed ed DPS ...
W
Reset 0 1 0 1 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
Reserv Reserv Reserv CM70 FUNC DEST Reserv
R Reserved
ed ed ed HLT RST RST ed
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
28 Cortex-M7_0 Debug Restarted
CM70DBGRST Indicates if Cortex-M7_0 has returned to Normal mode from Debug mode.
RD
0b - In Debug mode
1b - In Normal mode
Reserved
27-24
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
20 Cortex-M7_0 Sleeping
CM70SLPNG Indicates if Cortex-M7_0 is in Sleep mode.
Table continues on the next page...

---

*Page 3274*

Debug Subsystem
Table continued from the previous page...
Field Function
0b - Not in Sleep mode
1b - In Sleep mode
Reserved
19
—
Reserved
18
—
Reserved
17
—
16 Cortex-M7_0 Deep Sleep
CM70DPSLP Indicates if Cortex-M7_0 is in Deep Sleep mode.
0b - Not in Deep Sleep mode
1b - In Deep Sleep mode
Reserved
15
—
Reserved
14
—
Reserved
13
—
12 Cortex-M7_0 Halted
CM70HLT Indicates if Cortex-M7_0 is halted because of entry into Debug mode.
0b - Core is not halted
1b - Core is halted
Reserved
11-3
—
2 Functional Reset
FUNCRST Indicates the system reset state.
0b - Not in functional reset
1b - In functional reset
1 Destructive Reset
DESTRST Indicates the system reset state.
Table continues on the next page...

---

*Page 3275*

Debug Subsystem
Table continued from the previous page...
Field Function
0b - Not in destructive reset
1b - In destructive reset
Reserved
0
—

#### 75.15.3 Control (MDMAPCTL)

Offset
Register Offset
MDMAPCTL 4h
Function
Allows the debugger:
• To give the destructive reset to the system. A system destructive reset enables this. The debugger also loses connection
to the system with this reset.
• To hold the system in functional reset. A system functional reset enables this.
NOTE
The trace functionality on trace pins is selected with this register and is retained across the functional reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv Reserv Reserv CM70 SWOO Reserv Reserv Reserv Reserv
Reserved Reserved
ed ed ed DBG ... VRD ed ed ed ed
W
Reset 0 0 0 0 0 1 1 0 0 1 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
POR_
R
Reserv Reserv Reserv Reserv CM70 SYSF SYSR
WDG .. Reserved Reserved Reserved
ed ed ed ed DBG ... UNC ... ESE ...
W
.
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
Table continues on the next page...

---

*Page 3276*

Debug Subsystem
Table continued from the previous page...
Field Function
—
Reserved
30
—
Reserved
29
—
28 Cortex-M7_0 Debug Restart
CM70DBGRSR Indicates if a request to Cortex-M7_0 to leave the debug halt state is asserted.
T
0b - Normal operation
1b - Request asserted
Reserved
27-23
—
22 SWO Override
SWOOVRD Indicates if the SWO trace response is overriden. When TPIU is not the selected trace sink target, you
must override the trace response.
0b - Not overridden, and SWO generates the trace response
1b - Is overridden
Reserved
21
—
Reserved
20
—
Reserved
19-18
—
Reserved
17
—
Reserved
16
—
15 Power Watchdog Status
POR_WDG_DI When a 0 is written to this field, the power watchdog is disabled in case of MDM FUNC reset or JTAG
S reset. When a 1 is written to this field, the power watchdog is enabled in case of MDM FUNC reset or
JTAG reset.
Table continues on the next page...

---

*Page 3277*

Debug Subsystem
Table continued from the previous page...
Field Function
0b - Power watchdog is disabled
1b - Power watchdog is enabled
Reserved
14
—
Reserved
13-12
—
Reserved
11
—
Reserved
10
—
Reserved
9
—
8 Cortex-M7_0 Debug Request
CM70DBGREQ Drives the EDBGREQ input for Cortex-M7_0 and indicates if the debug request is generated.
When the core goes into debug state, the field acknowledges that with a halted output signal (see
MDMAPSTTS[CM70HLT] ). If the core is in Stop mode, this field is used to wake up the core and
transition it to the debug halt state.
0b - Debug request is not generated
1b - Debug request is generated
Reserved
7-6
—
5 System Functional Reset
SYSFUNCRST Asserts or deasserts functional reset to the chip. The debugger maintains connection with the chip.
0b - Deasserted
1b - Asserted
4 System Destructive Reset
SYSRESETRE Asserts or deasserts destructive reset to the chip. The debugger also loses connection with this reset.
Q When this field is reset, the entire system comes out of reset.
0b - Deasserted
1b - Asserted
Reserved
3-0
Table continues on the next page...

---

*Page 3278*

Debug Subsystem
Table continued from the previous page...
Field Function
—

#### 75.15.4 WIR Enable (MDMAPWIREN)

Offset
Register Offset
MDMAPWIREN 30h
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserv LWPW
Reserved
ed REN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-2
—
Reserved
1
—
0 Low Power Debug Enable
LWPWREN Enables or disables low-power debug.
When the debugger writes 1 to this field, MC_RGM's LPDEBUG[LP_DBG_EN] field becomes 1 too.
0b - Disabled
1b - Enabled

---

*Page 3279*

Debug Subsystem

#### 75.15.5 WIR Status (MDMAPWIRSTTS)

Offset
Register Offset
MDMAPWIRSTTS 34h
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R MDM_DAP_WIR_STATUS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R MDM_DAP_WIR_STATUS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 MDM_AP WIR Status
MDM_DAP_WI 0b - Indicates WIR status
R_STATUS
1b - Does not indicate WIR status

#### 75.15.6 WIR Release (MDMAPWIRREL)

Offset
Register Offset
MDMAPWIRREL 38h

---

*Page 3280*

Debug Subsystem
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PRVN WTRS
Reserved
TRS ... TRGM
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-2
—
1 Prevent Reset
PRVNTRSTRG Indicates if MC_RGM is prevented from generating reset.
M
After TPIU flush, this field prevents MC_RGM from generating reset even if MC_RGM receives an
acknowledge response from TPIU. This is valid for low-power entry.
0b - Normal operation
1b - MC_RGM prevented
0 Wait In Reset B
WTRSTRGM Indicates if waiting of MC_RGM from generating reset is supported.
On exiting Standby mode, MC_RGM waits until the debugger writes to another field in the MDM_AP register
to allow it to exit reset.
0b - Normal operation
1b - Wait supported

#### 75.15.7 Identity (ID)

Offset
Register Offset
ID FCh

---

*Page 3281*

Debug Subsystem
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R ID
W
Reset 0 0 0 0 0 0 0 0 0 0 0 1 1 1 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R ID
W
Reset 0 0 0 0 0 0 0 0 0 0 1 1 0 0 0 0
Fields
Field Function
Identity
31-0
ID

#### 75.16 SDA_AP register descriptions

The debugger and system have access to secure authentication control and status, implemented as registers in SDA_AP
on the DAP bus. These registers provide authentication control, authentication status, key exchange information, and debug
enable controls.

#### 75.16.1 SDA_AP memory map

SDA_AP base address: 4025_4700h
Offset Register Access Reset value
Width
(In bits)
0h Authentication Status (AUTHSTTS) 32 R 6000_0004h
4h Authentication Control (AUTHCTL) 32 RW 0000_0000h
10h - 2Ch Key Challenge (KEYCHAL0 - KEYCHAL7) 32 R 0000_0000h
40h - 5Ch Key Response (KEYRESP0 - KEYRESP7) 32 RW 0000_0000h
70h User Identification 0 (UID0) 32 R 0000_0000h
74h User Identification 1 (UID1) 32 R 0000_0000h
80h Debug Enable Control (DBGENCTRL) 32 RW See section
90h Reset Control (SDAAPRSTCTRL) 32 RW 0600_0000h
A0h SDA_AP Generic Status (SDAAPGENSTATUS0) 32 R 0000_0000h
A4h Generic Control 0 (SDAAPGENCTRL0) 32 RW 0000_0000h
B0h SDA_AP Generic Status (SDAAPGENSTATUS1) 32 R 0000_0000h
Table continues on the next page...

---

*Page 3282*

Debug Subsystem
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
C0h SDA_AP Generic Status (SDAAPGENSTATUS2) 32 R 0000_0000h
D0h SDA_AP Generic Status (SDAAPGENSTATUS3) 32 R 0000_0000h
E0h SDA_AP Generic Status (SDAAPGENSTATUS4) 32 R 0000_0000h
FCh Identity (ID) 32 R 001C_0040h

#### 75.16.2 Authentication Status (AUTHSTTS)

Offset
Register Offset
AUTHSTTS 0h
Function
Indicates the status of the authentication process.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
Reserv APPD
R Reserved
ed BGEN
W
Reset 0 1 1 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
SWAP UIDST Reserv CHAL
R Reserved
PDBG AT ... ed RDY
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0 0
Fields
Field Function
Reserved
31
—
30 Application Debug Enabled or Disabled
APPDBGEN Indicates:
Table continues on the next page...

---

*Page 3283*

Debug Subsystem
Table continued from the previous page...
Field Function
• The status of application debug
• Whether CR is satisfied
• Whether access to other APs is allowed
0b - Application debug disabled
1b - Application debug enabled
Reserved
29-4
—
3 Software Application Debug
SWAPPDBG Indicates:
• The status of software application debug
• Whether access to debug controls is allowed
0b - Software application debug disabled
1b - Software application debug enabled
2 User Identification Status
UIDSTATUS Indicates:
• The status of UID
• Whether DCM has finished reading the flash memory user section
0b - UID is not ready and is invalid
1b - UID is ready and is valid
Reserved
1
—
0 Challenge Ready
CHALRDY Indicates:
• The status of challenge ready when the value of export control is 0
• The status of the DCM_DONE signal if the value of export control is 1
0b - Challenge is not ready
1b - Challenge is ready

---

*Page 3284*

Debug Subsystem

#### 75.16.3 Authentication Control (AUTHCTL)

Offset
Register Offset
AUTHCTL 4h
Function
Controls the authentication process.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R Reserved
HSEN HSEA
EWD ... UTH ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-2
—
1 New Data Control
HSENEWDATA Indicates that the debugger has consumed the data registers. It is alright for the core to provide new
CTL data.
0b - Does not indicate that the debugger has consumed the data registers
1b - Indicates that the debugger has consumed the data registers
0 Debug Enablement Authentication Request
HSEAUTHREQ Indicates that all key values are written and the chip can start the authentication request.
0b - Does not start the authentication request
1b - Starts the authentication request

#### 75.16.4 Key Challenge (KEYCHAL0 - KEYCHAL7)

Offset
For a = 0 to 7:

---

*Page 3285*

Debug Subsystem
Register Offset
KEYCHALa 10h + (a × 4h)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R KEYCHAL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R KEYCHAL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Debug Enablement Key Challenge
31-0
KEYCHAL

#### 75.16.5 Key Response (KEYRESP0 - KEYRESP7)

Offset
For a = 0 to 7:
Register Offset
KEYRESPa 40h + (a × 4h)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
KEYRESP
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
KEYRESP
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 3286*

Debug Subsystem
Fields
Field Function
Debug Enablement Key Response
31-0
KEYRESP

#### 75.16.6 User Identification 0 (UID0)

Offset
Register Offset
UID0 70h
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R UID0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R UID0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 User ID 0
UID0 Indicates the JTAG user ID bits of the lower word.

#### 75.16.7 User Identification 1 (UID1)

Offset
Register Offset
UID1 74h

---

*Page 3287*

Debug Subsystem
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R UID1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R UID1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 User ID 1
UID1 Indicates the JTAG user ID bits of the upper word.

#### 75.16.8 Debug Enable Control (DBGENCTRL)

Offset
Register Offset
DBGENCTRL 80h
Function
Includes a special protection that allows access from Cortex-M0+ only if bit 30 of Authentication Status (AUTHSTTS) is 1.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
CNIDE CDBG
Reserved Reserved
N EN
W
Reset
u u u u u u u u u u u u u u u u
1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
GSPNI GSPID GNIDE GDBG
Reserved Reserved
DEN EN N EN
W
Reset u u u u u u u u u u u u u u u u
1. The reset value is controlled by an export control enable input. If export control is enabled, the reset value of this register is
FFFF_FFF0h. Otherwise, it is 0000_0000h.

---

*Page 3288*

Debug Subsystem
Fields
Field Function
Reserved
31-30
—
29 Core Non-Invasive Debug Enable
CNIDEN Controls CNIDEN of debug blocks coupled with the Cortex-M7 core.
0b - Disabled
1b - Enabled
28 Core Debug Enable
CDBGEN Controls CDBGEN of debug blocks coupled with the Cortex-M7 core.
0b - Disabled
1b - Enabled
Reserved
27-8
—
7 Global Secure Privileged Non-Invasive Debug Enable
GSPNIDEN Controls GSPNIDEN of debug blocks coupled with Cortex-M7's subsystems, ETM, ITM, and CTI.
0b - Disabled
1b - Enabled
6 Global Secure Privileged Debug Enable
GSPIDEN Controls GSPIDEN of debug blocks coupled with Cortex-M7's subsystems, ETM, ITM, and CTI.
0b - Disabled
1b - Enabled
5 Global Non-Invasive Debug Enable
GNIDEN Controls GNIDEN of debug blocks coupled with Cortex-M7's subsystems, ETM, ITM, and CTI.
0b - Disabled
1b - Enabled
4 Global Debug Enable
GDBGEN Controls GDBGEN of debug blocks coupled with Cortex-M7's subsystems, ETM, ITM, and CTI.
0b - Disabled
1b - Enabled
Reserved
3-0
—

---

*Page 3289*

Debug Subsystem

#### 75.16.9 Reset Control (SDAAPRSTCTRL)

Offset
Register Offset
SDAAPRSTCTRL 90h
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
RSTR RSTR
Reserved Reserved
ELT ... ELT ...
W
Reset 0 0 0 0 0 1 1 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-27
—
26 Reset Release Cortex-M7_1
RSTRELTLCM7 Indicates if the control signal released the reset for Cortex-M7_1. The reset is released to debug the core
1 from first instruction.
The default value of this field is 1.
0b - Core is in reset
1b - Reset is released
25 Reset Release Cortex-M7_0
RSTRELTLCM7 Indicates if the control signal released the reset for Cortex-M7_0. The reset is released to debug the core
0 from the first instruction.
The default value of this field is 1.
0b - Core is in reset
1b - Reset is released
Reserved
24-0
—

---

*Page 3290*

Debug Subsystem

#### 75.16.10 SDA_AP Generic Status (SDAAPGENSTATUS0 - SDAAPGENSTATUS4)

Offset
Register Offset
SDAAPGENSTATUS0 A0h
SDAAPGENSTATUS1 B0h
SDAAPGENSTATUS2 C0h
SDAAPGENSTATUS3 D0h
SDAAPGENSTATUS4 E0h
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R SDAAPGENSTATUS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R SDAAPGENSTATUS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 DAP Generic Status
SDAAPGENST Is a generic status field for future use.
ATUS
0b - Does not show generic status
1b - Shows generic status

#### 75.16.11 Generic Control 0 (SDAAPGENCTRL0)

Offset
Register Offset
SDAAPGENCTRL0 A4h

---

*Page 3291*

Debug Subsystem
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
JTAG_
Reserved
CR ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-1
—
0 JTAG CR Enable
JTAG_CR_EN Performs CR or password comparison based on SWJ-DP mode or JTAG mode. If you write 1 to this
field, this function is performed using JTAG irrespective of SWJ-DP mode of the debugger.
0b - Function performed on the basis of SWJ-DP mode
1b - Function performed on the basis of JTAG mode

#### 75.16.12 Identity (ID)

Offset
Register Offset
ID FCh
Function
NOTE
This register does not generate the bus error on performing the write operation.

---

*Page 3292*

Debug Subsystem
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R ID
W
Reset 0 0 0 0 0 0 0 0 0 0 0 1 1 1 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R ID
W
Reset 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0 0
Fields
Field Function
Identity
31-0
ID

#### 75.17 Glossary

AHB Advanced high-performance bus
AHB_AP Advanced high-performance bus access port
APB Advanced peripheral bus
APB_AP Advanced peripheral bus access port
ATB Advanced trace bus
ATBR ATB replicator
CSTF CoreSight trace funnel
DAP Debug access port
DC Design center
ETF Embedded CoreSight funnels
JTAG-DP JTAG debug port
MDM_AP Miscellaneous debug module access port
MIC Manufacturer identity code
PIN Part identification number
PRN Part revision number
SDA_AP Serial data access port
SiP System-in-package
SWJ-DP Serial wire/JTAG debug port
TAP Test and debug access port
TPIU Trace port interface unit

---

*Page 3293*

Debug Subsystem

#### 75.18 References

• Arm CoreSight SoC-400 Technical Reference Manual
https://static.docs.arm.com/100536/0302/coresight_soc400_technical_reference_manual_100536_0302_01_en.pdf
• Arm CoreSight Architecture Specification
http://infocenter.arm.com/help/topic/com.arm.doc.ihi0029d/IHI0029D_coresight_architecture_spec_v2_0.pdf
• CoreSight Components Technical Reference Manual
http://infocenter.arm.com/help/topic/com.arm.doc.ddi0314h/DDI0314H_coresight_components_trm.pdf
• Arm Debug Interface Architecture Specification
https://static.docs.arm.com/ihi0031/c/IHI0031C_debug_interface_as.pdf

---

*Page 3294*

