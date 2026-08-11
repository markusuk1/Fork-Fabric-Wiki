# OpenMW versus Skyrim Platform Decision

> Sources: PLATFORM-001 research plus delivered GAME-OPENMW completion evidence, collected 2026-08-10
> Raw: [PLATFORM-001 platform audit](../../raw/architecture/2026-08-10-openmw-skyrim-platform-audit.md); [SKYRIM-002 live humanoid bridge proof](../../raw/skyrim/2026-08-10-skyrim-live-humanoid-bridge-proof.md); [SKYRIM-003 embodied-day proof](../../raw/skyrim/2026-08-10-skyrim-embodied-day-proof.md); [SKYRIM-004 recovery proof](../../raw/skyrim/2026-08-10-skyrim-recovery-proof.md); [SKYRIM-005 scale proof](../../raw/skyrim/2026-08-10-skyrim-scale-proof.md); [SKYRIM-006 suppression proof](../../raw/skyrim/2026-08-11-skyrim-suppression-proof.md)
> Commit: 3098f74
> Updated: 2026-08-11

## Decision

Use the now-qualified `Combine` decision: Skyrim SE/AE+SKSE is the physical
executor for the next build, while Fork remains the sole simulation brain and
durable authority. S1-S6 all pass. Preserve the proven OpenMW implementation as
a fallback and behavioral reference, but do not continue OpenMW-specific
feature expansion on the selected product path.

Skyrim leads the directional weighted comparison 72.4 to 68.2 because visible
daily life is now a primary requirement and Skyrim already has native packages,
smart furniture, scenes, relationships, factions, ownership, crime and a large
action/animation ecosystem. OpenMW remains materially stronger in source
control, deterministic testing and runtime stability. Skyrim's earlier
clean-world uncertainty is now bounded by an independently verified Whiterun
suppression/control pair; whole-world expansion is still required and must
retain the same fail-closed method.

## Platform comparison

| Capability | OpenMW 0.51 | Skyrim SE/AE+SKSE | Consequence |
|---|---|---|---|
| Visible routine primitives | Travel/Wander plus custom Lua/animation composition | Native Eat, Sleep, Sandbox, Patrol, Guard, Use Item At and furniture | Skyrim removes a major custom executor programme |
| Physical interactions | Growing Lua animation API and several narrow mods | Havok behaviors, furniture, scenes, OAR/Pandora ecosystem | Skyrim has broader mature embodiment |
| Fork integration | Delivered companion/VFS protocol, deterministic and safe | Native receipt-bound SKSE/CommonLib bridge passes command, replay, rollback, restart and scale gates | Skyrim bridge is qualified; productionize the test-gated surfaces |
| Engine ownership | GPLv3 source and editor | Proprietary executable, reverse-engineered native interfaces | OpenMW can be fixed deeply; Skyrim must be wrapped defensively |
| World cleaning | Delivered dependency-safe three-master transformation | Generated Whiterun override passes 163-actor/26-quest suppression and 31-actor restoration control | Expand region by region; do not strip globally without the same audit |
| Tools | OpenMW-CS, DeltaPlugin, Lua | CK, xEdit, Mutagen/Synthesis, Spriggit, MO2, SKSE/CommonLib | Skyrim has the stronger content/tool ecosystem |
| Simulation mods | Smaller, fragmented and often overlapping | Large ecosystem across AI, economy, survival, factions and building | Reuse patterns selectively; Fork remains authority |
| Stability | Project can pin/build engine source | Game updates can break native DLLs | Immutable runtime bill of materials is mandatory |
| Testing | Strong headless/source-level potential and existing harness | GUI/native closed-engine tests are harder | Add record-level and native unit tests, then mandatory in-game proof |
| Distribution | Engine redistributable; assets are not | Game, CK and mod terms constrain distribution; SKSE cannot be rehosted | Ship project code/plugins only and require owned game/runtime |

## Weighted matrix

| Criterion | Weight | OpenMW | Skyrim+SKSE |
|---|---:|---:|---:|
| Native visible NPC routine semantics | 15 | 2 | 5 |
| Physical action/animation substrate | 10 | 2 | 5 |
| Reusable simulation ecosystem | 12 | 2 | 5 |
| Native extension and Fork bridge potential | 10 | 3 | 5 |
| Content authoring and patch tooling | 8 | 3 | 5 |
| Engine source/control | 12 | 5 | 1 |
| Deterministic automated testing | 8 | 5 | 2 |
| Runtime/version stability | 6 | 5 | 2 |
| Clean-world transformation/isolation | 6 | 5 | 2 |
| Whole-world abstract simulation fit | 8 | 4 | 3 |
| Legal/distribution flexibility | 3 | 4 | 3 |
| Existing project investment | 2 | 5 | 1 |
| Weighted total / 100 | 100 | 68.2 | 72.4 |

Scores are 1-5 directional judgments. They make the tradeoff explicit; they are
not claimed benchmark measurements.

## First combined build: living-village minimum

The first build is a product contract, not another technology demo:

| System | Minimum observable result |
|---|---|
| Population | 12-20 varied project NPCs with homes, roles and relationships |
| Routines | Actors visibly sleep, eat, work, trade, patrol and socialize at real locations |
| Economy | One finite production chain with wages, lots, delivery, scarcity and prices |
| Property/business | One household and one owned/operated shop with rent or income flow |
| Hierarchy | Mayor/reeve, guard captain, merchant, skilled workers and laborers enforce permissions/orders |
| Survival | Hunger, fatigue and cold affect the player and selected NPC behavior |
| Building | One material-consuming upgrade visibly changes the settlement |
| Knowledge | Witnessed/heard/read facts enter the journal and can be disclosed in conversation |
| Cognition | Two key NPCs support bounded LLM step-in; everyone remains playable deterministically |
| Scale | 20 embodied actors coexist with at least 200 abstract Fork actors |
| Recovery | Save/load, rollback, Fork/worker/game restart and outages do not duplicate effects |

## Authority boundary

```text
Fork authoritative state
  profiles, beliefs, memory, goals, schedules, roles, property,
  businesses, production, prices, contracts, quests, causal ledger
                         |
                         | validated command / physical receipt
                         v
Thin SKSE bridge and Papyrus adapters
                         |
                         v
Skyrim physical state
  actors, packages, navmesh, furniture, inventory objects, combat,
  crime response, animation, rendering, UI and audio
```

LLMs propose dialogue or high-level intent only inside a bounded lease. Fork
validates policy and current state; Skyrim validates physical preconditions.
No model, Papyrus script or community mod may mutate canonical state directly.

## Clean-world approach

The goal remains a world with Bethesda terrain/buildings/objects and project
characters/narrative, but the implementation must be reversible:

1. Work in an isolated MO2 profile against a pinned runtime.
2. Start in a project-owned test cell/worldspace.
3. Generate an override plugin that disables placed vanilla actors and
   narrative starts; never edit Skyrim.esm in place.
4. Maintain an explicit allowlist for engine/system quests and services.
5. Prove a target region before expanding to the whole world.
6. Store plugins as Spriggit text and reproduce/audit binaries from source.

This preserves world geometry, navmesh, furniture, doors, containers, crafting,
weather, calendar, combat and other useful machinery while avoiding a fragile
one-shot deletion of interconnected records.

## Validation sequence

| Spike | Proof | Go gate |
|---|---|---|
| S1 Runtime/toolchain | Pinned runtime, SKSE stack and reproducible text-to-plugin build | Runtime and isolated MO2 VFS launches pass; official Address Library provenance and generated plugin build remain |
| S2 Fork bridge | Identity/time/package/equipment observation plus one receipt-bound command | **Pass:** Lydia shared the player cell; draw/restore, replay/conflict/expiry/target rejection and post-restart no-duplicate-effect receipts pass with zero SKSE errors |
| S3 Embodied day | Three NPCs cover home/sleep, work/trade, patrol and social packages across cells | **Pass:** exact authored records, seven live cells, >1,100 comparable same-cell units per actor, furniture/sit-sleep state, native interruption/recovery, invalid-destination retention and guarded UWQHD capture pass with zero routine-actor teleports |
| S4 Recovery | Save/load, older-save rollback and all process restarts | **Pass:** named save/load, genuine older-save rollback, immutable old terminals, context-bound repair, worker outage/restart, two game processes and fresh owned Fork restart pass with zero SKSE errors |
| S5 Scale | 20 embodied + 200 abstract actors | **Pass:** 100 samples held 29-33 loaded humanoids with at least 29 native packages and 22 progressing actors at 16.667 ms frame p95/p99; separately, 200 Fork actors completed 60 atomic ticks/12,000 updates at 5.916 ms p95 with replay, stale/divergent and restart-fingerprint safety |
| S6 Suppression | Reversible clean target region and system allowlist | **Pass:** 163 actors disabled, 26 narrative quests disabled/stopped, five system quests preserved, zero loaded actors enabled versus 31 actors/31 packages restored on the identical save; two clean exits and exact restoration |

All six gates pass. The remaining work is product implementation: expand the
suppression manifest beyond Whiterun, provision the pinned runtime/profile,
replace test-only migration actions with a production bootstrap/reconciliation
adapter, and build the first Fork-owned population and content slice.

## Reuse policy

- Reuse permissively licensed tooling: xEdit, Mutagen, Synthesis, Spriggit,
  MO2, CommonLibSSE-NG, Papyrus Extender and Pandora as applicable.
- Configure native Skyrim packages, furniture, scenes, ownership, crime,
  barter, Survival Mode and Hearthfire patterns.
- Adapt ideas from AI Overhaul, Trade Routes, SunHelm, LandLord and similar
  systems without importing restricted code/assets.
- Treat SkyrimNet, SeverActions and IntelEngine as high-value feasibility and
  interface references until licensing, source completeness, state ownership
  and measured behavior are acceptable.
- Do not stack competing memory, autonomy, economy or persistence authorities.

## See Also

- [Skyrim SE/AE + SKSE Capability Baseline](../skyrim/capabilities.md)
- [OpenMW Capability Baseline](../openmw/capabilities.md)
- [Fork Capability Baseline](../fork/capabilities.md)
- [Fork and OpenMW Capability Matrix](capability-matrix.md)
- [OpenMW Ecosystem Reuse Matrix](ecosystem-reuse-matrix.md)
