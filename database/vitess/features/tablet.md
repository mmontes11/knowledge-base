---
upstream: https://github.com/vitessio/vitess
last_updated: 2026-09-04
---

# vitess — Tablets

A **tablet** is a combination of a `mysqld` process and a corresponding `vttablet` process, usually running on the same machine. It is the unit Vitess uses to run a single MySQL instance inside a [shard](https://vitess.io/docs/24.0/concepts/shard/): each shard is backed by one or more tablets, and clients reach a tablet through a [VTGate](https://vitess.io/docs/24.0/concepts/vtgate/) server. Every tablet carries a **tablet type** that fixes its current role in the shard's [replication graph](https://vitess.io/docs/24.0/concepts/replication-graph/).

Authoritative references: [Tablet concept](https://vitess.io/docs/24.0/concepts/tablet/) · [VTTablet design doc](https://vitess.io/docs/design-docs/vttablet/)

## Tablet types

The role is the `TabletType` enum (`topodatapb.TabletType`, package `topodata`; generated from [`proto/topodata.proto`](https://github.com/vitessio/vitess/blob/main/proto/topodata.proto)). The roles described in the concept guide:

| Type | Enum value | Meaning |
| --- | --- | --- |
| **primary** | `PRIMARY` (1) | A *replica* tablet that is currently the MySQL primary for its shard. Exactly one per shard. |
| **replica** | `REPLICA` (2) | A MySQL replica eligible to be promoted to primary; conventionally serves live, user-facing traffic. |
| **rdonly** | `RDONLY` (3) | A MySQL replica that cannot be promoted to primary; reserved for background work (backups, data dumps, heavy analytics, MapReduce). |
| **backup** | `BACKUP` (6) | Transient: replication stopped at a consistent snapshot so the tablet can upload a new shard backup, then it returns to its previous type. |
| **restore** | `RESTORE` (7) | Transient: started empty and restoring from the latest backup; afterwards it begins replicating and becomes `replica` or `rdonly`. |
| **drained** | `DRAINED` (8) | Reserved by a Vitess background process (e.g. the rdonly tablets used during resharding); no longer serving. |

`master` is a deprecated alias of `primary`. The enum also defines `UNKNOWN` (0), `SPARE` (4), and `EXPERIMENTAL` (5) — reserved/off-the-graph states not described in the concept guide. Parse and enumerate helpers live in the `topoproto` package (`ParseTabletType`, `AllTabletTypes`).

## How it works

- **Registration.** On start, `vttablet` registers itself in the [topology service](https://vitess.io/docs/24.0/concepts/topology-service/) under a unique **alias** of the form `cell:uid` (cell = deployment zone, uid = numeric id). The topology record holds the keyspace, shard, type, and tablet endpoint.
- **Health & discovery.** `vttablet` continuously reports its live role and replication lag over a health stream. The discovery/health-check layer aggregates these and publishes health events, which VTGate consumes to route queries to the correct tablet (primary for writes, replicas for reads).
- **Role transitions.** Type changes (promote to primary, demote to replica, drain, restore) are driven by the **tabletmanager** through RPCs — init, set type, start/stop replication, backup, restore. The **tabletserver** runs the resulting role state machine and flips its serving behavior accordingly. VTOrc coordinates the ordered sequence of these RPCs for switchover and failover.
- **Query serving.** The tabletserver is the per-tablet query engine: it accepts connections over the MySQL protocol and gRPC, executes them against the local `mysqld`, and also serves the binlog to VTGate/VStream for change-data-capture and reads.

## Key components

- **vttablet daemon** — the per-tablet binary. Entrypoint: [`go/cmd/vttablet/vttablet.go`](https://github.com/vitessio/vitess/blob/main/go/cmd/vttablet/vttablet.go).
- **tabletserver** (query serving) — package [`go/vt/vttablet/tabletserver/`](https://github.com/vitessio/vitess/tree/main/go/vt/vttablet/tabletserver). Core loop in [`tabletserver.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletserver/tabletserver.go); role state machine in [`state_manager.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletserver/state_manager.go); health streaming in [`health_streamer.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletserver/health_streamer.go); binlog serving in [`binlog_dump_engine.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletserver/binlog_dump_engine.go).
- **tabletmanager** (operations) — package [`go/vt/vttablet/tabletmanager/`](https://github.com/vitessio/vitess/tree/main/go/vt/vttablet/tabletmanager). State in [`tm_state.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/tm_state.go); backup in [`rpc_backup.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/rpc_backup.go) and restore in [`restore.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/restore.go); replication-graph RPCs in [`rpc_replication.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/rpc_replication.go); schema in [`rpc_schema.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/rpc_schema.go); VReplication in [`rpc_vreplication.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/rpc_vreplication.go).
- **discovery / health check** — package [`go/vt/discovery/`](https://github.com/vitessio/vitess/tree/main/go/vt/discovery). [`healthcheck.go`](https://github.com/vitessio/vitess/blob/main/go/vt/discovery/healthcheck.go) watches tablet types and refreshes health; [`tablet_picker.go`](https://github.com/vitessio/vitess/blob/main/go/vt/discovery/tablet_picker.go) is what VTGate uses to select a target tablet.
- **topo** — package [`go/vt/topo/`](https://github.com/vitessio/vitess/tree/main/go/vt/topo). Tablet topo read/write in [`tablet.go`](https://github.com/vitessio/vitess/blob/main/go/vt/topo/tablet.go); tablet-type helpers in [`topoproto/tablet.go`](https://github.com/vitessio/vitess/blob/main/go/vt/topo/topoproto/tablet.go); the `TabletType` enum in [`go/vt/proto/topodata/topodata.pb.go`](https://github.com/vitessio/vitess/blob/main/go/vt/proto/topodata/topodata.pb.go).

## Commands & operations

Tablet operations are exposed through [vtctl](https://vitess.io/docs/24.0/concepts/vtctl/) and the [vtadmin](https://vitess.io/docs/24.0/concepts/vtadmin/) API. Representative operations:

- **Provisioning / topology** — `CreateKeyspace`, `CreateShard`, `InitShard`, `InitTablet`, `InitTabletEndsFromShard`, `RebuildTabletEnds`.
- **Role changes** — `SetTabletType` (promote/demote); ordered switchover/failover sequences are orchestrated by [VTOrc](https://vitess.io/docs/24.0/user-guides/configuration-basic/vtorc/).
- **Backup & restore** — `Backup`, `Restore` ([user guide](https://vitess.io/docs/24.0/user-guides/operating-vitess/backup-and-restore/)).
- **Replication graph** — `InitExternalReplica`, `AddExternalReplica`, `RemoveExternalReplica`.
- **Lifecycle / validation** — `DrainTablet`, `RestartTablet`, `ValidateTablets`, `GetTabletsByShardName`.

See the [VTTablet modes user guide](https://vitess.io/docs/24.0/user-guides/configuration-basic/vttablet-mysql/) for per-type configuration.
