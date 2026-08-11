# Predictive Payload Speculation

> Sources: [PRED-001 as-built evidence](../../raw/vector/2026-07-15-pred001-as-built-predictive-fabric.md); [PRED-002 default-active request](../../docs/requests/PRED-002-default-active.md); [PRED-002 build-15 evidence](../../raw/vector/2026-07-15-pred002-default-active-build15.md); [adaptive CMT-backed concept](../../raw/vector/2026-07-15-adaptive-cmt-speculative-execution.md); [AI-Collab B/C calibration evidence](../../raw/workflow/2026-07-16-predictive-fabric-bc-calibration.md); [PRED-004 build-17 evidence](../../raw/engines/2026-07-16-pred004-adaptive-context-substrate-build17.md)
> Commit: 1ec595a95
> Updated: 2026-07-16
> Status: Default-active prediction retained; adaptive safety, stage, and rerank contracts implemented and staged in build 17

## Overview

Predictive payload speculation is a generic Fabric execution class that can prepare a likely follow-up before demand arrives, keep the result quarantined, and expose it only when an independently recomputed authoritative signature matches exactly. It is opt-in at schema level, bounded and preemptible, deterministic under replay, and unable to authorize side effects. PRED-002 makes a fresh opt-in database Active with conservative limits; missing or malformed state still fails closed, and upgrades preserve the existing durable operator selection. Fork build 14 first proved the rollback-safe substrate; build 15 carries the default-active seed.

## Fork and application ownership

Fork owns reusable mechanics: canonical signatures, deterministic selection and learning, allowlisted admission, budgets, demanded-first preemption, artifact quarantine, exact atomic promotion, body staging/reclamation, packed vector commits, CMT evidence, replay, counterfactual evaluation, metrics, configuration, emergency stop, and automatic trip.

AI-Collab or another application owns domain knowledge: candidate templates, repository/workload features and revisions, chunking, GPU/model workers, model-profile eligibility, and measurement of actual cost and latency saved. GPUs do not pool VRAM. Identical model profiles can independently claim batches from the same queue; the faster worker naturally claims more.

## Configuration and kill switches

Opt-in tables gain speculation fields and sidecars only when `fabric_payload(..., speculation)` is selected. Non-opt-in schemas remain unchanged, and the ordinary submit path always creates demanded work and clears speculative signatures.

Runtime configuration is immutable-by-revision durable state:

| Control | Behavior |
|---|---|
| Off / Shadow / Active | Off denies execution, Shadow records decisions without execution, Active admits eligible work. |
| Emergency stop | Immediately denies new speculative execution. |
| Automatic trip | Latched by demanded-work interference or unfavorable accumulated value. |
| Scope/type/primitive rules | Any matching deny wins; absent rules inherit the global mode. |
| Cleanup policy | Reclaim staged bodies immediately on disable, or retain only until bounded TTL. |
| Resource limits | Bound total/root/scope inflight work, bytes, compute units, fan-out, branch depth, and TTL. |
| Value gates | Minimum confidence and expected value; maximum demanded queue depth and latency regression. |

The build-15 fresh seed is Active with two total inflight artifacts, one per
root, two per scope, one fan-out, depth one, 64 MiB staged bytes, 250,000
compute units, a 60-second TTL, 5,000 basis-point confidence, non-negative
expected value, no pending demanded work, a 100 basis-point demanded-latency
trip threshold, and a 32-sample trip floor. It reclaims disabled work
immediately. These are bootstrap values, not hard-coded policy: operators can
replace them through the durable configuration reducers, including switching
to Shadow or Off at runtime.

Existing databases are not silently rewritten on module upgrade. Their active
configuration revision remains authoritative. Configuration absence,
malformation, emergency stop, automatic trip, explicit Off, or a matching deny
continues to block admission.

The operator ACL is seeded from the native publisher identity, supports explicit rotation, rejects anonymous control, and refuses to disable the final enabled operator.

## Deterministic prediction and exact identity

Candidate selection uses integer-only state and stable ordering. A decision records candidate evidence, predictor/config versions, confidence, expected value, pre-state and feature digests, and the selected signature. Feedback distinguishes matched-used, matched-slower, wrong prediction, stale revision, policy denial, cancellation, demand preemption, and expiry. Invalidations and capacity outcomes do not masquerade as behavior misses.

The v1 SHA-256 signature binds:

- root and authoritative predecessor event;
- payload type and immutable template digest;
- ordered canonical input references;
- repository and workload revisions;
- schema version, model profile, and dimensions;
- policy, predictor, and config revisions.

Promotion recomputes that signature from the real demand. Build 17 also binds
the active immutable action-policy digest, so changing the allowlisted
parameter contract invalidates stale work. A constant-size equality gate then
promotes the quarantined artifact atomically or rejects it and reclaims the
large body. The gate does not traverse CMT or update the learner synchronously.

Quality-risk evidence is evaluated before expected efficiency. Missing or weak
evidence abstains deterministically; a latency win cannot promote a candidate
that misses a configured quality threshold.

## Adaptive stages and rerank calibration

Build 17 adds content-addressed identities, leases, receipts, and deterministic
reuse decisions for embedding, recall, expansion, rerank, and frame assembly.
Exact concurrent work coalesces while any dependency, revision, policy,
model/profile, resolver, or implementation mismatch fails closed.

The rerank matrix crosses candidate fractions 100/75/50/25/12.5 percent with
batch sizes 1/2/4/8/16/32/64 under one retrieval-selection digest. Baseline,
latency, throughput, peak VRAM, recall, MRR, NDCG, and final-selection agreement
are durable evidence. Promotion is quality-first and baseline-backed; AI-Collab
retains ownership of the actual reranker, corpus, and experiment execution.

## Safety and lifecycle

Only enabled, capability-authorized, deterministic, replayable, idempotent, side-effect-free templates are eligible. The parent must be demanded and authoritative. Speculative results cannot recursively create new speculative ancestry, and an unpromoted artifact is never authoritative.

The legal lifecycle is:

```text
Planned -> Executing -> Quarantined -> Promoted
    |          |              |------> Rejected
    |          |--------------|------> Expired
    |-------------------------|------> Preempted
```

Demanded work has a separate priority-preserving claim path. It blocks or preempts speculation according to durable limits, so speculative capacity is opportunistic rather than equal competition.

An external worker learns about preemption through the subscribed public
payload row: claimed or running speculative work transitions to `Cancelled`,
while pending or failed speculative work is dead-lettered. The worker aborts
cooperatively between microbatches, re-reads after subscription reconnect, and
uses polling/TTL only as fallback. Private speculative sidecars are not required
for this cancellation path. See
[Fabric GPU Worker Guest Integration](../vector/fabric-gpu-worker-integration.md).

## Packed vector staging and commit

`FVBATCH1` carries bounded packed binary vectors plus deterministic keys and provenance. Decode and commit reject malformed lengths, digest mismatch, non-finite values, wrong dimensions or normalization, duplicates, stale revisions, scope mismatch, and conflicting receipts. One reducer transaction publishes an accepted batch and receipt idempotently. Retries with the same logical identity are no-ops; conflicting reuse fails closed.

Fabric therefore acts as a bounded durable buffer without becoming a second vector database. GPU workers can continue through a multi-buffer ring after durable staging acceptance while the authoritative sink drains. If combined producers outrun the sink on average, credits and byte budgets apply honest backpressure rather than unbounded memory growth.

## CMT evidence without schema drift

Tags 50–59 distinguish decision, start, quarantine, promotion, rejection, expiry, preemption, predictor update, staging acceptance, and vector commit. Edges use explicit relations such as `Produced`, `EvidenceFor`, `Conflicts`, and `Enables`; the actual request is never falsely marked as caused by its prediction.

Persisted event kinds remain the historical 16-variant enum. Higher registered tags use physical `InternalCommit`, with exact semantics atomically encoded in the existing opaque event metadata:

```text
FCMTP001 | registry_version:u16-le | tag:u8 | name_len:u16-le | canonical UTF-8 name
```

This design replaced two rejected approaches: widening the persisted enum broke stored schemas, while a new private system table prevented old hosts from reopening upgraded replicas. Builds 9 and 11 both reopened build-14 data and preserved tag-50 bytes unchanged.

## Observability and acceptance

Durable metric kinds cover decisions, admissions/denials, match/miss/invalidation, latency saved or lost, compute, staged bytes/age, queue depth, preemption, demanded interference, promotion/commit latency, duplicate no-op, automatic trip, rejection, vector items, expiry, and reclaimed bytes. Replay re-executes deterministic decisions from captured evidence; counterfactual evaluation can compare thresholds or budgets without mutating authoritative state.

Seven controlled release-mode demanded-selector runs measured p50 deltas from -0.53% to +0.27% and p99 deltas from -1.06% to +0.58%, with no repeatable regression. Literal zero overhead is not claimed; the enforced design goal is no measurable demanded-work regression, backed by strict priority, bounded work, preemption, and automatic disable.

## Downstream B/C calibration evidence

AI-Collab's first faithful 25-task B/C calibration delivered source-bearing
native Fabric frames on all 23 valid C tasks. Relative to AI-Collab with
prediction Off, Active prediction reduced input tokens by 15.38%, uncached
input by 19.41%, output by 10.62%, end-to-end runtime by 9.48%, and agent tool
calls by 51.92%.

Quality was 23/23 for B and 22/23 for C after two defective hidden graders were
excluded symmetrically. The C miss was an over-broad impact edit set rather
than a retrieval failure. This is a single-repetition calibration, not a
promoted product claim, and it does not include A-versus-B or indexing time.
The full controls and claim boundary are in the
[AI-Collab A/B/C Benchmark Reference](../workflow/ai-collab-abc-benchmark-reference.md).

## See Also

- [Fabric Engine](fabric-engine.md)
- [Fabric-Buffered Dual-GPU Vector Ingestion](../vector/fabric-buffered-vector-ingestion.md)
- [Causal Memory Overview](../cmt/causal-memory-overview.md)
- [Staged Builds & Compatibility](../operations/staged-builds-and-compat.md)
- [Fork Versioning & Drift Detection](../operations/fork-versioning.md)
- [AI-Collab A/B/C Benchmark Reference](../workflow/ai-collab-abc-benchmark-reference.md)
- [Adaptive Context Policy and Item-Level Utility](adaptive-context-policy.md)
- [Adaptive Context Substrate](adaptive-context-substrate.md)
- [Fabric GPU Worker Guest Integration](../vector/fabric-gpu-worker-integration.md)
