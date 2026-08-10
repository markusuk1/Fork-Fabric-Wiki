# Remembering Villager Vertical Slice

> Sources: BRIDGE-001 prior-art and implementation evidence, plus BRIDGE-002 greeting correction, 2026-08-07
> Raw: [BRIDGE-001 Combine Decision](../../raw/architecture/2026-08-07-bridge-001-one-remembering-villager.md), [BRIDGE-001 Implementation Evidence](../../raw/architecture/2026-08-07-bridge-001-implementation-evidence.md), [Greeting Fault Diagnosis](../../raw/openmw/2026-08-07-bridge-002-greeting-fault-diagnosis.md), [Greeting Runtime Verification](../../raw/openmw/2026-08-07-bridge-002-greeting-runtime-verification.md)
> Commit: c0cea44
> Updated: 2026-08-08

## Delivered capability

The clean Seyda Neen explorer now contains Ralen Varo, the first project-owned
villager. OpenMW creates and dresses him at runtime, places him near the player,
runs a native bounded Wander routine and intercepts activation into
project-owned text. One activation becomes a semantic meeting event, Fork
persists it, and a later activation can visibly recall the prior meeting count.

This proves embodiment, semantic perception, durable state, restart projection
and safe brain-offline play without an LLM or OpenMW engine patch.

The clean masters also supply one logical empty `Hello` voice-dialogue
container. It is engine plumbing, not authored dialogue: without it, native
ambient greeting evaluation throws once per frame when Ralen sees the player.

## Runtime flow

```text
activate Ralen
  -> OpenMW player message + tagged semantic JSON in openmw.log
  -> companion validates event and derives log-coordinate event ID
  -> Fork observe_meeting reducer inserts observation once
  -> villager_memory meeting_count increments once
  -> companion SQL read + atomic recall.json projection
  -> next activation/restart displays recalled count
```

The launcher includes the Lua package and generated projection directory in the
VFS. It starts one hidden companion guarded by a named mutex. Before each start
it removes the previous status lease. `-NoCompanion` tests the offline branch.

## Ownership and identities

OpenMW owns Ralen's runtime object, cell, transform, AI package and message.
Fork owns the durable observation and meeting count. The companion owns protocol
validation, database transport, log cursor and atomic VFS projection.

| Scope | Stable identity |
|---|---|
| World | `openmw:clean-seyda-neen-v1` |
| Villager | `npc:ralen-varo` |
| Explorer | `player:explorer` |
| Local Fork database | `game-openmw-village-v1` |

Runtime-generated OpenMW IDs are not durable domain keys.

## Data contracts

Outbound events are schema version 1, limited to `player_met_villager`, bounded
to 2,048 characters, and carry world/session/sequence/NPC/player identity. The
companion event ID is SHA-256 over log creation time, byte offset and the tagged
line. Re-reading one coordinate is idempotent; another coordinate is distinct.

Fork exposes two public tables:

- `observation`: immutable event receipt, semantic identity, sequence, payload
  and Fork timestamp;
- `villager_memory`: one row per villager with player/world binding, first/last
  seen, meeting count, last event and deterministic summary.

The VFS projection contains only bounded recall and status state. Lua remains
read-only and holds no database credential.

## Failure behavior

- Duplicate delivery: reducer succeeds without another count increment.
- Companion/Fork unavailable at launch: status lease is absent; Ralen wanders
  and activation shows an offline response using cached count if available.
- Ordinary OpenMW log lines are ignored.
- Invalid schema, identity, type, sequence or oversized event is rejected.
- Partial projection write never becomes the visible VFS path.
- Missing native greeting substrate: prevented at build time by the exact empty
  `Dialogue::Hello` replacement and caught by the 25-second encounter test.

The current cursor follows one configured log and detects truncation/recreation.
Before multi-save or multiplayer work, add save lineage, source epochs,
acknowledgements and a bounded durable outbox.

## Operation

Build the module with:

```text
powershell -ExecutionPolicy Bypass -File scripts/build-village-bridge.ps1
```

`-Publish` is for a fresh versioned local database. The proof module is already
published as `game-openmw-village-v1`; anonymous publication does not retain an
owner credential for later in-place schema changes.

Start through `OpenMW - Empty Seyda Neen`. Walk up to Ralen and activate him.
The first response introduces him; after projection, another activation or a
later launch recalls the meeting count.

Before accepting a change to generated content or NPC construction, run:

```text
powershell -NoProfile -ExecutionPolicy Bypass -File scripts\test-village-runtime.ps1 -DurationSeconds 25
```

The check must see exactly one Ralen creation and zero missing-Hello,
engine/fatal, or Lua errors after the normal greeting delay.

## Subsequent delivered evolution

`NPC-001` delivered the next deterministic intention proof with three witness
profiles, native Travel execution, destination receipts and explicit social
knowledge transfer. The originally deferred typed cognition leases, broader
routine lifecycle and versioned reconnect hardening were subsequently delivered
by LLM-001/002, ROUTINE-001 and BRIDGE-003. See [Profile-Driven NPC Cognition and
Social Knowledge](profile-driven-npc-cognition.md), [Bounded Key-NPC Cognition
Leases](bounded-key-npc-cognition-leases.md), [Daily NPC Routines and Terminal
Repair](daily-routines-and-terminal-repair.md) and [Reliable OpenMW/Fork Bridge
Protocol](reliable-bridge-protocol.md).
