<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 18 -->

# Chapter 18

# Extended Resource Domain Controller (XRDC)

#### 18.1 Chip-specific XRDC information

#### 18.1.1 MDAC configuration

All MDACs with DID = 0 use the default DID parameter.
Table 58. MDAC configuration
Submodule Configuration XRDC Bus master Master Default PID Nonsecure Applicability
instance MDACFG# ID value DID input
XRDC_MDAC0 Processor 1 0h PID0 0b MCXE315/
Cortex- 0h
MCXE316/
M7_0, AXI,
MCXE317/
AHBP
MCXE31B
Cortex-M7_0
debug
XRDC_MDAC1 Nonprocessor 1 2h 0h — 1b MCXE315/
eDMA AHB
MCXE316/
MCXE317/
MCXE31B
XRDC_MDAC2 Nonprocessor 1 Ah 0h — 1b MCXE315/
Test port
MCXE316/
AHB
MCXE317/
MCXE31B
XRDC_MDAC3 Processor 1 3h 0h PID3 0b MCXE315/
ELE_HSEB
MCXE316/
AHB
MCXE317/
ELE_HSEB
MCXE31B
debug
XRDC_MDAC5 Nonprocessor 1 EMAC AHB 4h 0h — 1b MCXE31B

#### 18.1.2 MRC configuration

Table 59. MRC configuration
Submodule instance Region format Number of Slaves protected (port number) Applicability
region descriptors
1
XRDC_MRC0 Auto 16 or 8 MCXE315/MCXE316/
PFLASH_0 (0)
MCXE317/MCXE31B
PFLASH_1 (1)
1
PFLASH_2 (2)
PFLASH_WR (3)
1
XRDC_MRC1 Auto 16 or 8 MCXE315/MCXE316/
PRAM0_0 (0)
MCXE317/MCXE31B
1
PRAM1_0 (2)
Table continues on the next page...

---

*Page 398*

Extended Resource Domain Controller (XRDC)
Table 59. MRC configuration (continued)
Submodule instance Region format Number of Slaves protected (port number) Applicability
region descriptors
TCM backdoor (1)
XRDC_MRC2 Auto 4 QuadSPI (0) MCXE31B
1. Applicable to MCXE31B

#### 18.1.3 PAC configuration

Table 60. PAC configuration
Module name Slaves protected Applicability
XRDC_PAC0 AIPS_0 MCXE315/MCXE316/MCXE317/MCXE31B
XRDC_PAC1 AIPS_1 MCXE315/MCXE316/MCXE317/MCXE31B
XRDC_PAC2 AIPS_2 MCXE31B
NOTE
For PDAC registers assignment to peripherals, see the memory map file attached to this document.

#### 18.1.4 Domain Error Word registers (DERR_W x _0-18) mapping

The mapping of the domain error capture registers is as follows:
Table 61. Domain Error Word registers mapping
Register Corresponding MRC/PAC Available in chips
DERR_W x _0 MRC0 MCXE315/MCXE316/MCXE317/MCXE31B
DERR_W x _1 MRC1 MCXE315/MCXE316/MCXE317/MCXE31B
DERR_W x _2 MRC2 MCXE31B
DERR_W x _16 PAC0 MCXE315/MCXE316/MCXE317/MCXE31B
DERR_W x _17 PAC1 MCXE315/MCXE316/MCXE317/MCXE31B
DERR_W x _18 PAC2 MCXE31B
Where x : 0, 1, 2, 3.
If the above registers are accessed in chips wherein they are not present, a bus error gets reported.

#### 18.1.5 Exceptions and violations

A write attempt by a noncore bus master outside the defined ranges leads to an exception in case the XRDC region is defined to
prevent noncore master access. The chip generates a bus error if you violate XRDC policies.

#### 18.1.6 Configuration using SBAF

SBAF must protect access to its own resources. Hence, XRDC is configured during initialization. SBAF provides the mechanism
for you to configure XRDC during boot. You must program the configuration data in the application flash memory region.

---

*Page 399*

Extended Resource Domain Controller (XRDC)
18.1.6.1 Default configuration of MDAC when ELE_HSEB firmware feature is disabled
Table 62. MDAC registers default value
MDAC register Value Domain number assigned
ELE_HSEB CPU (MDAC3) C000_0002h 0
All the other MDAC domain numbers are 0 and SBAF does not lock them.
18.1.6.2 Default configuration of MRC registers when ELE_HSEB firmware usage feature flag is disabled
MRC0
16 descriptors
RGD 0 to RGD 13
Protected slaves: PFLASH0, PFLASH1, PFLASH2, PFLASH3, DFLASH
Protected address range: 0h–1FFB_FFFFh
MRC0 Access attributes: Read and write
BAF-configurable region descriptors.
RGD 14 ELE_HSEB UTEST area These region descriptors are marked
valid after programming address range.
Application-configurable region
RGD 15 BAF code area
descriptors marked as invalid and locked.
BAF configures memory region descriptor 0
when ELE_HSEB firmware-feature flag is cleared.
Figure 38. Default configuration of MRC registers when ELE_HSEB firmware usage feature flag is disabled
Table 63. Default configuration of MRC registers when ELE_HSEB firmware usage feature flag is disabled
MRC Region Region address Name Remarks SBAF's configuration
number descriptor range (in hex)
number
0 14 1B00_0200 ‑ ELE_HSE UTEST area that
W0 = start address = 1B00_0200h
1B00_06FF B UTEST is accessible by
W1 = end address = 1B00_06FFh
area ELE_HSEB core only.
Start address is W2 = {SE = 0, SNUM = 0, D3ACP = 7h
1B00_0200h and [all permission]}
1B00_06FFh for others
LC
1 15 007F_4000 ‑ SBAF Code area for
W0 = start address= 007F_4000h
007F_FFFF code area SBAF and ELE_HSEB
W1 = end address= 007F_FFFFh
firmware
W2 = {SE = 0, SNUM = 0, D2ACP =7h [all
permission], D1ACP = 0h [no permission],
D0ACP = 0h [no permission]}
18.1.6.3 PDAC default configuration
SBAF configures and lock the following peripherals for its exclusive use. Read and write access to these peripheral are not
provided to application domains.

---

*Page 400*

Extended Resource Domain Controller (XRDC)
Table 64. PDAC default configuration
Peripheral Peripheral PDAC number Remarks
Flash memory controller alternate 155 ELE_HSEB uses the alternate interface exclusively.
Flash memory alternate 188 ELE_HSEB uses the alternate interface exclusively.
HSE_GPR 231 All cores have all permissions.

#### 18.1.7 Configuration when ELE_HSEB firmware-feature flag is cleared

When the ELE_HSEB firmware-feature flag is cleared, SBAF:
• Does not permit XRDC configuration.
• Locks the aforementioned configurations.

#### 18.2 Overview

XRDC manages access control between initiators (cores and noncore initiators) and targets (memories and peripherals) by
placing them in virtual groups called domains .
Conceptually, a domain is one or more initiators and memories and peripherals, that are isolated from others. It may help to look
at a domain as a permissions group within a computing environment. All initiators in a domain have the same access to chip
resources such as memory and peripherals. See Introduction to domains for more information on domains.
The protection provided by XRDC access control is in addition to the local memory protection unit contained within each core.
NOTE
Terminology in this chapter has been updated as follows:
Table 65. Updated terms
Updated term Deprecated term
Initiator Master
Target Slave

#### 18.2.1 Features

• Enables you to partition chip resources ( initiator and target ) into access-controlled domain .
— Each domain has a unique DID .
— The DID is an attribute of every system bus transaction.
• Provides a four-level hierarchical access control scheme for defining an ACP for each target in a domain. See Access control
model for more information.
— Memory region descriptors define access policies for address ranges within memories.
— Peripheral access control registers define access policies for individual peripherals.
• Supports optional hardware semaphores to dynamically modify access rights for target resources.

---

*Page 401*

Extended Resource Domain Controller (XRDC)

#### 18.2.2 Block diagram

Initiator m
XRDC
Domain
assignment
(MDAC)
Switch fabric or
crossbar
Memory access Peripheral access
control policy contol policy
evaluation evaluation
(MRC) (PAC)
Memory module Peripheral s
Figure 39. Block diagram

#### 18.2.3 Block descriptions

Block Description
Domain assignment A process that adds information to transactions, including:
• DID
• Privileged attribute
• Secure attribute
Domain assignment is performed by the MDAC submodule.
See:
• Domain assignment
• Master domain assignment controller (MDAC)
Initiator A core or noncore (for example, DMA) module that can initiate transactions with memory or
peripheral resources.
Memory Non-volatile memory, RAM, or other memory.
Memory access control A process that determines whether the domain associated with a transaction has access rights
policy evaluation to a memory location. The process is performed by the MRC submodule.
See:
• Memory region ACP evaluation
• Memory region controller (MRC)
Peripheral A nonmemory resource module within the chip—an ADC, timer module, or communications
module, for example.
Peripheral access control A process that determines whether the domain associated with a transaction has access rights
policy evaluation to a peripheral. The process is performed by the PAC submodule.
Table continues on the next page...

---

*Page 402*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Block Description
See Peripheral access controller (PAC) .
Crossbar The chip's module and I/O interconnect infrastructure.

#### 18.2.4 Indexes used in this chapter

Table 66. Indexes used in this chapter
Index Description
c Memory controller number. For example, MRC c .
d Domain number. For example, PDAC_W0_6[D d ACP].
m Initiator number. For example, PID m .
r Memory region number. For example, RGD_W0_ r .
s PDAC slot number. For example, PDAC_W0_ s [D0ACP].
w Word number. In a group of registers consisting of consecutive 32-bit registers, w is the 0-indexed register
number. For example, MRGD_W w _0.

#### 18.2.5 Exceptions and violations

A write attempt by a noncore bus initiator outside the defined ranges leads to an exception in case the XRDC region is defined to
prevent noncore initiator access. The chip generates a bus error if you violate XRDC policies.

#### 18.3 Modes of operation

XRDC does not support any special modes of operation.

#### 18.4 External signal description

XRDC does not have any external signals.

#### 18.5 Functional description

#### 18.5.1 Introduction to domains

A domain typically consists of one or more initiators , along with the memories and peripherals those initiators are allowed to
access. Because it is access-controlled, a domain acts as an independent computing environment.
Domains enable applications to coexist on the same silicon with a firewall between them, enforcing absolute
interference protection.
Generally, you create each domain to meet a specific need. Examples of XRDC domain usage include:
• Isolation of real-time from non-real-time applications to ensure resource availability
• Isolation of safety-critical code from non-safety-critical code
• Isolation of third-party untrusted applications from trusted software
• Isolation of memory regions to ensure data security or to prevent accidental overwrites
• Limiting write access for a specific area of system memory to a single DMA module instance

---

*Page 403*

Extended Resource Domain Controller (XRDC)
• Limiting read access for a specific area of system memory to a specific processor core
You can assign a core to multiple domains but only one of those domains can be active at a given time.
Within each XRDC instance, each domain has a unique identifier, known as its DID . If an XRDC instance has 16 DIDs, that means
the instance has 16 available domains.
You control which initiators can access a peripheral by configuring the domain ACP for each peripheral. Similarly, you control
which initiators can access a memory region by configuring the ACP for each memory region.

#### 18.5.2 Submodules

XRDC implements its functionality through its hardware submodules:
• Master domain assignment controller (MDAC)
• Memory region controller (MRC)
• Peripheral access controller (PAC)
18.5.2.1 Master domain assignment controller (MDAC)
The MDAC submodule performs domain assignment logic. XRDC contains an MDAC submodule for each XRDC-protected
initiator. Each MDAC assigns a DID to every transaction from its associated initiator. You configure the domain assignment activity
for each MDAC through a set of registers , in one of two formats :
• DFMT0 core domain assignment registers
• DFMT1 noncore domain assignment registers
To understand the role of domains in XRDC protection, see Introduction to domains .
For a full explanation of the domain assignment process, see Domain assignment .
18.5.2.2 Memory region controller (MRC)
The MRC submodule performs memory region access control. Each XRDC instance contains the number of MRCs indicated in
HWCFG0[NMRC]. Each MRC is associated with one or more memories (see the chip-specific XRDC information for details). The
MRC controls memory access using memory region descriptors. MRCFG c [NMRGD] indicates the number of region descriptors
available for MRC c .
Each memory region descriptor defines a memory address range and a configurable access control policy for each domain using
a set of four or five 32-bit registers (see Memory ACP evaluation registers ).
Memory region descriptors also support including an optional hardware semaphore in the ACP evaluation for memory regions
shared by multiple domains (see Hardware semaphores and dynamic access rights ).
For a full explanation of the memory region ACP evaluation process, see Memory region ACP evaluation .
18.5.2.3 Peripheral access controller (PAC)
The PAC submodule provides domain access control for all peripherals connected to a single peripheral bus. Each XRDC contains
the number of PACs indicated in HWCFG0[NPAC]. Each PAC supports up to 128 peripheral slots (see Finding the PDAC slot
number for a peripheral ). You configure the ACP for each peripheral using a set of PDAC_W w _ s registers (see Peripheral ACP
evaluation registers ).
Peripheral access control also enable a hardware semaphore to be included in the access control policy evaluation for peripherals
shared by multiple domains. See Hardware semaphores and dynamic access rights for more details.
For a full explanation of the peripheral ACP evaluation process, see Peripheral ACP evaluation process .

---

*Page 404*

Extended Resource Domain Controller (XRDC)

#### 18.5.3 Transaction protection

During application execution, high-level chip modules such as cores or DMA, Ethernet, known as initiators, initiate transaction
requests to memory and peripheral resources. XRDC adds protection capabilities to ensure the requesting initiator accesses only
the resources that it is authorized to access. These capabilities support security and safety requirements.
XRDC provides this protection by adding two steps to the unprotected transaction flow, as shown in XRDC transaction flow .
XRDC transaction processing differs depending on:
• The type of initiator making the request (see Transaction request sources ).
• The type of target receiving the request (see Transaction targets ).
NOTE
See chip-specific XRDC information for the domain ID of each initiator on chip.

#### 18.5.4 XRDC transaction flow

Table 67. XRDC transaction flow
Step Operation Performed by Description
1 Transaction Initiator An initiator requests a read or write transaction targeting memory or a
request peripheral.
2 Domain XRDC XRDC MDAC submodule intercepts the request and performs domain
assignment assignment, which adds this metadata to the transaction:
• DID
• Privileged attribute
• Secure attribute
3 Interconnect Chip The chip transmits the domain-assigned transaction request across the
interconnect ( crossbar ).
4 ACP evaluation XRDC XRDC MRC or PAC submodule intercepts the transaction request and
evaluates it against the target's ACP to determine whether the requesting
initiator has sufficient access rights to the target. If it does, the transaction
continues. Otherwise, XRDC generates an access violation error and the
transaction terminates.
5 Transaction Target resource
If the previous step does not generate an access violation error, the target
response
resource processes the transaction request and transmits data (for read
transactions) and transaction status information (for all transactions) back
across the interconnect to the requesting initiator.
XRDC is not involved in the flow of information from the target resource back
to the requesting initiator.

#### 18.5.5 Domain assignment

18.5.5.1 Overview
Domain assignment associates a DID with each transaction request from an initiator. To determine the DID for a
transaction, XRDC evaluates the domain-specific configuration data in the set of MDAC registers ( MDA_W n _ m _DFMT0 or
MDA_W n _ m _DFMT1) associated with the requesting initiator. The exact process depends on the source of the request (see
Transaction request sources ).

---

*Page 405*

Extended Resource Domain Controller (XRDC)
If the value of MDACFG m [NMDAR] is 1, which means a single W w register for a given initiator, the specified domain identifier is
used directly. In case this value is more than 1, which means there are multiple W w registers for a given initiator, MDAC evaluates
the conditional terms to determine a "hit". For all W w hits, their corresponding domain identifiers are logically summed together
(boolean OR). Use cases are typically expected to hit in a single W w . Special care is needed if none of the conditional terms hit in
any Wn evaluation; in this case, the generated DID = 0 and you must be aware of any potential access rights granted for this DID.
The number of MDAC registers can vary for each initiator. See the chip-specific information. MDACFG m [NMDAR] indicates the
number of MDA registers, where m is the initiator number. You need m to locate the registers relevant for domain assignment.
See the chip-specific XRDC information for more on initiator numbers. See:
• Register settings for DFMT0 direct domain assignment transactions
• Register settings for DFMT0 PID-based transactions
• Register settings for DFMT1 direct domain assignment transactions
Domain assignment also assigns the secure and privileged attributes to the transaction.
18.5.5.2 PID-based domain assignment
To provide more flexibility in routing core tasks to chip resources in different access-controlled domains, XRDC supports the use
of a PID. If the core initiator contains a built-in PID register, indicated by HWCFG2[PIDP m ] = 1 , XRDC reflects the core PID value
in PID m [PID], and bit 5 of that field indicates the secure attribute for the transaction request. Otherwise, an application can mimic
PID-based domain assignment by writing a value to that field.
18.5.5.3 Transaction request sources
The domain assignment process for an XRDC-protected transaction request depends on the source of the request.
Table 68. Transaction request sources
Request source Topic Brief description
Core initiator DFMT0 direct domain assignment Direct domain assignment using a DFMT0 master domain assignment
example register.
Core initiator DFMT0 PID-based domain PID-based domain assignment using a DFMT0 master domain
assignment example assignment register.
Core initiator DFMT1 direct domain assignment Direct domain assignment using a DFMT1 master domain assignment
example-single MDA register with single MDA.
18.5.5.4 DFMT0 core domain assignment registers
Table 69. DFMT0 core domain assignment registers
Register Index
MDACFG m m = initiator number. See the chip-specific XRDC information for the available list of initiators with
their IDs.
1
MDA_W w _ m _DFMT0
m = initiator number.
w = word (see MDA register structure ). MDACFG m [NMDAR] indicates the number of MDA registers
(words) per initiator.
PID m m = initiator number.
1. See MDA register structure .

---

*Page 406*

Extended Resource Domain Controller (XRDC)
18.5.5.5 DFMT1 noncore domain assignment registers
Table 70. DFMT1 noncore domain assignment registers
Register Index
MDACFG m m = initiator number. See the chip-specific XRDC information for the available list of initiators and
their IDs.
1
MDA_W w _ m _DFMT1
m = initiator number.
w = word. MDACFG m [NMDAR] indicates the number of MDA registers (words) per initiator.
1. See MDA register structure .
18.5.5.6 MDA register structure
31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
MDA_W n _ m _DFMT0
DFMT 0 0 0
Core initiators VLD LK1 LPE LPID PID PIDM PE DIDS DID
One set per domain
31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
MDA_W n _ m _DFMT1
DFMT 0 0 0 0
Noncore initiators VLD LK1 DIDB SA PA DID
One set per master
31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
MDA_W n _ m _DFMT1
DFMT 0 0
Core initiators VLD LK1 LPE LPID DIDB SA PA DID
One set per domain
Figure 40. MDA register structure
NOTE
This XRDC configuration does not have LPE and LPID fields and should be considered as reserved fields.

#### 18.5.6 ACP evaluation

18.5.6.1 Overview
When XRDC is not enabled, all peripherals and memories allow unrestricted access. XRDC allows you to limit that access to
requests from a particular domain or domains with an application-specified ACP. XRDC intercepts the transaction request and
evaluates it against the target's ACP to determine whether the requesting initiator has sufficient access rights to the target, based
on the:
• Domain ID assignment (DID)
• Privileged attribute
• Secured attribute
XRDC obtains the target resource's domain ACP from the associated Domain Access Control Policy (D d ACP) field (see Domain
ACP specification ) in the appropriate register set:
• Peripheral ACP evaluation registers
• Memory ACP evaluation registers
If ACP evaluation determines that the transaction request has sufficient access rights to the target resource, XRDC allows the
transaction to continue. Otherwise, it terminates the request with an error and captures the address and attribute information in
the appropriate error registers.

---

*Page 407*

Extended Resource Domain Controller (XRDC)
The exact process depends on the target of the request (see Transaction targets ).
XRDC optionally supports the inclusion of a hardware semaphore to dynamically alter the ACP of a memory region or peripheral
(see Hardware semaphores and dynamic access rights ).
18.5.6.2 Transaction targets
The ACP evaluation for an XRDC-protected transaction request depends on the target of the request.
Table 71. Transaction targets
Request target Topic Brief description
Peripheral Peripheral ACP evaluation example Process for a transaction request to a target peripheral that is
within a protected domain, with ACP evaluation configured by
PDAC_W w _ s .
Memory Memory ACP evaluation example Process for a transaction request to a target memory region
that is within a protected domain, with ACP evaluation
configured by MRGD_W w _ r .
18.5.6.3 Peripheral ACP evaluation registers
Table 72. Peripheral ACP evaluation registers
Register Index Brief description
1
PDAC_W0_ s s = peripheral slot Specifies the ACP for an XRDC-protected peripheral, and an optional hardware
semaphore.
1
PDAC_W1_ s s = peripheral slot Enables the set of PDAC registers for the associated peripheral and locks the set.
Typically, you configure the peripheral by writing to the PDAC registers and then
limiting their respective domains from making updates to the D d ACP fields or by
locking the set for all updates.
1. See PDAC register structure .
18.5.6.4 PDAC register structure
31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
0 0
PDAC_W0_ r SE SNUM D7ACP D6ACP D5ACP D4ACP D3ACP D2ACP D1ACP D0ACP
0
PDAC_W1_ r VLD LK2 D15ACP D14ACP D13ACP D12ACP D11ACP D10ACP D9ACP D8ACP
Figure 41. PDAC register structure
18.5.6.5 Memory ACP evaluation registers
When XRDC is enabled (CR[GVLD] = 1), you cannot access any XRDC-protected memory unless you configure at least one set
of memory region descriptors (see MRGD_W w _ r ) that includes the targeted memory.

---

*Page 408*

Extended Resource Domain Controller (XRDC)
Table 73. Memory ACP evaluation registers
Register Index Brief description
MRCFG c c = memory Indicates the number of memory regions per memory controller (NMRGD). Each
controller instance memory region is configured by a set of memory region descriptor registers
(MRGD_W w _ r ) described below.
1
MRGD_W0_ r r = memory region Specifies starting address for the memory region.
1
MRGD_W1_ r r = memory region Specifies ending address for the memory region.
1
MRGD_W2_ r r = memory region Specifies the ACP for each supported domain, and an optional hardware
semaphore.
1
MRGD_W3_ r r = memory region Enables the set of MRGD registers for the associated region and locks the set.
Typically, you define the memory region by writing to the MRGD registers and then
limiting their respective domains from updating the D d ACP fields or by locking the
set for all updates.
1. See MRGD register structure .
18.5.6.6 MRGD register structure
31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
Reserved
MRGD_W0_ r SRTADDR
1
MRGD_W1_ r ENDADDR
0 0
MRGD_W2_ r SE SNUM D7ACP D6ACP D5ACP D4ACP D3ACP D2ACP D1ACP D0ACP
0
MRGD_W3_ r VLD LK2 D15ACP D14ACP D13ACP D12ACP D11ACP D10ACP D9ACP D8ACP
Figure 42. MRGD register structure
18.5.6.7 Access control model
XRDC supports a four-level hierarchical access control model. This model combines the traditional privileged (also known as
supervisor) and user access levels with an additional signal for the secure attribute of each memory reference, as shown in Access
control model levels .
Each level has different access control policies that specify the read and write accessibility for a target. XRDC combines the
privileged and secure attributes with the DID assigned to each system bus transaction to form the hardware basis for the access
control mechanism. You specify the ACP for target resources using the D d ACP fields (see Domain ACP specification ) found in
the configuration registers shown in Peripheral ACP evaluation registers and Memory ACP evaluation registers .
You can dynamically control access to shared memory regions and target peripherals with the optional inclusion of a hardware
semaphore (see Hardware semaphores and dynamic access rights ). If you enable this semaphore for a given address space or
peripheral, XRDC allows writes to the target resource only if the requesting domain owns the semaphore.
For cores that support only the three-state access control model (Secure Privileged, Secure User, Nonsecure User), XRDC forces
the nonsecure output signal from the MDAC submodule to 0 in privileged mode. This change enables precise state transitions
between user and privileged modes. Specifically, the MDAC logic for initiator m generates the nonsecure attribute output signal
as a function of the Three-State Model (PID m [TSM]) and PID Present (HWCFG2[PIDP m ]) fields, as shown in Generation of
secure attribute ).

---

*Page 409*

Extended Resource Domain Controller (XRDC)
18.5.6.7.1 Access control model levels
Table 74. Access control model levels
Secure Nonsecure Privileged Not privileged Level
(supervisor) (user)
Yes Not applicable Yes Not applicable Most restricted
Yes Not applicable Not applicable Yes More restricted
Not applicable Yes Yes Not applicable Less restricted
Not applicable Yes Not applicable Yes Least restricted
18.5.6.7.2 Generation of secure attribute
This table shows how XRDC generates the secure attribute for a transaction.
Table 75. Generation of secure attribute
2
Access PID m [TSM] PID present Secure attribute determined by
1
levels (b)
4 0 No Initiator secure attribute
4 0 Yes Initiator secure attribute && ~initiator privileged attribute
3 1 No PID m [5] && ~initiator privileged attribute
3 1 Yes Local secure attribute && ~initiator privileged attribute
1. XRDC assumes a core initiator supports the four-level access model. If a core supports only the three-state access control
model, you must write 1 to PID m [TSM] before loading any nonsecure value into the PID.
2. Indicated in HWCFG2–3[PIDP m ].
18.5.6.8 Domain ACP specification
Table 76. Domain ACP specification
Allowable accesses
D d ACP
Secure Secure Nonsecure Nonsecure
Privileged User Privileged User
111b R, W R, W R, W R, W
110b R, W R, W R, W None
101b R, W R, W R R
100b R, W R, W R None
011b R, W R, W None None
010b R, W None None None
001b R R None None
000b None None None None

---

*Page 410*

Extended Resource Domain Controller (XRDC)
18.5.6.9 Memory region ACP evaluation
During ACP evaluation of a memory transaction request, if the target memory location falls within the address range specified
by any XRDC memory region descriptor, then XRDC identifies the request as a memory region hit. For each memory region
hit, XRDC compares the DID, privileged attribute, and secured attribute of the transaction to the associated domain ACP
(MRGD_W{2,3}_ r [D d ACP]) in the memory region descriptor. See ACP evaluation for additional details on this function.
The following conditions cause XRDC to report an access error:
• The target memory location does not fall within any defined memory regions; in other words, the transaction request is not
a hit.
• The transaction request does not have the appropriate access permissions for the region, which triggers a domain
violation.
• The transaction request hits multiple (overlapping) regions, and all of those regions signal access violations.
Unimplemented domain identifiers default to no access privileges, and therefore the access type of a D d ACP field for an
unimplemented domain is read-as-zero/writes-ignored (RAZ/WI).
18.5.6.10 Hardware semaphores and dynamic access rights
XRDC memory region descriptors and peripheral access control support an optional hardware semaphore in their access
evaluations. This hardware semaphore allows hardware enforcement of dynamic access rights, based on the state of the
semaphore for shared memory regions or shared peripherals.
If enabled, the state of the semaphore dynamically modifies the access control policies so that only the domain owning it has write
access to the resource. The write permissions for all other domains are revoked based on the semaphore state. If no domain owns
the semaphore, the PAC and MRC submodules evaluate DdACP normally.
If you enable a hardware semaphore (by writing 1 to MRGD_W2_ r [SE] or PDAC_W0_ r [SE]) then, before the normal DdACP
evaluation, XRDC checks the state of the hardware semaphore specified in MRGD_W2_ r [SNUM] or PDAC_W0_ r [SNUM].
On a write transaction, if the semaphore is not idle (the semaphore state is non-zero) and the requesting domain does not own the
semaphore, the memory or peripheral access terminates with an error. In other words, writes into a semaphore-enabled address
space or peripheral are allowed only if the semaphore is idle or the requesting domain owns the semaphore.

#### 18.5.7 XRDC transaction examples

To see the complete transaction process for an XRDC-protected transaction request from an initiator to target:
1. Follow one of these examples of domain assignment:
• DFMT0 direct domain assignment example
• DFMT0 PID-based domain assignment example
• DFMT1 direct domain assignment example-single MDA
2. Then follow one of these examples of ACP evaluation:
• Peripheral ACP evaluation example
• Memory ACP evaluation example
18.5.7.1 DFMT0 direct domain assignment example
This configuration assigns a specific domain to all incoming transactions.
• A core initiator has initiator number = 6.
• The core has one MDAC register (MDACFG6[NMDAR] = 1), configured as shown in Register settings for DFMT0 direct
domain assignment transactions .
MDAC_W w _ m _DFMT0 registers do not include fields for secure and privileged attributes. Those attributes are part of the
transaction data from core initiators and are forwarded with the transaction after domain assignment.

---

*Page 411*

Extended Resource Domain Controller (XRDC)
18.5.7.1.1 Register settings for DFMT0 direct domain assignment transactions
Table 77. Register settings for DFMT0 direct domain assignment transactions
Field MDA_W0_6_DFMT0 Comments
VLD 1 Enables this register for use in domain assignment.
LK1 1 Locks the settings in this register until the next module reset.
DFMT 0 Indicates that this is a DFMT0 register.
PID 00_0000b Not used because PE = 00b.
PIDM 00_0000b Not used because PE = 00b.
PE 00b Disables PID-based filtering.
DIDS 00b All incoming transactions are to be assigned to the domain specified by the DID
field.
DID 01b Because PE = 00b and DIDS = 00b, all incoming transactions are to be assigned
DID value 01b.
18.5.7.1.2 DFMT0 direct domain assignment process
The application configures XRDC as it boots. After application boot completes, a core issues a read request to a target peripheral.
1. XRDC intercepts the request and performs domain assignment using the configuration in MDA_W0_6_DFMT0. In this
example, XRDC assigns DID = 01b to all incoming transactions.
2. The transaction proceeds with DID = 01b and the privileged and secure attributes provided by the core.
18.5.7.2 DFMT0 PID-based domain assignment example
This example configuration demonstrates PID-based domain assignment:
• A core initiator with initiator number = 4 processes both safety-critical tasks and routine tasks, using two domains:
— Domain 0 is reserved for safety-critical tasks (PID = 0–15).
— Domain 1 is reserved for routine tasks (PID > 15).
• The core has eight MDAC registers (MDACFG4[NMDAR] = 8), configured as follows (see Register settings for DFMT0
PID-based transactions ):
— MDA_W0_4_DFMT0 assigns safety-critical tasks (PID = 0–15) to domain 0.
— MDA_W1_4_DFMT0 assigns routine tasks (PID = 16–31) to domain 1.
— MDA_W2_4_DFMT0 assigns routine tasks (PID = 32–63) to domain 1.
— MDA_W3_4_DFMT0 through MDA_W7_4_DFMT0 are not used.
MDAC_W w _ m _DFMT0 registers do not include fields for secure and privileged attributes. Those attributes are part of the
transaction data from core initiators and are forwarded with the transaction after domain assignment.

---

*Page 412*

Extended Resource Domain Controller (XRDC)
18.5.7.2.1 Register settings for DFMT0 PID-based transactions
Table 78. Register settings for PID-based transactions
Registers
Field
MDA_W0_4_D MDA_W1_4_D MDA_W2_4_D MDA_W[3– Comments
FMT0 FMT0 FMT0 7]_4_DFMT0
VLD 1 1 1 0 Enables this register for use when assigning
domains.
LK1 1 1 1 1 Locks the settings in each register until the next
device reset.
DFMT 0 0 0 0 Indicates that these registers apply to domain
assignment for core initiators.
PID 00_0000b 01_0000b 10_0000b — Constant match value to be used for PID-based
filtering.
PIDM 00_1111b 10_1111b 01_1111b — Each 0 bit causes the corresponding PID bit to
be considered in domain assignment.
PE 10b 10b 10b — Specifies the type of pattern matching used for
PID evaluation.
DIDS 00b 00b 00b — Assign all incoming transactions with PIDs that
pass the filtering criteria to the domain specified
by the DID field.
DID 00b 01b 01b — This DID value is assigned to incoming
transactions with PIDs that pass the filtering
criteria.
18.5.7.2.2 DFMT0 PID-based domain assignment process
The application configures XRDC as it boots. After booting completes, a core issues a read request to a target peripheral. The
task making the request has PID = 6, indicating that it is a safety-critical task.
1. XRDC intercepts the request and performs domain assignment using each enabled MDA_W n _4_DFMT0 register,
regardless of whether it has already found a match. In this example, XRDC performs the domain assignments as shown
in DFMT0 PID-based domain assignment evaluation .
2. After XRDC completes all domain assignment evaluations, it logically ORs the assigned DIDs to determine the final DID
assigned to the transaction. In this example, only one evaluation results in a DID assignment, so there is no OR operation.
3. The transaction proceeds with DID 00b and the privilege and secure attributes provided by the core.
18.5.7.2.3 DFMT0 PID-based domain assignment evaluation
Table 79. DFMT0 PID-based domain assignment evaluation
Register Evaluation steps Boolean math Result
MDA_W0_4_DFMT0 1. Bitwise AND of PID with inverted PIDM. 00_0000b & 11_0000b 00_0000b
2. Bitwise AND of transaction PID (PID4[PID]) 000110b & 11_0000b 00_0000b
with inverted PIDM.
3. Compare the results of steps 1 and 2. 00_0000b == 000000b True: Assign DID 0
Table continues on the next page...

---

*Page 413*

Extended Resource Domain Controller (XRDC)
Table 79. DFMT0 PID-based domain assignment evaluation (continued)
Register Evaluation steps Boolean math Result
MDA_W1_4_DFMT0 1. Bitwise AND of PID with inverted PIDM. 01_0000b & 01_0000b 01_0000b
2. Bitwise AND of transaction PID (PID4[PID]) 00_0110b & 01_0000b 00_0000b
with inverted PIDM.
3. Compare the results of steps 1 and 2. 01_0000b == 00_0000 False: No DID
b assignment
MDA_W2_4_DFMT0 1. Bitwise AND of PID with inverted PIDM. 10_0000b & 10_0000b 10_0000b
2. Bitwise AND of transaction PID (PID4[PID]) 00_0110b & 10_0000b 00_0000b
with inverted PIDM.
3. Compare the results of steps 1 and 2. 10_0000b == 00_0000 False: No DID
b assignment
18.5.7.3 DFMT1 direct domain assignment example-single MDA
This configuration assigns a specific domain to all incoming transactions.
• An initiator has initiator number = 6.
• The initiator has one MDAC register (MDACFG6[NMDAR] = 1) as shown in Register settings for DFMT1 direct domain
assignment transactions .
18.5.7.3.1 Register settings for DFMT1 direct domain assignment transactions
Table 80. Register settings for DFMT1 direct domain assignment transactions
Field MDA_W0_6_DFMT1 Comments
VLD 1 Enables this register for use in domain assignment.
LK1 1 Locks the settings in this register until the next chip reset.
DFMT 1 Indicates that this is a DFMT1 register.
DIDB 0b All incoming transactions are to be assigned to the domain that the DID field
specifies.
SA 10b All incoming transactions retain their Secure attribute value.
PA 10b All incoming transactions retain their Privileged attribute value.
DID 01b Because DIDB = 0b, all incoming transactions are to be assigned DID value 01b.
18.5.7.3.2 DFMT1 direct domain assignment process
The application configures XRDC as it boots. After application boot completes, an initiator issues a read request to a
target peripheral.
1. XRDC intercepts the request and assigns a domain using the configuration in MDA_W0_6_DFMT1. In this example, XRDC
assigns DID = 01b to all incoming transactions.
2. The transaction proceeds with DID = 01b and the privileged and secure attributes provided by the initiator.
18.5.7.4 Peripheral ACP evaluation example
This example configuration demonstrates ACP evaluation for a target peripheral.

---

*Page 414*

Extended Resource Domain Controller (XRDC)
• The core initiator must have exclusive access to the ADC0 peripheral for safety-critical tasks in domain 0.
• The core initiator supports 8 domains (HWCFG0[NDID] = 111b).
• ADC occupies PDAC slot 40 in the chip, as defined in an example memory map file (see Finding the PDAC slot number for
a peripheral ).
• Given 8 domains and PDAC slot 40, the PDAC registers associated with ADC0 are PDAC_W0_40 and PDAC_W1_40.
The following sections describe the register configurations for this example.
18.5.7.4.1 Finding the PDAC slot number for a peripheral
This topic shows how to find the PDAC slot number for a peripheral, but it is a generic example. Memory map file organization and
appearance can vary.
To find the PDAC slot number for a peripheral:
1. Open the memory map file attached to this document and view the peripherals page.
2. Locate the peripheral in the "Instance" column.
3. The PDAC slot number for the peripheral is at the intersection of the "PDAC slot number" column and the peripheral
row.
In this figure, for peripheral ADC_0, the PDAC slot number is 40. Therefore, the PDAC registers for ADC0 are PDAC_W w _40.
Figure 43. Finding the PDAC slot number for a peripheral
18.5.7.4.2 Register settings for peripheral ACP evaluation
This table shows the PDAC_W w _40 settings for a safety-critical task assigned to domain 0.
Table 81. Register settings for peripheral ACP evaluation
Register Field Value (b) Comments
SE 0 The hardware semaphore (see the SEMA42 chapter) is disabled.
SNUM (don't care) The hardware semaphore is not used in this example.
D7ACP 000 Domain 7 has no access to the peripheral.
D6ACP 000 Domain 6 has no access to the peripheral.
PDAC_W0_40
D5ACP 000 Domain 5 has no access to the peripheral.
D4ACP 000 Domain 4 has no access to the peripheral.
D3ACP 000 Domain 3 has no access to the peripheral.
D2ACP 000 Domain 2 has no access to the peripheral.
Table continues on the next page...

---

*Page 415*

Extended Resource Domain Controller (XRDC)
Table 81. Register settings for peripheral ACP evaluation (continued)
Register Field Value (b) Comments
D1ACP 000 Domain 1 has no access to the peripheral.
D0ACP 010 Only privileged, secure transactions from domain 0 have access.
VLD 1 Use this register set in domain ACP evaluations.
PDAC_W1_40
LK2 11 Lock the settings in this register until the next device reset.
18.5.7.4.3 Peripheral ACP evaluation process
XRDC performs the following process for ACP evaluation:
1. When the application is running, the core issues a read request to a target peripheral. The task making the request has
PID = 0, indicating that it is a safety-critical task. The transaction request is privileged and secured.
2. Between the chip interconnect and ADC0, XRDC intercepts the request and compares its DID, privileged attribute, and
secured attribute to the configuration in PDAC_W0_40 and PDAC_W1_40.
3. Because the ADC0 D0ACP field is 010b for privileged, secured access, XRDC grants access to the transaction.
4. The transaction proceeds normally without any further intervention from XRDC.
18.5.7.5 Memory ACP evaluation example
This example configuration demonstrates ACP evaluation for a target memory. Following are the desired features:
• The core initiator must have exclusive access to the memory region for safety-critical tasks in domain 0.
• The target memory is the address range 1B00_0000h–1B00_1FFFh, protected by memory controller 0 (MRC0), for
example.
• Access to the entire memory range will be controlled by the memory region descriptor defined by MRGD_W w _0.
• The requested transaction is secure privileged.
With the configuration settings shown in Register settings for memory ACP evaluation , XRDC grants access and the transaction
proceeds normally. There is no further XRDC intervention.
18.5.7.5.1 Register settings for memory ACP evaluation
Table 82. Register settings for memory ACP evaluation
Register Field Value Comments
MRGD_W0_0 SRTADDR 1B00_0000h Starting address of the memory region.
MRGD_W1_0 ENDADDR 1B00_1FFFh Ending address of the memory region.
SE 0 The hardware semaphore (see the SEMA42 chapter) is disabled.
SNUM (don't care) The hardware semaphore is not used in this example.
D7ACP 000b Domain 7 has no access to the peripheral.
MRGD_W2_0 D6ACP 000b Domain 6 has no access to the peripheral.
D5ACP 000b Domain 5 has no access to the peripheral.
D4ACP 000b Domain 4 has no access to the peripheral.
D3ACP 000b Domain 3 has no access to the peripheral.
Table continues on the next page...

---

*Page 416*

Extended Resource Domain Controller (XRDC)
Table 82. Register settings for memory ACP evaluation (continued)
Register Field Value Comments
D2ACP 000b Domain 2 has no access to the peripheral.
D1ACP 000b Domain 1 has no access to the peripheral.
D0ACP 010b Only privileged, secure transactions from domain 0 have access.
MRGD_W3_0 VLD 1 Use this register set in domain ACP evaluations.
LK2 11b Lock the settings in this register until the next device reset.
18.5.7.5.2 Memory ACP evaluation process
In this example, XRDC performs the following process for ACP evaluation:
1. When the application is running, the core issues a read request to address 1B00_0100h. The transaction request is
secure privileged.
2. Between the chip interconnect and the memory, XRDC intercepts the request and compares its DID, memory location of
the address, privileged attribute, and secured attribute to the configuration in MRGD_W0_0, MRGD_W1_0, MRGD_W2_0
and MRGD_W3_0.
3. Because the address 1B00_0100h is in the memory range 1B00_0000h–1B00_1FFFh and its D0ACP field is 010b for
privileged, secured access, XRDC grants access to the transaction.
4. The transaction proceeds normally without any no further intervention from XRDC.

#### 18.5.8 Clocking

This module has no clocking considerations.

#### 18.5.9 Interrupts

This module outputs an interrupt signal which can be connected to interrupt controller. Check chip-specific interrupt assignment
for details. Interrupt is asserted on detection of access violation by any checker, and it remains asserted until DERRLOC registers
are cleared.

#### 18.6 Initialization information

Out of reset, XRDC is disabled (CR[GVLD] = 0), which allows secure privileged startup code to configure the entire
programming model.

#### 18.6.1 Initialization procedure

1. Read the hardware configuration registers to obtain the implemented XRDC hardware capabilities for the chip:
• HWCFG0
• HWCFG1
• HWCFG2
• MDACFG m (one for each supported bus initiator—number indicated in HWCFG0[NMSTR])
• MRCFG c (one for each supported memory controller—number indicated in HWCFG0[NMRC])
2. Use the information retrieved in step 1 and the desired domain architecture to configure:
• Domain assignments (MDA_W w _ m _DFMT0 and MDA_W w _ m _DFMT1)
• Memory region descriptors (MRGD_W w _ n )

---

*Page 417*

Extended Resource Domain Controller (XRDC)
• Peripheral access control (PDAC_W w_s )
Ensure that you enable the necessary registers and register sets using the appropriate VLD fields. Also, you can limit
access to these registers or lock them after you configure them, by using the appropriate LK1 fields.
3. Enable XRDC (write 1 to CR[GVLD]).
XRDC is now fully operational.

#### 18.6.2 Minimize access errors

When you configure and enable XRDC, it begins generating DIDs for transaction requests and evaluating access rights at the
target memory and peripheral resources. Because of the distributed design hierarchy and the pipelined nature of the hardware
system bus fabric, it can take multiple cycles for a generated DID to propagate. Until that happens, XRDC uses the initiator's
default DID. Depending on the programmed ACPs, the default DID might generate an access error response.
If XRDC generates incorrect error responses, you can use the following approaches to minimize or eliminate these extraneous
access errors:
• Minimize the amount of system bus traffic when XRDC is enabled (CR[GVLD] = 1).
• Ensure that all target memory addresses provide sufficient access rights for any default DIDs and for the newly programmed
DIDs. After XRDC is fully operational, as confirmed by a read of HWCFG1, you can remove the permissions for the
default DIDs.
• Try to have the bus initiator that programs and configures XRDC use the same DID, that is, its default DID, for both initialization
and configuration, besides normal system operation. You do this when you define the DID assignments for the system.

#### 18.7 Application information

#### 18.7.1 Master domain assignments

The typical use case for master domain assignments is to include one or more core bus initiators in a single domain, possibly
combined with other noncore bus initiator modules such as DMA. This configuration may be static or may be changed dynamically
to select between a small number of domains. HWCFG0[NDID] indicates the maximum number of supported domains. XRDC also
supports the optional use of PIDs to create multiple classes of cores, each in different domains.
For example, you can group critical tasks—safety-critical, performance-critical, and so on—into one domain and all other tasks
into a second domain. Typically, you assign the DID at initialization, but you can also reconfigure domain assignment while the
application is running.
A core bus initiator typically has multiple MDA_W w _ m _DFMT0 registers associated with it.
A noncore bus initiator typically has a single MDA_W w _ m _DFMT1 register associated with it.
The master domain assignment, memory region descriptor, and peripheral domain access control registers have lock fields that
enable you to limit access to, or to lock, the registers. These actions protect the configuration.

#### 18.7.2 Memory region descriptor management

There are two important concepts to consider for managing the memory region descriptors.
Each MRC c configuration is chip-specific. See the chip-specific XRDC information for the number of implemented memory
region descriptors (MRGD_W w _ n ) in a given MRC c instance, and the specific port numbers associated with the target memories
being monitored.
Second, as detailed in Memory region ACP evaluation , after you enable the XRDC, a memory reference must hit one or more of
the configured regions. Otherwise, the transaction results in an access violation. Two other conditions also result in access errors:
• The access hits a single region descriptor and that region signals a domain violation.
• The access hits multiple (overlapping) regions and all regions signal violations.

---

*Page 418*

Extended Resource Domain Controller (XRDC)
The second condition reflects that XRDC gives priority to permission granting over access denying for overlapping regions. This
approach provides more flexibility to system software in memory region descriptor assignments.

#### 18.7.3 Domain error capture management

18.7.3.1 Domain error capture registers
When an MRC or PAC detects a domain access violation, XRDC captures information about the transaction in the
following registers:
Table 83. Domain error capture registers
Register[field] Index Information
DERRLOC d [MRCINST] d = DID Domain error location for MRC instances, with asserted bits indicating
which MRC instance numbers are reporting an error
DERRLOC d [PACINST] d = DID Domain error location for PAC instances, with asserted bits indicating
which PAC instance numbers are reporting an error
DERR_W0_ i i = instance number Transaction target address
DERR_W1_ i i = instance number Additional information about the transaction
DERR_W3_ i i = instance number Reset and rearm domain error capture for the instance
18.7.3.2 Handling domain access violation errors
When an MRC or PAC instance detects a domain access violation, it reports the error by asserting the associated bit in
DERRLOCd[MRCINST] or [PACINST], and XRDC asserts the error interrupt output. To retrieve information about the error, the
error handler must:
1. Read each DERRLOC d register until it finds a non-zero MRCINST or PACINST value.
The index of the DERRLOC d register is the DID for the domain in which the error occurred.
2. Configure the domain assignment for the master executing the exception handler (MDA_Ww_m_DFMTf[DID]) to assign
the DID that corresponds to the DERRLOC d register index, such that the DERR_Ww_i registers show the error for that
domain.
3. Read HWCFG1[DID] to be sure the error handler is now operating in the correct domain. In other words, make sure
HWCFG1[DID] equals the value written to MDA_Ww_m_DFMTf[DID].
4. Find the number of an MRC or PAC instance reporting an error by parsing DERRLOC d [MRCINST] and
DERRLOC d [PACINST] for an asserted bit.
There may be multiple access violations, across multiple MRC or PAC instances, pending for a given domain. To quickly
find the lowest numbered instance reporting an access violation, execute a "find first one bit" instruction (alternatively known
as "count leading zeroes") on the MRCINST and PACINST fields.
5. Retrieve the error address (DERR_W0_ i [EADDR]).
6. Retrieve the error information (DERR_W1_ i ).
More than one error may have occurred in the MRC or PAC instance, as indicated by the error status
(DERR_W1_ i [EST] = 11b). If more than one error has occurred in the instance, XRDC captures data only for the
first error.
7. Use the error address and information to handle the error (whatever that may require).
8. Reset the DERR_W w _ i registers and rearm error capture (write 1b to DERR_W3_ i [RECR]).
Rearming error capture deasserts the instance bit in DERRLOC d .
9. Repeat steps 1 and 8 for each asserted bit in PACINST and MRCINST until there are no more asserted bits.

---

*Page 419*

Extended Resource Domain Controller (XRDC)
Domain error retrieval illustrates an example error retrieval.
18.7.3.3 Domain error retrieval
DERRLOC d
PACINST MRCINST Instance Register Field
0 0000 0000 0000 0000 0000 MRC 0 DERR_W0_0 EADDR
1 0000 0000 0000 0000 0000 DERR_W1_0 EATR, EDID, EST = 10b, ...
Faulting DID 2 0000 0000 0000 0000 0001 DERR_W3_0 RECR
Faulting DID 3 1000 0000 0000 0000 0110 MRC 1 DERR_W0_1 EADDR
... . . . . . .
DERR_W1_1 EATR, EDID, EST = 11b, ...
14 0000 0000 0000 0000 0000 DERR_W3_1 RECR
15 0000 0000 0000 0000 0000 MCR 2 DERR_W0_2 EADDR
DERR_W1_2 EATR, EDID, EST = 11b, ...
DERR_W3_2 RECR
MRC 3 DERR_W0_3 EADDR
DERR_W1_3 EATR, EDID, ...
DERR_W3_3 RECR
... ...
PAC 2 DERR_W0_18 EADDR
DERR_W1_18 EATR, EDID, ...
DERR_W3_18 RECR
PAC 3 DERR_W0_19 EADDR
DERR_W1_19 EATR, EDID, EST = 11b, ...
DERR_W3_19 RECR
Figure 44. Domain error retrieval

#### 18.8 Memory map and register definitions

#### 18.8.1 Register organization

XRDC registers are partitioned into these groups:
• Basic hardware control and configuration
• Domain errors (including location and details)
• Master domain assignments
• Peripheral domain access controls
• Memory region descriptors

#### 18.8.2 Register access guidelines

The following guidelines apply to XRDC register access:
• You can access the XRDC registers only in secure, privileged access mode.
• Unless stated otherwise, the registers support 8-, 16-, and 32-bit reads, and 32-bit writes.
• Unless stated otherwise, XRDC terminates the following access attempts with an error:
— Accesses in a different access mode
— Unsupported write data size
— Writes to read-only resources

---

*Page 420*

Extended Resource Domain Controller (XRDC)
— Writes to reserved address spaces
• Accesses to these memory map holes return an error:
— Any access to a register that does not exist
— Holes in the 0–F0h and DERR to PID register space
— For MDAC, gaps in the master domain assignment (MDA_W w _ m _DFMT n ) registers
— For MRCs, any gap in the MRGD_W w _ r registers (for example, if there are four memory region descriptors,
attempted access to a fifth descriptor fails)
• Accesses to these memory map holes do not return a bus error:
— MRCs: Memory region descriptors occupy only four words but have an additional four words of address available:
words 4–7
— PDAC: Registers associated with unimplemented PDAC slots
• Read accesses to these memory map holes do not return a bus error:
— Offset F8h and FCh
— Offset 100–13Fh
— Offset 140–14Fh
— Offset 200–23Ch

#### 18.8.3 XRDC register descriptions

18.8.3.1 XRDC memory map
XRDC base address: 4027_8000h
Offset Register Access Reset value
Width
(In bits)
0h Control (CR) 32 RW 0000_008Ah
F0h Hardware Configuration 0 (HWCFG0) 32 R 1202_0502h
F4h Hardware Configuration 1 (HWCFG1) 32 R See section
F8h Hardware Configuration 2 (HWCFG2) 32 R 0000_0000h
100h Master Domain Assignment Configuration (MDACFG0) 8 R 01h
101h Master Domain Assignment Configuration (MDACFG1) 8 R 81h
102h Master Domain Assignment Configuration (MDACFG2) 8 R 81h
103h Master Domain Assignment Configuration (MDACFG3) 8 R 01h
105h Master Domain Assignment Configuration (MDACFG5) 8 R 81h
140h Memory Region Configuration (MRCFG0) 8 R 10h
141h Memory Region Configuration (MRCFG1) 8 R 10h
142h Memory Region Configuration (MRCFG2) 8 R 04h
200h - 208h Domain Error Location (DERRLOC0 - DERRLOC2) 32 R 0000_0000h
Table continues on the next page...

---

*Page 421*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
400h Domain Error Word 0 (DERR_W0_0) 32 R 0000_0000h
404h Domain Error Word 1 (DERR_W1_0) 32 R 0000_0000h
40Ch Domain Error Word 3 (DERR_W3_0) 32 RW 0000_0000h
410h Domain Error Word 0 (DERR_W0_1) 32 R 0000_0000h
414h Domain Error Word 1 (DERR_W1_1) 32 R 0000_0000h
41Ch Domain Error Word 3 (DERR_W3_1) 32 RW 0000_0000h
420h Domain Error Word 0 (DERR_W0_2) 32 R 0000_0000h
424h Domain Error Word 1 (DERR_W1_2) 32 R 0000_0000h
42Ch Domain Error Word 3 (DERR_W3_2) 32 RW 0000_0000h
500h Domain Error Word 0 (DERR_W0_16) 32 R 0000_0000h
504h Domain Error Word 1 (DERR_W1_16) 32 R 0000_0000h
50Ch Domain Error Word 3 (DERR_W3_16) 32 RW 0000_0000h
510h Domain Error Word 0 (DERR_W0_17) 32 R 0000_0000h
514h Domain Error Word 1 (DERR_W1_17) 32 R 0000_0000h
51Ch Domain Error Word 3 (DERR_W3_17) 32 RW 0000_0000h
520h Domain Error Word 0 (DERR_W0_18) 32 R 0000_0000h
524h Domain Error Word 1 (DERR_W1_18) 32 R 0000_0000h
52Ch Domain Error Word 3 (DERR_W3_18) 32 RW 0000_0000h
700h Process Identifier (PID0) 32 RW 0000_0000h
70Ch Process Identifier (PID3) 32 RW 0000_0000h
800h Master Domain Assignment (MDA_W0_0_DFMT0) 32 RW 0000_0000h
820h Master Domain Assignment (MDA_W0_1_DFMT1) 32 RW 2000_0000h
840h Master Domain Assignment (MDA_W0_2_DFMT1) 32 RW 2000_0000h
860h Master Domain Assignment (MDA_W0_3_DFMT0) 32 RW 0000_0000h
8A0h Master Domain Assignment (MDA_W0_5_DFMT1) 32 RW 2000_0000h
1100h Peripheral Domain Access Control Word 0 (PDAC_W0_32) 32 RW 0000_0000h
1104h Peripheral Domain Access Control Word 1 (PDAC_W1_32) 32 RW 0000_0000h
1108h Peripheral Domain Access Control Word 0 (PDAC_W0_33) 32 RW 0000_0000h
110Ch Peripheral Domain Access Control Word 1 (PDAC_W1_33) 32 RW 0000_0000h
1110h Peripheral Domain Access Control Word 0 (PDAC_W0_34) 32 RW 0000_0000h
1114h Peripheral Domain Access Control Word 1 (PDAC_W1_34) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 422*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
1118h Peripheral Domain Access Control Word 0 (PDAC_W0_35) 32 RW 0000_0000h
111Ch Peripheral Domain Access Control Word 1 (PDAC_W1_35) 32 RW 0000_0000h
1120h Peripheral Domain Access Control Word 0 (PDAC_W0_36) 32 RW 0000_0000h
1124h Peripheral Domain Access Control Word 1 (PDAC_W1_36) 32 RW 0000_0000h
1130h Peripheral Domain Access Control Word 0 (PDAC_W0_38) 32 RW 0000_0000h
1134h Peripheral Domain Access Control Word 1 (PDAC_W1_38) 32 RW 0000_0000h
1138h Peripheral Domain Access Control Word 0 (PDAC_W0_39) 32 RW 0000_0000h
113Ch Peripheral Domain Access Control Word 1 (PDAC_W1_39) 32 RW 0000_0000h
1140h Peripheral Domain Access Control Word 0 (PDAC_W0_40) 32 RW 0000_0000h
1144h Peripheral Domain Access Control Word 1 (PDAC_W1_40) 32 RW 0000_0000h
1148h Peripheral Domain Access Control Word 0 (PDAC_W0_41) 32 RW 0000_0000h
114Ch Peripheral Domain Access Control Word 1 (PDAC_W1_41) 32 RW 0000_0000h
1150h Peripheral Domain Access Control Word 0 (PDAC_W0_42) 32 RW 0000_0000h
1154h Peripheral Domain Access Control Word 1 (PDAC_W1_42) 32 RW 0000_0000h
1160h Peripheral Domain Access Control Word 0 (PDAC_W0_44) 32 RW 0000_0000h
1164h Peripheral Domain Access Control Word 1 (PDAC_W1_44) 32 RW 0000_0000h
1168h Peripheral Domain Access Control Word 0 (PDAC_W0_45) 32 RW 0000_0000h
116Ch Peripheral Domain Access Control Word 1 (PDAC_W1_45) 32 RW 0000_0000h
1170h Peripheral Domain Access Control Word 0 (PDAC_W0_46) 32 RW 0000_0000h
1174h Peripheral Domain Access Control Word 1 (PDAC_W1_46) 32 RW 0000_0000h
1178h Peripheral Domain Access Control Word 0 (PDAC_W0_47) 32 RW 0000_0000h
117Ch Peripheral Domain Access Control Word 1 (PDAC_W1_47) 32 RW 0000_0000h
1400h Peripheral Domain Access Control Word 0 (PDAC_W0_128) 32 RW 0000_0000h
1404h Peripheral Domain Access Control Word 1 (PDAC_W1_128) 32 RW 0000_0000h
1408h Peripheral Domain Access Control Word 0 (PDAC_W0_129) 32 RW 0000_0000h
140Ch Peripheral Domain Access Control Word 1 (PDAC_W1_129) 32 RW 0000_0000h
1410h Peripheral Domain Access Control Word 0 (PDAC_W0_130) 32 RW 0000_0000h
1414h Peripheral Domain Access Control Word 1 (PDAC_W1_130) 32 RW 0000_0000h
1418h Peripheral Domain Access Control Word 0 (PDAC_W0_131) 32 RW 0000_0000h
141Ch Peripheral Domain Access Control Word 1 (PDAC_W1_131) 32 RW 0000_0000h
1420h Peripheral Domain Access Control Word 0 (PDAC_W0_132) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 423*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
1424h Peripheral Domain Access Control Word 1 (PDAC_W1_132) 32 RW 0000_0000h
1428h Peripheral Domain Access Control Word 0 (PDAC_W0_133) 32 RW 0000_0000h
142Ch Peripheral Domain Access Control Word 1 (PDAC_W1_133) 32 RW 0000_0000h
1430h Peripheral Domain Access Control Word 0 (PDAC_W0_134) 32 RW 0000_0000h
1434h Peripheral Domain Access Control Word 1 (PDAC_W1_134) 32 RW 0000_0000h
1438h Peripheral Domain Access Control Word 0 (PDAC_W0_135) 32 RW 0000_0000h
143Ch Peripheral Domain Access Control Word 1 (PDAC_W1_135) 32 RW 0000_0000h
1440h Peripheral Domain Access Control Word 0 (PDAC_W0_136) 32 RW 0000_0000h
1444h Peripheral Domain Access Control Word 1 (PDAC_W1_136) 32 RW 0000_0000h
1448h Peripheral Domain Access Control Word 0 (PDAC_W0_137) 32 RW 0000_0000h
144Ch Peripheral Domain Access Control Word 1 (PDAC_W1_137) 32 RW 0000_0000h
1450h Peripheral Domain Access Control Word 0 (PDAC_W0_138) 32 RW 0000_0000h
1454h Peripheral Domain Access Control Word 1 (PDAC_W1_138) 32 RW 0000_0000h
1458h Peripheral Domain Access Control Word 0 (PDAC_W0_139) 32 RW 0000_0000h
145Ch Peripheral Domain Access Control Word 1 (PDAC_W1_139) 32 RW 0000_0000h
1460h Peripheral Domain Access Control Word 0 (PDAC_W0_140) 32 RW 0000_0000h
1464h Peripheral Domain Access Control Word 1 (PDAC_W1_140) 32 RW 0000_0000h
1468h Peripheral Domain Access Control Word 0 (PDAC_W0_141) 32 RW 0000_0000h
146Ch Peripheral Domain Access Control Word 1 (PDAC_W1_141) 32 RW 0000_0000h
1470h Peripheral Domain Access Control Word 0 (PDAC_W0_142) 32 RW 0000_0000h
1474h Peripheral Domain Access Control Word 1 (PDAC_W1_142) 32 RW 0000_0000h
1478h Peripheral Domain Access Control Word 0 (PDAC_W0_143) 32 RW 0000_0000h
147Ch Peripheral Domain Access Control Word 1 (PDAC_W1_143) 32 RW 0000_0000h
1480h Peripheral Domain Access Control Word 0 (PDAC_W0_144) 32 RW 0000_0000h
1484h Peripheral Domain Access Control Word 1 (PDAC_W1_144) 32 RW 0000_0000h
1488h Peripheral Domain Access Control Word 0 (PDAC_W0_145) 32 RW 0000_0000h
148Ch Peripheral Domain Access Control Word 1 (PDAC_W1_145) 32 RW 0000_0000h
1490h Peripheral Domain Access Control Word 0 (PDAC_W0_146) 32 RW 0000_0000h
1494h Peripheral Domain Access Control Word 1 (PDAC_W1_146) 32 RW 0000_0000h
1498h Peripheral Domain Access Control Word 0 (PDAC_W0_147) 32 RW 0000_0000h
149Ch Peripheral Domain Access Control Word 1 (PDAC_W1_147) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 424*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
14A0h Peripheral Domain Access Control Word 0 (PDAC_W0_148) 32 RW 0000_0000h
14A4h Peripheral Domain Access Control Word 1 (PDAC_W1_148) 32 RW 0000_0000h
14A8h Peripheral Domain Access Control Word 0 (PDAC_W0_149) 32 RW 0000_0000h
14ACh Peripheral Domain Access Control Word 1 (PDAC_W1_149) 32 RW 0000_0000h
14B0h Peripheral Domain Access Control Word 0 (PDAC_W0_150) 32 RW 0000_0000h
14B4h Peripheral Domain Access Control Word 1 (PDAC_W1_150) 32 RW 0000_0000h
14B8h Peripheral Domain Access Control Word 0 (PDAC_W0_151) 32 RW 0000_0000h
14BCh Peripheral Domain Access Control Word 1 (PDAC_W1_151) 32 RW 0000_0000h
14C0h Peripheral Domain Access Control Word 0 (PDAC_W0_152) 32 RW 0000_0000h
14C4h Peripheral Domain Access Control Word 1 (PDAC_W1_152) 32 RW 0000_0000h
14C8h Peripheral Domain Access Control Word 0 (PDAC_W0_153) 32 RW 0000_0000h
14CCh Peripheral Domain Access Control Word 1 (PDAC_W1_153) 32 RW 0000_0000h
14D0h Peripheral Domain Access Control Word 0 (PDAC_W0_154) 32 RW 0000_0000h
14D4h Peripheral Domain Access Control Word 1 (PDAC_W1_154) 32 RW 0000_0000h
14D8h Peripheral Domain Access Control Word 0 (PDAC_W0_155) 32 RW 0000_0000h
14DCh Peripheral Domain Access Control Word 1 (PDAC_W1_155) 32 RW 0000_0000h
14E0h Peripheral Domain Access Control Word 0 (PDAC_W0_156) 32 RW 0000_0000h
14E4h Peripheral Domain Access Control Word 1 (PDAC_W1_156) 32 RW 0000_0000h
14E8h Peripheral Domain Access Control Word 0 (PDAC_W0_157) 32 RW 0000_0000h
14ECh Peripheral Domain Access Control Word 1 (PDAC_W1_157) 32 RW 0000_0000h
14F0h Peripheral Domain Access Control Word 0 (PDAC_W0_158) 32 RW 0000_0000h
14F4h Peripheral Domain Access Control Word 1 (PDAC_W1_158) 32 RW 0000_0000h
14F8h Peripheral Domain Access Control Word 0 (PDAC_W0_159) 32 RW 0000_0000h
14FCh Peripheral Domain Access Control Word 1 (PDAC_W1_159) 32 RW 0000_0000h
1500h Peripheral Domain Access Control Word 0 (PDAC_W0_160) 32 RW 0000_0000h
1504h Peripheral Domain Access Control Word 1 (PDAC_W1_160) 32 RW 0000_0000h
1508h Peripheral Domain Access Control Word 0 (PDAC_W0_161) 32 RW 0000_0000h
150Ch Peripheral Domain Access Control Word 1 (PDAC_W1_161) 32 RW 0000_0000h
1510h Peripheral Domain Access Control Word 0 (PDAC_W0_162) 32 RW 0000_0000h
1514h Peripheral Domain Access Control Word 1 (PDAC_W1_162) 32 RW 0000_0000h
1518h Peripheral Domain Access Control Word 0 (PDAC_W0_163) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 425*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
151Ch Peripheral Domain Access Control Word 1 (PDAC_W1_163) 32 RW 0000_0000h
1520h Peripheral Domain Access Control Word 0 (PDAC_W0_164) 32 RW 0000_0000h
1524h Peripheral Domain Access Control Word 1 (PDAC_W1_164) 32 RW 0000_0000h
1528h Peripheral Domain Access Control Word 0 (PDAC_W0_165) 32 RW 0000_0000h
152Ch Peripheral Domain Access Control Word 1 (PDAC_W1_165) 32 RW 0000_0000h
1530h Peripheral Domain Access Control Word 0 (PDAC_W0_166) 32 RW 0000_0000h
1534h Peripheral Domain Access Control Word 1 (PDAC_W1_166) 32 RW 0000_0000h
1538h Peripheral Domain Access Control Word 0 (PDAC_W0_167) 32 RW 0000_0000h
153Ch Peripheral Domain Access Control Word 1 (PDAC_W1_167) 32 RW 0000_0000h
1540h Peripheral Domain Access Control Word 0 (PDAC_W0_168) 32 RW 0000_0000h
1544h Peripheral Domain Access Control Word 1 (PDAC_W1_168) 32 RW 0000_0000h
1548h Peripheral Domain Access Control Word 0 (PDAC_W0_169) 32 RW 0000_0000h
154Ch Peripheral Domain Access Control Word 1 (PDAC_W1_169) 32 RW 0000_0000h
1550h Peripheral Domain Access Control Word 0 (PDAC_W0_170) 32 RW 0000_0000h
1554h Peripheral Domain Access Control Word 1 (PDAC_W1_170) 32 RW 0000_0000h
1558h Peripheral Domain Access Control Word 0 (PDAC_W0_171) 32 RW 0000_0000h
155Ch Peripheral Domain Access Control Word 1 (PDAC_W1_171) 32 RW 0000_0000h
1568h Peripheral Domain Access Control Word 0 (PDAC_W0_173) 32 RW 0000_0000h
156Ch Peripheral Domain Access Control Word 1 (PDAC_W1_173) 32 RW 0000_0000h
1578h Peripheral Domain Access Control Word 0 (PDAC_W0_175) 32 RW 0000_0000h
157Ch Peripheral Domain Access Control Word 1 (PDAC_W1_175) 32 RW 0000_0000h
1588h Peripheral Domain Access Control Word 0 (PDAC_W0_177) 32 RW 0000_0000h
158Ch Peripheral Domain Access Control Word 1 (PDAC_W1_177) 32 RW 0000_0000h
1590h Peripheral Domain Access Control Word 0 (PDAC_W0_178) 32 RW 0000_0000h
1594h Peripheral Domain Access Control Word 1 (PDAC_W1_178) 32 RW 0000_0000h
1598h Peripheral Domain Access Control Word 0 (PDAC_W0_179) 32 RW 0000_0000h
159Ch Peripheral Domain Access Control Word 1 (PDAC_W1_179) 32 RW 0000_0000h
15A0h Peripheral Domain Access Control Word 0 (PDAC_W0_180) 32 RW 0000_0000h
15A4h Peripheral Domain Access Control Word 1 (PDAC_W1_180) 32 RW 0000_0000h
15A8h Peripheral Domain Access Control Word 0 (PDAC_W0_181) 32 RW 0000_0000h
15ACh Peripheral Domain Access Control Word 1 (PDAC_W1_181) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 426*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
15B0h Peripheral Domain Access Control Word 0 (PDAC_W0_182) 32 RW 0000_0000h
15B4h Peripheral Domain Access Control Word 1 (PDAC_W1_182) 32 RW 0000_0000h
15B8h Peripheral Domain Access Control Word 0 (PDAC_W0_183) 32 RW 0000_0000h
15BCh Peripheral Domain Access Control Word 1 (PDAC_W1_183) 32 RW 0000_0000h
15C0h Peripheral Domain Access Control Word 0 (PDAC_W0_184) 32 RW 0000_0000h
15C4h Peripheral Domain Access Control Word 1 (PDAC_W1_184) 32 RW 0000_0000h
15D0h Peripheral Domain Access Control Word 0 (PDAC_W0_186) 32 RW 0000_0000h
15D4h Peripheral Domain Access Control Word 1 (PDAC_W1_186) 32 RW 0000_0000h
15D8h Peripheral Domain Access Control Word 0 (PDAC_W0_187) 32 RW 0000_0000h
15DCh Peripheral Domain Access Control Word 1 (PDAC_W1_187) 32 RW 0000_0000h
15E0h Peripheral Domain Access Control Word 0 (PDAC_W0_188) 32 RW 0000_0000h
15E4h Peripheral Domain Access Control Word 1 (PDAC_W1_188) 32 RW 0000_0000h
15F8h Peripheral Domain Access Control Word 0 (PDAC_W0_191) 32 RW 0000_0000h
15FCh Peripheral Domain Access Control Word 1 (PDAC_W1_191) 32 RW 0000_0000h
1608h Peripheral Domain Access Control Word 0 (PDAC_W0_193) 32 RW 0000_0000h
160Ch Peripheral Domain Access Control Word 1 (PDAC_W1_193) 32 RW 0000_0000h
1610h Peripheral Domain Access Control Word 0 (PDAC_W0_194) 32 RW 0000_0000h
1614h Peripheral Domain Access Control Word 1 (PDAC_W1_194) 32 RW 0000_0000h
1618h Peripheral Domain Access Control Word 0 (PDAC_W0_195) 32 RW 0000_0000h
161Ch Peripheral Domain Access Control Word 1 (PDAC_W1_195) 32 RW 0000_0000h
1620h Peripheral Domain Access Control Word 0 (PDAC_W0_196) 32 RW 0000_0000h
1624h Peripheral Domain Access Control Word 1 (PDAC_W1_196) 32 RW 0000_0000h
1628h Peripheral Domain Access Control Word 0 (PDAC_W0_197) 32 RW 0000_0000h
162Ch Peripheral Domain Access Control Word 1 (PDAC_W1_197) 32 RW 0000_0000h
1630h Peripheral Domain Access Control Word 0 (PDAC_W0_198) 32 RW 0000_0000h
1634h Peripheral Domain Access Control Word 1 (PDAC_W1_198) 32 RW 0000_0000h
1648h Peripheral Domain Access Control Word 0 (PDAC_W0_201) 32 RW 0000_0000h
164Ch Peripheral Domain Access Control Word 1 (PDAC_W1_201) 32 RW 0000_0000h
1650h Peripheral Domain Access Control Word 0 (PDAC_W0_202) 32 RW 0000_0000h
1654h Peripheral Domain Access Control Word 1 (PDAC_W1_202) 32 RW 0000_0000h
1658h Peripheral Domain Access Control Word 0 (PDAC_W0_203) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 427*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
165Ch Peripheral Domain Access Control Word 1 (PDAC_W1_203) 32 RW 0000_0000h
1660h Peripheral Domain Access Control Word 0 (PDAC_W0_204) 32 RW 0000_0000h
1664h Peripheral Domain Access Control Word 1 (PDAC_W1_204) 32 RW 0000_0000h
1668h Peripheral Domain Access Control Word 0 (PDAC_W0_205) 32 RW 0000_0000h
166Ch Peripheral Domain Access Control Word 1 (PDAC_W1_205) 32 RW 0000_0000h
1670h Peripheral Domain Access Control Word 0 (PDAC_W0_206) 32 RW 0000_0000h
1674h Peripheral Domain Access Control Word 1 (PDAC_W1_206) 32 RW 0000_0000h
1678h Peripheral Domain Access Control Word 0 (PDAC_W0_207) 32 RW 0000_0000h
167Ch Peripheral Domain Access Control Word 1 (PDAC_W1_207) 32 RW 0000_0000h
1680h Peripheral Domain Access Control Word 0 (PDAC_W0_208) 32 RW 0000_0000h
1684h Peripheral Domain Access Control Word 1 (PDAC_W1_208) 32 RW 0000_0000h
1688h Peripheral Domain Access Control Word 0 (PDAC_W0_209) 32 RW 0000_0000h
168Ch Peripheral Domain Access Control Word 1 (PDAC_W1_209) 32 RW 0000_0000h
16A0h Peripheral Domain Access Control Word 0 (PDAC_W0_212) 32 RW 0000_0000h
16A4h Peripheral Domain Access Control Word 1 (PDAC_W1_212) 32 RW 0000_0000h
16A8h Peripheral Domain Access Control Word 0 (PDAC_W0_213) 32 RW 0000_0000h
16ACh Peripheral Domain Access Control Word 1 (PDAC_W1_213) 32 RW 0000_0000h
16B0h Peripheral Domain Access Control Word 0 (PDAC_W0_214) 32 RW 0000_0000h
16B4h Peripheral Domain Access Control Word 1 (PDAC_W1_214) 32 RW 0000_0000h
16B8h Peripheral Domain Access Control Word 0 (PDAC_W0_215) 32 RW 0000_0000h
16BCh Peripheral Domain Access Control Word 1 (PDAC_W1_215) 32 RW 0000_0000h
16C0h Peripheral Domain Access Control Word 0 (PDAC_W0_216) 32 RW 0000_0000h
16C4h Peripheral Domain Access Control Word 1 (PDAC_W1_216) 32 RW 0000_0000h
16C8h Peripheral Domain Access Control Word 0 (PDAC_W0_217) 32 RW 0000_0000h
16CCh Peripheral Domain Access Control Word 1 (PDAC_W1_217) 32 RW 0000_0000h
16D8h Peripheral Domain Access Control Word 0 (PDAC_W0_219) 32 RW 0000_0000h
16DCh Peripheral Domain Access Control Word 1 (PDAC_W1_219) 32 RW 0000_0000h
16E0h Peripheral Domain Access Control Word 0 (PDAC_W0_220) 32 RW 0000_0000h
16E4h Peripheral Domain Access Control Word 1 (PDAC_W1_220) 32 RW 0000_0000h
16E8h Peripheral Domain Access Control Word 0 (PDAC_W0_221) 32 RW 0000_0000h
16ECh Peripheral Domain Access Control Word 1 (PDAC_W1_221) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 428*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
16F8h Peripheral Domain Access Control Word 0 (PDAC_W0_223) 32 RW 0000_0000h
16FCh Peripheral Domain Access Control Word 1 (PDAC_W1_223) 32 RW 0000_0000h
1700h Peripheral Domain Access Control Word 0 (PDAC_W0_224) 32 RW 0000_0000h
1704h Peripheral Domain Access Control Word 1 (PDAC_W1_224) 32 RW 0000_0000h
1708h Peripheral Domain Access Control Word 0 (PDAC_W0_225) 32 RW 0000_0000h
170Ch Peripheral Domain Access Control Word 1 (PDAC_W1_225) 32 RW 0000_0000h
1710h Peripheral Domain Access Control Word 0 (PDAC_W0_226) 32 RW 0000_0000h
1714h Peripheral Domain Access Control Word 1 (PDAC_W1_226) 32 RW 0000_0000h
1718h Peripheral Domain Access Control Word 0 (PDAC_W0_227) 32 RW 0000_0000h
171Ch Peripheral Domain Access Control Word 1 (PDAC_W1_227) 32 RW 0000_0000h
1728h Peripheral Domain Access Control Word 0 (PDAC_W0_229) 32 RW 0000_0000h
172Ch Peripheral Domain Access Control Word 1 (PDAC_W1_229) 32 RW 0000_0000h
1738h Peripheral Domain Access Control Word 0 (PDAC_W0_231) 32 RW 0000_0000h
173Ch Peripheral Domain Access Control Word 1 (PDAC_W1_231) 32 RW 0000_0000h
1740h Peripheral Domain Access Control Word 0 (PDAC_W0_232) 32 RW 0000_0000h
1744h Peripheral Domain Access Control Word 1 (PDAC_W1_232) 32 RW 0000_0000h
1760h Peripheral Domain Access Control Word 0 (PDAC_W0_236) 32 RW 0000_0000h
1764h Peripheral Domain Access Control Word 1 (PDAC_W1_236) 32 RW 0000_0000h
1800h Peripheral Domain Access Control Word 0 (PDAC_W0_256) 32 RW 0000_0000h
1804h Peripheral Domain Access Control Word 1 (PDAC_W1_256) 32 RW 0000_0000h
1808h Peripheral Domain Access Control Word 0 (PDAC_W0_257) 32 RW 0000_0000h
180Ch Peripheral Domain Access Control Word 1 (PDAC_W1_257) 32 RW 0000_0000h
1820h Peripheral Domain Access Control Word 0 (PDAC_W0_260) 32 RW 0000_0000h
1824h Peripheral Domain Access Control Word 1 (PDAC_W1_260) 32 RW 0000_0000h
1828h Peripheral Domain Access Control Word 0 (PDAC_W0_261) 32 RW 0000_0000h
182Ch Peripheral Domain Access Control Word 1 (PDAC_W1_261) 32 RW 0000_0000h
1830h Peripheral Domain Access Control Word 0 (PDAC_W0_262) 32 RW 0000_0000h
1834h Peripheral Domain Access Control Word 1 (PDAC_W1_262) 32 RW 0000_0000h
1838h Peripheral Domain Access Control Word 0 (PDAC_W0_263) 32 RW 0000_0000h
183Ch Peripheral Domain Access Control Word 1 (PDAC_W1_263) 32 RW 0000_0000h
1840h Peripheral Domain Access Control Word 0 (PDAC_W0_264) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 429*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
1844h Peripheral Domain Access Control Word 1 (PDAC_W1_264) 32 RW 0000_0000h
1848h Peripheral Domain Access Control Word 0 (PDAC_W0_265) 32 RW 0000_0000h
184Ch Peripheral Domain Access Control Word 1 (PDAC_W1_265) 32 RW 0000_0000h
1850h Peripheral Domain Access Control Word 0 (PDAC_W0_266) 32 RW 0000_0000h
1854h Peripheral Domain Access Control Word 1 (PDAC_W1_266) 32 RW 0000_0000h
1858h Peripheral Domain Access Control Word 0 (PDAC_W0_267) 32 RW 0000_0000h
185Ch Peripheral Domain Access Control Word 1 (PDAC_W1_267) 32 RW 0000_0000h
1860h Peripheral Domain Access Control Word 0 (PDAC_W0_268) 32 RW 0000_0000h
1864h Peripheral Domain Access Control Word 1 (PDAC_W1_268) 32 RW 0000_0000h
1868h Peripheral Domain Access Control Word 0 (PDAC_W0_269) 32 RW 0000_0000h
186Ch Peripheral Domain Access Control Word 1 (PDAC_W1_269) 32 RW 0000_0000h
1870h Peripheral Domain Access Control Word 0 (PDAC_W0_270) 32 RW 0000_0000h
1874h Peripheral Domain Access Control Word 1 (PDAC_W1_270) 32 RW 0000_0000h
1878h Peripheral Domain Access Control Word 0 (PDAC_W0_271) 32 RW 0000_0000h
187Ch Peripheral Domain Access Control Word 1 (PDAC_W1_271) 32 RW 0000_0000h
1880h Peripheral Domain Access Control Word 0 (PDAC_W0_272) 32 RW 0000_0000h
1884h Peripheral Domain Access Control Word 1 (PDAC_W1_272) 32 RW 0000_0000h
1888h Peripheral Domain Access Control Word 0 (PDAC_W0_273) 32 RW 0000_0000h
188Ch Peripheral Domain Access Control Word 1 (PDAC_W1_273) 32 RW 0000_0000h
1890h Peripheral Domain Access Control Word 0 (PDAC_W0_274) 32 RW 0000_0000h
1894h Peripheral Domain Access Control Word 1 (PDAC_W1_274) 32 RW 0000_0000h
1898h Peripheral Domain Access Control Word 0 (PDAC_W0_275) 32 RW 0000_0000h
189Ch Peripheral Domain Access Control Word 1 (PDAC_W1_275) 32 RW 0000_0000h
18A0h Peripheral Domain Access Control Word 0 (PDAC_W0_276) 32 RW 0000_0000h
18A4h Peripheral Domain Access Control Word 1 (PDAC_W1_276) 32 RW 0000_0000h
18A8h Peripheral Domain Access Control Word 0 (PDAC_W0_277) 32 RW 0000_0000h
18ACh Peripheral Domain Access Control Word 1 (PDAC_W1_277) 32 RW 0000_0000h
18B0h Peripheral Domain Access Control Word 0 (PDAC_W0_278) 32 RW 0000_0000h
18B4h Peripheral Domain Access Control Word 1 (PDAC_W1_278) 32 RW 0000_0000h
18B8h Peripheral Domain Access Control Word 0 (PDAC_W0_279) 32 RW 0000_0000h
18BCh Peripheral Domain Access Control Word 1 (PDAC_W1_279) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 430*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
18C0h Peripheral Domain Access Control Word 0 (PDAC_W0_280) 32 RW 0000_0000h
18C4h Peripheral Domain Access Control Word 1 (PDAC_W1_280) 32 RW 0000_0000h
18C8h Peripheral Domain Access Control Word 0 (PDAC_W0_281) 32 RW 0000_0000h
18CCh Peripheral Domain Access Control Word 1 (PDAC_W1_281) 32 RW 0000_0000h
18D8h Peripheral Domain Access Control Word 0 (PDAC_W0_283) 32 RW 0000_0000h
18DCh Peripheral Domain Access Control Word 1 (PDAC_W1_283) 32 RW 0000_0000h
18E8h Peripheral Domain Access Control Word 0 (PDAC_W0_285) 32 RW 0000_0000h
18ECh Peripheral Domain Access Control Word 1 (PDAC_W1_285) 32 RW 0000_0000h
1900h Peripheral Domain Access Control Word 0 (PDAC_W0_288) 32 RW 0000_0000h
1904h Peripheral Domain Access Control Word 1 (PDAC_W1_288) 32 RW 0000_0000h
1918h Peripheral Domain Access Control Word 0 (PDAC_W0_291) 32 RW 0000_0000h
191Ch Peripheral Domain Access Control Word 1 (PDAC_W1_291) 32 RW 0000_0000h
1920h Peripheral Domain Access Control Word 0 (PDAC_W0_292) 32 RW 0000_0000h
1924h Peripheral Domain Access Control Word 1 (PDAC_W1_292) 32 RW 0000_0000h
1928h Peripheral Domain Access Control Word 0 (PDAC_W0_293) 32 RW 0000_0000h
192Ch Peripheral Domain Access Control Word 1 (PDAC_W1_293) 32 RW 0000_0000h
1930h Peripheral Domain Access Control Word 0 (PDAC_W0_294) 32 RW 0000_0000h
1934h Peripheral Domain Access Control Word 1 (PDAC_W1_294) 32 RW 0000_0000h
1938h Peripheral Domain Access Control Word 0 (PDAC_W0_295) 32 RW 0000_0000h
193Ch Peripheral Domain Access Control Word 1 (PDAC_W1_295) 32 RW 0000_0000h
1940h Peripheral Domain Access Control Word 0 (PDAC_W0_296) 32 RW 0000_0000h
1944h Peripheral Domain Access Control Word 1 (PDAC_W1_296) 32 RW 0000_0000h
1948h Peripheral Domain Access Control Word 0 (PDAC_W0_297) 32 RW 0000_0000h
194Ch Peripheral Domain Access Control Word 1 (PDAC_W1_297) 32 RW 0000_0000h
1950h Peripheral Domain Access Control Word 0 (PDAC_W0_298) 32 RW 0000_0000h
1954h Peripheral Domain Access Control Word 1 (PDAC_W1_298) 32 RW 0000_0000h
1978h Peripheral Domain Access Control Word 0 (PDAC_W0_303) 32 RW 0000_0000h
197Ch Peripheral Domain Access Control Word 1 (PDAC_W1_303) 32 RW 0000_0000h
1980h Peripheral Domain Access Control Word 0 (PDAC_W0_304) 32 RW 0000_0000h
1984h Peripheral Domain Access Control Word 1 (PDAC_W1_304) 32 RW 0000_0000h
1998h Peripheral Domain Access Control Word 0 (PDAC_W0_307) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 431*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
199Ch Peripheral Domain Access Control Word 1 (PDAC_W1_307) 32 RW 0000_0000h
19B8h Peripheral Domain Access Control Word 0 (PDAC_W0_311) 32 RW 0000_0000h
19BCh Peripheral Domain Access Control Word 1 (PDAC_W1_311) 32 RW 0000_0000h
19D0h Peripheral Domain Access Control Word 0 (PDAC_W0_314) 32 RW 0000_0000h
19D4h Peripheral Domain Access Control Word 1 (PDAC_W1_314) 32 RW 0000_0000h
19D8h Peripheral Domain Access Control Word 0 (PDAC_W0_315) 32 RW 0000_0000h
19DCh Peripheral Domain Access Control Word 1 (PDAC_W1_315) 32 RW 0000_0000h
2000h Memory Region Descriptor Word 0 (MRGD_W0_0) 32 RW 0000_0001h
2004h Memory Region Descriptor Word 1 (MRGD_W1_0) 32 RW 0000_001Fh
2008h Memory Region Descriptor Word 2 (MRGD_W2_0) 32 RW 0000_0000h
200Ch Memory Region Descriptor Word 3 (MRGD_W3_0) 32 RW 0000_0000h
2020h Memory Region Descriptor Word 0 (MRGD_W0_1) 32 RW 0000_0001h
2024h Memory Region Descriptor Word 1 (MRGD_W1_1) 32 RW 0000_001Fh
2028h Memory Region Descriptor Word 2 (MRGD_W2_1) 32 RW 0000_0000h
202Ch Memory Region Descriptor Word 3 (MRGD_W3_1) 32 RW 0000_0000h
2040h Memory Region Descriptor Word 0 (MRGD_W0_2) 32 RW 0000_0001h
2044h Memory Region Descriptor Word 1 (MRGD_W1_2) 32 RW 0000_001Fh
2048h Memory Region Descriptor Word 2 (MRGD_W2_2) 32 RW 0000_0000h
204Ch Memory Region Descriptor Word 3 (MRGD_W3_2) 32 RW 0000_0000h
2060h Memory Region Descriptor Word 0 (MRGD_W0_3) 32 RW 0000_0001h
2064h Memory Region Descriptor Word 1 (MRGD_W1_3) 32 RW 0000_001Fh
2068h Memory Region Descriptor Word 2 (MRGD_W2_3) 32 RW 0000_0000h
206Ch Memory Region Descriptor Word 3 (MRGD_W3_3) 32 RW 0000_0000h
2080h Memory Region Descriptor Word 0 (MRGD_W0_4) 32 RW 0000_0001h
2084h Memory Region Descriptor Word 1 (MRGD_W1_4) 32 RW 0000_001Fh
2088h Memory Region Descriptor Word 2 (MRGD_W2_4) 32 RW 0000_0000h
208Ch Memory Region Descriptor Word 3 (MRGD_W3_4) 32 RW 0000_0000h
20A0h Memory Region Descriptor Word 0 (MRGD_W0_5) 32 RW 0000_0001h
20A4h Memory Region Descriptor Word 1 (MRGD_W1_5) 32 RW 0000_001Fh
20A8h Memory Region Descriptor Word 2 (MRGD_W2_5) 32 RW 0000_0000h
20ACh Memory Region Descriptor Word 3 (MRGD_W3_5) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 432*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
20C0h Memory Region Descriptor Word 0 (MRGD_W0_6) 32 RW 0000_0001h
20C4h Memory Region Descriptor Word 1 (MRGD_W1_6) 32 RW 0000_001Fh
20C8h Memory Region Descriptor Word 2 (MRGD_W2_6) 32 RW 0000_0000h
20CCh Memory Region Descriptor Word 3 (MRGD_W3_6) 32 RW 0000_0000h
20E0h Memory Region Descriptor Word 0 (MRGD_W0_7) 32 RW 0000_0001h
20E4h Memory Region Descriptor Word 1 (MRGD_W1_7) 32 RW 0000_001Fh
20E8h Memory Region Descriptor Word 2 (MRGD_W2_7) 32 RW 0000_0000h
20ECh Memory Region Descriptor Word 3 (MRGD_W3_7) 32 RW 0000_0000h
2100h Memory Region Descriptor Word 0 (MRGD_W0_8) 32 RW 0000_0001h
2104h Memory Region Descriptor Word 1 (MRGD_W1_8) 32 RW 0000_001Fh
2108h Memory Region Descriptor Word 2 (MRGD_W2_8) 32 RW 0000_0000h
210Ch Memory Region Descriptor Word 3 (MRGD_W3_8) 32 RW 0000_0000h
2120h Memory Region Descriptor Word 0 (MRGD_W0_9) 32 RW 0000_0001h
2124h Memory Region Descriptor Word 1 (MRGD_W1_9) 32 RW 0000_001Fh
2128h Memory Region Descriptor Word 2 (MRGD_W2_9) 32 RW 0000_0000h
212Ch Memory Region Descriptor Word 3 (MRGD_W3_9) 32 RW 0000_0000h
2140h Memory Region Descriptor Word 0 (MRGD_W0_10) 32 RW 0000_0001h
2144h Memory Region Descriptor Word 1 (MRGD_W1_10) 32 RW 0000_001Fh
2148h Memory Region Descriptor Word 2 (MRGD_W2_10) 32 RW 0000_0000h
214Ch Memory Region Descriptor Word 3 (MRGD_W3_10) 32 RW 0000_0000h
2160h Memory Region Descriptor Word 0 (MRGD_W0_11) 32 RW 0000_0001h
2164h Memory Region Descriptor Word 1 (MRGD_W1_11) 32 RW 0000_001Fh
2168h Memory Region Descriptor Word 2 (MRGD_W2_11) 32 RW 0000_0000h
216Ch Memory Region Descriptor Word 3 (MRGD_W3_11) 32 RW 0000_0000h
2180h Memory Region Descriptor Word 0 (MRGD_W0_12) 32 RW 0000_0001h
2184h Memory Region Descriptor Word 1 (MRGD_W1_12) 32 RW 0000_001Fh
2188h Memory Region Descriptor Word 2 (MRGD_W2_12) 32 RW 0000_0000h
218Ch Memory Region Descriptor Word 3 (MRGD_W3_12) 32 RW 0000_0000h
21A0h Memory Region Descriptor Word 0 (MRGD_W0_13) 32 RW 0000_0001h
21A4h Memory Region Descriptor Word 1 (MRGD_W1_13) 32 RW 0000_001Fh
21A8h Memory Region Descriptor Word 2 (MRGD_W2_13) 32 RW 0000_0000h
Table continues on the next page...

---

*Page 433*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
21ACh Memory Region Descriptor Word 3 (MRGD_W3_13) 32 RW 0000_0000h
21C0h Memory Region Descriptor Word 0 (MRGD_W0_14) 32 RW 0000_0001h
21C4h Memory Region Descriptor Word 1 (MRGD_W1_14) 32 RW 0000_001Fh
21C8h Memory Region Descriptor Word 2 (MRGD_W2_14) 32 RW 0000_0000h
21CCh Memory Region Descriptor Word 3 (MRGD_W3_14) 32 RW 0000_0000h
21E0h Memory Region Descriptor Word 0 (MRGD_W0_15) 32 RW 0000_0001h
21E4h Memory Region Descriptor Word 1 (MRGD_W1_15) 32 RW 0000_001Fh
21E8h Memory Region Descriptor Word 2 (MRGD_W2_15) 32 RW 0000_0000h
21ECh Memory Region Descriptor Word 3 (MRGD_W3_15) 32 RW 0000_0000h
2200h Memory Region Descriptor Word 0 (MRGD_W0_16) 32 RW 0000_0001h
2204h Memory Region Descriptor Word 1 (MRGD_W1_16) 32 RW 0000_001Fh
2208h Memory Region Descriptor Word 2 (MRGD_W2_16) 32 RW 0000_0000h
220Ch Memory Region Descriptor Word 3 (MRGD_W3_16) 32 RW 0000_0000h
2220h Memory Region Descriptor Word 0 (MRGD_W0_17) 32 RW 0000_0001h
2224h Memory Region Descriptor Word 1 (MRGD_W1_17) 32 RW 0000_001Fh
2228h Memory Region Descriptor Word 2 (MRGD_W2_17) 32 RW 0000_0000h
222Ch Memory Region Descriptor Word 3 (MRGD_W3_17) 32 RW 0000_0000h
2240h Memory Region Descriptor Word 0 (MRGD_W0_18) 32 RW 0000_0001h
2244h Memory Region Descriptor Word 1 (MRGD_W1_18) 32 RW 0000_001Fh
2248h Memory Region Descriptor Word 2 (MRGD_W2_18) 32 RW 0000_0000h
224Ch Memory Region Descriptor Word 3 (MRGD_W3_18) 32 RW 0000_0000h
2260h Memory Region Descriptor Word 0 (MRGD_W0_19) 32 RW 0000_0001h
2264h Memory Region Descriptor Word 1 (MRGD_W1_19) 32 RW 0000_001Fh
2268h Memory Region Descriptor Word 2 (MRGD_W2_19) 32 RW 0000_0000h
226Ch Memory Region Descriptor Word 3 (MRGD_W3_19) 32 RW 0000_0000h
2280h Memory Region Descriptor Word 0 (MRGD_W0_20) 32 RW 0000_0001h
2284h Memory Region Descriptor Word 1 (MRGD_W1_20) 32 RW 0000_001Fh
2288h Memory Region Descriptor Word 2 (MRGD_W2_20) 32 RW 0000_0000h
228Ch Memory Region Descriptor Word 3 (MRGD_W3_20) 32 RW 0000_0000h
22A0h Memory Region Descriptor Word 0 (MRGD_W0_21) 32 RW 0000_0001h
22A4h Memory Region Descriptor Word 1 (MRGD_W1_21) 32 RW 0000_001Fh
Table continues on the next page...

---

*Page 434*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
22A8h Memory Region Descriptor Word 2 (MRGD_W2_21) 32 RW 0000_0000h
22ACh Memory Region Descriptor Word 3 (MRGD_W3_21) 32 RW 0000_0000h
22C0h Memory Region Descriptor Word 0 (MRGD_W0_22) 32 RW 0000_0001h
22C4h Memory Region Descriptor Word 1 (MRGD_W1_22) 32 RW 0000_001Fh
22C8h Memory Region Descriptor Word 2 (MRGD_W2_22) 32 RW 0000_0000h
22CCh Memory Region Descriptor Word 3 (MRGD_W3_22) 32 RW 0000_0000h
22E0h Memory Region Descriptor Word 0 (MRGD_W0_23) 32 RW 0000_0001h
22E4h Memory Region Descriptor Word 1 (MRGD_W1_23) 32 RW 0000_001Fh
22E8h Memory Region Descriptor Word 2 (MRGD_W2_23) 32 RW 0000_0000h
22ECh Memory Region Descriptor Word 3 (MRGD_W3_23) 32 RW 0000_0000h
2300h Memory Region Descriptor Word 0 (MRGD_W0_24) 32 RW 0000_0001h
2304h Memory Region Descriptor Word 1 (MRGD_W1_24) 32 RW 0000_001Fh
2308h Memory Region Descriptor Word 2 (MRGD_W2_24) 32 RW 0000_0000h
230Ch Memory Region Descriptor Word 3 (MRGD_W3_24) 32 RW 0000_0000h
2320h Memory Region Descriptor Word 0 (MRGD_W0_25) 32 RW 0000_0001h
2324h Memory Region Descriptor Word 1 (MRGD_W1_25) 32 RW 0000_001Fh
2328h Memory Region Descriptor Word 2 (MRGD_W2_25) 32 RW 0000_0000h
232Ch Memory Region Descriptor Word 3 (MRGD_W3_25) 32 RW 0000_0000h
2340h Memory Region Descriptor Word 0 (MRGD_W0_26) 32 RW 0000_0001h
2344h Memory Region Descriptor Word 1 (MRGD_W1_26) 32 RW 0000_001Fh
2348h Memory Region Descriptor Word 2 (MRGD_W2_26) 32 RW 0000_0000h
234Ch Memory Region Descriptor Word 3 (MRGD_W3_26) 32 RW 0000_0000h
2360h Memory Region Descriptor Word 0 (MRGD_W0_27) 32 RW 0000_0001h
2364h Memory Region Descriptor Word 1 (MRGD_W1_27) 32 RW 0000_001Fh
2368h Memory Region Descriptor Word 2 (MRGD_W2_27) 32 RW 0000_0000h
236Ch Memory Region Descriptor Word 3 (MRGD_W3_27) 32 RW 0000_0000h
2380h Memory Region Descriptor Word 0 (MRGD_W0_28) 32 RW 0000_0001h
2384h Memory Region Descriptor Word 1 (MRGD_W1_28) 32 RW 0000_001Fh
2388h Memory Region Descriptor Word 2 (MRGD_W2_28) 32 RW 0000_0000h
238Ch Memory Region Descriptor Word 3 (MRGD_W3_28) 32 RW 0000_0000h
23A0h Memory Region Descriptor Word 0 (MRGD_W0_29) 32 RW 0000_0001h
Table continues on the next page...

---

*Page 435*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
23A4h Memory Region Descriptor Word 1 (MRGD_W1_29) 32 RW 0000_001Fh
23A8h Memory Region Descriptor Word 2 (MRGD_W2_29) 32 RW 0000_0000h
23ACh Memory Region Descriptor Word 3 (MRGD_W3_29) 32 RW 0000_0000h
23C0h Memory Region Descriptor Word 0 (MRGD_W0_30) 32 RW 0000_0001h
23C4h Memory Region Descriptor Word 1 (MRGD_W1_30) 32 RW 0000_001Fh
23C8h Memory Region Descriptor Word 2 (MRGD_W2_30) 32 RW 0000_0000h
23CCh Memory Region Descriptor Word 3 (MRGD_W3_30) 32 RW 0000_0000h
23E0h Memory Region Descriptor Word 0 (MRGD_W0_31) 32 RW 0000_0001h
23E4h Memory Region Descriptor Word 1 (MRGD_W1_31) 32 RW 0000_001Fh
23E8h Memory Region Descriptor Word 2 (MRGD_W2_31) 32 RW 0000_0000h
23ECh Memory Region Descriptor Word 3 (MRGD_W3_31) 32 RW 0000_0000h
2400h Memory Region Descriptor Word 0 (MRGD_W0_32) 32 RW 0000_0001h
2404h Memory Region Descriptor Word 1 (MRGD_W1_32) 32 RW 0000_001Fh
2408h Memory Region Descriptor Word 2 (MRGD_W2_32) 32 RW 0000_0000h
240Ch Memory Region Descriptor Word 3 (MRGD_W3_32) 32 RW 0000_0000h
2420h Memory Region Descriptor Word 0 (MRGD_W0_33) 32 RW 0000_0001h
2424h Memory Region Descriptor Word 1 (MRGD_W1_33) 32 RW 0000_001Fh
2428h Memory Region Descriptor Word 2 (MRGD_W2_33) 32 RW 0000_0000h
242Ch Memory Region Descriptor Word 3 (MRGD_W3_33) 32 RW 0000_0000h
2440h Memory Region Descriptor Word 0 (MRGD_W0_34) 32 RW 0000_0001h
2444h Memory Region Descriptor Word 1 (MRGD_W1_34) 32 RW 0000_001Fh
2448h Memory Region Descriptor Word 2 (MRGD_W2_34) 32 RW 0000_0000h
244Ch Memory Region Descriptor Word 3 (MRGD_W3_34) 32 RW 0000_0000h
2460h Memory Region Descriptor Word 0 (MRGD_W0_35) 32 RW 0000_0001h
2464h Memory Region Descriptor Word 1 (MRGD_W1_35) 32 RW 0000_001Fh
2468h Memory Region Descriptor Word 2 (MRGD_W2_35) 32 RW 0000_0000h
246Ch Memory Region Descriptor Word 3 (MRGD_W3_35) 32 RW 0000_0000h

---

*Page 436*

Extended Resource Domain Controller (XRDC)
18.8.3.2 Control (CR)
Offset
Register Offset
CR 0h
Function
Provides XRDC status and enables XRDC operation.
Access: Secure privileged read/write
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
LK1
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 VAW MRF 0 HRL
GVLD
W
Reset 0 0 0 0 0 0 0 0 1 0 0 0 1 0 1 0
Fields
Field Function
Reserved
31
—
30 Lock
LK1 Prohibits writes to this register.
• If unlocked, this register accepts any secure privileged write.
• If locked, you cannot write to this register and it remains read-only until after the next reset.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Unlocked
1b - Locks
When writing
Table continues on the next page...

---

*Page 437*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Field Function
0b - No effect
1b - Locks
Reserved
29-9
—
8 Virtualization Aware
VAW Indicates whether domain assignments support the optional inclusion of a logical partition identifier,
which is also known as an operating system number or Arm virtual machine identifier (VMID).
0b - Not virtualization-aware
1b - Virtualization-aware
7 Memory Region Format
MRF Indicates the format of memory region descriptors.
0b - Reserved
1b - SMPU family format
Reserved
6-5
—
4-1 Hardware Revision Level
HRL Indicates the XRDC hardware revision level, which is associated with a set of functional characteristics of
the module.
0 Global Valid (XRDC Global Enable/Disable)
GVLD Enables XRDC. When XRDC is disabled, all bus initiators can access all targets.
0b - Disables
1b - Enables
18.8.3.3 Hardware Configuration 0 (HWCFG0)
Offset
Register Offset
HWCFG0 F0h
Function
Indicates XRDC configuration details, including:
• XRDC module ID
• Number of implemented domains

---

*Page 438*

Extended Resource Domain Controller (XRDC)
• Number of bus initiators
• Number of MRCs
• Number of PACs
Attempting to write to this register causes an error.
Access: Secure privileged read
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R MID NPAC NMRC
W
Reset 0 0 0 1 0 0 1 0 0 0 0 0 0 0 1 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R NMSTR NDID
W
Reset 0 0 0 0 0 1 0 1 0 0 0 0 0 0 1 0
Fields
Field Function
Module ID
31-28
MID
27-24 Number Of PACs
NPAC Indicates the number of PACs minus 1. In other words, the actual number of PACs is NPAC + 1.
23-16 Number of MRCs
NMRC Indicates the number of MRCs minus 1. In other words, the actual number of MRCs is NMRC + 1.
15-8 Number Of Bus Initiators
NMSTR Indicates the number of bus initiators minus 1. In other words, the actual number of bus initiators is
NMSTR + 1.
7-0 Number Of DIDs
NDID Indicates the number of domains (DIDs) minus 1. In other words, the actual number of DIDs is NDID + 1.
18.8.3.4 Hardware Configuration 1 (HWCFG1)
Offset
Register Offset
HWCFG1 F4h

---

*Page 439*

Extended Resource Domain Controller (XRDC)
Function
Indicates the DID of the bus initiator making the current transaction request. See Domain error capture management for
information about typical usage.
Attempting to write to this register causes an error.
Access: Secure privileged read
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 DID
W
1
Reset 0 0 0 0 0 0 0 0 0 0 0 0 u u u u
1. The reset value is determined by the current configuration of the accessing initiator.
Fields
Field Function
Reserved
31-4
—
3-0 Domain Identifier
DID Indicates the DID of the requesting bus initiator.
18.8.3.5 Hardware Configuration 2 (HWCFG2)
Offset
Register Offset
HWCFG2 F8h
Function
For initiators 0–31, indicates whether a given initiator has a built-in PID register as part of its programming model. If not, you must
use the corresponding PIDm register to mimic the functionality of a built-in PID register.
Each bit corresponds to the same numbered initiator. For example, if PIDP18 is 1, bus initiator 18 has its own PID register. If
PIDP18 is 0, then initiator 18 does not have its own PID register and you must use PID18.
Attempting to write to this register causes an error.
Access: Secure privileged read

---

*Page 440*

Extended Resource Domain Controller (XRDC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
PIDP3 PIDP3 PIDP2 PIDP2 PIDP2 PIDP2 PIDP2 PIDP2 PIDP2 PIDP2 PIDP2 PIDP2 PIDP1 PIDP1 PIDP1 PIDP1
R
1 0 9 8 7 6 5 4 3 2 1 0 9 8 7 6
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
PIDP1 PIDP1 PIDP1 PIDP1 PIDP1 PIDP1
R PIDP9 PIDP8 PIDP7 PIDP6 PIDP5 PIDP4 PIDP3 PIDP2 PIDP1 PIDP0
5 4 3 2 1 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Process Identifier Present
31-0
0b - Does not have PID register
PIDPn
1b - Has PID register
18.8.3.6 Master Domain Assignment Configuration (MDACFG0 - MDACFG5)
Offset
Register Offset
MDACFG0 100h
MDACFG1 101h
MDACFG2 102h
MDACFG3 103h
MDACFG5 105h
Function
Indicates the number of implemented master domain assignment registers ( MDA_W w _ m _DFMT0 or MDA_W w _ m _DFMT1) for
initiator m , where m ranges from 0 to 63. You can read these registers using 8-, 16-, or 32-bit accesses.
If NMDAR is 0, the associated initiator does not exist.
Attempting to write to this register causes an error.
Access: Secure privileged read

---

*Page 441*

Extended Resource Domain Controller (XRDC)
Diagram
Bits 7 6 5 4 3 2 1 0
R NCM 0 NMDAR
W
Reset See Register reset values .
Register reset values
Register Reset value
MDACFG0 01h
MDACFG1–MDACFG2 81h
MDACFG3 01h
MDACFG4 —
MDACFG5 81h
Fields
Field Function
7 Noncore Master
NCM If NMDAR is greater than zero, indicates whether initiator m uses MDA_W w _ m _DFMT0 or
MDA_W w _ m _DFMT1 to configure domain assignment.
This field is 0 for a non-existent initiator.
0b - Core initiator or initiator does not exist
1b - Noncore initiator
Reserved
6-4
—
3-0 Number Of Master Domain Assignment Registers
NMDAR Indicates the number of master domain assignment registers ( MDA_W w _ m _DFMT0 or
MDA_W w _ m _DFMT1) associated with initiator m .
0000b - Initiator does not exist
0001b-1000b - Number of registers
All other values are reserved.

---

*Page 442*

Extended Resource Domain Controller (XRDC)
18.8.3.7 Memory Region Configuration (MRCFG0 - MRCFG2)
Offset
Register Offset
MRCFG0 140h
MRCFG1 141h
MRCFG2 142h
Function
Indicates the number of memory region descriptors ( r ) for MRC c , from 4 to 16 in increments of four, with 0 indicating a non-existent
MRC. These registers are organized as byte-sized data arrays and can be read using 8-, 16-, or 32-bit accesses.
Attempting to write to this register causes an error.
Access: Secure Privileged Read
Diagram
Bits 7 6 5 4 3 2 1 0
R 0 NMRGD
W
Reset See Register reset values .
Register reset values
Register Reset value
MRCFG0–MRCFG1 10h
MRCFG2 04h
Fields
Field Function
Reserved
7-5
—
4-0 Number Of Memory Region Descriptors
NMRGD Indicates the number of memory region descriptors associated with the MRC.
0_0000b - MRC does not exist
0_0100b - 4
0_1000b - 8
Table continues on the next page...

---

*Page 443*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Field Function
0_1100b - 12
1_0000b - 16
All other values are reserved.
18.8.3.8 Domain Error Location (DERRLOC0 - DERRLOC2)
Offset
Register Offset
DERRLOC0 200h
DERRLOC1 204h
DERRLOC2 208h
Function
Indicates the MRC or PAC instance in domain d where an access violation has occurred. Each bit corresponds to the
like-numbered instance. For more information, see Domain error capture management .
Attempting to write to this register causes an error.
Access: Secure privileged read
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 PACINST
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R MRCINST
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-20
—
Table continues on the next page...

---

*Page 444*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Field Function
19-16 PAC Instance
PACINST Indicates the presence of a detected access violation for domain d in a PAC instance. Each bit corresponds
to the instance index for the DERR_W w _ i registers: bit 16 of the register corresponds to the DERR_W w _ 16
registers, which show error information for PAC 0, and so on. Multiple bits can be 1 at any time, indicating
access violations have been detected across multiple PACs.
For each bit in this field:
0b - No access violation error or the PAC instance is not physically present
1b - Access violation detected
15-0 MRC Instance
MRCINST Indicates the presence of a detected access violation for domain d in an MRC instance. Each bit
corresponds to the like-numbered MRC instance: bit 0 (bit 0 of the register) corresponds to MRC instance
0, and so on. Multiple bits can be 1 at any time, indicating access violations have been detected across
multiple MRCs.
For each bit in this field:
0b - No access violation error or the MRC instance is not physically present
1b - Access violation detected
18.8.3.9 Domain Error Word 0 (DERR_W0_0 - DERR_W0_18)
Offset
Register Offset
DERR_W0_0 400h
DERR_W0_1 410h
DERR_W0_2 420h
DERR_W0_16 500h
DERR_W0_17 510h
DERR_W0_18 520h
Function
Indicates the address of an access violation detected by an MRC or a PAC, indexed by the MRC or PAC instance ( i ) that detected
the violation, as indicated in DERRLOC d . This register is part of a 16-byte set:
• DERR_W0_ i : Word 0, the first 4 bytes
• DERR_W1_ i : Word 1, the second 4 bytes
• Word 2, 4 reserved bytes
• DERR_W3_ i : Word 3, the fourth 4 bytes

---

*Page 445*

Extended Resource Domain Controller (XRDC)
The first 16 sets ( i from 0 to 15) are associated with MRCs and the rest (starting with i = 16) are associated with PACs. For more
information, see Domain error capture management .
The error capture registers in the memory region controller and peripheral access controller submodules contain physical registers
for each domain, but are organized in the DERR_Wn registers to provide the information for the requesting domain only. The
registers return the error information for the domain id that is used to read the registers. When no error occurred for this domain
id, it returns 0 data.
When the error capture logic is rearmed by writing the required data pattern to DERR_W3_n, this register is cleared.
When XRDC detects an access violation, it captures the error information and disables subsequent updates to the error capture
registers until you write to DERR_W3_ i .
NOTE
If initiators with the same DID cause simultaneous error accesses, the error capture registers record only the error
of the lowest target index.
Attempting to write to this register causes an error. Attempting to read the error registers for a non-existent MRC or PAC instance
causes an error.
Access: Secure privileged read
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R EADDR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R EADDR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-0 Error Address
EADDR Indicates the target address of the first transaction that causes an access violation after reset or after
rearming error capture.
18.8.3.10 Domain Error Word 1 (DERR_W1_0 - DERR_W1_18)
Offset
Register Offset
DERR_W1_0 404h
DERR_W1_1 414h
Table continues on the next page...

---

*Page 446*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Register Offset
DERR_W1_2 424h
DERR_W1_16 504h
DERR_W1_17 514h
DERR_W1_18 524h
Function
Indicates the attributes of an access violation detected by an MRC or a PAC, indexed by the MRC or PAC instance ( i ) that detected
the access violation, as indicated in DERRLOC d . For more information, see DERR_W0_ i and Domain error capture management .
The error capture registers in the memory region controller and peripheral access controller submodules contain physical registers
for each domain, but are organized in the DERR_Wn registers to provide the information for the requesting domain only. The
registers return the error information for the domain id that is used to read the registers. When no error occurred for this domain
id, it returns 0 data.
When the error capture logic is rearmed by writing the required data pattern to DERR_W3_n, this register is cleared.
NOTE
If initiators with the same DID cause simultaneous error accesses, the error capture registers record only the error
of the lowest target index.
Attempting to write to this register causes an error. Attempting to read the error registers for a non-existent MRC or PAC instance
causes an error.
Access: Secure privileged read
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R EST 0 EPORT 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 ERW EATR 0 EDID
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-30 Error State
EST
Table continues on the next page...

---

*Page 447*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Field Function
Indicates the state of access violations for this domain in this instance of the MRC or PAC. After the first
access violation to occur after reset or rearming of error capture, XRDC records subsequent errors as an
overrun condition without any data captured.
00b-01b - No access violations detected
10b - A single access violation has been detected
11b - Multiple access violations have been detected
Reserved
29-27
—
26-24 Error Port
EPORT Identifies the encoded port number of the MRC that detected the access violation. See the chip-specific
configuration details for the MRC port connections. For access violations detected by a PAC, this field is
zero.
Reserved
23-12
—
11 Error Read Or Write
ERW Indicates whether the captured access violation occurred on a read or write access.
0b - Read access
1b - Write access
10-8 Error Attributes
EATR Indicates attributes of the access violation.
000b - Secure user mode, instruction fetch access
001b - Secure user mode, data access
010b - Secure privileged mode, instruction fetch access
011b - Secure privileged mode, data access
100b - Nonsecure user mode, instruction fetch access
101b - Nonsecure user mode, data access
110b - Nonsecure privileged mode, instruction fetch access
111b - Nonsecure privileged mode, data access
Reserved
7-4
—
3-0 Error Domain Identifier
EDID Indicates the DID of the access violation.

---

*Page 448*

Extended Resource Domain Controller (XRDC)
18.8.3.11 Domain Error Word 3 (DERR_W3_0 - DERR_W3_18)
Offset
Register Offset
DERR_W3_0 40Ch
DERR_W3_1 41Ch
DERR_W3_2 42Ch
DERR_W3_16 50Ch
DERR_W3_17 51Ch
DERR_W3_18 52Ch
Function
Rearms instance error capture, resets the error capture registers ( DERR_W0_ d , DERR_W1_ d ), and deasserts the instance bit
in DERRLOC d . After reading the access violation error information, an error handler must write 1 to RECR.
Register write only rearms error capture registers for the domain id that is used to write this register.
This register returns 0000h when read. Attempted reads of an MRC or PAC instance that is not physically present cause an error.
For more information, see Domain error capture management .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
W RECR
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-30 Rearm Error Capture Registers
RECR Resets and rearms the domain error capture registers for this instance, including deasserting the
instance bit in DERRLOC d .
00b,10b,11b - No effect
01b - Rearms error capture, resets error capture registers, and deasserts instance bit in
DERRLOCd
Table continues on the next page...

---

*Page 449*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Field Function
Reserved
29-0
—
18.8.3.12 Process Identifier (PID0 - PID3)
Offset
Register Offset
PID0 700h
PID3 70Ch
Function
Specifies the PID for the associated core initiator m .
Some cores contain a built-in PID register. If the core has a built-in PID register, XRDC populates the PID field with the value
from the core PID register. If the core does not have the built-in register, the XRDC PID register allows applications to mimic PID
operation for that core by writing the desired PID to the associated register.
HWCFG2 provides a bitmap of the implemented PID m registers. Noncore initiators do not have an associated PID register.
For information about PID-based operation, see PID-based domain assignment .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
LK2 TSM
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
PID
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
30-29 Lock
Table continues on the next page...

---

*Page 450*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Field Function
LK2 Limits or prohibits writes to this register.
When you assert a bit in this field, it remains asserted until the next module reset.
If the core initiator has a built-in PID register, as indicated in HWCFG2 , then a secure privileged read returns
0 for this field.
00b,01b - Any secure privileged write
10b - Secure privileged writes from initiator only
11b - Locks
28 Three-State Model
TSM Specifies that the core initiator supports only the three-state access control model. If you write 1 to this field,
it remains asserted until the next reset.
For cores that support only the three-state access control model, you must assert this field before loading
any nonsecure value into the PID.
See Generation of secure attribute .
Reserved
27-6
—
5-0 Process Identifier
PID Specifies the transaction PID for the corresponding core initiator.
If the core has a built-in PID register, then a secure privileged read returns the core's PID register value.
Bit 5 specifies the secure attribute (0 = secure, 1 = nonsecure) for the transaction.
18.8.3.13 Master Domain Assignment (MDA_W0_0_DFMT0 - MDA_W0_3_DFMT0)
Offset
Register Offset
MDA_W0_0_DFMT0 800h
MDA_W0_3_DFMT0 860h
Function
Specifies the information used by the MDAC to assign a core bus initiator to a specific domain (DID). For more information, see
Master domain assignment controller (MDAC) .
Access: Secure privileged read/write

---

*Page 451*

Extended Resource Domain Controller (XRDC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DFMT 0 0 0
VLD LK1 PID
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
PIDM PE DIDS DID
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Valid
VLD Specifies whether this domain assignment is valid. In other words, if VLD and CR[GVLD] are both asserted,
XRDC uses the configuration in this register in the domain assignment process. If CR[GVLD] is set to 1 and
VLD is set to 0 then every transaction from this initiator will be assigned a DID of 0.
This field has no effect unless XRDC is enabled ( CR[GVLD] = 1).
0b - Invalid
1b - Valid
30 Lock
LK1 Prohibits writes to this register.
• If unlocked, this register accepts any secure privileged write.
• If locked, you cannot write to this register and it remains read-only until after the next reset.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Unlocked
1b - Locks
When writing
0b - No effect
1b - Locks
29 Domain Format
DFMT Indicates the domain assignment format.
0b - Core bus initiator domain assignment (DFMT0)
Reserved
28
Table continues on the next page...

---

*Page 452*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Field Function
—
Reserved
27-24
—
Reserved
23-22
—
21-16 Process Identifier
PID Specifies the PID to be combined with the PIDM field and included in the domain assignment process.
This field applies only if PID is enabled ( PE = 1).
Reserved
15-14
—
13-8 Process Identifier Mask
PIDM Specifies a mask applied to the PID to support including multiple PIDs in the domain hit determination.
For each bit asserted in PIDM, the corresponding bit of the PID is ignored in the comparison. This field
applies only if PID is enabled ( PE = 1).
7-6 Process Identifier Enable
PE Enables the optional inclusion of PID , qualified by PIDM , in the domain hit evaluation. This inclusion
supports the definition of inclusive or exclusive sets of masked PID values.
00b-01b - No PID is included
10b - Partial domain hit = (PID & ~PIDM) == (PIDm[PID] & ~PIDM)
11b - Partial domain hit = ~((PID & ~PIDM) == (PIDm[PID] & ~PIDM))
5-4 DID Select
DIDS Selects the source of the DID.
00b - Use the DID field of this register
01b - Use the input DID
10b - Concatenate bits 3–2 of this register with the least significant 2 bits of the input DID
(DID_in[1:0])
11b - Reserved
Reserved
3-2
—
1-0 Domain Identifier
DID Specifies the DID. DIDS controls whether and how this value is used.

---

*Page 453*

Extended Resource Domain Controller (XRDC)
18.8.3.14 Master Domain Assignment (MDA_W0_1_DFMT1 - MDA_W0_5_DFMT1)
Offset
Register Offset
MDA_W0_1_DFMT1 820h
MDA_W0_2_DFMT1 840h
MDA_W0_5_DFMT1 8A0h
Function
Specifies the information used by the MDAC to assign a bus initiator to a specific domain (DID). For more information, see Master
domain assignment controller (MDAC) .
Access: Secure privileged read/write
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R DFMT 0 0 0
VLD LK1
W
Reset 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
DIDB SA PA DID
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Valid
VLD Specifies whether this domain assignment is valid. In other words, if VLD and CR[GVLD] are both asserted,
XRDC uses the configuration in this register in the domain assignment process. If CR[GVLD] is set to 1 and
VLD is set to 0 then every transaction from this initiator will be assigned a DID of 0.
This field has no effect unless XRDC is enabled ( CR[GVLD] = 1).
0b - Invalid
1b - Valid
30 Lock
LK1 Prohibits writes to this register.
• If unlocked, this register accepts any secure privileged write.
Table continues on the next page...

---

*Page 454*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Field Function
• If locked, you cannot write to this register and it remains read-only until after the next reset.
NOTE
This field behaves differently for register reads and writes.
When reading
0b - Unlocked
1b - Locks
When writing
0b - No effect
1b - Locks
29 Domain Format
DFMT Indicates the domain assignment format.
1b - Bus initiator domain assignment (DFMT1)
Reserved
28
—
Reserved
27-24
—
Reserved
23-9
—
8 DID Bypass
DIDB Enables bypassing of an input DID as the domain identifier for this initiator. This capability allows noncore
initiators (for example, a DMA) to appear as a core.
After this field is set to 1, it remains at that value until the next reset.
0b - Bypass DID input. Use DID
1b - Use DID input
7-6 Secure Attribute
SA Specifies the secure attribute.
NOTE
If SA = 1X or VLD = 0, use the secure attribute input from the initiator.
00b - Force to secure
01b - Force to nonsecure
1xb - Use secure attribute from the initiator
Table continues on the next page...

---

*Page 455*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Field Function
5-4 Privileged Attribute
PA Specifies the privileged (supervisor/user) attribute.
NOTE
If PA = 1X or VLD = 0, use the privileged attribute input from the initiator.
00b - Force to user
01b - Force to privileged
1xb - Use privileged attribute from the initiator
Reserved
3-2
—
1-0 Domain Identifier
DID Specifies the DID.
18.8.3.15 Peripheral Domain Access Control Word 0 (PDAC_W0_32 - PDAC_W0_315)
Offset
Register Offset
PDAC_W0_32 1100h
PDAC_W0_33 1108h
PDAC_W0_34 1110h
PDAC_W0_35 1118h
PDAC_W0_36 1120h
PDAC_W0_38 1130h
PDAC_W0_39 1138h
PDAC_W0_40 1140h
PDAC_W0_41 1148h
PDAC_W0_42 1150h
PDAC_W0_44 1160h
PDAC_W0_45 1168h
PDAC_W0_46 1170h
PDAC_W0_47 1178h
PDAC_W0_128 1400h
Table continues on the next page...

---

*Page 456*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Register Offset
PDAC_W0_129 1408h
PDAC_W0_130 1410h
PDAC_W0_131 1418h
PDAC_W0_132 1420h
PDAC_W0_133 1428h
PDAC_W0_134 1430h
PDAC_W0_135 1438h
PDAC_W0_136 1440h
PDAC_W0_137 1448h
PDAC_W0_138 1450h
PDAC_W0_139 1458h
PDAC_W0_140 1460h
PDAC_W0_141 1468h
PDAC_W0_142 1470h
PDAC_W0_143 1478h
PDAC_W0_144 1480h
PDAC_W0_145 1488h
PDAC_W0_146 1490h
PDAC_W0_147 1498h
PDAC_W0_148 14A0h
PDAC_W0_149 14A8h
PDAC_W0_150 14B0h
PDAC_W0_151 14B8h
PDAC_W0_152 14C0h
PDAC_W0_153 14C8h
PDAC_W0_154 14D0h
PDAC_W0_155 14D8h
PDAC_W0_156 14E0h
PDAC_W0_157 14E8h
PDAC_W0_158 14F0h
PDAC_W0_159 14F8h
PDAC_W0_160 1500h
Table continues on the next page...

---

*Page 457*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Register Offset
PDAC_W0_161 1508h
PDAC_W0_162 1510h
PDAC_W0_163 1518h
PDAC_W0_164 1520h
PDAC_W0_165 1528h
PDAC_W0_166 1530h
PDAC_W0_167 1538h
PDAC_W0_168 1540h
PDAC_W0_169 1548h
PDAC_W0_170 1550h
PDAC_W0_171 1558h
PDAC_W0_173 1568h
PDAC_W0_175 1578h
PDAC_W0_177 1588h
PDAC_W0_178 1590h
PDAC_W0_179 1598h
PDAC_W0_180 15A0h
PDAC_W0_181 15A8h
PDAC_W0_182 15B0h
PDAC_W0_183 15B8h
PDAC_W0_184 15C0h
PDAC_W0_186 15D0h
PDAC_W0_187 15D8h
PDAC_W0_188 15E0h
PDAC_W0_191 15F8h
PDAC_W0_193 1608h
PDAC_W0_194 1610h
PDAC_W0_195 1618h
PDAC_W0_196 1620h
PDAC_W0_197 1628h
PDAC_W0_198 1630h
PDAC_W0_201 1648h
Table continues on the next page...

---

*Page 458*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Register Offset
PDAC_W0_202 1650h
PDAC_W0_203 1658h
PDAC_W0_204 1660h
PDAC_W0_205 1668h
PDAC_W0_206 1670h
PDAC_W0_207 1678h
PDAC_W0_208 1680h
PDAC_W0_209 1688h
PDAC_W0_212 16A0h
PDAC_W0_213 16A8h
PDAC_W0_214 16B0h
PDAC_W0_215 16B8h
PDAC_W0_216 16C0h
PDAC_W0_217 16C8h
PDAC_W0_219 16D8h
PDAC_W0_220 16E0h
PDAC_W0_221 16E8h
PDAC_W0_223 16F8h
PDAC_W0_224 1700h
PDAC_W0_225 1708h
PDAC_W0_226 1710h
PDAC_W0_227 1718h
PDAC_W0_229 1728h
PDAC_W0_231 1738h
PDAC_W0_232 1740h
PDAC_W0_236 1760h
PDAC_W0_256 1800h
PDAC_W0_257 1808h
PDAC_W0_260 1820h
PDAC_W0_261 1828h
PDAC_W0_262 1830h
PDAC_W0_263 1838h
Table continues on the next page...

---

*Page 459*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Register Offset
PDAC_W0_264 1840h
PDAC_W0_265 1848h
PDAC_W0_266 1850h
PDAC_W0_267 1858h
PDAC_W0_268 1860h
PDAC_W0_269 1868h
PDAC_W0_270 1870h
PDAC_W0_271 1878h
PDAC_W0_272 1880h
PDAC_W0_273 1888h
PDAC_W0_274 1890h
PDAC_W0_275 1898h
PDAC_W0_276 18A0h
PDAC_W0_277 18A8h
PDAC_W0_278 18B0h
PDAC_W0_279 18B8h
PDAC_W0_280 18C0h
PDAC_W0_281 18C8h
PDAC_W0_283 18D8h
PDAC_W0_285 18E8h
PDAC_W0_288 1900h
PDAC_W0_291 1918h
PDAC_W0_292 1920h
PDAC_W0_293 1928h
PDAC_W0_294 1930h
PDAC_W0_295 1938h
PDAC_W0_296 1940h
PDAC_W0_297 1948h
PDAC_W0_298 1950h
PDAC_W0_303 1978h
PDAC_W0_304 1980h
PDAC_W0_307 1998h
Table continues on the next page...

---

*Page 460*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Register Offset
PDAC_W0_311 19B8h
PDAC_W0_314 19D0h
PDAC_W0_315 19D8h
Function
In conjunction with PDAC_W1_ s , specifies the ACP configuration for peripheral slot s . The ACP controls access to the
peripheral by all initiators within the domain. For the available ACPs, see Domain ACP specification . For more information, see
Peripheral access controller (PAC) .
Access: Secure privileged read/write
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
SE SNUM
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
D2ACP D1ACP D0ACP
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
30 Semaphore Enable
SE Enables the inclusion of the semaphore specified in SNUM in the D d ACP evaluation.
0b - Disables
1b - Enables
Reserved
29-28
—
27-24 Semaphore Number
SNUM Specifies the hardware semaphore to include in the D d ACP access evaluation. This field applies only if
you enable semaphore (write 1 to SE ).
Table continues on the next page...

---

*Page 461*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Field Function
Reserved
23-9
—
8-6: D2ACP Domain Access Control Policy
5-3: D1ACP Specifies the ACP for the associated domain. This field applies only for a supported DID; if the DID
is not implemented, the field is read-only zero (no access rights). For field values, see Domain ACP
2-0: D0ACP
specification .
18.8.3.16 Peripheral Domain Access Control Word 1 (PDAC_W1_32 - PDAC_W1_315)
Offset
Register Offset
PDAC_W1_32 1104h
PDAC_W1_33 110Ch
PDAC_W1_34 1114h
PDAC_W1_35 111Ch
PDAC_W1_36 1124h
PDAC_W1_38 1134h
PDAC_W1_39 113Ch
PDAC_W1_40 1144h
PDAC_W1_41 114Ch
PDAC_W1_42 1154h
PDAC_W1_44 1164h
PDAC_W1_45 116Ch
PDAC_W1_46 1174h
PDAC_W1_47 117Ch
PDAC_W1_128 1404h
PDAC_W1_129 140Ch
PDAC_W1_130 1414h
PDAC_W1_131 141Ch
PDAC_W1_132 1424h
PDAC_W1_133 142Ch
PDAC_W1_134 1434h
Table continues on the next page...

---

*Page 462*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Register Offset
PDAC_W1_135 143Ch
PDAC_W1_136 1444h
PDAC_W1_137 144Ch
PDAC_W1_138 1454h
PDAC_W1_139 145Ch
PDAC_W1_140 1464h
PDAC_W1_141 146Ch
PDAC_W1_142 1474h
PDAC_W1_143 147Ch
PDAC_W1_144 1484h
PDAC_W1_145 148Ch
PDAC_W1_146 1494h
PDAC_W1_147 149Ch
PDAC_W1_148 14A4h
PDAC_W1_149 14ACh
PDAC_W1_150 14B4h
PDAC_W1_151 14BCh
PDAC_W1_152 14C4h
PDAC_W1_153 14CCh
PDAC_W1_154 14D4h
PDAC_W1_155 14DCh
PDAC_W1_156 14E4h
PDAC_W1_157 14ECh
PDAC_W1_158 14F4h
PDAC_W1_159 14FCh
PDAC_W1_160 1504h
PDAC_W1_161 150Ch
PDAC_W1_162 1514h
PDAC_W1_163 151Ch
PDAC_W1_164 1524h
PDAC_W1_165 152Ch
PDAC_W1_166 1534h
Table continues on the next page...

---

*Page 463*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Register Offset
PDAC_W1_167 153Ch
PDAC_W1_168 1544h
PDAC_W1_169 154Ch
PDAC_W1_170 1554h
PDAC_W1_171 155Ch
PDAC_W1_173 156Ch
PDAC_W1_175 157Ch
PDAC_W1_177 158Ch
PDAC_W1_178 1594h
PDAC_W1_179 159Ch
PDAC_W1_180 15A4h
PDAC_W1_181 15ACh
PDAC_W1_182 15B4h
PDAC_W1_183 15BCh
PDAC_W1_184 15C4h
PDAC_W1_186 15D4h
PDAC_W1_187 15DCh
PDAC_W1_188 15E4h
PDAC_W1_191 15FCh
PDAC_W1_193 160Ch
PDAC_W1_194 1614h
PDAC_W1_195 161Ch
PDAC_W1_196 1624h
PDAC_W1_197 162Ch
PDAC_W1_198 1634h
PDAC_W1_201 164Ch
PDAC_W1_202 1654h
PDAC_W1_203 165Ch
PDAC_W1_204 1664h
PDAC_W1_205 166Ch
PDAC_W1_206 1674h
PDAC_W1_207 167Ch
Table continues on the next page...

---

*Page 464*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Register Offset
PDAC_W1_208 1684h
PDAC_W1_209 168Ch
PDAC_W1_212 16A4h
PDAC_W1_213 16ACh
PDAC_W1_214 16B4h
PDAC_W1_215 16BCh
PDAC_W1_216 16C4h
PDAC_W1_217 16CCh
PDAC_W1_219 16DCh
PDAC_W1_220 16E4h
PDAC_W1_221 16ECh
PDAC_W1_223 16FCh
PDAC_W1_224 1704h
PDAC_W1_225 170Ch
PDAC_W1_226 1714h
PDAC_W1_227 171Ch
PDAC_W1_229 172Ch
PDAC_W1_231 173Ch
PDAC_W1_232 1744h
PDAC_W1_236 1764h
PDAC_W1_256 1804h
PDAC_W1_257 180Ch
PDAC_W1_260 1824h
PDAC_W1_261 182Ch
PDAC_W1_262 1834h
PDAC_W1_263 183Ch
PDAC_W1_264 1844h
PDAC_W1_265 184Ch
PDAC_W1_266 1854h
PDAC_W1_267 185Ch
PDAC_W1_268 1864h
PDAC_W1_269 186Ch
Table continues on the next page...

---

*Page 465*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Register Offset
PDAC_W1_270 1874h
PDAC_W1_271 187Ch
PDAC_W1_272 1884h
PDAC_W1_273 188Ch
PDAC_W1_274 1894h
PDAC_W1_275 189Ch
PDAC_W1_276 18A4h
PDAC_W1_277 18ACh
PDAC_W1_278 18B4h
PDAC_W1_279 18BCh
PDAC_W1_280 18C4h
PDAC_W1_281 18CCh
PDAC_W1_283 18DCh
PDAC_W1_285 18ECh
PDAC_W1_288 1904h
PDAC_W1_291 191Ch
PDAC_W1_292 1924h
PDAC_W1_293 192Ch
PDAC_W1_294 1934h
PDAC_W1_295 193Ch
PDAC_W1_296 1944h
PDAC_W1_297 194Ch
PDAC_W1_298 1954h
PDAC_W1_303 197Ch
PDAC_W1_304 1984h
PDAC_W1_307 199Ch
PDAC_W1_311 19BCh
PDAC_W1_314 19D4h
PDAC_W1_315 19DCh
Function
In conjunction with PDAC_W0_ s , specifies the ACP configuration for peripheral slot s .
Access: Secure privileged read/write

---

*Page 466*

Extended Resource Domain Controller (XRDC)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
VLD LK2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Valid
VLD Specifies whether this domain assignment is valid. In other words, if VLD and CR[GVLD] are both asserted,
XRDC uses the configuration in this pair of registers. If either CR[GVLD] or this field is 0, all accesses to
the peripheral are allowed. To support a coherent register state, any write to PDAC_W0_ s forces this field
to zero.
This field has no effect unless XRDC is enabled ( CR[GVLD] = 1).
0b - Invalid
1b - Valid
30-29 Lock
LK2 Limits or prohibits writes to the set of PDAC words (PDAC_W0_ s and PDAC_W1_ s ) for this peripheral slot.
When you assert a bit in this field, it remains asserted until the next module reset.
00b-01b - Both words can be written to
10b - Domain d can update only its associated DdACP field—all other fields are read-only
11b - Locks (both words are read-only)
Reserved
28-24
—
Reserved
23-0
—
18.8.3.17 Memory Region Descriptor Word 0 (MRGD_W0_0 - MRGD_W0_35)
Offset
Registers in this array exist only for the following combinations of index values.

---

*Page 467*

Extended Resource Domain Controller (XRDC)
Index n Index m
0–1 0–15
2 0–3
Register Offset
MRGD_W0_(n * 16 + m) 2000h + (n × 200h) + (m × 20h)
Function
Specifies the starting address of memory region r .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
SRTADDR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R Reserved
SRTADDR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1
Fields
Field Function
31-5 Start Address
SRTADDR Specifies the most significant bits of the 0-modulo 32-byte start address of the memory region. The minimum
region size is 32 bytes.
Reserved
4-0
—
18.8.3.18 Memory Region Descriptor Word 1 (MRGD_W1_0 - MRGD_W1_35)
Offset
Registers in this array exist only for the following combinations of index values.
Index n Index m
0–1 0–15
2 0–3

---

*Page 468*

Extended Resource Domain Controller (XRDC)
Register Offset
MRGD_W1_(n * 16 + m) 2004h + (n × 200h) + (m × 20h)
Function
Specifies the ending address of memory region r .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
ENDADDR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 1
ENDADDR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 1 1 1 1 1
Fields
Field Function
31-5 End Address
ENDADDR Specifies the most significant bits of the 31-modulo 32-byte end address of memory region r .
Reserved
4-0
—
18.8.3.19 Memory Region Descriptor Word 2 (MRGD_W2_0 - MRGD_W2_35)
Offset
Registers in this array exist only for the following combinations of index values.
Index n Index m
0–1 0–15
2 0–3
Register Offset
MRGD_W2_(n * 16 + m) 2008h + (n × 200h) + (m × 20h)

---

*Page 469*

Extended Resource Domain Controller (XRDC)
Function
Specifies the ACP for the associated domain. The encodings specify read and write access capabilities based on the four
operating states. This field applies only for a supported DID; if the DID is not implemented, the field is read-only zero (no
access rights).
For field values, see Domain ACP specification .
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0
SE SNUM
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
D2ACP D1ACP D0ACP
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31
—
30 Semaphore Enable
SE Enables the inclusion of the semaphore specified in SNUM in the D d ACP evaluation.
0b - Disables
1b - Enables
Reserved
29-28
—
27-24 Semaphore Number
SNUM Specifies the hardware semaphore to include in the D d ACP access evaluation. This field applies only if
you enable semaphore (write 1 to SE ).
Reserved
23-9
—
8-6: D2ACP Domain Access Control Policy
5-3: D1ACP Specifies the ACP for the associated domain. This field applies only for a supported DID; if the DID
is not implemented, the field is read-only zero (no access rights). For field values, see Domain ACP
2-0: D0ACP
specification .

---

*Page 470*

Extended Resource Domain Controller (XRDC)
18.8.3.20 Memory Region Descriptor Word 3 (MRGD_W3_0 - MRGD_W3_35)
Offset
Registers in this array exist only for the following combinations of index values.
Index n Index m
0–1 0–15
2 0–3
Register Offset
MRGD_W3_(n * 16 + m) 200Ch + (n × 200h) + (m × 20h)
Function
Specifies whether this memory region descriptor is enabled and limits or prohibits writes to it.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0
VLD LK2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Valid
VLD Specifies whether this domain assignment is valid. In other words, if VLD and CR[GVLD] are both asserted,
XRDC uses the configuration in this set of registers. If CR[GVLD] is 0, all accesses to the memory are
allowed. If CR[GVLD] is 1 and VLD is 0, all accesses are blocked. To support a coherent register state, a
write to any of the MRGD W0–W2 registers forces this field to zero.
This field has no effect unless XRDC is enabled ( CR[GVLD] = 1).
0b - Invalid
1b - Valid
30-29 Lock
Limits or prohibits writes to the set of MRGD words (MRGD_W w _ r ) for this memory region.
Table continues on the next page...

---

*Page 471*

Extended Resource Domain Controller (XRDC)
Table continued from the previous page...
Field Function
LK2 When you assert a bit in this field, it remains asserted until the next module reset.
00b - All words in the set can be written to
01b - Reserved
10b - Domain d can update only its associated DdACP field; all other fields are read-only
11b - Locks (all words are read-only)
Reserved
28-24
—
Reserved
23-0
—

#### 18.9 Glossary

ACP access control policy. The access limitations specified for memory and peripheral resources.
DID domain identifier. A numeric value that identifies a specific domain.
domain
An access-controlled virtual group of on-chip initiators (cores and noncore initiators) and targets (memories and
peripherals) that comprise an isolated computing environment. All initiators in a domain have the same access to
chip resources within that domain.
See Introduction to domains for more information.
initiator A processor core or non-processor module, such as DMA or a communications channel, that can initiate
transactions with memory and peripheral resources. This term replaces "master".
LPID logical partition identifier. Also called an operating system ID or VMID, the LPID identifies a virtual initiator (either
core or noncore) that runs on a hypervisor.
MDAC Master Domain Assignment Controller. Manages resource assignments and DIDs .
MGR Manager. Manages accesses through the XRDC programming model.
MRC Memory Region Controller. Controls access to memories based on memory region descriptors.
PAC Peripheral Access Controller (also sometimes called PDAC). Controls access to peripherals.
PDAC See PAC .
PID process identifier. A numeric value provided by some core processors to identify the currently active process.
SDAC Deprecated. See MRC .
target A peripheral or memory resource that one or more initiators can access. This term replaces "slave".
transaction A read or write request made by an initiator to a target peripheral or memory.
VMID virtual machine identifier. See LPID .

---

*Page 472*

