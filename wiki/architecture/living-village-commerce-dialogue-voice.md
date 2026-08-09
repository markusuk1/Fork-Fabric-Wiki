# Living Village, Commerce, Conversation and Voice

> Sources: DISC-002 research, VILLAGE/INTERACT evidence, DIALOGUE-001 contextual-session evidence and LLM-002 proactive-cognition evidence, 2026-08-09
> Raw: DISC-002 Research, VILLAGE-001 Evidence, INTERACT-003 Evidence, DIALOGUE-001 Evidence, LLM-002 Evidence
> Commit: 6e7f7a9
> Updated: 2026-08-09

## Overview

The clean Seyda Neen shell contains the world structure needed for a credible
first living village. Its cells, buildings, interiors, doors, pathgrids,
furniture and objects survived; the legacy actors and story did not.
VILLAGE-001 replaced the former player-relative riverbank lineup with ten
project-owned villagers at authored exterior and interior work points.

The selected design combines spatially authored OpenMW actors and native
commerce with Fork-owned schedules, memory, beliefs, relationships and
conversation state. External LLM and voice workers receive temporary bounded
work; they do not become another authoritative NPC platform.

## Spatial population

Use stable location anchors instead of player-relative placement. An anchor
contains a named cell, exact position and rotation, purpose tags, capacity and
verified path reachability. Each NPC references home, work, social, meal, sleep
and emergency anchors. OpenMW executes movement and local Wander behavior; Fork
stores the intended routine, reservations, deviations and reasons.

The delivered first population distributes ten villagers across exteriors and
interiors:

| Role | Primary location | World function |
|---|---|---|
| Merchant | Tradehouse counter | Native barter, local information and supply history |
| Assistant/porter | Tradehouse floor/storage route | Stock movement, deliveries and overhearing |
| Guard | Gate, bridge and patrol anchors | Authority, reports and public safety |
| Fisher/dock worker | Shore/dock anchors | Food supply and local observation |
| Net maker | Outdoor work/home anchors | Craft role and household/social ties |
| Herbalist/healer | Home/work and gathering anchors | Services, injuries and ecology knowledge |
| Census clerk | Census office | Administration, identity and newcomer context |
| Boatwright/carpenter | Outdoor work anchors | Repair economy and material demand |

## VILLAGE-001 as-built slice

`openmw/village-lab/village_lab/population.json` is the versioned source for ten
stable actors and ten exact anchors. Six villagers work in the exterior, three
occupy Arrille's Tradehouse, and one occupies the Census and Excise Office.
They cover ten roles, four races, both sexes, distinct heads/hair/outfits and
bounded native Wander packages. Production code contains no player-relative
placement; that geometry exists only in explicit NPC regression staging.

Arelion Faren is the tradehouse merchant. OpenMW creates his NPC record with
native Barter and the selected service flags, gives the real actor 15 validated
static stock types (29 units) and 800 barter gold, and opens native `Barter` mode when
the player activates him. Dava Relas and Lirae Fane provide a porter and cook
elsewhere in the tradehouse rather than forming another lineup at the counter.
Tracked kwama eggs are deliberately absent from this static seed and arrive only
through the ECONOMY-001 finite production/delivery path.

The 18-second commerce proof observed all ten actors in the exact cell split,
the complete merchant stock/gold contract, one real merchant activation, native
`Barter` mode and zero engine, Lua, Hello or Idle errors. A 30-second village
run measured 2.291 average CPU cores, below the documented three-core
uncapped-rendering regression ceiling. NPC-001 and NPC-002 also pass with the
ten-actor population.

Durable runtime rules discovered by this slice:

- actor-local scripts persist their full initialization configuration through
  save/load rather than assuming initial data will always be supplied again;
- a second teleport cannot occur while actor creation/placement is still being
  processed, so isolated lab staging waits before and after moving actors;
- Travel aimed at another actor completes at interaction range because actor
  collision prevents reaching the exact occupied target point;
- runtime proofs use isolated VFS roots, fresh logs and unique companion
  mutexes so normal or older companions cannot contaminate evidence.

Vary race, sex, head, hair, clothes, equipment condition, cleanliness, age
impression and role. Keep these separate from personality, beliefs and dynamic
emotional state. A role informs duty and access; it does not fully determine a
person.

## Native shop vertical slice

`Seyda Neen, Arrille's Tradehouse` survives as a furnished, door-connected but
unoccupied interior. Reuse it for a newly authored merchant.

OpenMW 0.51 can create the NPC record with merchant service flags, stock their
inventory, set barter gold and open the native `Barter` UI from player Lua.
OpenMW should remain authoritative for immediate prices, gold and item transfer.
After the trade, a semantic observation records the transaction in Fork.

Fork owns the durable business layer:

- stock provenance, deliveries, restock schedules and scarcity;
- customer history, trust, debt or reservations;
- theft/witness consequences and rumours;
- relationship and reputation effects;
- why a shop opened, closed, refused service or changed policy.

ECONOMY-001 delivers the first instance of that layer: one finite six-unit
production lot, Dava's physically verified delivery, FIFO lot consumption by a
purchase and native-crime theft, and a 3/6 stock result with scarcity 500. The
active command survives OpenMW save/load and the final ledger survives Fork
restart. Native Barter offer calculation is not dynamically overridden because
OpenMW 0.51 exposes no stable Lua hook for it.

The LLM may propose `open_barter` or discuss an item, but it cannot directly set
a price, create a ware or transfer gold. Mechanical effects always pass through
typed validation and return an OpenMW receipt.

## Hybrid conversation ladder

The deterministic foundation is now delivered as a usable turn-taking flow:
every project villager opens a persistent interaction window; the player can
introduce themselves, ask for news, select a provenance/confidence-labelled
journal entry, choose a real carried item from a paged Gift/Sale picker, and use
Arelion's native Barter or shipment-theft branch. Waiting, response, offline and
timeout states remain in-window. Fork decisions remain pending until later-tick
OpenMW inventory/gold or crime reconciliation. See [Journal-Driven Knowledge
and Physical Interactions](journal-driven-knowledge-interactions.md).

DIALOGUE-001 replaces isolated replies with one durable, participant-bound
session and ordered turns. Each response freezes a fingerprinted bounded
snapshot of profile/state, relationship, observer-local identity/reputation,
routine, personal knowledge and market state. Activation owns one exact routine
interruption; goodbye, cancellation, timeout and typed barter resume it exactly
once. The same session/UI survives OpenMW save/load, and the Fork transcript
survives restart. See [Persistent Contextual Conversation
State](persistent-contextual-conversation-state.md).

| Tier | Use | Runtime | Voice |
|---|---|---|---|
| 0: reflex | Danger, greeting, refusal, acknowledgement | Deterministic local rule | Pre-generated and immediate |
| 1: structured | Authored topics and state-machine responses | Fork/OpenMW facts select bounded content | Pre-generated or cached |
| 2: cognition lease | Salient free-form conversation with a key NPC | Bounded Fork context to external LLM; typed proposal validated by reducer | Streamed/generated and cached |

A cognition lease begins because the player enters relevant observational
range, activates the NPC, interrupts a routine, or creates a salient event. In
the delivered system, LLM-002 adds the player-entry path: actor-local OpenMW
evidence must cross a profile salience threshold and durable cooldown before a
bounded wait/initiate lease exists, then OpenMW rechecks physical range and line
of sight. Active free-text conversation and proven terminal repair remain the
other delivered lease sources. The context packet includes identity and profile,
current routine and needs,
location, observed player presentation, personal beliefs and memories,
relationships, current shop state, recent conversation and allow-listed
actions. It includes provenance and hard token/latency budgets.

The delivered worker returns strict structured speech, tone, one allow-listed
action, evidence references and a bounded memory candidate. Reducers reject
unsupported evidence/actions, stale context, wrong claims and reserved control
markers, persist only validated outcomes, and close the lease. The NPC then
continues its normal routine or follows a receipt-confirmed authored command.
See [Bounded Key-NPC Cognition Leases](bounded-key-npc-cognition-leases.md).

## Voice architecture

There is no required or currently identified turnkey ElevenLabs plugin for
OpenMW. Use the companion as a provider-neutral voice worker:

```text
validated speech intent
  -> cache lookup
  -> ElevenLabs / alternative / local fallback
  -> bounded OpenMW VFS voice slot
  -> core.sound.say(NPC, exact subtitle)
  -> started / completed / interrupted receipt
```

OpenMW supplies spatial playback, subtitles and its normal voiced-speech
animation. The provider supplies audio. Fork supplies the validated text,
speaker identity, emotion, priority, causal history and persistence.

Because OpenMW indexes VFS paths at startup, the first prototype pre-creates a
small rotating set of slot filenames. It must test file/decode caching and
interruption before this seam is accepted. A narrow native audio/IPC bridge is
conditional hardening, not the first design.

## Provider decisions

| Option | Best role | Decision |
|---|---|---|
| ElevenLabs | Expressive primary character voices; streaming TTS and optional STT | Preferred quality benchmark behind the adapter |
| Deepgram Aura-2 | Low-latency cloud TTS/STT comparator | Preferred latency/fallback benchmark |
| Azure Speech | Broad voices, SSML and viseme events | Revisit for richer facial animation |
| Google Chirp 3 HD | Bidirectional streaming | Evaluate after its Pre-GA surface stabilizes |
| Inworld TTS | Game-oriented voice-only API | Evaluate voice only; never adopt its second brain |
| Piper | Fast private CPU/offline speech | Local deterministic-line/failure fallback; verify each voice license |
| MOSS-TTSD | Expressive open multi-speaker research | Defer until hardware/latency are measured |
| Coqui XTTS-v2 | Local cloning | Reject commercial/distribution dependency due CPML constraints |

Use new synthetic voice designs, licensed stock voices, or actors' own
authorized verified clones. Ownership of Morrowind does not grant performer
voice-cloning rights.

## What makes this frontier

The frontier comes from joining speech to an epistemic, embodied life rather
than merely generating more lines:

- an NPC speaks only from their beliefs and evidence and can be honestly wrong;
- public speech can be overheard while private speech has explicit listeners;
- appearance, reputation, previous conduct and current duty affect willingness
  and tone;
- conversation can change a schedule, relationship, report, reservation or
  economy only through validated actions;
- memories are salience-filtered outcomes, not an indiscriminate transcript;
- interruption, timeout and provider failure fall back to deterministic play;
- a stuck or terminal routine requests a short repair lease and gives control
  back to native AI after a verified recovery.

Evaluate grounding, character consistency, invalid-action rejection, memory
precision, time to first audible speech, interruption latency and successful
return to routine. These measures matter more than provider-only inference
latency.

## Delivery sequence

1. `VILLAGE-001` **delivered**: ten authored anchors and distributed villagers,
   varied looks/roles, tradehouse staff, native merchant inventory/gold and
   Barter-mode proof.
2. `DIALOGUE-001` **delivered**: bounded contextual sessions, ordered turns,
   persistent UI, typed native Barter, exact routine lifecycle and save/restart
   recovery.
3. `LLM-001`: provider-neutral bounded cognition leases, validated proposals,
   timeouts/fallback and terminal-repair step-out using the delivered schema.
4. `VOICE-001`: provider-neutral voice slots, spatial playback, subtitles,
   cancellation, caching and offline fallback.
5. `PLAYER-001`, `POP-002` and `QA-001`: project player identity, household/
   occupancy semantics and integrated long-run qualification.

## See Also

- [Forkâ€“OpenMW Integration Architecture](integration-architecture.md)
- [Fork and OpenMW Capability Matrix](capability-matrix.md)
- [Causal Supply Chain and Scarcity](causal-supply-chain-and-scarcity.md)
- [Persistent Contextual Conversation State](persistent-contextual-conversation-state.md)
- [OpenMW Ecosystem Reuse Matrix](ecosystem-reuse-matrix.md)
- [Profile-Driven NPC Cognition and Social Knowledge](profile-driven-npc-cognition.md)
- [Contextual Player Presentation and NPC Reactions](contextual-presentation-reactions.md)
- [Replacement World-Shell Pipeline](../openmw/world-shell-pipeline.md)
