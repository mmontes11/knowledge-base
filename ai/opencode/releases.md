---
upstream: https://github.com/anomalyco/opencode
last_updated: 2026-10-01
---

# opencode — releases

Latest 10 official releases, newest first. OpenCode ships small, frequent patch releases (roughly every few days); note the API-generation ("v1" vs "v2") entries before upgrading an embedded or remote setup.

## v1.18.34 — 2026-09-30

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.34)

- Model requests now send namespaced session and parent-session identity headers.
- macOS: locally compiled binaries are re-signed so they run reliably on macOS 27+, and release CLI binaries are signed with a Developer ID.

## v1.18.33 — 2026-09-28

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.33)

- Cloudflare AI Gateway models now honor provider response and stream timeouts.
- Debug configuration output now redacts credentials and sensitive headers, and MCP browser launch failures are reported when the launcher exits immediately.
- Gemini thinking defaults and effort options now match the supported controls across model generations.

## v1.18.32 — 2026-09-21

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.32)

- Bedrock image attachments are only hoisted for Claude, Nova, and Llama 4 models.
- Together AI streaming usage reporting fixed.

## v1.18.31 — 2026-09-14

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.31)

- ACP session model, effort, mode, and reasoning chunk boundaries restored when loading, resuming, or forking sessions.
- TUI: remote config authentication errors are shown at startup and the app exits with a failure status.
- GitHub Copilot models now request summarized adaptive thinking.

## v1.18.30 — 2026-09-09

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.30)

- Astra system prompt added for GPT-6 models.
- Bedrock DeepSeek model IDs (including ARN-based IDs) are preserved so they resolve correctly, and reasoning-effort variants were added for supported GitLab GPT and Claude models.
- Azure and OpenAI provider SDKs updated for compatibility fixes.

## v1.18.29 — 2026-09-04

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.29)

- Codex OAuth model filtering now recognizes integer GPT versions like `gpt-6`, fixing `gpt-6-astra` not showing up for OpenAI subscription users.
- Console: quota reset support action added.

## v1.18.28 — 2026-09-04

[Release page](https://github.com/anomalyco/opencode/releases/tag/v1.18.28)

- The session ID is now sent as GitHub Copilot's interaction header to improve request tracking across a session.
- Desktop: uses the desktop client ID during OpenCode account device authentication, and the open-in app icon is larger for better visibility.

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
