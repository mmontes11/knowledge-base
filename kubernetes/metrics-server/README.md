---
upstream: https://github.com/kubernetes-sigs/metrics-server
last_updated: 2026-08-23
---

# metrics-server

[Metrics Server](https://github.com/kubernetes-sigs/metrics-server) is a scalable, efficient source of container resource metrics for Kubernetes built-in autoscaling pipelines. It collects CPU/memory metrics from Kubelets (every 15 seconds by default) and serves them to the kube-apiserver through the [Metrics API](https://github.com/kubernetes/metrics), where they are consumed by [Horizontal Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/), [Vertical Pod Autoscaler](https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler), and `kubectl top`. It is explicitly **not** a monitoring solution — for full observability, scrape the Kubelet `/metrics/resource` endpoint directly.

- Upstream repository: https://github.com/kubernetes-sigs/metrics-server
- Documentation: [README](https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md) (requirements, scaling, design), [FAQ](https://github.com/kubernetes-sigs/metrics-server/blob/master/FAQ.md), [Known issues](https://github.com/kubernetes-sigs/metrics-server/blob/master/KNOWN_ISSUES.md), [Metrics API design](https://github.com/kubernetes/design-proposals-archive/blob/main/instrumentation/resource-metrics-api.md)
- License: Apache-2.0
- Maintained by [SIG Instrumentation](https://github.com/kubernetes/community/tree/master/sig-instrumentation)
- API group/version: `metrics.k8s.io/v1beta1` (`NodeMetrics`, `PodMetrics`); served via an `APIService` through the kube-apiserver aggregation layer

Deployed in this stack via Flux `HelmRelease` (chart `metrics-server` 3.13.0 from `https://kubernetes-sigs.github.io/metrics-server`) in the `monitoring` namespace — see `mmontes11/k8s-infrastructure`, `infrastructure/metrics-server/`.

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
