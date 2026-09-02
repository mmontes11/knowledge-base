---
upstream: https://github.com/OT-CONTAINER-KIT/redis-operator
last_updated: 2026-08-23
---

# redis-operator

A Golang-based Redis operator for Kubernetes that deploys and operates Redis workloads in four modes — standalone, sharded cluster, master/replica replication, and Sentinel — applying best-practice defaults (TLS, security, monitoring) via custom resources. It manages failover and recovery, in-cluster monitoring with redis-exporter, and password/password-less or authenticated setups; it supports Redis `>=6` on Kubernetes 1.18+ and is distributed as Helm charts (plus OperatorHub).

- Upstream repository: https://github.com/OT-CONTAINER-KIT/redis-operator
- Documentation: https://redis-operator.opstree.dev/ (docs source: [`docs/content/en/docs/`](https://github.com/OT-CONTAINER-KIT/redis-operator/tree/main/docs/content/en/docs) in the upstream repository)
- License: Apache-2.0
- API group/version: `redis.redis.opstreelabs.in/v1beta2`
- Helm charts: [OT-CONTAINER-KIT helm-charts](https://github.com/OT-CONTAINER-KIT/helm-charts), repo `https://ot-container-kit.github.io/helm-charts/`
- Images: `quay.io/opstree/redis`, `quay.io/opstree/redis-sentinel`, `quay.io/opstree/redis-exporter`

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
