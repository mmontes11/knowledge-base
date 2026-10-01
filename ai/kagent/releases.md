---
upstream: https://github.com/kagent-dev/kagent
last_updated: 2026-10-01
---

# kagent — releases

kagent ships a fast-moving `0.x` line plus release candidates, and as of September 2026 a new `1.0.0` alpha line is in progress. Release notes are generated from conventional commits (Features / Bug Fixes). Release pages: https://github.com/kagent-dev/kagent/releases.

## v1.0.0-alpha6 — 2026-09-30

[Release page](https://github.com/kagent-dev/kagent/releases/tag/v1.0.0-alpha6) — newest 1.0.0 alpha.

- Fixes-only alpha: expire idle sessions via the delete workflow, keep Ollama tool-call argument order stable, and defer unsupported config fields in the API.

## v1.0.0-alpha5 — 2026-09-27

[Release page](https://github.com/kagent-dev/kagent/releases/tag/v1.0.0-alpha5) — 1.0.0 alpha line.

- New sandbox API (`Sandbox` / `SandboxTemplate`) with standalone runtimes, plus explicit Agent definitions and Sessions (create/edit/delete in the UI); Substrate bumped to v0.3.0-alpha1.

## v1.0.0-alpha4 — 2026-09-25

[Release page](https://github.com/kagent-dev/kagent/releases/tag/v1.0.0-alpha4) — 1.0.0 alpha line.

- A2A execution moves into runtimes (per-instance A2A JSON-RPC + agent cards, gRPC TaskStore); microVM sandbox harnesses and BYO harnesses in the UI; SDK-spec OTEL telemetry.

## v1.0.0-alpha3 — 2026-09-24

[Release page](https://github.com/kagent-dev/kagent/releases/tag/v1.0.0-alpha3) — 1.0.0 alpha line.

- Structured output in JSON Schema over A2A.

## v0.10.2 — 2026-09-23

[Release page](https://github.com/kagent-dev/kagent/releases/tag/v0.10.2) — current stable 0.10 line.

- Fix: skip unchanged MCP tool writes to Postgres.

## v1.0.0-alpha2 — 2026-09-22

[Release page](https://github.com/kagent-dev/kagent/releases/tag/v1.0.0-alpha2) — 1.0.0 alpha line.

- Helm: global values for registry, pull config, and more.

## v1.0.0-alpha1 — 2026-09-18

[Release page](https://github.com/kagent-dev/kagent/releases/tag/v1.0.0-alpha1) — first 1.0.0 alpha.

- UI: add agent namespace to the list view.

## v0.10.1 — 2026-09-08

[Release page](https://github.com/kagent-dev/kagent/releases/tag/v0.10.1) — 0.10 line.

- ADK: support the Responses API for Python ADK agents.

## v0.10.0 — 2026-09-04

[Release page](https://github.com/kagent-dev/kagent/releases/tag/v0.10.0) — new 0.10 minor.

- Streaming: configurable nginx, controller, and UI ingress.

## v0.10.0-rc6 — 2026-09-01

[Release page](https://github.com/kagent-dev/kagent/releases/tag/v0.10.0-rc6) — 0.10 release candidate.

- Fix: ensure API key passthrough works for OpenAI embeddings.
