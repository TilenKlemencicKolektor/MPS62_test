<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 77 -->

# Chapter 77

# JTAG Data Communication (JDC)

#### 77.1 Overview

JDC module provides the capability to move register data between the IPS and JTAG domains. This facilitates communication
between internal resources that access memory-mapped register space and an external tool that accesses the JTAG port.
The JDC module consists of:
• IPS-accessible registers
• JTAG-accessible registers
• Associated logic to coordinate movement of data from one register domain to another
The JDC implements the following IPS data registers, occupying separate memory space:
• 1 32-bit memory mapped register that can be read or written via IPS ( JTAG Output Data Register (JOUT_IPS) ), and whose
contents are ported out for capture into a JTAG register (JOUT) to be read via the JTAG port.
• 1 32-bit memory mapped register that can only be read via IPS ( JTAG Input Data Register (JIN_IPS) ), and whose contents
are loaded from a JTAG register (JIN).
JDC indicates when:
• New data has been shifted in via the JTAG port and is ready to be read from the JTAG Input Data Register (JIN_IPS) register
• New data has been written to the JTAG Output Data Register (JOUT_IPS) register and is ready to be read via the JTAG port
A Module Status Register (MSR) captures the state of these flags and also provides the flags to a JTAG register (JOUT) for
tool visibility.

#### 77.1.1 Block diagram

There is a single bus interface to port register data out to the JTAGC, and a single bus interface to port data in from the JTAGC.
1 8
The following figure is the JDC module block diagram.
TDO
IPS Domain Logic
System Reset 1 x 32-bit
1 x JIN_IPS
JIN
System Clock
1 x 32-bit
1 x 32-bit JOUT
1 x JOUT_IPS JTAG (1 x 32+2)-bit
Peripheral
Register
2-bit JTAG
Bus
MSR Register
Interrupts
TDI
TCK
MCR
TMS
JTAGC
TCK Domain Logic JCOMP
Figure 451. JDC block diagram

---

*Page 3304*

JTAG Data Communication (JDC)

#### 77.2 Functional description

The JDC module provides the ability to shift in data via the JTAG port and capture that data in memory-mapped register that can
be accessed via IPS. It also provides the ability to capture data written to memory-mapped register into a JTAG shift register for
output via the JTAG port.

#### 77.2.1 JDC functionality

An overview of the module functionality is described here:
• Software write to the JOUT_IPS register:
1. The JDC sets the JOUT_RDY flag bit, indicating new data is available to be read from the JOUT register via the
JTAG port.
2. The MSR reflects the state of the JOUT_RDY bit.
3. The JDC also captures the state of the JOUT_RDY bit in the JOUT register.
4. A JTAG read of the JOUT register via execution of the JOUT_READ instruction with a JOUT_RDY bit whose value
is logic 1 indicates that the register contains new data.
5. The JDC clears the JOUT_RDY flag bit upon exit of the Capture-DR JTAG state during execution of the
JOUT_READ instruction.
6. Clearing the JOUT_RDY bit indicates to software that a new data value can be written to the JOUT_IPS register.
• JTAG write to the JIN register via execution of the JIN_WRITE JTAG instruction:
1. The JDC updates the contents of the JIN_IPS register upon exit of the Update-DR state.
2. An update of the JIN_IPS register sets the JIN_RDY flag bit, indicating new data is available to be read via IPS.
3. The MSR register reflects the state of the JIN_RDY bit.
4. The JDC also captures the state of the JIN_RDY bit in the JOUT register.
5. The JDC clears the JIN_RDY flag bit upon software read of the JIN_IPS register.
6. A JTAG read of the JOUT register with a JIN_RDY value of logic 0 indicates that new data can be written to the JIN
register.

#### 77.2.2 JTAG register access

See the JTAGC documentation for information on how to access the JTAG registers.
77.2.2.1 JDC block instructions
The JDC block implements the instructions listed in Table 599 . This section gives an overview of each instruction. All undefined
opcodes are reserved.
Table 599. JTAG instructions
Instruction Code[4:0] Instruction summary
Reserved 00001 Factory debug reserved.
JOUT_READ 00010 Selects JOUT data register. The JDC captures data from JOUT_IPS into
JOUT data register upon entry to Capture-DR state when JOUT_READ
is active.
JIN_WRITE 01110 Selects JIN data register. The JDC captures data from JIN into JIN_IPS
upon exit of Update-DR state when JIN_WRITE is active.
Table continues on the next page...

---

*Page 3305*

JTAG Data Communication (JDC)
Table 599. JTAG instructions (continued)
Instruction Code[4:0] Instruction summary
1
BYPASS 11111 Selects bypass register for data operations.
Reserved All other opcodes Decoded to select bypass register.
1. This is an IEEE 1149.1-2001 defined instruction. See the IEEE 1149.1-2001 standard for more details.

#### 77.2.3 Clocking

JDC module uses TCK and inverted TCK as clock source.

#### 77.2.4 Interrupts

This module has no interrupts.

#### 77.3 External signals

JDC signals are listed in the following table.
Table 600. Signal properties
Name Function I/O Reset
TCK Test clock input I —
TMS Test mode select I 0
TDI Test data in I 0
JCOMP JTAG compliance pin I 0
TDO Test data out O HiZ

#### 77.4 Initialization

This module does not require initialization.

#### 77.5 Secure challenge/response connection procedure

A summary of how the JDC can be used to interact with a security module to perform challenge/response password authorization
is as follows:
1. After exit of reset, all registers are at their reset values. JIN_RDY bit value of 0 indicates that the tool may write data to the
JIN register. JOUT_RDY bit value of 0 indicates that software may write data to the JOUT_IPS register.
2. The MCR register is programmed to enable JOUT and JIN interrupts, or not.
3. Once the tool is ready to start the authorization process, it writes a value to the JIN register via execution of the JTAGC
JIN_WRITE instruction.
4. Software monitors the state of the JIN_RDY bit or enables the JIN interrupt and uses the interrupt assertion as a trigger to
start the authorization process.
5. Once the authorization process is started, software writes the first JOUT value to the JOUT_IPS register. This sets the
JOUT_RDY bit value to 1.

---

*Page 3306*

JTAG Data Communication (JDC)
6. Following the initial tool write to the JIN register, the tool polls the JOUT register to determine when software has provided
a challenge word. Upon reading the JOUT register with JOUT_RDY bit set, the tool records the data value to be used
to generate the appropriate response. The read of the JOUT register clears the JOUT_RDY bit. The clearing of the
JOUT_RDY bit also sets the JOUT_INT bit and asserts the JOUT interrupt if the MCR[JOUT_IEN] is set.
7. With the JOUT_RDY bit cleared, software may now provide the next challenge word.
8. Once the tool has generated a response word, it uses the value of the JIN_RDY bit from the JOUT register read to determine
when it can perform a write to the JIN register to provide the response.
9. The security module can provide additional challenge words and read back the corresponding response words as needed,
repeating the process.
Simply providing a password without using the challenge/response protocol can be done in a similar way. The tool provides 32-bits
of the password at a time with each write to the JIN register, and monitors the JIN_RDY bit value of the JOUT register to determine
when the next JIN register value can be written. There is no need for software to write the JOUT_IPS register in this case.
For details related to the lifecycle stage at which this process is performed, see the "Hardware Security Engine" chapter.

#### 77.6 JDC register descriptions

The following sections describe the implemented 32-bit registers. Only 32-bit accesses are valid. The effects of access that are
not 32 bits are not defined.

#### 77.6.1 JDC memory map

JDC base address: 4039_4000h
Offset Register Access Reset value
Width
(In bits)
0h Module Configuration Register (MCR) 32 RW 0000_0000h
4h Module Status Register (MSR) 32 RW 0000_0000h
8h JTAG Output Data Register (JOUT_IPS) 32 RW 0000_0000h
Ch JTAG Input Data Register (JIN_IPS) 32 R 0000_0000h

#### 77.6.2 Module Configuration Register (MCR)

Offset
Register Offset
MCR 0h
Function
The MCR enables the interrupt outputs of the JDC. This register is reset by system destructive reset.

---

*Page 3307*

JTAG Data Communication (JDC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R Reserved
JIN_
IEN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R Reserved
JOUT_
IEN
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-17
—
JIN Interrupt Enable
16
0b - Setting MSR[JIN_INT] bit does not assert the JIN interrupt
JIN_IEN
1b - Setting MSR[JIN_INT] bit asserts the JIN interrupt
Reserved
15-1
—
JOUT Interrupt Enable
0
0b - Setting MSR[JOUT_INT] bit does not assert the JOUT interrupt
JOUT_IEN
1b - Setting MSR[JOUT_INT] bit asserts the JOUT interrupt

#### 77.6.3 Module Status Register (MSR)

Offset
Register Offset
MSR 4h
Function
The MSR holds the JTAG register status and interrupt bits. This register is reset by system destructive reset.

---

*Page 3308*

JTAG Data Communication (JDC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
JIN_ Reserv JIN_
R Reserved
RDY ed INT
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
JOUT_ Reserv JOUT_
R Reserved
RDY ed INT
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-19
—
JIN Ready (read only)
18
0b - Cleared upon software read of JIN_IPS contents via IPS
JIN_RDY
1b - Set when new data is written to the JIN_IPS register
Reserved
17
—
JIN Interrupt
16
0b - Cleared by writing logic 1
JIN_INT
1b - Set when new data is written to the JIN_IPS register
Reserved
15-3
—
JOUT Ready (read only)
2
0b - Cleared upon tool read of JOUT register via JTAG port
JOUT_RDY
1b - Set when new data is written to the JOUT_IPS register
Reserved
1
—
JOUT Interrupt
0
0b - Cleared by writing logic 1
JOUT_INT
1b - Set when JOUT_RDY bit is cleared by tool reading JOUT register

---

*Page 3309*

JTAG Data Communication (JDC)

#### 77.6.4 JTAG Output Data Register (JOUT_IPS)

Offset
Register Offset
JOUT_IPS 8h
Function
The JOUT_IPS register holds data written via IPS. The JDC captures the JOUT_IPS contents into the JOUT register to be read
via the JTAG port. This register is reset by system destructive reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
Data
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
Data
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
JOUT_IPS Data
31-0
Data

#### 77.6.5 JTAG Input Data Register (JIN_IPS)

Offset
Register Offset
JIN_IPS Ch
Function
The JIN_IPS register holds data written to the JTAG input data register (JIN) via the JTAG port, where the data can be read via
IPS. Any IPS write to the JIN_IPS register returns a transfer error. This register is reset by system destructive reset.

---

*Page 3310*

JTAG Data Communication (JDC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R Data
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R Data
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
JIN_IPS data
31-0
Data

#### 77.7 Non-memory-mapped register definition

The JDC also implements two JTAG accessible registers that are not memory-mapped. One JTAG register shifts in data to be
placed in the JIN_IPS register. The other JTAG register captures data from the JOUT_IPS register plus ready status from the MSR,
to be shifted out via the JTAG port.

#### 77.7.1 JTAG output data register (JOUT)

The JOUT register captures data from the JOUT_IPS register upon execution of the JOUT_READ JTAG instruction. It also holds
the JIN_RDY and JOUT_RDY status bits. This register is reset by system destructive reset and JTAG reset. The reset value of
the JOUT register is 0. The following figure shows the format of the JOUT register.
Bits 33 2 1 0
R
JIN_ JOUT_
JOUT_IPS Data
RDY RDY
W
0
Reset 0 0 0
Figure 452. JOUT
The JOUT register is described in the following table.
Table 601. JOUT JTAG register field descriptions
Field Function
JOUT_IPS Data JOUT_IPS Data
Data value from JOUT_IPS register
Table continues on the next page...

---

*Page 3311*

JTAG Data Communication (JDC)
Table 601. JOUT JTAG register field descriptions (continued)
Field Function
JIN_RDY JIN_RDY
State of JIN_RDY bit from MSR
JOUT_RDY JOUT_RDY
State of JOUT_RDY bit from MSR

#### 77.7.2 JTAG input data register (JIN)

The external tool writes data to the JIN register via JTAG during execution of the JIN_WRITE JTAG instruction. The JDC later
captures JIN data in the JIN_IPS register to be read via IPS. This register is reset by system destructive reset and JTAG reset.
The reset value of the JIN register is 0. The following figure shows the format of the JIN register.
Bits 255
0
R
JIN Data
W
Reset
Figure 453. JIN
The JIN register is described in the following table.
Table 602. JIN JTAG register field descriptions
Field Description
JIN Data Contains data to be captured in JIN_IPS register upon exit of Update-DR state when executing WRITE_JIN
JTAG instruction.

#### 77.8 Glossary

IPS Internal peripheral system
XBAR Crossbar bus interface

---

*Page 3312*

