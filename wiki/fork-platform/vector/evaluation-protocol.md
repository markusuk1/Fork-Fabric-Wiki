# Evaluation Protocol — how to trust the numbers

> Sources: [docs/FORK-EVAL-PROTOCOL.md](../../docs/FORK-EVAL-PROTOCOL.md); fork-ingest-manual §12

## Overview

**LoCoMo and SWE-bench are RETIRED** (owner decision, 2026-07-02): both
predate every current model's training cutoff, so scores measure
memorization, not capability. Every quality claim in this repo instead
traces to instruments that **compute their own ground truth at run time** —
nothing a model could have memorized.

## The instruments

1. **Planted-fact grid** (`eval_procedural.rs`, ~1 s in CI): a seeded
   generator plants a target that is a semantic near-miss while decoys sit
   strictly closer in vector space; the target is graph-linked. Result:
   pure vector r@1 = 0.000 under 16-decoy pressure (by construction);
   fused RRF recovers r@1 = 1.000; noisy-structure tax measured at 0.2%
   MRR (ceiling 2%). Assertions are structural, not point estimates.
2. **Planted-contradiction family** (`eval_reconcile.rs`): separated regime
   must be EXACT (p = r = 1.0, fixture self-checked); topic-bleed regime
   reports raw precision (0.133 — the honest limit of a similarity band)
   and asserts the shared-neighbor filter restores p = r = 1.0; propagation
   exactness (zero false quarantines); convergence (second pass = 0
   candidates, 0 writes).
3. **The 1M gate bench** — see the vector architecture page. Re-run at
   every rebase.

## Comparative claims

Against pure-vector systems (pgvector/Qdrant/LanceDB), dominance holds by
construction: the grid's vector column is EXACT kNN — the ceiling of any
pure-vector store on this family — and fused beats it by the measured
margins. Latency head-to-heads would need provisioned instances (recorded
re-open trigger, not done).

## Rules for new claims

New task families: seeded generators beside the existing ones. Real text if
ever needed: post-cutoff data only. A claim without a reproduction path
doesn't ship.

## See Also

- [Vector search architecture](vector-search-architecture.md)
- [Memory engine](../engines/memory-engine.md)
