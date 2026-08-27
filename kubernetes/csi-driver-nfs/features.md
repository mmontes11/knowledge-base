---
upstream: https://github.com/kubernetes-csi/csi-driver-nfs
last_updated: 2026-08-27
---

# csi-driver-nfs — features

Key feature areas, each linked to the upstream documentation covering it. The in-repo docs at [docs/](https://github.com/kubernetes-csi/csi-driver-nfs/tree/master/docs) and the worked examples under [deploy/example/](https://github.com/kubernetes-csi/csi-driver-nfs/tree/master/deploy/example) are authoritative.

## Dynamic provisioning

- **Sub-directory per PVC**: binding a PVC against a `StorageClass` with `provisioner: nfs.csi.k8s.io` creates a new directory under the configured `share` (with `server` + `share` parameters); `subDir` supports `${pvc.metadata.name}`, `${pvc.metadata.namespace}`, and `${pv.metadata.name}` token substitution and is auto-created when missing. [driver parameters](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/docs/driver-parameters.md), [basic usage example](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/deploy/example/README.md)
- **`onDelete` lifecycle modes**: `delete` (default) removes the sub-directory when the volume is deleted, `retain` keeps it, and `archive` moves the data to an `archived-<subDir>` directory on the share; a cluster-wide default can be set with the controller flag `--default-ondelete-policy` (chart value `controller.defaultOnDeletePolicy`). [driver parameters](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/docs/driver-parameters.md)
- **`mountPermissions`**: optional chmod of the mount root after each mount (cluster-wide via chart `driver.mountPermissions`, per-StorageClass, or per-PV `volumeAttributes`). [driver parameters — when to set it](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/docs/driver-parameters.md)

## Static provisioning

- **Existing shares as PVs**: a `PersistentVolume` with `spec.csi.volumeHandle` (`{server}#{share}#{subDir}` recommended) and `volumeAttributes.server`/`share` (plus optional `mountPermissions`) binds an existing NFS export to a cluster with no controller-side changes. [driver parameters](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/docs/driver-parameters.md), [PV/PVC examples](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/deploy/example/pv-nfs-csi.yaml)

## Snapshots

- **CSI volume snapshots on the share (since v4.3.0)**: each snapshot is stored on the NFS share as a `.tar.gz` archive (`.tar` when `--enable-snapshot-compression=false`, since v4.13.0); restore and the chart's `externalSnapshotter` (CRDs + optional controller Deployment) are required. `VolumeSnapshotClass` can carry custom `server`/`share` and — since v4.11.0 — `mountOptions` for the snapshot-creation mount. [snapshot example](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/deploy/example/snapshot/README.md), [driver parameters](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/docs/driver-parameters.md)

## Volume cloning

- **PVC-to-PVC cloning (since v4.3.0)**: a new PVC with a `dataSource` pointing at an existing PVC is materialized by copying the source volume's data on the share into the new sub-directory; upstream warns to quiesce the source while cloning. [cloning example](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/deploy/example/cloning/README.md)

## Permission handling (fsGroup)

- **fsGroup-based POSIX permissions**: the `CSIDriver` advertises `fsGroupPolicy: File` by default (chart `feature.enableFSGroupPolicy`), so the standard Kubernetes `securityContext.fsGroup` pattern works out of the box; `mountPermissions` is documented as a last resort (with a warning against `0777` in multi-tenant clusters) and since v4.13.3 correctly applies setgid/setuid/sticky bits. [fsGroup example](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/deploy/example/fsgroup/README.md), [driver parameters](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/docs/driver-parameters.md)

## Ephemeral and inline volumes

- **Inline (ephemeral) volumes**: enabling `feature.enableInlineVolume` adds the `Ephemeral` lifecycle mode to the `CSIDriver`, so pods can declare `volume.csi` inline NFS volumes (no persistent lifecycle); a per-node ephemeral NFS mount is also covered. [inline-volume example](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/deploy/example/nginx-pod-inline-volume.yaml), [ephemeral example](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/deploy/example/daemonset-nfs-ephemeral.yaml)

## Volume expansion

- **`allowVolumeExpansion: true`**: the chart deploys the csi-resizer sidecar and the example StorageClass enables expansion; on NFS this updates the PV's recorded size (the export itself is not resized). [example StorageClass](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/deploy/example/storageclass-nfs.yaml)

## Mount options for delete operations

- **`mountOptions` via secrets**: `DeleteVolumeRequest`/`DeleteSnapshotRequest` carry no mount options upstream; the documented workaround passes them through a Secret wired with the `csi.storage.k8s.io/provisioner-secret-*` (StorageClass) or `csi.storage.k8s.io/snapshotter-secret-*` (VolumeSnapshotClass) parameters. [driver parameters](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/docs/driver-parameters.md), [issue #260](https://github.com/kubernetes-csi/csi-driver-nfs/issues/260)

## Mount robustness

- A timeout was added to mount calls to avoid stuck volume operations (v4.10.0), stale NFS mounts are detected and remounted in `NodePublishVolume` (v4.13.3), `ENODEV` surfaces as a corrupted mount (v4.9.0), and failed unmount detection was fixed (v4.9.0). See [releases](releases.md) and the [CSI troubleshooting guide](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/docs/csi-debug.md).

## Deployment and operations

- **Install paths**: Helm chart from `https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/charts`, plain `kubectl` manifests per release line (`deploy/vX.Y.Z/`), or MicroK8s ([MicroK8s NFS docs](https://microk8s.io/docs/how-to-nfs)); [install guide](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/docs/install-nfs-csi-driver.md).
- **Health endpoints**: livenessprobe sidecars expose health HTTP endpoints (controller and node, default ports 29652/29653, configurable in the chart) for container liveness checks. [chart values](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/charts/latest/csi-driver-nfs/values.yaml)
- **CI**: Prow/TestGrid dashboards for the driver build and e2e suites ([sig-storage-csi-nfs](https://testgrid.k8s.io/sig-storage-csi-other)). [README](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/README.md)
