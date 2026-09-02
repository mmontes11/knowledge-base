---
upstream: https://github.com/multica-ai/multica
last_updated: 2026-08-23
---

# multica — API reference

Multica has no CRDs. Its API surface is a Go REST/WebSocket backend, the `multica` CLI, and a local agent daemon. Route catalog: [server/cmd/server/router.go](https://github.com/multica-ai/multica/blob/main/server/cmd/server/router.go) (~2.4k lines, chi router). CLI reference: [CLI_AND_DAEMON.md](https://github.com/multica-ai/multica/blob/main/CLI_AND_DAEMON.md) and [multica.ai/docs/cli](https://multica.ai/docs/cli).

## CLI commands (`multica`)

Top-level commands registered in [server/cmd/multica/main.go](https://github.com/multica-ai/multica/blob/main/server/cmd/multica/main.go). The `multica` CLI is the interface agents use to operate the platform; every platform surface is scriptable through it.

| Command | Purpose |
| ------- | ------- |
| `setup` | One-command setup (configure, authenticate, start daemon); `setup self-host` for local instances. |
| `login` | Browser OAuth or `--token` login; mints a 90-day personal access token and auto-registers watched workspaces. |
| `auth` | Token and authentication management. |
| `issue` | Issue CRUD, comments, timeline, metadata, status changes, quick-create, batch operations (the core agent interface). |
| `project` | Projects and their attached resources (repos, docs, local directories). |
| `label` | Labels shared by issues, agents, and skills. |
| `property` | Custom issue properties (typed fields). |
| `agent` | Agent definitions: create/update/copy, skills, MCP servers, environment. |
| `autopilot` | Scheduled and webhook-triggered automations (cron, triggers, runs). |
| `workspace` | List and inspect workspaces. |
| `repo` | Check out a project repository on a dedicated branch in the working directory. |
| `skill` | Import, list, and manage skills (playbooks). |
| `squad` | Squads: teams of agents and people with a routing leader. |
| `chat` | Chat sessions with workspace agents; `chat history` reads back sessions. |
| `daemon` | Start/stop/manage the local agent daemon that executes agent tasks on this machine. |
| `runtime` | Register and manage agent runtimes (profiles, local directories). |
| `user` | Current user and profile information. |
| `attachment` | Issue/comment attachment handling (upload, download). |
| `config` | Local client configuration. |
| `update` | Self-update, auto-detecting the install method. |
| `version` | Print version. |

## HTTP endpoint groups

All paths below are relative to the server root; the canonical list is [router.go](https://github.com/multica-ai/multica/blob/main/server/cmd/server/router.go). Groups marked *(auth)* require a session cookie or personal access token; groups marked *(daemon)* require a daemon token or valid user token.

| Endpoint group | Purpose |
| -------------- | ------- |
| `POST /auth/send-code`, `/auth/verify-code`, `/auth/google`, `/auth/logout` | Passwordless email auth and Google OAuth. |
| `GET/PATCH /api/me`, `/api/me/onboarding` *(auth)* | Identity, profile, onboarding state. |
| `/api/workspaces` *(auth)* | Workspace CRUD; members and invitations, VCS connections, workspace MCP servers, plugins, share links, runtime profiles. |
| `/api/issues` *(auth)* | Issue CRUD and `POST /query`; comments, timeline, subscribers, metadata, labels, properties, attachments, pull-requests, rerun, quick-actions, task-runs, usage; board/table grouping (`/table/groups`, `/table/rows`, `/table/facets`); `POST /quick-create`; batch update/delete. |
| `GET /api/issues/search`, `/api/projects/search`, `/api/skills/search` *(auth)* | Full-text search over issues (+comments), projects, skills — implementation in [features.md](features.md#pgvector-postgresql-image--search). |
| `/api/issue-statuses`, `/api/issue-views`, `/api/issue-view-preferences` *(auth)* | Per-workspace custom issue statuses (7 canonical categories), saved issue views. |
| `/api/projects` *(auth)* | Projects CRUD + attached resources. |
| `/api/squads` *(auth)* | Squads CRUD + member status. |
| `/api/labels`, `/api/properties` *(auth)* | Shared label catalog; custom issue property definitions. |
| `/api/agents` *(auth)* | Agent CRUD + skills, MCP servers, env vars, tasks, cancel-tasks, archive/restore. |
| `/api/agent-runtimes`, `/api/cloud-runtime` *(auth)* | Runtime listing/usage/activity, in-place runtime updates, model discovery, local skill sync; cloud runtime node lifecycle. |
| `/api/tasks/{id}/cancel`, `/api/tasks/{id}/messages`, `/api/agent-task-snapshot`, `/api/working-agents` *(auth)* | Task control and execution-log reads. |
| `/api/chat/sessions` *(auth)* | Chat sessions with agents: CRUD, pin, archive, messages. |
| `/api/autopilots` *(auth)* | Autopilot CRUD + runs, deliveries (with replay), triggers (cron/webhook), collaborators, `/cron-preview`, quota `/usage`. |
| `/api/skills` *(auth)* | Skill CRUD + import from source, refresh, files, labels. |
| `/api/comments/{id}` *(auth)* | Comment edit/delete, resolve/unresolve, reactions. |
| `/api/attachments/{id}` *(auth)* | Attachment fetch/content/delete; `/signed-download` capability URLs. |
| `/api/pins` *(auth)* | Pinned items in the workspace sidebar. |
| `/api/personal-access-tokens` *(auth)* | 90-day CLI tokens: create, list, renew, revoke. |
| `/api/webhooks/github`, `/api/github/setup`, `/api/webhooks/vcs/{connectionId}` | GitHub App and generic VCS (GitLab/Gitea/Forgejo) webhooks and install callbacks. |
| `/api/webhooks/autopilots/{token}`, `/api/webhooks/stripe` | Autopilot webhook trigger and Stripe billing webhooks. |
| Slack, Lark, DingTalk, WeCom, Telegram: `/api/workspaces/{id}/<channel>/installations`, `/binding/redeem` *(auth)* | Chat-channel install/uninstall and identity binding; Composio integration under `/api/integrations/composio`. |
| `/api/cloud-billing/*`, `/api/cloud-workspace-subscription/*` *(auth)* | Cloud billing: balance, transactions, checkout and portal sessions, seat purchases. |
| `/api/dashboard/*` *(auth)* | Usage and failure dashboards (daily, per-agent, per-hour). |
| `/api/daemon/*` *(daemon)* | Daemon lifecycle: register/heartbeat/WebSocket, workspace and repo listing, task claim/start/progress/complete/fail/usage/messages, runtime updates, model lists, skill bundle resolution, GC checks. |
| `GET /ws` *(auth)* | Client realtime channel (issue/comment/status/activity fan-out). |
| `/health`, `/readyz`, `/healthz`, `/health/realtime` | Liveness/readiness probes and realtime metrics (token-gated). |

## Client SDKs

There is no generated client SDK. Clients are the React web app, the Electron desktop app, the Expo mobile app, and the `multica` CLI — all written against the same REST API. Server-side, queries are generated with [sqlc](https://sqlc.dev) from [`server/pkg/db/queries/`](https://github.com/multica-ai/multica/tree/main/server/pkg/db/queries).
