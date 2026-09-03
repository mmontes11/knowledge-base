---
upstream: https://github.com/planetscale/vitess-operator
last_updated: 2026-09-03
---

# vitess-operator — features

Feature areas of the Vitess Operator, each linked to the upstream documentation that covers it. The [CRD definitions](https://github.com/planetscale/vitess-operator/tree/main/deploy/crds) and the [API documentation](https://github.com/planetscale/vitess-operator/blob/main/docs/api.md) are the authoritative source; the [operator getting-started guide](https://vitess.io/docs/24.0/get-started/operator/) walks through a full deployment.

## Cluster management

- **Topology & cells** — manage the etcd topology backend (`EtcdLockserver`) and Vitess cells (`VitessCell`) as custom resources. [CRDs](https://github.com/planetscale/vitess-operator/tree/main/deploy/crds)
- **Keyspaces & shards** — declare keyspaces (`VitessKeyspace`) and shards (`VitessShard`) with their sharding and routing configuration, backed by tablet pools (`VitessCluster`). [CRDs](https://github.com/planetscale/vitess-operator/tree/main/deploy/crds)
- **Multi-namespace deployments** — run the operator in one namespace and Vitess clusters in others (default examples since v2.15). [Getting started](https://vitess.io/docs/24.0/get-started/operator/)

## Backups & recovery

- **Scheduled backups** — automate periodic backups via `VitessBackupSchedule` (cluster- and keyspace-level schedules from v2.17) and `VitessBackup`, backed by `VitessBackupStorage` (S3 / GCS / local). Scheduled backups run `vtbackup` pods since v2.15. [CRDs](https://github.com/planetscale/vitess-operator/tree/main/deploy/crds)
- **Point-in-time recovery** — binary logs for PITR are kept in a shared directory (v2.17). [Release notes](https://github.com/planetscale/vitess-operator/releases/tag/v2.17.0)

## Observability & operations

- **mysqld_exporter** — per-tablet MySQL metrics via a `mysqld_exporter` sidecar, with configurable extra flags and resources (v0.15.0+ supported in v2.17). [API](https://github.com/planetscale/vitess-operator/blob/main/docs/api.md)
- **VTGate autoscaling & rollout** — auto-scale vtgate with HPA and configure its rolling-update strategy (v2.15). [Getting started](https://vitess.io/docs/24.0/get-started/operator/)
- **Rolling maintenance** — `vttablet` performs a rolling restart when its storage size changes (v2.17). [Release notes](https://github.com/planetscale/vitess-operator/releases/tag/v2.17.0)
