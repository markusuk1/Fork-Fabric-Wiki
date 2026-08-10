# Proactive Observation-Range Cognition

> Sources: LLM-002 runtime evidence and AUDIT-001 closure reconciliation, 2026-08-09 to 2026-08-10
> Raw: [LLM-002 implementation evidence](../../raw/architecture/2026-08-09-llm-002-proactive-observation-evidence.md), [LLM-001 prior-art research](../../raw/architecture/2026-08-09-llm-001-cognition-lease-research.md), [Programme Closure Audit](../../raw/architecture/2026-08-10-audit-001-programme-closure-reconciliation.md)
> Commit: c0cea44
> Updated: 2026-08-10

## Purpose

Key NPCs may notice a nearby player, temporarily request bounded cognition, and
choose to wait or begin an ordinary conversation. This is event-triggered
attention, not continuous model possession and not per-frame inference.

## Ownership and flow

| Stage | Owner | Contract |
|---|---|---|
| Entry detection | OpenMW actor-local Lua | Same cell, radius crossing, ray line of sight, signal, 0.5-second sampling and 100-unit hysteresis |
| Eligibility and salience | Fork reducers | Canonical profile allowlist/threshold plus current state, relationship, routine, cooldown and physical evidence |
| Temporary cognition | Native Fork Context and Fabric | Bounded context, 15-second lease, provider-neutral worker, at most `wait` or `initiate_conversation` |
| Proposal validation | Fork reducers | Recheck owner, claim, expiry, revisions, evidence subset, output bounds, control markers and replay digest |
| Final physical gate | OpenMW actor-local Lua | Recheck availability, cell, range and line of sight before any visible initiation |
| Conversation lifecycle | Existing DIALOGUE-001 path | Visible greeting, exact receipt, routine interrupt/resume and zero fabricated turns |

## Profile policy and trigger bounds

Every canonical profile must define `proactive_cognition`. Disabled profiles
remain explicit rather than inheriting accidental behavior. Enabled profiles
choose a radius from 128 to 600 OpenMW units, cooldown from 5 to 240 game
minutes, and salience threshold from 0 to 1000. Arelion's released policy is
350 units, 30 minutes and threshold 520.

Actor scripts keep a saved encounter counter and bounded handled-action set.
They emit only on entry into the configured radius; the player must leave past
the hysteresis boundary before another entry can occur. Fork additionally
coalesces by durable NPC/player cooldown, so reloads, log replay and companion
restarts cannot turn one approach into repeated model work.

## Safe step-in and step-out

An eligible encounter creates native Context/Fabric work with a 15-second TTL.
`wait` settles durably and produces no OpenMW action. `initiate_conversation`
creates one participant-, cell-, radius- and context-bound action. OpenMW may
apply it only after a fresh physical check, then sends an exact receipt.

A reported `applied` result with stale cell/range/line-of-sight evidence is
recorded as rejected. Wrong workers, malformed output, unsupported evidence,
changed revisions, provider exhaustion and expiry all converge to safe wait.
Every branch releases the lease; the ordinary deterministic game continues.

## Proven result and boundary

Direct adversarial, real OpenMW, active save/load, actual Fork restart, bridge
recovery, affected-system regression and inspected visual gates pass. The live
run averaged 0.788 CPU cores and ended with zero engine/Lua errors, active
leases or bridge dead letters. One initiation opened a normal zero-turn
conversation and interrupted/resumed its routine exactly once.

Fixture provenance is visible and durable. It proves the integration and
safety contract, not language quality. VOICE-001 now provides the separate
provider-neutral spatial playback boundary.

## See Also

- [Bounded Key-NPC Cognition Leases](bounded-key-npc-cognition-leases.md)
- [Persistent Contextual Conversation State](persistent-contextual-conversation-state.md)
- [Normalized Physical and Social Perception](normalized-perception-and-attention.md)
- [Daily NPC Routines and Terminal Repair](daily-routines-and-terminal-repair.md)
- [Fork–OpenMW Integration Architecture](integration-architecture.md)
