---
upstream: https://github.com/weave-os/router
last_updated: 2026-09-25
---

# weave-router — features

Weave Router is a self-hostable, BYOK LLM router that chooses the right upstream model for every request. Key capability areas, each linked to the in-repo docs that cover it.

## Per-action model routing

- **Cluster scorer**: a scorer derived from [Avengers-Pro](https://arxiv.org/abs/2508.12631) picks the right model from your enabled providers for every upstream API request, using an in-process ONNX embedder — not a prompt-based heuristic.
- **Routes per action, not per turn**: canonical session/round/turn/action/step terminology is defined in [Semantics](https://github.com/weave-os/router/blob/main/docs/SEMANTICS.md).

## Multi-provider

- **Speaks everyone's API**: Anthropic Messages, OpenAI Chat Completions, and Gemini native — streaming, tools, and vision. (README)
- **Knows OSS too**: DeepSeek, Kimi, GLM, Qwen, Llama, Mistral via OpenRouter or any OpenAI-compatible endpoint. (README)

## BYOK and security

- **BYOK by default**: provider keys stay on your box, encrypted at rest. [Configuration](https://github.com/weave-os/router/blob/main/docs/CONFIGURATION.md)

## Policy sidecar

- **Optional frozen HMM policy**: run the HMM policy as a companion container (`make up-hmm`, needs a Google API key); uses the frozen `hmm-model-v1` artifact (Gemini Embedding 2, 3072 dims). [HMM sidecar](https://github.com/weave-os/router/blob/main/sidecars/hmm/README.md) / [Policy router harness](https://github.com/weave-os/router/blob/main/docs/POLICY_ROUTER_HARNESS.md)

## Observability

- **OTLP out of the box**: traces to the Weave dashboard (`http://localhost:8080/ui/dashboard`) or Honeycomb, Datadog, Grafana, etc. (README)
- **Analytics export**: pull raw routing decisions into your own warehouse with a read-only key. [Analytics export](https://github.com/weave-os/router/blob/main/docs/ANALYTICS_EXPORT.md)

## Client integration

- First-class installers for **Claude Code**, **Codex** (OpenAI CLI), **opencode**, **pi** (+ Loom UI), and **Cursor** (early beta), with on/off switching and per-model enable/disable from the terminal or dashboard. [install](https://github.com/weave-os/router/blob/main/install/README.md)

## Benchmarking

- [Codex benchmark harness](https://github.com/weave-os/router/blob/main/bench/README.md): reproduces the published SWE-Atlas QnA and Terminal-Bench 4.0 comparisons of `/beta` routing vs. direct and OpenRouter controls.
