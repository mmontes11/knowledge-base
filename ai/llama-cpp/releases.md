---
upstream: https://github.com/ggml-org/llama.cpp
last_updated: 2026-09-07
---

# llama.cpp — releases

llama.cpp publishes two tracks (per the [v0.2.0 release notes](https://github.com/ggml-org/llama.cpp/releases/tag/v0.2.0) and [docs/release.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/release.md)):

- `vX.Y.Z` — "stable", slower cadence, recommended for downstream distribution and casual users. Latest stable: **[v0.4.0](https://github.com/ggml-org/llama.cpp/releases/tag/v0.4.0)** (2026-09-04) — initial Qwen3.8-Flash-Next (`qwen4exp`) and NVIDIA Nemotron-3-Puzzle-75B-A9B model support, on-demand lazy tensor reading, per-slot server context limits, and `--video-*` input options; ggml is bumped to [v0.23.0](https://github.com/ggml-org/ggml/releases/tag/v0.23.0) (sparse flash attention, Apple RDMA RPC transport); `preserve_reasoning` is now enabled by default on `llama-server` ([#28174](https://github.com/ggml-org/llama.cpp/pull/28174)).
- `b[NUM]` — "nightly"/dev, published on almost every commit to `master`; latest functionality, but potentially less stable.

Below are the latest 10 releases overall, most recent first (nightly track).

## b10835 — 2026-09-07

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10835)

- `ggml-cuda`: fix divergent barrier in f16 flash attention ([#27870](https://github.com/ggml-org/llama.cpp/pull/27870)).

## b10834 — 2026-09-07

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10834)

- `ggml`: allow backend inputs to not create another split ([#28387](https://github.com/ggml-org/llama.cpp/pull/28387)).

## b10833 — 2026-09-07

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10833)

- `vulkan`: rms_norm fusion opportunities — RMS_NORM+MUL+ADD(+MUL) and RMS_NORM+VIEW+SET_ROWS, and ROPE+VIEW+SET_ROWS extended to IMROPE ([#28024](https://github.com/ggml-org/llama.cpp/pull/28024)).

## b10831 — 2026-09-07

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10831)

- `vulkan`: TQ1_0 support for mm, mat-vec, mat-vec-id, dequant, and get_rows; Metal now declines TQ1_0 for GET_ROWS and matrix-multiply, falling back to CPU ([#27765](https://github.com/ggml-org/llama.cpp/pull/27765)).

## b10830 — 2026-09-06

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10830)

- `convert`: new `--fuse-qkv` flag to fuse Q/K/V into QKV during HF-to-GGUF conversion ([#22780](https://github.com/ggml-org/llama.cpp/pull/22780)).

## b10829 — 2026-09-06

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10829)

- Model: fix GDN (gated delta net) q/k normalization from `max` to `rsqrt`, aligning with the flash-linear-attention reference ([#28068](https://github.com/ggml-org/llama.cpp/pull/28068)).

## b10828 — 2026-09-06

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10828)

- Model: add Spark2.5 (`Spark2_5ForCausalLM`) support ([#27868](https://github.com/ggml-org/llama.cpp/pull/27868)).

## b10827 — 2026-09-06

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10827)

- `opencl`: properly choose the weights pack for q4_K/q5_K mul_mat ([#28402](https://github.com/ggml-org/llama.cpp/pull/28402)).

## b10826 — 2026-09-06

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10826)

- `cuda`: fix races in mmid and mmf ([#28475](https://github.com/ggml-org/llama.cpp/pull/28475)).

## b10825 — 2026-09-06

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10825)

- `grammar`: fix max repetition threshold ([#28469](https://github.com/ggml-org/llama.cpp/pull/28469)).

> Note: nightly releases ship on near-daily (often multiple-per-day) cadence, so this 10-entry window can span a single day. For stable-upgrade decisions use the `vX.Y.Z` track above; the [full release history](https://github.com/ggml-org/llama.cpp/releases) and each release's attested build list are authoritative.
