---
upstream: https://github.com/vitessio/vitess
last_updated: 2026-09-04
---

# vitess — features

Feature areas of Vitess, each linked to the upstream documentation that covers it. The [concepts guide](https://vitess.io/docs/24.0/concepts/) is the authoritative source; the user-guides cover migrations and operations.

## Architecture & scaling

- **Keyspaces** (`Keyspace`) — the logical, top-level unit of a Vitess deployment; a keyspace can be sharded across many shards. [Keyspace](https://vitess.io/docs/24.0/concepts/keyspace/) - details: [Keyspaces](features/keyspace.md)
- **Shards** (`Shard`) — the unit of sharding; a keyspace is partitioned into shards, each backed by a MySQL replica set. [Shard](https://vitess.io/docs/24.0/concepts/shard/) - details: [Shards](features/shard.md)
- **Tablets** (`Tablet`) — a running MySQL instance (primary, replica, or rdonly) that serves a shard. [Tablet](https://vitess.io/docs/24.0/concepts/tablet/) - details: [Tablets](features/tablet.md)
- **Scaling philosophy** — scale horizontally by adding shards, and vertically within a shard via replication. [Scalability philosophy](https://vitess.io/docs/24.0/overview/scalability-philosophy/)

## Query routing

- **VTGate** (`vtgate`) — the global gateway/proxy that applications connect to; it parses, routes, and reassembles SQL. [VTGate](https://vitess.io/docs/24.0/concepts/vtgate/) - details: [VTGate](features/vtgate.md)
- **Query rewriting** — push down query fragments to shards and rewrite multi-shard queries for parallel execution. [Query rewriting](https://vitess.io/docs/24.0/concepts/query-rewriting/)
- **vschema / routing rules** — define routing rules (including view routing in v24) that map tables and views to keyspaces and shards. [vschema](https://vitess.io/docs/24.0/concepts/vschema/) - details: [vschema](features/vschema.md)

## Replication & high availability

- **VTOrc** (`vtorc`) — orchestration for replication, failover, and switchover, exposing a REST/gRPC control plane. [VTOrc](https://vitess.io/docs/24.0/user-guides/configuration-basic/vtorc/) - details: [VTOrc](features/vtorc.md)
- **Replication graph** — the primary/replica/rdonly topology within a shard and its failover behavior. [Replication graph](https://vitess.io/docs/24.0/concepts/replication-graph/) - details: [Replication graph](features/replication-graph.md)
- **Backup & restore** — schedule and restore tablet backups. [Backup and restore](https://vitess.io/docs/24.0/user-guides/operating-vitess/backup-and-restore/) - details: [Backup & restore](features/backup-and-restore.md)

## Migration & schema changes

- **Online resharding / MoveTables** — move table data between shards online with minimal downtime. [Move tables](https://vitess.io/docs/24.0/user-guides/migration/move-tables/) - details: [MoveTables](features/move-tables.md)
- **Declarative schema migrations** — apply schema changes declaratively across a keyspace. [Declarative migrations](https://vitess.io/docs/24.0/user-guides/schema-changes/declarative-migrations/) - details: [Declarative schema migrations](features/declarative-migrations.md)

## Change data capture

- **VStream** (`vstream`) — stream row changes across shards to external consumers as a sharded, distributed binlog; in v24 also available as GTID-based streaming through VTGate. [VStream](https://vitess.io/docs/24.0/concepts/vstream/) - details: [VStream](features/vstream.md)

## Ecosystem

- **Kubernetes operator** — run and manage Vitess clusters on Kubernetes with CRDs. [Operator](https://vitess.io/docs/24.0/get-started/operator/) - details: [Kubernetes operator](features/kubernetes-operator.md)
- **vtadmin / vtctl** — the management API and CLI for keyspace, shard, tablet, and routing operations. [vtadmin](https://vitess.io/docs/24.0/concepts/vtadmin/), [vtctl](https://vitess.io/docs/24.0/concepts/vtctl/) - details: [vtadmin / vtctl](features/vtadmin-vtctl.md)
