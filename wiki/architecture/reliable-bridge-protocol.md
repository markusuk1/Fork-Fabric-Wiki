# Reliable OpenMW/Fork Bridge Protocol

> Sources: BRIDGE-003 research/implementation, dialogue save/replay evidence, PLAYER-001 compatibility/stability corrections and DIALOGUE-002 restored-save/projection corrections, 2026-08-09 to 2026-08-10
> Raw: [Prior-art research](../../raw/architecture/2026-08-09-bridge-003-reliable-transport-research.md), [BRIDGE-003 evidence](../../raw/architecture/2026-08-09-bridge-003-reliable-transport-evidence.md), [DIALOGUE-001 evidence](../../raw/architecture/2026-08-09-dialogue-001-contextual-state-evidence.md), [Large-Epoch ACK Correction](../../raw/architecture/2026-08-09-player-001-large-epoch-ack-research.md), [Atomic Status Stability](../../raw/architecture/2026-08-09-player-001-atomic-status-read-stability-research.md), [Restored-Save Presentation Research](../../raw/architecture/2026-08-10-dialogue-002-stale-presentation-obsolete-research.md), [Atomic Snapshot Sharing Research](../../raw/architecture/2026-08-10-dialogue-002-atomic-snapshot-sharing-research.md), [Final Qualification](../../raw/architecture/2026-08-10-dialogue-002-final-qualification-evidence.md)
> Commit: c0cea44
> Updated: 2026-08-10

## Guarantee

Protocol 6 provides at-least-once transport and effectively-once accepted game
effects. It does not claim impossible cross-process exactly-once delivery.
Stable producer identity, durable local capture and one Fork reducer
transaction make replay safe.

```text
OpenMW bounded outbox
  -> stable tagged envelope / resend
  -> companion capture-before-cursor journal
  -> ordered retry or terminal dead letter
  -> atomic Fork checkpoint + receipt + domain effect
  -> bounded VFS acknowledgement
  -> OpenMW removes acknowledged outbox item
```

## Envelope and stream identity

Existing domain schemas 1 through 5 remain under `data`. The outer envelope is
CloudEvents-shaped and carries `specversion`, stable `id`, `source`, `type`,
`dataschema`, `game_protocol = 6`, world/save/session lineage, source epoch and
sequence. The message ID is fixed as
`msg:{session_id}:{source_epoch}:{sequence}`.

Fork stream identity combines world, save lineage, session, source and epoch.
Each stream begins at sequence one and must remain contiguous. The companion
also checks that envelope and payload world/session/sequence/type/schema agree.

## Commit and replay rules

- The companion stores or diagnostically classifies each tagged record before
  moving its log cursor past that line.
- Fork validates and inserts `bridge_source` and `bridge_message` in the same
  reducer transaction as the domain mutation.
- An exact message replay returns successfully without repeating the domain
  reducer. Reused identity with different content fails.
- Fully valid but superseded physical input can terminate as `obsolete` when a
  reducer has an explicit audit contract. Restored-save player-presentation
  baselines use this path: strict direct stale writes still fail, the bridge
  retains the envelope plus one immutable obsolete audit, and current state is
  not rewound.
- A gap remains pending. Later messages cannot overtake the missing sequence.
- Connectivity failures remain pending with bounded backoff. Deterministic
  invalid data is retried to the configured limit, then
  `dead_letter_bridge_message` atomically records the failure and advances the
  stream so subsequent valid work can continue.
- An acknowledgement means Fork committed either the applied effect or an
  intentional terminal dead letter.

## Bounds and diagnostics

The OpenMW outbox is capped at 256 messages and is serialized with Lua save
state. The companion journal caps pending work, recent acknowledgements and
dead-letter diagnostics; log reads are bounded to 1 MiB. When pending is full,
the byte cursor stops before the first uncaptured event, so backpressure cannot
become silent loss.

`status.json` reports protocol compatibility, online reason, pending/retry/
dead-letter counts and last progress. `bridge-acks.json` reports stream high
watermarks and recent terminal acknowledgements. Each acknowledgement stores
the SHA-256 digest of the accepted tagged line. A legacy acknowledgement without
a digest is resubmitted to Fork for canonical replay/divergence validation;
identity alone cannot silently discard content. Protocol generations use
separate VFS roots and mutex names to prevent stale companions from masquerading
as current ones.

ACK projection schema 2 represents 64-bit source epochs/high watermarks as
decimal strings because OpenMW 0.51 markup integer scalars are C++ `int`-bound;
Lua normalizes them with `tonumber`, while companion/Fork authority remains
int64. For `status.json`, OpenMW reuses only the last fully validated snapshot
for at most one second during an atomic replacement gap. A valid offline status
is applied immediately, and persistent absence still becomes offline.

Companion JSON projections use same-directory temporary files and atomic
replacement. OpenMW can briefly hold a reader handle without delete sharing;
the writer therefore retries only `IOException` with bounded backoff. It never
falls back to truncate-in-place or delete-then-move, and non-I/O or exhausted
failures still make bridge health fail closed.

## Failure behavior

- Fork offline: OpenMW remains responsive with ordinary local AI; the bounded
  outbox retains semantic work and the UI reports the brain as offline.
- Companion crash after Fork commit: the unacknowledged stable event is resent;
  Fork returns an exact no-op and the replacement companion publishes the ack.
- Companion restart: pending journal entries replay before later work.
- Fork process restart: durable checkpoint, receipt and domain rows survive;
  exact replay remains a no-op.
- Log truncation or replacement: the companion resets the byte cursor when
  length regresses or file creation identity changes.
- Save/load: pending messages retain their original epoch, while restored
  producers create a fresh monotonic epoch and restart per-epoch sequencing.
  Acknowledgement high-watermarks are applied only within the matching epoch,
  preventing post-save messages from colliding with restored sequence numbers.

## Ownership

OpenMW owns event creation, physical effects and verification receipts. The
companion owns local capture, retry and VFS projection. Fork owns durable
ordering, terminal classification and domain transactions. No credentials enter
Lua, no frame-time controls cross the bridge and no broker or engine patch is
required.

## See Also

- [Fork–OpenMW Integration Architecture](integration-architecture.md)
- [Fork and OpenMW Capability Matrix](capability-matrix.md)
- [Production Living-World Gap-Closure Programme](system-gap-closure-programme.md)
