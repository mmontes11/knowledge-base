---
upstream: https://github.com/SynologyOpenSource/synology-csi
last_updated: 2026-08-17
---

# synology-csi — Features

Feature areas of the Synology CSI driver, with links to the upstream documentation ([README](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md)).

## Storage protocols

- **iSCSI LUNs (default)** — Provisions thin-provisioned LUNs on DSM, formatted `ext4` or `btrfs` (`fsType`), and exposes them as RWO block volumes; LUN description is populated from PVC info (v1.1.3+). See [Creating Storage Classes](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md#creating-storage-classes).
- **SMB / CIFS** — Provisions shared folders as RWX file volumes; requires a node stage secret with share credentials. See [example StorageClass](https://github.com/SynologyOpenSource/synology-csi/blob/main/deploy/example/storageclass-smb.yaml).
- **NFS** — Provisions shared folders over NFS with optional per-node addresses (v1.2.0+) and `mountPermissions` `chmod` after mount. See [example StorageClass](https://github.com/SynologyOpenSource/synology-csi/blob/main/deploy/example/storageclass-nfs.yaml).
- **NVMe/TCP namespaces (v1.3.0+)** — `protocol: nvme` on NVMe-capable hardware (e.g. PAS7700); driver picks the NAS and subsystem automatically. See [example StorageClass](https://github.com/SynologyOpenSource/synology-csi/blob/main/deploy/example/storageclass-nvme.yaml).

## Volume lifecycle

- **Cloning** — `volumeCloneSource` PVC cloning is advertised by the driver ([README](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md)).
- **Expansion** — PVCs can be expanded in place (iSCSI LUNs and shared folders) ([README](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md)).
- **Snapshots** — `VolumeSnapshot` support via the external-snapshotter with `description` and `is_locked` snapshot-class parameters; LUN snapshots make cloning and restore possible. See [Creating Volume Snapshot Classes](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md#creating-volume-snapshot-classes).
- **Capacity accuracy** — `GET_VOLUME_STATS` (v1.1.3+) reports actual LUN utilization to Kubernetes (v1.21+ `kubectl get pvc`/metrics) ([commits](https://github.com/SynologyOpenSource/synology-csi/tree/v1.1.3)).
- **LUN formatting options** — `formatOptions` (v1.1.2+) passes extra arguments to `mkfs.*` ([README](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md#creating-storage-classes)).

## iSCSI tuning and hardening (v1.2.1+)

- **Space reclamation** — `enableSpaceReclamation` triggers thin-space reclaim on Btrfs LUNs; may affect performance and free-space display ([README](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md#creating-storage-classes)).
- **FUA / Sync Cache** — `enableFuaSyncCache` opts LUNs into FUA and Sync Cache SCSI commands for explicit write flush semantics ([README](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md#creating-storage-classes)).

## DSM connectivity

- **Multi-NAS** — One `client-info` secret can list many DSM endpoints; StorageClasses route volumes by `dsm` ([Creating a Secret](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md#creating-a-secret)).
- **TLS certificate verification (v1.3.1+)** — HTTPS endpoints are verified against the system CA pool; self-signed certificates use `tlsCACert`/`tlsServerName`, and `insecureSkipVerify: true` is available (with warning logs) for legacy setups ([Creating a Secret](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md#creating-a-secret)).
- **Session resilience** — DSM login retries automatically on session timeout (v1.1.3+, [PR #56](https://github.com/SynologyOpenSource/synology-csi/pull/56)).
- **DSM platform support** — DSM 7.0+, DSM UC 3.1+, and DSME 1.0+ (v1.3.1); iSCSI multipath works with DSM UC hardware ([Prerequisites](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md#prerequisites)).

## Deployment and operations

- **Install script** — `scripts/deploy.sh` builds from a local Dockerfile or pulls `synology/synology-csi` from Docker Hub, then installs the controller and node plugin; full mode also installs the common snapshot controller and creates a default `synology-iscsi-storage` StorageClass (`Retain`) and `synology-snapshotclass` (`Delete`). Uninstall via `scripts/uninstall.sh` ([Installation](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md#installation)).
- **Kubernetes manifests** — Per-version manifests under `deploy/kubernetes/` for manual installation on Kubernetes v1.19 and v1.20+ ([manifests](https://github.com/SynologyOpenSource/synology-csi/tree/main/deploy/kubernetes)).
- **Helm** — The cluster typically consumes the driver through the community Helm chart `christian-schlichtherle/synology-csi-chart` (used by the install script since v1.1.2) ([chart repo](https://github.com/christian-schlichtherle/synology-csi-chart)).
- **Node containment** — chroot is enabled only for the node side and DSM commands execute through a tool executor (v1.2.1+); pod-security labels are applied to the driver namespace ([commits](https://github.com/SynologyOpenSource/synology-csi/tree/v1.2.1)).
- **synocli** — Bundled developer tool for ad-hoc DSM storage operations (e.g. `lun list`, v1.1.1+) ([README, Building](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md#building)).
- **Supported CSI sidecars** — Standard `external-snapshotter`, `livenessprobe`, `node-driver-registrar`, and `resizer` images, pinned per release in the manifests ([manifests](https://github.com/SynologyOpenSource/synology-csi/tree/main/deploy/kubernetes)).
