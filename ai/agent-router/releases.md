---
upstream: https://github.com/theagentrouter/agent-router
last_updated: 2026-09-25
---

# agent-router — releases

Agent Router (formerly Envoy AI Gateway) follows a minor/patch cadence with release candidates for each minor. The stable 1.x API began with v1.0.0 (GA). Release pages: https://github.com/theagentrouter/agent-router/releases.

## v1.1.0 — 2026-08-21

[Release page](https://github.com/theagentrouter/agent-router/releases/tag/v1.1.0) — first minor on the stable 1.x API.

- Token counting across providers; per-request upstream credentials; stream idle timeout with failover.

## v1.0.0 — 2026-06-23

[Release page](https://github.com/theagentrouter/agent-router/releases/tag/v1.0.0) — General Availability.

- The core control-plane API — `AIGatewayRoute`, `AIServiceBackend`, `BackendSecurityPolicy` — reaches GA.

## v1.0.0-rc1 — 2026-06-23

[Release page](https://github.com/theagentrouter/agent-router/releases/tag/v1.0.0-rc1) — release candidate for v1.0.0 (prerelease).

## v0.7.0 — 2026-06-06

[Release page](https://github.com/theagentrouter/agent-router/releases/tag/v0.7.0) — 0.x line.

- Hostname-based routing on `AIGatewayRoute`, enabling multi-tenant deployments where different hostnames expose different backends.

## v0.6.0 — 2026-05-05

[Release page](https://github.com/theagentrouter/agent-router/releases/tag/v0.6.0) — first production-ready API surface.

- Core CRDs (`AIGatewayRoute`, `AIServiceBackend`, `BackendSecurityPolicy`, …) marked production-ready.

## v0.6.0-rc1 — 2026-04-30

[Release page](https://github.com/theagentrouter/agent-router/releases/tag/v0.6.0-rc1) — release candidate for v0.6.0 (prerelease).

## v0.5.0 — 2026-01-23

[Release page](https://github.com/theagentrouter/agent-router/releases/tag/v0.5.0) — 0.x line.

- Multi-gateway configuration, prompt caching cost savings, fine-grained MCP authorization, OpenAI Responses API, and Google Search grounding for Gemini.

## v0.5.0-rc1 — 2026-01-12

[Release page](https://github.com/theagentrouter/agent-router/releases/tag/v0.5.0-rc1) — release candidate for v0.5.0 (prerelease).

## v0.4.0 — 2025-11-08

[Release page](https://github.com/theagentrouter/agent-router/releases/tag/v0.4.0) — 0.x line.

- Introduces the MCP Gateway, OpenAI Image Generation, and Anthropic support (direct and AWS Bedrock).

## v0.4.0-rc2 — 2025-11-07

[Release page](https://github.com/theagentrouter/agent-router/releases/tag/v0.4.0-rc2) — release candidate for v0.4.0 (prerelease).
