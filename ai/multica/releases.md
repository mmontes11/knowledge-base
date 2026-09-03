---
upstream: https://github.com/multica-ai/multica
last_updated: 2026-09-03
---

# multica — releases

Latest 10 official releases, newest first. Multica ships daily patch releases; check the ⚠️ entries before upgrading.

## v0.4.38 — 2026-09-02

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.38)

- `multica issue runs` now exposes active and cross-issue agent runs; issue property filters gain operator matching on scalar properties, indexed behind a bigram prefilter.
- Claude Fable 5.1 model added with pricing; Claude Code's model catalog is now discovered from the CLI; workspaces get notified about autopilot quota.

## v0.4.37 — 2026-08-31

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.37)

- Native iPad support in the mobile client; new Huawei Cloud CodeArts agent runtime.
- Server hardening: read-header and idle timeouts on the main HTTP server; skill-file loads batched; WeCom replies carry an operator-countable delivery reason and route to the replica holding the bot's socket.

## v0.4.36 — 2026-08-28

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.36)

- Issue property filters extended to text / number / date / url values; Cloud workspaces get enforced issue-count limits with an upgrade prompt before quick create.
- "Import from local" added to the New skill dialog; MCP config for the Oh-My-Pi runtime; daemon agent inactivity budget raised to 2h with the tool budget derived from it.

## v0.4.35 — 2026-08-26

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.35)

- Per-agent conversation starters, discoverable from the chat they run in; channels gain `/new` and `/clear` conversation controls; inbox gains From and unread-only filters.
- Qwen/Kimi/Ark models priced (with `:` provider prefixes stripped); private runtimes hidden from non-owners.
- Note: two features (Codex capacity retry, editable live HTML preview) landed and were reverted within this release.

## v0.4.34 — 2026-08-25

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.34)

- Sub-issues preserve their source context; creating custom issue statuses is now always allowed; full-UUID issue refs resolve locally without a resolver GET.
- Billing: seats purchasable from the invite flow and prepaid member seat capacity enforced; self-hosting docs route `/health` through the proxy with a single-origin example.

## v0.4.33 — 2026-08-24

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.33)

- ZeroClaw added as a native ACP runtime; the workspace status catalog is injected into the agent brief; a local dev environment becomes a named object with one verb per lifecycle step.
- Plugin Public API v1 foundation exposed (globally versioned) with durable scheduled plugin hooks; self-hosting gains a daemon server URL override and `MULTICA_TASK_QUEUED_TTL` for task queue expiry.

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
