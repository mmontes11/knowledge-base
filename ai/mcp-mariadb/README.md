---
upstream: https://github.com/mmontes11/mcp-mariadb
last_updated: 2026-08-21
---

# mcp-mariadb

MariaDB [MCP](https://modelcontextprotocol.io/) (Model Context Protocol) server in Python: instead of wiring each AI agent to its own database client, you point an MCP client at the server and the agent gets a small tool surface over a MariaDB server — database and table discovery, table schemas (including foreign-key relations), and parameterized, read-only SQL execution (verified against the live homelab instances, running `0.4.0` against MariaDB 11.8.6).

- Upstream repository: https://github.com/mmontes11/mcp-mariadb — **not publicly accessible** as of 2026-08-21 (HTTP 404 for anonymous and `mmontes11`-authenticated API access); the publicly verifiable artifact is the container image on Docker Hub.
- Container image: [docker.io/mmontes11/mcp-mariadb](https://hub.docker.com/r/mmontes11/mcp-mariadb) (latest tag: `0.4.0`, pushed 2026-05-03; multi-arch amd64/arm64; Python 3.11.15 base; entrypoint `python src/server.py`, default transport `sse`, port 9001).
- License: not published (upstream repository is private).
- Homelab deployment: one upstream project, two instances in the homelab cluster's `ai` namespace, defined in [mmontes11/k8s-ai](https://github.com/mmontes11/k8s-ai) — `infrastructure/mcp-photoprism-mmontes` (against the media MariaDB, database `photoprism`) and `infrastructure/mcp-photoprism-xiaowen` (against the xiaowen MariaDB, database `photoprism`). Both run image `mmontes11/mcp-mariadb:0.4.0` in read-only mode over an HTTP MCP transport; credentials live in per-instance SealedSecrets (referenced by name only, e.g. `photoprism-mmontes`).

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
