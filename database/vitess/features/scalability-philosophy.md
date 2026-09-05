---
upstream: https://github.com/vitessio/vitess
last_updated: 2026-09-05
---

# vitess — scalability philosophy

Vitess treats scalability as a set of deliberate trade-offs rather than a single mechanism: break data into small MySQL instances, make data durable by *replicating* it instead of flushing it, and trade global consistency for horizontal scale — writes scale by adding shards, reads scale by adding replicas, and every shard has exactly one primary. The authoritative write-up is the [Scalability Philosophy (v24.0)](https://vitess.io/docs/24.0/overview/scalability-philosophy/) page; this doc restates each principle and ties it to the parts of the codebase that implement it.

Upstream: [Scalability Philosophy (v24.0)](https://vitess.io/docs/24.0/overview/scalability-philosophy/) · [What Is Vitess (v24.0)](https://vitess.io/docs/24.0/overview/whatisvitess/) · [Architecture (v24.0)](https://vitess.io/docs/24.0/overview/architecture/)

## Small instances

Rather than sharding just enough to fit one database on one machine, Vitess recommends breaking data into manageable chunks — roughly 250GB per MySQL server — and not shying away from running multiple instances per host. Net resource usage ends up about the same, but manageability improves sharply once instances are small:

- Fewer lock contentions to worry about.
- Replication is "a lot happier" with smaller datasets.
- Outages have smaller production impact.
- Backups and restores run faster.
- Instances can be shuffled between hosts for better machine/rack diversity, further shrinking outage impact.

The only real complication is operational: keeping track of ports and separating data directories for the multiple instances per host. This principle is what makes [sharding](https://vitess.io/docs/24.0/concepts/keyspace/) cheap to operate — each [shard](https://vitess.io/docs/24.0/concepts/shard/) is a small, self-contained MySQL replica set, and horizontal scale is simply "add another small one".

## Durability through replication

Traditional storage software treated data as durable once it was flushed to disk. On commodity hardware that is impractical and does not address disaster scenarios, so Vitess defines durability as data copied to multiple machines, and even geographic locations. Consequences:

- **Semi-sync replication is highly recommended.** With at least one replica acknowledging each write, Vitess can fail over to a new primary with no data loss when the primary goes down.
- **Prefer restore over crash recovery.** When a MySQL instance crashes, the recommendation is to create a fresh instance from a recent backup and let it catch up, rather than recovering the crashed data directory in place.
- **Disk durability can be loosened.** Because durability comes from the copies, not the flush, settings such as `sync_binlog` can be relaxed — greatly reducing disk IOPS and increasing effective write throughput.

The replication and backup machinery is built around this model:

- [`go/vt/mysqlctl/reparent.go`](https://github.com/vitessio/vitess/blob/main/go/vt/mysqlctl/reparent.go) — reparenting verifies semi-sync durability state before promoting a replica.
- [`go/vt/vttablet/tabletmanager/semisyncmonitor/monitor.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/semisyncmonitor/monitor.go) — continuously monitors semi-sync status on each tablet.
- [`go/vt/vtctl/reparentutil/`](https://github.com/vitessio/vitess/tree/main/go/vt/vtctl/reparentutil) — the reparenting decision logic, including the durability policy that selects a candidate with the most durable (semi-sync-acknowledged) GTID position.
- [`go/vt/mysqlctl/backup.go`](https://github.com/vitessio/vitess/blob/main/go/vt/mysqlctl/backup.go) and [`go/vt/mysqlctl/backupengine.go`](https://github.com/vitessio/vitess/blob/main/go/vt/mysqlctl/backupengine.go) — the backup/restore engine with pluggable backup tools (built-in, XtraBackup, MySQL Shell dump) and pluggable storage ([`filebackupstorage/`](https://github.com/vitessio/vitess/tree/main/go/vt/mysqlctl/filebackupstorage), [`gcsbackupstorage/`](https://github.com/vitessio/vitess/tree/main/go/vt/mysqlctl/gcsbackupstorage), [`s3backupstorage/`](https://github.com/vitessio/vitess/tree/main/go/vt/mysqlctl/s3backupstorage), [`cephbackupstorage/`](https://github.com/vitessio/vitess/tree/main/go/vt/mysqlctl/cephbackupstorage), [`azblobbackupstorage/`](https://github.com/vitessio/vitess/tree/main/go/vt/mysqlctl/azblobbackupstorage) under the same package) — see the [Backup and restore](https://vitess.io/docs/24.0/user-guides/operating-vitess/backup-and-restore/) guide.

## Consistency model

Before sharding or moving tables to different keyspaces, the application must tolerate:

- **Cross-shard reads may not be consistent with each other** — and since cross-shard reads are more expensive, the sharding decision should minimize them.
- **Cross-shard transactions** either run in *best-effort mode* (they can fail mid-way and result in partial commits) or in *2PC mode* (distributed atomicity), which costs roughly **50% more in write latency**.
- **Single-shard transactions remain fully ACID**, exactly as MySQL provides them.

Reads scale by offloading to replicas:

- `REPLICA` tablets serve OLTP read traffic; `RDONLY` tablets serve OLAP/batch workloads. This lets read traffic be scaled — and geographically distributed — independently of writes.
- Replica reads are eventually consistent (and lag can vary per shard). To bound staleness, **VTGate monitors replica lag and can be configured to stop serving from instances lagging beyond a threshold** (`--lag_throttle_threshold`; implemented in [`go/vt/vttablet/tabletserver/throttle/`](https://github.com/vitessio/vitess/tree/main/go/vt/vttablet/tabletserver/throttle)).
- For a true snapshot, queries must run on the primary inside a transaction; for read-after-write consistency, reading from the primary outside a transaction is sufficient.

The supported consistency levels, in increasing order of guarantee:

| Level | Guarantee |
| --- | --- |
| `REPLICA`/`RDONLY` read | Servers scale geographically; local reads are fast but can be stale depending on replica lag. |
| `PRIMARY` read | There is exactly one worldwide primary per shard; remote reads pay network latency but see up-to-date data (read-after-write); `READ_COMMITTED`. |
| `PRIMARY` transaction | `REPEATABLE_READ` with ACID writes for a single shard. |

The corresponding atomicity levels for transactions touching more than one shard:

- `SINGLE` — multi-shard transactions are disallowed.
- `MULTI` — multi-shard transactions with best-effort commit.
- `TWOPC` — multi-shard transactions with two-phase commit (VTGate's `--twopc` flag).

The implementation: VTGate opens one connection per shard for a transaction ([`go/vt/vtgate/tx_conn.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/tx_conn.go)); in `TWOPC` mode a dedicated 2PC coordinator in [`go/vt/vtgate/txresolver/tx_resolver.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/txresolver/tx_resolver.go) drives commit across shards, and each tablet participates as a 2PC participant via [`go/vt/vttablet/tabletserver/twopc.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletserver/twopc.go).

### No active-active replication

Vitess does not support active-active (multi-master) replication; each use case that setup is normally chosen for has a different answer:

- **Scalability** — active-active buys only a little extra runway, since every write must eventually be applied on every writable server. Sharding scales indefinitely instead.
- **High availability** — Vitess natively detects primary failure and fails over to a new primary within seconds of detection, which is usually sufficient.
- **Low-latency geographically distributed writes** — the one case not addressed: absorb the long-distance round-trip latency, or shard by geographic affinity so each shard's primary sits near the traffic that writes to it.

## Multi-cell

Vitess is meant to run in multiple data centers/regions/*cells*; a cell is a set of servers that are very close together and share the same regional availability. A cell typically contains a set of tablets, a VTGate pool, and the app servers using the cluster. Key properties:

- **The primary for a shard can live in any cell.** VTGate reaches cross-cell primaries through its watched-cells configuration (`--cells_to_watch`, defined in [`go/cmd/vtgate/cli/cli.go`](https://github.com/vitessio/vitess/blob/main/go/cmd/vtgate/cli/cli.go) and resolved by [`go/vt/vtgate/tabletgateway.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/tabletgateway.go)).
- **Primary-capable cells are over-provisioned.** They keep one extra replica so they can absorb a failover while retaining the same replica-serving capacity.
- **A cross-cell failover is just a local failover.** It changes traffic and latency, but if application traffic is redirected to the new cell the end state is stable.
- **Different shards can have primaries in different cells** — e.g. U.S. user records with a U.S. primary and European user records with a European primary. VTGate routes to the right place; only remote access pays the extra latency.
- **Replica-serving cells** contain only replicas; all primary access is remote. A good fit for mostly-read profiles.
- **`rdonly` tablets only where needed** — only cells running batch/OLAP jobs really need them.

Vitess uses local-cell data first and is resilient to any cell going down; most processes handle that case gracefully.

## Commands and operations

The philosophy is mostly a configuration posture, but the operations that put it into practice:

- **`Reparent` / `EmergencyReparent`** (`vtctl` / [vtadmin](https://vitess.io/docs/24.0/concepts/vtadmin/)) — execute the durability-backed failover; [VTOrc](https://vitess.io/docs/24.0/user-guides/configuration-basic/vtorc/) automates it.
- **`--cells_to_watch`** (VTGate) — make cross-cell primaries reachable for multi-cell layouts.
- **`--lag_throttle_threshold`** (VTTablet) — bound replica staleness for read traffic.
- **`--twopc`** (VTGate) — opt into 2PC cross-shard transactions.
- **Backup / restore** (`vtctl` `BackupNow`, restore on tablet init) — the "fresh instance from a recent backup" workflow.

## Key components in the source tree

Paths verified against the current `main` branch of [vitessio/vitess](https://github.com/vitessio/vitess):

- [`go/vt/vtgate/`](https://github.com/vitessio/vitess/tree/main/go/vt/vtgate) — the transparent routing layer that hides sharding from clients: [`vtgate.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vtgate.go) (gateway), [`executor.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/executor.go) (query execution), [`tabletgateway.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/tabletgateway.go) (tablet discovery across watched cells), and [`vstream_manager.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vstream_manager.go) (binlog streaming so reads/CDC scale horizontally too).
- [`go/vt/vtgate/tx_conn.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/tx_conn.go) + [`go/vt/vtgate/txresolver/tx_resolver.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/txresolver/tx_resolver.go) — multi-shard transactions; `txresolver` is the 2PC coordinator for `TWOPC` mode.
- [`go/vt/vttablet/tabletserver/twopc.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletserver/twopc.go) — the 2PC participant side on each tablet.
- [`go/vt/vttablet/tabletserver/throttle/`](https://github.com/vitessio/vitess/tree/main/go/vt/vttablet/tabletserver/throttle) — replica lag throttling used to bound read staleness.
- [`go/vt/mysqlctl/`](https://github.com/vitessio/vitess/tree/main/go/vt/mysqlctl) — the durability/backup machinery: [`reparent.go`](https://github.com/vitessio/vitess/blob/main/go/vt/mysqlctl/reparent.go), [`backup.go`](https://github.com/vitessio/vitess/blob/main/go/vt/mysqlctl/backup.go)/[`backupengine.go`](https://github.com/vitessio/vitess/blob/main/go/vt/mysqlctl/backupengine.go), plus the storage backends.
- [`go/vt/vttablet/tabletmanager/semisyncmonitor/monitor.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/semisyncmonitor/monitor.go) — semi-sync status monitoring.
- [`go/cmd/vtgate/cli/cli.go`](https://github.com/vitessio/vitess/blob/main/go/cmd/vtgate/cli/cli.go) — the `--cells_to_watch` flag that wires multi-cell deployments together.

## Related

- [Keyspace](https://vitess.io/docs/24.0/concepts/keyspace/) — the logical unit that scales by adding shards
- [Shard](https://vitess.io/docs/24.0/concepts/shard/) — the unit of horizontal scale
- [Tablet](https://vitess.io/docs/24.0/concepts/tablet/) — the small MySQL instance that serves a shard
- [Replication graph](https://vitess.io/docs/24.0/concepts/replication-graph/) — vertical scale and failover inside a shard
- [VTGate](https://vitess.io/docs/24.0/concepts/vtgate/) — hides sharding from clients and enforces the consistency model
- [VTOrc](https://vitess.io/docs/24.0/user-guides/configuration-basic/vtorc/) — automates the failovers the durability model depends on
- [Backup and restore](https://vitess.io/docs/24.0/user-guides/operating-vitess/backup-and-restore/) — the "fresh instance from a recent backup" workflow
- [Architecture](https://vitess.io/docs/24.0/overview/architecture/) — how the pieces fit together
