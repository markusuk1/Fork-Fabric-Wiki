# Daily NPC Routines and Terminal Repair

> Sources: ROUTINE-001 prior-art/implementation evidence, POP-002 exact lifecycle evidence and AUDIT-001 closure reconciliation, 2026-08-09 to 2026-08-10
> Raw: [Research](../../raw/architecture/2026-08-09-routine-001-scheduler-research.md), [Routine Evidence](../../raw/architecture/2026-08-09-routine-001-daily-scheduler-evidence.md), [POP-002 Exact Lifecycle Evidence](../../raw/architecture/2026-08-09-pop-002-exact-lifecycle-reconciliation-evidence.md), [Programme Closure Audit](../../raw/architecture/2026-08-10-audit-001-programme-closure-reconciliation.md)
> Commit: c0cea44
> Updated: 2026-08-10

## Capability

The ten embodied villagers now follow role-authored days selected from actual
OpenMW game time. Their schedule is not a set of independent Lua timers: one
validated manifest expands to 50 complete actor slots, and Fork owns the
current slot, reservation, command revision, interruption state, repair count
and causal transition history. OpenMW owns physical movement and reports what
actually happened.

This split makes time scaling, pause and save/load engine-correct while keeping
needs, future work output and cognitive interruptions transactionally coherent.
ROUTINE-001 was originally qualified on `game-openmw-npc-v11`. Current
consolidated production is `game-openmw-npc-v203`, which retains this routine
contract and its later population, dialogue and recovery consumers.

## Clock and schedule contract

- Each actor has exactly five contiguous half-open slots covering minute 0
  through 1,440 with no gap or overlap.
- OpenMW emits coarse observations from its game clock; server wall time never
  chooses an activity.
- Fork derives the active slot, evolves needs from elapsed game time, reserves
  an exclusive station and emits one revision-bound command atomically.
- Same-cell station targets come from the population/routine manifests. Visible
  actors are never teleported as normal schedule execution.
- OpenMW accepts only allow-listed Travel/Wander commands and acknowledges
  physical arrival before Fork closes the transition.

## Interruption and recovery

A higher-priority physical event pauses rather than destroys the scheduled
command. Fork records the interruption; OpenMW temporarily executes the event
behavior and then resumes the surviving routine revision. The witness proof
records three interruptions and three resumptions.

No-progress is measured in simulation time, avoiding false stalls while a menu
pauses the world. One deterministic repair resets and retries the station. If
progress still cannot be made, the actor enters a safe local Wander fallback;
there is no unbounded retry loop. LLM-001 now also permits a bounded validated
repair proposal after deterministic exhaustion, but ordinary correctness never
depends on a model.

Late messages are expected around slot changes, especially under accelerated
time. A validated receipt/interruption/stall for a superseded command is stored
in `routine_obsolete_event` and acknowledged as `obsolete`. It cannot mutate
the current routine and is exactly replay-safe.

## Persistence and outage behavior

Actor scripts serialize the active command, last receipt, interruption and
progress-watchdog state. Loading restores that local execution state and
reconciles it against Fork's current revision. A real OpenMW save/load restored
six loaded actors exactly, while Fork retained ten states/reservations. A real
standalone Fork restart retained routine and transport state.

If the companion is offline, loaded actors use bounded native Wander and local
game-clock observations. Reconnect uses protocol 6 to converge on Fork's
current commands; offline behavior does not become a second durable schedule.

## Proven boundary

The delivered ROUTINE-001 slice proves same-cell daily life, reservations, need evolution,
physical arrival, interruption/resumption, terminal repair, stale/late message
handling, save/load, companion outage and Fork restart. ROUTINE-001 itself does
not own economic output, general goal arbitration or model execution: those
downstream contracts are now delivered by ECONOMY-001, DECIDE-001 and LLM-001.
POP-002 now adds canonical household/work places, capacity,
exact lifecycle/census identity and inactive-only cross-cell materialization
with lineage/revision-bound receipts. Save/load, Fork restart and companion
outage/reconnect qualification pass. See
[Household Occupancy and Unloaded Actors](household-occupancy-and-unloaded-actors.md).

## See Also

- [Unified NPC Profile and State](unified-npc-profile-and-state.md)
- [Reliable OpenMW/Fork Bridge Protocol](reliable-bridge-protocol.md)
- [Fork–OpenMW Integration Architecture](integration-architecture.md)
- [Production Living-World Gap-Closure Programme](system-gap-closure-programme.md)
