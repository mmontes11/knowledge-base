---
upstream: https://github.com/ggml-org/llama.cpp
last_updated: 2026-08-22
---

# llama.cpp — features

Key feature areas, each linked to the upstream documentation covering it. The [Documentation section of the README](https://github.com/ggml-org/llama.cpp#documentation) and the [`docs/` tree](https://github.com/ggml-org/llama.cpp/tree/master/docs) are authoritative.

## Models and quantization

- **GGUF model format**: the project's model container; models are downloadable from Hugging Face and run directly (`llama cli -hf <org>/<model>-GGUF`). [docs/models.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/models.md)
- **Integer quantization**: 1.5-, 2-, 3-, 4-, 5-, 6-, and 8-bit quantization for faster inference and lower memory, applied with `llama-quantize` and quality-tuned with importance matrices (`llama-imatrix`). [README — Description](https://github.com/ggml-org/llama.cpp#description), [tools/quantize](https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/README.md)
- **Hugging Face conversion**: convert `hf.co` checkpoints to GGUF via the [conversion/ scripts](https://github.com/ggml-org/llama.cpp/tree/master/conversion) (e.g. `convert_hf_to_gguf.py`).

## Hardware backends

- **Multi-backend compute**: BLAS/BLIS, CUDA (NVIDIA), HIP (AMD), MUSA (Moore Threads), Metal (Apple Silicon), Vulkan, SYCL (Intel GPU), OpenCL (Adreno), OpenVINO (Intel CPU/GPU/NPU), CANN/Ascend NPU, WebGPU, ZenDNN, IBM zDNN, VirtGPU, RPC (remote CPU offload). [README — Supported backends](https://github.com/ggml-org/llama.cpp#supported-backends), [docs/build.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/build.md)
- **CPU architecture support**: AVX/AVX2/AVX512/AMX (x86), ARM NEON/Accelerate (Apple), RVV/ZVFH/ZFH/ZICBOP/ZIHINTPAUSE (RISC-V). [README — Description](https://github.com/ggml-org/llama.cpp#description)
- **CPU+GPU hybrid inference**: partially accelerate models larger than total VRAM by splitting layers across CPU and GPU. [README — Description](https://github.com/ggml-org/llama.cpp#description)
- **Multi-GPU**: tensor-split models across multiple GPUs. [docs/multi-gpu.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/multi-gpu.md)

## Serving and clients

- **`llama-server`**: OpenAI-compatible HTTP API server for chat completions, completions, etc. [tools/server/README.md](https://github.com/ggml-org/llama.cpp/blob/master/tools/server/README.md)
- **Built-in web UI**: served by `llama-server`, shipped as a release asset. [README — Quick start](https://github.com/ggml-org/llama.cpp#quick-start), [tools/ui/](https://github.com/ggml-org/llama.cpp/tree/master/tools/ui)
- **CLI chat (`llama-cli`)**: interactive terminal client, including VLM sessions. [tools/cli/README.md](https://github.com/ggml-org/llama.cpp/blob/master/tools/cli/README.md)
- **Docker deployment**: official images on `ghcr.io/ggml-org/llama.cpp` (CPU/CUDA/ROCm/Vulkan variants). [docs/docker.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/docker.md)
- **Mobile**: Android (AAR builds, [docs/android.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/android.md)) and iOS/macOS (XCFramework, [docs/xcframework.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/xcframework.md)).

## Inference features

- **Multimodal (mtmd)**: vision (and audio) inference via mmproj adapters, covering VLMs across backends. [docs/multimodal.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/multimodal.md), [tools/mtmd/README.md](https://github.com/ggml-org/llama.cpp/blob/master/tools/mtmd/README.md)
- **Speculative decoding**: draft-model and multi-token-prediction (MTP) acceleration of generation. [docs/speculative.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/speculative.md)
- **Function calling and structured output**: tool/function calling plus GBNF grammar-constrained generation. [docs/function-calling.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/function-calling.md), [grammars/](https://github.com/ggml-org/llama.cpp/blob/master/grammars/README.md)
- **Presets**: named bundles of generation parameters. [docs/preset.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/preset.md)
- **LoRA**: loading, merging, and exporting LoRA adapters. [tools/export-lora/](https://github.com/ggml-org/llama.cpp/tree/master/tools/export-lora)

## Performance and tooling

- **Benchmarking**: `llama-bench` for per-model throughput/latency numbers; performance troubleshooting guide. [tools/llama-bench/](https://github.com/ggml-org/llama.cpp/tree/master/tools/llama-bench), [docs/development/token_generation_performance_tips.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/development/token_generation_performance_tips.md)
- **Model fitting**: `llama-fit-params` suggests the largest `n_ctx`/parallelism that fits on the available devices. [tools/fit-params/](https://github.com/ggml-org/llama.cpp/tree/master/tools/fit-params)
- **Release process and attestations**: `vX.Y.Z` stable vs `b[NUM]` nightly tracks, signed release attestations. [docs/release.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/release.md), [releases](https://github.com/ggml-org/llama.cpp/releases)
