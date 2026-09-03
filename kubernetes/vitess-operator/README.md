---
upstream: https://github.com/planetscale/vitess-operator
last_updated: 2026-09-03
---

# vitess-operator

The [Vitess Operator](https://github.com/planetscale/vitess-operator) is a Kubernetes operator that manages the lifecycle of [Vitess](../database/vitess/README.md) clusters. It deploys and maintains the Vitess components — the etcd topology backend, cells, clusters, keyspaces, and shards — as custom resources and keeps the underlying MySQL datastores in sync with declared state. It runs as a standard controller-manager with its CRDs defined in the `planetscale.com/v2` API group.

- Upstream repository: [planetscale/vitess-operator](https://github.com/planetscale/vitess-operator)
- CRD definitions: [deploy/crds](https://github.com/planetscale/vitess-operator/tree/main/deploy/crds)
- API documentation: [docs/api.md](https://github.com/planetscale/vitess-operator/blob/main/docs/api.md)
- Get started with the operator: [https://vitess.io/docs/24.0/get-started/operator/](https://vitess.io/docs/24.0/get-started/operator/)
- License: Apache-2.0

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
