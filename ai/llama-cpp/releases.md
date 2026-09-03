---
upstream: https://github.com/ggml-org/llama.cpp
last_updated: 2026-09-03
---

# llama.cpp — releases

llama.cpp publishes two tracks (per the [v0.2.0 release notes](https://github.com/ggml-org/llama.cpp/releases/tag/v0.2.0) and [docs/release.md](https://github.com/ggml-org/llama.cpp/blob/master/docs/release.md)):

- `vX.Y.Z` — "stable", slower cadence, recommended for downstream distribution and casual users. Latest stable: **[v0.3.0](https://github.com/ggml-org/llama.cpp/releases/tag/v0.3.0)** (2026-08-25) — adds the dots3-note multimodal model, MTP for GLM-4.5-Air, and a new experimental `-sm tensor` split mode (e.g. for DeepSeek 4); ggml is bumped to [v0.22.0](https://github.com/ggml-org/ggml/releases/tag/v0.22.0); ⚠️ removes the `-no-cnv` CLI option ([#27542](https://github.com/ggml-org/llama.cpp/pull/27542)).
- `b[NUM]` — "nightly"/dev, published on almost every commit to `master`; latest functionality, but potentially less stable.

Below are the latest 10 releases overall, most recent first (nightly track).

## b10775 — 2026-09-03

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10775)

- `mtmd`: fix idefics3 preprocessing ([#28273](https://github.com/ggml-org/llama.cpp/pull/28273)).

## b10774 — 2026-09-03

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10774)

- `finetune`: fix training with no KV cache ([#27199](https://github.com/ggml-org/llama.cpp/pull/27199)).

## b10773 — 2026-09-03

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10773)

- `server`: accept `data:` URLs for `input_video` and `input_audio` ([#27735](https://github.com/ggml-org/llama.cpp/pull/27735)).

## b10772 — 2026-09-03

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10772)

- `ggml-hexagon`: F16 support for unary ops on the HTP backend — adds `ABS` to the existing `NORM`/`RMS_NORM`/`L2_NORM`/`SCALE`/`CLAMP`/`SQR`/`SQRT` set ([#28228](https://github.com/ggml-org/llama.cpp/pull/28228)).

## b10771 — 2026-09-03

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10771)

- `mtmd`: new `mtmd_tokenize_from_parts()` API, adopted by `mtmd-cli` ([#28250](https://github.com/ggml-org/llama.cpp/pull/28250)).

## b10770 — 2026-09-03

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10770)

- Metal: flash-attention `fa-vec` tunings for M3 ([#28236](https://github.com/ggml-org/llama.cpp/pull/28236)).

## b10769 — 2026-09-02

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10769)

- Metal: fix memory query under low-memory conditions ([#27701](https://github.com/ggml-org/llama.cpp/pull/27701)).

## b10767 — 2026-09-02

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10767)

- ROCm: update to the 10.0.0 release ([#27803](https://github.com/ggml-org/llama.cpp/pull/27803)).

## b10766 — 2026-09-02

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10766)

- Model: correct input-vision support for DeepSeek 4 ([#28154](https://github.com/ggml-org/llama.cpp/pull/28154)).

## b10764 — 2026-09-02

[Release page](https://github.com/ggml-org/llama.cpp/releases/tag/b10764)

- `ggml-cuda`: remove unused variables (cleanup) ([#28235](https://github.com/ggml-org/llama.cpp/pull/28235)).

> Note: nightly releases ship on near-daily (often multiple-per-day) cadence, so this 10-entry window can span a single day. For stable-upgrade decisions use the `vX.Y.Z` track above; the [full release history](https://github.com/ggml-org/llama.cpp/releases) and each release's attested build list are authoritative.
