---
upstream: https://github.com/prometheus/blackbox_exporter
last_updated: 2026-08-23
---

# prometheus-blackbox-exporter — API reference

The prober itself has no Kubernetes CRD surface: it is a standalone HTTP daemon, and the Helm chart renders only core built-in resources (Deployment/DaemonSet, Service, ConfigMap/Secret, optional ServiceMonitor/PodMonitor/HTTPRoute/NetworkPolicy). There are two API surfaces: the prober's HTTP endpoints and its module configuration language.

## Prober HTTP endpoints

All routes are relative to `--web.route-prefix` (default `/`); TLS/basic-auth for the whole endpoint set is provided via the exporter-toolkit `--web.config.file` (see [README — TLS and basic authentication](https://github.com/prometheus/blackbox_exporter/blob/master/README.md)).

| Endpoint | Method | Purpose |
| -------- | ------ | ------- |
| `/probe` | GET | Runs one probe against `?target=<url>` with the `?module=<name>` defined in the config. Optional params: `hostname` (sets the HTTP `Host` header and TLS SNI), `debug=true` (returns the probe log and the metrics that would have been returned), `timeout`. Returns the probe in the Prometheus text format; `probe_success` indicates the outcome. |
| `/config` | GET | Returns the currently loaded configuration (source: [`main.go`](https://github.com/prometheus/blackbox_exporter/blob/master/main.go)). |
| `/logs` | GET | Returns probe logs, optionally filtered by target (target-scoped probe logs since v0.25.0). |
| `/metrics` | GET | Self-monitoring (operational) metrics of the exporter itself. |
| `/-/healthy` | GET | Liveness/readiness health check (the Helm chart's probes use this path). |
| `/-/reload` | POST | Reloads the configuration file at runtime; unchanged content does not trigger a reload (v0.28.0). |
| `/` | GET | Web UI landing page. |

## Configuration (module language)

Configuration is a YAML file (passed via `--config.file`) mapping module names to probe definitions; each module picks one `prober` plus prober-specific settings and a `timeout` (default 120s, and effective scrape timeouts are derived from the Prometheus `scrape_timeout` parameter — see [README — Configuration](https://github.com/prometheus/blackbox_exporter/blob/master/README.md)). Field-level truth lives in [CONFIGURATION.md](https://github.com/prometheus/blackbox_exporter/blob/master/CONFIGURATION.md); a complete annotated example is [example.yml](https://github.com/prometheus/blackbox_exporter/blob/master/example.yml).

| Prober (`prober:` value) | Protocol | Notable module fields (see CONFIGURATION.md for the full list) |
| ------------------------ | -------- | -------------------------------------------------------------- |
| `http` | HTTP/HTTPS (HTTP/3 since v0.28.0 via `enable_http3`) | `method`, `valid_http_versions`, `valid_status_codes`, `headers`, `body`, `follow_redirects`, `ip_protocol_fallback`, `no_follow_redirects`, `basic_auth`/`authorization_credentials`, `tls_config`, `proxy_url`, `body_size_limit` (v0.20.0), `fail_if_body_matches_regexp` (renamed in v0.14.0), JSON-body CEL matching (v0.27.0), `enable_http3` (v0.28.0). |
| `tcp` | TCP sockets | `query`/`expect` (regex), `send`, `expect_bytes` exact-byte matching (v0.28.0), `starttls`, `tls`, `tls_config`. |
| `unix` | Unix domain sockets (v0.28.0) | Same `query`/`expect`/`send`/`starttls` semantics as `tcp`, against `unix:///path`. |
| `dns` | DNS (UDP/TCP, DNS-over-TLS) | `query_type`, `query_class`, `valid_answer_codes`, `prefer_ip_protocol`, `recurse` (RD flag, v0.24.0), `dot` + `tls_config` (DNS-over-TLS, v0.17.0). |
| `icmp` | ICMP echo (needs raw sockets for `dont_fragment`) | `preferred_ip_protocol`, `ip_protocol_fallback`, `source_ip_address`, `dont_fragment`, `payload_size`, `ttl`; `root`, `CAP_NET_RAW`, or `net.ipv4.ping_group_range` (on Linux) required for raw sockets — see [README — Permissions](https://github.com/prometheus/blackbox_exporter/blob/master/README.md). |
| `grpc` | gRPC | `service` (health check, v0.20.0), `preferred_ip_protocol`, `client_tls`/`tls_config`, `request` metadata (v0.28.0). |
| `websocket` | WebSocket | `http` client settings + headers, `query`/`expect` against post-upgrade messages, `send`. |

Common per-module fields: `timeout`, `prefers_ipv6`, `source_ip_address`, `preferred_ip_protocol`, `ip_protocol_fallback` (protocol-dependent). The full canonical field list with defaults is in [CONFIGURATION.md](https://github.com/prometheus/blackbox_exporter/blob/master/CONFIGURATION.md); do not rely on this summary for exact defaults.

## Key metrics

Self metrics are at `/metrics`; per-probe metrics are returned from `/probe`. Verified names (from the prober sources and release notes; the live set is authoritative):

| Metric | Source | Notes |
| ------ | ------ | ----- |
| `probe_success` | [README](https://github.com/prometheus/blackbox_exporter/blob/master/README.md) | 1/0 probe outcome; the primary alerting signal. |
| `probe_duration_seconds` | [README](https://github.com/prometheus/blackbox_exporter/blob/master/README.md) | Total probe time; compare with `probe_timeout_seconds` to see consumed timeout. |
| `probe_http_status_code`, `probe_http_version`, `probe_http_redirects`, `probe_http_content_length`, `probe_http_uncompressed_body_length` | [prober/http.go](https://github.com/prometheus/blackbox_exporter/blob/master/prober/http.go) | HTTP probe outcomes. |
| `probe_http_ssl`, `probe_http_last_modified_timestamp_seconds` | [prober/http.go](https://github.com/prometheus/blackbox_exporter/blob/master/prober/http.go) | TLS validity (0 = invalid, 1 = valid) and Last-Modified (v0.14.0). |
| `probe_ssl_last_chain_info` | [prober/http.go](https://github.com/prometheus/blackbox_exporter/blob/master/prober/http.go) | Leaf certificate details — issuer, subject, serial, SANs, validity (v0.23.0); certificate serial number reported from v0.26.0. |
| `probe_failed_due_to_regex`, `probe_failed_due_to_cel` | [prober/http.go](https://github.com/prometheus/blackbox_exporter/blob/master/prober/http.go) | Content-matching failures (CEL matching since v0.27.0). |
| `probe_dns_duration_seconds`, `probe_dns_lookup_time_seconds`, `probe_dns_query_succeeded`, `probe_dns_answer_rrs` / `probe_dns_authority_rrs` / `probe_dns_additional_rrs`, `probe_dns_serial` | [prober/dns.go](https://github.com/prometheus/blackbox_exporter/blob/master/prober/dns.go) | DNS probe timings and RRset counts; `probe_dns_query_succeeded` since v0.23.0. |
| `probe_icmp_duration_seconds`, `probe_icmp_reply_hop_limit` | [prober/icmp.go](https://github.com/prometheus/blackbox_exporter/blob/master/prober/icmp.go) | ICMP probe timing; reply hop limit since v0.17.0. |
| `probe_grpc_status_code`, `probe_grpc_healthcheck_response`, `probe_grpc_ssl` | [prober/grpc.go](https://github.com/prometheus/blackbox_exporter/blob/master/prober/grpc.go) | gRPC health check outcomes (health check since v0.20.0). |
| `probe_websocket_status_code`, `probe_websocket_connection_upgraded`, `probe_websocket_duration_seconds` | [prober/websocket.go](https://github.com/prometheus/blackbox_exporter/blob/master/prober/websocket.go) | WebSocket upgrade and probe outcomes. |
| `probe_expect_info` | [CONFIGURATION.md](https://github.com/prometheus/blackbox_exporter/blob/master/CONFIGURATION.md) | Labels exported for TCP/Unix `query`/`expect` matching. |
| `probe_ip_protocol`, `probe_ip_addr_hash` | [prober/dns.go](https://github.com/prometheus/blackbox_exporter/blob/master/prober/dns.go) | Resolved IP information; `probe_ip_addr_hash` to detect IP changes (v0.17.0). |

## Helm chart surface

Chart `prometheus-blackbox-exporter` (source: [`charts/prometheus-blackbox-exporter/`](https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus-blackbox-exporter); published as `oci://ghcr.io/prometheus-community/charts/prometheus-blackbox-exporter`). Chart.yaml (as of 11.17.2): `appVersion: v0.28.0`, `kubeVersion: ">=1.21.0-0"`; the image is `quay.io/prometheus/blackbox-exporter`. No CRDs are installed; the optional exported kinds (`ServiceMonitor`, `PodMonitor`, `PrometheusRule`, HTTPRoute, `NetworkPolicy`) come from external projects (prometheus-operator, Gateway API, core `networking.k8s.io`).

Main values (defaults from [`values.yaml`](https://github.com/prometheus-community/helm-charts/blob/main/charts/prometheus-blackbox-exporter/values.yaml)):

| Value | Default | Purpose |
| ----- | ------- | ------- |
| `image.registry/repository/tag` | `quay.io` / `prometheus/blackbox-exporter` / `v<appVersion>` | Prober image; tag defaults to the chart's `appVersion`. |
| `kind` | `Deployment` | Workload kind; `DaemonSet` supported. |
| `replicas` | `1` | Replica count. |
| `config` | inline `modules.http_2xx` example | Module configuration, rendered as a ConfigMap mounted at `/config/blackbox.yaml`. NOTE (chart ≥ 11.17.1): binary payloads and non-ASCII bytes in `config` are silently corrupted by Helm's `toYaml` — use `configExistingSecretName` for such content. |
| `secretConfig` | `false` | Render the config into a `Secret` instead of a `ConfigMap` (sensitive data). |
| `configExistingSecretName` | `""` | Reference an externally managed config `Secret` (e.g. SealedSecret) instead of embedding config in values. |
| `configPath` | `/config/blackbox.yaml` | Alternate config file path. |
| `service.type` / `service.port` | `ClusterIP` / `9115` | Service for the prober; dual-stack option under `service.ipDualStack`. |
| `securityContext` | non-root (`1000:1000`), `drop: [ALL]`, `readOnlyRootFilesystem: true` | **ICMP probes require raw sockets** — add `NET_RAW` (or run as root) for `icmp` modules. |
| `serviceMonitor.enabled` / `serviceMonitor.selfMonitor` | both `false` | Creates one `ServiceMonitor` per `serviceMonitor.targets[]` entry (plus the self-scrape), wiring `__param_target`, `__param_module`, `__param_hostname` and per-target labels/interval; this is the chart's signature feature for declarative probe targets. |
| `serviceMonitor.apiVersion` | `monitoring.coreos.com/v1` | ServiceMonitor API version (configurable since chart 11.17.0). |
| `serviceMonitor.targets[]` | — | Each target: `name`, `url`, optional `hostname` (Host header/SNI), `labels`, `interval`, `scrapeTimeout`, `module`, `additionalMetricsRelabels`, `additionalRelabeling`; URLs accept Helm `tpl` (chart ≥ 11.15.0). |
| `prometheusRule.enabled` / `rules` | `false` / `[]` | Render custom `PrometheusRule`s (e.g., blackbox alerts). |
| `podMonitoring.enabled` | `false` | Google Managed Prometheus `PodMonitor` equivalent of the ServiceMonitors. |
| `route.main.enabled` | `false` | Gateway API `HTTPRoute` in front of the Service (chart ≥ 11.11.0); configurable `apiVersion`, `parentRefs`, `hostnames`, `matches`, `filters`, default rule name. |
| `networkPolicy.enabled` / `allowMonitoringNamespace` | `false` / `false` | NetworkPolicy restricting access; the monitoring-namespace variant uses `kubernetes.io/metadata.name` (chart ≥ 11.14.0). |
| `ingress.enabled` | `false` | Classic `Ingress` routing. |
| `configReloader.enabled` | `false` | `prometheus-config-reloader` sidecar watching the config for change (app auto-reload is a v0.28.0 feature flag instead — see releases). |
| `extraArgs`, `extraEnv`, `extraContainers`, `extraVolumes`, `extraVolumeMounts`, `extraInitContainers` | empty | Extension points (`extraArgs` is how `--web.listen-address` etc. are set). |
| `hostPort` / `hostNetwork` | `0` / `false` | DaemonSet-to-daemon communication. |
| `releaseLabel` | `false` | Add the release label so kube-prometheus-stack ServiceMonitor scraping works out of the box. |
| `verticalPodAutoscaler.enabled` | `false` | VPA for the prober. |
| `resources`, `nodeSelector`, `tolerations`, `affinity`, `topologySpreadConstraints` | standard | Scheduling and sizing; `resources` default is empty (unconstrained). |
