# MCXE315MLF (LQFP48) - SAFETY MCU 1 - MCUXpresso Pins Tool Configuration

**Columns:** Route = Peripheral/Signal to select in "Routed pin/signal"  
**IBE** = Input buffer enable | **OBE** = Output buffer enable | **n/a** = not applicable  
**Note:** Verify polarity items marked **(!)** against schematic **MODUL+ELEKTRONSKI 13_2603_02_01**

---

## SPI

| Pin | Pkg | Signal name | Route to | Dir | SafeMode | Pull | PullupEn | PadKeep | Invert | IBE | OBE | InitVal | Drive | Filter | Slew |
|---|---:|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| PTB0 | 26 | O_D_SPI0_CS_SMCU1 | LPSPI_0/PCS0 | Output | Disable | n/a | Disabled | Disabled | Don't invert | Disabled | Enabled | n/a | High | n/a | Fast |
| PTC8 | 28 | O_D_SPI0_SCK_SMCU1 | LPSPI_0/SCK | Output | Disable | n/a | Disabled | Disabled | Don't invert | Disabled | Enabled | n/a | High | n/a | Fast |
| PTC9 | 27 | I_D_SPI0_SIN_SMCU1 | LPSPI_0/SIN | Input | Disable | Pullup | Enabled | Disabled | Don't invert | Enabled | Disabled | n/a | n/a | n/a | n/a |
| PTD3 | 33 | O_D_SPI1_CS_SMCU1 | LPSPI_1/PCS | Output | Disable | n/a | Disabled | Disabled | Don't invert | Disabled | Enabled | n/a | High | n/a | Fast |
| PTA3 | 35 | O_D_SPI1_SCK_SMCU1 | LPSPI_1/SCK | Output | Disable | n/a | Disabled | Disabled | Don't invert | Disabled | Enabled | n/a | High | n/a | Fast |
| PTA2 | 36 | I_D_SPI1_SIN_SMCU1 | LPSPI_1/SIN | Input | Disable | Pullup | Enabled | Disabled | Don't invert | Enabled | Disabled | n/a | n/a | n/a | n/a |
| PTC15 | 21 | O_D_SPI2_CS_SMCU1 | LPSPI_2/PCS | Output | Disable | n/a | Disabled | Disabled | Don't invert | Disabled | Enabled | n/a | High | n/a | Fast |
| PTC14 | 22 | O_D_SPI2_SCK_SMCU1 | LPSPI_2/SCK | Output | Disable | n/a | Disabled | Disabled | Don't invert | Disabled | Enabled | n/a | High | n/a | Fast |
| PTB2 | 24 | I_D_SPI2_SIN_SMCU1 | LPSPI_2/SIN | Input | Disable | Pullup | Enabled | Disabled | Don't invert | Enabled | Disabled | n/a | n/a | n/a | n/a |
| PTB1 | 25 | I_D_SPI3_SIN (!) | LPSPI_3/SIN (!) | Input | Disable | Pullup | Enabled | Disabled | Don't invert | Enabled | Disabled | n/a | n/a | n/a | n/a |

**Notes**
- **(!)** Tool defaults **PTB1** to `LPI2C_0/SCLS` - change it.
- If `LPSPI_3/SIN` is not offered on **PTB1**, route as `SIUL2/gpio` Input and flag to HW.

---

## UART

| Pin | Pkg | Signal name | Route to | Dir | SafeMode | Pull | PullupEn | PadKeep | Invert | IBE | OBE | InitVal | Drive | Filter | Slew |
|---|---:|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| PTC7 | 39 | O_D_LPUART1_TX_SMCU1 | LPUART_1/TX | Output | Disable | n/a | Disabled | Disabled | Don't invert | Disabled | Enabled | n/a | Low | n/a | Slow |
| PTC6 | 40 | I_D_LPUART1_RX_SMCU1 | LPUART_1/RX | Input | Disable | Pullup | Enabled | Disabled | Don't invert | Enabled | Disabled | n/a | n/a | n/a | n/a |

---

## GPIO OUTPUTS
*Initial value is set BEFORE OBE enable - never glitches.*

| Pin | Pkg | Signal name | Route to | Dir | SafeMode | Pull | PullupEn | PadKeep | Invert | IBE | OBE | InitVal | Drive | Filter | Slew |
|---|---:|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| PTB4 | 15 | O_D_STO_DIAGA_SMCU1 | SIUL2/gpio | Output | Disable | n/a | Disabled | Disabled | Don't invert | Disabled* | Enabled | Low (!) | Low | n/a | Slow |
| PTB5 | 14 | O_D_STO1_CHP_SMCU1 | SIUL2/gpio | Output | Disable | n/a | Disabled | Disabled | Don't invert | Disabled | Enabled | Low (!) | Low | n/a | Slow |
| PTA1 | 37 | O_D_STO1_CHP_SMCU1 (!) | SIUL2/gpio | Output | Disable | n/a | Disabled | Disabled | Don't invert | Disabled | Enabled | Low (!) | Low | n/a | Slow |
| PTB13 | 32 | O_D_RST_SMCU2 | SIUL2/gpio | Output | Disable | n/a | Disabled | Disabled | Don't invert | Disabled | Enabled | High (!) | Low | n/a | Slow |
| PTC16 | 20 | O_D_SBC_DIAGA_SMCU1 | SIUL2/gpio | Output | Disable | n/a | Disabled | Disabled | Don't invert | Disabled | Enabled | Low (!) | Low | n/a | Slow |
| PTE8 | 13 | O_D_STO_FAIL_TRIG | SIUL2/gpio | Output | Enable (!) | n/a | Disabled | Disabled | Don't invert | Enabled* | Enabled | Safe lvl (!) | Low | n/a | Slow |

**Notes**
- `*` IBE=Enabled on safety outputs (**PTE8**, optionally **PTB13**) allows pad readback verification (RM 9.2.1) - recommended for SIL2.
- **(!)** **PTA1** duplicates name with **PTB5** - verify schematic.
- If CHP lines are PWM: route to `EMIOS_x/CHn`, `Drive=High`, `Slew=Fast`.
- **(!)** **PTB13 InitVal=High** assumes `SMCU2 reset` is active-low - verify.
- **(!)** **PTE8 SafeMode=Enable** per SIL2 safety concept - verify desired safe-mode pad state.

---

## GPIO INPUTS
*Fail flags & cross-monitors.*

| Pin | Pkg | Signal name | Route to | Dir | SafeMode | Pull | PullupEn | PadKeep | Invert | IBE | OBE | InitVal | Drive | Filter | Slew |
|---|---:|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| PTA0 | 38 | I_D_SBC_D_FAIL | SIUL2/gpio | Input | Disable | Pullup (!) | Enabled | Disabled | Don't invert | Enabled | Disabled | n/a | n/a | n/a | n/a |
| PTD15 | 11 | I_D_STO1_D_FAIL | SIUL2/gpio | Input | Disable | Pullup (!) | Enabled | Disabled | Don't invert | Enabled | Disabled | n/a | n/a | n/a | n/a |
| PTE9 | 12 | I_D_STO2_D_FAIL | SIUL2/gpio | Input | Disable | Pullup (!) | Enabled | Disabled | Don't invert | Enabled | Disabled | n/a | n/a | n/a | n/a |
| PTC1 | 19 | I_D_VM_RST_SMCU2 | SIUL2/gpio | Input | Disable | Pullup (!) | Enabled | Disabled | Don't invert | Enabled | Disabled | n/a | n/a | n/a | n/a |
| PTB3 | 23 | I_D_SPI2_SIN_SMCU2 | SIUL2/gpio | Input | Disable | n/a | Disabled | Disabled | Don't invert | Enabled | Disabled | n/a | n/a | n/a | n/a |
| PTD2 | 34 | I_D_SPI1_SIN_SMCU2 | SIUL2/gpio | Input | Disable | n/a | Disabled | Disabled | Don't invert | Enabled | Disabled | n/a | n/a | n/a | n/a |

**Notes**
- **(!)** Pullup assumes fail signals are active-low; if active-high with external pulldown, set `Pull=Pulldown` or `Disabled`.
- **PTB3/PTD2** are driven by SMCU2 lines - no pull, do not load the line.
- **NOTE:** **PTA0** has JTAG input muxed by default out of reset (`IMCR61`) - tool must reroute; check generated code.

---

## SGPIO INTER-MCU LINES
*Start as inputs; direction toggled at runtime by GPIO driver.*

| Pin | Pkg | Signal name | Route to | Dir | SafeMode | Pull | PullupEn | PadKeep | Invert | IBE | OBE | InitVal | Drive | Filter | Slew |
|---|---:|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| PTA11 | 43 | IO_SGPIO0_SMCU1 | SIUL2/gpio | Input | Disable | Pullup | Enabled | Disabled | Don't invert | Enabled | Disabled | n/a | n/a | n/a | n/a |
| PTA12 | 42 | IO_SGPIO1_SMCU1 | SIUL2/gpio | Input | Disable | Pullup | Enabled | Disabled | Don't invert | Enabled | Disabled | n/a | n/a | n/a | n/a |
| PTA13 | 41 | IO_SGPIO2_SMCU1 | SIUL2/gpio | Input | Disable | Pullup | Enabled | Disabled | Don't invert | Enabled | Disabled | n/a | n/a | n/a | n/a |
| PTD0 | 2 | IO_SGPIO3_SMCU1 | SIUL2/gpio | Input | Disable | Pullup | Enabled | Disabled | Don't invert | Enabled | Disabled | n/a | n/a | n/a | n/a |

---

## ANALOG

| Pin | Pkg | Signal name | Route to | Dir | SafeMode | Pull | PullupEn | PadKeep | Invert | IBE | OBE | InitVal | Drive | Filter | Slew |
|---|---:|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| PTD1 | 1 | I_A_HALL_SMCU1 | ADC_x/channel | n/a | Disable | n/a | Disabled | Disabled | Don't invert | Disabled | Disabled | n/a | n/a | n/a | n/a |

**Notes**
- All digital buffers OFF so the pad is purely analog.
- Pick the ADC channel the tool offers on **PTD1**.

---

## DO NOT ROUTE (leave at reset defaults)

| Pin | Pkg | Signal name | Route to / Note |
|---|---:|---|---|
| PTC4 | 46 | JTAG_TCLK_SMCU1 | leave unrouted - JTAG TCK (debugger) |
| PTC5 | 45 | JTAG_TDI_SMCU1 | leave unrouted - JTAG TDI (debugger) |
| PTA4 | 48 | JTAG_TMS_SMCU1 | leave unrouted - JTAG TMS/SWD_DIO (debugger) |
| PTA10 | 44 | JTAG_TDO_SMCU1 | leave unrouted - JTAG TDO/SWO (debugger) |
| PTA5 | 47 | O_D_RST_SMCU1 | leave unrouted - dedicated RESET pad (only pad with input filter, per RM ch.9) |
| PTA7 | 29 | PTA7_SMCU1 (TP35) | leave unrouted - test point |
| PTC2 | 17 | PTC2_SMCU1 (TP43) | leave unrouted - test point |
| PTC3 | 16 | PTC3_SMCU1 | leave unrouted - test point |
| PTD5 | 18 | PTD5_SMCU1 | leave unrouted - test point |

---

## OPEN ITEMS TO VERIFY

1. **PTB1**: confirm `LPSPI3_SIN` is a valid mux option; tool defaulted to `LPI2C_0/SCLS`, which is wrong.
2. Polarities / initial values: **PTB13** (`RST_SMCU2`), **PTE8** (`STO_FAIL_TRIG`), all `*_FAIL` inputs -> check schematic.
3. **PTB5 vs PTA1**: duplicate name `O_D_STO1_CHP_SMCU1` - two phases or schematic typo?
4. CHP outputs: if PWM (chopper), use `eMIOS` channel routing instead of GPIO (`Drive=High`, `Slew=Fast`).
5. **PTE8 Safe Mode Control** state must match SIL2 safety concept.
