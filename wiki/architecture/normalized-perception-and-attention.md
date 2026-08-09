# Normalized Physical and Social Perception

> Sources: PERCEPT-001 official-surface research and implementation/runtime evidence, 2026-08-09
> Raw: Prior-art research, Implementation evidence
> Commit: 6e7f7a9
> Updated: 2026-08-09

## Contract

Perception is observer-specific evidence, not omniscient world state. OpenMW
creates a shared physical or social stimulus, and every embodied project actor
independently measures whether it is in the same cell, its distance, collision-
ray visibility or auditory signal. Fork retains both accepted and rejected
observer results so later cognition can distinguish ignorance from absence.

The normalized kinds are `attack`, `death`, `crime`, `stance` and `speech`.
Visual kinds require visual provenance; speech is auditory. Every row carries a
stable stimulus/observer-derived identity, subject/object, game time, cell,
modality, geometry evidence, signal, salience, provenance, source reference and
detail.

## Ownership and flow

```text
OpenMW actor-local source
  Hit / Died / stance / native crime result / embodied speech
        -> shared stimulus fan-out
        -> each actor: cell + distance + ray/range
        -> schema-8 observer candidate with focus/safe action coordinates
        -> protocol-6 outbox / companion journal
        -> Fork stimulus + observer appraisal transaction
        -> current perception + immutable result
```

OpenMW owns physical truth and does not accept remote frame-time sensing. Fork
uses the observer's profile perception, curiosity and reasoning plus dynamic
rest/fear, signal and salience to derive bounded attention and confidence.
Detection never erases rejected evidence.

API 129 supplies actor-local combat callbacks and Died, stance polling, rays and
native crime operations. It does not provide one universal callback for every
legacy offense, so the project records a crime stimulus only when its own
native `Crime.commitCrime` operation returns witnessed evidence.

## Identity, replay and bounds

`perception:{stimulus_id}:{observer_id}` is the only valid observation key. One
stimulus may have many observer rows but is stored once. Exact input replay is
a no-op; divergent identity reuse and impossible schema combinations fail.
Unknown or non-embodied observers cannot enter the table.

Each actor keeps a save-serialized, bounded 128-stimulus handled cache. The
global protocol state saves its stimulus counter and stance fingerprint. The
OpenMW outbox remains bounded at 256 messages and protocol 6 provides durable
retry, acknowledgement and exact accepted effects.

## Proven behavior

The original real OpenMW run produced all five kinds from actual stance, speech, Hit,
native witnessed Assault, lethal Hit and Died surfaces. Ten actors generated 80
observer candidates: 36 detected and 44 rejected, with zero engine/Lua errors
and 2.231 average CPU cores. Direct invalid-input tests, six-actor OpenMW
save/load, standalone Fork restart/replay and ten-observer companion outage /
post-commit recovery all pass. The current v24 production run retained the
memory proof and added 20 atomic threat arbitrations over 36 detected episodes,
with 100 candidate audits and zero engine/Lua/dead-letter errors.

The outage test also corrected transport comparison: a resend is compared by
its canonical tagged envelope, never by the surrounding log timestamp.

## Boundary and next dependency

This system records what each actor could attend to. MEMORY-001 consumes that
boundary and provides immutable episodes, typed belief revision,
non-destructive maintenance and bounded recall. DECIDE-001 now atomically
arbitrates attack/death/crime evidence over that bounded context; stance and
speech remain observational. Perception and memory alone never authorize a
physical action: revision-bound dispatch is the sole projection boundary.

## See Also

- [Fork and OpenMW Capability Matrix](capability-matrix.md)
- [Forkâ€“OpenMW Integration Architecture](integration-architecture.md)
- [Reliable OpenMW/Fork Bridge Protocol](reliable-bridge-protocol.md)
- [Unified NPC Profile and State](unified-npc-profile-and-state.md)
- [Production Living-World Gap-Closure Programme](system-gap-closure-programme.md)
- [Epistemic Memory and Bounded Recall](epistemic-memory-and-bounded-recall.md)
- [Explainable Goal and Intention Arbitration](explainable-goal-and-intention-arbitration.md)
