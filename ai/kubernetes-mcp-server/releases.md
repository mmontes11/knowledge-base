---
upstream: https://github.com/containers/kubernetes-mcp-server
last_updated: 2026-08-22
---

# kubernetes-mcp-server — releases

Latest 10 releases, newest first. Check the ⚠️ entries before upgrading.

## v0.0.66 — 2026-07-31

[Release page](https://github.com/containers/kubernetes-mcp-server/releases/tag/v0.0.66)

- New TLS hardening env vars `TLS_MIN_VERSION` and `TLS_CIPHER_SUITES` ([#1270](https://github.com/containers/kubernetes-mcp-server/pull/1270)).
- KubeVirt toolset gains `TargetCompatibilityFilters` ([#1298](https://github.com/containers/kubernetes-mcp-server/pull/1298)); Kiali toolset gains a `meshCluster` parameter for multi-cluster and Gateway API / Inference API tool-schema updates ([#1224](https://github.com/containers/kubernetes-mcp-server/pull/1224), [#1256](https://github.com/containers/kubernetes-mcp-server/pull/1256)); NetObserv OpenShift detection and TLS handling hardened.
- Security: the well-known OAuth proxy now rejects unknown paths and uses the configured URL instead of a user-provided one ([#1316](https://github.com/containers/kubernetes-mcp-server/pull/1316), [#1323](https://github.com/containers/kubernetes-mcp-server/pull/1323)); Tekton PipelineRun troubleshooting tools added ([#1245](https://github.com/containers/kubernetes-mcp-server/pull/1245)).

## v0.0.65 — 2026-07-14

[Release page](https://github.com/containers/kubernetes-mcp-server/releases/tag/v0.0.65)

- New `netobserv` toolset: network observability backed by the NetObserv console plugin API ([#1159](https://github.com/containers/kubernetes-mcp-server/pull/1159)).
- New config option for toolset prefiltering ([#1293](https://github.com/containers/kubernetes-mcp-server/pull/1293)); discovery requests during startup are now bounded ([#1285](https://github.com/containers/kubernetes-mcp-server/pull/1285)).

## v0.0.64 — 2026-07-10

[Release page](https://github.com/containers/kubernetes-mcp-server/releases/tag/v0.0.64)

- ⚠️ **Breaking** (marked `feat!` upstream): target-specific tool filtering — tools can now be filtered per cluster target ([#1196](https://github.com/containers/kubernetes-mcp-server/pull/1196)).
- `resources_get` and `resources_list` return `structuredContent` ([#1232](https://github.com/containers/kubernetes-mcp-server/pull/1232)); server-side-apply full-state semantics clarified in the `resources_create_or_update` description.

## v0.0.63 — 2026-06-23

[Release page](https://github.com/containers/kubernetes-mcp-server/releases/tag/v0.0.63)

- `fieldSelector` support added to the `events_list` and `namespaces_list` tools ([#1138](https://github.com/containers/kubernetes-mcp-server/pull/1138)).
- KubeVirt: QEMU guest-agent access tool ([#811](https://github.com/containers/kubernetes-mcp-server/pull/811)); KubeVirt bumped to v1.8.2 (CDI v1.65.0, Multus v4.2.4).
- Token-exchange provider gains EC key support and Entra ID federated-auth style ([#1147](https://github.com/containers/kubernetes-mcp-server/pull/1147)); config support for logging to a file ([#1083](https://github.com/containers/kubernetes-mcp-server/pull/1083)).

## v0.0.62 — 2026-05-05

[Release page](https://github.com/containers/kubernetes-mcp-server/releases/tag/v0.0.62)

- ⚠️ **Breaking** (validation change): the `kubeconfig` auth mode combined with `require_oauth` is now rejected at startup ([#1122](https://github.com/containers/kubernetes-mcp-server/pull/1122)) — check your auth configuration before upgrading.
- MCP resources support added to the server API ([#1091](https://github.com/containers/kubernetes-mcp-server/pull/1091)).
- Complete hot-reload surface for HTTP middleware config ([#1107](https://github.com/containers/kubernetes-mcp-server/pull/1107)) with a fully transactional `ReloadConfiguration` ([#1130](https://github.com/containers/kubernetes-mcp-server/pull/1130)).

## v0.0.61 — 2026-04-24

[Release page](https://github.com/containers/kubernetes-mcp-server/releases/tag/v0.0.61)

- New `tekton` toolset with pipeline and task management (start pipelines/tasks, run lifecycle, restarts, logs) ([#892](https://github.com/containers/kubernetes-mcp-server/pull/892)).
- Multi-arch container image builds for the native binary ([#1029](https://github.com/containers/kubernetes-mcp-server/pull/1029)); Helm chart gains backend storage driver support ([#998](https://github.com/containers/kubernetes-mcp-server/pull/998)).

## v0.0.60 — 2026-04-01

[Release page](https://github.com/containers/kubernetes-mcp-server/releases/tag/v0.0.60)

- ⚠️ **Breaking** (Go API): `provider.GetTargets` is now optionally user-scoped — custom code calling it needs call-site updates ([#843](https://github.com/containers/kubernetes-mcp-server/pull/843)).
- Kiali: response body size limit to prevent unbounded memory consumption ([#927](https://github.com/containers/kubernetes-mcp-server/pull/927), [#930](https://github.com/containers/kubernetes-mcp-server/pull/930)); metrics endpoints no longer require OAuth ([#948](https://github.com/containers/kubernetes-mcp-server/pull/948)).
- Helm chart: reference validation with an `allowed_registries` config ([#950](https://github.com/containers/kubernetes-mcp-server/pull/950)); well-known OAuth proxy security hardened ([#943](https://github.com/containers/kubernetes-mcp-server/pull/943)).

## v0.0.59 — 2026-03-18

[Release page](https://github.com/containers/kubernetes-mcp-server/releases/tag/v0.0.59)

- MCP elicitation from tool calls, including URL-mode elicitation ([#804](https://github.com/containers/kubernetes-mcp-server/pull/804), [#862](https://github.com/containers/kubernetes-mcp-server/pull/862)).
- Container images published to `ghcr.io` for MCP Registry compatibility ([#837](https://github.com/containers/kubernetes-mcp-server/pull/837)); Helm chart supports `initContainers` override ([#822](https://github.com/containers/kubernetes-mcp-server/pull/822)).

## v0.0.58 — 2026-02-27

[Release page](https://github.com/containers/kubernetes-mcp-server/releases/tag/v0.0.58)

- ⚠️ **Breaking** (metrics): Prometheus metrics renamed from the `mcp_` prefix to `k8s_mcp_` — update dashboards, alerts, and queries before upgrading.
- KubeVirt: `troubleshoot` action added to the `vm_lifecycle` tool ([#653](https://github.com/containers/kubernetes-mcp-server/pull/653)); secondary network interface support in `vm_create` ([#682](https://github.com/containers/kubernetes-mcp-server/pull/682)).
- Default server instructions configurable for MCP Tool Search ([#716](https://github.com/containers/kubernetes-mcp-server/pull/716)); prompt documentation generation and a CI check that keeps README tool docs current.

## v0.0.57 — 2026-01-27

[Release page](https://github.com/containers/kubernetes-mcp-server/releases/tag/v0.0.57)

- Token-exchange provider support for delegated OIDC authentication ([#604](https://github.com/containers/kubernetes-mcp-server/pull/604)).
- MCP logging capability implemented with error categorization and automatic secret redaction ([#629](https://github.com/containers/kubernetes-mcp-server/pull/629)).
- Field-selector support for filtering pods and resources ([#641](https://github.com/containers/kubernetes-mcp-server/pull/641)); Helm chart RBAC specification support ([#636](https://github.com/containers/kubernetes-mcp-server/pull/636)); `targets_list` tool in the `config` toolset ([#635](https://github.com/containers/kubernetes-mcp-server/pull/635)); MCP Registry publishing workflow added.
