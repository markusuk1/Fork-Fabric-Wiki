# CMT — Causal Memory Twin (the "why" layer)

> Sources: [docs/FORK-OVERVIEW.md](../../docs/FORK-OVERVIEW.md) §5; fork-ingest-manual §6.3; COMP-CMT-001..010; PRED-001 implementation and rollback evidence
> Raw: [Adaptive CMT-backed speculative execution](../../raw/vector/2026-07-15-adaptive-cmt-speculative-execution.md); [PRED-001 as-built evidence](../../raw/vector/2026-07-15-pred001-as-built-predictive-fabric.md)
> Commit: 88d331a18
> Updated: 2026-07-15

## Overview

A native causal layer over transactions: meaningful changes stamp **events**
attached to real rows, events link by causal edges, and the result is
queryable for *why / what-if / blast-radius*. A normal database remembers
facts; CMT remembers the story behind them.

## The query surface (guest)

| Call | Question it answers |
|---|---|
| `causal_why(event, depth)` | why did this happen (walk causes backward) |
| `causal_causes` / `causal_effects` | upstream / downstream chains |
| `causal_impact_slice(table, row_ptr, depth)` | what did this row's changes affect |
| `causal_trace_path(a, b, depth)` | is there a causal path between two events |
| `causal_events_for(col_val)` | events touching a row/key |
| `causal_begin_counterfactual(base, change)` + `causal_replay_diff(run)` | run base vs changed inputs, diff the outcome |
| `code_change_blast_radius(snapshot, seeds, depth)` | impacted code symbols + diagnostics + action plans |

All return `CausalQueryResult` — see the wire-contract page for the 8-array
shape and compat rules.

## Recording

`spacetimedb::causal_record_event(row, kind, cause)` guest-side (engines do
this automatically — memory verbs stamp tags 17–24, fabric 25–34, context
35–44, perception 45–49, and predictive execution 50–59). The global
integration registry imports every owner and forbids collisions, silent holes,
and non-contiguous allocations. Events +
`CausedBy` edges are ordinary same-transaction inserts into
`st_causal_event`/`st_causal_edge` — which is what makes causal evidence
subscribable (see CMT-SUB page).

The persisted event-kind sum remains its historical 16 variants. Registered
tags above that range use physical `InternalCommit` plus an atomic exact-semantic
envelope in the existing opaque metadata column (`FCMTP001`, registry version,
tag, canonical name). This survives rollback to builds 11 and 9 without a
catalog migration. Predictive events also use explicit relations and direction,
so an actual demand is never falsely recorded as caused by its prediction.

New engine blocks must be reserved through
`crates/smoketests/tests/cmt_tag_registry.rs`; per-crate comments are not an
allocation authority.

## Replay capsules

Capture reducer invocation/args/module hashes for deterministic
re-execution — the basis of counterfactuals and the Fabric's control-plane
replay.

## See Also

- [Causal-query wire contract](causal-query-wire-contract.md)
- [Causal evidence subscriptions](causal-evidence-subscriptions.md)
- [Code intelligence](code-intelligence.md)
- [Fabric-buffered dual-GPU vector ingestion](../vector/fabric-buffered-vector-ingestion.md)
- [Predictive payload speculation](../engines/predictive-payload-speculation.md)
