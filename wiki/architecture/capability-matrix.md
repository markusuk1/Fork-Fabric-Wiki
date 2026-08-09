# Fork and OpenMW Capability Matrix

> Sources: Fork build-36 and OpenMW 0.51 capability inventories, ARCH-001 integration analysis, NPC/interaction/commerce evidence, PROGRAM-001 audit, BRIDGE-003 transport evidence, PROFILE-001 model evidence, ROUTINE-001 scheduler evidence, PERCEPT-001 observation evidence, MEMORY-001 epistemic-memory evidence, DECIDE-001 arbitration evidence, SOCIAL-001 embodied-flow evidence, JOURNAL-002 general-journal evidence, REPUTATION-001 derived-identity evidence, ECONOMY-001 causal-supply evidence, DIALOGUE-001 contextual-session evidence and LLM-002 proactive-cognition evidence, 2026-08-09
> Raw: Fork Inventory, OpenMW Inventory, Integration Analysis, Ecosystem Survey, NPC-001 Evidence, NPC-002 Evidence, VILLAGE-001 Evidence, INTERACT-001 Evidence, INTERACT-003 Evidence, PROGRAM-001 Audit, COMMERCE-001 Evidence, BRIDGE-003 Evidence, PROFILE-001 Evidence, ROUTINE-001 Evidence, MEMORY-001 Evidence, DECIDE-001 Evidence, SOCIAL-001 Evidence, JOURNAL-002 Evidence, LLM-002 Evidence
> Commit: 6e7f7a9
> Updated: 2026-08-09

## Overview

OpenMW and Fork are complementary. OpenMW is a mature real-time RPG runtime;
Fork is a transactional memory, reasoning-evidence and orchestration substrate.
The bridge converts physical game events into semantic durable observations and
converts validated high-level intentions back into locally executed game
actions.

## Ownership matrix

| Capability | OpenMW 0.51 | Fork build 36 | Combined design and owner |
|---|---|---|---|
| Assets/content loading | Strong: Morrowind and OpenMW-native content/VFS | None | OpenMW owns; assets are separately licensed inputs |
| World rendering | Strong, frame-time renderer and visual effects | None | OpenMW only |
| Physics/collision | Strong, Bullet-backed | None | OpenMW only |
| Navigation/pathfinding | Navmesh/pathgrid and Lua queries | Can store plans/graphs, not walk a mesh | Fork chooses goals; OpenMW computes/executes paths |
| Actor mechanics | Combat, inventory, magic, crime, stats | Can model intent/state abstractly | OpenMW resolves effects; Fork remembers/reasons over results |
| Conventional NPC AI | AI packages and actor controls | No game-specific actuator | Fork proposes high-level intent; OpenMW AI packages execute |
| Dialogue/quests | Existing dialogue, faction, journal and UI systems | Memory, context, graphs, external work | OpenMW presents/applies; Fork selects/generates grounded content |
| Commerce | Native barter UI, merchant services, inventory, barter gold and mechanical transfer | Durable stock provenance, deliveries, customer history, reputation and causal business state | OpenMW resolves each trade; Fork owns business meaning and consequences |
| Content authoring | OpenMW-CS and mod formats | Registry/web tooling only | OpenMW-CS for world content; Fork tools for live brain data |
| Local saves | Full physical-world save state | Independent durable database | Explicit save-lineage synchronization; neither mirrors the other wholesale |
| Transactions | Game update/save semantics, not distributed DB reducers | Strong serial reducers and durable log | Fork owns cognitive/system transactions |
| Live data delivery | In-process Lua events | Committed WebSocket subscriptions and finite SSE | Companion translates subscriptions to bounded engine events |
| Cross-runtime reliability | Save-serializable bounded outbox and VFS acknowledgements | Atomic checkpoint, message, dead-letter and domain reducer state | Protocol 6 supplies ordered at-least-once delivery with effectively-once accepted effects |
| Semantic recall | None beyond scripted/local storage | Memory Engine plus project typed filters, as-of time, strict item/character budgets and abstention | Fork owns; v24 retains the vector/multi-vector/graph/CMT and domain-bound proof |
| Relationships/knowledge | Factions/disposition and record links | Native graph with provenance/confidence | Fork holds rich graph; OpenMW receives gameplay projection |
| Causal explanation | Gameplay state/logs, no native causal ledger | CMT why/causes/effects/blast radius | Fork owns durable explanations |
| Context assembly | Script-selected local data | Exact-budget Context Engine | Fork builds grounded model/agent context |
| Work orchestration | Game loop and Lua timers | Fabric jobs, claims, leases, retries and replay | Fork coordinates background workers; OpenMW never blocks on them |
| Model inference | None | Seams/orchestration only; models external | External workers own LLM/embedding/voice/vision execution |
| Perception/control | Actor-local Hit/Died, stance, crime results, cell/ray/range observation and actuation | Durable shared stimuli plus observer-specific attention/confidence evidence | OpenMW measures physical truth; Fork normalizes and retains detected/rejected evidence |
| Social information | Native actor movement, active-cell identity, local range/ray/signal and subtitle presentation | Typed messages, directed trust, privacy, confidence/distortion, listener memory and provenance graph | OpenMW proves an utterance was physically available; Fork decides and remembers who learned what |
| Presentation and reputation | Actual stance, equipment, wear, inventory credentials, native reputation/bounty/factions and causal movement/swim/combat signals | Exact evidence, causal condition, objective deeds, per-observer exposure and capability-sensitive identity appraisal | OpenMW reports physical/native facts; Fork derives cleanliness, blood, fame/notoriety and apparent/authentic identity |
| Web/operator tooling | In-game Lua UI | HTTP/SSE/apps, Security Centre, optional Constructs | OpenMW UI for player; Fork web/Constructs for operators |
| Multiplayer | Not in mainline | Database networking, not a game netcode stack | Not provided; requires a separate multiplayer architecture |
| Counterfactual worlds | No persistent alternate simulation | CMT comparisons released; Worldline unshipped | Baseline can evaluate proposals externally; future Worldline may add safe branches |

## Resulting game capabilities

| Result | How it is produced | Initial maturity |
|---|---|---|
| Persistent NPC memory | Detected OpenMW evidence -> immutable domain episode + Fork native Memory/Vector/Version/CMT -> typed claim graph -> bounded recall | Delivered and regression-proven on v24: rejection audit without invented memory, reinforcement/supersession/contest, traversable support/contradiction edges, temporal recall, abstention and non-destructive maintenance |
| Unified NPC profile/state | OpenMW physical records + validated authored manifest -> revisioned Fork profile/current state/history -> bounded VFS projection | Delivered for ten embodied and one offscreen person; exact reseed/upgrade/restart and invalid-startup behavior remain proven, with v24 now the production target |
| Daily routines and repair | OpenMW game time/AI/save state -> Fork slots, reservations, commands, interruptions and transitions -> physical receipts | Delivered and regression-proven on v24: 50 complete slots, 40 exclusive stations, need evolution, interruption/resumption, bounded repair, obsolete-event audit, offline fallback and save/Fork-restart proof |
| Normalized perception and attention | Actor-local physical/social source -> independent cell/ray/range evidence -> profile/state-weighted Fork appraisal | Delivered on v24 for attack, death, native crime, stance and speech: ten observers, detected/rejected evidence, exact replay, save/restart and outage recovery |
| Explainable relationships | Fork graph edges plus causal events projected into disposition/faction behavior | Feasible after identity mapping |
| Goal-directed NPC behavior | Profile/state/routine + detected evidence + bounded recall -> five audited Fork goals -> commitment/revision-bound dispatch -> OpenMW AI package -> receipt | Delivered on v24: 20 live threat arbitrations/100 candidates, three selected goals, retention/emergency switch, stale rejection, physical closure and recovery |
| Witness-specific beliefs and rumours | Applied report goal -> Fork claim -> native speaker approach -> independent listener hearing -> privacy/trust/distortion -> memory -> bounded retell | Delivered and regression-proven on v45: private rejection, genuine overhearing, hops 0/1/2, expiry/retry, active save/load and restart/reconnect recovery |
| Derived identity and reputation reactions | OpenMW physical/native/causal evidence -> Fork revisioned condition + deduplicated deed/exposure -> capability-sensitive identity appraisal -> bounded response/receipt | Delivered on v64: causal grime/blood/wash, native stats/factions, real credentials, observer-local reputation, plausible/exposed disguise, exact replay and restart/reconnect proof |
| Grounded deterministic conversations | Activation -> bounded Fork context snapshot -> ordered turn/tone/typed action -> persistent OpenMW UI/effect -> exact receipt and routine resume | Delivered on v100: profile/state, relationship, observer-local identity/reputation, knowledge, routine and market grounding; bounded history; exact replay; native Barter; save/load, restart and visual proof |
| General player journal | Physical read/sight/hearing, direct conversation or native quest text -> typed Fork acquisition/version -> bounded snapshot/filter/picker -> revision-checked disclosure | Delivered on v57 with five source kinds, append-only correction/retraction, five-entry pages, native Journal access, save/load and restart/reconnect proof |
| Adaptive conversations | Schema-12/13 session -> exact-budget native Context frame -> native Fabric claim -> external structured proposal -> independent validation -> same turn/UI/receipt path | Delivered on v116: provider-neutral worker, injection/stale/owner/action rejection, crash reclaim, timeout fallback, active save/load, Fork restart, visible provenance and zero final errors; fixture is not model-quality evidence |
| Proactive key-NPC attention | Actor-local player entry/LOS -> profile/state salience and cooldown -> schema-14 Context/Fabric wait/initiate lease -> fresh OpenMW physical recheck -> ordinary conversation/receipt | Delivered on v134: bounded entry/hysteresis, durable coalescing, stale/provider/TTL safe wait, zero-turn initiation, save/load and Fork restart continuity; fixture is not model-quality evidence |
| Voiced hybrid conversations | Deterministic tiers plus temporary LLM lease -> validated speech intent -> cached/provider audio -> OpenMW spatial playback -> receipt | Native playback and providers verified; voice-slot seam and end-to-end latency remain to prove |
| Living village commerce | Authored spatial anchors/schedules + native merchant/barter + Fork production, provenance, delivery, balance and scarcity state | Delivered on v76: finite six-unit production lot, embodied carrier delivery, exact receipt closure, purchase/theft reconciliation, FIFO provenance, scarcity, visual status, save/load and Fork-restart proof |
| Dynamic quests/narrative | Fork state machine/graph -> bounded quest/journal/content commands | Feasible but needs strong authoring/policy constraints |
| Inactive-region simulation | Fork abstract world ticks; reconcile only material state when cells activate | Feasible; must avoid dual authority |
| Causal replay/debugging | CMT links observation, decision, command and verification receipts | Native Fork strength |
| Reliable bridge recovery | Stable OpenMW outbox -> capture journal -> atomic Fork receipt/effect -> VFS ack | Delivered: duplicate/gap/poison/backpressure/rotation, companion crash and Fork process restart proved |
| Save rollback identity | Restored OpenMW state -> new source epoch/per-epoch sequence -> digest-checked ack -> Fork exact replay/divergence validation | Delivered and exercised by active DIALOGUE-001 save/load; prevents restored sequence collisions and identity-only acknowledgement |
| Agent/model work farm | Fabric claims work; external workers return content-addressed results | Native coordination, external execution |
| Human oversight | Fork web surfaces/optional Constructs plus in-game debug UI | Available after configuration and UI work |
| Multiplayer persistent world | Fork authority plus custom OpenMW replication/netcode | Major separate program, not an emergent benefit |

## What the combination still does not provide

- Morrowind asset redistribution rights.
- An LLM or embedding model running inside the database.
- A direct Lua network integration; the delivered reliable bridge intentionally
  uses a companion-mediated VFS-file/tagged-log seam without patching OpenMW.
- Frame-by-frame remote control without unacceptable latency and failure risk.
- Mainline OpenMW multiplayer.
- Shipped Worldline speculation/promotion.
- Automatic semantic correctness of model output; reducers and game-side
  preconditions must validate every durable or physical effect.

The result rows above describe architecture and vertical maturity, not a claim
that every system is production-complete. The canonical as-built/target/gap
classification and executable evidence standard are in the [Production
Living-World Gap-Closure Programme](system-gap-closure-programme.md).

## See Also

- [Fork Capability Baseline](../fork/capabilities.md)
- [OpenMW Capability Baseline](../openmw/capabilities.md)
- [Integration Architecture](integration-architecture.md)
- [Reliable OpenMW/Fork Bridge Protocol](reliable-bridge-protocol.md)
- [OpenMW Ecosystem Reuse Matrix](ecosystem-reuse-matrix.md)
- [Profile-Driven NPC Cognition and Social Knowledge](profile-driven-npc-cognition.md)
- [Unified NPC Profile and State](unified-npc-profile-and-state.md)
- [Explainable Goal and Intention Arbitration](explainable-goal-and-intention-arbitration.md)
- [Embodied Social Information Flow](embodied-social-information-flow.md)
- [Contextual Player Presentation and NPC Reactions](contextual-presentation-reactions.md)
- [Living Village, Commerce, Conversation and Voice](living-village-commerce-dialogue-voice.md)
- [Causal Supply Chain and Scarcity](causal-supply-chain-and-scarcity.md)
- [Persistent Contextual Conversation State](persistent-contextual-conversation-state.md)
- [Proactive Observation-Range Cognition](proactive-observation-range-cognition.md)
- [Production Living-World Gap-Closure Programme](system-gap-closure-programme.md)
