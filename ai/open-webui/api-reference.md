---
upstream: https://github.com/open-webui/open-webui
last_updated: 2026-08-18
---

# open-webui — API reference

Open WebUI's backend exposes a REST API (FastAPI, [main.py](https://github.com/open-webui/open-webui/blob/main/backend/open_webui/main.py) mounts the routers) that the web app and external clients consume. Authentication is via JWT session or per-user API keys sent as `Authorization: Bearer <key>` (header name customizable via `CUSTOM_API_KEY_HEADER` since v0.9.2); keys are managed in Settings > Account. Canonical references: the [API Endpoints guide](https://docs.openwebui.com/reference/api-endpoints), the [API Flow](https://docs.openwebui.com/reference/api-flow), and the [API keys feature doc](https://docs.openwebui.com/features/authentication-access/api-keys). Swagger UI is served at `/docs` only when `ENV=dev`.

## REST API groups

| Prefix | Purpose |
| ------ | ------- |
| `/api/v1/auths/...` | Sign-in/sign-out, session management, profile, LDAP. |
| `/api/v1/users/...` | User administration, role and access management. |
| `/api/v1/groups/...`, `/api/v1/functions/...` | RBAC groups and access-control rules. |
| `/api/v1/models/...` | Model registry: CRUD, tags, and declarative `export`/`import`/`sync` (sync is admin-only). |
| `/api/v1/chats/...`, `/api/v1/channels/...`, `/api/v1/notes/...`, `/api/v1/calendars/...`, `/api/v1/folders/...` | Conversations, folders (incl. folder sharing since v0.10.0), team channels, notes, calendar. |
| `/api/v1/knowledge/...`, `/api/v1/files/...`, `/api/v1/retrieval/...` | RAG: knowledge bases (create, connect, reindex), file upload/search, retrieval config. |
| `/api/v1/prompts/...`, `/api/v1/tools/...`, `/api/v1/skills/...`, `/api/v1/memories/...` | Chat configuration: prompt library, tools (incl. MCP/OpenAPI servers), skills, memory. |
| `/api/v1/pipelines/...`, `/api/v1/tasks/...` | Plugin pipeline (filter/action/pipe) management and task execution. |
| `/api/v1/audio/...`, `/api/v1/images/...` | TTS/stt and image generation endpoints. |
| `/api/v1/configs/...`, `/api/v1/notifications/...`, `/api/v1/automations/...` | Backend configuration, notification (webhook) targets, user automations. |
| `/ollama/...` | Ollama-compatible proxy surface for Ollama clients. |
| `/openai/...` | OpenAI provider admin/config surface (backend config, verification). |

## OpenAI-compatible chat API

The OpenAI client integration exposes `POST /api/chat/completions` and `POST /api/v1/chat/completions` (mirroring the OpenAI Chat Completions request shape, with `model`, `messages`, `stream`, `tool_ids` for server-side tool execution). See the [API Endpoints guide](https://docs.openwebui.com/reference/api-endpoints) for request/response fields.

## Realtime

- `/ws` — SvelteKit socket app for UI realtime features (message streaming state, presence).

Notable admin endpoints beyond the groups above: `POST /api/v1/models/sync` (declarative model reconciliation; admin-only, [API Endpoints guide](https://docs.openwebui.com/reference/api-endpoints)) and `GET /api/models` (full model list for the workspace).
