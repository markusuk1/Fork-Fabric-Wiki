# Live and Legacy Deployment

> Sources: [machine-readable release state](../../docs/release-state.json); [staged-build registry](staged-builds-and-compat.md); [build-36 production promotion](../../raw/operations/2026-07-30-build36-production-promotion.md); [build-36 release evidence](../../raw/engines/2026-07-29-construct-build36.md); [legacy LAN retirement](legacy-lan-host-retirement.md)
> Commit: 8d4f4531e90e1a2292c0e163f32509d8fdf7c8a0
> Updated: 2026-07-30

## Current observed state

Fresh checks at `2026-07-30T09:22:24+01:00` establish three aligned truths:

| Scope | Authoritative fact | Authority |
|---|---|---|
| Repository source | Fork build **36**, release commit `8d4f4531e90e1a2292c0e163f32509d8fdf7c8a0` | `SpacetimeDB/FORK-VERSION` and `SpacetimeDB/fork-version.json` |
| Newest accepted immutable stage | **`v2.7.0-construct-r1`**, build **36**, standalone SHA-256 `287EBBFAA1FEBC6AACB56DFF3DBEB01DAA5210CBDBC61B599961A74A617EFE83` | Staged executable stamp/hash and build-36 release evidence |
| Observed public/local runtime | Build **36**, commit `8d4f4531e90e1a2292c0e163f32509d8fdf7c8a0`, executable from **`v2.7.0-construct-r1-production`** | Both `/v1/fork-version` endpoints, local `/v1/status`, and serving-process path |

Build 36 was production-promoted on 2026-07-30 using a byte-identical
production-layout copy of the accepted stage. The accepted release folder
remains immutable. Source, accepted artifact, production executable, and both
live endpoints now agree on the same build and commit.

The local endpoint is `http://127.0.0.1:3012`; the public endpoint is
`https://app.weyaisoft.com`. Both return the same build-36 stamp. The serving
process is
`D:\Projects\SpacetimeDB_Builds\v2.7.0-construct-r1-production\bin\spacetimedb-standalone.exe`.
The existing detached production supervisor remains responsible for lifecycle.

## Current runtime capabilities

The build-36 `/v1/status` response reports:

- CUDA compiled into the executable, but GPU discovery/selection **disabled**
  and `accelerated=false`; the configured per-index budget is 1 GiB and there
  is no runtime device budget;
- all six adaptive-context facilities configured/effective;
- all five Fabric-orchestration facilities configured/effective;
- CINT batch configured/effective enabled;
- Security Centre active;
- consumer-registry and service-hosting capabilities present but
  disabled/effective false;
- Constructs compiled and present, but configured false, effective false,
  catalog not ready, and explicit initialization still required.

These are startup/status facts, not proof that a particular query, payload or
CINT call executed. The CUDA compile default is a source/build property; runtime
GPU selection remains separately configurable.

## Historical promotion versus current observation

Build 33 was production-promoted on 2026-07-29 and build 34 was later observed
live despite remaining a rejected CINT candidate. Those records remain dated
history. Build 36 superseded both on the live route on 2026-07-30 after exact
hash verification, restart, build-34 rollback, and build-36 restore. Historical
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
