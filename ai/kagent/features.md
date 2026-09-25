---
upstream: https://github.com/kagent-dev/kagent
last_updated: 2026-09-25
---

# kagent — features

kagent is a CNCF Kubernetes-native framework for building, deploying, and managing AI agents, where agents and tools are declared as Kubernetes custom resources. Key capability areas, each linked to the docs that cover it.

## Agents as Kubernetes resources

- **Declarative agents**: an `Agent` CR is a system prompt + a set of tools + an LLM configuration; agents and tools are managed with `kubectl` workflows. [Quick Start](https://kagent.dev/docs/kagent/getting-started/quickstart)
- **Core principles**: Kubernetes-native, extensible, flexible, observable, declarative, and testable. [README — Core Principles](https://github.com/kagent-dev/kagent#core-principles)

## LLM providers

- **Multi-provider via `ModelConfig`**: OpenAI, Azure OpenAI, Anthropic, Google Vertex AI, Ollama, plus any custom provider/model accessible via an AI gateway.
  - [OpenAI](https://kagent.dev/docs/kagent/supported-providers/openai) · [Azure OpenAI](https://kagent.dev/docs/kagent/supported-providers/azure-openai) · [Anthropic](https://kagent.dev/docs/kagent/supported-providers/anthropic) · [Google Vertex AI](https://kagent.dev/docs/kagent/supported-providers/google-vertexai) · [Ollama](https://kagent.dev/docs/kagent/supported-providers/ollama)

## MCP tools

- **Built-in MCP server**: tools for Kubernetes, Istio, Helm, Argo, Prometheus, Grafana, Cilium, and others; all tools are `ToolServer` CRs reusable across agents. (README)

## Observability

- **OpenTelemetry tracing**: monitor what agents and tools are doing. [Tracing](https://kagent.dev/docs/kagent/getting-started/tracing)

## Runtime

- **ADK engine**: agents are executed on the Google ADK engine. [Architecture](https://github.com/kagent-dev/kagent#architecture)
- **Agent Substrate**: kagent can run sandboxed, stateful agent workloads on [Agent Substrate](https://github.com/agent-substrate/substrate). (announcement)
