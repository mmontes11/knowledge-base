---
upstream: https://github.com/mmontes11/mcp-mariadb
last_updated: 2026-08-21
---

# mcp-mariadb — features

Key feature areas, each tied to the verifiable source (the running `0.4.0` server, the [Docker Hub image](https://hub.docker.com/r/mmontes11/mcp-mariadb/tags), or the homelab manifests in [mmontes11/k8s-ai](https://github.com/mmontes11/k8s-ai/tree/main/infrastructure)). The upstream repository is not public, so there is no upstream docs tree to cite for deeper detail.

## SQL query execution

- **Parameterized, read-only SQL**: `execute_sql` runs a query against a chosen database with optional bind parameters, and deployments can enforce read-only mode via the `MCP_READ_ONLY` switch (both homelab instances run it with `true`, so an agent can query but not mutate the database). Verified live against the `mcp-photoprism-mmontes` instance (2026-08-21).

## Schema introspection

- **Table schemas**: `get_table_schema` returns the column/type/key layout of one table.
- **Foreign-key relations**: `get_table_schema_with_relations` extends that with the table's FK relationship information — useful for letting an agent navigate a many-table database (e.g. the `photoprism` schema) without hand-written schema documentation.

## Database and table discovery

- **Server-level discovery**: `list_databases` and `list_tables` let the agent enumerate what exists before querying.
- **Database creation**: `create_database` is the one DDL operation exposed — it creates a database if it does not exist, which supports bootstrapping a target database from the agent side even in otherwise read-only setups.

## Connection management

- **Connection pooling**: the server pools its MariaDB connections with the size bounded by `MCP_MAX_POOL_SIZE` (homelab instances: `10`).
- **Single-target wiring**: exactly one upstream server per instance (`DB_HOST`/`DB_PORT`/`DB_NAME`), which is why one project yields multiple homelab instances — see below.

## Transports and delivery

- **MCP transports**: the image defaults to the `sse` transport on port 9001 (`EXPOSE 9001/tcp`); the `http` transport with a configurable `--path` is also supported (both homelab instances serve it at `/mcp`).
- **Container image**: multi-arch (amd64/arm64) on a Python 3.11.15 base; source layout in the image is `/app/src` with a prebuilt venv at `/app/.venv`. [Docker Hub](https://hub.docker.com/r/mmontes11/mcp-mariadb)

## Homelab multi-instance pattern

- **One image, one database per instance**: the same `mmontes11/mcp-mariadb:0.4.0` image backs two live instances in the homelab cluster's `ai` namespace — `mcp-photoprism-mmontes` over the media MariaDB and `mcp-photoprism-xiaowen` over the xiaowen MariaDB, each pointed at its own `photoprism` database ([manifests](https://github.com/mmontes11/k8s-ai/tree/main/infrastructure/mcp-photoprism-mmontes)).
- **Per-instance secrets**: credentials are injected from per-instance SealedSecrets (named e.g. `photoprism-mmontes`), so instances never share credentials; only the secret names ever appear in the manifests.
- **Client allow-listing**: the `ALLOWED_HOSTS` variable restricts which clients the HTTP transport accepts, complementing the read-only mode as the deployment-level security boundary.
