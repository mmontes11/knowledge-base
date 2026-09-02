---
upstream: https://github.com/ggml-org/llama.cpp
last_updated: 2026-08-22
---

# llama.cpp — API reference

llama.cpp is a non-Kubernetes project: its API surface is the set of CLI tools, the HTTP endpoints of `llama-server`, and the client libraries (C/C++ and Python). The [Documentation section of the README](https://github.com/ggml-org/llama.cpp#documentation) is the index of the canonical per-tool docs; this table links, it does not copy.

| Surface | Purpose | Upstream docs |
| ------- | ------- | ------------- |
| `llama-cli` | Interactive chat REPL over GGUF models (text and VLM), function calling, grammar-constrained output. | [tools/cli/README.md](https://github.com/ggml-org/llama.cpp/blob/master/tools/cli/README.md) |
| `llama-server` | OpenAI-compatible HTTP API server (`/v1/chat/completions`, `/v1/completions`, `/v1/models`, audio endpoints) with a built-in web UI; the primary serving surface. | [tools/server/README.md](https://github.com/ggml-org/llama.cpp/blob/master/tools/server/README.md) |
| `llama-completion` | One-shot text completion (non-interactive). | [tools/completion/README.md](https://github.com/ggml-org/llama.cpp/blob/master/tools/completion/README.md), [docs/completions.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/completions.md) |
| `llama-bench` | Throughput and latency benchmarks for models on the configured backends. | [tools/llama-bench/](https://github.com/ggml-org/llama.cpp/tree/master/tools/llama-bench) |
| `llama-quantize` | Quantize/re-quantize GGUF models across the 1.5–8-bit integer types. | [tools/quantize/README.md](https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/README.md) |
| `llama-imatrix` | Compute importance matrices used for quantization that preserves more quality at low bit widths. | [tools/imatrix/](https://github.com/ggml-org/llama.cpp/tree/master/tools/imatrix) |
| `llama-mtmd-cli` / `mtmd` | Multimodal (vision and audio) inference over GGUF models with mmproj adapters. | [tools/mtmd/README.md](https://github.com/ggml-org/llama.cpp/blob/master/tools/mtmd/README.md), [docs/multimodal.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/multimodal.md) |
| `llama-perplexity` | Evaluate model perplexity on a text corpus. | [tools/perplexity/](https://github.com/ggml-org/llama.cpp/tree/master/tools/perplexity) |
| `llama-tts` | Text-to-speech using TTS-capable GGUF models. | [tools/tts/](https://github.com/ggml-org/llama.cpp/tree/master/tools/tts) |
| `llama-parser` | Autoregressive parser for structured output (GBNF grammars and similar). | [tools/parser/](https://github.com/ggml-org/llama.cpp/tree/master/tools/parser), [docs/autoparser.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/autoparser.md) |
| `llama-export-lora` | Extract/merge LoRA adapters from GGUF models. | [tools/export-lora/](https://github.com/ggml-org/llama.cpp/tree/master/tools/export-lora) |
| C/C++ library (`llama.h`) | The "lib llama" inference API for embedding llama.cpp in your own program (model loading, context, sampling, backends). | [include/llama.h](https://github.com/ggml-org/llama.cpp/blob/master/include/llama.h), API status: [issue #9289](https://github.com/ggml-org/llama.cpp/issues/9289) |
| `ggml` library | The tensor/compute library underneath llama.cpp (backends, ops), a subrepo of the [ggml-org](https://github.com/ggml-org) organization. | [ggml-org/ggml](https://github.com/ggml-org/ggml), ops reference: [docs/ops.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/ops.md) |
| Python (`gguf-py`) | Official Python bindings for the GGUF container format (read/write GGUF headers and tensors). | [gguf-py/](https://github.com/ggml-org/llama.cpp/tree/master/gguf-py) |
| iOS / Android | Prebuilt XCFramework for iOS and AAR for Android from release assets. | [docs/xcframework.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/xcframework.md), [docs/android.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/android.md) |
| Docker images | Prebuilt images on `ghcr.io/ggml-org/llama.cpp` (CPU, CUDA, ROCm, Vulkan variants). | [docs/docker.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/docker.md) |

Notes:

- The OpenAI-compatibility scope of `llama-server` is tracked upstream in [issue #9291](https://github.com/ggml-org/llama.cpp/issues/9291); treat the per-endpoint behavior there as the source of truth and re-check before depending on an endpoint.
- The CLI tool set above is not exhaustive — `tools/` additionally contains [rpc](https://github.com/ggml-org/llama.cpp/tree/master/tools/rpc), [fit-params](https://github.com/ggml-org/llama.cpp/tree/master/tools/fit-params), [gguf-split](https://github.com/ggml-org/llama.cpp/tree/master/tools/gguf-split), [tokenize](https://github.com/ggml-org/llama.cpp/tree/master/tools/tokenize), and [cvector-generator](https://github.com/ggml-org/llama.cpp/tree/master/tools/cvector-generator). Follow the [tools/ tree](https://github.com/ggml-org/llama.cpp/tree/master/tools) for the full list.
