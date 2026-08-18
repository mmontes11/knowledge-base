---
upstream: https://github.com/SynologyOpenSource/synology-csi
last_updated: 2026-08-17
---

# synology-csi

The official [Container Storage Interface](https://github.com/container-storage-interface) (CSI) driver for Synology NAS, maintained by Synology. It provisions Kubernetes `PersistentVolume`s on DSM storage as thin-provisioned iSCSI LUNs (formatted `ext4` or `btrfs`), shared SMB/CIFS or NFS folders, and — since v1.3.0 — NVMe/TCP namespaces, with a single `client-info` secret covering one or many NAS endpoints. The driver advertises Read/Write Many access modes, cloning, expansion, and snapshots ([README](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md)).

- Upstream repository: [SynologyOpenSource/synology-csi](https://github.com/SynologyOpenSource/synology-csi)
- Documentation: [README](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md) (installation, CSI configuration, building, uninstall), manifests per Kubernetes version under [`deploy/kubernetes/`](https://github.com/SynologyOpenSource/synology-csi/tree/main/deploy/kubernetes)
- Container image: `synology/synology-csi` on [Docker Hub](https://hub.docker.com/r/synology/synology-csi)
- License: [Apache-2.0](https://github.com/SynologyOpenSource/synology-csi/blob/main/LICENSE)

## Standard documents

- [api-reference.md](api-reference.md) — CSI driver, configuration secrets, and `StorageClass`/`VolumeSnapshotClass` parameters
- [releases.md](releases.md) — the latest 10 releases, newest first
- [features.md](features.md) — feature areas by protocol, lifecycle, and connectivity
