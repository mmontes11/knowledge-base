---
upstream: https://github.com/backube/volsync
last_updated: 2026-08-16
---

# volsync — features

Significant feature areas, each with a link to the matching upstream doc. The [documentation site](https://volsync.readthedocs.io/en/stable/) (source: [docs/ tree](https://github.com/backube/volsync/tree/main/docs)) is authoritative.

## Replication methods ("movers")

One mover is chosen per `ReplicationSource`/`ReplicationDestination` pair by setting the matching `spec` block:

- **Rclone** (`spec.rclone`): multi-way `1:many` distribution, one source to many destinations. [docs](https://volsync.readthedocs.io/en/stable/usage/rclone/index.html)
- **Restic** (`spec.restic`): scheduled backup and restore of PVC data to object storage or a remote server. [docs](https://volsync.readthedocs.io/en/stable/usage/restic/index.html)
- **Rsync over TLS** (`spec.rsyncTLS`): `1:1` replication; the recommended mover for new deployments. [docs](https://volsync.readthedocs.io/en/stable/usage/rsync-tls/index.html)
- **Rsync over SSH** (`spec.rsync`): the original `1:1` mover; upstream recommends the TLS-based implementation for new deployments. [docs](https://volsync.readthedocs.io/en/stable/usage/rsync/index.html)
- **Syncthing** (`spec.syncthing`): multi-way `many:many` live replication with eventual consistency. [docs](https://volsync.readthedocs.io/en/stable/usage/syncthing/index.html)

## Scheduling and triggers

- **Triggers**: schedule-driven, continuous, or manual syncing via `spec.trigger` on the CR: [triggers doc](https://volsync.readthedocs.io/en/stable/usage/triggers.html)
- **PVC copy triggers**: annotation-driven, one-shot copies of existing data: [docs](https://volsync.readthedocs.io/en/stable/usage/pvccopytriggers.html)

## Volume populator

- Using a `ReplicationDestination` as a PVC's `dataSourceRef` to populate the PVC when it is created: [docs](https://volsync.readthedocs.io/en/stable/usage/volume-populator/index.html)

## Security model

- **Permission model**: mover pods run in the *user's* namespace and use least-privilege service accounts: [docs](https://volsync.readthedocs.io/en/stable/usage/permissionmodel.html)
- **Mover service account**: choosing the ServiceAccount (and associated secrets) the mover pod runs as: [docs](https://volsync.readthedocs.io/en/stable/usage/moverserviceaccount.html)

## Mover configuration

- **Resource requirements**: sizing and resource considerations per mover: [docs](https://volsync.readthedocs.io/en/stable/usage/resourcerequirements.html)
- **`moverVolumes`** (added v0.14.0): mount secrets or PVCs (NFS-type volumeMounts allowed since v0.15.0) into the mover pod: [docs](https://volsync.readthedocs.io/en/stable/usage/movervolumes.html)
- **CLI (`volcli`)**: trigger syncs and inspect replication from the command line, installable as a Krew plugin (macOS supported since v0.16.0): [docs](https://volsync.readthedocs.io/en/stable/usage/cli/index.html)

## Metrics and operations

- **Metrics**: Prometheus endpoint with authenticated access (auth served by the controller-runtime builtin since v0.15.0): [docs](https://volsync.readthedocs.io/en/stable/usage/metrics/index.html)

## Deployment

- **Helm**: `helm repo add backube https://backube.github.io/helm-charts/` then `helm install volsync backube/volsync -n volsync-system --create-namespace`: [installation docs](https://volsync.readthedocs.io/en/stable/installation/index.html)
- **Development builds** from source: [docs](https://volsync.readthedocs.io/en/stable/installation/development.html)
