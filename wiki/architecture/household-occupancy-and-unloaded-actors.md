# Household Occupancy and Unloaded Actors

> Sources: POP-002 prior-art research, foundation evidence, exact lifecycle/reconciliation evidence and SKYRIM-005 scale evidence, 2026-08-09 to 2026-08-10
> Raw: [Research](../../raw/architecture/2026-08-09-pop-002-household-occupancy-unloaded-actor-research.md), [Foundation Evidence](../../raw/architecture/2026-08-09-pop-002-household-runtime-evidence.md), [Exact Lifecycle Evidence](../../raw/architecture/2026-08-09-pop-002-exact-lifecycle-reconciliation-evidence.md), [SKYRIM-005 Scale Evidence](../../raw/skyrim/2026-08-10-skyrim-scale-proof.md)
> Commit: c0cea44
> Updated: 2026-08-10

## Released population model

The village now has one canonical eleven-person population rather than ten
unrelated spawned actors. Fifteen typed places include five homes, work and
social locations. Profiles, households, home/work membership, routine places
and capacities join fail closed before startup. Nara and offscreen Lysa share
the Velas household; the tradehouse workers share both staff housing and a
capacity-four social room.

Fork stores the revisioned manifest, one member/presence/occupancy row per
person and immutable presence transitions. Reducers reject divergent replay,
stale revisions, backward game time, impossible embodiment and over-capacity
movement. An isolated proof exercised a capacity-one collision and preserved
dynamic presence through an exact manifest upgrade.

## Desired and observed state

OpenMW remains the only physical authority. The population runtime scans the
engine's native active-actor list. Fork stores desired `population_presence`
separately from observed `population_embodiment`. Actor-local lifecycle events
bind one NPC to the exact OpenMW object/record, save lineage, source epoch,
cell and position. A one-time census rebuilds those bindings after each load.

A visible actor whose next station is in another cell keeps a bounded local
Wander and records a deferral; it is never silently removed in front of the
player. For an inactive mismatch, Fork issues one command bound to the NPC,
object, desired-presence revision, embodiment revision, lineage and epoch.
OpenMW moves only that inactive object, verifies the resulting cell/position
and sends an exact receipt. Wrong-object, stale-revision and unknown-lineage
messages fail closed.

The final accelerated day recorded 25 clock observations/arrivals, eight
activity kinds, four exact inactive reconciliation commands/receipts, ten
unique embodiments, 1.85 average CPU cores and no engine/Lua errors. This is
bounded schedule materialization, not full unloaded physics or navigation.

## Transactional off-screen scale

SKYRIM-005 extends the same canonical population tables with
`offscreen_population_clock`, immutable `offscreen_population_tick` rows and
one `advance_offscreen_population_clock` reducer. A tick validates world,
expected revision, strictly advancing game time, target-place ownership and
capacity before updating any actor. SpacetimeDB transaction semantics ensure
that either every selected non-embodied member advances or none do.

The isolated qualification seeded 200 real profiles and matching non-embodied
members. Sixty ticks advanced all 200 presences and occupancy reservations,
producing 12,000 transitions at 5.916 ms p95 and 10.068 ms maximum. Exact
replay was inert, divergent tick identity and stale time rejected, and the
whole public-table fingerprint was unchanged after an owned server restart.
This reducer is qualified in current module source; it has not been deployed
into the retained OpenMW production database during the Skyrim programme.

## Persistence and recovery

OpenMW save/load retained ten unique object bindings across 32 lifecycle
events. Six active routine/perception states round-tripped exactly. A real
standalone Fork stop/restart retained the exact presence, embodiment and
lifecycle rows. A forced post-commit companion crash then recovered ten
embodiments and 16 lifecycle events through 46 stable resends with zero
pending work, dead letters, engine errors or Lua errors.

Disposable VFS command projections are reset to empty at every launch and are
repopulated from the selected Fork database. This prevents a reused data
directory from executing commands belonging to an older database. Durable
OpenMW outbox/ACK state is separate and is not discarded.

## Production operation

`scripts/initialize-village-database.ps1` synchronizes profiles, population,
voice and routines plus the promoted player identity without consuming an old
OpenMW log or advancing the game clock. POP-002 was qualified on v183; current
consolidated production is v203. The ordinary desktop shortcut auto-resumes
Delantris against the current production database.

## Visual evidence

Two inspected OpenMW renderer previews show daytime Tradehouse work occupancy
and 23:00 staff-home occupancy. Both were generated without changing the
foreground window. The owner also independently observed three people upstairs
in the Tradehouse during the nighttime manual check. Capture tests use their
own 1280x720 profile; the playable explorer profile remains 3440x1440
borderless with GUI scale 1.50.

## See Also

- [Daily NPC Routines and Terminal Repair](daily-routines-and-terminal-repair.md)
- [Unified NPC Profile and State](unified-npc-profile-and-state.md)
- [Fork–OpenMW Integration Architecture](integration-architecture.md)
- [Production Living-World Gap-Closure Programme](system-gap-closure-programme.md)
