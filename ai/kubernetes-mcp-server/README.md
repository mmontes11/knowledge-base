---
upstream: https://github.com/containers/kubernetes-mcp-server
last_updated: 2026-08-22
---

# kubernetes-mcp-server

The Kubernetes MCP Server is a Go-native implementation of the [Model Context Protocol (MCP)](https://modelcontextprotocol.io) for **Kubernetes** and **OpenShift**: it exposes cluster operations — generic resource CRUD, pod, node, namespace, and event operations, Helm, and optional toolsets for KubeVirt, Kiali, Tekton, NetObserv, and kcp — as MCP tools, prompts, and configuration, so AI agents can inspect and operate clusters without any `kubectl`/`helm` wrapper or external runtime dependencies (it talks to the API server directly).

- Upstream repository: [containers/kubernetes-mcp-server](https://github.com/containers/kubernetes-mcp-server)
- Documentation: [README](https://github.com/containers/kubernetes-mcp-server/blob/main/README.md) and the [docs/ tree](https://github.com/containers/kubernetes-mcp-server/tree/main/docs) (configuration reference, logging, observability, Keycloak/Entra ID setup guides, per-toolset docs)
- License: Apache-2.0 ([LICENSE](https://github.com/containers/kubernetes-mcp-server/blob/main/LICENSE))
- Transports: stdio (default) and Streamable HTTP on `/mcp` (run with `--port`)
- Distribution: [release binaries](https://github.com/containers/kubernetes-mcp-server/releases) (Linux/macOS/Windows), [npm package](https://www.npmjs.com/package/kubernetes-mcp-server), [PyPI package](https://pypi.org/project/kubernetes-mcp-server/), container image on `ghcr.io`, and an in-repo [Helm chart](https://github.com/containers/kubernetes-mcp-server/tree/main/charts/kubernetes-mcp-server) (`oci://ghcr.io/containers/charts/kubernetes-mcp-server`); also published to the MCP Registry as `io.github.containers/kubernetes-mcp-server`

## Usage in this stack

Our [k8s-ai](https://github.com/mmontes11/k8s-ai) infrastructure repository deploys the server with Flux: an `OCIRepository` for the chart `oci://ghcr.io/containers/charts/kubernetes-mcp-server` (pinned to `0.1.0`) and a `HelmRelease` (`mcp-kubernetes`) that installs it **read-only** (`read_only = true`) with a read-only kubeconfig volume mount and a mounted TOML config file; an internal `HTTPRoute` exposes the Streamable HTTP endpoint. Workspace agent runtimes connect to this deployment for all of their live Kubernetes access.

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
