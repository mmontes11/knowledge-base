---
upstream: https://github.com/backube/snapscheduler
last_updated: 2026-08-16
---

# snapscheduler

Kubernetes operator for scheduled snapshots of CSI-backed persistent volumes. Instead of taking or expiring `VolumeSnapshot`s by hand, you declare a `SnapshotSchedule` — a crontab expression plus a PVC label selector — and the operator creates one snapshot per selected PVC on every firing, names it `<pvcname>-<schedulename>-<timestamp>`, and enforces age- and count-based retention automatically. A single cluster-wide operator instance manages all schedules.

- Upstream repository: https://github.com/backube/snapscheduler
- Documentation: https://backube.github.io/snapscheduler/ — [usage](https://backube.github.io/snapscheduler/usage.html), [installation](https://backube.github.io/snapscheduler/install.html), [labeling strategies](https://backube.github.io/snapscheduler/labeling.html)
- License: AGPL-3.0-or-later (per [GitHub license detection](https://github.com/backube/snapscheduler) and the chart's `artifacthub.io/license` annotation in [Chart.yaml](https://github.com/backube/snapscheduler/blob/master/helm/snapscheduler/Chart.yaml))
- API group/version: `snapscheduler.backube/v1`
- Requirements: Kubernetes ≥ 1.20 (`kubeVersion: ^1.20.0-0` in [Chart.yaml](https://github.com/backube/snapscheduler/blob/master/helm/snapscheduler/Chart.yaml); support for older versions removed in the [v3.0.0 release](https://github.com/backube/snapscheduler/releases/tag/v3.0.0)) and a CSI storage driver that advertises the `CREATE_DELETE_SNAPSHOT` capability ([chart README](https://github.com/backube/snapscheduler/blob/master/helm/snapscheduler/README.md), [usage docs](https://backube.github.io/snapscheduler/usage.html))

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
