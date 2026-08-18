---
upstream: https://github.com/topolvm/topolvm
last_updated: 2026-08-17
---

# topolvm — features

Key feature areas, each linked to the upstream documentation covering it. The in-repo docs at [topolvm/topolvm/docs](https://github.com/topolvm/topolvm/tree/main/docs) are authoritative.

## Volume provisioning and device classes

- **Dynamic provisioning from node-local LVM**: the CSI plugin provisions each PV as a logical volume inside a volume group on the node that hosts the pod; standard (thick) or **thin-provisioned** volumes, with optional striping, per-class spare capacity, and raw `lvcreate-options`. [docs/advanced-setup](https://github.com/topolvm/topolvm/blob/main/docs/advanced-setup.md)
- **Device classes**: named LVM backends (volume group + thin pool + overprovision ratio + options) configured for `lvmd` per node and selected per StorageClass with the `topolvm.io/device-class` parameter; a class can be marked as the node default. [docs/lvmd](https://github.com/topolvm/topolvm/blob/main/docs/lvmd.md)
- **Filesystem volumes**: `ext4`, `xfs` (reflink/rmapbt require kernel ≥ 4.9 with the official images), and `btrfs` (beta), via the `csi.storage.k8s.io/fstype` StorageClass parameter. [docs/getting-started](https://github.com/topolvm/topolvm/blob/main/docs/getting-started.md)

## LVMd and deployment modes

- **LVMd**: a per-node gRPC service (`VGService` for listing volume groups and watching free space, `LVService` for create/remove/resize) that isolates LVM state-mutating calls behind the socket `/run/topolvm/lvmd.sock`. [docs/lvmd](https://github.com/topolvm/topolvm/blob/main/docs/lvmd.md), [protocol](https://github.com/topolvm/topolvm/blob/main/docs/lvmd-protocol.md)
- **Deployment modes**: dedicated `lvmd` DaemonSet (chart default), embedded inside `topolvm-node`, or external via systemd; per-node config overrides are supported. Note that config changes require an lvmd restart. [docs/advanced-setup](https://github.com/topolvm/topolvm/blob/main/docs/advanced-setup.md)

## LogicalVolume CRD (controller ↔ node communication)

- The cluster-scoped `LogicalVolume` kind is the durable handoff between the CSI controller and the node agent: the controller writes desired state (`name`, `nodeName`, `size`, `deviceClass`, …) and `topolvm-node` realizes it through lvmd and reports `volumeID`/`currentSize`. Online **volume expansion** is driven by the `topolvm.io/resize-requested-at` annotation. [docs/logical-volume-crd](https://github.com/topolvm/topolvm/blob/main/docs/logical-volume-crd.md)

## Topology- and capacity-aware scheduling

- **Storage capacity tracking** (CSI Storage Capacity API + `WaitForFirstConsumer`) is the default: the kube-scheduler already refuses nodes without adequate capacity for the requested device class. [docs/advanced-setup](https://github.com/topolvm/topolvm/blob/main/docs/advanced-setup.md)
- **Optional extension scheduler**: `topolvm-scheduler` runs as a kube-scheduler extender (with a pod-mutating webhook for topology hints) for finer-grained capacity-aware placement; it is not required for basic topology scheduling. [docs/advanced-setup](https://github.com/topolvm/topolvm/blob/main/docs/advanced-setup.md), [design](https://github.com/topolvm/topolvm/blob/main/docs/topolvm-scheduler.md)

## Snapshots and restore

- **CSI snapshots for thin volumes**: create a thin pool, define a `type: thin` device class with `thin-pool.name`/`thin-pool.overprovision-ratio`, and point a `VolumeSnapshotClass` at driver `topolvm.io`; requires the external-snapshotter CRDs and controller. Restores use `dataSourceRef` on the PVC. [docs/snapshot-and-restore](https://github.com/topolvm/topolvm/blob/main/docs/snapshot-and-restore.md)

## Monitoring

- **Prometheus metrics**: `topolvm-controller`, `topolvm-node`, and `topolvm-scheduler` each expose `/metrics`; `topolvm-node` reports per-volume-group available bytes, and filesystem usage of PVs comes from kubelet metrics. [docs/prometheus](https://github.com/topolvm/topolvm/blob/main/docs/prometheus.md)

## Limitations (operator-facing)

- ⚠️ Snapshots can be created **only for thin volumes**, and a snapshot/clone can be restored **only on the same node** as its source. [docs/limitations](https://github.com/topolvm/topolvm/blob/main/docs/limitations.md)
- ⚠️ Capacity-aware scheduling can return stale results if many PVCs are created at once; pods **without** a PVC are not subject to the topology/whole-node checks. [docs/limitations](https://github.com/topolvm/topolvm/blob/main/docs/limitations.md)
- ⚠️ `lvcreate-options` are applied verbatim at your own risk (e.g. RAID layouts can distort capacity accounting); old kernels may fail with the official xfs images; restoring a snapshot with a *different* StorageClass can fail; rarely an LVM volume can remain after PVC deletion (upstream #486). [docs/limitations](https://github.com/topolvm/topolvm/blob/main/docs/limitations.md)
