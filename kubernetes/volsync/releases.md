---
upstream: https://github.com/backube/volsync
last_updated: 2026-08-16
---

# volsync — releases

Latest 10 official releases, newest first. Check the ⚠️ entries before upgrading.

## v0.16.0 — 2026-06-10

[Release page](https://github.com/backube/volsync/releases/tag/v0.16.0)

- **rsync-tls security hardening**: TLS 1.3 is now the minimum version, stunnel DH-parameter regeneration is eliminated, cipher suites follow the OpenShift TLS profile, and the controller's metrics endpoint checks the central OpenShift TLS profile.
- ⚠️ **Breaking**: the Syncthing `insecureAllowOldTLSVersions` option is removed as unused; rsync-tls peers running older versions may no longer be able to connect. Restic is updated to v0.18.1 and Rclone to v1.74.2.
- Krew CLI plugin is now published for macOS, and the restic mover supports Azure workload-identity environment variables.

## v0.15.0 — 2026-03-05

[Release page](https://github.com/backube/volsync/releases/tag/v0.15.0)

- ⚠️ **Metrics auth change**: the `kube-rbac-proxy` sidecar container is gone; metrics-endpoint authentication is now provided by the controller-runtime builtin. Re-check RBAC/service-auth assumptions for Prometheus scraping after upgrading.
- `moverVolumes` now allows NFS-type volumeMounts into the mover pod; Rclone is updated to v1.73.1 and `lost+found` is excluded for Rclone.

## v0.14.0 — 2025-11-20

[Release page](https://github.com/backube/volsync/releases/tag/v0.14.0)

- **New `moverVolumes` field**: advanced users can mount secrets or PVCs into their mover pods.
- Rclone `--checksum`, `--transfers`, and `--stats` options can be overridden via `RCLONE_*` environment variables in the rclone config secret; built with Golang 1.24, Syncthing v1.30.0, Rclone v1.71.2.

## v0.13.1 — 2025-09-25

[Release page](https://github.com/backube/volsync/releases/tag/v0.13.1)

- Bugfix: longer timeout when initializing restic repos that need ~10 seconds or more for `restic cat config` to resolve.

## v0.13.0 — 2025-07-15

[Release page](https://github.com/backube/volsync/releases/tag/v0.13.0)

- **arm64**: multi-architecture VolSync images (AMD64 + ARM64) are now released on quay.io; restic is updated to v0.18.0 and Syncthing to v1.29.7.
- Bugfix: restic cache PVC name-collision when a `ReplicationSource` and `ReplicationDestination` with the same name exist in the same namespace.

## v0.12.1 — 2025-03-26

[Release page](https://github.com/backube/volsync/releases/tag/v0.12.1)

- ⚠️ **Security-only release**: fixes CVE-2025-22869 (`golang.org/x/crypto`) and CVE-2025-22868 (`golang.org/x/oauth2`).

## v0.12.0 — 2025-03-03

[Release page](https://github.com/backube/volsync/releases/tag/v0.12.0)

- `imagePullSecrets` from the VolSync controller's namespace are now copied into the mover's namespace so mover jobs can pull private images; Syncthing is updated to v1.29.2 and the kube-rbac-proxy is made configurable.
- Bugfixes: a failed `EnsurePVCFromSrc` now errors out for all movers, and Job/Service names longer than 63 characters caused long CR names.

## v0.11.0 — 2024-11-08

[Release page](https://github.com/backube/volsync/releases/tag/v0.11.0)

- **New spec options**: `moverAffinity`, `cleanupTempPVC` (delete the dynamically provisioned destination PVC after replication), `cleanupCachePVC` (restic), and restic `enableFileDeletion` when restoring into an existing PVC.
- Restic is updated to v0.17.0, Syncthing to v1.27.12.

## v0.10.0 — 2024-07-31

[Release page](https://github.com/backube/volsync/releases/tag/v0.10.0)

- rsync-tls fixes for PVCs with many files at the root and for files whose names begin with `#`; mover jobs can run in debug mode; Syncthing is updated to v1.27.8.

## v0.9.1 — 2024-04-12

[Release page](https://github.com/backube/volsync/releases/tag/v0.9.1)

- Restic can now restore from an empty destination, and `lost+found` is ignored when checking for a source volume to be empty.
