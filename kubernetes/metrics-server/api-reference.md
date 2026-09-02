---
upstream: https://github.com/kubernetes-sigs/metrics-server
last_updated: 2026-08-23
---

# metrics-server — API reference

Metrics Server is not a CRD project. It is a cluster component that **serves the `metrics.k8s.io/v1beta1` API group** to the kube-apiserver through an `APIService` (aggregated API), and it exposes a fixed set of Kubernetes surfaces via its YAML manifests and the [official Helm chart](https://github.com/kubernetes-sigs/metrics-server/tree/master/charts/metrics-server). The Metrics API group itself is defined by the [Metrics API KEP/spec](https://github.com/kubernetes/metrics) and the [Metrics Server design doc](https://github.com/kubernetes/design-proposals-archive/blob/main/instrumentation/metrics-server.md).

## Served API (via kube-apiserver aggregation)

| Kind | API version | Purpose | Upstream API docs |
| ---- | ----------- | ------- | ----------------- |
| `NodeMetrics` | `metrics.k8s.io/v1beta1` | Per-node CPU and memory usage (total, per node). Read by kube-scheduler (ResourceScoring), HPA, and `kubectl top nodes`. | [Metrics API spec — NodeMetrics](https://github.com/kubernetes/metrics/blob/master/docs/node-metrics-api.md) |
| `PodMetrics` | `metrics.k8s.io/v1beta1` | Per-pod, per-container CPU and memory usage. Read by HPA, `kubectl top pods`, and the Kubernetes Scheduler. | [Metrics API spec — PodMetrics](https://github.com/kubernetes/metrics/blob/master/docs/metrics-api.md) |

- The group is registered by an `APIService` named `v1beta1.metrics.k8s.io` (`apiregistration.k8s.io/v1`), pointing at the metrics-server `Service`. Verify readiness with `kubectl get apiservice v1beta1.metrics.k8s.io` (look for `Available: True`); `kubectl get --raw "/apis/metrics.k8s.io/v1beta1"` should return `status: Success`.
- Metrics are read-only and short-lived (kept in memory, 15 s resolution by default); there is no custom storage.

## Kubernetes surfaces exposed by the manifests / Helm chart

| Surface | API version / kind | Purpose | Upstream docs |
| ------- | ------------------ | ------- | ------------- |
| Metrics API registration | `apiregistration.k8s.io/v1` `APIService` `v1beta1.metrics.k8s.io` | Registers the `metrics.k8s.io/v1beta1` group with the kube-apiserver aggregation layer; carries `caBundle` and (by default) `insecureSkipTLSVerify`. | [chart `apiservice.yaml`](https://github.com/kubernetes-sigs/metrics-server/blob/master/charts/metrics-server/templates/apiservice.yaml) |
| Metrics Server workload | `apps/v1` `Deployment` (single replica by default; `replicas>1` for HA) | Runs the metrics-server binary, scrapes Kubelets, serves the API over HTTPS on container port 10250. | [HA guide](https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#high-availability) |
| Metrics service | `v1` `Service` (ClusterIP, port 443 → 10250) | Front for the `APIService`; the kube-apiserver proxies group requests here. Add `kubernetes.io/cluster-service`/`kubernetes.io/name` labels to surface it in `kubectl cluster-info`. | [chart README](https://github.com/kubernetes-sigs/metrics-server/blob/master/charts/metrics-server/README.md) |
| RBAC | `rbac.authorization.k8s.io/v1` `ClusterRole`/`ClusterRoleBinding` (`system:<metrics-server>`, auth-delegator, aggregated reader) | Grants `get` on `nodes/metrics`, `list`/`watch` on `nodes`, `pods`, `namespaces`, `configmaps`; an aggregated-reader role lets other tools read the metrics API; an auth-delegator binding enables TokenReview `system:auth-delegator`. | [manifests](https://github.com/kubernetes-sigs/metrics-server/tree/master/deploy) |
| TLS / certificates | `v1` `Secret`; optional `cert-manager.io/v1` `Certificate` | Serves the API over TLS and backs the `APIService` `caBundle`. Four `tls.type` modes: `metrics-server` (self-signed, default), `helm` (Helm `genSelfSignedCert`, lookup-aware), `cert-manager` (issuer-driven, CA-injector annotation), `existingSecret` (bring your own). | [chart README — TLS](https://github.com/kubernetes-sigs/metrics-server/blob/master/charts/metrics-server/README.md) |
| Kubelet scraping endpoint | Kubelet `/metrics/resource` on each node (cAdvisor data) | The actual data source; requires Webhook authn/authz enabled on the Kubelet and a CA-signed Kubelet serving cert (or `--kubelet-insecure-tls`). Not a typed object — the component's input surface. | [Requirements](https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#requirements), [cAdvisor](https://github.com/google/cadvisor) |
| Observability (optional) | `monitoring.coreos.com/v1` `ServiceMonitor` | Prometheus Operator scrape of metrics-server's own `/metrics` (component self-metrics, not the served resource metrics). | [chart `servicemonitor.yaml`](https://github.com/kubernetes-sigs/metrics-server/blob/master/charts/metrics-server/templates/servicemonitor.yaml) |
| Sizing / HA (optional) | `policy/v1` `PodDisruptionBudget`; Addon Resizer sidecar | `podDisruptionBudget` block for HA deployments; optional `addonResizer` container that scales the Deployment's resource requests with cluster size. | [Scaling](https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#scaling) |

Notes:

- There is **no** `metrics.k8s.io/v1` or any other version; `v1beta1` is the only served version and `NodeMetrics`/`PodMetrics` are its only kinds.
- The served API is a separate project from the component: the group/version is defined in [kubernetes/metrics](https://github.com/kubernetes/metrics); metrics-server is one possible implementation of it.
- Compatibility matrix (component ↔ Kubernetes): `0.9.x` → 1.34+, `0.8.x` → 1.31+, `0.7.x` → 1.27+, `0.6.x` → 1.25+ (all `metrics.k8s.io/v1beta1`). Older clusters (<1.16) need `--authorization-always-allow-paths=/livez,/readyz`. See the [README compatibility matrix](https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#compatibility-matrix).
- Field-level detail for the served group is intentionally not duplicated here; follow the [Metrics API spec](https://github.com/kubernetes/metrics) and [design doc](https://github.com/kubernetes/design-proposals-archive/blob/main/instrumentation/metrics-server.md) links above.
