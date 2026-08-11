# Inbound Request: Guest Integration Surface for Fabric-Managed GPU Scheduling

> From: AI-Collab-v3 (D:/Projects/AI-Collab-v3), 2026-07-16
> Copy of record: AI-Collab-v3 [GPU scheduling guest-integration request](../../../AI-Collab-v3/docs/requests/fork/2026-07-16-fabric-gpu-scheduling-guest-integration.md)
> Design authority: AI-Collab-v3 [Fabric-managed GPU scheduling design](../../../AI-Collab-v3/docs/design/fabric-managed-gpu-scheduling.md) (owner-approved direction)
> Related Fork pages: [Fabric-Buffered Dual-GPU Vector Ingestion](../vector/fabric-buffered-vector-ingestion.md), [Fabric Engine](../engines/fabric-engine.md), [Predictive Payload Speculation](../engines/predictive-payload-speculation.md)
> Raw: [Preserved AI-Collab request](../../raw/vector/2026-07-16-ai-collab-gpu-scheduling-guest-integration.md)
> Commit: 6e20ac8d9
> Updated: 2026-07-24
> Status: ANSWERED — current Fork facilities and historical request outcome reconciled

> **Current-state correction (2026-07-24):** the request and dated status
> sections below preserve the 2026-07-16 exchange. Since then, PRED-006 shipped
> generic leased resource reservations and the rest of the orchestration
> platform in build 20; build 27 carries it and fixes the requested
> speculation-column order/default migration defect. See
> [Fabric Orchestration Platform](../engines/fabric-orchestration-platform.md).

## Context

AI-Collab is adopting the Fabric Engine as its heterogeneous GPU scheduler:
model inference, predictive cache generation, placement, and two-GPU load
balancing become ordinary Fabric payloads; external llama.cpp workers keep
model loading, CUDA execution, batching, and telemetry. This matches the
boundary your ingestion page already draws ("application-specific candidate
generation and GPU execution do not belong in Fork"), and we accept your
rejected-designs list in full — including "claiming a new Fork primitive is
required before measuring the guest path." Accordingly, everything below is
either a documentation/confirmation ask or explicitly conditional on
measurement.

We also accept the acceptance gates in Fabric-Buffered Dual-GPU Vector
Ingestion (bounded memory, no lost/duplicate chunks, crash/restart replay,
revision replacement, no buffer-attributable GPU idle, no interactive
regression) as ours to prove, and we own the `index_add_chunk` idempotency gap
your page names.

## Status update (2026-07-16, requester)

Ask 1 is self-answered: live :3012 runs fork build 17 (commit ed64e1d0b9) with
the predictive/adaptive surface active per /v1/status. Remaining asks are
documentation/pattern confirmations only — nothing below blocks AI-Collab's
guest-side work, which is proceeding.

## Asks (in priority order)

1. **Contract confirmation and guest docs.** Confirm which Fork build ships the
   guest-facing typed contracts `vector.embed.batch/v1`,
   `vector.store.commit/v1`, and `vector.rerank.batch/v1`, and document the
   guest claim semantics for GPU worker primitives (claim, retry, settle,
   dead-letter) sufficient for two same-host workers claiming from one shared
   demanded-first queue. If these are already generic `submit_work`/`claim_work`
   over typed capsules, a short worked example in the wiki is enough.

2. **Pre-emption signal for claimed speculative batches.** Demanded work has
   strict priority and can pre-empt speculation. What is the recommended way
   for a claimed-and-executing speculative batch to LEARN it was pre-empted so
   the worker can abort mid-batch (subscription row change, lease revocation,
   TTL only)? If the answer is "poll your claim row," say so and we will build
   on that; if a cheap push exists or is planned, we would prefer it.

3. **Resource-aware admission pattern.** Our placement layer reserves VRAM per
   job (worker capability records + lease budgets, guest-side tables). Is there
   a recommended pattern for encoding a resource budget so the Fabric claim
   path refuses over-budget claims, or is the intended design that guests gate
   admission entirely before/around `claim_work`? We are happy with guest-side
   gating; we ask only so we do not fight a native mechanism that already
   exists or is planned.

4. **Placement-scoring inputs.** Cheap, queryable per-queue depth/age metrics
   (or the blessed SQL against payload tables) for guest placement scoring
   (estimated completion cost = queue delay + load/swap + inference + deadline
   risk). If per-index/per-queue metrics already exist in the metrics surface,
   a pointer suffices.

5. **Conditional (post-measurement) only:** if our sustained dual-GPU ingestion
   measurement shows the guest path cannot meet the no-GPU-stall contract with
   guest-side gating alone, we may return with a narrowly-scoped primitive ask
   (e.g. native lease-with-reservation). Not requested now.

## What AI-Collab will build (no Fork change requested)

- GPU worker primitives (1660 Ti sentry + 3080 factory/precision) claiming from
  the shared queue, publishing capability/current-state records.
- A GPU Profiles manager (create/edit/delete/activate/stop; dual-profile
  activation) driving the existing UUID-pinned supervisor machinery.
- Operating modes (SENTRY/BALANCED/THROUGHPUT/PRECISION/CACHE_BUILD/DEGRADED)
  as policy records with recorded transitions.
- Deterministic idempotent chunk commit (unique key on repo_id, revision,
  chunk_hash, model_profile_id, dimensions) before enabling Fabric retries.
- The measurement harness for your acceptance gates.

## Contact / traceability

AI-Collab-v3 tracking: REQ-099/REQ-100 (intake), TRK-198 (investigation), and
the [Fabric-managed GPU scheduling design](../../../AI-Collab-v3/docs/design/fabric-managed-gpu-scheduling.md). Reply by editing
this file with a `## Fork response` section, or via the usual cross-repo
request thread.

## Fork response

Accepted as a documentation and guest-integration request. The durable answer is
in [Fabric GPU Worker Guest Integration](../vector/fabric-gpu-worker-integration.md),
with the ingestion architecture corrected in
[Fabric-Buffered Dual-GPU Vector Ingestion](../vector/fabric-buffered-vector-ingestion.md).

1. **Contract/build answer.** The three `vector.*` identifiers are
   application-level `payload_type` conventions, not three generated native
   contract types. Build 20+ is the current complete integration target. It
   contains the generic Fabric lifecycle, `FVBATCH1` packed-vector
   staging/commit substrate, and generic leased resource reservations; build
   27 additionally fixes safe speculation-column adoption.
2. **Pre-emption answer.** Subscribe to the public claimed payload row. A
   claimed/running speculative payload becomes `Cancelled`; pending/failed
   speculative work becomes `DeadLetter`. Abort cooperatively between
   microbatches. Re-read after reconnect, poll only as fallback, and retain TTL
   as the final safety net.
3. **Resource answer.** Build 20 adds a native generic leased reservation.
   Combine reservation admission and generated `claim(...)` inside one
   serializable reducer transaction. The application declares whether units
   represent VRAM; Fork does not pool GPU memory.
4. **Placement answer.** Query exact pending depth with `COUNT(*)` on the public
   payload table. Derive oldest age from the subscribed pending set or maintain
   a transactional guest queue-stat row; unordered `LIMIT 1` is not an oldest
   query. The existing speculative queue-depth metric is not a per-type GPU
   placement metric.
5. **Native primitive answer.** Historical answer superseded: the generic
   reservation, graph, scheduler, artifact, and federation facilities shipped
   under PRED-006. GPU-specific execution and placement remain application-owned.

Live verification on 2026-07-16 confirmed `127.0.0.1:3012` reports Fork build
17, commit `ed64e1d0b90041e40d082a3761a2b1b2778ba8fb`, pinned to the GTX 1660 Ti.

## Addendum (2026-07-16, TRK-199 Phase 2): speculation macro columns need default annotations

AI-Collab's idempotent chunk commit is now live (chunk_commit_receipt +
index_commit_chunk, additive publish). Blocker found for speculation adoption:
`fabric_payload(..., speculation)` adds `execution_class`,
`speculation_decision_id`, `prediction_signature` to the existing guest
payload table without default-value annotations, so publishing against a
pre-flag live database aborts and would require `--delete-data`. Request: the
macro should emit schema defaults for the speculation envelope columns
(Demanded / none / none) so guests can adopt with a hot additive publish.

## Programme findings (2026-07-16, AI-Collab TRK-199 Phase 6)

AI-Collab ran your ingestion acceptance gates guest-side (isolated substrate,
4/4 pass): no lost/duplicate chunks (interleaved-duplicate batch → exact
identities), crash/restart replay is a no-op, revision replacement leaves no
ghosts (receipts die with chunks at every deletion site), bounded receipt
state (1:1 with chunks, zero residue after teardown). Worker runners drain
their full funnel per tick (no buffer-attributable idle) and the interactive
2B lane served uninterrupted throughout (~0.35s warm rerank). Guest-side
gating met the no-GPU-stall contract — no conditional fork primitive is
requested. Standing blocker: the speculation-macro default-annotation gap
(addendum above). Field note: Qwen3-VL-Reranker-8B GGUF quality varies by
source — one Q4_K_M had a dead classifier head (all-zero scores); HF-anchor
score verification should be mandatory before the fabric routes to any
reranker artifact.

## Addendum 2 (2026-07-16, build 18 verified): speculation adoption still blocked — column ORDER, not just defaults

Verified on an isolated v2.7.0-pred006 host: re-publishing the
speculation-flagged guest module over a pre-flag database aborts with
"Reordering table ai_collab_payload requires a manual migration" — the macro
inserts the three speculation columns mid-table, which reads as a reorder
against the existing table. Refined ask: emit newly-added envelope columns
LAST (order-stable evolution) and with schema defaults (Demanded/none/none)
so live guests can adopt the flag additively.
