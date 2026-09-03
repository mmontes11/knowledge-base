---
upstream: https://github.com/ollama/ollama
last_updated: 2026-08-22
---

# ollama — API reference

Ollama exposes three main interfaces: a native JSON REST API on port `11434`, an [OpenAI-compatible API](https://docs.ollama.com/api/openai-compatibility) under `/v1`, and the `ollama` CLI. The canonical API documentation lives at [docs.ollama.com/api](https://docs.ollama.com/api) with a machine-readable [OpenAPI spec](https://docs.ollama.com/openapi.yaml); the raw endpoint reference is also in the [docs/api.md](https://github.com/ollama/ollama/blob/main/docs/api) source file.

## Native REST endpoints

Served by the Ollama server (default `http://127.0.0.1:11434`):

| Endpoint | Purpose | Upstream docs |
| -------- | ------- | ------------- |
| `POST /api/generate` | Completion for a prompt; streaming, JSON/structured output, images, thinking. | [generate](https://docs.ollama.com/api/generate) |
| `POST /api/chat` | Next chat message; tool calling, vision, structured outputs, thinking. | [chat](https://docs.ollama.com/api/chat) |
| `POST /api/embed` | Vector embeddings for text (semantic search, RAG). | [embed](https://docs.ollama.com/api/embed) |
| `POST /api/create` | Create or fine-tune a model from a base model, GGUF, or safetensors; quantization, adapters. | [create](https://docs.ollama.com/api/create) |
| `POST /api/show` | Model details: Modelfile, template, parameters, quantization level. | [show model details](https://docs.ollama.com/api-reference/show-model-details) |
| `GET /api/tags` | List models available locally. | [tags](https://docs.ollama.com/api/tags) |
| `GET /api/ps` | List models currently loaded in memory, with size and processor split. | [ps](https://docs.ollama.com/api/ps) |
| `POST /api/pull` | Download a model from a registry (resumable; streams progress). | [pull](https://docs.ollama.com/api/pull) |
| `POST /api/push` | Upload a model to a registry (requires `ollama signin`). | [push](https://docs.ollama.com/api/push) |
| `POST /api/copy` | Copy a model to a new name. | [copy](https://docs.ollama.com/api/copy) |
| `POST /api/delete` | Delete a model. | [delete](https://docs.ollama.com/api/delete) |
| `POST /api/version` | Server version (and `version.min` for the CLI floor). | [get version](https://docs.ollama.com/api-reference/get-version) |
| `POST`/`HEAD /api/blobs/:digest` | Push or check model file blobs; used internally for model file transfer. | [docs/api.md](https://github.com/ollama/ollama/blob/main/docs/api) |

## OpenAI-compatible endpoints

Drop-in endpoints for existing OpenAI applications (`api_key` is required but any value is accepted, e.g. `ollama`):

| Endpoint | Purpose | Upstream docs |
| -------- | ------- | ------------- |
| `POST /v1/chat/completions` | Chat completions; streaming, JSON mode, vision, tools, reasoning/thinking control, reproducible outputs. | [OpenAI compatibility](https://docs.ollama.com/api/openai-compatibility) |
| `POST /v1/completions` | Prompt completions; streaming, JSON mode. | [OpenAI compatibility](https://docs.ollama.com/api/openai-compatibility) |
| `POST /v1/responses` | OpenAI Responses API (non-stateful); streaming, tools, reasoning summaries. Added in v0.13.3; web-search support since v0.32.11. | [OpenAI compatibility](https://docs.ollama.com/api/openai-compatibility) |
| `GET /v1/models` | List models. | [OpenAI compatibility](https://docs.ollama.com/api/openai-compatibility) |
| `GET /v1/models/{model}` | Single model details. | [OpenAI compatibility](https://docs.ollama.com/api/openai-compatibility) |
| `POST /v1/embeddings` | Text embeddings. | [OpenAI compatibility](https://docs.ollama.com/api/openai-compatibility) |

An [Anthropic-compatible API](https://docs.ollama.com/api/anthropic-compatibility) is also provided.

## CLI commands

Reference: [CLI reference](https://docs.ollama.com/cli). Selected commands:

| Command | Purpose |
| ------- | ------- |
| `ollama run <model>` | Run a model in interactive chat; supports multimodal input and multiline strings. |
| `ollama launch [<target>]` | Configure and launch app integrations (Claude Code, Codex, Droid, OpenCode, VS Code, …); `--config` to configure only. |
| `ollama serve` | Start the Ollama server; `ollama serve --help` lists the `OLLAMA_*` environment variables it honors. |
| `ollama pull / pull --help` / `ollama push` | Download a model from / publish a model to a registry (push requires sign-in). |
| `ollama create -f Modelfile` | Create a custom model from a [Modelfile](https://docs.ollama.com/modelfile) (also via [importing GGUF/safetensors](https://docs.ollama.com/import)). |
| `ollama show <model>` | Show model parameters, quantization, and template. |
| `ollama ls` | List local models. |
| `ollama ps` | List models loaded in memory. |
| `ollama stop <model>` | Stop a running model. |
| `ollama rm <model>` | Remove a local model. |
| `ollama cp <src> <dst>` | Copy a model to a new name (e.g. aliasing for OpenAI default model names). |
| `ollama signin` / `ollama signout` | Manage the ollama.com account used for cloud models and registry push. |

## Client SDKs and registry

- Python: [ollama-python](https://github.com/ollama/ollama-python)
- JavaScript: [ollama-js](https://github.com/ollama/ollama-js)
- Go: [llama.cpp-based client in the main repo](https://github.com/ollama/ollama/tree/main/llama.cpp)
- Model registry: [ollama.com/library](https://ollama.com/library); [importing Hugging Face / llama.cpp models](https://docs.ollama.com/import)
