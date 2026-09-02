---
upstream: https://github.com/anomalyco/opencode
last_updated: 2026-08-23
---

# opencode — releases

Latest 10 official releases, newest first. OpenCode ships small, frequent patch releases (roughly every few days); note the API-generation ("v1" vs "v2") entries before upgrading an embedded or remote setup.

## v1.18.21 — 2026-08-21

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.21)

- Continue responses when a model reports an unknown finish reason instead of stopping early; Vertex AI `eu`/`us` multi-region Gemini requests routed through REP endpoints.
- Desktop: file search results stay visible while the next search loads; archive session command registered in both layouts.

## v1.18.20 — 2026-08-21

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.20)

- Failed subagent tool calls are surfaced with a resumable `task_id` instead of returning empty results.
- Wider network-error retry coverage (`finish_reason: network_error` variants, xAI capacity/unavailability); Cerebras `max_completion_tokens` preserved.
- Permission requests triggered by subagents during `opencode run` are now answered interactively.

## v1.18.19 — 2026-08-20

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.19)

- Native OpenAI and Anthropic passthroughs for Cloudflare AI Gateway models; Codex rate limits aligned to ChatGPT subscription limits.
- Built-in Qwen sampling defaults removed (they could send unsupported settings); malformed model pricing no longer breaks usage cost calculation; web search enabled for the OpenCode Go provider.
- Compatibility with existing v1 databases preserved while the v2 surface ships.

## v1.18.18 — 2026-08-13

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.18)

- Kimi system prompt selected correctly for official Moonshot and Kimi providers; `xhigh` reasoning effort fixed for xAI models.
- *Note: this is the version deployed in this stack ([k8s-ai apps/opencode](https://github.com/mmontes11/k8s-ai) image tag `v1.18.18`).*

## v1.18.17 — 2026-08-12

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.17)

- Session compaction keeps complete recent turns and writes clearer summaries for smaller models.
- Automatic session retries capped with jitter (fewer retry storms); MERGE Gateway reasoning variants; PDF attachments enabled for GitHub Copilot models with PDF vision; DeepSeek V4 Flash sampling defaults; Muse family models routed to the Meta system prompt.

## v1.18.16 — 2026-08-10

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.16)

- Unknown top-level config fields are ignored instead of failing config parsing; projects opened from Home are registered with the rest of the app.
- Desktop: right-click opens the project menu; local-directory fallback when the project picker server lacks search; macOS app keeps running after the last window closes.

## v1.18.15 — 2026-08-07

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.15)

- Chronological message ordering fixed for imported/legacy IDs; revert and fork now use real message chronology; repeated compaction keeps earlier tool-call history.
- Blob-based attachments load correctly in the web UI; desktop gains much broader locale coverage and full session transcript export to JSON from the UI.

## v1.18.14 — 2026-08-05

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.14)

- xAI login simplified to a single device-code flow (headless/remote-friendly).
- Structured mid-stream provider errors preserved so providers can retry; more transient provider/network errors retried; ACP usage totals now count cache writes; remote-workspace proxying fixed (host `directory` no longer forwarded; upstream 5xx bodies logged).

## v1.18.13 — 2026-08-04

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.13)

- TUI: GitHub pull request reviews include the PR number and URL in context.
- Desktop: early right-to-left layout support, locale-aware plural rules, expanded translations; markdown parsing moved off the main thread; untitled sessions fall back to generated names.

## v1.18.12 — 2026-08-04

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.12)

- Azure GPT-5.5+ completion requests with reasoning enabled fixed.
- ⚠️ **Behavior**: legacy config reads are skipped against v2 servers (clients must use the v2 config surface); desktop composer lag from large pasted images reduced; project search covers all known recent projects, not just the first five.
