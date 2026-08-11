# Worldline Engine

- **Status:** Accepted end goal; Phases 0-5 are accepted internal dependencies
  at CHML `0/0/0/0`. Complete-Phase-5 report-only
  [AUD-WLD-022](../../docs/audits/AUD-WLD-022.md) accepts P5-COMPAT source
  `9295b729a` after initial [AUD-WLD-021](../../docs/audits/AUD-WLD-021.md) and
  [REM-AUD-WLD-021](../../docs/remediations/REM-AUD-WLD-021.md). Phase 6 is
  active. P6-CMT-001 remediation is implemented at `0c6e29aa8`; report-only
  [AUD-WLD-023](../../docs/audits/AUD-WLD-023.md) failed at CHML `0/4/1/0` and
  [REM-AUD-WLD-023](../../docs/remediations/REM-AUD-WLD-023.md) closed those
  functional findings, but [AUD-WLD-024](../../docs/audits/AUD-WLD-024.md)
  failed at CHML `0/1/0/0` because abandoned attempts bypass the durable
  record bound. [REM-AUD-WLD-024](../../docs/remediations/REM-AUD-WLD-024.md)
  is implemented, and report-only
  [AUD-WLD-025](../../docs/audits/AUD-WLD-025.md) accepts P6-CMT-001 at CHML
  `0/0/0/0`. Initial P6-FAB report-only
  [AUD-WLD-026](../../docs/audits/AUD-WLD-026.md) rejects the candidate at
  CHML `0/2/2/1`; [REM-AUD-WLD-026](../../docs/remediations/REM-AUD-WLD-026.md)
  is implemented at exact source `732ef6e26` (tree `ef7eac116`), and
  report-only [AUD-WLD-027](../../docs/audits/AUD-WLD-027.md) closes every
  finding at CHML `0/0/0/0`. P6-FAB-001 is accepted internally. P6-MC-001
  exact source `c790628e9` is accepted internally by report-only
  [AUD-WLD-030](../../docs/audits/AUD-WLD-030.md) at CHML `0/0/0/0`, and
  post-acceptance [AUD-WLD-031](../../docs/audits/AUD-WLD-031.md) also passes.
  P6-APP-001 product source `0d34accaa` (tree `daf81e4cc`) implements its
  [fixed signed-approval contract](../../docs/evidence/WLD-001-phase6-approval-grounding.md)
  and is accepted internally by report-only
  [AUD-WLD-032](../../docs/audits/AUD-WLD-032.md) at CHML `0/0/0/0`. P6-CTRL is
  accepted internally by report-only
  [AUD-WLD-033](../../docs/audits/AUD-WLD-033.md) from its
  [fixed host-control contract](../../docs/evidence/WLD-001-phase6-control-grounding.md)
  and exact
  [implementation candidate](../../docs/evidence/WLD-001-phase6-control-increment.md)
  at product source `97dca8fee` (tree `4bfd027d4`). The four-file semantic
  correction is closed by [AUD-WLD-035](../../docs/audits/AUD-WLD-035.md), and
  broader [AUD-WLD-036](../../docs/audits/AUD-WLD-036.md) is closed by
  exhaustive [AUD-WLD-037](../../docs/audits/AUD-WLD-037.md) at final CHML
  `0/0/0/0`. The
  [P6-INTEGRATION contract](../../docs/evidence/WLD-001-phase6-integration-grounding.md)
  is frozen and implementation is active.
  This article does not describe a shipped capability; production remains
  accepted build 36 and WLD-001 remains incomplete.
- **Updated:** 2026-08-11
- **Plan:**
  [complete native Worldline Engine plan](../../docs/plans/WLD-001-native-worldline-engine.md)
- **Tracking:** [WLD-001 tracking](../../TRACKING.md)

## Purpose

The Worldline Engine is Fork's planned native runtime for executable alternative
database realities. A worldline pins authoritative state, executes deterministic
work against an isolated copy-on-write view, records complete dependencies and
effect intents, compares possible outcomes, and promotes an approved result only
when serializability can be proven.

This goes beyond the current CMT counterfactual surface. CMT can compare base
and candidate transaction deltas and explain causal differences; it does not
currently retain an independently queryable branch or promote it into authority.

## Architectural representation

A worldline is represented by:

1. an immutable versioned authority base;
2. an ordered deterministic execution log;
3. a complete SQL/graph/vector and environment dependency certificate;
4. copy-on-write relational state plus graph/vector overlay indexes;
5. an isolated effect-intent outbox;
6. evaluations, approvals, conflicts and promotion/effect receipts;
7. parent/root and distributed-participant lineage.

The execution log and dependency certificate are authoritative. Overlay state is
derived and content-digested so it can be validated or reconstructed.

## Phase 1 accepted foundation

The accepted Phase 1 source implements the native foundation without claiming
the complete engine or a shipped capability:

- immutable datastore authority roots published atomically while retained
  readers continue using their exact older root;
- bounded RAII retention admission and an exact-root, zero-row-copy
  `WorldlineBase` capture;
- persistent copy-on-write page/table/index roots plus bounded relational,
  graph, vector and schema overlays;
- a durable `AuthorityVersion` format that separates runtime publication order
  from restart-stable identity;
- one canonical per-table row/graph content commitment shared by incremental
  commit, full reconstruction and snapshot restore;
- deterministic schema identity and restart-stable sequence allocation-fence
  identity;
- validated commit deltas that fail closed on pointer, row, graph or
  cardinality inconsistency;
- bounded versioned codecs, a digest-chained journal, double-buffer manifest
  recovery and rejection of unsupported authority descriptor formats;
- reserved native authority/effect/participant system records with old-host
  snapshot preservation evidence.

Report-only [AUD-WLD-004](../../docs/audits/AUD-WLD-004.md) rejected the prior
candidate at CHML `0/1/0/0` because its durable identity depended on runtime
counters and different incremental/rebuild hash paths.
[REM-AUD-WLD-004](../../docs/remediations/REM-AUD-WLD-004.md) defines the
bounded correction whose implementation gates passed before the later graph
identity audit and remediation.

Implementation pin `f56bc12f97861c1796fea9b8f86cd81a8103e754` passes the
complete remediation gate. The generated
[creation-scale evidence](../../docs/evidence/WLD-001-worldline-creation-scale.json)
records 750,000 exact-root captures over 100K/1M/10M keyed-row fixtures with
zero copied row/page/vector/graph payload bytes and a 371bp slope under the
fixed 1000bp bound. Old-host snapshot import, the Worldline/datastore/engine/
commitlog/snapshot suites and all required serial affected-crate checks pass.
This evidence did not itself accept Phase 1; the later report-only audit did.

The next audit was
[AUD-WLD-005](../../docs/audits/AUD-WLD-005.md). It found that commit merge
reports a graph insertion even when the native graph index rejects the edge as
a duplicate or over its hard adjacency cap. Incremental identity can therefore
describe graph content absent from authoritative state and differ after
reconstruction. Phase 2 remained blocked until that high finding was remediated
and re-audited by `AUD-WLD-006` below.

The bounded [remediation](../../docs/remediations/REM-AUD-WLD-005.md) is now
implemented in the working candidate. Final native graph state decides whether
an insertion enters `TxData`; a cached exact edge count permits table-level
cardinality reconciliation in constant time; and authority validates every
absent→present or present→absent transition while refusing duplicate/mismatched
deltas. Exact implementation pin
`4886ebcb5d655cf2c7cbedd8b75761a358c6ee46` passes the generated 750,000-capture
100K/1M/10M scale gate at 252/243/250 ns medians, an 80bp slope and zero copied
user payload. Preserved-old-host import, all foundation suites, the complete
serial check matrix and explicit engine checks pass. Those results supplied the
evidence boundary for the fresh report-only audit below.

Report-only [AUD-WLD-006](../../docs/audits/AUD-WLD-006.md) independently
closes the graph-identity remediation and every retained Phase 1 finding at
CHML `0/0/0/0`. Phase 1 is therefore accepted as an internal foundation gate,
and Phase 2 is active. This acceptance does not expose a Worldline operator or
guest API, alter production build 36, or complete the end goal.

## Phase 2 deterministic execution candidate

The current remediation candidate implements every Phase 2 task while
preserving the phase boundary:

- a durable monotonic lifecycle with revision, actor, reason, policy and content
  binding plus explicit suspend/resume and terminal transitions;
- a deterministic capsule for virtual wall/monotonic time, seeded entropy,
  logical IDs, virtual sequences, module/schema/config/registry/capability pins,
  captured observations and hard resource ceilings;
- detached relational state rooted at an exact immutable authority generation,
  with reducer and procedure execution through both Wasmtime and V8;
- SQL execution over the combined base-plus-overlay view, including point,
  range, negative, full-scan, uniqueness and constraint behavior;
- ordered durable execution envelopes binding input/output/delta/dependency/
  effect/capsule/resulting-state digests, deterministic replay and explicit
  divergence, torn-tail and corruption classification;
- create, inspect, execute, query, replay, fork, suspend, resume and discard
  internal APIs with bounded ordered ancestor identity/revision/content proofs;
- fail-closed denial of uncaptured network and volatile scheduler activity,
  with speculative transaction data confined to the worldline and never
  broadcast to authority subscribers.

Report-only [AUD-WLD-007](../../docs/audits/AUD-WLD-007.md) rejected the first
candidate at CHML `0/5/4/2`. The current
[remediation](../../docs/remediations/REM-AUD-WLD-007.md) makes every mutating
step a per-step COW candidate published only with durable log acceptance,
denies live wall-time/sleep/CINT-policy escape, reconstructs and verifies the
exact historic authority root from bounded commitlog history after restart,
validates capsule/envelope/lifecycle semantics both live and during recovery,
binds grants to subject/owner/worldline/policy, recovers a missing first
manifest only from a verified create record, rejects a torn create, inherits
store bounds, and fixes inclusive sequence and fail-before-mutation behavior.

A real guest module exercises reducer and procedure execution, commit-then-fail
rollback, forbidden sleep, authority isolation and replay after store restart.
Fresh remediation gates pass Worldline 45/45, core 5/5, guest integration 1/1,
datastore 197/197 plus two schema tests, engine 59/59 plus seven inherited
fixture ignores, durability, both core feature configurations and strict
Worldline clippy. The earlier [Phase 2 exact-pin
record](../../docs/evidence/WLD-001-phase2-exact-pin.md) remains historical
evidence for rejected implementation `dbaeebef3`. The [remediation exact-pin
record](../../docs/evidence/WLD-001-phase2-remediation-exact-pin.md) binds the
green matrix to implementation `4ef82ccf7`; a fresh report-only CHML-zero audit
was required. At that checkpoint Phase 3 remained blocked and no runtime capability was shipped.

That report-only [AUD-WLD-008](../../docs/audits/AUD-WLD-008.md) found four
remaining high integrity gaps. The bounded
[REM-AUD-WLD-008](../../docs/remediations/REM-AUD-WLD-008.md) implementation at
`1059c3de397778415f13f868706f1ed796f11606` now:

- fences the branch after every journal-owned append error and proves real
  bounded journal/manifest failure recovery without publishing a live COW
  candidate;
- applies a durable logical-ID ceiling, validates realizable random stream
  accounting and requires observation capability for every new captured
  observation, including async executor state;
- exposes one durable SQL recovery operation that derives the owner and full
  `AuthorityVersion` from the verified create record, reconstructs that exact
  historic database/offset, validates format/durability/database/schema/
  sequence/state, restores stored module/config pins and replays the branch;
- binds every ancestor to its complete lineage-prefix digest, includes lineage
  in branch content identity, verifies stored manifest lineage and recomputes
  the retained base's initial overlay digest before recovery; and
- versions the changed capsule/create/step durable formats and rejects
  checksum-valid re-encoded semantic corruption.

The [integrity exact-pin
record](../../docs/evidence/WLD-001-phase2-integrity-exact-pin.md) preserves the
green 53/53 Worldline, 7/7 core Worldline/host, 1/1 real guest, datastore,
engine, durability, strict lint and dual core-configuration matrix. At that
checkpoint it was an internal Phase 2 candidate: Phase 3 remained blocked until
a fresh report-only audit reached CHML `0/0/0/0`, and production build 36 was unchanged.

Report-only [AUD-WLD-009](../../docs/audits/AUD-WLD-009.md) found two remaining
high semantic gaps in that candidate. General capsule integrity admitted a
checksum-valid create with operation-produced mutable progress before step
zero, and historic recovery treated the durability watermark recorded at
capture as a permanent prohibition instead of checking whether durable history
can reconstruct the target now.

The [final semantic remediation](../../docs/remediations/REM-AUD-WLD-009.md) at
exact source `f9d57f44535ebd1a6ce4247ce9dd9b81b6c20adf` closes both boundaries:

- a dedicated create invariant requires zero monotonic/random/ID/step/
  observation progress and empty observations/sequences while preserving the
  configured wall time, seed, grants, pins and ceilings;
- direct create rejects before creating its store, and re-encoded durable
  create rejection occurs before any manifest repair;
- `Volatile` and `DurableThrough(durable <= visible)` remain valid capture-time
  descriptors, while missing-visible and ahead-of-visible states fail closed;
- target history coverage plus complete authority reconstruction determines
  present recoverability without rewriting the stored descriptor; and
- real shutdown/restart tests recover both lagged and volatile captures after
  history catches up, replay isolated overlay state and exclude a later
  authority row.

The [final semantic exact-pin
record](../../docs/evidence/WLD-001-phase2-final-semantic-exact-pin.md) preserves
the green 55/55 Worldline, 7/7 core Worldline/host, 1/1 real guest, datastore,
engine, durability, strict lint and dual core-configuration matrix. At that
checkpoint Phase 2 was unaccepted and unshipped until another independent
report-only audit reached CHML `0/0/0/0`; Phase 3 remained blocked and
production build 36 was unchanged.

Report-only [AUD-WLD-010](../../docs/audits/AUD-WLD-010.md) closed both prior
semantic findings but found one retained high create-identity gap. A
checksum-valid create record could carry an ID different from the capsule and
seed inconsistent lifecycle/manifest identity during missing-manifest recovery.
The [durable create-identity remediation](../../docs/remediations/REM-AUD-WLD-010.md)
at exact source `e4849f5768d926a05e9a87768498120240c67039` now enforces one
nonzero record/capsule identity after normal construction and immediately after
durable decode in both stored-authority and full-recovery paths. Alternate and
zero IDs fail with a typed mismatch before authority use or manifest repair;
valid create-only recovery remains supported. The [durable create-identity
exact-pin record](../../docs/evidence/WLD-001-phase2-create-identity-exact-pin.md)
preserves Worldline 56/56, core 7/7, real guest 1/1, datastore, engine,
durability, strict lint, dual core configurations, formatting and every
repository validator. At that remediation checkpoint Phase 2 remained
unaccepted and unshipped pending a fresh report-only CHML-zero audit;
production build 36 remained unchanged.

Report-only [AUD-WLD-011](../../docs/audits/AUD-WLD-011.md) passes the complete
Phase 2 plan and every CRAFTESB dimension at CHML `0/0/0/0`. Phase 2 is
accepted as an internal, unshipped dependency gate; Phase 3 relational
dependency validation and promotion is active. The acceptance checkpoint is
tagged `phase-WLD-2-complete`; production build 36 and every public/operator
surface remain unchanged.

## Phase 3 canonical dependency certificate

The first Phase 3 increment now implements the certificate boundary described by
the plan. The native Worldline crate:

- carries explicit restart-stable authority generations derived from the
  visible transaction coordinate, while the datastore's process-local
  publication counter remains internal;
- canonicalizes a versioned dependency algebra through a stable record wire in
  a BSATN certificate envelope, normalizing, sorting, deduplicating and
  deterministically merging ranges before content addressing;
- rejects conflicting observations, mixed generations, invalid row authority
  identity and inconsistent vector full-index coverage;
- widens pressure only to explicit conservative full-table/full-graph/
  full-vector-index dependencies and fails closed when evidence cannot be
  safely coarsened; and
- makes branch commit, durable recovery and sync/async replay use the canonical
  certificate identity, so executor ordering and duplicates cannot alter the
  branch digest.

The [certificate checkpoint evidence](../../docs/evidence/WLD-001-phase3-certificate-increment.md)
records Worldline 66/66, strict Worldline-only lint and both core library
configurations green. It also retains the test-profile OpenSSL native-build
timeout as a warning that must be rerun before Phase 3 acceptance. Authority
change history and fail-closed exact/advanced certificate validation are now
implemented; the durable atomic promotion state machine remains active work.


## Phase 3 retained history and dependency validation

The next two internal checkpoints connect canonical evidence to live authority:

- every visible authority generation publishes a canonical content-addressed
  invalidation set, retained contiguously while a Worldline lease needs it;
- hard generation, byte and table-scope pressure widens conservatively or
  advances an explicit history floor, so missing proof fails closed;
- exact-base validation confirms the retained root and explicit current
  environment pins, while advanced-base validation examines only retained
  change sets plus certificate entries rather than database rows;
- unrelated authority table changes may validate, but intersecting relational,
  graph, vector, schema or sequence changes conflict; and
- module, host-config, registry resolution, capability policy, captured
  observation and parent-worldline state must match exact revision/digest pins.

Conflict records expose stable classes and content digests, not protected rows,
keys, vectors or payloads. The live `NativeWorldline` boundary can emit its
aggregate canonical certificate and invoke this validator. The [history
checkpoint](../../docs/evidence/WLD-001-phase3-history-increment.md) and
[validation checkpoint](../../docs/evidence/WLD-001-phase3-validation-increment.md)
record executable evidence. The [promotion-journal
checkpoint](../../docs/evidence/WLD-001-phase3-promotion-journal-increment.md)
adds a digest-linked prepared/authority-committed/finalized/conflicted chain
that recovers across restart and rejects tampering or request splicing.

The [atomic relational promotion
checkpoint](../../docs/evidence/WLD-001-phase3-atomic-promotion-increment.md)
now validates beneath the serial writer lock and commits verified relational
deltas plus the unique receipt in one authority transaction. Exact retry
returns that receipt without another commit; changed bytes, malformed or
unsupported deltas, intersecting authority changes and receipt substitution
fail closed. Core Worldline passes 9/9, datastore authority 13/13 and Worldline
75/75, including restart-stable durable identity.

The [restart-live authority
checkpoint](../../docs/evidence/WLD-001-phase3-restart-live-history-increment.md)
now gives durable recovered Worldlines two safe paths to the actual live
authority. A stable state identity selects retained per-generation history when
it exists. After process restart, a bounded canonical diff compares the exact
verified recovered base root with the writer-fenced live root using table,
graph, vector, schema and sequence commitments rather than runtime generation
numbers or copied row payloads. A real durable regression reconstructs both
roots independently, promotes across an unrelated-table advance, and proves
idempotent receipt replay. Worldline passes 75/75, core Worldline 11/11 and
datastore authority 15/15. At that checkpoint, sequence translation,
re-execution, crash injection and the Phase 3 audit remained active.

The [precise relational capture
checkpoint](../../docs/evidence/WLD-001-phase3-precise-relational-capture-increment.md)
now records the physical relational operations actually selected by SQL:
full-table scans, typed point/range seeks, stable primary-key row identities and
unique-constraint keys. Empty point seeks remain explicit negative evidence.
Mutation inserts resolve unique keys against the immutable base even where
constraint enforcement bypasses an executor scan. Authority change-set format
v4 retains bounded typed key details, so a disjoint key in the same table may
advance while the observed or written key still conflicts. Schema/truncation,
restart diff, missing stable identity and pressure paths widen conservatively.
Fresh gates pass Worldline 75/75, core Worldline 14/14 and datastore authority
16/16.


The [deterministic sequence-promotion
checkpoint](../../docs/evidence/WLD-001-phase3-sequence-promotion-increment.md)
now gives detached SQL, reducer and procedure execution replay-stable logical
auto-increment values without reserving authority IDs. The capsule binds the
sequence definition, base fence, exact allocation count and reconstructed
values under a hard global ceiling. Promotion holds the authority writer,
allocates native physical values in canonical sequence/ordinal order and
rewrites only matching typed columns.

Concurrent authority cursor advance is safe because it is translated; changed
sequence definition, table or column conflicts. The complete mapping is
count-bounded, structurally canonical, digest-bound and stored in both the
authority receipt and promotion journal. Exact retry returns it without a
second allocation or commit. Failed promotion restores the promotion
transaction's original in-memory and persisted native cursor, while ordinary
authority sequence semantics are unchanged. Final gates pass Worldline 77/77,
core Worldline 16/16, datastore authority 11/11 and system tables 2/2.

The [deterministic re-execution and approval
checkpoint](../../docs/evidence/WLD-001-phase3-reexecution-approval-increment.md)
creates a distinct lineage child against the current authority and replays the
exact bounded SQL, reducer or procedure request stream. Re-execution retains
the source's initial virtual time and random seed but not its stale authority
base.

Approval equivalence has two independent projections. The semantic projection
binds operations, inputs, outputs, deltas, effects, deterministic runtime
position, observations and logical sequences. The binding projection binds the
module revision/digest/ABI, config, registry, capability policy, caller, owner,
grants and resource ceilings. `Invalidate` always clears approval;
`PreserveIfEquivalent` carries it only when both projections match exactly.
Otherwise a versioned digest-chained journal record requires one distinct
successor approval. Recovery revalidates the record, lineage, actor, lifecycle
and revision; direct lifecycle bypass, stale approval reuse and promotion with
the wrong durable effective approval fail closed.

Conflicted sources accept only their terminal durable promotion approval.
Target capability denial occurs before target state exists, and every admitted
replay failure moves to the durable `Failed` lifecycle. Fresh gates pass
Worldline 80/80, core Worldline 22/22 and the core library compile gate. The
remaining adversarial cases, exact pin and report-only Phase 3 audit remain
active; this surface is still internal and unshipped.

The [crash-safe promotion recovery
checkpoint](../../docs/evidence/WLD-001-phase3-crash-recovery-increment.md)
exercises every durable promotion publication boundary: prepared record,
preparing and promoting lifecycle, pre-authority commit, authority published,
authority-committed record, promoted lifecycle and finalized record. Failure
before commit explicitly rolls back native sequence state. Failure after commit
reconciles the durable receipt without a second authority generation, row,
receipt or sequence allocation, even after the branch instance is destroyed and
retried repeatedly.

Journal recovery also handles a verified prepare, authority-commit or finalize
record whose bytes reached disk while both manifest slots still contain their
previous state. A real durable-host test crashes after authority publication,
commits unrelated later work, shuts down, and reconstructs live authority plus
the older branch independently. The receipt remains verifiable even when
restart compaction does not retain the exact intermediate promotion root.

That independence comes from a non-circular canonical transition commitment,
not a mutable live-root digest. At that historical checkpoint, the canonical
request seal also bound caller-supplied external validation state. Its identity
work failed closed above 100,000 entries or 64 MiB, but `AUD-WLD-012` later
proved that bounded integrity did not establish live provenance. Fresh gates
at the checkpoint passed Worldline 82/82, core Worldline 24/24 and the core compile check.
Remaining adversarial coverage, the exact pin and Phase 3 CHML-zero audit are
still required.

The [relational adversarial matrix
checkpoint](../../docs/evidence/WLD-001-phase3-adversarial-matrix.md) closes
the remaining executable Phase 3 conflict surface. Real authority transactions
exercise indexed range phantoms, secondary uniqueness, exact-row read/delete,
two-branch write skew and target-table schema drift in addition to primary
write/write, negative-read, sequence-definition and safe-disjoint-advance
cases. Module, host-config, registry-resolution, capability-policy,
captured-observation and parent-worldline mismatch each returns its exact
content-safe conflict class without requiring an authority advance.

Every rejected promotion proves that neither the candidate row nor its receipt
is partially applied. Exact-base promotion also proves that its single returned
canonical `TxData` contains exactly one user insert and one reserved receipt
insert at the same authority offset, with no deletes. Fresh gates pass
Worldline 82/82, core Worldline 29/29 and the core library check. The [Phase 3
exact-pin record](../../docs/evidence/WLD-001-phase3-exact-pin.md) binds that
command matrix to source `10df36c05bf4aa71ac593b054e7a7bcadb046d9c`.

Report-only [AUD-WLD-012](../../docs/audits/AUD-WLD-012.md) found one High
provenance gap. External mismatch classification is correct when changed state
is supplied, but `promote_with_hook` clones that state from the request and
refreshes only datastore fields. The request digest authenticates those bytes;
it cannot attest current module, host-config, registry, capability-policy,
captured-observation or parent-worldline authority. The guarded host-owned
live-state [remediation](../../docs/remediations/REM-AUD-WLD-012.md) is now
implemented at `ba0efcf4ec6d8d2f3ee4a6967fa37903f7935072`.

`NativeWorldlinePromotionRequest` now carries only request-owned promotion,
revision, caller, approval and creation-time identity. The bounded host-owned
`NativeWorldlineExternalAuthority` rejects invalid initialization, stale
compare-and-replace, overflow and poisoned state. Its read guard remains held
from snapshot acquisition through live dependency validation and the
relational commit. The live `RelationalDB` supplies database identity, visible
offset, durability, schema, sequences and state; only the guarded store supplies
module, host-config, registry, capability, observation and parent truth.

Production-path tests independently drift all six external classes and prove
their exact conflicts without applying the candidate row or receipt. Missing
required entries fail closed, unrelated entries remain safe, a concurrent
writer cannot publish inside the validation-to-commit window, committed replay
remains idempotent after drift, and an uncommitted retry revalidates newly
changed state after a precommit failure.

The [replacement exact-pin
record](../../docs/evidence/WLD-001-phase3-exact-pin.md) preserves the rejected
source and appends the trusted-provenance matrix: Worldline 82/82, core
Worldline 34/34, real guest 1/1, datastore 203/203 plus 2 schema tests, engine
59 passed with 7 documented ignores, durability, strict Worldline Clippy, both
core feature configurations and targeted Rust 1.93 formatting. A fresh
report-only re-audit is still mandatory. Phase 4 is blocked; the capability is
internal and unshipped, and production build 36 is unchanged.

Report-only [AUD-WLD-013](../../docs/audits/AUD-WLD-013.md) confirms that the
prior High caller-controlled provenance defect is closed in source, then finds
two proof gaps. The writer regression signals before calling `replace` and
uses a 50 ms non-completion window, so it can pass without proving a write
attempt reached the held lock. The store test also does not directly execute
invalid construction or `GenerationOverflow` despite those claims.

[REM-AUD-WLD-013](../../docs/remediations/REM-AUD-WLD-013.md) is implemented at
clean exact pin `1d0737f3e1f0ced1e2b43ce25475a7fe874cba39`. The commit-boundary
hook directly requires `try_write` to return `WouldBlock`; the real `replace`
succeeds after guard release; and oversized construction/replacement plus
generation overflow execute without state mutation. The full matrix is green
in the [proof-determinism evidence](../../docs/evidence/WLD-001-phase3-proof-determinism-remediation.md).
Artifact impact is neither. Report-only
[AUD-WLD-014](../../docs/audits/AUD-WLD-014.md) closes both proof findings and
passes the complete Phase 3 plan plus every CRAFTESB dimension at CHML
`0/0/0/0`. Phase 3 is accepted as an internal, unshipped dependency gate and
checkpointed by annotated `phase-WLD-3-complete`. Phase 4 native graph,
vector and multi-vector worldlines is now source-grounded but has no accepted
implementation. Production build 36 is unchanged, Worldline is unshipped and WLD-001
remains incomplete.

## Phase 4 source-grounded architecture

The [Phase 4 grounding record](../../docs/evidence/WLD-001-phase4-grounding.md)
confirms that each Worldline already runs against a detached `RelationalDB`
created from an immutable retained authority root. Its populated graph and
vector indexes are therefore the real combined copy-on-write execution view.

P4-GRAPH-001 now supplies the stable native mutation layer. Canonical
transaction format v2 interns version-qualified table/name/primary-key/row
identities for endpoints and every cognition reference, rejects unknown or
malformed input, enforces entry and byte ceilings, and still decodes accepted
v1 relational arrays. SQL, reducer and procedure mutation paths canonicalize
before commit and roll back on rejection. Promotion/replay applies relational
rows, incident graph deletes, exact graph inserts, sequence translations and
the reserved receipt inside one authority writer transaction. Authenticated
canonical recovery preserves original transaction boundaries, logical sequence
values, legacy journals and the final state digest.

The [P4-GRAPH-001 checkpoint](../../docs/evidence/WLD-001-phase4-graph-delta-increment.md)
and [completion report](../../docs/completions/COMP-P4-GRAPH-001.md) record core
Worldline 48/48, Worldline 82/82, real guest 1/1, datastore 203/203,
graph-index 19/19, graph-focused core 14/14, all affected all-target checks and
the required serial consumers. This is internal host source, not a phase
acceptance or release; production build 36 is unchanged.

P4-GRAPH-002 now supplies bounded graph combined-query certificates and precise
authority validation. Versioned `GraphTraversalProof` values bind stable
starts, normalized traversal filters, visited and positive/negative frontiers,
exact traversed edges, capability-gate references and caller/owner/policy
digests. Decode checks enforce 64 MiB, 100,000 aggregate-item and depth-64
ceilings before unbounded allocation.

The physical datastore executor used by Worldline SQL and reducer/procedure
mutable and read transaction contexts install the graph observer. Replay
dispatch returns through the same observed execution functions. Authority
change-set format 7 records stable edge, endpoint, label, temporal, weight,
gate and changed-primary-key scopes and widens fail closed when a stable
reference cannot be resolved. This is executor-path coverage; it does not claim
new textual SQL graph grammar or a new graph guest fixture.

The [P4-GRAPH-002 checkpoint](../../docs/evidence/WLD-001-phase4-graph-certificate-increment.md)
and [completion report](../../docs/completions/COMP-P4-GRAPH-002.md) record
implementation source `aca13f062017b0e163ed83dda4b085f3bf1dba8f`, tree
`71df558018114d4be44cfa05ae4b8416e8b1bfe4` and the fresh Worldline,
core, datastore, graph-index, integration, all-target, Clippy and downstream
consumer matrix. This remains internal host source, not Phase 4 acceptance or a
release; production build 36 is unchanged.

P4-VEC-001 now supplies strict bounded vector and multi-vector certificates.
Proof format v1 binds scan, join, direct and multi-vector modes; metric and
dimensions; normalized query/filter; `k` and `ef_search`; stable segment
inventory; stable result identities; exact native distance or score/member
evidence; and deterministic boundary or full-index evidence. Unknown fields,
trailing bytes, non-finite scalars and oversized dimensions/items/bytes fail
closed before unbounded allocation. Equal-distance and multi-vector ordering is
stable.

The physical scan/join executor and direct read-only/mutable datastore paths
emit the observation from the one native search. Mutable reads cover committed
segments plus the transaction overlay, represented by virtual CPU segment ID
zero when native segment metadata is absent. The bounded host observer resolves
row pointers to stable primary-key identities, deduplicates evidence and adds
schema, selected-row and vector dependencies to certificate format 2.

Authority history format 8 carries per-index vector row and segment scopes.
For exact ordinary queries, advanced-base validation admits disjoint changes,
farther inserts and unselected deletes, while selected-row changes, closer
inserts and equal-distance tie crossovers conflict. Approximate HNSW,
full-index, multi-vector, widened and malformed evidence remains conservative
and fails closed. Normal HNSW results are not mislabeled exact merely because a
boundary is present.

The [P4-VEC-001 checkpoint](../../docs/evidence/WLD-001-phase4-vector-certificate-increment.md)
and [completion report](../../docs/completions/COMP-P4-VEC-001.md) bind source
`fb5e5aa4b3d11e6b34174856be6500a2638ef8e4`, tree
`63fbe878b2dadf7d81a1fa6c72c2ff77697691f9` and the final exact-tree
planner, execution, datastore, Worldline, core and downstream consumer gates.
This remains internal host source, not Phase 4 acceptance or a release;
production build 36 is unchanged.

P4-OVERLAY-001 now supplies canonical bounded native projection and exact
recovery. `OverlayDeltaBatch` uses stable versioned row identities for
relational, graph and vector entries, preserves exact vector scalar bits,
coalesces repeated mutations and reset markers, advances generation once per
non-empty step, and enforces strict format, finite-value, item and byte bounds.

Authenticated step envelope v3 binds the canonical batch and digest plus the
prior/result generation and root. The branch builds a candidate projection
before journaling and publishes it only after both journal append and manifest
store succeed. Manifest failure leaves the live projection unchanged and the
durable step is applied exactly once by restart replay.

Accepted v2 history remains readable but is fenced from native continuation
until the host deterministically rebuilds it. A metadata-only
`OverlayProjectionCheckpoint` binds the exact journal prefix, prior branch
semantics and rebuilt generation/root; no duplicate mutable overlay bytes are
trusted. Native v3 recovery replays and verifies every commitment. SQL, reducer
and procedure paths derive projection from the already-authenticated canonical
transaction delta before proof finalization.

The [P4-OVERLAY-001 checkpoint](../../docs/evidence/WLD-001-phase4-overlay-projection-increment.md),
[completion report](../../docs/completions/COMP-P4-OVERLAY-001.md) and report-only
[CHML-zero re-audit](../../docs/audits/AUD-P4-OVERLAY-002.md) bind source
`bad9cea7e974fbfebe336e86e460e463afddfc1a`, tree
`1e9d85b7eb67798bf0f6a1650f718760057106e0`, Worldline 96/96, core Worldline
54/54 and the isolated real snapshot-backed recovery proof. The recovered
inspection/root is byte-identical to pre-restart state and excludes authority
changes made after capture.

This remains internal host source, not Phase 4 acceptance or a release;
production build 36 is unchanged.

P4-DIFF-001 now supplies the native deterministic comparison and promotion-delta
boundary. `WorldlineComparison` binds two verified Worldline anchors—their
IDs, revisions, state/dependency digests, overlay generations and roots—and
emits versioned canonical records for relational rows/table resets, graph
overlay entries plus visited/frontier/negative-frontier/path-edge/gate changes,
and vector overlay entries plus selected-result membership, rank, distance,
score and member-evidence changes. Stable primary-key, edge and semantic query
identities keep the format pointer-free. Explicit presence bits distinguish
absence from a recorded tombstone. Unknown fields, inconsistent presence,
duplicate identities/ranks, noncanonical order, invalid graph identities,
non-finite vector evidence, integrity mismatch and hard aggregate entry/byte
limits fail closed.

The physical read-only SQL wrapper now routes vector and multi-vector work
through a scoped Worldline observer exactly once. The same native search returns
rows and captures normalized scan/join inputs, stable results and complete
multi-vector parent/member evidence; no duplicate search, queue or selection
change is introduced.

A successful promotion now canonicalizes the entire pending authority
transaction—including accepted branch changes and the reserved promotion
receipt—before commit and returns one digest-bound
`NativeWorldlineAuthorityDelta` at the published generation/offset. Failure
still occurs before authority publication; byte-identical idempotent replay
returns the durable receipt with no second transaction delta. This is the
Phase 4 planner/execution/subscription-delta seam. Phase 5 still owns client
subscription isolation, snapshot/delta delivery, backpressure, gaps and catch-up.

The [P4-DIFF-001 checkpoint](../../docs/evidence/WLD-001-phase4-comparative-subscription-increment.md),
[completion report](../../docs/completions/COMP-P4-DIFF-001.md), initial
[audit](../../docs/audits/AUD-P4-DIFF-001.md),
[noticeboard remediation](../../docs/remediations/REM-AUD-P4-DIFF-001.md) and
report-only [CHML-zero re-audit](../../docs/audits/AUD-P4-DIFF-002.md) bind exact
source `489f02da7ffc55618ef308c66201db1d75f5c605`, tree
`f8b623cf8b9997c8087835cb82041c5c919c2263`, Worldline 99/99, core Worldline
56/56, datastore/core checks and strict Worldline Clippy. The initial audit's
only finding was committed noticeboard syntax/identity drift; remediation
preserved both historical messages and restored full YAML/count/uniqueness/
timestamp invariants. This remains internal host source, not Phase 4 acceptance
or a release; production build 36 is unchanged.

## Phase 4 CPU/CUDA parity candidate

P4-PARITY-001 now canonicalizes CPU/CUDA IVF selection, selected segment
ranges and equal-distance external-ID ties. HNSW, HENN and QuIV expose the same
stable tie order, while CUDA rejects unordered, overlapping or out-of-bounds
range selections. Native Worldline coverage compares ordered row IDs, exact
`f32` distance bits, graph/vector/multi-vector evidence, dependency digests and
canonical certificate bytes. AnyMax, TopNSum(2) and WeightedRoles are all
covered explicitly.

GPU placement is retained across create, recover, replay, nesting and
step-candidate construction. Graph observations share immutable path arrays
instead of cloning complete path payloads. Observer-free `DeltaTx` construction
and graph/vector forwarding are inlineable so disabled release execution can
collapse to the authority call.

At exact source `6d08f0433e4fe5b2ecdb0b2dcd41e9533412871c`, tree
`66651424be7e24bd04435a2c694f662b79191b90`, the committed manifest and all
five content seals reproduce; both graph shapes contain exactly 1,000,000
edges; exact, ANN and three-policy multi-vector fixtures use 1536 dimensions;
and every CPU/CUDA result, distance bit and certificate byte matches. Two
independent fixed-affinity 64-operation runs pass the unchanged 2% disabled and
20% empty-overlay gates. Real hardware suites pass on the only visible device,
GTX 1660 Ti `GPU-6f37c632-1256-7218-5550-6e35e92f5be3`. The 20,000 x 1536
ANN fixture has recall@10 1.0 and reports 5.665 ms GPU versus 22.965 ms CPU
median-run p95. The RTX 3080 was not visible, so no two-device result is claimed.

The [P4-PARITY evidence](../../docs/evidence/WLD-001-phase4-parity-increment.md),
[completion candidate](../../docs/completions/COMP-P4-PARITY-001.md), failed
[report-only audit](../../docs/audits/AUD-P4-PARITY-001.md) at CHML
`0/0/2/0`, and
[remediation](../../docs/remediations/REM-AUD-P4-PARITY-001.md) preserve the
product history. [AUD-P4-PARITY-002](../../docs/audits/AUD-P4-PARITY-002.md)
confirmed product closure but failed at CHML `0/0/0/1` because the mandatory
reader exceeded its bounded workflow window.
[REM-AUD-P4-PARITY-002](../../docs/remediations/REM-AUD-P4-PARITY-002.md) is
verified against generated and real boards, and report-only
[AUD-P4-PARITY-003](../../docs/audits/AUD-P4-PARITY-003.md) passes at CHML
`0/0/0/0`. Complete Phase 4
[AUD-WLD-015](../../docs/audits/AUD-WLD-015.md) passes all six increments and
CRAFTESB at CHML `0/0/0/0`. Phase 4 is accepted internally and Phase 5 is
active. This remains internal host source; production is still build 36 and no
binary or WASM was produced.

## Phase 4 acceptance boundary

Phase 4 is a complete internal dependency gate, not a released capability. The
fresh clean-pin audit passes Worldline 103/103 and CUDA-enabled core Worldline
58/58 with two explicit scale/performance ignores whose accepted real-device
executions remain bound to the exact P4-PARITY evidence. The first resumed-shell
CUDA invocation lacked `cl.exe`; the identical Visual Studio x64/CUDA 13.3 rerun
passed. Phase 5 owns effect intents, isolated client subscriptions and
schema/module evolution.

## Phase 5 source grounding

The accepted [Phase 5 source-grounding record](../../docs/evidence/WLD-001-phase5-grounding.md)
bound the accepted plan to the effect-intent, promotion, durable-offset,
subscription, schema/module, authority-retention and journal source before this
increment began.

## P5-EFFECT-001 accepted internal increment

The [durable effect-plane evidence](../../docs/evidence/WLD-001-phase5-effect-increment.md)
records the exact implementation and report-only acceptance evidence. `EffectIntent` now carries an
optional backward-compatible executable contract with explicit canonical digest,
content reference, authorization, expiry, bounded deterministic retry and
digest-bound compensation. Promotion validates the complete contract and
atomically materializes idempotent outbox rows with authoritative user data, the
canonical promotion delta and receipt.

The native host plane discovers only durably eligible work through a bounded
indexed query, issues expiring fenced leases, records immutable receipts,
reconciles unknown outcomes and atomically enqueues compensation. Master disable
and emergency stop block discovery and new claims while issued leases can drain.
All mutation paths pair commit or rollback explicitly. Real disk-backed reopen
coverage proves pending, claimed, retry, unknown, reconciled, terminal and replay
states. Fresh gates pass Worldline 106/106, focused effect-plane 6/6 and full
core 344/344; strict Worldline Clippy passes and whole-core strict Clippy retains
25 inherited, non-task-owned findings.

This accepted internal host-source increment is not yet exposed through the
unified runtime config or public host API; those surfaces belong to later phases
and no configurability claim is made here. Report-only
[AUD-WLD-016](../../docs/audits/AUD-WLD-016.md) passes at CHML `0/0/0/0` and
P5-SUB-001 is active. Schema evolution and reachability GC remain unimplemented.
Nothing is shipped and production remains accepted build 36.

## P5-SUB-001 accepted internal increment

The [subscription grounding record](../../docs/evidence/WLD-001-phase5-subscription-grounding.md)
and [implementation evidence](../../docs/evidence/WLD-001-phase5-subscription-increment.md)
bind one transport-neutral native plane to authenticated branch truth. It
rebuilds from the durable journal/manifest and canonical overlay rather than
creating a parallel log or exposing speculative rows through ordinary
authority subscriptions.

An exact snapshot binds worldline, lineage, lifecycle, revision, every branch
digest, overlay generation/root and bounded overlay bytes. Each accepted step
becomes one versioned domain-separated delta frame bound to its predecessor;
terminal lifecycle truth follows the final delta once. Reconnect cursors and
monotonic acknowledgements are digest-validated. Shared retention,
per-subscriber frames/bytes, poll budgets, frame bytes and snapshot bytes are
hard bounded. Retention, slow-client and oversized-frame gaps require an exact
catch-up snapshot and never block branch execution.

Admission requires the capsule caller, exact worldline/lineage and Inspect
capability. Native create, recover and fork paths construct independent planes,
so parent sessions never appear in children. Sync begins at the first
unsynchronized durable step instead of walking historical steps on every poll.
Successful promotion still uses the existing one-shot canonical authority delta
as the sole authority-visible handoff.

Fresh gates pass focused subscriptions 7/7, Worldline 113/113, focused host
fork isolation 1/1 and full core 344 passed with one intentional
release-performance ignore. Both library checks and strict Worldline Clippy
pass. Whole-core strict Clippy retains 25 inherited findings and none touch this
increment.

The [completion report](../../docs/completions/COMP-P5-SUB-001.md) and
report-only [AUD-WLD-017](../../docs/audits/AUD-WLD-017.md) accept the internal
host-source increment at CHML 0/0/0/0. Unified enablement/limits remain Phase 6
work and public transport/CLI/SDK exposure remains Phase 8 work. P5-EVOLVE-001
is active; production remains build 36 and Worldline is unshipped.

## P5-EVOLVE-001 accepted internal increment

The [evolution evidence](../../docs/evidence/WLD-001-phase5-evolution-increment.md)
and [completion report](../../docs/completions/COMP-P5-EVOLVE-001.md) bind
implementation source `5c2c90b1707f54b4d823ed04bda133203a57f494` and its
complete internal migration contract. The versioned, bounded evolution plan
owns every current native `AutoMigrateStep`: schema, table, column, index,
constraint, sequence, schedule, view, RLS, access, client and module changes.
Future native variants fail compilation until Worldline handling is explicit.

Automatic migrations use the native deterministic planner. Declared reducer
rewrites require nonzero risk acknowledgement and reason commitments. Both
execute against rollbackable detached candidate state, bind canonical inputs,
outputs, affected rows and migration deltas, and recover exactly after restart.
Graph, vector and sequence dependencies remain source-aware: changed resources
widen conservatively, while target-only resources cannot invent dependencies
against the captured source authority.

Accepted promotion validates source and target pins, then atomically applies
pre-migration, migration and post-migration deltas with schema, program,
sequences, effects and the promotion record. Real-WASM tests prove automatic
evolution, reducer row rewrites, rollback on invalid table removal, exact
recovery, live authority-drift rejection and promotion-gated target activation.
Fresh gates pass evolution 6/6, Worldline 120/120, core 349 passed with one
intentional ignore, native migration 17/17 and integration 5/5. Strict
Worldline Clippy is clean; whole-core strict Clippy has 24 inherited findings
and zero task-owned findings.

Report-only [AUD-WLD-018](../../docs/audits/AUD-WLD-018.md) accepts the
host-and-guest source increment at CHML `0/0/0/0`. At that acceptance
checkpoint P5-GC-001 became active; its later accepted result is documented
below. This was not a release: complete Phase 5 remains unfinished, public configuration
and consumer surfaces remain later-phase work, production remains accepted
build 36 and Worldline is unshipped.

## P5-GC-001 accepted internal increment

The [native retention and GC grounding record](../../docs/evidence/WLD-001-phase5-gc-grounding.md)
and [implementation evidence](../../docs/evidence/WLD-001-phase5-gc-increment.md)
now bind a real database-scoped manager over the configured Worldline root.
It derives reachability from authenticated lifecycle and
lineage, active subscribers, incomplete promotion journals, unresolved
authority effects, recovery state, durable legal/audit holds, admission
recovery and bounded external roots that may only add protection.

Eligible collection appends and synchronizes mandatory journal kind 11 and
advances the atomic manifest. The consuming core path releases live branch,
database, subscription and retained-root handles before a same-volume
write-through move into a path-contained quarantine. A versioned payload-free
record independently redacts descriptor, receipt and tombstone-core evidence
at their policy deadlines; the full recovery tombstone exists only while
payload deletion is incomplete. Deterministic entry/byte-bounded post-order
sweeps resume idempotently after every tested crash boundary.

Managed create, fork, SQL re-execution and module re-execution use the same
single-use durable reservation path. If a failed operation has published a
root, a digest-verified admission-recovery record is durable before the
reservation is removed. It remains a pressure/reachability root until exact
recovered or discarded resolution. Active/terminal/quarantine/record counts
and retained/reserved bytes deny new work before unsafe eviction.

The initial report-only [AUD-WLD-019](../../docs/audits/AUD-WLD-019.md) rejected
the candidate at CHML `0/4/4/0`. Its remediation now closes independent
retention, aggregate scan budgets, failed-admission recovery, exact-cap
pressure, hold idempotency, delete/store compatibility, authority-fence
lifetime and the crash/corruption/reparse matrix. Fresh gates pass focused GC
20/20, complete Worldline 149/149, core 353 with one intentional ignore, core
check and strict task-owned Clippy. Dependency-inclusive strict Clippy remains
stopped by an inherited `crates/lib/build.rs` warning before the task crate.

Exact implementation source `231c9c9c045114cb2634787d564b2e7ad0834deb`
and report-only [AUD-WLD-020](../../docs/audits/AUD-WLD-020.md) pass the complete
P5-GC grounding and CRAFTESB contract at CHML `0/0/0/0`. The
[completion report](../../docs/completions/COMP-P5-GC-001.md) records internal
acceptance and activates P5-COMPAT-001. This is not shipped behavior;
production remains accepted build 36, Worldline has no production toggle, and
WLD-001 remains incomplete.

## P5-COMPAT-001 source-grounded contract

The [compatibility grounding record](../../docs/evidence/WLD-001-phase5-compat-grounding.md)
binds the final Phase 5 increment to one real integrated matrix. Accepted
effect, subscription, evolution and GC states must recover together across
their durability boundaries with no duplicate promotion, authority delta,
receipt, terminal frame or deletion.

The grounded contract required a bounded, deterministic, digest-only seal over every
regular file in a quiescent copied Worldline store. File count, total bytes,
depth and canonical relative-path bytes are independently bounded; path escape,
links/reparse points, special files and overflow fail closed. The seal lets the
real prior-host drill prove that the separate Worldline directory was ignored
and byte-preserved without exposing payloads.

The authority half reuses the accepted Phase 1 current→prior-host
`2b69c837b`→current snapshot method, now with final Phase 5 table states and
exact authority identity. Legacy v2, unknown optional, unknown mandatory,
future format, corruption and torn-tail cases are executable gates.

## P5-COMPAT-001 exact-source candidate

The candidate implements the fixed contract as one reusable fail-closed gate.
A versioned domain-separated BLAKE3 store seal binds normalized relative paths,
regular-file length/content digests, empty directories and total inventory
under independent entry, file, byte, depth and path-byte ceilings. The entry
budget is charged globally across recursion before each child path is retained.
Links, Windows reparse points, path escape, special files, invalid names, overflow and
concurrent mutation fail closed.

The real integrated matrix recovers effect claim/retry/unknown/reconciliation,
exact subscriptions and gap/catch-up, evolution commitments and independent GC
roots across repeated authority and manager reopen. A feature-gated current
host executable proves typed rows and pre-Worldline self-heal without coupling
to unrelated monolithic datastore tests. Exact prior host `2b69c837b` restores
and re-snapshots the copied unknown tables in its own disposable Cargo target;
current source re-imports exact typed equality and authority identity.

The correctly ordered gate also passes journal 5/5, manifest 7/7 and legacy-v2
rebuild/recovery. Unknown optional bytes remain the exact authenticated prefix
across a later supported append, while mandatory/future/corrupt state remains
unavailable and byte-preserved. The before/after store seal is format 1 over 20
entries, 10 files and 30,358 bytes with digest
`3b34a0bd37302bc72dd8eb352652e8fb384232fa9e004d37bd78644bcb6ce462`.

Exact remediation source `9295b729aae204f0f838c150b97d986c2314bf03` passes
focused compatibility 8/8, Worldline 157 with one intentional environment-driven
ignore, core 354 with one intentional release-performance ignore, datastore
212/212, the complete serial downstream ladder, strict task-owned lint,
CUDA-enabled standalone and benchmark all-target gates. The exact prior-host
gate retains the same format-1 seal digest. The [implementation evidence](../../docs/evidence/WLD-001-phase5-compat-increment.md)
and [completion report](../../docs/completions/COMP-P5-COMPAT-001.md) record
C1-C12 and the initial-audit remediation. Report-only
[AUD-WLD-022](../../docs/audits/AUD-WLD-022.md) accepts complete Phase 5 at
CHML `0/0/0/0`; Phase 6 is active. Production remains accepted build 36 and
Worldline remains unshipped.

## P6-CMT-001 exact-source candidate

The [Phase 6 grounding](../../docs/evidence/WLD-001-phase6-grounding.md),
[implementation evidence](../../docs/evidence/WLD-001-phase6-cmt-increment.md)
and [completion report](../../docs/completions/COMP-P6-CMT-001.md) bind
exact remediated source `0c6e29aa809afb922f95bfda7928f7d928ab13d4`.

Worldline now owns a bounded canonical evaluation record that binds one exact
candidate revision and its state, log, dependency, effect, overlay, policy and
config identity to an evaluator revision, metrics, disposition and typed CMT
evidence references. The durable branch journal recovers the ordered
evaluation chain and rejects stale candidate/policy/config bindings. An
evaluation is evidence only: it cannot approve, promote or advance candidate
lifecycle.

The datastore writes comparison, evaluation and blast-radius events into the
ordinary CMT event/edge graph under protocol tags 61, 60 and 62. A versioned
`FWLDCMT1` envelope binds evidence database, evaluation run, Worldline,
candidate revision and all relevant digests. Comparison-to-evaluation
`EvidenceFor` and comparison-to-changed-subject `BlastRadius` edges make the
explanation traversable without copying row/vector/graph content into the
Worldline journal.

Changed subjects are canonical typed identities for table resets, relational
rows, graph/vector overlays, vector-index resets and graph/vector query scopes.
Subject count, total rows and aggregate metadata bytes are validated and
charged before the first write. Exact retries are byte-identical; conflicting
identity reuse, malformed metadata, duplicates, over-limit input and
cross-database binding fail closed.

The evidence store must be a different database from candidate authority. The
datastore self-binds every record to its actual identity before lookup/write and
reconstructs every non-generated event and connected-edge field on retry. A
versioned digest-chained `Prepared` handoff is durable in the Worldline journal
before CMT commit; it binds the exact request, candidate, evaluator and evidence
store. Pending handoffs fence ordinary mutation/promotion and recover to exact
completion or an authorised durable `Abandoned` successor. Seven injected
boundaries cover both sides of handoff, CMT commit, evaluation append, final
journal frame/manifest publication and completion without orphaned evidence or
duplicate records. Every recovered `Prepared` handoff consumes one aggregate
durable attempt under `max_records`; exact retry of the current pending request
does not consume another attempt, while completion and abandonment do not
refund capacity. Exhaustion is checked before journal or manifest mutation.

Evaluation format 2 commits the request and handoff digests and requires exact
kind-specific protocol tags, names, counts and terminal relationships across
one coherent causal-chain/comparison pair and optional blast-radius reference.
Format-1 evaluation records keep their original canonical digest and validation
contract, including valid legacy single-reference records.

Fresh gates pass Worldline 172 with one intentional ignore, datastore 221/221,
core 356 with one intentional ignore, global CMT tag registry 5/5, focused
rollback/restart/isolation tests, strict task-owned Worldline lint and the
complete serial downstream check matrix. Whole-engine/core strict lint retains
inherited findings outside this increment and is not represented as clean.

Initial report-only [AUD-WLD-023](../../docs/audits/AUD-WLD-023.md) failed at
CHML `0/4/1/0`. It found caller-asserted evidence-database identity,
under-validated recovered rows/edges, no durable discoverable handoff across
the CMT-commit/Worldline-journal boundary, incomplete kind-specific evidence
semantics and missing adversarial tests. The mandatory
[remediation](../../docs/remediations/REM-AUD-WLD-023.md) is implemented.
Report-only [AUD-WLD-024](../../docs/audits/AUD-WLD-024.md)
confirmed those closures but failed at CHML `0/1/0/0`: repeated
prepare/abandon cycles are not charged to an aggregate durable attempt limit.
[REM-AUD-WLD-024](../../docs/remediations/REM-AUD-WLD-024.md) is implemented at
`0c6e29aa8`. Report-only [AUD-WLD-025](../../docs/audits/AUD-WLD-025.md)
independently passes the complete clean matrix at CHML `0/0/0/0`.

P6-CMT-001 and P6-FAB-001 are accepted as internal unshipped increments.
P6-MC-001 product source `c790628e9` is complete with its report-only audit
pending. No public API, build, publication or production state changes at this
checkpoint; production remains accepted build 36 and Worldline remains
unshipped.

## P6-FAB-001 accepted native Fabric tournament increment

The [P6-FAB grounding](../../docs/evidence/WLD-001-phase6-fabric-grounding.md)
and [implementation evidence](../../docs/evidence/WLD-001-phase6-fabric-increment.md)
bind exact remediated product source `732ef6e26` (tree `ef7eac116`). Worldline owns a separate authenticated
tournament journal for one canonical specification, an exact terminal
candidate/evaluator outcome matrix and one deterministic result. Candidate and
evaluator admission binds reservation request, reservation, lease, worker,
schedule score, subject, expiry and work digests without retaining capability
tokens.

Success, failure, timeout and abstention are distinct terminal forms. Success
and abstention must cite an exact evaluation already persisted in the supplied
candidate Worldline; a structurally valid but unpersisted record fails closed.
Checked integer aggregation yields stable ranks, tie groups and a stable
Worldline-ID winner while preserving exact-top-tie and evaluator-disagreement
flags. The result binds a candidate/evaluator-sorted semantic matrix instead of
the append-order chain; all 24 focused arrival permutations produce identical
result bytes and digest. Result completion must be at or after every terminal
outcome, including through persisted retry and restart. These are comparative
facts only: the API has no approval or promotion operation.

The core adapter uses Fabric's existing reservation and deterministic worker
selection functions with all-or-nothing admission. A separate private,
versioned, digest-chained and bounded reservation owner durably prepares the
exact batch before tournament creation. It retains lease tokens, while
Worldline evidence retains digests. Idempotent activation, renewal, release and
expiry are restart-safe. Recovery releases prepare-only orphans, activates
prepare-plus-tournament handoffs, immediately expires elapsed active leases,
repairs only torn tails and refuses corrupt or cross-store-mismatched records.
Capacity exhaustion, insufficient lease coverage, disabled
master/reservation/scheduling gates and emergency stop refuse the tournament
before creation.

The unified `fabric-orchestration` config registers a default-enabled
tournament toggle and deny-only hard-bounded candidate, evaluator, outcome,
per-record encoded-byte, aggregate tournament-journal byte, work, duration and
score ceilings. The aggregate journal default is 64 MiB under a compile-time
256 MiB maximum, and cannot be set below the 16 MiB default per-record ceiling.
The journal charges specification, outcomes, result and frame overhead before
append. Cross-validation also requires the outcome ceiling to cover the
candidate/evaluator product, tournament work to fit the reservation ceiling and
duration to fit the lease ceiling. The existing 68-byte guest packet is
deliberately unchanged because public guest/API exposure belongs to later
Worldline phases.

Fresh remediation verification passes tournament 10/10, full Worldline 182
plus one intentional ignore, full core 361 plus one intentional ignore, full
standalone 242/242, Fabric 214/214, strict task-owned Worldline Clippy and the
seven-crate serial downstream check ladder. Whole-core Clippy retains exactly
24 inherited findings and no task-owned finding. The accepted increment is
still internal and unshipped. Initial report-only
[AUD-WLD-026](../../docs/audits/AUD-WLD-026.md) fails at CHML `0/2/2/1` on
durable reservation ownership/lifecycle, arrival-order-independent result
identity, causal completion, aggregate configured bytes and verification-count
drift. [REM-AUD-WLD-026](../../docs/remediations/REM-AUD-WLD-026.md) is
implemented, and report-only
[AUD-WLD-027](../../docs/audits/AUD-WLD-027.md) accepts exact source
`732ef6e26` at CHML `0/0/0/0`. The
[completion report](../../docs/completions/COMP-P6-FAB-001.md) closes the
increment. P6-MC-001 product source `c790628e9` is accepted by report-only
[AUD-WLD-030](../../docs/audits/AUD-WLD-030.md) at CHML `0/0/0/0`; P6-APP
source grounding is active. Production remains accepted build 36.

## P6-MC-001 accepted native Memory/Context lineage

Exact product source `c790628e9` (tree `c3d1f4a72`) implements the fixed
[Memory/Context contract](../../docs/evidence/WLD-001-phase6-memory-context-grounding.md).
Worldline stores typed, content-free references rather than Memory or Context
bodies. Each reference identifies Memory or Context, principal/federation/hive
scope, live/quarantined/stale/revoked/archived lifecycle, exact source database
and revision, stable reference/content digests and optional expiry. Principal
scope is domain-separated from the capsule principal.

One canonical evidence-set record is bound to the exact durable evaluation,
candidate revision and lineage, capsule principal, record time and predecessor
evidence digest. It has a dedicated journal kind, exact replay is idempotent,
conflicting replay fails, and recovery reconstructs the same reference counts
and manifest state. Configurable record, per-record reference, aggregate
reference, evidence-byte and promoted-lineage-byte limits are capped by
compile-time hard maxima and charged before append.

Promotion derives a second canonical envelope containing only live, unexpired
references. It binds the promotion, candidate, evaluation, evidence digest,
authority database/generation/offset, principal and logical time. The envelope
is inserted in the private `st_worldline_promotion.memory_context_lineage`
column in the same transaction as the authority delta and effect outbox, and it
is covered by the authority-transition digest. Empty bytes mean no evidence;
a valid zero-reference envelope proves evidence existed but none was eligible.

Recovery reconstructs the expected envelope from the branch and exact
authority coordinates, strictly decodes the stored canonical bytes and
requires equality before finalizing. Injected pre-commit failure leaves no
row; injected post-commit interruption recovers the one atomic row from the
historic authority base. Rejected candidates can retain speculative evidence
but create no authority lineage. Memory and Context guest crates keep their
existing host-free dependency boundaries and gain no authority operation.

The [implementation evidence](../../docs/evidence/WLD-001-phase6-memory-context-increment.md)
records Worldline 189 plus one intentional ignore, core Worldline 72 plus one
intentional ignore, datastore 221 plus 2 integration tests, Memory 160,
Context 88, strict task-owned lint, seven serial checks and the complete
current/prior-host compatibility drill. The
[completion report](../../docs/completions/COMP-P6-MC-001.md) and report-only
[AUD-WLD-030](../../docs/audits/AUD-WLD-030.md) accept the exact source at CHML
`0/0/0/0` without a fix. Post-acceptance
[AUD-WLD-031](../../docs/audits/AUD-WLD-031.md) also passes. That checkpoint
activated P6-APP under the fixed contract below. P6-APP and P6-CTRL were
subsequently accepted, and the current P6-INTEGRATION state is recorded at the
top of this article. Production remains accepted build 36 and Worldline remains
unshipped.

## P6-APP-001 accepted signed Construct approval

The [source-grounded contract](../../docs/evidence/WLD-001-phase6-approval-grounding.md)
starts from recovery point `099f20b75`. Approval journal kind 7 retains exact
format-1 re-execution records and gains explicit format-2 request, decision and
revocation dispatch. One request binds the latest durable evaluation and exact
candidate state/log/dependency/write/effect/module/config/registry/capability
truth, a bounded multi-reviewer approval/rejection policy, expiry and a
content-free Construct request-event reference.

Constructs remains the authenticated transport and audit feed. The standalone
bridge persists typed idempotent review events, then the node's real ES256 key
signs exact claims containing reviewer, decision, request, issuer/key authority
and persisted room/member/sequence/content-digest identity. Core verifies the
signature against the same generation-fenced external authority snapshot used
through promotion. Worldline stores no event body, bearer token, credential or
private key.

Canonical active approvals form a quorum certificate. Rejection threshold or
post-quorum revocation rejects the candidate; expiry, key rotation, reviewer
revocation, policy drift, missing `Approve` or missing separate `Promote`
capability fails before authority mutation. Ordinary promotion refuses a
caller-chosen non-zero digest.

Exact product source `0d34accaa974bc67ebfad985fd085f7f56124502`
(tree `daf81e4cc4d1a60d01e6d1d3679c865aeb8b4358`) implements the complete
contract. Format-1 recovery remains exact; bounded format 2, fenced ES256
authority, rejection/revocation/expiry, raw-digest refusal and the persisted
idempotent Construct bridge have full neutral/core/standalone/datastore and
serial downstream proof. See the
[implementation evidence](../../docs/evidence/WLD-001-phase6-approval-increment.md)
and [completion report](../../docs/completions/COMP-P6-APP-001.md). Report-only
[AUD-WLD-032](../../docs/audits/AUD-WLD-032.md) repeats the complete affected
suites, consumer ladder, boundary inspection and knowledge/release gates and
accepts the exact source at CHML `0/0/0/0`. P6-CTRL is accepted internally by
[AUD-WLD-033](../../docs/audits/AUD-WLD-033.md) at CHML `0/0/0/0`; the
[P6-INTEGRATION proof contract](../../docs/evidence/WLD-001-phase6-integration-grounding.md)
is now frozen and implementation is active. Production remains accepted build
36 and Worldline remains unshipped.

## Phase 6 unified control accepted internal increment

The [P6-CTRL grounding record](../../docs/evidence/WLD-001-phase6-control-grounding.md)
fixes one host-owned admission boundary in front of later public Worldline
surfaces. The implemented runtime atomically owns a versioned deny-by-default `[worldlines]`
policy, independently gate create/execute/evaluate/approve/promote/effect/
nesting/federation, authenticate and filter four allowlists before resource
lookup, and maps configured ceilings back into the real Worldline engine limit
types. Inspect, completion, reconciliation and recovery remain available during
an emergency stop so accepted work and audit evidence cannot be stranded.

Ordinary hot applies may only tighten rights. The unified-config manager now
reject invalid or widening adoption before replacing `config.toml`; explicit
rollback may restore the prior snapshot, while storage-root and journal/
manifest/codec storage ceilings remain restart-bound. Seven immutable UCR kinds
cover policy, evaluator, effect kind, executor, promotion policy, retention and
federation participant. They are declarative references, never capability grants
or process launch/trust decisions.

Observation is deliberately content-free: fixed-label counters, lazy bounded
Security Centre events, and request-time status expose operation/reason classes,
revision/digest and aggregate ceilings, not SQL, rows, vectors, prompts, model
output, effect bodies, credentials or keys. Exhaustive tests mutate every
numeric ceiling to zero and past its hard cap, violate every cross-field rule,
attempt every hot numeric widening, restart all seven UCR kinds and exercise
the complete standalone library at 265/265. Report-only
[AUD-WLD-033](../../docs/audits/AUD-WLD-033.md) independently repeats the full
neutral Worldline, datastore, core and standalone suites, affected serial checks,
strict task-owned lint and source/knowledge boundaries and accepts exact product
source `97dca8fee` (tree `4bfd027d4`) at CHML `0/0/0/0`. P6-INTEGRATION is active
for source grounding. This is not a shipped configuration claim; build 36 still
has no Worldline toggle or consumer endpoint.

Post-acceptance [AUD-WLD-034](../../docs/audits/AUD-WLD-034.md) found one Low
documentation-transition class across four live clauses. The bounded
[REM-AUD-WLD-034](../../docs/remediations/REM-AUD-WLD-034.md) preserves
historical observations while reconciling current truth, and report-only
[AUD-WLD-035](../../docs/audits/AUD-WLD-035.md) closes the loop at CHML
`0/0/0/0` for that four-file scope. Broader live-state
[AUD-WLD-036](../../docs/audits/AUD-WLD-036.md) found one Low class across ten
additional clauses; [REM-AUD-WLD-036](../../docs/remediations/REM-AUD-WLD-036.md)
is closed by exhaustive report-only
[AUD-WLD-037](../../docs/audits/AUD-WLD-037.md) at CHML `0/0/0/0`.

## P6-INTEGRATION-001 frozen complete proof

The [source-grounded integration contract](../../docs/evidence/WLD-001-phase6-integration-grounding.md)
does not infer integration from separately green components. One native
candidate must carry real SQL state, graph and vector dependencies and a
speculative executable effect through exact CMT evaluation, a durable bounded
Fabric tournament, Memory/Context evidence, signed approval, restart and atomic
promotion. A second restart must reproduce the authoritative data, eligible
effect, promotion-only evidence lineage and idempotent receipt.

The host boundary proof uses real Construct events and the node ES256 signer and
routes operation closures through the atomic Worldline control runtime. It must
observe every independent toggle, emergency stop, monotonic hot tightening,
explicit rollback, startup-only storage adoption and disabled-path zero work.
Telemetry remains content-free. The proof reuses the accepted engine owners;
it introduces no second coordinator and does not pull Phase 7 distribution or
Phase 8 public transport forward.

Fresh neutral grounding passes 197 Worldline tests with one intentional
compatibility ignore. Implementation is active. This remains internal proof,
not a shipped capability; production remains accepted build 36.

## Promotion is not merging

Promotion is serializability validation:

- If authority still equals the exact base, Fork validates and atomically
  applies the accepted delta.
- If authority advanced, every positive/negative row read, predicate/range,
  graph frontier, vector ranking, schema/module/config/capability/sequence and
  external observation dependency must remain valid and every write/constraint
  conflict must be absent.
- Incomplete proof causes an explicit conflict or policy-selected deterministic
  re-execution. Fork never silently rebases stale writes.

Vector dependencies require more than returned top-K rows. The planned query
certificate pins index/segment generations, query and parameter digests,
filters, exact results, candidate coverage and boundary evidence so a changed
outside vector cannot silently alter the accepted result.

## External effects

Speculative execution cannot send emails, launch unrestricted processes, call
live services or perform other external effects. It records typed bounded
intents. Promotion atomically makes accepted intents eligible; a durable
idempotent executor later claims them and records success, retry, ambiguous
outcome, failure or compensation.

Fork does not claim impossible atomicity with external systems. It provides a
durable outbox, fencing, idempotency and reconciliation contract.

## Relationship to existing engines

- Worldline owns alternative state, determinism, dependencies and promotion.
- Fabric schedules bounded worldline tournaments and distributed participants.
- CMT explains comparative causal chains and blast radius.
- Memory and Context carry worldline-aware evidence and working sets.
- Constructs provides authenticated human review and approval interaction but
  cannot bypass promotion policy.
- Unified registry/config holds evaluator/effect/promotion/retention/federation
  definitions and typed kill switches.
- Security Centre receives bounded secret-free integrity and denial evidence.

## Delivery contract

The implementation uses audited dependency-ordered phases, but none is a
product completion boundary. Every phase must pass a report-only audit against
the accepted plan and all CRAFTESB dimensions at CHML `0/0/0/0` before the next
begins. Final completion additionally requires host and guest artifacts from one
source pin, immutable staging, WASM publication, live smoke, restart,
rollback/restore, production promotion, reconciled manual/wiki/release facts and
a fresh whole-system CHML-zero audit.

Until those gates pass, this page remains explicitly **in development**.

The preserved initial plan audit is
[AUD-WLD-001](../../docs/audits/AUD-WLD-001.md); its mandatory remediation is
[REM-AUD-WLD-001](../../docs/remediations/REM-AUD-WLD-001.md). Engine work
was unblocked by report-only
[AUD-WLD-002](../../docs/audits/AUD-WLD-002.md) at CHML `0/0/0/0`. That closes
only the plan gate; the complete Worldline Engine remains unshipped. The
[Fork manual](../../docs/fork-ingest-manual.html) carries the same explicit
internal-development boundary and checkpoint links; it does not present the
surface as usable or shipped.
