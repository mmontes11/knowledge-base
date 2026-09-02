---
upstream: https://github.com/n8n-io/n8n
last_updated: 2026-08-19
---

# n8n — features

Key feature areas, each linked to the upstream documentation (2.36 line) covering it. n8n sits between iPaaS-style automation (visual canvas, 400+ integrations, webhooks/schedules) and application servers (REST API, CLI, data tables, RBAC), with first-class AI agent building on both directions: agents that consume external tools (MCP) and n8n itself exposed as a tool/MCP server to external agents.

## AI capabilities

- **AI Agent node**: LangChain-based root node composing LLMs, tools, memory, and other agent variants (tools agent, Plan-and-Execute, structured-output, code-based, …) into a workflow. [docs](https://docs.n8n.io/integrations/builtin/cluster-nodes/root-nodes/n8n-nodes-langchain.agent)
- **MCP client**: consume tools from external MCP servers — as a [standalone MCP Client Trigger-less node](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-langchain.mcpclient) or as an [MCP Tool sub-node](https://docs.n8n.io/integrations/builtin/cluster-nodes/sub-nodes/n8n-nodes-langchain.toolmcp) attached to an agent.
- **MCP Trigger (n8n as MCP server)**: expose n8n workflows/tools to outside MCP clients (Claude Desktop, opencode, Cursor, …); see the [node docs](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-langchain.mcptrigger) and [how to connect](https://docs.n8n.io/connect/connect-to-n8n-mcp-server). A companion [n8n-docs MCP server](https://docs.n8n.io/connect/connect-to-n8n-docs-mcp-server) serves the documentation itself to AI clients.
- **AI Assistant**: generate and edit whole workflows by chat (with credential setup, live canvas lock, and publish gating). [docs](https://docs.n8n.io/build/ways-of-building-workflows/ai-assistant)
- **Build and manage agents**: workflow-level agent building, channels, and agent runtime behavior. [docs](https://docs.n8n.io/build/build-and-manage-agents)

## Integrations

- **400+ built-in nodes**: the [integrations index](https://docs.n8n.io/integrations/) lists every node by service (cloud APIs, databases, communication, file transfer, AI providers, …), including [core nodes](https://docs.n8n.io/integrations/builtin/core-nodes/) such as [Webhook](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.webhook), Schedule, HTTP Request, IF, Merge, Code, and Set.
- **Community nodes**: installable packages for services without an official node. [docs](https://docs.n8n.io/integrations/community-nodes/)

## Data and storage

- **Data tables**: built-in tabular storage with rows, typed columns, and node/API access — the primary in-product store for structured data. [docs](https://docs.n8n.io/build/work-with-data/data-tables/)
- Instances persist state (workflows, executions, credentials) in SQLite by default or Postgres at scale; credentials can additionally be backed by external secret stores. [community edition features](https://docs.n8n.io/deploy/host-n8n/community-edition-features/), [external secret stores](https://docs.n8n.io/administer/manage-credentials/use-external-secret-stores/)

## Scaling

- **Scaling n8n**: architecture overview for running beyond a single process. [docs](https://docs.n8n.io/deploy/host-n8n/configure-n8n/scaling/)
- **Queue mode**: split the editor ("main") from workers backed by Redis + a queue broker for horizontal execution scaling. [enable queue mode](https://docs.n8n.io/deploy/host-n8n/configure-n8n/scaling/enable-queue-mode/)

## Multi-tenant and enterprise

- **RBAC**: roles and granular permissions per user/project. [docs](https://docs.n8n.io/administer/manage-users-and-access/set-permissions-and-roles-rbac/)
- **User identity**: [OIDC](https://docs.n8n.io/administer/manage-users-and-access/verify-user-identity/use-oidc/), [SAML](https://docs.n8n.io/administer/manage-users-and-access/verify-user-identity/use-saml/), [LDAP](https://docs.n8n.io/administer/manage-users-and-access/verify-user-identity/connect-ldap/), and [two-factor authentication](https://docs.n8n.io/administer/manage-users-and-access/verify-user-identity/require-two-factor-auth/) (enterprise).
- **Credentials**: [end-user credentials](https://docs.n8n.io/administer/manage-credentials/end-user-credentials/) for per-customer (multi-tenant) auth in hosted workflows.
- **Source control and environments**: Git-backed workflow branches/pull requests with environment promotion. [overview](https://docs.n8n.io/administer/use-source-control-and-environments/) / [work with environments](https://docs.n8n.io/administer/use-source-control-and-environments/work-with-environments/)
- **Observability**: [Insights usage tracking](https://docs.n8n.io/administer/observe-and-log/track-usage-with-insights/) and [log streaming](https://docs.n8n.io/administer/observe-and-log/stream-logs-to-external-systems/) to external systems.

## Editions and license

- **Community edition**: everything except the enterprise gates above; feature matrix at [community edition features](https://docs.n8n.io/deploy/host-n8n/community-edition-features/).
- **License terms**: fair-code [Sustainable Use License](https://docs.n8n.io/privacy-and-security/sustainable-use-license) for self-hosting (no OSI open-source license; commercial redistribution is prohibited); enterprise features are additionally licensed via the EE license — see [License](https://docs.n8n.io/privacy-and-security/sustainable-use-license) and the repo's `LICENSE.md` / `LICENSE_EE.md`.
