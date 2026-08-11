# Security Centre (SEC-CENTRE-001)

> Sources: [COMP-SEC-CENTRE-001](../../docs/completions/COMP-SEC-CENTRE-001.md); [final re-audit](../../docs/audits/AUD-SEC-CENTRE-001-R2.md); [architecture and final BR-DEC-014 correction](../../docs/SEC-CENTRE-001-ARCHITECTURE.md)
> Final source: 2e491dc15; carried by fork build 27 (`6e20ac8d9`)
> Updated: 2026-07-24

## What it is

One place to answer "what happened, security-wise?" on a node. Nine formerly
scattered surfaces emit one typed, secret-free event; those events are chained,
correlated into incidents, folded into a posture, and served over
`/v1/security/*`.

It is built on the fork's own primitives, not an external log stack — the
differentiator is that events sit next to the causal metadata and query engine
that already exist here.

## Scope and storage (BR-DEC-014)

Fork tables are **per-database only** and there is no host-owned database, so
all Tier A evidence uses the node's hash-chained store under
`<data-dir>/security-centre/`. Operator telemetry (TLS, ACME, GPU, config,
admin denials) is visible only to host-management admins. Tenant-scoped guest
events carry database and publisher identity in the same chain; database reads
are identity-scoped so one tenant cannot read another tenant's evidence.

There is **no native per-database SQL table or subscription surface in V1**.
The earlier BR-DEC-013 `DatabaseSink`/private-system-table design was
superseded after implementation review by BR-DEC-014. Native SQL and
subscriptions remain an explicitly Partial follow-up rather than a shipped
claim.

## Integrity model — read this before trusting the chain

Each Tier A event carries `seq` + `prev_hash`; its hash is
`blake3(seq ‖ prev_hash ‖ canonical bytes)`. A walk detects **in-place**
tampering: mutation, a recomputed-hash splice (breaks the *next* link), a
mid-chain deletion, and tail truncation.

**A hash chain cannot detect a wholesale rollback.** Restoring an older copy of
the store is internally consistent — every surviving link recomputes — so
`verify` correctly reports `intact`. This was proven on a live node: 25 events
vanished with a clean verdict.

That is why a **durable monotonic anchor witness** is kept in
`security-centre/anchor-witness.json`, *beside* and never inside the store. It
records the highest `(seq, hash)` ever seen and never decreases, so simply
running a rolled-back node cannot lower it. At startup the store head is
compared against it:

- head **behind** the witness → Critical `sec.chain.rollback_suspected`
- same seq, different hash → Critical `sec.chain.head_diverged`
- witness absent beside a populated store → Warning `sec.chain.witness_missing`

**Limits, stated plainly.** The witness is local: an attacker with write access
to the data directory can delete it too — which is why its absence is itself
reported. Detection granularity equals the anchor interval (default 300 s,
plus a write at boot): a rollback to a point *after* the last witnessed head is
not caught. A remote witness is the designed V2 seam, not a V1 claim.

## Operating it

| Endpoint | Purpose |
|---|---|
| `GET /v1/security/posture` | Node level + per-subsystem/per-database rollups, each citing incident ids |
| `GET /v1/security/events` | Paged Tier A events (`before_seq`, `limit`≤1000, `min_severity`, `name_prefix`, `subsystem`) |
| `GET /v1/security/incidents` | Correlated incidents with rule id, rule-set version, firing event seqs and digests |
| `GET /v1/security/verify` | Walk the chain and report the verdict |
| `GET /v1/security/chatter` | Tier B ring — **unchained, not evidence** |
| `GET /v1/security/feed` | SSE: events and incidents as they form (lossy; reports `lagged`) |

`/v1/status` carries a live `security_centre` block (chain head + anchored
head). It is read live on every request — a frozen copy would defeat anchoring.

**Access is deny-by-default.** Admins come from `[host-management]
admin-identities`, the same source as the TLS and host-config surfaces. An
empty set means the read surface is **off**, not open. Every authorized read
emits `sec.centre.read`: reads of an audit log must themselves be audited.

## Configuration

All seven `[security-centre]` keys are **HOSTCFG-hot** — an operator retunes
budgets on a node under load, which is exactly when a restart is least
acceptable.

```toml
[security-centre]
events-per-sec = 200            # per-source ingest ceiling
bytes-per-sec = 262144
ring-entries = 8192             # Tier B caps (whichever binds first)
ring-bytes = 8388608
raw-retention-days = 30         # raw Tier A events
incident-retention-days = 365   # digests outlive the raw events
chatter-retention-days = 7
```

Retention deletes are **explicit**. The fabric aging primitive dead-letters
rather than deleting; inheriting that would quietly turn a 30-day promise into
append-forever.

## Behaviour under load

The hot path never blocks: producers `try_send` onto a bounded channel behind a
per-source token bucket and byte budget. Overflow is dropped and counted, with
at most one rate-limited `sec.ingest.loss` event per source per window —
flooding to evict evidence is itself an attack worth recording, and the loss
event cannot amplify. Measured live: 300 requests against a 50/s budget left
`/v1/status` latency unchanged at 0.63 ms.

## What it deliberately does not do

LLM-based detection (rules are deterministic data — "the engine may learn; it
may not consult"), OS log ingestion, cross-node aggregation, replacing
Prometheus (ingest health is metrics, not events), and alerting integrations.
Semantic retrieval over events is a **seam**: the fork's vector/fusion/graph
retrieval and aging scheduler are guest-side only, and embeddings need an
external provider, so V1 ships structured filters and reserves the schema.

The first closeout was incomplete. A completeness audit found six material
gaps—incident/posture persistence, database-scoped evidence, ACME convergence,
metrics, bounded retention, and shutdown flushing. All six were remediated and
the final re-audit reached 0 Critical/High/Medium/Low. Requirements R3
(native per-database tables/subscriptions) and R6 (semantic retrieval) are
recorded as Partial rather than overstated.

## See also

- [Native TLS and ACME](native-tls-and-acme.md) — the `TlsEvent` discipline this generalises
- [Staged builds & compatibility](staged-builds-and-compat.md) — build 26 registry row
