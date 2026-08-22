---
upstream: https://github.com/github/github-mcp-server
last_updated: 2026-08-22
---

# github-mcp-server — API reference

The API surface is the set of MCP **tools** the server exposes, grouped into **toolsets**. The canonical, auto-generated catalog (tool names, descriptions, and input parameters) is the [Tools section of the upstream README](https://github.com/github/github-mcp-server#tools); parameter-level documentation is intentionally not duplicated here — the catalog is authoritative. Toolsets are selected with `--toolsets`/`GITHUB_TOOLSETS` or the per-tool allow-list `--tools`/`GITHUB_TOOLS`; see the [Server Configuration Guide](https://github.com/github/github-mcp-server/blob/main/docs/server-configuration.md).

As of 2026-08-22 the local (stdio/HTTP) catalog lists **87 tools across 21 toolsets**; the [remote server](https://api.githubcopilot.com/mcp/) adds 4 more (see below).

## Toolsets

From the upstream [Available Toolsets](https://github.com/github/github-mcp-server#available-toolsets) section:

| Toolset | Description |
| ------- | ----------- |
| `context` | **Strongly recommended**: tools that provide context about the current user and GitHub context you are operating in |
| `actions` | GitHub Actions workflows and CI/CD operations |
| `code_quality` | GitHub Code Quality related tools |
| `code_security` | Code security related tools, such as GitHub Code Scanning |
| `copilot` | Copilot related tools |
| `copilot_issue_intents` | Opt-in Copilot issue assignment tools that carry intent metadata (rationale, confidence, suggestion) |
| `dependabot` | Dependabot tools |
| `discussions` | GitHub Discussions related tools |
| `gists` | GitHub Gist related tools |
| `git` | GitHub Git API related tools for low-level Git operations |
| `issues` | GitHub Issues related tools |
| `labels` | GitHub Labels related tools |
| `notifications` | GitHub Notifications related tools |
| `orgs` | GitHub Organization related tools |
| `projects` | GitHub Projects related tools |
| `pull_requests` | GitHub Pull Request related tools |
| `repos` | GitHub Repository related tools |
| `secret_protection` | Secret protection related tools, such as GitHub Secret Scanning |
| `security_advisories` | Security advisories related tools |
| `stargazers` | GitHub Stargazers related tools |
| `users` | GitHub User related tools |

The `default` toolset (used when none is specified) is `context`, `repos`, `issues`, `pull_requests`, `users`; `all` enables everything. Remote-server-only toolsets (from the upstream [Additional Toolsets](https://github.com/github/github-mcp-server#additional-toolsets-in-remote-github-mcp-server) section): `copilot` (full, incl. the Coding Agent), `copilot_spaces`, `github_support_docs_search`.

## Tools by toolset

### Actions

| Tool | Purpose | Upstream docs |
| ---- | ------- | ------------- |
| `actions_get` | Get details of GitHub Actions resources (workflows, workflow runs, jobs, artifacts) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `actions_list` | List GitHub Actions workflows, runs, jobs, and artifacts in a repository | [catalog](https://github.com/github/github-mcp-server#tools) |
| `actions_run_trigger` | Trigger GitHub Actions operations (run workflows, cancel/re-run runs) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `get_job_logs` | Get GitHub Actions workflow job logs (single job, all failed jobs, tail) | [catalog](https://github.com/github/github-mcp-server#tools) |

### Code quality / code security

| Tool | Purpose | Upstream docs |
| ---- | ------- | ------------- |
| `get_code_quality_finding` | Get a code quality finding | [catalog](https://github.com/github/github-mcp-server#tools) |
| `get_code_scanning_alert` | Get a Code Scanning alert | [catalog](https://github.com/github/github-mcp-server#tools) |
| `list_code_scanning_alerts` | List Code Scanning alerts with severity/state/ref/tool filters | [catalog](https://github.com/github/github-mcp-server#tools) |

### Context

| Tool | Purpose | Upstream docs |
| ---- | ------- | ------------- |
| `get_me` | Get the authenticated user's profile | [catalog](https://github.com/github/github-mcp-server#tools) |
| `get_team_members` | Get members of a team in an organization | [catalog](https://github.com/github/github-mcp-server#tools) |
| `get_teams` | Get teams (for the authenticated user or a given username) | [catalog](https://github.com/github/github-mcp-server#tools) |

### Copilot

| Tool | Purpose | Upstream docs |
| ---- | ------- | ------------- |
| `assign_copilot_to_issue` | Assign GitHub Copilot (Coding Agent) to an issue | [catalog](https://github.com/github/github-mcp-server#tools) |
| `request_copilot_review` | Request a Copilot code review for a pull request | [catalog](https://github.com/github/github-mcp-server#tools) |
| `assign_copilot_to_issue_with_intent` *(toolset `copilot_issue_intents`)* | Opt-in intent-aware assignment carrying rationale/confidence/is_suggestion | [catalog](https://github.com/github/github-mcp-server#tools) |
| `create_pull_request_with_copilot` *(remote only)* | Delegate a task to the GitHub Copilot coding agent via a PR | [catalog](https://github.com/github/github-mcp-server#additional-tools-in-remote-github-mcp-server) |

### Dependabot

| Tool | Purpose | Upstream docs |
| ---- | ------- | ------------- |
| `get_dependabot_alert` | Get a Dependabot alert | [catalog](https://github.com/github/github-mcp-server#tools) |
| `list_dependabot_alerts` | List Dependabot alerts with severity/state filters and cursor pagination | [catalog](https://github.com/github/github-mcp-server#tools) |

### Discussions

| Tool | Purpose | Upstream docs |
| ---- | ------- | ------------- |
| `discussion_comment_write` | Manage discussion comments (add, reply, update, delete, mark/unmark answer) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `get_discussion` | Get a discussion | [catalog](https://github.com/github/github-mcp-server#tools) |
| `get_discussion_comments` | Get discussion comments (optionally with nested replies) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `list_discussion_categories` | List discussion categories (repo or org level) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `list_discussions` | List discussions (repo or org level) with category/sort filters | [catalog](https://github.com/github/github-mcp-server#tools) |

### Gists

| Tool | Purpose | Upstream docs |
| ---- | ------- | ------------- |
| `create_gist` | Create a Gist | [catalog](https://github.com/github/github-mcp-server#tools) |
| `get_gist` | Get Gist content | [catalog](https://github.com/github/github-mcp-server#tools) |
| `list_gists` | List Gists (authenticated user or a username, optional `since`) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `update_gist` | Update a Gist (file content/description) | [catalog](https://github.com/github/github-mcp-server#tools) |

### Git

| Tool | Purpose | Upstream docs |
| ---- | ------- | ------------- |
| `get_repository_tree` | Get a repository tree with optional path filter, recursion, and ref | [catalog](https://github.com/github/github-mcp-server#tools) |

### Issues

| Tool | Purpose | Upstream docs |
| ---- | ------- | ------------- |
| `add_issue_comment` | Add a comment to an issue or PR, or react to an issue/PR/comment | [catalog](https://github.com/github/github-mcp-server#tools) |
| `get_label` | Get a specific label from a repository (also listed under `labels`) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `issue_read` | Read an issue: details, comments, sub-issues, parent, closing PRs, fields | [catalog](https://github.com/github/github-mcp-server#tools) |
| `issue_write` | Create or update an issue (title, labels, assignees, milestone, type, custom issue fields) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `list_issue_fields` | List issue fields | [catalog](https://github.com/github/github-mcp-server#tools) |
| `list_issue_types` | List available issue types (org or repo scoped) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `list_issues` | List issues in a repository with state/label/date filters | [catalog](https://github.com/github/github-mcp-server#tools) |
| `search_issues` | Search issues (semantic search by default since v1.9.0) with rich filters | [catalog](https://github.com/github/github-mcp-server#tools) |
| `sub_issue_write` | Add/remove/reprioritize sub-issues of a parent issue | [catalog](https://github.com/github/github-mcp-server#tools) |

### Labels

| Tool | Purpose | Upstream docs |
| ---- | ------- | ------------- |
| `get_label` | Get a specific label from a repository | [catalog](https://github.com/github/github-mcp-server#tools) |
| `label_write` | Write operations on repository labels | [catalog](https://github.com/github/github-mcp-server#tools) |
| `list_label` | List labels from a repository (ordered by issue count since v1.9.0) | [catalog](https://github.com/github/github-mcp-server#tools) |

### Notifications

| Tool | Purpose | Upstream docs |
| ---- | ------- | ------------- |
| `dismiss_notification` | Dismiss a notification | [catalog](https://github.com/github/github-mcp-server#tools) |
| `get_notification_details` | Get notification details | [catalog](https://github.com/github/github-mcp-server#tools) |
| `list_notifications` | List notifications | [catalog](https://github.com/github/github-mcp-server#tools) |
| `manage_notification_subscription` | Manage a notification subscription (marked destructive since v1.10.0) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `manage_repository_notification_subscription` | Manage a per-repository notification subscription | [catalog](https://github.com/github/github-mcp-server#tools) |
| `mark_all_notifications_read` | Mark all notifications as read | [catalog](https://github.com/github/github-mcp-server#tools) |

### Organizations / Users

| Tool | Purpose | Upstream docs |
| ---- | ------- | ------------- |
| `search_orgs` | Search organizations | [catalog](https://github.com/github/github-mcp-server#tools) |
| `search_users` | Search users (username, real name, followers, ...) | [catalog](https://github.com/github/github-mcp-server#tools) |

### Projects

| Tool | Purpose | Upstream docs |
| ---- | ------- | ------------- |
| `projects_get` | Get details of GitHub Projects resources | [catalog](https://github.com/github/github-mcp-server#tools) |
| `projects_list` | List GitHub Projects resources | [catalog](https://github.com/github/github-mcp-server#tools) |
| `projects_write` | Manage Projects (create project/fields, views with visible fields, batched item updates) | [catalog](https://github.com/github/github-mcp-server#tools) |

### Pull requests

| Tool | Purpose | Upstream docs |
| ---- | ------- | ------------- |
| `add_comment_to_pending_review` | Add a review comment to the requester's latest pending PR review | [catalog](https://github.com/github/github-mcp-server#tools) |
| `add_reply_to_pull_request_comment` | Reply to an existing PR comment | [catalog](https://github.com/github/github-mcp-server#tools) |
| `create_pull_request` | Open a new pull request | [catalog](https://github.com/github/github-mcp-server#tools) |
| `list_pull_requests` | List pull requests (state/sort/head/base filters) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `merge_pull_request` | Merge a pull request (merge/squash/rebase) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `pull_request_read` | Read a PR: details, diff, status, check runs, files, reviews, comments, commits | [catalog](https://github.com/github/github-mcp-server#tools) |
| `pull_request_review_write` | Write operations (create, submit, delete) on PR reviews, resolve/unresolve threads | [catalog](https://github.com/github/github-mcp-server#tools) |
| `search_pull_requests` | Search pull requests with rich filters | [catalog](https://github.com/github/github-mcp-server#tools) |
| `update_pull_request` | Edit a pull request (title, body, reviewers, draft, state) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `update_pull_request_branch` | Update a PR branch with the base branch's latest changes | [catalog](https://github.com/github/github-mcp-server#tools) |

### Repositories

| Tool | Purpose | Upstream docs |
| ---- | ------- | ------------- |
| `create_branch` | Create a branch | [catalog](https://github.com/github/github-mcp-server#tools) |
| `create_or_update_file` | Create or update a file (plain-text content; SHA required for updates) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `create_repository` | Create a repository (defaults to private when visibility omitted, since v1.4.0) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `delete_file` | Delete a file with a commit message | [catalog](https://github.com/github/github-mcp-server#tools) |
| `delete_repository` | Delete a repository with confirmed, multi-round-trip eligibility check (since v1.10.0) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `fork_repository` | Fork a repository | [catalog](https://github.com/github/github-mcp-server#tools) |
| `get_commit` | Get commit details (optionally with the patch, since v1.2.0) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `get_file_contents` | Get file or directory contents (symlinks identified and labeled since v1.10.0) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `get_latest_release` | Get the latest release | [catalog](https://github.com/github/github-mcp-server#tools) |
| `get_release_by_tag` | Get a release by tag name | [catalog](https://github.com/github/github-mcp-server#tools) |
| `get_tag` | Get a tag | [catalog](https://github.com/github/github-mcp-server#tools) |
| `list_branches` | List branches | [catalog](https://github.com/github/github-mcp-server#tools) |
| `list_commits` | List commits (author/date/path filters) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `list_releases` | List releases | [catalog](https://github.com/github/github-mcp-server#tools) |
| `list_repository_collaborators` | List repository collaborators | [catalog](https://github.com/github/github-mcp-server#tools) |
| `list_tags` | List tags | [catalog](https://github.com/github/github-mcp-server#tools) |
| `push_files` | Push multiple files in a single commit | [catalog](https://github.com/github/github-mcp-server#tools) |
| `search_code` | Search code across repositories | [catalog](https://github.com/github/github-mcp-server#tools) |
| `search_commits` | Search commits | [catalog](https://github.com/github/github-mcp-server#tools) |
| `search_repositories` | Search repositories by name/description/topics | [catalog](https://github.com/github/github-mcp-server#tools) |

### Security (advisories, secret scanning, Copilot Spaces — remote)

| Tool | Purpose | Upstream docs |
| ---- | ------- | ------------- |
| `get_secret_scanning_alert` | Get a secret scanning alert | [catalog](https://github.com/github/github-mcp-server#tools) |
| `list_secret_scanning_alerts` | List secret scanning alerts | [catalog](https://github.com/github/github-mcp-server#tools) |
| `get_global_security_advisory` | Get a global security advisory (GHSA) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `list_global_security_advisories` | List global security advisories | [catalog](https://github.com/github/github-mcp-server#tools) |
| `list_org_repository_security_advisories` | List org repository security advisories (fork-linked) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `list_repository_security_advisories` | List repository security advisories | [catalog](https://github.com/github/github-mcp-server#tools) |
| `get_copilot_space` *(remote only)* | Get a Copilot Space | [catalog](https://github.com/github/github-mcp-server#additional-tools-in-remote-github-mcp-server) |
| `list_copilot_spaces` *(remote only)* | List Copilot Spaces | [catalog](https://github.com/github/github-mcp-server#additional-tools-in-remote-github-mcp-server) |
| `github_support_docs_search` *(remote only)* | Search GitHub product/support documentation | [catalog](https://github.com/github/github-mcp-server#additional-tools-in-remote-github-mcp-server) |

### Stargazers

| Tool | Purpose | Upstream docs |
| ---- | ------- | ------------- |
| `list_starred_repositories` | List starred repositories (sorted by starred-at or pushed-at) | [catalog](https://github.com/github/github-mcp-server#tools) |
| `star_repository` | Star a repository | [catalog](https://github.com/github/github-mcp-server#tools) |
| `unstar_repository` | Unstar a repository | [catalog](https://github.com/github/github-mcp-server#tools) |

Notes:

- Tool names are stable contract: renamed tools keep old names as aliases for backward compatibility — [tool-renaming policy](https://github.com/github/github-mcp-server/blob/main/docs/tool-renaming.md).
- Write tools can be disabled fleet-wide with read-only mode, and destructive tools advertise that behavior in their metadata (e.g. notification subscription tools since v1.10.0).

## CLI surface

From the upstream [CLI utilities](https://github.com/github/github-mcp-server#cli-utilities) section (binary `github-mcp-server`):

| Command | Purpose |
| ------- | ------- |
| `github-mcp-server stdio` | Run the server over stdio (default MCP transport) |
| `github-mcp-server http` | Run the server over HTTP (`--port`, `--base-url`, `--base-path`, custom listen address since v1.4.0, `--read-only`, `--scope-challenge`, ...) |
| `github-mcp-server tool-search "<query>"` | Search tools by name, description, and input parameter names; `--max-results N` |

## HTTP endpoints

| Endpoint | Purpose |
| -------- | ------- |
| `https://api.githubcopilot.com/mcp/` | GitHub-hosted remote MCP server (HTTP transport, OAuth or PAT header) — [remote-server.md](https://github.com/github/github-mcp-server/blob/main/docs/remote-server.md) |
| `https://api.githubcopilot.com/mcp/insiders` (or `X-MCP-Insiders: true` header) | Insiders mode of the remote server — [insiders-features.md](https://github.com/github/github-mcp-server/blob/main/docs/insiders-features.md) |
| `GHE instance: /mcp` | Same remote server on GitHub Enterprise Cloud with data residency (`*.ghe.com`) |
| `http://<local host>:<port>/<base-path>/mcp` | Local `http` subcommand transport (the homelab deployment uses this, port 8080, base path `/mcp`) |
