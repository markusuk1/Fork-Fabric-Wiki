# Memory Engine — what the system knows

> Sources: [SpacetimeDB/docs/memory-engine/SPEC.md](../../SpacetimeDB/docs/memory-engine/SPEC.md); [docs/FORK-OVERVIEW.md](../../docs/FORK-OVERVIEW.md) §10; [docs/FORK-RECONCILE-PLAN.md](../../docs/FORK-RECONCILE-PLAN.md)

## Overview

The Memory Engine is the fork's highest abstraction: declare a table with
`memory_engine(dims = 1536, versions = true, summaries = true, multi_vector = true)`
and a `Memory` auto-owns its row + vector members + graph links + causal
history + provenance + summaries + temporal versions. One macro provisions
sidecar tables and verbs; the app supplies only its own fields and a
`MemoryCell` impl.

## The verbs

| Verb | Semantics |
|---|---|
| `remember(row, embedding, links)` | insert + vector member + graph edges + version row + CMT event, atomically |
| `recall(...)` | 4-signal fused retrieval (vector, multi-vector, graph, causal) combined by RRF — **signals combine, never overwrite** |
| `forget` / `forget_hard` | tombstone + real vector purge (a forgotten memory cannot resurface via ANN) / full purge |
| `summarize` | LLM → summary Memory, graph-linked to sources |
| `consolidate` | idempotent + convergent dedup/cluster/promote/prune — re-run on a settled store is a no-op |
| `dream` | counterfactual replay → candidates born QUARANTINED until corroborated |
| `contradiction_candidates_in_tx` / `reconcile_contradiction_in_tx` | the MEM-RECONCILE loop (below) |

## The reconcile loop (organizational memory)

Division of labor: the substrate surfaces contradiction CANDIDATES
deterministically (cosine band `[0.60, 0.995)` — floor calibrated live at
1536 dims; ceiling leaves near-duplicates to `consolidate`; optional
shared-neighbor filter), the CALLER judges semantics, the substrate resolves
(`NewerWins`/`OlderWins`/`QuarantineBoth`/`ExplicitWinner`, lower-id
tie-break) and quarantines everything *derived from* the loser — never the
winner, even when the winner derives from the loser. Idempotent: a settled
pair is a zero-write no-op and never resurfaces as a candidate. CMT tags
17–19 trace the whole thing.

## Lifecycle arcs (validated; illegal transitions are typed errors)

`Active → Forgotten | Superseded | Quarantine`, `Quarantine → Active |
Forgotten`, `Superseded → Forgotten`. Default recall EXCLUDES Quarantine and
Superseded — reconciliation demotions drop out of retrieval immediately.

## Determinism contract

Embedding/LLM calls happen strictly OUTSIDE any tx; all reads in one
`with_read_tx` snapshot; all writes atomic (the "freeze read-set, then one
write set" template every engine verb follows).

## See Also

- [Causal memory overview](../cmt/causal-memory-overview.md)
- [Dimensions and similarity calibration](../embeddings/dimensions-and-similarity-calibration.md)
- [Evaluation protocol](../vector/evaluation-protocol.md)
