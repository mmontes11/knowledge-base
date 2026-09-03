---
upstream: https://github.com/ollama/ollama
last_updated: 2026-08-22
---

# ollama — features

Significant feature areas, each linked to the matching upstream doc. The [documentation site](https://docs.ollama.com) is authoritative.

## Model management and registry

Ollama pulls models from the [ollama.com/library](https://ollama.com/library) registry (`ollama pull`, resumable) and pushes back with `ollama push` (requires sign-in). New models can be [created from a base model, GGUF, or safetensors](https://docs.ollama.com/api/create) with quantization and LoRA-style adapters, configured declaratively through a [Modelfile](https://docs.ollama.com/modelfile), or [imported from Hugging Face / llama.cpp checkpoints](https://docs.ollama.com/import).

## Inference capabilities

- [Streaming](https://docs.ollama.com/streaming) text generation
- [Tool calling](https://docs.ollama.com/tool-calling) (function calling)
- [Structured outputs](https://docs.ollama.com/structured-outputs) (JSON mode)
- [Vision](https://docs.ollama.com/vision) (multimodal text+image input)
- [Thinking](https://docs.ollama.com/thinking) (reasoning/reasoning-effort control for thinking models)
- [Embeddings](https://docs.ollama.com/embeddings) for semantic search and RAG
- [Web search](https://docs.ollama.com/web-search), available through the OpenAI Responses API since v0.32.11

## Engines and hardware

Inference runs on [llama.cpp](https://github.com/ggml-org/llama.cpp) for CPU, CUDA (NVIDIA), and ROCm (AMD), and on MLX-based engines for Apple silicon ([MLX is the default engine on macOS since v0.32.5](https://github.com/ollama/ollama/releases/tag/v0.32.5)). A [beta "new engine"](https://github.com/ollama/ollama/releases/tag/v0.32.5) (faster, lower memory usage) ships for Linux, macOS, and Windows since v0.32.5. See [hardware support](https://docs.ollama.com/gpu) and [context length](https://docs.ollama.com/context-length) (tunable per model, e.g. `num_ctx` in a Modelfile).

## Cloud models

[Cloud models](https://docs.ollama.com/cloud) offload inference to ollama.com while keeping local tooling unchanged: after `ollama signin`, models with the `-cloud` suffix run remotely; direct API access uses an `OLLAMA_API_KEY`. Ollama retires old cloud models periodically (announced via email/website; local models are unaffected) and supports a [local-only mode](https://docs.ollama.com/faq).

## Application compatibility and integrations

- [OpenAI-compatible API](https://docs.ollama.com/api/openai-compatibility) (`/v1/chat/completions`, `/v1/completions`, `/v1/responses`, `/v1/models`, `/v1/embeddings`)
- [Anthropic-compatible API](https://docs.ollama.com/api/anthropic-compatibility)
- [`ollama launch`](https://docs.ollama.com/cli) configures and starts coding/agent integrations (Claude Code, Codex, Droid, OpenCode, VS Code, …); the full catalog is in the [integrations docs](https://docs.ollama.com/integrations/overview)

## Operations

Server behavior (logging, keep-alive, concurrency, memory) is configured through `OLLAMA_*` environment variables, listed by `ollama serve --help` and documented in the [FAQ](https://docs.ollama.com/faq). [Docker](https://docs.ollama.com/docker) packaging is provided upstream; OS installers per platform are described under [Linux](https://docs.ollama.com/linux) / [macOS](https://docs.ollama.com/macos) / [Windows](https://docs.ollama.com/windows). Troubleshooting guidance lives in the [troubleshooting page](https://docs.ollama.com/troubleshooting).
