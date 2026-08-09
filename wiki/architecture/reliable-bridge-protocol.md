# Reliable OpenMW/Fork Bridge Protocol

> Sources: BRIDGE-003 research/implementation and DIALOGUE-001 save/replay evidence, 2026-08-09
> Raw: Prior-art research, BRIDGE-003 evidence, DIALOGUE-001 evidence
> Commit: 6e7f7a9
> Updated: 2026-08-09

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

- [Forkâ€“OpenMW Integration Architecture](integration-architecture.md)
- [Fork and OpenMW Capability Matrix](capability-matrix.md)
- [Production Living-World Gap-Closure Programme](system-gap-closure-programme.md)
