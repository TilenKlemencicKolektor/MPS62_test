<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 4 -->

# Chapter 4

# Signal Multiplexing

#### 4.1 Introduction

The signal multiplexing enables the sharing of single pad for multiple functions.
The signal multiplexing unit comprises control signals from SIUL2 and pad interface logic. The signal multiplexing unit consists of
several individual sub-units, each handling the signal multiplexing of one pad.
The "SIUL2 Multiplexed Signal Configuration Register (MSCR)" controls the module specific pad settings (pull-up etc) and the
signal present on the external pin. See SIUL2_MSCR for the description of control signals. The pad attributes may vary depending
on the pad type.
For the pad attributes of each pad type and their reset values per port, see the IOMUX file attached to this document. The pads
specific to the packages and their multiplexing is also documented in the IOMUX file attached to this document.
NOTE
The input functions for the protocols which are not to be used should be appropriated configured as 'disabled
low'/'disabled high' with appropriate SIUL2.IMCR configurations corresponding for that function.

#### 4.2 Pad description

Following figure shows the basic representation of a GPIO pad.
OBE
DO
Pad
Output
SRC
driver
DSE
PUE
PUS
Pull logic
Input Low-pass
IND
receiver filter
lBE
IFE
Figure 7. GPIO pad representation
Table 9. Pad Signal description
Signal name Direction Descriprtion
Pad I/O I/O to external world
DO I Data coming from the core into the pad
OBE I Enable output driver
Table continues on the next page...

---

*Page 28*

Signal Multiplexing
Table 9. Pad Signal description (continued)
Signal name Direction Descriprtion
PUE I 0: Disable internal pullup or pulldown resistor 1: Enable internal pullup or
pulldown resistor
PUS I 0: Enable internal pulldown resistor if pue is set 1: Enable internal pullup
resistor if pue is set
IBE I Enable input receiver
IND O Data coming out of the pad into the core
SRC I Slew Rate Control
PKE I Pad keeping enable
IFE I Input filter enable
DSE I Drive Strength enable
Table 10. Input buffer enable
IBE Pad IND Description
0 X 0 Input buffer disabled, ind gets low
1 0/1 0/1 Input buffer enabled, ind = pad
Table 11. Output buffer enable
OBE DO Pad Description
0 X Z Output buffer disabled, pad hi-Z (If not configured as input)
1 0/1 0/1 Output buffer enable, pad = do
Table 12. Input filter enable
IFE Description
1 Input filter enabled
0 Input filter disabled
Table 13. Pull up/Pull down
PUE PUS Pad Description
0 X - Weak pull disabled. Pad retains previous state
1 0 0 Weak pull down enabled
1 1 1 Weak pull up enabled
Table 14. Slew rate control
SRC Description
0 Slew rate enabled
1 Slew rate disabled

---

*Page 29*

Signal Multiplexing
Table 15. Drive strength enable
DSE Description
1 Drive strength supported
0 Drive strength not supported
Table 16. Pad keeping enable
PKE Description
0 Pad keeping disabled
1 Pad keeping enabled
NOTE
The default state of GPIO pins on a reset event is high-Z. The high-Z state might settle to active high or active low
at chip depending on the supply, temperature and other factors. Hence, it is recommended to use external pulls to
ensure safe inactive state in event of a reset.

#### 4.3 Functional description

The signal multiplexing architectural implementation is as shown in the following figure.
IND IND IND
GPIO
Pad controls
Signal Functional
DO DO
Padring multiplexing modules or
unit peripherals
OBE OBE
IBE IBE
Figure 8. Signal Multiplexing

#### 4.4 Signal Multiplexing sheet

IO Signal Description Input Multiplexing sheet(s) attached to the Reference Manual contains information on pins/balls of
this device.
The 'IO Signal Table' and 'Input Muxing' tabs in the sheet correspond to the signal multiplexing information. The 'IO Signal Table'
consists of all the pin muxing details and the 'Input Muxing' specifies the priority for the input muxing where an input path is driven
by more than one pad.

#### 4.4.1 IO Signal Table

Following is an example snippet of IO Signal Table. For selecting any functionality, the pad MSCR register (refer to
SIUL2_MSCRn) needs to be configured accordingly.

---

*Page 30*

Signal Multiplexing
MSCR
Pad State During Pad State After Pad State During Pad State After Pad State After FCCU
Port CR SSS Function Module Description Direction Pad Type MCXE314_172mqfp I/O Power Domain Destructive Reset Destructive Reset Functional Reset Functional Reset Pad State After Selftest Error I/O Power Segment OBE[21] IBE[19] INV[17] PKE[16] SRC[14] PUE[13] PUS[11] DSE[8] IFE[6] SMC[5] SSS[2:0]
PTA0 SIUL_MSCR0 0000_0000 GPIO[0] SIUL I/O GPIO-STANDARD 137 VDD_HV_A Hi-Z Hi-Z Hi-Z Hi-Z Hi-Z Hi-Z 0 0 0 0 - 0 0 - - 0 000
PTA0 0000_0001 LPSPI4_PCS2 LPSPI4 Peripheral Chip Select 2 O
PTA0 0000_0010 eMIOS_0_CH[17]_Y eMIOS_0 eMIOS Channel O
PTA0 0000_0011 LCU0_OUT4 LCU0 LCU Output O
PTA0 0000_0100 FXIO_D2 FXIO FlexIO Bi-directional Shift/timer I/O O
PTA0 0000_0101 eMIOS_1_CH[0]_X eMIOS_1 eMIOS Channel O
PTA0 0000_0110 LPSPI0_PCS7 LPSPI0 Peripheral Chip Select 7 O
PTA0 0000_0111 TRGMUX_OUT3 TRGMUX Trigger Mux Output O
PTA0 - - ADC0_S8 ADC0 ADC Standard Input I
PTA0 - - CMP1_IN0 CMP1 Comparator Input Signal I
PTA0 SIUL_IMCR528 0000_0001 EIRQ[0] SIUL External Interrupt I
PTA0 SIUL_IMCR577 0000_0010 eMIOS_0_CH[17]_Y eMIOS_0 eMIOS Channel I
PTA0 SIUL_IMCR592 0000_0011 eMIOS_1_CH[0]_X eMIOS_1 eMIOS Channel I
PTA0 SIUL_IMCR666 0000_0010 FXIO_D2 FXIO FlexIO Bi-directional Shift/timer I/O I
PTA0 SIUL_IMCR740 0000_0001 LPSPI0_PCS7 LPSPI0 Peripheral Chip Select 7 I
PTA0 SIUL_IMCR769 0000_0001 LPSPI4_PCS2 LPSPI4 Peripheral Chip Select 2 I
PTA0 SIUL_IMCR872 0000_0001 LPUART0_CTS LPUART0 Clear To Send (bar) I
Figure 9. IO signal table snippet
The columns of the above figure are described below:
• Port: This field in IO Signal Table specifies the PAD names of the device.
• CR(Control Register): This field specifies the name of MSCR corresponding to the Port field. 'On this device, there are eight
port groups (PTA, PTB, PTC, PTD, PTE, PTF and PTG ) that are controlled by SIUL2_MSCRn registers. The below table
shows the mapping of ports with respect to SIUL2_MSCRn registers.
Table 17. Port/MSCR mapping
Port SIUL2_MSCRn index
PortA[0-31] MSCR0 - MSCR31
PortB[0-31] MSCR32 - MSCR63
PortC[0-31] MSCR64 - MSCR95
PortD[0-31] MSCR96 - MSCR127
PortE[0-31] MSCR128 - MSCR159
PortF[0-31] MSCR160 - MSCR191
PortG[0-27] MSCR192 - MSCR219
See SIUL2_MSCR/IMCR (See following Input Muxing section section for details on IMCR) for description of MSCR/
IMCR fields.
• SSS : This field specifies the ALT mode of operation as per MSCR[Mux_mode]. Not all pins support all pin muxing slots.
Unimplemented pin muxing slots are reserved. The corresponding pin is configured in the following pin muxing slot as follows:
— 000: Alternative 0 (GPIO)
— 001: Alternative 1 (chip-specific)
— 010: Alternative 2 (chip-specific)
— 011: Alternative 3 (chip-specific)
— 100: Alternative 4 (chip-specific)
— 101: Alternative 5 (chip-specific)
— 110: Alternative 6 (chip-specific)
— 111: Alternative 7 (chip-specific)
NOTE
The analog functionalities are specified with '-' in this field.
• Function: This field specifies the functionality of the pad as per the corresponding ALT mode specified by SSS field.
• Module: The Module field contains the module which is governing the pad for the ALT mode.

---

*Page 31*

Signal Multiplexing
• Description: This field mentions a short description of pad functionality.
• Direction: This field specifies the direction (Input, Output or Input/Output) of the pad for the concerned functionality.
• Pad Type: This field mentions the pad type of the corresponding pad.
— GPIO-Standard:
◦ Switching up to 10MHz
◦ High drive-strengh not supported.
◦ Slew-rate control not supported.
— GPIO-Standard plus:
◦ Switching up to 25MHz
◦ Supports high drive-strengh.
◦ Slew-rate control not supported.
— GPIO-Medium:
◦ Switching up to 50MHz
◦ Supports high drive-strengh.
◦ Supports slew-rate control.
— GPIO-Fast:
◦ Switching up to 120MHz
◦ Supports high drive-strengh.
◦ Supports slew-rate control.
• The next columns specify the pin number in the supported packages for the device.
• I/O Power Domain: This field refers to the power domain of associated pad (VDD_HV_A / VDD_HV_B).
• Pad State during/after Reset: These fields represent the pad states during and post different device resets.
• MSCR: This field specifies the default MSCR value for corresponding pad. Refer SIUL2_MSCRn for description of
MSCR fields.
• The next two columns specify the reset value and the configurable bit fields of MSCR corresponding to pad.

#### 4.4.2 Input muxing table

As the same function can be multiplexed to several pads by configuring their respective IMCRs, there is priority input muxing. In
case of same input being driven from multiple pads, the one with highest priority (1 being the highest) will drive the input. Following
is a snippet of Input Muxing Table.
Destination Instance Destination Function CR Instance Input CR# Input SSS Source Instance Source Signal MCXE31x_172mqfp_DC
CAN0 CAN0_RX SIUL SIUL_IMCR512 0000_0000 - disable low
0000_0001 IO_PAD PTC2 50
0000_0010 IO_PAD PTA6 102
0000_0011 IO_PAD PTB0 95
0000_0100 IO_PAD PTA28 30
0000_0101 IO_PAD PTF21 -
Figure 10. Input muxing table snippet
The columns of the figure are briefly described below:
• Destination Instance: This field contains the instance name of the input path to where the signal will propagate from padring.
• Destination Function: This field mentions the function name of the input path.

---

*Page 32*

Signal Multiplexing
• Input SSS: This field specifies the IMCR[Mux_mode] value corresponding to the pad specified in source signal column.
• Source Instance: This field specifies the source pad type. A blank is mentioned for the default source when no pad is driving
the input path.
• Source Signal: This field mentions the pad name. A ‘disable low’/’disable high’ specifies the signal behavior when none of the
pads are driving the input path.
• The next columns specify the pin number in the supported packages for the device.

#### 4.4.3 MSCR/IMCR description/explanation

MSCR assignments
The Implemented SIUL2 Multiplexed Single Configuration Register details is provided in the I/O Signal Description Table attached
as excel.
Example:
If user wants to configure PTB0 as CAN0RX and PTB1 as CAN0TX, then the following configuration can be used in
SIUL2 registers:
//.CAN0.RX.(PTB0):
SIUL2.IMCR0.B.SSS.=.0b011;.//.Select.CAN0-RX
SIUL2.MSCR32.B.IBE.=.1;....//.Enable.the.input.buffer
//.CAN0.TX.(PTB1):
SIUL2.MSCR33.B.SSS.=.0b101;.//.Select.CAN0-TX
SIUL2.MSCR33.B.OBE.=.1;.//.Enable.the.output.buffer
MSCR bit fields correspond to pin/pad basis, these are independent of muxing implemented on that specific pin/pad. As an
example, pad PTA31 has SSS, SMC , DSE , PUS , PKE , INV , IBE and OBE are implemented with the reset value 0 and SRC is
implemented with reset value 1.
In the 'IO Signal Table' tab of the IOMUX file attached, the MSCR register bits shows ‘0’, ‘1’, and ‘-’ for state of all bits associated
with different ports. ‘0’ or ’1’ represents the reset state and ‘-’ represents the bit is not supported on the respective port.
NOTE
The GPIOs 38 and 39 are used for direct connections and FXOSC.

#### 4.4.4 Pinout diagrams

See IO Signal Description Input Multiplexing sheet(s) attached to the Reference Manual for pinout diagrams corresponding to
available packages.

#### 4.5 Pin States

The tables in the upcoming sections mention the state of pins of the device under various conditions/event like Functional Reset,
Destructive Reset, Power Up condition, during Selftest Phase etc. The details of the Reset events can be found in the Reset
overview Chapter. The Details of Selftest can be found in the STCU chapter.

#### 4.5.1 Pin numbers

The pin numbers which have been referenced to the tables are explicitly mentioned here

---

*Page 33*

Signal Multiplexing
• RESET_B: PTA5 = GPIO5. The pad state tables below assume RESET_B configured as Reset pin (See UTEST
DCF record 'dcf_client_reset_pad_dedicated' in the DCF clients file attached to this document for Reset pin control as
dedicated/muxed GPIO'.
• JTAG_TMS : PTA4 = GPIO4
• JTAG_TCK : PTC4 = GPIO68
• JTAG_TDI : PTC5 = GPIO69
• JTAG_TDO : PTA10 = GPIO10
• TRACE_ETM_CLKOUT : PTG6 = GPIO[198] (Slow pad trace), PTC2=GPIO[66] (Fast pad trace)
• TRACE_ETM_D0 : PTG7 = GPIO[199] (Slow pad trace), PTD7=GPIO[103] (Fast pad trace)
• TRACE_ETM_D1 : PTG15 = GPIO[207] (Slow pad trace), PTD12=GPIO[108] (Fast pad trace)
• TRACE_ETM_D2 : PTG16 = GPIO[208] (Slow pad trace), PTD11=GPIO[107] (Fast pad trace)
• TRACE_ETM_D3 : PTF31 = GPIO[191] (Slow pad trace), PTD10=GPIO[106] (Fast pad trace)
• TRACE_ETM_D4 : PTG17 = GPIO[209] (Slow pad trace)
• TRACE_ETM_D5 : PTF28 = GPIO[188] (Slow pad trace)
• TRACE_ETM_D[6:15] : PTG18:PTG27 = GPIO[210]:GPIO[219] (Slow pad trace)
• CLKOUT_STANDBY : PTA12 = GPIO12
• FCCU_ERR[0:1] : PTA2:PTA3 = GPIO[2]:GPIO[3]

#### 4.5.2 Power up

This table mentions the pin behavior on power up condition of the device.
Table 18. Power up
1
Pin function POWER UP until SW comes up
RESET_B LOW
JTAG_TMS , JTAG_TCK , JTAG_TDI , JTAG_TDO as JTAG
PULLED Values:
(TMS = HIGH, TCK = LOW, TDI =HIGH)
TDO: HIGH Z
ETM_TRACE HIGH Z
FCCU_ERR HIGH Z
CLKOUT_STANDBY HIGH Z
GPIOs HIGH Z
EXTAL HIGH Z till extal is connected
XTAL HIGH Z till xtal is connected
1. The IO pad states are undefined until Supply rises sufficiently to enable the POR circuits.

#### 4.5.3 Destructive Reset

This table mentions the Pin behavior when any Destructive Reset event is triggered.

---

*Page 34*

Signal Multiplexing
Table 19. Destructive Reset
Pins ON Destructive Reset until SW comes up
RESET_B
LOW
TMS,TCK,TDI,TDO as JTAG
PULLED Values:
(TMS = HIGH TCK = LOW, TDI =HIGH)
TDO: HIGH Z
ETM_TRACE (Only on 17trace Pins) HIGH Z
FCCU_ERR HIGH Z
CLKOUT_STANDBY
Clkout expose if CLKOUT_STANDBY is configured to
be Enabled
Else HIGH Z
GPIOs HIGH Z

#### 4.5.4 Functional Reset including Functional entry sequence

This table mentions the Pin behavior when any functional Reset event is triggered. Also, when the FCCU reaction is configured
as Functional Reset , then also the same behavior is achieved.
Table 20. Functional Reset including Functional entry sequence
Pins Functional Reset Entry Reset until SW comes up
RESET_B
LOW LOW
TMS,TCK,TDI,TDO as JTAG
TMS,TCK,TDI: PULLED Values if PULLED Values
configured as JTAG
(TMS = HIGH TCK = LOW, TDI =HIGH)
(TMS = HIGH TCK = LOW, TDI =HIGH)
TDO: HIGH Z
HIGH Z if configured as GPIO
TDO: HIGH Z
ETM_TRACE
Trace if Trace if
MDMAPCTL[DBGRSTSLOWPAD] or MDMAPCTL[DBGRSTSLOWPAD] or
MDMAPCTL[DBGRSTFASTPAD] is MDMAPCTL[DBGRSTFASTPAD] is
configured to be Enabled configured to be Enabled
Else HIGH Z Else HIGH Z
FCCU_ERR
ERR from FCCU if ERR from FCCU if
UTEST_MISC[FCCU_EOUT_DEDICAT UTEST_MISC[FCCU_EOUT_DEDICAT
ED] is configured to be Enabled ED] is configured to be Enabled.
Else HIGH Z Else HIGH Z
CLKOUT_STANDBY
Clkout expose if Clkout expose if
DCMRWP1[CLKOUT_STANDBY] is DCMRWP1[CLKOUT_STANDBY] is
configured to be Enabled configured to be Enabled
Else HIGH Z Else HIGH Z
Table continues on the next page...

---

*Page 35*

Signal Multiplexing
Table 20. Functional Reset including Functional entry sequence (continued)
Pins Functional Reset Entry Reset until SW comes up
GPIOs HIGH Z HIGH Z

#### 4.5.5 SELFTEST(MC_RGM.ERCTRL[ERASSERT] configured as 1)

Table 21. SELFTEST(MC_RGM.ERCTRL[ERASSERT] configured as 1)
Pins Selftest Reset until SW comes up
RESET_B
LOW if configured as Reset Pin LOW if configured as Reset Pin
High Z if configured as GPIO High Z if configured as GPIO
TMS,TCK,TDI,TDO as JTAG
PULLED Values if configured as JTAG PULLED Values
(TMS = HIGH, TCK = LOW, TDI = HIGH) (TMS = HIGH, TCK = LOW, TDI = HIGH)
TDO: HIGH Z TDO: HIGH Z
ETM_TRACE
Trace if Trace if
MDMAPCTL[DBGRSTSLOWPAD] or MDMAPCTL[DBGRSTSLOWPAD] or
MDMAPCTL[DBGRSTFASTPAD] is MDMAPCTL[DBGRSTFASTPAD] is
configured to be Enabled. configured to be Enabled.
Else HIGH Z Else HIGH Z
FCCU_ERR
FCCU Error State if FCCU Error State if
UTEST_MISC[FCCU_EOUT_DEDICAT UTEST_MISC[FCCU_EOUT_DEDICAT
ED] is Enabled and ED] is Enabled and
DCMRWD2[EOUT_STAT_DUR_STEST DCMRWD2[EOUT_STAT_DUR_STEST
] is Enabled ] is Enabled
Else HIGH Z Else HIGH Z
CLKOUT_STANDBY
Clkout expose if Clkout expose if
DCMRWP1[CLKOUT_STANDBY] is DCMRWP1[CLKOUT_STANDBY] is
configured to be Enabled configured to be Enabled
Else HIGH Z Else HIGH Z
GPIOs HIGH Z HIGH Z

#### 4.5.6 SELFTEST (RGM_ERCTRL[ERASSERT]=0 and RGM_FRBE[ST_DONE] =0)

Table 22. SELFTEST (RGM_ERCTRL[ERASSERT]=0 and RGM_FRBE[ST_DONE] =0)
Pins Selftest Reset after Selftest until SW comes up
RESET_B Same state as before selftest Same state as before selftest
TMS,TCK,TDI,TDO as JTAG Same state as before selftest
PULLED Values
(TMS = HIGH, TCK = LOW, TDI = HIGH)
TDO: No change
ETM_TRACE Same state as before selftest
Same state as before selftest
Table continues on the next page...

---

*Page 36*

Signal Multiplexing
Table 22. SELFTEST (RGM_ERCTRL[ERASSERT]=0 and RGM_FRBE[ST_DONE] =0) (continued)
Pins Selftest Reset after Selftest until SW comes up
FCCU_ERR
FCCU Error State if FCCU Error State if
UTEST_MISC[FCCU_EOUT_DEDICAT UTEST_MISC[FCCU_EOUT_DEDICAT
ED] is Enabled and ED] is Enabled and
DCMRWD2[EOUT_STAT_DUR_STEST DCMRWD2[EOUT_STAT_DUR_STEST
] is Enabled ] is Enabled
Else No change Else No change
CLKOUT_STANDBY Same state as before selftest
Same state as before selftest
GPIOs Same state as before selftest Same state as before selftest

#### 4.5.7 FCCU fault in RUN mode when the fault reaction is not Reset

NOTE
For any GPIO PAD If SMC=1 then the pad would retain it's state during the fault mode else if SMC=0 then the pad
would become high Z during the fault mode.
Table 23. FCCU fault in RUN mode, fault reaction not Reset
Pins ON FCCU Fault
RESET_B
If RESET_B is assigned as a dedicated PAD then no change in the state of the PAD else if RESET_B
is configured as GPIO then it would behave as per the SMC configuration.
• Case 1: If dcf_client_reset_pad_dedicated[reset pad dedicated] is configured as 1'b1, the pad
functions as per the reset state machine.
• Case 2: If dcf_client_reset_pad_dedicated[reset pad dedicated] is configured as 1'b0 and
SIUL2.MSCR5[SMC] is configured as 1'b1, the pad functions as per the configured protocol
status, the protocol being configured via SIUL2.MSCR5[SSS].
• Case 3: If dcf_client_reset_pad_dedicated[reset pad dedicated] is configured as 1'b0 and
SIUL2.MSCR5[SMC] is configured as 1'b0, the pin gets tristated (high-Z).
TMS, TCK, TDI, TDO
If the PAD is configured as a JTAG PAD then no change and the pad would continue performing the
as JTAG
operation else if it is a GPIO PAD then it would behave as per the SMC configuration.
• Case 1: If the pad is configured for JTAG mode and SMC bit of SIUL2's MSCR register of the
corresponding pin is configured as 1'b1, the pad functions as per the JTAGC state machine
or protocol.
• Case 2: If the pad is configured for non-JTAG mode and SMC bit of SIUL2's MSCR register of the
corresponding pin is configured as 1'b0, the pins get tristated (high-Z).
ETM_TRACE
If the PAD is configured as a dedicated PAD then no change and the pad would continue performing
the operation else if it is a GPIO PAD then it would behave as per the SMC configuration.
• Case 1: If the pad is configured for ETM_TRACE functionality and SMC bit of SIUL2's MSCR
register of the corresponding pin is configured as 1'b1, the pad functions as per the ETM trace
control logic.
• Case 2: If the pad is configured for non-ETM_TRACE mode and SMC bit of SIUL2's MSCR
register of the corresponding pin is configured as 1'b0, the pins get tristated (high-Z).
Table continues on the next page...

---

*Page 37*

Signal Multiplexing
Table 23. FCCU fault in RUN mode, fault reaction not Reset (continued)
Pins ON FCCU Fault
FCCU_ERR
If the PAD is configured as a dedicated PAD by programming the
UTEST_MISC[FCCU_EOUT_DEDICATED] as well as Eout indication is enabled then it would be
indicating error state else if it is a GPIO PAD then it would behave as per the SMC configuration.
• Case 1: If dcf_client_utest_misc[FCCU_EOUT_DEDICATED] is configured as 1'b1, the
FCCU_ERR pins indicate the error state.
• Case 2: If dcf_client_utest_misc[FCCU_EOUT_DEDICATED] is configured as 1'b0 and SMC bit
of the SIUL2's corresponding MSCR register is configured as 1'b1, the pin retains its state.
• Case 3: If dcf_client_utest_misc[FCCU_EOUT_DEDICATED] is configured as 1'b0 and
SIUL2.MSCR5[SMC] is configured as 1'b0, the pin gets tristated (high-Z).
CLKOUT_STANDBY
If the PAD is configured as a dedicated PAD then no change and the pad would continue performing
the operation else if it is a GPIO PAD then it would behave as per the SMC configuration.
• Case 1: If the pad is configured for CLKOUT_STANDBY functionality and SMC bit of SIUL2's
MSCR register of the corresponding pin is configured as 1'b1, the pin continues functioning as the
CLKOUT_STANDBY pin.
• Case 2: If the SMC bit of SIUL2's MSCR register of the corresponding pin is configured as 1'b0,
the pin gets tristated (high-Z).
GPIOs
If the PAD is configured as a dedicated PAD then no change and the pad would continue performing
the operation else if it is a GPIO PAD then it would behave as per the SMC configuration.
• Case 1: If the SMC bit of SIUL2's MSCR register of the corresponding pin is configured as 1'b1,
the pin continues its operation.
• Case 2: If the SMC bit of SIUL2's MSCR register of the corresponding pin is configured as 1'b0,
the pins get tristated (high-Z).

#### 4.5.8 STANDBY ( DCMRWF1[STANDBY_IO_CONFIG] must be configured as 1)

• Please refer to Padkeeping Section for steps to configure the various pad states during standby
Table 24. STANDBY
Pins Standby until SW comes up
RESET_B Alive / HighZ /LOW/HIGH (Depends on Software configuration
before entering STANDBY)
TMS,TCK,TDI,TDO as JTAG HighZ /LOW/HIGH (Depends on Software configuration before
entering STANDBY)
ETM_TRACE HighZ /LOW/HIGH (Depends on Software configuration before
entering STANDBY)
FCUU_ERR HighZ /LOW/HIGH (Depends on Software configuration before
entering STANDBY)
CLKOUT_STANDBY Alive / HighZ /LOW/HIGH (Depends on Software configuration
before entering STANDBY)
GPIOs
HighZ /LOW/HIGH (Depends on Software configuration before
entering STANDBY)

---

*Page 38*

Signal Multiplexing

#### 4.6 Glossary

DSE Drive strength enable
EXTAL External crystal input
GPIO General purpose input/output
IBE Input buffer enable
INV Invert enable
JTAG_TMS JTAG test mode select
JTAG_TCK JTAG test clock input
JTAG_TDI JTAG test data input
JTAG_TDO JTAG test data output
OBE Output buffer enable
PUS Pullup and pulldown select
PKE Pad keeping enable
SMC Safe mode control
SRC Slew rate control
SSS Solid-state storage
XTAL External crystal output

---

*Page 39*

