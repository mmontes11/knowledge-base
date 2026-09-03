---
upstream: https://github.com/anomalyco/opencode
last_updated: 2026-09-03
---

# opencode — releases

Latest 10 official releases, newest first. OpenCode ships small, frequent patch releases (roughly every few days); note the API-generation ("v1" vs "v2") entries before upgrading an embedded or remote setup.

## v1.18.27 — 2026-09-02

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.27)

- Provider header and streamed-chunk timeouts now default to five minutes (slower model startups fail less often); the streamed-chunk timeout can be disabled with `false`.
- Anthropic thinking-block binding is limited to Claude 5.1+ models and can be opted out of via config, so older deployments no longer reject requests.
- Unhandled errors when cancelling timed-out SSE reads are avoided.

## v1.18.26 — 2026-09-01

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.26)

- Claude 5 sessions tolerate stale thinking blocks instead of failing after prompt or tool changes.
- Azure CLI sign-in now asks for the resource name directly instead of querying Azure management APIs; Bedrock GPT-5.6 accepts `none` reasoning effort and Bedrock reasoning/replay handling is more reliable.
- Desktop: session renames save reliably from the title editor and tab context menu.

## v1.18.25 — 2026-08-28

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.25)

- Azure CLI sign-in fixed to work without requiring Bun (follow-up to the Azure CLI auth path added in v1.18.24).

## v1.18.24 — 2026-08-28

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.24)

- The Azure provider can now sign in with Microsoft Entra ID through the Azure CLI instead of requiring an API key.
- V1 now reads supported V2 config fields so newer config files keep working in more mixed setups.
- Bedrock reasoning responses no longer get cached into unreplayable empty messages.
- Desktop: archived sessions disappear from the Home list immediately.

## v1.18.23 — 2026-08-25

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.23)

- Cloudflare AI Gateway now routes third-party (non-Workers) models through the REST API, and converts Anthropic dotted model IDs (e.g. `claude-haiku-4.5`) to the dashed slug the provider expects.
- Parent session IDs no longer sent in request headers for session-aware providers.
- TUI: GitHub auth fixed for immutable OIDC subject tokens.

## v1.18.22 — 2026-08-24

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.22)

- OpenCode device-login links fixed when servers return relative verification URLs or use a base path; outdated OpenCode Go first-month discount messaging and pricing removed.
- Amazon Bedrock provider updated for compatibility fixes; `textVerbosity` no longer sent to OpenAI-compatible providers that do not support it.
- Desktop: model provider headers stay visible while scrolling the model picker.

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
