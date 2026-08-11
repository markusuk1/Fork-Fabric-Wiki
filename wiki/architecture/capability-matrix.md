# Fork, OpenMW, and Skyrim Capability Matrix

> Sources: Fork build-36 and OpenMW 0.51 capability inventories, ARCH-001 integration analysis, NPC/interaction/commerce evidence, PROGRAM-001 audit, BRIDGE-003 transport evidence, PROFILE-001 model evidence, ROUTINE-001 scheduler evidence, PERCEPT-001 observation evidence, MEMORY-001 epistemic-memory evidence, DECIDE-001 arbitration evidence, SOCIAL-001 embodied-flow evidence, JOURNAL-002 general-journal evidence, REPUTATION-001 derived-identity evidence, ECONOMY-001 causal-supply evidence, DIALOGUE-001 contextual-session evidence, LLM-002 proactive-cognition evidence, POP-002 exact lifecycle evidence and QA-001 integrated qualification, 2026-08-09 to 2026-08-10
> Raw: [Fork Inventory](../../raw/fork/2026-08-07-fork-build36-capability-inventory.md), [OpenMW Inventory](../../raw/openmw/2026-08-07-openmw-0.51-capability-inventory.md), [Integration Analysis](../../raw/architecture/2026-08-07-fork-openmw-integration-analysis.md), [Ecosystem Survey](../../raw/openmw/2026-08-07-openmw-ecosystem-prior-art-survey.md), [NPC-001 Evidence](../../raw/architecture/2026-08-08-npc-001-profile-driven-witness-evidence.md), [NPC-002 Evidence](../../raw/architecture/2026-08-08-npc-002-contextual-appearance-evidence.md), [VILLAGE-001 Evidence](../../raw/openmw/2026-08-08-village-001-spatial-commerce-evidence.md), [INTERACT-001 Evidence](../../raw/architecture/2026-08-08-interact-001-journal-interaction-evidence.md), [INTERACT-003 Evidence](../../raw/architecture/2026-08-08-interact-003-usable-interactions-evidence.md), [PROGRAM-001 Audit](../../raw/architecture/2026-08-08-program-001-system-gap-audit-research.md), [COMMERCE-001 Evidence](../../raw/architecture/2026-08-08-commerce-001-negotiated-trade-evidence.md), [BRIDGE-003 Evidence](../../raw/architecture/2026-08-09-bridge-003-reliable-transport-evidence.md), [PROFILE-001 Evidence](../../raw/architecture/2026-08-09-profile-001-unified-npc-model-evidence.md), [ROUTINE-001 Evidence](../../raw/architecture/2026-08-09-routine-001-daily-scheduler-evidence.md), [MEMORY-001 Evidence](../../raw/architecture/2026-08-09-memory-001-epistemic-lifecycle-evidence.md), [DECIDE-001 Evidence](../../raw/architecture/2026-08-09-decide-001-explainable-goal-arbitration-evidence.md), [SOCIAL-001 Evidence](../../raw/architecture/2026-08-09-social-001-embodied-information-flow-evidence.md), [SOCIAL-001 Rollback Correction](../../raw/architecture/2026-08-09-social-001-save-load-rollback-correction.md), [JOURNAL-002 Evidence](../../raw/architecture/2026-08-09-journal-002-general-player-journal-evidence.md), [LLM-002 Evidence](../../raw/architecture/2026-08-09-llm-002-proactive-observation-evidence.md), [PLAYER-001 Native Production Evidence](../../raw/architecture/2026-08-09-player-001-native-character-production-evidence.md), [POP-002 Exact Lifecycle Evidence](../../raw/architecture/2026-08-09-pop-002-exact-lifecycle-reconciliation-evidence.md), [QA-001 Evidence](../../raw/architecture/2026-08-10-qa-001-integrated-living-village-evidence.md), [DIALOGUE-002 Final Qualification](../../raw/architecture/2026-08-10-dialogue-002-final-qualification-evidence.md)
> Commit: c0cea44
> Updated: 2026-08-11
> Reconciliation: [AUDIT-001 Programme Closure](../../raw/architecture/2026-08-10-audit-001-programme-closure-reconciliation.md)

## Overview

OpenMW and Fork are complementary. OpenMW is a mature real-time RPG runtime;
Fork is a transactional memory, reasoning-evidence and orchestration substrate.
The bridge converts physical game events into semantic durable observations and
converts validated high-level intentions back into locally executed game
actions.

## Executor selection overlay

The delivered OpenMW rows below remain the authoritative record of that
implementation. SKYRIM-001 through SKYRIM-006 subsequently qualified Skyrim
SE/AE+SKSE as the physical executor for the next build without changing Fork's
ownership. The decisive additions are a receipt-bound native bridge, visible
multi-location native routines, rollback/restart recovery, 29-33 loaded actors
beside 200 transactional Fork actors, and a reversible same-save Whiterun test:
zero loaded actors with 163 actor/26 narrative-quest suppressions versus 31
actors with 31 packages when restored. Whole-world suppression and the first
Fork-owned Skyrim village remain production work, not delivered capability.

Evidence: [Skyrim suppression proof](../../raw/skyrim/2026-08-11-skyrim-suppression-proof.md)
and [final platform decision](platform-decision-openmw-vs-skyrim.md).

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
| Native project player identity | OpenMW five-screen creation -> opaque player/save lineage -> ACK-gated Fork registration -> native save/load and automatic production resume | Delivered on v178: Delantris survives in-process load, OpenMW/companion/Fork restart, exact current-schema subsystem regressions and ordinary desktop launch with no focus takeover |
| Persistent NPC memory | Detected OpenMW evidence -> immutable domain episode + Fork native Memory/Vector/Version/CMT -> typed claim graph -> bounded recall | Delivered and regression-proven on v24: rejection audit without invented memory, reinforcement/supersession/contest, traversable support/contradiction edges, temporal recall, abstention and non-destructive maintenance |
| Unified NPC profile/state | OpenMW physical records + validated authored manifest -> revisioned Fork profile/current state/history -> bounded VFS projection | Delivered for ten embodied and one offscreen person; exact reseed/upgrade/restart and invalid-startup behavior remain proven. v24 is the integrated arbitration qualification revision; current consolidated production is v203 |
| Daily routines and repair | OpenMW game time/AI/save state -> Fork slots, reservations, commands, interruptions and transitions -> physical receipts | Delivered and regression-proven on v24: 50 complete slots, 40 exclusive stations, need evolution, interruption/resumption, bounded repair, obsolete-event audit, offline fallback and save/Fork-restart proof |
| Household occupancy and inactive actors | Typed home/work manifest -> Fork desired presence/capacity + observed embodiment -> exact inactive-only OpenMW object materialization -> lineage/revision-bound physical receipt | Delivered on v183: 15 places, 11 people, five homes, ten unique embodied objects, capacity/replay protection, exact lifecycle/census, save/load, Fork restart, crash/reconnect, day/night visual proof and zero final runtime errors/dead letters |
| Normalized perception and attention | Actor-local physical/social source -> independent cell/ray/range evidence -> profile/state-weighted Fork appraisal | Delivered on v24 for attack, death, native crime, stance and speech: ten observers, detected/rejected evidence, exact replay, save/restart and outage recovery |
| Explainable relationships | Fork graph edges plus causal events projected into disposition/faction behavior | Relationship/trust evidence is delivered; automatic native disposition/faction projection was not accepted into PROGRAM-001 and requires a new task contract |
| Goal-directed NPC behavior | Profile/state/routine + detected evidence + bounded recall -> five audited Fork goals -> commitment/revision-bound dispatch -> OpenMW AI package -> receipt | Delivered on v24: 20 live threat arbitrations/100 candidates, three selected goals, retention/emergency switch, stale rejection, physical closure and recovery |
| Witness-specific beliefs and rumours | Applied report goal -> Fork claim -> native speaker approach -> independent listener hearing -> privacy/trust/distortion -> memory -> bounded retell | Delivered: private rejection, genuine overhearing, hops 0/1/2, expiry/retry and restart/reconnect recovery; an isolated rollback proof now requires Fork-authoritative re-authorization or terminal suppression before restored OpenMW social work may continue |
| Derived identity and reputation reactions | OpenMW physical/native/causal evidence -> Fork revisioned condition + deduplicated deed/exposure -> capability-sensitive identity appraisal -> bounded response/receipt | Delivered on v64: causal grime/blood/wash, native stats/factions, real credentials, observer-local reputation, plausible/exposed disguise, exact replay and restart/reconnect proof |
| Grounded deterministic conversations | Activation -> bounded Fork context snapshot -> ordered turn/tone/typed action -> persistent OpenMW UI/effect -> exact receipt and routine resume | Delivered on v100: profile/state, relationship, observer-local identity/reputation, knowledge, routine and market grounding; bounded history; exact replay; native Barter; save/load, restart and visual proof |
| Natural deterministic speech | Canonical profile -> bounded register/cadence/manner/role voice -> grounded stable template -> separate UI/voice metadata -> exact receipt | Delivered on v203: 11 distinct profile styles/replies; fail-closed Fork/worker/OpenMW diagnostic and stage-direction boundaries; exact 3440x1440/1.50 WGC frame; matching 6,467 ms Piper WAV; save/load/restart and zero final engine/Lua/dead-letter errors |
| General player journal | Physical read/sight/hearing, direct conversation or native quest text -> typed Fork acquisition/version -> bounded snapshot/filter/picker -> revision-checked disclosure | Delivered on v57 with five source kinds, append-only correction/retraction, five-entry pages, native Journal access, save/load and restart/reconnect proof |
| Adaptive conversations | Schema-12/13 session -> exact-budget native Context frame -> native Fabric claim -> external structured proposal -> independent validation -> same turn/UI/receipt path | Delivered on v116: provider-neutral worker, injection/stale/owner/action rejection, crash reclaim, timeout fallback, active save/load, Fork restart, visible provenance and zero final errors; fixture is not model-quality evidence |
| Proactive key-NPC attention | Actor-local player entry/LOS -> profile/state salience and cooldown -> schema-14 Context/Fabric wait/initiate lease -> fresh OpenMW physical recheck -> ordinary conversation/receipt | Delivered on v134: bounded entry/hysteresis, durable coalescing, stale/provider/TTL safe wait, zero-turn initiation, save/load and Fork restart continuity; fixture is not model-quality evidence |
| Voiced hybrid conversations | Deterministic tiers plus temporary LLM lease -> validated Fork/Fabric speech intent -> verified cache/provider WAV -> 32-slot OpenMW actor playback -> exact receipt | Delivered and updated on v203: Piper baseline, ElevenLabs/Deepgram adapters, 30-second bounded admission, cache/recovery/restart continuity, final diagnostic/stage-direction rejection and exact live subtitle/WAV digest proof |
| Living village commerce | Authored spatial anchors/schedules + native merchant/barter + Fork production, provenance, delivery, balance and scarcity state | Delivered on v76: finite six-unit production lot, embodied carrier delivery, exact receipt closure, purchase/theft reconciliation, FIFO provenance, scarcity, visual status, save/load and Fork-restart proof |
| Dynamic quests/narrative | Fork state machine/graph -> bounded quest/journal/content commands | Feasible, but not accepted into PROGRAM-001; it requires a separate authored quest/policy programme |
| Inactive-region simulation | Fork abstract world ticks; reconcile only material state when cells activate | Household schedule presence and exact inactive-object reconciliation are delivered; general abstract travel, physics, combat and inventory were not accepted into PROGRAM-001 |
| Causal replay/debugging | CMT links observation, decision, command and verification receipts | Native Fork strength |
| Reliable bridge recovery | Stable OpenMW outbox -> capture journal -> atomic Fork receipt/effect -> VFS ack | Delivered: duplicate/gap/poison/backpressure/rotation, companion/Fork restart, audited obsolete restored-save presentation and bounded atomic-projection sharing recovery proved |
| Save rollback identity | Restored OpenMW state -> new source epoch/per-epoch sequence -> current Fork projection reconciliation -> digest-checked ack -> exact replay/divergence validation | Delivered and exercised by dialogue and embodied-social save/load: restored social work cannot re-emit changed physical evidence after Fork has already completed its dispatch |
| Agent/model work farm | Fabric claims work; external workers return content-addressed results | Native coordination, external execution |
| Human oversight | Fork web surfaces/optional Constructs plus in-game debug UI | Optional operator tooling, not accepted into PROGRAM-001; any product UI needs its own workflow and authority contract |
| Integrated production qualification | Owned current-module regressions + 24-hour OpenMW day + real cell round-trip + save/load + injected outage + voice playback + resource/error budgets + production fingerprint | Delivered by QA-001: 10/10 gates in 332.1 seconds, 465.5 MiB peak, 2.8 MiB post-warm growth, 2.396 cores, zero final engine/Lua/dead-letter errors and unchanged 113-table v202 digest |
| Multiplayer persistent world | Fork authority plus custom OpenMW replication/netcode | Explicitly a separate major programme, not a residual living-village gap or emergent benefit |

The non-delivered rows are capability ideas or platform boundaries, not hidden
accepted tasks. Promoting one requires prior-art research, a stable task ID and
explicit acceptance criteria; see [Programme Closure Reconciliation](programme-closure-reconciliation.md).

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
