<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 2 -->

# Chapter 2

# Introduction

#### 2.1 Overview

The MCXE 31 product series further extends the highly-scalable portfolio of MCX E family in the commercial and industrial
industries with the Arm Cortex-M7 core at higher frequency, more memory, SIL-2 rating and advanced security module. With a
focus on industrial environment robustness, the MCXE31 product series devices are well suited to a wide range of applications
in electrical harsh environments, and are optimized for cost-sensitive applications offering new, space saving package options.
The MCX E31 series offers a broad range of memory, peripherals and performance options. Devices in this series share common
peripherals and pin-out, allowing developers to migrate easily within a chip series or among other chip series to take advantage
of more memory or feature integration.
CAUTION
MCXE315/MCXE316/MCXE317 and MCXE31B specific information is preliminary until these devices are qualified
and may change without notice.

#### 2.2 MCXE31 Series Introduction

NOTE
MCXE31 series has only a single ARM core and does not support lockstep mode. Any content in this manual
related to multi-core or lockstep mode or dual-core interconnection must be ignored.
The MCXE31 series comes to market together with easy-to-use enablement software, application specific software, and various
development tools, supported by broad third parties in different development phases.
The portfolio scalability, future proof feature like advanced security, as well as software/tool/third-party development support
allows developers to standardize on the MCXE31 series for their end product platforms, maximizing hardware and software reuse,
and reducing time-to-market.
Following are the general features of the MCXE31 series chips:
• 32-bit Arm Cortex-M7 core with IEEE-754 compliant SPFPU , executing up to 160 MHz
• Scalable memory footprints up to 4 MB flash memory and up to 512 KB SRAM
• Precision mixed-signal capability with low power comparators (LPCMP) and multiple 12-bit ADCs
• Powerful timers for a broad range of applications including motor control, lighting control and body applications
• Serial communication interfaces such as Serial communication interfaces including LPUART, LPSPI, LPI2C, FlexCAN with
ISOCAN-FD support, Ethernet and QuadSPI. FXIO configuration allows other communication options including SENT.
• SESIP level 2 compliant EdgeLock Secure Enclave (HSE_B)
• Power supply (3.0 – 5.5 V) with fully functional flash memory program/erase/read operations
• Functional safety compliance with IEC 61508 SIL 2 hardware integrity and SIL 3 systematic capability (features depend on
device specification)
— Multiple internal watchdogs
— Voltage monitors
— Clock monitors
— Memory protection
— Data transport checks
— ECC on memories

---

*Page 15*

Introduction
— Cyclic redundancy checking
• Ambient operation temperature range: –40°C to 135°C
• Junction temperature range: –40°C to 135°C

#### 2.3 Feature summary

The MCX E31 product family includes the Arm Cortex-M7 core features described in the following table.
Table 4. MCXE 31 chip's feature summary
Feature Inclusions
Core and architecture • Singel Arm Cortex-M7 core running up to 160 MHz
• Arm core based on the Armv7 architecture and ThumbR-2 ISA with
2.14 DMIPS/MHz
• 8 KB data and 8 KB instruction cache for optimizing wait state execution
from memories
• 96 KB Tightly Coupled Memory associated with each core
• On-core MPU for dynamic task protection (16 regions)
• SPFPU, IEEE 754 compliant
• Harvard bus architecture implementing dedicated instruction and data
path
• 5-stage pipeline with branch speculation
• XRDC integrated with a crossbar switch to provide memory and
peripheral protection
• DSP
• I/O protection (VIRT_WRAPPER)
• ETM supporting instruction trace
• Arm third-party ecosystem support: software and tools to help minimize
development time and cost
DMA
• Up to 2x64-channel DMAMUX
• eDMA with up to 32 channels
• Complex data transfers performed with minimal intervention from a host
processor
• Programmable support for scatter-gather DMA processing
System and power management
• Support for simplified power modes (Run and Standby)
• Support for clock gating of unused modules; specific peripherals
continue to work in low-power modes
• Support for external ballast transistor to generate core supply
• Fully independent CPU and peripheral clocking scheme
• Rapid start-up from a 48 MHz FIRC
Table continues on the next page...

---

*Page 16*

Introduction
Table 4. MCXE 31 chip's feature summary (continued)
Feature Inclusions
• Various low-power oscillators such as the 32 kHz SIRC and an external
32 kHz crystal support SXOSC
• PMC with LVD and selectable trip points
• Support for multiple power modes
• NMI
Memory and memory interfaces
• Up to 4 MB program flash memory, up to 128 KB data flash memory,
and up to 512 KB SRAM, all with an ECC
• 4-bit/8-bit QuadSPI
Clocks
• External 8 MHz–40 MHz crystal oscillator or resonator
• External 32 kHz crystal oscillator
• Internal clock references
— 48 MHz FIRC ±5%
— 32 kHz SIRC ±10%
• Up to 960 MHz PLL for divided system clock operation
Security and integrity
• EdgeLock Secure Enclave (HSE_B)
— Upgradable Firmware delivered by NXP, to be programmed by the
user
• Security ciphers:
— Symmetric: AES-128/192/256
— Cipher modes: ECB , CBC , CMAC , GMAC , CTR , OFB , CCM , and
GCM
— Asymmetric: RSA (up to 4096 bits) and ECC (up to 521 bits)
— Hash: Miyaguchi-Preneel, SHA-2/SHA-3 (up to 512 bits)
— Number of keys is configurable and controlled by HSE FW
— Random number generator
• Security use case supported:
— Secure boot
— Secure communication
— Component protection
— Secure storage
— Key exchange
Safety IEC61508
• Classification up to SIL -2
• ERM and EIM support
• WDOG with an independent clock source
Table continues on the next page...

---

*Page 17*

Introduction
Table 4. MCXE 31 chip's feature summary (continued)
Feature Inclusions
• Voltage monitors
• Bandgap voltage available as ADC input
• External clock source monitoring using an independent reference
• PLL lock and loss-of-lock protection
• XRDC
• ECC on code flash memory, data flash memory, and system RAM
• ADC self-test feature
• Internal analog monitoring of all supplies available
• CRC generation module
• FCCU failure output
Analog
• 12-bit ADC
— Up to 72 external analog inputs
— 1 μs conversion time
— Internal bandgap voltage reference channel, supporting automatic
compare and an optional hardware trigger
— Up to five internal reference inputs
— Automatic compare with interrupt
— Self-test and self-calibration scheme
• ACMP with an internal 8-bit DAC as a reference
— ACMP with both positive and negative inputs, separately
selectable interrupts on rising and falling comparator output
— Ability to cross trigger the timers from both the ADC and ACMP
outputs
• Temperature Sensor (TempSense) with output measurable by ADC.
Timers
• 16-bit eMIOS timers, offering up to 72 standard channels
— Input capture, output compare, and PWM modes
— Fault input support with global fault control
— Multiple features such as deadtime insertion, configurable polarity,
quadrature decoding, and so on
• 32-bit PITs, with four channels, for raising interrupts and triggering DMA
channels
• 32-bit RTC
• Motor control and power conversion using combination of eMIOS, LCU ,
BCTU, and ADC
• Up to one STMs, with four channels each
Table continues on the next page...

---

*Page 18*

Introduction
Table 4. MCXE 31 chip's feature summary (continued)
Feature Inclusions
Communications
• LPSPI supporting DMA with full-duplex or single-wire bidirectional
communication in Master or Slave mode
• FlexIO, with an option to configure it as different communication
peripherals, offering support for SENT
• LPI2C modules with DMA support, low-power availability, master or
slave support, and system management bus
• LPUART with DMA support, having an optional 13-bit break, full-duplex
non-return- to-zero (NRZ), low-power availability and supports LIN
protocol versions 1.3, 2.0, 2.1, 2.2A, and SAE J2602 with using SW
LIN driver
• FlexCAN modules with ISOCAN-FD and DMA support
• SAI capable of supporting stereo audio channels
• EMAC complex (10/100 Ethernet) that supports 1588 timers, MII/RMII
interface, AVB , and TSN support
Debug
• DWT , with four configurable comparators as hardware watchpoints
• SWO -synchronous trace data support
• ITM with software and hardware trace, plus time stamping
• FPB with an ability to patch code and data from code space to system
space
• Trace of all execution units and bus masters made available through
an Arm TPIU over GPIO pins; a very low bandwidth trace option also
available via the SWO
• Embedded trace FIFO (ETF)—a dedicated trace buffer available for
each of the core masters, allowing data to be captured internally before
being optionally routed to external trace pins
• SWV —trace capability providing displays of reads, writes, exceptions,
PC samples, and print
I/O and package
• Up to 145 GPIO pins
• Up to 142 GPIO pins with interrupt functionality
• Up to 59 GPIO pins with wakeup capability
• Pseudo open drain support on LPUART, FlexIO, LPI2C
• Package options of 172 MAX QFP, 100 MAX QFP, 48 LQFP, and 100
LQFP

---

*Page 19*

Introduction

#### 2.4 Block diagram

eDMA3 Clock generation ELE_HSEB
32ch FXOSC FIRC SIRC 32KHz SXOSC
AHB CM0+ 8-40MHz 48MHz 32KHz
MCXE31B 64-bit AHB
64-bit
PLL
I-CACHE MDAC1 MDAC3 Cortex-M7 240/ 160MHz
8K B ADDR ADDR
MPU FPU GEN GEN RDATA
D-CACHE EMAC RDATA
System
CHK CHK
8K B NVIC DSP wDATA wDATA
GEN GEN PCU
64-bit 1:2 System Clock
I-TCM MDAC5 PMC
32K B AHB
32-bit M0 AHB
D-TCM 32-bit 1:2 32:64 AXBS_L ite splitter
32K B X BIC
ADDR
32-bit S0 S1
D-TCM I
GEN
RDATA
32K B AHBS 32-bit AX 64-bit AHBP 32-bit CHK
wDATA 1:1 bypass
GEN
XHB400 ADDR GEN
RDATA
CHK
wDATA
AHB_32_64 GEN
ADDR
GEN
RDATA
MDAC0 CHK wDATA
GEN
ADDR
GEN
RDATA
CHK
wDATA
GEN
M0 M0 M1 M3 M2 M1 M0 M3 M1 M2
System Peripheral
AXBS (64-bit) AXBS_L ite (64-bit)
AX BS AX BS
S0 S4 S1 S2 X BIC S6 S3 S5 S0 X BIC S1 S2
MRC0 MRC1 MRC2 2:1 2:1
ADDR
CHK ADDR ADDR ADDR
RDATA CHK CHK CHK
L EGEND GEN
2:1 RDATA RDATA RDATA
wDATA GEN GEN GEN wDATA wDATA wDATA
ADDR ADDR C HK ADDR CHK
CHK ADDR ADDR ADDR
CHK CHK
AX I64 C HK CHK RDATA RDATA RDATA CHK CHK 1:1 64:32 CHK EMAC
GEN GEN GEN RDATA RDATA RDATA L PUART8..15
GEN GEN GEN
AHB64 wDATA wDATA wDATA AIPS0 AIPS1 AIPS2 L PSPI4..5
CHK CHK ADDR GEN CHK QSPI
AHB32 P0 P2 P1 RDATA 120MHz SAI1
PFL ASH CHK L PC MP2
wDATA PAC0 PAC1 PAC2
IP-BUS 64-bit + ECC* GEN HSE_MU1_B
x256 P0 P0
Fixed gaskets PRAM0 PRAM1 AHB QSPI AHB
Splitter
64-bit + ECC* 64-bit + ECC* DATA TRGMUX AXBS XBIC 2..3 DMAMUX0..1 PMC
ECC gaskets C-Flash 32 MHz X BIC & CODE BC TU XBIC 0..1 eDMA_TC D12..31 RTC Flash memories
C-Flash x72 x72 eMIOS 0..2 eDMA SEMA_42 MC _RGM PIT2
Configurable gaskets 1 MB 2MB ADDR L C U0..1 eDMA_TC D0..11 RAM controller 1 VIRTWRAPPER0..6 FlexC AN0..5
CHK SRAM0 SRAM1 ADC 0..2 DEBUG0..4 STM1
RDATA DC M FXIO
On-platform peripheral AIPS1 C-Flash D-Flash C-Flash GEN 160 K B 160 K B PIT0..1 SDA_AP WK PU L PUART0..7
1 MB 128 K B wDATA 2MB
ADDR CHK
MUA EIM C MU0..5 L PI2C 0..1
On-platform peripheral AIPS2 CHK MUB ERM TSPC L PSPI0..3
RDATA MSC M 32kHz SIRC SAI0
Off-platform peripheral AIPS0 GEN wDATA RAM controller 0 32kHz SXOSC L PC MP0..1
CHK Flash controllers 48 MHz FIRC TEMPSENSE
Off-platform peripheral AIPS1 SWT0 8-40 MHz FXOSC C RC
STM0 MC _C GM FC C U
Off-platform peripheral AIPS2 XRDC MC _ME MTR
INTM PL L HSE_MU0_B
JDC
* ECC data and address encode C ONFIG_GPR
SEL FTEST_GPR
Figure 5. Block diagram - MCXE31B

---

*Page 20*

Introduction
C lock generation
FXOSC FIRC SIRC 32KHz SXOSC
8-40MHz 48MHz 32KHz
I-CACHE Cortex-M7 eDMA3 ELE_HSEB
8K B 12ch
CM0+ PLL
MPU FPU AHB AHB 120MHz
D-CACHE 64-bit 64-bit
8K B NVIC DSP
MDAC1 System
I-TCM 64-bit MDAC3
32K B ADDR PCU
Primary core GEN
RDATA ADDR System Clock
D-TCM 32-bit CHK GEN PMC
32K B wDATA RDATA
GEN CHK wDATA
D-TCM 32-bit I GEN
32K B AHBS 32-bit AX 64-bit AHBP 32-bit
1:1
XHB400
ADDR
MDAC0 GEN
RDATA
AHB_32_64 CHK
wDATA
GEN
ADDR
GEN
RDATA
CHK
wDATA
GEN
M0 M1 System AX BS M3 M2
AXBS, no IPS programming model, preset to Round-robin arbitration scheme with slaves parked on masters performing last accesses. 64-bit @120 MHz (64-bit) AX BS
S0 S1 S2 X BIC S3 S4 S5
MRC0 MRC1 2:1
ADDR ADDR ADDR ADDR
C HK CHK CHK CHK
RDATA RDATA RDATA RDATA
GEN GEN GEN GEN
ADDR wDATA wDATA
CHK ADDR wDATA wDATA CHK CHK CHK CHK CHK
RDATA
GEN RDATA GEN
AIPS0 AIPS1
L EGEND 1:1 64:32 P0
PFL ASH P1
64-bit + ECC
PAC0
AX I64 PAC1
x256 P0
AHB64 PRAM0
64-bit + ECC*
AHB32 TRGMUX C-Flash 30 MHz DMAMUX0..1
PMC
C-Flash x72 BC TU XBIC RTC Flash memories
1 MB 2MB eMIOS 0..1 eDMA MC _RGM
IP-BUS
SRAM0 L C U0..1 eDMA_TC D0..11 VIRTWRAPPER0..4 FlexC AN0..5
D-Flash 96 K B ADC 0..1 DEBUG0..4 DC M FXIO
Fixed gaskets 128 K B PIT0..1 SDA_AP WK PU L PUART0..7
I3C 0 EIM C MU0..5 L PI2C 0..1
ECC gaskets ERM TSPC L PSPI0..3
MSC M 32kHz SIRC
Configurable gaskets RAM controller 0 32kHz SXOSC L PC MP0..1
Flash controllers 48 MHz FIRC TEMPSENSE
On-platform peripheral AIPS1 SWT0 8-40 MHz FXOSC C RC
STM0 MC _C GM FC C U
Off-platform peripheral AIPS0 XRDC MC _ME MTR
INTM PL L HSE_MU0_B
Off-platform peripheral AIPS1 HSE_MU1_B
JDC
C ONFIG_GPR
* ECC data and address encode
Figure 6. Block diagram - MCXE315, MCXE316, and MCXE317

#### 2.5 Feature comparison

The following table compares some of the prominent features related to memory and package options of these chips from the
MCXE31 family/product series:
• MCXE315
• MCXE316
• MCXE317
• MCXE31B
Table 5. MCXE31 chip's feature comparison
Feature Chip
MCXE315 MCXE316 MCXE317 MCXE31B
Core 1 x M7
Safety/SIL 2
Program flash memory 512 KB 1 MB 2 MB 4 MB
Data flash memory (KB) 64 128
Total RAM (KB) 112KB (incl. 128KB (incl. 192KB (incl. 512KB (including
96KB TCM) 96KB TCM) 96KB TCM) 96KB TCM)
Table continues on the next page...

---

*Page 21*

Introduction
Table 5. MCXE31 chip's feature comparison (continued)
Feature Chip
MCXE315 MCXE316 MCXE317 MCXE31B
Standby RAM 16 KB 32 KB
Security EdgeLock Secure Enclave (HSE_B)
Frequency (MHz) 120 160
DMA channels 12 32
FlexCAN instances 3 6 6
EMAC instances — 1
SAI instances — 2
LPUART instances 4 8 16
LPSPI instances 4 6
2
I C instances 2
FlexIO (incl. SENT 16 32
support) channels
1
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
48-pin LQFP package Yes No
100-pin LQFP package No Yes
1. 4-bit data width

#### 2.6 Glossary

AES Advanced encryption standard
SIL SIL/Safety integrity Level. This is a risk classification scheme as defined by IEC 61508 functional safety
standard for industrial

---

*Page 22*

Introduction
AVB Audio video bridging
CBC Cipher block chaining
CCM Counter with CBC MAC (Cipher block chaining message authentication code)
CMAC Cipher-based message authentication code
CTR Counter-based block cipher mode
DSP Digital signal processor
DWT Debug watchpoint and trace
ECB Electronic code book
ECC Elliptic curve cryptography/ Error code correction
ETM Embedded trace macrocell
ETF Embedded trace FIFO
FPB Flash patch and breakpoint unit
GCM Galois/Counter mode, an encryption algorithm
GMAC Galois message authentication code
GPIO General purpose input/output
ITM Instrumentation trace macrocell
ISOCAN-FD ISO 11898-1 compliant CAN with FD (Flexible datarate)
LVD Low voltage detection
NMI Non-maskable interrupt
OFB Output feedback based block cipher mode
PIL Processor-in-the-loop
PLL Phase locked loop oscillator
PWM Pulse width modulation
SDK Software development kit
SENT Single edge nibble transmission
SWV Serial wire viewer
SPFPU Single precision floating point unit
SWO Serial wire output
TPIU Trace port interface unit
TSN Time sensitive networking
WDOG Windowed watchdog

---

*Page 23*

