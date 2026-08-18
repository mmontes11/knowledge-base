---
upstream: https://github.com/rook/rook
last_updated: 2026-08-18
---

# rook — API reference

Rook registers nineteen custom resource kinds under API group/version **`ceph.rook.io/v1`**: `v1` is the only served and storage version and every kind is namespaced. Go types live in [`pkg/apis/ceph.rook.io/v1/`](https://github.com/rook/rook/tree/master/pkg/apis/ceph.rook.io/v1) (kind registration in [register.go](https://github.com/rook/rook/blob/master/pkg/apis/ceph.rook.io/v1/register.go)).

`CephCluster` is the root resource (mon, mgr, and OSD daemons; storage selection; networking); the other kinds configure storage classes, daemons, and client objects that attach to a cluster.

| Kind | Purpose | Upstream API docs |
| ---- | ------- | ----------------- |
| `CephClient` | CephX user credentials so non-Kubernetes clients can authenticate to the cluster. | [CRD docs](https://github.com/rook/rook/blob/master/Documentation/CRDs/ceph-client-crd.md) |
| `CephCluster` | Defines and manages a complete Ceph cluster: mon/mgr/OSD layout, storage device selection, networking, health. | [CRD docs](https://github.com/rook/rook/blob/master/Documentation/CRDs/Cluster/ceph-cluster-crd.md) |
| `CephBlockPool` | Data pool for RBD; replicated or erasure-coded. | [CRD docs](https://github.com/rook/rook/blob/master/Documentation/CRDs/Block-Storage/ceph-block-pool-crd.md) |
| `CephBlockPoolRadosNamespace` | Rados namespace within a pool for Ceph CSI isolation. | [CRD docs](https://github.com/rook/rook/blob/master/Documentation/CRDs/Block-Storage/ceph-block-pool-rados-namespace-crd.md) |
| `CephRBDMirror` | RBD mirroring daemons for asynchronous block-storage disaster recovery. | [CRD docs](https://github.com/rook/rook/blob/master/Documentation/CRDs/Block-Storage/ceph-rbd-mirror-crd.md) |
| `CephFilesystem` | CephFS filesystem: MDS daemons and metadata/data pools. | [CRD docs](https://github.com/rook/rook/blob/master/Documentation/CRDs/Shared-Filesystem/ceph-filesystem-crd.md) |
| `CephFilesystemMirror` | CephFS mirroring daemons for asynchronous snapshot-based DR. | [CRD docs](https://github.com/rook/rook/blob/master/Documentation/CRDs/Shared-Filesystem/ceph-fs-mirror-crd.md) |
| `CephFilesystemSubVolumeGroup` | CephFS subvolume group for Ceph CSI organization and quotas. | [CRD docs](https://github.com/rook/rook/blob/master/Documentation/CRDs/Shared-Filesystem/ceph-fs-subvolumegroup-crd.md) |
| `CephNFS` | NFS-Ganesha gateway cluster exposing CephFS exports over NFS. | [CRD docs](https://github.com/rook/rook/blob/master/Documentation/CRDs/ceph-nfs-crd.md) |
| `CephNVMeOFGateway` | NVMe over Fabrics gateway serving RBD pools over NVMe-oF. | — (no dedicated CRD doc page) |
| `CephObjectStore` | RGW (S3-compatible) object store: daemons, pools, endpoints, encryption settings. | [CRD docs](https://github.com/rook/rook/blob/master/Documentation/CRDs/Object-Storage/ceph-object-store-crd.md) |
| `CephObjectStoreUser` | S3 access-key user credential for an object store. | [CRD docs](https://github.com/rook/rook/blob/master/Documentation/CRDs/Object-Storage/ceph-object-store-user-crd.md) |
| `CephObjectStoreAccount` | RGW account binding; experimental since v1.20, referenced via `CephObjectStoreUser.spec.accountRef`. | [object accounts doc](https://github.com/rook/rook/blob/master/Documentation/Storage-Configuration/Object-Storage-RGW/ceph-object-accounts.md) |
| `CephObjectRealm` | Multisite realm: shared identity/data group across zones (RGW multisite). | [CRD docs](https://github.com/rook/rook/blob/master/Documentation/CRDs/Object-Storage/ceph-object-realm-crd.md) |
| `CephObjectZoneGroup` | Multisite zone group within a realm. | [CRD docs](https://github.com/rook/rook/blob/master/Documentation/CRDs/Object-Storage/ceph-object-zonegroup-crd.md) |
| `CephObjectZone` | Multisite zone hosting RGW daemons. | [CRD docs](https://github.com/rook/rook/blob/master/Documentation/CRDs/Object-Storage/ceph-object-zone-crd.md) |
| `CephBucketTopic` | S3 bucket event-notification topic. | — (no dedicated CRD doc page) |
| `CephBucketNotification` | S3 bucket event-notification rule targeting a topic. | — (no dedicated CRD doc page) |
| `CephCOSIDriver` | Ceph CSI driver settings; since v1.20 replaces the operator configmap knobs (see [CSI configuration](https://rook.io/docs/rook/v1.20/Storage-Configuration/Ceph-CSI/csi-configuration/)). | [CSI configuration docs](https://rook.io/docs/rook/v1.20/Storage-Configuration/Ceph-CSI/csi-configuration/) |

Additional kinds in a separate group, provided by the Ceph bucket provisioner ([lib-bucket-provisioner](https://github.com/k8s-object-storage/lib-bucket-provisioner)):

| Kind | API group/version | Purpose |
| ---- | ----------------- | ------- |
| `ObjectBucket` / `ObjectBucketClaim` | `objectbucket.io/v1alpha1` | Object Bucket API claim for RGW buckets. |

Notes:

- There is no standalone `crds.yaml` in the repository: the CRDs are bundled with the `rook-ceph` Helm chart in [deploy/charts/rook-ceph/templates/resources.yaml](https://github.com/rook/rook/blob/master/deploy/charts/rook-ceph/templates/resources.yaml) and installed by Helm. All 19 `ceph.rook.io` CRDs are namespaced.
- `CephCluster` topology variants (host, stretch, PVC-based, network providers) are documented under [Documentation/CRDs/Cluster/](https://github.com/rook/rook/tree/master/Documentation/CRDs/Cluster), including an [external-cluster](https://github.com/rook/rook/tree/master/Documentation/CRDs/Cluster/external-cluster) folder for importing pre-existing Ceph clusters.
- Field-level documentation is intentionally not duplicated here; follow the per-kind links above.
