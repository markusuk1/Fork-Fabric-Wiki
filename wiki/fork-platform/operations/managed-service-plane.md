# Generic Managed Service Plane

> Sources: [SVC-001 request](../../docs/requests/SVC-001-managed-service-plane.md); [implementation plan](../../docs/plans/SVC-001-managed-service-plane.md); [final acceptance audit](../../docs/audits/AUD-SVC-009.md); [current release state](../../docs/release-state.json)
> Commit: 862f0a81e9bac57905d7427df0cbb3de06e58099
> Updated: 2026-07-30

## What shipped

Fork build 30 adds a protocol-neutral service plane to the standalone host.
It can discover, authenticate, admit, proxy, observe and optionally supervise
RSP, LSP, SCIP, model, reranker, verifier and future HTTP-compatible servers
without implementing their protocol semantics.

The ownership boundary is deliberate:

- Fork owns bounded hosting, identity-aware routing, lifecycle, resource
  admission, unified configuration and operational evidence.
- The hosted service owns its protocol and application semantics.
- PRD Plugin remains the sole Reason Guard policy and state authority. Hosting
  an RSP server does not move reasoning policy into Fork.

This is a host capability. It adds no guest ABI or WASM publication.

## Descriptor and registration model

Every version-1 descriptor declares:

- stable service ID, protocol family and contract versions;
- bounded capability identifiers;
- external or managed mode and an explicit IP-literal upstream;
- optional health path, owner and workspace labels;
- caller identity allowlist;
- resource units and a per-service in-flight ceiling;
- for a statically managed service only: executable, argument vector, working
  directory, inherited environment names, auto-start and restart policy.

Static descriptors in `config.toml` are the immutable base. The authenticated
runtime API may add and remove only external descriptors. Runtime state is
atomically persisted under `<data-dir>/service-hosting/services.json` and
survives restart. It cannot introduce executables, arguments, environments or
managed processes.

Public upstreams fail closed by default. DNS names are not accepted: the
descriptor uses an IP literal, and non-loopback addresses require the explicit
`allow-public-upstreams` switch.

## Routes

All routes live under `/v1/service-hosting`:

| Method and path | Purpose |
|---|---|
| `GET /services` | Caller-filtered discovery; admins see all services |
| `POST /services` | Admin-only external registration |
| `DELETE /services/:id` | Admin-only runtime-external removal |
| `POST /services/:id/start` | Admin-only managed start |
| `POST /services/:id/stop` | Admin-only managed stop |
| `POST /services/:id/restart` | Admin-only managed restart |
| `GET /services/:id/health` | Admin-only bounded readiness probe |
| `GET /receipts` | Admin-only bounded operational receipts |
| `ANY /invoke/:id/*path` | Authenticated, allowlisted streamed invocation |

Invocation requires
`X-Fork-Service-Contract-Version` naming exactly one advertised version. A
missing version returns `428`; an unsupported version fails before dispatch.
Methods, query strings, streamed bodies, SSE and WebSocket upgrades use the
existing native proxy path.

Fork validates the normal Spacetime bearer token before admission and never
forwards it. Every client-supplied `X-Fork-Service-*` header is removed. The
gateway then injects canonical caller, service, contract and request-ID
headers. Request IDs combine a CSPRNG runtime nonce, monotonic sequence and
trusted request dimensions, so they remain fixed-size and do not collide
across host restarts.

## Managed lifecycle

Managed launch is static-only and accepts no shell command string. The
canonical executable must exactly match `allowed-executables`; the canonical
working directory must be inside `allowed-working-roots`; both allowlists are
bounded and restart-bound.

Windows creates the process suspended, assigns it to a kill-on-close Job
Object, then resumes it. Unix uses a dedicated process group. A containment
setup failure denies launch. Operator stop, emergency stop and host shutdown
terminate the complete owned tree. Restart counts, backoff, waits, controller
operations and process totals all have hard ceilings.

The release audit found and fixed a subtle Windows failure: a bounded process
wait had treated “still running” as “failed,” killing healthy services. The
accepted implementation polls through short-lived duplicate handles; a poll
timeout means healthy, while command cancellation leaves at most one bounded
blocking task.

## Fabric and concurrency admission

Every accepted invocation holds an atomic per-service concurrency permit until
the response stream or upgraded connection ends. When
`fabric-admission-enabled` is true, it also holds a native Fabric resource
reservation for the full stream lifetime.

Leases renew while work remains live. If renewal is delayed beyond expiry, the
runtime atomically expires and reacquires under the same admission lock before
capacity can be reused. This prevents two streams from owning the same units.
Fork supplies generic resource admission only; service/model/GPU placement
semantics remain external.

## Unified configuration

`[service-hosting]` defaults off and deny-by-default. Hot settings include:

- `enabled`, `emergency-stop`, external registration, managed processes,
  Fabric admission and public-upstream permission;
- service/in-flight/request/response/WebSocket/Fabric ceilings;
- Fabric lease and connect/header/health timeouts.

Restart-bound settings include the admin list, executable allowlist, working
root allowlist, managed-process ceiling, restart policy defaults, receipt
bounds and static descriptors. The typed configurator exposes the three policy
lists as bounded string lists rather than opening arbitrary TOML mutation.

Applying `service-hosting.emergency-stop=true` takes effect without a host
restart: it denies new invocation and terminates managed work. Rollback restores
the prior hot policy; an operator may then explicitly restart desired services.

## Evidence and observability

`GET /v1/status` reports configured/effective state, service totals and running
managed totals. Prometheus counters cover invocation outcomes, process state,
receipt drops and lifecycle-event drops. Security-relevant authorization,
policy, limit, launch, crash and lifecycle events flow into the existing
[Security Centre](security-centre.md).

The node-local JSONL receipt journal records bounded metadata only. It has
independent record and byte ceilings, atomic oldest-first compaction, a
non-blocking bounded write queue, and final-record torn-write repair. Interior
corruption fails startup closed. Tokens, request/response bodies, executable
arguments, environment values, prompts and hidden reasoning are excluded.

Disabled mode starts no receipt OS thread and adds no work to ordinary database
requests.

## Release evidence

Accepted artifact:

- build 30, commit `862f0a81e`;
- immutable stage `v2.7.0-service-plane-r3`;
- standalone SHA-256
  `7615776A5B0697A588322ADA05CED671DE11BE81D428E5B9E4C70B5FE2FBD037`;
- 198 standalone tests and all standalone targets green;
- final [AUD-SVC-009](../../docs/audits/AUD-SVC-009.md) at CHML `0/0/0/0`.

The live isolated drill proved external and managed invoke, health, exact
contract gating, body limits, bearer stripping, spoof replacement, managed
restart, hot emergency stop/rollback, persisted runtime registration, host
restart, request-ID uniqueness, build-27 rollback and build-30 restore.
Builds 28 and 29 remain immutable rejected stages: the audit loop preserves
them rather than overwriting evidence.

Build 30 itself remains an immutable unpromoted stage; later builds carry the
capability. Build 33 was production-promoted on 2026-07-29, and build 36
superseded it in production on 2026-07-30. The current build-36 runtime carries
the service plane but reports service hosting disabled/effective false.
The former
`192.168.1.211:3000` build-17 listener was subsequently retired as a
[legacy host](legacy-lan-host-retirement.md): stopped, startup-disabled and
preserved for rollback evidence.

## See also

- [Host Configuration Management](host-configuration-management.md)
- [Staged Builds & Guest/Host Compatibility](staged-builds-and-compat.md)
- [Fork Versioning & Drift Detection](fork-versioning.md)
- [Fabric Orchestration Platform](../engines/fabric-orchestration-platform.md)
- [Security Centre](security-centre.md)
