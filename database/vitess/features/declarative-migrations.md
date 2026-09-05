---
upstream: https://github.com/vitessio/vitess
last_updated: 2026-09-05
---

# vitess — declarative schema migrations

Declarative migrations are Vitess's managed online-DDL mode for stating the *desired end state* of a table (or view) instead of the operation to perform: the user submits a full `CREATE TABLE` and Vitess makes the table match that state, whether or not it currently exists; or the user submits `DROP TABLE` and Vitess makes the table go away. Because the desired state — not the operation — is what matters, declarative DDL is idempotent: resubmitting a `CREATE TABLE` that already matches is an implicit no-op success rather than an error. The feature is a mode of the [online DDL engine](https://vitess.io/docs/24.0/user-guides/schema-changes/managed-online-schema-changes/), enabled by adding the `--declarative` flag to any DDL strategy; there is no separate command surface. [Declarative migrations](https://vitess.io/docs/24.0/user-guides/schema-changes/declarative-migrations/)

## What it is

- **Desired-state DDL.** Declarative DDL is expressed with complete `CREATE TABLE` statements (make the table the desired state) and `DROP TABLE` statements (make the table go away). `ALTER TABLE` statements are rejected in declarative mode; an "alter" is performed by resubmitting the table's full `CREATE TABLE` with the desired columns.
- **Idempotency.** Submitting the same `CREATE TABLE` twice in a row: the first succeeds, the second is a no-op and is considered implicitly successful. Likewise for two `DROP TABLE` statements on the same table: the second has nothing to do and is implicitly successful.
- **Tables and views.** `CREATE VIEW` participates: when the desired view differs from the existing one, the declarative migration is rewritten as `CREATE OR REPLACE VIEW`.
- **Strategy flag.** `--declarative` is one option among many on the `ddl_strategy` string and composes with others, e.g. `set @@ddl_strategy='vitess --declarative --allow-concurrent --postpone-completion';`.

## Usage

DDL is submitted through VTGate exactly like any other managed DDL; the mode comes from the session's `ddl_strategy`:

```sql
mysql> set @@ddl_strategy='vitess --declarative';

-- Table does not exist: runs as a regular CREATE
mysql> create table decl_table(id int primary key);

-- Table exists: Vitess diffs current vs desired state and runs the implied ALTER
mysql> create table decl_table(id int primary key, ts timestamp not null);

-- Table already matches: implicit no-op success
mysql> create table decl_table(id int primary key, ts timestamp not null);
```

Each submission is recorded in the `_vt.schema_migrations` table of the sidecar database and can be inspected with `show vitess_migrations like '<uuid>'`. For a declarative `CREATE` that ends up as an alter, the row's `migration_statement` is still the `CREATE TABLE`, but `ddl_action` is `alter` and `message` carries the actual alter clause that Vitess derived.

## How it works

- **Planning (VTGate).** `buildDDLPlans` in [`go/vt/vtgate/planbuilder/ddl.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/planbuilder/ddl.go) resolves the target keyspace and shard for the DDL statement — noting that in the `--declarative` case the table of a `CREATE TABLE` may already exist — and emits an `engine.OnlineDDL` operation that sends the statement to the shard.
- **Classification (tablet).** The shard's online DDL executor ([`go/vt/vttablet/onlineddl/executor.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/onlineddl/executor.go), `executeMigration`) processes each queued migration. Duplicates (same migration context and DDL as an already-completed migration) are marked implicitly complete. For a declarative strategy it then switches on the action:
  - `REVERT` — no special behavior; handled as a normal revert.
  - `ALTER` — fails: alters cannot run in declarative mode.
  - `DROP` — runs as a normal DROP if the table exists; marks the migration implicitly complete ("no change") if it does not.
  - `CREATE` — runs as a normal CREATE if the table does not exist; otherwise the migration is evaluated with `evaluateDeclarativeDiff`.
- **Diff evaluation.** `evaluateDeclarativeDiff` ([`executor.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/onlineddl/executor.go)) materializes the desired schema as a throwaway *comparison table* (created from the submitted `CREATE` under a GC-hold name from `schema.GenerateGCTableName`), fetches `SHOW CREATE TABLE` for both the existing table and the comparison table, and diffs them with `schemadiff.DiffCreateTablesQueries` (tables) or `schemadiff.DiffCreateViewsQueries` (views) under the `AutoIncrementApplyHigher` hint. An empty diff marks the migration implicitly complete; a non-empty diff rewrites the migration's `ddl_action` to `alter` and its SQL to the diff's canonical `ALTER` statement (or `CREATE OR REPLACE` for views), which then runs under the selected strategy.
- **Schema diff engine.** [`go/vt/schemadiff/`](https://github.com/vitessio/vitess/tree/main/go/vt/schemadiff) implements the structural comparison: the `Schema` object type ([`schema.go`](https://github.com/vitessio/vitess/blob/main/go/vt/schemadiff/schema.go), built from statements or queries), `DiffSchemas` / `DiffSchemasSQL` and `DiffTables` / `DiffCreateTablesQueries` / `DiffViews` / `DiffCreateViewsQueries` ([`diff.go`](https://github.com/vitessio/vitess/blob/main/go/vt/schemadiff/diff.go)), plus column/table semantics ([`semantics.go`](https://github.com/vitessio/vitess/blob/main/go/vt/schemadiff/semantics.go), [`column.go`](https://github.com/vitessio/vitess/blob/main/go/vt/schemadiff/column.go), [`table.go`](https://github.com/vitessio/vitess/blob/main/go/vt/schemadiff/table.go)) and capability/analysis helpers ([`capability.go`](https://github.com/vitessio/vitess/blob/main/go/vt/schemadiff/capability.go), [`analysis.go`](https://github.com/vitessio/vitess/blob/main/go/vt/schemadiff/analysis.go)).
- **State and auditing.** The `_vt` sidecar database that stores `schema_migrations` is itself created "based on the declarative schema defined for all tables" by [`go/vt/sidecardb/sidecardb.go`](https://github.com/vitessio/vitess/blob/main/go/vt/sidecardb/sidecardb.go) (`Init`); the migration table's columns (uuid, keyspace, shard, statement, strategy, options, `ddl_action`, `migration_context`, `reverted_uuid`, statuses and timestamps) are defined in [`go/vt/vttablet/onlineddl/schema.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/onlineddl/schema.go).
- **Special plans.** After the rewrite, [`go/vt/vttablet/onlineddl/analysis.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/onlineddl/analysis.go) (`analyzeSpecialAlterPlan`, `analyzeInstantDDL`) checks whether the resulting ALTER can run with a cheaper plan such as `ALGORITHM=INSTANT`; otherwise execution proceeds as a VReplication workflow ([`vrepl.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/onlineddl/vrepl.go)) or through the pt-osc/gh-ost integrations ([`ghost.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/onlineddl/ghost.go)) per the strategy.
- **Revertibility.** Declarative migrations are [revertible](https://vitess.io/docs/24.0/user-guides/schema-changes/revertible-migrations/): a declarative migration that ends up as an `ALTER` is revertible only when executed with the `vitess` strategy, and a no-op (implicitly successful) migration implies a no-op revert.

## Key components

| Component | Where | Role |
| --------- | ----- | ---- |
| `--declarative` strategy flag | [`go/vt/schema/ddl_strategy.go`](https://github.com/vitessio/vitess/blob/main/go/vt/schema/ddl_strategy.go) | parsed and held on `DDLStrategySetting`; `IsDeclarative()` gates declarative semantics |
| `OnlineDDL` | [`go/vt/schema/online_ddl.go`](https://github.com/vitessio/vitess/blob/main/go/vt/schema/online_ddl.go) | the migration object: uuid, keyspace, shard, statement, strategy, options, status |
| DDL planner | [`go/vt/vtgate/planbuilder/ddl.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/planbuilder/ddl.go) | `buildDDLPlans` resolves the target shard and emits the `engine.OnlineDDL` send |
| Online DDL executor | [`go/vt/vttablet/onlineddl/executor.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/onlineddl/executor.go) | declarative classification in `executeMigration`; comparison-table diff in `evaluateDeclarativeDiff` |
| Special alter analysis | [`go/vt/vttablet/onlineddl/analysis.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vttablet/onlineddl/analysis.go) | selects cheaper plans (e.g. `ALGORITHM=INSTANT`) for the rewritten ALTER |
| Schema diff engine | [`go/vt/schemadiff/`](https://github.com/vitessio/vitess/tree/main/go/vt/schemadiff) | `Schema` object, `DiffSchemas`, table/view diffs, diff hints |
| Sidecar database | [`go/vt/sidecardb/sidecardb.go`](https://github.com/vitessio/vitess/blob/main/go/vt/sidecardb/sidecardb.go) | the `_vt` database holding the `schema_migrations` audit table |
| Cross-tablet schema compare | [`go/vt/vtctl/schematools/diff.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/schematools/diff.go) | `CompareSchemas` (tablet-manager `GetSchema` + `tmutils.DiffSchemaToArray`), used by `CopySchemaShard` in [`go/vt/vtctl/workflow/server.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/workflow/server.go) |
| VDiff workflow | [`go/vt/vttablet/tabletmanager/vdiff/`](https://github.com/vitessio/vitess/tree/main/go/vt/vttablet/tabletmanager/vdiff) | row-level content comparison across keyspaces/shards, for [validating migrations](https://vitess.io/docs/24.0/user-guides/schema-changes/validating-migrations/) |

## Tooling notes (current tree)

Declarative DDL is the successor of the older declarative-schema workflow: the `vtctl MigrateSchemas` command that diffed a keyspace's desired versus actual schema and applied the resulting DDL no longer exists in the current tree, and the packages `go/vt/vtgate/vschema/vdiff/` and `go/vt/vtgate/vschema/vdumper/` do not exist (the diff engine now lives in [`go/vt/schemadiff/`](https://github.com/vitessio/vitess/tree/main/go/vt/schemadiff); [`go/vt/vtgate/schema/`](https://github.com/vitessio/vitess/tree/main/go/vt/vtgate/schema) holds the VTGate schema-cache tracker, not schema object types). Today the same outcomes come from `--declarative` DDL for per-table reconciliation, `vtctl ApplySchema` (wired in [`go/vt/vtctl/vtctl.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/vtctl.go)) for VSchema changes, and `schematools.CompareSchemas` for tablet-to-tablet comparison.

## Upstream docs

- [Declarative migrations](https://vitess.io/docs/24.0/user-guides/schema-changes/declarative-migrations/)
- [Managed, Online Schema Changes](https://vitess.io/docs/24.0/user-guides/schema-changes/managed-online-schema-changes/)
- [Online DDL strategies](https://vitess.io/docs/24.0/user-guides/schema-changes/ddl-strategies/)
- [ddl_strategy flags](https://vitess.io/docs/24.0/user-guides/schema-changes/ddl-strategy-flags/)
- [Applying, auditing, and controlling Online DDL](https://vitess.io/docs/24.0/user-guides/schema-changes/audit-and-control/)
- [Revertible migrations](https://vitess.io/docs/24.0/user-guides/schema-changes/revertible-migrations/)
- [Validating schema migrations using VDiff](https://vitess.io/docs/24.0/user-guides/schema-changes/validating-migrations/)
- [Online DDL (design doc)](https://vitess.io/docs/design-docs/online-ddl/)
