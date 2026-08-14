# Bounded Native CINT Batch Ingest

> Status: **Shipped and release-validated in Fork build 35; carried and
> effective in production build 39**
>
> Updated: 2026-08-14
>
> Sources: [downstream request](../../docs/requests/CINT-BATCH-001-bounded-native-cint-request.md);
> [accepted implementation plan](../../docs/plans/CINT-BATCH-001-bounded-native-cint-proposal.md);
> [build-35 release evidence](../../raw/engines/2026-07-29-cint-batch-build35.md);
> [completion report](../../docs/completions/COMP-CINT-BATCH-001.md);
> [current CINT architecture](code-intelligence.md)

## What shipped

ABI `spacetime_10.13` adds one bounded native CINT microbatch call. A reducer
submits an ordered batch and receives an ordered row iterator of correlated
per-file results inside the same transaction. The reducer can persist its own
identity-scoped receipts, and clients observe them through the existing
generated binary subscription path.

Each input carries repository, revision, path, language, source bytes,
correlation identity, and an optional superseded snapshot. Each result carries:

- `Inserted`, `Replaced`, `Unchanged`, or `Rejected`;
- snapshot and file identity for accepted results;
- stable bounded error code for expected rejection;
- SHA-256 content digest, byte length, and exact fact counts; and
- exact symbol identity, kind, canonical name, and source byte spans.

Expected per-file validation and live-admission failures are isolated.
Unexpected decode, datastore, or invariant failures abort the reducer
transaction. The existing single-file APIs remain available.

## Why it was accepted

The design was originally preserved behind an evidence gate because native
CINT timing had not been isolated. The owner subsequently authorized Fork to
ship the complete configurable substrate before downstream cutover. That
changed the implementation gate, not the performance claim: AI-Collab still
owns adoption and must measure end-to-end throughput before claiming that the
batch path improves its pipeline.

## Transaction and identity contract

The implementation reuses the existing CINT file-row digest and byte-length
columns:

- `(repository, revision, path, SHA-256)` is exact retry identity;
- an exact retry returns `Unchanged` and writes no duplicate facts;
- different content at the same revision/path is rejected;
- a legacy digest-less row is upgraded once; and
- explicit supersession replaces an old snapshot path atomically.

There is no second identity store. A report-only audit caught that the initial
build did not directly prove conflict, supersession, or digest-less legacy
upgrade. Build 35 adds datastore and real guest/host contract proof for those
paths.

## Bounds and configuration

Hard compile-time limits constrain files, aggregate source bytes, symbols,
references, names, and result bytes. Unified configuration can disable the
feature or lower those ceilings; it can never raise them:

- `cint-batch.enabled`;
- `cint-batch.max-files`;
- `cint-batch.max-source-bytes`;
- `cint-batch.max-symbols`;
- `cint-batch.max-references`; and
- `cint-batch.max-result-bytes`.

Defaults are enabled, 64 files, 16 MiB source, 1,000,000 symbols, 4,000,000
references, and 64 MiB encoded results. These are hard maxima; unified host
configuration is deny-only and hot.

The host distinguishes malformed wire input from normal live admission. Input
over the compile-time aggregate source hard limit aborts before mutation. If an
operator lowers the live source budget, only the item that would exceed the
remaining budget receives `Rejected(source_limit)`; it does not consume that
budget, so a later smaller peer can still be accepted.

Microbatches are the progress, backpressure, and cancellation boundary. There
is no whole-repository transaction and no mid-transaction cancellation claim.

## Ownership boundary

Fork owns native datastore semantics, ABI transport, guest bindings, deny-only
host ceilings, status, metrics, and the exact smoke/rollback contract.

AI-Collab retains repository scheduling, model/GPU placement, correlation
receipt schema, result retention policy, client adoption, and the benchmark
that determines whether this path is beneficial.

## Release evidence

Build 34 `v2.7.0-cint-batch-r1` is preserved as rejected audit evidence.
[AUD-CINT-BATCH-001](../../docs/audits/AUD-CINT-BATCH-001.md) found CHML
`0/1/1/0`. Build 35 at commit `6642d121c117fce799773326e6df7bc60aba61f5`
implements [the remediation](../../docs/remediations/REM-AUD-CINT-BATCH-001.md)
and is staged immutably as `v2.7.0-cint-batch-r2`.

The exact host and guest artifacts passed mixed result, exact retry,
conflict/replacement, lowered-budget peer isolation, hot disable/rollback,
metrics, restart persistence, full-stage build-33 rollback, and build-35
restore drills. This task did not promote build 35. Build 36 subsequently
carried the accepted implementation into production, and production build 39
now carries it with CINT batch configured/effective. Build 34 remains rejected
audit and rollback evidence; its brief dated live presence does not alter build
35's status as the accepted CINT release.

## Non-goals

This capability does not add filesystem access, model execution, GPU placement,
a durable job scheduler, new code-graph semantics, whole-repository atomicity,
or an unmeasured throughput claim.

## See Also

- [CINT — Code Intelligence](code-intelligence.md)
- [Host Configuration Management](../operations/host-configuration-management.md)
- [Staged Builds and Compatibility](../operations/staged-builds-and-compat.md)
