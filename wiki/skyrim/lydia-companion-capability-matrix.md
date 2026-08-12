# Lydia and Reusable Companion Capability Matrix

> Sources: COMPANION-001 source/configuration, exact final Skyrim runs
> `companion-final-baseline-full-02`, `companion-final-orders-01`,
> `companion-final-shortcuts-01`, `companion-final-burden-boundaries-01`,
> `companion-chatter-callout-native-08`, `companion-corpse-natural-discovery-01`,
> `companion-container-qualification-06`, developer ore run
> `companion-ore-qualified-18`, continuous observation
> `companion-ore-final-19`, and the qualified slices' exact-hash regressions, and
> the repository's released Fork systems, collected 2026-08-12
> Raw: [ore-mining development boundary](../../raw/skyrim/2026-08-12-companion-ore-mining-development-boundary.md); [container-looting qualification](../../raw/skyrim/2026-08-12-companion-container-looting-qualification.md); [corpse-looting qualification](../../raw/skyrim/2026-08-12-companion-corpse-looting-qualification.md); [chatter/callout qualification](../../raw/skyrim/2026-08-12-companion-chatter-callout-qualification.md); [final Orders/shortcut qualification](../../raw/skyrim/2026-08-12-companion-orders-shortcuts-qualification.md); [burden repair](../../raw/skyrim/2026-08-12-companion-burden-repair-evidence.md); [owner programme](../../raw/skyrim/2026-08-12-companion-orders-burden-llm-owner-requirements.md); [owner gather pass/voice failure](../../raw/skyrim/2026-08-12-companion-owner-gather-pass-voice-failure.md); [Say branch research](../../raw/skyrim/2026-08-12-companion-say-branch-binding-research.md); [Say branch repair evidence](../../raw/skyrim/2026-08-12-companion-say-branch-repair-evidence.md); [critical dependency closure](../../raw/skyrim/2026-08-11-companion-critical-dependency-closure.md)
> Commit: 3098f74
> Updated: 2026-08-12

## Purpose and status language

This is the admission matrix for every proposed Lydia or general companion
feature. It exposes what can be composed from proven machinery, what needs a
bounded extension, and what the present architecture cannot truthfully claim.
It prevents a voiced line, a configuration value or a discovered target from
being mistaken for an embodied feature.

Status has four precise meanings:

| Status | Meaning |
|---|---|
| **Proven now** | Exercised through the installed Skyrim production path with an observed terminal result and regression coverage |
| **Available to integrate** | Released elsewhere in this repository, but not connected to the Skyrim Lydia control loop |
| **Extension required** | The reusable base exists, but the feature needs a new grounded signal, native action adapter, verifier or content |
| **Unsupported by the current mechanism** | Another architecture or engine facility is required; composition alone cannot deliver it |

The physical natural-flora gather is now **owner accepted**. In the ordinary
save Lydia walked to a Purple Mountain Flower, used a natural crouch, gained
exactly one ingredient, showed the native HUD result and returned. The owner
accepted the roughly one-second native animation; the prior forced three-second
visible-pose requirement is withdrawn.

The owner subsequently confirmed the repaired branch-bound gathering audio is
audible. The expanded final build is regression-qualified, but the combined
burden/Orders/shortcut surface still awaits owner acceptance because the owner
is unavailable. This is a deferred experiential gate, not a reason to stop the
programme.

The contract is: commit the valid target, audibly speak the gathering intention
before movement, walk, use the natural accepted animation, verify the produce
delta, then show the exact item/quantity result.

Ore mining is at an earlier evidence state. One complete strike is
**developer proven**, including ordinary Orders selection, speech, movement,
the furniture-family pickaxe graph event, Lydia-only inventory, depletion,
HUD, return and failure boundaries. Two continuous strikes were observed on
the current build, but the focused checker still assumes a one-strike end
state; therefore continuous mining and the current combined build are not yet
regression-qualified or ready for owner acceptance.

## Mechanism matrix

| Layer | Current Lydia mechanism and proof | Reusable beyond Lydia | Enables now | Gap or limiting boundary |
|---|---|---|---|---|
| Identity and role | Data-driven Lydia profile binds form `666772`, `housecarl`, traits and thresholds | Replace the actor identity, role, traits, voice and permissions in a companion profile | Per-companion policy and role defaults | Profile loading exists locally; it is not yet synchronized with Fork's canonical revisioned NPC profile/state |
| Observation | One-second grounded samples of player health/combat/burden/movement/idle, companion capacity/context and bounded lawful nearby targets; loaded-3D generic targets use same-cell/range gates and Havok LOS-layer visibility beyond a collision-safe close floor | Same sampler can bind another loaded follower | Burden assistance, emergency care, idle gathering, grounded resource callouts and context safety | No complete semantic hearing, appearance, crime-witness, relationship, needs, weather, formation, threat ranking or cross-cell sensing in this slice |
| Deterministic arbitration | Priority is emergency care, burden edge, then idle gather; typed thresholds, cooldowns and one active action | Policy engine is actor-neutral | Safe autonomous reflexes with predictable behavior | Current rules are compact and local; traits do not yet calculate volunteering, refusal, courage, utility or dialogue style |
| Native conversation and Orders | Ordinary Lydia dialogue with no forced project INFO; voiced top-level Config and Orders roots; vanilla topic selections; TIF/Papyrus actions; native Yes/No | Topic/branch templates and typed commands can be generated per voice/actor | Discoverable tactics, continuous gather, corpse looting, lawful container looting, stop commands, manual pack help, stop all and consent-gated transfer | Finite authored choices only; no free-text intent parser, dynamic item picker, quantities or negotiation surface |
| Policy persistence | Four typed toggles (`burden_help`, `combat_care`, `resource_gathering`, `loose_loot`) use replace-safe local persistence | Schema can be expanded and projected per companion | Settings survive reload/restart | Not save-lineage scoped or Fork-authoritative yet; no version conflict/reconciliation across multiple companions |
| Safe inventory transfer | Consent plus safe stand-off, item eligibility, target burden ratio, capacity reserve, bounded itemised HUD summary, and explicit full/no-safe/decline/timeout/cooldown terminals | Generic transfer filter can serve porters, carts and storage workers | Autonomous or manually ordered carry assistance for ordinary eligible items | No category/quantity chooser, equipped-item loadout plan, price, barter, container route or multi-trip logistics |
| Target discovery | Capped same-cell enumeration stages handles and revalidates outside locks; ownership/crime/range/loaded-3D/visibility gates; useful flora can be named in a native consent callout; dead actors and world containers use action-specific legality/content filters; generated ore data classifies 581 installed bases, 1,735 authored references and 10 linked furniture families | Generic candidate pipeline for loose loot, activators, corpses, containers and authored ore | Find lawful harvestables, useful corpses, unlocked unowned non-criminal world containers and supported authored ore deposits | Inaccessible interiors, merchant/personal-storage semantics, dynamic/modded vein compatibility and strategic usefulness beyond conservative weight/value/capacity rules remain unqualified |
| Embodied locomotion | Dynamic quest aliases plus purpose-built native temporary Travel package; distance/arrival and follower-return receipts | Core action runner for fetch, inspect, deliver, guard-post, follow-up and local errands | Visible local movement to a loaded target and return | Each action still needs a suitable package/target; cross-cell doors, long journeys, unreachable navmesh and moving targets need dedicated routing and recovery |
| Animation and interaction | Owner-observed flora travel/crouch; regression-qualified corpse/container travel and targeted native searches; developer-proven ore travel plus furniture-family Bethesda pickaxe graph event gated on Lydia's real `AddToInventory` event | Reuse the action-state-machine shape with an action-specific native animation and exact terminal | Owner-accepted natural-herb gathering, regression-qualified corpse/container searches and one developer-proven ore strike | Continuous ore, broad current-build regressions, owner ore inspection, cross-cell routes and feature-specific player cancellation remain open |
| Lawful ore mining | Generated installed-load-order taxonomy and authored-reference catalogue; native Start/Stop Orders; pickaxe, legality, visibility, linked-furniture and capacity gates; actor-owned bark; temporary travel; wall/floor/table pickaxe graph sequence; real strike-event-gated Lydia-only reward; per-reference depletion, HUD and return | Taxonomy/action shape is companion-neutral once tool, resource and voice policies are rebound | One developer-proven iron strike with Lydia `+1`, player `+0`, depletion `3 -> 2`; missing-tool, capacity and manual-interruption boundaries | Lydia did not occupy the stock mining furniture through tested activation routes; the implementation uses its installed Bethesda graph sequence. Continuous `3 -> 0`, broad exact-hash regressions, mod-added ore compatibility and owner visual acceptance remain unqualified |
| Lawful corpse looting | Natural same-cell dead-actor discovery, legality/visibility gates, navigable marker paired to an independently revalidated corpse, 66.74-unit Lydia movement, targeted native `IdleSearchBody`, conservative item policy, paired corpse/Lydia deltas, itemised HUD, return and continuous exhaustion | Corpse adapter is actor-neutral once companion identity, voice and category policy are rebound | Regression-qualified manual corpse order; exact `-3/+3` transfer; capacity-full and mid-search invalidation mutate nothing | Owner acceptance, corpse-specific player cancellation, equipment-upgrade policy and cross-cell/multi-companion corpse work remain unqualified; container looting is separate |
| Lawful world-container looting | Same-cell loaded/visible container discovery rejects locked, owned, criminal, off-limits and blocked targets; actor-owned bark, collision-aware stand-off travel, targeted native `IdleSearchingChest`, conservative capacity-safe paired deltas, itemised HUD, return and continuous exhaustion | Container adapter is actor-neutral once companion identity, voice and category policy are rebound | Regression-qualified manual container order; exact controlled `-3/+3` transfer; real locked boundary and mid-search invalidation mutate nothing | Owner acceptance, merchant/personal-storage semantics, explicit player cancellation, equipment upgrade policy and cross-cell/multi-trip storage remain unqualified |
| Combat positioning | Reuses the native temporary-package runner to obtain range and line of sight without teleporting | General basis for guard, regroup, retreat and formation moves | Move Lydia close enough for a supported action | No tactical cover, interception geometry, threat map, friendly-fire corridor, formation controller or navmesh-aware body-block verifier |
| Resource-backed healing | Lydia-owned healing-arrow spell/projectile, exact effect-hit event, health delta and one real potion consumed | Healer roles can select allowed spell/scroll/potion adapters | Emergency ranged player heal and honest no-resource fallback | One heal type is proven; no cure, revive, buff, cleanse, optimal medicine reservation, spell-magicka accounting or multi-target triage |
| Native combat fallback | No medicine selects guard behavior rather than pretending to heal | Role/trait policy can choose protect, retreat, warn or fetch help | Honest deterministic fallback | “Guard” is not yet a proven intelligent interception/positioning system |
| Actor-owned voice | 44 dedicated branch-bound INFO/FUZ lines dispatch through Lydia's `ObjectReference.Say`; action families, corpse/container intent and idle speech use the shared lease; repeated families rotate without an immediate repeat | Branch-bound generated topics/assets and the shared speech lease are reusable per compatible voice type | Audible deterministic intent/result/command responses plus safe idle chatter, grounded callout speech and corpse/container-search intent | Owner cadence/wording acceptance and runtime TTS/lip/cache/provider failure behavior remain later extensions |
| Optional shortcuts | Live gameplay control-map audit plus Skyrim input event sink; `Ctrl+Shift+O/K/Y/N` route to native Orders, Config, Yes and No selection | Binding/audit/debounce layer is actor-neutral | Faster access without a parallel executor or reserved function keys | Keyboard only; no remapping UI or gamepad binding yet; a mapped key disables its chord |
| Receipts and terminals | Append-only phase receipts cover dialogue, policy, path, arrival, activation, cast, effect, inventory/health delta, cleanup and rejection | Common envelope can close any typed companion action | Machine-verifiable success/failure and regression tests | Current stream is local evidence; it is not yet projected into Fork's durable companion command/receipt tables |
| Failure recovery | One active action, timeouts, fresh handle/context checks, truthful rejection, alias/package cleanup and ordinary follower restoration | Reusable action state machine | No false completion after changed target, range, speech, menu or resource state | Cross-cell recovery, game save during action, competing mods/packages and multi-companion contention need explicit tests |
| Runtime qualification | Exact DLL/ESP/SEQ binding, one exact save load, isolated non-input desktop, zero SKSE errors and zero leftover processes | Harness pattern applies to every companion feature | Fast repeatable developer proof without stealing the desktop | Receipt proof is not a substitute for the owner's experiential check; each new animation/action needs appropriate visual proof |

## Fork systems available to integrate

The repository already contains a much larger “brain” than the current local
Lydia controller. These are not Lydia capabilities until the Skyrim bridge
projects and reconciles them.

| Fork system | Existing reusable value | Companion capability after integration | Missing Skyrim-side work |
|---|---|---|---|
| Unified profile/state | Revisioned traits, role, beliefs, needs, affect and causal changes | Individual willingness, risk tolerance, priorities, tone, relationship and evolving condition | Bind Skyrim actor identity to canonical profile revision; project relevant state into the local reflex budget |
| Normalized perception | Ordered observations with provenance, confidence and observer locality | Lydia can remember what she actually saw/heard rather than global game truth | Map Skyrim events and sensory gates into normalized observations |
| Epistemic memory and recall | Episodes, typed claims, evidence relations, contradiction handling and bounded recall | Long-term shared adventures, promises, player habits, discovered places and justified uncertainty | Commit Lydia observations/action outcomes; request compact context at dialogue/decision boundaries |
| Goal/intention arbitration | Explainable scored goals, commitment hysteresis and revision-bound dispatch | Trait/role-derived initiative, refusal and task switching beyond fixed thresholds | Add companion goals/actions to the allow-list and translate an accepted intention into a native adapter |
| Cognition leases | Bounded provider-neutral LLM context, proposal validation, timeout and deterministic fallback | Natural tactics discussion and key-moment reasoning without leaving an LLM resident in Lydia | Build Lydia context projection, action schema, validation and native receipt reconciliation |
| Reliable command bridge | Ordered idempotent commands, expiry, applied/obsolete/dead-letter terminals and restart reconciliation | Durable remote companion plans that survive worker/Fork/game interruptions safely | Connect the Skyrim plug-in to the protocol and save/load epoch identity |
| Provider-neutral voice | Licensed provider/cache jobs, priorities, cancellation and durable playback receipts | Dynamic voiced lines using Lydia's allowed voice with cached fallback | Generate Skyrim-compatible WAV/FUZ/lip assets within latency budget and dispatch actor-owned topics |
| Social/economy systems | Relationships, knowledge flow, commerce, ownership and causal world state | Discuss rumours, evaluate loot utility, shop, deliver, negotiate and act on shared plans | Skyrim-specific physical adapters and authoritative inventory/gold/ownership reconciliation |

## Feature-category forecast

### Regression-qualified mechanisms (combined owner acceptance deferred)

- Notice a post-load burden threshold crossing, ask through native Yes/No and
  carry safe ordinary items after consent.
- Notice low health during combat, move into a clear supported position, fire a
  visible healing arrow, consume a real healing resource and announce it.
- After the configured idle period, detect a lawful nearby target and walk into
  arrival range, use a natural crouch, harvest one exact ingredient, show the
  native HUD result and return. This physical gather is owner accepted.
- Discuss and persist the four existing tactics through normal Lydia dialogue.
- Use the voiced native Orders root to run continuous gather, stop gathering,
  request immediate pack help or stop all orders.
- Open Orders/Config or answer a pending pack offer with conflict-checked,
  debounced chords that select the same native topics.
- Dispatch actor-owned branch-bound lines and emit machine-readable receipts;
  the owner has confirmed gathering audio.
- Rotate gather, pickup, heal, guard and idle speech across three variants
  without an immediate repeat, with idle chatter suppressed during combat,
  dialogue, action, pending offers and other speech.
- Name a grounded lawful nearby resource, ask through native Yes/No, and on
  acceptance physically gather it through the exact movement/delta/HUD/return
  path; `Ctrl+Shift+Y/N` selects the same response topics.

### Developer-proven mechanism awaiting regression qualification

- Start Mining from ordinary Orders, require Lydia to carry a pickaxe, select
  a lawful visible authored vein with linked furniture, speak and walk to it,
  run the furniture-family Bethesda pickaxe graph sequence, grant only after
  the real strike event, credit Lydia rather than the player, decrement durable
  per-reference capacity, report the item and return. The first strike passed;
  continuous repetition has been observed but the checker and broad suite have
  not yet qualified the current build.

### Extension required, using the current base

| Feature family | What can be reused | Required addition before implementation |
|---|---|---|
| Filtered chest/container looting | **Proven now:** lawful target/content filter, native Orders, collision-aware movement, targeted chest search, paired deltas, HUD, return, locked and interruption boundaries | Owner acceptance, richer storage semantics, category/upgrade policy, explicit player cancellation and cross-cell/multi-trip routes |
| Herb, ore and material jobs | **Herbs proven; ore developer-proven:** generated taxonomy/reference catalogue, tool/legality/capacity gates, travel, furniture-family pickaxe graph event, Lydia-only resource/depletion verifier and Start/Stop Orders | qualify continuous `3 -> 0`, broad current-hash regressions and owner presentation; add modded-resource compatibility and category policy for expansion |
| Cart or home-storage logistics | transfer filter, travel runner, receipts | owned storage identity, cross-cell/multi-trip routing, capacity ledger and hand-off recovery |
| Formation, guarding and interception | combat observation, travel/positioning primitive | threat selection, geometry/LOS scoring, safe position verifier, interruption priorities and anti-oscillation |
| Equipment/loadout assistance | inventory inspection and native dialogue | item comparison, slot/build policy, explicit consent, equip/unequip verifier and quest/unique-item protection |
| Scouting and point-of-interest reports | bounded movement, perception/receipts | destination planner, cross-cell travel, sensory observation schema, return/report behavior and memory commit |
| Survival/camp tasks | profiles, needs, movement, furniture/actions | need projection, campsite ownership, fire/cook/sleep packages, supply accounting and schedule coordination |
| Relationship-driven autonomy | profile, policy and Fork goal systems | Skyrim/Fork profile projection plus allow-listed proposal/action mapping and refusal explanations |

### Requires the Fork integration, not merely another Skyrim action

- long-term episodic and semantic memory of the player and shared adventures;
- belief revision, rumours, promises, trust and non-omniscient knowledge;
- freeform tactics discussion that compiles into typed, confirmable policy;
- LLM step-in for novel situations with bounded context and deterministic
  fallback;
- multi-companion coordination, durable plans, role allocation and recovery;
- utility decisions based on economy, ownership, future needs and world state.

### Unsupported as stated without a new architecture

- unrestricted natural-language execution of arbitrary game actions;
- claiming that a bark, animation call or path request means success without a
  terminal physical delta;
- invisible remote looting or teleport-based errands when the feature promises
  embodied behavior;
- persistent whole-world perception from Lydia while she is unloaded;
- arbitrary dynamic voiced conversation with no synthesis/cache/lip/rights and
  outage contract;
- safe cross-cell or strategic travel using only the current same-cell gather
  package.

## New-feature admission checklist

Before a proposed companion feature can enter implementation, answer every row.
A missing answer determines the required research or adapter instead of being
discovered during player testing.

1. **Grounded trigger:** Which engine/Fork observation proves the condition?
2. **Identity and authority:** Which companion may act, for whom, and under
   which role, relationship, consent and policy revision?
3. **Decision:** Is this a local reflex, deterministic plan or bounded LLM
   proposal? What outranks or interrupts it?
4. **Target and permissions:** How are target identity, ownership, crime,
   quest protection and changed context revalidated?
5. **Embodiment:** Which real Skyrim package, navigation route, animation,
   magic or interaction performs it?
6. **Immersion:** Can the player see/hear the promised action, and is the chosen
   animation semantically credible rather than merely convenient?
7. **Accounting:** Which inventory, health, magicka, gold, capacity, durability
   or world resources are consumed or transferred?
8. **Terminal verifier:** What exact engine event and state delta distinguish
   success, failure, cancellation and partial progress?
9. **Interruption/recovery:** What happens on combat, dialogue, save/load,
   target loss, blocked path, mod conflict, timeout or process restart?
10. **Speech:** Which phase authorizes each line, who owns playback, and what is
    the silence/fallback path?
11. **Memory and Fork:** Which observations, decisions and outcomes become
    durable knowledge, and how are commands/receipts reconciled?
12. **Proof:** Which automated production path, failure regression, visual or
    audible evidence and short owner test will establish acceptance?

## Generalization contract

Lydia-specific data is her form identity, Housecarl role/traits, voice assets,
current relationship/follower context and authored dialogue. The reusable
companion core is the observer, policy/action arbitration, native-dialogue
bridge, safe inventory rules, temporary-package action runner, animation/magic
adapters, actor-owned speech lease, terminal receipts and recovery state
machine.

A new companion therefore needs a profile and content binding plus only the
action adapters their role allows. Healers, porters, scouts, guards, workers
and animal companions can share the core, but must not inherit actions,
dialogue, animations or perception their actor type cannot physically support.

## See Also

- [Frontier Companion System](frontier-companion-system.md)
- [Skyrim Capability Baseline](capabilities.md)
- [Unified NPC Profile and State](../architecture/unified-npc-profile-and-state.md)
- [Epistemic Memory and Bounded Recall](../architecture/epistemic-memory-and-bounded-recall.md)
- [Explainable Goal and Intention Arbitration](../architecture/explainable-goal-and-intention-arbitration.md)
- [Bounded Key-NPC Cognition Leases](../architecture/bounded-key-npc-cognition-leases.md)
- [Provider-Neutral Spatial NPC Speech](../architecture/provider-neutral-spatial-speech.md)
- [Reliable Bridge Protocol](../architecture/reliable-bridge-protocol.md)
