---
upstream: https://github.com/planetscale/vitess-operator
last_updated: 2026-09-03
---

# vitess-operator — releases

Latest 10 official releases of the `planetscale/vitess-operator` project, newest first. Scan the ⚠️ entries before upgrading — the operator upgrades the Vitess components it manages, so pair the operator version with the Vitess release it targets (see the operator's compatibility table).

## v2.17.0 — 2026-04-30
[Release page](https://github.com/planetscale/vitess-operator/releases/tag/v2.17.0)
- **Backups**: cluster- and keyspace-level backup schedules, a `backupMethod` field on `VitessBackupSchedule`, a cap on the number of concurrently reconciled backups, and `vtbackup` extra-flags support.
- **Behavior**: `vttablet` now triggers a rolling restart when its storage size changes, and binary logs for point-in-time recovery are placed in a shared directory.
- **Kubernetes / tooling**: tested against Kubernetes 1.35, `mysqld_exporter` ≥ 0.15.0, Go 1.26, and an added Dependabot config; `init_db.sql` synced with upstream Vitess.

## v2.17.0-rc1 — 2026-04-14
[Release page](https://github.com/planetscale/vitess-operator/releases/tag/v2.17.0-rc1)
- Release candidate for v2.17.0.

## v2.16.0 — 2025-11-05
[Release page](https://github.com/planetscale/vitess-operator/releases/tag/v2.16.0)
- **Features**: `--s3_backup_aws_min_partsize` is now optional, extra flags for `mysqld_exporter`, a `fetchCredentials` setting for VTAdmin, and the ability to specify the hex width used when generating shard ranges.
- **Tooling**: bumped to Go 1.25.1 and upgraded the Vitess dependency to latest.

## v2.16.0-rc2 — 2025-10-30
[Release page](https://github.com/planetscale/vitess-operator/releases/tag/v2.16.0-rc2)
- Release candidate for v2.16.0.

## v2.16.0-rc1 — 2025-10-17
[Release page](https://github.com/planetscale/vitess-operator/releases/tag/v2.16.0-rc1)
- Release candidate for v2.16.0.

## v2.15.1 — 2025-06-18
[Release page](https://github.com/planetscale/vitess-operator/releases/tag/v2.15.1)
- Bug-fix release for the v2.15.x series: made `--s3_backup_aws_min_partsize` optional and updated Golang to v1.24.4.

## v2.15.0 — 2025-04-29
[Release page](https://github.com/planetscale/vitess-operator/releases/tag/v2.15.0)
- **Kubernetes / tooling**: default supported Kubernetes bumped v1.31 → v1.32; Golang v1.23 → v1.24.
- **⚠️ Scheduled backups now use `vtbackup`** — the scheduled-backup controller now runs `vtbackup` pods (more fault-tolerant, and does not pull a serving tablet out of the pool).
- **Multi-namespace examples** — the default `operator.yaml` and CI now demonstrate an operator in one namespace and a `VitessCluster` in another.
- **Rolling update for VTGate** — the vtgate Deployment strategy is now configurable.
- **⚠️ Upgrade path**: default etcd bumped to 3.5.17 and default MySQL bumped 8.0.30 → 8.0.40 (follow the release notes for the ordered MySQL upgrade procedure).

## v2.15.0-rc3 — 2025-04-24
[Release page](https://github.com/planetscale/vitess-operator/releases/tag/v2.15.0-rc3)
- Release candidate for v2.15.0.

## v2.15.0-rc2 — 2025-04-11
[Release page](https://github.com/planetscale/vitess-operator/releases/tag/v2.15.0-rc2)
- Release candidate for v2.15.0.

## v2.15.0-rc1 — 2025-04-08
[Release page](https://github.com/planetscale/vitess-operator/releases/tag/v2.15.0-rc1)
- Release candidate for v2.15.0 (carries the same major changes as the v2.15.0 GA notes above).
