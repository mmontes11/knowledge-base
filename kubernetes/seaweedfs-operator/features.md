---
upstream: https://github.com/seaweedfs/seaweedfs-operator
last_updated: 2026-09-03
---

# seaweedfs-operator — features

Key feature areas, each linked to the upstream documentation covering it. The [README](https://github.com/seaweedfs/seaweedfs-operator/blob/master/README.md) and the `*_SUPPORT.md` guides in the upstream repository are authoritative.

## Cluster lifecycle

- **Declarative SeaweedFS cluster**: a `Seaweed` resource provisions and maintains Master, Volume, and Filer services (StatefulSets) with replication, storage requests, volume-server disk count, and rolling upgrades; the operator reconciles the whole infrastructure so it scales and heals with Kubernetes. [README](https://github.com/seaweedfs/seaweedfs-operator/blob/master/README.md)
- **Topology-aware volume placement**: `spec.volumes` per-rack/per-tree topology fields plus Kubernetes node labels place volume servers for rack-aware and tree-based replication layouts (e.g. 210 replication). [TOPOLOGY_SUPPORT.md](https://github.com/seaweedfs/seaweedfs-operator/blob/master/TOPOLOGY_SUPPORT.md)
- **Master state persistence**: `spec.master.persistence` mounts a volume for the master's `-mdir` (raft log, snapshots, and the cluster's `TopologyId`) so cluster identity survives a full restart; off by default, and a shared `existingClaim` is only accepted for a single master. [README](https://github.com/seaweedfs/seaweedfs-operator/blob/master/README.md#key-fields-explained)

## S3 API and IAM

- **S3-compatible API**: two modes — the recommended **standalone S3 gateway** (`spec.s3`, a stateless Deployment fronted by `<cluster>-s3:8333`) and the **deprecated embedded filer S3** (`spec.filer.s3.enabled`); the admission webhook rejects configuring both at once. [README](https://github.com/seaweedfs/seaweedfs-operator/blob/master/README.md#s3-api)
- **Embedded IAM**: the S3 server embeds an IAM service on the same port; identities, credentials, policies, and OIDC are exposed as the `S3Identity`, `S3Credentials`, `S3OIDCProvider`, `S3Policy`, and `S3PolicyBinding` kinds. [IAM_SUPPORT.md](https://github.com/seaweedfs/seaweedfs-operator/blob/master/IAM_SUPPORT.md)
- **Declarative buckets and lifecycle**: `Bucket` (versioning, object lock, quota, placement, IAM owners/access, adopt/claim policy) and `BucketLifecyclePolicy` (expiration, noncurrent-version and multipart cleanup) reconcile into the live S3 service. [README](https://github.com/seaweedfs/seaweedfs-operator/blob/master/README.md#declarative-buckets)
- **S3 credentials rotation**: changing an adopted `S3Credentials` `Secret` rotates the identity — a new access key is registered and the superseded pair revoked (secret-key-only changes rewrite onto the same access key). [README](https://github.com/seaweedfs/seaweedfs-operator/blob/master/README.md#declarative-iam-identities-credentials-policies)

## Storage and access

- **CSI block-object storage**: a `SeaweedCSIDriver` resource optionally deploys [seaweedfs-csi-driver](https://github.com/seaweedfs/seaweedfs-csi-driver) so pods can mount SeaweedFS as `PersistentVolume`s (operator-managed cluster via `seaweedRef`, or an external filer via `filerAddress`); the controller is opt-in (`ENABLE_CSI_DRIVER=true`). [CSI_SUPPORT.md](https://github.com/seaweedfs/seaweedfs-operator/blob/master/CSI_SUPPORT.md)
- **Cross-namespace references**: `Bucket.crossNamespaceAccessControl` and `ResourceReferenceGrant` let one namespace's buckets/credentials reference a cluster in another namespace, mirroring the Gateway API ReferenceGrant pattern. [README](https://github.com/seaweedfs/seaweedfs-operator/blob/master/README.md#cross-namespace-references-resourcereferencegrant)
- **Lance Namespace API**: `spec.filer.lance` serves a Lance Namespace REST API on the filer, on by default (port 9101); `enabled: false` opts out. [CRD](https://github.com/seaweedfs/seaweedfs-operator/blob/master/config/crd/bases/seaweed.seaweedfs.com_seaweeds.yaml)

## Backup and recovery

- **Metadata snapshots** (`SeaweedBackup`, `SeaweedRestore`): one-shot `fs.meta.save` / `fs.meta.load` jobs that back up and restore the filer namespace and chunk references; on-demand or cron-scheduled via `spec.backup.schedule` with `keep` retention. [BACKUP_SUPPORT.md](https://github.com/seaweedfs/seaweedfs-operator/blob/master/BACKUP_SUPPORT.md)
- **Continuous data mirror**: each `spec.backup.dataMirror` entry runs an always-on `weed filer.backup` Deployment that replicates file content to a sink — S3/GCS/Azure/B2 (object stores) or an in-cluster PVC — for disaster recovery; restore is metadata-level and a cross-cluster data reseed is a planned follow-up. [BACKUP_SUPPORT.md](https://github.com/seaweedfs/seaweedfs-operator/blob/master/BACKUP_SUPPORT.md)

## Operations

- **Scheduled admin scripts** (`AdminScript`): a cron-scheduled `weed shell` command reconciled into a `CronJob` for maintenance tasks such as `volume.balance` and `volume.fix.replication`. [README](https://github.com/seaweedfs/seaweedfs-operator/blob/master/README.md#scheduled-admin-scripts-adminscript)
- **TLS between components**: `spec.tls` uses cert-manager to issue a self-signed chain or attach an external issuer; backup/mirror workloads inherit the same `security.toml` and certificates. [README](https://github.com/seaweedfs/seaweedfs-operator/blob/master/README.md#tls-between-components-cert-manager)
- **JWT signing (security.toml)**: `spec.securityConfig.jwtSigning` turns per-section token enforcement on for volume and filer write/read paths, with auto-generated HMAC keys kept in a `<name>-security-config` Secret that backup/mirror jobs inherit; filer write-signing (`filerWrite`) is opt-in since 1.0.36. [README](https://github.com/seaweedfs/seaweedfs-operator/blob/master/README.md#jwt-signing-securitytoml)
- **Ingress exposure**: per-component `ingress` blocks (HDFS/REST/S3/cross-cluster replication endpoints) with TLS, or the top-level `hostSuffix` for HTTP publishing. [README](https://github.com/seaweedfs/seaweedfs-operator/blob/master/README.md#exposing-the-cluster-via-ingress-tls)

## Deployment

- **Helm and FluxCD**: install via the `seaweedfs-operator` Helm chart (published under the `seaweedfs-operator-0.1.x` chart series, versioned independently of the `1.0.x` operator tags by `appVersion`) or a FluxCD `HelmRelease`; manual `kustomize`/`make deploy` paths are also documented. [README](https://github.com/seaweedfs/seaweedfs-operator/blob/master/README.md#installation)
