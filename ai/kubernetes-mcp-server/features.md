---
upstream: https://github.com/containers/kubernetes-mcp-server
last_updated: 2026-08-22
---

# kubernetes-mcp-server — features

Significant feature areas, each with a link to the matching upstream source. The [README](https://github.com/containers/kubernetes-mcp-server/blob/main/README.md) and the [docs/ tree](https://github.com/containers/kubernetes-mcp-server/tree/main/docs) are authoritative.

## MCP toolsets and surface filtering

Tools, prompts, and resources are grouped into eight switchable toolsets — `config` and `core` by default, plus `helm`, `kcp`, `kiali`, `kubevirt`, `netobserv`, and `tekton` — so operators can expose only the surface their agents need (smaller context, better tool selection). Since v0.0.64 tools can be filtered **per cluster target** ([#1196](https://github.com/containers/kubernetes-mcp-server/pull/1196)) and since v0.0.65 a config option prefilters toolsets ([#1293](https://github.com/containers/kubernetes-mcp-server/pull/1293)). See the [toolset catalog](https://github.com/containers/kubernetes-mcp-server/blob/main/README.md#available-toolsets).

## Generic resource CRUD and safety modes

Any Kubernetes or OpenShift resource can be created/updated, read, listed, deleted, and scaled through the `resources_*` tools ([feature list](https://github.com/containers/kubernetes-mcp-server/blob/main/README.md#features)). Safety controls: `--read-only` and `--disable-destructive` modes, plus TOML `denied_resources` rules that block access to sensitive kinds (e.g. `Secret`) regardless of cluster RBAC ([Configuration reference](https://github.com/containers/kubernetes-mcp-server/blob/main/docs/configuration.md)).

## Pods and nodes

Pod-centric operations (list with filters, get, delete, logs, `top` via Metrics Server, `exec`, run-a-container) and node operations (`top`, kubelet system logs via the API proxy, kubelet Summary API stats including PSI metrics since v0.0.54) — all direct API-server calls, no `kubectl` subprocess. See the [feature list](https://github.com/containers/kubernetes-mcp-server/blob/main/README.md#features).

## Multi-cluster

Multiple clusters from one kubeconfig are addressed per tool call via an optional `context` argument (introduced in [v0.0.53](https://github.com/containers/kubernetes-mcp-server/releases/tag/v0.0.53)); disable with `--disable-multi-cluster` or `cluster_provider_strategy = "disabled"`. Cluster state and kubeconfig changes are watched and the server reloads automatically ([cluster state monitoring, v0.0.55](https://github.com/containers/kubernetes-mcp-server/releases/tag/v0.0.55)).

## Configuration and hot reload

Configuration via CLI flags, a main TOML file, and a drop-in `conf.d` directory in lexical order ([#524](https://github.com/containers/kubernetes-mcp-server/pull/524)); files are watched and applied on `SIGHUP` without restarting — reload is fully transactional since v0.0.62 ([#1130](https://github.com/containers/kubernetes-mcp-server/pull/1130)). See the [Configuration reference](https://github.com/containers/kubernetes-mcp-server/blob/main/docs/configuration.md).

## Authentication (Streamable HTTP mode)

OAuth 2.0/OIDC authorization for the HTTP endpoint with setup guides for [Keycloak](https://github.com/containers/kubernetes-mcp-server/blob/main/docs/KEYCLOAK_OIDC_SETUP.md) and [Microsoft Entra ID](https://github.com/containers/kubernetes-mcp-server/blob/main/docs/ENTRA_ID_SETUP.md); a token-exchange provider for delegated auth since v0.0.57 ([#604](https://github.com/containers/kubernetes-mcp-server/pull/604)); TLS minimum-version and cipher-suite env vars since v0.0.66 ([#1270](https://github.com/containers/kubernetes-mcp-server/pull/1270)).

## Observability and logging

Optional OpenTelemetry distributed tracing and metrics with custom sampling ([OTEL docs](https://github.com/containers/kubernetes-mcp-server/blob/main/docs/OTEL.md)), a `/stats` endpoint, and an MCP logging capability that categorizes Kubernetes API errors and redacts secrets (tokens, keys, passwords, cloud credentials) before sending them to clients ([MCP Logging guide](https://github.com/containers/kubernetes-mcp-server/blob/main/docs/logging.md)).

## Distribution

Single native binary for Linux/macOS/Windows, npm package (`kubernetes-mcp-server`), PyPI package, multi-arch container image on `ghcr.io` (since v0.0.61, [#1029](https://github.com/containers/kubernetes-mcp-server/pull/1029)), an in-repo [Helm chart](https://github.com/containers/kubernetes-mcp-server/tree/main/charts/kubernetes-mcp-server) (published as `oci://ghcr.io/containers/charts/kubernetes-mcp-server`), and MCP Registry publication (`io.github.containers/kubernetes-mcp-server`). `--stateless` mode supports load-balanced/serverless HTTP deployments (v0.0.55).

## Ecosystem integrations and quality gates

Optional toolsets integrate KubeVirt (VM lifecycle, guest agent), Kiali/Istio mesh operations, Tekton pipelines, NetObserv network telemetry, and kcp workspaces. Automated LLM eval suites ([evals/tasks](https://github.com/containers/kubernetes-mcp-server/tree/main/evals/tasks)) validate scenarios against Kubernetes, Helm, Istio, Kiali, KubeVirt, Tekton, and NetObserv (results published per release via mcpchecker), and a CI check keeps the generated README tool docs in sync.
