# Skyrim SE/AE + SKSE Capability Baseline

> Sources: official SKSE/runtime pages, Creation Kit reference and primary community repositories, collected 2026-08-10
> Raw: [PLATFORM-001 platform audit](../../raw/architecture/2026-08-10-openmw-skyrim-platform-audit.md)
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

- Mod Organizer 2 isolates the project profile and virtualizes game data.
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

