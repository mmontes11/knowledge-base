---
upstream: https://github.com/prometheus-community/kube-prometheus-stack
last_updated: 2026-08-16
---

# kube-prometheus-stack — releases

Latest 10 official chart releases of `kube-prometheus-stack`, newest first. Release pages live on the [prometheus-community/helm-charts](https://github.com/prometheus-community/helm-charts) monorepo. The version deployed in `mmontes11/k8s-infrastructure` (`infrastructure/prometheus/kube-prometheus-stack-ocirepository.yaml`) is `85.1.3`, so entries below cover the 85 → 88 gap.

Chart release notes are auto-generated from dependency bumps; treat the chart's [UPGRADE.md](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/UPGRADE.md) as the authoritative source for upgrade requirements.

## 88.5.3 — 2026-08-21
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-88.5.3)
- Bump `jkroepke/kube-webhook-certgen` (used by the admission webhooks) to `v1.8.7`.

## 88.5.2 — 2026-08-20
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-88.5.2)
- Bump `oauth2-proxy` to `v7.15.4`.

## 88.5.1 — 2026-08-20
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-88.5.1)
- Non-major dependency updates.

## 88.5.0 — 2026-08-18
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-88.5.0)
- Bump `kube-state-metrics` dependency to v8.4.0.

## 88.4.0 — 2026-08-18
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-88.4.0)
- Non-major dependency updates.

## 88.3.0 — 2026-08-11
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-88.3.0)
- `externalUrl` now supports TLS configuration, so Prometheus/Alertmanager/Grafana external URLs served over HTTPS from an ingress work out of the box.

## 88.2.0 — 2026-08-07
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-88.2.0)
- Bump `kube-state-metrics` dependency to v8.2.0.

## 88.1.6 — 2026-08-07
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-88.1.6)
- Non-major dependency updates.

## 88.1.5 — 2026-08-05
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-88.1.5)
- Bump `grafana` dependency to v12.10.3.

## 88.1.4 — 2026-08-04
[Release page](https://github.com/prometheus-community/helm-charts/releases/tag/kube-prometheus-stack-88.1.4)
- Non-major dependency updates.

## Breaking changes and operational pitfalls

Upgrading the deployed `85.1.3` to any 86+ release crosses three operator-bumping majors. Each one requires **updating the prometheus-operator CRDs before upgrading the chart**, because Helm cannot modify the schema of already-applied CRDs. Since chart `68.4.0` this can be done by setting `crds.upgradeJob.enabled: true`; otherwise apply the CRD manifests by hand. Source: [UPGRADE.md](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/UPGRADE.md)

- **85.x → 86.x**: upgrades prometheus-operator to `v0.91.0`; apply the `v0.91.0` `monitoring.coreos.com_*` CRDs first.
- **86.x → 87.x**: upgrades prometheus-operator to `v0.92.0`; apply the `v0.92.0` CRDs first.
- **87.x → 88.x**: upgrades prometheus-operator to `v0.93.0`; apply the `v0.93.0` CRDs first.
- **84.x → 85.x** (already in effect at the deployed version): the `distroless` variants of the `prometheus` and `prometheus-node-exporter` images became the default. If images are synced to a private registry, sync the tags carrying the `-distroless` suffix or the upgrade will fail on image pull.
- Because this cluster installs CRDs via the separate `prometheus-operator-crds` HelmRelease, a chart upgrade also has to keep that release in step (upgrade it to the matching operator version *before* the main HelmRelease).
