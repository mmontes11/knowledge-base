---
upstream: https://github.com/n8n-io/n8n
last_updated: 2026-10-01
---

# n8n — releases

Latest 10 official releases (newest first; GitHub release tags are versioned `n8n@x.y.z`). n8n ships several lines in parallel — the current minor line plus maintained lines that receive backports — so a patch for one line often appears as sibling releases of other lines in the same window; check each ⚠️ note before upgrading. Release pages: https://github.com/n8n-io/n8n/releases.

## n8n@2.42.2 — 2026-10-01

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.42.2) — 2.42 line.

- **Core**: stop waiting on Bull `job.finished()` for queued executions so shutdown is not blocked (#39945).

## n8n@2.41.5 — 2026-10-01

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.41.5) — maintained 2.41 line; auto-generated notes with no user-facing highlights.

## n8n@1.123.83 — 2026-09-30

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%401.123.83) — legacy 1.123 maintenance line.

- **Core**: pin `cjs-module-lexer` to 2.2.0 to address a review finding.

## n8n@2.41.4 — 2026-09-30

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.41.4) — maintained 2.41 line.

- **API**: return an execution when its stored trace context is incomplete (#39827).
- **Core**: count a database ping as successful when its reply arrives during event-loop lag (#39757); store queue job results only for executions this process enqueued (#39725).
- **Cloud**: add an n8n Assistant onboarding thread for new Cloud signups (#39771); route assistant credit CTAs to top-up for UBB accounts (#39854).

## n8n@2.42.1 — 2026-09-30

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.42.1) — 2.42 line.

- **API**: emit valid schemas for untyped values in the Public API spec (#39917); return an execution when its stored trace context is incomplete (#39836).
- **Editor**: show remaining credits on the Assistant banner for Cloud UBB (#39911); show the n8n logo on canvas in canvas-only mode (#39914).

## n8n@2.42.0 — 2026-09-29

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.42.0) — new current line (2.41/2.40 continue as maintained lines receiving the same backports).

- ⚠️ **Core — agents now on by default**: n8n agents are enabled by default (#39312) and run on a new durable agent message queue (#39435) that connects Preview and integrations to the queue (#39468), plus shared Agent context access (#38930) — agent sessions survive restarts; check before upgrading if you intentionally kept agents off.
- **API**: create projects with source IDs and package metadata (#39218); users read/write endpoints migrate to the decorator pattern (#38599, #39120); accept workflow descriptions on creation (#39705); allow `extendsCredential` in workflow create/update (#39243).
- **Nodes**: Azure OpenAI Chat Model node renamed to **Azure AI Foundry Chat Model**, gaining a project input and a deployment-list call (#39603, #39375); Anthropic node adds prompt caching to the Message operation (#35804); Microsoft Teams adds online-meeting attendees on Create/Update and user activity-feed notifications (#38850, #38954); Databricks trigger now appears in the node picker (#39176).
- No breaking changes declared; no security advisories listed for this release.

## n8n@2.40.7 — 2026-09-25

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.40.7) — maintained 2.40 line.

- **Core**: propagate project span attributes to node spans (#39457).

## n8n@1.123.82 — 2026-09-25

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%401.123.82) — legacy 1.123 maintenance line.

- **Core**: bump `adm-zip` to 0.6.1.

## n8n@2.41.3 — 2026-09-25

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.41.3) — maintained 2.41 line.

- **Core**: propagate project span attributes to node spans (#39456).

## n8n@2.41.2 — 2026-09-24

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.41.2) — maintained 2.41 line.

- **Core**: keep OpenTelemetry export working after a restart when Sentry is enabled (#39367); retry instance reports that cross the UTC midnight boundary (#39433); record live n8n Assistant test runs as verification evidence (#39383).
- **Core (feature)**: authenticate instance reports with the license certificate (#39380).

No security advisories listed for any release in this window.
