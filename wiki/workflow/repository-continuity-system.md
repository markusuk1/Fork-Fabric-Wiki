# Repository Continuity and Knowledge Workflow

> Sources: Repository continuity and knowledge system; Codex Stop continuation gate; WIKI-002 staleness/publication reconciliation; COMPANION-001 native handoff and feature-lifecycle corrections, 2026-08-07 to 2026-08-11
> Raw: [Repository Continuity and Knowledge System](../../raw/workflow/2026-08-07-repository-continuity-system.md), [Codex Stop Continuation Gate](../../raw/workflow/2026-08-07-codex-stop-continuation-gate.md), [WIKI-002 Reconciliation](../../raw/workflow/2026-08-10-wiki-002-staleness-publication-reconciliation.md), [Companion native handoff correction](../../raw/skyrim/2026-08-11-companion-native-handoff-correction.md), [Exact-hash qualification](../../raw/skyrim/2026-08-11-companion-exact-hash-qualification.md), [Feature delivery lifecycle](../../raw/workflow/2026-08-11-feature-delivery-lifecycle.md), [Analysis and design ownership gate](../../raw/workflow/2026-08-11-feature-delivery-design-gate.md), [Critical dependency closure gate](../../raw/workflow/2026-08-11-critical-dependency-closure-gate.md), [Noticeboard action supersession](../../raw/workflow/2026-08-11-noticeboard-action-supersession.md)
> Commit: c0cea44
> Updated: 2026-08-11

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

Because the noticeboard is append-only, a correction that withdraws an earlier
required action names the earlier IDs in `supersedes`. Chronological reads keep
the full history, while `-RequiresAction` excludes superseded messages. This
prevents a stale handoff from reappearing as live work in a later session.

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

## Feature delivery lifecycle

Every player-visible feature has a machine-readable record under
`docs/features/`. Its state distinguishes discovery, readiness to implement,
implemented-but-unproven code, developer production proof, regression
qualification, readiness for owner testing, owner acceptance, and failure.
Source code, compilation and native load are therefore never mistaken for a
working feature.

The mandatory order is grounded requirements, prior-art research, analysis of
the existing system, complete dependency closure, design, implementation,
developer production-path proof, regression testing, and finally owner
playtesting. Later-stage work never substitutes for an earlier gate.

The no-code gate requires grounded falsifiable requirements, analysis of current
behavior, gaps and constraints, prior-art research, and a `Reuse`, `Configure`,
`Adapt`, `Combine`, or `Build` decision. It also requires explicit closure of
platform/API, lifecycle/startup, identity,
UI/input/dialogue, movement/animation, assets, persistence, failure/recovery,
observability, deployment, automated-test and owner-test dependencies. A
critical unresolved or merely assigned item permits only discovery or failed
state; implementation readiness requires evidence-backed known or rejected
status. Every
requirement and dependency category must then map to a named design component
with a concrete responsibility and explicit failure handling; research alone
does not authorize coding.

Production proof must use the real player trigger and shipped runtime entry
point and observe the terminal result. Direct executor calls, selectors,
preflight, barks and isolated receipts retain narrow evidentiary value but
cannot qualify the combined feature. Regression coverage follows developer
proof. The owner receives a test only at `ready_for_owner` and supplies final
experiential acceptance; any failure returns the record to `failed` with exact
evidence and re-entry requirements.

`scripts/validate-feature-lifecycle.ps1` enforces these transitions.
`tests/test-feature-lifecycle-workflow.ps1` guards against unresolved readiness,
preflight substitution and owner acceptance without owner proof, and the
integrated workflow validator runs both.

## Native Skyrim handoff gate

Compilation, static inspection, Papyrus assembly, plugin round-trip parsing and
deployment hashes do not prove that Skyrim can load a native DLL/ESP pair. Any
change to a native companion DLL, loaded ESP, SKSE lifecycle/event registration
or deployment configuration must pass
`scripts/qualify-skyrim-companion-build.ps1 -RunId <id>
-NonIntrusiveCapture` before owner testing.

The qualifier builds and deploys the exact pair, starts SKSE on an isolated
non-input Windows desktop, loads a real save, and fails unless fresh lifecycle
receipts reach DataLoaded and world loaded. It also requires zero SKSE error
markers, exact built/direct/MO2 DLL and ESP hash equality, and zero remaining
owned processes. The 300-second default accommodates the observed third-attempt
save load without weakening the terminal conditions. The owner is never the
first process-load test.

## Unattended GUI isolation

Unattended GUI programs are contained by desktop separation, not presentation
flags. A hidden or minimized launch request can still surface a first-run,
update, error or modal window on the owner's desktop. Modding utilities,
content-authoring tools and game runtimes therefore run on an isolated non-input
Windows desktop or through an equally proven project harness. If neither is
available, the run is explicitly interactive and requires owner approval.

The xEdit 4.1.5f SEQ-generation preflight demonstrated the failure boundary:
`-WindowStyle Hidden` still exposed the tool's first-run **What's New?** window.
The exact process was terminated and the empty output was rejected. See the
[unattended GUI isolation correction](../../raw/workflow/2026-08-11-unattended-gui-isolation-correction.md).

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
