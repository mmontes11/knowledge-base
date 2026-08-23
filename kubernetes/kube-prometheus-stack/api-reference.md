---
upstream: https://github.com/prometheus-community/kube-prometheus-stack
last_updated: 2026-08-16
---

# kube-prometheus-stack — API reference

The chart is the Helm wrapper around the [prometheus-operator](https://github.com/prometheus-operator/prometheus-operator). Its primary Kubernetes surface is the set of **10 custom resource kinds** under API group/version **`monitoring.coreos.com`** that define the monitoring stack declaratively.

## prometheus-operator CRDs

All 10 kinds are **Namespaced**. Served/storage versions below were verified against the prometheus-operator `v0.91.0` CRDs (the generation installed in this cluster via the `prometheus-operator-crds` chart) and against the live cluster.

| Kind | Short name | Served / Storage | Purpose | Upstream docs |
| --- | --- | --- | --- | --- |
| `Alertmanager` | `am` | v1 | A highly available Alertmanager cluster managed by the operator. | [Prometheus operator kinds](https://github.com/prometheus-operator/prometheus-operator) |
| `AlertmanagerConfig` | `amcfg` | v1alpha1 | Per-namespace routing, inhibition and receiver configuration consumed by an `Alertmanager` instance. | [prometheus-operator docs](https://github.com/prometheus-operator/prometheus-operator) |
| `PodMonitor` | `pmon` | v1 | Declares which Pods are scraped, directly (independent of a Service). | [ServiceMonitor/PodMonitor docs](https://github.com/prometheus-operator/prometheus-operator/tree/main/Documentation) |
| `Probe` | `prb` | v1 | Blackbox probing of a set of targets via a blackbox exporter module. | [Probe docs](https://github.com/prometheus-operator/prometheus-operator/tree/main/Documentation) |
| `Prometheus` | `prom` | v1 | A Prometheus server instance: scrape config, rules, HA shard, Thanos sidecar, volume configuration. | [Prometheus CRD docs](https://github.com/prometheus-operator/prometheus-operator/blob/main/Documentation/custom resources/prometheus.md) |
| `PrometheusAgent` | `promagent` | v1alpha1 | Prometheus in [agent mode](https://github.com/prometheus-operator/prometheus-operator/blob/main/Documentation/prometheus-agent.md) (remote-write only, no local TSDB). | [Prometheus agent docs](https://github.com/prometheus-operator/prometheus-operator/blob/main/Documentation/prometheus-agent.md) |
| `PrometheusRule` | `promrule` | v1 | Alerting and recording rules for a `Prometheus` instance. | [PrometheusRule docs](https://github.com/prometheus-operator/prometheus-operator/blob/main/Documentation/custom rules/prometheusrule.md) |
| `ScrapeConfig` | `scfg` | v1alpha1 | Declarative, non-Kubernetes-specific scrape configuration (static targets, service discovery, relabeling). | [ScrapeConfig docs](https://github.com/prometheus-operator/prometheus-operator/blob/main/Documentation/scrapeconfigs.md) |
| `ServiceMonitor` | `smon` | v1 | Declares which Services are scraped, selected by labels; the primary discovery mechanism. | [ServiceMonitor docs](https://github.com/prometheus-operator/prometheus-operator/blob/main/Documentation/custom resources/servicemonitor.md) |
| `ThanosRuler` | `ruler` | v1 | A Thanos Ruler: evaluates Prometheus rules and publishes them to object storage. | [ThanosRuler docs](https://github.com/prometheus-operator/prometheus-operator/blob/main/Documentation/custom resources/thanosruler.md) |

In the live cluster these CRDs are installed by the separate `prometheus-operator-crds` HelmRelease (operator version `0.91.0` annotation). Because the chart would otherwise re-manage the same CRDs, the main release is deployed with `crds.enabled: false`.

## Objects the chart itself creates

Beyond the CRDs, a default installation renders the following managed objects (all in the release namespace, `monitoring` in this cluster):

- A `Prometheus` CR (`kube-prometheus-stack-prometheus`) and its backing StatefulSet with a PVC for the TSDB; a corresponding `Alertmanager` CR and StatefulSet; a `prometheus-operator` Deployment.
- `ServiceMonitor` objects for the core cluster components and for the chart's own exporters. Which of these are enabled by default is controlled by top-level values keys: `kubeApiServer`, `kubelet`, `kubeControllerManager`, `coreDns` (with a legacy `kubeDns` alias, off by default), `kubeEtcd`, `kubeScheduler`, `kubeProxy`, `kubeStateMetrics`, `nodeExporter`, plus self-monitors for `prometheus`, `alertmanager` and `thanosRuler`. Enabling `kubernetesServiceMonitors.enabled` turns on the core cluster set.
- `PrometheusRule` objects for the default rule groups (see [Releases](releases.md) and the values `defaultRules.rules.*` toggles; `alertmanager`, `configReloaders`, `etcd`, `general`, `kubeApiserverAvailability/Burnrate/Histogram/Slos`, `kubeControllerManager`, `kubelet`, `kubeProxy`, `kubePrometheusGeneral`, `kubePrometheusNodeRecording`, `kubernetesApps`, `kubernetesResources`, `kubernetesStorage`, `kubernetesSystem`, `kubeSchedulerAlerting`, `kubeSchedulerRecording`, `kubeStateMetrics`, `network`, `node`, `nodeExporterAlerting`, `nodeExporterRecording`, `prometheus`, `prometheusOperator`, `windows`).
- Grafana dashboards loaded via ConfigMaps (rendered from `charts/kube-prometheus-stack/templates/grafana/`, sourced from upstream Prometheus mixins).
- Optional `ThanosRuler` CR and StatefulSet when `thanosRuler.enabled: true`.

Field-level schema for the CRDs is intentionally not duplicated here; refer to the per-kind upstream links and the [prometheus-operator API reference](https://github.com/prometheus-operator/prometheus-operator/tree/main/Documentation). The chart's own configuration surface is the [values file](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/values.yaml).
