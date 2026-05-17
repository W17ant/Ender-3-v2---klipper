# Ender 3 V2 Klipper Configuration

Klipper configuration files for my heavily modified Ender 3 V2 3D printer.

![Ender 3 v2](https://github.com/W17ant/Ender-3-v2---klipper/assets/147003220/b7940b86-0989-4038-b4c2-7b7211fa7141)

## Table of Contents

- [Hardware Specs](#hardware-specs)
- [Configuration Files](#configuration-files)
- [Slicer Setup](#slicer-setup)
- [Macros](#macros)
- [Input Shaper Results](#input-shaper-results)
- [Useful Links](#useful-links)

## Hardware Specs

| Component | Details |
|-----------|---------|
| **Mainboard** | Stock Creality 4.2.2 (STM32F103) |
| **Motion** | 3DFused linear rails X/Y/Z |
| **Z-Axis** | Dual Z with 1.8° Creality steppers |
| **Hotend** | Creality Sprite Pro Extruder |
| **Probe** | BLTouch (physical endstops on XY) |
| **Accelerometer** | KUSBA ADXL345 (Rampon Firmware) |
| **Filament Sensor** | Trianglelab runout sensor |
| **Cooling** | Noctua NF-A9-14 PWM (PSU + Mainboard) |
| **Lighting** | 12V 6000K COB LED strip (480 LED/m) |
| **Camera** | ELP 1080P USB3.0 (IMX291, 50fps) |
| **Host** | Raspberry Pi 4B 8GB + Official 7" Display |

## Configuration Files

| File | Description |
|------|-------------|
| `printer.cfg` | Main printer configuration - steppers, bed, extruder, probe |
| `macro.cfg` | Print macros - START_PRINT, END_PRINT, filament handling |
| `mainsail.cfg` | Mainsail client macros (owns PAUSE/RESUME/CANCEL_PRINT, virtual_sdcard) |
| `KAMP_Settings.cfg` + `KAMP/` | Klipper Adaptive Meshing & Purging settings |
| `moonraker.conf` | Moonraker API server configuration |
| `moonraker_obico_macros.cfg` | Obico first-layer scan macros |
| `moonraker-obico-update.cfg` | Obico update_manager entry |
| `octoeverywhere-system.cfg` / `octoeverywhere.conf` | OctoEverywhere remote access |
| `crowsnest.conf` / `sonar.conf` | Camera streaming / link monitoring |
| `KlipperScreen.conf` | Touchscreen UI configuration |
| `timelapse.cfg` | Timelapse recording settings |
| `adxlmcu.cfg` | ADXL345 accelerometer configuration |

> **Not tracked** (see `.gitignore`): `moonraker-obico.cfg` (contains the Obico
> account `auth_token` — keep it off the public repo), the `Spoolman/`
> database and rotated `*.log` files.
>
> **Section ownership:** `PAUSE`, `RESUME`, `CANCEL_PRINT` and `pause_resume`
> are defined once, in `mainsail.cfg`. They were previously also redefined in
> `macro.cfg`; since Klipper parses with `strict=False`, the last include
> silently won, making behaviour depend on include order. The duplicates were
> removed so the maintained mainsail client macros are authoritative. `G29`
> lives once in `printer.cfg`.

## Slicer Setup

### OrcaSlicer / PrusaSlicer / SuperSlicer

**Start G-code:**
```gcode
START_PRINT BED_TEMP=[bed_temperature_initial_layer_single] EXTRUDER_TEMP=[nozzle_temperature_initial_layer]
```

**End G-code:**
```gcode
END_PRINT
```

## Macros

### Print Control
| Macro | Description |
|-------|-------------|
| `START_PRINT` | Heats bed/nozzle, homes, creates mesh, purges |
| `END_PRINT` | Parks head, cools down, clears mesh |
| `PAUSE` | Pauses print and parks toolhead |
| `RESUME` | Resumes paused print |
| `CANCEL_PRINT` | Cancels current print |

### Filament
| Macro | Description |
|-------|-------------|
| `LOAD_FILAMENT` | Loads filament into extruder |
| `UNLOAD_FILAMENT` | Unloads filament from extruder |
| `M600` | Filament change (pause + unload) |

### Calibration
| Macro | Description |
|-------|-------------|
| `G29` | Home + bed mesh + park |
| `BED_LEVELING` | Heat bed and run screw tilt adjust |
| `Z_PROBE_CALIBRATE` | Calibrate Z offset with heated bed |
| `PID_EXTRUDER` | PID tune extruder (default 210C) |
| `PID_BED` | PID tune bed (default 60C) |

### Utility
| Macro | Description |
|-------|-------------|
| `CG28` | Conditional home - only homes if not already homed |
| `HEAT_SOAK` | Heat bed and wait for thermal stabilization (default 10 min) |
| `M73` | Updates display with print progress percentage |

### Preheat
| Macro | Extruder | Bed |
|-------|----------|-----|
| `PREHEAT_PLA` | 150C | 60C |
| `PREHEAT_PETG` | 170C | 60C |
| `PREHEAT_ABS` | 180C | 80C |
| `PREHEAT_BED_ONLY` | - | 60C |
| `COOL` | Off | Off |

## Input Shaper Results

Calibrated using KUSBA ADXL345 accelerometer.

| Axis | Shaper Type | Frequency |
|------|-------------|-----------|
| X | EI | 69.6 Hz |
| Y | MZV | 30.2 Hz |

![X-Axis Shaper](shaper_calibrate_x.png)
![Y-Axis Shaper](shaper_calibrate_y.png)

## Useful Links

### Setup & Installation
- [Raspberry Pi Imager](https://www.raspberrypi.com/software/) - Flash OS to SD card
- [KIAUH](https://github.com/dw-0/kiauh) - Klipper Installation And Update Helper
- [Klipper Documentation](https://klipper3d.org/) - Official Klipper docs
- [Mainsail OS](https://docs-os.mainsail.xyz/) - Mainsail web interface

### Extensions
- [KAMP](https://github.com/kyleisah/Klipper-Adaptive-Meshing-Purging) - Adaptive Meshing & Purging
- [Crowsnest](https://crowsnest.mainsail.xyz/) - Camera streaming
- [KlipperScreen](https://klipperscreen.readthedocs.io/) - Touchscreen interface
- [Spoolman](https://github.com/Donkie/Spoolman) - Filament spool manager

### Remote Access
- [OctoEverywhere](https://octoeverywhere.com/) - Remote access & notifications
- [Obico](https://obico.io/) - AI failure detection (The Spaghetti Detective)

### Hardware
- [KUSBA](https://github.com/xbst/KUSBA) - USB accelerometer for input shaping
