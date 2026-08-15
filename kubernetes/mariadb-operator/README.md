---
upstream: https://github.com/mariadb-operator/mariadb-operator
last_updated: 2026-08-15
---

# mariadb-operator

Kubernetes operator for [MariaDB](https://mariadb.org/): instead of provisioning or patching database instances and SQL objects by hand, you define them declaratively as custom resources and the operator brings the cluster to that state and keeps it there. It runs MariaDB in standalone, Galera multi-master, replication (primary/replica), and multi-cluster topologies, and manages updates, failover, TLS, and backups, plus the databases, users, grants, SQL jobs, and MaxScale proxies that live on top of them.

- Upstream repository: https://github.com/mariadb-operator/mariadb-operator
- Documentation: [docs/](https://github.com/mariadb-operator/mariadb-operator/tree/main/docs) in the upstream repository; canonical API reference: [docs/api_reference.md](https://github.com/mariadb-operator/mariadb-operator/blob/main/docs/api_reference.md)
- License: MIT
- API group/version: `k8s.mariadb.com/v1alpha1`

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
