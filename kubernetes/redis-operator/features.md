---
upstream: https://github.com/OT-CONTAINER-KIT/redis-operator
last_updated: 2026-08-23
---

# redis-operator — features

Key feature areas, each linked to the upstream documentation covering it. The [docs site](https://redis-operator.opstree.dev/docs/) is authoritative.

## Deployment modes

- **Standalone** (`Redis`): a single-replica Redis with operator-managed config, storage, and service exposure. [Getting started](https://redis-operator.opstree.dev/docs/getting-started/standalone/), [configuration](https://redis-operator.opstree.dev/docs/configuration/redis/)
- **Replication** (`RedisReplication`): one master plus `N` replicas on a `StatefulSet`, with operator-managed failover and connection info in status (v0.23.0+). [Getting started](https://redis-operator.opstree.dev/docs/getting-started/replication/), [configuration](https://redis-operator.opstree.dev/docs/configuration/redisreplication/)
- **Sentinel** (`RedisSentinel`): Sentinel deployments for monitoring and failover — standalone, or paired with a `RedisReplication` (v0.23.0+) via `spec.redisReplicationName`. [Getting started](https://redis-operator.opstree.dev/docs/getting-started/sentinel/)
- **Cluster** (`RedisCluster`): sharded Redis Cluster (`clusterSize` × `replicas`), operator-managed slot allocation and membership. [Getting started](https://redis-operator.opstree.dev/docs/getting-started/cluster/), [configuration](https://redis-operator.opstree.dev/docs/configuration/rediscluster/)

## High availability and failover

- **Automatic failover**: operator-driven failover for `RedisReplication` (with or without a paired Sentinel), automatic recovery after master pod deletion (v0.22.0+), automatic re-election improvements (v0.26.0). [Failover testing guide](https://redis-operator.opstree.dev/docs/getting-started/failover-testing/)
- **Cluster self-repair**: disconnected `RedisCluster` nodes are detected and re-added automatically (v0.22.0+). [Release v0.22.0](https://github.com/OT-CONTAINER-KIT/redis-operator/releases/tag/v0.22.0)

## Scaling

- **Online cluster scaling**: scale `RedisCluster` shard count up and down with round-robin shard transfer and resilience to open/failovered slots; single-node clusters can be scaled (v0.26.0+). [Upgrading / scaling guide](https://redis-operator.opstree.dev/docs/advance-configuration/upgrading/)
- **StatefulSet control**: configurable `podManagementPolicy` (v0.26.0+) and rolling-update strategy (v0.23.0+).

## Persistence and storage

- **PVC-backed persistence** per Pod with `volumeClaimTemplate` support (including resize detection and changing the template size, v0.20.2+/v0.20.1 fix).
- **PersistentVolumeClaimRetentionPolicy** to control PVC lifecycle on resource deletion (v0.22.0+). [Configuration](https://redis-operator.opstree.dev/docs/configuration/rediscluster/)

## Security

- **TLS** for client and inter-node traffic, with managed (cert-manager compatible, `ca.crt` usage since v0.24.0) or external certificates. [TLS and security](https://redis-operator.opstree.dev/docs/advance-configuration/tls-and-security/)
- **Access control**: password/password-less setup, Redis ACL (including ACL loaded from a PVC, v0.23.0+).
- **Pod security contexts** on all generated workloads.
- **Feature gates**: opt-in staging of operator behavior changes. [Feature gates](https://redis-operator.opstree.dev/docs/advance-configuration/feature-gates/)
- **Namespace-scoped RBAC** mode for the operator (v0.26.0+). [Release v0.26.0](https://github.com/OT-CONTAINER-KIT/redis-operator/releases/tag/v0.26.0)

## Networking

- **IPv4 and IPv6** setups, including IPv6 in Redis cluster commands (v0.24.0+).
- **Service exposure**: `kubernetesConfig` serviceType control (ClusterIP/NodePort/LoadBalancer), external service with configurable nodePort, configurable service DNS domain. [Exposing Redis](https://redis-operator.opstree.dev/docs/advance-configuration/exposing-redis/)

## Monitoring

- **redis-exporter per workload**: built-in exporter sidecar/deployment for all four modes with Prometheus discovery annotations (`prometheus.io/scrape`) and optional per-install `ServiceMonitor` (v0.24.0 added the exporter service for cluster/replication/sentinel). [Monitoring](https://redis-operator.opstree.dev/docs/monitoring/metrics/)
- **Operator-level metrics** (v0.22.0+, HTTPS + RBAC hardened in v0.26.0), pprof profiling, and a maintained [Grafana dashboard](https://github.com/OT-CONTAINER-KIT/redis-operator/tree/main/grafana/dashboards).

## Configuration

- **Advanced configuration**: custom `redis.conf`/`sentinel.conf` values, externally mounted config (ConfigMap/Secret), node config volumes, `maxMemoryPercentOfLimit`, init-container image tuning (v0.23.0+), liveness/readiness probes (v0.23.0+). [Configuration](https://redis-operator.opstree.dev/docs/configuration/)
- **Operator tuning**: `maxConcurrentReconciles`, `execCommandTimeout`, kube client QPS/timeout (v0.22.0+/v0.26.0+).

## Deployment

- **Helm**: the `redis-operator` chart plus per-mode workload charts (`redis`, `redis-cluster`, `redis-replication`, `redis-sentinel`) in the [OT-CONTAINER-KIT/helm-charts repository](https://github.com/OT-CONTAINER-KIT/helm-charts) (`https://ot-container-kit.github.io/helm-charts/`), Helm >= 3. [Installation](https://redis-operator.opstree.dev/docs/installation/installation/)
- **OperatorHub**: [redis-operator](https://operatorhub.io/operator/redis-operator).
- **Validation and upgrades**: post-install validation and operator/CRD upgrade procedures are documented. [Validation](https://redis-operator.opstree.dev/docs/installation/validation/), [Upgradation](https://redis-operator.opstree.dev/docs/installation/upgradation/)
