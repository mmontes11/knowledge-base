---
upstream: https://github.com/n8n-io/n8n
last_updated: 2026-09-08
---

# n8n — releases

Latest 10 official releases (newest first; GitHub release tags are versioned `n8n@x.y.z`). n8n ships several lines in parallel — the current minor line plus maintained lines that receive backports — so a patch for one line often appears as sibling releases of other lines in the same window; check each ⚠️ note before upgrading. Release pages: https://github.com/n8n-io/n8n/releases.

## n8n@2.39.0 — 2026-09-08

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.39.0) — new current line (2.38/2.37 continue as maintained lines receiving the same backports).

- ⚠️ **API — source control**: new public API endpoints for Git-backed source control (push/pull projects #36998, status #37001, push #37873); a new workflow version endpoint deprecates the old two-variable path (#37114) — API consumers should migrate.
- **Core**: OTLP gRPC protocol for OpenTelemetry tracing (#37491); Instance AI gains folder exploration (#37865), search over past conversations (#37478), and opt-in concurrency limits (#37435); HashiCorp Vault external secrets can read a KV v2 sub-path (#37748).
- **Nodes**: Microsoft Teams node adds channel-message get/reply and Online Meeting operations (#37542, #37500, #37501); Jira and Confluence add Atlassian Service Account (2LO) authentication (#37513, #37536); GraphQL node supports predefined credential types (#37671).
- No breaking changes declared; no security advisories listed for this release.

## n8n@2.38.0 — 2026-09-08

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.38.0) — 2.38 line; release notes (re)generated 2026-09-08 covering the full `n8n@2.37.0…n8n@2.38.0` window, so they overlap with 2.39.0's (API source-control endpoints, agent/AI-builder fixes, Teams/Jira/Confluence additions) plus MongoDB node parameter binding for sort and projection (#37335).

## n8n@2.37.11 — 2026-09-07

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.37.11) — maintained 2.37 line.

- **Core**: ensure running-job cleanup when a workflow run rejects (#37826); keep a serving external secrets provider active when its replacement fails (#37872).

## n8n@2.38.4 — 2026-09-07

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.38.4) — 2.38 line.

- **Core**: cap task runner task timeouts to the graceful shutdown window (#37765); keep a serving external secrets provider active when its replacement fails (#37882); stop the task broker before task runner processes on shutdown (#37886).
- **Agent**: prevent Anthropic agent threads from breaking permanently and keep run errors visible (#37722); stop npm audit from stalling Instance AI sandbox setup (#37877).

## n8n@2.37.10 — 2026-09-04

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.37.10) — maintained 2.37 line; auto-generated notes with no user-facing highlights.

## n8n@2.37.9 — 2026-09-03

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.37.9) — maintained 2.37 line.

- **Core**: restore mutating array methods on `$json` data in expressions (#37727).

## n8n@2.38.3 — 2026-09-03

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.38.3) — 2.38 line.

- **Core**: ensure running-job cleanup when a workflow run rejects (#37629).

## n8n@1.123.77 — 2026-09-03

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%401.123.77) — legacy 1.123 maintenance line; auto-generated notes with no user-facing highlights.

## n8n@2.37.7 — 2026-09-02

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.37.7) — maintained 2.37 line; auto-generated notes with no user-facing highlights.

## n8n@2.38.2 — 2026-09-02

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.38.2) — 2.38 line; auto-generated notes with no user-facing highlights.

No security advisories listed for any release in this window.
