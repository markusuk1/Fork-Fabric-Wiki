# Player Identity and Save Lineage

> Sources: PLAYER-001 OpenMW/Fork prior-art, implementation, recovery, and native production evidence, 2026-08-09
> Raw: [Character Bootstrap Prior Art](../../raw/architecture/2026-08-09-player-001-character-bootstrap-prior-art.md), [Persistence Harness Evidence](../../raw/architecture/2026-08-09-player-001-persistence-harness-evidence.md), [Interactive Proof Recovery](../../raw/architecture/2026-08-09-player-001-interactive-proof-recovery-research.md), [Large-Epoch ACK Correction](../../raw/architecture/2026-08-09-player-001-large-epoch-ack-research.md), [Atomic Status Stability](../../raw/architecture/2026-08-09-player-001-atomic-status-read-stability-research.md), [Native Character and Production Evidence](../../raw/architecture/2026-08-09-player-001-native-character-production-evidence.md), [GPU-Backed Capture Correction](../../raw/openmw/2026-08-09-player-001-wgc-capture-correction.md)
> Commit: c0cea44
> Updated: 2026-08-09

## Status

PLAYER-001 is **released locally and production-qualified**. The user completed
all five native screens once and created Delantris (Imperial Crusader,
Trollkin). The exact opaque identity, lineage, and native fingerprint survived
native save/load, a new OpenMW process, a new companion process, and a real Fork
restart. The current-schema dialogue, journal, perception, reputation,
commerce, cognition, routine, and voice matrix passes in isolated servers.
PLAYER-001 was qualified on Fork v178; the ordinary launcher now auto-resumes
the same promoted save against consolidated production v203.

## Ownership

OpenMW owns the character's native physical and gameplay record: display name,
race, class, birthsign, sex, head and hair. The clean world's generated `Main`
script presents OpenMW's Name, Race, Class, Birthsign and Review screens in
order, waiting for each menu to close before advancing.

The project identity layer owns two opaque durable identifiers:

- `player:ow-<32 lowercase hex>` identifies the project player;
- `save:ow-<same 32 lowercase hex>:v1` identifies its initial save lineage.

Fork owns the current identity projection, immutable identity revisions,
lineage lifecycle and migration provenance. The companion owns the durable
binding between one running bridge state and its first accepted opaque
player/lineage pair.

## Bootstrap lifecycle

1. The clean `Main` script presents all native creation screens and sets
   `projectStage` to `-1` only after Review closes.
2. OpenMW Lua reads the resulting native record and birthsign, computes a
   deterministic fingerprint of those confirmed values, generates one opaque
   token and persists the identity in the OpenMW save state.
3. Player controls remain locked while the protocol-6
   `player_identity_bootstrap` event is pending.
4. The companion validates the envelope and payload lineage, adopts the first
   valid binding, and invokes the atomic Fork reducer.
5. Fork creates or exactly replays the player, lineage and immutable revision.
   Divergent reuse is rejected transactionally.
6. The companion writes the matching ACK. OpenMW releases controls and only
   then allows ordinary living-world processing.

The bootstrap envelope uses the opaque lineage immediately; it does not inherit
the legacy baseline envelope. Session identity is initialized before the first
update, so neither payload nor envelope may contain a `nil` session.

## Persistence and restart behavior

The OpenMW global save record contains the identity, lineage, native
fingerprint, bridge outbox and source epoch. Loading validates schema, content,
identifier form and current native fingerprint before re-emitting the same
identity in a fresh transport epoch. Exact replay is a no-op at the immutable
identity layer.

The companion keeps 64-bit source epochs internally and on the Fork wire, but
projects them as decimal strings in ACK schema 2 because OpenMW 0.51 markup
integer scalars are C++ `int`-bounded. Lua accepts ACK schemas 1 and 2 and
normalizes the epoch/high-watermark values with `tonumber`.

The ACK-gated test path sends a supported OpenMW menu event only after identity
readiness, then uses native `saveGame` and `loadGame`. The full live harness is
split deliberately:

- first process: explicit user consent, native character creation, bootstrap
  ACK and in-process save/load;
- second process: background `--load-savegame`, companion restart, exact
  identity/fingerprint comparison and non-intrusive visual capture.

An isolated real Fork restart retained byte-identical player, lineage and
revision state, preserved exact replay idempotence, and continued to reject
divergent reuse. A current-module regression matrix also bootstraps a valid
opaque player independently before every affected subsystem proof, avoiding
false confidence from obsolete historical database schemas.

## Production launch

The generated `current-player.json` manifest binds the production explorer
profile to the current Fork database (`game-openmw-npc-v203` at this
reconciliation) and to the promoted Delantris save. With the ordinary desktop
launcher, the newest save under the production
user-data root is loaded automatically. `-NewCharacter` explicitly bypasses
auto-resume; an explicit `-LoadSavegame` remains mutually exclusive with it.

Promotion is performed by `scripts/promote-player-identity.ps1`. It validates
the retained native logs, current Fork identity/lineage/revision, and save hash;
then archives the previous generated explorer state and writes the promoted
save and manifest. Generated saves/manifests remain local-only and untracked.

## Migration and failure behavior

A pre-identity save with no unacknowledged baseline outbox can migrate once as
`legacy_baseline`. Its lineage records the exact source
`player:explorer`/`save:explorer:baseline-v1`; it does not claim that historical
baseline semantic events belong to the new opaque identity.

Migration fails closed if baseline events are still pending. Unsupported
bootstrap schema/content, malformed IDs, native fingerprint drift, false legacy
provenance and companion rebinding all block progress without committing domain
state. OpenMW keeps controls locked and presents a visible reason.

## Visual-proof safety

Only the user-consented creation phase may present OpenMW in the foreground.
Automated restart capture uses an isolated bottom-stacked window and the exact
HWND `Windows.Graphics.Capture` path. Startup, wait, and capture gates require
that OpenMW itself never owns the foreground; unrelated user app switches are
allowed. There is no activation, `PrintWindow`, foreground-restoration or
visible-desktop-copy fallback.

The companion status snapshot remains atomically replaced. OpenMW caches only
the last fully validated status for a one-second read grace, preventing a
millisecond replacement gap from creating false offline/online edges. A valid
offline document is immediate, and persistent absence still fails closed after
the grace.

## See Also

- [Fork–OpenMW Integration Architecture](integration-architecture.md)
- [Reliable OpenMW/Fork Bridge Protocol](reliable-bridge-protocol.md)
- [Replacement World-Shell Pipeline](../openmw/world-shell-pipeline.md)
- [OpenMW Display and Interface Scaling](../openmw/display-and-interface.md)
