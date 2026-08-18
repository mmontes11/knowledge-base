---
upstream: https://github.com/rook/rook
last_updated: 2026-08-18
---

# rook — releases

Latest 10 official releases, newest first. Check the ⚠️ entries before upgrading.

## v1.20.4 — 2026-08-13

[Release page](https://github.com/rook/rook/releases/tag/v1.20.4)

- Patch release: floating mon service created with correct selectors; CRUSH MSR rules supported for EC pools; external-cluster import script creates the mon secret before the rados namespace.

## v1.20.3 — 2026-07-28

[Release page](https://github.com/rook/rook/releases/tag/v1.20.3)

- Multisite CR support added to the `rook-ceph-cluster` Helm chart; custom NFS server port; toolbox image repository/tag configurable via Helm; external MGR endpoints deduplicated in EndpointSlices.

## v1.19.8 — 2026-07-28

[Release page](https://github.com/rook/rook/releases/tag/v1.19.8)

- Object store bucket policies are now clobbered (not merged) on modification; external-cluster volume attachments deleted during unmount.

## v1.20.2 — 2026-07-07

[Release page](https://github.com/rook/rook/releases/tag/v1.20.2)

- Ceph updated to 20.2.2 and `ceph-csi-operator` to v1.0.4; Kubernetes and Ceph version support matrix added to the docs; MGR NetworkPolicy restricted to ingress-only with example operand NetworkPolicy CRs.

## v1.20.1 — 2026-06-16

[Release page](https://github.com/rook/rook/releases/tag/v1.20.1)

- ⚠️ **Behavior change**: csi-addons is disabled by default.
- OSD device-class assignment can now be driven by node labels; install guide for two-node fenced clusters added.

## v1.19.7 — 2026-06-16

[Release page](https://github.com/rook/rook/releases/tag/v1.19.7)

- Default Ceph version set to 19.2.4; Helm ownership applied to Ceph CSI resources when the operator chart is configured.

## v1.20.0 — 2026-06-02

[Release page](https://github.com/rook/rook/releases/tag/v1.20.0)

- ⚠️ **Breaking**: the Ceph CSI operator is now required to manage CSI settings; CSI settings were removed from the `rook-ceph-operator-config` configmap and the `rook-ceph` Helm chart. New installs configure CSI via the Ceph-CSI `OperatorConfig`/`Driver` CRs or the [ceph-csi-drivers chart](https://rook.io/docs/rook/v1.20/Helm-Charts/csi-drivers-chart/) (existing default-CSI clusters upgrade as-is).
- Supported Kubernetes versions are v1.31 through v1.36.
- New: SSE-S3 with Vault Agent; unused CRUSH rules deleted by default after mgr start (opt out with `ROOK_DELETE_UNUSED_CRUSH_RULES=false`); concurrent multiple-cluster reconciliation declared stable; encrypted host-based OSD disk resize; `CephObjectStoreAccount` (experimental); two-node clusters with a floating mon (experimental).

## v1.19.6 — 2026-05-27

[Release page](https://github.com/rook/rook/releases/tag/v1.19.6)

- Prometheus rules synced from upstream ceph/ceph; a `cluster` label added to all scraped metrics.

## v1.18.11 — 2026-05-27

[Release page](https://github.com/rook/rook/releases/tag/v1.18.11)

- Disks are zapped for forceful OSD installation; fixed a swapped provisioner/plugin `PriorityClassName` in csi-addons.

## v1.19.5 — 2026-04-28

[Release page](https://github.com/rook/rook/releases/tag/v1.19.5)

- Helm ownership annotations added to CSI resources; `CSIMetadataRadosNamespace` parameter added to `CephFilesystemSubVolumeGroup`; MDS behavior fixed for CephFS with no active standby.
