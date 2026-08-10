# OpenMW Display and Interface Scaling

> Sources: Official OpenMW 0.51 documentation and corrected local display/WGC evidence, 2026-08-07 to 2026-08-10
> Raw: [Display and Interface Scaling Prior Art](../../raw/openmw/2026-08-07-openmw-display-ui-scaling-prior-art.md), [Local Installation Verification](../../raw/openmw/2026-08-07-openmw-local-install-verification.md), [NVIDIA Driver Crash Evidence](../../raw/openmw/2026-08-07-openmw-nvidia-driver-crash.md), [Water Recovery Verification](../../raw/openmw/2026-08-07-openmw-water-recovery-verification.md), [UWQHD 1.50 Preference](../../raw/openmw/2026-08-09-uwqhd-scale-150-preference.md), [GPU-Backed Capture Correction](../../raw/openmw/2026-08-09-player-001-wgc-capture-correction.md), [WGC Process Isolation](../../raw/architecture/2026-08-10-dialogue-002-wgc-process-isolation-research.md), [Borderless Exact Frame](../../raw/openmw/2026-08-10-dialogue-002-borderless-wgc-frame-research.md), [DPI-Aware Capture Sizing](../../raw/openmw/2026-08-10-dialogue-002-dpi-aware-capture-sizing-research.md), [Final Qualification](../../raw/architecture/2026-08-10-dialogue-002-final-qualification-evidence.md)
> Commit: c0cea44
> Updated: 2026-08-10

## Decision

Modern resolution and readable menus/HUD are a **Configure** problem, not a
custom rendering problem. OpenMW 0.51 already supports arbitrary resolution,
borderless fullscreen, resolution-relative window layout, and GUI scaling from
0.5 to 8.0. Original-engine tools such as MGE XE, MWSE, and MCP do not apply to
OpenMW.

Use [the preset catalogue](../../config/openmw/display-presets.json) and
[the safe applicator](../../scripts/set-openmw-display.ps1). The applicator
detects the physical primary-display resolution in a DPI-aware process, updates
only the relevant OpenMW user settings, preserves unrelated settings, and backs
up an existing file.

## Scaling policy

Scale primarily from vertical pixels. Aspect-ratio width does not normally
inflate the interface. UWQHD is an explicit local usability override at 1.50;
QHD and dual-QHD retain the general 1440p base of 1.25.

| Display class | Resolution | Comfortable scale |
|---|---:|---:|
| HD | 1280x720 | 0.80 |
| Full HD | 1920x1080 | 1.00 |
| Ultrawide Full HD | 2560x1080 | 1.00 |
| QHD | 2560x1440 | 1.25 |
| UWQHD | 3440x1440 | 1.50 |
| Dual QHD | 5120x1440 | 1.25 |
| Ultrawide 1600p | 3840x1600 | 1.40 |
| 4K UHD | 3840x2160 | 1.75 |
| Ultrawide 4K | 5120x2160 | 1.75 |
| 5K | 5120x2880 | 2.25 |

Unknown resolutions use the next suitable vertical-height band. `Compact`
multiplies the base by 0.90; `Large` multiplies it by 1.15. Results are rounded
to 0.05 and remain within OpenMW's documented range.

## Local UWQHD default

The detected local primary display is 3440x1440. Its preferred preset is:

```text
[Video]
resolution x = 3440
resolution y = 1440
window mode = 1

[GUI]
scaling factor = 1.50
font size = 16
stretch menu background = false
```

`window mode = 1` is OpenMW's borderless fullscreen mode. Keeping menu
background stretching off avoids distorting 4:3 artwork across 21:9.

## Local installation state

OpenMW 0.51.0 is installed under `C:\Program Files\OpenMW 0.51.0`. Its wizard
successfully registered the GOG GOTY data directory, all three official BSAs,
and `Morrowind.esm`, `Tribunal.esm`, and `Bloodmoon.esm` in that order. The
UWQHD preset above is applied to the user `settings.cfg`.

The engine launched as a responsive process and its log identified version
0.51.0. After maximum water features were enabled, entering the first world cell
crashed inside NVIDIA's `nvoglv64.dll` with error 3/subcode 7. There was no
explicit OpenGL out-of-memory line. Treat this as a driver-path crash with the
maximum water configuration as the leading trigger candidate, not as a proven
VRAM OOM or a failure of UWQHD/UI scaling.

The controlled recovery test kept 3440x1440 and the then-current GUI scale 1.25 unchanged,
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

Use `-UiSize Large` if 1.50 remains too small, or `-UiSize Compact` if it is too
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

## Non-intrusive automated evidence

Automated visual proofs use `launch-clean-world.ps1 -NonIntrusiveCapture`. This
uses a separate borderless capture profile at the requested physical pixel
dimensions without activating it and keeps it at the bottom of the desktop
stack behind the user's current application. Placement temporarily applies a
thread-scoped per-monitor-aware-v2 context so a 150% desktop scale cannot turn
3440x1440 into 5160x2160. The previous DPI context is restored immediately.
A no-activate `CreateProcessW` startup contract and exact foreground-handle
invariant fail the run if OpenMW takes focus.

The capture profile is never the playable explorer profile. Runtime proofs
therefore cannot overwrite the owner's 3440x1440/1.50 settings. The normal
desktop launcher re-applies that UWQHD default before starting.

`capture-openmw-window.ps1` captures the exact HWND through
`Windows.Graphics.Capture`, copies the GPU frame through Direct3D 11, rejects
blank content and writes PNG without copying the visible desktop. There is no
`PrintWindow`, activation, foreground-restoration or desktop-copy fallback.
Failure deletes the candidate image and fails the proof.

Historical POP-002 evidence used an OpenMW-native `SCRN` renderer preview after
WGC returned `0x80070424`. Current repository policy is stricter: new automated
visual acceptance must pass `capture-openmw-window.ps1`; renderer previews may
diagnose rendering but cannot replace WGC or direct owner observation.

Completely off-screen placement is deliberately not used: OpenGL was observed
to leave a cached loading frame in the compositor even while simulation ticks
continued. The bottom-stacked window keeps the renderer live while remaining
behind the foreground application. The final DIALOGUE-002 proof preserved the
foreground handle and produced a visually inspected exact 3440x1440 live
dialogue frame at GUI scale 1.50.

## See Also

- [OpenMW Capability Baseline](capabilities.md)
- [Fork and OpenMW Capability Matrix](../architecture/capability-matrix.md)
