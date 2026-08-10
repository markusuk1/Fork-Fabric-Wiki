# OpenMW Capability Baseline

> Sources: Official OpenMW 0.51.0 release, documentation, FAQ, OpenMW-CS manual, GitLab source composition, DISC-001 ecosystem survey and LLM-002 runtime evidence, collected 2026-08-09
> Raw: [OpenMW 0.51 Capability Inventory](../../raw/openmw/2026-08-07-openmw-0.51-capability-inventory.md), [Ecosystem Survey](../../raw/openmw/2026-08-07-openmw-ecosystem-prior-art-survey.md), [LLM-002 Evidence](../../raw/architecture/2026-08-09-llm-002-proactive-observation-evidence.md)
> Commit: 6e7f7a9
> Updated: 2026-08-09

## Overview

OpenMW 0.51.0 is the released player-facing engine baseline. It supplies the
world simulation, rendering, physics, navigation, actors, mechanics, content
loading/editing, audio, UI, scripting and saves that the Fork deliberately does
not. OpenMW is highly moddable, but its Lua sandbox cannot directly connect to
an external database. A companion-mediated file/log protocol is proven for a
prototype; a small native bridge is a later hardening option.

## Engine capability map

| Area | OpenMW 0.51 capability | Integration significance |
|---|---|---|
| Content | ESM/ESP/BSA/NIF compatibility plus `.omwgame`, `.omwaddon`, `.omwscripts` and prioritized data paths | Reuse legally supplied Morrowind content or build an independent total conversion |
| Authoring | OpenMW-CS record/world editor and verification tools | Primary world/content authoring environment |
| World | Cells, streaming, terrain, weather/time, doors, containers, projectiles and object lifecycle | Physical world authority |
| Rendering | OpenSceneGraph-based renderer, lighting/shadows, water, distant terrain, groundcover and post-processing | Player-visible presentation and GPU frame loop |
| Physics/navigation | Bullet collision/physics plus Recast/Detour navmesh and pathgrid compatibility | Validate and execute movement locally |
| Actors/mechanics | Stats, inventory, equipment, actor-local Hit/Died events, combat, magic, alchemy, enchanting, crime, barter and travel | Resolve authoritative gameplay effects and emit physical evidence |
| Narrative | Dialogue, topics, factions, disposition, journals and quest stages; API-129 player journal text access | Native quest journal plus physically authored books/notices; JOURNAL-002 imports new native text without replacing OpenMW ownership |
| AI execution | Combat, Pursue, Follow, Escort, Wander and Travel packages; optional direct actor controls | Actuation layer for higher-level plans |
| Scripting | Sandboxed Lua global/local/player/menu/load contexts, events, interfaces and hot reload | Gameplay adapter and mod-facing API |
| UI/input | Custom Lua widgets/windows, messages, camera and input hooks | Conversation, debug and operator-facing in-game surfaces |
| Persistence | Game saves, Lua save/load serialization and persistent storage | Local physical-world continuity |
| Media | OpenAL audio and FFmpeg-backed media support | Presentation endpoint for external voice/media artifacts |

## Stable extension surfaces

For the village/commerce/voice baseline, OpenMW 0.51 exposes project-created NPC
drafts with race, class, head, hair, sex, merchant service flags and base gold;
actor inventory and barter-gold access; player UI entry into native `Barter`
mode; and `core.sound.say` for an actor-attached voice file with subtitle and
normal voiced animation. These mechanics are preferable to reimplementing
buying/selling or audio spatialization in the bridge. Runtime dialogue record
mutation remains incomplete, so free-form conversation belongs in project Lua
UI plus validated Fork state.

DIALOGUE-001 proves that project Lua surface end to end: activation opens a
persistent bounded conversation window, forwards ordered choices, shows
grounded tone/context and recent turns, interrupts and resumes the exact actor
routine, opens native Barter only from a typed Fork action, and serializes the
active local session across save/load. Loading creates a new bridge source epoch
so restored event sequence cannot collide with activity emitted after the save.

LLM-001 extends the same window with free-text input, visible pending/timeout
state and explicit provider/model/lease provenance. OpenMW still treats the
response as presentation data: it applies only an allow-listed typed action,
returns an exact receipt and resumes the NPC routine. The active cognition
conversation survives save/load; no network or provider credential enters Lua.

LLM-002 proves actor-local proactive attention without remote frame control.
Local Lua samples at 0.5 seconds, detects radius entry with hysteresis and
measures same-cell, distance, collision-ray line of sight and signal. Before a
validated initiation is shown, it rechecks actor availability and the same
physical facts. Saved counters and handled-action IDs prevent reload duplicates;
the normal dialogue path owns interruption, UI and exact receipt.

VOICE-001 proves the native actor-attached audio surface under production
constraints. The launcher pre-indexes 32 allow-listed VFS WAV paths; actor-local
Lua starts, observes and stops `say` playback, binds the exact subtitle and
returns physical started/completed/interrupted/busy/failure receipts. One live
process decoded different audio from slot 00 after allocator wrap. Active speech
resumes safely after save/load without duplicating its durable start receipt.
OpenMW still owns only presentation; Fork text and mechanics remain
authoritative.

Lua is the correct layer for observing semantic game events and applying
high-level intentions. Global scripts can modify the wider world; local scripts
can control their attached actor; player scripts can own UI/input. The API also
exposes nearby actors and objects, navigation queries, collision/render rays,
content identities, quests, factions and many actor properties.

OpenMW 0.51 adds a Load context that can inject or modify supported records
after content loading. Runtime custom record creation exists for a growing but
explicitly limited set of types. Any design that relies on dynamic content must
verify the exact record type against the pinned API.

Built-in AI packages are the preferred actuator because they already integrate
pathfinding, animation and mechanics. Direct movement/attack controls are useful
for special behaviors but create more responsibility for collision, timing and
compatibility.

## Hard integration boundary

Each Lua script is sandboxed and has only the allowed standard and `openmw.*`
packages. It cannot load a DLL, open arbitrary files, access the OS or use a
documented HTTP/WebSocket client. VFS access is read-only. Consequently:

- a normal Lua mod cannot maintain a live SpacetimeDB connection;
- a companion can safely prototype indirect IPC by atomically supplying a
  bounded VFS-visible JSON file and following validated tagged log output;
- direct sockets or arbitrary file output still require an OpenMW source patch;
- authentication and reconnect logic should live outside Lua.

OpenMW has no general stable native plugin ABI. Avoid an engine fork until the
file/log prototype is measured; if native hardening is justified, pin the fork
and keep the patch deliberately small and upstream-friendly.

## Identity and state mapping

Use content-file provenance plus FormId/record ID for content-defined objects.
Do not assume a runtime object ID alone is a cross-save database key. Give
dynamically created actors/objects a project UUID, persist it in save/Lua state,
and map it to the active runtime object when a save is loaded.

OpenMW remains authoritative for the physical result. A requested Travel action
is not complete because it was sent; it is complete only after OpenMW applies
it and reports the observed outcome.

## Assets, licensing and multiplayer

OpenMW contains engine code and separately licensed bundled resources, not the
Morrowind game data. Morrowind content requires a legally owned copy. An
independent `.omwgame` total conversion with appropriately licensed assets is
another valid route. This is a product/legal input, not something the technical
bridge solves.

Mainline OpenMW does not provide multiplayer. Its Lua rules anticipate possible
multiplayer semantics, and TES3MP exists separately, but distributed player
authority is not part of the 0.51 baseline. Fork can become a durable authority
component without magically adding client replication, prediction, combat
reconciliation or multiplayer security to OpenMW.

## Version discipline

OpenMW 0.51.0 is the baseline as of this inventory. Official `master` already
identifies itself as 0.52.0 development. Recheck release notes, Lua API revision,
record support and save compatibility before changing the pinned engine version.

## See Also

- [OpenMW Display and Interface Scaling](display-and-interface.md)
- [Fork Capability Baseline](../fork/capabilities.md)
- [Fork and OpenMW Capability Matrix](../architecture/capability-matrix.md)
- [Integration Architecture](../architecture/integration-architecture.md)
- [OpenMW Ecosystem Reuse Matrix](../architecture/ecosystem-reuse-matrix.md)
