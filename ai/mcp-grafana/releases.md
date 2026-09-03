---
upstream: https://github.com/grafana/mcp-grafana
last_updated: 2026-09-03
---

# mcp-grafana — releases

Latest 10 official releases, newest first. Check the ⚠️ entries before upgrading.

## v1.3.0 — 2026-08-28

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v1.3.0)

- **Dynamic multi-org**: per-call `orgId` argument on applicable tools via the opt-in `--dynamic-multi-org` flag, plus proxied-datasource tool discovery across every org the credential can access; new `user_info` tool reports the current identity, admin status, and accessible organizations with roles ([#943](https://github.com/grafana/mcp-grafana/pull/943)).
- **Documentation tools**: `search_docs` and `get_doc` (backed by `mcp-doc-server`) for querying Grafana product documentation — no RBAC required ([#1116](https://github.com/grafana/mcp-grafana/pull/1116)).
- **Query gating**: new `--disable-query` flag removes every datasource query tool (metadata/discovery tools stay); raw-SQL query tools (ClickHouse, Snowflake, Athena, MSSQL, PostgreSQL) are now gated behind `--disable-write` by default and can be kept with `--enable-query` ([#1085](https://github.com/grafana/mcp-grafana/pull/1085)).
- ⚠️ **Behavior change**: `get_panel_image` no longer declares its own `orgId` argument (superseded by the per-call `orgId` above); without `--dynamic-multi-org`, a call passing `orgId` is rejected as an unknown argument. The dashboard deeplink is also only returned when it opens in the organization the image was rendered from ([#943](https://github.com/grafana/mcp-grafana/pull/943)).
- Incident custom fields for create/update; `grafana_api_request` supports POST to `/api/ds/query` when query tools are enabled ([#1131](https://github.com/grafana/mcp-grafana/pull/1131), [#1125](https://github.com/grafana/mcp-grafana/pull/1125)).
- Fixes: `sqlstring` dashboard variables in `run_panel_query`; structured panel targets (e.g. CloudWatch, Elasticsearch) no longer silently dropped; proxied tools no longer leak non-published MCP clients ([#1132](https://github.com/grafana/mcp-grafana/pull/1132), [#1130](https://github.com/grafana/mcp-grafana/pull/1130), [#1128](https://github.com/grafana/mcp-grafana/pull/1128)).

## v1.2.0 — 2026-08-25

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v1.2.0)

- **New tools**: `alerting_manage_silences` (opt-in, read/write-gated), `update_incident`, and `update_alert_group` (acknowledge/resolve OnCall alert groups); `get_alert_group` now returns the last alert payload ([#991](https://github.com/grafana/mcp-grafana/pull/991), [#1080](https://github.com/grafana/mcp-grafana/pull/1080), [#1083](https://github.com/grafana/mcp-grafana/pull/1083), [#1081](https://github.com/grafana/mcp-grafana/pull/1081)).
- **Loki cost guardrail**: opt-in `--loki-guardrail-mode` (`off`/`shadow`/`enforce`) for `query_loki_logs` — requires a selective stream selector, caps the time range, and pre-checks the `index/stats` byte estimate against a budget; OTel counters record guardrail decisions ([#1031](https://github.com/grafana/mcp-grafana/pull/1031), [#1095](https://github.com/grafana/mcp-grafana/pull/1095)).
- **Agent Observability**: `agento11y_manage_experiments` and `agento11y_manage_test_suites` in the opt-in `agento11y` category ([#1062](https://github.com/grafana/mcp-grafana/pull/1062)).
- **`run_panel_query`** gains PostgreSQL and MSSQL datasource support ([#1112](https://github.com/grafana/mcp-grafana/pull/1112), [#1042](https://github.com/grafana/mcp-grafana/pull/1042)).
- **Distribution and networking**: an MCP Bundle (`.mcpb`) for Claude Desktop is attached to every release ([#1077](https://github.com/grafana/mcp-grafana/pull/1077)); scoped SOCKS5 egress proxy for Grafana traffic via `GRAFANA_SOCKS5_PROXY`, failing closed when the proxy cannot be applied ([#1119](https://github.com/grafana/mcp-grafana/pull/1119), [#1121](https://github.com/grafana/mcp-grafana/pull/1121)).
- `--server-name` flag for a custom MCP server name in the handshake and OTel `service.name` ([#1011](https://github.com/grafana/mcp-grafana/pull/1011)); compact output format for `query_loki_logs` ([#990](https://github.com/grafana/mcp-grafana/pull/990)); `list_datasources` name filter ([#973](https://github.com/grafana/mcp-grafana/pull/973)).
- ⚠️ **Behavior change**: the `mcp-go` v0.58.0 bump inherits default-on DNS-rebinding protection that returns `403` for a loopback connection carrying a non-loopback `Host` header; `--allowed-hosts` cannot loosen it, so a same-host reverse proxy preserving a non-localhost `Host` over loopback must rewrite it to localhost ([#1097](https://github.com/grafana/mcp-grafana/pull/1097)).
- Fixes: distributed tracing over the HTTP transports (global OTel `TextMapPropagator`), alert-rule matcher encoding, `GRAFANA_URL` normalization at startup, and resilient parallel proxied-tool startup ([#1084](https://github.com/grafana/mcp-grafana/issues/1084), [#1111](https://github.com/grafana/mcp-grafana/pull/1111), [#1034](https://github.com/grafana/mcp-grafana/pull/1034), [#1071](https://github.com/grafana/mcp-grafana/pull/1071)).

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
