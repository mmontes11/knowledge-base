---
upstream: https://github.com/grafana/mcp-grafana
last_updated: 2026-10-01
---

# mcp-grafana — features

Key feature areas, each linked to the upstream documentation covering it. The [upstream README](https://github.com/grafana/mcp-grafana/blob/main/README.md) (Features, CLI flags, and Usage sections) and the [`docs/` tree](https://github.com/grafana/mcp-grafana/tree/main/docs) are authoritative. The upstream [features disclaimer](https://github.com/grafana/mcp-grafana/blob/main/README.md#features) notes the capability list is informational, not a roadmap.

## Transports and deployment

- **Three MCP transports**: `stdio` (default, for local clients), `sse`, and `streamable-http` (networked, multi-client) — see the [CLI flags reference](https://github.com/grafana/mcp-grafana/blob/main/README.md#cli-flags-reference). Networked transports expose a `GET /healthz` health endpoint and optional Prometheus metrics on `/metrics`, each optionally on a separate listener/port (`--healthz-address` / `--metrics-address`, v1.4.0).
- **Distribution**: `uvx mcp-grafana` (recommended), the `grafana/mcp-grafana` Docker image (entrypoint defaults to SSE — pass `-t stdio` + `-i` for local clients), a release binary, `go install github.com/grafana/mcp-grafana/cmd/mcp-grafana@latest`, or the `grafana-mcp` Helm chart; since v1.2.0 an MCP Bundle (`.mcpb`) for Claude Desktop is attached to every release. [Usage](https://github.com/grafana/mcp-grafana/blob/main/README.md#usage)
- **TLS**: client TLS to Grafana (`--tls-*`) and server TLS for streamable-http (`--server.tls-cert-file` / `--server.tls-key-file`). [TLS Configuration](https://github.com/grafana/mcp-grafana/blob/main/README.md#tls-configuration)
- **SOCKS5 egress proxy**: `GRAFANA_SOCKS5_PROXY` routes this server's Grafana traffic (including plugin-catalog lookups) through a SOCKS5 proxy, scoped to mcp-grafana (does not touch global `HTTP(S)_PROXY`) and failing closed when misconfigured (v1.2.0). [SOCKS5 Proxy](https://github.com/grafana/mcp-grafana/blob/main/README.md#socks5-proxy)

## Dashboards

- Search, retrieve by UID, and a compact `get_dashboard_summary`; targeted reads via `get_dashboard_property` (JSONPath) to bound context-window use; `get_dashboard_panel_queries` for panel titles/queries/datasource info. [Features: Dashboards](https://github.com/grafana/mcp-grafana/blob/main/README.md#dashboards)
- Native dashboard **schema v2** support (v0.16.0) and **panel query execution** (`run_panel_query`, opt-in, v0.12.0-era BigQuery/panel overrides). [releases](https://github.com/grafana/mcp-grafana/blob/main/README.md#run-panel-query)
- **Saved dashboard versions** (v1.4.2): `get_dashboard_by_uid` accepts an optional `version` to retrieve a specific saved version, and `list_dashboard_versions` returns version metadata (number, author, timestamp, message). [releases](https://github.com/grafana/mcp-grafana/releases/tag/v1.4.2)

## Datasource querying and discovery

- **PromQL / Thanos / Mimir / VM / Cloud Monitoring**: `query_prometheus` (instant + range), `query_prometheus_histogram` (percentiles), and metric/label discovery tools. [Features: Prometheus](https://github.com/grafana/mcp-grafana/blob/main/README.md#prometheus-querying)
- **LogQL / Loki / VictoriaLogs**: `query_loki_logs` (logs + metrics), stream stats, detected patterns, label discovery (with an optional `matcher` stream selector to narrow the stream set since v1.4.0), and a label-strategy analyzer; VictoriaLogs is routed through these same tools. [Features: Loki](https://github.com/grafana/mcp-grafana/blob/main/README.md#loki-querying)
- **SQL** (opt-in `sql` category, v1.4.0): unified `list_sql_databases`, `list_sql_tables`, `describe_sql_table`, and `query_sql` (macro substitution, automatic limit enforcement) covering ClickHouse, Snowflake, Athena, MySQL, PostgreSQL, and MSSQL; the per-dialect tool sets from before v1.4.0 were replaced, and `clickhouse`/`snowflake`/`athena` remain as back-compat category aliases. [Features: SQL](https://github.com/grafana/mcp-grafana/blob/main/README.md#sql-datasource-querying)
- **Other backends** (all opt-in and routed through the Grafana datasource): CloudWatch (namespaces, metrics, dimensions, and dimension values since v1.4.0), Google Cloud Logging (opt-in `cloudlogging`, v1.6.0), Graphite, Elasticsearch/OpenSearch, Quickwit, and InfluxDB (InfluxQL/Flux). [Features overview](https://github.com/grafana/mcp-grafana/blob/main/README.md#features) and [Google Cloud Logging](https://github.com/grafana/mcp-grafana/blob/main/README.md#google-cloud-logging-querying)
- **Datasource management**: list/fetch datasources plus write-gated create/update with schema-guided configuration (v0.17.0). [releases](https://github.com/grafana/mcp-grafana/releases/tag/v0.17.0)
- **Loki cost guardrail** (`--loki-guardrail-mode` = `off`/`shadow`/`enforce`): requires a selective stream selector, caps the time range, and pre-checks Loki's index/stats byte estimate to stop runaway wide scans. [CLI flags](https://github.com/grafana/mcp-grafana/blob/main/README.md#cli-flags-reference)

## Alerting, incident, and on-call

- **Alerting**: list/get/create/update/delete alert rules, manage notification routing (policies, contact points, time intervals) and silences; supports both Grafana-managed and datasource-managed rules. [Features: Alerting](https://github.com/grafana/mcp-grafana/blob/main/README.md#alerting)
- **Incidents** (Grafana Incident): search, create, get, update (including custom fields, v1.3.0), and add activities. [Features: Incidents](https://github.com/grafana/mcp-grafana/blob/main/README.md#incidents)
- **OnCall** (Grafana IRM): schedules, shifts, current on-call users, teams, users, and alert groups (filter and acknowledge/resolve). [Features: OnCall](https://github.com/grafana/mcp-grafana/blob/main/README.md#grafana-oncall)

## Investigations and profiling

- **Sift**: list/get investigations and analyses, and detect error patterns in Loki or slow requests in Tempo; since v1.4.1 the detection tools take a `labelSelector` (PromQL/LogQL stream-selector syntax) instead of a `labels` map — **breaking** for callers using the old parameter. [Features: Sift](https://github.com/grafana/mcp-grafana/blob/main/README.md#sift-investigations)
- **Tempo**: since v1.5.0 the Tempo tools call the Tempo API directly over HTTP instead of proxying through an MCP layer, and the TraceQL metrics tools describe the query grammar with a worked example and correction hints for rejected queries (v1.6.0). [releases](https://github.com/grafana/mcp-grafana/releases/tag/v1.5.0)
- **Pyroscope** continuous profiling: profile-type, label, and profile/metric queries; default `pprof -top`-style table output with a DOT call graph available on request (v1.1.0). [Features: Pyroscope](https://github.com/grafana/mcp-grafana/blob/main/README.md#pyroscope) and [releases](https://github.com/grafana/mcp-grafana/releases/tag/v1.1.0)
- **Asserts**: assertion summaries for a given entity. [Features: Asserts](https://github.com/grafana/mcp-grafana/blob/main/README.md#asserts)

## AI / LLM tooling

- **Agent Observability** (opt-in, Grafana Cloud only): LLM conversations and generations, the agent catalog, evaluators and templates, eval rules/guards, saved-conversation collections, test suites, and offline experiments. [Features: Agent Observability](https://github.com/grafana/mcp-grafana/blob/main/README.md#agent-observability)
- **Grafana Assistant** (opt-in, write-gated, requires `grafana-assistant-app`): `ask_assistant` for open-ended, multi-turn stack-aware questions via `contextId` (v1.1.0). [Features: Assistant](https://github.com/grafana/mcp-grafana/blob/main/README.md#grafana-assistant)

## Dashboards to artifacts

- **Navigation**: `generate_deeplink` for accurate dashboard/panel/explore URLs and `shorten_url` for short links (v0.15.1) — avoids LLM URL guessing. [Features: Navigation](https://github.com/grafana/mcp-grafana/blob/main/README.md#navigation)
- **Annotations**: create (standard/Graphite), read, update/patch, and tag filters. [Features: Annotations](https://github.com/grafana/mcp-grafana/blob/main/README.md#annotations)
- **Snapshots**: list/get/create/delete dashboard snapshots (v0.16.0). [Features: Snapshots](https://github.com/grafana/mcp-grafana/blob/main/README.md#snapshots)
- **Rendering**: `get_panel_image` renders a panel or dashboard (or a provisioning-repo preview from a branch) to PNG; requires the Grafana Image Renderer; MCP Apps inline panel viewer with deeplink fallback (v1.0.0). [Features: Rendering](https://github.com/grafana/mcp-grafana/blob/main/README.md#rendering)
- **Provisioning**: list configured provisioning repositories and dry-run-validate a provisioning file — the same admission surface Grafana's PR commenter uses (v0.15.1). [Features: Provisioning](https://github.com/grafana/mcp-grafana/blob/main/README.md#provisioning)

## Generic access and plugins

- **Generic API request** (`grafana_api_request`): authenticated read-only GET to any Grafana API endpoint with optional `jq` filtering (v0.14.0); since v1.3.0 it also supports POST to `/api/ds/query` when query tools are enabled. [releases](https://github.com/grafana/mcp-grafana/releases/tag/v0.14.0)
- **Plugins**: `get_plugin` status (installed/version/type) plus install and search tools (v0.15.0). [releases](https://github.com/grafana/mcp-grafana/releases/tag/v0.15.0)

## Documentation

- **Grafana docs**: `search_docs` (search docs or list product groups) and `get_doc` (fetch a page, with outline/section retrieval) backed by `mcp-doc-server` against public `grafana.com/docs` — no RBAC required (v1.3.0). [Tools](https://github.com/grafana/mcp-grafana/blob/main/README.md#tools)

## Security and access control

- **Fine-grained RBAC**: each tool maps to required Grafana RBAC permissions and scopes; Incident/Sift use built-in roles (Viewer/Editor). [RBAC Permissions](https://github.com/grafana/mcp-grafana/blob/main/README.md#rbac-permissions) and [RBAC Scopes](https://github.com/grafana/mcp-grafana/blob/main/README.md#rbac-scopes)
- **Read-only mode**: `--disable-write` removes all write operations (dashboards, folders, incidents, alerting, OnCall, annotations, Sift investigation creation, snapshots, Agent Observability writes) while keeping reads — the mode our homelab deployment runs in. Since v1.3.0 it also removes the raw-SQL query tools by default (keepable with `--enable-query`), `--disable-query` removes every datasource query tool, and since v1.4.0 `--enable-write-tools=<names>` selectively re-enables individual tools (e.g. the Sift investigation tools) without enabling all writes. [Read-Only Mode](https://github.com/grafana/mcp-grafana/blob/main/README.md#read-only-mode) and [Query-Free Mode](https://github.com/grafana/mcp-grafana/blob/main/README.md#query-free-mode)
- **Caller authentication**: optional bearer-token auth (`--server-auth-token` / `MCP_GRAFANA_SERVER_TOKEN`) for networked transports (v1.1.0). [CLI flags](https://github.com/grafana/mcp-grafana/blob/main/README.md#caller-authentication-sse--streamable-http-only)
- **Per-request Grafana URL selection** (v1.6.0, opt-in, networked transports): the `X-Grafana-URL` header selects the target Grafana per request under `--allow-grafana-url-override` / `GRAFANA_ALLOW_URL_OVERRIDE`, optionally pinned to exact URLs by `--allowed-grafana-urls` / `GRAFANA_ALLOWED_URLS`; a selected URL uses a request-scoped token and never the server's environment credentials. [CLI flags](https://github.com/grafana/mcp-grafana/blob/main/README.md#cli-flags-reference)
- **Loki query enforcement** (`--loki-enforced-matchers`, v1.4.0): operator-configured LogQL label matchers AND-ed into every native-Loki query to restrict which streams the server can read; fails closed on unparseable queries and requires `--disable-api`. [Loki query enforcement](https://github.com/grafana/mcp-grafana/blob/main/README.md#loki-query-enforcement)
- **Network hardening**: `Host`/`Origin` allowlists on every networked route (DNS-rebinding mitigation, v0.17.1), credential-to-URL binding, credential redaction in debug logs, and since v1.6.3 outbound Grafana clients refuse redirects to a different scheme, host, or port so credentials are never forwarded to a redirect target (`--allow-cross-origin-redirects` restores the previous behaviour). [releases](https://github.com/grafana/mcp-grafana/releases/tag/v0.17.2) and [releases](https://github.com/grafana/mcp-grafana/releases/tag/v1.6.3)

## Observability (self-monitoring)

- **Prometheus metrics** (`--metrics`), **OTel tracing**, and **OTel log export** following OTel MCP semantic conventions (OTLP/gRPC only); configurable slow-request logging via `--slow-request-threshold`. [Observability](https://github.com/grafana/mcp-grafana/blob/main/README.md#observability)
- **Anonymous usage statistics** (v1.5.0, disabled by default): opt-in `--usage-stats` / `GRAFANA_USAGE_STATS` (`enabled`/`disabled`/`log`) reports tool-call counts and server configuration with no PII, honoring `DO_NOT_TRACK=1`. [Anonymous usage statistics](https://github.com/grafana/mcp-grafana/blob/main/README.md#anonymous-usage-statistics)

## Multi-org and header forwarding

- **Multi-organization**: target org via `GRAFANA_ORG_ID` or the `X-Grafana-Org-Id` header (header wins). **Custom and forwarded headers**: `GRAFANA_EXTRA_HEADERS` (static) and `GRAFANA_FORWARD_HEADERS` (per-request, for SSO/SSO-cookie forwarding behind a gateway). [Usage](https://github.com/grafana/mcp-grafana/blob/main/README.md#multi-organization-support)
- **Dynamic multi-org** (v1.3.0): `--dynamic-multi-org` lets one connection target a different organization per tool call via an optional per-call `orgId` argument (proxied datasource tools are discovered across all accessible orgs); `user_info` reports the current identity, admin status, and accessible orgs with roles. [Usage](https://github.com/grafana/mcp-grafana/blob/main/README.md#dynamic-per-call-organization-selection)
