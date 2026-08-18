---
upstream: https://github.com/SynologyOpenSource/synology-csi
last_updated: 2026-08-17
---

# synology-csi — API reference

synology-csi is a CSI driver, not an operator: it defines no CRDs and exposes no API group of its own. Its API surface is the CSI protocol under the driver name `csi.san.synology.com` plus the standard Kubernetes objects it consumes for configuration. The tables below reflect the current upstream README at [v1.3.1](https://github.com/SynologyOpenSource/synology-csi/tree/v1.3.1); link out, don't copy.

| Surface | Kind / object | Purpose | Upstream documentation |
| ------- | ------------- | ------- | ---------------------- |
| CSI driver | `csi.san.synology.com` (controller + node DaemonSets with the standard CSI sidecars) | Provisions, attaches, and expands iSCSI LUNs, SMB/CIFS and NFS shared folders, and NVMe/TCP namespaces | [README](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md) |
| Client info secret | `v1 Secret`, default name `client-info-secret`, key `client-info.yml` (template: [`config/client-info-template.yml`](https://github.com/SynologyOpenSource/synology-csi/blob/main/config/client-info-template.yml)) | One or many DSM endpoints (host, port, credentials, TLS options) | [Creating a Secret](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md#creating-a-secret) |
| Storage class | `storage.k8s.io/v1 StorageClass`, provisioner `csi.san.synology.com` (example: [`deploy/kubernetes/<version>/storage-class.yml`](https://github.com/SynologyOpenSource/synology-csi/tree/main/deploy/kubernetes)) | Per-protocol volume options (protocol, DSM, volume, filesystem) | [Creating Storage Classes](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md#creating-storage-classes) |
| Volume snapshot class | `snapshot.storage.k8s.io/v1 VolumeSnapshotClass` (`v1beta1` below Kubernetes v1.20), driver `csi.san.synology.com` | Options for the DSM snapshot the driver creates | [Creating Volume Snapshot Classes](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md#creating-volume-snapshot-classes) |
| Node stage secret (SMB only) | `v1 Secret` referenced from StorageClass parameters `csi.storage.k8s.io/node-stage-secret-name` / `csi.storage.k8s.io/node-stage-secret-namespace` | Credentials staged on each node to access the SMB share | [Creating Storage Classes](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md#creating-storage-classes) |
| Deployment | `scripts/deploy.sh` (installs into namespace `synology-csi`; also [`deploy/kubernetes/<k8s version>/`](https://github.com/SynologyOpenSource/synology-csi/tree/main/deploy/kubernetes) manifests; community Helm chart for local development) | Installs controller, node plugin, and (full mode) the snapshotter | [Installation](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md#installation), [community Helm chart](https://github.com/christian-schlichtherle/synology-csi-chart) |

## Client info fields

Each list item under `clients:` in `client-info.yml` describes one Synology NAS endpoint; multiple NASes are supported ([README](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md#creating-a-secret)):

| Field | Required | Description |
| ----- | -------- | ----------- |
| `host` | yes | IPv4 address (or hostname) of the DSM; the StorageClass `dsm` parameter must match one of these |
| `port` | yes | DSM connection port (defaults: 5000 HTTP, 5001 HTTPS) |
| `https` | yes | `true` to use the HTTPS endpoint |
| `username` | yes | DSM account used to manage storage (DSM administrator) |
| `password` | yes | Password for that account |
| `tlsCACert` | v1.3.1, optional | PEM-encoded CA certificate for verifying a self-signed DSM certificate; the system CA pool is always included, so CA-signed certificates (e.g. Let's Encrypt) need nothing |
| `tlsServerName` | v1.3.1, optional | Server name to verify the certificate against; required when `host` is an IP and the DSM certificate only has DNS SANs |
| `insecureSkipVerify` | v1.3.1, optional | `true` disables certificate verification entirely; a warning is logged on every connection — not recommended |

## StorageClass parameters

Provisioner `csi.san.synology.com` ([parameter table](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md#creating-storage-classes), [examples](https://github.com/SynologyOpenSource/synology-csi/tree/main/deploy/example)):

| Parameter | Type | Default | Supported protocols | Description |
| --------- | ---- | ------- | ------------------- | ----------- |
| `dsm` | string | — | iSCSI, SMB, NFS | DSM endpoint that must exist in `client-info.yml` |
| `location` | string | — | iSCSI, SMB, NFS | DSM volume path (e.g. `/volume1`); if blank, the driver picks a volume with available storage |
| `fsType` | string | `ext4` | iSCSI | Filesystem used to format the LUN (`ext4` or `btrfs`); SMB volumes are always `cifs` |
| `protocol` | string | `iscsi` | iSCSI, SMB, NFS, NVMe | `iscsi` creates LUNs, `nvme` creates NVMe/TCP namespaces, `smb`/`nfs` create shared folders |
| `formatOptions` | string | — | iSCSI | Extra arguments passed to `mkfs.*` |
| `enableSpaceReclamation` | string | `false` | iSCSI | Space reclamation for thin-provisioned Btrfs LUNs; may impact performance and space display |
| `enableFuaSyncCache` | string | `false` | iSCSI | Enables FUA and Sync Cache SCSI commands for LUNs |
| `csi.storage.k8s.io/node-stage-secret-name` | string | — | SMB | Name of the node stage secret — required for SMB |
| `csi.storage.k8s.io/node-stage-secret-namespace` | string | — | SMB | Namespace of the node stage secret — required for SMB |
| `mountPermissions` | string | `0750` | NFS | Folder permissions applied with `chmod` after mount when non-zero |

NVMe/TCP is the exception in the examples: `dsm` and `location` are optional and the driver selects the NAS and NVMe subsystem automatically ([NVMe example](https://github.com/SynologyOpenSource/synology-csi/blob/main/deploy/example/storageclass-nvme.yaml)).

## VolumeSnapshotClass parameters

Driver `csi.san.synology.com` — all parameters are optional ([README](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md#creating-volume-snapshot-classes)):

| Parameter | Type | Default | Supported protocols | Description |
| --------- | ---- | ------- | ------------------- | ----------- |
| `description` | string | `""` | iSCSI | Description of the snapshot created on DSM |
| `is_locked` | string | `false` | iSCSI, SMB, NFS | Locks the snapshot on DSM |

Prerequisites: Kubernetes 1.19+ (the [image matrix](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md) lists 1.20+ for v1.3.1), DSM 7.0+ / DSM UC 3.1+ / DSME 1.0+, an initialized storage pool and volume on DSM, and — for snapshots — the [external-snapshotter](https://github.com/kubernetes-csi/external-snapshotter) CRDs plus the common snapshot controller ([Prerequisites](https://github.com/SynologyOpenSource/synology-csi/blob/main/README.md#prerequisites)).
