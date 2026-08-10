# Repository Continuity and Knowledge Workflow

> Sources: Repository continuity and knowledge system; Codex Stop continuation gate; WIKI-002 staleness/publication reconciliation, 2026-08-07 to 2026-08-10
> Raw: [Repository Continuity and Knowledge System](../../raw/workflow/2026-08-07-repository-continuity-system.md), [Codex Stop Continuation Gate](../../raw/workflow/2026-08-07-codex-stop-continuation-gate.md), [WIKI-002 Reconciliation](../../raw/workflow/2026-08-10-wiki-002-staleness-publication-reconciliation.md)
> Commit: c0cea44
> Updated: 2026-08-10

## Overview

GAME-OPENMW separates live task state, current session context, chronological
handoffs, completion evidence, and durable system knowledge. Each artifact has
one job, and validation checks their boundaries so agents can resume work without
depending on chat history or copying stale narratives between files.

## Information ownership

- [`TRACKING.md`](../../TRACKING.md) answers which stable task exists and whether
  it is Backlog, In Progress, Blocked, or Done.
- [`MEMORY.md`](../../MEMORY.md) answers what is true now, which task is active,
  what constraints matter, and where the next session should resume.
- [`NOTICEBOARD.yml`](../../NOTICEBOARD.yml) records append-only discoveries,
  warnings, blockers, corrections, completions, and required actions.
- [`CHANGELOG.md`](../../CHANGELOG.md) summarizes completed outcomes, while
  `docs/completions/` preserves exact commands, observed results, limitations,
  and next actions.
- `raw/` preserves immutable sources. `wiki/` turns those sources into maintained
  explanations of architecture, behavior, decisions, procedures, measurements,
  root causes, and gotchas.

This separation prevents an event log from masquerading as current truth and
prevents current memory from becoming an unauditable history.

## Session and completion lifecycle

At session start, an agent reads the rules, current memory, task board, wiki
index, pending noticeboard actions, recent messages, and working-tree state. It
queries relevant wiki articles before re-deriving non-trivial repository facts.

Before a task becomes Done, the agent records a completion report, synchronizes
tracking and memory, classifies wiki impact, updates durable knowledge when
required, and runs both wiki and workflow validation. Noticeboard entries are
reserved for context that helps another session rather than routine narration.

## Wiki maintenance boundary

A wiki ingest is required when completed work changes a user-visible capability,
architecture, configuration surface, durable decision, operational procedure,
measured result, root cause, or known gotcha. Pure formatting, mechanical
refactors, and bookkeeping with no reusable understanding are knowledge-neutral.

Every article links its raw source, records the repository commit it reflects
(`unknown` before the first commit), and carries an Updated date. Material
changes cascade to related articles and the index; operation history is appended
to `wiki/log.md`.

The authoritative knowledge remains the local `wiki/` tree plus every linked
immutable `raw/` source. `markusuk1/Fork-Fabric-Wiki` mirrors the maintained
wiki, not the internal raw evidence tree, and is not a competing source of
truth. When a completed task materially changes the wiki, its completion gate
includes synchronizing that tree and the governed mirror README, verifying the
resulting GitHub commit/ref and recording the observation. Raw evidence,
generated game content, credentials, private evidence media and Bethesda assets
never enter the mirror.

## Validation boundary

`scripts/validate-wiki.ps1` checks wiki structure, index coverage, metadata, raw
provenance, and local links. `scripts/validate-workflow.ps1` checks the broader
task, memory, noticeboard, evidence, skills, and wiki contract. These checks
prevent documentation drift but never replace application-specific builds,
tests, or runtime verification.

## Continuation integrity

An agent statement that work is now starting, continuing, running, active, or
in progress is an execution commitment for the current turn. The agent must
perform that work until the promised scope is complete, genuinely blocked, or
requires specific user input. Final responses distinguish completed scope from
future recommendations and do not present a future task as current activity.

Codex's native project `Stop` hook enforces this boundary. `.codex/hooks.json`
invokes `.codex/hooks/stop-no-false-continuation.ps1`, which examines only the
documented `last_assistant_message` field. An ongoing-work claim returns
`decision: block`, causing Codex to continue with the hook's reason as the next
prompt. Genuine blocker/approval language and completed-result messages pass.
The hook intentionally continues to block repeated false continuation claims
even when `stop_hook_active` is already true.

Project hooks require Codex trust review when first added or whenever their
definition changes. Direct tests cover ongoing work, repeated stopping,
completed work, and approval blockers. The policy stays local, deterministic,
offline, and independent of the unstable transcript format.
