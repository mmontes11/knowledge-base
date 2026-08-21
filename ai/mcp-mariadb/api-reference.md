---
upstream: https://github.com/mmontes11/mcp-mariadb
last_updated: 2026-08-21
---

# mcp-mariadb — API reference

The API surface is the set of MCP tools exposed by the server, plus its command-line/env configuration. Surface verified against the live homelab instances running image `0.4.0` (2026-08-21). Because the upstream repository is not public, there is no canonical upstream docs page to link per tool; the tool contracts below are taken from the running `0.4.0` servers, and the image is the canonical public artifact: [docker.io/mmontes11/mcp-mariadb](https://hub.docker.com/r/mmontes11/mcp-mariadb/tags).

## MCP tools (image 0.4.0)

| Tool | Purpose | Notes |
| ---- | ------- | ----- |
| `list_databases` | List all databases accessible on the connected MariaDB server. | Server-level introspection; no arguments. |
| `create_database` | Create a new database if it does not exist. | One of the few DDL operations exposed; a `CREATE` that is a no-op when the database exists. |
| `list_tables` | List all tables within a specified database. | Requires `database_name`. |
| `get_table_schema` | Retrieve the schema (columns, types, keys) of a specific table. | Requires `database_name` and `table_name`. |
| `get_table_schema_with_relations` | Retrieve a table schema including foreign-key relationship information. | Same parameters as `get_table_schema`, plus FK relation context. |
| `execute_sql` | Execute a SQL query against a specified database. | Requires `sql_query` and `database_name`; optional positional `parameters` list for bind parameters. Read-only enforcement is deployment policy (see below), not a per-tool flag. |

## Configuration surface

Verified from the image manifest (`0.4.0`) and the homelab deployment manifests in [mmontes11/k8s-ai](https://github.com/mmontes11/k8s-ai/tree/main/infrastructure).

### Command line (entrypoint `python src/server.py`)

| Flag | Purpose |
| ---- | ------- |
| `--host` | Bind address (image default: `0.0.0.0`). |
| `--port` | Port to listen on (image default: `9001`; `EXPOSE 9001/tcp`). |
| `--transport` | MCP transport (image default: `sse`; homelab instances run `http`). |
| `--path` | HTTP path for the MCP endpoint (homelab instances use `/mcp`). |

### Environment variables

| Variable | Purpose |
| -------- | ------- |
| `DB_HOST` | MariaDB server host. |
| `DB_PORT` | MariaDB server port (homelab: `3306`). |
| `DB_NAME` | Default database name (homelab: `photoprism`). |
| `DB_USER` | Database user (supplied from a per-instance SealedSecret, e.g. name `photoprism-mmontes`, key `DB_USER`). |
| `DB_PASSWORD` | Database password (same SealedSecret, key `DB_PASSWORD`). |
| `MCP_READ_ONLY` | Enable read-only mode (homelab: `true`). |
| `MCP_MAX_POOL_SIZE` | Maximum database connection pool size (homelab: `10`). |
| `ALLOWED_HOSTS` | Comma-separated list of allowed client hosts for the HTTP transport (not reproduced here; see the deployment manifests). |

Notes:

- The homelab deployments reference credentials only by SealedSecret name; no env values (other than non-secret defaults already visible in the manifests) are copied into this knowledge base.
- Field-level documentation is intentionally not duplicated here; follow the upstream repository or the running server's tool schemas for the authoritative signatures.
