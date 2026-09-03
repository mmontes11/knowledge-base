---
upstream: https://github.com/vitessio/vitess
last_updated: 2026-09-03
---

# vitess

[Vitess](https://vitess.io/) is a scalable MySQL database cluster and proxy system, written in Go. It lets you scale a single logical MySQL database across many physical shards (keyspaces and shards) while remaining transparent to application clients: applications connect to Vitess as if it were one MySQL server, and Vitess handles sharding, query routing, and replication. Vitess is designed for cloud-native, horizontally scalable deployments and is the engine behind PlanetScale.

- Upstream repository: [vitessio/vitess](https://github.com/vitessio/vitess)
- Documentation: [https://vitess.io/docs/](https://vitess.io/docs/)
- v24.0 docs: [https://vitess.io/docs/24.0/](https://vitess.io/docs/24.0/)
- Concepts guide: [https://vitess.io/docs/24.0/concepts/](https://vitess.io/docs/24.0/concepts/)
- Get started with the operator: [https://vitess.io/docs/24.0/get-started/operator/](https://vitess.io/docs/24.0/get-started/operator/)
- License: Apache-2.0
- Written in: Go

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
