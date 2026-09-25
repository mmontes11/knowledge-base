---
upstream: https://github.com/kagent-dev/kagent
last_updated: 2026-09-25
---

# kagent

kagent is a Kubernetes-native framework for building AI agents, a Cloud Native Computing Foundation (CNCF) project. Agents are declared as a Kubernetes custom resource (an `Agent` = a system prompt + a set of tools + an LLM configuration), which makes agents and tools first-class, declarative objects managed with familiar `kubectl` workflows. LLM providers are represented by a `ModelConfig` resource, and tools are Model Context Protocol (MCP) servers exposed as `ToolServer` CRs (kagent ships an MCP server with tools for Kubernetes, Istio, Helm, Argo, Prometheus, Grafana, Cilium, and more). kagent runs agents on the Google ADK engine and is built from four components — a controller, a web UI, an engine, and a CLI — and is designed to be extensible, observable (OpenTelemetry tracing), and testable.

- Upstream repository: https://github.com/kagent-dev/kagent
- Documentation: https://kagent.dev (site and docs, at `https://kagent.dev/docs/kagent/`)
- Community: [Discord](https://discord.gg/Fu3k65f2k3); CNCF Slack `#kagent`
- License: Apache-2.0
- Stack: Kubernetes; Go controller; agents executed on the Google ADK engine; MCP for tools; web UI + CLI

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
