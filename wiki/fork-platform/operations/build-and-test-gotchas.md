# Build & Test Gotchas (this Windows machine)

Updated: 2026-08-12

> Sources: fork-ingest-manual §10; hard-won session records (memory notes)

## Overview

The environment traps that repeatedly cost hours until written down. Check
this page BEFORE debugging a "broken" build.

## The list

| Symptom | Truth | Fix |
|---|---|---|
| `cargo check --workspace` fails on `temporal_capi` | upstream feature-unification breakage, not your change | `bash SpacetimeDB/tools/check.sh` (two-scope: `--exclude spacetimedb-cli` + `-p spacetimedb-cli`) — never bare workspace check |
| `os error 4551` on freshly-linked build scripts / proc-macro DLLs / test exes; bogus `E0463 can't find crate` on rmeta | Windows AppControl blocks fresh binaries — OS policy, not code | delete the named `target/**` artifact, rebuild/relink |
| Test exe "failed" with no test output | same AppControl block on the test binary | `rm` the named `deps/*.exe`, rerun |
| `cargo ... \| grep`/`tail` shows nothing | piped cargo output gets swallowed unreliably | redirect to a file, then Read it; `\| tail` also masks exit codes |
| python `subprocess.run(['bash', ...])` results look vacuous | resolves to the WSL stub, not Git Bash — checks silently no-op | only Bash-tool runs are real |
| Cargo fills C: or a worktree-local `target/` | wrong or duplicate target root | use only `CARGO_TARGET_DIR=D:/CargoTargets/Fork/wld-active` with `CARGO_INCREMENTAL=0`; C:, worktree-local targets and parallel roots are forbidden |
| `Stop-Service`/`nssm` access denied | session isn't elevated | one-shot elevated script via `Start-Process -Verb RunAs` (user clicks UAC); pattern: `lan-deploy/roll-dims1536.ps1` |
| Anonymous republish 403s | each `--anonymous` publish mints a NEW identity; you can't update a db you "own" anonymously | fresh name, or wipe + republish (the roll drill) |

## Workspace discipline

[AGENTS.md](../../AGENTS.md) is the rulebook (CRAFTE: Correct, Robust, Aligned,
Faithful-to-method, Tested, Efficient — efficiency never means scope cuts).
Read NOTICEBOARD.yml at session start; append handovers there. Keep
[TRACKING.md](../../TRACKING.md) + [CHANGELOG.md](../../CHANGELOG.md) current on every completion. Shared-enum/public-
type changes require the serial downstream check list in [AGENTS.md](../../AGENTS.md).

### D-drive Cargo storage boundary

The owner-approved active cache is exactly `D:\CargoTargets\Fork\wld-active`.
Before a cold build and at every phase/release boundary, record D: free space,
cache allocation and compiler processes. Do not begin cold below 80 GiB free;
stop adding work at 70 GiB cache allocation or below 40 GiB free. After verified
release outputs are copied into a newly numbered immutable stage and evidence is
sealed, remove the disposable cache at the next safe boundary. A different root
or longer retention needs explicit owner approval. The safe containment,
reparse-point and native-PowerShell deletion procedure is normative in
[AGENTS.md](../../AGENTS.md).

## See Also

- [Staged builds and compatibility](staged-builds-and-compat.md)
- [Live deployment](live-deployment.md)
