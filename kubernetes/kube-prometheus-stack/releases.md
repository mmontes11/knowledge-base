---
upstream: https://github.com/prometheus-community/helm-charts
last_updated: 2026-09-08
---

# kube-prometheus-stack — releases

Latest 10 official chart releases of `kube-prometheus-stack`, newest first. Release pages live on the [prometheus-community/helm-charts](https://github.com/prometheus-community/helm-charts) monorepo (chart at `charts/kube-prometheus-stack`). The version deployed in `mmontes11/k8s-infrastructure` (`infrastructure/prometheus/kube-prometheus-stack-ocirepository.yaml`) is `85.1.3`, so entries below cover the 85 → 90 upgrade path.

Chart release notes are auto-generated from dependency bumps; treat the chart's [UPGRADE.md](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/UPGRADE.md) as the authoritative source for upgrade requirements.

## 90.0.0 — 2026-09-06
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-90.0.0)
- **Breaking:** control-plane `ServiceMonitor`s (kubelet, kube-apiserver, kube-controller-manager, kube-scheduler, kube-etcd, kube-proxy, coredns, kube-dns) no longer reference credentials on the scraper's filesystem; the bearer token now comes from a Secret through `authorization` and the CA from the `kube-root-ca.crt` ConfigMap. The chart now creates a long-lived `kubernetes.io/service-account-token` Secret named `<prometheus SA>-token` (`prometheus.serviceAccount.createTokenSecret`, default `true`), and values such as `bearerTokenFile`, `insecureSkipVerify`, `serverName`, and `caSecret` are removed in favor of the components' `authorization`/`tlsConfig` blocks. [UPGRADE.md](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/UPGRADE.md)

## 89.2.4 — 2026-09-06
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-89.2.4)
- Synchronize common files from `prometheus/prometheus`; bump `grafana` image to `v13.2.2`.

## 89.2.3 — 2026-09-06
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-89.2.3)
- Bump `kube-state-metrics` image to `v8.4.2`.

## 89.2.2 — 2026-09-04
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-89.2.2)
- Bump `grafana` chart to v13.2.1.

## 89.2.1 — 2026-09-04
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-89.2.1)
- Set a default `commonName` for the Prometheus certificate.

## 89.2.0 — 2026-09-04
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-89.2.0)
- Bump `grafana` chart to v13.2.0.

## 89.1.0 — 2026-09-03
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-89.1.0)
- Non-major dependency updates.

## 89.0.0 — 2026-09-03
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-89.0.0)
- **Breaking (dependency):** upgrades the `grafana` Helm chart to v13.0.0; check the [Grafana Helm Chart upgrade guide](https://github.com/grafana-community/helm-charts/tree/main/charts/grafana#to-1300) before upgrading.

## 88.6.5 — 2026-09-03
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-88.6.5)
- Make the cert-manager private key configurable.

## 88.6.4 — 2026-09-03
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-88.6.4)
- Document the Grafana dashboard folder options.

## Breaking changes and operational pitfalls

Upgrading the deployed `85.1.3` to any 86+ release crosses three operator-bumping majors, plus two further breaking chart majors: 89 (Grafana chart v13) and 90 (control-plane credential rework). Each of the operator majors requires **updating the prometheus-operator CRDs before upgrading the chart**, because Helm cannot modify the schema of already-applied CRDs. Since chart `68.4.0` this can be done by setting `crds.upgradeJob.enabled: true`; otherwise apply the CRD manifests by hand. Source: [UPGRADE.md](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/UPGRADE.md)

- **85.x → 86.x**: upgrades prometheus-operator to `v0.91.0`; apply the `v0.91.0` `monitoring.coreos.com_*` CRDs first.
- **86.x → 87.x**: upgrades prometheus-operator to `v0.92.0`; apply the `v0.92.0` CRDs first.
- **87.x → 88.x**: upgrades prometheus-operator to `v0.93.0`; apply the `v0.93.0` CRDs first.
- **88.x → 89.x**: upgrades the `grafana` Helm chart to v13.0.0; check the [Grafana Helm Chart upgrade guide](https://github.com/grafana-community/helm-charts/tree/main/charts/grafana#to-1300).
- **89.x → 90.x**: control-plane `ServiceMonitor` credentials move off the scraper's filesystem — bearer token via a Secret through `authorization`, CA via the `kube-root-ca.crt` ConfigMap. The chart renders a long-lived `<prometheus SA>-token` Secret (`prometheus.serviceAccount.createTokenSecret`, default `true`); rendering fails if `prometheus.enabled` or `prometheus.serviceAccount.create` is `false` while control-plane scraping is enabled, and external-scraper setups must point each component's `authorization` at their own credential.
- **84.x → 85.x** (already in effect at the deployed version): the `distroless` variants of the `prometheus` and `prometheus-node-exporter` images became the default. If images are synced to a private registry, sync the tags carrying the `-distroless` suffix or the upgrade will fail on image pull.
- Because this cluster installs CRDs via the separate `prometheus-operator-crds` HelmRelease, a chart upgrade also has to keep that release in step (upgrade it to the matching operator version *before* the main HelmRelease).
