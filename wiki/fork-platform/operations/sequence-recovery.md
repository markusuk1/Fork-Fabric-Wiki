# Auto-Increment Sequence Recovery

> Sources: AI-Collab downstream evidence, 2026-07-13; Fork SEQ-001 regression evidence, 2026-07-13; LAN build-9 promotion, 2026-07-14
> Raw: [Sequence Recovery High-Water Repair Evidence](../../raw/operations/2026-07-13-sequence-recovery-high-water.md); [LAN build 9 promotion evidence](../../raw/operations/2026-07-14-lan-build9-promotion.md)
> Commit: 3742ad4dd
> Updated: 2026-07-14

## Overview

An auto-increment cursor is durable state, but recovery correctness is defined by both that cursor and the rows it covers. After replay or a non-destructive schema publication, the next generated identifier must be beyond every preserved identifier. SEQ-001 makes that invariant datastore-owned for ordinary module tables, generated Fabric transition tables, and Fork system tables such as `st_causal_event`.

## Recovery invariant

For a positive-increment sequence, the repaired allocation is at least `max(existing identifier) + increment`. For a negative-increment sequence, it is at most `min(existing identifier) + increment`. The implementation scans only the directional extreme and reports exhaustion instead of wrapping into an occupied identifier.

Reconciliation runs in two places:

- after commit-log replay has rebuilt missing schema state and before indexes are rebuilt;
- inside the transaction that completes non-destructive module publication or migration, including unchanged tables and Fork-owned sidecars.

This keeps the repair below consumer code. Modules must not add parallel ID allocators, delete audit rows, suppress causal events, or republish with data deletion to mask drift.

## Replay-stable repair

The initial repair exposed a second-order case: a later logged transaction can delete an older `st_sequence` value after recovery has already advanced that row in memory. Replay handles that case narrowly by locating the same stable `sequence_id`, requiring every field except `allocated` to match the logged row, and then deleting the current repaired value. A mismatch in sequence name, table, column, increment, or any other field still fails replay.

## Regression and release proof

The engine regression preserves explicit high identifiers in a user auto-increment table, `st_fabric_payload`, `st_fabric_payload_transition`, and `st_causal_event`; exercises migration and commit-log recovery; generates the next rows; commits; and reopens a second time. The publication regression separately proves an unchanged table is repaired in the publication transaction.

Fork build 5 is rejected because it failed the second-restart case. Fork build 6 at commit `8eef14373` introduced the accepted repair in `v2.7.0-seq001-r2`. It passed the full 59-test engine library suite, local same-data recovery, repeated live AI-Collab Plan Execution submissions, snapshot creation, rollback to build 4, and return to build 6 without deleting preserved data. The later fork build 9 carried that repair into the historical LAN service through an elevated repoint and restart. This resolved [BLK-SEQ-001](../../docs/blockers/2026-07-13-lan-seq001-promotion-blocker.md) without deploying the obsolete intermediate build. The LAN service was later [retired as preserved legacy state](legacy-lan-host-retirement.md); retirement does not change the recovery result.

## See Also

- [Staged Builds & Compatibility](staged-builds-and-compat.md)
- [Live Deployment](live-deployment.md)
- [Fork Versioning & Drift Detection](fork-versioning.md)
- [Fabric Engine](../engines/fabric-engine.md)
