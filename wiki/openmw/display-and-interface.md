# OpenMW Display and Interface Scaling

> Sources: Official OpenMW 0.51 video, GUI, window, path, FAQ, and source-default documentation, collected 2026-08-07
> Raw: Display and Interface Scaling Prior Art, Local Installation Verification, NVIDIA Driver Crash Evidence, Water Recovery Verification
> Commit: unknown
> Updated: 2026-08-07

## Decision

Modern resolution and readable menus/HUD are a **Configure** problem, not a
custom rendering problem. OpenMW 0.51 already supports arbitrary resolution,
borderless fullscreen, resolution-relative window layout, and GUI scaling from
0.5 to 8.0. Original-engine tools such as MGE XE, MWSE, and MCP do not apply to
OpenMW.

Use the private `config/openmw/display-presets.json` preset catalogue and
`scripts/set-openmw-display.ps1` safe applicator. The applicator
detects the physical primary-display resolution in a DPI-aware process, updates
only the relevant OpenMW user settings, preserves unrelated settings, and backs
up an existing file.

## Scaling policy

Scale primarily from vertical pixels. Aspect-ratio width should not inflate the
interface: QHD, UWQHD, and dual-QHD all use the same comfortable 1.25 base scale.

| Display class | Resolution | Comfortable scale |
|---|---:|---:|
| HD | 1280x720 | 0.80 |
| Full HD | 1920x1080 | 1.00 |
| Ultrawide Full HD | 2560x1080 | 1.00 |
| QHD | 2560x1440 | 1.25 |
| UWQHD | 3440x1440 | 1.25 |
| Dual QHD | 5120x1440 | 1.25 |
| Ultrawide 1600p | 3840x1600 | 1.40 |
| 4K UHD | 3840x2160 | 1.75 |
| Ultrawide 4K | 5120x2160 | 1.75 |
| 5K | 5120x2880 | 2.25 |

Unknown resolutions use the next suitable vertical-height band. `Compact`
multiplies the base by 0.90; `Large` multiplies it by 1.15. Results are rounded
to 0.05 and remain within OpenMW's documented range.

## Local UWQHD default

The detected local primary display is 3440x1440. Its initial preset is:

```text
[Video]
resolution x = 3440
resolution y = 1440
window mode = 1

[GUI]
scaling factor = 1.25
font size = 16
stretch menu background = false
```

`window mode = 1` is OpenMW's borderless fullscreen mode. Keeping menu
background stretching off avoids distorting 4:3 artwork across 21:9.

## Local installation state

OpenMW 0.51.0 is installed under `<OpenMW 0.51.0 installation>`. Its wizard
successfully registered the GOG GOTY data directory, all three official BSAs,
and `Morrowind.esm`, `Tribunal.esm`, and `Bloodmoon.esm` in that order. The
UWQHD preset above is applied to the user `settings.cfg`.

The engine launched as a responsive process and its log identified version
0.51.0. After maximum water features were enabled, entering the first world cell
crashed inside NVIDIA's `nvoglv64.dll` with error 3/subcode 7. There was no
explicit OpenGL out-of-memory line. Treat this as a driver-path crash with the
maximum water configuration as the leading trigger candidate, not as a proven
VRAM OOM or a failure of UWQHD/UI scaling.

The controlled recovery preset keeps 3440x1440 and GUI scale 1.25 unchanged,
while reducing water reflection detail from 5 to 2 and water RTT size from 2048
to 1024. That baseline successfully loaded later exterior-world assets, produced
no new OpenMW/NVIDIA Windows error event, and ended with `Quitting peacefully`.
It is now the verified local baseline. Raise water settings individually only
when deliberate graphics testing justifies the risk.

## Operation

After OpenMW is installed and its Morrowind data path is configured, run:

```powershell
powershell -ExecutionPolicy Bypass -File scripts\set-openmw-display.ps1
```

Use `-UiSize Large` if 1.25 remains too small, or `-UiSize Compact` if it is too
large. Use `-ListPresets` to inspect explicit defaults. The script defaults to
`Documents\My Games\OpenMW\settings.cfg`, the official Windows user path.

## Verification checklist

Runtime-check these surfaces at native resolution:

- HUD health, magicka, fatigue, weapon, spell, and minimap elements;
- inventory, character, magic, and map windows together;
- dialogue, journal, tooltips, and settings text;
- main menu and loading backgrounds;
- cursor confinement, Alt-Tab, and borderless presentation.

If text is readable but blurry, research and test the fully compatible TrueType
font package before changing engine code. If layout clips, first compare
`Comfortable` and `Large`; record any reproducible OpenMW 0.51 defect before
considering a UI modification.

## See Also

- [OpenMW Capability Baseline](capabilities.md)
- [Fork and OpenMW Capability Matrix](../architecture/capability-matrix.md)
