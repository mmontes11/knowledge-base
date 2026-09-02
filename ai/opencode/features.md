---
upstream: https://github.com/anomalyco/opencode
last_updated: 2026-08-23
---

# opencode — features

Significant feature areas, each grounded in upstream source or release notes. The [documentation site](https://opencode.ai/docs) is authoritative for user-facing detail; source links below point into the monorepo.

## Agents

Two built-in agents switchable with `Tab`: **build** (default, full-access) and **plan** (read-only analysis, denies file edits and asks permission before running bash), plus a **general** subagent for complex searches and multistep tasks (`@general`). Custom agents are configurable and managed via `opencode agent` and the `/agent` endpoints.

- [Agents section in README](https://github.com/anomalyco/opencode#agents); [opencode.ai/docs/agents](https://opencode.ai/docs/agents)
- Source: [packages/opencode/src/agent](https://github.com/anomalyco/opencode/tree/dev/packages/opencode/src/agent)

## Models and providers

Broad multi-provider support: OpenAI, Anthropic, Google/Vertex (including `eu`/`us` multi-region Gemini routing, [v1.18.21](https://github.com/anomalyco/opencode/releases/tag/v1.18.21)), OpenAI Codex (with ChatGPT subscription rate limits and workspace compute residency, [v1.18.19](https://github.com/anomalyco/opencode/releases/tag/v1.18.19)), xAI, Cerebras, Kimi/Moonshot, DeepSeek, GitHub Copilot, and native OpenAI/Anthropic passthroughs for Cloudflare AI Gateway models. Model/auth discovery is served by `/api/model`, `/api/provider`, and the `models`/`providers` CLI; models can be switched per session (`POST /session/{id}/model`).

- Source: [packages/opencode/src/provider](https://github.com/anomalyco/opencode/tree/dev/packages/opencode/src/provider); provider endpoints in the [OpenAPI spec](https://github.com/anomalyco/opencode/blob/dev/packages/sdk/openapi.json)

## Headless and scriptable runs

`opencode run` executes the agent non-interactively (the entry point used by runtimes and CI); `opencode serve` runs the server standalone; subagent permission requests during `run` are answered (or fail with resumable `task_id`s) since [v1.18.20](https://github.com/anomalyco/opencode/releases/tag/v1.18.20). Sessions can also run in the background (`/experimental/session/{id}/background`) and be moved between instances (`/experimental/control-plane/move-session`).

- Commands: [run.ts](https://github.com/anomalyco/opencode/blob/dev/packages/opencode/src/cli/cmd/run.ts), [serve.ts](https://github.com/anomalyco/opencode/blob/dev/packages/opencode/src/cli/cmd/serve.ts)

## Terminal UI

The default `opencode` experience is a TUI (chat, session browser, diff review, model/theme pickers) built in [packages/tui](https://github.com/anomalyco/opencode/tree/dev/packages/tui); it talks to the server through a dedicated TUI control channel (`/tui/*` endpoints). Since [v1.18.13](https://github.com/anomalyco/opencode/releases/tag/v1.18.13), PR reviews in the TUI include the pull request number and URL in context.

## Desktop app (BETA)

Electron desktop app for macOS (Apple Silicon + Intel), Windows, and Linux (`.deb`, `.rpm`, `.AppImage`; also `brew install --cask opencode-desktop`): downloads at [opencode.ai/download](https://opencode.ai/download). Recent work: broad locale coverage ([v1.18.15](https://github.com/anomalyco/opencode/releases/tag/v1.18.15)), right-to-left layout and plural rules ([v1.18.13](https://github.com/anomalyco/opencode/releases/tag/v1.18.13)), JSON session transcript export from the UI ([v1.18.15](https://github.com/anomalyco/opencode/releases/tag/v1.18.15)), and project-picker/search improvements ([v1.18.12](https://github.com/anomalyco/opencode/releases/tag/v1.18.12)).

- Source: [packages/desktop](https://github.com/anomalyco/opencode/tree/dev/packages/desktop)

## Web UI and session sharing

Browser-rendered sessions via [packages/web](https://github.com/anomalyco/opencode/tree/dev/packages/web) and [packages/session-ui](https://github.com/anomalyco/opencode/tree/dev/packages/session-ui); sessions can be shared with `POST /session/{id}/share` (revocable) and exposed via `opencode web`. Blob-based attachments (images, PDFs) load correctly in the web UI since [v1.18.15](https://github.com/anomalyco/opencode/releases/tag/v1.18.15).

## Server, remote workspaces, and API generations

The server (Effect-HttpApi, [routes.ts](https://github.com/anomalyco/opencode/blob/dev/packages/server/src/routes.ts)) is the spine of the whole product; the [OpenAPI 3.1 spec](https://github.com/anomalyco/opencode/blob/dev/packages/sdk/openapi.json) currently documents two generations (legacy paths and `/api/`-prefixed "v2" paths). Remote workspaces are an experimental surface (`/experimental/workspace*` adapters/status/sync/warp); [v1.18.14](https://github.com/anomalyco/opencode/releases/tag/v1.18.14) fixed host-path leaking and added upstream 5xx logging, and [v1.18.19](https://github.com/anomalyco/opencode/releases/tag/v1.18.19) preserved v1 database compatibility during the v2 rollout.

## ACP (Agent Client Protocol)

`opencode acp` exposes an ACP server so other editors and agent systems can drive OpenCode as an engine; ACP usage metering (including cache writes) was added in [v1.18.14](https://github.com/anomalyco/opencode/releases/tag/v1.18.14).

- Source: [packages/opencode/src/acp](https://github.com/anomalyco/opencode/tree/dev/packages/opencode/src/acp)

## MCP

MCP servers are configured through the CLI and the `/mcp` endpoints, including per-server OAuth (`/mcp/{name}/auth*`) and connect/disconnect control; tools from MCP servers are exposed to the agents alongside built-in tools.

- Source: [packages/opencode/src/mcp](https://github.com/anomalyco/opencode/tree/dev/packages/opencode/src/mcp)

## LSP and code intelligence

Built-in LSP integration (`/lsp`), formatters (`/formatter`), project-wide find/symbol search (`/find`, `/find/file`, `/find/symbol`), and file status/content endpoints (`/file/*`, `/api/fs/*`) feed context to agents and the UIs.

- Source: [packages/opencode/src/lsp](https://github.com/anomalyco/opencode/tree/dev/packages/opencode/src/lsp), [packages/protocol](https://github.com/anomalyco/opencode/tree/dev/packages/protocol)

## Permissions and questions

A two-channel approval system: **permissions** (`/permission`, saved permissions, per-session replies) gate tool execution — the plan agent denies edits and asks before bash — and **questions** (`/api/question`) carry interactive model→user prompts with reply/reject. Subagent-triggered permission requests are handled since [v1.18.20](https://github.com/anomalyco/opencode/releases/tag/v1.18.20).

- Source: [packages/opencode/src/permission](https://github.com/anomalyco/opencode/tree/dev/packages/opencode/src/permission), [packages/opencode/src/question](https://github.com/anomalyco/opencode/tree/dev/packages/opencode/src/question)

## Plugins and skills

A plugin subsystem (TypeScript plugins, `opencode plug`) and a skills catalog (`/skill`) let users extend behavior; the project itself is steered by `AGENTS.md` convention files.

- Source: [packages/opencode/src/plugin](https://github.com/anomalyco/opencode/tree/dev/packages/opencode/src/plugin), [packages/plugin](https://github.com/anomalyco/opencode/tree/dev/packages/plugin), [packages/opencode/src/skill](https://github.com/anomalyco/opencode/tree/dev/packages/opencode/src/skill), [AGENTS.md](https://github.com/anomalyco/opencode/blob/dev/AGENTS.md)

## Session management, compaction, and sync

Sessions support prompting (sync/async), interruption, fork, revert/unrevert (with staged revert/commit in v2), compaction (improved to keep complete recent turns in [v1.18.17](https://github.com/anomalyco/opencode/releases/tag/v1.18.17)), summarization, and cross-instance sync (`/sync/start|replay|steal|history`), with message chronology fixes in [v1.18.15](https://github.com/anomalyco/opencode/releases/tag/v1.18.15).

- Source: [packages/opencode/src/session](https://github.com/anomalyco/opencode/tree/dev/packages/opencode/src/session), [packages/opencode/src/sync](https://github.com/anomalyco/opencode/tree/dev/packages/opencode/src/sync)

## Worktrees and VCS

Git worktrees can be managed through the API (`/experimental/worktree*` create/reset/delete) for isolated working copies, and VCS state/diffs are exposed via `/vcs/status`, `/vcs/diff`, `/vcs/apply`.

- Source: [packages/opencode/src/worktree](https://github.com/anomalyco/opencode/tree/dev/packages/opencode/src/worktree)

## GitHub integration

An official [GitHub Action](https://github.com/anomalyco/opencode/blob/dev/github/action.yml) runs OpenCode against pull requests; `opencode github` and `opencode pr` commands cover shell-side workflows, and the TUI supplies PR context to reviews ([v1.18.13](https://github.com/anomalyco/opencode/releases/tag/v1.18.13)).

## IDE and editor integrations

A VS Code extension ([sdks/vscode](https://github.com/anomalyco/opencode/tree/dev/sdks/vscode)) bridges to a running server, with IDE glue code in [packages/opencode/src/ide](https://github.com/anomalyco/opencode/tree/dev/packages/opencode/src/ide); externally, ACP and the HTTP API serve any other editor/tooling.

## Stats and cost

Token and cost statistics are tracked per session and exposed via `opencode stats`, the [packages/stats](https://github.com/anomalyco/opencode/tree/dev/packages/stats) package, and usage data in the API; malformed pricing no longer breaks cost calculation ([v1.18.19](https://github.com/anomalyco/opencode/releases/tag/v1.18.19)) and cache writes are billed into ACP usage totals ([v1.18.14](https://github.com/anomalyco/opencode/releases/tag/v1.18.14)).

## Configuration and storage

Runtime config is read/patched over HTTP (`/config`, `/global/config`) with environment overrides; unknown top-level fields are ignored instead of failing ([v1.18.16](https://github.com/anomalyco/opencode/releases/tag/v1.18.16)). State is stored in SQLite (drizzle; `opencode db` for inspection; [packages/effect-drizzle-sqlite](https://github.com/anomalyco/opencode/tree/dev/packages/effect-drizzle-sqlite)), with v1→v2 database compatibility maintained during the v2 rollout ([v1.18.19](https://github.com/anomalyco/opencode/releases/tag/v1.18.19)).

- Source: [packages/opencode/src/config](https://github.com/anomalyco/opencode/tree/dev/packages/opencode/src/config), [packages/opencode/src/storage](https://github.com/anomalyco/opencode/tree/dev/packages/opencode/src/storage)

## Installation and distribution

Official install script (`curl -fsSL https://opencode.ai/install | bash`), npm (`opencode-ai`), Homebrew (formula + `anomalyco/tap`, plus the desktop cask), Scoop, Chocolatey, pacman/AUR, mise, and nix; desktop packages per platform. Install-directory precedence: `$OPENCODE_INSTALL_DIR` → `$XDG_BIN_DIR` → `$HOME/bin` → `$HOME/.opencode/bin`.

- [Installation section in README](https://github.com/anomalyco/opencode#installation)
