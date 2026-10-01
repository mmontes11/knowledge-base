---
upstream: https://github.com/grafana/mcp-grafana
last_updated: 2026-10-01
---

# mcp-grafana — releases

Latest 10 official releases, newest first. Check the ⚠️ entries before upgrading.

## v1.6.3 — 2026-09-30

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v1.6.3)

- 🔒 **Security**: outbound Grafana clients now refuse redirects to a different scheme, host, or port, so Grafana credentials are never forwarded to a redirect target; `--allow-cross-origin-redirects` / `GRAFANA_ALLOW_CROSS_ORIGIN_REDIRECTS` restores the previous behaviour ([#1268](https://github.com/grafana/mcp-grafana/pull/1268)).
- Fixes: Prometheus errors other than 400/422 (e.g. 500s or proxy errors) now include the response body so failures explain what went wrong ([#1270](https://github.com/grafana/mcp-grafana/pull/1270)); Docker images report the correct release version instead of a build-info fallback ([#1269](https://github.com/grafana/mcp-grafana/pull/1269)).

## v1.6.2 — 2026-09-29

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v1.6.2)

- ⚠️ **Last 1.x release**: upstream's next release will be 2.0.
- Fixes: Loki log queries return lines in time order across streams and no longer drop a line at the limit boundary ([#1259](https://github.com/grafana/mcp-grafana/pull/1259)); Loki/VictoriaLogs time bounds keep sub-second (nanosecond) precision so narrow windows return results and callers can page by exact timestamps ([#1260](https://github.com/grafana/mcp-grafana/pull/1260)); a `GRAFANA_URL` with a trailing slash no longer produces double-slashed request paths ([#1258](https://github.com/grafana/mcp-grafana/pull/1258)); PyPI wheels now compress the bundled binary, shrinking each from ~55MB to ~17MB ([#1257](https://github.com/grafana/mcp-grafana/pull/1257)).

## v1.6.1 — 2026-09-28

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v1.6.1)

- Fixes: the stdio server no longer stops answering once a client opens a `subscriptions/listen` stream on protocol `2026-07-28`, which had made `tools/list` time out in clients such as Claude Code and GitHub Copilot CLI ([#1236](https://github.com/grafana/mcp-grafana/pull/1236)); blank optional fields in the Claude Desktop extension (MCPB) no longer pass literal `${user_config.*}` placeholders as credentials, so username/password auth works when the service-account token field is empty ([#1249](https://github.com/grafana/mcp-grafana/pull/1249)).

## v1.6.0 — 2026-09-25

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v1.6.0)

- **Per-request Grafana URL selection**: opt-in `X-Grafana-URL` header under `--allow-grafana-url-override` / `GRAFANA_ALLOW_URL_OVERRIDE`, with an optional exact `--allowed-grafana-urls` allowlist and a request-scoped token (the server's environment credentials are not used for a selected URL); SSE/streamable-HTTP only ([#1242](https://github.com/grafana/mcp-grafana/pull/1242)).
- **Google Cloud Logging tools**: new opt-in `cloudlogging` category — `query_cloud_logging` plus `list_cloud_logging_projects`, `list_cloud_logging_buckets`, and `list_cloud_logging_views` discovery (requires the `googlecloud-logging-datasource` plugin ≥ 1.8.0, i.e. Grafana 11.2+) ([#1228](https://github.com/grafana/mcp-grafana/pull/1228)).
- Tempo TraceQL metrics tools now describe the query grammar with a worked example and return correction hints for common PromQL-style mistakes, so agents can fix rejected queries ([#1207](https://github.com/grafana/mcp-grafana/pull/1207)).
- Fixes: `list_prometheus_metric_names` pushes regex filtering and the result limit to the datasource instead of downloading every metric name — `page * limit` must now not exceed 10000 ([#1217](https://github.com/grafana/mcp-grafana/pull/1217)); `--base-path` now applies to streamable HTTP and works for SSE with or without a trailing slash, while `/healthz` and `/metrics` stay at the server root and conflicting paths are rejected at startup ([#1033](https://github.com/grafana/mcp-grafana/pull/1033)); empty LogQL queries return `logql is required` instead of a Loki parse error ([#1238](https://github.com/grafana/mcp-grafana/pull/1238)); `get_panel_image` returns the deeplink as structured content so the panel viewer shows "Open in Grafana" in Claude ([#1240](https://github.com/grafana/mcp-grafana/pull/1240)).

## v1.5.1 — 2026-09-17

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v1.5.1)

- Fixes: `shorten_url` no longer doubles the Grafana sub-path prefix on instances served under a sub-path ([#1205](https://github.com/grafana/mcp-grafana/pull/1205)); `get_annotation_tags` accepts `limit` as a number instead of a string, fixing type-mismatch errors from LLM callers ([#1204](https://github.com/grafana/mcp-grafana/pull/1204)); datasource TLS schema fields no longer include PEM placeholder strings that could confuse LLMs into sending literal placeholder text ([#1200](https://github.com/grafana/mcp-grafana/pull/1200)).

## v1.5.0 — 2026-09-17

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v1.5.0)

- **Anonymous usage statistics**: opt-in via `--usage-stats` / `GRAFANA_USAGE_STATS` (modes `enabled`, `disabled`, `log`; default disabled), honoring `DO_NOT_TRACK=1`; reports tool-call counts and server configuration with no PII, flushed on a 4-hour interval and at shutdown ([#1188](https://github.com/grafana/mcp-grafana/pull/1188)).
- `UserAgent` field on `GrafanaConfig` for identifying API callers in outbound Grafana requests ([#1187](https://github.com/grafana/mcp-grafana/pull/1187)).
- **Behavior change**: Tempo tools now call the Tempo API directly over HTTP instead of proxying through an MCP layer ([#1194](https://github.com/grafana/mcp-grafana/pull/1194)).
- Fix: `query_loki_logs` preserves structured metadata when using the compact output format ([#1196](https://github.com/grafana/mcp-grafana/pull/1196)).

## v1.4.2 — 2026-09-14

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v1.4.2)

- **Dashboard versions**: optional `version` parameter on `get_dashboard_by_uid` to retrieve a specific saved version, plus a new `list_dashboard_versions` tool returning version metadata (number, author, timestamp, message) ([#1158](https://github.com/grafana/mcp-grafana/pull/1158)).
- Fix: removed unconditional `tools/list` response field injection (`resultType`, `cacheScope`, `ttlMs`) that broke legacy MCP clients validating against the base protocol schema ([#1179](https://github.com/grafana/mcp-grafana/pull/1179)).

## v1.4.1 — 2026-09-11

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v1.4.1)

- ⚠️ **Breaking**: the Sift tools (`find_error_pattern_logs`, `find_slow_requests`) take a `labelSelector` parameter (PromQL/LogQL stream-selector syntax, e.g. `{namespace=~"prod.*", cluster="us-east-1"}`) that replaces the previous required `labels` map parameter ([#1165](https://github.com/grafana/mcp-grafana/pull/1165)).
- Fixes: `run_panel_query` now routes on the datasource's real type rather than the type recorded in the dashboard panel, so `--loki-enforced-matchers` can no longer be bypassed by a panel that mislabels a Loki datasource ([#1169](https://github.com/grafana/mcp-grafana/pull/1169)); the default-organisation warning is no longer logged at startup when dynamic multi-org is enabled, where a default org is expected to be absent ([#1167](https://github.com/grafana/mcp-grafana/pull/1167)).

## v1.4.0 — 2026-09-10

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v1.4.0)

- ⚠️ **Unified SQL tools**: the per-dialect SQL tools are replaced by a single opt-in `sql` category — `list_sql_databases`, `list_sql_tables`, `describe_sql_table`, and `query_sql` (macro substitution, automatic limit enforcement) — covering ClickHouse, Snowflake, Athena, MySQL, PostgreSQL, and MSSQL; `clickhouse`, `snowflake`, and `athena` remain as back-compat category aliases ([#1126](https://github.com/grafana/mcp-grafana/pull/1126)).
- **`--enable-write-tools`**: selectively re-enable individual tool names under `--disable-write` (e.g. the Sift investigation tools) without enabling all writes; `--enable-query` is now shorthand for keeping the raw-SQL query tools ([#1157](https://github.com/grafana/mcp-grafana/pull/1157)).
- **New tools**: `delete_annotation` (write-gated, completing the annotation CRUD surface) ([#1134](https://github.com/grafana/mcp-grafana/pull/1134)) and `list_cloudwatch_dimension_values` ([#1141](https://github.com/grafana/mcp-grafana/pull/1141)); `search_dashboards` gains `folderUid`, `tag`, and `starred` filters, and empty-query searches are now always restricted to dashboards ([#1154](https://github.com/grafana/mcp-grafana/pull/1154)); `matcher` (LogQL stream selector) parameter on `list_loki_label_names`/`list_loki_label_values` to narrow label discovery ([#1135](https://github.com/grafana/mcp-grafana/pull/1135)).
- **`--loki-enforced-matchers`**: operator-configured LogQL label matchers AND-ed into every native-Loki query (logs, stats, patterns, label enumeration) to restrict readable streams — fails closed on unparseable queries, requires `--disable-api`, and refuses VictoriaLogs while set; companion `--loki-label-enumeration-fallback`; plus `--instructions-append` to append operator-supplied text to the server instructions returned to clients, and a separate `/healthz` listener (`--healthz-address`) so health checks and metrics are served independently of the MCP transport ([#978](https://github.com/grafana/mcp-grafana/pull/978), [#1137](https://github.com/grafana/mcp-grafana/pull/1137)).
- Fixes: Explore deeplinks use the `panes` format introduced in Grafana 10.2 (legacy `left` fallback below 10.2) ([#1088](https://github.com/grafana/mcp-grafana/pull/1088)); `--allowed-hosts` is honored through a same-host loopback reverse proxy ([#1142](https://github.com/grafana/mcp-grafana/pull/1142)); `alerting_manage_rules` applies `search_rule_name` client-side for datasource-provisioned rules ([#1161](https://github.com/grafana/mcp-grafana/pull/1161)); `get_current_oncall_users` handles user objects from the IRM proxy API ([#1150](https://github.com/grafana/mcp-grafana/pull/1150)); tool calls with a type mismatch return a structured MCP tool error instead of an opaque internal error ([#1138](https://github.com/grafana/mcp-grafana/pull/1138)).

## v1.3.0 — 2026-08-28

[Release page](https://github.com/grafana/mcp-grafana/releases/tag/v1.3.0)

- **Dynamic multi-org**: per-call `orgId` argument on applicable tools via the opt-in `--dynamic-multi-org` flag, plus proxied-datasource tool discovery across every org the credential can access; new `user_info` tool reports the current identity, admin status, and accessible organizations with roles ([#943](https://github.com/grafana/mcp-grafana/pull/943)).
- **Documentation tools**: `search_docs` and `get_doc` (backed by `mcp-doc-server`) for querying Grafana product documentation — no RBAC required ([#1116](https://github.com/grafana/mcp-grafana/pull/1116)).
- **Query gating**: new `--disable-query` flag removes every datasource query tool (metadata/discovery tools stay); raw-SQL query tools (ClickHouse, Snowflake, Athena, MSSQL, PostgreSQL) are now gated behind `--disable-write` by default and can be kept with `--enable-query` ([#1085](https://github.com/grafana/mcp-grafana/pull/1085)).
- ⚠️ **Behavior change**: `get_panel_image` no longer declares its own `orgId` argument (superseded by the per-call `orgId` above); without `--dynamic-multi-org`, a call passing `orgId` is rejected as an unknown argument. The dashboard deeplink is also only returned when it opens in the organization the image was rendered from ([#943](https://github.com/grafana/mcp-grafana/pull/943)).
- Incident custom fields for create/update; `grafana_api_request` supports POST to `/api/ds/query` when query tools are enabled ([#1131](https://github.com/grafana/mcp-grafana/pull/1131), [#1125](https://github.com/grafana/mcp-grafana/pull/1125)).
- Fixes: `sqlstring` dashboard variables in `run_panel_query`; structured panel targets (e.g. CloudWatch, Elasticsearch) no longer silently dropped; proxied tools no longer leak non-published MCP clients ([#1132](https://github.com/grafana/mcp-grafana/pull/1132), [#1130](https://github.com/grafana/mcp-grafana/pull/1130), [#1128](https://github.com/grafana/mcp-grafana/pull/1128)).
