---
upstream: https://github.com/vitessio/vitess
last_updated: 2026-09-05
---

# vitess — vstream

VStream (`vstream`) is Vitess's change-data-capture service. It exposes the row-level changes recorded in the binary logs of a keyspace's shards as a single, ordered, keyspace-wide stream of change events (`VEvent`). It has two halves: the **VStreamer** running on each VTTablet (which reads that tablet's local binlog and turns it into `VEvent`s) and the **VStream** running on VTGate (which fans the per-shard streams in, orders them by GTID position, applies table filters, and serves one merged stream to consumers). In v24 the same change data is also reachable as GTID-based binlog dumping through VTGate (`BinlogDumpGTID`, exposed over both gRPC and the MySQL wire protocol via `COM_BINLOG_DUMP_GTID`).

## What it is

- **The binlog, merged across shards.** Each VTTablet replays its MySQL binlog into typed change events; VTGate merges the per-shard streams into one keyspace-wide stream, ordered so a consumer sees changes in a consistent order even across shards. [VStream](https://vitess.io/docs/24.0/concepts/vstream/)
- **VStreamer vs VStream.** The *VStreamer* is the tablet-side component that produces events from one tablet's binlog. The *VStream* is the gateway-side component that consumes N VStreamers (one per shard) and presents a single merged stream — the same split the upstream concept doc calls out.
- **The unit of change: `VEvent`.** Every item on the stream is a `binlogdatapb.VEvent` wrapping a `RowChange` (insert/update/delete rows), a `RowEvent`/`FieldEvent` (table + column metadata), a DDL/schema change, a `Journal` (resharding / move-tables marker), or a copy-complete marker.
- **Pull-based gRPC stream, plus a MySQL-protocol mirror.** Consumers open the stream over gRPC (`VTGateService.VStream`) or, in v24, follow the raw binlog over the MySQL protocol (`COM_BINLOG_DUMP_GTID` → `BinlogDumpGTID`).
- **A primitive, not a workflow.** VStream produces the change stream only and carries no apply logic. VReplication and external CDC pipelines are built on top of it.

## How it works

- **Tablet side (VStreamer).** `newVStreamer` (`go/vt/vttablet/tabletserver/vstreamer/vstreamer.go`) opens the tablet's binlog and, in `replicate` / `parseEvents` / `parseEvent`, reads `mysql.BinlogEvent`s. For each touched table it builds a `streamerPlan` (`buildTablePlan` / `buildTableColumns`) mapping the table's physical columns to the Vitess schema (including enum/set conversions); `processRowEvent` then turns row events into `VEvent.RowChange`, and `processJournalEvent` surfaces resharding `Journal`s. The schema is refreshed via `SetVSchema` and plans are rebuilt (`rebuildPlans`) as schema changes arrive.
- **Gateway side (fan-in and ordering).** `vstreamManager.VStream` (`go/vt/vtgate/vstream_manager.go`) is the entry point: `resolveParams` expands the keyspace-wide `VGtid` into a per-shard `ShardGtid`, one `startOneStream` per shard calls `streamFromTablet` to talk to that tablet's VStreamer, and `sendEvents` / `sendAll` merge the results. Cross-shard ordering is coordinated by GTID position — `computeSkew` detects a lagging shard and `mustPause` / `alignStreams` hold the merged stream back until shards are consistent, giving consumers a total order. The request `Filter` (table include/exclude) is applied here.
- **Chunking.** Instead of buffering an entire (possibly huge) transaction in memory, `isChunkingEnabled` (added in v24, #18849) splits transactions into chunks before sending them to the client, removing an OOM risk on large multi-row transactions.
- **Schema changes.** DDL and schema-change events are delivered as `VEvent`s so consumers keep their table metadata current; v24 made this path reliable rather than best-effort (#19204).
- **GTID-based binlog dump (v24).** `vtgate.go` exposes `VTGate.BinlogDumpGTID` (gRPC), and `plugin_mysql_server.go` implements `vtgateHandler.ComBinlogDumpGTID` for `COM_BINLOG_DUMP_GTID` over the MySQL wire protocol (plain `COM_BINLOG_DUMP` is not supported — clients must use the GTID variant). The response streams raw MySQL binlog packet data (`binlogdatapb.BinlogDumpResponse`), so standard MySQL-compatible replication clients can follow a Vitess keyspace; `binlogdatapb.BinlogDumpGTIDRequest` carries the position, GTID set, and flags.
- **Resharding awareness.** While a keyspace is being resharded, `Journal` events (via `processJournalEvent` / `getJournalEvent`) and `keyspaceHasBeenResharded` let the merged stream track shards that appear or disappear mid-MoveTables.

## Key components

| Component | Where | Role |
| --------- | ----- | ---- |
| VStreamer (tablet) | `go/vt/vttablet/tabletserver/vstreamer/vstreamer.go` | Reads a tablet's binlog and emits `VEvent`s (`parseEvents`, `buildTablePlan`, `processRowEvent`, `processJournalEvent`, `rebuildPlans`) |
| VStream manager (VTGate) | `go/vt/vtgate/vstream_manager.go` | Fans in per-shard streams (`startOneStream`, `streamFromTablet`), orders them by GTID (`computeSkew`, `alignStreams`), applies the `Filter`, chunks output (`vstreamManager.VStream`) |
| VTGate entry points | `go/vt/vtgate/vtgate.go` | `VTGate.VStream` (gRPC) and `VTGate.BinlogDumpGTID` (GTID-based) methods |
| MySQL-protocol GTID dump | `go/vt/vtgate/plugin_mysql_server.go` | `vtgateHandler.ComBinlogDumpGTID` serves `COM_BINLOG_DUMP_GTID`; `COM_BINLOG_DUMP` is unsupported |
| gRPC service | `go/vt/vtgate/grpcvtgateservice/` | Serves the Vitess gRPC `VTGateService` (`VStream`, `BinlogDumpGTID`) |
| Client interface | `go/vt/vtgate/vtgateconn/vtgateconn.go` | `VTGateConn.VStream` / `VTGateConn.BinlogDumpGTID` — what consumers call to open a stream |
| Wire types | `proto/binlogdata.proto`, `go/vt/proto/binlogdata/` | `VEvent`, `RowChange`, `RowEvent`, `FieldEvent`, `VGtid`, `ShardGtid`, `Journal`, `Filter`, `VStreamOptions`, `BinlogDumpGTIDRequest` / `Response` |
| Consumer example: VReplication | `go/vt/vttablet/tabletmanager/vreplication/vreplicator.go` | `newVReplicator` takes a `VStreamerClient` source and applies `VEvent`s to a target |
| Testing | `go/vt/vtgate/binlog_dump_grpc_test.go`, `executor_vstream_test.go` | End-to-end coverage of VStream and BinlogDumpGTID |

## Supported operations

- **Subscribe to a keyspace's change stream** — open `VStream` with a start `VGtid` (or "new"), a tablet type, a table `Filter`, and `VStreamOptions` (e.g. chunking, send-schema, copy).
- **Filter tables** — include/exclude specific tables with `binlogdatapb.Filter` so consumers only receive the changes they care about.
- **Resume from a position** — start at a GTID position; VTGate resolves it into per-shard `ShardGtid`s and replays from there.
- **Follow during resharding** — `Journal` events let the consumer keep moving while MoveTables moves shards around.
- **Receive schema changes** — DDL / schema-change `VEvent`s keep consumer-side table metadata current.
- **GTID-based binlog dump (v24)** — `BinlogDumpGTID` over gRPC or `COM_BINLOG_DUMP_GTID` over the MySQL protocol, for MySQL-compatible replication clients.
- **Coordinate copy / backfill** — copy-complete markers (`LastPKEvent` / `TableLastPK`) and VStream-copy behavior (copy only when the VGTID requires it, #18938).

## Comparing with VReplication

- **VStream is the data plane.** It produces an ordered, keyspace-wide `VEvent` stream and nothing more — no applying, no workflow. Use it to feed an external CDC pipeline, a stream processor, or any custom consumer.
- **VReplication (VReplicator) is a stateful consumer.** `newVReplicator` (`go/vt/vttablet/tabletmanager/vreplication/vreplicator.go`) takes a `VStreamerClient` (i.e. a VStream) as its `sourceVStreamer` and applies the `VEvent`s to a destination MySQL / Vitess, adding schema sync, an initial row copy, a copy → resync → copy-complete state machine, row/column filters, and throttling. VStream is the foundation VReplication is built on. [VReplication design doc](https://vitess.io/docs/design-docs/vreplication/)
- **Rule of thumb:** want just the change stream? Use VStream. Want Vitess to replicate a keyspace or table to another store with a managed workflow? Use VReplication.

## Upstream docs

- [VStream (concept)](https://vitess.io/docs/24.0/concepts/vstream/)
- [VReplication (design doc)](https://vitess.io/docs/design-docs/vreplication/)
- [Keyspace (concept)](https://vitess.io/docs/24.0/concepts/keyspace/)
- [VTGate (concept)](https://vitess.io/docs/24.0/concepts/vtgate/)
- [Tablet (concept)](https://vitess.io/docs/24.0/concepts/tablet/)
- [MoveTables (concept)](https://vitess.io/docs/24.0/concepts/move-tables/)
