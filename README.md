# Keysatron

**Keysatron** is an inline USB-to-USB hardware adapter designed to intercept, transform, and remap USB HID keyboard input in real-time. By sitting transparently between your physical keyboard and your computer, it allows hardware-level key translation—such as converting German ISO (QWERTZ) keystrokes into Colemak ISO layout—without requiring any software, drivers, or administrative privileges on the host operating system.

---

## Hardware Overview

Keysatron is built around the **STM32F405RGT6** ARM Cortex-M4 microcontroller, which features two independent USB OTG controllers:
1. **USB OTG FS (Full-Speed)**: Operates as **USB Host** on port `USB_IN` (J2) to read input reports from connected USB keyboards.
2. **USB OTG HS (with embedded Full-Speed PHY)**: Operates as **USB Device** on port `USB_PC` (J3) to emulate a USB HID keyboard to the host PC.

### Specifications

| Feature | Description |
| :--- | :--- |
| **Microcontroller** | STM32F405RGT6 (ARM Cortex-M4 @ 168 MHz, 1 MB Flash, 192 KB SRAM) |
| **Input Port (`USB_IN`, J2)** | USB Type-A Female – USB OTG FS Host (PA11 `DM`, PA12 `DP`) |
| **Output Port (`USB_PC`, J3)** | USB Type-A Female / Cable – USB OTG HS Device in FS mode (PB14 `DM`, PB15 `DP`) |
| **Power Supply** | MP2451DT step-down buck converter (5 V VBUS to 3.3 V core rail) |
| **Oscillator** | 16 MHz external crystal (HSE) |
| **ESD Protection** | SRV05-4 transient voltage suppressors on USB data lines |
| **Status LEDs** | Red LED (`D1`): Power indicator (3.3 V rail)<br>Green LED (`D3`): Status / activity indicator (GPIO `PA0`) |
| **Debug Header** | 5-pin 2.54 mm SWD header (`J1`) |

---

## SWD Header Pinout (`J1`)

The board provides a 5-pin single-row header (`J1`, 2.54 mm pitch) for programming and debugging via SWD:

```
[ 1: GND ] [ 2: NRST ] [ 3: CLK ] [ 4: IO ] [ 5: 3.3V ]
```

| Pin | Label | STM32 Pin | Function |
| :---: | :---: | :---: | :--- |
| **1** | `GND` | `VSS` (Pins 12, 18, 60, 63) | Ground |
| **2** | `NRST` | `NRST` (Pin 7) | Active-low system reset |
| **3** | `CLK` | `PA14` (Pin 49) | SWD Clock (`SWCLK`) |
| **4** | `IO` | `PA13` (Pin 46) | SWD Data (`SWDIO`) |
| **5** | `3.3V` | `VDD` (Pins 1, 13, 19, 32, 48, 64) | 3.3 V Power rail |

### Connecting to an ST-LINK / Nucleo Debugger

When using the built-in ST-LINK/V2-1 on an STM32 Nucleo-64 board (CN4 header):

> [!IMPORTANT]
> **Remove the two jumpers on `CN2`** on the Nucleo board to disconnect the debugger from the onboard microcontroller and direct SWD lines to header `CN4`.
> 
> Note that `CN4` Pin 1 (`VDD_TARGET`) is a voltage-sensing line and does **not** power the target. Power Keysatron via USB or connect `3V3` from the Nucleo power header (`CN6` Pin 4) to Keysatron `J1` Pin 5.

Wiring between Nucleo `CN4` and Keysatron `J1`:

| Nucleo CN4 Pin | Nucleo Function | Wire to Keysatron `J1` Pin | Keysatron Function |
| :---: | :---: | :---: | :---: |
| Pin 1 | `VDD_TARGET` | **Pin 5** | `3.3V` |
| Pin 2 | `SWCLK` | **Pin 3** | `CLK` |
| Pin 3 | `GND` | **Pin 1** | `GND` |
| Pin 4 | `SWDIO` | **Pin 4** | `IO` |
| Pin 5 | `NRST` | **Pin 2** | `NRST` |

---

## Repository Contents

- `keysatron.kicad_pcb` – KiCad PCB layout (4-layer board)
- `keysatron.sch` – KiCad schematic
- `keysatron.pro` – KiCad project configuration
- `keysatron.csv` – Bill of Materials (BOM) with LCSC component part numbers
- `assembly/` – Pick-and-place component position files (`keysatron-all-pos.csv`) and BOM (`keysatron_bom.csv`) for automated assembly
- `gerber/` – Gerber fabrication files and drill files ready for PCB manufacturing (`keysatron_gerber.zip`)

---

## Firmware

The companion firmware for this board is maintained in the [keysatron-firmware](https://github.com/fablackner/keysatron-firmware) repository.
