# Profile-Derived Natural Deterministic Speech

> Sources: DIALOGUE-002 research, corrections and final live qualification evidence, 2026-08-09 to 2026-08-10
> Raw: [Prior-art and design decision](../../raw/architecture/2026-08-09-dialogue-002-natural-deterministic-speech-research.md), [Qualification evidence](../../raw/architecture/2026-08-09-dialogue-002-natural-deterministic-speech-evidence.md), [Spoken diagnostic leak correction](../../raw/architecture/2026-08-09-dialogue-002-spoken-diagnostic-leak-correction.md), [Owner audio rejection and final voice boundary](../../raw/architecture/2026-08-09-dialogue-002-owner-audio-rejection-and-voice-boundary.md), [WGC process-isolation research](../../raw/architecture/2026-08-10-dialogue-002-wgc-process-isolation-research.md), [Final qualification](../../raw/architecture/2026-08-10-dialogue-002-final-qualification-evidence.md)
> Commit: c0cea44
> Updated: 2026-08-10

## Purpose

DIALOGUE-002 removes policy and delivery directions from spoken dialogue while
keeping deterministic tiers grounded, replay-safe and recognisably individual.
It adapts the existing profile/context authority instead of installing a second
dialogue runtime or using an LLM for ordinary lines.

## Speech identity

Fork derives four bounded fields from the canonical profile:

| Field | Examples | Main inputs |
|---|---|---|
| Register | polished, precise, plain, conversational | reasoning and speechcraft |
| Cadence | terse, measured, expansive | patience, sociability and speechcraft |
| Manner | formal, considerate, forthright, inquisitive, exacting, frank, pragmatic | values and traits |
| Role voice | merchant, guard, healer, craftsperson, fisher, clerk, host | canonical role |

Tone remains current-state metadata such as neutral, warm, wary or
businesslike. None of these labels is prepended to `npc_text` or sent to voice
as words to pronounce.

## Realisation and stability

Tier-0/1 realisation chooses bounded templates for introductions, work,
observer-local reputation, market facts and barter. Facts and typed actions
still come from the captured context; style only changes wording and cadence.
Stable variation uses speaker/profile/action/turn identity. Mutable facts alter
the resulting text and cache key when relevant, but an unrelated state revision
does not randomly change the speaker's ordinary phrase choice.

Exact replays retain the same spoken text, style tuple and variant. The
companion projects those fields separately and OpenMW renders them separately
from the clean response. The player-facing panel shows only the speaker and
natural response; structured transcripts and `[GAME_OPENMW_DIALOGUE_STYLE]`
logs retain the inspectable style and causal metadata.

Normalized simulation values are never prose. Market units and scarcity remain
exact fields on each turn, while bounded phrases describe the condition to the
player. Owner observation proved that realiser/model validation alone was not a
sufficient claim: an older runtime audibly pronounced `0/1000`. The current
contract therefore rejects numeric scales, diagnostic labels and stage
directions at the common Fork voice queue, activation and stale-intent sweep,
again in the synthesis worker, and again when OpenMW receives or restores a
saved voice action.

## LLM boundary

Bounded cognition can supply free speech, but Fork rejects empty text, reserved
control markers, unsupported evidence/actions and known spoken stage-direction
prefixes before committing the proposal. The context packet explicitly tells
workers that tone is metadata. Accepted model text is retained exactly; it is
not silently rewritten by the deterministic formatter.

## Voice and load reconciliation

Voice synthesis receives the exact validated clean line plus separate tone and
profile metadata. A 30-second bounded admission window supports four ordinary
natural lines on the proved serial Piper path. Cache identity still covers all
audio-affecting fields and normalized text.

Fork is monotonic across native OpenMW loads. The companion therefore projects
both ready and currently playing authoritative intents. If an older OpenMW save
contains a voice that Fork has since superseded, the actor discards that local
resume when the current intent arrives and does not emit a divergent duplicate
terminal receipt.

## Released evidence boundary

Eleven profiles produce eleven distinct style tuples and work responses. Direct
replay, clean LLM acceptance/rejection, cache reuse/corruption recovery,
timeouts, proactive cognition, Fork restart, active OpenMW dialogue save/load
and production auto-resume pass. Production defaults target v203.

The final strict background WGC run captured an inspected exact 3440x1440 frame
at GUI scale 1.50. Arelion's visible market line naturally described six units
and scarcity zero without exposing `0/1000`; its exact separate machine fields
remained available for audit. The corresponding 6,467 ms Piper WAV used the
same subtitle and digest as the authoritative voice projection. The run ended
with zero pending work, dead letters, engine errors or Lua errors. See the
[final qualification](../../raw/architecture/2026-08-10-dialogue-002-final-qualification-evidence.md).

## See Also

- [Persistent Contextual Conversation State](persistent-contextual-conversation-state.md)
- [Provider-Neutral Spatial NPC Speech](provider-neutral-spatial-speech.md)
- [Unified NPC Profile and State](unified-npc-profile-and-state.md)
- [Bounded Key-NPC Cognition Leases](bounded-key-npc-cognition-leases.md)
