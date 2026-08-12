# Frontier Companion System

> **Withdrawn implementation:** the custom Lydia SKSE/dialogue runtime described
> in this article has been removed. It is not installed, released or ready for
> player testing. It never implemented a Fork adapter or an LLM integration.
> The article is retained as historical requirements, prior-art research,
> observed partial behaviours and failure evidence. Its former ownership table
> and “implemented” sections describe intent/history, not current capability.

> Sources: owner requirements, COMPANION-001 implementation, COMPANION-002 crash correction and primary prior-art repositories, collected 2026-08-11
> Raw: [frontier requirements](../../raw/skyrim/2026-08-11-frontier-companion-requirements.md); [prior-art decision](../../raw/skyrim/2026-08-11-companion-prior-art.md); [idle-gather investigation](../../raw/skyrim/2026-08-11-companion-idle-gather-crash.md); [idle-gather correction proof](../../raw/skyrim/2026-08-11-companion-idle-gather-correction-proof.md); [F10 manual crash correction](../../raw/skyrim/2026-08-11-companion-f10-manual-crash.md); [interaction replacement proof](../../raw/skyrim/2026-08-11-companion-interaction-replacement-proof.md); [foreground lockout correction](../../raw/skyrim/2026-08-11-skyrim-foreground-lockout-correction.md); [native handoff correction](../../raw/skyrim/2026-08-11-companion-native-handoff-correction.md); [exact-hash qualification](../../raw/skyrim/2026-08-11-companion-exact-hash-qualification.md); [player-path gap research](../../raw/skyrim/2026-08-11-companion-player-path-gap-research.md); [production-path re-entry research](../../raw/skyrim/2026-08-11-companion-production-path-reentry-research.md); [discovery static preflight](../../raw/skyrim/2026-08-11-companion-discovery-static-preflight.md); [critical dependency closure](../../raw/skyrim/2026-08-11-companion-critical-dependency-closure.md)
> Correction raw: [container-looting qualification](../../raw/skyrim/2026-08-12-companion-container-looting-qualification.md); [corpse-looting qualification](../../raw/skyrim/2026-08-12-companion-corpse-looting-qualification.md); [event-ledger production correction](../../raw/skyrim/2026-08-12-event-ledger-production-correction.md); [chatter/callout qualification](../../raw/skyrim/2026-08-12-companion-chatter-callout-qualification.md); [final Orders/shortcut qualification](../../raw/skyrim/2026-08-12-companion-orders-shortcuts-qualification.md); [burden repair evidence](../../raw/skyrim/2026-08-12-companion-burden-repair-evidence.md); [owner programme](../../raw/skyrim/2026-08-12-companion-orders-burden-llm-owner-requirements.md); [owner gather pass/voice failure](../../raw/skyrim/2026-08-12-companion-owner-gather-pass-voice-failure.md); [Say branch repair evidence](../../raw/skyrim/2026-08-12-companion-say-branch-repair-evidence.md); [focused-activity suspension requirements](../../raw/skyrim/2026-08-12-companion-focused-activity-suspension-requirements.md); [owner combat-heal acceptance](../../raw/skyrim/2026-08-12-companion-owner-combat-heal-acceptance.md)
> Commit: 3098f74
> Updated: 2026-08-12
> Rescue correction: [package-safe Lydia save rescue](../../raw/skyrim/2026-08-12-lydia-save-rescue-package-correction.md)
> Rescue acceptance: [owner-observed follower restoration](../../raw/skyrim/2026-08-12-lydia-save-rescue-owner-acceptance.md)

## Historical purpose

The companion system turns Skyrim followers into grounded, configurable
assistants while retaining Skyrim's physical execution and Fork's durable
authority. Lydia is the first configured profile, not a special-case design.
The same contracts are intended for healers, guards, porters, scouts, workers
and other companion roles.

The first slice deliberately prioritizes truthful actions over broad dialogue.
It observes the real player and companion, chooses from typed policy, announces
an intended action, performs it through Skyrim, and writes an observed receipt.
An LLM can later translate natural discussion into the same policy or propose a
bounded plan; it cannot invent perception, healing resources or success.

## Ownership and control loop

| Layer | Owns |
|---|---|
| Fork | Companion identity/profile, typed policy, normalized observations, memory, plan authority, action identity and durable terminal receipts |
| Native companion plug-in | Low-latency sampling, safety checks, cooldowns, Skyrim-thread execution, local degraded-mode policy and receipt emission |
| Skyrim | Player/follower state, combat, inventory, capacity, navigation, activation, harvestables, animation, subtitles and save lifecycle |
| Voice provider | Licensed authored or synthesized audio only; never action authority |

The intended loop is `observe -> normalize -> arbitrate -> announce -> execute ->
verify -> receipt -> reconcile`. Combat reflexes remain local and deterministic;
longer planning, memory and optional LLM work remain asynchronous.

The emergency ranged combat heal is **owner accepted**. In ordinary combat
Lydia spoke, wound up and released the healing arrow from 570.30 units; the
correlated terminal records a hit, player health `36.91 -> 64.86` and Lydia's
valid healing potion `1 -> 0`. This acceptance is limited to that one grounded
heal type and does not advance the unrelated failed companion claims.

## Implemented COMPANION-001 components and proof boundary

The exact installed baseline is DLL
`515D5B8B1945E3A7931D4B17249E2F9BD4A37A370347806BEE338BD7E3F7B5BE`,
ESP `FFF409006E608DFD5F0A90A960072B509B16F90DC8C8CB49D5F72F34620C0583`
and SEQ `A0E1269E459333A5D0486163DDA2B66EC35EB9B902DC42FA9931EAEC025407F6`.
The full companion, native Orders, event-admitted three-corpse, container and
continuous-ore production routes pass on that exact hash. COMPANION-008 is
`ready_for_owner`; the parent COMPANION-001 remains failed at its independent
semantic loot-policy/UI and focused-activity suspension requirements.

Orders is a voiced top-level native branch parallel to Config. It exposes
continuous lawful gathering, Stop Gathering, immediate pack help, Stop All,
lawful continuous corpse looting/Stop Looting, lawful continuous world-
container looting/Stop Container Looting and a no-order exit. Optional
`Ctrl+Shift+O/K/Y/N` chords are enabled only when the
base key is unmapped in the live gameplay control map; they open or select the
same vanilla topics and cannot call an executor directly.

The speech layer now rotates three variants for gathering, pickup, healing,
guarding and idle chatter without immediate repetition. Idle speech is
suppressed by combat, dialogue, an embodied action, pending consent or another
speech lease. A resource callout names a loaded lawful target in the HUD,
speaks before opening native Yes/No, and commits an accepted target to the same
visible movement/animation/delta/HUD/return terminal. Generic-object visibility
uses loaded 3D and a Havok LOS-layer ray beyond the maintained 300-unit
close-collision floor; actor LOS is retained for actor-to-actor healing.

Owner evidence withdraws the current callout usefulness and loose-item content
policy. The live stream proves a named `Bucket` callout was accepted and the
continuous gather path then collected two bucket references. The scanner treats
all loose `Misc` as resource candidates, while corpse/container selection
accepts `Misc` by value. Skyrim's `Misc` is not a semantic resource category.
The required replacement is one shared semantic policy with category defaults,
stable per-base-item overrides, immediate-need/value priority, explicit item
destination and an in-game Loot Policy surface. See the
[loot-policy/UI requirements](../../raw/skyrim/2026-08-12-companion-loot-policy-and-ui-requirements.md).

An owner lockpicking session exposed a separate focus-arbitration failure:
Lydia initiated an unsolicited junk callout while the lockpicking menu owned the
player's attention. The current source tracks `DialogueMenu` for callout and
chatter but does not apply one complete focus check to the whole automation
loop. The required native-menu adaptation suspends all companion scanning,
decisions, new/chained target work, speech and prompts during focused activity,
while preserving durable Orders. Closing the last nested focused menu starts a
three-second grace, discards stale candidates, rescans the current area and
then resumes the preserved Order.

Corpse looting uses the same native Orders route, bounded natural same-cell
discovery and legality/visibility gates. The current implementation adds the
shared event-driven activity window, bounded typed candidate ledger and
persistent work schedule: attributed deaths enter once, empty scans wait,
new deaths resume an armed Order, nearby targets chain directly and Lydia
regroups once when the route exhausts. Lydia announces before movement, a
temporary navigable marker supplies the Travel package while the dead actor
remains the independently revalidated animation/inventory target, and native
`IdleSearchBody` supplies a credible three-second search. Only conservative
capacity-safe items move, every source decrement must equal Lydia's increment,
the HUD names bounded items/counts, and ordinary follower control is restored.
The exact final event route proved three attributed corpses against 40 unrelated
living actors, one route bark, three body searches, exact paired transfers,
armed waiting, a fresh-death resume, one regroup, capacity-full no-transfer and
target-invalidated no-transfer. Ordinary moving-route owner acceptance remains.

The container adapter adds loaded visible unlocked unowned non-criminal world
container discovery, a collision-aware approach stand-off, targeted native
`IdleSearchingChest`, conservative item policy, paired container/Lydia deltas,
itemised HUD, follower return and continuous exhaustion. Exact qualification
observed 196.44 units of Lydia movement and container `-3` / Lydia `+3`; a real
locked reference and mid-search invalidation both mutated nothing. Owner visual
acceptance remains deferred.

The CommonLibSSE-NG plug-in at
`src/skyrim/fork_fabric_companion` is pinned to Skyrim `1.6.1170.0`, SKSE64
`2.2.6` and the project's CommonLib baseline. The Lydia profile is data-driven
through `config/skyrim/companion-lydia.json`. The following components exist in
source. Their individual evidence status is recorded in the capability matrix;
the overall slice is not qualified while autonomous voice is failed:

- one-second bounded observation of player health, combat, inventory
  weight/capacity, movement/idle time, Lydia's capacity and nearby lawful
  harvest or loose-loot candidates;
- deterministic priority of emergency combat care over burden assistance and
  idle gathering, with separate cooldowns;
- native clickable follower dialogue that toggles burden help, emergency care,
  loose loot and idle resource gathering, persisted with replace-safe writes;
- near-capacity offers which move only ordinary eligible inventory after player
  consent, excluding equipped, favourited, quest, protected, enchanted and
  instance-bearing items while preserving Lydia's configured capacity reserve;
- medicine-only health consumables for close-range emergency care, consumed
  from Lydia's real inventory; if no valid resource exists she truthfully
  announces and requests native guard/combat behavior instead;
- bounded same-cell collection of lawful flora/tree activators or loose items,
  staging engine handles under the cell lock, resolving and validating them
  afterward, then using native pathing, arrival validation, a pickup animation,
  close-range activation, outcome verification and follower-package return;
- generated Skyrim INFO/FUZ actor-owned barks with matching subtitles, plus
  append-only observations and action receipts. The assets and dispatch exist,
  but audible owner playback has failed.

The intended production voice path is spatial and actor-owned through Skyrim's
`ObjectReference.Say`. It does not clone or redistribute Lydia's original
performer. `QSpeakingDone` is not accepted as audible proof. Unrestricted
runtime-generated conversation and its lip, latency, cache and provider-failure
contract remain later work.

MP3 is supported as voice-pipeline input, not as this deterministic player's
runtime format. The owner's ElevenLabs “Sending a heal your way” MP3 is decoded
and cached as mono PCM WAV before deployment; the other seven current barks use
the licensed Piper default. The source MP3 stays local. Later Skyrim-attached
spatial voice should emit Skyrim-compatible WAV/FUZ assets from the same
provider-neutral source.

## Safety and failure behavior

The plug-in never transfers equipped, favourited, quest, enchanted or
instance-bearing items. It never treats food, poison or an ordinary unavailable
potion as a player heal. It does not teleport Lydia or loot. Cross-cell actors,
open menus, combat-inappropriate gathering, owned targets, insufficient
capacity, changed targets and missing resources result in no action or an
explicit rejected receipt.

The ordinary owner playtest exposed an unsafe first implementation at the
idle-second-60 boundary: it globally walked attached cells, ran gameplay checks
under cell spin locks and retained a raw reference. COMPANION-002 replaced that
path with capped parent-cell enumeration and `ObjectRefHandle` staging. No
gameplay or ownership query runs inside the iterator callback, and every handle
is resolved and checked again outside the lock and before activation.

Policy writes use a temporary file followed by an operating-system replace and
write-through operation. A failed write rolls the menu toggle back and reports
failure rather than claiming persistence. Test-only mutation is guarded by an
unshipped flag, restores health and temporary inventory changes, quits without
saving and is cleaned by the harness.

Companion save rescue must release action ownership, not merely change actor
position. The project travel package is owned by a forced alias on the
dedicated companion action quest and can continue to outrank ordinary follower
behaviour after a teleport. The rescue helper therefore stops that quest,
ends the interrupted package, reevaluates Lydia, moves her at a safe stand-off,
waits for a non-project package, saves, reloads and requires the same released
package after reload. The corrected named save passes that structural gate;
the owner subsequently confirmed Lydia is unstuck and follows over ordinary
terrain. This accepts the rescue only, not the persistent work-order route.

## Evidence status

**Feature state: Failed at autonomous voice; physical natural gathering is
owner accepted.** Historical production run `companion-production-full-27` loaded the real Lydia save and
drove the installed Skyrim route. It opened Lydia's ordinary dialogue with no
forced project INFO, selected the visible top-level project entry and policy
through `DialogueMenu_mc.onSelectionClick -> TopicClicked -> TIF -> Papyrus`, observed
the policy round-trip, moved one safe item only after native Yes, walked Lydia
to a lawful loose target through a native temporary Travel package, played a
pickup idle, activated it, verified the inventory delta and restored ordinary
follower range. In real combat it positioned Lydia for line of sight without
teleporting, released a visible Lydia-owned healing arrow, observed the exact
effect and health delta, consumed one real potion and used actor-owned speech.

That run passed in `75,336 ms`, found zero SKSE error markers and left zero game
processes. Its DLL SHA-256 was
`955ADD080B8D3E7133EAB3339A7911AAF362A3E3B9EFA217D16402DD5D41935A`.

The subsequent owner run is authoritative for the experiential boundary:
Lydia moved to a Purple Mountain Flower, performed a natural crouch, gained the
exact ingredient `0 -> 1`, showed the HUD result and returned. The owner
accepted the roughly one-second animation. No gathering bark was audible.
Native comparison found the generated bark topics were missing the dialogue
branch used by sampled shipped Papyrus `Say` topics. The repaired current
dialogue ESP is
`BD18793CDBC7E318C9D27DB2776327F1B38AE917CB11BC7490128F546A008392`;
the two-entry SEQ was
`A0E1269E459333A5D0486163DDA2B66EC35EB9B902DC42FA9931EAEC025407F6`.

The lifecycle record is `failed` with only `CLAIM-VOICE` failed. The branch
repair is built, round-trip verified and deployed, but only an owner-audible
ordinary-save run can advance the voice claim. See the
[companion capability matrix](lydia-companion-capability-matrix.md) for reusable
mechanisms and feature-category limits.

### Superseded discovery and preflight record

The following chronology is retained to explain the rejected approaches and
why compilation, preflight or direct-executor calls are never capability proof.

**Historical state: Ready for implementation; superseded by the production run above.**
The owner-established failure remains preserved in immutable evidence: the
that the burden offer did not open a native Yes/No choice, activating Lydia
exposed no project configuration topics, gathering announced intent but ended
at path/arrival failure without activation, and healing had only spell/resource
preflight rather than release, exact effect, health gain and resource
consumption in one production run.

The generator now emits and verifies the missing metadata. Full qualification
run `companion-native-gate-06` rebuilt and deployed the DLL/ESP pair, started
SKSE on an isolated desktop, reached DataLoaded, loaded the real Lydia save,
recorded `world_loaded`, found zero SKSE error markers and left zero owned game
processes. Its installed DLL SHA-256 was
`475FD1D69DFDF9468F90B56A8B89C057514F464C9252E29349C656675812FE78`; its
dialogue ESP SHA-256 was
`666404BB9961FA506DD1EC8C9D413D089E42A5FDDCB779BA82A1291CF9E5E257`.

This remains useful startup and real-save compatibility evidence, not feature
proof. Automated burden checks invoked the transfer executor directly;
gathering preflight found a candidate without executing the complete action;
healing preflight found a resource/effect without establishing release and
impact. Those shortcuts cannot support a capability claim.

`docs/features/COMPANION-001.feature.json` governs re-entry. The verified
prior-art decision is to adapt Skyrim's native DialogueMenu, TIF/Papyrus,
pathing, activation, magic, inventory and FUZ voice systems. The former direct
self-test is explicitly invalid: vanilla `DialogueMenu.as` exposes a real
`onSelectionClick -> TopicClicked` route that automated proof can exercise
without synthetic input or direct action calls. Autonomous global
`PlaySoundW` barks are also unproven and must move to Lydia-owned native
voice/subtitle records.

The former discovery record treated dialogue quest startup, native topic
selection, embodied path/idle/activation/return, correlated healing outcomes,
actor-owned voice, failure recovery and production-route regression coverage
as critical unresolved dependencies.
All critical dependencies must be evidence-backed known or rejected—not merely
assigned—before implementation. Developer production proof and
regression qualification must pass before another owner test is requested.

Those dependencies are now evidence-backed `known`, with explicit validation
methods, so the lifecycle record passes `ready_for_implementation`. The
startup-data dependency was closed without automating xEdit's GUI: the
generator applies xEdit 4.1.5f's exact SEQ algorithm to the serialized
Start-Game-Enabled QUST and independently verifies fixed FormID `0x01000800`.
The generated ESP SHA-256 is
`666404BB9961FA506DD1EC8C9D413D089E42A5FDDCB779BA82A1291CF9E5E257`; the
four-byte SEQ SHA-256 is
`004BE5580012EFCDEC118FCE2444CF2DAB6A4709D71924066ED03B59A19969DE`.
Deployment and qualification must bind both hashes. This proves construction,
not live topic availability.

Implementation must drive the shipped vanilla
`DialogueMenu_mc.onSelectionClick -> TopicClicked -> TIF -> Papyrus` route,
persist selected policy across process restart, complete embodied
path/arrival/animation/activation/return, correlate heal release/effect/health
and resource consumption, and replace global playback with dedicated INFO/FUZ
topics spoken by Lydia through `ObjectReference.Say` under a non-overlapping
speech lease. These are implementation requirements, not current capabilities.

The current discovery build also passes warnings-as-errors DLL compilation,
policy unit tests, generated ESP verification, all 11 official Papyrus
assemblies and 15 native package-record checks. Those results are explicitly
static preflight and do not close any player-facing runtime dependency.

Unattended Skyrim proof is fail-closed and owner-safe. The harness uses an
isolated non-input Windows desktop and requires `-NonIntrusiveCapture`; the
handoff gate binds fresh lifecycle evidence to exact built/direct/MO2 DLL and
ESP hashes. Compilation or plugin round-trip parsing alone can never qualify a
native Skyrim handoff.

## Explicit next boundaries

- project the native observation and receipt stream into Fork's companion
  tables and reconcile policy revisions in both directions;
- replace the binary tactics surface with typed natural conversation while
  retaining deterministic confirmation and authorization;
- add runtime-generated spatial speech only behind a provider/cache/lip and
  failure contract; the deterministic actor-owned route is already proven;
- add mining furniture and ore-specific resource/animation/accounting proof
  with bounded return-to-follow behavior;
- add physical owned-container and horse/cart logistics, including walk,
  transfer, verification and return;
- generalize traits, relationship, role and memory into volunteering, refusal,
  risk and dialogue style without weakening mechanical validation.
