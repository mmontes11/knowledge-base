---
upstream: https://github.com/prometheus-community/kube-prometheus-stack
last_updated: 2026-08-16
---

# kube-prometheus-stack — features

Feature areas of the `kube-prometheus-stack` chart, each linked to the upstream documentation that covers it. The chart's own [README](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/README.md) and [UPGRADE.md](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/UPGRADE.md) in the [prometheus-community/helm-charts](https://github.com/prometheus-community/helm-charts) monorepo are the authoritative sources.

## Prometheus

- **Deployments and high availability** — a managed `Prometheus` instance with its TSDB on a PVC; `replicas` > 1 yields an HA setup where one instance alerts and the rest replicate state via the [HA peer](https://prometheus.io/docs/prometheus/latest/high-availability-persistent-storage/) mechanism. [README: Prometheus HA](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/README.md#prometheus-high-availability-ha)
- **Agent mode** — `prometheus.agentMode: true` switches the deployment to [Prometheus agent mode](https://github.com/prometheus-operator/prometheus-operator/blob/main/Documentation/prometheus-agent.md): remote-write only, no local TSDB, no rule evaluation.
- **Remote read/write, Thanos sidecar** — `prometheus.remoteReadSpec`, `prometheus.remoteWriteSpec` and the `thanos` sidecar block in the `Prometheus` CR are exposed through values for object-storage and remote-store integration.
- **PrometheusRules admission webhooks** — the operator runs a validating webhook that rejects invalid `PrometheusRule` specs (e.g. malformed PromQL) at admission time; configurable and can be disabled, with documented limitations. [README: admission webhooks](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/README.md#prometheusrules-admission-webhooks)

## Discovery and scrape configuration

- **ServiceMonitor / PodMonitor** — the primary scrape discovery path: declare which Services/Pods to scrape and with what relabeling. [prometheus-operator docs](https://github.com/prometheus-operator/prometheus-operator/tree/main/Documentation)
- **Probe** — blackbox probe configuration for network reachability checks.
- **ScrapeConfig** — pass raw, Kubernetes-independent scrape configuration (`monitoring.coreos.com/v1alpha1`) for static targets and non-k8s service discovery.
- **Default service monitors** — `kubernetesServiceMonitors.enabled` plus per-component toggles (`kubeApiServer`, `kubelet`, `kubeControllerManager`, `coreDns`, `kubeEtcd`, `kubeScheduler`, `kubeProxy`) ship ready-made monitors for the core cluster components.

## Alerting and rules

- **Default rule bundles** — a curated set of `PrometheusRule`s covering kube-api-server availability/burn-rate/SLOs, node health, kubelet, controller-manager, scheduler, proxy, etcd, config-reloaders, networking, Prometheus and operator self-monitoring; every logical group is individually toggleable and overridable under `defaultRules.rules.*`.
- **Custom rules** — `customRules` (plain list) and `additionalPrometheusRulesMap` (per-rule name for idempotent upgrades) inject operator-defined alerts.
- **Alertmanager** — a managed `Alertmanager` cluster; global `AlertmanagerConfig` objects are supported for per-namespace routing and receivers, and `alertmanager.config` / `templateFiles` allow fully custom configuration and templates. Since chart 88.3.0 the `externalUrl` supports TLS for HTTPS fronting.

## Visualization

- **Grafana dashboards** — the chart provisions a large collection of curated dashboards (node, kube-state-metrics, apiserver, controller-manager, scheduler, etcd, Prometheus, Alertmanager, Thanos, …) rendered from the [Prometheus mixins](https://github.com/kubernetes-monitoring/kubernetes-mixin) and loaded into the bundled Grafana via ConfigMaps. [README: Grafana Dashboards](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/README.md#grafana-dashboards)

## Long-term storage

- **Thanos Ruler** — `thanosRuler.enabled: true` provisions a `ThanosRuler` CR and StatefulSet for rule evaluation against object storage.
- **Thanos sidecar** — `prometheus.thanos.*` enables the sidecar on the `Prometheus` CR for querying long-term data.

## Windows workloads

- `windowsMonitoring.enabled: true` deploys `prometheus-windows-exporter` on Windows nodes, with its own service monitor dashboards. [README](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/README.md#usage)

## Operational patterns

- **Zero-downtime upgrades** — [documented procedure](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/README.md#zero-downtime) to upgrade multiple releases of the chart without dropping monitoring coverage (scale replicas on the second release first, then upgrade in order).
- **Multiple releases** — the chart is designed to be installed more than once (e.g. one release for the operator + rules, another for the Prometheus/Alertmanager deployment) by disabling components per release. [README: multiple releases](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/README.md#multiple-releases)
- **CRD upgrades** — `crds.upgradeJob.enabled: true` (preview) applies the CRDs in a Helm pre-install/pre-upgrade job so a chart upgrade can follow without manual CRD steps; `forceConflicts` takes over ownership when the CRDs were installed by another manager. [UPGRADE.md](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/UPGRADE.md)
- **Work-around for private GKE clusters** — [documented workaround](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/README.md#work-arounds-for-known-issues) for the well-known GKE private-cluster metrics gap.
