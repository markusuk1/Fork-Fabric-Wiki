# Agent Workflow & Workspace Standards

> Sources: [AGENTS.md](../../AGENTS.md); `NOTICEBOARD.yml` conventions; `ingest-manual-kit/`; [machine-readable release state](../../docs/release-state.json)
> Commit: 8d4f4531e90e1a2292c0e163f32509d8fdf7c8a0
> Updated: 2026-08-09

## Overview

This repo is multi-agent. The durable rules live in [AGENTS.md](../../AGENTS.md); the
cross-agent message board is [NOTICEBOARD.yml](../../NOTICEBOARD.yml)
(append-only, READ AT SESSION START); the live board is
[TRACKING.md](../../TRACKING.md); history is [CHANGELOG.md](../../CHANGELOG.md)
— both updated on every completion, not just docs/ artifacts.

The canonical [noticeboard reader](../../scripts/read-noticeboard.ps1) performs
one bounded line-oriented pass before filtering. It rejects boards above 16 MiB,
lines above 32 KiB, more than 10,000 messages, or more than 4,096 body lines per
message. Historical pre-protocol records remain visible as `legacy` in full
history but never enter modern type filters. The generated
[reader regression](../../.opencode/scripts/tests/read-noticeboard.tests.ps1)
keeps current-board scale and fail-closed ceilings executable. Temporary test
artifacts use the active process temp directory's `opencode` child; cleanup
must resolve and prove containment within that root before recursive removal.
Hard-coded paths into another user's profile are not portable or writable.

## CRAFTE (every non-trivial decision)

**C**orrect (solves the real problem) · **R**obust (survives likely edge
cases) · **A**ligned (user intent + repo conventions + task scope) ·
**F**aithful-to-method (documented workflow + durable decisions) ·
**T**ested (fresh verification, never assumption) · **E**fficient (smallest
sufficient implementation — same result, less machinery; NEVER scope
reduction).

## Completion rules

A task is complete when acceptance criteria pass and evidence is recorded —
time spent, files touched, and "progress" prove nothing. Fix real defects;
"document + defer" needs evidence the cure is worse plus a re-open trigger.
No placeholders/TODO stubs. Live verification beats unit green: the live
runs are what caught the ABI advertisement lag, the /http-vs-/route URL
bug, the query-param interop, the 8s timeout, and the wire drop.

**Knowledge artifacts are part of "done" ([AGENTS.md](../../AGENTS.md)).** A completion that adds
or changes a capability, architecture, config surface, or durable decision is
not fully done until this wiki AND `docs/fork-ingest-manual.html` reflect it:
update the article(s) + [index.md](../index.md) row + [log.md](../log.md) line and run the wiki lint
(clean), and re-run `validate_ingest_manual.py` (`conformant`). The tracking
layer alone (CHANGELOG/TRACKING/NOTICEBOARD) is necessary but not sufficient —
they lagged once and had to be back-filled.

### Current runtime stamp hygiene

Before changing or publishing documentation that says a node is "current",
"live", or "remains on" a build, query that node's `/v1/fork-version` and
`/v1/status` in the same documentation pass. Reconcile the result across the
manual, [Live Deployment](../operations/live-deployment.md),
[Fork Versioning & Drift Detection](../operations/fork-versioning.md),
[Staged Builds & Compatibility](../operations/staged-builds-and-compat.md),
[Host Configuration Management](../operations/host-configuration-management.md),
and the index summaries that describe live state. The executable endpoint stamp
wins over prose. Refresh [docs/release-state.json](../../docs/release-state.json)
to separate repository source, newest accepted immutable stage, observed live
runtime, and disabled legacy state. Run
`.opencode/scripts/audit-release-semantics.ps1`; it verifies source/manifest,
the staged executable stamp/hash, both live endpoints, local status, serving
process path, CUDA compile defaults, and the canonical current-state documents.
Wiki link/index lint and manual schema conformance are necessary but cannot
certify mutable semantic facts by themselves. Historical
[log.md](../log.md) entries remain append-only; add a new correction/ingest
entry instead of rewriting history. Observing a rejected artifact live never
makes it accepted.

## Cross-repo etiquette

Downstream repos file Markdown reports under `docs/`, named for the issue; the fork answers
with a RESPONSE doc + noticeboard entry, and closed exchanges get
INTEGRATED into this wiki. External repos' own integration work never
lands on this repo's board. Don't attribute repo work to specific agents
in reports — report the work and its state.

## The knowledge artifacts (which to use when)

### Green source is not a released capability

Before closing implementation, classify changes as host, guest, both, or
neither. Host changes require a new monotonic fork build and rollback-safe staged
folder. Guest changes require rebuilding and publishing all affected WASM
modules. Mixed changes require both from one compatible source pin. Completion
evidence includes executable version/commit stamps, publish smoke, restart and
rollback results, and the staged-build registry update.

- `docs/fork-ingest-manual.html` — the validated repo card the hub
  cold-indexes (`ingest-manual-kit/` is the drop-in standard for other
  repos: schema + template + validator + authoring guide + skill).
- `wiki/` (this knowledge base) — living, navigable, integrated knowledge;
  query it BEFORE re-deriving facts; ingest into it when exchanges close.
- Memory notes / NOTICEBOARD — session-to-session and agent-to-agent state.

## See Also

- [Decisions and rejected paths](decisions-and-rejected-paths.md)
- [Build and test gotchas](../operations/build-and-test-gotchas.md)
