---
upstream: https://github.com/theagentrouter/agent-router
last_updated: 2026-09-25
---

# agent-router

Agent Router is the open source control plane for AI and agent traffic, powered by Envoy and Envoy Gateway. It is an Agentic AI Foundation project and the successor to **Envoy AI Gateway** (same code, maintainers, release cadence, and Apache 2.0 license). It gives application teams one consistent, OpenAI-compatible API for every model and tool — from hosted providers to self-hosted inference and MCP servers — while platform teams keep credentials, routing, quotas, failover, and usage attribution in one place, enforced by Envoy. "Agent Router controls. Envoy carries."

- Upstream repository: https://github.com/theagentrouter/agent-router
- Website / Documentation: https://theagentrouter.ai/docs
- Quickstart: https://theagentrouter.ai/docs/getting-started/ · Release Notes: https://theagentrouter.ai/release-notes/
- Community: [Discord](https://discord.gg/xuxtPq43gZ); weekly Monday meeting
- License: Apache-2.0 (Agentic AI Foundation / LF Projects)
- Stack: Go (`github.com/envoyproxy/ai-gateway`); Envoy + Envoy Gateway; `aigw` CLI; Helm charts `docker.io/envoyproxy/ai-gateway-*`; namespace `envoy-ai-gateway-system`
- Formerly: `envoyproxy/ai-gateway` (repo and website moved; old links redirect; CRDs, API group `aigateway.envoyproxy.io`, images, and Go module path are unchanged)

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
