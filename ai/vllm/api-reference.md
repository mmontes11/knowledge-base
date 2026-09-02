---
upstream: https://github.com/vllm-project/vllm
last_updated: 2026-08-23
---

# vllm — API reference

vLLM exposes three main surfaces: an HTTP server (OpenAI-compatible, Anthropic Messages API, and gRPC), a Python offline-inference API, and the `vllm` CLI. **Link, don't copy**: endpoint parameters and full schemas live in the canonical docs below.

## HTTP server (`vllm serve`)

The canonical endpoint catalog is the [OpenAI-Compatible Server](https://docs.vllm.ai/en/latest/serving/online_serving/openai_compatible_server) page; the [Online serving](https://docs.vllm.ai/en/latest/serving/online_serving) page covers launch options, health/metrics, and pause/resume. Main endpoints:

| Endpoint | Purpose | Upstream docs |
| -------- | ------- | ------------- |
| `POST /v1/chat/completions` | Chat completions (streaming, tools, reasoning, multimodal inputs). | [OpenAI-Compatible Server](https://docs.vllm.ai/en/latest/serving/online_serving/openai_compatible_server) |
| `POST /v1/chat/completions/batch` | Bounded batch of chat completion requests. | [OpenAI-Compatible Server](https://docs.vllm.ai/en/latest/serving/online_serving/openai_compatible_server) |
| `POST /v1/completions` | Legacy text completions. | [OpenAI-Compatible Server](https://docs.vllm.ai/en/latest/serving/online_serving/openai_compatible_server) |
| `POST /v1/responses` | OpenAI Responses-style API (incl. render helpers). | [OpenAI-Compatible Server](https://docs.vllm.ai/en/latest/serving/online_serving/openai_compatible_server) |
| `POST /v1/embeddings` | Embeddings/retrieval models (E5, GTE, …). | [OpenAI-Compatible Server](https://docs.vllm.ai/en/latest/serving/online_serving/openai_compatible_server) |
| `POST /v1/audio/transcriptions` / `POST /v1/audio/translations` | Speech-to-text models (MOSS-Transcribe, …). | [Speech to text](https://docs.vllm.ai/en/latest/serving/online_serving/speech_to_text) |
| `GET /health`, `GET /metrics` | Liveness/readiness + Prometheus metrics (exposed for ServiceMonitors in this stack). | [Online serving](https://docs.vllm.ai/en/latest/serving/online_serving) |
| `GET /version`, `GET /server_info`, `POST /pause` / `POST /resume` | Engine introspection and runtime pause/resume of serving. | [Online serving](https://docs.vllm.ai/en/latest/serving/online_serving) |

Additional API families, with their generated Python entrypoint references:

| API | Purpose | Upstream docs |
| --- | ------- | ------------- |
| Anthropic Messages API (`/v1/messages`) | Anthropic-compatible chat endpoint. | [anthropic entrypoints](https://docs.vllm.ai/en/latest/api/vllm/entrypoints/anthropic/serving) |
| gRPC server | gRPC transport of the vLLM engine (see also the Rust frontend control plane, v0.22+). | [grpc_server](https://docs.vllm.ai/en/latest/api/vllm/entrypoints/grpc_server) |

## Python API (offline inference)

Generated reference index: [`vllm` package API](https://docs.vllm.ai/en/latest/api/vllm/). Usage walkthrough: [Offline inference](https://docs.vllm.ai/en/latest/serving/offline_inference).

| Surface | Purpose | Upstream docs |
| ------- | ------- | ------------- |
| `vllm.LLM` | Batch offline inference (`.generate()`, chat, embeddings/pooling). | [Offline inference](https://docs.vllm.ai/en/latest/serving/offline_inference) · [API](https://docs.vllm.ai/en/latest/api/vllm/) |
| `vllm.AsyncLLMEngine` / `AsyncLLM` | Async request submission/streaming for embedding vLLM in your own service. | [Offline inference](https://docs.vllm.ai/en/latest/serving/offline_inference) · [API](https://docs.vllm.ai/en/latest/api/vllm/engine/) |
| `vllm.SamplingParams`, `vllm.PoolingParams` | Decode/beam-search/embedding parameters passed to generate calls. | [API](https://docs.vllm.ai/en/latest/api/vllm/sampling_params/) |
| Structured outputs (xgrammar / guidance) | Constrained JSON-schema/regex decoding from Python. | [Structured outputs](https://docs.vllm.ai/en/latest/features/structured_outputs) |

## CLI

Registered subcommands live in [`vllm/entrypoints/cli/`](https://github.com/vllm-project/vllm/tree/main/vllm/entrypoints/cli).

| Command | Purpose | Upstream docs |
| ------- | ------- | ------------- |
| `vllm serve` | Launch the OpenAI-compatible HTTP server. | [Online serving](https://docs.vllm.ai/en/latest/serving/online_serving) |
| `vllm bench serve` / `latency` / `throughput` / `sweep` / `startup` / `mm_processor` | Benchmark and parameter sweeps (`vllm-bench` port to Rust, [API](https://docs.vllm.ai/en/latest/api/vllm/benchmarks/)). | [Benchmarks API](https://docs.vllm.ai/en/latest/api/vllm/benchmarks/) |
| `vllm launch <component>` | Docker-based launcher for vLLM components. | [CLI source](https://github.com/vllm-project/vllm/blob/main/vllm/entrypoints/cli/launch.py) |
| `vllm run-batch` | Batch offline inference over prompt files (pooling/chat). | [CLI source](https://github.com/vllm-project/vllm/blob/main/vllm/entrypoints/cli/run_batch.py) |
| `vllm collect-env` | Print environment/driver diagnostics for issue reports. | [CLI source](https://github.com/vllm-project/vllm/blob/main/vllm/entrypoints/cli/collect_env.py) |
