# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

ZMK firmware configuration for a **Dactyl CC** split ergonomic keyboard using **nice!nano** controllers. Firmware is built entirely via GitHub Actions — there is no local build system.

## Build System

- **Builds are CI-only**: Push to `master` triggers the ZMK GitHub Actions workflow (`.github/workflows/build.yml`), which builds firmware for both halves (`dactyl_cc_left` and `dactyl_cc_right`).
- `build.yaml` defines the build matrix (board + shield combinations).
- There are no local build, lint, or test commands.

## Architecture

### Keymap (`config/dactyl_cc.keymap`)

The keymap defines 5 layers using ZMK devicetree syntax:

| Layer | Index | Purpose |
|-------|-------|---------|
| DEFAULT | 0 | Standard QWERTY typing |
| LOWER | 1 | Navigation (arrows, page up/down, home/end), mouse movement/scroll, brightness/volume |
| RAISE | 2 | Symbols and numpad (right hand) |
| ADJUST | 3 | Function keys (F1-F12), Bluetooth profile switching, `sys_reset`/`bootloader`, toggle to GAME |
| GAME | 4 | Gaming layout (left hand only, shifted keys for WASD), exits back to DEFAULT |

Key conventions:
- Layer access: LOWER/RAISE via hold-tap on thumb keys (`lt`), ADJUST via sticky-layer (`sl`) from LOWER/RAISE
- Modifier-taps (`mt`) are used extensively on thumb cluster keys (e.g., `mt LGUI TAB`)
- `HYPER(key)` = Shift+Ctrl+Alt+Gui, `MEH(key)` = Shift+Ctrl+Alt
- Mouse pointing is enabled with custom speed settings (`ZMK_POINTING_DEFAULT_MOVE_VAL`, `ZMK_POINTING_DEFAULT_SCRL_VAL`)

### Shield Definition (`config/boards/shields/dactyl_cc/`)

Custom shield files define the hardware:
- `dactyl_cc.dtsi` — 6x12 matrix transform, GPIO pin mapping, key scan configuration
- `dactyl_cc_left.overlay` / `dactyl_cc_right.overlay` — per-half column GPIO pins
- Split keyboard communicates via Bluetooth (both halves have `CONFIG_ZMK_SLEEP=y`)

### Configuration (`config/dactyl_cc.conf`)

Enables ZMK pointing (mouse keys) with smooth scrolling.

### West Manifest (`config/west.yml`)

Pins ZMK firmware to revision `v0.3` from `zmkfirmware/zmk`.
