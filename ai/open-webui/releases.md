---
upstream: https://github.com/open-webui/open-webui
last_updated: 2026-09-03
---

# open-webui — releases

Latest 10 official releases, newest first. Check the ⚠️ entries before upgrading.

## v0.11.3 — 2026-08-31

[Release page](https://github.com/open-webui/open-webui/releases/tag/v0.11.3)

- Fix: a failed database upgrade now stops at the migration error instead of starting half-migrated (upgrades from v0.11.0–v0.11.2 previously came up reporting a missing `chat.timer_at` column).
- Fix: chat branches stay linked to their parent message across reloads, exports, and later edits; chats saved with the link missing are repaired on open.
- Accessibility mode now covers dropdown menus and the model picker; general performance, stability, and security improvements.

## v0.11.2 — 2026-08-31

[Release page](https://github.com/open-webui/open-webui/releases/tag/v0.11.2)

- ⚠️ **Security**: this release includes security and access-control fixes (not all of them are enumerated); upstream recommends updating production deployments at the earliest convenience.
- **New `request` filter step**: filter authors can now adjust the payload right before each model call, including follow-up calls after tool use; the existing `inlet`/`stream`/`outlet` steps are unchanged.
- The "High Contrast Mode" setting is now named "Accessibility Mode" (same switch); streaming no longer stalls on reasoning models, and stopping a reply now works across instances on Redis-cluster deployments.

## v0.11.1 — 2026-08-25

[Release page](https://github.com/open-webui/open-webui/releases/tag/v0.11.1)

- ⚠️ **Database migrations**: this release includes database schema changes — back up before upgrading in production, and rolling updates are not supported (all instances must be updated simultaneously). It also includes security and access-control fixes.
- **Human in the loop tool approval** (admin opt-in): a conversation can be switched to ask before each tool call, with allow/deny choices remembered per conversation; a new built-in tool also lets a model pause and ask the user up to three multiple-choice questions.
- **Streaming rebuilt from the ground up**: replies stream as deltas instead of resending the whole message (up to 1000x less data on long replies), and an in-progress reply is held on the server so a refresh resumes it instead of showing a blank message (Redis-backed deployments). Behavior changes: `ENABLE_REALTIME_CHAT_SAVE` no longer has any effect, `THREAD_POOL_SIZE` now sizes both background worker pools, and forms in embedded pages are allowed by default (new settings to lock them off).

## v0.11.0 — 2026-07-27

[Release page](https://github.com/open-webui/open-webui/releases/tag/v0.11.0)

- ⚠️ **Redesigned interface**: the entire UI (chat view through admin panel) was rebuilt — narrower conversation column, lighter typography, consistent menus/dropdowns, rearranged settings. Expect layout changes after upgrading.
- **Sub-agents** (admin opt-in via `ENABLE_SUBAGENTS` plus concurrency/iteration/system-prompt settings): a model can delegate parts of a task to background helper agents that run their own tool-driven conversations and report results back into the chat.
- Folder pages (chats load per page, sortable, new chat from folder), chat timers (assistant-scheduled prompts that self-cancel on read/reply), and a notification targets tab with multiple webhook destinations.

## v0.10.2 — 2026-07-01

[Release page](https://github.com/open-webui/open-webui/releases/tag/v0.10.2)

- Streamed reasoning display for thinking models (also rendered in chat overview and exported conversations).
- Folder uploads to knowledge bases preserve subfolder structure instead of flattening.
- Admin "Memory System Context" toggle (keep memory tools available without injecting stored memories into system context); automatic memories now focus on enduring details; OpenAI-compatible STT audio can be sent as multipart or base64 JSON.

## v0.10.1 — 2026-06-29

[Release page](https://github.com/open-webui/open-webui/releases/tag/v0.10.1)

- Fix: opening/reading chats from shared folders no longer signs users out on resource-level access errors.

## v0.10.0 — 2026-06-29

[Release page](https://github.com/open-webui/open-webui/releases/tag/v0.10.0)

- **Folder sharing**: share a folder (and its chats) with users, groups, or everyone with read/write access; recipients see shared folders in their sidebar, non-owners open chats read-only; controlled by a new "Folders Sharing" permission (off by default).
- **Automatic context compaction** (off by default): conversations past a configurable token threshold are summarized automatically to fit the model context window, with per-model threshold override for admins.

## v0.9.6 — 2026-06-01

[Release page](https://github.com/open-webui/open-webui/releases/tag/v0.9.6)

- **Knowledge base sync**: `oikb` companion tool for incremental KB sync from local directories, GitHub repos, S3, Confluence, and 40+ other sources; one-action local directory sync with checksum comparison, cleanup of removed files/orphaned subdirectories, and mirrored structure.
- Knowledge base folders: nested folders with breadcrumb navigation inside a KB.

## v0.9.5 — 2026-05-10

[Release page](https://github.com/open-webui/open-webui/releases/tag/v0.9.5)

- **Security**: redirect-based SSRF protection — all outbound HTTP requests block 3xx redirects by default (`AIOHTTP_CLIENT_ALLOW_REDIRECTS`), covering web fetch, image loading, OAuth discovery, tool server execution, and code interpreter login.
- **Security**: `IFRAME_CSP` environment variable to set a Content-Security-Policy on all srcdoc iframes (Artifacts, tool embeds, file previews).
- Granular markdown rendering toggles (user vs assistant); `TERMINAL_PROXY_HEADERS` for injecting response headers on terminal proxy; Channel streaming and full chat tool support on model mentions.

## v0.9.4 — 2026-05-09

[Release page](https://github.com/open-webui/open-webui/releases/tag/v0.9.4)

- Fix: chat scroll position on load (regression from `content-visibility: auto` prevented initial scroll to bottom).
