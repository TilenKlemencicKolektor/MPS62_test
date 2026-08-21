<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 78 -->

# Chapter 78

# Temperature Sensor (TEMPSENSE)

#### 78.1 Overview

TEMPSENSE is used to measure the temperature on the chip through an ADC and to flag if an overtemperature condition occurs .
The TEMPSENSE module contains control registers for use with the Engineering temperature sensor (ETS) hard block and the
flag generation hard block. These registers use a peripheral bus interface to communicate with the chip.

#### 78.1.1 Block diagram

The functional structure of the TEMPSENSE can be seen in the block diagram in Figure 454 .

---

*Page 3313*

Temperature Sensor (TEMPSENSE)

# ETSCTL

# GNDSEL GNDSEL ETS_EN ETS_EN

& &

### Vdd

## To ADC

Figure 454. TEMPSENSE block diagram

#### 78.1.2 Features

The TEMPSENSE includes the following features:
• Provide a voltage proportional to the temperature which will be read out by ADC
• Ground selection for improved precision in the ETS . The ETS ground could be different from the ADC ground. By
exposing it to the ADC, the temperature calculation could be improved as the voltage value will be more precise.

---

*Page 3314*

Temperature Sensor (TEMPSENSE)

#### 78.2 Functional description

#### 78.2.1 Clocking

This module has no clocking considerations.

#### 78.2.2 Interrupts

This module has no interrupts.

#### 78.2.3 General

The TEMPSENSE module is designed to measure the temperature on the chip.
The TEMPSENSE output can be read by an ADC on demand so that the software can determine the current die temperature. The
software should enable the TEMPSENSE by setting ETSCTL[ETS_EN].
The TEMPSENSE output can be read by an ADC on demand so that the software can determine the current die temperature..
Coefficients are used to allow a simple and accurate calculation of linearized temperature directly from the ADC. The increase of
the sampling time of the ADC from the minimum sampling value is increasing the temperature accuracy.

#### 78.2.4 Initialization

78.2.4.1 Conversion from voltage to temperature
Solve the equation for the conversion from voltage to temperature, where V ETS is the difference of V be and V GND . V GND is expose
on the ADC output when ETSCTL[GNDSEL] is set.
The coefficients TCA0, TCA1 and TCA2 are read from the corresponding registers. They are stored in a signed fixed-point format
as the following TCAx(11,4) (1 bit for the sign, 11 bits for the integer part and 4 bits for the decimal part).
The calculation of the temperature should be done with the actual coefficient values provided in the TCAx fields. See the example
below for an ambient temperature of 25C, and an ADC reference voltage (V REFH ) of 5V. See the ADC section of the device data
sheet for more details on V REFH and N-bit level resolution.
V REFH
N
By using the formula, the junction temperature calculated is 26.5838 C.
The maximal calculation error is 0.0313 C.

#### 78.3 External signals

There are no external signals for this module.

---

*Page 3315*

Temperature Sensor (TEMPSENSE)

#### 78.4 Memory map and register definition

#### 78.4.1 Transfer error description

The following actions will cause a transfer error and will not change the register content:
• Any access to an unused register address
• Write access to a read-only register (TCAx)

#### 78.4.2 TEMPSENSE register descriptions

78.4.2.1 TEMPSENSE memory map
TEMPSENSE base address: 4037_C000h
Offset Register Access Reset value
Width
(In bits)
0h ETS Control (ETSCTL) 32 RW 0000_0000h
8h Temperature Coefficient (TCA0) 32 R See section
Ch Temperature Coefficient (TCA1) 32 R See section
10h Temperature Coefficient (TCA2) 32 R See section
78.4.2.2 ETS Control (ETSCTL)
Offset
Register Offset
ETSCTL 0h
Function
This register contains control bits that control ETS.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
GNDS ETS_
EL EN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 3316*

Temperature Sensor (TEMPSENSE)
Fields
Field Function
Reserved
31-2
—
Ground selection
1
0b - No exposure of the ground
GNDSEL
1b - Expose ground on the ADC output if ETSCTL[ETS_EN]=1
0 Temperature Sensor enable
ETS_EN Power up the ETS.
0b - Power down
1b - Functional mode
78.4.2.3 Temperature Coefficient (TCA0)
Offset
Register Offset
TCA0 8h
Function
This register contains the coefficient TCA0 needed to calculate the temperature. the reset value is specific for each chip.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TCA0
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
Reserved
31-16
Table continues on the next page...

---

*Page 3317*

Temperature Sensor (TEMPSENSE)
Table continued from the previous page...
Field Function
—
15-0 Temperature coefficient A0
TCA0 see Conversion from voltage to temperature for the usage of this coefficient
78.4.2.4 Temperature Coefficient (TCA1)
Offset
Register Offset
TCA1 Ch
Function
This register contains the coefficient TCA1 needed to calculate the temperature. the reset value is specific for each chip.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TCA1
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
Reserved
31-16
—
15-0 Temperature coefficient A1
TCA1 see Conversion from voltage to temperature for the usage of this coefficient

---

*Page 3318*

Temperature Sensor (TEMPSENSE)
78.4.2.5 Temperature Coefficient (TCA2)
Offset
Register Offset
TCA2 10h
Function
This register contains the coefficient TCA2 needed to calculate the temperature. the reset value is specific for each chip.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset u u u u u u u u u u u u u u u u
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R TCA2
W
Reset u u u u u u u u u u u u u u u u
Fields
Field Function
Reserved
31-16
—
15-0 Temperature coefficient A2
TCA2 see Conversion from voltage to temperature for the usage of this coefficient

#### 78.5 Glossary

ADC Analog to digital converter
ETS Engineering temperature sensor
CTS Customer temperature sensor
V be Base emitter voltage
V ETS Engineering temperature sensor voltage
V GND Ground voltage

---

*Page 3319*

# Appendix A

# Release notes

#### A.1 About This Manual changes

No substantial content changes.

#### A.2 Introduction changes

No substantial content changes.

#### A.3 Memory map module changes

No substantial content changes.

#### A.4 Signal Multiplexing module changes

No substantial content changes.

#### A.5 Cortex-M7 changes

No substantial content changes.

#### A.6 Miscellaneous Control Module (MCM)

#### A.6.1 Chip-specific MCM information changes

No substantial content changes.

#### A.6.2 MCM module changes

• In Local Memory Descriptor 0 (LMEM_DESC_0) , updated the note to "The reserved bits can be read and written (instead
of read as zero and write ignored). Setting any of these bits does not have any functional impact".
• Changed the bullet from "FPU invalid operation interrupt is enabled (FDZCE) and an invalid operation occurs (FDZC)" to
"FPU invalid operation interrupt is enabled (FIOCE) and an invalid operation occurs (FIOC)" in Interrupts .
• Added PID register.
• Added more restrictions to note in Memory map and register descriptions .
• Updated with inclusive language.

#### A.7 Miscellaneous System Control Module (MSCM)

#### A.7.1 Chip-specific MSCM information changes

No substantial content changes.

---

*Page 3320*

Release notes

#### A.7.2 MSCM module changes

No substantial content changes.

#### A.8 Virtualization Wrapper (VIRT_WRAPPER)

#### A.8.1 Chip-specific VIRT WRAPPER information changes

No substantial content changes.

#### A.8.2 VIRT_WRAPPER changes

• Updated the note from "Access to reserved spaces outside the register bank generates the transfer error. However,
access to the reserved spaces within the register bank does not have any effect" to "Access to reserved spaces outside
the register bank and holes (unimplemented registers) within register bank generates the transfer error.".
• Added the note "Any master can access holes (unimplemented registers) of SIUL2 through PDAC0." for REG_A and
REG_B.
• Corrected the syntax to show the reserve RW Implemented hole bits within the registers REG_A and REG_B using
MSCR_MAPPED parameter for REG_A and REG_B.
• Changed from PDAC to PDAC slot as applicable throughout the document.
• Added a note, "PDAC and PDAC slot are interchangeable terms." in Overview .
• Added section Initialization , Clocking , Application Information , and External signals .
• Made editorial updates.
• In Virtualization of interrupt control registers , added "The PDAC that...16 KB space" and "Access to mirrored...region".
• In Modes of operation , added see "Chip specific... for more details".
• Replaced "master" with "initiator" throughout the document.
• Removed, "This process is called ANY_MASTER" from PDAC-based protection scheme .
• Replaced "that is programmed by PDAC4 ... accessing PDAC0" with "that is programmed by specific PDAC ...
accessing PDAC0" from Overview .
• Added a table in Register group mapping for SIUL2 .

#### A.9 System Integration Unit Lite2 (SIUL2)

#### A.9.1 Chip-specific SIUL2 information changes

No substantial content changes.

#### A.9.2 SIUL2 module changes

• In the note "For the array of 16-bit registers PGPDO n and PGPDI n ": rephrased the second bullet-item.
Table continues on the next page...

---

*Page 3321*

Release notes
• Added field values 'Low' and 'High' for PGPDOn and PGPDIn registers.
• In External interrupt initialization added a new step 1 and added a note "When you chose external interrupt inputs for ...".
• Changed access type of reserved field from ROZ to RO in IFCPR and IFMCR n registers.
• Updated Figure 16 .
• In the final note in register descriptions of MSCR n and IMCR n , added list items:
— "These registers are a part of SIUL memory map but physical implementation of these register is a part of IOMUX
RTL.
— SIUL2 interprets accesses to MSCRn at module level."
• Throughout register descriptions, changed every instance of the character Á to ×
• Added External signal description
• Moved block diagram related information to a new section: Block diagram
• Changed section title form "External interrupts/DMA requests (REQ Pins)" to External interrupts
• Added Clocking and has the following content: "This module has no clocking considerations".
• Added Initialization and has the following content: "This module does not require initialization".
• Updated DMA requests
• Throughout the register section, removed 'Register' from the register full names.
• In , replaced the existing content with: "See the IOMUX file attached to this document for more information" .
• In Block diagram , mentioned that REQ pins mean interrupt request pins.
• In External interrupts , mentioned that IRC means Internal RC Oscillator.
• Updated the Block diagram .
• IFCPR register is removed.

#### A.10 Touch Sensing Pin Coupling (TSPC)

#### A.10.1 Chip-specific TSPC information changes

No substantial content changes.

#### A.10.2 TSPC module changes

• Made editorial updates:
— Rephrased Overview .
— Added the following in Functional description " ... if any OBE within a group transitions from high to low, it enables
that group of OBEs for simultaneous transition from high to low ... ".
• Made editorial changes.
Table continues on the next page...

---

*Page 3322*

Release notes
• Moved TSPC register descriptions to the end of TSPC chapter, just before Glossary .
• In Logic block description , replaced "gated output buffer enables" with "gated OBEs".
• Added topic Submodules .
• Added topic Operation .
• Added topic Clocking .
• Added topic External signals .
• Added topic Application information .
• Added sentence "See the chip-specific TSPC configuration for group of pads information” in Features .

#### A.11 Crossbar Switch (AXBS)

#### A.11.1 Chip-specific AXBS information changes

No substantial content changes.

#### A.11.2 AXBS module changes

• Replaced "Master with Initiator" and "Slave with Target".

#### A.12 Peripheral Bridge (AIPS_Lite)

#### A.12.1 Chip-specific AIPS_Lite information changes

No substantial content changes.

#### A.12.2 AIPS_Lite module changes

No substantial content changes.

#### A.13 Direct Memory Access Multiplexer (DMAMUX)

#### A.13.1 Chip-specific DMAMUX information changes

No substantial content changes.

#### A.13.2 DMAMUX module changes

• Split the memory map within the DMAMUX register descriptions section into 2 sets of registers: one for channels having
triggering capability (having TRIG bit) and the other for channels that don't have triggering capability (and hence no
TRIG bit).
Table continues on the next page...

---

*Page 3323*

Release notes
• Split the memory map within the DMAMUX register descriptions section into 2 sets of registers: one for channels having
triggering capability (having TRIG bit) and the other for channels that don't have triggering capability (and hence no
TRIG bit).
• Added the footnote within the memory map as follows: "In this array, the index and offset values of the registers do not
increment in direct alignment. For details, see the register description."
• Added the section Glossary and cross-referenced the first-time usage of the terms.
• Updated Glossary .
• Updated the section Functional description .
• Updated Block diagram .
• Updated the image "DMAMUX triggered channels" ( Figure 24 ).
• Added a short introduction in Modes of operation .

#### A.14 Enhanced Direct Memory Access (eDMA)

#### A.14.1 Chip-specific eDMA information changes

No substantial content changes.

#### A.14.2 eDMA3 module changes

No substantial content changes.

#### A.15 INTM module changes

• Added Register links all over the document.
• Updated Figure 34 .
• Updated Registers, Initialization , Functional description topics.
• Topic Glossary removed.
• Updated Figure 34 .
• Updated Functional description with a note to mention only level type interrupts are supported.
• Added description of the term FCCU in bullet no.5 of the topic Initialization .
• Updated Figure 34 , added INTM_IRQSEL.
• Updated note in Functional description section.
• Removed ITRC occurence from Functional description.
• For INTM_TIMER0-INTM_TIMER3 registers, updated access of TIMER bit field to read only
• For INTM_TIMER0-INTM_TIMER3 registers, updated access of TIMER bit field to Read Write

---

*Page 3324*

Release notes

#### A.16 Semaphores2 (SEMA42)

#### A.16.1 Chip-specific SEMA42 information changes

No substantial content changes.

#### A.16.2 SEMA42 module changes

• Moved the block diagram to a new section Block diagram .
• Added following sections:
— Clocking
— Interrupts
— External signals
— Initialization
• Marked RSTGT_R[ROZ] as a reserved field in the field description table.
• Updated Functional description and Features .
• Updated Glossary .
• Updated GATEn state machine .
• Moved the Multi-core programming: software gates section to Functional description .
• Clarified the Function and field descriptions of Reset Gate Read (RSTGT_R) and Reset Gate Write (RSTGT_W)
register.
• Updated Clocking section.
• Moved the "Initialization" and "External signals" section above "Memory map/register defination" section.
• Added NOTE in Overview section.

#### A.17 Crossbar Integrity Checker (XBIC)

#### A.17.1 Chip-specific XBIC information changes

No substantial content changes.

#### A.17.2 XBIC module changes

• ESR.VLD: Updated the access to W1C
• Changed "Unsupported access type—an attempted write to a read-only register, for example" to "Writes to read-only
registers generate a transfer error" in "XBIC register description" section
• Added text "This module has no interrupts." in the Interrupts section
• Added text "XBIC has no external interface signals." in the External signals section
• Added text "This module does not require initialization." in the Initialization section
Table continues on the next page...

---

*Page 3325*

Release notes
• Added Glossary section. Glossary
• Removed section "Signal description" and moved the text "XBIC has no external interface signals." to External signals
section
• Removed "Writes to read-only registers generate a transfer error" from "XBIC register descriptions" section.
• Added note "Writes to read-only registers generate a transfer error" in the description of register XBIC Error Address
(EAR) EAR.
• Changed the access type of bitfield " VLD " in register ESR from "RO" to "RW".
• Updated note in register XBIC Error Address (EAR) to "An attempted write to this read-only register results in a transfer
error".
• Updated " Overview " section.
• Changed the access type of bitfield " VLD " of register ESR from "RW" to "RO".
• Updated the " Features ".
• Replaced "Master with Initiator" and "Slave with Target".

#### A.18 Extended Resource Domain Controller (XRDC)

#### A.18.1 Chip-specific XRDC information changes

No substantial content changes.

#### A.18.2 XRDC module changes

• Updated description of MRGD_W3_x[VLD]
• Overview :
— Added 'If the value of MDACFG m [NMDAR] is 1 ... rights granted for this DID.'
• Updated Process Identifier register.
• Added a bullet point "Register settings for DFMT1 direct domain assignment transactions" in Overview .
• Updated PID-based domain assignment and Transaction request sources .
• Added a bullet point "DFMT1 direct domain assignment example-single MDA" in XRDC transaction examples .
• Added DFMT1 direct domain assignment example-single MDA and its sub-sections.
• In MDA_W r _ m _DFMT1[PA] field description Note, changed "secure" to "privileged"
• Added the text "This module has no clocking considerations" in Clocking
• Added Interrupts
• Added a note in MDA register structure
• Updated description of (MDA_Wn_m_DFMT0[VLD]) and (MDA_Wn_m_DFMT1[VLD])
Table continues on the next page...

---

*Page 3326*

Release notes
• Added Exceptions and violations .
• Removed "Zipwire" and "FlexRay" from Transaction protection .
• Updated Handling domain access violation errors .
• Updated the register descriptions for DERR_W0_i, DERR_W1_i, and DERR_W3_i.
• Updated the register descriptions for DERR_W0_i and DERR_W1_i.
• Replaced "master" to "initiator" throughout the document.
• Added a note for deprecated terms in Overview .
• Updated Block descriptions , Peripheral ACP evaluation example , and Memory ACP evaluation example .

#### A.19 Memories and Memory Interfaces changes

No substantial content changes.

#### A.20 Embedded Flash Memory (c40asf)

#### A.20.1 Chip-specific C40asf information changes

No substantial content changes.

#### A.20.2 Embedded Flash Memory (c40asf)

No substantial content changes.

#### A.21 Flash Memory Controller (PFLASH)

#### A.21.1 Chip-specific PFLASH information changes

No substantial content changes.

#### A.21.2 PFLASH controller module changes

• Added PFAPR[M3AP] bit.

#### A.22 RAM Controller (PRAMC)

#### A.22.1 Chip-specific PRAMC information changes

No substantial content changes.

---

*Page 3327*

Release notes

#### A.22.2 PRAMC module changes

• Divided the Overview section to three sections: Overview , Block diagram , and Features .
• Added section for clocking, Clocking , and reset, Reset .
• Added Reset .
• Added information that the module has no external signals in the section External signals .
• Added information that the module has no interrupts in the section Interrupts .
• Block diagram - Removed internal content from the section.

#### A.23 Clocking module changes

No substantial content changes.

#### A.24 Clock Generation Module (MC_CGM)

#### A.24.1 Chip-specific MC_CGM information changes

No substantial content changes.

#### A.24.2 MC_CGM module changes

• Added following note in the MUX_n_DC_m register: Software-controlled clock multiplexer dividers are not expected to
return to the default state on the hardware transitions and handshakes occurring as part of the functional reset entry
sequence (only hardware-controlled clock multiplexer dividers return to the default state).
• Added following note in the MUX_n_DIV_UPD_STAT[DIV_STAT] field: This field clears once divider configuration is
updated or on destructive reset. If functional reset comes when this field is 1 then it can remain fixed to 1 until divider
input clock is restored.

#### A.25 FIRC module changes

• Added text "This module has no external signals." in External signals section
• Added text "This module does not require initialization." to Initialization section

#### A.26 SIRC module changes

• Added text "This module has no external signals" in the External signals section
• Added text "This module does not require initialization." in the Initialization section

#### A.27 Fast Crystal Oscillator Digital Controller (FXOSC)

#### A.27.1 Chip-specific FXOSC information changes

No substantial content changes.

---

*Page 3328*

Release notes

#### A.27.2 FXOSC module changes

• Added the CTRL[ALC_D] field.
• Updated Overview section as follows:
— Changed the title from "Introduction" to "Overview" in overview.dita file.
— Added "The Fast Crystal Oscillator (FXOSC) generates a clock which can be used at the SoC level. The FXOSC
has a digital interface to control and configure the oscillator to generate multiple frequencies. When FXOSC is
powered down at any time, it is designed not to generate any glitch at the output clock. A counter inside FXOSC
handles different stabilization times." in the overview section.
— Commented out "This module provides the fast crystal oscillator to the chip." in overview section.
• Removed the note "For bypass mode applications, the EXTAL pin should be driven low when FXOSC is in off/disabled
state" from "Functional Description" section.
• Removed the following notes from "Initialization section"
— "When the FXOSC is used in Bypass mode, the external clock source can only be enabled after the FXOSC is
enabled."
— "When the FXOSC is used in Bypass mode, the external clock source must already be inactive before disabling the
FXOSC."
• Added the new section Block diagram , , Clocking , External signals and Interrupts .
• Made changes in block diagram
• Replaced the content of Clocking section with "This module has no clocking considerations."
• Removed “to generate multiple frequencies” from the Overview section
• Added "CG cell is clock gating cell, it gates the clock till stabilization time." in Overview section
• Added note "When the FXOSC is used in Bypass mode, the clock from the external source can only be used after the
FXOSC is enabled" in Initialization section.
• Updated Block diagram section
• Added long description to CTRL[ALC_D] .
• In Initialization - Added a note "See Hardware design guide for further details and the recommended circuit for each
mode."
• Updated the section Clock generation in Single-Input Bypass mode .

#### A.28 SXOSC module changes

• Updated the reset value of CTRL register.
• Updated the bit field value of CURR_PRG_COMP , CURR_PRG_SF and GM_SEL .
• As per TKT0613245, parameterized the Bypass mode(to hide it for S32K3xx) and other points are already updated as
per TKT0611802.
• Fixed the shared review comments received on S32K5 RM.

---

*Page 3329*

Release notes

#### A.29 PLL Digital Interface (PLLDIG)

#### A.29.1 Chip-specific PLLDIG information changes

No substantial content changes.

#### A.29.2 PLLDIG module changes

• Note "The value of this field is 0b when the module is under reset." added in PLLSR[CGE_OUT2 and CGE_OUT1]
register.
• Maximum possible modulation depth formula added in "Frequency modulation programming" topic.
• Maximum possible modulation depth formula added for "When PLL[RDIV] is 0" in "Frequency modulation programming"
topic.
• LOL abbreviation added in LOL topic.

#### A.30 Reset module changes

No substantial content changes.

#### A.31 Boot Overview changes

No substantial content changes.

#### A.32 Reset Generation Module (MC_RGM)

#### A.32.1 Chip-specific MC_RGM information changes

No substantial content changes.

#### A.32.2 MC_RGM module changes

• Added following note in section Reset state machine : See chip-specific MC_RGM information for chip-specific reset
sequence details.
• Added following note in DES[ F_POR ] field: All reset status in the FES and DES registers must be ignored at power up if
this field is set.
• Removed the following content from Functional reset entry timer : See the chip-specific section for the default value of
timeout value and the details on the chip behavior in case of functional reset entry timer timeout.
• Added section: Alternate event generation .
• Removed the following statement from RESET_B pin assertion and pin safe state control : See STCU2 chapter for more
details on its usage.
• Added following note in Functional reset escalation : Functional counter increments for each reset source for which
escalation is enabled. For details, see "Reset sources" table in the Reset chapter.
Table continues on the next page...

---

*Page 3330*

Release notes
• Added following note in Destructive reset escalation : Destructive counter increments for each reset source for which
escalation is enabled. For details, see "Reset sources" table in the Reset chapter.
• Editorial changes.
• Updated Figure 133 .
• In Features , removed the first three features and added the following:
— Destructive and functional reset management
— Capturing the reset sources for each reset sequence (reset status flags)
— Assertion the Reset_B pin to propagate the reset sequence out of chip
• Changed the "Reset value" from 0 to 1 in Table 182 .
• Updated the description in Table 182 .
• Updated the last statement in RESET_B pin assertion and pin safe state control
• In Functional reset entry sequence , updated the second last paragraph for clarity.
• In Functional reset exit sequence , updated the second bullet.
• Moved content from External reset to RESET_B assertion control .
• Renamed the "External reset assertion control" section to RESET_B assertion control .
• Updated the second paragraph of the Destructive reset escalation for clarity.
• Updated the description of FREC , FRET[ FRET ] and DRET[ DRET ].
• In Reset sources , removed "and the MC_RGM itself" from the last statement of the third paragraph.
• In Reset sources , removed "(for example, debug modules, flash modules)" from the last statement of the last paragraph.
• Updated the Figure 134 .
• Updated the note in Functional /External Reset Status Register (FES) .
• In External signal description , updated the reset value of " RESET_B " signal.
• Updated the note in Functional /External Reset Status Register (FES) .
• Updated Figure 133 and Figure 134 .
• Removed "(except for a small portion, for example FIRC, JTAG interface)" from Reset sources .
• Updated references to reset chapter to make them generic.
• Updated the note in DES[ F_POR ].
• Updated the note in Functional /External Reset Status Register (FES) .
• Updated the note in Functional /External Reset Status Register (FES) .
• Updated the description of the Functional /External Reset Status Register (FES) .
• Removed the fields: MODE_CONF[POR_RST], MODE_CONF[FULL_WKUP], MODE_CONF[Standby].
• Updated Figure 134 .
Table continues on the next page...

---

*Page 3331*

Release notes
• Removed Standby mode related information from the chapter.
• Removed "LPRR_CONF" register.

#### A.33 POR_WDG module changes

No substantial content changes.

#### A.34 Security overview changes

No substantial content changes.

#### A.35 HSE_B module changes

No substantial content changes.

#### A.36 DCF module changes

No substantial content changes.

#### A.37 Chip-specific DCM information changes

No substantial content changes.

#### A.38 DCM module changes

• Removed the content related to CUST_DEL, NXP_PWD and MCU_PROD from the chapter. Removed the
DCMLCC[DCMFLC] and DCMLCC[DCMLCFN] bits.
• Changed the access of Reserved bits 3,8 and 9 from ROz to RW in DCMLCC register.
• Changed the access of Reserved bits 0 to 11 from ROz to W1C in DCMLCS register.
• Revised Overview , Figure 139 , and Features .
• Revised DCF mechanism , removed the content duplicate with DCF chapter of RM.
• Revised DCF client error mechanism , changed CERS to DCMCERS in figure title.
• Revised LC to update information of valid and invalid field types, and Table 196 to change PFA to Pre_FA.
• Revised Glossary , added OTP and DCF.
• Deleted DCMSTAT[9] DCMOTAS.
• Revised the description of DCM Status (DCMSTAT) , LC and LC Control (DCMLCC) , LC Scan Status (DCMLCS) ,
LC Scan Status 2 (DCMLCS_2) , DCMSTAT[DCMLCST] , , DCMMISC[DCMDBGE] , DCMMISC[DCMDBGT] , DCF Error
Count (DCMEC) , DCMEC[DCMECT] , DCMSRRn , DCMSRR1[DCMESF1] , and DCMSRR1[DCMDCFF1] .
• Revised DCM error detection mechanism and Figure 141 . Also, changed DCF to DCM in topic and figure title.

---

*Page 3332*

Release notes
• Revised DCF client error mechanism , Figure 140 and changed its title to "DCF error handling".
• Revised Features , updated debug related information.
• In LC , revised Table 196 .
• In Glossary , added PFC.
• Revised description of LC and LC Control (DCMLCC) .
• Revised DCMLCS[DCMLCE5] , DCMDEB[DCM_APPDBG_STAT] , DCMSRR1[DCMDCFF1] .

#### A.39 Messaging Unit (MU)

#### A.39.1 Chip-specific MU information changes

No substantial content changes.

#### A.39.2 MU module changes

• Updated the reset value of the SR[MURS] register bit field as undefined.
• Revised Figure 143 - changed "Processor A side" and "Processor B side" to "MUA" and "MUB" respectively.
• Added new topics Submodules and Initialization .
• Updated Glossary .
• Added Clocking - "This module has no clocking considerations.".
• In Glossary and Features , corrected MUA and MUB from Message Unit A and Message Unit B to Messaging Unit A and
Messaging Unit B respectively.
• Changed "external memory", and "memory array" to "shared memory" in Functional description .
• Added new clock "Bus clock" in "Clocking".
• Removed topic "Wake up core in low power mode".
• Revised Interrupts topic.
• Revised Table 201 .
• Revised , TR0-TR3[TR_DATA], RR0-RR3[RR_DATA].
• Revised Table 206 .
• Revised access type and reset value of CCR0[2].
• Revised access type and reset value of CCR0[4].
• Added note in MUA side and MUB side .
• Updated Asynchronous system reset .
• Deleted topic "Sending messages with interrupt" and revised Initialization to add information "MU does not require
initialization.".
• Updated topics MUA side and MUB side , revised notes.
Table continues on the next page...

---

*Page 3333*

Release notes
• Updated the description of SR[TEP] and RSR[RFn].
• Updated receive and transmit interrupts related points in Interrupts .

#### A.40 Power management changes

No substantial content changes.

#### A.41 PMC changes

No substantial content changes.

#### A.42 PMC changes

No substantial content changes.

#### A.43 Mode Entry Module (MC_ME)

#### A.43.1 Chip-specific MC_ME information changes

No substantial content changes.

#### A.43.2 MC_ME module changes

• Added following note in Partition 0 Process Configuration Register (PRTN0_PCONF) , Partition 0 Process Update
Register (PRTN0_PUPD) , Partition 0 Status Register (PRTN0_STAT) : See chip-specific MC_ME information to check if
this register is implemented on chip.
• Added Main Core ID Register (MAIN_COREID) .

#### A.44 MC_PCU changes

• Updated Table 217 .
• Updated Power sequence FSM .
• Updated first statement in the second paragraph of Introduction .

#### A.45 Wakeup Unit (WKPU)

#### A.45.1 Chip-specific WKPU information changes

No substantial content changes.

#### A.45.2 WKPU module changes

No substantial content changes.

---

*Page 3334*

Release notes

#### A.46 Safety overview changes

No substantial content changes.

#### A.47 Error Injection Module (EIM)

#### A.47.1 Chip-specific EIM information changes

No substantial content changes.

#### A.47.2 EIM module changes

• In Functional description , added information about enabling/disabling error injection.
• Added Initialization section stating that this module does not require initialization.
• Updated the definition of SEC-DED .
• Updated the first paragraph of Overview .
• Updated revision history title to "EIM module changes".
• Added NOTE in Overview section.

#### A.48 Error Reporting Module (ERM)

#### A.48.1 Chip-specific ERM information changes

No substantial content changes.

#### A.48.2 ERM module changes

• Updated the description of the SR registers.
• Added following note in the "ERM register descriptions" section: To access the channel registers, corresponding channel
clocks must be enabled.
• Added following note in the Non-correctable error events : Parity errors can be mapped to non-correctable errors where
error attributes like SYNDROME, ADDRESS are not provided.
• Updated the last statement of the Overview as follows: ERM does not capture syndrome or error address for cache
memories or memory with parity instead of ECC.
• Added following note in the "ERM Memory a Correctable Error Count Register": Non-correctable errors are considered a
serious fault, so the ERM does not provide any mechanism to count non-correctable errors. Only correctable errors are
counted.
• Removed the following statement from the CR n [ENCIE m ] and CR n [ESCIE m ] fields: This field is initialized by hardware
reset.
Table continues on the next page...

---

*Page 3335*

Release notes
• Added following note in the "ERM Memory a Error Address Register": This register provides the offset of error location
and not the absolute address. For example, if memory is SRAM0 (which starts from 0x20400000) if error occurs at
0x20400010 the EAR register gives value 0x10 and not 0x20400010.
• Removed following note from the "ERM Memory a Error Address Register": This register provides the offset of error
location and not the absolute address. For example, if memory is SRAM0 (which starts from 0x20400000) if error occurs
at 0x20400010 the EAR register gives value 0x10 and not 0x20400010.
• In introductory text of "ERM register descriptions", added information related to the XFR error behavior at the IPS
interface.
• Added Figure 178 .

#### A.49 Fault Collection and Control Unit (FCCU)

#### A.49.1 Chip-specific FCCU information changes

No substantial content changes.

#### A.49.2 FCCU module changes

• Note "Software reset of FCCU by MC_RGM_PRST5[FCCU_RST] bit is optional since FCCU registers are not initialized
during functional reset. MC_RGM_PRST5[FCCU_RST] has the same effect on FCCU as power on reset. If the bit is
activated, it can change the state of the error pin, if the current state of the pin is different from default." removed from
topic "Use cases and limitations".
• Reset interface topic moved into chip specific section.
• The equation changed from "Tmin = 250 μs + DELTA_T to "Tmin = (250us + DELTA_T) * (SAFE_CLK_FREQ/CLKSAFE
freq).
• Added a topic External signals .
• Added EIN input signal in block diagram.
• Note "The FCCU is not reset by short functional resets" deleted from BG.
• Updated the description of "Not Fault-Toggle" value of Timer state in FAULT state of Fault-Output Timer (TMR_ETMR)
register.
• Updated the External signals .
• For EINOUT , updated the bit field description for EINOUT[EIN1] and EINOUT[EIN0].

#### A.50 SELFTEST_GPR module changes

No substantial content changes.

---

*Page 3336*

Release notes

#### A.51 Self-Test Control Unit (STCU2)

#### A.51.1 Chip-specific STCU2 information changes

No substantial content changes.

#### A.51.2 STCU2 module changes

• In Example: Multi-phase BIST sequence , changed the BIST number from 38 to 30 for phase 0.
• Removed MTR from the function of "MBIST interface" in Table 240 .
• Updated the description of the STCU2 Online LBIST Unrecoverable FM (LBUFM0) .
• Removed instances of "INT_FLG" from Peripheral bus interface .
• Added title to Figure 189 .
• Updated the description of STCU2 SK Code (SKC) .
• Removed topic "Use cases and limitations".
• Revised FSM description , changed state machine to FSM.
• Revised Peripheral bus interface and Glossary .
• Revised description for CFG[PTR] , CFG[CLK_CFG] , CFG[LB_DELAY] , LB_CTRL0[PTR] , and MB_CTRLn[PTR] .
• Revised description for MBSSWn and MBESWn.

#### A.52 Register Protection (REG_PROT)

#### A.52.1 Chip-specific REGPROT information changes

No substantial content changes.

#### A.52.2 REG_PROT module changes

No substantial content changes.

#### A.53 Clock Monitoring Unit – Frequency Check (CMU_FC)

#### A.53.1 Chip-specific CMU_FC information changes

No substantial content changes.

#### A.53.2 CMU_FC module changes

• Updated Features , to remove clocking information.
• Added separate section for clocking, Clocking .
Table continues on the next page...

---

*Page 3337*

Release notes
• Added Reset .
• Added information that the module has no external signals in the section External signals .
• In Features
— Changed "Timeout functionality generates an FLL event if a monitored clock stops functioning" to "Timeout
functionality generates an FLL event (if enable) when monitored clock is very slow or stop functioning."
• Updated the Features .
• Updated the Operating modes .

#### A.54 Clock Monitoring Unit – Frequency Meter (CMU_FM)

#### A.54.1 Chip-specific CMU_FM information changes

No substantial content changes.

#### A.54.2 CMU_FM module changes

• Updated Features , to remove clocking information.
• Added separate section for clocking, Clocking .
• Added Reset .
• Added information that the module has no external signals in the section External signals .
• Fixed the Minimum RCCR[REF_CNT] formula calculation in Programming RCCR[REF_CNT] .

#### A.55 Cyclic Redundancy Check (CRC)

#### A.55.1 Chip-specific CRC information changes

No substantial content changes.

#### A.55.2 CRC module changes

• Renamed the section "Introduction" to Overview
• Added the section External signals .
• Moved the section Modes of operation within the Functional description section.
• Moved the section Initialization after the Functional description section.
• Updated the following sections: Features , Calculating a 16-bit CRC , Memory map and register descriptions , Expected
read data fields , and CTRL programming
• Updated the following section: Memory map and register descriptions .
• Updated Calculating a 32-bit CRC .
Table continues on the next page...

---

*Page 3338*

Release notes
• Added Clocking and Interrupts .
• Renamed section title "CRC initialization and reinitialization" to "Initialization".
• Updated the long description of CTRL[WAS].
• Updated Clocking .
• In section Calculating a 16-bit CRC cross reference in step 2 “Program the transpose and complement options in Data
(DATA) as required for the CRC calculation” )" changed to "Control (CTRL)".
• In section Calculating a 32-bit CRC cross reference in step 7 “CRC is computed on every data value write and the
intermediate CRC result is stored back into DATA[LU] and DATA[LL]” changed to "DATA".
• In register DATA[HL] long description updated to "Generates CRC checksum in both 16-bit and 32-bit CRC modes if
CTRL[WAS] = 0."
• Updated the description of Data (DATA) register.
• Updated the description of DATA[LL] .
• Updated Low-power modes and Clocking

#### A.56 PCMC changes

No substantial content changes.

#### A.57 Analog-to-Digital Converter (ADC)

#### A.57.1 Chip-specific ADC information changes

No substantial content changes.

#### A.57.2 ADC module changes

• In AMSIO register: reserved bit AMSIO[16] was made visible to customer as AMSIO[CMPCTRL0], deleted text from
function "you must write 3d to HSEN" and updated the description of field 18-17.
• In Clock section, the table "Clock configuration for highest conversion speeds" was updated with the new
recommendation, that is for freq > 80MZ the setting AMSIO[HSEN]=01b and AMSIO[CMPCTRL0]=1b shall be used.
• In Clock section, deleted text "For some conversion clock frequencies, you must write 3d to AMSIO[HSEN]".
• In Conversion time section, change the text from "The evaluation time of a single bit is 4 AD_clk cycles if AMSIO[HS] =
0h, and 5 AD_clk cycles if AMSIO[HS] = 1h." to "The evaluation time of a single bit is 4 AD_clk cycles if AMSIO[HSEN]
= 0h and AMSIO[CMPCTRL0] = 0b. The evaluation time of a single bit is 6 AD_clk cycles if AMSIO[HSEN] = 1h and
AMSIO[CMPCTRL0] = 1b."
• Deleted statement about analog watchdog programming from topic Analog watchdog configuration (up to 16 watchdogs)
"The threshold values to be selected for a channel must be programmed only once in CWSELRaIn."
• CDATA bit field description updated to "Contains conversion data from standard input n, determined by the SAR
algorithm. The conversion data bit count is dependent on the conversion resolution selected (CALBISTREG[RESN]).
Depending on the value of MCR[WLSIDE], the conversion data MSB bits start from 14 (MCR[WLSIDE] = 0) or 15
(MCR[WLSIDE] = 1).

---

*Page 3339*

Release notes

#### A.58 Low Power Comparator (LPCMP)

#### A.58.1 Chip-specific LPCMP information changes

No substantial content changes.

#### A.58.2 LPCMP module changes

• Removed references to "hard block" from CCR2[HYSTCTR] in register file for clarity.
• In Functional description , edited the title.
• Edited register file.
• Added Glossary of terms.
• Changed the name of the "Clocks" section to Clocking .
• Changed the name of the "Trigger Mode" section to Round-robin trigger mode and clarified the full name of the Round
Robin Trigger mode.
• Clarified Latency issues .
• Added register links to Initialization .
• Updated External signal descriptions with RR_ACTIVE related description.
• Updated Block diagram with RR_ACTIVE and Round_robin FSM.
• Updated Round-robin trigger mode with RR_ACTIVE wave form.
• Updated Block diagram , including input channels and register references using REG[FIELD].
• Updated Initialization .
• Updated Application information .
• Reserved CCR3 register in LPCMP register descriptions .
• Updated LPCMP register descriptions by adding a note to Comparator Status (CSR) .
• Updated Round-robin clock (RCLK) frequency requirement by changing "ms" to "μs".
• In LPCMP register descriptions , updated "ACMP" with "LPCMP" in the Note of CSR register.
• Added Version ID (VERID) register.

#### A.59 Logic Control Unit (LCU)

#### A.59.1 Chip-specific LCU information changes

No substantial content changes.

---

*Page 3340*

Release notes

#### A.59.2 LCU module

• Updated block diagram to show outputs that go to IO pads
• LC Diagram Revised the LC Diagram to update the LC0_FCTRL[FORCE_MODE0] mux values from 0 1 2 3 to 0 2 1 3.
• LC n Sync Control Revised the conditions for bit 8 and 9 to show bit 9 as Reserved whenever the input sync value is 1
or 2.
• Mux Select Added note "Access to Address space 200h + (a x 4h) does not generate a transfer error".
• Updated description of CFG[WP] .
• Updated the bit information in title for Mapping of 12-bit input, output, and state fields to reflect the correct bit value.
• Changed bit 9 of LC n Sync Control (LC0_SCTRL - LC2_SCTRL) to RW from ROZ.
• Added note in LC output filters .
• Added new topic DMA .
• Revised Interrupts .
• Revised the long description for LC0_FFILT[FORCE_POL] .
• Removed "Glossary" section.
• Revised Features , LC diagram .
• Revised Figure 251 , and Figure 252 to remove sync 2 and 3 inputs.
• Revised the long description of DBGEN[DBGEN] .
• Changed 1h to Eh in Table 299 for LUTCTRL0.
• Revised long description for LC0_SCTRL[SW_MODE] .
• Revised Features to update register information.
• Revised Figure 251
• Revised Block diagram to remove the note "3 LCs are shown in this block diagram.".
• Revised Figure 252 .

#### A.60 Enhanced Modular IO Subsystem (eMIOS)

#### A.60.1 Chip-specific eMIOS information changes

No substantial content changes.

#### A.60.2 eMIOS module changes

• Changed bit position from 0 to 31 for AS2 in Effect of C n [EDPOL] on edge capturing and SAIC submodes and MODE
field values
• Added bitfield A n [RISE_FALL] .
Table continues on the next page...

---

*Page 3341*

Release notes
• Editorial updates.
• Deleted counter bus E from figures in UC block diagram and UC control and datapath .

#### A.61 Body Cross-triggering Unit (BCTU)

#### A.61.1 Chip-specific BCTU information changes

No substantial content changes.

#### A.61.2 BCTU module changes

• Topic External Signals removed.
• Minor correction in figures of Trigger timing and Simultaneous trigger priority management .
• Updated Implementation .
• Updated no of triggers and ADC to (n) in Block diagram .

#### A.62 Trigger MUX (TRGMUX)

#### A.62.1 Chip-specific TRGMUX information changes

No substantial content changes.

#### A.62.2 TRGMUX module changes

• Renamed the "Introduction" section to Overview .
• Nested Features within the "Overview" section.
• In #unique_3756/unique_3756_Connect_42_bitFieldTable , updated values for the SELx field from binary to hex format.
• In #unique_3757 , updated description for SELn fields.
• Added Block diagram
• Updated Features to remove block diagram.
• Added Functional description
• Removed "Memory map and register definition" section.
• Removed "Memory map and register definition" section:
— Moved following content from the "Memory map and register definition" section to "TRGMUX memory map"
sections:
"You can only write to TRGMUX registers in Supervisor mode."
— Removed redundant content "The TRGMUX registers contain fields for selecting trigger sources for peripheral
modules."
Table continues on the next page...

---

*Page 3342*

Release notes
• Editorial updates:
— Renamed all the registers from "TRGMUX Reg_Name Register ( Reg_Name )" to "TRGMUX Reg_Name
( Reg_Name )", here Reg_Name refers to the register name.
— Updated the description of all the fields in all the registers.
• In Block diagram , updated reference to chip-specific TRGMUX information.
• In #unique_3757 , removed reference to chip-specific TRGMUX information.

#### A.63 Software Watchdog Timer (SWT)

#### A.63.1 Chip-specific SWT information changes

No substantial content changes.

#### A.63.2 SWT module changes

• In CR[FRZ] , replaced "chip" with "core."
• In Behavior in different chip and core modes , replaced "chip" with "core."
• Edited throughout.
• Added Block diagram section.
• Added Clocking section.
• Added Reset section.
• Added Interrupts section.
• Updated Block diagram section.
• Updated Reset section.
• Updated Initialize the SWT section to remove the bullet "Select the clock source (if there are more than one possible
sources)".
• Removed 'Select the clock source' section.
• Updated Clocking section to add the following details "See the chip-specific SWT information to find the clock source
that drives the countdown timer."
• Updated terminology in this chapter as follows:
— Updated "Master" to "Initiator"
• In Clocking , added reference to the chip-specific SWT information for the clock source of the counter and reset logic.

#### A.64 System Timer Module (STM)

#### A.64.1 Chip-specific STM information changes

No substantial content changes.

---

*Page 3343*

Release notes

#### A.64.2 STM module changes

• Renamed "Introduction" to Overview .
• Restructured to conform to BGAC outline.
• Added the Clocking topic.
• Added the Interrupts topic.
• Restored the External signals topic.
• Added the Initialization topic.

#### A.65 Periodic Interrupt Timer (PIT)

#### A.65.1 Chip-specific PIT information changes

No substantial content changes.

#### A.65.2 PIT module changes

• Updated description of MCR[MDIS] . Changed the sentence “You must write 1 to this field before setting up anything”
to “You must ensure that the clock is disabled (MDIS = 1) before configuring PIT. After configuration, user must enable
clock (MDIS=0).”
• Updated Modes of operation to include Debug mode.
• Added STOP mode section.
• Rearranged feature list for clarity and added an item for the number of timers.
• Rewrote all register and field descriptions for clarity and usability except LTMR64L.
• Removed STOP mode section.
• Removed unnecessary "Preface" section.
• Rewrote the following sections for clarity and usability.
— LTMR64H and LTMR64L register and field descriptions
— Interrupts
• Renamed "Introduction" to Overview and rewrote for clarity.
• In Modes of operation , replaced bulleted list with a table.
• Added new Clocking topic.
• Added new External signals topic.
• Corrected the link present in note of register RTI_LDVAL_STAT .
• Moved Modes of operation under Functional description .
• Updated in the sub-sections of Functional description .
• Updated in Application information and its sub-sections.
Table continues on the next page...

---

*Page 3344*

Release notes
• Updated the access row in "PIT memory map".
• Updated register function description in PIT Module Control (MCR) .
• Updated description in RTI_LDVAL_STAT[RT_STAT] .
• Updated description in RTI_TFLG[TIF] .
• Added text "See LTMR64H and LTMR64L." in TCTRL0[CHN] ."
• Updated description in TFLG0[TIF] .
• In Example configuration using chained timers :
— Updated the example configuration to (60 s/10 ns).
— Updated the step 6 and 7 in the configuring PIT.
— Updated the example code for Disable PIT to "PIT_MCR = 0x02"
• Updated the example code for Disable PIT to "PIT_MCR = 0x02" in Example configuration using the lifetimer .
• Updated the example code for Disable PIT to "PIT_MCR = 0x02" in Example configuration for general timers .
• Updated the section headings of Restart counter period , Change current timer period and Dynamically setting a new
timer period .
• Replaced "...when it expires (CVALn - 0)" to "...when it expires (CVALn = 0)" in Chained timers .
• In PIT register descriptions , changed PIT_RTI to PIT.
• Moved the statements about chip-specific PIT information from PIT register descriptions to Functional description and
rewrote it.
• In Example configuration using chained timers , corrected "(60 s/10 ns)" to "(60 s/100 ns)".
• In Clocking , added reference of the Clocking chapter.

#### A.66 Real Time Clock (RTC)

#### A.66.1 Chip-specific RTC information changes

No substantial content changes.

#### A.66.2 RTC module changes

• Updated last para of RTC to :In case, RTC wakeup's are used as a wakeup source, bus clock (system clock or register
interface clock of RTC) should be disabled (to save power in standby mode) after enabling the required wakeup and
ensuring sufficient time gap (3-6 bus clock or RTC clock (rtc_clock) cycles, whichever is slower) between bus clock
disabling and wakeup event. To ensure writing the register to enable the wakeup, the bus clock must be active, hence it
should not be disabled before enabling wakeup. Correct operation is not guaranteed if the specification is not followed.
• Changed "ACMP" into "CMP" in TRIG_EN .
• Modified note in CLKSEL .
• Added a note "See the chip-specific RTC information and clocking chapter for mapping of clock sources" in Features .

---

*Page 3345*

Release notes

#### A.67 Low Power Serial Peripheral Interface (LPSPI)

#### A.67.1 Chip-specific LPSPI information changes

No substantial content changes.

#### A.67.2 LPSPI module changes

• In Controller mode , Transmit and command FIFO commands , and Transmit Command (TCR) , clarified that LPSPI must
be enabled to update command word.
• Updated Features .
• Updated Timing parameters .
• Added a warning on writing data to Clock Configuration (CCR) .
• Moved "SPI bus transfers" content from Transmit and command FIFO commands to subtopic
• Moved "Circular FIFO" content from Transmit and command FIFO commands to subtopic
• Updated CR[MEN] .
• Updated Timing parameters .
• Updated CCR[SCKDIV] .
• Updated Features .
• Updated CFGR1[SAMPLE] .
• In LPSPI memory map , the access display changed to "See description" for some registers that have mixed access
types.
• Updated LPSPI register descriptions description.
• Updated CFGR1[PARTIAL] .
• Updated CFGR1[MATCFG] description and notes in Data Match 0 (DMR0) and Data Match 1 (DMR1) .
• Removed Low-power mode.
• Updated Interrupts and DMA requests .
• Removed paragraph from introduction to register descriptions, content is in Overview .
• Updated IER[REIE] and IER[TEIE] .
• Updated CFGR1[PCSPOL] .
• Updated CFGR1[SAMPLE] description.
• Updated RSR[SOF] .
• Updated Transmit Command Burst (TCBR) , Transmit Data Burst (TDBR0 - TDBR127) , and Receive Data Burst (RDBR0
- RDBR127) .
• Added DMA support registers .
Table continues on the next page...

---

*Page 3346*

Release notes
• Replaced "master" and "slave" with "controller" and "peripheral" throughout chapter.
• Updated note on CFGR1[PINCFG] .
• Clarified note on CFGR1[PINCFG] .
• Clarified description in Timing parameters , to say that you can only change CCR when LPSPI is disabled
(CR[MEN] = 0), previously it said you cannot change CCR when LPSPI is busy.

#### A.68 Low Power Inter-Integrated Circuit (LPI2C)

#### A.68.1 Chip-specific LPI2C information changes

No substantial content changes.

#### A.68.2 LPI2C module changes

• Changed MCFGR0[1] to reserved.
• Added MCFGR0[HRPOL] .
• Updated Low-power modes , Interrupts and DMA requests , Controller mode , and Target mode .
• Added cross references to LPI2C register descriptions .
• Updated MCFGR1[IGNACK] .
• Changed title of External signals
• Updated Low-power modes .
• Changed MCFGR0[3] reserved field to RW from ROZ.
• Updated Low-power modes .
• Updated Controller Configuration 1 (MCFGR1) register.
• Updated access type on MCRGR0[3] in Controller Configuration 0 (MCFGR0) register.
• In External signals , updated I2C Four-wire serial bus figure.
• Updated Timing parameters .
• Updated MCR[RST] description.
• Moved Interrupts and DMA requests and Peripheral triggers .
• Updated Overview .
• Added Initialization and Application information .
• Updated Timing parameters .
Table continues on the next page...

---

*Page 3347*

Release notes
• Text moved from Memory map and registers to LPI2C register descriptions .
• In memory map table, the access changed for some registers that have mixed access types.
• In Version ID (VERID) , removed "Read-only field." from field descriptions.
• In Controller Status (MSR) , updated the descriptions of fields: DMF, PLTF, FEF, ALF, NDF, SDF, and EPF.
• Updated MCFGR1[TIMECFG] .
• Updated Controller Configuration 2 (MCFGR2) , MCFGR2[FILTSDA] , and MCFGR2[FILTSCL] .
• Updated Controller Configuration 3 (MCFGR3) description and MCFGR3[PINLOW] description.
• Updated MDMR[MATCH1] and MDMR[MATCH0] .
• Updated Controller Clock Configuration 0 (MCCR0) , Controller Clock Configuration 1 (MCCR1) , and Controller FIFO
Control (MFCR) .
• Updated Controller FIFO Control (MFCR) description, MFCR[RXWATER] , and MFCR[TXWATER] .
• Updated MTDR[CMD] and MTDR[DATA] .
• Updated SCR[RRF] and SCR[RTF] .
• In Target Status (SSR) , updated description in fields: FEF, BEF, SDF, RSF, and AVF.
• Updated Target Configuration 1 (SCFGR1) description, SCFGR1[RXCFG] , and SCFGR1[TXCFG] descriptions.
• Updated descriptions in Target Configuration 2 (SCFGR2) , Target Address Match (SAMR) , and Target Address Status
(SASR) .
• Updated Error conditions .
• Updated Features .
• Updated Address matching .
• Updated Timing parameters .
• Updated Controller operations .
• Updated Pin configuration .
• Updated MCFGR0[HRPOL] description.
• Updated Pin configuration .
• Updated Controller FIFO Control (MFCR) description.
• Updated Controller Control (MCR) fields, RRF and RTF.
• Updated Target Configuration 1 (SCFGR1) references to SSR register.
• Updated Target DMA Enable (SDER) references to SSR register.

#### A.69 Flexible I/O (FlexIO)

#### A.69.1 Chip-specific FlexIO information changes

No substantial content changes.

---

*Page 3348*

Release notes

#### A.69.2 FLEXIO module changes

No substantial content changes.

#### A.70 CAN (FlexCAN)

#### A.70.1 Chip-specific FlexCAN information changes

No substantial content changes.

#### A.70.2 FlexCAN module changes

• In Overview , removed the text "See the chip-specific FlexCAN information for the actual number of message buffers
configured in the chip".
• In Features , Overview and , updated the ISO 11898-1 to ISO 11898-1:2015.
• In Protocol timing , added text "It is the smallest time unit for all configuration values".
• Updated the ISO 11898-1 to ISO 11898-1:2015 throughout the chapter.
• In Bus interface added the following registers:
— EPRS
— ENCBT
— EDCBT
— ETDC
• In Enhanced RX FIFO Point 5, changed EFRCR[NFE] to ERFCR[NFE].
• In FlexCAN initialization sequence , added text "FlexCAN memory must be initialized before switching to functional
mode".
• Changed the topic title for External signal descriptions from "FlexCAN signal descriptions" to "External signal
descriptions".
• In Enhanced RX FIFO , corrected register EFRCR to ERFCR.
• Revised Reset to add information about hard and soft reset.
• Interchangeable terms "message buffer" and "mailbox" consolidated to "message buffer" throughout chapter.
• In Clocks , clock signal names changed from "ipg_clk*" to "MODULE_CLK*".
• Added note in register section.
• Revised topic Reset .
• Added "NOTE" in MCR[SOFTRST] .
• Updated Features .
• Updated Freeze mode .
Table continues on the next page...

---

*Page 3349*

Release notes
• Removed the column "Affected by hard reset" from the table #unique_2735/unique_2735_Connect_42_id-25951b .
• Updated "NOTE" in MCR[LPMACK] and MCR[FRZACK].
• Updated long description of ERFSR[ERFCLR].
• Updated the section legacy RX FIFO structure (corrected the extended ID range in RXIDB_0 of format B from "296-" to
"29-16").
• Updated the section Reset .
• Combined notes into one in Overview .

#### A.71 Synchronous Audio Interface (SAI)

#### A.71.1 Chip-specific SAI information changes

No substantial content changes.

#### A.71.2 SAI module changes

• Updated TCR2[BCI] and RCR2[BCI] with note about enabling buffers.
• Updated field descriptions: TCSR[FR], TCSR[SR], TCSR[FWDE], TCR1[TFW], TCR5[WNW], TCR5[W0W], TCR5[FBT].
RCSR[FR], RCSR[SR], RCSR[FWDE], RCR1[RFW], RCR3[WDFL], RCR4[FRSZ], RCR4[SYWD], RCR5[WNW],
RCR5[W0W], and RCR5[FBT].
• In Receive Configuration 5 (RCR5) , updated description.
• Updated access type on RCR4[5].
• Updated reserved fields TCSR[30] and RCSR[30].
• Updated reserved field RCR4[5].
• Added Initialization .
• Moved Interrupts and DMA requests .
• Updated Synchronous modes .
• Corrected bit field descriptions for TCR2[BCP] and RCR2[BCP].
• Updated TCR1[TRW] .
• In Block diagram , changed input clock name to MCLK.
• Throughout, changed audio controller clock to audio clock or audio clock (MCLK).

#### A.72 Ethernet Media Access Controller (EMAC)

#### A.72.1 Chip-specific EMAC information changes

No substantial content changes.

---

*Page 3350*

Release notes

#### A.72.2 ENET_Controller module changes

• MAC_VLAN_HASH_TABLE register: Updated description as 'The upper four bits of the calculated CRC value are used
to index the contents of the VLAN hash table'
• MAC_VLAN_Tag.DOVLTC: Added 'The VLAN filter is bypassed when VLAN type ... VLAN type in the VLAN filter'
• Renamed "Signal descriptions" to External signals .
• Renamed "Introduction" to Overview .
• Renamed "Functional block diagram" to Block diagram .
• Corrected spelling errors
• Removed SGMII from figure: System-level block diagram
• TDES3 context descriptor : Added note 'This error is categorized as an abnormal event ...'
• Features : Added note: "Only byte-aligned ..."
• Updated registers MAC_Hash_Table_Reg0 and MAC_Hash_Table_Reg1 to add the note "In this chip, upper 6 bits
should be used for 64-bit Hash Table".
• Deleted DMA_Safety_Interrupt_Status[DECIS] and DMA_Safety_Interrupt_Status[DEUIS] and added
DMA_Safety_Interrupt_Status[1:0] as reserved.

#### A.73 Low Power Universal Asynchronous Receiver/Transmitter (LPUART)

#### A.73.1 Chip-specific LPUART information changes

No substantial content changes.

#### A.73.2 LPUART module changes

• Updated the following sections: Receiver wake-up operation , and Peripheral triggers .
• Updated the description of Watermark (WATER) .
• Updated the bit field descriptions of PF and TXDIR .
• In BAUD[OSR] , added a note to read "BAUD[OSR] in this bit field results an OSR of BAUD[OSR]+1, for example,
BAUD[OSR] = 0_0101b results in final divide by 6".
• In Receiver functional description , updated "the data character is transferred to receive FIFO, resulting the bit
STAT[RDRF] to 1".
• In Transmitter functional description , updated "the value waiting transmit FIFO is transferred to the transmit shift
register...". Replaced "STAT[TDRE]" with "transmit FIFO".
• Updated 0b and 1b value for STAT[RDRF] .
• In WATER[TXWATER] and WATER[RXWATER] , added "generates an interrupt". This now reads as: "Generates an
interrupt, or a DMA request when the number of datawords in the transmit FIFO or buffer is equal to or less than the
value of this field".
Table continues on the next page...

---

*Page 3351*

Release notes
• Updated SBR 0 in BAUD[SBR] .
• Updated Clocking .
• Added Reset .
• Added Initialization .
• Added functions in the following registers: Global (GLOBAL) , Pin Configuration (PINCFG) , Baud Rate (BAUD) , Status
(STAT) .
• Updated BAUD[SBR] .
• Replaced the instance of "IrDA" with "infrared data association (IrDA)" in Overview section.
• Updated the following sections: Receiver functional description , Data sampling technique , Receiver wake-up operation .
• Replaced section title "Interrupts and status fields" with "Interrupts".
• Replaced reserved field access type "RW" with "ROZ" in bit 12 of Control (CTRL) register.
• Updated Baud rate generation section.
• Added Baud rate tolerance and Calculating baud rate tolerance .
• Replaced "receive data" with "Receive data (from RxD)" in Figure 424 .
• Replaced 'sever to 10 bits' with 'along with N (7, 8, 9, 10) bits' in Receiver functional description .
• Replaced "When CTRL[LOOPS] is 1,....... (CTRL[RSRC] = 1)." with "Enable Loop mode.......CTRL[RSRC] = 0" in Loop
mode .
• Replaced "When CTRL[LOOPS] is 1...........(CTRL[RSRC] = 1)" with "Enable single.........CTRL[RSRC] = 1" in Single-
Wire mode .
• Replaced the word ‘DTS’ with ‘CTS’ in Hardware flow control .
• Changed the line ‘This is called resynchronization’ with ‘This Synchronization......as Resynchronization’ in Data sampling
technique .
• Updated Figure 427 .
• Updated the long description of DATA[IDLINE] .
• Replaced the term ‘Internal Slave peripheral bus‘ with ‘Internal chip peripheral bus’ in Figure 423 and Figure 424 .
• Updated Hardware flow control .
• In Baud rate generation , added statement "There is a relationship between the required baud rate and the input clock
frequency, SBR, and OSR. See the below figure for details".

#### A.74 Quad Serial Peripheral Interface (QuadSPI)

#### A.74.1 Chip-specific QuadSPI information changes

No substantial content changes.

---

*Page 3352*

Release notes

#### A.74.2 QuadSPI module changes

• Updated field size of BUF3CR[ADATSZ] .
• Added a new section Extended prefetch or Streaming mode .
• Updated field description of FR[ABOF] .
• Added footnote on JMP_ON_CSand STOP instructions in Table 555 .
• Updated field description of BUF3CR[ADATSZ] .
• Updated Extended prefetch or Streaming mode .
• Removed note saying "The hardware does not provide any protection against illegal programming" from register
descriptions of Buffer 0 Top Index Register (BUF0IND) , Buffer 1 Top Index Register (BUF1IND) , and Buffer 2 Top Index
Register (BUF2IND) .
• Removed the RSER[TBFIE] field.
• Added following information in the FRAD0_WORD2[MD0ACP]: If EAL is being written in same cycle (10 or 11), or if
FRAD_WORD3 [LOCK] bits are written as 11. if LOCK bits are written as 10 then this field can be programmed only if
master ID matches the ID check of respective MDAD queue. If MDAD descriptor is not valid then respective MDxACP
also becomes read-only.
• Updated the description of the FRAD0_WORD3[VLD], FRAD0_WORD3[LOCK], FRAD0_WORD5[CMP_ERR] fields.
• Updated the descriptions of the TG0SFARS[VLD] and TG0SFARS[CLR] fields.
• Updated the descriptions of the TG0IPCRS[VLD] and TG0IPCRS[CLR], and FLSEQREQ[FRAD] fields.
• Updated the description of the value 11 of the FRAD0_WORD3[EAL] field.
• Updated the description of the value 00 of the TG0MDAD[SA] field.
• Added caution in the section Power up and reset .
• Changes access of the following reserved fields to RW: DLCR[25], FR[30], FR[31], FR[13], FR[14], FR[4], RSER[25]
• Changes access of the following reserved fields to RO: FR[29], SR[25], SR[26], SR[5], SR[3]
• Changed the access of the following reserved fields to RW: RSER[27].
• Changed the access of the following reserved fields to ROZ: FR[4].
• Changed the access of the following reserved field to ROZ: FR[30].
• Added following note in RSER[WMRK] field: This field should never be programmed above 63 because there are only 64
memory mapped RBDR registers. If watermark is programmed above 63, data above 64 words will be lost.
• Changed the access of the following reserved field to ROZ: BFGENCR[17].
• Changed the access of the following reserved field to ROZ: BFGENCR[28].
• Added "Example: Flash reset sequence" in Power up and reset .
• Added following fields: MCR[ISD3FA] and MCR[ISD2FA] .
• Replaced all instances of "master/domain" ID with domain ID.
Table continues on the next page...

---

*Page 3353*

Release notes
• Added following information in the Extended prefetch or Streaming mode section: Also ensure that complete buffer is
assigned to Buffer 3 Configuration Register (BUF3CR) and remaining buffer sizes should be programmed to 0 by writing
0 to Buffer 0 Top Index Register (BUF0IND) , Buffer 1 Top Index Register (BUF1IND) , and Buffer 2 Top Index Register
(BUF2IND) registers.
• In Flash memory programming , updated the second statement of the fourth step as follows: Write an appropriate value to
IPCR[IDATSZ] to denote the size of the write in bytes.
• Added RSER[ TBFIE ] field.
• Updated Basic description section
• In Table 554 , removed row INTB
• In section Dummy Pad loopback , added following notes:
— "Refer to Auto-DataLearning... details"
• "Spansion hyperflash" changed to "hyperflash"
• Note added, "If DLL is disabled....can be used for JMP_ON_CS instruction " for sections
— Fast read quad output (Winbond)
— Fast read sequence (Macronix/Numonyx/Spansion/Winbond)
—
— LUT
• Figure 430 updated
• Replaced 'master port number' with 'master ID'
• In BUF0CR[ MSTRID ], added a Note, "See the chip-specific... components.
• Updated description of IPCR[ SEQID ] field.
• Access changed to reserved for RBCT[RXBRD] bitfield 8.
• In BFGENCR [SEQID] Function section, removed "This field....LUT index".
• In Flash memory read , removed "and this depends on the configuration of RBCT[RXBRD]"
• Figure title changed to "Data strobe functionality in SDR mode for read operation" in Figure 436 .
• In Figure 430 , added a note, "The IOs are driven ...ongoing phase"
• In Figure 430 , removed 2nd and 3rd note
• Updated Figure 430 with Note1.
• In section Dummy Pad loopback , added content "DLLCR[FREQEN]" and "or DLLCRB[SLV_DLY_COARSE]" for high
and low frequency delay chain manual programming
• In Basic description , changed the variable name "tDel,total" to "ttotal_delay"
• In section Features , added phrase "or using AHB (64-bit width interface)"
• In SR [RXFULL] , updated description
• In MCR [SWRSTSD] , updated description of 0b bit
• In MCR [SWRSTHD] , updated description of 0b bit
Table continues on the next page...

---

*Page 3354*

Release notes
• In SR [TXWA] , updated Function section
• In RSER [CRCAIE] , updated description
• In FR [CRCAEF] , updated description
• In Section Dummy Pad loopback , Title updated
• In Programmable sequence engine , updated the Instruction set entry for CMD instruction
• In LUT , updated the LUT section statement for Reset Sequence Table
• In Flash memory programming , description updated for Flash Programming memory section
• In Flash memory read , for Flash memory read section, description added "A read access to... the different ways"
• In Byte ordering of serial flash memory read data , description added in section "Byte ordering of serial flash memory
read data"
• In DLLCRA [FREQEN] , Note removed
• In Sequence Pointer Clear Register (SPTRCLR) , Function section updated
• In SR [TXWA] , updated Function section
• Removed RSER[ABSEIE], Reserved bitfield
• In Address scheme , removed sections:
— Separation of address into rows and columns
— Word addressable mode for flash memory
• FR[CRCBEF] access updated to Reserved
• RSER[CRCBIE] access updated to Reserved
• In Flexible read xAHB buffers , content updated
• Removed SPTRCLR[STREAM_EN]
• Updated note on MSTRID to "See the chip-specific... corresponding components"
• Updated Note on Master IDs
• In RBCT[WMRK] , Note added.
• In SOC Configuration Register (SOCCR) , updated Function section
• Removed from feature list, "Support for HyperFlash memory"
• In Module Disable mode , removed text "Software needs to ensure.... is requested."
• Section AHB bus access read considerations updated
• In Flash memory programming removed text, "If required,....addressable"
• Removed '4h' from section QUADSPI register descriptions
Table continues on the next page...

---

*Page 3355*

Release notes
• Driving external signals
— Note revised, "All read commands in Dual pad, Quad pad.... applicable to Single-pad mode"
• TGnSFARS[ERR]
— Bitfield description updated
• Revised Pad values of "DUMMY" instruction in the following tables:
— Table 571
— Table 569
— Table 572
• FR[ABOF]
— Bitfield description updated
• Updated bitfield description of FR[TBUF]
• Updated bitfield description of FR[ABOF]
• Function section updated in the following registers:
— In LUT n register, added text, "Write access.... if MGC[11]=1b"
— In register LUT Key Register (LUTKEY) , added text, "Write access.... if MGC[11]=1b"
— In register LUT Lock Configuration Register (LCKCR) , added text, "Write access.... if MGC[11]=1b"
— Buffer Generic Configuration Register (BFGENCR) , added text, "Write access.... if MGC[11]=1b"
• Revised note in Dummy Pad loopback .
• Revised TX buffer operation : a footnote is added to Fs
• Updated title of Table 556 to "Read sequence"

#### A.75 Debug changes

No substantial content changes.

#### A.76 JTAG Controller (JTAGC)

#### A.76.1 Chip-specific JTAGC information changes

No substantial content changes.

#### A.76.2 JTAGC module changes

• Marked SAMPLE instruction as internal.
• Marked SAMPLE keyword as internal in EXTEST external test instruction , IEEE 1149.1–2001 defined test modes ,
Boundary scan register , and Boundary scan .
• Marked SAMPLE keyword as internal in PRELOAD instruction
Table continues on the next page...

---

*Page 3356*

Release notes
• Renamed "Initialization/application information" to Initialization .
• Reordered and reorganized various subsections in Functional description .
• Added Clocking , Interrupts , and Application information .
• Moved "IDCODE instruction", "SAMPLE/PRELOAD instruction", "EXTEST external test instruction", "HIGHZ instruction",
"ENABLE_SOC_DATA1 instruction" , "CLAMP instruction", "ACCESS_AUX_x instructions" and "BYPASS instruction"
into JTAGC block instructions .
• Moved "TEST_LEAKAGE instruction" into JTAGC block instructions section.

#### A.77 JDC module changes

• Added Clocking section.
• Added Interrupts section containing text "This module has no interrupts".
• Added JDC functionality section.
• Renamed "Signal description" to External signals .
• Renamed "Introduction" to Overview .
• Added Initialization section.
• Updated Clocking section.

#### A.78 TEMPSENSE module changes

• Renamed Introduction to Overview .
• Added sections Clocking , External signals , and interrupt, to convey that there are no clock sources, external signals, and
interrupts for this module.
• Added the section head "Initialization" to Conversion from voltage to temperature .
• Updated Overview and Conversion from voltage to temperature
• Added ipg_scan_mode signal in .
• Improved LVIX signal description in and changed the coefficient unit from C2/V2 to C/V2 in Conversion from voltage to
temperature .

---

*Page 3357*

Legal information

# Legal information

Applications — Applications that are described herein for any of these

#### Definitions

products are for illustrative purposes only. NXP Semiconductors makes no
Draft — A draft status on a document indicates that the content is still
representation or warranty that such applications will be suitable for the
under internal review and subject to formal approval, which may result
specified use without further testing or modification.
in modifications or additions. NXP Semiconductors does not give any
Customers are responsible for the design and operation of their applications
representations or warranties as to the accuracy or completeness of
and products using NXP Semiconductors products, and NXP Semiconductors
information included in a draft version of a document and shall have no
accepts no liability for any assistance with applications or customer product
liability for the consequences of use of such information.
design. It is customer’s sole responsibility to determine whether the NXP
Semiconductors product is suitable and fit for the customer’s applications and

#### Disclaimers

products planned, as well as for the planned application and use of customer’s
Limited warranty and liability — Information in this document is believed third party customer(s). Customers should provide appropriate design and
to be accurate and reliable. However, NXP Semiconductors does not give operating safeguards to minimize the risks associated with their applications
any representations or warranties, expressed or implied, as to the accuracy and products.
or completeness of such information and shall have no liability for the
NXP Semiconductors does not accept any liability related to any default,
consequences of use of such information. NXP Semiconductors takes no
damage, costs or problem which is based on any weakness or default in the
responsibility for the content in this document if provided by an information
customer’s applications or products, or the application or use by customer’s
source outside of NXP Semiconductors.
third party customer(s). Customer is responsible for doing all necessary testing
In no event shall NXP Semiconductors be liable for any indirect, incidental, for the customer’s applications and products using NXP Semiconductors
punitive, special or consequential damages (including - without limitation - products in order to avoid a default of the applications and the products or of the
lost profits, lost savings, business interruption, costs related to the removal or application or use by customer’s third party customer(s). NXP does not accept
replacement of any products or rework charges) whether or not such damages any liability in this respect.
are based on tort (including negligence), warranty, breach of contract or any
Terms and conditions of commercial sale — NXP Semiconductors products
other legal theory.
are sold subject to the general terms and conditions of commercial sale,
Notwithstanding any damages that customer might incur for any reason
as published at https://www.nxp.com/profile/terms, unless otherwise agreed
whatsoever, NXP Semiconductors’ aggregate and cumulative liability towards
in a valid written individual agreement. In case an individual agreement
customer for the products described herein shall be limited in accordance with
is concluded only the terms and conditions of the respective agreement
the Terms and conditions of commercial sale of NXP Semiconductors.
shall apply. NXP Semiconductors hereby expressly objects to applying the
customer’s general terms and conditions with regard to the purchase of NXP
Right to make changes — NXP Semiconductors reserves the right to make
Semiconductors products by customer.
changes to information published in this document, including without limitation
specifications and product descriptions, at any time and without notice. This
Export control — This document as well as the item(s) described herein may be
document supersedes and replaces all information supplied prior to the
subject to export control regulations. Export might require a prior authorization
publication hereof.
from competent authorities.
Suitability for use — NXP Semiconductors products are not designed,
authorized or warranted to be suitable for use in life support, life-critical
or safety-critical systems or equipment, nor in applications where failure or
malfunction of an NXP Semiconductors product can reasonably be expected
to result in personal injury, death or severe property or environmental damage.
NXP Semiconductors and its suppliers accept no liability for inclusion and/or
use of NXP Semiconductors products in such equipment or applications and
therefore such inclusion and/or use is at the customer’s own risk.

---

*Page 3358*

Legal information
Suitability for use in non-automotive qualified products — Unless this NXP — wordmark and logo are trademarks of NXP B.V.
document expressly states that this specific NXP Semiconductors product
AMBA, Arm, Arm7, Arm7TDMI, Arm9, Arm11, Artisan, big.LITTLE, Cordio,
is automotive qualified, the product is not suitable for automotive use.
CoreLink, CoreSight, Cortex, DesignStart, DynamIQ, Jazelle, Keil, Mali,
It is neither qualified nor tested in accordance with automotive testing
Mbed, Mbed Enabled, NEON, POP, RealView, SecurCore, Socrates, Thumb,
or application requirements. NXP Semiconductors accepts no liability for
TrustZone, ULINK, ULINK2, ULINK-ME, ULINK-PLUS, ULINKpro, μVision,
inclusion and/or use of non-automotive qualified products in automotive
Versatile — are trademarks and/or registered trademarks of Arm Limited (or its
equipment or applications.
subsidiaries or affiliates) in the US and/or elsewhere. The related technology
In the event that customer uses the product for design-in and use in automotive may be protected by any or all of patents, copyrights, designs and trade
applications to automotive specifications and standards, customer (a) shall use secrets. All rights reserved.
the product without NXP Semiconductors’ warranty of the product for such
Bluetooth — the Bluetooth wordmark and logos are registered trademarks
automotive applications, use and specifications, and (b) whenever customer
owned by Bluetooth SIG, Inc. and any use of such marks by NXP
uses the product for automotive applications beyond NXP Semiconductors’
Semiconductors is under license.
specifications such use shall be solely at customer’s own risk, and (c) customer
eIQ — is a trademark of NXP B.V.
fully indemnifies NXP Semiconductors for any liability, damages or failed
product claims resulting from customer design and use of the product for I2C-bus — logo is a trademark of NXP B.V.
automotive applications beyond NXP Semiconductors’ standard warranty and
SafeAssure — is a trademark of NXP B.V.
NXP Semiconductors’ product specifications.
SafeAssure — logo is a trademark of NXP B.V.
HTML publications — An HTML version, if available, of this document is ® ®
SuperFlash — This product uses SuperFlash technology. SuperFlash is a
provided as a courtesy. Definitive information is contained in the applicable
registered trademark of Silicon Storage Technology, Inc.
document in PDF format. If there is a discrepancy between the HTML
Synopsys & Designware — are registered trademarks of Synopsys, Inc.
document and the PDF document, the PDF document has priority.
©
Synopsys — Portions Copyright 2018-2022 Synopsys, Inc. Used with
Translations — A non-English (translated) version of a document, including
permission. All rights reserved.
the legal information in that document, is for reference only. The English
version shall prevail in case of any discrepancy between the translated and
English versions.
Security — Customer understands that all NXP products may be subject to
unidentified vulnerabilities or may support established security standards or
specifications with known limitations. Customer is responsible for the design
and operation of its applications and products throughout their lifecycles
to reduce the effect of these vulnerabilities on customer’s applications
and products. Customer’s responsibility also extends to other open and/or
proprietary technologies supported by NXP products for use in customer’s
applications. NXP accepts no liability for any vulnerability. Customer should
regularly check security updates from NXP and follow up appropriately.
Customer shall select products with security features that best meet rules,
regulations, and standards of the intended application and make the
ultimate design decisions regarding its products and is solely responsible
for compliance with all legal, regulatory, and security related requirements
concerning its products, regardless of any information or support that may be
provided by NXP.
NXP has a Product Security Incident Response Team (PSIRT) (reachable
at PSIRT@nxp.com ) that manages the investigation, reporting, and solution
release to security vulnerabilities of NXP products.
NXP B.V. — NXP B.V. is not an operating company and it does not distribute
or sell products.

#### Trademarks

Notice: All referenced brands, product names, service names, and
trademarks are the property of their respective owners.

---

*Page 3359*

Please be aware that important notices concerning this document and the product(s) described
herein, have been included in section 'Legal information'.
© 2026 NXP B.V. All rights reserved.
For more information, please visit: https://www.nxp.com
Date of release: 26 May 2026
Document identifier: MCXE31XRM