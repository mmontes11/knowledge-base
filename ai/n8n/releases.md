---
upstream: https://github.com/n8n-io/n8n
last_updated: 2026-08-19
---

# n8n — releases

Latest 10 official releases (newest first; GitHub release tags are versioned `n8n@x.y.z`). n8n ships several lines in parallel — the current minor line plus maintained lines that receive backports — so a patch for one line often appears as sibling releases of other lines in the same window; check each ⚠️ note before upgrading. Release pages: https://github.com/n8n-io/n8n/releases.

## n8n@2.36.0 — 2026-08-18

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.36.0) — new current line (2.35/2.34 continue as maintained lines receiving the same backports).

- ⚠️ **Agent/AI-builder fixes**: HITL approval resume schemas aligned with the confirm envelope (#36047); agent can no longer claim integration tool-call success early (#36196); stuck tool-call UI and MCP-timeout progressive updates recovered (#36162).
- **Core**: expose incoming request headers to MCP trigger tools (#35236); allow `details` field in `continueErrorOutput` mode (#35939); apply TLS options per hop through proxies (#35518); keep node IDs stable when the AI Assistant edits a workflow (#36236); resolve agent credentials through the node decryption path so external secret stores work (#36275); crash enqueued executions with unreadable data instead of leaking state (#36010).
- No security advisories listed for this release.

## n8n@1.123.72 — 2026-08-17

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%401.123.72) — legacy 1.123 maintenance line; auto-generated notes with no user-facing highlights.

## n8n@2.35.3 — 2026-08-14

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.35.3)

- ⚠️ **Google Ads node**: migrates from the sunset v21 API to v25 for Google Ads (#36259) — verify affected workflows after upgrading.
- **Microsoft Teams node**: restores the `Group.ReadWrite.All` OAuth2 scope (#36186).
- **Feature**: skip update approval for workflows created within the same Instance AI session (#36106).
- No security advisories listed for this release.

## n8n@2.34.6 — 2026-08-14

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.34.6) — 2.34 line carrying the same backports as 2.35.3 (Google Ads v21→v25 #36260, Teams scope #36187).

## n8n@1.123.71 — 2026-08-13

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%401.123.71) — legacy 1.123 maintenance line; auto-generated notes with no user-facing highlights.

## n8n@2.35.2 — 2026-08-13

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.35.2)

- **Core**: report the real activation mode for triggers activated via the publication outbox (#36151).

## n8n@2.34.5 — 2026-08-12

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.34.5)

- **Core**: apply TLS options per hop when requests go through a proxy.

## n8n@1.123.70 — 2026-08-12

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%401.123.70) — legacy line dependency bumps (adm-zip, tar, undici, fast-uri) plus a fix for scheduled poll ticks releasing the activation's expression isolate (#35934).

## n8n@2.35.1 — 2026-08-12

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.35.1)

- **Core**: accept the "Always allow" scope on data-table resume (#36080); apply TLS options per hop through proxies (#36016).

## n8n@2.35.0 — 2026-08-11

[Release page](https://github.com/n8n-io/n8n/releases/tag/n8n%402.35.0) — minor line with fixes and node-specific changes; no breaking changes declared.

- ⚠️ **X (formerly Twitter) node**: OAuth2 and API endpoints moved to `x.com` (#32699) — credential behavior changes for Twitter/X workflows.
- ⚠️ **Kafka Trigger node**: stops consumers reliably when a workflow is deactivated or updated (#35069) — previously leaked consumers are now cleaned up.
- **Node fixes**: Schedule node applies declared interval defaults to stored rules (#35271); SharePoint node points delegated `Sites.Selected` users to URL-or-ID mode (#35369); Execute Workflow node blocks invalid input mappings (#35767).
- **Core**: AI-agent and Instance AI hardening (bounded oversized agent tool results #35759, redaction policy applied to Code node console output #35121, recovery of unresponsive task runners #35456, error workflows gated behind primary readiness #35634); MCP tool schemas compiled on their declared JSON Schema dialect (#35610).

No security advisories listed for any release in this window.
