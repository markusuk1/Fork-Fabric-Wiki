# Knowledge Base Index

## engines

The three composed engines: knowledge, work-in-motion, and attention.

| Article | Summary | Updated |
|---------|---------|---------|
| [Memory Engine](engines/memory-engine.md) | remember/recall/forget/summarize/consolidate/dream + the MEM-RECONCILE loop; lifecycle arcs; determinism contract | 2026-07-11 |
| [Fabric Engine](engines/fabric-engine.md) | Payload lifecycle, no-double-claim guarantee, demand-first prediction, exact promotion, by-reference binary results, and native generic resource admission | 2026-07-24 |
| [Fabric Orchestration Platform](engines/fabric-orchestration-platform.md) | Build-20 leased reservations, bounded DAGs, deterministic scheduling, chunked artifacts, federation, deny-only unified controls, live retry proof, and build-27 migration compatibility | 2026-07-24 |
| [Predictive Payload Speculation](engines/predictive-payload-speculation.md) | Bounded Active prediction plus build-17 signed actions, quality-first admission, exact retry-safe stage coalescing, and baseline-backed rerank calibration | 2026-07-16 |
| [Context Engine](engines/context-engine.md) | Budgeted working sets plus typed, digest-bound commitments, fail-closed exact unfolding, and accepted selective exact assembly proof | 2026-07-16 |
| [Adaptive Context Policy and Item-Level Utility](engines/adaptive-context-policy.md) | Evidence-first application architecture and frozen ablations; generic Fork interfaces remain current while GPU routing stays application-owned | 2026-07-30 |
| [Adaptive Context Substrate](engines/adaptive-context-substrate.md) | Build-17 item/CMT evidence, resolvable refs, exact token batches, signed actions, quality gate, retry-safe stages, and carry-forward into later builds | 2026-07-24 |
| [Worldline Engine](engines/worldline-engine.md) | Phases 0-6 and P7-NEST accepted internally; P7-DIST active; complete Phase 7 remains unshipped | 2026-08-12 |

## vector

Native ANN search: architecture, scale envelope, and how quality claims are proven.

| Article | Summary | Updated |
|---------|---------|---------|
| [Vector Search Architecture](vector/vector-search-architecture.md) | LSM-VEC tiering, IVF cell routing, the 1M gate (recall 1.0 @ 5.9 ms), per-index metrics, multi-vector, doctor + historical bugs; CUDA compile-default since build 25 with separately configurable runtime placement | 2026-07-30 |
| [Fabric-Buffered Dual-GPU Vector Ingestion](vector/fabric-buffered-vector-ingestion.md) | Fork packed staging plus build-20 reservations/orchestration and the AI-Collab boundary; `vector.*` names remain application payload IDs | 2026-07-24 |
| [Fabric GPU Worker Guest Integration](vector/fabric-gpu-worker-integration.md) | Build-20+ boundary, one-queue/two-worker lifecycle, subscription pre-emption, generic leased VRAM admission, and queue placement inputs | 2026-07-24 |
| [Evaluation Protocol](vector/evaluation-protocol.md) | LoCoMo/SWE-bench retired (contamination); run-time-generated ground truth; the planted-fact + planted-contradiction grids; dominance argument | 2026-07-11 |

## graph

Native edges, path traversal, staged mutations, and capability policies.

| Article | Summary | Updated |
|---------|---------|---------|
| [Graph Memory](graph/graph-memory.md) | Edge model, weighted traversal, gate-staged mutations, capability policies (incl. the same-tx fail-open lesson), trust-as-guest-ledger | 2026-07-11 |

## cmt

The Causal Memory Twin: why-chains, counterfactuals, code intelligence, and the guest-facing contracts.

| Article | Summary | Updated |
|---------|---------|---------|
| [Causal Memory Overview](cmt/causal-memory-overview.md) | The why/effects/impact/counterfactual surface, replay capsules, collision-safe tags through predictive 50-59, and rollback-safe exact semantic envelopes | 2026-07-15 |
| [Code Intelligence](cmt/code-intelligence.md) | Live code-symbol graph, incremental re-index, query surface, CINT↔CMT blast-radius bridge, and ABI 10.13 bounded native microbatch ingest | 2026-07-29 |
| [Bounded Native CINT Batch Ingest](cmt/bounded-native-cint-batch-proposal.md) | Accepted build-35 ordered microbatch contract, now carried by production build 36 | 2026-07-30 |
| [Causal-Query Wire Contract](cmt/causal-query-wire-contract.md) | The 8-array product, legacy-prefix compat, pin-lockstep rule, the silent-empty failure mode | 2026-07-11 |
| [Causal Evidence Subscriptions (CMT-SUB)](cmt/causal-evidence-subscriptions.md) | DELIVERED: causal tables Private (owner-scoped SQL+subscribe), CLI schema fallback (the error was client-side all along), same-update evidence tests; rejected alternatives | 2026-07-11 |

## web

The web/app platform: the client surface, the tool surface, hosting, and push.

| Article | Summary | Updated |
|---------|---------|---------|
| [Web Platform](web/web-platform.md) | Router/extractors, pinned middleware, reads-vs-reducer-writes, streaming pool, the with_ctx pattern, production hardening template | 2026-07-11 |
| [UTCP Tool Surface](web/utcp-tool-surface.md) | Router-derived manuals, fork_* namespace + band/cap/cost tags, the 12 reference agent-starter tools, MCP bridge, and historical interop proof | 2026-07-30 |
| [App Hosting & Gateway](web/app-hosting-and-gateway.md) | Built-in [app-hosting] host/path rules; native reverse-proxy `upstream` target (APP-HOST-002): streaming + WebSocket/HMR passthrough + optional Basic auth gate + clean 502; **runtime rule management API (APP-HOST-003): add/remove routes with no restart, persisted, deny-by-default admin auth + SSRF guard**; the optional allowlist Caddy edge; process model | 2026-07-11 |
| [Streaming & Live Events](web/streaming-and-live-events.md) | WS subscriptions vs finite SSE tails, the configurable app_event feed, what push unlocks, live proof | 2026-07-11 |
| [Perception and Control Substrate](web/perception-and-control-substrate.md) | Model-free ordered perception, bounded delivery, and the external GPU/driver producer boundary with calibrated timing and recovery | 2026-07-14 |
| [Native Constructs — Host-Native Collaboration Rooms](web/native-constructs-proposal.md) | Production build-36 reserved relational collaboration catalog, present but disabled and uninitialized by default | 2026-07-30 |

## embeddings

The real embedding backend: measured behavior and every calibrated number.

| Article | Summary | Updated |
|---------|---------|---------|
| [DeepInfra Latency and Embed Timeouts](embeddings/deepinfra-latency-and-timeouts.md) | Real latency 7–24 s/call; timeout layering (guest overrides host); retry-once beats bigger ceilings | 2026-07-11 |
| [Embedding Dimensions and Similarity Calibration](embeddings/dimensions-and-similarity-calibration.md) | Canonical 1536 dims; cosines shrink as dims grow — band floor 0.60 measured live; re-measure on any change | 2026-07-11 |

| [Local Model Sidecar Cutover](embeddings/local-model-sidecar-cutover.md) | Model-free URL-only cutover with exact loopback egress, pooled procedure HTTP, request metrics, and provider-safe bounded embed/rerank caches | 2026-07-12 |

## operations

Running, building, staging, and upgrading the fork for real.

| Article | Summary | Updated |
|---------|---------|---------|
| [Native TLS, ACME, and public domain routing](operations/native-tls-and-acme.md) | Opt-in native TLS termination, HTTP/2-correct fail-closed routing, hot SNI/ACME, Windows key hardening, and the build-24 real-CA parser fix; carried by build 27 | 2026-07-24 |
| [Security Centre](operations/security-centre.md) | Unified typed security evidence in one anchored node chain; tenant events are identity-scoped, not native per-database SQL/subscriptions; final six-gap remediation and 0 C/H/M/L re-audit | 2026-07-24 |
| [Live Deployment](operations/live-deployment.md) | Source, accepted stage, production bundle, and public/local endpoints agree on build 36 | 2026-07-30 |
| [Legacy LAN Host Retirement](operations/legacy-lan-host-retirement.md) | Why the unused :3000 agent-starter host was retired, proof it is disabled, preserved rollback state, and the no-accidental-reactivation rule | 2026-07-30 |
| [Build & Test Gotchas](operations/build-and-test-gotchas.md) | AppControl 4551, the two-scope check rule, swallowed pipes, the WSL-stub trap, elevation, anonymous-publish identity | 2026-07-11 |
| [Staged Builds & Compatibility](operations/staged-builds-and-compat.md) | Immutable registry through accepted and production-promoted build 36, including byte-identical production layout | 2026-07-30 |
| [Auto-Increment Sequence Recovery](operations/sequence-recovery.md) | Replay/publication high-water reconciliation for user, Fabric, and causal tables; replay-stable repair and build-6 evidence | 2026-07-13 |
| [Upstream Rebase Process](operations/upstream-rebase-process.md) | Drift script → worktree subtree-merge → validation ladder → promote; inherited-red discipline | 2026-07-11 |
| [Fork Versioning & Drift Detection](operations/fork-versioning.md) | Monotonic source/stage/runtime build 36 with provenance-separated drift surfaces | 2026-07-30 |
| [Host Configuration Management](operations/host-configuration-management.md) | Deny-by-default typed management through build 36, including registry, CINT-batch and Construct policy | 2026-07-30 |
| [Generic Managed Service Plane](operations/managed-service-plane.md) | Build-30 protocol-neutral service plane carried by production build 36; present but ineffective by policy | 2026-07-30 |
| [Unified Consumer Registry and Configuration Spaces](operations/unified-consumer-registry.md) | Build-33 accepted registry/config-space kernel carried disabled by production build 36 | 2026-07-30 |

## workflow

How agents work in this repo and what's already been decided.

| Article | Summary | Updated |
|---------|---------|---------|
| [Agent Workflow & Standards](workflow/agent-workflow-and-standards.md) | CRAFTE, completion rules, provenance-separated release state, semantic validation, and bounded append-only noticeboard discipline | 2026-08-09 |
| [Decisions & Rejected Paths](workflow/decisions-and-rejected-paths.md) | The 14 settled decisions with real reasons and re-open triggers — do not re-litigate; now including no native visual-cognition tables (guest module over PER-001) and Fabric speculation staying pure/idempotent (scaffolds ride generic Fabric as opaque payloads) | 2026-07-16 |
| [AI-Collab A/B/C Benchmark Reference](workflow/ai-collab-abc-benchmark-reference.md) | Controlled A/B/C design plus observed 25-task B/C calibration, evidence links, quality/efficiency tradeoff, limitations and domain ownership | 2026-07-16 |

## requests

Cross-repository questions and their durable Fork responses.

| Article | Summary | Updated |
|---------|---------|---------|
| [AI-Collab GPU Scheduling Guest Integration](requests/2026-07-16-ai-collab-gpu-scheduling-guest-integration.md) | Answered and reconciled: build 20+ supplies generic reservations/orchestration, build 27 fixes speculation migration, while named `vector.*` schemas, model execution, and placement remain AI-Collab-owned | 2026-07-24 |
