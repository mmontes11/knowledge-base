---
upstream: https://github.com/vitessio/vitess
last_updated: 2026-09-04
---

# vitess — query-rewriting

Vitess works hard to create the illusion of a user having a single connection to a single database. In reality, a single query may interact with multiple databases (shards) and multiple connections to the same database. Query rewriting is the set of mechanisms VTGate uses to make that transparent: it decomposes a statement into fragments, rewrites each fragment so it can execute standalone on a shard, pushes the fragments down to the right VTTablets in parallel, and reassembles the results at VTGate so the client sees exactly what it would have seen against a plain MySQL server.

## What it is

- **Query splitting.** A complicated query with a cross-shard join might first fetch information from a tablet keeping vindex lookup tables, use that information to query two different shards for more data, and subsequently join the incoming results into a single result. The queries that MySQL gets are often just pieces of the original query, and the final result is assembled at the VTGate level. [Query rewriting](https://vitess.io/docs/24.0/concepts/query-rewriting/)
- **Session-state emulation.** User-defined variables and many MySQL system variables cannot live on shared shard connections, so VTGate keeps that state itself and rewrites it out of every query before the query leaves VTGate. [Query rewriting](https://vitess.io/docs/24.0/concepts/query-rewriting/)
- **Connection pooling.** When a VTTablet talks to MySQL to execute a query on behalf of a user, it does not use a dedicated connection per user; the underlying connection is shared between users. It is therefore not safe to store any state in the session: you cannot be sure a query will keep executing on the same connection, or that the connection will not be reused by other users later.
- **Trivial query evaluation.** Statements that need no shard data at all — such as `SELECT @my_user_variable` — are fully executed on VTGate and never sent to MySQL.

## How it works

- **Parse.** Statements are parsed by Vitess's SQL parser in `go/vt/sqlparser/` (`parser.go`, grammar in `sql.y`) into an AST (`ast.go`). The AST is built to be rewritten safely: copy-on-rewrite semantics (`ast_copy_on_rewrite.go`), generic traversal (`ast_visit.go`), structural rewrites (`ast_rewrite.go`, `rewriter_api.go`), plus predicate normalization in `normalizer.go` and `predicate_rewriting.go`.
- **Plan and split.** The planner (`Planner` in `go/vt/vtgate/planbuilder/planner.go`, driven by `builder.go`) walks the AST against the keyspace's VSchema. Statement-specific planning lives in `select.go`, `insert.go`, `update.go`, `delete.go`, `route.go`, `vindex_func.go`, `single_sharded_shortcut.go`, and `other_read.go`, with shared rewrite helpers in `rewrite.go`. The planner decides what is pushed down and what runs at VTGate: statements pinned to a single shard are routed through essentially unchanged, while statements that cannot be pinned are split — only the fragments against individual tables (or against vindex lookup tables) are sent to shards, and joins, aggregations, limits, and filters are executed at VTGate as operators in `go/vt/vtgate/engine/` (e.g. `join.go`, `hash_join.go`, `aggregations.go`). Sharding-key values are turned into keyspace IDs by the vindexes in `go/vt/vtgate/vindexes/` (e.g. `hash.go`, `binary.go`, `multicol.go` for direct vindexes, `lookup.go`, `lookup_internal.go`, `consistent_lookup.go`, `cfc.go` for lookup vindexes), which also decides which lookup fragments are needed before the main query can be routed.
- **User-defined variables.** `SET @my_user_variable = 'foobar'` is not sent to MySQL; VTGate evaluates it and keeps the value in per-session state (the executor's session in `go/vt/vtgate/executor.go`). When a later query references the variable, VTGate rewrites the reference to its literal value before the fragment leaves VTGate: `WHERE col = @my_user_variable` arrives at MySQL as `WHERE col = 'foobar'`, so no session state is needed to evaluate it. Trivial reads like `SELECT @my_user_variable` are fully executed on VTGate.
- **Server system variables.** `SET` statements for MySQL system variables are handled by VTGate in one of five ways, planned in `planbuilder/set.go` and `planbuilder/system_variables.go`:
  - *NoOp* — silently ignored because the setting does not make much sense in a sharded setting (e.g. `big_tables`, `debug`, `wait_timeout`).
  - *Check and fail if not already set* — the variable is not changeable, but a `SET` that assigns it its current value is allowed (e.g. `character_set_client`, `collation_server`, `lock_wait_timeout`, `max_allowed_packet`).
  - *Not supported* — attempting to change it always results in an error (e.g. `sql_log_off`, `max_sp_recursion_depth`, `ndb_use_transactions`).
  - *Vitess aware* — the setting changes Vitess' behaviour and is not sent down to MySQL (e.g. `autocommit`, `transaction_mode`, `ddl_strategy`, `foreign_key_checks`, `query_timeout`, `read_after_write_timeout`).
  - *Reserved connection* — the variable may be set, but the connection becomes dedicated to that user instead of pooled (e.g. `sql_mode`, `time_zone`, `optimizer_switch`). Vitess now supports connection pooling for many of these settings when the session value matches the global value, removing the need for a reserved connection: see the [reserved connections reference](https://vitess.io/docs/24.0/reference/query-serving/reserved-conn/).
  - The complete per-variable table is maintained in the upstream [Query rewriting reference](https://vitess.io/docs/24.0/concepts/query-rewriting/#reference).
- **Special functions.** Functions that depend on session or connection state are rewritten or evaluated at VTGate instead of being delegated to MySQL: `DATABASE()` (and its synonym `SCHEMA()`) is rewritten to the literal keyspace name, since the keyspace name and the underlying database names do not have to be equal; `ROW_COUNT()` and `FOUND_ROWS()` are rewritten to the literal row count, because the last query may have run on a different connection; `LAST_INSERT_ID()` is likewise rewritten before hitting MySQL. These are planned in `planbuilder/other_read.go`.
- **Emulated version.** VTGate makes sure `@@version` includes both the emulated MySQL version and the Vitess version, such as `8.4.6-Vitess`. The emulated version is changeable with the VTGate flag `--mysql-server-version`.
- **Diagnostics.** `VExplain` (`planbuilder/vexplain.go`) shows the plan for a statement, including the rewritten per-shard fragments that would be pushed down, so the rewriting is inspectable without running the query. [Execution plans](https://vitess.io/docs/24.0/concepts/execution-plans/)

## Key components

| Component | Where | Role |
| --------- | ----- | ---- |
| SQL parser | `go/vt/sqlparser/` (`parser.go`, `sql.y`, `ast.go`) | Parses statements into the AST that rewriting operates on |
| AST rewriting | `go/vt/sqlparser/` (`ast_rewrite.go`, `ast_copy_on_rewrite.go`, `ast_visit.go`, `normalizer.go`, `predicate_rewriting.go`) | Safe structural mutation and normalization of the parsed AST |
| Planner | `go/vt/vtgate/planbuilder/` (`planner.go`, `builder.go`, `rewrite.go`, `select.go`, `insert.go`, `update.go`, `delete.go`) | Decides what is pushed down to shards versus executed at VTGate; rewrites fragments per shard |
| Vindexes | `go/vt/vtgate/vindexes/` (`vindex.go`, `hash.go`, `binary.go`, `multicol.go`, `lookup.go`, `consistent_lookup.go`, `cfc.go`) | Map sharding-key values to keyspace IDs; drive routing and lookup fragments |
| VTGate executor | `go/vt/vtgate/executor.go` | Holds per-session state (user variables, system variables) used to rewrite outgoing queries |
| Engine operators | `go/vt/vtgate/engine/` | Execute the non-pushed-down parts of a plan at VTGate (scatter/merge, joins, aggregation, limits) |
| VExplain | `go/vt/vtgate/planbuilder/vexplain.go` | Show the plan, including the rewritten per-shard subqueries |

## Supported operations

- **SELECT over sharded tables.** Single-shard (routed), scattered (all shards, merged at VTGate), and cross-shard queries (split into fragments plus VTGate-side joins/aggregations), executed in parallel where possible. [Execution plans](https://vitess.io/docs/24.0/concepts/execution-plans/)
- **DML over sharded tables.** `INSERT`, `UPDATE`, `DELETE` rewritten per shard (for example, multi-column vindex values are computed and added to `INSERT` fragments) and scattered to the owning shards.
- **User-defined variables.** `SET @var` and reads of `@var` are emulated at VTGate; references are rewritten to literals before reaching MySQL, and trivial variable queries execute entirely at VTGate.
- **System variables.** `SET`/`SELECT @@var` handled by one of the five strategies (NoOp, check-and-fail, not supported, Vitess aware, reserved connection); see the full per-variable table in the upstream reference.
- **Special functions.** `DATABASE()`/`SCHEMA()`, `ROW_COUNT()`/`FOUND_ROWS()`, and `LAST_INSERT_ID()` are rewritten or evaluated at VTGate so they are safe across pooled connections.
- **Cross-keyspace and cross-shard queries.** Rewritten into per-keyspace and per-shard fragments, executed in parallel, and merged at VTGate. [vschema](https://vitess.io/docs/24.0/concepts/vschema/)

## Upstream docs

- [Query rewriting (concept)](https://vitess.io/docs/24.0/concepts/query-rewriting/)
- [Execution plans (concept)](https://vitess.io/docs/24.0/concepts/execution-plans/)
- [vschema (concept)](https://vitess.io/docs/24.0/concepts/vschema/)
- [VTGate (concept)](https://vitess.io/docs/24.0/concepts/vtgate/)
- [Reserved connections (reference)](https://vitess.io/docs/24.0/reference/query-serving/reserved-conn/)
- [Query serving (design doc)](https://vitess.io/docs/design-docs/query-serving/)
