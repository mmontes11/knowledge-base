---
upstream: https://github.com/backube/volsync
last_updated: 2026-08-16
---

# volsync — API reference

VolSync registers exactly two custom resource kinds under API group/version **`volsync.backube/v1alpha1`**. Both are namespaced, `v1alpha1` is the only served and storage version, and the CRDs declare no short names.

| Kind | Short name | Purpose | Upstream API docs |
| ---- | ---------- | ------- | ----------------- |
| `ReplicationSource` | — | The send side of a replication pair: names the source PVC and selects/parametrizes the data mover that moves data out of the cluster. | [CRD YAML](https://github.com/backube/volsync/blob/main/config/crd/bases/volsync.backube_replicationsources.yaml) |
| `ReplicationDestination` | — | The receive side: provisions (from `capacity`/`accessModes`/`storageClassName`) or uses a destination PVC that is filled by the mover. | [CRD YAML](https://github.com/backube/volsync/blob/main/config/crd/bases/volsync.backube_replicationdestinations.yaml) |

Go type definitions live in [`api/v1alpha1/`](https://github.com/backube/volsync/tree/main/api/v1alpha1); group `volsync.backube` and version are set in [groupversion_info.go](https://github.com/backube/volsync/blob/main/api/v1alpha1/groupversion_info.go).

Notes:

- The **mover** is selected by setting exactly one of the `spec.rclone`, `spec.restic`, `spec.rsync`, `spec.rsyncTLS`, or `spec.syncthing` blocks on each CR; each mover block exists on both kinds. See the [usage docs](https://volsync.readthedocs.io/en/stable/usage/index.html) for per-mover semantics.
- `ReplicationSource.spec.sourcePVC` names the PVC being replicated; the `ReplicationDestination` equivalent block declares the destination volume's `capacity`, `accessModes`, `storageClassName` (and `volumeMode`), or an existing PVC.
- Shared per-mover-block knobs: `copyMethod` (`Direct`, `None`, `Clone`, `Snapshot`), `moverAffinity`, `moverPodLabels`, `moverResources`, `moverSecurityContext`, `moverServiceAccount`, and `moverVolumes` (added in v0.14.0; NFS-type volumeMounts allowed since v0.15.0).
- Shared top-level `spec` fields: `paused` and `trigger` (manual sync trigger).
- Status on both kinds: `lastSyncTime`, `lastSyncDuration`, `lastSyncStartTime`, `lastManualSync`, `nextSyncTime`, `latestMoverStatus`, `conditions`, `external`; `ReplicationDestination` additionally reports `latestImage`.
- Field-level documentation is intentionally not duplicated here; follow the per-kind links above.
