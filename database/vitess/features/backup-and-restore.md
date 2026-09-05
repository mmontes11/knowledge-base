---
upstream: https://github.com/vitessio/vitess
last_updated: 2026-09-04
---

# vitess — Backup and restore

Backup and restore are integrated into [tablets](tablet.md) managed by Vitess: a [vttablet](https://vitess.io/docs/24.0/concepts/vttablet/) can take a backup of its [shard's](shard.md) data, and any non-primary tablet can restore one. Backups serve two purposes — data protection (full and point-in-time restore) and provisioning, where new tablets are seeded from an existing backup instead of starting empty. Vitess is deliberately pluggable on both axes: where backup artifacts live (a *BackupStorage* service) and how the data is captured (a *BackupEngine*), and it tracks positions with MySQL GTIDs so full and incremental backups can be taken on any tablet in the shard and restored on any other.

Authoritative references: [Backup and restore (user guide)](https://vitess.io/docs/24.0/user-guides/operating-vitess/backup-and-restore/) · [vtbackup program reference](https://vitess.io/docs/24.0/reference/programs/vtbackup/)

## What it is

- **Full backup.** A consistent snapshot of the entire shard dataset, produced by a backup engine while the tablet is quiesced or (with `xtrabackup`) while it keeps serving. The backup is a point in time of the data.
- **Incremental backup.** A copy of the MySQL binary logs covering the change from one position to another — always a binlog copy, regardless of which engine took the full backup. Cheap and fast; typically interleaved with periodic fulls (e.g. daily full, hourly incremental).
- **Full restore.** Wipes a non-primary tablet's MySQL data, loads the dataset from a full backup, restarts mysqld, and rejoins the shard's replication stream to catch up. Used to seed new replicas and repair corrupted ones.
- **Point-in-time restore (PITR).** Loads a full backup, then applies zero or more incremental backups up to a target timestamp (1s granularity, v18+) or exact GTID position. The tablet ends as `DRAINED` — it does *not* rejoin replication — so its data stays static for inspection or copy-out (e.g. recovering from an accidental write).
- **Bootstrapping.** A restore triggered at startup by the `--restore-from-backup` flag; how new tablets in an existing shard get their data without a manual step.
- **BackupStorage.** The pluggable service that stores backup artifacts, organized by keyspace and shard under a configured root.
- **BackupEngine.** The pluggable technology that generates backup data and (on restore) how it is re-applied. A restore always uses the engine recorded in the backup's MANIFEST, not the one currently configured.
- **MANIFEST.** A per-backup JSON metadata file (engine, position, timestamps, hostname, file name, stream/stripe settings, compression) stored alongside the backup data.
- **Safety default.** Vitess refuses to restore onto a `PRIMARY` tablet by default; any non-primary tablet is eligible.

## How it works

- **Backup flow.** `vtctldclient Backup`/`BackupShard` calls the tablet's gRPC `TabletManager.Backup` ([`go/vt/vttablet/tabletmanager/rpc_backup.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/rpc_backup.go), service defined in [`proto/tabletmanagerservice.proto`](https://github.com/vitessio/vitess/blob/main/proto/tabletmanagerservice.proto)). The tablet's type transitions (e.g. to `BACKUP`), replication is caught up, then [`go/vt/mysqlctl/backup.go`](https://github.com/vitessio/vitess/blob/main/go/vt/mysqlctl/backup.go) runs the configured engine and streams the output — compressed by default — into the configured BackupStorage under `<keyspace>/<shard>/<timestamp>.<tablet-alias>/`.
- **Storage services.** One of five plugins, selected by `--backup-storage-implementation`: `file` (shared storage, e.g. NFS), `gcs`, `s3`, `ceph` (S3 API object gateway), `azblob` (Azure Blob). Each has its own location flags (bucket/root, region, account/container, endpoint config).
- **Backup engines.** Selected by `--backup-engine-implementation`:
  - `builtin` (default) — shuts down mysqld and copies all database files. Simple but not non-blocking.
  - `xtrabackup` — Percona XtraBackup physical, online backup; the recommended production engine. Requires `--xtrabackup-user` (a user authorized via `auth_socket` with the XtraBackup privileges) and, additionally for MySQL 8.0, `--xtrabackup-stream-mode=xbstream`.
  - `mysqlshell` (EXPERIMENTAL) — logical backup via `mysqlsh` dump into a target directory of plain-text files. Useful to flush out silent corruption that physical backups can carry. It writes thousands of files directly to a backend (`--mysql-shell-backup-location`); the Vitess backup location only holds the MANIFEST pointing at them.
- **Incremental backups.** `Backup`/`BackupShard` with `--incremental-from-pos` set to a GTID position, a prior backup name, or `auto` (last successful backup). Vitess rotates the active binlog and backs up whole binlog files covering the requested position. If no writes occurred since, the backup is *empty* — a success that produces no artifacts. If the required binlogs were already purged, the backup fails (another tablet in the shard may still have them).
- **Restore flow.** `vtctldclient RestoreFromBackup` (optionally with `--restore-to-timestamp`, `--restore-to-pos`, or `--dry-run` to validate the restore path without touching the tablet) maps the target onto a sequence — one full plus the incrementals that bridge the gap — wipes the tablet's MySQL, applies the engine's restore, and leaves a full restore replicating again or a PITR at `DRAINED`.
- **Compression.** Backups are compressed in-process by default with `pargzip` (gzip-compatible). Built-in engines: `pargzip`, `pgzip`, `lz4`, `zstd` for compression; `pgzip`, `lz4`, `zstd` for decompression. `--compression-engine-name` is written into the MANIFEST so restores pick the right decompressor even after the flag changes. External compressors/decompressors (`--external-compressor`, `--external-decompressor`) run as child processes streaming through STDIN/STDOUT, letting you pin CPU priority or cores.
- **Init SQL before backup.** `--init-backup-sql-queries` (plus `--init-backup-tablet-types`, `--init-backup-sql-timeout`, `--init-backup-sql-fail-on-error`) runs SQL after replication catch-up but before the backup begins — e.g. `OPTIMIZE LOCAL TABLE` to shrink a fragmented shard before backing it up.
- **Concurrency.** Backup and restore copy/compress multiple files in parallel: `--concurrency` on vtctl backup commands, `--restore-concurrency` on vttablet.
- **vtbackup (standalone).** A batch program ([`go/cmd/vtbackup/`](https://github.com/vitessio/vitess/tree/main/go/cmd/vtbackup)) for scheduled, low-disruption backups: it restores the most recent backup into a throw-away mysqld, replicates from the current shard primary until caught up, takes a new backup, and prunes old ones per `--min-backup-interval`, `--min-retention-time`, and `--min-retention-count`. It never registers a tablet in the topology, so serving is untouched; `--initial-backup` can seed a brand-new shard with an empty backup.
- **Positioning is tablet-independent.** Because GTIDs identify state, full and incremental backups can come from different tablets and restores can target any non-primary tablet.
- **Hooks.** Scripts in `${VTROOT}/vthook/` run at defined points; `vttablet_restore_done` fires when a restore completes (success or failure) with `TM_RESTORE_DATA_START_TS`, `TM_RESTORE_DATA_STOP_TS`, `TM_RESTORE_DATA_DURATION`, `TM_RESTORE_DATA_BACKUP_ENGINE`, and `TM_RESTORE_DATA_ERROR` (on failure) in the environment.
- **Metrics.** Backup/restore timing and status are exported by the `backupstats` package for Prometheus.

## Key components

| Component | Where | Role |
| --------- | ----- | ---- |
| `BackupStorage` interface | [`go/vt/mysqlctl/backupstorage/interface.go`](https://github.com/vitessio/vitess/blob/main/go/vt/mysqlctl/backupstorage/interface.go) | Contract for storing, reading, listing, and pruning backups |
| Storage plugins | [`go/vt/mysqlctl/filebackupstorage/file.go`](https://github.com/vitessio/vitess/blob/main/go/vt/mysqlctl/filebackupstorage/file.go), [`gcsbackupstorage/gcs.go`](https://github.com/vitessio/vitess/blob/main/go/vt/mysqlctl/gcsbackupstorage/gcs.go), [`s3backupstorage/s3.go`](https://github.com/vitessio/vitess/blob/main/go/vt/mysqlctl/s3backupstorage/s3.go) (+ [`retryer.go`](https://github.com/vitessio/vitess/blob/main/go/vt/mysqlctl/s3backupstorage/retryer.go)), [`cephbackupstorage/ceph.go`](https://github.com/vitessio/vitess/blob/main/go/vt/mysqlctl/cephbackupstorage/ceph.go), [`azblobbackupstorage/azblob.go`](https://github.com/vitessio/vitess/blob/main/go/vt/mysqlctl/azblobbackupstorage/azblob.go) | The five shipped BackupStorage implementations |
| `BackupEngine` interface | [`go/vt/mysqlctl/backupengine.go`](https://github.com/vitessio/vitess/blob/main/go/vt/mysqlctl/backupengine.go) | Contract for creating and restoring backups |
| Engines | [`go/vt/mysqlctl/builtinbackupengine.go`](https://github.com/vitessio/vitess/blob/main/go/vt/mysqlctl/builtinbackupengine.go), [`mysqlshellbackupengine.go`](https://github.com/vitessio/vitess/blob/main/go/vt/mysqlctl/mysqlshellbackupengine.go) (xtrabackup is a builtin variant) | Shipped backup engines |
| Orchestration | [`go/vt/mysqlctl/backup.go`](https://github.com/vitessio/vitess/blob/main/go/vt/mysqlctl/backup.go) | Backup, incremental backup, list, restore, and prune logic |
| Tablet backup/restore RPC | [`go/vt/vttablet/tabletmanager/rpc_backup.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/rpc_backup.go) (service: [`proto/tabletmanagerservice.proto`](https://github.com/vitessio/vitess/blob/main/proto/tabletmanagerservice.proto)) | `Backup` and `RestoreFromBackup` gRPC methods on the tablet |
| Backup metadata proto | [`proto/mysqlctl.proto`](https://github.com/vitessio/vitess/blob/main/proto/mysqlctl.proto) (`BackupInfo`), generated under [`go/vt/proto/mysqlctl/`](https://github.com/vitessio/vitess/tree/main/go/vt/proto/mysqlctl) | Wire representation of a backup for listing |
| `vtbackup` CLI | [`go/cmd/vtbackup/`](https://github.com/vitessio/vitess/tree/main/go/cmd/vtbackup) | Standalone batch backup/restore/maintenance program |
| Metrics | [`go/vt/mysqlctl/backupstats/`](https://github.com/vitessio/vitess/tree/main/go/vt/mysqlctl/backupstats) | Prometheus metrics for backup/restore operations |

## Commands & operations

- **Take a full backup** — `vtctldclient Backup [--upgrade-safe=false] <tablet-alias>`; with `builtin` replication stops before mysqld shuts down, with `xtrabackup` the tablet keeps serving.
- **Take a shard backup** — `vtctldclient BackupShard [--allow-primary=false] <keyspace/shard>`.
- **Take an incremental backup** — same commands with `--incremental-from-pos=<pos|backup-name|auto>`.
- **Restore** — `vtctldclient RestoreFromBackup [--restore-to-timestamp <RFC3339> | --restore-to-pos <gtid>] [--dry-run] <tablet-alias>`.
- **Manage backups** — `vtctldclient GetBackups <keyspace/shard>` (chronological list) and `vtctldclient RemoveBackup <keyspace/shard> <backup-name>`. Backups also appear as files under the storage root, e.g. `…/commerce/0/2021-03-10.205419.zone1-0000000102/{backup.xbstream.gz, MANIFEST}`.
- **Scheduled backups** — run `vtbackup` periodically per shard (cron or similar), or on Kubernetes use the operator's `VitessBackupSchedule` CRD (shard/keyspace/cluster scopes, cron `schedule` or `frequency`, staggered per shard) which materializes `VitessBackup` objects.
- **Auto-restore at startup** — start vttablet with `--restore-from-backup` (optionally `--restore-from-backup-ts`, `--restore-to-timestamp`, `--restore-to-pos`, and `--wait-for-backup-interval` to wait for the first backup to appear).

## Key flags

| Flag | Applies to | Purpose |
| ---- | ---------- | ------- |
| `--backup-storage-implementation` | vtbackup, vttablet, vtctld | `file`, `gcs`, `s3`, `ceph`, `azblob` |
| `--backup-engine-implementation` | vtbackup, vttablet, vtctld | `builtin` (default), `xtrabackup`, `mysqlshell` |
| `--backup-storage-compress` / `--compression-engine-name` / `--compression-level` | vtbackup, vttablet, vtctld | In-process compression (default on, `pargzip`) |
| `--external-compressor` / `--external-decompressor` | vtbackup, vttablet, vtctld | Out-of-process compression commands (stream on STDIN/STDOUT) |
| `--file-backup-storage-root` / `--gcs-backup-storage-bucket` / `--s3-backup-storage-bucket` + `--s3-backup-aws-region` / `--ceph-backup-storage-config` / `--azblob-backup-account-name` + `--azblob-backup-container-name` + `--azblob-backup-storage-root` | vtbackup, vttablet, vtctld | Location per storage plugin |
| `--xtrabackup-user`, `--xtrabackup-stream-mode`, `--xtrabackup-backup-flags`, `--xtrabackup-prepare-flags`, `--xtrabackup-stripes`, `--xtrabackup-root-path`, `--xbstream-restore-flags` | vtbackup, vttablet, vtctld | XtraBackup engine configuration |
| `--mysql-shell-backup-location`, `--mysql-shell-flags`, `--mysql-shell-dump-flags`, `--mysql-shell-load-flags` | vtbackup, vttablet, vtctld | MySQL Shell engine configuration |
| `--restore-from-backup` / `--restore-from-backup-ts` / `--wait-for-backup-interval` | vttablet | Auto-restore on startup |
| `--restore-to-timestamp` / `--restore-to-pos` / `--dry-run` | vtctldclient (restore), vttablet (startup) | Point-in-time restore targeting |
| `--restore-concurrency` / `--concurrency` | vttablet / vtctl | Parallel file copy/compress |
| `--init-backup-sql-queries` / `--init-backup-tablet-types` / `--init-backup-sql-timeout` / `--init-backup-sql-fail-on-error` | vtbackup, vtctldclient (backup) | SQL executed after catch-up, before the backup |
| `--restart-before-backup` | vtbackup, vtctldclient (backup) | Clean mysqld restart before backup (xtrabackup DDL workaround) |
| `--min-backup-interval` / `--min-retention-time` / `--min-retention-count` / `--initial-backup` | vtbackup | Scheduled-backup policy and pruning |

## Notes & caveats

- The topology server (etcd/Consul/ZooKeeper/K8s) is *not* covered by this feature; back it up per the backend's own procedure.
- Prefer `xtrabackup` over `builtin` in production: online, non-blocking, and more robust.
- Keep binlog retention longer than the incremental-backup window — if binlogs are purged before the incremental is taken, that backup (and any PITR spanning that gap) fails.
- A PITR restore ends at `DRAINED`; set the tablet back to `REPLICA` when you're done inspecting.
- For fast provisioning without object storage, [MySQL CLONE](https://vitess.io/docs/24.0/user-guides/operating-vitess/backup-and-restore/mysql-clone/) (MySQL ≥ 8.0.17, InnoDB-only) copies a donor's data over the network to the new tablet instead of staging a backup.
- See also [replication graph](replication-graph.md) (what a restore re-attaches to), [VTOrc](vtorc.md) (primary failover that makes PITR useful), and [tablets](tablet.md) (transient `BACKUP`/`RESTORE`/`DRAINED` types).

## Upstream docs

- [Backup and restore — Overview](https://vitess.io/docs/24.0/user-guides/operating-vitess/backup-and-restore/overview/)
- [Creating a Backup](https://vitess.io/docs/24.0/user-guides/operating-vitess/backup-and-restore/creating-a-backup/)
- [Bootstrapping and Restoring](https://vitess.io/docs/24.0/user-guides/operating-vitess/backup-and-restore/bootstrap-and-restore/)
- [Managing Backups](https://vitess.io/docs/24.0/user-guides/operating-vitess/backup-and-restore/managing-backups/)
- [Backups and Restore for Local Environment](https://vitess.io/docs/24.0/user-guides/operating-vitess/backup-and-restore/backup_and_restore_local/)
- [Scheduling Backups (operator)](https://vitess.io/docs/24.0/user-guides/operating-vitess/backup-and-restore/scheduled-backups/)
- [MySQL CLONE for Tablet Provisioning](https://vitess.io/docs/24.0/user-guides/operating-vitess/backup-and-restore/mysql-clone/)
- [vtbackup program reference](https://vitess.io/docs/24.0/reference/programs/vtbackup/)
- [vtctldclient Backup / BackupShard / RestoreFromBackup / GetBackups / RemoveBackup](https://vitess.io/docs/24.0/reference/programs/vtctldclient/)
