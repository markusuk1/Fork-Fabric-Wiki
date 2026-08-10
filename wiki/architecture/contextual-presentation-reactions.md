# Derived Player Identity, Reputation and NPC Reactions

> Sources: REPUTATION-001 research/evidence, NPC-002 evidence, NPC-003 inventory correction, 2026-08-09
> Raw: [REPUTATION-001 Research](../../raw/architecture/2026-08-09-reputation-001-derived-identity-research.md), [REPUTATION-001 Evidence](../../raw/architecture/2026-08-09-reputation-001-derived-identity-evidence.md), [NPC-002 Evidence](../../raw/architecture/2026-08-08-npc-002-contextual-appearance-evidence.md), [NPC-003 Inventory Correction](../../raw/openmw/2026-08-08-npc-003-active-preset-inventory-decision.md)
> Commit: c0cea44
> Updated: 2026-08-09

## Released boundary

`REPUTATION-001` replaces the NPC-002 semantic test presets with evidence-derived
state. OpenMW reports only facts it can physically or mechanically establish;
Fork derives condition, identity and observer-specific reputation, persists the
reasoning, selects an allow-listed response and waits for OpenMW's receipt.

The production schema-10 presentation event rejects caller-authored
`cleanliness`, `bloodiness`, `claimed_role`, `fame`, `notoriety` and
`recognized_authority`. A client therefore cannot turn a costume into authority
or manufacture reputation by asserting a score.

## Ownership and flow

```text
OpenMW physical/native evidence
  stance, weapon, equipment, wear, clothing, native reputation/bounty,
  faction membership, actual credential inventory, movement/swim/combat
        |
        v
Fork evidence and objective deeds
  exact event replay, monotonic source clocks, causal dirt/blood/wash state,
  one objective deed root, per-observer exposure
        |
        v
Observer appraisal
  capability + role + traits + known deeds + credentials + physical disguise
        |
        v
Bounded reaction -> OpenMW actuation -> applied/rejected/failed receipt
```

OpenMW owns frame-time physical truth and native mechanical state. Fork owns the
durable interpretation: `PlayerPresentationEvidence`, revisioned
`PlayerPresentationState`, `PlayerDeed`, `NpcPlayerRecognition`,
`NpcReputationEvidence` and `DerivedIdentityAppraisal`.

## Condition is causal, not cosmetic metadata

The player script maintains a bounded local causal sampler:

- actual movement emits `travel_grime` after a bounded distance;
- an actual combat hit emits `combat_blood`;
- entering the native swimming state emits `wash`;
- native synchronization carries current reputation, bounty, faction
  membership and actual credential inventory;
- equipment condition and visible clothing are measured from the actor's real
  equipped objects for every observation.

Fork canonicalizes every evidence payload, gives it an exact event identity and
rejects a divergent replay or a stale source clock. The resulting cleanliness
and blood state survives OpenMW save/load and Fork restart. It represents
semantic causal condition; REPUTATION-001 does not claim renderer-level mud,
blood decals or texture wear.

## Identity and disguise

A uniform is evidence of appearance, not proof of role. The observer receives
the count/completeness of actual Imperial equipment, while Fork independently
checks credentials, native faction membership, known deeds and observer
capability.

| Situation | Inquisitive civilian Oryn | Guard Sera |
|---|---|---|
| Complete Imperial uniform, no authority | `plausible_disguise` | `exposed_disguise` |
| Uniform plus actual Guard Writ or supported faction | recognized/credentialed authority | authentic authority |
| Armed supported authority | observes or yields according to profile | verifies rather than issuing the generic criminal warning |

The test harness changes actual equipment and creates a real dynamic `Seyda
Neen Guard Writ` Book only for the credentialed case. No semantic identity flag
crosses the bridge.

## Reputation is local knowledge

`PlayerDeed` stores one objective event with stable root identity and bounded
scope/value. `NpcPlayerRecognition` and `NpcReputationEvidence` record what a
particular observer knows and how they learned it. Direct witnesses learn from
native normalized crime evidence. Listeners learn only after an embodied social
delivery is accepted as `told` or physically overheard as `overheard`.

Retelling never creates a second objective deed and a repeated deed/observer
exposure cannot inflate the score. Consequently two NPCs can rationally hold
different fame/notoriety estimates for the same player. This is an epistemic
reputation system, not a globally broadcast alignment meter.

Applied gifts also create a positive deed rooted in the completed physical
receipt and recognition for the recipient. Native crime produces a negative
deed and direct-witness exposure. These paths give conversation, social flow
and later economic policy a common causal reputation substrate.

## Reaction lifecycle and safety

Every appraisal records the presentation-state revision and its explanation.
The reaction vocabulary remains bounded; OpenMW applies only allow-listed local
actions and returns an applied, rejected or failed receipt. Exact retries return
the prior result, changed payloads fail, malformed or stale observations invent
no state, and pending effects remain recoverable across companion or Fork
restart.

The legacy schema-3 route remains solely for regression coverage. Production
OpenMW emits protocol-6/schema-10 physical and native events.

## Playable proof

REPUTATION-001 was qualified on `game-openmw-npc-v64` with isolated data
generation `village-lab-data-reputation12`. Current consolidated production is
`game-openmw-npc-v203`. Tavia Quill's explicit regression fixture cycles four
real loadouts:
`civilian`, `false_guard`, `credentialed_guard` and `damaged_chitin`.

The final live matrix created 20 physical observations, 20 derived appraisals,
20 persisted reactions and 20 applied receipts across four observers and five
steps. Oryn accepted the unsupported complete uniform as
`plausible_disguise`; Guard Sera returned `exposed_disguise`; all eight
credentialed appraisals recognized supported authority. The run retained ten
project actors and ended with zero missing-Hello, missing-Idle, engine, fatal or
Lua errors.

Direct tests additionally prove causal condition revision, one-deed/many-
observer recognition, replay/divergence/stale rejection, embodied report
propagation, restart persistence and post-commit reconnect recovery.

The inspected visual proof shows Guard Sera's in-world derived-identity panel:
`EXPOSE FALSE GUARD`, the missing faction/credential support, threat 25,
authority 0 and suspicion 70. Because a protected Windows Security dialog owned
foreground focus, the guarded harness captured only the verified OpenMW window
without dismissing or changing the security dialog.

## Limits and next consumers

- This task derives social meaning; it does not add shader/material dirt or
  damage decals.
- Recognition currently uses bounded deterministic rules. A future LLM may
  explain or converse about the result, but may not author identity truth or
  bypass the reducer and receipt boundary.
- Regional institutions, warrants, economic credit and faction-specific law can
  extend the same deed/exposure model rather than introducing global reputation
  writes.
- Replaying a brand-new baseline profile against an old persistent database can
  correctly reject its lower source clocks as stale. Automated baseline runs
  therefore use a fresh versioned database; normal same-lineage saves remain
  monotonic.

## See also

- [Normalized Physical and Social Perception](normalized-perception-and-attention.md)
- [Embodied Social Information Flow](embodied-social-information-flow.md)
- [General Player Journal and Knowledge Interactions](journal-driven-knowledge-interactions.md)
- [Fork and OpenMW Capability Matrix](capability-matrix.md)
- [Fork-OpenMW Integration Architecture](integration-architecture.md)
