---
upstream: https://github.com/grafana/mcp-grafana
last_updated: 2026-08-21
---

# mcp-grafana

A [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) server for [Grafana](https://grafana.com/): instead of hand-crafting API calls or guessing dashboard URLs, an LLM client (Claude Desktop, Cursor, VS Code, …) connects to `mcp-grafana` and calls a large set of typed, RBAC-scoped tools that read and write against your Grafana instance and its surrounding ecosystem — dashboards, datasources, Prometheus/LogQL/Pyroscope queries, alerting, OnCall, Sift investigations, incidents, annotations, snapshots, rendering, navigation, and more. Written in Go; distributed via `uvx`, the Docker image `grafana/mcp-grafana`, a release binary, `go install`, or a Helm chart. Requires Grafana 9.0 or later for full functionality.

- Upstream repository: https://github.com/grafana/mcp-grafana
- Documentation: [README.md](https://github.com/grafana/mcp-grafana/blob/main/README.md) and the [`docs/` tree](https://github.com/grafana/mcp-grafana/tree/main/docs) in the upstream repository; the tool catalog with per-tool RBAC permissions is in the [README "Tools" table](https://github.com/grafana/mcp-grafana/blob/main/README.md#tools)
- License: Apache-2.0
- Language: Go
- Transports: `stdio` (default), `sse`, `streamable-http`
- Requires: Grafana 9.0+ (the `/datasources/uid/{uid}` endpoint used by datasource tools was introduced in 9.0)

Deployment notes (homelab): deployed to the Kubernetes cluster via the `grafana-mcp` Helm chart (OCI `oci://ghcr.io/grafana-community/helm-charts/grafana-mcp`, chart source [grafana/helm-charts](https://github.com/grafana/helm-charts/tree/main/charts/grafana-mcp)) in read-only mode (`--disable-write`), so the server can query the instance but cannot mutate it.

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
