---
upstream: https://github.com/cloudnative-pg/cloudnative-pg
last_updated: 2026-10-01
---

# cloudnative-pg — releases

Latest 10 official releases, newest first. Check the ⚠️ entries before upgrading.

## 1.30.1 — 2026-09-23

[Release page](https://github.com/cloudnative-pg/cloudnative-pg/releases/tag/v1.30.1)

- ⚠️ **Security fixes**: `CVE-2026-84304` (gRPC-Go updated to v1.83.1, fixing heap memory exhaustion via HTTP/2 DATA-frame fragmentation; [GHSA-vp52-pcj8-j9qc](https://github.com/advisories/GHSA-vp52-pcj8-j9qc)) and a webhook validation bypass that accepted absolute extension paths with embedded `..` traversal, escaping the extension's mount point once resolved at runtime. ([#11394](https://github.com/cloudnative-pg/cloudnative-pg/pull/11394), [#11210](https://github.com/cloudnative-pg/cloudnative-pg/pull/11210))
- **New options**: `restoreAdditionalCommandArgs` in `.barmanObjectStore.data` (forwards custom flags to `barman-cloud-restore` on in-tree recoveries), `auth_user` in `spec.pgbouncer.parameters` (overrides the user used for PgBouncer's `auth_query`), `ssl_ecdh_curve` allowed in `.spec.postgresql.parameters`, and a `--dry-run` option for the `cnpg` plugin `backup` command. ([#11278](https://github.com/cloudnative-pg/cloudnative-pg/pull/11278), [#10899](https://github.com/cloudnative-pg/cloudnative-pg/pull/10899), [#11308](https://github.com/cloudnative-pg/cloudnative-pg/pull/11308), [#11233](https://github.com/cloudnative-pg/cloudnative-pg/pull/11233))
- **Failover fixes and image updates**: an unreachable old primary is now demoted immediately instead of waiting on checkpoint/fast-shutdown attempts that can never complete, and fenced instances no longer stall failover or Pod recreation (they are recognized and skipped); default PostgreSQL images updated to 18.6-system-trixie and the operator base image moved from Debian 12 to Debian 13 (trixie). ([#11347](https://github.com/cloudnative-pg/cloudnative-pg/pull/11347), [#11274](https://github.com/cloudnative-pg/cloudnative-pg/pull/11274), [#11257](https://github.com/cloudnative-pg/cloudnative-pg/pull/11257), [#11193](https://github.com/cloudnative-pg/cloudnative-pg/pull/11193))

## 1.29.3 — 2026-09-23

[Release page](https://github.com/cloudnative-pg/cloudnative-pg/releases/tag/v1.29.3)

- ⚠️ **Final release of the `1.29.x` series**; the minor is no longer supported.
- ⚠️ **Security fixes** (same as 1.30.1): `CVE-2026-84304` (gRPC-Go updated to v1.83.1) and the extension `..`-traversal webhook validation bypass. ([#11394](https://github.com/cloudnative-pg/cloudnative-pg/pull/11394), [#11210](https://github.com/cloudnative-pg/cloudnative-pg/pull/11210))
- Backports the 1.30.1 option additions (`restoreAdditionalCommandArgs`, `auth_user`, `ssl_ecdh_curve`, `backup --dry-run`), the failover fixes, and the image updates (default PostgreSQL 18.6-system-trixie; operator base image on Debian 13). ([#11278](https://github.com/cloudnative-pg/cloudnative-pg/pull/11278), [#10899](https://github.com/cloudnative-pg/cloudnative-pg/pull/10899), [#11347](https://github.com/cloudnative-pg/cloudnative-pg/pull/11347), [#11274](https://github.com/cloudnative-pg/cloudnative-pg/pull/11274), [#11257](https://github.com/cloudnative-pg/cloudnative-pg/pull/11257), [#11193](https://github.com/cloudnative-pg/cloudnative-pg/pull/11193))

## 1.30.0 — 2026-06-29

[Release page](https://github.com/cloudnative-pg/cloudnative-pg/releases/tag/v1.30.0)

- ⚠️ **Native (in-tree) Barman Cloud: deprecation schedule changed** — removal is now scheduled for `1.31.0` (previously announced as `1.30.0`); migrate to the [Barman Cloud plugin](https://github.com/cloudnative-pg/plugin-barman-cloud). ([#11083](https://github.com/cloudnative-pg/cloudnative-pg/pull/11083))
- ⚠️ **Immutable `cluster` references**: the `cluster` field on `Database`, `Pooler`, `Publication`, `Subscription`, and `ScheduledBackup` is now immutable; re-pointing an existing object at a different cluster is rejected by a CEL rule. ([#10743](https://github.com/cloudnative-pg/cloudnative-pg/pull/10743))
- **`DatabaseRole` kind and primary `Lease`**: roles can now be managed as standalone `DatabaseRole` objects (independent lifecycle and status, optional TLS client credentials), and primary promotion is serialized through a cluster-named Kubernetes `Lease` controlled with `.spec.primaryLease`. ([#6155](https://github.com/cloudnative-pg/cloudnative-pg/pull/6155), [#10896](https://github.com/cloudnative-pg/cloudnative-pg/pull/10896), [#10627](https://github.com/cloudnative-pg/cloudnative-pg/pull/10627))

## 1.29.2 — 2026-06-29

[Release page](https://github.com/cloudnative-pg/cloudnative-pg/releases/tag/v1.29.2)

- **Security fixes**: `CVE-2026-55769` (pins `search_path` on operator-issued connections) and `CVE-2026-55765` (operator-side SCRAM-SHA-256 encoding of role passwords). ([#10774](https://github.com/cloudnative-pg/cloudnative-pg/pull/10774), [#10724](https://github.com/cloudnative-pg/cloudnative-pg/pull/10724))
- ⚠️ **Native Barman Cloud deprecation: schedule changed** — removal is now scheduled for `1.31.0`; `cluster` references on managed resources also became immutable (CEL). ([#11083](https://github.com/cloudnative-pg/cloudnative-pg/pull/11083), [#10743](https://github.com/cloudnative-pg/cloudnative-pg/pull/10743))
- **Kubernetes 1.36 support** added; default PostgreSQL images updated to 18.4. ([#10900](https://github.com/cloudnative-pg/cloudnative-pg/pull/10900), [#10719](https://github.com/cloudnative-pg/cloudnative-pg/pull/10719))

## 1.28.4 — 2026-06-29

[Release page](https://github.com/cloudnative-pg/cloudnative-pg/releases/tag/v1.28.4)

- ⚠️ **Final release of the `1.28.x` series**; the minor is no longer supported.
- Fixed **`CVE-2026-55769`** and **`CVE-2026-55765`** (same hardening as 1.29.2); ⚠️ native Barman Cloud removal moved to `1.31.0`; `cluster` references on managed resources are now immutable. ([#10774](https://github.com/cloudnative-pg/cloudnative-pg/pull/10774), [#10724](https://github.com/cloudnative-pg/cloudnative-pg/pull/10724), [#11083](https://github.com/cloudnative-pg/cloudnative-pg/pull/11083), [#10743](https://github.com/cloudnative-pg/cloudnative-pg/pull/10743))
- **Kubernetes 1.35 support** added (unit tests run on 1.36); default PostgreSQL images updated to 18.4. ([#10900](https://github.com/cloudnative-pg/cloudnative-pg/pull/10900), [#10719](https://github.com/cloudnative-pg/cloudnative-pg/pull/10719))

## 1.29.1 — 2026-05-08

[Release page](https://github.com/cloudnative-pg/cloudnative-pg/releases/tag/v1.29.1)

- ⚠️ **`CVE-2026-44477` (metrics exporter privilege)**: the exporter no longer authenticates as the `postgres` superuser; it uses a dedicated `cnpg_metrics_exporter` role with `pg_monitor` only. Custom monitoring queries may need explicit `GRANT`s, and source primaries must be upgraded **before** their replica clusters. ([GHSA-423p-g724-fr39](https://github.com/cloudnative-pg/cloudnative-pg/security/advisories/GHSA-423p-g724-fr39))
- **Failover hardening**: a former primary that returns during a failover is labeled `unhealthy` and cordoned from service traffic; failover is triggered on the Pod `Ready` condition when a node becomes unreachable, with a guard against spurious failovers. ([#10409](https://github.com/cloudnative-pg/cloudnative-pg/pull/10409), [#10448](https://github.com/cloudnative-pg/cloudnative-pg/pull/10448), [#10445](https://github.com/cloudnative-pg/cloudnative-pg/pull/10445))
- All `pg_catalog` references in default monitoring queries are now schema-qualified; SBOM and SLSA provenance are discoverable through the OCI 1.1 Referrers specification.

## 1.28.3 — 2026-05-08

[Release page](https://github.com/cloudnative-pg/cloudnative-pg/releases/tag/v1.28.3)

- ⚠️ **`CVE-2026-44477` backport** (metrics exporter privilege): the exporter no longer runs as the `postgres` superuser and instead uses the dedicated `cnpg_metrics_exporter` role — custom monitoring queries may need explicit `GRANT`s, and source primaries must be upgraded first. ([GHSA-423p-g724-fr39](https://github.com/cloudnative-pg/cloudnative-pg/security/advisories/GHSA-423p-g724-fr39))
- **Failover hardening backport**: unhealthy labeling of former primaries, `Ready`-condition-based failover triggers for unreachable nodes, and spurious-failover guards. ([#10409](https://github.com/cloudnative-pg/cloudnative-pg/pull/10409), [#10448](https://github.com/cloudnative-pg/cloudnative-pg/pull/10448), [#10445](https://github.com/cloudnative-pg/cloudnative-pg/pull/10445))
- Schema-qualified `pg_catalog` references in default monitoring queries; SBOM and SLSA provenance published via OCI 1.1 Referrers.

## 1.29.0 — 2026-03-31

[Release page](https://github.com/cloudnative-pg/cloudnative-pg/releases/tag/v1.29.0)

- ⚠️ **Native (in-tree) Barman Cloud: deprecation schedule changed** — removal moved from `1.29.0` to `1.30.0`; migrate to the [Barman Cloud plugin](https://github.com/cloudnative-pg/plugin-barman-cloud). ([#10167](https://github.com/cloudnative-pg/cloudnative-pg/pull/10167))
- **Dynamic `pg_hba` rules**: declarative `podSelectorRefs` let the operator manage host-based authentication rules from pod label selectors (ephemeral IPs resolved automatically); `ImageCatalog` support extended to PostgreSQL extensions. ([#10148](https://github.com/cloudnative-pg/cloudnative-pg/pull/10148), [#9781](https://github.com/cloudnative-pg/cloudnative-pg/pull/9781))
- Optional **`serviceAccountName`** on `Cluster` and `Pooler` for shared cloud IAM identities (AWS IRSA, GCP/Azure Workload Identity); `Pooler` gained fine-grained TLS version and cipher-suite control. ([#9287](https://github.com/cloudnative-pg/cloudnative-pg/pull/9287), [#9571](https://github.com/cloudnative-pg/cloudnative-pg/pull/9571))

## 1.28.2 — 2026-03-31

[Release page](https://github.com/cloudnative-pg/cloudnative-pg/releases/tag/v1.28.2)

- ⚠️ **Native Barman Cloud deprecation: schedule changed** — removal moved from `1.29.0` to `1.30.0`. ([#10167](https://github.com/cloudnative-pg/cloudnative-pg/pull/10167))
- **Security / supply chain**: prevents role passwords leaking into logs, adds SLSA provenance for binaries and container images, and generates SBOMs in the release pipeline. ([#9950](https://github.com/cloudnative-pg/cloudnative-pg/pull/9950), [#10048](https://github.com/cloudnative-pg/cloudnative-pg/pull/10048), [#10074](https://github.com/cloudnative-pg/cloudnative-pg/pull/10074))
- Fixed an operator-upgrade **deadlock affecting clusters with synchronous replication** and replicas stuck because of a deleted `VolumeSnapshot`; default PostgreSQL images updated to 18.3. ([#10342](https://github.com/cloudnative-pg/cloudnative-pg/pull/10342), [#10192](https://github.com/cloudnative-pg/cloudnative-pg/pull/10192), [#10090](https://github.com/cloudnative-pg/cloudnative-pg/pull/10090))

## 1.27.4 — 2026-03-31

[Release page](https://github.com/cloudnative-pg/cloudnative-pg/releases/tag/v1.27.4)

- ⚠️ **Final release of the `1.27.x` series**; the minor is no longer supported.
- ⚠️ **Native Barman Cloud deprecation: schedule changed** — removal moved from `1.29.0` to `1.30.0`. ([#10167](https://github.com/cloudnative-pg/cloudnative-pg/pull/10167))
- Backports the 1.28.2 security and supply-chain hardening (password-leak prevention, SLSA provenance, SBOMs).
