---
upstream: https://github.com/cloudnative-pg/cloudnative-pg
last_updated: 2026-08-15
---

# cloudnative-pg

Kubernetes operator for [PostgreSQL](https://www.postgresql.org/): instead of provisioning or patching PostgreSQL instances by hand, you define a `Cluster` custom resource and the operator brings the database to that state and keeps it there — high availability with automatic failover, managed storage, WAL archiving with point-in-time recovery, in-place major version upgrades, connection pooling with PgBouncer, declarative databases, roles, publications, and subscriptions, and image lifecycle management through image catalogs. It is CNCF incubating, cloud-neutral, and runs PostgreSQL in any private, public, hybrid, or multi-cloud Kubernetes cluster.

- Upstream repository: [cloudnative-pg/cloudnative-pg](https://github.com/cloudnative-pg/cloudnative-pg)
- Documentation: [https://cloudnative-pg.io/docs/](https://cloudnative-pg.io/docs/); canonical generated API reference: [docs/src/cloudnative-pg.v1.md](https://github.com/cloudnative-pg/cloudnative-pg/blob/main/docs/src/cloudnative-pg.v1.md)
- License: Apache-2.0
- API group/version: `postgresql.cnpg.io/v1`

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
