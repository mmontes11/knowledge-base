---
upstream: https://github.com/multica-ai/multica
last_updated: 2026-09-03
---

# multica

Multica is an open-source workspace for assigning work to AI coding agents the way you'd assign it to a teammate: an agent is picked as issue assignee, picks the task up on a runtime you control, comments progress as it goes, and hands the work back at review. It is self-hostable (Docker Compose or Helm), works with 26 agent CLIs (Claude Code, Codex, Cursor, Kimi, OpenCode, and more), and ships web, desktop, and mobile clients plus the `multica` CLI that agents themselves use to drive the platform.

- Upstream repository: [multica-ai/multica](https://github.com/multica-ai/multica)
- Documentation: [multica.ai/docs](https://multica.ai/docs); website and cloud: [multica.ai](https://multica.ai); self-hosting guide: [SELF_HOSTING.md](https://github.com/multica-ai/multica/blob/main/SELF_HOSTING.md)
- License: Apache-2.0 with additional conditions ([LICENSE](https://github.com/multica-ai/multica/blob/main/LICENSE)): Part I restricts offering Multica as a hosted service to third parties or embedding it in a sold product without a commercial license
- Stack: Go backend (Chi router, pgx v5, sqlc), PostgreSQL + Redis, React web app, Electron desktop, Expo mobile, `multica` CLI/daemon

## Usage in this stack

The infrastructure repository ([k8s-ai](https://github.com/mmontes11/k8s-ai) — `apps/multica/`) deploys Multica in the `ai` namespace via Flux: a `HelmRelease` consuming the OCI chart `oci://ghcr.io/multica-ai/charts/multica` with external PostgreSQL/Redis wired through values, and `HTTPRoute`s exposing `multica-api.mmontes.duckdns.org` plus the GitHub App webhook and setup routes.

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
