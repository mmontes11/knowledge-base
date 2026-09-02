---
upstream: https://github.com/kubernetes-csi/external-snapshotter
last_updated: 2026-08-27
---

# external-snapshotter — features

Significant feature areas, each with a link to the matching upstream doc. The [repository README](https://github.com/kubernetes-csi/external-snapshotter#readme) is authoritative; the CSI project site is [kubernetes-csi.github.io](https://kubernetes-csi.github.io).

## Volume snapshotting (GA)

- Individual CSI volume snapshots, GA since Kubernetes 1.20 — enabled by default and cannot be turned off on standard clusters. Dynamic (controller-created) and pre-provisioned (manual `VolumeSnapshotContent`) snapshots are both supported. [README § Usage](https://github.com/kubernetes-csi/external-snapshotter#usage) · [kubernetes.io: volume snapshots](https://kubernetes.io/docs/concepts/storage/volume-snapshots/)
- Creating new volumes from a snapshot via the PVC's `dataSource`/`dataSourceRef` (`VolumeSnapshotDataSource`): [kubernetes.io: volume pvc datasource](https://kubernetes.io/docs/concepts/storage/volume-pvc-datasource/)

## Volume group snapshotting (GA since v8.6.0)

- Snapshot a group of PVCs as one atomic unit; alpha in Kubernetes 1.27, GA in CSI spec v1.11.0, v1 API in v8.6.0. Requires drivers implementing CSI `CreateGroupSnapshot` (CSI spec v1.10+). [KEP-5013](https://github.com/kubernetes/enhancements/pull/5013) · [README § Volume Group Snapshot Support](https://github.com/kubernetes-csi/external-snapshotter#volume-group-snapshot-support) · [Kubernetes 1.27 announcement](https://kubernetes.io/blog/2023/05/08/kubernetes-1-27-volume-group-snapshot-alpha/)

## Deployment model and high availability

- One `snapshot-controller` per cluster serves all CSI drivers; one `csi-snapshotter` sidecar per CSI driver. Leader election is supported via `--leader-election` (plus lease-duration/renew/retry flags). [README § Usage](https://github.com/kubernetes-csi/external-snapshotter#usage)
- **Distributed snapshotting** for node-local volumes: run the sidecar on every node with `--node-deployment` and enable `--enable-distributed-snapshotting` on the controller. [README § Distributed Snapshotting](https://github.com/kubernetes-csi/external-snapshotter#distributed-snapshotting)
- `--prevent-volume-mode-conversion` (beta, default on) blocks changing the volume mode when restoring a PVC from a snapshot.

## Validation and quotas

- CEL validation rules embedded in the CRDs since v8.0.0 replaced the standalone validation webhook (deprecated in v8.0.0, removed in v8.2.0): [README § CRDs and Client Library](https://github.com/kubernetes-csi/external-snapshotter#crds-and-client-library)
- `ResourceQuota` support for snapshot kinds (`count/volumesnapshots.snapshot.storage.k8s.io`): [README § Setting quota limits](https://github.com/kubernetes-csi/external-snapshotter#setting-quota-limits-with-snapshot-custom-resources)
- Conversion webhook for `VolumeGroupSnapshotContent` `v1beta1`↔`v1beta2`: [README § Conversion Webhook](https://github.com/kubernetes-csi/external-snapshotter#conversion-webhook) · [webhook example](https://github.com/kubernetes-csi/external-snapshotter/tree/master/deploy/kubernetes/webhook-example)

## Metrics and observability

- Prometheus metrics via `--http-endpoint`/`--metrics-path` on both binaries; leader-election health check at `/healthz/leader-election` (recommended for liveness probes with leader election). Operation histograms for snapshot and group-snapshot operations; leader election, work queue, process, and Go runtime metrics since v8.2.1.

## Upgrades

- Versioned API surface and upgrade guidance (v1alpha1→v1beta1→v1 volume-snapshot APIs, group-snapshot API ladder v1alpha1→v1beta1→v1beta2→v1): [README § Upgrade](https://github.com/kubernetes-csi/external-snapshotter#upgrade)
