---
upstream: https://github.com/SynologyOpenSource/synology-csi
last_updated: 2026-08-17
---

# synology-csi — Releases

The latest 10 official releases, newest first. Upstream does not create GitHub Releases; versions are git tags on `main` pointing at the version-bump commit, so entries link to the tag and dates are the dates of those commits.

## 1.3.1 — 2026-07-29

[Tag v1.3.1](https://github.com/SynologyOpenSource/synology-csi/tree/v1.3.1)

- **Breaking (TLS):** DSM HTTPS endpoints are now verified to prevent credential exposure ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/4c5e192)). Self-signed DSM certificates require `tlsCACert` (plus `tlsServerName` when `host` is an IP); `insecureSkipVerify: true` restores the old behavior and logs a warning on every connection. Existing `https: true` setups with self-signed certificates will fail to log in until configured.
- Fix: synocli is copied into the builder image for the default `make` target ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/daae1c1)).
- Docs: DSME support and the NVMe/TCP StorageClass are documented in the README ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/5cb10ab)).

## 1.3.0 — 2026-05-20

[Tag v1.3.0](https://github.com/SynologyOpenSource/synology-csi/tree/v1.3.0)

- New NVMe/TCP storage backend: `protocol: nvme` creates NVMe namespaces (requires NVMe-capable hardware, e.g. a PAS7700) ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/cbe97aa), [example](https://github.com/SynologyOpenSource/synology-csi/blob/main/deploy/example/storageclass-nvme.yaml)).
- iSCSI: discovery uses `discovery -o new` so existing `iscsiadm` node settings are not overwritten ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/72de5ad)).
- Additional arguments can now be passed to the node csi-plugin ([PR #110](https://github.com/SynologyOpenSource/synology-csi/pull/110)).

## 1.2.1 — 2025-10-07

[Tag v1.2.1](https://github.com/SynologyOpenSource/synology-csi/tree/v1.2.1)

- iSCSI LUNs can enable space reclamation and FUA/Sync Cache SCSI commands via the `enableSpaceReclamation` / `enableFuaSyncCache` StorageClass parameters ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/9a9ebd0)).
- Pod-security admission labels are applied to the driver namespace ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/f1a262b)).
- Hardening: chroot is now enabled only for the node side and DSM commands run through a tool executor ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/bf76a99), [commit](https://github.com/SynologyOpenSource/synology-csi/commit/78e7a08)).

## 1.2.0 — 2024-08-27

[Tag v1.2.0](https://github.com/SynologyOpenSource/synology-csi/tree/v1.2.0)

- NFS: nodes are granted share access by explicit node address instead of a wildcard entry ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/e2cc8de)).
- Build: Go 1.21 by default with dependency updates ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/fbe1b7e)).
- Install script: uninstalls the Kubernetes v1.20 manifests ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/c2e1263)); deprecated `kubectl` short flags removed ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/d2cae9f)).

## 1.1.3 — 2023-11-08

[Tag v1.1.3](https://github.com/SynologyOpenSource/synology-csi/tree/v1.1.3)

- LUN description on DSM is populated from PVC info at CreateVolume ([PR #60](https://github.com/SynologyOpenSource/synology-csi/pull/60)).
- Driver advertises `GET_VOLUME_STATS` and reports more accurate LUN utilization ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/b502422), [commit](https://github.com/SynologyOpenSource/synology-csi/commit/9c19c7f)).
- DSM login retries on session timeout ([PR #56](https://github.com/SynologyOpenSource/synology-csi/pull/56)); Helm chart: custom kubelet path ([PR #57](https://github.com/SynologyOpenSource/synology-csi/pull/57)) and node-startup handling ([PR #55](https://github.com/SynologyOpenSource/synology-csi/pull/55)).

## 1.1.2 — 2023-06-06

[Tag v1.1.2](https://github.com/SynologyOpenSource/synology-csi/tree/v1.1.2)

- Helm installs now pull the community chart `christian-schlichtherle/synology-csi-chart` (fixes #8) ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/dbf1c3b)).
- New `formatOptions` StorageClass parameter passes extra arguments to `mkfs.*` ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/817a51b)).
- Images moved to the `synology/synology-csi` registry name ([PR #46](https://github.com/SynologyOpenSource/synology-csi/pull/46)); `RPC_VOLUME_MOUNT_GROUP` capability removed ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/278b4cb)).

## 1.1.1 — 2022-09-05

[Tag v1.1.1](https://github.com/SynologyOpenSource/synology-csi/tree/v1.1.1)

- DSM UC (Unified Cloud) support and iSCSI multipath ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/5762d9f)).
- `synocli` dev tool: `lun list` subcommand ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/63e8c1b)).

## 1.1.0 — 2022-04-29

[Tag v1.1.0](https://github.com/SynologyOpenSource/synology-csi/tree/v1.1.0)

- **Behavior change:** `protocol` defaults to iSCSI for backward compatibility (fixes #34) ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/fc33592)).
- Volume unstage failure fixed ([PR #15](https://github.com/SynologyOpenSource/synology-csi/pull/15)).
- csi-plugin containers now use `imagePullPolicy: IfNotPresent` (fixes #27) ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/f4cbfc8)).

## 1.0.1 — 2022-02-14

[Tag v1.0.1](https://github.com/SynologyOpenSource/synology-csi/tree/v1.0.1)

- iSCSI target now logs out on Unstage, avoiding stale sessions ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/7ba1bd8)).
- `btrfs-progs` added to the image ([commit](https://github.com/SynologyOpenSource/synology-csi/commit/228cbaa)).
- Deployment script improvements ([PR #24](https://github.com/SynologyOpenSource/synology-csi/pull/24)).

## 1.0.0 — 2021-08-31

[Tag v1.0.0](https://github.com/SynologyOpenSource/synology-csi/tree/v1.0.0)

- First public release: iSCSI, SMB/CIFS, and NFS protocols with cloning, expansion, and snapshot support ([initial commit](https://github.com/SynologyOpenSource/synology-csi/commit/dc05a79), [README](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md)).
