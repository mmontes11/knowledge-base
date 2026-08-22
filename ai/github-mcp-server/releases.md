---
upstream: https://github.com/github/github-mcp-server
last_updated: 2026-08-22
---

# github-mcp-server — Releases

Official release pages live in the [upstream repository](https://github.com/github/github-mcp-server/releases). The table below tracks the 10 most recent releases; check the upstream page for older history. Full patch notes are kept on the release page — only high-signal changes are summarized here, with ⚠️ marking breaking or behavior-changing items.

> Operator note: the homelab deployment (`k8s-ai/infrastructure/mcp-github`) is pinned to `ghcr.io/github/github-mcp-server:1.0.4`, which predates every release listed below.

## v1.10.1 — 2026-08-20

[Release page](https://github.com/github/github-mcp-server/releases/tag/v1.10.1)

- Fix: `add_issue_comment` input schema regression from v1.10.0 ([PR #3127](https://github.com/github/github-mcp-server/pull/3127)).

## v1.10.0 — 2026-08-19

[Release page](https://github.com/github/github-mcp-server/releases/tag/v1.10.0)

- ⚠️ Security hardening: Bearer credentials now restricted to configured authorities; GHES and `ghe.com` hosts require HTTPS; lockdown limits, per-caller cache isolation, raw URL traversal rejection, response sanitization, and file-size caps ([PR #3056](https://github.com/github/github-mcp-server/pull/3056), [PR #3069](https://github.com/github/github-mcp-server/pull/3069), [PR #3108](https://github.com/github/github-mcp-server/pull/3108)–[PR #3114](https://github.com/github/github-mcp-server/pull/3114)).
- ⚠️ Behavior changes: a startup now refuses invalid static `--tools` names; symlink writes require the `allow_symlink_write: true` opt-in; requests can no longer loosen server-enforced lockdown; `add_issue_comment` rejects invalid mode combinations ([PR #3071](https://github.com/github/github-mcp-server/pull/3071), [PR #3085](https://github.com/github/github-mcp-server/pull/3085), [PR #3112](https://github.com/github/github-mcp-server/pull/3112)).
- New `delete_repository` tool with confirmed, multi-round-trip eligibility checks that persist across sessions; project view lifecycle management; visible-field config for project views; smaller Actions responses; GHES issue-fields fallback.

## v1.9.0 — 2026-08-10

[Release page](https://github.com/github/github-mcp-server/releases/tag/v1.9.0)

- ⚠️ `search_issues` now defaults to semantic (AI-assisted) search; keyword matchers still work on the same tool ([PR #3010](https://github.com/github/github-mcp-server/pull/3010)).
- Opt-in `find_duplicate` tool gated by the `duplicate_detection` flag ([PR #3020](https://github.com/github/github-mcp-server/pull/3020)).
- `issue_read` includes PRs that close an issue; supports issue-type removal; `list_label` results are now ordered by issue count; issue-field value normalization.

## v1.8.0 — 2026-07-30

[Release page](https://github.com/github/github-mcp-server/releases/tag/v1.8.0)

- ⚠️ Tool response filtering goes GA: the `fields` parameter is available by default on the selected tools (it was an insiders-only feature since v1.6.0).
- Batched `update_project_items` writes via GraphQL ([PR #2903](https://github.com/github/github-mcp-server/pull/2903)); `SearchType` support in issues search; go-sdk v1.7.0 upgrade.

## v1.7.0 — 2026-07-23

[Release page](https://github.com/github/github-mcp-server/releases/tag/v1.7.0)

- Server-to-server authentication for GitHub Apps in stdio, enabling unattended enterprise/cloud tooling ([PR #2797](https://github.com/github/github-mcp-server/pull/2797)).
- MCP spec updated to 2026-07-28 via go-sdk v1.7.0-pre.3; multi-round-trip issue creation via elicitation ([PR #2870](https://github.com/github/github-mcp-server/pull/2870)).
- Opt-in intent-aware Copilot assignment tool and `copilot_issue_intents` toolset ([PR #2909](https://github.com/github/github-mcp-server/pull/2909)); cursor-based pagination for project tools; improved lockdown modes.

## v1.6.0 — 2026-07-15

[Release page](https://github.com/github/github-mcp-server/releases/tag/v1.6.0)

- Insiders mode: new `fields` parameter on `search_code`, `get_file_contents`, and six more list/search tools to shrink tool responses.
- Issue writes now accept `rationale`, `confidence`, and `is_suggestion` hints; `issue_read` exposes parent/child hierarchy signals.
- Project tools resolve project fields by name; improved GitHub App auth diagnostics.

## v1.5.0 — 2026-06-27

[Release page](https://github.com/github/github-mcp-server/releases/tag/v1.5.0)

- ⚠️ stdio now has built-in OAuth 2.1 login for `github.com` — no PAT required; the token lives in memory only. `GITHUB_PERSONAL_ACCESS_TOKEN` still takes precedence when set.
- New reaction capabilities for issues, PRs, and comments (exposed via `add_issue_comment`).
- `issue_read` gains a `get_parent` method and sub-issue read/write in the issue tools.

## v1.4.0 — 2026-06-18

[Release page](https://github.com/github/github-mcp-server/releases/tag/v1.4.0)

- ⚠️ `create_repository` defaults to private when no visibility is provided ([PR #2694](https://github.com/github/github-mcp-server/pull/2694)).
- MCP Apps: explicit `show_ui` parameter on UI-capable write tools and additional app features ([PR #1974](https://github.com/github/github-mcp-server/pull/1974)).
- New code-quality findings tool; custom listen address for `http` mode; repository-scoped `list_issue_types`.

## v1.3.0 — 2026-06-11

[Release page](https://github.com/github/github-mcp-server/releases/tag/v1.3.0)

- `pull_request_read` gains a `get_commits` method to fetch commit lists; `get_file_blame` added (insiders mode).
- Better, AI-agent-friendly error messages when hitting GitHub API rate limits ([PR #2386](https://github.com/github/github-mcp-server/pull/2386)).
- Cursor-based pagination for Dependabot alert listing.

## v1.2.0 — 2026-06-08

[Release page](https://github.com/github/github-mcp-server/releases/tag/v1.2.0)

- `projects_write` gains `create_project` and `create_iteration_field` operations; MCP Apps align with the 2026-01-26 stable spec.
- `confidence` parameter added to issue mutation tools; `get_commit` optionally returns the patch.
- Bug fixes: team reviewer filtering and empty assignee arrays now properly clear assignees.
