---
upstream: https://github.com/grafana/mcp-grafana
last_updated: 2026-08-21
---

# mcp-grafana — API reference

`mcp-grafana`'s API surface is the set of **MCP tools** it exposes. The full tool list — with each tool's description, required RBAC permission, and scope — is maintained in the [upstream README "Tools" table](https://github.com/grafana/mcp-grafana/blob/main/README.md#tools); follow that link for per-tool RBAC detail rather than duplicating it here.

The tool set is configurable at server start. Most categories are **enabled by default**; a set of categories are **opt-in** (disabled by default) and must be added to `--enabled-tools`. The default `--enabled-tools` value is "all categories except `admin`, `agento11y`, `assistant`, `athena`, `clickhouse`, `cloudwatch`, `elasticsearch`, `examples`, `graphite`, `quickwit`, `runpanelquery`, and `snowflake`" — see the [CLI flags reference](https://github.com/grafana/mcp-grafana/blob/main/README.md#cli-flags-reference). Per-category `--disable-<category>` flags also exist.

| Category | Default | Tools (see [upstream](https://github.com/grafana/mcp-grafana/blob/main/README.md#tools) for RBAC) |
| -------- | ------- | --------------------------------------------------------------------------------------------- |
| Search | enabled | `search_dashboards` |
| Dashboard | enabled | `get_dashboard_by_uid`, `get_dashboard_summary`, `get_dashboard_property`, `get_dashboard_panel_queries`, `update_dashboard` (write) |
| Datasources | enabled | `list_datasources`, `get_datasource`, plus create/update datasource management tools (write-gated, v0.17.0+) |
| Prometheus | enabled | `query_prometheus`, `query_prometheus_histogram`, `list_prometheus_metric_names`, `list_prometheus_metric_metadata`, `list_prometheus_label_names`, `list_prometheus_label_values` |
| Loki | enabled | `query_loki_logs`, `query_loki_stats`, `query_loki_patterns`, `list_loki_label_names`, `list_loki_label_values`, `analyze_loki_labels` |
| Config | enabled | `suggest_loki_alloy_label_config` |
| Incidents | enabled | `list_incidents`, `get_incident`, `create_incident` (write), `add_activity_to_incident` (write), `update_incident` (write) |
| Alerting | enabled | `alerting_manage_rules` (read + write-gated mutations), `alerting_manage_routing`, `alerting_manage_silences` (read + write-gated mutations) |
| Grafana OnCall | enabled | `list_oncall_schedules`, `get_oncall_shift`, `get_current_oncall_users`, `list_oncall_teams`, `list_oncall_users`, `list_alert_groups`, `get_alert_group`, `update_alert_group` (write) |
| Sift | enabled | `list_sift_investigations`, `get_sift_investigation`, `get_sift_analysis`, `find_error_pattern_logs` (write-gated), `find_slow_requests` (write-gated) |
| Pyroscope | enabled | `list_pyroscope_label_names`, `list_pyroscope_label_values`, `list_pyroscope_profile_types`, `query_pyroscope` |
| Asserts | enabled | `get_assertions` |
| Navigation | enabled | `generate_deeplink`, `shorten_url` (v0.15.1+) |
| Annotations | enabled | `get_annotations`, `get_annotation_tags`, `create_annotation` (write), `update_annotation` (write) |
| Snapshots | enabled | `list_snapshots`, `get_snapshot`, `create_snapshot` (write), `delete_snapshot` (write) |
| Rendering | enabled | `get_panel_image` (requires the Grafana Image Renderer service) |
| Provisioning | enabled | `list_provisioning_repositories`, `validate_provisioning_file` (v0.15.1+) |
| Generic API | enabled | `grafana_api_request` — arbitrary authenticated read-only request to any Grafana API endpoint with optional `jq` filtering (v0.14.0+) |
| Plugins | enabled | `get_plugin` (installed?/version/type), plus plugin install and search tools (v0.15.0+) |
| InfluxDB | opt-in (`influxdb`) | `query_influxdb` (InfluxQL v1 / Flux v2). _Feature note states disabled by default; enabled via `--enabled-tools=influxdb`._ |
| Admin | opt-in (`admin`) | `list_teams`, `list_users_by_org`, `list_all_roles`, `get_role_details`, `get_role_assignments`, `list_user_roles`, `list_team_roles`, `get_resource_permissions`, `get_resource_description` |
| Agent Observability | opt-in (`agento11y`) | `agento11y_manage_conversations`, `agento11y_manage_generations`, `agento11y_manage_agents`, `agento11y_manage_evaluators`, `agento11y_manage_eval_rules`, `agento11y_manage_eval_collections`, `agento11y_manage_experiments`, `agento11y_manage_test_suites` (read + write-gated mutations; Grafana Cloud only) |
| Grafana Assistant | opt-in (`assistant`) | `ask_assistant` (write-gated; requires the `grafana-assistant-app` plugin) |
| ClickHouse | opt-in (`clickhouse`) | `list_clickhouse_tables`, `describe_clickhouse_table`, `query_clickhouse` |
| CloudWatch | opt-in (`cloudwatch`) | `list_cloudwatch_namespaces`, `list_cloudwatch_metrics`, `list_cloudwatch_dimensions`, `query_cloudwatch` |
| Athena | opt-in (`athena`) | `list_athena_catalogs`, `list_athena_databases`, `list_athena_tables`, `describe_athena_table`, `query_athena` |
| Snowflake | opt-in (`snowflake`) | `list_snowflake_tables`, `describe_snowflake_table`, `query_snowflake` |
| Elasticsearch/OpenSearch | opt-in (`elasticsearch`) | `query_elasticsearch` |
| Quickwit | opt-in (`quickwit`) | `query_quickwit` |
| Graphite | opt-in (`graphite`) | Query Graphite, list metrics, list tags, and query density — see the [upstream features section](https://github.com/grafana/mcp-grafana/blob/main/README.md#graphite-querying) for the exact tool names. |
| Query Examples | opt-in (`examples`) | `get_query_examples` |
| Run Panel Query | opt-in (`runpanelquery`) | `run_panel_query` |

Notes:

- "write-gated" = the operation is registered only when write tools are enabled; it is removed when the server runs with `--disable-write` (our homelab deployment). See the [Read-Only Mode](https://github.com/grafana/mcp-grafana/blob/main/README.md#read-only-mode) section for the full write-operation list.
- Incidents and Sift tools use built-in Grafana roles (`Viewer` for read, `Editor` for write) rather than fine-grained RBAC scopes; Agent Observability and Assistant tools use plugin-specific permissions.
- Datasource query tools (Prometheus, Loki, ClickHouse, CloudWatch, Athena, Snowflake, InfluxDB, Graphite, Elasticsearch/OpenSearch, Quickwit) route through the **selected Grafana datasource**, so auth/config is handled by Grafana and credentials are never seen by the MCP server for those backends.
- Field-level parameters for each tool are not duplicated here; the [upstream "Tools" table](https://github.com/grafana/mcp-grafana/blob/main/README.md#tools) and each tool's schema are canonical.
