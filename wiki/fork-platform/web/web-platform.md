# Web Platform (`spacetimedb-web`) — apps served from modules

> Sources: [docs/FORK-OVERVIEW.md](../../docs/FORK-OVERVIEW.md) §9; fork-ingest-manual §8; web-rag-demo (the worked hardening example)

## Overview

HTTP/SSE/SSR applications served directly from a module, with retrieval
built in — and by the F2 decision this IS the fork's cross-language client
surface: non-Rust consumers reach vector/graph/CMT/recall through
module-defined HTTP endpoints, not per-language module SDKs.

## The stack

- **Router**: `App::new().route(...)` with typed extractors; handlers are
  `async fn(WebRequest, PathParams) -> Response`.
- **Pinned middleware order (cannot be misordered)**: Logging → CORS →
  Compression → RateLimit → Auth → CSRF → Custom → BodyLimit. Auth is one
  global fail-closed gate (`RequireAuth::jwt(..).except([..])`); CSRF is
  Origin-allowlist plus session-bound double-submit where sessions exist.
- **Reads**: `Tx.read(|tx| ...)` — short anonymous read tx under the SHARED
  lock; must be pure. **Durable writes go through reducers, never
  handlers** — the pattern is validate → `volatile_nonatomic_schedule_immediate!(reducer(args))`
  → 202 with a verify pointer.
- **Streaming**: `Sse`/`SseEvent` builders on a separate streaming instance
  pool (503 when exhausted), cancel-on-disconnect, bounded timeouts. Guest
  SSE streams are FINITE tails by design — the guest has no reactor; the
  infinite live tail is the WebSocket subscription.
- **Sessions/assets/multipart/compression**: framework primitives, all
  audit-hardened (AUD-WEB-001/002 closed).

## Handler ↔ host context

Handlers have no `ReducerContext`; a thread-local `with_ctx` pattern
(single-threaded guest) carries the `HandlerContext` for the request —
that's how the embed seam reaches `ctx.http`.

## Production posture

agent-starter ships all routes public for zero-config first contact —
web-rag-demo is the worked example of turning on the full hardening
(JWT auth, CSRF scoping, per-route rate limits) and is the template to copy.

## See Also

- [UTCP tool surface](utcp-tool-surface.md)
- [Streaming and live events](streaming-and-live-events.md)
- [App hosting and gateway](app-hosting-and-gateway.md)
