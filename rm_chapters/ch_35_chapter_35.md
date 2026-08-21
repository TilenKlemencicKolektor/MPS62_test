<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 35 -->

# Chapter 35

# EdgeLock Secure Enclave (HSE_B)

#### 35.1 HSE subsystem

HSE is a security subsystem. It runs security functions for applications having stringent confidentiality and authenticity
requirements. HSE has the following objectives:
• Isolating security-sensitive information (for example, secret keys) from the application (the host)
• Transferring the cryptographic operations from application cores and processing them
• Accelerating cryptographic operations with dedicated coprocessors
• Enforcing security measures on the application, during runtime and system startup
The HSE subsystem is the only master that is unconditionally released from reset after POR . It then releases the CPU subsystems
in the host from reset, with the opportunity to apply certain checks beforehand (secure system startup). Based on certain
conditions, HSE can also trigger interrupts and reset signals to the host during runtime.

#### 35.1.1 CPU subsystem

The HSE CPU subsystems process the security functions and control system resources to provide security services to the
application domain (the host).

#### 35.1.2 Cryptographic accelerators

The HSE subsystem supports the following cryptographic accelerators:
• An AES engine supporting all standard key sizes (128, 192, 256 bits) and various complex ciphering modes ( ECB , CBC , CTR ,
OFB , CFB , CCM , CMAC )
• A hash engine that supports standard several primitives: MD5, SHA-1, SHA-224, SHA-256
• PKC which accelerates RSA and ECC operations (key generation, signature generation, signature verification, ciphering)
— RSA (1024, 2048, 3072, 4096-bit)
— ECC over prime numbers
◦ BN P256, P638
◦ ANSI X9P192 to X9P512
◦ Brainpool P160 to P512
◦ Sec P128 to P521
◦ TU Darmstadt prime Curve 1 to 35
◦ Ed25519
The HSE subsystem supports several other cryptographic primitives through the software. See HSE firmware for
more information.

#### 35.1.3 Random number generator (RNG)

The RNG in this chip consists of a TRNG and a DRBG . Both are designed to be compliant to the highest strength in security as
specified in
• BSI AIS20/31
• NIST SP800-90a,b,c

---

*Page 848*

EdgeLock Secure Enclave (HSE_B)
The TRNG function provides a seed for the DRBG, while the DRBG is available to the host via dedicated random number
generation services.

#### 35.1.4 Timers

The HSE subsystem features:
• An independent dedicated system timer
• HSE_STM (apart from chip timer resources), that allows recurring autonomous functions such as runtime memory
verification checks
• A watchdog timer to reset the HSE subsystem in case of unexpected runtime failure

#### 35.1.5 Memory resources

See Configuration_GPR memory map for more information on configuration controls related to HSE functions.
35.1.5.1 Secure RAM
Secure RAM refers to RAM area that the HSE subsystem access exclusively. The HSE firmware use it to operate as well as to
store a copy of the cryptographic keys in service.
35.1.5.2 Secure NVM
Secure NVM refers to nonvolatile memory that the HSE subsystem accesses exclusively. It stores the HSE firmware and the
security assets (for example, keys and private data).
The size of the secure NVM is configurable by the HSE firmware.

#### 35.2 HSE interface

#### 35.2.1 Messaging unit (MU)

The HSE subsystem has two messaging units:
• HSE_MU0
• HSE_MU1
See the "Messaging Unit (MU)" chapter for more information.
35.2.1.1 Overview
MU is the communication interface between the host and the HSE subsystem. The host uses MU to trigger service requests and
to receive service responses. The HSE firmware uses MU to receive service requests, return service responses, and provide a
general status of the HSE subsystem.
Each of the two MU instances available in the HSE subsystem has:
• Two sides:
— MUA : Only the HSE subsystem accesses it.
— MUB : The host accesses it.
The registers on one side have corresponding registers on the other side.
• A set of 32-bit readable and writable transmit registers (MUB_TRn), which the host uses to transfer the address of the service
descriptor to the HSE firmware. The HSE firmware retrieves the address of the service descriptor in the corresponding
registers MUA_RRn.
• A set of 32-bit read-only receive registers (MUB_RRn), which the host uses to retrieve the response to the service requests.
The HSE firmware provides the response to service requests in the corresponding registers MUA_TRn.

---

*Page 849*

EdgeLock Secure Enclave (HSE_B)
• Control and status registers to manage MU and the access to transmit and receive registers.
The advantages of using the MU to manage the HSE service requests and responses are:
• Hardware mechanisms are in place on the transmit registers to avoid service request overrun.
• Interrupt signals are available to allow asynchronous management of the requests (avoiding active waiting loops).
• Freedom from interference between different application cores. You can configure each MU instance with specific access
restrictions that can be used, for example, to isolate the requests that different masters make (in different MU instances). You
configure such access controls using XRDC.

#### 35.2.2 HSE interface RAM

The HSE interface RAM (also known as host interface RAM) is a secure RAM area. The host accesses it with the
restrictions that HSE set via XRDC. This RAM area is a part of chip RAM and the size is configurable by secure core
using CONFIG_RAMPR[SECURE_SIZE] .

#### 35.3 Debug

#### 35.3.1 HSE subsystem debug

The debugging of the HSE subsystem and associated firmware is restricted to NXP engineering teams.

#### 35.3.2 Host debug

The host debug is either open or protected, depending on the device Lifecycle state. See the ‘Life cycle’ section in ‘Device
Configuration Module’ chapter for details on device lifecycle advancement and decoding. The details of the device security
features is published in a separate document, the Security Reference Manual. Please contact your NXP sales representative for
details. See the Debug chapter for details on host/application core debug.
The debug protection consists of locking the debugger access through the JTAG interface until the HSE firmware authenticates
the debugger. This authentication is based on ADK/P , a 16-byte region within UTEST used for application core debug. See UTEST
memory map in the DCF clients file attached to this document.
The authentication method can be:
• Static: In this case, ADK/P is a password which the debugger provides in plain form.
• Dynamic: In this case, ADK/P is a cryptographic key which the debugger uses to calculate a cryptographic response to a
random challenge.

#### 35.4 HSE firmware

Factory supplied firmware that runs in the HSE subsystem controls HSE functionality. It essentially serves the host with a set of
security services as described in Table 186 .
Table 186. Summary of firmware security services
Service Summary
Administration Install, configure, update, and test the HSE firmware
Key management Available for the application to manage different sets of keys that the HSE firmware manages, for
example, cryptographic services
Cryptographic Provide the application with cryptographic primitives that high level security stacks use in
the application
Random number Generate random streams that can be used in various security protocols
Table continues on the next page...

---

*Page 850*

EdgeLock Secure Enclave (HSE_B)
Table 186. Summary of firmware security services (continued)
Service Summary
Memory verification Allow the application to verify different memory areas at startup (after reset) and during runtime
Monotonic counter Provide the application with a set of monotonic counters that can be read and only incremented
Secure time Allow the configuration of a secure tick to be signaled to the application
Table 187 provides an overview of services and features that the HSE firmware supports.
Table 187. HSE firmware features
Service Category Feature
Cryptography Ciphers 1
AES : ECB, CBC, CFB, OFB, CTR, XTS
RSAES: PKCS1 -v1_5, OAEP
1 1 1
Message AES: CMAC , XCBC-MAC , HMAC , and GMAC
Authentication
Code (MAC)
Hashing
SHA1
SHA224, SHA256, SHA384, SHA512
1 1 1 1
SHA3_224 , SHA3_256 , SHA3_384 , SHA3_512
MD5
Miyaguchi-Preneel Compression
1
Authenticated ciphers AES: CCM, GCM
Digital signature
RSASSA_PSS
generation
RSASSA_PKCS1-v1_5
and verification
ECDSA – ECC over GF(P) with all prime standard curve supported
EdDSA - Ed25519 pre-hashed curve
Key management Max key sizes
AES: 256 bits
RSA: 4096 bits
ECC: 521 bits
DH: 4096 bits
Key generation Permanent and ephemeral RSA and ECC key pair generation
Key import or export
Plain or encrypted form, with optional authentication tag.
SHE key update protocol
Key derivation NIST 800-108, PBKDF2, and so on
Key exchange ECDH and Classic DH
Certificate handling
Key Installation from X.509 and CVC certificates
Certificate installation for Root of Trust establishment.
Table continues on the next page...

---

*Page 851*

EdgeLock Secure Enclave (HSE_B)
Table 187. HSE firmware features (continued)
Service Category Feature
Boot and Authentication schema
AES CMAC, XCBC-MAC
memory verification
RSA & ECC signatures
Verification flow
Before application startup (strict secure boot)
In parallel to the application startup
On the application demand
Sanctions
No startup (strict secure boot)
Chip reset
Key usage restrictions
Monotonic counter Counter management Incrementing and reading volatile and non-volatile counters
Random number Deterministic random
Based on a True Random Number
bit generation
AIS31 Class P2 high and FIPS 140-2 compliant
Secure time Secure tick Application interrupts at configurable frequency
Administration services HSE administration
Firmware installation and update
Subsystem configuration and testing
1. Software implementation of cryptographic primitives.
See documents in Table 188 for more information about how to install, configure, and use the HSE firmware that NXP provides.
Table 188. References
Document number Document title Description
HSEFWRM HSE Firmware Contains details about how to install, configure, and use the
Reference Manual HSE subsystem.
HSESIRM HSE Service Interface Security firmware API reference for non-AUTOSAR users.
Reference Manual

#### 35.5 General purpose registers with write access by HSE core

This is a general purpose register bank that only the HSE core can write. Secure master ID gating allows only the secure core to
write in these registers, whereas any application core can read.

#### 35.6 Configuration_GPR register descriptions

#### 35.6.1 Configuration_GPR memory map

This section describes the chip configurations that only the HSE core manages. These constitute control of peripherals, secure
size configurations for SRAM and flash memory, flash memory program or erase control, and so on.
NOTE
Write accesses to configuration registers apart from 32-bit accesses might result in unpredictable chip behavior,
therefore must not be done.

---

*Page 852*

EdgeLock Secure Enclave (HSE_B)
Configuration base address: 4039_C000h
Offset Register Access Reset value
Width
(In bits)
1Ch General Purpose Configuration 0 (CONFIG_REG0) 32 R 0000_0000h
34h General Purpose Configuration 6 (CONFIG_REG6) 32 R 0000_0035h
38h Configuration RAM Protected Region (CONFIG_RAMPR) 32 R See section
3Ch Configuration Code Flash Memory Active Block (CONFIG_CFPRL) 32 R See section
40h Configuration Code Flash Memory Passive Block (CONFIG_CFPRH) 32 R See section
44h Configuration Data Flash Memory Protected Region 32 R See section
(CONFIG_DFPR)
50h Configuration Program and Erase Lock (CONFIG_PE_LOCK) 32 R 0000_0000h
54h Configuration RAM Protected Region Alternate 32 R See section
(CONFIG_RAMPR_ALT)
58h Configuration Code Flash Memory Active Block Alternate 32 R See section
(CONFIG_CFPRL_ALT)
5Ch Configuration Code Flash Memory Passive Block Alternate 32 R See section
(CONFIG_CFPRH_ALT)
60h Configuration Data Flash Memory Protected Region Alternate 32 R See section
(CONFIG_DFPR_ALT)
64h Configuration REG_GPR (CONFIG_REG_GPR) 32 RW A000_0003h

#### 35.6.2 General Purpose Configuration 0 (CONFIG_REG0)

Offset
Register Offset
CONFIG_REG0 1Ch
Function
The EDB field resets on destructive or POR reset events and functional reset has no impact on it.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 853*

EdgeLock Secure Enclave (HSE_B)
1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R Reserved EDB Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
1. If export_control=1, the reset value of EDB is 1. If export_control=0, the reset value is 0.
Fields
Field Function
Reserved
31-7
—
6 Hardware Debugger Attached
EDB This is a sticky field that clears on destructive reset.
0b - Debugger not connected
1b - Debugger connected
Reserved
5-0
—

#### 35.6.3 General Purpose Configuration 6 (CONFIG_REG6)

Offset
Register Offset
CONFIG_REG6 34h
Function
Resets on functional reset.

---

*Page 854*

EdgeLock Secure Enclave (HSE_B)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R HL Reserved
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
Reserv SAI_S FLEXI Reserv EMAC Reserv QUAD
R Reserved
ed DI ... O_ ... ed _CL ... ed SPI ...
W
Reset 0 0 0 0 0 0 0 0 0 0 1 1 0 1 0 1
Fields
Field Function
31 Hard Lock
HL Only functional reset clears this field.
0b - You can write to this register
1b - Register is locked for any write
Reserved
30-7
—
Reserved
6
—
5 SAI0 and SAI1 clock gating
SAI_SDID_PCT Clock to SAI peripheral is on or off.
L
0b - Clock is off (gated)
1b - Clock is on
4 FlexIO Clock Gating
FLEXIO_CLOC Clock to FlexIO peripheral is on or off.
K_GATE
0b - Clock is off (gated)
1b - Clock is on
Reserved
3
—
2 Ethernet Clock Gating
EMAC_CLOCK Clock to Ethernet peripheral is on or off.
_GATE
Table continues on the next page...

---

*Page 855*

EdgeLock Secure Enclave (HSE_B)
Table continued from the previous page...
Field Function
0b - Clock is off (gated)
1b - Clock is on
Reserved
1
—
0 QuadSPI Clock Gating
QUADSPI_SDI Clock to QuadSPI peripheral is on or off.
D_PCTL
0b - Clock is off (gated)
1b - Clock is on

#### 35.6.4 Configuration RAM Protected Region (CONFIG_RAMPR)

Offset
Register Offset
CONFIG_RAMPR 38h
Function
Resets on functional reset.
NOTE
The secure size will go to default or reset value on assertion of any DCF violation from HSE.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
HARD SOFT_
R Reserved SECURE_SIZE
_LO ... LO ...
W
Reset
u u u u u u u u u u u u u u u u
1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R SECURE_SIZE Reserved
W
Reset u u u u u u u u u u u u u u u u
1. The default reset value of this register varies on the part basis, depending on NXP factory configurations.

---

*Page 856*

EdgeLock Secure Enclave (HSE_B)
Fields
Field Function
31 Hard Lock
HARD_LOCK This is a sticky field. If you write 1 to it, it remains 1 until next reset.
0b - Write access is allowed
1b - Write access is not allowed until next functional reset
Soft Lock
30
0b - Write access is allowed
SOFT_LOCK
1b - Write access is not allowed
Reserved
29-21
—
20-5 Secure Size
SECURE_SIZE Secure size region (in bytes) for PRAM1. This is 32-byte-aligned to ensure alignment with cache lines.
Reserved
4-0
—

#### 35.6.5 Configuration Code Flash Memory Active Block (CONFIG_CFPRL)

Offset
Register Offset
CONFIG_CFPRL 3Ch
Function
Resets on functional reset.
NOTE
The secure size will go to default or reset value on assertion of any DCF violation from HSE.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
HARD SOFT_
R Reserved SECURE_SIZE
_LO ... LO ...
W
Reset u u u u u u u u u u u u u u u u

---

*Page 857*

EdgeLock Secure Enclave (HSE_B)
1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R SECURE_SIZE Reserved
W
Reset u u u u u u u u u u u u u u u u
1. The default reset value of this register varies on the part basis, depending on NXP factory configurations.
Fields
Field Function
31 Hard Lock
HARD_LOCK After this field is configured it cannot be modified until a reset event.
0b - Write access is allowed
1b - Write access is not allowed until next functional reset
Soft Lock
30
0b - Write access is allowed
SOFT_LOCK
1b - Write access is not allowed
Reserved
29-21
—
20-13 Secure Size
SECURE_SIZE Flash memory active block secure size in bytes to align to 8 KB (sector) aligned write.
Reserved
12-0
—

#### 35.6.6 Configuration Code Flash Memory Passive Block (CONFIG_CFPRH)

Offset
Register Offset
CONFIG_CFPRH 40h
Function
Resets on functional reset.
NOTE
The secure size will go to default or reset value on assertion of any DCF violation from HSE.

---

*Page 858*

EdgeLock Secure Enclave (HSE_B)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
HARD SOFT_
R Reserved SECURE_SIZE
_LO ... LO ...
W
Reset
u u u u u u u u u u u u u u u u
1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R SECURE_SIZE Reserved
W
Reset u u u u u u u u u u u u u u u u
1. The default reset value of this register varies on the part basis, depending on NXP factory configurations.
Fields
Field Function
31 Hard Lock
HARD_LOCK After this field is configured, it cannot be modified until a reset event.
0b - Write access is allowed
1b - Write access is not allowed until next functional reset
Soft Lock
30
0b - Write access is allowed
SOFT_LOCK
1b - Write access is not allowed
Reserved
29-21
—
20-13 Secure Size
SECURE_SIZE Secure size region (in bytes) for flash memory passive block for alignment with 8 KB (sector) aligned
writes.
Reserved
12-0
—

#### 35.6.7 Configuration Data Flash Memory Protected Region (CONFIG_DFPR)

Offset
Register Offset
CONFIG_DFPR 44h

---

*Page 859*

EdgeLock Secure Enclave (HSE_B)
Function
Resets on functional reset.
NOTE
The secure size will go to default or reset value on assertion of any DCF violation from HSE.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
HARD SOFT_
R Reserved SECURE_SIZE
_LO ... LO ...
W
Reset
u u u u u u u u u u u u u u u u
1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R SECURE_SIZE Reserved
W
Reset u u u u u u u u u u u u u u u u
1. The default reset value of this register varies on the part basis, depending on NXP factory configurations.
Fields
Field Function
31 Hard Lock
HARD_LOCK After this field is configured, it cannot be modified until a reset event.
0b - Write access is allowed
1b - Write access is not allowed until next functional reset
Soft Lock
30
0b - Write access is allowed
SOFT_LOCK
1b - Write access is not allowed
Reserved
29-21
—
20-13 Secure Size
SECURE_SIZE Secure size region (in bytes) for data flash memory aligned to 8KB (sector) aligned writes.
Reserved
12-0
—

---

*Page 860*

EdgeLock Secure Enclave (HSE_B)

#### 35.6.8 Configuration Program and Erase Lock (CONFIG_PE_LOCK)

Offset
Register Offset
CONFIG_PE_LOCK 50h
Function
Resets on functional reset.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
PE_LO Reserv PE_LO
R Reserved
CK ... ed CK ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
PE_LO PE_LO PE_LO PE_LO
R Reserved
CK ... CK ... CK ... CK ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
Reserved
31-19
—
Program/Erase Lock for UTEST
18
0b - UTEST lock request is clear
PE_LOCK_UTE
ST
1b - UTEST lock requested
Reserved
17
—
Program/Erase Lock for Block 4
16
0b - Block 4 lock request is clear
PE_LOCK_BLO
CK_4
1b - Block 4 lock requested
Program/Erase Lock for Block 3
15
0b - Block 3 lock request is clear
PE_LOCK_BLO
CK_3
1b - Block 3 lock requested
Table continues on the next page...

---

*Page 861*

EdgeLock Secure Enclave (HSE_B)
Table continued from the previous page...
Field Function
Program/Erase Lock for Block 2
14
0b - Block 2 lock request is clear
PE_LOCK_BLO
CK_2
1b - Block 2 lock requested
Program/Erase Lock for Block 1
13
0b - Block 1 lock request is clear
PE_LOCK_BLO
CK_1
1b - Block 1 lock requested
Program/Erase Lock for Block 0
12
0b - Block 0 lock request is clear
PE_LOCK_BLO
CK_0
1b - Block 0 lock requested
Reserved
11-0
—

#### 35.6.9 Configuration RAM Protected Region Alternate (CONFIG_RAMPR_ALT)

Offset
Register Offset
CONFIG_RAMPR_ALT 54h
Function
Resets on functional reset.
NOTE
The secure size will go to default or reset value on assertion of any DCF violation from HSE.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R INVERT_VALUE_RAMPR
W
Reset
u u u u u u u u u u u u u u u u
1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R INVERT_VALUE_RAMPR
W
Reset u u u u u u u u u u u u u u u u
1. The default reset value of this register varies on the part basis, depending on NXP factory configurations.

---

*Page 862*

EdgeLock Secure Enclave (HSE_B)
Fields
Field Function
31-0 Invert Value RAMPR
INVERT_VALU Write inverted value of register CONFIG_RAMPR to CONFIG_RAMPR_ALT.
E_RAMPR

#### 35.6.10 Configuration Code Flash Memory Active Block Alternate (CONFIG_CFPRL_ALT)

Offset
Register Offset
CONFIG_CFPRL_ALT 58h
Function
Resets on functional reset.
NOTE
The secure size will go to default or reset value on assertion of any DCF violation from HSE.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R INVERT_VALUE_CFPRAB
W
Reset
u u u u u u u u u u u u u u u u
1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R INVERT_VALUE_CFPRAB
W
Reset u u u u u u u u u u u u u u u u
1. The default reset value of this register varies on the part basis, depending on NXP factory configurations.
Fields
Field Function
31-0 Invert Value CFPRAB
INVERT_VALU Write inverted value of register CONFIG_CFPRL to CONFIG_CFPRL_ALT.
E_CFPRAB

---

*Page 863*

EdgeLock Secure Enclave (HSE_B)

#### 35.6.11 Configuration Code Flash Memory Passive Block Alternate (CONFIG_CFPRH_ALT)

Offset
Register Offset
CONFIG_CFPRH_ALT 5Ch
Function
Resets on functional reset.
NOTE
The secure size will go to default or reset value on assertion of any DCF violation from HSE.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R INVERT_VALUE_CFPRP
W
Reset
u u u u u u u u u u u u u u u u
1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R INVERT_VALUE_CFPRP
W
Reset u u u u u u u u u u u u u u u u
1. The default reset value of this register varies on the part basis, depending on NXP factory configurations.
Fields
Field Function
31-0 Invert Value CFPRP
INVERT_VALU Write inverted value of register CONFIG_CFPRH to CONFIG_CFPRH_ALT.
E_CFPRP

#### 35.6.12 Configuration Data Flash Memory Protected Region Alternate (CONFIG_DFPR_ALT)

Offset
Register Offset
CONFIG_DFPR_ALT 60h
Function
Resets on functional reset.

---

*Page 864*

EdgeLock Secure Enclave (HSE_B)
NOTE
The secure size will go to default or reset value on assertion of any DCF violation from HSE.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R INVERT_VALUE_DFPR
W
Reset
u u u u u u u u u u u u u u u u
1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R INVERT_VALUE_DFPR
W
Reset u u u u u u u u u u u u u u u u
1. The default reset value of this register varies on the part basis, depending on NXP factory configurations.
Fields
Field Function
31-0 Invert Value CFPRP
INVERT_VALU Write inverted value of register CONFIG_DFPR to CONFIG_DFPR_ALT.
E_DFPR

#### 35.6.13 Configuration REG_GPR (CONFIG_REG_GPR)

Offset
Register Offset
CONFIG_REG_GPR 64h
Function
Resets on functional reset.

---

*Page 865*

EdgeLock Secure Enclave (HSE_B)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R APP_CORE_ACC Reserved
W
Reset 1 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R Reserved
FIRC_DIV_SEL
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1
Fields
Field Function
31-29 FIRC Divider
APP_CORE_A
NOTE
CC
While writing to this register, APP_CORE_ACC is RO and should not be changed
from 0b101.
101b - Application core can write this field [FIRC_DIV_SEL]
All other values - No access to application core
Reserved
28-2
—
1-0 FIRC Divider
FIRC_DIV_SEL Indicates this chip's FIRC clock division factor.
00b - Divided by 2
01b - Divided by 2
10b - Divided by 16
11b - Undivided

#### 35.7 Glossary

ADK/P Application debug key/password
AES Advanced encryption standard
CBC Cipher block chaining
CCM Counter with CBC MAC (Cipher block chaining message authentication code)
CFB Cipher feedback mode
CMAC Cipher-based message authentication code
CTR Counter-based block cipher mode

---

*Page 866*

EdgeLock Secure Enclave (HSE_B)
CVC Card verifiable certificates
Classic DH Classical Diffie–Hellman, a key exchange method
DRBG Deterministic random bit generator
ECB Electronic code book
ECC Elliptic curve cryptography
ECDSA Elliptic curve digital signature algorithm
ECDH Elliptic-curve Diffie–Hellman, a key exchange method
EdDSA Edwards-curve digital signature algorithm
FIPS Federal information processing standards
MUA Messaging unit A interface
MUB Messaging unit B interface
NIST National institute of standards and technology
OAEP Optimal asymmetric encryption padding
OFB Output feedback based block cipher mode
GCM Galois/counter mode
GMAC Galois message authentication code
HMAC Keyed-hash message authentication code
PKC Public key cryptographic engine
PKCS1 Public-key cryptography standards. PKCS provides the basic definitions of, and recommendations for
implementing the RSA algorithm.
POR Power-on reset
RNG Random number generator
RSA Rivest–Shamir–Adleman (a public key cryptosystem)
SHE Secure hardware extension
TRNG True random number generator
XCBC-MAC Extended ciphertext block chaining MAC
XTS XEX (XOR encrypt XOR) based tweaked-codebook mode with ciphertext stealing

---

*Page 867*

