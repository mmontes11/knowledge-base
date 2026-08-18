---
upstream: https://github.com/topolvm/topolvm
last_updated: 2026-08-17
---

# topolvm — API reference

TopoLVM adds exactly one custom kind: `LogicalVolume`, served and stored under `topolvm.io/v1` and **cluster-scoped**. The CRD reference is [docs/logical-volume-crd.md](https://github.com/topolvm/topolvm/blob/main/docs/logical-volume-crd.md); the Go types that back the schema live in [pkg/apis/topolvm/v1/types.go](https://github.com/topolvm/topolvm/blob/main/pkg/apis/topolvm/v1/types.go). Everything else is standard Kubernetes kinds consumed or created through the CSI plugin.

| Kind | API group/version | Purpose | Upstream API docs |
| --- | --- | --- | --- |
| `LogicalVolume` | `topolvm.io/v1` | Persistent record of a provisioned LVM volume, used to communicate desired state from `topolvm-controller` to `topolvm-node`: `spec` carries `name`, `nodeName`, `size` (required) plus `deviceClass`, `accessType`, `lvcreateOptionClass`, and `source` (thin clone/snapshot basis), and `status` carries `volumeID`, `currentSize`, `code`, and `message` once the node realizes the volume via lvmd | [CRD docs](https://github.com/topolvm/topolvm/blob/main/docs/logical-volume-crd.md) |

## Standard kinds managed by the CSI plugin

| Kind | Identifier | Role in TopoLVM | Upstream docs |
| --- | --- | --- | --- |
| `CSIDriver` | name `topolvm.io` | Node-local volumes: `attachRequired: false`, `storageCapacity: true`, `podInfoOnMount: true`, `volumeLifecycleModes: [Persistent]` | [getting started](https://github.com/topolvm/topolvm/blob/main/docs/getting-started.md) |
| `StorageClass` | provisioner `topolvm.io` | Declares TopoLVM provisioning; key parameters are `csi.storage.k8s.io/fstype` (ext4/xfs/btrfs) and `topolvm.io/device-class` | [advanced setup](https://github.com/topolvm/topolvm/blob/main/docs/advanced-setup.md) |
| `VolumeSnapshotClass` | driver `topolvm.io` | Enables CSI snapshots (thin volumes only) via the external-snapshotter | [snapshot and restore](https://github.com/topolvm/topolvm/blob/main/docs/snapshot-and-restore.md) |
| `PersistentVolume` / `PersistentVolumeClaim` | — | Standard CSI lifecycle; the `topolvm.io/logicalvolume` finalizer on the `LogicalVolume` keeps the LVM volume in sync with PVC/PV deletion | [CRD docs](https://github.com/topolvm/topolvm/blob/main/docs/logical-volume-crd.md) |

## Notes

- `LogicalVolume` has a kind name and plural (`logicalvolumes`) but no short names; it is cluster-scoped while all the surrounding kinds are namespace-scoped.
- Renaming history: the API and plugin domain used to be `topolvm.cybozu.com` and was renamed to `topolvm.io` — CRD, finalizers (`topolvm.io/logicalvolume`, `topolvm.io/node`), annotations/parameter keys (`topolvm.io/device-class`, `topolvm.io/resize-requested-at`, `topolvm.io/capacity`, `capacity.topolvm.io/*`), and the CSI driver name. The kind has been `LogicalVolume` throughout the documented history; only the domain changed. See [proposal: rename group](https://github.com/topolvm/topolvm/blob/main/docs/proposals/rename-group.md); legacy names are still honored via the opt-in legacy mode.
- Volume expansion is triggered through the `topolvm.io/resize-requested-at` annotation on the `LogicalVolume`, not via a custom resize API ([CRD docs](https://github.com/topolvm/topolvm/blob/main/docs/logical-volume-crd.md)).
- Device classes (the LVM-side counterpart of the `deviceClass` field) are configured per node for `lvmd` — name, volume group, `type` (`thin` or `standard`), thin pool name/overprovision ratio, spare GB, striping, and `lvcreate-options` ([lvmd docs](https://github.com/topolvm/topolvm/blob/main/docs/lvmd.md)).
