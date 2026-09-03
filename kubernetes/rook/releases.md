---
upstream: https://github.com/rook/rook
last_updated: 2026-09-03
---

# rook — releases

Latest 10 official releases, newest first. Check the ⚠️ entries before upgrading.

## v1.20.7 — 2026-09-02

[Release page](https://github.com/rook/rook/releases/tag/v1.20.7)

- Ceph CSI updated to 3.17.1 for AES256K (new cephx key type) compatibility, continuing the [CVE-2025-30156](https://ceph.io/en/news/blog/2026/v20-2-4-v19-2-6-combo-released/) key rotation; the rook mgr module is unset when disabled; csi secrets are created when admin creds are provided for an external cluster.
- Mon: election strategy change is skipped when stretch mode is already enabled; osd: an error is returned when a noout update fails.

## v1.19.11 — 2026-09-02

[Release page](https://github.com/rook/rook/releases/tag/v1.19.11)

- Ceph CSI updated to 3.16.3 for AES256K (new cephx key type) compatibility; the rook mgr module is unset when disabled; device paths that already start with `/dev` are not prepended again.

## v1.20.6 — 2026-08-20

[Release page](https://github.com/rook/rook/releases/tag/v1.20.6)

- ⚠️ **Security**: Ceph [CVE-2025-30156](https://ceph.io/en/news/blog/2026/v20-2-4-v19-2-6-combo-released/) advisory — upgrade to Ceph v20.2.4 and rotate CephX keys per the [Rook advisory](https://medium.com/@b.blaine.gardner/rook-advisory-for-ceph-cve-2025-30156-cc1f8dee6da3).
- The rook mgr module is disabled; the actual error is reported when a cephx key fallback fails.

## v1.19.10 — 2026-08-20

[Release page](https://github.com/rook/rook/releases/tag/v1.19.10)

- ⚠️ **Security**: Ceph [CVE-2025-30156](https://ceph.io/en/news/blog/2026/v20-2-4-v19-2-6-combo-released/) advisory — upgrade to Ceph v19.2.6 and rotate CephX keys per the [Rook advisory](https://medium.com/@b.blaine.gardner/rook-advisory-for-ceph-cve-2025-30156-cc1f8dee6da3).
- CephCluster updates can now mute ceph warnings; the rook mgr module is disabled.

## v1.20.5 — 2026-08-19

[Release page](https://github.com/rook/rook/releases/tag/v1.20.5)

- ⚠️ **Security**: first patch for Ceph [CVE-2025-30156](https://ceph.io/en/news/blog/2026/v20-2-4-v19-2-6-combo-released/) — support for the new cephx key type (AES256K); upgrade to Ceph v20.2.4 and rotate CephX keys per the [Rook advisory](https://medium.com/@b.blaine.gardner/rook-advisory-for-ceph-cve-2025-30156-cc1f8dee6da3).
- Egress restrictions removed from the example NetworkPolicies; mon config is saved in the failover path so a failed-over mon registers on v1.

## v1.19.9 — 2026-08-19

[Release page](https://github.com/rook/rook/releases/tag/v1.19.9)

- ⚠️ **Security**: first patch for Ceph [CVE-2025-30156](https://ceph.io/en/news/blog/2026/v20-2-4-v19-2-6-combo-released/) — support for the new cephx key type (AES256K); upgrade to Ceph v19.2.6 and rotate CephX keys per the [Rook advisory](https://medium.com/@b.blaine.gardner/rook-advisory-for-ceph-cve-2025-30156-cc1f8dee6da3).
- Ceph base image updated to 20.2.2; workaround for a context race after an admin cephx rotation; ipv6 check added to the multus `hostTemplateConfig` curl.

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
