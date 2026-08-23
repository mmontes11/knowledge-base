---
upstream: https://github.com/prometheus/blackbox_exporter
last_updated: 2026-08-23
---

# prometheus-blackbox-exporter — features

Significant feature areas, each summarized from upstream documentation ([README](https://github.com/prometheus/blackbox_exporter/blob/master/README.md), [CONFIGURATION.md](https://github.com/prometheus/blackbox_exporter/blob/master/CONFIGURATION.md)) and release notes.

## Prober types

The exporter ships seven probers, selected per module via `prober:` ([CONFIGURATION.md](https://github.com/prometheus/blackbox_exporter/blob/master/CONFIGURATION.md)):

- **`http`** — HTTP/HTTPS probes with method, status-code lists, header and body assertions, redirects, basic auth/API-key/OAuth2 client auth, full client `tls_config`, proxying (CONNECT headers consistent with Prometheus since v0.24.0), request body from file (v0.24.0), `body_size_limit` (v0.20.0), response-body regex matching (renamed `fail_if_body_matches_regexp` in v0.14.0), **JSON-body CEL matching** (v0.27.0), and **HTTP/3 (QUIC)** behind `enable_http3` (v0.28.0; cannot combine HTTP/3 and HTTP/2 in `valid_http_versions`).
- **`tcp`** — raw TCP with `send`/`expect` regex query-response exchange (with capture-group reuse such as `${1}`), byte-exact `expect_bytes` matching (v0.28.0), `starttls` upgrade, and a `hostname` parameter (v0.24.0).
- **`unix`** — Unix domain socket probes with the same `send`/`expect` semantics, added in v0.28.0.
- **`dns`** — DNS probes with `query_type`/`query_class`, valid answer-code lists, recursion-desired flag (v0.24.0), and **DNS-over-TLS** (`dot`, v0.17.0, port 853).
- **`icmp`** — ICMP echo with payload size, DF bit, TTL; requires raw sockets — `root`, `CAP_NET_RAW`, or a group in `net.ipv4_ping_group_range` on Linux (see [README — Permissions](https://github.com/prometheus/blackbox_exporter/blob/master/README.md)); "rootless" ping since v0.17.0.
- **`grpc`** — gRPC probes with health-check support (v0.20.0), TLS, and client metadata configuration (v0.28.0).
- **`websocket`** — WebSocket probes: HTTP upgrade settings plus `send`/`expect` against post-upgrade messages.

## Multi-target exporter pattern (Prometheus integration)

The exporter implements the [multi-target exporter pattern](https://prometheus.io/docs/guides/multi-target-exporter/): Prometheus scrapes `/probe?target=...&module=...` after relabeling `__address__` into `__param_target` and pointing `__address__` at the exporter (see [README — Prometheus Configuration](https://github.com/prometheus/blackbox_exporter/blob/master/README.md), including a `dns_sd_config` example that also sets `__param_hostname` for virtual hosting). Probe timeout is taken from the scrape's `scrape_timeout` (slightly reduced for network delay) and can be further capped per module.

## Modules and configuration management

All probe behavior is declared in a YAML config file of named *modules* (prober + settings + `timeout`); unknown/broken configs are rejected on reload. Reloads happen on `SIGHUP`, on `POST /-/reload`, or — since v0.28.0 — automatically at a fixed interval when `--config.enable-auto-reload` is set (interval via `--config.auto-reload-interval`, default 30s), and unchanged content no longer counts as a change. The Helm chart renders `config` as a ConfigMap (or `Secret` via `secretConfig`), or consumes an externally managed Secret via `configExistingSecretName`; an optional `prometheus-config-reloader` sidecar is also available (see [values.yaml](https://github.com/prometheus-community/helm-charts/blob/main/charts/prometheus-blackbox-exporter/values.yaml)).

## TLS and certificate inspection

HTTP/gRPC/TCP probes export the TLS state of the target: `probe_http_ssl` validity, `probe_http_version`, and — the classic certificate-expiry use case — `probe_ssl_last_chain_info` carrying leaf-certificate issuer, subject, serial number, SANs, and not-before/not-after (chain info since v0.23.0, serial reporting since v0.26.0), plus the negotiated ciphersuite metric (v0.26.0). Client-side TLS is fully configurable per prober via the standard `tls_config` ([CONFIGURATION.md](https://github.com/prometheus/blackbox_exporter/blob/master/CONFIGURATION.md)). Note: since v0.21.0 the Go 1.18 defaults reject SHA-1-signed certs and disable TLS 1.0/1.1 client-side.

## Observability

- Self-monitoring metrics at `/metrics` and a health endpoint `/-/healthy` (health endpoint since v0.19.0).
- **Probe logs**: per-probe logs are retrievable per target since v0.25.0 (`/logs`), and probe errors are logged explicitly; since v0.28.0 the prober logger is independent from the application logger and is controlled by `--log.prober` (`--log.level` still governs application logs) — see the v0.28.0 breaking-change notes in [releases.md](releases.md).
- `?debug=true` on `/probe` returns the full probe debug log and the metrics that would have been returned.
- A `probe_failed_due_to_regex` / `probe_failed_due_to_cel` metric distinguishes content-match failures from connectivity failures.

## Helm chart deployment features

From the [chart values](https://github.com/prometheus-community/helm-charts/blob/main/charts/prometheus-blackbox-exporter/values.yaml) (chart `prometheus-community/prometheus-blackbox-exporter` in [helm-charts](https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus-blackbox-exporter)):

- **Declarative probe targets**: `serviceMonitor.targets[]` generates one prometheus-operator `ServiceMonitor` per target with `__param_target`/`__param_module`/`__param_hostname` wired up, per-target labels and intervals; a `selfMonitor` variant scrapes the exporter itself. Google Managed Prometheus `podMonitoring` is the equivalent.
- **Gateway API `HTTPRoute`** (`route.main`, since chart 11.11.0) as an alternative to `Ingress` for exposing the `/probe` endpoint.
- Config as `Secret` (`secretConfig`) or external Secret reference (`configExistingSecretName`) for sensitive module configs; `prometheusRule` for bundled alert rules; `NetworkPolicy` with a monitoring-namespace allow option (chart ≥ 11.14.0 uses `kubernetes.io/metadata.name`).
- Deployment or DaemonSet (`kind`, `hostPort`, `hostNetwork`), extension points (`extraArgs`, `extraContainers`, `extraVolumes`, init containers), VPA, dual-stack service, and a hardened non-root `securityContext` by default (with `NET_RAW` needed for ICMP).
