# Decisions & Rejected Paths (do not re-litigate)

> Sources: fork-ingest-manual §11; [docs/FORK-CLIENT-SURFACE-DECISION.md](../../docs/FORK-CLIENT-SURFACE-DECISION.md); CHANGELOG decision entries

## Overview

Settled decisions with their real reasons and re-open triggers. These look
like gaps if you don't know they're choices; re-opening one without its
trigger firing wastes everyone's time.

## The table

| Decision | Why | Re-open when |
|---|---|---|
| **No WebSockets in the web platform** | subscriptions are the native push channel (typed, transactional — strictly stronger); SSE covers plain-HTTP push | a browser→server or binary bidirectional need neither covers |
| **No TS/C# module-SDK parity; the web platform is THE client surface** | one HTTP endpoint serves every language; primitives are most valuable composed inside the tx boundary | a consumer must author *modules* (not clients) in TS/C# |
| **UTCP-first, no bespoke MCP server** | wrapper-tax logic of the client-surface decision; the official bridge covers MCP clients | a hub requirement the manual/tag set cannot express |
| **Graph trust is a guest-computed ledger, not a host engine** | trust semantics are domain policy; measured host propagation ≈ chance | transitive trust needed transactionally with edge mutation |
| **Quantized (RaBitQ) search REMOVED** | built, measured 0.42–0.78 recall vs a 0.90 bar — killed by its own numbers; IVF cell routing shipped instead | a codec beating the bar on the same adversarial bench |
| **LoCoMo / SWE-bench RETIRED** | training contamination — scores measure memorization | never; use the eval protocol (post-cutoff data if real text is required) |
| **Canonical 1536 dims** (Qwen3-8B) | 16/32 were demo cuts discarding ~98% of model signal; matches the hub | model/provider change → re-measure everything |
| **Contradiction band floor 0.60** | measured live at 1536 dims (true pair 0.692, noise 0.45–0.49); 0.70 was a 32-dim calibration | any model/dims change |
| **Embed timeout 30 s** | live 8B calls take 7–24 s; 8 s sat on the wire | persistent >30 s latency → provider review, not a bigger ceiling |
| **Guest SSE = finite tails** | no reactor in the guest; an "infinite" guest stream would be a lie — the WS subscription is the infinite tail | a host-side reactor seam if plain-HTTP infinite tails become a hard requirement |
| **Causal tables → Private + owner-scoped (CMT-SUB)** | causal rows are activity metadata about every table; blanket-public leaks private tables' activity | a multi-tenant read-grant model |
| **Reverse-edges syscall deferred** | reconcile propagation's bounded scan is O(256×3×1) at defaults — a syscall is cure-worse-than-disease today | organizational stores outgrow the scan bound |
| **No native visual-cognition tables** (`visual_entity`, `visual_state`, …) | PER-001/PER-002 supply model-free *contracts* — ordered ticks, canonical state, action/expectation/verification — not visual cognition. Visual ontology is domain content and belongs in a guest module over the perception substrate, exactly as `vector.*` stayed an application convention over Fabric. Proposed by the 2026-07-16 real-time-visual concept; rejected on the substrate's own boundary | a real visual producer proves a stable, vendor-neutral capability/clock-calibration shape (PER-002's own trigger) |
| **Fabric speculation stays pure/idempotent payload results** | the primitive promotes a bounded, replayable, side-effect-free result by exact-signature equality. Repository *scaffolds* (file trees, symbols, wiring, migrations) are unbounded and side-effect-shaped — outside the model. Scaffold work can ride generic Fabric as an **opaque application payload** with zero engine change, like the GPU workers do | a measured case needs the engine itself to quarantine + atomically promote an opaque artifact (e.g. a file-tree diff into a sandbox) — a real design gap, not an extension |

## See Also

- [Agent workflow and standards](agent-workflow-and-standards.md)
- [Evaluation protocol](../vector/evaluation-protocol.md)
