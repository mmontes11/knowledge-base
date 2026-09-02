---
upstream: https://github.com/kubernetes-sigs/lws
last_updated: 2026-08-22
---

# lws — features

Key feature areas, each linked to the upstream documentation covering it. The [lws.sigs.k8s.io](https://lws.sigs.k8s.io/) documentation site is authoritative; the generated [API reference](https://lws.sigs.k8s.io/docs/reference/) details every field of the kinds.

## Leader–worker groups

- **Core model**: each `LeaderWorkerSet.replica` is a *group* of one **leader** pod plus `size-1` **worker** pods, implemented as two backing `StatefulSet`s behind a stable, domain-scoped DNS name. Pods get injected labels (`leaderworkerset.sigs.k8s.io/name`, `-worker-index`, `-group-index`, `-leader-name`) and env vars (`LWS_WORKER_INDEX`, `LWS_GROUP_SIZE`, `LWS_LEADER_ADDRESS`). [overview](https://lws.sigs.k8s.io/docs/overview/), [labels/annotations/env](https://lws.sigs.k8s.io/docs/reference/labels-annotations-and-environment-variables/)
- **SubGroups** (KEP-257, v0.6.0+): partition a group's workers into `subGroupPolicy` subgroups (including `LeaderExcluded`, which drops the leader, and per-subgroup `size`/`LeaderOnly`) for topology-bounded or per-chunk workloads, with `subgroup-index/size/key` labels. [concepts](https://lws.sigs.k8s.io/docs/concepts/rollout-strategy/), [API](https://lws.sigs.k8s.io/docs/reference/leaderworkerset.v1/#leaderworkerset-x-k8s-io-v1-LeaderWorkerSetSpec)

## Scaling and autoscaling

- **Scale subresource**: `LeaderWorkerSet` exposes `/scale`; HPA targets `spec.replicas` (group count) and reads leader-pod metrics (the leader may aggregate group-wide custom metrics). [HPA example](https://lws.sigs.k8s.io/docs/examples/hpa/)
- **Per-role autoscaling** (v0.10.0, KEP-849): `DisaggregatedSetRoleScaler` gives each External-scaling role its own `/scale` target for HPA/KEDA. [API reference](https://lws.sigs.k8s.io/docs/reference/disaggregatedset.v1/#disaggregatedset-x-k8s-io-v1-DisaggregatedSetRoleScaler)

## Rollout and partitioning

- **RollingUpdate strategy**: zero-downtime rollouts with `maxUnavailable` (Beta in Kubernetes 1.35, on by default) and `maxSurge` (cannot both be zero). [concepts/rollout-strategy](https://lws.sigs.k8s.io/docs/concepts/rollout-strategy/)
- **Partitioned rollouts** (v0.7.0, KEP-511): `rollingUpdateConfiguration.partition` holds a fraction of groups at the old revision for staged/controlled updates. [concepts/rollout-strategy](https://lws.sigs.k8s.io/docs/concepts/rollout-strategy/)
- **In-place group resize** (v0.7.0, KEP-552): update `leaderWorkerTemplate.size` to add/remove workers in an existing group. [release v0.7.0](https://github.com/kubernetes-sigs/lws/releases/tag/v0.7.0)
- **ControllerRevision** (v0.5.0, KEP-238): every template revision is recorded as a `ControllerRevision` (`template-revision-hash` label) for tracking. [release v0.5.0](https://github.com/kubernetes-sigs/lws/releases/tag/v0.5.0)

## Failure handling and restart policies

- **`restartPolicy`**: `RecreateGroupOnPodRestart` (default — any pod failure recreates the whole group), `None` (only the failed pod restarts), and `RecreateGroupAfterStart` (recreate the group on failure only when no pods are pending, so large image pulls finish). Introduced experimentally in v0.8.0, a first-class field in v0.9.0. [concepts/failure-handling](https://lws.sigs.k8s.io/docs/concepts/failure-handling/)
- **Node failure**: under the default policy a failed node recreates the group on healthy nodes; under `None` only the affected pods reschedule. [concepts/failure-handling](https://lws.sigs.k8s.io/docs/concepts/failure-handling/)

## Disaggregated inference (DisaggregatedSet)

- **Multi-role workloads** (v0.9.0, KEP-766): `DisaggregatedSet` models disaggregated LLM inference (e.g. separate prefill/decode roles) as at-least-two roles, each a full `LeaderWorkerSet`, with cross-role rolling updates. [concepts/disaggregatedset](https://lws.sigs.k8s.io/docs/concepts/disaggregatedset/), [example](https://lws.sigs.k8s.io/docs/examples/disaggregatedset/)
- **Slices** (v0.10.0): `spec.slices` creates independent copies of the whole role topology that scale and roll out independently. [API reference](https://lws.sigs.k8s.io/docs/reference/disaggregatedset.v1/#disaggregatedset-x-k8s-io-v1-DisaggregatedSetSpec)
- **Placement policy** (v0.10.0): `spec.placementPolicy` co-locates a slice's roles and spreads slices across topology domains by injecting pod affinity/anti-affinity. [API reference](https://lws.sigs.k8s.io/docs/reference/disaggregatedset.v1/#disaggregatedset-x-k8s-io-v1-PlacementPolicy)
- **External scaling**: per-role `scaling.mode: External` auto-creates the `DisaggregatedSetRoleScaler`. [API reference](https://lws.sigs.k8s.io/docs/reference/disaggregatedset.v1/#disaggregatedset-x-k8s-io-v1-RoleScaling)

## Scheduling

- **Gang scheduling** (v0.7.0, KEP-407) and **topology-aware scheduling** with Kueue (via the `exclusive-topology` / `subgroup-exclusive-topology` annotations and `leaderworkerset.sigs.k8s.io/exclusive-topology` label). [TAS with Kueue](https://lws.sigs.k8s.io/docs/examples/tas/)

## Storage

- **Persistent volumes** (v0.8.0, KEP-622): `spec.volumeClaimTemplates` + `spec.persistentVolumeClaimRetentionPolicy` give leader/worker `StatefulSet`s per-group PVCs with configurable retention on scale-down/delete. [API reference](https://lws.sigs.k8s.io/docs/reference/leaderworkerset.v1/#leaderworkerset-x-k8s-io-v1-LeaderWorkerSetSpec)

## Networking and discovery

- **Stable networking**: `spec.networkConfig` (`Stable` subdomain) and per-replica `UniquePerReplica` subdomain (`subdomainPolicy`) so pods are reachable by predictable DNS; the leader is discovered via the headless service and `LWS_LEADER_ADDRESS`. [labels/annotations/env](https://lws.sigs.k8s.io/docs/reference/labels-annotations-and-environment-variables/)
- **TPU support**: `TPU_NAME`, `TPU_WORKER_ID`, `TPU_WORKER_HOSTNAMES` injected (workers in the same subgroup only) for multi-host/TPU inference. [labels/annotations/env](https://lws.sigs.k8s.io/docs/reference/labels-annotations-and-environment-variables/)

## Observability

- **Prometheus metrics** (controller + webhook, HTTPS when cert-manager enabled), `status` conditions and `observedGeneration`, plus meaningful `kubectl` printer columns (v0.7.0). [Prometheus](https://lws.sigs.k8s.io/docs/manage/prometheus/)
- [Troubleshooting](https://lws.sigs.k8s.io/docs/troubleshooting/) guide for rollout/restart and webhook issues.

## Integrations and inference frameworks

- First-party examples and user guides for **vLLM**, **TensorRT-LLM**, **SGLang**, and **llama.cpp**; integrates with **KServe**, **Kueue** (TAS), and **LlM-d** (adopted). [examples index](https://lws.sigs.k8s.io/docs/examples/), [adopters](https://lws.sigs.k8s.io/docs/adoption/)

## Installation and configuration

- **Helm chart** published to the OCI registry (`oci://registry.k8s.io/lws/charts/lws`) and a kustomize manifest path; optional **cert-manager** for webhook serving certs. [installation](https://lws.sigs.k8s.io/docs/installation/)
- **Component configuration** (v0.6.0+): the `Configuration` CRD / `--config` file configures webhook, leader election, metrics, health, TLS, internal cert management, and gang-scheduling provider — direct flags are deprecated. [Configuration types](https://github.com/kubernetes-sigs/lws/blob/main/api/config/v1alpha1/configuration_types.go), [external cert-manager](https://lws.sigs.k8s.io/docs/manage/cert_manager/)
