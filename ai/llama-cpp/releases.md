---
upstream: https://github.com/ggml-org/llama.cpp
last_updated: 2026-10-01
---

# llama.cpp — releases

llama.cpp publishes two tracks (per the [v0.2.0 release notes](https://github.com/ggml-org/llama.cpp/releases/tag/v0.2.0) and [docs/release.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/release.md)):

- `vX.Y.Z` — "stable", slower cadence, recommended for downstream distribution and casual users. Latest stable: **[v0.5.0](https://github.com/ggml-org/llama.cpp/releases/tag/v0.5.0)** (2026-09-23) — HRM-Text/DFM Mimir 1B ([#27625](https://github.com/ggml-org/llama.cpp/pull/27625)), MiMo-V2.6 ([#29257](https://github.com/ggml-org/llama.cpp/pull/29257)) and HunyuanOCR (DFlash) ([#28890](https://github.com/ggml-org/llama.cpp/pull/28890)) model support, multi-address `--host` binding and image outputs from function calls on `llama-server`, and CUDA implicit-GEMM `conv2d` ([#29135](https://github.com/ggml-org/llama.cpp/pull/29135)); ggml is bumped to [v0.25.0](https://github.com/ggml-org/ggml/releases/tag/v0.25.0) (hyper-connection, flash-attention, and fused MoE/SSM support across backends; RPC protocol major v7).
- `b[NUM]` — "nightly"/dev, published on almost every commit to `master`; latest functionality, but potentially less stable.

Below are the latest 10 releases overall, most recent first (nightly track).

## b11318 — 2026-10-01

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b11318)

- `vocab`: honor BOS/EOS settings from `tokenizer_config.json` for PLaMo-2 and PLaMo-3 ([#29734](https://github.com/ggml-org/llama.cpp/pull/29734)).

## b11317 — 2026-10-01

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b11317)

- `llama-bench`: fix verbosity filter to show `GGML_LOG_ERROR` ([#28229](https://github.com/ggml-org/llama.cpp/pull/28229)).

## b11316 — 2026-10-01

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b11316)

- `metal`: use bf16 math for mxfp4 mul-mat ([#29770](https://github.com/ggml-org/llama.cpp/pull/29770)).

## b11313 — 2026-10-01

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b11313)

- `model`: re-enable `-sm tensor` for qwen4exp by expanding `hc_init` right after it is built, so the PLE `REPEAT` is assigned to the device ([#28569](https://github.com/ggml-org/llama.cpp/pull/28569)).

## b11312 — 2026-10-01

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b11312)

- `webgpu`: fix SSM_SCAN binding aliasing ([#29750](https://github.com/ggml-org/llama.cpp/pull/29750)).

## b11311 — 2026-10-01

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b11311)

- `ggml-opencl`: replace `alloca()` with `std::vector` ([#29765](https://github.com/ggml-org/llama.cpp/pull/29765)).

## b11310 — 2026-10-01

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b11310)

- `cuda`: guard the iq4_nl dequantize row kernel against short rows ([#29683](https://github.com/ggml-org/llama.cpp/pull/29683)).

## b11309 — 2026-10-01

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b11309)

- `hexagon`: optimize ALLREDUCE and add support for safe scatter mode ([#29757](https://github.com/ggml-org/llama.cpp/pull/29757)).

## b11308 — 2026-10-01

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b11308)

- `args`: fix the cli download mmproj argument ([#28977](https://github.com/ggml-org/llama.cpp/pull/28977)).

## b11307 — 2026-10-01

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b11307)

- `llama`: preserve the original batch order for speculative-decoding layer inputs, compatible with tensor split ([#29019](https://github.com/ggml-org/llama.cpp/pull/29019)).

> Note: nightly releases ship on near-daily (often multiple-per-day) cadence, so this 10-entry window can span a single day. For stable-upgrade decisions use the `vX.Y.Z` track above; the [full release history](https://github.com/ggml-org/llama.cpp/releases) and each release's attested build list are authoritative.
