# Owned Fork Regression Isolation

> Sources: TEST-001 repository inventory, implementation evidence and prior PLAYER-001 isolation work, 2026-08-09 to 2026-08-10
> Raw: [TEST-001 Research](../../raw/architecture/2026-08-09-test-001-regression-isolation-research.md), [TEST-001 Evidence](../../raw/architecture/2026-08-10-test-001-owned-regression-isolation-evidence.md), [PLAYER-001 Isolation Research](../../raw/architecture/2026-08-09-player-001-isolated-runtime-database-research.md)
> Commit: c0cea44
> Updated: 2026-08-10

## Problem

Historical fixed database names make a regression depend on an old schema and
whatever state previous runs left behind. A hard-coded shared listener also
lets a restart test interrupt production. Random event IDs prevent duplicate
collisions but do not reset clocks, unique state, leases, inventories or schema.

## Decision

Stateful unattended regressions use one current-module Fork process per test.
The repository helper owns a random loopback port, token-scoped data directory,
listener/launcher process identities and a test database. Teardown acts only
after validating that complete ownership context.

The helper is adapted rather than replaced: it rebuilds stale WASM, publishes
the current module, verifies listener ownership, canonicalizes complete SQL
rows, restarts in place and removes only its token directory. Production
qualification remains separate and explicit.

## Required invariants

- Current source/WASM, never an assumed historical schema.
- Random loopback port and token-owned data root for each stateful test.
- Effective server/database passed through companion, OpenMW, cognition and
  voice seams.
- Restart operates only on the owned context and preserves its exact data root.
- `try/finally` teardown on every ordinary success or failure path.
- Refuse teardown/restart when ownership fields, path prefix/token or process
  identity do not match.
- Static inventory prevents new version-default/shared-endpoint regressions.

## Evidence boundary

At research time, 47 PowerShell tests referenced historical databases or port
3012. The released inventory covers 71 scripts: 52 stateful tests own their
runtime, 12 are documented Fork-independent cases, one is explicitly
production-only, and one literal fixture proves shared opt-in rejection. Zero
historical/shared unattended defaults remain.

The 12-case representative runner passed direct reducers, restart/replay,
voice worker/companion recovery, real OpenMW interaction, save/load and
post-commit outage/reconnect. It canonically fingerprinted all 113 public user
tables in production v202 before and after; both digests were
`e78bdde9c6938e96793797ae3a490602eb2419cfa801eceab1170138d02591d4`.
Owned runtime-directory and standalone-process sets were unchanged, and final
engine/Lua/dead-letter counts were zero.

## See Also

- [Reliable OpenMW/Fork Bridge Protocol](../architecture/reliable-bridge-protocol.md)
- [Repository Continuity and Knowledge Workflow](repository-continuity-system.md)
- [Production Living-World Gap-Closure Programme](../architecture/system-gap-closure-programme.md)
