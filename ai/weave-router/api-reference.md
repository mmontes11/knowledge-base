---
upstream: https://github.com/weave-os/router
last_updated: 2026-09-25
---

# weave-router — API reference

Weave Router is a single HTTP service (default `http://localhost:8080`) that terminates Anthropic, OpenAI, and Gemini clients and routes each request to a chosen upstream model. Clients authenticate with a **router key** (`rk_...`) sent as a Bearer token; upstream provider keys (`sk-...`) live server-side. Routing decision headers on non-stream responses include `x-router-cost-usd`, `x-router-cost-input-usd`, `x-router-cost-output-usd`, and cache-token counts. The full config surface is in the in-repo [Configuration reference](https://github.com/weave-os/router/blob/main/docs/CONFIGURATION.md).

| Endpoint | Format / purpose |
| --- | --- |
| `POST /v1/messages` | Anthropic Messages, routed |
| `POST /v1/chat/completions` | OpenAI Chat Completions, routed |
| `POST /v1beta/models/:action` | Gemini `generateContent`, routed |
| `POST /v1/route` | Returns the routing decision without an upstream call |
| `GET /v1/models` | Anthropic passthrough (model list) |
| `POST /v1/messages/count_tokens` | Anthropic passthrough (token counting) |
| `GET /health` · `GET /readyz` · `GET /validate` | Liveness, dependency readiness, and key check |
| `GET /v1/sessions/:session_id/cost` | One session's committed cost + savings, scoped to the key |
| `GET /v1/analytics/routing-decisions` | Raw routing decisions as cursor-paginated NDJSON ([docs](https://github.com/weave-os/router/blob/main/docs/ANALYTICS_EXPORT.md)) |
| `GET /v1/analytics/schema` · `GET /v1/analytics/models` | Export field dictionary + price book |
| `GET /ui/` | Dashboard (self-hosted mode only) |

## Notes

- **Two keys — don't mix them**: `sk-or-...` / `sk-ant-...` / `sk-...` are your **upstream** provider keys (live in `.env.local`); `rk_...` is your **router** key (what clients send as a Bearer token).
- Keep liveness probes on `/health`; point readiness probes at `/readyz` when policy sidecars must be ready before traffic arrives.
- Streaming responses cannot carry final cost in HTTP headers (headers flush before usage is known); stream clients read the `weave_cost` object on the final Anthropic `message_delta` usage event.
- Multi-replica deployments need Pub/Sub (`PUBSUB_*`) for cache invalidation; `docker compose` runs the emulator.
