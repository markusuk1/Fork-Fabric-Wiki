# Persistent Contextual Conversation State

> Sources: DIALOGUE-001 research, implementation and observed runtime evidence, 2026-08-09
> Raw: Prior-art research, Implementation evidence
> Commit: unknown
> Updated: 2026-08-09

## Ownership

Conversation is split by fact ownership rather than by presentation layer:

| Fact | Owner |
|---|---|
| Session, ordered transcript, context snapshot, tone, response policy, typed proposal and causal receipt | Fork |
| Actor activation, visible UI, physical interruption/resumption, native Barter and save state | OpenMW |
| Schema-12 retry, acknowledgement, bounded snapshots and reconnect | Companion/protocol 6 |

This is the deterministic contract that later LLM cognition must reuse. An LLM
may propose speech and an allow-listed action, but it cannot write conversation
tables, inventory, gold, crime, quests, memories or routines directly.

## Context and response

Each turn freezes a bounded context snapshot containing the NPC's canonical
profile and current state, relationship, observer-local recognition and
reputation, current routine, recent personal knowledge and relevant market
state. Fork fingerprints the snapshot and stores its source revisions and
evidence references with the turn.

Tier-0/1 policy uses that evidence for tone and for work, recognition, news and
market responses. Missing knowledge is explicitly surfaced; the system does not
invent a fact to fill a dialogue line. `open_barter` becomes the typed
`open_native_barter` action and reaches OpenMW only after reducer validation.

## Lifecycle and replay

One active session binds one player/NPC pair. Expected session and turn
revisions prevent stale or out-of-order changes. Exact replay is harmless;
divergent identity reuse and cross-participant mutation fail closed. Every
accepted turn retains the input, response, tone, fingerprint, evidence, typed
action and status.

Activation interrupts the NPC's current routine under a conversation-owned
cause. Goodbye, cancellation, timeout and the barter transition close or pause
the conversation once and resume only that owned interruption. Save/load
restores the active local window and routine ownership rather than creating a
second session.

## Persistence boundary

Fork history is durable and ordered. OpenMW serializes its bounded outbox and
active local conversation. Loading a save creates a fresh monotonic source
epoch so messages emitted after the save cannot collide with restored sequence
numbers; pending messages retain their original epoch. Acknowledgements apply
only within the matching epoch.

Companion acknowledgements include the accepted line's SHA-256 digest. A record
without a digest is revalidated through Fork rather than accepted from identity
alone. Duplicate causal OpenMW receipts remain no-ops even when a retry supplies
a new receipt ID; divergent causal content is rejected.

## Proven result

Direct Fork tests passed ordered turns, contextual actions, exact replay and
receipt divergence checks. Real OpenMW passed activation, three turns, typed
native barter, exact interrupt/resume and zero final engine/Lua/dead-letter
errors. An active conversation survived OpenMW save/load under a new epoch; its
closed session, transcript and context survived a Fork restart. The inspected
in-game panel shows Arelion's grounded market response, tone, context
fingerprint and source revisions.

## See Also

- [Reliable OpenMW/Fork Bridge Protocol](reliable-bridge-protocol.md)
- [Forkâ€“OpenMW Integration Architecture](integration-architecture.md)
- [Living Village, Commerce, Conversation and Voice](living-village-commerce-dialogue-voice.md)
- [Epistemic Memory and Bounded Recall](epistemic-memory-and-bounded-recall.md)
- [Derived Player Identity, Reputation and NPC Reactions](contextual-presentation-reactions.md)
- [Causal Supply Chain and Scarcity](causal-supply-chain-and-scarcity.md)
