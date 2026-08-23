---
upstream: https://github.com/prometheus/blackbox_exporter
last_updated: 2026-08-23
---

# prometheus-blackbox-exporter — releases

Latest 10 official releases of the prober (`prometheus/blackbox_exporter`), newest first. There are two release streams for this stack: the **prober app** (below) and the **Helm chart** (`prometheus-community/helm-charts`, chart releases listed in the second section) — bump the artifact you consume, and check the ⚠️ entries before upgrading.

## Prober (prometheus/blackbox_exporter)

## v0.28.0 — 2025-12-06

[Release page](https://github.com/prometheus/blackbox_exporter/releases/tag/v0.28.0)

- ⚠️ **Breaking**: `--log.prober` behavior changed — the scrape probe logger is now a fully independent logger; log leveling was reworked (errors now `ERROR`, many probe logs `DEBUG`; use `--log.prober=debug` to get detailed probe logs back).
- New **Unix domain socket probe** type and **HTTP/3 (QUIC) support** in the HTTP prober (`enable_http3`); gRPC client metadata configuration; byte-exact matching (`expect_bytes`) in TCP query responses.
- Automatic config reload behind feature flags `--config.enable-auto-reload` / `--config.auto-reload-interval` (default 30s); config no longer reloaded when file content is unchanged; default HTTP User-Agent is now RFC 9110 compliant (`Blackbox-Exporter/<version>`).

## v0.27.0 — 2025-06-30

[Release page](https://github.com/prometheus/blackbox_exporter/releases/tag/v0.27.0)

- HTTP probe: support matching JSON response bodies with **CEL expressions**.
- Fixed the condition under which local DNS lookup happens; stopped scrape-logger spam.

## v0.26.0 — 2025-02-26

[Release page](https://github.com/prometheus/blackbox_exporter/releases/tag/v0.26.0)

- ⚠️ **Change**: adopted `log/slog`, dropped `go-kit/log` — log output format changes; log parsers may need updating.
- New metric recording the TLS ciphersuite negotiated during handshake; a way to export labels with content matched by the probe (feeds `probe_expect_info`); reports the certificate serial number.

## v0.25.0 — 2024-04-09

[Release page](https://github.com/prometheus/blackbox_exporter/releases/tag/v0.25.0)

- Probe logs retrievable **per target** (`/logs` endpoint); probe errors are logged explicitly; internal exporter metrics registered explicitly.

## v0.24.0 — 2023-05-16

[Release page](https://github.com/prometheus/blackbox_exporter/releases/tag/v0.24.0)

- `hostname` parameter added for TCP probes; HTTP request body can now be loaded from a file; proxy CONNECT headers made consistent with Prometheus.

## v0.23.0 — 2022-12-02

[Release page](https://github.com/prometheus/blackbox_exporter/releases/tag/v0.23.0)

- ⚠️ **Security**: Exporter Toolkit update fixing CVE-2022-46146 — upgrade from v0.22.0 and earlier.
- Multiple listen addresses and systemd socket activation; `probe_ssl_last_chain_info` metric with leaf certificate details (issuer, subject, SANs, validity); `probe_dns_query_succeeded` metric.

## v0.22.0 — 2022-08-02

[Release page](https://github.com/prometheus/blackbox_exporter/releases/tag/v0.22.0)

- HTTP: new `skip_resolve_phase_with_proxy` option; OAuth2 authenticated requests use the Blackbox Exporter user agent; usage/help printed to stdout instead of stderr.

## v0.21.1 — 2022-06-23

[Release page](https://github.com/prometheus/blackbox_exporter/releases/tag/v0.21.1)

- Fixed a data race in HTTP probes.

## v0.21.0 — 2022-05-30

[Release page](https://github.com/prometheus/blackbox_exporter/releases/tag/v0.21.0)

- ⚠️ Built with Go 1.18: **TLS 1.0/1.1 disabled client-side by default** (override via `min_version` in `tls_config`) and **SHA-1-signed certificates are rejected** (except self-signed roots) — probes of legacy TLS targets may start failing after this upgrade.
- Fixed negative probe timeouts when using small scrape intervals.

## v0.20.0 — 2022-03-16

[Release page](https://github.com/prometheus/blackbox_exporter/releases/tag/v0.20.0)

- gRPC health check support; `hostname` parameter for HTTP probes; `body_size_limit` option for the http module; default user-agent change.

## Helm chart (prometheus-community/helm-charts, `charts/prometheus-blackbox-exporter`)

Chart versions are per-chart tags/releases in the [`helm-charts`](https://github.com/prometheus-community/helm-charts) repository; the chart is also published as `oci://ghcr.io/prometheus-community/charts/prometheus-blackbox-exporter` (the artifact the Flux stack consumes via an OCIRepository).

| Chart version | Release date | Notes |
| ------------- | ------------ | ----- |
| [11.17.2](https://github.com/prometheus-community/helm-charts/releases/tag/prometheus-blackbox-exporter-11.17.2) | 2026-08-10 | `prometheus-config-reloader` bumped to v0.93.1. |
| [11.17.1](https://github.com/prometheus-community/helm-charts/releases/tag/prometheus-blackbox-exporter-11.17.1) | 2026-08-10 | ⚠️ Documents that binary/non-ASCII payloads in `config` are **corrupted by Helm's `toYaml`** — use `configExistingSecretName` (or `secretConfig`) for such module configs. |
| [11.17.0](https://github.com/prometheus-community/helm-charts/releases/tag/prometheus-blackbox-exporter-11.17.0) | 2026-08-10 | ServiceMonitor `apiVersion` made configurable (`serviceMonitor.apiVersion`). |
| [11.16.1](https://github.com/prometheus-community/helm-charts/releases/tag/prometheus-blackbox-exporter-11.16.1) | 2026-08-10 | Helm `tpl` support in generated ServiceMonitors. |
| [11.16.0](https://github.com/prometheus-community/helm-charts/releases/tag/prometheus-blackbox-exporter-11.16.0) | 2026-07-28 | `prometheus-config-reloader` bumped to v0.93.0. |
| [11.15.1](https://github.com/prometheus-community/helm-charts/releases/tag/prometheus-blackbox-exporter-11.15.1) | 2026-06-30 | `prometheus-config-reloader` bumped to v0.92.1. |
| [11.15.0](https://github.com/prometheus-community/helm-charts/releases/tag/prometheus-blackbox-exporter-11.15.0) | 2026-06-29 | `tpl` support in `serviceMonitor.targets` probe target URLs (and `podMonitoring` equivalents). |
| [11.14.0](https://github.com/prometheus-community/helm-charts/releases/tag/prometheus-blackbox-exporter-11.14.0) | 2026-06-29 | NetworkPolicy uses `kubernetes.io/metadata.name` for the monitoring-namespace allow rule. |
| [11.13.0](https://github.com/prometheus-community/helm-charts/releases/tag/prometheus-blackbox-exporter-11.13.0) | 2026-06-18 | Renovate dependency refresh (config-reloader v0.92.0 line). |
| [11.12.0](https://github.com/prometheus-community/helm-charts/releases/tag/prometheus-blackbox-exporter-11.12.0) | 2026-06-12 | Explicit name on the default rule of the Gateway API `HTTPRoute`. |

Notable just below the window: [11.11.0](https://github.com/prometheus-community/helm-charts/releases/tag/prometheus-blackbox-exporter-11.11.0) (2026-06-04) added Gateway API `HTTPRoute` support (`route.main.*` values).
