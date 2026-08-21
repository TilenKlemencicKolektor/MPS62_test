<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 19 -->

# Chapter 19

# Memory and Memory Interfaces

#### 19.1 Introduction

This chapter discusses the configuration of memories and memory interfaces, such as flash memory, flash memory controller,
and SRAM.

#### 19.2 Flash memory controller and flash memory modules

For information on this, see the following chapters:
• Flash Memory Controller (PFLASH)
• Embedded Flash Memory

#### 19.3 Related information

Table 84. Related information
Topic Related chapters For additional information
System memory map Memory Map
See the memory map file attached to this document.
Clocking —
• Clocking Overview
• Clock Generation Module (MC_CGM)
Arm Cortex-M7 core Cortex-M7 Overview
XRDC Extended Resource Domain Controller
Direct-memory access
• Direct Memory Access Multiplexer See the DMAMUX map file attached to
(DMAMUX) this document.
• Enhanced Direct Memory Access
(eDMA)
EIM Error Injection Module —
ERM Error Reporting Module —

#### 19.4 SRAM access

In case a master accesses an SRAM with multi-bit ECC errors, the chip may respond as follows:
• Map all such faults to FCCU. The recommended reaction for the fault is to generate the functional reset.
• Map such faults to ERM. If the ERM interrupt is enabled, ERM generates an interrupt.

#### 19.5 Memories

The following table provides information on the types of memories, with their associated configurations, available in MCXE31.

---

*Page 473*

Memory and Memory Interfaces
Table 85. Memory configuration
Memory Size Configuration ECC or parity ECC or Diagnostic Applicability
(words × bits) parity information or
width error report
SRAM0 160 KB 32 KB with ECC ( SECDED ) 8 ERM
MCXE31B
STANDBY mode retention
(4096 × (64 + 8))
128 KB (16384 × (64 + 8))
96 KB 32 KB with ECC (SECDED) 8 ERM
MCXE317
STANDBY mode retention
(4096 × (64 + 8))
64 KB (8192 × (64 + 8))
32 KB 32 KB with ECC (SECDED) 8 ERM
MCXE315/
STANDBY mode retention
MCXE316
(4096 × (64 + 8))
CM7_0 I-cache 8 KB 4 KB (512 × (64 + 8)) ECC (SECDED) 8 ERM
MCXE31B
data
4 KB (512 × (64 + 8)) MCXE317
CM7_0 I-cache 672 bytes 336 bytes (128 × (21 + 7)) ECC (SECDED) 7 ERM MCXE315/
tag MCXE316
336 bytes (128 × (21 + 7))
CM7_0 D-cache 8 KB 1 KB (256 × (32 + 7)) ECC (SECDED) 7 ERM
data
1 KB (256 × (32 + 7))
1 KB (256 × (32 + 7))
1 KB (256 × (32 + 7))
1 KB (256 × (32 + 7))
1 KB (256 × (32 + 7))
1 KB (256 × (32 + 7))
1 KB (256 × (32 + 7))
CM7_0 D-cache 800 bytes 200 bytes (64 × (25 + 7)) ECC (SECDED) 7 ERM
tag
200 bytes (64 × (25 + 7))
200 bytes (64 × (25 + 7))
200 bytes (64 × (25 + 7))
DMA TCD 1 KB 1 KB (128 × (64 + 8)) ECC (SECDED) 8 ERM
MCXE31B
640 bytes 640 bytes (80 × (64 + 8)) ECC (SECDED) 8 ERM
MCXE317
MCXE315/
MCXE316
FlexCAN_0 5 KB 5 KB (640 × (64 + 40)) ECC (SECDED) 40 FlexCAN_0
MCXE31B
MCXE317
Table continues on the next page...

---

*Page 474*

Memory and Memory Interfaces
Table 85. Memory configuration (continued)
Memory Size Configuration ECC or parity ECC or Diagnostic Applicability
(words × bits) parity information or
width error report
FlexCAN_1 1920 1920 bytes ECC (SECDED) 40 FlexCAN_1
MCXE315/
bytes (240 × (64 + 40))
MCXE316
FlexCAN_2 1920 1920 bytes ECC (SECDED) 40 FlexCAN_2
bytes (240 × (64 + 40))
FlexCAN_3 1152 1152 bytes ECC (SECDED) 40 FlexCAN_3
MCXE31B
bytes (144 × (64 + 40))
MCXE317
FlexCAN_4 1152 1152 bytes ECC(SECDED) 40 FlexCAN_4
MCXE31B
bytes (144 × (64 + 40))
MCXE317
FlexCAN_5 1152 1152 bytes ECC (SECDED) 40 FlexCAN_5
bytes (144 × (64 + 40))
QuadSPI RAM 1 KB 1 KB (128×64) No 0 Not applicable
MCXE31B
EMAC TX 8960 4480 bytes ECC (SECDED) 7 EMAC
bytes (1024 × (35 + 7))
4480 bytes
(1024 × (35 + 7))
EMAC RX 8960 4480 bytes ECC (SECDED) 7 EMAC
bytes (1024 × (35 + 7))
4480 bytes
(1024 × (35 + 7))
EMAC TSN 1664 1664 bytes (512 × (26 + 7)) ECC (SECDED) 7 EMAC
bytes
EMAC 960 bytes 960 bytes (80 × (96 + 8)) ECC (SECDED) 8 EMAC
RXPARSER
QuadSPI TX 320 bytes 320 bytes (80×32) No 0 Not applicable
Cortex-M7 cluster 1 KB 1 KB (128×64) No 0 Not applicable
MCXE31B
ETF ETMI
MCXE317
Cortex-M7 cluster 2 KB 2 KB (128×128) No 0 Not applicable
MCXE315/
ETF ETMD
MCXE316
HTM ETF 1 KB 1 KB (128×64) No 0 Not applicable
Shared system 2 KB 2 KB (256×64) No 0 Not applicable
ETF
HSE secure RAM 64 KB 64 KB (8192 × (64 + 8)) ECC (SECDED) 8 HSE
HSE PKC 8 KB 4 KB (512 × (64 + 8)) ECC (SECDED) 8 HSE
4 KB (512 × (64 + 8))
HSE MTB 1 KB 1 KB (256×32) No 0 HSE
Table continues on the next page...

---

*Page 475*

Memory and Memory Interfaces
Table 85. Memory configuration (continued)
Memory Size Configuration ECC or parity ECC or Diagnostic Applicability
(words × bits) parity information or
width error report
CM7_0 ITCM 32 KB 32 KB (4096 × (64 + 8)) ECC (SECDED) 8 ERM
MCXE31B
CM7_0_ DTCM 64 KB 32 KB (8192 × (32 + 8)) ECC (SECDED) 8 ERM MCXE317
32 KB (8192 × (32 + 8)) MCXE315/
MCXE316

#### 19.6 Recommendations for ARM memories

As per ARM M-7 Safety manual, following considerations must be ensured for proper operation of ARM memories:
• ITCM and DTCM must be properly initialized with correct ECC before any read operation to avoid any code runaway or
software malfucntion or core lockup.
NOTE
ITCM must be initialized with 64-bit writes whereas DTCM can be initialized with 32-bit writes also.
• To safely disable TCM:
1. Clear ITCMCR[EN] or DTCMCR[EN] as required. See ARM Cortex-M7 Devices Generic User Guide for details on
ITCMCR and DTCMCR register.
2. Execute DSB instruction
3. Execute ISB instruction
NOTE
Care must be taken if disabling the ITCM while executing from it. In this case, software must ensure that the
switch-off code is stored in the L2 code memory region from where execution continues when the ITCM is disabled.
• To safely disable the I-cache:
1. Clear CCR[IC]. See ARM Cortex-M7 Devices Generic User Guide for details on CCR register.
2. Execute DSB instruction
3. Execute ISB instruction
• To safely disable the D-cache:
1. Clean and invalidate non-WT locations in D-cache
2. Clear CCR[DC]
3. Execute DSB instruction
See Table 86 for details on memory ECC initialization.

#### 19.7 Memory ECC initialization summary

The table below summarizes memory ECC initialization.
Table 86. Memory ECC initialization summary
Memory Write access size Masters
Table continues on the next page...

---

*Page 476*

Memory and Memory Interfaces
Table 86. Memory ECC initialization summary (continued)
CM7_ n CM7_ m eDMA
SRAM 64-bits only System System System
CM7_n ITCM 64-bits only Direct or Backdoor Backdoor Not possible
CM7_n DTCM 32-bits or 64-bits Direct or Backdoor Backdoor Backdoor
CM7_m ITCM 64-bits only Backdoor Direct or Backdoor Not possible
CM7_m DTCM 32-bits or 64-bits Backdoor Direct or Backdoor Backdoor

#### 19.8 Glossary

DTCM Data tightly coupled memory
ECC Error code correction
ETF Embedded trace FIFO
ETMD Embedded trace macrocell-data
ETMI Embedded trace macrocell-instruction
ITCM Instruction tightly coupled memory
MTB Macrocell trace buffer
PKC Public key cryptography
RXPARSER Receive parser memory
SECDED Single error correction double error detection
SRAM Static random access memory
TSN Time sensitive network

---

*Page 477*

