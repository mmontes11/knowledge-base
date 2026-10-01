---
upstream: https://github.com/weave-os/router
last_updated: 2026-10-01
---

# weave-router — releases

Weave Router versions its **software** through git tags and the npm package (`@weave-os/router`, former `@workweave/router` alias) rather than GitHub Releases; the GitHub Releases page is used to publish **model artifacts** for the optional HMM policy sidecar. The Apache-2.0 software release line begins at `router-v0.2.24` (npm `0.2.24`); earlier tags retain the licenses they shipped with. Release pages: https://github.com/weave-os/router/releases.

## router-v0.2.28 — 2026-09-29

[Tag `router-v0.2.28`](https://github.com/weave-os/router/tree/router-v0.2.28) — npm `0.2.28`.

- Telemetry deepens: records user prompt gaps, error classes, and per-request tool results (new `model_router_request_telemetry` columns plus a `router.session_turn_clocks` table) (#1529).
- `GET /v1/sessions/:session_id/cost` now accepts a read-only analytics key in addition to a router key (#1544).
- Removed the balance-hold system (#1535); added a pinned sparse-domain scorer for HMM arm selection (#1536).
- Fixes: cap Anthropic output tokens per model (#1551), bound Postgres waits (#1531), omit unsupported reasoning summaries for Cortex (#1547), correct Claude routing status for parked/overridden settings (#1550); perf: reduced row- and escalation lock contention (#1538, #1540).

## router-v0.2.27 — 2026-09-28

[Tag `router-v0.2.27`](https://github.com/weave-os/router/tree/router-v0.2.27) — npm `0.2.27`.

- Added Claude Sonnet 5.5 to the model catalog (#1527).
- Tool-choice fidelity: forward `tool_choice: none` to OpenAI (#1501) and preserve Anthropic `tool_choice: none` on Responses (#1520); classify Anthropic `container_upload` blocks as file input (#1502).
- Hide routing markers during passthrough (#1525); report an unknown passthrough model instead of missing provider keys (#1528).
- OpenCode: raise the auto context limit to 400K (#1476) and report a 500k context window (#1530).
- Generic revisioned installation routing policy for the serving fleet (#1519).

## router-v0.2.26 — 2026-09-27

[Tag `router-v0.2.26`](https://github.com/weave-os/router/tree/router-v0.2.26) — npm `0.2.26`.

- Removed router-side compaction; the router now returns the provider's native overflow error instead (#1480).
- `GET /v1/router/models?scope=catalog` returns the full compile-time model catalog without a routing credential (#1514).
- Dropped unsupported Together model bindings, keeping Fireworks/OpenRouter alternatives and regenerating pricing (#1513).
- Translation fixes: handle OpenAI context-length-exceeded errors correctly (#1508) and fix the "message too big for every model" error (#1509); widen the Anthropic header timeout for large prompts (#1507).
- OpenCode: handle sessions correctly and classify title-generation turns (#1503, #1504, #1505); fix unambiguous Read line prefixes for non-Anthropic models (#1481).
- Serving control-plane hardening: admission-based routing of enrolled installations, additive `policyctl serving publish-release`, managed serving stamping, and fail-closed admission gates (#1488, #1491, #1499, #1510, #1516).

## router-v0.2.25 — 2026-09-27

[Tag `router-v0.2.25`](https://github.com/weave-os/router/tree/router-v0.2.25) — npm `0.2.25`.

- Fix routing failing when a third-party subscription is exhausted (#1487).

## hmm-model-v1 — 2026-07-13 (prerelease)

[Release page](https://github.com/weave-os/router/releases/tag/hmm-model-v1) — the frozen HMM policy model artifact.

- Ships the frozen Hidden-Markov-Model routing policy consumed by the optional HMM sidecar (`make up-hmm`).
- Embedding model: Google Gemini Embedding 2, 3072 dimensions. See [HMM sidecar docs](https://github.com/weave-os/router/blob/main/sidecars/hmm/README.md) for artifact verification and embedding compatibility.

No other GitHub Releases are published as of 2026-10-01; check the npm package for the latest software version.
