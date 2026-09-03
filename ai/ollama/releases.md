---
upstream: https://github.com/ollama/ollama
last_updated: 2026-09-03
---

# ollama — releases

Latest 10 stable releases, newest first (prereleases such as `v0.33.3-rc2` are excluded). Check the ⚠️ entries before upgrading.

## v0.33.2 — 2026-08-27

[Release page](https://github.com/ollama/ollama/releases/tag/v0.33.2)

- Desktop app follows the system appearance again (dark mode restored).
- macOS app hands off to an already-running instance instead of starting a second one.
- The Claude Desktop proxy no longer interrupts in-flight requests when the model catalog updates.

## v0.33.1 — 2026-08-26

[Release page](https://github.com/ollama/ollama/releases/tag/v0.33.1)

- MLX: Qwen3.8 Flash Next support.
- mlxrunner: structured output support; avoids Metal GPU timeouts when loading models from slow storage.
- MLX and llama.cpp dependency updates.

## v0.33.0 — 2026-08-21

[Release page](https://github.com/ollama/ollama/releases/tag/v0.33.0)

- Claude Desktop can use Ollama as a third-party gateway provider.
- Improved prefill caching: cancelled prefills keep their restore points, so retries resume instead of restarting.
- DeepSeek Harness launcher falls back to `npx` when the global npm install fails (Windows command-shim support).
- ⚠️ **Fixed broken default packaging** caused by macOS-specific assumptions affecting Linux/Windows builds — update if you build or package from source.

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


