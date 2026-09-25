---
upstream: https://github.com/weave-os/router
last_updated: 2026-09-25
---

# weave-router

Weave Router ("one endpoint. every model. always the right one.") is a drop-in, self-hostable LLM proxy for Anthropic, OpenAI, and Gemini that picks the best model for *every* request using a tiny on-box embedder — not a vibes-based prompt. It routes per **action** (not per turn) with a cluster scorer derived from Avengers-Pro, speaks Anthropic Messages / OpenAI Chat Completions / Gemini native (streaming, tools, vision), reaches OSS models (DeepSeek, Kimi, GLM, Qwen, Llama, Mistral) via OpenRouter or any OpenAI-compatible endpoint, and is BYOK by default with provider keys encrypted at rest. It is built in Go, runs a Postgres-backed stack (router + in-process ONNX embedder + optional HMM policy sidecar + dashboard), and is used by pointing Claude Code, Codex, Cursor, opencode, or your own app at `localhost:8080`.

- Upstream repository: https://github.com/weave-os/router
- Product / vendor: [Weave](https://www.workweave.ai); dashboard at `http://localhost:8080/ui/`
- Documentation: in-repo `docs/` — [Configuration](https://github.com/weave-os/router/blob/main/docs/CONFIGURATION.md), [Semantics](https://github.com/weave-os/router/blob/main/docs/SEMANTICS.md), [Analytics export](https://github.com/weave-os/router/blob/main/docs/ANALYTICS_EXPORT.md), [Architecture](https://github.com/weave-os/router/blob/main/AGENTS.md)
- Install: npm `@weave-os/router` (`npx @weave-os/router`, former `@workweave/router` alias); self-host with `make full-setup`
- License: Apache-2.0 (the Apache-2.0 release line begins with `router-v0.2.24` / npm `0.2.24`; earlier tags retain their original licenses)
- Stack: Go 1.25+; Postgres (installations, `rk_` keys, encrypted BYOK keys, usage); in-process ONNX cluster scorer; optional HMM policy sidecar; OTLP tracing

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
