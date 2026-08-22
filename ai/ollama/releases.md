---
upstream: https://github.com/ollama/ollama
last_updated: 2026-08-22
---

# ollama — releases

Latest 10 stable releases, newest first (prereleases such as `v0.33.0-rc1`/`v0.33.0-rc2` are excluded). Check the ⚠️ entries before upgrading.

## v0.32.15 — 2026-08-19

[Release page](https://github.com/ollama/ollama/releases/tag/v0.32.15)

- Fixes model loading on Apple silicon.

## v0.32.14 — 2026-08-15

[Release page](https://github.com/ollama/ollama/releases/tag/v0.32.14)

- Fixes GPU loading with large models and a context-length mismatch warning.

## v0.32.13 — 2026-08-14

[Release page](https://github.com/ollama/ollama/releases/tag/v0.32.13)

- Fixes a crash in the new engine and improves tool-call validation in the OpenAI-compatible API.

## v0.32.12 — 2026-08-14

[Release page](https://github.com/ollama/ollama/releases/tag/v0.32.12)

- Fixes macOS installer code signing, model unloading for cloud models, the Windows installer, and token usage for embeddings.

## v0.32.11 — 2026-08-14

[Release page](https://github.com/ollama/ollama/releases/tag/v0.32.11)

- OpenAI-compatible Responses API now supports web search.
- Fixes cloud-model streaming in the new engine and image generation in OpenCode; adds a Windows system tray app (beta).

## v0.32.9 — 2026-08-11

[Release page](https://github.com/ollama/ollama/releases/tag/v0.32.9)

- Fixes MLX tool and structured-output calls, the MLX OpenAI-compatible Responses API, and macOS build scripts.

## v0.32.8 — 2026-08-10

[Release page](https://github.com/ollama/ollama/releases/tag/v0.32.8)

- Fixes MLX structured-output calls on vision models.

## v0.32.7 — 2026-08-10

[Release page](https://github.com/ollama/ollama/releases/tag/v0.32.7)

- Fixes MLX structured-output calls.

## v0.32.6 — 2026-08-04

[Release page](https://github.com/ollama/ollama/releases/tag/v0.32.6)

- Aligns the OpenAI wire format with the OpenAI API.
- ⚠️ **Removal (temporary)**: image generation was pulled due to a regression — use v0.32.5 if you need it.

## v0.32.5 — 2026-07-27

[Release page](https://github.com/ollama/ollama/releases/tag/v0.32.5)

- **New engine (beta)**: a faster, lower-memory-usage inference engine for Linux, macOS, and Windows.
- **Experimental image generation** in the CLI, the new engine, and the API.
- ⚠️ **Default change on macOS**: [MLX](https://github.com/ml-explore/mlx) is now the default engine; set `OLLAMA_NEW_ENGINE_DISABLE_MLX=1` to revert.
