# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

ZMK firmware configuration for a **Corne wireless split keyboard** (36-key, 5-column layout) with nice!nano v2 MCU and nice!view display. US layout, heavily customized for the compact 36-key form factor.

## Build System

Firmware is built via **GitHub Actions** — push to trigger a build. There is no local build step.

- `.github/workflows/build.yml` calls the official ZMK build workflow (`zmkfirmware/zmk/.github/workflows/build-user-config.yml@v0.3`)
- `config/build.yaml` defines the build matrix (left/right halves, nice_nano_v2 board, corne + nice_view shields)
- `config/west.yml` pins the ZMK firmware version (west manifest)
- Build output: `.uf2` firmware files (downloaded from GitHub Actions artifacts)

## Key Files and Architecture

| File | Purpose |
|------|---------|
| `config/corne.keymap` | **Main config** — layers, combos, behaviors, physical layout (DTS format) |
| `config/corne.conf` | Kconfig options: BT power, debounce, ZMK Studio |
| `config/keys/36.h` | Position name mappings (LT0-LT4, LM0-LM4, etc.) for the 36-key matrix |
| `config/customkeys.h` | Custom HID keycodes (less-than/greater-than) |
| `config/keys_de.h` | Legacy German key mappings (may still be referenced but layout is US-based) |
| `config/build.yaml` | Hardware build matrix for CI |
| `config/west.yml` | ZMK firmware dependency manifest |

## Keymap Structure

The keymap (`corne.keymap`) defines:

- **4 layers**: Default (QWERTY), Number (F-keys + digits), Navigation, Media/Bluetooth
- **Combos**: Position-based chords (50ms timeout) for ESC, Backspace, Tab, Shift, Ctrl, Delete, Home, End, layer toggles
- **Shorthand macros**: `___` = transparent, `_x_` = none, `BPSTE`/`BCPY`/`BCUT` for clipboard, media aliases
- **Sticky shift** behavior configured with 2000ms release and quick-release

Layer access: MO(1) and MO(2) on thumb keys, MO(3) via combo (positions 31+34).

## Conventions

- Key position names use the grid from `keys/36.h`: L/R (side) + T/M/B (row) + 0-4 (column), H0-H2 for thumb keys
- `keys_de.h` is a legacy artifact; the layout is US-based
- Custom keycodes use ZMK's `&macro_press`/`&macro_release` pattern in `customkeys.h`
- Physical layout uses `five_column_transform` with the `corne_5col_layout` node

## Hardware

- **MCU**: nice!nano v2 (nRF52840)
- **Display**: nice!view via adapter shield
- **Connectivity**: Bluetooth (TX power +8dBm)
- **ZMK Studio**: Enabled (runtime keymap editing over USB)
