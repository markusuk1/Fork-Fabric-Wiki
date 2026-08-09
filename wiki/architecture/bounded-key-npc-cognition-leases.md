# Bounded Key-NPC Cognition Leases

> Sources: LLM-001 and LLM-002 research, implementation and observed runtime evidence, 2026-08-09
> Raw: Prior-art research, LLM-001 evidence, LLM-002 evidence
> Commit: unknown
> Updated: 2026-08-09

## Authority boundary

The model temporarily proposes; it never possesses an NPC or writes the world.

| Concern | Owner |
|---|---|
| Eligibility, context revisions, proposal validation, transcript, routine state and durable evidence | Fork reducers |
| Context item/byte/token limits | Native Fork Context Engine |
| Work claims, attempts, expiry, cancellation, settlement and dead letters | Native Fork Fabric |
| Structured inference | External provider-neutral worker |
| UI, subtitles, routine packages, native Barter, saves and physical receipts | OpenMW |
| Protocol-6/schema-13 transport and bounded projections | Companion |

## Lease lifecycle

An active participant-bound conversation can request one free-text lease for
its selected NPC. A routine can request one terminal-repair lease only after
two deterministic repairs have failed. One NPC may have at most one pending or
running cognition lease.

Fork assembles a native Context frame with at most eight memory references,
4096 bytes and 1024 estimated tokens. The immutable work packet includes the
player/NPC/session binding, current context fingerprint, exact evidence
allowlist, action allowlist and expected turn or routine revision. Native Fabric
then owns the pending/running/retry/settled/dead-letter state.

The worker claims a payload for five seconds and submits strict structured
output. Fork rechecks the claim token, current context, expiry, evidence subset,
tone, action, argument bounds, control markers and replay digest. Invalid output
is retained as a rejected proposal and requeued within the two-attempt bound.

## Commit and step-out

Accepted conversation speech creates one ordinary ordered DIALOGUE-001 turn.
It remains `awaiting_openmw` until the visible response or typed native action
returns an exact receipt. Closing the conversation resumes only its owned
routine interruption.

Terminal proposals map only to the current authored station or safe local
Wander. Both become ordinary revisioned routine commands and remain pending
until OpenMW confirms physical application. The model has stepped out before
the actuator runs; the routine scheduler owns subsequent behavior.

Worker refusal/outage, malformed output, a crash or a missing provider never
blocks ordinary play indefinitely. Stale claims are reclaimed, final provider
failure or TTL expiry commits one deterministic conversation fallback, and
cancellation releases ownership. A constant-size projection revision tells the
companion when external worker state changed without scanning lease history.

## Configuration

The launcher and standalone worker load only `GAME_LLM_PROVIDER`,
`GAME_LLM_BASE_URL`, `GAME_LLM_MODEL` and `GAME_LLM_API_KEY` from repository
`.env`. The released default is `disabled`; deterministic dialogue remains
usable. `openai-compatible` appends `/v1/chat/completions` to the configured
base URL and requests a strict JSON schema. `fixture` exists only for contract
tests and is always labelled in persisted and visible provenance.

## Proven result and boundary

Direct adversarial, real OpenMW, active save/load, actual Fork restart, bridge
recovery, performance and inspected visual gates pass. The live proof averaged
0.68 CPU cores and ended with zero engine/Lua errors, active leases or bridge
dead letters. Native Context and Fabric records persisted across restart.

No model endpoint is configured locally, so natural-language quality has not
been claimed. LLM-002 now supplies the separate salience-triggered,
cooldown-bound observation-range lease and reuses this validation and step-out
contract; see [Proactive Observation-Range Cognition](proactive-observation-range-cognition.md).
Voice playback remains a separate task.

## See Also

- [Persistent Contextual Conversation State](persistent-contextual-conversation-state.md)
- [Daily NPC Routines and Terminal Repair](daily-routines-and-terminal-repair.md)
- [Epistemic Memory and Bounded Recall](epistemic-memory-and-bounded-recall.md)
- [Forkâ€“OpenMW Integration Architecture](integration-architecture.md)
- [Fork and OpenMW Capability Matrix](capability-matrix.md)
