---
upstream: https://github.com/ggml-org/llama.cpp
last_updated: 2026-08-22
---

# llama.cpp — releases

llama.cpp publishes two tracks (per the [v0.2.0 release notes](https://github.com/ggml-org/llama.cpp/releases/tag/v0.2.0) and [docs/release.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/release.md)):

- `vX.Y.Z` — "stable", slower cadence, recommended for downstream distribution and casual users. Latest stable: **[v0.2.0](https://github.com/ggml-org/llama.cpp/releases/tag/v0.2.0)** (2026-08-21), which introduced consistent semantic versioning.
- `b[NUM]` — "nightly"/dev, published on almost every commit to `master`; latest functionality, but potentially less stable.

Below are the latest 10 releases overall, most recent first (nightly track).

## b10587 — 2026-08-22

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10587)

- Vulkan backend: new `PAD_REFLECT_1D` operation ([#26586](https://github.com/ggml-org/llama.cpp/pull/26586)).

## b10586 — 2026-08-22

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10586)

- `mtmd`: use `ggml_rope_set_offset` for position handling in multimodal pipelines ([#27521](https://github.com/ggml-org/llama.cpp/pull/27521)).

## b10585 — 2026-08-22

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10585)

- `common`: internal JSON abstraction layer (`common/json`) — `common`, `server`, and jinja template handling migrated off the vendored library ([#27511](https://github.com/ggml-org/llama.cpp/pull/27511)).

## b10584 — 2026-08-22

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10584)

- Model fitting now takes `n_streams` into account ([#27496](https://github.com/ggml-org/llama.cpp/pull/27496)).
- Speculative decoding fix: the draft context now follows the target context's size, fixing 500 errors on `llama-server` when the draft batch outgrows its allocated context.

## b10582 — 2026-08-22

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10582)

- CI: restored the Ubuntu ROCm job ([#27399](https://github.com/ggml-org/llama.cpp/pull/27399)).

## b10581 — 2026-08-22

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10581)

- Model support: DSpark for BailingMoe3 ([#27508](https://github.com/ggml-org/llama.cpp/pull/27508)).

## b10580 — 2026-08-22

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10580)

- `mtmd`: support for the dots3-note vision+audio model ([#27524](https://github.com/ggml-org/llama.cpp/pull/27524)).

## b10578 — 2026-08-22

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10578)

- `ggml`: optimized `concat` op by replacing per-element copies with row-level `memcpy` ([#24575](https://github.com/ggml-org/llama.cpp/pull/24575)).

## b10577 — 2026-08-22

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10577)

- `common`: fix draft-MTP (multi-token-prediction) speculative decoding when embeddings are requested ([#27400](https://github.com/ggml-org/llama.cpp/pull/27400)).

## b10576 — 2026-08-22

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10576)

- SYCL backend: Q2_K reordered MMVQ and ESIMD kernels re-landed after an earlier revert ([#27490](https://github.com/ggml-org/llama.cpp/pull/27490)).

> Note: nightly releases ship on near-daily (often multiple-per-day) cadence, so this 10-entry window can span a single day. For stable-upgrade decisions use the `vX.Y.Z` track above; the [full release history](https://github.com/ggml-org/llama.cpp/releases) and each release's attested build list are authoritative.
