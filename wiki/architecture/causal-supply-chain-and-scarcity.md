# Causal Supply Chain and Scarcity

> Sources: ECONOMY-001 research and implementation evidence, 2026-08-09
> Raw: [Prior-art research](../../raw/architecture/2026-08-09-economy-001-supply-chain-research.md), [Implementation evidence](../../raw/architecture/2026-08-09-economy-001-causal-supply-chain-evidence.md)
> Commit: unknown
> Updated: 2026-08-09

## Ownership

The economy is a dual-runtime causal system with only one owner per fact:

| Fact | Owner |
|---|---|
| Physical stack, holder, actor movement, Barter UI, barter gold, theft/crime, save state | OpenMW |
| Good definition, recipe, production run, provenance lot, delivery order, semantic balance, scarcity and history | Fork |
| Typed schema-11 transport, retry, snapshots and acknowledgement | Companion/protocol 6 |

Fork never invents a physical delivery. OpenMW creates the dispatched stack,
loads the named carrier, executes Travel and transfers the exact quantity. Fork
changes lot ownership and closes the order only after the receipt proves
carrier `before -> loaded -> after` and recipient `before -> after` deltas.

## Bounded model

The released slice defines one finite kwama-egg good, an offscreen Bitter Coast
harvest recipe, one production run, one six-unit lot and one delivery to
Arelion. A market state derives scarcity as the missing fraction of target
stock: 6/6 is available, 3/6 is scarcity 500 and `scarce`, and 0/6 is scarcity
1000 and `depleted`.

Every accepted unit belongs to a lot. Decreases consume lots FIFO. An increase
that cannot be tied to production or delivery is retained as
`unattributed_native_trade`; provenance is never guessed. Tracked eggs are not
in the authored merchant seed, preventing OpenMW restocking semantics from
silently duplicating them.

## Commands, receipts and recovery

Production, delay, command and inventory transitions carry stable IDs and
payload fingerprints. Exact replay is a no-op; divergent replay, stale time,
invalid capacity and incomplete physical evidence fail closed.

The actor-local delivery controller owns a 12-second no-progress watchdog. The
global reconciliation guard is deliberately longer, so a valid arrival cannot
lose a same-frame race. A failed attempt removes only the temporary carrier
stack, records a failed command/receipt and permits one revisioned retry in the
proved route.

OpenMW saves the active carrier command and handled-command set. Loading restarts
that same command once. Protocol-6 capture/retry protects companion outages and
post-commit retries, while Fork persistence retains business state across a
server restart.

## Player consequences

The native merchant inventory remains the mechanical shop truth. A purchase
reduces the physical stack and changes barter gold; theft moves a real item and
uses native OpenMW crime handling. Both produce inventory observations that
consume the same Fork lot and update scarcity. The project does not claim
dynamic native Barter-price control because OpenMW 0.51 exposes no stable Lua
offer hook.

The player-facing status panel reports production, embodied delivery,
transactions, remaining stock, scarcity and merchant gold. It is evidence of
the reconciled state, not a second authority.

## Proven result

The acceptance route produced six eggs, delivered them through Dava, purchased
two and stole one. Fork and OpenMW converged on three eggs, 814 barter gold and
scarcity 500 (`scarce`). Save/load preserved the active command, Fork restart
preserved the final ledger, native theft fired once, and final engine, Lua and
dead-letter counts were zero.

## See Also

- [Fork and OpenMW Capability Matrix](capability-matrix.md)
- [Fork–OpenMW Integration Architecture](integration-architecture.md)
- [Reliable OpenMW/Fork Bridge Protocol](reliable-bridge-protocol.md)
- [Living Village, Commerce, Conversation and Voice](living-village-commerce-dialogue-voice.md)
