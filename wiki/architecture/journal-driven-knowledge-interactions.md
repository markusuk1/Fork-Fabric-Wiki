# General Player Journal and Knowledge Interactions

> Sources: OpenMW 0.51/API-129 native-surface inspection and INTERACT-001 through JOURNAL-002 implementation/runtime evidence, 2026-08-09
> Raw: JOURNAL-002 research, JOURNAL-002 evidence, INTERACT-001 evidence, INTERACT-003 evidence, COMMERCE-001 evidence
> Commit: unknown
> Updated: 2026-08-09

## Delivered boundary

JOURNAL-002 replaces the shipment-only topic list with a general, revisioned
player knowledge ledger. Fork owns current entries, immutable versions,
acquisition/correction event identities, typed claims, source/evidence
provenance, confidence/detail and discussion eligibility. OpenMW owns physical
read/sight/hearing evidence, native book and quest-journal surfaces, player save
state and the clickable project UI. The companion validates schema-9 events and
projects a bounded atomic snapshot.

The clean playable Fork target is `game-openmw-npc-v57`; the matching default
data generation is `village-lab-data-journal11`.

## Acquisition and correction contract

Five source kinds enter the same table:

| Source | Required evidence | Confidence ownership |
|---|---|---|
| `read` | Same cell, collision-ray visibility and at most 250 units | Fork derives from reasoning |
| `witnessed` | Same cell, visible, within 1,800 units and signal at least 250 | Fork combines signal, perception and reasoning |
| `heard` | A known dispatched/delivered SOCIAL-001 message, same cell, within 900 units and signal at least 200 | Fork replaces client text/claim with the canonical utterance before scoring |
| `conversation` | An actual interaction with a role-qualified speaker | Fork creates the authoritative entry inside the interaction reducer |
| `native_import` | A new OpenMW player-journal text entry | Fork assigns authoritative imported detail; OpenMW polls in batches of eight |

An acquisition event is exactly replayable. Reusing its event ID with different
data or reusing an entry ID for another acquisition fails. A correction requires
the current expected revision, appends an immutable version/event and atomically
updates summary, confidence, status and discussability. Retraction retains
history but cannot be selected as current knowledge.

`player_journal_entry`, `player_journal_version` and `player_journal_event` are
authoritative. A compatibility `knowledge_entry` projection remains for older
interaction consumers; it is not the correction ledger.

## OpenMW collection and persistence

The player-local script performs collision-ray and range checks for physical
sources and reads Intelligence/Luck-derived perception/reasoning. It receives
actual SOCIAL-001 utterances, relevant attack/death/crime candidates and native
notice activation. Acquired source identities are saved by OpenMW so load does
not invent duplicates; the set is hard-bounded at 1,024.

Native quest text is inspected through `types.Player.journal(self)` every two
seconds, at most eight new entries per poll. The imported index and source set
survive save/load. Native journal ownership remains OpenMW's: the project UI
offers an explicit `Open native quest journal` action instead of replacing it.

## Project UI and deliberate disclosure

Shift+J opens the project journal. It displays source/category/status filters,
five entries per page, confidence, current revision and correction-history
count; detail view shows the typed claim and recent correction reasons.

Conversation no longer embeds six arbitrary topic buttons. `Browse journal
topics` opens the same journal in selection mode. The selected token is
`entry_id~rN`; Fork revalidates holder, current status, discussability and exact
revision before creating the recipient's lower-confidence
`player_disclosure`. Stale, retracted or foreign knowledge is rejected.

The custom tree uses OpenMW's `Windows` layer and intentional `Interface` mode.
At the JSON/event boundary, discussability rejects only explicit `false`; exact
identity with Lua's boolean singleton is not assumed.

## Bounds and failure behavior

- Companion snapshot: at most 128 current entries and 512 versions.
- OpenMW saved acquisition identities: at most 1,024.
- Native import: eight entries per two-second poll.
- UI: five matching entries per page; filters never mutate knowledge.
- Offline or malformed events create no Fork knowledge.
- Unspoken social proposals cannot become heard facts.
- Capture-before-cursor transport, explicit acknowledgements and atomic reducers
  make reconnect/replay safe; save and Fork process restart retain history.

## Observed evidence

Direct proof covers ten entries across all five source kinds, exact/divergent
replay, physical and unspoken rejection, correction/retraction and stale/current
topic selection. Real OpenMW proof covers native notice activation,
witnessing/hearing, visible selection UI, 6/6 save/load identity retention and
zero pending/dead-letter/engine/Lua errors. Companion crash recovery preserves
the physical facts; an isolated Fork stop/start preserves one entry, two
versions and two events. See
the private `COMP-JOURNAL-002` completion record.

## Related

- [Embodied Social Information Flow](embodied-social-information-flow.md)
- [Epistemic Memory and Bounded Recall](epistemic-memory-and-bounded-recall.md)
- [Living Village, Commerce, Conversation and Voice](living-village-commerce-dialogue-voice.md)
- [Forkâ€“OpenMW Integration Architecture](integration-architecture.md)
- [Replacement World-Shell Pipeline](../openmw/world-shell-pipeline.md)
