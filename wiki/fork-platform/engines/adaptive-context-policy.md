# Adaptive Context Policy and Item-Level Utility

> Sources: Repository-owner optimization programme, 2026-07-16; Fork Context/Fabric implementation; AI-Collab B/C calibration at `b419698f`; [PRED-004 build-17 evidence](../../raw/engines/2026-07-16-pred004-adaptive-context-substrate-build17.md); [current release state](../../docs/release-state.json)
> Raw: [Adaptive context policy programme](../../raw/context/2026-07-16-adaptive-context-policy-programme.md); [Predictive Fabric B/C calibration evidence](../../raw/workflow/2026-07-16-predictive-fabric-bc-calibration.md)
> Commit: ed64e1d0b
> Updated: 2026-07-30
> Status: Architecture retained; all six generic Fork interfaces implemented by PRED-004 in build 17

## Overview

The strongest optimization is not more context. It is a small context portfolio
that is task-aware, role-labelled, available before demand, exactly resolvable,
and measured from candidate through task outcome. The first B/C calibration
shows a real efficiency signal, but its one precision failure and zero admission
threshold make quality-aware abstention and item-level evidence the next gates.

The canonical spike and frozen matrix are in the
[PRED-003 architecture plan](../../docs/plans/PRED-003-adaptive-context-policy-spike.md).

## What already exists

Fork already supplies exact token counting, token-bearing Context candidates,
Hot/Warm/Cold compaction and recovery, frame replay, typed digest-bound source
spans, exact unfolding, prediction replay, demand-first pre-emption, automatic
trips, content-addressed staging, packed vector batches, multi-vector retrieval,
and CMT evidence.

At the 2026-07-16 experiment baseline, AI-Collab's builder estimated tokens as
characters divided by four and its tool view flattened source bodies instead of
exposing genuine progressive references. That dated observation motivated the
Fork contracts below; it is not a claim about AI-Collab's current source.

## Application-owned policy

AI-Collab owns task classification, context profiles, modification versus
verification roles, facet recall/fusion, diversity selection, builder
parallelism and caches, progressive tool UX, item-use joins, experiment control,
and GPU routing. These are repository and agent semantics, not database-engine
policy.

The most direct quality correction is to separate relevance from edit
necessity. Impact frames must distinguish the modification frontier,
verification frontier, downstream consumers, reusable dependencies, and
uncertain candidates. A relevant existing dependency is not automatically a
file to modify.

## Candidate Fork contracts

PRED-004 promoted the six generic interfaces into the
[Adaptive Context Substrate](adaptive-context-substrate.md):

1. signed parameterized prediction actions;
2. conservative quality-risk admission before efficiency optimization;
3. durable resolvable source references with scope/revision/range/digest/
   resolver/liveness identity;
4. calibrated confidence and multi-channel provenance;
5. content-addressed partial prediction stages;
6. authoritative item-to-outcome CMT feedback.

They remain application-neutral. Their implementation does not transfer task
classification, retrieval policy, context roles, model choice, or GPU routing
into Fork.

## Experiment order

Instrument item and phase telemetry first. Then isolate task profiles, context
roles, multi-facet recall, diversity, assembly execution, progressive unfolding,
admission/abstention, earlier start points, and declared 3080 escalation. Change
one factor per ablation, use independent state clones and randomized balanced
repetitions, and report suite-level paired outcomes.

Quality dominates every efficiency metric: a treatment-specific deterministic
failure blocks promotion. A fast failed task is not useful prediction, and a
positive aggregate cannot hide a weak task family.

## GPU policy

The original 2026-07-16 experiment baseline kept the RTX 3080 cold and used the
GTX 1660 Ti as sentry, escalating complete-profile bulk/image ingest, large
rerank, offline multi-vector, shadow-teacher, or difficult-query jobs. That is
an AI-Collab policy baseline, not a Fork invariant. After the 2026-07-30
build-36 promotion, the observed database runtime has CUDA compiled but GPU discovery disabled and
`accelerated=false`; it therefore has no current device placement. Whichever
external policy is active, never merge scores from different model profiles;
retain each complete ranking and provenance.

## See Also

- [Context Engine](context-engine.md)
- [Adaptive Context Substrate](adaptive-context-substrate.md)
- [Predictive Payload Speculation](predictive-payload-speculation.md)
- [Fabric-Buffered Dual-GPU Vector Ingestion](../vector/fabric-buffered-vector-ingestion.md)
- [AI-Collab A/B/C Benchmark Reference](../workflow/ai-collab-abc-benchmark-reference.md)
- [Evaluation Protocol](../vector/evaluation-protocol.md)
