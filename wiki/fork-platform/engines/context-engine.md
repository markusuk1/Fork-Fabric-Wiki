# Context Engine — what the system attends to

> Sources: [docs/FORK-OVERVIEW.md](../../docs/FORK-OVERVIEW.md) §12; [docs/CONTEXT-ENGINE-ROADMAP.md](../../docs/CONTEXT-ENGINE-ROADMAP.md); [adaptive context policy programme](../../raw/context/2026-07-16-adaptive-context-policy-programme.md)
> Commit: 4d160e7f5
> Updated: 2026-07-16

## Overview

Context management as a first-class primitive: a `ContextFrame`
(`context_frame(dims, max_items, max_bytes, public_sidecars)` on a table) is
the budgeted, versioned, CMT-traced **working-set / attention slice** a
reasoner is scoped to for one step. The third sibling to Memory (knowledge)
and Fabric (work-in-motion).

## Key properties

- **Budgets are exact**: item count, byte count, and token count — token
  budgeting uses the deterministic `token_count()` host syscall (real BPE,
  o200k_base), not estimates.
- **Recoverable tiered compaction**: items move Hot/Warm/Cold *by
  reference* — compaction never deletes a ref, so nothing is
  unrecoverable.
- **Replay-the-working-set**: because assembly is CMT-traced, the exact
  frame contents at any causal event can be reconstructed — "what was the
  agent looking at when it decided X?" is a query, not archaeology.
- Frames are assembled from recall results (see the fabric engine's
  claim-with-context flow) and attached to payloads by reference.

## Verifiable Context Codec

`spacetimedb-context::codec` adds a model-independent codec foundation for
agent payloads. A `CodecEnvelope` contains typed `CommitmentAtom`s, codec and
version identity, SHA-256 digests for the canonical raw object, canonical atom
encoding, and compact background, plus explicit byte/token measurements.

Verification is deliberately independent of compression: the caller supplies
the expected critical commitments, and the verifier rejects omissions,
mutations, polarity or scope changes, duplicate IDs, invalid ranges, stale raw
objects, background tampering, and inconsistent byte metrics. `unfold()` then
returns the exact original byte span only after the complete envelope passes
integrity checks. Canonical content remains outside the envelope and can be
attached to Fabric by its existing `body_ref`/`context_id` composition.

COD-001 also proved the first narrow utility claim for exact source selection.
In the replacement three-arm benchmark, `selective_8x` matched raw docs on
primary success (9/10 each) while reducing median input tokens from 307,499 to
43,267, and equal-budget leading truncation scored 0/10. The adopted production
direction is therefore typed protected commitments plus query-directed exact
source unfolding.

Learned tokens, KV caches, latent agent communication, and compressed reasoning
remain outside the trusted path until benchmarked separately.

## See Also

- [Fabric engine](fabric-engine.md)
- [Memory engine](memory-engine.md)
- [Adaptive Context Policy and Item-Level Utility](adaptive-context-policy.md)
