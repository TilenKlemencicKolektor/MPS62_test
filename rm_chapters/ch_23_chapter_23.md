<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 23 -->

# Chapter 23

# Clocking

#### 23.1 Introduction

This chapter describes the clocking architecture and includes the following information:
• System clock specifics
• Clock sources
• Clock architecture
• Clock control registers
• Clock monitoring
• Clock gating
• Module clocking
This chapter discusses the clocks generated on the chip. Peripheral-specific protocol clocks are described in the corresponding
peripheral chapters.

#### 23.2 Features

• Multiple clock sources supported for clock generation:
— Fast internal RC oscillator (FIRC)
— Slow internal RC oscillator (SIRC)
— Fast external crystal oscillator (FXOSC)
— Slow external crystal oscillator (SXOSC)
NOTE
SXOSC is not available in 100-MAX QFP, 48-pin LQFP, and 100-pin LQFP packages. For details, see 'MCXE31
chip's feature comparison' table in Introduction chapter.
— Phase-locked loop (PLL)
• Frequency-modulated PLL output clock to reduce electromagnetic emissions
• Precise clocks for timers and communication functions
• Glitchless clock switching Clock Generation module (MC_CGM) clock selectors
• System clock progressive clock frequency switching ( PCFS )
• Clock monitoring units (CMU_FC, CMU_FM) to check clock integrity
• Core and peripheral clock gating using the Mode Entry module's (MC_ME) partition process configuration registers

#### 23.3 Clocking overview

The MCXE31 clocking architecture consists of multiple:
• Clock sources
• Monitors
• Multiplexers
• Dividers

---

*Page 570*

Clocking
The blocks in the above bullet list provide the required clocking domains for the different functional blocks.
The sections in the following list show the clocking configuration of the chip:
• Clock source generation : PLL, FXOSC, FIRC, SIRC, and SXOSC
• MC_CGM mux 0 clocks : MC_CGM mux 0 generated clocks (not including EMAC clock signals)
• Clockout overview : CLKOUT_STANDBY and CLKOUT_RUN
• Other clocks
• EMAC clocking : Ethernet utilized clocking
The figures shown in MCXE31B clock system diagram , MCXE317 clock system diagram , and MCXE315/MCXE316 clock system
diagram show the overall clock tree for the different chip variants of the MCXE31 family, which are a combination of the sections
mentioned in the bullet list above.

#### 23.3.1 Clock source generation

PLLDIG
PLLDIG.PLLODIV_0[DIV]
PLLDIG.PLLDV[ODIV2]
1...16 PLL_PHI0_CLK
VCO_CLK PLLODIV2_CLK
PLL 1...63
1...16 PLL_PHI1_CLK
PLLDIG.PLLODIV_1[DIV]
FIRC
3 CMU_FM_1
DIV16 2
FIRC FIRC_CLK
DIV2 1
DIV2 0
ELE_HSEB.CONFIG_REG_GPR[FIRC_DIV_SEL]
CMU_FC_0
EXTAL
FXOSC FXOSC_CLK
XTAL
OSC32K_EXTAL
Not available on:
SXOSC SXOSC_CLK
- MCXE315/MCXE316
OSC32K_XTAL
CMU_FM_2
SIRC SIRC_CLK
Figure 50. Clock source generation

---

*Page 571*

Clocking

#### 23.3.2 MC_CGM mux 0 clocks

MUX_0_DC_0[DIV] CMU_FC_3
1...8 CORE_CLK
MUX_0_DC_1[DIV] CMU_FC_4
1...8 AIPS_PLAT_CLK
MUX_0_DC_2[DIV]
1...16 AIPS_SLOW_CLK
MUX0_0_CSC[RAMPUP]
MUX0_0_CSC[RAMPDOWN]
MUX_0_DC_3[DIV] CMU_FC_5
PLL_PHI0_CLK 8
PCFS 1...8 HSE_CLK
FIRC_CLK 0
PCFS_SDUR[SDUR] MUX_0_DC_4[DIV]
MUX_0_CSC[SELCTL]
1...8 DCM_CLK
MUX_0_DC_5[DIV]
1...8 LBIST_CLK Not available on:
- MCXE315
- MCXE316
MUX_0_DC_6[DIV]
- MCXE317
1...8 QSPI_MEM_CLK
Figure 51. MC_CGM mux 0 clocks
NOTE
The clock frequency relationship between TCK and HSE_CLK clocks for ELE_HSEB must be a minimum ratio of
1:1.5. For example, if HSE_CLK equals 80 MHz, then TCK must be less than or equal to 53 MHz (80 MHz ÷ 1.5).

---

*Page 572*

Clocking

#### 23.3.3 Clockout overview

MUX_6_CSC[SELCTL]
Not available on:
- MCXE315 EMAC_RX_CLK 25
- MCXE316
EMAC_RMII_TX_CLK 24
- MCXE317
AIPS_SLOW_CLK 23
Port
AIPS_PLAT_CLK 22
HSE_CLK 19 MUX_6_DC_0[DIV]
SIUL2_MSCR n [OBE]
CORE_CLK 16
1...64 CLKOUT_RUN
PLL_PHI1_CLK 9
SIUL2_MSCR n [SSS]
Not available on: PLL_PHI0_CLK 8
- MCXE315
SXOSC_CLK 4
- MCXE316
FXOSC_CLK 2
FIRC_CLK 1
SIRC_CLK 0
Port
MUX_5_DC_0[DIV] SIUL2_MSCR m [OBE]
Not available on:
AIPS_SLOW_CLK 23
- MCXE315
SXOSC_CLK
- MCXE316 4 1...8 CLKOUT_STANDBY
FXOSC_CLK 2
SIRC_CLK 1
FIRC_CLK 0
SIUL2_MSCR m [SSS]
MUX_5_CSC[SELCTL]
Figure 52. Clockout overview
NOTE
CLKOUT_RUN is not available during Standby mode.
23.3.3.1 SIUL2 options for CLKOUT_RUN
Table 98. SIUL2 options for CLKOUT_RUN
Source Destination Port MSCR n MSCR fields
OBE IBE SSS
PTB5 37 1 0 0101b
MUX_6_DC_0 divider
CLKOUT_RUN PTD10 106 1 0 0110b
output
PTD14 110 1 0 0111b
23.3.3.2 SIUL2 options for CLKOUT_STANDBY
Table 99. SIUL2 options for CLKOUT_STANDBY
Source Destination Port MSCR m MSCR fields
OBE IBE SSS
PTA12 12 1 0 0011b
MUX_5_DC_0 divider
CLKOUT_STANDBY
output
PTE10 138 1 0 0101b

---

*Page 573*

Clocking

#### 23.3.4 Other clocks

MUX_1_CSC[SELCTL]
MUX_1_DC_0[DIV]
AIPS_PLAT_CLK 22
FXOSC_CLK 2
1...2 STM0_CLK
FIRC_CLK 0
MUX_3_CSC[SELCTL]
MUX_3_DC_0[DIV]
22
CORE_CLK 16
1...4 FLEXCAN0_PE_CLK
2
FLEXCAN1_PE_CLK
0
FLEXCAN2_PE_CLK
Not available on:
MUX_4_CSC[SELCTL] - MCXE315
FLEXCAN3_PE_CLK - MCXE316
MUX_4_DC_0[DIV]
22
FLEXCAN4_PE_CLK Not available on:
16 - MCXE317
1...4 FLEXCAN5_PE_CLK
2
0
MUX_10_CSC[SELCTL]
MUX_10_DC_0[DIV]
PLL_AUX_PHI0_CLK 12
Not available on:
PLL_PHI1_CLK 9 - MCXE317
1...8 QSPI_2xSFCK
2 - MCXE315
- MCXE316
0 DIV-BY-2 QSPI_SFCK
MUX_11_CSC[SELCTL]
9 MUX_11_DC_0[DIV]
PLL_PHI0_CLK 8
1...8 TRACE_CLK
2
0
JTAG_TCK/SWD_CLK TCK
Figure 53. Other clocks

---

*Page 574*

Clocking

#### 23.3.5 MCXE31B clock system diagram

MCXE31B
MC_CGM MUX_0_DC_0[DIV] CMU_FC_3
1...8 CORE_CLK
MUX_0_DC_1[DIV] CMU_FC_4
1...8 AIPS_PLAT_CLK
MUX_0_DC_2[DIV]
1...16 AIPS_SLOW_CLK
8 MUX_0_DC_3[DIV] CMU_FC_5
PCFS 1...8 HSE_CLK
0 MUX_0_DC_4[DIV]
MC_CGM_MUX0
1...8 DCM_CLK
CMU with FXOSC reference
CMU with FIRC reference
22 MUX_1_DC_0[DIV]
2 1...2 STM0_CLK
0
PLLDIG.PLLODIV_0[DIV]
PLLDIG.PLLDV[ODIV2] PLL_PHI0_CLK
1...16
VCO_CLK PLLODIV2_CLK
PLL 1...63
1...16
PLL_PHI1_CLK 22 MUX_3_DC_0[DIV]
PLLDIG.PLLODIV_1[DIV] 2 1...4 FLEXCAN[0:2]_PE_CLK
0
MC_CGM_MUX3
3
FIRC ÷16 2 FIRC_CLK
(48 MHz) ÷2 1
÷2 0 CMU_FM_1
ELE_HSEB.CONFIG_REG_GPR[FIRC_DIV_SEL] 23
MUX_5_DC_0[DIV]
1...8 CLKOUT_STANDBY
2
1
0
EXTAL MC_CGM_MUX5 FXOSC FXOSC_CLK
(8-40 MHz)
XTAL 25
CMU_FC_0 24
23
22
MUX_6_DC_0[DIV]
9
19 1...64 CLKOUT_RUN
16
8
2
1
0
MC_CGM_MUX6
SIRC SIRC_CLK
(32 kHz)
CMU_FM_2
MC_CGM_MUX10
9 MUX_11_DC_0[DIV]
8 1...8 TRACE_CLK
2
1
0
MC_CGM_MUX11
RTC
1 1
2 2 RTC_CLK
3 3
RTC.RTCC[CLKSEL]
JTAG_TCK/SWD_CLK TCK
Figure 54. MCXE31B clock system diagram

---

*Page 575*

Clocking

#### 23.3.6 MCXE317 clock system diagram

MCXE31B
MC_CGM MUX_0_DC_0[DIV] CMU_FC_3
1...8 CORE_CLK
MUX_0_DC_1[DIV] CMU_FC_4
1...8 AIPS_PLAT_CLK
MUX_0_DC_2[DIV]
1...16 AIPS_SLOW_CLK
8 MUX_0_DC_3[DIV] CMU_FC_5
PCFS 1...8 HSE_CLK
0 MUX_0_DC_4[DIV]
MC_CGM_MUX0
1...8 DCM_CLK
CMU with FXOSC reference
CMU with FIRC reference
22 MUX_1_DC_0[DIV]
2 1...2 STM0_CLK
0
PLLDIG.PLLODIV_0[DIV]
PLLDIG.PLLDV[ODIV2] PLL_PHI0_CLK
1...16
VCO_CLK PLLODIV2_CLK
PLL 1...63
1...16
PLL_PHI1_CLK 22 MUX_3_DC_0[DIV]
PLLDIG.PLLODIV_1[DIV] 2 1...4 FLEXCAN[0:2]_PE_CLK
0
MC_CGM_MUX3
22 MUX_4_DC_0[DIV]
3
2 1...4 FLEXCAN[3:5]_PE_CLK
FIRC ÷16 2 FIRC_CLK
0
(48 MHz) ÷2 1
MC_CGM_MUX4
÷2 0 CMU_FM_1
ELE_HSEB.CONFIG_REG_GPR[FIRC_DIV_SEL] 23
MUX_5_DC_0[DIV]
1...8 CLKOUT_STANDBY
4
2
1
0
EXTAL MC_CGM_MUX5 FXOSC FXOSC_CLK
(8-40 MHz)
XTAL 25
CMU_FC_0 24
23
22
MUX_6_DC_0[DIV]
9
19 1...64 CLKOUT_RUN
16
8
4
2
1
OSC32K_EXTAL
SXOSC SXOSC_CLK 0
(32 kHz) MC_CGM_MUX6
OSC32K_XTAL
SIRC SIRC_CLK
(32 kHz)
CMU_FM_2
MC_CGM_MUX10
9 MUX_11_DC_0[DIV]
8 1...8 TRACE_CLK
2
1
0
MC_CGM_MUX11
RTC
0
1 RTC_CLK
2
3
RTC.RTCC[CLKSEL]
JTAG_TCK/SWD_CLK TCK
Figure 55. MCXE317 clock system diagram

---

*Page 576*

Clocking

#### 23.3.7 MCXE315/MCXE316 clock system diagram

MCXE31B
MC_CGM MUX_0_DC_0[DIV] CMU_FC_3
1...8 CORE_CLK
MUX_0_DC_1[DIV] CMU_FC_4
1...8 AIPS_PLAT_CLK
MUX_0_DC_2[DIV]
1...16 AIPS_SLOW_CLK
8 MUX_0_DC_3[DIV] CMU_FC_5
PCFS 1...8 HSE_CLK
0 MUX_0_DC_4[DIV]
MC_CGM_MUX0
1...8 DCM_CLK
CMU with FXOSC reference
CMU with FIRC reference
22 MUX_1_DC_0[DIV]
2 1...2 STM0_CLK
0
PLLDIG.PLLODIV_0[DIV]
PLLDIG.PLLDV[ODIV2] PLL_PHI0_CLK
1...16
VCO_CLK PLLODIV2_CLK
PLL 1...63
1...16
PLL_PHI1_CLK 22 MUX_3_DC_0[DIV]
PLLDIG.PLLODIV_1[DIV] 2 1...4 FLEXCAN[0:2]_PE_CLK
0
MC_CGM_MUX3
3
FIRC ÷16 2 FIRC_CLK
(48 MHz) ÷2 1
÷2 0 CMU_FM_1
ELE_HSEB.CONFIG_REG_GPR[FIRC_DIV_SEL] 23
MUX_5_DC_0[DIV]
1...8 CLKOUT_STANDBY
2
1
0
EXTAL MC_CGM_MUX5 FXOSC FXOSC_CLK
(8-40 MHz)
XTAL 25
CMU_FC_0 24
23
22
MUX_6_DC_0[DIV]
9
19 1...64 CLKOUT_RUN
16
8
2
1
0
MC_CGM_MUX6
SIRC SIRC_CLK
(32 kHz)
CMU_FM_2
MC_CGM_MUX10
9 MUX_11_DC_0[DIV]
8 1...8 TRACE_CLK
2
1
0
MC_CGM_MUX11
RTC
1 1
2 2 RTC_CLK
3 3
RTC.RTCC[CLKSEL]
JTAG_TCK/SWD_CLK TCK
Figure 56. MCXE315/MCXE316 clock system diagram

---

*Page 577*

Clocking

#### 23.4 Clock sources

#### 23.4.1 Introduction

The chip contains the following clock sourcing modules:
• FIRC
— FIRC_CLK is the default system clock source.
• SIRC
• PLL
• FXOSC
• SXOSC (not available on the MCXE315/MCXE316)
The following list shows some of the clock system features:
[3]
• All clock sources support software configurability for enabling or disabling.
• All clock sources, except SXOSC_CLK, are initialized to their default state on functional reset.
• The SXOSC_CLK supports RTC applications across functional reset and is reset on destructive reset.
Only SIRC_CLK and FIRC_CLK are enabled out of reset and are enabled on any functional reset. The other clock sources are
disabled on reset.
23.4.1.1 Chip clock sources
Table 100. Chip clock sources
Default
Clock source Divider Reset Uses
state
• Boot clock
POR (enabled on functional and
destructive reset) • Default system clock source
FIRC_CLK POR assertion - FIRC_CLK • Safe clock for safety modules FCCU
1, 2, 16 On
(48 MHz) disabled asynchronously and FOSU
POR deassertion - • SIUL2 filter clock
FIRC_CLK enabled
• MC_RGM clock source
• Optional system clock source
PLL_PHI n _CLK Functional (disabled on functional
1...16 Off
• Communication modules (FlexCAN,
(640-1280 MHz) reset)
EMAC, QuadSPI, and so on)
• Reference clock source for PLL
FXOSC_CLK Functional (disabled on functional
— Off
• Communication modules (FlexCAN,
(8-40 MHz) reset)
EMAC, QuadSPI, and so on)
• RTC source for operation across
SXOSC_CLK Destructive (disabled on
— Off functional reset (SXOSC_CLK is not
(32.768 KHz) destructive reset)
available on the MCXE315/MCXE316)
Table continues on the next page...
[3] FIRC_CLK and SIRC_CLK cannot be disabled during Run mode.

---

*Page 578*

Clocking
Table 100. Chip clock sources (continued)
Default
Clock source Divider Reset Uses
state
• Safe clock along with FIRC_CLK
SIRC_CLK
— On POR (enabled on functional reset) • SWT clock source
(32 KHz)
• POR_WDG source clock

#### 23.4.2 Chip input clocks

Table 101. Chip input clocks
Pin Description
XTAL
FXOSC crystal pins
EXTAL
OSC32K_XTAL
SXOSC crystal pins (not available on the MCXE315/MCXE316)
OSC32K_EXTAL
EMAC_MII_RMII_TX_CLK EMAC transmitter clock/EMAC RMII clock (not availalble on the MCXE315/MCXE316 or
MCXE317)
EMAC_MII_RX_CLK EMAC receiver clock (not availalble on the MCXE315/MCXE316 or MCXE317)
JTAG_TCLK/SWD_CLK JTAG/SWD clock
SAI n _MCLK SAI_ n clock in slave mode (not availalble on the MCXE315/MCXE316 or MCXE317)
SAI n _BCLK SAI_ n bit clock in slave mode (not availalble on the MCXE315/MCXE316 or MCXE317)
LPSPI n _SCK LPSPI n serial clock in slave mode
LPI2C n _SCL LPI2C_ n clock
LPI2C n _SCLS LPI2C_ n secondary clock

#### 23.4.3 Chip output clocks

Table 102. Chip output clocks
Pin Description
CLKOUT_RUN Available during Run mode, unavailable during Standby mode
CLKOUT_STANDBY Available during both Run and Standby modes
LPSPI n _SCK LPSPI n serial clock in master mode
LPI2C n _SCL LPI2C_ n clock
LPI2C n _SCLS LPI2C_ n secondary clock
EMAC_MII_RMII_MDC EMAC clock for control data transfer to PHY (not availalble on MCXE315/MCXE316 or
MCXE317)
EMAC_MII_RMII_TX_CLK EMAC transmit clock (not availalble on MCXE315/MCXE316 or MCXE317)
Table continues on the next page...

---

*Page 579*

Clocking
Table 102. Chip output clocks (continued)
Pin Description
TRACE_ETM_CLKOUT ETM trace clock (not availalble on MCXE315/MCXE316, MCXE317)
SAI n _BCLK SAI_ n bit clock in master mode (not availalble on MCXE315/MCXE316 or MCXE317)
QuadSPI_SCKFA QuadSPI serial clock for serial flash device A (fast) (not availalble on MCXE315/
MCXE316 or MCXE317)

#### 23.4.4 Fast internal RC oscillator (FIRC)

The chip has an FIRC with the following features:
• Acts as the system clock source on power-up and after any reset event.
— Important to detect FIRC_CLK failure and recovery
• Acts as the chip's safe clock for safety-relevant applications.
• Is always enabled in Run mode and can be optionally enabled in Standby mode.
• Used as clock source for the following:
— MC_RGM
— FCCU and FOSU
— SIUL2 filters
23.4.4.1 FIRC failure detection
The FIRC_CLK is the safe clock source used as the FCCU and FOSU clock source. The chip supports FIRC_CLK failure detection
and recovery by the mechanisms described in the following cases:
• Case 1 - FIRC_CLK not used as system clock and goes out of range:
— CMU_FM_1 continuously measures the FIRC_CLK clock frequency using FXOSC_CLK as the reference. On each
metering window completion, CMU_FM_1 asserts an interrupt (if configured, CMU_FM_1.IER[FMCIE] is 1). You store
a configured reference clock count CMU_FM_1.RCCR[REF_CNT] (for example, number of FXOSC_CLK cycles in
metering window). Your software checks the FIRC_CLK clock counts by reading CMU_FM_1.SR[MET_CNT].
— In the event FIRC_CLK goes out of range, the software detects the frequency variation after the subsequent metering
window by checking CMU_FM_1.SR[MET_CNT] and takes necessary action by either of the recommended options:
◦ SBC -driven power cycle: The SBC receives indication from the chip (through GPIO toggle, QuadSPI
communication, and so on). The SBC initiates a power cycle sequence.
◦ Software-driven functional reset: The chip executes a functional reset as configured by your software.
• Case 2 - FIRC_CLK not used as system clock and fails (becomes stuck):
— CMU_FM_1 continuously measures the FIRC_CLK frequency with FXOSC_CLK as the reference. Application software
must check FIRC_CLK after a defined time limit by reading CMU_FM_1.SR[MET_CNT] and CMU_FM_1.SR[FMTO].
— If there is an FIRC_CLK failure, the CMU_FM_1 writes a 1 to the timeout status flag CMU_FM_1.SR[FMTO].
— When CMU_FM_1.SR[FMTO] is 1, application software takes necessary action by an SBC-driven power cycle wherein
the chip provides an indication to the SBC (through GPIO toggle, QuadSPI communication, and so on). The SBC then
initiates a power cycle sequence.
• Case 3 - FIRC_CLK used as system clock and goes out of range or fails (becomes stuck):
— CMU_FC_3, CMU_FC_4, and CMU_FC_5 continuously monitor the system clock nodes with FXOSC_CLK as reference
for FLL or FHH events.

---

*Page 580*

Clocking
— In the event of FIRC_CLK failure (when FIRC_CLK acts as the system clock source), these CMUs report the FLL event,
acting as a destructive reset source.
— The system then undergoes the reset sequence, during which the FIRC_CLK is reinitialized.
— The MC_RGM.DES fields indicate the source of the reset event.
— In addition, CMU_FM_1 monitors FIRC_CLK. If the software is not able to service the CMU_FM_1 interrupt before
POR_WDG timeout, POR_WDG treats this as a critical FIRC_CLK failure and initiates a POR_WDG recovery.
Therefore, you must ensure that, if enabled, the CMU_FM_1 interrupt must be serviced within the POR_WDG
timeout duration.
23.4.4.2 FIRC_CLK behavior in Standby mode
FIRC_CLK can be optionally enabled in Standby mode by configuring FIRC.STDBY_ENABLE[STDBY_EN].
When the PMC acknowledges the Standby mode entry, the FIRC_CLK switches from the On state to the Standby mode
configuration selected by the FIRC.STDBY_ENABLE[STDBY_EN] configuration. On wakeup from Standby mode, the FIRC_CLK
configuration switches from the Standby mode configured state to the On state.
NOTE
When the trims are being applied, the FIRC will appear as momentarily disabled as shown in Reset timing diagram
in the Reset overview chapter.

#### 23.4.5 SIRC

The chip has a SIRC having the following features:
• Is always enabled in Run mode and can be optionally enabled in Standby mode. Having the SIRC always enabled
improves system robustness by ensuring that a clock is always available for various SWTs when reducing the chip power
consumption in Standby mode.
• Used as clock source for the following:
— SWT
— POR_WDG
23.4.5.1 SIRC failure detection
Like the FIRC_CLK, the SIRC_CLK is a safe clock for the design. The SIRC_CLK is used as clock source for SWTs and
POR_WDG and therefore it is important to detect SIRC failure and ensure its recovery. The chip supports SIRC_CLK failure
detection and recovery by the mechanism described below.
• Case 1 - SIRC_CLK goes out of range:
— CMU_FM_2 continuously measures the SIRC_CLK clock frequency with FXOSC_CLK as reference. On each
metering window completion, the CMU_FM_2 raises an interrupt. The software checks the SIRC clock counts by
CMU_FM_2.SR[MET_CNT] with respect to the reference clock counts CMU_FM_2.RCCR[REF_CNT].
— In the event of clock going out of range, the software detects the frequency variation after the subsequent metering
window by checking CMU_FM_2.SR[MET_CNT] and takes necessary action by either of the recommended options:
1. SBC-driven power cycle. The chip gives an indication to the SBC (through GPIO toggle, QuadSPI
communication, and so on.). The SBC then initiates a power cycle sequence.
2. SW-driven functional reset. The chip executes a functional reset by software.
• Case 2 - SIRC_CLK fails (becomes stuck):
— CMU_FM_2 continuously measures the SIRC clock frequency with FXOSC_CLK as reference. Software needs to check
this reference after a predefined time by checking CMU_FM_2.SR[MET_CNT] and CMU_FM_2.SR[FMTO].

---

*Page 581*

Clocking
— In the event of SIRC_CLK clock failure, the CMU_FM_2 writes 1 to the timeout status flag field in its status register,
namely, CMU_FM_2.SR[FMTO].
— When CMU_FM_2.SR[FMTO] is 1, the software takes necessary action by either of the recommended options:
1. SBC-driven power cycle: The chip gives an indication to the SBC (through GPIO toggle, QuadSPI
communication, and so on). The SBC then initiates a power cycle sequence.
2. Software-driven functional reset: User software executes a functional reset.
23.4.5.2 SIRC behavior in Standby mode
SIRC can optionally be enabled in Standby mode by configuring SIRC.MISCELLANEOUS_IN[STANDBY_ENABLE].
When PMC acknowledges the Standby mode entry, the SIRC switches from the On state to the standby configuration selected
by SIRC.MISCELLANEOUS_IN[STANDBY_ENABLE] configuration. On wake-up from Standby mode, the SIRC configuration
switches back from the standby-configured state to the On state.
NOTE
When the trims are being applied, the SIRC will appear as momentarily disabled similar to FIRC, as shown in
section Reset timing diagram .

#### 23.4.6 FXOSC

The chip supports an 8–40 MHz fast crystal oscillator which has following features:
• Acts as the reference for PLL.
• Supports crystal input mode and bypass mode if using an external oscillator.
• Acts as a clock source for communication modules:
— FlexCAN
[4]
— QuadSPI
[4]
— EMAC (EMAC_CLK_TS)

#### 23.4.7 SXOSC

The chip supports a slow crystal oscillator (SXOSC) which has the following features (not available on the MCXE315/MCXE316):
• Supports crystal input mode and bypass mode.
• Acts as a clock source for RTC. As SXOSC is not affected by functional reset, it supports RTC operation across functional
reset. SXOSC is only reset on destructive reset.

#### 23.4.8 PLL

The chip contains up to two PLL to provide precision clock source with the following features:
• Optional system clock source (in high performance applications)
• System clock source in safety applications
• Can be used as clock source for communication modules, when configured as system clock source:
— FlexCAN (in SYNC mode operation)
— EMAC (not available on MCXE315/MCXE316 or MCXE317)
— QuadSPI (not available on MCXE315/MCXE316 or MCXE317)
— LPSPI
[4] Not present in MCXE315/MCXE316 and MCXE317.

---

*Page 582*

Clocking
— LPI2C
— FlexIO
— LPUART
• Supports frequency modulation
• Contains lock status monitoring logic which supports loss-of-lock indication
NOTE
See PLL Digital Interface (PLLDIG) for PLL configuration details. See the MCXE31 Data Sheet for PLL limitations.
23.4.8.1 PLL configurations
The PLL output predivider frequency depends on the PLLDIG.PLLDV[RDIV] and PLLDIG.PLLDV[MFI] configurations. The PLL
VCO clock can be divided further by configuring PLLDIG.PLLODIV_0[DIV] for PLL_PHI0_CLK and PLLDIG_PLLODIV_1[DIV] for
PLL_PHI1_CLK (see the PLL Digital Interface (PLLDIG) chapter for configuration details).
23.4.8.1.1 PLL configuration sequence
Before enabling the PLL, you must enable FXOSC_CLK and wait until it is stable. FXOSC.STAT[OSC_STAT] must be monitored
to determine the FXOSC_CLK status.
To disable the PLL, the software must disable PLL first and only then disable FXOSC (if required).

#### 23.4.9 Chip clock outputs

The chip supports two CLKOUT_ x pins for allowing viewing of some internal clocks as follows:
• CLKOUT_STANDBY
— Used for showing clocks available in Run and Standby modes.
• CLKOUT_RUN
— Used for showing only Run mode clocks.
See the Clockout overview section and the Clock Generation Module (MC_CGM) chapter for details on available clocks
and configuration.
NOTE
The CLKOUT_STANDBY registers are latched when the chip enters Standby mode and are reset in Standby mode
sequence. Therefore, the CLKOUT_STANDBY signal needs to be reconfigured on Standby mode exit.
NOTE
CLKOUT_STANDBY is available on two pads GPIO[12] and GPIO[138] but CLKOUT across functional reset and
standby is supported only on GPIO[12] and OBE(output buffer enable) is controlled by DCM GPR bit. Please refer
to DCMRWP1[3] bit for detail.

#### 23.5 MC_CGM

The MC_CGM controls the clock functionality of the chip. See the Clock Generation Module (MC_CGM) chapter for details on
MC_CGM clocking controls.

#### 23.5.1 MC_CGM clock multiplexer types

In this chip, CLKOUT_RUN, CLKOUT_STANDBY, and TRACE_CLK multiplexers are software-controlled multiplexers. The
rest are hardware-controlled multiplexers (see the Clock Generation Module (MC_CGM) chapter for details on software and
hardware multiplexers).

---

*Page 583*

Clocking

#### 23.5.2 MC_CGM clock multiplexers for MCXE315/MCXE316 and MCXE317

Table 103. MC_CGM clock multiplexers for MCXE315/MCXE316 and MCXE317
1
Register description Source inputs Register Selector
Clock mux Divider output
output
Select Control MUX_0_CSC
FIRC_CLK
—
PLL_PHI0_CLK
Select Status MUX_0_CSS
Divider 0 Control MUX_0_DC_0 CORE_CLK
Clock mux 0 Divider 1 Control MUX_0_DC_1 — AIPS_PLAT_CLK
Divider 2 Control — MUX_0_DC_2 AIPS_SLOW_CLK
Divider 3 Control MUX_0_DC_3 HSE_CLK
Divider 4 Control MUX_0_DC_4 DCM_CLK
Select Control MUX_3_CSC
FIRC_CLK
FXOSC_CLK —
Select Status MUX_3_CSS
AIPS_PLAT_CLK
Clock mux 3 —
FLEXCAN0_PE_CLK
Divider 0 Control — MUX_3_DC_0 FLEXCAN1_PE_CLK
FLEXCAN2_PE_CLK
Select Control MUX_4_CSC
FIRC_CLK
FXOSC_CLK —
Select Status MUX_4_CSS
AIPS_PLAT_CLK
2
Clock mux 4 —
FLEXCAN3_PE_CLK
Divider 0 Control — MUX_4_DC_0 FLEXCAN4_PE_CLK
FLEXCAN5_PE_CLK
Select Control MUX_5_CSC
FIRC_CLK
SIRC_CLK
FXOSC_CLK —
3
Clock mux 5 Select Status MUX_5_CSS —
SXOSC_CLK
AIPS_SLOW_CLK
Divider 0 Control — MUX_5_DC_0 CLKOUT_STANDBY
Select Control MUX_6_CSC
FIRC_CLK
SIRC_CLK
FXOSC_CLK
SXOSC_CLK
PLL_PHI0_CLK
4
Clock mux 6 — —
PLL_PHI1_CLK
Select Status MUX_6_CSS
CORE_CLK
HSE_CLK
AIPS_PLAT_CLK
AIPS_SLOW_CLK
Table continues on the next page...

---

*Page 584*

Clocking
Table 103. MC_CGM clock multiplexers for MCXE315/MCXE316 and MCXE317 (continued)
1
Register description Source inputs Register Selector
Clock mux Divider output
output
EMAC_RMII_TX_CLK
EMAC_RX_CLK
Divider 0 Control — MUX_6_DC_0 CLKOUT_RUN
Select Control MUX_11_CSC
FIRC_CLK
FXOSC_CLK
—
PLL_PHI0_CLK
Clock mux 11 Select Status MUX_11_CSS —
PLL_PHI1_CLK
Divider 0 Control — MUX_11_DC_0 TRACE_CLK
1. The default clock selected for all clock mux selectors is FIRC_CLK (out of reset).
2. Clock mux 4 and FLEXCAN[3:5]_PE_CLK are not available on the MCXE315/MCXE316.
3. SXOSC as source for clock mux 5 is not available on the MCXE315/MCXE316.
4. SXOSC as source for clock mux 6 is not available on the MCXE315/MCXE316.

#### 23.5.3 MC_CGM clock multiplexers (excluding MCXE315/MCXE316 and MCXE317)

Table 104. MC_CGM clock multiplexers (MCXE31B)
1
Register description Source inputs Register Selector
Clock mux Divider output
output
Select Control MUX_0_CSC
FIRC_CLK
—
PLL_PHI0_CLK
Select Status MUX_0_CSS
Divider 0 Control MUX_0_DC_0 CORE_CLK
Divider 1 Control MUX_0_DC_1 AIPS_PLAT_CLK
Clock mux 0 Divider 2 Control MUX_0_DC_2 — AIPS_SLOW_CLK
Divider 3 Control — MUX_0_DC_3 HSE_CLK
Divider 4 Control MUX_0_DC_4 DCM_CLK
Divider 5 Control MUX_0_DC_5 LBIST_CLK
Divider 6 Control MUX_0_DC_6 QSPI_MEM_CLK
Select Control MUX_1_CSC
FIRC_CLK
Clock mux 1 FXOSC_CLK — —
Select Status MUX_1_CSS
AIPS_PLAT_CLK
Select Control MUX_2_CSC
FIRC_CLK
Clock mux 2 FXOSC_CLK — —
Select Status MUX_2_CSS
AIPS_PLAT_CLK
FLEXCAN0_PE_CLK
Clock mux 3 Divider 0 Control — MUX_3_DC_0 — FLEXCAN1_PE_CLK
FLEXCAN2_PE_CLK
Table continues on the next page...

---

*Page 585*

Clocking
Table 104. MC_CGM clock multiplexers (MCXE31B) (continued)
1
Register description Source inputs Register Selector
Clock mux Divider output
output
Select Control MUX_4_CSC
FIRC_CLK
FXOSC_CLK —
Select Status MUX_4_CSS
Clock mux 4 AIPS_PLAT_CLK —
Divider 0 Control — MUX_4_DC_0 FLEXCAN3_PE_CLK
Select Control MUX_5_CSC
FIRC_CLK
SIRC_CLK
FXOSC_CLK —
Clock mux 5 Select Status MUX_5_CSS —
SXOSC_CLK
AIPS_SLOW_CLK
Divider 0 Control — MUX_5_DC_0 CLKOUT_STANDBY
Select Control MUX_6_CSC
FIRC_CLK
SIRC_CLK
FXOSC_CLK
SXOSC_CLK
PLL_PHI0_CLK
PLL_PHI1_CLK
—
Clock mux 6 Select Status MUX_6_CSS —
CORE_CLK
HSE_CLK
AIPS_PLAT_CLK
AIPS_SLOW_CLK
EMAC_RMII_TX_CLK
EMAC_RX_CLK
Divider 0 Control — MUX_6_DC_0 CLKOUT_RUN
Select Control MUX_7_CSC
FIRC_CLK
EMAC_RMII_MII_TX_
—
CLK (pin)
Clock mux 7 Select Status MUX_7_CSS —
EMAC_RX_CLK (pin)
Divider 0 Control — MUX_7_DC_0 EMAC_RX_CLK
Select Control MUX_8_CSC
FIRC_CLK
EMAC_RMII_MII_TX_ —
Clock mux 8 Select Status MUX_8_CSS —
CLK (pin)
Divider 0 Control — MUX_8_DC_0 EMAC_TX_CLK
Select Control MUX_9_CSC
FIRC_CLK
FXOSC_CLK
Clock mux 9 PLL_PHI0_CLK — —
Select Status MUX_9_CSS
EMAC_RMII_TX_CLK
EMAC_RX_CLK
Table continues on the next page...

---

*Page 586*

Clocking
Table 104. MC_CGM clock multiplexers (MCXE31B) (continued)
1
Register description Source inputs Register Selector
Clock mux Divider output
output
Divider 0 Control — MUX_9_DC_0 EMAC_TS_CLK
Select Control MUX_10_CSC
FIRC_CLK
FXOSC_CLK —
Clock mux 10 Select Status MUX_10_CSS —
PLL_PHI1_CLK
Divider 0 Control — MUX_10_DC_0 QSPI_SFCK
Select Control MUX_11_CSC
FIRC_CLK
FXOSC_CLK
—
PLL_PHI0_CLK
Clock mux 11 Select Status MUX_11_CSS —
PLL_PHI1_CLK
Divider 0 Control — MUX_11_DC_0 TRACE_CLK
1. The default clock selected for all clock mux selectors is FIRC_CLK (out of reset).

#### 23.5.4 MC_CGM clock sources mapping

Table 105. MC_CGM clock sources mapping
1
Clock selector index MC_CGM clock source Clock source
0 clk_src_0 FIRC_CLK
1 clk_src_1 SIRC_CLK
2 clk_src_2 FXOSC_CLK
3 Reserved Reserved
2
4 clk_src_4 SXOSC_CLK
5–7 Reserved Reserved
8 clk_src_8
PLL_PHI0_CLK
9 clk_src_9 PLL_PHI1_CLK
10–11 Reserved Reserved
12 clk_src_12 PLL_AUX_PHI0_CLK
13 clk_src_13 PLL_AUX_PHI1_CLK
15 Reserved Reserved
16 clk_src_16 CORE_CLK
17–18 Reserved Reserved
19 clk_src_19 HSE_CLK
20–21 Reserved Reserved
22 clk_src_22 AIPS_PLAT_CLK
23 clk_src_23 AIPS_SLOW_CLK
Table continues on the next page...

---

*Page 587*

Clocking
Table 105. MC_CGM clock sources mapping (continued)
1
Clock selector index MC_CGM clock source Clock source
3
24 clk_src_24 EMAC_RMII_TX_CLK
3
25 clk_src_25 EMAC_RX_CLK
26–49 Reserved Reserved
50 clk_src_50 CLKOUT_RUN
1. All clock selector indexes not shown are reserved.
2. SXOSC_CLK is not available on the MCXE315/MCXE316.
3. EMAC_RMII_TX_CLK and EMAC_RX_CLK are not available on the MCXE315/MCXE316 or MCXE317.

#### 23.6 Peripheral clocking

The module clocking diagrams for the peripherals are shown in the following subsections (see Peripheral clock gating for
peripheral clock gating possibilities).

#### 23.6.1 Module clocking

The following sections show how the chip modules use MODULE_CLK and REG_INTF_CLK to control their functionality.
23.6.1.1 Communication modules
Figure 57 shows the REG_INTF_CLK and MODULE_CLK connections, and Table 106 shows the REG_INTF_CLK and
MODULE_CLK signals used by these modules. Any module diagram that does not explicitly show a REG_INTF_CLK uses the
same source for REG_INTF_CLK as used by MODULE_CLK.
Module
See REG_INTF_CLK
REG_INTF_CLK
and MODULE_CLK

# }

columns in table below MODULE_CLK
Figure 57. Communication module clocks
Table 106. Communication module clocking
Module MODULE_CLK REG_INTF_CLK
LPSPI See LPSPI n clocking .
2
LPI C See LPI2C_ n clocking .
FlexIO See FlexIO clocking .
FlexCAN See FlexCAN_ n clocking .
SAI See SAI_ n clocking .
EMAC See EMAC clocking .
LPUART_[0,8] AIPS_PLAT_CLK AIPS_PLAT_CLK
LPUART_[1:7,9:15] AIPS_SLOW_CLK AIPS_SLOW_CLK
QuadSPI See QuadSPI clocking .

---

*Page 588*

Clocking
23.6.1.1.1 FlexCAN_ n clocking
The figure below shows the FlexCAN_ n clocking.
FlexCAN_[0... n ]
AIPS_PLAT_CLK AIPS_PLAT_CLK MODULE_CLK MODULE_CLK
REG_INTF_CLK REG_INTF_CLK
CAN_CHI_CLK CAN_CHI_CLK
FLEXCAN_n_PE_CLK FLEXCAN_ n _PE_CLK CAN_PE_CLK CAN_PE_CLK
Figure 58. FlexCAN_ n clocking
FlexCAN has the following unique clocks:
• CAN_CHI_CLK—FlexCAN controller host interface clock
• CAN_PE_CLK—FlexCAN protocol engine clock
FlexCAN supports up to an 8 Mbps data rate using a 40 MHz CAN_PE_CLK. With a 16 MHz crystal source, 3.2 Mbps is achievable
for baud rate calculations (see the section Protocol timing in the "CAN (FlexCAN)" chapter for details).
For MCXE317, the maximum data rate supported in FlexCAN_0 instance is 5.7 Mbps.
NOTE
See Feature comparison for details on this module's availability on your chip variant.
23.6.1.1.1.1 FlexCAN timestamp implementation
The following figure shows the FlexCAN timestamping implementation. The related table shows the timestamp sources and
corresponding clock nodes.
TS0_CNT_IN
TS0_CLK TS_CNT_OUT
TS1_CNT_IN
TS1_CLK
TS_OUT_CLK_SEL D Q D Q
DCM.DCMRWF1[0]
clk clk
TS_OUT_CLK
Figure 59. FlexCAN timestamping

---

*Page 589*

Clocking
Table 107. Timestamp sources and clock nodes
Timestamp Module TS clock domain
TS0_CLK EMAC TS0_CLK—EMAC_TS_CLK
TS1_CLK STM0 TS1_CLK—AIPS_PLAT_CLK
TS_OUT_CLK FlexCAN_ n TS_OUT_CLK—EMAC_TS_CLK
NOTE
The timestamp clock (TS_OUT_CLK, EMAC_TS_CLK for MCXE31) must be greater than or equal
to FLEXCAN_ n _PE_CLK. When using STM0 as the timestamp source, the FlexCAN timestamp clock
(TS_OUT_CLK, EMAC_TS_CLK for MCXE31) must be greater than or equal to STM0_CLK.
23.6.1.1.2 LPI2C_ n clocking
The following figure shows LPI2C_ n clocking, and the related table shows the LPI2C SIUL2 configuration.
LPI2C_ n
AIPS_SLOW_CLK MODULE_CLK
REG_INTF_CLK
LPI2C_CLK
Port SIUL2_IMCR b [SSS]
SIUL2_MSCR a [IBE]
SCL
LPI2C n _SCL
SIUL2_MSCR a [OBE]
SIUL2_MSCR a [SSS]
Figure 60. LPI2C clocking
NOTE
See Feature comparison for details on this module's availability on your chip variant.
Table 108. SIUL2 options for LPI2C n _SCL clock
2 2
I C_ n signal I C I/O signal Port MSCR a MSCR fields IMCR b IMCR[SSS]
mode
OBE IBE SSS
Slave 0 1 X 0001b
PTC8 72
Master 1 0 001b X
I2C_0_SCL LPI2C0_SCL 212
Slave 0 1 X 0010b
PTD14 110
Master 1 0 100b X
Table continues on the next page...

---

*Page 590*

Clocking
Table 108. SIUL2 options for LPI2C n_SCL clock (continued)
2 2
I C_ n signal I C I/O signal Port MSCR a MSCR fields IMCR b IMCR[SSS]
mode
OBE IBE SSS
Slave 0 1 X 0011b
1
PTF20 180
Master 1 0 100b X
Slave 0 1 X 0001b
I2C_0_SCL
LPI2C0_SCLS PTB1 33 213
S
Master 1 0 001b X
Slave 0 1 X 0001b
PTC7 71
Master 1 0 011b X
Slave 0 1 X 0110b
PTC15 79
Master 1 0 111b X
Slave 0 1 X 0100b
I2C_1_SCL LPI2C1_SCL PTC28 92 217
Master 1 0 101b X
Slave 0 1 X 0010b
PTD9 105
Master 1 0 010b X
Slave 0 1 X 0101b
2
PTF7 167
Master 1 0 011b X
Slave 0 1 X 0010b
I2C_1_SCL
LPI2C1_SCLS PTC17 81 218
S
Master 1 0 100b X
1. PTF20 pad is not present in MCXE315/MCXE316 and MCXE317
2. PTF7 pad is not present in MCXE315/MCXE316 and MCXE317.
23.6.1.1.3 EMAC clocking

---

*Page 591*

Clocking
MC_CGM EMAC
RMII_CLK
MUX_7_CSC[SELCTL]
SIUL2
MUX_7_DC_0[DIV]
25
EMAC_RX_CLK
24 1...64 RX_CLK
IMCR300[SSS]
0
AIPS_PLAT_CLK CSR_CLK
EMAC_MII_RX_CLK
MUX_8_DC_0[DIV]
24
EMAC_TX_CLK
IMCR296[SSS] 1...64 TX_CLK
0
EMAC_RMII_MII_TX_CLK MUX_8_CSC[SELCTL]
25 MUX_9_DC_0[DIV]
24
EMAC_TS_CLK
PLL_PHI0_CLK 8 1...64 TS_CLK
FXOSC_CLK 2
FIRC_CLK 0
MUX_9_CSC[SELCTL]
Figure 61. EMAC clocking
NOTE
EMAC operates only in Clock options A and B, since the module clock becomes lower than the protocol clock
(RMII/MII clocks) in other modes.
NOTE
See Feature comparison for details on this module's availability on your chip variant.
23.6.1.1.3.1 EMAC RMII clocking
The following table shows the EMAC RMII clocking, and the related table shows the SIUL2 clock signal configuration for RMII.

---

*Page 592*

Clocking
MC_CGM EMAC
RMII_CLK
MUX_7_CSC[SELCTL]
SIUL2
MUX_7_DC_0[DIV]
EMAC_RX_CLK
24 1...64 RX_CLK
IMCR300[SSS]
AIPS_PLAT_CLK CSR_CLK
EMAC_MII_RX_CLK
MUX_8_DC_0[DIV]
24
EMAC_TX_CLK
IMCR296[SSS] 1...64 TX_CLK
EMAC_RMII_MII_TX_CLK MUX_8_CSC[SELCTL]
25 MUX_9_DC_0[DIV]
24
EMAC_TS_CLK
PLL_PHI0_CLK 8 1...64 TS_CLK
FXOSC_CLK 2
FIRC_CLK 0
MUX_9_CSC[SELCTL]
Figure 62. EMAC RMII clocking
Table 109. EMAC RMII clock configuration
SIUL2
Source clock Destination clock Port MSCR a MSCR fields IMCR b IMCR[SSS]
OBE IBE SSS
EMAC_RX_CLK
PTC0 64 0 1 X 0100b
EMAC_TX_CLK
EMAC_TX_CLK
PTD6 102 0 1 X 0010b
EMAC_RX_CLK
EMAC_RMII_MII_TX_CLK 296
EMAC_TX_CLK
PTD11 107 0 1 X 0001b
EMAC_RX_CLK
EMAC_TX_CLK
PTD12 108 0 1 X 0011b
EMAC_RX_CLK
PTC0 64 0 1 X 0100b
EMAC_RMII_MII_TX_CLK
PTD6 102 0 1 X 0010b
(MC_CGM.MUX_9_CSC[S EMAC_TS_CLK 296
PTD11 107 0 1 X 0001b
ELCTL] = 18h)
PTD12 108 0 1 X 0011b
Table continues on the next page...

---

*Page 593*

Clocking
Table 109. EMAC RMII clock configuration (continued)
SIUL2
Source clock Destination clock Port MSCR a MSCR fields IMCR b IMCR[SSS]
OBE IBE SSS
PTC1 65 0 1 X 0011b
EMAC_MII_RX_CLK
(MC_CGM.MUX_9_CSC[S EMAC_TS_CLK PTD5 101 0 1 X 300 0010b
ELCTL] = 19h)
PTD10 106 0 1 X 0001b
NOTE
The EMAC time-stamp clock (EMAC_TS_CLK) can use either of the source clocks show in the table above, but
can also select other clocks as selected in MC_CGM.MUX_9_CSC[SELCTL].
23.6.1.1.3.2 EMAC MII clocking
The following figure shows the EMAC MII clocking, and the related table shows the SIUL2 clock signal configuration for MII.
MC_CGM EMAC
RMII_CLK
MUX_7_CSC[SELCTL]
SIUL2
MUX_7_DC_0[DIV]
25
EMAC_RX_CLK
1...64 RX_CLK
IMCR300[SSS]
AIPS_PLAT_CLK CSR_CLK
EMAC_MII_RX_CLK
MUX_8_DC_0[DIV]
24
EMAC_TX_CLK
IMCR296[SSS] 1...64 TX_CLK
EMAC_RMII_MII_TX_CLK MUX_8_CSC[SELCTL]
25 MUX_9_DC_0[DIV]
24
EMAC_TS_CLK
PLL_PHI0_CLK 8 1...64 TS_CLK
FXOSC_CLK 2
FIRC_CLK 0
MUX_9_CSC[SELCTL]
Figure 63. EMAC MII clocking
Table 110. SIUL2 EMAC MII clock configuration
SIUL2
Source clock Destination Port MSCR a MSCR fields IMCR b IMCR[SSS]
OBE IBE SSS
PTC0 64 0 1 X 0100b
EMAC_RMII_MII_TX_CLK EMAC_TX_CLK 296
PTD6 102 0 1 X 0010b
Table continues on the next page...

---

*Page 594*

Clocking
Table 110. SIUL2 EMAC MII clock configuration (continued)
SIUL2
Source clock Destination Port MSCR a MSCR fields IMCR b IMCR[SSS]
OBE IBE SSS
PTD11 107 0 1 X 0001b
PTD12 108 0 1 X 0011b
PTC1 65 0 1 X 0011b
EMAC_MII_RX_CLK EMAC_RX_CLK PTD5 101 0 1 X 300 0010b
PTD10 106 0 1 X 0001b
PTC0 64 0 1 X 0100b
EMAC_RMII_MII_TX_CLK
PTD6 102 0 1 X 0010b
(MC_CGM.MUX_9_CSC[S EMAC_TS_CLK 296
PTD11 107 0 1 X 0001b
ELCTL] = 18h)
PTD12 108 0 1 X 0011b
PTC1 65 0 1 X 0011b
EMAC_MII_RX_CLK
(MC_CGM.MUX_9_CSC[S EMAC_TS_CLK PTD5 101 0 1 X 300 0010b
ELCTL] = 19h)
PTD10 106 0 1 X 0001b
NOTE
The EMAC time-stamp clock (EMAC_TS_CLK) can use any of the signals shown in the figure above which includes
either EMAC_RX_CLK or EMAC_RMII_MII_TX_CLK source clocks. MC_CGM.MUX_9_CSC[SELCTL] selects the
specific source clock for EMAC_TS_CLK.

---

*Page 595*

Clocking
23.6.1.1.4 LPSPI n clocking
LPSPI0
AIPS_PLAT_CLK AIPS_PLAT_CLK MODULE_CLK
REG_INTF_CLK
Port SIUL2_IMCR b [SSS] CAN_CHI_CLK
SIUL2_MSCR a [IBE]
LPSPI0_SCK SCK
SIUL2_MSCR a [OBE]
SIUL2_MSCR a [SSS]
LPSPI[1...5]
AIPS_SLOW_CLK MODULE_CLK MODULE_CLK
REG_INTF_CLK REG_INTF_CLK
CAN_CHI_CLK CAN_CHI_CLK
Port SIUL2_IMCR b [SSS]
SIUL2_MSCR a [IBE]
LPSPI n _SCK SCK
SIUL2_MSCR a [OBE]
SIUL2_MSCR a [SSS]
Figure 64. LPSPI n clocking
NOTE
See Feature comparison for details on this module's availability on your chip variant.
Table 111. SIUL2 options for LPSPI n SCK
Source Destination Port MSCR a MSCR fields IMCR b IMCR[SSS]
SPI
mode
OBE IBE SSS
Slave 0 1 X 0001b
PTC8 72
LPSPI0_SCK LPSPI0 SCK Master 1 0 0110b 229 X
Slave PTD11 107 0 1 X 0101b
Table continues on the next page...

---

*Page 596*

Clocking
Table 111. SIUL2 options for LPSPI n SCK (continued)
Source Destination Port MSCR a MSCR fields IMCR b IMCR[SSS]
SPI
mode
OBE IBE SSS
Master 1 0 0110b X
Slave 0 1 X 0011b
PTD15 111
Master 1 0 0100b X
Slave 0 1 X 0010b
PTE1 129
Master 1 0 0010b X
Slave 0 1 X 0001b
PTA3 3
Master 1 0 0011b X
Slave 0 1 X 0011b
PTA19 19
Master 1 0 0100b X
LPSPI1_SCK LPSPI1 SCK 238
Slave 0 1 X 0100b
PTA28 28
Master 1 0 0011b X
Slave 0 1 X 0010b
PTB14 46
Master 1 0 0011b X
Slave 0 1 X 0011b
PTB29 61
Master 1 0 0101b X
Slave 0 1 X 0010b
PTC15 79
Master 1 0 0011b X
LPSPI2_SCK LPSPI2 SCK 245
Slave 0 1 X 0001b
PTE15 143
Master 1 0 0011b X
Slave 0 1 X 0100b
PTF0 160
Master 1 0 011b X
Slave 0 1 X 0011b
PTC17 81
Master 1 0 0001b X
Slave 0 1 X 0001b
PTD1 97
Master 1 0 0011b X
LPSPI3_SCK LPSPI3 SCK 252
Slave 0 1 X 0010b
PTE7 135
Master 1 0 0110b X
Slave 0 1 X 0100b
PTF13 173
Master 1 0 0100b X
LPSPI4_SCK LPSPI4 SCK Slave PTB10 42 0 1 X 259 0010b
Table continues on the next page...

---

*Page 597*

Clocking
Table 111. SIUL2 options for LPSPI n SCK (continued)
Source Destination Port MSCR a MSCR fields IMCR b IMCR[SSS]
SPI
mode
OBE IBE SSS
Master 1 0 0001b X
Slave 0 1 X 0001b
PTC27 91
Master 1 0 0111b X
Slave 0 1 X 0011b
PTE22 150
Master 1 0 0110b X
Slave 0 1 X 0010b
PTA3 3
Master 1 0 0111b X
Slave 0 1 X 0001b
LPSPI5_SCK LPSPI5 SCK PTD14 110 266
Master 1 0 0001b X
Slave 0 1 X 0011b
PTD26 122
Master 1 0 0110b X
23.6.1.1.5 LPUART clocking
The following figure shows the LPUART clocking configuration, and the related table shows LPUART use case baud rates.
LPUART_[0,8]
AIPS_PLAT_CLK MODULE_CLK
REG_INTF_CLK
LPUART_[1:7, 9:15]
AIPS_SLOW_CLK MODULE_CLK
REG_INTF_CLK
Figure 65. LPUART clocking
NOTE
See Feature comparison for details on this module's availability on your chip variant.

---

*Page 598*

Clocking
Table 112. LPUART baud rate calculation
OSR SBR[12:0]
Required baud rate LPUART_CLK Calculated baud rate
1
(bps) (MHz) (bps)
8192 40 4 976 8196
8192 80 15 610 8196
8192 48 15 366 8196
115200 48 15 26 115384
115200 40 7 43 116279
19200 80 4 833 19207
19200 40 7 260 19230
38400 40 7 130 38461
1. MODULE_CLK ÷ (LPUART.BAUD[SBR] × (LPUART.BAUD[OSR] + 1))
23.6.1.1.6 FlexIO clocking
The following figure shows the FlexIO clocking interface. The related two tables show the FlexIO baud rate use cases.
FlexIO
AIPS_PLAT_CLK MODULE_CLK
REG_INTF_CLK
CORE_CLK FLEXIO_CLK
Figure 66. FlexIO clocking
NOTE
See Feature comparison for details on this module's availability on your chip variant.
Table 113. FlexIO baud rate calculation (FlexIO.TIMCFG n [TIMDEC] = 101b)
Required baud TIMCMP n [CMP] Theoretical Bit duration Observed baud
FLEXIO_CLK
1
rate baud rate rate
(CORE_CLK)
Hex Decimal
88 MHz 9600 0010h 16 10110.29 101.33 μs 9868
88 MHz 19200 0007h 7 21484.37 47.44 μs 21079
88 MHz 57600 0001h 1 85937.50 11.66 μs 85763
88 MHz 115200 0000h 0 171875.00 5.88 μs 170068
1. Theoretical baud rate = Frequency ÷ (256 × 2 × (TIMCMP n [CMP] + 1))

---

*Page 599*

Clocking
Table 114. FlexIO baud rate calculation (FlexIO.TIMCFG n [TIMDEC] = 100b)
Required baud TIMCMP n [CMP] Theoretical Bit duration Observed baud
FLEXIO_CLK
1
rate baud rate rate
(CORE_CLK)
Hex Decimal
88 MHz 9600 — — — — —
88 MHz 19200 8Eh 142 19230.77 51.88 μs 19275
88 MHz 57600 2Eh 46 58510.64 16.89 μs 59206
88 MHz 115200 16h 22 119565.21 8.44 μs 118483
23.6.1.1.7 QuadSPI clocking
QuadSPI
AIPS_PLAT_CLK MODULE_CLK
REG_INTF_CLK
AHB_CLK
PTD10 QSPI_2xSFIF 2xSFIF_CLK
QSPI_SFCK
QuadSPI_SCKFA 7 SF_IF_X_CLK
SFCK
SIUL2_MSCR106[OBE]
SIUL2_MSCR106[SSS]
QSPI_RAM
QSPI_MEM_CLK QSPI_RAM_CLK
QSPI_TX
QSPI_TX_MEM_CLK
Figure 67. QuadSPI clocking
NOTE
See Feature comparison for details on this module's availability on your chip variant.

---

*Page 600*

Clocking
Table 115. QuadSPI clocking
Clock option AIPS_PLAT_CLK QSPI_SFCK QSPI_MEM_CLK
See Option A - High Performance mode (CORE_CLK @ 160 80 MHz 120 MHz 160 MHz
MHz)
See Option B - Reduced Speed mode (CORE_CLK @ 120 60 MHz 120 MHz or 80 120 MHz
MHz) MHz
See Option F - Operation in 1:1 mode with CORE_CLK and 80 MHz 80 MHz 160 MHz
AXBS_CLK at same speed

---

*Page 601*

Clocking
23.6.1.1.8 SAI_ n clocking
AIPS_SLOW_CLK
FXOSC_CLK
SIUL2
SIUL2_IMCR315[SSS]
SIUL2_MSCR76[IBE]
SAI_0
0001
MODULE_CLK/
SAI0_BCLK REG_INTF_CLK
111 BCLK
SIUL2_MSCR76[OBE]
00
SIUL2_MSCR76[SSS]
01
SIUL2_IMCR320[SSS]
10
SIUL2_MSCR35[IBE] 11
0001
SAI_0. x CR2[MSEL]
SAI0_MCLK
SIUL2_MSCR35[OBE]
SIUL2_MSCR35[SSS]
SIUL2_IMCR263[SSS]
SIUL2_MSCR136[IBE]
0001
SAI_1
SAI1_BCLK
MODULE_CLK/
REG_INTF_CLK
110
SIUL2_MSCR136[OBE]
BCLK
SIUL2_MSCR136[SSS]
00
SIUL2_IMCR324[SSS]
01
SIUL2_MSCR110[IBE]
10
0001
11
SAI1_MCLK
SAI_1. x CR2[MSEL]
SIUL2_MSCR110[OBE]
Figure 68. SAI_ n clocking
NOTE
See Feature comparison for details on this module's availability on your chip variant.

---

*Page 602*

Clocking
Table 116. SIUL2 options for SAI_ n
Source Destination Port MSCR a MSCR fields IMCR b IMCR[SSS]
OBE IBE SSS
0 1 X 0001b
SAI_0. x CR2[MSEL] =
01b
SAI0_MCLK PTB3 35 320
Not
SAI_1. x CR2[MSEL] =
— — X
available
11b
0 1 X 0001b
SAI_0. x CR2[MSEL] =
11b
SAI1_MCLK PTD14 110 324
Not
SAI_1. x CR2[MSEL] =
1 0 X
available
01b
SAI0_BCLK SAI0 BCLK 0 1 X 0001b
PTC12 76 315
SAI0 BCLK SAI0_BCLK 1 0 111b X
SAI1_BCLK SAI1 BCLK 0 1 X 0001b
PTE8 136 322
SAI1 BCLK SAI1_BCLK 1 0 110b X
Internally generated MCLK is not supported on the MCXE31 chip family.
23.6.1.2 System modules
Figure 69 shows the REG_INTF_CLK and MODULE_CLK connections, and Table 117 shows the REG_INTF_CLK and
MODULE_CLK signals used by these modules. Any module diagram that does not explicitly show a REG_INTF_CLK uses the
same source for REG_INTF_CLK as used by MODULE_CLK.
Module
See REG_INTF_CLK
REG_INTF_CLK
and MODULE_CLK

# }

columns in table below MODULE_CLK
Figure 69. System module clocks
Table 117. System module clocking
Module MODULE_CLK REG_INTF_CLK
MSCM AIPS_PLAT_CLK AIPS_PLAT_CLK
MCM AIPS_SLOW_CLK AIPS_SLOW_CLK
SIUL2 See SIUL2 clocking .
VIRT_WRAPPER AIPS_SLOW_CLK AIPS_SLOW_CLK
AXBS CORE_CLK AIPS_PLAT_CLK
Table continues on the next page...

---

*Page 603*

Clocking
Table 117. System module clocking (continued)
Module MODULE_CLK REG_INTF_CLK
DMAMUX CORE_CLK CORE_CLK
eDMA CORE_CLK AIPS_PLAT_CLK
INTM AIPS_PLAT_CLK AIPS_PLAT_CLK
SEMA42 AIPS_PLAT_CLK AIPS_PLAT_CLK
XBIC CORE_CLK AIPS_PLAT_CLK
XRDC CORE_CLK AIPS_PLAT_CLK
23.6.1.2.1 SIUL2 clocking
SIUL2
AIPS_SLOW_CLK MODULE_CLK
REG_INTF_CLK
FIRC_CLK FILTER_CLK
Figure 70. SIUL2 clocking
23.6.1.3 Clocking modules
Figure 71 shows the REG_INTF_CLK and MODULE_CLK connections, and Table 118 shows the REG_INTF_CLK and
MODULE_CLK signals used by these modules. Any module diagram that does not explicitly show a REG_INTF_CLK uses the
same source for REG_INTF_CLK as used by MODULE_CLK.
Module
See REG_INTF_CLK
REG_INTF_CLK
and MODULE_CLK

# }

columns in table below MODULE_CLK
Figure 71. Clocking module clocks
Table 118. Clocking module clocking
Module MODULE_CLK REG_INTF_CLK
FXOSC See FXOSC clocking .
SXOSC See SXOSC clocking .
Table continues on the next page...

---

*Page 604*

Clocking
Table 118. Clocking module clocking (continued)
Module MODULE_CLK REG_INTF_CLK
SIRC See SIRC clocking .
FIRC See FIRC clocking .
PLLDIG See PLLDIG clocking .
MC_CGM — AIPS_SLOW_CLK
23.6.1.3.1 FIRC clocking
FIRC
AIPS_SLOW_CLK REG_INTF_CLK
3
÷16 2
Oscillator FIRC_CLK
÷2 1
÷2 0
ELE_HSEB.CONFIG_REG_GPR[FIRC_DIV_SEL]
Figure 72. FIRC clocking
23.6.1.3.2 SIRC clocking
SIRC
Oscillator SIRC_CLK
AIPS_SLOW_CLK REG_INTF_CLK
Figure 73. SIRC clocking

---

*Page 605*

Clocking
23.6.1.3.3 FXOSC clocking
FXOSC
AIPS_SLOW_CLK REG_INTF_CLK
EXTAL
FXOSC_CLK
XTAL
Figure 74. FXOSC clocking
23.6.1.3.4 SXOSC clocking
SXOSC
AIPS_SLOW_CLK REG_INTF_CLK
OSC32K_EXTAL
SXOSC_CLK
OSC32K_XTAL
Figure 75. SXOSC clocking
23.6.1.3.5 PLLDIG clocking
PLLDIG
Reference
FXOSC_CLK PLL_PHI0_CLK
clock
AIPS_SLOW_CLK REG_INTF_CLK PLL_PHI1_CLK
Figure 76. PLLDIG clocking

---

*Page 606*

Clocking
23.6.1.4 Reset modules
Figure 77 shows the REG_INTF_CLK and MODULE_CLK connections, and Table 119 shows the REG_INTF_CLK and
MODULE_CLK signals used by these modules. Any module diagram that does not explicitly show a REG_INTF_CLK uses the
same source for REG_INTF_CLK as used by MODULE_CLK.
Module
See REG_INTF_CLK
REG_INTF_CLK
and MODULE_CLK

# }

columns in table below MODULE_CLK
Figure 77. Reset module clocks
Table 119. Reset module clocking
Module MODULE_CLK REG_INTF_CLK
MC_RGM FIRC_CLK FIRC_CLK
23.6.1.5 Security modules
Figure 78 shows the REG_INTF_CLK and MODULE_CLK connections, and Table 120 shows the REG_INTF_CLK and
MODULE_CLK signals used by these modules. Any module diagram that does not explicitly show a REG_INTF_CLK uses the
same source for REG_INTF_CLK as used by MODULE_CLK.
Module
See REG_INTF_CLK
REG_INTF_CLK
and MODULE_CLK

# }

columns in table below MODULE_CLK
Figure 78. Security module clocks
Table 120. Security module clocking
Module MODULE_CLK REG_INTF_CLK
ELE_HSEB See ELE_HSEB clocking .
MU_ n AIPS_SLOW_CLK AIPS_SLOW_CLK
DCM DCM_CLK DCM_CLK

---

*Page 607*

Clocking
23.6.1.5.1 ELE_HSEB clocking
ELE_HSEB
HSE_CLK PLL (MODULE_CLK)
DCM_CLK DCF_clk
AIPS_SLOW_CLK ELE_HSEB_IPS
PLL standby
SIRC_CLK Slow 32k clk
TCK TCK
TCK_n
HSE_MU0
MODULE_CLK
REG_INTF_CLK
HSE_MU1
MODULE_CLK
REG_INTF_CLK
Figure 79. ELE_HSEB clocking
23.6.1.6 Power-management modules
Figure 80 shows the REG_INTF_CLK and MODULE_CLK connections, and Table 121 shows the REG_INTF_CLK and
MODULE_CLK signals used by these modules. Any module diagram that does not explicitly show a REG_INTF_CLK uses the
same source for REG_INTF_CLK as used by MODULE_CLK.
Module
See REG_INTF_CLK
REG_INTF_CLK
and MODULE_CLK

# }

columns in table below MODULE_CLK
Figure 80. Power-management module clocks

---

*Page 608*

Clocking
Table 121. Power-management module clocking
Module MODULE_CLK REG_INTF_CLK
PMC AIPS_SLOW_CLK AIPS_SLOW_CLK
MC_ME AIPS_SLOW_CLK AIPS_SLOW_CLK
MC_PCU FIRC_CLK FIRC_CLK
WKPU AIPS_SLOW_CLK AIPS_SLOW_CLK
23.6.1.7 Safety modules
Figure 81 shows the REG_INTF_CLK and MODULE_CLK connections, and Table 122 shows the REG_INTF_CLK and
MODULE_CLK signals used by these modules. Any module diagram that does not explicitly show a REG_INTF_CLK uses the
same source for REG_INTF_CLK as used by MODULE_CLK.
Module
See REG_INTF_CLK
REG_INTF_CLK
and MODULE_CLK

# }

columns in table below MODULE_CLK
Figure 81. Safety module clocks
Table 122. Safety module clocking
Module MODULE_CLK REG_INTF_CLK
EIM AIPS_PLAT_CLK AIPS_PLAT_CLK
ERM See ERM clocking .
FCCU See FCCU clocking .
STCU2 See STCU2 clocking .
REG_PROT AIPS_SLOW_CLK AIPS_SLOW_CLK
CMU_FC AIPS_SLOW_CLK AIPS_SLOW_CLK
CMU_FM AIPS_SLOW_CLK AIPS_SLOW_CLK
CRC AIPS_PLAT_CLK AIPS_PLAT_CLK

---

*Page 609*

Clocking
23.6.1.7.1 FCCU clocking
FCCU
AIPS_PLAT_CLK MODULE_CLK
REG_INTF_CLK
FIRC_CLK SAFE_CLK
Figure 82. FCCU clocking
23.6.1.7.2 STCU2 clocking
STCU2
AIPS_SLOW_CLK CORE_CLK
REG_INTF_CLK
JTAG_TCK SHIFT_CLK
Figure 83. STCU2 clocking
23.6.1.7.3 ERM clocking
ERM
AIPS_PLAT_CLK MODULE_CLK
CORE_CLK MEM_CLK[ n ]
Figure 84. ERM clocking
NOTE
MEM_CLK[20:23] are not used. Source clock for MEM_CLK[0:19] is CORE_CLK.

---

*Page 610*

Clocking
23.6.1.8 ADC and motor control modules
Figure 85 shows the REG_INTF_CLK and MODULE_CLK connections, and Table 123 shows the REG_INTF_CLK and
MODULE_CLK signals used by these modules. Any module diagram that does not explicitly show a REG_INTF_CLK uses the
same source for REG_INTF_CLK as used by MODULE_CLK.
Module
See REG_INTF_CLK
REG_INTF_CLK
and MODULE_CLK

# }

columns in table below MODULE_CLK
Figure 85. Motor control module clocks
Table 123. Motor control module clocking
Module MODULE_CLK REG_INTF_CLK
ADC See ADC_ n clocking .
LCU CORE_CLK
eMIOS See eMIOS_ n clocking .
BCTU See BCTU clocking .
TRGMUX AIPS_SLOW_CLK
TSPC AIPS_SLOW_CLK
23.6.1.8.1 ADC_ n clocking
The following figure shows ADC_ n clocking configuration.
ADC_ n
REG_INTF_CLK
MODULE_CLK
CORE_CLK
00
÷2 01 AD_CLK
÷4 10
Available in
÷8 11
MCXE315/MCXE316
only
ADC.MCR[ADCLKSEL]
Figure 86. ADC_ n clocking
NOTE
See Feature comparison for details on this module's availability on your chip variant.
The prescaler can be bypassed when using FIRC_CLK as the source (see MC_CGM mux 0 clocks for FIRC_CLK use details).
The prescaler must be controlled such that the AD_CLK frequency is less than or equal to 120 MHz for MCXE317/MCXE315/
MCXE316. For other MCXE31 products the frequency is 80 MHz.

---

*Page 611*

Clocking
The minimum operating speed of AD_CLK is 6 MHz using the following configuration:
1. Use FIRC_CLK (48 MHz) as clock source (MC_CGM.MUX_0_CSC[SELCTL] equal to 0000b).
2. Divide FIRC_CLK by 2 for CORE_CLK speed (MC_CGM.MUX_0_DC_0[DIV] equal to 1 (FIRC_CLK divide by 2 = 24 MHz)).
3. Write ADC.MCR[ADCCLKSEL] equal to 10b to divide the FIRC_CLK further by 4 (AD_CLK = 6 MHz).
However, at this lower speed, the ADC_ n results will be degraded.
23.6.1.8.2 eMIOS_ n clocking
eMIOS_ n
CORE_CLK MODULE_CLK
REG_INTF_CLK
EMIOS_CLK
EMIOS_CH[0:23]_CLK
Figure 87. eMIOS_ n clocking
NOTE
See Feature comparison for details on this module's availability on your chip variant.
23.6.1.8.3 BCTU clocking
BCTU
CORE_CLK MODULE_CLK
REG_INTF_CLK
BCTU_CLK
Figure 88. BCTU clocking
23.6.1.9 Timer modules
Figure 89 shows the REG_INTF_CLK and MODULE_CLK connections, and Table 124 shows the REG_INTF_CLK and
MODULE_CLK signals used by these modules. Any module diagram that does not explicitly show a REG_INTF_CLK uses the
same source for REG_INTF_CLK as used by MODULE_CLK.

---

*Page 612*

Clocking
Module
See REG_INTF_CLK
REG_INTF_CLK
and MODULE_CLK

# }

columns in table below MODULE_CLK
Figure 89. Timer module clocks
Table 124. Timer module clocking
Module MODULE_CLK REG_INTF_CLK
PIT See PIT_ n clocking .
SWT See SWT_ n clocking .
STM_ n STM0_CLK STM0_CLK
RTC See RTC clocking .
23.6.1.9.1 PIT_ n clocking
The following figure shows the PIT_ n clocking configuration. The related tables show the use case configuration.

---

*Page 613*

Clocking
PIT_0
AIPS_SLOW_CLK MODULE_CLK
Timer 0
REG_INTF_CLK
PIT_CLK
Timer 1
Timer 2
Timer 3
RTI_CLK
SIRC_CLK RTI
PIT_[1,2]
AIPS_SLOW_CLK MODULE_CLK
Timer 0
REG_INTF_CLK
PIT_CLK
Timer 1
Timer 2
Timer 3
Figure 90. PIT_ n clocking
NOTE
See Feature comparison for details on this module's availability on your chip variant.
Table 125. PIT_0 modes of operation
MC_ME.PRTN0_COFB1_C PIT_0.MCR[MDI PIT_0.MCR[MDI Mode Application
LKEN[REQ44] S] S_RTI]
0 X X PIT_0 clock gated Module clock gated and
(minimum power) unused / Standby mode,
PIT and RTI unused
1 0 0 Both PIT and RTI enabled Run mode
1 0 1 PIT running, RTI disabled Run mode with only PIT
active
1 1 0 PIT disabled, RTI enabled Standby mode with RTI
enabled
1 1 1 Both PIT and RTI disabled Standby mode with RTI
disabled

---

*Page 614*

Clocking
Table 126. PIT_[1,2] modes of operation
MC_ME.PRTN n _C PIT_[1,2].MCR[MD Mode Application
OFB1_CLKEN[RE IS]
1
Q[45,63]]
0 X PIT_[1,2] clock gated (minimum power) Module clock gated and unused,
Standby mode
1 0 PIT_[1,2] enabled Run mode
1 0 PIT_[1,2] running Run mode with PIT active
1 1 PIT_[1,2] disabled Standby mode
1. PIT_1 and PIT_2 MC_ME partition registers used are:
• PIT_1 - MC_ME.PRTN0_COFB1_CLKEN[REQ45]
• PIT_2 - MC_ME.PRTN1_COFB1_CLKEN[REQ63]
23.6.1.9.2 SWT_ n clocking
SWT_ n
AIPS_SLOW_CLK MODULE_CLK
SIRC_CLK COUNTER_CLK
Figure 91. SWT_ n clocking
NOTE
See Feature comparison for details on this module's availability on your chip variant.

---

*Page 615*

Clocking
23.6.1.9.3 RTC clocking
RTC
AIPS_SLOW_CLK MODULE_CLK/REG_INTF_CLK
RTC.RTCC[SELCTL]
Not available on:
Clock source 0
- MCXE315/MCXE316 SXOSC_CLK 0 RTC.RTCC[DIV512EN]
Clock source 1
SIRC_CLK 1 RTC.RTCC[CNTEN]
0
Clock source 2
FIRC_CLK 2 0 RTC_CLK
÷512 1 32-bit counter
Clock source 3
FXOSC_CLK ÷32 1 3
To other modules
RTC.RTCC[DIV32EN]
Figure 92. RTC clocking
NOTE
The RTC is available in Standby mode. Although bus clock is gated, the RTC can run on FIRC_CLK, SIRC_CLK,
FXOSC_CLK, or SXOSC_CLK.
23.6.1.10 Debug modules
Figure 93 shows the REG_INTF_CLK and MODULE_CLK connections, and Table 127 shows the REG_INTF_CLK and
MODULE_CLK signals used by these modules. Any module diagram that does not explicitly show a REG_INTF_CLK uses the
same source for REG_INTF_CLK as used by MODULE_CLK.
Module
See REG_INTF_CLK
REG_INTF_CLK
and MODULE_CLK

# }

columns in table below MODULE_CLK
Figure 93. Debug module clocks
Table 127. Debug module clocking
Module MODULE_CLK REG_INTF_CLK
JTAGC See JTAGC clocking .
JDC See JDC clocking .

---

*Page 616*

Clocking
23.6.1.10.1 JTAGC clocking
JTAGC
JTAG_TCK/SWD_CLK TCK_P
TCK_N
Figure 94. JTAGC clocking
23.6.1.10.2 JDC clocking
JDC
AIPS_SLOW_CLK MODULE_CLK
REG_INTF_CLK
JTAG_TCK/SWD_CLK TCK_P
TCK_N
Figure 95. JDC clocking
23.6.1.11 Analog modules
Figure 96 shows the REG_INTF_CLK and MODULE_CLK connections, and Table 128 shows the REG_INTF_CLK and
MODULE_CLK signals used by these modules. Any module diagram that does not explicitly show a REG_INTF_CLK uses the
same source for REG_INTF_CLK as used by MODULE_CLK.
Module
See REG_INTF_CLK
REG_INTF_CLK
and MODULE_CLK

# }

columns in table below MODULE_CLK
Figure 96. Analog module clocks
Table 128. Analog module clocking
Module MODULE_CLK REG_INTF_CLK
LPCMP See LPCMP_ n clocking .
Temperature Sensor AIPS_SLOW_CLK AIPS_SLOW_CLK

---

*Page 617*

Clocking
23.6.1.11.1 LPCMP_ n clocking
DAC
AIPS_SLOW_CLK MODULE_CLK
REG_INTF_CLK
LPCMP_ n
AIPS_SLOW_CLK MODULE_CLK
REG_INTF_CLK
RTC_CLK ROUND_ROBIN_CLK
Figure 97. LPCMP_ n clocking
NOTE
See Feature comparison for details on this module's availability on your chip variant.
23.6.1.12 Memory modules
Figure 98 shows the REG_INTF_CLK and MODULE_CLK connections, and Table 129 shows the REG_INTF_CLK and
MODULE_CLK signals used by these modules. Any module diagram that does not explicitly show a REG_INTF_CLK uses the
same source for REG_INTF_CLK as used by MODULE_CLK.
Module
See REG_INTF_CLK
REG_INTF_CLK
and MODULE_CLK

# }

columns in table below MODULE_CLK
Figure 98. Memory module clocks

---

*Page 618*

Clocking
Table 129. Memory module clocking
Module MODULE_CLK REG_INTF_CLK
PFLASH/ FLASH CORE_CLK AIPS_SLOW_CLK
PRAM/ SRAM CORE_CLK AIPS_SLOW_CLK

#### 23.6.2 Peripheral data rates

Table 130. Peripheral data rates
Peripheral Maximum data rate
MCXE31B MCXE315/MCXE316 and MCXE317
ADC See the MCXE31 Data Sheet for details. See the MCXE31 Data Sheet for details.
1
eMIOS Able to shift PWM edge by 1 ÷ (160 MHz) = 6.25 ns. Able to shift PWM edge by 1 ÷ (120 MHz) = 8.33 ns.
2
BCTU BCTU to generate triggers at 160 MHz. BCTU to generate triggers at 120 MHz.
LCU Same domain as EMIOS and BCTU. Same domain as EMIOS and BCTU.
• Flash memory interface: SDR 120 MHz • Flash memory interface: SDR 120 MHz
QuadSPI
• DDR and hyperflash not supported • DDR and hyperflash not supported
See the section EMAC instance and configuration in See the section EMAC instance and configuration in
EMAC the "Ethernet Media Access Controller (EMAC)" chapter the "Ethernet Media Access Controller (EMAC)" chapter
for details. for details.
3
FlexCAN 8 Mbps 8 Mbps
4
LPI2C 400 Kbps in fast mode. 400 Kbps in fast mode.
• LPSPI0 is to have a high clock rate of 20 Mbps • LPSPI0 is to have a high clock rate of 15 Mbps
5
LPSPI
• LPSPI1–LPSPI5 can be 10 Mbps • LPSPI1–LPSPI3 can be 7.5 Mbps
• Bit rate = 12.288 MHz (12.288 Mbps—bit clock • Bit rate = 12.288 MHz (12.288 Mbps—bit clock
frequency governs the bit rate) frequency governs the bit rate)
SAI0/SAI1
• Master clock = 24.576 MHz • Master clock = 24.576 MHz
6
(I2S)
• SAI0 and SAI1 operate asynchronously to • SAI0 and SAI1 operate asynchronously to
each other each other
See the section Baud rate generation in the "Low See the section Baud rate generation in the "Low
LPUART Power Universal Asynchronous Receiver/ Transmitter Power Universal Asynchronous Receiver/ Transmitter
(LPUART)" chapter and Table 112 for details (LPUART)" chapter and Table 112 for details
7
FlexIO
The different protocol data rates supported by FlexIO The different protocol data rates supported by FlexIO
are listed below. For master mode, max baud rate is are listed below. For master mode, max baud rate is
FLEXIO_CLK ÷ 4. For slave mode, max baud rate FLEXIO_CLK ÷ 4. For slave mode, max baud rate
is FLEXIO_CLK ÷ 6. The baud rate is controlled by is FLEXIO_CLK ÷ 6. The baud rate is controlled by
TIMCMP (lower 8 bits in 8-bit mode and 16 bits in TIMCMP (lower 8 bits in 8-bit mode and 16 bits in
16-bit mode). 16-bit mode).
• UART: 19200 bps • UART: 19200 bps
Table continues on the next page...

---

*Page 619*

Clocking
Table 130. Peripheral data rates (continued)
Peripheral Maximum data rate
MCXE31B MCXE315/MCXE316 and MCXE317
• I2C: 400 Kbps • I2C: 400 Kbps
• SPI: 10 Mbps • SPI: 7.5 Mbps
• I2S: 12.288 Mbps • I2S: 12.288 Mbps
Trace
• Fast-speed pins: 120 MHz • SWO trace
• Medium-speed pins: 48 MHz
1. See section Global clock prescaler (GCP) in the "Enhanced Modular IO Subsystem (eMIOS)" chapter for details.
2. See section Triggers in the "Enhanced Modular IO Subsystem (eMIOS)" chapter.
3. See the section Protocol timing in the "CAN (FlexCAN)" chapter for data rate calculation details.
4. See the section Clocking in the "Low Power Inter-Integrated Circuit (LPI2C)" chapter for LPI2C_CLK frequency details.
5. See the section Clocking in the "Low Power Serial Peripheral Interface (LPSPI)" chapter.
6. See the section Clocking in the "Synchronous Audio Interface (SAI)" chapter and SAI_ n clocking for details.
7. See the section Application information and FlexIO instances and configuration in the "Flexible I/O (FlexIO)" chapter and
FlexIO clocking for baud configuration details.

#### 23.6.3 Core and peripheral clock control

The chip provides provisions for core and peripheral clock gating. The next sections describe the details on clock gating
possibilities and controls (see Power Management Controller (PMC) and Mode Entry Module (MC_ME) for details).
23.6.3.1 Clock gating
Application core clocks are gated by individual MC_ME core clock enable bits. Additionally, application cores can be clock gated
by executing WFI (see the "Mode Entry Module (MC_ME)" chapter for details).
APP DEBUGEN
DAP-PWRUP REQUEST
MDMAPCTL[CM7_ n _CORE_ACCESS] FCLKEN
CLKEN
Cortex-M7_ n CCTL gating
Cortex-M7_ n
HCLKEN
MC_ME_PCTL | (CORE CM7 CCTL & !CORE SLEEPING)
CLKIN
CORE_CLK
Figure 99. Cortex-M7 core clock gating
To support core debug across functional reset, challenge response done (application debug enable) gating is done so that the
debugger can access core debug logic. When the debugger completes its programming with core debug logic, it must program
the MDM_AP DAP control bit to shift the control of CLKIN and FCLK to CCTL.
There are two cases in which other masters can access the TCM of each core:
• When the TCM is used as system memory—HCLK will always remain on if TCM PCTL is 1.

---

*Page 620*

Clocking
• In applications where TCM is not used as system memory, TCM PCTL is written to 0. TCM will then function as the core's
memory and HCLK will be gated on WFI.
See the section "MDM_AP register descriptions" in the Debug Subsystem chapter and the Memory Map chapter for details.
23.6.3.2 Peripheral clock gating
See the tables in section " Peripheral clock gating " for the chip partitions, plus peripheral initialization and shutdown details.

#### 23.7 Clocking details

#### 23.7.1 System clock frequency limitations

Table 131. System clock frequency limitations
System clock node System clock divider Maximum frequency allowed Remarks
• 160 MHz: MCXE31B
CORE_CLK is always greater than or
CORE_CLK MC_CGM.MUX_0_DC_0[DIV]
• 120 MHz: MCXE315/
equal to AIPS_PLAT_CLK.
MCXE316 and MCXE317
AIPS_PLAT_CLK is always less than
AIPS_PLAT_CLK MC_CGM.MUX_0_DC_1[DIV] 80 MHz
or equal to CORE_CLK.
AIPS_SLOW_CLK is always less
AIPS_SLOW_CLK MC_CGM.MUX_0_DC_2[DIV] 40 MHz
than or equal to AIPS_PLAT_CLK.
When CORE_CLK is equal to or
less than 120 MHz, HSE_CLK
can be equal to CORE_CLK.
HSE_CLK MC_CGM.MUX_0_DC_3[DIV] 120 MHz
When CORE_CLK is higher than
120 MHz, HSE_CLK must be half of
the CORE_CLK.
DCM_CLK MC_CGM.MUX_0_DC_4[DIV] 48 MHz DCM_CLK
LBIST_CLK MC_CGM.MUX_0_DC_5[DIV] 48 MHz LBIST clock
QSPI_MEM_CLK is always equal to
CORE_CLK except in 1:1 mode (see
QSPI_MEM_CLK MC_CGM.MUX_0_DC_6[DIV] 160 MHz Option F - Operation in 1:1 mode
with CORE_CLK and AXBS_CLK at
same speed ).
NOTE
The chip supports 1:1 clocking mode, whereby the core(s) are clocked at the same frequency as the slave
ports (flash memory, PRAM controller, AIPS controller). Option F - Operation in 1:1 mode with CORE_CLK and
AXBS_CLK at same speed supports this requirement.
The frequencies in the table above are maximum frequencies for a specific clock. However, any clock frequency
selected must adhere to the same clock divider ratios shown in Clocking use case examples .

#### 23.7.2 Clocking use case examples

The chip supports the clocking modes shown in the subsequent sections, as follows:
• Option A - High Performance mode (CORE_CLK @ 160 MHz) (only available in Run mode)
• Option B - Reduced Speed mode (CORE_CLK @ 120 MHz) (only available in Run mode)

---

*Page 621*

Clocking
• Option C - Boot Standby mode (CORE_CLK @ 24 MHz)
• Option D - Low-Speed RUN mode (CORE_CLK @ 48 MHz)
• Option E - Low-Speed Run mode (CORE_CLK @ 3 MHz)
• Option E2 - Very-Low-Speed Run mode (CORE_CLK @ 750 kHz)
• Option F - Operation in 1:1 mode with CORE_CLK and AXBS_CLK at same speed (only available in Run mode)
The following list are requirements when configuring the clocking system.
• Configure PRAM/SRAM wait states and flash memory read/write wait cycles in clock configurations where CORE_CLK
equals AIPS_PLAT_CLK. See "Option B" in Gasket configurations in various clocking modes for PRAM/SRAM wait states and
gasket configurations for clocking options.
• Enable PLL_PHI n _CLK only if using it as the system clock source.
• Disable PLL_PHI n _CLK when using FIRC_CLK as the system clock, you must.
• Configure the PMC last mile regulator before enabling the PLL. Configure PMC.CONFIG[LMEN] and
PMC.CONFIG[LMBCTLEN] (if using an external BJT) to enable the last mile regulator.
• Disable the PLL before disabling the last mile regulator (see PMC last-mile regulator auto-enable feature (for MCXE31B) for
last mile regulator details).
23.7.2.1 Option A - High Performance mode (CORE_CLK @ 160 MHz)
Table 132. Option A - High Performance mode (CORE_CLK @ 160 MHz)
Clock frequencies
Clocking options
1
MCXE31B
PLL VCO frequency 960 MHz
PLLODIV2_CLK 480 MHz
(PLLDIG.PLLDV[ODIV2]) (02h)
FIRC_CLK
—
(ELE_HSEB.CONFIG_REG_GPR[FIRC_DIV_SEL])
2
PLL_PHI1_CLK-related clocks
PLL_PHI1_CLK 240 MHz 160 MHz
(PLLDIG.PLLODIV_1[DIV]) (0001b) (0010b)
QSPI_SFCK 120 MHz 80 MHz
(MC_CGM.MUX_10_DC_0[DIV]) (0001b) (0001b)
For fast pads
120 MHz 80 MHz
(0001b) (0001b)
TRACE_CLK
(MC_CGM.MUX_11_DC_0[DIV])
For medium pads
48 MHz 40 MHz
(0100b) (011b)
3
PLL_PHI0_CLK-related clocks
Table continues on the next page...

---

*Page 622*

Clocking
Table 132. Option A - High Performance mode (CORE_CLK @ 160 MHz) (continued)
Clock frequencies
Clocking options
1
MCXE31B
PLL_PHI0_CLK 160 MHz
(PLLDIG.PLLODIV_0[DIV]) (0010b)
CORE_CLK
• Application cores
• AXBS
160 MHz
• SRAM
(0000b)
• Flash memory controller port clock
• AIPS0 (high-speed peripheral clock)
(MC_CGM.MUX_0_DC_0[DIV])
QSPI_MEM_CLK 160 MHz
(MC_CGM.MUX_0_DC_6[DIV]) (0000b)
AIPS_PLAT_CLK (medium-speed peripheral clock) 80 MHz
(MC_CGM.MUX_0_DC_1[DIV]) (0001b)
AIPS_SLOW_CLK (slow-speed peripheral clock) 40 MHz
(MC_CGM.MUX_0_DC_2[DIV]) (0011b)
DCM_CLK 40 MHz
(MC_CGM.MUX_0_DC_4[DIV]) (0011b)
HSE_CLK 80 MHz
(MC_CGM.MUX_0_DC_3[DIV]) (0001b)
LBIST_CLK 40 MHz
(MC_CGM.MUX_0_DC_5[DIV]) (0011b)
1. This table does not apply to MCXE315/MCXE316 or MCXE317.
2. MC_CGM.MUX_10_CSC[SELCTL] and MC_CGM.MUX_11_CSC[SELCTL] must equal 1001b.
3. MC_CGM.MUX_0_CSC[SELCTL] must equal 1000b.
23.7.2.2 Option B - Reduced Speed mode (CORE_CLK @ 120 MHz)
Table 133. Option B - Reduced Speed mode (CORE_CLK @ 120 MHz)
Clock frequencies
Clocking options
MCXE31B MCXE315/MCXE316 and MCXE317
PLL VCO frequency 960 MHz 960 MHz
PLLODIV2_CLK 480 MHz 240 MHz
(PLLDIG.PLLDV[ODIV2]) (02h) (04h)
Table continues on the next page...

---

*Page 623*

Clocking
Table 133. Option B - Reduced Speed mode (CORE_CLK @ 120 MHz) (continued)
Clock frequencies
Clocking options
MCXE31B MCXE315/MCXE316 and MCXE317
FIRC_CLK
(ELE_HSEB.CONFIG_REG_GPR[FIRC —
_DIV_SEL])
1 2
PLL_PHI1_CLK-related clocks ,
PLL_PHI1_CLK 240 MHz 160 MHz 48 MHz)
(PLLDIG.PLLODIV_1[DIV]) (0001b) (0010b) (0100b)
QSPI_SFCK 120 MHz 80 MHz
—
(MC_CGM.MUX_10_DC_0[DIV]) (001b) (001b)
For fast pads —
—
120 MHz 80 MHz
(001b) (001b)
TRACE_CLK
(MC_CGM.MUX_11_DC_0[DIV])
For medium pads —
—
48 MHz 40 MHz
(100b) (011b)
3
PLL_PHI0_CLK-related clocks
PLL_PHI0_CLK 120 MHz 120 MHz
(PLLDIG.PLLODIV_0[DIV]) (011b) (001b)
CORE_CLK
• Application cores
• AXBS
120 MHz 120 MHz
• SRAM
(000b) (000b)
• Flash memory controller port clock
• AIPS0 (high-speed peripheral clock)
(MC_CGM.MUX_0_DC_0[DIV])
QSPI_MEM_CLK 120 MHz
—
(MC_CGM.MUX_0_DC_6[DIV]) (000b)
AIPS_PLAT_CLK (medium-speed
60 MHz 60 MHz
peripheral clock)
(001b) (001b)
(MC_CGM.MUX_0_DC_1[DIV])
AIPS_SLOW_CLK (slow-speed
30 MHz 30 MHz
peripheral clock)
(011b) (011b)
(MC_CGM.MUX_0_DC_2[DIV])
Table continues on the next page...

---

*Page 624*

Clocking
Table 133. Option B - Reduced Speed mode (CORE_CLK @ 120 MHz) (continued)
Clock frequencies
Clocking options
MCXE31B MCXE315/MCXE316 and MCXE317
DCM_CLK 30 MHz 30 MHz
(MC_CGM.MUX_0_DC_4[DIV]) (011b) (011b)
HSE_CLK 120 MHz 60 MHz 120 MHz 60 MHz
(MC_CGM.MUX_0_DC_3[DIV]) (000b) (001b) (000b) (001b)
LBIST_CLK 30 MHz 30 MHz
(MC_CGM.MUX_0_DC_5[DIV]) (011b) (011b)
1. MC_CGM.MUX_10_CSC[SELCTL] and MC_CGM.MUX_11_CSC[SELCTL] must equal 1001b.
2. Do not use the combination of different frequencies mentioned below across the 2x2 matrix. The values shown in each cell
are valid and must not be clubbed with the values mentioned in any other cells.
3. MC_CGM.MUX_0_CSC[SELCTL] must equal 1000b.
23.7.2.3 Option C - Boot Standby mode (CORE_CLK @ 24 MHz)
Table 134. Option C - Boot Standby mode (CORE_CLK @ 24 MHz)
Clock frequencies
Clocking options
MCXE31
PLL VCO frequency —
—
PLLODIV2_CLK
(PLLDIG.PLLDV[ODIV2])
1
FIRC_CLK 24 MHz
(ELE_HSEB.CONFIG_REG_GPR[FIRC_DIV_SEL]) (00b)
2
PLL_PHI1_CLK-related clocks
PLL_PHI1_CLK
—
(PLLDIG.PLLODIV_1[DIV])
QSPI_SFCK
—
(MC_CGM.MUX_10_DC_0[DIV])
—
TRACE_CLK
(MC_CGM.MUX_11_DC_0[DIV])
3
PLL_PHI0_CLK-related clocks
PLL_PHI0_CLK
—
(PLLDIG.PLLODIV_0[DIV])
LBIST_CLK
—
(MC_CGM.MUX_0_DC_5[DIV])
Table continues on the next page...

---

*Page 625*

Clocking
Table 134. Option C - Boot Standby mode (CORE_CLK @ 24 MHz) (continued)
Clock frequencies
Clocking options
MCXE31
QSPI_MEM_CLK
—
(MC_CGM.MUX_0_DC_6[DIV])
CORE_CLK
• Application cores
• AXBS
24 MHz
• SRAM
(0000b)
• Flash memory controller port clock
• AIPS0 (high-speed peripheral clock)
(MC_CGM.MUX_0_DC_0[DIV])
AIPS_PLAT_CLK (medium-speed peripheral clock) 24 MHz
(MC_CGM.MUX_0_DC_1[DIV]) (0000b)
AIPS_SLOW_CLK (slow-speed peripheral clock) 12 MHz
(MC_CGM.MUX_0_DC_2[DIV]) (0001b)
DCM_CLK 24 MHz
(MC_CGM.MUX_0_DC_4[DIV]) (0000b)
HSE_CLK 24 MHz
(MC_CGM.MUX_0_DC_3[DIV]) (0000b)
1. The FIRC_DIV_SEL is configured by the sBAF code. It is set to 11b after reset or normal standby exit and FIRC_CLK is 48
MHz. In case of fast standby exit, FIRC_DIV_SEL is 00b and FIRC_CLK is 24 MHz.
2. MC_CGM.MUX_10_CSC[SELCTL] and MC_CGM.MUX_11_CSC[SELCTL] values are don't care since QSPI_SFCK and
TRACE_CLK are not used in this use case.
3. MC_CGM.MUX_0_CSC[SELCTL] must equal 0.
23.7.2.4 Option D - Low-Speed RUN mode (CORE_CLK @ 48 MHz)
Table 135. Option D - Low-Speed RUN mode (CORE_CLK @ 48 MHz)
Clock frequencies
Clocking options
(SYS_CLK = FIRC_CLK)
MCXE31
PLL VCO frequency —
—
PLLODIV2_CLK
(PLLDIG.PLLDV[ODIV2])
FIRC_CLK 48 MHz
(ELE_HSEB.CONFIG_REG_GPR[FIRC_DIV_SEL]) (11b)
1
PLL_PHI1_CLK-related clocks
Table continues on the next page...

---

*Page 626*

Clocking
Table 135. Option D - Low-Speed RUN mode (CORE_CLK @ 48 MHz) (continued)
Clock frequencies
Clocking options
(SYS_CLK = FIRC_CLK)
MCXE31
PLL_PHI1_CLK
—
(PLLDIG.PLLODIV_1[DIV])
QSPI_SFCK
—
(MC_CGM.MUX_10_DC_0[DIV])
—
TRACE_CLK
(MC_CGM.MUX_11_DC_0[DIV])
2
PLL_PHI0_CLK-related clocks
PLL_PHI0_CLK
—
(PLLDIG.PLLODIV_0[DIV])
LBIST_CLK
—
(MC_CGM.MUX_0_DC_5[DIV])
QSPI_MEM_CLK
—
(MC_CGM.MUX_0_DC_6[DIV])
CORE_CLK
• Application cores
• AXBS
48 MHz
• SRAM
(0000b)
• Flash memory controller port clock
• AIPS0 (high-speed peripheral clock)
(MC_CGM.MUX_0_DC_0[DIV])
AIPS_PLAT_CLK (medium-speed peripheral clock) 48 MHz
(MC_CGM.MUX_0_DC_1[DIV]) (0000b)
AIPS_SLOW_CLK (slow-speed peripheral clock) 24 MHz
(MC_CGM.MUX_0_DC_2[DIV]) (0001b)
DCM_CLK 48 MHz
(MC_CGM.MUX_0_DC_4[DIV]) (0000b)
HSE_CLK 48 MHz
(MC_CGM.MUX_0_DC_3[DIV]) (0000b)
1. MC_CGM.MUX_10_CSC[SELCTL] and MC_CGM.MUX_11_CSC[SELCTL] values are don't care since QSPI_SFCK and
TRACE_CLK are not used in this use case.
2. MC_CGM.MUX_0_CSC[SELCTL] must equal 0.

---

*Page 627*

Clocking
23.7.2.5 Option E - Low-Speed Run mode (CORE_CLK @ 3 MHz)
Table 136. Option E - Low-Speed Run mode (CORE_CLK @ 3 MHz)
Clock frequencies
Clocking options
MCXE31
PLL VCO frequency —
—
PLLODIV2_CLK
(PLLDIG.PLLDV[ODIV2])
FIRC_CLK 3 MHz
(ELE_HSEB.CONFIG_REG_GPR[FIRC_DIV_SEL]) (10b)
1
PLL_PHI1_CLK-related clocks
PLL_PHI1_CLK
—
(PLLDIG.PLLODIV_1[DIV])
QSPI_SFCK
—
(MC_CGM.MUX_10_DC_0[DIV])
—
TRACE_CLK
(MC_CGM.MUX_11_DC_0[DIV])
2
PLL_PHI0_CLK-related clocks
PLL_PHI0_CLK
—
(PLLDIG.PLLODIV_0[DIV])
LBIST_CLK
—
(MC_CGM.MUX_0_DC_5[DIV])
QSPI_MEM_CLK
—
(MC_CGM.MUX_0_DC_6[DIV])
CORE_CLK
• Application cores
• AXBS
3 MHz
• SRAM
(0000b)
• Flash memory controller port clock
• AIPS0 (high-speed peripheral clock)
(MC_CGM.MUX_0_DC_0[DIV])
AIPS_PLAT_CLK (medium-speed peripheral clock) 3 MHz
(MC_CGM.MUX_0_DC_1[DIV]) (0000b)
AIPS_SLOW_CLK (slow-speed peripheral clock) 1.5 MHz
(MC_CGM.MUX_0_DC_2[DIV]) (0001b)
Table continues on the next page...

---

*Page 628*

Clocking
Table 136. Option E - Low-Speed Run mode (CORE_CLK @ 3 MHz) (continued)
Clock frequencies
Clocking options
MCXE31
DCM_CLK 3 MHz
(MC_CGM.MUX_0_DC_4[DIV]) (0000b)
HSE_CLK 3 MHz
(MC_CGM.MUX_0_DC_3[DIV]) (0000b)
1. MC_CGM.MUX_10_CSC[SELCTL] and MC_CGM.MUX_11_CSC[SELCTL] values are don't care since QSPI_SFCK and
TRACE_CLK are not used in this use case.
2. MC_CGM.MUX_0_CSC[SELCTL] must equal 0.
NOTE
For FIRC_CLK frequency modes less than 24 MHz, safety modules like the CMU_F x _ n must be disabled for safety
applications, because safety applications are to run on the PLL clocks. The CMU_F x _ n will cause erroneous FHH
events if not disabled.
23.7.2.6 Option E2 - Very-Low-Speed Run mode (CORE_CLK @ 750 kHz)
Table 137. Option E2 - Very-Low-Speed Run mode (CORE_CLK @ 750 kHz)
Clock frequencies
Clocking options
(SYS_CLK = (FIRC_CLK ÷ 8) ÷ 8)
MCXE31
PLL VCO frequency —
—
PLLODIV2_CLK
(PLLDIG.PLLDV[ODIV2])
FIRC_CLK 3 MHz
(ELE_HSEB.CONFIG_REG_GPR[FIRC_DIV_SEL]) (10b)
1
PLL_PHI1_CLK-related clocks
PLL_PHI1_CLK
—
(PLLDIG.PLLODIV_1[DIV])
QSPI_SFCK
—
(MC_CGM.MUX_10_DC_0[DIV])
—
TRACE_CLK
(MC_CGM.MUX_11_DC_0[DIV])
2
PLL_PHI0_CLK-related clocks
PLL_PHI0_CLK
—
(PLLDIG.PLLODIV_0[DIV])
Table continues on the next page...

---

*Page 629*

Clocking
Table 137. Option E2 - Very-Low-Speed Run mode (CORE_CLK @ 750 kHz) (continued)
Clock frequencies
Clocking options
(SYS_CLK = (FIRC_CLK ÷ 8) ÷ 8)
MCXE31
LBIST_CLK
—
(MC_CGM.MUX_0_DC_5[DIV])
QSPI_MEM_CLK
—
(MC_CGM.MUX_0_DC_6[DIV])
CORE_CLK
• Application cores
• AXBS
750 KHz
• SRAM
(0011b)
• Flash memory controller port clock
• AIPS0 (high-speed peripheral clock)
(MC_CGM.MUX_0_DC_0[DIV])
AIPS_PLAT_CLK (medium-speed peripheral clock) 750 KHz
(MC_CGM.MUX_0_DC_1[DIV]) (0011b)
AIPS_SLOW_CLK (slow-speed peripheral clock) 375 KHz
(MC_CGM.MUX_0_DC_2[DIV]) (0111b)
DCM_CLK 750 KHz
(MC_CGM.MUX_0_DC_4[DIV]) (0011b)
HSE_CLK 750 KHz
(MC_CGM.MUX_0_DC_3[DIV]) (0011b)
1. MC_CGM.MUX_10_CSC[SELCTL] and MC_CGM.MUX_11_CSC[SELCTL] values are don't care since QSPI_SFCK and
TRACE_CLK are not used in this use case.
2. MC_CGM.MUX_0_CSC[SELCTL] must equal 0.
NOTE
For FIRC_CLK frequency modes less than 24 MHz, safety modules like the CMU_F x _ n must be disabled for safety
applications, because safety applications are to run on the PLL clocks. The CMU_F x _ n will cause erroneous FHH
events if not disabled.
23.7.2.7 Option F - Operation in 1:1 mode with CORE_CLK and AXBS_CLK at same speed
Table 138. Option F - Operation in 1:1 mode with CORE_CLK and AXBS_CLK at same speed
Clock frequencies
Clocking options
MCXE31B MCXE315/MCXE316
and MCXE317
PLL VCO frequency 960 MHz 960 MHz
Table continues on the next page...

---

*Page 630*

Clocking
Table 138. Option F - Operation in 1:1 mode with CORE_CLK and AXBS_CLK at same speed (continued)
Clock frequencies
Clocking options
MCXE31B MCXE315/MCXE316
and MCXE317
PLLODIV2_CLK 480 MHz 240 MHz
(PLLDIG.PLLDV[ODIV2]) (02h) (04h)
FIRC_CLK
(ELE_HSEB.CONFIG_REG_GPR[FIRC_DIV_SE —
L])
1
PLL_PHI1_CLK-related clocks
PLL_PHI1_CLK 240 MHz 160 MHz 48 MHz
(PLLDIG.PLLODIV_1[DIV]) (0001b) (0010b) (0100b)
QSPI_SFCK 120 MHz 80 MHz
—
(MC_CGM.MUX_10_DC_0[DIV]) (001b) (001b)
For fast pads —
—
120 MHz 80 MHz
(001b) (001b)
TRACE_CLK
(MC_CGM.MUX_11_DC_0[DIV])
For medium pads —
—
48 MHz 40 MHz
(100b) (011b)
2
PLL_PHI0_CLK-related clocks
PLL_PHI0_CLK 160 MHz 80 MHz
(PLLDIG.PLLODIV_0[DIV]) (010b) (010b)
CORE_CLK
• Application cores
• AXBS
80 MHz 80 MHz
• SRAM
(001b) (000b)
• Flash memory controller port clock
• AIPS0 (high-speed peripheral clock)
(MC_CGM.MUX_0_DC_0[DIV])
QSPI_MEM_CLK 160 MHz
—
(MC_CGM.MUX_0_DC_6[DIV]) (000b)
AIPS_PLAT_CLK (medium-speed peripheral
80 MHz 80 MHz
clock)
(001b) (000b)
(MC_CGM.MUX_0_DC_1[DIV])
Table continues on the next page...

---

*Page 631*

Clocking
Table 138. Option F - Operation in 1:1 mode with CORE_CLK and AXBS_CLK at same speed (continued)
Clock frequencies
Clocking options
MCXE31B MCXE315/MCXE316
and MCXE317
AIPS_SLOW_CLK (slow-speed peripheral clock) 40 MHz 40 MHz
(MC_CGM.MUX_0_DC_2[DIV]) (011b) (001b)
DCM_CLK 40 MHz 40 MHz
(MC_CGM.MUX_0_DC_4[DIV]) (011b) (001b)
HSE_CLK 80 MHz 80 MHz
(MC_CGM.MUX_0_DC_3[DIV]) (001b) (000b)
LBIST_CLK 40 MHz 40 MHz
(MC_CGM.MUX_0_DC_5[DIV]) (011b) (001b)
1. MC_CGM.MUX_10_CSC[SELCTL] and MC_CGM.MUX_11_CSC[SELCTL] must equal 1001b.
2. MC_CGM.MUX_0_CSC[SELCTL] must equal 1000b.

#### 23.7.3 Gasket configurations in various clocking modes

Table 139. Gasket configurations in various clocking modes (for MCXE31B)
1 2 3 4 5 6 7
Gasket Option A Option B Option C Option D Option E Option E2 Option F
configurations
1:1 Bypass
eDMA (S0)
Bypass
eDMA (S1)
ELE_HSEB 1:2 1:1, 1:2 Bypass
AIPS1/AIPS2 2:1 Bypass
QuadSPI 2:1 Bypass
PRAM/SRAM WS enabled WS disabled
EMAC 32:64 1:1
BDRAM 64:32 1:1
1. See Option A - High Performance mode (CORE_CLK @ 160 MHz) for details.
2. See Option B - Reduced Speed mode (CORE_CLK @ 120 MHz) for details.
3. See Option C - Boot Standby mode (CORE_CLK @ 24 MHz) for details.
4. See Option D - Low-Speed RUN mode (CORE_CLK @ 48 MHz) for details.
5. See Option E - Low-Speed Run mode (CORE_CLK @ 3 MHz) for details.
6. See Option E2 - Very-Low-Speed Run mode (CORE_CLK @ 750 kHz) for details.
7. See Option F - Operation in 1:1 mode with CORE_CLK and AXBS_CLK at same speed for details.
Table 140. Gasket configurations in various clocking modes (for MCXE315/MCXE316 and MCXE317)
1 2 3 4 5 6
Gasket configurations Option B Option C Option D Option E Option E2 Option F
ELE_HSEB 1:1, 1:2 Bypass
Table continues on the next page...

---

*Page 632*

Clocking
Table 140. Gasket configurations in various clocking modes (for MCXE315/MCXE316 and MCXE317) (continued)
1 2 3 4 5 6
Gasket configurations Option B Option C Option D Option E Option E2 Option F
AIPS1 2:1 Bypass
PRAM/SRAM WS disabled
BDRAM 64:32 (TCM 1:1
WS)
1. See Option B - Reduced Speed mode (CORE_CLK @ 120 MHz) for details.
2. See Option C - Boot Standby mode (CORE_CLK @ 24 MHz) for details.
3. See Option D - Low-Speed RUN mode (CORE_CLK @ 48 MHz) for details.
4. See Option E - Low-Speed Run mode (CORE_CLK @ 3 MHz) for details.
5. See Option E2 - Very-Low-Speed Run mode (CORE_CLK @ 750 kHz) for details.
6. See Option F - Operation in 1:1 mode with CORE_CLK and AXBS_CLK at same speed for details.

#### 23.7.4 Default clock configuration

At reset recovery, the chip runs on the FIRC_CLK as the default configuration as shown in Option C - Boot Standby mode
(CORE_CLK @ 24 MHz) . Clocking configuration Option_C is the default configuration out of reset with the ELE_HSEB core as the
boot core. The Cortex-M7_ n application core clocks are gated by default. You need to enable the core clocks by the configuring
the corresponding core clock enable bits shown in Core Clock Gating .

#### 23.7.5 PCFS

The chip supports software-controllable PCFS for MC_CGM MUX_0 (see section "Progressive Clock Frequency Switching
(PCFS)" in the "Clock Generation Module (MC_CGM)" chapter for details). PCFS increases and decreases the frequency in steps,
avoiding any overshoots or undershoots. When a functional reset event occurs with PCFS enabled, the PCFS process runs and
is then followed by the divider configuration updates.

#### 23.7.6 Updating dividers: crossbar halt handshake

The clock divider update process consists of the crossbar halt handshake sequence (see section "Clock dividers update" in the
"Clock Generation Module (MC_CGM)" chapter for the clock divider update process). A divider update asserts a request to the
crossbar switch to halt any transaction that is in process. The dividers are updated when the crossbar switch acknowledges the
request for halt. The halt request disables the crossbar switch gaskets in the following order:
1. Core gaskets (ELE_HSEB gaskets)
2. Crossbar switch (AXBS)
3. Flash AXBS bridge
4. PRAM/SRAM gasket
Dividers are updated after the gaskets acknowledge the halt request.

#### 23.7.7 Changing system clock configurations

Software sequence for switching clock configurations from FIRC to PLL or PLL to PLL:
1. Before changing clock dividers/clock switching, the communication modules must be disabled by software. This ensures
that erroneous communications do not occur during the clock transition.
2. All peripherals must be clock-gated using the MC_ME PCTL configurations.
3. All cores must be clock-gated, except the core being used to control the clock switching.
4. MC_CGM divider configurations can be done to change divider values as described above. The requisite system divider
frequency relations must be ensured.

---

*Page 633*

Clocking
5. Configure MC_CGM_MUX_0_DIV_TRIG_CTRL (enable HHEN and TCTL).
6. Configure MC_CGM_MUX_0_DIV_TRIG.
7. System clock switching can be done by configuration of MC_CGM_MUX_0_CSC[SELCTL]
Software sequence for switching clock configurations from PLL to FIRC clock switching:
1. Before changing clock dividers/clock switching, communication modules must be disabled by software. This ensures
that erroneous communications do not occur during the clock transition.
2. All the peripherals must be clock-gated using the MC_ME PCTL configurations.
3. All cores must be clock-gated, except the core being used to control the clock switching.
4. System clock switching can be done by configuration of MC_CGM_MUX_0_CSC[SELCTL].
5. MC_CGM divider configurations can be done to change divider values as described above. The requisite system divider
frequency relations must be ensured.
6. Configure MC_CGM_MUX_0_DIV_TRIG_CTRL (enable HHEN and TCTL).
7. Configure MC_CGM_MUX_0_DIV_TRIG.
NOTE
When enabling PLL, the PMC last mile regulator should be enabled first by configuring PMC_CONFIG[LMEN]
and PMC_CONFIG[LMBCTLEN] (if using an external BJT). The last mile regulator must be disabled after PLL
is disabled.

#### 23.8 Clock monitoring

The chip contains an independent clock monitoring mechanism which signals malfunctions in the clocking system. This chip
consists of six Clock Monitoring Units (CMU_F x _[0:5]) for monitoring system clock and clocking module outputs. Figure 100 and
Figure 101 show a lower-level view of the clocking monitoring system. Table 141 describes each CMU instance. Each CMU
instance provides an independent interrupt or reset indication when the clock signal is out of range or lost. The CMU_FM_ n
provides a timeout indication in case there is a loss of metered clock. Your software must periodically check the CMU_FM_1 and
CMU_FM_2 status within the chip FTTI (as specified in the Safety Manual).
NOTE
You must disable the CMU corresponding to the system clocks if the application changes the system clock source
or changes the system clock divider configuration.
You must disable the CMU monitoring a clock source before disabling the clock source, then enable it after enabling
the clock source.
The CMUs should be turned ON only after device has moved to PLL source (wherein LMR is ON).

---

*Page 634*

Clocking
CMU_FC_4
CMU_FC_4.IER[FHHAIE]
CMU_FC_4.SR[FHH]
CMU_FC_4.GCR[FCE]
AIPS_PLAT_CLK monitored-clk
CMU_FC_4 destructive reset
FIRC_CLK reference-clk
CMU_FC_4.SR[FLL]
CMU_FC_4.IER[FLLAIE]
CMU_FC_5
CMU_FC_5.IER[FHHAIE]
CMU_FC_5.SR[FHH]
CMU_FC_5.GCR[FCE]
HSE_CLK monitored-clk
CMU_FC_5 destructive reset
FIRC_CLK reference-clk
CMU_FC_5.SR[FLL]
CMU_FC_5.IER[FLLAIE]
CMU_FC_3
CMU_FC_3.IER[FHHAIE]
CMU_FC_3.SR[FHH]
CMU_FC_3.GCR[FCE]
CORE_CLK monitored-clk
CMU_FC_3 destructive reset
FXOSC_CLK reference-clk
CMU_FC_3.SR[FLL]
CMU_FC_3.IER[FLLAIE]
CMU_FC_0
CMU_FC_0.IER[FHHAIE]
CMU_FC_0.SR[FHH]
CMU_FC_0.GCR[FCE]
CMU_FC_0 destructive reset
CMU_FC_0.IER[FHHIE]
FXOSC_CLK monitored-clk
CMU_FC_0.IER[FLLAIE]
FIRC_CLK reference-clk
CMU_FC_0.SR[FLL]
CMU_FC_0 interrupt
CMU_FC_0.IER[FLLIE]
Figure 100. Frequency checking (FC) instances

---

*Page 635*

Clocking
CMU_FM_1
FIRC_CLK metered-clk
FXOSC_CLK reference-clk
CMU_FM_1.IER[FMCIE]
CMU_FM_1 frequency meter
CMU_FM_1.SR[FMC]
complete interrupt
CMU_FM_2
SIRC_CLK metered-clk
FXOSC_CLK reference-clk
CMU_FM_2.IER[FMCIE]
CMU_FM_2 frequency meter
CMU_FM_2.SR[FMC]
complete interrupt
Figure 101. Frequency metering (FM) instances
Table 141. System clock monitors
CMU Reference Monitored or Failure Monitoring type description
clock metered clock reaction
CMU_FC_0 FIRC_CLK FXOSC_CLK Destructive Precision over and under frequency
reset or
interrupt
CMU_FM_1 FXOSC_CLK FIRC_CLK Interrupt Current frequency measurement periodically triggered
by software
CMU_FM_2 FXOSC_CLK SIRC_CLK Interrupt Current frequency measurement periodically triggered
by software
CMU_FC_3 FXOSC_CLK CORE_CLK Destructive Precision over and under frequency
reset
CMU_FC_4 FIRC_CLK AIPS_PLAT_CLK Destructive Precision over and under frequency
reset
CMU_FC_5 FIRC_CLK HSE_CLK Destructive Precision over and under frequency
reset

#### 23.9 Glossary

MODULE_CLK Module operating clock
REG_INTF_CLK Module register interface clock used for register read and write
PCFS Progressive Clock Frequency Switching (see section PCFS for details)
POR Power On Reset
SBC System Basis Chip (see NXP SBC portfolio )
FLL Frequency lower than low frequency reference
FHH Frequency higher than high frequency reference

---

*Page 636*

Clocking
FTTI Fault Tolerance Time Interval

---

*Page 637*

