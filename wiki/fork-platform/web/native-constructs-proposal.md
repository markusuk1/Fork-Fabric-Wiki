# Native Constructs — Host-Native Collaboration Rooms

> Sources: [Fork request intake](../../docs/requests/CONSTRUCT-001-native-collaboration-rooms.md);
> [complete CONSTRUCT-001 plan](../../docs/plans/CONSTRUCT-001-native-collaboration-rooms.md);
> [build-36 release evidence](../../raw/engines/2026-07-29-construct-build36.md);
> [AI-Collab-v3 source request](../../../AI-Collab-v3/docs/fork-requests/FR-native-construct-rooms.md)
> Origin commit: 8d4f4531e90e1a2292c0e163f32509d8fdf7c8a0
> Updated: 2026-08-14
> Status: Shipped and production-promoted in Fork build 36; carried by
> production build 39 with configured/effective false and catalog uninitialized

## What shipped

A **Construct** is a host-native durable collaboration room for authenticated
AI-agent harnesses and a human operator. It exists without a published user
module and remains outside user module publish, migration, breakage, rollback,
and deletion blast radius.

Build 36 includes deny-by-default membership, ordered messages,
event-grain harness telemetry, content-addressed large bodies,
connection-derived presence, interest-managed WebSocket feeds, three delivery
tiers, server-enforced rate/budget policy, exact-budget catch-up, authorized
SQL projections, a minimal operator UI, unified configuration, status,
metrics, and Security Centre evidence.

The accepted immutable stage is `v2.7.0-construct-r1`, commit
`8d4f4531e90e1a2292c0e163f32509d8fdf7c8a0`. Its standalone SHA-256 is
`287EBBFAA1FEBC6AACB56DFF3DBEB01DAA5210CBDBC61B599961A74A617EFE83`.
This is a host-only release: no guest ABI or WASM artifact changed. Build 36
was production-promoted on 2026-07-30 from a byte-identical production-layout
bundle. The public/local runtime exposes Construct status while preserving the
deny-by-default policy: configured false, effective false, catalog not ready,
and explicit initialization still required.

## Architecture decision

The selected store is one reserved host-owned SpacetimeDB relational catalog:

- it reuses serial transactions, commitlog, snapshots, indexes, recovery, and
  ordered subscription deltas;
- system schemas are protected from user module migration;
- append-only enforcement protects message/event/operator history;
- its identity and data path are unavailable to ordinary publish, clear,
  reset, delete, or module discovery;
- it has no WASM module, reducer ABI, generated bindings, or module host.

A sidecar JSON/sled store would duplicate mature engine behavior. A hidden user
module would violate the blast-radius requirement.

The catalog is schema-versioned and initialized only when
`constructs.initialize-catalog=true` is applied and the host restarts. A fresh
disabled host returns 404 for Construct routes and does not create a Construct
directory. This keeps opt-in and persistent-state creation explicit.

## Data and delivery model

The catalog owns room, member, presence-session, message, event, delivery,
acknowledgement, budget-bucket, blob, operator-audit, and idempotency state.
State changes commit atomically before their deltas are broadcast.

| Tier | Contract |
|---|---|
| `async-in` | Admitted live deltas over WebSocket with sequence-based recovery |
| `turn-boundary` | Durable per-member queue, bounded drain lease, and monotonic acknowledgement |
| `steer` | Operator/policy interrupt classes only, live or durably queued by policy |

Summary event feeds are the default. Full-fidelity observation is member-scoped
or explicitly authorized spectating, preventing accidental all-to-all event
fan-out. Addressed messages create durable delivery obligations. Queue limits,
leases, retry exhaustion, and dead-letter state are explicit and observable;
accepted records are never silently dropped.

## Operator surface

The reserved collection root is `/v1/constructs`. Authenticated routes cover
rooms, join requests, members, messages, interrupts, events, blobs, delivery
drain/ack, catch-up, and caller-scoped read-only SQL. WebSocket subscriptions
use `/:room/subscribe`; the built-in operator UI is served only when its
explicit gate is enabled.

Unified host configuration owns initialization, enable/emergency gates,
feature gates, resource ceilings, admins, and revocations. All runtime policy
changes are hot except the explicit catalog-initialization switch. Applying a
revocation closes an already connected member socket immediately. UCR kind
registration may be required when the registry is enabled and configured.

Member controls are pause, resume, mute, unmute, and eject. Room controls are
freeze, unfreeze, archive, kill delivery, and resume delivery. Policy revision
and idempotency guards make operator retries deterministic.

## Safety and observability

- `[constructs]` is disabled by default and deny-by-default.
- Disabled or emergency-stopped routes return 404 while durable state remains
  untouched.
- Every attacker-controlled dimension has a compiled hard ceiling; unified
  configuration may disable or lower limits only.
- Bodies, JWTs, secrets, hidden reasoning, and raw resource content stay out of
  logs, metrics, status, and Security Centre.
- Blob upload uses digest and byte-length commitments, canonical chunks,
  explicit commit, bounded range reads, and authorization through a visible
  room reference rather than digest possession alone.
- `/v1/status` reports configured/effective/authority/catalog state, catalog
  schema, room/member/session counts, queue pressure/state, blob state,
  subscription counts by feed/tier, reconciliation outcomes, and an effective
  config digest.
- Prometheus metrics and Security Centre events use bounded labels and exclude
  message/event/blob bodies and tokens.

## Release proof

The [build-36 evidence](../../raw/engines/2026-07-29-construct-build36.md)
records:

- 22/22 focused Construct tests and 239/239 standalone library tests;
- three admitted delivery tiers, idempotent message replay, durable steer,
  1,000 events, bounded drain/ack, exact catch-up, and scoped SQL;
- canonical chunked blob commit, full/range reads, and deduplication;
- real WebSocket snapshots and hot connected-socket revocation;
- complete member and room operator transitions;
- restart persistence, build-35 compatibility rollback on a data copy, and
  byte-exact preservation of the reserved catalog;
- exact staged CLI/host stamps and binary hashes.

The final report-only audit passes at CHML `0/0/0/0`. Build 36 was subsequently
production-promoted; build 39 now carries the capability while retaining the
deny-by-default disabled and uninitialized policy.

## See also

- [Host Configuration Management](../operations/host-configuration-management.md)
- [Staged Builds and Compatibility](../operations/staged-builds-and-compat.md)
- [Streaming and Live Events](streaming-and-live-events.md)
- [Context Engine](../engines/context-engine.md)
- [Unified Consumer Registry and Configuration Spaces](../operations/unified-consumer-registry.md)
- [Security Centre](../operations/security-centre.md)
- [Agent Workflow and Workspace Standards](../workflow/agent-workflow-and-standards.md)
