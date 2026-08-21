<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 1 -->

# Chapter 1

# About This Manual

#### 1.1 Audience

This reference manual (RM) is intended for system software, hardware developers, and applications programmers who need to
develop products using this chip. It assumes that its users understand operating systems, microprocessor system design, and
basic principles of software and hardware.

#### 1.2 Organization

This manual has two main sets of chapters.
• Chapters in the first set contain information that applies to all components on the chip.
• Chapters in the second set are organized into functional groupings that detail particular areas of functionality.
— Examples of these groupings are clocking, timers, and communication interfaces.
— Each grouping includes chapters that provide a technical description of individual modules.

#### 1.2.1 Attachments

This manual includes key information in the files attached to it. For example, memory map and I/O details. Use the content in these
attachments in conjunction with this manual's content.
NOTE
Select the paperclip icon on the left side of the PDF window to see the list of attachments.

#### 1.3 Module descriptions

Each module chapter has two main parts:
• The first section, chip-specific [module name] information, provides details such as the number of module instances on the
chip and connections between that module and the other ones. Read this section first because its content is crucial for
understanding the information in the other sections of the chapter.
• The subsequent sections provide general information about the module, including its signals, registers, and functional
description.
The following figure shows you an example of this demarcation.

---

*Page 8*

About This Manual
Chapter 49
Enhanced Serial Communication Interface (eSCI)
49.1 Chip-specific eSCI information
This chip has six instances of the eSCI module. Some feature details vary between the
instances.
The following table summarizes the feature differences. The table does not list feature
details that the instances share.

#### Chip-specific information

Table 49-1. eSCI instance feature differences

#### Instance DMA support that should be read first

eSCI_A and eSCI_B Yes
eSCI_C, eSCI_D, eSCI_E, and eSCI_F No: descriptions of eSCI DMA functionality do not apply to
these instances
NOTE
For eSCI_D, the single wire feature does not apply for TX/RX
via PCSA3 because this pad works only as an output.
49.2 Introduction
The eSCI block is an enhanced SCI block with a LIN master interface layer and DMA
support. The LIN master layer complies with the specifications LIN 1.3, LIN 2.0, LIN
2.1, and SAE J2602/1.

#### Beginning of general

#### 49.2.1 Bibliography module information

# • LIN Specification Package Revision 1.3; December 12, 2002 EXAMPLE

• LIN Specification Package Revision 2.0; September 23, 2003
Sample Reference Manual
Figure 1. Example of chapter chip-specific information and general module information

#### 1.3.1 Chip-specific information that clarifies content in the same chapter

The following figure shows an example of chip-specific information that clarifies general module information presented later in the
chapter. In this case, the chip-specific register reset values supercede the reset values that appear in the register diagram.

---

*Page 9*

About This Manual
Introduction Introduction
SIUL2 Module SIUL2 Module
IP Modules IP Modules
Pad Control and Pin Muxing Pad Control and Pin Muxing
System Integration Unit Lite2 (SIUL2) System Integration Unit Lite2 (SIUL2)
Chapter 9 Chapter 9
System Integration Unit Lite2 (SIUL2) System Integration Unit Lite2 (SIUL2) MSCRs/ MSCRs/
IMCRs IMCRs
GPIO GPIO
9.1 Chip-specific SIUL2 information 9.1 Chip-specific SIUL2 information IO IO PADS PADS
MUX MUX
9.1.1 Feature configurations 9.1.1 Feature configurations
Data Registers Data Registers
In this device, the SIUL2_0 module instance does not support the following features described in the generic description: In this device, the SIUL2_0 module instance does not support the following features described in the generic description:
IPS IPS
• Interrupts • Interrupts Master Master
• DMA channels • DMA channels
9.1.2 Notes for IMCR 9.1.2 Notes for IMCR Interrupt/DMA Request Interrupt/DMA Request
Out of reset, PA_00, PA_04, and PA_05 pads have JTAG input functionality selected by default. It should be disabled in the Out of reset, PA_00, PA_04, and PA_05 pads have JTAG input functionality selected by default. It should be disabled in the
corresponding IMCR registers (IMCR61, IMCR60, and IMCR50 respectively) in order to use other functionality such as GPIO. corresponding IMCR registers (IMCR61, IMCR60, and IMCR50 respectively) in order to use other functionality such as GPIO.
Interrupt Interrupt
Controller Controller
Interrupt Interrupt
9.2 Introduction 9.2 Introduction - Configuration - Configuration
- Glitch Filter - Glitch Filter
IPS BUS IPS BUS DMA DMA
9.2.1 Overview 9.2.1 Overview
The System Integration Unit Lite2 provides control over all the electrical pin controls and ports with 16 bits of bidirectional, general- The System Integration Unit Lite2 provides control over all the electrical pin controls and ports with 16 bits of bidirectional, general-
purpose input and output signals. One of the most important functions of the SIUL2 is to enable the user to select the functions purpose input and output signals. One of the most important functions of the SIUL2 is to enable the user to select the functions
and electrical characteristics that appear on external device pins. It also controls the multiplexing of internal signals from one and electrical characteristics that appear on external device pins. It also controls the multiplexing of internal signals from one
module to another and controls chip I/O. It supports as many as 32 external interrupts with trigger event configuration. The following module to another and controls chip I/O. It supports as many as 32 external interrupts with trigger event configuration. The following
figure is the block diagram of SIUL2 and its interfaces to other system components. figure is the block diagram of SIUL2 and its interfaces to other system components.
Figure 23. System Integration Unit Lite2 block diagram Figure 23. System Integration Unit Lite2 block diagram
This module provides dedicated pad control to general-purpose pads that can be configured as either inputs or outputs. The This module provides dedicated pad control to general-purpose pads that can be configured as either inputs or outputs. The
SIUL2 module provides registers that enable user software to read values from GPIO pads configured as inputs, and write values SIUL2 module provides registers that enable user software to read values from GPIO pads configured as inputs, and write values
to GPIO pads configured as outputs: to GPIO pads configured as outputs:
• When configured as output, you can write to an internal register to control the state driven on the associated output pad. • When configured as output, you can write to an internal register to control the state driven on the associated output pad.
• When configured as input, you can detect the state of the associated pad by reading the value from an internal register. • When configured as input, you can detect the state of the associated pad by reading the value from an internal register.

# EXAMPLE • When configured as input and output, the pad value can be read back, which can be used as a method of checking if the • When configured as input and output, the pad value can be read back, which can be used as a method of checking if the

written value appeared on the pad. written value appeared on the pad.
To assist software development, GPIO data registers can be accessed using various mechanisms. These differing mechanisms To assist software development, GPIO data registers can be accessed using various mechanisms. These differing mechanisms
allow support for port access or for bit manipulation without the need to use read-modify-write operations: allow support for port access or for bit manipulation without the need to use read-modify-write operations:
• Access to two 16-bit ports in one access • Access to two 16-bit ports in one access
• Read/write access to a single bit • Read/write access to a single bit
• A 16-bit port write with a bit mask, using single 32-bit access. • A 16-bit port write with a bit mask, using single 32-bit access.
Sample Reference Manual Sample Reference Manual Sample Reference Manual Sample Reference Manual
NXP Semiconductors NXP Semiconductors NXP Semiconductors NXP Semiconductors
Figure 2. Example of chip-specific information that clarifies content in the same chapter

#### 1.3.2 Chip-specific information that refers to a different chapter

Related chip-specific information may be provided in different chapters of the manual. The following figure shows an example of
two such connected pieces of information. In this case, read both before you proceed.

---

*Page 10*

About This Manual
Chapter 10 Chapter 9
Crossbar Integrity Checker (XBIC) Crossbar Switch (XBAR)
10.1 Chip-specific XBIC information 9.1 Chip-specific XBAR information
This chip has one instance of the XBIC module. This chip has one instance of the XBAR module.
10.1.1 XBIC master and slave assignments 9.1.1 XBAR master and slave assignments
The XBIC identifies each XBAR master and slave in terms of the master or slave's The following table lists the XBAR physical port numbers and logical IDs for all master
physical port number. See the "Physical master port" assignments in Table 9-1 and the ports on this SoC.
"Slave port" assignments in Table 9-2. • Each port number matches the default priority assigned to the corresponding physical
master port. This default priority equals the reset value of the priority field for each
master port in the PRS n registers.
• A priority value of 0 is the highest priority. There is no "disabled" value for the
10.1.2 Unimplemented MCR and ESR fields
priority.
On this chip, the MCR[SE5] and ESR[DPSE5] fields are not implemented. In XBIC • A Nexus_3 module and core data bus share the same physical master port for each
Module Control Register (XBIC_MCR) and XBIC Error Status Register (XBIC_ESR), core.
these fields are reserved.
The logical master ID corresponds to the logical address provided by the master module
and is unique for each module. The logical master IDs are used by the bus masters
connected to the XBAR. The Nexus master is identified by setting the MSB in the 4-bit
10.2 Overview field that supplies the master ID number.
The Crossbar Integrity Checker (XBIC) verifies the integrity of the crossbar transfers. Table 9-1. XBAR master ports and logical master IDs
For forward signals (master to slave), it is done by verifying the integrity of the attribute Module Physical master port Logical master ID Comment
information using an 8-bit Error Detection Code (EDC). The EDC detects any single- or Core0 instruction 0 0
double-bit errors in the attribute information and signals the Fault Collection and Control Core0 data 1 0
Unit (FCCU) when an error is detected. For feedback signals (slave to master), it is done Nexus_3_0 8 Nexus_3_0 arbitrates with Core0 data for XBAR port 1
by comparing the consistency of the signals during the AHB dataphase.There are three Core1 instruction 2 1
signals from slave to master, hready, hresp0, and hresp2. If any of the master signals is Core1 data 3 1
different from the slave signals during dataphase, the error will be reported in the Error Nexus_3_1 9 Nexus_3_1 arbitrates with Core1 data for XBAR port 3
Status Register.

# EXAMPLE Table continues on the next page...

Sample Reference Manual Sample Reference Manual
Figure 3. Example of chip-specific information that refers to a different chapter

#### 1.4 Register descriptions

Module chapters present register information in the following:
• Memory maps, which contain:
— An offset from the module's base address
— The mnemonic and name of each register
— The width of each register (in bits)
— The reset value of each register
• Register figures
• Field-description tables
• Associated text
The following figure shows register figure conventions used throughout the manual.

---

*Page 11*

About This Manual
R R Mnemonic R R 0 R 1
Mnemonic
W W W Mnemonic W Mnemonic W Mnemonic
Read/write Read-only Write-only Write-only Write-only
reads zero reads one
R R Mnemonic R Mnemonic R Mnemonic R Mnemonic
W Mnemonic W 0 W 1 W W w1c
Write-only Read-only Read-only Read-only Write one to clear
reads undefined writes zero writes one writes undefined
R R R R 1 R 0
Reserved
W W 1 W 0 W W
Reserved, Write-only one Write-only zero Read-only one Read-only zero
unimplemented
Figure 4. Register figure conventions
NOTE
Reset values of reserved locations documented in this manual are subject to change and must not be used for
diagnostic purposes.

#### 1.5 Conventions

#### 1.5.1 Notes and cautions

Specific information is provided as part of notes and cautions throughout this manual.
NOTE
Emphasizes information that deserves extra attention.
CAUTION
Informs you of situations that could lead to highly undesirable outcomes—such as damage to the chip or
irreversible malfunction.

#### 1.5.2 Numbering systems

The following suffixes identify different numbering systems:
Table 1. Numbering systems
This suffix Identifies a
b Binary number. For example, the binary equivalent of the number 5 is mentioned as 101b. In some
cases, 0b is prefixed to binary numbers.
d Decimal number. Decimal numbers are followed by this suffix only when there is a possibility of
confusion. In general, decimal numbers are used without a suffix.
h Hexadecimal number. For example, the hexadecimal equivalent of the number 60 is mentioned as
3Ch. In some cases, 0x is prefixed to hexadecimal numbers.

#### 1.5.3 Typographic notation

The following typographic notations are used throughout this document:

---

*Page 12*

About This Manual
Table 2. Typographic notation
Example Description
x and other italicized text The italized, lowercase x is used as a placeholder for replaceable numbers. In general,
italicized text is used for titles of publications and for emphasis. Additionally, italics could
be used for metasymbols in syntax descriptions. Plain lowercase letters are used as
placeholders for single letters and numbers.
code font
Fixed-width font (such as Courier) used for code. It is used for a letter, word, or phrase that you
want the user to type. For example, "Type Read and press Enter."
This type of font is also used for instruction mnemonics, directives, symbols, subcommands,
parameters, operators, computer-language elements, code listings, commands that appear in
running text, and for sample code. Instruction mnemonics and directives in text and tables are
mentioned in all caps; for example, BSR.
SR[SCM] A mnemonic in square brackets represents the name of a register field. This example refers
to the Scaling Mode (SCM) field in the Status Register (SR).
REVNO[6:4], XAD[7:0] Numbers in brackets that are separated by a colon represent either:
• A subset of a register's named field
For example, REVNO[6:4] refers to bits 6-4 that are part of the COREREV field occupying
bits 6-0 of the REVNO register.
• A continuous range of individual signals of a bus
For example, XAD[7:0] refers to signals 7-0 of the XAD bus.
MOD.REG
A period separates the elements of a hierarchy: subsystem.module.register. For example:
• SWT.TO means that the TO register is located in the SWT module.
• SMU.XRDC.CR means that the CR register is located in the XRDC module within the
SMU subsystem.

#### 1.5.4 Special terms

The following terms have special meanings.
Table 3. Special terms
Term Meaning
Asserted Refers to the state of a signal as follows:
• An active-high signal is asserted when high (1).
• An active-low signal is asserted when low (0).
Deasserted Refers to the state of a signal as follows:
• An active-high signal is deasserted when low (0).
• An active-low signal is deasserted when high (1).
In some cases, deasserted signals are described as negated .
Reserved Refers to a memory space, register, field, or programming setting. Writes to a reserved location can
result in unpredictable functionality or behavior. You must:
Table continues on the next page...

---

*Page 13*

About This Manual
Table 3. Special terms (continued)
Term Meaning
• Not modify the default value of a reserved programming setting, such as the reset value of a
reserved register field.
• Consider undefined locations in memory to be reserved.
• Not use the reset values of reserved locations documented in this manual for diagnostic
purposes. They subject to change.
Write 1 to clear (w1c) Refers to the access type of a register field that is used to clear the field by writing the value 1 to it.
Undefined (u) Refers to undefined reset values

#### 1.6 Editorial changes

Each new release of this document includes editorial improvements such as:
• Spelling
• Grammar
• Punctuation
• Voice
• Tense
• Capitalization
• Formatting
• Punctuation
• Presentation
• Navigation

#### 1.7 Glossary

The "Glossary" section provides a list of selected terms and definitions used in a chapter. The glossary, if present, is placed at the
end of the chapter.
A glossary contains:
• NXP-specific terms, symbols, and notations, with definitions.
• Terms, symbols, and notations that can have different meanings in different contexts. The glossary defines the way they
are used in the chapter—especially if the meaning is different from the meaning in standard references.
A glossary does not contain:
• Field mnemonics
• Register mnemonics
• Module mnemonics
• Signal names
• Industry-standard terms

---

*Page 14*

