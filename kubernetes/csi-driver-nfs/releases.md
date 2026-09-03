---
upstream: https://github.com/kubernetes-csi/csi-driver-nfs
last_updated: 2026-08-27
---

# csi-driver-nfs — releases

Latest 10 official releases, newest first. Each `vX.Y.Z` release ships the driver images, the matching Helm chart (`charts/vX.Y.Z/`) and the raw manifest set (`deploy/vX.Y.Z/`) together. The current release line is 4.13; the [README compatibility table](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/README.md) tracks master, v4.13.4, v4.12.1, and v4.11.0 — all GA on Kubernetes 1.21+. Check the ⚠️ entries before upgrading.

## v4.13.4 — 2026-07-01

[Release page](https://github.com/kubernetes-csi/csi-driver-nfs/releases/tag/v4.13.4)

- Fixed [CVE-2026-25680](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1172).
- Updated the CSI sidecar images (csi-provisioner/resizer/snapshotter/livenessprobe/node-driver-registrar) to their latest versions ([#1176](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1176)).
- New `revisionHistoryLimit` chart parameter for the controller and node workloads ([#1178](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1178)); stricter `volumeHandle` validation ([#1181](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1181)).

## v4.13.3 — 2026-06-15

[Release page](https://github.com/kubernetes-csi/csi-driver-nfs/releases/tag/v4.13.3)

- ⚠️ `NodePublishVolume` now **detects and remounts stale NFS mounts** ([#1109](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1109)) — previously a stale (export gone/re-added) mount could pass the "already mounted" check and never recover.
- `mountPermissions` now applied via `syscall.Chmod`, so setgid/setuid/sticky bits are handled correctly ([#1110](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1110)).
- Snapshot restore now preserves file timestamps ([#1120](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1120)); a `VolumeSnapshotClass` with custom `server`/`share` can no longer override the source volume's mount in `CreateSnapshot` ([#1159](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1159)); fixed a liveness-probe port conflict during controller rolling updates ([#1156](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1156)); fixes CVE-2026-29181 and CVE-2026-35469.

## v4.13.2 — 2026-04-16

[Release page](https://github.com/kubernetes-csi/csi-driver-nfs/releases/tag/v4.13.2)

- Stopped the noisy `VolumeAttributesClass` error logs in the CSI sidecar containers ([#1049](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1049)).
- Fixed [CVE-2026-33186](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1077); rebuilt with go1.25.9 ([#1085](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1085)).
- Added mount-path validation when creating new NFS volumes ([#1089](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1089)).

## v4.13.1 — 2026-02-10

[Release page](https://github.com/kubernetes-csi/csi-driver-nfs/releases/tag/v4.13.1)

- Mount-path validation on the node (publish) side ([#1041](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1041)); release-tools update ([#1043](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1043)) — a small maintenance release.

## v4.13.0 — 2026-02-02

[Release page](https://github.com/kubernetes-csi/csi-driver-nfs/releases/tag/v4.13.0)

- ⚠️ New `--enable-snapshot-compression` flag (default `true`): with `false`, snapshots are stored as uncompressed `.tar` archives instead of `.tar.gz`, which speeds up snapshot of already-compressed data. Restore auto-detects the archive format, so pre-existing compressed snapshots stay compatible ([#1011](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1011)).
- Optional health check for node-driver-registrar ([#1026](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1026)); csi-provisioner upgraded to v6.1.0 ([#1020](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1020)); removed the vendor-specific cloud-provider-azure dependency ([#1024](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1024)).
- Fixed CVE-2025-52881 ([#1003](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1003)), CVE-2025-58181 ([#1007](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1007)) and CVE-2025-13281 ([#1021](https://github.com/kubernetes-csi/csi-driver-nfs/pull/1021)).

## v4.12.1 — 2025-10-13

[Release page](https://github.com/kubernetes-csi/csi-driver-nfs/releases/tag/v4.12.1)

- Maintenance release: CSI release-tools update ([#988](https://github.com/kubernetes-csi/csi-driver-nfs/pull/988)) — no behavioral change.

## v4.12.0 — 2025-10-09

[Release page](https://github.com/kubernetes-csi/csi-driver-nfs/releases/tag/v4.12.0)

- Added the `POD_NAMESPACE` environment variable to the driver container ([#901](https://github.com/kubernetes-csi/csi-driver-nfs/pull/901)).
- ⚠️ Volume/snapshot **creation now respects `mountOptions` from the provisioner/snapshotter secret** ([#911](https://github.com/kubernetes-csi/csi-driver-nfs/pull/911)) — clusters whose NFS server needs non-default mount options must set the documented `csi.storage.k8s.io/*-secret-*` parameters; creation previously ignored them.
- Fixed a goroutine leak when CSI calls time out ([#907](https://github.com/kubernetes-csi/csi-driver-nfs/pull/907)); sidecar image upgrades (csi-resizer v1.13.2, csi-snapshotter v8.2.1).

## v4.11.0 — 2025-03-18

[Release page](https://github.com/kubernetes-csi/csi-driver-nfs/releases/tag/v4.11.0)

- New: `mountOptions` parameter support in `VolumeSnapshotClass` ([#879](https://github.com/kubernetes-csi/csi-driver-nfs/pull/879)).
- ⚠️ `DeleteVolume` now removes a volume's parent sub-directory **only if it is empty** ([#878](https://github.com/kubernetes-csi/csi-driver-nfs/pull/878)). v4.10.0 had added unconditional parent-directory removal ([#770](https://github.com/kubernetes-csi/csi-driver-nfs/pull/770)), which could delete a shared parent still in use by other PVCs.
- Fixed the chart affinity misconfiguration ([#860](https://github.com/kubernetes-csi/csi-driver-nfs/pull/860), [#862](https://github.com/kubernetes-csi/csi-driver-nfs/pull/862)) and the `onDelete` chart config passthrough ([#859](https://github.com/kubernetes-csi/csi-driver-nfs/pull/859)).

## v4.10.0 — 2025-01-24

[Release page](https://github.com/kubernetes-csi/csi-driver-nfs/releases/tag/v4.10.0)

- ⚠️ Added a timeout on NFS mount calls to fix the stuck-mount issue ([#792](https://github.com/kubernetes-csi/csi-driver-nfs/pull/792)).
- ⚠️ `DeleteVolume` now also **removes the parent sub-directory** ([#770](https://github.com/kubernetes-csi/csi-driver-nfs/pull/770)) — with `subDir` layouts where multiple PVCs share a parent directory, deleting one PVC could remove the directory other volumes relied on; narrowed to empty parents only in v4.11.0.
- `CriticalAddonsOnly` toleration added to the controller pod ([#780](https://github.com/kubernetes-csi/csi-driver-nfs/pull/780)); CSI spec updated to `v1.10.0` ([#796](https://github.com/kubernetes-csi/csi-driver-nfs/pull/796)).

## v4.9.0 — 2024-09-03

[Release page](https://github.com/kubernetes-csi/csi-driver-nfs/releases/tag/v4.9.0)

- Fixed an unmount-detection failure that could leave stale mounts behind ([#718](https://github.com/kubernetes-csi/csi-driver-nfs/pull/718), [#719](https://github.com/kubernetes-csi/csi-driver-nfs/pull/719)).
- ⚠️ Upgraded csi-provisioner to v5.0.2 to fix a stuck-PV-deletion issue ([#734](https://github.com/kubernetes-csi/csi-driver-nfs/pull/734), [#750](https://github.com/kubernetes-csi/csi-driver-nfs/pull/750)); added PV patch permission for clusters with the `HonorPVReclaimPolicy` gate enabled ([#725](https://github.com/kubernetes-csi/csi-driver-nfs/pull/725)).
- Fixed the delete-volume error in archive deletion mode ([#754](https://github.com/kubernetes-csi/csi-driver-nfs/pull/754)); mount-utils bump treats `ENODEV` as a corrupted mount ([#743](https://github.com/kubernetes-csi/csi-driver-nfs/pull/743)).

## Upgrade notes

- No features were removed in this window and no API/CSI-driver rename happened, but the ⚠️ items to check before upgrading are the `DeleteVolume` parent-directory cleanup change (v4.10.0 → v4.11.0), the mount-call timeout, and the honor-secret `mountOptions` on creation — they matter if you use shared `subDir` layouts, unreliable NFS servers, or servers requiring non-default mount options.
- Snapshot archives are format-detected on restore (`.tar.gz` or `.tar`), so changing `enableSnapshotCompression` in flight is safe.
- Supported release lines as of the last upstream README update: master, v4.13.4, v4.12.1, v4.11.0 — all GA, Kubernetes 1.21+ ([README](https://github.com/kubernetes-csi/csi-driver-nfs/blob/master/README.md)).
