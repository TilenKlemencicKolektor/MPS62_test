<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 70 -->

2 21 20 19 18 17 16 15 8 7 0
80h IDHIT SRR IDE RTR DLC TIMESTAMP
84h ID standard ID extended
88h Data byte 0 Data byte 1 Data byte 2 Data byte 3
8Ch Data byte 4 Data byte 5 Data byte 6 Data byte 7
90h–DCh Reserved
E0h ID filter table element 0
E4h ID filter table element 1
E8h– ID filter table elements 2 to 125
2D4h
2D8h ID filter table element 126
2DCh ID filter table element 127
= Unimplemented or reserved
Each ID filter table element occupies an entire 32-bit word. One, two, or four Identifier Acceptance Filters (IDAF) can compound
each element, depending on MCR[IDAM] . The following tables show the IDAF indexing.
Table 465 shows the three different formats of the ID table elements. All elements of the table must have the same format. See
Legacy RX FIFO for more information.
Table 465. ID table structure
Format 31 30 29 24 23 16 15 14 13 8 7 1 0
A RTR IDE
RXIDA
(standard = 29–19, extended = 29–1)
B RTR IDE RTR IDE
RXIDB_0 RXIDB_1
(standard = 29–19, extended = 29–16) (standard = 13–3, extended = 13–0)
C RXIDC_0 RXIDC_1 RXIDC_2 RXIDC_3
(std and ext = 31–24) (std and ext = (std and ext = 15–8) (std and ext = 7–0)
23–16)
= Unimplemented or Reserved

---

*Page 2464*

CAN (FlexCAN)
Table 466. Field descriptions
Mnemonic Field Description
RTR Remote Frame
Specifies whether remote frames are accepted into the Legacy FIFO if they
match the target ID.
1: Remote frames can be accepted and data frames are rejected.
0: Remote frames are rejected and data frames can be accepted.
IDE Extended Frame
Specifies whether extended or standard frames are accepted into the Legacy
FIFO if they match the target ID.
1: Extended frames can be accepted and standard frames are rejected.
0: Extended frames are rejected and standard frames can be accepted.
RXIDA RX Frame Identifier (Format Specifies an ID to be used as acceptance criteria for the Legacy FIFO. In the
A) standard frame format, only the 11 most significant bits ( 29 to 19 ) are used
for frame identification. In the extended frame format, all bits are used.
RXIDB_0, RX Frame Identifier (Format Specifies an ID to be used as acceptance criteria for the Legacy FIFO. In the
RXIDB_1 B) standard frame format, the 11 most significant bits (a full standard ID) ( 29
to 19 and 13 to 3 ) are used for frame identification. In the extended frame
format, all 14 bits of the field are compared to the 14 most significant bits of
the received ID.
RXIDC_0, RX Frame Identifier (Format Specifies an ID to be used as acceptance criteria for the Legacy FIFO.
RXIDC_1, C) In both standard and extended frame formats, all 8 bits of the field are
RXIDC_2, compared to the 8 most significant bits of the received ID.
RXIDC_3
IDHIT Identifier Acceptance Filter Indicates which identifier acceptance filter the received message in the
Hit Indicator output of the Legacy RX FIFO hit. See Legacy RX FIFO for more information.

#### 70.6.7 Enhanced RX FIFO structure

When ERFCR[ERFEN] = 1, the Enhanced RX FIFO is enabled. The region 2000h–204Ch contains the output of the Enhanced
RX FIFO, which the CPU must read as a message buffer. This output contains the oldest message that has been received but
not yet read.
Table 467. Enhanced RX FIFO structure
31 30 29 28 24 23 22 21 20 19 18 17 16 15 8 7 6 0
2000h EDL BRS ESI Reserved SRR IDE RTR DLC TIMESTAMP LEGACY
2004h Reserved ID (standard/extended) ID (extended)
2008h Data byte 0 Data byte 1 Data byte 2 Data byte 3
200Ch Data byte 4 Data byte 5 Data byte 6 Data byte 7
2010h Data byte 8 Data byte 9 Data byte 10 Data byte 11
2014h Data byte 12 Data byte 13 Data byte 14 Data byte 15
2018h Data byte 16 Data byte 17 Data byte 18 Data byte 19
201Ch Data byte 20 Data byte 21 Data byte 22 Data byte 23
Table continues on the next page...

---

*Page 2465*

CAN (FlexCAN)
Table 467. Enhanced RX FIFO structure (continued)
2020h Data byte 24 Data byte 25 Data byte 26 Data byte 27
2024h Data byte 28 Data byte 29 Data byte 30 Data byte 31
2028h Data byte 32 Data byte 33 Data byte 34 Data byte 35
202Ch Data byte 36 Data byte 37 Data byte 38 Data byte 39
2030h Data byte 40 Data byte 41 Data byte 42 Data byte 43
2034h Data byte 44 Data byte 45 Data byte 46 Data byte 47
2038h Data byte 48 Data byte 49 Data byte 50 Data byte 51
203Ch Data byte 52 Data byte 53 Data byte 54 Data byte 55
2040h Data byte 56 Data byte 57 Data byte 58 Data byte 59
2044h Data byte 60 Data byte 61 Data byte 62 Data byte 63
IH_OFF Reserved ID HIT
TS_OFF HR TIMESTAMP
2050h
... 19 Enhanced FIFO Elements (Reserved)
263Ch
NOTE
ID HIT offset and high-resolution timestamp offset change dynamically according to data length code (DLC) as
shown in Table 468 .
Table 468. ID HIT offset and high-resolution timestamp offset
Data Length Code (DLC) ID HIT offset (IH_OFF) High-resolution timestamp offset
(TS_OFF)
0 2008h 200Ch
1–4 200Ch 2010h
5–8 2010h 2014h
9 2014h 2018h
10 2018h 201Ch
11 201Ch 2020h
12 2020h 2024h
13 2028h 202Ch
14 2038h 203Ch
15 2048h 204Ch

---

*Page 2466*

CAN (FlexCAN)
Table 469. Field descriptions
Mnemonic Field Description
EDL Extended Data Length
Distinguishes between classical CAN format and CAN FD format frames.
0: Classical CAN frame format
1: CAN FD frame format
BRS Bit Rate Switch
Defines whether the bit rate is switched inside a CAN FD format frame.
0: Bit rate is not switched in a CAN FD frame.
1: Bit rate is switched in a CAN FD frame.
ESI Error State Indicator
Indicates whether the transmitting node is error-active or error-passive. This
field is meaningful only if EDL = 1.
0: Error-active
1: Error-passive
SRR Substitute Remote Request
Fixed recessive bit, used only in extended format. Transmitting nodes always
send it as recessive and receiving nodes can receive it as either recessive
or dominant. If FlexCAN receives this bit as dominant, it is interpreted as an
arbitration loss.
IDE ID Extended Bit
Identifies whether the frame format is standard or extended.
0: Standard
1: Extended
RTR Remote Frame
Identifies whether the current frame is a data frame or a remote request.
0: Data frame
1: Remote request
DLC Data Length Code
Defines the number of bytes in the data field of a CAN frame (Data byte 0
to Data byte 63 ). When RTR = 1, the frame is a remote request and does
not include the data field, regardless of the DLC field. See Table 457 for
more details.
LEGACY 16-bit Timestamp
Provides a copy of the Free-Running Timer, captured during the CAN frame.
TIMESTAMP
See Table 456 for details about legacy timestamp operation.
ID Frame Identifier In base frame format, only the 11 most significant bits are used for frame
identification. The 18 least significant bits are ignored. In extended frame
format, all bits are used for frame identification.
DATA BYTE Data Field Up to 64 bytes can be stored in the data field.
0– 63
IDHIT Identifier Acceptance Filter Indicates which Enhanced RX FIFO Filter Element (ERFFEL0 - ERFFEL127)
Hit Indicator the received message in the output of the Enhanced RX FIFO hit. For each
filter region, standard-ID filter space, and extended-ID filter space, there is an
independent index starting from zero. Table 470 shows how FlexCAN writes
IDHIT according to each filter element.
Table continues on the next page...

---

*Page 2467*

CAN (FlexCAN)
Table 469. Field descriptions (continued)
Mnemonic Field Description
HR High-resolution Timestamp 32-bit timebase captured during the CAN frame. When CTRL2[TSTAMPCAP]
TIMESTAMP is not zero, a 32-bit timebase is captured from a dedicated on-chip timer
which operates in free-running mode. See CTRL2[TSTAMPCAP] for details
about capture point configuration of the high-resolution timestamp.
Table 470. IDHIT for Enhanced RX FIFO
Enhanced RX FIFO filter element - IDHIT value Filter element type
ERFFEL
ERFFEL0 0 Extended-ID
ERFFEL1 1 Extended-ID
Extended-ID
. .
. .
. .
ERFFEL(m-1) m-1 Extended-ID
ERFFEL(m) 0 Standard-ID
ERFFEL(m+1) 1 Standard-ID
Standard-ID
. .
. .
. .
ERFFEL(2n-m+1) 2x(n-m)+1 Standard-ID
NOTE
Where m = NEXIF and n = NFE. If NEXIF = 0, only standard-ID filter elements exist. If NEXIF > NFE, only
extended-ID filter elements exist.

#### 70.7 Glossary

Active message buffer A message buffer is active if it can participate in the current matching or arbitration process.
Bus interface unit FlexCAN submodule responsible for the interface to the CPU.
[8]
Bus off See CAN Specification .
[8]
CAN Controller Area Network, a serial communication protocol defined in CAN Specification and
[9]
ISO International Standard .
CHI Controller-host interface, a FlexCAN submodule responsible for message buffer matching and
arbitration algorithms.
CRC Cyclic redundancy check
[8] Controller Area Network - CAN Specification Version 2.0 Part A, Part B, Robert Bosch GmbH, 1991.
[9] ISO International Standard - ISO 11898 First Edition 1993 Road Vehicles - Interchange of Digital Information - Controller
Area Network (CAN) for high-speed Communication.

---

*Page 2468*

CAN (FlexCAN)
Dominant bit A dominant bit wins the arbitration on the CAN bus. It is transmitted as 0.
Doze mode A system low-power mode where the CPU bus remains active and a global Doze mode request
is sent to all peripherals asking them to enter low power mode.
[8]
Error active See CAN Specification.
[8]
Error frame See CAN Specification.
[8]
Error passive See CAN Specification.
[8]
Form error See CAN Specification.
Hard reset A reset coming from an external pin and/or following power-on. It resets everything.
[8]
Idle See CAN Specification.
[8]
Information processing time See CAN Specification.
[8]
Intermission See CAN Specification.
Matching elements Data used in the matching process, such as ID, filter, mask, etc.
MB See Message buffer .
Message buffer Internal FlexCAN data structure containing bytes received from, or to be transmitted to, the
CAN line, as well as information about this data.
[8]
Overload frame See CAN Specification.
[8]
Phase buffer segment See CAN Specification.
Recessive bit A recessive bit loses the arbitration on the CAN bus. It is transmitted as 1.
Sclock Serial clock. Obtained by dividing the clock feeding the CAN engine (oscillator or bus clock) by
a prescaler factor. The Sclock period defines the time quantum for CAN protocol timing.
Soft reset Global reset typically used by peripherals to reinitialize some of its registers, but not all of them.
Stop mode A system low-power mode in which all chip clocks are stopped for maximum power savings.
[8]
Stuffing error See CAN Specification.
Time quantum This is equal to the Sclock period. It is the minimum time period used to compose the CAN
protocol bit timing.

---

*Page 2469*

