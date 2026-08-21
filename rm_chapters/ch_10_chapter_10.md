<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 10 -->

# Chapter 10

# Touch Sensing Pin Coupling (TSPC)

#### 10.1 Chip-specific TSPC information

#### 10.1.1 TSPC instances and configuration

This chip has one instance of TSPC module. The GPIO pads being used for TSPC should be configured with Source Signal Select
(SSS) = 0. TSPC module works in conjunction with SIUL2.
For the GPIOs and ADC pads supporting TSPC, see the IOMUX file attached to this document. There are two groups of pads
which can be configured via TSPC:
1. Group 1 consists of 32 ADC channels and 14 GPIOs
2. Group 2 consists of 32 ADC channels and 6 GPIOs
NOTE
Use preferably the same type of I/O pins for driving each electrode. Such I/O pins matching will improve resolution
of the capacitance sensing. For more information about types of I/O pins, see the IOMUX file attached to
this document.

#### 10.2 Overview

TSPC provides simultaneous transitioning of a group of pads into high impedance to support a more robust recognition of
touch sense.

#### 10.2.1 Block diagram

lPS bus
TSPC registers lPS bus
TSPC logic block
Output buffer enable from SIUL2
Gated output buffer
enable from TSPC
Figure 21. TSPC block diagram

#### 10.2.2 Features

TSPC provides a set of control registers for pairing up GPIO pads for the touch sensing pairing operation. These control registers
select the set of GPIO pads to pair with the set of ADC channels of an ADC instance. See the chip-specific TSPC configuration
for group of pads information.
TSPC supports the following features:
• Up to 512 GPIO pads allow grouping

---

*Page 233*

Touch Sensing Pin Coupling (TSPC)
• OBE of all the grouped pads transitions from high to low simultaneously if SIUL2 transitions a pad's OBE from high to low
• Programming of group registers defines grouping

#### 10.3 Functional description

TSPC contains these types of registers:
• Group enable: Each field of this register enables the respective group's OBE register—that is, if any OBE within a group
transitions from high to low, it enables that group of OBEs for simultaneous transition from high to low. For example, GRP1_EN
is the enable field for group 1 OBE registers. The number of fields in this register is equal to the number of groups.
• Group n OBE register n : The number of registers for a particular group n depends on the number of grouped OBEs in the
respective group. For example, if the number of OBEs in group 1 is 36, then two registers of 32 fields each exist. Each field
of this register represents an OBE pad. If you write 1 to the field, it indicates the OBE's participation in the grouping. If one of
the pad's OBEs transitions from high to low, all the participating OBEs transition from high to low. The remaining OBEs that
are not participating in the grouping are not affected.

#### 10.3.1 Submodules

10.3.1.1 Logic block description
This block takes the information from register fields to produce gated OBEs.
The simultaneous transition of grouped OBEs occurs when the GRP n _EN field in Group Enable (GRP_EN) for the respective
group becomes 1.
The logic diagram below shows that if the GRP_EN is high for a particular group, and one of the output buffer enable
(ipp_obe_in_b) from SIUL2 belonging to that group transitions from high to low, then all the other output buffer enables belonging to
that group also transitions from high to low. If the GRP_EN is set low, the transitions in OBE do not affect the other OBE's belonging
to that group.

---

*Page 234*

Touch Sensing Pin Coupling (TSPC)
0
GRP1_EN
ipp_obe_in_b
Y ipp_obe_out
(GRP_EN from Group Enable register)
1
OBE1
ipp_obe_in_b
SEL0
OBE1
(OBE from register bits)
GRP1_EN
0
(GRP_EN from Group Enable register)
ipp_obe_in_b
Y ipp_obe_out
1
ipp_obe_in_b OBE2
SEL0
OBE2
(OBE from register bits)
Gated OBEs from TSPC
0
GRP2_EN
ipp_obe_in_b
Y ipp_obe_out
(GRP_EN from Group Enable register)
1
OBE1
ipp_obe_in_b
SEL0
OBE1
(OBE from register bits)
GRP2_EN
0
(GRP_EN from Group Enable register)
ipp_obe_in_b
Y ipp_obe_out
1
ipp_obe_in_b OBE2
SEL0
OBE2
(OBE from register bits)
Figure 22. Logic block diagram

#### 10.3.2 Operation

This section describes the TSPC_SPEC module’s operations.
1. Reset all the registers.
2. Write 1 to the GRP n _OBE x [OBE n ] of the OBEs that participate in grouping.
3. Program the corresponding group enable field participating in grouping in Group Enable (GRP_EN) .
You must write 0 to the corresponding GRP_EN field when you configure the group n OBE fields. You must write 1 to
the OBE n field in Group OBE (GRP1_OBE1 - GRP2_OBE1) and then write 1 to the corresponding GRP_EN field in Group
Enable (GRP_EN) .

#### 10.3.3 Clocking

The TSPC module has two clocks.
Clock
ipg_clk
ipg_clk_s

#### 10.3.4 Interrupts

This module has no interrupts.

---

*Page 235*

Touch Sensing Pin Coupling (TSPC)

#### 10.4 External signals

The TSPC module has two external signals.
Signal IO Description
ipp_obe_out Output Gated output buffer enable from TSPC
ipp_obe_in_b Input Output buffer enable from SIUL and input for TSPC

#### 10.5 Initialization

This module does not require initialization.

#### 10.6 Application information

TSPC IP block provides the functionality of simultaneously transitioning two pads into high impedance, in order to experience a
more robust recognition of touch sense.

#### 10.7 TSPC register descriptions

#### 10.7.1 TSPC memory map

TSPC base address: 402C_4000h
Offset Register Access Reset value
Width
(In bits)
0h Group Enable (GRP_EN) 32 RW 0000_0000h
50h Group OBE (GRP1_OBE1) 32 RW 0000_0000h
54h Group OBE (GRP1_OBE2) 32 RW 0000_0000h
A0h Group OBE (GRP2_OBE1) 32 RW 0000_0000h
A4h Group OBE (GRP2_OBE2) 32 RW 0000_0000h

#### 10.7.2 Group Enable (GRP_EN)

Offset
Register Offset
GRP_EN 0h
Function
Allows enabling of the group, the pads of which participate in simultaneous transition.

---

*Page 236*

Touch Sensing Pin Coupling (TSPC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
GRP2_ GRP1_
EN EN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-2
—
1 Enable for GRP2_OBEn Register
GRP2_EN Enables or disables the GRP2_OBE n register that participates in grouping.
0b - Disable
1b - Enable
0 Enable for GRP1_OBEn Register
GRP1_EN Enables or disables the GRP1_OBE n register that participates in grouping.
0b - Disable
1b - Enable

#### 10.7.3 Group OBE (GRP1_OBE1 - GRP2_OBE1)

Offset
Register Offset
GRP1_OBE1 50h
GRP2_OBE1 A0h
Function
Allows grouping of OBEs in the respective group. Each field corresponds to an OBE pad.

---

*Page 237*

Touch Sensing Pin Coupling (TSPC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
OBE3 OBE3 OBE2 OBE2 OBE2 OBE2 OBE2 OBE2 OBE2 OBE2 OBE2 OBE2 OBE1 OBE1 OBE1 OBE1
1 0 9 8 7 6 5 4 3 2 1 0 9 8 7 6
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
OBE1 OBE1 OBE1 OBE1 OBE1 OBE1
OBE9 OBE8 OBE7 OBE6 OBE5 OBE4 OBE3 OBE2 OBE1 OBE0
5 4 3 2 1 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Output Buffer Enable
OBEn Indicates if a particular OBE participates in grouping.
0b - OBE pad does not transition if any OBE in group transitions from high to low. The OBE pad
value is unaltered at the output.
1b - OBE pad transitions from high to low if any OBE in group transitions from high to low.

#### 10.7.4 Group OBE (GRP1_OBE2)

Offset
Register Offset
GRP1_OBE2 54h
Function
Allows grouping of OBEs in the respective group. Each field corresponds to an OBE pad.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
OBE4 OBE4 OBE4 OBE4 OBE4 OBE4 OBE3 OBE3 OBE3 OBE3 OBE3 OBE3 OBE3 OBE3
5 4 3 2 1 0 9 8 7 6 5 4 3 2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 238*

Touch Sensing Pin Coupling (TSPC)
Fields
Field Function
Reserved
31-14
—
13-0 Output Buffer Enable
OBEn Indicates if a particular OBE participates in grouping.
0b - OBE pad does not transition if any OBE in group transitions from high to low. The OBE pad
value is unaltered at the output.
1b - OBE pad transitions from high to low if any OBE in group transitions from high to low.

#### 10.7.5 Group OBE (GRP2_OBE2)

Offset
Register Offset
GRP2_OBE2 A4h
Function
Allows grouping of OBEs in the respective group. Each field corresponds to an OBE pad.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
OBE37 OBE36 OBE35 OBE34 OBE33 OBE32
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-6
—
5-0 Output Buffer Enable
Table continues on the next page...

---

*Page 239*

Touch Sensing Pin Coupling (TSPC)
Table continued from the previous page...
Field Function
Indicates if a particular OBE participates in grouping.
OBEn
0b - OBE pad does not transition if any OBE in group transitions from high to low. The OBE pad
value is unaltered at the output.
1b - OBE pad transitions from high to low if any OBE in group transitions from high to low.

#### 10.8 Glossary

GPIO General purpose input and output
OBE Output buffer enable

---

*Page 240*

