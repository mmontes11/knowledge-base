---
upstream: https://github.com/kubernetes-sigs/metrics-server
last_updated: 2026-08-23
---

# metrics-server — releases

Latest 10 official releases, newest first. The repository publishes **two independent release streams**: the Metrics Server component itself (`vX.Y.Z`) and the official Helm chart (`metrics-server-helm-chart-X.Y.Z`). Since this stack deploys via `HelmRelease`, both streams are listed and interleaved by date. Check the ⚠️ entries before upgrading.

## metrics-server-helm-chart-3.14.0 — 2026-08-19

[Release page](https://github.com/kubernetes-sigs/metrics-server/releases/tag/metrics-server-helm-chart-3.14.0)

- Update the bundled Metrics Server image to [`0.9.0`](https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.9.0).
- ⚠️ **`namespaceOverride` value added**: deploy the chart to a namespace other than `.Release.namespace` — relevant if the `HelmRelease` namespace and the target namespace differ.
- **`hostUsers` value added** for extra container isolation (host user namespaces).
- **Fixed (GitOps):** `tls.certManager.annotations`/`tls.certManager.labels` are now actually applied to the cert-manager `Certificate` (they were documented but never rendered); and `annotations: null` is no longer rendered on the cert-manager `Issuer`, which prevented a permanent ArgoCD/Flux `OutOfSync`.

## v0.9.0 — 2026-07-13

[Release page](https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.9.0)

- ⚠️ **Compatibility bump:** Kubernetes dependencies raised to `v1.36.2` — the 0.9.x line targets **K8s 1.34+** per the [compatibility matrix](https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#compatibility-matrix).
- **Security:** Go bumped to `v1.26.4`, addressing [CVE-2025-47907](https://nvd.nist.gov/vuln/detail/CVE-2025-47907) and [CVE-2025-47906](https://nvd.nist.gov/vuln/detail/CVE-2025-47906).
- **Bug fix:** server storage readiness now requires **both** node and pod metrics before reporting ready.
- **Logging:** `--stderrthreshold` is honored when `--logtostderr` is enabled.

## metrics-server-helm-chart-3.13.1 — 2026-06-11

[Release page](https://github.com/kubernetes-sigs/metrics-server/releases/tag/metrics-server-helm-chart-3.13.1)

- Update the Metrics Server image to `0.8.1`.
- ⚠️ **GitOps fix:** `insecureSkipTLSVerify` is rendered conditionally on the `APIService` — previously a `false` value still rendered the field (or its absence drifted), causing sync drift on ArgoCD/Flux.
- **GitOps fix:** `annotations` are no longer rendered as `null` in the `APIService` template, fixing a permanent `OutOfSync`.

## v0.8.1 — 2026-01-29

[Release page](https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.8.1)

- Maintenance/security patch: Golang → `v1.24.12`, Kubernetes dependencies → `v0.33.7`.
- Install via the release `components.yaml`: `kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.8.1/components.yaml`.

## metrics-server-helm-chart-3.13.0 — 2025-07-22

[Release page](https://github.com/kubernetes-sigs/metrics-server/releases/tag/metrics-server-helm-chart-3.13.0)

- **Added:** chart options to **secure the connection** between Metrics Server and the Kubernetes API server (tighter TLS/RBAC defaults).
- **Added:** `unhealthyPodEvictionPolicy` on the `PodDisruptionBudget`, as a user-enabled feature.
- Update Addon Resizer image to `1.8.23` and Metrics Server image to `0.8.0`.

## v0.8.0 — 2025-07-03

[Release page](https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.8.0)

- **Wire server run options to flags:** `SecureServingOptions` are now exposed, including a new `--disable-http2-serving` flag (HTTP/2 opt-out on the serving endpoint).
- Golang → `v1.24.4`, Kubernetes client → `v0.33.2`; `--kubelet-request-timeout` and friends remain the way to tune scraping.
- Tooling: support specifying non-default container engines in the build/test harness.

## metrics-server-helm-chart-3.12.2 — 2024-10-07

[Release page](https://github.com/kubernetes-sigs/metrics-server/releases/tag/metrics-server-helm-chart-3.12.2)

- Patch release; no changelog body was published on the release page. See the [chart CHANGELOG](https://github.com/kubernetes-sigs/metrics-server/blob/master/charts/metrics-server/CHANGELOG.md).

## v0.7.2 — 2024-08-28

[Release page](https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.7.2)

- Maintenance: Golang → `v1.22.5`, Kubernetes dependencies → `v1.29.8`.

## metrics-server-helm-chart-3.12.1 — 2024-04-05

[Release page](https://github.com/kubernetes-sigs/metrics-server/releases/tag/metrics-server-helm-chart-3.12.1)

- Patch release; no changelog body was published on the release page. See the [chart CHANGELOG](https://github.com/kubernetes-sigs/metrics-server/blob/master/charts/metrics-server/CHANGELOG.md).

## v0.7.1 — 2024-03-27

[Release page](https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.7.1)

- Golang → `v1.21.8`, Kubernetes dependencies → `v1.29.2`.
- **Bug fix:** fixed a regression causing `kubectl get PodMetrics`/`NodeMetrics` to fail.

## Upgrade notes and operational pitfalls

- **⚠️ `v0.7.0` removed klog-specific logging flags** (deprecated since `v0.6.1`): `--log-dir`, `--log-file`, `--logtostderr`, `--alsologtostderr`, `--one-output`, `--stderrthreshold`, `--log-file-max-size`, `--skip-log-headers`, `--add-dir-header`, `--skip-headers`, `--log-backtrace-at`. If your `HelmRelease` `args`/`defaultArgs` pass any of these, the container will fail to start on a 0.7.x+ image. Use the standard k8s logging flags instead (e.g. `--logtostderr` is gone — see [v0.7.0 notes](https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.7.0)).
- **`APIService` TLS:** the chart defaults `apiService.insecureSkipTLSVerify` to `true`. For a hardened deployment, set it to `false` and provide a `caBundle` (or use the `tls.*` block so the chart/cert-manager generates a trusted CA). The 3.13.1 fix makes a `false` value render cleanly under GitOps.
- **Kubelet must present a CA-signed serving certificate** (or set `--kubelet-insecure-tls`, testing only), and Webhook authn/authz must be enabled — otherwise the `APIService` reports `Available: false` and `kubectl top` / HPA return no data.
- **Chart versioning:** do not pin the chart from the `master` branch; reference the chart repository / chart release tag (this stack uses `3.13.0`). The chart and component version independently.
- **API stability:** `metrics.k8s.io/v1beta1` is still a beta group — no `v1` exists yet; `NodeMetrics`/`PodMetrics` are the only kinds.
