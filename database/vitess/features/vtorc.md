---
upstream: https://github.com/vitessio/vitess
last_updated: 2026-09-04
---

# vitess — VTOrc

VTOrc (`vtorc`) is Vitess's automated fault detection and repair tool: a standalone daemon that continuously watches the [replication graph](https://vitess.io/docs/24.0/concepts/replication-graph/) of every shard and keeps it healthy. It polls each [tablet](https://vitess.io/docs/24.0/concepts/tablet/) to read the underlying MySQL state, polls the [topology service](https://vitess.io/docs/24.0/concepts/topology-service/) to learn the desired state, detects the gap between the two, and repairs it by driving [reparenting](https://vitess.io/docs/24.0/user-guides/configuration-advanced/reparenting/) and configuration fixes on the tablets. VTOrc is the component that makes "the primary is always reachable" a property of the cluster rather than an operator chore — with it running you can skip the manual [Initialize the Shard Primary](https://vitess.io/docs/24.0/user-guides/configuration-basic/initialize-shard-primary/) step, because it elects a primary for a brand-new shard on its own.

VTOrc began as a fork of the standalone [Orchestrator](https://github.com/openark/orchestrator) project (its core carries the original Outbrain copyright) and was then custom-fitted to Vitess: it targets Vitess tablets through their RPC API and coordinates with `vtctld` and other VTOrc instances through the shared topology server rather than through a global database.

## What it is

- **Fault detector.** A background loop that samples each tablet's MySQL/replication state and each shard's desired topology, then classifies the results into named "problems" (dead primary, primary is read-only, replica not connected, etc.).
- **Self-healer.** For each recognized problem it applies a fixed repair: a graceful or emergency reparent, a primary/replica configuration fix, or a stale-primary demotion. Repairs are issued as gRPC RPCs to the relevant [vttablet](https://vitess.io/docs/24.0/concepts/tablet/) `tabletmanager`.
- **Control plane.** Exposes an HTTP (REST/JSON) API for inspecting detected problems, the detection analysis, and pausing or resuming recovery cluster-wide. [UI, API and metrics](https://vitess.io/docs/24.0/reference/vtorc/ui_api_metrics/)
- **Cluster-scoped by default.** It manages every keyspace/shard in the topology; an optional `clusters_to_watch` flag restricts it to a comma-separated list of keyspaces or `keyspace/shard` values.
- **Durability-policy aware.** Every failover it performs honors the keyspace's [durability policy](https://vitess.io/docs/24.0/user-guides/configuration-basic/durability_policy/), which determines which failure modes VTOrc is allowed to recover automatically and which are left to manual intervention.

## How it works

VTOrc runs an infinite ticker-driven loop (`ContinuousDiscovery` in [`go/vt/vtorc/logic/vtorc.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/logic/vtorc.go)) with four cadences:

- **Instance polling** (every `instance-poll-time`, default ~1s). `DiscoverInstance` samples each tablet the topology knows about — MySQL role, replication state, GTID position, health — and caches the result. A poll that exceeds `instance-poll-time` on a primary is recorded as a failed primary health check ([`go/vt/vtorc/inst/primary_health.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/inst/primary_health.go)).
- **Topology refresh** (every `topo-information-refresh-duration`, default ~30s). `refreshAllInformation` re-reads the keyspace/shard records and the tablet records from the topology server, so VTOrc always knows the *desired* state it is converging toward.
- **Recovery tick** (every `recovery-period-block-duration`). `CheckAndRecover` in [`go/vt/vtorc/logic/topology_recovery.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/logic/topology_recovery.go) walks the detected problems and, for any that are actionable, acquires a shard lock and runs the repair.
- **Caretaking** (every minute). Housekeeping: forgeting long-unseen instances, expiring the audit log and the detection/recovery history.

**Coordination.** Users are expected to run multiple VTOrc instances (VTOrc itself can fail) alongside `vtctld`, which also issues topology-changing commands. All of them coordinate through the central topology server: before any repair a VTOrc instance takes a **shard lock**, so at most one actor mutates a shard at a time. Because the polling data may be stale, a VTOrc instance re-reads the information it needs *after* acquiring the lock, before acting. This is what lets you run N VTOrc instances against the same cluster without double-recovering.

## Problems VTOrc detects and fixes

The problem taxonomy and the fixed repair for each are defined in [`go/vt/vtorc/inst/analysis_problem.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/inst/analysis_problem.go); the analysis that raises them lives in [`go/vt/vtorc/inst/analysis.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/inst/analysis.go). Representative entries:

| Problem | Detection | Fix VTOrc applies |
| --- | --- | --- |
| `ClusterHasNoPrimary` | A shard has no primary elected (e.g. a freshly created shard) | Runs `PlannedReparentShard` to elect a primary |
| `DeadPrimary` | The primary tablet is unreachable | Runs `EmergencyReparentShard` to elect a different primary |
| `IncapacitatedPrimary` | The primary consistently fails health checks but is still network-reachable | Runs `PlannedReparentShard`, falling back to `EmergencyReparentShard` |
| `PrimaryIsReadOnly`, `PrimarySemiSyncMustBeSet`, `PrimarySemiSyncMustNotBeSet` | The primary has a bad configuration (read-only, semi-sync set or not) | Corrects the configuration on the primary |
| `NotConnectedToPrimary`, `ConnectedToWrongPrimary`, `ReplicationStopped`, `ReplicaIsWritable`, `ReplicaSemiSyncMustBeSet`, `ReplicaSemiSyncMustNotBeSet` | A replica has a bad configuration or is disconnected | Repoints the replica at the primary, starts replication, and fixes its configuration |
| `StaleTopoPrimary` | A tablet still has type `PRIMARY` in the topology after a newer primary was elected (e.g. a failed topology update during emergency reparent) | Demotes the stale primary to a read-only `REPLICA`, updates its topology type, and repoints it at the current primary |

## Key components

| Component | Where | Role |
| --------- | ----- | ---- |
| `vtorc` binary | [`go/cmd/vtorc/main.go`](https://github.com/vitessio/vitess/blob/main/go/cmd/vtorc/main.go) (flags in [`go/vt/vtorc/config/`](https://github.com/vitessio/vitess/tree/main/go/vt/vtorc/config)) | The daemon entry point; owns the runtime config (poll intervals, `cell`, `clusters_to_watch`, `allow-recovery`) |
| Continuous discovery loop | [`go/vt/vtorc/logic/vtorc.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/logic/vtorc.go) (`ContinuousDiscovery`, `DiscoverInstance`, `refreshAllInformation`) | The polling engine and the tick that drives topology refresh and recovery |
| Topology recovery engine | [`go/vt/vtorc/logic/topology_recovery.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/logic/topology_recovery.go) (`CheckAndRecover`) | Failover/switchover decision and execution; acquires shard locks and drives reparenting |
| Problem analysis | [`go/vt/vtorc/inst/analysis.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/inst/analysis.go), [`analysis_problem.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/inst/analysis_problem.go) | Samples instance state and classifies it into named problems |
| Instance data access | [`go/vt/vtorc/inst/instance_dao.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/inst/instance_dao.go), [`instance.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/inst/instance.go) | Reads per-instance MySQL/replication state and the audit log |
| Shard / primary / peer health | [`go/vt/vtorc/inst/shard_dao.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/inst/shard_dao.go), [`primary_health.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/inst/primary_health.go), [`shard_peer_health.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/inst/shard_peer_health.go) | Tracks primary health and cross-observer quorum for a shard's primary |
| Keyspace & tablet discovery | [`go/vt/vtorc/logic/keyspace_shard_discovery.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/logic/keyspace_shard_discovery.go), [`tablet_discovery.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/logic/tablet_discovery.go), [`discovery_queue.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/logic/discovery_queue.go) | Watches the topology's keyspaces/shards/tablets and queues them for polling |
| Control-plane API | [`go/vt/vtorc/server/api.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/server/api.go), [`discovery.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/server/discovery.go) | Registers and serves the HTTP endpoints (see below) |
| Process health | [`go/vt/vtorc/process/health.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/process/health.go) | Reports VTOrc process health |
| Audit/history store | [`go/vt/vtorc/db/`](https://github.com/vitessio/vitess/tree/main/go/vt/vtorc/db) | Orchestrator-derived schema for the audit log and detection/recovery history |

## Control-plane API

VTOrc registers a small set of HTTP endpoints (see [`go/vt/vtorc/server/api.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtorc/server/api.go)). Most are `MONITORING`-level; the recovery pause/resume pair is `ADMIN`-level.

| Endpoint | Purpose |
| -------- | ------- |
| `/api/problems` | Detected problems, filterable by `keyspace` and `shard` |
| `/api/errant-gtids` | Instances with errant GTIDs |
| `/api/detection-analysis` | The raw detection/analysis data behind the problem list |
| `/api/database-state` | Dumps VTOrc's own database state (debug) |
| `/api/config` | Snapshot of VTOrc's current configuration |
| `/api/shard-tablet-health-quorum` | Per-shard primary quorum verdict (primary, votes, thresholds) |
| `/api/disable-global-recoveries` | Pause automatic recovery cluster-wide |
| `/api/enable-global-recoveries` | Resume automatic recovery |
| `/debug/health` | Process health (non-200 until the first discovery cycle completes) |

## Commands & operations

VTOrc is a long-running daemon, not a CLI command — its "operations" are the repairs it performs and the flags that shape its behavior.

- **Brought up with flags.** A minimal launch points VTOrc at the global topology and sets its polling cadence: `vtorc --topo-implementation etcd2 --topo-global-server-address <addr> --topo-global-root /vitess/global --cell zone1 --port 15000 --recovery-period-block-duration 10m --instance-poll-time 1s --topo-information-refresh-duration 30s`. The full flag list is on the [VTOrc reference page](https://vitess.io/docs/24.0/reference/programs/vtorc).
- **Cell awareness.** Since v24, `--cell` names the cell VTOrc runs in (optional in v24, required from v25); VTOrc validates the cell exists in the topology and fails to start if it does not.
- **Reparenting.** The core repairs are the same operations operators can run manually via [vtctl](https://vitess.io/docs/24.0/concepts/vtctl/): `PlannedReparentShard` for graceful switchover/election and `EmergencyReparentShard` for failover, plus primary/replica configuration fixes and stale-primary demotion. [Reparenting guide](https://vitess.io/docs/24.0/user-guides/configuration-advanced/reparenting/)
- **Gated by durability policy.** The keyspace's [durability policy](https://vitess.io/docs/24.0/user-guides/configuration-basic/durability_policy/) determines which failures VTOrc may auto-recover; set it deliberately, because it decides what VTOrc will and will not fix on its own.
- **On Kubernetes.** The [Vitess Operator](https://vitess.io/docs/24.0/get-started/operator/) can run VTOrc as part of a cluster; see [Running VTOrc with the Operator](https://vitess.io/docs/24.0/reference/vtorc/running_with_vtop/).

## Upstream docs

- [VTOrc (user guide)](https://vitess.io/docs/24.0/user-guides/configuration-basic/vtorc/)
- [VTOrc architecture (reference)](https://vitess.io/docs/24.0/reference/vtorc/architecture/)
- [VTOrc UI, API and metrics (reference)](https://vitess.io/docs/24.0/reference/vtorc/ui_api_metrics/)
- [VTOrc program reference (flags)](https://vitess.io/docs/24.0/reference/programs/vtorc)
- [Running VTOrc with the Vitess Operator](https://vitess.io/docs/24.0/reference/vtorc/running_with_vtop/)
- [Reparenting (user guide)](https://vitess.io/docs/24.0/user-guides/configuration-advanced/reparenting/)
- [Durability policy (user guide)](https://vitess.io/docs/24.0/user-guides/configuration-basic/durability_policy/)
- [Replication graph (concept)](https://vitess.io/docs/24.0/concepts/replication-graph/)
- [Initialize the shard primary (user guide)](https://vitess.io/docs/24.0/user-guides/configuration-basic/initialize-shard-primary/)
