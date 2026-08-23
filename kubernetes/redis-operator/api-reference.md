---
upstream: https://github.com/OT-CONTAINER-KIT/redis-operator
last_updated: 2026-08-23
---

# redis-operator — API reference

The operator registers **4 custom resource kinds** under API group/version **`redis.redis.opstreelabs.in/v1beta2`** (the single served and storage version). The generated CRDs live in [`config/crd/bases/`](https://github.com/OT-CONTAINER-KIT/redis-operator/tree/main/config/crd/bases), the Go type definitions in the per-Kind `api/<kind>/v1beta2/` packages, and the rendered reference is published at [CRD Reference / API Docs](https://redis-operator.opstree.dev/docs/crd-reference/api-reference/) on the docs site.

| Kind | Plural | Purpose | Upstream API docs |
| ---- | ------ | ------- | ----------------- |
| `Redis` | `redis` | Standalone Redis instance — image, config, persistence, service exposure, TLS. | [CRD docs](https://redis-operator.opstree.dev/docs/crd-reference/api-reference/), [configuration guide](https://redis-operator.opstree.dev/docs/configuration/redis/) |
| `RedisCluster` | `redisclusters` | Sharded Redis Cluster — `clusterSize` shard count with `replicas` per shard, slot management, online scaling. | [CRD docs](https://redis-operator.opstree.dev/docs/crd-reference/api-reference/), [configuration guide](https://redis-operator.opstree.dev/docs/configuration/rediscluster/) |
| `RedisReplication` | `redisreplications` | One master + `N` replica `StatefulSet` with automatic failover (managed, or delegated to a paired `RedisSentinel`). | [CRD docs](https://redis-operator.opstree.dev/docs/crd-reference/api-reference/), [configuration guide](https://redis-operator.opstree.dev/docs/configuration/redisreplication/) |
| `RedisSentinel` | `redissentinels` | Sentinel deployment for monitoring and failover; standalone, or paired with a `RedisReplication` via `spec.redisReplicationName` (v0.23.0+). | [CRD docs](https://redis-operator.opstree.dev/docs/crd-reference/api-reference/), [getting started](https://redis-operator.opstree.dev/docs/getting-started/sentinel/) |

Notes:

- Kinds use `v1beta2` only (e.g. `apiVersion: redis.redis.opstreelabs.in/v1beta2`); no short names are defined on the CRDs.
- `RedisSentinel` paired with `RedisReplication` is the operator-managed HA path; a lone `RedisReplication` without Sentinel still gets operator-driven failover. The `redisReplicationName` cross-reference and its caveats are documented in [Sentinel documentation](https://redis-operator.opstree.dev/docs/getting-started/sentinel/).
- The workload Helm charts (`redis`, `redis-cluster`, `redis-replication`, `redis-sentinel` in [OT-CONTAINER-KIT/helm-charts](https://github.com/OT-CONTAINER-KIT/helm-charts)) are thin wrappers over these Kinds — the operator, not the chart, performs reconciliation.
- Field-level documentation is intentionally not duplicated here; follow the per-Kind links above.
