---
upstream: https://github.com/kubernetes-csi/external-snapshotter
last_updated: 2026-08-27
---

# external-snapshotter — API reference

The project serves two custom resource API groups; the CRDs live in the repository under [client/config/crd/](https://github.com/kubernetes-csi/external-snapshotter/tree/master/client/config/crd) and are installed cluster-wide (they are independent of any CSI driver). The volume snapshot group `snapshot.storage.k8s.io` has `v1` as served and storage version (GA since Kubernetes 1.20; `v1beta1` is deprecated). The volume group snapshot group `groupsnapshot.storage.k8s.io` has `v1` as served and storage version (GA since v8.6.0), with `v1beta2` and `v1beta1` still served.

| Kind | Scope | Purpose | Upstream API docs |
| ---- | ----- | ------- | ----------------- |
| `VolumeSnapshot` | Namespaced | User-facing snapshot request for a PVC: names a class, becomes ready when the driver's snapshot exists. Also the `dataSource` for restoring a new PVC from a snapshot. | [kubernetes.io](https://kubernetes.io/docs/concepts/storage/volume-snapshots/) · [CRD YAML](https://github.com/kubernetes-csi/external-snapshotter/blob/master/client/config/crd/snapshot.storage.k8s.io_volumesnapshots.yaml) |
| `VolumeSnapshotClass` | Cluster | The StorageClass of snapshots: selects the CSI driver (`driver`) and passes `parameters` to it. | [kubernetes.io](https://kubernetes.io/docs/concepts/storage/volume-snapshot-classes/) · [CRD YAML](https://github.com/kubernetes-csi/external-snapshotter/blob/master/client/config/crd/snapshot.storage.k8s.io_volumesnapshotclasses.yaml) |
| `VolumeSnapshotContent` | Cluster | The snapshot itself: the driver's snapshot handle plus metadata. Created by the controller for dynamic snapshots, or by hand for pre-provisioned snapshots (`spec.source.snapshotHandle` + `driver`). | [CRD YAML](https://github.com/kubernetes-csi/external-snapshotter/blob/master/client/config/crd/snapshot.storage.k8s.io_volumesnapshotcontents.yaml) |
| `VolumeGroupSnapshot` | Namespaced | Snapshot of a group of PVCs matched by the label selector `spec.source.selector`, or a reference to a pre-existing group content. Requires drivers implementing CSI `CreateGroupSnapshot` (CSI spec v1.10+). | [CRD YAML](https://github.com/kubernetes-csi/external-snapshotter/blob/master/client/config/crd/groupsnapshot.storage.k8s.io_volumegroupsnapshots.yaml) |
| `VolumeGroupSnapshotClass` | Cluster | The class for group snapshots: selects the CSI driver and its `parameters`. | [CRD YAML](https://github.com/kubernetes-csi/external-snapshotter/blob/master/client/config/crd/groupsnapshot.storage.k8s.io_volumegroupsnapshotclasses.yaml) |
| `VolumeGroupSnapshotContent` | Cluster | The content of a group snapshot: the member per-volume snapshots produced by the driver. | [CRD YAML](https://github.com/kubernetes-csi/external-snapshotter/blob/master/client/config/crd/groupsnapshot.storage.k8s.io_volumegroupsnapshotcontents.yaml) |

Notes:

- Dynamic flow: a `VolumeSnapshot` (+ class) makes `snapshot-controller` create a `VolumeSnapshotContent`; the driver's `csi-snapshotter` sidecar calls CSI `CreateSnapshot` and the handle is recorded in the content; `status.readyToUse` flips true once the driver reports ready. Group snapshots follow the same pattern, with the controller creating one member `VolumeSnapshot`/`VolumeSnapshotContent` pair per selected PVC.
- Pre-provisioned (manual) snapshots: `VolumeSnapshot` can reference existing content via `spec.source.volumeSnapshotContentName`; the group kind has the analogous `spec.source.volumeGroupSnapshotContentName`.
- Status: both snapshot kinds report `readyToUse`, `creationTime`, and an `error` condition when applicable; `VolumeSnapshot` additionally has `restoreSize` and `boundVolumeSnapshotContentName`, group content binds via `boundVolumeGroupSnapshotContentName`.
- Restoring a volume from a snapshot: set the PVC's `spec.dataSource`/`spec.dataSourceRef` to the `VolumeSnapshot` — [Volume pvc datasource](https://kubernetes.io/docs/concepts/storage/volume-pvc-datasource/).
- API conversion between `VolumeGroupSnapshotContent` `v1beta1` and `v1beta2` is provided by the `snapshot-conversion-webhook` deployment; it is only needed if you consume the `v1beta1` group API.
- Validation is done via CEL rules embedded in the CRDs since v8.0.0 (the standalone validation webhook was deprecated in v8.0.0 and removed in v8.2.0).
- Quotas: `ResourceQuota` can cap snapshot counts, e.g. `count/volumesnapshots.snapshot.storage.k8s.io` — [README § Setting quota limits with snapshot custom resources](https://github.com/kubernetes-csi/external-snapshotter#setting-quota-limits-with-snapshot-custom-resources).

Field-level documentation is intentionally not duplicated here; follow the CRD/k8s.io links above.
