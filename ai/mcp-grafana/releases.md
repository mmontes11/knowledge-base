---
upstream: https://github.com/grafana/mcp-grafana
last_updated: 2026-08-21
---

# mcp-grafana — releases

Latest 10 official releases, newest first. Check the ⚠️ entries before upgrading.

## v1.1.0 — 2026-08-10

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v1.1.0)

- **Caller authentication**: optional bearer-token auth for the `sse` and `streamable-http` transports via `--server-auth-token` / `MCP_GRAFANA_SERVER_TOKEN`; unauthenticated requests are rejected with `401` before any tool runs ([#1059](https://github.com/grafana/mcp-grafana/pull/1059), [#1060](https://github.com/grafana/mcp-grafana/pull/1060)).
- **Grafana Assistant**: `ask_assistant` tool (opt-in, write-gated) for open-ended questions with a full text reply ([#1026](https://github.com/grafana/mcp-grafana/pull/1026)).
- **Agent Observability**: new tools `agento11y_manage_agents`, `agento11y_manage_eval_collections`, `agento11y_manage_evaluators`, and `agento11y_manage_eval_rules` in the opt-in `agento11y` category.
- ⚠️ **Default output change**: `query_pyroscope` now returns a per-function table (`pprof -top` style) by default instead of a line-level DOT call graph; the DOT graph is still available via `format="dot"` ([#1025](https://github.com/grafana/mcp-grafana/pull/1025)).
- ⚠️ **Removed**: support for the undocumented `X-Grafana-URL` request header ([#1052](https://github.com/grafana/mcp-grafana/pull/1052)).
- Fixes: declared `readOnly`/`destructive`/`openWorld` hints on every tool; proxied-tools memory scaling; Prometheus backend restricted to known-compatible datasource types; respects `OTEL_LOGS_EXPORTER=none`.

## v1.0.0 — 2026-07-28

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v1.0.0)

- **1.0 milestone** for the server.
- **Agent Observability**: initial `agento11y` category (opt-in) with `agento11y_manage_conversations` and `agento11y_manage_generations` ([#944](https://github.com/grafana/mcp-grafana/pull/944)).
- `get_panel_image` gains an inline panel viewer for [MCP Apps](https://modelcontextprotocol.io/)-aware hosts, with a dashboard deeplink fallback tagged `_meta.ui.kind = "deeplink"` ([#882](https://github.com/grafana/mcp-grafana/pull/882)).
- ⚠️ **Behavior change**: tool calls with unknown argument keys are now rejected with an error naming the unknown keys, instead of silently ignoring them ([#997](https://github.com/grafana/mcp-grafana/pull/997)).
- Fix: enables OTLP trace export via the signal-specific `OTEL_EXPORTER_OTLP_TRACES_ENDPOINT` so traces can be shipped without also exporting logs ([#1004](https://github.com/grafana/mcp-grafana/pull/1004)).

## v0.17.2 — 2026-07-13

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v0.17.2)

- 🔒 **Security**: environment-configured credentials (service-account token, deprecated API key, basic auth, extra headers) are now bound to the configured `GRAFANA_URL`. A URL supplied in the `X-Grafana-URL` request header no longer causes those credentials to be sent to a caller-specified host.

## v0.17.1 — 2026-07-07

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v0.17.1)

- 🔒 **Security**: blocks DNS-rebinding attacks on the HTTP and SSE transports ([#957](https://github.com/grafana/mcp-grafana/pull/957)).
- Fix: navigation deeplinks now send the relative path (rather than an absolute URL) to the short-urls API ([#976](https://github.com/grafana/mcp-grafana/pull/976)).

## v0.17.0 — 2026-06-23

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v0.17.0)

- **Datasource management tools**: create and update datasources via the MCP server, write-gated, with schema-guided configuration that follows each datasource type's JSON schema and excludes sensitive credential fields ([#939](https://github.com/grafana/mcp-grafana/pull/939)).
- Fix: recognizes the Athena plugin's `rawSQL` query field when extracting dashboard panel queries ([#956](https://github.com/grafana/mcp-grafana/pull/956)).

## v0.16.0 — 2026-06-16

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v0.16.0)

- **Snapshot tools** (`list_snapshots`, `get_snapshot`, `create_snapshot`, `delete_snapshot`) for managing dashboard snapshots ([#949](https://github.com/grafana/mcp-grafana/pull/949)).
- **Dashboard schema v2**: native support in the dashboard tools ([#937](https://github.com/grafana/mcp-grafana/pull/937)); **Quickwit** datasource support and **BigQuery** in `run_panel_query`.
- **Relative time syntax** (e.g. `now-1h`) for time-range parameters across tools, and `GRAFANA_SERVICE_ACCOUNT_TOKEN_FILE` to read the service-account token from a (rotatable) file.
- `query_prometheus` now surfaces datasource `warnings` (e.g. partial Thanos responses).
- Fix: Elasticsearch client refuses HTTP redirects that would drop the request body.

## v0.15.2 — 2026-06-04

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v0.15.2)

- ⚠️ **Release fix**: Docker images are again published to `docker.io/grafana/mcp-grafana`. The v0.15.0 and v0.15.1 images were never published because the shared Docker Hub credential was restricted to read-only; the workflow now publishes via Grafana's GAR-based Docker Hub mirror pipeline ([#925](https://github.com/grafana/mcp-grafana/pull/925)).

## v0.15.1 — 2026-06-03

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v0.15.1)

- **`shorten_url`** tool for creating Grafana short links, and provisioning workflow tools (`list_provisioning_repositories`, `validate_provisioning_file`, and provisioning-branch preview in `get_panel_image`/`generate_deeplink`).
- 🔒 **Security**: redacts credentials from debug transport logs ([#920](https://github.com/grafana/mcp-grafana/pull/920)) and updates Go to 1.26.3 to fix CVE-2026-33810 ([#916](https://github.com/grafana/mcp-grafana/pull/916)).

## v0.15.0 — 2026-06-01

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v0.15.0)

- **Snowflake** and **Amazon Athena** datasource tools, plus **VictoriaLogs** support through the existing Loki tools, Loki label-strategy analyzer tools, and plugin install/search tools.
- Fixes: datasource fallback cache scoped by request path; error response body reads capped at 1 KB across all HTTP clients.
- 🔒 **Security**: updates `golang.org/x/net` to v0.55.0 ([#901](https://github.com/grafana/mcp-grafana/pull/901)).

## v0.14.0 — 2026-05-08

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v0.14.0)

- **Generic API request tool** (`grafana_api_request`) for authenticated read-only requests to any Grafana API endpoint with optional `jq` filtering ([#841](https://github.com/grafana/mcp-grafana/pull/841)).
- **OpenSearch** datasource support; a tool to retrieve Grafana plugin information; OTLP log export when `OTEL_EXPORTER_OTLP_*` is set; configurable slow-request-threshold logging.
- Server instructions now reflect only the enabled tool categories, so agents don't attempt disabled tools.
- Fix: routes OnCall tools through the IRM plugin proxy for correct on-behalf-of authentication.
