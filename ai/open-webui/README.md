---
upstream: https://github.com/open-webui/open-webui
last_updated: 2026-08-18
---

# open-webui

Open WebUI is an extensible, self-hosted AI platform: a SvelteKit web application over a FastAPI backend that speaks to local and cloud model providers (Ollama and any OpenAI-compatible API first-class) and layers on chat features (multi-model conversations, web search, image generation and editing, speech-to-text/TTS, voice mode), RAG knowledge bases across multiple vector stores, server-side tool and function calling (including MCP and OpenAPI tool servers), multi-user access control (users, groups, RBAC), and a Python plugin system (Filters, Actions, Pipes, Tools, Skills) plus an OpenAI-compatible HTTP API for external clients.

- Upstream repository: [open-webui/open-webui](https://github.com/open-webui/open-webui)
- Documentation: [docs.openwebui.com](https://docs.openwebui.com)
- License: BSD-3-Clause with an added branding clause ("Open WebUI License"; prior-license history in [LICENSE_HISTORY](https://github.com/open-webui/open-webui/blob/main/LICENSE_HISTORY), [LICENSE](https://github.com/open-webui/open-webui/blob/main/LICENSE))
- Website: [openwebui.com](https://openwebui.com)

## Usage in this stack

Deployed in namespace `ai` from [`k8s-ai/apps/open-webui/`](https://github.com/mmontes11/k8s-ai/tree/main/apps/open-webui): Flux `HelmRepository`/`HelmRelease` install the upstream `open-webui` chart (v16.0.0 from [helm.openwebui.com](https://helm.openwebui.com/)) as a `StatefulSet` on an existing 5 Gi `rook-ceph` PVC (`open-webui`). The bundled Ollama subchart is disabled; models are served by llama.cpp in the same namespace through its OpenAI-compatible API (`http://llamacpp.ai.svc.cluster.local:8080/v1`, passed via `openaiBaseApiUrls`). WebSocket support is enabled (Redis manager on the shared `database` Redis, DB 2). Gateway API `HTTPRoute` `llm` (`llm.mmontes-internal.duckdns.org`) publishes it on the internal network. The PVC is backed up nightly by VolSync/Restic (`ReplicationSource`, daily 01:00, 7-day retention); a `ReplicationDestination` is kept for restores.

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
