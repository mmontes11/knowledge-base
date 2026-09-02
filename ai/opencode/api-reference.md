---
upstream: https://github.com/anomalyco/opencode
last_updated: 2026-08-23
---

# opencode — API reference

OpenCode has no CRDs. It is a client/server system: `opencode` starts a local HTTP server that hosts the agent runtime, and every client (TUI, desktop, web, SDK, IDE) talks to it. The API surface is (1) a documented HTTP/SSE API with an [OpenAPI 3.1 spec](https://github.com/anomalyco/opencode/blob/dev/packages/sdk/openapi.json) — 162 paths, also served live at [opencode.ai/openapi.json](https://opencode.ai/openapi.json) — (2) the `opencode` CLI, and (3) client SDKs and integrations. The server is built on Effect-HttpApi: [routes.ts](https://github.com/anomalyco/opencode/blob/dev/packages/server/src/routes.ts) wires auth middleware and the API groups.

Note there are two API generations in the spec: legacy un-prefixed paths (`/session`, `/agent`, ...) and a newer `/api/`-prefixed generation (e.g. `/api/session`, `/api/event`). Release notes refer to this as the "v1" vs "v2" server/database (e.g. [v1.18.12](https://github.com/anomalyco/opencode/releases/tag/v1.18.12) and [v1.18.19](https://github.com/anomalyco/opencode/releases/tag/v1.18.19)); both are shipped behind the same port.

## CLI commands (`opencode`)

One row per command file in [packages/opencode/src/cli/cmd/](https://github.com/anomalyco/opencode/tree/dev/packages/opencode/src/cli/cmd); the npm package [`opencode-ai`](https://www.npmjs.com/package/opencode-ai) ships the same binary, and per-flag usage is `opencode <cmd> --help`. Running bare `opencode` launches the terminal UI. Config has no dedicated CLI command — it is read/patched at runtime via `GET/PATCH /config` and `/global/config`.

| Command | Purpose |
| ------- | ------- |
| (default) | Terminal UI for chatting with agents, browsing sessions, and reviewing diffs. |
| `run` | Run the agent non-interactively; the headless entry point used by embedders and runtimes (permission handling for `opencode run` was hardened in [v1.18.20](https://github.com/anomalyco/opencode/releases/tag/v1.18.20)). |
| `serve` | Run the server component headlessly. |
| `attach` | Attach to an existing server instance. |
| `session` | List and manage sessions from the shell. |
| `agent` | Manage custom agents (built-ins: `build`, `plan`, `general`). |
| `models` | List available models. |
| `providers` | List providers and auth state. |
| `account` | Account and authentication management. |
| `mcp` | Configure MCP servers (the server also supports per-server OAuth: `/mcp/{name}/auth/*`). |
| `plug` | Manage plugins. |
| `cmd` | Manage custom commands (see `/command` endpoints). |
| `generate` | Scaffold/generate helpers (see `--help`). |
| `export`, `import` | Export/import sessions and history (JSON transcript export also landed in the desktop UI in [v1.18.15](https://github.com/anomalyco/opencode/releases/tag/v1.18.15)). |
| `github`, `pr` | GitHub and pull-request integrations; the TUI surfaces PR review context ([v1.18.13](https://github.com/anomalyco/opencode/releases/tag/v1.18.13)). |
| `acp` | Agent Client Protocol interface (used to drive OpenCode from other editors/agents). |
| `web` | Open/expose the web UI and share links. |
| `stats` | Token and cost statistics (see [features](features.md#stats-and-cost)). |
| `db` | Inspect the local SQLite database. |
| `debug` | Debug helpers. |
| `tui` | TUI sub-commands and utilities. |
| `upgrade`, `uninstall` | Self-update (also `POST /global/upgrade`) and uninstall. |

## HTTP endpoint groups

All paths below are taken from the [OpenAPI spec](https://github.com/anomalyco/opencode/blob/dev/packages/sdk/openapi.json) (162 paths; both generations listed). Event endpoints (`/event`, `/api/event`, `/api/session/{id}/event`, `/global/event`) are server-sent streams.

| Endpoint group | Purpose |
| -------------- | ------- |
| `/session`, `POST /session`, `/session/{id}/*` (and `/api/session*`) | Session lifecycle: create, list (`/active`, `/status`), prompt (sync `POST .../prompt`, async `prompt_async`), `abort`, `interrupt`, `wait`, `diff`, `fork`, `revert`/`unrevert` (v2 also has staged `revert/stage|commit|clear`), `share`, `message`/parts CRUD, `command`, `shell`, `permissions`, `todo`, `children`, `init`, `summarize`. |
| `/api/session/{id}/compact`, `/api/session/{id}/context`, `/api/session/{id}/model`, `/api/session/{id}/agent` | Session tuning: compaction (improved in [v1.18.17](https://github.com/anomalyco/opencode/releases/tag/v1.18.17)), context, per-session model/agent switches. |
| `/agent`, `/api/agent`, `/command`, `/api/command` | Agent and custom-command catalogs. |
| `/provider`, `/api/provider`, `/api/model`, `/config/providers`, `/api/config` | Provider and model discovery; provider auth endpoints `/provider/{id}/oauth/*`. |
| `/config`, `/global/config` (+ `PATCH`), `/path` | Runtime and global configuration read/patch. |
| `/permission`, `/api/permission/*`, `/question`, `/api/question/*`, `POST /permission/{id}/reply` | Permission request/reply flow (with saved permissions) and interactive question flow; the plan agent and subagents trigger these. |
| `/pty`, `/api/pty` (+ `/{id}/connect`, `/connect-token`, `/shells`) | PTY lifecycle and WebSocket attach for terminal rendering. |
| `/mcp`, `POST /mcp`, `/mcp/{name}/(auth|connect|disconnect)` | MCP server management, including per-server OAuth flows. |
| `/lsp`, `/formatter`, `/find`, `/find/file`, `/find/symbol`, `/file`, `/file/content`, `/file/status`, `/api/fs/*` | Code intelligence: LSP, formatting, project find/symbol search, file read/status, and remote filesystem operations. |
| `/skill`, `/api/skill` | Skills catalog. |
| `/project`, `/project/current`, `POST /project/git/init`, `/experimental/project/{id}/copy*` | Project discovery, git init, and project copy. |
| `/experimental/workspace*`, `/experimental/resource`, `/experimental/capabilities` | Workspace adapters/status/sync/warp (remote workspaces, exercised by the fixes in [v1.18.14](https://github.com/anomalyco/opencode/releases/tag/v1.18.14)) plus capability introspection. |
| `/experimental/worktree*` | Git worktree management (list/create/delete/reset). |
| `/vcs`, `/vcs/status`, `/vcs/diff(/raw)`, `POST /vcs/apply` | VCS state and diff/apply. |
| `/sync/start`, `/sync/replay`, `/sync/steal`, `/sync/history`, `/experimental/session/{id}/background`, `/experimental/control-plane/move-session` | Cross-instance session sync, background sessions, and session migration. |
| `/auth/{providerID}` (`PUT`/`DELETE`) | Provider credential storage. |
| `/api/credential/{id}` (v2), `/api/integration*` (v2) | Credential management and OAuth/key integration flows with attempt tracking. |
| `/tui/*` (`append-prompt`, `submit-prompt`, `select-session`, `open-*`, `publish`, ...) | TUI control channel used by the terminal UI. |
| `/global/health`, `/api/health`, `/log`, `/global/dispose`, `/instance/dispose`, `POST /global/upgrade` | Health, logging, disposal, and upgrade. |

## Client SDKs and integrations

- **JavaScript SDK** — [packages/sdk/js](https://github.com/anomalyco/opencode/tree/dev/packages/sdk/js), generated around the same [OpenAPI spec](https://github.com/anomalyco/opencode/blob/dev/packages/sdk/openapi.json) (codegen tooling in [packages/httpapi-codegen](https://github.com/anomalyco/opencode/tree/dev/packages/httpapi-codegen)).
- **Desktop app (BETA)** — Electron client in [packages/desktop](https://github.com/anomalyco/opencode/tree/dev/packages/desktop); downloads at [opencode.ai/download](https://opencode.ai/download).
- **Web / session UI** — [packages/web](https://github.com/anomalyco/opencode/tree/dev/packages/web) and [packages/session-ui](https://github.com/anomalyco/opencode/tree/dev/packages/session-ui) render sessions in the browser against the API.
- **VS Code extension** — [sdks/vscode](https://github.com/anomalyco/opencode/tree/dev/sdks/vscode), bridging to the server (IDE glue in [packages/opencode/src/ide](https://github.com/anomalyco/opencode/tree/dev/packages/opencode/src/ide)).
- **GitHub Action** — [github/action.yml](https://github.com/anomalyco/opencode/blob/dev/github/action.yml) runs OpenCode against pull requests.
- **ACP** — Agent Client Protocol server mode via `opencode acp` ([packages/opencode/src/acp](https://github.com/anomalyco/opencode/tree/dev/packages/opencode/src/acp)).
