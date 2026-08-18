# kubernetes

Projects in the Kubernetes domain.

| Project | Description |
| ------- | ----------- |
| [cloudnative-pg](cloudnative-pg/) | Kubernetes operator for PostgreSQL that manages clustered deployments with high availability, WAL archiving and point-in-time recovery, in-place major upgrades, PgBouncer pooling, and declarative databases, roles, publications, and subscriptions. |
| [mariadb-operator](mariadb-operator/) | Kubernetes operator that provisions, runs, and maintains MariaDB clusters (standalone, Galera, replication, and multi-cluster topologies) along with their SQL objects, MaxScale proxies, and backup/restore infrastructure. |
| [traefik](traefik/) | Modern HTTP reverse proxy and L7 load balancer that on Kubernetes runs as an ingress controller, translating Ingress, its own `traefik.io` CRDs, and Gateway API resources into hot-reloaded routing configuration. |
| [trust-manager](trust-manager/) | Kubernetes operator (cert-manager family) for declaratively managing TLS trust bundles: it combines CA certificate sources into a trust bundle and keeps per-namespace target ConfigMaps/Secrets in sync. |
| [seaweedfs-operator](seaweedfs-operator/) | Kubernetes operator that deploys and maintains SeaweedFS clusters (master, volume, and filer services) with a standalone S3-compatible API, embedded IAM, CSI mounts, declarative buckets and lifecycle policies, scheduled admin scripts, and metadata backup/restore with continuous data mirroring. |
| [volsync](volsync/) | Declarative asynchronous replication of Kubernetes persistent volumes (rsync TLS/SSH, rclone, Syncthing movers) and restic-based PVC backup, driven by the `ReplicationSource`/`ReplicationDestination` CRDs. |
| [rook](rook/) | Cloud-native storage orchestrator that deploys and manages Ceph clusters in Kubernetes (RBD block, CephFS, RGW object storage with CSI, mirroring DR, multisite RGW, Prometheus monitoring). |
