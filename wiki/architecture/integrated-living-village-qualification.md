# Integrated Living-Village Qualification

> Sources: QA-001 existing-solution research and observed integrated evidence, 2026-08-10
> Raw: [QA-001 Research](../../raw/architecture/2026-08-10-qa-001-integrated-qualification-research.md), [QA-001 Evidence](../../raw/architecture/2026-08-10-qa-001-integrated-living-village-evidence.md), [TEST-001 Evidence](../../raw/architecture/2026-08-10-test-001-owned-regression-isolation-evidence.md)
> Commit: c0cea44
> Updated: 2026-08-10

## Qualification model

QA uses **Combine**, not a monolithic scripted demo. One real OpenMW run owns
the living-day, cell-transition and sustained-resource boundary. Native
save/load and injected companion outage remain separate owned runs because
their destructive transitions need independent causality. Current-module
reducers, restart/replay, voice and spoken-text gates complete the dashboard.

Every mutating run uses TEST-001's current-module, random-port, token-owned Fork
context. The persistent production database is read only and canonically
fingerprinted across all public user tables before and after.

## Released gates

| Gate | Observed release evidence |
|---|---|
| Living day | 26 clock observations, 27 station arrivals, 10 activities, 10 states/reservations, one repair |
| Cells and embodiment | One exterior/Tradehouse/exterior player round-trip, 34 lifecycle events, 10 stable object identities |
| Sustained resources | 120 seconds, 2.396 CPU cores, 465.5 MiB peak, 2.8 MiB post-warm growth |
| Save/load | 6/6 active routine and perception states exact; 10 embodiments; 32 lifecycle events |
| Outage/recovery | Exit 86, 46 stable resends, 10/10 offline observations, zero final pending/dead letters |
| Voice | Stale lease reclaim, identical projection, real 5.877-second actor playback and exact receipts |
| Cross-system | Dialogue, journal, perception, reputation, commerce, cognition, routine and voice reducers pass |
| Production | 113-table v202 digest unchanged; owned directory and process sets unchanged |

Final engine and Lua errors are zero throughout the engine-backed cases. Direct
negative fixtures may intentionally create bounded Fabric audit/dead-letter
rows inside disposable databases; those are expected rejection evidence, not
final bridge errors.

## Evidence surfaces

- Structured: [QA dashboard](../../docs/evidence/QA-001-dashboard.json).
- Visual: [native OpenMW save preview](../../docs/evidence/QA-001-save-preview.jpg).
- Audio: current actor-attached playback receipts plus the retained VOICE-001
  WAV referenced from the provider-neutral speech article.

WGC remains unavailable with Windows `0x80070424`, so QA does not relabel the
save preview as WGC evidence and does not claim a new automated UWQHD frame.
The ordinary playable UWQHD/market-reply observation remains DIALOGUE-002's
separate owner-input blocker.

## Operational command

Run the dashboard only when OpenMW is closed:

`powershell -NoProfile -ExecutionPolicy Bypass -File tests/test-qa-integrated-living-village.ps1 -ProductionDatabase <current-production-database> -ProductionServer <current-production-server>`

Production arguments are mandatory because they are used only for explicit
before/after read-only fingerprinting; no production mutation is performed.

## See Also

- [Production Living-World Gap-Closure Programme](system-gap-closure-programme.md)
- [Owned Fork Regression Isolation](../workflow/owned-fork-regression-isolation.md)
- [Daily NPC Routines and Terminal Repair](daily-routines-and-terminal-repair.md)
- [Provider-Neutral Spatial NPC Speech](provider-neutral-spatial-speech.md)
