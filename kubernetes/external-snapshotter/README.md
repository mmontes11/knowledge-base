---
upstream: https://github.com/kubernetes-csi/external-snapshotter
last_updated: 2026-08-27
---

# external-snapshotter

The CSI Snapshotter is the Kubernetes implementation of the Container Storage Interface (CSI) snapshot feature: it ships the snapshot API surface (the `VolumeSnapshot*` and `VolumeGroupSnapshot*` CRDs), one cluster-wide snapshot controller, and a per-driver CSI sidecar used to take point-in-time snapshots of CSI persistent volumes — individual volume snapshots (GA since Kubernetes 1.20) and volume group snapshots (GA since v8.6.0) — and to create new volumes from those snapshots.

- Upstream repository: [kubernetes-csi/external-snapshotter](https://github.com/kubernetes-csi/external-snapshotter)
- Documentation: the [repository README](https://github.com/kubernetes-csi/external-snapshotter#readme) is the primary doc; the CSI project's site is [kubernetes-csi.github.io](https://kubernetes-csi.github.io)
- License: [Apache-2.0](https://github.com/kubernetes-csi/external-snapshotter/blob/master/LICENSE)
- API groups: `snapshot.storage.k8s.io/v1` (volume snapshots) and `groupsnapshot.storage.k8s.io/v1` (volume group snapshots; the older `v1beta2`/`v1beta1` group versions are still served, with a conversion webhook covering `v1beta1`↔`v1beta2`)
- Images: `registry.k8s.io/sig-storage/snapshot-controller`, `registry.k8s.io/sig-storage/csi-snapshotter`, `registry.k8s.io/sig-storage/snapshot-conversion-webhook`
- Deployed from [`k8s-infrastructure/infrastructure/external-snapshotter/`](https://github.com/mmontes11/k8s-infrastructure/tree/main/infrastructure/external-snapshotter) (namespace `storage`) pinned at `v8.5.0`: the cluster-wide snapshot controller plus the snapshot CRDs. The `csi-snapshotter` sidecar runs once per CSI driver alongside that driver's deployment.

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
