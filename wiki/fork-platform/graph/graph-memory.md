# Graph Memory — native edges, traversal, gates, capabilities

> Sources: [docs/FORK-OVERVIEW.md](../../docs/FORK-OVERVIEW.md) §4; [docs/graph-memory.md](../../docs/graph-memory.md); GNCE completion docs

## Overview

Directed, labeled, temporal edges live natively alongside relational rows
and vector indexes — no separate edge table. Edges are inserted by the
endpoints' unique keys, carry connector/port-aware metadata (type, status,
confidence/strength, evidence + provenance refs), and traversal returns
**paths**, not just nodes, with depth/visit budgets. Graph changes emit
subscription deltas like any other write.

## Guest API shapes

- `graph_insert_edge(a, b, label, ...)` on a table's unique column.
- `graph_neighbors(seed, label, depth)` — depth-1..n neighbor sets.
- Weighted traversal (`..._weighted`, `spacetime_10.10` ABI block): edges
  below a `min_weight_bps` confidence are skipped — the recall engine's
  `min_edge_weight` rides this.
- Reasoning-query modes filter path results for evidence / contradiction /
  blocked-gate / promotion / stale-knowledge / provenance cases.

## Staged mutations + gate outcomes (GNCE substrate)

Transactions can STAGE graph edge mutations and resolve
allowed/rejected/blocked gate outcomes atomically; unresolved staged
mutations are auto-blocked at merge; only allowed edges reach the native
graph index. This is the trust-system substrate: an edge can require a
decision before it exists.

## Capability policies

`st_graph_capability` rows constrain operations (GraphRead, GraphMutation,
ActionExecution, …) per table/edge-label/identity. The permission check
honors SAME-TX policy writes (a freshly installed restriction constrains
the installing transaction — this was the FIX-GRAPHCAP fail-open bug).
Trust propagation itself is deliberately a GUEST-computed ledger, not a
host engine (measured host propagation ≈ chance; weighted traversal +
scheduled reducers give guests the loop).

## See Also

- [Causal memory overview](../cmt/causal-memory-overview.md)
- [Memory engine](../engines/memory-engine.md)
