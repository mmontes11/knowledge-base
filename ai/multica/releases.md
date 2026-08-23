---
upstream: https://github.com/multica-ai/multica
last_updated: 2026-08-23
---

# multica — releases

Latest 10 official releases, newest first. Multica ships daily patch releases; check the ⚠️ entries before upgrading.

## v0.4.32 — 2026-08-21

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.32)

- Manual rerun no longer cancels an in-flight agent run; "Project" added as a grouping option on Board and Table; admins can add workspace seats; recently-created-issue activity windows enforced.

## v0.4.31 — 2026-08-20

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.31)

- Telegram channel integration; Dim (DimCode) ACP runtime; `multica issue timeline` CLI command.
- Plugin system rebuilt in four PRs (foundations, Action API + Surface + SDK, hook engine, agent integration).
- ⚠️ **Schema**: comment search indexes made mutually exclusive (`pg_bigm` and `pg_trgm` no longer coexist) and the issue last-activity index retired; UUIDv7 primary keys introduced for append-heavy tables.
- Custom issue statuses synced over the realtime channel; insecure JWT secret defaults now fail fast in production.

## v0.4.30 — 2026-08-19

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.30)

- Custom issue status UI (picker, board, filters, settings); revision-aware concurrency guards on issues; entitlement-backed autopilot quotas with a fail-open policy provider.
- Plugin system rebuild (parts 1/2); semantic issue activity timestamps; provider secrets redacted from command logs.

## v0.4.29 — 2026-08-18

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.29)

- MiniMax Code ACP runtime; issue-status board fetches by category (archive retires without migration); tasks preserved through runtime network partitions; cache reads now billed in daily/weekly cost charts.

## v0.4.28 — 2026-08-17

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.28)

- Per-workspace custom issue statuses over 7 canonical categories; actor / multi_actor custom property types (members); bulk skill update from source.

## v0.4.27 — 2026-08-17

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.27)

- Workspace-level MCP configuration (resolution contract, write-only API, Settings UI, CLI) with per-agent assignment; workspace share-link invite system; run trace rebuilt around steps, lanes, and outcome.
- ⚠️ **Behavior change**: private runtimes are owner-only in both the API and the CLI.

## v0.4.26 — 2026-08-14

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.26)

- Workspace Billing settings UI backed by the Stripe subscription proxy; DeepSeek Harness runtime; private skill plugin developer loop; daemon workspaces root configurable.

## v0.4.25 — 2026-08-13

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.25)

- First official Plugin V1 product slice shipped (execution chain completed); worktree mode for `local_directory` project resources; imported skills updatable from their source; DingTalk groups routable to different agents.

## v0.4.24 — 2026-08-12

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.24)

- Web/WeCom agents can read photos, files, and videos from chat; `multica chat history` reads back web-chat history; daemon identifies itself on `/health`; bulk edit and env-file paste for agent environment variables.

## v0.4.23 — 2026-08-11

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.23)

- Sub-issues inherit the parent's project and assignee; Hermes agents keep memory across tasks; browser-style Cmd+[ / Cmd+] history navigation; ACP `thinking_level` driven from backend `configOptions`.
