# Live and Legacy Deployment

> Sources: [machine-readable release state](../../docs/release-state.json); [staged-build registry](staged-builds-and-compat.md); [build-39 release evidence](../../raw/engines/2026-08-14-worldline-build39.md); [legacy LAN retirement](legacy-lan-host-retirement.md)
> Repository source: ea2478522e5dd4e714a242e46fe5a17bcf5b477d
> Updated: 2026-08-14

## Current observed state

Fresh checks on 2026-08-14 establish four deliberately separate truths:

| Scope | Authoritative fact | Authority |
|---|---|---|
| Repository source | Fork build **39**, release commit `ea2478522e5dd4e714a242e46fe5a17bcf5b477d` | `SpacetimeDB/FORK-VERSION` and `SpacetimeDB/fork-version.json` |
| Newest verified immutable stage | **`v2.7.0-worldline-build39-r1`**, build **39**, standalone SHA-256 `13AFC61EB3602F27795A2D3B795FE28464238C63A04BFE62765E457C06D03E33` | Staged executable stamp/hash, isolated publication, restart and rollback/restore |
| Newest accepted immutable stage | **`v2.7.0-worldline-build39-r1`**, build **39**, same exact stamp/hash | Build-39 evidence and Phase 9 acceptance record |
| Observed public/local runtime | Build **39**, commit `ea2478522e5dd4e714a242e46fe5a17bcf5b477d`, executable from **`v2.7.0-worldline-build39-r1-production`** | Both `/v1/fork-version` and `/v1/status` endpoints, capabilities and serving-process path |

Build 39 was production-promoted from a byte-identical D-only production-layout
copy of its accepted stage. Earlier accepted stages remain immutable rollback
evidence; build 36 successfully opened a copied build-39 data root without
touching its 28 Worldline files, and build 39 restored them exactly.

The local endpoint is `http://127.0.0.1:3012`; the public endpoint is
`https://app.weyaisoft.com`. Both return the same build-39 stamp. The serving
process is
`D:\Projects\SpacetimeDB_Builds\v2.7.0-worldline-build39-r1-production\bin\spacetimedb-standalone.exe`.
The existing detached production supervisor remains responsible for lifecycle.

## Current runtime capabilities

The build-39 `/v1/status` response reports:

- CUDA compiled into the executable, but GPU discovery/selection **disabled**
  and `accelerated=false`; the configured per-index budget is 1 GiB and there
  is no runtime device budget;
- all six adaptive-context facilities configured/effective;
- all six Fabric-orchestration facilities configured/effective;
- CINT batch configured/effective enabled;
- Security Centre active;
- consumer-registry and service-hosting capabilities present but
  disabled/effective false;
- Constructs compiled and present, but configured false, effective false,
  catalog not ready, and explicit initialization still required;
- Worldlines present/configured/effective with API 1, ABI 10.14, SDK contract 1
  and HTTP/WebSocket transports. Only the exact create/execute/query/evaluate
  allowlist is admitted; approval, promotion, effects, subscriptions,
  retention/GC, nesting, federation and delegated capabilities remain denied.

These are startup/status facts, not proof that a particular query, payload or
CINT call executed. The CUDA compile default is a source/build property; runtime
GPU selection remains separately configurable.

## Historical promotion versus current observation

Build 33 was production-promoted on 2026-07-29 and build 34 was later observed
live despite remaining a rejected CINT candidate. Those records remain dated
history. Build 36 superseded both, and build 39 now supersedes build 36 after
exact hash verification, publication, restart and rollback/restore. Historical
evidence must retain its observation date rather than using an unqualified
“current” claim.

The former NSSM `SpacetimeDB` listener at `192.168.1.211:3000` remains a
[legacy LAN host](legacy-lan-host-retirement.md), not production. Its service is
Stopped, startup type Disabled, port closed, and preserved state is for
rollback/history only.

## Routing model

Every published module is reachable at
`http://<host>/v1/database/<db-name>/route/<module-path>`—one process, one
port, many apps by database name. `[app-hosting]` can route public host/path
rules directly to modules while reserving `/v1` and `/internal`. The optional
Caddy gateway remains an edge/TLS choice.

## Host roll and guest publish rules

Classify artifact impact before deployment. Host changes require a new immutable
stage and explicit owner-controlled promotion that preserves data, JWT and
configuration. Verify executable stamp, `/v1/fork-version`, `/v1/status`,
module health, restart and rollback before calling the promotion complete.

Guest changes require a rebuilt WASM published with the existing database owner
identity. Anonymous publish creates a different identity and cannot update the
canonical database. ABI-changing rollbacks are full-stage host+guest rollbacks,
not an old-host-only swap.

Staged builds live in `D:\Projects\SpacetimeDB_Builds\<stage>` and must never be
overwritten. The canonical inventory is
[Staged Builds and Compatibility](staged-builds-and-compat.md). Current mutable
facts are recorded in [docs/release-state.json](../../docs/release-state.json)
and must be refreshed from the executable/endpoints whenever a document makes a
live-runtime claim.

## See Also

- [Fork Versioning and Drift Detection](fork-versioning.md)
- [Staged Builds and Compatibility](staged-builds-and-compat.md)
- [Host Configuration Management](host-configuration-management.md)
- [Legacy LAN Host Retirement](legacy-lan-host-retirement.md)
