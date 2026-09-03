---
upstream: https://github.com/planetscale/vitess-operator
last_updated: 2026-09-03
---

# vitess-operator — API reference

The Vitess Operator exposes **8** kinds under the API group **`planetscale.com/v2`**, all **Namespaced** and **without short names**. Together they describe the full Vitess topology — from the storage and topology backends down to the data units — plus the backups around them. The canonical schema lives in the [CRD definitions under `deploy/crds`](https://github.com/planetscale/vitess-operator/tree/main/deploy/crds).

| Kind | Short name | Scope | Purpose | Upstream docs |
| ---- | ---------- | ----- | ------- | ------------- |
| `EtcdLockserver` | — | Namespaced | An etcd instance that backs the Vitess topology (the global/local "lock server"). | [CRDs](https://github.com/planetscale/vitess-operator/tree/main/deploy/crds) |
| `VitessCell` | — | Namespaced | A Vitess cell (a unit of deployment, typically a datacenter/region) and the tablet pool that lives in it. | [CRDs](https://github.com/planetscale/vitess-operator/tree/main/deploy/crds) |
| `VitessCluster` | — | Namespaced | A cluster of tablets (spanning one or more cells) that serves a single shard. | [CRDs](https://github.com/planetscale/vitess-operator/tree/main/deploy/crds) |
| `VitessKeyspace` | — | Namespaced | A keyspace (logical database) with its sharding, routing, and table definitions. | [CRDs](https://github.com/planetscale/vitess-operator/tree/main/deploy/crds) |
| `VitessShard` | — | Namespaced | A shard within a keyspace, backed by a `VitessCluster` of tablets. | [CRDs](https://github.com/planetscale/vitess-operator/tree/main/deploy/crds) |
| `VitessBackup` | — | Namespaced | A one-off backup of a shard to the configured backup storage. | [CRDs](https://github.com/planetscale/vitess-operator/tree/main/deploy/crds) |
| `VitessBackupSchedule` | — | Namespaced | A schedule for periodic backups; cluster- and keyspace-level schedules supported from v2.17. | [CRDs](https://github.com/planetscale/vitess-operator/tree/main/deploy/crds) |
| `VitessBackupStorage` | — | Namespaced | The backup storage backend (S3 / GCS / local) and its credentials. | [CRDs](https://github.com/planetscale/vitess-operator/tree/main/deploy/crds) |

Notes:

- No kind defines a short name; address resources by full kind name.
- A deployment requires an `EtcdLockserver` (topology) and one or more `VitessCell` resources before the operator can create `VitessKeyspace`, `VitessShard`, and the `VitessCluster` tablet pools.
- For a walkthrough of a full cluster (including multi-namespace layouts), see the [operator getting-started guide](https://vitess.io/docs/24.0/get-started/operator/).
