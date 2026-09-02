---
upstream: https://github.com/anomalyco/opencode
last_updated: 2026-08-23
---

# opencode

OpenCode is an open-source AI coding agent built as a client/server system: the `opencode` CLI runs a local HTTP server that hosts the LLM agent runtime (built-in `build` and `plan` agents plus a `general` subagent) with tools for editing, shell execution, MCP, LSP, and permissions. The terminal UI, the desktop app (BETA), and the web UI all speak to the same documented HTTP/SSE API, so the agent can be driven interactively, headlessly via `opencode run`, or externally through the API, the JS SDK, ACP, the VS Code extension, or the GitHub Action.

- Upstream repository: [anomalyco/opencode](https://github.com/anomalyco/opencode)
- Documentation: [opencode.ai/docs](https://opencode.ai/docs); product site and downloads: [opencode.ai](https://opencode.ai) (desktop app: [opencode.ai/download](https://opencode.ai/download))
- License: MIT ([LICENSE](https://github.com/anomalyco/opencode/blob/dev/LICENSE))
- Stack: TypeScript/Bun + Effect, Turbo monorepo; server on Effect-HttpApi with an [OpenAPI 3.1 spec](https://github.com/anomalyco/opencode/blob/dev/packages/sdk/openapi.json); clients (TUI, desktop, web) in the same repo; distributed as the [`opencode-ai` npm package](https://www.npmjs.com/package/opencode-ai), brew/scoop/choco/AUR/mise/nix packages, and a standalone install script

## Usage in this stack

The infrastructure repository ([k8s-ai](https://github.com/mmontes11/k8s-ai) — `apps/opencode/`) deploys OpenCode into the `ai` namespace via Flux: a `StatefulSet` running the wrapper image `ghcr.io/mmontes11/opencode:v1.18.18` (built from [mmontes11/docker-opencode](https://github.com/mmontes11/docker-opencode)) on GPU-attached nodes with a dind sidecar, plus a `multica` daemon container in the same pod; an `HTTPRoute` exposes `opencode.mmontes-internal.duckdns.org` → service `opencode:4096`; volumes are protected by restic backups and Velero replication.

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
