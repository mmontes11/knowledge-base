---
upstream: https://github.com/cloudnative-pg/plugin-barman-cloud
last_updated: 2026-08-22
---

# plugin-barman-cloud — releases

Latest 10 official releases, newest first. Check the ⚠️ entries before upgrading. The chart in `ghcr.io/cloudnative-pg/charts/plugin-barman-cloud` tracks these releases (chart `0.7.1` ships plugin `v0.14.0`).

## 0.14.0 — 2026-07-29

[Release page](https://github.com/cloudnative-pg/plugin-barman-cloud/releases/tag/v0.14.0)

- **Honor the operator's `check_empty_wal_archive` decision** for WAL archiving. ([#1009](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/1009))
- **Restore WAL from a replica source during designated primary promotion**, and serve `pg_rewind` without the prefetch/flag machinery. ([#966](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/966), [#1007](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/1007))
- Inject the barman sidecar into replica clusters bootstrapped with `pg_basebackup`. ([#965](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/965))

## 0.13.0 — 2026-06-10

[Release page](https://github.com/cloudnative-pg/plugin-barman-cloud/releases/tag/v0.13.0)

- **`additionalCommandArgs` for `barman-cloud-restore`** (data restore customization). ([#914](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/914))
- **`lz4` compression support for base backups**; WAL restore errors now classified with precise gRPC statuses; Kubernetes recommended labels added to subresources. ([#868](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/868), [#927](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/927), [#865](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/865))

## 0.12.0 — 2026-04-13

[Release page](https://github.com/cloudnative-pg/plugin-barman-cloud/releases/tag/v0.12.0)

- **Barman updated to 3.18.0**, plus dependency bumps. ([#813](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/813))

## 0.11.0 — 2026-01-30

[Release page](https://github.com/cloudnative-pg/plugin-barman-cloud/releases/tag/v0.11.0)

- **Azure `DefaultAzureCredential`** authentication support (`azureCredentials.useDefaultAzureCredentials: true` on the `ObjectStore`), with an Azure endpoint validation fix. ([#681](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/681), [#710](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/710))
- **WAL archiving performance and memory issues resolved**. ([#746](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/746))

## 0.10.0 — 2025-12-30

[Release page](https://github.com/cloudnative-pg/plugin-barman-cloud/releases/tag/v0.10.0)

- **`pprof-server`** profiling support; WAL files removed from the cache after archiving. ([#538](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/538), [#659](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/659))
- Full environment variables are no longer logged at the default log level; RFC3339-like `targetTime` values without timezone are treated as UTC. ([#589](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/589), [#700](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/700))

## 0.9.0 — 2025-11-06

[Release page](https://github.com/cloudnative-pg/plugin-barman-cloud/releases/tag/v0.9.0)

- Release without feature additions: documentation fix for the `archiveAdditionalCommandArgs` WAL configuration key and dependency updates. ([#630](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/630))

## 0.8.0 — 2025-10-27

[Release page](https://github.com/cloudnative-pg/plugin-barman-cloud/releases/tag/v0.8.0)

- ⚠️ **BREAKING: RBAC resource names are all prefixed `barman-plugin-`** to avoid conflicts with other objects in the cluster; see the [resource-name migration guide](https://cloudnative-pg.io/plugin-barman-cloud/docs/resource-name-migration/). ([#593](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/593))
- Leader-election release-on-cancel enabled so the control plane can RollingUpdate; end-of-WAL flag no longer managed during backup restore; copyright assigned to the Linux Foundation. ([#615](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/615), [#604](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/604), [#571](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/571))

## 0.7.0 — 2025-09-25

[Release page](https://github.com/cloudnative-pg/plugin-barman-cloud/releases/tag/v0.7.0)

- **`logLevel` setting** to control log verbosity; additional sidecar container arguments support; proper gRPC error codes for expected conditions. ([#536](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/536), [#520](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/520), [#549](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/549))
- Object cache management reliability improvements; no more panic when `serverRecoveryWindow` is unset. ([#508](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/508), [#525](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/525))

## 0.6.0 — 2025-08-21

[Release page](https://github.com/cloudnative-pg/plugin-barman-cloud/releases/tag/v0.6.0)

- **Upstream backup and recovery metrics**, plus a last-failed-backup status field and metric. ([#459](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/459), [#467](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/467))
- Empty WAL archive check during WAL archiving; ObjectStore-from-cache retrieval logic; `Cluster` finalizers update permission; sidecar image moved to bookworm. ([#458](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/458), [#429](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/429), [#465](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/465), [#476](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/476))

## 0.5.0 — 2025-06-03

[Release page](https://github.com/cloudnative-pg/plugin-barman-cloud/releases/tag/v0.5.0)

- Release without feature additions: removes the lifecycle `Pod` `Patch` subscription from the control plane. ([#378](https://github.com/cloudnative-pg/plugin-barman-cloud/pull/378))
