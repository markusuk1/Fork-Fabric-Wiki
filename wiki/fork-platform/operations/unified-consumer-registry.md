# Unified Consumer Registry and Configuration Spaces

> Sources: [UCR-001 implementation plan](../../docs/plans/UCR-001-unified-consumer-registry.md); [source re-audit](../../docs/audits/AUD-UCR-003.md); [completion evidence](../../docs/completions/COMP-UCR-001.md); [production promotion](../../raw/operations/2026-07-29-build33-production-promotion.md); [current release state](../../docs/release-state.json)
> Commit: 2c82d1b3560bb1eb454f5b9e0064b0970e8fd36a
> Updated: 2026-07-30

## Purpose and ownership

Fork build 33 adds one generic host kernel for consumer-defined registries and
typed configuration spaces. A consumer can register an immutable declarative
kind and then create, discover, version, resolve and observe entries without a
new Fork build for each registry.

Fork owns storage, validation, permissions, deterministic resolution and
observation. Consumers retain their semantics: registering a tool, skill,
workflow, service or module does not install code, launch a process or make the
resource trusted. The [Generic Managed Service Plane](managed-service-plane.md)
remains service routing/process authority, while Fabric remains orchestration
authority.

## Kind and value model

A kind definition fixes its ID, schema version, registry/config-space mode,
bounded JSON value schema, ordered scope types, visibility, writers, merge
strategy and entry ceiling. `(kind-id, schema-version)` is immutable; evolution
registers a new schema version.

The schema language includes null, Boolean, signed integer, bounded strings,
arrays, maps, objects and `one-of`. String formats cover tokens, URIs, logical
paths, semantic versions, digests and secret references. Floating point and
uploaded validators are excluded so validation and canonical digests stay
portable.

Seven system kinds are present on every enabled host:

- `fork.module`
- `fork.service`
- `fork.tool`
- `fork.skill`
- `fork.workflow`
- `fork.path`
- `fork.version`

Dynamic kinds use the same API and storage rules.

## Entry, history and resolution contract

An entry is addressed by kind, schema version, key and exact scope. Writes use
compare-and-swap revisions (`0` means create). One sled transaction advances a
global revision and writes current state, immutable history and the durable
change feed. Canonical JSON produces a BLAKE3 content digest independent of
object input order.

Registry mode resolves exact or deterministic most-specific fallback.
Config-space mode collects the caller-declared scope layers and applies
replace, recursive object merge, set union or ordered append. The response
contains the effective value/digest, highest revision and exact ordered
provenance. The host never guesses scope IDs.

History and change reads are bounded. If retention has removed the next change
needed by a cursor, the host returns HTTP 410 `cursor_expired` with
`minimum_revision`; it never silently skips the gap.

## Authority and security

`[consumer-registry]` defaults off, and every `/v1/registry` route requires
normal Spacetime bearer authentication. Independent hot switches control kind
registration and entry writes. Emergency stop denies mutation without deleting
state.

Host admins can register kinds and administer entries. Kind owners and
configured writers can mutate their entries. Visibility is either authenticated
or private. A bounded hot `revoked-identities` list overrides persisted admin,
owner and writer grants immediately, which permits incident response without
rewriting immutable definitions.

This is not a secret store. Consumers must store secret-reference URIs rather
than credential material. A generic opaque JSON host cannot reliably infer
whether caller data is a secret. Values are therefore excluded from status,
metrics, Security Centre events and errors, while authenticated history returns
values by design.

Every attacker-controlled dimension has a configured ceiling: kinds, entries,
per-kind entries, schema/value bytes, depth, arrays, objects, total nodes,
history, query results, identities, scopes, keys and links. Stored state is
integrity-checked at startup and a corrupted registry fails closed.

## HTTP, CLI and unified configuration

The authenticated HTTP surface is:

| Route | Operation |
|---|---|
| `GET/POST /v1/registry/kinds` | discover or register immutable kinds |
| `GET/PUT /v1/registry/entries` | discover or CAS-write entries |
| `POST /v1/registry/entries/retire` | CAS-retire an entry |
| `POST /v1/registry/resolve` | registry/config-space resolution |
| `GET /v1/registry/history` | bounded exact-entry history |
| `GET /v1/registry/changes` | revision-cursor change feed |

`spacetime registry` exposes the same operations. Mutation and resolve requests
are versioned JSON files; `--json` preserves the complete response.

The [Host Configuration Management](host-configuration-management.md) surface
registers 17 `consumer-registry.*` keys: enable/emergency/registration/write
switches, all resource ceilings, admins and revocations. All are hot in build
33. The host CLI accepts valid compound JSON, so bounded string-list controls
such as admins and revocations are usable through `host config set`; the server
remains the authoritative schema/range validator.

## Persistence and compatibility

State lives in a dedicated `<data-dir>/consumer-registry` sled database. It is
independent of module schemas and guest ABI:

- host-only release; no WASM rebuild or publish;
- disabled/default mode does no registry work on ordinary database requests;
- build 30 ignores the separate registry directory;
- rollback requires disabling the registry and using a build-30-compatible
  config without the unknown `[consumer-registry]` section;
- restoring build 33 reopens the same entries and revision history.

## Build 33 release evidence

The accepted immutable stage is
`D:\Projects\SpacetimeDB_Builds\v2.7.0-unified-registry-r4`:

- fork build 33, commit `2c82d1b3560bb1eb454f5b9e0064b0970e8fd36a`;
- standalone SHA-256
  `2B6E44480B6D5542E9096A47340FE568A113C72995CDAE8B397EC7A799EEEC81`;
- CLI SHA-256
  `E9F0241CD1F57E3A3E283D35DBBA3E46C3EB94DD9321CD514DB1C479C3BB1703`;
- update SHA-256
  `4094A66507166DE4413AFB47DA01765B998CE8359FB3C3439AE3807460EBB390`.

Isolated live acceptance registered a dynamic `indexing.profile` config space,
merged global/project layers with exact provenance, populated the built-in path
and version kinds, exercised history/change, returned 401 without auth and 409
on stale CAS, hot-revoked and restored the owner on the same PID, survived
restart, rolled back to build 30 and restored build 33 with revision 5 and all
four entries intact.

Build 31 is rejected because live testing exposed that the CLI rejected
compound JSON values required by typed string-list settings. Build 32 corrected
the behavior but is superseded because its generated host-config guidance still
described the older restart-only contract. The `r2` packaging attempt is
incomplete warning-stream evidence, not a deployable stage. Build 33 is the
first accepted UCR release and was production-promoted on 2026-07-29.

Build 33's 2026-07-29 promotion retained the existing deny-by-default policy.
Build 36 superseded it in production on 2026-07-30 and carries the same
consumer-registry capability; `/v1/status` reports it disabled/effective false.
Enabling the registry remains an explicit unified-config decision rather than
a side effect of installing a capable binary. The former
port-3000 build-17 host remains
[disabled legacy state](legacy-lan-host-retirement.md).

## See Also

- [Staged Builds & Compatibility](staged-builds-and-compat.md)
- [Fork Versioning & Drift Detection](fork-versioning.md)
- [Live Deployment](live-deployment.md)
- [Host Configuration Management](host-configuration-management.md)
