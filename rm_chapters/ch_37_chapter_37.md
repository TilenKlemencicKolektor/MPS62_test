<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 37 -->

# Chapter 37

# Device Configuration Module General-Purpose

# Registers (DCM_GPR)

#### 37.1 DCM controlled features and availability in product family

Based on the chip features described in ‘Feature comparison’ section in ‘Introduction’ chapter, there are some features which are
present only in specific parts in the MCXE31xx product family. The following table summarizes the corresponding DCM register
fields along with the parts wherein the corresponding register fields are available. In rest of the parts within the product family, the
corresponding fields are reserved.
Table 194. DCM controlled features and availability in product family
Parts wherein this field is
Register field Register field abbreviation Register field description
available
DCMROD3[5] TCM_GSKT_ALARM See Register section for details MCXE31B, MCXE317,
MCXE315/MCXE316
DCMROD3[6] DMA_SYS_GSKT_ALARM See Register section for details MCXE31B
DCMROD3[7] DMA_PERIPH_GSKT_ALARM See Register section for details MCXE31B
DCMROD3[9] DMA_AXBS_ALARM See Register section for details MCXE31B
DCMROD3[12] QSPI_GSKT_ALARM See Register section for details MCXE31B
DCMROD3[14] AIPS2_GSKT_ALARM See Register section for details MCXE31B
DCMROD3[17] TCM_AXBS_ALARM See Register section for details MCXE31B
DCMROD3[18] MAC_GSKT_ALARM See Register section for details MCXE31B
DCMROD3[19] PERIPH_AXBS_ALARM See Register section for details MCXE31B
DCMROD3[24] PRAM1_ECC_ERR See Register section for details MCXE31B
DCMROD4[10] PRAM1_FCCU_ALARM See Register section for details MCXE31B
DCMROD5[14] TCM_RDATA_EDC_ERR Specifies whether an integrity MCXE31B
error is reported on the TCM
read data for safety.
Read this field to identify the
reason for a fault in case of
FCCU NCF 1.
DCMROD5[15] MAC_RDATA_EDC_ERR See Register section for details MCXE31B
DCMROF1[0] MAC_MDC_CHID_0 See Register section for details MCXE31B
DCMROF1[1] MAC_MDC_CHID_1 See Register section for details MCXE31B
DCMROF20[1] LMAUTO_DIS Specifies whether the PMC MCXE31B
last-mile automatic crossover
from the boot regulation feature
is supported for the chip.
DCMROF20[3] DMA_AXBS_IAHB_BYP See Register section for details MCXE31B
Table continues on the next page...

---

*Page 873*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table 194. DCM controlled features and availability in product family (continued)
Parts wherein this field is
Register field Register field abbreviation Register field description
available
DCMROF20[5] QSPI_IAHB_BYP See Register section for details MCXE31B
DCMRWD3[5] TCM_GSKT_ALARM_EN See Register section for details MCXE31B, MCXE317,
MCXE315/MCXE316
DCMRWD3[6] DMA_SYS_GSKT_ALARM_EN See Register section for details MCXE31B
DCMRWD3[7] DMA_PERIPH_GSKT_ALARM_ See Register section for details MCXE31B
EN
DCMRWD3[9] DMA_AXBS_ALARM_EN See Register section for details MCXE31B
DCMRWD3[12] QSPI_GSKT_ALARM_EN See Register section for details MCXE31B
DCMRWD3[14] AIPS2_GSKT_ALARM_EN See Register section for details MCXE31B
DCMRWD3[17] TCM_AXBS_ALARM_EN See Register section for details MCXE31B
DCMRWD3[18] MAC_GSKT_ALARM_EN See Register section for details MCXE31B
DCMRWD3[19] PERIPH_AXBS_ALARM_EN See Register section for details MCXE31B
DCMRWD3[24] PRAM1_ECC_ERR_EN See Register section for details MCXE31B
DCMRWD4[10] PRAM1_FCCU_ALARM_EN See Register section for details MCXE31B
DCMRWD5[14] TCM_RDATA_EDC_ERR_EN Specifies whether an integrity MCXE31B
error is reported on the TCM
read data.
The field enables fault
monitoring at FCCU NCF 1, if
there is an integrity error on the
TCM read data, for safety.
DCMRWD5[15] MAC_RDATA_EDC_ERR_EN See Register section for details MCXE31B
DCMRWD6[6] eMIOS2_DBG_DIS_CM7_0 See Register section for details MCXE31B
DCMRWD6[11] STM1_DBG_DIS_CM7_0 See Register section for details MCXE31B
DCMRWD6[14] PIT2_DBG_DIS_CM7_0 See Register section for details MCXE31B
DCMRWD6[19] LPSPI4_DBG_DIS_CM7_0 See Register section for details MCXE31B
DCMRWD6[20] LPSPI5_DBG_DIS_CM7_0 See Register section for details MCXE31B
DCMRWD6[27] FLEXCAN3_DBG_DIS_CM7_0 See Register section for details MCXE31B, MCXE317
DCMRWD6[28] FLEXCAN4_DBG_DIS_CM7_0 See Register section for details MCXE31B, MCXE317
DCMRWD6[29] FLEXCAN5_DBG_DIS_CM7_0 See Register section for details MCXE31B, MCXE317
DCMRWD6[30] SAI0_DBG_DIS_CM7_0 See Register section for details MCXE31B
DCMRWD6[31] SAI1_DBG_DIS_CM7_0 See Register section for details MCXE31B
DCMRWF1[0] CAN_TIMESTAMP_SEL See Register section for details MCXE31B
DCMRWF1[1] CAN_TIMESTAMP_EN See Register section for details MCXE31B
Table continues on the next page...

---

*Page 874*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table 194. DCM controlled features and availability in product family (continued)
Parts wherein this field is
Register field Register field abbreviation Register field description
available
DCMRWF1[7] MAC_CONF_SEL See Register section for details MCXE31B
DCMRWF1[15] VDD_HV_B_IO_CTRL_LATCH See Register section for details MCXE31B
DCMRWF1[26] VDD_HV_B_VLT_DVDR_EN See Register section for details MCXE31B
DCMRWF1[27] VDD_1_5_VLT_DVDR_EN See Register section for details MCXE31B
DCMRWF4[0] MUX_MODE_EN_ADC1_S18 Controls the selection of GPIOs MCXE315/MCXE316
to drive ADC1 standard channel
18th.
DCMRWF4[5] MUX_MODE_EN_ADC1_S22 See Register section for details MCXE31B, MCXE317
DCMRWF4[6] MUX_MODE_EN_ADC1_S23 See Register section for details MCXE31B, MCXE317
DCMRWF4[7] MUX_MODE_EN_ADC0_S12 Controls the selection of GPIOs MCXE315/MCXE316
to drive ADC_0 standard
channel 12.
DCMRWF4[8] MUX_MODE_EN_ADC0_S13 Controls the selection of GPIOs MCXE315/MCXE316
to drive ADC_0 standard
channel 13.
DCMRWF4[9] MUX_MODE_EN_ADC2_S8 See Register section for details MCXE31B
DCMRWF4[10] MUX_MODE_EN_ADC2_S9 See Register section for details MCXE31B
DCMRWF4[11] MUX_MODE_EN_ADC0_S14 Controls the selection of GPIOs MCXE315/MCXE316
to drive ADC_0 standard
channel 14.
DCMRWF4[12] MUX_MODE_EN_ADC0_S17 Controls the selection of GPIOs MCXE315/MCXE316
to drive ADC_0 standard
channel 17.
DCMRWF4[28] MUX_MODE_EN_ADC0_P2 Controls the selection of MCXE315/MCXE316
GPIOs to drive ADC0 precision
channel 2nd.

#### 37.2 DCM_GPR register descriptions

NOTE
Do not write to the reserved locations, registers, or reserved bits in the registers. Chip behavior is not guaranteed
in case of such writes.
NOTE
The MC_RGM_ERCTRL register configuration takes several cycles to be effective. Any further access to DCM
should be made after at least 9 AIPS_SLOW_CLK cycles after writing to MC_RGM_ERCTRL
NOTE
The DCM Read Write registers, DCMRW X are synchronized. Hence changes to these configurations might take
upto 4 CORE_CLK cycles to be effective.

---

*Page 875*

Device Configuration Module General-Purpose Registers (DCM_GPR)
NOTE
The DCM read only registers are present in standby domain and retain values across standby mode. Hence
reading the status registers after standby exit might indicate a previously latched value. To read back these status
registers after any reset event or standby exit, first clear these and then read. This would clear the previous status
or transient information due to reset event and indicate the correct status.

#### 37.2.1 DCM_GPR memory map

DCM_GPR base address: 402A_C000h
Offset Register Access Reset value
Width
(In bits)
200h Read Only GPR On Destructive Reset Register (DCMROD1) 32 RW 0000_0000h
208h Read Only GPR On Destructive Reset Register (DCMROD3) 32 RW 0000_0000h
20Ch Read Only GPR On Destructive Reset Register (DCMROD4) 32 RW 0000_0000h
210h Read Only GPR On Destructive Reset Register (DCMROD5) 32 RW 0000_0000h
300h Read Only GPR On Functional Reset Register (DCMROF1) 32 RW 0000_0000h
304h Read Only GPR On Functional Reset Register (DCMROF2) 32 RW 0000_0000h
308h Read Only GPR On Functional Reset Register (DCMROF3) 32 RW 0000_0000h
30Ch Read Only GPR On Functional Reset Register (DCMROF4) 32 RW 0000_0000h
310h Read Only GPR On Functional Reset Register (DCMROF5) 32 RW 0000_0000h
314h Read Only GPR On Functional Reset Register (DCMROF6) 32 RW 0000_0000h
318h Read Only GPR On Functional Reset Register (DCMROF7) 32 RW 0000_0000h
31Ch Read Only GPR On Functional Reset Register (DCMROF8) 32 RW 0000_0000h
320h Read Only GPR On Functional Reset Register (DCMROF9) 32 RW 0000_0000h
324h Read Only GPR On Functional Reset Register (DCMROF10) 32 RW 0000_0000h
328h Read Only GPR On Functional Reset Register (DCMROF11) 32 RW 0000_0000h
32Ch Read Only GPR On Functional Reset Register (DCMROF12) 32 RW 0000_0000h
330h Read Only GPR On Functional Reset Register (DCMROF13) 32 RW 0000_0000h
334h Read Only GPR On Functional Reset Register (DCMROF14) 32 RW 0000_0000h
338h Read Only GPR On Functional Reset Register (DCMROF15) 32 RW 0000_0000h
33Ch Read Only GPR On Functional Reset Register (DCMROF16) 32 RW 0000_0000h
340h Read Only GPR On Functional Reset Register (DCMROF17) 32 RW 0000_0000h
348h Read Only GPR On Functional Reset Register (DCMROF19) 32 R 4000_0000h
34Ch Read Only GPR On Functional Reset Register (DCMROF20) 32 R See section
350h Read Only GPR On Functional Reset Register (DCMROF21) 32 R 0000_0000h
400h Read Write GPR On Power On Reset Register (DCMRWP1) 32 RW 0000_0400h
Table continues on the next page...

---

*Page 876*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
408h Read Write GPR On Power On Reset Register (DCMRWP3) 32 RW 0000_0000h
504h Read Write GPR On Destructive Reset Register (DCMRWD2) 32 RW 0000_0000h
508h Read Write GPR On Destructive Reset Register (DCMRWD3) 32 RW FF4F_FBFFh
50Ch Read Write GPR On Destructive Reset Register (DCMRWD4) 32 RW EF7F_FFFFh
510h Read Write GPR On Destructive Reset Register (DCMRWD5) 32 RW 007F_FFFFh
514h Read Write GPR On Destructive Reset Register (DCMRWD6) 32 RW 0000_0000h
518h Read Write GPR On Destructive Reset Register (DCMRWD7) 32 R 0000_0000h
51Ch Read Write GPR On Destructive Reset Register (DCMRWD8) 32 R 0000_0000h
520h Read Write GPR On Destructive Reset Register (DCMRWD9) 32 R 0000_0000h
600h Read Write GPR On Functional Reset Register (DCMRWF1) 32 RW 0000_0000h
604h Read Write GPR On Functional Reset Register (DCMRWF2) 32 RW 0000_0000h
60Ch Read Write GPR On Functional Reset Register (DCMRWF4) 32 RW 0000_0000h
610h Read Write GPR On Functional Reset Register (DCMRWF5) 32 RW See section
700h Read Only GPR On PMCPOR Reset (DCMROPP1) 32 RW 0000_0000h
704h Read Only GPR On PMCPOR Reset (DCMROPP2) 32 RW 0000_0000h
708h Read Only GPR On PMCPOR Reset (DCMROPP3) 32 RW 0000_0000h
70Ch Read Only GPR On PMCPOR Reset (DCMROPP4) 32 RW 0000_0000h

#### 37.2.2 Read Only GPR On Destructive Reset Register (DCMROD1)

Offset
Register Offset
DCMROD1 200h
Function
Resets on destructive reset.

---

*Page 877*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R Reserved
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
KEY_R HSE_ PCU_I
R Reserved
ES ... DCF ... SO ...
W W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-3
—
2 Key Response Ready
KEY_RESP_RE SDA-AP key response status. Indicates that the for the SDA-AP all key values have been written and
ADY HSE can begin authentication.
0b - Key response is not ready.
1b - Key response is ready.
DCF violation from HSE
1
0b - No violation from HSE DCF records while decoding.
HSE_DCF_VIO
1b - Violation from HSE DCF records while decoding.
PCU Input Isolation status on previous standby entry
0
0b - Input isolation did not get enabled in the previous standby entry.
PCU_ISO_STA
TUS
1b - Input isolation enabled in the previous standby entry.

#### 37.2.3 Read Only GPR On Destructive Reset Register (DCMROD3)

Offset
Register Offset
DCMROD3 208h
Function
Resets on destructive reset.

---

*Page 878*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
Reserv CM7_0 Reserv CM7_0 Reserv CM7_0 PRAM PRAM Reserv LC_ PERIP EMAC TCM_ DATA_
R Reserved
ed _I ... ed _D ... ed _D ... 0_E ... 1_E ... ed ERR H_ ... _GS ... AXB ... ED ...
W W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
ADDR AIPS2 AIPS1 QSPI_ HSE_ Reserv DMA_ SYS_A DMA_ DMA_ TCM_ Reserv Reserv HSE_L Reserv CM7_0
R
_ED ... _G ... _G ... GS ... GSK ... ed AXB ... XB ... PER ... SYS ... GSK ... ed ed OC ... ed _L ...
W W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
Multi bit ECC error from CM7_0 ICache data memory. Read this bit to identify the reason of fault in case
30
of FCCU NCF 2.
CM7_0_ICDAT
0b - CM7_1 core not in lockup state.
A_ECC_ERR
1b - CM7_1 core in lockup state.
Reserved
29
—
Multi bit ECC error from CM7_0 DCache tag memory. Read this bit to identify the reason of fault in case
28
of FCCU NCF 2.
CM7_0_DCTAG
0b - No multi-bit ECC error.
_ECC_ERR
1b - Multi-bit ECC error.
Reserved
27
—
Multi bit ECC error from CM7_0 DCache data memory. Read this bit to identify the reason of fault in case
26
of FCCU NCF 2.
CM7_0_DCDAT
0b - No multi-bit ECC error.
A_ECC_ERR
1b - Multi-bit ECC error.
Multi bit ECC error from SRAM0. Read this bit to identify the reason of fault in case of FCCU NCF 2.
25
0b - No multi-bit ECC error.
PRAM0_ECC_E
RR
1b - Multi-bit ECC error.
Multi bit ECC error from SRAM1. Read this bit to identify the reason of fault in case of FCCU NCF 2.
24
Table continues on the next page...

---

*Page 879*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
PRAM1_ECC_E 0b - No multi-bit ECC error.
RR
1b - Multi-bit ECC error.
Reserved
23
—
Error in Lifecycle Scanning. Read this bit to identify the reason of fault in case of FCCU NCF 3.
22
0b - No error while lifecycle scanning.
LC_ERR
1b - Error while lifecycle scanning.
Reserved
21-20
—
PERIPH AXBS_Lite Safety Alarm Status. Read this bit to identify the reason of fault in case of FCCU
19
NCF 1.
PERIPH_AXBS
0b - No safety alarm indicated by Periph AXBS_Lite.
_ALARM
1b - Safety alarm indicated by Periph AXBS_Lite.
EMAC IAHB Gasket Alarm Status. Read this bit to identify the reason of fault in case of FCCU NCF 1.
18
0b - No alarm indicated by EMAC IAHB gasket.
EMAC_GSKT_
ALARM
1b - Alarm indicated by EMAC IAHB gasket.
TCM AHB Splitter Safety Alarm Status. Read this bit to identify the reason of fault in case of FCCU NCF
17
1.
TCM_AXBS_AL
0b - No safety alarm indicated by TCM AHB splitter.
ARM
1b - Safety alarm indicated by TCM AHB splitter.
Status of integrity error on data for safety. Read this bit to identify the reason of fault in case of FCCU
16
NCF 1.
DATA_EDC_ER
0b - No integrity error on data.
R
1b - Data integrity error.
Status of integrity error on address for safety. Read this bit to identify the reason of fault in case of FCCU
15
NCF 1.
ADDR_EDC_E
0b - No integrity error on address.
RR
1b - Address integrity error.
AIPS2 IAHB Gasket Alarm Status. Read this bit to identify the reason of fault in case of FCCU NCF 1.
14
0b - No alarm indicated by AIPS2 IAHB gasket.
AIPS2_GSKT_A
LARM
1b - Alarm indicated by AIPS2 IAHB gasket.
AIPS1 IAHB Gasket Alarm Status. Read this bit to identify the reason of fault in case of FCCU NCF 1.
13
Table continues on the next page...

---

*Page 880*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
AIPS1_GSKT_A 0b - No alarm indicated by AIPS1 IAHB gasket.
LARM
1b - Alarm indicated by AIPS1 IAHB gasket.
QSPI IAHB Gasket Alarm Status. Read this bit to identify the reason of fault in case of FCCU NCF 1.
12
0b - No alarm indicated by QSPI IAHB gasket.
QSPI_GSKT_A
LARM
1b - Alarm indicated by QSPI IAHB gasket.
HSE IAHB Gasket Alarm Status. Read this bit to identify the reason of fault in case of FCCU NCF 1.
11
0b - No alarm indicated by HSE IAHB gasket.
HSE_GSKT_AL
ARM
1b - Alarm indicated by HSE IAHB gasket.
Reserved
10
—
DMA AXBS_Lite Safety Alarm Status. Read this bit to identify the reason of fault in case of FCCU NCF 1.
9
0b - No safety alarm indicated by DMA AXBS_Lite.
DMA_AXBS_AL
ARM
1b - Safety alarm indicated by DMA AXBS_Lite.
System AXBS Safety Alarm Status. Read this bit to identify the reason of fault in case of FCCU NCF 1.
8
0b - No safety alarm indicated by system AXBS.
SYS_AXBS_AL
ARM
1b - Safety alarm indicated by system AXBS.
Status of IAHB gasket safety alarm from DMA periph AXBS IAHB gasket. Read this bit to identify the
7
reason of fault in case of FCCU NCF 1.
DMA_PERIPH_
0b - No safety alarm reported from DMA-periph AXBS IAHB gasket.
GSKT_ALARM
1b - Safety alarm indicated by DMA-periph AXBS IAHB gasket.
Status of IAHB gasket safety alarm from DMA system AXBS IHAB gasket. Read this bit to identify the
6
reason of fault in case of FCCU NCF 1.
DMA_SYS_GS
0b - No safety alarm reported from DMA-system AXBS IAHB gasket.
KT_ALARM
1b - Safety alarm indicated by DMA-system AXBS IAHB gasket.
TCM IAHB Gasket Monitor Alarm Status. Read this bit to identify the reason of fault in case of FCCU
5
NCF 1.
TCM_GSKT_AL
0b - No alarm reported from TCM IAHB gasket.
ARM
1b - Monitor alarm reported from TCM IAHB gasket.
Reserved
4
—
Reserved
3
—
Table continues on the next page...

---

*Page 881*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
HSE Core Lockup Status. Read this bit to identify the reason of fault in case of FCCU NCF 0.
2
0b - HSE core not in lockup state.
HSE_LOCKUP
1b - HSE core in lockup state.
Reserved
1
—
CM7_0 Core Lockup Status. Read this bit to identify the reason of fault in case of FCCU NCF 0.
0
0b - CM7_0 core not in lockup state.
CM7_0_LOCKU
P
1b - CM7_0 core in lockup state.

#### 37.2.4 Read Only GPR On Destructive Reset Register (DCMROD4)

Offset
Register Offset
DCMROD4 20Ch
Function
Resets on destructive reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
Reserv TEST_ TEST_ Reserv VDD2 VDD1 Reserv FLAS Reserv FLAS FLAS FLAS FLAS FLAS Reserv Reserv
R
ed AC ... AC ... ed P5_ ... P1_ ... ed H_A ... ed H_S ... H_R ... H_R ... H_A ... H_E ... ed ed
W W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
PF1_D PF1_C PF0_D PF0_C HSE_ PRAM PRAM DMA_ Reserv Reserv Reserv CM7_0 CM7_0 CM7_0 Reserv CM7_0
R
AT ... OD ... AT ... OD ... RAM ... 1_F ... 0_F ... TCD ... ed ed ed _D ... _D ... _I ... ed _I ...
W W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
Table continues on the next page...

---

*Page 882*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
—
Accidental Partial Test Activation. Read this bit to identify the reason of fault in case of FCCU NCF 5.
30
0b - No partial test activated accidentally.
TEST_ACTIVA
TION_1_ERR
1b - Partial test activated accidentally.
Accidental Partial Test Activation. Read this bit to identify the reason of fault in case of FCCU NCF 5.
29
0b - No partial test activated accidentally.
TEST_ACTIVA
TION_0_ERR
1b - Partial test activated accidentally.
Reserved
28
—
Go/no-go for VDD_HV_FLA (double bond) going to FXOSC and PLL. Read this bit to identify the reason
27
of fault in case of FCCU NCF 4.
VDD2P5_GNG_
0b - Go indication referring to the supply being clean.
ERR
1b - No-go indication referring to the supply being unclean and a fault in double bond connection
or its routing within the chip.
Go/no-go indicator for VDD1PD1 (double bond) supply going to PLL. Read this bit to identify the reason
26
of fault in case of FCCU NCF 4.
VDD1P1_GNG_
0b - Go indication referring to the supply being clean.
ERR
1b - No-go indication referring to the supply being unclean and a fault in double bond connection
or its routing within the chip.
Reserved
25
—
24 Transaction Monitor Mismatch Error From Flash Controller
FLASH_ACCES This alarm idicates that the flash controller detected a transaction monitor mismatch when compared
S_ERR to flash safety feedback output. There is an indication in the flash where reconstructed address is
compared with the address which invoked the flash access. Read this bit to identify the reason of fault in
case of FCCU NCF 3.
0b - No transaction monitor mismatch error from flash controller.
1b - Transaction monitor mismatch error from flash controller.
Reserved
23
—
Error while DCM flash scanning process due to invalid data. Read this bit to identify the reason of fault in
22
case of FCCU NCF 3.
FLASH_SCAN_
0b - No error while DCM flash scanning.
ERR
1b - Error while DCM flash scanning.
Table continues on the next page...

---

*Page 883*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
21 Flash Reset Error Status
FLASH_RST_E This error indication is set when flash encounters errors during its reset reads. Read this bit to identify
RR the reason of fault in case of FCCU NCF 3.
0b - No flash reset error indicated.
1b - Flash reset error indicated.
Flash reference current loss or read voltage error while prevoius read(s). Read this bit to identify the
20
reason of fault in case of FCCU NCF 3.
FLASH_REF_E
0b - No reference current loss or read voltage error while previous reads(s).
RR
1b - Reference current loss or read voltage error while previous read(s).
19 Flash Address Encode Error
FLASH_ADDR_ In address decoding, if multiple or no address line is selected, FMU reports address encode error. Read
ENC_ERR this bit to identify the reason of fault in case of FCCU NCF 3.
0b - No address encode error in flash.
1b - Address enocde error in flash.
Status of flash ECC correction error through EDC reported by FMU. Read this bit to identify the reason of
18
fault in case of FCCU NCF 3.
FLASH_EDC_E
0b - No EDC after ECC error reported in flash.
RR
1b - EDC after ECC error reported in flash.
Reserved
17
—
Reserved
16
—
15 Flash1 Data ECC Uncorrectable Error
PF1_DATA_EC The errors are reported from the FMU and are connected to FCCU NCFs. These are also connected to
C_ERR ERM. See ERM chapter for the memory errors and mapping onto ERM channels. Read this bit to identify
the reason of fault in case of FCCU NCF 3.
0b - No uncorrectable error reported in flash controller port 1 data memory by FMU.
1b - Uncorrectable error reported in flash controller port 1 data memory by FMU.
14 Flash1 Code ECC Uncorrectable Error
PF1_CODE_EC The errors are reported from the FMU and are connected to FCCU NCFs. These are also connected to
C_ERR ERM. See ERM chapter for the memory errors and mapping onto ERM channels. Read this bit to identify
the reason of fault in case of FCCU NCF 3.
0b - No uncorrectable error reported in flash controller port 1 code memory by FMU.
1b - Uncorrectable error reported in flash controller port 1 code memory by FMU.
Table continues on the next page...

---

*Page 884*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
13 Flash0 Data ECC Uncorrectable Error
PF0_DATA_EC The errors are reported from the FMU and are connected to FCCU NCFs. These are also connected to
C_ERR ERM. See ERM chapter for the memory errors and mapping onto ERM channels. Read this bit to identify
the reason of fault in case of FCCU NCF 3.
0b - No uncorrectable error reported in flash controller port 0 data memory by FMU.
1b - Uncorrectable error reported in flash controller port 0 data memory by FMU.
12 Flash0 Code ECC Uncorrectable Error
PF0_CODE_EC The errors are reported from the FMU and are connected to FCCU NCFs. These are also connected to
C_ERR ERM. See ERM chapter for the memory errors and mapping onto ERM channels. Read this bit to identify
the reason of fault in case of FCCU NCF 3.
0b - No uncorrectable error reported in flash controller port 0 code memory by FMU.
1b - Uncorrectable error reported in flash controller port 0 code memory by FMU.
HSE RAM Uncorrectable ECC Status. Read this bit to identify the reason of fault in case of FCCU NCF
11
2.
HSE_RAM_EC
0b - No uncorrectable ECC reported by HSE RAM.
C_ERR
1b - Uncorrectable ECC reported by HSE RAM.
Status of PRAM1 safety alarm. This alarm is set on faulty SRAM1 read or read modify error. Read this
10
bit to identify the reason of fault in case of FCCU NCF 2.
PRAM1_FCCU_
0b - No safety alarm indicated by PRAM1.
ALARM
1b - Safety alarm indicated by PRAM1.
Status of PRAM0 safety alarm. This alarm is set on faulty SRAM0 read or read modify error. Read this
9
bit to identify the reason of fault in case of FCCU NCF 2.
PRAM0_FCCU_
0b - No safety alarm indicated by PRAM0.
ALARM
1b - Safety alarm indicated by PRAM0.
Uncorrectable ECC error reported from DMA_TCD memory. This uncorrectable ECC error consists of
8
multi-bit data ECC error and address ECC error. Read this bit to identify the reason of fault in case of
DMA_TCD_RA
FCCU NCF 2.
M_ECC_ERR
0b - No uncorrectable ECC error detected.
1b - Uncorrectable ECC error detected.
Reserved
7
—
Reserved
6
—
Reserved
5
Table continues on the next page...

---

*Page 885*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
—
Uncorrectable ECC error reported from CM7_0 Data TCM memory block 1. This uncorrectable ECC
4
error consists of multi-bit data ECC error and address ECC error. The CM7_0 Data TCM physically
CM7_0_DTCM1
consists of two blocks. Read this bit to identify the reason of fault in case of FCCU NCF 2.
_ECC_ERR
NOTE
Address ECC error is not supported on CM7_0 Data TCM memory in MCXE31B.
0b - No uncorrectable ECC error detected.
1b - Uncorrectable ECC error detected.
Uncorrectable ECC error reported from CM7_0 Data TCM memory block 0. This uncorrectable ECC
3
error consists of multi-bit data ECC error and address ECC error. The CM7_0 Data TCM physically
CM7_0_DTCM0
consists of two blocks. Read this bit to identify the reason of fault in case of FCCU NCF 2.
_ECC_ERR
NOTE
Address ECC error is not supported on CM7_0 Data TCM memory in MCXE31B.
0b - No uncorrectable ECC error detected.
1b - Uncorrectable ECC error detected.
Uncorrectable ECC error reported from CM7_0 Instruction TCM memory. This uncorrectable ECC error
2
consists of multi-bit data ECC error and address ECC error. Read this bit to identify the reason of fault in
CM7_0_ITCM_
case of FCCU NCF 2.
ECC_ERR
NOTE
Address ECC error is not supported on CM7_0 Instruction TCM memory in MCXE31B.
0b - No uncorrectable ECC error detected.
1b - Uncorrectable ECC error detected.
Reserved
1
—
Multi bit ECC error from CM7_0 ICache tag memory. Read this bit to identify the reason of fault in case
0
of FCCU NCF 2.
CM7_0_ICTAG_
0b - No multi-bit ECC error.
ECC_ERR
1b - Multi-bit ECC error.

#### 37.2.5 Read Only GPR On Destructive Reset Register (DCMROD5)

Offset
Register Offset
DCMROD5 210h

---

*Page 886*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Function
Resets on destructive reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv HSE_ CM7_0 CM7_0 Reserv Reserv DMA_ Reserv
R
ed ed ed ed ed ed ed ed ed RDA ... _A ... _A ... ed ed RDA ... ed
W W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
EMAC TCM_ DEBU MTR_ STCU MBIST STCU SW_N SW_N SW_N SW_N INTM_ INTM_ INTM_ INTM_ Reserv
R
_RD ... RDA ... G_A ... BUS ... _BI ... _A ... _NCF CF_3 CF_2 CF_1 CF_0 3_ ... 2_ ... 1_ ... 0_ ... ed
W W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C
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
Reserved
28
—
Reserved
27
—
Reserved
26
—
Reserved
25
—
Reserved
24
—
Reserved
23
Table continues on the next page...

---

*Page 887*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
—
Integrity (EDC) error on HSE read data for safety. Read this bit to identify the reason of fault in case of
22
FCCU NCF 1.
HSE_RDATA_E
0b - No integrity error reported on HSE read data.
DC_ERR
1b - Integrity error reported on HSE read data.
Integrity error on CM7_0 main read data for safety. Read this bit to identify the reason of fault in case of
21
FCCU NCF 1.
CM7_0_AHBM_
0b - No integrity error reported on CM7_0 main read data.
RDATA_EDC_E
RR
1b - Integrity error reported on CM7_0 main read data.
Integrity error on CM7_0 peripheral read data for safety. Read this bit to identify the reason of fault in
20
case of FCCU NCF 1.
CM7_0_AHBP_
0b - No integrity error reported on CM7_0 peripheral read data.
RDATA_EDC_E
RR
1b - Integrity error reported on CM7_0 peripheral read data.
Reserved
19
—
Reserved
18
—
Integrity(EDC) error on eDMA read data for safety. Read this bit to identify the reason of fault in case of
17
FCCU NCF 1.
DMA_RDATA_
0b - No integrity error reported on eDMA read data.
EDC_ERR
1b - Integrity error reported on eDMA read data.
Reserved
16
—
Integrity(EDC) error on EMAC read data for safety. Read this bit to identify the reason of fault in case of
15
FCCU NCF 1.
EMAC_RDATA
0b - No integrity error reported on EMAC read data.
_EDC_ERR
1b - Integrity error reported on EMAC read data.
Integrity(EDC) error on TCM read data for safety. Read this bit to identify the reason of fault in case of
14
FCCU NCF 1.
TCM_RDATA_E
0b - No integrity error reported on TCM read data.
DC_ERR
1b - Integrity error reported on TCM read data.
Monitoring of unintended debug activation. This is set when the core is in halted state with application
13
debug not enabled or debugger request not enabled. Read this bit to identify the reason of fault in case
of FCCU NCF 5.
Table continues on the next page...

---

*Page 888*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
DEBUG_ACTIV 0b - Unintended debug not activated.
ATION_ERR
1b - Unintended debug activated.
Fault reported due to illegal access on MTR. This fault is reported via a transfer error indication to the
12
system. Read this bit to identify the reason of fault in case of FCCU NCF 5.
MTR_BUS_ER
0b - No transfer error indicated from MTR.
R
1b - Transfer error indicated from MTR.
L/M BIST enabled accidentally. Fault condition in run mode (LBIST or MBIST control signals go to wrong
11
condition). Read this bit to identify the reason of fault in case of FCCU NCF 5.
STCU_BIST_U
0b - No accidental L/M BIST enabled.
SER_CF
1b - Accidental L/M BIST enabled.
Indicates an accidental backdoor access on memories. This monitor needs to be disabled on FCCU
10
when doing fault injection. Read this bit to identify the reason of fault in case of FCCU NCF 5.
MBIST_ACTIVA
0b - No accidental backdoor enabled on memories.
TION_ERR
1b - Accidental backdoor enabled on memories.
STCU non-critical fault, that is, BIST result error. Read this bit to identify the reason of fault in case of
9
FCCU NCF 5.
STCU_NCF
0b - STCU non-critical fault not reported.
1b - STCU non-critical fault reported.
8 Software NCF3 Status
SW_NCF_3 Status of DCMRWF1[FCCU_SW_NCF3]. Read this bit to identify the reason of fault in case of FCCU
NCF 7.
0b - Software NCF3, DCMRWF1[FCCU_SW_NCF3] not enabled by software.
1b - Software NCF3, DCMRWF1[FCCU_SW_NCF3] enabled by software.
7 Software NCF2 Status
SW_NCF_2 Status of DCMRWF1[FCCU_SW_NCF2]. Read this bit to identify the reason of fault in case of FCCU
NCF 7.
0b - Software NCF2, DCMRWF1[FCCU_SW_NCF2] not enabled by software.
1b - Software NCF2, DCMRWF1[FCCU_SW_NCF2] enabled by software.
6 Software NCF1 Status
SW_NCF_1 Status of DCMRWF1[FCCU_SW_NCF1]. Read this bit to identify the reason of fault in case of FCCU
NCF 7.
0b - Software NCF1, DCMRWF1[FCCU_SW_NCF1] not enabled by software.
1b - Software NCF1, DCMRWF1[FCCU_SW_NCF1] enabled by software.
5 Software NCF0 Status
Table continues on the next page...

---

*Page 889*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
Status of DCMRWF1[FCCU_SW_NCF0]. Read this bit to identify the reason of fault in case of FCCU
SW_NCF_0
NCF 7.
0b - Software NCF0, DCMRWF1[FCCU_SW_NCF0] not enabled by software.
1b - Software NCF0, DCMRWF1[FCCU_SW_NCF0] enabled by software.
Interrupt monitor3 error reported by INTM. Also this error is reflected in INTM.INTM_STATUS3. See
4
'Functional Description' section in the INTM chapter for details. Read this bit to identify the reason of fault
INTM_3_ERR
in case of FCCU NCF 6.
0b - No error reported by interrupt monitor 3.
1b - Error reported by interrupt monitor 3.
Interrupt monitor2 error reported by INTM. Also this error is reflected in INTM.INTM_STATUS2. See
3
'Functional Description' section in the INTM chapter for details. Read this bit to identify the reason of fault
INTM_2_ERR
in case of FCCU NCF 6.
0b - No error reported by interrupt monitor 2.
1b - Error reported by interrupt monitor 2.
Interrupt monitor1 error reported by INTM. Also this error is reflected in INTM.INTM_STATUS1. See
2
'Functional Description' section in the INTM chapter for details. Read this bit to identify the reason of fault
INTM_1_ERR
in case of FCCU NCF 6.
0b - No error reported by interrupt monitor 1.
1b - Error reported by interrupt monitor 1.
Interrupt monitor0 error reported by INTM. Also this error is reflected in INTM.INTM_STATUS0. See
1
'Functional Description' section in the INTM chapter for details. Read this bit to identify the reason of fault
INTM_0_ERR
in case of FCCU NCF 6.
0b - No error reported by interrupt monitor 0.
1b - Error reported by interrupt monitor 0.
Reserved
0
—

#### 37.2.6 Read Only GPR On Functional Reset Register (DCMROF1)

Offset
Register Offset
DCMROF1 300h
Function
Resets on functional reset.

---

*Page 890*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R Reserved
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
EMAC EMAC
R Reserved
_MD ... _MD ...
W W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-2
—
EMAC DMA Channel ID1 Status
1
0b - Channel ID1 is not the current transfer channel ID.
EMAC_MDC_C
HID_1
1b - Channel ID1 is the current transfer channel ID.
EMAC DMA Channel ID0 Status
0
0b - Channel ID0 is not the current transfer channel ID.
EMAC_MDC_C
HID_0
1b - Channel ID0 is the current transfer channel ID.

#### 37.2.7 Read Only GPR On Functional Reset Register (DCMROF2)

Offset
Register Offset
DCMROF2 304h
Function
Resets on functional reset.

---

*Page 891*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DCF_SDID0
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCF_SDID0
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Configuration bits of DCF client SDID 0.
DCF_SDID0 This register indicates the SDID0 contents scanned from flash by DCM
NOTE
See the DCF clients file attached to this document for more information. These are DCF
clients in Utest sector which contains Device Configurable ID information and are being
captured as status on Read Only GPR on Functional Reset.

#### 37.2.8 Read Only GPR On Functional Reset Register (DCMROF3)

Offset
Register Offset
DCMROF3 308h
Function
Resets on functional reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DCF_SDID1
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCF_SDID1
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 892*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Fields
Field Function
31-0 Configuration bits of DCF client SDID 1.
DCF_SDID1 This register indicates the SDID1 contents scanned from flash by DCM.
NOTE
See the DCF clients file attached to this document for more information. These are DCF
clients in Utest sector which contains Device Configurable ID information and are being
captured as status on Read Only GPR on Functional Reset.

#### 37.2.9 Read Only GPR On Functional Reset Register (DCMROF4)

Offset
Register Offset
DCMROF4 30Ch
Function
Resets on functional reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DCF_SDID2
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCF_SDID2
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Configuration bits of DCF client SDID 2.
DCF_SDID2 This register indicates the SDID2 contents scanned from flash by DCM.
NOTE
See the DCF clients file attached to this document for more information. These are DCF
clients in Utest sector which contains Device Configurable ID information and are being
captured as status on Read Only GPR on Functional Reset.

---

*Page 893*

Device Configuration Module General-Purpose Registers (DCM_GPR)

#### 37.2.10 Read Only GPR On Functional Reset Register (DCMROF5)

Offset
Register Offset
DCMROF5 310h
Function
Resets on functional reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DCF_SDID3
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCF_SDID3
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Configuration bits of DCF client SDID 3.
DCF_SDID3 This register indicates the SDID3 contents scanned from flash by DCM.
NOTE
See the DCF clients file attached to this document for more information. These are DCF
clients in Utest sector which contains Device Configurable ID information and are being
captured as status on Read Only GPR on Functional Reset.

#### 37.2.11 Read Only GPR On Functional Reset Register (DCMROF6)

Offset
Register Offset
DCMROF6 314h
Function
Resets on functional reset.

---

*Page 894*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DCF_SDID4
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCF_SDID4
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Configuration bits of DCF client SDID 4.
DCF_SDID4 This register indicates the SDID4 contents scanned from flash by DCM.
NOTE
See the DCF clients file attached to this document for more information. These are DCF
clients in Utest sector which contains Device Configurable ID information and are being
captured as status on Read Only GPR on Functional Reset.

#### 37.2.12 Read Only GPR On Functional Reset Register (DCMROF7)

Offset
Register Offset
DCMROF7 318h
Function
Resets on functional reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DCF_SDID5
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCF_SDID5
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 895*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Fields
Field Function
31-0 Configuration bits of DCF client SDID 5.
DCF_SDID5 This register indicates the SDID5 contents scanned from flash by DCM.
NOTE
See the DCF clients file attached to this document for more information. These are DCF
clients in Utest sector which contains Device Configurable ID information and are being
captured as status on Read Only GPR on Functional Reset.

#### 37.2.13 Read Only GPR On Functional Reset Register (DCMROF8)

Offset
Register Offset
DCMROF8 31Ch
Function
Resets on functional reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DCF_SDID6
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCF_SDID6
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Configuration bits of DCF client SDID 6.
DCF_SDID6 This register indicates the SDID6 contents scanned from flash by DCM.
NOTE
See the DCF clients file attached to this document for more information. These are DCF
clients in Utest sector which contains Device Configurable ID information and are being
captured as status on Read Only GPR on Functional Reset.

---

*Page 896*

Device Configuration Module General-Purpose Registers (DCM_GPR)

#### 37.2.14 Read Only GPR On Functional Reset Register (DCMROF9)

Offset
Register Offset
DCMROF9 320h
Function
Resets on functional reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DCF_SDID7
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCF_SDID7
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Configuration bits of DCF client SDID 7.
DCF_SDID7 This register indicates the SDID7 contents scanned from flash by DCM.
NOTE
See the DCF clients file attached to this document for more information. These are DCF
clients in Utest sector which contains Device Configurable ID information and are being
captured as status on Read Only GPR on Functional Reset.

#### 37.2.15 Read Only GPR On Functional Reset Register (DCMROF10)

Offset
Register Offset
DCMROF10 324h
Function
Resets on functional reset.

---

*Page 897*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DCF_SDID8
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCF_SDID8
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Configuration bits of DCF client SDID 8.
DCF_SDID8 This register indicates the SDID8 contents scanned from flash by DCM.
NOTE
See the DCF clients file attached to this document for more information. These are DCF
clients in Utest sector which contains Device Configurable ID information and are being
captured as status on Read Only GPR on Functional Reset.

#### 37.2.16 Read Only GPR On Functional Reset Register (DCMROF11)

Offset
Register Offset
DCMROF11 328h
Function
Resets on functional reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DCF_SDID9
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCF_SDID9
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 898*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Fields
Field Function
31-0 Configuration bits of DCF client SDID 9.
DCF_SDID9 This register indicates the SDID9 contents scanned from flash by DCM.
NOTE
See the DCF clients file attached to this document for more information. These are DCF
clients in Utest sector which contains Device Configurable ID information and are being
captured as status on Read Only GPR on Functional Reset.

#### 37.2.17 Read Only GPR On Functional Reset Register (DCMROF12)

Offset
Register Offset
DCMROF12 32Ch
Function
Resets on functional reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DCF_SDID10
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCF_SDID10
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Configuration bits of DCF client SDID 10.
DCF_SDID10 This register indicates the SDID10 contents scanned from flash by DCM.
NOTE
See the DCF clients file attached to this document for more information. These are DCF
clients in Utest sector which contains Device Configurable ID information and are being
captured as status on Read Only GPR on Functional Reset.

---

*Page 899*

Device Configuration Module General-Purpose Registers (DCM_GPR)

#### 37.2.18 Read Only GPR On Functional Reset Register (DCMROF13)

Offset
Register Offset
DCMROF13 330h
Function
Resets on functional reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DCF_SDID11
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCF_SDID11
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Configuration bits of DCF client SDID 11.
DCF_SDID11 This register indicates the SDID11 contents scanned from flash by DCM.
NOTE
See the DCF clients file attached to this document for more information. These are DCF
clients in Utest sector which contains Device Configurable ID information and are being
captured as status on Read Only GPR on Functional Reset.

#### 37.2.19 Read Only GPR On Functional Reset Register (DCMROF14)

Offset
Register Offset
DCMROF14 334h
Function
Resets on functional reset.

---

*Page 900*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DCF_SDID12
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCF_SDID12
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Configuration bits of DCF client SDID 12.
DCF_SDID12 This register indicates the SDID12 contents scanned from flash by DCM.
NOTE
See the DCF clients file attached to this document for more information. These are DCF
clients in Utest sector which contains Device Configurable ID information and are being
captured as status on Read Only GPR on Functional Reset.

#### 37.2.20 Read Only GPR On Functional Reset Register (DCMROF15)

Offset
Register Offset
DCMROF15 338h
Function
Resets on functional reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DCF_SDID13
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCF_SDID13
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 901*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Fields
Field Function
31-0 Configuration bits of DCF client SDID 13.
DCF_SDID13 This register indicates the SDID13 contents scanned from flash by DCM.
NOTE
See the DCF clients file attached to this document for more information. These are DCF
clients in Utest sector which contains Device Configurable ID information and are being
captured as status on Read Only GPR on Functional Reset.

#### 37.2.21 Read Only GPR On Functional Reset Register (DCMROF16)

Offset
Register Offset
DCMROF16 33Ch
Function
Resets on functional reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DCF_SDID14
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCF_SDID14
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Configuration bits of DCF client SDID 14.
DCF_SDID14 This register indicates the SDID14 contents scanned from flash by DCM.
NOTE
See the DCF clients file attached to this document for more information. These are DCF
clients in Utest sector which contains Device Configurable ID information and are being
captured as status on Read Only GPR on Functional Reset.

---

*Page 902*

Device Configuration Module General-Purpose Registers (DCM_GPR)

#### 37.2.22 Read Only GPR On Functional Reset Register (DCMROF17)

Offset
Register Offset
DCMROF17 340h
Function
Resets on functional reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DCF_SDID15
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCF_SDID15
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Configuration bits of DCF client SDID 15.
DCF_SDID15 This register indicates the SDID15 contents scanned from flash by DCM.
NOTE
See the DCF clients file attached to this document for more information. These are DCF
clients in Utest sector which contains Device Configurable ID information and are being
captured as status on Read Only GPR on Functional Reset.

#### 37.2.23 Read Only GPR On Functional Reset Register (DCMROF19)

Offset
Register Offset
DCMROF19 348h
Function
Resets on functional reset.

---

*Page 903*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
FCCU DCM_ Reserv
R Reserved
_EO ... DONE ed
W
Reset 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 FCCU EOUT Dedicated
FCCU_EOUT_ Indicates the status of FCCU_EOUT functions on GPIO2 and GPIO3 as configured in DCF record,
DEDICATED UTEST_MISC[FCCU_EOUT_DEDICATED].
0b - FCCU_EOUT pins on GPIO2 and GPIO3 acts as general purpose pins supporting all
functions.
1b - FCCU_EOUT pins on GPIO2 and GPIO3 acts as dedicated EOUT pins.
30 DCM Done
DCM_DONE Status of flash scanning by DCM.
0b - Flash scanning by DCM not complete.
1b - Flash scanning completed by DCM.
Reserved
29
—
Reserved
28-0
—

#### 37.2.24 Read Only GPR On Functional Reset Register (DCMROF20)

Offset
Register Offset
DCMROF20 34Ch
Function
Resets on functional reset.

---

*Page 904*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
Reserv Reserv
R DCF_DEST_RST_ESC
ed ed
W
1
Reset u u u u u u u u u u u u u u 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
POR_
AIPS_I QSPI_I Reserv DMA_ Reserv LMAU
R Reserved Reserved WDG ..
A ... A ... ed AXB ... ed TO_ ...
.
W
1 1 1 1 1
Reset 0 0 0 0 0 0 0 0 0 u u 0 u 0 u u
1. The reset value of this register is dependent on DCF client default value.
Fields
Field Function
This register indicates the information of chip destructive reset escalation support for destructive reset
31-18
sources as configured in DCF record, DEST_RST_ESC[13:0]. See the DCF clients file attached to this
DCF_DEST_RS
document for the mapping of corresponding destructive reset event. A 1 (or 0) in the corresponding
T_ESC
field indicates that the destructive reset escalation feature is enabled (or disabled) for that corresponding
destructive reset event.
00_0000_0000_0000b - Destructive Reset Escalation disabled.
00_0000_0000_0001b - Destructive Reset Escalation enabled.
Reserved
17
—
Reserved
16
—
Reserved
15-12
—
Reserved
11-7
—
Status of AIPS1/2 IAHB gasket as configured in DCF record, UTEST_MISC[AIPS_IAHB_BYP].
6
0b - Register wall enabled.
AIPS_IAHB_BY
P
1b - Register wall bypassed.
Status of QSPI IAHB gasket as configured in DCF record, UTEST_MISC[QSPI_IAHB_BYP].
5
0b - Register wall enabled.
Table continues on the next page...

---

*Page 905*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
QSPI_IAHB_BY 1b - Register wall bypassed.
P
Reserved
4
—
Status of DMA AXBS IAHB gasket as configured in DCF record, UTEST_MISC[DMA_AXBS_IAHB_BYP].
3
0b - Register wall enabled.
DMA_AXBS_IA
HB_BYP
1b - Register wall bypassed.
Reserved
2
—
PMC last mile automatic croosover from boot regulationfeature support.
1
0b - Last mile auto crossover feautre is supported for the chip.
LMAUTO_DIS
1b - Last mile auto crossover feautre is not supported for the chip.
Indicates the status of POR_WDG as configured in DCF record, UTEST_MISC[POR_WDG_EN].
0
0b - POR Watchdog Disabled.
POR_WDG_EN
1b - POR Watchdog Enabled (default).

#### 37.2.25 Read Only GPR On Functional Reset Register (DCMROF21)

Offset
Register Offset
DCMROF21 350h
Function
Resets on functional reset.

---

*Page 906*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
HSE_CLK_MOD Reserv DCF_DEST_RS
R Reserved
E_OP ... ed T_ESC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCF_DEST_RST_ESC
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-21
—
HSE Clock Mode Option
20-19
00b - Applicable for clocking option A. Ratio of 1:2 in between HSE IPS interface clock
HSE_CLK_MO
(AIPS_SLOW_CLK) and HSE module clock (HSE_CLK), HSE_IAHB gasket enabled.
DE_OPTION
01b - Applicable for clocking option C, D, E, E2, and F. Ratio of 1:2 in between HSE IPS interface
clock (AIPS_SLOW_CLK) and HSE module clock (HSE_CLK), HSE_IAHB gasket bypass.
10b - 10b and 11b both are applicable for clocking option B in same way. Ratio of 1:4 in between
HSE IPS interface clock (AIPS_SLOW_CLK) and HSE module clock (HSE_CLK), HSE_IAHB
gasket enabled.
11b - 10b and 11b both are applicable for clocking option B in same way. Ratio of 1:4 in between
HSE IPS interface clock (AIPS_SLOW_CLK) and HSE module clock (HSE_CLK), HSE_IAHB
gasket enabled.
Reserved
18
—
This register indicates the information of chip destructive reset escalation support for destructive reset
17-0
sources as configured in DCF record, DEST_RST_ESC[31:14]. See the DCF clients file attached to this
DCF_DEST_RS
document for the mapping of corresponding destructive reset event. A 1 (or 0) in the corresponding
T_ESC
field indicates that the destructive reset escalation feature is enabled (or disabled) for that corresponding
destructive reset event.
00_0000_0000_0000_0000b - Destructive Reset Escalation disabled.
00_0000_0000_0000_0001b - Destructive Reset Escalation enabled.

---

*Page 907*

Device Configuration Module General-Purpose Registers (DCM_GPR)

#### 37.2.26 Read Write GPR On Power On Reset Register (DCMRWP1)

Offset
Register Offset
DCMRWP1 400h
Function
Resets on Power On reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
POR_WDOG_T STAN Reserv Reserv CLKO Reserv
Reserved Reserved Reserved
RIM BDY ... ed ed UT_ ... ed
W
Reset 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-11
—
10-9 Trims for POR WDG timeout value.
POR_WDOG_T POR_WDOG TRIM BITS:
RIM
00= POR WDOG Timeout= 06.25ms
01= POR WDOG Timeout = 12.50ms
10= POR WDOG Timeout = 25.00ms
11= POR WDOG Timeout = 50.00ms
Disables the standby entry and exit monitoring window of the POR WDOG.
8
0b - Enabled
STANBDY_PW
DOG_DIS
1b - Disabled
Reserved
7
—
Reserved
6
Table continues on the next page...

---

*Page 908*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
—
Reserved
5-4
—
3 Clockout standby expose over functional and destructive reset.
CLKOUT_STAN This field controls the CLKOUT_STANDBY function availability while functional or destructive reset on
DBY PTA12.
0b - The CLKOUT_STANDBY function is not available while functional or destructive reset on
PTA12.
1b - The CLKOUT_STANDBY function is available while functional or destructive reset on PTA12.
Reserved
2-1
—
Reserved
0
—

#### 37.2.27 Read Write GPR On Power On Reset Register (DCMRWP3)

Offset
Register Offset
DCMRWP3 408h
Function
Resets on Power On reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
DEST_
Reserved Reserved
RS ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 909*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Fields
Field Function
Reserved
31-10
—
Configures a destructive reset to interrupt.
9
0b - Destructive reset.
DEST_RST9_A
S_IPI
1b - PLL LOL interrupt.
Reserved
8-0
—

#### 37.2.28 Read Write GPR On Destructive Reset Register (DCMRWD2)

Offset
Register Offset
DCMRWD2 504h
Function
Resets on destructive reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
EOUT Reserv Reserv Reserv Reserv Reserv Reserv Reserv
Reserved
_ST ... ed ed ed ed ed ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-8
—
Controls the EOUT state during selftest.
7
Table continues on the next page...

---

*Page 910*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
EOUT_STAT_D 0b - EOUT state will change to high Z post selftest when device is under reset.
UR_STEST
1b - EOUT state will remain in FAULT state until this bit is cleared.
Reserved
6
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
Reserved
2
—
Reserved
1
—
Reserved
0
—

#### 37.2.29 Read Write GPR On Destructive Reset Register (DCMRWD3)

Offset
Register Offset
DCMRWD3 508h
Function
Fault disable fields. Resets on destructive reset.

---

*Page 911*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv CM7_0 Reserv CM7_0 Reserv CM7_0 PRAM PRAM Reserv LC_ER PERIP EMAC TCM_ DATA_
Reserved
ed _I ... ed _D ... ed _D ... 0_E ... 1_E ... ed R_ ... H_ ... _GS ... AXB ... ED ...
W
Reset 1 1 1 1 1 1 1 1 0 1 0 0 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
ADDR AIPS2 AIPS1 QSPI_ HSE_ Reserv DMA_ SYS_A DMA_ DMA_ TCM_ Reserv Reserv Reserv Reserv CM7_0
_ED ... _G ... _G ... GS ... GSK ... ed AXB ... XB ... PER ... SYS ... GSK ... ed ed ed ed _L ...
W
Reset 1 1 1 1 1 0 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
Reserved
31
—
Enable bit for enabling the fault monitoring at FCCU NCF 2 for the fault: Multi bit ECC error from CM7_0
30
ICache data memory.
CM7_0_ICDAT
0b - No multi-bit ECC error.
A_ECC_ERR_E
N
1b - Multi-bit ECC error.
Reserved
29
—
Enable bit for enabling the fault monitoring at FCCU NCF 2 for the fault: Multi bit ECC error from CM7_0
28
DCache tag memory.
CM7_0_DCTAG
0b - No multi-bit ECC error.
_ECC_ERR_EN
1b - Multi-bit ECC error.
Reserved
27
—
Enable bit for enabling the fault monitoring at FCCU NCF 2 for the fault: Multi bit ECC error from CM7_0
26
DCache data memory.
CM7_0_DCDAT
0b - No multi-bit ECC error.
A_ECC_ERR_E
N
1b - Multi-bit ECC error.
Enable bit for enabling the fault monitoring at FCCU NCF 2 for the fault: Multi bit ECC error from SRAM0.
25
0b - No multi-bit ECC error.
PRAM0_ECC_E
RR_EN
1b - Multi-bit ECC error.
Enable bit for enabling the fault monitoring at FCCU NCF 2 for the fault: Multi bit ECC error from SRAM1.
24
Table continues on the next page...

---

*Page 912*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
PRAM1_ECC_E 0b - No multi-bit ECC error.
RR_EN
1b - Multi-bit ECC error.
Reserved
23
—
Enable bit for enabling the fault monitoring at FCCU NCF 3 for the fault: Error in lifecycle scanning.
22
LC_ERR_EN NOTE
On any POR or destructive reset event, since this bit gets cleared, it has no effect and
lifecycle error (in case if present) will not get disabled.
0b - No error while lifecycle scanning.
1b - Error while lifecycle scanning.
Reserved
21-20
—
Enable bit for enabling the fault monitoring at FCCU NCF 1 for the fault: PERIPH AXBS_Lite safety
19
alarm.
PERIPH_AXBS
0b - No safety alarm indicated by Periph AXBS_Lite.
_ALARM_EN
1b - Safety alarm indicated by Periph AXBS_Lite.
Enable bit for enabling the fault monitoring at FCCU NCF 1 for the fault: EMAC IAHB gasket alarm.
18
0b - No alarm indicated by EMAC IAHB gasket.
EMAC_GSKT_
ALARM_EN
1b - Alarm indicated by EMAC IAHB gasket.
Enable bit for enabling the fault monitoring at FCCU NCF 1 for the fault: TCM AHB splitter safety alarm.
17
0b - No safety alarm indicated by TCM AHB splitter.
TCM_AXBS_AL
ARM_EN
1b - Safety alarm indicated by TCM AHB splitter.
Enable bit for enabling the fault monitoring at FCCU NCF 1 for the fault: integrity error on data for safety.
16
0b - No integrity error on data.
DATA_EDC_ER
R_EN
1b - Data integrity error..
Enable bit for enabling the fault monitoring at FCCU NCF 1 for the fault: integrity error on address for
15
safety.
ADDR_EDC_E
0b - No integrity error on address.
RR_EN
1b - Address integrity error.
Enable bit for enabling the fault monitoring at FCCU NCF 1 for the fault: AIPS2 IAHB gasket alarm.
14
0b - No alarm indicated by AIPS2 IAHB gasket.
AIPS2_GSKT_A
LARM_EN
1b - Alarm indicated by AIPS2 IAHB gasket.
Table continues on the next page...

---

*Page 913*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
Enable bit for enabling the fault monitoring at FCCU NCF 1 for the fault: AIPS1 IAHB gasket alarm.
13
0b - No alarm indicated by AIPS1 IAHB gasket.
AIPS1_GSKT_A
LARM_EN
1b - Alarm indicated by AIPS1 IAHB gasket.
Enable bit for enabling the fault monitoring at FCCU NCF 1 for the fault: QSPI IAHB gasket alarm.
12
0b - No alarm indicated by QSPI IAHB gasket.
QSPI_GSKT_A
LARM_EN
1b - Alarm indicated by QSPI IAHB gasket.
Enable bit for enabling the fault monitoring at FCCU NCF 1 for the fault: HSE IAHB gasket alarm.
11
0b - No alarm indicated by HSE IAHB gasket.
HSE_GSKT_AL
ARM_EN
1b - Alarm indicated by HSE IAHB gasket.
Reserved
10
—
Enable bit for enabling the fault monitoring at FCCU NCF 1 for the fault: DMA AXBS_Lite safety alarm.
9
0b - No safety alarm indicated by DMA AXBS_Lite.
DMA_AXBS_AL
ARM_EN
1b - Safety alarm indicated by DMA AXBS_Lite.
Enable bit for enabling the fault monitoring at FCCU NCF 1 for the fault: system AXBS safety alarm.
8
0b - No safety alarm indicated by system AXBS.
SYS_AXBS_AL
ARM_EN
1b - Safety alarm indicated by system AXBS.
Enable bit for enabling the fault monitoring at FCCU NCF 1 for the fault: IAHB gasket safety alarm from
7
DMA periph AXBS IAHB gasket.
DMA_PERIPH_
0b - No safety alarm reported from DMA-periph AXBS IAHB gasket.
GSKT_ALARM_
EN
1b - Safety alarm indicated by DMA-periph AXBS IAHB gasket.
Enable bit for enabling the fault monitoring at FCCU NCF 1 for the fault: IAHB gasket safety alarm from
6
DMA system AXBS IHAB gasket.
DMA_SYS_GS
0b - No safety alarm reported from DMA-system AXBS IAHB gasket.
KT_ALARM_EN
1b - Safety alarm indicated by DMA-system AXBS IAHB gasket.
Enable bit for enabling the fault monitoring at FCCU NCF 1 for the fault: TCM IAHB Gasket monitor
5
alarm.
TCM_GSKT_AL
0b - No alarm reported from TCM IAHB gasket.
ARM_EN
1b - Monitor alarm reported from TCM IAHB gasket.
Reserved
4
—
Reserved
3
Table continues on the next page...

---

*Page 914*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
—
Reserved
2
—
Reserved
1
—
Enable bit for enabling the fault monitoring at FCCU NCF 0 for the fault: CM7_0 core lockup.
0
0b - CM7_0 core not in lockup state.
CM7_0_LOCKU
P_EN
1b - CM7_0 core in lockup state.

#### 37.2.30 Read Write GPR On Destructive Reset Register (DCMRWD4)

Offset
Register Offset
DCMRWD4 50Ch
Function
Resets on destructive reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv TEST_ TEST_ Reserv VDD2 VDD1 Reserv FLAS Reserv FLAS FLAS FLAS FLAS FLAS Reserv Reserv
ed AC ... AC ... ed P5_ ... P1_ ... ed H_A ... ed H_S ... H_R ... H_R ... H_A ... H_E ... ed ed
W
Reset 1 1 1 0 1 1 1 1 0 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
PF1_D PF1_C PF0_D PF0_C HSE_ PRAM PRAM DMA_ Reserv Reserv Reserv CM7_0 CM7_0 CM7_0 Reserv CM7_0
AT ... OD ... AT ... OD ... RAM ... 1_F ... 0_F ... TCD ... ed ed ed _D ... _D ... _I ... ed _I ...
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
Reserved
31
—
Table continues on the next page...

---

*Page 915*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
Enable bit for enabling the fault monitoring at FCCU NCF 5 for the fault: Accidental partial test activation.
30
0b - No partial test activated accidentally.
TEST_ACTIVA
TION_1_ERR_E
1b - Partial test activated accidentally.
N
Enable bit for enabling the fault monitoring at FCCU NCF 5 for the fault: Accidental partial test activation.
29
0b - No partial test activated accidentally.
TEST_ACTIVA
TION_0_ERR_E
1b - Partial test activated accidentally.
N
Reserved
28
—
Enable bit for enabling the fault monitoring at FCCU NCF 4 for the fault: Go/Nogo for VDD_HV_FLA
27
(double bond) going to FXOSC and PLL.
VDD2P5_GNG_
0b - Go indication referring to the supply being clean.
ERR_EN
1b - No go indication referring to the supply being unclean and a fault in double bond connection
or its routing within the chip.
Enable bit for enabling the fault monitoring at FCCU NCF 4 for the fault: Go/Nogo indicator for VDD1PD1
26
(double bond) supply going to PLL.
VDD1P1_GNG_
0b - Go indication referring to the supply being clean.
ERR_EN
1b - No go indication referring to the supply being unclean and a fault in double bond connection
or its routing within the chip.
Reserved
25
—
Enable bit for enabling the fault monitoring at FCCU NCF 3 for the fault: Transaction monitor mismatch
24
error from flash controller. This alarm idicates that the flash controller detected a transaction monitor
FLASH_ACCES
mismatch when compared to flash safety feedback output. There is an indication in the flash where
S_ERR_EN
reconstructed address is compared with the address which invoked the flash access.
0b - No transaction monitor mismatch error from flash controller.
1b - Transaction monitor mismatch error from flash controller.
Reserved
23
—
Enable bit for enabling the fault monitoring at FCCU NCF 3 for the fault: Error while DCM flash scanning
22
process due to invalid data.
FLASH_SCAN_
ERR_EN NOTE
On any POR or destructive reset event, since this bit gets cleared, it has no effect and DCM
flash scanning error (in case if present) will not get disabled.
Table continues on the next page...

---

*Page 916*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
0b - No error while DCM flash scanning.
1b - Error while DCM flash scanning.
Enable bit for enabling the fault monitoring at FCCU NCF 3 for the fault: Flash reset error . This error
21
indication is set when flash encounters errors during its reset reads.
FLASH_RST_E
0b - No flash reset error indicated.
RR_EN
1b - Flash reset error indicated.
Enable bit for enabling the fault monitoring at FCCU NCF 3 for the fault: Flash reference current loss or
20
read voltage error while prevoius read(s).
FLASH_REF_E
0b - No reference current loss or read voltage error while previous reads(s).
RR_EN
1b - Reference current loss or read voltage error while previous read(s).
Enable bit for enabling the fault monitoring at FCCU NCF 3 for the fault: Flash address encode error. In
19
address decoding, if multiple or no address line is selected, FMU reports address encode error.
FLASH_ADDR_
0b - No address encode error in flash.
ENC_ERR_EN
1b - Address encode error in flash.
Enable bit for enabling the fault monitoring at FCCU NCF 3 for the fault: Flash ECC correction error
18
through EDC reported by FMU.
FLASH_EDC_E
0b - No EDC after ECC error reported in flash.
RR_EN
1b - EDC after ECC error reported in flash.
Reserved
17
—
Reserved
16
—
Enable bit for enabling the fault monitoring at FCCU NCF 3 for the fault: Flash1 data ECC uncorrectable
15
error.
PF1_DATA_EC
0b - No uncorrectable error reported in flash controller port 1 data memory by FMU.
C_ERR_EN
1b - Uncorrectable error reported in flash controller port 1 data memory by FMU.
Enable bit for enabling the fault monitoring at FCCU NCF 3 for the fault: Flash1 code ECC uncorrectable
14
error.
PF1_CODE_EC
0b - No uncorrectable error reported in flash controller port 1 code memory by FMU.
C_ERR_EN
1b - Uncorrectable error reported in flash controller port 1 code memory by FMU.
Enable bit for enabling the fault monitoring at FCCU NCF 3 for the fault: Flash0 data ECC uncorrectable
13
error.
PF0_DATA_EC
0b - No uncorrectable error reported in flash controller port 0 data memory by FMU.
C_ERR_EN
1b - Uncorrectable error reported in flash controller port 0 data memory by FMU.
Table continues on the next page...

---

*Page 917*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
Enable bit for enabling the fault monitoring at FCCU NCF 3 for the fault: Flash0 code ECC uncorrectable
12
error.
PF0_CODE_EC
0b - No uncorrectable error reported in flash controller port 0 code memory by FMU.
C_ERR_EN
1b - Uncorrectable error reported in flash controller port 0 code memory by FMU.
Enable bit for enabling the fault monitoring at FCCU NCF 2 for the fault: HSE RAM Uncorrectable ECC.
11
0b - No uncorrectable ECC reported by HSE RAM.
HSE_RAM_EC
C_ERR_EN
1b - Uncorrectable ECC reported by HSE RAM
Enable bit for enabling the fault monitoring at FCCU NCF 2 for the fault: PRAM1 safety alarm. This alarm
10
is set on faulty SRAM1 read or read modify error.
PRAM1_FCCU_
0b - No safety alarm indicated by PRAM1.
ALARM_EN
1b - Safety alarm indicated by PRAM1.
Enable bit for enabling the fault monitoring at FCCU NCF 2 for the fault: PRAM0 safety alarm. This alarm
9
is set on faulty SRAM0 read or read modify error.
PRAM0_FCCU_
0b - No safety alarm indicated by PRAM0.
ALARM_EN
1b - Safety alarm indicated by PRAM0.
Enable bit for enabling the fault monitoring at FCCU NCF 2 for the fault: Uncorrectable ECC error
8
reported from DMA_TCD memory. This uncorrectable ECC error consists of multi-bit data ECC error and
DMA_TCD_RA
address ECC error.
M_ECC_ERR_E
0b - Uncorrectable ECC error detection at FCCU not enabled.
N
1b - Uncorrectable ECC error detection enabled at FCCU.
Reserved
7
—
Reserved
6
—
Reserved
5
—
Enable bit for enabling the fault monitoring at FCCU NCF 2 for the fault: Uncorrectable ECC error from
4
CM7_0 Data TCM memory block 1. This uncorrectable ECC error consists of multi-bit data ECC error
CM7_0_DTCM1
and address ECC error. The CM7_0 Data TCM physically consists of two blocks.
_ECC_ERR_EN
NOTE
Address ECC error is not supported on CM7_0 Data TCM memory in MCXE31B.
0b - Uncorrectable ECC error detection at FCCU not enabled.
1b - Uncorrectable ECC error detection enabled at FCCU.
Table continues on the next page...

---

*Page 918*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
Enable bit for enabling the fault monitoring at FCCU NCF 2 for the fault: Uncorrectable ECC error from
3
CM7_0 Data TCM memory block 0. This uncorrectable ECC error consists of multi-bit data ECC error
CM7_0_DTCM0
and address ECC error. The CM7_0 Data TCM physically consists of two blocks.
_ECC_ERR_EN
NOTE
Address ECC error is not supported on CM7_0 Data TCM memory in MCXE314
and MCXE31B.
0b - Uncorrectable ECC error detection at FCCU not enabled.
1b - Uncorrectable ECC error detection enabled at FCCU.
Enable bit for enabling the fault monitoring at FCCU NCF 2 for the fault: Uncorrectable ECC error from
2
CM7_0 Instruction TCM memory. This uncorrectable ECC error consists of multi-bit data ECC error and
CM7_0_ITCM_
address ECC error.
ECC_ERR_EN
NOTE
Address ECC error is not supported on CM7_0 Instruction TCM memory in MCXE314
and MCXE31B.
0b - Uncorrectable ECC error detection at FCCU not enabled.
1b - Uncorrectable ECC error detection enabled at FCCU.
Reserved
1
—
Enable bit for enabling the fault monitoring at FCCU NCF 2 for the fault: Multi bit ECC error from CM7_0
0
ICache tag memory.
CM7_0_ICTAG_
0b - No multi-bit ECC error.
ECC_ERR_EN
1b - Multi-bit ECC error

#### 37.2.31 Read Write GPR On Destructive Reset Register (DCMRWD5)

Offset
Register Offset
DCMRWD5 510h
Function
Resets on destructive reset.

---

*Page 919*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
HSE_ CM7_0 CM7_0 Reserv Reserv DMA_ Reserv
Reserved
RDA ... _A ... _A ... ed ed RDA ... ed
W
Reset 0 0 0 0 0 0 0 0 0 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
EMAC TCM_ DEBU MTR_ STCU MBIST STCU SW_N SW_N SW_N SW_N INTM_ INTM_ INTM_ INTM_ Reserv
_RD ... RDA ... G_A ... BUS ... _BI ... _A ... _NC ... CF_ ... CF_ ... CF_ ... CF_ ... 3_ ... 2_ ... 1_ ... 0_ ... ed
W
Reset 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
Fields
Field Function
Reserved
31-23
—
Enable bit for enabling the fault monitoring at FCCU NCF 1 for the fault: Integrity (EDC) error on HSE
22
read data for safety.
HSE_RDATA_E
0b - No integrity error reported on HSE read data.
DC_ERR_EN
1b - Integrity error reported on HSE read data.
Enable bit for enabling the fault monitoring at FCCU NCF 1 for the fault: Integrity error on CM7_0 main
21
read data for safety.
CM7_0_AHBM_
0b - No integrity error reported on CM7_0 main read data.
RDATA_EDC_E
RR_EN
1b - Integrity error reported on CM7_0 main read data.
Enable bit for enabling the fault monitoring at FCCU NCF 1 for the fault: Integrity error on CM7_0
20
peripheral read data for safety.
CM7_0_AHBP_
0b - No integrity error reported on CM7_0 peripheral read data.
RDATA_EDC_E
RR_EN
1b - Integrity error reported on CM7_0 peripheral read data.
Reserved
19
—
Reserved
18
—
Enable bit for enabling the fault monitoring at FCCU NCF 1 for the fault: Integrity(EDC) error on eDMA
17
read data for safety.
DMA_RDATA_
0b - No integrity error reported on eDMA read data.
EDC_ERR_EN
1b - Integrity error reported on eDMA read data.
Reserved
16
Table continues on the next page...

---

*Page 920*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
—
Enable bit for enabling the fault monitoring at FCCU NCF 1 for the fault: Integrity(EDC) error on EMAC
15
read data for safety.
EMAC_RDATA
0b - No integrity error reported on EMAC read data.
_EDC_ERR_EN
1b - Integrity error reported on EMAC read data.
Enable bit for enabling the fault monitoring at FCCU NCF 1 for the fault: Integrity(EDC) error on TCM
14
read data for safety.
TCM_RDATA_E
0b - No integrity error reported on TCM read data.
DC_ERR_EN
1b - Integrity error reported on TCM read data
Enable bit for enabling the fault monitoring at FCCU NCF 5 for the fault: Monitoring of unintended debug
13
activation. This is set when the core is in halted state with application debug not enabled or debugger
DEBUG_ACTIV
request not enabled.
ATION_ERR_E
N NOTE
While debugger is connected, DCM.DCMRWD5[DEBUG_ACTIVATION_ERR_EN] should
be configured as 0, to disable debug activation error monitoring, since debugger is
intentionally connected to the chip.
0b - Unintended debug not activated.
1b - Unintended debug activated.
Enable bit for enabling the fault monitoring at FCCU NCF 5 for the fault: Fault reported due to illegal
12
access on MTR. This fault is reported via a transfer error indication to the system.
MTR_BUS_ER
0b - No transfer error indicated from MTR.
R_EN
1b - Transfer error indicated from MTR.
Enable bit for enabling the fault monitoring at FCCU NCF 5 for the fault: L/M BIST enabled accidentally.
11
Fault condition in run mode (LBIST or MBIST control signals go to wrong condition).
STCU_BIST_U
0b - No accidental L/M BIST enabled.
SER_CF_EN
1b - Accidental L/M BIST enabled.
Enable bit for enabling the fault monitoring at FCCU NCF 5 for the fault: Indicates an accidental
10
backdoor access on memories. This monitor needs to be disabled on FCCU when doing fault injection.
MBIST_ACTIVA
0b - No accidental backdoor enabled on memories.
TION_ERR_EN
1b - Accidental backdoor enabled on memories.
Enable bit for enabling the fault monitoring at FCCU NCF 5 for the fault: STCU non-critical fault, that is,
9
BIST result error.
STCU_NCF_EN
0b - STCU non-critical fault not reported.
1b - STCU non-critical fault reported.
Table continues on the next page...

---

*Page 921*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
Enable bit for enabling the fault monitoring at FCCU NCF 7 for the fault: Software NCF3 .
8
DCMRWF1[FCCU_SW_NCF3].
SW_NCF_3_EN
0b - Software NCF3, DCMRWF1[FCCU_SW_NCF3] not enabled by software.
1b - Software NCF3, DCMRWF1[FCCU_SW_NCF3] enabled by software.
Enable bit for enabling the fault monitoring at FCCU NCF 7 for the fault: Software NCF2 .
7
DCMRWF1[FCCU_SW_NCF2]
SW_NCF_2_EN
0b - Software NCF2, DCMRWF1[FCCU_SW_NCF2] not enabled by software.
1b - Software NCF2, DCMRWF1[FCCU_SW_NCF2] enabled by software.
Enable bit for enabling the fault monitoring at FCCU NCF 7 for the fault: Software NCF1 .
6
DCMRWF1[FCCU_SW_NCF1].
SW_NCF_1_EN
0b - Software NCF1, DCMRWF1[FCCU_SW_NCF1] not enabled by software.
1b - Software NCF1, DCMRWF1[FCCU_SW_NCF1] enabled by software.
Enable bit for enabling the fault monitoring at FCCU NCF 7 for the fault: Software NCF0 .
5
DCMRWF1[FCCU_SW_NCF0].
SW_NCF_0_EN
0b - Software NCF0, DCMRWF1[FCCU_SW_NCF0] not enabled by software.
1b - Software NCF0, DCMRWF1[FCCU_SW_NCF0] enabled by software.
Enable bit for enabling the fault monitoring at FCCU NCF 6 for the fault: Interrupt monitor3 error
4
reported by error reported by INTM. Also this error is reflected in INTM.INTM_STATUS3. See 'Functional
INTM_3_ERR_
Description' section in the INTM chapter for details.
EN
0b - No error reported by interrupt monitor 3.
1b - Error reported by interrupt monitor 3.
Enable bit for enabling the fault monitoring at FCCU NCF 6 for the fault: Interrupt monitor2 error
3
reported by error reported by INTM. Also this error is reflected in INTM.INTM_STATUS2. See 'Functional
INTM_2_ERR_
Description' section in the INTM chapter for details.
EN
0b - No error reported by interrupt monitor 2.
1b - Error reported by interrupt monitor 2.
Enable bit for enabling the fault monitoring at FCCU NCF 6 for the fault: Interrupt monitor1 error
2
reported by error reported by INTM. Also this error is reflected in INTM.INTM_STATUS1. See 'Functional
INTM_1_ERR_
Description' section in the INTM chapter for details.
EN
0b - No error reported by interrupt monitor 1.
1b - Error reported by interrupt monitor 1.
Enable bit for enabling the fault monitoring at FCCU NCF 6 for the fault: Interrupt monitor0 error reported
1
by INTM. Also this error is reflected in INTM.INTM_STATUS0. See 'Functional Description' section in the
INTM_0_ERR_
INTM chapter for details.
EN
0b - No error reported by interrupt monitor 0.
1b - Error reported by interrupt monitor 0.
Table continues on the next page...

---

*Page 922*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
Reserved
0
—

#### 37.2.32 Read Write GPR On Destructive Reset Register (DCMRWD6)

Offset
Register Offset
DCMRWD6 514h
Function
Resets on destructive reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SAI1_ SAI0_ FLEX FLEX FLEX FLEX FLEX FLEX FLEXI LPI2C LPI2C LPSPI LPSPI LPSPI LPSPI LPSPI
DB ... DB ... CAN ... CAN ... CAN ... CAN ... CAN ... CAN ... O_ ... 1_ ... 0_ ... 5_ ... 4_ ... 3_ ... 2_ ... 1_ ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
LPSPI PIT2_ PIT1_ PIT0_ STM1_ STM0_ SWT1 SWT0 RTC_ EMIO EMIO EMIO LCU1_ LCU0_ FCCU EDMA
0_ ... DB ... DB ... DB ... DB ... DB ... _DB ... _DB ... DBG ... S2_ ... S1_ ... S0_ ... DB ... DB ... _DB ... _DB ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
SAI1 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
31
0b - SAI1 enters debug mode when CM7_0 enters debug mode.
SAI1_DBG_DIS
_CM7_0
1b - SAI1 remains functional and is not impacted when CM7_0 enters debug mode.
SAI0 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
30
0b - SAI0 enters debug mode when CM7_0 enters debug mode.
SAI0_DBG_DIS
_CM7_0
1b - SAI0 remains functional and is not impacted when CM7_0 enters debug mode.
FLEXCAN5 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
29
0b - FLEXCAN5 enters debug mode when CM7_0 enters debug mode.
FLEXCAN5_DB
G_DIS_CM7_0
1b - FLEXCAN5 remains functional and is not impacted when CM7_0 enters debug mode.
Table continues on the next page...

---

*Page 923*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
FLEXCAN4 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
28
0b - FLEXCAN4 enters debug mode when CM7_0 enters debug mode.
FLEXCAN4_DB
G_DIS_CM7_0
1b - FLEXCAN4 remains functional and is not impacted when CM7_0 enters debug mode.
FLEXCAN3 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
27
0b - FLEXCAN3 enters debug mode when CM7_0 enters debug mode.
FLEXCAN3_DB
G_DIS_CM7_0
1b - FLEXCAN3 remains functional and is not impacted when CM7_0 enters debug mode.
FLEXCAN2 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
26
0b - FLEXCAN2 enters debug mode when CM7_0 enters debug mode.
FLEXCAN2_DB
G_DIS_CM7_0
1b - FLEXCAN2 remains functional and is not impacted when CM7_0 enters debug mode.
FLEXCAN1 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
25
0b - FLEXCAN1 enters debug mode when CM7_0 enters debug mode.
FLEXCAN1_DB
G_DIS_CM7_0
1b - FLEXCAN1 remains functional and is not impacted when CM7_0 enters debug mode.
FLEXCAN0 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
24
0b - FLEXCAN0 enters debug mode when CM7_0 enters debug mode.
FLEXCAN0_DB
G_DIS_CM7_0
1b - FLEXCAN0 remains functional and is not impacted when CM7_0 enters debug mode.
FLEXIO debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
23
0b - FLEXIO enters debug mode when CM7_0 enters debug mode.
FLEXIO_DBG_
DIS_CM7_0
1b - FLEXIO remains functional and is not impacted when CM7_0 enters debug mode.
LPI2C1 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
22
0b - LPI2C1 enters debug mode when CM7_0 enters debug mode.
LPI2C1_DBG_D
IS_CM7_0
1b - LPI2C1 remains functional and is not impacted when CM7_0 enters debug mode.
LPI2C0 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
21
0b - LPI2C0 enters debug mode when CM7_0 enters debug mode.
LPI2C0_DBG_D
IS_CM7_0
1b - LPI2C0 remains functional and is not impacted when CM7_0 enters debug mode.
LPSPI5 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
20
0b - LPSPI5 enters debug mode when CM7_0 enters debug mode.
LPSPI5_DBG_
DIS_CM7_0
1b - LPSPI5 remains functional and is not impacted when CM7_0 enters debug mode.
LPSPI4 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
19
0b - LPSPI4 enters debug mode when CM7_0 enters debug mode.
LPSPI4_DBG_
DIS_CM7_0
1b - LPSPI4 remains functional and is not impacted when CM7_0 enters debug mode.
LPSPI3 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
18
Table continues on the next page...

---

*Page 924*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
LPSPI3_DBG_ 0b - LPSPI3 enters debug mode when CM7_0 enters debug mode.
DIS_CM7_0
1b - LPSPI3 remains functional and is not impacted when CM7_0 enters debug mode.
LPSPI2 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
17
0b - LPSPI2 enters debug mode when CM7_0 enters debug mode.
LPSPI2_DBG_
DIS_CM7_0
1b - LPSPI2 remains functional and is not impacted when CM7_0 enters debug mode.
LPSPI1 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
16
0b - LPSPI1 enters debug mode when CM7_0 enters debug mode.
LPSPI1_DBG_
DIS_CM7_0
1b - LPSPI1 remains functional and is not impacted when CM7_0 enters debug mode.
LPSPI0 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
15
0b - LPSPI0 enters debug mode when CM7_0 enters debug mode.
LPSPI0_DBG_
DIS_CM7_0
1b - LPSPI0 remains functional and is not impacted when CM7_0 enters debug mode.
PIT2 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
14
0b - PIT2 enters debug mode when CM7_0 enters debug mode.
PIT2_DBG_DIS
_CM7_0
1b - PIT2 remains functional and is not impacted when CM7_0 enters debug mode.
PIT1 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
13
0b - PIT1 enters debug mode when CM7_0 enters debug mode.
PIT1_DBG_DIS
_CM7_0
1b - PIT1 remains functional and is not impacted when CM7_0 enters debug mode.
PIT0 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
12
0b - PIT0 enters debug mode when CM7_0 enters debug mode.
PIT0_DBG_DIS
_CM7_0
1b - PIT0 remains functional and is not impacted when CM7_0 enters debug mode.
STM1 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
11
0b - STM1 enters debug mode when CM7_0 enters debug mode.
STM1_DBG_DI
S_CM7_0
1b - STM1 remains functional and is not impacted when CM7_0 enters debug mode.
STM0 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
10
0b - STM0 enters debug mode when CM7_0 enters debug mode.
STM0_DBG_DI
S_CM7_0
1b - STM0 remains functional and is not impacted when CM7_0 enters debug mode.
SWT1 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
9
0b - SWT0 enters debug mode when CM7_0 enters debug mode.
SWT1_DBG_DI
S_CM7_0
1b - SWT0 remains functional and is not impacted when CM7_0 enters debug mode.
SWT0 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
8
0b - SWT0 enters debug mode when CM7_0 enters debug mode.
SWT0_DBG_DI
S_CM7_0
1b - SWT0 remains functional and is not impacted when CM7_0 enters debug mode.
Table continues on the next page...

---

*Page 925*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
RTC debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
7
0b - RTC enters debug mode when CM7_0 enters debug mode.
RTC_DBG_DIS
_CM7_0
1b - RTC remains functional and is not impacted when CM7_0 enters debug mode.
EMIOS2 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
6
0b - eMIOS2 enters debug mode when CM7_0 enters debug mode.
EMIOS2_DBG_
DIS_CM7_0
1b - eMIOS2 remains functional and is not impacted when CM7_0 enters debug mode.
EMIOS1 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
5
0b - eMIOS1 enters debug mode when CM7_0 enters debug mode.
EMIOS1_DBG_
DIS_CM7_0
1b - eMIOS1 remains functional and is not impacted when CM7_0 enters debug mode.
EMIOS0 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
4
0b - eMIOS0 enters debug mode when CM7_0 enters debug mode.
EMIOS0_DBG_
DIS_CM7_0
1b - eMIOS0 remains functional and is not impacted when CM7_0 enters debug mode.
LCU1 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
3
0b - LCU1 enters debug mode when CM7_0 enters debug mode.
LCU1_DBG_DI
S_CM7_0
1b - LCU1 remains functional and is not impacted when CM7_0 enters debug mode.
LCU0 debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
2
0b - LCU0 enters debug mode when CM7_0 enters debug mode.
LCU0_DBG_DI
S_CM7_0
1b - LCU0 remains functional and is not impacted when CM7_0 enters debug mode.
FCCU debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
1
0b - FCCU enters debug mode when CM7_0 enters debug mode.
FCCU_DBG_DI
S_CM7_0
1b - FCCU remains functional and is not impacted when CM7_0 enters debug mode.
EDMA debug disable bit for CM7_0. Set this bit 1 to disable the debug of IP.
0
0b - EDMA enters debug mode when CM7_0 enters debug mode.
EDMA_DBG_DI
S_CM7_0
1b - EDMA remains functional and is not impacted when CM7_0 enters debug mode.

#### 37.2.33 Read Write GPR On Destructive Reset Register (DCMRWD7)

Offset
Register Offset
DCMRWD7 518h
Function
Resets on destructive reset.

---

*Page 926*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv Reserv Reserv Reserv Reserv
Reserved
ed ed ed ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv
ed ed ed ed ed ed ed ed ed ed ed ed ed ed ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-21
—
Reserved
20
—
Reserved
19
—
Reserved
18
—
Reserved
17
—
Reserved
16
—
Reserved
15
—
Reserved
14
—
Reserved
13
—
Reserved
12
—
Table continues on the next page...

---

*Page 927*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
Reserved
11
—
Reserved
10
—
Reserved
9
—
Reserved
8
—
Reserved
7
—
Reserved
6
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
Reserved
2
—
Reserved
1
—
Reserved
0
—

#### 37.2.34 Read Write GPR On Destructive Reset Register (DCMRWD8)

Offset
Register Offset
DCMRWD8 51Ch

---

*Page 928*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Function
Resets on destructive reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv
ed ed ed ed ed ed ed ed ed ed ed ed ed ed ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv
ed ed ed ed ed ed ed ed ed ed ed ed ed ed ed ed
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
Reserved
28
—
Reserved
27
—
Reserved
26
—
Reserved
25
—
Reserved
24
—
Reserved
23
—
Table continues on the next page...

---

*Page 929*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
Reserved
22
—
Reserved
21
—
Reserved
20
—
Reserved
19
—
Reserved
18
—
Reserved
17
—
Reserved
16
—
Reserved
15
—
Reserved
14
—
Reserved
13
—
Reserved
12
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
Reserved
8
Table continues on the next page...

---

*Page 930*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
—
Reserved
7
—
Reserved
6
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
Reserved
2
—
Reserved
1
—
Reserved
0
—

#### 37.2.35 Read Write GPR On Destructive Reset Register (DCMRWD9)

Offset
Register Offset
DCMRWD9 520h
Function
Resets on destructive reset.

---

*Page 931*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserved Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Reserv Reserv Reserv Reserv Reserv
Reserved
ed ed ed ed ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-21
—
Reserved
20-5
—
Reserved
4
—
Reserved
3
—
Reserved
2
—
Reserved
1
—
Reserved
0
—

#### 37.2.36 Read Write GPR On Functional Reset Register (DCMRWF1)

Offset
Register Offset
DCMRWF1 600h
Function
Resets on functional reset.

---

*Page 932*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv VDD_1 VDD_ VDD_ VSS_L SUPP Reserv STAN
Reserved SUPPLY_MON_SEL Reserved
ed _5 ... HV_ ... HV_ ... V_ ... LY_ ... ed DBY ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
VDD_ Reserv Reserv Reserv Reserv Reserv Reserv Reserv RMII_ Reserv FCCU FCCU FCCU FCCU CAN_ CAN_
HV_ ... ed ed ed ed ed ed ed MI ... ed _SW ... _SW ... _SW ... _SW ... TIM ... TIM ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
Reserved
30-28
—
Enable for VDD1P5 2:1 divider for voltage measurement using supply voltage monitoring by ADC.
27
0b - Disabled
VDD_1_5_VLT_
DVDR_EN
1b - Enabled
Enable for VDD_HV_B 2:1 divider for voltage measurement using supply voltage monitoring by ADC.
26
0b - Disabled
VDD_HV_B_VL
T_DVDR_EN
1b - Enabled
Enable for VDD_HV_A 2:1 divider for voltage measurement using supply voltage monitoring by ADC.
25
0b - Disabled
VDD_HV_A_VL
T_DVDR_EN
1b - Enabled
Enable VSS_LV monitoring.
24
VSS_LV_ANMU NOTE
X_EN This field must be set alongwith SUPPLY_MON_EN configured as 1b0 for VSS_LV
monitoring by ADC0.
0b - Disabled
1b - Enabled
Selects the source of voltage used by ADC for supply monitoring.
23-21
SUPPLY_MON
_SEL
Table continues on the next page...

---

*Page 933*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
NOTE
The SUPPLY_MON_SEL configurations are effective only when SUPPLY_MON_EN is
configured as 1b1.
When SUPPLY_MON_EN is configured as 0 and VSS_LV_ANMUX_EN is configured as 1
then VSS_LV is monitored.
000b - VDD_HV_A_DIV
001b - VDD_HV_B_DIV
010b - VDD_1.5_DIV
011b - VDD_2.5_OSC
100b - VDD1.1_PD1_HOT_POINT
101b - VDD1.1_PD1_COLD_POINT
110b - VDD1.1_PLL
111b - VDD1.1_PD0
Enable the supply voltage monitoring by ADC.
20
0b - Disabled
SUPPLY_MON
_EN
1b - Enabled
Reserved
19-18
—
Reserved
17
—
Controls the IO state in the standby mode.
16
0b - STANDBY IO configuration standby entry: Must be written as 0 before IO configurations are
STANDBY_IO_
done in standby entry sequence.
CONFIG
1b - STANDBY IO configuration standby exit: Must be written as 1 after IO configurations are
done on standby exit.
15 Controls the IO controls latching in low frequency RUN mode to reduce power consumption on VDD_HV_B
domain pins.
VDD_HV_B_IO_
CTRL_LATCH
NOTE
This bit should remain 0, except in FIRC 3MHz and FIRC 187.5kHz operation mode.
0b - VDD_HV_B domain pins function as normal.
1b - The IO controls of VDD_HV_B domain pins are latched.
Reserved
14
—
Table continues on the next page...

---

*Page 934*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
Reserved
13
—
Reserved
12
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
Reserved
8
—
Selects between MII and RMII mode of ethernet.
7
0b - MII mode
RMII_MII_SEL
1b - RMII mode
Reserved
6
—
Control to initiate Software NCF to FCCU.
5
0b - NCF to FCCU will not be generated.
FCCU_SW_NC
F3
1b - NCF to FCCU will be generated. For exact slot of FCCU, please refer FCCU chapter.
Control to initiate Software NCF to FCCU.
4
0b - NCF to FCCU will not be generated.
FCCU_SW_NC
F2
1b - NCF to FCCU will be generated. For exact slot of FCCU, please refer FCCU chapter.
Control to initiate Software NCF to FCCU.
3
0b - NCF to FCCU will not be generated.
FCCU_SW_NC
F1
1b - NCF to FCCU will be generated. For exact slot of FCCU, please refer FCCU chapter.
Control to initiate Software NCF to FCCU.
2
0b - NCF to FCCU will not be generated.
FCCU_SW_NC
F0
1b - NCF to FCCU will be generated. For exact slot of FCCU, please refer FCCU chapter.
Enables CAN timestamping feature for all flexcans.
1
Table continues on the next page...

---

*Page 935*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
CAN_TIMESTA 0b - CAN timestamping is disabled.
MP_EN
1b - CAN timestamping is enabled.
Select between EMAC and STM for CAN timestamping
0
0b - EMAC selected for CAN timestamping.
CAN_TIMESTA
MP_SEL
1b - STM0 selected for CAN timestamping.

#### 37.2.37 Read Write GPR On Functional Reset Register (DCMRWF2)

Offset
Register Offset
DCMRWF2 604h
Function
Resets on functional reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv HSE_
Reserved
ed ed ed ed ed ed ed ed GSK ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
SIRC_ PMC_ FIRC_ DCM_ Reserv
Reserved Reserved
TR ... TRI ... TR ... SCA ... ed
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
Table continues on the next page...

---

*Page 936*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
—
Reserved
28
—
Reserved
27
—
Reserved
26
—
Reserved
25
—
Reserved
24
—
Reserved
23-17
—
16 Control to enable the HSE IAHB gasket behavior out of standby mode.
HSE_GSKT_BY
NOTE
PASS
If configured as 1 to bypass HSE IAHB gasket out of standby, the system should continue
to run on FIRC. If intended to be run on PLL, a functional reset should be asserted.
0b - When configured as 0, HSE IAHB gasket bypass configuration is controlled by DCF client.
1b - When configured as 1, HSE IAHB gasket is bypassed out of standby.
Reserved
15-7
—
Control to bypass the SIRC trimming on standby exit.
6
0b - Not bypassed
SIRC_TRIM_BY
P_STDBY_EXT
1b - Bypassed
Control to bypass the PMC trimming and RGM DCF loading on standby exit.
5
0b - Not bypassed
PMC_TRIM_RG
M_DCF__BYP_
1b - Bypassed
STDBY_EXT
Control to bypass the FIRC trimming on standby exit.
4
0b - Not bypassed
FIRC_TRIM_BY
P_STDBY_EXT
1b - Bypassed
Table continues on the next page...

---

*Page 937*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
Control to bypass the DCM scanning on standby exit.
3
0b - Not bypassed
DCM_SCAN_B
YP_STDBY_EX
1b - Bypassed
T
Reserved
2-1
—
Reserved
0
—

#### 37.2.38 Read Write GPR On Functional Reset Register (DCMRWF4)

Offset
Register Offset
DCMRWF4 60Ch
Function
Resets on functional reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Reserv Reserv CM7_0 GLITC
Reserved
ed ed _C ... H_ ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
GLITC GLITC GLITC MUX_ MUX_ MUX_ MUX_ MUX_ MUX_ MUX_ MUX_ Reserv
Reserved Reserved
H_ ... H_ ... H_ ... MOD ... MOD ... MOD ... MOD ... MOD ... MOD ... MOD ... MOD ... ed
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-20
—
Reserved
19
Table continues on the next page...

---

*Page 938*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
—
Reserved
18
—
Configuration to put CM7_0 core into wait mode.
17
0b - CPUWAIT for CM7_0 disabled.
CM7_0_CPUW
AIT
1b - CM7_0 put into wait mode using CPUWAIT.
Selects whether to BYPASS or filter out the pulse.
16
0b - Glitch filter on TRGMUX input60 is enabled.
GLITCH_FIL_T
RG_IN3_BYP
1b - Glitch filter on TRGMUX input60 is bypassed.
Selects whether to BYPASS or filter out the pulse.
15
0b - Glitch filter on TRGMUX input61 is enabled.
GLITCH_FIL_T
RG_IN2_BYP
1b - Glitch filter on TRGMUX input61 is bypassed.
Selects whether to BYPASS or filter out the pulse.
14
0b - Glitch filter on TRGMUX input62 is enabled.
GLITCH_FIL_T
RG_IN1_BYP
1b - Glitch filter on TRGMUX input62 is bypassed.
Selects whether to BYPASS or filter out the pulse.
13
0b - Glitch filter on TRGMUX input63 is enabled.
GLITCH_FIL_T
RG_IN0_BYP
1b - Glitch filter on TRGMUX input63 is bypassed.
Reserved
12-11
—
Selects the GPIO46 to drive adc2 standard channel 9th.
10
0b - Selects the default PAD 1.
MUX_MODE_E
N_ADC2_S9
1b - Selects the PAD mentioned in description.
Selects the GPIO45 to drive adc2 standard channel 8th.
9
0b - Selects the default PAD 1.
MUX_MODE_E
N_ADC2_S8
1b - Selects the PAD mentioned in description.
Reserved
8-7
—
Selects the GPIO115 to drive ADC1 standard channel 23rd.
6
0b - Selects the default PAD 1.
MUX_MODE_E
N_ADC1_S23
1b - Selects the PAD mentioned in description.
Table continues on the next page...

---

*Page 939*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
Selects the GPIO114 to drive ADC1 standard channel 22nd.
5
0b - Selects the default PAD 1.
MUX_MODE_E
N_ADC1_S22
1b - Selects the PAD mentioned in description.
Selects the GPIO33 to drive ADC1 standard channel 15th.
4
0b - Selects the default PAD 1.
MUX_MODE_E
N_ADC1_S15
1b - Selects the PAD mentioned in description.
Selects the GPIO32 to drive ADC1 standard channel 14th.
3
0b - Selects the default PAD 1.
MUX_MODE_E
N_ADC1_S14
1b - Selects the PAD mentioned in description.
Selects the GPIO46 to drive ADC0 standard channel 9th.
2
0b - Selects the default PAD 1.
MUX_MODE_E
N_ADC0_S9
1b - Selects the PAD mentioned in description.
Selects the GPIO45 to drive ADC0 standard channel 8th.
1
0b - Selects the default PAD 1.
MUX_MODE_E
N_ADC0_S8
1b - Selects the PAD mentioned in description.
Reserved
0
—

#### 37.2.39 Read Write GPR On Functional Reset Register (DCMRWF5)

Offset
Register Offset
DCMRWF5 610h
Function
Resets on functional reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
BOOT_ADDRESS
W
Reset u u u u u u u u u u u u u u u u

---

*Page 940*

Device Configuration Module General-Purpose Registers (DCM_GPR)
1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
BOOT
BOOT_ADDRESS
_MO ...
W
Reset u u u u u u u u u u u u u u u u
1. The reset value is undefined on reset and gets loaded from the flash contents at the end of reset sequence.
Fields
Field Function
Cortex-M7_0 base address of vector table to be used after exiting Standby mode (only to be considered
31-1
in Fast Standby mode.
BOOT_ADDRE
SS
Selects the boot mode after exiting Standby mode.
0
0b - Normal
BOOT_MODE
1b - Fast Standby

#### 37.2.40 Read Only GPR On PMCPOR Reset (DCMROPP1)

Offset
Register Offset
DCMROPP1 700h
Function
Resets on PMCPOR reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
POR_ POR_ POR_ POR_ POR_
Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv Reserv
R WDG .. WDG .. WDG .. WDG .. WDG ..
ed ed ed ed ed ed ed ed ed ed ed
. . . . .
W W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_
Reserv Reserv Reserv Reserv Reserv Reserv
R WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG ..
ed ed ed ed ed ed
. . . . . . . . . .
W W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 941*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Fields
Field Function
31 This bit captures MC_RGM reset event (if occured) while the device is in STANDBY mode.
POR_WDG_ST
NOTE
AT31
This bit is applicable only for standby sequence monitoring.
0b - MC_RGM reset event not detected or no reset event arrived in STANDBY mode when
POR_WDG overflows.
1b - MC_RGM reset event is detected in STANDBY mode when POR_WDG overflows.
30 This bit captures the status of standby exit acknowledgement by MC_PCU when POR_WDG overflows.
POR_WDG_ST
NOTE
AT30
This bit is applicable only for standby sequence monitoring.
0b - MC_PCU has not acknowledged standby exit when POR_WDG overflows.
1b - MC_PCU has acknowledged standby exit when POR_WDG overflows.
29 This bit captures the status of standby entry request initiated by MC_ME when POR_WDG overflows.
POR_WDG_ST
NOTE
AT29
This bit is applicable only for standby sequence monitoring.
0b - MC_ME standby request is active when POR_WDG overflows.
1b - MC_ME standby request is not active when POR_WDG overflows.
Reserved
28
—
Reserved
27
—
Reserved
26
—
Reserved
25
—
Reserved
24
—
Reserved
23
—
Reserved
22
—
Table continues on the next page...

---

*Page 942*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
Reserved
21
—
This bit captures the status of functional reset sequence process DEST0 when POR_WDG overflows.
20
0b - DEST0 is inactive when POR_WDG overflows.
POR_WDG_ST
AT20
1b - DEST0 is active when POR_WDG overflows.
Reserved
19
—
Reserved
18
—
This bit captures the status of functional reset sequence process FUNC10 when POR_WDG overflows.
17
0b - FUNC10 is inactive when POR_WDG overflows.
POR_WDG_ST
AT17
1b - FUNC10 is active when POR_WDG overflows.
Reserved
16
—
Reserved
15
—
This bit captures the status of functional reset sequence process FUNC9 when POR_WDG overflows.
14
0b - FUNC9 is inactive when POR_WDG overflows.
POR_WDG_ST
AT14
1b - FUNC9 is active when POR_WDG overflows.
Reserved
13
—
Reserved
12
—
This bit captures the status of functional reset sequence process FUNC7 when POR_WDG overflows.
11
0b - FUNC8 is inactive when POR_WDG overflows.
POR_WDG_ST
AT11
1b - FUNC8 is active when POR_WDG overflows.
This bit captures the status of functional reset sequence process FUNC7 when POR_WDG overflows.
10
0b - FUNC7 is inactive when POR_WDG overflows.
POR_WDG_ST
AT10
1b - FUNC7 is active when POR_WDG overflows.
Reserved
9
Table continues on the next page...

---

*Page 943*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
—
Reserved
8
—
Reserved
7
—
This bit captures the status of functional reset sequence process FUNC6 when POR_WDG overflows.
6
0b - FUNC6 is inactive when POR_WDG overflows.
POR_WDG_ST
AT6
1b - FUNC6 is active when POR_WDG overflows.
This bit captures the status of functional reset sequence process FUNC5 when POR_WDG overflows.
5
0b - FUNC5 is inactive when POR_WDG overflows.
POR_WDG_ST
AT5
1b - FUNC5 is active when POR_WDG overflows.
This bit captures the status of functional reset sequence process FUNC4 when POR_WDG overflows.
4
0b - FUNC4 is inactive when POR_WDG overflows.
POR_WDG_ST
AT4
1b - FUNC4 is active when POR_WDG overflows.
This bit captures the status of functional reset sequence process FUNC3 when POR_WDG overflows.
3
0b - FUNC3 is inactive when POR_WDG overflows.
POR_WDG_ST
AT3
1b - FUNC3 is active when POR_WDG overflows.
This bit captures the status of functional reset sequence process FUNC2 when POR_WDG overflows.
2
0b - FUNC2 is inactive when POR_WDG overflows.
POR_WDG_ST
AT2
1b - FUNC2 is active when POR_WDG overflows.
This bit captures the status of functional reset sequence process FUNC1 when POR_WDG overflows.
1
0b - FUNC1 is inactive when POR_WDG overflows.
POR_WDG_ST
AT1
1b - FUNC1 is active when POR_WDG overflows.
This bit captures the status of functional reset sequence process FUNC0 when POR_WDG overflows.
0
0b - FUNC0 is inactive when POR_WDG overflows.
POR_WDG_ST
AT0
1b - FUNC0 is active when POR_WDG overflows.

#### 37.2.41 Read Only GPR On PMCPOR Reset (DCMROPP2)

Offset
Register Offset
DCMROPP2 704h

---

*Page 944*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Function
Resets on PMCPOR reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_
R WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG ..
. . . . . . . . . . . . . . . .
W W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_
R WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG ..
. . . . . . . . . . . . . . . .
W W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
31
0b - MC_RGM.RGM_FES[31] is 0 when POR_WDG overflows.
POR_WDG_ST
AT63
1b - MC_RGM.RGM_FES[31] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
30
0b - MC_RGM.RGM_FES[30] is 0 when POR_WDG overflows.
POR_WDG_ST
AT62
1b - MC_RGM.RGM_FES[30] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
29
0b - MC_RGM.RGM_FES[29] is 0 when POR_WDG overflows.
POR_WDG_ST
AT61
1b - MC_RGM.RGM_FES[29] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
28
0b - MC_RGM.RGM_FES[28] is 0 when POR_WDG overflows.
POR_WDG_ST
AT60
1b - MC_RGM.RGM_FES[28] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
27
0b - MC_RGM.RGM_FES[27] is 0 when POR_WDG overflows.
POR_WDG_ST
AT59
1b - MC_RGM.RGM_FES[27] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
26
0b - MC_RGM.RGM_FES[26] is 0 when POR_WDG overflows.
POR_WDG_ST
AT58
1b - MC_RGM.RGM_FES[26] is 1 when POR_WDG overflows.
Table continues on the next page...

---

*Page 945*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
25
0b - MC_RGM.RGM_FES[25] is 0 when POR_WDG overflows.
POR_WDG_ST
AT57
1b - MC_RGM.RGM_FES[25] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
24
0b - MC_RGM.RGM_FES[24] is 0 when POR_WDG overflows.
POR_WDG_ST
AT56
1b - MC_RGM.RGM_FES[24] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
23
0b - MC_RGM.RGM_FES[23] is 0 when POR_WDG overflows.
POR_WDG_ST
AT55
1b - MC_RGM.RGM_FES[23] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
22
0b - MC_RGM.RGM_FES[22] is 0 when POR_WDG overflows.
POR_WDG_ST
AT54
1b - MC_RGM.RGM_FES[22] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
21
0b - MC_RGM.RGM_FES[21] is 0 when POR_WDG overflows.
POR_WDG_ST
AT53
1b - MC_RGM.RGM_FES[21] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
20
0b - MC_RGM.RGM_FES[20] is 0 when POR_WDG overflows.
POR_WDG_ST
AT52
1b - MC_RGM.RGM_FES[20] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
19
0b - MC_RGM.RGM_FES[19] is 0 when POR_WDG overflows.
POR_WDG_ST
AT51
1b - MC_RGM.RGM_FES[19] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
18
0b - MC_RGM.RGM_FES[18] is 0 when POR_WDG overflows.
POR_WDG_ST
AT50
1b - MC_RGM.RGM_FES[18] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
17
0b - MC_RGM.RGM_FES[17] is 0 when POR_WDG overflows.
POR_WDG_ST
AT49
1b - MC_RGM.RGM_FES[17] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
16
0b - MC_RGM.RGM_FES[16] is 0 when POR_WDG overflows.
POR_WDG_ST
AT48
1b - MC_RGM.RGM_FES[16] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
15
Table continues on the next page...

---

*Page 946*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
POR_WDG_ST 0b - MC_RGM.RGM_FES[15] is 0 when POR_WDG overflows.
AT47
1b - MC_RGM.RGM_FES[15] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
14
0b - MC_RGM.RGM_FES[14] is 0 when POR_WDG overflows.
POR_WDG_ST
AT46
1b - MC_RGM.RGM_FES[14] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
13
0b - MC_RGM.RGM_FES[13] is 0 when POR_WDG overflows.
POR_WDG_ST
AT45
1b - MC_RGM.RGM_FES[13] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
12
0b - MC_RGM.RGM_FES[12] is 0 when POR_WDG overflows.
POR_WDG_ST
AT44
1b - MC_RGM.RGM_FES[12] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
11
0b - MC_RGM.RGM_FES[11] is 0 when POR_WDG overflows.
POR_WDG_ST
AT43
1b - MC_RGM.RGM_FES[11] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
10
0b - MC_RGM.RGM_FES[10] is 0 when POR_WDG overflows.
POR_WDG_ST
AT42
1b - MC_RGM.RGM_FES[10] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
9
0b - MC_RGM.RGM_FES[9] is 0 when POR_WDG overflows.
POR_WDG_ST
AT41
1b - MC_RGM.RGM_FES[9] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
8
0b - MC_RGM.RGM_FES[8] is 0 when POR_WDG overflows.
POR_WDG_ST
AT40
1b - MC_RGM.RGM_FES[8] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
7
0b - MC_RGM.RGM_FES[7] is 0 when POR_WDG overflows.
POR_WDG_ST
AT39
1b - MC_RGM.RGM_FES[7] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
6
0b - MC_RGM.RGM_FES[6] is 0 when POR_WDG overflows.
POR_WDG_ST
AT38
1b - MC_RGM.RGM_FES[6] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
5
0b - MC_RGM.RGM_FES[5] is 0 when POR_WDG overflows.
POR_WDG_ST
AT37
1b - MC_RGM.RGM_FES[5] is 1 when POR_WDG overflows.
Table continues on the next page...

---

*Page 947*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
4
0b - MC_RGM.RGM_FES[4] is 0 when POR_WDG overflows.
POR_WDG_ST
AT36
1b - MC_RGM.RGM_FES[4] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
3
0b - MC_RGM.RGM_FES[3] is 0 when POR_WDG overflows.
POR_WDG_ST
AT35
1b - MC_RGM.RGM_FES[3] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
2
0b - MC_RGM.RGM_FES[2] is 0 when POR_WDG overflows.
POR_WDG_ST
AT34
1b - MC_RGM.RGM_FES[2] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
1
0b - MC_RGM.RGM_FES[1] is 0 when POR_WDG overflows.
POR_WDG_ST
AT33
1b - MC_RGM.RGM_FES[1] is 1 when POR_WDG overflows.
This bit captures MC_RGM functional/external event status register when POR_WDG overflows.
0
0b - MC_RGM.RGM_FES[0] is 0 when POR_WDG overflows.
POR_WDG_ST
AT32
1b - MC_RGM.RGM_FES[0] is 1 when POR_WDG overflows.

#### 37.2.42 Read Only GPR On PMCPOR Reset (DCMROPP3)

Offset
Register Offset
DCMROPP3 708h
Function
Resets on PMCPOR reset.

---

*Page 948*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_
R WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG ..
. . . . . . . . . . . . . . . .
W W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_ POR_
R WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG .. WDG ..
. . . . . . . . . . . . . . . .
W W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
31
0b - MC_RGM.RGM_DES[31] is 0 when POR_WDG overflows.
POR_WDG_ST
AT95
1b - MC_RGM.RGM_DES[31] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
30
0b - MC_RGM.RGM_DES[30] is 0 when POR_WDG overflows.
POR_WDG_ST
AT94
1b - MC_RGM.RGM_DES[30] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
29
0b - MC_RGM.RGM_DES[29] is 0 when POR_WDG overflows.
POR_WDG_ST
AT93
1b - MC_RGM.RGM_DES[29] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
28
0b - MC_RGM.RGM_DES[28] is 0 when POR_WDG overflows.
POR_WDG_ST
AT92
1b - MC_RGM.RGM_DES[28] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
27
0b - MC_RGM.RGM_DES[27] is 0 when POR_WDG overflows.
POR_WDG_ST
AT91
1b - MC_RGM.RGM_DES[27] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
26
0b - MC_RGM.RGM_DES[26] is 0 when POR_WDG overflows.
POR_WDG_ST
AT90
1b - MC_RGM.RGM_DES[26] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
25
Table continues on the next page...

---

*Page 949*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
POR_WDG_ST 0b - MC_RGM.RGM_DES[25] is 0 when POR_WDG overflows.
AT89
1b - MC_RGM.RGM_DES[25] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
24
0b - MC_RGM.RGM_DES[24] is 0 when POR_WDG overflows.
POR_WDG_ST
AT88
1b - MC_RGM.RGM_DES[24] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
23
0b - MC_RGM.RGM_DES[23] is 0 when POR_WDG overflows.
POR_WDG_ST
AT87
1b - MC_RGM.RGM_DES[23] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
22
0b - MC_RGM.RGM_DES[22] is 0 when POR_WDG overflows.
POR_WDG_ST
AT86
1b - MC_RGM.RGM_DES[22] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
21
0b - MC_RGM.RGM_DES[21] is 0 when POR_WDG overflows.
POR_WDG_ST
AT85
1b - MC_RGM.RGM_DES[21] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
20
0b - MC_RGM.RGM_DES[20] is 0 when POR_WDG overflows.
POR_WDG_ST
AT84
1b - MC_RGM.RGM_DES[20] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
19
0b - MC_RGM.RGM_DES[19] is 0 when POR_WDG overflows.
POR_WDG_ST
AT83
1b - MC_RGM.RGM_DES[19] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
18
0b - MC_RGM.RGM_DES[18] is 0 when POR_WDG overflows.
POR_WDG_ST
AT82
1b - MC_RGM.RGM_DES[18] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
17
0b - MC_RGM.RGM_DES[17] is 0 when POR_WDG overflows.
POR_WDG_ST
AT81
1b - MC_RGM.RGM_DES[17] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
16
0b - MC_RGM.RGM_DES[16] is 0 when POR_WDG overflows.
POR_WDG_ST
AT80
1b - MC_RGM.RGM_DES[16] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
15
0b - MC_RGM.RGM_DES[15] is 0 when POR_WDG overflows.
POR_WDG_ST
AT79
1b - MC_RGM.RGM_DES[15] is 1 when POR_WDG overflows.
Table continues on the next page...

---

*Page 950*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
14
0b - MC_RGM.RGM_DES[14] is 0 when POR_WDG overflows.
POR_WDG_ST
AT78
1b - MC_RGM.RGM_DES[14] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
13
0b - MC_RGM.RGM_DES[13] is 0 when POR_WDG overflows.
POR_WDG_ST
AT77
1b - MC_RGM.RGM_DES[13] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
12
0b - MC_RGM.RGM_DES[12] is 0 when POR_WDG overflows.
POR_WDG_ST
AT76
1b - MC_RGM.RGM_DES[12] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
11
0b - MC_RGM.RGM_DES[11] is 0 when POR_WDG overflows.
POR_WDG_ST
AT75
1b - MC_RGM.RGM_DES[11] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
10
0b - MC_RGM.RGM_DES[10] is 0 when POR_WDG overflows.
POR_WDG_ST
AT74
1b - MC_RGM.RGM_DES[10] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
9
0b - MC_RGM.RGM_DES[9] is 0 when POR_WDG overflows.
POR_WDG_ST
AT73
1b - MC_RGM.RGM_DES[9] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
8
0b - MC_RGM.RGM_DES[8] is 0 when POR_WDG overflows.
POR_WDG_ST
AT72
1b - MC_RGM.RGM_DES[8] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
7
0b - MC_RGM.RGM_DES[7] is 0 when POR_WDG overflows.
POR_WDG_ST
AT71
1b - MC_RGM.RGM_DES[7] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
6
0b - MC_RGM.RGM_DES[6] is 0 when POR_WDG overflows.
POR_WDG_ST
AT70
1b - MC_RGM.RGM_DES[6] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
5
0b - MC_RGM.RGM_DES[5] is 0 when POR_WDG overflows.
POR_WDG_ST
AT69
1b - MC_RGM.RGM_DES[5] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
4
Table continues on the next page...

---

*Page 951*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Table continued from the previous page...
Field Function
POR_WDG_ST 0b - MC_RGM.RGM_DES[4] is 0 when POR_WDG overflows.
AT68
1b - MC_RGM.RGM_DES[4] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
3
0b - MC_RGM.RGM_DES[3] is 0 when POR_WDG overflows.
POR_WDG_ST
AT67
1b - MC_RGM.RGM_DES[3] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
2
0b - MC_RGM.RGM_DES[2] is 0 when POR_WDG overflows.
POR_WDG_ST
AT66
1b - MC_RGM.RGM_DES[2] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
1
0b - MC_RGM.RGM_DES[1] is 0 when POR_WDG overflows.
POR_WDG_ST
AT65
1b - MC_RGM.RGM_DES[1] is 1 when POR_WDG overflows.
This bit captures MC_RGM destructive event status register when POR_WDG overflows.
0
0b - MC_RGM.RGM_DES[0] is 0 when POR_WDG overflows.
POR_WDG_ST
AT64
1b - MC_RGM.RGM_DES[0] is 1 when POR_WDG overflows.

#### 37.2.43 Read Only GPR On PMCPOR Reset (DCMROPP4)

Offset
Register Offset
DCMROPP4 70Ch
Function
Resets on PMCPOR reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R Reserved
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
POR_
R Reserved
WDG ...
W W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 952*

Device Configuration Module General-Purpose Registers (DCM_GPR)
Fields
Field Function
Reserved
31-1
—
This bit captures POR_WDG reset event if POR_WDG initiates a POR sequence.
0
0b - POR_WDG is inactive.
POR_WDG_ST
AT96
1b - POR_WDG has detected a stuck scenario and raised device POR event. Refer
POR_WDG_STAT[95:0] for device status when POR_WDG overflows

---

*Page 953*

