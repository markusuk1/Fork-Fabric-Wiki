# App Hosting & Gateway — serving real apps on the built-in server

> Sources: [SpacetimeDB/deploy/README.md](../../SpacetimeDB/deploy/README.md); [SpacetimeDB/deploy/APP-GATEWAY.md](../../SpacetimeDB/deploy/APP-GATEWAY.md); COMP-APP-HOST-001; [docs/completions/COMP-APP-HOST-002.md](../../docs/completions/COMP-APP-HOST-002.md); [SpacetimeDB/docs/completions/COMP-APP-HOST-003.md](../../SpacetimeDB/docs/completions/COMP-APP-HOST-003.md); [docs/app-host-native-proxy-RESPONSE.md](../../docs/app-host-native-proxy-RESPONSE.md); [docs/FORK-DEPLOYMENT.md](../../docs/FORK-DEPLOYMENT.md)

## Overview

The standalone server is both database and web server. Two hosting layers,
one built-in and primary, one optional edge:

## Built-in: `[app-hosting]` (APP-HOST-001)

Host/path rules in standalone `config.toml` route otherwise-unmatched
public requests **straight to a published module's HTTP handlers** — so
`myapp.local` can serve a module directly, no reverse proxy. `/v1` and
`/internal` stay reserved for core APIs; rules require an explicit database
(no silent default routing). Dev/prod = separate database names
(`myapp-dev` / `myapp-prod`) with rules pointing hostnames at each.

## Native reverse-proxy upstream (APP-HOST-002)

An `[[app-hosting.apps]]` rule now targets **exactly one** of `database` (module
dispatch, above) or `upstream` (`host:port`) — a native reverse proxy. A matched
request is forwarded over a fresh HTTP/1.1 connection with **streamed** bodies
(chunked/SSE pass through) and full **`Upgrade`→WebSocket** passthrough, so an
external local dev server (Vite, any framework) is served live — **HMR and app
WebSockets work end to end** — behind the fork's own address, no module publish,
single binary. Hop-by-hop headers are stripped per RFC 9110 (the `Upgrade` pair
preserved on a handshake); an unreachable upstream returns a clean **502**, never
a panic/hang; `/v1` and `/internal` stay reserved. An optional per-rule
`[apps.auth]` HTTP Basic gate (constant-time) challenges unauthenticated requests
**before** the target is reached — module or proxy alike. It is native axum/hyper
(not a module: buffered guest HTTP can't stream or upgrade). This **supersedes the
Python `app_gateway.py` for the live-preview use case**; keep the Caddy layer only
for edge concerns (TLS, public domains). Fulfilled the AI-Collab-v3 request
([docs/app-host-native-proxy-RESPONSE.md](../../docs/app-host-native-proxy-RESPONSE.md)).

## Runtime rule management (APP-HOST-003)

The `[[app-hosting.apps]]` rules above are the immutable **base**. Rules can also
be added/removed **at runtime — no restart** — and are persisted node-locally to
`<data-dir>/app-host-rules.json` so they survive a restart. The base config and
the runtime overlay merge into a hot-swappable snapshot (`AppHostRegistry`) the
request path reads under a short read-lock (the router injects
`Extension<Arc<AppHostRegistry>>`, not a static `Arc<AppHostOptions>`). This is
the fork primitive a fleet/preview UI (e.g. AI-Collab) builds on: spin up a
preview, register `upstream=127.0.0.1:port`, surface the URL, tear it down — live.

API (all admin-authenticated):

| Method | Path | Effect |
|---|---|---|
| `GET` | `/v1/app-host/rules` | list runtime overlay rules + `mutation_enabled` |
| `POST` | `/v1/app-host/rules` | add/replace by `name` (body = an `[[app-hosting.apps]]` entry as JSON) |
| `DELETE` | `/v1/app-host/rules/<name>` | remove a runtime rule |

**Security — deliberate, because this routes live traffic:**

- **Deny-by-default.** Mutation is gated on `[app-hosting] admin-identities`
  (hex). **Empty ⇒ every operation returns 403** — opt-in, so a misconfiguration
  can't silently expose the control surface. Reads are gated too (rules can carry
  basic-auth credentials). Enforced with `SpacetimeAuthRequired` (a valid
  SpacetimeDB JWT) at the endpoint layer, before the registry is touched.
  App-host is node-level and the fork has no node-owner identity, so an explicit
  admin-identity list is the gate.
- **SSRF guard.** A rule *added at runtime* whose target is an `upstream` must
  resolve to a loopback/private address unless `allow-public-upstreams = true`.
  Static config rules are trusted/exempt. Non-IP hostnames are treated as
  non-private (conservative — a DNS name could resolve anywhere).

Caveat: the overlay file and the `GET` response contain any basic-auth
credentials in plaintext — same trust level as `config.toml`, node-local,
admin-only; secure the data directory. Noted follow-ups: a CLI/UTCP wrapper and a
fully-subscribable live-push variant (AI-Collab's UX domain).

## Optional edge: the Caddy gateway (APP-GATEWAY-001)

`deploy/app_gateway.py` renders Caddy host blocks from JSON — friendly
hostnames rewritten to `/v1/database/<db>/route{uri}` — for when you need
public TLS/domains in front. Deliberately allowlist-shaped: it does NOT
expose the database control plane; per-app `"expose_subscribe": true` adds
exactly one reviewed route (`/subscribe` → the live WebSocket subscription
endpoint), default off.

## Process model

`spacetime start --listen-addr ... --data-dir ... --jwt-key-dir ...` — one
process, plain HTTP (no TLS/domain flags; that's the edge's job), no
self-daemonizing (OS supervision: NSSM on Windows, systemd artifacts in
deploy/).

## See Also

- [Live deployment](../operations/live-deployment.md)
- [Web platform](web-platform.md)
- [Fork versioning & drift detection](../operations/fork-versioning.md) — the
  `/v1/status` runtime endpoint served alongside the app-host routes.
