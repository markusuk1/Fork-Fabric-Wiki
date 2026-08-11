# Vector Search Architecture & Performance Envelope

> Sources: [docs/FORK-OVERVIEW.md](../../docs/FORK-OVERVIEW.md) §2–3, §7; [docs/vector-memory.md](../../docs/vector-memory.md); [Fork manual](../../docs/fork-ingest-manual.html) §7; [GPU completion](../../docs/completions/COMP-GPU-CUDA-001.md); [GPU plan](../../docs/plans/GPU-CUDA-001-cuda-acceleration.md); `SpacetimeDB/crates/standalone/Cargo.toml`; [current release state](../../docs/release-state.json)
> Commit: 8d4f4531e90e1a2292c0e163f32509d8fdf7c8a0
> Updated: 2026-07-30

## Overview

Embeddings are first-class values: `#[index(vector, dims = 1536)]` on a
column gives native ANN search (`ctx.db.t().col().vector_search(q, k)`),
transactional, subscribable, snapshot/restore-safe, integrated into the
query planner. Backends behind one `VectorIndex` trait: HNSW, QuIVer, HENN —
with **LSM-VEC** as the tiered store for scale.

## LSM-VEC tiering (the scale path)

Hot tier → size-tiered **exact** segments → IVF-cell-aligned terminal tier.
Small segments (<64k vectors) use exact brute-force (this fixed a historical
recall bug where greedy HNSW search silently dropped ~half the true
neighbors); large segments route through a frozen 128-cell IVF codebook
where `ef_search` = number of probed cells.

## The proven envelope (gate bench, reproduced at every rebase)

**1M vectors: recall@10 = 1.0 (clustered) @ ~5.9 ms, 13× faster than
exact.** This is the regression gate — any upstream rebase or datastore
change must reproduce it (release build, ~6 min run).

## Per-index metric declaration

`euclidean` / `cosine` / `inner_product` declared per index and honored
end-to-end including restart/replay. Embeddings are L2-normalized at
projection so Euclidean ranking equals cosine.

## Multi-vector (parent/member)

Model an entity by many member vectors (chunks/roles/fields); rank parents
by aggregating member hits (`any_max`, `top_n_sum`, `weighted_roles`).
Parent suppression on member delete; delta-aware subscriptions.

## Health / doctor

`LsmVecIndex::health_report()` + `self_check()` — recall vs brute force over
the index's own raw entries; detects seeded corruption. Two historical bugs
worth knowing: the greedy-search recall bug (above) and the clear-leak bug
(table truncate left stale vectors that resurrected under row-pointer reuse
— fixed by resetting vector indexes on clear).

## GPU acceleration and placement (GPU-CUDA-001, HOSTCFG-002)

The `cuda` Cargo feature is in the standalone crate's **default feature set**
since fork build 25, so a plain `cargo build --release` produces a
CUDA-capable host. A toolchain-less/CPU-only build must explicitly use
`--no-default-features`; that build compiles no CUDA code. Compilation and
runtime use are separate gates: `[gpu] discovery-enabled=false` keeps a
CUDA-capable binary on the CPU path. When selected at runtime,
`LsmVecIndex::search` scores immutable segments on an NVIDIA GPU. Design points
that matter:

- **Integrated inside the index**, not bolted on at the datastore — so the hot
  tier, IVF routing, metric, and tombstones are all visible. Distances come from a
  metric-aware CUDA kernel (`vector_distance_kernel`) that matches the CPU
  `DistanceMetric` **bit-for-bit** for all three metrics (Euclidean/Cosine/
  InnerProduct: f64 accumulation, `finite→inf`), so a GPU result is identical to —
  only more exact than — the CPU path. Hot tier + any non-resident / over-budget
  segments stay on CPU; results merge into one candidate set with the same
  tombstone/liveness filtering.
- **One launch per query**: all segment vectors live in a single concatenated
  device buffer, rebuilt only when the segment set changes (an `(id,len)`
  signature; compaction always shrinks `len`, flush/merge/cluster mint new ids).
  The kernel is JIT-compiled once per pool and cached. This is what makes it fast —
  per-segment launches were ~5× *slower* than CPU.
- **Budget & fallback**: `[gpu] vram-budget-bytes` plumbs in via the
  `VectorIndex::set_gpu_residency_budget` trait method; the `GpuMemoryPool` is the
  single VRAM authority. Over budget, no device, or any CUDA error → the CPU
  routing path (memoized so it does not re-flatten every query). The budget is
  **per-index**.
- **Exact device authority**: build 9 carries the selected NVIDIA UUID,
  process-local CUDA ordinal, and budget as one immutable configuration from
  standalone discovery through engine/datastore transactions to every vector
  index. The allocator verifies that its CUDA context UUID matches. There is no
  legacy implicit ordinal-0 allocation when discovery is disabled or fails.
- **Placement modes**: `gpu.discovery-enabled=false` disables GPU allocation;
  enabled with `gpu.device-uuid=null` selects automatically; enabled with a UUID
  pins exactly that physical device. A missing or CUDA-hidden pinned UUID fails
  closed to the CPU path rather than selecting another GPU.
- **Measured (RTX 3080, Ampere sm_86; CUDA Toolkit 13.3):** ~**9× faster than CPU**
  at 1536 dims (2.63 vs 22.85 ms/query, n=20k) and 384 dims (0.83 vs 7.68 ms),
  result-set overlap **1.000** (exact). Compute lives in `crates/vector-index`
  (beside `DistanceMetric`); `execution` re-exports it (no dependency cycle).
- **Audited to 0 C/H/M/L** via a 4-lens adversarial review + a re-audit. Build env:
  `vcvars64` + `CUDA_PATH` (nvcc needs the MSVC host). Accepted release binaries
  from build 25 onward use the CUDA compile default. HOSTCFG-002 hardware
  validation additionally executed the real allocator and vector kernel on both
  the GTX 1660 Ti and RTX 3080, including their reversed physical/CUDA ordinal
  mapping. The runtime observed after the 2026-07-30 promotion is build 36 with
  `compiled=true`, discovery disabled and `accelerated=false`; that runtime
  policy does not change the compile-default fact.

`/v1/status` reports startup readiness and topology, not a claim that every
query executed a GPU kernel. Query execution counters and the hardware tests are
the execution proof. Multiple GPUs are separate residency/allocation domains;
their VRAM is not pooled. External embedding, indexer, and reranker model
placement is outside the Fork database engine.

Distinct from the *residency-policy model* in `tiered-vector/gpu_residency.rs`,
which is now a metrics estimator only — the real search-path cache is
`tiered-vector/gpu_search.rs`.

## See Also

- [Fabric-buffered dual-GPU vector ingestion](fabric-buffered-vector-ingestion.md)
- [Evaluation protocol](evaluation-protocol.md)
- [Dimensions and similarity calibration](../embeddings/dimensions-and-similarity-calibration.md)
- [Staged builds & compatibility](../operations/staged-builds-and-compat.md)
