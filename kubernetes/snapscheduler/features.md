---
upstream: https://github.com/backube/snapscheduler
last_updated: 2026-08-16
---

# snapscheduler — features

Significant feature areas, each summarized from upstream documentation; the [docs site](https://backube.github.io/snapscheduler/) is the source of truth.

## Scheduled snapshot creation

Each `SnapshotSchedule` carries a crontab expression evaluated in UTC; on every firing the operator creates one `VolumeSnapshot` per PVC selected by the schedule, named `<pvcname>-<schedulename>-<timestamp>`. A `disabled` flag pauses a schedule without deleting it. Cron validation accepts "slash" notation since [v3.3.0](https://github.com/backube/snapscheduler/releases/tag/v3.3.0). Semantics are documented in the [usage docs](https://backube.github.io/snapscheduler/usage.html).

## PVC selection

Schedules select PVCs with standard Kubernetes label selectors (`spec.claimSelector` with `matchLabels` / `matchExpressions`), so snapshotting can be toggled per workload by labeling or unlabeled PVCs, per namespace (schedules are namespaced). See the [usage docs](https://backube.github.io/snapscheduler/usage.html).

## Retention policies

Two retention knobs per schedule, both optional and independent: `retention.expires` deletes snapshots older than a duration, and `retention.maxCount` deletes the oldest snapshots beyond a count (per PVC). When both are set, the stricter of the two applies. Documented in the [usage docs](https://backube.github.io/snapscheduler/usage.html).

## Snapshot customization

`spec.snapshotTemplate` lets a schedule stamp every snapshot it creates with custom labels and choose the `VolumeSnapshotClass`; an unset class falls back to the PVC's snapshot class / cluster default. See the [type definition](https://github.com/backube/snapscheduler/blob/master/api/v1/snapshotschedule_types.go) and [usage docs](https://backube.github.io/snapscheduler/usage.html).

## Labeling strategies

Upstream documents three labeling approaches for `SnapshotSchedule`s — **application-centric**, **schedule-centric**, and **service-level** — to keep snapshot ownership attributable across schedules. The [labeling guide](https://backube.github.io/snapscheduler/labeling.html) compares them and recommends per environment.

## Cascade delete of snapshots

Since [v3.5.0](https://github.com/backube/snapscheduler/releases/tag/v3.5.0), the operator can set owner references on the snapshots it creates (Helm value `enableOwnerReferences`, default `false`), so deleting a `SnapshotSchedule` also deletes its snapshots. Disabled by default, keeping pre-3.5 behavior (orphaned snapshots) unless opted in.

## Observability

The operator exposes Prometheus metrics; the Helm chart ships a kube-rbac-proxy sidecar and a `{{ fullname }}-metrics` Service on port 8443 (HTTPS) so scrapes are authenticated by default, with `metrics.disableAuth` to turn auth off. See [`service-metrics.yaml`](https://github.com/backube/snapscheduler/blob/master/helm/snapscheduler/templates/service-metrics.yaml) and [chart values](https://github.com/backube/snapscheduler/blob/master/helm/snapscheduler/values.yaml); the metrics endpoint fix dates from [v1.2.0](https://github.com/backube/snapscheduler/releases/tag/v1.2.0).

## Deployment model

Deployed as a Helm chart (`backube/snapscheduler`, repo `https://backube.github.io/helm-charts/`), a single cluster-level operator instance (leader election enabled by default; run one operator per cluster) that watches `SnapshotSchedule`s across all namespaces. Since [v3.2.0](https://github.com/backube/snapscheduler/releases/tag/v3.2.0) the chart owns CRD installation — see that release's upgrade note before upgrading from 3.1.0 or earlier. Requirements: Kubernetes ≥ 1.20 and a CSI driver advertising `CREATE_DELETE_SNAPSHOT` ([chart README](https://github.com/backube/snapscheduler/blob/master/helm/snapscheduler/README.md)).
