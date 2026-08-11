# Local Model Sidecar Cutover

> Sources: FORK-LMS-001 live verification, 2026-07-12
> Raw: [FORK-LMS-001 sidecar evidence](../../raw/embeddings/2026-07-12-local-model-sidecar-cutover.md)
> Updated: 2026-07-12

## Overview

The fork can point its existing procedure-level embedding and reranking HTTP
clients at a local model sidecar without putting inference inside the database
engine. The host exception is disabled by default and deliberately narrower
than the existing test-only loopback feature.

## Configuration and boundary

```toml
[procedure-http]
local-model-sidecar-port = 18081
```

This admits only `http://127.0.0.1:18081/v1/embeddings` and
`http://127.0.0.1:18081/rerank`. Other loopback addresses or ports,
`localhost` and other DNS names, extra path segments, HTTPS, user-info,
fragments, LAN/private ranges, and redirect escapes are rejected. Port zero is
invalid configuration.

## Procedure configuration

The guest clients remain unchanged. Construct their canonical provider config,
then override only the URL:

```rust
let embed = EmbedConfig::deepinfra(api_key.clone())
    .with_url("http://127.0.0.1:18081/v1/embeddings");
let rerank = RerankConfig::deepinfra(api_key)
    .with_url("http://127.0.0.1:18081/rerank");
```

This retains `Qwen/Qwen3-Embedding-8B`, 1536 dimensions, normalization, and
`Qwen/Qwen3-Reranker-8B`. Only endpoint routing changes.

## Operational proof

The live smoke module proved reverse-wire-order embedding responses are restored
to input order, every vector is 1536-dimensional, and reranking returns one
finite score per input document in order. Loading errors, denied destinations,
redirect escapes, and a stopped sidecar all returned bounded structured errors.
Both sidecar and fork restart drills passed. Switching the procedure URLs back
to the prior DeepInfra endpoints also passed with real calls.

## Connection reuse, metrics, and cache behavior

Procedure HTTP clients are owned once per replica and reused across calls, so
reqwest can pool connections instead of rebuilding a client for every request.
The host exports `spacetime_procedure_http_request_duration_seconds` alongside
the request/response byte counters and terminal outcome counters.

`EmbedKey::for_text` and `RerankKey::for_request` include the configured endpoint
as well as model and dimensions/query content. A sidecar/provider cutover therefore
starts a distinct cache namespace even when the model name is unchanged. The
local-sidecar smoke procedures demonstrate bounded private table caches: cache
reads occur in a read transaction, HTTP misses occur with no transaction open,
and cache fills occur in a separate write transaction. New entries stop being
admitted at 10,000 per cache; hits and uncached inference continue normally.

Live OPT-001 proof produced zero hits on the first two-text embed request and two
hits on the identical second request. Only one procedure HTTP request was then
present in the duration histogram, confirming the hit path skipped HTTP.

## See Also

- [DeepInfra Latency and Embed Timeouts](deepinfra-latency-and-timeouts.md)
- [Embedding Dimensions and Similarity Calibration](dimensions-and-similarity-calibration.md)
- [Live Deployment](../operations/live-deployment.md)
