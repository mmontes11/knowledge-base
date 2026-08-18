---
upstream: https://github.com/backube/snapscheduler
last_updated: 2026-08-16
---

# snapscheduler — API reference

The operator registers a single custom resource kind, `SnapshotSchedule`, under API group/version **`snapscheduler.backube/v1`** (namespaced — a schedule manages PVCs within one namespace). Field-level truth lives upstream:

- Go type: [`api/v1/snapshotschedule_types.go`](https://github.com/backube/snapscheduler/blob/master/api/v1/snapshotschedule_types.go)
- CRD as shipped by the Helm chart: [`helm/snapscheduler/templates/snapscheduler.backube_snapshotschedules.yaml`](https://github.com/backube/snapscheduler/blob/master/helm/snapscheduler/templates/snapscheduler.backube_snapshotschedules.yaml)
- Usage semantics (naming, retention behavior): [usage docs](https://backube.github.io/snapscheduler/usage.html)

| Kind | API group/version | Scope | Purpose | Upstream API docs |
| ---- | ----------------- | ----- | ------- | ----------------- |
| `SnapshotSchedule` | `snapscheduler.backube/v1` | Namespaced | Crontab schedule that creates a `VolumeSnapshot` for every selected PVC on each firing, with optional age/count retention and per-snapshot labels and snapshot class. | [usage docs](https://backube.github.io/snapscheduler/usage.html), [type definition](https://github.com/backube/snapscheduler/blob/master/api/v1/snapshotschedule_types.go) |

`spec` fields (`SnapshotScheduleV1` spec — summarized from the type definition; see the upstream link above for authoritative details):

- `claimSelector` — standard `LabelSelector` (`matchLabels` / `matchExpressions`) selecting the PVCs to snapshot.
- `schedule` — crontab expression; evaluated in UTC. "Slash" notation accepted since v3.3.0.
- `disabled` — pause snapshotting for the schedule without deleting it (default false).
- `retention.expires` — duration; snapshots older than it are deleted.
- `retention.maxCount` — keep at most N snapshots per PVC, oldest deleted beyond that.
- `snapshotTemplate.labels` — labels applied to every generated snapshot.
- `snapshotTemplate.snapshotClassName` — `VolumeSnapshotClass` used for new snapshots; unset falls back to the PVC's class / cluster default.

`status` fields: `lastSnapshotTime`, `nextSnapshotTime`, and `conditions`.

Built-in kinds the operator acts on (not registered by snapscheduler):

| Kind | API group/version | Role for the operator | Upstream docs |
| ---- | ----------------- | --------------------- | ------------- |
| `VolumeSnapshot` | `snapshot.storage.k8s.io/v1` | Created per PVC per firing, named `<pvcname>-<schedulename>-<timestamp>` (UTC); deleted by retention. | [Kubernetes docs](https://kubernetes-csi.github.io/docs/snapshot-features.html) |
| `VolumeSnapshotClass` | `snapshot.storage.k8s.io/v1` | Selects the storage driver snapshot semantics via `spec.snapshotTemplate.snapshotClassName`. | [Kubernetes docs](https://kubernetes-csi.github.io/docs/snapshot-features.html) |
| `PersistentVolumeClaim` | `v1` | Snapshot target, selected by `spec.claimSelector`. | [Kubernetes docs](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) |

## Helm chart surface

Chart `snapscheduler` from repo `https://backube.github.io/helm-charts/` (chart source: [`helm/snapscheduler/`](https://github.com/backube/snapscheduler/tree/master/helm/snapscheduler); defaults from [`values.yaml`](https://github.com/backube/snapscheduler/blob/master/helm/snapscheduler/values.yaml)). The chart installs the CRD itself since v3.2.0.

| Value | Default | Purpose |
| ----- | ------- | ------- |
| `replicaCount` | `1` | Operator Deployment replicas (single instance per cluster; leader election is on). |
| `image.*` / `image.tagOverride` | `quay.io/backube/snapscheduler` / appVersion tag | Operator image; hash pinning instead of tag since v3.2.0. |
| `enableOwnerReferences` | `false` | (v3.5.0+) Set owner references on snapshots so deleting a schedule deletes them. |
| `enableLeaderElection` | `true` | Leader election for the operator. |
| `rbacProxy.image` | `quay.io/brancz/kube-rbac-proxy` v0.22.1 (sha256-pinned) | Sidecar serving the authenticated metrics endpoint. |
| `metrics.disableAuth` | `false` | Disable auth on the metrics endpoint. |
| `nodeSelector` | `kubernetes.io/arch: amd64`, `kubernetes.io/os: linux` | Operator placement (beta.kubernetes.io labels removed in v2.0.0). |
| `tolerations`, `topologySpreadConstraints`, `affinity` | empty / preferred pod-anti-affinity | Scheduling controls; TSC configurable since v3.1.0. |
| `podLabels`, `podAnnotations` | empty | Operator pod metadata, since v3.3.0. |
| `priorityClassName` | unset | Operator priority, since v3.3.0. |
| `serviceAccount.*`, `resources.*`, `podSecurityContext`, `securityContext` | see values | Standard hardening (runs non-root, drops all capabilities). |

## Metrics

Prometheus metrics are exposed by the operator container and published by the chart's `{{ fullname }}-metrics` Service on port `8443` (HTTPS, served by the kube-rbac-proxy sidecar) — see [`service-metrics.yaml`](https://github.com/backube/snapscheduler/blob/master/helm/snapscheduler/templates/service-metrics.yaml). The `snapscheduler-metrics` Service name and endpoint fix date from the [v1.2.0 release](https://github.com/backube/snapscheduler/releases/tag/v1.2.0).
