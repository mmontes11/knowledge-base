---
upstream: https://github.com/n8n-io/n8n
last_updated: 2026-08-19
---

# n8n — API reference

n8n is exposed through three surfaces: a **REST API** served by the same process as the web editor (JSON, authenticated with an API key created in the editor under *Settings → n8n API*), a **command-line client** (`n8n` CLI shipped in the Docker image), and the **runtime HTTP surface** that workflows themselves listen on (webhooks, MCP servers). The full HTTP reference is upstream at [API reference](https://docs.n8n.io/connect/n8n-api/api-reference); one row per API resource below.

| Surface | Purpose | Upstream docs |
| --- | --- | --- |
| Workflows | Create/update/delete workflows, manage draft vs published versions, activate/deactivate, execute, clone, evaluate. | [workflows](https://docs.n8n.io/connect/n8n-api/workflow) |
| Executions | List and retrieve execution history, stop running executions, inspect node payload data. | [executions](https://docs.n8n.io/connect/n8n-api/execution) |
| Credentials | CRUD for credentials (secrets are redacted on read); scope per project/user. | [credentials](https://docs.n8n.io/connect/n8n-api/credential) |
| Users | Manage user accounts, invitation, roles, owner/administrator/member status. | [users](https://docs.n8n.io/connect/n8n-api/user) |
| Roles | Role definitions and user-to-role bindings (RBAC). | [roles](https://docs.n8n.io/connect/n8n-api/role) |
| Projects | Team projects and project–user membership. | [projects](https://docs.n8n.io/connect/n8n-api/projects) |
| Folders | Folder tree organization of workflows/credentials. | [folders](https://docs.n8n.io/connect/n8n-api/folders) |
| Tags | Label workflows and other resources. | [tags](https://docs.n8n.io/connect/n8n-api/tags) |
| Data tables | Structured row/column storage accessible from nodes and the API. | [data tables](https://docs.n8n.io/connect/n8n-api/data-table) |
| Variables | Instance-wide key/value variables shared by workflows. | [variables](https://docs.n8n.io/connect/n8n-api/variables) |
| Models | LLM provider/model catalog used by AI nodes. | [models](https://docs.n8n.io/connect/n8n-api/models) |
| Source control | Branches, sync, and pull requests for workflow repos (enterprise). | [source control](https://docs.n8n.io/connect/n8n-api/source-control) |
| Evaluation | Trigger and compare evaluations of AI workflows. | [evaluation](https://docs.n8n.io/connect/n8n-api/evaluation) |
| Insights | Usage metrics (executions, node usage) for reporting. | [insights](https://docs.n8n.io/connect/n8n-api/insights) |
| Audit | Audit log entries (enterprise). | [audit](https://docs.n8n.io/connect/n8n-api/audit) |
| Settings: SSO OIDC | Configure OpenID Connect SSO (enterprise). | [settings: SSO OIDC](https://docs.n8n.io/connect/n8n-api/settings-sso-oidc) |
| Settings: SSO SAML | Configure SAML SSO (enterprise). | [settings: SSO SAML](https://docs.n8n.io/connect/n8n-api/settings-sso-saml) |
| Settings: LDAP | Configure LDAP (enterprise). | [settings: LDAP](https://docs.n8n.io/connect/n8n-api/settings-ldap) |
| Log streaming | Stream n8n logs to external systems. | [log streaming](https://docs.n8n.io/connect/n8n-api/log-streaming) |

## Guides and clients

- [Authentication](https://docs.n8n.io/connect/n8n-api/authentication) — creating and using API keys.
- [Pagination](https://docs.n8n.io/connect/n8n-api/pagination) — cursor/limit semantics of list endpoints.
- [Use an API playground](https://docs.n8n.io/connect/n8n-api/use-an-api-playground) — interactive testing of an instance's API.
- [Community Package](https://docs.n8n.io/connect/n8n-api/community-package) and [N8n Package](https://docs.n8n.io/connect/n8n-api/n8n-package) — official npm client packages for scripting against the REST API.
- [Command line](https://docs.n8n.io/deploy/host-n8n/configure-n8n/use-the-command-line) — the `n8n` CLI: import/export workflows and credentials, update workflows, evaluate workflows, and user operations.

## Runtime HTTP surface (workflows listening)

- [Webhook node](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.webhook) — the primary trigger for inbound HTTP requests (production and test URLs per workflow).
- [MCP trigger node](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-langchain.mcptrigger) — exposes n8n workflows/tools to external MCP clients; [tool reference](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-langchain.mcptrigger#tool-reference) for the tools it serves.
- [Connect to n8n MCP server](https://docs.n8n.io/connect/connect-to-n8n-mcp-server) — how agents and CLI clients (Claude, Cursor, opencode, …) attach to that MCP server.
- [Connect to n8n docs MCP server](https://docs.n8n.io/connect/connect-to-n8n-docs-mcp-server) — MCP server that serves the n8n documentation to AI clients.

## Notes

- In `mmontes11/k8s-ai`, the instance is deployed at `apps/n8n/` with the `n8nio/n8n:2.26.3` image (Helm chart 1.9.0 from `oci://ghcr.io/n8n-io/n8n-helm-chart/n8n`), SQLite storage, no queue mode, on host `https://n8n.mmontes-internal.duckdns.org`.
- Enterprise-gated resources (projects, audit, SSO settings, source control, log streaming) require the respective enterprise license flags; the community surface covers workflows, executions, credentials, tags, folders, variables, data tables, and users (owner-level operations).
