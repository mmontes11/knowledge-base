---
upstream: https://github.com/containers/kubernetes-mcp-server
last_updated: 2026-08-22
---

# kubernetes-mcp-server — API reference

An MCP server has no client SDK; its API surface is the set of **MCP tools, prompts, and resources** it exposes, grouped into switchable **toolsets** (enabled with the `--toolsets` flag or the `toolsets` TOML option). When multi-cluster support is enabled (the default), every cluster-scoped tool also accepts an optional `context` argument selecting the Kubernetes context. Per-parameter schemas for every tool are maintained in the canonical list in the [upstream README](https://github.com/containers/kubernetes-mcp-server/blob/main/README.md#tools-and-functionalities) — that list is generated from the tool definitions and checked for staleness in CI, so link to it rather than duplicating it here.

## Toolsets

`config` and `core` are enabled by default; the rest must be opted in.

| Toolset | Default | Tools | Description | Upstream docs |
| ------- | ------- | ----- | ----------- | ------------- |
| `config` | ✓ | 3 | View and manage the local kubeconfig. | [README tools](https://github.com/containers/kubernetes-mcp-server/blob/main/README.md#tools-and-functionalities) |
| `core` | ✓ | 19 | Most common Kubernetes management operations (pods, generic resources, events, nodes, namespaces). | [README tools](https://github.com/containers/kubernetes-mcp-server/blob/main/README.md#tools-and-functionalities) |
| `helm` | | 3 | Manage Helm charts and releases. | [README tools](https://github.com/containers/kubernetes-mcp-server/blob/main/README.md#tools-and-functionalities) |
| `kcp` | | 2 | Manage kcp workspaces and multi-tenancy. | [README tools](https://github.com/containers/kubernetes-mcp-server/blob/main/README.md#tools-and-functionalities) |
| `kiali` | | 22 | Kiali/Istio service-mesh management. | [Kiali toolset docs](https://github.com/containers/kubernetes-mcp-server/blob/main/docs/KIALI.md) |
| `kubevirt` | | 8 | KubeVirt virtual-machine management. | [KubeVirt toolset docs](https://github.com/containers/kubernetes-mcp-server/blob/main/docs/kubevirt.md) |
| `netobserv` | | 3 | Network observability backed by the NetObserv console plugin API (flows, metrics, export). | [NetObserv toolset docs](https://github.com/containers/kubernetes-mcp-server/blob/main/docs/NETOBSERV.md) |
| `tekton` | | 7 | Tekton pipeline management (pipelines, PipelineRuns, tasks, TaskRuns, troubleshooting). | [README tools](https://github.com/containers/kubernetes-mcp-server/blob/main/README.md#tools-and-functionalities) |

## Tools

### `config` (default)

| Tool | Purpose |
| ---- | ------- |
| `configuration_contexts_list` | List all context names and associated server URLs from the kubeconfig. |
| `configuration_view` | Get the current Kubernetes configuration as kubeconfig YAML (optionally minified to the current context). |
| `targets_list` | List all available targets (since v0.0.57). |

### `core` (default)

| Tool | Purpose |
| ---- | ------- |
| `resources_list` | List resources by `apiVersion`/`kind` with optional namespace and label/field selectors — works for **any** Kubernetes or OpenShift resource. |
| `resources_get` | Get a single resource by `apiVersion`/`kind`/name. |
| `resources_create_or_update` | Create or update any resource via server-side apply. |
| `resources_delete` | Delete any resource. |
| `resources_scale` | Get or update the scale of a scalable resource. |
| `pods_list` / `pods_list_in_namespace` | List pods across clusters/namespaces with label/field filtering. |
| `pods_get` | Get a pod. |
| `pods_delete` | Delete a pod. |
| `pods_log` | Get pod logs (previous container, tail lines). |
| `pods_top` | Resource consumption (CPU/memory) of pods via Metrics Server. |
| `pods_exec` | Exec a command in a pod container. |
| `pods_run` | Run a container image in a pod, optionally exposing it. |
| `namespaces_list` | List namespaces (field-selector filtering since v0.0.63). |
| `projects_list` | List OpenShift projects. |
| `events_list` | List cluster events for debugging (field-selector filtering since v0.0.63). |
| `nodes_top` | Node resource consumption via Metrics Server. |
| `nodes_log` | Node system logs (kubelet, kube-proxy, files) via the kubelet API proxy. |
| `nodes_stats_summary` | Kubelet Summary API: node/pod/container CPU, memory, filesystem, network, PSI metrics. |

### `helm`

| Tool | Purpose |
| ---- | ------- |
| `helm_install` | Install a chart (repository or OCI reference) as a release. |
| `helm_list` | List Helm releases in a namespace or cluster-wide. |
| `helm_uninstall` | Uninstall a release. |

### `kcp`

| Tool | Purpose |
| ---- | ------- |
| `kcp_workspaces_list` | List kcp workspaces. |
| `kcp_workspace_describe` | Describe a kcp workspace (multi-tenancy). |

### `kiali` — 22 tools

Verified subset: `kiali_get_mesh_traffic_graph`, `kiali_get_mesh_status`, `kiali_manage_istio_config`, `kiali_manage_istio_config_read`, `kiali_list_mesh_clusters` (multi-cluster `meshCluster` parameter since v0.0.66), `kiali_get_resource_details` (includes Argo CD applications since v0.0.63), `kiali_list_traces`, `kiali_get_trace_details`, `kiali_get_pod_performance`, `kiali_get_logs`, `kiali_get_metrics` (Istio metrics; Gateway API / Inference API schemas since v0.0.66) — the remaining tools are documented in the [Kiali toolset docs](https://github.com/containers/kubernetes-mcp-server/blob/main/docs/KIALI.md).

### `kubevirt` — 8 tools

Verified subset: `vm_create` (secondary NICs since v0.0.58), `vm_clone`, `vm_lifecycle` (start/stop/restart since v0.0.56; `troubleshoot` action since v0.0.58; QEMU guest-agent access tool since v0.0.63; `TargetCompatibilityFilters` since v0.0.66) — the full set is documented in the [KubeVirt toolset docs](https://github.com/containers/kubernetes-mcp-server/blob/main/docs/kubevirt.md).

### `netobserv` — 3 tools

Flow, metrics, and export operations against the NetObserv console plugin API (toolset added in v0.0.65); see the [NetObserv toolset docs](https://github.com/containers/kubernetes-mcp-server/blob/main/docs/NETOBSERV.md).

### `tekton` — 7 tools

Toolset added in v0.0.61: start a Pipeline (PipelineRun), restart/cancel a PipelineRun, retrieve PipelineRun/TaskRun logs (via pod resolution), start a Task (TaskRun), restart a TaskRun, and a PipelineRun troubleshooting tool (since v0.0.66) — see the [README tool list](https://github.com/containers/kubernetes-mcp-server/blob/main/README.md#tools-and-functionalities).

## Prompts

MCP prompts exposed by toolset (full parameter list in the [upstream README](https://github.com/containers/kubernetes-mcp-server/blob/main/README.md#prompts)):

| Toolset | Prompt | Purpose |
| ------- | ------ | ------- |
| `core` | `cluster-health-check` | Comprehensive cluster health assessment (optional namespace scope, warning/error events). |
| `kiali` | `mesh-list-applications`, `mesh-list-services`, `mesh-list-workloads`, `mesh-list-namespaces`, `list-istio-config` | Mesh inventory listings. |
| `kiali` | `mesh-health-check`, `mesh-topology`, `traffic-topology` | Mesh health, control-plane topology, and traffic flow analysis. |
| `kiali` | `service-troubleshoot`, `trace-analysis`, `istio-config-review` | Root-cause investigation (logs/traces/Istio config) and config validation. |
| `kubevirt` | `vm-troubleshoot`, `windows-golden-image` | VM troubleshooting guide; Windows golden image via the windows-efi-installer Tekton pipeline. |
| `tekton` | `pipeline-troubleshoot` | Gather PipelineRun status, TaskRuns, failed step logs, and repo/Config context. |

No MCP resources or resource templates are defined on the default surfaces (the upstream README sections are empty as of this revision); the `resources_get`/`resources_list` tools carry `structuredContent` support since v0.0.64.

## CLI configuration surface

Main CLI options (most have TOML equivalents — full reference in the [Configuration docs](https://github.com/containers/kubernetes-mcp-server/blob/main/docs/configuration.md)):

| Option | Purpose |
| ------ | ------- |
| `--port` | Run in Streamable HTTP mode (path `/mcp`) on the given port. |
| `--config` / `--config-dir` | Main TOML config file and drop-in directory (`conf.d`, lexical order). |
| `--kubeconfig` | Kubeconfig path (otherwise resolved via in-cluster/default locations). |
| `--read-only` | Disable all write operations. |
| `--disable-destructive` | Disable destructive operations only (no effect when read-only). |
| `--toolsets` | Comma-separated toolsets to enable (reduces context size); per-target tool filtering since v0.0.64, toolset prefiltering config since v0.0.65. |
| `--stateless` | Stateless mode for load-balanced/container deployments (no change notifications). |
| `--disable-multi-cluster` | Pin to the current kubeconfig context (or `cluster_provider_strategy` in TOML: `kubeconfig`, `in-cluster`, `kcp`, `disabled`). |
| `--log-level` | Logging level 0–9, kubectl-style. |

## HTTP endpoints and authentication (Streamable HTTP mode)

- `GET /mcp` — Streamable HTTP MCP endpoint.
- Metrics and `/stats` observability endpoints (OTel + [MCP stats](https://github.com/containers/kubernetes-mcp-server/blob/main/docs/OTEL.md)); metrics are exposed under the `k8s_mcp_` prefix (renamed from `mcp_` in v0.0.58).
- OAuth 2.0 / OIDC authorization for HTTP mode with [Keycloak](https://github.com/containers/kubernetes-mcp-server/blob/main/docs/KEYCLOAK_OIDC_SETUP.md) and [Microsoft Entra ID](https://github.com/containers/kubernetes-mcp-server/blob/main/docs/ENTRA_ID_SETUP.md) (federated/Entra style since v0.0.63; token-exchange provider since v0.0.57); well-known OAuth endpoint hardening in v0.0.60/v0.0.66; `TLS_MIN_VERSION`/`TLS_CIPHER_SUITES` environment variables since v0.0.66.
- TOML `denied_resources` entries restrict access to sensitive kinds (e.g. `Secret`) regardless of cluster RBAC.
