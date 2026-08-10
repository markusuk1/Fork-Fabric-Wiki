# Provider-Neutral Spatial NPC Speech

> Sources: VOICE-001 research/evidence and DIALOGUE-002 final exact live voice qualification, 2026-08-09 to 2026-08-10
> Raw: [Research](../../raw/architecture/2026-08-09-voice-001-provider-neutral-spatial-speech-research.md), [Implementation Evidence](../../raw/architecture/2026-08-09-voice-001-provider-neutral-spatial-speech-evidence.md), [DIALOGUE-002 Final Qualification](../../raw/architecture/2026-08-10-dialogue-002-final-qualification-evidence.md)
> Commit: c0cea44
> Updated: 2026-08-10

## Overview

VOICE-001 is the released presentation layer for deterministic dialogue,
bounded LLM responses and proactive key-NPC speech. It combines native OpenMW
actor-attached `say/isSayActive/stopSay`, Fork-owned intent/Fabric/receipt state,
an external provider-neutral synthesis worker, a content-addressed cache and a
measured 32-file VFS slot ring.

The authoritative artifact is always validated text. Audio may enrich it but
cannot delay, authorize, reverse or mechanically modify gameplay.

## Ownership and flow

```text
validated Fork speech and speaker
  -> voice profile + priority + replaceable policy
  -> native Fabric synthesis claim
  -> provider/cache worker verifies WAV and digest
  -> atomic replacement of allow-listed slot-00..31.wav
  -> companion projects ready intent
  -> actor-local OpenMW say(exact slot, exact subtitle)
  -> started/completed/interrupted/busy/failure receipt
  -> Fork terminal state and causal audit
```

Fork owns stable intent identity, speaker/text/tone, provider profile,
priority, slot allocation, worker leases, retries, expiry, digests, receipt
validation and persistence. The worker owns provider I/O and local artifact
validation. OpenMW owns the loaded actor, spatial audio, subtitle presentation,
voiced animation, actual playback state and save-local deduplication.

## Profiles, providers and rights

The fail-closed voice manifest covers all 11 canonical NPCs exactly once. Each
entry names a provider voice ID, local voice, style, bounded rate and rights
basis. Released profiles use licensed Piper voices as the private,
no-credential baseline. Implemented adapters also cover ElevenLabs and Deepgram;
they use local environment credentials and have no gameplay authority.

Owning Morrowind does not grant performer voice-cloning rights. New synthetic
voices, licensed stock voices or an actor's explicitly authorized clone remain
the accepted sources.

## Cache and fixed-slot contract

The SHA-256 cache identity covers provider, model, provider voice, style, rate
and normalized text. Cache hits are trusted only after RIFF/WAVE bounds,
duration and digest validation. Corrupt entries are regenerated. Cache and slot
writes use temporary files plus atomic replacement.

OpenMW indexes VFS filenames at startup, so the launcher creates exactly 32
allow-listed slots before engine launch. Fork advances a durable modulo-32
allocator. A live OpenMW proof decoded two different texts and audio hashes from
slot 00 before and after allocation 33 while the same process remained alive.
This accepts the file seam and defers a native audio bridge.

## Playback, priority and cancellation

Each NPC's local Lua owns at most one active voice. A lower-priority line gets
an explicit `busy` receipt and subtitle-only fallback. An equal/higher-priority
replaceable line stops the current speech, records `interrupted`, then starts
the replacement. Closing a conversation cancels active speech or rejects a
late ready action as interrupted. Invalid action shape, speaker/path mismatch,
decoder rejection and start/duration failure fail closed.

Started is recorded only after `isSayActive` observes real playback. Completed
is recorded only after previously active speech becomes inactive. Receipt IDs
are replay-safe and bind exact intent, actor, slot and subtitle.

## Failure and continuity

- Synthesis starts with a 30-second end-to-end deadline; a worker claim lasts
  eight seconds and permits two attempts.
- Ready audio has a separate 30-second playback-start deadline.
- Missing/disabled providers, corrupt cache, timeout, invalid result, busy actor
  or playback failure settle to authoritative subtitle-only presentation.
- Active actor speech save/load resumes the same action. A saved `started`
  marker prevents a duplicate durable start receipt even though OpenMW requests
  the audio again after load.
- Ready and playing authoritative intents are projected. If an older OpenMW
  save restores audio already superseded in monotonic Fork state, the actor
  discards the stale local resume when the current projection arrives and emits
  no divergent terminal receipt.
- Stale worker claims return to the native Fabric queue; a replacement worker
  can finish them. Companion restart rebuilds projections from Fork rather than
  local memory. Actual Fork restart preserves profiles, allocator, intent,
  audit and Fabric state exactly.

## Measured result

The final priority run measured queue-to-ready at 276-516 ms and ready-to-start
at 672-814 ms using deterministic Piper fixtures. Live CPU averaged
0.837-1.034 cores across priority, cancellation, save/load and slot-reuse runs.
All final runs had zero engine/Lua errors and zero bridge dead letters.

The retained 12,841 ms Piper WAV and inspected 1280x720 OpenMW framebuffer bind
Arelion, the complete subtitle, slot 00 and SHA-256
`63460e78f89e97c229698d30852f646a0801ea6bd1b9de141d0e2172cd47ca27`.

## Released boundary

Production defaults target `game-openmw-npc-v203`. The final worker and OpenMW
playback/save boundaries independently refuse numeric diagnostic scales and
spoken stage directions, even if an older state bypassed the current dialogue
realiser. ElevenLabs and
Deepgram integration contracts exist but their real provider quality/latency is
not claimed without credentialed calls. Speech recognition, lip-viseme driving,
multiplayer replication and voice cloning are outside VOICE-001.

## See Also

- [Living Village, Commerce, Conversation and Voice](living-village-commerce-dialogue-voice.md)
- [Persistent Contextual Conversation State](persistent-contextual-conversation-state.md)
- [Profile-Derived Natural Deterministic Speech](profile-derived-natural-speech.md)
- [Bounded Key-NPC Cognition Leases](bounded-key-npc-cognition-leases.md)
- [Fork-OpenMW Integration Architecture](integration-architecture.md)
- [OpenMW Capability Baseline](../openmw/capabilities.md)
- [Fork Capability Baseline](../fork/capabilities.md)
