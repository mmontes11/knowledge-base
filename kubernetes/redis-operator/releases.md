---
upstream: https://github.com/OT-CONTAINER-KIT/redis-operator
last_updated: 2026-08-23
---

# redis-operator — releases

Latest 10 official releases, newest first. Check the ⚠️ entries before upgrading; see also the upstream [release history](https://redis-operator.opstree.dev/docs/release-history/) and [upgrade guide](https://redis-operator.opstree.dev/docs/installation/upgradation/).

## v0.26.0 — 2026-07-15

[Release page](https://github.com/OT-CONTAINER-KIT/redis-operator/releases/tag/v0.26.0)

- **Enhanced `RedisReplication`/`RedisSentinel` master election** ([#1821](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1821)).
- **Operator tuning knobs**: new `maxConcurrentReconciles` and `execCommandTimeout` flags, dynamic configuration support for Redis instances, and HTTPS + RBAC for the operator metrics endpoint ([#1810](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1810), [#1805](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1805), [#1802](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1802)).
- **Namespace-scoped RBAC mode** ([#1637](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1637)); Sentinel pod placement controls (affinity, topologySpreadConstraints, tolerations) ([#1786](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1786)); single-node `RedisCluster` can now be scaled up and down ([#1522](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1522)).
- Fixes: heal failed followers after pod restarts ([#1705](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1705)), batch `CLUSTER ADDSLOTS` to avoid exec URL limits ([#1706](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1706)), sanitize Sentinel agent config against injection ([#1763](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1763)).

## v0.25.0 — 2026-05-13

[Release page](https://github.com/OT-CONTAINER-KIT/redis-operator/releases/tag/v0.25.0)

- **Fix**: mount config on an `emptyDir` volume so `sentinel.conf` persists across container restarts ([#1725](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1725)).
- **Fix**: prevent crash loop when all replication pods restart with an empty `realMaster` ([#1720](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1720)).
- Docs: added `RedisReplication` configuration documentation; **updated the v0.24.0 migration guide** covering the TLS CA-certificate and webhook-configuration changes from the previous release ([#1718](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1718)).

## v0.24.0 — 2026-03-13

[Release page](https://github.com/OT-CONTAINER-KIT/redis-operator/releases/tag/v0.24.0)

- ⚠️ **Breaking**: the operator now references `ca.crt` instead of `ca.key`, fixing startup failures when using cert-manager-issued certificates ([#1644](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1644)).
- ⚠️ **Breaking**: the mutating webhook configuration name now includes the Helm release-name prefix ([#1651](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1651)) — existing installs must follow the [updated migration guide](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1718).
- **Sentinel support in the `redis-replication` chart** ([#1684](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1684)); **redis-exporter metrics service for all four modes** ([#1658](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1658)); `RedisCluster` scale-out made resilient to failover and open slots ([#1629](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1629), [#1647](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1647)); IPv6 support in cluster commands ([#1703](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1703)).

## v0.23.0 — 2026-01-08

[Release page](https://github.com/OT-CONTAINER-KIT/redis-operator/releases/tag/v0.23.0)

- **New**: Sentinel support for `RedisReplication` — pair a `RedisSentinel` with a replication resource to delegate failover ([#1610](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1610)).
- Configurable `livenessProbe`/`readinessProbe` on generated StatefulSets ([#1625](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1625)); `connectionInfo` added to `RedisReplication` status ([#1627](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1627)); support for changing the StatefulSet Rolling Update strategy ([#1595](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1595)).
- ACL loaded from a PVC ([#1582](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1582)); configurable service DNS domain ([#1580](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1580)); expose Redis via `kubernetesConfig` serviceType ([#1596](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1596)).

## v0.22.2 — 2025-10-29

[Release page](https://github.com/OT-CONTAINER-KIT/redis-operator/releases/tag/v0.22.2)

- **Improved `RedisCluster` health checks**: retry with multi-node validation ([#1575](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1575)).
- Fixes: operator crash when a PodDisruptionBudget is defined only for Redis followers ([#1563](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1563)); cluster creation/scale with NodePort and TLS enabled ([#1568](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1568)); external-config included in the config initContainer ([#1566](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1566)).

## v0.22.1 — 2025-09-19

[Release page](https://github.com/OT-CONTAINER-KIT/redis-operator/releases/tag/v0.22.1)

- Bug-fix release: ServiceMonitor defaults to the release namespace instead of the monitoring namespace ([#1537](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1537)); correct operator image in init containers ([#1530](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1530)); reset Sentinel config on reconcile ([#1533](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1533)); context timeout on local-IP lookup.
- Grafana dashboard updated from Grafana v8 to v12.

## v0.22.0 — 2025-09-04

[Release page](https://github.com/OT-CONTAINER-KIT/redis-operator/releases/tag/v0.22.0)

- ⚠️ **Safety**: removed a dangerous failover operation that could cause data loss ([#1505](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1505)).
- **PersistentVolumeClaimRetentionPolicy support** ([#1448](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1448)); automatic repair of disconnected `RedisCluster` nodes whenever detected ([#1426](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1426)); `clusterSize` validation webhook ([#1458](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1458)).
- Operator controller metrics + pprof profiling config ([#1431](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1431), [metrics guide](https://redis-operator.opstree.dev/docs/monitoring/metrics/)); `RedisReplication` recovery from master pod deletion without Sentinel ([#1449](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1449)).

## v0.21.0 — 2025-06-30

[Release page](https://github.com/OT-CONTAINER-KIT/redis-operator/releases/tag/v0.21.0)

- **Fix**: resolve StatefulSet selector immutability issues that blocked updates on changed labels/selectors ([#1382](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1382)).
- Automatic max-memory configuration for Redis instances ([#1411](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1411)); round-robin shard transfer when scaling in a Redis Cluster ([#1412](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1412)); cluster observability metrics ([#1392](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1392)).
- Avoid Sentinel restart after a replication failover ([#1381](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1381)).

## v0.20.2 — 2025-05-12

[Release page](https://github.com/OT-CONTAINER-KIT/redis-operator/releases/tag/v0.20.2)

- ⚠️ **Build**: migrated tooling from `kubebuilder` v3 to v4 ([#1340](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1340)).
- **Feature gates support** in the operator for staged rollout of behavior ([#1333](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1333)) — documented at [Feature Gates](https://redis-operator.opstree.dev/docs/advance-configuration/feature-gates/).
- VolumeClaimTemplate resize detection and scaling out with a new VCT size ([#1342](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1342)); panic fix when retrieving StatefulSets ([#1330](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1330)).

## v0.20.1 — 2025-04-27

[Release page](https://github.com/OT-CONTAINER-KIT/redis-operator/releases/tag/v0.20.1)

- Fix: move VCT (volumeClaimTemplates) logic before the StatefulSet diff calculation, correcting update behavior for `RedisCluster` storage templates ([#1322](https://github.com/OT-CONTAINER-KIT/redis-operator/pull/1322)).
