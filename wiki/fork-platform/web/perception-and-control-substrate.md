# Perception and Control Substrate

> Sources: `crates/perception`; `modules/perception-demo`; PER-001 release evidence; PER-002 GPU/driver architecture
> Updated: 2026-07-14

## Boundary

Fork supplies deterministic, model-free contracts for a real-time perception
producer to commit compact observations, canonical state changes, semantic
events, actions, expectations, and verification. Capture, inference, tracking,
fusion policy, ontology, raw-media storage, and device execution remain in the
consumer or its sidecars. The database never loads a visual model.

`spacetimedb-perception` is a WASM-safe guest crate with no network, model, GPU,
or device dependencies. `perception-demo` is an executable reference module,
not a prescribed visual schema.

## Ordered tick contract

One `PerceptionBatch` is one source tick and one reducer transaction. Its
ordering authority is `(source_id, epoch, sequence)`; timestamps are provenance
only. Strictly newer positions apply, filtered sequence gaps are legal, and an
exact retry at the same position is a no-op. A stale or divergent retry fails
before the module writes anything.

The batch retains observation order through an explicit ordinal. Default
limits are 256 observations, 256 canonical updates, 256 semantic events, 4 KiB
per evidence reference, and 1 MiB total compact payload. Regions, confidence,
duplicates, and byte limits are validated before writes. Raw frames belong in
an external content-addressed store; hot rows carry references.

Canonical entity revisions advance only when the state kind or bytes change.
Semantic events are emitted only for those meaningful changes, and the
reference module records CMT tag 45 for the change.

## Action loop

The reference action table uses the existing `fabric_payload` lifecycle. An
action submission and its pending expectation commit together. Verification is
accepted only for the expectation's source and a source cursor already covered
by the durable checkpoint. Outcomes are terminal and idempotent:

- satisfied -> Fabric `Done`;
- violated or expired -> Fabric `DeadLetter`;
- cancelled -> Fabric `Cancelled`.

CMT tags 46-49 identify those outcomes. Action, predicate, and evidence bodies
are bounded opaque references; execution remains external.

## Subscription delivery guarantees

The per-connection message-to-encoder queue is bounded at 256 messages and the
encoded-frame queue at 32 frames. Existing client output remains bounded at
16,384 messages with slow-client disconnect. Ping and close retain biased
priority, close drains the current complete message, ordering is preserved, and
encoder exit or execution-error overflow closes cleanly instead of hanging or
growing memory.

Queue length, high-water, and enqueue-wait metrics are exported with static
stage labels. The database-global subscription send-worker queue remains
lossless and non-blocking because it is fed while the datastore lock is held;
its depth is now complemented by high-water and queue-age metrics. This avoids
moving backpressure into transaction commit.

## GPU/driver producer mapping

DXGI Desktop Duplication, PresentMon/ETW, NVML (or vendor equivalents),
hardware optical flow, capture recovery, visual inference, and input execution
belong to an external producer/runtime. Vendor SDKs and device handles do not
enter the database engine or WASM ABI. GPU-shaped tables are consumer/module
schemas, not Fork system tables.

The 30-120 Hz reflex loop stays local and is limited to a pre-authorised bounded
policy envelope. Meaningful observations, threshold crossings, keyframe
boundaries, actions, and verification evidence are committed through the
PER-001 contract. Tactical and strategic actions continue through Fabric. This
keeps Fork out of the frame-time critical path while retaining a durable causal
record.

Timing values must retain their clock domain and calibration. DXGI presentation
ticks cannot be directly subtracted from Unix/database timestamps. Producers
record raw ticks, frequency, calibration generation, and uncertainty; an
uncorrelated value is unavailable, not zero. Optional telemetry likewise has
explicit validity such as valid, unsupported, permission-denied, stale, or
estimated. NVML samples are not assumed to be per-frame, and normalized
optical-flow confidence is recorded as producer-derived.

Capture recreation or access loss advances `SourceCursor.epoch` and requires a
keyframe. Accumulated/missed frames and coalesced rectangles are loss signals;
move rectangles apply before dirty rectangles; protected/masked content is
incomplete evidence. Raw frames, dense vector fields, and high-rate samples
stay external or in bounded memory. Fork normally persists aggregates,
threshold crossings, discontinuities, semantic changes, and evidence refs.

The released primitives already cover the first sidecar implementation. New
guest types for capability snapshots or clock calibration should be promoted
only after a real producer proves a stable vendor-neutral shape.

## Release evidence

Fork build 7 is staged in `v2.7.0-per001` from commit `008bee2f1` with CUDA and
the exact `perception_demo.wasm`. Unit suites passed for the primitive crate,
reference module, client API, and core. The live module preserved its source
cursor and canonical revision through non-destructive publication, build-7
restart, rollback to build 6, and restoration to build 7. The first post-restore
Fabric action succeeded. An exact duplicate returned 200 as a no-op; a stale
tick returned a well-formed 530 in 4 ms.

The local PER-001 validation evidence was produced on build 7. The separate LAN
NSSM service has since advanced to successor build 9, which contains PER-001's
host changes; its existing guest module remains unchanged.

## See also

- [Streaming & Live Events](streaming-and-live-events.md)
- [Fabric Engine](../engines/fabric-engine.md)
- [Staged Builds & Compatibility](../operations/staged-builds-and-compat.md)
- [Auto-Increment Sequence Recovery](../operations/sequence-recovery.md)
