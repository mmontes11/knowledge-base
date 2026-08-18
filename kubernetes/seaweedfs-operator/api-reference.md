---
upstream: https://github.com/seaweedfs/seaweedfs-operator
last_updated: 2026-08-17
---

# seaweedfs-operator — API reference

The operator registers 13 custom resource kinds under API group/version **`seaweed.seaweedfs.com/v1`**. Generated CRD manifests live in [`config/crd/bases/`](https://github.com/seaweedfs/seaweedfs-operator/tree/master/config/crd/bases); Go type definitions live in [`api/v1/`](https://github.com/seaweedfs/seaweedfs-operator/tree/master/api/v1).

| Kind | Short name | Purpose |
| ---- | ---------- | ------- |
| `Seaweed` | — | Core kind: declares a full SeaweedFS cluster — master, volume, and filer services (StatefulSets), S3 gateway, CSI driver enablement, IAM configuration, storage layout, TLS, backup/restore defaults. |
| `SeaweedBackup` | `swbk` | Point-in-time backup of a cluster's filer metadata (a `fs.meta.save` snapshot to a configured storage). |
| `SeaweedRestore` | `swr` | Restores a previously taken snapshot into a cluster (referenced by `backupName` or a remote `backupSource`). |
| `SeaweedCSIDriver` | `swcsi` | Deploys [seaweedfs-csi-driver](https://github.com/seaweedfs/seaweedfs-csi-driver) so pods can mount SeaweedFS-backed PersistentVolumes; the controller is off by default (`ENABLE_CSI_DRIVER=true` to enable). |
| `AdminScript` | `swas` | Reconciles a cron-scheduled `weed shell` admin script into a Kubernetes `CronJob` (e.g. `volume.balance`, `volume.fix.replication`). |
| `Bucket` | `swb` | Declarative S3 bucket on an operator-managed cluster: versioning, object lock, quota, placement, IAM owners/access, adopt/claim policy, `crossNamespaceAccessControl` for `clusterRef`/`seaweedRef`/`secretRef` references from other namespaces. |
| `BucketLifecyclePolicy` | `swblp` | Declarative S3 lifecycle rules for a `Bucket`: object expiration, noncurrent-version cleanup, and incomplete multipart upload expiration. |
| `ResourceReferenceGrant` | `refgrant` | Opt-in grant that a namespace accepts cross-namespace SeaweedFS references (`ResourceReferenceGrant` in the target namespace) — required for `Bucket.crossNamespaceAccessControl` and similar `seaweedRef`/`clusterRef`/`secretRef` patterns. |
| `S3Credentials` | `s3cred` | Defines an S3 IAM access-key pair; reconciles the `Secret` the operator manages or adopts for credentials. |
| `S3Identity` | `s3id` | Defines an IAM user (identity) on the SeaweedFS S3 service, tied to an `S3Credentials` for key pairing. |
| `S3OIDCProvider` | `s3oidc` | Registers an OIDC identity provider with the SeaweedFS S3/IAM service for federated, token-based access. |
| `S3Policy` | `s3pol` | Defines an IAM policy document (structured `statements` or a raw `policyDocument` YAML/JSON, exactly one of the two). |
| `S3PolicyBinding` | `s3pb` | Attaches an `S3Policy` to one or more `S3Identity` subjects. |

Notes:

- All kinds use API version `seaweed.seaweedfs.com/v1` and belong to the `categories=seaweedfs` category; short names come from the `+kubebuilder:resource` markers in [`api/v1/`](https://github.com/seaweedfs/seaweedfs-operator/tree/master/api/v1).
- The `Seaweed` kind carries no short name marker; it is the only one of the 13.
- Field-level documentation is intentionally not duplicated here; follow the per-kind CRD in `config/crd/bases/` and the Go types for the full spec.
