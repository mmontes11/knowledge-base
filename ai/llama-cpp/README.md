---
upstream: https://github.com/ggml-org/llama.cpp
last_updated: 2026-08-22
---

# llama.cpp

Plain C/C++ implementation of LLM (and VLM) inference with minimal setup: no external dependencies, built on the [ggml](https://github.com/ggml-org/ggml) tensor library, distributed as GGUF models with 1.5-, 2-, 3-, 4-, 5-, 6-, and 8-bit integer quantization, and runnable on a wide range of hardware — Apple Silicon (NEON/Accelerate/Metal), x86 (AVX/AVX2/AVX512/AMX), RISC-V (RVV), NVIDIA/AMD/Moore Threads GPUs (CUDA/HIP/MUSA), Vulkan, SYCL, OpenCL, OpenVINO — including CPU+GPU hybrid inference for models larger than total VRAM. The mainline goal is local inference with minimal setup and state-of-the-art performance, per the [project description in the README](https://github.com/ggml-org/llama.cpp#description).

- Upstream repository: https://github.com/ggml-org/llama.cpp
- Website / install / models / releases: https://llama.app
- License: MIT
- Release tracks: `vX.Y.Z` "stable" releases (consistent semantic versioning since [v0.2.0](https://github.com/ggml-org/llama.cpp/releases/tag/v0.2.0)) and `b[NUM]` "nightly" builds published on almost every commit to `master` — see [docs/release.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/release.md).

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
