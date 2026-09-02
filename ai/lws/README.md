---
upstream: https://github.com/kubernetes-sigs/lws
last_updated: 2026-08-22
---

# lws

[LeaderWorkerSet (LWS)](https://lws.sigs.k8s.io/) is a Kubernetes API for deploying a tightly-coupled group of pods — one "leader" pod plus N "worker" pods — as a single unit of replication. It targets distributed workloads, primarily LLM inference (vLLM, TensorRT-LLM, SGLang, llama.cpp) and multi-process training/serving stacks, where a single model replica spans several pods and accelerators that must be scheduled, rolled, restarted, and scaled together. Each group is realized as two backing `StatefulSet`s (one leader, one for the workers) behind a stable, domain-scoped DNS name, with LWS adding gang-aware rollout, restart, and topology-aware-scheduling primitives on top.

- Upstream repository: https://github.com/kubernetes-sigs/lws
- Documentation: https://lws.sigs.k8s.io/ (generated API reference under [/docs/reference/](https://lws.sigs.k8s.io/docs/reference/))
- License: Apache-2.0
- API groups/versions: `leaderworkerset.x-k8s.io/v1` (`LeaderWorkerSet`), `disaggregatedset.x-k8s.io/v1` (`DisaggregatedSet`, `DisaggregatedSetRoleScaler`), and `lws.x-k8s.io/v1alpha1` (`Configuration`)

## Homelab deployment

Tracked in [`mmontes11/k8s-ai`](https://github.com/mmontes11/k8s-ai) under `infrastructure/lws/`, installed into the `ai` namespace via Flux: an `OCIRepository` (`lws`) pointing at `oci://registry.k8s.io/lws/charts/lws` (pinning tag `0.8.0`) plus a `HelmRelease` (`lws`) using a `chartRef` to that repository. The Flux `Kustomization` wiring `./infrastructure/lws` is **currently disabled** (commented out) in `clusters/homelab/infrastructure.yaml`, so LWS is present in the repo but not reconciled on the homelab.

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
