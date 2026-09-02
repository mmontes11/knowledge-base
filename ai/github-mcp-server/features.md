---
upstream: https://github.com/github/github-mcp-server
last_updated: 2026-08-22
---

# github-mcp-server — Features

Feature areas of the GitHub MCP Server. Each entry links to the canonical upstream documentation (in-repo [docs/](https://github.com/github/github-mcp-server/tree/main/docs) unless noted); this index summarizes intent and current status, not configuration detail.

## Transports & hosting

- **stdio transport** — default MCP transport for local agent runtimes (Claude Code, VS Code, Copilot CLI, ...); `github-mcp-server stdio`.
- **HTTP transport** — multi-client hosting; `github-mcp-server http` with `--port`, `--base-url`, `--base-path`, and a configurable listen address (since [v1.4.0](https://github.com/github/github-mcp-server/releases/tag/v1.4.0)). Used by the [remote server](https://api.githubcopilot.com/mcp/) and by the homelab deployment.
- **Docker image** — `ghcr.io/github/github-mcp-server`; supports stdio (default), `http` subcommand, GH-host configuration, and MCP Apps UI via `MCP_APPS` — see the upstream [README](https://github.com/github/github-mcp-server#run-with-docker) and [Dockerfile](https://github.com/github/github-mcp-server/blob/main/Dockerfile).
- **GitHub-hosted remote server** — fully managed at `https://api.githubcopilot.com/mcp/` with data residency options on `*.ghe.com` — [remote-server.md](https://github.com/github/github-mcp-server/blob/main/docs/remote-server.md).

## Authentication

- **OAuth 2.1, built-in for stdio** — browser-based (with device-code fallback for headless environments); the access token stays in memory and is never persisted; no `GITHUB_PERSONAL_ACCESS_TOKEN` needed for `github.com` (since [v1.5.0](https://github.com/github/github-mcp-server/releases/tag/v1.5.0)) — [oauth-login.md](https://github.com/github/github-mcp-server/blob/main/docs/oauth-login.md).
- **Personal Access Tokens** — `GITHUB_PERSONAL_ACCESS_TOKEN`; takes precedence over the built-in OAuth flow; scope requirements and scope challenges documented in the [README](https://github.com/github/github-mcp-server#personal-access-tokens); `--scope-challenge` flag on `http`.
- **GitHub App, server-to-server** — unauthenticated S2S auth over stdio for enterprise/cloud tooling (since [v1.7.0](https://github.com/github/github-mcp-server/releases/tag/v1.7.0)) — [github-app-auth.md](https://github.com/github/github-mcp-server/blob/main/docs/github-app-auth.md).
- **GHES & GitHub Enterprise Cloud** — `--gh-host` / `GITHUB_HOST`; GHES support including issue-fields fallback and, since v1.10.0, enforced HTTPS for GHES and `ghe.com` hosts.
- **Policies & governance** — how the server maps auth identity and scopes to allowed tools — [policies-and-governance.md](https://github.com/github/github-mcp-server/blob/main/docs/policies-and-governance.md).

## Tool configuration & governance

- **Toolset / tool selection** — `--toolsets` / `GITHUB_TOOLSETS` (named bundles: `context`, `actions`, `repos`, ...) or `--tools` / `GITHUB_TOOLS` (exact tool names); `default` toolset is context+repos+issues+pull_requests+users; `all` enables everything; since v1.10.0 an invalid static `--tools` name fails startup — [server-configuration.md](https://github.com/github/github-mcp-server/blob/main/docs/server-configuration.md).
- **Read-only mode** — `--read-only` / `GITHUB_READ_ONLY`; takes precedence over toolset selection and disables mutating tools.
- **Lockdown** — server-enforced cap on what clients can do; since v1.10.0 clients cannot loosen lockdown and per-callers have isolated response caches.
- **Tool response filtering** — opt-in `fields` parameter on list/search tools returns only requested fields, shrinking model context; GA since [v1.8.0](https://github.com/github/github-mcp-server/releases/tag/v1.8.0), originally an [insiders feature](https://github.com/github/github-mcp-server/blob/main/docs/insiders-features.md) (since v1.6.0).
- **Insiders mode** — experimental APIs (`--insiders` / `GITHUB_INSIDERS`, `/mcp/insiders` on the remote server, `X-MCP-Insiders: true` header) — [insiders-features.md](https://github.com/github/github-mcp-server/blob/main/docs/insiders-features.md).
- **Tool search CLI** — `github-mcp-server tool-search "<query>"` for name/description/parameter discovery ([CLI utilities](https://github.com/github/github-mcp-server#cli-utilities)).
- **Tool renames** — stable-name aliasing policy for renamed tools — [tool-renaming.md](https://github.com/github/github-mcp-server/blob/main/docs/tool-renaming.md).

## MCP Apps (interactive UI)

Write tools can render interactive UIs instead of blind argument entry: forms for issue/PR creation and updates (labels, assignees, milestone, type, description), explicit `show_ui` parameter, and open-link capability. Aligns with the MCP Apps 2026-01-26 stable spec (v1.2.0) and the multi-round-trip elicitation flow (v1.7.0); enabled in Docker via `MCP_APPS`.

## Security & hardening

- Central sanitization of untrusted GitHub response fields and removal of invisible Unicode (since v1.10.0); raw URL traversal rejection; request-body size limits; Bearer credentials restricted to configured authorities; HTTPS enforced for GHES/ghe.com hosts (v1.10.0).
- File writes: plain-text content only; symlink writes are blocked unless `allow_symlink_write: true` (v1.10.0); binary content requires separate file download then base64/encode write.
- `delete_repository` requires confirmed, multi-round-trip eligibility checks that persist across sessions (v1.10.0); destructive tools mark that in their metadata (e.g. notification subscription tools).
- OAuth tokens held in memory only; no local state or token persistence.

## Copilot integration

- `assign_copilot_to_issue`, `request_copilot_review`, and (remote-only) `create_pull_request_with_copilot` — delegating tasks to the Copilot coding agent and requesting automated reviews.
- Intent-aware issue assignment carrying `rationale`, `confidence`, and `is_suggestion` — opt-in `copilot_issue_intents` toolset (v1.7.0); issue mutation tools accept these hints since v1.6.0.
