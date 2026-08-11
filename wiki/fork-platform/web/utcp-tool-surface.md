# UTCP Tool Surface — how agents call the fork

> Sources: [docs/FORK-UTCP.md](../../docs/FORK-UTCP.md); `SpacetimeDB/modules/agent-starter/src/web.rs`; `SpacetimeDB/modules/agent-starter/src/tools.rs`; historical `SpacetimeDB/tools/utcp-live-e2e` evidence
> Updated: 2026-07-30

## Overview

Modules publish a machine-readable UTCP manual at `GET /utcp`, derived from
the router at finalize time — it can never list a route that doesn't exist,
and an enrichment `ToolSpec` matching no route panics at module init. Agents
call tools natively over HTTP; MCP-only clients use the official
`@utcp/mcp-bridge` (a meta-tool server: `list_tools`/`search_tools`/
`call_tool`, qualifying names as `<manual>.<tool>`).

## Conventions (workspace hub contract)

- **Namespace REQUIRED**: every tool name is `fork_*`
  (`UtcpConfig::namespace("fork")`; `_` join because MCP restricts the name
  charset; missing/invalid namespace = init panic).
- Every tool: exactly one `band:` tag (observe | recall | act |
  orchestrate), `cap:` tags (read-only/idempotent/mutating), `cost_hint`,
  and an `idempotent` bool on act/mutating tools — enforced by test.
- Manual root carries a one-sentence `description` (the hub's repo card).
- Streaming tools opt into `call_template_type: "sse"` via
  `ToolSpec::sse()` (values verified against the bridge plugin's set).

## The 12 reference tools (`agent-starter`)

memory: `fork_remember`, `fork_recall`, `fork_get_memory` · reconcile:
`fork_find_contradictions`, `fork_reconcile`, `fork_reconcile_log` ·
fabric/context: `fork_submit_work`, `fork_claim_work`, `fork_get_work` ·
events: `fork_app_events`, `fork_app_events_stream` (SSE) · `fork_health`.
Writes return 202 naming the verify tool; reads answer directly. “Reference”
describes the shipped module surface, not a claim that the retired port-3000
`agent-starter` database is currently running. See
[Live Deployment](../operations/live-deployment.md) for mutable runtime state.

## Interop lessons (paid for live — don't relearn)

- Module HTTP mounts at `/v1/database/<db>/route/...` (NOT `/http`).
- The manual's `base_url` bakes the database name — publish under the
  canonical name or every tool URL 404s.
- The reference UTCP client sends POST arguments as **query params** unless
  the template declares `body_field` — handlers accept JSON body OR query
  params (`parse_args`).
- The whole surface is proven end-to-end: live MCP bridge session drove
  recall → contradictions → reconcile → verify → claim-with-frame.

## See Also

- [Web platform](web-platform.md)
- [Live deployment](../operations/live-deployment.md)
