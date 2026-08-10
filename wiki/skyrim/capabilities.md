# Skyrim SE/AE + SKSE Capability Baseline

> Sources: official SKSE/runtime pages, Creation Kit reference and primary community repositories, collected 2026-08-10
> Raw: [PLATFORM-001 platform audit](../../raw/architecture/2026-08-10-openmw-skyrim-platform-audit.md); [SKSE Steam-bootstrap observation](../../raw/skyrim/2026-08-10-skse-steam-bootstrap-observation.md); [live actor receipt](../../raw/skyrim/2026-08-10-skyrim-live-actor-receipt.md); [live humanoid bridge proof](../../raw/skyrim/2026-08-10-skyrim-live-humanoid-bridge-proof.md); [embodied-day prior art](../../raw/skyrim/2026-08-10-skyrim-embodied-day-prior-art.md); [embodied-day proof](../../raw/skyrim/2026-08-10-skyrim-embodied-day-proof.md)
> Commit: 3098f74
> Updated: 2026-08-10

## Overview

The Skyrim candidate is the 64-bit Special Edition engine on a pinned 1.6.x
runtime, extended through SKSE64 and a project-owned native plugin. Classic
32-bit Skyrim, Windows Store/Game Pass and Epic builds are outside the candidate
set.

Skyrim's central advantage is an existing physical-life vocabulary. Actors can
eat, sleep, sandbox, patrol, guard, travel, use items and furniture, participate
in scenes, trade, own property and react to crime through native engine systems.
That does not provide the desired complete simulation by itself; it provides a
far richer executor for Fork's decisions.

## Observed local validation baseline

SKYRIM-001 directly verified the installed Steam game as runtime `1.6.1170.0`.
The Steam `Skyrim Script Extender` app initially present was classic 32-bit
SKSE `1.7.3` in a separate classic Skyrim directory, not SKSE64. The matching
official SKSE64 `2.2.6` loader, runtime DLL and 62 compiled Papyrus files are
now installed in Skyrim Special Edition with source-to-destination hash
equality.

Steam must already be running before `skse64_loader.exe` is invoked. A local
attempt started the loader at 16:28:05, Steam at 16:28:06 and the surviving
game at 16:28:28. Although the loader reported `hook thread complete`, the
surviving process contained no SKSE/Fork modules, its current SKSE log was empty
and it emitted no receipts. This is consistent with Steam bootstrap replacing
the initially hooked process. Accept a launch only after inspecting the
surviving modules and current non-empty SKSE log; a loader log alone is not
proof of injection.

Two same-BOM minimized launches reached the exact SKSE lifecycle through
`data_loaded` with zero logged error markers. A project-owned native plugin and
50 ms background file bridge produced allow-listed applied, idempotent replay,
deadline-obsolete and unallow-listed rejected receipts without doing polling
work on Skyrim's game thread. A two-launch recovery test restored the byte
checkpoint and receipt ledger, retained the original exact-replay terminal,
and rejected divergent command-ID reuse. A 5,000-command run returned 5,000
applied terminals in an `812 ms` bridge span; this proves transport capacity,
not actor simulation scale.

A repository-local portable Mod Organizer 2 `2.5.2` profile owns separate
INIs/saves and an isolated `Fork Fabric Runtime` mod. A direct-file-absent VFS
run loaded both project probes and reached `data_loaded` in `7,382 ms` with no
SKSE error markers. CommonLibSSE-NG `v3.7.0` is pinned and both project plugins
compile with warnings treated as errors.

SKYRIM-002 closes the live humanoid bridge gate. A two-launch harness loaded a
Whiterun save and observed 12 humanoids per run. Lydia (form `666772`) shared
the player's cell `107121`, had package `378955` and two equipped forms. An
external durable command passed deadline, command-ID, fingerprint, allowlist
and loaded-target validation, entered Skyrim only through its task queue, and
changed her weapon state from sheathed to drawn after seven bounded polls. A
separate command restored the original state after twelve polls; neither action
used teleport.

Exact replay retained the applied terminal, divergent reuse of the same ID
conflicted, an expired command became obsolete, and missing-target and
unallow-listed commands rejected precisely. After a clean Skyrim restart Lydia
was again observed in her original saved state; replaying the original draw
command returned its retained terminal without physically drawing the weapon.
The run produced 10 terminal receipts in 33,882 ms with zero SKSE error
markers. The direct actor/restart harness requires the independently qualified
MO2 profile to contain the identical current plugin hashes, preserving the
isolated deployment contract while avoiding nondeterministic refresh behavior
from a long-lived protected MO2 command receiver.

Evidence: [SKYRIM-001 runtime/toolchain observation](../../raw/skyrim/2026-08-10-skyrim-runtime-toolchain-observation.md),
[runtime/bridge feasibility evidence](../../raw/skyrim/2026-08-10-skyrim-runtime-bridge-feasibility-evidence.md)
and [live actor receipt](../../raw/skyrim/2026-08-10-skyrim-live-actor-receipt.md),
plus the [live humanoid bridge proof](../../raw/skyrim/2026-08-10-skyrim-live-humanoid-bridge-proof.md).

SKYRIM-003 closes the embodied-day gate. The installed `Skyrim.esm` was parsed
read-only to bind 15 package Form IDs to authored Editor IDs rather than
inferring activities from generic package type `18`. Adrianne's catalogue
contains forge work, house sandbox, city walk and sleep; Idolaf's contains the
Bannered Mare, sleep, city patrol and home; Jon's contains repairs, patrol, the
Bannered Mare and fallback sleep.

A final 148,482 ms live run drove 10:00, 18:00 and 01:00 phases through the
durable bridge. Adrianne, Idolaf and Jon crossed at least three cells each and
moved more than 1,100 comparable same-cell units; seven distinct cells included
the Bannered Mare interior. The run recorded 109 furniture or sit/sleep samples.
These are observed engine states, not background schedule claims.

A same-cell native flee interruption displaced Adrianne by 1,733.87 units.
Ending the interruption selected authored forge package `228542`, which
remained stable through the recovery window. A missing destination rejected as
`destination_not_loaded` with `retain_current_package`; the package remained
unchanged. Twenty-one test-only observer relocations moved only the player so
otherwise unloaded interiors could be inspected; every receipt explicitly
reported no routine-actor teleport.

Three 3440x1440 frames were captured through Windows Graphics Capture. Failed
attempts were rejected when Skyrim owned foreground or exposed only its
minimized title bar. The accepted harness asks Skyrim to minimize itself, waits
for Windows to transfer focus naturally and uses only no-activate/bottom window
operations with DPI-correct sizing. The run had
zero SKSE error markers. Save rollback, full process recovery, actor scale and
regional suppression remain S4-S6.

Evidence: [embodied-day proof](../../raw/skyrim/2026-08-10-skyrim-embodied-day-proof.md).

## Native capability catalogue

| Area | Native capability | Project use |
|---|---|---|
| Actor model | Stats, class, inventory, factions, relationships, packages, animation and combat data | Physical projection of Fork-owned identity and state |
| Daily life | Eat, Sleep, Sandbox, Travel, Patrol, Guard, Use Item At and Dialogue packages | Visibly enact schedules selected by Fork |
| Smart objects | Exclusive furniture markers for sit, sleep, lean, workbenches and custom idles | Work, meals, sleep, worship, social and craft stations |
| Navigation | Navmesh, doors, exterior/interior pathing and package destinations | Engine resolves local physical routes |
| Scenes | Phased dialogue, package and action orchestration | Short deterministic embodied interactions |
| Quests/aliases | Dynamic actor/reference binding, stages, objectives and script events | Thin presentation/adaptation only; never canonical world state |
| Story Manager | Event-conditioned quest selection | Optional local event adapter, not the global narrative brain |
| Society | Factions, ranks, relationships, crime factions, ownership and location records | Mechanical projection of Fork hierarchy, law and property |
| Commerce | Merchant factions, barter, containers, leveled stock and gold | Physical transaction surface reconciled with Fork ledger |
| Construction | Hearthfire materials, workbenches and staged structure activation | Proven pattern for Fork-authorized building projects |
| Survival | Official Survival Mode plus extensible actor values/effects | Presentation and physical consequences of Fork-owned needs |

## Extension stack

| Layer | Role | Constraint |
|---|---|---|
| SKSE64 | Script extensions, native plugin loading, messaging and cosave surfaces | Exact runtime/store build required; do not bundle SKSE |
| Address Library | Runtime-relative address IDs for native plugins | Version data must cover the pinned executable |
| CommonLibSSE-NG | C++ access to reverse-engineered engine types and cross-runtime relocation | Native code can crash the game; wrap all engine access and test in process |
| Papyrus | Engine-thread-safe scripted content/actions | Event-driven and latency-sensitive; not a database or heavy-work scheduler |
| powerofthree's Papyrus Extender | Additional functions and events | Useful dependency, but keep the core bridge minimal |
| SkyUI/MCM or PrismaUI | Player/operator configuration surfaces | UI only; no canonical state ownership |

The production bridge should be a small CommonLibSSE-NG plugin plus minimal
Papyrus quests/packages. It must never wait for network, database, LLM, TTS or
embedding work on Skyrim's game thread.

## Authoring and reproducibility

- Mod Organizer 2 `2.5.2` isolates the project profile and virtualizes game
  data. The portable profile requires `game_edition=Steam` and a current
  `version=2.5.2` field to avoid first-run setup/migration prompts.
- Creation Kit authors cells, navmesh, actors, packages, scenes and dialogue.
- xEdit audits records, masters and conflicts.
- Mutagen generates repeatable plugins and suppression patches from code.
- Spriggit serializes plugin records into reviewable text and rebuilds binaries.
- Open Animation Replacer supplies conditional presentation animations.
- Pandora Behaviour Engine+ is reserved for genuinely new behavior graphs; it
  should not enter the first build unless native packages/OAR are insufficient.

## Community systems worth studying

| Capability | Projects | Use boundary |
|---|---|---|
| Varied schedules | AI Overhaul SSE | Package-authoring reference; project NPCs should have owned schedules |
| Visible interactions | Immersive Interactions, OAR | Candidate presentation layer |
| LLM speech | Mantella, SkyrimNet | Provider/UI/perception reference; Fork remains memory and policy authority |
| Physical action library | SeverActions | Strong spike reference; no code reuse until license is explicit |
| Cross-cell autonomy | IntelEngine | Feasibility reference; its bounded concurrency and teleport recovery are not whole-world proof |
| Economy/trade | Trade Routes, Your Market Stall, Buy Displayed Items NG | Adapt market and interaction patterns into Fork-owned ledger |
| Property/business | LandLord | Reference purchase/contracts/income flow; restricted scripts are not bundled |
| Factions | Organic Factions, Faction Warfare | Reference growth, reputation, armory and event patterns |
| Survival | SunHelm, Last Seed | Reference UX and physical consequences |
| Construction | Hearthfire Extended, Settlement Builder | Reference staged/free-placement approaches; permissions decide reuse |

## What Skyrim does not solve

- It does not provide a transactional, server-authoritative world simulation.
- Papyrus and save files are not substitutes for Fork's durable ledger.
- Native packages do not create believable goals, beliefs, memory or economics.
- Unloaded actors and cross-world events still need abstraction and careful
  reconciliation; community mods often simulate or teleport around failures.
- Runtime updates can break SKSE native plugins.
- The engine is proprietary and cannot be fixed or instrumented like OpenMW.
- Vanilla quests/aliases/scenes have hidden dependencies; a clean world requires
  audited suppression, not blind record deletion.
- Bethesda and third-party assets/code retain their own redistribution terms.

## Recommended ownership split

Fork owns identity, profiles, needs, beliefs, memories, relationships, goals,
roles, laws, property, businesses, inventory provenance, production, prices,
contracts, schedules, quests and causal history. Skyrim owns loaded physical
truth: rendering, animation, navmesh, collision, furniture occupancy, combat,
inventory objects, local crime response, UI and audio playback.

The bridge translates observed physical facts into idempotent Fork events and
allow-listed Fork commands into package/action execution with explicit
applied/rejected/obsolete receipts.

## See Also

- [OpenMW versus Skyrim Platform Decision](../architecture/platform-decision-openmw-vs-skyrim.md)
- [Fork Capability Baseline](../fork/capabilities.md)
- [OpenMW Capability Baseline](../openmw/capabilities.md)
- [Fork and OpenMW Capability Matrix](../architecture/capability-matrix.md)
