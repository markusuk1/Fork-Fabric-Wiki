# Adaptive Context Substrate

> Sources: [PRED-004 request](../../docs/requests/PRED-004-adaptive-context-substrate.md); [PRED-004 implementation plan](../../docs/plans/PRED-004-adaptive-context-substrate.md); [build-17 as-built evidence](../../raw/engines/2026-07-16-pred004-adaptive-context-substrate-build17.md); [build-17 production promotion](../../raw/operations/2026-07-16-pred004-build17-production-promotion.md); [build-27 runtime reconciliation](../../raw/operations/2026-07-24-build27-runtime-reconciliation.md)
> Commit: ed64e1d0b (feature release); carried by build 27 (`6e20ac8d9`)
> Updated: 2026-07-24
> Status: Implemented and validated in Fork build 17; its historical LAN host is now preserved offline as a disabled legacy service

## Overview

Build 17 turns the candidate interfaces from the
[adaptive context policy architecture](adaptive-context-policy.md) into generic
Fork contracts. It does not choose task types, retrieval channels, context
roles, models, or GPUs. Instead, it makes application decisions verifiable,
replayable, quality-gated, exactly budgeted, coalescible, and independently
disablable.

## Context evidence and exact resolution

Every context item has a canonical identity and an append-oriented,
hash-chained lifecycle from candidate through selection, presentation, use, and
task outcome. Legal transitions and exact duplicate feedback are deterministic;
conflicting or out-of-order evidence fails closed. Applications may link the
events to CMT without making the learner or CMT gate part of a demanded request.

`ResolvableSourceRef` v1 binds scope, repository revision, reference kind,
source identity, byte range, content digest, resolver identity/version, and
liveness. Resolution succeeds only when all authoritative evidence agrees. A
stale repository, changed resolver, wrong range, dead reference, or byte digest
mismatch cannot silently return nearby or current content.

Exact token evidence binds the `o200k_base/v1` tokenizer identity. The bounded
batch API accepts no more than 1,024 items, 1 MiB per item, or 4 MiB aggregate.
Context Demo uses those exact counts in assembly, expansion, and contraction;
character estimates are not load-bearing.

## Safe adaptive actions

Prediction actions are canonical allowlisted parameter maps with explicit
limits. Their policy digest is included in the real predictive signature, so a
policy revision invalidates stale work before promotion. The admission order is
deliberate:

1. validate action and capability;
2. require sufficient quality-risk evidence and pass its thresholds;
3. only then compare expected efficiency utility.

Weak evidence causes deterministic abstention. A fast candidate cannot outrank
a quality failure.

## Stage coalescing and receipts

Embedding, recall, expansion, rerank, and frame stages use content-addressed
identities over inputs, dependencies, revisions, policy, model/profile,
resolver, and implementation. An exact completed receipt is reused. Exact
concurrent work shares one lease. A digest collision or any incompatible
dependency fails closed rather than returning partial or stale work.

This is not an unbounded cache. Durable database policy owns replay truth;
bounded leases, receipts, and decisions provide the generic coordination
substrate while applications own orchestration.

## Rerank calibration

The fixed experiment matrix crosses candidate fractions
100/75/50/25/12.5 percent with batches 1/2/4/8/16/32/64. All 35 cases bind one
retrieval-selection digest and record latency, documents per second, peak VRAM,
recall, MRR, NDCG, and final-selection agreement in deterministic fixed-point
units. Promotion requires baseline evidence and every quality/resource gate;
the winner is chosen quality-first and then by latency with stable tie-breaks.

The release smoke promoted a 50-candidate, batch-16 case at 600 ms against a
4,764.9 ms 100-candidate, batch-1 baseline while retaining the fixed retrieval
identity. That proves the contract and reducer path, not a universal model
speedup; AI-Collab must run the full matrix on its actual reranker and corpus.

## Unified controls

The standalone `[adaptive-context]` section independently controls item
telemetry, source resolution, exact token batching, parameterized actions,
quality gating, and stage reuse. It also supplies optional maximum stage bytes,
maximum inflight stages, and an emergency stop. Fresh defaults are enabled.

Host control is deny-only: it can reduce local capability but cannot grant what
the durable database policy denies. `/v1/status` exposes configured and
effective values. Emergency stop forces every effective capability false
without rewriting the configured intent, so clearing the stop is reversible.

## Release and compatibility

Fork build 17 is staged immutably as `v2.7.0-pred004-r2` from commit
`ed64e1d0b`. The locked CUDA host and exact Context Demo/Fabric Demo WASMs were
built from that pin, published on an isolated host, restarted, rolled back to
build 16 against preserved data, and restored. Exact historical item-event and
stage-completion retries are no-ops; a conflicting stage receipt fails closed.
The unchanged demanded selector had no detected p95 regression in seven paired
build-15/build-16 release rounds.

The LAN NSSM service historically ran that exact immutable build-17 host. It is
now stopped and startup-disabled under
[LAN-LEGACY-001](../operations/legacy-lan-host-retirement.md), with its data and
configuration preserved.
The promotion preserved its service identity, parameters, data/JWT locations,
configuration hash, CPU-only GPU policy, and 12-tool `agent-starter` surface.
All six adaptive capabilities reported effective true, emergency stop reported
false, and a second supervised restart passed. On 2026-07-24 AI-Collab's
independently owned local node reported build 27 with pinned RTX 3080 placement;
build 27 carries the build-17 adaptive substrate plus later orchestration,
Security Centre, and migration fixes. See
[Live Deployment](../operations/live-deployment.md) for current stamps.

## See Also

- [Adaptive Context Policy and Item-Level Utility](adaptive-context-policy.md)
- [Context Engine](context-engine.md)
- [Predictive Payload Speculation](predictive-payload-speculation.md)
- [Host Configuration Management](../operations/host-configuration-management.md)
- [Staged Builds & Compatibility](../operations/staged-builds-and-compat.md)
- [AI-Collab A/B/C Benchmark Reference](../workflow/ai-collab-abc-benchmark-reference.md)
