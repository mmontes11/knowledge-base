---
upstream: https://github.com/vitessio/vitess
last_updated: 2026-09-04
---

# vitess — shards

A *shard* is a subset of a [keyspace](https://vitess.io/docs/24.0/concepts/keyspace/): a keyspace always contains one or more shards, and a sharded keyspace is partitioned into `N` shards with non-overlapping data. Each shard is backed by a physical MySQL replica set — typically one MySQL primary and any number of replicas, all holding identical data (modulo replication lag). Replicas serve read-only traffic with eventual-consistency guarantees, run long-running analysis queries, and perform administrative work (backup, restore, diff). An *unsharded* keyspace has exactly one shard, named `0` (or sometimes `-`) by convention; production deployments may scale a single keyspace to hundreds of shards.

Upstream: [Shard concept (v24.0)](https://vitess.io/docs/24.0/concepts/shard/) · [Resharding user guide (v24.0)](https://vitess.io/docs/24.0/user-guides/configuration-advanced/resharding/)

## Shard naming and key ranges

Shards are identified by a *key range* over unsigned integer space in keyspace IDs. The naming rules:

- A name represents a range where the left bound is included and the right bound is excluded.
- Notation is hexadecimal.
- Ranges are left-justified and right-padded with zeros.
- A `-` prefix means "anything less than the right value" (`-80` == `00-80`).
- A `-` postfix means "anything greater than or equal to the left value" (`80-`).
- A plain `-` denotes the full key range.

Thus `-80` == `00-80` == `0000-8000` == `000000-800000` == `0000000000000000-8000000000000000`. Note that `80-` is *not* the same as `80-FF`: the latter is `8000-FF00`, so `FFFF` falls outside it, while `80-` means "anything >= `0x80`".

The key range determines which rows a shard holds: a vindex (e.g. `xxhash`) maps a sharding column to a keyspace ID, and the ID selects the shard. `xxhash` produces a 64-bit integer, so all IDs below `0x8000000000000000` land in shard `-80`, and IDs with the top bit set land in `80-`. Because ranges are left-justified, keyspace IDs can be arbitrary length — an `md5` hash (16 bytes) or an arbitrary `varbinary` (the `binary` vindex) work directly as keyspace IDs. Key-range parsing lives in [`go/vt/key/`](https://github.com/vitessio/vitess/tree/main/go/vt/key) (see `ParseKeyRange` in [go/vt/key/key.go](https://github.com/vitessio/vitess/blob/main/go/vt/key/key.go)).

## How a shard maps to physical MySQL

A shard is a topology object; the MySQL servers that actually store and serve its data are [tablets](https://vitess.io/docs/24.0/concepts/tablet/) (`primary`, `replica`, `rdonly`). The topology records the shard's key range and its replication state per cell, and VTGate uses the key-space routing rules to resolve which shards hold a given query's data before fanning out to tablets. Key components in the source tree (paths verified against the current `main` branch):

- [`go/vt/topo/shard.go`](https://github.com/vitessio/vitess/blob/main/go/vt/topo/shard.go) — the shard record in the [topology service](https://vitess.io/docs/24.0/concepts/topology-service/): key range, primary/replica replication graphs, and read-only tablets per cell; [`go/vt/topo/shard_lock.go`](https://github.com/vitessio/vitess/blob/main/go/vt/topo/shard_lock.go) serializes shard mutations.
- [`go/vt/key/key.go`](https://github.com/vitessio/vitess/blob/main/go/vt/key/key.go) — key range and keyspace ID parsing/formatting (`ParseKeyRange`, hex range notation).
- [`go/vt/vtctl/vtctl.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/vtctl.go) — shard lifecycle commands: `CreateShard`, `DeleteShards`, `RebuildKeyspace`, `Reparent`, and the `Reshard` workflow entry points.
- [`go/vt/vttablet/`](https://github.com/vitessio/vitess/tree/main/go/vt/vttablet) — the tablet side: [`go/vt/vttablet/tabletmanager/`](https://github.com/vitessio/vitess/tree/main/go/vt/vttablet/tabletmanager) handles tablet lifecycle (init, restore, reparenting, serving), and [`go/vt/vttablet/tabletserver/`](https://github.com/vitessio/vitess/tree/main/go/vt/vttablet/tabletserver) runs the query-serving stack on each MySQL instance of a shard.

## Commands and operations

Shard operations are exposed through `vtctl` / `vtctldclient` (see the [vtctl concept](https://vitess.io/docs/24.0/concepts/vtctl/)). The core set:

- **CreateShard** — create an empty shard in the topology for a keyspace and key range (`vtctldclient CreateShard keyspace/shard`).
- **DeleteShards** — remove shard records from the topology (`vtctldclient DeleteShards --recursive keyspace/shard`); used to tear down source shards after resharding.
- **Reparent** / **EmergencyReparent** — change which tablet is the shard primary ([reparenting guide](https://vitess.io/docs/24.0/user-guides/configuration-advanced/reparenting/)); the [VTOrc](https://vitess.io/docs/24.0/user-guides/configuration-basic/vtorc/) controller automates this.
- **RebuildKeyspace** — reconcile a keyspace's serving state (shard key ranges vs. routing rules) after topology drift.
- **Reshard** — change the shard count of a live keyspace online; subcommands `create`, `SwitchTraffic`, and `complete` drive the workflow ([Reshard reference](https://vitess.io/docs/24.0/reference/vreplication/reshard/)).
- **VDiff** — compare source and target shards to verify data integrity during/after a reshard ([VDiff reference](https://vitess.io/docs/24.0/reference/vreplication/vdiff/)).

## Resharding

Resharding changes the number of shards on a live cluster by either splitting shards into smaller pieces or merging neighbors into bigger pieces, without blocking reads or writes. The workflow (from the [v24.0 resharding guide](https://vitess.io/docs/24.0/user-guides/configuration-advanced/resharding/)):

1. **Preparation** — plan the new VSchema: pick sharding columns and [primary vindexes](https://vitess.io/docs/24.0/reference/features/vindexes/#the-primary-vindex), and replace `auto_increment` with [sequences](https://vitess.io/docs/24.0/reference/features/vitess-sequences/) (an unsharded single-row table whose values VTGate fills in transparently on `INSERT`).
2. **Create new shards** — add the target shards to the topology (or apply shard CRDs when using the [Kubernetes operator](https://vitess.io/docs/24.0/get-started/operator/)).
3. **Start the reshard** — e.g. `vtctldclient Reshard --target-keyspace customer --workflow cust2cust create --source-shards '0' --target-shards '-80,80-'`; the [VReplication](https://vitess.io/docs/24.0/design-docs/vreplication/) materializers copy data from source to destination shards and stream live changes so they catch up.
4. **Validate** — run `VDiff` to confirm source and target shards match (`HasMismatch: false`).
5. **Switch traffic in stages** — first non-primary reads (`Reshard ... SwitchTraffic --tablet-types=rdonly,replica`), then writes and primary reads (`Reshard ... SwitchTraffic`), so the new tablets prove healthy before taking writes.
6. **Complete and clean up** — `Reshard ... complete` finalizes the workflow; then `DeleteShards --recursive` removes the source shard from the topology and the old on-disk data is deleted manually.

Because resharding is repeatable, a suboptimal initial sharding scheme can always be fixed later by resharding again. See also [Region based sharding](https://vitess.io/docs/24.0/user-guides/configuration-advanced/region-sharding/) for geo-distributed shard layouts.

## Related

- [Keyspace](https://vitess.io/docs/24.0/concepts/keyspace/) — the logical parent of shards
- [Tablet](https://vitess.io/docs/24.0/concepts/tablet/) — the MySQL instance that serves a shard
- [Replication graph](https://vitess.io/docs/24.0/concepts/replication-graph/) — intra-shard replication topology
- [VSchema](https://vitess.io/docs/24.0/concepts/vschema/) — routing rules mapping tables to shards
- [Scalability philosophy](https://vitess.io/docs/24.0/overview/scalability-philosophy/) — why Vitess scales by adding shards
