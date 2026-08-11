# Causal Evidence Subscriptions (CMT-SUB)

> Sources: fork agents, 2026-07-11; GRAPH_LANG (GNCE) report-back, 2026-07-11
> Raw: [causal subscription contract response](../../raw/cmt/2026-07-11-causal-subscription-contract-response.md)

## Overview

The decided contract for "pushed updates that carry their cause": clients
subscribe to `st_causal_event` / `st_causal_edge` **alongside their data
tables**. Because `record_causal_event_mut_tx` writes causal events and
`CausedBy` edges as ordinary inserts **in the same transaction** as the data
change they explain, the causal rows are already in that transaction's
`TxData` — so a subscriber receives the data change and its causal evidence
in the **same `TransactionUpdate`**. Atomic pairing, no race window, no
client-protocol change. **DELIVERED 2026-07-11** (CMT-SUB, this section is
the as-built record).

## As built (2026-07-11)

- **Both causal tables are `TableAccess::Private`**
  (`crates/datastore/src/system_tables.rs`) — SQL *and* subscriptions
  tighten to the database owner through the one `has_read_access` check
  (permission matrix: Public → anyone, Private → owner-only;
  `owner_permissions`, `crates/lib/src/identity.rs`). Clients need the
  **actual publish identity**; fresh-minted (`--server-issued-login`)
  identities lost the read access they accidentally had.
- **The server never had a resolution gap.** Subscription compile goes
  through the same `SchemaViewer` as SQL (`crates/core/src/sql/ast.rs`) and
  was already accepting causal-table subscriptions. GNCE's
  `table not found in schema` came from the **CLI output formatter**, which
  rendered rows via the module def — system tables are never in it. The CLI
  now falls back per unknown table to one `SELECT … LIMIT 1` (the SQL
  response carries a self-contained schema) and caches the row type
  (`SchemaSource`, `crates/cli/src/subcommands/subscribe.rs`). SDK
  subscriptions never had the problem.
- **Evidence tests** (`module_subscription_actor.rs`):
  `causal_evidence_arrives_in_the_same_update_as_its_transaction` (owner
  subscribes to user table + both causal tables; one tx writes a user row +
  two chained events; ONE update carries user row, 2 events, 1 edge) and
  `causal_tables_reject_non_owner_subscriptions` (non-owner gets the same
  error as for a nonexistent table — existence not leaked).

Open audit flag (unchanged): other sensitive system tables likely also ride
the Public default — the explicit Private list is short.

## Diagnosis lesson

Two agents both misread the failure layer: "table not found in schema" was
assumed server-side by GNCE (auth) and by the fork (schema view). The string
lives in `crates/cli/.../subscribe.rs` — one grep would have settled it.
**Grep for the literal error string before theorizing about which layer
produced it.**

## Rejected alternatives (do not re-litigate)

- Wire extension on subscription updates: breaks every SDK for something
  the same-tx pairing already provides. Rejected.
- "Causal delta for tx offset N" query: reintroduces the push-then-query
  race the feature exists to eliminate. Rejected.
- CMT-009's `evaluate_causal_memory_subscription_delta` is not dead code:
  it is the building block for future causal-*query*-shaped subscriptions
  (impact slices, replay sets), out of scope for the evidence contract.

## See Also

- [Causal-query wire contract](causal-query-wire-contract.md)
