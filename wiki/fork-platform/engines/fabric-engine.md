# Fabric Engine — what the system is doing

> Sources: [docs/FORK-OVERVIEW.md](../../docs/FORK-OVERVIEW.md) §11; agent-starter module source; [PRED-006 completion](../../docs/completions/COMP-PRED-006.md)
> Commit: 73fb27f6e
> Updated: 2026-07-24

## Overview

The Payload/Execution Fabric makes work-in-motion a first-class primitive: a
`Payload` is a by-reference envelope (`fabric_payload(dims, causal_event_base,
public_sidecars)` on a table) with a guarded lifecycle, a starvation-free
aging scheduler, CMT-traced transitions with control-plane **replay**, and a
primitive registry + deterministic planner + policy layer.

## Lifecycle

`submit → claim → start → settle`, with `fail` (retry arc back to Pending
until `max_attempts`, then DeadLetter), `cancel`, and `dead_letter`. Every
arc is validated (illegal transitions are typed errors), stamps its own CMT
event tag, and writes an audit transition row. **No-double-claim is
guaranteed** by the atomic guarded Pending→Claimed transition inside a
serializable reducer tx — first claimer wins, always.

## The by-reference composition (with Context)

`claim_work` in agent-starter shows the intended shape: claim the payload,
run fused recall over its intent, assemble a budgeted ContextFrame from the
hits, and attach it BY REFERENCE (`payload.context_id = Some(frame_id)`) —
all one transaction, all CMT-traced, so `frame().replay(...)` can
reconstruct exactly what the worker attended to.

## As a work bus

Because payload tables are subscribable, `submit_work` → every subscribed
worker sees the row instantly → first `claim_work` wins. A distributed job
queue with zero extra infrastructure (proven pattern: ai-collab-v3's payload
bus runs on exactly this).

For high-volume binary results, keep the lifecycle by-reference. PRED-001 uses
typed batch capsules for claims, retries, and receipts while packed vectors move
through bounded durable staging into the authoritative vector store. It avoids
one payload per vector and does not make Fabric a second vector database.

GPU worker names, capsule fields, device/resource tables, and public reducer
names remain application-owned. Two workers may subscribe to one queue and
race the same guarded claim, but resource admission should be checked and
reserved in the same guest reducer transaction as `claim(...)`. Build 20
provides native, generic leased resource reservations for that admission; it
does not interpret a reservation as VRAM or execute a GPU model. See
[Fabric GPU Worker Guest Integration](../vector/fabric-gpu-worker-integration.md)
for the application boundary and worked lifecycle, and
[Fabric Orchestration Platform](fabric-orchestration-platform.md) for the
native facilities.

## Predictive execution

Macro-opt-in payloads can use a predictive execution class with durable
Off/Shadow/Active controls, deterministic expected-value selection,
demand-first preemption, quarantine, and one exact atomic promotion gate. Only
capability-authorized pure/replayable/idempotent templates are eligible. CMT
records tags 50-59 without widening the historical persisted event enum. See
[Predictive Payload Speculation](predictive-payload-speculation.md) for the
signature, learning, vector-batch, rollback, and release contracts.

Starting with fork build 15, a fresh opt-in database seeds conservative Active
limits. Existing databases preserve their durable mode, while missing or
malformed configuration remains Off. Runtime Off/Shadow controls, deny rules,
emergency stop and automatic trip remain available without rebuilding.

## See Also

- [Context engine](context-engine.md)
- [Streaming and live events](../web/streaming-and-live-events.md)
- [Fabric-buffered dual-GPU vector ingestion](../vector/fabric-buffered-vector-ingestion.md)
- [Predictive payload speculation](predictive-payload-speculation.md)
- [Fabric GPU Worker Guest Integration](../vector/fabric-gpu-worker-integration.md)
- [Fabric Orchestration Platform](fabric-orchestration-platform.md)
