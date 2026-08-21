<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 43 -->

# Chapter 43

# Mode Entry Module (MC_ME)

#### 43.1 Chip-specific MC_ME information

#### 43.1.1 MC_ME modes

This chip implements these modes:
• Reset
• Run
• Standby
The chip always enters Run mode after exiting Reset mode wherein you can configure the chip to perform its computational and
communication functions. In Run mode:
• The chip remains fully powered. The boot core is the only core enabled on Run entry.
• You can enable and disable application cores and peripherals as needed, based on functional and power requirements.
• You can configure the pins and self-test as needed. See "Self-Test programming sequence" section in the STCU2 chapter
for the self-test programming sequence to be followed before initiating self-test, in which:
— Pins are safe-stated.
— No computational or communication activities are possible.
• The chip automatically enters Reset mode after self-test (BIST) is complete.

#### 43.1.2 MC_ME partition mapping of cores and peripherals

MC_ME provides registers and interface signals to support multiple partitions. These MC_ME partitions are different from the
chip's LBIST partitions described in the "Safety Overview" chapter. This chip has three MC_ME partitions:
• Partition 0: Contains application cores and the on/off-platform slots on AIPS_0 bridge
• Partition 1: Contains the on/off-platform slots on AIPS_1 bridge
• Partition 2: Contains the on/off-platform slots on AIPS_2 bridge
Table 215 and Table 216 specify the core and peripheral mapping on MC_ME partitions and their associated clock
gating possiblities.
MC_ME also provides provisions to control the booting address for application cores, which can be configured to start from a
nondefault address location by appropriately configuring PRTN x _CORE n _ADDR[ADDR].

#### 43.1.3 Core clock gating

MC_ME has individual core-clock enable fields that gate the application core clocks, which can also be clock gated by executing
Waiting for Interrupt (WFI).
You can enable the application cores by configuring the respective CCE fields. See Application core initialization process and
Application core shutdown process for proper initialization and shutdown of application cores. There is no clock control for the
ELE_HSEB core. It needs to be only put into WFI if required to be shutdown (for example, in Standby mode).
Table 215. MC_ME partition core mapping
Core MC_ME partition MC_ME clock enable register field
Cortex-M7_0 0 MC_ME.PRTN0_CORE0_PCONF[CCE]

---

*Page 1170*

Mode Entry Module (MC_ME)

#### 43.1.4 Peripheral clock gating

See Peripheral initialization process and Peripheral shutdown process for proper initialization and shutdown of peripherals. See
Table 216 for peripheral clock gating possibilities.
The application core can program the reserved configurations.
NOTE
Before accessing the registers of a peripheral to start using it, its clock must be turned on, otherwise, a Hard-Fault
event will occur.
Table 216. MC_ME partition peripheral mapping and clock control
AIPS Peripheral MC_ME's MC_ME MC_ME MC_M MC_M MC_M On IPS
1
peripher description PRTN n _COFB n _CLKEN peripher peripher E E E platfor slot
al register fields al al slot COFB CLKE defaul m numbe
control number prese N t r
register in nt prese CLKI
partition nt N
0 Reserved PRTN0_COFB0_CLKEN[REQ 0 0 0 0 0 Yes 0
0]
0 Reserved PRTN0_COFB0_CLKEN[REQ 1 1 0 0 0 Yes 1
1]
0 Reserved PRTN0_COFB0_CLKEN[REQ 2 2 0 0 0 Yes 2
2]
0 Reserved PRTN0_COFB0_CLKEN[REQ 3 3 0 0 0 Yes 3
3]
0 Reserved PRTN0_COFB0_CLKEN[REQ 4 4 0 0 0 Yes 4
4]
0 Reserved PRTN0_COFB0_CLKEN[REQ 5 5 0 0 0 Yes 5
5]
0 Reserved PRTN0_COFB0_CLKEN[REQ 6 6 0 0 0 Yes 6
6]
0 Reserved PRTN0_COFB0_CLKEN[REQ 7 7 0 0 0 Yes 7
7]
0 Reserved PRTN0_COFB0_CLKEN[REQ 8 8 0 0 0 Yes 8
8]
0 Reserved PRTN0_COFB0_CLKEN[REQ 9 9 0 0 0 Yes 9
9]
0 Reserved PRTN0_COFB0_CLKEN[REQ 10 10 0 0 0 Yes 10
10]
0 Reserved PRTN0_COFB0_CLKEN[REQ 11 11 0 0 0 Yes 11
11]
0 Reserved PRTN0_COFB0_CLKEN[REQ 12 12 0 0 0 Yes 12
12]
0 Reserved PRTN0_COFB0_CLKEN[REQ 13 13 0 0 0 Yes 13
13]
Table continues on the next page...

---

*Page 1171*

Mode Entry Module (MC_ME)
Table 216. MC_ME partition peripheral mapping and clock control (continued)
AIPS Peripheral MC_ME's MC_ME MC_ME MC_M MC_M MC_M On IPS
1
peripher description PRTN n_COFB n_CLKEN peripher peripher E E E platfor slot
al register fields al al slot COFB CLKE defaul m numbe
control number prese N t r
register in nt prese CLKI
partition nt N
0 Reserved PRTN0_COFB0_CLKEN[REQ 14 14 0 0 0 Yes 14
14]
0 Reserved PRTN0_COFB0_CLKEN[REQ 15 15 0 0 0 Yes 15
15]
0 Reserved PRTN0_COFB0_CLKEN[REQ 16 16 0 0 0 Yes 16
16]
0 Reserved PRTN0_COFB0_CLKEN[REQ 17 17 0 0 0 Yes 17
17]
0 Reserved PRTN0_COFB0_CLKEN[REQ 18 18 0 0 0 Yes 18
18]
0 Reserved PRTN0_COFB0_CLKEN[REQ 19 19 0 0 0 Yes 19
19]
0 Reserved PRTN0_COFB0_CLKEN[REQ 20 20 0 0 0 Yes 20
20]
0 Reserved PRTN0_COFB0_CLKEN[REQ 21 21 0 0 0 Yes 21
21]
0 Reserved PRTN0_COFB0_CLKEN[REQ 22 22 0 0 0 Yes 22
22]
0 Reserved PRTN0_COFB0_CLKEN[REQ 23 23 0 0 0 Yes 23
23]
0 Reserved PRTN0_COFB0_CLKEN[REQ 24 24 0 0 0 Yes 24
24]
0 Reserved PRTN0_COFB0_CLKEN[REQ 25 25 0 0 0 Yes 25
25]
0 Reserved PRTN0_COFB0_CLKEN[REQ 26 26 0 0 0 Yes 26
26]
0 Reserved PRTN0_COFB0_CLKEN[REQ 27 27 0 0 0 Yes 27
27]
0 Reserved PRTN0_COFB0_CLKEN[REQ 28 28 0 0 0 Yes 28
28]
0 Reserved PRTN0_COFB0_CLKEN[REQ 29 29 0 0 0 Yes 29
29]
0 Reserved PRTN0_COFB0_CLKEN[REQ 30 30 0 0 0 Yes 30
30]
Table continues on the next page...

---

*Page 1172*

Mode Entry Module (MC_ME)
Table 216. MC_ME partition peripheral mapping and clock control (continued)
AIPS Peripheral MC_ME's MC_ME MC_ME MC_M MC_M MC_M On IPS
1
peripher description PRTN n_COFB n_CLKEN peripher peripher E E E platfor slot
al register fields al al slot COFB CLKE defaul m numbe
control number prese N t r
register in nt prese CLKI
partition nt N
0 Reserved PRTN0_COFB0_CLKEN[REQ 31 31 0 0 0 Yes 31
31]
0 TRGMUX PRTN0_COFB1_CLKEN[REQ 32 32 1 1 0 No 0
32]
0 BCTU PRTN0_COFB1_CLKEN[REQ 33 33 1 1 0 No 1
33]
0 eMIOS_0 PRTN0_COFB1_CLKEN[REQ 34 34 1 1 0 No 2
34]
0 eMIOS_1 PRTN0_COFB1_CLKEN[REQ 35 35 1 1 0 No 3
35]
0 eMIOS_2 PRTN0_COFB1_CLKEN[REQ 36 36 1 1 0 No 4
36]
0 Reserved PRTN0_COFB1_CLKEN[REQ 37 37 0 0 0 No 5
37]
0 LCU_0 PRTN0_COFB1_CLKEN[REQ 38 38 1 1 0 No 6
38]
0 LCU_1 PRTN0_COFB1_CLKEN[REQ 39 39 1 1 0 No 7
39]
0 ADC_0 PRTN0_COFB1_CLKEN[REQ 40 40 1 1 0 No 8
40]
0 ADC_1 PRTN0_COFB1_CLKEN[REQ 41 41 1 1 0 No 9
41]
0 ADC_2 PRTN0_COFB1_CLKEN[REQ 42 42 1 1 0 No 10
42]
0 Reserved PRTN0_COFB1_CLKEN[REQ 43 43 0 0 0 No 11
43]
0 PIT_0 PRTN0_COFB1_CLKEN[REQ 44 44 1 1 1 No 12
44]
0 PIT_1 PRTN0_COFB1_CLKEN[REQ 45 45 1 1 0 No 13
45]
0 MU_A PRTN0_COFB1_CLKEN[REQ 46 46 1 1 0 No 14
46]
0 MU_B PRTN0_COFB1_CLKEN[REQ 47 47 1 1 0 No 15
47]
Table continues on the next page...

---

*Page 1173*

Mode Entry Module (MC_ME)
Table 216. MC_ME partition peripheral mapping and clock control (continued)
AIPS Peripheral MC_ME's MC_ME MC_ME MC_M MC_M MC_M On IPS
1
peripher description PRTN n_COFB n_CLKEN peripher peripher E E E platfor slot
al register fields al al slot COFB CLKE defaul m numbe
control number prese N t r
register in nt prese CLKI
partition nt N
1 AXBS switch PRTN1_COFB0_CLKEN[REQ 128 0 1 0 1 Yes 0
0]
1 AXBS_0 PRTN1_COFB0_CLKEN[REQ 129 1 1 0 1 Yes 1
1]
1 AXBS_1 PRTN1_COFB0_CLKEN[REQ 130 2 1 0 1 Yes 2
2]
1 eDMA control and PRTN1_COFB0_CLKEN[REQ 131 3 1 1 0 Yes 3
status (MP_CSR; 3]
MP_ES; MP_HRS)
1 eDMA transfer PRTN1_COFB0_CLKEN[REQ 132 4 1 1 0 Yes 4
control descriptor 0 4]
1 eDMA transfer PRTN1_COFB0_CLKEN[REQ 133 5 1 1 0 Yes 5
control descriptor 1 5]
1 eDMA transfer PRTN1_COFB0_CLKEN[REQ 134 6 1 1 0 Yes 6
control descriptor 2 6]
1 eDMA transfer PRTN1_COFB0_CLKEN[REQ 135 7 1 1 0 Yes 7
control descriptor 3 7]
1 eDMA transfer PRTN1_COFB0_CLKEN[REQ 136 8 1 1 0 Yes 8
control descriptor 4 8]
1 eDMA transfer PRTN1_COFB0_CLKEN[REQ 137 9 1 1 0 Yes 9
control descriptor 5 9]
1 eDMA transfer PRTN1_COFB0_CLKEN[REQ 138 10 1 1 0 Yes 10
control descriptor 6 10]
1 eDMA transfer PRTN1_COFB0_CLKEN[REQ 139 11 1 1 0 Yes 11
control descriptor 7 11]
1 eDMA transfer PRTN1_COFB0_CLKEN[REQ 140 12 1 1 0 Yes 12
control descriptor 8 12]
1 eDMA transfer PRTN1_COFB0_CLKEN[REQ 141 13 1 1 0 Yes 13
control descriptor 9 13]
1 eDMA transfer PRTN1_COFB0_CLKEN[REQ 142 14 1 1 0 Yes 14
control descriptor 14]
10
1 eDMA transfer PRTN1_COFB0_CLKEN[REQ 143 15 1 1 0 Yes 15
control descriptor 15]
11
Table continues on the next page...

---

*Page 1174*

Mode Entry Module (MC_ME)
Table 216. MC_ME partition peripheral mapping and clock control (continued)
AIPS Peripheral MC_ME's MC_ME MC_ME MC_M MC_M MC_M On IPS
1
peripher description PRTN n_COFB n_CLKEN peripher peripher E E E platfor slot
al register fields al al slot COFB CLKE defaul m numbe
control number prese N t r
register in nt prese CLKI
partition nt N
1 Debug APB page0 PRTN1_COFB0_CLKEN[REQ 144 16 1 0 1 Yes 16
16]
1 Debug APB page1 PRTN1_COFB0_CLKEN[REQ 145 17 1 0 1 Yes 17
17]
1 Debug APB page2 PRTN1_COFB0_CLKEN[REQ 146 18 1 0 1 Yes 18
18]
1 Debug APB page3 PRTN1_COFB0_CLKEN[REQ 147 19 1 0 1 Yes 19
19]
1 Debug APB paged PRTN1_COFB0_CLKEN[REQ 148 20 1 0 1 Yes 20
area 20]
1 SDA_AP PRTN1_COFB0_CLKEN[REQ 149 21 1 1 1 Yes 21
21]
1 EIM PRTN1_COFB0_CLKEN[REQ 150 22 1 1 0 Yes 22
22]
1 ERM PRTN1_COFB0_CLKEN[REQ 151 23 1 1 0 Yes 23
23]
1 MSCM PRTN1_COFB0_CLKEN[REQ 152 24 1 1 0 Yes 24
24]
1 PRAMC_0 PRTN1_COFB0_CLKEN[REQ 153 25 1 0 1 Yes 25
25]
1 Flash memory PRTN1_COFB0_CLKEN[REQ 154 26 1 0 1 Yes 26
controller 26]
1 Flash memory PRTN1_COFB0_CLKEN[REQ 155 27 1 0 1 Yes 27
controller alternate 27]
1 SWT_0 PRTN1_COFB0_CLKEN[REQ 156 28 1 1 1 Yes 28
28]
1 STM_0 PRTN1_COFB0_CLKEN[REQ 157 29 1 1 0 Yes 29
29]
1 XRDC PRTN1_COFB0_CLKEN[REQ 158 30 1 0 1 Yes 30
30]
1 INTM PRTN1_COFB0_CLKEN[REQ 159 31 1 1 0 Yes 31
31]
1 DMAMUX_0 PRTN1_COFB1_CLKEN[REQ 160 32 1 1 0 No 0
32]
Table continues on the next page...

---

*Page 1175*

Mode Entry Module (MC_ME)
Table 216. MC_ME partition peripheral mapping and clock control (continued)
AIPS Peripheral MC_ME's MC_ME MC_ME MC_M MC_M MC_M On IPS
1
peripher description PRTN n_COFB n_CLKEN peripher peripher E E E platfor slot
al register fields al al slot COFB CLKE defaul m numbe
control number prese N t r
register in nt prese CLKI
partition nt N
1 DMAMUX_1 PRTN1_COFB1_CLKEN[REQ 161 33 1 1 0 No 1
33]
1 RTC PRTN1_COFB1_CLKEN[REQ 162 34 1 1 1 No 2
34]
1 MC_RGM PRTN1_COFB1_CLKEN[REQ 163 35 1 0 1 No 3
35]
1 SIUL2_VIRTWRA PRTN1_COFB1_CLKEN[REQ 164 36 1 0 1 No 4
PPER_PDAC0 36]
1 SIUL2_VIRTWRA PRTN1_COFB1_CLKEN[REQ 165 37 1 0 1 No 5
PPER_PDAC0 37]
1 SIUL2_VIRTWRA PRTN1_COFB1_CLKEN[REQ 166 38 1 0 1 No 6
PPER_PDAC1 38]
1 SIUL2_VIRTWRA PRTN1_COFB1_CLKEN[REQ 167 39 1 0 1 No 7
PPER_PDAC1 39]
1 SIUL2_VIRTWRA PRTN1_COFB1_CLKEN[REQ 168 40 1 0 1 No 8
PPER_PDAC2 40]
1 SIUL2_VIRTWRA PRTN1_COFB1_CLKEN[REQ 169 41 1 0 1 No 9
PPER_PDAC2 41]
1 SIUL2_VIRTWRA PRTN1_COFB1_CLKEN[REQ 170 42 1 1 1 No 10
PPER 42]
1 SSCM PRTN1_COFB1_CLKEN[REQ 171 43 1 0 1 No 11
43]
1 Reserved PRTN1_COFB1_CLKEN[REQ 172 44 0 0 0 No 12
44]
1 WKPU PRTN1_COFB1_CLKEN[REQ 173 45 1 1 1 No 13
45]
1 Reserved PRTN1_COFB1_CLKEN[REQ 174 46 0 0 0 No 14
46]
1 CMU 0-5 PRTN1_COFB1_CLKEN[REQ 175 47 1 1 0 No 15
47]
1 Reserved PRTN1_COFB1_CLKEN[REQ 176 48 0 0 0 No 16
48]
1 TSPC PRTN1_COFB1_CLKEN[REQ 177 49 1 1 1 No 17
49]
Table continues on the next page...

---

*Page 1176*

Mode Entry Module (MC_ME)
Table 216. MC_ME partition peripheral mapping and clock control (continued)
AIPS Peripheral MC_ME's MC_ME MC_ME MC_M MC_M MC_M On IPS
1
peripher description PRTN n_COFB n_CLKEN peripher peripher E E E platfor slot
al register fields al al slot COFB CLKE defaul m numbe
control number prese N t r
register in nt prese CLKI
partition nt N
1 32 kHz SIRC PRTN1_COFB1_CLKEN[REQ 178 50 1 0 1 No 18
50]
1 32 kHz SXOSC PRTN1_COFB1_CLKEN[REQ 179 51 1 1 1 No 19
51]
1 48 MHz FIRC PRTN1_COFB1_CLKEN[REQ 180 52 1 0 1 No 20
52]
1 8-40 MHz FXOSC PRTN1_COFB1_CLKEN[REQ 181 53 1 1 1 No 21
53]
1 MC_CGM PRTN1_COFB1_CLKEN[REQ 182 54 1 0 1 No 22
54]
1 MC_ME PRTN1_COFB1_CLKEN[REQ 183 55 1 0 1 No 23
55]
1 Frequency- PRTN1_COFB1_CLKEN[REQ 184 56 1 1 0 No 24
modulated PLL 56]
1 Reserved PRTN1_COFB1_CLKEN[REQ 185 57 0 0 0 No 25
57]
1 PMC PRTN1_COFB1_CLKEN[REQ 186 58 1 0 1 No 26
58]
1 Flash memory PRTN1_COFB1_CLKEN[REQ 187 59 1 0 1 No 27
controller 59]
1 Flash memory PRTN1_COFB1_CLKEN[REQ 188 60 1 0 1 No 28
controller alternate 60]
1 Reserved PRTN1_COFB1_CLKEN[REQ 189 61 0 0 0 No 29
61]
1 Reserved PRTN1_COFB1_CLKEN[REQ 190 62 0 0 0 No 30
62]
1 PIT_2 PRTN1_COFB1_CLKEN[REQ 191 63 1 1 0 No 31
63]
1 Reserved PRTN1_COFB2_CLKEN[REQ 192 64 0 0 0 No 32
64]
1 FlexCAN_0 PRTN1_COFB2_CLKEN[REQ 193 65 1 1 0 No 33
65]
1 FlexCAN_1 PRTN1_COFB2_CLKEN[REQ 194 66 1 1 0 No 34
66]
Table continues on the next page...

---

*Page 1177*

Mode Entry Module (MC_ME)
Table 216. MC_ME partition peripheral mapping and clock control (continued)
AIPS Peripheral MC_ME's MC_ME MC_ME MC_M MC_M MC_M On IPS
1
peripher description PRTN n_COFB n_CLKEN peripher peripher E E E platfor slot
al register fields al al slot COFB CLKE defaul m numbe
control number prese N t r
register in nt prese CLKI
partition nt N
1 FlexCAN_2 PRTN1_COFB2_CLKEN[REQ 195 67 1 1 0 No 35
67]
1 FlexCAN_3 PRTN1_COFB2_CLKEN[REQ 196 68 1 1 0 No 36
68]
1 FlexCAN_4 PRTN1_COFB2_CLKEN[REQ 197 69 1 1 0 No 37
69]
1 FlexCAN_5 PRTN1_COFB2_CLKEN[REQ 198 70 1 1 0 No 38
70]
1 Reserved PRTN1_COFB2_CLKEN[REQ 199 71 0 0 0 No 39
71]
1 Reserved PRTN1_COFB2_CLKEN[REQ 200 72 0 0 0 No 40
72]
1 FlexIO PRTN1_COFB2_CLKEN[REQ 201 73 1 1 0 No 41
73]
1 LPUART_0 PRTN1_COFB2_CLKEN[REQ 202 74 1 1 0 No 42
74]
1 LPUART_1 PRTN1_COFB2_CLKEN[REQ 203 75 1 1 0 No 43
75]
1 LPUART_2 PRTN1_COFB2_CLKEN[REQ 204 76 1 1 0 No 44
76]
1 LPUART_3 PRTN1_COFB2_CLKEN[REQ 205 77 1 1 0 No 45
77]
1 LPUART_4 PRTN1_COFB2_CLKEN[REQ 206 78 1 1 0 No 46
78]
1 LPUART_5 PRTN1_COFB2_CLKEN[REQ 207 79 1 1 0 No 47
79]
1 LPUART_6 PRTN1_COFB2_CLKEN[REQ 208 80 1 1 0 No 48
80]
1 LPUART_7 PRTN1_COFB2_CLKEN[REQ 209 81 1 1 0 No 49
81]
1 Reserved PRTN1_COFB2_CLKEN[REQ 210 82 0 0 0 No 50
82]
1 Reserved PRTN1_COFB2_CLKEN[REQ 211 83 0 0 0 No 51
83]
Table continues on the next page...

---

*Page 1178*

Mode Entry Module (MC_ME)
Table 216. MC_ME partition peripheral mapping and clock control (continued)
AIPS Peripheral MC_ME's MC_ME MC_ME MC_M MC_M MC_M On IPS
1
peripher description PRTN n_COFB n_CLKEN peripher peripher E E E platfor slot
al register fields al al slot COFB CLKE defaul m numbe
control number prese N t r
register in nt prese CLKI
partition nt N
1 LPI2C_0 PRTN1_COFB2_CLKEN[REQ 212 84 1 1 0 No 52
84]
1 LPI2C_1 PRTN1_COFB2_CLKEN[REQ 213 85 1 1 0 No 53
85]
1 LPSPI_0 PRTN1_COFB2_CLKEN[REQ 214 86 1 1 0 No 54
86]
1 LPSPI_1 PRTN1_COFB2_CLKEN[REQ 215 87 1 1 0 No 55
87]
1 LPSPI_2 PRTN1_COFB2_CLKEN[REQ 216 88 1 1 0 No 56
88]
1 LPSPI_3 PRTN1_COFB2_CLKEN[REQ 217 89 1 1 0 No 57
89]
1 Reserved PRTN1_COFB2_CLKEN[REQ 218 90 0 0 0 No 58
90]
1 SAI_0 PRTN1_COFB2_CLKEN[REQ 219 91 1 1 0 No 59
91]
1 LPCMP_0 PRTN1_COFB2_CLKEN[REQ 220 92 1 1 1 No 60
92]
1 LPCMP_1 PRTN1_COFB2_CLKEN[REQ 221 93 1 1 1 No 61
93]
1 Reserved PRTN1_COFB2_CLKEN[REQ 222 94 0 0 0 No 62
94]
1 TempSense PRTN1_COFB2_CLKEN[REQ 223 95 1 1 0 No 63
95]
1 CRC PRTN1_COFB3_CLKEN[REQ 224 96 1 1 0 No 64
96]
1 FCCU (+FOSU) PRTN1_COFB3_CLKEN[REQ 225 97 1 0 1 No 65
97]
1 MTR PRTN1_COFB3_CLKEN[REQ 226 98 1 0 1 No 66
98]
1 HSE_MU0_B PRTN1_COFB3_CLKEN[REQ 227 99 1 0 1 No 67
99]
1 Reserved PRTN1_COFB3_CLKEN[REQ 228 100 0 0 0 No 68
100]
Table continues on the next page...

---

*Page 1179*

Mode Entry Module (MC_ME)
Table 216. MC_ME partition peripheral mapping and clock control (continued)
AIPS Peripheral MC_ME's MC_ME MC_ME MC_M MC_M MC_M On IPS
1
peripher description PRTN n_COFB n_CLKEN peripher peripher E E E platfor slot
al register fields al al slot COFB CLKE defaul m numbe
control number prese N t r
register in nt prese CLKI
partition nt N
1 JDC PRTN1_COFB3_CLKEN[REQ 229 101 1 0 1 No 69
101]
1 PRTN1_COFB3_CLKEN[REQ 230 102 1 1 1 No 70
Reserved
102]
1 Configuration GPR PRTN1_COFB3_CLKEN[REQ 231 103 1 0 1 No 71
103]
1 STCU2 PRTN1_COFB3_CLKEN[REQ 232 104 1 1 1 No 72
104]
1 Reserved PRTN1_COFB3_CLKEN[REQ 233 105 1 0 1 No 73
105]
1 Reserved PRTN1_COFB3_CLKEN[REQ 234 106 1 0 1 No 74
106]
1 Reserved PRTN1_COFB3_CLKEN[REQ 235 107 1 0 1 No 75
107]
2
1 SELFTEST_GPR PRTN1_COFB3_CLKEN[REQ 236 108 1 0 1 No 76
108]
1 PRTN1_COFB3_CLKEN[REQ 237 109 0 0 0 No 77
Reserved
109]
1 PRTN1_COFB3_CLKEN[REQ 238 110 1 0 1 No 78
Reserved
110]
2 XBIC (TCM PRTN2_COFB0_CLKEN[REQ 256 0 1 0 1 Yes 0
backdoor 0]
AHB_splitter)
2 XBIC (eDMA PRTN2_COFB0_CLKEN[REQ 257 1 1 0 1 Yes 1
AXBS-Lite) 1]
2 Reserved PRTN2_COFB0_CLKEN[REQ 258 2 0 0 0 Yes 2
2]
2 Reserved PRTN2_COFB0_CLKEN[REQ 259 3 0 0 0 Yes 3
3]
2 eDMA transfer PRTN2_COFB0_CLKEN[REQ 260 4 1 1 0 Yes 4
control descriptor 4]
12
2 eDMA transfer PRTN2_COFB0_CLKEN[REQ 261 5 1 1 0 Yes 5
control descriptor 5]
13
Table continues on the next page...

---

*Page 1180*

Mode Entry Module (MC_ME)
Table 216. MC_ME partition peripheral mapping and clock control (continued)
AIPS Peripheral MC_ME's MC_ME MC_ME MC_M MC_M MC_M On IPS
1
peripher description PRTN n_COFB n_CLKEN peripher peripher E E E platfor slot
al register fields al al slot COFB CLKE defaul m numbe
control number prese N t r
register in nt prese CLKI
partition nt N
2 eDMA transfer PRTN2_COFB0_CLKEN[REQ 262 6 1 1 0 Yes 6
control descriptor 6]
14
2 eDMA transfer PRTN2_COFB0_CLKEN[REQ 263 7 1 1 0 Yes 7
control descriptor 7]
15
2 eDMA transfer PRTN2_COFB0_CLKEN[REQ 264 8 1 1 0 Yes 8
control descriptor 8]
16
2 eDMA transfer PRTN2_COFB0_CLKEN[REQ 265 9 1 1 0 Yes 9
control descriptor 9]
17
2 eDMA transfer PRTN2_COFB0_CLKEN[REQ 266 10 1 1 0 Yes 10
control descriptor 10]
18
2 eDMA transfer PRTN2_COFB0_CLKEN[REQ 267 11 1 1 0 Yes 11
control descriptor 11]
19
2 eDMA transfer PRTN2_COFB0_CLKEN[REQ 268 12 1 1 0 Yes 12
control descriptor 12]
20
2 eDMA transfer PRTN2_COFB0_CLKEN[REQ 269 13 1 1 0 Yes 13
control descriptor 13]
21
2 eDMA transfer PRTN2_COFB0_CLKEN[REQ 270 14 1 1 0 Yes 14
control descriptor 14]
22
2 eDMA transfer PRTN2_COFB0_CLKEN[REQ 271 15 1 1 0 Yes 15
control descriptor 15]
23
2 eDMA transfer PRTN2_COFB0_CLKEN[REQ 272 16 1 1 0 Yes 16
control descriptor 16]
24
2 eDMA transfer PRTN2_COFB0_CLKEN[REQ 273 17 1 1 0 Yes 17
control descriptor 17]
25
Table continues on the next page...

---

*Page 1181*

Mode Entry Module (MC_ME)
Table 216. MC_ME partition peripheral mapping and clock control (continued)
AIPS Peripheral MC_ME's MC_ME MC_ME MC_M MC_M MC_M On IPS
1
peripher description PRTN n_COFB n_CLKEN peripher peripher E E E platfor slot
al register fields al al slot COFB CLKE defaul m numbe
control number prese N t r
register in nt prese CLKI
partition nt N
2 eDMA transfer PRTN2_COFB0_CLKEN[REQ 274 18 1 1 0 Yes 18
control descriptor 18]
26
2 eDMA transfer PRTN2_COFB0_CLKEN[REQ 275 19 1 1 0 Yes 19
control descriptor 19]
27
2 eDMA transfer PRTN2_COFB0_CLKEN[REQ 276 20 1 1 0 Yes 20
control descriptor 20]
28
2 eDMA transfer PRTN2_COFB0_CLKEN[REQ 277 21 1 1 0 Yes 21
control descriptor 21]
29
2 eDMA transfer PRTN2_COFB0_CLKEN[REQ 278 22 1 1 0 Yes 22
control descriptor 22]
30
2 eDMA transfer PRTN2_COFB0_CLKEN[REQ 279 23 1 1 0 Yes 23
control descriptor 23]
31
2 SEMA42 PRTN2_COFB0_CLKEN[REQ 280 24 1 1 0 Yes 24
24]
2 PRAMC_1 PRTN2_COFB0_CLKEN[REQ 281 25 1 0 1 Yes 25
25]
2 Reserved PRTN2_COFB0_CLKEN[REQ 282 26 0 0 0 Yes 26
26]
2 SWT_1 PRTN2_COFB0_CLKEN[REQ 283 27 1 1 0 Yes 27
27]
2 Reserved PRTN2_COFB0_CLKEN[REQ 284 28 0 0 0 Yes 28
28]
2 STM_1 PRTN2_COFB0_CLKEN[REQ 285 29 1 1 0 Yes 29
29]
2 Reserved PRTN2_COFB0_CLKEN[REQ 286 30 0 0 0 Yes 30
30]
2 Reserved PRTN2_COFB0_CLKEN[REQ 287 31 0 0 0 Yes 31
31]
2 EMAC PRTN2_COFB1_CLKEN[REQ 288 32 1 1 0 No 0
32]
Table continues on the next page...

---

*Page 1182*

Mode Entry Module (MC_ME)
Table 216. MC_ME partition peripheral mapping and clock control (continued)
AIPS Peripheral MC_ME's MC_ME MC_ME MC_M MC_M MC_M On IPS
1
peripher description PRTN n_COFB n_CLKEN peripher peripher E E E platfor slot
al register fields al al slot COFB CLKE defaul m numbe
control number prese N t r
register in nt prese CLKI
partition nt N
2 Reserved PRTN2_COFB1_CLKEN[REQ 289 33 0 0 0 No 1
33]
2 Reserved PRTN2_COFB1_CLKEN[REQ 290 34 0 0 0 No 2
34]
2 LPUART_8 PRTN2_COFB1_CLKEN[REQ 291 35 1 1 0 No 3
35]
2 LPUART_9 PRTN2_COFB1_CLKEN[REQ 292 36 1 1 0 No 4
36]
2 LPUART_10 PRTN2_COFB1_CLKEN[REQ 293 37 1 1 0 No 5
37]
2 LPUART_11 PRTN2_COFB1_CLKEN[REQ 294 38 1 1 0 No 6
38]
2 LPUART_12 PRTN2_COFB1_CLKEN[REQ 295 39 1 1 0 No 7
39]
2 LPUART_13 PRTN2_COFB1_CLKEN[REQ 296 40 1 1 0 No 8
40]
2 LPUART_14 PRTN2_COFB1_CLKEN[REQ 297 41 1 1 0 No 9
41]
2 LPUART_15 PRTN2_COFB1_CLKEN[REQ 298 42 1 1 0 No 10
42]
2 Reserved PRTN2_COFB1_CLKEN[REQ 299 43 0 0 0 No 11
43]
2 Reserved PRTN2_COFB1_CLKEN[REQ 300 44 0 0 0 No 12
44]
2 Reserved PRTN2_COFB1_CLKEN[REQ 301 45 0 0 0 No 13
45]
2 Reserved PRTN2_COFB1_CLKEN[REQ 302 46 0 0 0 No 14
46]
2 LPSPI_4 PRTN2_COFB1_CLKEN[REQ 303 47 1 1 0 No 15
47]
2 LPSPI_5 PRTN2_COFB1_CLKEN[REQ 304 48 1 1 0 No 16
48]
2 Reserved PRTN2_COFB1_CLKEN[REQ 305 49 0 0 0 No 17
49]
Table continues on the next page...

---

*Page 1183*

Mode Entry Module (MC_ME)
Table 216. MC_ME partition peripheral mapping and clock control (continued)
AIPS Peripheral MC_ME's MC_ME MC_ME MC_M MC_M MC_M On IPS
1
peripher description PRTN n_COFB n_CLKEN peripher peripher E E E platfor slot
al register fields al al slot COFB CLKE defaul m numbe
control number prese N t r
register in nt prese CLKI
partition nt N
2 Reserved PRTN2_COFB1_CLKEN[REQ 306 50 0 0 0 No 18
50]
2 QuadSPI PRTN2_COFB1_CLKEN[REQ 307 51 1 1 0 No 19
51]
2 Reserved PRTN2_COFB1_CLKEN[REQ 308 52 0 0 0 No 20
52]
2 Reserved PRTN2_COFB1_CLKEN[REQ 309 53 0 0 0 No 21
53]
2 Reserved PRTN2_COFB1_CLKEN[REQ 310 54 0 0 0 No 22
54]
2 SAI_1 PRTN2_COFB1_CLKEN[REQ 311 55 1 1 0 No 23
55]
2 Reserved PRTN2_COFB1_CLKEN[REQ 312 56 0 0 0 No 24
56]
2 Reserved PRTN2_COFB1_CLKEN[REQ 313 57 0 0 0 No 25
57]
2 LPCMP_2 PRTN2_COFB1_CLKEN[REQ 314 58 1 1 1 No 26
58]
2 HSE_MU1_B PRTN2_COFB1_CLKEN[REQ 315 59 1 0 1 No 27
59]
2 CM7_0_TCM_CLK PRTN2_COFB1_CLKEN[REQ 318 62 1 1 1 No 30
EN 62]
2 CM7_1_TCM_CLK PRTN2_COFB1_CLKEN[REQ 319 63 1 1 1 No 31
EN 63]
1. See the memory-map sheet attached to this document for details on available peripherals in different chips.
2. While accessing SELFTEST_GPR space, software must ensure that the STCU2 block has its clock enabled. i.e.,
MC_ME.PRTN1_COFB3_CLKEN[REQ104] configured as 1'b1. Not ensuring this might result in unpredictable device
behaviour.

---

*Page 1184*

Mode Entry Module (MC_ME)

#### 43.1.5 Application core initialization process

Start
Write the application core boot address to
PRTN m _CORE _ADDR n
Enable the application core clock by using
PRTN m _CORE _PCONF[CCE] n
Write to
PRTN m _CORE _PUPD n
Write valid key sequence to
CTL_KEY
Read the process-update status from
PRTN m _CORE _PUPD n
Process
completed
(PUPD=0)?
Read the application core clock status from
PRTN m _CORE _STAT[CCS] n
Core clock No
Run software diagnostics
enabled?
Yes
Core starts execution
from boot address
Figure 160. Application core initialization process

#### 43.1.6 Application core shutdown process

If a debugger is attached to the chip and application debug is enabled, the application core continues running if you write 0
to MDM_AP.MDMAPCT[CM7_ n _CORE_ACCESS].

---

*Page 1185*

Mode Entry Module (MC_ME)
Core is in Running
or Active state
Stop any ongoing core
communication
Request core to enter WFI by writing
to corresponding core registers
Read the application core WFI status from
PRTN m _CORE _STAT[WFI] n
Disable the application core clock by using
PRTN m _CORE _PCONF[CCE] n
Write to
PRTN m _CORE _PUPD n
Write valid key sequence to
CTL_KEY
Read the process-update status from
PRTN m _CORE _PUPD n
Process
completed
(PUPD=0)?
Read the application core clock status by using
PRTN m _CORE _STAT[CCS] n
Core clock No
Run software diagnostics
disabled?
Yes
Core in Turn-Off
or Inactive state
Figure 161. Application core shutdown process

#### 43.1.7 Peripheral initialization process

You cannot control all the peripherals. For example, you cannot turn on and turn off the peripherals required for chip functionality
across reset or power-up. They always remain on.

---

*Page 1186*

Mode Entry Module (MC_ME)
Start
Consider the peripheral initialization
requirements or sequence in the
corresponding chapter
Enable the peripheral core clock by using
PRTN m _COFB _CLKEN[REQ ] n z
Write to
PRTN m _PUPD
Write valid key sequence to
CTL_KEY
Read the process-update status from
PRTN m _PUPD
Process
completed
(PUPD=0)?
Read the application core clock status from
PRTN m _COFB _STAT[BLOCK ] n z
Peripheral
No
clock Run software diagnostics
enabled?
Yes
Start peripheral
operation
Figure 162. Peripheral initialization process

#### 43.1.8 Peripheral shutdown process

You cannot control all the peripherals. For example, you cannot turn on and turn off the peripherals required for chip functionality
across reset or power-up. They always remain on.

---

*Page 1187*

Mode Entry Module (MC_ME)
Peripheral in Running
or Active state
Stop any ongoing communication with
input, output, cores or peripherals
Disable peripheral by writing to
its module-disable field (MDIS, EN or similar)
Read the peripheral status by reading back
the module-disable field (MDIS, EN or similar)
Disable the peripheral by using
PRTN m _COFB _CLKEN[REQ ] n z
Write to
PRTN m _PUPD
Write valid key sequence to
CTL_KEY
Read the process-update status from
PRTN m _PUPD
Process
completed
(PUPD=0)?
Read the application core clock status from
PRTN m _COFB _STAT[BLOCK ] n z
Peripheral
No
clock Run software diagnostics
disabled?
Yes
Peripheral in Turn-Off
or Inactive state
Figure 163. Peripheral shutdown process

#### 43.2 Introduction

The MC_ME module generates control signals for a set of modules of the SoC. The set of signals are defined in corresponding
'Partition Configuration Registers'. It also implements a software-based mechanism for initiating a functional and destructive reset
sequence and standby entry handshake with power management of SoC . See Figure 164 for the MC_ME block diagram.

---

*Page 1188*

Mode Entry Module (MC_ME)
Configuration bus
Configuration registers
Partition 0 Partition 2 Mode controller
P P P P P P
Partition controls Partition controls
Figure 164. MC_ME block diagram

#### 43.3 Features

MC_ME includes the following features:
• 3 logic partitions implementation and their controls
• Core clock controls
• Partition clock control
• Control mechanism for initiating a destructive or functional reset sequence to MC_RGM
• Control mechanism for initiating standby mode entry for SoC
The logic partition inside MC_ME refers to a certain group of on-chip resources (or IP blocks) that are clubbed together to represent
a single 'Partition' inside MC_ME. The MC_ME partition can be the same or different than an LBIST partition. Each of the MC_ME
partitions implements a certain number of hardware processes. These hardware processes provide a mechanism to regulate
various control signals provided to or received from the IP blocks. The corresponding status signals can also be monitored from
MC_ME register(s). Each of the hardware processes is bound to finish in 512 cycles of the MC_ME register configuration clocks.
Therefore, the hardware processes are non blocking in nature. Mismatch in the expected versus actual status of any hardware
process is controlled by a pre-defined software.

#### 43.4 Partition processes

Each of the processes inside the partition controls register space and corresponds to a control signal provided to that partition. A
partition can include a core, or COFB s, or both. The MC_ME hardware processes provide control and status via signals provided
to partitions. Each partition can be assigned a signal for control and a signal for status. Each of the control signals implements
functionality for the partition. For example, clock gating and peripheral control.
The hardware process can be triggered and monitored using a set of three registers:
• Configuration register; for example, Partition n Process Configuration register
• Update register; for example, Partition n Process Update register
• Status register; for example, Partition n Status register
Similar registers exist for cores inside the partition.
The process setup and triggering procedure is shown in Figure 165 . Each of the processes is independent of others and can be
triggered or re-triggered in parallel or sequential to other processes. The triggering or re-triggering mechanism remains the same.

---

*Page 1189*

Mode Entry Module (MC_ME)
Program
PRTNn_PCONF
register
Program
PRTNn_PUPD
register
Write valid key
sequence in
CTL_KEY register
Read
PRTNn_PUPD
register
Is
hardware
N
update
process
finished?
Y
Is status
N
Run software
expected?
diagnostics
Y
Finish
Figure 165. Partition process setup procedure
All the hardware processes are bound to finish in 512 cycles of the MC_ME configuration clock. If the actual and the expected
status for a process does not match, then the diagnostics is left as a software responsibility. The software diagnostic can include
further wait cycles for the status to match.

#### 43.5 Mode transition

MC_ME implements a mode transition mechanism, whereby the mode of operation for SoC can be changed. Then module
implements a mechanism that can lead to:
• Destructive reset
• Functional reset
• Standby mode entry

---

*Page 1190*

Mode Entry Module (MC_ME)
Destructive reset and functional reset requests from MC_ME are non-retractable transitions. After it is initiated, the other MC_ME
functionality is rendered unusable and bus errors are provided for upcoming access to the MC_ME register until a reset sequence
is executed by MC_RGM. Hence, it is vital that MC_RGM should never ignore or gate the reset requests from MC_ME.
For transition into the standby mode, the software should ensure that required IP blocks such as clock sources and I/O
communication are in their respective inactive states before initiating a standby mode transition to MC_ME. After MC_ME
initiates a power down sequence request, it cannot be retracted. The SoC enters a standby power down sequence and then
reenter power-up sequence even for cases where the standby wakeup happens right at the time of initiating a power-down
sequence request.
Steps for initiating the MC_ME mode transition:
1. Setup the MODE_CONF register with the corresponding target mode bit set to logic-1.
2. Perform the same update as done in the MODE_CONF register on the CONF_UPD register.
3. Write the valid control key (0x5AF0) on the CTL_KEY register.
4. Write the valid invert control key (0xA50F) on the CTL_KEY register.
Mode transition to MC_ME is initiated, after the sequence mentioned above is completed.
In step 1, if both FUNC_RST and DEST_RST in Mode Configuration Register (MODE_CONF) are 1:
• After step 4 is complete, MC_ME initiates a mode transition to a destructive (not functional) reset.
• After the chip exits reset, MC_RGM records that both MC_ME's destructive reset and MC_ME's functional reset were the
reset source.
NOTE
Any hardware partition processes setup, along with mode transition, is executed in parallel to the mode transition
of MC_ME.

#### 43.6 Standby entry

MC_ME provides hardware processes that implement shutdown sequencing of on-chip resources, such as cores and COFBs.
The standby entry sequencing can be achieved or implemented using these hardware processes. The order of the hardware
process is determined by the software and MC_ME. It requires no restriction in sequencing of the operation. Following is an
example sequence for initiating a power-down sequence for entering the standby mode for SoC. The standby entry sequence
should include (but not limited to) the following steps:
1. Setting up wakeup lines
2. Shutting down cores and COFBs
3. Switching all MC_CGM muxes to FIRC with PCFS
4. Powering down all clock sources except FIRC
5. Setting up MC_ME using the main core and initiating a standby mode transition
6. Executing WFI instruction on the main core (per Arm specification)

#### 43.6.1 Application core shutdown

This section describes a mechanism for shutting down an application core. The sequence proposed here is extendible with the
housekeeping tasks required for other IPs. Each of the tasks mentioned in the following sequence, can be further integrated with
an SoC-specific task.

---

*Page 1191*

Mode Entry Module (MC_ME)
MAIN CORE Application core MC_ME
Request application
cores to stop
Application cores
begin shut down
sequence
Disable all IRQs
undefined
Outstanding
delay
instructions
finished/retired
WFI executed
MC_ME receives WFI
status
Check for application
MC_ME updates
core WFI status in
corresponding core
MC_ME status register
WFI Application
N Y
core
received?
shutdown
Figure 166. Application core shutdown
After the application core is shutdown, the main core can optionally decide to gate the respective core clock using the
corresponding core clock hardware process.

#### 43.6.2 Main core shutdown and standby entry

This section describes standby entry sequence along with the main core shutdown. This sequence should only be initiated after
SoC is ready for entering standby and has completed all the housekeeping activities. It is necessary that the main core has
completed all the operations pertaining to other (application cores) and is the last active core before initiating the standby entry
sequence. See Figure 167 .

---

*Page 1192*

Mode Entry Module (MC_ME)
Main core MC_ME MC_PCU
Program wakeup IP
Disable IRQs
and NMI
Program MC_ME for
valid main core ID
Finish outstanding
instructions
MC_ME waits for WFI
signaling from main
Program MC_ME for
core
standby mode
transition
Finish outstanding
instructions
WFI executed
Power down
sequence
initiated
Figure 167. Standby entry sequence along with main core shutdown
NOTE
• MC_ME initiates the power sequence to MC_PCU. This enables the main core to remain inactive (WFI state)
until it is reset and power-up again at standby exit.

#### 43.7 MC_ME register descriptions

MC_ME implements set hardware processes that can be used by the software for changing the mode of operation for a partition.
Following are the features of MC_ME registers:
• All registers are 32-bit wide.
• Only 32-bit read and write accesses are supported.
• Read/write accesses of less than 32 bits terminate with an error.
• Writes to read-only register fields in writable registers are ignored and do not provide an error message.

---

*Page 1193*

Mode Entry Module (MC_ME)
• Writes to read-only registers are aborted with an error message.

#### 43.7.1 MC_ME memory map

MC_ME base address: 402D_C000h
Offset Register Access Reset value
Width
(In bits)
0h Control Key Register (CTL_KEY) 32 RW 0000_5AF0h
4h Mode Configuration Register (MODE_CONF) 32 RW 0000_0000h
8h Mode Update Register (MODE_UPD) 32 RW 0000_0000h
Ch Mode Status Register (MODE_STAT) 32 R 0000_0000h
10h Main Core ID Register (MAIN_COREID) 32 RW 0000_0000h
100h Partition 0 Process Configuration Register (PRTN0_PCONF) 32 RW 0000_0001h
104h Partition 0 Process Update Register (PRTN0_PUPD) 32 RW 0000_0000h
108h Partition 0 Status Register (PRTN0_STAT) 32 R 0000_0001h
114h Partition 0 COFB Set 1 Clock Status Register 32 R 0000_1000h
(PRTN0_COFB1_STAT)
134h Partition 0 COFB Set 1 Clock Enable Register 32 RW 0000_1000h
(PRTN0_COFB1_CLKEN)
140h Partition 0 Core 0 Process Configuration Register 32 RW 0000_0000h
(PRTN0_CORE0_PCONF)
144h Partition 0 Core 0 Process Update Register (PRTN0_CORE0_PUPD) 32 RW 0000_0000h
148h Partition 0 Core 0 Status Register (PRTN0_CORE0_STAT) 32 R 0000_0000h
14Ch Partition 0 Core 0 Address Register (PRTN0_CORE0_ADDR) 32 RW 0040_0000h
188h Partition 0 Core 2 Status Register (PRTN0_CORE2_STAT) 32 R 0000_0001h
18Ch Partition 0 Core 2 Address Register (PRTN0_CORE2_ADDR) 32 R 007F_FC00h
300h Partition 1 Process Configuration Register (PRTN1_PCONF) 32 RW 0000_0001h
304h Partition 1 Process Update Register (PRTN1_PUPD) 32 RW 0000_0000h
308h Partition 1 Status Register (PRTN1_STAT) 32 R 0000_0001h
310h Partition 1 COFB Set 0 Clock Status Register 32 R 5E3F_0007h
(PRTN1_COFB0_STAT)
314h Partition 1 COFB Set 1 Clock Status Register 32 R 1CFE_2FFCh
(PRTN1_COFB1_STAT)
318h Partition 1 COFB Set 2 Clock Status Register 32 R 3000_0000h
(PRTN1_COFB2_STAT)
31Ch Partition 1 COFB Set 3 Clock Status Register 32 R 0000_5FEEh
(PRTN1_COFB3_STAT)
Table continues on the next page...

---

*Page 1194*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Offset Register Access Reset value
Width
(In bits)
330h Partition 1 COFB Set 0 Clock Enable Register 32 RW 5E3F_0007h
(PRTN1_COFB0_CLKEN)
334h Partition 1 COFB Set 1 Clock Enable Register 32 RW 1CFE_2FFCh
(PRTN1_COFB1_CLKEN)
338h Partition 1 COFB Set 2 Clock Enable Register 32 RW 3000_0000h
(PRTN1_COFB2_CLKEN)
33Ch Partition 1 COFB Set 3 Clock Enable Register 32 RW 0000_5FEEh
(PRTN1_COFB3_CLKEN)
500h Partition 2 Process Configuration Register (PRTN2_PCONF) 32 RW 0000_0001h
504h Partition 2 Process Update Register (PRTN2_PUPD) 32 RW 0000_0000h
508h Partition 2 Status Register (PRTN2_STAT) 32 R 0000_0001h
510h Partition 2 COFB Set 0 Clock Status Register 32 R 0200_0003h
(PRTN2_COFB0_STAT)
514h Partition 2 COFB Set 1 Clock Status Register 32 R CC00_0000h
(PRTN2_COFB1_STAT)
530h Partition 2 COFB Set 0 Clock Enable Register 32 RW 0200_0003h
(PRTN2_COFB0_CLKEN)
534h Partition 2 COFB Set 1 Clock Enable Register 32 RW CC00_0000h
(PRTN2_COFB1_CLKEN)

#### 43.7.2 Control Key Register (CTL_KEY)

Offset
Register Offset
CTL_KEY 0h
Function
This register provides the mechanism to MC_ME for starting the hardware processes for the partition(s) and standby entry
sequence. The hardware processes for partitions are triggered through the corresponding PRTNn_PCONF register. The
mechanism to trigger the hardware processes of the respective partitions require two write operations: first time with key and
second time with inverted key. The hexadecimal value of key is 0x5AF0 whereas for inverted key is 0xA50F.
For initiating a standby entry sequence, the MODE_CONF register is used for providing a standby entry request along with a valid
key combination.
NOTE
Reads from this register return a valid key value to be written next.

---

*Page 1195*

Mode Entry Module (MC_ME)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R
KEY
W
Reset 0 1 0 1 1 0 1 0 1 1 1 1 0 0 0 0
Fields
Field Function
31-16 Reserved
— This field is reserved and read returns zeros.
15-0 Control key
KEY Key for starting the hardware processes. Writes with a value other than key or inverted key are ignored.
Reads return bit inverted value corresponding to last write.

#### 43.7.3 Mode Configuration Register (MODE_CONF)

Offset
Register Offset
MODE_CONF 4h
Function
This register is used for initiating a standby request or a reset event (destructive or functional) for the chip. The functional or
destructive events are signaled to MC_RGM for further handling.
NOTE
Software must not enable mode entry if the value of multiple fields is 1 in the MODE_CONF register.

---

*Page 1196*

Mode Entry Module (MC_ME)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
STAN FUNC DEST_
DBY _RST RST
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-16 Reserved
— This field is reserved and read returns zeros.
15 Standby request
STANDBY Writing a logic-1 to this bit along with the MODE_UPD register configuration and followed with a valid key
combination makes a standby entry sequence request to MC_ME.
14-2 Reserved
— This field is reserved and read returns zeros.
1 Functional reset request
FUNC_RST Writing a logic-1 to this bit along with the MODE_UPD register configuration and followed with a valid key
combination makes a functional reset event signaling to MC_RGM.
0 Destructive reset request
DEST_RST Writing a logic-1 to this bit along with the MODE_UPD register configuration and followed with a valid key
combination makes a destructive reset event signaling to MC_RGM.

#### 43.7.4 Mode Update Register (MODE_UPD)

Offset
Register Offset
MODE_UPD 8h
Function
This register is used for initiating a mode change. Mode change refers to initiating a standby request, or generating a destructive
or functional reset event to MC_RGM. Setting mode update field to logic-1, along with programming MODE_CONF registers and
then followed by a valid key combination will generate a mode transition request.

---

*Page 1197*

Mode Entry Module (MC_ME)
NOTE
The MODE_UPD register is implemented to make mode transition programming model the same as partition
programming model. This is for future expansion inside MC_ME.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
MODE
_UPD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-1 Reserved
— This field is reserved and read returns zeros.
0 Mode update
MODE_UPD Writing a logic-1 to this bit, followed by a valid key combination initiates a mode change as per the
MODE_CONF register.

#### 43.7.5 Mode Status Register (MODE_STAT)

Offset
Register Offset
MODE_STAT Ch
Function
This register provides the status of the previous mode. In case of standby exit, if the reset event status register of MC_RGM are
set, then contents of this register should be ignored.

---

*Page 1198*

Mode Entry Module (MC_ME)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
PREV_
R 0
MO ...
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-1 Reserved
— This field is reserved and read returns zeros.
0 Previous mode
PREV_MODE This bit shows the status of the previous mode.
0b - The previous mode was reset (any reset).
1b - The previous mode was standby.

#### 43.7.6 Main Core ID Register (MAIN_COREID)

Offset
Register Offset
MAIN_COREID 10h
Function
This register provides the ID of the main core sequencing the operation for the standby sequence. Core ID is required for entering
in the standby mode, and using this MC_ME locates the WFI instruction execution of the main core. The core ID in this register
is specified by the partition index along with the core index.
NOTE
Before initiating a standby entry sequence, the contents of this register should point to the correct main core.
Providing non-existing or incorrect core ID leads to unpredictable hardware behavior.

---

*Page 1199*

Mode Entry Module (MC_ME)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
PIDX CIDX
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-13 Reserved
— This field is reserved and read returns zeros.
12-8 Partition index
PIDX Provides the partition index of the main core. Only values 0 - 2 can be written.
7-3 Reserved
— This field is reserved and read returns zeros.
2-0 Core index
CIDX Provides the core index of the main core inside the partition.

#### 43.7.7 Partition 0 Process Configuration Register (PRTN0_PCONF)

Offset
Register Offset
PRTN0_PCONF 100h
Function
This register provides a configuration for the hardware processes corresponding to partition 0. Each of the configuration bit
corresponds to the 'nature' of the processes; for example, enabling/disabling and the trigger is controlled by the corresponding
field in the PRTN0_PUPD register. When valid KEY combinations are written onto the CTL_KEY register, the PRTN0_PCONF
and PRTN0_PUPD registers are used to determine the hardware processes to be executed. These are triggered in parallel and
independent of each other. All dependent processes should be requested one after another from the software.
NOTE
The partition clock enable/disable are not standalone and must be done coherently in a fixed sequence. For details,
see Software Reset Partition Turn-On Flow Chart and Software reset partition turn-off flowchart in Reset chapter.

---

*Page 1200*

Mode Entry Module (MC_ME)
NOTE
See chip-specific MC_ME information to check if this register is implemented on chip.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0 0 0
PCE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1
Fields
Field Function
31-7 Reserved
— This field is reserved and read returns zeros.
6 Reserved
— This field is reserved and read returns zeros.
5 Reserved
— This field is reserved and read returns zeros.
4 Reserved
— This field is reserved and read returns zeros.
3 Reserved
— This field is reserved and read returns zeros.
2 Reserved
— This field is reserved and read returns zeros.
1 Reserved
— This field is reserved and read returns zeros.
0 Partition clock enable
PCE This bit controls whether the clock to IPs (other than core(s)) in the partition should be enabled or disabled.
0b - Disable the clock to IPs
1b - Enable the clock to IPs

---

*Page 1201*

Mode Entry Module (MC_ME)

#### 43.7.8 Partition 0 Process Update Register (PRTN0_PUPD)

Offset
Register Offset
PRTN0_PUPD 104h
Function
This register provides trigger signaling for the hardware processes corresponding to partition 0. Each of the control bit acts as
a trigger for the corresponding hardware processes. When valid KEY combinations are written onto the CTL_KEY register, the
hardware checks the bit fields that are programmed as logic-1 in this register, and then triggers the hardware process per the value
in the corresponding bit field in the PRTN0_PCONF register. When the hardware process is finished the corresponding bit in this
register is auto-cleared to logic-0.
NOTE
See chip-specific MC_ME information to check if this register is implemented on chip.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0 0 0
PCUD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-7 Reserved
— This field is reserved and read returns zeros.
6 Reserved
— This field is reserved and read returns zeros.
5 Reserved
— This field is reserved and read returns zeros.
4 Reserved
— This field is reserved and read returns zeros.
Table continues on the next page...

---

*Page 1202*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
3 Reserved
— This field is reserved and read returns zeros.
2 Reserved
— This field is reserved and read returns zeros.
1 Reserved
— This field is reserved and read returns zeros.
0 Partition clock update
PCUD This bit controls whether the hardware processes for enabling/disabling the clock to IPs (other than core(s))
in the partition should be triggered or not.
0b - Do not trigger the hardware process
1b - Trigger the hardware process

#### 43.7.9 Partition 0 Status Register (PRTN0_STAT)

Offset
Register Offset
PRTN0_STAT 108h
Function
This register provides the current status of the control signals from the partition 0.
NOTE
See chip-specific MC_ME information to check if this register is implemented on chip.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0 0 0 PCS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1

---

*Page 1203*

Mode Entry Module (MC_ME)
Fields
Field Function
31-7 Reserved
— This field is reserved and read returns zeros.
6 Reserved
— This field is reserved and read returns zeros.
5 Reserved
— This field is reserved and read returns zeros.
4 Reserved
— This field is reserved and read returns zeros.
3 Reserved
— This field is reserved and read returns zeros.
2 Reserved
— This field is reserved and read returns zeros.
1 Reserved
— This field is reserved and read returns zeros.
0 Partition clock status
PCS This bit provides the status of the clock to partition.
0b - Clock is inactive
1b - Clock is active

#### 43.7.10 Partition 0 COFB Set 1 Clock Status Register (PRTN0_COFB1_STAT)

Offset
Register Offset
PRTN0_COFB1_STAT 114h
Function
This register provides the status of set 1 of COFB s inside partition 0.
NOTE
The reset value of this register can vary depending on the availability of active clock pulses inside partition 0.

---

*Page 1204*

Mode Entry Module (MC_ME)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC
R 0 0
K47 K46 K45 K44 K42 K41 K40 K39 K38 K36 K35 K34 K33 K32
W
Reset 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Reserved
— This field is reserved and read returns zeros.
30 Reserved
— This field is reserved and read returns zeros.
29 Reserved
— This field is reserved and read returns zeros.
28 Reserved
— This field is reserved and read returns zeros.
27 Reserved
— This field is reserved and read returns zeros.
26 Reserved
— This field is reserved and read returns zeros.
25 Reserved
— This field is reserved and read returns zeros.
24 Reserved
— This field is reserved and read returns zeros.
23 Reserved
— This field is reserved and read returns zeros.
22 Reserved
— This field is reserved and read returns zeros.
Table continues on the next page...

---

*Page 1205*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
21 Reserved
— This field is reserved and read returns zeros.
20 Reserved
— This field is reserved and read returns zeros.
19 Reserved
— This field is reserved and read returns zeros.
18 Reserved
— This field is reserved and read returns zeros.
17 Reserved
— This field is reserved and read returns zeros.
16 Reserved
— This field is reserved and read returns zeros.
15 IP block status
BLOCK47 This bit provides the clock status of block 47 in partition 0.
0b - Clock is not running.
1b - Clock is running.
14 IP block status
BLOCK46 This bit provides the clock status of block 46 in partition 0.
0b - Clock is not running.
1b - Clock is running.
13 IP block status
BLOCK45 This bit provides the clock status of PIT_1 in partition 0.
0b - Clock is not running.
1b - Clock is running.
12 IP block status
BLOCK44 This bit provides the clock status of PIT_0 in partition 0.
0b - Clock is not running.
1b - Clock is running.
11 Reserved
— This field is reserved and read returns zeros.
Table continues on the next page...

---

*Page 1206*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
10 IP block status
BLOCK42 This bit provides the clock status of ADC_2 in partition 0.
0b - Clock is not running.
1b - Clock is running.
9 IP block status
BLOCK41 This bit provides the clock status of ADC_1 in partition 0.
0b - Clock is not running.
1b - Clock is running.
8 IP block status
BLOCK40 This bit provides the clock status of ADC_0 in partition 0.
0b - Clock is not running.
1b - Clock is running.
7 IP block status
BLOCK39 This bit provides the clock status of LCU_1 in partition 0.
0b - Clock is not running.
1b - Clock is running.
6 IP block status
BLOCK38 This bit provides the clock status of LCU_0 in partition 0.
0b - Clock is not running.
1b - Clock is running.
5 Reserved
— This field is reserved and read returns zeros.
4 IP block status
BLOCK36 This bit provides the clock status of eMIOS_2 in partition 0.
0b - Clock is not running.
1b - Clock is running.
3 IP block status
BLOCK35 This bit provides the clock status of eMIOS_1 in partition 0.
0b - Clock is not running.
1b - Clock is running.
2 IP block status
Table continues on the next page...

---

*Page 1207*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
BLOCK34 This bit provides the clock status of eMIOS_0 in partition 0.
0b - Clock is not running.
1b - Clock is running.
1 IP block status
BLOCK33 This bit provides the clock status of BCTU in partition 0.
0b - Clock is not running.
1b - Clock is running.
0 IP block status
BLOCK32 This bit provides the clock status of TRGMUX in partition 0.
0b - Clock is not running.
1b - Clock is running.

#### 43.7.11 Partition 0 COFB Set 1 Clock Enable Register (PRTN0_COFB1_CLKEN)

Offset
Register Offset
PRTN0_COFB1_CLKEN 134h
Function
This register provides clock control signaling to the individual COFBs in set 1 inside partition 0. Whenever a partition clock enable
(non-core) hardware process is initiated, the value of logic-1 in the corresponding bit locations of this register enables the clock
to the corresponding block in the partition.
NOTE
The reset value of this register is not defined and is as per the availability of the clock source. See Chip-specific
MC_ME information for clock source availability.

---

*Page 1208*

Mode Entry Module (MC_ME)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0
REQ4 REQ4 REQ4 REQ4 REQ4 REQ4 REQ4 REQ3 REQ3 REQ3 REQ3 REQ3 REQ3 REQ3
7 6 5 4 2 1 0 9 8 6 5 4 3 2
W
Reset 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Reserved
— This field is reserved and read returns zeros.
30 Reserved
— This field is reserved and read returns zeros.
29 Reserved
— This field is reserved and read returns zeros.
28 Reserved
— This field is reserved and read returns zeros.
27 Reserved
— This field is reserved and read returns zeros.
26 Reserved
— This field is reserved and read returns zeros.
25 Reserved
— This field is reserved and read returns zeros.
24 Reserved
— This field is reserved and read returns zeros.
23 Reserved
— This field is reserved and read returns zeros.
22 Reserved
— This field is reserved and read returns zeros.
Table continues on the next page...

---

*Page 1209*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
21 Reserved
— This field is reserved and read returns zeros.
20 Reserved
— This field is reserved and read returns zeros.
19 Reserved
— This field is reserved and read returns zeros.
18 Reserved
— This field is reserved and read returns zeros.
17 Reserved
— This field is reserved and read returns zeros.
16 Reserved
— This field is reserved and read returns zeros.
15 Clock enable
REQ47 This bit provides the clock enable control for block 47 in partition 0.
0b - Clock is turned off.
1b - Clock is turned on.
14 Clock enable
REQ46 This bit provides the clock enable control for block 46 in partition 0.
0b - Clock is turned off.
1b - Clock is turned on.
13 Clock enable
REQ45 This bit provides the clock enable control for PIT_1 in partition 0.
0b - Clock is turned off.
1b - Clock is turned on.
12 Clock enable
REQ44 This bit provides the clock enable control for PIT_0 in partition 0.
0b - Clock is turned off.
1b - Clock is turned on.
11 Reserved
— This field is reserved and read returns zeros.
Table continues on the next page...

---

*Page 1210*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
10 Clock enable
REQ42 This bit provides the clock enable control for ADC_2 in partition 0.
0b - Clock is turned off.
1b - Clock is turned on.
9 Clock enable
REQ41 This bit provides the clock enable control for ADC_1 in partition 0.
0b - Clock is turned off.
1b - Clock is turned on.
8 Clock enable
REQ40 This bit provides the clock enable control for ADC_0 in partition 0.
0b - Clock is turned off.
1b - Clock is turned on.
7 Clock enable
REQ39 This bit provides the clock enable control for LCU_1 in partition 0.
0b - Clock is turned off.
1b - Clock is turned on.
6 Clock enable
REQ38 This bit provides the clock enable control for LCU_0 in partition 0.
0b - Clock is turned off.
1b - Clock is turned on.
5 Reserved
— This field is reserved and read returns zeros.
4 Clock enable
REQ36 This bit provides the clock enable control for eMIOS_2 in partition 0.
0b - Clock is turned off.
1b - Clock is turned on.
3 Clock enable
REQ35 This bit provides the clock enable control for eMIOS_1 in partition 0.
0b - Clock is turned off.
1b - Clock is turned on.
2 Clock enable
Table continues on the next page...

---

*Page 1211*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
REQ34 This bit provides the clock enable control for eMIOS_0 in partition 0.
0b - Clock is turned off.
1b - Clock is turned on.
1 Clock enable
REQ33 This bit provides the clock enable control for BCTU in partition 0.
0b - Clock is turned off.
1b - Clock is turned on.
0 Clock enable
REQ32 This bit provides the clock enable control for TRGMUX in partition 0.
0b - Clock is turned off.
1b - Clock is turned on.

#### 43.7.12 Partition 0 Core 0 Process Configuration Register (PRTN0_CORE0_PCONF)

Offset
Register Offset
PRTN0_CORE0_PCONF 140h
Function
This register provides configurations for the Core 0 hardware processes corresponding to partition 0. Each of the configuration
bit corresponds to the 'nature' of the processes; for example, enabling/disabling and the trigger is controlled by the corresponding
field in the PRTN0_CORE0_PUPD register. When valid KEY combinations are written onto the CTL_KEY register, the
PRTN0_CORE0_PUPD and PRTN0_CORE0_PCONF registers are used to determine the hardware processes to be executed.
These processes are triggered in parallel and are independent of each other. All dependent processes should be requested one
after another from the software.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
CCE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1212*

Mode Entry Module (MC_ME)
Fields
Field Function
31-1 Reserved
— This field is reserved and read returns zeros.
0 Core 0 clock enable
CCE This bit controls whether the clock to Core 0 in partition 0 should be enabled or disabled.
0b - Disable the core clock
1b - Enable the core clock

#### 43.7.13 Partition 0 Core 0 Process Update Register (PRTN0_CORE0_PUPD)

Offset
Register Offset
PRTN0_CORE0_PUPD 144h
Function
This register provides trigger signaling for the core hardware processes corresponding to partition 0. Each of the control bit acts
as a trigger for the corresponding hardware processes. When valid KEY combinations are written onto the CTL_KEY register, the
hardware checks the bit fields that are programmed as logic-1 in this register, and then triggers the hardware process per the value
in the corresponding bit field in the PRTN0_CORE0_PCONF register. When the hardware process is finished, the corresponding
bit in this register is auto-cleared to logic-0.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
CCUP
D
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-1 Reserved
Table continues on the next page...

---

*Page 1213*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
— This field is reserved and read returns zeros.
0 Core 0 clock update
CCUPD This bit controls whether the hardware processes for enabling/disabling the clock to Core 0 in the partition
0 should be triggered or not.
0b - Do not trigger the hardware process
1b - Trigger the hardware process

#### 43.7.14 Partition 0 Core 0 Status Register (PRTN0_CORE0_STAT)

Offset
Register Offset
PRTN0_CORE0_STAT 148h
Function
This register provides the status corresponding to Core 0 in partition 0. The status signal corresponds to clock states and the WFI
signal included from Core 0.
NOTE
The value held in WFI field of this STATUS register is "current" value of the WFISTANDBY signal from the core.
Hence out-of-reset, the reset value of this field will depend on the status of the core (core is running or in low power
mode). So, simple reset read sweep will always return current value (different than other register reads such as on
control registers).
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R WFI 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 CCS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1214*

Mode Entry Module (MC_ME)
Fields
Field Function
31 Wait for interrupt status
WFI This bit provides the WFI status approaching from Core 0 in partition 0.
0b - No WFI executed
1b - WFI executed
30-1 Reserved
— This field is reserved and read returns zeros.
0 Core 0 clock process status
CCS This bit provides the status of the clock corresponding to core clock enablement/disablement.
0b - Clock is inactive.
1b - Clock is active.

#### 43.7.15 Partition 0 Core 0 Address Register (PRTN0_CORE0_ADDR)

Offset
Register Offset
PRTN0_CORE0_ADDR 14Ch
Function
This register contains the boot address for Core 0 in partition 0.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
ADDR
W
Reset 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0
ADDR
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

---

*Page 1215*

Mode Entry Module (MC_ME)
Fields
Field Function
31-2 Address
ADDR Core 0 boot address
1-0 Reserved
— This field is reserved and read returns zeros.

#### 43.7.16 Partition 0 Core 2 Status Register (PRTN0_CORE2_STAT)

Offset
Register Offset
PRTN0_CORE2_STAT 188h
Function
This register provides the status corresponding to Core 2 in partition 0. The status signal corresponds to clock states and the WFI
signal included from Core 2.
NOTE
The value held in WFI field of this STATUS register is "current" value of the WFISTANDBY signal from the core.
Hence out-of-reset, the reset value of this field will depend on the status of the core (core is running or in low power
mode). So, simple reset read sweep will always return current value (different than other register reads such as on
control registers).
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R WFI 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 CCS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1
Fields
Field Function
31 Wait for interrupt status
Table continues on the next page...

---

*Page 1216*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
WFI This bit provides the WFI status approaching from Core 2 in partition 0.
0b - No WFI executed
1b - WFI executed
30-1 Reserved
— This field is reserved and read returns zeros.
0 Core 2 clock process status
CCS This bit provides the status of the clock corresponding to core clock enablement/disablement.
1b - Clock is active.

#### 43.7.17 Partition 0 Core 2 Address Register (PRTN0_CORE2_ADDR)

Offset
Register Offset
PRTN0_CORE2_ADDR 18Ch
Function
This register contains the boot address for Core 2 in partition 0.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R ADDR
W
Reset 0 0 0 0 0 0 0 0 0 1 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R ADDR 0
W
Reset 1 1 1 1 1 1 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-2 Address
ADDR Core 2 boot address
Table continues on the next page...

---

*Page 1217*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
1-0 Reserved
— This field is reserved and read returns zeros.

#### 43.7.18 Partition 1 Process Configuration Register (PRTN1_PCONF)

Offset
Register Offset
PRTN1_PCONF 300h
Function
This register provides a configuration for the hardware processes corresponding to partition 1. Each of the configuration bit
corresponds to the 'nature' of the processes; for example, enabling/disabling and the trigger is controlled by the corresponding
field in the PRTN1_PUPD register. When valid KEY combinations are written onto the CTL_KEY register, the PRTN1_PCONF
and PRTN1_PUPD registers are used to determine the hardware processes to be executed. These are triggered in parallel and
independent of each other. All dependent processes should be requested one after another from the software.
NOTE
The partition clock enable/disable are not standalone and must be done coherently in a fixed sequence. For details,
see Software Reset Partition Turn-On Flow Chart and Software reset partition turn-off flowchart in Reset chapter.
NOTE
See chip-specific MC_ME information to check if this register is implemented on chip.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0 0 0
PCE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1
Fields
Field Function
31-7 Reserved
Table continues on the next page...

---

*Page 1218*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
— This field is reserved and read returns zeros.
6 Reserved
— This field is reserved and read returns zeros.
5 Reserved
— This field is reserved and read returns zeros.
4 Reserved
— This field is reserved and read returns zeros.
3 Reserved
— This field is reserved and read returns zeros.
2 Reserved
— This field is reserved and read returns zeros.
1 Reserved
— This field is reserved and read returns zeros.
0 Partition clock enable
PCE This bit controls whether the clock to IPs (other than core(s)) in the partition should be enabled or disabled.
0b - Disable the clock to IPs
1b - Enable the clock to IPs

#### 43.7.19 Partition 1 Process Update Register (PRTN1_PUPD)

Offset
Register Offset
PRTN1_PUPD 304h
Function
This register provides trigger signaling for the hardware processes corresponding to partition 1. Each of the control bit acts as
a trigger for the corresponding hardware processes. When valid KEY combinations are written onto the CTL_KEY register, the
hardware checks the bit fields that are programmed as logic-1 in this register, and then triggers the hardware process per the value
in the corresponding bit field in the PRTN1_PCONF register. When the hardware process is finished the corresponding bit in this
register is auto-cleared to logic-0.
NOTE
See chip-specific MC_ME information to check if this register is implemented on chip.

---

*Page 1219*

Mode Entry Module (MC_ME)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0 0 0
PCUD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-7 Reserved
— This field is reserved and read returns zeros.
6 Reserved
— This field is reserved and read returns zeros.
5 Reserved
— This field is reserved and read returns zeros.
4 Reserved
— This field is reserved and read returns zeros.
3 Reserved
— This field is reserved and read returns zeros.
2 Reserved
— This field is reserved and read returns zeros.
1 Reserved
— This field is reserved and read returns zeros.
0 Partition clock update
PCUD This bit controls whether the hardware processes for enabling/disabling the clock to IPs (other than core(s))
in the partition should be triggered or not.
0b - Do not trigger the hardware process
1b - Trigger the hardware process

---

*Page 1220*

Mode Entry Module (MC_ME)

#### 43.7.20 Partition 1 Status Register (PRTN1_STAT)

Offset
Register Offset
PRTN1_STAT 308h
Function
This register provides the current status of the control signals from the partition 1.
NOTE
See chip-specific MC_ME information to check if this register is implemented on chip.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0 0 0 PCS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1
Fields
Field Function
31-7 Reserved
— This field is reserved and read returns zeros.
6 Reserved
— This field is reserved and read returns zeros.
5 Reserved
— This field is reserved and read returns zeros.
4 Reserved
— This field is reserved and read returns zeros.
3 Reserved
— This field is reserved and read returns zeros.
2 Reserved
Table continues on the next page...

---

*Page 1221*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
— This field is reserved and read returns zeros.
1 Reserved
— This field is reserved and read returns zeros.
0 Partition clock status
PCS This bit provides the status of the clock to partition.
0b - Clock is inactive
1b - Clock is active

#### 43.7.21 Partition 1 COFB Set 0 Clock Status Register (PRTN1_COFB0_STAT)

Offset
Register Offset
PRTN1_COFB0_STAT 310h
Function
This register provides the status of set 0 of COFB s inside partition 1.
NOTE
The reset value of this register can vary depending on the availability of active clock pulses inside partition 1.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC
R
K31 K30 K29 K28 K27 K26 K25 K24 K23 K22 K21 K20 K19 K18 K17 K16
W
Reset 0 1 0 1 1 1 1 0 0 0 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC
R
K15 K14 K13 K12 K11 K10 K9 K8 K7 K6 K5 K4 K3 K2 K1 K0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1 1

---

*Page 1222*

Mode Entry Module (MC_ME)
Fields
Field Function
31 IP block status
BLOCK31 This bit provides the clock status of INTM in partition 1.
0b - Clock is not running.
1b - Clock is running.
30 IP block status
BLOCK30 This bit provides the clock status of XRDC in partition 1.
0b - Clock is not running.
1b - Clock is running.
29 IP block status
BLOCK29 This bit provides the clock status of STM_0 in partition 1.
0b - Clock is not running.
1b - Clock is running.
28 IP block status
BLOCK28 This bit provides the clock status of SWT_0 in partition 1.
0b - Clock is not running.
1b - Clock is running.
27 IP block status
BLOCK27 This bit provides the clock status of PFC_alt in partition 1.
0b - Clock is not running.
1b - Clock is running.
26 IP block status
BLOCK26 This bit provides the clock status of PFC in partition 1.
0b - Clock is not running.
1b - Clock is running.
25 IP block status
BLOCK25 This bit provides the clock status of PRAM_0 in partition 1.
0b - Clock is not running.
1b - Clock is running.
24 IP block status
BLOCK24 This bit provides the clock status of MSCM in partition 1.
0b - Clock is not running.
Table continues on the next page...

---

*Page 1223*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
1b - Clock is running.
23 IP block status
BLOCK23 This bit provides the clock status of ERM in partition 1.
0b - Clock is not running.
1b - Clock is running.
22 IP block status
BLOCK22 This bit provides the clock status of EIM in partition 1.
0b - Clock is not running.
1b - Clock is running.
21 IP block status
BLOCK21 This bit provides the clock status of SDA-AP in partition 1.
0b - Clock is not running.
1b - Clock is running.
20 IP block status
BLOCK20 This bit provides the clock status of Debug_APB in partition 1.
0b - Clock is not running.
1b - Clock is running.
19 IP block status
BLOCK19 This bit provides the clock status of Debug_APB in partition 1.
0b - Clock is not running.
1b - Clock is running.
18 IP block status
BLOCK18 This bit provides the clock status of Debug_APB in partition 1.
0b - Clock is not running.
1b - Clock is running.
17 IP block status
BLOCK17 This bit provides the clock status of Debug_APB in partition 1.
0b - Clock is not running.
1b - Clock is running.
16 IP block status
BLOCK16 This bit provides the clock status of Debug_APB in partition 1.
Table continues on the next page...

---

*Page 1224*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
0b - Clock is not running.
1b - Clock is running.
15 IP block status
BLOCK15 This bit provides the clock status of eDMA in partition 1.
0b - Clock is not running.
1b - Clock is running.
14 IP block status
BLOCK14 This bit provides the clock status of eDMA in partition 1.
0b - Clock is not running.
1b - Clock is running.
13 IP block status
BLOCK13 This bit provides the clock status of eDMA in partition 1.
0b - Clock is not running.
1b - Clock is running.
12 IP block status
BLOCK12 This bit provides the clock status of eDMA in partition 1.
0b - Clock is not running.
1b - Clock is running.
11 IP block status
BLOCK11 This bit provides the clock status of eDMA in partition 1.
0b - Clock is not running.
1b - Clock is running.
10 IP block status
BLOCK10 This bit provides the clock status of eDMA in partition 1.
0b - Clock is not running.
1b - Clock is running.
9 IP block status
BLOCK9 This bit provides the clock status of eDMA in partition 1.
0b - Clock is not running.
1b - Clock is running.
8 IP block status
Table continues on the next page...

---

*Page 1225*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
BLOCK8 This bit provides the clock status of eDMA in partition 1.
0b - Clock is not running.
1b - Clock is running.
7 IP block status
BLOCK7 This bit provides the clock status of eDMA in partition 1.
0b - Clock is not running.
1b - Clock is running.
6 IP block status
BLOCK6 This bit provides the clock status of eDMA in partition 1.
0b - Clock is not running.
1b - Clock is running.
5 IP block status
BLOCK5 This bit provides the clock status of eDMA in partition 1.
0b - Clock is not running.
1b - Clock is running.
4 IP block status
BLOCK4 This bit provides the clock status of eDMA in partition 1.
0b - Clock is not running.
1b - Clock is running.
3 IP block status
BLOCK3 This bit provides the clock status of eDMA in partition 1.
0b - Clock is not running.
1b - Clock is running.
2 IP block status
BLOCK2 This bit provides the clock status of XBIC in partition 1.
0b - Clock is not running.
1b - Clock is running.
1 IP block status
BLOCK1 This bit provides the clock status of XBIC in partition 1.
0b - Clock is not running.
1b - Clock is running.
Table continues on the next page...

---

*Page 1226*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
0 IP block status
BLOCK0 This bit provides the clock status of AXBS in partition 1.
0b - Clock is not running.
1b - Clock is running.

#### 43.7.22 Partition 1 COFB Set 1 Clock Status Register (PRTN1_COFB1_STAT)

Offset
Register Offset
PRTN1_COFB1_STAT 314h
Function
This register provides the status of set 1 of COFB s inside partition 1.
NOTE
The reset value of this register can vary depending on the availability of active clock pulses inside partition 1.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC
R 0 0 0 0
K63 K60 K59 K58 K56 K55 K54 K53 K52 K51 K50 K49
W
Reset 0 0 0 1 1 1 0 0 1 1 1 1 1 1 1 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC
R 0 0
K47 K45 K43 K42 K41 K40 K39 K38 K37 K36 K35 K34 K33 K32
W
Reset 0 0 1 0 1 1 1 1 1 1 1 1 1 1 0 0
Fields
Field Function
31 IP block status
BLOCK63 This bit provides the clock status of PIT_2 in partition 1.
0b - Clock is not running.
Table continues on the next page...

---

*Page 1227*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
1b - Clock is running.
30 Reserved
— This field is reserved and read returns zeros.
29 Reserved
— This field is reserved and read returns zeros.
28 IP block status
BLOCK60 This bit provides the clock status of FMU_alt in partition 1.
0b - Clock is not running.
1b - Clock is running.
27 IP block status
BLOCK59 This bit provides the clock status of FMU in partition 1.
0b - Clock is not running.
1b - Clock is running.
26 IP block status
BLOCK58 This bit provides the clock status of PMC in partition 1.
0b - Clock is not running.
1b - Clock is running.
25 Reserved
— This field is reserved and read returns zeros.
24 IP block status
BLOCK56 This bit provides the clock status of PLL in partition 1.
0b - Clock is not running.
1b - Clock is running.
23 IP block status
BLOCK55 This bit provides the clock status of MC_ME in partition 1.
0b - Clock is not running.
1b - Clock is running.
22 IP block status
BLOCK54 This bit provides the clock status of MC_CGM in partition 1.
0b - Clock is not running.
Table continues on the next page...

---

*Page 1228*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
1b - Clock is running.
21 IP block status
BLOCK53 This bit provides the clock status of FXOSC in partition 1.
0b - Clock is not running.
1b - Clock is running.
20 IP block status
BLOCK52 This bit provides the clock status of FIRC in partition 1.
0b - Clock is not running.
1b - Clock is running.
19 IP block status
BLOCK51 This bit provides the clock status of SXOSC in partition 1.
0b - Clock is not running.
1b - Clock is running.
18 IP block status
BLOCK50 This bit provides the clock status of SIRC in partition 1.
0b - Clock is not running.
1b - Clock is running.
17 IP block status
BLOCK49 This bit provides the clock status of TSPC in partition 1.
0b - Clock is not running.
1b - Clock is running.
16 Reserved
— This field is reserved and read returns zeros.
15 IP block status
BLOCK47 This bit provides the clock status of CMU_0-5 in partition 1.
0b - Clock is not running.
1b - Clock is running.
14 Reserved
— This field is reserved and read returns zeros.
13 IP block status
This bit provides the clock status of WKPU in partition 1.
Table continues on the next page...

---

*Page 1229*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
BLOCK45 0b - Clock is not running.
1b - Clock is running.
12 Reserved
— This field is reserved and read returns zeros.
11 IP block status
BLOCK43 This bit provides the clock status of DCM in partition 1.
0b - Clock is not running.
1b - Clock is running.
10 IP block status
BLOCK42 This bit provides the clock status of SIUL_VIRTWRAPPER_PDAC3 in partition 1.
0b - Clock is not running.
1b - Clock is running.
9 IP block status
BLOCK41 This bit provides the clock status of SIUL_VIRTWRAPPER_PDAC2_M7_1 in partition 1.
0b - Clock is not running.
1b - Clock is running.
8 IP block status
BLOCK40 This bit provides the clock status of SIUL_VIRTWRAPPER_PDAC2_M7_1 in partition 1.
0b - Clock is not running.
1b - Clock is running.
7 IP block status
BLOCK39 This bit provides the clock status of SIUL_VIRTWRAPPER_PDAC1_M7_0 in partition 1.
0b - Clock is not running.
1b - Clock is running.
6 IP block status
BLOCK38 This bit provides the clock status of SIUL_VIRTWRAPPER_PDAC1_M7_0 in partition 1.
0b - Clock is not running.
1b - Clock is running.
5 IP block status
BLOCK37 This bit provides the clock status of SIUL_VIRTWRAPPER_PDAC0_HSE in partition 1.
Table continues on the next page...

---

*Page 1230*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
0b - Clock is not running.
1b - Clock is running.
4 IP block status
BLOCK36 This bit provides the clock status of SIUL_VIRTWRAPPER_PDAC0_HSE in partition 1.
0b - Clock is not running.
1b - Clock is running.
3 IP block status
BLOCK35 This bit provides the clock status of MC_RGM in partition 1.
0b - Clock is not running.
1b - Clock is running.
2 IP block status
BLOCK34 This bit provides the clock status of RTC in partition 1.
0b - Clock is not running.
1b - Clock is running.
1 IP block status
BLOCK33 This bit provides the clock status of DMAMUX_1 in partition 1.
0b - Clock is not running.
1b - Clock is running.
0 IP block status
BLOCK32 This bit provides the clock status of DMAMUX_0 in partition 1.
0b - Clock is not running.
1b - Clock is running.

#### 43.7.23 Partition 1 COFB Set 2 Clock Status Register (PRTN1_COFB2_STAT)

Offset
Register Offset
PRTN1_COFB2_STAT 318h
Function
This register provides the status of set 2 of COFB s inside partition 1.
NOTE
The reset value of this register can vary depending on the availability of active clock pulses inside partition 1.

---

*Page 1231*

Mode Entry Module (MC_ME)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC
R 0 0 0 0
K95 K93 K92 K91 K89 K88 K87 K86 K85 K84 K81 K80
W
Reset 0 0 1 1 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC
R 0 0 0
K79 K78 K77 K76 K75 K74 K73 K70 K69 K68 K67 K66 K65
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 IP block status
BLOCK95 This bit provides the clock status of TempSense in partition 1.
0b - Clock is not running.
1b - Clock is running.
30 Reserved
— This field is reserved and read returns zeros.
29 IP block status
BLOCK93 This bit provides the clock status of LPCMP_1 in partition 1.
0b - Clock is not running.
1b - Clock is running.
28 IP block status
BLOCK92 This bit provides the clock status of LPCMP_0 in partition 1.
0b - Clock is not running.
1b - Clock is running.
27 IP block status
BLOCK91 This bit provides the clock status of SAI_0 in partition 1.
0b - Clock is not running.
1b - Clock is running.
26 Reserved
— This field is reserved and read returns zeros.
25 IP block status
Table continues on the next page...

---

*Page 1232*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
BLOCK89 This bit provides the clock status of LPSPI_3 in partition 1.
0b - Clock is not running.
1b - Clock is running.
24 IP block status
BLOCK88 This bit provides the clock status of LPSPI_2 in partition 1.
0b - Clock is not running.
1b - Clock is running.
23 IP block status
BLOCK87 This bit provides the clock status of LPSPI_1 in partition 1.
0b - Clock is not running.
1b - Clock is running.
22 IP block status
BLOCK86 This bit provides the clock status of LPSPI_0 in partition 1.
0b - Clock is not running.
1b - Clock is running.
21 IP block status
BLOCK85 This bit provides the clock status of LPI2C_1 in partition 1.
0b - Clock is not running.
1b - Clock is running.
20 IP block status
BLOCK84 This bit provides the clock status of LPI2C_0 in partition 1.
0b - Clock is not running.
1b - Clock is running.
19 Reserved
— This field is reserved and read returns zeros.
18 Reserved
— This field is reserved and read returns zeros.
17 IP block status
BLOCK81 This bit provides the clock status of LPUART_7 in partition 1.
0b - Clock is not running.
1b - Clock is running.
Table continues on the next page...

---

*Page 1233*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
16 IP block status
BLOCK80 This bit provides the clock status of LPUART_6 in partition 1.
0b - Clock is not running.
1b - Clock is running.
15 IP block status
BLOCK79 This bit provides the clock status of LPUART_5 in partition 1.
0b - Clock is not running.
1b - Clock is running.
14 IP block status
BLOCK78 This bit provides the clock status of LPUART_4 in partition 1.
0b - Clock is not running.
1b - Clock is running.
13 IP block status
BLOCK77 This bit provides the clock status of LPUART_3 in partition 1.
0b - Clock is not running.
1b - Clock is running.
12 IP block status
BLOCK76 This bit provides the clock status of LPUART_2 in partition 1.
0b - Clock is not running.
1b - Clock is running.
11 IP block status
BLOCK75 This bit provides the clock status of LPUART_1 in partition 1.
0b - Clock is not running.
1b - Clock is running.
10 IP block status
BLOCK74 This bit provides the clock status of LPUART_0 in partition 1.
0b - Clock is not running.
1b - Clock is running.
9 IP block status
BLOCK73 This bit provides the clock status of FlexIO in partition 1.
0b - Clock is not running.
Table continues on the next page...

---

*Page 1234*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
1b - Clock is running.
8 Reserved
— This field is reserved and read returns zeros.
7 Reserved
— This field is reserved and read returns zeros.
6 IP block status
BLOCK70 This bit provides the clock status of FlexCAN_5 in partition 1.
0b - Clock is not running.
1b - Clock is running.
5 IP block status
BLOCK69 This bit provides the clock status of FlexCAN_4 in partition 1.
0b - Clock is not running.
1b - Clock is running.
4 IP block status
BLOCK68 This bit provides the clock status of FlexCAN_3 in partition 1.
0b - Clock is not running.
1b - Clock is running.
3 IP block status
BLOCK67 This bit provides the clock status of FlexCAN_2 in partition 1.
0b - Clock is not running.
1b - Clock is running.
2 IP block status
BLOCK66 This bit provides the clock status of FlexCAN_1 in partition 1.
0b - Clock is not running.
1b - Clock is running.
1 IP block status
BLOCK65 This bit provides the clock status of FlexCAN_0 in partition 1.
0b - Clock is not running.
1b - Clock is running.
0 Reserved
Table continues on the next page...

---

*Page 1235*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
— This field is reserved and read returns zeros.

#### 43.7.24 Partition 1 COFB Set 3 Clock Status Register (PRTN1_COFB3_STAT)

Offset
Register Offset
PRTN1_COFB3_STAT 31Ch
Function
This register provides the status of set 3 of COFB s inside partition 1.
NOTE
The reset value of this register can vary depending on the availability of active clock pulses inside partition 1.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC
R 0 0 0
K110 K108 K107 K106 K105 K104 K103 K102 K101 K99 K98 K97 K96
W
Reset 0 1 0 1 1 1 1 1 1 1 1 0 1 1 1 0
Fields
Field Function
31 Reserved
— This field is reserved and read returns zeros.
30 Reserved
— This field is reserved and read returns zeros.
29 Reserved
— This field is reserved and read returns zeros.
Table continues on the next page...

---

*Page 1236*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
28 Reserved
— This field is reserved and read returns zeros.
27 Reserved
— This field is reserved and read returns zeros.
26 Reserved
— This field is reserved and read returns zeros.
25 Reserved
— This field is reserved and read returns zeros.
24 Reserved
— This field is reserved and read returns zeros.
23 Reserved
— This field is reserved and read returns zeros.
22 Reserved
— This field is reserved and read returns zeros.
21 Reserved
— This field is reserved and read returns zeros.
20 Reserved
— This field is reserved and read returns zeros.
19 Reserved
— This field is reserved and read returns zeros.
18 Reserved
— This field is reserved and read returns zeros.
17 Reserved
— This field is reserved and read returns zeros.
16 Reserved
— This field is reserved and read returns zeros.
15 Reserved
— This field is reserved and read returns zeros.
14 IP block status
Table continues on the next page...

---

*Page 1237*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
BLOCK110 This bit provides the clock status of Reserved block in partition 1.
0b - Clock is not running.
1b - Clock is running.
13 Reserved
— This field is reserved and read returns zeros.
12 IP block status
BLOCK108 This bit provides the clock status of SELF-TEST GPR in partition 1.
0b - Clock is not running.
1b - Clock is running.
11 IP block status
BLOCK107 This bit provides the clock status of Reserved block in partition 1.
0b - Clock is not running.
1b - Clock is running.
10 IP block status
BLOCK106 This bit provides the clock status of Reserved block in partition 1.
0b - Clock is not running.
1b - Clock is running.
9 IP block status
BLOCK105 This bit provides the clock status of Reserved block in partition 1.
0b - Clock is not running.
1b - Clock is running.
8 IP block status
BLOCK104 This bit provides the clock status of STCU2 in partition 1.
0b - Clock is not running.
1b - Clock is running.
7 IP block status
BLOCK103 This bit provides the clock status of Reserved block in partition 1.
0b - Clock is not running.
1b - Clock is running.
6 IP block status
BLOCK102 This bit provides the clock status of Reserved block in partition 1.
Table continues on the next page...

---

*Page 1238*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
0b - Clock is not running.
1b - Clock is running.
5 IP block status
BLOCK101 This bit provides the clock status of JDC in partition 1.
0b - Clock is not running.
1b - Clock is running.
4 Reserved
— This field is reserved and read returns zeros.
3 IP block status
BLOCK99 This bit provides the clock status of HSE in partition 1.
0b - Clock is not running.
1b - Clock is running.
2 IP block status
BLOCK98 This bit provides the clock status of MTR in partition 1.
0b - Clock is not running.
1b - Clock is running.
1 IP block status
BLOCK97 This bit provides the clock status of FCCU in partition 1.
0b - Clock is not running.
1b - Clock is running.
0 IP block status
BLOCK96 This bit provides the clock status of CRC in partition 1.
0b - Clock is not running.
1b - Clock is running.

#### 43.7.25 Partition 1 COFB Set 0 Clock Enable Register (PRTN1_COFB0_CLKEN)

Offset
Register Offset
PRTN1_COFB0_CLKEN 330h

---

*Page 1239*

Mode Entry Module (MC_ME)
Function
This register provides clock control signaling to the individual COFBs in set 0 inside partition 1. Whenever a partition clock enable
(non-core) hardware process is initiated, the value of logic-1 in the corresponding bit locations of this register enables the clock
to the corresponding block in the partition.
NOTE
The reset value of this register is not defined and is as per the availability of the clock source. See Chip-specific
MC_ME information for clock source availability.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0 0 0 0 0 0
REQ3 REQ2 REQ2 REQ2 REQ2 REQ2 REQ2
1 9 8 4 3 2 1
W
Reset 0 1 0 1 1 1 1 0 0 0 1 1 1 1 1 1
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
REQ1 REQ1 REQ1 REQ1 REQ1 REQ1
REQ9 REQ8 REQ7 REQ6 REQ5 REQ4 REQ3
5 4 3 2 1 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1 1
Fields
Field Function
31 Clock enable
REQ31 This bit provides the clock enable control for INTM in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
30 Reserved
— This field is reserved and read returns zeros.
29 Clock enable
REQ29 This bit provides the clock enable control for STM_0 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
28 Clock enable
REQ28 This bit provides the clock enable control for SWT_0 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
27 Reserved
Table continues on the next page...

---

*Page 1240*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
— This field is reserved and read returns zeros.
26 Reserved
— This field is reserved and read returns zeros.
25 Reserved
— This field is reserved and read returns zeros.
24 Clock enable
REQ24 This bit provides the clock enable control for MSCM in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
23 Clock enable
REQ23 This bit provides the clock enable control for ERM in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
22 Clock enable
REQ22 This bit provides the clock enable control for EIM in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
21 Clock enable
REQ21 This bit provides the clock enable control for SDA-AP in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
20 Reserved
— This field is reserved and read returns zeros.
19 Reserved
— This field is reserved and read returns zeros.
18 Reserved
— This field is reserved and read returns zeros.
17 Reserved
— This field is reserved and read returns zeros.
16 Reserved
Table continues on the next page...

---

*Page 1241*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
— This field is reserved and read returns zeros.
15 Clock enable
REQ15 This bit provides the clock enable control for eDMA in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
14 Clock enable
REQ14 This bit provides the clock enable control for eDMA in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
13 Clock enable
REQ13 This bit provides the clock enable control for eDMA in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
12 Clock enable
REQ12 This bit provides the clock enable control for eDMA in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
11 Clock enable
REQ11 This bit provides the clock enable control for eDMA in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
10 Clock enable
REQ10 This bit provides the clock enable control for eDMA in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
9 Clock enable
REQ9 This bit provides the clock enable control for eDMA in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
8 Clock enable
REQ8 This bit provides the clock enable control for eDMA in partition 1.
Table continues on the next page...

---

*Page 1242*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
0b - Clock is turned off.
1b - Clock is turned on.
7 Clock enable
REQ7 This bit provides the clock enable control for eDMA in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
6 Clock enable
REQ6 This bit provides the clock enable control for eDMA in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
5 Clock enable
REQ5 This bit provides the clock enable control for eDMA in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
4 Clock enable
REQ4 This bit provides the clock enable control for eDMA in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
3 Clock enable
REQ3 This bit provides the clock enable control for eDMA in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
2 Reserved
— This field is reserved and read returns zeros.
1 Reserved
— This field is reserved and read returns zeros.
0 Reserved
— This field is reserved and read returns zeros.

---

*Page 1243*

Mode Entry Module (MC_ME)

#### 43.7.26 Partition 1 COFB Set 1 Clock Enable Register (PRTN1_COFB1_CLKEN)

Offset
Register Offset
PRTN1_COFB1_CLKEN 334h
Function
This register provides clock control signaling to the individual COFBs in set 1 inside partition 1. Whenever a partition clock enable
(non-core) hardware process is initiated, the value of logic-1 in the corresponding bit locations of this register enables the clock
to the corresponding block in the partition.
NOTE
The reset value of this register is not defined and is as per the availability of the clock source. See Chip-specific
MC_ME information for clock source availability.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0 0 0 0 0 0 0 0
REQ6 REQ5 REQ5 REQ5 REQ4
3 6 3 1 9
W
Reset 0 0 0 1 1 1 0 0 1 1 1 1 1 1 1 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0 0 0 0 0 0
REQ4 REQ4 REQ4 REQ3 REQ3 REQ3
7 5 2 4 3 2
W
Reset 0 0 1 0 1 1 1 1 1 1 1 1 1 1 0 0
Fields
Field Function
31 Clock enable
REQ63 This bit provides the clock enable control for PIT_2 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
30 Reserved
— This field is reserved and read returns zeros.
29 Reserved
— This field is reserved and read returns zeros.
28 Reserved
Table continues on the next page...

---

*Page 1244*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
— This field is reserved and read returns zeros.
27 Reserved
— This field is reserved and read returns zeros.
26 Reserved
— This field is reserved and read returns zeros.
25 Reserved
— This field is reserved and read returns zeros.
24 Clock enable
REQ56 This bit provides the clock enable control for PLL in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
23 Reserved
— This field is reserved and read returns zeros.
22 Reserved
— This field is reserved and read returns zeros.
21 Clock enable
REQ53 This bit provides the clock enable control for FXOSC in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
20 Reserved
— This field is reserved and read returns zeros.
19 Clock enable
REQ51 This bit provides the clock enable control for SXOSC in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
18 Reserved
— This field is reserved and read returns zeros.
17 Clock enable
REQ49 This bit provides the clock enable control for TSPC in partition 1.
Table continues on the next page...

---

*Page 1245*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
0b - Clock is turned off.
1b - Clock is turned on.
16 Reserved
— This field is reserved and read returns zeros.
15 Clock enable
REQ47 This bit provides the clock enable control for CMU_0-5 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
14 Reserved
— This field is reserved and read returns zeros.
13 Clock enable
REQ45 This bit provides the clock enable control for WKPU in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
12 Reserved
— This field is reserved and read returns zeros.
11 Reserved
— This field is reserved and read returns zeros.
10 Clock enable
REQ42 This bit provides the clock enable control for SIUL_VIRTWRAPPER_PDAC3 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
9 Reserved
— This field is reserved and read returns zeros.
8 Reserved
— This field is reserved and read returns zeros.
7 Reserved
— This field is reserved and read returns zeros.
6 Reserved
— This field is reserved and read returns zeros.
Table continues on the next page...

---

*Page 1246*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
5 Reserved
— This field is reserved and read returns zeros.
4 Reserved
— This field is reserved and read returns zeros.
3 Reserved
— This field is reserved and read returns zeros.
2 Clock enable
REQ34 This bit provides the clock enable control for RTC in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
1 Clock enable
REQ33 This bit provides the clock enable control for DMAMUX_1 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
0 Clock enable
REQ32 This bit provides the clock enable control for DMAMUX_0 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.

#### 43.7.27 Partition 1 COFB Set 2 Clock Enable Register (PRTN1_COFB2_CLKEN)

Offset
Register Offset
PRTN1_COFB2_CLKEN 338h
Function
This register provides clock control signaling to the individual COFBs in set 2 inside partition 1. Whenever a partition clock enable
(non-core) hardware process is initiated, the value of logic-1 in the corresponding bit locations of this register enables the clock
to the corresponding block in the partition.
NOTE
The reset value of this register is not defined and is as per the availability of the clock source. See Chip-specific
MC_ME information for clock source availability.

---

*Page 1247*

Mode Entry Module (MC_ME)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0
REQ9 REQ9 REQ9 REQ9 REQ8 REQ8 REQ8 REQ8 REQ8 REQ8 REQ8 REQ8
5 3 2 1 9 8 7 6 5 4 1 0
W
Reset 0 0 1 1 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0
REQ7 REQ7 REQ7 REQ7 REQ7 REQ7 REQ7 REQ7 REQ6 REQ6 REQ6 REQ6 REQ6
9 8 7 6 5 4 3 0 9 8 7 6 5
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Clock enable
REQ95 This bit provides the clock enable control for TempSense in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
30 Reserved
— This field is reserved and read returns zeros.
29 Clock enable
REQ93 This bit provides the clock enable control for LPCMP_1 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
28 Clock enable
REQ92 This bit provides the clock enable control for LPCMP_0 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
27 Clock enable
REQ91 This bit provides the clock enable control for SAI_0 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
26 Reserved
— This field is reserved and read returns zeros.
25 Clock enable
Table continues on the next page...

---

*Page 1248*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
REQ89 This bit provides the clock enable control for LPSPI_3 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
24 Clock enable
REQ88 This bit provides the clock enable control for LPSPI_2 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
23 Clock enable
REQ87 This bit provides the clock enable control for LPSPI_1 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
22 Clock enable
REQ86 This bit provides the clock enable control for LPSPI_0 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
21 Clock enable
REQ85 This bit provides the clock enable control for LPI2C_1 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
20 Clock enable
REQ84 This bit provides the clock enable control for LPI2C_0 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
19 Reserved
— This field is reserved and read returns zeros.
18 Reserved
— This field is reserved and read returns zeros.
17 Clock enable
REQ81 This bit provides the clock enable control for LPUART_7 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
Table continues on the next page...

---

*Page 1249*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
16 Clock enable
REQ80 This bit provides the clock enable control for LPUART_6 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
15 Clock enable
REQ79 This bit provides the clock enable control for LPUART_5 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
14 Clock enable
REQ78 This bit provides the clock enable control for LPUART_4 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
13 Clock enable
REQ77 This bit provides the clock enable control for LPUART_3 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
12 Clock enable
REQ76 This bit provides the clock enable control for LPUART_2 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
11 Clock enable
REQ75 This bit provides the clock enable control for LPUART_1 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
10 Clock enable
REQ74 This bit provides the clock enable control for LPUART_0 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
9 Clock enable
REQ73 This bit provides the clock enable control for FlexIO in partition 1.
0b - Clock is turned off.
Table continues on the next page...

---

*Page 1250*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
1b - Clock is turned on.
8 Reserved
— This field is reserved and read returns zeros.
7 Reserved
— This field is reserved and read returns zeros.
6 Clock enable
REQ70 This bit provides the clock enable control for FlexCAN_5 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
5 Clock enable
REQ69 This bit provides the clock enable control for FlexCAN_4 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
4 Clock enable
REQ68 This bit provides the clock enable control for FlexCAN_3 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
3 Clock enable
REQ67 This bit provides the clock enable control for FlexCAN_2 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
2 Clock enable
REQ66 This bit provides the clock enable control for FlexCAN_1 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
1 Clock enable
REQ65 This bit provides the clock enable control for FlexCAN_0 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
0 Reserved
Table continues on the next page...

---

*Page 1251*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
— This field is reserved and read returns zeros.

#### 43.7.28 Partition 1 COFB Set 3 Clock Enable Register (PRTN1_COFB3_CLKEN)

Offset
Register Offset
PRTN1_COFB3_CLKEN 33Ch
Function
This register provides clock control signaling to the individual COFBs in set 3 inside partition 1. Whenever a partition clock enable
(non-core) hardware process is initiated, the value of logic-1 in the corresponding bit locations of this register enables the clock
to the corresponding block in the partition.
NOTE
The reset value of this register is not defined and is as per the availability of the clock source. See Chip-specific
MC_ME information for clock source availability.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0 0 0 0 0 0 0 0 0
REQ1 REQ1 REQ9
04 02 6
W
Reset 0 1 0 1 1 1 1 1 1 1 1 0 1 1 1 0
Fields
Field Function
31 Reserved
— This field is reserved and read returns zeros.
30 Reserved
— This field is reserved and read returns zeros.
29 Reserved
Table continues on the next page...

---

*Page 1252*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
— This field is reserved and read returns zeros.
28 Reserved
— This field is reserved and read returns zeros.
27 Reserved
— This field is reserved and read returns zeros.
26 Reserved
— This field is reserved and read returns zeros.
25 Reserved
— This field is reserved and read returns zeros.
24 Reserved
— This field is reserved and read returns zeros.
23 Reserved
— This field is reserved and read returns zeros.
22 Reserved
— This field is reserved and read returns zeros.
21 Reserved
— This field is reserved and read returns zeros.
20 Reserved
— This field is reserved and read returns zeros.
19 Reserved
— This field is reserved and read returns zeros.
18 Reserved
— This field is reserved and read returns zeros.
17 Reserved
— This field is reserved and read returns zeros.
16 Reserved
— This field is reserved and read returns zeros.
15 Reserved
— This field is reserved and read returns zeros.
Table continues on the next page...

---

*Page 1253*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
14 Reserved
— This field is reserved and read returns zeros.
13 Reserved
— This field is reserved and read returns zeros.
12 Reserved
— This field is reserved and read returns zeros.
11 Reserved
— This field is reserved and read returns zeros.
10 Reserved
— This field is reserved and read returns zeros.
9 Reserved
— This field is reserved and read returns zeros.
8 Clock enable
REQ104 This bit provides the clock enable control for STCU2 in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
7 Reserved
— This field is reserved and read returns zeros.
6 Clock enable
REQ102 This bit provides the clock enable control for Reserved block in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.
5 Reserved
— This field is reserved and read returns zeros.
4 Reserved
— This field is reserved and read returns zeros.
3 Reserved
— This field is reserved and read returns zeros.
2 Reserved
Table continues on the next page...

---

*Page 1254*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
— This field is reserved and read returns zeros.
1 Reserved
— This field is reserved and read returns zeros.
0 Clock enable
REQ96 This bit provides the clock enable control for CRC in partition 1.
0b - Clock is turned off.
1b - Clock is turned on.

#### 43.7.29 Partition 2 Process Configuration Register (PRTN2_PCONF)

Offset
Register Offset
PRTN2_PCONF 500h
Function
This register provides a configuration for the hardware processes corresponding to partition 2. Each of the configuration bit
corresponds to the 'nature' of the processes; for example, enabling/disabling and the trigger is controlled by the corresponding
field in the PRTN2_PUPD register. When valid KEY combinations are written onto the CTL_KEY register, the PRTN2_PCONF
and PRTN2_PUPD registers are used to determine the hardware processes to be executed. These are triggered in parallel and
independent of each other. All dependent processes should be requested one after another from the software.
NOTE
The partition clock enable/disable are not standalone and must be done coherently in a fixed sequence. For details,
see Software Reset Partition Turn-On Flow Chart and Software reset partition turn-off flowchart in Reset chapter.
NOTE
See chip-specific MC_ME information to check if this register is implemented on chip.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0 0 0
PCE
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1

---

*Page 1255*

Mode Entry Module (MC_ME)
Fields
Field Function
31-7 Reserved
— This field is reserved and read returns zeros.
6 Reserved
— This field is reserved and read returns zeros.
5 Reserved
— This field is reserved and read returns zeros.
4 Reserved
— This field is reserved and read returns zeros.
3 Reserved
— This field is reserved and read returns zeros.
2 Reserved
— This field is reserved and read returns zeros.
1 Reserved
— This field is reserved and read returns zeros.
0 Partition clock enable
PCE This bit controls whether the clock to IPs (other than core(s)) in the partition should be enabled or disabled.
0b - Disable the clock to IPs
1b - Enable the clock to IPs

#### 43.7.30 Partition 2 Process Update Register (PRTN2_PUPD)

Offset
Register Offset
PRTN2_PUPD 504h
Function
This register provides trigger signaling for the hardware processes corresponding to partition 2. Each of the control bit acts as
a trigger for the corresponding hardware processes. When valid KEY combinations are written onto the CTL_KEY register, the
hardware checks the bit fields that are programmed as logic-1 in this register, and then triggers the hardware process per the value
in the corresponding bit field in the PRTN2_PCONF register. When the hardware process is finished the corresponding bit in this
register is auto-cleared to logic-0.
NOTE
See chip-specific MC_ME information to check if this register is implemented on chip.

---

*Page 1256*

Mode Entry Module (MC_ME)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0 0 0
PCUD
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31-7 Reserved
— This field is reserved and read returns zeros.
6 Reserved
— This field is reserved and read returns zeros.
5 Reserved
— This field is reserved and read returns zeros.
4 Reserved
— This field is reserved and read returns zeros.
3 Reserved
— This field is reserved and read returns zeros.
2 Reserved
— This field is reserved and read returns zeros.
1 Reserved
— This field is reserved and read returns zeros.
0 Partition clock update
PCUD This bit controls whether the hardware processes for enabling/disabling the clock to IPs (other than core(s))
in the partition should be triggered or not.
0b - Do not trigger the hardware process
1b - Trigger the hardware process

---

*Page 1257*

Mode Entry Module (MC_ME)

#### 43.7.31 Partition 2 Status Register (PRTN2_STAT)

Offset
Register Offset
PRTN2_STAT 508h
Function
This register provides the current status of the control signals from the partition 2.
NOTE
See chip-specific MC_ME information to check if this register is implemented on chip.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0 0 0 PCS
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1
Fields
Field Function
31-7 Reserved
— This field is reserved and read returns zeros.
6 Reserved
— This field is reserved and read returns zeros.
5 Reserved
— This field is reserved and read returns zeros.
4 Reserved
— This field is reserved and read returns zeros.
3 Reserved
— This field is reserved and read returns zeros.
2 Reserved
Table continues on the next page...

---

*Page 1258*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
— This field is reserved and read returns zeros.
1 Reserved
— This field is reserved and read returns zeros.
0 Partition clock status
PCS This bit provides the status of the clock to partition.
0b - Clock is inactive
1b - Clock is active

#### 43.7.32 Partition 2 COFB Set 0 Clock Status Register (PRTN2_COFB0_STAT)

Offset
Register Offset
PRTN2_COFB0_STAT 510h
Function
This register provides the status of set 0 of COFB s inside partition 2.
NOTE
The reset value of this register can vary depending on the availability of active clock pulses inside partition 2.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC
R 0 0 0 0 0
K29 K25 K24 K23 K22 K21 K20 K19 K18 K17 K16
W
Reset 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC
R 0 0
K15 K14 K13 K12 K11 K10 K9 K8 K7 K6 K5 K4 K1 K0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1

---

*Page 1259*

Mode Entry Module (MC_ME)
Fields
Field Function
31 Reserved
— This field is reserved and read returns zeros.
30 Reserved
— This field is reserved and read returns zeros.
29 IP block status
BLOCK29 This bit provides the clock status of STM_1 in partition 2.
0b - Clock is not running.
1b - Clock is running.
28 Reserved
— This field is reserved and read returns zeros.
27 Reserved
— This field is reserved and read returns zeros.
26 Reserved
— This field is reserved and read returns zeros.
25 IP block status
BLOCK25 This bit provides the clock status of PRAM_1 in partition 2.
0b - Clock is not running.
1b - Clock is running.
24 IP block status
BLOCK24 This bit provides the clock status of SEMA42 in partition 2.
0b - Clock is not running.
1b - Clock is running.
23 IP block status
BLOCK23 This bit provides the clock status of eDMA in partition 2.
0b - Clock is not running.
1b - Clock is running.
22 IP block status
BLOCK22 This bit provides the clock status of eDMA in partition 2.
0b - Clock is not running.
1b - Clock is running.
Table continues on the next page...

---

*Page 1260*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
21 IP block status
BLOCK21 This bit provides the clock status of eDMA in partition 2.
0b - Clock is not running.
1b - Clock is running.
20 IP block status
BLOCK20 This bit provides the clock status of eDMA in partition 2.
0b - Clock is not running.
1b - Clock is running.
19 IP block status
BLOCK19 This bit provides the clock status of eDMA in partition 2.
0b - Clock is not running.
1b - Clock is running.
18 IP block status
BLOCK18 This bit provides the clock status of eDMA in partition 2.
0b - Clock is not running.
1b - Clock is running.
17 IP block status
BLOCK17 This bit provides the clock status of eDMA in partition 2.
0b - Clock is not running.
1b - Clock is running.
16 IP block status
BLOCK16 This bit provides the clock status of eDMA in partition 2.
0b - Clock is not running.
1b - Clock is running.
15 IP block status
BLOCK15 This bit provides the clock status of eDMA in partition 2.
0b - Clock is not running.
1b - Clock is running.
14 IP block status
BLOCK14 This bit provides the clock status of eDMA in partition 2.
0b - Clock is not running.
Table continues on the next page...

---

*Page 1261*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
1b - Clock is running.
13 IP block status
BLOCK13 This bit provides the clock status of eDMA in partition 2.
0b - Clock is not running.
1b - Clock is running.
12 IP block status
BLOCK12 This bit provides the clock status of eDMA in partition 2.
0b - Clock is not running.
1b - Clock is running.
11 IP block status
BLOCK11 This bit provides the clock status of eDMA in partition 2.
0b - Clock is not running.
1b - Clock is running.
10 IP block status
BLOCK10 This bit provides the clock status of eDMA in partition 2.
0b - Clock is not running.
1b - Clock is running.
9 IP block status
BLOCK9 This bit provides the clock status of eDMA in partition 2.
0b - Clock is not running.
1b - Clock is running.
8 IP block status
BLOCK8 This bit provides the clock status of eDMA in partition 2.
0b - Clock is not running.
1b - Clock is running.
7 IP block status
BLOCK7 This bit provides the clock status of eDMA in partition 2.
0b - Clock is not running.
1b - Clock is running.
6 IP block status
BLOCK6 This bit provides the clock status of eDMA in partition 2.
Table continues on the next page...

---

*Page 1262*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
0b - Clock is not running.
1b - Clock is running.
5 IP block status
BLOCK5 This bit provides the clock status of eDMA in partition 2.
0b - Clock is not running.
1b - Clock is running.
4 IP block status
BLOCK4 This bit provides the clock status of eDMA in partition 2.
0b - Clock is not running.
1b - Clock is running.
3 Reserved
— This field is reserved and read returns zeros.
2 Reserved
— This field is reserved and read returns zeros.
1 IP block status
BLOCK1 This bit provides the clock status of XBIC in partition 2.
0b - Clock is not running.
1b - Clock is running.
0 IP block status
BLOCK0 This bit provides the clock status of XBIC in partition 2.
0b - Clock is not running.
1b - Clock is running.

#### 43.7.33 Partition 2 COFB Set 1 Clock Status Register (PRTN2_COFB1_STAT)

Offset
Register Offset
PRTN2_COFB1_STAT 514h
Function
This register provides the status of set 1 of COFB s inside partition 2.

---

*Page 1263*

Mode Entry Module (MC_ME)
NOTE
The reset value of this register can vary depending on the availability of active clock pulses inside partition 2.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
BLOC BLOC BLOC BLOC BLOC BLOC BLOC
R 0 0 0 0 0 0 0 0 0
K63 K62 K59 K58 K55 K51 K48
W
Reset 1 1 0 0 1 1 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC BLOC
R 0 0 0 0 0 0
K47 K42 K41 K40 K39 K38 K37 K36 K35 K32
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 IP block status
BLOCK63 This bit provides the clock status of CM7_1_TCM in partition 2.
0b - Clock is not running.
1b - Clock is running.
30 IP block status
BLOCK62 This bit provides the clock status of CM7_0_TCM in partition 2.
0b - Clock is not running.
1b - Clock is running.
29 Reserved
— This field is reserved and read returns zeros.
28 Reserved
— This field is reserved and read returns zeros.
27 IP block status
BLOCK59 This bit provides the clock status of HSE in partition 2.
0b - Clock is not running.
1b - Clock is running.
26 IP block status
BLOCK58 This bit provides the clock status of LPCMP_2 in partition 2.
Table continues on the next page...

---

*Page 1264*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
0b - Clock is not running.
1b - Clock is running.
25 Reserved
— This field is reserved and read returns zeros.
24 Reserved
— This field is reserved and read returns zeros.
23 IP block status
BLOCK55 This bit provides the clock status of SAI_1 in partition 2.
0b - Clock is not running.
1b - Clock is running.
22 Reserved
— This field is reserved and read returns zeros.
21 Reserved
— This field is reserved and read returns zeros.
20 Reserved
— This field is reserved and read returns zeros.
19 IP block status
BLOCK51 This bit provides the clock status of QuadSPI in partition 2.
0b - Clock is not running.
1b - Clock is running.
18 Reserved
— This field is reserved and read returns zeros.
17 Reserved
— This field is reserved and read returns zeros.
16 IP block status
BLOCK48 This bit provides the clock status of LPSPI_5 in partition 2.
0b - Clock is not running.
1b - Clock is running.
15 IP block status
BLOCK47 This bit provides the clock status of LPSPI_4 in partition 2.
Table continues on the next page...

---

*Page 1265*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
0b - Clock is not running.
1b - Clock is running.
14 Reserved
— This field is reserved and read returns zeros.
13 Reserved
— This field is reserved and read returns zeros.
12 Reserved
— This field is reserved and read returns zeros.
11 Reserved
— This field is reserved and read returns zeros.
10 IP block status
BLOCK42 This bit provides the clock status of LPUART_15 in partition 2.
0b - Clock is not running.
1b - Clock is running.
9 IP block status
BLOCK41 This bit provides the clock status of LPUART_14 in partition 2.
0b - Clock is not running.
1b - Clock is running.
8 IP block status
BLOCK40 This bit provides the clock status of LPUART_13 in partition 2.
0b - Clock is not running.
1b - Clock is running.
7 IP block status
BLOCK39 This bit provides the clock status of LPUART_12 in partition 2.
0b - Clock is not running.
1b - Clock is running.
6 IP block status
BLOCK38 This bit provides the clock status of LPUART_11 in partition 2.
0b - Clock is not running.
1b - Clock is running.
Table continues on the next page...

---

*Page 1266*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
5 IP block status
BLOCK37 This bit provides the clock status of LPUART_10 in partition 2.
0b - Clock is not running.
1b - Clock is running.
4 IP block status
BLOCK36 This bit provides the clock status of LPUART_9 in partition 2.
0b - Clock is not running.
1b - Clock is running.
3 IP block status
BLOCK35 This bit provides the clock status of LPUART_8 in partition 2.
0b - Clock is not running.
1b - Clock is running.
2 Reserved
— This field is reserved and read returns zeros.
1 Reserved
— This field is reserved and read returns zeros.
0 IP block status
BLOCK32 This bit provides the clock status of EMAC in partition 2.
0b - Clock is not running.
1b - Clock is running.

#### 43.7.34 Partition 2 COFB Set 0 Clock Enable Register (PRTN2_COFB0_CLKEN)

Offset
Register Offset
PRTN2_COFB0_CLKEN 530h
Function
This register provides clock control signaling to the individual COFBs in set 0 inside partition 2. Whenever a partition clock enable
(non-core) hardware process is initiated, the value of logic-1 in the corresponding bit locations of this register enables the clock
to the corresponding block in the partition.
NOTE
The reset value of this register is not defined and is as per the availability of the clock source. See Chip-specific
MC_ME information for clock source availability.

---

*Page 1267*

Mode Entry Module (MC_ME)
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0 0 0
REQ2 REQ2 REQ2 REQ2 REQ2 REQ2 REQ1 REQ1 REQ1 REQ1
9 4 3 2 1 0 9 8 7 6
W
Reset 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0
REQ1 REQ1 REQ1 REQ1 REQ1 REQ1
REQ9 REQ8 REQ7 REQ6 REQ5 REQ4
5 4 3 2 1 0
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1
Fields
Field Function
31 Reserved
— This field is reserved and read returns zeros.
30 Reserved
— This field is reserved and read returns zeros.
29 Clock enable
REQ29 This bit provides the clock enable control for STM_1 in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
28 Reserved
— This field is reserved and read returns zeros.
27 Reserved
— This field is reserved and read returns zeros.
26 Reserved
— This field is reserved and read returns zeros.
25 Reserved
— This field is reserved and read returns zeros.
24 Clock enable
REQ24 This bit provides the clock enable control for SEMA42 in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
23 Clock enable
Table continues on the next page...

---

*Page 1268*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
REQ23 This bit provides the clock enable control for eDMA in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
22 Clock enable
REQ22 This bit provides the clock enable control for eDMA in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
21 Clock enable
REQ21 This bit provides the clock enable control for eDMA in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
20 Clock enable
REQ20 This bit provides the clock enable control for eDMA in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
19 Clock enable
REQ19 This bit provides the clock enable control for eDMA in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
18 Clock enable
REQ18 This bit provides the clock enable control for eDMA in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
17 Clock enable
REQ17 This bit provides the clock enable control for eDMA in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
16 Clock enable
REQ16 This bit provides the clock enable control for eDMA in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
Table continues on the next page...

---

*Page 1269*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
15 Clock enable
REQ15 This bit provides the clock enable control for eDMA in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
14 Clock enable
REQ14 This bit provides the clock enable control for eDMA in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
13 Clock enable
REQ13 This bit provides the clock enable control for eDMA in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
12 Clock enable
REQ12 This bit provides the clock enable control for eDMA in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
11 Clock enable
REQ11 This bit provides the clock enable control for eDMA in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
10 Clock enable
REQ10 This bit provides the clock enable control for eDMA in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
9 Clock enable
REQ9 This bit provides the clock enable control for eDMA in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
8 Clock enable
REQ8 This bit provides the clock enable control for eDMA in partition 2.
0b - Clock is turned off.
Table continues on the next page...

---

*Page 1270*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
1b - Clock is turned on.
7 Clock enable
REQ7 This bit provides the clock enable control for eDMA in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
6 Clock enable
REQ6 This bit provides the clock enable control for eDMA in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
5 Clock enable
REQ5 This bit provides the clock enable control for eDMA in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
4 Clock enable
REQ4 This bit provides the clock enable control for eDMA in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
3 Reserved
— This field is reserved and read returns zeros.
2 Reserved
— This field is reserved and read returns zeros.
1 Reserved
— This field is reserved and read returns zeros.
0 Reserved
— This field is reserved and read returns zeros.

#### 43.7.35 Partition 2 COFB Set 1 Clock Enable Register (PRTN2_COFB1_CLKEN)

Offset
Register Offset
PRTN2_COFB1_CLKEN 534h

---

*Page 1271*

Mode Entry Module (MC_ME)
Function
This register provides clock control signaling to the individual COFBs in set 1 inside partition 2. Whenever a partition clock enable
(non-core) hardware process is initiated, the value of logic-1 in the corresponding bit locations of this register enables the clock
to the corresponding block in the partition.
NOTE
The reset value of this register is not defined and is as per the availability of the clock source. See Chip-specific
MC_ME information for clock source availability.
Diagram
Bits 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R 0 0 0 0 0 0 0 0 0 0
REQ6 REQ6 REQ5 REQ5 REQ5 REQ4
3 2 8 5 1 8
W
Reset 1 1 0 0 1 1 0 0 0 0 0 0 0 0 0 0
Bits 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R 0 0 0 0 0 0
REQ4 REQ4 REQ4 REQ4 REQ3 REQ3 REQ3 REQ3 REQ3 REQ3
7 2 1 0 9 8 7 6 5 2
W
Reset 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
Fields
Field Function
31 Clock enable
REQ63 This bit provides the clock enable control for CM7_1_TCM in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
30 Clock enable
REQ62 This bit provides the clock enable control for CM7_0_TCM in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
29 Reserved
— This field is reserved and read returns zeros.
28 Reserved
— This field is reserved and read returns zeros.
27 Reserved
— This field is reserved and read returns zeros.
26 Clock enable
Table continues on the next page...

---

*Page 1272*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
REQ58 This bit provides the clock enable control for LPCMP_2 in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
25 Reserved
— This field is reserved and read returns zeros.
24 Reserved
— This field is reserved and read returns zeros.
23 Clock enable
REQ55 This bit provides the clock enable control for SAI_1 in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
22 Reserved
— This field is reserved and read returns zeros.
21 Reserved
— This field is reserved and read returns zeros.
20 Reserved
— This field is reserved and read returns zeros.
19 Clock enable
REQ51 This bit provides the clock enable control for QuadSPI in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
18 Reserved
— This field is reserved and read returns zeros.
17 Reserved
— This field is reserved and read returns zeros.
16 Clock enable
REQ48 This bit provides the clock enable control for LPSPI_5 in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
15 Clock enable
Table continues on the next page...

---

*Page 1273*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
REQ47 This bit provides the clock enable control for LPSPI_4 in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
14 Reserved
— This field is reserved and read returns zeros.
13 Reserved
— This field is reserved and read returns zeros.
12 Reserved
— This field is reserved and read returns zeros.
11 Reserved
— This field is reserved and read returns zeros.
10 Clock enable
REQ42 This bit provides the clock enable control for LPUART_15 in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
9 Clock enable
REQ41 This bit provides the clock enable control for LPUART_14 in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
8 Clock enable
REQ40 This bit provides the clock enable control for LPUART_13 in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
7 Clock enable
REQ39 This bit provides the clock enable control for LPUART_12 in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
6 Clock enable
REQ38 This bit provides the clock enable control for LPUART_11 in partition 2.
0b - Clock is turned off.
Table continues on the next page...

---

*Page 1274*

Mode Entry Module (MC_ME)
Table continued from the previous page...
Field Function
1b - Clock is turned on.
5 Clock enable
REQ37 This bit provides the clock enable control for LPUART_10 in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
4 Clock enable
REQ36 This bit provides the clock enable control for LPUART_9 in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
3 Clock enable
REQ35 This bit provides the clock enable control for LPUART_8 in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.
2 Reserved
— This field is reserved and read returns zeros.
1 Reserved
— This field is reserved and read returns zeros.
0 Clock enable
REQ32 This bit provides the clock enable control for EMAC in partition 2.
0b - Clock is turned off.
1b - Clock is turned on.

#### 43.8 Glossary

WFI Wait for interrupt
COFB Collection of functional blocks also referred as number of peripherals

---

*Page 1275*

