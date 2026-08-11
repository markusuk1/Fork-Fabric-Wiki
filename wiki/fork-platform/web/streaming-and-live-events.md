# Streaming & Live Events — the push-driven surface

> Sources: fork-ingest-manual §8.1; STREAM-EVENTS changelog entry; live smoke records

## Overview

Two push mechanisms, both shipped and live-verified:

1. **WebSocket table subscriptions** (core): subscribe SQL over any visible
   table at `/v1/database/<db>/subscribe`; every committed row change is
   pushed at commit time. Because events ARE committed transactions, the
   stream is transactionally honest — no phantom events, none missed.
   Fork-extended for vector/graph/causal deltas.
2. **SSE over module HTTP**: finite-tail `text/event-stream` responses
   (guest has no reactor — the infinite tail is the WebSocket's job),
   consumable by curl/EventSource/bridged MCP clients.

## The app-event feed (configurable observability)

agent-starter records its own life in the public `app_event` ring table
(512 cap): `module_init` + client connect/disconnect always; per-request
`http_request` rows OPT-IN via `configure_events(true)` (off by default —
an access log doubles write traffic). Consumable by poll
(`fork_app_events`), SSE (`fork_app_events_stream`), or live subscription.

## What push unlocks (the argument, in one list)

Push-driven agents instead of polling (reaction time = commit latency);
the Fabric as a zero-infrastructure work bus (submit → subscribed workers
race to claim); live CMT audit feeds where every entry carries its causal
chain; real-time quarantine review closing the reconcile loop;
always-current UIs (the subscription IS the data binding); instant hub
re-sync on provider changes.

## Proven

WS subscriber on the deployed box received the insert push at commit time
(`spacetime subscribe` + HTTP trigger, 2026-07-10); SSE frames verified
live the same day.

## See Also

- [Causal evidence subscriptions](../cmt/causal-evidence-subscriptions.md)
- [Fabric engine](../engines/fabric-engine.md)
