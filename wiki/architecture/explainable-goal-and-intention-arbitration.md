# Explainable Goal and Intention Arbitration

> Sources: DECIDE-001 prior-art review and implementation/runtime evidence, plus PROFILE-001, ROUTINE-001, PERCEPT-001 and MEMORY-001 contracts, 2026-08-09
> Raw: [Prior-art decision](../../raw/architecture/2026-08-09-decide-001-goal-arbitration-research.md), [Implementation evidence](../../raw/architecture/2026-08-09-decide-001-explainable-goal-arbitration-evidence.md)
> Commit: 6e7f7a9
> Updated: 2026-08-09

## Contract

A decision is a durable, inspectable choice over current canonical evidence,
not a scenario script and not an LLM instruction. Fork owns candidate
generation, scoring, commitment, revision checks, dispatch audit and receipt
closure. OpenMW owns positions, pathfinding, AI packages and the physical
outcome.

Detected attack, death and crime observations enter decision derivation in the
same Fork transaction as perception and memory intake. The transaction creates
a recall receipt bounded to four items, 512 characters and minimum confidence
300, then evaluates exactly five goal types:

| Goal | Representative action | Principal factors |
|---|---|---|
| `preserve_safety` | `hide_then_report` | threat, fear, safety need, low courage/combat, reasoning |
| `protect_community` | `confront_and_call_help` | courage, combat, empathy, community/duty, risk |
| `seek_information` | `investigate_recklessly` | curiosity, reasoning, perception, uncertainty, risk |
| `uphold_law` | `hide_then_report` | lawfulness, duty, community, reasoning, memory |
| `maintain_routine` | no physical dispatch | low threat, diligence, patience, need for purpose, routine stability |

Each candidate stores urgency, profile, state, evidence, memory and routine
components, risk penalty, total, eligibility, rank, status and explanation.
Sorting is deterministic; the goal kind breaks an exact score tie.

## Commitment and switching

One active commitment per NPC prevents action thrashing. The current goal is
retained while it is still proposed or dispatched unless a competing candidate
clears the 120-point switch margin. `preserve_safety` with urgency at least 900
is an emergency and may override the margin. Same-goal evidence refreshes the
single proposed intention to the newest observation, memory query, revisions
and targets instead of creating a queue of duplicates.

Switching preserves the prior intention as `superseded`. `maintain_routine` is
closed as `satisfied` without a physical command. Exact arbitration replay is a
no-op; reuse with different inputs is rejected.

## Dispatch safety

An intention snapshots profile, dynamic-state, routine and commitment revisions
plus the detected observation and bounded memory query. The companion cannot
publish a proposal directly. It first invokes the dispatch reducer, which
checks:

- all source revisions still match;
- the observation still belongs to the NPC and is the current detected
  perception for that subject and stimulus kind;
- the active commitment still owns the intention; and
- the action is one of `confront_and_call_help`, `investigate_recklessly` or
  `hide_then_report`.

A failed check records `stale` and never reaches OpenMW. A successful claim is
projected through the bounded `intentions.json` snapshot. OpenMW executes a
Travel package, reports applied/rejected/failed, and Fork closes the intention
and commitment exactly once from that receipt.

## Local action serialization

Presentation reactions and goal movements share OpenMW's one actor AI-package
surface, so the actor script serializes them. A goal cannot overwrite an active
presentation reaction. The routine adapter also treats its locally advanced
revision as a causal floor: a lagging companion snapshot cannot revert the
interrupted flag or revision. Every applied goal action therefore has one
`active -> interrupted -> active` routine pair.

Generalized `hide_then_report` deliberately does not fabricate a social edge or
listener. Embodied listeners, privacy/range, trust, distortion and multi-hop
delivery belong to SOCIAL-001.

## Proven behavior and limits

The direct matrix proves different profile/state combinations choose community
protection, investigation and law/reporting for comparable threat evidence;
commitment retention, emergency switching, stale dispatch, exact receipt
closure and negative bounds all pass. The production v24 OpenMW run produced
20 automatic arbitrations, 100 candidate audits, three selected goal kinds and
11 physical receipts with zero engine/Lua/dead-letter errors. Decisions survive
a standalone Fork restart, while existing save/load and companion-reconnect
contracts remain green.

This is deterministic high-level arbitration, not combat tactics, a general
planner, social propagation or an LLM lease. Standard OpenMW AI continues to
handle frame-time mechanics; later systems may propose new typed candidates but
must pass the same revision, allow-list and receipt boundary.

## See Also

- [Unified NPC Profile and State](unified-npc-profile-and-state.md)
- [Daily NPC Routines and Terminal Repair](daily-routines-and-terminal-repair.md)
- [Normalized Physical and Social Perception](normalized-perception-and-attention.md)
- [Epistemic Memory and Bounded Recall](epistemic-memory-and-bounded-recall.md)
- [Fork–OpenMW Integration Architecture](integration-architecture.md)
