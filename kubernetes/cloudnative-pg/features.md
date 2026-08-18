---
upstream: https://github.com/cloudnative-pg/cloudnative-pg
last_updated: 2026-08-16
---

# cloudnative-pg — features

Key feature areas, each linked to the upstream documentation covering it. The docs at [cloudnative-pg.io/docs/](https://cloudnative-pg.io/docs/) (versioned, e.g. `1.30`) are authoritative.

## Cluster management and topologies

- **Clusters**: a `Cluster` resource declares a whole PostgreSQL deployment — primary, replicas, storage, configuration, and TLS — and the operator drives and keeps it at that state. [docs](https://cloudnative-pg.io/docs/1.30/index)
- **Replica clusters** (`replica_cluster`): read-only replica clusters consuming the same `Cluster` spec, including cross-cluster and cross-region deployments. [docs/replica_cluster](https://cloudnative-pg.io/docs/1.30/replica_cluster)
- **Physical replication** and **logical replication** (publications/subscriptions) between clusters. [docs/replication](https://cloudnative-pg.io/docs/1.30/replication), [docs/logical_replication](https://cloudnative-pg.io/docs/1.30/logical_replication)

## High availability and failover

- **Failover**: automatic primary failover based on health checks, with **fencing** of the demoted former primary to prevent split-brain writes. [docs/failover](https://cloudnative-pg.io/docs/1.30/failover), [docs/fencing](https://cloudnative-pg.io/docs/1.30/fencing)
- **Primary lease** (1.30.0): primary election is serialized through a cluster-named Kubernetes `Lease` (`.spec.primaryLease`) so two instances can never hold primary simultaneously. [release v1.30.0](https://github.com/cloudnative-pg/cloudnative-pg/releases/tag/v1.30.0)

## Backup and disaster recovery

- **Physical backups** (`Backup`, `ScheduledBackup`) and continuous **WAL archiving**: base backups plus WAL to S3-compatible, Azure Blob, GCS, NFS, or in-cluster storage, with retention policies. [docs/backup_recovery](https://cloudnative-pg.io/docs/1.30/backup_recovery), [docs/wal_archiving](https://cloudnative-pg.io/docs/1.30/wal_archiving)
- **Recovery**: point-in-time recovery to the `Cluster` itself (there is no separate `Restore` kind). [docs/recovery](https://cloudnative-pg.io/docs/1.30/recovery)
- ⚠️ **Built-in (in-tree) Barman Cloud is deprecated**: it is removed in `1.31.0`; migrate to the [Barman Cloud plugin](https://github.com/cloudnative-pg/barman-cloud-plugin). Deprecation announced in 1.29.0, schedule changed twice (1.30.0 → 1.31.0). [release v1.29.0](https://github.com/cloudnative-pg/cloudnative-pg/releases/tag/v1.29.0), [release v1.30.0](https://github.com/cloudnative-pg/cloudnative-pg/releases/tag/v1.30.0)

## Connection pooling

- **PgBouncer poolers** (`Pooler`): operator-managed connection pool in front of a cluster, with declarative PgBouncer configuration, TLS (cipher-suite and version control since 1.28.x/1.29.0), and metrics. [docs/connection_pooling](https://cloudnative-pg.io/docs/1.30/connection_pooling)

## Declarative database management

- **Databases and logical objects** (`Database`, `Publication`, `Subscription`): declarative creation of databases, extensions, and logical replication objects with owned lifecycle. [docs/declarative_database_management](https://cloudnative-pg.io/docs/1.30/declarative_database_management)
- **Roles and users**: role management via `role` entries on `Cluster` and, since 1.30.0, the standalone `DatabaseRole` resource (own lifecycle, status, optional TLS client credentials). [docs/declarative_role_management](https://cloudnative-pg.io/docs/1.30/declarative_role_management)

## Upgrades

- **Major version upgrades**: in-place PostgreSQL major upgrades via `pg_upgrade` (to PG 18 and, in 1.30.0, up to PG 19 for clusters using Image Volume extensions), plus minor version updates through image catalog updates. [docs/postgres_upgrades](https://cloudnative-pg.io/docs/1.30/postgres_upgrades)

## Image catalogs

- **`ImageCatalog` / `ClusterImageCatalog`**: declarative catalogs of certified PostgreSQL and extension images; cluster `imageCatalogRef` pins the versions the operator may use, enabling safe, reviewable update rollouts. [docs/image_catalog](https://cloudnative-pg.io/docs/1.30/image_catalog)

## Storage, resources, and scheduling

- **Storage**: PVC-based data storage with volume snapshots, resize, and per-topology sizing. [docs/storage](https://cloudnative-pg.io/docs/1.30/storage)
- **Resource management**: CPU/memory requests and limits per container (instance, wal, exporter), affinity, priority classes, and instance scheduling options. [docs/resource_management](https://cloudnative-pg.io/docs/1.30/resource_management)

## Security and TLS

- **TLS for client and internal connections** with managed or externally supplied certificates. [docs/ssl_connections](https://cloudnative-pg.io/docs/1.30/ssl_connections), [docs/certificates](https://cloudnative-pg.io/docs/1.30/certificates)
- **Security**: RBAC-based access, service-account selection (1.29.0+), password encryption (SCRAM), and secret management. [docs/security](https://cloudnative-pg.io/docs/1.30/security)

## Monitoring

- **Metrics and monitoring**: built-in Prometheus metrics endpoint (hardened for `CVE-2026-44477` in 1.28.3/1.29.1), custom monitoring queries, and Grafana dashboards. [docs/monitoring](https://cloudnative-pg.io/docs/1.30/monitoring)

## Operations and tooling

- **`cnpg` kubectl plugin**: operational verbs for status, backup, recovery, migration, and more. [docs/kubectl-plugin](https://cloudnative-pg.io/docs/1.30/kubectl-plugin)
- **Database import**: import schemas and data from external PostgreSQL instances into a managed `Cluster`. [docs/database_import](https://cloudnative-pg.io/docs/1.30/database_import)
