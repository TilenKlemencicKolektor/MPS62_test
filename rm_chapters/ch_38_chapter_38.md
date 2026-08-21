<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 38 -->

# Chapter 38

# Device Configuration Module (DCM)

#### 38.1 Overview

DCM controls:
• LC
• DCF client registers
• Debug authorization (Export Control mode)
The module also establishes a RoT for the chip by parsing the master root key and other security records.

#### 38.1.1 Block diagram

This figure is a block representation of DCM.
Platform Flash Controller (PFC)
uTest sector
PFC flash memory Block 0
interface
DCM
Block 1
Lifecycle Flash memory
Lifecycle
scanner
Flash
Memory
DCF client
DCF client
bus controller
Block 2
Block 3
SWD/JTAG
DCM debug DCM registers
interface
Block 4
Block 5
Figure 139. DCM block diagram

#### 38.1.2 Features

• Scans flash memory and configures the system for following information:
— LC detection
— DCF records configuration using flash memory
• Allows debug features for flash memory content and the chip
• Provides debug enable control for HSE and application

---

*Page 954*

Device Configuration Module (DCM)
• Provides a valid boot address detection
• Enables the DCF client to be writable via the IPS bus
• Parametrizes control to mask a set of DCF client chip select
• Supports temporary advancement of the LC by programming DCMLCC[DCMFLC]
• Manages debug features for flash memory content and the chip like debug enable control and debug authorization.

#### 38.2 Functional description

DCM provides information about the current state and configuration of the system that you could use to:
• Configure the application software.
• Debug the system.

#### 38.2.1 Modes of operation

DCM operates identically in all system modes of operation.

#### 38.2.2 DCF mechanism

The DCF mechanism handles chip parameter settings via the OTP flash memory.
You can store a series of DCF records in UTEST flash memory, and each record is 64 bits in length. The chip processes these
records during the system reset sequence before the CPU leaves reset.

#### 38.2.3 DCF error recording

DCF errors are recorded in DCM for both types of clients—spread spectrum safe and normal. See DCF client error mechanism
for details on spread spectrum safe clients.

#### 38.2.4 DCF client error mechanism

The DCM consists of DCMMISC[DCMCERS] for detecting faulty DCF records. The DCMSRRn registers capture the details up
to 16 faulty records. The DCMCERS bit can be cleared by writing 1 to it.
While scanning the flash, in case if a faulty record is encountered, the DCMMISC[DCMCERS] gets set indicating that there is
atleast one faulty record. In such a case, the user can identify the details about the faulty record in the DCMSRRn registers and
should update the flash memory with a new record, provided the record is not write-once.
For example, in the image below, if the faulty DCF record is DCF2, then the correct DCF record must also be DCF2. Even in case
if the faulty record is updated, the DCM stores the faulty record via the DCMMISC[DCMCERS] and DCMSRRn registers.
This figure shows that a faulty DCF record is loaded between the original and final DCF record sets.

---

*Page 955*

Device Configuration Module (DCM)
Utest flash memory
DCF1
Original
DCF2
DCF
DCM
DCM error flags
and reports IPS interface
Faulty
DCF2
DCF
Final Pulse
DCF2 DCF1 DCF2
DCF wrapper
Figure 140. DCF error handling

#### 38.2.5 DCM error detection mechanism

This figure shows the flow diagram of errors logged into DCM from DCF clients and Flash memory scanner. It also shows that
DCM provides software control for lifecycle and export control mode.

---

*Page 956*

Device Configuration Module (DCM)
Flash memory
DCF bus
Flash memory
DCF scanner
DCF bus Mux
DCF bus
Lifecycle &
Start record
ExportControl DCF client
control
Utest
DCM regs
TimeOut/ECC/
FlashIntfViol
Software control
End record
DCF client
error / Error logging
Safe DCF
client error
Figure 141. DCM error detection mechanism

#### 38.2.6 LC

DCM determines the LC of the chip by reading the LC slots from the Utest flash memory. This read operation is performed during
the reset phase, with normal timings. The operating monitors and an ECC check protect the operation. Additionally, a set of sanity
checks executed over the LC read data guarantee the integrity of the final LC value.
At the end of the reset phase, the LC contains one of the following values:
• OEM production (OEM_PROD)
• In field (IN_FIELD)
• Pre-FA
• FA
The DCM LC progresses in the direction shown in this figure:
MCU Customer OEM Pre-failure Failure
In field
production delivery production analysis analysis
Figure 142. LC sequence
The LC is written into six slots, 128 bits each, and at fixed locations in the Utest flash memory block . Each LC slot is read in a single
atomic operation and is organized in two types of fields:
• Valid (lower 64b of LC slot)
• Invalid (higher 64b of LC slot)

---

*Page 957*

Device Configuration Module (DCM)
Depending on the possible combinations of data programmed into these fields, each LC slot indicates one of the four possible
statuses as shown in Table 195 . To know more about LC slots, see Table 196 .
Table 195. LC slot status
LC slots LC slot value
Valid field (64 bits) Invalid field (64 bits)
Erased Erased Erased
Marked Erased Active
Marked Marked Inactive
Other values Illegal
In this case, "Marked" refers to a value that is configured based on the bit pattern 55AA_50AF_55AA_50AFh, and "Erased" is
detected using the bit pattern FFFF_FFFF_FFFF_FFFFh.
Table 196. LC slots
LC slot 4 Resulting LC
LC slot 2 LC slot 3 LC slot 5
Pre-FA
OEM_PROD IN_FIELD FA
Active Erased Erased Erased OEM_PROD
Inactive Active Erased Erased IN_FIELD
Inactive Inactive Active Erased Pre-FA
Inactive Inactive Inactive Active FA
NOTE
When triggering DCM for rescanning, the software must ensure that the flash memory program and erase fields are
not set. Otherwise, DCM does not configure the chip and ignores all data returned from the UTEST flash memory.
This results in LC becoming IN_FIELD. All DCF clients indicate their default values in this case.

#### 38.3 DCM register descriptions

#### 38.3.1 DCM memory map

DCM base address: 402A_C000h
Offset Register Access Reset value
Width
(In bits)
0h DCM Status (DCMSTAT) 32 R 0000_0371h
4h LC and LC Control (DCMLCC) 32 R See section
8h LC Scan Status (DCMLCS) 32 RW 0000_0000h
1Ch DCM Miscellaneous (DCMMISC) 32 RW 0000_0001h
20h Debug Status and Configuration (DCMDEB) 32 R 0000_0000h
Table continues on the next page...

---

*Page 958*

Device Configuration Module (DCM)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
2Ch DCF Error Count (DCMEC) 32 R 0000_0000h
30h DCF Scan Report (DCMSRR1) 32 RW 0000_0000h
34h DCF Scan Report (DCMSRR2) 32 RW 0000_0000h
38h DCF Scan Report (DCMSRR3) 32 RW 0000_0000h
3Ch DCF Scan Report (DCMSRR4) 32 RW 0000_0000h
40h DCF Scan Report (DCMSRR5) 32 RW 0000_0000h
44h DCF Scan Report (DCMSRR6) 32 RW 0000_0000h
48h DCF Scan Report (DCMSRR7) 32 RW 0000_0000h
4Ch DCF Scan Report (DCMSRR8) 32 RW 0000_0000h
50h DCF Scan Report (DCMSRR9) 32 RW 0000_0000h
54h DCF Scan Report (DCMSRR10) 32 RW 0000_0000h
58h DCF Scan Report (DCMSRR11) 32 RW 0000_0000h
5Ch DCF Scan Report (DCMSRR12) 32 RW 0000_0000h
60h DCF Scan Report (DCMSRR13) 32 RW 0000_0000h
64h DCF Scan Report (DCMSRR14) 32 RW 0000_0000h
68h DCF Scan Report (DCMSRR15) 32 RW 0000_0000h
6Ch DCF Scan Report (DCMSRR16) 32 RW 0000_0000h
80h LC Scan Status 2 (DCMLCS_2) 32 RW 0000_0000h

#### 38.3.2 DCM Status (DCMSTAT)

Offset
Register Offset
DCMSTAT 0h
Function
Indicates the status of DCM at different stages.
NOTE
This register resets on functional reset.

---

*Page 959*

Device Configuration Module (DCM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
DCMD DCMU DCML DCME DCMD
R 0 0 0 0 0
BGPS TS CST RR ONE
W
Reset 0 0 0 0 0 0 1 1 0 1 1 1 0 0 0 1
Fields
Field Function
Reserved
31-19
—
Reserved
18-16
—
Reserved
15-12
—
Reserved
11
—
10 Debug Password Scanning Status
DCMDBGPS Indicates the DCM debug password scanning status.
0b - Completed with errors
1b - Completed successfully
Reserved
9
—
8 DCM Utest DCF Scanning Status (valid only if DCMDONE bit is set)
DCMUTS
NOTE
This bit always returns 0 in In Field LC.
0b - Completed with errors.
1b - Completed successfully.
Reserved
7-5
Table continues on the next page...

---

*Page 960*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
—
4 LC Scanning Status (valid only if DCMDONE bit is set)
DCMLCST Indicates the DCM LC scanning status.
NOTE
This bit always returns 0 in INFIELD lifecycle.
0b - Completed with errors
1b - Completed successfully
Reserved
3-2
—
DCM Completion of Flash Scan with Error Status (valid only if DCMDONE bit is set)
1
0b - Completed with success.
DCMERR
1b - Completed with error.
0 DCM Flash Scanning Status
DCMDONE Indicates whether the DCM scanning is in progress or complete.
0b - Running
1b - Completed

#### 38.3.3 LC and LC Control (DCMLCC)

Offset
Register Offset
DCMLCC 4h
Function
Resets on the functional reset.

---

*Page 961*

Device Configuration Module (DCM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 DCMRLC DCMCLC
Reserv
Reserved
ed
W
1 1
Reset 0 0 0 0 0 0 0 0 0 u u u 0 u u u
1. Post reset, the reset value of this register is 0000_0077h, and after scanning, it changes according to the programmed
value.
Fields
Field Function
Reserved
31-10
—
Reserved
9-8
—
Reserved
7
—
6-4 Real LC
DCMRLC Projects the real LC of the chip.
The LC can move in this sequence: (010 : OEM_PROD) > (111 : IN_FIELD) > (001 : Pre-FA) > (000 : FA)
000b - FA
001b - Pre-FA
010b - OEM_PROD
100b - Reserved
101b - Reserved
111b - IN_FIELD
Reserved
3
—
2-0 Current LC
DCMCLC Projects the current LC of the chip.
The LC can move in this sequence: (010 : OEM_PROD) > (111 : IN_FIELD) > (001 : Pre-FA) > (000 : FA)
Table continues on the next page...

---

*Page 962*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
000b - FA
001b - Pre-FA
010b - OEM_PROD
100b - Reserved
101b - Reserved
111b - IN_FIELD

#### 38.3.4 LC Scan Status (DCMLCS)

Offset
Register Offset
DCMLCS 8h
Function
Stores the status of LC scanning. By default, the status of each LC is "not yet scanned."
This register:
• Resets on destructive reset.
• Always returns 0 in a valid IN_FIELD LC (in LC without an error).
This register captures the errors related to LC scanning on each of these resets: POR, destructive, and functional. If an error is
captured, its status in this register is cleared by writing 1 to the corresponding field or to any of the destructive or POR events.
All LC slot errors are captured and cleared independently.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DCML DCML DCML DCML DCML DCML DCML DCML
R 0 DCMLCC5 DCMLCC4
CFE5 CE5 CSS5 CFE4 CE4 CSS4 CFE3 CE3
W W1C W1C W1C W1C W1C W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
DCML
R DCMLCC3 Reserved
CSS3
W W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 963*

Device Configuration Module (DCM)
Fields
Field Function
Reserved
31-30
—
29 Pre-FA Flash Memory Error Check
DCMLCFE5 Indicates the status of the Pre-FA flash memory error check.
0b - No errors
1b - Errors exist
28 Pre-FA ECC Errors
DCMLCE5 Indicates if Pre-FA is successful or has ECC errors.
0b - No errors
1b - Errors exist
27-25 Pre-FA Marking Status
DCMLCC5 Indicates the Pre-FA marking status.
These errors may cause this field to indicate the "not scanned yet" status:
• If the reading completes too early and DCM has not yet scanned the LC.
• If there is an error in the flash memory after completion of the reading.
000b - Not scanned yet
001b - Marked as active
010b - Marked as inactive
011b - Region is erased/virgin
101b - Marked as inactive by an unknown pattern
110b - Scanning timed out
24 Pre-FA Scan Status
DCMLCSS5 Indicates the status of the Pre-FA scan.
0b - No errors
1b - Errors exist
23 IN_FIELD Flash Memory Error Check
DCMLCFE4 Indicates the status of IN_FIELD flash memory error check.
0b - No errors
1b - Errors exist
22 IN_FIELD ECC Errors
DCMLCE4 Indicates if IN_FIELD has ECC errors.
Table continues on the next page...

---

*Page 964*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
0b - No errors
1b - Errors exist
21-19 IN_FIELD Marking Status
DCMLCC4 Indicates the IN_FIELD marking status.
These errors may cause this field to indicate the "not scanned yet" status:
• If the reading completes too early and DCM has not yet scanned the LC.
• If there is an error in the flash memory after completion of the reading.
000b - Not scanned yet
001b - Marked as active
010b - Marked as inactive
011b - Region is erased/virgin
101b - Marked as inactive by an unknown pattern
110b - Scanning timed out
18 IN_FIELD Scan Status
DCMLCSS4 Indicates the status of the IN_FIELD scan.
0b - No errors
1b - Errors exist
17 OEM_PROD Flash Memory Error Check
DCMLCFE3 Indicates the status of the OEM_PROD flash memory error check.
0b - No errors
1b - Errors exist
16 OEM_PROD ECC Errors
DCMLCE3 Indicates if OEM_PROD has ECC errors.
0b - No errors
1b - Errors exist
15-13 OEM_PROD Marking
DCMLCC3 Indicates the OEM_PROD marking status.
These errors may cause this field to indicate the "not scanned yet" status:
• If the reading completes too early and DCM has not yet scanned the LC.
• If there is an error in the flash memory after completion of the reading.
000b - Not scanned yet
Table continues on the next page...

---

*Page 965*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
001b - Marked as active
010b - Marked as inactive
011b - Region is erased/virgin
101b - Marked as inactive by an unknown pattern
110b - Scanning timed out
12 OEM_PROD Scan Status
DCMLCSS3 Indicates the status of OEM_PROD scan.
0b - No errors
1b - Errors exist
Reserved
11-0
—

#### 38.3.5 DCM Miscellaneous (DCMMISC)

Offset
Register Offset
DCMMISC 1Ch
Function
Resets on destructive reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DCMC
R 0 0 0 0 0
ERS
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
DCMD DCMD
R 0 0 0
BGE BGT
W W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1

---

*Page 966*

Device Configuration Module (DCM)
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
28 DCF Client Errors
DCMCERS Records the status of errors from DCF clients.
0b - No errors on any of the DCF clients
1b - Atleast one safety DCF client has an error
Reserved
27-24
—
Reserved
23-14
—
Reserved
13-12
—
11 DCM ECC error on DBG sections
DCMDBGE This bit is set if there is any ECC error during scanning of CUST_PWD, or UID.
0b - No ECC error
1b - ECC error
10 DBG Section Timeout Error
DCMDBGT Indicates if there is a DCM UTEST flash memory timeout error in DBG sections. The value of this field is
1 in case a timeout error occurs when scanning CUST_PWD, or UID.
0b - No error
1b - Error exists
Reserved
9-0
—

---

*Page 967*

Device Configuration Module (DCM)

#### 38.3.6 Debug Status and Configuration (DCMDEB)

Offset
Register Offset
DCMDEB 20h
Function
Resets on destructive reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
APPD
R 0
BG_ ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
DCM_
R 0 0 0 0 0 0 0 0 0
APP ...
Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-17
—
16 Application Debug Status
APPDBG_STAT Indicates the application debug status of the chip.
_SOC
0b - Disabled
1b - Enabled
Reserved
15
—
Reserved
14
—
Reserved
13
—
Table continues on the next page...

---

*Page 968*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
Reserved
12
—
Reserved
11-10
—
Reserved
9
—
Reserved
8
—
Reserved
7-6
—
Reserved
5-2
—
1 DCM Authentication Engine Status for Application Core
DCM_APPDBG
NOTE
_STAT
This bit will read 0 in non-export control mode.
Reserved
0
—

#### 38.3.7 DCF Error Count (DCMEC)

Offset
Register Offset
DCMEC 2Ch
Function
Indicates the number of faulty DCF records.
This register resets on destructive reset.

---

*Page 969*

Device Configuration Module (DCM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCMECT
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-16
—
15-0 DCF Error Count
DCMECT Indicates the number of faulty DCF records.

#### 38.3.8 DCF Scan Report (DCMSRR1)

Offset
Register Offset
DCMSRR1 30h
Function
Resets on destructive reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DCMD DCME DCME
R 0 DCMDCFF1 0 DCMDCFE1
CFT1 SD1 SF1
W W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCMDCFE1
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 970*

Device Configuration Module (DCM)
Fields
Field Function
Reserved
31-30
—
29 Scanning Timeout On Flash Memory
DCMDCFT1 Indicates if scanning timeout exists on flash memory address.
0b - Does not exist
1b - Exists
28 Chip Side Error
DCMESD1 Indicates if an error exists on chip side. These errors could be parity errors or the ones reported by the
DCF client, such as write-once error.
0b - No errors
1b - Errors exist
27 Flash Memory Error
DCMESF1 Indicates if a UTEST flash memory ECC error exists.
0b - No errors
1b - Errors exist
26-24 DCF Record Location
DCMDCFF1 Indicates the DCF record location.
010b - Utest flash memory
101b - Others: Reserved
Reserved
23-21
—
20-0 Flash Memory Address
DCMDCFE1 Indicates the flash memory address of the DCF client having an error.

#### 38.3.9 DCF Scan Report (DCMSRR2)

Offset
Register Offset
DCMSRR2 34h
Function
Resets on destructive reset.

---

*Page 971*

Device Configuration Module (DCM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DCMD DCME DCME
R 0 DCMDCFF2 0 DCMDCFE2
CFT2 SD2 SF2
W W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCMDCFE2
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-30
—
29 Scanning Timeout On Flash Memory
DCMDCFT2 Indicates if scanning timeout exists on flash memory address.
0b - Does not exist
1b - Exists
28 Chip Side Error
DCMESD2 Indicates if an error exists on chip side. These errors could be parity errors or the ones reported by the
DCF client, such as write-once error.
0b - No errors
1b - Errors exist
27 Flash Memory Error
DCMESF2 Indicates if a UTEST flash memory ECC error exists.
0b - No errors
1b - Errors exist
26-24 DCF Record Location
DCMDCFF2 Indicates the DCF record location.
010b - Utest flash memory
101b - Others: Reserved
Reserved
23-21
—
Table continues on the next page...

---

*Page 972*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
20-0 Flash Memory Address
DCMDCFE2 Indicates the flash memory address of the DCF client having an error.

#### 38.3.10 DCF Scan Report (DCMSRR3)

Offset
Register Offset
DCMSRR3 38h
Function
Resets on destructive reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DCMD DCME DCME
R 0 DCMDCFF3 0 DCMDCFE3
CFT3 SD3 SF3
W W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCMDCFE3
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-30
—
29 Scanning Timeout On Flash Memory
DCMDCFT3 Indicates if scanning timeout exists on flash memory address.
0b - Does not exist
1b - Exists
28 Chip Side Error
DCMESD3
Table continues on the next page...

---

*Page 973*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
Indicates if an error exists on chip side. These errors could be parity errors or the ones reported by the
DCF client, such as write-once error.
0b - No errors
1b - Errors exist
27 Flash Memory Error
DCMESF3 Indicates if a UTEST flash memory ECC error exists.
0b - No errors
1b - Errors exist
26-24 DCF Record Location
DCMDCFF3 Indicates the DCF record location.
010b - Utest flash memory
101b - Others: Reserved
Reserved
23-21
—
20-0 Flash Memory Address
DCMDCFE3 Indicates the flash memory address of the DCF client having an error.

#### 38.3.11 DCF Scan Report (DCMSRR4)

Offset
Register Offset
DCMSRR4 3Ch
Function
Resets on destructive reset.

---

*Page 974*

Device Configuration Module (DCM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DCMD DCME DCME
R 0 DCMDCFF4 0 DCMDCFE4
CFT4 SD4 SF4
W W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCMDCFE4
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-30
—
29 Scanning Timeout On Flash Memory
DCMDCFT4 Indicates if scanning timeout exists on flash memory address.
0b - Does not exist
1b - Exists
28 Chip Side Error
DCMESD4 Indicates if an error exists on chip side. These errors could be parity errors or the ones reported by the
DCF client, such as write-once error.
0b - No errors
1b - Errors exist
27 Flash Memory Error
DCMESF4 Indicates if a UTEST flash memory ECC error exists.
0b - No errors
1b - Errors exist
26-24 DCF Record Location
DCMDCFF4 Indicates the DCF record location.
010b - Utest flash memory
101b - Others: Reserved
Reserved
23-21
—
Table continues on the next page...

---

*Page 975*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
20-0 Flash Memory Address
DCMDCFE4 Indicates the flash memory address of the DCF client having an error.

#### 38.3.12 DCF Scan Report (DCMSRR5)

Offset
Register Offset
DCMSRR5 40h
Function
Resets on destructive reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DCMD DCME DCME
R 0 DCMDCFF5 0 DCMDCFE5
CFT5 SD5 SF5
W W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCMDCFE5
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-30
—
29 Scanning Timeout On Flash Memory
DCMDCFT5 Indicates if scanning timeout exists on flash memory address.
0b - Does not exist
1b - Exists
28 Chip Side Error
DCMESD5
Table continues on the next page...

---

*Page 976*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
Indicates if an error exists on chip side. These errors could be parity errors or the ones reported by the
DCF client, such as write-once error.
0b - No errors
1b - Errors exist
27 Flash Memory Error
DCMESF5 Indicates if a UTEST flash memory ECC error exists.
0b - No errors
1b - Errors exist
26-24 DCF Record Location
DCMDCFF5 Indicates the DCF record location.
010b - Utest flash memory
101b - Others: Reserved
Reserved
23-21
—
20-0 Flash Memory Address
DCMDCFE5 Indicates the flash memory address of the DCF client having an error.

#### 38.3.13 DCF Scan Report (DCMSRR6)

Offset
Register Offset
DCMSRR6 44h
Function
Resets on destructive reset.

---

*Page 977*

Device Configuration Module (DCM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DCMD DCME DCME
R 0 DCMDCFF6 0 DCMDCFE6
CFT6 SD6 SF6
W W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCMDCFE6
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-30
—
29 Scanning Timeout On Flash Memory
DCMDCFT6 Indicates if scanning timeout exists on flash memory address.
0b - Does not exist
1b - Exists
28 Chip Side Error
DCMESD6 Indicates if an error exists on chip side. These errors could be parity errors or the ones reported by the
DCF client, such as write-once error.
0b - No errors
1b - Errors exist
27 Flash Memory Error
DCMESF6 Indicates if a UTEST flash memory ECC error exists.
0b - No errors
1b - Errors exist
26-24 DCF Record Location
DCMDCFF6 Indicates the DCF record location.
010b - Utest flash memory
101b - Others: Reserved
Reserved
23-21
—
Table continues on the next page...

---

*Page 978*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
20-0 Flash Memory Address
DCMDCFE6 Indicates the flash memory address of the DCF client having an error.

#### 38.3.14 DCF Scan Report (DCMSRR7)

Offset
Register Offset
DCMSRR7 48h
Function
Resets on destructive reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DCMD DCME DCME
R 0 DCMDCFF7 0 DCMDCFE7
CFT7 SD7 SF7
W W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCMDCFE7
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-30
—
29 Scanning Timeout On Flash Memory
DCMDCFT7 Indicates if scanning timeout exists on flash memory address.
0b - Does not exist
1b - Exists
28 Chip Side Error
DCMESD7
Table continues on the next page...

---

*Page 979*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
Indicates if an error exists on chip side. These errors could be parity errors or the ones reported by the
DCF client, such as write-once error.
0b - No errors
1b - Errors exist
27 Flash Memory Error
DCMESF7 Indicates if a UTEST flash memory ECC error exists.
0b - No errors
1b - Errors exist
26-24 DCF Record Location
DCMDCFF7 Indicates the DCF record location.
010b - Utest flash memory
101b - Others: Reserved
Reserved
23-21
—
20-0 Flash Memory Address
DCMDCFE7 Indicates the flash memory address of the DCF client having an error.

#### 38.3.15 DCF Scan Report (DCMSRR8)

Offset
Register Offset
DCMSRR8 4Ch
Function
Resets on destructive reset.

---

*Page 980*

Device Configuration Module (DCM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DCMD DCME DCME
R 0 DCMDCFF8 0 DCMDCFE8
CFT8 SD8 SF8
W W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCMDCFE8
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-30
—
29 Scanning Timeout On Flash Memory
DCMDCFT8 Indicates if scanning timeout exists on flash memory address.
0b - Does not exist
1b - Exists
28 Chip Side Error
DCMESD8 Indicates if an error exists on chip side. These errors could be parity errors or the ones reported by the
DCF client, such as write-once error.
0b - No errors
1b - Errors exist
27 Flash Memory Error
DCMESF8 Indicates if a UTEST flash memory ECC error exists.
0b - No errors
1b - Errors exist
26-24 DCF Record Location
DCMDCFF8 Indicates the DCF record location.
010b - Utest flash memory
101b - Others: Reserved
Reserved
23-21
—
Table continues on the next page...

---

*Page 981*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
20-0 Flash Memory Address
DCMDCFE8 Indicates the flash memory address of the DCF client having an error.

#### 38.3.16 DCF Scan Report (DCMSRR9)

Offset
Register Offset
DCMSRR9 50h
Function
Resets on destructive reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DCMD DCME DCME
R 0 DCMDCFF9 0 DCMDCFE9
CFT9 SD9 SF9
W W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCMDCFE9
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-30
—
29 Scanning Timeout On Flash Memory
DCMDCFT9 Indicates if scanning timeout exists on flash memory address.
0b - Does not exist
1b - Exists
28 Chip Side Error
DCMESD9
Table continues on the next page...

---

*Page 982*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
Indicates if an error exists on chip side. These errors could be parity errors or the ones reported by the
DCF client, such as write-once error.
0b - No errors
1b - Errors exist
27 Flash Memory Error
DCMESF9 Indicates if a UTEST flash memory ECC error exists.
0b - No errors
1b - Errors exist
26-24 DCF Record Location
DCMDCFF9 Indicates the DCF record location.
010b - Utest flash memory
101b - Others: Reserved
Reserved
23-21
—
20-0 Flash Memory Address
DCMDCFE9 Indicates the flash memory address of the DCF client having an error.

#### 38.3.17 DCF Scan Report (DCMSRR10)

Offset
Register Offset
DCMSRR10 54h
Function
Resets on destructive reset.

---

*Page 983*

Device Configuration Module (DCM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DCMD DCME DCME
R 0 DCMDCFF10 0 DCMDCFE10
CFT ... SD10 SF10
W W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCMDCFE10
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-30
—
29 Scanning Timeout On Flash Memory
DCMDCFT10 Indicates if scanning timeout exists on flash memory address.
0b - Does not exist
1b - Exists
28 Chip Side Error
DCMESD10 Indicates if an error exists on chip side. These errors could be parity errors or the ones reported by the
DCF client, such as write-once error.
0b - No errors
1b - Errors exist
27 Flash Memory Error
DCMESF10 Indicates if a UTEST flash memory ECC error exists.
0b - No errors
1b - Errors exist
26-24 DCF Record Location
DCMDCFF10 Indicates the DCF record location.
010b - Utest flash memory
101b - Others: Reserved
Reserved
23-21
—
Table continues on the next page...

---

*Page 984*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
20-0 Flash Memory Address
DCMDCFE10 Indicates the flash memory address of the DCF client having an error.

#### 38.3.18 DCF Scan Report (DCMSRR11)

Offset
Register Offset
DCMSRR11 58h
Function
Resets on destructive reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DCMD DCME DCME
R 0 DCMDCFF11 0 DCMDCFE11
CFT ... SD11 SF11
W W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCMDCFE11
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-30
—
29 Scanning Timeout On Flash Memory
DCMDCFT11 Indicates if scanning timeout exists on flash memory address.
0b - Does not exist
1b - Exists
28 Chip Side Error
DCMESD11
Table continues on the next page...

---

*Page 985*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
Indicates if an error exists on chip side. These errors could be parity errors or the ones reported by the
DCF client, such as write-once error.
0b - No errors
1b - Errors exist
27 Flash Memory Error
DCMESF11 Indicates if a UTEST flash memory ECC error exists.
0b - No errors
1b - Errors exist
26-24 DCF Record Location
DCMDCFF11 Indicates the DCF record location.
010b - Utest flash memory
101b - Others: Reserved
Reserved
23-21
—
20-0 Flash Memory Address
DCMDCFE11 Indicates the flash memory address of the DCF client having an error.

#### 38.3.19 DCF Scan Report (DCMSRR12)

Offset
Register Offset
DCMSRR12 5Ch
Function
Resets on destructive reset.

---

*Page 986*

Device Configuration Module (DCM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DCMD DCME DCME
R 0 DCMDCFF12 0 DCMDCFE12
CFT ... SD12 SF12
W W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCMDCFE12
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-30
—
29 Scanning Timeout On Flash Memory
DCMDCFT12 Indicates if scanning timeout exists on flash memory address.
0b - Does not exist
1b - Exists
28 Chip Side Error
DCMESD12 Indicates if an error exists on chip side. These errors could be parity errors or the ones reported by the
DCF client, such as write-once error.
0b - No errors
1b - Errors exist
27 Flash Memory Error
DCMESF12 Indicates if a UTEST flash memory ECC error exists.
0b - No errors
1b - Errors exist
26-24 DCF Record Location
DCMDCFF12 Indicates the DCF record location.
010b - Utest flash memory
101b - Others: Reserved
Reserved
23-21
—
Table continues on the next page...

---

*Page 987*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
20-0 Flash Memory Address
DCMDCFE12 Indicates the flash memory address of the DCF client having an error.

#### 38.3.20 DCF Scan Report (DCMSRR13)

Offset
Register Offset
DCMSRR13 60h
Function
Resets on destructive reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DCMD DCME DCME
R 0 DCMDCFF13 0 DCMDCFE13
CFT ... SD13 SF13
W W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCMDCFE13
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-30
—
29 Scanning Timeout On Flash Memory
DCMDCFT13 Indicates if scanning timeout exists on flash memory address.
0b - Does not exist
1b - Exists
28 Chip Side Error
DCMESD13
Table continues on the next page...

---

*Page 988*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
Indicates if an error exists on chip side. These errors could be parity errors or the ones reported by the
DCF client, such as write-once error.
0b - No errors
1b - Errors exist
27 Flash Memory Error
DCMESF13 Indicates if a UTEST flash memory ECC error exists.
0b - No errors
1b - Errors exist
26-24 DCF Record Location
DCMDCFF13 Indicates the DCF record location.
010b - Utest flash memory
101b - Others: Reserved
Reserved
23-21
—
20-0 Flash Memory Address
DCMDCFE13 Indicates the flash memory address of the DCF client having an error.

#### 38.3.21 DCF Scan Report (DCMSRR14)

Offset
Register Offset
DCMSRR14 64h
Function
Resets on destructive reset.

---

*Page 989*

Device Configuration Module (DCM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DCMD DCME DCME
R 0 DCMDCFF14 0 DCMDCFE14
CFT ... SD14 SF14
W W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCMDCFE14
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-30
—
29 Scanning Timeout On Flash Memory
DCMDCFT14 Indicates if scanning timeout exists on flash memory address.
0b - Does not exist
1b - Exists
28 Chip Side Error
DCMESD14 Indicates if an error exists on chip side. These errors could be parity errors or the ones reported by the
DCF client, such as write-once error.
0b - No errors
1b - Errors exist
27 Flash Memory Error
DCMESF14 Indicates if a UTEST flash memory ECC error exists.
0b - No errors
1b - Errors exist
26-24 DCF Record Location
DCMDCFF14 Indicates the DCF record location.
010b - Utest flash memory
101b - Others: Reserved
Reserved
23-21
—
Table continues on the next page...

---

*Page 990*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
20-0 Flash Memory Address
DCMDCFE14 Indicates the flash memory address of the DCF client having an error.

#### 38.3.22 DCF Scan Report (DCMSRR15)

Offset
Register Offset
DCMSRR15 68h
Function
Resets on destructive reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DCMD DCME DCME
R 0 DCMDCFF15 0 DCMDCFE15
CFT ... SD15 SF15
W W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCMDCFE15
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-30
—
29 Scanning Timeout On Flash Memory
DCMDCFT15 Indicates if scanning timeout exists on flash memory address.
0b - Does not exist
1b - Exists
28 Chip Side Error
DCMESD15
Table continues on the next page...

---

*Page 991*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
Indicates if an error exists on chip side. These errors could be parity errors or the ones reported by the
DCF client, such as write-once error.
0b - No errors
1b - Errors exist
27 Flash Memory Error
DCMESF15 Indicates if a UTEST flash memory ECC error exists.
0b - No errors
1b - Errors exist
26-24 DCF Record Location
DCMDCFF15 Indicates the DCF record location.
010b - Utest flash memory
101b - Others: Reserved
Reserved
23-21
—
20-0 Flash Memory Address
DCMDCFE15 Indicates the flash memory address of the DCF client having an error.

#### 38.3.23 DCF Scan Report (DCMSRR16)

Offset
Register Offset
DCMSRR16 6Ch
Function
Resets on destructive reset.

---

*Page 992*

Device Configuration Module (DCM)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
DCMD DCME DCME
R 0 DCMDCFF16 0 DCMDCFE16
CFT ... SD16 SF16
W W1C W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R DCMDCFE16
W W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-30
—
29 Scanning Timeout On Flash Memory
DCMDCFT16 Indicates if scanning timeout exists on flash memory address.
0b - Does not exist
1b - Exists
28 Chip Side Error
DCMESD16 Indicates if an error exists on chip side. These errors could be parity errors or the ones reported by the
DCF client, such as write-once error.
0b - No errors
1b - Errors exist
27 Flash Memory Error
DCMESF16 Indicates if a UTEST flash memory ECC error exists.
0b - No errors
1b - Errors exist
26-24 DCF Record Location
DCMDCFF16 Indicates the DCF record location.
010b - Utest flash memory
101b - Others: Reserved
Reserved
23-21
—
Table continues on the next page...

---

*Page 993*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
20-0 Flash Memory Address
DCMDCFE16 Indicates the flash memory address of the DCF client having an error.

#### 38.3.24 LC Scan Status 2 (DCMLCS_2)

Offset
Register Offset
DCMLCS_2 80h
Function
Stores the status of LC scanning. By default, the status of each LC is "not yet scanned."
This register:
• Resets on destructive reset.
• Always returns 0 in a valid IN_FIELD LC (in LC without an error).
This register captures the errors related to LC scanning on each of these resets: POR, destructive, and functional. If an error is
captured, its status in this register is cleared by writing 1 to the corresponding field or to any of the destructive or POR events.
All LC slot errors are captured and cleared independently.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
DCML DCML DCML
R 0 DCMLCC6
CFE6 CE6 CSS6
W W1C W1C W1C W1C
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-6
—
Table continues on the next page...

---

*Page 994*

Device Configuration Module (DCM)
Table continued from the previous page...
Field Function
5 Flash Memory Error Check
DCMLCFE6 Indicates the status of flash memory check.
0b - No errors
1b - Errors exist
4 FA ECC Errors
DCMLCE6 Indicates if ECC errors exist in FA.
0b - No errors
1b - Errors exist
3-1 FA Marking
DCMLCC6 Indicates the FA marking status.
These errors may cause this field to indicate the "not scanned yet" status:
• If the reading completes too early and DCM has not yet scanned the LC.
• If there is an error in the flash memory after completion of the reading.
000b - Not scanned yet
001b - Marked as active
010b - Marked as inactive
011b - Region is erased/virgin
101b - Marked as inactive by an unknown pattern
110b - Scanning timed out
0 FA Scan Status
DCMLCSS6 Indicates if errors exist in the FA scan.
0b - No errors
1b - Errors exist

#### 38.4 Glossary

FA Failure analysis
LC Life cycle
Pre-FA Pre-failure analysis
RoT Root of trust
OTP One Time Programmable
DCF Device Configuration Format
PFC Platform Flash Controller

---

*Page 995*

