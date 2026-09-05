---
upstream: https://github.com/vitessio/vitess
last_updated: 2026-09-04
---

# vitess — Replication graph

The **replication graph** is the set of primary/replica relationships inside a [shard](https://vitess.io/docs/24.0/concepts/shard/): which [tablet](https://vitess.io/docs/24.0/concepts/tablet/) is the primary, and which tablets replicate from it. During a failover, the replication graph is what lets Vitess re-point every existing replica at a newly designated primary so replication can continue. It is maintained in two places at once — the [topology service](https://vitess.io/docs/24.0/concepts/topology-service/) (the *desired* graph) and each `mysqld`'s own replication configuration (the *physical* graph) — and Vitess's job is to keep the two in sync and to swap the primary when the current one dies.

Authoritative references: [Replication graph (concept)](https://vitess.io/docs/24.0/concepts/replication-graph/) · [Reparenting (user guide)](https://vitess.io/docs/24.0/user-guides/configuration-advanced/reparenting/)

## What it is

- **The shard's replica set, as a graph.** Each shard has exactly one `primary` tablet and zero or more `replica` and `rdonly` tablets, all replicating from the primary via MySQL replication. The tablet type (`topodatapb.TabletType`, package `topodata`) fixes a tablet's role in the graph.
- **A two-sided state.** The topology server holds the intended graph — the shard record's `PrimaryAlias` plus each tablet record's type — while each `mysqld` holds the realized one (its `CHANGE REPLICATION SOURCE` state, replication threads, GTID position, semi-sync settings). Drift between the two is what Vitess's tools detect and repair.
- **The unit of failover.** "Failover" in Vitess means changing the graph: demote the old primary, promote a replica, and re-point every other replica at the new primary. The operations that do this are called **reparents** because they change a shard's primary or a tablet's replication source.

Vitess requires MySQL **GTIDs** for its replication operations — replication is initialized from GTIDs and reparenting depends on the GTID stream being correct. Semi-synchronous replication is not required but is strongly encouraged, and larger deployments typically use it; the keyspace's [durability policy](https://vitess.io/docs/24.0/user-guides/configuration-basic/durability_policy/) wires it into promotion and failover decisions.

## Roles in the graph

| Type | Enum value | Role in the graph |
| --- | --- | --- |
| **primary** | `PRIMARY` (1) | The MySQL primary for the shard; exactly one. Serves writes. |
| **replica** | `REPLICA` (2) | A MySQL replica eligible for promotion to primary; conventionally serves live read traffic. |
| **rdonly** | `RDONLY` (3) | A MySQL replica that *cannot* be promoted to primary; reserved for background work (backups, dumps, heavy analytics). |

`replica` and `rdonly` differ only in promotion eligibility, which is exactly what failover candidate selection and the durability policy key on. `master` is a deprecated alias for `primary`. Transient off-graph states (`BACKUP`, `RESTORE`, `DRAINED`) exist while a tablet is being backed up, restored, or drained; see [Tablets](tablet.md).

## How it works

**Graph bookkeeping.** The topology service is the source of truth for the *desired* graph. A reparenting operation takes the **shard lock** ([`go/vt/topo/shard_lock.go`](https://github.com/vitessio/vitess/blob/main/go/vt/topo/shard_lock.go)) so that at most one actor mutates a shard at a time, then atomically updates the shard record's `PrimaryAlias` and the tablets' types.

**Per-tablet replication state.** The tabletmanager owns the physical side. All replication-graph primitives — `InitPrimary`, `InitReplica`, `DemotePrimary`, `UndoDemotePrimary`, `PromoteReplica`, `SetReplicationSource`, `StartReplication`, `StopReplication`, `WaitForPosition`, `GetReplicas` — live in [`go/vt/vttablet/tabletmanager/rpc_replication.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/rpc_replication.go). Two mechanisms make reparents recoverable:

- **`reparent_journal` table.** Every reparent writes a marker row into this Vitess-internal MySQL table (via `PopulateReparentJournal` / `ReadReparentJournalInfo`); a crashed mid-operation tablet can read the journal to learn what happened and finish (or undo) the reparent. It also doubles as a reparenting history log.
- **Semi-sync monitoring.** [`go/vt/vttablet/tabletmanager/semisyncmonitor/monitor.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/semisyncmonitor/monitor.go) watches semi-sync state so the replication configuration is corrected when semi-sync is (or is not) required by the keyspace's durability policy.

**Serving and health.** Each tablet's `tabletserver` runs a role state machine ([`go/vt/vttablet/tabletserver/state_manager.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletserver/state_manager.go): `StateNotConnected` / `StateNotServing` / `StateServing`) that stops or serves queries as the tablet is demoted, promoted, or drained. The tablet continuously streams its live role and replication lag ([`go/vt/vttablet/tabletserver/health_streamer.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletserver/health_streamer.go)). The discovery layer ([`go/vt/discovery/healthcheck.go`](https://github.com/vitessio/vitess/blob/main/go/vt/discovery/healthcheck.go), [`tablet_health.go`](https://github.com/vitessio/vitess/blob/main/go/vt/discovery/tablet_health.go)) subscribes to these streams and computes the set of healthy tablets per (keyspace, shard, tablet type); VTGate's tablet picker ([`go/vt/discovery/tablet_picker.go`](https://github.com/vitessio/vitess/blob/main/go/vt/discovery/tablet_picker.go)) consumes that to route writes to the primary and reads to replicas. So a graph change is only *visible to clients* once the new primary's health events propagate.

## Failover behavior

Reparents are the operations that change the graph; they come in two active flavors plus one external hook. Both active ones lock the shard record, cannot run in parallel for a given shard, and both insert rows into `reparent_journal`.

### PlannedReparentShard — switchover / election

Used for a healthy shard: changing the primary during maintenance, or electing the first primary when a shard is brought up.

*Change of primary (switchover):* the current primary is put read-only and its query service shut down (VTGate can [buffer](https://vitess.io/docs/24.0/reference/features/vtgate-buffering/) queries during this window, and a grace period lets in-flight transactions finish before they are killed); the primary's executed GTID position is captured; the primary-elect waits for that position; the primary-elect is promoted; then, on the new primary, a marker row is inserted and the shard's `PrimaryAlias` is updated, while *in parallel* each replica (including the old primary) is re-pointed at the new primary and made to wait for the marker row to replicate.

*Initial promotion:* a fresh shard has no primary yet — the designated tablet is simply promoted and every other tablet is re-pointed at it.

The primary-elect, if not specified, is chosen by the keyspace's [durability policy](https://vitess.io/docs/24.0/user-guides/configuration-basic/durability_policy/).

### EmergencyReparentShard — failover

Used when the current primary is unavailable. It does not rely on the old primary at all:

1. Determine the replication position of all replica tablets and find the most advanced one.
2. Choose the primary-elect — the user's choice, or one selected by the durability policy.
3. Wait for the primary-elect to catch up to the most advanced replica (or verify it already is).
4. Promote the primary-elect to primary and apply the configuration changes its new state requires.
5. Insert a marker row into `reparent_journal`, update the shard's `PrimaryAlias`, and in parallel re-point every replica *except* the old primary at the new primary, waiting for the marker row to replicate.

The implementation ([`go/vt/vtctl/reparentutil/emergency_reparenter.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/reparentutil/emergency_reparenter.go)) additionally handles the messy cases: errant GTIDs (`findErrantGTIDs`), an intermediate source that must be promoted first (`promoteIntermediateSource`), stopped replicas that must be restarted once re-pointed, and relay-log reconciliation before the new primary is trusted.

### External reparenting

If another tool (e.g. an external MySQL failover manager) changes the primary, Vitess just needs to be told: `TabletExternallyReparented` updates the topology service, the replication graph, and the serving graph to match — it marks the tablet `PRIMARY`, asynchronously updates the shard record, and demotes any other tablet still claiming `PRIMARY`. Deployments that rely on external reparents can disable Vitess's own active reparents with the vtctld `--disable-active-reparents` flag.

### Orphans and repair

A replica that was down during a reparent comes back pointing at the old (now-demoted) primary. VTOrc detects and repairs this automatically; manually, `ReparentTablet` re-points the tablet at the current primary and `StartReplication` restarts it. [VTOrc](vtorc.md) is the continuous watcher of the replication graph — it polls each tablet's MySQL state and the desired topology, classifies the gap into named problems (dead primary, replica not connected, stale primary in the topology, bad semi-sync config, ...), and triggers the reparent or configuration fix itself, honoring the durability policy.

## Key components

| Component | Where | Role |
| --------- | ----- | ---- |
| Topology service (shard record, tablet records, shard lock) | [`go/vt/topo/`](https://github.com/vitessio/vitess/tree/main/go/vt/topo) (shard lock in [`shard_lock.go`](https://github.com/vitessio/vitess/blob/main/go/vt/topo/shard_lock.go)) | Stores the desired graph; the shard lock serializes reparents |
| Tablet replication primitives | [`go/vt/vttablet/tabletmanager/rpc_replication.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/rpc_replication.go) | `InitPrimary`/`InitReplica`/`DemotePrimary`/`PromoteReplica`/`SetReplicationSource`, `WaitForPosition`, and the `reparent_journal` marker table |
| Semi-sync monitor | [`go/vt/vttablet/tabletmanager/semisyncmonitor/monitor.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletmanager/semisyncmonitor/monitor.go) | Tracks and corrects semi-sync configuration on primary and replicas |
| Serving state machine | [`go/vt/vttablet/tabletserver/state_manager.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletserver/state_manager.go) | Stops/serves queries per role during demotion, promotion, draining |
| Health streaming | [`go/vt/vttablet/tabletserver/health_streamer.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/tabletserver/health_streamer.go) | Publishes the tablet's live role, replication lag, and health |
| Discovery / health check | [`go/vt/discovery/healthcheck.go`](https://github.com/vitessio/vitess/blob/main/go/vt/discovery/healthcheck.go), [`tablet_health.go`](https://github.com/vitessio/vitess/blob/main/go/vt/discovery/tablet_health.go) | Aggregates health streams into the set of healthy tablets per (keyspace, shard, type) |
| Tablet picker | [`go/vt/discovery/tablet_picker.go`](https://github.com/vitessio/vitess/blob/main/go/vt/discovery/tablet_picker.go) | What VTGate uses to pick the primary for writes and replicas for reads |
| Planned reparenter | [`go/vt/vtctl/reparentutil/planned_reparenter.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/reparentutil/planned_reparenter.go) | `PlannedReparentShard`: switchover, initial promotion, partial-promotion recovery |
| Emergency reparenter | [`go/vt/vtctl/reparentutil/emergency_reparenter.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/reparentutil/emergency_reparenter.go) | `EmergencyReparentShard`: candidate selection, errant GTID detection, relay-log reconciliation |
| Durability policy | [`go/vt/vtctl/reparentutil/durability_funcs.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/reparentutil/durability_funcs.go), [`policy/durability.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/reparentutil/policy/durability.go) | Promotion eligibility and semi-sync acknowledgements that steer primary-elect choice |
| Reparent CLI commands | [`go/vt/vtctl/reparent.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/reparent.go) | `InitShardPrimary`, `PlannedReparentShard`, `EmergencyReparentShard`, `ReparentTablet`, `TabletExternallyReparented` |
| Graph watcher / auto-repair | [`go/vt/vtorc/logic/topology_recovery.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/logic/topology_recovery.go), [`go/vt/vtorc/inst/analysis_problem.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/inst/analysis_problem.go) | VTOrc's problem detection and the automatic reparent it triggers |

## Commands & operations

Reparenting operations are exposed through [vtctl](https://vitess.io/docs/24.0/concepts/vtctl/)/vtctldclient and the [vtadmin](https://vitess.io/docs/24.0/concepts/vtadmin/) API:

- **`InitShardPrimary`** — elect the first primary of a new shard (a planned reparent with no current primary).
- **`PlannedReparentShard`** — switchover a healthy shard to a chosen (or policy-chosen) primary; optionally zero-downtime with [VTGate query buffering](https://vitess.io/docs/24.0/reference/features/vtgate-buffering/).
- **`EmergencyReparentShard`** — failover when the current primary is unavailable; the most-advanced replica wins.
- **`ReparentTablet`** — re-point a single (orphaned) tablet at the current shard primary.
- **`TabletExternallyReparented`** — sync Vitess's graph after an external tool has changed the primary.
- **`SetTabletType`** — promote/demote a tablet's graph role (full ordered sequences are orchestrated by [VTOrc](https://vitess.io/docs/24.0/user-guides/configuration-basic/vtorc/)).
- **`StartReplication` / `StopReplication`** — start or stop a tablet's replication against the graph.
- **`InitExternalReplica` / `AddExternalReplica` / `RemoveExternalReplica`** — manage replication from tablets outside Vitess.
- **Durability policy** — set per keyspace via `CreateKeyspace` or `SetKeyspaceDurabilityPolicy`; the policies `semi_sync`, `semi_sync_with_rdonly_ack`, `none` (default), `cross_cell`, and `cross_cell_with_rdonly_ack` decide which tablet types may be promoted and how many semi-sync acknowledgements are required.
- **Metrics** — `planned_reparent_counts`, `emergency_reparent_counts`, and `reparent_shard_operation_timings` are exposed on `/debug/vars` and `/metrics` of the VTOrc and vtctld that run reparents.

## Upstream docs

- [Replication graph (concept)](https://vitess.io/docs/24.0/concepts/replication-graph/)
- [Tablet (concept)](https://vitess.io/docs/24.0/concepts/tablet/)
- [Shard (concept)](https://vitess.io/docs/24.0/concepts/shard/)
- [Topology service (concept)](https://vitess.io/docs/24.0/concepts/topology-service/)
- [Reparenting (user guide)](https://vitess.io/docs/24.0/user-guides/configuration-advanced/reparenting/)
- [Durability policy (user guide)](https://vitess.io/docs/24.0/user-guides/configuration-basic/durability_policy/)
- [Initialize the shard primary (user guide)](https://vitess.io/docs/24.0/user-guides/configuration-basic/initialize-shard-primary/)
- [VTOrc (user guide)](https://vitess.io/docs/24.0/user-guides/configuration-basic/vtorc/)
- [VTGate buffering (reference)](https://vitess.io/docs/24.0/reference/features/vtgate-buffering/)
