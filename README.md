# TOTEM — ZMK Firmware Config

[![Build ZMK firmware](../../actions/workflows/build.yml/badge.svg)](../../actions/workflows/build.yml)

> Miryoku-inspired ZMK configuration for the [TOTEM](https://github.com/GEIGEIGEIST/TOTEM) 38-key split keyboard. QWERTY base, vim-style navigation, home row mods, mouse keys, combos, real-time [ZMK Studio](https://zmk.studio/) support, and [Keymap Editor](https://nickcoutsos.github.io/keymap-editor/) integration.

---

## Keymap

![Keymap](keymap-drawer/totem.svg)

*Auto-generated with [keymap-drawer](https://github.com/caksoylar/keymap-drawer) on every push.*

---

## Layout Philosophy

This config follows the [Miryoku](https://github.com/manna-harbour/miryoku) layout system — a minimal 36-key layout that puts everything within one key of the home row.

- **QWERTY** base with **home row mods** (GACS order)
- **Vim-style** navigation (HJKL arrows)
- **Thumb-activated layers** — access NAVNUM, SYMFN, MEDIA, or MOUSE
- **Mouse keys** — full mouse emulation (movement, scroll, click) on the MOUSE layer
- **Combos** — Caps Word, Esc, Bspc, Tab, Enter, clipboard, and symbol combos
- **Extra keys** — Ctrl/Esc (left) and Hyper (right) on the outer pinky positions

### Thumb Keys

```
Left hand                          Right hand
┌───────────┬───────────┬────────┐ ┌─────────┬──────────┬─────────┐
│ MEDIA/ESC │  NAV/SPC  │MOUSE/TAB│ │ SYM/ENT │ NUM/BSPC │ FUN/DEL │
└───────────┴───────────┴────────┘ └─────────┴──────────┴─────────┘
```

### Extra Keys

| Position | Tap | Hold | Use Case |
|:---------|:----|:-----|:---------|
| Left outer | Esc | Ctrl | Vim escape + Ctrl shortcuts without leaving home row |
| Right outer | — | Hyper (GUI+Alt+Ctrl+Shift) | Launcher shortcuts (Raycast, Alfred, etc.) with a clean modifier namespace |

### Layers

| Layer | Activation | Left Hand | Right Hand |
|:------|:-----------|:----------|:-----------|
| BASE | Default | QWERTY + home row mods | QWERTY + home row mods |
| NAVNUM | Hold `Space` / `Tab` | Home row mods / Nav | Vim arrows, Keypad numbers (`1-9`), PgUp/PgDn |
| SYMFN | Hold `Enter` | Symbols (`! @ # $ % ^ & *`) | Function keys (`F1-F12`), Volume / Play controls |
| MEDIA | Hold `Esc` | Home row mods | Prev/Next, Vol Up/Down, Play/Pause, BT profiles (0-3, Clear) |
| MOUSE | Hold `Tab` | Mouse Scroll / Speed | Mouse movement (`mmv`), Scroll (`msc`), Left/Right/Middle Click |

### Combos

| Combo | Keys | Output | Type |
|:------|:-----|:-------|:-----|
| F + J | index home | Caps Word | Horizontal |
| K + L | home right | Enter | Horizontal |
| X + C | bottom left | Cmd+C (Copy) | Horizontal |
| C + V | bottom left | Cmd+V (Paste) | Horizontal |
| X + V | bottom left | Cmd+X (Cut) | Horizontal |
| F + V | left index | `=` (equal) | Vertical |
| Thumbs 32+35 | inner thumbs | Media Layer | Dual Thumb |
| J + M (16+25) | right index | `~` (tilde) | Vertical |

All combos use `require-prior-idle-ms` to prevent misfires during fast typing.

### Home Row Mods

```
Left:   GUI / A    ALT / S    CTRL / D    SHFT / F
Right:  SHFT / J   CTRL / K   ALT / L     GUI / '
```

| Setting | Value |
|:--------|:------|
| Flavor | tap-preferred |
| Tapping term | 200 ms |
| Quick tap | 175 ms |
| Require prior idle | 150 ms |

---

## Hardware

| | |
|:--|:--|
| **Keyboard** | [TOTEM](https://github.com/GEIGEIGEIST/TOTEM) — 38-key columnar stagger split |
| **MCU** | Seeeduino XIAO BLE (nRF52840) |
| **Firmware** | [ZMK](https://zmk.dev/) main branch (Zephyr 4.1) |
| **Features** | Mouse keys, ZMK Studio, Keymap Editor layout mapping, Bluetooth |

---

## Getting the Firmware

Firmware builds automatically via GitHub Actions on every push.

1. Go to the [Actions](../../actions) tab
2. Open the latest successful **Build ZMK firmware** run
3. Download the `totem_left` and `totem_right` artifacts (`.uf2` files)

### Flashing

1. Double-tap the reset button on the XIAO BLE to enter bootloader mode
2. A USB drive will appear — drag the `.uf2` file onto it
3. Flash left half first (`totem_left`), then right (`totem_right`)

---

## Keymap Editors

### 1. Keymap Editor (by Nick Coutsos)

This repository includes a custom `config/info.json` metadata file specifically formatted for [Keymap Editor by Nick Coutsos](https://nickcoutsos.github.io/keymap-editor/).

- **1:1 Visual Alignment:** Maps physical keys `Z X C V B N M , . /` and Outer Pinky keys cleanly without matrix key shifting.
- **GitHub Integration:** Authorize Keymap Editor to edit your keymap graphically in your browser; changes commit directly back to GitHub.
- **Matrix Formatting:** Includes full `row` and `col` properties for clean `.keymap` matrix generation.

### 2. ZMK Studio

[ZMK Studio](https://zmk.studio/) lets you edit your keymap in real time over WebUSB or Bluetooth — no reflashing or code needed.

#### How to Connect

1. **Plug in the left half** via USB (Studio is enabled on the left half)
2. Open [zmk.studio](https://zmk.studio/) in a WebUSB-compatible browser (Chrome, Edge, or Brave)
3. Click **Connect** and select your TOTEM from the device list
4. Your current keymap loads automatically — start editing

#### Build Config

Studio support is configured in `build.yaml` for the left half:

```yaml
- board: xiao_ble//zmk
  shield: totem_left
  snippet: studio-rpc-usb-uart
  cmake-args: -DCONFIG_ZMK_STUDIO=y -DCONFIG_ZMK_STUDIO_LOCKING=n
```

- `CONFIG_ZMK_STUDIO=y` — enables the Studio RPC endpoint
- `CONFIG_ZMK_STUDIO_LOCKING=n` — disables security lock
- `snippet: studio-rpc-usb-uart` — routes Studio communication over USB serial

---

## Firmware Config

Key settings in `config/totem.conf`:

| Setting | Value | Purpose |
|:--------|:------|:--------|
| `CONFIG_ZMK_MOUSE` | `y` | Enable mouse key emulation (movement, scroll, click) |
| `CONFIG_ZMK_USB_LOGGING` | `n` | Disable USB logging for production use |

---

## Repository Structure

```
├── config/
│   ├── totem.keymap        # Keymap definition (layers, combos, macros)
│   ├── totem.conf          # Firmware config (mouse keys, logging)
│   ├── info.json           # Keymap Editor (Nick Coutsos) physical layout mapping
│   └── west.yml            # ZMK module manifest (main branch)
├── boards/shields/totem/   # Board shield definition (matrix, pins, layout)
├── keymap-drawer/          # Auto-generated keymap diagrams
│   ├── config.yaml         # Diagram styling + mouse key labels
│   ├── totem.yaml          # Parsed keymap data
│   └── totem.svg           # Visual keymap diagram
├── build.yaml              # GitHub Actions build matrix
└── .github/workflows/
    ├── build.yml            # Firmware build workflow
    └── draw-keymaps.yml     # Keymap diagram generator
```

---

## Credits

- [TOTEM](https://github.com/GEIGEIGEIST/TOTEM) keyboard by GEIGEIGEIST
- [Miryoku](https://github.com/manna-harbour/miryoku) layout by Manna Harbour
- [ZMK Firmware](https://zmk.dev/)
- [Keymap Editor](https://github.com/nickcoutsos/keymap-editor) by Nick Coutsos
- [keymap-drawer](https://github.com/caksoylar/keymap-drawer) by caksoylar
- [urob/zmk-config](https://github.com/urob/zmk-config) — combo and home row mod tuning reference
