---
upstream: https://github.com/kubernetes-sigs/lws
last_updated: 2026-08-22
---

# lws — API reference

LWS registers four custom resource kinds across three API groups: the primary workload kind `LeaderWorkerSet` under **`leaderworkerset.x-k8s.io/v1`**, the disaggregated-inference workload `DisaggregatedSet` and its per-role `DisaggregatedSetRoleScaler` under **`disaggregatedset.x-k8s.io/v1`**, and the controller component configuration `Configuration` under **`lws.x-k8s.io/v1alpha1`**. The generated reference is maintained at [lws.sigs.k8s.io/docs/reference/](https://lws.sigs.k8s.io/docs/reference/); the Go type definitions live in [`api/`](https://github.com/kubernetes-sigs/lws/tree/main/api).

| Kind | API version | Purpose | Upstream API docs |
| ---- | ----------- | ------- | ----------------- |
| `LeaderWorkerSet` | `leaderworkerset.x-k8s.io/v1` | One leader pod plus a group of workers as a single unit of replication: `replicas` (number of leader-worker groups; `/scale` subresource targets the leader pod so HPA can scale it), `leaderWorkerTemplate` (`size` = group size including the leader, `restartPolicy`, `subGroupPolicy`, `volumeClaimTemplates`, leader/worker pod templates), `rolloutStrategy` (RollingUpdate with `partition`/`maxUnavailable`/`maxSurge`), `startupPolicy`, and `networkConfig` (Stable domain-scoped DNS). | [docs](https://lws.sigs.k8s.io/docs/reference/leaderworkerset.v1/#leaderworkerset-x-k8s-io-v1-LeaderWorkerSet) |
| `DisaggregatedSet` | `disaggregatedset.x-k8s.io/v1` | Multi-role (e.g. prefill/decode) disaggregated inference workload: `spec.roles[]` (each embeds a `LeaderWorkerSetTemplateSpec` and optionally `scaling.mode` Static/External), `spec.slices` (independent copies of the whole role topology that roll out independently), and `placementPolicy` (co-locate a slice's roles / spread slices across topology domains via injected pod affinity). | [docs](https://lws.sigs.k8s.io/docs/reference/disaggregatedset.v1/#disaggregatedset-x-k8s-io-v1-DisaggregatedSet) |
| `DisaggregatedSetRoleScaler` | `disaggregatedset.x-k8s.io/v1` | Per-role `/scale` subresource exposing a single role of a `DisaggregatedSet`. Auto-created by the controller for every role with `scaling.mode: External`; external autoscalers (HPA, KEDA, any `/scale`-aware controller) write `spec.replicas` and the controller drives that role's `LeaderWorkerSet`. | [docs](https://lws.sigs.k8s.io/docs/reference/disaggregatedset.v1/#disaggregatedset-x-k8s-io-v1-DisaggregatedSetRoleScaler) |
| `Configuration` | `lws.x-k8s.io/v1alpha1` | Controller component configuration (single cluster-scoped object): webhook port/host/certDir, leader election, metrics bind address, health probe addresses, TLS options, internal certificate management toggle, gang-scheduling provider, and API-server client QPS/burst. | [types](https://github.com/kubernetes-sigs/lws/blob/main/api/config/v1alpha1/configuration_types.go) |

Notes:

- `DisaggregatedSet` requires at least two roles, and the all-or-nothing replicas rule (every role non-zero or every role zero) applies to non-External roles only — External roles take their count from their `DisaggregatedSetRoleScaler`.
- `DisaggregatedSetRoleScaler` objects are named `<disaggregatedset>-<role>` and exist only for roles with External scaling; do not create them by hand.
- `LeaderWorkerSet` exposes the scale subresource; the HPA selector matches leader pods (worker-index 0).
- `Configuration` is applied by the Helm chart / kustomize manifests (the `--config` path); it is not a per-workload resource.
- Field-level documentation is intentionally not duplicated here; follow the per-kind upstream links above.
