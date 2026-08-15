---
upstream: https://github.com/mariadb-operator/mariadb-operator
last_updated: 2026-08-15
---

# mariadb-operator — API reference

The operator registers 12 custom resource kinds under API group/version **`k8s.mariadb.com/v1alpha1`**. The full generated reference is maintained upstream: [docs/api_reference.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/api_reference.md); the Go type definitions live in [`api/v1alpha1/`](https://github.com/mariadb-operator/mariadb-operator/tree/main/api/v1alpha1).

| Kind | Short name | Purpose | Upstream API docs |
| ---- | ---------- | ------- | ----------------- |
| `MariaDB` | `mdb` | Core kind: defines and runs a MariaDB instance/cluster — topology (standalone, Galera, replication), storage, updates, TLS. | [docs](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/api_reference.md#mariadb) |
| `Backup` | `bmdb` | Defines a logical (dump-based) backup job and its storage. | [docs](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/api_reference.md#backup) |
| `PhysicalBackup` | `pbmdb` | Defines a physical backup job (xtrabackup-based) and its storage. | [docs](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/api_reference.md#physicalbackup) |
| `Restore` | `rmdb` | Defines a restore job that materializes databases from backups. | [docs](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/api_reference.md#restore) |
| `PointInTimeRecovery` | `pitr` | Archives binary logs and recovers a database to a point in time (26.3.0+). | [docs](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/api_reference.md#pointintimerecovery) |
| `Database` | `dmdb` | A logical database (`CREATE DATABASE`) on a `MariaDB` or `ExternalMariaDB`. | [docs](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/api_reference.md#database) |
| `User` | `umdb` | A database user and its credentials (`CREATE USER` / `ALTER USER`). | [docs](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/api_reference.md#user) |
| `Grant` | `gmdb` | Privileges granted to a user (`GRANT`). | [docs](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/api_reference.md#grant) |
| `Connection` | `cmdb` | An application connection string to a `MariaDB` or `ExternalMariaDB`. | [docs](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/api_reference.md#connection) |
| `SqlJob` | `smdb` | Runs a SQL script as a one-off job. | [docs](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/api_reference.md#sqljob) |
| `ExternalMariaDB` | `emdb` | Declares an external MariaDB instance so the SQL-object kinds can target it (25.8.4+). | [docs](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/api_reference.md#externalmariadb) |
| `MaxScale` | `mxs` | Provisions and runs a MaxScale proxy instance in front of a `MariaDB`. | [docs](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/api_reference.md#maxscale) |

Notes:

- All kinds use API version `k8s.mariadb.com/v1alpha1`; short names come from the kubebuilder markers in [`api/v1alpha1/`](https://github.com/mariadb-operator/mariadb-operator/tree/main/api/v1alpha1).
- [`event_types.go`](https://github.com/mariadb-operator/mariadb-operator/blob/main/api/v1alpha1/event_types.go) contains event-reason *constants* used in status reporting — `Event` is not a kind.
- Field-level documentation is intentionally not duplicated here; follow the per-kind upstream links above.
