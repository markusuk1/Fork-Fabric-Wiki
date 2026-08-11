# Fabric Orchestration Platform

> Sources: [PRED-006 completion](../../docs/completions/COMP-PRED-006.md);
> [live behavioural re-audit](../../docs/audits/AUD-PRED-010.md);
> [build-27 runtime reconciliation](../../raw/operations/2026-07-24-build27-runtime-reconciliation.md)
> Commit: 73fb27f6e (completed build 20); carried by build 27 (`6e20ac8d9`)
> Updated: 2026-07-24

## What shipped

PRED-006 turns Fabric from a payload lifecycle into a reusable orchestration
substrate without making the host understand application work. An opt-in
`fabric_payload(orchestration)` schema provides five independent facilities:

1. **Leased resource reservations** — atomic capacity accounting, deterministic
   idempotency, bounded leases, renewal, release, and expiry.
2. **Work graphs** — bounded DAG registration and dependency-aware ready-node
   selection with retry/terminal receipts.
3. **Scheduling** — deterministic deadline, quality, cost, and locality scoring
   over a bounded candidate set.
4. **Chunked artifacts** — content-addressed staging, bounded chunks and bytes,
   completeness/digest verification, and idempotent commit.
5. **Federation** — permission-bound envelopes with bounded hop counts and
   bodies, replay protection, and deterministic forwarding decisions.

All facilities are generic. A `ResourceRequest` may represent VRAM, CPU,
bandwidth, a model slot, or another application-defined unit, but Fork never
guesses which. Work graphs order opaque application work; scheduling ranks
declared evidence; artifact storage does not become a vector database; and
federation does not create a transport daemon.

## Control model

The unified `[fabric-orchestration]` host configuration is **deny-only**.
Durable database configuration opts into facilities and sets its own policy;
the host may impose stricter limits or stop local orchestration, but cannot
grant a database capability. Effective permission is therefore:

```text
database allows ∩ host allows ∩ emergency stop is clear
```

Resource reservations, work graphs, scheduling, chunked artifacts, and
federation each have an independent toggle. Typed host ceilings bound active
reservations and units, lease duration, graph nodes/edges/depth, scheduling
candidates, artifact/chunk size and count, and federation hops/body bytes.
`/v1/status` exposes configured and effective values. On 2026-07-24 the local
build-27 AI-Collab node reported all five facilities configured/effective true
and emergency stop false.

## Transaction and retry guarantees

Reservation admission and a guarded Fabric claim can occur in one serializable
guest reducer transaction, eliminating the detached “check capacity, then
claim” overcommit race. Reservation retries use a caller-supplied idempotency
key. Re-reserving an expired key renews its row in place rather than inserting a
duplicate.

A work-node receipt means that node is terminal. A retryable failure returns
the node to Pending **without** writing a receipt; only terminal success or
failure writes one. The build-20 live drill proved claim → retryable fail →
re-claim → terminal fail, plus renew-after-expiry with one reservation row.
This corrected an incomplete build-19 implementation that returned a node to
Pending while simultaneously making it ineligible through a premature receipt.

## Release and compatibility

The complete platform shipped in fork build 20
(`v2.7.0-pred006-r3`, `73fb27f6e`) after the live re-audit reached zero
Critical/High/Medium findings. PRED-006 raises the guest ABI from 10.11 to
10.12. Rollback from a database that has published an ABI-10.12 module is a
**full-stage rollback**—compatible host, data, and module—not an old-host-only
binary swap.

Build 27 carries PRED-006 and fixes a separate migration defect in the
`fabric_payload(speculation)` conditional columns: they now append after all
unconditional columns and have defaults, so an existing non-speculation table
can enable speculation without renumbering resident columns or deleting data.

## Boundaries

Fork does not execute Qwen or other models, pool VRAM across GPUs, migrate live
CUDA allocations, select an AI-Collab worker profile, or open cross-node
sockets. Those are deployment/application responsibilities. Fork supplies the
transactional reservations, dependency graph, scheduling evidence, bounded
artifact transfer, federation policy, and replayable receipts that make those
systems safe to coordinate.

## See Also

- [Fabric Engine](fabric-engine.md)
- [Host Configuration Management](../operations/host-configuration-management.md)
- [Fabric GPU Worker Guest Integration](../vector/fabric-gpu-worker-integration.md)
- [Staged Builds & Guest/Host Compatibility](../operations/staged-builds-and-compat.md)
