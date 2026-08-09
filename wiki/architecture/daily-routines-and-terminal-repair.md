# Daily NPC Routines and Terminal Repair

> Sources: ROUTINE-001 prior-art research and implementation/runtime evidence, 2026-08-09
> Raw: Research, Evidence
> Commit: 6e7f7a9
> Updated: 2026-08-09

## Capability

The ten embodied villagers now follow role-authored days selected from actual
OpenMW game time. Their schedule is not a set of independent Lua timers: one
validated manifest expands to 50 complete actor slots, and Fork owns the
current slot, reservation, command revision, interruption state, repair count
and causal transition history. OpenMW owns physical movement and reports what
actually happened.

This split makes time scaling, pause and save/load engine-correct while keeping
needs, future work output and cognitive interruptions transactionally coherent.
The production target is `game-openmw-npc-v11`.

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
there is no unbounded retry loop. LLM-001 may later propose a validated repair,
but ordinary correctness never depends on a model.

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

The delivered slice proves same-cell daily life, reservations, need evolution,
physical arrival, interruption/resumption, terminal repair, stale/late message
handling, save/load, companion outage and Fork restart. It does not yet produce
economic output, simulate unloaded cross-cell travel, arbitrate general goals
or use an LLM. Those boundaries belong to ECONOMY-001, POP-002, DECIDE-001 and
LLM-001 respectively.

## See Also

- [Unified NPC Profile and State](unified-npc-profile-and-state.md)
- [Reliable OpenMW/Fork Bridge Protocol](reliable-bridge-protocol.md)
- [Forkâ€“OpenMW Integration Architecture](integration-architecture.md)
- [Production Living-World Gap-Closure Programme](system-gap-closure-programme.md)
