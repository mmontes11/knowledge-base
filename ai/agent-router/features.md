---
upstream: https://github.com/theagentrouter/agent-router
last_updated: 2026-09-25
---

# agent-router — features

Agent Router is an Envoy-based control plane that gives one OpenAI-compatible API for every model and tool, with platform-level control over credentials, routing, quotas, failover, and usage. Key capability areas, each linked to the docs that cover it.

## OpenAI-compatible API

- One consistent, OpenAI-compatible API for every model and tool, from hosted providers to self-hosted inference and MCP servers. [Concepts](https://theagentrouter.ai/docs/concepts/)

## Multi-provider

- Supported providers include OpenAI, Azure OpenAI, Google Gemini, Vertex AI, AWS Bedrock, Mistral, Cohere, Groq, Together AI, DeepInfra, DeepSeek, Hunyuan, SambaNova, Grok, Tetrate Agent Router Service, and Anthropic. (README)

## Two-tier gateway

- **Tier One**: centralized entry point — authentication, top-level routing, global rate limiting. **Tier Two**: fine-grained control over self-hosted model access with an endpoint picker for LLM inference optimization. (README)

## Routing and multi-tenancy

- **Hostname-based routing** on `AIGatewayRoute` enables multi-tenant deployments where different hostnames expose different backends. (v0.7.0)

## Security and credentials

- **Per-request upstream credentials** and a `BackendSecurityPolicy` CRD keep provider keys in one place, enforced by Envoy. (v1.1.0)
- Quotas, failover, and usage attribution managed centrally. (README)

## MCP

- **MCP Gateway**: serve and authorize MCP servers through the gateway, with fine-grained MCP authorization. (v0.4.0 / v0.5.0)

## Cost and streaming

- **Token counting** across providers and **stream idle timeout with failover**. (v1.1.0)
- **Prompt caching** cost savings, **OpenAI Responses API**, and **Google Search grounding for Gemini**. (v0.5.0)

## Observability

- Usage attribution and analytics for platform teams. [Documentation](https://theagentrouter.ai/docs)
