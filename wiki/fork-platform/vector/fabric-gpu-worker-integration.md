# Fabric GPU Worker Guest Integration

> Sources: [AI-Collab GPU scheduling guest-integration request](../../raw/vector/2026-07-16-ai-collab-gpu-scheduling-guest-integration.md); [PRED-006 completion](../../docs/completions/COMP-PRED-006.md); [build-27 runtime reconciliation](../../raw/operations/2026-07-24-build27-runtime-reconciliation.md)
> Raw: [Fabric-buffered dual-GPU vector ingestion concept](../../raw/vector/2026-07-14-fabric-buffered-dual-gpu-vector-ingestion.md); [PRED-001 as-built evidence](../../raw/vector/2026-07-15-pred001-as-built-predictive-fabric.md)
> Commit: 73fb27f6e; carried by build 27 (`6e20ac8d9`)
> Updated: 2026-07-24
> Status: Guest integration contract confirmed; generic native leased reservations shipped in build 20

## Overview

Fork build 20 and later provide the complete integration substrate for two
same-host GPU workers over one demanded-first Fabric queue. Fork supplies the generic durable
payload lifecycle, atomic claim, demand-first speculative cancellation, packed
vector staging and idempotent commit substrate, generic leased resource
reservations, subscriptions, replay, and metrics. AI-Collab supplies the
application schemas, CUDA/model execution, placement policy, and authoritative
vector-store writes.

There is no native VRAM pooling. The GTX 1660 Ti and RTX 3080 remain separate
allocation domains, and a worker must reserve capacity on one explicit device.

## Contract Names and Build Boundary

The identifiers `vector.embed.batch/v1`, `vector.store.commit/v1`, and
`vector.rerank.batch/v1` are **application-level `payload_type` conventions**.
They are not three generated Rust types or three host APIs shipped by a
particular Fork build. A guest defines the fields carried by each type and
publishes reducers around the generated Fabric table extension methods.

| Surface | First proven Fork release | Current meaning |
|---|---:|---|
| Generic `fabric_payload` lifecycle | Earlier Fabric releases | `submit`, atomic `claim`, optional `start`, `settle`, `fail` with retry/dead-letter, `cancel`, TTL aging, transition audit, and CMT evidence |
| Generic leased resource reservation | Build 20 (PRED-006) | Atomic application-defined capacity admission, renewal, release, expiry, and idempotent retry; units may represent VRAM but Fork does not interpret them |
| `FVBATCH1` packed vectors and `application/vnd.fork.vector-batch.v1` | Build 14 (PRED-001) | Bounded binary decode/validation, content-addressed staging, and idempotent atomic vector receipt/commit behavior remain available |
| `vector.embed.batch/v1` | Application-owned name | Typed references for repository revision, chunks, model/profile, dimensions, eligibility, priority, and retry policy |
| `vector.store.commit/v1` | Application-owned name | Reference to an accepted staged batch and expected commit manifest; the authoritative AI-Collab sink remains application-owned |
| `vector.rerank.batch/v1` | Application-owned name | Whole-candidate-set work; adaptive-context releases supply fixed rerank calibration evidence, but Fork does not generate a binding for this payload name |

Documentation and code must not say that Fork ships three named native
contracts. It ships the generic substrate on which those versioned application
contracts are built.

## One Queue, Two GPU Workers

Define one public application work table with `fabric_payload(...,
public_sidecars, speculation)` and application fields for the typed capsule.
Register both workers for the same embedding payload type and model contract.
Each worker subscribes to relevant pending rows and invokes the same guest claim
reducer with its stable worker/device identity.

The reducer performs these steps in one serializable transaction:

1. Validate that the worker is enabled and capability-authorized for the
   payload type, model profile, modality, and dimensions.
2. Check and reserve the worker's declared VRAM/slot pool with the generic
   leased reservation primitive.
3. Select pending demanded work first. Only when the demanded queue is empty
   and policy permits it may the worker select speculative work.
4. Call the generated `claim(...)` extension method. Its guarded
   `Pending -> Claimed` transition is atomic, so concurrent 1660 Ti and 3080
   claims cannot both win the same row.
5. Return the claimed row and claim token to the winning worker.

Public reducer names such as `submit_gpu_work` or `claim_gpu_work` are chosen by
AI-Collab; they are not host endpoints fixed by Fork. The generated lifecycle
verbs are invoked inside those reducers.

After claiming, the worker calls a guest reducer that performs
`Claimed -> Running`. On success it durably stages and commits the result before
`Running -> Done`. On a retryable failure, `fail(...)` records
`Running -> Failed` and then either returns the row to `Pending` with its claim
token cleared or moves it to `DeadLetter` when `max_attempts` is exhausted.
Expired pending work is dead-lettered by aging. Application resource
reservations must be released idempotently on settle, failure, cancellation,
dead-letter, and repair after worker loss.

The faster GPU naturally claims more work. No repository split or VRAM pooling
is required, and using identical profiles keeps embedding outputs compatible.

## Pre-emption Signal

The recommended signal is a subscription to the claimed public payload row.
Predictive maintenance changes a claimed or running speculative
payload to `Cancelled` when demanded work, policy invalidation, or a kill switch
pre-empts it. A pending or failed speculative payload is moved to `DeadLetter`.
Those base-row changes are subscription-visible; workers do not need access to
the private speculative-artifact sidecar.

For each active job, the worker should watch `payload_id`, `status`,
`claim_token`, and `updated_at` and maintain a local cancellation token. On a
`Cancelled` status, terminal status, or claim-token mismatch, it stops before
launching the next microbatch and releases its guest reservation through an
idempotent reducer. A CUDA kernel already in flight is cooperative-cancellation
granularity: Fabric cannot interrupt a kernel that the external process has
already launched.

Subscriptions are the normal push path. After disconnect/reconnect, re-read the
authoritative row before continuing. Bounded polling at microbatch boundaries is
the fallback, and TTL is only a final safety net, not the primary pre-emption
signal.

## Resource-Aware Admission

Build 20 exposes generic leased resource reservations. The application declares
the pool, unit scale, capacity, lease, and idempotency key; Fork deliberately
does not hard-code a `reservation_bytes` field or claim that units are VRAM.
The guest claim reducer combines reservation admission and Fabric `claim(...)`
in one serializable database transaction.

Do not check VRAM only in the external process and claim in a later call; two
workers or concurrent slots could pass that check and overcommit before either
reservation is visible. Record at least stable device UUID, worker/profile,
capacity, currently reserved bytes, safety headroom, active slot count, lease
expiry/heartbeat, and the claimed payload ID. Use the worker's measured free
capacity for scheduling, not the sum of both cards.

Use bounded leases and idempotent renewal/release so worker loss does not strand
capacity. Re-open GPU-specific native semantics only if measurements show the
generic reservation contract is insufficient; do not duplicate the generic
facility with a second VRAM-only ledger.

## Placement Inputs

`SpeculationMetricKind::QueueDepth` is a durable predictive-maintenance sample
for the speculative artifact set. It is not a per-`payload_type`, per-GPU queue
age service.

For exact demanded queue depth, use the public application payload table. With
illustrative table name `gpu_work`, the supported SQL shape is:

```sql
SELECT COUNT(*) AS depth
FROM gpu_work
WHERE status = 0
  AND execution_class = 0
  AND payload_type = 'vector.embed.batch/v1'
```

`status = 0` is `Pending`; `execution_class = 0` is `Demanded`. Use generated
bindings/enums rather than numeric literals in application code.

For oldest age, do not treat `LIMIT 1` as oldest: the SQL surface does not make
that ordering guarantee. Either compute the minimum `created_at` from the
subscribed pending set or maintain a guest queue-stat row transactionally on
submit, claim, retry, cancellation, and expiry. Placement can then use
`now - oldest_pending_created_at` together with depth, worker load/swap cost,
inference cost, and deadline risk without adding a synchronous scan to every
claim.

## See Also

- [Fabric-Buffered Dual-GPU Vector Ingestion](fabric-buffered-vector-ingestion.md)
- [Fabric Engine](../engines/fabric-engine.md)
- [Predictive Payload Speculation](../engines/predictive-payload-speculation.md)
- [Host Configuration Management](../operations/host-configuration-management.md)
- [Fabric Orchestration Platform](../engines/fabric-orchestration-platform.md)
- [Inbound AI-Collab GPU Scheduling Request](../requests/2026-07-16-ai-collab-gpu-scheduling-guest-integration.md)
