---
upstream: https://github.com/kagent-dev/kagent
last_updated: 2026-10-01
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
- **A2A execution**: A2A is served per-instance (JSON-RPC + agent cards) and runs in runtimes backed by a gRPC TaskStore. (introduced in [v1.0.0-alpha4](https://github.com/kagent-dev/kagent/releases/tag/v1.0.0-alpha4))
- **Agent Substrate**: kagent can run sandboxed, stateful agent workloads on [Agent Substrate](https://github.com/agent-substrate/substrate); updated to Substrate v0.3.0-alpha1 in the 1.0.0 alpha line. (announcement)

## Sandbox runtimes

- **MicroVM sandboxes**: the agent harness supports microVM-based sandboxes for isolated execution. (introduced in [v1.0.0-alpha4](https://github.com/kagent-dev/kagent/releases/tag/v1.0.0-alpha4))
- **Standalone sandboxes + API**: new `Sandbox` and `SandboxTemplate` contracts and standalone sandbox runtimes, with sandbox workflows and grouped agent commands. (introduced in [v1.0.0-alpha5](https://github.com/kagent-dev/kagent/releases/tag/v1.0.0-alpha5))
