# AI-Collab A/B/C Benchmark Reference

> Sources: Repository-owner proposal and grounded Fork/AI-Collab-v3 inspection, 2026-07-15; AI-Collab-v3 B/C calibration at `b419698f`, 2026-07-15
> Raw: [AI-Collab A/B/C benchmark reference](../../raw/workflow/2026-07-15-ai-collab-abc-benchmark-reference.md); [Predictive Fabric B/C calibration evidence](../../raw/workflow/2026-07-16-predictive-fabric-bc-calibration.md)
> Commit: 4d160e7f5
> Updated: 2026-07-16
> Status: B/C calibration observed; single repetition and not a promoted counted product claim; 25-task A arm not yet run

## Overview

The benchmark must answer two separate causal questions: what AI-Collab adds
over the same agents running directly, and what build-15 predictive Fabric adds
over AI-Collab without speculation. The reliable design holds every other
variable constant, runs B and C on the same build-15 host, and changes only the
AI-Collab/Fabric behavior named by each arm.

## Headline arms

| Arm | Configuration | Interpretable effect |
|---|---|---|
| A — direct control | Same model, prompts, tools, worker count, concurrency, hardware, budgets, tasks and grading; bypass AI-Collab context, memory, collaboration and Fabric dispatch | Direct-agent baseline |
| B — AI-Collab, prediction Off | AI-Collab on build 15 with an isolated but logically identical state snapshot; predictive Fabric explicitly Off | `B - A`: AI-Collab's net contribution |
| C — AI-Collab, prediction Active | Same build, AI-Collab revision, snapshot, model profiles and workload as B; predictive Fabric Active | `C - B`: incremental predictive-Fabric contribution |

`C - A` is the combined-system effect. B must not use build 9: otherwise the
comparison confounds predictive execution with every host change between builds
9 and 15.

## Observed 25-task B/C calibration

AI-Collab implemented the controlled runner and live C treatment, then committed
the canonical [adjudicated report](../../../AI-Collab-v3/artifacts/eval/abc/faithful-bc-adjudicated-latest.md)
at `b419698f`. Two shared v2 failures were excluded because their hidden graders
depended on an unrelated broken fixture. Across the 23 valid tasks:

| Metric | B: prediction Off | C: prediction Active | C versus B |
|---|---:|---:|---:|
| Passed | 23/23 | 22/23 | -1 task |
| Input tokens | 5,181,827 | 4,384,970 | -15.38% |
| Uncached input tokens | 499,459 | 402,506 | -19.41% |
| Output tokens | 83,082 | 74,255 | -10.62% |
| End-to-end runtime | 2,156,922 ms | 1,952,425 ms | -9.48% |
| Agent tool calls | 52 | 25 | -51.92% |
| Source-bearing native frames | 0 | 23/23 | +23 |

C's one valid miss was an over-broad impact edit set, not a retrieval outage.
The result is promising efficiency evidence with a concrete precision cost; it
does not justify hiding the quality delta. All recorded tools succeeded and
returned non-empty results.

This calibration does not complete the headline A/B/C claim. It used one
repetition, ran only B and C, executed them sequentially against one shared
database and byte-identical stored indexes, and started timing after index
readiness. It therefore does not measure A versus B, indexing time, parallel
dual-GPU indexing, or packed-vector sink throughput.

## AI-Collab benchmark foundation

AI-Collab began with a shelved
[ON-versus-OFF benchmark brainstorm](../../../AI-Collab-v3/docs/brainstorm/agent-benchmark-harness.md)
and an implemented [SWE task pilot](../../../AI-Collab-v3/tools/swe-tp/run-pilot.mjs).
The runner provides `no-context`, `ours-ci-off`, and `ours-ci-on` arms, paired
task output, gold-file localization, patch capture, objective test grading, and
failure attribution. Its current 12-instance
[scorecard](../../../AI-Collab-v3/tools/swe-tp/scorecard.json) is useful harness
proof but too small and noisy to establish uplift: the arms resolved 9/12,
8/12, and 8/12.

It now has a reusable [controlled A/B/C benchmark system](../../../AI-Collab-v3/wiki/architecture/controlled-abc-benchmark.md)
with frozen task/catalog/template identities, independent subject sandboxes,
hidden graders, capability preflight, immutable evidence, paired analysis, and
live Fabric frame delivery. The 25-task B/C run is its first faithful
calibration at useful breadth.

The Fork's 7.11x median token reduction is not an A-versus-B AI-Collab result.
It proves selective exact source assembly: selective and raw-doc inputs both
scored 9/10, while equal-budget leading truncation scored 0/10. The A/B/C
benchmark must measure complete task trajectories rather than reuse that number
as a proxy.

## Implemented AI-Collab arm C

The earlier integration gap is now closed for the benchmark treatment.
AI-Collab's C arm admits only live Fabric prediction receipts from a frozen,
allowlisted template-set hash and materializes bounded prefetch artifacts for
the agent. Capability preflight fails closed rather than silently falling back
to the B path. The report proves source-bearing native frames on every valid C
task.

This does not imply that every proposed downstream integration is complete.
Packed vector staging/commit for high-throughput indexing and identical-profile
dual-GPU queue workers remain separate acceptance targets. The calibration
timer explicitly excluded indexing.

Good first templates are repeated chains with measurable overlap: repository
scan → chunk → embed → vector commit; query → expanded retrieval → rerank →
context pack; and agent handoff → graph impact analysis → likely test selection.
Known deterministic steps should still bypass prediction when direct dispatch is
cheaper.

## Workload policy and the SWE-bench disagreement

AI-Collab's shelved design selected SWE-bench Verified for paired, objective
test grading. Fork's [Evaluation Protocol](../vector/evaluation-protocol.md)
retired SWE-bench and LoCoMo for primary quality claims because their pre-cutoff
tasks may be memorized. Both positions are recorded rather than silently
collapsing one into the other.

Use two labelled suites:

- **Primary causal suite:** runtime-generated ground truth or genuinely
  post-cutoff repository tasks, paired across all arms.
- **Continuity suite:** the existing AI-Collab SWE harness, retained for
  regression continuity and objective patch/test grading, explicitly marked as
  contamination-sensitive.

Prediction also requires sequential structure. A random set of independent bug
fixes may measure AI-Collab context quality but offer no repeated follow-up to
predict. Include large-repository indexing, repeated retrieval/reranking/context
assembly, and multi-agent handoff workloads alongside outcome tasks.

## Controls against false uplift

- Clone clean database, vector-index, memory, predictor and provider-cache state
  per arm; never let one arm warm another.
- Hold model/provider revision, prompts, tool permissions, worker count,
  concurrency, GPU placement/profile, token/turn limits and repository commit
  constant.
- Randomize or Latin-square arm order per task to distribute provider and
  machine drift.
- Report cold and warm behavior separately.
- Train/calibrate adaptive state on a disjoint workload, snapshot it, and start
  evaluation arms from declared equivalent state.
- Score with gold tests and deterministic evidence before any model judge.
- Preserve full trajectories and failure taxonomy, not only aggregate pass
  rates.

Shadow mode is a calibration instrument, not a fourth headline arm. It records
what would have been predicted without executing speculative work, allowing
template and threshold validation before Active measurements.

## Measurements and interpretation

Primary outcome measures are resolved tasks, regression tests and failure
class. Efficiency measures are total/cached input, output and reasoning tokens
per resolved task, wall time, model cost, turns, agent waiting, interventions,
duplicate work, collisions and rework. Retrieval measures include gold-file
localization, recall at K, and precision of use—whether an agent actually edits
or relies on returned evidence.

C adds prediction admissions, hits, misses, invalidations, pre-emptions,
reclaimed bytes, speculative compute/bytes, measured latency saved or lost,
promotion/commit latency, vector throughput, GPU utilization, demanded
p50/p95/p99, emergency/automatic trips and reason codes.

A useful C result need not improve every dimension. It may preserve task quality
and tokens while reducing waiting or indexing latency. Report that honestly.
Likewise, positive prediction hit rate without end-to-end benefit is not a win;
it identifies overhead or a downstream bottleneck.

Use paired task outcomes with confidence intervals and a paired binary test for
resolved/not-resolved; use paired bootstrap intervals for token, latency and
cost deltas. The existing 12-task pilot remains harness evidence only.

## Domain ownership and handoff

This benchmark is primarily AI-Collab domain work. AI-Collab owns agent
execution, models/profiles, GPU workers, task harnesses, state isolation and
end-to-end interpretation. Fork owns the reusable Off/Shadow/Active controls,
canonical identity, admission, demand-first pre-emption, quarantine/exact
promotion, vector staging/commit, replay and metrics. Fork should accept changes
only when the benchmark exposes a substrate defect or missing generic primitive.

## See Also

- [Predictive Payload Speculation](../engines/predictive-payload-speculation.md)
- [Fabric-Buffered Dual-GPU Vector Ingestion](../vector/fabric-buffered-vector-ingestion.md)
- [Context Engine](../engines/context-engine.md)
- [Evaluation Protocol](../vector/evaluation-protocol.md)
- [Agent Workflow & Standards](agent-workflow-and-standards.md)
- [Adaptive Context Policy and Item-Level Utility](../engines/adaptive-context-policy.md)
