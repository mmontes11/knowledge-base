---
upstream: https://github.com/multica-ai/multica
last_updated: 2026-10-01
---

# multica — releases

Latest 10 official releases, newest first. Multica ships daily patch releases; check the ⚠️ entries before upgrading.

## v0.6.1 — 2026-10-01

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.6.1)

- CLI: `multica issue status` / `issue update` can mark duplicates with `--duplicate-of`; run charts follow the pointer and a cost sparkline replaces the sidebar strip.
- Daemon follows directory junctions when authorizing repo checkout workdirs; a 403 usage-limit failure classifies as provider quota, not auth.
- Codex catalog gains a GPT-6.1 Sol fallback with current model pricing; working-agent refresh queries scoped to visible agents.

## v0.6.0 — 2026-09-28

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.6.0)

- Issue **wakeups v2**: conditions, runaway protection, check-ins and visibility, on top of the event/time wakeups from v0.5.1; a running agent can be steered from the normal composer, per recipient.
- Issue deliverables and dynamic blocks: sidebar section, overview grid, viewer info panel, html/mermaid block frame; the attachment viewer gains CSV tables, JSON/YAML tree, numbered code and HTML viewports, every kind opening in a new tab.
- The workspace chooses the status merged PRs move an issue to; local search index for web and desktop; settings regrouped by scope; Mermaid previews fit to the whole diagram with inline zoom.
- Fallback catalogs list Claude Opus 5.5 and GPT-6 Sol/Luna; WeCom notices localized to the reader's language.

## v0.5.3 — 2026-09-24

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.5.3)

- Issues auto-complete when every linked PR merges — and only a closing keyword completes an issue on PR merge.
- Telegram sends and receives photos, videos and files; Grok gains live-run steering via Grok Build ACP; Antigravity streams live tool execution events.
- Full-window attachment viewer pages through every file; French documentation added; mobile Simplified Chinese localization; `MULTICA_LLM_DISABLE_THINKING` env supported.

## v0.5.2 — 2026-09-23

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.5.2)

- **Duplicate marking**: new column, markable from the status picker, one click from the original and traceable; issues can be created atomically with custom properties.
- Messages can be sent to active Claude Code and Codex runs to steer them (task supplements); Windows CLI installer offered alongside curl.
- Telegram inlines recent group context on @mentions; invitation-based signup restored; OpenClaw `agents.entries` per-agent workspaces supported.

## v0.5.1 — 2026-09-21

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.5.1)

- Issue **event and time wakeups** added: an agent run triggers off issue events or a schedule; repositories can set their checkout ref from the UI.
- Steering active agent turns from comments landed and was **reverted** within this release; OpenCode 2.x runtimes supported.
- CLI supports self-hosted Gitea as a release source; copy-link-to-a-single-comment; self-hosting passes `MULTICA_TELEGRAM_SECRET_KEY` / `MULTICA_DINGTALK_SECRET_KEY` through to the backend.

## v0.5.0 — 2026-09-18

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.5.0)

- CLI: `multica issue comment update` command and skill label commands (label filtering in skill lists) added.
- Issues filterable by the parent project's status; UI session expiry now slides instead of forcing a re-login every 30 days.
- Issue status category model completed (four lifecycle categories over the 7 canonical keys); archived inbox paginated; French locale added; OpenCode >= 1.1.54 required.

## v0.4.44 — 2026-09-15

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.44)

- ⚠️ **Schema**: issue lifecycle unified into four stored categories (unstarted / started / done / closed) over the 7 canonical status keys; a reserved **Triage** status key was introduced and withdrawn within this release. A resumable internal backfill API migrates existing data.
- DingTalk gains contextual replies and task lifecycle reactions; the daemon supports self-hosted DeepSeek Harness (`dsh`) desktop; anonymous self-hosting telemetry added.
- Reference-only PR links retired (column dropped); Alpine OpenSSL packages upgraded in the web image.

## v0.4.43 — 2026-09-11

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.43)

- Self-hosting: universal Redis clients and **cluster mode** supported; desktop gains navigation history menus.
- Comments and descriptions can be annotated; agent conversation starters exposed in CLI agent create/update; the transcript flags truncated tool outputs.
- gpt-6-astra added to the Codex fallback catalog with published rates; Next.js CVE-2026-75604 patched; unused issue-description search indexes retired.

## v0.4.42 — 2026-09-09

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.42)

- Agent executions render inline in comment threads; thread navigation consolidated in the sidebar outline.
- Issue search statement timeout extended to eight seconds; obsolete comment-content search indexes retired.
- Daemon records task phase latency; claim-path attachment and runtime lookups batched.

## v0.4.41 — 2026-09-07

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.41)

- CLI: `issue list` JSON output gains `--fields`, and custom property names resolve in issue JSON; agent task history exposes usage.
- Issue search refactored to a candidate-first pipeline with a 5-second statement timeout; GitHub PR refresh lookups routed to the read replica.
- Sidebar navigation reorganized by work vs AI team; settings navigation and integration settings regrouped.

## v0.4.40 — 2026-09-04

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.40)

- Agent "task records" renamed to **runs** — a terminology/API rename; anything keyed on the old "task records" naming may need updating.
- Read replica infrastructure added; daemon workspace-sync reads now routed to the replica.
- Built-in skills merged into a single platform skill and skill source maps dropped; session expiry now redirects to login.

## v0.4.39 — 2026-09-03

[Release page](https://github.com/multica-ai/multica/releases/tag/v0.4.39)

- CLI: filter and sort issue lists by custom property; agents get a live model catalog refresh.
- Performance: database reads removed from WebSocket heartbeats; runtime-deletion invalidation pushed to active daemons.
- Issue assignment handoff notes removed (a follow-up fix preserves legacy handoff notes); desktop numbered tab shortcuts added.

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
