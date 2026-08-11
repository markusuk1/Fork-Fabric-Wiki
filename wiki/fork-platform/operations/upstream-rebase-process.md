# Upstream Rebase Process (keeping the fork current)

> Sources: [docs/FORK-REBASE-002-PLAN.md](../../docs/FORK-REBASE-002-PLAN.md); AUD-REBASE-002; tools/upstream-drift.sh

## Overview

The fork nests upstream SpacetimeDB under `SpacetimeDB/` and upgrades by
subtree-merging upstream tags on an isolated worktree branch — the main
line is untouched until the full validation ladder is green. Currently
based on **upstream v2.7.0-hotfix1** (REBASE-002).

## The process (proven twice: 2.4.1→2.6.0, 2.6.0→2.7.0-hotfix1)

1. **Drift first**: `bash SpacetimeDB/tools/upstream-drift.sh` → upstream
   movement, fork divergence surface, collision set, green/amber/red
   verdict. Re-run immediately before starting (upstream moves).
2. Worktree + rollback tag (`pre-<ver>-rebase`); per-file intent dossier
   for every collision file (`git log upstream-<base>..HEAD -- <file>` —
   commit messages carry the why; resolution preserves intent, never just
   syntax).
3. Subtree-merge the tag; compile-driven resolution, buckets: mechanical
   (take upstream + regenerate), semantic (schema/subscription clusters),
   fork-boundary migrations (e.g. the engine-crate split absorbed ~1,100
   fork lines via rename detection).
4. **Validation ladder, all green before promotion**: two-scope check;
   every engine suite; bindings + trybuild; web all-features; demo wasm
   builds; a live publish smoke; **the 1M vector gate bench reproduced**;
   drift re-run vs the new base expecting 0 collisions.
5. Promote (fast-forward the integration line), green tag, binaries to a
   NEW staged folder, regenerate the manual's pinned commit.

## Standing rules

Upgrade commits contain upgrade work ONLY (no features ride along).
Inherited-red upstream tests get `#[ignore]` with evidence they were red on
fork main before the rebase — and get revisited (one such "inherited" red,
the graph-capability test, turned out to be a real fork bug later fixed).

## See Also

- [Staged builds and compatibility](staged-builds-and-compat.md)
- [Vector search architecture](../vector/vector-search-architecture.md)
