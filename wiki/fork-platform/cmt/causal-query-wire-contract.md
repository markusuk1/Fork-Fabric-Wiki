# Causal-Query Wire Contract (guest ↔ host)

> Sources: fork agents, 2026-07-10
> Raw: [blast-radius wire fix response](../../raw/cmt/2026-07-10-blast-radius-wire-fix-response.md)

## Overview

Every causal-memory query (`causal_why`, `code_change_blast_radius`, the
counterfactual queries, …) returns one BSATN product whose **field order is
the wire contract** between the host projection
(`causal_query_rows_to_product_value`, `crates/core/src/host/instance_env.rs`)
and the guest `CausalQueryResult` (`crates/bindings/src/causal_code.rs`).
Since FIX-BLAST-WIRE (commit `631c30e5d`, 2026-07-11) the product carries
**all 8 row kinds** the host aggregate computes.

## The 8 arrays, in order

1–3. `events`, `edges`, `blast_radius` — the legacy prefix. Old modules that
decode only these keep working: they read a prefix of the product and never
touch trailing bytes.

4. `code_graph_edges` — the impacted-symbol set from
`code_change_blast_radius` (same row shape as `CodeQueryResult.graph_edges`).
5. `code_diagnostics` — diagnostics attached to the impacted set.
6. `code_action_plans` — action plans attached to the impacted set
(metadata blob NOT projected; read the system table).
7. `replay_capsules` — from the counterfactual queries; addressable identity
only (arg blobs, caller identity, hashes stay host-side — size + privacy).
8. `counterfactual_runs` — id/status/divergence/timing.

## Compatibility rules

- **Old module + new host: fine** (prefix read).
- **New module + old host: breaks** — the guest expects 8 arrays, an old
  host sends 3. Pin bindings and host from the **same fork commit**, always.
- The arities are locked by the host test
  `causal_query_projection_carries_all_row_kinds`; a field drift fails CI
  instead of silently emptying guest results.

## The failure mode this contract prevents

Before the fix, the host computed blast-radius code rows and silently
dropped 5 of the 8 kinds at the projection — guests read an empty-looking
result for a demonstrably non-empty blast set, and nothing errored. If a
causal query ever "returns empty" for data that visibly exists, suspect the
wire projection before the query logic.

## See Also

- [Causal evidence subscriptions](causal-evidence-subscriptions.md)
