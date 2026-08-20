*Page 1*

#### Rev. 4 — 6 April 2026 Product data sheet

® ®
SIL2 5V robust Arm Cortex -M7 core 32-bit MCU, up to 4 MB Flash, 512
®
KB SRAM, EdgeLock Secure Enclave, Advanced Profile
48LQFP 100HDQFP
Features
7 x 7 x 1.4 mm, 0.5 10 x 10 x 1.65 mm,
® ® ®
• Arm Cortex -M7 160 MHz with 846 CoreMark (5.28 CoreMark/MHz)
mm 0.65 mm
• IEC 61508 SIL2 certified.
100LQFP 172HDQFP
®
• Platform Security with EdgeLock Secure Enclave, Advanced Profile.
14 x 14 x 1.7 mm, 16 x 16 x 1.65 mm,
0.5 mm 0.65 mm
• -40 °C to +135 °C junction temperature range.
Operating characteristics
• Voltage range: 2.97 V to 5.5 V
• Ambient temperature range: -40 °C to 125 °C for all power modes
™
Arm Cortex-M7 core, 32-bit CPU
• M7 supports up to 160 MHz frequency
®
• Arm Core based on the Armv7 and Thumb -2 ISA
• Integrated Digital Signal Processor (DSP)
• Configurable Nested Vectored Interrupt Controller (NVIC)
• Single Precision Floating Point Unit (FPU)
Clock interfaces
• 8 - 40 MHz Fast External Oscillator (FXOSC)
• 48 MHz Fast Internal RC oscillator (FIRC)
• 32 kHz Low Power Oscillator (SIRC)
• 32 kHz Slow External Oscillator (SXOSC)
• System Phased Lock Loop (SPLL)
I/O and package
• LQFP48, LQFP100, HDQFP100, HDQFP172
• Up to 32-channel DMA with up to 128 request sources using DMAMUX
Memory and memory interfaces
• Up to 4 MB program flash memory with ECC
• Up to 128 KB of flexible program or data flash memory
• Up to 512 KB SRAM with ECC, includes 96 KB of TCM RAM ensuring maximum CPU performance of fast control loops
with minimal latency
• Data and instruction cache for each core to minimize performance impact of memory access latencies
• QuadSPI support

---

*Page 2*

Mixed-signal analog
• Up to three 12-bit Analog-to-Digital Converters (ADC) with up to 24 channel analog inputs per module
• One Temperature Sensor (TempSense)
• Up to three Analog Comparators (CMP), with each comparator having an internal 8-bit DAC
Human-Machine Interface (HMI)
• Up to 145 GPIO pins
• Non-Maskable Interrupt (NMI)
• Up to 59 pins with wakeup capability
• Up to 32 pins with interrupt support
Power management
• Low-power Arm Cortex-M7 core with excellent energy efficiency, balanced with performance
• Power Management Controller (PMC) with simplified mode management (RUN and STANDBY)
• Supports peripheral specific clock gating. Only specific peripherals remain working in low power modes.
Communications interfaces
• Up to 16 serial communication interface (LPUART) modules, with LIN, UART and DMA support
• Up to six Low Power Serial Peripheral Interface (LPSPI) modules with DMA support
• Up to two Low Power Inter-Integrated Circuit (LPI2C) modules with DMA support
• Up to Six FlexCAN modules (with optional CAN-FD support)
• FlexIO module for flexible and high performance serial interfaces
• Up to one ethernet modules
• Up to two Synchronous Audio Interface (SAI) modules
Reliability, safety and security
• Up to two Internal Software Watchdog Timers (SWT)
• Error-Correcting Code (ECC) on all memories
• Error Detection Code (EDC) on data path
• Cyclic Redundancy Check (CRC) module
• 120-bit Unique Identification (ID) number
• Extended Cross domain Domain Controller (XRDC), providing protection for master core access rights
• Virtualization Wrapper (VIRT_WRAPPER), providing I/O protection
Debug functionality
• Serial Wire JTAG debug Port (SWJ-DP), with 2 pin Serial Wire Debug (SWD) for external debugger
• Debug Watchpoint and Trace (DWT), with four configurable comparators as hardware watchpoints
• Serial Wire Output (SWO)-synchronous trace data support
• Instrumentation Trace Macrocell (ITM) with software and hardware trace, plus time stamping
• CoreSight AHB Trace Macrocell (HTM)
• Flash Patch and Breakpoints (FPB) with ability to patch code and data from code space to system space
• Serial Wire Viewer (SWV): A trace capability providing displays of reads, writes, exceptions, PC Samples and print
• Full data trace for up to 16 output wide

---

*Page 3*

• Embedded Cross Trigger (ECT) is used for multicore run-control and trace cross triggering, using CoreSight Cross Trigger
Interface (CTI)
Timing and control
• Up to three enhanced modular I/O system (eMIOS), offering up to 72 timer channels (IC/OC/PWM)
• Up to two System Timer Modules (STM)
• Up to two Logic Control Units (LCU)
• Full cross triggering support for ADC / timer (BCTU)
• One Trigger MUX Control (TRGMUX) module
• Up to three Periodic Interrupt Timer (PIT) modules
• 32-bit Real Time Counter (RTC) with autonomous periodic interrupt (API) function

---

*Page 4*

### 1 Overview

® ®
The MCXE31 product series further extends the highly-scalable portfolio of Arm Cortex - M4F MCX E24 series chips in the
commercial and industrial industry with the Arm Cortex-M7 core at higher frequency, more memory, SIL 2 rating and advanced
security module. With a focus on commercial and industrial environment robustness, the MCXE31 series devices are well suited to
a wide range of applications in electrical harsh environments, and are optimized for cost-sensitive applications offering new, space
saving package options. The MCXE31 series offers a broad range of memory, peripherals and performance options. Devices in
this series share common peripherals and pin-out, allowing developers to migrate easily within a chip series or among other chip
series to take advantage of more memory or feature integration.

### 2 Block diagram

The following figure shows the MCX E31 product series block diagram:
Analog Core Connectivity
® ®
3x ADC 24 ch Arm Cortex -M7
3x LPCMP 2x LPI2C 6x LPSPI
12-bit at 1 Msps FPU, DSP, I/D-cache up to 160 MHz
BCTU body 1x 32 ch
Temp 16x LPUART
control trigger FlexIO
Memories
2x SAI
LCU logic control TRGMUX
Up to 4 MB Pflash/128 kB (TDM, I2S)
Dflash w/ ECC
Quad SPI 480 Mbps
Up to 512 kB SRAM w/ ECC
Safety
6x FlexCAN ETH TSN
FCCU fault STCU self
w/ CAN-FD 10/100 Mbps
collection test control
ERM/EIM REG_PROT
System
EdgeLock® Secure Enclave
Simplified power modes:
(Advanced Profile)
standbyand run
Timers
(+last mile regulator on E31B)
Secure boot
3x 16 bit eMIOS 3x periodic
Clock generation
timers input timer
low-power internal clock
Crypto Device
(32 kHz, 48 MHz, PLL)
Security Acc. key
fast/slow external OSC (32 kHz)
core Internal and
2x System
RAM TRNG external WDT
timers
CMU clock monitoring unit
Other security modules
Resource Access Controller Low-power
32 bit RTC
32 ch eDMA timer
Tamper Detection Controller
Figure 1. MCX E31 product series block diagram MCU

### 3 Feature comparison

The following table compares some of the prominent features related to memory and package options of these chips from the MCX
E31 family/product series:
• MCX E315
• MCX E316
• MCX E317

---

*Page 5*

• MCX E31B
Table 1. MCX E31 chip's feature comparison
Feature Chip
MCX E315 MCX E316 MCX E317 MCX E31B
Core 1 x M7
Safety SIL2
Program flash memory 512 KB 1 MB 2 MB 4 MB
Data flash memory (KB) 64 128
Total RAM (KB) 112KB (incl. 96KB 128KB (incl. 96KB 192KB (incl. 96KB 512KB (including 96KB
TCM) TCM) TCM) TCM)
Standby RAM 16 KB 32 KB
Security EdgeLock Enclave (HSE_B)
Frequency (MHz) 120 160
DMA channels 12 32
FlexCAN instances 3 6 6
EMAC instances — 1
SAI instances — 2
LPUART instances 4 8 16
LPSPI instances 4 6
2
I C instances 2
FlexIO (incl. SENT support) 16 32
channels
[1]
QuadSPI instances — 1
ADC instances 2 3
LPCMP instances 1 2 3
PIT instances 2 3
SWT instances 1 1
STM instances 1 2
LCU instances 2
BCTU instances 1
TRGMUX instances 1
eMIOS instances 2 3
RTC instances 1
172-HDQFP package No Yes
100-HDQFP package Yes No
Table continues on the next page...

---

*Page 6*

Table 1. MCX E31 chip's feature comparison ...continued
Feature Chip
MCX E315 MCX E316 MCX E317 MCX E31B
48-pin LQFP package Yes No
100-pin LQFP package No Yes
[1] 4-bit data width, SDR mode only

### 4 Ordering information (flash vs package)

The following table lists the available part numbers and their associated flash size / package.
Table 2. Ordering information
Core
Orderable Part Number Core Flash RAM Security Ethernet Package
speed
MCXE315MLF 48 (7x7) LQFP
112 KB
512 KB
SRAM
MCXE315MPA 100 (10x10) HDQFP
MCXE316MLF 48 (7x7) LQFP
128 KB
120 MHz 1 MB No
SRAM
MCXE316MPA 100 (10x10) HDQFP
MCXE317MPA EdgeLock
192 KB
Cortex M7 2 MB Enclave
SRAM
MCXE317MPB (HSE_B) 172 (16x16) HDQFP
MCXE31BMPB 160 MHz 4 MB 512 KB Yes
SRAM
120 MHz 192 KB
MCXE317MLL 2 MB No 100 (14x14) LQFP
SRAM
160 MHz 512 KB
MCXE31BMLL 4 MB Yes 100 (14x14) LQFP
SRAM

#### 4.1 Determining valid orderable parts

To determine the orderable part numbers for this device, please contact NXP sales representative.

### 5 General

#### 5.1 Absolute maximum ratings

Caution: When the MCU is in an unpowered state, current injected through the chip pins may bias internal chip structures
(for example, ESD diodes) and incorrectly power up these internal structures through inadvertent paths. The presence of
such residual voltage may influence different chip-internal blocks in an unpredictable manner and may ultimately result in
unpredictable chip behavior (for example, POR flag not set). Once in the illegal state, powering up the chip further and
then applying reset will clear the illegal state. Injection current specified for the chip under the aspect of absolute maximum ratings
represent the capability of the internal circuitry to withstand such condition without causing physical damage. Functional operation
of the chip under conditions - specified as absolute maximum ratings - is not implied.

---

*Page 7*

Note: Functional operating conditions appear in the DC electrical characteristics. Absolute maximum ratings are stress ratings
only, and functional operation at the maximum values is not guaranteed. See footnotes in the following table for specific conditions.
Stress beyond the listed maximum values may affect device reliability or cause permanent damage to the device. All the
limits defined in the datasheet specification must be honored together and any violation to any one or more will not guarantee
desired operation. Unless otherwise specified, all maximum and minimum values in the datasheet are across process, voltage,
and temperature.
Table 3. Absolute maximum ratings
Symbol Description Min Typ Max Unit
VDD_HV_A Main I/O and analog supply -0.3 — 6.0 V
[1] [2]
voltage
VDD_HV_B Secondary I/O supply -0.3 — 6.0 V
[1] [2]
voltage
V25 Flash memory supply (2.5 V), -0.3 — 2.9 V
[2]
internally regulated
V11 Core logic voltage supply (1.1 -0.3 — 1.26 V
[2]
V), internally regulated
[1]
VREFH ADC high reference voltage -0.3 — 6.0 V
[2]
[2]
VREFL ADC low reference voltage -0.3 — 0.3 V
VGPIO_trans Transient overshoot voltage - — 6.0 V
[1] [2] [3]
allowed on I/O pin
I_INJPAD_DC_ABS Continuous DC input current -3 — 3 mA
(positive/negative) that can
[4]
be injected into an I/O pin
I_INJSUM_DC_ABS Sum of absolute value — — 30 mA
of injected currents on all
the I/O pins (continuous DC
[4] [5]
limit)
TSTG Storage ambient -55 — 150 °C
[6]
temperature
[1] 6.0 V maximum for 10 hours over lifetime; 7.0 V maximum for 60 seconds over lifetime.
[2] All voltages are referred to VSS unless otherwise specified.
[3] When a low impedance voltage source, without current limitation, is connected to one or more I/O pins, the VGPIO_trans absolute max rating must be
honored. During current injection, the voltage at the I/O pin or pins could go beyond this limit if (and ONLY IF) the injected current is being limited
(I_INJPAD_DC_ABS is respected).
[4] When the input pad voltage levels are close to VDD_HV_A (respectively to VDD_HV_B) or VSS, plus /minus the forward voltage of ESD diodes, practically, no
current is being injected. When these limits are exceeded, the maximum input current spec must be honored. See MCXE31 Hardware Design Guidelines for
more details and recommendations for protecting the devices against injection current.
[5] If a positive injection current is present in one or more I/O pins, and the device is in Low-Speed RUN or STANDBY mode, the VDD_HV_A (or respectively,
VDD_HV_B) may lift and cause unexpected behavior. Therefore, it is recommended to add external protection hardware, to safely cover this scenario.
[6] TSTG specifies the storage temperature range. It is not the operating temperature range. Please refer to the Thermal operating characteristics table.

#### 5.2 Voltage and current operating requirements

Note: Device functionality is guaranteed down to the LVR assert level, however electrical performance of 12-bit ADC, CMP with
8-bit DAC, IO electrical characteristics, and communication modules electrical characteristics will be degraded when voltage
drops below 2.97 V.

---

*Page 8*

Table 4. Voltage and current operating requirements
Symbol Description Min Typ Max Unit Condition
VDD_HV_A Main I/O and analog 2.97 3.3 or 5.0 5.5 V —
[1]
supply voltage
VDD_HV_B Secondary I/O supply
[1]
voltage
VREFH ADC high reference
[1] [2]
voltage
VREFL ADC low reference -0.1 0 0.1 V —
[1]
voltage
V25 Flash memory — 2.5 — V —
and clock
supply (2.5 V),
[1]
internally regulated
V11 Core logic supply — 1.14 — V —
(1.1 V), internally
[1]
regulated
VGPIO Input voltage range -0.3 — VDD_HV_ V —
at any I/O or analog A/B + 0.3
[1] [3]
pin
VODPU Open-drain pull-up — — VDD_HV_ V —
[1] [4]
voltage A/B
IINJPAD_DC_OP Continuous DC input -3 — 3 mA VDD_HV_A >= 3.6V
current (positive/
negative) that can be
injected into an I/O
[5]
pin
IINJPAD_DC_OP Continuous DC input -2 — 3 mA VDD_HV_A >= 2.97V
current (positive/
negative) that can be
injected into an I/O
[5]
pin
IINJSUM_DC_OP Sum of absolute value -30 — 30 mA VDD_HV_A >= 3.6V
of injected currents
on all the I/O
pins (continuous
[5] [6]
DC limit)
IINJSUM_DC_OP Sum of absolute value -20 — 30 mA VDD_HV_A >= 2.97V
of injected currents
on all the I/O
pins (continuous
[5] [6]
DC limit)
Vramp_slow Supply ramp rate 0.5 — — V/min —
[1] [7]
(slow)
Table continues on the next page...

---

*Page 9*

Table 4. Voltage and current operating requirements ...continued
Symbol Description Min Typ Max Unit Condition
Vramp_fast Supply ramp rate — — 100 V/ms —
[1] [7]
(fast)
[1] All voltages are referred to VSS unless otherwise specified.
[2] VREFH should always be equal to or less than VDD_HV_A +0.1. Any positive differential voltage between VREFH and VDD_HV_A i.e., VDD_HV_A < VREFH
<= VDD_HV_A + 0.1V) is for RF-AC only. Appropriate decoupling capacitors should be used to filter noise on the supplies. See application note AN5032 for
reference supply design for SAR ADC
[3] Keeping the input voltage between this range practically ensures that no (noticeable) current is being injected. When exceeding these limits, the current being
injected must be lower than IINJPAD_DC_OP, all the time.
[4] Open-drain outputs must be pulled respectively to their supply rail (VDD_HV_A or VDD_HV_B).
[5] When the input pad voltage levels are close to VDD_HV_A (respectively to VDD_HV_B) or VSS, plus /minus the forward voltage of ESD diodes, practically,
no current is being injected. When these limits are exceeded, the maximum input current spec must be honored. Refer to the MCXE31 Hardware Design
Guidelines AN for more details and recommendations for protecting the devices against injection current.
[6] If a positive injection current is present in one or more I/O pins, and the device is in Low-Speed RUN or STANDBY mode, the VDD_HV_A (or respectively,
VDD_HV_B) may lift and cause unexpected behavior. Therefore, it is recommended to add external protection hardware, to safely cover this scenario.
[7] The MCU supply ramp rate parameter must be applicable to the MCU input/external supplies. The ramp rate assumes that the MCXE31 HW design
guidelines available on www.nxp.com are followed.
If total power dissipation and maximum junction temperature allows. Please refer to Thermal operating characteristics table for
the maximum junction temperature, and Thermal characteristics table for the thermal characteristics, to determine the maximum
power dissipation allowed for a given package.

#### 5.3 Thermal operating characteristics

Table 5. Thermal operating characteristics
Symbol Description Min Typ Max Unit Condition
TA Ambient -40 — 125 °C M- Grade
[1]
temperature
TJ Junction — — 135 °C —
[2] [3] [4]
temperature
[1] The device may operate at maximum TA rating as long as TJ maximum of 135 °C is not exceeded. The simplest method to determine TJ is: TJ = TA + RθJA
× chip power dissipation.
[2] The device operating specification is not guaranteed beyond 135 °C TJ.
[3] The maximum operating requirement applies to all chapters unless otherwise specifically stated.
[4] Operating at maximum conditions for extended periods may affect device reliability. Refer to Product Lifetime Usage application note (AN14180).

#### 5.4 ESD and Latch-up Protection Characteristics

Table 6. ESD and Latch-up Protection Characteristics
Symbol Description Min Typ Max Unit
Vhbm Electrostatic discharge -2000 — 2000 V
voltage, human body model
[1] [2]
(HBM)
Vcdm Electrostatic -500 — 500 V
discharge voltage, charged-
device model (CDM), all pins
[1] [3]
except corner
Vcdm Electrostatic -750 — 750 V
discharge voltage, charged-
device model (CDM), corner
[1] [3]
pins
Table continues on the next page...

---

*Page 10*

Table 6. ESD and Latch-up Protection Characteristics ...continued
Symbol Description Min Typ Max Unit
Ilat Latch-up current at -100 — 100 mA
ambient temperature
[4]
of 125°C
[1] Device failure is defined as: "If after exposure to ESD pulses, the device does not meet specification requirements."
[2] This parameter is tested in conformity with JEDEC-JS-001.
[3] This parameter is tested in conformity with JEDEC-JS-002.
[4] This parameter is tested in conformity with JEDEC-JESD78.

#### 5.5 Thermal Attributes

#### 5.5.1 Description

The tables in the following sections describe the thermal characteristics of the device.
Note: Junction temperature is a function of die size, on-chip power dissipation, package thermal resistance, mounting side
(board) temperature, ambient temperature, air flow, power dissipation or other components on the board, and board thermal
resistance.

#### 5.5.2 Thermal characteristics

Thermal Design and Characteristics
• Junction temperature of the device does not solely depend on package thermal resistance but is also a function of chip
power dissipation, PCB attributes, environmental conditions (ambient temperature & air flow) and cumulative effects of
other heat generating ICs on the PCB.
• The appropriate thermal design must be carried out on package so that it can safely dissipate the necessary amount of
power needed for it to function properly without exceeding the maximum junction temperature. This may involve adding a
cooling solution on the package, creating thermal enhancements on the PCB and improving environmental conditions.
• The customer is encouraged to use the package model to perform design and risk assessment through simulations.
Package models in FloTHERM or Icepak formats can be obtained under NDA from the sales team.
Thermal Ratings
• The table below is the package thermal ratings for LQFP and HDQFP package variants. These numbers are derived
through simulations based on standardized tests as described in the footnotes.
• Thermal resistance data in this report is solely for a thermal performance comparison of one package to another in a
standardized specified environment. It is not meant to predict the performance of a package in an application-specific
environment :
Table 7. Thermal characteristics
Rating Conditions Symbol Package Device Unit
MCXE31 MCXE31
MCXE31
5/ 7
B
MCXE31
6
Thermal resistance, Junction to Four-layer R ϴ JA 48-LQFP 45 NA NA °C/W
[1] [2]
Ambient (Natural Convection) board (2s2p)
100-HDQFP 35.3 38 NA °C/W
172-HDQFP NA 30.5 28.9 °C/W
Table continues on the next page...

---

*Page 11*

Table 7. Thermal characteristics ...continued
Rating Conditions Symbol Package Device Unit
MCXE31 MCXE31
MCXE31
5/ 7
B
MCXE31
6
100-LQFP NA 38.0 36.1 °C/W
Thermal characterization Natural Ψ JT 48-LQFP 2 NA NA °C/W
parameter, Junction-to-Top of Convection
100-HDQFP 0.66 0.8 NA °C/W
[1]
package
172-HDQFP NA 0.5 0.4 °C/W
100-LQFP NA 1.1 1.0 °C/W
[1] Determined in accordance to JEDEC JESD51-2A natural convection environment. Thermal resistance data in this report is solely for a thermal performance
comparison of one package to another in a standardized specified environment. It is not meant to predict the performance of a package in an application-
specific environment
[2] Thermal test board meets JEDEC specification for this package (JESD51-9).

### 6 Power management

#### 6.1 Power mode transition operating behaviors

#### 6.1.1 Power mode transition operating behavior

The values in the table below are provided for reference only.
Table 8. Power mode transition operating behaviour
Symbol Description Min Typ Max Unit Condition Spec
Number
tMODE_ RUN --> STANDBY — 955 — ns — —
STDBYENTRY transition time
tMODE_ STANDBY --> RUN — 53 — us —
FIRC ON @48MHz
STDBYEXIT_FAST transition time, fast
in Standby
standby exit
tMODE_ STANDBY --> RUN — 80 — us — —
STDBYEXIT transition time,
normal standby exit

#### 6.1.2 Boot time, HSE firmware not installed

Table 9. Boot time, HSE firmware not installed
Symbol Description Min Typ Max Unit Condition Spec
Number
tBOOT_noHSE After a POR event, — 2 — ms Device running from —
amount of time to FIRC (clocking option
execution of the D). CORE_CLK = 48
first instruction of
Table continues on the next page...

---

*Page 12*

Table 9. Boot time, HSE firmware not installed
Symbol Description Min Typ Max Unit Condition Spec
Number
the application core, MHz; HSE_CLK = 48
when HSE firmware MHz.
is not installed. (HSE
FW feature flag is
disabled)

#### 6.1.3 Boot time, HSE firmware installed

The following table provides the boot time of the MCXE31 SBAF and Firmware initialization. To obtain the total boot time, the
corresponding user code verification time must be added.
Table 10. Boot time, HSE firmware installed
Symbol Description Min Typ Max Unit Condition Spec
Number
tBOOT_HSE_ After a POR event, — — 3 ms Device running from —
NONSECURE amount of time to FIRC (clocking option
execution of the D). CORE_CLK = 48
first instruction of MHz; HSE_CLK = 48
the application core, MHz.
when HSE firmware
is installed. (BOOT
SEQ = 0)
tBOOT_HSE After a POR event, — 12.36 — ms Device running from —
amount of time to FIRC (clocking option
execution of the D). CORE_CLK = 48
first instruction of MHz; HSE_CLK = 48
the application core, MHz.
when HSE firmware
is installed.
tBOOT_HSE After a POR event, — 9.51 — ms Device running from —
amount of time to PLL (clocking option
execution of the B). CORE_CLK = 120
first instruction of MHz; HSE_CLK = 120
the application core, MHz.
when HSE firmware
is installed.
tBOOT_HSE After a POR event, — 10.91 — ms Device running from —
amount of time to PLL (clocking option
execution of the A). CORE_CLK = 160
first instruction of MHz; HSE_CLK = 80
the application core, MHz.
when HSE firmware
is installed.

---

*Page 13*

#### 6.1.4 HSE firmware memory verification time examples

Table 11. HSE firmware memory verification time examples
Symbol Description Min Typ Max Unit Condition Spec
Number
tCMAC_64KB Memory verification — 11.3 — ms Device running from —
of 64 KB of FIRC (clocking option
application firmware, D). CORE_CLK = 48
using AES-128 MHz; HSE_CLK = 48
CMAC cipher. MHz.
tCMAC_1024KB Memory verification — 176 — ms Device running from —
of 1024 KB of FIRC (clocking option
application firmware, D). CORE_CLK = 48
using AES-128 MHz; HSE_CLK = 48
CMAC cipher. MHz.
tGMAC_64KB Memory verification — 3.2 — ms Device running from —
of 64 KB of FIRC (clocking option
application firmware, D). CORE_CLK = 48
using AES-128 MHz; HSE_CLK = 48
GMAC cipher. MHz.
tGMAC_1024KB Memory verification — 46.8 — ms Device running from —
of 1024 KB of FIRC (clocking option
application firmware, D). CORE_CLK = 48
using AES-128 MHz; HSE_CLK = 48
GMAC cipher. MHz.
tHMAC_64KB Memory verification — 1.74 — ms Device running from —
of 64 KB of FIRC (clocking option
application firmware, D). CORE_CLK = 48
using AES-128 MHz; HSE_CLK = 48
HMAC cipher. MHz.
tHMAC_1024KB Memory verification — 22.87 — ms Device running from —
of 1024 KB of FIRC (clocking option
application firmware, D). CORE_CLK = 48
using AES-128 MHz; HSE_CLK = 48
HMAC cipher. MHz.
tRSA_64KB Memory verification — 31.03 — ms Device running from —
of 64 KB of FIRC (clocking option
application firmware, D). CORE_CLK = 48
using RSA 2048 MHz; HSE_CLK = 48
cipher. MHz.
tRSA_1024KB Memory verification — 52.15 — ms Device running from —
of 1024 KB of FIRC (clocking option
application firmware, D). CORE_CLK = 48
using RSA 2048 MHz; HSE_CLK = 48
cipher. MHz.
tECDSA_64KB Memory verification — 126.46 — ms Device running from —
of 64 KB of FIRC (clocking option
application firmware, D). CORE_CLK = 48
Table continues on the next page...

---

*Page 14*

Table 11. HSE firmware memory verification time examples ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
using ECDSA 521 MHz; HSE_CLK = 48
bits cipher. MHz.
tECDSA_1024KB Memory verification — 147.53 — ms Device running from —
of 1024 KB of FIRC (clocking option
application firmware, D). CORE_CLK = 48
using ECDSA 521 MHz; HSE_CLK = 48
bits cipher. MHz.
tSHA2_256_64KB Memory verification — 1.62 — ms Device running from —
of 64 KB of FIRC (clocking option
application firmware, D). CORE_CLK = 48
using SHA2 256 bits MHz; HSE_CLK = 48
bits cipher. MHz.
tSHA2_256_ Memory verification — 22.73 — ms Device running from —
1024KB of 1024 KB of FIRC (clocking option
application firmware, D). CORE_CLK = 48
using SHA2 256 bits MHz; HSE_CLK = 48
bits cipher. MHz.
tCMAC_64KB Memory verification — 6.67 — ms Device running from —
of 64 KB of PLL (clocking option
application firmware, A). CORE_CLK = 160
using AES-128 MHz; HSE_CLK = 80
CMAC cipher. MHz.
tCMAC_1024KB Memory verification — 105.24 — ms Device running from —
of 1024 KB of PLL (clocking option
application firmware, A). CORE_CLK = 160
using AES-128 MHz; HSE_CLK = 80
CMAC cipher. MHz.
tGMAC_64KB Memory verification — 1.85 — ms Device running from —
of 64 KB of PLL (clocking option
application firmware, A). CORE_CLK = 160
using AES-128 MHz; HSE_CLK = 80
GMAC cipher. MHz.
tGMAC_1024KB Memory verification — 28.03 — ms Device running from —
of 1024 KB of PLL (clocking option
application firmware, A). CORE_CLK = 160
using AES-128 MHz; HSE_CLK = 80
GMAC cipher. MHz.
tHMAC_64KB Memory verification — 0.98 — ms Device running from —
of 64 KB of PLL (clocking option
application firmware, A). CORE_CLK = 160
using AES-128 MHz; HSE_CLK = 80
HMAC cipher. MHz.
tHMAC_1024KB Memory verification — 13.68 — ms Device running from —
of 1024 KB of PLL (clocking option
Table continues on the next page...

---

*Page 15*

Table 11. HSE firmware memory verification time examples ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
application firmware, A). CORE_CLK = 160
using AES-128 MHz; HSE_CLK = 80
HMAC cipher. MHz.
tRSA_64KB Memory verification — 17.39 — ms Device running from —
of 64 KB of PLL (clocking option
application firmware, A). CORE_CLK = 160
using RSA 2048 MHz; HSE_CLK = 80
cipher. MHz.
tRSA_1024KB Memory verification — 23.32 — ms Device running from —
of 1024 KB of PLL (clocking option
application firmware, A). CORE_CLK = 160
using RSA 2048 MHz; HSE_CLK = 80
cipher. MHz.
tECDSA_64KB Memory verification — 72.2 — ms Device running from —
of 64 KB of PLL (clocking option
application firmware, A). CORE_CLK = 160
using ECDSA 521 MHz; HSE_CLK = 80
bits cipher. MHz.
tECDSA_1024KB Memory verification — 84.91 — ms Device running from —
of 1024 KB of PLL (clocking option
application firmware, A). CORE_CLK = 160
using ECDSA 521 MHz; HSE_CLK = 80
bits cipher. MHz.
tSHA2_256_64KB Memory verification — 0.9 — ms Device running from —
of 64 KB of PLL (clocking option
application firmware, A). CORE_CLK = 160
using SHA2 256 bits MHz; HSE_CLK = 80
bits cipher. MHz.
tSHA2_256_ Memory verification — 13.6 — ms Device running from —
1024KB of 1024 KB of PLL (clocking option
application firmware, A). CORE_CLK = 160
using SHA2 256 bits MHz; HSE_CLK = 80
bits cipher. MHz.
tCMAC_64KB Memory verification — 4.5 — ms Device running from —
of 64 KB of PLL (clocking option
application firmware, B). CORE_CLK = 120
using AES-128 MHz; HSE_CLK = 120
CMAC cipher. MHz.
tCMAC_1024KB Memory verification — 69.9 — ms Device running from —
of 1024 KB of PLL (clocking option
application firmware, B). CORE_CLK = 120
using AES-128 MHz; HSE_CLK = 120
CMAC cipher. MHz.
Table continues on the next page...

---

*Page 16*

Table 11. HSE firmware memory verification time examples ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
tGMAC_64KB Memory verification — 1.3 — ms Device running from —
of 64 KB of PLL (clocking option
application firmware, B). CORE_CLK = 120
using AES-128 MHz; HSE_CLK = 120
GMAC cipher. MHz.
tGMAC_1024KB Memory verification — 18.7 — ms Device running from —
of 1024 KB of PLL (clocking option
application firmware, B). CORE_CLK = 120
using AES-128 MHz; HSE_CLK = 120
GMAC cipher. MHz.
tHMAC_64KB Memory verification — 0.7 — ms Device running from —
of 64 KB of PLL (clocking option
application firmware, B). CORE_CLK = 120
using AES-128 MHz; HSE_CLK = 120
HMAC cipher. MHz.
tHMAC_1024KB Memory verification — 9.12 — ms Device running from —
of 1024 KB of PLL (clocking option
application firmware, B). CORE_CLK = 120
using AES-128 MHz; HSE_CLK = 120
HMAC cipher. MHz.
tRSA_64KB Memory verification — 15.4 — ms Device running from —
of 64 KB of PLL (clocking option
application firmware, B). CORE_CLK = 120
using RSA 2048 MHz; HSE_CLK = 120
cipher. MHz.
tRSA_1024KB Memory verification — 23.8 — ms Device running from —
of 1024 KB of PLL (clocking option
application firmware, B). CORE_CLK = 120
using RSA 2048 MHz; HSE_CLK = 120
cipher. MHz.
tECDSA_64KB Memory verification — 53.95 — ms Device running from —
of 64 KB of PLL (clocking option
application firmware, B). CORE_CLK = 120
using ECDSA 521 MHz; HSE_CLK = 120
bits cipher. MHz.
tECDSA_1024KB Memory verification — 62.34 — ms Device running from —
of 1024 KB of PLL (clocking option
application firmware, B). CORE_CLK = 120
using ECDSA 521 MHz; HSE_CLK = 120
bits cipher. MHz.
tSHA2_256_64KB Memory verification — 0.64 — ms Device running from —
of 64 KB of PLL (clocking option
application firmware, B). CORE_CLK = 120
Table continues on the next page...

---

*Page 17*

Table 11. HSE firmware memory verification time examples ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
using SHA2 256 bits MHz; HSE_CLK = 120
bits cipher. MHz.
tSHA2_256_ Memory verification — 9.07 — ms Device running from —
1024KB of 1024 KB of PLL (clocking option
application firmware, B). CORE_CLK = 120
using SHA2 256 bits MHz; HSE_CLK = 120
bits cipher. MHz.

#### 6.2 Supply Monitoring

Certain monitors are present on certain devices. See Power Management chapter in reference manual.
Table 12. Supply Monitoring
Symbol Description Min Typ Max Unit Condition Spec
Number
LVD_V15 Low Voltage Detect 1.34 1.38 1.42 V — —
(LVD) on V15,
deassert threshold
(in FPM)
HVD_V15 High Voltage Detect — 2.5 — V — —
(HVD) on V15,
assert threshold (in
FPM)
LVR_VDD_HV_A LVR on VDD_HV_A, 2.77 2.85 2.93 V — —
assert threshold (in
FPM)
LVR_VDD_HV_A LVR on VDD_HV_A, 2.77 2.85 2.93 V — —
assert threshold (in
RPM)
— VDD_HV_A LVR — 18.75 — mV — —
monitor hysteresis
HVD_VDD_HV_A HVD on VDD_HV_A, 5.787 5.887 5.987 V — —
assert threshold (in
FPM)
— VDD_HV_A HVD — 37.5 — mV — —
monitor hysteresis
LVR_VDD_HV_B LVR on VDD_HV_B, 2.77 2.85 2.93 V — —
assert threshold (in
RPM)
HVD_VDD_HV_B HVD on VDD_HV_B, 5.787 5.887 5.987 V — —
assert threshold (in
FPM)
Table continues on the next page...

---

*Page 18*

Table 12. Supply Monitoring ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
LVD_VDD_HV_A Low Voltage 4.33 4.41 4.49 V — —
Detect (LVD5A) on
VDD_HV_A, assert
threshold (in FPM)
— VDD_HV_A — 37.5 — mV — —
LVD monitor
hysteresis
VPOR_VDD_HV_A Power-On-Reset 0.9 1.5 2.2 V — —
(VPOR) on
VDD_HV_A,
deassert threshold
VREF12 Bandgap reference, 1.18 1.2 1.22 V — —
trimmed

#### 6.3 Recommended Decoupling Capacitors

Table 13. Recommended Decoupling Capacitors
Symbol Description Min Typ Max Unit
CDEC Decoupling capacitor (one per 70 100 — nF
[1] [2] [3]
supply pin)
[2]
CBULK Input supply bulk capacitor — 4.7 — μF
[4] [5] [6]
COUT_V11 V11 (1.1V Regulator) — 1 — μF
output capacitor (MCXE315/
MCXE316/MCXE317 and
[2]
MCXE31B)
COUT_V25 V25 (2.5V Regulator) output 140 220 — nF
[1] [2]
capacitor
[1] These capacitors must be placed as close as possible to the corresponding supply and ground pins.
[2] All capacitors must be low ESR ceramic capacitors (for example, X7R). The minimum recommendation is after considering component aging and tolerance.
[3] Optionally, 1 nF capacitors can be added in parallel to the decoupling capacitors.
[4] These capacitors must be placed close to the source.
[5] For devices where the VDD_HV_B domain is present, if the VDD_HV_B supply is different supply from VDD_HV_A, a dedicated bulk capacitor is needed.
[6] It is also possible to use higher capacitance values (for example, 10 μF) in place of the 4.7 μF capacitor.

---

*Page 19*

#### 6.3.1 Recommended Decoupling Capacitor diagrams

V25
6 V25
COUT_V25
V11
7 V11
COUT_V11
VREFL 4
VSS 8
VDD_HV_A
VSS 30
5 VDD_HV_A
CBULK CDEC 31 VDD_HV_A
VREFH
3 VREFH
CDEC
Figure 2. 48-pin LQFP decoupling capacitor pinout diagram (MCXE315/MCXE316)

---

*Page 20*

V25
11 V25
COUT_V25
V11
13 V11
COUT_V11 CDEC 60 V11
VREFL 9
VSS 12
VDD_HV_A
VSS 14
10 VDD_HV_A
VSS 16
CBULK CDEC CDEC CDEC 37 VDD_HV_A
VSS 38
62 VDD_HV_A
VSS 61
87 VDD_HV_A
VSS 86
VREFH
8 VREFH
CDEC
Figure 3. 100-pin HDQFP decoupling capacitor pinout diagram (MCXE315/MCXE316 and MCXE317)

---

*Page 21*

V25
19 V25
COUT_V25
V11
21 V11
COUT_V11 CDEC CDEC CDEC 59 V11
106 V11
VREFL 17
149 V11
VSS 20
VSS 22
VSS 24
VDD_HV_A VSS 37
VSS 58
18 VDD_HV_A
CBULK CDEC CDEC CDEC CDEC CDEC
VSS 78
38 VDD_HV_A
VSS 107
57 VDD_HV_A
VSS 127
77 VDD_HV_A
VSS 150
108 VDD_HV_A
VSS 168
128 VDD_HV_A
151 VDD_HV_A
169 VDD_HV_A
VREFH
16 VREFH
CDEC
Figure 4. 172-pin HDQFP decoupling capacitor pinout diagram (MCXE317)

---

*Page 22*

V25
19 V25
COUT_V25
V11
21 V11
COUT_V11 CDEC CDEC CDEC 59 V11
106 V11
149 V11
V15
20 V15
CDEC CDEC CDEC 60 V15
105 V15
148 V15
VREFL 17
V15
VSS 22
COUT_V15_NPN RBTC15 VSS 24
VSS 37
Q_V15_NPN 11 VRC_CTRL
VSS 58
VSS 78
VDD_HV_NPN
VSS 107
BJT option
VSS 127
VSS 150
VSS 168
VDD_HV_B
38 VDD_HV_B
CBULK CDEC CDEC 57 VDD_HV_B
77 VDD_HV_B
VDD_HV_A
18 VDD_HV_A
CBULK CDEC CDEC 108 VDD_HV_A
128 VDD_HV_A
151 VDD_HV_A
169 VDD_HV_A
VREFH
16 VREFH
CDEC
Figure 5. 172-pin HDQFP decoupling capacitor pinout diagram (MCXE31B)

---

*Page 23*

#### 6.4 V15 regulator (BJT option, NPN ballast transistor control) electrical specifications

MCXE31B support a linear regulator stage, with a dedicated pin to control an external NPN bipolar transistor. The chip hardware
design guidelines document lists the recommended part numbers for the external devices.
Table 14. V15 regulator (BJT option, NPN ballast transistor control) electrical specifications
Symbol Description Min Typ Max Unit Condition Spec
Number
V15 V15 output — 1.51 — V — —
V15 V15 input — 1.5 — V — —
IBCTL IBCTL (V15 reg) 10 — — mA — —
source
IBCTL IBCTL (V15 reg) sink — — -50 uA — —
tsettle_lm Required setting 2 — — us — —
time from activating
last mile regulator to
load change
VDD_HV_NPN Input voltage supply 2.5 3.3 or 5 — V — —
for NPN external
ballast transistor
BJT option VDD_HV_NPN
IBCTL

#### PTE13 | VRC_CTRL Q_V15_NPN

External NPN
Ballast transistor
RBTC15
2.2k
V15 High-current
Logic supply (1.5V)
V15
COUT_V15_NPN
2.2uF
Figure 6. Ballast circuit

#### 6.5 Supply currents

Note: All data in this table is preliminary and based on first samples.
Typical current numbers are indicative for typical silicon process and may vary based on the silicon distribution and user
configuration. Typical conditions assumes VDD_HV_A = VREFH = 5 V, VDD_HV_B = 5V (if the VDD_HV_B domain present in the
device), temperature = 25 °C, and typical silicon process unless otherwise stated. In STANDBY configuration, no current flows
through the V15 supply.

---

*Page 24*

Table 15. STANDBY mode supply currents
[1]
STANDBY
[2] [2]
VDD_HV_A VDD_HV_B
All clocks &
FIRC ON (24
peripherals SIRC ON All Config.
MHz)
OFF
(μA) (μA)
(mA)
Chip Ambient Temperature (°C) (μA)
[3]
MCX E31B 25, typ 50 52 0.91 1.8
[4]
25, max 153 153 1.09 3.8
[3]
85, typ 315 316 1.18 6.1
[4]
85, max 900 910 1.78 15.4
[3]
105, typ 498 530 1.40 8.5
[4]
105, max 1672 1682 2.55 26.2
[3]
125, typ 932 998 1.88 18.5
[4]
125, max 2638 2650 3.5 47.3
[3]
MCX E317 25, typ 40 41 0.887 NA
[4]
25, max 79 80 1.031
[3] 1.027
85, typ 178 178
[4]
85, max 496 497 1.422
[3]
105, typ 350 346 1.197
[4]
105, max 994 997 1.924
[3]
125, typ 620 611 1.457
[4]
125, max 1788 1792 2.761
[3]
MCX 25, typ 38.9 39.8 1.365 NA
E315/MCX E316
[4]
25, max 77.2 79.8 1.823
[3] 1.480
85, typ 144.3 144.9
[4]
85, max 491.5 494.8 2.263
[3]
105, typ 263.8 264.2 1.559
[4]
105, max 937.4 947.1 2.597
Table continues on the next page...

---

*Page 25*

Table 15. STANDBY mode supply currents ...continued
[3]
125, typ 508.5 510 1.811
[4]
125, max 1740.1 1760.3 3.488
[1] See the configurations in Table 21 .
[2] IO load current is not included. The actual current requirements for IOs will depend on the I/O configuration in the application.
[3] “typ” is indicative of the average current numbers at the nominal internally regulated V11 supply voltage, VDD_HV_A = 5.0V, VDD_HV_B = 5.0V, for the
typical silicon process..
[4] “max” is indicative of the maximum current numbers at the maximum internally regulated V11 supply voltage (1.16 V), VDD_HV_A = 5.5V, VDD_HV_B = 5.5V,
for the fast silicon process.
Note: All data in this table is preliminary and based on first samples.
Typical current numbers are indicative for typical silicon process and may vary based on the silicon distribution and user
configuration. Typical conditions assumes VDD_HV_A = VREFH = 5 V, VDD_HV_B = 5V (if the VDD_HV_B domain present in the
device), temperature = 25 °C, and typical silicon process unless otherwise stated.
Table 16. Low speed RUN mode supply currents
[1]
Low Speed RUN Mode (mA)
.
[2]
[2] [2]
[2] [2]
[2] [2]
All Config
BOOT Mode BOOT Mode
Low Speed RUN [Last Mile Disabled] Low Speed RUN [Last Mile Enabled]
[Last Mile Enabled]
[Last Mile Disabled] [Last Mile Enabled] Low Speed RUN [Last Mile Disabled] Low Speed RUN
[Clock Option E] FIRC @3 MHz [Clock Option E] FIRC @3 MHz
[Clock Option D] FIRC @48 MHz [Clock Option D] FIRC @48 MHz
[Clock Option C] FIRC @ 24 MHz [Clock Option C] FIRC @ 24 MHz
,
[4]
,
[4] [4]
, ,
[4]
,
[4]
,
[4]
[3]
[3] [3] [3] [3] [3] [3]
/ V11 / V11 / V11 / V11 / V11 / V11
[5] [5] [5] [5] [5] [5]
V15 V15 V15 V15 V15 V15
VDD_HV_B
Chip
Ambient Temperature (°C) VDD_HV_A VDD_HV_A VDD_HV_A VDD_HV_A VDD_HV_A VDD_HV_A
[6]
MCX E31B 25, typ 20.5 - 2.8 17.9 6.4 - 2.8 4.5 37.2 - 2.9 34 0.6
[7]
25, max 29.4 - 3.3 27.2 14.8 - 3.3 12.6 46.8 - 3.4 46.6 0.8
[6] 0.6
85, typ 34.2 - 2.9 31.2 19.7 - 2.9 17.5 50.4 - 2.9 47.3
[7] 0.8
85, max 71.6 - 3.5 68.7 56.2 - 3.4 54 89.1 - 3.5 86.2
[6]
105, typ 46.1 - 2.9 43.1 31.7 - 2.9 29.3 62.2 - 2.9 59.2 0.6
105, 114 - 3.7 111 99.1 - 3.6 96.1 131 - 3.9 128 0.8
[7]
max
[6]
125, typ 69.9 - 3.0 66.8 55.8 - 3.0 53.1 86 - 3.1 83 0.6
Table continues on the next page...

---

*Page 26*

Table 16. Low speed RUN mode supply currents ...continued
125, 161 - 4.2 159 148 - 4.1 145 178 - 4.3 176 0.8
[7] ,
max
[6]
MCX E317 25, typ 15 NA NA 5 NA NA 26 NA NA NA
[7]
25, max 20 10 32
[6]
85, typ 20 10 31
[7]
85, max 35.2 24.6 46.4
[6]
105, typ 26.1 16.2 37
105, 52.9 42.6 64.2
[7]
max
[6]
125, typ 35.3 25.3 46.4
125, 79.8 66.9 90.1
[7] , [9] ,
max
[10]
[6]
MCX 25, typ 12.9 NA NA 4.4 NA NA 22.4 NA NA NA
E315/MCX
[7]
E316 25, max 14.9 6.0 24.8
[6]
85, typ 16.0 7.5 25.6
[7]
85, max 31.0 22.2 41.1
[6]
105, typ 19.1 10.5 28.7
105, 45.8 36.8 55.6
[7]
max
[6]
125, typ 25.2 16.5 34.7
125, 73.2 64.3 82.4
[7] , [8] ,
max
[10]
[1] Current numbers are for reduced configuration and may vary based on user configuration and silicon process variation.
[2] See the example configurations in Table 21
[3] IO load current is not included. The actual current requirements for IOs will depend on the I/O configuration in the application.
[4] RUN IDD @ VDD_HV_A includes Flash memory read current from the V25 voltage rail.
[5] RUN IDD @ V15 includes Flash memory read current from the V11 voltage rail
[6] “typ” is indicative of the average current numbers at the nominal internally regulated V11 supply voltage, VDD_HV_A = 5.0V, VDD_HV_B = 5.0V, V15 = 1.5V,
for the typical silicon process.
[7] “max” is indicative of the maximum current numbers at the maximum internally regulated V11 supply voltage (1.16 V), VDD_HV_A = 5.5V, VDD_HV_B = 5.5V,
V15 = 1.65V, for the fast silicon process.
[8] For the maximum allowable RUN current in an application, the junction temperature must be kept below the maximum specification, T J < 135°C, to avoid
self-heating.
[9] For the maximum allowable RUN current in an application, the junction temperature must be kept below the maximum specification, T J < 135°C, to avoid
self-heating.
[10] If the total power dissipation would cause the junction temperature to be exceeded when VDD_HV_A is at 5V, then VDD_HV_A should be limited to operate at
3.3V.
Note: All data in this table is preliminary and based on first samples.

---

*Page 27*

Typical current numbers are indicative for typical silicon process and may vary based on the silicon distribution and user
configuration. Typical conditions assumes VDD_HV_A = VREFH = 5 V, VDD_HV_B = 5V (if the VDD_HV_B domain present in the
device), temperature = 25 °C and typical silicon process unless otherwise stated.
Table 17. RUN mode supply currents (peripherals disabled) for MCX E31B
[1]
RUN Mode (mA)
[2] [2]
All. Config. All. Config.
[Clock Option F]
[Clock Option B] [Clock Option A]
[2] [2] [2]
Cortex M7 @80 MHz
Cortex M7 @120 MHz Cortex M7 @160 MHz
Min. Config. Min. Config. Min. Config.
[4] ,
[5]
[4]
/ V11 / V11 / V11
[3] [3] [3]
Ambient
V15 V15 V15
VDD_HV_B
Chip Temperature (°C)
VDD_HV_A
[6] 3.1
MCX E31B 25, typ 51.3 54.8 69.6 0.6
[7] 3.6
25, max 60.2 64.5 80.4 0.8
[6] 3.2
85, typ 64.5 68.1 83.1 0.6
3.9
85, max 104 108 124 0.8
3.2
105, typ 75.4 79 93.9 0.6
[7] 149 166 4.0
105, max 145 0.8
3.3
125, typ 97.4 101.2 116.4 0.6
, [8] 4.3
125, max 191 196 212 0.8
[1] Current numbers are for reduced configuration and may vary based on user configuration and silicon process variation.
[2] See the configurations in Table 22 .
[3] RUN IDD @ V15 includes Flash memory read current from the V11 voltage rail.
[4] IO load current is not included. The actual current requirements for IOs will depend on the I/O configuration in the application.
[5] RUN IDD @ VDD_HV_A includes Flash memory read current from the V25 voltage rail.
[6] “typ” is indicative of the average current numbers at the nominal internally regulated V11 supply voltage, VDD_HV_A = 5.0V, VDD_HV_B = 5.0V, V15 = 1.5V,
for the typical silicon process.
[7] "max" is indicative of the maximum current numbers at the maximum internally regulated V11 supply voltage (1.16 V), VDD_HV_A = 5.5V, VDD_HV_B = 5.5V,
V15= 1.65V, for the fast silicon process.
[8] For the maximum allowable RUN current in an application, the junction temperature must be kept below the maximum specification, T J < 135°C, to avoid
self-heating.
Note: The data in this table is preliminary and based on first samples.
Typical current numbers are indicative for typical silicon process and may vary based on the silicon distribution and user
configuration. Typical conditions assumes VDD_HV_A = VREFH = 5 V, VDD_HV_B = 5V (if the VDD_HV_B domain present in the
device), temperature = 25 °C and typical silicon process unless otherwise stated.

---

*Page 28*

Table 18. RUN mode supply currents (peripherals disabled) for MCX E315/MCX E316 and MCX E317
[1]
RUN Mode (mA)
[2] [2]
Min. Config. Min. Config.
Cortex M7 @80 MHz Cortex M7 @120 MHz
[Clock Option F] [Clock Option B]
,
[4]
,
[4]
[3] [3]
/ V11 / V11
[5] [5]
Ambient
V15 V15
Chip Temperature (°C)
VDD_HV_A VDD_HV_A
[6]
MCX E317 25, typ 37 NA 37 NA
[7]
25, max 44 47
[6]
85, typ 42 43
[7]
85, max 58.5 59.7
[6]
105, typ 48.1 48.7
[7]
105, max 76.4 77.8
[6]
125, typ 56.5 57
[7] , [8] , [9]
125, max 98.7 99.9
[6] NA
MCX E315/MCX E316 25, typ 34.9 NA 36.5
[7]
25, max 39.1 41.1
[6]
85, typ 38.1 39.8
[7]
85, max 54.2 55.9
[6]
105, typ 41.5 43.2
[7]
105, max 69.1 71.1
[6]
125, typ 47.7 49.4
[7] , [8] , [9]
125, max 97 99.1
[1] Current numbers are for reduced configuration and may vary based on user configuration and silicon process variation.
[2] See the configurations in Table 22 .
[3] IO load current is not included. The actual current requirements for IOs will depend on the I/O configuration in the application.
[4] RUN IDD @ VDD_HV_A includes Flash memory read current from the V25 voltage rail.
[5] RUN IDD @ V15 includes Flash memory read current from the V11 voltage rail.
[6] “typ” is indicative of the average current numbers at the nominal internally regulated V11 supply voltage, VDD_HV_A = 5.0V, VDD_HV_B = 5.0V, V15 = 1.5V,
for the typical silicon process.
[7] “max” is indicative of the maximum current numbers at the maximum internally regulated V11 supply voltage (1.16 V), VDD_HV_A = 5.5V, VDD_HV_B = 5.5V,
V15 = 1.65V, for the fast silicon process.
[8] For the maximum allowable RUN current in an application, the junction temperature must be kept below the maximum specification, T J < 135°C, to avoid
self-heating.

---

*Page 29*

[9] If the total power dissipation would cause the junction temperature to be exceeded when VDD_HV_A is at 5V, then VDD_HV_A should be limited to operate at
3.3V.
Note: The data in this table is preliminary and based on first samples.
Typical current numbers are indicative for typical silicon process and may vary based on the silicon distribution and user
configuration. Typical conditions assumes VDD_HV_A = VREFH = 5 V, VDD_HV_B = 5V (if the VDD_HV_B domain present in the
device), temperature = 25 °C and typical silicon process unless otherwise stated.
Table 19. Example RUN mode configuration supply currents for MCX E31B
[1]
RUN Mode (mA)
[2] [2]
[2]
[2] [2]
All Config. All Config.
Config. 3
Config. 1 Config. 2
Cortex M7 @80 MHz
Cortex M7 @160 MHz Cortex M7 @120 MHz
[4] ,
[5]
[4]
/ V11 / V11 / V11
[3] [3] [3]
Ambient
V15 V15 V15
VDD_HV_B
Chip Temperature (°C)
VDD_HV_A
[6] 3.1
MCX E31B 25, typ 102 80 68 0.6
[7] 3.6
25, max 115 92 79 0.8
[6] 3.2
85, typ 116 94 81.8 0.6
[7] 3.9
85, max 160 137 123 0.8
[6] 3.2
105, typ 128 105 93 0.6
[7] 4.0
105, max 203 179 165 0.8
[6] 3.3
125, typ 151 128 116 0.6
[7] , [8] 4.5
125, max 250 226 213 0.8
[1] Current numbers are for reduced configuration and may vary based on user configuration and silicon process variation.
[2] See the configurations in Table 22 .
[3] RUN IDD @ V15 includes Flash memory read current from the V11 voltage rail.
[4] IO current is not included. The actual current requirements for IOs will depend on the I/O configuration in the application.
[5] RUN IDD @ VDD_HV_A includes Flash memory read current from the V25 voltage rail.
[6] “typ” is indicative of the average current numbers at the nominal internally regulated V11 supply voltage, VDD_HV_A = 5.0V, VDD_HV_B = 5.0V, V15 = 1.5V,
for the typical silicon process.
[7] “max” is indicative of the maximum current numbers at the maximum internally regulated V11 supply voltage (1.16 V), VDD_HV_A = 5.5V, VDD_HV_B = 5.5V,
V15 = 1.65V, for the fast silicon process.
[8] For the maximum allowable RUN current in an application, the junction temperature must be kept below the maximum specification, T J < 135°C, to avoid
self-heating.

---

*Page 30*

Table 20. Example RUN mode configuration supply currents for MCX E315/MCX E316 and MCX E317
[1]
RUN Mode (mA)
[2] [2]
Config. 1 Config. 2
Cortex M7 Cortex M7
@120 MHz @80 MHz
[4] [4]
, ,
[3] [3]
/V11 / V11
[5]
[5]
Ambient
V15 V15
Chip Temperature (°C)
VDD_HV_A VDD_HV_A
[6]
MCX E317 25, typ 54 NA 44 NA
[7]
25, max 62 54
[6]
85, typ 60 49
[7]
85, max 76.4 66.3
[6]
105, typ 65.8 55
[7]
105, max 94.4 84.4
[6]
125, typ 78.6 64.7
[7] , [8] , [9]
125, max 120.7 110.5
[6]
MCX E315 and 25, typ 53.4 NA 43 NA
MCX E316
[7]
25, max 57.7 51.2
[6]
85, typ 56.8 50.8
[7]
85, max 73.2 66
[6]
105, typ 60.1 54
[7]
105, max 88.5 81.9
[6]
125, typ 66.3 60.2
[7] , [8] , [9]
125, max 115.3 109.3
[1] Current numbers are for reduced configuration and may vary based on user configuration and silicon process variation.
[2] See the configurations in Table 22 .
[3] IO current is not included. The actual current requirements for IOs will depend on the I/O configuration in the application.
[4] RUN IDD @ VDD_HV_A includes Flash memory read current from the V25 voltage rail.
[5] RUN IDD @ V15 includes Flash memory read current from the V11 voltage rail.
[6] “typ” is indicative of the average current numbers at the nominal internally regulated V11 supply voltage, VDD_HV_A = 5.0V, VDD_HV_B = 5.0V, V15 = 1.5V, for
the typical silicon process
[7] "max” is indicative of the maximum current numbers at the maximum internally regulated V11 supply voltage (1.16 V), VDD_HV_A = 5.5V, VDD_HV_B = 5.5V,
V15 = 1.65V, for the fast silicon proce
[8] For the maximum allowable RUN current in an application, the junction temperature must be kept below the maximum specification, T J < 135°C, to
avoid self-heating.
[9] If the total power dissipation would cause the junction temperature to be exceeded when VDD_HV_A is at 5V, then VDD_HV_A should be limited to operate at 3.3V.

---

*Page 31*

#### 6.6 Operating mode

Table 21. STANDBY and low speed RUN configuration options
STANDBY STANDBY STANDBY BOOT Mode Low Speed RUN FIRC Mode
[1] [1]
(OptionC , (OptionE ,
[1]
All OFF SIRC ON FIRC ON (OptionD ,
FIRC @24 MHz) FIRC @ 3MHz)
MODULE FIRC @48 MHz)
Core M7 OFF OFF OFF 24 MHz 3 MHz 48 MHz
ELE_HSEB OFF OFF OFF 24 MHz 3 MHz 48 MHz
FIRC OFF OFF 24 MHz 24 MHz 3 MHz 48 MHz
FXOSC OFF OFF OFF OFF OFF OFF
SIRC OFF ON OFF ON ON ON
PLL OFF OFF OFF OFF OFF OFF
Flash OFF OFF OFF ON ON ON
eDMA OFF OFF OFF ON ON ON
FlexCAN All OFF All OFF All OFF All OFF All OFF All OFF
LPUART All OFF All OFF All OFF All OFF All OFF All OFF
LPSPI All OFF All OFF All OFF All OFF All OFF All OFF
LPI2C All OFF All OFF All OFF All OFF All OFF All OFF
EMAC/GMAC OFF OFF OFF OFF OFF OFF
eMIOS All OFF All OFF All OFF All OFF All OFF All OFF
SAR_ADC All OFF All OFF All OFF All OFF All OFF All OFF
LPCMP All OFF All OFF All OFF All OFF All OFF All OFF
[1] See clocking use case examples in the Clocking chapter of the MCXE31 Reference Manual.
Table 22. RUN mode configuration options
Min. Config. Min. Config. Min. Config. Config. 1 Config. 2 Config. 35
[1] [1] [1]
(OptionF ), (OptionB ), (OptionA ),
Cortex M7 Cortex M7 Cortex M7
PLL@80 MHz PLL@120 MHz PLL@160 MHz
MODULE @160MHz @120MHz @80MHz
Core M7 80 MHz 120 MHz 160 MHz 160 MHz 120 MHz 80 MHz
[2]
ELE_HSEB 80 MHz 120 MHz 80 MHz 80 MHz 120 MHz 80 MHz
FIRC ON ON ON ON ON ON
Table continues on the next page...

---

*Page 32*

Table 22. RUN mode configuration options ...continued
FXOSC ON ON ON ON ON ON
SIRC ON ON ON ON ON ON
PLL ON ON ON ON ON ON
Flash ON ON ON ON ON ON
eDMA ON ON ON ON ON ON
[3]
FlexCAN All OFF All OFF All OFF 2x 6x 1x
[4]
LPUART All OFF All OFF All OFF 4x 8x 7x
[5]
LPSPI All OFF All OFF All OFF 4x 4x 3x
[6]
LPI2C All OFF All OFF All OFF 2x 2x All OFF
[7]
EMAC/GMAC OFF OFF OFF OFF OFF OFF
SAI OFF OFF OFF OFF OFF OFF
QSPI OFF OFF OFF OFF OFF OFF
[8]
eMIOS All OFF All OFF All OFF 3x 2x 2x
[9]
SAR_ADC All OFF All OFF All OFF 3x 2x 2x
[10]
LPCMP All OFF All OFF All OFF 2x All OFF All OFF
[1] See clocking use case examples in the Clocking chapter of the MCXE31 Reference Manual.
[2] ELE_HSEB: After start-up, the HSE core is in WFI.
[3] • FlexCAN0: Transmitting an 8-byte CAN-FD data frame at 5 Mbps, every 10 ms.
• FlexCAN1: Transmitting a 64-byte CAN-FD data frame at 2 Mbps, every 20 ms.
• FlexCAN2-5: Transmitting an 8-byte CAN data frame at 500 Kbps, every 20 ms.
[4] LPUART0-15: Transmitting at 19200 bps, every 100ms.
[5] • LPSPI0: Transmitting 32 bits at 20 Mbps (GPIO Fast pads), every 5 ms.
• LPSPI1-5: Transmitting 32 bits at 1 Mbps, every 5 ms.
[6] LPI2C0-1: Transmitting 3 bytes at 400 Kbps, every 100ms.
[7] EMAC/GMAC: ON for MII interface.
[8] • eMIOS0: 6 channels in PWM mode @ 20 KHz.
• eMIOS1-2: 8 channels in PWM mode @ 400 Hz.
[9] • SAR_ADC0: 16 channels at 400 Hz rate, BCTU triggered.
• SAR_ADC1-2: 4 channels at 20 KHz rate, BCTU triggered.
[10] LPCMP0: 8 channels enabled; LPCMP1-2: 4 channels enabled.

#### 6.7 Cyclic wake-up current

The cyclic wake-up current is the calculated average current consumption during the periodic switching between RUN mode and
STANDBY mode. This average current can be calculated with the following formula:
ICYCL = RUN Current According to Ratio + STANDBY Current According to Ratio
Where the Current According to Ratio value is calculated as follows:
Current According to Ratio = Supply Current × Ratio of Duration
As an example, the following data was obtained with an application that periodically (every 40ms) alternates between RUN mode,
for approximately 200μs to scan several GPIO inputs (51 GPIOS), and spends the rest of the time in STANDBY mode.

---

*Page 33*

Table 23. Cyclic wake-up current example
[2]
Device Supply Duration [ms] Ratio of Current ICYCL -
Chip [1] [3]
Operating Mode Current [μA] Duration According to Average current
[4] [5]
Ratio [μA] [μA]
MCXE31B RUN 20000 0.2 0.005 100 159.7
STANDBY 60 39.8 0.995 59.7
[1] The supply current is obtained through the measurements of the current during the corresponding operating mode.
[2] The duration is defined by the application (how much time will the device spend in the according operating mode).
[3] The ratio of duration is obtained by dividing the duration of the corresponding operating mode by the total duration of the application.
[4] The current according to ratio is obtained by multiplying the supply current and the ratio of duration related to the proper operating mode.
[5] The average current is calculated by the addition of each device operating mode’s current according to ratio.

### 7 I/O parameters

#### 7.1 GPIO DC electrical specifications, 3.3V Range (2.97V - 3.63V)

The leakage current on the GPIO pins is specified as a function of the pad type (Standard, Standard Plus, Medium, Fast, or GPI)
and the number of Analog functions (CMP and ADC channels) multiplexed per pin.
For other devices, the "Analog Function Count" is defined from the number of CMP and ADC channels multiplexed to a given
pin. This information can be obtained from the "Direct Signals" column in the IOMUX files attached to the Reference Manual. The
"Analog Function Count" is shown in the Condition column of the following table.
Table 24. GPIO DC electrical specifications, 3.3V Range (2.97V - 3.63V)
Symbol Description Min Typ Max Unit Condition Spec
Number
VIH Input high level DC 0.70 x — VDD_HV V VDD_HV_A/B = 3.3V —
voltage threshold VDD_HV _A/B +
_A/B 0.3
VIL Input low level DC VSS - 0.3 — 0.30 x V VDD_HV_A/B = 3.3V —
voltage threshold VDD_HV
_A/B
WFRST RESET Input — — 33 ns — —
Filtered pulse
[1]
width
WNFRST RESET Input 100 — — ns — —
not filtered pulse
[2]
width
ILKG_33_S0 3.3V input leakage -133 — 300 nA Pins with Analog —
current for Standard Function Count = 0
[3]
GPIO
ILKG_33_S1 3.3V input leakage -545 — 445 nA Pins with Analog —
current for Standard Function Count = 1
[3]
GPIO
ILKG_33_S2 3.3V input leakage -749 — 517 nA Pins with Analog —
current for Standard Function Count = 2,
[3]
GPIO plus PTA12, PTD1
Table continues on the next page...

---

*Page 34*

Table 24. GPIO DC electrical specifications, 3.3V Range (2.97V - 3.63V) ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
ILKG_33_S3 3.3V input leakage -1288 — 679 nA Pins with Analog —
current for Standard Function Count = 3,
[3]
GPIO plus PTD0
ILKG_33_SP0 3.3V input leakage -370 — 575 nA Pins with Analog —
current for Standard Function Count = 0
Plus GPIO and
[3]
RESET IO
ILKG_33_SP1 3.3V input leakage -660 — 659 nA Pins with Analog —
current for Standard Function Count = 1
Plus GPIO and
[3]
RESET IO
ILKG_33_SP2 3.3V input leakage -1094 — 794 nA Pins with Analog —
current for Standard Function Count = 2
Plus GPIO and
[3]
RESET IO
ILKG_33_M0 3.3V GPIO input -792 — 750 nA Pins with Analog —
leakage current for Function Count = 0
[3]
Medium GPIO
ILKG_33_M1 3.3V GPIO input -989 — 824 nA Pins with Analog —
leakage current for Function Count = 1,
[3]
Medium GPIO plus PTC16, PTD5
ILKG_33_M2 3.3V GPIO input -1233 — 1248 nA Pins PTD6 and PTE8 —
leakage current for
[3]
Medium GPIO
ILKG_33_I 3.3V input leakage -120 — 120 nA — —
[3]
current for GPI
VHYS_33 Input hysteresis 0.06 x — — mV Always Enabled, —
voltage VDD_HV applies to MCXE315/
_A/B MCXE316/ MCXE317
and MCXE31B
devices.
CIN GPIO Input 2 4 6 pF add 2pF for package/ —
capacitance parasitic
IPU_33 3.3V GPIO pull up/ 20 — 60 kΩ pull up @ 0.3 x VDD_ —
down resistance HV_A/B, pull down @
0.7 x VDD_HV_A/B
IOH_33_S 3.3V output 1.0 — — mA VOH >= VDD_HV_A/B —
high current for - 0.7V
[4] [5]
Standard GPIO
IOH_33_SP 3.3V output high 1.5 — — mA DSE = 0, VOH >= —
current for Standard VDD_HV_A/B - 0.7V
Table continues on the next page...

---

*Page 35*

Table 24. GPIO DC electrical specifications, 3.3V Range (2.97V - 3.63V) ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
Plus GPIO and
[4] [5]
RESET IO
IOH_33_M 3.3V output high 3 — — mA DSE = 0, VOH >= —
current for Medium VDD_HV_A/B - 0.7V
[4] [5]
GPIO
IOH_33_SP 3.3V output high 3 — — mA DSE = 1, VOH >= —
current for Standard VDD_HV_A/B - 0.7V
Plus GPIO and
[4] [5]
RESET IO
IOH_33_M 3.3V output high 6 — — mA DSE = 1, VOH >= —
current for Medium VDD_HV_A/B - 0.7V
[4] [5]
GPIO
IOL_33_S 3.3V output low 1.0 — — mA VOL <= 0.7V —
current for Standard
[4] [5]
GPIO
IOL_33_SP 3.3V output low 1.5 — — mA DSE =0, VOL <= 0.7V —
current for Standard
Plus GPIO and
[4] [5]
RESET IO
IOL_33_M 3.3V output low 3.0 — — mA DSE =0, VOL <= 0.7V —
current for Medium
[4] [5]
GPIO
IOL_33_SP 3.3V output low 3 — — mA DSE =1, VOL <= 0.7V —
current for Standard
Plus GPIO and
[4] [5]
RESET IO
IOL_33_M 3.3V output low 6 — — mA DSE =1, VOL <= 0.7V —
current for Medium
[4] [5]
GPIO
FMAX_33_S 3.3V maximum — — 10 MHz 2.9V - 3.6V CL(max) = —
frequency for 25pF
[4] [6]
Standard GPIO
FMAX_33_SP 3.3V maximum — — 25 MHz 2.9V - 3.6V CL (max) = —
frequency for 25pF
Standard Plus
[4] [6]
GPIO
FMAX_33_M 3.3V maximum — — 50 MHz 2.9V - 3.6V CL (max) = —
frequency for 25pF
[4] [6]
Medium GPIO
IOHT Output high current — — 100 mA — —
[7]
total for all ports
[1] Maximum length of RESET pulse will be filtered by an internal filter on this pin.
[2] Minimum length of RESET pulse, guaranteed not to be filtered by the internal filter.

---

*Page 36*

[3] A positive value is leakage flowing into pin with pin at VDD_HV_A/B (the GPIO supply level); a negative value is leakage flowing out the pin with the pin at
ground.
[4] GPIO output transition time information can be obtained from the device IBIS model. IBIS models are recommended for system level simulations, as discrete
values for I/O transition times are not representative of the I/O pad behavior when connected to an actual transmission line load.
[5] I/O output current specifications are valid for the given reference load figure, and the constraints given in the Operating Conditions of this document.
[6] I/O timing specifications are valid for the un-terminated 50ohm transmission line reference load given in the figure below. A lumped 8pF load is assumed
in addition to a 5 inch microstrip trace on standard FR4 with approximately 3.3pF/inch. For signals with frequency greater than 63MHz, a maximum 2 inch
PCB trace is assumed. For best signal integrity, the series resistance in the transmission line should be matched closely to the selected output resistance
(ROUT_*) of the I/O pad.
[7] To determine total switching current on any I/O supply, current values per output pin should not be incrementally summed. I/O interfaces on the device are
asynchronous to each other, so not all switching occurs at the same instant. Actual use case must be considered.
Hysteresis spec does not apply to fast pad
MCU see note 1 PCB External
device
5 in.
(4 layer FR4)
CPCB = ~3.3 pF/inch * 5 inch
RDSON
RPCB = RDSON
RDSON CL = 8 pF
Notes:
1. See IBIS models for further details.
Figure 7. Reference Load Diagram

#### 7.2 GPIO DC electrical specifications, 5.0V (4.5V - 5.5V)

The leakage current on the GPIO pins is specified as a function of the pad type (Standard, Standard Plus, Medium, Fast, or GPI)
and the number of Analog functions (CMP and ADC channels) multiplexed per pin.
For other devices, the "Analog Function Count" is defined from the number of CMP and ADC channels multiplexed to a given
pin. This information can be obtained from the "Direct Signals" column in the IOMUX files attached to the Reference Manual. The
"Analog Function Count" is shown in the Condition column of the following table.
Table 25. GPIO DC electrical specifications, 5.0V (4.5V - 5.5V)
Symbol Description Min Typ Max Unit Condition Spec
Number
VIH Input high level DC 0.65 x — VDD_HV V VDD_HV_A/B = 5.0V —
voltage threshold VDD_HV _A/B +
_A/B 0.3
VIL Input low level DC VSS - 0.3 — 0.35 x V VDD_HV_A/B = 5.0V —
voltage threshold VDD_HV
_A/B
WFRST RESET Input filtered — — 33 ns — —
[1]
pulse width
WNFRST RESET Input 100 — — ns — —
not filtered pulse
[2]
width
ILKG_50_S0 5.0V input leakage -193 — 389 nA Pins with Analog —
current for Standard Function Count = 0
[3]
GPIO
Table continues on the next page...

---

*Page 37*

Table 25. GPIO DC electrical specifications, 5.0V (4.5V - 5.5V) ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
ILKG_50_S1 5.0V input leakage -691 — 580 nA Pins with Analog —
current for Standard Function Count = 1
[3]
GPIO
ILKG_50_S2 5.0V input leakage -947 — 673 nA Pins with Analog —
current for Standard Function Count = 2,
[3]
GPIO plus PTA12, PTD1
ILKG_50_S3 5.0V input leakage -1614 — 879 nA Pins with Analog —
current for Standard Function Count = 3,
[3]
GPIO plus PTD0
ILKG_50_SP0 5.0V input leakage -553 — 736 nA Pins with Analog —
current for Standard Function Count = 0
Plus GPIO and
[3]
RESET IO
ILKG_50_SP1 5.0V input leakage -855 — 846 nA Pins with Analog —
current for Standard Function Count = 1
Plus GPIO and
[3]
RESET IO
ILKG_50_SP2 5.0V input leakage -1389 — 1017 nA Pins with Analog —
current for Standard Function Count = 2
Plus GPIO and
[3]
RESET IO
ILKG_50_M0 5.0V input leakage -1036 — 951 nA Pins with Analog —
current for Medium Function Count = 0
[3]
GPIO
ILKG_50_M1 5.0V input leakage -1284 — 1057 nA Pins with Analog —
current for Medium Function Count = 1,
[3]
GPIO plus PTC16, PTD5
ILKG_50_M2 5.0V input leakage -1518 — 1298 nA Pins PTD6 and PTE8 —
current for Medium
[3]
GPIO
ILKG_50_I 5.0V input leakage -150 — 150 nA — —
[3]
current for GPI
VHYS_50 input hysteresis 0.06 x — — mV Always enabled, —
voltage VDD_HV Applies to MCXE315/
_A/B MCXE316/ MCXE317
and MCXE31B
devices.
CIN GPIO Input 2 4 6 pF add 2pF for package/ —
capacitance parasitic
IPU_50 5.0V GPIO pull up/ 20 — 55 kΩ pull up @ 0.3 * VDD_ —
down resistance HV_*, pull down @ 0.7
* VDD_HV_*
Table continues on the next page...

---

*Page 38*

Table 25. GPIO DC electrical specifications, 5.0V (4.5V - 5.5V) ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
IOH_50_S 5.0V output 1.6 — — mA VOH >= VDD_HV_A/B —
high current - 0.7V
[4] [5]
Standard GPIO
IOH_50_SP 5.0V output high 2.5 — — mA DSE = 0, VOH >= —
current Standard VDD_HV_A/B - 0.7V
Plus GPIO and
[4] [5]
RESET IO
IOH_50_M 5.0V output high 4.0 — — mA DSE = 0, VOH >= —
current for Medium VDD_HV_A/B - 0.7V
[4] [5]
GPIO
IOH_50_SP 5.0V output high 5.0 — — mA DSE = 1, VOH >= —
current for Standard VDD_HV_A/B - 0.7V
Plus GPIO and
[4] [5]
RESET IO
IOH_50_M 5.0V output high 8.0 — — mA DSE = 1, VOH >= —
current for Medium VDD_HV_A/B - 0.7V
[4] [5]
GPIO
IOL_50_S 5.0V output 1.6 — — mA VOL <= 0.7V —
low current for
[4] [5]
Standard GPIO
IOL_50_SP 5.0V output low 2.5 — — mA DSE =0, VOL <= 0.7V —
current for Standard
Plus GPIO and
[4] [5]
RESET IO
IOL_50_M 5.0V output low 4.0 — — mA DSE =0, VOL <= 0.7V —
current for Medium
[4] [5]
GPIO
IOL_50_SP 5.0V output low 5.0 — — mA DSE =1, VOL <= 0.7V —
current for Standard
Plus GPIO and
[4] [5]
RESET IO
IOL_50_M 5.0V output low 8.0 — — mA DSE =1, VOL <= 0.7V —
current for medium
[4] [5]
GPIO
FMAX_50_S 5.0V maximum — — 10 MHz 3.6V - 5.5V CL (max) = —
frequency for 25pF
[4] [6]
Standard GPIO
FMAX_50_SP 5.0V maximum — — 25 MHz 3.6V - 5.5V CL (max) = —
frequency for 25pF
Standard Plus
[4] [6]
GPIO
Table continues on the next page...

---

*Page 39*

Table 25. GPIO DC electrical specifications, 5.0V (4.5V - 5.5V) ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
FMAX_50_M 5.0V maximum — — 25 MHz 3.6V - 5.5V CL (max) = —
frequency for 25pF
[4] [6]
Medium GPIO
IOHT Output high current — — 100 mA — —
[7]
total for all ports
[1] Maximum length of RESET pulse will be filtered by an internal filter on this pin.
[2] Minimum length of RESET pulse, guaranteed not to be filtered by the internal filter.
[3] A positive value is leakage flowing into pin with pin at VDD_HV_A/B (the GPIO supply level); a negative value is leakage flowing out the pin with the pin at
ground.
[4] GPIO output transition time information can be obtained from the device IBIS model. IBIS models are recommended for system level simulations, as discrete
values for I/O transition times are not representative of the I/O pad behavior when connected to an actual transmission line load.
[5] I/O output current specifications are valid for the given reference load figure, and the constraints given in the Operating Conditions of this document.
[6] I/O timing specifications are valid for the un-terminated 50ohm transmission line reference load given in the figure below. A lumped 8pF load is assumed in
addition to a 5 inch microstrip trace on standard FR4 with approximately 3.3pF/inch.. For best signal integrity, the series resistance in the transmission line
should be matched closely to the selected output resistance (ROUT_*) of the I/O pad.
[7] To determine total switching current on any I/O supply, current values per output pin should not be incrementally summed. I/O interfaces on the device are
asynchronous to each other, so not all switching occurs at the same instant. Actual use case must be considered.
Hysteresis spec does not apply to fast pad
MCU see note 1 PCB External
device
5 in.
(4 layer FR4)
CPCB = ~3.3 pF/inch * 5 inch
RDSON
RPCB = RDSON
RDSON CL = 8 pF
Notes:
1. See IBIS models for further details.
Figure 8. Reference Load Diagram

#### 7.3 5.0V (4.5V - 5.5V) GPIO Output AC Specification

Table 26. 5.0V (4.5V - 5.5V) GPIO Output AC Specification
Symbol Description Min Typ Max Unit Condition Spec
Number
TR_TF_50_S 5.0V Standard GPIO 5 — 21 ns CL (max) = 25pF —
rise/fall time
TR_TF_50_S 5.0V Standard GPIO 8.5 — 31 ns CL (max) = 50pF —
rise/fall time
TR_TF_50_SP 5.0V Standard Plus 3 — 13.2 ns DSE=0 CL (max) = —
GPIO rise/fall time 25pF
TR_TF_50_SP 5.0V Standard 1 — 7.1 ns DSE=1 CL (max) = —
Plus GPIO rise/fall 25pF
time
Table continues on the next page...

---

*Page 40*

Table 26. 5.0V (4.5V - 5.5V) GPIO Output AC Specification ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
TR_TF_50_SP 5.0V Standard Plus 6.4 — 18.8 ns DSE=0 CL (max) = —
GPIO rise/fall time 50pF
TR_TF_50_SP 5.0V Standard Plus 3.4 — 11 ns DSE=1 CL (max) —
GPIO rise/fall time =50pF
TR_TF_50_M 5.0V Medium GPIO 1.8 — 8.2 ns DSE=0, SRE=0 CL —
rise/fall time (max) = 25pF
TR_TF_50_M 5.0V Medium GPIO 2.5 — 9.8 ns DSE=0, SRE=1 CL —
rise/fall time (max) = 25pF
TR_TF_50_M 5.0V Medium GPIO 0.7 — 4.5 ns DSE=1, SRE=0 CL —
rise/fall time (max) = 25pF
TR_TF_50_M 5.0V Medium GPIO 1.8 — 7.2 ns DSE=1, SRE=1 CL —
rise/fall time (max) = 25pF
TR_TF_50_M 5.0V Medium GPIO 3.95 — 13.2 ns DSE=0, SRE=0 CL —
rise/fall time (max) = 50pF
TR_TF_50_M 5.0V Medium GPIO 4.3 — 13.8 ns DSE=0, SRE=1 CL —
rise/fall time (max) = 50pF
TR_TF_50_M 5.0V Medium GPIO 1.6 — 7.1 ns DSE=1, SRE=0 CL —
rise/fall time (max) = 50pF
TR_TF_50_M 5.0V Medium GPIO 2.7 — 9.6 ns DSE=1, SRE=1 CL —
rise/fall time (max) = 50pF

#### 7.4 3.3V (2.97V - 3.63V) GPIO Output AC Specification

Table 27. 3.3V (2.97V - 3.63V) GPIO Output AC Specification
Symbol Description Min Typ Max Unit Condition Spec
Number
TR_TF_33_S 3.3V Standard GPIO 5 — 28 ns CL (max) = 25pF —
rise/fall time
TR_TF_33_S 3.3V Standard GPIO 9.5 — 43 ns CL (max) = 50pF —
rise/fall time
TR_TF_33_SP 3.3V Standard Plus 4 — 17.5 ns DSE=0 CL (max) = —
GPIO rise/fall time 25pF
TR_TF_33_SP 3.3V Standard Plus 1.9 — 10 ns DSE=1 CL (max) = —
GPIO rise/fall time 25pF
TR_TF_33_SP 3.3V Standard Plus 7.5 — 27 ns DSE=0 CL (max) = —
GPIO rise/fall time 50pF
TR_TF_33_SP 3.3V Standard Plus 3.5 — 15 ns DSE=1 CL (max) = —
GPIO rise/fall time 50pF
Table continues on the next page...

---

*Page 41*

Table 27. 3.3V (2.97V - 3.63V) GPIO Output AC Specification ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
TR_TF_33_M 3.3V Medium GPIO 2.2 — 12.3 ns DSE=0, SRE=0 CL —
rise/fall time (max) = 25pF
TR_TF_33_M 3.3V Medium GPIO 3.0 — 14 ns DSE=0, SRE=1 CL —
rise/fall time (max) = 25pF
TR_TF_33_M 3.3V Medium 0.8 — 6.6 ns DSE=1, SRE=0 CL —
GPIO rise/fall time (max) = 25pF
TR_TF_33_M 3.3V Medium GPIO 2.4 — 10.5 ns DSE=1, SRE=1 CL —
rise/fall time (max) = 25pF
TR_TF_33_M 3.3V Medium GPIO 4.5 — 17.3 ns DSE=0, SRE=0 CL —
rise/fall time (max) = 50pF
TR_TF_33_M 3.3V Medium GPIO 5 — 19.8 ns DSE=0, SRE=1 CL —
rise/fall time (max) = 50pF
TR_TF_33_M 3.3V Medium GPIO 2.2 — 10 ns DSE=1, SRE=0 CL —
rise/fall time (max) = 50pF
TR_TF_33_M 3.3V Medium GPIO 3.6 — 13.9 ns DSE=1, SRE=1 CL —
rise/fall time (max) = 50pF

### 8 Glitch Filter

The glitch filter parameters in the following table apply to the filters of WKPU pins and TRGMUX inputs 60-63.
Table 28. Glitch Filter
Symbol Description Min Typ Max Unit Condition Spec
Number
TFILT Glitch filter max — — 20 ns — —
filtered pulse
[1] [2] [3]
width
TUNFILT Glitch filter min 400 — — ns — —
unfiltered pulse
[1] [3] [4]
width
[1] Pulses in between the max filtered and min unfiltered may or may not be passed through.
[2] Pulses shorter than defined by the maximum value are guaranteed to be filtered (not passed).
[3] An input signal pulse is defined by the duration between the input signal's crossing of a Vil/Vih threshold voltage level, and the next crossing of the opposite
level.
[4] Pulses larger than defined by the minimum value are guaranteed to not be filtered (passed).

### 9 Flash memory specification

---

*Page 42*

#### 9.1 Flash memory program and erase specifications

Table 29. Flash memory program and erase specifications
[1] [2]
Symbol Characteristic Typ Factory Field Update Unit
[3] [4]
Programming ,
[6]
Initial Max Initial Max, Typical Lifetime Max
Full Temp End of
[5]
Life
20°C ≤T A -40°C ≤T J -40°C ≤T J ≤ 1,000 ≤ 100,000
≤30°C ≤135°C ≤135°C cycles cycles
102 122 129 150
t dwpgm Doubleword (64 bits) program time 111 μs
142 171 180 157
t ppgm Page (256 bits) program time 200 μs
314 377 341
t qppgm Quad-page (1024 bits) 396 450 μs
program time
20 24 22
t 8kpgm 8 KB Sector program time 26 30 ms
8.5 10.6 6.5 30
t 8kers 8 KB Sector erase time 4.8 ms
22.8 27.4 24.4 40
t 256kbers 256KB Block erase time 28.8 — ms
t 512kbers 25.4 30.5 27.9 45
512KB Block erase time 32.1 — ms
30.6 36.8 33.6 50
t 1mbers 1MB Block erase time 38.7 — ms
2MB Block erase time 41.1 49.3 51.8 45.2 60 —
t 2mbers ms
[1] Program times are actual hardware programming times and do not include software overhead. Sector program times assume quad-page programming.
[2] Typical program and erase times represent the median performance and assume nominal supply values and operation at 25 °C. Typical program and erase
times may be used for throughput calculations.
[3] Conditions: ≤ 25 cycles, nominal voltage.
[4] Plant Programing times provide guidance for timeout limits used in the factory.
[5] Typical End of Life program and erase times represent the median performance and assume nominal supply values. Typical End of Life program and erase
values may be used for throughput calculations.
[6] Conditions: -40°C ≤T J ≤135°C, full spec voltage.

#### 9.2 Flash memory Array Integrity and Margin Read specifications

Table 30. Flash memory Array Integrity and Margin Read specifications
[1] [2] [3]
Symbol Characteristic Min Typical Max Units
t ai256kseq Array Integrity time and Margin — — 8192 x Tperiod x Nread —
Read time for sequential sequence
(plus 40uS adder required if User
on 256KB block.
Margin Read)
t ai512kseq Array Integrity time and Margin — — 16384 x Tperiod x Nread —
Read time for sequential sequence
(plus 40uS adder required if User
on 512KB block.
Margin Read)
Table continues on the next page...

---

*Page 43*

Table 30. Flash memory Array Integrity and Margin Read specifications ...continued
[1] [2] [3]
Symbol Characteristic Min Typical Max Units
t ai1mseq Array Integrity time and Margin — — 32768 x Tperiod x Nread —
Read time for sequential sequence
(plus 40uS adder required if User
on 1MB block.
Margin Read)
— — —
t ai2mseq Array Integrity time and Margin 65536 x Tperiod x Nread
Read time for sequential sequence
(plus 40uS adder required if User
on 2MB block.
Margin Read)
106496
t ai256kprop Array Integrity time for proprietary — — —
sequence on 256KB block.
x Tperiod x Nread
Array Integrity time for proprietary 229376 —
t ai512kprop — —
sequence on 512KB block.
x Tperiod x Nread
491520
t ai1mprop Array Integrity time for proprietary — — —
sequence on 1MB block.
x Tperiod x Nread
— — 1048576 —
t ai2mprop Array Integrity time for proprietary
sequence on 2MB block.
x Tperiod x Nread
[1] Array Integrity times need to be calculated and is dependent on system frequency and number of clocks per read. The equation presented require Tperiod
(which is the unit accurate period, thus for 200 MHz, Tperiod would equal 5e-9) and Nread (which is the number of clocks required for read, including single
read, dual read, quad read contribution. Thus for a read setup that requires 6 clocks to read Nread would equal 6.
[2] Array Integrity times are actual hardware execution times and do not include software overhead or system code execution overhead.
[3] The units for Array Integrity are determined by the period of the system clock. If unit accurate period is used in the equation, the results of the equation are
also unit accurate.

#### 9.3 Flash memory module life specifications

Table 31. Flash memory module life specifications
Symbol Characteristic Conditions Min Typical Units
Array P/E Number of program/erase cycles per block — 100,000 — P/E
cycles for 256 KB and 512 KB blocks using cycles
Sector Erase.
Number of program/erase cycles per block for — 1,000 — P/E
1 MB and 2 MB blocks using Sector Erase. cycles
Number of program/erase cycles per block — 25 — P/E
[1]
using Block Erase cycles
Data Minimum data retention. Blocks with 0 - 1,000 20 — Years
retention P/E cycles.
Blocks with 100,000 10 — Years
P/E cycles.
[1] Program and erase supported for factory conditions. Nominal supply values and operation at 25°C.

---

*Page 44*

#### 9.3.1 Data retention vs program/erase cycles

Graphically, Data Retention versus Program/Erase Cycles can be represented by the following figure.
The spec window represents qualified limits.
25
20
15
10
5
Minimum Data Retention Life (Years)
0
1 10 100 1000 10000 100000 1000000
P/E Cycles (Sector Erases)
Figure 9. Data retention vs program/erase cycles

#### 9.4 Flash memory AC timing specifications

Table 32. Flash memory AC timing specifications
Symbol Characteristic Min Typical Max Units
t done Time from 0 to 1 transition on the MCR[EHV] bit — — 5 ns
initiating a program/erase until the MCR[DONE]
bit is cleared.
22 plus four
t dones Time from 1 to 0 transition on the MCR[EHV] bit 5 plus four — μs
system clock
aborting a program/erase until the MCR[DONE] system clock
[1]
periods
bit is set to a 1. periods
t drcv Time to recover once exiting low power mode. 14 plus seven 17.5 plus 21 plus seven μs
system clock seven system system clock
[2]
periods clock periods periods
t aistart Time from 0 to 1 transition of UT0[AIE] initiating a — — 5 ns
Margin Read or Array Integrity until the UT0[AID]
bit is cleared. This time also applies to the
resuming from a suspend or breakpoint by
clearing UT0[AISUS] or clearing UT0[NAIBP]
50
t aistop Time from 1 to 0 transition of UT0[AIE] initiating — — ns
an Array Integrity abort until the UT0[AID] bit is
system clock
set. This time also applies to the UT0[AISUS] to
periods
UT0[AID] setting in the event of a Array Integrity
suspend request.
Table continues on the next page...

---

*Page 45*

Table 32. Flash memory AC timing specifications ...continued
Symbol Characteristic Min Typical Max Units
—
t mrstop Time from 1 to 0 transition of UT0[AIE] initiating — 26 μs
a Margin Read abort until the UT0[AID] bit is
plus fifteen
set. This time also applies to the UT0[AISUS] to
system clock
UT0[AID] setting in the event of a Margin Read
periods
suspend request.
[1] For Block Erase, Tdones times may be 3x max spec.
[2] In extreme cases (1 block configurations) Tdrcv min may be faster (12uS plus seven system clocks)

#### 9.5 Flash memory read timing parameters

Table 33. Flash Read Wait State Settings MCXE315/MCXE316/MCXE317/MCXE31B
Flash Frequency
RWSC setting
250 KHz < Freq ≤ 66 MHz 1
2
66 MHz < Freq ≤ 100 MHz
100 MHz < Freq ≤ 133 MHz 3
133 MHz < Freq ≤ 167 MHz 4

### 10 Analog modules

#### 10.1 SAR_ADC

All below specs are applicable only when one ADC instance is in operation and averaging is used or multiple ADC instances are
operational at the same time but sampling different channels. Best performance can be achieved if only one ADC is operational
at a time sampling one channel
Table 34. SAR_ADC
Symbol Description Min Typ Max Unit Condition
[1]
VDD_HV_A ADC Supply Voltage 2.97 — 5.5 V —
DVREFL VSS / VREFL Voltage -100 — 100 mV —
[2]
Difference
[3]
VAD_INPUT ADC Input Voltage VREFL — VREFH V —
fAD_CK ADC Clock Frequency 10 — 120 MHz —
(MCXE315/MCXE316
and MCXE317)
fAD_CK ADC Clock Frequency 10 — 80 MHz —
(MCXE31B
tSAMPLE ADC Input Sampling 275 — — ns —
Time
tCONV ADC Total Conversion 1 — — us 12-bit result
Time
Table continues on the next page...

---

*Page 46*

Table 34. SAR_ADC ...continued
Symbol Description Min Typ Max Unit Condition
tCONV ADC Total Conversion 0.9 — — us 10-bit result
Time
CAD_INPUT ADC Input — — 13.8 pF ADC component plus pad
Capacitance capacitance (~2pF)
RAD_INPUT ADC Input Resistance — — 4.6 KΩ ADC + mux+SOC routing
RS Source Impedance, — 20 — Ω —
precision channels
RS Source Impedance, — 20 — Ω —
standard channels
TUE ADC Total Unadjusted — +/-4 +/-6 LSB without adjacent pin
[4] [5]
Error current injection
TUE ADC Total Unadjusted — +/-4 +/-8 LSB with up to +/-3mA
[5]
Error of current injection on
adjacent pins
IAD_REF Current Consumption — — 200 uA Per ADC for dedicated or
on ADC Reference pin, shared reference pins
VREFH.
IDDA Current Consumption — 2.1 — mA Current consumption per
on ADC Supply, ADC module, ADC
VDD_HV_A enabled and converting
CS Sampling Capacitance 6.4 7.36 8.32 pF all channels
(gain=0) (gain=0) (gain=0)
9.72 11.12 12.52
pF(gain=m pF(gain=m (gain=max
ax) ax) )
RAD Sampling Switch 80 170 520 Ohm all channels
Impedance
CP1 Pin capacitance 1.42 — 5.30 pF all channels
CP1 Pin capacitance 1.42 — 4.38 pF Precision channels
CP1 Pin capacitance 1.61 — 5.30 pF Standard channels
CP2 Analog Bus 0.32 — 4.18 pF all channels
Capacitance
CP2 Analog Bus 0.32 — 1.42 pF Precision channels
Capacitance
CP2 Analog Bus 0.497 — 4.18 pF Standard channels
Capacitance
RSW1 Channel selection 65.9 — 1410 Ohm all channels
Switch impedance
RSW1 Channel selection 65.9 — 712 Ohm Precision channels
Switch impedance
Table continues on the next page...

---

*Page 47*

Table 34. SAR_ADC ...continued
Symbol Description Min Typ Max Unit Condition
RSW1 Channel selection 65.9 — 1410 Ohm Standard channels
Switch impedance
[1] Appropriate decoupling capacitors to be used to filter noise on the supplies. See application note AN5032 for reference supply design for SAR_ADC.
[2] VSS and VREFL should be shorted on PCB. 100mV difference between VSS and VREFL is for transient only (not for DC).
[3] This is ADC Input range for ADC accuracy guaranteed in this input range only. For SoC Pin capability, see Operation Condition Section.
[4] Spec valid if potential difference between VDD_HV_A and VREFH should follow VDD_HV_A +0.1V >=VREFH >= VDD_HV_A -1.5V
[5] TUE spec for precision and standard channels is based on 12-bit level resolution.
EXTERNAL CIRCUIT INTERNAL CIRCUIT SCHEME
VDD_HV_A
Channel
Selection Sampling
Source Filter Current Limiter
RS RF RL RSW1 RAD
VA CF CP1 CP2 CS
R S Source Impedance
R F Filter Resistance
C F Filter Capacitance
R L Current Limiter Resistance
R SW1 Channel Selection Switch Impedance
R AD Sampling Switch Impedance
C P Pin Capacitance (two contributions: C P1 , C P2 )
C S Sampling Capacitance
Figure 10. SAR_ADC Input Circuit

#### 10.2 Supply Diagnosis

The table below gives the specification for the on die supply diagnosis.
Table 35. Supply Diagnosis
Symbol Description Min Typ Max Unit Condition Spec
Number
AN_ACC Offset to internally -5 0 5 % — —
monitored supply at
[1] [2] [3]
ADC input
AN_T_on Switching time from — 2.5 12 ns — —
closed (OFF) to
[1]
conducting (ON)
AN_TADCSA Required ADC 1.2 — — μs — —
[2]
sampling time
[1] These specs will have degraded performance when used in extended supply voltage operation range, i.e. normal supply voltage range specification is
exceeded.
[2] Required ADC sampling time specified by parameter AN_TADCSA needs to be used at the ADC conversion to guarantee the specified accuracy. A
smaller sampling time leads to a less accurate result.
[3] If V15 > VDD_HV_A +100mV then the V15 measurement via anamux may be imprecise.

---

*Page 48*

#### 10.3 Low Power Comparator (LPCMP)

Table 36. Low Power Comparator (LPCMP)
Symbol Description Min Typ Max Unit Condition Spec
Number
idda(IDHSS) vdda Supply — 240 — uA — —
Current, High Speed
[1] [2]
Mode
idda(IDLSS) vdda Supply — 17 — uA — —
Current, Low Speed
[1] [2]
Mode
idda(IDHSS) vdda Supply — 10 — uA — —
Current, high speed
[2]
mode, DAC only
idda_lkg vdda Supply — 2 — nA vdda=5.5V, T=25C —
Current, module
[2]
disabled
TDHSB Propagation Delay, — — 200 ns — —
[3]
High Speed Mode
TDLSB Propagation Delay, — — 2 us — —
[3]
Low Speed mode
TDHSS Propagation Delay, — — 400 ns — —
[4]
High Speed Mode
TDLSS Propagation Delay, — — 5 us — —
[4]
Low Speed mode
TIDHS Initialization Delay, — — 3 us — —
[5]
High Speed Mode
TIDLS Initialization Delay, — — 30 us — —
[5]
Low Speed mode
VAIO Analog Input Offset -25 +/-1 25 mV — —
Voltage, High Speed
Mode
VAIO Analog Input Offset -40 + /- 5 40 mV — —
Voltage, Low Speed
mode
VAHYST0 Analog Comparator — 0 — mV HYSTCTR[1:0]= 2'b00 —
Hysteresis, High
Speed Mode
VAHYST1 Analog Comparator — 14 41 mV HYSTCTR[1:0]= 2'b01 —
Hysteresis, High
Speed Mode
VAHYST2 Analog Comparator — 27 76 mV HYSTCTR[1:0]= 2'b10 —
Hysteresis, High
Speed Mode
Table continues on the next page...

---

*Page 49*

Table 36. Low Power Comparator (LPCMP) ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
VAHYST3 Analog Comparator — 40 111 mV HYSTCTR[1:0]= 2'b11 —
Hysteresis, High
Speed Mode
VAHYST0 Analog Comparator — 0 — mV HYSTCTR[1:0]= 2'b00 —
Hysteresis, Low
Speed mode
VAHYST1 Analog Comparator — 8 60 mV HYSTCTR[1:0]= 2'b01 —
Hysteresis, Low
Speed mode
VAHYST2 Analog Comparator — 15 113 mV HYSTCTR[1:0]= 2'b10 —
Hysteresis, Low
Speed mode
VAHYST3 Analog Comparator — 23 165 mV HYSTCTR[1:0]= 2'b11 —
Hysteresis, Low
Speed mode
INL DAC integral -1 — 1 LSB vrefh_cmp = vdda, —
[2] [6] [7]
linearity vrefl_cmp = vss
INL DAC integral -1.5 — 1.5 LSB vrefh_cmp < vdda —
[2] [6] [7]
linearity
DNL DAC differential -1 — 1 LSB vrefh_cmp = vdda, —
[2] [6]
linearity vrefl_cmp = vss
DNL DAC differential -1.5 — 1.5 LSB vrefh_cmp < vdda —
[2] [6]
linearity
tDDAC DAC — — 30 us — —
Initialization time
VAIN Analog input voltage 0 — VDDA V — —
[1] Difference at input > 200mV
[2] vdda is comparator HV supply and internally shorted to VDD_HV_A pin. vss is comparator ground
[3] Applied +/- (100 mV + VAHYST0/1/2/3 + max. of VAIO) around switch point
[4] Applied +/- (30 mV + VAHYST0/1/2/3 + max. of VAIO) around switch point
[5] Applied ± (100 mV + VAHYST0/1/2/3 ).
[6] 1 LSB = (vrefh_cmp - vrefl_cmp) /256. vrefh_cmp and vrefl_cmp are comparator reference high and low
[7] Calculation method used: Linear Regression Least Square Method
For Comparator IN signals adjacent to VDD_HV_A/VDD_HV_B/VSS or XTAL/EXTAL or switching pins cross coupling may
happen and hence hysteresis settings can be used to obtain the desired Comparator performance. Additionally an external
capacitor to ground (1nF) should be used to filter noise on input signal. Also source drive should not be weak (Signal with <50K
pull up/down is recommended).
For devices where the VDD_HV_B domain is present, LPCMP0 channels must only be selected/enabled when VDD_HV_A >=
VDD_HV_B. These channels must be disabled when VDD_HV_A goes below VDD_HV_B.

---

*Page 50*

90
Hysteresis
(mV)
60
30
0
0 0.3 0.6 0.9 1.2 1.5 1.8 2.1 2.4 2.7 3.0 3.3 VAIN (V)
25 Junction Temp (°C)
3.3 VDD_HV_A (V)
HYSTCTR setting
00 01 10 11
Figure 11. Typical Hysteresis vs VAIN (VDD_HV_A = 3.3 V, High Speed Mode)
60
Hysteresis
(mV)
40
20
0
0 0.3 0.6 0.9 1.2 1.5 1.8 2.1 2.4 2.7 3.0 3.3 VAIN (V)
25 Junction Temp (°C)
3.3 VDD_HV_A (V)
HYSTCTR setting
00 01 10 11
Figure 12. Typical Hysteresis vs VAIN (VDD_HV_A = 3.3 V, Low Speed Mode)
90
Hysteresis
(mV)
60
30
0
0 0.5 1.0 1.5 2.0 2.5 3.0 3.5 4.0 4.5 5.0 VAIN (V)
25 Junction Temp (°C)
5 VDD_HV_A (V)
HYSTCTR setting
00 01 10 11
Figure 13. Typical Hysteresis vs VAIN (VDD_HV_A = 5 V, High Speed Mode).png

---

*Page 51*

60
Hysteresis
(mV)
40
20
0
0 0.5 1.0 1.5 2.0 2.5 3.0 3.5 4.0 4.5 5.0 VAIN (V)
25 Junction Temp (°C)
5 VDD_HV_A (V)
HYSTCTR setting
00 01 10 11
Figure 14. Typical Hysteresis vs VAIN (VDD_HV_A = 5 V, Low Speed Mode).png

#### 10.4 Temperature Sensor

The table below gives the specification for the MCU on-die temperature sensor.
Table 37. Temperature Sensor
Symbol Description Min Typ Max Unit Condition Spec
Number
TS_TJ Junction -40 — 135 °C — —
temperature
monitoring range
TS_IV25 ON state current — 400 — μA ETS_EN=1 —
consumption on V25
TS_ACC1 Temperature output -5 0 +5 °C 100 °C < Tj <= 135 °C —
error at circuit output
[1] [2] [3]
(Voltage)
TS_ACC2 Temperature output -10 0 +10 °C -40 °C <= Tj <=100 °C —
error at circuit output
[1] [2] [3]
(Voltage)
TS_TSTART Circuit start up time — 4 30 μs — —
TS_TADCSA Required ADC 1.2 — — μs — —
[1]
sampling time
[1] Required ADC sampling time specified by parameter TS_TADCSA needs to be used at the ADC conversion to guarantee the specified accuracy. A
smaller sampling time leads to a less accurate result.
[2] Note: The temperature sensor measures the junction temperature Tj at the location where it is placed on die. The local Tj is modulated by current and
previous active state of the circuit elements on die.
[3] The error caused by ADC conversion and provided temperature calculation formula is not included.

---

*Page 52*

### 11 Clocking modules

#### 11.1 FIRC

Table 38. FIRC
Symbol Description Min Typ Max Unit Condition Spec
Number
fFIRC FIRC nominal — 48 — MHz — —
Frequency
FACC FIRC Frequency -5 — 5 % — —
deviation across
process, voltage,
and temperature
after trimming
[1]
TSTART Startup Time — 10 25 us — —
[1] Startup time is for reference only.

#### 11.2 SIRC

Table 39. SIRC
Symbol Description Min Typ Max Unit Condition Spec
Number
fSIRC SIRC nominal — 32 — KHz — —
Frequency
fSIRC_ACC SIRC Frequency -10 — 10 % — —
deviation across
process, voltage,
and temperature
after trimming
TSIRC_start SIRC Startup — — 3 ms — —
[1]
Time
TSIRC_DC SIRC duty cycle 30 — 70 % — —
[1] Startup time is for information only.

#### 11.3 PLL

Jitter values specified in this table are applicable for FXOSC reference clock input only.
Table 40. PLL
Symbol Description Min Typ Max Unit Condition Spec
Number
FPLL_in PLL input frequency 8 — 40 MHz This is the frequency —
after the Reference
Divider within the PLL
Table continues on the next page...

---

*Page 53*

Table 40. PLL ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
FPLL_out PLL output 25 — 320 MHz — —
frequency
(PLL_PHIn_CLK)
FPLL_vcoRange VCO Frequency 640 — 1280 MHz — —
range
FPLL_DS Modulation Depth -0.5 — -3 % — —
(down spread)
FPLL_FM Modulation — — 32 KHz — —
frequency
TPLL_start PLL lock time — — 1 ms — —
JPLL_cyc PLL period jitter (pk- — — 353 ps FPLL_out = 120MHz, —
[1] [2] [3]
pk) Integer Mode
JPLL_cyc PLL period jitter (pk- — — 853 ps FPLL_out = 120MHz, —
[1] [2] [3]
pk) Fractional Mode
JPLL_acc PLL accumulated — — 840 ps FPLL_out = 120MHz, —
[1] [2] [3]
jitter (pk-pk) Integer Mode
JPLL_acc PLL accumulated — — 1680 ps FPLL_out = 120MHz, —
[1] [2] [3]
jitter (pk-pk) Fractional Mode
[1] For SSCG, jitter due to systematic modulation needs to be added as per applied modulation. Accumulated jitter specification is not valid with SSCG
[2] Jitter numbers calculated by extrapolating RMS jitter numbers to +/- 7 sigma .
[3] Jitter numbers are valid only at IP boundary and does not include any degradation due to IO pad for clock measurement.

#### 11.4 FXOSC

Table 41. FXOSC
Symbol Description Min Typ Max Unit Condition Spec
Number
FREQ_BYPASS Input clock — — 50 MHz — —
frequency in bypass
[1]
mode
TRF_BYPASS Input clock rise/fall — — 5 ns — —
time in bypass
[1]
mode
CLKIN_DUTY_ Input clock duty 47.5 — 52.5 % — —
BYPASS cycle in bypass
[1]
mode
FXOSC_CLK output clock 8 — 40 MHz — —
frequency in crystal
mode
Table continues on the next page...

---

*Page 54*

Table 41. FXOSC ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
TFXOSC Fxosc start — — 2 ms — —
up time (ALC
[2]
enabled)
IFXOSC Oscillator Analog — — 1 mA using 8, 16 or 40 MHz —
circuit supply crystal
current, V25 supply
(ALC enable)
IFXOSC Oscillator Analog — — 2.7 mA using 8, 16 or 40 MHz —
circuit supply crystal
current, V25 supply
(ALC disabled)
EXTAL_SWING_ Peak-to-peak 0.3 — 1.4 V — —
PP voltage swing on
EXTAL pin in crystal
oscillator mode (ALC
enabled)
EXTAL_SWING_ Peak-to-peak 1.2 — 2.75 V — —
PP voltage swing on
EXTAL pin in crystal
oscillator mode (ALC
[3]
disabled)
CLKIN_VIL_ Input clock low level 0 — vref-1 V vref=0.5*VDD_HV_A —
[4]
EXTAL_BYPASS in bypass mode
CLKIN_VIH_ Input clock high level vref+1 — VDD_HV V vref=0.5*VDD_HV_A —
[4]
EXTAL_BYPASS in bypass mode _A
VSB Self Bias Voltage 350 — 850 mV — —
GM Amplifier 9.7 14.04 18.5 mA/V GM_SEL[3:0] = —
Transconductance 4`b1111
[1] For bypass mode applications, the EXTAL pin should be driven low when FXOSC is in off/disabled state.
[2] The startup time specification is valid only when the recommended crystal and load capacitors are used. For higher load capacitances, the actual startup time
might be higher.
[3] The recommended gm setting to ensure extal swing < 2.75V at 8MHz in ALC-disabled mode is gm=4'b0010. Recommended gm settings in ALC-disabled
mode for all other supported frequencies and crystals remain the same.
[4] For bypass mode applications, the EXTAL pin should be driven symmetrical around Vref =0.5* VDD_HV_A
To ensure stable oscillations, FXOSC incorporates the feedback resistance internally.
In single ended bypass mode, the XTAL pin can be left unconnected.
Drive level is a crystal specification and if crystal load capacitance is increased beyond the recommended value, it may violate
the crystal drive level rating. In such cases, contact NXP sales representative for selecting the correct crystal.

---

*Page 55*

Crystal oscillator circuit provides stable oscillations when gmXOSC > 5 * gm_crit. The gm_crit is defined as:
2 2
gm_crit = 4 * (ESR + RS) * (2πF) * (C0 + CL)
where:
• gmXOSC is the transconductance of the internal oscillator circuit
• ESR is the equivalent series resistance of the external crystal
• RS is the series resistance connected between XTAL pin and external crystal for current limitation
• F is the external crystal oscillation frequency
• C0 is the shunt capacitance of the external crystal
• CL is the external crystal total load capacitance. CL = Cs+ [C1*C2/(C1+C2)]
• Cs is stray or parasitic capacitance on the pin due to any PCB traces
• C1, C2 external load capacitances on EXTAL and XTAL pins
See manufacture datasheet for external crystal component values
Figure 15. Oscillation build-up equation
Note: To improve the FXOSC jitter & duty cycle performance and the functionality of the pin next to the Oscillator (namely, PTE14
in 172-HDQFP and PTE3 in 100-HDQFP package) must be limited to static GPIO operation.
Figure 16. Block diagram

---

*Page 56*

#### 11.5 SXOSC

Table 42. SXOSC
Symbol Description Min Typ Max Unit Condition Spec
Number
Fsxosc Oscillator Crystal — 32.768 — KHz IP in crystal mode —
[1]
Frequency
Tstart SXOSC startup time — — 2 s start up time is —
dependent upon board
and crystal model.
ISXOSC Oscillator Analog — 2.1 10 uA — —
circuit supply current
gm_sxocs NMOS Amplifier 3 — 40 u A/V — —
Transconductance
[1] Supports single frequency

### 12 Communication interfaces

#### 12.1 LPSPI

The Low Power Serial Peripheral Interface (LPSPI) provides a synchronous serial bus with master and slave operations. Many
of the transfer attributes are programmable. The following table provides timing characteristics for classic LPSPI timing modes.
1. All timing is shown with respect to 50% VDD_HV_A/B thresholds.
2. All measurements are with maximum output load of 30pF input transition of 1 ns and pad configured DSE = 1, SRC = 0.
Table 43. LPSPI
Symbol Description Min Typ Max Unit Condition Spec
Number
fperiph Peripheral Frequenc — — 40 MHz Master —
[1] [2] [3]
y
fperiph Peripheral Frequenc — — 40 MHz Slave —
[1] [2] [3]
y
fperiph Peripheral Frequenc — — 80 MHz Master Loopback —
[2] [3] [4]
y
fop Operating frequency — — 15 MHz Slave 1
fop Operating frequency — — 15 MHz Master 1
fop Operating — — 10 MHz Slave_10Mbps 1
[5]
frequency
fop Operating — — 10 MHz Master_10Mbps 1
[5]
frequency
tSPSCK SPSCK period 66 — — ns Slave 2
tSPSCK SPSCK period 66 — — ns Master 2
[4]
tSPSCK SPSCK period 50 — — ns Master Loopback 2
Table continues on the next page...

---

*Page 57*

Table 43. LPSPI ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
tSPSCK SPSCK period 100 — — ns Master_10Mbps 2
tSPSCK SPSCK period 100 — — ns Slave_10Mbps 2
tLEAD Enable lead time tSPCK/2 — — ns Slave 3
(PCS to SPSCK
[6]
delay)
tLEAD Enable lead time 30 — — ns Master 3
(PCS to SPSCK
[6]
delay)
tLEAD Enable lead time 30 — — ns Master Loopback 3
(PCS to SPSCK
[4] [6]
delay)
tLAG Enable lag time tSPCK/2 — — ns Slave 4
(After SPSCK
[7]
delay)
tLAG Enable lag time 30 — — ns Master 4
(After SPSCK
[7]
delay)
tLAG Enable lag time 30 — — ns Master Loopback 4
(After SPSCK
[4] [7]
delay)
tWSPCK Clock (SPSCK) time tSPSCK/ — tSPSCK/ ns Slave 5
(SPSCK duty 2 - 3 2 + 3
[8]
cycle)
tWSPCK Clock (SPSCK) time tSPSCK/ — tSPSCK/ ns Master 5
(SPSCK duty 2 - 3 2 + 3
[8]
cycle)
tWSPCK Clock (SPSCK) time tSPSCK/ — tSPSCK/ ns Master Loopback 5
(SPSCK duty 2 - 3 2 + 3
[4] [8]
cycle)
tSU Data setup 6 — — ns Slave 6
time(inputs)
tSU Data setup 25 — — ns Master 6
time(inputs)
tSU Data setup 5 — — ns Slave_10Mbps 6
time(inputs)
tSU Data setup 36 — — ns Master_10Mbps 6
time(inputs)
tSU Data setup 6 — — ns Master_Loopback 6
[4]
time(inputs)
Table continues on the next page...

---

*Page 58*

Table 43. LPSPI ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
tHI Data hold 3 — — ns Slave 7
time(inputs)
tHI Data hold 0 — — ns Master 7
time(inputs)
tHI Data hold 4 — — ns Slave_10Mbps 7
time(inputs)
tHI Data hold 0 — — ns Master_10Mbps 7
time(inputs)
tHI Data hold 3 — — ns Master Loopback 7
[4]
time(inputs)
tA MISO valid time after — — 50 ns Slave 8
SS assertion
tDIS Slave MISO (SOUT) — — 50 ns Slave 9
disable time
tV Data valid (after — — 26 ns Slave 10
[9]
SPSCK edge)
tV Data valid (after — — 14 ns Master 10
[9]
SPSCK edge)
tV Data valid (after — — 36 ns Slave_10Mbps 10
[9]
SPSCK edge)
tV Data valid (after — — 21 ns Master_10Mbps 10
[9]
SPSCK edge)
tV Data valid (after — — 17.5 ns Master Loopback, 10
[4] [9]
SPSCK edge) applies to all devices
LPSPI0 @20 MHz
tHO Data hold time 3 — — ns Slave 11
[9]
(outputs)
tHO Data hold time -8 — — ns Master 11
[9]
(outputs)
tHO Data hold time 3 — — ns Slave_10Mbps 11
[9]
(outputs)
tHO Data hold time -15 — — ns Master_10Mbps 11
[9]
(outputs)
tHO Data hold time -2 — — ns Master Loopback, 11
[4] [9]
(outputs) applies to all devices
LPSPI0 @20 MHz
tRI/FI Rise/Fall time — — 1 ns Slave —
[10]
input
Table continues on the next page...

---

*Page 59*

Table 43. LPSPI ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
tRI/FI Rise/Fall time — — 1 ns Master —
[10]
input
tRI/FI Rise/Fall time — — 1 ns Master Loopback —
[4] [10]
input
[1] For LPSPI0 instance, max. peripheral frequency is equal to AIPS_PLAT_CLK.
[2] tperiph = 1/fperiph
[3] fperiph = LPSPI peripheral clock
[4] Master Loopback mode: In this mode LPSPI_SCK clock is delayed for sampling the input data which is enabled by setting LPSPI_CFGR1[SAMPLE] bit as 1.
[5] These specifications apply to the SPI operation, as master or slave, at up to 10 Mbps for the combinations not indicated in the table below. Unless otherwise
noted, all other ‘master’ and ‘slave’ specifications are also applicable in the 10Mbps configurations. See table "LPSPI 20 MHz and 15 MHz Combinations.
[6] Minimum configuration value for CCR[PCSSCK] field is 3(0x00000011).
[7] Minimum configuration value for CCR[SCKPCS] field is 3(0x00000011).
[8] While selecting odd dividers, ensure Duty Cycle is meeting this parameter.
[9] Output rise/fall time is determined by the output load and GPIO pad drive strength setting. See the GPIO specifications for detail.
[10] The input rise/fall time specification applies to both clock and data, and is required to guarantee related timing parameters.
SS
(INPUT)
2 4
3
SPSCK
(CPOL = 0)
(INPUT)
5 5
SPSCK
(CPOL = 1)
(INPUT)
10 11 9
MISO
SLAVE MSB OUT BIT 6 ... 1 SLAVE LSB OUT
(OUTPUT)
8 6 7
MOSI
MSB IN BIT 6 ... 1 LSB IN
(INPUT)
Figure 17. LPSPI Slave Mode Timing (CPHA=1)
SS
(INPUT)
2 12 4
3 13
SPSCK
(CPOL = 0)
(INPUT)
5 5
12 13
SPSCK
(CPOL = 1)
9
(INPUT)
8
10 11 11
MISO
SLAVE MSB BIT 6 ... 1 SLAVE LSB OUT
(OUTPUT)
6 7
MOSI
MSB IN BIT 6 ... 1 LSB IN
(INPUT)
Figure 18. LPSPI Slave Mode Timing (CPHA=0)

---

*Page 60*

1
SS
(OUTPUT)
2 4
3
SPSCK
(CPOL = 0)
(OUTPUT)
5 5
SPSCK
(CPOL = 1)
(OUTPUT)
6 7
MISO
2
MSB IN BIT 6 ... 1 LSB IN
(INPUT)
10 11
MOSI
2
MSB OUT BIT 6 ... 1 LSB OUT
(OUTPUT)
Figure 19. LPSPI Master Mode Timing (CPHA=0)
1
SS
(OUTPUT)
2 4
3
SPSCK
(CPOL = 0)
(OUTPUT)
5 5
SPSCK
(CPOL = 1)
(OUTPUT)
6 7
MISO
2
MSB IN BIT 6 ... 1 LSB IN
(INPUT)
10 11
MOSI
PORT DATA MASTER MSB OUT BIT 6 ... 1 MASTER LSB OUT PORT DATA
(OUTPUT)
Figure 20. LPSPI Master Mode Timing (CPHA=1)

#### 12.2 LPSPI0 15 MHz

Note: 15 Mbps is supported on LPSPI0 only.
Table 44. LPSPI0 15 MHz
PORT SPI Signal 15 Mbps
PTB1 LPSPI0_SOUT LPSPI0_SOUT
PTB0 LPSPI0_PCS0 LPSPI0_PCS0
PTC9 LPSPI0_SIN LPSPI0_SIN
PTC8 LPSPI0_SCK LPSPI0_SCK
PTD6 LPSPI0_PCS0
PTD5 LPSPI0_PCS1
PTD12 LPSPI0_SOUT
PTD11 LPSPI0_SCK
PTD10 LPSPI0_SIN

---

*Page 61*

Note: Trace length should not exceed 11 inches for SCK pad when used in Master loopback mode.
2

#### 12.3 I C

2
See I/O parameters for I C specification.
"For supported baud rate see section 'Chip-specific LPI2C information' of the Reference Manual."

#### 12.4 FlexCAN characteristics

See I/O parameters for FlexCAN specification.
"For supported baud rate, see section 'Protocol timing' of the Reference Manual."

#### 12.5 SAI electrical specifications

#### 12.5.1 SAI Electrical Characteristics, Slave Mode

The following table describes the SAI electrical characteristics. Measurements are with maximum output load of 30pF,
input transition of 1ns and pad configured with DSE = 1'b1 and SRE = 1'b0. I/O operating voltage ranges from 2.97 V to 3.63 V.
Valid pin combinations to be referred from MCXE_Use sheet in IOmux.
Table 45. SAI Electrical Characteristics, Slave Mode
Symbol Description Min Typ Max Unit Condition Spec
Number
S13 SAI_BCLK cycle 80 — — ns — —
time (input)
S14 SAI_BCLK pulse 45 — 55 % — —
width high/low
[1]
(input)
S15 SAI_RXD input 8 — — ns — —
setup before
SAI_BCLK
S16 SAI_RXD input hold 2 — — ns — —
after SAI_BCLK
S17 SAI_BCLK to — — 28 ns — —
SAI_TXD output
valid
S18 SAI_BCLK to 0 — — ns — —
SAI_TXD output
invalid
S19 SAI_FS input setup 8 — — ns — —
before SAI_BCLK
S20 SAI_FS input hold 2 — — ns — —
after SAI_BCLK
S21 SAI_BCLK to — — 28 ns — —
SAI_FS output valid
Table continues on the next page...

---

*Page 62*

Table 45. SAI Electrical Characteristics, Slave Mode ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
S22 SAI_BCLK to 0 — — ns — —
SAI_FS output
invalid
[1] The slave mode parameters (S15 - S22) assume 50% duty cycle on SAI_BCLK input. Any change in SAI_BCLK duty cycle input must be taken care during
the board design or by the master timing.
S13
S14
SAI_BCLK (input) S14
S21
S22
SAI_FS (output)
S19
S20
SAI_FS (input)
S17
S17 S18
S18
SAI_TXD
S15 S16
SAI_RXD
Figure 21. SAI slave mode

#### 12.5.2 SAI Electrical Characteristics, Master Mode

The following table describes the SAI electrical characteristics. Measurements are with maximum output load of 30pF,
input transition of 1ns and pad configured with DSE = 1'b1 and SRE = 1'b 0. I/O operating voltage ranges from 2.97 V to 3.63 V.
Valid pin combinations to be referred from MCXE31x*_Use sheet in IOmux.
Table 46. SAI Electrical Characteristics, Master Mode
Symbol Description Min Typ Max Unit Condition Spec
Number
S1 SAI_MCLK cycle 40 — — ns — —
time
S2 SAI_MCLK pulse 45 — 55 % — —
width high/low
S3 SAI_BCLK cycle 80 — — ns — —
time
S4 SAI_BCLK pulse 45 — 55 % — —
width high/low
S5 SAI_RXD input 28 — — ns — —
setup before
SAI_BCLK
S6 SAI_RXD input hold 0 — — ns — —
after SAI_BCLK
Table continues on the next page...

---

*Page 63*

Table 46. SAI Electrical Characteristics, Master Mode ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
S7 SAI_BCLK to — — 8 ns — —
SAI_TXD output
valid
S8 SAI_BCLK to -2 — — ns — —
SAI_TXD output
invalid
S9 SAI_FS input setup 28 — — ns — —
before SAI_BCLK
S10 SAI_FS input hold 0 — — ns — —
after SAI_BCLK
S11 SAI_BCLK to — — 8 ns — —
SAI_FS output valid
S12 SAI_BCLK to -2 — — ns — —
SAI_FS output
invalid
Figure 22. SAI master mode

#### 12.6 Ethernet characteristics

#### 12.6.1 Ethernet MII (10/100 Mbps)

The following timing specs are defined at the device I/O pin and must be translated appropriately to arrive at timing specs/
constraints for the physical interface. Measurements are with maximum output load of 25pF, input transition of 1ns and pad
configured with DSE = 1'b1 and SRE = 1'b0. I/O operating voltage ranges from 2.97 V to 3.63 V.
Valid pin combinations to be referred from MCXE31x*_Use sheet in IOmux.

---

*Page 64*

Table 47. Ethernet MII (10/100 Mbps)
Symbol Description Min Typ Max Unit Condition Spec
Number
— RXCLK frequency — 2.5/25 — MHz 10/100 Mbps —
MII1 RXCLK pulse width 35 — 65 %RXCLK — —
high period
MII2 RXCLK pulse width 35 — 65 %RXCLK — —
low period
MII3 RXD[3:0], RXDV, 5 — — ns 10/100 Mbps —
RXER to RXCLK
setup
MII4 RXCLK to RXD[3:0], 5 — — ns 10/100 Mbps —
RXDV, RXER hold
tCYC_TX TXCLK frequency — 2.5 / 25 — MHz 10/100 Mbps —
MII5 TXCLK pulse width 35 — 65 %TXCLK — —
high period
MII6 TXCLK pulse 35 — 65 %TXCLK — —
width low period
MII7 TXCLK to TXD[3:0], 2 — — ns — —
TXEN, TXER invalid
MII8 TXCLK to TXD[3:0], — — 25 ns — —
TXEN, TXER valid
Figure 23. MII receive diagram

---

*Page 65*

Figure 24. MII transmit diagram

#### 12.6.2 Ethernet RMII (10/100 Mbps)

The following timing specs are defined at the device I/O pin and must be translated appropriately to arrive at timing specs/
constraints for the physical interface. Measurements are with maximum output load of 25pF, input transition of 1ns and pad
configured with DSE = 1'b1 and SRE = 1'b0. I/O operating voltage ranges from 2.97 V to 3.63 V.
Valid pin combinations to be referred from MCXE_Use sheet in IOmux.
Table 48. Ethernet RMII (10/100 Mbps)
Symbol Description Min Typ Max Unit Condition Spec
Number
— RMII input — — 50 MHz 10/100 Mbps —
clock frequency
(RMII_CLK)
RMII1,RMII5 RMII_CLK pulse 35 — 65 %RMII_C — —
width high LK period
RMII2,RMII6 RMII_CLK pulse 35 — 65 %RMII_C — —
width low LK period
RMII3 RXD[1:0], CRS_DV, 4 — — ns — —
RXER to RMII_CLK
setup
RMII4 RMII_CLK to 2 — — ns — —
RXD[1:0], CRS_DV,
RXER hold
Table continues on the next page...

---

*Page 66*

Table 48. Ethernet RMII (10/100 Mbps) ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
RMII8 RMII_CLK to — — 15 ns — —
TXD[1:0], TXEN
data valid
RMII7 RMII_CLK to 2 — — ns — —
TXD[1:0], TXEN
data invalid
Figure 25. RMII receive diagram

---

*Page 67*

Figure 26. RMII transmit diagram

#### 12.6.3 MDIO timing specifications

The following table describes the MDIO electrical characteristics. Measurements are with maximum output load of 25 pF, input
transition of 1 ns and pad configured with fastest slew settings (DSE = 1'b1 and SRE = 1’b0). I/O operating voltage ranges from
2.97 V to 3.63 V. MDIO pin must have external Pull-up.
Valid pin combinations to be referred from MCXE31x*_Use sheet in IOmux.
Table 49. MDIO timing specifications
Symbol Description Min Typ Max Unit Condition Spec
Number
— MDC clock — — 2.5 MHz — —
frequency
MDC1 MDC pulse width 40 — 60 %MDC — MDC1
high period
MDC2 MDC pulse width low 40 — 60 %MDC — MDC2
period
MDC5 MDC falling edge — — 25 ns — MDC5
to MDIO output
valid(maximum
propagation delay)
MDC6 MDC falling edge -10 — — ns — MDC6
to MDIO output
invalid(minimum
propagation delay)
Table continues on the next page...

---

*Page 68*

Table 49. MDIO timing specifications ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
MDC3 MDIO (input) to 25 — — ns Applies to MCXE31B MDC3
MDC rising edge
setup time
MDC4 MDIO (input) to 0 — — ns — MDC4
MDC rising edge
hold time
MDC1 MDC2
MDC (output)
MDC6
MDIO (output)
MDC5
MDIO (input)
MDC3 MDC4
Figure 27. MII/RMII serial management channel timing

#### 12.7 QuadSPI

#### 12.7.1 QuadSPI Quad 3.3V SDR 120MHz

The following table applies to MCXE31B.
The following table describes the QuadSPI electrical characteristics. Measurements are with maximum output load of 25pF, input
transition of 1ns and pads configured with DSE = 1'b1 and SRE = 1'b0. I/O operating voltage ranges from 2.97V to 3.63V. QuadSPI
trace length should be less than or equal to 2 inches. For Single and Dual IO modes of operation if external device doesn’t have
pull-up feature, then external pull-up must be added at board level for unused device pins. With external pull-up, performance
of the interface may degrade in Quad IO mode based on load associated with external pull-up. QuadSPI support delay chain
upto length 16, wherein delay length of low-frequency segment is 16 and length of high-frequency segment is 0. See the device
Reference Manual for register and bit descriptions.
Valid pin combinations to be referred from MCXE31x*_Use sheet in IOmux
Program register value QuadSPI_FLSHCR[TCSS] = 4`h3.
Program register value QuadSPI_FLSHCR[TCSH] = 4`h3.
Program register value QuadSPI_DLLCRA[SLV_FINE_OFFSET] to 4'b0001.
Data transitions measured at 30%/70% supply for the write path. Data transitions measured at mid-supply for the read path. Clock
transitions measured at mid-supply.

---

*Page 69*

Table 50. QuadSPI Quad 3.3V SDR 120MHz
Symbol Description Min Typ Max Unit Condition Spec
Number
fSCK SCK clock — — 120 MHz Pad Loopback —
[1]
frequency
fSCK SCK clock — — 60 MHz Internal Loopback —
[1]
frequency
tSCK SCK clock period 1/fSCK — — ns Pad Loopback —
tSCK SCK clock period 1/fSCK — — ns Internal Loopback —
tSDC SCK duty cycle 45 — 55 % Internal Loopback —
tSDC SCK duty cycle 45 — 55 % Pad Loopback —
tIS Data input setup 1.75 — — ns Pad Loopback —
time
tIS Data input setup 9 — — ns Internal Loopback —
time
tIH Data input hold time 1 — — ns Pad Loopback —
tIH Data input hold time 1 — — ns Internal Loopback —
tOV Data output valid — — 1.75 ns Pad Loopback —
time
tOV Data output valid — — 1.75 ns Internal Loopback —
time
tIV Data output invalid -1.5 — — ns Pad Loopback —
time
tIV Data output invalid -1.5 — — ns Internal Loopback —
time
tCSSCK CS to SCK time 5 — — ns Pad Loopback —
tCSSCK CS to SCK time 5 — — ns Internal Loopback —
tSCKCS SCK to CS time 3 — — ns Pad Loopback —
tSCKCS SCK to CS time 3 — — ns Internal Loopback —
[1] This frequency specification is valid only if output valid time of external flash is ≤ 5.5ns, and if output valid time of external flash is more than 5.5ns but ≤
6.5ns, then maximum fSCK is 104MHz.

---

*Page 70*

Figure 28. QuadSPI input timing (SDR mode)
Figure 29. QuadSPI output timing (SDR mode)

#### 12.8 LPUART specifications

See I/O parameters for LPUART specifications.

### 13 Debug modules

#### 13.1 Debug trace timing specifications

The following table describes the Debug trace electrical characteristics. Measurements are with maximum output load of 25pF,
input transition of 1ns and pad configured with DSE = 1'b1 and SRE = 1'b0.
See I/O parameters for GPIO electrical specifications.

---

*Page 71*

Table 51. Debug trace timing specifications
Symbol Description Min Typ Max Unit Condition Spec
Number
fTRACE Trace clock — — 120 MHz — —
frequency (trace on
Fast pads)
fTRACE Trace clock — — 25 MHz — —
frequency (trace on
StandardPlus pads)
tDVW Data output valid 1.2 — — ns — —
window
tDIV Data output invalid 0.3 — — ns — —
Figure 30. Trace CLKOUT specifications

#### 13.2 SWD electrical specifications

The following table describes the SWD electrical characteristics. Measurements are with maximum output load of 30pF, input
transition of 1ns and pad configured with DSE = 1'b1 and SRE = 1'b0.
Table 52. SWD electrical specifications
Symbol Description Min Typ Max Unit Condition Spec
Number
S1 SWD_CLK — — 33 MHz — S1
frequency
S2 SWD_CLK cycle 1 / S1 — — ns — S2
period
S3 SWD_CLK pulse 40 — 60 % — S3
width
S4 SWD_CLK rise and — — 1 ns — S4
fall times
Table continues on the next page...

---

*Page 72*

Table 52. SWD electrical specifications ...continued
Symbol Description Min Typ Max Unit Condition Spec
Number
S9 SWD_DIO input 5 — — ns — S9
data setup time to
SWD_CLK rise
S10 SWD_DIO input 5 — — ns — S10
data hold time
after SWD_CLK
rising edge
S11 SWD_CLK high to — — 22 ns — S11
SWD_DIO output
data valid
S12 SWD_CLK high to — — 22 ns — S12
SWD_DIO output
data hi-Z
S13 SWD_CLK high to 0 — — ns — S13
SWD_DIO output
data invalid
S2
S3 S3
SWD_CLK (input)
S4 S4
Figure 31. SWD Input Clock Timing
SWD_CLK
S9 S10
SWD_DIO Input data valid
S11
S13
SWD_DIO Output data valid
S12
SWD_DIO
Figure 32. SWD Output Data Timing

#### 13.3 JTAG electrical specifications

The following table describes the JTAG electrical characteristics. These specifications apply to JTAG and boundary scan.
Measurements are with maximum output load of 30pF, input transition of 1ns and pad configured with DSE = 1'b1 and SRE = 1'b0.

---

*Page 73*

Table 53. JTAG electrical specifications
Symbol Description Min Typ Max Unit Condition Spec
Number
[1] [2]
tJCYC TCK cycle time 30 — — ns — 1
tJDC TCK clock pulse 40 — 60 % — 2
width
tTCKRISE TCK rise/fall times — — 1 ns — 3
(40%-70%)
tTMSS, tTDIS TMS, TDI data setup 5 — — ns — 4
time
tTMSH, tTDIH TMS, TDI data hold 5 — — ns — 5
time
tTDOV TCK low to TDO — — 22 ns — 6
[3]
data valid
tTDOI TCK low to TDO 0 — — ns — 7
data invalid
tTDOHZ TCK low to TDO — — 22 ns — 8
high impedance
tBSDV TCK falling edge to — — 600 ns — 11
[4]
output valid
tBSDVZ TCK falling edge to — — 600 ns — 12
output valid out of
high impedance
tBSDHZ TCK falling edge — — 600 ns — 13
to output high
impedance
tBSDST Boundary scan input 15 — — ns — 14
valid to TCK rising
edge
tBSDHT TCK rising edge to 15 — — ns — 15
boundary scan input
invalid
[1] This timing applies to TDI, TDO, TMS pins, however, actual frequency is limited by pad type for EXTEST instructions. Refer to pad specification for allowed
transition frequency
[2] Cycle time is 30ns assuming full cycle timing. Cycle time is 60ns assuming half cycle timing.
[3] Timing includes TCK pad delay, clock tree delay, logic delay and TDO output pad delay.
[4] Applies to all pins, limited by pad slew rate. Refer to IO delay and transition specification and add 20 ns for JTAG delay.

---

*Page 74*

TCK
2
3 2
1 3
Figure 33. JTAG TCK Input Timing
TCK
4
5
TMS, TDI
6
7 8
TDO
Figure 34. JTAG Test Access Port Timing

---

*Page 75*

TCK
11 13
Output
signals
12
Output
signals
14
15
Input
signals
Figure 35. Boundary Scan Timing

### 14 Package dimensions

#### 14.1 Obtaining package dimensions

Package dimensions are provided in the package drawings. To find a package drawing, go to nxp.com and perform a keyword
search for the drawing’s document number:
Package option Document Number
48-pin LQFP 98ASH00962A
172-pin HDQFP 98ASA01107D
100-pin HDQFP 98ASA01570D
100-pin LQFP 98ASS23308W

### 15 Revision history

The following table lists the changes in this document.
Document ID Release date Description
MCXEP172M160 6 April 2026
• Added 100LQFP package details.
FB0 v.4.0
MCXEP172M160 2 February 2026
• Updated Document ID and title.
FB0 v.3.0
Table continues on the next page...

---

*Page 76*

Table continued from the previous page...
Document ID Release date Description
• Updated Ambient temperature T A to 125 °C
• Updated maximum temperature T J to 135 °C
• Updated SIL2 related content as MCX E is SIL2 certified.
MCXEP172M160 4 December 2024
• Initial release.
FB0 v.2.0

---

*Page 77*

### Legal information

#### Data sheet status

[1] [2] [3]
Document status Product status Definition
Objective [short] data sheet Development This document contains data from the objective specification for product
development.
Preliminary [short] data sheet Qualification This document contains data from the preliminary specification.
Product [short] data sheet Production This document contains the product specification.
[1] Please consult the most recently issued document before initiating or completing a design.
[2] The term 'short data sheet' is explained in section "Definitions".
[3] The product status of device(s) described in this document may have changed since this document was published and may differ in case of multiple devices.
The latest product status information is available on the Internet at URL https://www.nxp.com .

#### Definitions Disclaimers

Draft — A draft status on a document indicates that the content is still Limited warranty and liability — Information in this document is believed
under internal review and subject to formal approval, which may result to be accurate and reliable. However, NXP Semiconductors does not give
in modifications or additions. NXP Semiconductors does not give any any representations or warranties, expressed or implied, as to the accuracy
representations or warranties as to the accuracy or completeness of or completeness of such information and shall have no liability for the
information included in a draft version of a document and shall have no consequences of use of such information. NXP Semiconductors takes no
liability for the consequences of use of such information. responsibility for the content in this document if provided by an information
source outside of NXP Semiconductors.
Short data sheet — A short data sheet is an extract from a full data sheet with
In no event shall NXP Semiconductors be liable for any indirect, incidental,
the same product type number(s) and title. A short data sheet is intended for
punitive, special or consequential damages (including - without limitation -
quick reference only and should not be relied upon to contain detailed and full
lost profits, lost savings, business interruption, costs related to the removal or
information. For detailed and full information see the relevant full data sheet,
replacement of any products or rework charges) whether or not such damages
which is available on request via the local NXP Semiconductors sales office.
are based on tort (including negligence), warranty, breach of contract or any
In case of any inconsistency or conflict with the short data sheet, the full data
other legal theory.
sheet shall prevail.
Notwithstanding any damages that customer might incur for any reason
Product specification — The information and data provided in a Product data
whatsoever, NXP Semiconductors’ aggregate and cumulative liability towards
sheet shall define the specification of the product as agreed between NXP
customer for the products described herein shall be limited in accordance with
Semiconductors and its customer, unless NXP Semiconductors and customer
the Terms and conditions of commercial sale of NXP Semiconductors.
have explicitly agreed otherwise in writing. In no event however, shall an
agreement be valid in which the NXP Semiconductors product is deemed to Right to make changes — NXP Semiconductors reserves the right to make
offer functions and qualities beyond those described in the Product data sheet. changes to information published in this document, including without limitation
specifications and product descriptions, at any time and without notice. This
document supersedes and replaces all information supplied prior to the
publication hereof.

---

*Page 78*

Applications — Applications that are described herein for any of these HTML publications — An HTML version, if available, of this document is
products are for illustrative purposes only. NXP Semiconductors makes no provided as a courtesy. Definitive information is contained in the applicable
representation or warranty that such applications will be suitable for the document in PDF format. If there is a discrepancy between the HTML
specified use without further testing or modification. document and the PDF document, the PDF document has priority.
Customers are responsible for the design and operation of their applications
Translations — A non-English (translated) version of a document, including
and products using NXP Semiconductors products, and NXP Semiconductors
the legal information in that document, is for reference only. The English
accepts no liability for any assistance with applications or customer product
version shall prevail in case of any discrepancy between the translated and
design. It is customer’s sole responsibility to determine whether the NXP
English versions.
Semiconductors product is suitable and fit for the customer’s applications and
products planned, as well as for the planned application and use of customer’s Security — Customer understands that all NXP products may be subject to
third party customer(s). Customers should provide appropriate design and unidentified vulnerabilities or may support established security standards or
operating safeguards to minimize the risks associated with their applications specifications with known limitations. Customer is responsible for the design
and products. and operation of its applications and products throughout their lifecycles
to reduce the effect of these vulnerabilities on customer’s applications
NXP Semiconductors does not accept any liability related to any default,
and products. Customer’s responsibility also extends to other open and/or
damage, costs or problem which is based on any weakness or default in the
proprietary technologies supported by NXP products for use in customer’s
customer’s applications or products, or the application or use by customer’s
applications. NXP accepts no liability for any vulnerability. Customer should
third party customer(s). Customer is responsible for doing all necessary testing
regularly check security updates from NXP and follow up appropriately.
for the customer’s applications and products using NXP Semiconductors
products in order to avoid a default of the applications and the products or of the Customer shall select products with security features that best meet rules,
application or use by customer’s third party customer(s). NXP does not accept regulations, and standards of the intended application and make the
any liability in this respect. ultimate design decisions regarding its products and is solely responsible
for compliance with all legal, regulatory, and security related requirements
Limiting values — Stress above one or more limiting values (as defined in
concerning its products, regardless of any information or support that may be
the Absolute Maximum Ratings System of IEC 60134) will cause permanent
provided by NXP.
damage to the device. Limiting values are stress ratings only and (proper)
NXP has a Product Security Incident Response Team (PSIRT) (reachable
operation of the device at these or any other conditions above those
at PSIRT@nxp.com ) that manages the investigation, reporting, and solution
given in the Recommended operating conditions section (if present) or the
release to security vulnerabilities of NXP products.
Characteristics sections of this document is not warranted. Constant or
repeated exposure to limiting values will permanently and irreversibly affect the
quality and reliability of the device. Suitability for use in automotive applications (functional safety) — This NXP
product has been qualified for use in automotive applications. It has been
Terms and conditions of commercial sale — NXP Semiconductors products developed in accordance with ISO 26262, and has been ASIL classified
are sold subject to the general terms and conditions of commercial sale, accordingly. If this product is used by customer in the development of, or for
as published at https://www.nxp.com/profile/terms, unless otherwise agreed incorporation into, products or services (a) used in safety critical applications
in a valid written individual agreement. In case an individual agreement or (b) in which failure could lead to death, personal injury, or severe physical
is concluded only the terms and conditions of the respective agreement or environmental damage (such products and services hereinafter referred to
shall apply. NXP Semiconductors hereby expressly objects to applying the as “Critical Applications”), then customer makes the ultimate design decisions
customer’s general terms and conditions with regard to the purchase of NXP regarding its products and is solely responsible for compliance with all legal,
Semiconductors products by customer. regulatory, safety, and security related requirements concerning its products,
regardless of any information or support that may be provided by NXP. As
No offer to sell or license — Nothing in this document may be interpreted or
such, customer assumes all risk related to use of any products in Critical
construed as an offer to sell products that is open for acceptance or the grant,
Applications and NXP and its suppliers shall not be liable for any such use by
conveyance or implication of any license under any copyrights, patents or other
customer. Accordingly, customer will indemnify and hold NXP harmless from
industrial or intellectual property rights.
any claims, liabilities, damages and associated costs and expenses (including
attorneys’ fees) that NXP may incur related to customer’s incorporation of any
Quick reference data — The Quick reference data is an extract of the product
product in a Critical Application.
data given in the Limiting values and Characteristics sections of this document,
and as such is not complete, exhaustive or legally binding.
NXP B.V. — NXP B.V. is not an operating company and it does not distribute
or sell products.
Export control — This document as well as the item(s) described herein may be
subject to export control regulations. Export might require a prior authorization

#### from competent authorities. Trademarks

Notice: All referenced brands, product names, service names, and
trademarks are the property of their respective owners.

---

*Page 79*

NXP — wordmark and logo are trademarks of NXP B.V.

---

*Page 80*

### Contents

1 Overview ............................................................ 4 9.2 Flash memory Array Integrity and Margin Read
2 Block diagram ..................................................... 4 specifications .................................................... 42
3 Feature comparison ........................................... 4 9.3 Flash memory module life specifications .......... 43
4 Ordering information (flash vs package) ............ 6 9.3.1 Data retention vs program/erase cycles ........... 44
4.1 Determining valid orderable parts ..................... 6 9.4 Flash memory AC timing specifications ........... 44
5 General ............................................................... 6 9.5 Flash memory read timing parameters ............. 45
5.1 Absolute maximum ratings ................................. 6 10 Analog modules ................................................ 45
5.2 Voltage and current operating requirements ...... 7 10.1 SAR_ADC ......................................................... 45
5.3 Thermal operating characteristics ...................... 9 10.2 Supply Diagnosis .............................................. 47
5.4 ESD and Latch-up Protection Characteristics .... 9 10.3 Low Power Comparator (LPCMP) .................... 48
5.5 Thermal Attributes ............................................ 10 10.4 Temperature Sensor ........................................ 51
5.5.1 Description ....................................................... 10 11 Clocking modules ............................................. 52
5.5.2 Thermal characteristics .................................... 10 11.1 FIRC ................................................................. 52
6 Power management ......................................... 11 11.2 SIRC ................................................................. 52
6.1 Power mode transition operating behaviors ..... 11 11.3 PLL ................................................................... 52
6.1.1 Power mode transition operating behavior ....... 11 11.4 FXOSC ............................................................. 53
6.1.2 Boot time, HSE firmware not installed .............. 11 11.5 SXOSC ............................................................. 56
6.1.3 Boot time, HSE firmware installed .................... 12 12 Communication interfaces ................................ 56
6.1.4 HSE firmware memory verification time examples 12.1 LPSPI ............................................................... 56
......................................................................... 13 12.2 LPSPI0 15 MHz ................................................ 60
2
6.2 Supply Monitoring ............................................. 17 12.3 I C .................................................................... 61
6.3 Recommended Decoupling Capacitors ............ 18 12.4 FlexCAN characteristics ................................... 61
6.3.1 Recommended Decoupling Capacitor diagrams 12.5 SAI electrical specifications .............................. 61
......................................................................... 19 12.5.1 SAI Electrical Characteristics, Slave Mode ...... 61
6.4 V15 regulator (BJT option, NPN ballast transistor 12.5.2 SAI Electrical Characteristics, Master Mode .... 62
control) electrical specifications ........................ 23 12.6 Ethernet characteristics .................................... 63
6.5 Supply currents ................................................ 23 12.6.1 Ethernet MII (10/100 Mbps) .............................. 63
6.6 Operating mode ................................................ 31 12.6.2 Ethernet RMII (10/100 Mbps) ........................... 65
6.7 Cyclic wake-up current .................................... 32 12.6.3 MDIO timing specifications ............................... 67
7 I/O parameters ................................................. 33 12.7 QuadSPI ........................................................... 68
7.1 GPIO DC electrical specifications, 3.3V Range 12.7.1 QuadSPI Quad 3.3V SDR 120MHz .................. 68
(2.97V - 3.63V) ................................................. 33 12.8 LPUART specifications ..................................... 70
7.2 GPIO DC electrical specifications, 5.0V (4.5V - 13 Debug modules ................................................ 70
5.5V) ................................................................. 36 13.1 Debug trace timing specifications ..................... 70
7.3 5.0V (4.5V - 5.5V) GPIO Output AC Specification 13.2 SWD electrical specifications ........................... 71
......................................................................... 39 13.3 JTAG electrical specifications .......................... 72
7.4 3.3V (2.97V - 3.63V) GPIO Output AC 14 Package dimensions ........................................ 75
Specification ..................................................... 40 14.1 Obtaining package dimensions ........................ 75
8 Glitch Filter ....................................................... 41 15 Revision history ................................................ 75
9 Flash memory specification .............................. 41 Legal information .............................................. 77
9.1 Flash memory program and erase specifications
......................................................................... 42

---

*Page 81*

Please be aware that important notices concerning this document and the product(s) described
herein, have been included in section 'Legal information'.
© 2026 NXP B.V. All rights reserved.
For more information, please visit: https://www.nxp.com
Date of release: 6 April 2026
Document identifier: MCXEP172M160FB0