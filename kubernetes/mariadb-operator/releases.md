---
upstream: https://github.com/mariadb-operator/mariadb-operator
last_updated: 2026-10-01
---

# mariadb-operator — releases

Latest 10 official releases, newest first. Check the ⚠️ entries before upgrading; for major upgrades also read the [upstream upgrade guides](https://github.com/mariadb-operator/mariadb-operator/tree/main/docs/releases).

## 26.10.1 — 2026-09-21

[Release page](https://github.com/mariadb-operator/mariadb-operator/releases/tag/26.10.1)

- Patch release on top of [26.10.0](https://github.com/mariadb-operator/mariadb-operator/releases/tag/26.10.0) — see that entry for the full `26.10.x` changelog.
- **MariaDB major-version upgrades**: new optional field `updateStrategy.mariadbAutoUpgradeEnabled` (disabled by default) sets `MARIADB_AUTO_UPGRADE=true` in the `mariadb` container so the image entrypoint runs `mariadb-upgrade` on start. Set it **before** bumping `spec.image` across a major version (e.g. `mariadb:11.8` → `mariadb:12.3`); without it the Pods restart in place against an unmigrated datadir and every query touching `mysql.proc` fails. [PR #1921](https://github.com/mariadb-operator/mariadb-operator/pull/1921)
- **MaxScale filters**: MaxScale now supports [filters](https://mariadb.com/docs/maxscale/reference/maxscale-filters) — declare them in `spec.filters` and reference them by name (in application order) from `spec.services[].filters`; declared filters are applied only when referenced by a service. `spec.volumes` was also added to the MaxScale Pod template, pairing with the existing `spec.volumeMounts`. [docs/maxscale.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/maxscale.md#filter-configuration), [PR #1923](https://github.com/mariadb-operator/mariadb-operator/pull/1923)
- ⚠️ **Upgrade**: read the [UPGRADE guide](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/releases/UPGRADE_26.10.1.md).

## 26.10.0 — 2026-09-18

[Release page](https://github.com/mariadb-operator/mariadb-operator/releases/tag/26.10.0)

- ⚠️ A patch release is available — **skip `26.10.0` and upgrade to [26.10.1](https://github.com/mariadb-operator/mariadb-operator/releases/tag/26.10.1) instead.**
- **MariaDB 12.3 support**: 12.3 LTS is supported and is now the default — the default `mariadb` image is bumped to `mariadb:12.3.3` (existing clusters keep the image pinned in their spec and are unaffected). [PR #1899](https://github.com/mariadb-operator/mariadb-operator/pull/1899)
- **Replication topology hardening**: no more `RESET MASTER` when configuring replicas (binary-log history is now preserved for PITR and multi-cluster); rejoining nodes with self-owned GTIDs are demoted with `MASTER_DEMOTE_TO_SLAVE`; correct `rpl_semi_sync_master_enabled` is enforced on every node; new fields `replication.semiSyncBootAsReplica` (boot `read_only` with semi-sync disabled) and `replication.semiSyncWaitNoSlave`; the liveness probe no longer restarts the Pod on an error-free administrative SQL-thread stop. [docs/replication.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/replication.md)
- ⚠️ **Galera behavior change**: the operator no longer recovers a Galera cluster where all members present an empty state (`00000000-0000-0000-0000-000000000000` UUID, `seqno: -1`); choose the bootstrap node explicitly via `forceClusterBootstrapInPod`. [docs/galera.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/galera.md#force-cluster-bootstrap)
- **Galera logical backups are restorable**: dumps exclude the Galera-managed `mysql.wsrep_*` system tables.
- **ZSTD compression**: `Backup`, `PhysicalBackup` and `PointInTimeRecovery` now support `compression: zstd` alongside `none`, `bzip2` and `gzip`, with a new `compressionThreads` field to cap the CPU threads used for compression. [PR #1874](https://github.com/mariadb-operator/mariadb-operator/pull/1874)
- **Helm**: the `mariadb-cluster` chart now supports `MaxScale` resources; chart releases include generated release notes for Renovate/Dependabot.
- Bug fixes: repeatable `mariadb-dump` flags in `spec.args` are no longer silently dropped; `PhysicalBackup` jobs inherit `podSecurityContext.seLinuxOptions` from the `MariaDB`; failed scheduled `Backup`/`SqlJob` runs report `CronJobFailed` instead of `CronJobScheduled`.
- ⚠️ **Upgrade**: read the [UPGRADE guide](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/releases/UPGRADE_26.10.0.md).

## 26.6.0 — 2026-06-06

[Release page](https://github.com/mariadb-operator/mariadb-operator/releases/tag/26.6.0)

- **Multi-cluster topology**: data replication between multiple clusters (within one Kubernetes cluster or across Kubernetes clusters), building on the replication or Galera topology for HA, DR, and blue-green deployments. [docs/multi-cluster.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/multi-cluster.md)
- **Maintenance mode**: composable modes on a `MariaDB` for planned work — cordon (removes Pods from Service endpoints), drain connections, and read-only. [docs/maintenance.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/maintenance.md)
- **Root password rotation**: rotate the MariaDB root password in place.
- **Helm OCI charts**: charts are now published as OCI artifacts under `oci://ghcr.io/mariadb-operator/charts/`. [docs/helm.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/helm.md)
- ⚠️ **Deprecations (breaking ahead)**: the `helm.mariadb.com` Helm repository and the `docker-registry*.mariadb.com` Docker registries are deprecated and will be removed in a future release. When migrating the `mariadb-operator-crds` chart to OCI, always `helm upgrade` in place — `helm uninstall` first deletes the CRDs and cascade-deletes all custom resources (downtime and data-plane restarts).
- ⚠️ **Upgrade**: the MariaDB data plane must be updated to 26.6.0 — set `spec.updateStrategy.autoUpdateDataPlane=true` on your `MariaDB` resources before upgrading the operator. [UPGRADE guide](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/releases/UPGRADE_26.6.0.md)

## 26.3.0 — 2026-03-13

[Release page](https://github.com/mariadb-operator/mariadb-operator/releases/tag/26.3.0)

- **Point-in-time recovery (PITR)**: automatic binary-log archival with `PointInTimeRecovery` and restore of a `MariaDB` to an arbitrary point in time, built on `PhysicalBackup` base backups. [docs/pitr.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/pitr.md)
- **On-demand physical backups** and **Azure Blob Storage** as a backup storage target. [docs/physical_backup.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/physical_backup.md)
- ⚠️ **Upgrade**: the MariaDB data plane must be updated to 26.3.0 (`spec.updateStrategy.autoUpdateDataPlane=true` before upgrading the operator). [UPGRADE guide](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/releases/UPGRADE_26.3.0.md)

## 25.10.4 — 2026-01-08

[Release page](https://github.com/mariadb-operator/mariadb-operator/releases/tag/25.10.4)

- ⚠️ **`mariadb-cluster` chart behavior change**: with default values, generated `Secret`s (including the root password, now generated by default) are prefixed with the release name when running multiple instances per namespace; set `mariadb.rootPasswordSecretKeyRef.generate=false` to stay backwards-compatible. [PR #1572](https://github.com/mariadb-operator/mariadb-operator/pull/1572)
- Bug fixes: duplicate `args` on generated backup/restore Job resources; new `pvcRetentionPolicy` field for backup/restore PVCs.

## 25.10.3 — 2025-12-24

[Release page](https://github.com/mariadb-operator/mariadb-operator/releases/tag/25.10.3)

- **`PhysicalBackup` target policy**: `spec.target` controls where backup jobs are scheduled — `Replica` (default, ready replicas only) or `PreferReplica` (falls back to the primary).
- **Kubernetes 1.35 support.**
- Backup/restore improvements and bug fixes.

## 25.10.2 — 2025-10-28

[Release page](https://github.com/mariadb-operator/mariadb-operator/releases/tag/25.10.2)

- **Replication topology generally available**: `replicas > 1` plus `spec.replication.enabled=true` provisions a primary/replica cluster with operator-managed replication user, status monitoring, and automatic primary failover ([PR #1441](https://github.com/mariadb-operator/mariadb-operator/pull/1441)).
- ⚠️ When upgrading *to* 25.10.x from earlier versions, follow the [UPGRADE guide](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/releases/UPGRADE_25.10.0.md) — the 25.10.0/25.10.1 notes mark both as containing a regression; `25.10.2` is the stable entry point of the 25.10.x line.

## 25.10.1 — 2025-10-23

[Release page](https://github.com/mariadb-operator/mariadb-operator/releases/tag/25.10.1)

- ⚠️ **Regression**: upstream notes say to upgrade to [25.10.2](https://github.com/mariadb-operator/mariadb-operator/releases/tag/25.10.2) directly.
- Part of the 25.10.x line introducing the replication (primary/replica) topology, GA'd in 25.10.2.

## 25.10.0 — 2025-10-23

[Release page](https://github.com/mariadb-operator/mariadb-operator/releases/tag/25.10.0)

- ⚠️ **Regression**: upstream notes say to upgrade to [25.10.2](https://github.com/mariadb-operator/mariadb-operator/releases/tag/25.10.2) directly.
- ⚠️ **Breaking for existing replication clusters**: a migration step is required — run the `hack/migrate_v25.10.0.sh` script over a saved `MariaDB` manifest; the default `repl` user credentials changed (`repl-password-$MARIADB` → `$MARIADB-repl-password`) and `gtid_strict_mode` is enabled by default.
- **Galera**: `spec.galera.primary.automaticFailover` renamed to `spec.galera.primary.autoFailover`; update the Galera data-plane images (or set `spec.updateStrategy.autoUpdateDataPlane=true`).
- [UPGRADE guide](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/releases/UPGRADE_25.10.0.md)

## 25.8.4 — 2025-09-19

[Release page](https://github.com/mariadb-operator/mariadb-operator/releases/tag/25.8.4)

- **New `ExternalMariaDB` kind**: declares an externally hosted MariaDB instance as a target for `User`, `Grant`, `Database`, `SqlJob`, and `Backup`. [docs/external_mariadb.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/external_mariadb.md)
- `VolumeSnapshot` locking optimization: the operator locks until the snapshot is created by the storage system rather than waiting for full data replication.
