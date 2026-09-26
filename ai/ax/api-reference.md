---
upstream: https://github.com/google/ax
last_updated: 2026-09-26
---

# ax — API reference

AX is driven through three surfaces: **`ax.io/v1alpha1` manifests** (applied with the `ax` CLI), the **`ax.v1alpha1.AX` gRPC control-plane service** the CLI talks to, and the **`ax` CLI** itself, which is deliberately `kubectl`-shaped and follows the active `kubectx` context. State lives in Redis, not in Kubernetes CRDs. AX is pre-1.0, so none of these surfaces carry stability guarantees. Generated Go types live in [`pkg/apis/v1alpha1`](https://github.com/google/ax/tree/main/pkg/apis/v1alpha1); the full gRPC method list is in [DESIGN.md — API reference](https://github.com/google/ax/blob/main/DESIGN.md#api-reference).

| Surface | Purpose | Upstream docs |
| --- | --- | --- |
| `Task` (ax.io/v1alpha1) | Smallest unit of isolated execution: container image and command, CPU/memory requests and limits, env vars, workspace bindings (first entry is the working directory), and `debug` (enables `ax ssh`). Immutable once created; lifecycle exposed as `status.phase` plus `WorkspaceReady`/`Ready` conditions. | [Concepts](https://github.com/google/ax/blob/main/docs/concepts.md#task), [Manifests](https://github.com/google/ax/blob/main/docs/manifests.md#task) |
| `Workspace` (ax.io/v1alpha1) | Reusable environment setup materialized inside each sandbox: Git repos cloned at a branch, MCP servers and registries, and skill registries; a binding `goal` lets a bootstrap agent finish environment setup on first run. | [Concepts](https://github.com/google/ax/blob/main/docs/concepts.md#workspace), [Manifests](https://github.com/google/ax/blob/main/docs/manifests.md#workspace) |
| `Model` (ax.io/v1alpha1) | LLM provider configuration: provider (`google`, `anthropic`), model identifier, generation parameters, and a Kubernetes secret reference for credentials; reusable across tasks and read by AX's own components (e.g. goal-driven workspace planning). | [Concepts](https://github.com/google/ax/blob/main/docs/concepts.md#model), [Manifests](https://github.com/google/ax/blob/main/docs/manifests.md#model) |
| `ax.v1alpha1.AX` gRPC service | Control plane on port 8080: task/workspace/model CRUD, `SuspendTask`/`ResumeTask` (checkpoint actor state), server-streaming `WatchTask` for status and condition transitions; health via HTTP `GET /healthz`. | [DESIGN.md — API reference](https://github.com/google/ax/blob/main/DESIGN.md#api-reference) |
| `ax` CLI | `kubectl`-shaped verbs: `apply`, `get`, `describe`, `watch`, `delete`, plus agent-specific `ssh`, `suspend`, `resume`, and connection plumbing (`ctx`, `tunnel`, `version`); global flags `-a/--atespace`, `-n/--namespace`, `--context`, `--server`. Install: `go install github.com/google/ax/cmd/ax@latest`. | [README — CLI usage](https://github.com/google/ax#cli-usage) |

## Notes

- `metadata.name` and `metadata.atespace` become Substrate resource names, so they must be lowercase RFC 1123 labels (≤ 63 chars); `ax apply` rejects anything else up front rather than failing later with `ActorCreationFailed`.
- All kinds are scoped to an **atespace** (default: `default`), which maps to Substrate atespaces.
- The sandbox exposes a small in-sandbox metadata server (HTTP on port 80): `/healthz`, `/readyz`, `/metadata/v1alpha1/ax/task`, and `/metadata/v1alpha1/ax/workspaces` — the contract a task container can rely on. [Sandbox](https://github.com/google/ax/blob/main/docs/sandbox.md#metadata-server)
- `Sandbox` / `SandboxConfig` kinds are [planned](https://github.com/google/ax/blob/main/docs/roadmap.md#1-stabilize-core-specs), not part of `v1alpha1` yet.
