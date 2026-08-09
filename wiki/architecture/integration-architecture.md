# Forkâ€“OpenMW Integration Architecture

> Sources: Fork build-36 and OpenMW 0.51 capability inventories, ARCH-001 integration analysis, DISC-001 ecosystem survey, NPC/interaction runtime evidence, BRIDGE-003 transport evidence, PERCEPT-001 observation evidence, MEMORY-001 epistemic-memory evidence, DECIDE-001 arbitration evidence and LLM-002 proactive-cognition evidence, 2026-08-09
> Raw: Integration Analysis, Fork Inventory, OpenMW Inventory, Ecosystem Survey, NPC-001 Evidence, NPC-002 Evidence, INTERACT-003 Evidence, BRIDGE-003 Evidence, ROUTINE-001 Evidence, MEMORY-001 Evidence, DECIDE-001 Evidence, LLM-002 Evidence
> Commit: 6e7f7a9
> Updated: 2026-08-09

## Decision

Build a hybrid runtime with a deliberate split:

- OpenMW is the embodied world: renderer, physics, navigation, mechanics,
  active-cell simulation, presentation and physical truth.
- Fork is the durable brain substrate: observations, memory, relationships,
  beliefs, goals, plans, context, work, causal evidence and cross-session truth.
- External workers are the cognition engines: LLM, embedding, reranking, voice,
  vision and expensive simulation.
- A local companion joins OpenMW to Fork without blocking the frame loop or
  placing credentials in game content. The first prototype uses a supported
  file-in/log-out Lua seam; a native bridge is conditional hardening.

```text
Player / OpenMW 0.51
  rendering Â· physics Â· mechanics Â· navmesh Â· saves
           |
  Lua gameplay adapters (`openmw.brain` project API)
           |
  VFS JSON input + tagged log output (prototype)
  or small native bridge (conditional hardening)
           |
  companion (auth, protocol, subscriptions, reconnect)
           |
  SpacetimeDB game module on Fork build 36
  tables Â· Memory Â· Graph Â· CMT Â· Context Â· Fabric Â· Perception
           |
  external workers (LLM Â· embeddings Â· voice Â· tools)
```

## Why the companion is the default

OpenMW Lua cannot open network connections, write arbitrary files, or access the
operating system. Zdo RPG AI nevertheless proves a supported indirect seam: an
external companion atomically updates a JSON file in an OpenMW data directory,
Lua reads it through the read-only VFS, and Lua emits tagged JSON through
`print()` for the companion to follow in `openmw.log`.

Prototype that seam first with bounded files, session IDs, monotonic sequences,
acknowledgments, idempotency, schema versions, atomic replacement and
save-lineage checks. It avoids an engine fork while the gameplay and Fork
contracts are still changing.

If measured reliability, latency, log-coexistence, security or packaging limits
justify native work, keep the patch as a narrow IPC and Lua-binding layer. That
later design has four advantages:

- OpenMW does not absorb SpacetimeDB authentication/protocol dependencies.
- Network reconnect and worker evolution do not run on the render/game thread.
- the engine fork remains easier to rebase and test;
- secrets stay out of Lua, saves and distributable content.

A direct native SpacetimeDB client inside OpenMW can be reconsidered later. The
Fork tree has an Unreal-specific C++ client, not a documented general OpenMW C++
SDK, while module HTTP/SSE is the supported cross-language escape hatch. The
sidecar can use a first-class Rust, C# or TypeScript client and present a small
stable local protocol to OpenMW.

## Runtime ownership

### OpenMW owns

- the active frame, cell lifecycle and object availability;
- transforms, collision, ray tests, navmesh and path execution;
- combat, inventory, magic and other mechanical resolution;
- animation, UI, sound and media presentation;
- physical save state and compatibility with enabled content/mods.

### Fork owns

- normalized identities and save/session lineage metadata;
- semantic observations and canonical abstract state;
- episodic/semantic memories and retrieval evidence;
- relationship, knowledge, faction and plan graphs;
- beliefs, goals, intentions and their causal provenance;
- background work, retries, results and command/verification receipts;
- context packets for model workers and operator inspection.

### External workers own

- model loading and inference;
- embedding/reranking execution when not using a configured host seam;
- speech synthesis/recognition and media generation;
- domain-specific planning that is not deterministic reducer logic;
- large artifacts stored outside hot database rows.

## Bridge contracts

### OpenMW to Fork: semantic perception

One batch represents one ordered source tick and includes:

- `world_id`, `save_lineage_id` and `world_session_id`;
- `source_id`, `source_epoch` and monotonically increasing `sequence`;
- OpenMW game time and simulation time, with clock domains identified;
- current cell and relevant actor/entity IDs;
- ordered semantic observations and optional evidence references.

Examples are player spoke to NPC, item transferred, actor damaged, actor died,
quest stage changed, faction reaction crossed a threshold, cell entered, door
unlocked or a prior command completed. Do not stream every transform or frame.
The Fork Perception contract already supplies retry, staleness and batch-limit
semantics.

`PERCEPT-001` implements this boundary for five normalized kinds. OpenMW
fan-outs one shared attack, death, native crime, stance or speech stimulus to
all embodied actors; each local script independently supplies cell, distance,
ray/range and signal evidence. Fork stores the stimulus once and derives one
profile/state-weighted detected or rejected result per observer. The production
proof recorded 80 candidates across ten observers and survived save/load,
standalone Fork restart and companion outage/reconnect. See
[Normalized Physical and Social Perception](normalized-perception-and-attention.md).

### Fork epistemic memory boundary

`MEMORY-001` consumes only detected observer evidence. In one transaction it
creates an immutable domain episode, a native Fork memory cell and verified
vector/version/CMT sidecars. Closed typed claims retain current and alternative
values plus revision reasons; native support/contradiction edges must be
traversable before their audit commits. Rejected observations remain intake
evidence and never become memories.

Maintenance changes active-context eligibility without deleting raw episodes.
Recall combines native signals with holder/subject/topic/as-of/confidence
constraints, item and character budgets, per-hit explanations and explicit
abstention. Later goals, dialogue and LLM workers consume these receipts; they
must not scan unbounded memory or treat recalled prose as physical authority.
See [Epistemic Memory and Bounded Recall](epistemic-memory-and-bounded-recall.md).

### Fork explainable decision boundary

Detected attack, death and crime bridge events derive bounded recall and five
typed goal candidates atomically. Fork stores every score component,
eligibility, rank, explanation and canonical source revision. One commitment
per NPC applies a 120-point switch margin with an urgent safety override.

The companion projects no proposal until the dispatch reducer confirms the
profile, state, routine, current perception and commitment revisions still
match. Stale work becomes terminal evidence, not an OpenMW effect. Claimed
actions are limited to confrontation/help, investigation or hide/report; the
actor serializes this movement with presentation reactions, interrupts its
routine, and returns a physical receipt before Fork closes the intention. See
[Explainable Goal and Intention Arbitration](explainable-goal-and-intention-arbitration.md).

### Fork to OpenMW: bounded intention

Each command includes:

- command and idempotency IDs;
- target project entity and required save/session lineage;
- intent type and a bounded typed body or content reference;
- preconditions such as cell, object validity, current package or quest stage;
- expiry/deadline in an explicit clock domain;
- causal parent, policy version and expected verification predicate.

The lifecycle is `Proposed -> Dispatched -> Applied | Rejected -> Verified`.
OpenMW may reject a command that was valid when planned but stale when delivered.
Fork records the rejection as evidence; it does not force the physical world.

## Identity model

Use a project `entity_id` as the durable database key.

- Content-defined objects map to normalized content-file identity plus
  FormId/RefNum and record ID.
- Dynamically created objects receive a project UUID persisted with OpenMW
  script/save state.
- Runtime object IDs are session mappings, not the only durable key.
- A load-order/content digest belongs to each save lineage so a FormId is not
  interpreted under the wrong content configuration.
- Deletion/despawn is a lifecycle transition; database history is retained
  rather than silently reassigning identity.

## NPC cognition loop

1. OpenMW emits meaningful observations for an NPC and its surroundings.
2. A reducer validates ordering, updates canonical state, stores CMT evidence
   and derives memory/graph changes.
3. The deterministic arbiter stores competing goals and either commits an
   allow-listed intention or leaves routine maintenance satisfied; later
   systems may request Fabric work only through a separately validated lease.
4. Context Engine assembles a bounded, provenance-bearing context packet.
5. An external worker produces a structured proposal, never a direct effect.
6. A reducer validates schema, policy, budgets and current revisions, then
   commits or claims a high-level intention.
7. The companion receives the subscription delta and sends the intention to
   OpenMW.
8. Lua resolves the target and uses an AI package, quest/UI operation or other
   approved actuator.
9. OpenMW reports applied/rejected state and later verification evidence.
10. Fork closes the Fabric action and causal chain.

This loop can run at conversational or planning latency while standard OpenMW
AI handles reflexes between decisions.

`DECIDE-001` proves the generalized deterministic branch. A real production run
created 20 threat arbitrations, 100 complete candidate audits, three selected
goal kinds and 11 physical receipts. Commitment hysteresis, emergency switch,
stale dispatch, exact replay, actor action serialization, save/load, companion
reconnect and standalone Fork restart pass on v24.

`NPC-001` proves the deterministic branch through personal observations,
profile-weighted candidate scores, three bounded Travel intentions, physical
destination receipts and two ordered social transmissions. Mutable VFS
snapshots must be reopened and decoded during the session; the proof does not
use a cached one-shot markup load for live intentions. See
[Profile-Driven NPC Cognition and Social Knowledge](profile-driven-npc-cognition.md).

`NPC-002` introduced real player stance, equipment and condition sensing.
`REPUTATION-001` removes its explicit semantic identity context: production
schema 10 accepts only physical/native/causal evidence, while Fork derives
revisioned dirt/blood state, objective deeds, per-observer fame/notoriety and
plausible, exposed or credentialed identity. Fork selects bounded reactions and
closes their lifecycle from OpenMW receipts. Mutable snapshot paths must exist
before the engine indexes its VFS. See
[Contextual Player Presentation and NPC Reactions](contextual-presentation-reactions.md).

`INTERACT-003` hardens the prototype companion lifecycle. Mutable snapshot files
are created before VFS indexing; protocol generations use separate data roots,
mutex names and cursor files; and each companion child exits when its launched
OpenMW process ends. Player choices show waiting, reply and explicit
offline/timeout states. This prevents a hidden old process from both rejecting a
new schema and suppressing the current companion through an unversioned mutex.
See [Journal-Driven Knowledge and Physical Interactions](journal-driven-knowledge-interactions.md).

## Routine ownership and late-event semantics

`ROUTINE-001` uses OpenMW's actual game time and AI/save lifecycle while Fork
owns the durable schedule interpretation. A coarse clock observation activates
one of 50 validated slots, evolves needs, reserves one exclusive station and
issues a revision-bound command in one reducer transaction. Actor-local Lua
executes only allow-listed Travel/Wander packages and returns position-backed
receipts; it cannot rewrite the schedule.

Physical receipts can arrive after a slot boundary, particularly under time
acceleration. Protocol-6 routine wrappers validate these messages, acknowledge
them with terminal status `obsolete`, and retain causal evidence in
`routine_obsolete_event`. Exact replay returns the same result. A superseded
receipt, interruption or stall never mutates the new command and is not a dead
letter. Current-command validation remains strict for ordinary direct reducer
calls.

One no-progress repair retries the station; exhaustion degrades to a safe local
Wander. Save/load restores actor execution state and reconciles it to Fork's
revision. During a companion outage, loaded actors use bounded native fallback,
not an independent durable schedule. See
[Daily NPC Routines and Terminal Repair](daily-routines-and-terminal-repair.md).

## Actuation levels

Prefer the highest safe level:

1. **Narrative/UI:** show a grounded response, add a journal stage, change a
   bounded disposition projection.
2. **AI package:** Travel, Follow, Escort, Wander, Pursue or Combat with explicit
   targets/destinations.
3. **World operation:** spawn/move/enable a validated object or apply inventory
   changes through global APIs.
4. **Direct controls:** movement/turn/use fields only for specialized behavior.

Direct controls are expensive to make robust. They should never be driven over
the database round trip per frame.

## Dialogue and narrative strategy

The safest first adaptive-dialogue surface is a project-owned Lua UI. It can
show generated text and choices without rewriting the legacy dialogue database
at runtime. Fork supplies recalled evidence, relationship state and causal
provenance; an external worker generates a structured response; reducers enforce
allowed topics/actions.

DIALOGUE-001 now delivers that non-LLM authority boundary. Fork persists one
participant-bound session, ordered turns, tone, bounded evidence/revisions and
allow-listed typed actions; OpenMW owns the visible window, exact routine
interrupt/resume and native Barter. Active state survives save/load through a
fresh producer epoch, and Fork restart retains the ordered transcript. See
[Persistent Contextual Conversation State](persistent-contextual-conversation-state.md).

LLM-001 now delivers the temporary proposal layer on that boundary. Native
Context frames cap selected evidence at eight references, 4096 bytes and 1024
estimated tokens; native Fabric owns claims, two attempts, expiry, cancellation,
settlement and dead letters. The external worker returns only strict speech,
tone, evidence and one allow-listed action. Fork revalidates ownership, current
revisions, evidence membership, control markers and replay identity; OpenMW
still supplies the visible or physical receipt. See [Bounded Key-NPC Cognition
Leases](bounded-key-npc-cognition-leases.md).

LLM-002 extends that boundary before manual activation. Actor-local Lua emits
only a physically measured player-entry event; Fork applies the profile
allowlist, salience threshold and durable cooldown, then leases at most a
`wait` or `initiate_conversation` proposal. OpenMW rechecks cell, distance,
line of sight and actor availability immediately before entering the ordinary
conversation path. This prevents per-frame inference and prevents a stale
proposal from possessing or moving an NPC. See [Proactive Observation-Range
Cognition](proactive-observation-range-cognition.md).

Integration with native dialogue, voices and dynamically injected records can
follow, but OpenMW's custom-record surface is versioned and incomplete. Dynamic
quest/content mutation must use explicit templates and allowlists so an LLM
cannot create arbitrary mechanical effects.

For merchant interaction, keep generated conversation separate from mechanical
trade. OpenMW 0.51 can open its native `Barter` mode for a project-created NPC
with service flags, stock and barter gold. Fork records stock provenance,
deliveries, customer history and social consequences after OpenMW reports the
real item/gold transaction. An LLM may propose `open_barter`; it never transfers
items, chooses an unvalidated price or fabricates stock.

Conversation uses three tiers: immediate authored reflexes, deterministic
structured topics, and a temporary LLM cognition lease for active free-form
interaction. The delivered lease receives a bounded provenance-bearing native
Context packet and returns speech plus typed action proposals through native
Fabric. Reducers validate both before anything persists or reaches OpenMW. A
separate proactive observation-range lease remains to be delivered. A
provider-neutral voice worker then
caches or generates audio while `core.sound.say` provides spatial playback,
the exact subtitle and normal voiced animation. See
[Living Village, Commerce, Conversation and Voice](living-village-commerce-dialogue-voice.md).

## Active and inactive world simulation

OpenMW remains authoritative for loaded physical cells. Fork may simulate an
abstract, lower-frequency model for inactive regions: schedules, travel intents,
relationships, rumors, stock/economy and faction state. On cell activation, a
reconciliation reducer computes bounded commands from the abstract state and
the newly observed OpenMW state.

Do not mirror full geometry, physics or inventory continuously. Store only the
abstract facts needed for decisions and reconcile through explicit receipts.

## Failure, save and reconnect behavior

- **Fork unavailable:** keep playing with baseline OpenMW AI; stop issuing new
  brain commands; retain the bounded save-serializable OpenMW outbox.
- **Worker unavailable:** deterministic/rule behavior continues; Fabric retries
  or dead-letters background work.
- **Reconnect:** the companion replays its capture-before-cursor journal;
  unacknowledged OpenMW messages retain their identity until Fork commits.
- **Duplicate message:** an exact envelope returns a no-op; divergent identity
  reuse and stale/gapped sequence fail closed.
- **Poison message:** deterministic invalid data becomes a terminal Fork dead
  letter after bounded attempts and intentionally advances that stream.
- **Save/load:** select or create the save lineage, advance session/epoch, reject
  commands from other lineages and rebuild runtime object mappings.
- **Content change:** compare content/load-order digest and run an explicit
  migration or open a new lineage.
- **Stale intention:** fail the precondition, report rejection and replan.

## Security boundary

- Companion holds Fork credentials and provider secrets.
- Lua receives only bounded typed data.
- Prototype companion accepts only the configured profile/log paths, uses
  atomic bounded files, validates every tagged message and never evaluates
  arbitrary code.
- A later native bridge, if justified, accepts local authenticated IPC from the
  configured companion and applies message/queue ceilings.
- Module reducers validate identity, ownership, revisions, payload size,
  command allowlists and causal linkage.
- Large or untrusted generated content is stored externally by digest and
  scanned/validated before presentation.
- Operator and service-hosting surfaces remain deny-by-default until explicitly
  configured.

## Delivery roadmap

### Stage 0 â€” reproducible baselines

Pin OpenMW 0.51.0 and Fork build 36, define asset/content licensing, select a
small legal test world, and record the content/load-order digest.

### Stage 1 â€” one remembered event

Implement the hardened file/log protocol, companion and minimal Rust module. One
NPC observes one player action; Fork persists it; a custom Lua debug panel
recalls it after both sides restart. No LLM or engine patch is required.

**Delivered by BRIDGE-001:** Ralen Varo in Seyda Neen now proves this loop with
activation text rather than a separate debug panel. The companion uses a tagged
log event, atomic VFS recall/status projection and an idempotent Fork reducer;
fresh runtime checks passed first meeting, restart recall and offline fallback.
See [Remembering Villager Vertical Slice](remembering-villager-vertical-slice.md).

**Hardened by BRIDGE-003:** protocol 6 now wraps all existing domain events in a
lineage-bearing envelope, captures each tagged line before advancing the log
cursor, commits checkpoint/receipt/domain effect atomically, publishes explicit
acknowledgements and handles gaps, transient retry, poison, crash replay,
compaction, log rotation and real Fork/OpenMW restarts. See
[Reliable OpenMW/Fork Bridge Protocol](reliable-bridge-protocol.md).

### Stage 2 â€” one verified intention

**Delivered by NPC-001:** three witnesses consume the same staged incident,
select three different deterministic actions, execute allow-listed native
Travel packages and close the intentions only after destination receipts.
Guard and spouse beliefs arrive through two later social events rather than
omniscient state propagation. DECIDE-001 supersedes its scenario scoring with
the generalized revision-bound arbiter; the old path remains a regression
fixture until SOCIAL-001 replaces its scenario transmission queue.

**Completed by REPUTATION-001:** four embodied observers interpret physical
player loadouts and causal condition without accepting semantic truth from the
client. One objective deed can produce distinct direct, told or overheard
recognition per NPC without retelling inflation. A complete unsupported uniform
plausibly fools Oryn but is exposed by Guard Sera; a real credential or supported
faction establishes authority. The v64 live proof persisted and applied 20
derived reactions with restart/reconnect recovery and zero final engine/Lua
errors.

### Stage 3 â€” memory, relationships and dialogue

Add graph-backed relationships, Context Engine packets and a custom
conversation UI. Introduce an external LLM worker through Fabric only after the
non-LLM loop is deterministic and replayable.

MEMORY-001 delivered the epistemic foundation initially on v16 and retained on v24:
native-backed immutable episodes, typed claim revision, evidence graph edges,
non-destructive maintenance and bounded temporal recall with abstention.
DECIDE-001 now consumes that bounded service for deterministic goal arbitration;
SOCIAL-001 consumes applied report-worthy goals and now physically delivers
private reports and bounded public retellings. OpenMW speakers approach real
listeners; every local actor supplies independent range/ray/signal evidence;
Fork applies directed trust, privacy, confidence/distortion and listener memory
while retaining root/parent/source provenance. DIALOGUE-001 assembles the
participant-bound deterministic conversation context; LLM-001 now adds native
Context/Fabric leases, structured proposal validation, deterministic failure
fallback and the same physical receipt path.

JOURNAL-002 exposes those embodied heard facts beside physical read/witnessed,
direct-conversation and native OpenMW journal sources. Fork holds a revisioned
current view plus immutable history; OpenMW holds local acquisition evidence,
save deduplication, filters/paging and the native Journal surface. Conversation
passes `entry_id~revision`, so stale or retracted knowledge cannot be disclosed.
The protocol-6 snapshot is bounded to 128 current entries and 512 versions.

VILLAGE-001 delivered ten villagers at authored anchors and a stocked native
merchant. DIALOGUE-001 now gives them a persistent deterministic conversation
surface grounded in profile/state, relationships, observer-local identity and
reputation, personal knowledge, routines and market state. Its typed
`open_native_barter` action reaches the existing physical shop only through an
exact receipt. LLM-001 reuses this schema without acquiring mechanical
authority; voice remains a separate later presentation layer.

ECONOMY-001 now closes the first causal shop loop. Fork produces one finite
provenance lot and dispatches a revisioned delivery command; OpenMW temporarily
loads the named carrier, executes Travel and transfers the exact stack. Only an
exact carrier/recipient receipt changes Fork ownership and availability.
Purchase and native-crime theft consume the same lot, update merchant balance
and derive scarcity. The active delivery survives OpenMW save/load, the final
ledger survives Fork restart, and tracked stock is excluded from static
restocking seed inventory. OpenMW 0.51 native Barter remains authoritative; no
unsupported dynamic-offer hook is claimed.

### Stage 4 â€” living-world systems

Add inactive-region ticks, rumors, faction/economy state, plan scheduling,
operator tooling and interest-managed scale tests.

### Separate programs

Multiplayer requires its own replication/authority/prediction design. Worldline
adoption waits for a released public capability and then receives a separate
evaluation. Neither belongs on the critical path for the first living NPC.

## See Also

- [Fork Capability Baseline](../fork/capabilities.md)
- [OpenMW Capability Baseline](../openmw/capabilities.md)
- [Epistemic Memory and Bounded Recall](epistemic-memory-and-bounded-recall.md)
- [Explainable Goal and Intention Arbitration](explainable-goal-and-intention-arbitration.md)
- [Embodied Social Information Flow](embodied-social-information-flow.md)
- [Fork and OpenMW Capability Matrix](capability-matrix.md)
- [OpenMW Ecosystem Reuse Matrix](ecosystem-reuse-matrix.md)
- [Reliable OpenMW/Fork Bridge Protocol](reliable-bridge-protocol.md)
- [Profile-Driven NPC Cognition and Social Knowledge](profile-driven-npc-cognition.md)
- [Daily NPC Routines and Terminal Repair](daily-routines-and-terminal-repair.md)
- [Contextual Player Presentation and NPC Reactions](contextual-presentation-reactions.md)
- [Living Village, Commerce, Conversation and Voice](living-village-commerce-dialogue-voice.md)
- [Causal Supply Chain and Scarcity](causal-supply-chain-and-scarcity.md)
- [Persistent Contextual Conversation State](persistent-contextual-conversation-state.md)
- [Bounded Key-NPC Cognition Leases](bounded-key-npc-cognition-leases.md)
