---
upstream: https://github.com/cloudnative-pg/plugin-barman-cloud
last_updated: 2026-08-22
---

# plugin-barman-cloud — features

Key feature areas, each linked to the upstream documentation or release. The docs at [cloudnative-pg.io/plugin-barman-cloud](https://cloudnative-pg.io/plugin-barman-cloud) (versioned) are authoritative.

## Architecture

- **CNPG-I plugin architecture**: an independent control-plane operator talks gRPC to a per-instance **sidecar** that the CloudNativePG operator injects into each cluster pod; the operator delegates backup, WAL archival, and restore requests to the plugin instead of running barman-cloud in-tree. [concepts doc](https://cloudnative-pg.io/plugin-barman-clouddocs/concepts/)
- **Plugin identity**: the plugin registers as `barman-cloud.cloudnative-pg.io`; a cluster opts in via `spec.plugins[]` with `parameters.barmanObjectName` pointing at an `ObjectStore` CR. [usage doc](https://cloudnative-pg.io/plugin-barman-clouddocs/usage/)

## Backups and WAL archiving

- **Base backups**: on-demand `Backup` and recurring `ScheduledBackup` resources set to `method: plugin` with `pluginConfiguration.name: barman-cloud.cloudnative-pg.io`. [usage doc](https://cloudnative-pg.io/plugin-barman-clouddocs/usage/)
- **Continuous WAL archiving**: enable with `.spec.plugins[].isWALArchiver: true` on the `Cluster`; the sidecar runs `barman-cloud-wal-archive`, including empty-archive checks ([v0.6.0](https://github.com/cloudnative-pg/plugin-barman-cloud/releases/tag/v0.6.0)) and, since [v0.14.0](https://github.com/cloudnative-pg/plugin-barman-cloud/releases/tag/v0.14.0), honors the operator's `check_empty_wal_archive` decision.
- **Parallelism**: set on the `ObjectStore`, not the plugin — `configuration.wal.maxParallel` (WALs archived/restored in parallel; CloudNativePG docs recommend **8** when object-store bandwidth allows) and `configuration.data.jobs` (parallel backup-upload jobs, default 2). See [API reference](api-reference.md) and the [CloudNativePG BarmanObjectStore appendix](https://cloudnative-pg.io/documentation/current/appendixes/backup_barmanobjectstore/).
- **Compression and encryption**: `gzip`/`bzip2`/`lz4`/`snappy` (base backups; `xz`/`zstd` too for WAL), with optional forced `AES256`/`aws:kms` encryption, per `data`/`wal` sections. [compression doc](https://cloudnative-pg.io/plugin-barman-clouddocs/compression/)

## Recovery

- **Point-in-time recovery**: `Cluster.spec.bootstrap.recovery` with the plugin's `externalClusters` entry; the sidecar serves WAL for replay (`barman-cloud-restore`), and [v0.14.0](https://github.com/cloudnative-pg/plugin-barman-cloud/releases/tag/v0.14.0) restores WAL from a replica source during designated-primary promotion and serves `pg_rewind` without the prefetch flag machinery.
- **Retention policies**: defined on the `ObjectStore`, not the `Cluster` (the in-tree location is gone); the [retention doc](https://cloudnative-pg.io/plugin-barman-clouddocs/retention/) documents the accepted values.

## Object stores

- **Providers**: S3-compatible endpoints (SeaweedFS, MinIO, ...), Azure Blob (with `DefaultAzureCredential` since [v0.11.0](https://github.com/cloudnative-pg/plugin-barman-cloud/releases/tag/v0.11.0)), GCS, and local/NFS paths, all modeled as `ObjectStore` CRs. [object stores doc](https://cloudnative-pg.io/plugin-barman-clouddocs/object_stores/)
- **`serverName`**: set as a **plugin parameter** on the `Cluster`; the same field on `ObjectStore` is rejected by a CEL validation. [parameters doc](https://cloudnative-pg.io/plugin-barman-clouddocs/parameters/)

## Migration from in-tree Barman Cloud

- In-tree Barman Cloud is deprecated (CloudNativePG 1.26+) and removed in 1.31.0; the [migration doc](https://cloudnative-pg.io/plugin-barman-clouddocs/migration/) shows the mechanical mapping `Cluster.spec.backup.barmanObjectStore` → `ObjectStore.spec.configuration`, with retention moving to `spec.retentionPolicy`, and a full working migration in the [cnpg-playground repo](https://github.com/cloudnative-pg/cnpg-playground/commit/596f30e252896edf8f734991c3538df87630f6f7).

## Observability

- **Metrics**: upstream backup/recovery metrics and a last-failed-backup status field ([v0.6.0](https://github.com/cloudnative-pg/plugin-barman-cloud/releases/tag/v0.6.0)); [observability doc](https://cloudnative-pg.io/plugin-barman-clouddocs/observability/), pprof profiling since [v0.10.0](https://github.com/cloudnative-pg/plugin-barman-cloud/releases/tag/v0.10.0).

## Operational notes (GitOps deployments with Flux/Kustomize)

Findings from deploying chart `0.7.1` (plugin `v0.14.0`) in a Flux-managed, Kustomize-structured GitOps setup:

- **Resource naming**: use the chart's default release name `plugin-barman-cloud`. The chart hardcodes the Service name `barman-cloud` and the Certificate names `barman-cloud-client`/`barman-cloud-server`, while the Deployment/SA/RBAC names follow the release — a custom release name produces doubled long names and no benefit, since CloudNativePG locates the plugin via the Service's `cnpg.io/pluginName` annotation, never the release name.
- **Image pinning**: leave `image.tag` and `sidecarImage.tag` explicit (e.g. `v0.14.0`) rather than the empty tag that falls back to the chart `appVersion`, so each image has its own renovate/dependency-tracking entry alongside the chart version.
- **Single bootstrap block**: keep one (non-restoring) `bootstrap` in the `Cluster` and keep restore/`externalClusters` variants as commented variants to enable when needed, since only one bootstrap section may be active at a time.
