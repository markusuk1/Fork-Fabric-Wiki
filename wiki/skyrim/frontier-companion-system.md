# Frontier Companion System

> Sources: owner requirements, COMPANION-001 implementation, COMPANION-002 crash correction and primary prior-art repositories, collected 2026-08-11
> Raw: [frontier requirements](../../raw/skyrim/2026-08-11-frontier-companion-requirements.md); [prior-art decision](../../raw/skyrim/2026-08-11-companion-prior-art.md); [idle-gather investigation](../../raw/skyrim/2026-08-11-companion-idle-gather-crash.md); [idle-gather correction proof](../../raw/skyrim/2026-08-11-companion-idle-gather-correction-proof.md); [F10 manual crash correction](../../raw/skyrim/2026-08-11-companion-f10-manual-crash.md)
> Commit: 3098f74
> Updated: 2026-08-11

## Purpose

The companion system turns Skyrim followers into grounded, configurable
assistants while retaining Skyrim's physical execution and Fork's durable
authority. Lydia is the first configured profile, not a special-case design.
The same contracts are intended for healers, guards, porters, scouts, workers
and other companion roles.

The first slice deliberately prioritizes truthful actions over broad dialogue.
It observes the real player and companion, chooses from typed policy, announces
an intended action, performs it through Skyrim, and writes an observed receipt.
An LLM can later translate natural discussion into the same policy or propose a
bounded plan; it cannot invent perception, healing resources or success.

## Ownership and control loop

| Layer | Owns |
|---|---|
| Fork | Companion identity/profile, typed policy, normalized observations, memory, plan authority, action identity and durable terminal receipts |
| Native companion plug-in | Low-latency sampling, safety checks, cooldowns, Skyrim-thread execution, local degraded-mode policy and receipt emission |
| Skyrim | Player/follower state, combat, inventory, capacity, navigation, activation, harvestables, animation, subtitles and save lifecycle |
| Voice provider | Licensed authored or synthesized audio only; never action authority |

The intended loop is `observe -> normalize -> arbitrate -> announce -> execute ->
verify -> receipt -> reconcile`. Combat reflexes remain local and deterministic;
longer planning, memory and optional LLM work remain asynchronous.

## Implemented COMPANION-001 slice

The CommonLibSSE-NG plug-in at
`src/skyrim/fork_fabric_companion` is pinned to Skyrim `1.6.1170.0`, SKSE64
`2.2.6` and the project's CommonLib baseline. The Lydia profile is data-driven
through `config/skyrim/companion-lydia.json` and currently provides:

- one-second bounded observation of player health, combat, inventory
  weight/capacity, movement/idle time, Lydia's capacity and nearby lawful
  harvest or loose-loot candidates;
- deterministic priority of emergency combat care over burden assistance and
  idle gathering, with separate cooldowns;
- a binary F10 tactics conversation that toggles burden help, emergency care,
  loose loot and idle resource gathering, persisted with replace-safe writes;
- near-capacity offers which move only ordinary eligible inventory after player
  consent, excluding equipped, favourited, quest, protected, enchanted and
  instance-bearing items while preserving Lydia's configured capacity reserve;
- medicine-only health consumables for close-range emergency care, consumed
  from Lydia's real inventory; if no valid resource exists she truthfully
  announces and requests native guard/combat behavior instead;
- bounded same-cell collection of lawful flora/tree activators or loose items,
  staging at most 64 engine handles under the cell lock, resolving and
  validating them afterward, requiring the selected target to be within 220
  game units and revalidating it immediately before activation;
- eight licensed Piper `en_GB-alba-medium` authored barks with exact Skyrim
  notification subtitles, plus append-only observations and action receipts.

The authored WAV path is audible but currently non-spatial Windows playback.
It does not clone or redistribute Lydia's original performer. Spatial
actor-attached playback, facial animation and unrestricted dynamic conversation
remain later presentation work.

MP3 is supported as voice-pipeline input, not as this deterministic player's
runtime format. The owner's ElevenLabs “Sending a heal your way” MP3 is decoded
and cached as mono PCM WAV before deployment; the other seven current barks use
the licensed Piper default. The source MP3 stays local. Later Skyrim-attached
spatial voice should emit Skyrim-compatible WAV/FUZ assets from the same
provider-neutral source.

## Safety and failure behavior

The plug-in never transfers equipped, favourited, quest, enchanted or
instance-bearing items. It never treats food, poison or an ordinary unavailable
potion as a player heal. It does not teleport Lydia or loot. Cross-cell actors,
open menus, combat-inappropriate gathering, owned targets, insufficient
capacity, changed targets and missing resources result in no action or an
explicit rejected receipt.

The ordinary owner playtest exposed an unsafe first implementation at the
idle-second-60 boundary: it globally walked attached cells, ran gameplay checks
under cell spin locks and retained a raw reference. COMPANION-002 replaced that
path with capped parent-cell enumeration and `ObjectRefHandle` staging. No
gameplay or ownership query runs inside the iterator callback, and every handle
is resolved and checked again outside the lock and before activation.

Policy writes use a temporary file followed by an operating-system replace and
write-through operation. A failed write rolls the menu toggle back and reports
failure rather than claiming persistence. Test-only mutation is guarded by an
unshipped flag, restores health and temporary inventory changes, quits without
saving and is cleaned by the harness.

## Evidence status

**Manual status correction:** this slice is not currently playable. The first
owner F10 test terminated Skyrim after four valid observer samples and before
any tactics or policy receipt. Evidence narrows the failure to the F10
`show_policy_menu` path at its native message-box boundary, but does not yet
prove the exact ABI, lifetime or callback-ownership cause. Do not treat the
binary tactics UI, burden acceptance or subsequent manual checklist as shipped.

The corrected build compiles with warnings as errors and its deterministic tests
cover arbitration priority, truthful no-resource guard fallback, cross-cell
rejection, idle collection selection, transfer exclusions/order and empty
post-transfer replay. Run `companion-idle-soak-03` loaded the owner's real
Lydia save and passed in `100,673 ms`: pair identity, licensed voice start,
medicine-backed health change `140 -> 120 -> 140`, physical pickup `0 -> 1`, 79
consecutive ObservePlayer samples through idle second 78, zero SKSE error
markers and zero remaining game processes. That crosses 19 ordinary scan ticks
beyond the former second-60 crash boundary. The installed DLL SHA-256 is
`3441135AD3997D6A212193748905CF1B54778C55888D110435757ADF85FD3C86`.

Skyrim's minimized main menu still requires one genuine DirectInput Continue
selection on launches where its save-list event is delayed. The harness does
not steal focus or claim background window messages are equivalent input.

## Explicit next boundaries

- project the native observation and receipt stream into Fork's companion
  tables and reconcile policy revisions in both directions;
- replace the binary tactics surface with typed natural conversation while
  retaining deterministic confirmation and authorization;
- use a spatial actor-attached voice route and optional provider adapter;
- add path-complete search, mining furniture, corpse/container permissions and
  bounded return-to-follow behavior;
- add physical owned-container and horse/cart logistics, including walk,
  transfer, verification and return;
- generalize traits, relationship, role and memory into volunteering, refusal,
  risk and dialogue style without weakening mechanical validation.
