---
upstream: https://github.com/topolvm/topolvm
last_updated: 2026-08-17
---

# topolvm

Kubernetes CSI (Container Storage Interface) plugin that provisions block volumes from node-local **LVM** (Logical Volume Manager) volume groups instead of external or shared storage: the CSI controller creates logical volumes (standard or thin-provisioned) in the volume group on the node where the pod will run, and a topology/capacity-aware scheduling path (CSI storage capacity tracking, plus an optional kube-scheduler extender) keeps pods on nodes that have a matching device class and enough free capacity. It is composed of `topolvm-controller` (the CSI plugin), `topolvm-node` (node-side driver), `lvmd` (a per-node gRPC LVM service), and the optional `topolvm-scheduler` extender.

- Upstream repository: [topolvm/topolvm](https://github.com/topolvm/topolvm)
- Documentation: in-repo Markdown under [docs/](https://github.com/topolvm/topolvm/tree/main/docs) (there is no hosted docs site; `topolvm.readthedocs.io` no longer resolves)
- License: Apache-2.0 ([LICENSE](https://github.com/topolvm/topolvm/blob/main/LICENSE))
- Conformed CSI version: `1.5.0`; API group/version: `topolvm.io/v1`
- Supported Kubernetes versions: `1.33`–`1.35`; filesystems `ext4`, `xfs`, `btrfs` (beta); requires LVM2 `2.02.163` or later ([README](https://github.com/topolvm/topolvm/blob/main/README.md))

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
