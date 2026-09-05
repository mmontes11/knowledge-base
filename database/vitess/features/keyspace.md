---
upstream: https://github.com/vitessio/vitess
last_updated: 2026-09-04
---

# vitess — keyspace

The keyspace is the top-level logical unit of a Vitess deployment: a self-contained database that may be sharded across many shards, with its own schema, routing rules (VSchema), and replication topology. A single Vitess cluster — one global topology server and `vtctld` — can host many keyspaces, and each keyspace scales independently: you add shards to one keyspace without touching any other.

## What it is

- **Logical, not physical.** A keyspace groups shards; each shard owns a key range (a contiguous slice of the keyspace ID space) and is backed by a MySQL replica set of tablets. The keyspace itself has no physical state — it is a record in the topology server.
- **Sharded vs unsharded.** An unsharded keyspace holds a single shard covering the whole key range (by convention named `0` or `-`); a sharded keyspace partitions the key range across several shards named after their key ranges (e.g. `-80`, `80-`).
- **Keyspace ID.** Rows route to shards by a keyspace ID derived from the sharding key through the VSchema's vindexes. The default ID space is 8 bytes (0..2^64); sub-64-bit and non-uniform IDs are supported for specific workloads. [Key space ID](https://vitess.io/docs/24.0/concepts/keyspace-id/)
- **VSchema.** Every keyspace carries a VSchema in the topology that defines its tables, vindexes, and routing rules (including view-based routing in v24). [vschema](https://vitess.io/docs/24.0/concepts/vschema/)

## How it works

- **Topology records.** Keyspace and shard definitions live in the global topology server (etcd, Consul, ZooKeeper, or K8s). [`proto/topodata.proto`](https://github.com/vitessio/vitess/blob/main/proto/topodata.proto) defines the wire records (`Keyspace`, `Shard`, `EndTablet`); the Go topology layer implements them in [`go/vt/topo/keyspace.go`](https://github.com/vitessio/vitess/blob/main/go/vt/topo/keyspace.go) (the `Keyspace` record and `KeyspaceInfo`) and [`go/vt/topo/shard.go`](https://github.com/vitessio/vitess/blob/main/go/vt/topo/shard.go) (the `Shard` record and `ShardInfo`).
- **Per-cell view (SRVKeyspace).** `vtctld` publishes a per-cell `SRVKeyspace` record listing the shards serving in that cell and the cells serving the keyspace. [`go/vt/topo/srv_keyspace.go`](https://github.com/vitessio/vitess/blob/main/go/vt/topo/srv_keyspace.go) provides the read/watch API (`WatchSrvKeyspace`, `GetSrvKeyspaceNames`) that VTGate uses to learn where queries should be routed.
- **VTGate discovery and events.** VTGate watches topology changes for the keyspaces it serves. [`go/vt/discovery/keyspace_events.go`](https://github.com/vitessio/vitess/blob/main/go/vt/discovery/keyspace_events.go) implements the `KeyspaceEventWatcher`, which consolidates topology-server events with the gRPC health-check stream to detect when availability incidents (failovers, resharding) across a cell's keyspaces have resolved.
- **Routing.** VTGate computes each row's keyspace ID from the sharding key via the keyspace's VSchema and sends the query fragment to the shard whose key range contains it; results are merged back into a single result set. [Sharding](https://vitess.io/docs/24.0/concepts/sharding/)
- **Cross-keyspace replication.** VReplication-based workflows (MoveTables, resharding, lookup vindexes) replicate between keyspaces; per-workflow state is stored in the `vreplication` sidecar tables on the serving vttablets, whose schema lives in [`go/vt/sidecardb/schema/vreplication/`](https://github.com/vitessio/vitess/tree/main/go/vt/sidecardb/schema/vreplication). [Move tables](https://vitess.io/docs/24.0/user-guides/migration/move-tables/)
- **VTOrc configuration.** Keyspace- and shard-level VTOrc settings (e.g. `disable_emergency_reparent`) are topology records defined in [`proto/vtorcdata.proto`](https://github.com/vitessio/vitess/blob/main/proto/vtorcdata.proto).

## Key components

| Component | Where | Role |
| --------- | ----- | ---- |
| `Keyspace` / `Shard` records | [`go/vt/topo/keyspace.go`](https://github.com/vitessio/vitess/blob/main/go/vt/topo/keyspace.go), [`go/vt/topo/shard.go`](https://github.com/vitessio/vitess/blob/main/go/vt/topo/shard.go) (proto: [`proto/topodata.proto`](https://github.com/vitessio/vitess/blob/main/proto/topodata.proto)) | The topology definitions a cluster persists |
| `SRVKeyspace` | [`go/vt/topo/srv_keyspace.go`](https://github.com/vitessio/vitess/blob/main/go/vt/topo/srv_keyspace.go) | Per-cell serving view consumed by VTGate |
| VSchema | [`proto/vschema.proto`](https://github.com/vitessio/vitess/blob/main/proto/vschema.proto), per-keyspace in the topology | Tables, vindexes, and routing rules for the keyspace |
| `KeyspaceEventWatcher` | [`go/vt/discovery/keyspace_events.go`](https://github.com/vitessio/vitess/blob/main/go/vt/discovery/keyspace_events.go) | Detects resolution of failover/resharding incidents across keyspaces |
| vreplication sidecar tables | [`go/vt/sidecardb/schema/vreplication/`](https://github.com/vitessio/vitess/tree/main/go/vt/sidecardb/schema/vreplication) | State for MoveTables/resharding workflows that span shards or keyspaces |
| VTOrc keyspace config | [`proto/vtorcdata.proto`](https://github.com/vitessio/vitess/blob/main/proto/vtorcdata.proto) | Keyspace-level replication/HA settings |

## Lifecycle & commands

Key operations are implemented in [`go/vt/vtctl/vtctl.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/vtctl.go) and available as `vtctl` subcommands (or `vtctldclient` equivalents):

- `CreateKeyspace` / `DeleteKeyspace` — create or drop the keyspace's topology record; `CreateKeyspace` can apply a schema and VSchema in the same step.
- `CreateShard` / `DeleteShard` — add or remove a shard with a key range under the keyspace.
- `RemoveKeyspaceCell` / `RemoveShardCell` — detach a cell from the keyspace or a shard's serving view.
- Resharding — done by creating target shards and running a MoveTables workflow (`vtctldclient vreplication MoveTables Create ...`), then switching traffic and deleting source shards. [Move tables](https://vitess.io/docs/24.0/user-guides/migration/move-tables/)
- `RebuildKeyspaceGraph` / `RebuildVSchemaGraph` — rebuild per-cell `SRVKeyspace`/VSchema records from the global topology after a keyspace changes shape. [Keyspaces and shards](https://vitess.io/docs/24.0/user-guides/configuration-basic/keyspaces-shards/)

## Upstream docs

- [Keyspace (concept)](https://vitess.io/docs/24.0/concepts/keyspace/)
- [Key space ID (concept)](https://vitess.io/docs/24.0/concepts/keyspace-id/)
- [Sharding (concept)](https://vitess.io/docs/24.0/concepts/sharding/)
- [Shard (concept)](https://vitess.io/docs/24.0/concepts/shard/)
- [Keyspaces and shards (user guide)](https://vitess.io/docs/24.0/user-guides/configuration-basic/keyspaces-shards/)
- [Move tables (user guide)](https://vitess.io/docs/24.0/user-guides/migration/move-tables/)
- [vschema (concept)](https://vitess.io/docs/24.0/concepts/vschema/)
