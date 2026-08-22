---
upstream: https://github.com/cloudnative-pg/plugin-barman-cloud
last_updated: 2026-08-22
---

# plugin-barman-cloud — API reference

One CRD, in API group/version `barmancloud.cnpg.io/v1`. The canonical CRD in the repository is [barmancloud.cnpg.io_objectstores.yaml](https://github.com/cloudnative-pg/plugin-barman-cloud/blob/main/config/crd/bases/barmancloud.cnpg.io_objectstores.yaml) (CRD `objectstores.barmancloud.cnpg.io`, plural `objectstores`). Plugin-side wiring (how a CloudNativePG cluster references the plugin) lives in the CloudNativePG `Cluster` API under `spec.plugins` — see the [usage docs](https://cloudnative-pg.io/plugin-barman-clouddocs/usage/).

| Kind | API group/version | Purpose | Canonical schema |
| --- | --- | --- | --- |
| `ObjectStore` | `barmancloud.cnpg.io/v1` | A remote backup store (S3-compatible, S3, Azure Blob, GCS, NFS) that the plugin uses for base backups, WAL archiving, and recovery; owns the retention policy | [ObjectStore section of the usage docs](https://cloudnative-pg.io/plugin-barman-clouddocs/usage/) and [object stores doc](https://cloudnative-pg.io/plugin-barman-clouddocs/object_stores/) |

## `ObjectStore` spec surface (v0.14.0)

- `spec.retentionPolicy` — retention policy; ⚠️ with the plugin it is defined here, whereas in-tree it was `Cluster.spec.backup.retentionPolicy`. [migration doc](https://cloudnative-pg.io/plugin-barman-clouddocs/migration/)
- `spec.configuration` — directly mirrors the in-tree `barman-cloud` `BarmanObjectStoreConfiguration` (same field set and semantics; full field reference: the [CloudNativePG BarmanObjectStore appendix](https://cloudnative-pg.io/documentation/current/appendixes/backup_barmanobjectstore/)):
  - `destinationPath` (required), `endpointURL`, `s3Credentials` / `azureCredentials` (including `useDefaultAzureCredentials` since [v0.11.0](https://github.com/cloudnative-pg/plugin-barman-cloud/releases/tag/v0.11.0)) / GCS / NFS credentials.
  - `data`: `compression` (`gzip`, `bzip2`, `lz4`, `snappy`), `encryption` (`AES256`, `aws:kms`), `jobs` (parallel backup-upload jobs, **default 2**), `immediateCheckpoint`, `additionalCommandArgs`, `restoreAdditionalCommandArgs`.
  - `wal`: `compression` (adds `xz`, `zstd`), `encryption`, `maxParallel` (WAL files archived **or restored** in parallel; 1 = sequential, the default; the [CloudNativePG docs](https://cloudnative-pg.io/documentation/current/appendixes/backup_barmanobjectstore/) use **8** as the recommended example value), `archiveAdditionalCommandArgs`, `restoreAdditionalCommandArgs`.
  - ⚠️ `serverName` is **forbidden by a CEL rule** (`FieldValueForbidden: use the 'serverName' plugin parameter in the Cluster resource`) — it is retained in the schema only for API compatibility and must be left unset. [parameters doc](https://cloudnative-pg.io/plugin-barman-clouddocs/parameters/)
- `spec.instanceSidecarConfiguration` — options for the per-instance sidecar container (e.g. additional container arguments, [v0.7.0](https://github.com/cloudnative-pg/plugin-barman-cloud/releases/tag/v0.7.0)).

## Plugin parameters (v0.14.0)

The plugin accepts exactly two parameters in the `Cluster`'s `spec.plugins[].parameters` block: [parameters doc](https://cloudnative-pg.io/plugin-barman-clouddocs/parameters/)

- `barmanObjectName` — name of the `ObjectStore` resource the plugin should use (required in practice).
- `serverName` — the server name in the object store (in-tree equivalent of `barmanObjectStore.serverName`).

There are **no** plugin-level `jobs` / `maxParallel` parameters in v0.14.0: parallelism is set on the `ObjectStore` (`configuration.data.jobs`, `configuration.wal.maxParallel`).

## What the Helm chart deploys

[Chart `plugin-barman-cloud` (0.7.x)](https://github.com/cloudnative-pg/plugin-barman-cloud): control-plane `Deployment`, `ServiceAccount`/RBAC (`ClusterRole`, `Role`), a `Service`, and two cert-manager `Certificate` resources.

- Images: `image` (`ghcr.io/cloudnative-pg/plugin-barman-cloud`) and `sidecarImage` (`ghcr.io/cloudnative-pg/plugin-barman-cloud-sidecar`); both tags default to the chart `appVersion` — set them explicitly in values when you need pinned, renovate-trackable references.
- The Service name (`barman-cloud`, `service.name`) and the Certificate names (`barman-cloud-client`, `barman-cloud-server`) are **hardcoded in the chart templates**, while the Deployment/SA/RBAC names follow the release name. Keep the chart's default release name `plugin-barman-cloud` to avoid doubled long names and name conflicts; the operator finds the plugin through the Service's `cnpg.io/pluginName` annotation (`barman-cloud.cloudnative-pg.io`), not the release name.
