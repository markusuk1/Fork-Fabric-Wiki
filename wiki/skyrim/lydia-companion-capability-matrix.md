# Lydia and Reusable Companion Capability Matrix

> **Current status correction:** the project-owned Lydia runtime has been
> withdrawn and removed from source, generated output, direct Skyrim and Mod
> Organizer. There is no installed custom companion capability, no Lydia Fork
> adapter and no LLM integration. This article now preserves historical
> mechanisms, owner observations, failures and candidate integrations only.
> Any phrase below such as “proven now,” “current implementation” or “installed
> DLL” refers to the removed experiment and is not a present capability.

> Sources: COMPANION-001 source/configuration, exact final Skyrim runs
> `companion-final-baseline-full-02`, `companion-final-orders-01`,
> `companion-final-shortcuts-01`, `companion-final-burden-boundaries-01`,
> `companion-chatter-callout-native-08`, `companion-corpse-natural-discovery-01`,
> `companion-container-qualification-06`, developer ore run
> `companion-ore-qualified-18`, continuous observation
> `companion-ore-final-19`, and the qualified slices' exact-hash regressions, and
> the repository's released Fork systems, collected 2026-08-12
> Raw: [ore-mining development boundary](../../raw/skyrim/2026-08-12-companion-ore-mining-development-boundary.md); [container-looting qualification](../../raw/skyrim/2026-08-12-companion-container-looting-qualification.md); [corpse-looting qualification](../../raw/skyrim/2026-08-12-companion-corpse-looting-qualification.md); [chatter/callout qualification](../../raw/skyrim/2026-08-12-companion-chatter-callout-qualification.md); [final Orders/shortcut qualification](../../raw/skyrim/2026-08-12-companion-orders-shortcuts-qualification.md); [burden repair](../../raw/skyrim/2026-08-12-companion-burden-repair-evidence.md); [owner programme](../../raw/skyrim/2026-08-12-companion-orders-burden-llm-owner-requirements.md); [owner gather pass/voice failure](../../raw/skyrim/2026-08-12-companion-owner-gather-pass-voice-failure.md); [Say branch research](../../raw/skyrim/2026-08-12-companion-say-branch-binding-research.md); [Say branch repair evidence](../../raw/skyrim/2026-08-12-companion-say-branch-repair-evidence.md); [critical dependency closure](../../raw/skyrim/2026-08-11-companion-critical-dependency-closure.md)
> Loot correction: [semantic loot policy and UI requirements](../../raw/skyrim/2026-08-12-companion-loot-policy-and-ui-requirements.md)
> Focus correction: [focused-activity suspension requirements](../../raw/skyrim/2026-08-12-companion-focused-activity-suspension-requirements.md)
> Owner acceptance: [combat-heal acceptance](../../raw/skyrim/2026-08-12-companion-owner-combat-heal-acceptance.md)
> Failure corrections: [persistent Orders and natural observation](../../raw/skyrim/2026-08-12-companion-persistent-orders-observation-correction.md); [owner corpse-continuation failure](../../raw/skyrim/2026-08-12-companion-owner-corpse-continuation-failure.md)
> Event architecture: [reusable activity-window and candidate-ledger design](../../raw/skyrim/2026-08-12-event-driven-reusable-activity-ledger-design.md)
> Final event proof: [event-ledger production correction](../../raw/skyrim/2026-08-12-event-ledger-production-correction.md)
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

The withdrawn implementation attempted to replace snapshot polling as the primary work
recorder with the smallest shared event-driven boundary: a grace-safe activity
window, deduplicated bounded handle ledger, persistent work schedule and typed
reconciliation. Corpse and container adapters share that core while retaining
their different awareness, legality, animation and exact mutation checks.
Combat/death/load events stage facts cheaply; validation remains on the safe
game thread. The exact installed DLL `515D5B8B...B5BE` passes native load, the
complete dialogue/burden/gather/heal regression, three-corpse direct chaining
and three-container direct chaining. The focused routes prove one initial bark,
exact transfers, armed waiting, one route-exhaustion regroup and movement-
triggered resume. Real attributed Skyrim death events admit corpse candidates
once and a fresh death resumes the still-armed order. The same hash also passes the complete native Orders surface
and continuous native ore furniture, animation-backed yield, tool, capacity and
interruption gates. Owner ordinary-route acceptance remains, so persistent
chaining is developer-proven work in progress, not owner accepted.

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

Ore mining is **regression qualified**, including ordinary Orders selection,
speech, movement, native furniture occupancy, the pickaxe animation event,
Lydia-only inventory, continuous depletion, HUD and missing-tool/capacity/
interruption boundaries. Owner visual acceptance remains separate.

Corpse-looting continuation is **ready for owner testing**. The repaired
production route admits three real attributed death events once, ignores 40
unrelated living actors, walks/searches/transfers across all three bodies with
one bark and no intermediate return, regroups once, remains armed and resumes
on a fresh death. Capacity and target-invalidation boundaries mutate nothing.
The original owner failure remains historical evidence; ordinary moving-world
acceptance is the remaining gate.

The owner's follow-up established that the second corpse held accepted gold and
arrows and became lootable after the player moved closer and reissued the
Order. All resource Orders therefore require a corrected persistent-mode
contract: empty scans wait rather than disarm, and new targets encountered
while travelling are processed without menu re-entry. The shared 900-unit
radius is also withdrawn. Initial admission defaults separate loaded/LOS
awareness (4096 corpse/ore, 3072 container, 1536 plant/loose item), maximum
local excursion (4096), physical interaction (140) and follower return (300).
These values are implemented and regression qualified; ordinary owner
observation of the moving route remains.

Returning to the player after every target is also withdrawn. Persistent work
must chain directly from corpse to corpse, container to container or deposit to
deposit using a bounded deterministic route inside a moving player-centred
leash. Target choice favours short Lydia travel while penalising backtracking
and player separation. Lydia regroups once when the local route ends or a
safety/interruption boundary requires it, then waits with the Order still
active.

Loose-world item selection is independently **failed**. Live receipts prove an
accepted native callout named `Bucket`, Lydia collected that exact target, then
continuous gathering collected a second bucket. The source admits all loose
`Misc` references and treats low weight/high value as useful; corpse/container
selection separately admits any `Misc` worth at least 10. Because Skyrim's
`Misc` includes both crafting resources and clutter, all such generic rules are
withdrawn. The replacement contract uses semantic categories, per-base-item
`Always`/`Default`/`Never`, category `Auto`/`Ask`/`Ignore`, need/role/scarcity/
value/route priority and explicit `Lydia`/`Player`/`Cargo` destination.

Player-focus arbitration is also **failed**. Lydia interrupted an active
lockpicking session with a junk callout. The existing menu event sink tracks
only dialogue for the callout/chatter branches, while a later modal-menu value
protects only part of the loop. The replacement is one cross-cutting suspension
lease: any focused activity freezes scanning, decisions, new/chained targets,
movement orders, speech and prompts while preserving active Order state. After
the final focused menu closes, a three-second grace and fresh rescan resume the
order; stale candidates are never replayed.

## Mechanism matrix

| Layer | Current Lydia mechanism and proof | Reusable beyond Lydia | Enables now | Gap or limiting boundary |
|---|---|---|---|---|
| Identity and role | Data-driven Lydia profile binds form `666772`, `housecarl`, traits and thresholds | Replace the actor identity, role, traits, voice and permissions in a companion profile | Per-companion policy and role defaults | Profile loading exists locally; it is not yet synchronized with Fork's canonical revisioned NPC profile/state |
| Observation | One-second grounded samples plus event-ledger updates for combat, attributed deaths and loaded containers; target-specific loaded/LOS awareness is 4096 corpse/ore, 3072 container and 1536 plant/loose, with bounded reconciliation | Same sampler/window/typed-ledger core can bind another follower and other observation classes | Burden assistance, emergency care, persistent target eligibility and responsive new-candidate admission | **Focused-activity suspension is unimplemented:** lockpicking can currently be interrupted by callouts. No semantic hearing, appearance, crime-witness, weather or cross-cell sensing yet |
| Deterministic arbitration | Priority is emergency care, burden edge, then idle gather; typed thresholds, cooldowns and one active action | Policy engine is actor-neutral | Safe autonomous reflexes with predictable behavior | Current rules are compact and local; traits do not yet calculate volunteering, refusal, courage, utility or dialogue style |
| Native conversation and Orders | Ordinary Lydia dialogue with no forced project INFO; voiced top-level Config and Orders roots; vanilla topic selections; TIF/Papyrus actions; persistent Start/Stop state and native Yes/No | Topic/branch templates and typed commands can be generated per voice/actor | Discoverable tactics, persistent gathering/corpse/container/mining orders, stop commands, manual pack help, stop all and consent-gated transfer | COMPANION-008 still needs ordinary moving-route owner acceptance. Finite choices only; no free-text intent parser or quantities |
| Policy persistence | Four typed toggles (`burden_help`, `combat_care`, `resource_gathering`, `loose_loot`) use replace-safe local persistence | Schema can be expanded and projected per companion | Existing settings survive reload/restart | Semantic category rules, per-base-item overrides, plugin remapping, destination and save-lineage/Fork reconciliation are unimplemented |
| Loot policy and UI | Vanilla inventory capture proves the desired category/footer affordance; pinned CommonLib exposes selected `ItemList`, `BottomBar` and SKSE inventory Scaleform callbacks; SkyUI/I4/PrismaUI are researched presentation candidates | One policy engine and UI can serve companions, porters, carts and storage | Requirements and integration routes are grounded | **Unimplemented:** `Always`/`Default`/`Never`, category `Auto`/`Ask`/`Ignore`, priority/destination, Loot Policy screen and contextual inventory action. SkyUI/I4 are not installed and no exact-runtime UI spike has passed |
| Safe inventory transfer | Consent plus safe stand-off, instance safety, target burden ratio, capacity reserve, bounded itemised HUD summary, and explicit full/no-safe/decline/timeout/cooldown terminals | Generic transfer mechanics can serve porters, carts and storage workers | Autonomous or manually ordered carry assistance for ordinary eligible items | Eligibility is withdrawn where it depends on generic `Misc`/value rules; no category/quantity chooser, destination handoff, equipped loadout, barter or multi-trip logistics |
| Target discovery | Capped same-cell enumeration stages handles and revalidates outside locks; ownership/crime/range/loaded-3D/visibility gates; callout/corpse/container/ore adapters exist | Generic candidate pipeline for loose loot, activators, corpses, containers and authored ore | Find lawful natural flora and supported authored ore; identify candidate corpses/containers | Loose `Misc` classification is failed by the two-bucket owner path; corpse/container content policy using `Misc >= 10` is withdrawn. Inaccessible interiors, merchant storage and mod-added semantic classification remain unqualified |
| Embodied locomotion | Dynamic quest aliases plus purpose-built native temporary Travel package; direct verified target-to-target chaining inside a moving player leash; one final regroup | Core action runner for fetch, inspect, deliver, guard-post, follow-up and local errands | Visible local routes across multiple loaded targets without wasteful per-target returns | Ordinary moving-route owner acceptance remains; cross-cell doors, unreachable navmesh and long journeys remain separate |
| Animation and interaction | Owner-observed flora travel/crouch; regression-qualified corpse/container travel and targeted native searches; regression-qualified ore travel/furniture/pickaxe event gated on Lydia's real `AddToInventory` event | Reuse the action-state-machine shape with an action-specific native animation and exact terminal | Owner-accepted natural-herb gathering plus exact-build corpse/container/continuous-ore embodiment | Owner ore and persistent-route inspection, cross-cell routes and feature-specific player cancellation remain open |
| Lawful ore mining | Generated installed-load-order taxonomy and authored-reference catalogue; native Start/Stop Orders; pickaxe, legality, visibility, linked-furniture and capacity gates; actor-owned bark; travel; native furniture occupancy and pickaxe animation event; Lydia-only reward and per-reference depletion | Taxonomy/action shape is companion-neutral once tool, resource and voice policies are rebound | Continuous exact-build mining plus missing-tool, capacity and interruption boundaries | Mod-added ore compatibility and owner visual acceptance remain unqualified |
| Lawful corpse looting | Combat/death event-ledger admission plus bounded typed reconciliation, legality/visibility gates, moving-leash scoring, targeted native `IdleSearchBody`, paired deltas, itemised HUD, direct chaining and one regroup | Activity/ledger/scheduler and corpse adapter are actor-neutral once companion identity, voice and semantic policy are rebound | Regression-qualified three-corpse event route, dense 40-actor non-starvation, fresh-death resume and no-mutation boundaries | Ordinary-route owner acceptance remains; generic `Misc >= 10` content policy is still withdrawn pending shared semantic category/override/priority evaluation |
| Lawful world-container looting | Same-cell loaded/visible container discovery rejects locked, owned, criminal, off-limits and blocked targets; actor-owned bark, travel, `IdleSearchingChest`, paired deltas and HUD | Container adapter is actor-neutral once companion identity, voice and semantic policy are rebound | Physical mechanics and locked/invalidation boundaries remain regression evidence | Player readiness withdrawn for content selection because it shares generic `Misc >= 10`; also lacks owner acceptance, merchant/storage semantics and cross-cell logistics |
| Combat positioning | Reuses the native temporary-package runner to obtain range and line of sight without teleporting | General basis for guard, regroup, retreat and formation moves | Move Lydia close enough for a supported action | No tactical cover, interception geometry, threat map, friendly-fire corridor, formation controller or navmesh-aware body-block verifier |
| Resource-backed healing | **Owner accepted:** Lydia-owned healing-arrow spell/projectile, actor-owned intent, exact effect-hit event, player health `36.91 -> 64.86` and one real potion `1 -> 0` in ordinary combat | Healer roles can select allowed spell/scroll/potion adapters | Emergency ranged player heal and honest no-resource fallback | One heal type is proven; no cure, revive, buff, cleanse, optimal medicine reservation, spell-magicka accounting or multi-target triage |
| Native combat fallback | No medicine selects guard behavior rather than pretending to heal | Role/trait policy can choose protect, retreat, warn or fetch help | Honest deterministic fallback | “Guard” is not yet a proven intelligent interception/positioning system |
| Actor-owned voice | 44 dedicated branch-bound INFO/FUZ lines dispatch through Lydia's `ObjectReference.Say`; action families, corpse/container intent and idle speech use the shared lease; repeated families rotate without an immediate repeat | Branch-bound generated topics/assets and the shared speech lease are reusable per compatible voice type | Audible deterministic intent/result/command responses plus safe idle chatter, grounded callout speech and corpse/container-search intent | Owner cadence/wording acceptance and runtime TTS/lip/cache/provider failure behavior remain later extensions |
| Optional shortcuts | Live gameplay control-map audit plus Skyrim input event sink; `Ctrl+Shift+O/K/Y/N` route to native Orders, Config, Yes and No selection | Binding/audit/debounce layer is actor-neutral | Faster access without a parallel executor or reserved function keys | Keyboard only; no remapping UI or gamepad binding yet; a mapped key disables its chord |
| Receipts and terminals | Append-only phase receipts cover dialogue, policy, path, arrival, activation, cast, effect, inventory/health delta, cleanup and rejection | Common envelope can close any typed companion action | Machine-verifiable success/failure and regression tests | Current stream is local evidence; it is not yet projected into Fork's durable companion command/receipt tables |
| Failure recovery | One active action, timeouts, fresh handle/context checks, truthful rejection, alias/package cleanup and ordinary follower restoration | Reusable action state machine | No false completion after changed target, range, speech or resource state on qualified paths | Native menu lifecycle is not applied consistently. Add a nested focused-activity suspension lease that preserves Orders, cancels at safe boundaries, suppresses all initiative, then resumes after a three-second grace and fresh revalidation. Cross-cell recovery, game save during action, competing mods/packages and multi-companion contention also need explicit tests |
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

### Qualified mechanisms (combined feature acceptance remains withdrawn)

- Notice a post-load burden threshold crossing, ask through native Yes/No and
  carry safe ordinary items after consent.
- Notice low health during combat, move into a clear supported position, fire a
  visible healing arrow, consume a real healing resource and announce it. This
  complete combat-heal path is owner accepted.
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
9. **Interruption/recovery:** What happens on combat, focused player activity,
   dialogue, save/load, target loss, blocked path, mod conflict, timeout or
   process restart? A focused activity must preserve the Order while suspending
   all companion automation and resume only after grace plus fresh revalidation.
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
