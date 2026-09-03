---
upstream: https://github.com/vitessio/vitess
last_updated: 2026-09-03
---

# vitess — releases

Latest 10 official releases of the `vitessio/vitess` project, newest first. Vitess maintains parallel stable release trains (for example v24.0.x and v23.0.x), so you will see interleaved series in this list. Scan the ⚠️ entries before upgrading — v24.0.0 and the v22.0.4 / v23.0.3 security releases change restore-time behavior and logging defaults.

## v24.0.2 — 2026-06-24
[Release page](https://github.com/vitessio/vitess/releases/tag/v24.0.2) · [changelog](https://github.com/vitessio/vitess/blob/main/changelog/24.0/24.0.2/changelog.md)
- Bug-fix release for the v24.0.x series; 34 merged pull requests.

## v23.0.5 — 2026-06-24
[Release page](https://github.com/vitessio/vitess/releases/tag/v23.0.5) · [changelog](https://github.com/vitessio/vitess/blob/main/changelog/23.0/23.0.5/changelog.md)
- Bug-fix release for the v23.0.x series; 35 merged pull requests.

## v24.0.1 — 2026-05-07
[Release page](https://github.com/vitessio/vitess/releases/tag/v24.0.1) · [changelog](https://github.com/vitessio/vitess/blob/main/changelog/24.0/24.0.1/changelog.md)
- Bug-fix release for the v24.0.x series; 6 merged pull requests.

## v23.0.4 — 2026-05-07
[Release page](https://github.com/vitessio/vitess/releases/tag/v23.0.4) · [changelog](https://github.com/vitessio/vitess/blob/main/changelog/23.0/23.0.4/changelog.md)
- Bug-fix release for the v23.0.x series; 44 merged pull requests.

## v24.0.0 — 2026-04-30
[Release page](https://github.com/vitessio/vitess/releases/tag/v24.0.0)
- **Window function pushdown** for sharded keyspaces when `PARTITION BY` matches a unique vindex. [MySQL compatibility](https://vitess.io/docs/24.0/reference/compatibility/mysql-compatibility/)
- **View routing rules** — route queries on views via vschema routing rules (requires `--enable-views` / `--queryserver-enable-views`). [Schema routing rules](https://vitess.io/docs/24.0/reference/features/schema-routing-rules/)
- **Tablet targeting via `USE`** — target a specific tablet by alias with `USE keyspace:shard@tablet_type|alias`. [VTGate](https://vitess.io/docs/24.0/concepts/vtgate/)
- **VTGate binlog streaming** — GTID-based binlog streaming over the MySQL protocol and gRPC (`--enable-binlog-dump`). [VStream](https://vitess.io/docs/24.0/concepts/vstream/)
- **Structured JSON logging** — JSON logs by default; `--log-level`, `--log-format=text`, and `--log-structured=false`. ⚠️ `glog` is deprecated and will be removed in v25.
- ⚠️ **Breaking (security)**: the external decompressor stored in a backup's `MANIFEST` is no longer used at restore time by default; opt back in with `--external-decompressor-use-manifest`.
- **Minor**: `--shards` flag for MoveTables/Reshard, automatic tablet retry on tablet-specific errors, `JSON_EXTRACT` dynamic path arguments, QueryThrottler observability metrics, OpenTelemetry tracing, and MySQL CLONE for replica provisioning.

## v24.0.0-rc1 — 2026-04-14
[Release page](https://github.com/vitessio/vitess/releases/tag/v24.0.0-rc1)
- Release candidate for the v24.0.0 major release.

## v23.0.3 — 2026-02-27
[Release page](https://github.com/vitessio/vitess/releases/tag/v23.0.3)
- **Security** release fixing two CVEs: **CVE-2026-27965** (external decompressor read from backup `MANIFEST`) and **CVE-2026-27969** (path traversal via backup `MANIFEST` on restore). ⚠️ The `MANIFEST`-based decompressor is ignored by default from this version; opt in with `--external-decompressor-use-manifest`. 22 merged pull requests.

## v22.0.4 — 2026-02-27
[Release page](https://github.com/vitessio/vitess/releases/tag/v22.0.4)
- **Security** release backporting the same two CVE fixes as v23.0.3 (CVE-2026-27965 and CVE-2026-27969) to the v22.0.x series. ⚠️ Same `MANIFEST`-based decompressor default change. 37 merged pull requests.

## v23.0.2 — 2026-02-10
[Release page](https://github.com/vitessio/vitess/releases/tag/v23.0.2) · [changelog](https://github.com/vitessio/vitess/blob/main/changelog/23.0/23.0.2/changelog.md)
- Bug-fix release for the v23.0.x series; 16 merged pull requests.

## v23.0.1 — 2026-02-04
[Release page](https://github.com/vitessio/vitess/releases/tag/v23.0.1) · [changelog](https://github.com/vitessio/vitess/blob/main/changelog/23.0/23.0.1/changelog.md)
- Bug-fix release for the v23.0.x series; 51 merged pull requests.
