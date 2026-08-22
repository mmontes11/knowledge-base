---
upstream: https://github.com/ollama/ollama
last_updated: 2026-08-22
---

# ollama

Ollama is a fast, local runtime for Open LLMs: it runs models from the [ollama.com model library](https://ollama.com/library) and user-created models on a single server, exposed through a JSON REST API (port `11434`), an [OpenAI-compatible API](https://docs.ollama.com/api/openai-compatibility), and the `ollama` CLI. Inference is built on [llama.cpp](https://github.com/ggml-org/llama.cpp) (CPU, CUDA, and ROCm) plus MLX-based engines for Apple silicon.

- Upstream repository: [ollama/ollama](https://github.com/ollama/ollama)
- Documentation: [docs.ollama.com](https://docs.ollama.com)
- License: MIT ([LICENSE](https://github.com/ollama/ollama/blob/main/LICENSE))
- Default port: `11434` (native API and `/v1` compatibility endpoints)
- Model registry: [ollama.com/library](https://ollama.com/library)

## Usage in this stack

Our infrastructure repository deploys Ollama through a Flux `HelmRelease` in the `ai` namespace using the `ollama` chart (v1.71.0) from a private Helm repository, GPU-enabled on a 1x NVIDIA node, with models pulled at install (`qwen3-coder:30b`, `qwen3-vl:8b-instruct`, plus a locally created `gemma4:26b-256k`) and a 300 Gi topolvm-backed PVC for the model cache. It is exposed to the internal network through a Traefik Gateway `HTTPRoute`.

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
