# Host Configuration Management

> Sources: HOSTCFG-001 and HOSTCFG-002 as-built evidence, 2026-07-14; [PRED-004 build-17 evidence](../../raw/engines/2026-07-16-pred004-adaptive-context-substrate-build17.md); [PRED-006 completion](../../docs/completions/COMP-PRED-006.md); [Security Centre completion](../../docs/completions/COMP-SEC-CENTRE-001.md); [build-33 UCR completion](../../docs/completions/COMP-UCR-001.md); [build-35 CINT batch release](../../raw/engines/2026-07-29-cint-batch-build35.md); [build-36 Construct release](../../raw/engines/2026-07-29-construct-build36.md); [current release state](../../docs/release-state.json)
> Raw: [Unified Host Configuration Management](../../raw/operations/2026-07-14-unified-host-configuration.md)
> Raw: [Exact Multi-GPU Placement](../../raw/operations/2026-07-14-exact-multi-gpu-placement.md)
> Commit: 8d4f4531e90e1a2292c0e163f32509d8fdf7c8a0
> Updated: 2026-07-30

## Overview

Fork builds 8 through 36 provide one unified, authenticated tool for inspecting
and changing a fixed set of standalone host features. Settings truthfully
declare whether they apply hot or require restart. The surface does not mutate
durable database policy, infer models, restart its own process, or couple the
fork to Windows/Linux service managers.

## Operator Surface

Bootstrap access in the node's `config.toml`:

```toml
[host-management]
admin-identities = ["<hex-identity>"]
```

Empty or absent `admin-identities` disables every operation. The list is
deliberately separate from `[app-hosting] admin-identities` and cannot be changed
through the API. Every route requires normal SpacetimeDB bearer authentication
and an exact admin identity match.

```text
spacetime host config list [--server <server>] [--json]
spacetime host config get <key> [--server <server>] [--json]
spacetime host config set <key> <json-value> [--server <server>] [--json]
spacetime host config apply [--server <server>] [--json]
spacetime host config rollback [--server <server>] [--json]
```

The equivalent reserved routes are `GET /v1/host/config`, `GET|PUT
/v1/host/config/:key`, and `POST /v1/host/config/{apply,rollback}`. Responses
show `active_value`, `configured_value`, optional `pending_value`, and
`restart_required`; secrets and raw TOML are never returned.

## Fixed Allowlist

The server, not the CLI, owns the authoritative type/range validation:

| Area | Keys |
|---|---|
| GPU/vector/model sidecar | `gpu.discovery-enabled`, `gpu.device-uuid`, `gpu.vram-budget-bytes`, `vector.subscription-safe-k`, `procedure-http.local-model-sidecar-port` |
| App/runtime pools | `app-hosting.allow-public-upstreams`, `wasm.procedure-instance-pool-size`, `v8.procedure-instance-pool-size` |
| V8 heap | `v8-heap-policy.heap-check-request-interval`, `v8-heap-policy.heap-check-time-interval`, `v8-heap-policy.heap-limit-mb` |
| Streaming/WebSocket | `web.streaming-pool-size`, `web.max-concurrent-streams`, `web.per-stream-timeout`, `websocket.ping-interval`, `websocket.idle-timeout`, `websocket.close-handshake-timeout`, `websocket.incoming-queue-length` |
| Commitlog | `commitlog.offset-index-require-segment-fsync`, `commitlog.preallocate-segments` |
| Adaptive context | `adaptive-context.emergency-stop`, six independent capability toggles, `adaptive-context.max-stage-bytes`, `adaptive-context.max-inflight-stages` |
| Fabric orchestration | emergency stop; five independent facility toggles; reservation, graph, scheduling, artifact, and federation ceilings under `fabric-orchestration.*` |
| Native TLS | ten `tls.*` settings covering enablement/listener, redirect, ACME, renewal, HSTS, and trusted proxies; the runtime marks only genuinely hot keys hot |
| Security Centre | seven hot `security-centre.*` ingest, ring, and retention settings |
| Service hosting | hot enable/emergency/registration/managed/Fabric/public-upstream switches; service, in-flight, byte, lease and timeout ceilings; restart-bound admin/executable/root lists and managed-process ceiling |
| Consumer registry | 17 hot `consumer-registry.*` enable/emergency/registration/write, resource-ceiling, admin and revocation settings |
| Native CINT batch | hot `cint-batch.enabled` plus deny-only file, aggregate-source, symbol, reference and encoded-result ceilings |
| Native Constructs | restart-bound `constructs.initialize-catalog`; hot enable/emergency, room/event/blob/UI/SQL/kind gates, all room/member/message/event/blob/queue/catch-up/query/rate/lease/retention ceilings, admin and revocation lists |

Values are booleans, unsigned integers, duration strings such as `30s`, or
strings or bounded string lists. `null` removes a nullable override. Durations normalize before
staging, so an equivalent spelling does not create a redundant change.

`gpu.device-uuid` is a canonical NVIDIA `GPU-...` UUID and is restart-bound:

| Discovery | Device UUID | Result |
|---|---|---|
| disabled | any value | CPU mode; no discovery or CUDA allocation |
| enabled | `null` | automatic; most free VRAM, then lowest physical ordinal |
| enabled | UUID | pinned; use exactly that GPU or fail closed to CPU |

The status inventory reports physical ordinal and UUID, while the selected
device additionally reports its process-local CUDA ordinal. These ordinals can
differ: on the validated host the physical GTX 1660 Ti at ordinal 0 maps to CUDA
ordinal 1, and the physical RTX 3080 at ordinal 1 maps to CUDA ordinal 0.

The interface cannot access certificate/JWT material, credentials, app-host
Basic auth, host-management admins, data/storage paths, in-memory mode,
PostgreSQL ports, Tracy/flamegraph/logging environment switches, or arbitrary
TOML. Explicit bounded subsystem ACL settings are available only where the
fixed registry declares them.

## Adaptive Context Controls

Build 17 adds `[adaptive-context]` without making host configuration the replay
authority. The host is deny-only: a disabled host capability or emergency stop
can refuse local work, while durable database policy may impose stricter rules.
Host configuration cannot grant a capability denied by database state.

The six fresh-default-on toggles are item telemetry, source resolution, exact
token batching, parameterized actions, quality gating, and stage reuse. Optional
stage-byte and inflight-stage ceilings further constrain local resources.
`/v1/status` reports configured and effective values; emergency stop forces all
effective toggles false while preserving the configured values for reversible
recovery.

## Fabric Orchestration Controls

Build 20 adds `[fabric-orchestration]` under the same deny-by-default
management plane. The host is **deny-only**: effective permission is the
intersection of host permission and durable database configuration. Separate
toggles control resource reservations, work graphs, scheduling, chunked
artifacts, and federation; emergency stop denies all five while retaining their
configured values. Typed ceilings bound reservations, leases, graph size/depth,
scheduling candidates, artifact/chunk counts and bytes, and federation
hops/body size. `/v1/status` reports configured and effective states so an
operator can distinguish policy from emergency intervention. See
[Fabric Orchestration Platform](../engines/fabric-orchestration-platform.md).

## Persistence and Restart Contract

`set` writes only `<data-dir>/host-config-pending.json`. Pending changes carry
the BLAKE3 hash of their base `config.toml`; `apply` returns conflict rather than
overwriting a manual edit. The complete candidate is parsed with the real
standalone configuration types and cross-field WebSocket timing is checked.

An apply synchronously persists an exact one-level backup at
`config.toml.hostcfg.bak`, atomically replaces `config.toml`, and clears pending
state. `rollback` restores and consumes that backup. The API remains responsive
because file reads, syncs, and replacements run on Tokio's blocking pool.

Apply and rollback push settings marked hot into their registered live
subsystem and update the reported active snapshot. Restart-bound settings stay
at their previous active value and make `restart_required` truthful; restart
the host through its existing supervisor only in that case. GPU placement is
restart-bound, so a CUDA-capable binary uses the new discovery/device policy on
the next host start without requiring a rebuild.

Service-hosting feature switches and traffic/resource ceilings are hot. Its
admin identities, canonical executable allowlist and canonical working-root
allowlist are typed bounded string lists that require restart. Applying
`service-hosting.emergency-stop=true` immediately denies new service
invocations and terminates managed work; rollback restores the prior hot
policy. See the [Generic Managed Service Plane](managed-service-plane.md).

Build 33 adds the [Unified Consumer Registry](unified-consumer-registry.md).
All 17 `consumer-registry.*` controls are hot, including bounded admin and
revocation string lists. Build 33 also lets the CLI submit valid JSON arrays
and objects to the server, which remains authoritative for each setting's
registered type and limits.

Build 35 adds the
[bounded native CINT batch](../cmt/bounded-native-cint-batch-proposal.md).
All six `cint-batch.*` settings apply hot. The feature is enabled by default
but inert until a guest calls ABI 10.13. Host policy is deny-only: it can
disable the call or lower a compile-time ceiling, never raise one. Lowering the
aggregate source budget yields an ordered per-file `source_limit` rejection
without charging that item against the remaining budget; malformed input over
the compile-time hard aggregate still aborts before mutation.

Build 36 adds
[Native Constructs](../web/native-constructs-proposal.md). The explicit
`constructs.initialize-catalog` setting is restart-bound because it authorizes
creation or opening of reserved persistent state. Every remaining
`constructs.*` setting is hot: enable/emergency and operation gates, ceilings,
admins, and revocations. Applying a revocation also closes that identity's
active Construct sockets immediately. The active value remains the
intersection of compiled bounds, configured gates, authority readiness, and
catalog readiness; host configuration cannot raise a compiled ceiling.

## Release and Evidence

Exact multi-GPU placement was introduced by build 9. Build 17 added adaptive
context controls; build 20 added Fabric orchestration controls; later TLS and
Security Centre releases added their typed settings without opening arbitrary
TOML mutation. Build 30 adds the service-hosting hot switches, limits and
restart-bound policy lists under the same fixed typed surface. Build 33 adds
the hot consumer-registry controls and compound-JSON CLI transport. Build 35
adds the hot CINT-batch controls. Build 36 adds the Construct catalog
initialization barrier and hot runtime policy. See
[Staged Builds & Compatibility](staged-builds-and-compat.md) for the immutable
artifact registry and [Live Deployment](live-deployment.md) for current node
stamps.

The former build-17 LAN service is now a stopped/startup-disabled
[legacy host](legacy-lan-host-retirement.md); its preserved config had GPU
discovery disabled. The public/local node observed after the 2026-07-30
promotion runs build 36. It reports CUDA compiled but GPU discovery disabled
and `accelerated=false`, all five Fabric-orchestration and all six
adaptive-context facilities configured/effective, CINT batch effective,
Security Centre active, and consumer-registry/service-hosting present but
disabled/effective false. Construct settings are present, but configured false,
effective false, catalog not ready, and explicit initialization still required.
See the [current release state](../../docs/release-state.json).

This is exact placement, not VRAM pooling. The GPUs remain separate allocation
domains; Fork does not add their memory capacities. Embedding/indexer/reranker
model placement remains an external deployment or AI-Collab responsibility.

## See Also

- [Staged Builds & Compatibility](staged-builds-and-compat.md)
- [Fork Versioning & Drift Detection](fork-versioning.md)
- [Live Deployment](live-deployment.md)
- [Local Model Sidecar Cutover](../embeddings/local-model-sidecar-cutover.md)
- [Vector Search Architecture](../vector/vector-search-architecture.md)
- [Generic Managed Service Plane](managed-service-plane.md)
- [Unified Consumer Registry and Configuration Spaces](unified-consumer-registry.md)
