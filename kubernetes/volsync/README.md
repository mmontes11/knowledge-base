---
upstream: https://github.com/backube/volsync
last_updated: 2026-08-16
---

# volsync

VolSync asynchronously replicates Kubernetes persistent volumes between clusters (within one cluster or across clusters) and takes restic-based backups of persistent volumes, driven by two declarative custom resources: `ReplicationSource` and `ReplicationDestination`. It covers DR data mirroring, dev/test and edge sync from a central cluster, one-to-many distribution, and multi-way live replication.

- Upstream repository: [backube/volsync](https://github.com/backube/volsync)
- Documentation: [volsync.readthedocs.io](https://volsync.readthedocs.io/en/stable/); source in the upstream [docs/ tree](https://github.com/backube/volsync/tree/main/docs)
- License: AGPL-3.0 ([LICENSE](https://github.com/backube/volsync/blob/main/LICENSE)); Go API types under [api/v1alpha1/](https://github.com/backube/volsync/tree/main/api/v1alpha1) are dual-licensed GNU AGPL 3.0 / Apache-2.0
- API group/version: `volsync.backube/v1alpha1`

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
