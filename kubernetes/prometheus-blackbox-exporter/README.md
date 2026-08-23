---
upstream: https://github.com/prometheus/blackbox_exporter
last_updated: 2026-08-23
---

# prometheus-blackbox-exporter

Blackbox prober daemon that lets Prometheus probe endpoints from the outside — without any agent installed — over HTTP/HTTPS (HTTP/3 included since v0.28.0), DNS, TCP, ICMP, gRPC, WebSocket, and (since v0.28.0) Unix domain sockets. It implements the [multi-target exporter pattern](https://prometheus.io/docs/guides/multi-target-exporter/): Prometheus scrapes the `/probe` endpoint with a `target` and `module` parameter, and the exporter returns the probe as metrics (`probe_success`, timings, status codes, TLS details). Modules — named probe definitions combining a prober type with its settings and timeout — come from a YAML configuration file that supports runtime reload.

> **Naming note:** this folder documents the `prometheus-blackbox-exporter` Helm chart and the prober it deploys. There is no `prometheus-community/prometheus-blackbox-exporter` repository; the prober's upstream is [`prometheus/blackbox_exporter`](https://github.com/prometheus/blackbox_exporter) (the chart's [`Chart.yaml`](https://github.com/prometheus-community/helm-charts/blob/main/charts/prometheus-blackbox-exporter/Chart.yaml) names it as `home`/`sources`), and the chart itself lives in [`prometheus-community/helm-charts`](https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus-blackbox-exporter).

- Upstream repository: https://github.com/prometheus/blackbox_exporter
- Helm chart: https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus-blackbox-exporter (published as `oci://ghcr.io/prometheus-community/charts/prometheus-blackbox-exporter`)
- Documentation: [README](https://github.com/prometheus/blackbox_exporter/blob/master/README.md), [CONFIGURATION.md](https://github.com/prometheus/blackbox_exporter/blob/master/CONFIGURATION.md), [Prometheus blackbox exporter docs](https://prometheus.io/docs/guides/blackbox-exporter/)
- License: Apache-2.0 (per [LICENSE](https://github.com/prometheus/blackbox_exporter/blob/master/LICENSE))
- Prober image: `quay.io/prometheus/blackbox-exporter`
- Chart defaults: `appVersion: v0.28.0`, `kubeVersion: ">=1.21.0-0"` (per [Chart.yaml](https://github.com/prometheus-community/helm-charts/blob/main/charts/prometheus-blackbox-exporter/Chart.yaml))

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
