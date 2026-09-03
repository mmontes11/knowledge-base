---
upstream: https://github.com/vitessio/vitess
last_updated: 2026-09-03
---

# vitess — API reference

Vitess is a non-Kubernetes project: its primary API surface is the standard MySQL wire protocol (applications connect with any MySQL client to a VTGate instance), plus a set of control-plane surfaces — the gRPC/REST daemons (`vtctld`, `vtadmin`, `vtorc`) and the CLI tools (`vtctl`, `vtctldclient`) — that manage the topology, keyspaces, shards, and tablets. This table links, it does not copy.

| Surface | Purpose | Upstream docs |
| ------- | ------- | ------------- |
| MySQL wire protocol (via VTGate) | The primary API: applications connect with any standard MySQL client to VTGate and issue SQL; Vitess shards, routes, and aggregates results transparently. | [Architecture](https://vitess.io/docs/24.0/overview/architecture/) |
| VTGate | The global gateway that parses queries, routes each fragment to the correct shard, and reassembles results. | [VTGate](https://vitess.io/docs/24.0/concepts/vtgate/) |
| vtctld (gRPC) | The control daemon exposing the gRPC control-plane API used to manage topology, keyspaces, shards, and tablets. | [vtctld](https://vitess.io/docs/24.0/concepts/vtctld/) |
| vtadmin (gRPC) | The management API for keyspace, shard, and tablet lifecycle and metadata (the backing service for the admin UI). | [vtadmin](https://vitess.io/docs/24.0/concepts/vtadmin/) |
| vtorc (REST / gRPC) | The orchestration control plane for replication, failover, and switchover. | [VTOrc](https://vitess.io/docs/24.0/user-guides/configuration-basic/vtorc/) |
| vtctl / vtctldclient (CLI) | Command-line tooling for topology, keyspace, shard, schema, routing-rule, and migration operations. | [vtctl](https://vitess.io/docs/24.0/concepts/vtctl/) |
| VStream | Stream row changes across shards to external consumers, acting as a distributed, sharded binlog. | [VStream](https://vitess.io/docs/24.0/concepts/vstream/) |
| Query rewriting | Push down query fragments to shards and rewrite multi-shard queries so they run in parallel. | [Query rewriting](https://vitess.io/docs/24.0/concepts/query-rewriting/) |

Notes:

- The MySQL wire protocol is the only surface application code talks to; the gRPC/REST and CLI surfaces are operational and used to change the shape of the cluster (sharding, keyspaces, failovers) rather than to serve queries.
- The Kubernetes deployment of Vitess is managed by a separate operator; see the [Vitess Operator](../kubernetes/vitess-operator/api-reference.md) and its [getting-started guide](https://vitess.io/docs/24.0/get-started/operator/).
