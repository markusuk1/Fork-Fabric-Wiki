# CINT — Code Intelligence (live code-symbol graph)

> Sources: [docs/FORK-OVERVIEW.md](../../docs/FORK-OVERVIEW.md) §6; COMP-CINT-001..006; REPO-IDX completion docs
>
> Updated: 2026-07-29

## Overview

A live code-symbol graph inside the database: repositories → snapshots →
files → symbols → occurrences, plus diagnostics, analysis runs, and action
plans, with confidence-scored CALLS edges. Multi-language via a
tags-query extractor; per-file incremental re-index
(`code_remove_file` / `code_reindex_file`) so a watcher can keep it current
cheaply. ai-collab-v3's repo indexers feed exactly this (one is watching
this repo right now).

Fork build 35 adds an optional bounded native microbatch path through ABI
`spacetime_10.13`. It preserves ordered per-file identity and exact result rows
while collapsing submission and observation into one guest/host flow. It is
enabled by default, hot-toggleable through unified host configuration, and
inert until a guest calls it. The single-file interfaces remain supported.

## Guest query surface

- `code_resolve_symbol(snapshot, name)` — name → symbol id.
- `code_callers` / `code_callees` / `code_references` /
  `code_definition` — return `CodeQueryResult { symbols, occurrences,
  graph_edges, symbol_infos }`; `symbol_infos` carries kind tag, canonical
  name, defining file, and definition byte span for fine-grained citations.
- `code_change_blast_radius` — bridges CINT into CMT: impacted symbols +
  attached diagnostics/action plans + the causal neighborhood (returned via
  `CausalQueryResult`, see the wire-contract page).

## Scale note

Validated at 10k/50k/100k symbols (REPO-IDX-3); the recall/latency notes
live in the completion doc. File-scoped symbol identity means renames
re-index one file, not the world.

## See Also

- [Bounded native CINT batch ingest](bounded-native-cint-batch-proposal.md)
- [Causal memory overview](causal-memory-overview.md)
- [Causal-query wire contract](causal-query-wire-contract.md)
