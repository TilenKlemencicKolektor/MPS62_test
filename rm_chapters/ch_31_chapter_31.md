<!-- Source: MCXE31XRM (1)-for-ai.md | Chapter: Chapter 31 -->

# Chapter 31

# Boot Overview

#### 31.1 Overview

#### 31.1.1 Introduction

This chapter describes the system boot sequence and provides details of boot options. The only CPU available when the hardware
reset sequence completes is in the HSE subsystem hereafter referred to as HSE CPU.
The HSE CPU starts executing firmware code in HSE code flash from fixed location that contains the Secure BAF code . Secure
BAF provides the boot sequence until control is passed as determined by the type of boot:
• Non-Secure boot - To customer software that executes on one or all the application cores.
• Secure boot - To the HSE FW running on HSE CPU.

#### 31.2 Secure BAF features

The features of Secure BAF are as follows:
• Secure and Non-secure boot modes
• Application boot core selection
• Chip LC advancement
• Debug authorization
• XRDC configuration

#### 31.3 Chip Configuration

This section describes the chip configuration details for MCXE31 variants when Secure BAF is programmed and HSE Firmware
feature flag is disabled.
If HSE firmware feature flag is enabled see the HSE Firmware Reference manual.

#### 31.3.1 Memory map

This section explains the memory sections used by the Secure BAF in various configurations.
31.3.1.1 Memory map when HSE firmware usage feature flag is disabled
Table 162. Memory map when HSE firmware usage feature flag is disabled
Memory Section MCXE315/MCXE316 MCXE317 MCXE31B
Flash memory 1 MB 2 MB 4 MB
IVT 0040_0000h, 0040_0000h, 0040_0000h,
Locations in Priority order 0048_0000h, 0050_0000h, 0050_0000h,
1000_0000h 1000_0000h 0060_0000h,
0070_0000h
1000_0000h
Table continues on the next page...

---

*Page 789*

Boot Overview
Table 162. Memory map when HSE firmware usage feature flag is disabled (continued)
Memory Section MCXE315/MCXE316 MCXE317 MCXE31B
Flash memory 1 MB 2 MB 4 MB
Secure BAF Boot 004F_FC00h 005F_FC00h 007F_FC00h
Location
Secure BAF code region 004F_4000h 005F_4000h to 007F_4000h
005F_FFFFh (48 KB)
to 004F_FFFFh to 007F_FFFFh
(48 KB) (48 KB)
Application Flash area 00400_000h 00400_000h 0040_0000h
to 004F_3FFFh (976 KB) to 005F_3FFFh (2000 KB) to 007F_3FFFh (4048 KB)
1000_0000h to 1000_0000h to 1001_FFFFh (128 KB)
Application Data Flash
1000_FFFFh (64 KB)
1B00_0200h to 1B00_06FFh (1280 Bytes)
Secure BAF UTEST
flash
31.3.1.2 Memory map when HSE firmware usage feature flag is enabled
Following table explains the memory sections used by the Secure BAF in case HSE firmware feature flag is enabled in UTEST.
Below tables are applicable for standard firmware in which HSE Firmware size is 128 KB. For custom firmware, the secure size
depends on size of custom HSE Firmware. This table is provided for the information to the user that how much memory will be
used by security firmware in case security feature is enabled.
Table 163. Memory map when full memory HSE firmware is installed
Memory Section MCXE315/MCXE316 MCXE317 MCXE31B
Flash 1 MB 2 MB 4 MB
memory
IVT 0040_0000h, 0040_0000h, 0040_0000h,
Locations in Priority order 0048_0000h, 0050_0000h, 0050_0000h,
1000_0000h 1000_0000h 0060_0000h,
0070_0000h
1000_0000h
Secure BAF Boot 004F_FC00h 005F_FC00h 007F_FC00h
Location
Secure BAF code region 004F_4000h 005F4_000h to 007F_4000h
005F_FFFFh (48 KB)
to 004F_FFFFh to 007F_FFFFh
(48 KB) (48 KB)
Table continues on the next page...

---

*Page 790*

Boot Overview
Table 163. Memory map when full memory HSE firmware is installed (continued)
Memory Section MCXE315/MCXE316 MCXE317 MCXE31B
Flash 1 MB 2 MB 4 MB
memory
Full memory HSE 004D_4000h 005D_4000h to 007D_4000h
005F_3FFFh (128 KB)
Firmware to 004F_3FFFh (128 KB) to 007F_3FFFh (128 KB)
code area in last block
Application Flash area when Full 0040_0000h 0040_0000h 0040_0000h
memory HSE
To 004D_3FFFh (848 KB) to 005D_3FFFh (1824 KB) to 007D_3FFFh (3920 KB)
Firmware present
1000_0000h to 1000_0000h to 1001_5FFFh (88 KB)
Application Data Flash
1000_FFFFh (64 KB)
1B00_0200h to 1B00_06FFh (1280 Bytes)
Secure BAF UTEST Flash
Table 164. Memory map in AB swap configuration
Memory Section MCXE315/MCXE316 MCXE317 MCXE31B
Flash 1 MB 2 MB 4 MB
memory
IVT locations in 0040_0000h, 0040_0000h, 0040_0000h,
priority order
1000_0000h 1000_0000h 0050_0000h,
1000_0000h
HSE FW boot location 0047_FC00h 004F_FC00h 005F_FC00h
AB Swap HSE firmware 0045_4000h 004D_4000h 005D_4000h to
code area in active
to 0047_FFFFh (176 KB) to 004F_FFFFh (176 KB) 005F_FFFFh (176 KB)
1
block
AB Swap HSE 004D_4000h 005D_4000h 007D_4000h to
firmware code area in
to 004F_FFFFh (176 KB) to 005F_FFFFh (176 KB) 007F_FFFFh (176 KB)
1
passive block
0040_0000h to
Application Flash area 0040_0000h 0040_0000h to
0045_3FFFh (336 KB)
in active block
to 004D_3FFFh (848 KB) 005D_3FFFh (1872 KB)
00480_000h to
Application Flash area 0050_0000h 0060_0000h to
004_D3FFFh (336 KB)
in passive block
to 005D_3FFFh (848 KB) 007D_3FFFh (1872 KB)
1000_0000h to 1001_FFFFh (128 KB)
Application Data Flash 1000_0000h
to
Table continues on the next page...

---

*Page 791*

Boot Overview
Table 164. Memory map in AB swap configuration (continued)
Memory Section MCXE315/MCXE316 MCXE317 MCXE31B
Flash 1 MB 2 MB 4 MB
memory
1000_FFFFh (64 KB)
1B00_0200h to 1B00_06FFh (1280 Bytes)
Secure BAF UTEST
Flash
1. HSE firmware includes the Secure BAF in AB swap configuration

#### 31.3.2 Hardware protection

Platform flash of MCXE31 has facilitated hardware protection exclusive to HSE core. BAF releases hardware protection of that
area which is being used by Application as mentioned in previous section. If the hardware protection fails, for security purpose,
Secure BAF issues a reset.
Below table explains default values of Hardware protection.
Table 165. Hardware protection by Secure BAF
Register name Register address Secure memory Configured value
CONFIG_RAMPR 4039_C038h SRAM memory 4000_0000h
CONFIG_CFPRL 4039_C03Ch Code flash memory active block 4000_0000h
CONFIG_CFPRH 4039_C040h Code flash memory passive block 4000_C000h
CONFIG_DFPR 4039_C044h Data flash memory 4002_0000h

#### 31.4 Device common configuration

#### 31.4.1 Feature configuration in virgin device

Following table lists down the status of various features when device "LC = CUST_DEL". Many features are configurable directly
or indirectly by application software.
Feature Configuration information Status Configurability
OTA functionality Samples are always shipped in the OTA Disabled Yes
disabled configuration
Can be enabled by
application by requesting
to HSE firmware or
Secure BAF
HSE firmware usage This flag indicates whether firmware installation Disabled Yes
feature flag allowed in the device or not. By default, this flag
Table continues on the next page...

---

*Page 792*

Boot Overview
Table continued from the previous page...
Feature Configuration information Status Configurability
is unprogrammed and Secure BAF assumes that Can be enabled by
firmware installation is not allowed. programming in the
UTEST location. More
details on “UTEST Flag
description” section.
Secure BAF firmware Samples are always shipped with Secure Programmed No
BAF programmed
HSE Firmware HSE firmware is always programmed by user/ Not programmed Yes
Encrypted and signed firmware image is always
Can be installed by the
delivered to user. application software.
Image Vector Table No IVT is programmed at any of the IVT locations. Not programmed Yes
Can be programmed by
application software.
SWT0 Application core watchdog SWT0 is disabled. Disabled Yes
It can be optionally
enabled by BAF if SWT
bit is configured in boot
configuration word.
Boot Sequence Boot sequence is non-secure boot Non secure boot Yes
i.e. application are booted by Secure BAF without It can be changed
any authentication. to secure boot
by programming the
BOOT_SEQ bit in IVT.
Life Cycle Samples are always delivered in LC=CUST_DEL Customer Delivery Yes
It can be changed
to OEM_PROD and
IN_FIELD by application
through Secure BAF and
HSE firmware.
Application Core All applications cores are in disable state. Disabled. Yes
enablement status
Single or all application
cores can be enabled
at required address by
programming the required
fields in the IVT.
Clock Source Fast Internal Reference clock FIRC Yes
Table continues on the next page...

---

*Page 793*

Boot Overview
Table continued from the previous page...
Feature Configuration information Status Configurability
FIRC Frequency Value 48 MHz 48 MHz Yes, application can
configures after Secure
BAF goes in WFI.
Application debug Debug Authorization mode of application cores is Password When HSE Firmware
authorization mode password based. It can be changed to challenge based approach Feature flag is disabled,
response mode by programming the configuration debug authorization mode
in UTEST. cannot be changed.
When HSE Firmware
Feature flag is enabled,
debug authorization mode
can be changed to
challenge response mode
by requesting to HSE
firmware.
Application core Debug of application cores is enabled in customer Enabled No
debug status delivery lifecycle.

#### 31.4.2 Secure BAF memory map description

Following table explains the memory sections used by the Secure BAF.
Table 166. Secure BAF memory
Memory Section Remarks
IVT location This is the base address of IVT. The size of IVT is always 256 byte starting from base address.
IVT at one location must be programmed by the application.
Secure BAF boot location Location where interrupt vector table of HSE core is present. Secure BAF on HSE CPU boots
from this location. From this address 1024 bytes of interrupt vector table is present.
This location is protected by XRDC and it can be accessed only by HSE core.
Secure BAF code region Secure BAF code area in flash. This area is protected by XRDC and it can be accessed only by
HSE core.
UTEST flash location 1280 Kbytes of UTEST area of flash.

#### 31.4.3 UTEST memory usage by Secure BAF

Table 167. UTEST memory location usage by Secure BAF
Start address End address Size (bytes) Description Programmed by Write protected
1B00_0000h 1B00_0007h 8 HSE Firmware Application No
Feature Usage
Table continues on the next page...

---

*Page 794*

Boot Overview
Table 167. UTEST memory location usage by Secure BAF (continued)
Start address End address Size (bytes) Description Programmed by Write protected
flag. For more
detail, see UTEST
flag description
1B00_0040h 1B00_0047h 8 Unique Chip NXP No
Identifier (UID) 0
1B00_0050h 1B00_0057h 8 FXOSC Application No
enablement flag
For more
detail, UTEST
flag description
1B00_0080h 1B00_009Fh 32 Debug Application No
password(CUST_D
B_PSWD_A).
When HSE FW
feature Flag is
disabled, this
location is used
by Secure BAF
to run the debug
authorization
feature. Secure
BAF copies this
value in application
expected response
register and this
value is used to
derive the HSE
expected response
register. Size of
this register is 16B
and 1B00_0090h
– 1B00_009h
is reserved.
When HSE
Firmware feature
flag is enabled,
password is
programmed by
HSE firmware at
different location.
Password is
scanned by DCM
during Reset only.
Table continues on the next page...

---

*Page 795*

Boot Overview
Table 167. UTEST memory location usage by Secure BAF (continued)
Start address End address Size (bytes) Description Programmed by Write protected
Password to be
retained in standby.
1B00_0220h 1B00_022Fh 16 Lifecycle slot NXP Yes
1: CUST_DEL
1B00_0230h 1B00_023Fh 16 Lifecycle slot Secure BAF Yes
2: OEM_PROD
1B00_0240h 1B00_024Fh 16 Lifecycle slot Secure BAF Yes
3: IN_FEILD
1B00_0250h 1B00_025Fh 16 Lifecycle slot Secure BAF Yes
4: Pre-FA
1B00_0260h 1B00_026Fh 16 Lifecycle slot 5: FA Secure BAF Yes
Detail description of each field is out of scope of this document. For details, see MCXE31_DCF_clients file attached with
this document.

#### 31.4.4 UTEST flag description

31.4.4.1 HSE firmware usage feature flag
This flag is an indication to Secure BAF that application intend to use HSE firmware on the device. By default, this flag
is unprogrammed and Secure BAF assumes that HSE firmware installation is not allowed in the secure samples. But, if
application decides to allow installation of HSE firmware, it can program the HSE Firmware Usage Feature flag in the UTEST at
location 0x1B000000.
Description of the flag as follows:
Table 168. HSE firmware usage feature flag description
Field type Description Remarks
Size 64 bits
Default value 0xFFFFFFFFFFFFFFFF
Default status Disabled Secure BAF does not allows installation
of HSE firmware.
UTEST location 0x1B000000
Programmed by Application software
LC in which programming is allowed All LC
Value to program to to enable HSE Any value other than default value Secure BAF allows installation of
FW installation HSE firmware.
Implication when this flag is not programmed by application

---

*Page 796*

Boot Overview
• Secure BAF never try to install the HSE firmware.
• Secure BAF does not protect the HSE firmware code flash area.
• The logic of debug authorization protocol is different. See Debug authorization for more details.
• Secure BAF does not protect the data flash area reserved for HSE firmware.
• Debug authorization always be password based for application cores.
• Secure BAF always boot the application without any authentication. BOOT_SEQ bit in the boot configuration word is ignored.
• Secure BAF does not authenticate the IVT and XRDC configuration table in all LC.
• Application password area in UTEST area is not read and write protected by XRDC in LC = CUST_DEL. In other LC, it is
accessible only to HSE core.
• Number of region descriptors in MRC0 and MRC1 increased which can be configured by the application.
• Default configuration of XRDC and valid address range changed. For more details see XRDC configuration .
31.4.4.2 Crystal oscillator configuration flag
Description of the flag as follows:
Field type Description
Size 64 bits
Default Value FFFF_FFFF_FFFF_FFFFh
Default Status Boot via FIRC
UTEST Location 1B00_0050h
Programmed by Application
LC in which programming is allowed All LC
Table 169. Crystal oscillator configuration flag in UTEST
63 62 61 60 59 58 57 56 55 54 53 52 51 50 49 48
R FXOSC_ENABLE_MAGIC_NUMBER
W
47 46 45 44 43 42 41 40 39 38 37 36 35 34 33 32
R
W
31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R GMSEL EOCV
Table continues on the next page...

---

*Page 797*

Boot Overview
Table continued from the previous page...
W
15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
R CRYSTAL_OSCILLATOR_FREQUENCY
W
Table 170. Crystal oscillator configuration bit definition
Field Description
63-33 FXOSC_ENABLE_MAGIC_NUMBER
AAAA_5555h – Enable
FFFF_FFFFh – Disable
31 - 28 GMSEL (Crystal overdrive protection):
For invalid values = 0x0 or 0xF, default value 0xC is used.
27 - 20 EOCV (End of count value):
For invalid value = 0x0, default value 9Dh is used.
19 - 16 Reserved
15 - 0 CRYSTAL_OSCILLATOR_FREQUENCY:
Crystal frequency in KHz. Valid Crystal frequency range is 8000 – 40000 KHz. BAF does not checks for invalid
frequencies. This field is only necessary if HSE Firmware installation is required.

#### 31.5 Image Vector Table

The following section describes the various fields in Image Vector Table that is to be programmed by the application. IVT is
scanned by Secure BAF after the device is out of reset. The structure is 256 bytes in size. This structure contains application start
address, XRDC configuration etc. It must be programmed at least at one of the locations as mentioned in Chip Configuration
After reset, Secure BAF searches for the first valid IVT starting from the lowest address. If there are multiple valid IVT at IVT
locations at the same time, the lowest address wins the priority.
Table 171. Image Vector Table
Address Size in Content Comments
offset bytes
00h 4 Image Vector It is a magic number which marks the starting of Image Vector Table location.
Table Marker Its value must be 5AA5_5AA5h
04h 4 Boot Configuration word that allows the user to select the various configuration in
Configuration Word which device can be booted. Details are mentioned in the next section.
Table continues on the next page...

---

*Page 798*

Boot Overview
Table 171. Image Vector Table (continued)
Address Size in Content Comments
offset bytes
08h 4 Reserved
0Ch 4 CM7_0 Boot address of CM7_0 application in code flash area. It must honor
core VTOR register alignment restrictions. This field is used by BAF when
application
BOOT_SEQ bit is 0.
Start Address
10h 4 Reserved
14h 4 CM7_1 Boot address of CM7_1 application in code flash area. It must honor core
VTOR register alignment restrictions. This field is used by BAF only when
application
BOOT_SEQ bit is 0.
Start Address
This filed is ignored if Lockstep configuration is enabled.
Reserved for MCXE315/MCXE316/MCXE317/MCXE31B
18h 4 Reserved
1Ch 4 CM7_2 Boot address of CM7_2 application in code flash area. It must honor core
VTOR register alignment restrictions. This field is used by BAF only when
application
BOOT_SEQ bit is 0
Start Address
Reserved for MCXE315/MCXE316/MCXE317/MCXE31B.
20h 4 Reserved
24h 4 Address of Address of Configuration word that allows User to Advance LC. Details are
LC Configuration mentioned below.
28h 216 Reserved

#### 31.5.1 Boot configuration word

This register informs Secure BAF to allow booting of selected applications.
Table 172. Boot configuration register
31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16
R
W
15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
Table continues on the next page...

---

*Page 799*

Boot Overview
Table 172. Boot configuration register (continued)
R
RESERVED BOOT_SEQ
APP_SWT_INIT
CM7_2_ENABLE CM7_1_ENABLE CM7_0_ENABLE
W
Table 173. Boot configuration register bit definition
Field Description
31 – 6 Reserved
5 APP_SWT_INIT: Control SWT0 enablement before control is passed to application core(s).
0 Disable
1 Enable
BAF initializes the SWT0 before enabling the application cores. BAF scans this bit only when BOOT_SEQ bit is 0.
4 RESERVED
3 BOOT_SEQ
Control the boot flow of the application.
0 Non-Secure Boot- Application image without any authentication is started by Secure BAF in parallel to HSE
firmware.
1 Secure Boot- Application image is executed by HSE firmware after the authentication. Secure BAF only starts the
HSE firmware after successful authentication.
(Only valid for HSE Firmware feature flag enabled)
2 CM7_2_ENABLE
0 Cortex-M7_2 application core clock gated after boot
1 Cortex-M7_2 application core clock un-gated after boot
Reserved for MCXE315/MCXE316/MCXE317/MCXE31B.
1 CM7_1_ENABLE
0 Cortex-M7_1 application core clock gated after boot
1 Cortex-M7_1 application core clock un-gated after boot
This field is ignored if Lockstep configuration is enabled.
0 CM7_0_ENABLE
0 Cortex-M7_0 application core clock gated after boot
1 Cortex-M7_0 application core clock un-gated after boot

---

*Page 800*

Boot Overview

#### 31.5.2 Address LC configuration word

This field allows User to Advance LC. To advance lifecycle User must program a valid 32 bits wide value at an address which
must be given in IVT at address offset 24h. This address must be 4 bytes aligned and should not lie in Secure BAF/HSE FW
protected areas.
Valid values for advancement in next lifecycles are mentioned in below table. For all other values at given address lifecycle
advancement is not allowed.
Table 174. Valid values for lifecycle advancement
Lifecyle Valid values for lifecycle advancement
OEM_PROD DADA_DADAh
IN_FIELD BABA_BABAh
Application password on the location depending on HSE FW Feature flag must be program before lifecycle advancement
otherwise lifecycle advancement doesn’t attempted by Secure BAF.
The SoC provides a LC mechanism for an irreversible progression of restrictions to accessing security related content on the
device. The device LC cannot be reversed, so it is only possible to mature the device. Secure BAF takes on the responsibility of
advancing the device through the LC:
• CUST_DEL --> OEM_PROD
• CUST_DEL and OEM_PROD --> IN_FIELD
Advancing the LC through Secure BAF involves configuring the LC configuration word in the IVT. Upon successful advancement,
Secure BAF issues a Destructive reset. LC advancement is ignored if the device is found in same LC as indicated by the IVT.

#### 31.5.3 Structure definition of Image Vector Table

Following structure can be used by application to configure the IVT configuration.
NOTE
The fields which are not valid for the specific configuration must be kept reserved. For Secure-Boot, see HSE
Firmware Reference Manual.
typedef const struct image_vector_table
{
uint32_t Header; /*Header of IVT Structure */
uint32_t BootConfig; /*Boot Configuration Word */
const uint32_t Reserved1; /* Reserved */
const uint32_t * CM7_0_StartAddress; /*Start Address of Application on CM7_0 Core */
const uint32_t Reserved2; /* Reserved */
const uint32_t * CM7_1_StartAddress; /*Start Address of Application on CM7_1 Core */
const uint32_t Reserved3; /* Reserved */
const uint32_t * CM7_2_StartAddress; /*Start Address of Application on CM7_1 Core */
const uint32_t * Reserved4; /* Reserved */
const uint32_t * LCConfig; /*Address of LC configuration Word */
uint8_t Reserved5[216];
}ivt_t;

#### 31.5.4 Invalid IVT scenarios

This section lists down the scenarios in which IVT is considered as invalid by Secure BAF.

---

*Page 801*

Boot Overview
1. IVT marker is not present.
2. ECC error is present in any of the 256 bytes of IVT memory.

#### 31.6 Boot flow

Below diagram explains boot sequence flow of Secure BAF.
START Reset Exit System Initialization
Standby Exit
Load and verify the
Normal Boot
IVT
Standby Boot
Valid IVT Valid? Invalid
Execute Recovery
Boot the Application Failure
Mode Sequence
Execute Debug
Shutdown
Success Authorization
Secure BAF
Sequence
Figure 127. Boot flow

#### 31.7 Standby boot

Secure BAF supports boot from Standby exit. SOC register DCMRWF5 (address 0x402AC610) provided to support boot on
standby exit. This register gets clear on POR. For more details, see chapter Device Configuration Module (DCM) in MCXE31
Reference Manual.
Application is supposed to program this register before entering the standby mode.
There are two types of boot mode on exit from standby.
• Fast standby mode
• Normal boot on exit from standby
In Fast standby mode Secure BAF boots CM7_0 and halts the HSE core. The flow of standby boot is explained below:

---

*Page 802*

Boot Overview
Standby Boot Flow Normal Boot Flow
START
MC_ME_MODE_
Status[PreviousMode] RESET EXIT
STANDBY EXIT
DCMRWF5[0] for StandBy Boot NORMAL STANDBY EXIT
Configuration
FAST STANDBY EXIT
Release FIRC Divider for Continue with Secure BAF
Application Core boot Flow
Read App Core reset register
DCMRWF5[31:1]
MC_ME_CADDR_CM7_0 = Core
Reset Address for standby Exit
Enable CM7_0
Shutdown
Secure BAF
Figure 128. Standby boot flow

#### 31.8 Reduced clock mode configuration

In case user wants to use clocking option B, application can enable this mode by setting “dcf_client_utest_misc” DCF record. For
more details on DCF record, see the DCF sheet available with the MCXE31 Reference Manual.
After reset, Secure BAF checks for the HSE_CLK_MODE_OPTION field in DCMROF21 register in DCM. If this field is configured
for clocking option B, Secure BAF configures the following dividers in MC CGM
• MUX_0_DC_1
• MUX_0_DC_2
Below figure explains steps for reduced clock mode configuration steps by Secure BAF.

---

*Page 803*

Boot Overview
Start
YES NO
HSE_CLK_MODE_OPTION =
2b10
Configure
MC_CGM_MUX_0_DC_1[DIV]
for AIPS_PLAT_CLK as 2
Configure
STATUS =
MC_CGM_MUX_0_DC_2[DIV]
CONFIGURATION_NOT_REQUIRED
for AIPS_SLOW_CLK as 4
STATUS =
CONFIGURATION_SUCCESS
return STATUS
End
Figure 129. Reduced clock mode configuration

#### 31.9 Debug authorization

If HSE firmware feature flag is disabled then application core debug is always password based. CUST_DB_PSWD_A must be
programmed at location 1B00_0080h. See UTEST memory usage by Secure BAF more details.

#### 31.10 FIRC divider register control

FIRC divider is controlled by Configuration HSE GPR Register (CONFIG_REG_GPR) address 0x4039C064. This HSE GPR
register for FIRC divider is write protected by default and application can’t modify it.
After Secure BAF executes WFI, it provides write access to FIRC divider field of this HSE GPR register and application
can configure this register. Before access to this register, application should wait for Secure BAF to go in WFI by reading
register PRTN0_CORE2_STAT.

#### 31.11 Application boot

Secure-BAF is not responsible for booting application image in a secure way. The responsibility of booting application image in a
secure way lies with HSE firmware. In case secure boot is not requested (by setting BOOT_SEQ bit in Boot configuration word in
IVT structure to 0), the application image is always loaded by Secure-BAF without authentication in all LC of the device. By default,
Secure-BAF releases the reset of every core which is configured to be enabled.
Following flow chart explains the steps of application boot performed by Secure-BAF when boot sequence field in boot
configuration word is configured to 0.

---

*Page 804*

Boot Overview
Start
Extract the core enable bit mask
from Boot Configuration field
YES At least one NO
bit mask is set?
Extract the Start Address of Return Application Boot
Application Core(s) from Status as FAIL
Application IVT
Enable Application Core(s) End
Is SWT bit set in Boot YES Return Application Boot
Enable the SWT0 Watchdog
Configuration Word Status as SUCCESS
NO
Trigger Mode Transition
Figure 130. Application boot sequence
Before configuring HSE_CLK, application should wait for Secure BAF to go in WFI by reading register PRTN0_CORE2_STAT.

#### 31.12 Recovery mode sequence

This feature is provided to allow the user to program the application image in LC = MCU_PROD and LC= CUST_DEL and debug
the reason of corruption of IVT and re-program the IVT in other LC.
Below are the scenarios when Secure BAF executes recovery mode sequence.
• Valid IVT is not found.
• Application is not booted by Secure BAF.
• Application enablement bit is not programmed in Boot configuration word in IVT.
• Application issued more than eight functional reset and disable recovery mode on functional reset bit is not disabled in
DCMRWP1 register.
• Application issued more than eight destructive reset and disable recovery mode on destructive reset bit is not disabled in
DCMRWP1 register.

---

*Page 805*

Boot Overview
Start
YES Is Lifecycle = MCU_PROD or NO
Lifecycle = CUST_DEL?
This bit determine
whether debugger is
Is Functional Reset
attached to the
Counter ≥ 8?
system or not.
Only when debugger is
attached, application
cores will be enabled Is EDB bit in HSE_GPR set? YES
NO
at default address This bit determine
whether successful
Start the timer
YES debug authorization
NO sequence has
been completed
and JTAG is
enabled on the
YES Is APPDBG_STAT_SOC
application core
Disable the STM timer
bit set in DCMDEB?
NO
NO
Copy Infinite Loop 30 seconds Timeout?
Code to SRAM
YES
Enable the Application Return Recovery Status as
Core(s) at SRAM address FAIL
Return Recovery Status as
End
SUCCESS
Figure 131. Recovery mode sequence
The infinite loop with WFI code is copied to SRAM1 i.e. 2040_0100h and size of code is 16B. To prevent prefetching errors in
application cores, the Infinite loop instruction is placed at 2040_012Ch.

#### 31.13 XRDC configuration

Secure BAF needs to ensure that is has access to its own resources and application don’t have access to secure area. The
following sections describe the XRDC default configuration values when HSE Firmware feature flag is disabled. In this scenario,
application don’t required to provide XRDC configuration via IVT and Secure BAF never enables the XRDC at any time.
When HSE Firmware Feature flag is enabled, Application can provide XRDC configuration via IVT. For this scenario, see
ELE_HSEB Firmware reference manual for the details of XRDC configuration.

#### 31.13.1 XRDC configuration of MDAC

Following table list down the default configuration of MDAC by Secure BAF.
Table 175. MDAC registers default value
Device variant MDAC Register HSE CPU Value Domain number assigned to
HSE core
MCXE315/MCXE316/ MDAC3 C000_0001h 1
MCXE317/MCXE31B

---

*Page 806*

Boot Overview
All the other MDAC domain numbers are 0 and not locked by Secure-BAF.

#### 31.13.2 XRDC configuration of MRC

Following table list down the default configuration of MRC by Secure BAF.
Table 176. Default Configuration of MRC
MRC Region Name Remarks Configuration done by Secure BAF
Descriptor
Number
Number
0 14 HSE UTEST UTEST area that is accessible by W0- Start address W1= End
HSE core only. Address W2={ SE=0,SNUM=0,
area
See chip configuration chapter HSE domain =0x7[All permission]
for Start address and
End address.
0 15 Secure BAF Code area for Secure BAF. See W0- Start address
Code area chip configuration chapter for
W1= End Address
Start address and End address.
W2={ SE=0, SNUM=0,
HSE domain =0x7[All permission]
Other domains = 0x00 [No permission]
}
W3={VLD=1, LK2=0x3}

#### 31.14 BAF flash programming controls

Platform flash controller generates an exception of Read-while-Write if Read and Write are performed on the same block
simultaneously. HSE core can also perform the Program and Erase for application, and the status is indicated at Configuration
PE Lock Register (CONFIG_PE_LOCK) in the HSE space.
BAF locks the high addressing code flash block during its execution and it's cleared when HSE core enters into WFI. Address of
high addressing code flash area should be referred to chip configuration chapter.
Before Programming/Erasing/Executing from this address space, application core is requested to poll for WFI bit of
PRTN0_CORE2_STAT register to ensure HSE core is in WFI state.
For Boot Sequence 1 or flash synchronization with HSE Firmware, see HSE Firmware reference manual.
Below table explains PE lock bits setting in CONFIG_PE_LOCK register of HSE GPR during Secure BAF execution.
Table 177. PE lock bits setting in CONFIG_PE_LOCK register of HSE GPR
Chip UTEST Data Flash Block Code Flash Code Flash Code Flash Code Flash
variants Block 3 Block 2 Block 1 Block 0
Block
MCXE315/ 1 0 0 0 0 1
MCXE316
MCXE317 1 0 0 0 1 0
MCXE31B 1 0 1 0 0 0

---

*Page 807*

Boot Overview

#### 31.15 Status registers for application usage

This section explain various status register configuration to provide status information to application.

#### 31.15.1 Secure BAF version information

Secure BAF version is a 64-bit field. Secure BAF version can be read by application from HSE GPR register 4039_C020h. The
version information is explained below.
Table 178. Secure BAF version information
Bit number Field Name Description
0 – 7 RESERVED Reserved, must be 0
8 - 15 SOC_TYPE_ID This field Identifies the SoC family
0 – not used
5 – ELE_HSEB MCXE31B
12 - ELE_HSEB MCXE315/MCXE316
13 - ELE_HSEB MCXE317
16 - 31 FW_TYPE This field identify the FW type:
0 – Standard generic FW targeting all customers
1-7 – Reserved
8 >= Custom1, Custom2 (For example: Custom1 = customer X’s project A, Custom2 =
customer Y’s project B)
32 - 39 MAJOR_REV Major version number
0 - EAR/BETA
1 - at first RTM and incremented by 1 for subsequent RTM
40 - 47 MINOR_REV 4 – Pre-EAR
8 - EAR
9 - BETA
This field is reset for every new MAJOR_REV change.
48 - 63 PATCH_REV bug fix releases (After BETA) or new feature for EAR/PRE-EAR releases. (least significant
8 bits are reserved for hotfix / frozen branch info)

#### 31.15.2 DCM Register DCMRWP1

Application can disable recovery mode entry by Secure BAF after programming bits 23 and 22 of DCM register DCMRWP1. This
register gets clears on power on reset.
Table 179. DCM register DCMRWP1 availalble at address 402A_C400h
Bits Number R/W access Description
of bits by application
24-31 8 Reserved
Table continues on the next page...

---

*Page 808*

Boot Overview
Table 179. DCM register DCMRWP1 availalble at address 402A_C400h (continued)
Bits Number R/W access Description
of bits by application
23 1 R/W Disable recovery mode on destructive reset.
This bit is reset by default and Secure BAF allows recovery mode sequence if
Application issues >8 destructive reset. Application can set this bit to disable
recovery mode when Application issues > 8
destructive reset. [1]
22 1 R/W Disable recovery mode on functional reset.
This bit is reset by default and Secure BAF allows recovery mode sequence
if Application issues >8 functional reset. Application can set this bit to disable
recovery mode when Application issues > 8
functional reset. [1]
21 1 R Reserved
16-20 5 R Recovery mode Reset counter. For OEM_PROD and INFIELD lifecycle, to enable
recovery mode functionality, Secure BAF increments this counter when Functional
or destructive reset is issued.
15 1 R Reserved
11-14 4 R Destructive reset counter. Secure BAF increments this counter when Destructive
reset is issued.
0-10 11 R Reserved
[1] Application core should not modify other bits of this register while updating these bits.

#### 31.15.3 Status bits on HSE GPR register

HSE GPR register 4039_C028h is configured by Secure BAF to show various status information as explained in below table.
Table 180. Status bits on HSE GPR register available at 4039_C028h
Bit Description
0 HSE FW is present and booted by Secure BAF.
1-4 Reserved
5 Application cores booted in Recovery mode by SBAF.
6 Debug Authentication performed by SBAF.
7-31 Reserved.

#### 31.16 Interrupt and exception handling

---

*Page 809*

Boot Overview

#### 31.16.1 Interrupt handling

No special interrupt handling routines are required during the boot process. Interrupts are disabled during Secure BAF execution.

#### 31.16.2 Exception handling

Secure BAF enters the recovery mode after Enabling Debug Authorization.
After eight consecutive functional resets or destructive resets either from Application Firmware, the Device Enters the
Recovery Mode.

#### 31.16.3 Boot target watchdog

Secure BAF conditionally configures SWT0 watchdog with default timeout i.e. 25 ms, before enabling the application core(s). It
is expected that the application services this watchdog before expiration.

#### 31.17 Hardware modules used by Secure BAF

#### 31.17.1 MC_ME

Secure BAF uses MC_ME to enable the Application cores, mode switch, and other operations during its execution. MC_ME
operations supported by Secure BAF are discussed in below section.

#### 31.17.2 FXOSC

Secure BAF configures FXOSC if FXOSC enablement flag is programmed in UTEST.

#### 31.17.3 Clock Generation Module (MC_CGM)

Secure BAF configures MC_CGM in Reduced Clock Mode as well. See following section for the same.

#### 31.17.4 DCM

Secure BAF uses the DCM to identify the Life-Cycle, Standby Boot Configuration, Lockstep, Reduced Clock Mode Configuration.

#### 31.17.5 HSE_GPR

Secure BAF configures Hardware Protection, Program Erase Lock, FIRC Divider Control, Secure BAF Version number, HSE FW
Handshaking via HSE-GPR.

#### 31.17.6 XRDC

Secure BAF configures the XRDC Configuration. See XRDC Configuration for the same.

#### 31.17.7 Flash module

Secure BAF always perform the write and erase operation on alternate interface.
See Chip Configuration for details of Flash memory usage by Secure BAF.

#### 31.18 Hardware IP registers details modified by Secure BAF

Below table explains the registers details which are modified by Secure BAF when HSE Firmware Feature Flag is disabled.

---

*Page 810*

Boot Overview
Table 181. Hardware IP registers modified by Secure BAF
IP Register Name Default value (hex) Modified value (hex)
Master Domain Assignment (MDA_W0_3_DFMT0) 0000_0000h C000_0002h
C000_0001h
1
Memory Region Descriptor (MRGD_W0_14) 0000_0001h HSE Utest Start
1
Memory Region Descriptor (MRGD_W1_14) 0000_001Fh HSE Utest End
Memory Region Descriptor (MRGD_W2_14) 0000_0000h 0000_01C0h
Memory Region Descriptor (MRGD_W3_14) 0000_0000h E000_0000h
1
Memory Region Descriptor (MRGD_W0_15) 0000_0001h HSE Code Flash Start
1
Memory Region Descriptor (MRGD_W1_15) 0000_001Fh HSE code Flash End
Memory Region Descriptor (MRGD_W2_15) 0000_0000h 0000_01C0h
Memory Region Descriptor (MRGD_W3_15) 0000_0000h E000_0000h
Peripheral Domain Access Control (PDAC_W0_155) 0000_0000h 0000_01C0h
Peripheral Domain Access Control (PDAC_W1_155) 0000_0000h E000_0000h
Peripheral Domain Access Control (PDAC_W0_188) 0000_0000h 0000_01C0h
Peripheral Domain Access Control (PDAC_W1_188) 0000_0000h E000_0000h
Peripheral Domain Access Control (PDAC_W0_231) 0000_0000h 0000_01FFh
Peripheral Domain Access Control (PDAC_W1_231) 0000_0000h E000_0000h
1
General Configuration Program and Erase 0000_0000h Protect only HSE Area
purpose Lock (CONFIG_PE_LOCK)
registers with
1
write access by Configuration RAM Protected See chapter Release Full memory
HSE core Region (CONFIG_RAMPR) EdgeLock Secure
Enclave (HSE_B)
1
Configuration RAM Protected Region See chapter Release Full memory
Alternate (CONFIG_RAMPR_ALT) EdgeLock Secure
Enclave (HSE_B)
Configuration Code Flash Memory Active See chapter Protect only HSE Code Flash
1
Block (CONFIG_CFPRL) EdgeLock Secure Area
Enclave (HSE_B)
Configuration Code Flash Memory Active Block See chapter Protect only HSE Code Flash
1
Alternate (CONFIG_CFPRL_ALT) EdgeLock Secure Area
Enclave (HSE_B)
Table continues on the next page...

---

*Page 811*

Boot Overview
Table 181. Hardware IP registers modified by Secure BAF (continued)
IP Register Name Default value (hex) Modified value (hex)
Configuration Code Flash Memory Passive See chapter Protect only HSE Code Flash
1
Block (CONFIG_CFPRH) EdgeLock Secure Area
Enclave (HSE_B)
Configuration Code Flash Memory Passive Block See chapter Protect only HSE Code Flash
1
Alternate (CONFIG_CFPRH_ALT) EdgeLock Secure Area
Enclave (HSE_B)
Configuration Data Flash Memory Protected Region See chapter Protect only HSE Data Flash
1
EdgeLock Secure Area
(CONFIG_DFPR)
Enclave (HSE_B)
Configuration Data Flash Memory Protected Region See chapter Protect only HSE Data Flash
1
Alternate (CONFIG_DFPR_ALT) EdgeLock Secure Area
Enclave (HSE_B)
Configuration REG_GPR (CONFIG_REG_GPR) A000_0003h Refer chapter ”FIRC Divider
Register Control“.
MC_ME Partition 1 COFB Set 1 Clock Status 1CFE_2FFCh Clock of FXOSC and
Register (PRTN1_COFB1_STAT) MC_CGM are enabled by
default. But for 120 MHz clock
request, Secure BAF ensures
if MC_CGM clock is enabled.
Partition 0 Core 0 Address 0040_0000h Secure BAF update this
Register (PRTN0_CORE0_ADDR) address if App core 0 boot
is requested during Normal
boot sequence in IVT. Or in
recovery mode.
Partition 0 Core 0 Process Configuration 0000_0000h 0000_0001h
Register (PRTN0_CORE0_PCONF)
Partition 0 Core 0 Process Update 0000_0000h 0000_0001h
Register (PRTN0_CORE0_PUPD)
Partition 0 Core 1 Process Configuration 0000_0000h 0000_0001h
Register (PRTN0_CORE1_PCONF)
Partition 0 Core 1 Process Update 0000_0000h 00000001h
Register (PRTN0_CORE1_PUPD)
Partition 0 Core 1 Address 0041_0000h Secure BAF update this
Register (PRTN0_CORE1_ADDR) address if App core 1 boot
is requested during Normal
boot sequence in IVT. Or in
recovery mode.
Table continues on the next page...

---

*Page 812*

Boot Overview
Table 181. Hardware IP registers modified by Secure BAF (continued)
IP Register Name Default value (hex) Modified value (hex)
Partition 0 Core 2 Process Configuration 0000_0000h 0000_0001h
Register (PRTN0_CORE2_PCONF)
Partition 0 Core 2 Process Update 0000_0000h 0000_0001h
Register (PRTN0_CORE2_PUPD)
Partition 0 Core 2 Address See Mode Secure BAF update this
Register (PRTN0_CORE2_ADDR) Entry Module address if App core 2 boot
(MC_ME) chapter is requested during Normal
boot sequence in IVT. Or in
recovery mode.
Control Key Register (CTL_KEY) 0000_5AF0h Secure BAF update this
register if anyone of
application core is booted
by Secure BAF. 0000_5AF0h
and then 0000_A50Fh
FXOSC FXOSC Control Register (CTRL) 019D_00C0h See Crystal oscillator
configuration flag
MC_RGM Functional Reset Escalation Counter Register (FREC) 0000_0000h 0000_0000h
Destructive Reset Escalation Threshold 0000_0000h 0000_000Fh
Register (DRET)
SWT_0 Control (CR) FF00_010Ah FF00_000Bh
Interrupt (IR) 0000_0000h 0000_0001h
Service (SR) 0000_0000h C520h and then D928h
1. See Chip Configuration for details of HSE area addressing.

#### 31.19 Appendix

Secure BAF takes into account for following scenarios to prevent bricking of device :
After Maximum Functional Resets are observed in the device, BAF enters into Recovery Mode, to recover application core’s
failing status.
BAF does not allows the LC to be advanced to OEM_PROD or IN_FIELD, if CUST_DBG_PSWD/ADKP is not programmed by
the Application.
BAF boots the Application from System-RAM during recovery to avoid unpredictable behavior.

#### 31.20 Glossary

AES Advanced Encryption Standard; a cipher primitive
BAF Boot Assist Flash
CUST_DEL Device Life Cycle customer delivery

---

*Page 813*

Boot Overview
IN_FIELD Device Life Cycle is IN_FIELD
LC Device Life Cycle; used to limit by design the configuration and debug/test possibilities of the device for in-field
usage
MCU_PROD Device Life Cycle MCU Production
OEM_PROD Device Life Cycle OEM Production
OTA Over The Air
Secure BAF Secure Boot Assist Flash

---

*Page 814*

