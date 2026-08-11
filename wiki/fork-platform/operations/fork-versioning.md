# Fork versioning & drift detection

> Sources: `SpacetimeDB/FORK-VERSION`; `SpacetimeDB/fork-version.json`; [machine-readable release state](../../docs/release-state.json); [staged-build registry](staged-builds-and-compat.md); [build-36 production promotion](../../raw/operations/2026-07-30-build36-production-promotion.md); [build-36 Construct release](../../raw/engines/2026-07-29-construct-build36.md)
> Commit: 8d4f4531e90e1a2292c0e163f32509d8fdf7c8a0
> Updated: 2026-07-30

## The problem

The fork's crates carry the **upstream** version (`2.7.0`) — every fork build
reports the same `spacetimedb tool version 2.7.0`. Distinct fork builds were told
apart only by git commit hash and folder name, neither of which is **orderable**:
`ceedd0147` vs `800b4cb25` says nothing about which is newer, so "am I on the
latest fork?" and "is this build ≥ the one I pinned?" had no cheap answer.

## The scheme

A **monotonic fork build number** layered on top of the upstream base version.

Current staged release: **fork build 36**, commit `8d4f4531e`, folder
`v2.7.0-construct-r1`. It is accepted and production-promoted. It carries the
completed build-20 native Fabric
orchestration platform, build-26 Security Centre, build-27
`fabric_payload(speculation)` migration fix, the build-30
[generic managed service plane](managed-service-plane.md), and the build-33
[unified consumer registry](unified-consumer-registry.md), plus ABI 10.13
[bounded native CINT microbatch ingest](../cmt/bounded-native-cint-batch-proposal.md),
plus host-native [collaboration rooms](../web/native-constructs-proposal.md).
Build 36 was production-promoted on 2026-07-30 from a byte-identical
`v2.7.0-construct-r1-production` layout. The local node on port 3012 and public
`app.weyaisoft.com` endpoint both report build 36 and exact commit
`8d4f4531e90e1a2292c0e163f32509d8fdf7c8a0`.

The observed build-36 runtime has CUDA compiled but GPU discovery disabled,
`accelerated=false`, and a configured 1 GiB per-index budget with no runtime
device budget. All five Fabric-orchestration and all six adaptive-context
facilities are configured/effective, CINT batch is effective, Security Centre
is active, consumer registry and service hosting are present but ineffective,
and Constructs are present but configured/effective false with no initialized
catalog. The former LAN listener
at `192.168.1.211:3000` last ran **build 17** (`ed64e1d0b`,
`v2.7.0-pred004-r2`); it is now a preserved
[legacy host](legacy-lan-host-retirement.md) whose service is stopped and
startup-disabled. Build 27 changes guest macro
migration behaviour; its standalone binary is byte-identical to build 26.
The earlier LAN promotion resolved
[BLK-SEQ-001](../../docs/blockers/2026-07-13-lan-seq001-promotion-blocker.md);
build 5 remains rejected.

- **Source of truth:** `SpacetimeDB/FORK-VERSION` — a single integer, the fork
  build number. Never reset across upstream rebases (a 2.7→2.8 rebase keeps
  counting up); the upstream base rides alongside it.
- **Stamped into the binary** by `crates/lib/build.rs` (env `FORK_VERSION`,
  mirroring how `GIT_HASH` is baked) and exposed as
  `spacetimedb_lib::version::fork_build_version()`. `crates/lib` is the **single
  source**: because every crate depends on it, the CLI (`--version`), the
  standalone host (`/v1/status`), and guest modules all read the same number
  without re-baking. _(Originally baked in `crates/cli/build.rs`; promoted to
  `lib` by TOOL-OBS-001.)_ Surfaced by `spacetimedb-cli --version`:

  ```
  SpacetimeDB fork build 1 (base 2.7.0)
  Commit: <git-sha>
  spacetimedb tool version 2.7.0; spacetimedb-lib version 2.7.0;
  ```
- **`CARGO_PKG_VERSION` is deliberately NOT touched.** Crates stay at the upstream
  `2.7.0` so SDK/ABI version matching (`spacetimedb-lib version 2.7.0`) is
  unaffected. The fork number is a separate stamped field, not a semver change.
- **Complements, does not replace, the commit stamp.** The git commit remains the
  precise ABI-pin (see the compat rules in the staged-builds doc); the fork number
  is the orderable ops/drift signal on top.

## How to use it

- **Bump on every staging:** increment `FORK-VERSION` by one in the staging
  commit, alongside the registry row **and `SpacetimeDB/fork-version.json`'s
  `fork_version`** (keep the two files in lockstep; `/v1/status` re-bakes
  automatically from `FORK-VERSION`). The staged exe then stamps that number.
- **Drift check:** `fork build N` vs the registry's newest `N` — a `>` comparison.
- **Downstream pin:** "requires fork build ≥ N" instead of comparing commit
  hashes; the registry maps fork number → commit + folder.
- **`dev`:** a build made without `FORK-VERSION` (partial checkout) stamps
  `fork build dev` — a visible signal it is not a staged/numbered build.

## Decision record

- **Decided:** a monotonic, never-reset integer fork build number in
  `FORK-VERSION`, stamped via the CLI build script, shown in `--version` on top of
  the upstream base; the registry gains a `Fork` column.
- **Rejected — bumping Cargo `version` (e.g. `2.7.0+fork.6`):** build metadata is
  ignored by semver precedence and risks SDK/ABI matchers that key on the exact
  `2.7.0`; a pre-release suffix (`2.7.0-fork.6`) inverts ordering. A separate
  stamped field is safer and clearer.
- **Rejected — per-upstream-base reset (`2.7.0-fork.N`):** a never-reset integer
  makes "newest" a single global `>` even across rebases; the base is carried
  separately for context.
- **Scope:** wired into `spacetimedb-cli --version` (the registry's canonical
  stamp). The standalone/update binaries can adopt the same env in a follow-up if
  their `--version` needs it.

## Version manifest for downstream drift detection (`fork-version.json`)

A downstream tool (e.g. the prd-plugin fork-version check) needs **one field name
at a configurable URL** to compare against a local marker. Two surfaces expose the
same field, `fork_version`:

- **Static manifest** — `SpacetimeDB/fork-version.json` (committed beside
  `FORK-VERSION`), for repo-pinned consumers / vendored copies / a hosted file:

  ```json
  { "fork_version": 36, "name": "spacetimedb-fork", "base_version": "2.7.0",
    "released": "2026-07-29" }
  ```
  Only `fork_version` is required (number, or `"dev"`); the rest is optional
  metadata. The optional `notes_url` may target the repository
  [CHANGELOG.md](../../CHANGELOG.md). Keep the manifest
  **in lockstep with `FORK-VERSION`** — bump both on every
  staging (see below).
- **Running node** — two endpoints carry `fork_version` for node-connected
  consumers: `GET /v1/fork-version` serves the **clean manifest**
  (`{ fork_version, name, base_version, commit }`) — the preferred poll target —
  and `GET /v1/status` also carries `fork_version` alongside GPU/host detail. Same
  field name across all three surfaces → zero per-source config. No extra
  infrastructure: the node already hosts these; a node-independent static host is
  optional (only if the URL must stay up while the fork node is down).

Compare `fork_version` as an integer (`>`); it never resets across upstream
rebases, so it is a single global key. `notes_url` is repo-relative pending a
public host for the fork (the fork has no remote of its own — only `upstream`).

## Runtime status endpoint (`/v1/status`)

For a **running** node (not just the CLI), the standalone host serves a JSON
status snapshot at `GET /v1/status` (TOOL-OBS-001):

The exact response is mutable. The 2026-07-30 observation is summarized below;
refresh [docs/release-state.json](../../docs/release-state.json) rather than
copying this sample forward after a deployment:

```json
{ "fork_version": 36, "fork_build": "36", "base_version": "2.7.0",
  "commit": "8d4f4531e90e1a2292c0e163f32509d8fdf7c8a0",
  "gpu": { "compiled": true, "accelerated": false,
           "selection": { "mode": "disabled", "configured_device_uuid": null },
           "devices": [],
           "discovery": { "state": "disabled" },
           "configured_vram_budget_bytes": 1073741824,
           "runtime_vram_budget_bytes": null },
  "consumer_registry": { "version": 1, "enabled": false,
                         "effective": false, "revision": 0 },
  "constructs": { "compiled": true, "configured": false,
                  "effective": false, "catalog_ready": false,
                  "catalog_initialization_required": true } }
```

- `fork_version` (a JSON **number** when numeric, or `"dev"`) is the canonical
  field, shared with the static manifest below — a downstream drift-checker reads
  one field name across both surfaces. `fork_build` (always a string) is kept for
  back-compat.
- `gpu.compiled` says whether the binary contains the CUDA search path.
  `gpu.accelerated` is startup readiness: CUDA is compiled, an exact device was
  selected, and a runtime budget exists. It does not assert that a particular
  query launched a kernel; query counters provide that proof.
- `gpu.selection`, `gpu.devices`, and `gpu.discovery.device` distinguish the
  persisted policy, physical inventory, and selected process-local CUDA mapping.
  Discovery state is `disabled`, `no-device`, `discovered`, or `failed`.
- A CPU-only binary reports `compiled:false, accelerated:false` even when a
  device is present. A missing pinned UUID also reports failure and does not
  silently select another device.

Why a host route and not the `fork_health` UTCP tool: `fork_health` is a **guest
module** tool (wasm) and cannot see host GPU state or the baked build number, so
the observability lives at the host layer. It is unauthenticated read-only status
(no secrets), agent-reachable over HTTP, and documented in the fork manual.

## See also

- [Host configuration management](host-configuration-management.md) — the
  deny-by-default typed management plane and build-9 exact GPU selector.

- [Staged builds & guest/host compatibility](staged-builds-and-compat.md) — the
  registry (now with the `Fork` column) and the ABI-pin rules.
- [App hosting & gateway](../web/app-hosting-and-gateway.md) — the runtime rule
  management API (APP-HOST-003) served alongside `/v1/status`.
- [Vector search architecture](../vector/vector-search-architecture.md) — the GPU
  acceleration whose live status `/v1/status` reports.
- [Auto-increment sequence recovery](sequence-recovery.md) — the durability fix
  carried by staged fork build 6.
- [Perception and control substrate](../web/perception-and-control-substrate.md)
  — the mixed host/guest capability carried by staged fork build 7.
