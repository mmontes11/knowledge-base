---
upstream: https://github.com/prometheus-community/helm-charts
last_updated: 2026-10-01
---

# kube-prometheus-stack — releases

Latest 10 official chart releases of `kube-prometheus-stack`, newest first. Release pages live on the [prometheus-community/helm-charts](https://github.com/prometheus-community/helm-charts) monorepo (chart at `charts/kube-prometheus-stack`). The version deployed in `mmontes11/k8s-infrastructure` (`infrastructure/prometheus/kube-prometheus-stack-ocirepository.yaml`) is `85.1.3`, so entries below cover the 85 → 91 upgrade path.

Chart release notes are auto-generated from dependency bumps; treat the chart's [UPGRADE.md](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/UPGRADE.md) as the authoritative source for upgrade requirements.

## 91.8.2 — 2026-09-29
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-91.8.2)
- Bump `grafana` image to `v13.2.7`.

## 91.8.1 — 2026-09-28
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-91.8.1)
- New `nodeExporter.prometheusScrape` value, default `false`: the node-exporter service is no longer scraped via `prometheus.io/scrape` annotations by default, since the ServiceMonitor already covers it ([#7306](https://github.com/prometheus-community/helm-charts/pull/7306)).

## 91.8.0 — 2026-09-27
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-91.8.0)
- Bump `prometheus-node-exporter` image to `v4.59.0`.

## 91.7.1 — 2026-09-27
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-91.7.1)
- Bump `grafana` image to `v13.2.6`.

## 91.7.0 — 2026-09-26
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-91.7.0)
- Bump `prometheus-node-exporter` image to `v4.58.0`.

## 91.6.0 — 2026-09-26
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-91.6.0)
- Bump `kube-state-metrics` image to `v8.6.0`.

## 91.5.3 — 2026-09-25
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-91.5.3)
- Non-major dependency updates.

## 91.5.2 — 2026-09-24
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-91.5.2)
- Allow the prometheus-operator to adjust finalizers on the `ConfigMap`/`Secret` objects it owns ([#7295](https://github.com/prometheus-community/helm-charts/pull/7295)).

## 91.5.1 — 2026-09-23
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-91.5.1)
- Bump prometheus-operator to `v0.94.1`.

## 91.5.0 — 2026-09-22
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-91.5.0)
- Non-major dependency updates.

## Breaking changes and operational pitfalls

Upgrading the deployed `85.1.3` to any 86+ release crosses four operator-bumping majors (86, 87, 88 and 91), plus two further breaking chart majors: 89 (Grafana chart v13) and 90 (control-plane credential rework). Each of the operator majors requires **updating the prometheus-operator CRDs before upgrading the chart**, because Helm cannot modify the schema of already-applied CRDs. Since chart `68.4.0` this can be done by setting `crds.upgradeJob.enabled: true`; otherwise apply the CRD manifests by hand. Source: [UPGRADE.md](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/UPGRADE.md)

- **85.x → 86.x**: upgrades prometheus-operator to `v0.91.0`; apply the `v0.91.0` `monitoring.coreos.com_*` CRDs first.
- **86.x → 87.x**: upgrades prometheus-operator to `v0.92.0`; apply the `v0.92.0` CRDs first.
- **87.x → 88.x**: upgrades prometheus-operator to `v0.93.0`; apply the `v0.93.0` CRDs first.
- **88.x → 89.x**: upgrades the `grafana` Helm chart to v13.0.0; check the [Grafana Helm Chart upgrade guide](https://github.com/grafana-community/helm-charts/tree/main/charts/grafana#to-1300).
- **89.x → 90.x**: control-plane `ServiceMonitor` credentials move off the scraper's filesystem — bearer token via a Secret through `authorization`, CA via the `kube-root-ca.crt` ConfigMap. The chart renders a long-lived `<prometheus SA>-token` Secret (`prometheus.serviceAccount.createTokenSecret`, default `true`); rendering fails if `prometheus.enabled` or `prometheus.serviceAccount.create` is `false` while control-plane scraping is enabled, and external-scraper setups must point each component's `authorization` at their own credential.
- **90.x → 91.x**: upgrades prometheus-operator to `v0.94.0`; apply the `v0.94.0` `monitoring.coreos.com_*` CRDs first. The operator's `ClusterRole` no longer grants wildcard verbs, following the tightened upstream RBAC.
- **84.x → 85.x** (already in effect at the deployed version): the `distroless` variants of the `prometheus` and `prometheus-node-exporter` images became the default. If images are synced to a private registry, sync the tags carrying the `-distroless` suffix or the upgrade will fail on image pull.
- Because this cluster installs CRDs via the separate `prometheus-operator-crds` HelmRelease, a chart upgrade also has to keep that release in step (upgrade it to the matching operator version *before* the main HelmRelease).
