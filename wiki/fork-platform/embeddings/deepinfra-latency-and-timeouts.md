# DeepInfra Latency and Embed Timeouts

> Sources: fork agents, 2026-07-10
> Raw: [embed timeout root-cause response](../../raw/embeddings/2026-07-10-embed-timeout-root-cause-response.md); [DIMS calibration changelog](../../raw/embeddings/2026-07-10-dims-calibration-changelog.md)

## Overview

Real DeepInfra latency for `Qwen/Qwen3-Embedding-8B` from this machine is
**7–24 seconds per call** (measured live 2026-07-10: 7.2 s via plain curl,
10–24 s through the engine), with call-to-call variance spanning ~1–24 s.
Any timeout ceiling below that range produces 100%-reproducible,
engine-only "operation timed out" failures that look like engine bugs but
are not. The fork's guest-side default embed timeout is **30 s**
(`DEFAULT_EMBED_TIMEOUT`, `crates/bindings/src/embed.rs`), raised from 8 s
after this exact failure hit the first production roll.

## The timeout layering (who wins)

- If the guest attaches **no** timeout, the host default applies:
  `HTTP_DEFAULT_TIMEOUT = 30 s` (`instance_env.rs`), hard max 180 s.
- If the guest attaches an explicit timeout (as `spacetimedb::embed` always
  does since AUD-SYS-001), it **overrides** the host default — which is how
  an 8 s guest default once silently lowered the effective ceiling from
  30 s and broke every slow call.
- The streaming clamp and cancel-select in `InstanceEnv::http_request` are
  gated on streaming state and **never** apply to plain procedures — that
  suspect was investigated and cleared with both code refs and live 10–24 s
  calls through the same function.

## Operational guidance

- A single fast curl sample (sub-second) proves nothing about the
  distribution; do not calibrate timeouts from it.
- DeepInfra occasionally exceeds even 30 s; the error is clean and a retry
  lands. Latency-sensitive flows should retry once, not raise the ceiling.
- Downstream modules pin bindings, so the timeout is compiled into their
  wasm: fixing a timeout problem means re-pinning + rebuilding the module
  (or setting `.with_timeout(...)` explicitly), never just swapping host
  binaries.

## See Also

- [Dimensions and similarity calibration](dimensions-and-similarity-calibration.md)
