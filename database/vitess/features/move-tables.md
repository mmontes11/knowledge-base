---
upstream: https://github.com/vitessio/vitess
last_updated: 2026-09-04
---

# vitess — MoveTables (online resharding)

[MoveTables](https://vitess.io/docs/24.0/concepts/move-tables) is a [VReplication](https://vitess.io/docs/24.0/reference/vreplication/) workflow that moves all or a subset of tables from a source [keyspace](keyspace.md) to a target keyspace without downtime. It is the tool for *vertical* (functional) sharding — splitting a monolithic keyspace (e.g. `commerce`) into several smaller ones (e.g. `commerce` + `customer`) — and is the usual stepping stone before *horizontal* [resharding](https://vitess.io/docs/24.0/user-guides/migration/resharding/) a single table across shards. Because it runs on VReplication, the source keeps serving reads and writes for the entire copy-and-replicate phase; only a brief cutover window occurs when traffic is switched.

Authoritative references: [Moving Tables (user guide)](https://vitess.io/docs/24.0/user-guides/migration/move-tables/) · [vtctldclient MoveTables (CLI reference)](https://vitess.io/docs/24.0/reference/programs/vtctldclient/vtctldclient_movetables/)

## What it is

- **MoveTables workflow.** A named, resumable operation (e.g. `commerce2customer`) that copies selected tables from a source keyspace to a target keyspace, keeps the copy in sync via binlog replication, and then switches query traffic over. The workflow name is arbitrary and is the handle for all later actions.
- **Copy phase.** A full row-by-row copy of each selected table from the source to the target, run in parallel across the source shards.
- **Replicate phase.** After the copy, VReplication follows the source binlog so the target copy stays in sync while both keyspaces keep serving. The workflow is "in the running/replicating phase" when caught up.
- **Routing rules.** VTGate [routing rules](https://vitess.io/docs/24.0/reference/features/schema-routing-rules/) auto-created so that, before the switch, every query against a moved table — including the fully-qualified `customer.customer` form — is pinned to the *source* keyspace. After `SwitchTraffic` they are rewritten to point at the target.
- **Traffic mirroring.** `MirrorTraffic` sends a copy of real queries from the source to the target (results and errors from the target are discarded) so you can test target performance and verify routing before cutover.
- **Reverse replication.** On `SwitchTraffic`, Vitess automatically creates a reverse VReplication workflow in the *source* keyspace (named `<workflow>_reverse`) that copies writes made to the target back to the source, enabling a lossless `ReverseTraffic` rollback.
- **VDiff.** A logical, row-level diff between source and target used to confirm the copy is complete and correct before switching.
- **Complete.** Final cleanup: drops the original tables in the source keyspace and removes the routing rules and the reverse workflow, so a later misconfiguration returns "table not found" rather than stale data.

## How it works

- **Create.** `vtctldclient MoveTables create` copies the schema of the selected tables from source to target (the `CopySchemaShard` logic, [`go/vt/vtctl/vtctl.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/vtctl.go) with helpers in [`go/vt/vtctl/schematools/`](https://github.com/vitessio/vitess/tree/main/go/vt/vtctl/schematools)), sets up the forward VReplication streams (one per source shard, running on the target tablets), writes the routing rules into the topology, and — by default — starts the workflow. Until `SwitchTraffic`, routing still targets the source keyspace, so there is no ambiguity while the copies are built.
- **Copy & replicate.** The VReplication engine on each target tablet ([`go/vt/vttablet/tabletmanager/vreplication/`](https://github.com/vitessio/vitess/tree/main/go/vt/vttablet/tabletmanager/vreplication), e.g. [`controller.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/vreplication/controller.go), [`engine.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/vreplication/engine.go), [`vplayer.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/vreplication/vplayer.go), [`vcopier.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/vreplication/vcopier.go)) performs the full copy and then replays the source binlog. Progress (positions, rows copied, lag, copy states, logs) is reported by the `status`/`show` actions.
- **Validate.** `vtctldclient VDiff create` runs a per-shard, per-table logical comparison and reports `RowsCompared`, `MismatchedRows`, `ExtraRowsSource/Target`; a clean `HasMismatch: false` is the standard pre-cutover check.
- **Switch traffic.** `SwitchTraffic` rewrites the routing rules so reads and writes for the moved tables go to the target keyspace (other tables keep routing to the source). A reverse VReplication workflow is started automatically (unless `--enable-reverse-replication=false`), so post-cutover writes keep flowing back to the source and the switch can be undone with `ReverseTraffic` without data loss.
- **Mirror traffic.** `MirrorTraffic --percent N` is a VTGate-level operation: N% of queries against the moved tables are duplicated to the target. Useful to warm caches and validate target latency, at the cost of extra VTGate CPU and memory — start small and ramp up.
- **Complete.** `complete` is only allowed once all traffic is switched. It drops the source tables, deletes the routing rules, and removes the reverse workflow. The source keyspaces themselves remain; only the moved tables disappear from them.

## Key components

| Component | Where | Role |
| --------- | ----- | ---- |
| MoveTables orchestration | [`go/vt/vtctl/workflow/`](https://github.com/vitessio/vitess/tree/main/go/vt/vtctl/workflow) ([`server.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/workflow/server.go), [`switcher.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/workflow/switcher.go), [`traffic_switcher.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/workflow/traffic_switcher.go), [`vdiff.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/workflow/vdiff.go), [`workflows.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/workflow/workflows.go)) | Start/copy/verify/switch/reverse/complete lifecycle for the workflow |
| CopySchemaShard command | [`go/vt/vtctl/vtctl.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/vtctl.go) (schema helpers in [`go/vt/vtctl/schematools/`](https://github.com/vitessio/vitess/tree/main/go/vt/vtctl/schematools)) | Copies table schemas from source to target keyspace |
| Tablet VReplication RPC | [`go/vt/vttablet/tabletmanager/rpc_vreplication.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/rpc_vreplication.go) | `Start`/`Stop`/`Show` and status gRPC for VReplication streams on a tablet |
| VReplication engine | [`go/vt/vttablet/tabletmanager/vreplication/`](https://github.com/vitessio/vitess/tree/main/go/vt/vttablet/tabletmanager/vreplication) ([`controller.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/vreplication/controller.go), [`engine.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/vreplication/engine.go), [`vplayer.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/vreplication/vplayer.go), [`vcopier.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/vreplication/vcopier.go), [`vreplicator.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/vreplication/vreplicator.go)) | Full copy and binlog replication on the target tablets |
| VDiff | [`go/vt/vtctl/workflow/vdiff.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/workflow/vdiff.go) + [`go/vt/vttablet/tabletmanager/vdiff/`](https://github.com/vitessio/vitess/tree/main/go/vt/vttablet/tabletmanager/vdiff) | Logical source/target row comparison for validation |
| VTGate routing rules | topology (see [vschema](vschema.md) routing rules) | Pin moved tables to source, then re-point them to target at cutover |

## Commands & operations

- **Create (and start)** — `vtctldclient MoveTables --target-keyspace customer --workflow commerce2customer create --source-keyspace commerce --tables 'customer,corder'`. Add `--all-tables` to move everything, or `--exclude-tables` to skip specific ones.
- **Monitor** — `MoveTables ... status` (compact summary of shard streams and traffic state) and `MoveTables ... show` (full workflow detail, including per-stream positions, copy states, and logs).
- **Validate** — `vtctldclient VDiff --target-keyspace customer --workflow commerce2customer create` then `VDiff ... show last --verbose`.
- **Mirror traffic** — `MoveTables ... MirrorTraffic --percent 1.0` (ramp in small increments).
- **Switch traffic** — `MoveTables ... SwitchTraffic` (optionally `--tablet-types` to switch non-primary reads/writes separately from the primary).
- **Revert** — `MoveTables ... ReverseTraffic` to roll back the cutover using the auto-created reverse workflow.
- **Pause / resume** — `MoveTables ... stop` and `MoveTables ... start`.
- **Complete** — `MoveTables ... complete` to drop the source tables, remove routing rules, and clean up the reverse workflow.

## Key flags

| Flag | Action | Purpose |
| ---- | ------ | ------- |
| `--source-keyspace` / `--target-keyspace` / `--workflow` | create (and all) | Identify the migration and workflow |
| `--tables` / `--all-tables` / `--exclude-tables` | create | Select which source tables to move |
| `--cells` / `--all-cells` | create | Source cells to replicate from |
| `--tablet-types` | create | Source tablet types to replicate from (e.g. `PRIMARY,REPLICA,RDONLY`) |
| `--auto-start` | create | Start the workflow after creating it (default `true`) |
| `--stop-after-copy` | create | Stop after the full copy, before replicating changes |
| `--on-ddl` | create | Handling of DDL in the stream: `IGNORE` (default), `STOP`, `EXEC`, `EXEC_IGNORE` |
| `--sharded-auto-increment-handling` | create | For a sharded target, `LEAVE`, `REMOVE` (default), or `REPLACE` (with Vitess sequences, see `--global-keyspace`) |
| `--atomic-copy` | create | (EXPERIMENTAL) single copy phase for all tables; use with foreign keys |
| `--defer-secondary-keys` | create | Defer secondary index creation until after the copy (default `true`) |
| `--source-time-zone` | create | Convert `DATETIME` fields from the given zone to UTC |
| `--config-overrides` | create | Override VReplication flags as `key=value` pairs |
| `--no-routing-rules` | create | (Advanced) skip automatic routing-rule creation |
| `--percent` | mirrortraffic | Fraction (0–1) of queries to mirror to the target |
| `--tablet-types` | switchtraffic | Switch non-primary reads/writes separately |
| `--enable-reverse-replication` | switchtraffic | Auto-create the reverse workflow for rollback (default on) |
| `--tenant-id` / `--shards` / `--source-shards` | create | (EXPERIMENTAL) multi-tenant / partial MoveTables into a multi-tenant keyspace |

## Notes & caveats

- The move is online: reads and writes to the source tables are unblocked throughout copy and replication; only the `SwitchTraffic` cutover is a short, deliberate window.
- After `SwitchTraffic`, writes to the target flow back to the source via the auto-created `<workflow>_reverse` workflow until you run `complete`. Disable it with `--enable-reverse-replication=false` if you don't need a rollback path.
- `complete` errors if any traffic has not been switched; it drops the source tables and all routing rules for the workflow.
- `MirrorTraffic` raises VTGate CPU and memory and lowers performance — start with a small `--percent` and watch VTGate `/debug/querylog` and target-tablet metrics.
- Moving into a *sharded* target keyspace, MySQL `auto_increment` clauses are removed (or replaced by Vitess sequences with `--sharded-auto-increment-handling=REPLACE` + `--global-keyspace`), since sharded keyspaces must generate unique values themselves.
- Typical sequence is vertical split (MoveTables) first, then horizontal [resharding](https://vitess.io/docs/24.0/user-guides/migration/resharding/) of a single table across multiple shards.
- See also [vschema](vschema.md) (routing rules that drive cutover), [keyspaces](keyspace.md) and [shards](shard.md) (the units being reorganized), and [VStream](https://vitess.io/docs/24.0/concepts/vstream/) (binlog streaming under the hood).

## Upstream docs

- [Moving Tables — user guide](https://vitess.io/docs/24.0/user-guides/migration/move-tables/)
- [MoveTables — reference](https://vitess.io/docs/24.0/reference/vreplication/movetables/)
- [vtctldclient MoveTables — CLI reference](https://vitess.io/docs/24.0/reference/programs/vtctldclient/vtctldclient_movetables/)
- [vtctldclient MoveTables create — CLI reference](https://vitess.io/docs/24.0/reference/programs/vtctldclient/vtctldclient_movetables/vtctldclient_movetables_create/)
- [vtctldclient CopySchemaShard — CLI reference](https://vitess.io/docs/24.0/reference/programs/vtctldclient/vtctldclient_copyschemashard/)
- [VDiff — reference](https://vitess.io/docs/24.0/reference/vreplication/vdiff/)
- [VReplication — design doc](https://vitess.io/docs/design-docs/vreplication/)
- [Life of a stream — VReplication](https://vitess.io/docs/design-docs/vreplication/life-of-a-stream/)
