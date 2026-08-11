# Staged Builds & Guest/Host Compatibility

> Sources: SpacetimeDB_Builds folder convention; [machine-readable release state](../../docs/release-state.json); ABI incident records (UTCP-LIVE, FIX-BLAST-WIRE); [build-36 production promotion](../../raw/operations/2026-07-30-build36-production-promotion.md); [build-34 rejection audit](../../docs/audits/AUD-CINT-BATCH-001.md); [build-35 CINT batch release](../../raw/engines/2026-07-29-cint-batch-build35.md); [build-36 Construct release](../../raw/engines/2026-07-29-construct-build36.md)
> Commit: 8d4f4531e90e1a2292c0e163f32509d8fdf7c8a0
> Updated: 2026-07-30

## Overview

Release binaries stage to `D:/Projects/SpacetimeDB_Builds/<version-feature>/`
— **a NEW folder per feature, never overwrite an existing one** (folders are
rollback points). Each folder: `bin/` (cli, standalone, update exes),
usually `agent-starter.wasm` beside it, and `build.log`. The CLI stamps the
git commit (`spacetimedb-cli --version`) — verify the stamp matches the
code you think you shipped.

## Version & commit registry (current stage verified 2026-07-30)

Repo version: **2.7.0** on upstream base **v2.7.0-hotfix1**. Current numbered
stage: `v2.7.0-construct-r1` (fork build 36, source `8d4f4531e`).
Build root: `D:/Projects/SpacetimeDB_Builds/` (release builds via
`CARGO_TARGET_DIR=D:/Projects/SpacetimeDB_Builds/target`). The accepted
build-36 stage predates strict `bin/` packaging and stores its executables at
the folder root; its byte-identical production bundle provides the canonical
`bin/` layout without overwriting the accepted stage.

### Fork build number (drift detection)

The **Fork** column is the monotonic fork build number stamped into the exe from
`SpacetimeDB/FORK-VERSION` and shown by `spacetimedb-cli --version` as
`SpacetimeDB fork build <N> (base <upstream-version>)`. Unlike a git commit hash,
`N` is **orderable** — "is my build the latest fork?" is a `>` check, and a
downstream pin is `fork build ≥ N`. It never resets across upstream rebases; the
upstream base version (e.g. `2.7.0`) rides alongside it. **Bump `FORK-VERSION` by
one as part of each staging commit.** Builds staged before the scheme are `—`
(identify those by commit stamp). See [fork versioning](fork-versioning.md).

The registry is append-only release history. Every row describes what was true
when that stage was validated; words such as “newest”, “live” or “stays” inside
an older row are historical row-local facts, not current deployment claims. The
current source/stage/runtime reconciliation follows the table and is also
machine-readable in [docs/release-state.json](../../docs/release-state.json).

| Folder | Fork | Host exe commit stamp | What it carries |
|---|---|---|---|
| `v2.7.0-rebase` | — | `fd97edefd` | REBASE-002 tip. ⚠ IMPLEMENTED_ABI 10.8 bug — rejects 10.9+ modules; 8 s embed default |
| `v2.7.0-reconcile` | — | `b6e740de6` | MEM-RECONCILE. ⚠ same ABI-advertisement bug |
| `v2.7.0-utcp-live` | — | `bcfbce93c` | first ABI-correct host (10.11); tool surface + fork-up wasm |
| `v2.7.0-dims1536` | — | `bcfbce93c` (host UNCHANGED — dims/timeout/band fixes are guest-side, in the wasm beside it) | 1536-dim + 30 s-timeout + 0.60-band agent-starter wasm |
| `v2.7.0-app-host-cebd2e07` | — | `cebd2e07d` | built-in `[app-hosting]` |
| `v2.7.0-app-host-5478fc8` | — | `5478fc830` | app-host line rebuilt after the GNCE HTTP triage |
| `v2.7.0-blast-wire` | — | `631c30e5d` | 8-array causal wire (blast-radius payload) |
| `v2.7.0-cmt-sub` | — | `800b4cb25` | causal tables `Private` + CLI subscribe schema fallback (CMT-SUB); host-only change, wasm carried from blast-wire |
| `v2.7.0-app-host-proxy` | — | `ceedd0147` | native app-host reverse-proxy upstream target (APP-HOST-002): streaming + WebSocket/HMR passthrough + optional Basic auth gate + clean 502 |
| `v2.7.0-app-host-mgmt` | 1 | `aaf0ab7fd` | first fork-numbered build. Runtime app-host rule management API (APP-HOST-003): add/remove routes at runtime, persisted, deny-by-default admin auth + SSRF guard. Host `/v1/status` (TOOL-OBS-001). `FORK_VERSION` single-sourced in `crates/lib`. Default features — **GPU off** (superseded by build 2) |
| `v2.7.0-gpu-mgmt` | 2 | `acfc4c76b` | GPU-accelerated build (`--features cuda`). Build 1 **plus**: version manifest `fork-version.json` + `fork_version` in `/v1/status` (FORK-VER-002); GPU-CUDA-001 wired to the server binary — `/v1/status` `gpu:{compiled:true, accelerated:true}`. Superseded by build 3 |
| `v2.7.0-fork-manifest` | **3** | `6357a67dd` | Build 2 **plus** `GET /v1/fork-version` — the version manifest served node-side (`{fork_version, name, base_version, commit}`), a clean drift-check poll target (FORK-VER-003). `--features cuda`, GPU accelerated. Superseded by build 4. |
| `v2.7.0-opt001` | **4** | `76458c6d8` | OPT-001: pooled procedure HTTP, request duration metrics, normalized recall filters, provider-safe bounded sidecar caches, and bounded cleanup. `--features cuda`; superseded on the LAN service by build 9. |
| `v2.7.0-seq001` | **5** | `6ef0d215e` | **REJECTED — do not deploy.** Initial sequence high-water repair passed the first recovery but failed replay on the second restart. |
| `v2.7.0-seq001-r2` | **6** | `8eef14373` | SEQ-001: replay/publication sequence high-water reconciliation plus replay-stable repaired rows. `--features cuda`; staged and locally verified against preserved AI-Collab data. Its repair is carried by live successor build 9. |
| `v2.7.0-per001` | **7** | `008bee2f1` | PER-001: model-free perception/action guest contracts and reference module plus bounded per-connection encoder/frame subscription stages and queue-pressure metrics. `--features cuda`; exact WASM included. Local preserved-data publish, restart, build-6 rollback, and build-7 restore passed. |
| `v2.7.0-hostcfg001` | **8** | `89815d259` | HOSTCFG-001: deny-by-default typed host configuration list/get/set/apply/rollback for 19 restart-bound settings, conflict-bound pending state, atomic persistence, and one-level rollback. `--features cuda`; host-only, so no WASM rebuild. Preserved-data apply/restart/config rollback and build-7 binary rollback/restore passed. |
| `v2.7.0-hostcfg002` | **9** | `3742ad4dd` | HOSTCFG-002: stable UUID disabled/automatic/pinned database GPU placement; complete inventory and exact physical-to-CUDA mapping; allocator identity verification; dual-GPU vector-kernel proof. Locked `--features cuda` host-only build. Preserved-data config rollback, build-8 binary rollback, build-9 restore, LAN promotion, and LAN restart passed. It was later superseded on LAN; the pinned GTX 1660 Ti allocation moved to AI-Collab's independent local node. Standalone SHA-256 `5DBD935822CA7D2F74396E617FA733284A05D464659378C0B4D3664CEC799E48`. |
| `v2.7.0-pred001` | **10** | `33cc9e5c6` | **REJECTED — do not deploy.** Publisher control used the wrong identity domain and could lock the real publisher out. |
| `v2.7.0-pred001-r2` | **11** | `0cde82f4c` | **REJECTED — do not deploy.** Authorization fixed, but registered CMT tags above 15 were silently relabelled. Retained as a downgrade fixture. |
| `v2.7.0-pred001-r3` | **12** | `aa9dc52dc` | **REJECTED — do not deploy.** Widening the persisted event-kind sum broke databases whose stored schema described the historical enum. |
| `v2.7.0-pred001-r4` | **13** | `b0fc553d4` | **REJECTED — do not deploy.** Exact semantic sidecar table passed forward migration but prevented build 11 from reopening the upgraded replica. |
| `v2.7.0-pred001-r5` | **14** | `88d331a18` | PRED-001 full Fork substrate: default-off deterministic prediction, demand-first preemption, quarantine/exact promotion, packed vector staging/commit, metrics/replay, and rollback-safe CMT metadata semantics. Locked CUDA host plus exact WASMs. Fresh build-11 data, build-14 upgrade/publish, build-11 and build-9 downgrade reopen, build-14 restore, and restart passed. **Validated stage; not promoted to production.** Standalone SHA-256 `8245C6EC2609AA6F0352BC54AC9D34049ED4BBFD9A27EF81EBEDE27823000D78`. |
| `v2.7.0-pred002` | **15** | `51bf1679d` | PRED-002 makes fresh opt-in predictive databases bounded Active by default while generic absent/malformed state remains Off and existing durable configuration is preserved. Locked CUDA host plus exact `fabric_demo` and `fabric_macro_smoke` WASMs. Fresh default-active publish and real prediction, build-14 Off database upgrade, build-15 restart, build-14 rollback, and build-15 restore passed on isolated port 3013. **Validated stage; not promoted to production.** Standalone SHA-256 `49557D2B3A278309BD7C359E58360B332B72B33A71E6742CEABB1847CBCEB32E`. |
| `v2.7.0-pred004` | **16** | `10e5428c5` | PRED-004 adaptive context substrate: item/CMT evidence, exact resolvable references and token batches, signed action policy, quality-first admission, content-addressed stage coalescing, fixed rerank calibration/promotion, and unified deny-only host controls. Locked CUDA host plus exact `context_demo` and `fabric_demo` WASMs. Isolated publish, reducer smoke, restart, build-15 rollback, build-16 restore, emergency-stop disable/re-enable, and seven-round demanded-path checks passed. Standalone SHA-256 `74BA8FD38537C9DEA9B42DA8D8B6227D6F492DF74C146684AA011F62ECF1BCC3`. |
| `v2.7.0-pred004-r2` | **17** | `ed64e1d0b` | Remediated PRED-004 stage: historical item-event retries and exact stage-completion retries are idempotent; conflicting receipts and sequence/basis-point overflow fail closed. Locked CUDA host plus exact `context_demo` and `fabric_demo` WASMs. Isolated publish, exact/conflicting retry smoke, restart, build-16 rollback, build-17 restore, and full Context/Fabric/bindings/standalone/module suites passed. Standalone SHA-256 `67DD3234F7BF068C36A0E026AE41D6AA966F18860BCF9FDE3D1F67310081CCE4`. **Historical LAN promotion; retired legacy host.** The promotion preserved the exact config hash, launch parameters and 12-tool surface. LAN-LEGACY-001 later stopped and startup-disabled that unused service while preserving its binaries/data/JWT state. |
| `v2.7.0-pred006` | **18** | `715dc2f5c` | First PRED-006 native Fabric orchestration stage: atomic generic resource reservations, bounded reusable work graphs, deadline/quality/cost/locality scheduling, chunked artifact staging, permission-bound federation, opt-in macro schema, and independent unified host controls. CUDA host. It was live on the local AI-Collab node during 2026-07-16 validation, then superseded by build 19 after the CHML audit found a High. **Do not deploy as the completed PRED-006 release.** LAN remained on build 17. Standalone SHA-256 `9CC837AE49BE6715143479923CE74E22D6ACEC0D6E2C174CE92B9730160AE785`. |
| `v2.7.0-pred006-r2` | 19 | `af72b3690` | **SUPERSEDED — do not deploy.** Its H1 fix was incomplete (`fail_work_node` still wrote a Failed receipt on a retryable failure → node stranded despite the Pending row). Never deployed; the incompleteness was caught by the build-20 live drill. Standalone SHA-256 `55EE99F0CEF6EBFB9A52CE538A717586A72A3D0A74618A7D80243B79A6B54638`. |
| `v2.7.0-gpu-default` | **24** | `e2fcca615` | **GPU-DEFAULT-001 + TLS-001 at 0/0/0/0 after seven adversarial rounds.** Two capabilities the docs had claimed since build 21 did not actually work, and are real here: **(1) fail-closed domain routing never worked in a browser** — the guard read only the `Host` header, which HTTP/2 does not send (RFC 9113 §8.3.1 uses `:authority`) and the HTTPS listener advertises `h2` first in ALPN, so build 21 answered **421 to every browser request** for a served domain over HTTPS. A fifth round then found the same bug one layer down — past the guard, the app router also read only `Host`, so host-scoped rules were dropped and a host-agnostic rule matched *every* domain. Both closed by one shared `request_host` (authority-first per RFC 7230 §5.4, `host()` not `as_str()` so userinfo cannot smuggle a registered name) plus a `ResolvedHost` the guard publishes. Measured across binaries, same config, `GET /app/path` over real h2 with a host-scoped rule: **421 (build 21) → 404, rule dropped (round 4) → 502, rule matched (build 23)**. Missed for two builds because the local `curl` has no `HTTP2` feature and physically cannot speak the protocol. **(2) HSTS and the HTTP→HTTPS redirect were never implemented** — both were config keys with zero consumers; now real, with per-domain opt-in authoritative in both directions, `includeSubDomains` as its own opt-in, a cert-presence gate and 307-not-308. Also: **7 of 10 `[tls]` keys now apply with no restart** (`apply` reports `restart_required` truthfully per key); **Windows TLS key ACL hardening** (protected DACL: running account + LocalSystem + Administrators — `icacls` showed 6 inherited ACEs incl. a group with Modify, now exactly 3); ACME order resume (a slow CA no longer costs a duplicate-cert slot per retry); and the runtime GPU default (`accelerated: true` with zero `[gpu]` config, deterministic 1024 MiB budget — free VRAM read 9132/6247/6211 MiB across three boots of the same card). Carries all of build 21 (TLS-001) plus GPU. Standalone SHA-256 `098DD81FC8CC23E82E12AC550851B49216CD2086ED343D1940ACFECD65AE35EF`. **Build 24** adds the ACME real-CA fix: instant-acme 0.7.2 could not parse Boulder's `dns-persist-01` (tokenless) challenge — proven against real Let's Encrypt staging, not Pebble — normalized at the transport (dedicated audit 0/0/0/0). **Newest validated stage; NOT LAN-promoted.** |
| `v2.7.0-cuda-default` | **25** | `ae8cb768f` | **GPU on by default at BOTH gates.** `cuda` is now a **default cargo feature**, so a plain `cargo build --release` produces a GPU binary — no flag to forget. It was build-time opt-in over 3 audit Criticals (bare `cargo build`, both Dockerfiles, the macOS release matrix); those are *inherited upstream* artifacts this fork never builds — it has **no fork remote**, so its GitHub Actions never run, and it ships locally-built staged binaries. Deferring to consumers that do not exist let a routine ACME-hotfix rebuild **silently ship a CPU-only binary to the live production node** (`compiled:false`, nothing announced it). A GPU-less build now also **warns loudly at startup**. The inherited Dockerfiles/workflows were deliberately NOT edited (pure rebase-conflict surface for builds that never run here). Proven with the plain command, no `--features cuda` and no `CUDA_PATH`: `compiled:true`, `accelerated:true`, GTX 1660 Ti, 1024 MiB. Carries all of build 24 (TLS-001 + the real-LE ACME fixes). Standalone SHA-256 `353418366D6EF71315FAF408725F41AC02952E5D18C55E5D85740B815824DBEA`. **Staged in its own folder** — build 24 stays intact as a rollback point, restoring the "never overwrite a stage" rule that in-place re-staging had eroded (the live node runs its binary from the staged folder). **NOT LAN-promoted.** |
| `v2.7.0-security-centre` | **26** | `2d2edb2af` | **SEC-CENTRE-001: the native Security Centre.** One typed secret-free event envelope (`core/src/security.rs`) with a process-wide sink registry; a **hash-chained** node-plane store (seq + prev_hash, blake3) plus a **durable monotonic anchor witness**; a bounded Tier B chatter ring (explicitly unchained, labelled 'not evidence'); a deterministic versioned cascade folding events → incidents → posture with citable provenance at every level; explicit retention (30d/365d/7d, all hot); and `/v1/security/{posture,events,incidents,chatter,verify,feed}` behind a deny-by-default admin gate whose reads are themselves audited. Nine emitters converged, closing real blind spots: **every admin/proxy denial site previously logged nothing**, and `TlsEvent::Config*` had had no emit site since TLS-001 P4. A live rollback drill found what five review passes missed — restoring an older store removed 25 events while the chain walk still said `Intact`, because a wholesale rollback is internally consistent; the witness closes it. Re-audited **0 C/H/M/L with R3/R6 recorded as partial** (`AUD-SEC-CENTRE-001-R2`); the FIRST closeout was premature — its audit had only implementation lenses, so it certified an incomplete build, and a completeness pass found six gaps (incidents/posture not persisted, no database plane, ACME unconverged, no metrics, unbounded retention scan, no shutdown path), all since closed and re-drilled. Drills on the staged binary (flood proved `/v1/status` latency unchanged at 0.63 ms; secret grep over 7,418 bytes of live payload including the node's own JWT and private key → 0 hits). Standalone SHA-256 `483D699301565791199005CBCD84C3A1AC45C61D76F62DD905F9E8AA289DCC98`. **NOT LAN-promoted.** |
| `v2.7.0-fabric-spec-cols` | **27** | `6e20ac8d9` | **FABRIC-SPEC-COLS** — the `fabric_payload` macro made `speculation` un-enablable on any existing database. Two independent defects: the three columns were injected **mid-struct**, renumbering every column below them (the auto-migrator rejects a changed `col_id` outright, one error per shifted column — no default value can fix that), and none carried `#[default]`, without which the migrator cannot fill resident rows. Conditional columns now append **after** every unconditional one, each with a default that states what a pre-speculation row actually was: demanded work, no decision, no signature. Macro-only — the host binary is byte-identical to build 26; this stage exists because build 26's binaries carried a **stale commit stamp** (`9cde739fa`, predating its own content) and its CLI predated the macro fix. Stamps here match HEAD exactly. Carries all of build 26 (Security Centre + its six remediated gaps). Standalone SHA-256 `28ECCF55030D81A50E66614AC1E97C560082CE384DF9FF12CAB6DB52E3B11108`; CLI SHA-256 `C99D914DE8739D277ED0F21994D8112B7181CBDFD7E1CFB139DAD1D6566CC406`. Live-proven on the staged CLI: publish without speculation → 3 rows → republish WITH it → **Updated database**, rows intact, `execution_class=0`, no manual migration and no `--delete-data`. **NOT LAN-promoted.** |
| `v2.7.0-service-plane` | **28** | `9c7f6e038` | **REJECTED — do not deploy.** First SVC-001 stage. Its Windows wait bound misclassified a healthy long-lived managed process as failed and closed the kill-on-close Job Object. Preserved as the live-audit fixture; never overwritten or promoted. Standalone SHA-256 `79F48AEEF59F6F40D8AE87E1B12772C8D8BD4A10E7BFD9B9D9E29649D0105E94`. |
| `v2.7.0-service-plane-r2` | **29** | `8efdaadef` | **REJECTED — do not deploy.** Fixed long-lived process waiting and passed lifecycle/restart/emergency-stop/rollback drills, but the re-audit found trusted request IDs repeated across host restarts because their sequence lacked a process-generation nonce. Preserved and unpromoted. Standalone SHA-256 `9D087D43DCCBA81B0A7314270C63B9FF5D5FC543619838AAFC00AFB6932EBCBB`. |
| `v2.7.0-service-plane-r3` | **30** | `862f0a81e` | **SVC-001 generic managed service plane, accepted at CHML 0/0/0/0.** Protocol-neutral static/persisted-external registry, authenticated caller-filtered discovery/admin, streamed HTTP/SSE/WebSocket invocation, enforced contract selection, bounded managed lifecycle with Windows Job Object/Unix process-group containment, native Fabric stream-lifetime admission, unified hot/restart controls, bounded receipts/metrics/Security Centre evidence, and restart-unique trusted request IDs. Live external/managed invoke, health, `401`/`428`/`413` gates, restart, hot emergency stop/rollback, overlay persistence, build-27 rollback and build-30 restore passed. Host-only; no WASM rebuild. Standalone SHA-256 `7615776A5B0697A588322ADA05CED671DE11BE81D428E5B9E4C70B5FE2FBD037`; CLI SHA-256 `41BB4560376B1FC814BFD550F3DD29912868188FD3434A23EC49269A53BBD8CD`. **Staged and usable; NOT production-promoted.** [Final audit](../../docs/audits/AUD-SVC-009.md). |
| `v2.7.0-unified-registry` | **31** | `489077160` | **REJECTED — do not deploy.** Live operator testing proved the CLI rejected compound JSON before the typed host validator, making string-list revocation/admin settings unusable through the unified configurator. Preserved unpromoted. Standalone SHA-256 `6F1EF4225B745C48DC9CB2EE528A2EDD4CBF7A9BC20F640B7BEF3FEC81B2BC8B`; CLI SHA-256 `6FEF6E76FB357623C6F8DF9A1DDD3072B1B9FCE3292230290158FFAB9DED19BB`. |
| `v2.7.0-unified-registry-r2` | **32 packaging attempt** | `001ee6f6d` | **INCOMPLETE — not deployable.** PowerShell treated ordinary Cargo warning output as a terminating logging error before binaries were copied. The partial folder is preserved as packaging evidence and must never be treated as a stage. |
| `v2.7.0-unified-registry-r3` | **32** | `001ee6f6d` | **SUPERSEDED — do not deploy.** Compound JSON reaches the typed server validator, but pre-acceptance review found the generated host-config comments still described the older restart-only/admin-list contract. Standalone SHA-256 `804FEC5FFE07D6E565C87C3F06D2262B020A89007C5A7190B35C1AE3881AC603`; CLI SHA-256 `D5D2A6EC514C9030BA6FAA8117511672B7A4A6EC84C7C295AEE10AADB350DDC2`. |
| `v2.7.0-unified-registry-r4` | **33** | `2c82d1b35` | **UCR-001 unified consumer registry, accepted and production-promoted on 2026-07-29.** Immutable declarative registry kinds, seven system kinds, bounded typed values, scoped CAS entries, canonical BLAKE3 digests, transactional current/history/change state, deterministic config-space merge with provenance, authenticated ACL/revocation, unified hot controls, status/metrics/Security Centre, HTTP and CLI. Live 401/403/409 gates, dynamic kind, path/version, layered resolution, hot revocation/rollback, restart, build-30 rollback and build-33 restore passed with revision 5/four entries intact. Host-only; no WASM rebuild. Standalone SHA-256 `2B6E44480B6D5542E9096A47340FE568A113C72995CDAE8B397EC7A799EEEC81`; CLI SHA-256 `E9F0241CD1F57E3A3E283D35DBBA3E46C3EB94DD9321CD514DB1C479C3BB1703`. Later superseded on the live route; that does not invalidate build-33 acceptance. [Operations article](unified-consumer-registry.md). |
| `v2.7.0-cint-batch-r1` | **34** | `9befa74bc` | **REJECTED — do not deploy as the accepted CINT release.** The primary ABI 10.13 mixed-result/retry/config/restart/full-stage rollback drills passed, but [AUD-CINT-BATCH-001](../../docs/audits/AUD-CINT-BATCH-001.md) found CHML `0/1/1/0`: a lowered live source ceiling aborted the whole batch, and conflict/replacement/legacy identity paths lacked required contract proof. Preserved immutably as audit evidence. Both public/local endpoints and the serving-process path identified this exact stage earlier on 2026-07-30; build 36 later superseded it. That dated observation did not constitute acceptance. Standalone SHA-256 `CAB1F00539D63732FF3094BFA029CF64F7A9822A161106B969CB54F1CECA0698`; causal-code WASM SHA-256 `259E740BCA33F5B5A0FA0E411E03E2088B2F770B9EB548E47B853375A7C4374D`. |
| `v2.7.0-cint-batch-r2` | **35** | `6642d121c` | **CINT-BATCH-001 accepted release candidate.** ABI 10.13 ordered native microbatch results, SHA-256 retry identity, conflict rejection, legacy upgrade, atomic replacement, exact symbols/counts, guest/host hard bounds, hot deny-only `[cint-batch]` controls, status and bounded metrics. Build 35 makes a lowered live source budget reject only the offending item without consuming remaining budget. Exact staged host/WASM drills passed mixed results, retry, `Inserted/Conflict/Replaced/Unchanged`, `[Inserted, Rejected(source_limit), Inserted]`, hot disable/rollback, metrics, restart, compatible build-33 full-stage rollback and build-35 restore. Both host and guest; not production-promoted. Standalone SHA-256 `1C37D358FFB95FD611475C576AAF018EAD7AA9B68EEF4D2F12993D61A6CF1D4F`; CLI `CC5429E88F917411A1796D55ECBA08EDC4F9052BA7E304DECB9441D5CA4E219D`; causal-code WASM `D40960496E1D25403F927FFC17DA18FE9841C0D5F026ED09BCE741B0BB1FFA6B`. [Release evidence](../../raw/engines/2026-07-29-cint-batch-build35.md). |
| `v2.7.0-construct-r1` | **36** | `8d4f4531e` | **CONSTRUCT-001 host-native collaboration rooms, accepted at CHML 0/0/0/0 and production-promoted on 2026-07-30.** Reserved host-owned RelationalDB catalog for rooms, admission, presence, ordered messages/events, blobs, delivery/acks, budgets, operator audit and idempotency; authenticated native HTTP/WebSocket, async-in/steer/turn-boundary tiers, exact-budget catch-up, caller-scoped SQL, operator UI, UCR checks, Security Centre, metrics/status, and typed unified config. Disabled/deny-by-default with restart-bound catalog initialization and hot revocation that closes connected sockets. Exact staged drills passed three-tier admission, replay, durable steer, 1,000 events, drain/ack, scoped SQL, blob full/range/dedup, WebSocket snapshots, operator controls, hot disable/emergency/revocation rollback, restart, build-35 compatibility rollback without touching 17 Construct files, and build-36 restore. Host only; no WASM rebuild. Standalone SHA-256 `287EBBFAA1FEBC6AACB56DFF3DBEB01DAA5210CBDBC61B599961A74A617EFE83`; CLI `03047C9D4A30A45A502E199DDF9E8E28A0F087AC1D0057067C1A96E3C38D6B9D`. [Release evidence](../../raw/engines/2026-07-29-construct-build36.md). |
| `v2.7.0-construct-r1-production` | **36 production layout** | `8d4f4531e` | **Current production bundle.** New rollback-safe folder containing byte-identical accepted build-36 CLI, standalone, and updater under `bin/`. Source/target SHA-256 equality was verified before promotion. Local/public stamps, restart, explicit build-34 rollback, build-36 restore, public HTTP 200, and disabled/uninitialized Construct policy passed. [Promotion evidence](../../raw/operations/2026-07-30-build36-production-promotion.md). |
| `v2.7.0-tls001` | **21** | `4ce716287` | **Superseded by build 22** (same TLS-001 content, but GPU-off — build 22 carries TLS-001 *and* GPU). **TLS-001 native TLS + ACME + public domain routing, DONE + live-proven** (4-lens adversarial audit → 0 C/H/M; [COMP-TLS-001](../../docs/completions/COMP-TLS-001.md), [AUD-TLS-001](../../docs/audits/AUD-TLS-001.md)). Opt-in native TLS edge in `crates/standalone/src/tls/`: tokio-rustls SNI hot-swap (renewals never restart, open connections survive), instant-acme HTTP-01 issuance + renewal daemon, atomic host-side cert store (`bundle.pem` 0600, no key material in tables/status/logs/errors), fail-closed `domain → app` registry (unknown/missing/public-IP Host → 421, trusted-proxy-only forwarded headers, disabled evicted, expired refused), typed `tls.*` host-config + admin-gated domain API + secret-free status endpoint. **Default (no `[tls]`) builds byte-identical.** All 5 acceptance criteria proven live vs a running host + local Pebble ACME (issuance E2E chaining to Pebble root, forced-renewal hot-swap keeping an open TLS connection alive, 421 fail-closed, spoofed forwarded ignored, zero key material, self-signed dev mode). **⚠ Default features — GPU OFF** (TLS-001 is host-side + GPU-orthogonal; a `--features cuda` build 21 from the same commit is the step for a GPU-serving promotion). **Newest validated stage; NOT LAN-promoted** — `:3000` stays 17, the AI-Collab node stays 20. Standalone SHA-256 `4BAA3DB4324EFB000F454FB20CFB12869862B83D18F8274C1CD9AD277AC9249D`. |
| `v2.7.0-pred006-r3` | **20** | `73fb27f6e` | **Remediated + DONE PRED-006, live-proven** (AUD-PRED-008 1 High + 4 Medium → fixes → [AUD-PRED-010](../../docs/audits/AUD-PRED-010.md) 0 C/H/M). H1 completed: `fail_work_node` returns a node to Pending while retry budget remains AND writes a Failed receipt only on a *terminal* failure (a receipt marks a node done; `ready_work_nodes` excludes it). M2: `reserve_resource` renews in place instead of panicking on idempotent retry-after-expiry. **Live behavioural drill (operator identity, isolated `:3094`): claim→fail→RE-CLAIM succeeds→fail→claim rejected; final node Failed/attempt_count=2; M2 re-reserve-after-expiry OK, one renewed row, no panic.** CUDA host + fixed `fabric_demo` wasm (staged wasm byte-identical to the drilled one). **⚠ Module ABI 10.11→10.12** — rollback off an 18+ node is **full-stage** (host + build-17 data/module), never host-only. Completed PRED-006 stage; later stages carry it. **NOT LAN-promoted** — `:3000` stays 17. Standalone SHA-256 `C7099B5CAF175C183D1C358D895A70F3B2EEC56918333893FD5A5A0E8C579805`. |

> **Staged builds are now built `--features cuda`** for GPU hosts (fork build 2 =
> `v2.7.0-gpu-mgmt`, stamped `acfc4c76b`; verify with `bin/spacetimedb-cli.exe
> --version` → `fork build 2`). GPU-CUDA-001 was previously never reachable from
> the server binary (two feature-plumbing gaps — see the CHANGELOG); build 2 fixes
> that and enables it. A cuda binary still runs on GPU-less hosts (cudarc loads
> the driver dynamically and falls back to CPU). Keep `FORK-VERSION` and
> `fork-version.json` in lockstep on every subsequent staging.

Historical row descriptions preserve what was true when each stage was
validated; “newest” inside an older row is not a current-status claim. The
current authoritative summary follows the table.

**Rollback history is 2.7.0-era only.** The older 2.4.1/2.6.0-era lineage
(`v2.4.1*`, `v2.6.0-{tooling,gap-closure,frontier,memory-engine,…}`) is **no
longer on disk** — a 2026-07-17 disk cleanup confirmed zero v2.4/v2.6 staged
folders remain, and removed the last 2.4.1-era artifact (a 62 GB *cargo cache*
at `D:\SpacetimeDB_Builds\v2.4.1`, `debug/`+`release/` — build output, never a
deployable host). Do not plan a rollback below the 2.7.0 staged line.

The newest numbered stage is **`v2.7.0-construct-r1`** (fork build 36,
`8d4f4531e`). Its standalone SHA-256 is
`287EBBFAA1FEBC6AACB56DFF3DBEB01DAA5210CBDBC61B599961A74A617EFE83`.
It is the accepted host-only
[Native Constructs release](../web/native-constructs-proposal.md). Its
byte-identical `v2.7.0-construct-r1-production` bundle is the current
public/local executable. Build 35 remains the accepted host+guest CINT batch
stage, and build 34 remains rejected audit and rollback evidence. Builds 31/32
remain rejected, incomplete or superseded evidence.
The historical
LAN-promoted host was
**`v2.7.0-pred004-r2`** (fork build 17, `ed64e1d0b`). LAN-LEGACY-001 later
retired that unused listener: its service is stopped/startup-disabled, while
the immutable binary, service definition, data and JWT state remain preserved.
The independent public/local node reports CUDA compiled but discovery disabled,
`accelerated=false`, and a configured 1 GiB per-index budget with no runtime
device budget. CUDA is a default Cargo feature from build 25 onward; GPU use
remains runtime-configurable. Build 17's
historical promotion preserved its configuration hash and 12-tool application
surface through a second supervised restart. See
[Legacy LAN Host Retirement](legacy-lan-host-retirement.md). AI-Collab owns the
active local process and its lifecycle.
Maintain this table on every staging: one new row per folder, and re-verify
a stamp with `<folder>/bin/spacetimedb-cli.exe --version` rather than
trusting the folder name.

The completed elevated promotion and original safe procedure are recorded in
resolved [BLK-SEQ-001](../../docs/blockers/2026-07-13-lan-seq001-promotion-blocker.md).

## The compatibility rules (each learned the hard way)

1. **Pin bindings and host from the SAME fork commit.** The causal-query
   wire (8 arrays), ABI import blocks, and result encodings move in
   lockstep; new module + old host breaks in ways that look like data bugs.
2. **Host ABI advertisement**: `WasmtimeModule::IMPLEMENTED_ABI` must equal
   the highest `spacetime_10.x` import block in `wasm_common.rs` — it once
   lagged at 10.8 while 10.11 syscalls existed, so the host REJECTED modules
   using features it implemented. Hosts older than `v2.7.0-utcp-live` still
   have this bug.
3. **ABI additions always get a NEW import block** (`spacetime_10.<n+1>`),
   never grow an existing one — the guest/host block-name skew
   (`10.6` vs `10.10` on weighted traverse) produced "unknown import" at
   publish.
4. Guest-side constants (embed timeout, DIMS) are COMPILED INTO module wasm
   — fixing them means rebuilding the module against the new pin, not
   swapping host exes.

## One-command bring-up

`BIN=<builds-folder> bash SpacetimeDB/tools/fork-up.sh` — server if needed +
canonical publish from the staged wasm + manual check. Idempotent.

## See Also

- [Host configuration management](host-configuration-management.md)

- [Build and test gotchas](build-and-test-gotchas.md)
- [Auto-increment sequence recovery](sequence-recovery.md)
- [Perception and control substrate](../web/perception-and-control-substrate.md)
- [Causal-query wire contract](../cmt/causal-query-wire-contract.md)
- [DeepInfra latency and timeouts](../embeddings/deepinfra-latency-and-timeouts.md)
