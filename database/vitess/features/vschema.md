---
upstream: https://github.com/vitessio/vitess
last_updated: 2026-09-04
---

# vitess — vschema / routing rules

The VSchema is Vitess's declarative routing layer: per-keyspace metadata that describes how data is organized (tables, vindexes, views) plus a set of routing rules that map unqualified names to concrete keyspaces, shards, and tablet types. It is the input VTGate's planner uses to decide which shard(s) serve a given statement, and it is also consulted during resharding. The VSchema lives in the topology service as a `SrvVSchema` proto; VTGate materializes it into a denormalized in-memory object that is rebuilt on every change.

## What it is

- **Keyspace schema.** For each keyspace, the VSchema records whether it is sharded, the vindexes defined in it, and — for sharded keyspaces — the table definitions with their column vindexes. In a sharded keyspace every non-reference, non-pinned table must have at least one column vindex. [VSchema (concept)](https://vitess.io/docs/24.0/concepts/vschema/)
- **Vindexes.** Functions that map a set of column values to a keyspace ID (and thus to a shard's key range). They are what make a table *routable*. [VSchema and Query Serving](https://vitess.io/docs/24.0/user-guides/vschema-guide/)
- **Routing rules.** Declarative overrides that remap a name to a different target: table routing (including view routing), keyspace routing, shard routing, and mirror rules. Rules are matched by keyspace-qualified name, optionally with a tablet-type suffix.
- **Table types.** Besides ordinary tables, a VSchema can mark a table as a `sequence` (a table backing an auto-increment column) or as a `reference` (a logical table in one keyspace backed by a table in another).
- **Views.** Keyspaces can define views (stored as `SELECT`/`UNION` statements in the VSchema); in v24, views can also be the *target* of routing rules, letting one view name resolve to a view in a different keyspace.

## Structure

The in-memory object is the `VSchema` struct in [`go/vt/vtgate/vindexes/vschema.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vindexes/vschema.go):

- **`Keyspaces map[string]*KeyspaceSchema`** — one entry per keyspace. A `KeyspaceSchema` holds the keyspace (name + sharded flag), foreign-key mode, `preventCrossKeyspaceReads`, the table map, the vindex map, the view map, and the multi-tenancy spec.
- **`Tables` (`BaseTable`)** — each table carries its `ColumnVindexes` (primary first, then secondaries), the `Owned` lookup vindexes, the auto-increment linkage to a sequence table, an optional `Pinned` keyspace ID, the `Source` for reference tables, column metadata, primary key and unique keys, and child/parent foreign-key info.
- **`Views`** — name plus the parsed view statement; `FindView` returns a copy-on-rewrite of the statement so planner rewrites do not share AST state.
- **`RoutingRules` / `ViewRoutingRules`** — built from the topology's `RoutingRules`: `from_table` → one keyspace-qualified target, either a table or (v24) a view.
- **`KeyspaceRoutingRules` / `ShardRoutingRules`** — `from_keyspace` → `to_keyspace` and `keyspace.shard` → `to_keyspace` remappings.
- **`MirrorRules`** — `from_table` → `to_table` with a percentage; cross-keyspace only, and mirror chains are rejected.
- **`globalTables` / `uniqueVindexes`** — name → object indexes for unqualified resolution; a name present in more than one keyspace is marked ambiguous (`nil`).

Build-time validation (in `buildTables`, `buildReferences`, `buildRoutingRule`, `buildMirrorRule`): the primary vindex must be unique and not owned; multi-column vindexes must be the primary vindex; sequence tables must be in an unsharded keyspace or pinned; reference tables must point at a valid table in a different keyspace and cannot be chained; routing rules must have exactly one keyspace-qualified target; and a routing entry that appears in both the table and view maps is a duplicate error.

## Vindexes

Built-in vindex implementations live in [`go/vt/vtgate/vindexes/`](https://github.com/vitessio/vitess/tree/main/go/vt/vtgate/vindexes), all implementing the `Vindex` interface in [`vindex.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vindexes/vindex.go):

| Type | File | Behavior |
| ---- | ---- | -------- |
| `hash` | [`hash.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vindexes/hash.go) | Keyspace ID = uniform hash of the column value; even distribution, no range preservation |
| `consistent_lookup` | [`consistent_lookup.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vindexes/consistent_lookup.go) | Keyspace ID = hash of the key, stable across vindex changes; used by resharding and lookup migrations |
| `lookup` | [`lookup.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vindexes/lookup.go) | Maintains a lookup table mapping key values → keyspace IDs; unique or non-unique, optionally *owned* (VTGate writes the lookup table itself) |
| `multicol` | [`multicol.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vindexes/multicol.go) | Composite vindex over several columns, with partial-column support (subsets get higher planning cost) |
| `numeric` | [`numeric.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vindexes/numeric.go) | Keyspace ID derived directly from the numeric column value |
| `binary` | [`binary.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vindexes/binary.go) | Keyspace ID derived from a binary column value |
| `null` | [`null.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vindexes/null.go) | Always returns an empty keyspace ID — the table is effectively unroutable |

Each vindex exposes uniqueness and a planning *cost*; the planner orders a table's `ColumnVindexes` by cost (`Ordered`) and prefers unique vindexes so a query can be pinned to one shard. Lookup vindexes support backfill state (`LookupBackfill`) so routing knows whether the lookup table is still being populated. [Sharded Keyspace](https://vitess.io/docs/24.0/user-guides/vschema-guide/sharded/), [Unique Lookup Vindexes](https://vitess.io/docs/24.0/user-guides/vschema-guide/unique-lookup/), [Non-Unique Lookup Vindexes](https://vitess.io/docs/24.0/user-guides/vschema-guide/non-unique-lookup/), [Lookup as Primary Vindex](https://vitess.io/docs/24.0/user-guides/vschema-guide/lookup-as-primary/), [Shared Vindexes and Foreign Keys](https://vitess.io/docs/24.0/user-guides/vschema-guide/shared-vindexes/)

## Routing rules

Resolution order (in `VSchema.FindRoutedTable` / `FindRoutedView` / `FindRoutedShard`, [`go/vt/vtgate/vindexes/vschema.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vindexes/vschema.go)):

- **Keyspace routing** — `findRoutedKeyspace` first rewrites the keyspace, honoring a tablet-type suffix (`@primary`, `@replica`, …; non-primary types fall back to the `@primary` rule).
- **Table / view routing** — looked up as `keyspace.table@tablet_type`, then `keyspace.table`. A matched rule redirects to the target table or view; an empty target means the table is *disabled*.
- **Shard routing** — `FindRoutedShard` maps `keyspace.shard` → target keyspace (used by resharding cutovers).
- **Default** — with no rule, `findTable` resolves the name directly: unsharded keyspaces can serve any table even if it is not listed in the VSchema, and unqualified names resolve through `globalTables` (erroring as ambiguous when the name exists in several keyspaces).

[Sequences](https://vitess.io/docs/24.0/user-guides/vschema-guide/sequences/) also obey routing rules — the sequence target is resolved through `FindRoutedTable` when auto-increments are wired up.

## How it works

- **Storage.** The topology service stores one `SrvVSchema` (`go/vt/proto/vschema/vschema.proto`) per keyspace, plus cluster-wide rule sets (routing, keyspace routing, shard routing, mirror rules).
- **Materialization.** `VSchemaManager` ([`go/vt/vtgate/vschema_manager.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vschema_manager.go)) watches the topology and, on change, calls `BuildVSchema` (in [`go/vt/vtgate/vindexes/vschema.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vindexes/vschema.go)), which builds keyspaces → global tables → references → routing rules → shard/keyspace routing → mirror rules → auto-increment resolution, in that order. The result is cached with a creation timestamp so VTGate can detect a stale copy and invalidate the plan cache.
- **Planning.** The planbuilder ([`go/vt/vtgate/planbuilder/`](https://github.com/vitessio/vitess/tree/main/go/vt/vtgate/planbuilder)) consults the materialized `VSchema` to resolve tables and vindexes; vindex-based routing is where the planner pins a query to a shard or decides to scatter. [Query Serving (design doc)](https://vitess.io/docs/design-docs/query-serving/)
- **Applying changes.**
  - `vtctl ApplySchema` applies a VSchema JSON file from disk to the topology (command wiring in [`go/vt/vtctl/vtctl.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/vtctl.go)).
  - `ApplyVSchemaDDL` ([`go/vt/topotools/vschema_ddl.go`](https://github.com/vitessio/vitess/blob/main/go/vt/topotools/vschema_ddl.go)) applies VSchema DDL statements — `CREATE VINDEX`, `ALTER VSCHEMA …`, routing-rule statements — parsed and planned by VTGate (the `VSchemaDdl` plan type in [`go/vt/vtgate/planbuilder/builder.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/planbuilder/builder.go)) and executed by the executor (covered by [`go/vt/vtgate/executor_vschema_ddl_test.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/executor_vschema_ddl_test.go)), then written to the topology via vtadmin/vtctl.
  - On Kubernetes, the operator exposes the same VSchema as a CRD and applies it through the same server path. [VSchema guide overview](https://vitess.io/docs/24.0/user-guides/vschema-guide/overview/)

## Key components

| Component | Where | Role |
| --------- | ----- | ---- |
| `VSchema` (in-memory) | [`go/vt/vtgate/vindexes/vschema.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vindexes/vschema.go) | Denormalized routing object: keyspaces, tables, views, all rule types |
| `KeyspaceSchema` / `BaseTable` / `ColumnVindex` | [`go/vt/vtgate/vindexes/vschema.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vindexes/vschema.go) | Per-keyspace schema, table definitions, vindex bindings |
| Vindex implementations | [`go/vt/vtgate/vindexes/`](https://github.com/vitessio/vitess/tree/main/go/vt/vtgate/vindexes) ([`hash.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vindexes/hash.go), [`lookup.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vindexes/lookup.go), [`consistent_lookup.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vindexes/consistent_lookup.go), [`multicol.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vindexes/multicol.go), [`numeric.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vindexes/numeric.go), [`binary.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vindexes/binary.go), [`null.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vindexes/null.go)) | Column value → keyspace ID mapping |
| `VSchemaManager` | [`go/vt/vtgate/vschema_manager.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/vschema_manager.go) | Watches topology `SrvVSchema`, rebuilds and caches the in-memory VSchema |
| `SrvVSchema` proto | `go/vt/proto/vschema/vschema.proto` | Topology-stored schema + rule definitions |
| VSchema DDL | [`go/vt/vtgate/planbuilder/builder.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtgate/planbuilder/builder.go) (planning), [`go/vt/topotools/vschema_ddl.go`](https://github.com/vitessio/vitess/blob/main/go/vt/topotools/vschema_ddl.go) (apply), [`go/vt/vtctl/vtctl.go`](https://github.com/vitessio/vitess/blob/main/go/vt/vtctl/vtctl.go) (commands) | SQL-based VSchema changes through VTGate/vtadmin/vtctl |
| Planner | [`go/vt/vtgate/planbuilder/`](https://github.com/vitessio/vitess/tree/main/go/vt/vtgate/planbuilder) | Resolves tables/vindexes to build shard-routed execution plans |

## Upstream docs

- [VSchema (concept)](https://vitess.io/docs/24.0/concepts/vschema/)
- [VSchema and Query Serving (user guide)](https://vitess.io/docs/24.0/user-guides/vschema-guide/)
- [Sharding Guidelines](https://vitess.io/docs/24.0/user-guides/vschema-guide/sharding-guidelines/)
- [Configuring an unsharded Keyspace](https://vitess.io/docs/24.0/user-guides/vschema-guide/unsharded/)
- [Sharded Keyspace](https://vitess.io/docs/24.0/user-guides/vschema-guide/sharded/)
- [Sequences](https://vitess.io/docs/24.0/user-guides/vschema-guide/sequences/)
- [Foreign Keys in Vitess](https://vitess.io/docs/24.0/user-guides/vschema-guide/foreign-keys/)
- [Query Serving (design doc)](https://vitess.io/docs/design-docs/query-serving/)
