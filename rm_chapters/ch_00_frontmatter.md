<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: frontmatter -->

*Page 1*

NXP Semiconductors Document identifier: MCXE31XRM
Reference Manual Rev. 3, 2026-05-26

# MCXE31x Reference Manual

### Supports MCXE315, MCXE316, MCXE317, and MCXE31B

---

*Page 2*

# Contents

### Chapter 1 About This Manual ................................................................................ 7

### Chapter 2 Introduction ......................................................................................... 14

### Chapter 3 Memory Map ....................................................................................... 23

### Chapter 4 Signal Multiplexing .............................................................................. 27

### Chapter 5 Cortex-M7 Overview ........................................................................... 39

### Chapter 6 Miscellaneous Control Module (MCM) ................................................ 43

### Chapter 7 Miscellaneous System Control Module (MSCM) ................................. 62

### Chapter 8 Virtualization Wrapper (VIRT_WRAPPER) ......................................... 94

### Chapter 9 System Integration Unit Lite2 (SIUL2) ............................................... 113

### Chapter 10 Touch Sensing Pin Coupling (TSPC) .............................................. 232

### Chapter 11 Crossbar Switch (AXBS) ................................................................. 240

### Chapter 12 Peripheral Bridge (AIPS_Lite) ......................................................... 252

### Chapter 13 Direct Memory Access Multiplexer (DMAMUX) .............................. 254

### Chapter 14 Enhanced Direct Memory Access (eDMA) ...................................... 264

### Chapter 15 Interrupt Monitor (INTM) .................................................................. 365

### Chapter 16 Semaphores2 (SEMA42) ................................................................ 374

### Chapter 17 Crossbar Integrity Checker (XBIC) .................................................. 383

### Chapter 18 Extended Resource Domain Controller (XRDC) ............................. 397

---

*Page 3*

Contents

### Chapter 19 Memory and Memory Interfaces ..................................................... 472

### Chapter 20 Embedded Flash Memory (c40asf) ................................................. 477

### Chapter 21 Flash Memory Controller (PFLASH) ............................................... 538

### Chapter 22 RAM Controller (PRAMC) ............................................................... 562

### Chapter 23 Clocking .......................................................................................... 569

### Chapter 24 Clock Generation Module (MC_CGM) ............................................ 637

### Chapter 25 Fast Internal RC Oscillator (FIRC) .................................................. 731

### Chapter 26 Slow Internal RC Oscillator (SIRC) ................................................. 734

### Chapter 27 Fast Crystal Oscillator Digital Controller (FXOSC) .......................... 737

### Chapter 28 Slow Crystal Oscillator Digital Controller (SXOSC) ......................... 744

### Chapter 29 PLL Digital Interface (PLLDIG) ........................................................ 750

### Chapter 30 Reset Overview ............................................................................... 766

### Chapter 31 Boot Overview ................................................................................. 788

### Chapter 32 Reset Generation Module (MC_RGM) ............................................ 814

### Chapter 33 Power-on Reset Watchdog (POR_WDG) ....................................... 842

### Chapter 34 Security Overview ........................................................................... 845

### Chapter 35 EdgeLock Secure Enclave (HSE_B) ............................................... 847

### Chapter 36 Device Configuration Format (DCF) records ................................... 867

### Chapter 37 Device Configuration Module General-Purpose Registers

### (DCM_GPR) ................................................................................................... 872

---

*Page 4*

Contents

### Chapter 38 Device Configuration Module (DCM) .............................................. 953

### Chapter 39 Messaging Unit (MU) ...................................................................... 995

### Chapter 40 Power Management ...................................................................... 1124

### Chapter 41 Power Management Controller (PMC for MCXE31B) ................... 1144

### Chapter 42 Power Management Controller (PMC for MCXE315, MCXE316,

### and MCXE317) ............................................................................................. 1156

### Chapter 43 Mode Entry Module (MC_ME) ....................................................... 1169

### Chapter 44 Power Control Unit (MC_PCU) ...................................................... 1275

### Chapter 45 Wakeup Unit (WKPU) ................................................................... 1278

### Chapter 46 Safety Overview ............................................................................ 1304

### Chapter 47 Error Injection Module (EIM) ......................................................... 1314

### Chapter 48 Error Reporting Module (ERM) ..................................................... 1370

### Chapter 49 Fault Collection and Control Unit (FCCU) ..................................... 1401

### Chapter 50 Self-test General-Purpose Registers (SELFTEST_GPR) ............. 1457

### Chapter 51 Self-Test Control Unit (STCU2) ..................................................... 1461

### Chapter 52 Register Protection (REG_PROT) ................................................. 1506

### Chapter 53 Clock Monitoring Unit – Frequency Check (CMU_FC) .................. 1518

### Chapter 54 Clock Monitoring Unit – Frequency Meter (CMU_FM) .................. 1534

### Chapter 55 Cyclic Redundancy Check (CRC) ................................................. 1542

### Chapter 56 Power Conversion and Motor Control (PCMC) ............................. 1553

---

*Page 5*

Contents

### Chapter 57 Analog-to-Digital Converter (ADC) ................................................ 1570

### Chapter 58 Low Power Comparator (LPCMP) ................................................. 1733

### Chapter 59 Logic Control Unit (LCU) ............................................................... 1774

### Chapter 60 Enhanced Modular IO Subsystem (eMIOS) .................................. 1850

### Chapter 61 Body Cross-triggering Unit (BCTU) ............................................... 1937

### Chapter 62 Trigger MUX (TRGMUX) ............................................................... 1976

### Chapter 63 Software Watchdog Timer (SWT) ................................................. 2038

### Chapter 64 System Timer Module (STM) ........................................................ 2055

### Chapter 65 Periodic Interrupt Timer (PIT) ........................................................ 2064

### Chapter 66 Real Time Clock (RTC) ................................................................. 2088

### Chapter 67 Low Power Serial Peripheral Interface (LPSPI) ............................ 2101

### Chapter 68 Low Power Inter-Integrated Circuit (LPI2C) .................................. 2155

### Chapter 69 Flexible I/O (FlexIO) ...................................................................... 2218

### Chapter 70 CAN (FlexCAN) ............................................................................. 2297

### Chapter 71 Synchronous Audio Interface (SAI) ............................................... 2469

### Chapter 72 Ethernet Media Access Controller (EMAC) ................................... 2519

### Chapter 73 Low Power Universal Asynchronous Receiver/Transmitter

### (LPUART) ..................................................................................................... 3109

### Chapter 74 Quad Serial Peripheral Interface (QuadSPI) ................................. 3162

### Chapter 75 Debug Subsystem ......................................................................... 3251

---

*Page 6*

Contents

### Chapter 76 JTAG Controller (JTAGC) ............................................................. 3294

### Chapter 77 JTAG Data Communication (JDC) ................................................ 3303

### Chapter 78 Temperature Sensor (TEMPSENSE) ............................................ 3312

### Appendix A Release notes ............................................................................... 3319

### Legal information ............................................................................................. 3357

---

*Page 7*

