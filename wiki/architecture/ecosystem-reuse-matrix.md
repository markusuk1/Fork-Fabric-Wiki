# OpenMW Ecosystem Reuse Matrix

> Sources: OpenMW ecosystem prior-art survey and disposable-install product decision, 2026-08-07
> Raw: OpenMW Ecosystem Prior-Art Survey, Disposable Source Install Decision
> Commit: 6e7f7a9
> Updated: 2026-08-07

## Overview

The project does not need to invent a complete content, scripting, AI, or asset
toolchain. OpenMW and its ecosystem supply most of the pieces. The missing work
is concentrated in two thin project-specific layers: a declarative world-shell
transformer and a Fork-owned bridge protocol.

## Selected stack

| Need | Selected existing capability | Project-specific addition | Decision |
|---|---|---|---|
| Preserve the owned Morrowind world | Local `Morrowind.esm`/BSA data, OpenMW profile, OpenMW-CS | Generated replacement game/master; reinstall is acceptable recovery | Configure |
| Remove legacy actors and narrative | DeltaPlugin YAML/filter/diff; `esmtool` inventory; OpenMW-CS Verify | Declarative keep/remove manifest and dependency checks | Combine |
| Clean-room standalone fixtures | OpenMW Game Template and Example Suite | Project test cases | Reuse |
| In-engine logic and presentation | OpenMW 0.51 Lua, UI, events, AI packages, storage, hot reload | Stable project interfaces and bounded actuators | Reuse |
| Living NPC behavior | Procedural Chatter and Lua NPC Schedule patterns | Fork-authored schedules, policies, and compatibility data | Adapt |
| Village population | Retained cells/interiors/pathgrids plus OpenMW NPC creation and AI packages | Stable spatial-anchor manifest, authored profiles and schedule reservations | Combine |
| Merchant/shop and supply | Retained Arrille's Tradehouse, OpenMW services, inventory, barter gold, native Barter UI and AI Travel | Fork production/lot/order/balance/scarcity ledger, schema-11 receipts and bounded delivery controller | Combine |
| First external brain bridge | Zdo RPG AI's VFS-file input and tagged-log output protocol | Fork session lineage, schemas, acks, backpressure, and security | Adapt |
| Durable memory/narrative/work | Fork Memory, Graph, CMT, Context, Fabric, Perception | Game-specific reducers and workers | Reuse |
| Voice pipeline | OpenMW spatial `say`, Mantella decomposition, ElevenLabs/Deepgram cloud APIs and Piper local TTS | Fork speech intents, provider adapter, cache, consent/provenance policy and bounded VFS playback slots | Combine |
| New project-owned 3D assets | Blender, OpenMW COLLADA exporter, Example Suite examples | Source-asset conventions and validation | Reuse |
| Legacy local NIF work | Morrowind Blender Plugin, NifSkope, Blender NifTools | Provenance ledger and non-distributable asset boundary | Reuse |
| Content/asset validation | `esmtool`, `niftest`, OpenMW-CS Verify, bullet object tool, navmesh tool | Repeatable validation workflow and evidence capture | Combine |
| Multiplayer | TES3MP design and server-side Lua experience | Separate authority, replication, prediction, and rebase program | Defer |

## World-shell decision

There are no saves or installed-game state to preserve, and reinstalling the GOG
copy is acceptable. Optimize for the cleanest complete shell. Start with a
project-owned replacement game/master generated from the local `Morrowind.esm`
rather than requiring an overlay to suppress inherited content. The repository
stores only transformation policy and tooling; generated content is local-only.

SHELL-001 passed the deliberately small acceptance proof: it removed one NPC
record/reference and the functional quest/dialogue trigger closure while Cell,
Landscape, and Static totals remained unchanged. OpenMW loaded the affected
Seyda Neen exterior and neighboring cells. The next expansion must first handle
nested dialogue INFO chains, actor selectors, ownership fields, named interiors,
and arbitrary cross-record dependencies safely.

An overlay addon remains available as a diagnostic or compatibility experiment,
but is no longer the preferred shell solely to protect the base installation.
The generated `.omwgame` or transformed ESM remains a non-distributable
derivative. The official blank Game Template is still valuable for tests and
clean-room content, but it cannot inherit Vvardenfell because an `.omwgame`
cannot depend on another game.

## Bridge decision corrected by prior art

OpenMW Lua remains sandboxed: it cannot open sockets, call Fork, or write
arbitrary OS files. However, Zdo RPG AI proves that a companion can communicate
without patching the engine:

```text
Fork / companion
    |
    | atomic JSON input file in an OpenMW data directory
    v
OpenMW VFS -> global/player Lua scripts -> tagged JSON print lines
    ^                                             |
    |                                             v
    +------------- companion follows OpenMW.log --+
```

The project should prototype this seam first with session IDs, monotonic
sequences, acknowledgments, idempotency, bounded files, atomic replacement,
schema versions, and save-lineage checks. A native OpenMW bridge is now a
conditional hardening step, not a prerequisite. Promote to it only if observed
latency, reliability, security, portability, or log-coexistence failures justify
maintaining an engine fork.

Fork replaces Zdo's separate durable memory/server. OpenMW sends observations
and receives bounded intentions; Fork remains the sole cognitive, social,
causal, context, work, and narrative authority.

## Scripting strategy

- Use official `.omwscripts` packages and the Example Suite as the baseline.
- Keep project interfaces versioned so gameplay scripts do not depend directly
  on transport details.
- Use global scripts for world-level observation and actuation, player scripts
  for UI/input, and local scripts for actor-local behavior.
- Use `reloadlua` and the Lua console during development; do not pack scripts
  into archives until iteration no longer needs hot reload.
- Treat the 0.51 load context as useful but work in progress. Its supported
  record types do not cover NPC/dialogue/cell stripping.
- Adapt schedule, reservation, cross-cell travel, activity, subtitle, and JSON
  library patterns from current living-world mods; keep the actual durable
  schedule and relationship truth in Fork.

## Asset strategy

Prefer project-owned source assets and the official Blender-to-COLLADA path for
new static and animated models. Use the Morrowind Blender Plugin and NifSkope
for inspection or modification of locally licensed legacy NIFs. Every imported
asset needs recorded source, license/permission, allowed uses, modifications,
and whether it may be distributed.

OpenMW-CS owns record, cell, terrain, and placement authoring. Its Verify report
joins the installed command-line tools in the content gate:

1. inspect archives and records with `bsatool`/`esmtool`;
2. validate NIF inputs with `niftest` and inspect them with NifSkope;
3. verify content records in OpenMW-CS;
4. generate/update collision and navmesh caches where relevant;
5. run targeted startup and cell-traversal tests;
6. retain logs, hashes, and exact profile/content order as evidence.

## Explicit non-selections

- Do not optimize around preserving the installed game or save compatibility;
  reinstalling the GOG copy is acceptable. Prefer reproducible transformation
  over unrecorded manual edits.
- Do not adopt the official blank template as the Vvardenfell shell.
- Do not use 0.51 load scripts as a substitute for placed-reference and
  dialogue transformation.
- Do not import Mantella or the archived MWSE-only Morrowind AI mod as the game
  integration layer.
- Do not place TES3MP on the single-player bridge critical path; its released
  base is OpenMW 0.47 and multiplayer needs a separate authority design.
- Do not allow a living-world mod or model worker to become a second durable
  memory, schedule, quest, or relationship authority beside Fork.

## Next proofs

1. `SHELL-002`: extend the proved replacement-master pipeline with dependency
   inventory and safe nested/cross-record rewriting, then expand toward the full
   legacy actor and narrative strip.
2. `BRIDGE-001`: prove one remembered OpenMW event through hardened file/log IPC
   before considering native engine work.
3. Establish an asset provenance manifest before importing any third-party or
   generated asset into distributable project content.
4. Populate Seyda Neen through named spatial anchors and prove the retained
   tradehouse with a native project-owned merchant before adding LLM speech.

## See Also

- [Fork and OpenMW Capability Matrix](capability-matrix.md)
- [Forkâ€“OpenMW Integration Architecture](integration-architecture.md)
- [OpenMW Capability Baseline](../openmw/capabilities.md)
- [Living Village, Commerce, Conversation and Voice](living-village-commerce-dialogue-voice.md)
- [Causal Supply Chain and Scarcity](causal-supply-chain-and-scarcity.md)
