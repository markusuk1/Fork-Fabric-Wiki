# Unified NPC Profile and State

> Sources: PROFILE-001 prior-art research and implementation/runtime evidence, 2026-08-09
> Raw: Research, Evidence
> Commit: 6e7f7a9
> Updated: 2026-08-09

## Purpose and authority

PROFILE-001 replaces the village's separate witness and economy seeds with one
validated definition for every person. OpenMW remains authoritative for
physical actor records and mechanics. Fork is authoritative for stable
cognitive/social/economic attributes and changing needs and affect.

The canonical manifest contains ten embodied villagers and one declared
offscreen spouse. Each definition carries identity, role, race, class, sex,
household links, ten traits, six competencies, five values, five need
baselines, four affect baselines, useful item categories, starting money and a
bounded authoring summary. Population joins prevent role or identity drift;
Belyn Falas is consistently a boatwright.

## Durable model

Fork stores four related records:

| Record | Responsibility |
|---|---|
| `npc_profile` | Current immutable-definition revision and all authored fields/baselines |
| `npc_profile_revision` | Append-only manifest revision evidence |
| `npc_state` | Current needs/affect with monotonic revision and last cause |
| `npc_state_change` | Idempotent causal old/new transition history |

`npc_economy` is not a competing profile. Its role and useful categories are
projected from the profile; liquid money remains dynamic and survives profile
upgrades.

## Consistency rules

- New definitions begin at revision one; upgrades advance exactly once.
- Same-revision replay is a no-op only when every persisted field and both
  SHA-256 values agree. Divergence fails closed.
- Existing dynamic state and liquid money survive authored upgrades/resync.
- State mutations require the current revision, derive their idempotency
  signature inside Fork, clamp bounded values and retain complete old/new
  values plus cause.
- The companion validates all cross-record constraints first. Invalid startup
  remains visibly offline and exits without dispatching work.
- `profiles.json` is a bounded read-only projection, not another authority.

## Proven behavior

The profile contract passed six negative manifest/startup cases and an isolated
real Fork restart. Restart/resync retained the mutated state exactly once;
stale and divergent events failed, a sequential definition upgrade succeeded,
and a skipped revision failed. Production v9 published all 11 profile/state
rows. Witness, appearance, interaction, native commerce and reconnect
regressions passed in OpenMW with zero engine or Lua errors.

During qualification, two actors could converge on the incident and collision-
block the later arrival. The allow-listed local executor now treats 256 units
as a bounded group-confrontation arrival radius while flee destinations retain
their stricter 96-unit threshold.

## Boundary for later work

The model now supplies stable inputs and revisioned dynamic state, but does not
itself advance time or choose arbitrary activity. ROUTINE-001 owns game-time
need decay, schedules, interruptions and terminal repair. PERCEPT-001,
MEMORY-001 and DECIDE-001 own generalized evidence, epistemic lifecycle and
goal arbitration respectively.

## See Also

- [Profile-Driven NPC Cognition and Social Knowledge](profile-driven-npc-cognition.md)
- [Fork and OpenMW Capability Matrix](capability-matrix.md)
- [Production Living-World Gap-Closure Programme](system-gap-closure-programme.md)
- [Reliable OpenMW/Fork Bridge Protocol](reliable-bridge-protocol.md)
