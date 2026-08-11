# Embedding Dimensions and Similarity Calibration

> Sources: fork agents, 2026-07-10
> Raw: [DIMS calibration changelog](../../raw/embeddings/2026-07-10-dims-calibration-changelog.md)

## Overview

The fork's canonical embedding stack is **`Qwen/Qwen3-Embedding-8B` via
DeepInfra at 1536 dimensions** (`EmbedConfig::deepinfra()` default; matches
ai-collab-v3). Every similarity threshold in the system is calibrated to
that stack, and the single most important rule this page exists to record:
**cosine similarities shrink as dimensionality grows — re-measure every
threshold whenever the model or dimension count changes.**

## The measured numbers (2026-07-10, live)

| Setup | True claim/evidence contradiction pair | Unrelated prose |
|---|---|---|
| 32 dims (Matryoshka cut) | ~0.80 | (band floor was 0.70) |
| **1536 dims (canonical)** | **0.692** | **0.45–0.49** |

Same sentences, same model — only the dimension count changed. The 1536-dim
separation is *cleaner* (bigger relative gap) but the absolute values run
lower, which is why the contradiction band floor moved **0.70 → 0.60**
(`CandidateOptions::similarity_low`, `crates/memory/src/reconcile.rs`, with
the measurement recorded in the source).

## History worth knowing (why the demo numbers lied)

agent-starter began life with a 16-dim hermetic stub, moved to 32 dims when
the live API rejected 16 (the model's Matryoshka floor is 32), and was
raised to the canonical 1536 by owner decision — 32 dims discards ~98% of
the 8B model's signal and was never acceptable for the deployed tool
surface. Hermetic word-hash embeddings score realistic prose pairs at
~0.16–0.40 (below any sane floor); only token-identical negations reach the
band. Real semantic work requires the real backend (`configure` with a
DeepInfra key).

## See Also

- [DeepInfra latency and timeouts](deepinfra-latency-and-timeouts.md)
