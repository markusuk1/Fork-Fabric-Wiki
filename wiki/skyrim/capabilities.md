# Skyrim SE/AE + SKSE Capability Baseline

> Sources: official SKSE/runtime pages, Creation Kit reference and primary community repositories, collected 2026-08-10
> Raw: [PLATFORM-001 platform audit](../../raw/architecture/2026-08-10-openmw-skyrim-platform-audit.md); [SKSE Steam-bootstrap observation](../../raw/skyrim/2026-08-10-skse-steam-bootstrap-observation.md); [live actor receipt](../../raw/skyrim/2026-08-10-skyrim-live-actor-receipt.md); [live humanoid bridge proof](../../raw/skyrim/2026-08-10-skyrim-live-humanoid-bridge-proof.md); [embodied-day prior art](../../raw/skyrim/2026-08-10-skyrim-embodied-day-prior-art.md); [embodied-day proof](../../raw/skyrim/2026-08-10-skyrim-embodied-day-proof.md); [recovery prior art](../../raw/skyrim/2026-08-10-skyrim-recovery-prior-art.md); [recovery proof](../../raw/skyrim/2026-08-10-skyrim-recovery-proof.md); [scale prior art](../../raw/skyrim/2026-08-10-skyrim-scale-prior-art.md); [scale proof](../../raw/skyrim/2026-08-10-skyrim-scale-proof.md); [modded-save confirmation prior art](../../raw/skyrim/2026-08-11-modded-save-confirmation-prior-art.md); [suppression proof](../../raw/skyrim/2026-08-11-skyrim-suppression-proof.md); [companion requirements](../../raw/skyrim/2026-08-11-frontier-companion-requirements.md); [companion prior art](../../raw/skyrim/2026-08-11-companion-prior-art.md); [idle-gather investigation](../../raw/skyrim/2026-08-11-companion-idle-gather-crash.md); [idle-gather correction proof](../../raw/skyrim/2026-08-11-companion-idle-gather-correction-proof.md); [F10 manual crash correction](../../raw/skyrim/2026-08-11-companion-f10-manual-crash.md)
> Companion correction raw: [ordinary dialogue production proof](../../raw/skyrim/2026-08-11-companion-ordinary-dialogue-production-proof.md)
> Commit: 3098f74
> Updated: 2026-08-11

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
zero SKSE error markers. Actor scale and regional suppression remain S5-S6.

Evidence: [embodied-day proof](../../raw/skyrim/2026-08-10-skyrim-embodied-day-proof.md).

SKYRIM-004 closes the recovery gate. The plug-in now reports a distinct runtime
session for each game process and advances a load epoch after every successful
load. SKSE lifecycle messages expose pre-load, post-load and save boundaries;
test-gated native named save/load uses Skyrim's existing save manager. Fork
authority and terminal receipts remain outside Skyrim saves.

In `recovery-live-05`, one named checkpoint and the original older Whiterun save
were loaded in process, then the older save was loaded again through a second
game process. The checkpoint restored the saved `02:00` clock; the older save
restored its independently observed `20.712118` baseline. Two old terminal
commands returned idempotent receipts without changing the rolled-back clock or
Lydia. New commands bound to authority revision, runtime session and load epoch
restored `02:00` plus Lydia's drawn weapon after rollback and restart.

Five independent worker processes proved a 1,500 ms outage and same-context
restart. The 41,554 ms run returned 27 applied terminals and six idempotent
duplicates with zero SKSE error markers. An independently fresh owned Fork
server restart retained the sampled transport, routine, population, perception,
memory, goal, journal, presentation and identity state without duplication.
SKSE co-save serialization remains available for Skyrim-local plug-in state,
but it is deliberately not the authority because it rolls back with the save.

Evidence: [recovery proof](../../raw/skyrim/2026-08-10-skyrim-recovery-proof.md).

SKYRIM-005 closes the bounded scale gate without conflating loaded physical
actors with unloaded semantic actors. A test-gated read-only CommonLib sample
uses `ProcessLists::ForEachHighActor` and Skyrim's own animation-frame timer;
it does not spawn or move actors. At native schedule hour 16, 100 samples over
35.459 seconds held 29-33 living, 3D-loaded playable-race NPCs and at least 29
valid native packages. Twenty-two actors changed cell, position, package or
furniture/sit-sleep state through native execution.

Engine frame p95/p99 was 16.667/16.667 ms. High-actor scan p95 was 68 us and
durable receipt p95 was 117.767 ms, with zero SKSE error markers. Separately,
an isolated owned Fork database advanced exactly 200 non-embodied canonical
population members through 60 transactional clock ticks and 12,000 immutable
transitions at 5.916 ms p95 and 10.068 ms maximum. Exact replay, divergent
identity, stale time and an unchanged whole-database fingerprint after server
restart passed. This qualifies the loaded/off-screen ownership split on the
observed machine; it is not a claim that 220 actors were rendered at once.

Evidence: [scale proof](../../raw/skyrim/2026-08-10-skyrim-scale-proof.md).

SKYRIM-006 closes the reversible target-region suppression gate and makes the
platform decision final. A fail-closed manifest classifies 163 placed Whiterun
actors and 31 relevant start-game quest dependencies: 26 narrative controllers
are suppressed and five engine/system quests are preserved. The generated
plugin retains 83 navmeshes, 177 doors, 524 furniture references, 585
containers and 216 activators.

The final enabled run loaded the unchanged Whiterun save, disabled all 163
actor references, disabled and explicitly stopped all 26 narrative quests,
observed all five preserved system quests running, and ended with zero loaded
humanoids. The disabled control loaded the identical save hash and exposed 31
humanoids with 31 valid native AI packages. Both phases exited through Skyrim's
own shutdown path, reported zero SKSE error markers, and restored the original
DLL, plugin list and `Skyrim.ini` hashes. The independent verifier passed the
two-phase evidence.

The blocking modded-save confirmation was removed by reusing the MIT-licensed
Achievements Mods Enabler `v1.2` only inside the isolated proof. Production
packaging must provision that or an equivalently qualified maintained solution;
the binary is not redistributed by this repository. The generated suppression
patch proves the approach for Whiterun, not an already-complete global clean
world. Expansion remains fail-closed and region-by-region.

Evidence: [suppression proof](../../raw/skyrim/2026-08-11-skyrim-suppression-proof.md).

COMPANION-001 adds an installed Lydia-first native companion slice without
clearing or replacing the existing world. It samples grounded player health,
combat, burden and idle state plus Lydia's capacity and nearby lawful targets;
provides persistent F10 tactics; safely transfers only ordinary eligible
items; consumes real medicine for close-range emergency care or truthfully
falls back to native guard behavior; and activates one bounded nearby loose or
harvestable target. Eight licensed authored barks have matching subtitles.

The plug-in builds with warnings as errors and its deterministic policy tests
pass. The owner's first ordinary idle playtest corrected the initial short
proof by reproducing a process termination at the second-60 resource scan.
COMPANION-002 replaced the global attached-cell/raw-pointer path with a capped
same-cell iterator that stages engine handles and validates them after the cell
lock and again before activation. `companion-idle-soak-03` then proved Lydia
form `666772`, licensed voice start, medicine-backed `140 -> 120 -> 140`
healing, physical `0 -> 1` pickup and 79 ordinary samples through idle second
78, with zero SKSE errors and clean process state. See
[Frontier Companion System](frontier-companion-system.md) for the precise
shipped/proven boundary.

The first owner F10 test then terminated Skyrim before any tactics receipt.
This withdraws the claimed playable tactics and burden-acceptance surface even
though observation and test-gated engine probes remain real. COMPANION-001 is
reopened until a replacement UI and every claimed manual path are proven.

The installed replacement removes the crashing message callback and uses a
task-queued non-modal key state. Run `companion-interaction-05` passed tactics
open/close, persistent policy roundtrip, F9 burden acceptance, resource
activation, healing and voice with zero SKSE errors. Physical owner F10 and one
selection remain required before the manual surface is accepted.

The exact current source rebuild passed native policy tests and DLL
`F1EE9DBA...D3D6D7` is installed in both runtime paths with eight voice files.
Skyrim is configured as 3440x1440 borderless-windowed. After an unattended proof
restored Skyrim and monopolized the desktop, all unattended companion runs are
fail-closed: `-NonIntrusiveCapture` is mandatory, the window is never restored,
and foreground acquisition aborts and cleans only the owned process. See the
[foreground lockout correction](../../raw/skyrim/2026-08-11-skyrim-foreground-lockout-correction.md).

The chronology above records superseded failures. The current authoritative
production run is `companion-production-full-27`: ordinary Lydia dialogue with
no forced project INFO exposed the visible top-level project entry; vanilla
root/policy selection reached TIF/Papyrus and persisted policy; native Yes moved a safe item; a
temporary Travel package visibly moved Lydia to a lawful loose target before
pickup/activation and follower return; real combat produced a Lydia-owned
healing-arrow hit, health delta and one consumed potion; and Skyrim-owned speech
was observed. It passed in `75,336 ms` with zero SKSE errors and zero leftover
game processes. That historical run did not establish the current owner-facing
voice claim. The owner has since accepted the physical natural-flora gather
(Purple Mountain Flower `0 -> 1`, natural crouch, HUD result and return) but
heard no autonomous bark. COMPANION-001 is therefore `failed` at audible voice;
`QSpeakingDone` is not audio proof. A missing bark dialogue-branch relationship
was found through native comparison, then built, verified and deployed for the
next owner check. The
[Lydia and reusable companion matrix](lydia-companion-capability-matrix.md)
separates these proven mechanisms from Fork systems still awaiting integration
and feature categories needing new native adapters.

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
  reconciliation. The Fork off-screen clock now proves a 200-actor
  transactional baseline, but Skyrim still does not provide unloaded physics.
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
