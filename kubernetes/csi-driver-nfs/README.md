---
upstream: https://github.com/kubernetes-csi/csi-driver-nfs
last_updated: 2026-08-27
---

# csi-driver-nfs

Kubernetes CSI (Container Storage Interface) plugin that attaches a cluster to an existing, pre-configured NFSv3 or NFSv4 server: dynamic provisioning creates a new sub-directory on the NFS export for each bound PVC, static provisioning maps a `PersistentVolume` directly to a share, and snapshots/volume clones are stored on the share itself as tar archives. It runs as a controller `Deployment` (with csi-provisioner/csi-resizer/csi-snapshotter/livenessprobe sidecars) plus a node `DaemonSet`, with optional fsGroup-based POSIX permission handling, per-mount `chmod`, and ephemeral/inline volume support. It ships as a Helm chart (`charts/`) and as plain `kubectl` manifests (`deploy/`), and is GA.

- Upstream repository: [kubernetes-csi/csi-driver-nfs](https://github.com/kubernetes-csi/csi-driver-nfs)
- Documentation: in-repo Markdown under [docs/](https://github.com/kubernetes-csi/csi-driver-nfs/tree/master/docs) (no hosted docs site); [driver parameters](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/docs/driver-parameters.md)
- License: Apache-2.0 ([LICENSE](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/LICENSE))
- CSI driver name: `nfs.csi.k8s.io`; status GA; supported Kubernetes versions 1.21+ ([README](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/README.md))
- Install: [Helm chart](https://github.com/kubernetes-csi/csi-driver-nfs/tree/master/charts) (chart repo `https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/charts`) or [kubectl manifests](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/docs/install-nfs-csi-driver.md)

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
