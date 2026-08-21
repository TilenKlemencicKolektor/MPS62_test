<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 40 -->

# Chapter 40

# Power Management

#### 40.1 Introduction

The power management system generates, monitors, and controls power supplies and related resets. This chapter describes the
system's interaction with other peripherals.
The power management system includes the following modules:
Table 208. Power management system modules and their functions
Part Function
MC_ME Initiates entry into Low-Power mode
MC_PCU Controls entry into and exit from Low-Power mode
PMC Regulates and monitors power supplies of the Run and the
Standby domains
MC_RGM Ensures a clean state and Run domain sanity by controlling the
reset sequence
FIRC, WKPU, and other chip peripherals Controls Standby mode wake-up and operations in Run and
Standby modes
Power management system for MCXE315/MCXE316 and MCXE317 represent block diagrams of the power management system
for the MCXE31 family.

---

*Page 1125*

Power Management

#### 40.1.1 Power management system (for MCXE31B)

VC_BJT
Optional
VDD_HV_B VDD_HV_A VRC_CTRL V15 VREFH VREFL
VDD_HV_A
Last Mile
SOG Pads
distributed LPCMP_ n ADC_ n
(Standby)
regulator
LPM Boot FPM GPIO
PMC
SOG
V11_STANDBY (Run)
V11_STANDBY
VSS
SW*
V25 32 K B
VDD_HV_B
SRAM
SIRC FIRC SXOSC
Pads
V11_RUN
GPIO
Registers V11_STANDBY
FXOSC
PIT_0
MC_RGM
(RTI) VSS
Flash
memory
WKPU RTC
PLLDIG
MC_PCU DCM
TempSense
(Double bond) (Double bond)
VSS V25 V11
- Standby domain
- Standby domain (optional)
SW* - On during FPM (Run mode) only
Figure 147. Power management system for MCXE31B

---

*Page 1126*

Power Management

#### 40.1.2 Power management system for MCXE315/MCXE316 and MCXE317

VDD_HV_A VREFH VREFL
VDD_HV_A
SOG Pads
LPCMP_ n ADC_ n
(Standby)
FPM LPM GPIO
PMC
SOG
(Run)
V11_STANDBY
VSS
SW*
V25 32 K B
SRAM
SIRC FIRC SXOSC
Not available
V11_RUN in the MCXE315/MCXE316
Registers V11_STANDBY
FXOSC
PIT_0
MC_RGM
(RTI)
Flash
memory
WKPU RTC
PLLDIG
MC_PCU DCM
TempSense
(Double bond) (Double bond)
VSS V25 V11
- Standby domain
- Standby domain (optional)
SW* - On during FPM (Run mode) only
Figure 148. Power management system for MCXE315/MCXE316 and MCXE317

#### 40.1.3 Features

• For MCXE315, MCXE316, MCXE317, PMC uses VDD_HV_A to generate 1.1 V (nominal) supply for the core logic
(V11_RUN)
• For MCXE31B, PMC uses a 1.5 V supply to generate 1.1 V (nominal) supply for the core logic (V11_RUN)
• MCXE31B includes a linear regulator to use an optional external ballast (BJT) and VRC_CTRL output for 1.5 V
generation.
• Supports a low-power regulator ( LPM ) supplying core logic during Standby mode ( V11_STANDBY )
• MCXE31B includes 1.1 V FPM regulator options for driving the logic in RUN mode (V11_RUN) from a 1.5V supply:

---

*Page 1127*

Power Management
— Boot LDO
— Internal Last-mile
• Supports power switches to isolate voltage islands and configure the chip in Standby mode
• Includes a linear regulator that generates a 2.5 V supply ( V25 ) from VDD_HV_A
• Supports voltage monitors ensuring transitions to a safe state (POR) when a supply is out of a valid range
• Controls power-mode transitions through an interaction with the digital interface
• Offers a padkeeping feature that retains PAD state during standby mode till software boots up
• Separates ADC reference supplies ( VREFH and VREFL )

#### 40.1.4 Operational power modes

This chip uses two modes:
• Run mode (FPM): Main operation mode having full-chip performance and a higher current consumption as compared to
Standby mode.
• Standby mode (LPM): Low-performance mode of the chip in which the Run domain is turned off. Most of the cores and
peripherals turn off in this mode.
The boot regulator manages the chip during the booting process except for MCXE315, MCXE316, and MCXE317.
The last-mile regulator is the full-performance regulator, which you enable for running applications. The upcoming sections
discuss the sequence to enable or disable the regulator.
The LPM regulator manages the chip in Standby mode.

#### 40.1.5 External 1.5 V source (for MCXE31B)

Power supply MCXE31
VDD_HV_A
5 V VDD_HV_B
1.5 V V15
Power supply MCXE31
VDD_HV_A
3.3 V VDD_HV_B
1.5 V V15
Power supply MCXE31
5 V VDD_HV_A
3.3 V VDD_HV_B
1.5 V V15
Figure 149. Core supply from external 1.5 V source for MCXE31B

---

*Page 1128*

Power Management

#### 40.1.6 Using a BJT for 1.5 V generation (for MCXE31B)

Power supply MCXE31
VDD_HV_A
5 V VDD_HV_B
VRC_CTRL
V15
Power supply MCXE31
VDD_HV_A
3.3 V VDD_HV_B
VRC_CTRL
V15
Power supply MCXE31
5 V VDD_HV_A
3.3 V VDD_HV_B
VRC_CTRL
V15
Figure 150. Using a BJT for 1.5 V generation for MCXE31B

#### 40.2 Power-up sequence

Figure 151 shows the reset sequence for a power-up or Standby mode event. This sequence starts from the POR phase. For the
Run domain logic, Standby mode exit operation is same as power-up.
See the Reset Overview chapter for more information about the chip reset sequence.

---

*Page 1129*

Power Management
Chip
power-up
Keep chip in POR
No Run-domain POR
Power-on reset V > Vt POR
sequence proceeds
Yes
Run-domain destructive
FIRC powers on
reset sequence proceeds
Wait for FIRC stabilization STANDBY_ENTRY_SW1
Peripheral shutdown
Destructive reset Run-domain functional
Destructive reset
reset sequence proceeds
sequence proceeds STANDBY_ENTRY_SW2
Application core shutdown
Functional reset Flash memory initialization
Functional reset
Flash memory scanning
sequence proceeds STANDBY_ENTRY_SW3
Trim loading
Flash memory or PMC
Chip out of reset in normal Run mode low-power handshake
Standby Yes Security functions disabled
STANDBY_ENTRY_SW4 Run
exit?
Main core shutdown
No
Security functions enabled
Change FIRC_DIV to DIV1
sBAF initialization code lsolations in between Run and
Enable FXOSC, PLL (Configurable)
standby logic enabled
Boot header parsing
XRDC configurations
CAAM_RNG initialization
HSE_FW verification
Debug authorization MC_RGM asserts all
resets to Run domain
CAAM_RNG initialization
Secure Yes OS initialization
HSE_FW boot
boot? HSE_FW initialization code
PMC moves to LPM regulator
Application boot
and device enters standby
No
Standby recovery initialization
Application core VTOR change
Run Wake-up Yes Wake-up sequence proceeds with
sBAF enabling application core event detected
PMC moving to run regulation
Standby
no
HSE_FW enabling application core
Remain in
Standby mode
Trigger Yes
Execute self-test
self-test? Legend:
No Hardware
ELE_HSEB sBAF
Enter Yes Continue to standby
Standby mode software entry ELE_HSEB firmware
No
Application core
software
Continue to application Note: HSE_FW and application sizes are 128 KB each
Figure 151. Power-up sequence

#### 40.3 PMC last-mile regulator auto-enable feature (for MCXE31B)

PMC includes an automatic last-mile auto-enable feature. After starting on boot regulator, this feature allows automatic switch over
to last-mile regulator if 1.5 V is present during:
• Chip startup
• Standby mode recovery
You can control the PMC last-mile regulator by appropriately configuring:
• PMC's CONFIG[LMAUTOEN] field
• PMC's CONFIG[LM_EN] field
For more information, see the descriptions of these fields in PMC's CONFIG register.

---

*Page 1130*

Power Management
NOTE
You must write 1 to PMC's CONFIG[LM_EN] field before transtioning to faster clock frequencies irrespective of
the setting of PMC's CONFIG[LMAUTOEN] field. This is because of the reduced clock speed when using the
boot regulator.

#### 40.3.1 Last-mile regulator with 1.5 V from an external source

Table 209. Last-mile regulator with 1.5 V from an external source
Operating condition Last-mile regulator operation
After POR Boots on boot regulator and then automatically switches to the last-mile regulator
After destructive reset Remains on last-mile regulator
NOTE
If the destructive reset source is low voltage reset on 1.1 V then
switchback happens from last-mile to boot regulator for boot.
After functional reset Remains on last-mile regulator
After PMC's LVSC[LVD15S] field Switches, automatically, to the boot regulator to configure clocks for slow speed
becomes 1

#### 40.3.2 Last-mile regulator using a BJT

Table 210. Last-mile regulator using a BJT
Operating condition Last-mile regulator operation
After POR Boots on boot regulator; switches to the last-mile regulator post reset when the Cortex-M7
core configures the software (post-secure boot) on FIRC (100 ms)
After destructive reset Switches to boot regulator to check reset propagation delay
After functional reset Remains on last-mile regulator
After PMC's LVSC[LVD15S] Switches, automatically, to boot regulator to configure clocks for slow speed
field becomes 1

#### 40.4 Standby mode entry sequence

The Standby mode entry sequence includes three phases of operation:
• Standby mode entry configuration phase or software Standby mode entry sequence
• Standby mode entry handshake phase or hardware Standby mode entry sequence
• Standby mode entry or PMC Standby mode entry
NOTE
The Standby mode entry sequence described in this section is the only supported sequence. Contact NXP support
if you require an alternate Standby mode entry sequence.

#### 40.4.1 Software Standby mode entry sequence

The Standby mode entry sequence discusses chip configuration described in the sections that follow.
Before entering the software Standby mode sequence, the system clock source must be changed to FIRC at 48 MHz because
PLLDIG is not available in Standby mode. In this mode, all clock sources can be optionally disabled (including FIRC, which results

---

*Page 1131*

Power Management
in a no-clock, low-power consumption mode). You could use FXOSC, if enabled, when the 2.5 V supply is available by appropriate
configuration of PMC's CONFIG[LPM25EN].
The software Standby mode entry sequence consists of four steps:
1. SW1: Module shutdown process
2. SW2: Application core shutdown process
3. SW3: Flash memory low-power handshake and PMC last-mile regulator control
4. SW4: Main core shutdown process
These processes are described in detail in the sections that follow. See Figure 151 that shows the relationship between these four
steps in a flow diagram.
40.4.1.1 SW1: Module shutdown process
I/O and module configuration for Standby mode discusses the procedure to configure I/O and the chip modules for Standby mode.
The entry sequence for this mode includes module clock disabling steps (see the "Clocking" chapter for module clock turn on and
turn off processes). You must use MC_ME's PRTN n _COFB m _CLKEN[REQ p ] fields to enable or disable module clocks.
40.4.1.1.1 Disabling modules
Disable modules by configuring the appropriate fields in their registers for Standby mode operation. See specific module chapters
for more information.
The Standby mode entry sequence includes the module clock disabling step, with which you can disable the modules that you do
not need for Standby mode operation.
The sequence of disabling modules is shown in I/O and module configuration for Standby mode .
NOTE
While enabling or disabling the modules, you must verify that the module is disabled when you read MC_ME's
PRTNn_COFBm_STAT register and the module disable field, if applicable. In case of a discrepancy, you must
perform proper diagnostic steps.
You must clear the I/O controls for the pads that you do not require in Standby mode (OBE, IBE, and so on). This
avoids any unwanted pad keeping settings. See Pad keeping for more information on the chip pad keeping process.
For any standby wake source, if an interrupt occurs it must be disabled before entering into standby and only the
wake up event of that source must be enabled. This is to avoid any SW conflict in the interrupt handling for multi
application core cases.
The MC_ME's PRTN n _COFB m _STAT register indicates the status of peripheral clock enable or disable. It may take up to three
clock cycles for MC_ME's PRTN n _COFB m _STAT register to update after MC_ME's PRTN n _COFB m _CLKEN register is updated.
Once modules are disabled by following above steps:
• Switch to FIRC as the system clock
• Disable FXOSC and wait for clock status
• Configure standby entry

---

*Page 1132*

Power Management
40.4.1.1.2 I/O and module configuration for Standby mode
Initiate Standby sequence
Clear all pending interrupts
Disable all communications
(QuadSPI, EMAC, and so on)
Have all
No
communications
stopped?
Yes
As needed, configure the associated
MC_ME.PRTN n _COFB m _CLKEN
fields for Standby mode
Are all necessary
No
MC_ME.PRTN n _COFB m _STAT
fields 0?
Yes
Before switching to
FIRC_CLK, you must
Configure entire chip to
take the necessary
only use FIRC_CLK
precautions to avoid
clock glitches.
Need fast startup
No
of FXOSC_CLK after
Standby exit?
Yes
Not recommended if
minimum power Verify PMC.CONFIG[LPM25EN]
Configure FXOSC_CLK for
consumption is needed. equals 0 disabling 2.5 V
operation after Standby mode
Use if faster FXOSC during Standby mode
start is required.
Write 1 to
PMC.CONFIG[LPM25EN]
Write 0 to
See the ‘’Padkeeping’’
DCM.DCMRWF1[STANDBY_IO_CONFIG]
section for details.
to enable padkeeping
Write 1 to SIUL2.MSCR n [PKE]
of the I/O that you want active in
Standby mode
Configure SIUL2.MSCR
for the function of the I/O
during Standby mode
Configure module registers
used in Standby mode
Application core shutdown
Figure 152. I/O and module configuration for Standby mode

---

*Page 1133*

Power Management
40.4.1.2 SW2: Application core shutdown process Add information about
interrupt?
The application cores(s) execute a WFI (as opposed to the main core running the Standby mode entry sequence). See the section
"Application core shutdown" in the MC_ME chapter for more information. low voltage reset will
occur if LM does not start
The main core configuration (programming valid core ID and enabling standby entry process) and wake-up source configuration
must also be set in SW2, so that SW4 contains only the main core WFI execution.
Once PLL is shutdown, FXOSC
40.4.1.3 SW3: Flash memory low-power handshake and PMC last-mile regulator
In this process, you execute a flash memory low-power handshake and disable the PMC last-mile regulator by executing the
procedures indicated in Figure 153 and Figure 154 .
Flash memory
disable
Initiate a flash memory If a
handshake to cofirm no high tur
voltage operations are in process
W
PM
High voltage
Yes
flash memory operation
in progress?
No
PLLDIG not on during Disable PLLDIG by writing zero
Standby mode. to PLLDIG.PLLCR[PLLPD]
FIRC.STDBY_ENABLE[STDBY_ENABLE] Configure SIRC, FXOSC, and
SIRC.MISCELLANEOUS_IN[STANDBY_ENABLE]
SXOSC as needed for Standby
FXOSC.CTRL[OSCON]
mode
SXOSC.SXOSC_CTRL[OSCON]
Configure the V25 regulator for
operation in Standby mode
Write
to cle
Configure the following DCM register fields as needed:
DCMRWF2[PMC_TRIM_RGM_DCF__BYP_STDBY_EXT]
DCMRWF2[FIRC_TRIM_BYP_STDBY_EXT]
DCMRWF2[DCM_SCAN_BYP_STDBY_EXT]
DCMRWF2[SIRC_TRIM_BYP_STDBY_EXT]
Do not turn on anything W
Continue Standby
else until after settling pr
mode entry process
fo
time.
Figure 153. Flash memory Standby mode configuration
NOTE
Disable the last-mile regulator according to the Last-mile regulator disable sequence (not applicable for MCXE315,
MCXE316, and MCXE317) .

---

*Page 1134*

Power Management
40.4.1.3.1 Last-mile regulator disable sequence (not applicable for MCXE315, MCXE316, and MCXE317)
Disable PMC
last-mile regulator
Simultaneously write 0 to both
PMC.CONFIG[LMEN] and
PMC.CONFIG[LMBCTLEN]
Software writes GPIO indicating
last-mile regulator is disabled
External source disables
BJT collector voltage
Continue Standby mode
disable process
Figure 154. Last-mile regulator disable sequence
When exiting Standby mode, the 1.1 V capacitor is charging. You can charge the 1.5 V capacitor during or after the 1.1 V capacitor
is charged. If you charge the 1.5 V capacitor sequentially after the 1.1 V capacitor, you will need additional time to complete the
overall charging process.
Leaving PMCs CTRL[LMBCTLEN] = 1 saves time when exiting Standby mode and does not draw any additional current during
this mode.
40.4.1.4 SW4: Main core shutdown process
For information on this process, see these in the "Mode Entry Module (MC_ME)" chapter:
• Figure "Standby entry sequence along with main core shutdown"
• Section "Main core shutdown and Standby mode entry"
You must configure WKPU before disabling interrupts to avoid missing any events as shown in the Standby entry sequence along
with main core shutdown flowchart in the MC_ME chapter. You must program WKPU, disable interrupts, and configure MC_ME's
Standby mode entry in SW1 (see SW1: Module shutdown process for more information). In SW4, you must perform only the
main-core WFI execution.

#### 40.4.2 Hardware Standby mode entry sequence

The hardware Standby mode entry sequence consists of handshaking between MC_PCU and MC_ME occuring after Software
Standby mode entry sequence completes. The FSM in MC_PCU performs these steps automatically and does not require your
intervention (see Power sequence FSM in the Power Control Unit (MC_PCU) chapter for more information).
During the MC_ME and PCU phase, MC_ME and PCU:
• Enable FIRC
• Deassert isolation
• Deassert reset to PD1

#### 40.4.3 PMC Standby mode entry

The PMC Standby mode entry sequence starts after Hardware Standby mode entry sequence completes, and consists of
these phases:

---

*Page 1135*

Power Management
1. Standby mode entry acknowledgment and initiation of an internal low-power process on receiving Standby mode
entry request
2. Disabling of the boot regulator within the PMC low-power process (for MCXE31B)
3. Disabling of the V25 regulator and the FPM LVR monitors
4. Disabling of the V25 regulator (oscillator and flash memory supply) and the LPM monitor, unless it is enabled
during Standby mode (see PMC's CONFIG[LPM25EN] field in the "Power management controller (PMC)" chapter for
more information)
5. Disabling of the VDD_HV_B LPM monitors (not applicable for MCXE315, MCXE316, and MCXE317)
During PMC phase, after receiving an LPM request, PMC:
• Deasserts FPM ready signal
• Starts the courtesy timer
• When the courtesy timer expires, PMC:
— Disables the V25 regulator
— Disables the HP reference blocks
— Disables the external NMOS
— Opens the PD0 switch
— Enables a core LPM request
— If selected, enables a 2.5 V LPM request
— Disengages FPM monitors
— Disables the VDD_HV_B LPM monitor, if present and deselected
• Waits for LPM signal deassertion

#### 40.5 Chip status at the end of Standby mode entry sequence

After PMC Standby mode entry completes, the chip completes Standby mode entry as follows:
1. Configures Standby domain peripherals according to SW1 (see SW1: Module shutdown process ).
2. Enables pad keeping on pins as described in SW1 (see SW1: Module shutdown process ). See Pad keeping for other
details.
3. Powers down all memory types except Standby RAM (in SRAM0).
• The V25 regulator can remain on during Standby mode. However, for maximum power savings, it must remain off in
this mode.
4. Isolates Standby and Run domains from each other:
• Standby domain is functional.
• Run domain is held in reset.
5. Configures the system clock (FIRC_CLK or PLL_PHI n _CLK, depending on their configuration) and other clock sources
according to SW3 (see Figure 153 for the procedure details).
The cores are off and in the Standby domain.
6. Turns off the boot (not applicable for MCXE315, MCXE316, and MCXE317) and FPM regulators
7. Waits for a wake-up event to initiate recovery from Standby mode.

---

*Page 1136*

Power Management

#### 40.6 Chip operation in Standby mode

This chip supports the following functionalities in Standby mode:
• STANDBY_RAM content retention during Standby mode.
• Wake-up from up to 60 digital inputs (for details, see the signals WKUP[ n ] functions of WKPU module in the IOMUX file
attached to this document). The section WKPU configuration on the chip in the "Wakeup Unit (WKPU)" chapter shows
mapping of the wake-up sources.
• Wake-up from up to 16 analog inputs through the Trigger mode functionality (see the signals CMP n _IN m functions of CMP n
modules in the IOMUX file attached to this document).
• Wake-up from on-chip timers:
— RTI (function of PIT[0])
— SWT0
— RTC
• Ability to configure the chip clocking modules to optionally enable or disable in Standby mode (FIRC, SXOSC, FXOSC,
and SIRC).

#### 40.7 Standby mode exit

This chip supports Standby mode exit from a wake-up, functional reset, or destructive reset event. The sources that cause chip
Standby mode exit are:
• MC_RGM functional reset event
• MC_RGM destructive reset event
• WKPU wake-up events, WKPU[0]–WKPU[63]. See the WKPU chapter for more information.
After Standby mode exit, the following events occur (for more information, see "Power sequence FSM" in the MC_PCU chapter
for MC_PCU FSM transitions during entry into and exit from Standby mode):
1. A wake-up event arrives.
2. FIRC starts powering up (if disabled in Standby mode).
3. PMC starts the transition process to FPM (for example, enables the last-mile regulator and provides V11_RUN supply to
the chip).
4. MC_PCU removes the isolation between Run and Standby domains.
5. Run domain reset deasserts (asserts on Standby mode entry) and the chip undergoes a functional reset exit sequence for
this domain.
6. The chip enters Run mode of operation.
NOTE
A reset event during standby results in pad controls to get reset. Thereby, resulting in unpredictable toggling
at GPIO.

#### 40.7.1 Faster Standby mode exit

The chip supports an optional configuration for faster recovery from Standby mode on the expense of a higher capacitor
recharging current. See the CONFIG[FASTREC] field description in the PMC chapter for more information on faster PMC recovery
from Standby mode.
This chip supports an optional feature that bypasses:
• FIRC trimming

---

*Page 1137*

Power Management
• PMC trimming
• DCM scanning
To use the bypass operation, write 1 to DCM.DCMRWF2[5], DCM.DCMRWF2[4], and DCM.DCMRWF2[3] respectively before
Standby mode entry. This results in a considerable reduction in Standby mode exit duration. The trim values are retained across
Standby mode and bypassing these values does not cause any impact. Even if the FIRC trimming is bypassed, the FIRC must
be at 48 MHz before entering Standby mode.
Configuration to achieve the tMODE_STDBYEXIT_FAST as specified in Datasheet
• DCMRWF2[PMC_TRIM_RGM_DCF__BYP_STDBY_EXT] = 1
• DCMRWF2[FIRC_TRIM_BYP_STDBY_EXT] = 1
• DCMRWF2[DCM_SCAN_BYP_STDBY_EXT] = 1

#### 40.7.2 Last-mile regulator enable sequence (not applicable for MCXE315, MCXE316, and MCXE317)

After Standby mode exit, you must re-enable the PMC last-mile regulator before transitioning to faster clock frequencies. Figure
155 shows the last-mile regulator enable sequence part of the Standby mode exit process.

---

*Page 1138*

Power Management
Enable PMC
last-mile regulator
Using BJT
No
for 1.5 V
generation?
Yes
If applicable,
Request turn on of 1.5 V
turn on BJT collector voltage
Write 1 to
PMC.CONFIG[LMBCTLEN]
PMC
No
CONFIG[FASTREC]
equal 0?
Yes
Wait 50 μs Wait 15 μs
PMC
No
LVSC[LVD15S]
equal 0?
Yes
Write 1 to PMC.LVSC[LVD15F]
to clear flag
Write 1 to
PMC.CONFIG[LMEN]
Yes
Do not turn on anything Wait tsettle_lm time before
else until after setting proceeding (see data sheet
time. for settling time)
Continue Standby
mode exit process
Figure 155. Last-mile regulator enable sequence

#### 40.8 Chip power domain partitioning

These are the modules in the Standby domain. For more information, see "Peripheral reset status" in the "Reset
Overview" chapter.
• FIRC
• FXOSC
• MC_RGM

---

*Page 1139*

Power Management
• DCM
• WKPU
• PIT_0 (RTI)
• LPCMP_[0:2]
• PMC
• SIRC
• SXOSC
• RTC
• SWT_0
• DCF clients

#### 40.8.1 Module power status

Table 211. Module power status
PD0 contents MCXE31B MCXE317 MCXE315/MCXE316
External Pin wakeups Minimum 60 Minimum 60 33 on 100MQFP & 12
on 48LQFP
RTC_API Yes Yes Yes
LPCMP0 Yes Yes Yes
LPCMP1 Yes Yes No
LPCMP2 Yes No No
SWT0 Yes Yes Yes
WKPU Yes Yes Yes
PMC Yes Yes Yes
RGM Yes Yes Yes
SXOSC(only used for RTC) Yes Yes No
FXOSC Yes Yes Yes
FIRC Yes Yes Yes
SIRC Yes Yes Yes
CLK OUT Yes Yes Yes
SRAM 32K 32K 32K
PIT_RTI_0 Yes Yes Yes
ATX Yes Yes Yes
DCM and DCF records Yes Yes Yes
DCM Flash Interface Yes Yes Yes
SWT0 (Cortex-M7_0) resides in the Standby domain and supports a configurable hardware-based timer operation during Standby
mode depending on the configuration of SWTs.

---

*Page 1140*

Power Management
All clock sources (excpet PLLDIG) are available in Standby mode. SIRC is present in the Standby domain for low-power wake-up,
but can be enabled.
NOTE
This chip does not support Stop and Wait modes. The only low-power mode it supports is Standby.

#### 40.9 Pad keeping

Pad keeping allows you to return to I/O Run mode configuration settings after using I/O during Standby mode. It eliminates I/O
reconfiguration in Run mode.

#### 40.9.1 Pad keeping on Run domain pins

The process of entering Standby mode ensures that the chip maintains the Run domain configuration settings. Software Standby
mode entry sequence specifies that you must write 0 to DCM's DCMRWF1[STANDBY_IO_CONFIG] before configuring I/O pad
keeping. You could consider DCM's DCMRWF1[STANDBY_IO_CONFIG] field as a global enable for all pad keeping purposes
during Standby mode. If you are unable to write to this field as described, pad keeping works as explained in this chapter, during
Standby mode.
After the chip exits Standby mode, you must write 1 to DCM's DCMRWF1[STANDBY_IO_CONFIG] field (see "Chip specific
register descriptions" in the DCM chapter). Writing 1 to this field disables pad keeping and places chip I/O according to its Run
mode configuration.

#### 40.9.2 Pad keeping on Standby domain pins

The Standby domain pads can have pad keeping enabled or disabled based on pad availability in Standby mode. Writing to
SIUL2's MSCR n [PKE] field configures Standby mode pad keeping on a selected I/O.
Ensure that DCM's DCMRWF1[STANDBY_IO_CONFIG] = 0 before Standby mode entry for pad keeping on Standby domain
pads. Write 0 to the field in case its value is not 0 already.
If a pad is not required during Standby mode, the corresponding MSCR n [PKE] field in SIUL2 must be 0.

#### 40.9.3 Pad keeping configuration procedure

This procedure specifies how to enable an I/O for pad keeping:
1. Configure SIUL2's MSCR n register to control the pad state prior to Standby mode entry (for example, writing 0 to SIUL2's
MSCR n [OBE], MSCR n [IBE], and MSCR[PUE] fields tristates the corresponding I/O).
2. Configure SIUL2's MSCR n [PKE] field as needed for an I/O pad keeping state during Standby mode.
3. Write 0 to DCM's DCMRWF1[STANDBY_IO_CONFIG].
The application core executes WFI, and Standby mode sequence starts.
4. Write 1 to DCM's DCMRWF1[STANDBY_IO_CONFIG] field on Standby mode exit to disable pad keeping. This configures
SIUL2 according to its previous settings.
If you write 1 to DCM's DCMRWF1[STANDBY_IO_CONFIG] field before entering Standby mode, the isolation removal
hardware removes pad keeping on Standby mode exit. This (writing 1 to DCM's DCMRWF1[STANDBY_IO_CONFIG] field
before Standby mode entry) is useful in case of low-power debug because enabling pad keeping does not allow low-power
debug protocol to work properly (because the TDO pad is padkept). For low-power debug, you must write 1 to DCM's
DCMRWF1[STANDBY_IO_CONFIG] field prior to Standby mode entry.
In case of Standby mode exit by reset, the pad keeping of the reset pin is removed when the chip is reset on Standby mode wake
up. See the "GPIO padkeeping enable" signal in Figure 157 . The signal corresponds to wake-up via reset event case.

#### 40.9.4 Pad keeping waveforms

---

*Page 1141*

Power Management
40.9.4.1 Pad keeping when the chip wakes up from Standby mode via an interrupt event
FIRC_CLK
Run domain reset (active low) a f
Standby domain reset (active low)
Chip isolation enabled after Run Run domain comes out of reset
domain is reset on standby entry
Run domain isolation b e
Isolation is removed on wake-up event and PMC
switches from Standby to Run mode gracefully
Wake-up event d
GPIO padkeeping enabled on isolation enable
Padkeeping enable, DCM.DCMRWF1[16] You must write 0 to this GPR GPR must be 1 to remove GPIO padkeeping g
before Standby entry.
GPIO padkeeping disabled after padkeeping enable GPR is 1
GPIO padkeeping enable c h
Figure 156. Pad keeping when the chip wakes up from Standby mode via an interrupt event
40.9.4.2 Pad keeping when the chip wakes up from Standby mode via reset
If the chip exits Standby mode via reset, the reset pad keeping is removed when the chip resets after Standby-mode wake-up.
See the figure's "GPIO padkeeping enable" waveform corresponding to wake-up via a reset event.
FIRC_CLK
Run domain reset (active low) a f g
Run domain comes out of reset
Standby domain reset (active low)
Chip isolation enabled after Run
domain is reset on standby entry After graceful Standby exit, the chip undergoes reset
(functional/destructive as per the reset event)
Run domain isolation b e
Isolation is removed on wake-up event and PMC
gracefully switches from Standby to Run mode
Wake-up event d
(functional or destructive reset event)
GPIO padkeeping enabled on isolation enable GPIO padkeeping disabled due to reset.
GPR configuration is not required.
Padkeeping enable, DCM.DCMRWF1[16] You must write 0 to this GPR
before Standby entry.
GPIO padkeeping enable c h
Figure 157. Pad keeping when the chip wakes up from Standby mode via reset

#### 40.9.5 SIUL2's fields for actively-driven pins in Standby mode

If you use a WKPU pin as a wake-up input, perform these operations on SIUL2's fields:
1. Write 1 to MSCR x [IBE].

---

*Page 1142*

Power Management
2. Program MSCR x [PUE] and MSCR x [PUS] according to the use case.
3. Write 0 to MSCR x [PKE].
For the GPIO pins that are driven to high impedance during Standby mode:
1. Write 0 to DCM's DCMRWF1[STANDBY_IO_CONFIG] field.
2. Write 0 to SIUL2's MSCR x [SSS] field (GPIO mode).
3. Write 0 to SIUL2's MSCR x [IBE] and MSCR x [OBE] fields.
Some of the pins are, by default, actively driven in Standby mode. If these pins retain a static value throughout the mode, program
the corresponding MSCR x [PKE] bits individually.
This table shows the list of pins available in Standby mode along with the functions they perform.
Table 212. Active pins in Standby mode
1
Pin Function
GPIO_4 CMP0_OUT
GPIO_5 RESET_b
GPIO_9 CMP2_OUT
GPIO_11 CMP0_RRT
GPIO_12 CLKOUT_STANDBY; CMP1_OUT
GPIO_69 CMP2_RRT
GPIO_110 CMP0_RRT
GPIO_131 CMP0_OUT
GPIO_138 CLKOUT_STANDBY
GPIO_143 CMP1_RRT
GPIO_158 CMP1_OUT
GPIO_159 CMP1_RRT
GPIO_174 CMP0_OUT
GPIO_175 CMP0_RRT
GPIO_196 CMP2_OUT
GPIO_197 CMP2_RRT
1. See the IOMUX sheet attached to this document for details on pins configuration in different chips.

#### 40.10 Glossary

Application core Core apart from the main core
Boot regulator The on chip 1.1 V regulator that is active during startup and entry and exit from Standby mode
FSM Finite state machine
FPM Full-power mode (Run mode)—uses the last-mile regulator and 1.5 V source to generate the 1.1 V core
logic supply during a full-power operation (V11_RUN)
LPM Low-power mode (Standby mode)—uses the low-power regulator to generate the 1.1 V core logic supply
during low-power operation (V11_STANDBY)

---

*Page 1143*

Power Management
LVR Low voltage reset
Main core Core initiating the chip Standby mode request (for example, the core corresponding to the "core index" in
MC_ME's MAIN_COREID register)
Pad keeping Maintains I/O pad configuration during Standby mode, if enabled
V11_STANDBY Core logic and clock sources, low-voltage supply to Standby domain
V11_RUN Low-voltage supply to Run domain
V15 High-current input for core or logic supply from either an external BJT or from direct 1.5 V external supply
V25 Flash memory, FXOSC, and PLLDIG high-voltage supply
VDDA_ADC ADC supply voltage
VREFH ADC high-voltage reference supply
VREFL ADC low-voltage reference supply
VSS Core logic ground supply
VDD_HV_A Main I/O voltage supply (5 V or 3.3 V)
VDD_HV_B Other I/O domain voltage supply (5 V or 3.3 V)
VRC_CTRL PMC voltage regulator control output using the BJT option to generate a 1.5 V supply
WFI Wait for interrupt software instruction

---

*Page 1144*

