---
upstream: https://github.com/vitessio/vitess
last_updated: 2026-09-05
---

# vitess — Kubernetes operator

The Vitess Operator is a Kubernetes operator that deploys a full Vitess cluster from a single declarative configuration and keeps it there: it provisions the etcd lockserver, `vtctld`, `vtadmin`, per-cell `vtgate`, per-shard `vttablet` pools, `vtorc`, and the backup machinery, and then reconciles that desired state on every change — rolling upgrades, resharding, failover support, and scheduled backups included. It is developed by PlanetScale under the Apache 2.0 license and released as a standalone repository, [planetscale/vitess-operator](https://github.com/planetscale/vitess-operator) (older Vitess versions bundled the operator inside `vitessio/vitess` under `go/vt/operator/` with a `vitess.io/v1beta2` API group; that code no longer exists in the Vitess tree, so all source references below are to the operator repository).

The [Vitess Operator for Kubernetes](https://vitess.io/docs/24.0/get-started/operator/) getting-started guide walks through installing it on Minikube and bringing up an example cluster.

## What it is

- **Declarative cluster API.** The entire cluster is described by custom resources in the `planetscale.com/v2` API group. `VitessCluster` is the only user-editable resource: all user-accessible configuration (images, backup, cells, keyspaces) lives in its spec, and the operator materializes it as child objects that should be treated as read-only views of cluster status (`pkg/apis/planetscale/v2/vitesscluster_types.go`).
- **Full component lifecycle.** For each cluster it deploys a global lockserver (etcd by default), `vtctld` dashboards, `vtadmin` UIs, one `vtgate` Deployment per cell, `vttablet` pods with PVCs per shard/cell, `vtorc` per shard, and `vtbackup` storage subcontrollers.
- **Rolling updates with gates.** Changing `spec.images` (or any spec field) triggers a coordinated rollout across cells, keyspaces, and shards; rollout progress is tracked via `planetscale.com/desired-state-hash` annotations and can be gated per-object with `rollout.planetscale.com/scheduled`, `.../released`, and `.../cascade` annotations (`pkg/operator/rollout/rollout.go`, `pkg/operator/desiredstatehash/builder.go`).
- **Online resharding.** A keyspace's `partitionings` field can hold multiple shard layouts at once; adding a new partitioning brings up the destination shards alongside the sources, the operator drives the vreplication MoveTables workflow, and the old partitioning is removed afterwards. [Move tables](https://vitess.io/docs/24.0/user-guides/migration/move-tables/)
- **Backups built in.** `spec.backup` selects an engine (`builtin`, `xtrabackup`, or `mysqlshell`) and storage locations (local volume, GCS, S3); the operator takes an initial placeholder backup per shard and can run cron-scheduled backups via `VitessBackupSchedule` templates. New tablets in a populated shard are provisioned by restoring from these backups. [Backup and restore](https://vitess.io/docs/24.0/user-guides/operating-vitess/backup-and-restore/)
- **Failure-domain aware.** Cells map to Kubernetes zones, so keyspaces can be deployed across multiple availability zones in one cluster; `vtgate` supports HPA autoscaling per cell (`AutoscalerSpec` in `pkg/apis/planetscale/v2/vitesscell_types.go`).

## Custom resources

All CRDs are in the `planetscale.com` group, version `v2`; manifests live in `deploy/crds/` of the operator repository.

| CRD | Role |
| --- | --- |
| `VitessCluster` | Top-level, user-editable cluster configuration (short name `vt`): images, backup, global lockserver, dashboard, vtadmin, cell and keyspace templates |
| `VitessCell` | A failure-domain (zone) in which keyspaces can be deployed; owns the cell-local lockserver and the cell's `vtgate` Deployment |
| `VitessKeyspace` | A logical database; its `partitionings` define the key-ranges of the shards that make up the keyspace |
| `VitessShard` | One key-range of a keyspace; its `tabletPools` (one per cell/type) define the `vttablet` deployments, plus replication and `vtorc` settings |
| `EtcdLockserver` | The etcd stateful set backing the global (or cell-local) topology store |
| `VitessBackup` | One backup (or restore) operation for a shard |
| `VitessBackupStorage` | A backup storage location; runs a `vtbackup` subcontroller pod that manages the backup files there |
| `VitessBackupSchedule` | A cron schedule that periodically creates `VitessBackup` jobs |

Tablet pool types are `replica` (primary-eligible), `rdonly` (primary-ineligible, OLAP), and `externalmaster`/`externalreplica`/`externalrdonly` (tablets pointed at an external MySQL endpoint) — see `VitessShardTabletPool` in `pkg/apis/planetscale/v2/vitessshard_types.go`.

## How it works

The operator is a controller-runtime manager (entry point `cmd/manager/main.go`, wiring in `pkg/controller/controller.go` and `pkg/operator/controllermanager/`) with one controller per CRD, reconciling top-down:

- **VitessCluster controller** (`pkg/controller/vitesscluster/`). The root reconcile: creates or updates the `VitessCell` and `VitessKeyspace` children from the spec templates (`reconcile_cells.go`, `reconcile_keyspaces.go`), the global lockserver (`reconcile_etcd.go`), the `vtctld` dashboards (`reconcile_vtctld.go`), `vtadmin` (`reconcile_vtadmin.go`), and the backup storage and schedules (`reconcile_backup_storage.go`, `reconcile_backup_schedule.go`), then registers Vitess components in the topology (`reconcile_topo.go`).
- **VitessCell controller** (`pkg/controller/vitesscell/`). Per-cell: cell-local etcd when requested (`reconcile_etcd.go`), cell registration in the global topology (`reconcile_topo.go`), the `vtgate` Deployment with auth/TLS/autoscaling (`reconcile_vtgate.go`), and cell-aware keyspace status.
- **VitessKeyspace controller** (`pkg/controller/vitesskeyspace/`). Creates the keyspace in the Vitess topology (`reconcile_keyspace_information.go`), creates one `VitessShard` per shard in each partitioning (`reconcile_shards.go`), and during resharding drives the vreplication workflow between old and new partitionings (`reconcile_resharding.go`); the keyspace status reports workflow state and copy progress (`ReshardingStatus`).
- **VitessShard controller** (`pkg/controller/vitessshard/`). Per shard: the `vtorc` Deployment (`reconcile_vtorc.go`), `vttablet` pods and PVCs for each tablet pool (`reconcile_tablets.go`, `reconcile_disk.go`), rolling updates gated on tablet health (`reconcile_rollout.go`), initial or periodic backup jobs (`reconcile_backup_job.go`), and shard/replication topology bookkeeping (`reconcile_topo.go`).
- **VitessShardReplication controller** (`pkg/controller/vitessshardreplication/`). The replication-facing half of shard work, talking to `vttablet` through the tabletmanager gRPC client and `wrangler`: elects the initial primary of a new or restored shard (`init_shard_primary.go`, `init_restored_shard.go`) and drains the old shards when a resharding completes (`reconcile_drain.go`).
- **EtcdLockserver controller** (`pkg/controller/etcdlockserver/`). The etcd members as a stateful workload plus headless/external services and PodDisruptionBudgets.
- **Backup controllers** (`pkg/controller/vitessbackupstorage/`, `pkg/controller/vitessbackupschedule/`). The storage controller runs a long-lived `vtbackup` subcontroller pod per location that lists, verifies, and prunes backup files; the schedule controller expands cron templates into concrete `VitessBackup` resources (`schedule_generator.go`).

Shared machinery in `pkg/operator/` does the heavy lifting: a declarative object reconciler that diffs and creates/updates/deletes the desired Kubernetes objects (`reconciler/`), content and desired-state hashes that drive rollout gating (`contenthash/`, `desiredstatehash/`), the rollout annotation logic (`rollout/`), a topology connection pool over the lockserver gRPC API (`toposerver/`), and per-component desired-state builders (`etcd/`, plus `vtgate/`, `vttablet/` (including the `vtbackup` pod builder, `vtbackup_pod.go`), `vtadmin/`, `vtctld/`, and `vtorc/`). Failover itself is not done by the operator — each shard's `vtorc` performs [reparenting](https://vitess.io/docs/24.0/user-guides/configuration-advanced/reparenting/) automatically; see [Running VTOrc with the Operator](https://vitess.io/docs/24.0/reference/vtorc/running_with_vtop/).

## Commands & operations

The operator is a long-running Deployment, not a CLI — operations are `kubectl` actions on the CRDs. The [examples/operator](https://github.com/vitessio/vitess/tree/main/examples/operator) directory in the Vitess repo contains the operator manifest and a phased example (initial cluster, added shards, MoveTables, scheduled backups).

- **Install the operator.** `kubectl apply -f operator.yaml` from `examples/operator` (equivalently `deploy/operator.yaml` in the operator repo); supported Vitess/Kubernetes version pairings are in the [compatibility matrix](https://github.com/planetscale/vitess-operator#compatibility).
- **Deploy a cluster.** `kubectl apply -f 101_initial_cluster.yaml` — a single `VitessCluster` with a cell, a keyspace, and its shards; verify with `kubectl get pods -n <ns>`.
- **Scale.** Edit the `VitessCluster`: add cells, add keyspaces, or change a shard's `tabletPools[].replicas`; the controllers converge.
- **Upgrade Vitess.** Change the `images` section of the `VitessCluster`; the rollout machinery rolls each component in order, gated by the `rollout.planetscale.com/*` annotations.
- **Reshard.** Add a second `partitioning` to the keyspace; the operator creates the destination shards, runs the MoveTables vreplication workflow, and reports progress in the keyspace status; removing the old partitioning tears down the old shards. [Resharding](https://vitess.io/docs/24.0/user-guides/configuration-advanced/resharding/)
- **Back up and restore.** `spec.backup` with engine and locations; scheduled backups via `spec.backup.schedules` (cron); a single `VitessBackup` object takes or restores one backup. [Backup and restore](https://vitess.io/docs/24.0/user-guides/operating-vitess/backup-and-restore/)
- **Port-forward for local access.** [`examples/operator/pf.sh`](https://github.com/vitessio/vitess/blob/main/examples/operator/pf.sh) forwards `vtgate` (MySQL), `vtctld`, and `vtadmin` (UI on port 14000) from the cluster to localhost.

## Upstream docs

- [Vitess Operator for Kubernetes (getting started)](https://vitess.io/docs/24.0/get-started/operator/)
- [Vitess Operator repository (README, compatibility, build)](https://github.com/planetscale/vitess-operator)
- [Operator overview](https://github.com/planetscale/vitess-operator/blob/main/docs/readme.md)
- [VitessCluster CRD API reference](https://github.com/planetscale/vitess-operator/blob/main/docs/api.md)
- [Getting started on AWS](https://github.com/planetscale/vitess-operator/blob/main/docs/aws-quickstart.md) / [on GCP](https://github.com/planetscale/vitess-operator/blob/main/docs/gcp-quickstart.md)
- [Running VTOrc with the Vitess Operator](https://vitess.io/docs/24.0/reference/vtorc/running_with_vtop/)
- [Operator examples in the Vitess repo](https://github.com/vitessio/vitess/tree/main/examples/operator)
- [Move tables (user guide)](https://vitess.io/docs/24.0/user-guides/migration/move-tables/)
- [Backup and restore (user guide)](https://vitess.io/docs/24.0/user-guides/operating-vitess/backup-and-restore/)
