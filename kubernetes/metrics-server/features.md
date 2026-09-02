---
upstream: https://github.com/kubernetes-sigs/metrics-server
last_updated: 2026-08-23
---

# metrics-server — features

Significant feature areas, each linked to the upstream documentation covering it. The [README](https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md) and the [Metrics Server design doc](https://github.com/kubernetes/design-proposals-archive/blob/main/instrumentation/metrics-server.md) are authoritative.

## Autoscaling pipelines

- **Horizontal Pod Autoscaler / Vertical Pod Autoscaler**: serves CPU/memory per-pod metrics to the built-in autoscalers over the [Metrics API](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/). Metrics Server is sized at ~1 millicore and 2 MiB per node and supports up to 5,000-node clusters. [README](https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md)
- **`kubectl top`**: the same API is what `kubectl top nodes|pods` queries, making it the quick debug path into an autoscaling pipeline. [Metrics Server design](https://github.com/kubernetes/design-proposals-archive/blob/main/instrumentation/metrics-server.md)
- **Out-of-scope by design**: explicit caution that it must not be used to feed monitoring systems — those should scrape the Kubelet `/metrics/resource` endpoint (cAdvisor) directly. [README — Use cases](https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#use-cases)

## Metrics collection

- **Kubelet scraping**: collects container CPU/memory from each node's Kubelet `/metrics/resource` (cAdvisor/cRI data) — see [container metrics RPCs](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-node/cri-container-stats.md).
- **15-second resolution**: fast autoscaling via a short collection interval by default (`--metric-resolution=15s`), cached in memory — there is no backend store and metrics are short-lived. [README — Design](https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#design)
- **Kubelet address/port tuning**: `--kubelet-preferred-address-types` (default `InternalIP,ExternalIP,Hostname`), `--kubelet-request-timeout` (v0.7.0+), `--node-selector` to exclude nodes by label (v0.7.0+), and per-node override of the metrics path via the `metrics.k8s.io/resource-metrics-path` node annotation (v0.7.0+). [README — Configuration](https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#configuration)

## Aggregated API serving

- **`APIService` registration**: exposes `metrics.k8s.io/v1beta1` (`NodeMetrics`, `PodMetrics`) to the kube-apiserver through its [aggregation layer](https://kubernetes.io/docs/tasks/access-kubernetes-api/configure-aggregation-layer/) — the component must be reachable from the control plane (Port 10250 on the pod, or node IP with `hostNetwork`). [Metrics API spec](https://github.com/kubernetes/metrics)
- **TLS/cert-manager modes**: the chart supports four `tls.type` values — `metrics-server` (self-signed), `helm` (`genSelfSignedCert`, lookup-persistent), `cert-manager` (with optional `cert-manager.io/inject-ca-from` APIService annotation), and `existingSecret` (bring your own Secret) — plus `apiService.caBundle`/`insecureSkipTLSVerify` for the kube-apiserver side. [chart README — TLS](https://github.com/kubernetes-sigs/metrics-server/blob/master/charts/metrics-server/README.md)
- **Secure serving options** (v0.8.0+): standard `SecureServingOptions` wired to flags, including `--disable-http2-serving` to opt out of HTTP/2 on the serving port. [v0.8.0 release](https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.8.0)

## High availability

- **HA deployment**: run `replicas > 1` (chart `replicas` value or the `high-availability*.yaml` manifests) on a ≥2-node cluster; recommended companion flag is `--enable-aggregator-routing=true` on the kube-apiserver so requests load-balance across replicas. [README — High Availability](https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#high-availability)
- **PodDisruptionBudget**: chart `podDisruptionBudget` block (`minAvailable`/`maxUnavailable`, and `unhealthyPodEvictionPolicy` since chart 3.13.0). [chart README](https://github.com/kubernetes-sigs/metrics-server/blob/master/charts/metrics-server/README.md)

## Security

- **Cluster-scoped RBAC**: `system:<metrics-server>` ClusterRole/ClusterRoleBinding (nodes/metrics get, pods/nodes/namespaces/configmaps read), `system:auth-delegator` binding, and an *aggregated reader* ClusterRole so other components can opt in to read `metrics.k8s.io`. [manifests](https://github.com/kubernetes-sigs/metrics-server/blob/master/deploy)
- **Pod Security "restricted" compatible** manifests (v0.7.0+), with the caveat that `CAP_NET_BIND_SERVICE` must be allowed for privileged-port binding. [README — Security context](https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#security-context)
- **Request authentication**: served through the kube-apiserver with standard authn/authz; `--requestheader-client-ca-file` controls request-header CA verification. [README — Configuration](https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#configuration)
- **CVE-2025-47906 / CVE-2025-47907** fixed in v0.9.0 via the Go toolchain bump. [v0.9.0 release](https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.9.0)

## Observability

- **Component self-metrics**: exposes its own `/metrics` endpoint (scrape failures, cache state, etc.); chart `metrics.enabled` + optional `ServiceMonitor` (Prometheus Operator, `monitoring.coreos.com/v1`) for it. [chart `servicemonitor.yaml`](https://github.com/kubernetes-sigs/metrics-server/blob/master/charts/metrics-server/templates/servicemonitor.yaml)
- **JSON logging**: structured JSON log output since v0.7.0. [v0.7.0 release](https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.7.0)

## Scaling and sizing

- **Default resource requests**: 100m CPU / 200MiB (sane up to ~100 nodes, 70 pods/node, 100 HPAs — the [Scalability Envelope](https://github.com/kubernetes/community/blob/master/sig-scalability/configs-and-limits/thresholds.md)); scale +1m CPU and +2MiB per extra node above 100. [README — Scaling](https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#scaling)
- **Addon Resizer**: optional sidecar (`addonResizer.enabled`) that auto-scales the Deployment's resource requests with cluster size, with `nanny` tuning (threshold, minClusterSize, pollPeriod). [chart README](https://github.com/kubernetes-sigs/metrics-server/blob/master/charts/metrics-server/README.md)

## Installation and compatibility

- **Two install surfaces**: raw YAML (`components.yaml`, `high-availability*.yaml` per release) or the official [Helm chart](https://artifacthub.io/packages/helm/metrics-server/metrics-server) (this stack: Flux `HelmRelease`, chart repo `https://kubernetes-sigs.github.io/metrics-server`). [README — Installation](https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#installation)
- **Compatibility matrix**: component ↔ Kubernetes support ranges (0.9.x → 1.34+, 0.8.x → 1.31+, 0.7.x → 1.27+, 0.6.x → 1.25+), with `--authorization-always-allow-paths=/livez,/readyz` for clusters <1.16. [README — Compatibility Matrix](https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#compatibility-matrix)
- **Troubleshooting**: dedicated [FAQ](https://github.com/kubernetes-sigs/metrics-server/blob/master/FAQ.md) and [Known issues](https://github.com/kubernetes-sigs/metrics-server/blob/master/KNOWN_ISSUES.md).
