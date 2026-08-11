# Wiki Log

## [2026-08-11] ingest | COMPANION-001 frontier companion implementation @ working tree

- Added: [Frontier Companion System](skyrim/frontier-companion-system.md).
- Raw: `raw/skyrim/2026-08-11-frontier-companion-requirements.md` and
  `raw/skyrim/2026-08-11-companion-prior-art.md`.
- Implemented: data-driven Lydia selection, bounded ObservePlayer sampling,
  typed persistent tactics, safe burden transfer, real medicine-backed healing
  with truthful guard fallback, lawful close-range collection, licensed barks
  and append-only native evidence.
- Verified: warnings-as-errors native build, deterministic policy tests and
  `companion-live-18` against the real Lydia save: voice, potion-backed heal,
  physical pickup, three periodic observations, zero SKSE errors and cleanup.
- Voice: the owner's ElevenLabs heal MP3 is now decoded to an identical
  deployed mono PCM WAV in both runtime locations; the source remains local.
- Boundary: spatial WAV/FUZ, natural-language policy, deeper resource logistics
  and the production Fork projection adapter remain later work.

## [2026-08-11] ingest | SKYRIM-006 reversible suppression and final platform decision @ working tree

- Updated: [Skyrim SE/AE + SKSE Capability Baseline](skyrim/capabilities.md),
  [OpenMW versus Skyrim Platform Decision](architecture/platform-decision-openmw-vs-skyrim.md),
  [Fork, OpenMW, and Skyrim Capability Matrix](architecture/capability-matrix.md)
  and [Fork–OpenMW Integration Architecture](architecture/integration-architecture.md).
- Raw: `raw/skyrim/2026-08-11-modded-save-confirmation-prior-art.md` and
  `raw/skyrim/2026-08-11-skyrim-suppression-proof.md`.
- Proven: identical-save enabled/control pair; 163 actors disabled, 26
  narrative quests disabled/stopped, five system quests preserved, zero loaded
  actors enabled versus 31 actors/31 packages restored, two clean exits and
  byte-exact restoration.
- Decision: select Skyrim SE/AE+SKSE as the next build's physical executor,
  retain Fork authority and preserve OpenMW as the proven fallback/reference.
- Boundary: Whiterun is qualified; whole-world fail-closed expansion and the
  first Fork-owned Skyrim village remain production work.

## [2026-08-10] ingest | SKYRIM-005 loaded/off-screen scale @ working tree

- Updated: [Skyrim SE/AE + SKSE Capability Baseline](skyrim/capabilities.md),
  [OpenMW versus Skyrim Platform Decision](architecture/platform-decision-openmw-vs-skyrim.md),
  [Household Occupancy and Unloaded Actors](architecture/household-occupancy-and-unloaded-actors.md)
  and [Fork Capability Baseline](fork/capabilities.md).
- Raw: `raw/skyrim/2026-08-10-skyrim-scale-prior-art.md` and
  `raw/skyrim/2026-08-10-skyrim-scale-proof.md`.
- Proven: 29-33 simultaneous loaded humanoids, at least 29 native packages,
  22 progressing actors and 16.667 ms frame p95/p99 across 100 samples;
  separately, 200 Fork actors, 60 atomic ticks and 12,000 updates at 5.916 ms
  p95 with replay, stale/divergent and restart-fingerprint safety.
- Boundary: the counts are intentionally separate, current module scale is not
  an OpenMW production-schema upgrade, and reversible regional suppression
  remains SKYRIM-006.

## [2026-08-10] ingest | SKYRIM-004 recovery @ working tree

- Updated: [Skyrim SE/AE + SKSE Capability Baseline](skyrim/capabilities.md)
  and [OpenMW versus Skyrim Platform Decision](architecture/platform-decision-openmw-vs-skyrim.md)
- Raw: `raw/skyrim/2026-08-10-skyrim-recovery-proof.md`
- Proven: native named save/load, genuine older-save rollback, immutable old
  terminals, revision/session/load-epoch reconciliation, worker outage/restart,
  two game processes and fresh owned Fork restart with zero live SKSE errors.
- Boundary: 20 embodied plus 200 abstract actor scale and reversible target
  region suppression remain SKYRIM-005 and SKYRIM-006.

## [2026-08-10] ingest | SKYRIM-003 embodied day @ working tree

- Updated: [Skyrim SE/AE + SKSE Capability Baseline](skyrim/capabilities.md)
  and [OpenMW versus Skyrim Platform Decision](architecture/platform-decision-openmw-vs-skyrim.md)
- Raw: `raw/skyrim/2026-08-10-skyrim-embodied-day-proof.md`
- Proven: exact authored work/home/sleep/patrol/social package identities,
  three named actors crossing seven cells, furniture/sit-sleep observation,
  native interruption with authored-package recovery, invalid-destination
  retention and three non-foreground UWQHD captures without routine actor
  teleport or SKSE error markers.
- Boundary: save rollback/process recovery, 20+200 actor scale and reversible
  target-region suppression remain SKYRIM-004 through SKYRIM-006.

## [2026-08-10] ingest | SKYRIM-002 live humanoid bridge @ working tree

- Updated: [Skyrim SE/AE + SKSE Capability Baseline](skyrim/capabilities.md)
  and [OpenMW versus Skyrim Platform Decision](architecture/platform-decision-openmw-vs-skyrim.md)
- Raw: `raw/skyrim/2026-08-10-skyrim-live-humanoid-bridge-proof.md`
- Proven: same-cell Lydia observation, external durable command through the
  Skyrim task queue, visible draw/restore receipts, replay/conflict/expiry/
  absent-target/allowlist terminals and no duplicate physical effect after a
  second Skyrim launch, with zero SKSE error markers.
- Boundary: native multi-location packages, furniture occupancy, interruption
  and resumption remain SKYRIM-003.

## [2026-08-10] ingest | SKYRIM-001 live actor receipt @ working tree

- Updated: [Skyrim SE/AE + SKSE Capability Baseline](skyrim/capabilities.md)
  and [OpenMW versus Skyrim Platform Decision](architecture/platform-decision-openmw-vs-skyrim.md)
- Raw: `raw/skyrim/2026-08-10-skyrim-live-actor-receipt.md`
- Proven: current SKSE injection, save load, real player/cell observation,
  living high-process actor/package inspection, package-evaluation invocation
  and an applied terminal receipt with zero SKSE error markers.
- Boundary: animation was rejected and the action was not external-command
  routed; humanoid visible action remains the next S2 gate.

## [2026-08-10] correction | SKYRIM-001 Steam-first SKSE bootstrap @ working tree

- Updated: [Skyrim SE/AE + SKSE Capability Baseline](skyrim/capabilities.md)
- Raw: `raw/skyrim/2026-08-10-skse-steam-bootstrap-observation.md`
- Observed: starting SKSE before Steam produced a later surviving game process
  with no SKSE/Fork modules, an empty current SKSE log and no receipts despite
  the loader reporting a completed hook thread.
- Procedure: start and settle Steam first; accept a launch only when the
  surviving game has SKSE/Fork modules and a current non-empty SKSE log.

## [2026-08-10] correction | SKYRIM-001 Main Menu activation boundary @ working tree

- Updated: [Skyrim SE/AE + SKSE Capability Baseline](skyrim/capabilities.md)
- Raw: `raw/skyrim/2026-08-10-skyrim-runtime-bridge-feasibility-evidence.md`
- Observed: save-manager requests, queued and immediate process-local input,
  the SKSE UI task queue, Scaleform delegate calls and both registered native
  Continue callbacks failed to produce `post_load_game`.
- Boundary: one ordinary Continue/save-load operation remains necessary before
  live actor, visible routine, physical recovery and actor-scale claims can run.

## [2026-08-10] ingest | SKYRIM-001 isolation, recovery and transport scale @ working tree

- Updated: [Skyrim SE/AE + SKSE Capability Baseline](skyrim/capabilities.md)
  and [OpenMW versus Skyrim Platform Decision](architecture/platform-decision-openmw-vs-skyrim.md)
- Raw: `raw/skyrim/2026-08-10-skyrim-runtime-bridge-feasibility-evidence.md`
- Proven: a direct-file-absent MO2 VFS launch, restart-safe receipt replay and
  conflict rejection, and 5,000 applied command terminals in an 812 ms bridge
  span. Actor execution, embodied/unloaded scale and suppression remain open.

## [2026-08-10] ingest | SKYRIM-001 installed runtime baseline @ working tree
## [2026-08-10] ingest | SKYRIM-001 runtime bridge boundary @ working tree

- Updated: [Skyrim SE/AE + SKSE Capability Baseline](skyrim/capabilities.md)
- Raw: `raw/skyrim/2026-08-10-skyrim-runtime-bridge-feasibility-evidence.md`
- Proven: two same-BOM lifecycle runs, a warnings-as-errors native plugin and
  applied/replay/obsolete/rejected command receipts with zero SKSE log errors.
- Boundary: the CommonLib actor adapter builds and loads, but its live
  player/NPC/package/action receipt requires one normal Continue/save-load
  operation to produce `post_load_game`.


- Updated: [Skyrim SE/AE + SKSE Capability Baseline](skyrim/capabilities.md)
- Source: [Runtime/toolchain observation](../raw/skyrim/2026-08-10-skyrim-runtime-toolchain-observation.md)
- Result: Steam Skyrim SE `1.6.1170` now has official matching SKSE64 `2.2.6`
  and 62 required compiled Papyrus files with post-install hash equality.
- Boundary: no launch/profile/native-plugin proof has passed; SKYRIM-001 and
  the platform pivot remain In Progress and conditional.

## [2026-08-10] ingest | OpenMW versus Skyrim platform decision @ working tree

- Added: [OpenMW versus Skyrim Platform Decision](architecture/platform-decision-openmw-vs-skyrim.md)
- Added: [Skyrim SE/AE + SKSE Capability Baseline](skyrim/capabilities.md)
- Source: [PLATFORM-001 Platform Audit](../raw/architecture/2026-08-10-openmw-skyrim-platform-audit.md)
- Decision: conditionally combine Skyrim SE/AE's native AI packages, furniture,
  scenes, animation and physical systems with Fork as the sole durable
  simulation authority, using a thin SKSE/CommonLibSSE-NG bridge.
- Gate: preserve the proven OpenMW implementation and require six Skyrim
  runtime, bridge, embodiment, recovery, scale and world-suppression spikes
  before accepting the pivot or performing a whole-world transformation.

## [2026-08-10] correction | Current production wording and publication mirror @ working tree

- Updated: routine, population, journal, reputation, profile cognition, player
  identity, remembering-villager, capability-matrix and continuity articles.
- Source: WIKI-002 staleness/publication reconciliation.
- Correction: v9/v11/v57/v64/v178/v183 are retained as exact historical task
  qualification revisions, not current playable targets. Consolidated
  production is v203, and the originally deferred routine, reliable bridge and
  bounded LLM consumers are delivered.
- Workflow: the local `wiki/` plus linked `raw/` sources remain authoritative;
  `markusuk1/Fork-Fabric-Wiki` mirrors the maintained wiki while internal raw
  evidence remains local.

## [2026-08-10] completion | DIALOGUE-002 natural deterministic speech @ working tree

- Updated: [Profile-Derived Natural Speech](architecture/profile-derived-natural-speech.md), [Reliable Bridge Protocol](architecture/reliable-bridge-protocol.md), [Display and Interface Scaling](openmw/display-and-interface.md), [Provider-Neutral Spatial Speech](architecture/provider-neutral-spatial-speech.md), [Capability Matrix](architecture/capability-matrix.md), [Production Gap-Closure Programme](architecture/system-gap-closure-programme.md), [Programme Closure Reconciliation](architecture/programme-closure-reconciliation.md), and [Wiki Index](index.md)
- Source: [Final Qualification](../raw/architecture/2026-08-10-dialogue-002-final-qualification-evidence.md)
- Result: production v203 has eleven distinct deterministic speech identities,
  strict diagnostic/stage-direction boundaries, audited obsolete restored-save
  presentation, bounded atomic VFS replacement retry, exact 3440x1440/1.50 WGC
  evidence and a matching 6,467 ms Piper WAV. Final save/load/restart checks
  have zero engine, Lua and dead-letter errors.

## [2026-08-10] completion | AUDIT-001 programme closure reconciliation @ working tree

- Added: [Programme Closure Reconciliation](architecture/programme-closure-reconciliation.md)
- Updated: [Capability Matrix](architecture/capability-matrix.md), [Production Living-World Gap-Closure Programme](architecture/system-gap-closure-programme.md), [Daily NPC Routines](architecture/daily-routines-and-terminal-repair.md), [Epistemic Memory](architecture/epistemic-memory-and-bounded-recall.md), [Profile-Driven Cognition](architecture/profile-driven-npc-cognition.md), [Persistent Conversation](architecture/persistent-contextual-conversation-state.md), [Bounded Cognition Leases](architecture/bounded-key-npc-cognition-leases.md), [Proactive Cognition](architecture/proactive-observation-range-cognition.md), [Living Village](architecture/living-village-commerce-dialogue-voice.md), [Integration Architecture](architecture/integration-architecture.md), and [Wiki Index](index.md)
- Source: [AUDIT-001 Evidence](../raw/architecture/2026-08-10-audit-001-programme-closure-reconciliation.md)
- Result: every accepted PROGRAM-001 task is Done; DIALOGUE-002 remains the
  sole owner-observation blocker. Platform boundaries, unaccepted expansions,
  configured-provider evidence and separate programmes are now explicit, and
  maintained implementation source contains no unowned completion marker.

## [2026-08-10] completion | QA-001 integrated living-village qualification @ working tree

- Added: [Integrated Living-Village Qualification](architecture/integrated-living-village-qualification.md)
- Updated: [Production Living-World Gap-Closure Programme](architecture/system-gap-closure-programme.md), [Capability Matrix](architecture/capability-matrix.md), and [Wiki Index](index.md)
- Source: [QA-001 Evidence](../raw/architecture/2026-08-10-qa-001-integrated-living-village-evidence.md)
- Evidence: 10/10 gates in 332.1 seconds; real 24-hour day and cell round-trip,
  120-second resource soak, save/load, outage recovery and spatial voice pass;
  production v202's 113-table digest and owned directory/process sets remain
  unchanged.

## [2026-08-10] completion | TEST-001 owned Fork regression isolation @ working tree

- Updated: [Owned Fork Regression Isolation](workflow/owned-fork-regression-isolation.md) and [Wiki Index](index.md)
- Source: [TEST-001 Evidence](../raw/architecture/2026-08-10-test-001-owned-regression-isolation-evidence.md)
- Released: 52 stateful tests own current-module random-port runtimes; 12
  Fork-independent and one production-only case are explicit; zero unsafe
  historical/shared unattended defaults remain.
- Evidence: 12/12 representative cases passed in 236.7 seconds. All 113 public
  production v202 tables retained the same SHA-256 digest; owned directory and
  standalone process sets were unchanged; real OpenMW cases ended with zero
  engine/Lua/dead-letter errors.

## [2026-08-09] correction | DIALOGUE-002 owner audio rejection and final voice boundary @ working tree

- Updated: [Profile-Derived Natural Deterministic Speech](architecture/profile-derived-natural-speech.md), [Provider-Neutral Spatial NPC Speech](architecture/provider-neutral-spatial-speech.md), [Capability Matrix](architecture/capability-matrix.md), and [Wiki Index](index.md)
- Source: [Owner Audio Rejection and Final Voice Boundary](../raw/architecture/2026-08-09-dialogue-002-owner-audio-rejection-and-voice-boundary.md)
- Correction: the owner heard the obsolete `0/1000` scale, disproving the
  narrower v201 acceptance claim. v202 now rejects unsafe speech at the common
  Fork queue, activation and stale-state sweep, the synthesis worker, and
  OpenMW projection/save recovery.
- Evidence: focused and isolated natural-speech suites pass; Delantris v202
  production auto-resume passes; an isolated clean exact-subtitle Piper WAV was
  retained. WGC still fails with `0x80070424`, so owner reinspection remains.

## [2026-08-09] ingest | TEST-001 owned Fork regression isolation @ working tree

- Added: [Owned Fork Regression Isolation](workflow/owned-fork-regression-isolation.md)
- Source: [TEST-001 Research](../raw/architecture/2026-08-09-test-001-regression-isolation-research.md)
- Decision: **Adapt** the existing token-owned process/data helper with safe
  restart and leaf-test context semantics; do not reset production, retain
  historical defaults or add a new container/test dependency.
- Inventory: 47 scripts referenced historical databases/shared port 3012;
  seven already owned a runtime, one separate orchestrator covered eight
  current-module boundaries, and forty candidates require migration or an
  explicit production-only classification.

## [2026-08-09] correction | DIALOGUE-002 WGC acceptance boundary @ working tree

- Updated: [Profile-Derived Natural Deterministic Speech](architecture/profile-derived-natural-speech.md), [OpenMW Display and Interface Scaling](openmw/display-and-interface.md), [Capability Matrix](architecture/capability-matrix.md), and [Wiki Index](index.md)
- Source: [WGC Acceptance Correction](../raw/architecture/2026-08-09-dialogue-002-wgc-acceptance-correction.md)
- Correction: the F12 renderer candidate is diagnostic rather than accepted
  visual evidence and was deleted. The dialogue harness now requires WGC and
  can request the exact 3440x1440/1.50 proof profile.
- Observed blocker: Windows fails at the WGC support query with `0x80070424`;
  the protected per-user capture service cannot be started. The shortcut,
  manifest and profile are correct, but owner observation remains required.

## [2026-08-09] correction | DIALOGUE-002 spoken diagnostics and UI projection @ working tree

- Updated: [Profile-Derived Natural Deterministic Speech](architecture/profile-derived-natural-speech.md) and [Wiki Index](index.md)
- Source: [Spoken Diagnostic Leak Correction](../raw/architecture/2026-08-09-dialogue-002-spoken-diagnostic-leak-correction.md)
- Correction: normalized scarcity and other diagnostic values remain exact
  transcript fields but can no longer enter deterministic or accepted model
  speech. OpenMW now receives Arelion's real profile-derived style and keeps
  hashes, revisions, scores and variant identifiers out of the player panel.
- Evidence: forced `0/1000` state speaks "extremely scarce"; process-scoped
  background capture shows a clean live response; v201 production auto-resume
  remains stable without engine/Lua errors.

## [2026-08-09] update | DIALOGUE-002 natural deterministic speech @ working tree

- Added: [Profile-Derived Natural Deterministic Speech](architecture/profile-derived-natural-speech.md)
- Updated: [Persistent Conversation State](architecture/persistent-contextual-conversation-state.md), [Provider-Neutral Speech](architecture/provider-neutral-spatial-speech.md), [Integration Architecture](architecture/integration-architecture.md), [Living Village](architecture/living-village-commerce-dialogue-voice.md), [Capability Matrix](architecture/capability-matrix.md), and [Wiki Index](index.md)
- Sources: [Research](../raw/architecture/2026-08-09-dialogue-002-natural-deterministic-speech-research.md) and [Qualification Evidence](../raw/architecture/2026-08-09-dialogue-002-natural-deterministic-speech-evidence.md)
- Result: 11 profile-derived style/reply identities, clean spoken text, stable
  variants, LLM stage-direction rejection, cache/timeout/restart proof and real
  OpenMW active-dialogue save/load pass on the v199 production build.
- Open item: owner-visible UWQHD inspection remains required because Windows
  background capture returned `0x80070424`; no foreground fallback was used.

## [2026-08-09] release | POP-002 exact household and inactive-actor lifecycle @ working tree

- Updated: [Household Occupancy and Unloaded Actors](architecture/household-occupancy-and-unloaded-actors.md), [Daily Routines](architecture/daily-routines-and-terminal-repair.md), [Integration Architecture](architecture/integration-architecture.md), [Capability Matrix](architecture/capability-matrix.md), [Display and Interface](openmw/display-and-interface.md), and [Wiki Index](index.md)
- Source: [Exact Lifecycle Evidence](../raw/architecture/2026-08-09-pop-002-exact-lifecycle-reconciliation-evidence.md)
- Result: v183 separates desired presence from exact observed embodiment,
  reconciles only inactive lineage/revision-bound objects, survives save/load,
  Fork restart and companion crash/reconnect, and finishes with zero pending
  work, dead letters or runtime errors.
- Visual/display: inspected work/home renderer previews were produced without
  foreground activation. Capture configuration is isolated from the playable
  3440x1440/1.50 explorer profile.
- Correction: disposable command projections now reset before launch, so a
  reused data directory cannot execute intentions from an earlier database.

## [2026-08-09] update | POP-002 household and inactive-actor foundation @ working tree

- Added: [Household Occupancy and Unloaded Actors](architecture/household-occupancy-and-unloaded-actors.md)
- Updated: [Daily Routines](architecture/daily-routines-and-terminal-repair.md), [Integration Architecture](architecture/integration-architecture.md), [Capability Matrix](architecture/capability-matrix.md), and [Wiki Index](index.md)
- Sources: [POP-002 Research](../raw/architecture/2026-08-09-pop-002-household-occupancy-unloaded-actor-research.md) and [POP-002 Runtime Evidence](../raw/architecture/2026-08-09-pop-002-household-runtime-evidence.md)
- Result: schema-2 places/members/capacities and Fork presence authority pass
  isolated proofs; an accelerated OpenMW day safely materialized eight inactive
  actors, deferred twelve visible cross-cell moves and logged zero errors.
- Boundary: manual work/home inspection and exact lifecycle/restart/outage
  reconciliation remain open, so POP-002 stays In Progress.

## [2026-08-09] release | Native player identity and promoted desktop resume @ working tree

- Updated: [Player Identity and Save Lineage](architecture/player-identity-and-save-lineage.md), [Reliable Bridge Protocol](architecture/reliable-bridge-protocol.md), [Replacement World-Shell Pipeline](openmw/world-shell-pipeline.md), [Gap-Closure Programme](architecture/system-gap-closure-programme.md), [Integration Architecture](architecture/integration-architecture.md), [Capability Matrix](architecture/capability-matrix.md), and [Wiki Index](index.md)
- Sources: [Native Character and Production Evidence](../raw/architecture/2026-08-09-player-001-native-character-production-evidence.md), [Large-Epoch ACK Correction](../raw/architecture/2026-08-09-player-001-large-epoch-ack-research.md), and [Atomic Status Stability](../raw/architecture/2026-08-09-player-001-atomic-status-read-stability-research.md)
- Result: the user-created Delantris identity and lineage survive native
  save/load, OpenMW/companion restart and real Fork restart. Eight
  current-schema subsystem regressions pass in isolated servers.
- Production: v178 is the launcher default; the generated promoted manifest
  auto-resumes the newest exact save through the ordinary desktop path, while
  `-NewCharacter` is the explicit bypass. Final launch loaded all three masters,
  remained focus-safe and reported zero engine/Lua errors.
- Corrections: ACK schema 2 string-projects int64 epochs for OpenMW markup;
  post-load readiness requires a fresh ACK; focus guards test OpenMW ownership,
  not unrelated app changes; a one-second validated-status grace absorbs atomic
  replacement gaps without masking persistent or authoritative offline state.

## [2026-08-09] correction | Fork-authoritative social save rollback @ working tree

- Updated: [Embodied Social Information Flow](architecture/embodied-social-information-flow.md), [Integration Architecture](architecture/integration-architecture.md), and [Capability Matrix](architecture/capability-matrix.md)
- Source: [SOCIAL-001 Save Rollback Correction](../raw/architecture/2026-08-09-social-001-save-load-rollback-correction.md)
- Correction: an older OpenMW save can predate a later Fork commit, so restored
  social movement must not blindly re-emit the same delivery or journal identity
  with newly measured physical evidence.
- Decision: combine saved OpenMW physical work with the current bounded Fork
  projection. Re-authorize exact live dispatches, suppress terminal ones and
  wait fail-closed if authority is unavailable. The isolated native proof
  restored 10/10 actors, delivered two messages, suppressed one stale replay
  and ended with zero dead letters or engine/Lua errors.

## [2026-08-09] update | UWQHD interface scale 1.50 @ working tree

- Updated: [OpenMW Display and Interface Scaling](openmw/display-and-interface.md)
- Source: [UWQHD Interface Scale 1.50 Preference](../raw/openmw/2026-08-09-uwqhd-scale-150-preference.md)
- Observed: the safe applicator set 3440x1440 borderless and GUI scale 1.50,
  created a backup, and preserved reflection detail 2 / RTT size 1024.

## [2026-08-09] correction | GPU-backed background OpenMW capture @ working tree

- Updated: [OpenMW Display and Interface Scaling](openmw/display-and-interface.md)
- Source: [PLAYER-001 GPU-Backed Background Capture Correction](../raw/openmw/2026-08-09-player-001-wgc-capture-correction.md)
- Correction: `PrintWindow` was a title-bar false positive for the black
  OpenGL client, and fully off-screen WGC retained a stale loading frame.
- Decision: adapt Microsoft's `Windows.Graphics.Capture` HWND snapshot path,
  launch with `SW_SHOWMINNOACTIVE`, keep OpenMW at the bottom of the desktop
  stack, and require an unchanged foreground handle plus inspected live frame.

## [2026-08-09] ingest | Player identity and save lineage @ working tree

- Added: [Player Identity and Save Lineage](architecture/player-identity-and-save-lineage.md)
- Sources: [PLAYER-001 prior art](../raw/architecture/2026-08-09-player-001-character-bootstrap-prior-art.md), [persistence evidence](../raw/architecture/2026-08-09-player-001-persistence-harness-evidence.md), and [non-intrusive capture evidence](../raw/openmw/2026-08-09-player-001-nonintrusive-visual-capture.md)
- Status: implemented and partially runtime-qualified, not released. Native
  ACK-gated save/load plumbing and real isolated Fork restart pass; one
  user-consented five-screen creation run remains before full OpenMW restart,
  regression and production promotion gates.

## [2026-08-09] ingest | Non-intrusive OpenMW visual evidence @ working tree

- Updated: [OpenMW Display and Interface Scaling](openmw/display-and-interface.md)
- Source: [PLAYER-001 Non-Intrusive OpenMW Visual Capture Evidence](../raw/openmw/2026-08-09-player-001-nonintrusive-visual-capture.md)
- Decision: automated OpenMW captures run in an isolated off-screen window and
  use foreground-invariant `PrintWindow` capture. There is no focus-taking or
  visible-desktop fallback; capture failure fails the proof.

## [2026-08-09] ingest | Provider-neutral spatial NPC speech @ working tree

- Added: [Provider-Neutral Spatial NPC Speech](architecture/provider-neutral-spatial-speech.md)
- Updated: [Living Village](architecture/living-village-commerce-dialogue-voice.md), [Integration Architecture](architecture/integration-architecture.md), [Capability Matrix](architecture/capability-matrix.md), [Fork Capabilities](fork/capabilities.md), and [OpenMW Capabilities](openmw/capabilities.md)
- Sources: [VOICE-001 research](../raw/architecture/2026-08-09-voice-001-provider-neutral-spatial-speech-research.md) and [implementation evidence](../raw/architecture/2026-08-09-voice-001-provider-neutral-spatial-speech-evidence.md)
- Decision: combine Fork domain/Fabric ownership, a provider-neutral verified
  cache worker, a measured 32-slot OpenMW VFS ring and actor-local physical
  receipts. Priority, cancellation, timeout, save/load, live slot wrap and
  worker/companion/Fork restart gates pass. Piper is the proved baseline;
  uncalled cloud-provider quality is not claimed.

## [2026-08-09] ingest | Proactive observation-range cognition @ working tree

- Added: [Proactive Observation-Range Cognition](architecture/proactive-observation-range-cognition.md)
- Updated: [Bounded Cognition Leases](architecture/bounded-key-npc-cognition-leases.md), [Integration Architecture](architecture/integration-architecture.md), [Capability Matrix](architecture/capability-matrix.md), [Profile Cognition](architecture/profile-driven-npc-cognition.md), [Living Village](architecture/living-village-commerce-dialogue-voice.md), [Gap-Closure Programme](architecture/system-gap-closure-programme.md), [Fork Capabilities](fork/capabilities.md), and [OpenMW Capabilities](openmw/capabilities.md)
- Source: [LLM-002 implementation evidence](../raw/architecture/2026-08-09-llm-002-proactive-observation-evidence.md)
- Decision: combine actor-local OpenMW entry/LOS evidence with profile-driven
  Fork salience/cooldown, native Context/Fabric wait/initiate work and a fresh
  physical receipt gate. Direct, live, save/load, Fork restart, visual and
  affected regression gates pass; fixture output is not model-quality evidence.

## [2026-08-09] ingest | Bounded key-NPC cognition leases @ working tree

- Added: [Bounded Key-NPC Cognition Leases](architecture/bounded-key-npc-cognition-leases.md)
- Updated: [Integration Architecture](architecture/integration-architecture.md), [Capability Matrix](architecture/capability-matrix.md), [Living Village](architecture/living-village-commerce-dialogue-voice.md), [Gap-Closure Programme](architecture/system-gap-closure-programme.md), [Fork Capabilities](fork/capabilities.md), and [OpenMW Capabilities](openmw/capabilities.md)
- Sources: [LLM-001 research](../raw/architecture/2026-08-09-llm-001-cognition-lease-research.md) and [implementation evidence](../raw/architecture/2026-08-09-llm-001-bounded-cognition-lease-evidence.md)
- Decision: combine native Fork Context/Fabric with external provider-neutral
  structured inference and the existing dialogue/routine receipt boundary.
  Direct adversarial, real OpenMW, save/load, Fork restart, visual and regression
  gates pass. Fixture provenance is explicit and is not model-quality evidence;
  proactive observation-range assessment remains separate.

## [2026-08-09] ingest | Persistent contextual conversation state @ working tree

- Added: [Persistent Contextual Conversation State](architecture/persistent-contextual-conversation-state.md)
- Updated: [Integration Architecture](architecture/integration-architecture.md), [Capability Matrix](architecture/capability-matrix.md), [Reliable Bridge Protocol](architecture/reliable-bridge-protocol.md), [Living Village](architecture/living-village-commerce-dialogue-voice.md), [Gap-Closure Programme](architecture/system-gap-closure-programme.md), and [OpenMW Capabilities](openmw/capabilities.md)
- Sources: [DIALOGUE-001 research](../raw/architecture/2026-08-09-dialogue-001-contextual-state-research.md) and [implementation evidence](../raw/architecture/2026-08-09-dialogue-001-contextual-state-evidence.md)
- Decision: adapt proven dialogue lifecycle/state patterns into Fork-owned
  sessions, bounded context and typed actions while OpenMW owns UI, routines,
  Barter and save state. Direct, live, visual, save/load and restart proof pass.
  Save rollback now uses a new producer epoch and content-digested acks.

## [2026-08-09] correction | REPUTATION-001 visual gate resolved @ working tree

- Updated: [Derived Player Identity, Reputation and NPC Reactions](architecture/contextual-presentation-reactions.md) and [Gap-Closure Programme](architecture/system-gap-closure-programme.md)
- Source: [REPUTATION-001 implementation evidence](../raw/architecture/2026-08-09-reputation-001-derived-identity-evidence.md)
- Correction: the protected Windows Security dialog no longer blocks the task.
  A guarded `PrintWindow` fallback captured only the verified OpenMW window;
  the inspected frame visibly shows Guard Sera's `EXPOSE FALSE GUARD` result,
  evidence and scores without dismissing or modifying the protected dialog.

## [2026-08-09] ingest | Causal supply chain and scarcity @ working tree

- Added: [Causal Supply Chain and Scarcity](architecture/causal-supply-chain-and-scarcity.md)
- Updated: [Integration Architecture](architecture/integration-architecture.md), [Capability Matrix](architecture/capability-matrix.md), [Gap-Closure Programme](architecture/system-gap-closure-programme.md), [Living Village Commerce](architecture/living-village-commerce-dialogue-voice.md), and [Ecosystem Reuse Matrix](architecture/ecosystem-reuse-matrix.md)
- Sources: [ECONOMY-001 research](../raw/architecture/2026-08-09-economy-001-supply-chain-research.md) and [implementation evidence](../raw/architecture/2026-08-09-economy-001-causal-supply-chain-evidence.md)
- Decision: combine OpenMW physical inventory, actor Travel, native Barter,
  crime and saves with Fork production, provenance, delivery, balance and
  scarcity authority. Direct, live, visual, save/load, restart and transport
  recovery evidence proves the first finite six-unit supply chain.

## [2026-08-09] ingest | Derived player identity and reputation @ working tree

- Replaced: [Derived Player Identity, Reputation and NPC Reactions](architecture/contextual-presentation-reactions.md)
- Updated: [Integration Architecture](architecture/integration-architecture.md), [Capability Matrix](architecture/capability-matrix.md), and [Gap-Closure Programme](architecture/system-gap-closure-programme.md)
- Sources: [REPUTATION-001 research](../raw/architecture/2026-08-09-reputation-001-derived-identity-research.md) and [implementation evidence](../raw/architecture/2026-08-09-reputation-001-derived-identity-evidence.md)
- Decision: combine OpenMW physical/native/causal evidence with Fork-owned
  condition, objective deeds, observer-local exposure and identity appraisal.
  Production schema 10 rejects semantic truth claims; direct, live, restart and
  reconnect tests prove disguise, credentials, reputation and replay behavior.

## [2026-08-09] ingest | General player journal and knowledge interactions @ working tree

- Updated: [General Player Journal and Knowledge Interactions](architecture/journal-driven-knowledge-interactions.md), [Integration Architecture](architecture/integration-architecture.md), [Capability Matrix](architecture/capability-matrix.md), [Gap-Closure Programme](architecture/system-gap-closure-programme.md), and [OpenMW Capability Baseline](openmw/capabilities.md)
- Sources: [JOURNAL-002 research](../raw/architecture/2026-08-09-journal-002-general-player-journal-research.md) and [implementation evidence](../raw/architecture/2026-08-09-journal-002-general-player-journal-evidence.md)
- Decision: combine OpenMW physical/native journal surfaces with Fork-owned
  revisioned current knowledge and immutable correction history. Direct and
  live proofs cover all five source kinds, paging, deliberate disclosure,
  physical/unspoken/stale rejection, save/load and restart/reconnect recovery.

## [2026-08-09] ingest | Embodied social information flow @ working tree

- Added: [Embodied Social Information Flow](architecture/embodied-social-information-flow.md)
- Updated: [Integration Architecture](architecture/integration-architecture.md), [Capability Matrix](architecture/capability-matrix.md), and [Gap-Closure Programme](architecture/system-gap-closure-programme.md)
- Sources: [SOCIAL-001 research](../raw/architecture/2026-08-09-social-001-embodied-information-flow-research.md) and [implementation evidence](../raw/architecture/2026-08-09-social-001-embodied-information-flow-evidence.md)
- Decision: combine OpenMW actor-local movement/range/ray evidence with Fork
  message, trust, privacy, memory and bounded provenance. Direct and live
  proofs cover rejected privacy, real overhearing, hops 0/1/2, active
  save/load, retry, expiry and restart/reconnect recovery.

## [2026-08-09] ingest | Explainable goal and intention arbitration @ working tree

- Added: [Explainable Goal and Intention Arbitration](architecture/explainable-goal-and-intention-arbitration.md)
- Updated: [Integration Architecture](architecture/integration-architecture.md), [Capability Matrix](architecture/capability-matrix.md), [Normalized Perception](architecture/normalized-perception-and-attention.md), and [Gap-Closure Programme](architecture/system-gap-closure-programme.md)
- Sources: [DECIDE-001 research](../raw/architecture/2026-08-09-decide-001-goal-arbitration-research.md) and [implementation evidence](../raw/architecture/2026-08-09-decide-001-explainable-goal-arbitration-evidence.md)
- Decision: combine deterministic BDI stage separation, utility ranking,
  conflict explanations, commitment hysteresis, Fork revision checks and
  existing OpenMW AI packages. Production v24 proves 20 atomic threat
  arbitrations, 100 candidates, three selected goals, physical receipt closure,
  exact routine interruption/resumption and recovery boundaries.

## [2026-08-09] ingest | Epistemic memory and bounded recall @ working tree

- Added: [Epistemic Memory and Bounded Recall](architecture/epistemic-memory-and-bounded-recall.md)
- Updated: [Normalized Perception](architecture/normalized-perception-and-attention.md), [Integration Architecture](architecture/integration-architecture.md), [Capability Matrix](architecture/capability-matrix.md), and [Gap-Closure Programme](architecture/system-gap-closure-programme.md)
- Sources: [MEMORY-001 research](../raw/architecture/2026-08-09-memory-001-epistemic-lifecycle-research.md) and [implementation evidence](../raw/architecture/2026-08-09-memory-001-epistemic-lifecycle-evidence.md)
- Decision: combine immutable typed domain evidence with the native Fork Memory
  Engine. Production v16 proves vector/version/CMT creation, traversable
  support/contradiction edges, deterministic claim revision, non-destructive
  maintenance, bounded temporal recall, abstention and full recovery paths.

## [2026-08-09] ingest | Normalized physical and social perception @ working tree

- Added: [Normalized Physical and Social Perception](architecture/normalized-perception-and-attention.md)
- Updated: [Integration Architecture](architecture/integration-architecture.md), [Capability Matrix](architecture/capability-matrix.md), [Gap-Closure Programme](architecture/system-gap-closure-programme.md), [OpenMW Capability Baseline](openmw/capabilities.md), and [World-Shell Pipeline](openmw/world-shell-pipeline.md)
- Sources: [PERCEPT-001 research](../raw/architecture/2026-08-09-percept-001-observation-gateway-research.md) and [implementation evidence](../raw/architecture/2026-08-09-percept-001-normalized-observation-evidence.md)
- Decision: combine OpenMW actor-local physical evidence with independent
  observer geometry and Fork profile/state-weighted appraisal. Production v12
  proves five stimulus kinds, detected/rejected evidence, exact replay,
  save/process restart, outage recovery and affected regressions.

## [2026-08-09] ingest | Daily NPC routines and terminal repair @ working tree

- Added: [Daily NPC Routines and Terminal Repair](architecture/daily-routines-and-terminal-repair.md)
- Updated: [Integration Architecture](architecture/integration-architecture.md), [Capability Matrix](architecture/capability-matrix.md), and [Gap-Closure Programme](architecture/system-gap-closure-programme.md)
- Sources: [ROUTINE-001 research](../raw/architecture/2026-08-09-routine-001-scheduler-research.md) and [implementation evidence](../raw/architecture/2026-08-09-routine-001-daily-scheduler-evidence.md)
- Decision: combine OpenMW game time, AI and save lifecycle with Fork-owned
  slots, reservations, commands and transitions. Production v11 proves a full
  accelerated day, physical receipts, interruption/resumption, bounded repair,
  obsolete-message audit, offline fallback and save/process restart recovery.

## [2026-08-09] ingest | Unified NPC profile and state @ working tree

- Added: [Unified NPC Profile and State](architecture/unified-npc-profile-and-state.md)
- Updated: [Profile-Driven NPC Cognition](architecture/profile-driven-npc-cognition.md), [Capability Matrix](architecture/capability-matrix.md), and [Gap-Closure Programme](architecture/system-gap-closure-programme.md)
- Sources: [PROFILE-001 research](../raw/architecture/2026-08-09-profile-001-unified-npc-model-research.md) and [implementation evidence](../raw/architecture/2026-08-09-profile-001-unified-npc-model-evidence.md)
- Decision: combine OpenMW physical records, one validated authored manifest
  and revisioned Fork definition/state/history. Production v9 proves exact
  reseed/upgrades, internal event fingerprints, restart retention, fail-closed
  startup and all affected engine regressions.

## [2026-08-09] ingest | Reliable OpenMW/Fork bridge protocol @ working tree

- Added: [Reliable OpenMW/Fork Bridge Protocol](architecture/reliable-bridge-protocol.md)
- Updated: [Integration Architecture](architecture/integration-architecture.md), [Capability Matrix](architecture/capability-matrix.md), and [Gap-Closure Programme](architecture/system-gap-closure-programme.md)
- Sources: [BRIDGE-003 research](../raw/architecture/2026-08-09-bridge-003-reliable-transport-research.md) and [implementation evidence](../raw/architecture/2026-08-09-bridge-003-reliable-transport-evidence.md)
- Decision: combine the existing VFS/tagged-log seam, CloudEvents-shaped outer
  envelope, Fork ordering semantics and a thin bounded journal. Protocol 6 now
  proves stable replay, atomic checkpoint/domain effects, explicit ack,
  gap/poison/backpressure/rotation recovery, companion crash and standalone
  Fork process restart without a broker or engine patch.

## [2026-08-08] correction | Person-to-person trade is negotiated and compensated @ working tree

- Updated: [Journal-Driven Knowledge and Physical Interactions](architecture/journal-driven-knowledge-interactions.md), [Contextual Player Presentation](architecture/contextual-presentation-reactions.md), and [Capability Matrix](architecture/capability-matrix.md)
- Source: [COMMERCE-001 negotiated-trade evidence](../raw/architecture/2026-08-08-commerce-001-negotiated-trade-evidence.md)
- Correction: the delivered flow is no longer fixed at one item/base value.
  Players choose exact quantity and total asking price; Fork can accept, counter
  or decline; counter acceptance/decline is explicit and parent-bound. Exact
  multi-unit settlement and injected post-item failure compensation passed.
  Three inspected input-free 3440x1440 frames prove quantity, price and counter
  controls.

## [2026-08-08] ingest | Production living-world gap-closure programme @ 6e7f7a9

- Added: [Production Living-World Gap-Closure Programme](architecture/system-gap-closure-programme.md)
- Source: [System gap audit and prior-art decision](../raw/architecture/2026-08-08-program-001-system-gap-audit-research.md)
- Decision: combine OpenMW physical/UI/time authority, Fork durable cognition
  and work authority, and a hardened companion; accept no player capability
  from a staged reducer path alone.
- Recorded sixteen explicit system contracts and a dependency-ordered backlog.

## [2026-08-08] correction | Interaction backend made usable end to end @ working tree

- Updated: [Journal-Driven Knowledge and Physical Interactions](architecture/journal-driven-knowledge-interactions.md), [Living Village](architecture/living-village-commerce-dialogue-voice.md), [Capability Matrix](architecture/capability-matrix.md), and [Integration Architecture](architecture/integration-architecture.md)
- Source: [INTERACT-003 Evidence](../raw/architecture/2026-08-08-interact-003-usable-interactions-evidence.md)
- Correction: INTERACT-001 proved reducers and transfers but its player flow
  closed on click, used fleeting HUD replies and silently chose demo food. The
  production bridge was also blocked by a stale unversioned companion. The UI
  now preserves turn-taking, offers a real paged inventory selection, rechecks
  the chosen stack in OpenMW, shows offline/timeout states, and uses a
  protocol-v5 engine-bound companion. Production response and selected-gift
  proofs passed with zero engine/Lua errors.

## [2026-08-08] correction | Custom interaction root requires a visible layer @ working tree

- Updated: [Journal-Driven Knowledge and Physical Interactions](architecture/journal-driven-knowledge-interactions.md)
- Source: [Visible UI correction](../raw/openmw/2026-08-08-interact-002-visible-ui-correction.md)
- Correction: the INTERACT-001 construction marker did not prove visibility.
  The MWUI root was detached because it named no layer while Interface mode
  still paused the game. It now attaches to `Windows`; a captured OpenMW frame
  visibly shows all Dava Relas menu choices and the runtime proof rejects layer
  removal.

## [2026-08-08] verify | Deterministic responses are receipt-closed @ working tree

- Updated: [Journal-Driven Knowledge and Physical Interactions](architecture/journal-driven-knowledge-interactions.md)
- Source: [Response-receipt verification](../raw/architecture/2026-08-08-interact-001-response-receipt-verification.md)
- Result: the final OpenMW proof produced one zero-delta response presentation
  receipt plus the gift, sale and theft receipts, with zero engine/Lua errors.

## [2026-08-08] correction | Final interaction database is v4b @ working tree

- Updated: [Journal-Driven Knowledge and Physical Interactions](architecture/journal-driven-knowledge-interactions.md)
- Source: [Production database correction](../raw/architecture/2026-08-08-interact-001-production-database-correction.md)
- Correction: the final response-receipt build is published and seeded at
  `game-openmw-npc-v4b`; `v4a` is superseded after its anonymous owner identity
  rejected an in-place refresh.

## [2026-08-08] verify | Journal-driven knowledge and physical interaction loop @ working tree

- Added: [Journal-Driven Knowledge and Physical Interactions](architecture/journal-driven-knowledge-interactions.md)
- Updated: [Capability Matrix](architecture/capability-matrix.md), [Living Village](architecture/living-village-commerce-dialogue-voice.md), and [World-Shell Pipeline](openmw/world-shell-pipeline.md)
- Source: [INTERACT-001 Evidence](../raw/architecture/2026-08-08-interact-001-journal-interaction-evidence.md)
- Result: clickable topics, stat-tiered knowledge, sourced disclosure,
  need/funds-aware gift/sale decisions, later-tick physical reconciliation and
  native Theft passed Fork/live OpenMW proofs.
- Correction: empty `Thief` and `Alarm` records are required engine substrates
  for native crime in the otherwise dialogue-free clean world.

## [2026-08-08] verify | Spatial village and native commerce delivered @ working tree

- Updated: [Living Village, Commerce, Conversation and Voice](architecture/living-village-commerce-dialogue-voice.md)
- Updated: [Fork and OpenMW Capability Matrix](architecture/capability-matrix.md)
- Updated: [Forkâ€“OpenMW Integration Architecture](architecture/integration-architecture.md)
- Source: [VILLAGE-001 Evidence](../raw/openmw/2026-08-08-village-001-spatial-commerce-evidence.md)
- Result: ten project villagers now occupy six exterior, three tradehouse and
  one census anchor; the project merchant exposes 16 stock types, 35 units,
  800 gold and native Barter mode with zero engine/Lua/dialogue errors.
- Operational corrections: persist local actor initialization through save/load;
  delay test teleports until actor placement settles; accept actor-to-actor
  Travel at collision-safe interaction range; isolate companion runtime roots,
  logs and mutexes during end-to-end proofs.

## [2026-08-08] ingest | Living village, commerce, conversation and voice @ 6e7f7a9

- Added: [Living Village, Commerce, Conversation and Voice](architecture/living-village-commerce-dialogue-voice.md)
- Updated: [Fork and OpenMW Capability Matrix](architecture/capability-matrix.md)
- Updated: [Fork–OpenMW Integration Architecture](architecture/integration-architecture.md)
- Updated: [OpenMW Ecosystem Reuse Matrix](architecture/ecosystem-reuse-matrix.md)
- Updated: [OpenMW Capability Baseline](openmw/capabilities.md)
- Source: [DISC-002 Research](../raw/architecture/2026-08-08-disc-002-village-commerce-dialogue-voice-research.md)
- Decision: replace player-relative actor offsets with authored spatial anchors;
  reuse the retained tradehouse and native barter; combine deterministic and
  leased LLM dialogue; keep voice provider-neutral with OpenMW spatial playback.

## [2026-08-08] correction | Appearance presets no longer over-encumber the explorer @ working tree

- Updated: [Contextual Player Presentation and NPC Reactions](architecture/contextual-presentation-reactions.md)
- Source: [NPC-003 Active-Preset Inventory Decision](../raw/openmw/2026-08-08-npc-003-active-preset-inventory-decision.md)
- Owner-observed fault: NPC-002 placed all four presets' items in the player
  inventory simultaneously, exceeding carry capacity and preventing movement.
- Decision: adapt native OpenMW object removal/creation so only exact project-
  created objects for the active preset are carried; do not cheat capacity or
  remove owner-acquired items by record ID.
- Result: all four presets measured below 150 capacity (maximum 119), and the
  full 20-reaction runtime proof plus brain-offline regression passed.

## [2026-08-08] ingest | Contextual player presentation and NPC reactions @ working tree

- Added: [Contextual Player Presentation and NPC Reactions](architecture/contextual-presentation-reactions.md)
- Updated: [Profile-Driven NPC Cognition and Social Knowledge](architecture/profile-driven-npc-cognition.md), [Fork and OpenMW Capability Matrix](architecture/capability-matrix.md), and [Fork–OpenMW Integration Architecture](architecture/integration-architecture.md)
- Sources: [NPC-002 Research](../raw/architecture/2026-08-08-npc-002-contextual-appearance-research.md) and [NPC-002 Evidence](../raw/architecture/2026-08-08-npc-002-contextual-appearance-evidence.md)
- Decision: combine OpenMW real stance/equipment/condition perception and native
  actuation with Fork-owned observer appraisal, identity/reputation context and
  receipt lifecycle.
- Result: four embodied observers completed 20 reactions across five player
  states; the same armed raider produced four distinct profile/role outcomes.
- Operational correction: mutable VFS snapshot paths must exist before OpenMW
  starts, even though their contents can be atomically replaced during play.

## [2026-08-08] correction | Clarify current role/profile scoring boundary @ 6e7f7a9

- Updated: [Profile-Driven NPC Cognition and Social Knowledge](architecture/profile-driven-npc-cognition.md)
- Source: current `bridge/fork-module/src/lib.rs` implementation and the existing
  [NPC-001 Evidence](../raw/architecture/2026-08-08-npc-001-profile-driven-witness-evidence.md)
- Correction: roles and spouse identities are stored context, but roles do not
  yet affect witness candidate scores; spouse identity currently addresses the
  delayed private disclosure only.

## [2026-08-08] ingest | Profile-driven NPC cognition and social knowledge @ 6e7f7a9

- Added: [Profile-Driven NPC Cognition and Social Knowledge](architecture/profile-driven-npc-cognition.md)
- Updated: [Fork–OpenMW Integration Architecture](architecture/integration-architecture.md), [Fork and OpenMW Capability Matrix](architecture/capability-matrix.md), [Remembering Villager Vertical Slice](architecture/remembering-villager-vertical-slice.md), and [Replacement World-Shell Pipeline](openmw/world-shell-pipeline.md)
- Sources: [NPC-001 Research](../raw/architecture/2026-08-08-npc-001-profile-driven-witness-research.md) and [NPC-001 Evidence](../raw/architecture/2026-08-08-npc-001-profile-driven-witness-evidence.md)
- Result: three profile-driven witness decisions, OpenMW destination receipts,
  delayed non-omniscient guard/spouse belief transfer, and the required empty
  Idle dialogue substrate are now verified.

## [2026-08-07] correction | Empty Hello substrate fixes Ralen frame-error storm @ working tree

- Updated: [Replacement World-Shell Pipeline](openmw/world-shell-pipeline.md)
- Updated: [Remembering Villager Vertical Slice](architecture/remembering-villager-vertical-slice.md)
- Sources: [Greeting Fault Diagnosis](../raw/openmw/2026-08-07-bridge-002-greeting-fault-diagnosis.md), [Greeting Runtime Verification](../raw/openmw/2026-08-07-bridge-002-greeting-runtime-verification.md)
- Correction: the BRIDGE-001 startup smoke missed a native ambient-greeting
  lookup that began about ten seconds after Ralen appeared and generated 5,244
  per-frame errors because every Dialogue record had been removed.
- Result: all three `Hello` overrides are now empty generated Voice containers;
  binary round-trip audit passed and the 25-second encounter check observed one
  Ralen creation with zero missing-Hello, engine/fatal, or Lua errors.

## [2026-08-07] verify | First remembering villager delivered @ working tree

- Added: [Remembering Villager Vertical Slice](architecture/remembering-villager-vertical-slice.md)
- Updated: [Fork–OpenMW Integration Architecture](architecture/integration-architecture.md)
- Sources: [BRIDGE-001 Combine Decision](../raw/architecture/2026-08-07-bridge-001-one-remembering-villager.md), [Implementation Evidence](../raw/architecture/2026-08-07-bridge-001-implementation-evidence.md)
- Result: OpenMW created Ralen Varo with native Wander and project activation;
  Fork stored the meeting idempotently; a fresh process recalled count 1; the
  no-companion run kept gameplay active and displayed offline fallback.
- Deployment: clean local `game-openmw-village-v1` starts at zero memories for
  the owner's first real interaction.

## [2026-08-07] verify | Seyda Neen explorer launch passed @ working tree

- Updated: [Replacement World-Shell Pipeline](openmw/world-shell-pipeline.md)
- Sources: [Village Spawn Decision](../raw/openmw/2026-08-07-village-spawn-native-profile-decision.md), [Seyda Neen Launch Verification](../raw/openmw/2026-08-07-seyda-neen-explorer-launch-verification.md)
- Decision: configure OpenMW's native unquoted `start=Seyda Neen` profile and
  bypass mode; keep the generated Main inert.
- Result: the desktop launcher loaded exterior `Seyda Neen (-2, -9)`, remained
  healthy for 15 seconds, and logged no engine error, fatal, or missing script.

## [2026-08-07] verify | Clean GOTY world shell passed @ working tree

- Updated: [Replacement World-Shell Pipeline](openmw/world-shell-pipeline.md)
- Source: [SHELL-002 Clean GOTY World Evidence](../raw/openmw/2026-08-07-shell-002-clean-goty-world.md)
- Result: the checked three-master pipeline removed all legacy world actors,
  spawns, dialogue/journals, scripts, startup registrations, and 12,276 actor
  placements while retaining world record counts.
- Runtime: clean representative cells in base Morrowind, Mournhold, and
  Solstheim loaded without engine error entries.
- Boundary: player templates and one generated inert Main remain for engine
  construction; project character creation and gameplay must be supplied anew.

## [2026-08-07] update | Native Stop hook enforces continuation integrity @ 6e7f7a9

- Updated: [Repository Continuity and Knowledge Workflow](workflow/repository-continuity-system.md)
- Source: [Codex Stop Continuation Gate](../raw/workflow/2026-08-07-codex-stop-continuation-gate.md)
- Decision: configure Codex's native project `Stop` hook to reject final
  messages claiming work is still starting, continuing, or active.
- Verification: direct tests cover initial and repeated blocking, completed
  messages, and genuine approval blockers.

## [2026-08-07] verify | Replacement world-shell proof passed @ working tree

- Added: [Replacement World-Shell Pipeline](openmw/world-shell-pipeline.md)
- Updated: [OpenMW Ecosystem Reuse Matrix](architecture/ecosystem-reuse-matrix.md)
- Source: [SHELL-001 World-Shell Proof Evidence](../raw/openmw/2026-08-07-shell-001-world-shell-proof.md)
- Result: a source-pinned manifest removed Fargoth and the functional quest
  closure, preserved cell/landscape/static counts, and loaded successfully in
  Seyda Neen using generated Morrowind plus installed Tribunal/Bloodmoon.
- Constraint: DeltaPlugin conversion retains the canonical `Morrowind`
  basename; complete stripping still needs safe nested INFO/cross-reference
  rewriting.

## [2026-08-07] lint | 0 issues found

- Verified the ecosystem matrix, cascaded bridge/install corrections, index
  coverage, source provenance, metadata, and local Markdown links.

## [2026-08-07] correction | Installed Morrowind is disposable @ 6e7f7a9

- Updated: [OpenMW Ecosystem Reuse Matrix](architecture/ecosystem-reuse-matrix.md)
- Source: [Disposable Source Install Decision](../raw/openmw/2026-08-07-disposable-source-install-decision.md)
- Correction: there are no saves or installation state to preserve, and GOG
  reinstall is acceptable. Prefer the cleanest replacement game/master instead
  of an overlay chosen only to protect installed files. Redistribution limits
  remain unchanged.

## [2026-08-07] ingest | OpenMW ecosystem prior-art and reuse matrix @ 6e7f7a9

- Added: [OpenMW Ecosystem Reuse Matrix](architecture/ecosystem-reuse-matrix.md)
- Updated: [Fork–OpenMW Integration Architecture](architecture/integration-architecture.md)
- Updated: [OpenMW Capability Baseline](openmw/capabilities.md)
- Updated: [Fork and OpenMW Capability Matrix](architecture/capability-matrix.md)
- Source: [OpenMW Ecosystem Prior-Art Survey](../raw/openmw/2026-08-07-openmw-ecosystem-prior-art-survey.md)
- Decisions: combine DeltaPlugin with a thin declarative world-shell transformer;
  adapt Zdo RPG AI's file-in/log-out seam for the first Fork bridge; reuse
  official Lua and asset tooling; defer TES3MP to a separate multiplayer program.

## [2026-08-07] correction | First bridge does not require a native patch @ 6e7f7a9

- Corrected the earlier ARCH-001 claim that a native OpenMW patch is unavoidable.
- Evidence: Zdo RPG AI reads bounded JSON through OpenMW's VFS and emits tagged
  JSON through the OpenMW log for an external companion.
- Current decision: prove and measure hardened file/log IPC first; retain a
  native bridge only as conditional hardening.

## [2026-08-07] verify | Conservative water baseline stable @ unknown

- Updated: [OpenMW Display and Interface Scaling](openmw/display-and-interface.md)
- Source: [Water Recovery Verification](../raw/openmw/2026-08-07-openmw-water-recovery-verification.md)
- Result: world/exterior assets loaded, no new NVIDIA/OpenMW error event was
  observed, and OpenMW logged `Quitting peacefully`.

## [2026-08-07] update | NVIDIA OpenGL crash isolated from display scaling @ unknown

- Updated: [OpenMW Display and Interface Scaling](openmw/display-and-interface.md)
- Source: [NVIDIA Driver Crash Evidence](../raw/openmw/2026-08-07-openmw-nvidia-driver-crash.md)
- Diagnosis: `nvoglv64.dll` kernel exception, error 3/subcode 7, after enabling
  maximum water settings; no explicit OpenGL out-of-memory evidence.

## [2026-08-07] update | OpenMW 0.51 installed and UWQHD preset applied @ unknown

- Updated: [OpenMW Display and Interface Scaling](openmw/display-and-interface.md)
- Source: [Local Installation Verification](../raw/openmw/2026-08-07-openmw-local-install-verification.md)
- Observed the verified official install, GOG content configuration, exact
  3440x1440 preset application, and a responsive 0.51.0 engine process.

## [2026-08-07] ingest | OpenMW display and interface scaling @ unknown

- Added: [OpenMW Display and Interface Scaling](openmw/display-and-interface.md)
- Source: [Display and Interface Scaling Prior Art](../raw/openmw/2026-08-07-openmw-display-ui-scaling-prior-art.md)
- Decision: configure OpenMW 0.51 native video and GUI settings through a
  resolution-aware, reversible preset applicator; do not rewrite the UI.

## [2026-08-07] ingest | Fork/OpenMW capability and integration baseline @ 4cdd46b

- Added: [Fork Capability Baseline](fork/capabilities.md)
- Added: [OpenMW Capability Baseline](openmw/capabilities.md)
- Added: [Fork and OpenMW Capability Matrix](architecture/capability-matrix.md)
- Added: [Fork–OpenMW Integration Architecture](architecture/integration-architecture.md)
- Sources: [Fork Inventory](../raw/fork/2026-08-07-fork-build36-capability-inventory.md), [OpenMW Inventory](../raw/openmw/2026-08-07-openmw-0.51-capability-inventory.md), and [Integration Analysis](../raw/architecture/2026-08-07-fork-openmw-integration-analysis.md)

## [2026-08-07] lint | 0 issues found

- Verified the expanded article structure, index coverage, source provenance,
  metadata, and local Markdown links.

## [2026-08-07] ingest | Repository Continuity and Knowledge Workflow @ unknown

- Added: [Repository Continuity and Knowledge Workflow](workflow/repository-continuity-system.md)
- Source: [Repository Continuity and Knowledge System](../raw/workflow/2026-08-07-repository-continuity-system.md)

## [2026-08-07] lint | 0 issues found

- Verified article structure, index coverage, source provenance, metadata, and
  local Markdown links.
