---
upstream: https://github.com/open-webui/open-webui
last_updated: 2026-09-03
---

# open-webui — features

Significant feature areas, each with a link to the matching upstream doc. The [documentation site](https://docs.openwebui.com) is authoritative; the [features overview](https://docs.openwebui.com/features/) catalogues them all.

## Model providers

Open WebUI fronts any Ollama or OpenAI-compatible API (plus first-party adapters for OpenAI, OpenRouter, Anthropic, Google, Mistral, Groq, etc.); the OpenAI-compatible surface is the one used by this stack (see [README](README.md)). The backend registers provider models in its own model registry, which the workspace manages.

- [Connect a provider overview](https://docs.openwebui.com/getting-started/quick-start/connect-a-provider/)
- [Llama.cpp setup (OpenAI-compatible)](https://docs.openwebui.com/getting-started/quick-start/connect-a-provider/starting-with-llama-cpp)
- [OpenAI-compatible providers](https://docs.openwebui.com/getting-started/quick-start/connect-a-provider/starting-with-openai-compatible)

## Chat and conversations

Multi-model conversations (mention `@model` in one chat), web search with many providers (Bing, Brave, Exa, Firecrawl, SearXNG, ...), image generation and editing (ComfyUI, Automatic1111, OpenAI, Gemini, ...), audio (TTS, speech-to-text, Voice Mode with real-time voice calls), reasoning-model display, automatic context compaction (v0.10.0), and human-in-the-loop tool approval that asks before each tool call (v0.11.1).

- [Chat features](https://docs.openwebui.com/features/chat-conversations/)
- [Web search](https://docs.openwebui.com/features/chat-conversations/web-search)
- [Image generation and editing](https://docs.openwebui.com/features/chat-conversations/image-generation-and-editing/usage)
- [Audio (TTS/STT/Voice Mode)](https://docs.openwebui.com/features/chat-conversations/audio)
- [Automations (scheduled/triggered chat runs)](https://docs.openwebui.com/features/chat-conversations/chat-features/automations)
- [Memory (automatic + explicit)](https://docs.openwebui.com/features/chat-conversations/memory)

## RAG and knowledge bases

User- and admin-managed knowledge bases with chunking, multiple extraction backends (Docling, Apache Tika, Mistral OCR, PaddleOCR-vl), several vector stores (Chroma, PGVector, Qdrant, Milvus, LanceDB, ...), folder structure (v0.9.6), and incremental sync via the `oikb` companion tool (directories, GitHub, S3, Confluence, 40+ sources).

- [RAG overview](https://docs.openwebui.com/features/chat-conversations/rag/)
- [Document extraction backends](https://docs.openwebui.com/features/chat-conversations/rag/document-extraction/)
- [Knowledge base (workspace)](https://docs.openwebui.com/features/workspace/knowledge)
- [oikb sync tool](https://docs.openwebui.com/ecosystem/knowledge-base-sync)

## Workspace

Workspace-level management of models (import/export/sync), knowledge, prompts, and skills; folder organization and folder sharing (v0.10.0).

- [Workspace overview](https://docs.openwebui.com/features/workspace/)
- [Models](https://docs.openwebui.com/features/workspace/models)

## Channels, notes, calendar

Team collaboration surfaces: `@model`-aware channels, shared notes, and a calendar the assistant can read and write (events with reminders, automations).

- [Channels](https://docs.openwebui.com/features/channels/)
- [Notes](https://docs.openwebui.com/features/notes/)
- [Calendar](https://docs.openwebui.com/features/calendar/)

## Extensibility: plugins, pipelines, tools, MCP

Python plugin system with **Filters** (pre/post-process requests; a `request` step added in v0.11.2 adjusts the payload before each model call), **Actions** (intercept), **Pipes** (custom LLM backends), and **Tools** (server-side function calling, with Rich UI); tool servers over OpenAPI and MCP (Model Context Protocol) so external MCP servers can be plugged in directly.

- [Plugin system](https://docs.openwebui.com/features/extensibility/plugin/)
- [Functions (filter/action/event/pipe)](https://docs.openwebui.com/features/extensibility/plugin/functions/)
- [Tools](https://docs.openwebui.com/features/extensibility/plugin/tools/)
- [OpenAPI tool servers](https://docs.openwebui.com/features/extensibility/plugin/tools/openapi-servers/)
- [MCP](https://docs.openwebui.com/features/extensibility/mcp)
- [Pipelines](https://docs.openwebui.com/features/extensibility/pipelines/)
- [Community plugins](https://docs.openwebui.com/features/extensibility/community)

## Users, groups, and access control

Multi-user with roles (admin/config/pending/user), groups, and granular per-resource permissions (models, tools, knowledge, folders); external authentication via SSO (SAML/OIDC), LDAP, and SCIM; per-user API keys.

- [Authentication and access overview](https://docs.openwebui.com/features/authentication-access/)
- [RBAC](https://docs.openwebui.com/features/authentication-access/rbac/)
- [SSO (SAML/OIDC)](https://docs.openwebui.com/features/authentication-access/auth/sso/)
- [LDAP](https://docs.openwebui.com/features/authentication-access/auth/ldap)
- [SCIM provisioning](https://docs.openwebui.com/features/authentication-access/auth/scim)
- [API keys](https://docs.openwebui.com/features/authentication-access/api-keys)

## Administration

Admin panel: analytics, evaluation (LLM-judge chat evaluations), webhooks (notification targets since v0.11.0), retrieval/audio/image config, and security hardening docs for deployment.

- [Administration](https://docs.openwebui.com/features/administration/)
- [Analytics](https://docs.openwebui.com/features/administration/analytics/)
- [Evaluation](https://docs.openwebui.com/features/administration/evaluation/)
- [Webhooks/notifications](https://docs.openwebui.com/features/administration/webhooks)
- [Deployment hardening](https://docs.openwebui.com/getting-started/advanced-topics/hardening)
