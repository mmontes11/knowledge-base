---
upstream: https://github.com/rook/rook
last_updated: 2026-08-18
---

# rook

Rook is a cloud-native storage orchestrator for Kubernetes that deploys and manages [Ceph](https://ceph.com/) clusters as native Kubernetes workloads: it automates Ceph deployment, configuration, provisioning, scaling, upgrades, and monitoring so that block (RBD), shared filesystem (CephFS), and object (RGW/S3) storage become self-managing, self-scaling, and self-healing. Rook is a CNCF graduated project; applications consume the storage through Ceph CSI drivers.

- Upstream repository: [rook/rook](https://github.com/rook/rook)
- Documentation: [rook.io](https://rook.github.io/docs/rook/latest-release); source in the [Documentation/ tree](https://github.com/rook/rook/tree/master/Documentation)
- License: Apache-2.0 ([LICENSE](https://github.com/rook/rook/blob/master/LICENSE))
- API group/version: `ceph.rook.io/v1`
- Helm charts (in-repo, [deploy/charts/](https://github.com/rook/rook/tree/master/deploy/charts)): `rook-ceph` (operator), `rook-ceph-cluster` (Ceph cluster + CSI/monitoring add-ons), `ceph-csi-drivers` (CSI driver values library)

## Usage in this stack

Our infrastructure repository deploys Rook-Ceph through Helm: the `rook-ceph` operator chart plus the `rook-ceph-cluster` chart, with the `ceph-csi-drivers` values library enabling the RBD and CephFS CSI drivers. Since v1.20 the Ceph CSI operator is a required companion (chart dependency `ceph-csi-operator`, v1.0.4).

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
