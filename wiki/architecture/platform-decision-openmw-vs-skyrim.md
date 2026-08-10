# OpenMW versus Skyrim Platform Decision

> Sources: PLATFORM-001 research plus delivered GAME-OPENMW completion evidence, collected 2026-08-10
> Raw: [PLATFORM-001 platform audit](../../raw/architecture/2026-08-10-openmw-skyrim-platform-audit.md); [SKYRIM-002 live humanoid bridge proof](../../raw/skyrim/2026-08-10-skyrim-live-humanoid-bridge-proof.md)
> Commit: 3098f74
> Updated: 2026-08-10

## Decision

Use a conditional `Combine` decision: validate Skyrim SE/AE+SKSE as the new
embodiment layer while preserving Fork as the sole simulation brain and durable
authority. Freeze further OpenMW-specific feature expansion during the spikes;
do not discard the proven OpenMW implementation.

Skyrim leads the directional weighted comparison 72.4 to 68.2 because visible
daily life is now a primary requirement and Skyrim already has native packages,
smart furniture, scenes, relationships, factions, ownership, crime and a large
action/animation ecosystem. The lead is deliberately treated as conditional:
OpenMW remains materially stronger in source control, deterministic testing,
runtime stability and the already-proven clean-world/Fork integration.

## Platform comparison

| Capability | OpenMW 0.51 | Skyrim SE/AE+SKSE | Consequence |
|---|---|---|---|
| Visible routine primitives | Travel/Wander plus custom Lua/animation composition | Native Eat, Sleep, Sandbox, Patrol, Guard, Use Item At and furniture | Skyrim removes a major custom executor programme |
| Physical interactions | Growing Lua animation API and several narrow mods | Havok behaviors, furniture, scenes, OAR/Pandora ecosystem | Skyrim has broader mature embodiment |
| Fork integration | Delivered companion/VFS protocol, deterministic and safe | Native SKSE C++ plugin is possible but unproven here | Skyrim must pass bridge/recovery spikes |
| Engine ownership | GPLv3 source and editor | Proprietary executable, reverse-engineered native interfaces | OpenMW can be fixed deeply; Skyrim must be wrapped defensively |
| World cleaning | Delivered dependency-safe three-master transformation | Hidden quest/alias/scene dependencies; generated override strategy required | Do not strip Skyrim globally first |
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
| S3 Embodied day | Three NPCs sleep, eat, work, trade and patrol across cells | Activity is visibly enacted without routine teleport fallback |
| S4 Recovery | Save/load, older-save rollback and all process restarts | Bridge restart passes; save/load, rollback and physical-effect recovery remain |
| S5 Scale | 20 embodied + 200 abstract actors | 5,000-command transport passes; embodied and abstract actor budgets remain |
| S6 Suppression | Reversible clean target region and system allowlist | No vanilla actor/quest leakage and no lost engine machinery |

If S2, S3 or S4 fails after bounded remediation, retain OpenMW and implement the
smallest missing OpenMW smart-object/activity executor informed by this audit.

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
