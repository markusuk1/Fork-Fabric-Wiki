# Epistemic Memory and Bounded Recall

> Sources: MEMORY-001 prior-art research and implementation/runtime evidence plus AUDIT-001 closure reconciliation, 2026-08-09 to 2026-08-10
> Raw: [Prior-art research](../../raw/architecture/2026-08-09-memory-001-epistemic-lifecycle-research.md), [Implementation evidence](../../raw/architecture/2026-08-09-memory-001-epistemic-lifecycle-evidence.md), [Programme Closure Audit](../../raw/architecture/2026-08-10-audit-001-programme-closure-reconciliation.md)
> Commit: c0cea44
> Updated: 2026-08-10

## Contract

Memory is observer-owned, provenance-bearing evidence, not a mutable prose
summary and not omniscient world truth. Only a detected normalized observation
can become an episode. Rejected perception remains an intake audit so the
system can distinguish ignorance from absence without inventing knowledge.

Every detected result atomically maps to one immutable domain episode and one
Fork-native memory cell. Native `remember` must produce its vector member,
temporal version and CMT creation event before the transaction commits. Raw
episodes remain queryable even after maintenance moves them outside the active
context budget.

## Epistemic model

The first typed predicates are `presentation.stance` (`nothing`, `weapon`, or
`spell`) and `vital_status` (`dead`). Each holder/subject/predicate key retains
all competing claims and their source episodes. The current belief is a
revisioned pointer, never an overwrite of history.

- Same-value independent evidence reinforces confidence toward, but never
  beyond, 995 permille.
- Newer credible or substantially stronger conflicting evidence supersedes the
  current claim while retaining both values.
- Older or weak conflicting evidence remains a contested alternative.
- Every decision stores prior, incoming and resulting confidence plus a plain
  reason.
- Supporting and contradicting episodes receive corresponding native graph
  edges, verified by native traversal before their relation audit commits.

This is deterministic epistemic bookkeeping. It does not ask an LLM to decide
whether evidence is true, and it does not collapse contradictions into a
single untraceable narrative.

## Recall and maintenance

Maintenance uses a caller-supplied active budget from 1 to 128. Eligible
episodes are ordered deterministically by salience, confidence, bounded recency
and stable identity. Excess episodes become `dormant`; nothing is physically
forgotten or deleted.

Recall accepts holder, optional subject/topic, an as-of game minute, minimum
confidence, maximum items and maximum characters. Fork-native vector,
multi-vector, graph and causal signals contribute a rank bonus, but the typed
domain filters remain authoritative. Each result records its score and reason.
No eligible evidence returns an explicit abstention rather than a fabricated
answer. Exact query and maintenance replay are no-ops; divergent identity reuse
fails.

## Ownership and flow

```text
OpenMW physical/social evidence
  -> PERCEPT-001 observer result
  -> memory intake audit
       rejected -> stop, no memory
       detected -> immutable episode + Fork native Memory cell
                  -> vector/version/CMT sidecars
                  -> typed claim revision + support/contradiction graph
  -> bounded maintenance
  -> typed as-of recall receipt + ranked items or abstention
  -> later DECIDE/DIALOGUE/LLM context consumers
```

OpenMW continues to own physical truth, actor save state and actuation. Fork
owns memory identity, claim revisions, active-context policy and recall
receipts. Later decision and dialogue systems may consume bounded recall but
must not bypass its provenance, temporal or confidence constraints.

## Proven behavior

Production v16 proves seven detected episodes against one rejection, native
vector/version/CMT creation, three traversable evidence relations, typed
reinforcement/supersession/contest, as-of recall, distractor exclusion,
explicit abstention and active/dormant maintenance. A real OpenMW sequence
produced 80 intake audits: 36 detected results mapped one-to-one to 36 domain
episodes/native cells and 44 rejected results created none. Standalone Fork
restart, OpenMW save/load, companion outage/replay, visible interaction and the
embodied witness loop pass with zero engine/Lua errors.

## Boundary and delivered consumers

MEMORY-001 does not itself select goals or intentions. DECIDE-001 now consumes
profiles, dynamic state, routine context, current perception and bounded recall
to arbitrate competing goals with revision-bound, stale-safe explanations.
SOCIAL-001, JOURNAL-002 and LLM-001 now consume the same provenance-bearing
boundary for embodied propagation, player topics and bounded cognition. Their
separate completion evidence—not memory storage alone—supports those claims.

## See Also

- [Normalized Physical and Social Perception](normalized-perception-and-attention.md)
- [Unified NPC Profile and State](unified-npc-profile-and-state.md)
- [Daily NPC Routines and Terminal Repair](daily-routines-and-terminal-repair.md)
- [Fork and OpenMW Capability Matrix](capability-matrix.md)
- [Fork–OpenMW Integration Architecture](integration-architecture.md)
- [Production Living-World Gap-Closure Programme](system-gap-closure-programme.md)
