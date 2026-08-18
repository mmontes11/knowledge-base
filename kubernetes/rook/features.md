---
upstream: https://github.com/rook/rook
last_updated: 2026-08-18
---

# rook — features

Significant feature areas, each with a link to the matching upstream doc. The [documentation site](https://rook.github.io/docs/rook/latest-release) (source: [Documentation/ tree](https://github.com/rook/rook/tree/master/Documentation)) is authoritative.

## Storage types and Ceph CSI

Rook exposes all storage types (RBD block, CephFS, object) to pods through Ceph CSI drivers. The `ceph-csi-drivers` values library enables the RBD and CephFS drivers by default; NFS and NVMe-oF drivers exist but are disabled. Since v1.20, CSI settings are managed exclusively by the Ceph CSI operator (⚠️ required; settings moved out of the Rook operator chart/configmap).

- [Ceph CSI configuration](https://rook.io/docs/rook/v1.20/Storage-Configuration/Ceph-CSI/csi-configuration/)
- [csi-drivers Helm chart](https://rook.io/docs/rook/v1.20/Helm-Charts/csi-drivers-chart/)
- [rook-ceph operator chart](https://rook.io/docs/rook/v1.20/Helm-Charts/operator-chart/)

## Cluster topologies

- **Host-based clusters** (raw or prepared raw devices): [host-cluster.md](https://github.com/rook/rook/blob/master/Documentation/CRDs/Cluster/host-cluster.md)
- **Stretch clusters** (three-data-center): [stretch-cluster.md](https://github.com/rook/rook/blob/master/Documentation/CRDs/Cluster/stretch-cluster.md)
- **PVC-based OSDs** (no host disks required): [pvc-cluster.md](https://github.com/rook/rook/blob/master/Documentation/CRDs/Cluster/pvc-cluster.md)
- **Two-node clusters** with a floating mon (experimental since v1.20; [v1.20.0 release](https://github.com/rook/rook/releases/tag/v1.20.0))
- **External clusters**: import a pre-existing Ceph cluster into Rook/CSI: [external-cluster docs](https://github.com/rook/rook/tree/master/Documentation/CRDs/Cluster/external-cluster)

## Object storage

- **Multisite (RGW)** across datacenters with `CephObjectRealm`/`CephObjectZoneGroup`/`CephObjectZone`; multisite CRs are supported by the `rook-ceph-cluster` chart since v1.20.3: [realm CRD docs](https://github.com/rook/rook/blob/master/Documentation/CRDs/Object-Storage/ceph-object-realm-crd.md)
- **RGW accounts** (experimental since v1.20): [object accounts doc](https://github.com/rook/rook/blob/master/Documentation/Storage-Configuration/Object-Storage-RGW/ceph-object-accounts.md)
- **SSE-S3 with Vault Agent** (v1.20): [object store CRD docs](https://github.com/rook/rook/blob/master/Documentation/CRDs/Object-Storage/ceph-object-store-crd.md)
- **S3 bucket event notifications** via `CephBucketTopic` and `CephBucketNotification`
- **ObjectBucketClaim** (Object Bucket API) backed by the Ceph bucket provisioner ([lib-bucket-provisioner](https://github.com/k8s-object-storage/lib-bucket-provisioner))

## Disaster recovery

- **RBD mirroring** (`CephRBDMirror`) for asynchronous block-mirror groups: [CRD docs](https://github.com/rook/rook/blob/master/Documentation/CRDs/Block-Storage/ceph-rbd-mirror-crd.md)
- **CephFS mirroring** (`CephFilesystemMirror`) for asynchronous snapshot-based replication: [CRD docs](https://github.com/rook/rook/blob/master/Documentation/CRDs/Shared-Filesystem/ceph-fs-mirror-crd.md)

## Monitoring and operations

- The `rook-ceph-cluster` chart ships Prometheus rules and service monitors under [deploy/charts/rook-ceph/prometheus/](https://github.com/rook/rook/tree/master/deploy/charts/rook-ceph/prometheus); rules are synced from upstream ceph/ceph and all metrics carry a `cluster` label (since v1.19.6).
- **Toolbox pods** for interactive `ceph` CLI access; image repository/tag configurable through Helm since v1.20.3.
- **Upgrade path**: see the [upgrade guide](https://rook.github.io/docs/rook/v1.20/Upgrade/rook-upgrade/); the [QuickStart](https://rook.github.io/docs/rook/latest-release/Getting-Started/quickstart) covers initial deployment.
