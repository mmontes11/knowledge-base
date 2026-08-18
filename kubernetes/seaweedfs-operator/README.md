---
upstream: https://github.com/seaweedfs/seaweedfs-operator
last_updated: 2026-08-17
---

# seaweedfs-operator

Kubernetes operator for [SeaweedFS](https://github.com/seaweedfs/seaweedfs): instead of deploying and tuning the large-scale distributed object storage system by hand, you define the desired cluster as `Seaweed` custom resources and the operator deploys and maintains the Master, Volume, and Filer services — with standalone S3-compatible API, built-in IAM, and CSI block-object storage — along with S3 buckets, IAM identities and policies, scheduled admin scripts, and backup/restore.

- Upstream repository: https://github.com/seaweedfs/seaweedfs-operator
- Documentation: [README](https://github.com/seaweedfs/seaweedfs-operator/blob/master/README.md) and feature guides in the upstream repository: [BACKUP_SUPPORT.md](https://github.com/seaweedfs/seaweedfs-operator/blob/master/BACKUP_SUPPORT.md), [CSI_SUPPORT.md](https://github.com/seaweedfs/seaweedfs-operator/blob/master/CSI_SUPPORT.md), [IAM_SUPPORT.md](https://github.com/seaweedfs/seaweedfs-operator/blob/master/IAM_SUPPORT.md), [TOPOLOGY_SUPPORT.md](https://github.com/seaweedfs/seaweedfs-operator/blob/master/TOPOLOGY_SUPPORT.md); config examples in [`config/samples/`](https://github.com/seaweedfs/seaweedfs-operator/tree/master/config/samples)
- License: Apache-2.0
- API group/version: `seaweed.seaweedfs.com/v1`

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
