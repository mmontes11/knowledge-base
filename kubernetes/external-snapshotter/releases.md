---
upstream: https://github.com/kubernetes-csi/external-snapshotter
last_updated: 2026-08-27
---

# external-snapshotter — releases

Latest 10 official releases, newest first. Check the ⚠️ entries before upgrading.

## v8.6.0 — 2026-05-28

[Release page](https://github.com/kubernetes-csi/external-snapshotter/releases/tag/v8.6.0)

- **VolumeGroupSnapshot promoted to GA**: the group API now has a `v1` version under `groupsnapshot.storage.k8s.io`; controllers use the v1 APIs and set `v1beta2` as the stored version during migration. The `CSIVolumeGroupSnapshot` feature gate is GA and enabled by default.
- Bugfixes: VolumeSnapshot/VolumeGroupSnapshot deletion is retried while a PVC restore from that snapshot is in progress; racing deletion of snapshots marked for deletion while the CSI driver is still taking them; PVC finalizer update conflicts are retried; the conversion webhook HTTP server got timeouts.
- Kubernetes dependencies bumped to v1.36.1, Go to 1.26.0. Supports CSI spec 1.0–1.12; minimum Kubernetes is 1.25.

## v8.5.0 — 2026-02-12

[Release page](https://github.com/kubernetes-csi/external-snapshotter/releases/tag/v8.5.0)

- ⚠️ **Security**: Go toolchain updated to fix CVE-2025-68121.
- Kubernetes dependencies bumped to v1.35.0. Supports CSI spec 1.0–1.12.

## v8.4.0 — 2025-10-23

[Release page](https://github.com/kubernetes-csi/external-snapshotter/releases/tag/v8.4.0)

- **New `v1beta2` VolumeGroupSnapshot API** as described by [KEP 5013](https://github.com/kubernetes/enhancements/pull/5013); the `v1beta1` group API is now marked deprecated.
- Worker-thread count is configurable via the `--worker-threads` flag in both `snapshot-controller` and `csi-snapshotter` (default 10).
- Several `VolumeGroupSnapshot`, `VolumeGroupSnapshotClass`, and `VolumeGroupSnapshotContent` fields are now immutable.
- Kubernetes dependencies v1.34.0; CSI spec v1.12.

## v8.3.0 — 2025-06-18

[Release page](https://github.com/kubernetes-csi/external-snapshotter/releases/tag/v8.3.0)

- New `--automaxprocs` flag (matches `GOMAXPROCS` to the container CPU quota) and `-logging-format=json` support; klog-specific flags removed per [KEP-2845](https://github.com/kubernetes/enhancements/tree/master/keps/sig-instrumentation/2845-structured-logging).
- New served metrics for leader election, work queues, process, and Go runtime.
- Group snapshot naming now uses the `VolumeGroupSnapshot` uid, and credentials for `VolumeSnapshotContent`s that are members of a group snapshot are read from annotations.
- ⚠️ **Security**: fixes CVE-2025-22870 and CVE-2025-22872.
- Bugfixes: `VolumeSnapshotContent` objects no longer fail to re-sync; finalizer removal on deletion no longer gets stuck on API-server/network hiccups.

## v8.2.1 — 2025-02-27

[Release page](https://github.com/kubernetes-csi/external-snapshotter/releases/tag/v8.2.1)

- Serves additional leader election, work queue, process, and Go runtime metrics.
- Kubernetes dependencies updated to 1.32.0.

## v8.2.0 — 2024-12-10

[Release page](https://github.com/kubernetes-csi/external-snapshotter/releases/tag/v8.2.0)

- ⚠️ **API**: the `VolumeGroupSnapshot`, `VolumeGroupSnapshotContent`, and `VolumeGroupSnapshotClass` kinds are served in `v1beta1`; `v1alpha1` support was removed.
- ⚠️ **Validation webhook removed** (deprecated in v8.0.0). Multiple default classes for the same driver are now detected at dynamic-provisioning time instead of at object-creation time.
- The `--enable-volume-group-snapshots` flag was replaced by `--feature-gates=CSIVolumeGroupSnapshot=true` (disabled by default in this release; GA and enabled by default since v8.6.0).
- Creation of the individual `VolumeSnapshot`/`VolumeSnapshotContent` objects for a dynamic group snapshot moved from the sidecar to `snapshot-controller`. CSI spec v1.11.0.

## v8.1.1 — 2024-12-17

[Release page](https://github.com/kubernetes-csi/external-snapshotter/releases/tag/v8.1.1)

- Fix: unbounded `VolumeSnapshot` list call on snapshot-controller startup. (Backported to the 8.1 line after v8.2.0 shipped.)

## v8.1.0 — 2024-08-30

[Release page](https://github.com/kubernetes-csi/external-snapshotter/releases/tag/v8.1.0)

- Documented the Volume Group Snapshot feature in the README.
- Group-snapshot operation histogram (`snapshot_controller_operation_total_seconds`) exposed via `--http-endpoint`/`--metrics-path` on the snapshot-controller.
- Bugfixes: group-snapshot metrics no longer look up a `VolumeSnapshotClass`; a group snapshot is rejected when the member volumes' CSI driver differs from the group class's driver; dynamic provisioning fails when multiple default classes exist for one driver; PVC finalizer removal race on update conflicts.
- Kubernetes dependencies v1.31.0.

## v8.0.2 — 2024-12-10

[Release page](https://github.com/kubernetes-csi/external-snapshotter/releases/tag/v8.0.2)

- ⚠️ **Security**: fixes GHSA-xr7q-jx4m-x55m and the unbounded `VolumeSnapshot` list call on snapshot-controller startup (same fix as v8.1.1, backported).

## v8.0.1 — 2024-06-04

[Release page](https://github.com/kubernetes-csi/external-snapshotter/releases/tag/v8.0.1)

- csi-lib-utils updated to v0.18.1.

Note: the VolumeSnapshot/VolumeGroupSnapshot API types and Go client library ship as a separate Go module under [`client/`](https://github.com/kubernetes-csi/external-snapshotter/tree/master/client) with their own `client/vX.Y.Z` release tags mirroring each mainline version (e.g. `client/v8.2.0` carries "urgent upgrade notes" about the validation-webhook removal).
