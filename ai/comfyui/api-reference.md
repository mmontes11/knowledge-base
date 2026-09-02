---
upstream: https://github.com/comfyanonymous/ComfyUI
last_updated: 2026-08-18
---

# comfyui — API reference

ComfyUI exposes a single HTTP server (aiohttp, `server.py`; default port 8188, overridable via `--port`). All core endpoints are registered twice: at the root (e.g. `POST /prompt`) and under the `/api/` prefix (e.g. `POST /api/prompt`), so existing clients keep working while the spec'd surface is namespaced. A WebSocket at `GET /ws` (or `/api/ws`) streams execution status, progress, and execution outputs to the client. The machine-readable contract for the namespaced surface is the [OpenAPI 3.0.3 specification](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) maintained at the repository root (auto-synced from the Comfy Cloud API contract), and the root-level endpoints are defined inline in [server.py](https://github.com/comfy-Org/ComfyUI/blob/master/server.py).

| Endpoint | Method | Purpose | Upstream |
| -------- | ------ | ------- | -------- |
| `/prompt` | POST | Queue a workflow: submit a prompt graph (workflow JSON with node inputs), returns the `prompt_id` and queue position — the primary execution entry point for production pipelines. | [server.py](https://github.com/comfy-Org/ComfyUI/blob/master/server.py) |
| `/prompt` | GET | Retrieve queued/pending prompts. | [server.py](https://github.com/comfy-Org/ComfyUI/blob/master/server.py) |
| `/queue` | GET+POST | GET: inspect the running and pending queue (`clear` to drain). POST: remove a specific queue entry. | [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) |
| `/interrupt` | POST | Stop the current running prompt and, when given `workflow_id`, any of its descendant runs. | [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) |
| `/free` | POST | Release model weights from GPU (`unload_models` action) to free VRAM. | [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) |
| `/ws` | GET (WebSocket) | Event stream: `status` (queue state), `execution_start`, `executing` (per-node progress), `executed`, `execution_success` / `execution_cached` / `execution_error` / `executing` outputs. | [server.py](https://github.com/comfy-Org/ComfyUI/blob/master/server.py) |
| `/object_info` | GET | Full node introspection: every node class with its input/output class types — the schema used by API clients and custom-node tooling to validate workflows. | [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) |
| `/object_info/{node_class}` | GET | Introspection for a single node class. | [server.py](https://github.com/comfy-Org/ComfyUI/blob/master/server.py) |
| `/history` | GET | History of completed prompts with node outputs (in-memory; optional persistence). | [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) |
| `/history_v2` (+`/{prompt_id}`) | GET | Paginated, asset-aware history API (server-side pagination, `next_cursor`) — the modern replacement for `/history`. | [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) |
| `/api/jobs` (+`/{job_id}`, `/{job_id}/cancel`, `/cancel`, `/job/{job_id}/status`) | GET/POST | Job model for queued runs: list/status/inspect individual jobs, fetch their outputs, and cancel one job or all non-running jobs in a namespace (cancel endpoints added in v0.26.0). | [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) |
| `/api/assets` (+`/{id}`, `/content`, `/tags`, `/from-hash`, `/hash/{hash}`, `/prune`, `/seed*`, `/tags/refine`) | GET/POST | User-owned generated media (images/video/audio) and model assets with Blake3 content hashing, tagging, pagination, and LLM tag refinement. | [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) |
| `/api/files/mask-layers` | GET/POST | Mask-layer file management (layered image editing). | [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) |
| `/view`, `/view_metadata/{folder}` | GET | Serve generated images (`/view?filename=...&type=output`) and per-image metadata (prompt, seed) for a given output folder. | [server.py](https://github.com/comfy-Org/ComfyUI/blob/master/server.py) |
| `/upload/image`, `/upload/mask` | POST | Multipart upload of images and masks for use as workflow inputs (`subfolder`, `overwrite` params). | [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) |
| `/models`, `/models/{folder}`, `/embeddings` | GET | List available checkpoints/LoRAs/ ControlNets per model folder, and available text embeddings (CLIP). | [server.py](https://github.com/comfy-Org/ComfyUI/blob/master/server.py) |
| `/api/workflows` (+`/{workflow_id}`, `/content`, `/fork`, `/versions`, `/published/{share_id}`), `/api/workflow_templates` | GET/POST | Saved/managed workflows: list, inspect, fork, version, share, plus the built-in workflow-templates catalog. | [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) |
| `/api/global_subgraphs` (+`/{id}`) | GET/POST | Reusable subgraph (node-group) definitions — the API surface behind the subgraph feature. | [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) |
| `/api/tasks` (+`/{task_id}`) | GET | Training jobs: list and inspect LoRA/DiT training tasks (see [features](features.md)). | [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) |
| `/api/userdata` (+`/{file}`, `/move/{dest}`, `/publish`) | GET/POST/DELETE | Per-user data files (workflows, embeddings, etc.) with move and publish operations. | [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) |
| `/api/users`, `/api/user` | GET | User management (multi-user mode): list users, current session user. | [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) |
| `/api/settings` (+`/{id}`) | GET/POST | Server- and user-level settings (queue behavior, preview method, etc.). | [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) |
| `/api/features` | GET | Machine-readable capability/feature flags for the running instance. | [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) |
| `/system_stats`, `/health` | GET | GPU/VRAM/OS statistics (`/system_stats`) and liveness probe (`/health`). | [server.py](https://github.com/comfy-Org/ComfyUI/blob/master/server.py), [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) |
| `/api/i18n`, `/api/extensions` | GET | i18n string bundles for the frontend and the list of installed extensions. | [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) |
| `/internal/*` (folder_paths, logs, logs/raw, logs/subscribe) | GET | Internal endpoints for the desktop app: folder layout, log access (plain and SSE subscribe). Not part of the public API contract. | [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml) |
| `/api/billing/*`, partner-node endpoints | * | Optional paid **partner nodes** (external model providers) route through ComfyUI's own nodes plus per-provider backends; disable with `--disable-api-nodes`. Not covered by the stable API contract. | [spec](https://github.com/comfy-Org/ComfyUI/blob/master/openapi.yaml), [README](https://github.com/comfyanonymous/ComfyUI#features) |

Notes:

- The OpenAPI spec documents the `/api/*` namespaced surface (auth via an `apiKey` in the `Authorization` header — a Firebase JWT in multi-user/managed deployments); classic root endpoints (`/prompt`, `/models`, `/view`, …) are defined directly in `server.py` and served without authentication in single-user mode.
- Workflows are plain JSON graphs (node `class_type`, `inputs`, and `links/edges`); the JSON format itself is the client SDK — there is no separate official client library, so most integrations (e.g. [mmontes11/docker-comfyui](https://github.com/mmontes11/docker-comfyui)) speak HTTP directly.
- The frontend (Vue/TS) is a separate repository — [ComfyUI Frontend](https://github.com/comfy-Org/ComfyUI_frontend) — published to PyPI as `comfyui-frontend-package` and pinned per release; `--front-end-version` overrides it.
- Node/class introspection via `/object_info` is the canonical way to discover the ~hundreds of built-in node classes without scraping the frontend.
