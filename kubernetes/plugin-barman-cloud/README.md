---
upstream: https://github.com/cloudnative-pg/plugin-barman-cloud
last_updated: 2026-08-22
---

# plugin-barman-cloud

The reference CNPG-I (CloudNativePG-Interceptor) backup/restore plugin for Barman Cloud: an independently deployed control-plane operator plus a sidecar that the CloudNativePG operator injects into each instance pod. It is the replacement for CloudNativePG's built-in (in-tree, deprecated since 1.26) Barman Cloud integration, scheduled for removal in CloudNativePG 1.31.0. With the plugin, base backups, scheduled backups, continuous WAL archiving, and point-in-time recovery run against an `ObjectStore` CR instead of the `Cluster`'s `backup.barmanObjectStore` section — backup configuration and retention move out of the cluster spec and become a standalone resource.

- Upstream repository: [cloudnative-pg/plugin-barman-cloud](https://github.com/cloudnative-pg/plugin-barman-cloud)
- Documentation: [https://cloudnative-pg.io/plugin-barman-cloud](https://cloudnative-pg.io/plugin-barman-cloud) (versioned)
- License: Apache-2.0
- API group/version: `barmancloud.cnpg.io/v1`
- Helm chart: [ghcr.io/cloudnative-pg/charts/plugin-barman-cloud](https://github.com/cloudnative-pg/plugin-barman-cloud) (chart `0.7.1` ↔ plugin release `v0.14.0`; the chart's default release name is `plugin-barman-cloud`)

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
