# Embodied Social Information Flow

> Sources: SOCIAL-001 prior-art research and implementation evidence, 2026-08-09
> Raw: [Research](../../raw/architecture/2026-08-09-social-001-embodied-information-flow-research.md), [Evidence](../../raw/architecture/2026-08-09-social-001-embodied-information-flow-evidence.md), [Save rollback correction](../../raw/architecture/2026-08-09-social-001-save-load-rollback-correction.md)
> Commit: 6e7f7a9
> Updated: 2026-08-09

## Overview

Social knowledge is no longer copied between NPC rows or released by a timer.
It travels as an embodied act: a speaker approaches an intended listener,
utters a typed claim, and every possible listener measures its own physical
hearing conditions. Fork decides what each listener actually learns from that
evidence and retains the full chain back to the original observation.

## Ownership and flow

1. A detected observation becomes an immutable memory and typed belief.
2. DECIDE-001 can select `uphold_law` or `preserve_safety`; only an applied
   `hide_then_report` receipt creates a root report.
3. Fork claims a fresh revision and the companion publishes a bounded snapshot.
4. The OpenMW speaker serializes the interruption with its routine, approaches
   the intended actor with native Travel, then emits the utterance.
5. Each active same-cell actor independently records distance, collision-ray
   visibility and signal. No caller supplies the final heard/not-heard result.
6. Fork recomputes intended/overheard eligibility, privacy, directed trust,
   attenuated confidence and deterministic distortion.
7. Only accepted hearing creates one immutable episode, one typed belief and a
   native graph `heard_from` relation. Rejections remain audits only.
8. After the full delivery batch, Fork may select one eligible new recipient
   for the next public retelling.

## Durable model

`social_message` retains root, parent and source episode IDs, speaker and
intended listener, structured claim fields, privacy, confidence, hop/max-hop,
game-time expiry, revision, attempt count and terminal reason. `social_dispatch`
binds one claim revision to transport. `social_delivery` retains physical
evidence and Fork's decision. `social_trust` is directional: listener trust in
speaker, not a symmetric relationship score.

Exact replay is a no-op. Reusing an ID with changed data fails. A failed
intended attempt moves the message to a new retry revision; attempts stop at
three. Messages expire after 240 game minutes. Propagation stops at hop 2,
visited/root-knower exhaustion or expiry. Stopping early is valid; the system
never invents a listener merely to fill the hop budget.

## Persistence and failure behavior

Each actor saves its queue, handled revision keys and active message alongside
the routine interruption. The global script saves handled dispatch IDs and the
completed runtime state. Loading an older OpenMW save does **not** assume its
social work is still current: every restored active or queued item waits while
the global script checks the companion's current Fork projection. An exact live
dispatch is re-authorized with fresh runtime object mappings; an absent terminal
dispatch is suppressed without repeating speech, listener measurements or the
player-journal entry. Invalid/unavailable projections fail closed and produce
no invented result. Companion post-commit crash and Fork process restart retain
the protocol-6 guarantees.

This reconciliation is required because an OpenMW save can predate an external
Fork commit. Reusing the old delivery ID with newly measured distance, signal
or game time is divergent replay and must remain rejected. Fresh baseline
runtime proofs therefore use a test-owned isolated Fork process and database,
not accumulated development state.

## Proven capability and boundary

Direct proof covers private rejection, genuine overhearing, trust, distortion,
typed memory, expiry, exact replay, retry and a full 0/1/2 chain. Real OpenMW
proof covers physical movement/utterance, intended and overheard listeners,
privacy rejects, native memory provenance, runtime budget and active save/load.
The corrected isolated rollback proof restored 10/10 actor states, delivered
both eligible messages, suppressed one terminal replay and ended with zero
dead letters or engine/Lua errors. The historical production database is
`game-openmw-npc-v45`; repeatable qualification uses a fresh isolated database.

The system is deterministic and content-grounded. General player journal topic
selection, contextual deterministic dialogue, leased LLM speech and voice
rendering are separate layers; none may bypass this delivery or receipt model.

## See Also

- [Normalized Physical and Social Perception](normalized-perception-and-attention.md)
- [Epistemic Memory and Bounded Recall](epistemic-memory-and-bounded-recall.md)
- [Explainable Goal and Intention Arbitration](explainable-goal-and-intention-arbitration.md)
- [Reliable OpenMW/Fork Bridge Protocol](reliable-bridge-protocol.md)
- [Production Living-World Gap-Closure Programme](system-gap-closure-programme.md)
