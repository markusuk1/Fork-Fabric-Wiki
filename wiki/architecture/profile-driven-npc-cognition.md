# Profile-Driven NPC Cognition and Social Knowledge

> Sources: NPC-001 research/evidence, PROFILE-001 unified-model evidence and LLM-002 proactive-cognition evidence, 2026-08-09
> Raw: NPC-001 Research, NPC-001 Evidence, PROFILE-001 Evidence, LLM-002 Evidence
> Commit: 6e7f7a9
> Updated: 2026-08-09

## Overview

`NPC-001` is the first deterministic cognition slice. Several witnesses can
observe the same staged event, form separate beliefs, select different actions
from profile and situation factors, execute bounded OpenMW AI packages, return
physical receipts, and transmit knowledge without making uninformed actors
omniscient. It establishes the typed substrate required before an LLM can take
a temporary cognition lease.

## Ownership and model

OpenMW owns active actors, positions, nearby visibility, collision rays,
navigation and completion of Travel packages. Fork owns stable profiles,
observations, beliefs, decision scores, intentions, receipts and social-source
provenance. The companion validates and translates; it owns neither truth.

The canonical PROFILE-001 schema separates ten traits, six competencies, five
values, stable identity/household context and independent dynamic needs/affect.
The witness policy currently consumes these six fields:

| Field | Current decision effect |
|---|---|
| Courage | Reduces the practical dominance of threat and supports intervention |
| Curiosity | Raises investigation and information-gathering value |
| Reasoning | Supports consequence-aware reporting; low values raise reckless investigation |
| Strength | Raises direct-intervention feasibility |
| Sociability | Raises help/report communication value |
| Lawfulness | Raises formal guard-report value |

Observation confidence and perceived threat join those fields in the current
candidate formulas. Role and spouse identity are stored profile context, but
role does not yet alter the three witness scores and spouse identity is used
only to address the later private disclosure. The names `brave`, `cautious` or
`inquisitive` are authoring summaries; the reducer uses bounded numeric values
and stores the resulting candidate scores. Future emotions, needs,
relationships, role duties and values should join as separate dynamic layers
rather than being folded into permanent traits.

LLM-002 adds a separate explicit profile policy for proactive attention:
enabled/disabled, observation radius, game-time cooldown and salience threshold.
The reducer combines that stable policy with current state, relationship,
routine ownership and fresh physical evidence. A profile therefore permits
attention but does not directly force conversation; the bounded lease may wait,
and OpenMW must still revalidate the physical encounter.

`NPC-002` adds a separate presentation-appraisal policy in which observer role
does affect reaction selection: Guard Sera's duty can produce a weapon order or
authority verification while farmer profiles select resistance, observation or
distance from the same perceived threat. This does not retroactively change the
NPC-001 incident candidate formulas.

## Canonical proof profiles

| NPC | Relevant profile | Selected action |
|---|---|---|
| Belyn Falas | Strong, brave, inquisitive farmer | `confront_and_call_help` |
| Oryn Saren | Weak, brave, highly curious, poor risk judgment | `investigate_recklessly` |
| Nara Velas | Weak, highly reasoning, cautious, social and law-minded | `hide_then_report` |

These outcomes are deterministic for the current scoring policy and staged
threat. They are not hard-coded per NPC: all three use the same candidate
functions and incident reducer.

## Runtime flow

```text
activate Ralen once
  -> clearly labelled witness-lab incident
  -> three local OpenMW distance/LOS observations
  -> schema-2 tagged events
  -> Fork direct beliefs + three scored intentions
  -> atomic pending-intention projection
  -> allow-listed OpenMW Travel packages
  -> destination-reached receipts
  -> Fork closes all intentions
  -> Nara emits delayed guard report
  -> guard gains a sourced belief
  -> Nara emits later private disclosure
  -> spouse gains a separately sourced belief
```

The companion projection is a snapshot of pending intentions. Local scripts
track handled intention IDs, reject unknown actions or invalid coordinates and
time out stalled Travel packages. Mutable snapshots are reopened with
`openmw.vfs.open` and decoded from bounded text; this is the observed reliable
path for atomic replacements during an active OpenMW session.

## Epistemic rules

- World truth and NPC belief are different records.
- Each direct witness owns a separate belief with confidence and source event.
- Guard Sera and Lysa Velas begin with no incident belief.
- Nara's social transmissions cannot run until her hide intention has an
  applied physical receipt.
- A report/disclosure creates one listener belief with the channel,
  transmission identity and trust-attenuated confidence.
- Duplicate observation, receipt and delivery IDs are idempotent.

This is the minimum useful rumour substrate: information moves through explicit
edges and may later be distorted, contradicted or rejected without rewriting
the original observation.

## Operation and proof

The desktop `OpenMW - Empty Seyda Neen` launch uses the current playable local
Fork database `game-openmw-npc-v9`. Ralen and the three physical witnesses appear
near the explorer. Activating Ralen for the first time in a session starts the
labelled witness drill; normal baseline routines remain available when the
companion is offline.

The live OpenMW proof observed four physical actors, three witness observations,
three distinct actions, three destination-reached receipts and two ordered
social transmissions, with zero missing-Hello, missing-Idle, Lua, engine or
fatal errors. The live Fork proof also verified early-report rejection,
guard/spouse knowledge boundaries, duplicate idempotency and recovery through a
fresh client process.

## Boundaries and next work

- The trigger is staged; general combat/death/crime recognition remains work.
- Lysa remains a durable profile without embodiment. NPC-002 now embodies Guard
  Sera as a presentation observer; she is still only a social recipient in the
  NPC-001 staged incident.
- The first policy still has fixed candidate functions. Complete profiles and
  revisioned needs/affect now exist; time-driven routines and generalized goal
  arbitration remain ROUTINE-001 and DECIDE-001.
- Bounded conversation, terminal-repair and proactive-attention LLM leases now
  consume allow-listed context, propose typed actions and never directly resolve
  physical truth. A deterministic fixture proves the contract; language quality
  with a configured model remains unproven.
- A version lease/upgrade handshake should replace the historical companion
  mutex as the sole process-version guard.

## See Also

- [Forkâ€“OpenMW Integration Architecture](integration-architecture.md)
- [Fork and OpenMW Capability Matrix](capability-matrix.md)
- [Remembering Villager Vertical Slice](remembering-villager-vertical-slice.md)
- [Contextual Player Presentation and NPC Reactions](contextual-presentation-reactions.md)
- [Replacement World-Shell Pipeline](../openmw/world-shell-pipeline.md)
- [Unified NPC Profile and State](unified-npc-profile-and-state.md)
- [Proactive Observation-Range Cognition](proactive-observation-range-cognition.md)
