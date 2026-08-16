---
upstream: https://github.com/cloudnative-pg/cloudnative-pg
last_updated: 2026-08-16
---

# cloudnative-pg — API reference

All kinds are served and stored under the single version `v1` of the `postgresql.cnpg.io` API group. The generated API reference in the repo is [docs/src/cloudnative-pg.v1.md](https://github.com/cloudnative-pg/cloudnative-pg/blob/main/docs/src/cloudnative-pg.v1.md); the published version is [cloudnative-pg.v1](https://cloudnative-pg.io/docs/1.30/cloudnative-pg.v1/).

| Kind | Short name | Purpose | Upstream API docs |
| --- | --- | --- | --- |
| `Cluster` | `—` | Core kind: a highly available PostgreSQL cluster (primary + replicas) with storage, configuration, TLS, failover, and upgrades | [docs](https://cloudnative-pg.io/docs/1.30/cloudnative-pg.v1/#cluster) |
| `Backup` | `—` | On-demand Barman-based backup job and its backup storage | [docs](https://cloudnative-pg.io/docs/1.30/cloudnative-pg.v1/#backup) |
| `ScheduledBackup` | `—` | Recurring backup schedule (schedule + retention) for a cluster | [docs](https://cloudnative-pg.io/docs/1.30/cloudnative-pg.v1/#scheduledbackup) |
| `Database` | `—` | Declarative logical database (owner, template, collation, configuration) | [docs](https://cloudnative-pg.io/docs/1.30/cloudnative-pg.v1/#database) |
| `DatabaseRole` | `—` | Declarative database role/user with lifecycle/reclaim policies (introduced in 1.30) | [docs](https://cloudnative-pg.io/docs/1.30/cloudnative-pg.v1/#databaserole) |
| `Pooler` | `—` | Managed PgBouncer pooler for a cluster, with authentication, TLS, and metrics | [docs](https://cloudnative-pg.io/docs/1.30/cloudnative-pg.v1/#pooler) |
| `Publication` | `—` | Operator-managed PostgreSQL logical publication | [docs](https://cloudnative-pg.io/docs/1.30/cloudnative-pg.v1/#publication) |
| `Subscription` | `—` | Operator-managed PostgreSQL logical subscription | [docs](https://cloudnative-pg.io/docs/1.30/cloudnative-pg.v1/#subscription) |
| `FailoverQuorum` | `—` | Internal state kind: current failover quorum status of a cluster (updated by the instance manager, reset by the operator) | [docs](https://cloudnative-pg.io/docs/1.30/cloudnative-pg.v1/#failoverquorum) |
| `ImageCatalog` | `—` | Namespace-scoped catalog of PostgreSQL images for the image-lifecycle feature | [docs](https://cloudnative-pg.io/docs/1.30/cloudnative-pg.v1/#imagecatalog) |
| `ClusterImageCatalog` | `—` | Cluster-scoped catalog of PostgreSQL images for the image-lifecycle feature | [docs](https://cloudnative-pg.io/docs/1.30/cloudnative-pg.v1/#clusterimagecatalog) |

## Notes

- `ClusterImageCatalog` is cluster-scoped; every other kind above is namespace-scoped.
- No `spec.names.shortNames` are defined in the current CRDs — use the full kind names (plurals: `clusters`, `backups`, `scheduledbackups`, `databases`, `databaseroles`, `poolers`, `publications`, `subscriptions`, `failoverquorums`, `imagecatalogs`, `clusterimagecatalogs`).
- `DatabaseRoleList` appears in the generated reference only as a list type, not as a separate kind.
- There is no `Restore` kind: point-in-time recovery is requested on the `Cluster` itself (see the [recovery docs](https://cloudnative-pg.io/docs/1.30/recovery)). `SchedulingPolicy` and `Certificate` kinds do not exist either.
