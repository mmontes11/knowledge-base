---
upstream: https://github.com/prometheus-community/kube-prometheus-stack
last_updated: 2026-08-16
---

# kube-prometheus-stack

[kube-prometheus-stack](https://github.com/prometheus-community/kube-prometheus-stack) is a Helm chart that bundles the [kube-prometheus](https://github.com/prometheus-operator/kube-prometheus) stack into a single release: Prometheus, prometheus-operator, Alertmanager, Grafana, kube-state-metrics, and node_exporter, together with a curated set of default `PrometheusRule`s and Grafana dashboards to provide end-to-end Kubernetes cluster monitoring.

- Upstream repository: [prometheus-community/kube-prometheus-stack](https://github.com/prometheus-community/kube-prometheus-stack)
- Chart sources: the chart now lives at `charts/kube-prometheus-stack` in the [prometheus-community/helm-charts](https://github.com/prometheus-community/helm-charts) monorepo. The standalone repository above no longer exists (verified 2026-08-16): all links below point at the live monorepo location.
- Distributed as: OCI artifact `oci://ghcr.io/prometheus-community/charts/kube-prometheus-stack` (also via the `https://prometheus-community.github.io/helm-charts` Helm repository)
- Chart homepage / project: [https://github.com/prometheus-operator/kube-prometheus](https://github.com/prometheus-operator/kube-prometheus)
- License: Apache-2.0
- Chart `appVersion`: `v0.93.1` (prometheus-operator), `kubeVersion >=1.25.0-0`
- API group/version: `monitoring.coreos.com` (10 custom resource kinds, see [API reference](api-reference.md))

This entry documents the API surface and feature set of the chart as deployed by `mmontes11/k8s-infrastructure` in `infrastructure/prometheus/` (the `kube-prometheus-stack` HelmRelease in the `monitoring` namespace, pinned to chart version `85.1.3`). The prometheus-operator CRDs are installed there by a separate `prometheus-operator-crds` HelmRelease, so the main release runs with `crds.enabled: false`.

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
