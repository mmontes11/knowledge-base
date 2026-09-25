---
upstream: https://github.com/kagent-dev/kagent
last_updated: 2026-09-25
---

# kagent — API reference

kagent's "API" is its set of **Kubernetes custom resources** (CRDs) plus the **web UI** and the **CLI** used to manage them. There is no separate REST control plane; you create and update agents, models, and tools as K8s objects and the controller reconciles them into the resources that run the agents. The full docs live at https://kagent.dev/docs/kagent/.

| Surface | Purpose | Upstream docs |
| --- | --- | --- |
| `Agent` CRD | An agent = system prompt + tools + LLM configuration; the main building block of kagent. | [Quick Start](https://kagent.dev/docs/kagent/getting-started/quickstart) |
| `ModelConfig` CRD | Declares an LLM provider/model (OpenAI, Azure OpenAI, Anthropic, Google Vertex AI, Ollama, or any custom provider reachable via an AI gateway). | [Supported providers](https://kagent.dev/docs/kagent/supported-providers/openai) |
| `ToolServer` CRD | An MCP server exposing tools, usable by multiple agents (built-in tools for Kubernetes, Istio, Helm, Argo, Prometheus, Grafana, Cilium, …). | [Quick Start](https://kagent.dev/docs/kagent/getting-started/quickstart) |
| Controller | Watches the kagent CRDs and creates the resources needed to run the agents. | [README — Architecture](https://github.com/kagent-dev/kagent#architecture) |
| Web UI | Manage and inspect agents and tools. | [Installation](https://kagent.dev/docs/kagent/introduction/installation) |
| CLI | Command-line management of agents and tools. | [README — Technical Details](https://github.com/kagent-dev/kagent#technical-details) |

## Notes

- Everything is declarative YAML; the controller reconciles CRs to the running state.
- Agents run on the Google ADK engine (see [ADK docs](https://google.github.io/adk-docs/)).
- The project is in active development; track the [roadmap](https://github.com/orgs/kagent-dev/projects/3).
