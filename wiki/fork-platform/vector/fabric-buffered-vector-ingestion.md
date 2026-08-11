# Fabric-Buffered Dual-GPU Vector Ingestion

> Sources: User/Fork architecture discussion, 2026-07-14 and 2026-07-15; Fork Fabric/CMT as-built behavior; AI-Collab V3 dual-GPU profile evidence; [PRED-006 completion](../../docs/completions/COMP-PRED-006.md)
> Raw: [Fabric-buffered dual-GPU vector ingestion concept](../../raw/vector/2026-07-14-fabric-buffered-dual-gpu-vector-ingestion.md); [Adaptive CMT-backed speculative execution](../../raw/vector/2026-07-15-adaptive-cmt-speculative-execution.md); [PRED-001 as-built evidence](../../raw/vector/2026-07-15-pred001-as-built-predictive-fabric.md); [AI-Collab A/B/C benchmark reference](../../raw/workflow/2026-07-15-ai-collab-abc-benchmark-reference.md); [AI-Collab B/C calibration evidence](../../raw/workflow/2026-07-16-predictive-fabric-bc-calibration.md); [AI-Collab GPU scheduling request](../../raw/vector/2026-07-16-ai-collab-gpu-scheduling-guest-integration.md)
> Commit: 73fb27f6e; carried by build 27 (`6e20ac8d9`)
> Updated: 2026-07-24
> Status: Fork substrate implemented; AI-Collab single-GPU model placement observed, while parallel dual-GPU indexing and sink throughput remain unproven

## Overview

The implemented Fork substrate uses Payload Fabric as a bounded, durable control
plane and asynchronous handoff between two identical AI-Collab embedding workers
and the authoritative per-repository vector store. Fabric distributes typed
embedding batches, records claims/retries/replay, and buffers completed batch
references. It does not become a duplicate vector database or carry individual
vectors as ordinary JSON payloads.

## Domain Boundary

The end-to-end dual-GPU indexer is primarily an **AI-Collab capability**. AI-Collab owns repo scanning and
chunking, the identical GTX 1660 Ti/RTX 3080 model profiles, shared-queue worker
scheduling, embedding provenance, repository revision policy, and writes to its
isolated `code_chunk` stores.

Fork now supplies the complete generic half: packed binary batch validation,
content-addressed durable staging, idempotent atomic vector commit receipts,
shared demanded-first scheduling, deterministic speculative decisions,
quarantine and exact promotion, generic leased resource reservations, bounded
work graphs, scheduling, chunked artifacts, metrics, CMT replay, and runtime
toggles. AI-Collab can integrate these primitives without another Fork
engine change; application-specific candidate generation and GPU execution do
not belong in Fork.

## Typed Fabric Pipeline

The following `vector.*` values are versioned **application payload type IDs**,
not generated native Fork types. Build 20+ supplies the generic Fabric lifecycle,
packed-vector staging/commit substrate, and leased resource reservations;
AI-Collab defines the capsule fields and reducer names. See
[Fabric GPU Worker Guest Integration](fabric-gpu-worker-integration.md) for the
exact build boundary, two-worker lifecycle, pre-emption signal, VRAM admission,
and queue metrics.

```text
scan/chunk
    |
vector.embed.batch/v1
    |
    +-- identical 1660 Ti embedding primitive
    +-- identical RTX 3080 embedding primitive
    |
durable packed-binary staging + vector.store.commit/v1
    |
idempotent vector-store sink
    |
per-repo code_chunk index
```

`vector.embed.batch/v1` contains references and contracts: repository,
snapshot/revision, deterministic batch/chunk IDs and hashes, modality,
model/profile, dimensions, normalization, device eligibility, priority, and
retry policy. Both GPU primitives claim from the same queue, so the faster 3080
naturally accepts more batches without a fixed repository split.

`vector.store.commit/v1` references a durably staged packed binary result plus
its provenance and expected manifest. It does not embed JSON float arrays in the
lifecycle row. A 1,536-dimensional `f32` vector is 6,144 bytes; the validated
batch-128 profile produces 786,432 raw vector bytes per full batch before
metadata. Per-vector capsules would multiply lifecycle, transition, replay,
subscription, and serialization overhead.

Reranking uses `vector.rerank.batch/v1`, not the durable-vector path. A complete
candidate set stays on one GPU because AI-Collab measured GPU-generation score
calibration differences. Text embedding batches can share the two workers;
image vectors and rerank results retain profile/GPU provenance and remain gated
from unsafe cross-GPU merging.

## No-GPU-Stall Latency Contract

The honest target is **zero added GPU critical-path stall**, not literal zero
latency. Durable acceptance and database commit necessarily do work, but neither
must serialize behind the next GPU batch.

Each worker uses a bounded multi-buffer ring:

```text
buffer A: GPU computes batch N
buffer B: asynchronously submits batch N-1
buffer C: accepted batch awaits vector-store commit
```

Fabric acknowledges only after it durably owns the packed batch. The worker can
then discard its copy while immediately continuing on another buffer. Index
commit and retrieval visibility happen asynchronously.

Three milestones are reported separately:

1. **Accepted:** Fabric durably owns the batch.
2. **Committed:** the atomic vector-store transaction succeeded.
3. **Visible:** retrieval can query the vectors.

Credits return after commit, bounding staging bytes. If vector-store drain
throughput remains below combined GPU production, backpressure is unavoidable;
unbounded buffering merely converts a throughput mismatch into a memory/disk
failure. Under a healthy sink whose average drain rate meets production, normal
bursts are absorbed without GPU idle.

## Transaction and Retry Safety

The sink validates claim identity, 1,536 finite normalized values, model/profile
and modality, expected chunk completeness, current repository revision, and
per-repo isolation. It commits a batch atomically before settling its work.

The logical idempotency key is:

```text
(repo_id, revision, chunk_hash, model_profile_id, dimensions)
```

GPU UUID is provenance, not vector identity. The first valid commit wins; a
retry on the other GPU returns success without duplicating or replacing it.
AI-Collab's current `index_add_chunk` reducer performs a plain insert, so its
domain implementation needs a deterministic unique key or atomic idempotent
batch reducer before enabling Fabric retries.

## Metrics and Acceptance Evidence

Required metrics include per-GPU batch duration and idle gaps, acceptance
latency, staged bytes/batches and oldest age, claim-to-commit time, sink
throughput, visibility lag, backpressure, retries/dead letters, duplicate no-op
count, stale-revision rejection, provenance, and interactive query latency.

The concept is accepted for production only after sustained dual-GPU large-repo
ingestion proves bounded memory, no lost/duplicate chunks, crash/restart replay,
correct revision replacement, no buffer-attributable GPU idle under a healthy
sink, and no material regression to interactive retrieval.

## Adaptive Speculative Execution Extension

Fabric can extend this pipeline by predicting a likely follow-up payload,
executing only safe work before it is requested, and quarantining the result.
An actual follow-up promotes that result only when an exact authoritative
signature matches; otherwise the body is reclaimed and a compact resolution
record remains.

```text
authoritative predecessor
    -> prediction decision
    -> speculative execution
    -> quarantined result

actual authoritative follow-up
    -> exact signature gate
    -> promote or discard
    -> predictor-state update
```

The signature binds the task root and causal predecessor, typed payload
template, canonical input references, repository revision, schema,
model/profile and dimensions, policy version, and predictor version. Prediction
never authorizes work. Pure, replayable, and idempotent primitives may execute
fully; side-effecting operations may only prepare inputs before normal policy
and authorization succeed.

Known indexing work such as scan -> chunk -> embed -> commit should be
scheduled directly. Speculation is valuable for optional branches such as
richer parsing, image processing, graph enrichment, retrieval expansion,
reranking, context assembly, and likely validation work.

## Deterministic Adaptive Predictor

Start with an interpretable deterministic contextual predictor. Its inputs are
versioned workflow facts: predecessor event/payload type, workflow stage,
repository/workload class, modality, dependency state, revision, model/profile,
schema, and policy. Candidates are registered typed payload templates, not
arbitrary generated instructions.

The learner records observations, matches, mismatches, stale revisions, policy
denials, pre-emption, compute cost, bytes, latency saved, and demanded-work
interference. It optimizes expected value rather than hit rate alone:

```text
expected value = confidence lower bound * latency saved
               - compute cost
               - GPU/VRAM interference
               - storage and transfer cost
```

Use fixed priors, integer or fixed-point state, deterministic updates, and a
canonical tie-break. Every decision records predictor version, pre-state
digest, features, selected candidate, score components, resolution, state
delta, and post-state digest. Old evidence may decay or age out, but the exact
state used by each historical decision remains addressable.

Feedback distinguishes genuine prediction quality from invalidation:

- matched and used is positive evidence weighted by measured latency saved;
- matched but slower is negative evidence;
- wrong type or wrong canonical inputs are true misses;
- stale repository/model/policy revisions are invalidations;
- policy denial makes the candidate ineligible under that policy version;
- cancellation and demanded-work pre-emption are neutral capacity outcomes;
- expiry without demand records non-use and its compute/storage cost.

## CMT as Evidence and Learning Backbone

Fabric CMT-traces lifecycle transitions and replays its control plane.
Speculation extends that same causal record rather than creating a separate
telemetry system.

Use existing causal relations precisely:

- `DerivedFrom`: prediction derived from authoritative context;
- `Produced`: execution produced a quarantined result;
- `EvidenceFor`: the resolved actual follow-up supports a learner update;
- `Conflicts`: the actual follow-up disproves the candidate;
- `Enables`: an exact match allowed promotion;
- `CausedBy`: only for genuine causal transitions.

The prediction did not cause the actual request. Training consumes only
authoritative follow-ups and resolved outcomes, never speculative payloads
merely because the predictor created them. This avoids a self-confirming
feedback loop and preserves the meaning of CMT why/effects queries.

CMT unlocks explanations for every prediction, promotion, and rejection;
deterministic predictor reconstruction; per-GPU cost attribution; blast-radius
analysis for model/profile/policy changes; rollback of harmful predictor
versions; and counterfactual replay of alternative confidence thresholds and
budgets against recorded workloads.

## Speculation Latency and Resource Contract

The exact gate compares a precomputed canonical signature and never traverses
CMT synchronously. Promotion/rejection and minimal causal evidence commit
atomically; predictor aggregation and learning run off the critical path. CMT
records one event per control transition or batch, never per vector or token.

Demanded work has strict priority and can immediately pre-empt speculation.
Bound speculative bytes, VRAM, compute, fan-out, branch depth, expiry, and
per-workspace/repository budgets. Do not recursively speculate from an
unpromoted prediction. Authorization, isolation, side-effect policy, and
eligible primitive types remain non-learning allowlists.

Literal zero overhead is not claimed. Seven controlled release-mode demanded
selector runs found no repeatable regression (p50 -0.53% to +0.27%; p99 -1.06%
to +0.58%). Strict demand priority, preemption, bounded work, and automatic trip
enforce the no-measurable-regression contract at runtime.

## CMT Tag Allocation Guardrail

The global registry covers Memory 17-24, Fabric 25-34, Context 35-44,
Perception 45-49, and predictive execution 50-59. It proves collision freedom
against known host tags plus complete and contiguous engine blocks. Exact
higher-tag semantics use the rollback-safe `FCMTP001` metadata envelope while
retaining the historical physical event enum.

This corrects stale Fabric guidance that previously called 35+ free and closes
the prior registry gap that omitted Perception.

## What the B/C calibration proves—and does not

The AI-Collab preflight proved a useful placement configuration: the headless
GTX 1660 Ti held both embedding and reranking workers at 4,623/6,144 MiB while
the RTX 3080 had no model worker and remained available for desktop use. Both
arms saw 25 ready indexes, 542 chunks, 1,536-dimensional model output, seven
Conduit tools, and source-bearing Fabric probes.

This is not evidence for the proposed parallel dual-GPU ingestion path. Indexes
were already ready before timing began, and the run did not exercise identical
profiles on both GPUs, shared-queue batch claims, packed vector staging/commit,
or sustained sink backpressure. Those acceptance gates remain AI-Collab work;
no new Fork primitive is justified by this calibration alone.

## Rejected Designs

- Per-vector Fabric capsules or JSON vector bodies.
- Fabric as a second vector store.
- ACK before durable acceptance or ACK only after vector-index commit.
- Unbounded queues or silent loss under pressure.
- Cross-GPU rerank score merging without calibration evidence.
- Claiming a new Fork primitive is required before measuring the guest path.
- CMT traversal on the synchronous promotion path.
- Learning from speculative artifacts without authoritative resolution.
- Treating prediction as authorization or as the cause of actual demand.
- Randomized control-plane updates that cannot be replayed.
- Recursive or unbounded speculative branches.

## See Also

- [Fabric Engine](../engines/fabric-engine.md)
- [Causal Memory Overview](../cmt/causal-memory-overview.md)
- [Predictive Payload Speculation](../engines/predictive-payload-speculation.md)
- [Vector Search Architecture](vector-search-architecture.md)
- [Evaluation Protocol](evaluation-protocol.md)
- [AI-Collab A/B/C Benchmark Reference](../workflow/ai-collab-abc-benchmark-reference.md)
- [Adaptive Context Policy and Item-Level Utility](../engines/adaptive-context-policy.md)
- [Fabric GPU Worker Guest Integration](fabric-gpu-worker-integration.md)
