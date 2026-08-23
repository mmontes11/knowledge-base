---
upstream: https://github.com/multica-ai/multica
last_updated: 2026-08-23
---

# multica — features

Significant feature areas, each with a link to the matching upstream doc. The [documentation site](https://multica.ai/docs) is authoritative.

## Agents as teammates

Agents are first-class assignees: name, provider, runtime, skills, and environment are configured per agent, and they show up on the board, receive issue assignments, comment on issues, and hand work to review.

- [Agents](https://multica.ai/docs/agents) — definitions, permissions and access scopes
- [Assigning issues](https://multica.ai/docs/assigning-issues)
- **23 agent CLIs** (Claude Code, Codex, Cursor, Copilot, Kimi, OpenCode, Hermes, Pi, Reasonix, Dim, MiniMax Code, Antigravity, DeepSeek Harness, ...) via a pluggable runtime layer: [runtimes section in README](https://github.com/multica-ai/multica#runtimes)

## Runtimes and the daemon

Work executes on runtimes you control: the `multica` CLI runs a **daemon** on a laptop or cloud box that registers as a runtime, claims tasks, executes the agent CLI, and streams progress/tokens back. Runtime profiles and `local_directory` resources (with worktree mode) let tasks operate on specific folders; private runtimes are owner-only.

- [Daemon and runtimes](https://multica.ai/docs/daemon-runtimes); [CLI and daemon guide](https://github.com/multica-ai/multica/blob/main/CLI_AND_DAEMON.md)
- **Retry and timeout handling** with automatic retries and a failure monitor: [tasks docs](https://multica.ai/docs/tasks)

## Issues, review gates, and planning

Full issue lifecycle: per-workspace custom statuses over 7 canonical categories, labels, typed custom properties, KV metadata, sub-issues, timeline, board/table/grouped views, quick actions, batch operations, reruns. Completed work lands in review rather than main; humans decide what ships.

- [Issues](https://multica.ai/docs/issues); [Projects](https://multica.ai/docs/projects)
- Execution log with token usage per agent and per issue: [tasks docs](https://multica.ai/docs/tasks)

## Autopilots

Run standups, audits, and reports on a schedule: cron and webhook triggers, delivery logging with replay, collaborators, and quota enforcement.

- [Autopilots](https://multica.ai/docs/autopilots)

## Skills

Turn a solved problem into a playbook every agent reuses: skill files, import from a source with update-from-source, runtime-local skill sync, and per-agent enablement.

- [Skills](https://multica.ai/docs/skills)

## Squads

Put agents and people on one team; the squad leader routes incoming work to a member.

- [Squads](https://multica.ai/docs/squads)

## Chat

Ask the workspace a question or start work without filing an issue; sessions are listed, pinned, and archived; `multica chat history` exposes transcripts.

- [Chat](https://multica.ai/docs/chat)

## pgvector (PostgreSQL image & search)

Despite the base image name, Multica does **not** use the `pgvector` extension for vector search: no migration creates `CREATE EXTENSION vector` and no table has a `vector` column. The image matters as the container for the extensions that power full-text search, and the search implementation is where the "pgvector" string in the codebase actually points:

- **Where it is used** — self-hosting ships PostgreSQL as [`pgvector/pgvector:pg17`](https://hub.docker.com/r/pgvector/pgvector): [docker-compose.yml](https://github.com/multica-ai/multica/blob/main/docker-compose.yml#L5), [docker-compose.selfhost.yml](https://github.com/multica-ai/multica/blob/main/docker-compose.selfhost.yml#L37), and `postgres.repository: pgvector/pgvector` in [deploy/helm/multica/values.yaml](https://github.com/multica-ai/multica/blob/main/deploy/helm/multica/values.yaml#L17); CI runs the same image as a test service.
- **The use case** — issue, project, and comment search (the `q` query parameter behind `GET /api/issues/search` and `GET /api/projects/search`): multi-word OR matching with ranking (title prefix > title contains > other).
- **How it is implemented** — the handlers build `LOWER(column) LIKE '%term%'` predicates in Go and rely on GIN indexes. The pgvector image ships `pg_trgm` but *not* `pg_bigm`, so before the trgm fallback migrations self-hosted instances silently lost index support and search degraded to sequential scans (issue [MUL-4059](https://multica.ai)); the code documents this directly in [search.go](https://github.com/multica-ai/multica/blob/main/server/internal/handler/search.go#L15-L27).

  Schema — [032_issue_search_index.up.sql](https://github.com/multica-ai/multica/blob/main/server/migrations/032_issue_search_index.up.sql) enables pg_bigm with a graceful skip; [036_search_index_lower.up.sql](https://github.com/multica-ai/multica/blob/main/server/migrations/036_search_index_lower.up.sql) rebuilds the indexes on `LOWER()` expressions (pg_bigm 1.2 has no ILIKE index scan):

  ```sql
  CREATE INDEX idx_issue_title_bigm ON issue USING gin (LOWER(title) gin_bigm_ops);
  ```

  Fallback — [137_search_index_pg_trgm_extension.up.sql](https://github.com/multica-ai/multica/blob/main/server/migrations/137_search_index_pg_trgm_extension.up.sql) (`CREATE EXTENSION IF NOT EXISTS pg_trgm`) plus [138_issue_title_trgm_index.up.sql](https://github.com/multica-ai/multica/blob/main/server/migrations/138_issue_title_trgm_index.up.sql) through 142 for issue title/description, comment content, and project title/description:

  ```sql
  CREATE INDEX CONCURRENTLY IF NOT EXISTS idx_issue_title_trgm
      ON issue USING gin (LOWER(title) gin_trgm_ops);
  ```

  Code usage — the match predicate assembled per term in [issue.go](https://github.com/multica-ai/multica/blob/main/server/internal/handler/issue.go#L653) (title, description, and correlated comment content):

  ```sql
  (LOWER(i.title) LIKE %s OR LOWER(COALESCE(i.description, '')) LIKE %s
   OR EXISTS (SELECT 1 FROM comment c WHERE c.issue_id = i.id
              AND LOWER(c.content) LIKE %s))
  ```

  Every search runs in a read-only transaction with `SET LOCAL statement_timeout = 3000`; statement timeout (SQLSTATE 57014) is mapped to HTTP 503 so the frontend distinguishes a slow search from a generic 500 ([search.go](https://github.com/multica-ai/multica/blob/main/server/internal/handler/search.go)). As of v0.4.31 the bigram and trigram comment indexes are mutually exclusive (one or the other, not both).

## VCS integration

Any Git host — GitHub (App installation), GitLab, Gitea, Forgejo — with webhooks driving issue/PR sync, issue prefixes, and PR snapshots.

- [VCS integration](https://multica.ai/docs/vcs-integration)

## Channels and integrations

Trigger and follow agent work from Slack, Lark, DingTalk, WeCom, and Telegram (DingTalk/WeCom/Telegram are community-maintained); agents send produced files back into chat. Composio provides per-connection toolkits via OAuth.

- [Channels](https://multica.ai/docs/channels)

## MCP and plugins

Workspace-level MCP servers with per-agent assignment (write-only API), and a Plugin system (V1 since v0.4.25) with an Action API, surfaces, hooks, hosted MCP connections, and artifact hosting bound to immutable versions.

- [Workspaces](https://multica.ai/docs/workspaces); plugin system changes in the [v0.4.25](https://github.com/multica-ai/multica/releases/tag/v0.4.25) and [v0.4.31](https://github.com/multica-ai/multica/releases/tag/v0.4.31) releases

## Multitenancy, roles, and security

Workspaces isolate agents, issues, and settings; roles are `owner`, `admin`, `member` with per-member agent access scopes; invitations can come from email or share links.

- [Workspaces](https://multica.ai/docs/workspaces); [Members and roles](https://multica.ai/docs/members-roles); [Security model](https://multica.ai/docs/security-model)

## Self-hosting and clients

Docker Compose or Helm on your own infrastructure; web, desktop (macOS/Windows/Linux), and mobile (iOS from source) clients all against the same API.

- [Self-Hosting guide](https://github.com/multica-ai/multica/blob/main/SELF_HOSTING.md); [Desktop app](https://multica.ai/docs/desktop-app); [CLI reference](https://multica.ai/docs/cli)
