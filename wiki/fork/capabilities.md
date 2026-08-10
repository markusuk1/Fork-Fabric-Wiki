# Fork Capability Baseline

> Sources: Fork build-36 release state, complete manual, source tree, maintained Fork wiki and LLM-002 runtime evidence, inspected 2026-08-09
> Raw: [Fork Build 36 Capability Inventory](../../raw/fork/2026-08-07-fork-build36-capability-inventory.md), [LLM-002 Evidence](../../raw/architecture/2026-08-09-llm-002-proactive-observation-evidence.md)
> Commit: 4cdd46b
> Updated: 2026-08-09

## Overview

The Fork is the proposed durable nervous system and cognitive substrate for the
game. Its released strengths are transactional state, live subscriptions,
semantic retrieval, native graphs, causal evidence, budgeted context and
durable work orchestration. It is not a renderer, game simulation loop or LLM
runtime.

The production baseline is Fork build 36 on SpacetimeDB 2.7.0. Worldline code
in the neighboring Fork checkout is under active development and must not be
treated as shipped.

## Capability map

| Area | Released capability | Game use | Boundary |
|---|---|---|---|
| Transactional core | Serial reducers, durable tables, commitlog/snapshots, live table subscriptions | Persistent game-brain state and event delivery | Do not put frame-time physics here |
| Vector retrieval | Exact/IVF ANN, three metrics, filters, multi-vector aggregation, optional CUDA | Recall memories, lore, situations and similar entities | Embedding/reranking models are external |
| Native graph | Weighted/confident edges, traversal and staged mutation | Relationships, factions, ownership, knowledge and plans | Domain truth/trust is module policy |
| CMT | Why/causes/effects/blast-radius/counterfactual evidence | Explain NPC decisions and trace consequences | Counterfactual query is not an executable alternate world |
| CINT | Live code-symbol graph and bounded batch ingestion | Developer/agent understanding of the project | Not a gameplay reasoning engine by itself |
| Memory Engine | Remember, fused recall, summarize, consolidate, reconcile, quarantine | Long-lived episodic/semantic NPC and world memory | Caller judges semantic contradictions |
| Fabric Engine | Durable jobs, claims, leases, retries, aging, replay, dead letters | Coordinate LLM, embedding, voice and simulation workers | Workers/models/placement remain external |
| Context Engine | Exact token/item/byte budgets, tiered references, verifiable unfolding | Assemble grounded NPC/agent prompts | KV/latent state is outside the trusted store |
| Perception/control | Ordered idempotent observation batches, action expectations and verification | Commit semantic game events and verify applied intentions | Raw frames and 30-120 Hz control stay local |
| Web/streaming | Module HTTP, finite SSE, infinite WebSocket table subscriptions | Cross-language bridge, dashboards and live tools | Handler writes still go through reducers |
| Operations | App hosting, proxying, TLS/ACME, config/status, Security Centre | Run and inspect the game-brain service | Several surfaces are opt-in or disabled |
| Registry/services | Typed registries and managed service invocation | Discover workflows/tools and mediate local workers | Present but disabled in the observed runtime |
| Constructs | Durable collaboration rooms and operator controls | Future human/agent oversight | Present but uninitialized/disabled |

## What is effective in the observed deployment

The build-36 binary carries more features than the live configuration enables.
Adaptive Context, Fabric orchestration, CINT batching and Security Centre are
effective. CUDA is compiled but GPU discovery/acceleration is disabled. Consumer
Registry, managed service hosting and Constructs are available in the binary
but not effective. Architecture must state both binary availability and runtime
configuration whenever it relies on an optional surface.

## Game-facing use

The first module should use the released primitives conservatively:

- tables for actors, saves, observations, beliefs, intentions and receipts;
- Memory Engine for recall over bounded evidence;
- graph edges for relationships, factions and knowledge provenance;
- CMT for decision and consequence traces;
- Context Engine for prompt/work packets;
- Fabric for external inference and background simulation;
- Perception batches for ordered OpenMW semantic deltas;
- table subscriptions for commands and state relevant to the active player/cell.

The module remains the semantic authority. External LLM output is a proposal or
piece of evidence until a reducer validates and commits it.

LLM-001 now exercises this boundary rather than merely planning it. Native
Context frames retain recoverable personal-memory references under exact
item/byte/token limits; native Fabric owns the external work lifecycle; and
domain reducers independently validate structured results before they enter
ordinary dialogue or routine-command receipts. The model worker remains
external and provider-neutral.

LLM-002 also exercises event-triggered proactive work. Domain reducers derive
salience and cooldown from canonical state, create a 15-second native
Context/Fabric lease with only wait/initiate actions, retain rejected physical
receipts, and deterministically settle changed-context, provider-exhausted and
expired work. Actual Fork restart proof preserves the encounter, cooldown,
receipt and conversation/routine causal history.

VOICE-001 exercises the same boundary for presentation work. Domain tables bind
validated speaker/text/profile/priority to a durable modulo-32 slot; native
Fabric owns claim, retry, expiry, completion and dead-letter state; external
workers return digest-checked audio; and exact OpenMW receipts close playback.
Stale workers, companion restart and actual Fork restart cannot duplicate or
lose ownership. Providers and audio decoding remain external to Fork.

## Performance and safety boundaries

- Never round-trip movement, collision, animation, targeting or combat frames
  through Fork.
- Batch meaningful state changes; reference large artifacts by digest/URI.
- Preserve ordering with source/epoch/sequence, not wall-clock comparisons.
- Make every command idempotent, expiring and verifiable.
- Keep credentials and model/provider secrets in the companion/worker layer.
- Treat subscriptions as committed state delivery, not an unbounded arbitrary
  WebSocket service owned by a guest module.

## Worldline status

Worldline is intended to create deterministic copy-on-write alternative
database realities, compare outcomes and promote an approved result only after
complete dependency validation. Its first three internal gates through Phase 2
are accepted, while Phase 3 remains active. It is not part of production build
36, has no shipped public/operator surface and is excluded from the baseline.

If eventually shipped, it may support bounded tournaments over narrative,
economy or NPC-plan outcomes. That would complement OpenMW; it would still not
replace OpenMW physics/rendering or permit speculative external effects.

## See Also

- [OpenMW Capability Baseline](../openmw/capabilities.md)
- [Fork and OpenMW Capability Matrix](../architecture/capability-matrix.md)
- [Integration Architecture](../architecture/integration-architecture.md)
