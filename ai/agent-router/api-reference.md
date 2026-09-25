---
upstream: https://github.com/theagentrouter/agent-router
last_updated: 2026-09-25
---

# agent-router — API reference

Agent Router is exposed through three surfaces: **Kubernetes custom resources** in the `aigateway.envoyproxy.io` API group that declare routing and backends, an **OpenAI-compatible HTTP API** served by the data plane (Envoy), and the **`aigw` CLI** for standalone runs and day-2 operations. The full reference is at https://theagentrouter.ai/docs, with [Concepts](https://theagentrouter.ai/docs/concepts/) covering the architecture and resources.

| Surface | Purpose | Upstream docs |
| --- | --- | --- |
| `AIGatewayRoute` CRD | Declares AI routes: how inbound OpenAI-compatible requests map to backends, with hostname-based routing for multi-tenant deployments. | [Concepts](https://theagentrouter.ai/docs/concepts/) |
| `AIServiceBackend` CRD | Declares a model/tool backend (hosted provider, self-hosted inference, or MCP server) with credentials and failover. | [Concepts](https://theagentrouter.ai/docs/concepts/) |
| `BackendSecurityPolicy` CRD | Security policy applied to a backend (per-request upstream credentials, auth). | [Concepts](https://theagentrouter.ai/docs/concepts/) |
| OpenAI-compatible API | `POST /v1/...` endpoints served by Envoy; point any OpenAI-compatible client at the gateway (standalone default `http://localhost:1975/v1`). | [CLI guide](https://theagentrouter.ai/docs/cli/) |
| MCP Gateway | Serves and authorizes Model Context Protocol servers through the same gateway. | [Concepts](https://theagentrouter.ai/docs/concepts/) |
| `aigw` CLI | Run standalone (`aigw run`) with provider auto-configuration, and deploy on Kubernetes with Envoy Gateway. | [CLI guide](https://theagentrouter.ai/docs/cli/) |

## Notes

- **Two-tier gateway pattern**: the Tier One gateway handles authentication, top-level routing, and global rate limiting; the Tier Two gateway provides fine-grained control over self-hosted model access (with an endpoint picker for LLM inference optimization). (README)
- The API group `aigateway.envoyproxy.io`, CRD names, the `aigw` CLI, the `envoy-ai-gateway-system` namespace, container images, Helm charts, and the Go module path are all unchanged from the Envoy AI Gateway era.
- Standalone quick start: `OPENAI_API_KEY=sk-... aigw run`, then point clients at `http://localhost:1975/v1`.
