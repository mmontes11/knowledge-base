---
upstream: https://github.com/mariadb-operator/mariadb-operator
last_updated: 2026-08-15
---

# mariadb-operator — features

Key feature areas, each linked to the upstream documentation covering it. The [docs/ tree in the upstream repository](https://github.com/mariadb-operator/mariadb-operator/tree/main/docs) is authoritative.

## Cluster topologies

- **Standalone**: single-replica instance, the simplest supported topology. [docs/standalone.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/standalone.md)
- **Galera (multi-master)**: synchronous multi-master replication with operator-managed bootstrap, locking, and failover. [docs/galera.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/galera.md)
- **Replication (primary/replica)**: asynchronous primary/replica topology (`replicas > 1` plus `spec.replication.enabled=true`); the operator manages the replication user, replication status, and automatic primary failover. Introduced in 25.10.x, GA'd in [25.10.2](https://github.com/mariadb-operator/mariadb-operator/releases/tag/25.10.2). [docs/replication.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/replication.md)
- **Multi-cluster** (new in 26.6.0): data replication between multiple clusters — within one Kubernetes cluster or across Kubernetes clusters — building on the replication or Galera topology for HA, DR, and blue-green deployments. [docs/multi-cluster.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/multi-cluster.md)

## High availability and updates

- **Failover and update ordering**: `ReplicasFirstPrimaryLast` default update strategy, replica recovery, and automatic primary failover. [docs/high_availability.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/high_availability.md), [docs/updates.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/updates.md)
- **Maintenance mode** (new in 26.6.0): composable cordon, drain-connections, and read-only modes for planned maintenance windows. [docs/maintenance.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/maintenance.md)

## MaxScale proxy

- **MaxScale**: provisions and runs a MaxScale instance in front of a `MariaDB`, with servers and listeners routed to the current primary automatically. [docs/maxscale.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/maxscale.md)

## Backup and disaster recovery

- **Logical backups** (`Backup`): scheduled, on-demand, and retention-managed dump-based backups to cloud or in-cluster storage. [docs/logical_backup.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/logical_backup.md)
- **Physical backups** (`PhysicalBackup`): xtrabackup-based backups with scheduling, retention, and per-job `Replica`/`PreferReplica` target policies (25.10.3+). On-demand execution added in 26.3.0. [docs/physical_backup.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/physical_backup.md)
- **Point-in-time recovery** (26.3.0, `PointInTimeRecovery` + `Restore`): automatic binary-log archival and recovery of a `MariaDB` to a specific time, built on `PhysicalBackup` base backups. [docs/pitr.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/pitr.md)
- **Storage backends**: S3-compatible object storage, Azure Blob (26.3.0+), in-cluster PVCs, and `VolumeSnapshot`. [docs/storage.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/storage.md)

## SQL objects

- **Databases, users, grants, and SQL jobs**: declarative `Database`, `User`, `Grant`, and `SqlJob` kinds on a `MariaDB` or `ExternalMariaDB`. [docs/sql_resources.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/sql_resources.md)
- **Connections** (`Connection`): application connection strings for `MariaDB` or `ExternalMariaDB` targets. [docs/connections.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/connections.md)

## External MariaDB

- **ExternalMariaDB** (25.8.4): manage SQL objects and backups on externally hosted MariaDB instances with the same CRs used for in-cluster instances. [docs/external_mariadb.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/external_mariadb.md)

## Security and operations

- **TLS**: TLS for client connections and internal endpoints, with managed or external certificates. [docs/tls.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/tls.md)
- **Root password rotation** (26.6.0): rotate the MariaDB root password in place. [release notes](https://github.com/mariadb-operator/mariadb-operator/releases/tag/26.6.0)
- **Metrics**: a metrics deployment alongside each `MariaDB` for Prometheus scraping. [docs/metrics.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/metrics.md)
- **Suspend**: pause reconciliation of a `MariaDB` and its SQL objects. [docs/suspend.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/suspend.md)

## Deployment

- **Helm**: `mariadb-operator`, `mariadb-operator-crds`, and `mariadb-cluster` charts; OCI artifacts under `oci://ghcr.io/mariadb-operator/charts/` since 26.6.0 (the legacy `helm.mariadb.com` repository is deprecated). [docs/helm.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/helm.md)
- **Images and operator configuration**: image registry details and operator flags/values. [docs/docker.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/docker.md), [docs/configuration.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/configuration.md)
